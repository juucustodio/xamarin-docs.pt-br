---
title: Reproduzindo um vídeo da Web
description: Este artigo explica como reproduzir vídeos da Web em um aplicativo de player de vídeo usando o Xamarin.Forms.
ms.prod: xamarin
ms.assetid: 75781A10-865D-4BA8-8D6B-E3DA012922BC
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/12/2018
ms.openlocfilehash: 9beff615c39fc34b5a58a93d309bb20543cad77f
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68650413"
---
# <a name="playing-a-web-video"></a>Reproduzindo um vídeo da Web

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-videoplayerdemos)

A classe `VideoPlayer` define uma propriedade `Source` usada para especificar a origem do arquivo de vídeo, bem como uma propriedade `AutoPlay`. `AutoPlay` tem uma configuração padrão igual a `true`, o que significa que o vídeo deve iniciar a reprodução automaticamente após a definição de `Source`:

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

A propriedade `Source` é do tipo `VideoSource`, que é padronizado após a classe abstrata [`ImageSource`](xref:Xamarin.Forms.ImageSource) do Xamarin.Forms e seus três derivados [`UriImageSource`](xref:Xamarin.Forms.UriImageSource), [`FileImageSource`](xref:Xamarin.Forms.FileImageSource) e [`StreamImageSource`](xref:Xamarin.Forms.StreamImageSource). No entanto, nenhuma opção de fluxo está disponível para o `VideoPlayer`, pois o iOS e o Android não dão suporte à reprodução de um vídeo de um fluxo.

## <a name="video-sources"></a>Fontes de vídeo

A classe abstrata `VideoSource` consiste exclusivamente em três métodos estáticos que criam uma instância das três classes que derivam de `VideoSource`:

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

A classe `UriVideoSource` é usada para especificar um arquivo de vídeo para download com um URI. Ela define uma única propriedade do tipo `string`:

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

A manipulação de objetos do tipo `UriVideoSource` é descrita abaixo.

A classe `ResourceVideoSource` é usada para acessar os arquivos de vídeo armazenados como recursos inseridos no aplicativo da plataforma, também especificado com uma propriedade `string`:

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

A manipulação de objetos do tipo `ResourceVideoSource` é descrita no artigo [Carregando vídeos de recursos do aplicativo](loading-resources.md). A classe `VideoPlayer` não têm recursos para carregar um arquivo de vídeo armazenado como um recurso na biblioteca do .NET Standard.

A classe `FileVideoSource` é usada para acessar os arquivos de vídeo da biblioteca de vídeos do dispositivo. A única propriedade também é do tipo `string`:

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

A manipulação de objetos do tipo `FileVideoSource` é descrita no artigo [Acessando a biblioteca de vídeos do dispositivo](accessing-library.md).

A classe `VideoSource` inclui um atributo `TypeConverter` que referencia `VideoSourceConverter`:

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

Este conversor de tipo é invocado quando a propriedade `Source` é definida como uma cadeia de caracteres em XAML. Esta é a classe `VideoSourceConverter`:

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

O método `ConvertFromInvariantString` tenta converter a cadeia de caracteres em um objeto `Uri`. Se houver êxito e o esquema não for `file:`, o método retornará uma `UriVideoSource`. Caso contrário, ele retornará uma `ResourceVideoSource`.

## <a name="setting-the-video-source"></a>Definindo a fonte de vídeo

Toda a outra lógica que envolve fontes de vídeo é implementada nos renderizadores de plataforma individual. As seções a seguir mostram como os renderizadores de plataforma reproduzem vídeos quando a propriedade `Source` é definida como um objeto `UriVideoSource`.

### <a name="the-ios-video-source"></a>A fonte de vídeo do iOS

Duas seções do `VideoPlayerRenderer` estão envolvidas na definição da fonte de vídeo do player de vídeo. Quando o Xamarin.Forms cria pela primeira vez um objeto do tipo `VideoPlayer`, o método `OnElementChanged` é chamado com a propriedade `NewElement` do objeto de argumentos definida como esse `VideoPlayer`. O método `OnElementChanged` chama `SetSource`:

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

Posteriormente, quando a propriedade `Source` é alterada, o método `OnElementPropertyChanged` é chamado com uma propriedade `PropertyName` igual a "Origem", e `SetSource` é chamado novamente.

Para reproduzir um arquivo de vídeo no iOS, um objeto do tipo [`AVAsset`](xref:AVFoundation.AVAsset) é criado primeiro para encapsular o arquivo de vídeo e ele é usado para criar um [`AVPlayerItem`](xref:AVFoundation.AVPlayerItem), que é então transferido para o objeto `AVPlayer`. É desta forma que o método `SetSource` manipula a propriedade `Source` quando ela é do tipo `UriVideoSource`:

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

A propriedade `AutoPlay` não tem nenhuma comparação nas classes de vídeo do iOS e, portanto, a propriedade é examinada no final do método `SetSource` para chamar o método `Play` no objeto `AVPlayer`.

Em alguns casos, os vídeos continuaram a execução depois que a página com o `VideoPlayer` foi direcionada para a home page. Para interromper o vídeo, o `ReplaceCurrentItemWithPlayerItem` também é definido na substituição `Dispose`:

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

O `VideoPlayerRenderer` do Android precisa definir a fonte do player de vídeo quando o `VideoPlayer` é criado pela primeira vez e, posteriormente, quando a propriedade `Source` é alterada:

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

O método `SetSource` manipula objetos do tipo `UriVideoSource` chamando `SetVideoUri` na `VideoView` com um objeto `Uri` do Android criado com base no URI da cadeia de caracteres. A classe `Uri` é totalmente qualificada aqui para distingui-la da classe `Uri` do .NET:

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

O `VideoView` do Android não tem uma propriedade `AutoPlay` correspondente e, portanto, o método `Start` é chamado se um novo vídeo foi definido.

Há uma diferença entre o comportamento dos renderizadores do iOS e do Android se a propriedade `Source` de `VideoPlayer` é definida como `null` ou se a propriedade `Uri` de `UriVideoSource` é definida como `null` ou uma cadeia de caracteres em branco. Se o player de vídeo do iOS estiver reproduzindo um vídeo no momento e `Source` for definido como `null` (ou a cadeia de caracteres for `null` ou em branco), `ReplaceCurrentItemWithPlayerItem` será chamado com o valor `null`. O vídeo atual será substituído e a execução será interrompida.

O Android não dá suporte a um recurso semelhante. Se a propriedade `Source` estiver definida como `null`, o método `SetSource` apenas a ignorará e o vídeo atual continuará sendo reproduzido.

### <a name="the-uwp-video-source"></a>A fonte de vídeo do UWP

O `MediaElement` do UWP define uma propriedade `AutoPlay`, que é manipulada no renderizador como qualquer outra propriedade:

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

A propriedade `SetSource` manipula um objeto `UriVideoSource` definindo a propriedade `Source` de `MediaElement` como um valor `Uri` do .NET ou como `null` se a propriedade `Source` de `VideoPlayer` está definida como `null`:

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

Com a implementação dessas propriedades nos três renderizadores, é possível reproduzir um vídeo de uma fonte de URL. A página **Reproduzir um vídeo da Web** no programa [**VideoPlayDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-videoplayerdemos) é definida pelo seguinte arquivo XAML:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:video="clr-namespace:FormsVideoLibrary"
             x:Class="VideoPlayerDemos.PlayWebVideoPage"
             Title="Play Web Video">

    <video:VideoPlayer Source="https://archive.org/download/BigBuckBunny_328/BigBuckBunny_512kb.mp4" />

</ContentPage>
```

A classe `VideoSourceConverter` converte a cadeia de caracteres em uma `UriVideoSource`. Quando você navega para a página **Reproduzir um vídeo da Web**, o vídeo começa a ser carregado e inicia a execução quando uma quantidade suficiente de dados é baixada e armazenada em buffer. O vídeo tem cerca de 10 minutos:

[![Reproduzir um vídeo da Web](web-videos-images/playwebvideo-small.png "Reproduzir um vídeo da Web")](web-videos-images/playwebvideo-large.png#lightbox "Reproduzir um vídeo da Web")

Em cada uma das plataformas, os controles de transporte esmaecem se não são usados, mas podem ser restaurados para exibição com um toque no vídeo.

Você pode impedir que o vídeo seja iniciado automaticamente definindo a propriedade `AutoPlay` como `false`:

```xaml
<video:VideoPlayer Source="https://archive.org/download/BigBuckBunny_328/BigBuckBunny_512kb.mp4"
                   AutoPlay="false" />
```

Você precisará pressionar o botão **Reproduzir** para iniciar o vídeo.

Da mesma forma, você pode suprimir a exibição dos controles de transporte definindo a propriedade `AreTransportControlsEnabled` como `false`:

```xaml
<video:VideoPlayer Source="https://archive.org/download/BigBuckBunny_328/BigBuckBunny_512kb.mp4"
                   AreTransportControlsEnabled="False" />
```

Se você definir ambas as propriedades como `false`, o vídeo não começará a execução e não haverá nenhuma maneira de iniciá-lo. Você precisará chamar `Play` no arquivo code-behind ou criar seus próprios controles de transporte, conforme descrito no artigo [Implementando controles de transporte de vídeo personalizados](custom-transport.md).

O arquivo **App.xaml** inclui recursos para dois vídeos adicionais:

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

Para referenciar um desses outros filmes, substitua a URL explícita no arquivo **PlayWebVideo.xaml** com uma extensão de marcação `StaticResource`. Nesse caso, `VideoSourceConverter` não é necessário para criar o objeto `UriVideoSource`:

```xaml
<video:VideoPlayer Source="{StaticResource ElephantsDream}" />
```

Como alternativa, você pode definir a propriedade `Source` em um arquivo de vídeo em uma `ListView`, conforme descrito no próximo artigo, [Associando fontes de vídeo ao player](source-bindings.md).





## <a name="related-links"></a>Links relacionados

- [Demonstrações do player de vídeo (amostra)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-videoplayerdemos)
