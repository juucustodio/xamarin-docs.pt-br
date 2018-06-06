---
title: Estrutura de visão do xamarin
description: Este documento descreve como usar o iOS 11 visão Framework em xamarin. Especificamente, ele aborda a detecção de retângulo e enfrentam detecção.
ms.prod: xamarin
ms.assetid: 7273ED68-7B7D-4252-B3A0-02DB2E357A8C
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 08/31/2016
ms.openlocfilehash: c44c4b3ab12c1ba448f1befb6f831f5ad9119f18
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/05/2018
ms.locfileid: "34787413"
---
# <a name="vision-framework-in-xamarinios"></a>Estrutura de visão do xamarin

A estrutura de visão adiciona um número de novos recursos para o iOS 11, inclusive de processamento de imagem:

- [Detecção de retângulo](#rectangles)
- [Detecção de rosto](#faces)
- Análise de imagem de aprendizado de máquina (discutido na [CoreML](~/ios/platform/introduction-to-ios11/coreml.md))
- Detecção de código de barras
- Análise de alinhamento da imagem
- Detecção de texto
- Detecção de horizonte
- Controle de & objeto detecção

![Fotografar com três retângulos detectados](vision-images/found-rectangles-tiny.png) ![Fotografar com duas faces detectadas](vision-images/xamarin-home-faces-tiny.png)

Detecção de retângulo e detecção de rosto são discutidos em mais detalhes abaixo.

<a name="rectangles" />

## <a name="rectangle-detection"></a>Detecção de retângulo

O [VisionRects exemplo](https://developer.xamarin.com/samples/monotouch/ios11/VisionRectangles/) mostra como processar uma imagem e desenhar retângulos detectados nele.

### <a name="1-initialize-the-vision-request"></a>1. Inicializar a solicitação de visão

Em `ViewDidLoad`, criar um `VNDetectRectanglesRequest` que referencia o `HandleRectangles` método ser chamado no final de cada solicitação:

O `MaximumObservations` também deve ser definida uma propriedade, caso contrário, o padrão será 1 e apenas um único resultado será retornado.

```csharp
RectangleRequest = new VNDetectRectanglesRequest(HandleRectangles);
RectangleRequest.MaximumObservations = 10;
```

### <a name="2-start-the-vision-processing"></a>2. Iniciar o processamento de visão

O código a seguir inicia o processamento da solicitação. No **VisionRects** exemplo, esse código é executado depois que o usuário selecionou uma imagem:

```csharp
// Run the rectangle detector
var handler = new VNImageRequestHandler(ciImage, uiImage.Orientation.ToCGImagePropertyOrientation(), new VNImageOptions());
DispatchQueue.DefaultGlobalQueue.DispatchAsync(()=>{
  handler.Perform(new VNRequest[] {RectangleRequest}, out NSError error);
});
```

Este manipulador passa o `ciImage` para a estrutura de visão `VNDetectRectanglesRequest` que foi criado na etapa 1.

### <a name="3-handle-the-results-of-vision-processing"></a>3. Lidar com os resultados do processamento de visão

Quando a detecção de retângulo for concluída, a estrutura executará o `HandleRectangles` método, um resumo do que é mostrado abaixo:

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

O `OverlayRectangles` método o **VisionRectangles** exemplo tem três funções:

- Renderização de imagem de origem
- Desenhar um retângulo para indicar onde cada uma delas foi detectada, e
- Adicionar um rótulo de texto para cada retângulo usando CoreGraphics.

Exibição de [fonte do exemplo](https://developer.xamarin.com/samples/monotouch/ios11/VisionRectangles/) para o método CoreGraphics exato.

![Fotografar com três retângulos detectados](vision-images/found-rectangles-phone-sml.png)

### <a name="5-further-processing"></a>5. Processamento adicional

Detecção de retângulo geralmente é a primeira etapa em uma cadeia de operações, como com [Este exemplo CoreMLVision](~/ios/platform/introduction-to-ios11/coreml.md#coremlvision), onde os retângulos são passados para um modelo de CoreML analisar manuscritos dígitos.


<a name="faces" />

## <a name="face-detection"></a>Detecção de rosto

O [VisionFaces exemplo](https://developer.xamarin.com/samples/monotouch/ios11/VisionFaces/) funciona de maneira semelhante para o **VisionRectangles** de exemplo, usando uma classe diferente de solicitação de visão.

### <a name="1-initialize-the-vision-request"></a>1. Inicializar a solicitação de visão

Em `ViewDidLoad`, criar um `VNDetectFaceRectanglesRequest` que referencia o `HandleRectangles` método ser chamado no final de cada solicitação.

```csharp
FaceRectangleRequest = new VNDetectFaceRectanglesRequest(HandleRectangles);
```

### <a name="2-start-the-vision-processing"></a>2. Iniciar o processamento de visão

O código a seguir inicia o processamento da solicitação. No **VisionFaces** esse código é executado depois que o usuário selecionou uma imagem de exemplo:

```csharp
// Run the face detector
var handler = new VNImageRequestHandler(ciImage, uiImage.Orientation.ToCGImagePropertyOrientation(), new VNImageOptions());
DispatchQueue.DefaultGlobalQueue.DispatchAsync(()=>{
  handler.Perform(new VNRequest[] {FaceRectangleRequest}, out NSError error);
});
```

Este manipulador passa o `ciImage` para a estrutura de visão `VNDetectFaceRectanglesRequest` que foi criado na etapa 1.

### <a name="3-handle-the-results-of-vision-processing"></a>3. Lidar com os resultados do processamento de visão

Quando a detecção de rosto for concluída, o manipulador executa o `HandleRectangles` método que executa o tratamento de erros e exibe os limites de faces detectadas e chama o `OverlayRectangles` para desenhar retângulos delimitadoras na imagem original:

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

O `OverlayRectangles` método o **VisionFaces** exemplo tem três funções:

- Renderização de imagem de origem
- Desenhar um retângulo para cada face detectado, e
- Adicionar um rótulo de texto para cada face usando CoreGraphics.

Exibição de [fonte do exemplo](https://developer.xamarin.com/samples/monotouch/ios11/VisionFaces/) para o método CoreGraphics exato.

![Fotografar com duas faces detectadas](vision-images/found-faces-phone-sml.png)

### <a name="5-further-processing"></a>5. Processamento adicional

A estrutura de visão inclui recursos adicionais para detectar recursos faciais, como o olhos e boca. Use o `VNDetectFaceLandmarksRequest` tipo, que retornará `VNFaceObservation` resultados da etapa 3 acima, mas com adicionais `VNFaceLandmark` dados.


## <a name="related-links"></a>Links relacionados

- [Visão retângulos (exemplo)](https://developer.xamarin.com/samples/monotouch/ios11/VisionRectangles/)
- [Visão Faces (exemplo)](https://developer.xamarin.com/samples/monotouch/ios11/VisionFaces/)
- [Avanços na imagem principal - filtros, sistema operacional, a visão e mais (WWDC) (vídeo)](https://developer.apple.com/videos/play/wwdc2017/510/)
