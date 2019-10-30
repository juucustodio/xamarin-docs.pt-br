---
title: Visão geral do StoreKit e recuperação das informações do produto no Xamarin. iOS
description: Este documento fornece uma visão geral do StoreKit. Ele descreve as classes usadas com StoreKit, testando interações de StoreKit, exibindo produtos para venda, tratando de produtos inválidos e exibindo os preços localizados.
ms.prod: xamarin
ms.assetid: FC21192E-6325-4389-C060-E92DBB5EBD87
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/18/2017
ms.openlocfilehash: 086dcb87f03ed4abbf3b82dc10add0f5698d52b3
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73032327"
---
# <a name="storekit-overview-and-retrieving-product-info-in-xamarinios"></a>Visão geral do StoreKit e recuperação das informações do produto no Xamarin. iOS

A interface do usuário para uma compra no aplicativo é mostrada nas capturas de tela abaixo.
Antes que qualquer transação ocorra, o aplicativo deve recuperar o preço e a descrição do produto para exibição. Em seguida, quando o usuário pressiona a **compra**, o aplicativo faz uma solicitação para StoreKit que gerencia a caixa de diálogo de confirmação e o logon da Apple ID. Supondo que a transação seja bem sucedido, StoreKit notifica o código do aplicativo, que deve armazenar o resultado da transação e fornecer ao usuário acesso à sua compra.   

 [![](store-kit-overview-and-retreiving-product-information-images/image14.png "StoreKit notifies the application code, which must store the transaction result and provide the user with access to their purchase")](store-kit-overview-and-retreiving-product-information-images/image14.png#lightbox)

## <a name="classes"></a>Classes

A implementação de compras no aplicativo requer as seguintes classes da estrutura StoreKit:   
   
 **SKProductsRequest** – uma solicitação de StoreKit para produtos aprovados a serem vendidos (loja de aplicativos). Pode ser configurado com um número de IDs de produto.

- **SKProductsRequestDelegate** – declara métodos para lidar com solicitações e respostas de produtos. 
- **SKProductsResponse** – enviado de volta para o delegado de StoreKit (App Store). Contém o SKProducts que corresponde às IDs de produto enviadas com a solicitação. 
- **SKProduct** – um produto recuperado do StoreKit (que você configurou no iTunes Connect). Contém informações sobre o produto, como ID do produto, título, descrição e preço. 
- **SKPayment** – criado com uma ID de produto e adicionado à fila de pagamento para executar uma compra. 
- **SKPaymentQueue** – solicitações de pagamento enfileiradas a serem enviadas à Apple. As notificações são disparadas como resultado de cada pagamento sendo processado. 
- **SKPaymentTransaction** – representa uma transação concluída (uma solicitação de compra que foi processada pela loja de aplicativos e enviada de volta para seu aplicativo por meio de StoreKit). A transação pode ser comprada, restaurada ou falhou. 
- **SKPaymentTransactionObserver** – subclasse personalizada que responde a eventos gerados pela fila de pagamentos do StoreKit. 
- **As operações StoreKit são assíncronas** – depois que um SKProductRequest é iniciado ou um SKPayment é adicionado à fila, o controle é retornado ao seu código. StoreKit chamará métodos em sua subclasse SKProductsRequestDelegate ou SKPaymentTransactionObserver quando receber dados dos servidores da Apple. 

O diagrama a seguir mostra as relações entre as várias classes StoreKit (classes abstratas devem ser implementadas em seu aplicativo):   

 [![](store-kit-overview-and-retreiving-product-information-images/image15.png "The relationships between the various StoreKit classes abstract classes must be implemented in the app")](store-kit-overview-and-retreiving-product-information-images/image15.png#lightbox)   

Essas classes são explicadas em mais detalhes posteriormente neste documento.

## <a name="testing"></a>Testes

A maioria das operações de StoreKit requer um dispositivo real para teste. Recuperando Informações do produto (isto é, o preço &amp; descrição) funcionará no simulador, mas as operações de compra e restauração retornarão um erro (como FailedTransaction código = 5002 ocorreu um erro desconhecido).

Observação: o StoreKit não funciona no simulador do iOS. Ao executar seu aplicativo no simulador de iOS, o StoreKit registrará um aviso se seu aplicativo tentar recuperar a fila de pagamento. O teste do armazenamento deve ser feito em dispositivos reais.   

Importante: não entre com sua conta de teste no aplicativo configurações. Você pode usar o aplicativo de configurações para sair de qualquer conta existente da ID da Apple e, em seguida, aguardar a solicitação *em sua sequência de compra no aplicativo* para fazer logon usando uma ID da Apple de teste.   

Se você tentar entrar no real Store com uma conta de teste, ela será automaticamente convertida em uma ID Apple real. Essa conta não será mais útil para teste.

Para testar o código StoreKit, você deve fazer logoff de sua conta de teste do iTunes regular e logon com uma conta de teste especial (criada no iTunes Connect) que esteja vinculada ao repositório de teste. Para sair da conta atual, acesse **as configurações > iTunes e App Store** , conforme mostrado aqui:

 [![](store-kit-overview-and-retreiving-product-information-images/image16.png "To sign out of the current account visit Settings iTunes and App Store")](store-kit-overview-and-retreiving-product-information-images/image16.png#lightbox)

em seguida, entre com uma conta de teste *quando solicitado por StoreKit em seu aplicativo*:

Para criar usuários de teste no iTunes Connect, clique em **usuários e funções** na página principal.

 [![](store-kit-overview-and-retreiving-product-information-images/image17.png "To create test users in iTunes Connect click on Users and Roles on the main page")](store-kit-overview-and-retreiving-product-information-images/image17.png#lightbox)

Selecionar **testadores da área restrita**

 [![](store-kit-overview-and-retreiving-product-information-images/image18.png "Selecting Sandbox Testers")](store-kit-overview-and-retreiving-product-information-images/image18.png#lightbox)

A lista de usuários existentes é exibida. Você pode adicionar um novo usuário ou excluir um registro existente. O portal não (atualmente) permite que você exiba ou edite os usuários de teste existentes, portanto, é recomendável que você mantenha um bom registro de cada usuário de teste criado (especialmente a senha que você atribui). Depois de excluir um usuário de teste, o endereço de email não poderá ser usado novamente para outra conta de teste.  
   
 [![](store-kit-overview-and-retreiving-product-information-images/image19.png "The list of existing users is displayed")](store-kit-overview-and-retreiving-product-information-images/image19.png#lightbox)   
   
 Os novos usuários de teste têm atributos semelhantes a uma ID da Apple real (como nome, senha, pergunta secreta e resposta). Mantenha um registro de todos os detalhes inseridos aqui. O campo **selecionar repositório do iTunes** determinará a moeda e o idioma que as compras no aplicativo usarão quando estiverem conectados como esse usuário.

 [![](store-kit-overview-and-retreiving-product-information-images/image20.png "The Select iTunes Store field will determine the user's currency and language for their in-app purchases")](store-kit-overview-and-retreiving-product-information-images/image20.png#lightbox)

## <a name="retrieving-product-information"></a>Recuperando Informações do produto

A primeira etapa na venda de um produto de compra no aplicativo é exibi-lo: Recuperando o preço atual e a descrição da loja de aplicativos para exibição.   

Independentemente do tipo de produtos que um aplicativo vende (consumível, não consumível ou de um tipo de assinatura), o processo de recuperação de informações do produto para exibição é o mesmo. O código InAppPurchaseSample que acompanha este artigo contém um projeto chamado *itens consumíveis* que demonstra como recuperar informações de produção para exibição. Ele mostra como:

- Crie uma implementação de `SKProductsRequestDelegate` e implemente o método abstrato de `ReceivedResponse`. O código de exemplo chama essa classe de `InAppPurchaseManager`. 
- Verifique com StoreKit para ver se os pagamentos são permitidos (usando `SKPaymentQueue.CanMakePayments`). 
- Crie uma instância de um `SKProductsRequest` com as IDs de produto que foram definidas no iTunes Connect. Isso é feito no método de `InAppPurchaseManager.RequestProductData` do exemplo. 
- Chame o método Start no `SKProductsRequest`. Isso dispara uma chamada assíncrona para os servidores da loja de aplicativos. O delegado (`InAppPurchaseManager`) será chamado de volta com os resultados. 
- O método do delegado (`InAppPurchaseManager`) `ReceivedResponse` atualiza a interface do usuário com os dados retornados da loja de aplicativos (preços de produtos & descrições ou mensagens sobre produtos inválidos). 

A interação geral é parecida com esta (o **StoreKit** é integrado ao Ios e a **loja de aplicativos** representa os servidores da Apple):

 [![](store-kit-overview-and-retreiving-product-information-images/image21.png "Retrieving Product Information graph")](store-kit-overview-and-retreiving-product-information-images/image21.png#lightbox)

### <a name="displaying-product-information-example"></a>Exibindo o exemplo de informações do produto

O código de exemplo [InAppPurchaseSample](https://docs.microsoft.com/samples/xamarin/ios-samples/storekit) *itens consumíveis* demonstra como as informações do produto podem ser recuperadas. A tela principal do exemplo exibe informações para dois produtos que são recuperados da loja de aplicativos:   

 [![](store-kit-overview-and-retreiving-product-information-images/image23.png "The main screen displays information products  retrieved from the App Store")](store-kit-overview-and-retreiving-product-information-images/image23.png#lightbox)   

O código de exemplo para recuperar e exibir informações do produto é explicado em mais detalhes abaixo.

#### <a name="viewcontroller-methods"></a>Métodos ViewController

A classe `ConsumableViewController` gerenciará a exibição de preços para dois produtos cujas IDs de produto são codificadas na classe.

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

No nível de classe, também deve haver um NSObject declarado que será usado para configurar um observador de `NSNotificationCenter`:

```csharp
NSObject priceObserver;
```

No método ViewWillAppear, o observador é criado e atribuído usando o centro de notificações padrão:

```csharp
priceObserver = NSNotificationCenter.DefaultCenter.AddObserver (
  InAppPurchaseManager.InAppPurchaseManagerProductsFetchedNotification,
(notification) => {
   // display code goes here, to handle the response from the App Store
}
```

No final do método `ViewWillAppear`, chame o método `RequestProductData` para iniciar a solicitação StoreKit. Depois que essa solicitação for feita, o StoreKit irá contatar os servidores da Apple de forma assíncrona para obter as informações e recolocá-las em seu aplicativo. Isso é obtido pela subclasse de `SKProductsRequestDelegate` (`InAppPurchaseManager`) que é explicada na próxima seção.

```csharp
iap.RequestProductData(products);
```

O código para exibir o preço e a descrição simplesmente recupera as informações do SKProduct e as atribui aos controles UIKit (Observe que exibimos o `LocalizedTitle` e `LocalizedDescription` – StoreKit resolve automaticamente o texto correto e os preços com base no configurações de conta). O código a seguir pertence à notificação que criamos acima:

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

Por fim, o método `ViewWillDisappear` deve garantir que o observador seja removido:

```csharp
NSNotificationCenter.DefaultCenter.RemoveObserver (priceObserver);
```

#### <a name="skproductrequestdelegate-inapppurchasemanager-methods"></a>Métodos SKProductRequestDelegate (InAppPurchaseManager)

O método `RequestProductData` é chamado quando o aplicativo deseja recuperar os preços do produto e outras informações. Ele analisa a coleção de IDs de produto no tipo de dados correto e, em seguida, cria um `SKProductsRequest` com essas informações. Chamar o método Start faz com que uma solicitação de rede seja feita aos servidores da Apple. A solicitação será executada de forma assíncrona e chamará o método `ReceivedResponse` do delegado quando ele for concluído com êxito.

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

o iOS roteará automaticamente a solicitação para a versão "sandbox" ou "produção" da loja de aplicativos, dependendo do perfil de provisionamento com o qual o aplicativo está sendo executado – então, quando você estiver desenvolvendo ou testando seu aplicativo, a solicitação terá acesso a todos os produtos configurado no iTunes Connect (mesmo aqueles que ainda não foram enviados ou aprovados pela Apple). Quando seu aplicativo estiver em produção, as solicitações de StoreKit retornarão apenas informações para produtos **aprovados** .   

O `ReceivedResponse` método substituído é chamado depois que os servidores da Apple tiverem respondido com os dados. Como isso é chamado em segundo plano, o código deve analisar os dados válidos e usar uma notificação para enviar as informações do produto para qualquer ViewControllers que esteja ' ouvindo ' para essa notificação. O código para coletar informações válidas do produto e enviar uma notificação é mostrado abaixo:

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

Embora não seja mostrado no diagrama, o método `RequestFailed` também deve ser substituído para que você possa fornecer alguns comentários ao usuário caso os servidores da loja de aplicativos não estejam acessíveis (ou algum outro erro ocorra). O código de exemplo simplesmente grava no console, mas um aplicativo real pode optar por consultar `error.Code` Propriedade e implementar o comportamento personalizado (como um alerta para o usuário).

```csharp
public override void RequestFailed (SKRequest request, NSError error)
{
   Console.WriteLine (" ** InAppPurchaseManager RequestFailed() " + error.LocalizedDescription);
}
```

Esta captura de tela mostra o aplicativo de exemplo imediatamente após o carregamento (quando nenhuma informação de produto está disponível):

 [![](store-kit-overview-and-retreiving-product-information-images/image24.png "The sample app immediately after loading when no product information is available")](store-kit-overview-and-retreiving-product-information-images/image24.png#lightbox)

## <a name="invalid-products"></a>Produtos inválidos

Um `SKProductsRequest` também pode retornar uma lista de IDs de produto inválidas. Os produtos inválidos geralmente são retornados devido a um dos seguintes:   

A **ID do produto foi digitada intipadamente** – somente IDs de produto válidas são aceitas.   
   
 O **produto não foi aprovado** – durante o teste, todos os produtos limpos para venda devem ser retornados por um `SKProductsRequest`; no entanto, em produção, apenas produtos aprovados são retornados.   
   
 A **ID do aplicativo não é explícita** – as IDs do aplicativo curinga (com um asterisco) não permitem a compra no aplicativo.   
   
 **Perfil de provisionamento incorreto** – se você fizer alterações na configuração do aplicativo no portal de provisionamento (como habilitar compras no aplicativo), lembre-se de gerar novamente e usar o perfil de provisionamento correto ao compilar o aplicativo.   
   
 o **contrato de aplicativos pagos do IOS não está em vigor** – os recursos do StoreKit não funcionarão, a menos que haja um contrato válido para sua conta de desenvolvedor da Apple.   
   
 **O binário está no estado rejeitado** – se houver um binário enviado anteriormente no estado rejeitado (seja pela equipe da loja de aplicativos ou pelo desenvolvedor), os recursos do StoreKit não funcionarão.

O método `ReceivedResponse` no código de exemplo gera os produtos inválidos para o console:

```csharp
public override void ReceivedResponse (SKProductsRequest request, SKProductsResponse response)
{
   // code removed for clarity
   foreach (string invalidProductId in response.InvalidProducts) {
       Console.WriteLine("Invalid product id: " + invalidProductId );
   }
}
```

## <a name="displaying-localized-prices"></a>Exibindo preços localizados

Os tipos de preço especificam um preço específico para cada produto em todas as lojas de aplicativos internacionais. Para garantir que os preços sejam exibidos corretamente para cada moeda, use o método de extensão a seguir (definido em `SKProductExtension.cs`) em vez da propriedade Price de cada `SKProduct`:

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

Usar duas contas de teste do iTunes diferentes (uma para a loja americana e outra para a loja japonesa) resulta nas seguintes capturas de tela:   

 [![](store-kit-overview-and-retreiving-product-information-images/image25.png "Two different iTunes test accounts showing language specific results")](store-kit-overview-and-retreiving-product-information-images/image25.png#lightbox)   

Observe que o repositório afeta o idioma usado para as informações do produto e a moeda de preço, enquanto a configuração de idioma do dispositivo afeta os rótulos e outros conteúdos localizados.   

Lembre-se de que para usar uma conta de teste de repositório diferente, você deve **sair** das **configurações > iTunes e App Store** e reiniciar o aplicativo para entrar com uma conta diferente. Para alterar o idioma do dispositivo, acesse **configurações > geral > linguagem de > internacional**.
