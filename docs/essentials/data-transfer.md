---
title: 'Xamarin.Essentials: Transferência de dados'
description: A classe DataTransfer Xamarin.Essentials permite que um aplicativo compartilhar dados, como links de texto e da web para outros aplicativos no dispositivo.
ms.assetid: B7B01D55-0129-4C87-B515-89F8F4E94665
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 31e27556a6681b144084d2177cf3fde8fe8e5459
ms.sourcegitcommit: 51c274f37369d8965b68ff587e1c2d9865f85da7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2018
ms.locfileid: "39353513"
---
# <a name="xamarinessentials-data-transfer"></a>Xamarin.Essentials: Transferência de dados

![Pré-lançamento NuGet](~/media/shared/pre-release.png)

O **DataTransfer** classe permite que um aplicativo compartilhar dados, como links de texto e da web para outros aplicativos no dispositivo.

## <a name="using-data-transfer"></a>Usando a transferência de dados

Adicione uma referência ao Xamarin.Essentials em sua classe:

```csharp
using Xamarin.Essentials;
```

A funcionalidade de transferência de dados funciona chamando o `RequestAsync` método com uma carga de solicitação de dados que inclui informações para compartilhar com outros aplicativos. Texto e o Uri podem ser misturados e cada plataforma tratará a filtragem com base no conteúdo.

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

Interface do usuário para compartilhar a aplicativo externo que aparece quando é feita:

![Transferência de dados](data-transfer-images/data-transfer.png)

## <a name="platform-differences"></a>Diferenças de plataforma

# <a name="androidtabandroid"></a>[Android](#tab/android)

* `Subject` propriedade é usada para o assunto desejado de uma mensagem.

# <a name="iostabios"></a>[iOS](#tab/ios)

* `Subject` Não usado.
* `Title` Não usado.

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

* `Title` padrão para o nome do aplicativo se não definida.
* `Subject` Não usado.

-----

## <a name="api"></a>API

- [Código de origem de transferência de dados](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/DataTransfer)
- [Documentação da API de transferência de dados](xref:Xamarin.Essentials.DataTransfer)
