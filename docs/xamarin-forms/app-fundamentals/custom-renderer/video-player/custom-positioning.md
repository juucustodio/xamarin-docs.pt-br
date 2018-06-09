---
title: Posicionamento de vídeo personalizado
description: Este artigo explica como implementar uma barra de posição personalizada em um aplicativo de player de vídeo, usando o xamarin. Forms.
ms.prod: xamarin
ms.assetid: 6D792264-30FF-46F7-8C1B-2FEF9D277DF4
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/12/2018
ms.openlocfilehash: b5f3c9dcbaa6ba1a9e86568ccabe38416cc653f2
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/08/2018
ms.locfileid: "35241904"
---
# <a name="custom-video-positioning"></a>Posicionamento de vídeo personalizado

Os controles de transporte implementados por cada plataforma incluem uma barra de posição. Essa barra é semelhante a um controle deslizante ou a barra de rolagem e mostra a localização atual do vídeo dentro de sua duração total. Além disso, o usuário pode manipular a barra de posição para frente ou para trás para uma nova posição no vídeo.

Este artigo mostra como você pode implementar sua própria barra de posição personalizada.

## <a name="the-duration-property"></a>A propriedade Duration

Um item de informação que `VideoPlayer` precisa dar suporte a um personalizado barra de posição é a duração do vídeo. O `VideoPlayer` define somente leitura `Duration` propriedade do tipo `TimeSpan`:

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

Como o `Status` propriedade descrito o [artigo anterior](custom-transport.md), este `Duration` propriedade é somente leitura. Ela é definida com uma particular `BindablePropertyKey` e só pode ser definida por referência a `IVideoPlayerController` interface, que inclui esse `Duration` propriedade:

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

Observe também o manipulador de propriedade alterada que chama um método chamado `SetTimeToEnd` que é descrita posteriormente neste artigo.

A duração de um vídeo é *não* disponíveis imediatamente após o `Source` propriedade `VideoPlayer` está definida. O arquivo de vídeo deve ser parcialmente baixado antes que o player de vídeo subjacente pode determinar sua duração.

Aqui está como cada um dos processadores de plataforma obtém a duração do vídeo:

### <a name="video-duration-in-ios"></a>Duração de vídeo no iOS

No iOS, a duração de um vídeo é obtida a `Duration` propriedade de `AVPlayerItem`, mas não imediatamente após o `AVPlayerItem` é criado. É possível definir um observador do iOS para o `Duration` propriedade, mas o `VideoPlayerRenderer` obtém a duração no `UpdateStatus` método, que é chamado de 10 vezes por segundo:

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

O `ConvertTime` método converte um `CMTime` o objeto para um `TimeSpan` valor.

### <a name="video-duration-in-android"></a>Duração de vídeo no Android

O `Duration` propriedade o Android `VideoView` informa uma duração válida em milissegundos quando o `Prepared` eventos de `VideoView` é acionado. O Android `VideoPlayerRenderer` classe usa esse manipulador para obter o `Duration` propriedade:

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

### <a name="video-duration-in-uwp"></a>Duração do vídeo em UWP

O `NaturalDuration` propriedade de `MediaElement` é um `TimeSpan` valor e torna-se válida quando `MediaElement` dispara o `MediaOpened` evento:

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

`VideoPlayer` também precisa de um `Position` que aumenta de zero a propriedade `Duration` como a reprodução do vídeo. `VideoPlayer` implementa essa propriedade como o `Position` propriedade o UWP `MediaElement`, que é uma propriedade ligável normal com público `set` e `get` acessadores:

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

O `get` acessador retorna a posição atual do vídeo, pois ele está em execução, mas o `set` acessador destina-se para responder a manipulação do usuário da barra de posição movendo a posição do vídeo para a frente ou para trás.

No iOS e Android, a propriedade que obtém a posição atual tem apenas um `get` acessador e um `Seek` método está disponível para executar esta tarefa de segundo. Se você pensar nisso, um separado `Seek` método parece ser uma abordagem mais adequada do que um único `Position` propriedade. Um único `Position` propriedade tem um problema inerente: durante a reprodução do vídeo, o `Position` propriedade deverá ser continuamente atualizada para refletir a nova posição. Mas não deseja que a maioria das alterações para o `Position` propriedade para fazer com que o player de vídeo mover para uma nova posição no vídeo. Se isso acontecer, o player de vídeo deve responder busca para o último valor de `Position` não adiantar a propriedade e o vídeo.

Apesar das dificuldades de implementação de um `Position` propriedade com `set` e `get` acessadores, essa abordagem foi escolhida porque ele é consistente com o UWP `MediaElement`, e ele tem uma grande vantagem com associação de dados: O `Position` propriedade de `VideoPlayer` podem ser associados para o controle deslizante que é usado para exibir a posição e a busca para uma nova posição. No entanto, várias precauções são necessárias ao implementar isso `Position` propriedade para evitar loops de comentários.

### <a name="setting-and-getting-ios-position"></a>Configuração e a obtenção de posição de iOS

No iOS, o `CurrentTime` propriedade o `AVPlayerItem` objeto indica a posição atual de reprodução de vídeo. O iOS `VideoPlayerRenderer` define o `Position` propriedade o `UpdateStatus` manipulador ao mesmo tempo que define o `Duration` propriedade:

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

O renderizador detecta quando o `Position` conjunto de propriedades `VideoPlayer` foi alterado no `OnElementPropertyChanged` substituir e usa esse novo valor para chamar um `Seek` método no `AVPlayer` objeto:

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

Tenha em mente que sempre o `Position` propriedade no `VideoPlayer` é definido do `OnUpdateStatus` manipulador, o `Position` propriedade dispara um `PropertyChanged` evento, que é detectado no `OnElementPropertyChanged` substituir. Para a maioria dessas alterações, o `OnElementPropertyChanged` método não deve fazer nada. Caso contrário, com cada alteração na posição do vídeo, ele será movido para a mesma posição atingiu apenas!

Para evitar este loop de comentários, o `OnElementPropertyChanged` método só chama `Seek` quando a diferença entre o `Position` propriedade e a posição atual do `AVPlayer` é maior do que um segundo.

### <a name="setting-and-getting-android-position"></a>Definindo e Obtendo posição Android

Assim como o renderizador do iOS, o Android `VideoPlayerRenderer` define um novo valor para o `Position` propriedade o `OnUpdateStatus` manipulador. O `CurrentPosition` propriedade `VideoView` contém a nova posição em unidades de milissegundos:

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

Além disso, assim como o renderizador do iOS, Android renderizador chama o `SeekTo` método de `VideoView` quando o `Position` propriedade foi alterada, mas somente quando a alteração é diferente de mais de um segundo a `CurrentPosition` valor `VideoView`:

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

### <a name="setting-and-getting-uwp-position"></a>Configuração e a obtenção de posição de UWP

O UWP `VideoPlayerRenderer` identificadores o `Position` da mesma maneira como o iOS e Android renderizadores, mas como o `Position` propriedade o UWP `MediaElement` também é um `TimeSpan` valor, nenhuma conversão é necessária:

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

Players de vídeo, às vezes, mostram o tempo restante no vídeo. Esse valor começa a duração do vídeo quando o vídeo começa e diminui para zero quando o vídeo termina.

`VideoPlayer` inclui somente leitura `TimeToEnd` propriedade que é manipulada inteiramente dentro da classe com base nas alterações de `Duration` e `Position` propriedades:

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

O `SetTimeToEnd` método é chamado de manipuladores de propriedade alterada de ambos `Duration` e `Position`.

## <a name="a-custom-slider-for-video"></a>Um controle deslizante personalizado para vídeo

É possível gravar um controle personalizado de uma barra de posição, ou usar o xamarin. Forms `Slider` ou uma classe que deriva de `Slider`, como o seguinte `PositionSlider` classe. A classe define duas novas propriedades denominadas `Duration` e `Position` do tipo `TimeSpan` que se destinam a ser associado a dados para as duas propriedades de mesmo nome no `VideoPlayer`. Observe que o padrão de associação de modo do `Position` propriedade é bidirecional:

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

O manipulador de propriedade alterada para o `Duration` conjuntos de propriedades a `Maximum` propriedade subjacente `Slider` para o `TotalSeconds` propriedade o `TimeSpan` valor. Da mesma forma, o manipulador de propriedade alterada para `Position` define o `Value` propriedade o `Slider`. Dessa forma, subjacente `Slider` controla a posição do `PositionSlider`.

O `PositionSlider` é atualizado de subjacente `Slider` em apenas uma instância: quando o usuário manipula a `Slider` para indicar que o vídeo deve ser avançado ou revertido para uma nova posição. Isso é detectado no `PropertyChanged` manipulador no construtor do `PositionSlider`. O manipulador verifica uma alteração no `Value` propriedade, e se ele for diferente de `Position` propriedade, o `Position` propriedade é definida do `Value` propriedade.

Em teoria, interna `if` instrução poderia ser escrita como este:

```csharp
if (newPosition.Seconds != Position.Seconds)
{
    Position = newPosition;
}
```

No entanto, a implementação do Android do `Slider` tem apenas 1.000 etapas distintas, independentemente do `Minimum` e `Maximum` configurações. O comprimento de um vídeo é maior que 1.000 segundos, em seguida, dois diferentes `Position` valores corresponde à mesma `Value` configuração do `Slider`e isso `if` instrução irá disparar um falso positivo para a manipulação de um usuário de o `Slider`. É mais seguro em vez disso, verifique que a nova posição e a posição existente são maiores que um-centésimos da duração total.

## <a name="using-the-positionslider"></a>Usando o PositionSlider

Documentação para a UWP [ `MediaElement` ](/uwp/api/Windows.UI.Xaml.Controls.MediaElement/) avisa sobre a associação para o `Position` propriedade porque a propriedade atualiza com frequência. A documentação recomenda que um timer de ser usada para consultar o `Position` propriedade.

Essa é uma recomendação válida, mas as três `VideoPlayerRenderer` classes indiretamente já estiver usando um timer para atualizar o `Position` propriedade. O `Position` propriedade é alterada em um manipulador para o `UpdateStatus` evento, que é disparado somente 10 vezes por segundo.

Portanto, o `Position` propriedade do `VideoPlayer` podem ser associados ao `Position` propriedade do `PositionSlider` sem problemas de desempenho, conforme demonstrado no **barra de posição personalizada** página:

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

No primeiro botão de reticências (...) oculta o `ActivityIndicator`; é igual ao anterior **transporte personalizadas** página. Observe os dois `Label` elementos exibindo o `Position` e `TimeToEnd` propriedades. No botão de reticências entre esses dois `Label` elementos oculta os dois `Button` elementos mostrados na **transporte personalizadas** página para executar, pausar e parar. A lógica por trás do código também é o mesmo que o **transporte personalizadas** página.

[![Posicionamento personalizado](custom-positioning-images/custompositioning-small.png "posicionamento personalizado")](custom-positioning-images/custompositioning-large.png#lightbox "posicionamento personalizado")

Isso conclui a discussão sobre o `VideoPlayer`.

## <a name="related-links"></a>Links relacionados

- [Demonstrações em vídeo Player (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/VideoPlayerDemos/)
