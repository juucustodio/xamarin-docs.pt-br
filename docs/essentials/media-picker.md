---
title: 'Xamarin.Essentials: Seletor de mídia'
description: A classe MediaPicker no Xamarin.Essentials permite que um usuário escolha ou tire uma foto ou um vídeo no dispositivo.
ms.assetid: 23460875-6cf9-4440-a97b-46c55b0bca69
author: jamesmontemagno
ms.author: jamont
ms.date: 09/22/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 9848fbe6b82d1c1705916d35ab1688dd2cb9f443
ms.sourcegitcommit: 744f977b0595f489c592e29c8a3ba548fde02b6f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/28/2020
ms.locfileid: "91414731"
---
# <a name="no-locxamarinessentials-media-picker"></a>Xamarin.Essentials: Seletor de mídia

A classe **MediaPicker** permite que um usuário escolha ou tire uma foto ou um vídeo no dispositivo.

![API de pré-lançamento](~/media/shared/preview.png)

## <a name="get-started"></a>Introdução

[!include[](~/essentials/includes/get-started.md)]

Para acessar a funcionalidade **Fileescolhar** , a configuração específica da plataforma a seguir é necessária.

# <a name="android"></a>[Android](#tab/android)

As permissões a seguir são necessárias e devem ser configuradas no projeto do Android. Isso pode ser usado das seguintes maneiras:

Abra o arquivo **AssemblyInfo.cs** na pasta **Propriedades** e adicione:

```csharp
// Needed for Picking photo/video
[assembly: UsesPermission(Android.Manifest.Permission.ReadExternalStorage)]

// Needed for Taking photo/video
[assembly: UsesPermission(Android.Manifest.Permission.WriteExternalStorage)]
[assembly: UsesPermission(Android.Manifest.Permission.CameraExternalStorage)]

// Add these properties if you would like to filter out cameras that do not have cameras or set to false to make them optional
[assembly: UsesFeature("android.hardware.camera", Required = true)]
[assembly: UsesFeature("android.hardware.camera.autofocus", Required = true)]
```

OU Atualize o Manifesto do Android:

Abra o arquivo **AndroidManifest.xml** na pasta **Propriedades** e adicione o seguinte no nó do **manifesto** .

```xml
<uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" />
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
<uses-permission android:name="android.permission.CAMERA" />
```

Ou clique com o botão direito do mouse no projeto do Android e abra as propriedades do projeto. Em **manifesto do Android** , localize as **permissões necessárias:** área e verifique essas permissões. Isso atualizará automaticamente o arquivo **AndroidManifest.xml**.

# <a name="ios"></a>[iOS](#tab/ios)

Em seu `Info.plist` adicione as seguintes chaves:

```xml
<key>NSCameraUsageDescription</key>
<string>This app needs access to the camera to take photos.</string>
<key>NSMicrophoneUsageDescription</key>
<string>This app needs access to microphone for taking videos.</string>
<key>NSPhotoLibraryAddUsageDescription</key>
<string>This app needs access to the photo gallery for picking photos and videos.</string>
<key>NSPhotoLibraryUsageDescription</key>
<string>This app needs access to photos gallery for picking photos and videos.</string>
```

Certifique-se de atualizar o `<string>` em cada para uma descrição específica para seu aplicativo, pois ele será mostrado aos usuários.

# <a name="uwp"></a>[UWP](#tab/uwp)

Em `Package.appxmanifest` em **recursos** , verifique se `Microphone` e os `Webcam` recursos estão verificados.

-----

## <a name="using-media-picker"></a>Usando o seletor de mídia

A `MediaPicker` classe tem os seguintes métodos que retornam um `FileResult` que pode ser usado para obter o local dos arquivos ou lê-lo como um `Stream` .

* `PickPhotoAsync`: Abre o navegador de mídia para selecionar uma foto.
* `CapturePhotoAsync`: Abre a câmera para tirar uma foto.
* `PickVideoAsync`: Abre o navegador de mídia para selecionar um vídeo.
* `CaptureVideoAsync`: Abre a câmera para tirar um vídeo.

Cada método, opcionalmente, usa um `MediaPickerOptions` parâmetro que permite que o seja `Title` definido em alguns sistemas operacionais que são exibidos para os usuários.

## <a name="general-usage"></a>Uso geral

```csharp
async Task TakePhotoAsync()
{
    try
    {
        var photo = await MediaPicker.CapturePhotoAsync();
        await LoadPhotoAsync(photo);
        Console.WriteLine($"CapturePhotoAsync COMPLETED: {PhotoPath}");
    }
    catch (Exception ex)
    {
        Console.WriteLine($"CapturePhotoAsync THREW: {ex.Message}");
    }
}

async Task LoadPhotoAsync(FileResult photo)
{
    // canceled
    if (photo == null)
    {
        PhotoPath = null;
        return;
    }
    // save the file into local storage
    var newFile = Path.Combine(FileSystem.CacheDirectory, photo.FileName);
    using (var stream = await photo.OpenReadAsync())
    using (var newStream = File.OpenWrite(newFile))
        await stream.CopyToAsync(newStream);

    PhotoPath = newFile;
}
```


## <a name="api"></a>API

- [MediaPicker código-fonte](https://github.com/xamarin/Essentials/tree/main/Xamarin.Essentials/MediaPicker)
- [Documentação da API do MediaPicker](xref:Xamarin.Essentials.MediaPicker)
