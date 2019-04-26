---
title: Pesquisa de destaque principal no xamarin. IOS
description: Este documento descreve como usar o destaque principal em um aplicativo xamarin. IOS para fornecem links para conteúdo no aplicativo. Ele discute como criar, restaurar, atualizar e excluir itens pesquisáveis.
ms.prod: xamarin
ms.assetid: 1374914C-0F63-41BF-BD97-EBCEE86E57B1
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/20/2017
ms.openlocfilehash: fb9ddcc39bd33199dc370897250cd0d74597612f
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61248450"
---
# <a name="search-with-core-spotlight-in-xamarinios"></a>Pesquisa de destaque principal no xamarin. IOS

Destaque principal é uma nova estrutura para iOS 9 que apresenta uma API de banco de dados semelhantes para adicionar, editar ou excluir links para conteúdos dentro do seu aplicativo. Itens que foram adicionados usando o destaque do Core estarão disponíveis na pesquisa do Spotlight no dispositivo iOS.

Para obter um exemplo dos tipos de conteúdo que podem ser indexadas usando o destaque de núcleo, examinar aplicativos de mensagens, email, calendário e notas da Apple. Todos eles utilizam destaque principal para fornecer os resultados da pesquisa.

## <a name="creating-an-item"></a>Criação de um Item

Este é um exemplo de criação de um item e indexando-o usando o destaque do Core:

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

Essa informação será exibida como o seguinte em um resultado de pesquisa:

[![](corespotlight-images/corespotlight01.png "Visão geral de resultados de pesquisa de destaque Core")](corespotlight-images/corespotlight01.png#lightbox)

## <a name="restoring-an-item"></a>Restauração de um Item

Quando o usuário toca em um item adicionado ao resultado da pesquisa por meio de destaque principal para seu aplicativo, o `AppDelegate` método `ContinueUserActivity` é chamado (esse método também é usado para `NSUserActivity`). Por exemplo:

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

Observe que dessa vez estamos seleção para a atividade com um `ActivityType` de `CSSearchableItem.ActionType`.

## <a name="updating-an-item"></a>Atualizar um Item

Pode haver ocasiões em que um Item do índice que criamos com destaque principal precisa ser modificado, como uma alteração de título ou imagem em miniatura é necessária. Para fazer essa alteração, podemos usar o mesmo método que foi usada para criar inicialmente o índice.
Podemos criar um novo `CSSearchableItem` usando a mesma ID que foi usada para criar o item e anexar um novo `CSSearchableItemAttributeSet` que contém atributos modificados:

[![](corespotlight-images/corespotlight02.png "Atualizando uma visão geral de Item")](corespotlight-images/corespotlight02.png#lightbox)

Quando esse item é gravada para o índice de pesquisado, o item existente será atualizado com as novas informações.

## <a name="deleting-an-item"></a>Excluindo um Item

Destaque principal fornece várias maneiras de excluir um item de índice quando ele não é mais necessário.

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

Por fim, você pode excluir todos os itens de índice com o código a seguir:

```csharp
// Delete all index items
CSSearchableIndex.DefaultSearchableIndex.DeleteAll((error) => {
    // Successful?
    if (error !=null) {
        Console.WriteLine(error.LocalizedDescription);
    }
});
```
## <a name="additional-core-spotlight-features"></a>Recursos do destaque Core adicionais

Destaque principal tem os seguintes recursos que ajudam a manter o índice precisas e atualizadas:

- **Suporte de atualização do lote** – se seu aplicativo precisa para criar ou modificar um grande grupo de índices ao mesmo tempo, todo o lote pode ser enviado para o `Index` método o `CSSearchableIndex` classe em uma chamada.
- **Responder a alterações de índice** – usando o `CSSearchableIndexDelegate` seu aplicativo pode responder a alterações e notificações do índice pesquisável.
- **Aplicar proteção de dados** – usando as classes de proteção de dados, você pode implementar a segurança nos itens que você adicionar ao índice pesquisável usando o destaque de núcleo.



## <a name="related-links"></a>Links relacionados

- [Amostras do iOS 9](https://developer.xamarin.com/samples/ios/iOS9/)
- [iOS 9 para desenvolvedores](https://developer.apple.com/ios/pre-release/)
- [iOS 9.0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [Guia de programação de aplicativo de pesquisa](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/AppSearch/index.html#//apple_ref/doc/uid/TP40016308)
