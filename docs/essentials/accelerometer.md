---
title: Xamarin.Essentials acelerômetro
description: A classe acelerômetro permite monitorar sensor do acelerômetro do dispositivo que indica a aceleração do dispositivo em três espaço bidimensional.
ms.assetid: 97883573-F0D9-4854-AC7C-A654814401C5
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: bb62ad438c2db906af112322174656bc62740cbc
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/09/2018
---
# <a name="xamarinessentials-accelerometer"></a>Xamarin.Essentials acelerômetro

![Pré-lançamento NuGet](~/media/shared/pre-release.png)

O **acelerômetro** classe permite que você monitore o sensor de acelerômetro do dispositivo que indica a aceleração do dispositivo em três espaço bidimensional.

## <a name="using-accelerometer"></a>Usando o acelerômetro

Adicione uma referência a Xamarin.Essentials em sua classe:

```csharp
using Xamarin.Essentials;
```

A funcionalidade de acelerômetro funciona chamando o `Start` e `Stop` métodos para escuta de alterações para a aceleração. As alterações são enviadas através de `ReadingChanged` eventos. Aqui está o exemplo de uso:

```csharp

public class AccelerometerTest
{
    // Set speed delay for monitoring changes.
    SensorSpeed speed = SensorSpeed.Ui;

    public AccelerometerTest()
    {
        // Register for reading changes, be sure to unsubscribe when finished
        Accelerometer.ReadingChanged += Accelerometer_ReadingChanged;
    }

    void Accelerometer_ReadingChanged(AccelerometerChangedEventArgs e)
    {
        var data = e.Reading;
        Console.WriteLine($"Reading: X: {data.Acceleration.X}, Y: {data.Acceleration.Y}, Z: {data.Acceleration.Z}");
        // Process Acceleration X, Y, and Z
    }

    public void ToggleAcceleromter()
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

Leituras acelerômetro são relatadas de volta no G. Um G é uma unidade de gravitação forçar igual ao exercida pelo campo gravitacional da Terra (9.81 m/s ^ 2).

O sistema de coordenadas é definido em relação à tela do telefone em sua orientação padrão. Os eixos não são alternados quando alterações de orientação da tela do dispositivo.

O eixo X é horizontal e aponta para a direita, o eixo Y é vertical e pontos de e o eixo Z aponta para fora da face frontal da tela. Nesse sistema, coordenadas atrás da tela tem valores de Z negativos.

Exemplos:

* Quando o dispositivo está simples em uma tabela e é enviado em seu lado esquerdo para a direita, o valor de aceleração de x é positivo.

* Quando o dispositivo está simples em uma tabela, o valor de aceleração é + 1,00 G ou (+ 9.81 m/s ^ 2), que corresponde a aceleração do dispositivo (0 m/s ^ 2) a força da gravidade de subtração (-9.81 m/s ^ 2) e normalizado como G.

* Quando o dispositivo está simples em uma tabela e é enviado para o sky com uma aceleração de um m/s ^ 2, o valor de aceleração é igual a 9.81 + que correspondem à aceleração do dispositivo (+ um m/s ^ 2) a força da gravidade de subtração (-9.81 m/s ^ 2) e normalizado em G. 

## <a name="sensor-speedxrefxamarinessentialssensorspeed"></a>[Sensor de velocidade](xref:Xamarin.Essentials.SensorSpeed)

- **Mais rápido** – obter os dados de sensor mais rápido possível (não é garantido para retornar no thread de interface do usuário).
- **Jogo** – taxa adequado para jogos (não é garantidos para retornar no thread de interface do usuário).
- **Normal** – taxa padrão adequada para alterações de orientação da tela.
- **Interface do usuário** – taxa adequado para a interface de usuário geral.

## <a name="api"></a>API

- [Código-fonte acelerômetro](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Accelerometer)
- [Documentação da API do acelerômetro](xref:Xamarin.Essentials.Accelerometer)
