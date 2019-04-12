---
title: 'Xamarin.Essentials: Detectar movimento'
description: A classe Accelerometer no Xamarin.Essentials permite detectar um movimento do dispositivo.
ms.assetid: 07513D32-120F-4F12-8757-A47802A8027B
author: jamesmontemagno
ms.author: jamont
ms.date: 11/04/2018
ms.openlocfilehash: 2a14d739806fef353472a5186a9dbedd8e218662
ms.sourcegitcommit: be51b459a0a148ae3adca31d7599f53f7b2c3a68
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2019
ms.locfileid: "59019302"
---
# <a name="xamarinessentials-detect-shake"></a>Xamarin.Essentials: Detectar movimento

A classe **[Accelerometer](accelerometer.md)** permite monitorar o sensor de acelerômetro do dispositivo, que indica a aceleração do dispositivo no espaço tridimensional. Além disso, ele permite que você se registre para eventos quando o usuário sacudir o dispositivo.

## <a name="get-started"></a>Introdução

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-detect-shake"></a>Usando a detecção de movimento

Adicione uma referência ao Xamarin.Essentials na classe:

```csharp
using Xamarin.Essentials;
```

Para detectar um movimento do dispositivo, você deve usar a funcionalidade do acelerômetro, chamando os métodos `Start` e `Stop` para ouvir alterações na aceleração e detectar um movimento. Sempre que um movimento for detectado, um evento `ShakeDetected ` será disparado. Recomenda-se usar `Game` ou mais veloz para o `SensorSpeed`. Veja um exemplo de uso:

```csharp

public class DetectShakeTest
{
    // Set speed delay for monitoring changes.
    SensorSpeed speed = SensorSpeed.Game;

    public DetectShakeTest()
    {
        // Register for reading changes, be sure to unsubscribe when finished
        Accelerometer.ShakeDetected  += Accelerometer_ShakeDetected ;
    }

    void Accelerometer_ShakeDetected (object sender, EventArgs e)
    {
        // Process shake event
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

[!include[](~/essentials/includes/sensor-speed.md)]

## <a name="implementation-details"></a>Detalhes da implementação

A API de detecção de movimento usa leituras brutas do acelerômetro para calcular a aceleração. Ela usa um mecanismo de fila simples para detectar se 3/4 dos eventos recentes do acelerômetro ocorreram na última metade do segundo. A aceleração é calculada adicionando o quadrado das leituras de X, Y e Z do acelerômetro e comparando com um limite específico.

## <a name="api"></a>API

- [Código-fonte do acelerômetro](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Accelerometer)
- [Documentação da API do acelerômetro](xref:Xamarin.Essentials.Accelerometer)
