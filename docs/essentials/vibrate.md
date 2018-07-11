---
title: 'Xamarin.Essentials: vibração'
description: Este documento descreve a classe de vibração Xamarin.Essentials, que permite iniciar e parar a funcionalidade de Vibrar por uma quantidade de tempo desejada.
ms.assetid: 7E8B24C4-2625-4DAE-A129-383542D34F1E
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 530273543c6cb71038613c22fa4a6bfbde4928d7
ms.sourcegitcommit: 632955f8cdb80712abd8dcc30e046cb9c435b922
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/10/2018
ms.locfileid: "37947251"
---
# <a name="xamarinessentials-vibration"></a>Xamarin.Essentials: vibração

![Pré-lançamento NuGet](~/media/shared/pre-release.png)

O **vibração** classe permite iniciar e interromper a funcionalidade de Vibrar para uma quantidade de tempo desejada.

## <a name="getting-started"></a>Guia de Introdução

Para acessar o **vibração** funcionalidade a seguinte configuração específica de plataforma é necessária.

# <a name="androidtabandroid"></a>[Android](#tab/android)

A permissão de Vibrar é obrigatório e deve ser configurada no projeto do Android. Isso pode ser adicionado das seguintes maneiras:

Abra o **AssemblyInfo.cs** do arquivo sob o **propriedades** pasta e adicione:

```csharp
[assembly: UsesPermission(Android.Manifest.Permission.Vibrate)]
```

OU atualize o manifesto do Android:

Abra o **androidmanifest. XML** arquivo sob o **propriedades** pasta e adicione o seguinte dentro do **manifesto** nó.

```xml
<uses-permission android:name="android.permission.VIBRATE" />
```

Ou clique com botão direito no projeto do Android e abra as propriedades do projeto. Sob **manifesto do Android** localizar o **permissões necessárias:** área e verifique se o **VIBRAR** permissão. Isso atualizará automaticamente a **androidmanifest. XML** arquivo.

# <a name="iostabios"></a>[iOS](#tab/ios)

Nenhuma configuração adicional necessária.

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

Não há diferenças de plataforma.

-----

## <a name="using-vibration"></a>Usando Vibração

Adicione uma referência ao Xamarin.Essentials em sua classe:

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

# <a name="androidtabandroid"></a>[Android](#tab/android)

Não há diferenças de plataforma.

# <a name="iostabios"></a>[iOS](#tab/ios)

* Somente vibra quando o dispositivo estiver definido como "Vibrar no anel".
* Sempre vibra por 500 milissegundos.
* Não é possível cancelar a vibração.

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

Não há diferenças de plataforma.

-----

## <a name="api"></a>API

- [Código-fonte vibração](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Vibration)
- [Documentação da API de vibração](xref:Xamarin.Essentials.Vibration)
