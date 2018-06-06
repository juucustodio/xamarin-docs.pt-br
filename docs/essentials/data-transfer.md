---
title: 'Xamarin.Essentials: Transferência de dados'
description: A classe DataTransfer Xamarin.Essentials permite que um aplicativo de compartilhamento de dados, como os links da web e de texto para outros aplicativos no dispositivo.
ms.assetid: B7B01D55-0129-4C87-B515-89F8F4E94665
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 69d429b1cdbbbd6dbb53e3cefa89695666494ba7
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/05/2018
ms.locfileid: "34782379"
---
# <a name="xamarinessentials-data-transfer"></a>Xamarin.Essentials: Transferência de dados

![Pré-lançamento NuGet](~/media/shared/pre-release.png)

O **DataTransfer** classe permite que um aplicativo de compartilhamento de dados, como os links da web e de texto para outros aplicativos no dispositivo.

## <a name="using-data-transfer"></a>Usando a transferência de dados

Adicione uma referência a Xamarin.Essentials em sua classe:

```csharp
using Xamarin.Essentials;
```

A funcionalidade de transferência de dados funciona ao chamar o `RequestAsync` método com uma carga de solicitação de dados que inclui informações para compartilhar com outros aplicativos. Texto e Uri podem ser misturados e tratará cada plataforma de filtragem com base no conteúdo.

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

Interface do usuário para compartilhar a aplicativo externo que aparece quando é feita a solicitação:

![Transferência de dados](data-transfer-images/data-transfer.png)

## <a name="platform-differences"></a>Diferenças de plataforma

| Plataforma | Diferença |
| --- | --- |
| Android | Propriedade de entidade é usada para desejado assunto da mensagem. |
| iOS | Entidade não usada. |
| iOS | Título não usado. |
| UWP | Título padrão para o nome do aplicativo se não definida. |
| UWP | Entidade não usada. |

## <a name="api"></a>API

- [Código de origem de transferência de dados](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/DataTransfer)
- [Documentação da API de transferência de dados](xref:Xamarin.Essentials.DataTransfer)
