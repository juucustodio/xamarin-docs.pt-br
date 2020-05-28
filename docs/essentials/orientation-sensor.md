---
Título: ' ' Xamarin.Essentials : OrientationSensor ' ' Descrição: MS. AssetID: autor: MS. Author: MS. Date: no-loc:
- 'Xamarin.Forms'
- 'Xamarin.Essentials'

---
# <a name="xamarinessentials-orientationsensor"></a>Xamarin.Essentials: OrientationSensor

A classe **OrientationSensor** permite monitorar a orientação de um dispositivo no espaço tridimensional.

> [!NOTE]
> Essa classe é usada para determinar a orientação de um dispositivo no espaço tridimensional. Se você precisar determinar se a exibição de vídeo do dispositivo está no modo retrato ou paisagem, use a `Orientation` Propriedade do `ScreenMetrics` objeto disponível na [`DeviceDisplay`](device-display.md) classe.

## <a name="get-started"></a>Introdução

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-orientationsensor"></a>Uso do OrientationSensor

Adicione uma referência a Xamarin.Essentials em sua classe:

```csharp
using Xamarin.Essentials;
```

O `OrientationSensor` é habilitado pela chamada do método `Start`, para monitorar as alterações da orientação do dispositivo, e desabilitado chamando o método `Stop`. Todas as alterações são enviadas de volta por meio do evento `ReadingChanged`. Veja um exemplo de uso:

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

`OrientationSensor`as leituras são relatadas de volta na forma de um [`Quaternion`](xref:System.Numerics.Quaternion) que descreve a orientação do dispositivo com base em dois sistemas de coordenadas 3D:

O dispositivo (geralmente um telefone ou tablet) possui um sistema de coordenadas 3D com os seguintes eixos:

- O eixo positivo X aponta para o canto direito do visor no modo retrato.
- O eixo positivo Y aponta para o canto superior do dispositivo no modo retrato.
- O eixo positivo Z aponta para fora da tela.

O sistema de coordenadas 3D da Terra tem os seguintes eixos:

- O eixo positivo X é a tangente da superfície da Terra e aponta para leste.
- O eixo positivo Y também é a tangente da superfície da Terra e aponta para norte.
- O eixo positivo Z é perpendicular à superfície da Terra e aponta para cima.

O `Quaternion` descreve a rotação do sistema de coordenadas do dispositivo em relação ao sistema de coordenadas da Terra.

Um valor `Quaternion` está muito intimamente relacionado à rotação em torno de um eixo. Se um eixo de rotação for o vetor normalizado (a<sub>x</sub>, a<sub>y</sub>, a<sub>z</sub>) e o ângulo de rotação for Θ, então, os componentes (X, Y, Z, W) do quatérnio serão:

(a<sub>x</sub>·sin(Θ/2), a<sub>y</sub>·sin(Θ/2), a<sub>z</sub>·sin(Θ/2), cos(Θ/2))

Estes são sistemas de coordenadas à direita, portanto, com o polegar da mão direita apontado na direção positiva do eixo de rotação, a curva dos dedos indica a direção da rotação para ângulos positivos.

Exemplos:

- Os dois sistemas de coordenadas estarão alinhados quando o dispositivo estiver em uma mesa com a tela voltada para cima e a parte superior do dispositivo (no modo retrato) estiver apontando para norte. O valor `Quaternion` representa o quatérnio da identidade (0, 0, 0, 1). Todas as rotações podem ser analisadas em relação a essa posição.

- O valor `Quaternion` será (0, 0, 0.707, 0.707) quando dispositivo estiver em uma mesa com a tela voltada para cima e a parte superior do dispositivo (no modo retrato) estiver apontando para oeste. O dispositivo girou 90 graus em torno do eixo Z da Terra.

- O dispositivo terá girado 90 graus ao redor do eixo X quando for colocado na posição vertical de modo que a parte superior (no modo retrato) aponte para o céu e a parte de trás do dispositivo fique voltada para norte. O valor `Quaternion` é (0.707, 0, 0, 0.707).

- O dispositivo terá sido girado &ndash;90 graus ao redor do eixo Y (ou 90 graus ao redor do eixo negativo Y) se ele estiver posicionado de forma que sua borda esquerda esteja sobre uma mesa e os pontos superiores ao norte. O valor `Quaternion` é (0, -0.707, 0, 0.707).

[!include[](~/essentials/includes/sensor-speed.md)]

## <a name="api"></a>API

- [Código-fonte do OrientationSensor](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/OrientationSensor)
- [Documentação da API do OrientationSensor](xref:Xamarin.Essentials.OrientationSensor)
