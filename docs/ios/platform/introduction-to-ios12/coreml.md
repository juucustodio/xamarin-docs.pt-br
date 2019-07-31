---
title: Principal ML 2 no Xamarin. iOS
description: Este documento descreve as atualizações do core ML disponíveis como parte do iOS 12. Em particular, ele analisa as melhorias de desempenho associadas à nova API de previsão do lote.
ms.prod: xamarin
ms.assetid: 408E752C-2C78-4B20-8B43-A6B89B7E6D1B
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 08/15/2018
ms.openlocfilehash: 4fc72e855101f110310a46145c577b272a647ac3
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68645711"
---
# <a name="core-ml-2-in-xamarinios"></a>Principal ML 2 no Xamarin. iOS

O ML principal é uma tecnologia de aprendizado de máquina disponível em iOS, macOS, tvOS e watchOS. Ele permite que os aplicativos façam previsões baseadas em modelos de aprendizado de máquina.

No iOS 12, o ML principal inclui uma API de processamento em lotes. Essa API torna o ML principal mais eficiente e fornece melhorias de desempenho em cenários em que um modelo é usado para fazer uma sequência de previsões.

## <a name="sample-app-marshabitatcoremltimer"></a>Aplicativo de exemplo: MarsHabitatCoreMLTimer

Para demonstrar previsões de lote com o ML principal, dê uma olhada no aplicativo de exemplo [MarsHabitatCoreMLTimer](https://docs.microsoft.com/samples/xamarin/ios-samples/ios12-marshabitatcoremltimer) . Este exemplo usa um modelo de ML básico treinado para prever o custo da criação de um habitat no Mars, com base em várias entradas: número de painéis solares, número de estufas e número de acres.

Os trechos de código neste documento vêm deste exemplo.

## <a name="generate-sample-data"></a>Gerar dados de exemplo

No `ViewController`, as `ViewDidLoad` chamadas`LoadMLModel`de método do aplicativo de exemplo, que carregam o modelo de ml principal incluído:

```csharp
void LoadMLModel()
{
    var assetPath = NSBundle.MainBundle.GetUrlForResource("CoreMLModel/MarsHabitatPricer", "mlmodelc");
    model = MLModel.Create(assetPath, out NSError mlErr);
}
```

Em seguida, o aplicativo de exemplo `MarsHabitatPricerInput` cria 100.000 objetos a serem usados como entrada para previsões de ml de núcleos sequenciais. Cada amostra gerada tem um valor aleatório definido para o número de painéis solares, o número de estufas e o número de acres:

```csharp
async void CreateInputs(int num)
{
    // ...
    Random r = new Random();
    await Task.Run(() =>
    {
        for (int i = 0; i < num; i++)
        {
            double solarPanels = r.NextDouble() * MaxSolarPanels;
            double greenHouses = r.NextDouble() * MaxGreenHouses;
            double acres = r.NextDouble() * MaxAcres;
            inputs[i] = new MarsHabitatPricerInput(solarPanels, greenHouses, acres);
        }
    });
    // ...
}
```

Tocar em qualquer um dos três botões do aplicativo executa duas sequências de previsões: uma usando um `for` loop e outra usando o novo método de `GetPredictions` lote introduzido no IOS 12:

```csharp
async void RunTest(int num)
{
    // ...
    await FetchNonBatchResults(num);
    // ...
    await FetchBatchResults(num);
    // ...
}
```

## <a name="for-loop"></a>para loop

A `for` versão de loop do teste naively itera sobre o número especificado de entradas, chamando [`GetPrediction`](xref:CoreML.MLModel.GetPrediction*) para cada e descartando o resultado. O método vezes o tempo necessário para fazer as previsões:

```csharp
async Task FetchNonBatchResults(int num)
{
    Stopwatch stopWatch = Stopwatch.StartNew();
    await Task.Run(() =>
    {
        for (int i = 0; i < num; i++)
        {
            IMLFeatureProvider output = model.GetPrediction(inputs[i], out NSError error);
        }
    });
    stopWatch.Stop();
    nonBatchMilliseconds = stopWatch.ElapsedMilliseconds;
}
```

## <a name="getpredictions-new-batch-api"></a>Getprevêtions (nova API do lote)

A versão do lote do teste cria um `MLArrayBatchProvider` objeto a partir da matriz de entrada (já que esse é um parâmetro de `GetPredictions` entrada necessário para o método), cria um[`MLPredictionOptions`](xref:CoreML.MLPredictionOptions)
objeto que impede que os cálculos de previsão sejam restritos à CPU e use a `GetPredictions` API para buscar as previsões, descartando novamente o resultado:

```csharp
async Task FetchBatchResults(int num)
{
    var batch = new MLArrayBatchProvider(inputs.Take(num).ToArray());
    var options = new MLPredictionOptions()
    {
        UsesCpuOnly = false
    };

    Stopwatch stopWatch = Stopwatch.StartNew();
    await Task.Run(() =>
    {
        model.GetPredictions(batch, options, out NSError error);
    });
    stopWatch.Stop();
    batchMilliseconds = stopWatch.ElapsedMilliseconds;
}
```

## <a name="results"></a>Resultados

No simulador e no dispositivo `GetPredictions` , o é concluído mais rapidamente do que as previsões de ml principais baseadas em loop.

## <a name="related-links"></a>Links relacionados

- [Aplicativo de exemplo – MarsHabitatCoreMLTimer](https://docs.microsoft.com/samples/xamarin/ios-samples/ios12-marshabitatcoremltimer)
- [O que há de novo no ML principal, parte 1 (WWDC 2018)](https://developer.apple.com/videos/play/wwdc2018/708/)
- [O que há de novo no ML principal, parte 2 (WWDC 2018)](https://developer.apple.com/videos/play/wwdc2018/709/)
- [Introdução ao ML principal no Xamarin. iOS](https://docs.microsoft.com/xamarin/ios/platform/introduction-to-ios11/coreml)
- [ML principal (Apple)](https://developer.apple.com/documentation/coreml?language=objc)
- [Trabalhando com modelos de ML principais](https://developer.apple.com/machine-learning/build-run-models/)
