---
title: Controles personalizados de transporte de vídeo
ms.prod: xamarin
ms.assetid: CE9E955D-A9AC-4019-A5D7-6390D80DECA1
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/12/2018
ms.openlocfilehash: 5463a91dba5840ebe655aa1509d9f98e73643d26
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="custom-video-transport-controls"></a>Controles personalizados de transporte de vídeo

Os controles de transporte de um player de vídeo incluem os botões que executam as funções **reproduzir**, **pausar**, e **parar**. Geralmente, esses botões são identificados com ícones familiares em vez de texto e o **reproduzir** e **pausar** funções geralmente são combinados em um botão.

Por padrão, o `VideoPlayer` exibe cada plataforma com suporte de controles de transporte. Quando você define o `AreTransportControlsEnabled` propriedade `false`, esses controles são suprimidos. Em seguida, você pode controlar o `VideoPlayer` programaticamente ou fornecer seus próprios controles de transporte.

## <a name="the-play-pause-and-stop-methods"></a>Os métodos de reproduzir, pausar e parar

O `VideoPlayer` classe define três métodos chamados `Play`, `Pause`, e `Stop` que são implementados por eventos acionados:

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

Manipuladores de eventos para esses eventos são definidos `VideoPlayerRenderer` classe em cada plataforma, conforme mostrado abaixo:

### <a name="ios-transport-implementations"></a>implementações de transporte do iOS

A versão do iOS de `VideoPlayerRenderer` usa o `OnElementChanged` método para definir manipuladores para esses três eventos quando o `NewElement` propriedade não é `null` e desconecta os manipuladores de eventos quando `OldElement` não `null`:

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

Os manipuladores de eventos são implementados chamando métodos no `AVPlayer` objeto. Não há nenhum `Stop` método para `AVPlayer`, portanto, ele é simulado pausar o vídeo e movendo a posição de início.

### <a name="android-transport-implementations"></a>Implementações de transporte Android

A implementação do Android é semelhante à implementação do iOS. Os manipuladores para três funções são definidos quando `NewElement` não é `null` e desanexado quando `OldElement` não `null`:

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

As três funções chamam métodos definidos pelo `VideoView`.

### <a name="uwp-transport-implementations"></a>Implementações de transporte UWP

A implementação de UWP das funções de três transporte é muito semelhante para as implementações do Android e iOS:

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

## <a name="the-video-player-status"></a>O status de player de vídeo

Implementando o **reproduzir**, **pausar**, e **parar** funções não é suficiente para dar suporte aos controles de transporte. Geralmente o **reproduzir** e **pausar** comandos são implementados com o botão mesmo que altera sua aparência para indicar se o vídeo está atualmente em execução ou em pausa. Além disso, o botão mesmo não deve ser habilitado se o vídeo ainda não foi carregado.

Esses requisitos implicam que o player de vídeo precisa disponibilizar um status atual que indica se ele está executando ou pausado, ou se ele ainda não está pronto para executar um vídeo. (As três plataformas também dão suporte a propriedades que indicam se o vídeo pode ser pausado, ou pode ser movido para uma nova posição, mas essas propriedades são aplicáveis para streaming de vídeo em vez de arquivos de vídeo, para que eles não têm suporte no `VideoPlayer` descrito aqui.)

O **VideoPlayerDemos** projeto inclui um `VideoStatus` enumeração com três membros:

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

O `VideoPlayer` classe define uma propriedade ligável somente real chamada `Status` do tipo `VideoStatus`. Essa propriedade é definida como somente leitura porque ele só deve ser definido de renderizador de plataforma:

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

Normalmente, uma propriedade ligável somente leitura teria uma particular `set` acessador no `Status` propriedade para permitir que ele seja definido de dentro da classe. Para um `View` derivado com suporte renderizadores, no entanto, a propriedade deve ser definida de fora da classe, mas somente pelo renderizador de plataforma.

Por esse motivo, outra propriedade é definida com o nome `IVideoPlayerController.Status`. Essa é uma implementação de interface explícita e é possibilitada pelo `IVideoPlayerController` interface que o `VideoPlayer` implementos de classe:

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

Isso é semelhante ao modo como o [ `WebView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.WebView/) controlar usa o [ `IWebViewController` ](https://developer.xamarin.com/api/type/Xamarin.Forms.IWebViewController/) interface para implementar o `CanGoBack` e `CanGoForward` propriedades. (Consulte o código-fonte do [ `WebView` ](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Core/WebView.cs) e seus renderizadores para obter detalhes.)

Isso torna possível para uma classe externos `VideoPlayer` para definir o `Status` propriedade referenciando o `IVideoPlayerController` interface. (Você verá o código em breve.) A propriedade pode ser definida de outras classes também, mas é improvável de ser definida inadvertidamente. O mais importante é o `Status` propriedade não pode ser definida por meio de uma associação de dados.

Para ajudar os renderizadores manter esse `Status` propriedade atualizada, o `VideoPlayer` classe define um `UpdateStatus` evento que é disparado cada décimo de um segundo:

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

### <a name="the-ios-status-setting"></a>A configuração do iOS

O iOS `VideoPlayerRenderer` define um manipulador para o `UpdateStatus` evento (e desanexa esse manipulador quando subjacente `VideoPlayer` elemento está ausente) e usa o manipulador para definir o `Status` propriedade:

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

Duas propriedades de `AVPlayer` deve ser acessado: O [ `Status` ](https://developer.xamarin.com/api/property/AVFoundation.AVPlayer.Status/) propriedade do tipo `AVPlayerStatus` e [ `TimeControlStatus` ](https://developer.xamarin.com/api/property/AVFoundation.AVPlayer.TimeControlStatus/) propriedade do tipo `AVPlayerTimeControlStatus`. Observe que o `Element` propriedade (que é o `VideoPlayer`) deve ser convertido em `IVideoPlayerController` para definir o `Status` propriedade.

### <a name="the-android-status-setting"></a>A configuração do Android

O [ `IsPlaying` ](https://developer.xamarin.com/api/property/Android.Widget.VideoView.IsPlaying/) propriedade o Android `VideoView` é um valor booleano que indica somente se o vídeo está em execução ou em pausa. Para determinar se o `VideoView` pode não executar ou pausar o vídeo ainda, o `Prepared` evento `VideoView` devem ser tratados. Esses dois manipuladores são definidos `OnElementChanged` método e desconectado durante o `Dispose` substituir:

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

O `UpdateStatus` manipulador utiliza o `isPrepared` campo (definido `Prepared` manipulador) e o `IsPlaying` propriedade para definir o `Status` propriedade:

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

### <a name="the-uwp-status-setting"></a>A configuração de status UWP

A UWP `VideoPlayerRenderer` usa o `UpdateStatus` evento, mas ele não precisará dele para configuração de `Status` propriedade. O `MediaElement` define uma [ `CurrentStateChanged` ](/uwp/api/windows.ui.xaml.controls.mediaelement#Windows_UI_Xaml_Controls_MediaElement_CurrentStateChanged) evento que permite que o processador a ser notificado quando o [ `CurrentState` ](/uwp/api/windows.ui.xaml.controls.mediaelement#Windows_UI_Xaml_Controls_MediaElement_CurrentState) propriedade foi alterada. A propriedade é separada no `Dispose` substituir:

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

O `CurrentState` é de propriedade do tipo [ `MediaElementState` ](/uwp/api/windows.ui.xaml.media.mediaelementstate)e mapeia facilmente em `VideoStatus`:

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

## <a name="play-pause-and-stop-buttons"></a>Executar, pausar e parar botões

Usando caracteres Unicode para simbólicos **reproduzir**, **pausar**, e **parar** imagens é problemático. O [diversas técnicas](https://unicode-table.com/en/blocks/miscellaneous-technical/) seção do padrão Unicode define três caracteres de símbolo aparentemente apropriadas para essa finalidade. Elas são:

- 0x23F5 (preto médio direita triângulo) ou &#x23F5; para **reproduzir**
- 0x23F8 (barra vertical dupla) ou &#x23F8; para **pausar**
- 0x23F9 (quadrado preto) ou &#x23F9; para **parar**

Independentemente como esses símbolos exibida no navegador (e navegadores diferentes tratá-los de maneiras diferentes), eles não são exibidos consistentemente as plataformas com suporte de xamarin. Forms. No iOS e dispositivos UWP, o **pausar** e **parar** caracteres têm uma aparência gráfica, com um plano de fundo azul 3D e um plano de fundo branco. Isso não é o caso no Android, onde o símbolo é simplesmente azul. No entanto, o ponto de código 0x23F5 para **reproduzir** não tem a mesma aparência de UWP, e é nem mesmo tem suporte no iOS e Android.

Por esse motivo, o ponto de código 0x23F5 não pode ser usado para **reproduzir**. É um substituto bom:

- 0x25B6 (triângulo preto apontando para a direita) ou &#x25B6; para **reproduzir**

Isso é compatível com todas as plataformas de três exceto que é um triângulo preto simples que não se assemelha a aparência 3D de **pausar** e **parar**. Uma possibilidade é seguem o ponto de código 0x25B6 com um código de grade:

- 0x25B6 seguido por 0xFE0F (variante 16) ou &#x25B6; &#xFE0F; para **reproduzir**

Este é o que é usado na marcação mostrada abaixo. No iOS, ele fornece o **reproduzir** símbolo a mesma aparência 3D que o **pausar** e **parar** botões, mas a variante não funciona no Android e o UWP.

O **transporte personalizadas** página define o **AreTransportControlsEnabled** propriedade **false** e inclui um `ActivityIndicator` exibida quando estiver carregando o vídeo e dois botões. `DataTrigger` objetos são usados para habilitar e desabilitar o `ActivityIndicator` e os botões e para alternar o primeiro botão entre **reproduzir** e **pausar**:

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

Gatilhos de dados são descritos detalhadamente no artigo [gatilhos dados](~/xamarin-forms/app-fundamentals/triggers.md#data).

O arquivo code-behind tem os manipuladores de botão `Clicked` eventos:

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

Porque `AutoPlay` é definido como `false` no **CustomTransport.xaml** arquivo, será necessário pressionar o **reproduzir** botão quando ele fica habilitado para iniciar o vídeo. Os botões são definidos para que os caracteres Unicode discutidos acima são acompanhados por seus equivalentes de texto. Os botões tem uma aparência consistente em cada plataforma quando a reprodução do vídeo:

[![Execução de transporte personalizado](custom-transport-images/customtransportplaying-small.png "reprodução de transporte personalizado")](custom-transport-images/customtransportplaying-large.png#lightbox "reprodução de transporte personalizado")

Mas, no Android e UWP, o **reproduzir** botão se parece muito diferente quando o vídeo é pausado:

[![Em pausa de transporte personalizado](custom-transport-images/customtransportpaused-small.png "pausado de transporte personalizado")](custom-transport-images/customtransportpaused-large.png#lightbox "pausado de transporte personalizado")

Em um aplicativo de produção, você provavelmente desejará usar suas próprias imagens de bitmap para os botões para alcançar a uniformidade visual.


## <a name="related-links"></a>Links relacionados

- [Demonstrações em vídeo Player (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/VideoPlayerDemos/)
