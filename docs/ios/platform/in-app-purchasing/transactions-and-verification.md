---
title: Transações e verificação no xamarin. IOS
description: Este documento descreve como permitir a restauração das compras passadas em um aplicativo xamarin. IOS. Ele também discute as maneiras de proteger as compras e entregue ao servidor de produtos.
ms.prod: xamarin
ms.assetid: 84EDD2B9-3FAA-B3C7-F5E8-C1E5645B7C77
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/18/2017
ms.openlocfilehash: 83f5fd233c004271169a4d00d0a65e70aa925b95
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61369079"
---
# <a name="transactions-and-verification-in-xamarinios"></a>Transações e verificação no xamarin. IOS

## <a name="restoring-past-transactions"></a>Restaurando após transações

Se seu aplicativo dá suporte a tipos de produtos que são restauráveis, você deve incluir alguns elementos de interface do usuário para permitir que os usuários restaurar essas compras.
Essa funcionalidade permite que um cliente para adicionar o produto para dispositivos adicionais ou para restaurar o produto para o mesmo dispositivo após a que está sendo limpo ou remover e reinstalar o aplicativo. Os seguintes tipos de produto são restauráveis:

-  Produtos não consumíveis
-  Pode ser renovado automaticamente assinaturas
-  Assinaturas gratuitas

O processo de restauração deve atualizar os registros de manter no dispositivo para atender a seus produtos. O cliente pode optar por restaurar a qualquer momento, em qualquer um dos seus dispositivos. O processo de restauração novamente envia todas as transações anteriores para que o usuário; o código do aplicativo deve determinar qual ação tomar com essas informações (por exemplo, verificando se já houver um registro de aquisição no dispositivo e se não, criando um registro da compra e habilitando o produto para o usuário).

### <a name="implementing-restore"></a>Implementação de restauração

A interface do usuário **restaurar** botão chama o método seguinte, o que dispara RestoreCompletedTransactions no `SKPaymentQueue`.

```csharp
public void Restore()
{
   // theObserver will be notified of when the restored transactions start arriving <- AppStore
   SKPaymentQueue.DefaultQueue.RestoreCompletedTransactions();
}
```

StoreKit enviará a solicitação de restauração para servidores da Apple assincronamente.   
   
Porque o `CustomPaymentObserver` está registrado como um observador de transação, ele receberá mensagens quando os servidores da Apple respondem. A resposta irá conter todas as transações que esse usuário efetuou neste aplicativo (em todos os dispositivos). O código executa um loop por meio de cada transação, detecta o estado restaurado e chama o `UpdatedTransactions` método processá-la, conforme mostrado abaixo:

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

Se não houver nenhum produto restaurável para o usuário `UpdatedTransactions` não é chamado.   
   
O código mais simples possível para restaurar uma determinada transação no exemplo faz as mesmas ações quando uma compra entrará em vigor, exceto que o `OriginalTransaction` propriedade é usada para acessar a ID do produto:

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

Uma implementação mais sofisticada pode verificar outros `transaction.OriginalTransaction` propriedades, como o número original de data e de recebimento. Essa informação será útil para alguns tipos de produto (como as assinaturas).

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

No exemplo a esses métodos não fazem nada, no entanto, um aplicativo real pode optar por implementar uma mensagem para o usuário ou alguma outra funcionalidade.

## <a name="securing-purchases"></a>Protegendo as compras

Os dois exemplos neste documentam usam `NSUserDefaults` para rastrear compras:   
   
 **Consumíveis** – o 'saldo' de compras de crédito é um simples `NSUserDefaults` valor inteiro que é incrementado a cada compra.   
   
 **Não consumíveis** – cada compra de filtro de foto é armazenada como um par chave-valor no `NSUserDefaults`.

Usando `NSUserDefaults` mantém o código de exemplo simples, mas não oferece uma solução muito segura, pois ele pode ser possível tecnicamente tendo em mente que os usuários atualizar as configurações (ignorando o mecanismo de pagamento).   
   
Observação: Aplicativos reais devem adotar um mecanismo seguro para armazenar comprado o conteúdo que não está sujeita à violação do usuário. Isso pode envolver a criptografia de e/ou outras técnicas, incluindo a autenticação de servidor remoto.   
   
 O mecanismo também deve ser projetado para tirar proveito dos recursos internos de backup e recuperação do iOS, iTunes e iCloud. Isso garantirá que, depois que um usuário restaura um backup de suas compras anteriores estará disponíveis imediatamente.   
   
Consulte Secure codificação guia da Apple para obter mais diretrizes específicas do iOS.

## <a name="receipt-verification-and-server-delivered-products"></a>Verificação de recebimento e entregue ao servidor de produtos

Os exemplos neste documento até o momento consistiram exclusivamente o aplicativo se comunique diretamente com os servidores do App Store para realizar transações de compra, que desbloquear recursos ou funcionalidades que já são codificadas no aplicativo.   
   
A Apple fornece um nível adicional de segurança de compra, permitindo que as confirmações de compra a ser verificada de forma independente por outro servidor, que pode ser útil para validar uma solicitação antes de entregar conteúdo digital como parte de uma compra (como um livro digital ou Magazine).   
   
 **Produtos internos** – como os exemplos neste documento, o produto que está sendo comprado existe como funcionalidade fornecida com o aplicativo. Uma compra no aplicativo permite que o usuário acessar a funcionalidade.
As IDs de produto são codificadas.   
   
 **Produtos de servidor-entregue** – o produto consiste em conteúdo para download que é armazenado em um servidor remoto até que uma transação bem-sucedida faz com que o conteúdo seja baixado.
Os exemplos podem incluir problemas revistos ou manuais. As IDs de produto geralmente são originadas de um servidor externo (em que o conteúdo do produto também está hospedado). Os aplicativos devem implementar uma maneira robusta de gravação quando uma transação for concluída, de modo que se a falha de download de conteúdo pode ser tentada novamente sem confundir o usuário.

### <a name="server-delivered-products"></a>Entregue ao servidor de produtos

Alguns produtos conteúdo do, como livros e revistas (ou até mesmo um nível de jogo) precisa ser baixado de um servidor remoto durante o processo de compra. Isso significa que um servidor adicional é necessário para armazenar e entregar o conteúdo do produto depois de ser comprado.

#### <a name="getting-prices-for-server-delivered-products"></a>Obter os preços para os produtos fornecidos pelo servidor

Porque os produtos sejam entregues remotamente, também é possível adicionar mais produtos ao longo do tempo (sem atualizar o código do aplicativo), como a adição de mais manuais ou novos problemas de uma revista. Para que o aplicativo possa descobrir esses produtos de notícias e exibi-los para o usuário, o servidor adicional deve armazenar e fornecer essas informações.   
   
[![](transactions-and-verification-images/image38.png "Obter os preços para os produtos fornecidos pelo servidor")](transactions-and-verification-images/image38.png#lightbox)   
   
1. Informações do produto devem ser armazenadas em vários lugares: no seu servidor e no iTunes Connect. Além disso, cada produto terá os arquivos de conteúdo associados a ele. Esses arquivos serão entregues após uma compra bem-sucedida.   
   
2. Quando o usuário deseja comprar um produto, o aplicativo deve determinar quais produtos estão disponíveis. Essas informações podem ser armazenados em cache, mas devem ser entregue de um servidor remoto em que a lista mestre de produtos é armazenada.   
   
3. O servidor retorna uma lista de IDs de produto para o aplicativo analisar.   
   
4. Em seguida, o aplicativo determina qual destas identificações de produto para enviar no storekit para recuperar os preços e descrições.   
   
5. StoreKit envia a lista de IDs de produtos para servidores da Apple.   
   
6. Os servidores do iTunes respondem com informações de produto válida (descrição e preço atual).   
   
7. O aplicativo `SKProductsRequestDelegate` é passado para exibir as informações do produto para o usuário.

#### <a name="purchasing-server-delivered-products"></a>Comprar produtos entregues pelo servidor

Como o servidor remoto requer alguma forma de validar que uma solicitação de conteúdo é válida (ie. pago para), as informações de recebimento são passadas para a autenticação. O servidor remoto encaminha esses dados para o iTunes para verificação e, se for bem-sucedida, inclui o conteúdo do produto na resposta para o aplicativo.   
   
 [![](transactions-and-verification-images/image39.png "Comprar produtos entregues pelo servidor")](transactions-and-verification-images/image39.png#lightbox)   
   
1. O aplicativo adiciona um `SKPayment` para a fila. Se necessário, o usuário será solicitado a fornecer sua ID Apple e solicitado a confirmar o pagamento.   
   
2. StoreKit envia a solicitação para o servidor para processamento.   
   
3. Quando a transação for concluída, o servidor responde com uma confirmação de transação.   
   
4. O `SKPaymentTransactionObserver` subclasse recebe o recebimento de mensagem e a processa. Como o produto deve ser baixado de um servidor, o aplicativo inicia uma solicitação de rede para o servidor remoto.   
   
5. A solicitação de download é acompanhada pelos dados de recebimento para que o servidor remoto pode verificar se que ele está autorizado a acessar o conteúdo. Cliente de rede do aplicativo aguarda uma resposta a essa solicitação.   
   
6. Quando o servidor recebe uma solicitação de conteúdo, ele analisa os dados de recebimento e envia uma solicitação diretamente para os servidores do iTunes para verificar o recebimento é para uma transação válida. O servidor deve usar alguma lógica para determinar se deve enviar a solicitação para a URL de produção ou de área restrita. A Apple sugere sempre usando a URL de produção e alternar para a área restrita se seu recebem status 21007 (recebimento de área restrita enviado ao servidor de produção). Consulte da Apple [guia de programação de validação de recibo](https://developer.apple.com/library/archive/releasenotes/General/ValidateAppStoreReceipt/Chapters/ValidateRemotely.html) para obter mais detalhes.
   
7. iTunes verificará o recebimento de mensagem e retornar um status de zero se ele é válido.   
   
8. O servidor aguarda a resposta dos iTunes. Se for recebida uma resposta válida, o código deve localizar o arquivo de conteúdo do produto associado para incluir na resposta para o aplicativo.   
  
9. O aplicativo recebe e analisa a resposta, salvando o conteúdo de produto do sistema de arquivos do dispositivo.   
   
10. O aplicativo permite que o produto e, em seguida, chama do StoreKit `FinishTransaction`. O aplicativo pode, em seguida, exibir, opcionalmente, o conteúdo comprado (por exemplo, mostrar a primeira página de um livro adquirido ou problema de revista).

Uma implementação alternativa para os arquivos de conteúdo de produtos muito grande pode envolver simplesmente armazenar o recebimento de transação na etapa 9 # para que a transação pode ser concluída rapidamente e fornecer uma interface do usuário para o usuário baixar o conteúdo real do produto em algum momento posterior. A solicitação de download subsequentes novamente pode enviar o recebimento armazenado para acessar o arquivo de conteúdo de produto necessária.

### <a name="writing-server-side-receipt-verification-code"></a>Escrever código de verificação de recebimento do lado do servidor

Validar um recebimento de mensagem no código do lado do servidor pode ser feito com uma HTTP POST solicitação/resposta simples que abrange as etapas 5 de # a #8 no diagrama de fluxo de trabalho.   
   
Extrair o `SKPaymentTansaction.TransactionReceipt` propriedade no aplicativo. Esses são os dados que precisa ser enviada para o iTunes para verificação (etapa 5 de #).

Codificar Base64 os dados de confirmação de transação (seja na etapa 5 # ou #6).

Crie uma carga JSON simple como este:

```csharp
{
   "receipt-data" : "(base-64 encoded receipt here)"
}
```

HTTP POST o JSON a ser [ https://buy.itunes.apple.com/verifyReceipt ](https://buy.itunes.apple.com/verifyReceipt) para a produção ou [ https://sandbox.itunes.apple.com/verifyReceipt ](https://sandbox.itunes.apple.com/verifyReceipt) para teste.   
   
 A resposta JSON conterá as seguintes chaves:

```csharp
{
   "status" : 0,
   "receipt" : { (receipt repeated here) }
}
```

Um status igual a zero indica um recebimento de mensagem válido. O servidor pode prosseguir para atender a conteúdo do produto comprado. A chave de recebimento de mensagem contém um dicionário em JSON com as mesmas propriedades que o `SKPaymentTransaction` objeto que foi recebido pelo aplicativo, portanto, o código do servidor pode consultar este dicionário para recuperar informações tais como o product_id e a quantidade da compra.

Consulte da Apple [guia de programação de validação de recibo](https://developer.apple.com/library/archive/releasenotes/General/ValidateAppStoreReceipt/Introduction.html) documentação para obter informações adicionais.
