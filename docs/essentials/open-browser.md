---
title: Xamarin.Essentials Open Browser
description: A classe Browser no Xamarin.Essentials permite que um aplicativo abra um link da Web no navegador preferido do sistema otimizado ou no navegador externo.
ms.assetid: BABF40CC-8BEE-43FD-BE12-6301DF27DD33
author: jamesmontemagno
ms.author: jamont
ms.date: 04/02/2019
ms.custom: video
ms.openlocfilehash: 26c4c2487bf2ec32ade3ceef9db73b4809053fec
ms.sourcegitcommit: 154a3e7aec775327565bb54eda1a610976af1d6f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/01/2020
ms.locfileid: "82624716"
---
# <a name="xamarinessentials-browser"></a>Xamarin.Essentials: Browser

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

Esse método retorna depois que o navegador foi _iniciado_ e não necessariamente _fechado_ pelo usuário.  O `bool` resultado indica se a inicialização foi bem-sucedida ou não.

## <a name="customization"></a>Personalização

Ao usar o navegador preferencial do sistema, existem várias opções de personalização disponíveis para iOS e Android. Isso inclui um `TitleMode` (somente Android) e opções de cores preferenciais para a `Toolbar` (iOS e Android) e os `Controls` (somente iOS) que são exibidos. 

Essas opções são especificadas usando `BrowserLaunchOptions` ao chamar `OpenAsync`.

```csharp
await Browser.OpenAsync(uri, new BrowserLaunchOptions
                {
                    LaunchMode = BrowserLaunchMode.SystemPreferred,
                    TitleMode = BrowserTitleMode.Show,
                    PreferredToolbarColor = Color.AliceBlue,
                    PreferredControlColor = Color.Violet
                });
```

![Opções de navegador](images/browser-options.png)

## <a name="platform-implementation-specifics"></a>Particularidades de implementação da plataforma

# <a name="android"></a>[Android](#tab/android)

O Modo de Inicialização determina como o navegador é iniciado:

## <a name="system-preferred"></a>Sistema preferido

As [Custom Tabs do Chrome](https://developer.chrome.com/multidevice/android/customtabs) tentarão ser usadas para carregar o Uri e manter o reconhecimento da navegação.

## <a name="external"></a>Externo

Uma `Intent` será usada para solicitar o Uri que será aberto por meio do navegador normal dos sistemas.

# <a name="ios"></a>[iOS](#tab/ios)

## <a name="system-preferred"></a>Sistema preferido

[SFSafariViewController](xref:SafariServices.SFSafariViewController) é usado para carregar o Uri e manter o reconhecimento da navegação.

## <a name="external"></a>Externo

O padrão `OpenUrl` no aplicativo principal é usado para iniciar o navegador padrão fora do aplicativo.

# <a name="uwp"></a>[UWP](#tab/uwp)

O navegador padrão do usuário sempre será iniciado independentemente do `BrowserLaunchMode`.

--------------

## <a name="api"></a>API

- [Código-fonte do Browser](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Browser)
- [Documentação da API do Browser](xref:Xamarin.Essentials.Browser)

## <a name="related-video"></a>Vídeo relacionados

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Open-Browser-XamarinEssentials-API-of-the-Week/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
