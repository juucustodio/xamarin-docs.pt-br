---
title: Acessar a biblioteca de vídeos do dispositivo
ms.prod: xamarin
ms.assetid: 364C1D43-EAAE-45B9-BE24-0DA5AE74C4D9
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/12/2018
ms.openlocfilehash: eb3d66630613225c9b2becaa20f73a82f409ce7e
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="accessing-the-devices-video-library"></a>Acessar a biblioteca de vídeos do dispositivo

Maioria dos dispositivos móveis e computadores desktop têm a capacidade de gravar vídeos usando a câmera do dispositivo. Os vídeos que cria um usuário, em seguida, são armazenados como arquivos no dispositivo. Esses arquivos podem ser recuperados da biblioteca de imagem e desempenhados pelo `VideoPlayer` classe como qualquer outro vídeo.

## <a name="the-photo-picker-dependency-service"></a>O serviço de dependência de seletor de fotos

Cada uma das três plataformas inclui um recurso que permite que o usuário selecione uma foto ou vídeo da biblioteca de imagens do dispositivo. A primeira etapa na execução de um vídeo de biblioteca de imagens do dispositivo está criando um serviço de dependência que invoca o seletor de imagem em cada plataforma. O serviço de dependência descrito abaixo é muito semelhante a um definido no [ **uma foto de separação da biblioteca de imagens** ](~/xamarin-forms/app-fundamentals/dependency-service/photo-picker.md) artigo, exceto que o seletor de vídeo retorna um nome de arquivo em vez de um `Stream`objeto.

O projeto PCL define uma interface denominada `IVideoPicker` para o serviço de dependência:

```csharp
namespace FormsVideoLibrary
{
    public interface IVideoPicker
    {
        Task<string> GetVideoFileAsync();
    }
}
```

Cada uma das três plataformas contém uma classe denominada `VideoPicker` que implementa essa interface.

### <a name="the-ios-video-picker"></a>O seletor de vídeo iOS

O iOS `VideoPicker` usa o iOS [ `UIImagePickerController` ](https://developer.xamarin.com/api/type/UIKit.UIImagePickerController/) para acessar a biblioteca de imagens, especificando que devem ser restrita a vídeos (conhecidos como "filmes") no iOS `MediaType` propriedade. Observe que `VideoPicker` implementa explicitamente a `IVideoPicker` interface. Observe também o `Dependency` atributo que identifica essa classe como um serviço de dependência. Estes são os dois requisitos que permitem xamarin. Forms localizar o serviço de dependência no projeto de plataforma:

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

### <a name="the-android-video-picker"></a>O seletor de vídeo Android

A implementação do Android do `IVideoPicker` requer um método de retorno de chamada que é parte da atividade do aplicativo. Por esse motivo, a `MainActivity` classe define um método de retorno de chamada, um campo e duas propriedades:

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

O `OnCreate` método `MainActivity` armazena sua própria instância em estático `Current` propriedade. Isso permite a implementação de `IVideoPicker` para obter o `MainActivity` instância para iniciar o **selecione vídeo** seletor:

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

As adições para o `MainActivity` objeto são o único código no [ **VideoPlayerDemos** ](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/VideoPlayerDemos/) solução em que o código normal do aplicativo precisa ser alterado para dar suporte a `FormsVideoLibrary` classes.

### <a name="the-uwp-video-picker"></a>O seletor de vídeo de UWP

A implementação de UWP do `IVideoPicker` interface usa o UWP [ `FileOpenPicker` ](/uwp/api/Windows.Storage.Pickers.FileOpenPicker/). Ele começa a pesquisa de arquivos com a biblioteca de imagens e restringe os tipos de arquivo MP4 e WMV (Windows Media Video):

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

## <a name="invoking-the-dependency-service"></a>Chamar o serviço de dependência

O **reproduzir o vídeo de biblioteca** página do [ **VideoPlayerDemos** ](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/VideoPlayerDemos/) programa demonstra como usar o serviço de dependência do seletor de vídeo. O arquivo XAML contém um `VideoPlayer` instância e um `Button` rotulada **Mostrar biblioteca de vídeos**:

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

O arquivo code-behind contém o `Clicked` manipulador para o `Button`. Chamar o serviço de dependência requer uma chamada para `DependencyService.Get` para obter a implementação de um `IVideoPicker` interface no projeto de plataforma. O `GetVideoFileAsync` método é chamado nessa instância:

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

O `Clicked` manipulador, em seguida, usa esse nome de arquivo para criar um `FileVideoSource` objeto e defini-lo como o `Source` propriedade o `VideoPlayer`.

Cada uma da `VideoPlayerRenderer` classes contém código em seu `SetSource` método para objetos do tipo `FileVideoSource`. Eles são mostrados abaixo:

### <a name="handling-ios-files"></a>Tratamento de arquivos iOS

A versão do iOS de `VideoPlayerRenderer` processos `FileVideoSource` objetos usando estático `Asset.FromUrl` método com o nome do arquivo. Isso cria uma `AVAsset` objeto que representa o arquivo na biblioteca de imagens do dispositivo:

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

### <a name="handling-android-files"></a>Tratando arquivos Android

Durante o processamento de objetos do tipo `FileVideoSource`, a implementação do Android do `VideoPlayerRenderer` usa o `SetVideoPath` método `VideoView` para especificar o arquivo na biblioteca de imagens do dispositivo:

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

### <a name="handling-uwp-files"></a>Tratando arquivos UWP

Ao manipular objetos do tipo `FileVideoSource`, a implementação de UWP do `SetSource` método precisa criar um `StorageFile` do objeto, abra o arquivo para leitura e passar o objeto de fluxo para o `SetSource` método o `MediaElement`:

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

Para todas as três plataformas, o vídeo começa a ser reproduzida quase imediatamente após o vídeo a fonte está definida como o arquivo está no dispositivo e não precisa ser baixado.



## <a name="related-links"></a>Links relacionados

- [Demonstrações em vídeo Player (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/VideoPlayerDemos/)
- [Escolhendo uma foto na biblioteca de imagens](~/xamarin-forms/app-fundamentals/dependency-service/photo-picker.md)
