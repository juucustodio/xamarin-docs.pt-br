---
title: Transações e verificação no Xamarin. iOS
description: Este documento descreve como permitir a restauração de compras passadas em um aplicativo Xamarin. iOS. Ele também aborda maneiras de proteger as compras e os produtos entregues pelo servidor.
ms.prod: xamarin
ms.assetid: 84EDD2B9-3FAA-B3C7-F5E8-C1E5645B7C77
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/18/2017
ms.openlocfilehash: 605f82c90f98bb4b50e5b630a53721d186ff35a1
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/22/2020
ms.locfileid: "86935753"
---
# <a name="transactions-and-verification-in-xamarinios"></a>Transações e verificação no Xamarin. iOS

## <a name="restoring-past-transactions"></a>Restaurando transações anteriores

Se seu aplicativo oferece suporte a tipos de produtos que podem ser restaurados, você deve incluir alguns elementos da interface do usuário para permitir que os usuários restaurem essas compras.
Essa funcionalidade permite que um cliente adicione o produto a dispositivos adicionais ou restaure o produto para o mesmo dispositivo após a limpeza ou remoção e reinstalação do aplicativo. Os seguintes tipos de produto são restauráveis:

- Produtos não consumíveis
- Assinaturas renováveis automaticamente
- Assinaturas gratuitas

O processo de restauração deve atualizar os registros que você mantém no dispositivo para atender aos seus produtos. O cliente pode optar por restaurar a qualquer momento, em qualquer um de seus dispositivos. O processo de restauração envia novamente todas as transações anteriores para esse usuário; o código do aplicativo deve determinar a ação a ser tomada com essas informações (por exemplo, verificar se já existe um registro dessa compra no dispositivo e, se não estiver, criando um registro da compra e habilitando o produto para o usuário).

### <a name="implementing-restore"></a>Implementando a restauração

O botão de **restauração** da interface do usuário chama o método a seguir, que dispara RestoreCompletedTransactions no `SKPaymentQueue` .

```csharp
public void Restore()
{
   // theObserver will be notified of when the restored transactions start arriving <- AppStore
   SKPaymentQueue.DefaultQueue.RestoreCompletedTransactions();
}
```

O StoreKit enviará a solicitação de restauração para os servidores da Apple de forma assíncrona.   
   
Como o `CustomPaymentObserver` é registrado como um observador de transação, ele receberá mensagens quando os servidores da Apple responderem. A resposta conterá todas as transações que esse usuário já realizou neste aplicativo (em todos os seus dispositivos). O código percorre cada transação, detecta o estado restaurado e chama o `UpdatedTransactions` método para processá-lo, conforme mostrado abaixo:

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

Se não houver nenhum produto restaurável para o usuário, o `UpdatedTransactions` não será chamado.   
   
O código mais simples possível para restaurar uma determinada transação no exemplo faz as mesmas ações que a de uma compra ocorre, exceto que a `OriginalTransaction` propriedade é usada para acessar a ID do produto:

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

Uma implementação mais sofisticada pode verificar outras `transaction.OriginalTransaction` Propriedades, como a data e o número de recebimento originais. Essas informações serão úteis para alguns tipos de produtos (como assinaturas).

#### <a name="restore-completion"></a>Conclusão da restauração

O `CustomPaymentObserver` tem dois métodos adicionais que serão chamados por StoreKit quando o processo de restauração for concluído (com êxito ou com uma falha), mostrado abaixo:

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

No exemplo, esses métodos não fazem nada, no entanto, um aplicativo real pode optar por implementar uma mensagem para o usuário ou alguma outra funcionalidade.

## <a name="securing-purchases"></a>Protegendo compras

Os dois exemplos neste documento usam `NSUserDefaults` para controlar as compras:   
   
 **Itens consumíveis** – o "saldo" de compras de crédito é um `NSUserDefaults` valor inteiro simples que é incrementado com cada compra.   
   
 **Não itens consumíveis** – cada compra de filtro de fotos é armazenada como um par chave-valor no `NSUserDefaults` .

`NSUserDefaults`O uso do mantém o código de exemplo simples, mas não oferece uma solução muito segura, pois pode ser possível que usuários de sua opinião atualizem as configurações (ignorando o mecanismo de pagamento).   
   
Observação: os aplicativos reais devem adotar um mecanismo seguro para armazenar o conteúdo adquirido que não está sujeito à violação do usuário. Isso pode envolver a criptografia e/ou outras técnicas, incluindo a autenticação de servidor remoto.   
   
 O mecanismo também deve ser projetado para aproveitar os recursos internos de backup e recuperação do iOS, do iTunes e do iCloud. Isso garantirá que, depois que um usuário restaurar um backup, suas compras anteriores estarão imediatamente disponíveis.   
   
Consulte o guia de codificação seguro da Apple para obter mais diretrizes específicas do iOS.

## <a name="receipt-verification-and-server-delivered-products"></a>Verificação de recebimento e produtos entregues pelo servidor

Os exemplos neste documento até agora já consistiram apenas no aplicativo que se comunica diretamente com os servidores da loja de aplicativos para realizar transações de compra, que desbloqueiam recursos ou funcionalidades já codificados no aplicativo.   
   
A Apple fornece um nível adicional de segurança de compra, permitindo que as confirmações de compra sejam verificadas de forma independente por outro servidor, o que pode ser útil para validar uma solicitação antes de fornecer conteúdo digital como parte de uma compra (como uma revista ou um livro digital).   
   
 **Produtos internos** – como os exemplos neste documento, o produto que está sendo adquirido existe como uma funcionalidade fornecida com o aplicativo. Uma compra no aplicativo permite que o usuário acesse a funcionalidade.
As IDs de produto são codificadas.   
   
 **Produtos entregues pelo servidor** – o produto consiste em conteúdo para download que é armazenado em um servidor remoto até que uma transação bem-sucedida faça com que o conteúdo seja baixado.
Exemplos podem incluir problemas de livros ou da revista. As IDs de produto geralmente são originadas de um servidor externo (onde o conteúdo do produto também é hospedado). Os aplicativos devem implementar uma maneira robusta de gravar quando uma transação for concluída, de modo que, se o download do conteúdo falhar, ele poderá ser tentado novamente sem confundir o usuário.

### <a name="server-delivered-products"></a>Produtos entregues pelo servidor

O conteúdo de um produto, como livros e revistas (ou até mesmo um nível de jogo), precisa ser baixado de um servidor remoto durante o processo de compra. Isso significa que um servidor adicional é necessário para armazenar e entregar o conteúdo do produto após sua compra.

#### <a name="getting-prices-for-server-delivered-products"></a>Obtendo preços para produtos entregues pelo servidor

Como os produtos são entregues remotamente, também é possível adicionar mais produtos ao longo do tempo (sem Atualizar o código do aplicativo), como adicionar mais livros ou novos problemas de uma revista. Para que o aplicativo possa descobrir esses produtos de notícias e exibi-los para o usuário, o servidor adicional deve armazenar e fornecer essas informações.   
   
[![Obtendo preços para produtos entregues pelo servidor](transactions-and-verification-images/image38.png)](transactions-and-verification-images/image38.png#lightbox)   
   
1. As informações do produto devem ser armazenadas em vários locais: no servidor e no iTunes Connect. Além disso, cada produto terá arquivos de conteúdo associados a ele. Esses arquivos serão entregues após uma compra bem-sucedida.   
   
2. Quando o usuário deseja comprar um produto, o aplicativo deve determinar quais produtos estão disponíveis. Essas informações podem ser armazenadas em cache, mas devem ser entregues de um servidor remoto onde a lista mestra de produtos é armazenada.   
   
3. O servidor retorna uma lista de IDs de produto para o aplicativo a ser analisado.   
   
4. Em seguida, o aplicativo determina quais dessas IDs de produto enviar ao StoreKit para recuperar preços e descrições.   
   
5. O StoreKit envia a lista de IDs de produto para os servidores da Apple.   
   
6. Os servidores iTunes respondem com informações válidas do produto (descrição e preço atual).   
   
7. O aplicativo `SKProductsRequestDelegate` recebe as informações do produto para exibição para o usuário.

#### <a name="purchasing-server-delivered-products"></a>Comprando produtos entregues pelo servidor

Como o servidor remoto requer alguma maneira de validar que uma solicitação de conteúdo é válida (ou seja, paga), as informações de recebimento são passadas para autenticação. O servidor remoto encaminha esses dados para o iTunes para verificação e, se bem-sucedido, inclui o conteúdo do produto na resposta ao aplicativo.   
   
 [![Comprando produtos entregues pelo servidor](transactions-and-verification-images/image39.png)](transactions-and-verification-images/image39.png#lightbox)   
   
1. O aplicativo adiciona um `SKPayment` à fila. Se necessário, o usuário receberá uma solicitação para sua ID da Apple e será solicitado a confirmar o pagamento.   
   
2. StoreKit envia a solicitação ao servidor para processamento.   
   
3. Quando a transação for concluída, o servidor responderá com um recibo de transação.   
   
4. A `SKPaymentTransactionObserver` subclasse recebe o recibo e o processa. Como o produto deve ser baixado de um servidor, o aplicativo inicia uma solicitação de rede para o servidor remoto.   
   
5. A solicitação de download é acompanhada pelos dados de recebimento para que o servidor remoto possa verificar se ele está autorizado a acessar o conteúdo. O cliente de rede do aplicativo aguarda uma resposta a essa solicitação.   
   
6. Quando o servidor recebe uma solicitação de conteúdo, ele analisa os dados de recebimento e envia uma solicitação diretamente aos servidores iTunes para verificar se a confirmação é para uma transação válida. O servidor deve usar alguma lógica para determinar se deve enviar a solicitação para a URL de produção ou área restrita. A Apple sugere sempre usar a URL de produção e alternar para a área restrita se o status de recebimento 21007 (recebimento de área restrita enviado ao servidor de produção). Consulte o guia de [programação de validação de recibo](https://developer.apple.com/library/archive/releasenotes/General/ValidateAppStoreReceipt/Chapters/ValidateRemotely.html) da Apple para obter mais detalhes.
   
7. o iTunes verificará o recebimento e retornará um status de zero se ele for válido.   
   
8. O servidor aguarda a resposta do iTunes. Se receber uma resposta válida, o código deverá localizar o arquivo de conteúdo do produto associado para incluir na resposta ao aplicativo.   
  
9. O aplicativo recebe e analisa a resposta, salvando o conteúdo do produto no sistema de arquivos do dispositivo.   
   
10. O aplicativo habilita o produto e, em seguida, chama o StoreKit `FinishTransaction` . O aplicativo pode, opcionalmente, exibir o conteúdo adquirido (por exemplo, mostrar a primeira página de um problema de livro comprado ou revista).

Uma implementação alternativa para arquivos de conteúdo de produtos muito grandes poderia envolver simplesmente armazenar o recebimento de transações na etapa #9 para que a transação possa ser rapidamente concluída e fornecer uma interface de usuário para que o usuário Baixe o conteúdo real do produto em algum momento posterior. A solicitação de download subsequente pode enviar novamente o recebimento armazenado para acessar o arquivo de conteúdo do produto necessário.

### <a name="writing-server-side-receipt-verification-code"></a>Gravando código de verificação de recebimento no lado do servidor

A validação de um recebimento no código do servidor pode ser feita com uma solicitação/resposta HTTP POST simples que abrange as etapas #5 por meio de #8 no diagrama de fluxo de trabalho.   
   
Extraia a `SKPaymentTansaction.TransactionReceipt` propriedade no aplicativo. Estes são os dados que precisam ser enviados ao iTunes para verificação (etapa #5).

Base64-codificar os dados de confirmação de transação (na etapa #5 ou #6).

Crie um conteúdo JSON simples como este:

```csharp
{
   "receipt-data" : "(base-64 encoded receipt here)"
}
```

HTTP poste o JSON para [https://buy.itunes.apple.com/verifyReceipt](https://buy.itunes.apple.com/verifyReceipt) para produção ou [https://sandbox.itunes.apple.com/verifyReceipt](https://sandbox.itunes.apple.com/verifyReceipt) para teste.   
   
 A resposta JSON conterá as seguintes chaves:

```csharp
{
   "status" : 0,
   "receipt" : { (receipt repeated here) }
}
```

Um status de zero indica um recebimento válido. O servidor pode continuar a preencher o conteúdo do produto comprado. A chave de recebimento contém um dicionário JSON com as mesmas propriedades que o `SKPaymentTransaction` objeto recebido pelo aplicativo, portanto, o código do servidor pode consultar esse dicionário para recuperar informações como a product_id e a quantidade da compra.

Consulte a documentação do [Guia de programação de validação de recibo](https://developer.apple.com/library/archive/releasenotes/General/ValidateAppStoreReceipt/Introduction.html) da Apple para obter informações adicionais.
