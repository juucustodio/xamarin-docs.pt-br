---
title: Escolhendo uma foto da biblioteca de imagens
description: Este artigo explica como usar a classe DependencyService do Xamarin.Forms para escolher uma foto da biblioteca de imagens do telefone.
ms.prod: xamarin
ms.assetid: 4F51B0E7-6A63-403C-B488-500CCBCE75DD
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/06/2017
ms.openlocfilehash: 36601b02bb2984d9350166dedac0d650d9642f91
ms.sourcegitcommit: c1d85b2c62ad84c22bdee37874ad30128581bca6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/08/2019
ms.locfileid: "67650638"
---
# <a name="picking-a-photo-from-the-picture-library"></a>Escolhendo uma foto da biblioteca de imagens

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://developer.xamarin.com/samples/xamarin-forms/DependencyService/DependencyServiceSample)

Este artigo descreve a criação de um aplicativo que permite que o usuário escolha uma foto da biblioteca de imagens de seu telefone. Como o Xamarin.Forms não inclui essa funcionalidade, é necessário usar [`DependencyService`](xref:Xamarin.Forms.DependencyService) para acessar APIs nativas em cada plataforma.

## <a name="creating-the-interface"></a>Como criar a interface

Primeiro, crie no código compartilhado uma interface que expressa a funcionalidade desejada. No caso de um aplicativo de seleção de fotos, é necessário apenas um método. Ele é definido na interface [`IPicturePicker`](https://github.com/xamarin/xamarin-forms-samples/blob/master/DependencyService/DependencyServiceSample/DependencyServiceSample/IPicturePicker.cs) na biblioteca do .NET Standard do código de exemplo:

```csharp
namespace DependencyServiceSample
{
    public interface IPicturePicker
    {
        Task<Stream> GetImageStreamAsync();
    }
}
```

O método `GetImageStreamAsync` é definido como assíncrono porque ele deve ser retornado rapidamente, mas ele não pode retornar um objeto `Stream` para a foto selecionada até que o usuário tenha navegado na biblioteca de imagens e selecionado uma.

Essa interface é implementada em todas as plataformas usando código específico da plataforma.

## <a name="ios-implementation"></a>Implementação de iOS

A implementação de iOS da interface `IPicturePicker` usa o [`UIImagePickerController`](xref:UIKit.UIImagePickerController) conforme descrito em [**Escolher uma foto da galeria**](https://github.com/xamarin/recipes/tree/master/Recipes/ios/media/video_and_photos/choose_a_photo_from_the_gallery) e no [código de exemplo](https://github.com/xamarin/recipes/tree/master/Recipes/ios/media/video_and_photos/choose_a_photo_from_the_gallery).

A implementação de iOS está contida na classe [`PicturePickerImplementation`](https://github.com/xamarin/xamarin-forms-samples/blob/master/DependencyService/DependencyServiceSample/iOS/PicturePickerImplementation.cs) no projeto do iOS do código de exemplo. Para tornar essa classe visível para o gerenciador do `DependencyService`, ela deve ser identificada com um atributo [`assembly`] do tipo `Dependency`, e a classe deve ser pública e implementar explicitamente a interface `IPicturePicker`:

```csharp
[assembly: Dependency (typeof (PicturePickerImplementation))]

namespace DependencyServiceSample.iOS
{
    public class PicturePickerImplementation : IPicturePicker
    {
        TaskCompletionSource<Stream> taskCompletionSource;
        UIImagePickerController imagePicker;

        public Task<Stream> GetImageStreamAsync()
        {
            // Create and define UIImagePickerController
            imagePicker = new UIImagePickerController
            {
                SourceType = UIImagePickerControllerSourceType.PhotoLibrary,
                MediaTypes = UIImagePickerController.AvailableMediaTypes(UIImagePickerControllerSourceType.PhotoLibrary)
            };

            // Set event handlers
            imagePicker.FinishedPickingMedia += OnImagePickerFinishedPickingMedia;
            imagePicker.Canceled += OnImagePickerCancelled;

            // Present UIImagePickerController;
            UIWindow window = UIApplication.SharedApplication.KeyWindow;
            var viewController = window.RootViewController;
            viewController.PresentModalViewController(imagePicker, true);

            // Return Task object
            taskCompletionSource = new TaskCompletionSource<Stream>();
            return taskCompletionSource.Task;
        }
        ...
    }
}

```

O método `GetImageStreamAsync` cria um `UIImagePickerController` e o inicializa para selecionar imagens da biblioteca de fotos. São necessários dois manipuladores de eventos: Um para quando o usuário seleciona uma foto e outro para quando o usuário cancela a exibição da biblioteca de fotos. O `PresentModalViewController`, em seguida, exibe a biblioteca de fotos para o usuário.

Neste ponto, o método `GetImageStreamAsync` deve retornar um objeto `Task<Stream>` para o código que o está chamando. Essa tarefa é concluída somente quando o usuário termina de interagir com a biblioteca de fotos e um dos manipuladores de eventos é chamado. Para situações como essa, a classe [`TaskCompletionSource`](https://msdn.microsoft.com/library/dd449174(v=vs.110).aspx) é essencial. A classe fornece um objeto `Task` do tipo genérico adequado para retornar do método `GetImageStreamAsync`, e mais tarde a classe poderá ser sinalizada quando a tarefa for concluída.

O manipulador de eventos `FinishedPickingMedia` é chamado quando o usuário seleciona uma imagem. No entanto, o manipulador fornece um objeto `UIImage` e o `Task` deve retornar um objeto `Stream` do .NET. Isso é feito em duas etapas: Primeiro, o objeto `UIImage` é convertido em um arquivo JPEG na memória armazenada em um objeto `NSData` e, em seguida, o objeto `NSData` é convertido em um objeto `Stream` do .NET. Uma chamada para o método `SetResult` do objeto `TaskCompletionSource` conclui a tarefa fornecendo o objeto `Stream`:

```csharp
namespace DependencyServiceSample.iOS
{
    public class PicturePickerImplementation : IPicturePicker
    {
        TaskCompletionSource<Stream> taskCompletionSource;
        UIImagePickerController imagePicker;
        ...
        void OnImagePickerFinishedPickingMedia(object sender, UIImagePickerMediaPickedEventArgs args)
        {
            UIImage image = args.EditedImage ?? args.OriginalImage;

            if (image != null)
            {
                // Convert UIImage to .NET Stream object
                NSData data = image.AsJPEG(1);
                Stream stream = data.AsStream();

                UnregisterEventHandlers();

                // Set the Stream as the completion of the Task
                taskCompletionSource.SetResult(stream);
            }
            else
            {
                UnregisterEventHandlers();
                taskCompletionSource.SetResult(null);
            }
            imagePicker.DismissModalViewController(true);
        }

        void OnImagePickerCancelled(object sender, EventArgs args)
        {
            UnregisterEventHandlers();
            taskCompletionSource.SetResult(null);
            imagePicker.DismissModalViewController(true);
        }

        void UnregisterEventHandlers()
        {
            imagePicker.FinishedPickingMedia -= OnImagePickerFinishedPickingMedia;
            imagePicker.Canceled -= OnImagePickerCancelled;
        }
    }
}

```

Um aplicativo iOS requer permissão do usuário para acessar a biblioteca de fotos do telefone. Adicione o seguinte à seção `dict` do arquivo Info.plist:

```xml
<key>NSPhotoLibraryUsageDescription</key>
<string>Picture Picker uses photo library</string>
```

## <a name="android-implementation"></a>Implementação de Android

A implementação de Android usa a técnica descrita em [**Selecionar uma imagem**](https://github.com/xamarin/recipes/tree/master/Recipes/android/other_ux/pick_image) e o [código de exemplo](https://github.com/xamarin/recipes/tree/master/Recipes/android/other_ux/pick_image). No entanto, o método que é chamado quando o usuário selecionou uma imagem da biblioteca de imagens é uma substituição de `OnActivityResult` em uma classe que deriva de `Activity`. Por esse motivo, a classe [`MainActivity`](https://github.com/xamarin/xamarin-forms-samples/blob/master/DependencyService/DependencyServiceSample/Droid/MainActivity.cs) normal no projeto do Android foi complementada com um campo, uma propriedade e uma substituição do método `OnActivityResult`:

```csharp
public class MainActivity : FormsAppCompatActivity
{
    ...
    // Field, property, and method for Picture Picker
    public static readonly int PickImageId = 1000;

    public TaskCompletionSource<Stream> PickImageTaskCompletionSource { set; get; }

    protected override void OnActivityResult(int requestCode, Result resultCode, Intent intent)
    {
        base.OnActivityResult(requestCode, resultCode, intent);

        if (requestCode == PickImageId)
        {
            if ((resultCode == Result.Ok) && (intent != null))
            {
                Android.Net.Uri uri = intent.Data;
                Stream stream = ContentResolver.OpenInputStream(uri);

                // Set the Stream as the completion of the Task
                PickImageTaskCompletionSource.SetResult(stream);
            }
            else
            {
                PickImageTaskCompletionSource.SetResult(null);
            }
        }
    }
}

```

A substituição `OnActivityResult`indica o arquivo de imagem selecionado com um objeto `Uri` do Android, mas ele pode ser convertido em um objeto `Stream` do .NET chamando o método `OpenInputStream` do objeto `ContentResolver` que foi obtido da propriedade `ContentResolver` da atividade.

Assim como a implementação do iOS, a implementação do Android usa um `TaskCompletionSource` para sinalizar quando a tarefa foi concluída. Esse objeto `TaskCompletionSource` é definido como uma propriedade pública na classe `MainActivity`. Isso permite que a propriedade seja referenciada na classe [`PicturePickerImplementation`](https://github.com/xamarin/xamarin-forms-samples/blob/master/DependencyService/DependencyServiceSample/Droid/PicturePickerImplementation.cs) no projeto do Android. Essa é a classe com o método `GetImageStreamAsync`:

```csharp
[assembly: Dependency(typeof(PicturePickerImplementation))]

namespace DependencyServiceSample.Droid
{
    public class PicturePickerImplementation : IPicturePicker
    {
        public Task<Stream> GetImageStreamAsync()
        {
            // Define the Intent for getting images
            Intent intent = new Intent();
            intent.SetType("image/*");
            intent.SetAction(Intent.ActionGetContent);

            // Start the picture-picker activity (resumes in MainActivity.cs)
            MainActivity.Instance.StartActivityForResult(
                Intent.CreateChooser(intent, "Select Picture"),
                MainActivity.PickImageId);

            // Save the TaskCompletionSource object as a MainActivity property
            MainActivity.Instance.PickImageTaskCompletionSource = new TaskCompletionSource<Stream>();

            // Return Task object
            return MainActivity.Instance.PickImageTaskCompletionSource.Task;
        }
    }
}
```

Esse método acessa a classe `MainActivity` para várias finalidades: para a propriedade `Instance`, para o campo `PickImageId`, para a propriedade `TaskCompletionSource` e para chamar `StartActivityForResult`. O método é definido pela classe `FormsAppCompatActivity`, que é a classe base de `MainActivity`.

## <a name="uwp-implementation"></a>Implementação da UWP

Diferente das implementações de iOS e Android, a implementação do seletor de fotos na Plataforma Universal do Windows não requer a classe `TaskCompletionSource`. A classe [`PicturePickerImplementation`](https://github.com/xamarin/xamarin-forms-samples/blob/master/DependencyService/DependencyServiceSample/UWP/PicturePickerImplementation.cs) usa a classe [`FileOpenPicker`](/uwp/api/Windows.Storage.Pickers.FileOpenPicker/) para ter acesso à biblioteca de fotos. Como o método `PickSingleFileAsync` de `FileOpenPicker` é assíncrono, o método `GetImageStreamAsync` pode simplesmente usar `await` com esse método (e outros métodos assíncronos) e retornar um objeto `Stream`:


```csharp
[assembly: Dependency(typeof(PicturePickerImplementation))]

namespace DependencyServiceSample.UWP
{
    public class PicturePickerImplementation : IPicturePicker
    {
        public async Task<Stream> GetImageStreamAsync()
        {
            // Create and initialize the FileOpenPicker
            FileOpenPicker openPicker = new FileOpenPicker
            {
                ViewMode = PickerViewMode.Thumbnail,
                SuggestedStartLocation = PickerLocationId.PicturesLibrary,
            };

            openPicker.FileTypeFilter.Add(".jpg");
            openPicker.FileTypeFilter.Add(".jpeg");
            openPicker.FileTypeFilter.Add(".png");

            // Get a file and return a Stream
            StorageFile storageFile = await openPicker.PickSingleFileAsync();

            if (storageFile == null)
            {
                return null;
            }

            IRandomAccessStreamWithContentType raStream = await storageFile.OpenReadAsync();
            return raStream.AsStreamForRead();
        }
    }
}
```

## <a name="implementing-in-shared-code"></a>Implementação em código compartilhado

Agora que a interface foi implementada para cada plataforma, o aplicativo na biblioteca do .NET Standard pode tirar proveito dela.

A classe [`App`](https://github.com/xamarin/xamarin-forms-samples/blob/master/DependencyService/DependencyServiceSample/DependencyServiceSample/DependencyServiceSample.cs) cria um `Button` para escolher uma foto:

```csharp
Button pickPictureButton = new Button
{
    Text = "Pick Photo",
    VerticalOptions = LayoutOptions.CenterAndExpand,
    HorizontalOptions = LayoutOptions.CenterAndExpand
};
stack.Children.Add(pickPictureButton);
```

O manipulador `Clicked` usa a classe `DependencyService` para chamar `GetImageStreamAsync`. Isso resulta em uma chamada no projeto da plataforma. Se o método retornar um objeto `Stream`, o manipulador criará um elemento `Image` para a imagem com um `TapGestureRecognizer` e substituirá o `StackLayout` na página pelo `Image`:

```csharp
pickPictureButton.Clicked += async (sender, e) =>
{
    pickPictureButton.IsEnabled = false;
    Stream stream = await DependencyService.Get<IPicturePicker>().GetImageStreamAsync();

    if (stream != null)
    {
        Image image = new Image
        {
            Source = ImageSource.FromStream(() => stream),
            BackgroundColor = Color.Gray
        };

        TapGestureRecognizer recognizer = new TapGestureRecognizer();
        recognizer.Tapped += (sender2, args) =>
        {
            (MainPage as ContentPage).Content = stack;
            pickPictureButton.IsEnabled = true;
        };
        image.GestureRecognizers.Add(recognizer);

        (MainPage as ContentPage).Content = image;
    }
    else
    {
        pickPictureButton.IsEnabled = true;
    }
};
```

Tocar o elemento `Image` faz com que a página volte ao normal.

## <a name="related-links"></a>Links relacionados

- [Escolher uma foto na galeria (iOS)](https://github.com/xamarin/recipes/tree/master/Recipes/ios/media/video_and_photos/choose_a_photo_from_the_gallery)
- [Selecionar uma imagem (Android)](https://github.com/xamarin/recipes/tree/master/Recipes/android/other_ux/pick_image)
- [DependencyService (amostra)](https://developer.xamarin.com/samples/xamarin-forms/DependencyService/DependencyServiceSample)
