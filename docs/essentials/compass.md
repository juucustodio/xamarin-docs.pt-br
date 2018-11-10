---
title: 'Xamarin.Essentials: Bússola'
description: Este documento descreve a classe Compass no Xamarin.Essentials, que permite que você monitore o norte magnético do dispositivo.
ms.assetid: BF85B0C3-C686-43D9-811A-07DCAF8CDD86
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 51812f9b4f88d77bf553a26ef3a6802239e338e0
ms.sourcegitcommit: 729035af392dc60edb9d99d3dc13d1ef69d5e46c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/31/2018
ms.locfileid: "50675491"
---
# <a name="xamarinessentials-compass"></a>Xamarin.Essentials: Bússola

![Pré-lançamento NuGet](~/media/shared/pre-release.png)

A classe **Compass** permite que você monitore o norte magnético do dispositivo.

## <a name="get-started"></a>Introdução

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-compass"></a>Como usar Compass

Adicione uma referência ao Xamarin.Essentials na classe:

```csharp
using Xamarin.Essentials;
```

A funcionalidade de Bússola funciona chamando os métodos `Start` e `Stop` e fica atenta às alterações na bússola. Todas as alterações são enviadas de volta por meio do evento `ReadingChanged`. Veja um exemplo:

```csharp
public class CompassTest
{
    // Set speed delay for monitoring changes.
    SensorSpeed speed = SensorSpeed.UI;

    public CompassTest()
    {
        // Register for reading changes, be sure to unsubscribe when finished
        Compass.ReadingChanged += Compass_ReadingChanged;
    }

    void Compass_ReadingChanged(object sender, CompassChangedEventArgs e)
    {
        var data = e.Reading;
        Console.WriteLine($"Reading: {data.HeadingMagneticNorth} degrees");
        // Process Heading Magnetic North
    }

    public void ToggleCompass()
    {
        try
        {
            if (Compass.IsMonitoring)
              Compass.Stop();
            else
              Compass.Start(speed);
        }
        catch (FeatureNotSupportedException fnsEx)
        {
            // Feature not supported on device
        }
        catch (Exception ex)
        {
            // Some other exception has occurred
        }
    }
}
```

[!include[](~/essentials/includes/sensor-speed.md)]

## <a name="platform-implementation-specifics"></a>Particularidades de implementação da plataforma

# <a name="androidtabandroid"></a>[Android](#tab/android)

O Android não fornece uma API para recuperar a orientação da bússola. Utilizamos o acelerômetro e o magnetômetro para calcular o norte magnético, o que é recomendado pelo Google.

Em casos raros, talvez você veja resultados inconsistentes devido à necessidade de calibração dos sensores, o que envolve a movimentação do seu dispositivo em um movimento de figura 8. A melhor maneira de fazer isso é abrir o Google Maps, tocar no ponto do seu local e selecionar **Calibrar bússola**.

Saiba que executar simultaneamente vários sensores em seu aplicativo pode ajustar a velocidade do sensor.

## <a name="low-pass-filter"></a>Filtro Passa Baixa

Devido ao modo como os valores da bússola do Android são atualizados e calculados, pode haver a necessidade para suavizar os valores. É possível aplicar um _Filtro de Passa Baixa_ que deixa na média os valores de seno e cosseno dos ângulos por meio da definição da propriedade `ApplyLowPassFilter` na classe `Compass`:

```csharp
Compass.ApplyLowPassFilter = true;
```

Isso só se aplica à plataforma Android. Encontre mais informações [aqui](https://github.com/xamarin/Essentials/pull/354#issuecomment-405316860).

--------------

## <a name="api"></a>API

- [Código-fonte da Bússola](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Compass)
- [Documentação da API de Bússola](xref:Xamarin.Essentials.Compass)
