---
title: Posicionamento de vídeo personalizado
description: Este artigo explica como implementar uma barra de posição personalizada em um aplicativo de player de vídeo, usando o Xamarin.Forms .
ms.prod: xamarin
ms.assetid: 6D792264-30FF-46F7-8C1B-2FEF9D277DF4
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/12/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 00b6617f754eb251eae0a7be9715deb840c33102
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91562737"
---
# <a name="custom-video-positioning"></a>Posicionamento de vídeo personalizado

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-videoplayerdemos)

Os controles de transporte implementados por cada plataforma incluem uma barra de posição. Essa barra é semelhante a um controle deslizante ou a uma barra de rolagem e mostra a localização atual do vídeo dentro de sua duração total. Além disso, o usuário pode manipular a barra de posição para mover para frente ou para trás para uma nova posição no vídeo.

Este artigo mostra como é possível implementar sua própria barra de posição personalizada.

## <a name="the-duration-property"></a>A propriedade Duration

Um item de informações que o `VideoPlayer` precisa para dar suporte a uma barra de posição personalizada é a duração do vídeo. O `VideoPlayer` define uma propriedade `Duration` somente leitura do tipo `TimeSpan`:

```csharp
namespace FormsVideoLibrary
{
    public class VideoPlayer : View, IVideoPlayerController
    {
        ···
        // Duration read-only property
        private static readonly BindablePropertyKey DurationPropertyKey =
            BindableProperty.CreateReadOnly(nameof(Duration), typeof(TimeSpan), typeof(VideoPlayer), new TimeSpan(),
                propertyChanged: (bindable, oldValue, newValue) => ((VideoPlayer)bindable).SetTimeToEnd());

        public static readonly BindableProperty DurationProperty = DurationPropertyKey.BindableProperty;

        public TimeSpan Duration
        {
            get { return (TimeSpan)GetValue(DurationProperty); }
        }

        TimeSpan IVideoPlayerController.Duration
        {
            set { SetValue(DurationPropertyKey, value); }
            get { return Duration; }
        }
        ···
    }
}
```

Como a propriedade `Status` descrita no [artigo anterior](custom-transport.md), essa propriedade `Duration` é somente leitura. É definida com uma `BindablePropertyKey` privada e pode ser definida apenas referenciando a interface `IVideoPlayerController`, que inclui essa propriedade `Duration`:

```csharp
namespace FormsVideoLibrary
{
    public interface IVideoPlayerController
    {
        VideoStatus Status { set; get; }

        TimeSpan Duration { set; get; }
    }
}
```

Além disso, observe o manipulador de propriedade alterada que chama um método chamado `SetTimeToEnd` descrito posteriormente neste artigo.

A duração de um vídeo *não* está disponível imediatamente depois que a propriedade `Source` de `VideoPlayer` for definida. O arquivo de vídeo deve ser baixado parcialmente antes que o player de vídeo subjacente possa determinar sua duração.

Veja como cada um dos renderizadores de plataforma obtém a duração do vídeo:

### <a name="video-duration-in-ios"></a>Duração do vídeo no iOS

No iOS, a duração de um vídeo é obtida com base na propriedade `Duration` de `AVPlayerItem`, mas não imediatamente após a criação de `AVPlayerItem`. É possível definir um observador de iOS para a propriedade `Duration`, mas o `VideoPlayerRenderer` obtém a duração no método `UpdateStatus`, chamado de 10 vezes por segundo:

```csharp
namespace FormsVideoLibrary.iOS
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, UIView>
    {
        ···
        void OnUpdateStatus(object sender, EventArgs args)
        {
            ···
            if (playerItem != null)
            {
                ((IVideoPlayerController)Element).Duration = ConvertTime(playerItem.Duration);
                ···
            }
        }

        TimeSpan ConvertTime(CMTime cmTime)
        {
            return TimeSpan.FromSeconds(Double.IsNaN(cmTime.Seconds) ? 0 : cmTime.Seconds);

        }
        ···
    }
}
```

O método `ConvertTime` converte um objeto `CMTime` em um `TimeSpan` valor.

### <a name="video-duration-in-android"></a>Duração do vídeo no Android

A propriedade `Duration` do Android `VideoView` relata uma duração válida em milissegundos quando o evento `Prepared` de `VideoView` é acionado. A classe `VideoPlayerRenderer` do Android usa esse manipulador para obter a propriedade `Duration`:

```csharp
namespace FormsVideoLibrary.Droid
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, ARelativeLayout>
    {
        ···
        void OnVideoViewPrepared(object sender, EventArgs args)
        {
            ···
            ((IVideoPlayerController)Element).Duration = TimeSpan.FromMilliseconds(videoView.Duration);
        }
        ···
    }
}
```

### <a name="video-duration-in-uwp"></a>Duração do vídeo na UWP

A propriedade `NaturalDuration` de `MediaElement` é um valor `TimeSpan` e torna-se válida quando `MediaElement` aciona o evento `MediaOpened`:

```csharp
namespace FormsVideoLibrary.UWP
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, MediaElement>
    {
        ···
        void OnMediaElementMediaOpened(object sender, RoutedEventArgs args)
        {
            ((IVideoPlayerController)Element).Duration = Control.NaturalDuration.TimeSpan;
        }
        ···
    }
}
```

## <a name="the-position-property"></a>A propriedade Position

`VideoPlayer` também precisa de uma propriedade `Position` que aumenta de zero a `Duration` conforme o vídeo é reproduzido. `VideoPlayer` implementa essa propriedade como a propriedade `Position` no `MediaElement` UWP, que é uma propriedade associável normal com acessadores públicos `set` e `get`:

```csharp
namespace FormsVideoLibrary
{
    public class VideoPlayer : View, IVideoPlayerController
    {
        ···
        // Position property
        public static readonly BindableProperty PositionProperty =
            BindableProperty.Create(nameof(Position), typeof(TimeSpan), typeof(VideoPlayer), new TimeSpan(),
                propertyChanged: (bindable, oldValue, newValue) => ((VideoPlayer)bindable).SetTimeToEnd());

        public TimeSpan Position
        {
            set { SetValue(PositionProperty, value); }
            get { return (TimeSpan)GetValue(PositionProperty); }
        }
        ···
    }
}
```

O acessador `get` retorna a posição atual do vídeo quando ele está sendo reproduzido, mas o acessador `set` é projetado para responder à manipulação do usuário da barra de posição movendo a posição de vídeo para frente ou para trás.

No iOS e Android, a propriedade que obtém a posição atual tem apenas um acessador `get`, e um método `Seek` está disponível para executar essa segunda tarefa. Se você pensar a respeito, um método `Seek` separado parecerá ser uma abordagem mais adequada do que uma única propriedade `Position`. Uma única propriedade `Position` tem um problema inerente: durante a reprodução do vídeo, a propriedade `Position` deve ser continuamente atualizada para refletir a nova posição. Mas você não quer que a maioria das alterações à propriedade `Position` faça o player de vídeo se mover para uma nova posição no vídeo. Se isso acontecesse, o player de vídeo responderia buscando o último valor da propriedade `Position` e o vídeo não avançaria.

Apesar das dificuldades de implementar uma propriedade `Position` com os acessadores `set` e `get`, essa abordagem foi escolhida porque é consistente com o `MediaElement` da UWP e ela tem uma grande vantagem relacionada à associação de dados: a propriedade `Position` do `VideoPlayer` pode ser associado ao controle deslizante usado tanto para exibir a posição quanto para buscar uma nova posição. No entanto, várias precauções são necessárias ao implementar essa propriedade `Position` para evitar loops de comentários.

### <a name="setting-and-getting-ios-position"></a>Definir e obter a posição do iOS

No iOS, a propriedade `CurrentTime` do objeto `AVPlayerItem` indica a posição atual do vídeo sendo reproduzido. O iOS `VideoPlayerRenderer` define a propriedade `Position` no manipulador `UpdateStatus` ao mesmo tempo em que ele define a propriedade `Duration`:

```csharp
namespace FormsVideoLibrary.iOS
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, UIView>
    {
        ···
        void OnUpdateStatus(object sender, EventArgs args)
        {
            ···
            if (playerItem != null)
            {
                ···
                ((IElementController)Element).SetValueFromRenderer(VideoPlayer.PositionProperty, ConvertTime(playerItem.CurrentTime));
            }
        }
        ···
    }
}
```

O renderizador detecta quando o conjunto de propriedades `Position` de `VideoPlayer` foi alterado na substituição do `OnElementPropertyChanged`, e usa esse novo valor para chamar um método `Seek` no objeto `AVPlayer`:

```csharp
namespace FormsVideoLibrary.iOS
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, UIView>
    {
        ···
        protected override void OnElementPropertyChanged(object sender, PropertyChangedEventArgs args)
        {
            ···
            else if (args.PropertyName == VideoPlayer.PositionProperty.PropertyName)
            {
                TimeSpan controlPosition = ConvertTime(player.CurrentTime);

                if (Math.Abs((controlPosition - Element.Position).TotalSeconds) > 1)
                {
                    player.Seek(CMTime.FromSeconds(Element.Position.TotalSeconds, 1));
                }
            }
        }
        ···
    }
}
```

Tenha em mente que sempre que a propriedade `Position` no `VideoPlayer` for definida com base no manipulador `OnUpdateStatus`, a propriedade `Position` disparará um evento `PropertyChanged`, detectado na substituição do `OnElementPropertyChanged`. Para a maioria dessas alterações, o método `OnElementPropertyChanged` não deve fazer nada. Caso contrário, a cada alteração na posição do vídeo, ele seria ser movido para a mesma posição que ela acabou de atingir!

Para evitar esse loop de comentários, o método `OnElementPropertyChanged` apenas chamará `Seek` quando a diferença entre a propriedade `Position` e a posição atual do `AVPlayer` for maior que um segundo.

### <a name="setting-and-getting-android-position"></a>Definir e obter a posição do Android

Como acontece no renderizador do iOS, o `VideoPlayerRenderer` do Android define um novo valor para a propriedade `Position` no manipulador `OnUpdateStatus`. A propriedade `CurrentPosition` de `VideoView` contém a nova posição em unidades de milissegundos:

```csharp
namespace FormsVideoLibrary.Droid
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, ARelativeLayout>
    {
        ···
        void OnUpdateStatus(object sender, EventArgs args)
        {
            ···
            TimeSpan timeSpan = TimeSpan.FromMilliseconds(videoView.CurrentPosition);
            ((IElementController)Element).SetValueFromRenderer(VideoPlayer.PositionProperty, timeSpan);
        }
        ···
    }
}
```

Além disso, como acontece no renderizador do iOS, o renderizador do Android chamará o método `SeekTo` de `VideoView` quando a propriedade `Position` tiver sido alterada, mas somente quando a alteração for mais de um segundo diferente do valor `CurrentPosition` de `VideoView`:

```csharp
namespace FormsVideoLibrary.Droid
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, ARelativeLayout>
    {
        ···
        protected override void OnElementPropertyChanged(object sender, PropertyChangedEventArgs args)
        {
            ···
            else if (args.PropertyName == VideoPlayer.PositionProperty.PropertyName)
            {
                if (Math.Abs(videoView.CurrentPosition - Element.Position.TotalMilliseconds) > 1000)
                {
                    videoView.SeekTo((int)Element.Position.TotalMilliseconds);
                }
            }
        }
        ···
    }
}
```

### <a name="setting-and-getting-uwp-position"></a>Definir e obter a posição da UWP

O `VideoPlayerRenderer` da UWP manipula o `Position` da mesma maneira que os renderizadores do iOS e do Android, mas, como a propriedade `Position` do `MediaElement` da UWP também é um valor `TimeSpan`, não é necessária nenhuma conversão:

```csharp
namespace FormsVideoLibrary.UWP
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, MediaElement>
    {
        ···
        protected override void OnElementPropertyChanged(object sender, PropertyChangedEventArgs args)
        {
            ···
            else if (args.PropertyName == VideoPlayer.PositionProperty.PropertyName)
            {
                if (Math.Abs((Control.Position - Element.Position).TotalSeconds) > 1)
                {
                    Control.Position = Element.Position;
                }
            }
        }
        ···
        void OnUpdateStatus(object sender, EventArgs args)
        {
            ((IElementController)Element).SetValueFromRenderer(VideoPlayer.PositionProperty, Control.Position);
        }
        ···
    }
}
```

## <a name="calculating-a-timetoend-property"></a>Calculando uma propriedade TimeToEnd

Às vezes, os players de vídeo mostram o tempo restante no vídeo. Esse valor começa na duração do vídeo quando o vídeo é iniciado e diminui para zero quando o vídeo é encerrado.

`VideoPlayer` inclui uma propriedade `TimeToEnd` somente leitura que é manipulada inteiramente dentro da classe com base nas alterações às propriedades `Duration` e `Position`:

```csharp
namespace FormsVideoLibrary
{
    public class VideoPlayer : View, IVideoPlayerController
    {
        ···
        private static readonly BindablePropertyKey TimeToEndPropertyKey =
            BindableProperty.CreateReadOnly(nameof(TimeToEnd), typeof(TimeSpan), typeof(VideoPlayer), new TimeSpan());

        public static readonly BindableProperty TimeToEndProperty = TimeToEndPropertyKey.BindableProperty;

        public TimeSpan TimeToEnd
        {
            private set { SetValue(TimeToEndPropertyKey, value); }
            get { return (TimeSpan)GetValue(TimeToEndProperty); }
        }

        void SetTimeToEnd()
        {
            TimeToEnd = Duration - Position;
        }
        ···
    }
}
```

O método `SetTimeToEnd` é chamado dos manipuladores de propriedade alterada de `Duration` e de `Position`.

## <a name="a-custom-slider-for-video"></a>Um controle deslizante personalizado para vídeo

É possível escrever um controle personalizado para uma barra de posição ou usar o Xamarin.Forms `Slider` ou uma classe derivada de `Slider` , como a classe a seguir `PositionSlider` . A classe define duas novas propriedades denominadas `Duration` e `Position` do tipo `TimeSpan` que se destinam a ser associadas às duas propriedades de mesmo nome no `VideoPlayer`. Observe que o modo de associação padrão da propriedade `Position` é bidirecional:

```csharp
namespace FormsVideoLibrary
{
    public class PositionSlider : Slider
    {
        public static readonly BindableProperty DurationProperty =
            BindableProperty.Create(nameof(Duration), typeof(TimeSpan), typeof(PositionSlider), new TimeSpan(1),
                                    propertyChanged: (bindable, oldValue, newValue) =>
                                    {
                                        double seconds = ((TimeSpan)newValue).TotalSeconds;
                                        ((PositionSlider)bindable).Maximum = seconds <= 0 ? 1 : seconds;
                                    });

        public TimeSpan Duration
        {
            set { SetValue(DurationProperty, value); }
            get { return (TimeSpan)GetValue(DurationProperty); }
        }

        public static readonly BindableProperty PositionProperty =
            BindableProperty.Create(nameof(Position), typeof(TimeSpan), typeof(PositionSlider), new TimeSpan(0),
                                    defaultBindingMode: BindingMode.TwoWay,
                                    propertyChanged: (bindable, oldValue, newValue) =>
                                    {
                                        double seconds = ((TimeSpan)newValue).TotalSeconds;
                                        ((PositionSlider)bindable).Value = seconds;
                                    });

        public TimeSpan Position
        {
            set { SetValue(PositionProperty, value); }
            get { return (TimeSpan)GetValue(PositionProperty); }
        }

        public PositionSlider()
        {
            PropertyChanged += (sender, args) =>
            {
                if (args.PropertyName == "Value")
                {
                    TimeSpan newPosition = TimeSpan.FromSeconds(Value);

                    if (Math.Abs(newPosition.TotalSeconds - Position.TotalSeconds) / Duration.TotalSeconds > 0.01)
                    {
                        Position = newPosition;
                    }
                }
            };
        }
    }
}
```

O manipulador de propriedade alterada dos conjuntos de propriedades `Duration` define a propriedade `Maximum` do `Slider` subjacente como a propriedade `TotalSeconds` do valor `TimeSpan`. Da mesma forma, o manipulador de propriedade alterada de `Position` define a propriedade `Value` do `Slider`. Dessa forma, o `Slider` subjacente controla a posição do `PositionSlider`.

O `PositionSlider` é atualizado do `Slider` subjacente em apenas uma instância: quando o usuário manipula o `Slider` para indicar que o vídeo deve ser avançado ou revertido para uma nova posição. Isso é detectado no manipulador `PropertyChanged` no construtor do `PositionSlider`. O manipulador verifica uma alteração na propriedade `Value` e, se for diferente da propriedade `Position`, então a propriedade `Position` será definida com base na propriedade `Value`.

Em teoria, a instrução `if` interna poderia ser escrita da seguinte forma:

```csharp
if (newPosition.Seconds != Position.Seconds)
{
    Position = newPosition;
}
```

No entanto, a implementação do Android de `Slider` tem apenas 1.000 etapas distintas, independentemente das configurações do `Minimum` e do `Maximum`. Se o comprimento de um vídeo for maior que 1.000 segundos, os dois valores `Position` diferentes corresponderá à mesma configuração `Value` do `Slider`, e essa instrução `if` dispararia um falso positivo para a manipulação de um usuário do `Slider`. É mais seguro verificar se a nova posição e a existente são maiores do que um centésimo da duração geral.

## <a name="using-the-positionslider"></a>Usando o PositionSlider

A documentação do UWP [`MediaElement`](/uwp/api/Windows.UI.Xaml.Controls.MediaElement/) avisa sobre a associação à `Position` propriedade porque a propriedade é atualizada com frequência. A documentação recomenda que um temporizador seja usado para consultar a propriedade `Position`.

É uma boa recomendação, mas as três classes `VideoPlayerRenderer` já estão usando indiretamente um temporizador para atualizar a propriedade `Position`. A propriedade `Position` é alterada em um manipulador para o evento `UpdateStatus`, que é disparado apenas 10 vezes por segundo.

Portanto, a propriedade `Position` do `VideoPlayer` pode ser associada à propriedade `Position` do `PositionSlider` sem problemas de desempenho, conforme demonstrado na página **Barra de Posição Personalizada**:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:video="clr-namespace:FormsVideoLibrary"
             x:Class="VideoPlayerDemos.CustomPositionBarPage"
             Title="Custom Position Bar">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="*" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
        </Grid.RowDefinitions>

        <video:VideoPlayer x:Name="videoPlayer"
                           Grid.Row="0"
                           AreTransportControlsEnabled="False"
                           Source="{StaticResource ElephantsDream}" />

        ···

        <StackLayout Grid.Row="1"
                     Orientation="Horizontal"
                     Margin="10, 0"
                     BindingContext="{x:Reference videoPlayer}">

            <Label Text="{Binding Path=Position,
                                  StringFormat='{0:hh\\:mm\\:ss}'}"
                   VerticalOptions="Center"/>

            ···

            <Label Text="{Binding Path=TimeToEnd,
                                  StringFormat='{0:hh\\:mm\\:ss}'}"
                   VerticalOptions="Center" />
        </StackLayout>

        <video:PositionSlider Grid.Row="2"
                              Margin="10, 0, 10, 10"
                              BindingContext="{x:Reference videoPlayer}"
                              Duration="{Binding Duration}"           
                              Position="{Binding Position}">
            <video:PositionSlider.Triggers>
                <DataTrigger TargetType="video:PositionSlider"
                             Binding="{Binding Status}"
                             Value="{x:Static video:VideoStatus.NotReady}">
                    <Setter Property="IsEnabled" Value="False" />
                </DataTrigger>
            </video:PositionSlider.Triggers>
        </video:PositionSlider>
    </Grid>
</ContentPage>
```

As primeiras reticências (···) ocultam o `ActivityIndicator`; é o mesmo da página **Transporte Personalizada** anterior. Observe os dois elementos `Label` que exibem as propriedades `Position` e `TimeToEnd`. As reticências entre esses dois elementos `Label` oculta os dois elementos `Button` mostrados na página **Transporte Personalizado** para executar, pausar e parar. A lógica code-behind também é a mesma da página **Transporte Personalizado**.

[![Posicionamento personalizado](custom-positioning-images/custompositioning-small.png "Posicionamento personalizado")](custom-positioning-images/custompositioning-large.png#lightbox "Posicionamento personalizado")

Isso conclui a discussão do `VideoPlayer`.

## <a name="related-links"></a>Links Relacionados

- [Demonstrações do player de vídeo (exemplo)](/samples/xamarin/xamarin-forms-samples/customrenderers-videoplayerdemos)