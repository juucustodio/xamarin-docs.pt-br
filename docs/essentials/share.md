---
title: 'Xamarin.Essentials: Compartilhar'
description: A classe Share no Xamarin.Essentials permite que um aplicativo compartilhe dados, como links de texto e da Web com outros aplicativos no dispositivo.
ms.assetid: B7B01D55-0129-4C87-B515-89F8F4E94665
author: jamesmontemagno
ms.author: jamont
ms.date: 02/12/2019
ms.custom: video
ms.openlocfilehash: ad56a626133e03c1ca75b1db26b0904d5df7fea3
ms.sourcegitcommit: 64d6da88bb6ba222ab2decd2fdc8e95d377438a6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/18/2019
ms.locfileid: "58175324"
---
# <a name="xamarinessentials-share"></a>Xamarin.Essentials: Compartilhar

A classe **Share** permite que um aplicativo compartilhe dados, como links de texto e da Web com outros aplicativos no dispositivo.

## <a name="get-started"></a>Introdução

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-share"></a>Usando o compartilhamento

Adicione uma referência ao Xamarin.Essentials na classe:

```csharp
using Xamarin.Essentials;
```

A funcionalidade Share funciona chamando o método `RequestAsync` com um conteúdo de solicitação de dados que inclui informações para compartilhar com outros aplicativos. É possível combinar texto e Uri, e cada plataforma tratará a filtragem com base no conteúdo.

```csharp

public class ShareTest
{
    public async Task ShareText(string text)
    {
        await Share.RequestAsync(new ShareTextRequest
            {
                Text = text,
                Title = "Share Text"
            });
    }

    public async Task ShareUri(string uri)
    {
        await Share.RequestAsync(new ShareTextRequest
            {
                Uri = uri,
                Title = "Share Web Link"
            });
    }
}
```

Interface do usuário para compartilhar com aplicativo externo exibida quando uma solicitação é feita:

![Compartilhar](images/share.png)

## <a name="platform-differences"></a>Diferenças entre plataformas

# <a name="androidtabandroid"></a>[Android](#tab/android)

* A propriedade `Subject` é usada para o assunto desejado de uma mensagem.

# <a name="iostabios"></a>[iOS](#tab/ios)

* `Subject` não usado.
* `Title` não usado.

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

* `Title` assumirá o padrão de Nome do Aplicativo se não for definido.
* `Subject` não usado.

-----

## <a name="api"></a>API

- [Código-fonte de Share](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Share)
- [Documentação da API de Share](xref:Xamarin.Essentials.Share)

## <a name="related-video"></a>Vídeo relacionados

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Share-Essential-API-of-the-Week/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
