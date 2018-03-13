---
title: "Vídeos de recursos do aplicativo de carregamento"
ms.topic: article
ms.prod: xamarin
ms.assetid: F75BD540-9354-4C17-A119-57F3DEC66D54
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/12/2018
ms.openlocfilehash: 08d07a82651887c9d87b908acd82296a3d80e43f
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2018
---
# <a name="loading-application-resource-videos"></a>Vídeos de recursos do aplicativo de carregamento

Os renderizadores personalizados para o `VideoPlayer` exibição são capazes de reproduzir arquivos de vídeo inseridos nos projetos individuais de plataforma como recursos do aplicativo. No entanto, a versão atual do `VideoPlayer` não é possível acessar os recursos inseridos em uma biblioteca de classes portátil.

Para carregar esses recursos, criar uma instância de `ResourceVideoSource` definindo o `Path` propriedade para o nome de arquivo (ou a pasta e nome de arquivo) do recurso. Como alternativa, você pode chamar estático `VideoSource.FromResource` método para fazer referência ao recurso. Em seguida, defina o `ResourceVideoSource` o objeto para o `Source` propriedade `VideoPlayer`. 

## <a name="storing-the-video-files"></a>Armazenar os arquivos de vídeo

Armazenar um arquivo de vídeo no projeto de plataforma é diferente para as três plataformas:

### <a name="ios-video-resources"></a>recursos de vídeo iOS

Em um projeto do iOS, você pode armazenar um vídeo no **recursos** pasta ou subpasta do **recursos** pasta. O arquivo de vídeo deve ter uma `Build Action` de `BundleResource`. Definir o `Path` propriedade de `ResourceVideoSource` o nome do arquivo, por exemplo, **MyFile.mp4** para um arquivo no **recursos** pasta, ou **MyFolder/MyFile.mp4**, onde **minhapasta** é uma subpasta da **recursos**.

No **VideoPlayerDemos** solução, o **VideoPlayerDemos.iOS** projeto contém uma subpasta da **recursos** chamado **vídeos** que contém um arquivo chamado **iOSApiVideo.mp4**. Este é um vídeo curto mostra como usar o site Xamarin para localizar a documentação para o iOS `AVPlayerViewController` classe.

### <a name="android-video-resources"></a>Recursos de vídeo Android

Em um projeto Android, vídeos devem ser armazenados em uma subpasta da **recursos** chamado **bruto**. O **bruto** pasta não pode conter subpastas. Dê ao arquivo de vídeo uma `Build Action` de `AndroidResource`. Definir o `Path` propriedade `ResourceVideoSource` o nome do arquivo, por exemplo, **MyFile.mp4**. 

O **VideoPlayerDemos.Android** projeto contém uma subpasta da **recursos** chamado **bruto**, que contém um arquivo chamado **AndroidApiVideo.mp4**. 

### <a name="uwp-video-resources"></a>Recursos de vídeo de UWP

Em um projeto de plataforma Universal do Windows, você pode armazenar vídeos em qualquer pasta do projeto. Dê ao arquivo um `Build Action` de `Content`. Definir o `Path` propriedade `ResourceVideoSource` para a pasta e o nome de arquivo, por exemplo, **MyFolder/MyVideo.mp4**. 

O **VideoPlayerDemos.UWP** projeto contém uma pasta chamada **vídeos** com o arquivo **UWPApiVideo.mp4**.

## <a name="loading-the-video-files"></a>Carregando os arquivos de vídeo

Cada uma das classes de renderizador plataforma contém código em seu `SetSource` método de carregamento de arquivos de vídeo armazenados como recursos.

### <a name="ios-resource-loading"></a>carregamento de recursos do iOS

A versão do iOS de `VideoPlayerRenderer` usa o `GetUrlForResource` método `NSBundle` para carregar o recurso. O caminho completo deve ser dividido em um nome de arquivo, a extensão e o diretório. O código usa o `Path` classe no .NET `System.IO` namespace para dividir o caminho do arquivo nesses componentes:

```csharp
namespace FormsVideoLibrary.iOS
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, UIView>
    {
        ···
        void SetSource()
        {
            AVAsset asset = null;
            ···
            else if (Element.Source is ResourceVideoSource)
            {
                string path = (Element.Source as ResourceVideoSource).Path;

                if (!String.IsNullOrWhitespace(path))
                {
                    string directory = Path.GetDirectoryName(path);
                    string filename = Path.GetFileNameWithoutExtension(path);
                    string extension = Path.GetExtension(path).Substring(1);
                    NSUrl url = NSBundle.MainBundle.GetUrlForResource(filename, extension, directory);
                    asset = AVAsset.FromUrl(url);
                }
            }
            ···
        }
        ···
    }
}
```

### <a name="android-resource-loading"></a>Carregamento de recursos do Android

O Android `VideoPlayerRenderer` usa o nome do pacote e nome de arquivo para construir um `Uri` objeto. O nome do pacote é o nome do aplicativo, nesse caso **VideoPlayerDemos.Android**, que pode ser obtido a estática `Context.PackageName` propriedade. O RSoP `Uri` objeto é transmitido para o `SetVideoURI` método `VideoView`:

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
            ···
            else if (Element.Source is ResourceVideoSource)
            {
                string package = Context.PackageName;
                string path = (Element.Source as ResourceVideoSource).Path;

                if (!String.IsNullOrWhiteSpace(path))
                {
                    string filename = Path.GetFileNameWithoutExtension(path).ToLowerInvariant();
                    string uri = "android.resource://" + package + "/raw/" + filename;
                    videoView.SetVideoURI(Android.Net.Uri.Parse(uri));
                    hasSetSource = true;
                }
            }
            ···
        }
        ···
    }
}
```

### <a name="uwp-resource-loading"></a>Carregamento de recursos UWP

O UWP `VideoPlayerRenderer` constrói um `Uri` objeto para o caminho e o configura para o `Source` propriedade `MediaElement`:

```csharp
namespace FormsVideoLibrary.UWP
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, MediaElement>
    {
        ···
        async void SetSource()
        {
            bool hasSetSource = false;
            ···
            else if (Element.Source is ResourceVideoSource)
            {
                string path = "ms-appx:///" + (Element.Source as ResourceVideoSource).Path;

                if (!String.IsNullOrWhiteSpace(path))
                {
                    Control.Source = new Uri(path);
                    hasSetSource = true;
                }
            }
        }
        ···
    }
}
```

## <a name="playing-the-resource-file"></a>A execução do arquivo de recurso

O **reproduzir o vídeo recurso** página o **VideoPlayerDemos** solução usa o `OnPlatform` classe para especificar o arquivo de vídeo para cada plataforma:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:video="clr-namespace:FormsVideoLibrary"
             x:Class="VideoPlayerDemos.PlayVideoResourcePage"
             Title="Play Video Resource">
    <video:VideoPlayer>
        <video:VideoPlayer.Source>
            <video:ResourceVideoSource>
                <video:ResourceVideoSource.Path>
                    <OnPlatform x:TypeArguments="x:String">
                        <On Platform="iOS" Value="Videos/iOSApiVideo.mp4" />
                        <On Platform="Android" Value="AndroidApiVideo.mp4" />
                        <On Platform="UWP" Value="Videos/UWPApiVideo.mp4" />
                    </OnPlatform>
                </video:ResourceVideoSource.Path>
            </video:ResourceVideoSource>
        </video:VideoPlayer.Source>
    </video:VideoPlayer>
</ContentPage>
```

Se o recurso do iOS é armazenado na **recursos** pasta, e se os recursos UWP são armazenados na pasta raiz do projeto, você pode usar o mesmo nome de arquivo para as plataformas de três. Se esse for o caso, você pode definir esse nome diretamente para o `Source` propriedade `VideoPlayer`. 

Aqui está a página em execução em plataformas de três:

[![Reproduzir o vídeo recurso](loading-resources-images/playvideoresource-small.png "reproduzir o vídeo recurso")](loading-resources-images/playvideoresource-large.png#lightbox "reproduzir o vídeo recursos")

Você viu como [carregar vídeos de um URI de Web](web-videos.md) e como reproduzir recursos inseridos. Além disso, você pode [carregar vídeos da biblioteca de vídeos do dispositivo](accessing-library.md).


## <a name="related-links"></a>Links relacionados

- [Demonstrações em vídeo Player (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/VideoPlayerDemos/)
