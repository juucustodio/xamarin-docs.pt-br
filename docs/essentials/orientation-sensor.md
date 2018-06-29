---
title: 'Xamarin.Essentials: OrientationSensor'
description: A classe OrientationSensor permite monitorar a orientação de um dispositivo no espaço tridimensional.
ms.assetid: F3091D93-E779-41BA-8696-23D296F2F6F5
author: charlespetzold
ms.author: chape
ms.date: 05/21/2018
ms.openlocfilehash: c7bbc849e5fa0b901f5b54e77d548b28bc2a72c6
ms.sourcegitcommit: 72450a6a29599fa133ff4f16fb0b1f443d89f9dc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2018
ms.locfileid: "37080425"
---
# <a name="xamarinessentials-orientationsensor"></a>Xamarin.Essentials: OrientationSensor

![Pré-lançamento NuGet](~/media/shared/pre-release.png)

O **OrientationSensor** classe permite que você monitore a orientação de um dispositivo no espaço bidimensional três.

> [!NOTE]
> Essa classe é para determinar a orientação de um dispositivo no espaço 3D. Se você precisar determinar se do vídeo do dispositivo vídeo está no modo retrato ou paisagem, use o `Orientation` propriedade do `ScreenMetrics` disponível a partir do objeto de [ `DeviceDisplay` ](device-display.md) classe.

## <a name="using-orientationsensor"></a>Usando OrientationSensor

Adicione uma referência a Xamarin.Essentials em sua classe:

```csharp
using Xamarin.Essentials;
```

O `OrientationSensor` é ativada chamando o `Start` método para monitorar as alterações a orientação do dispositivo e desabilitado chamando o `Stop` método. As alterações são enviadas através de `ReadingChanged` eventos. Aqui está um exemplo de uso:

```csharp

public class OrientationSensorTest
{
    // Set speed delay for monitoring changes.
    SensorSpeed speed = SensorSpeed.Ui;

    public OrientationSensorTest()
    {
        // Register for reading changes, be sure to unsubscribe when finished
        OrientationSensor.ReadingChanged += OrientationSensor_ReadingChanged;
    }

    void OrientationSensor_ReadingChanged(AccelerometerChangedEventArgs e)
    {
        var data = e.Reading;
        Console.WriteLine($"Reading: X: {data.Orientation.X}, Y: {data.Orientation.Y}, Z: {data.Orientation.Z}, W: {data.Orientation.W}");
        // Process Orientation quaternion (X, Y, Z, and W)
    }

    public void ToggleOrientationSensor()
    {
        try
        {
            if (OrientationSensor.IsMonitoring)
                OrientationSensor.Stop();
            else
                OrientationSensor.Start(speed);
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

`OrientationSensor` as leituras são relatadas de volta na forma de um [ `Quaternion` ](xref:System.Numerics.Quaternion) que descreve a orientação do dispositivo com base em dois sistemas de coordenadas 3D:

O dispositivo (geralmente um telefone ou tablet) tem um sistema de coordenadas 3D com os seguintes eixos:

- O sinal positivo pontos à direita da exibição no modo retrato do eixo X.
- O eixo Y positivo aponta para a parte superior do dispositivo no modo retrato.
- O eixo Z positivo pontos fora da tela.

O sistema de coordenadas 3D da Terra tem os seguintes eixos:

- O sinal positivo eixo X tangente à superfície da Terra e pontos Leste.
- O eixo Y positivo também é a superfície da Terra e Norte pontos tangente.
- O eixo Z positivo é perpendicular à superfície da Terra e pontos de backup.

O `Quaternion` descreve a rotação de sistema de coordenadas do dispositivo em relação ao sistema de coordenadas da Terra.

Um `Quaternion` valor está muito relacionado à rotação ao redor de um eixo. Se um eixo de rotação é o vetor normalizado (um<sub>x</sub>, um<sub>y</sub>, um<sub>z</sub>), e o ângulo de rotação é Θ, em seguida, o (X, Y, Z, W) componentes o quaternion são:

(um<sub>x</sub>·sin(Θ/2), um<sub>y</sub>·sin(Θ/2), um<sub>z</sub>·sin(Θ/2), cos(Θ/2))

Estes são os sistemas de coordenadas à direita, portanto, com o dedo da direita mão apontado na direção positiva do eixo de rotação, a curva dos dedos indicar a direção de rotação ângulos positivos.

Exemplos:

* Quando o dispositivo está simples em uma tabela com sua tela voltado para cima, com a parte superior do dispositivo (no modo de retrato) apontando Norte, dois sistemas de coordenadas são alinhados. O `Quaternion` valor representa o quaternion de identidade (0, 0, 0, 1). Todas as rotações podem ser analisadas em relação a essa posição.

* Quando o dispositivo está simples em uma tabela com sua tela voltado para cima e a parte superior do dispositivo (no modo de retrato) apontando Oeste o `Quaternion` valor é (0, 0, 0.707, 0.707). O dispositivo foi girado 90 graus em torno do eixo Z da Terra.

* Quando o dispositivo é mantido na posição perpendicular, para que a parte superior (em modo de retrato) aponta para o sky e posterior do dispositivo faces Norte, o dispositivo for girados 90 graus em torno do eixo X. O `Quaternion` valor é (0.707, 0, 0, 0.707).

* Se o dispositivo é posicionado para sua borda esquerda está em uma tabela e os principais pontos Norte, o dispositivo foi girado &ndash;90 graus em torno do eixo Y (ou 90 graus em torno do eixo Y negativo). O `Quaternion` valor é (0,-0.707, 0, 0.707).

## <a name="sensor-speedxrefxamarinessentialssensorspeed"></a>[Sensor de velocidade](xref:Xamarin.Essentials.SensorSpeed)

- **Mais rápido** – obter os dados de sensor mais rápido possível (não é garantido para retornar no thread de interface do usuário).
- **Jogo** – taxa adequado para jogos (não é garantidos para retornar no thread de interface do usuário).
- **Normal** – taxa padrão adequada para alterações de orientação da tela.
- **Interface do usuário** – taxa adequado para a interface de usuário geral.

Se o manipulador de eventos não é garantido para executar no thread da interface do usuário e se o manipulador de eventos precisa acessar elementos de interface do usuário, use o [ `MainThread.BeginInvokeOnMainThread` ](main-thread.md) método para executar esse código no thread da interface do usuário.

## <a name="api"></a>API

- [Código-fonte OrientationSensor](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/OrientationSensor)
- [Documentação da API OrientationSensor](xref:Xamarin.Essentials.OrientationSensor)
