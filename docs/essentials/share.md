---
title: 'Xamarin.Essentials: Compartilhar'
description: A classe share no Xamarin.Essentials permite que um aplicativo Compartilhe dados, como links de texto e da Web, para outros aplicativos no dispositivo.
ms.assetid: B7B01D55-0129-4C87-B515-89F8F4E94665
author: jamesmontemagno
ms.author: jamont
ms.date: 01/04/2021
ms.custom: video
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: b6bc8383f1c19e94c6760a213b4b9813ea77139a
ms.sourcegitcommit: 2a7bbe9cbee3727ba20ee755c1713bcfdb4d8ecb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98950945"
---
# <a name="no-locxamarinessentials-share"></a>Xamarin.Essentials: Compartilhar

A classe **Share** permite que um aplicativo compartilhe dados, como links de texto e da Web com outros aplicativos no dispositivo.

## <a name="get-started"></a>Introdução

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-share"></a>Usando o compartilhamento

Adicione uma referência a Xamarin.Essentials em sua classe:

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

![Compartilhar com a interface do usuário do aplicativo externo](images/share.png)

## <a name="file"></a>Arquivo

Esse recurso permite que um aplicativo compartilhe arquivos com outros aplicativos no dispositivo. Xamarin.Essentials o detectará automaticamente o tipo de arquivo (MIME) e solicitará um compartilhamento. Cada plataforma poderá ser compatível somente com extensões de arquivo específicas.

Veja um exemplo de como gravar texto no disco e compartilhá-lo com outros aplicativos:

```csharp
var fn =  "Attachment.txt";
var file = Path.Combine(FileSystem.CacheDirectory, fn);
File.WriteAllText(file, "Hello World");

await Share.RequestAsync(new ShareFileRequest
{
    Title = Title,
    File = new ShareFile(file)
});
```

## <a name="multiple-files"></a>Vários arquivos

O uso de compartilhamento de vários arquivos difere do único arquivo somente na capacidade de enviar vários arquivos ao mesmo tempo:

```csharp
var file1 = Path.Combine(FileSystem.CacheDirectory, "Attachment1.txt");
File.WriteAllText(file, "Content 1");
var file2 = Path.Combine(FileSystem.CacheDirectory, "Attachment2.txt");
File.WriteAllText(file, "Content 2");

await Share.RequestAsync(new ShareMultipleFilesRequest
{
    Title = ShareFilesTitle,
    Files = new ShareFile[] { new ShareFile(file1), new ShareFile(file2) }
});
```

## <a name="presentation-location"></a>Local da apresentação

[!include[](~/essentials/includes/ios-PresentationSourceBounds.md)]

## <a name="platform-differences"></a>Diferenças entre plataformas

# <a name="android"></a>[Android](#tab/android)

- A propriedade `Subject` é usada para o assunto desejado de uma mensagem.

# <a name="ios"></a>[iOS](#tab/ios)

- `Subject` não usado.
- `Title` não usado.

# <a name="uwp"></a>[UWP](#tab/uwp)

- `Title` assumirá o padrão de Nome do Aplicativo se não for definido.
- `Subject` não usado.

-----

## <a name="api"></a>API

- [Compartilhar código-fonte](https://github.com/xamarin/Essentials/tree/main/Xamarin.Essentials/Share)
- [Compartilhar documentação da API](xref:Xamarin.Essentials.Share)

## <a name="related-video"></a>Vídeo relacionados

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Share-Essential-API-of-the-Week/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
