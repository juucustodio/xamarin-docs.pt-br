---
title: Carregando vídeos de recursos do aplicativo
description: Este artigo explica como carregar vídeos armazenados como recursos do aplicativo em um aplicativo de player de vídeo, usando o xamarin. Forms.
ms.prod: xamarin
ms.assetid: F75BD540-9354-4C17-A119-57F3DEC66D54
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/12/2018
ms.openlocfilehash: 17e9e7061e4329431a0f34abdbbb616a1aff1b43
ms.sourcegitcommit: 5fc171a45697f7c610d65f74d1f3cebbac445de6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2018
ms.locfileid: "52171320"
---
# <a name="loading-application-resource-videos"></a>Carregando vídeos de recursos do aplicativo

Os renderizadores personalizados para o `VideoPlayer` exibição são capazes de reproduzir arquivos de vídeo que foram inseridos nos projetos de plataforma individual como recursos do aplicativo. No entanto, a versão atual do `VideoPlayer` não é possível acessar os recursos incorporados em uma biblioteca .NET Standard.

Para carregar esses recursos, crie uma instância do `ResourceVideoSource` definindo o `Path` propriedade para o nome do arquivo (ou a pasta e nome de arquivo) do recurso. Como alternativa, você pode chamar estático `VideoSource.FromResource` método para fazer referência ao recurso. Em seguida, defina as `ResourceVideoSource` do objeto para o `Source` propriedade de `VideoPlayer`.

## <a name="storing-the-video-files"></a>Armazenar os arquivos de vídeo

Armazenar um arquivo de vídeo no projeto de plataforma é diferente para cada plataforma.

### <a name="ios-video-resources"></a>recursos de vídeo iOS

Em um projeto do iOS, você pode armazenar um vídeo na **recursos** pasta, ou em uma subpasta de **recursos** pasta. O arquivo de vídeo deve ter uma `Build Action` de `BundleResource`. Defina a `Path` propriedade de `ResourceVideoSource` para o nome do arquivo, por exemplo, **MyFile.mp4** para um arquivo no **recursos** pasta, ou **MyFolder/MyFile.mp4**, em que **MyFolder** é uma subpasta da **recursos**.

No **VideoPlayerDemos** solução, o **VideoPlayerDemos.iOS** projeto contém uma subpasta da **recursos** denominado **vídeos** que contém um arquivo chamado **iOSApiVideo.mp4**. Esse é um breve vídeo que mostra como usar o site do Xamarin para localizar a documentação para o iOS `AVPlayerViewController` classe.

### <a name="android-video-resources"></a>Recursos de vídeo Android

Em um projeto do Android, os vídeos devem ser armazenados em uma subpasta da **recursos** denominado **brutos**. O **brutos** pasta não pode conter subpastas. Dê ao arquivo de vídeo uma `Build Action` de `AndroidResource`. Defina as `Path` propriedade de `ResourceVideoSource` para o nome do arquivo, por exemplo, **MyFile.mp4**.

O **VideoPlayerDemos.Android** projeto contém uma subpasta da **recursos** denominada **brutos**, que contém um arquivo chamado **AndroidApiVideo.mp4**.

### <a name="uwp-video-resources"></a>Recursos de vídeo de UWP

Em um projeto da plataforma Universal do Windows, você pode armazenar vídeos em qualquer pasta no projeto. Dê ao arquivo de um `Build Action` de `Content`. Defina as `Path` propriedade de `ResourceVideoSource` para a pasta e o nome de arquivo, por exemplo, **MyFolder/MyVideo.mp4**.

O **VideoPlayerDemos.UWP** projeto contém uma pasta chamada **vídeos** com o arquivo **UWPApiVideo.mp4**.

## <a name="loading-the-video-files"></a>Ao carregar os arquivos de vídeo

Cada uma das classes de renderizador plataforma contém código em seu `SetSource` método de carregamento de arquivos de vídeo armazenados como recursos.

### <a name="ios-resource-loading"></a>carregamento de recursos do iOS

A versão do iOS dos `VideoPlayerRenderer` usa o `GetUrlForResource` método `NSBundle` para carregar o recurso. O caminho completo deve ser dividido em um nome de arquivo, a extensão e o diretório. O código usa o `Path` classe no .NET `System.IO` namespace para dividir o caminho do arquivo nesses componentes:

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

O Android `VideoPlayerRenderer` usa o nome do pacote e nome de arquivo para construir um `Uri` objeto. O nome do pacote é o nome do aplicativo, neste caso **VideoPlayerDemos.Android**, que pode ser obtida da estático `Context.PackageName` propriedade. O resultante `Uri` objeto é passado para o `SetVideoURI` método `VideoView`:

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

A UWP `VideoPlayerRenderer` constrói uma `Uri` objeto para o caminho e o configura para o `Source` propriedade do `MediaElement`:

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

O **reproduzir vídeo Resource** página na **VideoPlayerDemos** solução usa o `OnPlatform` classe para especificar o arquivo de vídeo para cada plataforma:

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

Se o recurso do iOS é armazenado na **recursos** pasta, e se os recursos UWP são armazenados na pasta raiz do projeto, você pode usar o mesmo nome de arquivo para cada plataforma. Se esse for o caso, você pode definir esse nome diretamente para o `Source` propriedade de `VideoPlayer`.

Aqui está a página em execução:

[![Reproduzir vídeo Resource](loading-resources-images/playvideoresource-small.png "reproduzir vídeo Resource")](loading-resources-images/playvideoresource-large.png#lightbox "reproduzir vídeo recursos")

Agora você já viu como [carregar vídeos de um URI de Web](web-videos.md) e como reproduzir recursos inseridos. Além disso, você pode [carregar vídeos da biblioteca de vídeos do dispositivo](accessing-library.md).


## <a name="related-links"></a>Links relacionados

- [Demonstrações do Player de vídeo (amostra)](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/VideoPlayerDemos/)
