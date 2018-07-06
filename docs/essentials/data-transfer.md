---
title: 'Xamarin.Essentials: Transferência de dados'
description: A classe DataTransfer Xamarin.Essentials permite que um aplicativo compartilhar dados, como links de texto e da web para outros aplicativos no dispositivo.
ms.assetid: B7B01D55-0129-4C87-B515-89F8F4E94665
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: c1ed298e1317d0a3f78f4dbd9fc89a2b01c6958c
ms.sourcegitcommit: ec50c626613f2f9af51a9f4a52781129bcbf3fcb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/05/2018
ms.locfileid: "37855104"
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

* `Subject` não usado.
* `Title` não usado. 

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

* `Title` padrão para o nome do aplicativo se não definida.
* `Subject` não usado.

-----

## <a name="api"></a>API

- [Código de origem de transferência de dados](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/DataTransfer)
- [Documentação da API de transferência de dados](xref:Xamarin.Essentials.DataTransfer)
