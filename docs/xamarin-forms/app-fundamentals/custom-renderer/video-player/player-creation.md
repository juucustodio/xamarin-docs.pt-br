---
title: ''
description: Este artigo explica como implementar um renderizador personalizado do player de vídeo em cada plataforma usando o Xamarin.Forms .
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: d026ecc3288da155aefb0f68ee0c70721106c0da
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/28/2020
ms.locfileid: "84133738"
---
# <a name="creating-the-platform-video-players"></a>Criando os players de vídeo de plataforma

[![Baixar exemplo ](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-videoplayerdemos)

A solução [**VideoPlayerDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-videoplayerdemos) contém todo o código para implementar um player de vídeo para o Xamarin.Forms . Também inclui uma série de páginas que demonstram como usar o player de vídeo em um aplicativo. Todo o código `VideoPlayer` e seus renderizadores de plataforma residem em pastas de projeto chamadas `FormsVideoLibrary` e também usam o namespace `FormsVideoLibrary`. Isso deve facilitar a cópia dos arquivos em seu próprio aplicativo e a referência das classes.

## <a name="the-video-player"></a>O player de vídeo

A [`VideoPlayer`](https://github.com/xamarin/xamarin-forms-samples/blob/master/CustomRenderers/VideoPlayerDemos/VideoPlayerDemos/VideoPlayerDemos/FormsVideoLibrary/VideoPlayer.cs) classe faz parte da biblioteca de .net Standard de **VideoPlayerDemos** que é compartilhada entre as plataformas. Ela deriva de `View`:

```csharp
using System;
using Xamarin.Forms;

namespace FormsVideoLibrary
{
    public class VideoPlayer : View, IVideoPlayerController
    {
        ···
    }
}
```

Os membros dessa classe (e a interface `IVideoPlayerController`) são descritos nos artigos a seguir.

Cada uma das plataformas contém uma classe chamada `VideoPlayerRenderer` que contém o código específico da plataforma para implementar um player de vídeo. A tarefa principal desse renderizador é criar um player de vídeo para essa plataforma.

### <a name="the-ios-player-view-controller"></a>O controlador de exibição do player do iOS

Várias classes estão envolvidas ao implementar um player de vídeo no iOS. O aplicativo primeiro cria um [`AVPlayerViewController`](xref:AVKit.AVPlayerViewController) e, em seguida, define a [`Player`](xref:AVKit.AVPlayerViewController.Player*) propriedade como um objeto do tipo [`AVPlayer`](xref:AVFoundation.AVPlayer) . Classes adicionais são necessárias quando o player recebe uma fonte de vídeo.

Como todos os renderizadores, o iOS [`VideoPlayerRenderer`](https://github.com/xamarin/xamarin-forms-samples/blob/master/CustomRenderers/VideoPlayerDemos/VideoPlayerDemos/VideoPlayerDemos.iOS/FormsVideoLibrary/VideoPlayerRenderer.csVideoPlayerRenderer.cs) contém um `ExportRenderer` atributo que identifica a `VideoPlayer` exibição com o renderizador:

```csharp
using System;
using System.ComponentModel;
using System.IO;

using AVFoundation;
using AVKit;
using CoreMedia;
using Foundation;
using UIKit;

using Xamarin.Forms;
using Xamarin.Forms.Platform.iOS;

[assembly: ExportRenderer(typeof(FormsVideoLibrary.VideoPlayer),
                          typeof(FormsVideoLibrary.iOS.VideoPlayerRenderer))]

namespace FormsVideoLibrary.iOS
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, UIView>
    {
        ···
    }
}
```

Geralmente, um processador que define um controle de plataforma deriva da [`ViewRenderer<View, NativeView>`](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Platform.iOS/ViewRenderer.cs) classe, em que `View` é o Xamarin.Forms `View` derivativo (nesse caso, `VideoPlayer` ) e `NativeView` é um `UIView` derivativo do IOS para a classe do processador. Para esse renderizador, esse argumento genérico é apenas definido como `UIView`, por motivos que você verá em breve.

Quando um renderizador se basear em um derivado de `UIViewController` (como esse), a classe deverá substituir a propriedade `ViewController` e retornar o controlador de exibição, neste caso, `AVPlayerViewController`. Essa é a finalidade do campo `_playerViewController`:

```csharp
namespace FormsVideoLibrary.iOS
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, UIView>
    {
        AVPlayer player;
        AVPlayerItem playerItem;
        AVPlayerViewController _playerViewController;       // solely for ViewController property

        public override UIViewController ViewController => _playerViewController;

        protected override void OnElementChanged(ElementChangedEventArgs<VideoPlayer> args)
        {
            base.OnElementChanged(args);

            if (args.NewElement != null)
            {
                if (Control == null)
                {
                    // Create AVPlayerViewController
                    _playerViewController = new AVPlayerViewController();

                    // Set Player property to AVPlayer
                    player = new AVPlayer();
                    _playerViewController.Player = player;

                    // Use the View from the controller as the native control
                    SetNativeControl(_playerViewController.View);
                }
                ···
            }
        }
        ···
    }
}
```

A principal responsabilidade da substituição `OnElementChanged` é verificar se a propriedade `Control` é `null` e, nesse caso, criar um controle de plataforma e passá-lo para o método `SetNativeControl`. Nesse caso, esse objeto só está disponível na propriedade `View` do `AVPlayerViewController`. Por acaso, esse derivado de `UIView` é uma classe particular chamada `AVPlayerView`, mas como é particular, ela não pode ser explicitamente especificada como o segundo argumento genérico para `ViewRenderer`.

Geralmente, a propriedade `Control` da classe de renderizador depois disso se refere à `UIView` usada para implementar o renderizador, mas nesse caso, a propriedade `Control` não é usada em nenhum outro lugar.

### <a name="the-android-video-view"></a>A exibição de vídeo do Android

O renderizador do Android para `VideoPlayer` o é baseado na classe do Android [`VideoView`](xref:Android.Widget.VideoView) . No entanto, se `VideoView` for usado por si só para reproduzir um vídeo em um Xamarin.Forms aplicativo, o vídeo preencherá a área alocada para o `VideoPlayer` sem manter a taxa de proporção correta. Por esse motivo (como você verá em breve), a `VideoView` se torna um filho de um `RelativeLayout` do Android. Uma `using` diretiva define `ARelativeLayout` para distingui-la do Xamarin.Forms `RelativeLayout` , e esse é o segundo argumento genérico no `ViewRenderer` :

```csharp
using System;
using System.ComponentModel;
using System.IO;

using Android.Content;
using Android.Media;
using Android.Widget;
using ARelativeLayout = Android.Widget.RelativeLayout;

using Xamarin.Forms;
using Xamarin.Forms.Platform.Android;

[assembly: ExportRenderer(typeof(FormsVideoLibrary.VideoPlayer),
                          typeof(FormsVideoLibrary.Droid.VideoPlayerRenderer))]

namespace FormsVideoLibrary.Droid
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, ARelativeLayout>
    {
        ···
        public VideoPlayerRenderer(Context context) : base(context)
        {
        }
        ···
    }
}
```

A partir do Xamarin.Forms 2,5, os renderizadores do Android devem incluir um construtor com um `Context` argumento.

A substituição `OnElementChanged` cria a `VideoView` e o `RelativeLayout` e define os parâmetros de layout para a `VideoView`, a fim de centralizá-la dentro do `RelativeLayout`.

```csharp
namespace FormsVideoLibrary.Droid
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, ARelativeLayout>
    {
        VideoView videoView;
        MediaController mediaController;    // Used to display transport controls
        bool isPrepared;
        ···
        protected override void OnElementChanged(ElementChangedEventArgs<VideoPlayer> args)
        {
            base.OnElementChanged(args);

            if (args.NewElement != null)
            {
                if (Control == null)
                {
                    // Save the VideoView for future reference
                    videoView = new VideoView(Context);

                    // Put the VideoView in a RelativeLayout
                    ARelativeLayout relativeLayout = new ARelativeLayout(Context);
                    relativeLayout.AddView(videoView);

                    // Center the VideoView in the RelativeLayout
                    ARelativeLayout.LayoutParams layoutParams =
                        new ARelativeLayout.LayoutParams(LayoutParams.MatchParent, LayoutParams.MatchParent);
                    layoutParams.AddRule(LayoutRules.CenterInParent);
                    videoView.LayoutParameters = layoutParams;

                    // Handle a VideoView event
                    videoView.Prepared += OnVideoViewPrepared;

                    // Use the RelativeLayout as the native control
                    SetNativeControl(relativeLayout);
                }
                ···
            }
            ···
        }

        protected override void Dispose(bool disposing)
        {
            if (Control != null && videoView != null)
            {
                videoView.Prepared -= OnVideoViewPrepared;
            }
            base.Dispose(disposing);
        }

        void OnVideoViewPrepared(object sender, EventArgs args)
        {
            isPrepared = true;
            ···
        }
        ···
    }
}
```

Um manipulador para o evento `Prepared` é anexado a esse método e desanexado do método `Dispose`. Esse evento é disparado quando a `VideoView` tem informações suficientes para começar a reprodução de um arquivo de vídeo.

### <a name="the-uwp-media-element"></a>O elemento de mídia do UWP

No Plataforma Universal do Windows (UWP), o player de vídeo mais comum é [`MediaElement`](xref:Windows.UI.Xaml.Controls.MediaElement) . Essa documentação `MediaElement` indica que o [`MediaPlayerElement`](xref:Windows.UI.Xaml.Controls.MediaPlayerElement) deve ser usado em vez disso, quando é necessário apenas dar suporte a versões do Windows 10 começando com o Build 1607.

A substituição `OnElementChanged` precisa criar um `MediaElement`, definir alguns manipuladores de eventos e passar o objeto `MediaElement` para `SetNativeControl`:

```csharp
using System;
using System.ComponentModel;

using Windows.Storage;
using Windows.Storage.Streams;
using Windows.UI.Xaml.Controls;
using Windows.UI.Xaml.Media;

using Xamarin.Forms;
using Xamarin.Forms.Platform.UWP;

[assembly: ExportRenderer(typeof(FormsVideoLibrary.VideoPlayer),
                          typeof(FormsVideoLibrary.UWP.VideoPlayerRenderer))]

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
                    MediaElement mediaElement = new MediaElement();
                    SetNativeControl(mediaElement);

                    mediaElement.MediaOpened += OnMediaElementMediaOpened;
                    mediaElement.CurrentStateChanged += OnMediaElementCurrentStateChanged;
                }
                ···
            }
            ···
        }

        protected override void Dispose(bool disposing)
        {
            if (Control != null)
            {
                Control.MediaOpened -= OnMediaElementMediaOpened;
                Control.CurrentStateChanged -= OnMediaElementCurrentStateChanged;
            }

            base.Dispose(disposing);
        }        
        ···
    }
}
```

Os dois manipuladores de eventos são desanexados no evento `Dispose` para o renderizador.

## <a name="showing-the-transport-controls"></a>Mostrando os controles de transporte

Todos os players de vídeo incluídos nas plataformas dão suporte a um conjunto padrão de controles de transporte que incluem botões para reproduzir e pausar, bem como uma barra para indicar a posição atual no vídeo e para mover para uma nova posição.

A classe `VideoPlayer` define uma propriedade chamada `AreTransportControlsEnabled` e define o valor padrão como `true`:

```csharp
namespace FormsVideoLibrary
{
    public class VideoPlayer : View, IVideoPlayerController
    {
        ···
        // AreTransportControlsEnabled property
        public static readonly BindableProperty AreTransportControlsEnabledProperty =
            BindableProperty.Create(nameof(AreTransportControlsEnabled), typeof(bool), typeof(VideoPlayer), true);

        public bool AreTransportControlsEnabled
        {
            set { SetValue(AreTransportControlsEnabledProperty, value); }
            get { return (bool)GetValue(AreTransportControlsEnabledProperty); }
        }
        ···
    }
}
```

Embora essa propriedade tenha os acessadores `set` e `get`, o renderizador precisa lidar com casos somente quando a propriedade está definida. O acessador `get` apenas retorna o valor atual da propriedade.

Propriedades como `AreTransportControlsEnabled` são tratadas em renderizadores de plataforma de duas maneiras:

- A primeira vez é quando Xamarin.Forms o cria um `VideoPlayer` elemento. Isso é indicado na substituição `OnElementChanged` do renderizador quando a propriedade `NewElement` não é `null`. Neste momento, o renderizador pode definir seu próprio player de vídeo da plataforma com base no valor inicial da propriedade, conforme definido no `VideoPlayer`.

- Se a propriedade em `VideoPlayer` for alterada posteriormente, o método `OnElementPropertyChanged` no renderizador será chamado. Isso permite que o renderizador atualize o player de vídeo da plataforma com base na nova configuração de propriedade.

As seções a seguir abordam como a propriedade `AreTransportControlsEnabled` é tratada em cada plataforma.

### <a name="ios-playback-controls"></a>Controles de reprodução do iOS

A propriedade do iOS `AVPlayerViewController` que governa a exibição de controles de transporte é [`ShowsPlaybackControls`](xref:AVKit.AVPlayerViewController.ShowsPlaybackControls*) . É assim como essa propriedade é definida no `VideoViewRenderer` do iOS:

```csharp
namespace FormsVideoLibrary.iOS
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, UIView>
    {
        ···
        AVPlayerViewController _playerViewController;       // solely for ViewController property

        public override UIViewController ViewController => _playerViewController;

        protected override void OnElementChanged(ElementChangedEventArgs<VideoPlayer> args)
        {
            ···
            if (args.NewElement != null)
            {
                ···
                SetAreTransportControlsEnabled();
                ···
            }
        }

        protected override void OnElementPropertyChanged(object sender, PropertyChangedEventArgs args)
        {
            base.OnElementPropertyChanged(sender, args);

            if (args.PropertyName == VideoPlayer.AreTransportControlsEnabledProperty.PropertyName)
            {
                SetAreTransportControlsEnabled();
            }
            ···
        }

        void SetAreTransportControlsEnabled()
        {
            ((AVPlayerViewController)ViewController).ShowsPlaybackControls = Element.AreTransportControlsEnabled;
        }
        ···
    }
}
```

A propriedade `Element` do renderizador refere-se à classe `VideoPlayer`.

### <a name="the-android-media-controller"></a>O controlador de mídia do Android

No Android, a exibição dos controles de transporte requer a criação de um [`MediaController`](xref:Android.Widget.MediaController) objeto e sua associação com o `VideoView` objeto. A mecânica é demonstrada no método `SetAreTransportControlsEnabled`:

```csharp
namespace FormsVideoLibrary.Droid
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, ARelativeLayout>
    {
        VideoView videoView;
        MediaController mediaController;    // Used to display transport controls
        bool isPrepared;

        protected override void OnElementChanged(ElementChangedEventArgs<VideoPlayer> args)
        {
            ···
            if (args.NewElement != null)
            {
                ···
                SetAreTransportControlsEnabled();
                ···
            }
        }

        protected override void OnElementPropertyChanged(object sender, PropertyChangedEventArgs args)
        {
            base.OnElementPropertyChanged(sender, args);

            if (args.PropertyName == VideoPlayer.AreTransportControlsEnabledProperty.PropertyName)
            {
                SetAreTransportControlsEnabled();
            }
            ···
        }

        void SetAreTransportControlsEnabled()
        {
            if (Element.AreTransportControlsEnabled)
            {
                mediaController = new MediaController(Context);
                mediaController.SetMediaPlayer(videoView);
                videoView.SetMediaController(mediaController);
            }
            else
            {
                videoView.SetMediaController(null);

                if (mediaController != null)
                {
                    mediaController.SetMediaPlayer(null);
                    mediaController = null;
                }
            }
        }
        ···
    }
}
```

### <a name="the-uwp-transport-controls-property"></a>A propriedade de Controles de Transporte do UWP

O UWP `MediaElement` define uma propriedade chamada [`AreTransportControlsEnabled`](xref:Windows.UI.Xaml.Controls.MediaElement.AreTransportControlsEnabled*) , de modo que a propriedade seja definida a partir da `VideoPlayer` propriedade de mesmo nome:

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
                SetAreTransportControlsEnabled();
                ···
            }
        }

        protected override void OnElementPropertyChanged(object sender, PropertyChangedEventArgs args)
        {
            base.OnElementPropertyChanged(sender, args);

            if (args.PropertyName == VideoPlayer.AreTransportControlsEnabledProperty.PropertyName)
            {
                SetAreTransportControlsEnabled();
            }
            ···
        }

        void SetAreTransportControlsEnabled()
        {
            Control.AreTransportControlsEnabled = Element.AreTransportControlsEnabled;
        }
        ···
    }
}
```

Mais uma propriedade é necessária para iniciar a reprodução de um vídeo: essa é a propriedade `Source` crucial que referencia um arquivo de vídeo. A implementação da propriedade `Source` é descrita no próximo artigo, [Reproduzindo um vídeo da Web](web-videos.md).

## <a name="related-links"></a>Links relacionados

- [Demonstrações do player de vídeo (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-videoplayerdemos)
