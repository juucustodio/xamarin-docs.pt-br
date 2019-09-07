---
title: Comprando produtos consumíveis no Xamarin. iOS
description: Este documento descreve os produtos consumíveis no Xamarin. iOS. Os produtos consumíveis são partes de funcionalidade de uso único, como a moeda no jogo.
ms.prod: xamarin
ms.assetid: E0CB4A0F-C3FA-3933-58A7-13246971D677
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/18/2017
ms.openlocfilehash: f48f84c704fa8ce20ce24dfbfaca2df23a8494eb
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70752712"
---
# <a name="purchasing-consumable-products-in-xamarinios"></a>Comprando produtos consumíveis no Xamarin. iOS

Os produtos consumíveis são os mais simples de implementar, pois não há nenhum requisito de "restauração". Eles são úteis para produtos como a moeda no jogo ou uma parte de funcionalidade de uso único. Os usuários podem adquirir novamente os produtos consumíveis novamente.

## <a name="built-in-product-delivery"></a>Entrega de produtos interna

O código de exemplo que acompanha este documento demonstra produtos internos – as IDs de produto são codificadas no aplicativo porque estão intimamente ligadas ao código que ' desbloqueia ' o recurso após o pagamento. O processo de compra pode ser visualizado da seguinte maneira:   
   
[![A visualização do processo de compra](purchasing-consumable-products-images/image26.png)](purchasing-consumable-products-images/image26.png#lightbox)     
   
 O fluxo de trabalho básico é:   
   
 1. O aplicativo adiciona um `SKPayment` à fila. Se necessário, o usuário receberá uma solicitação para sua ID da Apple e será solicitado a confirmar o pagamento.   
   
 2. StoreKit envia a solicitação ao servidor para processamento.   
   
 3. Quando a transação for concluída, o servidor responderá com um recibo de transação.   
   
 4. A `SKPaymentTransactionObserver` subclasse recebe o recibo e o processa.   
   
 5. O aplicativo habilita o produto (Atualizando `NSUserDefaults` ou algum outro mecanismo) e, em seguida, chama `FinishTransaction`o StoreKit.

Há outro tipo de fluxo de trabalho – *produtos entregues pelo servidor* – que é discutido posteriormente no documento (consulte a seção *verificação de recebimento e produtos entregues pelo servidor*).

## <a name="consumable-products-example"></a>Exemplo de produtos consumíveis

O [código InAppPurchaseSample](https://docs.microsoft.com/samples/xamarin/ios-samples/storekit) contém um projeto chamado *itens consumíveis* que implementa uma "moeda no jogo" básica (chamada de "créditos do macaco"). O exemplo a seguir mostra como implementar dois produtos de compra no aplicativo para permitir que o usuário compre quantos "créditos de macaco" desejar – em um aplicativo real também seria uma forma de gastar!   

O aplicativo é mostrado nessas capturas de tela – cada compra adiciona mais "créditos de macaco" ao saldo do usuário:   

 [![Cada compra adiciona mais créditos de macaco ao saldo dos usuários](purchasing-consumable-products-images/image27.png)](purchasing-consumable-products-images/image27.png#lightbox)   

As interações entre as classes personalizadas, StoreKit e a App Store têm esta aparência:   

 [![As interações entre classes personalizadas, StoreKit e a loja de aplicativos](purchasing-consumable-products-images/image28.png)](purchasing-consumable-products-images/image28.png#lightbox)

### <a name="viewcontroller-methods"></a>Métodos ViewController

Além das propriedades e dos métodos necessários para recuperar as informações do produto, o controlador de exibição exige que os observadores de notificação adicionais escutem as notificações relacionadas à compra. Eles são apenas `NSObjects` aqueles que serão registrados e removidos no `ViewWillAppear` e `ViewWillDisappear` , respectivamente.

```csharp
NSObject succeededObserver, failedObserver;
```

O Construtor também criará a `SKProductsRequestDelegate` subclasse ( `InAppPurchaseManager`) que, por sua vez, cria e `SKPaymentTransactionObserver` registra `CustomPaymentObserver`o ().   

A primeira parte do processamento de uma transação de compra no aplicativo é manipular o pressionamento de botão quando o usuário deseja comprar algo, conforme mostrado no código a seguir do aplicativo de exemplo:

```csharp
buy5Button.TouchUpInside += (sender, e) => {
   iap.PurchaseProduct (Buy5ProductId);
};
buy10Button.TouchUpInside += (sender, e) => {
   iap.PurchaseProduct (Buy10ProductId);
};
```

A segunda parte da interface do usuário está tratando a notificação de que a transação foi bem-sucedida, nesse caso, atualizando o saldo exibido:

```csharp
priceObserver = NSNotificationCenter.DefaultCenter.AddObserver (InAppPurchaseManager.InAppPurchaseManagerTransactionSucceededNotification,
(notification) => {
   balanceLabel.Text = CreditManager.Balance() + " monkey credits";
});
```

A parte final da interface do usuário será exibir uma mensagem se uma transação for cancelada por algum motivo. No código de exemplo, uma mensagem é simplesmente gravada na janela de saída:

```csharp
failedObserver = NSNotificationCenter.DefaultCenter.AddObserver (InAppPurchaseManager.InAppPurchaseManagerTransactionFailedNotification,
(notification) => {
   Console.WriteLine ("Transaction Failed");
});
```

Além desses métodos no controlador de exibição, uma transação de compra de produto consumível também requer código no `SKProductsRequestDelegate` e no. `SKPaymentTransactionObserver`

### <a name="inapppurchasemanager-methods"></a>Métodos InAppPurchaseManager

O código de exemplo implementa um número de métodos relacionados à compra na classe InAppPurchaseManager, incluindo `PurchaseProduct` o método que cria `SKPayment` uma instância e a adiciona à fila para processamento:

```csharp
public void PurchaseProduct(string appStoreProductId)
{
   SKPayment payment = SKPayment.PaymentWithProduct (appStoreProductId);
   SKPaymentQueue.DefaultQueue.AddPayment (payment);
}
```

A adição do pagamento à fila é uma operação assíncrona. O aplicativo recupera o controle enquanto o StoreKit processa a transação e a envia para os servidores da Apple. Nesse ponto, o iOS verificará se o usuário está conectado à loja de aplicativos e solicitará uma ID da Apple e uma senha, se necessário.   

Supondo que o usuário se autentique com êxito com a loja de aplicativos e concorde com a `SKPaymentTransactionObserver` transação, o receberá a resposta do StoreKit e chamará o método a seguir para atender à transação e finalizá-la.

```csharp
public void CompleteTransaction (SKPaymentTransaction transaction)
{
   var productId = transaction.Payment.ProductIdentifier;
   // Register the purchase, so it is remembered for next time
   PhotoFilterManager.Purchase(productId);
   FinishTransaction(transaction, true);
}
```

A última etapa é garantir que você Notifique a StoreKit que você atendeu com êxito à transação, chamando `FinishTransaction`:

```csharp
public void FinishTransaction(SKPaymentTransaction transaction, bool wasSuccessful)
{
   // remove the transaction from the payment queue.
   SKPaymentQueue.DefaultQueue.FinishTransaction(transaction);  // THIS IS IMPORTANT - LET'S APPLE KNOW WE'RE DONE !!!!
   using (var pool = new NSAutoreleasePool()) {
       NSDictionary userInfo = NSDictionary.FromObjectsAndKeys(new NSObject[] {transaction},new NSObject[] {new NSString("transaction")});
       if (wasSuccessful) {
           // send out a notification that we've finished the transaction
           NSNotificationCenter.DefaultCenter.PostNotificationName (InAppPurchaseManagerTransactionSucceededNotification, this, userInfo);
       } else {
           // send out a notification for the failed transaction
           NSNotificationCenter.DefaultCenter.PostNotificationName (InAppPurchaseManagerTransactionFailedNotification, this, userInfo);
       }
   }
}
```

Depois que o produto for entregue `SKPaymentQueue.DefaultQueue.FinishTransaction` , deverá ser chamado para remover a transação da fila de pagamentos.

### <a name="skpaymenttransactionobserver-custompaymentobserver-methods"></a>Métodos SKPaymentTransactionObserver (CustomPaymentObserver)

StoreKit chama o `UpdatedTransactions` método quando recebe uma resposta dos servidores da Apple e passa uma matriz de `SKPaymentTransaction` objetos para que seu código inspecione. O método percorre cada transação e executa uma função diferente com base no estado da transação (como mostrado aqui):

```csharp
public override void UpdatedTransactions (SKPaymentQueue queue, SKPaymentTransaction[] transactions)
{
   foreach (SKPaymentTransaction transaction in transactions)
   {
       switch (transaction.TransactionState)
       {
           case SKPaymentTransactionState.Purchased:
              theManager.CompleteTransaction(transaction);
               break;
           case SKPaymentTransactionState.Failed:
              theManager.FailedTransaction(transaction);
               break;
           default:
               break;
       }
   }
}
```

O `CompleteTransaction` método foi abordado anteriormente nesta seção – ele salva os detalhes da compra `NSUserDefaults`em, finaliza a transação com StoreKit e, por fim, notifica a interface do usuário para atualizar.

### <a name="purchasing-multiple-products"></a>Comprando vários produtos

Se fizer sentido em seu aplicativo para comprar vários produtos, use a `SKMutablePayment` classe e defina o campo quantidade:

```csharp
public void PurchaseProduct(string appStoreProductId)
{
   SKMutablePayment payment = SKMutablePayment.PaymentWithProduct (appStoreProductId);
   payment.Quantity = 4; // hardcoded as an example
   SKPaymentQueue.DefaultQueue.AddPayment (payment);
}
```

O código que manipula a transação concluída também deve consultar a propriedade Quantity para atender corretamente a compra:

```csharp
public void CompleteTransaction (SKPaymentTransaction transaction)
{
   var productId = transaction.Payment.ProductIdentifier;
   var qty = transaction.Payment.Quantity;
   if (productId == ConsumableViewController.Buy5ProductId)
       CreditManager.Add(5 * qty);
   else if (productId == ConsumableViewController.Buy10ProductId)
       CreditManager.Add(10 * qty);
   else
       Console.WriteLine ("Shouldn't happen, there are only two products");
   FinishTransaction(transaction, true);
}
```

Quando o usuário adquire várias quantidades, o alerta de confirmação do StoreKit refletirá a quantidade, o preço unitário e o preço total que serão cobrados, conforme mostrado na seguinte captura de tela:

[![Confirmando uma compra](purchasing-consumable-products-images/image30.png)](purchasing-consumable-products-images/image30.png#lightbox)

## <a name="handling-network-outages"></a>Lidando com interrupções de rede

As compras no aplicativo exigem uma conexão de rede funcional para que o StoreKit se comunique com os servidores da Apple. Se uma conexão de rede não estiver disponível, a compra no aplicativo não estará disponível.

### <a name="product-requests"></a>Solicitações de produtos

Se a rede não estiver disponível durante a criação `SKProductRequest`de um `RequestFailed` , o método `SKProductsRequestDelegate` da subclasse ( `InAppPurchaseManager`) será chamado, conforme mostrado abaixo:

```csharp
public override void RequestFailed (SKRequest request, NSError error)
{
   using (var pool = new NSAutoreleasePool()) {
       NSDictionary userInfo = NSDictionary.FromObjectsAndKeys(new NSObject[] {error},new NSObject[] {new NSString("error")});
       // send out a notification for the failed transaction
       NSNotificationCenter.DefaultCenter.PostNotificationName (InAppPurchaseManagerRequestFailedNotification, this, userInfo);
   }
}
```

Em seguida, o ViewController escuta a notificação e exibe uma mensagem nos botões de compra:

```csharp
requestObserver = NSNotificationCenter.DefaultCenter.AddObserver (InAppPurchaseManager.InAppPurchaseManagerRequestFailedNotification,
(notification) => {
   Console.WriteLine ("Request Failed");
   buy5Button.SetTitle ("Network down?", UIControlState.Disabled);
   buy10Button.SetTitle ("Network down?", UIControlState.Disabled);
});
```

Como uma conexão de rede pode ser transitória em dispositivos móveis, os aplicativos podem querer monitorar o status da rede usando a estrutura SystemConfiguration e tentar novamente quando uma conexão de rede estiver disponível. Consulte a Apple ou o que o utiliza.

### <a name="purchase-transactions"></a>Transações de compra

A fila de pagamento StoreKit armazenará e encaminhará solicitações de compra, se possível, para que o efeito de uma interrupção de rede varie dependendo de quando a rede falhou durante o processo de compra.   

Se ocorrer um erro durante uma `SKPaymentTransactionObserver` transação, a subclasse ( `CustomPaymentObserver`) terá o `UpdatedTransactions` método chamado e a `SKPaymentTransaction` classe estará no estado de falha.

```csharp
public override void UpdatedTransactions (SKPaymentQueue queue, SKPaymentTransaction[] transactions)
{
   foreach (SKPaymentTransaction transaction in transactions)
   {
       switch (transaction.TransactionState)
       {
           case SKPaymentTransactionState.Purchased:
               theManager.CompleteTransaction(transaction);
               break;
           case SKPaymentTransactionState.Failed:
               theManager.FailedTransaction(transaction);
               break;
           default:
               break;
       }
   }
}
```

O `FailedTransaction` método detecta se o erro ocorreu devido ao cancelamento do usuário, como mostrado aqui:

```csharp
public void FailedTransaction (SKPaymentTransaction transaction)
{
   //SKErrorPaymentCancelled == 2
   if (transaction.Error.Code == 2) // user cancelled
       Console.WriteLine("User CANCELLED FailedTransaction Code=" + transaction.Error.Code + " " + transaction.Error.LocalizedDescription);
   else // error!
       Console.WriteLine("FailedTransaction Code=" + transaction.Error.Code + " " + transaction.Error.LocalizedDescription);
   FinishTransaction(transaction,false);
}
```

Mesmo que uma transação falhe, o `FinishTransaction` método deve ser chamado para remover a transação da fila de pagamentos:

```csharp
SKPaymentQueue.DefaultQueue.FinishTransaction(transaction);
```

Em seguida, o código de exemplo envia uma notificação para que o ViewController possa exibir uma mensagem. Os aplicativos não devem mostrar uma mensagem adicional se o usuário cancelou a transação. Outros códigos de erro que podem ocorrer incluem:

```csharp
FailedTransaction Code=0 Cannot connect to iTunes Store
FailedTransaction Code=5002 An unknown error has occurred
FailedTransaction Code=5020 Forget Your Password?
Applications may detect and respond to specific error codes, or handle them in the same way.
```

## <a name="handling-restrictions"></a>Restrições de tratamento

As **configurações > recurso de restrições de > geral** do IOS permitem que os usuários bloqueiem determinados recursos de seu dispositivo.   

Você pode consultar se o usuário tem permissão para fazer compras no aplicativo por meio do `SKPaymentQueue.CanMakePayments` método. Se isso retornar false, o usuário não poderá acessar a compra no aplicativo. O StoreKit exibirá automaticamente uma mensagem de erro para o usuário se uma compra for tentada. Ao marcar esse valor, seu aplicativo pode ocultar os botões de compra ou executar alguma outra ação para ajudar o usuário.   

No arquivo, o `CanMakePayments` método encapsula a função StoreKit como esta: `InAppPurchaseManager.cs`

```csharp
public bool CanMakePayments()
{
   return SKPaymentQueue.CanMakePayments;
}
```

Para testar esse método, use o recurso de **restrições** do IOS para desabilitar **compras no aplicativo**:   

 [![Usar o recurso de restrições do iOS para desabilitar compras no aplicativo](purchasing-consumable-products-images/image31.png)](purchasing-consumable-products-images/image31.png#lightbox)   

Esse código de `ConsumableViewController` exemplo reage a `CanMakePayments` retornar false exibindo o texto **desabilitado AppStore** nos botões desabilitados.

```csharp
// only if we can make payments, request the prices
if (iap.CanMakePayments()) {
   // now go get prices, if we don't have them already
   if (!pricesLoaded)
       iap.RequestProductData(products); // async request via StoreKit -> App Store
} else {
   // can't make payments (purchases turned off in Settings?)
   // the buttons are disabled by default, and only enabled when prices are retrieved
   buy5Button.SetTitle ("AppStore disabled", UIControlState.Disabled);
   buy10Button.SetTitle ("AppStore disabled", UIControlState.Disabled);
}
```

O aplicativo tem esta aparência quando o recurso de **compras no aplicativo** é restrito – os botões de compra são desabilitados.   

 [![O aplicativo é semelhante a este quando o recurso de compras no aplicativo está restrito os botões de compra estão desabilitados](purchasing-consumable-products-images/image32.png)](purchasing-consumable-products-images/image32.png#lightbox)   

As informações do produto ainda podem ser `CanMakePayments` solicitadas quando for false, para que o aplicativo ainda possa recuperar e exibir os preços. Isso significa que, se removermos a `CanMakePayments` verificação do código, os botões de compra ainda estarão ativos; no entanto, quando uma compra for tentada, o usuário verá uma mensagem informando que as **compras no aplicativo não são permitidas** (geradas pelo StoreKit quando a fila de pagamento é acessado):   

 [![Compras no aplicativo não são permitidas](purchasing-consumable-products-images/image33.png)](purchasing-consumable-products-images/image33.png#lightbox)   

Os aplicativos do mundo real podem adotar uma abordagem diferente para lidar com a restrição, como ocultar os botões completamente e, talvez, oferecer uma mensagem mais detalhada do que o alerta que o StoreKit mostra automaticamente.
