---
title: 'Xamarin.Essentials: Giroscópio'
description: A classe Gyroscope no Xamarin.Essentials permite que você monitore o sensor de giroscópio do dispositivo, que mede a rotação em torno dos três eixos principais do dispositivo.
ms.assetid: DA4F968A-D988-41F5-8745-1BEE693660A1
author: jamesmontemagno
ms.author: jamont
ms.date: 11/04/2018
ms.openlocfilehash: 1e19585e238d66568364be7ccdbdb52d22b04066
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "61259468"
---
# <a name="xamarinessentials-gyroscope"></a>Xamarin.Essentials: Giroscópio

A classe **Gyroscope** permite que você monitore o sensor de giroscópio do dispositivo, que mede a rotação em torno dos três eixos principais do dispositivo.

## <a name="get-started"></a>Introdução

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-gyroscope"></a>Como usar o Giroscópio

Adicione uma referência ao Xamarin.Essentials na classe:

```csharp
using Xamarin.Essentials;
```

A funcionalidade de Giroscópio chama os métodos `Start` e `Stop` e fica atenta às alterações no giroscópio. Todas as alterações são enviadas de volta por meio do evento `ReadingChanged` em rad/s. Veja um exemplo de uso:

```csharp

public class GyroscopeTest
{
    // Set speed delay for monitoring changes.
    SensorSpeed speed = SensorSpeed.UI;

    public GyroscopeTest()
    {
        // Register for reading changes.
        Gyroscope.ReadingChanged += Gyroscope_ReadingChanged;
    }

    void Gyroscope_ReadingChanged(object sender, GyroscopeChangedEventArgs e)
    {
        var data = e.Reading;
        // Process Angular Velocity X, Y, and Z reported in rad/s
        Console.WriteLine($"Reading: X: {data.AngularVelocity.X}, Y: {data.AngularVelocity.Y}, Z: {data.AngularVelocity.Z}");
    }

    public void ToggleGyroscope()
    {
        try
        {
            if (Gyroscope.IsMonitoring)
              Gyroscope.Stop();
            else
              Gyroscope.Start(speed);
        }
        catch (FeatureNotSupportedException fnsEx)
        {
            // Feature not supported on device
        }
        catch (Exception ex)
        {
            // Other error has occurred.
        }
    }
}
```

[!include[](~/essentials/includes/sensor-speed.md)]

## <a name="api"></a>API

- [Código-fonte de Giroscópio](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Gyroscope)
- [Documentação da API de Giroscópio](xref:Xamarin.Essentials.Gyroscope)
