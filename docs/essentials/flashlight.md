---
title: Xamarin.Essentials lâmpada
description: A classe de lâmpada tem a capacidade de ativar ou desativar a câmera do dispositivo flash transformá-la em uma lâmpada.
ms.assetid: 06A03553-D212-43A2-9E6E-C2D2D93EB136
ms.technology: xamarin-crossplatform
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 3d867d742314f2677eeab35bbc354f696c99bf75
ms.sourcegitcommit: 46d3c9daa45350bdd536d9e105517f3c1c753c5b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/07/2018
---
# <a name="xamarinessentials-flashlight"></a>Xamarin.Essentials lâmpada

![Pré-lançamento NuGet](~/media/shared/pre-release.png)

O **lâmpada** classe tem a capacidade de ativar ou desativar a câmera do dispositivo flash transformá-la em uma lâmpada.

## <a name="getting-started"></a>Guia de Introdução

Para acessar o **lâmpada** funcionalidade a seguinte configuração específica de plataforma é necessária.

# <a name="androidtabandroid"></a>[Android](#tab/android)

As permissões de lâmpada e câmera são necessárias e devem ser configuradas no projeto Android. Isso pode ser adicionado das seguintes maneiras:

Abra o **AssemblyInfo.cs** arquivo sob o **propriedades** pasta e adicionar:

```csharp
[assembly: UsesPermission(Android.Manifest.Permission.Flashlight)]
[assembly: UsesPermission(Android.Manifest.Permission.Camera)]
```

OU atualize o manifesto do Android:

Abra o **AndroidManifest.xml** arquivo sob o **propriedades** pasta e adicione o seguinte dentro do **manifesto** nó.

```xml
<uses-permission android:name="android.permission.FLASHLIGHT" />
<uses-permission android:name="android.permission.CAMERA" />
```

Ou clique com o botão direito no projeto Android e abra as propriedades do projeto. Em **manifesto do Android** encontrar o **as permissões necessárias:** área e verifique se o **LÂMPADA** e **CÂMERA** permissões. Isso atualizará automaticamente o **AndroidManifest.xml** arquivo.

Adicionando essas permissões [Google Play automaticamente filtrará dispositivos](http://developer.android.com/guide/topics/manifest/uses-feature-element.html#permissions-features) sem hardware específico. Você pode obter esse problema adicionando o seguinte ao arquivo AssemblyInfo.cs em seu projeto Android:

```csharp
[assembly: UsesFeature("android.hardware.camera", Required = false)]
[assembly: UsesFeature("android.hardware.camera.autofocus", Required = false)]
```

# <a name="iostabios"></a>[iOS](#tab/ios)

Nenhuma configuração adicional necessária.

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

Nenhuma configuração adicional necessária.

-----

## <a name="using-flashlight"></a>Usando lâmpada

Adicione uma referência a Xamarin.Essentials em sua classe:

```csharp
using Xamarin.Essentials;
```

A lâmpada pode ser ativada e desativado por meio de `TurnOnAsync` e `TurnOffAsync` métodos:

```csharp
try
{
    // Turn On
    await Flashlight.TurnOnAsync();

    // Turn Off
    await Flashlight.TurnOffAsync();
}
catch (FeatureNotSupportedException fnsEx)
{
    // Handle not supported on device exception
}
catch (PermissionException pEx)
{
    // Handle permission exception
}
catch (Exception ex)
{
    // Unable to turn on/off flashlight
}
```

## <a name="platform-implementation-specifics"></a>Detalhes de implementação de plataforma

### <a name="androidtabandroid-specifics"></a>[Android](#tab/android-specifics)

A classe de lâmpada foi optmized com base no sistema operacional do dispositivo.

#### <a name="api-level-23-and-higher"></a>API nível 23 e superior

Nos níveis de API mais recentes, [tocha modo](https://developer.android.com/reference/android/hardware/camera2/CameraManager.html#setTorchMode) será usado para ativar ou desativar a unidade flash do dispositivo.

#### <a name="api-level-22-and-lower"></a>API nível 22 e inferior

Uma textura da superfície câmera é criada para ativar ou desativar o `FlashMode` da unidade de câmera. 

### <a name="iostabios-specifics"></a>[iOS](#tab/ios-specifics)

[AVCaptureDevice](https://developer.xamarin.com/api/type/AVFoundation.AVCaptureDevice/) é usado para ativar e desativar o tocha e o modo Flash do dispositivo.

### <a name="uwptabuwp-specifics"></a>[UWP](#tab/uwp-specifics)

[Lâmpada](https://docs.microsoft.com/en-us/uwp/api/windows.devices.lights.lamp) é usado para detectar a lâmpada primeiro na parte traseira do dispositivo para ativar ou desativar.

-----

## <a name="api"></a>API

- [Código-fonte lâmpada](https://github.com/xamarin/Essentials/tree/master/Essentials/Flashlight)
- [Documentação da API de lâmpada](xref:Xamarin.Essentials.Flashlight)
