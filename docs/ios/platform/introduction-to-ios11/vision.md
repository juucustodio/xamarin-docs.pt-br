---
title: Estrutura de visão no Xamarin. iOS
description: Este documento descreve como usar a estrutura da visão do iOS 11 no Xamarin. iOS. Especificamente, ele aborda a detecção de retângulo e a detecção facial.
ms.prod: xamarin
ms.assetid: 7273ED68-7B7D-4252-B3A0-02DB2E357A8C
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 08/31/2017
ms.openlocfilehash: c33317835ff07826e8b9276c4f5cdc13c04c2680
ms.sourcegitcommit: e27e29c14b783263e063baaa65d4eecb8dd31f57
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/21/2021
ms.locfileid: "98628807"
---
# <a name="vision-framework-in-xamarinios"></a>Estrutura de visão no Xamarin. iOS

O Vision Framework adiciona uma série de novos recursos de processamento de imagens ao iOS 11, incluindo:

- [Detecção de retângulo](#rectangles)
- [Detecção Facial](#faces)
- Análise de imagem de Machine Learning (discutida em [do coreml](~/ios/platform/introduction-to-ios11/coreml.md))
- Detecção de código de barras
- Análise de alinhamento de imagem
- Detecção de texto
- Detecção de horizonte
- Rastreamento de & de detecção de objeto

![Fotografia com três retângulos detectados.](vision-images/found-rectangles-tiny.png) ![Fotografia com duas faces detectadas.](vision-images/xamarin-home-faces-tiny.png)

A detecção e a Detecção Facial de retângulo são discutidas em mais detalhes abaixo.

<a name="rectangles"></a>

## <a name="rectangle-detection"></a>Detecção de retângulo

O [exemplo VisionRects](/samples/xamarin/ios-samples/ios11-visionrectangles) mostra como processar uma imagem e desenhar os retângulos detectados nela.

### <a name="1-initialize-the-vision-request"></a>1. inicializar a solicitação de visão

No `ViewDidLoad` , crie um `VNDetectRectanglesRequest` que faça referência ao `HandleRectangles` método que será chamado ao final de cada solicitação:

A `MaximumObservations` propriedade também deve ser definida; caso contrário, ela usará como padrão 1 e apenas um único resultado será retornado.

```csharp
RectangleRequest = new VNDetectRectanglesRequest(HandleRectangles);
RectangleRequest.MaximumObservations = 10;
```

### <a name="2-start-the-vision-processing"></a>2. iniciar o processamento da visão

O código a seguir inicia o processamento da solicitação. No exemplo de **VisionRects** , esse código é executado depois que o usuário seleciona uma imagem:

```csharp
// Run the rectangle detector
var handler = new VNImageRequestHandler(ciImage, uiImage.Orientation.ToCGImagePropertyOrientation(), new VNImageOptions());
DispatchQueue.DefaultGlobalQueue.DispatchAsync(()=>{
  handler.Perform(new VNRequest[] {RectangleRequest}, out NSError error);
});
```

Esse manipulador passa o `ciImage` para a estrutura de visão `VNDetectRectanglesRequest` que foi criada na etapa 1.

### <a name="3-handle-the-results-of-vision-processing"></a>3. manipule os resultados do processamento da visão

Depois que a detecção de retângulo é concluída, a estrutura executa o `HandleRectangles` método, um resumo do qual é mostrado abaixo:

```csharp
private void HandleRectangles(VNRequest request, NSError error){
  var observations = request.GetResults<VNRectangleObservation>();
  // ... omitted error handling ...
  bool atLeastOneValid = false;
  foreach (var o in observations){
    if (InputImage.Extent.Contains(boundingBox)) {
      atLeastOneValid |= true;
    }
  }
  if (!atLeastOneValid) return;
  // Show the pre-processed image
  DispatchQueue.MainQueue.DispatchAsync(() =>
  {
    ClassificationLabel.Text = summary;
    ImageView.Image = OverlayRectangles(RawImage, imageSize, observations);
  });
}
```

### <a name="4-display-the-results"></a>4. exibir os resultados

O `OverlayRectangles` método no exemplo **VisionRectangles** tem três funções:

- Renderizando a imagem de origem,
- Desenhar um retângulo para indicar onde cada um foi detectado e
- Adicionando um rótulo de texto para cada retângulo usando CoreGraphics.

Exiba a [origem do exemplo](/samples/xamarin/ios-samples/ios11-visionrectangles) para o método CoreGraphics exato.

![Fotografia com três retângulos detectados em um dispositivo móvel.](vision-images/found-rectangles-phone-sml.png)

### <a name="5-further-processing"></a>5. processamento adicional

A detecção de retângulo geralmente é apenas a primeira etapa em uma cadeia de operações, como com [Este exemplo de CoreMLVision](~/ios/platform/introduction-to-ios11/coreml.md#coremlvision), em que os retângulos são passados para um modelo do coreml para analisar os dígitos manuscritos.

<a name="faces"></a>

## <a name="face-detection"></a>Detecção Facial

O [exemplo de VisionFaces](/samples/xamarin/ios-samples/ios11-visionfaces) funciona de maneira semelhante ao exemplo de **VisionRectangles** , usando uma classe de solicitação de visão diferente.

### <a name="1-initialize-the-vision-request"></a>1. inicializar a solicitação de visão

No `ViewDidLoad` , crie um `VNDetectFaceRectanglesRequest` que faça referência ao `HandleRectangles` método que será chamado ao final de cada solicitação.

```csharp
FaceRectangleRequest = new VNDetectFaceRectanglesRequest(HandleRectangles);
```

### <a name="2-start-the-vision-processing"></a>2. iniciar o processamento da visão

O código a seguir inicia o processamento da solicitação. No exemplo de **VisionFaces** , esse código é executado depois que o usuário seleciona uma imagem:

```csharp
// Run the face detector
var handler = new VNImageRequestHandler(ciImage, uiImage.Orientation.ToCGImagePropertyOrientation(), new VNImageOptions());
DispatchQueue.DefaultGlobalQueue.DispatchAsync(()=>{
  handler.Perform(new VNRequest[] {FaceRectangleRequest}, out NSError error);
});
```

Esse manipulador passa o `ciImage` para a estrutura de visão `VNDetectFaceRectanglesRequest` que foi criada na etapa 1.

### <a name="3-handle-the-results-of-vision-processing"></a>3. manipule os resultados do processamento da visão

Depois que a detecção de face for concluída, o manipulador executará o `HandleRectangles` método que executa o tratamento de erros e exibirá os limites das faces detectadas e chamará os `OverlayRectangles` retângulos delimitadores de desenho no quadro original:

```csharp
private void HandleRectangles(VNRequest request, NSError error){
  var observations = request.GetResults<VNFaceObservation>();
  // ... omitted error handling...
  var summary = "";
  var imageSize = InputImage.Extent.Size;
  bool atLeastOneValid = false;
  Console.WriteLine("Faces:");
  summary += "Faces:";
  foreach (var o in observations) {
    // Verify detected rectangle is valid. omitted
    var boundingBox = o.BoundingBox.Scaled(imageSize);
    if (InputImage.Extent.Contains(boundingBox)) {
      atLeastOneValid |= true;
    }
  }
  // Show the pre-processed image (on UI thread)
  DispatchQueue.MainQueue.DispatchAsync(() =>
  {
    ClassificationLabel.Text = summary;
    ImageView.Image = OverlayRectangles(RawImage, imageSize, observations);
  });
}
```

### <a name="4-display-the-results"></a>4. exibir os resultados

O `OverlayRectangles` método no exemplo **VisionFaces** tem três funções:

- Renderizando a imagem de origem,
- Desenhando um retângulo para cada face detectada e
- Adicionar um rótulo de texto para cada face usando CoreGraphics.

Exiba a [origem do exemplo](/samples/xamarin/ios-samples/ios11-visionfaces) para o método CoreGraphics exato.

![Fotografia com duas faces detectadas em um dispositivo móvel.](vision-images/found-faces-phone-sml.png)

### <a name="5-further-processing"></a>5. processamento adicional

O Vision Framework inclui recursos adicionais para detectar recursos faciais, como os olhos e a boca. Use o `VNDetectFaceLandmarksRequest` tipo, que retornará `VNFaceObservation` resultados como na etapa 3 acima, mas com dados adicionais `VNFaceLandmark` .

## <a name="related-links"></a>Links Relacionados

- [Retângulos de visão (exemplo)](/samples/xamarin/ios-samples/ios11-visionrectangles)
- [Faces de visão (exemplo)](/samples/xamarin/ios-samples/ios11-visionfaces)
- [Avanços na imagem principal-filtros, metal, visão e muito mais (WWDC) (vídeo)](https://developer.apple.com/videos/play/wwdc2017/510/)