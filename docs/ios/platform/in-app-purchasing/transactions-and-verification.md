---
title: Transações e verificação em xamarin
description: Este documento descreve como permitir a restauração de compras passadas em um aplicativo xamarin. Ele também discute maneiras de proteger compras e produtos de servidor fornecido.
ms.prod: xamarin
ms.assetid: 84EDD2B9-3FAA-B3C7-F5E8-C1E5645B7C77
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/18/2017
ms.openlocfilehash: 2cb38df4bbabc3534f5c90c7695569d68349ccc3
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/05/2018
ms.locfileid: "34786919"
---
# <a name="transactions-and-verification-in-xamarinios"></a>Transações e verificação em xamarin

## <a name="restoring-past-transactions"></a>Restaurando após transações

Se seu aplicativo dá suporte a tipos de produto que são restauráveis, você deve incluir alguns elementos de interface do usuário para permitir que os usuários restaurar essas compras.
Essa funcionalidade permite que um cliente para adicionar o produto para dispositivos adicionais ou restaurar o produto para o mesmo dispositivo após sendo limpo ou remover e reinstalar o aplicativo. Os seguintes tipos de produto são restauráveis:

-  Produtos não consumível
-  Assinaturas automática renovável
-  Assinaturas gratuitas


O processo de restauração deve atualizar os registros de manter no dispositivo para atender a seus produtos. O cliente pode escolher restaurar a qualquer momento, em qualquer um dos seus dispositivos. O processo de restauração novamente envia todas as transações anteriores para esse usuário; o código do aplicativo deve determinar qual ação a ser executada com essas informações (por exemplo, verificando se já houver um registro de que compra no dispositivo e se não, criando um registro de compra e ativar o produto para o usuário).

### <a name="implementing-restore"></a>Implementação de restauração

A interface do usuário **restaurar** botão chama o método a seguir, que aciona RestoreCompletedTransactions no `SKPaymentQueue`.

```csharp
public void Restore()
{
   // theObserver will be notified of when the restored transactions start arriving <- AppStore
   SKPaymentQueue.DefaultQueue.RestoreCompletedTransactions();
}
```

StoreKit enviará a solicitação de restauração para servidores da Apple assincronamente.   
   
   
   
 Porque o `CustomPaymentObserver` está registrado como um observador de transação, ele receberá mensagens quando responderem servidores da Apple. A resposta conterá todas as transações que este usuário efetuou neste aplicativo (em todos os dispositivos). Os loops de código por meio de cada transação, detecta o estado restaurada e chama o `UpdatedTransactions` método processá-la, conforme mostrado abaixo:

```csharp
// called when the transaction status is updated
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
       case SKPaymentTransactionState.Restored:
           theManager.RestoreTransaction(transaction);
           break;
default:
           break;
       }
   }
}
```

Se não houver nenhum produto restaurável para o usuário `UpdatedTransactions` não for chamado.   
   
   
   
 O código de possíveis mais simples para restaurar uma determinada transação no exemplo faz as mesmas ações quando uma compra entra em vigor, exceto que o `OriginalTransaction` propriedade é usada para acessar a ID do produto:

```csharp
public void RestoreTransaction (SKPaymentTransaction transaction)
{
   // Restored Transactions always have an 'original transaction' attached
   var productId = transaction.OriginalTransaction.Payment.ProductIdentifier;
   // Register the purchase, so it is remembered for next time
   PhotoFilterManager.Purchase(productId); // it's as though it was purchased again
   FinishTransaction(transaction, true);
}
```

Uma implementação mais sofisticada pode verificar outros `transaction.OriginalTransaction` propriedades, como o número original de data e de recebimento. Essas informações serão úteis para alguns tipos de produto (como assinaturas).

#### <a name="restore-completion"></a>Conclusão de restauração

O `CustomPaymentObserver` tem dois métodos adicionais que serão chamados pelo StoreKit quando o processo de restauração for concluída (com êxito ou com falha), mostrado abaixo:

```csharp
public override void PaymentQueueRestoreCompletedTransactionsFinished (SKPaymentQueue queue)
{
   Console.WriteLine(" ** RESTORE Finished ");
}
public override void RestoreCompletedTransactionsFailedWithError (SKPaymentQueue queue, NSError error)
{
   Console.WriteLine(" ** RESTORE FailedWithError " + error.LocalizedDescription);
}
```

No exemplo a esses métodos não fazem nada, no entanto, um aplicativo real pode optar por implementar uma mensagem para o usuário ou alguns outros recursos.

## <a name="securing-purchases"></a>Proteção de compras

Os dois exemplos neste documentam use `NSUserDefaults` para rastrear compras:   
   
 **Consumível** – o 'saldo' de compras de crédito é um simples `NSUserDefaults` valor inteiro que é incrementado a cada compra.   
   
 **Não consumíveis** – cada compra de filtro de foto é armazenada como um par chave-valor em `NSUserDefaults`.

Usando `NSUserDefaults` mantém o código de exemplo simples, mas não oferece uma solução muito segura, talvez seja possível para usuários tecnicamente voltada atualizar as configurações (ignorando o mecanismo de pagamento).   
   
Observação: Aplicativos reais devem adotar um mecanismo seguro para armazenar adquiriu o conteúdo que não está sujeita à violação de usuário. Isso pode envolver a criptografia e/ou outras técnicas, incluindo autenticação de servidor remoto.   
   
 O mecanismo também deve ser criado para tirar proveito dos recursos internos de backup e recuperação do iOS, iTunes e iCloud. Isso garantirá que, depois que um usuário restaura um backup de suas compras anteriores estará disponíveis imediatamente.   
   
   
 Consulte seguro codificação guia da Apple para obter mais diretrizes específicas do iOS.

## <a name="receipt-verification-and-server-delivered-products"></a>Verificação de recebimento e produtos de servidor e entregue

Os exemplos neste documento até tem consistia somente do aplicativo se comunique diretamente com os servidores do App Store para realizar transações de compra, que desbloquear recursos ou capacidades já codificadas no aplicativo.   
   
   
   
 Apple fornece para um nível adicional de segurança de compra, permitindo que os recibos de compra a ser verificada independentemente por outro servidor, que pode ser útil para validar uma solicitação antes de entregar conteúdo digital como parte de uma compra (como um livro digital ou revista).   
   
   
   
 **Produtos internos** – como os exemplos neste documento, o produto que está sendo comprado existe como funcionalidade fornecida com o aplicativo. Uma compra no aplicativo permite que o usuário acessar a funcionalidade.
IDs de produto são embutidos em código.   
   
 **Produtos de servidor e entregue** – o produto consiste em conteúdo para download que é armazenado em um servidor remoto até que uma transação bem-sucedida faz com que o conteúdo a ser baixado.
Os exemplos podem incluir problemas de revista ou manuais. IDs de produto geralmente são originadas de um servidor externo (onde o conteúdo do produto também está hospedado). Os aplicativos devem implementar uma forma robusta de gravação quando uma transação for concluída, se falhar de download de conteúdo pode ser tentada novamente sem confundir o usuário.

### <a name="server-delivered-products"></a>Produtos de servidor e entregue

Alguns produtos conteúdo do, como livros e revistas (ou até mesmo um nível de jogo) precisa ser baixados de um servidor remoto durante o processo de compra. Isso significa que um servidor adicional é necessário para armazenar e entregar o conteúdo do produto depois de ser comprado.

#### <a name="getting-prices-for-server-delivered-products"></a>Obter os preços de produtos de servidor e entregue

Como os produtos sejam entregues remotamente, também é possível adicionar mais produtos ao longo do tempo (sem atualizar o código do aplicativo), como a adição de mais manuais ou novos problemas de uma revista. Para que o aplicativo possa descobrir esses produtos notícias e exibi-los para o usuário, o servidor adicional deve armazenar e fornecer essas informações.   
   
   
   
 [![](transactions-and-verification-images/image38.png "Obter os preços de produtos de servidor e entregue")](transactions-and-verification-images/image38.png#lightbox)   
   
   
   
 1. Informações sobre o produto deve ser armazenado em vários locais: no seu servidor e na iTunes conectar. Além disso, cada produto terá os arquivos de conteúdo associados a ele. Esses arquivos serão entregues após uma compra bem-sucedida.   
   
 2. Quando o usuário deseja adquirir um produto, o aplicativo deve determinar quais produtos estão disponíveis. Essas informações podem ser armazenadas em cache, mas devem ser entregue em um servidor remoto em que a lista mestre de produtos é armazenada.   
   
 3. O servidor retorna uma lista de IDs de produto para o aplicativo para analisar.   
   
 4. Em seguida, o aplicativo determina que essas IDs de produto para enviar ao StoreKit para recuperar os preços e descrições.   
   
 5. StoreKit envia a lista de IDs de produto para servidores da Apple.   
   
 6. Os servidores do iTunes responderem com informações de produto válida (descrição e o preço atual).   
   
 7. O aplicativo `SKProductsRequestDelegate` é passado para exibir as informações de produto para o usuário.

#### <a name="purchasing-server-delivered-products"></a>Compras de produtos entregues pelo servidor

Porque o servidor remoto requer alguma forma de validação que uma solicitação de conteúdo é válida (ie. pago para), as informações de recebimento são passadas para a autenticação. O servidor remoto encaminha dados iTunes para verificação e, se for bem-sucedida, inclui o conteúdo de produto na resposta para o aplicativo.   
   
 [![](transactions-and-verification-images/image39.png "Compras de produtos entregues pelo servidor")](transactions-and-verification-images/image39.png#lightbox)   
   
 1. O aplicativo adiciona um `SKPayment` para a fila. Se necessário, o usuário será solicitado a fornecer sua ID Apple e solicitado a confirmar o pagamento.   
   
 2. StoreKit envia a solicitação para o servidor para processamento.   
   
 3. Quando a transação é concluída, o servidor responde com uma confirmação de transação.   
   
 4. O `SKPaymentTransactionObserver` subclasse recebe a confirmação e a processa. Como o produto deve ser baixado de um servidor, o aplicativo inicia uma solicitação de rede para o servidor remoto.   
   
 5. A solicitação de download é acompanhada pelos dados de recebimento para que o servidor remoto pode verificar se que ele está autorizado a acessar o conteúdo. Cliente de rede do aplicativo aguarda uma resposta para esta solicitação.   
   
 6. Quando o servidor recebe uma solicitação de conteúdo, ele analisa os dados de recebimento e envia uma solicitação diretamente para os servidores do iTunes para verificar o recebimento é para uma transação válida. O servidor deve usar alguma lógica para determinar se deseja enviar a solicitação para a URL de produção ou de área restrita. Apple sugere sempre usando a URL de produção e alternar para a área restrita se status: 21007 (recebimento da área restrita enviado ao servidor de produção) – consulte [2259 de observação técnica perguntas frequentes sobre o n º 16](https://developer.apple.com/library/ios/#technotes/tn2259/_index.html).   
   
 7. iTunes verifica o recebimento e retornar um status de zero se ele é válido.   
   
 8. O servidor aguarda a resposta dos iTunes. Se receber uma resposta válida, o código deve localizar o arquivo de conteúdo do produto associado para incluir na resposta para o aplicativo.   
   
 9. O aplicativo recebe e analisa a resposta, salvando o conteúdo de produto para o sistema de arquivos do dispositivo.   
   
 10. O aplicativo permite que o produto e, em seguida, chama do StoreKit `FinishTransaction`. O aplicativo, em seguida, opcionalmente, pode exibir o conteúdo adquirido (por exemplo, mostrar a primeira página de um livro adquirido ou problema de revista).

Uma implementação alternativa para arquivos de conteúdo muito grande de produto pode envolver simplesmente armazenando a confirmação de transação na etapa 9 # para que a transação pode ser concluída rapidamente e fornecer uma interface de usuário para o usuário baixar o conteúdo real do produto em algum momento mais tarde. A solicitação de download subsequente pode enviar novamente o recebimento armazenado para acessar o arquivo de conteúdo de produto necessária.

### <a name="writing-server-side-receipt-verification-code"></a>Escrevendo código de verificação de recebimento do lado do servidor

Validar um recebimento no código do lado do servidor pode ser feito com uma HTTP POST solicitação/resposta simples que abrange as etapas #5 a 8 # no diagrama de fluxo de trabalho.   
   
   
   
 Extrair o `SKPaymentTansaction.TransactionReceipt` propriedade no aplicativo. Estes são os dados que precisam ser enviada ao iTunes para verificação (etapa 5 #).

Base64 codifica dos dados de confirmação de transação (a etapa 5 # ou #6).

Crie uma carga JSON simple como este:

```csharp
{
   "receipt-data" : "(base-64 encoded receipt here)"
}
```

HTTP POST JSON para [ https://buy.itunes.apple.com/verifyReceipt ](https://buy.itunes.apple.com/verifyReceipt) para produção ou [ https://sandbox.itunes.apple.com/verifyReceipt ](https://sandbox.itunes.apple.com/verifyReceipt) para teste.   
   
 A resposta JSON conterá as seguintes chaves:

```csharp
{
   "status" : 0,
   "receipt" : { (receipt repeated here) }
}
```

Um status de zero indica um recebimento válido. O servidor pode prosseguir para atender o conteúdo do produto comprado. A chave de confirmação contém um dicionário JSON com as mesmas propriedades que o `SKPaymentTransaction` objeto que foi recebido pelo aplicativo, para que o código do servidor pode consultar esse dicionário para recuperar informações como a quantidade de compra e product_id.

Consulte da Apple [verificando recebimentos de repositório](https://developer.apple.com/library/ios/#documentation/NetworkingInternet/Conceptual/StoreKitGuide/VerifyingStoreReceipts/VerifyingStoreReceipts.html#//apple_ref/doc/uid/TP40008267-CH104-SW1) documentação para obter informações adicionais.

#### <a name="using-aspnet"></a>Usando o ASP.NET

Para desenvolvedores do c#, há um projeto de código-fonte aberto útil chamado [APNS-Sharp](https://github.com/Redth/APNS-Sharp) que inclui o código de verificação de recebimento que funciona no ASP.NET. Em particular, o `Receipt.cs` e `ReceiptVerification.cs` arquivos de [ `Jdsoft.Apple.AppStore` ](https://github.com/Redth/APNS-Sharp/tree/master/JdSoft.Apple.AppStore) diretório pode ser adicionado a um site do .NET para implementar facilmente etapas #6 a 8 # do diagrama de fluxo de trabalho Server-Delivered produtos.   
   
   
   
 Comunicação com os servidores do iTunes usa JSON, que é fácil processar em c#.   
   
   
   
 Consulte as compras no aplicativo da Apple [validação de recebimento](https://developer.apple.com/library/ios/#releasenotes/StoreKit/IAP_ReceiptValidation/_index.html) documentação para obter informações detalhadas sobre como verificar se uma confirmação é válida.

### <a name="3rd-party-receipt-verification"></a>Verificação de recebimento parte 3

Há empresas que oferecem plataformas para verificação de recebimento (e outras coisas) que você pode usar em vez de criar seu próprio servidor. Xamarin não endossa esses serviços; eles são simplesmente mencionados aqui para referência.

#### <a name="urban-airship"></a>Airship Urbano

Airship Urbano fornece um número de diferentes serviços de back-end para aplicativos iOS incluindo as notificações de verificação e envio de confirmação.   
   
 [http://urbanairship.com/products/in-app-purchase/](http://urbanairship.com/products/in-app-purchase/)



