---
title: Imagem principal no xamarin. IOS
description: Imagem Core é uma nova estrutura introduzida com o iOS 5 para fornecer processamento de imagens e ao vivo a funcionalidade de aprimoramento de vídeo. Este artigo apresenta estes recursos com exemplos de xamarin. IOS.
ms.prod: xamarin
ms.assetid: 91E0780B-FF8A-E70D-9CD4-419119612B2D
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/19/2017
ms.openlocfilehash: d71f14c26865b71eca991910df4a68f2540f9715
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50115400"
---
# <a name="core-image-in-xamarinios"></a>Imagem principal no xamarin. IOS

_Imagem Core é uma nova estrutura introduzida com o iOS 5 para fornecer processamento de imagens e ao vivo a funcionalidade de aprimoramento de vídeo. Este artigo apresenta estes recursos com exemplos de xamarin. IOS._

Imagem Core é uma nova estrutura introduzida no iOS 5 que fornece uma série de filtros internos e efeitos a serem aplicados a imagens e vídeos, incluindo a detecção de face.

Este documento contém exemplos simples de:

-  Detecção facial.
-  Aplicação de filtros a uma imagem
-  Listando os filtros disponíveis.


Esses exemplos devem ajudar você a começar a incorporar recursos de imagem principal em seus aplicativos do xamarin. IOS.

## <a name="requirements"></a>Requisitos

Você deve usar a versão mais recente do Xcode.

## <a name="face-detection"></a>Detecção facial

O recurso de detecção de face da imagem principal faz apenas o que diz – ele tenta identificar rostos em uma foto e retorna as coordenadas de qualquer faces que ele reconhece. Essas informações podem ser usadas para contar o número de pessoas em uma imagem, desenhar indicadores na imagem (por exemplo. para 'marcação' pessoas em uma fotografia), ou qualquer outra coisa você pode pensar.

Esse código de CoreImage\SampleCode.cs demonstra como criar e usar a detecção de face em uma imagem inserida:

```csharp
var image = new UIImage("photoFace.JPG");
var context = CIContext.FromOptions(null);
var detector = CIDetector.CreateFaceDetector (context, true);
var ciImage = CIImage.FromCGImage(image.CGImage);
CIFeature[] features = detector.FeaturesInImage(ciImage);
```

A matriz de recursos será preenchida com `CIFaceFeature` objetos (se nenhuma face foi detectada). Há um `CIFaceFeature` para cada face. `CIFaceFeature` tem as seguintes propriedades:

-  HasMouthPosition – se uma boca foi detectada para essa face.
-  HasLeftEyePosition – se o olho à esquerda foi detectado para essa face.
-  HasRightEyePosition – se o olho à direita foi detectado para essa face. 
-  MouthPosition – as coordenadas da boca para essa face.
-  LeftEyePosition – as coordenadas do olho à esquerda para essa face.
-  RightEyePosition – as coordenadas do olho à direita para essa face.


As coordenadas para todas essas propriedades têm origem no canto inferior esquerdo – ao contrário de UIKit que usa o canto superior esquerdo como a origem. Ao usar as coordenadas em `CIFaceFeature` não se esqueça de 'Inverter'-los. Este modo de exibição de imagem personalizada muito básico em CoreImage\CoreImageViewController.cs demonstra como desenhar triângulos 'indicador de face' na imagem (Observe o `FlipForBottomOrigin` método):

```csharp
public class FaceDetectImageView : UIView
{
    public Xamarin.iOS.CoreImage.CIFeature[] Features;
    public UIImage Image;
    public FaceDetectImageView (RectangleF rect) : base(rect) {}
    CGPath path;
    public override void Draw (RectangleF rect) {
        base.Draw (rect);
        if (Image != null)
            Image.Draw(rect);

        using (var context = UIGraphics.GetCurrentContext()) {
            context.SetLineWidth(4);
            UIColor.Red.SetStroke ();
            UIColor.Clear.SetFill ();
            if (Features != null) {
                foreach (var feature in Features) { // for each face
                    var facefeature = (CIFaceFeature)feature;
                    path = new CGPath ();
                    path.AddLines(new PointF[]{ // assumes all 3 features found
                        FlipForBottomOrigin(facefeature.LeftEyePosition, 200),
                        FlipForBottomOrigin(facefeature.RightEyePosition, 200),
                        FlipForBottomOrigin(facefeature.MouthPosition, 200)
                    });
                    path.CloseSubpath();
                    context.AddPath(path);
                    context.DrawPath(CGPathDrawingMode.FillStroke);
                }
            }
        }
    }
    /// <summary>
    /// Face recognition coordinates have their origin in the bottom-left
    /// but we are drawing with the origin in the top-left, so "flip" the point
    /// </summary>
    PointF FlipForBottomOrigin (PointF point, int height)
    {
        return new PointF(point.X, height - point.Y);
    }
}
```

Em seguida, no arquivo SampleCode.cs a imagem e os recursos são atribuídos antes que a imagem é redesenhada:

```csharp
faceView.Image = image;
faceView.Features = features;
faceView.SetNeedsDisplay();
```

A captura de tela mostra a saída de exemplo: os locais dos recursos faciais detectados são exibidos em um UITextView e desenhados na imagem de origem usando CoreGraphics.

Por causa da maneira que funciona de reconhecimento facial-lo ocasionalmente detectará coisas além de faces humanas (como essas brinquedo Monkeys (Macacos)!).

## <a name="filters"></a>Filtros

Há mais de 50 filtros internos diferentes, e a estrutura é extensível para que os novos filtros podem ser implementados.

## <a name="using-filters"></a>Usando filtros

Aplicando um filtro em uma imagem tem quatro etapas distintas: Carregando a imagem, criar o filtro, aplicar o filtro e salvar (ou exibindo) o resultado.

Primeiro, carregue uma imagem em um `CIImage` objeto.

```csharp
var uiimage = UIImage.FromFile ("photo.JPG");
var ciimage = new CIImage (uiimage);
```

Em segundo lugar, crie a classe de filtro e defina suas propriedades.

```csharp
var sepia = new CISepiaTone();
sepia.Image = ciimage;
sepia.Intensity = 0.8f;
```

Em terceiro lugar, acessar o `OutputImage` propriedade e chame o `CreateCGImage` método para renderizar o resultado final.

```csharp
CIImage output = sepia.OutputImage;
var context = CIContext.FromOptions(null);
var cgimage = context.CreateCGImage (output, output.Extent);
```

Por fim, atribua a imagem para uma exibição para ver o resultado. Em um aplicativo do mundo real, a imagem resultante pode ser salva para o sistema de arquivos, o álbum de fotos, um Tweet ou email.

```csharp
var ui = UIImage.FromImage (cgimage);
imgview.Image = ui;
```

Essas capturas de tela mostram o resultado do `CISepia` e `CIHueAdjust` filtros que são demonstrados a CoreImage.zip exemplos de código.

Consulte a [ajustar o contrato e o brilho de uma imagem de receita](https://github.com/xamarin/recipes/tree/master/Recipes/ios/media/coreimage/adjust_contrast_and_brightness_of_an_image) para obter um exemplo da `CIColorControls` filtro.

```csharp
var uiimage = UIImage.FromFile("photo.JPG");
var ciimage = new CIImage(uiimage);
var hueAdjust = new CIHueAdjust();   // first filter
hueAdjust.Image = ciimage;
hueAdjust.Angle = 2.094f;
var sepia = new CISepiaTone();       // second filter
sepia.Image = hueAdjust.OutputImage; // output from last filter, input to this one
sepia.Intensity = 0.3f;
CIFilter color = new CIColorControls() { // third filter
    Saturation = 2,
    Brightness = 1,
    Contrast = 3,
    Image = sepia.OutputImage    // output from last filter, input to this one
};
var output = color.OutputImage;
var context = CIContext.FromOptions(null);
// ONLY when CreateCGImage is called do all the effects get rendered
var cgimage = context.CreateCGImage (output, output.Extent);
var ui = UIImage.FromImage (cgimage);
imgview.Image = ui;
```

```csharp
var context = CIContext.FromOptions (null);
```

```csharp
var context = CIContext.FromOptions(new CIContextOptions() {
    UseSoftwareRenderer = true  // CPU
});
var cgimage = context.CreateCGImage (output, output.Extent);
var ui = UIImage.FromImage (cgimage);
imgview.Image = ui;
```

### <a name="listing-filters-and-their-properties"></a>Filtros de listagem e suas propriedades

Esse código de CoreImage\SampleCode.cs gera a lista completa de filtros internos e seus parâmetros.

```csharp
var filters = CIFilter.FilterNamesInCategories(new string[0]);
foreach (var filter in filters){
   display.Text += filter +"\n";
   var f = CIFilter.FromName (filter);
   foreach (var key in f.InputKeys){
     var attributes = (NSDictionary)f.Attributes[new NSString(key)];
     var attributeClass = attributes[new NSString("CIAttributeClass")];
     display.Text += "   " + key;
     display.Text += "   " + attributeClass + "\n";
   }
}
```

O [referência de classe CIFilter](https://developer.apple.com/library/prerelease/ios/#documentation/GraphicsImaging/Reference/QuartzCoreFramework/Classes/CIFilter_Class/Reference/Reference.html) descreve os filtros internos 50 e suas propriedades. Usando o código acima, você pode consultar as classes de filtro, incluindo os valores padrão para parâmetros e os valores mínimos e máximo de permitidos (que pode ser usados para validar entradas antes de aplicar um filtro).

A saída lista categorias tem esta aparência no simulador – você pode rolar a lista para ver todos os filtros e seus parâmetros.

 [![](introduction-to-coreimage-images/coreimage05.png "A saída lista categorias tem esta aparência no simulador")](introduction-to-coreimage-images/coreimage05.png#lightbox)

Cada filtro listado foi exposto como uma classe no xamarin. IOS, portanto, você também pode explorar a API Xamarin.iOS.CoreImage no navegador de Assembly ou usando o preenchimento automático no Visual Studio para Mac ou Visual Studio. 

## <a name="summary"></a>Resumo

Este artigo mostra como usar alguns dos novos iOS 5 imagem Core framework recursos como detecção facial e aplicar filtros a uma imagem. Existem dezenas de filtros de imagem diferentes disponíveis no framework para uso.

## <a name="related-links"></a>Links relacionados

- [Imagem do Core (amostra)](https://developer.xamarin.com/samples/CoreImage/)
- [Ajustar o contrato e o brilho da receita de uma imagem](https://github.com/xamarin/recipes/tree/master/Recipes/ios/media/coreimage/adjust_contrast_and_brightness_of_an_image)
- [Usando filtros de imagem de núcleo](https://developer.apple.com/library/prerelease/ios/#documentation/GraphicsImaging/Conceptual/CoreImaging/ci_tasks/ci_tasks.html)
- [Referência de classe CIFilter](https://developer.apple.com/library/prerelease/ios/#documentation/GraphicsImaging/Reference/QuartzCoreFramework/Classes/CIFilter_Class/Reference/Reference.htm)
