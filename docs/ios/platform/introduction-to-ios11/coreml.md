---
title: Introdução ao do coreml no Xamarin. iOS
description: Este documento descreve o do coreml, que habilita o aprendizado de máquina no iOS. Este documento discute como começar a usar o do coreml e como usá-lo com o Vision Framework.
ms.prod: xamarin
ms.assetid: BE1E2CA1-E3AE-4C90-914C-CFDBD1DCB82B
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 08/30/2017
ms.openlocfilehash: 96ea328901beede663c9ed3d8d42979544b041ea
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70292642"
---
# <a name="introduction-to-coreml-in-xamarinios"></a>Introdução ao do coreml no Xamarin. iOS

O do coreml traz o Machine Learning para o iOS – os aplicativos podem aproveitar os modelos de aprendizado de máquina treinados para executar todos os tipos de tarefas, desde a solução de problemas até o reconhecimento de imagens.

Esta introdução aborda o seguinte:

- [Introdução com do coreml](#coreml)
- [Usando o do coreml com o Vision Framework](#coremlvision)

<a name="coreml" />

## <a name="getting-started-with-coreml"></a>Introdução com do coreml

Estas etapas descrevem como adicionar o do coreml a um projeto do iOS. Consulte o [exemplo de preço do Mars habitat](https://docs.microsoft.com/samples/xamarin/ios-samples/ios12-marshabitatcoremltimer/) para obter um exemplo prático.

![Captura de tela de exemplo de previsão de preço Mars habitat](coreml-images/marspricer-heading.png)

### <a name="1-add-the-coreml-model-to-the-project"></a>1. Adicionar o modelo do coreml ao projeto

Adicione um modelo do coreml (um arquivo com a extensão **. mlmodel** ) ao diretório de **recursos** do projeto. 

Nas propriedades do arquivo de modelo, sua **ação de Build** é definida como **CoreMLModel**. Isso significa que ele será compilado em um arquivo **. mlmodelc** quando o aplicativo for compilado.

### <a name="2-load-the-model"></a>2. Carregar o modelo

Carregue o modelo usando o `MLModel.Create` método estático:

```csharp
var assetPath = NSBundle.MainBundle.GetUrlForResource("NameOfModel", "mlmodelc");
model = MLModel.Create(assetPath, out NSError error1);
```

### <a name="3-set-the-parameters"></a>3. Definir os parâmetros

Os parâmetros de modelo são passados e desativados usando uma classe `IMLFeatureProvider`de contêiner que implementa.

As classes de provedor de recursos se comportam `MLFeatureValue`como um dicionário de cadeia de caracteres e s, em que cada valor de recurso pode ser uma cadeia de caracteres simples ou um número, uma matriz ou dados ou um buffer de pixel contendo uma imagem.

O código para um provedor de recursos de valor único é mostrado abaixo:

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

Usando classes como essa, os parâmetros de entrada podem ser fornecidos de uma maneira que é compreendida pelo do coreml. Os nomes dos recursos ( `myParam` como no exemplo de código) devem corresponder ao que o modelo espera.

### <a name="4-run-the-model"></a>4. Executar o modelo

Usar o modelo requer que o provedor de recursos seja instanciado e parâmetros definidos, então o `GetPrediction` método será chamado:

```csharp
var input = new MyInput {MyParam = 13};
var outFeatures = model.GetPrediction(inputFeatures, out NSError error2);
```

### <a name="5-extract-the-results"></a>5. Extrair os resultados

O resultado `outFeatures` da previsão também é uma instância de `IMLFeatureProvider`; os valores de saída podem ser `GetFeatureValue` acessados usando com o `theResult`nome de cada parâmetro de saída (como), como neste exemplo:

```csharp
var result = outFeatures.GetFeatureValue("theResult").DoubleValue; // eg. 6227020800
```

<a name="coremlvision" />

## <a name="using-coreml-with-the-vision-framework"></a>Usando o do coreml com o Vision Framework

O do coreml também pode ser usado em conjunto com o Vision Framework para executar operações na imagem, como reconhecimento de forma, identificação de objeto e outras tarefas.

As etapas a seguir descrevem como a do coreml e a visão são usadas juntas no [exemplo de CoreMLVision](https://docs.microsoft.com/samples/xamarin/ios-samples/ios11-coremlvision). O exemplo combina o [reconhecimento de retângulos](~/ios/platform/introduction-to-ios11/vision.md#rectangles) da estrutura de visão com o modelo _MNINSTClassifier_ do coreml para identificar um dígito manuscrito em uma fotografia.

![Reconhecimento de imagem do número 3](coreml-images/vision3.png) ![Reconhecimento de imagem do número 5](coreml-images/vision5.png)

### <a name="1-create-a-vision-coreml-model"></a>1. Criar um modelo de do coreml de visão

O modelo do coreml _MNISTClassifier_ é carregado e, em seguida, `VNCoreMLModel` encapsulado em um que torna o modelo disponível para tarefas de visão. Esse código também cria duas solicitações de visão: primeiro para localizar retângulos em uma imagem e, em seguida, para processar um retângulo com o modelo do coreml:

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

A classe ainda precisa implementar os `HandleRectangles` métodos e `HandleClassification` para as solicitações de visão, mostradas nas etapas 3 e 4 abaixo.

### <a name="2-start-the-vision-processing"></a>2. Iniciar o processamento da visão

O código a seguir inicia o processamento da solicitação. No exemplo de **CoreMLVision** , esse código é executado depois que o usuário seleciona uma imagem:

```csharp
// Run the rectangle detector, which upon completion runs the ML classifier.
var handler = new VNImageRequestHandler(ciImage, uiImage.Orientation.ToCGImagePropertyOrientation(), new VNImageOptions());
DispatchQueue.DefaultGlobalQueue.DispatchAsync(()=>{
  handler.Perform(new VNRequest[] {RectangleRequest}, out NSError error);
});
```

Esse manipulador passa o `ciImage` para a estrutura `VNDetectRectanglesRequest` de visão que foi criada na etapa 1.

### <a name="3-handle-the-results-of-vision-processing"></a>3. Manipular os resultados do processamento da visão

Depois que a detecção de retângulo for concluída, ela executará o `HandleRectangles` método, que cortará a imagem para extrair o primeiro retângulo, converterá a imagem do retângulo em escala de cinza e a passará para o modelo do coreml para classificação.

O `request` parâmetro passado para esse método contém os detalhes da solicitação de visão e, usando o `GetResults<VNRectangleObservation>()` método, ele retorna uma lista de retângulos encontrados na imagem. O primeiro retângulo `observations[0]` é extraído e passado para o modelo do coreml:

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

O `ClassificationRequest` foi inicializado na etapa 1 para usar `HandleClassification` o método definido na próxima etapa.

### <a name="4-handle-the-coreml"></a>4. Manipular o do coreml

O `request` parâmetro passado para esse método contém os detalhes da solicitação do coreml e usando o `GetResults<VNClassificationObservation>()` método, ele retorna uma lista de possíveis resultados ordenados por confiança (mais alta confiança primeiro):

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

Há três exemplos de do coreml a serem experimentados:

- O [exemplo de previsão de preço do Mars habitat](https://docs.microsoft.com/samples/xamarin/ios-samples/ios12-marshabitatcoremltimer/) tem entradas e saídas numéricas simples.

- O [exemplo de visão & do coreml](https://docs.microsoft.com/samples/xamarin/ios-samples/ios11-coremlvision) aceita um parâmetro de imagem e usa o Vision Framework para identificar regiões quadradas na imagem, que são passadas para um modelo do coreml que reconhece dígitos únicos.

- Por fim, o [exemplo de reconhecimento de imagem do coreml](https://docs.microsoft.com/samples/xamarin/ios-samples/ios11-coremlimagerecognition) usa o do coreml para identificar recursos em uma foto. Por padrão, ele usa o modelo de **SqueezeNet** menor (5 MB), mas foi escrito para que você possa baixar e incorporar o 553MB (modelo de **VGG16** maior). Para obter mais informações, consulte o [Leiame do exemplo](https://github.com/xamarin/ios-samples/blob/master/ios11/CoreMLImageRecognition/CoreMLImageRecognition/README.md).

## <a name="related-links"></a>Links relacionados

- [Machine Learning (Apple)](https://developer.apple.com/machine-learning/)
- [Exemplo de do coreml (Mars Habitat) (exemplo)](https://docs.microsoft.com/samples/xamarin/ios-samples/ios12-marshabitatcoremltimer/)
- [Do coreml e visão (reconhecimento de número) (exemplo)](https://docs.microsoft.com/samples/xamarin/ios-samples/ios11-coremlvision)
- [Reconhecimento de imagem do coreml (exemplo)](https://docs.microsoft.com/samples/xamarin/ios-samples/ios11-coremlimagerecognition)
- [Do coreml com o Azure Visão Personalizada (exemplo)](https://docs.microsoft.com/samples/xamarin/ios-samples/ios11-coremlazuremodel)
- [Introdução ao do coreml (WWDC) (vídeo)](https://developer.apple.com/videos/play/wwdc2017/703/)
