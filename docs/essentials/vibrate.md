---
title: Vibração Xamarin.Essentials
description: A classe vibração permite iniciar e interromper a funcionalidade de vibrate para uma quantidade de tempo desejada.
ms.assetid: 7E8B24C4-2625-4DAE-A129-383542D34F1E
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 68b064d9824a82ea733c7c8bef0c2d43f0a04283
ms.sourcegitcommit: 3e05b135b6ff0d607bc2378c1b6e66d2eebbcc3e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/12/2018
---
# <a name="xamarinessentials-vibration"></a>Vibração Xamarin.Essentials

![Pré-lançamento NuGet](~/media/shared/pre-release.png)

O **vibração** classe permite iniciar e interromper a funcionalidade de vibrate para uma quantidade de tempo desejada.

## <a name="getting-started"></a>Guia de Introdução

Para acessar o **vibração** funcionalidade a seguinte configuração específica de plataforma é necessária.

# <a name="androidtabandroid"></a>[Android](#tab/android)

A permissão Vibrate é necessária e deve ser configurada no projeto Android. Isso pode ser adicionado das seguintes maneiras:

Abra o **AssemblyInfo.cs** arquivo sob o **propriedades** pasta e adicionar:

```csharp
[assembly: UsesPermission(Android.Manifest.Permission.Vibrate)]
```

OU atualize o manifesto do Android:

Abra o **AndroidManifest.xml** arquivo sob o **propriedades** pasta e adicione o seguinte dentro do **manifesto** nó.

```xml
<uses-permission android:name="android.permission.VIBRATE" />
```

Ou clique com o botão direito no projeto Android e abra as propriedades do projeto. Em **manifesto do Android** encontrar o **as permissões necessárias:** área e verifique se o **VIBRATE** permissão. Isso atualizará automaticamente o **AndroidManifest.xml** arquivo.

# <a name="iostabios"></a>[iOS](#tab/ios)

Nenhuma configuração adicional necessária.

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

Nenhuma configuração adicional necessária.

-----

## <a name="using-vibration"></a>Usando Vibração

Adicione uma referência a Xamarin.Essentials em sua classe:

```csharp
using Xamarin.Essentials;
```

A funcionalidade de vibração pode ser solicitada para um determinado período de tempo ou o padrão de 500 milissegundos.

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

Cancelamento de vibração de dispositivo pode ser solicitado com o `Cancel` método:

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

## <a name="platform-differences"></a>Diferenças de plataforma

| Plataforma | Diferença |
| --- | --- |
| iOS | Sempre vibra para 500 milissegundos. |
| iOS | Não é possível cancelar vibração. |

## <a name="api"></a>API

- [Código-fonte vibração](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Vibration)
- [Documentação da API de vibração](xref:Xamarin.Essentials.Vibration)
