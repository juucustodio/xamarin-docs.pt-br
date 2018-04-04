---
title: Introdução ao CoreML
description: Máquina de aprendizado para aplicativos móveis no iOS 11
ms.prod: xamarin
ms.assetid: BE1E2CA1-E3AE-4C90-914C-CFDBD1DCB82B
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 08/30/2016
ms.openlocfilehash: 412a534829349dbbc3f3b76b166882fa6e0e1cd1
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="introduction-to-coreml"></a>Introdução ao CoreML

_Máquina de aprendizado para aplicativos móveis no iOS 11_

CoreML coloca o aprendizado de máquina para iOS – aplicativos podem tirar proveito dos modelos de aprendizado de máquina treinado para executar todos os tipos de tarefas de solução de problemas de reconhecimento de imagem.

Esta introdução aborda os seguintes tópicos:

- [Introdução ao CoreML](#coreml)
- [Usando CoreML com o framework de visão](#coremlvision)

<a name="coreml" />

## <a name="getting-started-with-coreml"></a>Introdução ao CoreML

Estas etapas descrevem como adicionar CoreML a um projeto do iOS. Consulte o [exemplo Mars programa moradia Pricer](https://developer.xamarin.com/samples/monotouch/ios11/CoreML/) para obter um exemplo prático.

![Captura de tela de exemplo de indicador de preço do programa moradia MARS](coreml-images/marspricer-heading.png)

### <a name="1-add-the-model-to-the-project"></a>1. Adicionar o modelo ao projeto

Adicionar um modelo compilado (um diretório com o **.modelc** extensão) para o **recursos** diretório do projeto. O conteúdo do diretório deve ter uma ação de compilação de **BundleResource**:

![A pasta de recursos deve conter o modelo compilado](coreml-images/resources-modelc.png)

O [exemplos](https://developer.xamarin.com/samples/monotouch/ios11/) usar modelos compilados no Xcode 9 ou manualmente usando o seguinte comando de terminal:

```csharp
xcrun coremlcompiler compile {model.mlmodel} {outputFolder}
```

> [!NOTE]
> **. Model** arquivos _deve_ compilada em **.modelc** antes que possam ser usados por CoreML

### <a name="2-load-the-model"></a>2. Carregar o modelo

Antes de usar um modelo, carregá-lo usando o `MLModel.FromUrl` método estático:

```csharp
var assetPath = NSBundle.MainBundle.GetUrlForResource("NameOfModel", "mlmodelc");
model = MLModel.FromUrl(assetPath, out NSError error1);
```

### <a name="3-set-the-parameters"></a>3. Defina os parâmetros

Parâmetros de modelo são passados de entrada e saída usando uma classe de contêiner que implementa `IMLFeatureProvider`.

Classes de provedor de recurso se comportam como um dicionário de cadeia de caracteres e `MLFeatureValue`s, onde cada valor de recurso pode ser uma cadeia de caracteres simple ou número, um buffer de pixels que contém uma imagem, uma matriz ou dados.

Código para um provedor de recursos de valor único é mostrado abaixo:

```csharp
public class MyInput : NSObject, IMLFeatureProvider
{
  public double MyParam { get; set; }
  public NSSet<NSString> FeatureNames => new NSSet<NSString>(new NSString("myParam"));
  public MLFeatureValue GetFeatureValue(string featureName)
  {
    if (featureName == "myParam")
      return MLFeatureValue.FromDouble(MyParam);
    return MLFeatureValue.FromDouble(0); // default value
  }
```

Usando classes assim, os parâmetros de entrada podem ser fornecidos de forma que seja entendida pelo CoreML. Os nomes dos recursos (como `myParam` no código de exemplo) deve coincidir com o modelo espera.

### <a name="4-run-the-model"></a>4. Executar o modelo

Usando o modelo requer que os parâmetros e o provedor de recursos a ser instanciado, depois que o `GetPrediction` método ser chamado:

```csharp
var input = new MyInput {MyParam = 13};
var outFeatures = model.GetPrediction(inputFeatures, out NSError error2);
```

### <a name="5-extract-the-results"></a>5. Extrair os resultados

O resultado da previsão `outFeatures` também é uma instância de `IMLFeatureProvider`; saída valores podem ser acessados usando `GetFeatureValue` com o nome de cada parâmetro de saída (como `theResult`), como no exemplo:

```csharp
var result = outFeatures.GetFeatureValue("theResult").DoubleValue; // eg. 6227020800
```

<a name="coremlvision" />

## <a name="using-coreml-with-the-vision-framework"></a>Usando CoreML com o Framework de visão

CoreML também pode ser usado em conjunto com a estrutura de visão para executar operações em imagem, como reconhecimento de forma, identificação de objeto e outras tarefas.

As etapas a seguir descrevem como CoreML e visão são usados juntos no [CoreMLVision exemplo](https://developer.xamarin.com/samples/monotouch/ios11/CoreMLVision/). O exemplo combina o [reconhecimento de retângulos](~/ios/platform/introduction-to-ios11/vision.md#rectangles) da estrutura de visuais com o _MNINSTClassifier_ CoreML modelo para identificar um dígito manuscrito em uma fotografia.

![Reconhecimento de imagem de número 3](coreml-images/vision3.png) ![Reconhecimento de imagem de número 5](coreml-images/vision5.png)

### <a name="1-create-a-vision-coreml-model"></a>1. Criar um modelo de visão CoreML

O modelo de CoreML _MNISTClassifier_ é carregado e, em seguida, encapsulado em um `VNCoreMLModel` que disponibiliza o modelo para tarefas de visão. Esse código também cria duas solicitações de visão: primeiro para localizar os retângulos em uma imagem e, em seguida, para o processamento de um retângulo com o modelo de CoreML:

```csharp
// Load the ML model
var assetPath = NSBundle.MainBundle.GetUrlForResource("MNISTClassifier", "mlmodelc");
var mlModel = MLModel.FromUrl(assetPath, out NSError mlErr);
var vModel = VNCoreMLModel.FromMLModel(mlModel, out NSError vnErr);

// Initialize Vision requests
RectangleRequest = new VNDetectRectanglesRequest(HandleRectangles);
ClassificationRequest = new VNCoreMLRequest(vModel, HandleClassification);
```

A classe deve implementar o `HandleRectangles` e `HandleClassification` métodos para as solicitações de visão, mostrados nas etapas 3 e 4 abaixo.

### <a name="2-start-the-vision-processing"></a>2. Iniciar o processamento de visão

O código a seguir inicia o processamento da solicitação. No **CoreMLVision** exemplo, esse código é executado depois que o usuário selecionou uma imagem:

```csharp
// Run the rectangle detector, which upon completion runs the ML classifier.
var handler = new VNImageRequestHandler(ciImage, uiImage.Orientation.ToCGImagePropertyOrientation(), new VNImageOptions());
DispatchQueue.DefaultGlobalQueue.DispatchAsync(()=>{
  handler.Perform(new VNRequest[] {RectangleRequest}, out NSError error);
});
```

Este manipulador passa o `ciImage` para a estrutura de visão `VNDetectRectanglesRequest` que foi criado na etapa 1.

### <a name="3-handle-the-results-of-vision-processing"></a>3. Lidar com os resultados do processamento de visão

Quando a detecção de retângulo for concluída, ele executa o `HandleRectangles` método, que corta a imagem para extrair o retângulo primeiro, converte a imagem de retângulo em escala de cinza e passa para o modelo de CoreML para classificação.

O `request` parâmetro passado para este método contém os detalhes da solicitação de visão e usando o `GetResults<VNRectangleObservation>()` método, retorna uma lista dos retângulos encontrado na imagem. O primeiro retângulo `observations[0]` é extraído e passados para o modelo de CoreML:

```csharp
void HandleRectangles(VNRequest request, NSError error) {
  var observations = request.GetResults<VNRectangleObservation>();
  // ... omitted error handling ...
  var detectedRectangle = observations[0]; // first rectangle
  // ... omitted cropping and greyscale conversion ...
  // Run the Core ML MNIST classifier -- results in handleClassification method
  var handler = new VNImageRequestHandler(correctedImage, new VNImageOptions());
  DispatchQueue.DefaultGlobalQueue.DispatchAsync(() => {
    handler.Perform(new VNRequest[] { ClassificationRequest }, out NSError err);
  });
}
```

O `ClassificationRequest` foi inicializada na etapa 1 para usar o `HandleClassification` método definido na próxima etapa.

### <a name="4-handle-the-coreml"></a>4. Identificador de CoreML

O `request` parâmetro passado para este método contém os detalhes da solicitação CoreML e usando o `GetResults<VNClassificationObservation>()` método, ele retorna uma lista de possíveis resultados ordenados por confiança (confiança mais alta primeiro):

```csharp
void HandleClassification(VNRequest request, NSError error){
  var observations = request.GetResults<VNClassificationObservation>();
  ... omitted error handling ...
  var best = observations[0]; // first/best classification result
  // render in UI
  DispatchQueue.MainQueue.DispatchAsync(()=>{
    ClassificationLabel.Text = $"Classification: {best.Identifier} Confidence: {best.Confidence * 100f:#.00}%";
  });
}
```



## <a name="samples"></a>Exemplos

Há três amostras CoreML tentar:

* O [exemplo do indicador de preço do programa moradia Mars](https://developer.xamarin.com/samples/monotouch/ios11/CoreML/) tem simples entradas e saídas numéricas.

* O [exemplo visão & CoreML](https://developer.xamarin.com/samples/monotouch/ios11/CoreMLVision/) aceita um parâmetro de imagem e usa a estrutura de visão para identificar quadrados regiões na imagem, que são passados para um modelo de CoreML que reconhece um único dígito.

* Por fim, o [exemplo de reconhecimento de imagem CoreML](https://developer.xamarin.com/samples/monotouch/ios11/CoreMLImageRecognition/) usa CoreML para identificar recursos em uma foto. Por padrão, ele usa o menor **SqueezeNet** modelo (5MB), mas foi gravado para que você possa baixar e incorporar o maior **VGG16** modelo (553 MB). Para obter mais informações, consulte o [Leiame do exemplo](https://github.com/xamarin/ios-samples/blob/master/ios11/CoreMLImageRecognition/CoreMLImageRecognition/README.md).


## <a name="related-links"></a>Links relacionados

- [Aprendizado de máquina (Apple)](https://developer.apple.com/machine-learning/)
- [Exemplo de CoreML (Mars programa moradia) (amostra)](https://developer.xamarin.com/samples/monotouch/ios11/CoreML/)
- [CoreML e visão (reconhecimento de número) (amostra)](https://developer.xamarin.com/samples/monotouch/ios11/CoreMLVision/)
- [Reconhecimento de imagem CoreML (exemplo)](https://developer.xamarin.com/samples/monotouch/ios11/CoreMLImageRecognition/)
- [CoreML com deficiência visual personalizado do Azure (exemplo)](https://developer.xamarin.com/samples/monotouch/ios11/CoreMLAzureModel)
- [Introdução ao CoreML (WWDC) (vídeo)](https://developer.apple.com/videos/play/wwdc2017/703/)
