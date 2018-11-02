---
title: Xamarin.Essentials Open Browser
description: A classe Browser no Xamarin.Essentials permite que um aplicativo abra um link da Web no navegador preferido do sistema otimizado ou no navegador externo.
ms.assetid: BABF40CC-8BEE-43FD-BE12-6301DF27DD33
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: a68837ac4447dabcf52a1d1b27913adf80b4cbd7
ms.sourcegitcommit: 729035af392dc60edb9d99d3dc13d1ef69d5e46c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/31/2018
ms.locfileid: "50675387"
---
# <a name="xamarinessentials-browser"></a>Xamarin.Essentials: Browser

![Pré-lançamento NuGet](~/media/shared/pre-release.png)

A classe **Browser** permite que um aplicativo abra um link da Web no navegador preferido do sistema otimizado ou no navegador externo.

## <a name="get-started"></a>Introdução

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-browser"></a>Uso do Browser

Adicione uma referência ao Xamarin.Essentials na classe:

```csharp
using Xamarin.Essentials;
```

A funcionalidade Browser funciona chamando o método `OpenAsync` com `Uri` e `BrowserLaunchMode`.

```csharp

public class BrowserTest
{
    public async Task OpenBrowser(Uri uri)
    {
        await Browser.OpenAsync(uri, BrowserLaunchMode.SystemPreferred);
    }
}
```

## <a name="platform-implementation-specifics"></a>Particularidades de implementação da plataforma

# <a name="androidtabandroid"></a>[Android](#tab/android)

O Modo de Inicialização determina como o navegador é iniciado:

## <a name="system-preferred"></a>Sistema preferido

As [Custom Tabs do Chrome](https://developer.chrome.com/multidevice/android/customtabs) tentarão ser usadas para carregar o Uri e manter o reconhecimento da navegação.

## <a name="external"></a>Externo

Uma `Intent` será usada para solicitar o Uri que será aberto por meio do navegador normal dos sistemas.

# <a name="iostabios"></a>[iOS](#tab/ios)

## <a name="system-preferred"></a>Sistema preferido

[SFSafariViewController](https://developer.xamarin.com/api/type/SafariServices.SFSafariViewController/) é usado para carregar o Uri e manter o reconhecimento da navegação.

## <a name="external"></a>Externo

O padrão `OpenUrl` no aplicativo principal é usado para iniciar o navegador padrão fora do aplicativo.

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

O navegador padrão do usuário sempre será iniciado independentemente do `BrowserLaunchMode`.

--------------

## <a name="api"></a>API

- [Código-fonte do Browser](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Browser)
- [Documentação da API do Browser](xref:Xamarin.Essentials.Browser)
