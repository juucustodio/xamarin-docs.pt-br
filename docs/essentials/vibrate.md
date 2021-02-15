---
title: 'Xamarin.Essentials: Vibração'
description: Este documento descreve a classe vibração no Xamarin.Essentials , que permite iniciar e parar a funcionalidade vibrar por um período de tempo desejado.
ms.assetid: 7E8B24C4-2625-4DAE-A129-383542D34F1E
author: jamesmontemagno
ms.custom: video
ms.author: jamont
ms.date: 11/04/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 77ef074ab23532249e7a22bdd0cf8ecd429120a3
ms.sourcegitcommit: 744f977b0595f489c592e29c8a3ba548fde02b6f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/28/2020
ms.locfileid: "91410669"
---
# <a name="no-locxamarinessentials-vibration"></a>Xamarin.Essentials: Vibração

A classe **Vibration** permite iniciar e interromper a funcionalidade de vibrar por um tempo desejado.

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

Não exige mais configurações.

-----

## <a name="using-vibration"></a>Como usar a Vibração

Adicione uma referência a Xamarin.Essentials em sua classe:

```csharp
using Xamarin.Essentials;
```

A funcionalidade de vibração pode ser solicitada por um determinado tempo ou pelo padrão de 500 milissegundos.

```csharp
try
{
    // Use default vibration length
    Vibration.Vibrate();

    // Or use specified time
    var duration = TimeSpan.FromSeconds(1);
    Vibration.Vibrate(duration);
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

O cancelamento da vibração do dispositivo pode ser solicitado com o método `Cancel`:

```csharp
try
{
    Vibration.Cancel();
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

## <a name="platform-differences"></a>Diferenças entre plataformas

# <a name="android"></a>[Android](#tab/android)

Sem diferenças entre plataformas.

# <a name="ios"></a>[iOS](#tab/ios)

- Somente vibra quando o dispositivo estiver definido como "Vibrar ao tocar".
- Sempre vibra por 500 milissegundos.
- Não é possível cancelar a vibração.

# <a name="uwp"></a>[UWP](#tab/uwp)

Sem diferenças entre plataformas.

-----

## <a name="api"></a>API

- [Código-fonte de Vibração](https://github.com/xamarin/Essentials/tree/main/Xamarin.Essentials/Vibration)
- [Documentação da API de Vibração](xref:Xamarin.Essentials.Vibration)

## <a name="related-video"></a>Vídeo relacionados

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Vibration-XamarinEssentials-API-of-the-Week/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
