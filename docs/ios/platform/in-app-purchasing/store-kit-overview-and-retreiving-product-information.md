---
title: Visão geral de StoreKit e ao recuperar informações do produto no xamarin. IOS
description: Este documento fornece uma visão geral de StoreKit. Ele descreve as classes usadas com StoreKit, testar StoreKit interações, exibindo seus produtos para venda, produtos inválidos de manipulação e exibir os preços localizados.
ms.prod: xamarin
ms.assetid: FC21192E-6325-4389-C060-E92DBB5EBD87
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/18/2017
ms.openlocfilehash: 0dcda2e4fd1ca7773668a0a6fdf46e01f2f0841d
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61366880"
---
# <a name="storekit-overview-and-retrieving-product-info-in-xamarinios"></a>Visão geral de StoreKit e ao recuperar informações do produto no xamarin. IOS

A interface do usuário para uma compra no aplicativo é mostrada nas capturas de tela abaixo.
Antes que ocorra qualquer transação, o aplicativo deve recuperar preço do produto e a descrição para a exibição. Em seguida, quando o usuário pressiona **comprar**, o aplicativo faz uma solicitação no storekit que gerencia o diálogo de confirmação e o logon de ID da Apple. Supondo que a transação for bem-sucedida, StoreKit notifica o código do aplicativo, que deve armazenar o resultado da transação e fornecer ao usuário acesso a sua compra.   

   
 [![](store-kit-overview-and-retreiving-product-information-images/image14.png "StoreKit notifica o código do aplicativo, que deve armazenar o resultado da transação e fornecer ao usuário acesso à sua compra")](store-kit-overview-and-retreiving-product-information-images/image14.png#lightbox)

## <a name="classes"></a>Classes

A implementação de compras no aplicativo requer as seguintes classes do framework StoreKit:   
   
 **SKProductsRequest** – uma solicitação no storekit de produtos aprovados vender (App Store). Pode ser configurado com um número de Ids de produtos.

-   **SKProductsRequestDelegate** – declara os métodos para manipular solicitações de produto e as respostas. 
-   **SKProductsResponse** – enviadas de volta para o delegado de StoreKit (App Store). Contém o SKProducts que correspondem ao produto Ids enviadas com a solicitação. 
-   **SKProduct** – recuperados de um produto de StoreKit (que você configurou no iTunes Connect). Contém informações sobre o produto, como ID do produto, título, descrição e preço. 
-   **SKPayment** – criado com uma ID de produto e adicionados à fila de pagamento para realizar uma compra. 
-   **SKPaymentQueue** – solicitações de pagamento na fila para ser enviada à Apple. As notificações são disparadas como resultado de cada pagamento que está sendo processado. 
-   **SKPaymentTransaction** – representa uma transação concluída (uma solicitação de compra que foi processada pelo Store do aplicativo e enviada de volta ao seu aplicativo por meio de StoreKit). A transação pode ser comprado, restaurada ou falha. 
-   **SKPaymentTransactionObserver** – subclasse personalizada que responde a eventos gerados pela fila StoreKit pagamento. 
-   **As operações no StoreKit são assíncronas** – depois de um SKProductRequest é iniciado ou um SKPayment é adicionada à fila, controle é retornado ao seu código. StoreKit irá chamar métodos em sua subclasse SKProductsRequestDelegate ou SKPaymentTransactionObserver quando ele recebe dados de servidores da Apple. 


O diagrama a seguir mostra as relações entre as várias classes de StoreKit (classes abstratas devem ser implementadas em seu aplicativo):   
   
   
   
 [![](store-kit-overview-and-retreiving-product-information-images/image15.png "As relações entre as várias classes abstratas de classes de StoreKit devem ser implementadas no aplicativo")](store-kit-overview-and-retreiving-product-information-images/image15.png#lightbox)   
   
   
   
 Essas classes são explicadas em mais detalhes posteriormente neste documento.

## <a name="testing"></a>Testes

A maioria das operações no StoreKit exigem um dispositivo real para teste. Recuperando informações do produto (ie. preço &amp; descrição) funcionarão no simulador, mas compra e operações de restauração serão retornado um erro (como o código de FailedTransaction = 5002 Ocorreu um erro desconhecido).

Observação: Não funciona no StoreKit no iOS Simulator. Ao executar seu aplicativo no simulador de iOS, StoreKit registra um aviso se seu aplicativo tenta recuperar a fila de pagamento. Teste o armazenamento deve ser feito em dispositivos reais.   
   
   
   
 Importante: Não entre com sua conta de teste no aplicativo configurações. Você pode usar o aplicativo de configurações para o sinal de fora de qualquer conta existente do ID da Apple e, em seguida, você deve esperar para ser solicitado *dentro de sua sequência de compra no aplicativo* para fazer logon usando um teste de ID da Apple.   
   
   
   

Se você tentar entrar para o armazenamento real com uma conta de teste, ele será automaticamente convertido para uma ID da Apple real. Essa conta não poderá ser usada para teste.

Para testar código no StoreKit, você deve faça logoff da sua conta de teste regulares do iTunes e faça logon com uma conta de teste especial (criada no iTunes Connect) que esteja vinculada ao armazenamento de teste. Sair a conta atual visita **Configurações > iTunes e o aplicativo Store** conforme mostrado aqui:

 [![](store-kit-overview-and-retreiving-product-information-images/image16.png "Para assinar fora da conta atual visite configurações iTunes e App Store")](store-kit-overview-and-retreiving-product-information-images/image16.png#lightbox)
 
em seguida, entrar com uma conta de teste *quando solicitado por StoreKit dentro de seu aplicativo*:



Para criar usuários de teste no iTunes Connect, clique no **usuários e funções** na página principal.

 [![](store-kit-overview-and-retreiving-product-information-images/image17.png "Para criar usuários de teste no iTunes Connect clique em usuários e funções na página principal")](store-kit-overview-and-retreiving-product-information-images/image17.png#lightbox)

Selecione **testadores de área restrita**

 [![](store-kit-overview-and-retreiving-product-information-images/image18.png "Selecionar testadores de área restrita")](store-kit-overview-and-retreiving-product-information-images/image18.png#lightbox)

A lista de usuários existentes é exibida. Você pode adicionar um novo usuário ou excluir um registro existente. O portal não (atualmente) permite exibir ou editar existentes de usuários de teste, portanto, é recomendável que você mantenha um registro boa de cada usuário de teste é criado (especialmente a senha que você atribua). Depois de excluir um usuário de teste o endereço de email não pode ser reutilizado para outra conta de teste.  
   
 [![](store-kit-overview-and-retreiving-product-information-images/image19.png "É exibida a lista de usuários existentes")](store-kit-overview-and-retreiving-product-information-images/image19.png#lightbox)   
   
 Novos usuários de teste com atributos semelhantes a uma ID Apple real (como nome, senha, pergunta secreta e resposta). Manter um registro de todos os detalhes inseridos aqui. O **Select iTunes Store** campo determinará quais moedas e idiomas o compras no aplicativo usará quando entrar como esse usuário.

 [![](store-kit-overview-and-retreiving-product-information-images/image20.png "O campo Select iTunes Store determinará a moeda e o idioma para suas compras no aplicativo do usuário")](store-kit-overview-and-retreiving-product-information-images/image20.png#lightbox)

## <a name="retrieving-product-information"></a>Recuperando informações de produto

A primeira etapa na venda de um produto de compra no aplicativo é exibi-lo: recuperando o preço atual e a descrição de Store de aplicativo para exibição.   
   
   
   
 Independentemente de qual tipo de produtos de um aplicativo vende (consumíveis, não consumível ou um tipo de assinatura), o processo de recuperar informações do produto para exibição é o mesmo. O código InAppPurchaseSample que acompanha este artigo contém um projeto chamado *consumíveis* que demonstra como recuperar informações de produção para exibição. Ele mostra como:

-  Criar uma implementação do `SKProductsRequestDelegate` e implemente o `ReceivedResponse` método abstrato. O exemplo de código chama o `InAppPurchaseManager` classe. 
-  Entre em contato com StoreKit para ver se os pagamentos são permitidos (usando `SKPaymentQueue.CanMakePayments` ). 
-  Criar uma instância de um `SKProductsRequest` com as Ids do produto que foram definidos no iTunes Connect. Isso é feito no exemplo a `InAppPurchaseManager.RequestProductData` método. 
-  Chamar o método Start no `SKProductsRequest` . Isso dispara uma chamada assíncrona para os servidores do App Store. O delegado ( `InAppPurchaseManager` ) será o retorno de chamada com os resultados. 
-  O delegado ( `InAppPurchaseManager` ) `ReceivedResponse` método atualiza a interface do usuário com os dados retornados de Store de aplicativo (os preços dos produtos e descrições ou mensagens sobre produtos inválidos). 

A interação geral tem esta aparência ( **StoreKit** é interno para iOS e o **App Store** representa os servidores da Apple):

 [![](store-kit-overview-and-retreiving-product-information-images/image21.png "Recuperando o grafo de informações do produto")](store-kit-overview-and-retreiving-product-information-images/image21.png#lightbox)

### <a name="displaying-product-information-example"></a>Exibir exemplo de informações do produto

O [InAppPurchaseSample](https://developer.xamarin.com/samples/monotouch/StoreKit/) *consumíveis* código de exemplo demonstra como é possível recuperar informações do produto. A tela principal do exemplo exibe informações para dois produtos que são recuperadas de Store de aplicativo:   
   
   
   
 [![](store-kit-overview-and-retreiving-product-information-images/image23.png "A tela principal exibe produtos de informações recuperados do Store do aplicativo")](store-kit-overview-and-retreiving-product-information-images/image23.png#lightbox)   
   
   
   
 O código de exemplo para recuperar e exibir informações sobre o produto é explicado mais detalhadamente abaixo.


#### <a name="viewcontroller-methods"></a>Métodos de ViewController

O `ConsumableViewController` classe irá gerenciar a exibição de preços dos dois produtos cujos IDs de produto são codificadas na classe.

```csharp
public static string Buy5ProductId = "com.xamarin.storekit.testing.consume5credits",
   Buy10ProductId = "com.xamarin.storekit.testing.consume10credits";
List<string> products;
InAppPurchaseManager iap;
public ConsumableViewController () : base()
{
   // two products for sale on this page
   products = new List<string>() {Buy5ProductId, Buy10ProductId};
   iap = new InAppPurchaseManager();
}
```

A classe nível também deverá haver um NSObject declarado que será usado para configurar um `NSNotificationCenter` observador:

```csharp
NSObject priceObserver;
```

No método ViewWillAppear o observador é criado e atribuído usando o Centro de notificação padrão:

```csharp
priceObserver = NSNotificationCenter.DefaultCenter.AddObserver (
  InAppPurchaseManager.InAppPurchaseManagerProductsFetchedNotification,
(notification) => {
   // display code goes here, to handle the response from the App Store
}
```

   
   
 No final o `ViewWillAppear` método, chame o `RequestProductData` método para iniciar a solicitação StoreKit. Depois que essa solicitação foi feita, StoreKit assincronamente contatar servidores da Apple para obter as informações e alimentá-lo de volta para seu aplicativo. Isso é feito com o `SKProductsRequestDelegate` subclasse ( `InAppPurchaseManager`) que é explicado na próxima seção.

```csharp
iap.RequestProductData(products);
```

O código para exibir o preço e a descrição simplesmente recupera as informações da SKProduct e atribui a controles UIKit (Observe que podemos exibir o `LocalizedTitle` e `LocalizedDescription` – StoreKit resolve automaticamente o texto correto e os preços com base em configurações da conta do usuário). O código a seguir pertence na notificação que criamos acima:

```csharp
priceObserver = NSNotificationCenter.DefaultCenter.AddObserver (
  InAppPurchaseManager.InAppPurchaseManagerProductsFetchedNotification,
(notification) => {
   // display code goes here, to handle the response from the App Store
   var info = notification.UserInfo;
   if (info.ContainsKey(NSBuy5ProductId)) {
       var product = (SKProduct) info.ObjectForKey(NSBuy5ProductId);
       buy5Button.Enabled = true;
       buy5Title.Text = product.LocalizedTitle;
       buy5Description.Text = product.LocalizedDescription;
       buy5Button.SetTitle("Buy " + product.Price, UIControlState.Normal); // price display should be localized
   }
}
```

Por fim, o `ViewWillDisappear` método deve garantir que o observador é removido:

```csharp
NSNotificationCenter.DefaultCenter.RemoveObserver (priceObserver);
```

#### <a name="skproductrequestdelegate-inapppurchasemanager-methods"></a>Métodos de SKProductRequestDelegate (InAppPurchaseManager)

O `RequestProductData` método é chamado quando o aplicativo quiser recuperar os preços dos produtos e outras informações. Ele analisa a coleção de Ids de produto no tipo de dados correto, em seguida, cria um `SKProductsRequest` com essas informações. Chamar o método Start faz com que uma solicitação de rede a serem feitas nos servidores da Apple. A solicitação será executado de forma assíncrona e chamar o `ReceivedResponse` método do delegado quando ele for concluído com êxito.

```csharp
public void RequestProductData (List<string> productIds)
{
   var array = new NSString[productIds.Count];
   for (var i = 0; i < productIds.Count; i++) {
       array[i] = new NSString(productIds[i]);
   }
   NSSet productIdentifiers = NSSet.MakeNSObjectSet<NSString>(array);
   productsRequest = new SKProductsRequest(productIdentifiers);
   productsRequest.Delegate = this; // for SKProductsRequestDelegate.ReceivedResponse
   productsRequest.Start();
}
```

iOS será automaticamente rotear a solicitação para a versão 'área restrita' ou 'produção' do Store do aplicativo, dependendo de qual perfil de provisionamento que o aplicativo é executado com – portanto, quando você estiver desenvolvendo ou Testando seu aplicativo a solicitação terá acesso a todos os produtos configurado no iTunes Connect (mesmo aqueles que ainda não foram enviadas ou aprovado pela Apple). Quando seu aplicativo está em produção, solicitações de StoreKit só retornará informações para **aprovado** produtos.   
   
   
   
 O `ReceivedResponse` método substituído é chamado depois que os servidores da Apple responderam com dados. Como isso é chamado em segundo plano, o código deve analisar os dados válidos e usar uma notificação para enviar as informações do produto para qualquer ViewControllers que são 'escutar' para essa notificação. O código para coletar informações de produto válida e enviar uma notificação é mostrado abaixo:

```csharp
public override void ReceivedResponse (SKProductsRequest request, SKProductsResponse response)
{
   SKProduct[] products = response.Products;
   NSDictionary userInfo = null;
   if (products.Length > 0) {
       NSObject[] productIdsArray = new NSObject[response.Products.Length];
       NSObject[] productsArray = new NSObject[response.Products.Length];
       for (int i = 0; i < response.Products.Length; i++) {
           productIdsArray[i] = new NSString(response.Products[i].ProductIdentifier);
           productsArray[i] = response.Products[i];
       }
       userInfo = NSDictionary.FromObjectsAndKeys (productsArray, productIdsArray);
   }
   NSNotificationCenter.DefaultCenter.PostNotificationName (InAppPurchaseManagerProductsFetchedNotification, this, userInfo);
}
```

Embora não mostrado no diagrama, o `RequestFailed` método também deve ser substituído para que você possa fornecer alguns comentários ao usuário no caso de servidores do App Store não estiverem acessíveis (ou algum outro erro ocorre). O exemplo de código simplesmente grava no console, mas um aplicativo real pode optar por consultar para `error.Code` propriedade e implementar comportamento personalizado (por exemplo, um alerta para o usuário).

```csharp
public override void RequestFailed (SKRequest request, NSError error)
{
   Console.WriteLine (" ** InAppPurchaseManager RequestFailed() " + error.LocalizedDescription);
}
```

Esta captura de tela mostra o aplicativo de exemplo imediatamente após o carregamento (quando não há informações sobre o produto está disponível):

 [![](store-kit-overview-and-retreiving-product-information-images/image24.png "O aplicativo de exemplo imediatamente após o carregamento quando nenhuma informação do produto está disponível")](store-kit-overview-and-retreiving-product-information-images/image24.png#lightbox)

## <a name="invalid-products"></a>Produtos inválidos

Um `SKProductsRequest` também podem retornar uma lista de IDs de produto inválido. Produtos inválidos geralmente são retornados devido a um dos seguintes:   
   
   
   
 **ID do produto tiver sido digitada incorretamente** – somente IDs de produto válidas são aceitos.   
   
 **Produto não foi aprovado** – ao testar, todos os produtos que estão desmarcados para venda devem ser retornados por um `SKProductsRequest`; no entanto, em produção, apenas os produtos aprovados são retornados.   
   
 **ID do aplicativo não é explícita** – Wildcard App IDs (com um asterisco) não permitem compras no aplicativo.   
   
 **Incorreta de perfil de provisionamento** – se você fizer alterações à sua configuração de aplicativo no portal de provisionamento (como habilitar compras no aplicativo), lembre-se gerar novamente e usar o perfil de provisionamento correto ao compilar o aplicativo.   
   
 **contrato de aplicativos pagos do iOS não está em vigor** – StoreKit recursos não funcionarão em todos os menos que haja um contrato válido para sua conta de desenvolvedor da Apple.   
   
 **O binário está no estado rejeitado** – se não houver um binário enviado anteriormente no estado rejeitado (ou pela equipe do App Store ou pelo desenvolvedor), em seguida, no StoreKit recursos não funcionarão.

O `ReceivedResponse` método no código de exemplo gera os produtos inválidos no console:

```csharp
public override void ReceivedResponse (SKProductsRequest request, SKProductsResponse response)
{
   // code removed for clarity
   foreach (string invalidProductId in response.InvalidProducts) {
       Console.WriteLine("Invalid product id: " + invalidProductId );
   }
}
```

## <a name="displaying-localized-prices"></a>Exibir preços localizados

As camadas de preço especificam um preço específico para cada produto em todas as lojas de aplicativos internacionais. Para garantir que os preços são exibidos corretamente para cada moeda, use o seguinte método de extensão (definido em `SKProductExtension.cs`) em vez da propriedade de preço de cada `SKProduct`:

```csharp
public static class SKProductExtension {
   public static string LocalizedPrice (this SKProduct product)
   {
       var formatter = new NSNumberFormatter ();
       formatter.FormatterBehavior = NSNumberFormatterBehavior.Version_10_4;  
       formatter.NumberStyle = NSNumberFormatterStyle.Currency;
       formatter.Locale = product.PriceLocale;
       var formattedString = formatter.StringFromNumber(product.Price);
       return formattedString;
   }
}
```

O código que define o título do botão usa o método de extensão como este:

```csharp
string Buy = "Buy {0}"; // or a localizable string
buy5Button.SetTitle(String.Format(Buy, product.LocalizedPrice()), UIControlState.Normal);
```

Usando duas contas de teste diferentes do iTunes (uma para o repositório de American) e outro para o repositório de japonês resulta em capturas de tela as seguir:   
   
   
   
 [![](store-kit-overview-and-retreiving-product-information-images/image25.png "Mostrando resultados específicos de idioma de contas de teste de dois iTunes diferentes")](store-kit-overview-and-retreiving-product-information-images/image25.png#lightbox)   
   
   
   
 Observe que o armazenamento afeta o idioma que é usado para a moeda de informações e o preço do produto, enquanto a configuração de idioma do dispositivo afeta rótulos e outros tipos de conteúdo localizado.   
   
   
   
 Lembre-se de que usar um repositório diferente de teste conta, você deve **sair** na **Configurações > iTunes e o aplicativo Store** e inicie novamente o aplicativo para entrar com uma conta diferente. Para alterar idioma do dispositivo, acesse **Configurações > Geral > internacional > linguagem**.

