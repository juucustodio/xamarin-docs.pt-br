---
title: Imagem principal no Xamarin. iOS
description: A imagem principal é uma nova estrutura introduzida com o iOS 5 para fornecer processamento de imagens e funcionalidade de aprimoramento de vídeo ao vivo. Este artigo apresenta esses recursos com exemplos do Xamarin. iOS.
ms.prod: xamarin
ms.assetid: 91E0780B-FF8A-E70D-9CD4-419119612B2D
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/19/2017
ms.openlocfilehash: 6960fe3db1ddf7d6d911fe8151e49b1a42388d26
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/16/2019
ms.locfileid: "69527846"
---
# <a name="core-image-in-xamarinios"></a>Imagem principal no Xamarin. iOS

_A imagem principal é uma nova estrutura introduzida com o iOS 5 para fornecer processamento de imagens e funcionalidade de aprimoramento de vídeo ao vivo. Este artigo apresenta esses recursos com exemplos do Xamarin. iOS._

A imagem principal é uma nova estrutura introduzida no iOS 5 que fornece vários filtros e efeitos internos para aplicar a imagens e vídeos, incluindo a detecção facial.

Este documento contém exemplos simples de:

- Detecção facial.
- Aplicando filtros a uma imagem
- Listando os filtros disponíveis.


Esses exemplos devem ajudar a começar a incorporar recursos de imagem principal em seus aplicativos Xamarin. iOS.

## <a name="requirements"></a>Requisitos

Você deve usar a versão mais recente do Xcode.

## <a name="face-detection"></a>Detecção Facial

O recurso de detecção de face de imagem principal faz exatamente o que ele diz – ele tenta identificar faces em uma foto e retorna as coordenadas de quaisquer rostos que ele reconheça. Essas informações podem ser usadas para contar o número de pessoas em uma imagem, desenhar indicadores na imagem (por exemplo, para a ' marcação ' de pessoas em uma fotografia) ou qualquer outra coisa que você possa imaginar.

Este código do CoreImage\SampleCode.cs demonstra como criar e usar a detecção facial em uma imagem inserida:

```csharp
var image = new UIImage("photoFace.JPG");
var context = CIContext.FromOptions(null);
var detector = CIDetector.CreateFaceDetector (context, true);
var ciImage = CIImage.FromCGImage(image.CGImage);
CIFeature[] features = detector.FeaturesInImage(ciImage);
```

A matriz de recursos será preenchida `CIFaceFeature` com objetos (se quaisquer faces forem detectadas). Há um `CIFaceFeature` para cada face. `CIFaceFeature`tem as seguintes propriedades:

- HasMouthPosition – se uma boca foi detectada para essa face.
- HasLeftEyePosition – se o olho à esquerda foi detectado para esta face.
- HasRightEyePosition – se o olho certo foi detectado para essa face. 
- MouthPosition – as coordenadas da boca para essa face.
- LeftEyePosition – as coordenadas do olho à esquerda para essa face.
- RightEyePosition – as coordenadas do olho certo para essa face.


As coordenadas para todas essas propriedades têm sua origem no canto inferior esquerdo – ao contrário de UIKit, que usa o canto superior esquerdo como a origem. Ao usar as coordenadas `CIFaceFeature` , certifique-se de ' Inverter '. Essa exibição de imagem personalizada muito básica no CoreImage\CoreImageViewController.cs demonstra como desenhar triângulos de "indicador facial" na imagem (Observe o `FlipForBottomOrigin` método):

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

Em seguida, no arquivo SampleCode.cs, a imagem e os recursos são atribuídos antes que a imagem seja redesenhada:

```csharp
faceView.Image = image;
faceView.Features = features;
faceView.SetNeedsDisplay();
```

A captura de tela mostra a saída de exemplo: os locais dos recursos faciais detectados são exibidos em um UITextView e desenhados na imagem de origem usando CoreGraphics.

Devido ao modo como o reconhecimento facial funciona, ocasionalmente ele detectará coisas além de faces humanas (como essas Monkeys!).

## <a name="filters"></a>Filtros

Há mais de 50 filtros internos diferentes, e a estrutura é extensível para que novos filtros possam ser implementados.

## <a name="using-filters"></a>Usando filtros

A aplicação de um filtro a uma imagem tem quatro etapas distintas: carregar a imagem, criar o filtro, aplicar o filtro e salvar (ou exibir) o resultado.

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

Em terceiro lugar, `OutputImage` acesse a propriedade `CreateCGImage` e chame o método para renderizar o resultado final.

```csharp
CIImage output = sepia.OutputImage;
var context = CIContext.FromOptions(null);
var cgimage = context.CreateCGImage (output, output.Extent);
```

Por fim, atribua a imagem a uma exibição para ver o resultado. Em um aplicativo do mundo real, a imagem resultante pode ser salva no sistema de arquivos, no álbum de fotos, em um tweet ou em um email.

```csharp
var ui = UIImage.FromImage (cgimage);
imgview.Image = ui;
```

Essas capturas de tela mostram o resultado `CISepia` dos `CIHueAdjust` filtros e demonstrados no código de exemplo CoreImage. zip.

Consulte [ajustar o contrato e o brilho de uma receita de imagem](https://github.com/xamarin/recipes/tree/master/Recipes/ios/media/coreimage/adjust_contrast_and_brightness_of_an_image) para obter um `CIColorControls` exemplo do filtro.

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

### <a name="listing-filters-and-their-properties"></a>Listando filtros e suas propriedades

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

A [referência de classe CIFilter](https://developer.apple.com/library/prerelease/ios/#documentation/GraphicsImaging/Reference/QuartzCoreFramework/Classes/CIFilter_Class/Reference/Reference.html) descreve os filtros internos 50 e suas propriedades. Usando o código acima, você pode consultar as classes de filtro, incluindo valores padrão para parâmetros e os valores máximos e mínimos permitidos (que podem ser usados para validar entradas antes de aplicar um filtro).

A saída de categorias de lista tem esta aparência no simulador – você pode percorrer a lista para ver todos os filtros e seus parâmetros.

 [![](introduction-to-coreimage-images/coreimage05.png "A saída de categorias de lista tem esta aparência no simulador")](introduction-to-coreimage-images/coreimage05.png#lightbox)

Cada filtro listado foi exposto como uma classe no Xamarin. iOS, de modo que você também pode explorar a API Xamarin. iOS. CoreImage no navegador de assembly ou usando o preenchimento automático no Visual Studio para Mac ou no Visual Studio. 

## <a name="summary"></a>Resumo

Este artigo mostrou como usar alguns dos novos recursos da estrutura de imagem principal do iOS 5, como detecção de face e aplicação de filtros a uma imagem. Há dezenas de filtros de imagem diferentes disponíveis na estrutura para uso.

## <a name="related-links"></a>Links relacionados

- [Imagem principal (exemplo)](https://docs.microsoft.com/samples/xamarin/ios-samples/coreimage)
- [Ajustar o contrato e o brilho de uma receita de imagem](https://github.com/xamarin/recipes/tree/master/Recipes/ios/media/coreimage/adjust_contrast_and_brightness_of_an_image)
- [Usando filtros de imagem de núcleo](https://developer.apple.com/library/prerelease/ios/#documentation/GraphicsImaging/Conceptual/CoreImaging/ci_tasks/ci_tasks.html)
- [Referência de classe CIFilter](https://developer.apple.com/library/prerelease/ios/#documentation/GraphicsImaging/Reference/QuartzCoreFramework/Classes/CIFilter_Class/Reference/Reference.htm)
