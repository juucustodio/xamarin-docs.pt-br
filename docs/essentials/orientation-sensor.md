---
title: 'Xamarin.Essentials: OrientationSensor'
description: A classe OrientationSensor permite monitorar a orientação de um dispositivo no espaço tridimensional.
ms.assetid: F3091D93-E779-41BA-8696-23D296F2F6F5
author: charlespetzold
ms.author: chape
ms.date: 05/21/2018
ms.openlocfilehash: a15338795424885882ed9c86288342d196f6fda2
ms.sourcegitcommit: 51c274f37369d8965b68ff587e1c2d9865f85da7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2018
ms.locfileid: "39353809"
---
# <a name="xamarinessentials-orientationsensor"></a>Xamarin.Essentials: OrientationSensor

![Pré-lançamento NuGet](~/media/shared/pre-release.png)

O **OrientationSensor** classe permite que você monitore a orientação de um dispositivo em três espaço dimensional.

> [!NOTE]
> Essa classe é para determinar a orientação de um dispositivo no espaço 3D. Se você precisar determinar se o dispositivo do vídeo vídeo está no modo retrato ou paisagem, use o `Orientation` propriedade do `ScreenMetrics` disponível a partir do objeto a [ `DeviceDisplay` ](device-display.md) classe.

## <a name="using-orientationsensor"></a>Usando OrientationSensor

Adicione uma referência ao Xamarin.Essentials em sua classe:

```csharp
using Xamarin.Essentials;
```

O `OrientationSensor` está habilitado por meio da chamada a `Start` método para monitorar as alterações a orientação do dispositivo e desabilitado chamando o `Stop` método. Todas as alterações são enviadas de volta pelo `ReadingChanged` eventos. Aqui está um exemplo de uso:

```csharp

public class OrientationSensorTest
{
    // Set speed delay for monitoring changes.
    SensorSpeed speed = SensorSpeed.UI;

    public OrientationSensorTest()
    {
        // Register for reading changes, be sure to unsubscribe when finished
        OrientationSensor.ReadingChanged += OrientationSensor_ReadingChanged;
    }

    void OrientationSensor_ReadingChanged(object sender, OrientationSensorChangedEventArgs e)
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

`OrientationSensor` as leituras são reportadas na forma de um [ `Quaternion` ](xref:System.Numerics.Quaternion) que descreve a orientação do dispositivo com base em dois sistemas de coordenadas 3D:

O dispositivo (geralmente um telefone ou tablet) tem um sistema de coordenadas 3D com os seguintes eixos:

- O positivo X pontos de eixo à direita da exibição no modo retrato.
- O eixo Y positivo aponta para a parte superior do dispositivo no modo retrato.
- Eixo positivo Z aponta para fora da tela.

O sistema de coordenadas 3D da Terra tem os seguintes eixos:

- O positivo eixo X é a tangente para a superfície da Terra e aponta Leste.
- O eixo Y positivo também é a superfície da Terra e Norte pontos tangente.
- Eixo positivo Z é perpendicular para a superfície da Terra e pontos de backup.

O `Quaternion` descreve a rotação do sistema de coordenadas do dispositivo em relação ao sistema de coordenadas da Terra.

Um `Quaternion` valor está muito intimamente relacionado a rotação em torno de um eixo. Se um eixo de rotação é o vetor normalizado (um<sub>x</sub>, um<sub>y</sub>, um<sub>z</sub>), e o ângulo de rotação é Θ, em seguida, (X, Y, Z, W) os componentes do quatérnio são:

(um<sub>x</sub>·sin(Θ/2), um<sub>y</sub>·sin(Θ/2), uma<sub>z</sub>·sin(Θ/2), cos(Θ/2))

Esses são os sistemas de coordenadas à direita, portanto, com o polegar da mão direita apontado na direção do eixo de rotação positiva, a curva dos dedos indicar a direção de rotação para ângulos positivos.

Exemplos:

* Quando o dispositivo encontra-se simples em uma tabela com sua tela virada para cima, com a parte superior do dispositivo (no modo retrato) apontando para Norte, os dois sistemas de coordenadas são alinhados. O `Quaternion` valor representa o quatérnio de identidade (0, 0, 0, 1). Todas as rotações podem ser analisadas em relação a essa posição.

* Quando o dispositivo encontra-se simples em uma tabela com sua tela virada para cima e a parte superior do dispositivo (no modo retrato) apontando para Oeste, o `Quaternion` valor é (0, 0, 0.707, 0.707). O dispositivo foi girado 90 graus em torno do eixo Z da Terra.

* Quando o dispositivo é mantido na vertical para que a parte superior (no modo retrato) aponta para o céu e faces de trás do dispositivo Norte, o dispositivo foi girada em 90 graus em torno do eixo X. O `Quaternion` valor é (0.707, 0, 0, 0.707).

* Se o dispositivo estiver posicionado para que sua borda esquerda está em uma tabela e os principais pontos Norte, o dispositivo foi girado &ndash;90 graus em torno do eixo Y (ou 90 graus em torno do eixo Y negativo). O `Quaternion` valor é (0,-0.707, 0, 0.707).

[!include[](~/essentials/includes/sensor-speed.md)]

## <a name="api"></a>API

- [Código-fonte OrientationSensor](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/OrientationSensor)
- [Documentação da API OrientationSensor](xref:Xamarin.Essentials.OrientationSensor)
