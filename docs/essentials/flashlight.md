---
title: 'Xamarin.Essentials: lanterna'
description: Este documento descreve a classe lanterna Xamarin.Essentials, que tem a capacidade de ativar ou desativar a câmera do dispositivo flash para transformá-lo em uma lanterna.
ms.assetid: 06A03553-D212-43A2-9E6E-C2D2D93EB136
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 8c471f64c14a2e41693c450e02f89e7ac845d060
ms.sourcegitcommit: 51c274f37369d8965b68ff587e1c2d9865f85da7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2018
ms.locfileid: "39353354"
---
# <a name="xamarinessentials-flashlight"></a>Xamarin.Essentials: lanterna

![Pré-lançamento NuGet](~/media/shared/pre-release.png)

O **lanterna** classe tem a capacidade de ativar ou desativar a câmera do dispositivo flash para transformá-lo em uma lanterna.

## <a name="getting-started"></a>Guia de Introdução

Para acessar o **lanterna** funcionalidade a seguinte configuração específica de plataforma é necessária.

# <a name="androidtabandroid"></a>[Android](#tab/android)

As permissões lanterna e câmera são necessárias e devem ser configuradas no projeto do Android. Isso pode ser adicionado das seguintes maneiras:

Abra o **AssemblyInfo.cs** do arquivo sob o **propriedades** pasta e adicione:

```csharp
[assembly: UsesPermission(Android.Manifest.Permission.Flashlight)]
[assembly: UsesPermission(Android.Manifest.Permission.Camera)]
```

OU atualize o manifesto do Android:

Abra o **androidmanifest. XML** arquivo sob o **propriedades** pasta e adicione o seguinte dentro do **manifesto** nó.

```xml
<uses-permission android:name="android.permission.FLASHLIGHT" />
<uses-permission android:name="android.permission.CAMERA" />
```

Ou clique com botão direito no projeto do Android e abra as propriedades do projeto. Sob **manifesto do Android** localizar o **permissões necessárias:** área e verifique se o **LANTERNA** e **CÂMERA** permissões. Isso atualizará automaticamente a **androidmanifest. XML** arquivo.

Adicionando essas permissões [Google Play automaticamente filtrará os dispositivos](http://developer.android.com/guide/topics/manifest/uses-feature-element.html#permissions-features) sem hardware específico. Você pode contornar isso adicionando o seguinte ao arquivo AssemblyInfo.cs em seu projeto Android:

```csharp
[assembly: UsesFeature("android.hardware.camera", Required = false)]
[assembly: UsesFeature("android.hardware.camera.autofocus", Required = false)]
```

# <a name="iostabios"></a>[iOS](#tab/ios)

Nenhuma configuração adicional necessária.

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

Nenhuma configuração adicional necessária.

-----

## <a name="using-flashlight"></a>Usando lanterna

Adicione uma referência ao Xamarin.Essentials em sua classe:

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

## <a name="platform-implementation-specifics"></a>Particularidades de implementação de plataforma

### <a name="androidtabandroid"></a>[Android](#tab/android)

A classe lanterna foi otimizada com base no sistema operacional do dispositivo.

#### <a name="api-level-23-and-higher"></a>Nível de API 23 e superior

Nos níveis de API mais recentes, [tocha modo](https://developer.android.com/reference/android/hardware/camera2/CameraManager.html#setTorchMode) será usado para ativar ou desativar a unidade flash do dispositivo.

#### <a name="api-level-22-and-lower"></a>API nível 22 e inferior

Uma textura de superfície da câmera é criada para ativar ou desativar o `FlashMode` da unidade de câmera. 

### <a name="iostabios"></a>[iOS](#tab/ios)

[AVCaptureDevice](https://developer.xamarin.com/api/type/AVFoundation.AVCaptureDevice/) é usado para ativar e desativar o Torch e o modo de Flash do dispositivo.

### <a name="uwptabuwp"></a>[UWP](#tab/uwp)

[Lamp](https://docs.microsoft.com/en-us/uwp/api/windows.devices.lights.lamp) é usado para detectar a lâmpada primeiro na parte traseira do dispositivo para ativar ou desativar.

-----

## <a name="api"></a>API

- [Código-fonte lanterna](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Flashlight)
- [Documentação da API de lanterna](xref:Xamarin.Essentials.Flashlight)
