---
title: Comprando produtos não consumíveis no Xamarin. iOS
description: Este documento descreve produtos não consumíveis no Xamarin. iOS, que são recursos adquiridos por um usuário que permanecem disponíveis indefinidamente, independentemente do dispositivo.
ms.prod: xamarin
ms.assetid: 635D9CA2-6BCA-53E1-7B10-968029AA3493
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/18/2017
ms.openlocfilehash: 50b26e1b37000df3e6cd96c8a3cd2f424dd4f1f0
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70752656"
---
# <a name="purchasing-non-consumable-products-in-xamarinios"></a>Comprando produtos não consumíveis no Xamarin. iOS

Os produtos não consumíveis são "de propriedade" pelo cliente. A expectativa é que eles sempre terão acesso a eles, mesmo que seu dispositivo seja perdido/roubado ou compre um novo. Eles são úteis para livros, problemas da revista, níveis de jogos, filtros de fotos, ' Pro-Features ', etc. Depois que um usuário tiver comprado um produto não consumível, ele nunca precisará pagar novamente. Se o código acidentalmente permitir que eles tentem, o StoreKit mostrará uma mensagem de que ele já foi comprado.

## <a name="non-consumable-products-sample"></a>Exemplo de produtos não consumíveis

O [código InAppPurchaseSample](https://docs.microsoft.com/samples/xamarin/ios-samples/storekit) contém um projeto chamado *NonConsumables*. O exemplo de código demonstra como implementar produtos não consumíveis usando filtros de fotos como um exemplo. Depois de adquirir um filtro, você poderá aplicá-lo à foto repetidamente. Você nunca precisa comprá-la novamente.   

O processo de compra é mostrado nesta série de capturas de tela – o botão **comprar** se torna o botão ativação de recurso:   

 [![](purchasing-non-consumable-products-images/image34.png "O processo de compra é mostrado nesta série de capturas de tela")](purchasing-non-consumable-products-images/image34.png#lightbox)   

O processo de compra é o mesmo que um produto consumível; a principal diferença é a forma como a compra é controlada no código do aplicativo. Neste exemplo, o botão comprar só estará disponível se o produto ainda não tiver sido comprado, caso contrário, o botão ativará o recurso em si.   

O diagrama a seguir mostra as interações entre as classes e o servidor da App Store para executar uma compra de produto não consumível:   

 [![](purchasing-non-consumable-products-images/image35.png "As interações entre classes e o servidor da App Store para executar uma compra de produto não consumível")](purchasing-non-consumable-products-images/image35.png#lightbox)   

A principal diferença do exemplo de consumo é que, após a conclusão da compra, a interface do usuário é atualizada para evitar a nova compra. Neste exemplo, a notificação de uma transação bem-sucedida atualiza a interface do usuário para que o botão **comprar** seja convertido em um botão que ativa o próprio recurso.

## <a name="re-purchasing-non-consumable-products"></a>Renovando produtos não consumíveis

Em geral, seu código deve ocultar ou redirecionar um botão de compra depois que o produto tiver sido comprado com êxito, para impedir que o usuário tente comprar o produto novamente. O aplicativo de exemplo faz isso alterando o botão **comprar** para o botão que faz com que o filtro de fotos de exemplo funcione.   

Há situações em que um aplicativo não pode dizer se um produto não consumível já foi adquirido:

- Se um aplicativo for excluído e instalado novamente em um dispositivo, todos os registros de compra serão desfeitos (a menos que/até que o usuário faça uma restauração de backup). 
- Se o usuário tiver o aplicativo instalado em dois (ou mais) dispositivos e fizer uma compra em um dos dispositivos. Os outros dispositivos continuarão a mostrar o produto disponível para compra. 
- Se um cliente tentar comprar novamente um produto não consumível nessas situações, a loja de aplicativos atenderá o produto novamente sem encargos. Inicialmente, a interface do usuário será exibida para executar uma compra (por exemplo, um alerta de confirmação será exibido e a ID da Apple será necessária), no entanto, o usuário verá uma mensagem avisando que o produto já foi adquirido.  

O caminho de código nesse cenário é exatamente o mesmo que uma compra regular, as únicas diferenças são:

- O usuário não será cobrado novamente para o produto.
- O `SKPaymentTransaction` objeto passado para o aplicativo terá uma `OriginalTransaction` propriedade que se refere à transação que foi gerada quando o produto foi inicialmente adquirido. 
- Os aplicativos que vendem produtos não consumíveis também devem implementar o recurso de **restauração** do StoreKit para ajudar os usuários a recuperar as compras existentes. 
