---
title: Criar os players de vídeo de plataforma
description: Este artigo explica como implementar um renderizador personalizado do player de vídeo em cada plataforma, usando xamarin. Forms.
ms.prod: xamarin
ms.assetid: EEE2FB9B-EB73-4A3F-A859-7A1D4808E149
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/12/2018
ms.openlocfilehash: 0090ec798e8d7b1dfb9bd8e25f09d71ec0353b45
ms.sourcegitcommit: 5fc171a45697f7c610d65f74d1f3cebbac445de6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2018
ms.locfileid: "52171905"
---
# <a name="creating-the-platform-video-players"></a>Criar os players de vídeo de plataforma

O [ **VideoPlayerDemos** ](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/VideoPlayerDemos/) solução contém todo o código para implementar um player de vídeo para xamarin. Forms. Ele também inclui uma série de páginas que demonstra como usar o player de vídeo dentro de um aplicativo. Todos os as `VideoPlayer` seus renderizadores de plataforma e código residam em pastas de projeto chamadas `FormsVideoLibrary`e também usar o namespace `FormsVideoLibrary`. Isso deve tornar fácil copiar os arquivos no seu próprio aplicativo e as classes de referência.

## <a name="the-video-player"></a>O player de vídeo

O [ `VideoPlayer` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/CustomRenderers/VideoPlayerDemos/VideoPlayerDemos/VideoPlayerDemos/VideoPlayer.cs) classe faz parte do **VideoPlayerDemos** biblioteca .NET Standard que é compartilhada entre as plataformas. Ela é derivada da `View`:

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

Os membros dessa classe (e o `IVideoPlayerController` interface) são descritas nos artigos a seguir.

Cada uma das plataformas contém uma classe denominada `VideoPlayerRenderer` que contém o código específico da plataforma para implementar um player de vídeo. A tarefa principal deste renderizador é criar um player de vídeo para essa plataforma.

### <a name="the-ios-player-view-controller"></a>O controlador de exibição do player de iOS

Várias classes envolvidas ao implementar um player de vídeo no iOS. O aplicativo primeiro cria uma [ `AVPlayerViewController` ](https://developer.xamarin.com/api/type/AVKit.AVPlayerViewController/) e, em seguida, define o [ `Player` ](https://developer.xamarin.com/api/property/AVKit.AVPlayerViewController.Player/) propriedade para um objeto do tipo [ `AVPlayer` ](https://developer.xamarin.com/api/type/AVFoundation.AVPlayer/). Classes adicionais são necessárias quando o player é atribuído a uma fonte de vídeo.

Como todos os processadores, o iOS [ `VideoPlayerRenderer` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/CustomRenderers/VideoPlayerDemos/VideoPlayerDemos/VideoPlayerDemos.iOS/VideoPlayerRenderer.cs) contém um `ExportRenderer` atributo que identifica o `VideoPlayer` exibição com a renderização:

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

Geralmente um renderizador que define um controle de plataforma deriva de [ `ViewRenderer<View, NativeView>` ](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Platform.iOS/ViewRenderer.cs) classe, onde `View` é o xamarin. Forms `View` derivativo (nesse caso, `VideoPlayer`) e `NativeView` é um iOS `UIView` derivados da classe de renderizador. Para este processador, esse argumento genérico é simplesmente definido como `UIView`, por motivos de você verá em breve.

Quando um renderizador se basear em um `UIViewController` derivativo (como esta é), e em seguida, a classe deve substituir o `ViewController` propriedade e retorno, neste caso, o controlador de exibição `AVPlayerViewController`. Que é a finalidade do `_playerViewController` campo:

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

A principal responsabilidade do `OnElementChanged` substituição é para verificar se o `Control` é de propriedade `null` e, nesse caso, crie um controle de plataforma e passá-lo para o `SetNativeControl` método. Nesse caso, esse objeto só está disponível na `View` propriedade do `AVPlayerViewController`. Que `UIView` derivativos, por acaso, é uma classe privada chamada `AVPlayerView`, mas porque ela é privada, ele não pode ser explicitamente especificado como o segundo argumento genérico para `ViewRenderer`.

Geralmente o `Control` propriedade da classe de renderizador depois disso se refere ao `UIView` usado para implementar o renderizador, mas nesse caso, o `Control` propriedade não é usada em outro lugar.

### <a name="the-android-video-view"></a>A exibição de vídeo do Android

O renderizador do Android para `VideoPlayer` se baseia no Android [ `VideoView` ](https://developer.xamarin.com/api/type/Android.Widget.VideoView/) classe. No entanto, se `VideoView` é usado por si só para reproduzir um vídeo em um aplicativo xamarin. Forms, os preenchimentos de vídeos indicado a área para o `VideoPlayer` sem manter a taxa de proporção correta. Para este motivo (como você verá em breve), o `VideoView` é feita a um filho de um Android `RelativeLayout`. Um `using` diretiva define `ARelativeLayout` distingui-lo de que o xamarin. Forms `RelativeLayout`, e que é o segundo argumento genérico `ViewRenderer`:

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

A partir do xamarin. Forms 2.5, renderizadores Android devem incluir um construtor com um `Context` argumento.

O `OnElementChanged` substituição cria ambos os `VideoView` e `RelativeLayout` e define os parâmetros de layout para o `VideoView` para centralizá-la dentro a `RelativeLayout`.


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

Um manipulador para o `Prepared` evento é anexado a esse método e desanexado no `Dispose` método. Este evento é disparado quando o `VideoView` tem informações suficientes para começar a reprodução de um arquivo de vídeo.

### <a name="the-uwp-media-element"></a>O elemento de mídia da UWP

Na Universal Windows Platform (UWP), o player de vídeo mais comuns é [ `MediaElement` ](/uwp/api/Windows.UI.Xaml.Controls.MediaElement/). Essa documentação de `MediaElement` indica que o [ `MediaPlayerElement` ](/uwp/api/windows.ui.xaml.controls.mediaplayerelement/) deve ser usado em vez disso, quando é necessário dar suporte a versões do Windows 10 que começa com build 1607 apenas.

O `OnElementChanged` substituição precisa criar um `MediaElement`, defina alguns manipuladores de eventos e passar a `MediaElement` objeto `SetNativeControl`:

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

Os dois manipuladores de evento são desanexados no `Dispose` eventos para o renderizador.

## <a name="showing-the-transport-controls"></a>Mostrando os controles de transporte

Todos os players de vídeo incluídos nas plataformas dão suporte a um conjunto padrão de controles de transporte que incluem botões para reproduzir e pausar e uma barra para indicar a posição atual no vídeo e para mover para uma nova posição.

O `VideoPlayer` classe define uma propriedade chamada `AreTransportControlsEnabled` e define o valor padrão para `true`:


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

Embora essa propriedade tiver tanto `set` e `get` acessadores, o renderizador tem que lidar com casos somente quando a propriedade está definida. O `get` acessador simplesmente retorna o valor atual da propriedade.

Propriedades, como `AreTransportControlsEnabled` são tratadas em renderizadores de plataforma de duas maneiras:

- Na primeira vez é quando o xamarin. Forms cria um `VideoPlayer` elemento. Isso é indicado na `OnElementChanged` substituir do renderizador quando o `NewElement` propriedade não é `null`. Neste momento, o renderizador pode definir é o player de vídeo da própria plataforma do valor inicial da propriedade conforme definido no `VideoPlayer`.

- Se a propriedade na `VideoPlayer` for alterado posteriormente, em seguida, a `OnElementPropertyChanged` método no renderizador é chamado. Isso permite que o renderizador atualizar o player de vídeo de plataforma com base na nova configuração de propriedade.

As seções a seguir discutem como o `AreTransportControlsEnabled` propriedade é tratada em cada plataforma.

### <a name="ios-playback-controls"></a>controles de reprodução do iOS

A propriedade do iOS `AVPlayerViewController` que controla a exibição de transporte é controles [ `ShowsPlaybackControls` ](https://developer.xamarin.com/api/property/AVKit.AVPlayerViewController.ShowsPlaybackControls/). Aqui está como essa propriedade é definida no iOS `VideoViewRenderer`:

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

O `Element` refere-se a propriedade do processador para o `VideoPlayer` classe.

### <a name="the-android-media-controller"></a>O controlador de mídia do Android

No Android, exibindo os controles de transporte requer a criação de um [ `MediaController` ](https://developer.xamarin.com/api/type/Android.Widget.MediaController/) objeto e associá-lo com o `VideoView` objeto. A mecânica é demonstrada a `SetAreTransportControlsEnabled` método:

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

### <a name="the-uwp-transport-controls-property"></a>A propriedade de controles de transporte de UWP

A UWP `MediaElement` define uma propriedade chamada [ `AreTransportControlsEnabled` ](/uwp/api/windows.ui.xaml.controls.mediaelement#Windows_UI_Xaml_Controls_MediaElement_AreTransportControlsEnabled), de modo que a propriedade é definida do `VideoPlayer` propriedade o mesmo nome:

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

Mais de uma propriedade é necessária iniciar a reprodução de um vídeo: isso é o crucial `Source` propriedade que faz referência a um arquivo de vídeo. Implementando o `Source` propriedade é descrita no próximo artigo [reproduzir um vídeo da Web](web-videos.md).


## <a name="related-links"></a>Links relacionados

- [Demonstrações do Player de vídeo (amostra)](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/VideoPlayerDemos/)
