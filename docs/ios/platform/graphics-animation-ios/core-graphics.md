---
title: Gráficos de principal no xamarin. IOS
description: Este artigo discute as estruturas de iOS de elementos gráficos principais. Ele mostra como usar gráficos principais para desenhar geometria, imagens e PDFs.
ms.prod: xamarin
ms.assetid: 4A30F480-0723-4B8A-9049-7CEB6211304A
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/18/2017
ms.openlocfilehash: 82c54074db722824c56ae3ae86620c804b8d109e
ms.sourcegitcommit: b56b3f906d2c05a3f1be219ef41be8b79e519b8e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/25/2018
ms.locfileid: "39241943"
---
# <a name="core-graphics-in-xamarinios"></a>Gráficos de principal no xamarin. IOS

_Este artigo discute as estruturas de iOS de elementos gráficos principais. Ele mostra como usar gráficos principais para desenhar geometria, imagens e PDFs._

iOS inclui o [ *gráficos principais* ](https://developer.apple.com/library/prerelease/ios/documentation/CoreGraphics/Reference/CoreGraphics_Framework/index.html) estrutura para fornecer suporte de desenho de baixo nível. Essas estruturas são o que permite que os recursos avançados de gráficos dentro de UIKit. 

Elementos gráficos principais é uma estrutura de nível baixo de gráficos 2D que permite que o desenho gráficos independentes de dispositivo. Todos os 2D de desenho no UIKit usa elementos gráficos principais internamente.

Elementos gráficos principais dá suporte a desenho em vários cenários, incluindo:

-  [Desenhando na tela por meio de um `UIView` ](#Drawing_in_a_UIView_Subclass) .
-  [Desenhando imagens na memória ou na tela](#Drawing_Images_and_Text).
-  A criação e o desenho a um PDF.
-  Leitura e desenhando um PDF existente.


## <a name="geometric-space"></a>Espaço Geométrico

Independentemente do cenário, todos os desenhos feito com elementos gráficos principais é feito no espaço geométrico, que significa que ele funciona em pontos abstratos em vez de pixels. Você descreve o que você deseja desenhada em termos de geometria e estado, como cores, estilos de linha, etc. de desenho e gráficos principais manipula traduzindo tudo em pixels. Esse estado é adicionado a um contexto de gráficos, você pode pensar como tela de um pincel.

Há alguns benefícios dessa abordagem:

-  Código de desenho torna-se dinâmico e, posteriormente, pode modificar gráficos em tempo de execução.
-  Reduzindo a necessidade de imagens estáticas no pacote do aplicativo pode reduzir o tamanho do aplicativo.
-  Elementos gráficos se tornam mais resistentes a alterações de resolução em todos os dispositivos.

<a name="Drawing_in_a_UIView_Subclass"/>

## <a name="drawing-in-a-uiview-subclass"></a>Desenhando em uma subclasse de UIView

Cada `UIView` tem um `Draw` método é chamado pelo sistema quando ele precisa ser desenhado. Para adicionar o código de desenho a um modo de exibição, subclasse `UIView` e substituir `Draw`:

```csharp
public class TriangleView : UIView
{
    public override void Draw (CGRect rect)
    {
        base.Draw (rect);
    }
}
```

Desenho nunca deve ser chamado diretamente. Ele é chamado pelo sistema durante o processamento de loop de execução. Na primeira vez pelo loop execução depois que um modo de exibição é adicionado à hierarquia de exibição, seu `Draw` método é chamado. As chamadas subsequentes para `Draw` ocorrem quando o modo de exibição é marcado como a necessidade de ser desenhada com uma chamada a `SetNeedsDisplay` ou `SetNeedsDisplayInRect` no modo de exibição.

### <a name="pattern-for-graphics-code"></a>Padrão para códigos de gráficos

O código a `Draw` implementação deve descrever o que quer desenhado. O código de desenho segue um padrão em que ele define algum estado de desenho e chama um método para a solicitação de ela ser desenhada. Esse padrão pode ser generalizado da seguinte maneira:

1. Obter um contexto de gráficos.

2. Configure atributos de desenho.

3. Crie alguns geometria dos primitivos de desenho.

4. Chame um método de desenho ou ao traço.

### <a name="basic-drawing-example"></a>Exemplo de desenho básico

Por exemplo, considere o trecho de código a seguir:

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

Vamos dividir esse código:

```csharp
using (CGContext g = UIGraphics.GetCurrentContext ()) {
...
}
```
Com essa linha, ele primeiro obtém o contexto de gráficos atual a ser usado para o desenho. Você pode pensar um contexto de gráficos como a tela de desenho acontece, que contém todo o estado sobre o desenho, como cores de preenchimento e traço, bem como a geometria a desenhar.

```csharp
g.SetLineWidth (10);
UIColor.Blue.SetFill ();
UIColor.Red.SetStroke ();
``` 

Depois de obter um contexto de gráficos, o código configura alguns atributos a ser usado ao desenhar, mostrado acima. Nesse caso, as cores de largura, traço e preenchimento de linha são definidas. Qualquer drawing subsequente, em seguida, usará esses atributos porque eles são mantidos no estado do contexto de gráficos.

Para criar o código de geometria usa um `CGPath`, que permite que um caminho de gráficos para ser descrito de linhas e curvas. Nesse caso, o caminho adiciona linhas que conectam a uma matriz de pontos que compõem um triângulo. Como exibido abaixo de gráficos principal usa um sistema de coordenadas para o modo de desenho, cuja origem é na parte superior esquerda, com o x-direct positivo para a direita e a direção de y positivo para baixo:

```csharp
var path = new CGPath ();

path.AddLines (new CGPoint[]{
new CGPoint (100, 200),
new CGPoint (160, 100), 
new CGPoint (220, 200)});

path.CloseSubpath ();
``` 

Depois que o caminho é criado, ele é adicionado ao contexto de gráficos, de modo que chamar `AddPath` e `DrawPath` respectivamente poderá desenhá-lo.

A exibição resultante é mostrada abaixo:

 ![](core-graphics-images/00-bluetriangle.png "O triângulo de saída de exemplo")

## <a name="creating-gradient-fills"></a>Criar preenchimentos com gradiente

Formas mais avançadas de desenho também estão disponíveis. Por exemplo, elementos gráficos principais permite criar preenchimentos com gradiente e aplicar caminhos de recorte. Para desenhar um preenchimento de gradiente dentro do caminho do exemplo anterior, primeiro o caminho deve ser definido como o caminho de recorte:

```csharp
// add the path back to the graphics context so that it is the current path
g.AddPath (path);
// set the current path to be the clipping path
g.Clip ();
```

Definir o caminho atual, como o caminho de recorte restringe todos os desenhos subsequentes dentro da geometria de caminho, como o código a seguir, que desenha um gradiente linear:

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

 ![](core-graphics-images/01-gradient-fill.png "O exemplo com um preenchimento gradual")

## <a name="modifying-line-patterns"></a>Modificando os padrões de linha

Os atributos de desenho das linhas também podem ser modificados com elementos gráficos principais. Isso inclui a alterar a cor de traço e de largura de linha, bem como o padrão de linha em si, como visto no código a seguir:

```csharp
//use a dashed line
g.SetLineDash (0, new nfloat[] { 10, 4 * (nfloat)Math.PI });
```

Adicionando este código antes de quaisquer resultados de operações de desenho em unidades de traços tracejada 10 longos, com 4 unidades de espaçamento entre traços, conforme mostrado abaixo:

 ![](core-graphics-images/02-dashed-stroke.png "Adicionando este código antes de quaisquer resultados de operações de desenho em traços tracejados")
 
Observe que ao usar a API unificada do xamarin. IOS, o tipo de matriz deve ser um `nfloat`e também precisa ser convertido explicitamente em Math. pi.

<a name="Drawing_Images_and_Text"/>

## <a name="drawing-images-and-text"></a>Texto e imagens de desenho

Além de desenhar caminhos no contexto de gráficos do modo de exibição, elementos gráficos principais também dá suporte ao desenho de imagens e texto. Para desenhar uma imagem, basta criar uma `CGImage` e passe-o para um `DrawImage` chamar:

```csharp
public override void Draw (CGRect rect)
{
    base.Draw (rect);

    using(CGContext g = UIGraphics.GetCurrentContext ()){
        g.DrawImage (rect, UIImage.FromFile ("MyImage.png").CGImage);
    }
}
```

No entanto, isso produz uma imagem desenhada de cabeça para baixo, conforme mostrado abaixo:

 ![](core-graphics-images/03-upside-down-monkey.png "Uma imagem desenhada de cabeça para baixo")

A razão para isso é a origem de gráficos principal para o desenho da imagem é na parte inferior esquerda, enquanto o modo de exibição tem sua origem no canto superior esquerdo. Portanto, para exibir corretamente a imagem, a origem precisa ser modificado, que pode ser feito modificando o *matriz de transformação atual* *(CTM)*. O CTM define onde pontos ao vivo, também conhecido como *espaço de usuário*. Invertendo o CTM na direção y e deslocando-pela altura dos limites na direção y negativo podem inverter a imagem.

O contexto gráfico tem métodos auxiliares para transformar o CTM. Nesse caso, `ScaleCTM` "inverte" o desenho e `TranslateCTM` desloca para a parte superior esquerda, conforme mostrado abaixo:

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

A imagem resultante é exibida na vertical:

 ![](core-graphics-images/04-upright-monkey.png "O rodado da imagem exibida de exemplo")

> [!IMPORTANT]
> Altera para o contexto de gráficos se aplicam a todas as operações de desenho subsequentes. Portanto, quando o CTM é transformado, ele afetará qualquer drawing adicional. Por exemplo, se você desenhou o triângulo após a transformação CTM, parece cabeça para baixo.

### <a name="adding-text-to-the-image"></a>Adicionar texto à imagem

Como com imagens e os caminhos de desenho de texto com elementos gráficos principais envolve o mesmo padrão básico de configuração de algum estado de elementos gráficos e chamar um método para desenhar. No caso de texto, o método para exibir o texto é `ShowText`. Quando adicionado à imagem de exemplo de desenho, o código a seguir desenha um texto usando elementos gráficos principais:

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

Como você pode ver, definindo o estado de gráficos para desenho de texto é semelhante ao desenho de geometria. Para texto de desenho no entanto, a fonte e o modo de desenho do texto também são aplicados. Nesse caso, uma sombra também é aplicada, embora a aplicação de sombras funciona da mesma forma para o caminho de desenho.

O texto resultante é exibido com a imagem, conforme mostrado abaixo:

 ![](core-graphics-images/05-text-on-image.png "O texto resultante é exibido com a imagem")

## <a name="memory-backed-images"></a>Imagens com suporte de memória

Além de desenho para o contexto de gráficos do modo de exibição, elementos gráficos principais o dá suporte a memória de desenho feito imagens, também conhecido como desenho fora da tela. Isso requer:

-  Criando um contexto de gráficos que é apoiada por na memória bitmap
-  Definir o estado de desenho e emitir comandos de desenho
-  Obtendo a imagem de contexto
-  Removendo o contexto


Ao contrário de `Draw` método, em que o contexto é fornecido pelo modo de exibição, nesse caso, você cria o contexto de uma das duas maneiras:

1. Chamando `UIGraphics.BeginImageContext` (ou `BeginImageContextWithOptions`)

2. Criando um novo `CGBitmapContextInstance`

 `CGBitmapContextInstance` é útil quando você estiver trabalhando diretamente com os bits de imagem, por exemplo, para casos em que você está usando um algoritmo de manipulação de imagem personalizada. Em todos os outros casos, você deve usar `BeginImageContext` ou `BeginImageContextWithOptions`.

Quando você tiver um contexto de imagem, a adição de código de desenho é exatamente como ele está em um `UIView` subclasse. Por exemplo, o exemplo de código usado anteriormente para desenhar um triângulo pode ser usado para desenhar a uma imagem na memória em vez de usar um `UIView`, conforme mostrado abaixo:

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

Um uso comum de desenho para um bitmap de memória com suporte é para capturar uma imagem de qualquer `UIView`. Por exemplo, o código a seguir renderiza a camada do modo de exibição para um contexto de bitmap e cria um `UIImage` dele:

```csharp
UIGraphics.BeginImageContext (cellView.Frame.Size);

//render the view's layer in the current context
anyView.Layer.RenderInContext (UIGraphics.GetCurrentContext ());

//get a UIImage from the context
UIImage anyViewImage = UIGraphics.GetImageFromCurrentImageContext ();
UIGraphics.EndImageContext ();
```

## <a name="drawing-pdfs"></a>Desenho de PDFs

Além de imagens, gráficos principais dá suporte a desenho do PDF. Como imagens, você pode renderizar um PDF na memória, bem como ler um PDF para renderização em um `UIView`.

### <a name="pdf-in-a-uiview"></a>PDF em uma UIView

Elementos gráficos principais também dá suporte a leitura de um PDF de um arquivo e renderizá-lo em um modo de exibição usando o `CGPDFDocument` classe. O `CGPDFDocument` classe representa um PDF no código e pode ser usado para ler e desenhar páginas.

Por exemplo, o código a seguir em um `UIView` subclasse lê um PDF de um arquivo em um `CGPDFDocument`:

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

O `Draw` método, em seguida, pode usar o `CGPDFDocument` ler uma página em `CGPDFPage` e renderizá-lo chamando `DrawPDFPage`, conforme mostrado abaixo:

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

Para um PDF na memória, você precisa criar um contexto PDF chamando `BeginPDFContext`. Desenho em PDF é granular para páginas. Cada página é iniciada ao chamar `BeginPDFPage` e concluída chamando `EndPDFContent`, com os gráficos de código entre os dois. Além disso, como com o desenho da imagem, memória copiadas em PDF de desenho usa uma origem na parte inferior esquerda, que pode ser contabilizada, modificando o CTM apenas como com imagens.

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

O texto resultante é desenhado para PDF, que, em seguida, está contido em um `NSData` que podem ser salvos, carregados, enviado por email, etc.


## <a name="summary"></a>Resumo

Neste artigo vimos os recursos gráficos fornecidos por meio de *gráficos principais* framework. Vimos como usar gráficos principais para desenhar geometria, imagens e PDFs dentro do contexto de um `UIView,` bem como para contextos de gráficos com suporte de memória.

## <a name="related-links"></a>Links relacionados

- [Exemplo de elementos gráficos principais](https://developer.xamarin.com/samples/monotouch/GraphicsAndAnimation/)
- [Instruções passo a passo de animação e elementos gráficos](~/ios/platform/graphics-animation-ios/graphics-animation-walkthrough.md)
- [Animação principal](~/ios/platform/graphics-animation-ios/core-animation.md)
- [Receitas de animação de núcleo](https://github.com/xamarin/recipes/tree/master/Recipes/ios/animation/coreanimation)
