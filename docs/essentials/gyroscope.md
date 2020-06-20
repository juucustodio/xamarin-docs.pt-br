---
title: 'Xamarin.Essentials: Giroscópio'
description: A classe giroscópio no Xamarin.Essentials permite que você monitore o sensor giroscópio do dispositivo, que mede a rotação em todos os três eixos principais do dispositivo.
ms.assetid: DA4F968A-D988-41F5-8745-1BEE693660A1
author: jamesmontemagno
ms.author: jamont
ms.date: 11/04/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 5a085ac5bd18e660339443ea33fe552dd86259cb
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2020
ms.locfileid: "84802302"
---
# <a name="xamarinessentials-gyroscope"></a>Xamarin.Essentials: Giroscópio

A classe **Gyroscope** permite que você monitore o sensor de giroscópio do dispositivo, que mede a rotação em torno dos três eixos principais do dispositivo.

## <a name="get-started"></a>Introdução

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-gyroscope"></a>Como usar o Giroscópio

Adicione uma referência a Xamarin.Essentials em sua classe:

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

- [Código-fonte de Giroscópio](https://github.com/xamarin/Essentials/tree/main/Xamarin.Essentials/Gyroscope)
- [Documentação da API de Giroscópio](xref:Xamarin.Essentials.Gyroscope)
