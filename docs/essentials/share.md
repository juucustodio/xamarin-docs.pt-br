---
title: 'Xamarin.Essentials: Compartilhar'
description: A classe Share no Xamarin.Essentials permite que um aplicativo compartilhe dados, como links de texto e da Web com outros aplicativos no dispositivo.
ms.assetid: B7B01D55-0129-4C87-B515-89F8F4E94665
author: jamesmontemagno
ms.author: jamont
ms.date: 04/02/2019
ms.custom: video
ms.openlocfilehash: 1a9a7b008773255d9d7743a4fcb21f02feb3e116
ms.sourcegitcommit: 495680e74c72e7c570e68cde95d3d3643b1fcc8a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/02/2019
ms.locfileid: "58869371"
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

# [<a name="android"></a>Android](#tab/android)

* `Subject` a propriedade é usada para o assunto desejado de uma mensagem.

# [<a name="ios"></a>iOS](#tab/ios)

* `Subject` não usado.
* `Title` não usado.

# [<a name="uwp"></a>UWP](#tab/uwp)

* `Title` assumirá o padrão de Nome do Aplicativo se não for definido.
* `Subject` não usado.

-----

## <a name="files"></a>Arquivos

![Versão prévia do recurso](~/media/shared/preview.png)

O compartilhamento de arquivos está disponível como uma versão prévia experimental no Xamarin.Essentials versão 1.1.0. Esse recurso permite que um aplicativo compartilhe arquivos com outros aplicativos no dispositivo. Para habilitar esse recurso, defina a propriedade a seguir no código de inicialização do aplicativo:

```csharp
ExperimentalFeatures.Enable(ExperimentalFeatures.ShareFileRequest);
```

Depois de habilitar o recurso, qualquer arquivo poderá ser compartilhado. O Xamarin.Essentials detectará automaticamente o tipo de arquivo (MIME) e solicitará um compartilhamento. Cada plataforma poderá ser compatível somente com extensões de arquivo específicas.

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

## <a name="api"></a>API

- [Compartilhar código-fonte](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Share)
- [Compartilhar documentação da API](xref:Xamarin.Essentials.Share)

## <a name="related-video"></a>Vídeo relacionados

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Share-Essential-API-of-the-Week/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
