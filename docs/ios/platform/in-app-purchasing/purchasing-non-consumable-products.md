---
title: Comprar produtos não consumíveis no xamarin. IOS
description: Este documento descreve os produtos não consumíveis no xamarin. IOS, que são recursos comprados por um usuário que permanecem disponíveis por tempo indeterminado, independentemente do dispositivo.
ms.prod: xamarin
ms.assetid: 635D9CA2-6BCA-53E1-7B10-968029AA3493
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/18/2017
ms.openlocfilehash: 060403baf8ac28b9b160632a01471b9828735069
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61403188"
---
# <a name="purchasing-non-consumable-products-in-xamarinios"></a>Comprar produtos não consumíveis no xamarin. IOS

Produtos não consumíveis são 'propriedade' pelo cliente. A expectativa é que eles sempre terão acesso a elas, mesmo se o dispositivo é perdido ou roubado ou comprar um novo. Eles são úteis para livros, problemas de revista, níveis de jogos, filtros de foto, 'recursos do pro', etc. Depois que um usuário comprou um produto não consumíveis, eles nunca precisam pagar por ele novamente. Se seu código acidentalmente permite que eles tente, StoreKit mostrará uma mensagem que ele já tiver sido comprado.

## <a name="non-consumable-products-sample"></a>Exemplo de produtos não consumíveis

O [código InAppPurchaseSample](https://developer.xamarin.com/samples/monotouch/StoreKit/) contém um projeto chamado *NonConsumables*. O exemplo de código demonstra como implementar produtos não consumíveis usando filtros de foto como um exemplo. Depois de comprar um filtro você pode aplicá-lo para a foto repetidamente. Você nunca precise Compre-o novamente.   
   
   
   
 O processo de compra é mostrado nesta série de capturas de tela – o **comprar** botão se transforma no botão de ativação do recurso:   
   
   
   
 [![](purchasing-non-consumable-products-images/image34.png "O processo de compra é mostrado nesta série de capturas de tela")](purchasing-non-consumable-products-images/image34.png#lightbox)   
   
   
   
 O processo de compra é o mesmo que um produto consumível; a principal diferença está em como a compra é controlada no código do aplicativo. Neste exemplo, que no botão comprar só está disponível se o produto já não foi adquirido, caso contrário, o botão ativa o recurso em si.   
   
   
   

O diagrama a seguir mostra as interações entre classes e o servidor de aplicativo Store para realizar uma compra de produtos não consumíveis:   
   
   
   
 [![](purchasing-non-consumable-products-images/image35.png "Comprar as interações entre classes e o servidor de aplicativo Store para executar um produto não consumíveis")](purchasing-non-consumable-products-images/image35.png#lightbox)   
   
   
   
 A principal diferença do exemplo consumível é que, depois que a compra for concluída a interface do usuário é atualizada para evitar a compra novamente. Neste exemplo, a notificação de uma transação bem-sucedida atualiza a interface do usuário para que o **comprar** botão é convertido em um botão que ativa o recurso em si.

## <a name="re-purchasing-non-consumable-products"></a>Novamente, comprar produtos não consumíveis

O código normalmente deve ocultar ou realocar um botão de compra depois que o produto tem sido adquirido com êxito, para impedir que o usuário tentar adquiri-lo novamente. O aplicativo de exemplo faz isso alterando os **comprar** botão em um botão que faz com que o filtro de foto de exemplo funcione.   
   
   
   
 Há situações em que um aplicativo não pode determinar se um produto não consumíveis já tiver sido adquirido:

-  Se um aplicativo é excluído e instalado novamente em um dispositivo, todos os registros de compra desaparecerão (a menos que / até que o usuário faz uma restauração de backup). 
-  Se o usuário tiver o aplicativo instalado em dispositivos de dois (ou mais) e faz uma compra em um dos dispositivos. Os outros dispositivos continuarão mostrar os produtos disponíveis para compra. 
-  Se um cliente tenta novamente comprar um produto não consumíveis nessas situações, o aplicativo Store atenderá o produto novamente sem custos adicionais. A interface do usuário será exibido inicialmente realizar uma compra (por exemplo, um alerta de confirmação é exibido e a ID da Apple, será necessária) no entanto o usuário, em seguida, verá uma mensagem informando que o produto já foi comprado.  
   
   
   
 O caminho do código nesse cenário é exatamente o mesmo como uma aquisição regular, as únicas diferenças são:

-  O usuário não cobrado novamente para o produto.
-  O `SKPaymentTransaction` objeto passado para o aplicativo terá um `OriginalTransaction` propriedade refere-se à transação que foi gerada quando o produto foi adquirido inicialmente. 
-  Aplicativos que vendem produtos não consumíveis também devem implementar da StoreKit **restaurar** recurso para ajudar os usuários a recuperar compras existentes. 
