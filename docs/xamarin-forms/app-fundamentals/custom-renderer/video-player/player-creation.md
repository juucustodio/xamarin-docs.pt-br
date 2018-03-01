---
title: "Criando os players de vídeo de plataforma"
ms.topic: article
ms.prod: xamarin
ms.assetid: EEE2FB9B-EB73-4A3F-A859-7A1D4808E149
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/12/2018
ms.openlocfilehash: d9204593eea97f27b9f461f3f7571c326919f0a9
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/28/2018
---
# <a name="creating-the-platform-video-players"></a>Criando os players de vídeo de plataforma

O [ **VideoPlayerDemos** ](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/VideoPlayerDemos/) solução contém todo o código para implementar um player de vídeo para xamarin. Forms. Ele também inclui uma série de páginas que demonstra como usar o player de vídeo em um aplicativo. Todos os `VideoPlayer` seus renderizadores de plataforma e código residem em pastas de projeto chamadas `FormsVideoLibrary`e também usar o namespace `FormsVideoLibrary`. Isso deve tornar fácil de copiar os arquivos no seu próprio aplicativo e as classes de referência.

## <a name="the-video-player"></a>O player de vídeo

O [ `VideoPlayer` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/CustomRenderers/VideoPlayerDemos/VideoPlayerDemos/VideoPlayerDemos/VideoPlayer.cs) classe é parte do **VideoPlayerDemos** biblioteca de classes portátil (PCL) que é compartilhada entre as plataformas. Deriva de `View`:

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

Os membros dessa classe (e o `IVideoPlayerController` interface) são descritos nos artigos a seguir.

Cada uma das três plataformas contém uma classe denominada `VideoPlayerRenderer` que contém o código específico da plataforma para implementar um player de vídeo. A principal tarefa deste processador é criar um player de vídeo para essa plataforma.

### <a name="the-ios-player-view-controller"></a>O controlador de exibição do player iOS

Várias classes estão envolvidos ao implementar um player de vídeo em iOS. O aplicativo cria primeiro um [ `AVPlayerViewController` ](https://developer.xamarin.com/api/type/AVKit.AVPlayerViewController/) e, em seguida, define o [ `Player` ](https://developer.xamarin.com/api/property/AVKit.AVPlayerViewController.Player/) propriedade para um objeto do tipo [ `AVPlayer` ](https://developer.xamarin.com/api/type/AVFoundation.AVPlayer/). Classes adicionais são necessárias quando o player é atribuído a uma fonte de vídeo.

Como todos os processadores, o iOS [ `VideoPlayerRenderer` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/CustomRenderers/VideoPlayerDemos/VideoPlayerDemos/VideoPlayerDemos.iOS/VideoPlayerRenderer.cs) contém um `ExportRenderer` atributo que identifica o `VideoPlayer` exibição com o processador:

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

Geralmente um processador que define um controle de plataforma deriva o [ `ViewRenderer<View, NativeView>` ](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Platform.iOS/ViewRenderer.cs) classe, onde `View` é o xamarin. Forms `View` derivativo (nesse caso, `VideoPlayer`) e `NativeView` é um iOS `UIView` derivados para a classe de renderizador. Para este processador, esse argumento genérico simplesmente é definido como `UIView`, por motivos de você verá em breve.

Quando um renderizador tem base em um `UIViewController` derivada (como esta é), então a classe deve substituir o `ViewController` propriedade e retornar o controlador de exibição, neste caso `AVPlayerViewController`. Que é a finalidade de `_playerViewController` campo:

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

Responsabilidade principal a `OnElementChanged` substituição é verificar se o `Control` é de propriedade `null` e, nesse caso, crie um controle de plataforma e passá-lo para o `SetNativeControl` método. Nesse caso, esse objeto está disponível somente do `View` propriedade o `AVPlayerViewController`. Que `UIView` derivada é uma classe privada denominada `AVPlayerView`, mas porque ele é particular, ele não pode ser especificado explicitamente como o segundo argumento genérico para `ViewRenderer`.

Geralmente o `Control` propriedade da classe renderizador posteriormente se refere ao `UIView` usadas para implementar o processador, mas nesse caso o `Control` propriedade não é usada em outro lugar.

### <a name="the-android-video-view"></a>O modo de exibição de vídeo Android

O renderizador do Android para `VideoPlayer` se baseia o Android [ `VideoView` ](https://developer.xamarin.com/api/type/Android.Widget.VideoView/) classe. No entanto, se `VideoView` é usado para reproduzir um vídeo em um aplicativo xamarin. Forms, os vídeos preenchimentos alocado a área para o `VideoPlayer` sem manter a taxa de proporção correta. Para este motivo (como você verá em breve), o `VideoView` é feita a um filho de um Android `RelativeLayout`. Um `using` diretiva define `ARelativeLayout` distingui-lo de que o xamarin. Forms `RelativeLayout`, e que é o segundo argumento genérico `ViewRenderer`:

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

O `OnElementChanged` substituição cria ambos o `VideoView` e `RelativeLayout` e define os parâmetros de layout para o `VideoView` para centralizá-la dentro a `RelativeLayout`.


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

Um manipulador para o `Prepared` evento é anexado a este método e desanexado no `Dispose` método. Esse evento é acionado quando o `VideoView` tem informações suficientes para começar a execução de um arquivo de vídeo.

### <a name="the-uwp-media-element"></a>O elemento de mídia UWP

No Windows UWP (plataforma Universal), o player de vídeo mais comum é [ `MediaElement` ](/uwp/api/Windows.UI.Xaml.Controls.MediaElement/). Essa documentação de `MediaElement` indica que o [ `MediaPlayerElement` ](/uwp/api/windows.ui.xaml.controls.mediaplayerelement/) deve ser usado em vez disso, quando é necessário dar suporte a versões da partir do Windows 10 versão 1607 apenas.

O `OnElementChanged` substituição precisa criar um `MediaElement`, definir alguns manipuladores de eventos e passar o `MediaElement` do objeto para `SetNativeControl`:

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

Os manipuladores de eventos de dois são desanexados no `Dispose` evento para o processador.

## <a name="showing-the-transport-controls"></a>Mostrar controles de transporte

Todos os os players de vídeo incluídos nas três plataformas oferecem suporte a um conjunto padrão de controles de transporte que incluem botões para reproduzir e pausar e uma barra para indicar a posição atual dentro do vídeo e para mover para uma nova posição.

O `VideoPlayer` classe define uma propriedade chamada `AreTransportControlsEnabled` e define o valor padrão `true`:


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

Embora essa propriedade tem ambos `set` e `get` acessadores, o processador precisa lidar com casos somente quando a propriedade é definida. O `get` acessador simplesmente retorna o valor atual da propriedade.

Propriedades como `AreTransportControlsEnabled` são tratadas em renderizadores de plataforma de duas maneiras:

- Na primeira vez que é quando o xamarin. Forms cria um `VideoPlayer` elemento. Isso é indicado no `OnElementChanged` substituir do renderizador quando o `NewElement` propriedade não é `null`. Neste momento, pode definir o processador é o player de vídeo própria plataforma do valor inicial da propriedade, conforme definido no `VideoPlayer`.

- Se a propriedade em `VideoPlayer` alterar posteriormente, o `OnElementPropertyChanged` é chamado de método em que o renderizador. Isso permite que o renderizador atualizar o player de vídeo de plataforma com base na nova configuração de propriedade.

Aqui está como o `AreTransportControlsEnabled` propriedade é tratada em três plataformas:

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

O `Element` refere-se a propriedade do renderizador para o `VideoPlayer` classe.

### <a name="the-android-media-controller"></a>O controlador de mídia Android

No Android, exibindo os controles de transporte requer a criação de um [ `MediaController` ](https://developer.xamarin.com/api/type/Android.Widget.MediaController/) objeto e associá-lo com o `VideoView` objeto. Os mecanismos são demonstrados no `SetAreTransportControlsEnabled` método:

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

### <a name="the-uwp-transport-controls-property"></a>A propriedade de controles de transporte UWP

O UWP `MediaElement` define uma propriedade chamada [ `AreTransportControlsEnabled` ](/uwp/api/windows.ui.xaml.controls.mediaelement#Windows_UI_Xaml_Controls_MediaElement_AreTransportControlsEnabled), de modo que a propriedade é definida do `VideoPlayer` propriedade o mesmo nome:

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

Mais uma propriedade é necessária iniciar a reprodução de um vídeo: Este é o crucial `Source` propriedade que faz referência a um arquivo de vídeo. Implementando o `Source` propriedade é descrita no próximo artigo, [reprodução de um vídeo Web](web-videos.md).


## <a name="related-links"></a>Links relacionados

- [Demonstrações em vídeo Player (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/VideoPlayerDemos/)
