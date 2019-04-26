---
title: Estrutura Vision no xamarin. IOS
description: Este documento descreve como usar o iOS 11 estrutura Vision no xamarin. IOS. Especificamente, ele aborda a detecção de retângulo e detecção facial.
ms.prod: xamarin
ms.assetid: 7273ED68-7B7D-4252-B3A0-02DB2E357A8C
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 08/31/2017
ms.openlocfilehash: 291cbdb93cfb6ac123d740e98065ba877bb44da5
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61159635"
---
# <a name="vision-framework-in-xamarinios"></a>Estrutura Vision no xamarin. IOS

A estrutura da pesquisa Visual computacional adiciona um número de novos recursos para o iOS 11, inclusive de processamento de imagens:

- [Detecção de retângulo](#rectangles)
- [Detecção facial](#faces)
- Análise de imagem de aprendizado de máquina (discutidos [CoreML](~/ios/platform/introduction-to-ios11/coreml.md))
- Detecção de código de barras
- Análise de alinhamento da imagem
- Detecção de texto
- Detecção de horizonte
- Acompanhamento e detecção de objetos

![Fotografar com três retângulos detectados](vision-images/found-rectangles-tiny.png) ![Fotografar com duas faces detectadas](vision-images/xamarin-home-faces-tiny.png)

Detecção facial e detecção do retângulo são discutidos em mais detalhes abaixo.

<a name="rectangles" />

## <a name="rectangle-detection"></a>Detecção de retângulo

O [VisionRects exemplo](https://developer.xamarin.com/samples/monotouch/ios11/VisionRectangles/) mostra como processar uma imagem e desenhar retângulos detectados nele.

### <a name="1-initialize-the-vision-request"></a>1. Inicializar a solicitação de visão

Na `ViewDidLoad`, crie um `VNDetectRectanglesRequest` que faz referência a `HandleRectangles` método ser chamado no final de cada solicitação:

O `MaximumObservations` também deve ser definida a propriedade, caso contrário, o padrão será 1 e apenas um único resultado será retornado.

```csharp
RectangleRequest = new VNDetectRectanglesRequest(HandleRectangles);
RectangleRequest.MaximumObservations = 10;
```

### <a name="2-start-the-vision-processing"></a>2. Iniciar o processamento da pesquisa Visual computacional

O código a seguir inicia o processamento da solicitação. No **VisionRects** amostra, esse código é executado depois que o usuário selecionou uma imagem:

```csharp
// Run the rectangle detector
var handler = new VNImageRequestHandler(ciImage, uiImage.Orientation.ToCGImagePropertyOrientation(), new VNImageOptions());
DispatchQueue.DefaultGlobalQueue.DispatchAsync(()=>{
  handler.Perform(new VNRequest[] {RectangleRequest}, out NSError error);
});
```

Esse manipulador passa o `ciImage` para a estrutura de visão `VNDetectRectanglesRequest` que foi criado na etapa 1.

### <a name="3-handle-the-results-of-vision-processing"></a>3. Lidar com os resultados do processamento da pesquisa Visual computacional

Depois que a detecção de retângulo for concluída, a estrutura executará o `HandleRectangles` método, um resumo do que é mostrado abaixo:

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

### <a name="4-display-the-results"></a>4. Exibir os resultados

O `OverlayRectangles` método na **VisionRectangles** exemplo tem três funções:

- Renderizando a imagem de origem
- Desenhar um retângulo para indicar onde cada uma delas foi detectada, e
- Adicionando um rótulo de texto para cada retângulo usando CoreGraphics.

Modo de exibição de [fonte do exemplo](https://developer.xamarin.com/samples/monotouch/ios11/VisionRectangles/) para o método CoreGraphics exato.

![Fotografar com três retângulos detectados](vision-images/found-rectangles-phone-sml.png)

### <a name="5-further-processing"></a>5. Processamento adicional

Detecção de retângulo costuma ser a primeira etapa em uma cadeia de operações, como com [Este exemplo CoreMLVision](~/ios/platform/introduction-to-ios11/coreml.md#coremlvision), onde os retângulos são passados para um modelo de CoreML analisar dígitos manuscritos.


<a name="faces" />

## <a name="face-detection"></a>Detecção facial

O [amostra VisionFaces](https://developer.xamarin.com/samples/monotouch/ios11/VisionFaces/) funciona de maneira semelhante para o **VisionRectangles** de exemplo, usando uma classe diferente de solicitação de visão.

### <a name="1-initialize-the-vision-request"></a>1. Inicializar a solicitação de visão

Na `ViewDidLoad`, crie um `VNDetectFaceRectanglesRequest` que faz referência a `HandleRectangles` método ser chamado no final de cada solicitação.

```csharp
FaceRectangleRequest = new VNDetectFaceRectanglesRequest(HandleRectangles);
```

### <a name="2-start-the-vision-processing"></a>2. Iniciar o processamento da pesquisa Visual computacional

O código a seguir inicia o processamento da solicitação. No **VisionFaces** esse código é executado depois que o usuário selecionou uma imagem de exemplo:

```csharp
// Run the face detector
var handler = new VNImageRequestHandler(ciImage, uiImage.Orientation.ToCGImagePropertyOrientation(), new VNImageOptions());
DispatchQueue.DefaultGlobalQueue.DispatchAsync(()=>{
  handler.Perform(new VNRequest[] {FaceRectangleRequest}, out NSError error);
});
```

Esse manipulador passa o `ciImage` para a estrutura de visão `VNDetectFaceRectanglesRequest` que foi criado na etapa 1.

### <a name="3-handle-the-results-of-vision-processing"></a>3. Lidar com os resultados do processamento da pesquisa Visual computacional

Depois que a detecção de face for concluída, o manipulador é executado o `HandleRectangles` método que executa o tratamento de erros e exibe os limites das faces detectadas e chama o `OverlayRectangles` para desenhar retângulos delimitadores na imagem original:

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

### <a name="4-display-the-results"></a>4. Exibir os resultados

O `OverlayRectangles` método na **VisionFaces** exemplo tem três funções:

- Renderizando a imagem de origem
- Desenhar um retângulo para cada rosto detectado, e
- Adicionando um rótulo de texto para cada face usando CoreGraphics.

Modo de exibição de [fonte do exemplo](https://developer.xamarin.com/samples/monotouch/ios11/VisionFaces/) para o método CoreGraphics exato.

![Fotografar com duas faces detectadas](vision-images/found-faces-phone-sml.png)

### <a name="5-further-processing"></a>5. Processamento adicional

A estrutura de visão inclui recursos adicionais para detectar o rosto, como os olhos e boca. Use o `VNDetectFaceLandmarksRequest` tipo, que retornará `VNFaceObservation` resultados como na etapa 3 acima, mas com adicionais `VNFaceLandmark` dados.


## <a name="related-links"></a>Links relacionados

- [Visão retângulos (amostra)](https://developer.xamarin.com/samples/monotouch/ios11/VisionRectangles/)
- [Visão Faces (amostra)](https://developer.xamarin.com/samples/monotouch/ios11/VisionFaces/)
- [Avanços na imagem de Core - filtros, Bare Metal, visão e mais (WWDC) (vídeo)](https://developer.apple.com/videos/play/wwdc2017/510/)
