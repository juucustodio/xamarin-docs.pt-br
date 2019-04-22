---
title: Core ML 2 no xamarin. IOS
description: Este documento descreve as atualizações para Core ML disponível como parte do iOS 12. Em particular, ele examina as melhorias de desempenho associadas com a nova API de previsão em lotes.
ms.prod: xamarin
ms.assetid: 408E752C-2C78-4B20-8B43-A6B89B7E6D1B
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 08/15/2018
ms.openlocfilehash: 50d59f0b6ff2133c5870d84a1d740547768116e0
ms.sourcegitcommit: 3489c281c9eb5ada2cddf32d73370943342a1082
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/18/2019
ms.locfileid: "58869722"
---
# <a name="core-ml-2-in-xamarinios"></a>Core ML 2 no xamarin. IOS

Core ML é uma tecnologia disponível no iOS, macOS, tvOS e watchOS de aprendizado de máquina. Ele permite que os aplicativos fazer previsões com base em modelos de aprendizado de máquina.

No iOS 12, Core ML inclui uma API de processamento em lotes. Essa API ML Core o torna mais eficiente e oferece melhorias de desempenho em cenários onde um modelo é usado para criar uma sequência de previsões.

## <a name="sample-app-marshabitatcoremltimer"></a>Aplicativo de exemplo: MarsHabitatCoreMLTimer

Para demonstrar as previsões em lotes com Core ML, examine os [MarsHabitatCoreMLTimer](https://developer.xamarin.com/samples/monotouch/iOS12/MarsHabitatCoreMLTimer) aplicativo de exemplo. Este exemplo usa um modelo de Core ML treinado para prever o custo da criação de um habitat MARS, com base em várias entradas: número de painéis solares, o número de greenhouses e o número de m2.

Os trechos de código neste documento são provenientes neste exemplo.

## <a name="generate-sample-data"></a>Gerar dados de exemplo

Na `ViewController`, o aplicativo de exemplo `ViewDidLoad` chamadas de método `LoadMLModel`, que carrega o modelo de ML Core incluído:

```csharp
void LoadMLModel()
{
    var assetPath = NSBundle.MainBundle.GetUrlForResource("CoreMLModel/MarsHabitatPricer", "mlmodelc");
    model = MLModel.Create(assetPath, out NSError mlErr);
}
```

Em seguida, o aplicativo de exemplo cria 100.000 `MarsHabitatPricerInput` objetos a serem usados como entrada para previsões de Core ML sequenciais. Cada amostra gerada tem um valor aleatório definido para o número de painéis solares, o número de greenhouses e o número de M2:

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

Tocando em qualquer um dos três botões do aplicativo executa duas sequências de previsões: usando um `for` loop e outra usando o novo lote `GetPredictions` método introduzido no iOS 12:

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

O `for` versão de loop do teste ingenuamente itera sobre o número especificado de entradas, chamando [ `GetPrediction` ](xref:CoreML.MLModel.GetPrediction*) para cada e descartando o resultado. O método atinge o tempo quanto tempo leva para fazer as previsões:

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

## <a name="getpredictions-new-batch-api"></a>GetPredictions (API de lote novo)

Cria a versão do lote do teste de um `MLArrayBatchProvider` objeto da matriz de entrada (já que este é um parâmetro de entrada necessário para o `GetPredictions` método), cria um [`MLPredictionOptions`](xref:CoreML.MLPredictionOptions)
objeto que impede que os cálculos de previsão sejam restritas à CPU e usa o `GetPredictions` API para buscar as previsões, descartando novamente o resultado:

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

No simulador e no dispositivo, `GetPredictions` termina mais rapidamente do que as previsões de Core ML baseado em loop.

## <a name="related-links"></a>Links relacionados

- [Aplicativo de exemplo – MarsHabitatCoreMLTimer](https://developer.xamarin.com/samples/monotouch/iOS12/MarsHabitatCoreMLTimer)
- [O que há de novo no Core ML, parte 1 (WWDC 2018)](https://developer.apple.com/videos/play/wwdc2018/708/)
- [O que há de novo no Core ML, parte 2 (WWDC 2018)](https://developer.apple.com/videos/play/wwdc2018/709/)
- [Introdução ao Core ML no xamarin. IOS](https://docs.microsoft.com/xamarin/ios/platform/introduction-to-ios11/coreml)
- [Core ML (Apple)](https://developer.apple.com/documentation/coreml?language=objc)
- [Trabalhando com modelos do Core ML](https://developer.apple.com/machine-learning/build-run-models/)
