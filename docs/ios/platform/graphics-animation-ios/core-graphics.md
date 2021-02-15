---
title: Principais gráficos no Xamarin. iOS
description: Este artigo discute as principais estruturas de gráficos do iOS. Ele mostra como usar gráficos de núcleo para desenhar geometria, imagens e PDFs.
ms.prod: xamarin
ms.assetid: 4A30F480-0723-4B8A-9049-7CEB6211304A
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/18/2017
ms.openlocfilehash: 281595f45db48316feeebb88debf8c70873f87f3
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91430863"
---
# <a name="core-graphics-in-xamarinios"></a>Principais gráficos no Xamarin. iOS

_Este artigo discute as principais estruturas de gráficos do iOS. Ele mostra como usar gráficos de núcleo para desenhar geometria, imagens e PDFs._

o iOS inclui a estrutura [*gráfica básica*](https://developer.apple.com/library/prerelease/ios/documentation/CoreGraphics/Reference/CoreGraphics_Framework/index.html) para fornecer suporte a desenhos de baixo nível. Essas estruturas são o que habilita os recursos gráficos avançados no UIKit.

O Core Graphics é uma estrutura gráfica 2D de baixo nível que permite o desenho de gráficos independentes de dispositivo. Todo desenho de 2D no UIKit usa gráficos de núcleo internamente.

Os gráficos principais dão suporte ao desenho em vários cenários, incluindo:

- [Desenho na tela por meio de `UIView` um](#Drawing_in_a_UIView_Subclass) .
- [Desenhando imagens na memória ou na tela](#Drawing_Images_and_Text).
- Criar e desenhar em um PDF.
- Ler e desenhar um PDF existente.

## <a name="geometric-space"></a>Espaço geométrico

Independentemente do cenário, todo o desenho feito com gráficos de núcleo é feito no espaço geométrico, o que significa que ele funciona em pontos abstratos em vez de pixels. Você descreve o que deseja desenhar em termos de geometria e estado de desenho, como cores, estilos de linha etc., e gráficos de núcleo manipulam a conversão de tudo em pixels. Esse estado é adicionado a um contexto de gráficos, que pode ser considerado como uma tela de pintor.

Há alguns benefícios para essa abordagem:

- O código de desenho se torna dinâmico e, posteriormente, pode modificar gráficos em tempo de execução.
- Reduzir a necessidade de imagens estáticas no pacote de aplicativos pode reduzir o tamanho do aplicativo.
- Os elementos gráficos tornam-se mais resilientes a alterações de resolução entre dispositivos.

<a name="Drawing_in_a_UIView_Subclass"></a>

## <a name="drawing-in-a-uiview-subclass"></a>Desenho em uma subclasse UIView

Cada `UIView` tem um `Draw` método que é chamado pelo sistema quando ele precisa ser desenhado. Para adicionar o código de desenho a uma exibição, subclasse `UIView` e substituição `Draw` :

```csharp
public class TriangleView : UIView
{
    public override void Draw (CGRect rect)
    {
        base.Draw (rect);
    }
}
```

Draw nunca deve ser chamado diretamente. Ele é chamado pelo sistema durante o processamento do loop de execução. Na primeira vez que o loop de execução é adicionado após uma exibição ser adicionada à hierarquia de exibição, seu `Draw` método é chamado. As chamadas subsequentes `Draw` ocorrem quando a exibição é marcada como precisa ser desenhada chamando `SetNeedsDisplay` ou `SetNeedsDisplayInRect` na exibição.

### <a name="pattern-for-graphics-code"></a>Padrão para código de gráficos

O código na `Draw` implementação deve descrever o que deseja desenhar. O código de desenho segue um padrão no qual ele define algum estado de desenho e chama um método para solicitar que ele seja desenhado. Esse padrão pode ser generalizado da seguinte maneira:

1. Obter um contexto de gráfico.

2. Configurar atributos de desenho.

3. Crie uma geometria de primitivos de desenho.

4. Chame um método Draw ou Stroke.

### <a name="basic-drawing-example"></a>Exemplo de desenho básico

Por exemplo, considere o snippet de código a seguir:

```csharp
//get graphics context
using (CGContext g = UIGraphics.GetCurrentContext ()) {

    //set up drawing attributes
    g.SetLineWidth (10);
    UIColor.Blue.SetFill ();
    UIColor.Red.SetStroke ();

    //create geometry
    var path = new CGPath ();

    path.AddLines (new CGPoint[]{
    new CGPoint (100, 200),
    new CGPoint (160, 100),
    new CGPoint (220, 200)});

    path.CloseSubpath ();

    //add geometry to graphics context and draw it
    g.AddPath (path);
    g.DrawPath (CGPathDrawingMode.FillStroke);
}
```

Vamos interromper esse código:

```csharp
using (CGContext g = UIGraphics.GetCurrentContext ()) {
...
}
```

Com essa linha, primeiro ele obtém o contexto gráfico atual a ser usado para desenhar. Você pode pensar em um contexto de gráficos como a tela em que o desenho ocorre, contendo todo o estado sobre o desenho, como cores de preenchimento e de traçado, bem como a geometria a ser desenhada.

```csharp
g.SetLineWidth (10);
UIColor.Blue.SetFill ();
UIColor.Red.SetStroke ();
```

Depois de obter um contexto de gráficos, o código configura alguns atributos para usar ao desenhar, mostrados acima. Nesse caso, as cores de largura, borda e preenchimento da linha são definidas. Qualquer desenho subsequente usará esses atributos, pois eles serão mantidos no estado do contexto de gráficos.

Para criar a geometria, o código usa um `CGPath` , que permite que um caminho de gráfico seja descrito a partir de linhas e curvas. Nesse caso, o caminho adiciona linhas conectando uma matriz de pontos para criar um triângulo. Conforme exibido abaixo, os gráficos principais usam um sistema de coordenadas para exibição de desenho, onde a origem está no canto superior esquerdo, com x-Direct positivo à direita e a direção positiva y para baixo:

```csharp
var path = new CGPath ();

path.AddLines (new CGPoint[]{
new CGPoint (100, 200),
new CGPoint (160, 100),
new CGPoint (220, 200)});

path.CloseSubpath ();
```

Depois que o caminho é criado, ele é adicionado ao contexto de gráficos para que a chamada `AddPath` e, `DrawPath` respectivamente, possa desenhá-lo.

A exibição resultante é mostrada abaixo:

 ![O triângulo de saída de exemplo](core-graphics-images/00-bluetriangle.png)

## <a name="creating-gradient-fills"></a>Criando Preenchimentos graduais

Formas mais sofisticadas de desenho também estão disponíveis. Por exemplo, os gráficos principais permitem a criação de preenchimentos graduais e a aplicação de caminhos de recorte. Para desenhar um preenchimento gradual dentro do caminho do exemplo anterior, primeiro o caminho precisa ser definido como o caminho de recorte:

```csharp
// add the path back to the graphics context so that it is the current path
g.AddPath (path);
// set the current path to be the clipping path
g.Clip ();
```

Definir o caminho atual como o caminho de recorte restringe todo o desenho subsequente dentro da geometria do caminho, como o código a seguir, que desenha um gradiente linear:

```csharp
// the color space determines how Core Graphics interprets color information
    using (CGColorSpace rgb = CGColorSpace.CreateDeviceRGB()) {
        CGGradient gradient = new CGGradient (rgb, new CGColor[] {
        UIColor.Blue.CGColor,
        UIColor.Yellow.CGColor
    });

// draw a linear gradient
    g.DrawLinearGradient (
        gradient,
        new CGPoint (path.BoundingBox.Left, path.BoundingBox.Top),
        new CGPoint (path.BoundingBox.Right, path.BoundingBox.Bottom),
        CGGradientDrawingOptions.DrawsBeforeStartLocation);
    }
```

Essas alterações produzem um preenchimento gradual, conforme mostrado abaixo:

 ![O exemplo com um preenchimento gradual](core-graphics-images/01-gradient-fill.png)

## <a name="modifying-line-patterns"></a>Modificando padrões de linha

Os atributos de desenho de linhas também podem ser modificados com gráficos principais. Isso inclui alterar a largura da linha e a cor do traço, bem como o próprio padrão de linha, como visto no código a seguir:

```csharp
//use a dashed line
g.SetLineDash (0, new nfloat[] { 10, 4 * (nfloat)Math.PI });
```

A adição desse código antes de qualquer operação de desenho resulta em traços tracejados de 10 unidades, com 4 unidades de espaçamento entre traços, conforme mostrado abaixo:

 ![Adicionar este código antes de qualquer operação de desenho resultar em traços tracejados](core-graphics-images/02-dashed-stroke.png)

Observe que, ao usar o API Unificada no Xamarin. iOS, o tipo de matriz precisa ser um `nfloat` e também precisa ser convertido explicitamente em Math. PI.

<a name="Drawing_Images_and_Text"></a>

## <a name="drawing-images-and-text"></a>Desenho de imagens e texto

Além de desenhar caminhos no contexto gráfico de uma exibição, os gráficos de núcleo também oferecem suporte a imagens e texto de desenho. Para desenhar uma imagem, basta criar uma `CGImage` e passá-la para uma `DrawImage` chamada:

```csharp
public override void Draw (CGRect rect)
{
    base.Draw (rect);

    using(CGContext g = UIGraphics.GetCurrentContext ()){
        g.DrawImage (rect, UIImage.FromFile ("MyImage.png").CGImage);
    }
}
```

No entanto, isso produz uma imagem desenhada de cabeça para baixo, como mostrado abaixo:

 ![Uma imagem desenhada de cabeça para baixo](core-graphics-images/03-upside-down-monkey.png)

A razão para isso é a origem dos gráficos principais para desenho de imagem no canto inferior esquerdo, enquanto a exibição tem sua origem na parte superior esquerda. Portanto, para exibir a imagem corretamente, a origem precisa ser modificada, o que pode ser feito modificando a *matriz de transformação atual* *(CTM)*. O CTM define onde os pontos residem, também conhecidos como *espaço do usuário*. Inverter o CTM na direção y e deslocar-o pela altura dos limites na direção y negativa pode virar a imagem.

O contexto de gráficos tem métodos auxiliares para transformar o CTM. Nesse caso, `ScaleCTM` "inverte" o desenho e `TranslateCTM` o desloca para a parte superior esquerda, conforme mostrado abaixo:

```csharp
public override void Draw (CGRect rect)
{
    base.Draw (rect);

    using (CGContext g = UIGraphics.GetCurrentContext ()) {

        // scale and translate the CTM so the image appears upright
        g.ScaleCTM (1, -1);
        g.TranslateCTM (0, -Bounds.Height);
        g.DrawImage (rect, UIImage.FromFile ("MyImage.png").CGImage);
}
```

A imagem resultante é exibida verticalmente:

 ![A imagem de exemplo exibida na vertical](core-graphics-images/04-upright-monkey.png)

> [!IMPORTANT]
> As alterações no contexto de gráficos se aplicam a todas as operações de desenho subsequentes. Portanto, quando o CTM é transformado, ele afetará qualquer desenho adicional. Por exemplo, se você desenhou o triângulo após a transformação CTM, ele pareceria de cabeça para baixo.

### <a name="adding-text-to-the-image"></a>Adicionando texto à imagem

Assim como acontece com caminhos e imagens, o texto de desenho com gráficos de núcleo envolve o mesmo padrão básico de definir um estado de gráfico e chamar um método para desenhar. No caso do texto, o método para exibir o texto é `ShowText` . Quando adicionado ao exemplo de desenho de imagem, o código a seguir desenha um texto usando gráficos de núcleo:

```csharp
public override void Draw (RectangleF rect)
{
    base.Draw (rect);

    // image drawing code omitted for brevity ...

    // translate the CTM by the font size so it displays on screen
    float fontSize = 35f;
    g.TranslateCTM (0, fontSize);

    // set general-purpose graphics state
    g.SetLineWidth (1.0f);
    g.SetStrokeColor (UIColor.Yellow.CGColor);
    g.SetFillColor (UIColor.Red.CGColor);
    g.SetShadow (new CGSize (5, 5), 0, UIColor.Blue.CGColor);

    // set text specific graphics state
    g.SetTextDrawingMode (CGTextDrawingMode.FillStroke);
    g.SelectFont ("Helvetica", fontSize, CGTextEncoding.MacRoman);

    // show the text
    g.ShowText ("Hello Core Graphics");
}
```

Como você pode ver, a definição do estado de gráficos para desenho de texto é semelhante à geometria de desenho. No caso de desenho de texto, no entanto, o modo de desenho de texto e a fonte também são aplicados. Nesse caso, uma sombra também é aplicada, embora a aplicação de sombras funcione da mesma forma para o desenho de caminho.

O texto resultante é exibido com a imagem, conforme mostrado abaixo:

 ![O texto resultante é exibido com a imagem](core-graphics-images/05-text-on-image.png)

## <a name="memory-backed-images"></a>Imagens com suporte de memória

Além de desenhar para o contexto de gráficos de uma exibição, os gráficos principais dão suporte ao desenho de imagens com backup de memória, também conhecido como desenho fora da tela. Isso requer:

- Criando um contexto de gráfico que é apoiado por um bitmap na memória
- Definindo o estado de desenho e emitindo comandos de desenho
- Obtendo a imagem do contexto
- Removendo o contexto

Ao contrário do `Draw` método, no qual o contexto é fornecido pela exibição, neste caso, você cria o contexto de uma das duas maneiras:

1. Chamando `UIGraphics.BeginImageContext` (ou `BeginImageContextWithOptions` )

2. Criando um novo `CGBitmapContextInstance`

 `CGBitmapContextInstance` é útil quando você está trabalhando diretamente com os bits de imagem, como para casos em que você está usando um algoritmo de manipulação de imagem personalizada. Em todos os outros casos, você deve usar `BeginImageContext` ou `BeginImageContextWithOptions` .

Depois que você tiver um contexto de imagem, adicionar o código de desenho será exatamente como ele está em uma `UIView` subclasse. Por exemplo, o exemplo de código usado anteriormente para desenhar um triângulo pode ser usado para desenhar uma imagem na memória em vez de em um `UIView` , conforme mostrado abaixo:

```csharp
UIImage DrawTriangle ()
{
    UIImage triangleImage;

    //push a memory backed bitmap context on the context stack
    UIGraphics.BeginImageContext (new CGSize (200.0f, 200.0f));

    //get graphics context
    using(CGContext g = UIGraphics.GetCurrentContext ()){

        //set up drawing attributes
        g.SetLineWidth(4);
        UIColor.Purple.SetFill ();
        UIColor.Black.SetStroke ();

        //create geometry
        path = new CGPath ();

        path.AddLines(new CGPoint[]{
            new CGPoint(100,200),
            new CGPoint(160,100),
            new CGPoint(220,200)});

        path.CloseSubpath();

        //add geometry to graphics context and draw it
        g.AddPath(path);
        g.DrawPath(CGPathDrawingMode.FillStroke);

        //get a UIImage from the context
        triangleImage = UIGraphics.GetImageFromCurrentImageContext ();
    }

    return triangleImage;
}
```

Um uso comum do desenho em um bitmap com suporte de memória é capturar uma imagem de qualquer `UIView` . Por exemplo, o código a seguir processa a camada de uma exibição para um contexto de bitmap e cria uma a `UIImage` partir dela:

```csharp
UIGraphics.BeginImageContext (cellView.Frame.Size);

//render the view's layer in the current context
anyView.Layer.RenderInContext (UIGraphics.GetCurrentContext ());

//get a UIImage from the context
UIImage anyViewImage = UIGraphics.GetImageFromCurrentImageContext ();
UIGraphics.EndImageContext ();
```

## <a name="drawing-pdfs"></a>Desenho de PDFs

Além das imagens, os gráficos principais dão suporte ao desenho em PDF. Assim como as imagens, você pode renderizar um PDF na memória, bem como ler um PDF para renderização em um `UIView` .

### <a name="pdf-in-a-uiview"></a>PDF em um UIView

Os gráficos principais também dão suporte à leitura de um PDF de um arquivo e renderização em uma exibição usando a `CGPDFDocument` classe. A `CGPDFDocument` classe representa um PDF no código e pode ser usada para ler e desenhar páginas.

Por exemplo, o código a seguir em uma `UIView` subclasse lê um PDF de um arquivo em um `CGPDFDocument` :

```csharp
public class PDFView : UIView
{
    CGPDFDocument pdfDoc;

    public PDFView ()
    {
        //create a CGPDFDocument from file.pdf included in the main bundle
        pdfDoc = CGPDFDocument.FromFile ("file.pdf");
    }

     public override void Draw (Rectangle rect)
    {
        ...
    }
}
```

`Draw`Em seguida, o método pode usar o `CGPDFDocument` para ler uma página `CGPDFPage` e renderizá-la chamando `DrawPDFPage` , conforme mostrado abaixo:

```csharp
public override void Draw (CGRect rect)
{
    base.Draw (rect);

    //flip the CTM so the PDF will be drawn upright
    using (CGContext g = UIGraphics.GetCurrentContext ()) {
        g.TranslateCTM (0, Bounds.Height);
        g.ScaleCTM (1, -1);

        // render the first page of the PDF
        using (CGPDFPage pdfPage = pdfDoc.GetPage (1)) {

        //get the affine transform that defines where the PDF is drawn
        CGAffineTransform t = pdfPage.GetDrawingTransform (CGPDFBox.Crop, rect, 0, true);

        //concatenate the pdf transform with the CTM for display in the view
        g.ConcatCTM (t);

        //draw the pdf page
        g.DrawPDFPage (pdfPage);
        }
    }
}
```

### <a name="memory-backed-pdf"></a>PDF com suporte de memória

Para um PDF na memória, você precisa criar um contexto de PDF chamando `BeginPDFContext` . O desenho em PDF é granular para páginas. Cada página é iniciada chamando `BeginPDFPage` e concluída chamando `EndPDFContent` , com o código de gráficos entre. Além disso, assim como acontece com o desenho de imagem, o desenho de PDF com suporte de memória usa uma origem no canto inferior esquerdo, que pode ser contada modificando o CTM, assim como acontece com imagens.

O código a seguir mostra como desenhar texto em um PDF:

```csharp
//data buffer to hold the PDF
NSMutableData data = new NSMutableData ();

//create a PDF with empty rectangle, which will configure it for 8.5x11 inches
UIGraphics.BeginPDFContext (data, CGRect.Empty, null);

//start a PDF page
UIGraphics.BeginPDFPage ();

using (CGContext g = UIGraphics.GetCurrentContext ()) {
    g.ScaleCTM (1, -1);
    g.TranslateCTM (0, -25);
    g.SelectFont ("Helvetica", 25, CGTextEncoding.MacRoman);
    g.ShowText ("Hello Core Graphics");
    }

//complete a PDF page
UIGraphics.EndPDFContent ();
```

O texto resultante é desenhado no PDF, que é então contido em um `NSData` que pode ser salvo, carregado, enviado por email, etc.

## <a name="summary"></a>Resumo

Neste artigo, examinamos os recursos gráficos fornecidos por meio da estrutura *gráfica principal* . Vimos como usar gráficos de núcleo para desenhar geometria, imagens e PDFs dentro do contexto de um `UIView,` , bem como contextos gráficos com suporte de memória.

## <a name="related-links"></a>Links Relacionados

- [Exemplo de gráficos de núcleo](/samples/xamarin/ios-samples/graphicsandanimation)
- [Instruções de gráficos e animação](~/ios/platform/graphics-animation-ios/graphics-animation-walkthrough.md)
- [Animação principal](~/ios/platform/graphics-animation-ios/core-animation.md)
- [Receitas de animação de núcleo](https://github.com/xamarin/recipes/tree/master/Recipes/ios/animation/coreanimation)