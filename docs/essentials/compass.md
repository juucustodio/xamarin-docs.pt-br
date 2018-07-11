---
title: 'Xamarin.Essentials: bússola'
description: Este documento descreve a classe Compass em Xamarin.Essentials, que permite que você monitore o título do Norte magnético do dispositivo.
ms.assetid: BF85B0C3-C686-43D9-811A-07DCAF8CDD86
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: cf41948c55c742140896bfb48d9bb4abf25c8d68
ms.sourcegitcommit: 632955f8cdb80712abd8dcc30e046cb9c435b922
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/10/2018
ms.locfileid: "37947407"
---
# <a name="xamarinessentials-compass"></a>Xamarin.Essentials: bússola

![Pré-lançamento NuGet](~/media/shared/pre-release.png)

O **bússola** classe permite que você monitore o título do Norte magnético do dispositivo.

## <a name="using-compass"></a>Usando a bússola

Adicione uma referência ao Xamarin.Essentials em sua classe:

```csharp
using Xamarin.Essentials;
```

A funcionalidade de bússola funciona chamando o `Start` e `Stop` métodos para ouvir alterações para a bússola. Todas as alterações são enviadas de volta pelo `ReadingChanged` eventos. Veja um exemplo:

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

[!include[](~/essentials/includes/sensor-speed.md)]

## <a name="platform-implementation-specifics"></a>Particularidades de implementação de plataforma

# <a name="androidtabandroid"></a>[Android](#tab/android)

Android não fornece uma API para recuperar o rumo da bússola. Utilizamos o acelerômetro e magnetômetro para calcular o norte magnético título, que é recomendado pelo Google. 

Em casos raros, você talvez veja resultados inconsistentes porque os sensores precisam ser calibrado, que envolve a movimentação de seu dispositivo em um movimento da Figura 8. A melhor maneira de fazer isso é abrir o Google Maps, toque no ponto para seu local e selecione **Calibrate bússola**.

Lembre-se de que executando vários sensores do seu aplicativo ao mesmo tempo pode ajustar a velocidade de sensor.

--------------

## <a name="api"></a>API

- [Código-fonte da bússola](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Compass)
- [Documentação da API bússola](xref:Xamarin.Essentials.Compass)
