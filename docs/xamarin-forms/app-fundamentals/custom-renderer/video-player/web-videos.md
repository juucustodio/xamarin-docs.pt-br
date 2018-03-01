---
title: "Reprodução de um vídeo de Web"
ms.topic: article
ms.prod: xamarin
ms.assetid: CE9E955D-A9AC-4019-A5D7-6390D80DECA1
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/12/2018
ms.openlocfilehash: 2e44d32f199d5c85bbae242d9168a43e028c73d9
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="playing-a-web-video"></a>Reprodução de um vídeo de Web

O `VideoPlayer` classe define um `Source` propriedade usada para especificar a origem do arquivo de vídeo, bem como um `AutoPlay` propriedade. `AutoPlay` tem uma configuração padrão de `true`, o que significa que o vídeo deve começar a executar automaticamente após `Source` foi definida:

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

O `Source` é de propriedade do tipo `VideoSource`, que está em conformidade com o xamarin. Forms [ `ImageSource` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ImageSource/) abstrato de classe e seus três derivados, [ `UriImageSource` ](https://developer.xamarin.com/api/type/Xamarin.Forms.UriImageSource/), [ `FileImageSource` ](https://developer.xamarin.com/api/type/Xamarin.Forms.FileImageSource/), e [ `StreamImageSource` ](https://developer.xamarin.com/api/type/Xamarin.Forms.StreamImageSource/). Nenhuma opção de fluxo está disponível para o `VideoPlayer` no entanto, como iOS e Android não dão suporte a reprodução de um vídeo de um fluxo.

## <a name="video-sources"></a>Fontes de vídeo

O resumo `VideoSource` classe consiste exclusivamente em três métodos estáticos que instancia as três classes que derivam de `VideoSource`:

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

O `UriVideoSource` classe é usada para especificar um arquivo de vídeo para download com um URI. Define uma única propriedade de tipo `string`:

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

Manipulação de objetos do tipo `ResourceVideoSource` é descrita no artigo [vídeos de recurso de aplicativo ao carregar](loading-resources.md). O `VideoPlayer` classe não possui nenhum recurso para carregar um arquivo de vídeo armazenado como um recurso na biblioteca de classes portátil.

O `FileVideoSource` classe é usada para acessar arquivos de vídeo da biblioteca de vídeos do dispositivo. A única propriedade também é do tipo `string`:

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

O `ConvertFromInvariantString` método tenta converter a cadeia de caracteres para um `Uri` objeto. Se houver êxito, e o esquema não é `file:`, em seguida, o método retorna um `UriVideoSource`. Caso contrário, ele retorna um `ResourceVideoSource`.

## <a name="setting-the-video-source"></a>Definir a fonte de vídeo

Toda a outra lógica que envolvem a fontes de vídeo é implementada em renderizadores de plataforma individual. As seções a seguir mostram como os renderizadores de plataforma reproduzir vídeos quando o `Source` está definida como um `UriVideoSource` objeto.

### <a name="the-ios-video-source"></a>A fonte de vídeo iOS

Duas seções do `VideoPlayerRenderer` estão envolvidas na definição de fonte de vídeo do player de vídeo. Quando o xamarin. Forms cria primeiro um objeto do tipo `VideoPlayer`, o `OnElementChanged` método for chamado com o `NewElement` propriedade do objeto argumentos definida para que `VideoPlayer`. O `OnElementChanged` chamadas de método `SetSource`:

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

Mais tarde no, quando o `Source` propriedade for alterada, o `OnElementPropertyChanged` método for chamado com um `PropertyName` propriedade de "Origem", e `SetSource` é chamado novamente.

Para reproduzir um arquivo de vídeo no iOS, um objeto do tipo [ `AVAsset` ](https://developer.xamarin.com/api/type/AVFoundation.AVAsset/) é criada para encapsular o arquivo de vídeo, e que é usado para criar um [ `AVPlayerItem` ](https://developer.xamarin.com/api/type/AVFoundation.AVPlayerItem/), em seguida, que é transferido para o `AVPlayer`objeto. Aqui está como o `SetSource` método trata o `Source` quando ela é do tipo de propriedade `UriVideoSource`:

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

O `AutoPlay` propriedade não possui nenhum análogo nas classes vídeo iOS, para a propriedade é examinada no final do `SetSource` método para chamar o `Play` método no `AVPlayer` objeto.

Em alguns casos, vídeos continuassem a execução após a página com o `VideoPlayer` navegar de volta para a home page. Para interromper o vídeo, o `ReplaceCurrentItemWithPlayerItem` também é definido `Dispose` substituir:

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

### <a name="the-android-video-source"></a>A fonte de vídeo Android

O Android `VideoPlayerRenderer` precisa definir a fonte do player de vídeo quando o `VideoPlayer` primeiro é criado e posteriormente quando o `Source` alterações de propriedade:

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

O `SetSource` método manipular objetos do tipo `UriVideoSource` chamando `SetVideoUri` no `VideoView` com um Android `Uri` objeto criado a partir da URI de cadeia de caracteres. O `Uri` classe totalmente qualificada aqui para distingui-lo de .NET `Uri` classe:

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

O Android `VideoView` não tem um `AutoPlay` propriedade, portanto, o `Start` método é chamado se um novo vídeo tiver sido definido.

Há uma diferença entre o comportamento do iOS e Android renderizadores se o `Source` propriedade `VideoPlayer` é definido como `null`, ou se o `Uri` propriedade de `UriVideoSource` é definido como `null` ou uma cadeia de caracteres em branco. Se o player de vídeo iOS estiver em execução no momento um vídeo e `Source` é definido como `null` (ou a cadeia de caracteres é `null` ou em branco), `ReplaceCurrentItemWithPlayerItem` é chamado com `null` valor. O vídeo atual é substituído e interrompe a execução.

Android não oferece suporte a um recurso semelhante. Se o `Source` está definida como `null`, o `SetSource` método simplesmente ignora a ele e o vídeo atual continua sendo reproduzido.

### <a name="the-uwp-video-source"></a>A fonte de vídeo de UWP

O UWP `MediaElement` define um `AutoPlay` propriedade, que é tratada no renderizador como qualquer outra propriedade:

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

O `SetSource` identificadores de propriedade um `UriVideoSource` objeto definindo o `Source` propriedade de `MediaElement` para .NET `Uri` valor, ou `null` se o `Source` propriedade de `VideoPlayer` é definido como `null`:

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

Com a implementação dessas propriedades em renderizadores de três, é possível executar um vídeo de uma fonte de URL. O **reproduzir o vídeo de Web** página o [ **VideoPlayDemos** ]( https://developer.xamarin.com/samples/xamarin-forms/customrenderers/videoplayerdemos/index.md) programa é definido no arquivo XAML a seguir:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:video="clr-namespace:FormsVideoLibrary"
             x:Class="VideoPlayerDemos.PlayWebVideoPage"
             Title="Play Web Video">

    <video:VideoPlayer Source="https://archive.org/download/BigBuckBunny_328/BigBuckBunny_512kb.mp4" />

</ContentPage>
```

O `VideoSourceConverter` classe converte a cadeia de caracteres para um `UriVideoSource`. Quando você navega para o **reproduzir o vídeo do Web** página, o vídeo começa a carregar e inicia a execução quando uma quantidade suficiente de dados foi baixada e armazenado em buffer. O vídeo é de cerca de 10 minutos de comprimento:

[![Reproduzir o vídeo do Web](web-videos-images/playwebvideo-small.png "reproduzir o vídeo do Web")](web-videos-images/playwebvideo-large.png "reproduzir o vídeo de Web")

Em cada um dos três plataformas, os controles de transporte desaparecer se não for usados, mas pode ser restaurados para exibir tocando o vídeo.

Você pode impedir que o vídeo seja iniciado automaticamente, definindo o `AutoPlay` propriedade `false`:

```xaml
<video:VideoPlayer Source="https://archive.org/download/BigBuckBunny_328/BigBuckBunny_512kb.mp4"
                   AutoPlay="false" />
```

Será necessário pressionar o **reproduzir** botão para iniciar o vídeo.

Da mesma forma, você pode suprimir a exibição dos controles de transporte, definindo o `AreTransportControlsEnabled` propriedade `false`:

```xaml
<video:VideoPlayer Source="https://archive.org/download/BigBuckBunny_328/BigBuckBunny_512kb.mp4"
                   AreTransportControlsEnabled="False" />
```

Se você definir duas propriedades como `false`, o vídeo não começar a execução e não haverá nenhuma maneira de iniciá-lo! Você precisa chamar `Play` de arquivo code-behind, ou para criar seus próprios controles de transporte, conforme descrito no artigo [implementar controles de transporte de vídeo personalizados](custom-transport.md). 

O **App** arquivo inclui recursos para dois vídeos adicionais:

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

A referência de um desses outros filmes, você pode substituir o URL explícito no **PlayWebVideo.xaml** arquivo com um `StaticResource` extensão de marcação, caso em que `VideoSourceConverter` não é necessário para criar o `UriVideoSource` objeto:

```xaml
<video:VideoPlayer Source="{StaticResource ElephantsDream}" />
```

Como alternativa, você pode definir o `Source` propriedade de um arquivo de vídeo em um `ListView`, conforme descrito no próximo artigo, [fontes de vídeo de associação para o Player](source-bindings.md).





## <a name="related-links"></a>Links relacionados

- [Demonstrações em vídeo Player (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/VideoPlayerDemos/)
