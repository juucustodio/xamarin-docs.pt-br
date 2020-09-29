---
title: 'Xamarin.Essentials: Comentários do Haptic'
description: Este documento descreve a classe HapticFeedback no Xamarin.Essentials , que permite que você controle os comentários do Haptic sobre o dispositivo.
ms.assetid: 4462936c-4018-443b-906d-d63da6d0ed7d
author: dimonovdd
ms.author: jamont
ms.date: 09/22/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 129501b5b622901d4347daf60d3cee6579bb115a
ms.sourcegitcommit: 744f977b0595f489c592e29c8a3ba548fde02b6f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/28/2020
ms.locfileid: "91414732"
---
# <a name="no-locxamarinessentials-haptic-feedback"></a>Xamarin.Essentials: Comentários do Haptic

A classe **HapticFeedback** permite que você controle os comentários do Haptic sobre o dispositivo.

![API de pré-lançamento](~/media/shared/preview.png)

## <a name="get-started"></a>Introdução

[!include[](~/essentials/includes/get-started.md)]

Para acessar a funcionalidade de **Vibração**, a seguinte configuração específica da plataforma é necessária.

# <a name="android"></a>[Android](#tab/android)

A permissão de Vibração é necessária e deve ser configurada no projeto do Android. Isso pode ser usado das seguintes maneiras:

Abra o arquivo **AssemblyInfo.cs** na pasta **Propriedades** e adicione:

```csharp
[assembly: UsesPermission(Android.Manifest.Permission.Vibrate)]
```

OU Atualize o Manifesto do Android:

Abra o arquivo **AndroidManifest.xml** na pasta **Propriedades** e adicione o seguinte no nó do **manifesto** .

```xml
<uses-permission android:name="android.permission.VIBRATE" />
```

Ou clique com o botão direito do mouse no projeto do Android e abra as propriedades do projeto. Em **Manifesto do Android**, localize a área **Permissões necessárias:** e marque a permissão **VIBRAR**. Isso atualizará automaticamente o arquivo **AndroidManifest.xml**.

# <a name="ios"></a>[iOS](#tab/ios)

Não exige mais configurações.

# <a name="uwp"></a>[UWP](#tab/uwp)

Sem diferenças entre plataformas.

-----

## <a name="using-haptic-feedback"></a>Usando os comentários do Haptic

Adicione uma referência a Xamarin.Essentials em sua classe:

```csharp
using Xamarin.Essentials;
```

A funcionalidade de comentários do Haptic pode ser executada com um `Click` `LongPress` tipo de comentário ou.

```csharp
try
{
    // Perform click feedback
    HapticFeedback.Perform(HapticFeedbackType.Click);

    // Or use long press    
    HapticFeedback.Perform(HapticFeedbackType.LongPress);
}
catch (FeatureNotSupportedException ex)
{
    // Feature not supported on device
}
catch (Exception ex)
{
    // Other error has occurred.
}
```

## <a name="api"></a>API

- [HapticFeedback código-fonte](https://github.com/xamarin/Essentials/tree/main/Xamarin.Essentials/HapticFeedback)
- [Documentação da API do HapticFeedback](xref:Xamarin.Essentials.HapticFeedback)