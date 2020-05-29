---
Título: " Xamarin.Essentials : bússola" Descrição: "este documento descreve a classe Compass no Xamarin.Essentials , que permite monitorar o cabeçalho do norte magnético do dispositivo".
MS. AssetID: BF85B0C3-C686-43D9-811A-07DCAF8CDD86 autor: jamesmontemagno MS. Custom: vídeo MS. Author: Jamont MS. Date: 11/04/2018 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="xamarinessentials-compass"></a>Xamarin.Essentials: Bússola

A classe **Compass** permite que você monitore o norte magnético do dispositivo.

## <a name="get-started"></a>Introdução

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-compass"></a>Como usar Compass

Adicione uma referência a Xamarin.Essentials em sua classe:

```csharp
using Xamarin.Essentials;
```

A funcionalidade de Bússola funciona chamando os métodos `Start` e `Stop` e fica atenta às alterações na bússola. Todas as alterações são enviadas de volta por meio do evento `ReadingChanged`. Veja um exemplo:

```csharp
public class CompassTest
{
    // Set speed delay for monitoring changes.
    SensorSpeed speed = SensorSpeed.UI;

    public CompassTest()
    {
        // Register for reading changes, be sure to unsubscribe when finished
        Compass.ReadingChanged += Compass_ReadingChanged;
    }

    void Compass_ReadingChanged(object sender, CompassChangedEventArgs e)
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
            // Some other exception has occurred
        }
    }
}
```

[!include[](~/essentials/includes/sensor-speed.md)]

## <a name="platform-implementation-specifics"></a>Particularidades de implementação da plataforma

# <a name="android"></a>[Android](#tab/android)

O Android não fornece uma API para recuperar o título da bússola. Utilizamos o acelerômetro e o magnetômetro para calcular o norte magnético, o que é recomendado pelo Google.

Em casos raros, talvez você veja resultados inconsistentes devido à necessidade de calibração dos sensores, o que envolve a movimentação do seu dispositivo em um movimento de figura 8. A melhor maneira de fazer isso é abrir o Google Maps, tocar no ponto do seu local e selecionar **Calibrar bússola**.

A execução de vários sensores do seu aplicativo ao mesmo tempo pode ajustar a velocidade do sensor.

## <a name="low-pass-filter"></a>Filtro Passa Baixa

Devido ao modo como os valores da bússola do Android são atualizados e calculados, pode haver a necessidade para suavizar os valores. Um _filtro de baixa Pass_ pode ser aplicado, que calcula a média dos valores seno e cosseno dos ângulos e pode ser ativado usando a `Start` sobrecarga do método, que aceita o `bool applyLowPassFilter` parâmetro:

```csharp
Compass.Start(SensorSpeed.UI, applyLowPassFilter: true);
```

Isso só é aplicado na plataforma Android, e o parâmetro é ignorado no iOS e UWP.  Encontre mais informações [aqui](https://github.com/xamarin/Essentials/pull/354#issuecomment-405316860).

--------------

## <a name="api"></a>API

- [Código-fonte da Bússola](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Compass)
- [Documentação da API de Bússola](xref:Xamarin.Essentials.Compass)

## <a name="related-video"></a>Vídeo relacionados

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Compass-XamarinEssentials-API-of-the-Week/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
