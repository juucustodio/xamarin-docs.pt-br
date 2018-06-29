---
title: 'Xamarin.Essentials: bússola'
description: Este documento descreve a classe Compass em Xamarin.Essentials, que permite que você monitore o título do Norte magnética do dispositivo.
ms.assetid: BF85B0C3-C686-43D9-811A-07DCAF8CDD86
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 63818014a9b3bdbef479055cbbcfbf8d348080fc
ms.sourcegitcommit: 72450a6a29599fa133ff4f16fb0b1f443d89f9dc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2018
ms.locfileid: "37080424"
---
# <a name="xamarinessentials-compass"></a>Xamarin.Essentials: bússola

![Pré-lançamento NuGet](~/media/shared/pre-release.png)

O **bússola** classe permite que você monitore o título do Norte magnética do dispositivo.

## <a name="using-compass"></a>Usando bússola

Adicione uma referência a Xamarin.Essentials em sua classe:

```csharp
using Xamarin.Essentials;
```

A funcionalidade de bússola funciona chamando o `Start` e `Stop` métodos para ouvir alterações bússola. As alterações são enviadas através de `ReadingChanged` eventos. Veja um exemplo:

```csharp
public class CompassTest
{
    // Set speed delay for monitoring changes.
    SensorSpeed speed = SensorSpeed.Ui;

    public CompassTest()
    {
        // Register for reading changes, be sure to unsubscribe when finished
        Compass.ReadingChanged += Compass_ReadingChanged;
    }

    void Compass_ReadingChanged(CompassChangedEventArgs e)
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
            // Some other exception has occured
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

## <a name="platform-implementation-specifics"></a>Detalhes de implementação de plataforma

# <a name="androidtabandroid"></a>[Android](#tab/android)

Android não fornece uma API para recuperar o título bússola. Utilizamos o acelerômetro e magnetômetro para calcular o título magnética Norte, que é recomendado pelo Google. 

Em casos raros, você talvez ver resultados inconsistentes porque os sensores precisam ser calibrada, que envolve mover seu dispositivo em um movimento da Figura 8. A melhor maneira de fazer isso é abrir o Google Maps, toque em um ponto para seu local e selecione **calibrar bússola**.

Lembre-se de que executando vários sensores de seu aplicativo ao mesmo tempo pode ajustar a velocidade de sensor.

--------------

## <a name="api"></a>API

- [Código-fonte bússola](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Compass)
- [Documentação da API bússola](xref:Xamarin.Essentials.Compass)
