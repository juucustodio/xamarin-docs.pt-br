---
title: 'Xamarin.Essentials: Transferência de dados'
description: A classe DataTransfer no Xamarin.Essentials permite que um aplicativo compartilhe dados, como links de texto e da Web com outros aplicativos no dispositivo.
ms.assetid: B7B01D55-0129-4C87-B515-89F8F4E94665
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 179d4327aa768e7aa2c81dbbffd694d078327400
ms.sourcegitcommit: 729035af392dc60edb9d99d3dc13d1ef69d5e46c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/31/2018
ms.locfileid: "50674828"
---
# <a name="xamarinessentials-data-transfer"></a>Xamarin.Essentials: Transferência de dados

![Pré-lançamento NuGet](~/media/shared/pre-release.png)

A classe **DataTransfer** permite que um aplicativo compartilhe dados, como links de texto e da Web com outros aplicativos no dispositivo.

## <a name="get-started"></a>Introdução

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-data-transfer"></a>Como usar Data Transfer

Adicione uma referência ao Xamarin.Essentials na classe:

```csharp
using Xamarin.Essentials;
```

A funcionalidade de Transferência de dados funciona chamando o método `RequestAsync` com uma carga de solicitação de dados que inclui informações para compartilhar com outros aplicativos. É possível combinar texto e Uri, e cada plataforma tratará a filtragem com base no conteúdo.

```csharp

public class DataTransferTest
{
    public async Task ShareText(string text)
    {
        await DataTransfer.RequestAsync(new ShareTextRequest
            {
                Text = text,
                Title = "Share Text"
            });
    }

    public async Task ShareUri(string uri)
    {
        await DataTransfer.RequestAsync(new ShareTextRequest
            {
                Uri = uri,
                Title = "Share Web Link"
            });
    }
}
```

Interface do usuário para compartilhar com aplicativo externo exibida quando uma solicitação é feita:

![Transferência de dados](data-transfer-images/data-transfer.png)

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

- [Código-fonte de Transferência de Dados](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/DataTransfer)
- [Documentação da API de Transferência de Dados](xref:Xamarin.Essentials.DataTransfer)
