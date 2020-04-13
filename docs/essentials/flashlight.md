---
title: 'Xamarin.Essentials: Lanterna'
description: Este documento descreve a classe Flashlight no Xamarin.Essentials, que tem a capacidade de ativar ou desativar o flash da câmera do dispositivo para transformá-lo em uma lanterna.
ms.assetid: 06A03553-D212-43A2-9E6E-C2D2D93EB136
author: jamesmontemagno
ms.author: jamont
ms.date: 11/04/2018
ms.openlocfilehash: b94ba73b334ac68b256ca840956f987a4ef670ce
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "61075552"
---
# <a name="xamarinessentials-flashlight"></a>Xamarin.Essentials: Lanterna

A classe **Flashlight** tem a capacidade de ativar ou desativar o flash da câmera do dispositivo para transformá-lo em uma lanterna.

## <a name="get-started"></a>Introdução

[!include[](~/essentials/includes/get-started.md)]

Para acessar a funcionalidade **Flashlight**, a seguinte configuração específica da plataforma é necessária.

# <a name="android"></a>[Android](#tab/android)

As permissões Flashlight e Camera são necessárias e devem ser configuradas no projeto do Android. Isso pode ser usado das seguintes maneiras:

Abra o arquivo **AssemblyInfo.cs** na pasta **Propriedades** e adicione:

```csharp
[assembly: UsesPermission(Android.Manifest.Permission.Flashlight)]
[assembly: UsesPermission(Android.Manifest.Permission.Camera)]
```

OU Atualize o Manifesto do Android:

Abra o arquivo **AndroidManifest.xml** na pasta **Propriedades** e adicione o seguinte dentro do **nó manifesto.**

```xml
<uses-permission android:name="android.permission.FLASHLIGHT" />
<uses-permission android:name="android.permission.CAMERA" />
```

Ou clique com o botão direito do mouse no projeto do Android e abra as propriedades do projeto. Em **Manifesto do Android**, localize a área **Permissões necessárias:** e marque as permissões **FLASHLIGHT** e **CAMERA**. Isso atualizará automaticamente o arquivo **AndroidManifest.xml**.

Ao adicionar essas permissões, o [Google Play filtrará automaticamente os dispositivos](https://developer.android.com/guide/topics/manifest/uses-feature-element.html#permissions-features) sem um hardware específico. Você pode contornar isso adicionando o seguinte ao seu arquivo AssemblyInfo.cs em seu projeto do Android:

```csharp
[assembly: UsesFeature("android.hardware.camera", Required = false)]
[assembly: UsesFeature("android.hardware.camera.autofocus", Required = false)]
```

# <a name="ios"></a>[iOS](#tab/ios)

Não exige mais configurações.

# <a name="uwp"></a>[UWP](#tab/uwp)

Não exige mais configurações.

-----

## <a name="using-flashlight"></a>Como usar Flashlight

Adicione uma referência ao Xamarin.Essentials na classe:

```csharp
using Xamarin.Essentials;
```

A lanterna pode ser ativada e desativada por meio dos métodos `TurnOnAsync` e `TurnOffAsync`:

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

## <a name="platform-implementation-specifics"></a>Particularidades de implementação da plataforma

### <a name="android"></a>[Android](#tab/android)

A classe Flashlight foi otimizada com base no sistema operacional do dispositivo.

#### <a name="api-level-23-and-higher"></a>Nível da API 23 e superior

Em níveis mais recentes da API, o [Modo Tocha](https://developer.android.com/reference/android/hardware/camera2/CameraManager.html#setTorchMode) será usado para ativar ou desativar a unidade de flash do dispositivo.

#### <a name="api-level-22-and-lower"></a>Nível da API 22 e inferior

Uma textura de superfície da câmera é criada para ativar ou desativar o `FlashMode` da unidade de câmera. 

### <a name="ios"></a>[iOS](#tab/ios)

[AVCaptureDevice](xref:AVFoundation.AVCaptureDevice) é usado para ativar e desativar o Modo Tocha e o modo Flash do dispositivo.

### <a name="uwp"></a>[UWP](#tab/uwp)

[Lamp](https://docs.microsoft.com/uwp/api/windows.devices.lights.lamp) é usado para detectar a primeira lâmpada na parte traseira do dispositivo a fim de ativá-la ou desativá-la.

-----

## <a name="api"></a>API

- [Código-fonte de Lanterna](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Flashlight)
- [Documentação da API de Lanterna](xref:Xamarin.Essentials.Flashlight)
