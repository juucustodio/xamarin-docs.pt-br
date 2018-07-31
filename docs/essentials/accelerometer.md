---
title: 'Xamarin.Essentials: acelerômetro'
description: A classe Accelerometer Xamarin.Essentials permite que você monitore o sensor de acelerômetro do dispositivo, que indica a aceleração do dispositivo em três espaço dimensional.
ms.assetid: 97883573-F0D9-4854-AC7C-A654814401C5
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: b5a24e214eb129b4d53b94586632791c8827447b
ms.sourcegitcommit: 51c274f37369d8965b68ff587e1c2d9865f85da7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2018
ms.locfileid: "39353835"
---
# <a name="xamarinessentials-accelerometer"></a>Xamarin.Essentials: acelerômetro

![Pré-lançamento NuGet](~/media/shared/pre-release.png)

O **acelerômetro** classe permite que você monitore o sensor de acelerômetro do dispositivo que indica a aceleração do dispositivo em três espaço dimensional.

## <a name="using-accelerometer"></a>Usando o acelerômetro

Adicione uma referência ao Xamarin.Essentials em sua classe:

```csharp
using Xamarin.Essentials;
```

A funcionalidade do acelerômetro funciona chamando o `Start` e `Stop` métodos para ouvir alterações para a aceleração. Todas as alterações são enviadas de volta pelo `ReadingChanged` eventos. Aqui está o exemplo de uso:

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

Leituras do acelerômetro são relatadas de volta no G. A G é uma unidade de gravidade forçar igual àquela exercida pelo campo de gravitacional da Terra (9,81 m/s ^ 2).

O sistema de coordenadas é definido em relação à tela do telefone em sua orientação padrão. Os eixos não são trocados quando alterações de orientação da tela do dispositivo.

O eixo X é horizontal e aponta para a direita, o eixo Y é vertical e aponta para cima e o eixo Z aponta em direção a parte externa da face frontal da tela. Nesse sistema, as coordenadas atrás da tela têm valores de Z negativos.

Exemplos:

* Quando o dispositivo encontra-se simples em uma tabela e é enviada por push em seu lado esquerdo para a direita, o valor de aceleração de x é positivo.

* Quando o dispositivo encontra-se simples em uma tabela, o valor de aceleração é G + 1,00 ou (+ 9,81 m/s ^ 2), que correspondem à aceleração do dispositivo (0 m/s ^ 2) menos a força da gravidade (-9,81 m/s ^ 2) e normalizado como G.

* Quando o dispositivo encontra-se simples em uma tabela e é enviada por push para o céu com uma aceleração de um m/s ^ 2, o valor de aceleração é igual a + 9.81 correspondentes para a aceleração do dispositivo (+ um m/s ^ 2) menos a força da gravidade (-9,81 m/s ^ 2) e normalizados em G.

[!include[](~/essentials/includes/sensor-speed.md)]

## <a name="api"></a>API

- [Código-fonte do acelerômetro](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Accelerometer)
- [Documentação da API do acelerômetro](xref:Xamarin.Essentials.Accelerometer)
