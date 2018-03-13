---
title: Comprar produtos de consumo
ms.topic: article
ms.prod: xamarin
ms.assetid: E0CB4A0F-C3FA-3933-58A7-13246971D677
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/18/2017
ms.openlocfilehash: 3e5875249d7a84412b1c30580c3a8038cb53892f
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2018
---
# <a name="purchasing-consumable-products"></a>Comprar produtos de consumo

Produtos de consumo são mais simples de implementar, pois não há nenhum requisito 'restore'. Eles são úteis para produtos como moeda do jogo ou um pedaço de uso único de funcionalidade. Os usuários novamente podem comprar produtos consumíveis por e-failover novamente.

## <a name="built-in-product-delivery"></a>Entrega de produtos internos

O código de exemplo que acompanha este documento demonstra produtos internos – as IDs de produto são embutidos em código no aplicativo, porque eles estão estritamente ligados ao código 'desbloqueia' o recurso depois de pagamento. O processo de compra pode ser visualizado como este:   
   
[![A visualização de processo de compra](purchasing-consumable-products-images/image26.png)](purchasing-consumable-products-images/image26.png#lightbox)     
   
 O fluxo de trabalho básico é:   
   
   
   
 1. O aplicativo adiciona um `SKPayment` para a fila. Se necessário, o usuário será solicitado a fornecer sua ID Apple e solicitado a confirmar o pagamento.   
   
 2. StoreKit envia a solicitação para o servidor para processamento.   
   
 3. Quando a transação é concluída, o servidor responde com uma confirmação de transação.   
   
 4. O `SKPaymentTransactionObserver` subclasse recebe a confirmação e a processa.   
   
 5. O aplicativo permite que o produto (Atualizando `NSUserDefaults` ou outro mecanismo) e, em seguida, chama do StoreKit `FinishTransaction`.

Há outro tipo de fluxo de trabalho – *Server-Delivered produtos* – que é discutido posteriormente neste documento (consulte a seção *verificação de recebimento e produtos Server-Delivered*).

## <a name="consumable-products-example"></a>Exemplo de produtos de consumo

O [InAppPurchaseSample código](https://developer.xamarin.com/samples/monotouch/StoreKit/) contém um projeto chamado *consumível* que implementa básico 'do jogo moeda' (chamado de "testar créditos"). O exemplo mostra como implementar os dois produtos de compra no aplicativo para permitir que o usuário comprar como muitos "créditos macaco" desejarem – em um aplicativo real também seria uma forma de gastos-los!   
   
   
   
 O aplicativo é mostrado nessas capturas de tela – cada compra adiciona mais "créditos macaco" saldo do usuário:   
   
   
   
 [![Cada compra adiciona mais créditos de monkey do saldo de usuários](purchasing-consumable-products-images/image27.png)](purchasing-consumable-products-images/image27.png#lightbox)   
   
   
   
 As interações entre classes personalizadas, StoreKit e loja de aplicativos tem esta aparência:   
   
   
   
 [![As interações entre classes personalizadas, StoreKit e loja de aplicativos](purchasing-consumable-products-images/image28.png)](purchasing-consumable-products-images/image28.png#lightbox)

&nbsp;

### <a name="viewcontroller-methods"></a>Métodos de ViewController

Além das propriedades e métodos necessários para recuperar informações do produto, o controlador de exibição requer observadores de notificação adicional ouvir as notificações relacionadas a compra. Essas são apenas `NSObjects` que será registrado e removido em `ViewWillAppear` e `ViewWillDisappear` respectivamente.

```csharp
NSObject succeededObserver, failedObserver;
```

O construtor também criará o `SKProductsRequestDelegate` subclasse ( `InAppPurchaseManager`) que por sua vez cria e registra o `SKPaymentTransactionObserver` ( `CustomPaymentObserver`).   
   
   
   
 É a primeira parte do processamento de uma transação de compra no aplicativo lidar com o pressionamento do botão quando o usuário deseja comprar algo, conforme mostrado no código a seguir do aplicativo de exemplo:

```csharp
buy5Button.TouchUpInside += (sender, e) => {
   iap.PurchaseProduct (Buy5ProductId);
};
buy10Button.TouchUpInside += (sender, e) => {
   iap.PurchaseProduct (Buy10ProductId);
};
```

   
   
 A segunda parte da interface do usuário está manipulando a notificação de que a transação foi bem-sucedida, nesse caso, atualizando o saldo exibido:

```csharp
priceObserver = NSNotificationCenter.DefaultCenter.AddObserver (InAppPurchaseManager.InAppPurchaseManagerTransactionSucceededNotification,
(notification) => {
   balanceLabel.Text = CreditManager.Balance() + " monkey credits";
});
```

A parte final da interface do usuário está exibindo uma mensagem se uma transação foi cancelada por alguma razão. No código de exemplo uma mensagem é gravada simplesmente a janela de saída:

```csharp
failedObserver = NSNotificationCenter.DefaultCenter.AddObserver (InAppPurchaseManager.InAppPurchaseManagerTransactionFailedNotification,
(notification) => {
   Console.WriteLine ("Transaction Failed");
});
```

Além desses métodos no controlador de exibição, uma transação de compra do produto consumível também requer código no `SKProductsRequestDelegate` e `SKPaymentTransactionObserver`.

### <a name="inapppurchasemanager-methods"></a>Métodos de InAppPurchaseManager

O código de exemplo implementa um número de compra relacionados métodos na classe InAppPurchaseManager, incluindo o `PurchaseProduct` método que cria um `SKPayment` de instância e o adiciona à fila de processamento:

```csharp
public void PurchaseProduct(string appStoreProductId)
{
   SKPayment payment = SKPayment.PaymentWithProduct (appStoreProductId);
   SKPaymentQueue.DefaultQueue.AddPayment (payment);
}
```

Adicionar o pagamento para a fila é uma operação assíncrona. O aplicativo recupera o controle enquanto StoreKit processa a transação e o envia para servidores da Apple. É nesse ponto que iOS verificará se o usuário está conectado à loja de aplicativos e solicitará a ela uma ID Apple e a senha se necessário.   
   
   
   
 Supondo que o usuário com êxito se autentica com a loja de aplicativos e concordar com a transação, o `SKPaymentTransactionObserver` receberá resposta do StoreKit e chame o método a seguir para atender a transação e finalizá-la.

```csharp
public void CompleteTransaction (SKPaymentTransaction transaction)
{
   var productId = transaction.Payment.ProductIdentifier;
   // Register the purchase, so it is remembered for next time
   PhotoFilterManager.Purchase(productId);
   FinishTransaction(transaction, true);
}
```

A última etapa é garantir que você notificar StoreKit que você terá preenchido com êxito a transação chamando `FinishTransaction`:

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

Depois que o produto é entregue, `SKPaymentQueue.DefaultQueue.FinishTransaction` deve ser chamado para remover a transação de fila de pagamento.

### <a name="skpaymenttransactionobserver-custompaymentobserver-methods"></a>Métodos de SKPaymentTransactionObserver (CustomPaymentObserver)

Chamadas de StoreKit o `UpdatedTransactions` método quando ele recebe uma resposta de servidores da Apple e passa uma matriz de `SKPaymentTransaction` objetos para o código a ser inspecionado. O método executa um loop em cada transação e executa uma função diferente com base no estado da transação (conforme mostrado aqui):

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

O `CompleteTransaction` método foi abordado anteriormente nesta seção – ele salva os detalhes de compra para `NSUserDefaults`, finaliza a transação com StoreKit e finalmente notifica a interface do usuário para atualizar.

### <a name="purchasing-multiple-products"></a>Vários produtos de compra

Se isso faz sentido em seu aplicativo para adquirir vários produtos, use o `SKMutablePayment` de classe e defina o campo Quantidade:

```csharp
public void PurchaseProduct(string appStoreProductId)
{
   SKMutablePayment payment = SKMutablePayment.PaymentWithProduct (appStoreProductId);
   payment.Quantity = 4; // hardcoded as an example
   SKPaymentQueue.DefaultQueue.AddPayment (payment);
}
```

O código de tratamento de transações concluídas também deve consultar a propriedade de quantidade para atender corretamente a compra:

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

Quando o usuário adquire várias instâncias, o alerta de confirmação StoreKit refletirá a quantidade, o preço unitário e o preço total que serão cobrados, conforme mostrado na seguinte captura de tela:

[![Confirmação de compra](purchasing-consumable-products-images/image30.png)](purchasing-consumable-products-images/image30.png#lightbox)

## <a name="handling-network-outages"></a>Tratamento de falhas de rede

Compras no aplicativo exigem uma conexão de rede de trabalho para StoreKit para se comunicar com servidores da Apple. Se uma conexão de rede não estiver disponível, em seguida, compra no aplicativo estará disponível.

### <a name="product-requests"></a>Solicitações de produto

Se a rede não está disponível ao fazer uma `SKProductRequest`, o `RequestFailed` método o `SKProductsRequestDelegate` subclasse ( `InAppPurchaseManager`) será chamado, conforme mostrado abaixo:

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

O ViewController, em seguida, aguarda a notificação e exibe uma mensagem nos botões de compra:

```csharp
requestObserver = NSNotificationCenter.DefaultCenter.AddObserver (InAppPurchaseManager.InAppPurchaseManagerRequestFailedNotification,
(notification) => {
   Console.WriteLine ("Request Failed");
   buy5Button.SetTitle ("Network down?", UIControlState.Disabled);
   buy10Button.SetTitle ("Network down?", UIControlState.Disabled);
});
```

Como uma conexão de rede pode ser transitório em dispositivos móveis, aplicativos podem deseja monitorar o status da rede usando a estrutura de SystemConfiguration e tente novamente quando uma conexão de rede está disponível. Consulte da Apple ou o que ele usa.

### <a name="purchase-transactions"></a>Transações de compra

A fila de pagamento StoreKit armazenará e compra encaminhar solicitações se possível, para que o efeito de uma interrupção da rede irão variar dependendo de quando a rede falhou durante o processo de compra.   
   
   
   
 Se ocorrer um erro durante uma transação, o `SKPaymentTransactionObserver` subclasse ( `CustomPaymentObserver`) terá o `UpdatedTransactions` método chamado e o `SKPaymentTransaction` classe estará em estado de falha.

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

O `FailedTransaction` método detecta se o erro ocorreu devido a cancelamento de usuário, como mostrado aqui:

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

Mesmo se uma transação falha, o `FinishTransaction` método deve ser chamado para remover a transação de fila de pagamento:

```csharp
SKPaymentQueue.DefaultQueue.FinishTransaction(transaction);
```

O exemplo de código, em seguida, envia uma notificação para que o ViewController pode exibir uma mensagem. Aplicativos não devem mostrar adicional de mensagem se o usuário cancelou a transação. Outros códigos de erro que podem ocorrer incluem:

```csharp
FailedTransaction Code=0 Cannot connect to iTunes Store
FailedTransaction Code=5002 An unknown error has occurred
FailedTransaction Code=5020 Forget Your Password?
Applications may detect and respond to specific error codes, or handle them in the same way.
```

## <a name="handling-restrictions"></a>Tratamento de restrições

O **Configurações > Geral > restrições** recurso do iOS permite que os usuários bloquear determinados recursos do seu dispositivo.   
   
   
   
 Você pode consultar se o usuário tem permissão para fazer compras no aplicativo por meio de `SKPaymentQueue.CanMakePayments` método. Se isso retornar falso, em seguida, o usuário não pode acessar a compra no aplicativo. StoreKit automaticamente exibirá uma mensagem de erro para o usuário se for tentada uma compra. Verificando esse valor seu aplicativo em vez disso, pode ocultar os botões de compra ou executar alguma outra ação para ajudar o usuário.   
   
   
   
 No `InAppPurchaseManager.cs` arquivo o `CanMakePayments` método ajusta a função StoreKit, desta forma:

```csharp
public bool CanMakePayments()
{
   return SKPaymentQueue.CanMakePayments;
}
```

Para testar esse método, use o **restrições** recurso do iOS para desabilitar **compras no aplicativo**:   
   
   
   
 [![Use o recurso de restrições do iOS para desabilitar compras no aplicativo](purchasing-consumable-products-images/image31.png)](purchasing-consumable-products-images/image31.png#lightbox)   
   
   
   
 Esse código de exemplo da `ConsumableViewController` reage a `CanMakePayments` retornar false exibindo **AppStore desabilitado** texto nos botões desabilitados.

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

O aplicativo é semelhante a quando o **compras no aplicativo** recurso é restrito – os botões de compra estão desabilitados.   
   
   
   
 [![O aplicativo tem esta aparência quando as compras em aplicativo recurso está restrito a compra botões estão desabilitados](purchasing-consumable-products-images/image32.png)](purchasing-consumable-products-images/image32.png#lightbox)   
   
   
   

Informações de produto ainda poderá ser solicitada quando `CanMakePayments` for false, para o aplicativo ainda possa recuperar e exibir os preços. Isso significa que se removemos o `CanMakePayments` verificação do código os botões de compra prefere ser ativo, no entanto, quando uma tentativa de uma compra o usuário verá uma mensagem que **compras no aplicativo não são permitidas** (gerados pela StoreKit Quando a fila de pagamento é acessada):   
   
   
   
 [![Compras no aplicativo não são permitidas](purchasing-consumable-products-images/image33.png)](purchasing-consumable-products-images/image33.png#lightbox)   
   
   
   
 Aplicativos reais podem adotar uma abordagem diferente para lidar com a restrição, como ocultar os botões completamente e talvez até oferecendo uma mensagem mais detalhada que o alerta que StoreKit mostra automaticamente.

