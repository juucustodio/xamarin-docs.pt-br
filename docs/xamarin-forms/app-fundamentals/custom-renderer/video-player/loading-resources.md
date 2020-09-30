---
title: Carregando vídeos de recursos do aplicativo
description: Este artigo explica como carregar vídeos armazenados como recursos de aplicativo em um aplicativo de player de vídeo, usando o Xamarin.Forms .
ms.prod: xamarin
ms.assetid: F75BD540-9354-4C17-A119-57F3DEC66D54
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/12/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: a1a0febfdb38e577363ba59e8d0a5ceda9464a26
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91563491"
---
# <a name="loading-application-resource-videos"></a>Carregando vídeos de recursos do aplicativo

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-videoplayerdemos)

Os renderizadores personalizados para a exibição `VideoPlayer` podem reproduzir arquivos de vídeo que foram inseridos nos projetos da plataforma individual como recursos do aplicativo. No entanto, a versão atual do `VideoPlayer` não pode acessar os recursos inseridos em uma biblioteca do .NET Standard.

Para carregar esses recursos, crie uma instância da `ResourceVideoSource` definindo a propriedade `Path` com o nome de arquivo (ou o nome de arquivo e a pasta) do recurso. Como alternativa, você pode chamar o método estático `VideoSource.FromResource` para referenciar o recurso. Em seguida, defina o objeto `ResourceVideoSource` como a propriedade `Source` de `VideoPlayer`.

## <a name="storing-the-video-files"></a>Armazenando os arquivos de vídeo

O armazenamento de um arquivo de vídeo no projeto da plataforma é diferente para cada plataforma.

### <a name="ios-video-resources"></a>Recursos de vídeo do iOS

Em um projeto do iOS, você pode armazenar um vídeo na pasta **Resources** ou em uma subpasta da pasta **Resources**. O arquivo de vídeo precisa ter uma `Build Action` igual a `BundleResource`. Defina a propriedade `Path` de `ResourceVideoSource` com o nome doe arquivo, por exemplo, **MyFile.mp4** para um arquivo na pasta **Resources**, ou **MyFolder/MyFile.mp4**, em que **MyFolder** é uma subpasta de **Resources**.

Na solução **VideoPlayerDemos**, o projeto **VideoPlayerDemos.iOS** contém uma subpasta de **Resources** chamada **Videos**, que contém um arquivo chamado **iOSApiVideo.mp4**. Esse é um breve vídeo que mostra como usar o site do Xamarin para encontrar a documentação da classe `AVPlayerViewController` do iOS.

### <a name="android-video-resources"></a>Recursos de vídeo do Android

Em um projeto do Android, os vídeos precisam ser armazenados em uma subpasta de **Resources** chamada **raw**. A pasta **raw** não pode conter subpastas. Dê ao arquivo de vídeo uma `Build Action` igual a `AndroidResource`. Defina a propriedade `Path` de `ResourceVideoSource` com o nome de arquivo, por exemplo, **MyFile.mp4**.

O projeto **VideoPlayerDemos.Android** contém uma subpasta de **Resources** chamada **raw**, que contém um arquivo chamado **AndroidApiVideo.mp4**.

### <a name="uwp-video-resources"></a>Recursos de vídeo do UWP

Em um projeto da Plataforma Universal do Windows, você pode armazenar vídeos em qualquer pasta do projeto. Dê ao arquivo uma `Build Action` igual a `Content`. Defina a propriedade `Path` de `ResourceVideoSource` como a pasta e o nome de arquivo, por exemplo, **MyFolder/MyVideo.mp4**.

O projeto **VideoPlayerDemos.UWP** contém uma pasta chamada **Videos** com o arquivo **UWPApiVideo.mp4**.

## <a name="loading-the-video-files"></a>Carregando os arquivos de vídeo

Cada uma das classes de renderizador da plataforma contém um código em seu método `SetSource` para carregamento de arquivos de vídeo armazenados como recursos.

### <a name="ios-resource-loading"></a>Carregamento de recursos do iOS

A versão do iOS do `VideoPlayerRenderer` usa o método `GetUrlForResource` de `NSBundle` para carregar o recurso. O caminho completo precisa ser dividido em um nome de arquivo, uma extensão e um diretório. O código usa a classe `Path` no namespace `System.IO` do .NET para dividir o caminho do arquivo nestes componentes:

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

                if (!String.IsNullOrWhiteSpace(path))
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

O `VideoPlayerRenderer` do Android usa o nome de arquivo e o nome de pacote para construir um objeto `Uri`. O nome de pacote é o nome do aplicativo, neste caso, **VideoPlayerDemos.Android**, que pode ser obtido da propriedade estática `Context.PackageName`. O objeto `Uri` resultante é então passado para o método `SetVideoURI` de `VideoView`:

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

### <a name="uwp-resource-loading"></a>Carregamento de recursos do UWP

O `VideoPlayerRenderer` do UWP constrói um objeto `Uri` para o caminho e o define com a propriedade `Source` de `MediaElement`:

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

## <a name="playing-the-resource-file"></a>Reproduzindo o arquivo de recurso

A página **Reproduzir recurso de vídeo** da solução **VideoPlayerDemos** usa a classe `OnPlatform` para especificar o arquivo de vídeo para cada plataforma:

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

Se o recurso do iOS está armazenado na pasta **Resources** e se o recurso do UWP está armazenado na pasta raiz do projeto, você pode usar o mesmo nome de arquivo para cada plataforma. Se esse for o caso, você poderá definir esse nome diretamente como a propriedade `Source` de `VideoPlayer`.

Esta é a página em execução:

[![Reproduzir recurso de vídeo](loading-resources-images/playvideoresource-small.png "Reproduzir recurso de vídeo")](loading-resources-images/playvideoresource-large.png#lightbox "Reproduzir recurso de vídeo")

Agora você já viu como [carregar vídeos de um URI da Web](web-videos.md) e como reproduzir recursos inseridos. Além disso, você pode [carregar vídeos da biblioteca de vídeos do dispositivo](accessing-library.md).

## <a name="related-links"></a>Links Relacionados

- [Demonstrações do player de vídeo (exemplo)](/samples/xamarin/xamarin-forms-samples/customrenderers-videoplayerdemos)