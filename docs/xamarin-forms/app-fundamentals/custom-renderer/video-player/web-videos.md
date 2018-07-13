---
title: Reproduzir um vídeo da Web
description: Este artigo explica como reproduzir vídeos da web em um aplicativo de player de vídeo, usando o xamarin. Forms.
ms.prod: xamarin
ms.assetid: 75781A10-865D-4BA8-8D6B-E3DA012922BC
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/12/2018
ms.openlocfilehash: 566f056bd616c918ce274b9c7406d94fdc265ea2
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/12/2018
ms.locfileid: "38994553"
---
# <a name="playing-a-web-video"></a>Reproduzir um vídeo da Web

O `VideoPlayer` classe define um `Source` propriedade usada para especificar a origem do arquivo de vídeo, bem como um `AutoPlay` propriedade. `AutoPlay` tem uma configuração padrão de `true`, o que significa que o vídeo deve começar a reprodução automaticamente após `Source` foi definido:

```csharp
using System;
using Xamarin.Forms;

namespace FormsVideoLibrary
{
    public class VideoPlayer : View, IVideoPlayerController
    {
        ···
        // Source property
        public static readonly BindableProperty SourceProperty =
            BindableProperty.Create(nameof(Source), typeof(VideoSource), typeof(VideoPlayer), null);

        [TypeConverter(typeof(VideoSourceConverter))]
        public VideoSource Source
        {
            set { SetValue(SourceProperty, value); }
            get { return (VideoSource)GetValue(SourceProperty); }
        }

        // AutoPlay property
        public static readonly BindableProperty AutoPlayProperty =
            BindableProperty.Create(nameof(AutoPlay), typeof(bool), typeof(VideoPlayer), true);

        public bool AutoPlay
        {
            set { SetValue(AutoPlayProperty, value); }
            get { return (bool)GetValue(AutoPlayProperty); }
        }
        ···
    }
}
```

O `Source` propriedade é do tipo `VideoSource`, que é padronizado desde o xamarin. Forms [ `ImageSource` ](xref:Xamarin.Forms.ImageSource) abstrata de classe e seus três derivativos [ `UriImageSource` ](xref:Xamarin.Forms.UriImageSource), [ `FileImageSource` ](xref:Xamarin.Forms.FileImageSource), e [ `StreamImageSource` ](xref:Xamarin.Forms.StreamImageSource). Nenhuma opção de fluxo está disponível para o `VideoPlayer` no entanto, como iOS e Android não dão suporte a reprodução de um vídeo de um fluxo.

## <a name="video-sources"></a>Fontes de vídeo

Abstrata `VideoSource` classe consiste exclusivamente em três métodos estáticos que instanciar as três classes que derivam de `VideoSource`:

```csharp
namespace FormsVideoLibrary
{
    [TypeConverter(typeof(VideoSourceConverter))]
    public abstract class VideoSource : Element
    {
        public static VideoSource FromUri(string uri)
        {
            return new UriVideoSource() { Uri = uri };
        }

        public static VideoSource FromFile(string file)
        {
            return new FileVideoSource() { File = file };
        }

        public static VideoSource FromResource(string path)
        {
            return new ResourceVideoSource() { Path = path };
        }
    }
}
```

O `UriVideoSource` classe é usada para especificar um arquivo de vídeo para download com um URI. Ele define uma única propriedade do tipo `string`:

```csharp
namespace FormsVideoLibrary
{
    public class UriVideoSource : VideoSource
    {
        public static readonly BindableProperty UriProperty =
            BindableProperty.Create(nameof(Uri), typeof(string), typeof(UriVideoSource));

        public string Uri
        {
            set { SetValue(UriProperty, value); }
            get { return (string)GetValue(UriProperty); }
        }
    }
}
```

Manipulação de objetos do tipo `UriVideoSource` é descrito abaixo.

O `ResourceVideoSource` classe é usada para acessar os arquivos de vídeo que são armazenados como recursos incorporados no aplicativo de plataforma, também é especificado com um `string` propriedade:

```csharp
namespace FormsVideoLibrary
{
    public class ResourceVideoSource : VideoSource
    {
        public static readonly BindableProperty PathProperty =
            BindableProperty.Create(nameof(Path), typeof(string), typeof(ResourceVideoSource));

        public string Path
        {
            set { SetValue(PathProperty, value); }
            get { return (string)GetValue(PathProperty); }
        }
    }
}
```

Manipulação de objetos do tipo `ResourceVideoSource` é descrita no artigo [Carregando vídeos de recursos do aplicativo](loading-resources.md). O `VideoPlayer` classe não têm recursos para carregar um arquivo de vídeo armazenado como um recurso na biblioteca do .NET Standard.

O `FileVideoSource` classe é usada para acessar os arquivos de vídeo da biblioteca de vídeos do dispositivo. Também é a única propriedade do tipo `string`:

```csharp
namespace FormsVideoLibrary
{
    public class FileVideoSource : VideoSource
    {
        public static readonly BindableProperty FileProperty =
                  BindableProperty.Create(nameof(File), typeof(string), typeof(FileVideoSource));

        public string File
        {
            set { SetValue(FileProperty, value); }
            get { return (string)GetValue(FileProperty); }
        }
    }
}
```

Manipulação de objetos do tipo `FileVideoSource` é descrita no artigo [acessar a biblioteca de vídeos do dispositivo](accessing-library.md).

O `VideoSource` classe inclui um `TypeConverter` atributo que referencia `VideoSourceConverter`:

```csharp
namespace FormsVideoLibrary
{
    [TypeConverter(typeof(VideoSourceConverter))]
    public abstract class VideoSource : Element
    {
        ···
    }
}
```

Este conversor de tipo é invocado quando o `Source` estiver definida como uma cadeia de caracteres em XAML. Aqui está o `VideoSourceConverter` classe:

```csharp
namespace FormsVideoLibrary
{
    public class VideoSourceConverter : TypeConverter
    {
        public override object ConvertFromInvariantString(string value)
        {
            if (!String.IsNullOrWhiteSpace(value))
            {
                Uri uri;
                return Uri.TryCreate(value, UriKind.Absolute, out uri) && uri.Scheme != "file" ?
                                VideoSource.FromUri(value) : VideoSource.FromResource(value);
            }

            throw new InvalidOperationException("Cannot convert null or whitespace to ImageSource");
        }
    }
}
```

O `ConvertFromInvariantString` método tenta converter a cadeia de caracteres para um `Uri` objeto. Se houver êxito, e o esquema não é `file:`, em seguida, o método retorna um `UriVideoSource`. Caso contrário, retornará um `ResourceVideoSource`.

## <a name="setting-the-video-source"></a>Definir a fonte de vídeo

Toda a outra lógica que envolvem fontes de vídeo é implementada nos renderizadores de plataforma individual. As seções a seguir mostram como os renderizadores de plataforma reproduzir vídeos quando o `Source` estiver definida como um `UriVideoSource` objeto.

### <a name="the-ios-video-source"></a>A fonte de vídeo iOS

Duas seções do `VideoPlayerRenderer` estão envolvidas na configuração de fonte de vídeo do player de vídeo. Quando o xamarin. Forms cria primeiro um objeto do tipo `VideoPlayer`, o `OnElementChanged` método for chamado com o `NewElement` propriedade do objeto de argumentos definida como que `VideoPlayer`. O `OnElementChanged` chamadas de método `SetSource`:

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
                SetSource();
                ···
            }
        }

        protected override void OnElementPropertyChanged(object sender, PropertyChangedEventArgs args)
        {
            ···
            else if (args.PropertyName == VideoPlayer.SourceProperty.PropertyName)
            {
                SetSource();
            }
            ···
        }
        ···
    }
}
```

Mais tarde no, quando o `Source` propriedade for alterada, o `OnElementPropertyChanged` método é chamado com um `PropertyName` propriedade de "Origem", e `SetSource` é chamado novamente.

Para reproduzir um arquivo de vídeo no iOS, um objeto do tipo [ `AVAsset` ](https://developer.xamarin.com/api/type/AVFoundation.AVAsset/) é criado para encapsular o arquivo de vídeo, e que é usado para criar um [ `AVPlayerItem` ](https://developer.xamarin.com/api/type/AVFoundation.AVPlayerItem/), em seguida, que é transferido para o `AVPlayer`objeto. Aqui está como o `SetSource` identificadores de método de `Source` propriedade quando ela é do tipo `UriVideoSource`:

```csharp
namespace FormsVideoLibrary.iOS
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, UIView>
    {
        AVPlayer player;
        AVPlayerItem playerItem;
        ···
        void SetSource()
        {
            AVAsset asset = null;

            if (Element.Source is UriVideoSource)
            {
                string uri = (Element.Source as UriVideoSource).Uri;

                if (!String.IsNullOrWhiteSpace(uri))
                {
                    asset = AVAsset.FromUrl(new NSUrl(uri));
                }
            }
            ···
            if (asset != null)
            {
                playerItem = new AVPlayerItem(asset);
            }
            else
            {
                playerItem = null;
            }

            player.ReplaceCurrentItemWithPlayerItem(playerItem);

            if (playerItem != null && Element.AutoPlay)
            {
                player.Play();
            }
        }
        ···
    }
}
```

O `AutoPlay` propriedade não tem nenhuma comparação é pensar nas classes de vídeo iOS, portanto, a propriedade é examinada no final do `SetSource` método para chamar o `Play` método no `AVPlayer` objeto.

Em alguns casos, os vídeos continuação executada após a página com o `VideoPlayer` direcionado para a home page. Para interromper o vídeo, o `ReplaceCurrentItemWithPlayerItem` também é definido `Dispose` substituir:

```csharp
namespace FormsVideoLibrary.iOS
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, UIView>
    {
        ···
        protected override void Dispose(bool disposing)
        {
            base.Dispose(disposing);

            if (player != null)
            {
                player.ReplaceCurrentItemWithPlayerItem(null);
            }
        }
        ···
    }
}
```

### <a name="the-android-video-source"></a>A fonte de vídeo do Android

O Android `VideoPlayerRenderer` precisa definir a fonte do player de vídeo quando o `VideoPlayer` primeiro é criado e posterior quando a `Source` as alterações de propriedade:

```csharp
namespace FormsVideoLibrary.Droid
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, ARelativeLayout>
    {
        ···
        protected override void OnElementChanged(ElementChangedEventArgs<VideoPlayer> args)
        {
            ···
            if (args.NewElement != null)
            {
                ···
                SetSource();
                ···
            }
        }
        ···
        protected override void OnElementPropertyChanged(object sender, PropertyChangedEventArgs args)
        {
            ···
            else if (args.PropertyName == VideoPlayer.SourceProperty.PropertyName)
            {
                SetSource();
            }
            ···
        }
        ···
    }
}
```

O `SetSource` método lida com objetos do tipo `UriVideoSource` chamando `SetVideoUri` sobre o `VideoView` com um Android `Uri` objeto criado do URI da cadeia de caracteres. O `Uri` classe é totalmente qualificado aqui para distingui-lo a partir do .NET `Uri` classe:

```csharp
namespace FormsVideoLibrary.Droid
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, ARelativeLayout>
    {
        ···
        void SetSource()
        {
            isPrepared = false;
            bool hasSetSource = false;

            if (Element.Source is UriVideoSource)
            {
                string uri = (Element.Source as UriVideoSource).Uri;

                if (!String.IsNullOrWhiteSpace(uri))
                {
                    videoView.SetVideoURI(Android.Net.Uri.Parse(uri));
                    hasSetSource = true;
                }
            }
            ···

            if (hasSetSource && Element.AutoPlay)
            {
                videoView.Start();
            }
        }
        ···
    }
}
```

O Android `VideoView` não tem um correspondente `AutoPlay` propriedade, portanto, o `Start` método é chamado se um novo vídeo tiver sido definido.

Há uma diferença entre o comportamento do iOS e Android renderizadores, se o `Source` propriedade de `VideoPlayer` é definido como `null`, ou se o `Uri` propriedade de `UriVideoSource` é definido como `null` ou uma cadeia de caracteres em branco. Se o player de vídeo iOS estiver em execução no momento, um vídeo, e `Source` é definido como `null` (ou a cadeia de caracteres é `null` ou em branco), `ReplaceCurrentItemWithPlayerItem` é chamado com `null` valor. O vídeo atual é substituído e interrompe a execução.

Android não dá suporte a um recurso semelhante. Se o `Source` estiver definida como `null`, o `SetSource` método simplesmente ignorá-la, e o vídeo atual continua sendo reproduzido.

### <a name="the-uwp-video-source"></a>A fonte de vídeo de UWP

A UWP `MediaElement` define um `AutoPlay` propriedade, que é tratada no renderizador como qualquer outra propriedade:

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
                SetSource();
                SetAutoPlay();
                ···    
            }
        }

        protected override void OnElementPropertyChanged(object sender, PropertyChangedEventArgs args)
        {
            ···
            else if (args.PropertyName == VideoPlayer.SourceProperty.PropertyName)
            {
                SetSource();
            }
            else if (args.PropertyName == VideoPlayer.AutoPlayProperty.PropertyName)
            {
                SetAutoPlay();
            }
            ···
        }
        ···
    }
}
```

O `SetSource` identificadores de propriedade um `UriVideoSource` objeto definindo o `Source` propriedade do `MediaElement` para um .NET `Uri` valor, ou `null` se o `Source` propriedade de `VideoPlayer` está definido como `null`:

```csharp
namespace FormsVideoLibrary.UWP
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, MediaElement>
    {
        ···
        async void SetSource()
        {
            bool hasSetSource = false;

            if (Element.Source is UriVideoSource)
            {
                string uri = (Element.Source as UriVideoSource).Uri;

                if (!String.IsNullOrWhiteSpace(uri))
                {
                    Control.Source = new Uri(uri);
                    hasSetSource = true;
                }
            }
            ···
            if (!hasSetSource)
            {
                Control.Source = null;
            }
        }

        void SetAutoPlay()
        {
            Control.AutoPlay = Element.AutoPlay;
        }
        ···
    }
}
```

## <a name="setting-a-url-source"></a>Definindo uma fonte de URL

Com a implementação dessas propriedades em renderizadores de três, é possível reproduzir um vídeo de uma fonte de URL. O **reproduzir vídeo da Web** página de [ **VideoPlayDemos** ]( https://developer.xamarin.com/samples/xamarin-forms/customrenderers/videoplayerdemos/index.md) programa é definido pelo arquivo de XAML a seguir:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:video="clr-namespace:FormsVideoLibrary"
             x:Class="VideoPlayerDemos.PlayWebVideoPage"
             Title="Play Web Video">

    <video:VideoPlayer Source="https://archive.org/download/BigBuckBunny_328/BigBuckBunny_512kb.mp4" />

</ContentPage>
```

O `VideoSourceConverter` classe converte a cadeia de caracteres para um `UriVideoSource`. Quando você navega para o **reproduzir vídeo da Web** página, o vídeo começa carregando e inicia a execução quando uma quantidade suficiente de dados ter sido baixada e armazenada em buffer. O vídeo é cerca de 10 minutos de comprimento:

[![Reproduzir vídeo da Web](web-videos-images/playwebvideo-small.png "reproduzir vídeo da Web")](web-videos-images/playwebvideo-large.png#lightbox "reproduzir vídeo da Web")

Em cada um dos três plataformas, os controles de transporte desaparecer se eles não forem usados, mas podem ser restaurados para exibir, toque o vídeo.

Você pode impedir que o vídeo seja iniciado automaticamente definindo a `AutoPlay` propriedade para `false`:

```xaml
<video:VideoPlayer Source="https://archive.org/download/BigBuckBunny_328/BigBuckBunny_512kb.mp4"
                   AutoPlay="false" />
```

Será necessário pressionar o **reproduzir** botão para iniciar o vídeo.

Da mesma forma, você pode suprimir a exibição de controles de transporte, definindo o `AreTransportControlsEnabled` propriedade para `false`:

```xaml
<video:VideoPlayer Source="https://archive.org/download/BigBuckBunny_328/BigBuckBunny_512kb.mp4"
                   AreTransportControlsEnabled="False" />
```

Se você definir ambas as propriedades como `false`, em seguida, o vídeo não comece a executar, e não haverá nenhuma maneira para iniciá-lo! Você precisaria chamar `Play` do arquivo code-behind, ou criar seus próprios controles de transporte, conforme descrito no artigo [implementar controles de transporte de vídeo personalizada](custom-transport.md).

O **App. XAML** arquivo inclui recursos para dois vídeos adicionais:

```xaml
<Application xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:video="clr-namespace:FormsVideoLibrary"
             x:Class="VideoPlayerDemos.App">
    <Application.Resources>
        <ResourceDictionary>

            <video:UriVideoSource x:Key="ElephantsDream"
                                  Uri="https://archive.org/download/ElephantsDream/ed_hd_512kb.mp4" />

            <video:UriVideoSource x:Key="BigBuckBunny"
                                  Uri="https://archive.org/download/BigBuckBunny_328/BigBuckBunny_512kb.mp4" />

            <video:UriVideoSource x:Key="Sintel"
                                  Uri="https://archive.org/download/Sintel/sintel-2048-stereo_512kb.mp4" />

        </ResourceDictionary>
    </Application.Resources>
</Application>
```

Para fazer referência a uma desses outros filmes, você pode substituir a URL explícita na **PlayWebVideo.xaml** do arquivo com um `StaticResource` extensão de marcação, caso em que `VideoSourceConverter` não é necessário para criar o `UriVideoSource` objeto:

```xaml
<video:VideoPlayer Source="{StaticResource ElephantsDream}" />
```

Como alternativa, você pode definir as `Source` propriedade de um arquivo de vídeo em um `ListView`, conforme descrito no próximo artigo, [associação de fontes de vídeo ao Player](source-bindings.md).





## <a name="related-links"></a>Links relacionados

- [Demonstrações do Player de vídeo (amostra)](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/VideoPlayerDemos/)
