---
title: 'Xamarin.Essentials: Magnetômetro'
description: A classe magnetômetro no Xamarin.Essentials permite que você monitore o sensor magnetômetro do dispositivo, que indica a orientação do dispositivo em relação ao campo magnético da terra.
ms.assetid: 64DD0D41-03E2-40DD-9EC8-101CA0ED852B
author: jamesmontemagno
ms.author: jamont
ms.date: 11/04/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: b01bc1fd9c65186952635c5f472b1ac6beb0c9bd
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2020
ms.locfileid: "84802284"
---
# <a name="xamarinessentials-magnetometer"></a>Xamarin.Essentials: Magnetômetro

A classe **Magnetometer** permite que você monitore o sensor do magnetômetro do dispositivo, que indica a orientação do dispositivo em relação ao campo magnético da Terra.

## <a name="get-started"></a>Introdução

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-magnetometer"></a>Como usar Magnetometer

Adicione uma referência a Xamarin.Essentials em sua classe:

```csharp
using Xamarin.Essentials;
```

A funcionalidade do Magnetometer chama os métodos `Start` e `Stop` para ficar atenta às alterações no magnetômetro. Todas as alterações são enviadas de volta por meio do evento `ReadingChanged`. Veja um exemplo de uso:

```csharp

public class MagnetometerTest
{
    // Set speed delay for monitoring changes.
    SensorSpeed speed = SensorSpeed.UI;

    public MagnetometerTest()
    {
        // Register for reading changes.
        Magnetometer.ReadingChanged += Magnetometer_ReadingChanged;
    }

    void Magnetometer_ReadingChanged(object sender, MagnetometerChangedEventArgs e)
    {
        var data = e.Reading;
        // Process MagneticField X, Y, and Z
        Console.WriteLine($"Reading: X: {data.MagneticField.X}, Y: {data.MagneticField.Y}, Z: {data.MagneticField.Z}");
    }

    public void ToggleMagnetometer()
    {
        try
        {
            if (Magnetometer.IsMonitoring)
              Magnetometer.Stop();
            else
              Magnetometer.Start(speed);
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

Todos os dados são retornados em μT (microteslas).

[!include[](~/essentials/includes/sensor-speed.md)]

## <a name="api"></a>API

- [Código-fonte de Magnetômetro](https://github.com/xamarin/Essentials/tree/main/Xamarin.Essentials/Magnetometer)
- [Documentação da API de Magnetômetro](xref:Xamarin.Essentials.Magnetometer)
