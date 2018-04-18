---
title: Escolhendo uma foto na biblioteca de imagens
description: Use DependencyService para escolher uma foto da biblioteca de imagens do telefone
ms.prod: xamarin
ms.assetid: 4F51B0E7-6A63-403C-B488-500CCBCE75DD
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/06/2017
ms.openlocfilehash: 95ac9912f0ff6788a2a633b3f8d3495e286030f1
ms.sourcegitcommit: 775a7d1cbf04090eb75d0f822df57b8d8cff0c63
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/18/2018
---
# <a name="picking-a-photo-from-the-picture-library"></a>Escolhendo uma foto na biblioteca de imagens

Este artigo orienta durante a criação de um aplicativo que permite que o usuário escolha uma foto da biblioteca de imagens do telefone. Como xamarin. Forms não têm essa funcionalidade, é necessário usar [ `DependencyService` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DependencyService/) para acessar as APIs nativas em cada plataforma.  Este artigo aborda as etapas a seguir para usar `DependencyService` para esta tarefa:

- **[Criar a Interface](#Creating_the_Interface)**  &ndash; entender como a interface é criada no código compartilhado.
- **[iOS implementação](#iOS_Implementation)**  &ndash; saber como implementar a interface em código nativo para iOS.
- **[Implementação do Android](#Android_Implementation)**  &ndash; saber como implementar a interface em código nativo para o Android.
- **[Implementação da plataforma Windows universal](#UWP_Implementation)**  &ndash; saber como implementar a interface em código nativo para o Windows UWP (plataforma Universal).
- **[Implementação do Windows Phone](#Windows_Phone_Implementation)**  &ndash; saber como implementar a interface em código nativo para Windows Phone 8.1.
- **[Implementando em código compartilhado](#Implementing_in_Shared_Code)**  &ndash; aprender a usar `DependencyService` para chamar a implementação nativa de código compartilhado.

<a name="Creating_the_Interface" />

## <a name="creating-the-interface"></a>Criando a Interface

Primeiro, crie uma interface no código compartilhado que expressa a funcionalidade desejada. No caso de um aplicativo de separação de fotos, apenas um método é necessário. Isso é definido no [ `IPicturePicker` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/DependencyService/DependencyServiceSample/DependencyServiceSample/IPicturePicker.cs) interface na biblioteca de classes portátil do código de exemplo:

```csharp
namespace DependencyServiceSample
{
    public interface IPicturePicker
    {
        Task<Stream> GetImageStreamAsync();
    }
}
```

O `GetImageStreamAsync` for definido como assíncrona porque o método deve retornar rapidamente, mas ele não pode retornar um `Stream` a foto selecionado até que o usuário tiver navegado a biblioteca de imagens e selecionar um objeto.

Essa interface é implementada em todas as plataformas usando código específico da plataforma.

<a name="iOS_Implementation" />

## <a name="ios-implementation"></a>Implementação do iOS

A implementação do iOS do `IPicturePicker` interface usos de [ `UIImagePickerController` ](https://developer.xamarin.com/api/type/UIKit.UIImagePickerController/) conforme descrito no [ **escolher uma foto da galeria** ](https://developer.xamarin.com/recipes/ios/media/video_and_photos/choose_a_photo_from_the_gallery/) receita e [código de exemplo](https://github.com/xamarin/recipes/tree/master/Recipes/ios/media/video_and_photos/choose_a_photo_from_the_gallery).

A implementação do iOS está contida no [ `PicturePickerImplementation` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/DependencyService/DependencyServiceSample/iOS/PicturePickerImplementation.cs) classe no projeto do iOS do código de exemplo. Para tornar essa classe visível para o `DependencyService` manager, a classe deve ser identificada com um [`assembly`] atributo de tipo `Dependency`, e a classe deve ser público e implementar explicitamente o `IPicturePicker` interface:

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

O `GetImageStreamAsync` método cria um `UIImagePickerController` e a inicializa para selecionar imagens da biblioteca de fotos. Dois manipuladores de eventos são necessários: um para quando o usuário seleciona uma foto e outro para quando o usuário cancela a exibição da biblioteca de fotos. O `PresentModalViewController` , em seguida, exibe a biblioteca de fotos para o usuário.

Neste ponto, o `GetImageStreamAsync` método deve retornar um `Task<Stream>` objeto para o código que está chamando. Essa tarefa é concluída somente quando o usuário termina de interagir com a biblioteca de fotos e um dos manipuladores de eventos é chamado. Para situações como essa, o [ `TaskCompletionSource` ](https://msdn.microsoft.com/library/dd449174(v=vs.110).aspx) classe é essencial. A classe fornece um `Task` objeto do tipo genérico adequado para retornar a partir de `GetImageStreamAsync` método e a classe posteriormente podem ser sinalizado quando a tarefa é concluída.

O `FinishedPickingMedia` manipulador de eventos é chamado quando o usuário selecionou uma imagem. No entanto, o manipulador fornece um `UIImage` objeto e o `Task` deve retornar um .NET `Stream` objeto. Isso é feito em duas etapas: O `UIImage` objeto é primeiro convertido em um arquivo JPEG na memória armazenada em um `NSData` objeto e, em seguida, o `NSData` objeto é convertido em .NET `Stream` objeto. Uma chamada para o `SetResult` método o `TaskComkpletionSource` objeto conclui a tarefa, fornecendo o `Stream` objeto:

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

                // Set the Stream as the completion of the Task
                taskCompletionSource.SetResult(stream);
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

Um aplicativo do iOS requer a permissão do usuário para acessar a biblioteca de fotos do telefone. Adicione o seguinte para o `dict` seção do arquivo. plist:

```xml
<key>NSPhotoLibraryUsageDescription</key>
<string>Picture Picker uses photo library</string>
```


<a name="Android_Implementation" />

## <a name="android-implementation"></a>Implementação do Android

A implementação do Android usa a técnica descrita a [ **selecionar uma imagem** ](https://developer.xamarin.com/recipes/android/other_ux/pick_image/) receita e o [código de exemplo](https://github.com/xamarin/recipes/tree/master/Recipes/android/other_ux/pick_image). No entanto, o método é chamado quando o usuário selecionou uma imagem da biblioteca de imagens é um `OnActivityResult` substituição em uma classe que deriva de `Activity`. Por esse motivo, o normal [ `MainActivity` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/DependencyService/DependencyServiceSample/Droid/MainActivity.cs) classe no projeto do Android tem sido complementada com um campo, uma propriedade e uma substituição do `OnActivityResult` método:

```csharp
public class MainActivity : global::Xamarin.Forms.Platform.Android.FormsApplicationActivity
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

O `OnActivityResult`substituição indica o arquivo de imagem selecionada com um Android `Uri` objeto, mas isso pode ser convertido em .NET `Stream` objeto chamando o `OpenInputStream` método do `ContentResolver` objeto que foi obtido o atividade `ContentResolver` propriedade.

Como a implementação do iOS, Android implementação usa um `TaskCompletionSource` para sinalizar quando a tarefa foi concluída. Isso `TaskCompletionSource` objeto é definido como uma propriedade pública no `MainActivity` classe. Isso permite que a propriedade a ser referenciado no [ `PicturePickerImplementation` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/DependencyService/DependencyServiceSample/Droid/PicturePickerImplementation.cs) classe no projeto do Android. Esta é a classe com o `GetImageStreamAsync` método:

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

Esse método acessa o `MainActivity` classe para várias finalidades: para o `Instance` propriedade, para o `PickImageId` campo para o `TaskCompletionSource` propriedade e chamar `StartActivityForResult`. Este método é definido pelo `FormsApplicationActivity` classe que é a classe base de `MainActivity`.

<a name="UWP_Implementation" />

## <a name="uwp-implementation"></a>Implementação de UWP

Ao contrário das implementações do Android e iOS, a implementação do seletor de foto para a plataforma Universal do Windows não exige o `TaskCompletionSource` classe. O [ `PicturePickerImplementation` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/DependencyService/DependencyServiceSample/UWP/PicturePickerImplementation.cs) classe usa a [ `FileOpenPicker` ](/uwp/api/Windows.Storage.Pickers.FileOpenPicker/) classe para obter acesso à biblioteca de fotos. Porque o `PickSingleFileAsync` método `FileOpenPicker` é assíncrona, o `GetImageStreamAsync` método pode simplesmente usar `await` com esse método (e outros métodos assíncronos) e retornar um `Stream` objeto:


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

<a name="Windows_Phone_Implementation" />

## <a name="windows-phone-81-implementation"></a>Implementação do Windows Phone 8.1

A implementação do Windows Phone 8.1 é semelhante à implementação UWP, exceto uma grande diferença que tem um grande impacto: O `PickSingleFileAsync` método `FileOpenPicker` não está disponível. Em vez disso, o `GetImageStreamAsync` método deve chamar `PickSingleFileAndContinue`. Este método retorna quando a biblioteca de fotos é exibida para o usuário, mas a seleção do usuário de uma fotografia é sinalizada por uma chamada para o `OnActivated` método o `App` classe.

Por esse motivo, o [ `App` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/DependencyService/DependencyServiceSample/WinPhone/App.xaml.cs) classe criada pelo modelo de projeto xamarin. Forms no projeto do Windows Phone 8.1 tem sido complementada com uma propriedade de tipo `TaskCompletionSource` e substitui o `OnActivated` método:

```csharp
namespace DependencyServiceSample.WinPhone
{
    public sealed partial class App : Application
    {
        ...
        public TaskCompletionSource<Stream> TaskCompletionSource { set; get; }

        protected async override void OnActivated(IActivatedEventArgs args)
        {
            base.OnActivated(args);

            IContinuationActivatedEventArgs continuationArgs = args as IContinuationActivatedEventArgs;

            if (continuationArgs != null &&
                continuationArgs.Kind == ActivationKind.PickFileContinuation)
            {
                FileOpenPickerContinuationEventArgs pickerArgs = args as FileOpenPickerContinuationEventArgs;

                if (pickerArgs.Files.Count > 0)
                {
                    // Get the file and a Stream
                    StorageFile storageFile = pickerArgs.Files[0];
                    IRandomAccessStreamWithContentType raStream = await storageFile.OpenReadAsync();
                    Stream stream = raStream.AsStreamForRead();

                    // Set the completion of the Task
                    TaskCompletionSource.SetResult(stream);
                }
                else
                {
                    TaskCompletionSource.SetResult(null);
                }
            }
        }
    }
}
```

O `OnActivated` método pode ser chamado por vários motivos, incluindo a inicialização do aplicativo. O código se restringe a apenas as chamadas quando o seletor de abertura de arquivo foi concluída e, em seguida, obtém um `Stream` de objeto o `StorageFile`.

O [ `PicturePickerImplementation` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/DependencyService/DependencyServiceSample/WinPhone/PicturePickerImplementation.cs) classe contém o `GetImageStreamAsync` método que cria o `FileOpenPicker` e chamadas `PickSingleFileAndContainue`:

```csharp
[assembly: Xamarin.Forms.Dependency(typeof(PicturePickerImplementation))]

namespace DependencyServiceSample.WinPhone
{
    public class PicturePickerImplementation : IPicturePicker
    {
        public Task<Stream> GetImageStreamAsync()
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

            // Display the picker for a single file (resumes in OnActivated in App.xaml.cs)
            openPicker.PickSingleFileAndContinue();

            // Create a TaskCompletionSource stored in App.xaml.cs
            TaskCompletionSource<Stream> taskCompletionSource = new TaskCompletionSource<Stream>();
            (Application.Current as App).TaskCompletionSource = taskCompletionSource;

            // Return the Task object
            return taskCompletionSource.Task;
        }
    }
}

```

<a name="Implementing_in_Shared_Code" />

## <a name="implementing-in-shared-code"></a>Implementando em código compartilhado

Agora que a interface foi implementada para cada plataforma, o aplicativo na biblioteca de classes portátil comum pode tirar proveito dele.

O [ `App` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/DependencyService/DependencyServiceSample/DependencyServiceSample/DependencyServiceSample.cs) classe cria um `Button` para escolher uma foto:

```csharp
Button pickPictureButton = new Button
{
    Text = "Pick Photo",
    VerticalOptions = LayoutOptions.CenterAndExpand,
    HorizontalOptions = LayoutOptions.CenterAndExpand
};
stack.Children.Add(pickPictureButton);
```

O `Clicked` manipulador utiliza o `DependencyService` classe chamar `GetImageStreamAsync`. Isso resulta em uma chamada no projeto de plataforma. Se o método retorna um `Stream` do objeto, em seguida, cria o manipulador de um `Image` elemento de imagem com um `TabGestureRecognizer`e substitui o `StackLayout` na página com que `Image`:

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

Tocar o `Image` elemento retorna a página normal.


## <a name="related-links"></a>Links relacionados

- [Escolher uma foto da galeria (iOS)](https://developer.xamarin.com/recipes/ios/media/video_and_photos/choose_a_photo_from_the_gallery/)
- [Selecione uma imagem (Android)](https://developer.xamarin.com/recipes/android/other_ux/pick_image/)
- [DependencyService (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/DependencyService/DependencyServiceSample)
