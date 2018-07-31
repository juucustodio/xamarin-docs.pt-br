---
title: Introdução ao CoreML no xamarin. IOS
description: Este documento descreve CoreML, que permite que o aprendizado de máquina no iOS. Este documento discute como começar com CoreML e como usá-lo com o framework de visão.
ms.prod: xamarin
ms.assetid: BE1E2CA1-E3AE-4C90-914C-CFDBD1DCB82B
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 08/30/2017
ms.openlocfilehash: 13178d4530e3214c6cf31c1018b21815ccd2227f
ms.sourcegitcommit: aa9b9b203ab4cd6a6b4fd51e27d865e2abf582c1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2018
ms.locfileid: "39350673"
---
# <a name="introduction-to-coreml-in-xamarinios"></a>Introdução ao CoreML no xamarin. IOS

CoreML traz o machine learning para iOS – aplicativos podem tirar proveito dos modelos de aprendizado de máquina treinado para realizar todos os tipos de tarefas, de solução de problemas para reconhecimento de imagem.

Esta introdução aborda o seguinte:

- [Introdução ao CoreML](#coreml)
- [Usando CoreML com o framework de visão](#coremlvision)

<a name="coreml" />

## <a name="getting-started-with-coreml"></a>Introdução ao CoreML

Estas etapas descrevem como adicionar CoreML a um projeto do iOS. Consulte a [exemplo de Mars Habitat Pricer](https://developer.xamarin.com/samples/monotouch/ios11/CoreML/) para obter um exemplo prático.

![Captura de tela de exemplo de previsão de preço do MARS Habitat](coreml-images/marspricer-heading.png)

### <a name="1-add-the-coreml-model-to-the-project"></a>1. Adicione o modelo CoreML ao projeto

Adicionar um modelo de CoreML (um arquivo com o **.mlmodel** extensão) para o **recursos** diretório do projeto. 

Nas propriedades do arquivo de modelo, suas **ação de Build** é definido como **CoreMLModel**. Isso significa que ele será compilado em um **.mlmodelc** arquivo quando o aplicativo é compilado.

### <a name="2-load-the-model"></a>2. Carregar o modelo

Carregar o modelo usando o `MLModel.Create` método estático:

```csharp
var assetPath = NSBundle.MainBundle.GetUrlForResource("NameOfModel", "mlmodelc");
model = MLModel.Create(assetPath, out NSError error1);
```

### <a name="3-set-the-parameters"></a>3. Defina os parâmetros

Parâmetros de modelo são passados de entrada e saída usando uma classe de contêiner que implementa `IMLFeatureProvider`.

Classes de provedor de recursos se comportam como um dicionário de cadeia de caracteres e `MLFeatureValue`s, onde cada valor de recurso pode ser uma simple cadeia de caracteres ou número, uma matriz ou dados ou um buffer de pixel que contém uma imagem.

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

Usando classes como esta, parâmetros de entrada podem ser fornecidos de forma que é compreendida pelo CoreML. Os nomes dos recursos (como `myParam` no código de exemplo) deve coincidir com o modelo espera.

### <a name="4-run-the-model"></a>4. Executar o modelo

Usando o modelo requer que o provedor de recursos a ser instanciado e os parâmetros definido, em seguida, que o `GetPrediction` método ser chamado:

```csharp
var input = new MyInput {MyParam = 13};
var outFeatures = model.GetPrediction(inputFeatures, out NSError error2);
```

### <a name="5-extract-the-results"></a>5. Extrair os resultados

O resultado da previsão `outFeatures` também é uma instância do `IMLFeatureProvider`; saída valores podem ser acessados usando `GetFeatureValue` com o nome de cada parâmetro de saída (como `theResult`), como neste exemplo:

```csharp
var result = outFeatures.GetFeatureValue("theResult").DoubleValue; // eg. 6227020800
```

<a name="coremlvision" />

## <a name="using-coreml-with-the-vision-framework"></a>Usando CoreML com o Framework de visão

CoreML também pode ser usado em conjunto com a estrutura de visão para executar operações em imagem, como reconhecimento de formas, identificação de objeto e outras tarefas.

As etapas a seguir descrevem como CoreML e visão são usados juntos na [CoreMLVision exemplo](https://developer.xamarin.com/samples/monotouch/ios11/CoreMLVision/). O exemplo combina o [reconhecimento de retângulos](~/ios/platform/introduction-to-ios11/vision.md#rectangles) da estrutura da pesquisa Visual computacional com o _MNINSTClassifier_ CoreML modelo para identificar um dígito manuscrito em uma fotografia.

![Reconhecimento de imagem de número 3](coreml-images/vision3.png) ![Reconhecimento de imagem de número 5](coreml-images/vision5.png)

### <a name="1-create-a-vision-coreml-model"></a>1. Criar um modelo de visão CoreML

O modelo de CoreML _MNISTClassifier_ é carregado e, em seguida, encapsuladas em um `VNCoreMLModel` que disponibiliza o modelo para tarefas de visão. Esse código também cria duas solicitações de visão: primeiro para localizar retângulos em uma imagem e, em seguida, para o processamento de um retângulo com o modelo de CoreML:

```csharp
// Load the ML model
var bundle = NSBundle.MainBundle;
var assetPath = bundle.GetUrlForResource("MNISTClassifier", "mlmodelc");
NSError mlErr, vnErr;
var mlModel = MLModel.Create(assetPath, out mlErr);
var model = VNCoreMLModel.FromMLModel(mlModel, out vnErr);

// Initialize Vision requests
RectangleRequest = new VNDetectRectanglesRequest(HandleRectangles);
ClassificationRequest = new VNCoreMLRequest(model, HandleClassification);
```

A classe precisa implementar o `HandleRectangles` e `HandleClassification` métodos para as solicitações de visão, mostrados nas etapas 3 e 4 abaixo.

### <a name="2-start-the-vision-processing"></a>2. Iniciar o processamento da pesquisa Visual computacional

O código a seguir inicia o processamento da solicitação. No **CoreMLVision** amostra, esse código é executado depois que o usuário selecionou uma imagem:

```csharp
// Run the rectangle detector, which upon completion runs the ML classifier.
var handler = new VNImageRequestHandler(ciImage, uiImage.Orientation.ToCGImagePropertyOrientation(), new VNImageOptions());
DispatchQueue.DefaultGlobalQueue.DispatchAsync(()=>{
  handler.Perform(new VNRequest[] {RectangleRequest}, out NSError error);
});
```

Esse manipulador passa o `ciImage` para a estrutura de visão `VNDetectRectanglesRequest` que foi criado na etapa 1.

### <a name="3-handle-the-results-of-vision-processing"></a>3. Lidar com os resultados do processamento da pesquisa Visual computacional

Quando a detecção de retângulo for concluída, ele executa o `HandleRectangles` método, que corta a imagem para extrair o primeiro retângulo, converte a imagem de retângulo em escala de cinza e o passa para o modelo de CoreML para classificação.

O `request` parâmetro passado para este método contém os detalhes da solicitação da pesquisa Visual computacional e usando o `GetResults<VNRectangleObservation>()` método, ele retorna uma lista dos retângulos encontrados na imagem. O primeiro retângulo `observations[0]` é extraído e passado para o modelo de CoreML:

```csharp
void HandleRectangles(VNRequest request, NSError error) {
  var observations = request.GetResults<VNRectangleObservation>();
  // ... omitted error handling ...
  var detectedRectangle = observations[0]; // first rectangle
  // ... omitted cropping and greyscale conversion ...
  // Run the Core ML MNIST classifier -- results in handleClassification method
  var handler = new VNImageRequestHandler(correctedImage, new VNImageOptions());
  DispatchQueue.DefaultGlobalQueue.DispatchAsync(() => {
    handler.Perform(new VNRequest[] {ClassificationRequest}, out NSError err);
  });
}
```

O `ClassificationRequest` foi inicializado na etapa 1 para usar o `HandleClassification` método definido na próxima etapa.

### <a name="4-handle-the-coreml"></a>4. Identificador do CoreML

O `request` parâmetro passado para este método contém os detalhes da solicitação CoreML e usando o `GetResults<VNClassificationObservation>()` método, ele retorna uma lista de possíveis resultados ordenados por confiança (confiança mais alta primeiro):

```csharp
void HandleClassification(VNRequest request, NSError error){
  var observations = request.GetResults<VNClassificationObservation>();
  // ... omitted error handling ...
  var best = observations[0]; // first/best classification result
  // render in UI
  DispatchQueue.MainQueue.DispatchAsync(()=>{
    ClassificationLabel.Text = $"Classification: {best.Identifier} Confidence: {best.Confidence * 100f:#.00}%";
  });
}
```

## <a name="samples"></a>Exemplos

Há três amostras de CoreML para experimentar:

* O [exemplo de previsão de preço do Mars Habitat](https://developer.xamarin.com/samples/monotouch/ios11/CoreML/) tem simples entradas e saídas numéricas.

* O [exemplo de visão e CoreML](https://developer.xamarin.com/samples/monotouch/ios11/CoreMLVision/) aceita um parâmetro de imagem e usa a estrutura de visão para identificar regiões quadrados na imagem, que são passados para um modelo de CoreML que reconhece dígitos únicos.

* Por fim, o [exemplo de reconhecimento de imagem CoreML](https://developer.xamarin.com/samples/monotouch/ios11/CoreMLImageRecognition/) usa CoreML para identificar recursos em uma foto. Por padrão, ele usa o menor **SqueezeNet** modelo (5MB), mas ele está gravado para que você pode baixar e incorporar o maior **VGG16** modelo (553 MB). Para obter mais informações, consulte o [Leiame da amostra](https://github.com/xamarin/ios-samples/blob/master/ios11/CoreMLImageRecognition/CoreMLImageRecognition/README.md).

## <a name="related-links"></a>Links relacionados

- [Machine Learning (Apple)](https://developer.apple.com/machine-learning/)
- [Exemplo de CoreML (Mars Habitat) (amostra)](https://developer.xamarin.com/samples/monotouch/ios11/CoreML/)
- [CoreML e visão (reconhecimento de número) (amostra)](https://developer.xamarin.com/samples/monotouch/ios11/CoreMLVision/)
- [Reconhecimento de imagem CoreML (amostra)](https://developer.xamarin.com/samples/monotouch/ios11/CoreMLImageRecognition/)
- [CoreML com deficiência visual personalizado do Azure (exemplo)](https://developer.xamarin.com/samples/monotouch/ios11/CoreMLAzureModel)
- [Introdução ao CoreML (WWDC) (vídeo)](https://developer.apple.com/videos/play/wwdc2017/703/)
