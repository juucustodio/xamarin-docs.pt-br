---
title: 'Xamarin.Essentials: Acelerômetro'
description: A classe acelerômetro no Xamarin.Essentials permite monitorar o sensor de acelerômetro do dispositivo, que indica a aceleração do dispositivo em três espaços dimensionais.
ms.assetid: 97883573-F0D9-4854-AC7C-A654814401C5
author: jamesmontemagno
ms.author: jamont
ms.date: 04/02/2019
ms.custom: video
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: f3d7b313039e66294a0095fd34a2caa6689cef2e
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2020
ms.locfileid: "84802530"
---
# <a name="xamarinessentials-accelerometer"></a>Xamarin.Essentials: Acelerômetro

A classe **Accelerometer** permite monitorar o sensor de acelerômetro do dispositivo, que indica a aceleração do dispositivo no espaço tridimensional.

## <a name="get-started"></a>Introdução

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-accelerometer"></a>Uso do acelerômetro

Adicione uma referência a Xamarin.Essentials em sua classe:

```csharp
using Xamarin.Essentials;
```

A funcionalidade do Acelerômetro chama os métodos `Start` e `Stop` e escuta as alterações na aceleração. Todas as alterações são enviadas de volta por meio do evento `ReadingChanged`. Veja um exemplo de uso:

```csharp

public class AccelerometerTest
{
    // Set speed delay for monitoring changes.
    SensorSpeed speed = SensorSpeed.UI;

    public AccelerometerTest()
    {
        // Register for reading changes, be sure to unsubscribe when finished
        Accelerometer.ReadingChanged += Accelerometer_ReadingChanged;
    }

    void Accelerometer_ReadingChanged(object sender, AccelerometerChangedEventArgs e)
    {
        var data = e.Reading;
        Console.WriteLine($"Reading: X: {data.Acceleration.X}, Y: {data.Acceleration.Y}, Z: {data.Acceleration.Z}");
        // Process Acceleration X, Y, and Z
    }

    public void ToggleAccelerometer()
    {
        try
        {
            if (Accelerometer.IsMonitoring)
              Accelerometer.Stop();
            else
              Accelerometer.Start(speed);
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

As leituras do acelerômetro são registradas em G. A G, uma unidade de força gravitacional equivalente à exercida pelo campo gravitacional da Terra (9,81 m/s2).

O sistema de coordenadas é definido em relação à tela do telefone na orientação padrão. Os eixos não são trocados quando a orientação da tela do dispositivo é alterada.

O eixo X é horizontal e aponta para a direita; o eixo Y é vertical e aponta para cima; o eixo Z aponta para a parte externa da face frontal da tela. Nesse sistema, as coordenadas atrás da tela têm valores de Z negativos.

Exemplos:

- Quando o dispositivo estiver em uma superfície plana e for empurrado da esquerda para a direita, o valor de aceleração x será positivo.

- Quando o dispositivo estiver em uma superfície plana, o valor de aceleração será +1,00 G ou (+9,81 m/s2), o que corresponde à aceleração do dispositivo (0 m/s2) menos a força da gravidade (-9,81 m/s2) e normalizado em G.

- Quando o dispositivo estiver em uma superfície plana e for empurrado para cima com uma aceleração de A m/s^2, o valor da aceleração será igual a A + 9,81, o que corresponde à aceleração do dispositivo (+A m/s^2) menos a força da gravidade (-9,81 m/s^2) e normalizado em G.

[!include[](~/essentials/includes/sensor-speed.md)]

## <a name="api"></a>API

- [Código-fonte do acelerômetro](https://github.com/xamarin/Essentials/tree/main/Xamarin.Essentials/Accelerometer)
- [Documentação da API do acelerômetro](xref:Xamarin.Essentials.Accelerometer)

## <a name="related-video"></a>Vídeo relacionados

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Accelerometer-XamarinEssentials-API-of-the-Week/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
