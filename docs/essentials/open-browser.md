---
title: Abrir Xamarin.Essentials navegador
description: A classe de navegador permite que um aplicativo abrir um link da web no navegador preferencial sistema otimizado ou navegador externo.
ms.assetid: BABF40CC-8BEE-43FD-BE12-6301DF27DD33
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 2a95d0b89b78ce8b0ddfdc94d86c284a6f8b2bbe
ms.sourcegitcommit: 4db5f5c93f79f273d8fc462de2f405458b62fc02
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/19/2018
---
# <a name="xamarinessentials-browser"></a>Navegador de Xamarin.Essentials

![Pré-lançamento NuGet](~/media/shared/pre-release.png)

O **navegador** classe permite que um aplicativo abrir um link da web no navegador preferencial sistema otimizado ou navegador externo.

## <a name="using-browser"></a>Usando o navegador

Adicione uma referência a Xamarin.Essentials em sua classe:

```csharp
using Xamarin.Essentials;
```

A funcionalidade de navegador funciona chamando o `OpenAsync` método com o `Uri` e `BrowserLaunchType`.

```csharp

public class BrowserTest
{
    public async Task OpenBrowser(Uri uri)
    {
        await Browser.OpenAsync(uri, BrowserLaunchType.SystemPreferred);
    }
}
```

## <a name="platform-implementation-specifics"></a>Detalhes de implementação de plataforma

# <a name="androidtabandroid"></a>[Android](#tab/android)

O tipo de inicialização determina como o navegador é iniciado:

## <a name="system-preferred"></a>Sistema preferido

[Chrome personalizado guias](https://developer.chrome.com/multidevice/android/customtabs) será tentada a ser usado o Uri de carga e manter o reconhecimento de navegação.

## <a name="external"></a>Externo

Um `Intent` será usado para solicitar o Uri ser aberto por meio do navegador normal de sistemas.

# <a name="iostabios"></a>[iOS](#tab/ios)

## <a name="system-preferred"></a>Sistema preferido

[SFSafariViewController](https://developer.xamarin.com/api/type/SafariServices.SFSafariViewController/) é usado para carregar o Uri e manter o reconhecimento de navegação.

## <a name="external"></a>Externo

O padrão de `OpenUrl` no aplicativo principal é usado para iniciar o navegador padrão fora do aplicativo.

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

O navegador do usuário padrão será iniciado sempre independentemente do `BrowserLaunchType`.

--------------

## <a name="api"></a>API

- [Código de origem do navegador](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Browser)
- [Documentação da API do navegador](xref:Xamarin.Essentials.Browser)
