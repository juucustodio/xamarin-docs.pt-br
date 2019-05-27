---
title: Controles personalizados de transporte de vídeo
description: Este artigo explica como implementar controles de transporte personalizados em um aplicativo de player de vídeo, usando o Xamarin.Forms.
ms.prod: xamarin
ms.assetid: CE9E955D-A9AC-4019-A5D7-6390D80DECA1
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/12/2018
ms.openlocfilehash: 3e5db00a73047aaadb1162fa1cc8a21f6e77b3a0
ms.sourcegitcommit: b23a107b0fe3d2f814ae35b52a5855b6ce2a3513
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/20/2019
ms.locfileid: "65926201"
---
# <a name="custom-video-transport-controls"></a>Controles personalizados de transporte de vídeo

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://developer.xamarin.com/samples/xamarin-forms/CustomRenderers/VideoPlayerDemos/)

Os controles de transporte de um player de vídeo incluem os botões que executam as funções **Reproduzir**, **Pausar** e **Parar**. Em geral, esses botões são identificados com ícones conhecidos, em vez de um texto, e as funções **Reproduzir** e **Pausar** geralmente são combinadas em um botão.

Por padrão, o `VideoPlayer` exibe controles de transporte compatíveis com cada plataforma. Quando você define a propriedade `AreTransportControlsEnabled` como `false`, esses controles são suprimidos. Em seguida, você pode controlar o `VideoPlayer` de forma programática ou fornecer seus próprios controles de transporte.

## <a name="the-play-pause-and-stop-methods"></a>Os métodos Play, Pause e Stop

A classe `VideoPlayer` define três métodos chamados `Play`, `Pause` e `Stop` que são implementados pelo acionamento de eventos:

```csharp
namespace FormsVideoLibrary
{
    public class VideoPlayer : View, IVideoPlayerController
    {
        ···
        public event EventHandler PlayRequested;

        public void Play()
        {
            PlayRequested?.Invoke(this, EventArgs.Empty);
        }

        public event EventHandler PauseRequested;

        public void Pause()
        {
            PauseRequested?.Invoke(this, EventArgs.Empty);
        }

        public event EventHandler StopRequested;

        public void Stop()
        {
            StopRequested?.Invoke(this, EventArgs.Empty);
        }
    }
}
```

Os manipuladores de eventos para esses eventos são definidos pela classe `VideoPlayerRenderer` em cada plataforma, conforme mostrado abaixo:

### <a name="ios-transport-implementations"></a>Implementações de transporte do iOS

A versão do iOS de `VideoPlayerRenderer` usa o método `OnElementChanged` para definir manipuladores para esses três eventos quando a propriedade `NewElement` não é `null` e desanexa os manipuladores de eventos quando `OldElement` não é `null`:

```csharp
namespace FormsVideoLibrary.iOS
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, UIView>
    {
        AVPlayer player;
        ···
        protected override void OnElementChanged(ElementChangedEventArgs<VideoPlayer> args)
        {
            ···
            if (args.NewElement != null)
            {
                ···
                args.NewElement.PlayRequested += OnPlayRequested;
                args.NewElement.PauseRequested += OnPauseRequested;
                args.NewElement.StopRequested += OnStopRequested;
            }

            if (args.OldElement != null)
            {
                ···
                args.OldElement.PlayRequested -= OnPlayRequested;
                args.OldElement.PauseRequested -= OnPauseRequested;
                args.OldElement.StopRequested -= OnStopRequested;
            }
        }
        ···
        // Event handlers to implement methods
        void OnPlayRequested(object sender, EventArgs args)
        {
            player.Play();
        }

        void OnPauseRequested(object sender, EventArgs args)
        {
            player.Pause();
        }

        void OnStopRequested(object sender, EventArgs args)
        {
            player.Pause();
            player.Seek(new CMTime(0, 1));
        }
    }
}
```

Os manipuladores de eventos são implementados com uma chamada aos métodos no objeto `AVPlayer`. Não há nenhum método `Stop` para `AVPlayer` e, portanto, ele é simulado com a pausa do vídeo e movimentação da posição para o início.

### <a name="android-transport-implementations"></a>Implementações de transporte do Android

A implementação do Android é semelhante à implementação do iOS. Os manipuladores para as três funções são definidos quando `NewElement` não é `null` e é desanexado quando `OldElement` não é `null`:

```csharp
namespace FormsVideoLibrary.Droid
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, ARelativeLayout>
    {
        VideoView videoView;
        ···
        protected override void OnElementChanged(ElementChangedEventArgs<VideoPlayer> args)
        {
            ···
            if (args.NewElement != null)
            {
                ···
                args.NewElement.PlayRequested += OnPlayRequested;
                args.NewElement.PauseRequested += OnPauseRequested;
                args.NewElement.StopRequested += OnStopRequested;
            }

            if (args.OldElement != null)
            {
                ···
                args.OldElement.PlayRequested -= OnPlayRequested;
                args.OldElement.PauseRequested -= OnPauseRequested;
                args.OldElement.StopRequested -= OnStopRequested;
            }
        }
        ···
        void OnPlayRequested(object sender, EventArgs args)
        {
            videoView.Start();
        }

        void OnPauseRequested(object sender, EventArgs args)
        {
            videoView.Pause();
        }

        void OnStopRequested(object sender, EventArgs args)
        {
            videoView.StopPlayback();
        }
    }
}
```

As três funções chamam os métodos definidos pela `VideoView`.

### <a name="uwp-transport-implementations"></a>Implementações de transporte do UWP

A implementação do UWP das três funções de transporte é muito semelhante às implementações do iOS e do Android:

```csharp
namespace FormsVideoLibrary.UWP
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, MediaElement>
    {
        protected override void OnElementChanged(ElementChangedEventArgs<VideoPlayer> args)
        {
            ···
            if (args.NewElement != null)
            {
                ···
                args.NewElement.PlayRequested += OnPlayRequested;
                args.NewElement.PauseRequested += OnPauseRequested;
                args.NewElement.StopRequested += OnStopRequested;
            }

            if (args.OldElement != null)
            {
                ···
                args.OldElement.PlayRequested -= OnPlayRequested;
                args.OldElement.PauseRequested -= OnPauseRequested;
                args.OldElement.StopRequested -= OnStopRequested;
            }
        }
        ···
        // Event handlers to implement methods
        void OnPlayRequested(object sender, EventArgs args)
        {
            Control.Play();
        }

        void OnPauseRequested(object sender, EventArgs args)
        {
            Control.Pause();
        }

        void OnStopRequested(object sender, EventArgs args)
        {
            Control.Stop();
        }
    }
}
```

## <a name="the-video-player-status"></a>O status do player de vídeo

A implementação das funções **Reproduzir**, **Pausar** e **Parar** não é suficiente para dar suporte aos controles de transporte. Muitas vezes, os comandos **Reproduzir** e **Pausar** são implementados com o mesmo botão que muda de aparência para indicar se o vídeo ainda está execução ou em pausa. Além disso, o botão nem deverá ser habilitado se o vídeo ainda não tiver sido carregado.

Esses requisitos implicam que o player de vídeo precisa disponibilizar um status atual que indica se ele está reproduzindo um vídeo ou em pausa ou se ele ainda não está pronto para reproduzir um vídeo. (Cada plataforma também dá suporte a propriedades que indicam se o vídeo pode ser colocado em pausa ou pode ser movido para uma nova posição, mas essas propriedades são aplicáveis para transmissão de vídeo, em vez de arquivos de vídeo. Portanto, não há suporte para elas no `VideoPlayer` descrito aqui.)

O projeto **VideoPlayerDemos** inclui uma enumeração `VideoStatus` com três membros:

```csharp
namespace FormsVideoLibrary
{
    public enum VideoStatus
    {
        NotReady,
        Playing,
        Paused
    }
}
```

A classe `VideoPlayer` define uma propriedade associável somente real chamada `Status` do tipo `VideoStatus`. Essa propriedade é definida como somente leitura porque só deve ser definida no renderizador da plataforma:

```csharp
using System;
using Xamarin.Forms;

namespace FormsVideoLibrary
{
    public class VideoPlayer : View, IVideoPlayerController
    {
        ···
        // Status read-only property
        private static readonly BindablePropertyKey StatusPropertyKey =
            BindableProperty.CreateReadOnly(nameof(Status), typeof(VideoStatus), typeof(VideoPlayer), VideoStatus.NotReady);

        public static readonly BindableProperty StatusProperty = StatusPropertyKey.BindableProperty;

        public VideoStatus Status
        {
            get { return (VideoStatus)GetValue(StatusProperty); }
        }

        VideoStatus IVideoPlayerController.Status
        {
            set { SetValue(StatusPropertyKey, value); }
            get { return Status; }
        }
        ···
    }
}
```

Normalmente, uma propriedade associável somente leitura terá um acessador `set` particular na propriedade `Status` para permitir que ela seja definida na classe. No entanto, para um derivado de `View` compatível com renderizadores, a propriedade precisa ser definida fora da classe, mas apenas pelo renderizador da plataforma.

Por esse motivo, outra propriedade é definida com o nome `IVideoPlayerController.Status`. Essa é uma implementação explícita da interface e foi possibilitada pela interface `IVideoPlayerController` implementada pela classe `VideoPlayer`:

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

Isso é semelhante ao modo como o controle [`WebView`](xref:Xamarin.Forms.WebView) usa a interface [`IWebViewController`](xref:Xamarin.Forms.IWebViewController) para implementar as propriedades `CanGoBack` e `CanGoForward`. (Confira o código-fonte de [`WebView`](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Core/WebView.cs) e seus renderizadores para obter detalhes.)

Isso possibilita que uma classe externa a `VideoPlayer` defina a propriedade `Status` referenciando a interface `IVideoPlayerController`. (Você verá o código em breve.) A propriedade pode ser definida em outras classes também, mas é improvável que ela seja definida inadvertidamente. O mais importante é que a propriedade `Status` não pode ser definida por meio de uma associação de dados.

Para auxiliar os renderizadores a manter essa propriedade `Status` atualizada, a classe `VideoPlayer` define um evento `UpdateStatus` que é disparado a cada décimo de segundo:

```csharp
namespace FormsVideoLibrary
{
    public class VideoPlayer : View, IVideoPlayerController
    {
        public event EventHandler UpdateStatus;

        public VideoPlayer()
        {
            Device.StartTimer(TimeSpan.FromMilliseconds(100), () =>
            {
                UpdateStatus?.Invoke(this, EventArgs.Empty);
                return true;
            });
        }
        ···
    }
}
```

### <a name="the-ios-status-setting"></a>A configuração de status do iOS

O `VideoPlayerRenderer` do iOS define um manipulador para o evento `UpdateStatus` (e desanexa o manipulador quando o elemento `VideoPlayer` subjacente está ausente) e usa o manipulador para definir a propriedade `Status`:

```csharp
namespace FormsVideoLibrary.iOS
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, UIView>
    {
        ···
        protected override void OnElementChanged(ElementChangedEventArgs<VideoPlayer> args)
        {
            ···
            if (args.NewElement != null)
            {
                ···
                args.NewElement.UpdateStatus += OnUpdateStatus;
                ···
            }

            if (args.OldElement != null)
            {
                args.OldElement.UpdateStatus -= OnUpdateStatus;
                ···
            }
        }
        ···
        void OnUpdateStatus(object sender, EventArgs args)
        {
            VideoStatus videoStatus = VideoStatus.NotReady;

            switch (player.Status)
            {
                case AVPlayerStatus.ReadyToPlay:
                    switch (player.TimeControlStatus)
                    {
                        case AVPlayerTimeControlStatus.Playing:
                            videoStatus = VideoStatus.Playing;
                            break;

                        case AVPlayerTimeControlStatus.Paused:
                            videoStatus = VideoStatus.Paused;
                            break;
                    }
                    break;
                }
            }

            ((IVideoPlayerController)Element).Status = videoStatus;
            ···
        }
        ···
    }
}
```

Duas propriedades de `AVPlayer` devem ser acessadas: A propriedade [`Status`](xref:AVFoundation.AVPlayer.Status*) do tipo `AVPlayerStatus` e a propriedade [`TimeControlStatus`](xref:AVFoundation.AVPlayer.TimeControlStatus*) do tipo `AVPlayerTimeControlStatus`. Observe que a propriedade `Element` (que é o `VideoPlayer`) precisa ser convertida em `IVideoPlayerController` para definir a propriedade `Status`.

### <a name="the-android-status-setting"></a>A configuração de status de Android

A propriedade [`IsPlaying`](https://developer.xamarin.com/api/property/Android.Widget.VideoView.IsPlaying/) do `VideoView` do Android é um booliano que indica somente se o vídeo está sendo reproduzido ou está em pausa. Para determinar se a `VideoView` ainda não pode reproduzir nem pausar o vídeo, o evento `Prepared` de `VideoView` precisa ser manipulado. Esses dois manipuladores são definidos no método `OnElementChanged` e são desanexados durante a substituição `Dispose`:

```csharp
namespace FormsVideoLibrary.Droid
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, ARelativeLayout>
    {
        VideoView videoView;
        ···
        bool isPrepared;

        protected override void OnElementChanged(ElementChangedEventArgs<VideoPlayer> args)
        {
            ···
            if (args.NewElement != null)
            {
                if (Control == null)
                {
                    ···
                    videoView.Prepared += OnVideoViewPrepared;
                    ···
                }
                ···
                args.NewElement.UpdateStatus += OnUpdateStatus;
                ···
            }

            if (args.OldElement != null)
            {
                args.OldElement.UpdateStatus -= OnUpdateStatus;
                ···
            }

        }

        protected override void Dispose(bool disposing)
        {
            if (Control != null && videoView != null)
            {
                videoView.Prepared -= OnVideoViewPrepared;
            }
            if (Element != null)
            {
                Element.UpdateStatus -= OnUpdateStatus;
            }

            base.Dispose(disposing);
        }
        ···
    }
}
```

O manipulador `UpdateStatus` usa o campo `isPrepared` (definido no manipulador `Prepared`) e a propriedade `IsPlaying` para definir a propriedade `Status`:

```csharp
namespace FormsVideoLibrary.Droid
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, ARelativeLayout>
    {
        VideoView videoView;
        ···
        bool isPrepared;
        ···
        void OnVideoViewPrepared(object sender, EventArgs args)
        {
            isPrepared = true;
            ···
        }
        ···
        void OnUpdateStatus(object sender, EventArgs args)
        {
            VideoStatus status = VideoStatus.NotReady;

            if (isPrepared)
            {
                status = videoView.IsPlaying ? VideoStatus.Playing : VideoStatus.Paused;
            }
            ···
        }
        ···
    }
}
```

### <a name="the-uwp-status-setting"></a>A configuração de status do UWP

O `VideoPlayerRenderer` do UWP usa o evento `UpdateStatus`, mas não precisa dele para definir a propriedade `Status`. O `MediaElement` define um evento [`CurrentStateChanged`](xref:Windows.UI.Xaml.Controls.MediaElement.CurrentStateChanged) que permite que o renderizador seja notificado quando a propriedade [`CurrentState`](xref:Windows.UI.Xaml.Controls.MediaElement.CurrentState*) é alterada. A propriedade é desanexada na substituição `Dispose`:

```csharp
namespace FormsVideoLibrary.UWP
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, MediaElement>
    {
        protected override void OnElementChanged(ElementChangedEventArgs<VideoPlayer> args)
        {
            base.OnElementChanged(args);

            if (args.NewElement != null)
            {
                if (Control == null)
                {
                    ···
                    mediaElement.CurrentStateChanged += OnMediaElementCurrentStateChanged;
                };
                ···
            }
            ···
        }

        protected override void Dispose(bool disposing)
        {
            if (Control != null)
            {
                ···
                Control.CurrentStateChanged -= OnMediaElementCurrentStateChanged;
            }

            base.Dispose(disposing);
        }
        ···
    }
}
```

A propriedade `CurrentState` é do tipo [`MediaElementState`](/uwp/api/windows.ui.xaml.media.mediaelementstate) e é mapeada com facilidade para `VideoStatus`:

```csharp
namespace FormsVideoLibrary.UWP
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, MediaElement>
    {
        ···
        void OnMediaElementCurrentStateChanged(object sender, RoutedEventArgs args)
        {
            VideoStatus videoStatus = VideoStatus.NotReady;

            switch (Control.CurrentState)
            {
                case MediaElementState.Playing:
                    videoStatus = VideoStatus.Playing;
                    break;

                case MediaElementState.Paused:
                case MediaElementState.Stopped:
                    videoStatus = VideoStatus.Paused;
                    break;
            }

            ((IVideoPlayerController)Element).Status = videoStatus;
        }
        ···
    }
}
```

## <a name="play-pause-and-stop-buttons"></a>Botões Reproduzir, Pausar e Parar

É problemático usar caracteres Unicode para imagens simbólicas de **Reproduzir**, **Pausar** e **Parar**. A seção [Diversos – Técnico](https://unicode-table.com/en/blocks/miscellaneous-technical/) do padrão Unicode define três caracteres de símbolo aparentemente apropriados para essa finalidade. Elas são:

- 0x23F5 (triângulo médio preto apontando para a direita) ou &#x23F5; para **Reproduzir**
- 0x23F8 (barra vertical dupla) ou &#x23F8; para **Pausar**
- 0x23F9 (quadrado preto) ou &#x23F9; para **Parar**

Independentemente de como esses símbolos são exibidos no navegador (e navegadores diferentes os tratarão de maneiras diferentes), eles não são exibidos de maneira consistente nas plataformas compatíveis com o Xamarin.Forms. Em dispositivos iOS e UWP, os caracteres **Pausar** e **Parar** têm uma aparência gráfica, com uma tela de fundo 3D azul e um primeiro plano branco. Esse não é o caso no Android, em que o símbolo é simplesmente azul. No entanto, o ponto de código 0x23F5 para **Reproduzir** não tem a mesma aparência no UWP e nem mesmo é compatível com o iOS e o Android.

Por esse motivo, o ponto de código 0x23F5 não pode ser usado para **Reproduzir**. Um bom substituto é:

- 0x25B6 (triângulo preto apontando para a direita) ou &#x25B6; para **Reproduzir**

Há suporte para isso em cada plataforma, exceto que ele é um triângulo preto simples que não se assemelha à aparência 3D de **Pausar** e **Parar**. Uma possibilidade é seguir o ponto de código 0x25B6 com um código de variante:

- 0x25B6 seguido por 0xFE0F (variante 16) ou &#x25B6;&#xFE0F; para **Reproduzir**

Isso é o que é usado na marcação mostrada abaixo. No iOS, ele fornece o símbolo **Reproduzir** a mesma aparência 3D dos botões **Pausar** e **Parar**, mas a variante não funciona no Android e no UWP.

A página **Transporte personalizado** define a propriedade **AreTransportControlsEnabled** como **falso** e inclui um `ActivityIndicator` exibido quando o vídeo está sendo carregado e dois botões. Os objetos `DataTrigger` são usados para habilitar e desabilitar o `ActivityIndicator` e os botões, bem como para alternar o primeiro botão entre **Reproduzir** e **Pausar**:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:video="clr-namespace:FormsVideoLibrary"
             x:Class="VideoPlayerDemos.CustomTransportPage"
             Title="Custom Transport">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="*" />
            <RowDefinition Height="Auto" />
        </Grid.RowDefinitions>

        <video:VideoPlayer x:Name="videoPlayer"
                           Grid.Row="0"
                           AutoPlay="False"
                           AreTransportControlsEnabled="False"
                           Source="{StaticResource BigBuckBunny}" />

        <ActivityIndicator Grid.Row="0"
                           Color="Gray"
                           IsVisible="False">
            <ActivityIndicator.Triggers>
                <DataTrigger TargetType="ActivityIndicator"
                             Binding="{Binding Source={x:Reference videoPlayer},
                                               Path=Status}"
                             Value="{x:Static video:VideoStatus.NotReady}">
                    <Setter Property="IsVisible" Value="True" />
                    <Setter Property="IsRunning" Value="True" />
                </DataTrigger>
            </ActivityIndicator.Triggers>
        </ActivityIndicator>

        <StackLayout Grid.Row="1"
                     Orientation="Horizontal"
                     Margin="0, 10"
                     BindingContext="{x:Reference videoPlayer}">

            <Button Text="&#x25B6;&#xFE0F; Play"
                    HorizontalOptions="CenterAndExpand"
                    Clicked="OnPlayPauseButtonClicked">
                <Button.Triggers>
                    <DataTrigger TargetType="Button"
                                 Binding="{Binding Status}"
                                 Value="{x:Static video:VideoStatus.Playing}">
                        <Setter Property="Text" Value="&#x23F8; Pause" />
                    </DataTrigger>

                    <DataTrigger TargetType="Button"
                                 Binding="{Binding Status}"
                                 Value="{x:Static video:VideoStatus.NotReady}">
                        <Setter Property="IsEnabled" Value="False" />
                    </DataTrigger>
                </Button.Triggers>
            </Button>

            <Button Text="&#x23F9; Stop"
                    HorizontalOptions="CenterAndExpand"
                    Clicked="OnStopButtonClicked">
                <Button.Triggers>
                    <DataTrigger TargetType="Button"
                                 Binding="{Binding Status}"
                                 Value="{x:Static video:VideoStatus.NotReady}">
                        <Setter Property="IsEnabled" Value="False" />
                    </DataTrigger>
                </Button.Triggers>
            </Button>
        </StackLayout>
    </Grid>
</ContentPage>
```

Gatilhos de dados são descritos em detalhes no artigo [Gatilhos de dados](~/xamarin-forms/app-fundamentals/triggers.md#data).

O arquivo code-behind tem os manipuladores para os eventos `Clicked` do botão:

```csharp
namespace VideoPlayerDemos
{
    public partial class CustomTransportPage : ContentPage
    {
        public CustomTransportPage()
        {
            InitializeComponent();
        }

        void OnPlayPauseButtonClicked(object sender, EventArgs args)
        {
            if (videoPlayer.Status == VideoStatus.Playing)
            {
                videoPlayer.Pause();
            }
            else if (videoPlayer.Status == VideoStatus.Paused)
            {
                videoPlayer.Play();
            }
        }

        void OnStopButtonClicked(object sender, EventArgs args)
        {
            videoPlayer.Stop();
        }
    }
}
```

Como `AutoPlay` é definido como `false` no arquivo **CustomTransport.xaml**, você precisará pressionar o botão **Reproduzir** quando ele ficar habilitado para iniciar o vídeo. Os botões são definidos para que os caracteres Unicode abordados acima sejam acompanhados por seus equivalentes de texto. Os botões têm uma aparência consistente em cada plataforma durante a reprodução do vídeo:

[![Reprodução de transporte personalizado](custom-transport-images/customtransportplaying-small.png "Reprodução de transporte personalizado")](custom-transport-images/customtransportplaying-large.png#lightbox "Reprodução de transporte personalizado")

Porém, no Android e no UWP, o botão **Reproduzir** é muito diferente quando o vídeo está em pausa:

[![Transporte personalizado em pausa](custom-transport-images/customtransportpaused-small.png "Transporte personalizado em pausa")](custom-transport-images/customtransportpaused-large.png#lightbox "Transporte personalizado em pausa")

Em um aplicativo de produção, provavelmente, você desejará usar suas próprias imagens de bitmap para os botões para alcançar a uniformidade visual.


## <a name="related-links"></a>Links relacionados

- [Demonstrações do player de vídeo (amostra)](https://developer.xamarin.com/samples/xamarin-forms/CustomRenderers/VideoPlayerDemos/)
