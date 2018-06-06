---
title: Imagem de núcleo no xamarin
description: Imagem principal é uma nova estrutura introduzida com o iOS 5 para fornecer processamento de imagem e ao vivo a funcionalidade de aprimoramento de vídeo. Este artigo apresenta estes recursos com exemplos de xamarin.
ms.prod: xamarin
ms.assetid: 91E0780B-FF8A-E70D-9CD4-419119612B2D
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/19/2017
ms.openlocfilehash: 6032554a0ddbda26ff5de94f6035bc4f8c15a22a
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/05/2018
ms.locfileid: "34786626"
---
# <a name="core-image-in-xamarinios"></a>Imagem de núcleo no xamarin

_Imagem principal é uma nova estrutura introduzida com o iOS 5 para fornecer processamento de imagem e ao vivo a funcionalidade de aprimoramento de vídeo. Este artigo apresenta estes recursos com exemplos de xamarin._

Imagem principal é uma nova estrutura introduzida no iOS 5 que fornece uma série de filtros internos e efeitos a serem aplicados a imagens e vídeos, incluindo a detecção de rosto.

Este documento contém exemplos simples de:

-  Detecção de rosto.
-  Aplicar filtros a uma imagem
-  Listar os filtros disponíveis.


Estes exemplos devem ajudar você a começar a incorporação de recursos de imagem principal em seus aplicativos xamarin.

## <a name="requirements"></a>Requisitos

Você deve usar a versão mais recente do Xcode.

## <a name="face-detection"></a>Detecção de rosto

O recurso de detecção de rosto de imagem principal faz hotfixes – tenta identificar as faces em uma foto e retorna as coordenadas de qualquer faces que reconhece. Essas informações podem ser usadas para contar o número de pessoas em uma imagem, desenhar indicadores na imagem (por exemplo. para 'marcação' pessoas em uma fotografia), ou qualquer outra coisa você pode pensar.

Esse código da CoreImage\SampleCode.cs demonstra como criar e usar a detecção de rosto em uma imagem inserida:

```csharp
var image = new UIImage("photoFace.JPG");
var context = CIContext.FromOptions(null);
var detector = CIDetector.CreateFaceDetector (context, true);
var ciImage = CIImage.FromCGImage(image.CGImage);
CIFeature[] features = detector.FeaturesInImage(ciImage);
```

A matriz de recursos será preenchida com `CIFaceFeature` objetos (se qualquer faces foram detectadas). Há um `CIFaceFeature` para cada face. `CIFaceFeature` tem as seguintes propriedades:

-  HasMouthPosition – se uma boca foi detectada para este face.
-  HasLeftEyePosition – se o olho esquerdo foi detectado para este face.
-  HasRightEyePosition – se o olho correto foi detectado para este face. 
-  MouthPosition – as coordenadas da boca para esta face.
-  LeftEyePosition – as coordenadas do olho esquerdo para esta face.
-  RightEyePosition – as coordenadas do olho correto para esta face.


As coordenadas para todas essas propriedades têm origem no canto inferior esquerdo – diferentemente UIKit que usa a parte superior esquerda como a origem. Ao usar as coordenadas em `CIFaceFeature` Certifique-se de que 'Virar'-los. Este modo de exibição de imagem personalizada muito básica CoreImage\CoreImageViewController.cs demonstra como desenhar triângulos 'indicador face' na imagem (Observe o `FlipForBottomOrigin` método):

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

Em seguida, no arquivo SampleCode.cs a imagem e recursos são atribuídos antes que a imagem é redesenhada:

```csharp
faceView.Image = image;
faceView.Features = features;
faceView.SetNeedsDisplay();
```

A captura de tela mostra a saída de exemplo: os locais dos recursos faciais detectados são exibidos em um UITextView e desenhados para a imagem de origem usando CoreGraphics.

Devido a maneira como reconhecimento facial funciona ele ocasionalmente detectará coisas além de faces humanas (como esses monkeys brinquedos!).

## <a name="filters"></a>Filtros

Há mais de 50 filtros internos diferentes e a estrutura é extensível para que novos filtros podem ser implementados.

## <a name="using-filters"></a>Usando filtros

Aplicar um filtro a uma imagem tem quatro etapas distintas: Carregando a imagem, criar o filtro, aplicar o filtro e salvar (ou exibindo) o resultado.

Primeiro, carregue uma imagem em um `CIImage` objeto.

```csharp
var uiimage = UIImage.FromFile ("photo.JPG");
var ciimage = new CIImage (uiimage);
```

Em seguida, criar a classe de filtro e definir suas propriedades.

```csharp
var sepia = new CISepiaTone();
sepia.Image = ciimage;
sepia.Intensity = 0.8f;
```

Em terceiro lugar, acessar o `OutputImage` propriedade e chame o `CreateCGImage` método para processar o resultado final.

```csharp
CIImage output = sepia.OutputImage;
var context = CIContext.FromOptions(null);
var cgimage = context.CreateCGImage (output, output.Extent);
```

Por fim, atribua a imagem a um modo de exibição para ver o resultado. Em um aplicativo do mundo real, a imagem resultante pode ser salvo para o sistema de arquivos, álbum de fotos, um Tweet ou email.

```csharp
var ui = UIImage.FromImage (cgimage);
imgview.Image = ui;
```

Essas capturas de tela mostram o resultado da `CISepia` e `CIHueAdjust` filtros que são demonstrados no CoreImage.zip código de exemplo.

Consulte o [ajustar contrato e o brilho da receita de uma imagem](https://developer.xamarin.com/recipes/ios/media/coreimage/adjust_contrast_and_brightness_of_an_image) para obter um exemplo de `CIColorControls` filtro.

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

### <a name="listing-filters-and-their-properties"></a>Lista de filtros e suas propriedades

Esse código da CoreImage\SampleCode.cs gera a lista completa de filtros internos e seus parâmetros.

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

O [CIFilter Referência de classe](https://developer.apple.com/library/prerelease/ios/#documentation/GraphicsImaging/Reference/QuartzCoreFramework/Classes/CIFilter_Class/Reference/Reference.html) descreve os 50 filtros internos e suas propriedades. Usando o código acima você pode consultar as classes de filtro, incluindo valores padrão para parâmetros e os valores mínimos e máximo de permitidos (que podem ser usados para validar entradas antes de aplicar um filtro).

A saída lista categorias tem esta aparência no simulador – você pode rolar pela lista para ver todos os filtros e seus parâmetros.

 [![](introduction-to-coreimage-images/coreimage05.png "A saída lista categorias tem esta aparência no simulador")](introduction-to-coreimage-images/coreimage05.png#lightbox)

Cada filtro listado foi exposto como uma classe em xamarin, para que você também pode explorar o API Xamarin.iOS.CoreImage no navegador do Assembly ou usando o preenchimento automático no Visual Studio para Mac ou o Visual Studio. 

## <a name="summary"></a>Resumo

Este artigo mostra como usar os novos recursos de estrutura do iOS 5 imagem principal como detecção de rosto e aplicar filtros a uma imagem. Dezenas de filtros de imagem diferentes estão disponíveis no framework para uso.

## <a name="related-links"></a>Links relacionados

- [Imagem do Core (exemplo)](https://developer.xamarin.com/samples/CoreImage/)
- [Ajustar o contrato e o brilho da receita de uma imagem](https://developer.xamarin.com/recipes/ios/media/coreimage/adjust_contrast_and_brightness_of_an_image)
- [Usando filtros de imagem de núcleo](https://developer.apple.com/library/prerelease/ios/#documentation/GraphicsImaging/Conceptual/CoreImaging/ci_tasks/ci_tasks.html)
- [Referência de classe CIFilter](https://developer.apple.com/library/prerelease/ios/#documentation/GraphicsImaging/Reference/QuartzCoreFramework/Classes/CIFilter_Class/Reference/Reference.htm)
