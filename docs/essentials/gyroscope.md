---
title: 'Xamarin.Essentials: giroscópio'
description: A classe giroscópio Xamarin.Essentials permite monitorar o sensor de giroscópio do dispositivo, que mede a rotação ao redor de três eixos de principal do dispositivo.
ms.assetid: DA4F968A-D988-41F5-8745-1BEE693660A1
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 6b2df3b6c5061464a06730ad09cbbbfdbceeb247
ms.sourcegitcommit: 72450a6a29599fa133ff4f16fb0b1f443d89f9dc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2018
ms.locfileid: "37080433"
---
# <a name="xamarinessentials-gyroscope"></a>Xamarin.Essentials: giroscópio

![Pré-lançamento NuGet](~/media/shared/pre-release.png)

O **giroscópio** classe permite que você monitore o sensor de giroscópio do dispositivo que é a rotação em torno de três eixos de principal do dispositivo.

## <a name="using-gyroscope"></a>Usando giroscópio

Adicione uma referência a Xamarin.Essentials em sua classe:

```csharp
using Xamarin.Essentials;
```

A funcionalidade de giroscópio funciona chamando o `Start` e `Stop` métodos para escuta de alterações para o giroscópio. As alterações são enviadas através de `ReadingChanged` eventos. Aqui está o exemplo de uso:

```csharp

public class GyroscopeTest
{
    // Set speed delay for monitoring changes.
    SensorSpeed speed = SensorSpeed.Ui;

    public GyroscopeTest()
    {
        // Register for reading changes.
        Gyroscope.ReadingChanged += Gyroscope_ReadingChanged;
    }

    void Gyroscope_ReadingChanged(GyroscopeChangedEventArgs e)
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

## <a name="sensor-speedxrefxamarinessentialssensorspeed"></a>[Sensor de velocidade](xref:Xamarin.Essentials.SensorSpeed)

- **Mais rápido** – obter os dados de sensor mais rápido possível (não é garantido para retornar no thread de interface do usuário).
- **Jogo** – taxa adequado para jogos (não é garantidos para retornar no thread de interface do usuário).
- **Normal** – taxa padrão adequada para alterações de orientação da tela.
- **Interface do usuário** – taxa adequado para a interface de usuário geral.

Se o manipulador de eventos não é garantido para executar no thread da interface do usuário e se o manipulador de eventos precisa acessar elementos de interface do usuário, use o [ `MainThread.BeginInvokeOnMainThread` ](main-thread.md) método para executar esse código no thread da interface do usuário.

## <a name="api"></a>API

- [Código-fonte giroscópio](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Gyroscope)
- [Documentação da API do giroscópio](xref:Xamarin.Essentials.Gyroscope)
