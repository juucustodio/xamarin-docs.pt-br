---
title: 'Xamarin.Essentials: Detectar agite'
description: A classe acelerômetro no Xamarin.Essentials permite que você detecte um movimento agitado do dispositivo.
ms.assetid: 07513D32-120F-4F12-8757-A47802A8027B
author: jamesmontemagno
ms.author: jamont
ms.date: 05/28/2019
ms.custom: video
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 4fd4c7d0e3ffe04ebbb936cff0992272a9085649
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2020
ms.locfileid: "84802423"
---
# <a name="xamarinessentials-detect-shake"></a>Xamarin.Essentials: Detectar agite

A classe **[Accelerometer](accelerometer.md)** permite monitorar o sensor de acelerômetro do dispositivo, que indica a aceleração do dispositivo no espaço tridimensional. Além disso, ele permite que você se registre para eventos quando o usuário sacudir o dispositivo.

## <a name="get-started"></a>Introdução

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-detect-shake"></a>Usando a detecção de movimento

Adicione uma referência a Xamarin.Essentials em sua classe:

```csharp
using Xamarin.Essentials;
```

Para detectar um movimento do dispositivo, você deve usar a funcionalidade do acelerômetro, chamando os métodos `Start` e `Stop` para ouvir alterações na aceleração e detectar um movimento. Sempre que um movimento for detectado, um evento `ShakeDetected` será disparado. Recomenda-se usar `Game` ou mais veloz para o `SensorSpeed`. Veja um exemplo de uso:

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

- [Código-fonte do acelerômetro](https://github.com/xamarin/Essentials/tree/main/Xamarin.Essentials/Accelerometer)
- [Documentação da API do acelerômetro](xref:Xamarin.Essentials.Accelerometer)

## <a name="related-video"></a>Vídeo relacionados

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Detect-Shake-XamarinEssentials-API-of-the-Week/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
