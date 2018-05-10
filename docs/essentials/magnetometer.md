---
title: Magnetômetro Xamarin.Essentials
description: A classe magnetômetro permite monitorar o sensor de magnetômetro do dispositivo que indica a orientação do dispositivo em relação ao campo de magnética da Terra.
ms.assetid: 64DD0D41-03E2-40DD-9EC8-101CA0ED852B
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: bb9bd656c809b05c49a27f7b3dab2a64ff7b7e94
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/09/2018
---
# <a name="xamarinessentials-magnetometer"></a>Magnetômetro Xamarin.Essentials

![Pré-lançamento NuGet](~/media/shared/pre-release.png)

O **magnetômetro** classe permite que você monitore o sensor de magnetômetro do dispositivo que indica a orientação do dispositivo em relação ao campo de magnética da Terra.

## <a name="using-magnetometer"></a>Usando magnetômetro

Adicione uma referência a Xamarin.Essentials em sua classe:

```csharp
using Xamarin.Essentials;
```

A funcionalidade de magnetômetro funciona chamando o `Start` e `Stop` métodos para escuta de alterações para o magnetômetro. As alterações são enviadas através de `ReadingChanged` eventos. Aqui está o exemplo de uso:

```csharp

public class MagnetometerTest
{
    // Set speed delay for monitoring changes.
    SensorSpeed speed = SensorSpeed.Ui;

    public MagnetometerTest()
    {
        // Register for reading changes.
        Magnetometer.ReadingChanged += Magnetometer_ReadingChanged;
    }

    void Magnetometerr_ReadingChanged(MagnetometerChangedEventArgs e)
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

Todos os dados são retornados em microteslas.

## <a name="sensor-speedxrefxamarinessentialssensorspeed"></a>[Sensor de velocidade](xref:Xamarin.Essentials.SensorSpeed)

- **Mais rápido** – obter os dados de sensor mais rápido possível (não é garantido para retornar no thread de interface do usuário).
- **Jogo** – taxa adequado para jogos (não é garantidos para retornar no thread de interface do usuário).
- **Normal** – taxa padrão adequada para alterações de orientação da tela.
- **Interface do usuário** – taxa adequado para a interface de usuário geral.

## <a name="api"></a>API

- [Código-fonte magnetômetro](https://github.com/xamarin/Essentials/tree/master/Essentials/Magnetometer)
- [Documentação da API de magnetômetro](xref:Xamarin.Essentials.Magnetometer)
