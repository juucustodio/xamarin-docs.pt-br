---
title: Comprar produtos não consumíveis no xamarin
description: Este documento descreve os produtos não consumíveis no xamarin, que são recursos adquiridos por um usuário que permanecem disponíveis por tempo indeterminado, independentemente do dispositivo.
ms.prod: xamarin
ms.assetid: 635D9CA2-6BCA-53E1-7B10-968029AA3493
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/18/2017
ms.openlocfilehash: db193493ea11a7963fe5fc57f0c45978c26a7a74
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/05/2018
ms.locfileid: "34786945"
---
# <a name="purchasing-non-consumable-products-in-xamarinios"></a>Comprar produtos não consumíveis no xamarin

Não consumível produtos são 'propriedade' pelo cliente. A expectativa é que eles sempre terão acesso a eles, mesmo se o dispositivo é perdido ou roubado ou comprar um novo. Eles são úteis para manuais, problemas de revista, níveis de jogos, filtros de fotos, 'recursos do pro', etc. Depois que um usuário comprou um produto não consumíveis, eles nunca precisarão pague-lo novamente. Se seu código acidentalmente permite que eles tente, StoreKit mostrará uma mensagem que já foi comprado.

## <a name="non-consumable-products-sample"></a>Exemplo de produtos não consumíveis

O [InAppPurchaseSample código](https://developer.xamarin.com/samples/monotouch/StoreKit/) contém um projeto chamado *NonConsumables*. O exemplo de código demonstra como implementar produtos não consumíveis usando filtros de foto como um exemplo. Depois que você comprar um filtro você pode aplicá-lo à foto repetidamente. Você nunca precisa adquiri-lo novamente.   
   
   
   
 O processo de compra é mostrado nesta série de capturas de tela – o **comprar** botão se transforma no botão de ativação de recursos:   
   
   
   
 [![](purchasing-non-consumable-products-images/image34.png "O processo de compra é mostrado nesta série de capturas de tela")](purchasing-non-consumable-products-images/image34.png#lightbox)   
   
   
   
 O processo de compra é o mesmo que um produto pode ser consumido; a principal diferença é como a compra é controlada no código do aplicativo. Neste exemplo, que o botão comprar só está disponível se o produto já não tiver sido comprado, caso contrário, o botão ativa o recurso em si.   
   
   
   

O diagrama a seguir mostra as interações entre classes e o servidor de App Store para realizar uma compra não consumíveis produto:   
   
   
   
 [![](purchasing-non-consumable-products-images/image35.png "As interações entre classes e o servidor de armazenamento de aplicativo para executar um produto não consumíveis de compra")](purchasing-non-consumable-products-images/image35.png#lightbox)   
   
   
   
 A principal diferença do exemplo consumível é que, após a conclusão da compra a interface do usuário é atualizada para evitar a compra novamente. Neste exemplo, a notificação de uma transação bem-sucedida atualiza a interface do usuário para que o **comprar** botão é convertido em um botão que ativa o recurso em si.

## <a name="re-purchasing-non-consumable-products"></a>Produtos não consumíveis compras novamente

Normalmente, seu código deve ocultar ou realocar um botão de compra depois que o produto tem sido comprado com êxito, para impedir que o usuário tentar adquirir o produto novamente. O aplicativo de exemplo faz isso alterando o **comprar** botão no botão que faz com que o filtro de foto de exemplo funcionar.   
   
   
   
 Há situações em que um aplicativo não pode determinar se um produto não consumíveis já foi comprado:

-  Se um aplicativo é excluído e novamente está instalado em um dispositivo, todos os registros de compra desaparecerão (a menos que / até que o usuário faz uma restauração de backup). 
-  Se o usuário tiver o aplicativo instalado em dispositivos de duas (ou mais) e faz uma compra em um dos dispositivos. Os outros dispositivos continuarão mostrando o produto disponível para compra. 
-  Se um cliente tenta novamente adquirir o produto não consumíveis nessas situações, a loja de aplicativos terão o produto novamente sem custos adicionais. A interface do usuário será exibido inicialmente realizar uma compra (por exemplo, um alerta de confirmação é exibido e a ID da Apple, será necessário) no entanto o usuário, em seguida, verá uma mensagem informando que o produto já foi comprado.  
   
   
   
 O caminho do código nesse cenário é exatamente o mesmo que uma compra regular, as únicas diferenças são:

-  O usuário não seja cobrado novamente para o produto.
-  O `SKPaymentTransaction` objeto passado para o aplicativo terá uma `OriginalTransaction` propriedade refere-se à transação que foi gerada quando o produto foi adquirido inicialmente. 
-  Aplicativos que vendem produtos não consumível também devem implementar do StoreKit **restaurar** recurso para ajudar os usuários a recuperar compras existentes. 
