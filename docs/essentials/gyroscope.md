---
title: 'Xamarin.Essentials: giroscópio'
description: A classe de giroscópio Xamarin.Essentials permite que você monitore o sensor de giroscópio do dispositivo, que mede a rotação em torno de três eixos de primário do dispositivo.
ms.assetid: DA4F968A-D988-41F5-8745-1BEE693660A1
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: f1e1199ae32158889ec569eb5f7e9742f37d45d4
ms.sourcegitcommit: 51c274f37369d8965b68ff587e1c2d9865f85da7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2018
ms.locfileid: "39353620"
---
# <a name="xamarinessentials-gyroscope"></a>Xamarin.Essentials: giroscópio

![Pré-lançamento NuGet](~/media/shared/pre-release.png)

O **giroscópio** classe permite que você monitore o sensor de giroscópio do dispositivo que é a rotação em torno de três eixos de primário do dispositivo.

## <a name="using-gyroscope"></a>Usando giroscópio

Adicione uma referência ao Xamarin.Essentials em sua classe:

```csharp
using Xamarin.Essentials;
```

A funcionalidade de giroscópio funciona chamando o `Start` e `Stop` métodos para ouvir alterações para o giroscópio. Todas as alterações são enviadas de volta pelo `ReadingChanged` eventos. Aqui está o exemplo de uso:

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
        // Process Angular Velocity X, Y, and Z
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

- [Código-fonte giroscópio](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Gyroscope)
- [Documentação da API de giroscópio](xref:Xamarin.Essentials.Gyroscope)
