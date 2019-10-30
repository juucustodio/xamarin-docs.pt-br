---
title: Pesquisar com o principal Spotlight no Xamarin. iOS
description: Este documento descreve como usar o destaque principal em um aplicativo Xamarin. iOS para fornecer links para conteúdo no aplicativo. Ele aborda como criar, restaurar, atualizar e excluir itens pesquisáveis.
ms.prod: xamarin
ms.assetid: 1374914C-0F63-41BF-BD97-EBCEE86E57B1
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/20/2017
ms.openlocfilehash: 102c0e7dbd2f4c903793e83d7551a84a52cac4fb
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73031569"
---
# <a name="search-with-core-spotlight-in-xamarinios"></a>Pesquisar com o principal Spotlight no Xamarin. iOS

O Core Spotlight é uma nova estrutura para iOS 9 que apresenta uma API do tipo banco de dados para adicionar, editar ou excluir links para conteúdo em seu aplicativo. Os itens que foram adicionados usando o destaque de núcleo estarão disponíveis na pesquisa de destaque no dispositivo iOS.

Para obter um exemplo dos tipos de conteúdo que podem ser indexados usando o destaque principal, consulte os aplicativos de mensagens, email, calendário e notas da Apple. No momento, eles usam o destaque principal para fornecer resultados da pesquisa.

## <a name="creating-an-item"></a>Criando um item

Veja a seguir um exemplo de como criar um item e indexá-lo usando o destaque principal:

```csharp
using CoreSpotlight;
...

// Create attributes to describe an item
var attributes = new CSSearchableItemAttributeSet();
attributes.Title = "App Center Test";
attributes.ContentDescription = "Automatically test your app on 1,000 devices in the cloud.";

// Create item
var item = new CSSearchableItem ("1", "products", attributes);

// Index item
CSSearchableIndex.DefaultSearchableIndex.Index (new CSSearchableItem[]{ item }, (error) => {
    // Successful?
    if (error !=null) {
        Console.WriteLine(error.LocalizedDescription);
    }
});
```

Essas informações seriam exibidas como a seguir em um resultado de pesquisa:

[![](corespotlight-images/corespotlight01.png "Core Spotlight search result overview")](corespotlight-images/corespotlight01.png#lightbox)

## <a name="restoring-an-item"></a>Restaurando um item

Quando o usuário toca em um item adicionado ao resultado da pesquisa por meio do destaque principal para seu aplicativo, o método `AppDelegate` `ContinueUserActivity` é chamado (esse método também é usado para `NSUserActivity`). Por exemplo:

```csharp
public override bool ContinueUserActivity (UIApplication application,
   NSUserActivity userActivity, UIApplicationRestorationHandler completionHandler)
{

    // Take action based on the activity type
    switch (userActivity.ActivityType) {
    case "com.xamarin.platform":
        // Restore the state of the app here...
        break;
    default:
        if (userActivity.ActivityType == CSSearchableItem.ActionType.ToString ()) {
            // Display content for searchable item...
        }
        break;
    }

    return true;
}
```

Observe que, desta vez, verificamos se a atividade tem uma `ActivityType` de `CSSearchableItem.ActionType`.

## <a name="updating-an-item"></a>Atualizando um item

Pode haver ocasiões em que um item de índice criado com o destaque principal precisa ser modificado, como uma alteração no título ou imagem em miniatura é necessária. Para fazer essa alteração, usamos o mesmo método que foi usado para criar inicialmente o índice.
Criamos um novo `CSSearchableItem` usando a mesma ID que foi usada para criar o item e anexar um novo `CSSearchableItemAttributeSet` contendo os atributos modificados:

[![](corespotlight-images/corespotlight02.png "Updating an Item overview")](corespotlight-images/corespotlight02.png#lightbox)

Quando esse item é gravado no índice pesquisável, o item existente é atualizado com as novas informações.

## <a name="deleting-an-item"></a>Excluindo um item

O destaque principal fornece várias maneiras de excluir um item de índice quando ele não é mais necessário.

Primeiro, você pode excluir um item por seu identificador, por exemplo:

```csharp
// Delete Items by ID
CSSearchableIndex.DefaultSearchableIndex.Delete(new string[]{"1","16"},(error) => {
    // Successful?
    if (error !=null) {
        Console.WriteLine(error.LocalizedDescription);
    }
});
```

Em seguida, você pode excluir um grupo de itens de índice por seu nome de domínio. Por exemplo:

```csharp
// Delete by Domain Name
CSSearchableIndex.DefaultSearchableIndex.DeleteWithDomain(new string[]{"domain-name"},(error) => {
    // Successful?
    if (error !=null) {
        Console.WriteLine(error.LocalizedDescription);
    }
});
```

Por fim, você pode excluir todos os itens de índice com o seguinte código:

```csharp
// Delete all index items
CSSearchableIndex.DefaultSearchableIndex.DeleteAll((error) => {
    // Successful?
    if (error !=null) {
        Console.WriteLine(error.LocalizedDescription);
    }
});
```

## <a name="additional-core-spotlight-features"></a>Recursos adicionais do Spotlight Core

O destaque principal tem os seguintes recursos que ajudam a manter o índice preciso e atualizado:

- **Suporte à atualização em lotes** – se seu aplicativo precisar criar ou modificar um grande grupo de índices ao mesmo tempo, todo o lote poderá ser enviado para o método `Index` da classe `CSSearchableIndex` em uma chamada.
- **Responder a alterações de índice** – usando o `CSSearchableIndexDelegate` seu aplicativo pode responder a alterações e notificações do índice pesquisável.
- **Aplicar a proteção de dados** – usando as classes de proteção de dados, você pode implementar a segurança nos itens que você adiciona ao índice pesquisável usando o destaque principal.

## <a name="related-links"></a>Links relacionados

- [Amostras do iOS 9](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+iOS9)
- [iOS 9 para desenvolvedores](https://developer.apple.com/ios/pre-release/)
- [iOS 9,0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [Guia de programação de pesquisa de aplicativo](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/AppSearch/index.html#//apple_ref/doc/uid/TP40016308)
