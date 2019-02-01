---
title: Acessando a biblioteca de vídeos do dispositivo
description: Este artigo explica como acessar a biblioteca de vídeos do dispositivo em um aplicativo de player de vídeo usando o Xamarin.Forms.
ms.prod: xamarin
ms.assetid: 364C1D43-EAAE-45B9-BE24-0DA5AE74C4D9
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/12/2018
ms.openlocfilehash: dedd2768bfa843e529e2eddcaed3b102c0a2efbd
ms.sourcegitcommit: a1a58afea68912c79d16a3f64de9a0c1feb2aeb4
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55233504"
---
# <a name="accessing-the-devices-video-library"></a>Acessando a biblioteca de vídeos do dispositivo

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/VideoPlayerDemos/)

A maioria dos computadores desktop e dispositivos móveis modernos tem a capacidade de gravar vídeos usando a câmera do dispositivo. Em seguida, os vídeos criados por um usuário são armazenados como arquivos no dispositivo. Esses arquivos podem ser recuperados da biblioteca de imagens e reproduzidos pela classe `VideoPlayer`, assim como qualquer outro vídeo.

## <a name="the-photo-picker-dependency-service"></a>O serviço de dependência do seletor de foto

Cada uma das plataformas inclui um recurso que permite ao usuário selecionar uma foto ou um vídeo da biblioteca de imagens do dispositivo. A primeira etapa na reprodução de um vídeo da biblioteca de imagens do dispositivo é criar um serviço de dependência que invoque o seletor de imagem em cada plataforma. O serviço de dependência descrito abaixo é muito semelhante ao definido no artigo [**Escolhendo uma foto na biblioteca de imagens**](~/xamarin-forms/app-fundamentals/dependency-service/photo-picker.md), exceto que o seletor de vídeo retorna um nome de arquivo em vez de um objeto `Stream`.

O projeto da biblioteca do .NET Standard define uma interface chamada `IVideoPicker` para o serviço de dependência:

```csharp
namespace FormsVideoLibrary
{
    public interface IVideoPicker
    {
        Task<string> GetVideoFileAsync();
    }
}
```

Cada uma das plataformas contém uma classe chamada `VideoPicker` que implementa essa interface.

### <a name="the-ios-video-picker"></a>O seletor de vídeo do iOS

O `VideoPicker` do iOS usa o [`UIImagePickerController`](xref:UIKit.UIImagePickerController) do iOS para acessar a biblioteca de imagens, especificando que ela deve ser restrita a vídeos (conhecidos como "filmes") na propriedade `MediaType` do iOS. Observe que `VideoPicker` implementa explicitamente a interface `IVideoPicker`. Observe também o atributo `Dependency` que identifica essa classe como um serviço de dependência. Esses são os dois requisitos que permitem que o Xamarin.Forms encontre o serviço de dependência no projeto da plataforma:

```csharp
using System;
using System.Threading.Tasks;
using UIKit;
using Xamarin.Forms;

[assembly: Dependency(typeof(FormsVideoLibrary.iOS.VideoPicker))]

namespace FormsVideoLibrary.iOS
{
    public class VideoPicker : IVideoPicker
    {
        TaskCompletionSource<string> taskCompletionSource;
        UIImagePickerController imagePicker;

        public Task<string> GetVideoFileAsync()
        {
            // Create and define UIImagePickerController
            imagePicker = new UIImagePickerController
            {
                SourceType = UIImagePickerControllerSourceType.SavedPhotosAlbum,
                MediaTypes = new string[] { "public.movie" }
            };

            // Set event handlers
            imagePicker.FinishedPickingMedia += OnImagePickerFinishedPickingMedia;
            imagePicker.Canceled += OnImagePickerCancelled;

            // Present UIImagePickerController;
            UIWindow window = UIApplication.SharedApplication.KeyWindow;
            var viewController = window.RootViewController;
            viewController.PresentModalViewController(imagePicker, true);

            // Return Task object
            taskCompletionSource = new TaskCompletionSource<string>();
            return taskCompletionSource.Task;
        }

        void OnImagePickerFinishedPickingMedia(object sender, UIImagePickerMediaPickedEventArgs args)
        {
            if (args.MediaType == "public.movie")
            {
                taskCompletionSource.SetResult(args.MediaUrl.AbsoluteString);
            }
            else
            {
                taskCompletionSource.SetResult(null);
            }
            imagePicker.DismissModalViewController(true);
        }

        void OnImagePickerCancelled(object sender, EventArgs args)
        {
            taskCompletionSource.SetResult(null);
            imagePicker.DismissModalViewController(true);
        }
    }
}
```

### <a name="the-android-video-picker"></a>O seletor de vídeo do Android

A implementação do Android de `IVideoPicker` exige um método de retorno de chamada que faz parte da atividade do aplicativo. Por esse motivo, a classe `MainActivity` define duas propriedades, um campo e um método de retorno de chamada:

```csharp
namespace VideoPlayerDemos.Droid
{
    ···
    public class MainActivity : global::Xamarin.Forms.Platform.Android.FormsAppCompatActivity
    {
        protected override void OnCreate(Bundle bundle)
        {
            Current = this;
            ···
        }

        // Field, properties, and method for Video Picker
        public static MainActivity Current { private set; get; }

        public static readonly int PickImageId = 1000;

        public TaskCompletionSource<string> PickImageTaskCompletionSource { set; get; }

        protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
        {
            base.OnActivityResult(requestCode, resultCode, data);

            if (requestCode == PickImageId)
            {
                if ((resultCode == Result.Ok) && (data != null))
                {
                    // Set the filename as the completion of the Task
                    PickImageTaskCompletionSource.SetResult(data.DataString);
                }
                else
                {
                    PickImageTaskCompletionSource.SetResult(null);
                }
            }
        }
    }
}
```

O método `OnCreate` em `MainActivity` armazena sua própria instância na propriedade estática `Current`. Isso permite a implementação de `IVideoPicker` para obter a instância `MainActivity` a fim de iniciar o seletor **Selecionar Vídeo**:

```csharp
using System;
using System.Threading.Tasks;
using Android.Content;
using Xamarin.Forms;

// Need application's MainActivity
using VideoPlayerDemos.Droid;

[assembly: Dependency(typeof(FormsVideoLibrary.Droid.VideoPicker))]

namespace FormsVideoLibrary.Droid
{
    public class VideoPicker : IVideoPicker
    {
        public Task<string> GetVideoFileAsync()
        {
            // Define the Intent for getting images
            Intent intent = new Intent();
            intent.SetType("video/*");
            intent.SetAction(Intent.ActionGetContent);

            // Get the MainActivity instance
            MainActivity activity = MainActivity.Current;

            // Start the picture-picker activity (resumes in MainActivity.cs)
            activity.StartActivityForResult(
                Intent.CreateChooser(intent, "Select Video"),
                MainActivity.PickImageId);

            // Save the TaskCompletionSource object as a MainActivity property
            activity.PickImageTaskCompletionSource = new TaskCompletionSource<string>();

            // Return Task object
            return activity.PickImageTaskCompletionSource.Task;
        }
    }
}
```

As adições ao objeto `MainActivity` são o único código na solução [**VideoPlayerDemos**](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/VideoPlayerDemos/), em que o código normal do aplicativo precisa ser alterado para dar suporte às classes `FormsVideoLibrary`.

### <a name="the-uwp-video-picker"></a>O seletor de vídeo do UWP

A implementação do UWP da interface `IVideoPicker` usa o [`FileOpenPicker`](/uwp/api/Windows.Storage.Pickers.FileOpenPicker/) do UWP. Ela inicia a pesquisa de arquivo com a biblioteca de imagens e restringe os tipos de arquivo a MP4 e WMV (Vídeo do Windows Media):

```csharp
using System;
using System.Threading.Tasks;
using Windows.Storage;
using Windows.Storage.Pickers;
using Xamarin.Forms;

[assembly: Dependency(typeof(FormsVideoLibrary.UWP.VideoPicker))]

namespace FormsVideoLibrary.UWP
{
    public class VideoPicker : IVideoPicker
    {
        public async Task<string> GetVideoFileAsync()
        {
            // Create and initialize the FileOpenPicker
            FileOpenPicker openPicker = new FileOpenPicker
            {
                ViewMode = PickerViewMode.Thumbnail,
                SuggestedStartLocation = PickerLocationId.PicturesLibrary
            };

            openPicker.FileTypeFilter.Add(".wmv");
            openPicker.FileTypeFilter.Add(".mp4");

            // Get a file and return the path
            StorageFile storageFile = await openPicker.PickSingleFileAsync();
            return storageFile?.Path;
        }
    }
}
```

## <a name="invoking-the-dependency-service"></a>Invocando o serviço de dependência

A página **Reproduzir vídeo da biblioteca** do programa [**VideoPlayerDemos**](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/VideoPlayerDemos/) demonstra como usar o serviço de dependência do seletor de vídeo. O arquivo XAML contém uma instância `VideoPlayer` e um `Button` rotulado **Mostrar biblioteca de vídeos**:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:video="clr-namespace:FormsVideoLibrary"
             x:Class="VideoPlayerDemos.PlayLibraryVideoPage"
             Title="Play Library Video">
    <StackLayout>
        <video:VideoPlayer x:Name="videoPlayer"
                           VerticalOptions="FillAndExpand" />

        <Button Text="Show Video Library"
                Margin="10"
                HorizontalOptions="Center"
                Clicked="OnShowVideoLibraryClicked" />
    </StackLayout>
</ContentPage>
```

O arquivo code-behind contém o manipulador `Clicked` para o `Button`. A invocação do serviço de dependência exige uma chamada a `DependencyService.Get` para obter a implementação de uma interface `IVideoPicker` no projeto da plataforma. O método `GetVideoFileAsync` é então chamado nessa instância:

```csharp
namespace VideoPlayerDemos
{
    public partial class PlayLibraryVideoPage : ContentPage
    {
        public PlayLibraryVideoPage()
        {
            InitializeComponent();
        }

       async void OnShowVideoLibraryClicked(object sender, EventArgs args)
        {
            Button btn = (Button)sender;
            btn.IsEnabled = false;

            string filename = await DependencyService.Get<IVideoPicker>().GetVideoFileAsync();

            if (!String.IsNullOrWhiteSpace(filename))
            {
                videoPlayer.Source = new FileVideoSource
                {
                    File = filename
                };
            }

            btn.IsEnabled = true;
        }
    }
}
```

Em seguida, o manipulador `Clicked` usa esse nome de arquivo para criar um objeto `FileVideoSource` e defini-lo como a propriedade `Source` do `VideoPlayer`.

Cada uma das classes `VideoPlayerRenderer` contém um código em seu método `SetSource` para objetos do tipo `FileVideoSource`. Elas são mostradas abaixo:

### <a name="handling-ios-files"></a>Manipulando arquivos do iOS

A versão do iOS do `VideoPlayerRenderer` processa objetos `FileVideoSource` usando o método estático `Asset.FromUrl` com o nome de arquivo. Isso cria um objeto `AVAsset` que representa o arquivo na biblioteca de imagens do dispositivo:

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
            else if (Element.Source is FileVideoSource)
            {
                string uri = (Element.Source as FileVideoSource).File;

                if (!String.IsNullOrWhiteSpace(uri))
                {
                    asset = AVAsset.FromUrl(new NSUrl(uri));
                }
            }
            ···
        }
        ···
    }
}
```

### <a name="handling-android-files"></a>Manipulando arquivos do Android

Durante o processamento de objetos do tipo `FileVideoSource`, a implementação do Android de `VideoPlayerRenderer` usa o método `SetVideoPath` de `VideoView` para especificar o arquivo na biblioteca de imagens do dispositivo:

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
            else if (Element.Source is FileVideoSource)
            {
                string filename = (Element.Source as FileVideoSource).File;

                if (!String.IsNullOrWhiteSpace(filename))
                {
                    videoView.SetVideoPath(filename);
                    hasSetSource = true;
                }
            }
            ···
        }
        ···
    }
}
```

### <a name="handling-uwp-files"></a>Manipulando arquivos do UWP

Ao manipular objetos do tipo `FileVideoSource`, a implementação do UWP do método `SetSource` precisa criar um objeto `StorageFile`, abrir esse arquivo para leitura e passar o objeto de fluxo para o método `SetSource` do `MediaElement`:

```csharp
namespace FormsVideoLibrary.UWP
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, MediaElement>
    {
        protected override void OnElementChanged(ElementChangedEventArgs<VideoPlayer> args)
        {
            ···
        async void SetSource()
        {
            bool hasSetSource = false;
            ···
            else if (Element.Source is FileVideoSource)
            {
                // Code requires Pictures Library in Package.appxmanifest Capabilities to be enabled
                string filename = (Element.Source as FileVideoSource).File;

                if (!String.IsNullOrWhiteSpace(filename))
                {
                    StorageFile storageFile = await StorageFile.GetFileFromPathAsync(filename);
                    IRandomAccessStreamWithContentType stream = await storageFile.OpenReadAsync();
                    Control.SetSource(stream, storageFile.ContentType);
                    hasSetSource = true;
                }
            }
            ···
        }
        ···
    }
}
```

Para cada plataforma, o vídeo começa a ser reproduzido quase que imediatamente após a origem do vídeo ser definida, porque o arquivo está no dispositivo e não precisa ser baixado.



## <a name="related-links"></a>Links relacionados

- [Demonstrações do player de vídeo (amostra)](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/VideoPlayerDemos/)
- [Escolhendo uma foto da biblioteca de imagens](~/xamarin-forms/app-fundamentals/dependency-service/photo-picker.md)
