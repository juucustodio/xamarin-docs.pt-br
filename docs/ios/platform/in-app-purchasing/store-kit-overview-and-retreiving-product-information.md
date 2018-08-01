---
title: Visão geral de StoreKit e recuperar informações do produto em xamarin
description: Este documento fornece uma visão geral de StoreKit. Descreve classes usadas com StoreKit, teste StoreKit interações, exibindo produtos de venda, produtos inválidos de manipulação e exibindo preços localizados.
ms.prod: xamarin
ms.assetid: FC21192E-6325-4389-C060-E92DBB5EBD87
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/18/2017
ms.openlocfilehash: 964b97e82db8e79cb32598d0c955fac3ab122314
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/05/2018
ms.locfileid: "34787218"
---
# <a name="storekit-overview-and-retrieving-product-info-in-xamarinios"></a>Visão geral de StoreKit e recuperar informações do produto em xamarin

A interface do usuário para uma compra no aplicativo é mostrada nas capturas de tela abaixo.
Antes de qualquer transação ocorre, o aplicativo deve recuperar preço do produto e a descrição para exibição. Em seguida, quando o usuário pressiona **comprar**, o aplicativo faz uma solicitação para StoreKit que gerencia o diálogo de confirmação e o logon de ID da Apple. Supondo que a transação tiver êxito, StoreKit notifica o código do aplicativo, que deve armazenar o resultado da transação e fornecer ao usuário acesso a suas compras.   

   
 [![](store-kit-overview-and-retreiving-product-information-images/image14.png "StoreKit notifica o código do aplicativo, que deve armazenar o resultado da transação e fornecer ao usuário acesso a compra")](store-kit-overview-and-retreiving-product-information-images/image14.png#lightbox)

## <a name="classes"></a>Classes

Implementação de compras no aplicativo requer as seguintes classes do framework StoreKit:   
   
 **SKProductsRequest** – uma solicitação StoreKit para os produtos aprovados vender (loja de aplicativos). Pode ser configurado com um número de Ids de produtos.

-   **SKProductsRequestDelegate** – declara os métodos para lidar com o produto solicitações e respostas. 
-   **SKProductsResponse** – enviadas de volta para o representante de StoreKit (loja de aplicativos). Contém o SKProducts que correspondem o produto Ids enviados com a solicitação. 
-   **SKProduct** – um produto é recuperado do StoreKit (que você configurou no iTunes conectar). Contém informações sobre o produto, como ID do produto, título, descrição e preço. 
-   **SKPayment** – criado com uma ID de produto e adicionados à fila de pagamento para realizar uma compra. 
-   **SKPaymentQueue** – solicitações de pagamento na fila para ser enviada à Apple. As notificações são acionadas como resultado de cada pagamento que está sendo processado. 
-   **SKPaymentTransaction** – representa uma transação concluída (uma solicitação de compra que foi processada pela loja de aplicativos e enviada de volta ao seu aplicativo por meio de StoreKit). A transação pode ser comprado, restaurados ou falha. 
-   **SKPaymentTransactionObserver** – subclasse personalizada que responde a eventos gerados pela fila StoreKit pagamento. 
-   **As operações StoreKit são assíncronas** – depois que um SKProductRequest é iniciado ou um SKPayment é adicionado à fila, o controle é retornado para o seu código. StoreKit chamará métodos na sua subclasse SKProductsRequestDelegate ou SKPaymentTransactionObserver quando ele recebe dados de servidores da Apple. 


O diagrama a seguir mostra as relações entre as várias classes de StoreKit (classes abstratas devem ser implementadas em seu aplicativo):   
   
   
   
 [![](store-kit-overview-and-retreiving-product-information-images/image15.png "As relações entre as várias classes abstratas de classes de StoreKit devem ser implementadas no aplicativo")](store-kit-overview-and-retreiving-product-information-images/image15.png#lightbox)   
   
   
   
 Essas classes são explicadas em mais detalhes posteriormente neste documento.

## <a name="testing"></a>Testes

A maioria das operações de StoreKit requer um dispositivo real para teste. Recuperando informações do produto (ie. preço &amp; descrição) funcionarão no simulador mas compra e operações de restauração serão retornado um erro (como código FailedTransaction = 5002 Ocorreu um erro desconhecido).

Observação: StoreKit não funcionará no simulador de iOS. Ao executar seu aplicativo no simulador de iOS, StoreKit registra um aviso se o aplicativo tenta recuperar a fila de pagamento. O repositório de teste deve ser feito em dispositivos.   
   
   
   
 Importante: Não entre com sua conta de teste no aplicativo configurações. Você pode usar o aplicativo de configurações para assinar qualquer conta existente do ID da Apple e, em seguida, você deve esperar para ser solicitado *dentro de sua sequência de compras no aplicativo* ao fazer logon usando um teste ID Apple.   
   
   
   

Se você tentar entrar para o armazenamento real com uma conta de teste, ele será convertido automaticamente para uma ID da Apple real. Essa conta não poderá ser usada para teste.

Para testar código StoreKit, você deve logoff de sua conta de teste do iTunes regular e o logon com uma conta de teste especial (criada no iTunes Connect) que está vinculada ao armazenamento de teste. Sair de visitar a conta atual **Configurações > iTunes e App Store** conforme mostrado aqui:

 [![](store-kit-overview-and-retreiving-product-information-images/image16.png "Para sair da conta atual visite iTunes configurações de loja de aplicativos")](store-kit-overview-and-retreiving-product-information-images/image16.png#lightbox)
 
Faça logon com uma conta de teste *quando solicitado por StoreKit dentro de seu aplicativo*:



Para criar usuários de teste no iTunes Connect, clique no **usuários e funções** na página principal.

 [![](store-kit-overview-and-retreiving-product-information-images/image17.png "Para criar usuários de teste no iTunes conectar clique em usuários e funções na página principal")](store-kit-overview-and-retreiving-product-information-images/image17.png#lightbox)

Selecione **testadores de área restrita**

 [![](store-kit-overview-and-retreiving-product-information-images/image18.png "Selecionando os testadores a área restrita")](store-kit-overview-and-retreiving-product-information-images/image18.png#lightbox)

A lista de usuários existentes é exibida. Você pode adicionar um novo usuário ou exclua um registro existente. O portal não (atualmente) lhe permite exibir ou editar existentes testar usuários, portanto, é recomendável que você mantenha um registro boa de cada usuário de teste é criado (especialmente a senha que você atribuir). Depois de excluir um usuário de teste o endereço de email não pode ser reutilizado para outra conta de teste.  
   
 [![](store-kit-overview-and-retreiving-product-information-images/image19.png "É exibida a lista de usuários existentes")](store-kit-overview-and-retreiving-product-information-images/image19.png#lightbox)   
   
 Novos usuários de teste com atributos semelhantes a uma ID Apple real (como nome, senha, pergunta e resposta). Manter um registro de todos os detalhes inseridos aqui. O **selecione iTunes Store** campo determinará qual moeda e linguagem de compras no aplicativo usará quando logado como esse usuário.

 [![](store-kit-overview-and-retreiving-product-information-images/image20.png "O campo Selecione iTunes Store determinará a moeda e o idioma para suas compras no aplicativo do usuário")](store-kit-overview-and-retreiving-product-information-images/image20.png#lightbox)

## <a name="retrieving-product-information"></a>Recuperando informações de produto

A primeira etapa na venda de um produto de compra no aplicativo está exibindo: recuperando o preço atual e a descrição da loja de aplicativos para exibição.   
   
   
   
 Independentemente de qual tipo de produtos que um aplicativo vende (que pode ser utilizada, não consumível ou um tipo de assinatura), o processo de recuperar informações de produto para exibição é o mesmo. O código de InAppPurchaseSample que acompanha este artigo contém um projeto chamado *consumível* que demonstra como recuperar informações de produção para exibição. Ele mostra como:

-  Crie uma implementação de `SKProductsRequestDelegate` e implementar o `ReceivedResponse` método abstract. O exemplo de código chama o `InAppPurchaseManager` classe. 
-  Entre em contato com StoreKit para ver se os pagamentos são permitidos (usando `SKPaymentQueue.CanMakePayments` ). 
-  Criar uma instância de um `SKProductsRequest` com os Ids de produtos que foram definidos na iTunes conectar. Isso é feito o exemplo `InAppPurchaseManager.RequestProductData` método. 
-  Chamar o método de iniciar o `SKProductsRequest` . Isso dispara uma chamada assíncrona para os servidores do App Store. O representante ( `InAppPurchaseManager` ) será o retorno de chamada com os resultados. 
-  O representante ( `InAppPurchaseManager` ) `ReceivedResponse` método atualiza a interface do usuário com os dados retornados da App Store (preços de produtos e descrições ou mensagens sobre produtos inválidos). 

A interação geral tem esta aparência ( **StoreKit** é interno para iOS e o **App Store** representa servidores da Apple):

 [![](store-kit-overview-and-retreiving-product-information-images/image21.png "Gráfico de recuperar informações do produto")](store-kit-overview-and-retreiving-product-information-images/image21.png#lightbox)

### <a name="displaying-product-information-example"></a>Exibir exemplo de informações do produto

O [InAppPurchaseSample](https://developer.xamarin.com/samples/monotouch/StoreKit/) *consumível* código de exemplo demonstra como informações de produto podem ser recuperadas. A tela principal do exemplo exibe informações dos dois produtos que são recuperadas da loja de aplicativos:   
   
   
   
 [![](store-kit-overview-and-retreiving-product-information-images/image23.png "A tela principal exibe produtos de informações recuperados do repositório do aplicativo")](store-kit-overview-and-retreiving-product-information-images/image23.png#lightbox)   
   
   
   
 O exemplo de código para recuperar e exibir informações sobre o produto é explicado em mais detalhes abaixo.


#### <a name="viewcontroller-methods"></a>Métodos de ViewController

O `ConsumableViewController` classe gerenciará a exibição de preços dos dois produtos cujos IDs de produto são embutidos em código na classe.

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

   
   
 No final do `ViewWillAppear` método, chame o `RequestProductData` método para iniciar a solicitação StoreKit. Quando essa solicitação é feita, StoreKit assincronamente contatar servidores da Apple para obter as informações e inseri-la para seu aplicativo. Isso é obtido com o `SKProductsRequestDelegate` subclasse ( `InAppPurchaseManager`) que é explicada na próxima seção.

```csharp
iap.RequestProductData(products);
```

O código para exibir o preço e a descrição simplesmente recupera as informações do SKProduct e o atribui a controles UIKit (Observe que podemos exibir o `LocalizedTitle` e `LocalizedDescription` – StoreKit resolve automaticamente o texto correto e preços com base em configurações da conta do usuário). O código a seguir pertence a notificação que criamos acima:

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

Por fim, o `ViewWillDisappear` método deve garantir que o observador seja removido:

```csharp
NSNotificationCenter.DefaultCenter.RemoveObserver (priceObserver);
```

#### <a name="skproductrequestdelegate-inapppurchasemanager-methods"></a>Métodos de SKProductRequestDelegate (InAppPurchaseManager)

O `RequestProductData` método é chamado quando o aplicativo que deseja recuperar os preços dos produtos e outras informações. Ele analisa a coleção de Ids de produto para o tipo de dados correto, em seguida, cria um `SKProductsRequest` com essas informações. Chamar o método Start faz com que uma solicitação de rede a serem feitas nos servidores da Apple. A solicitação será executado de forma assíncrona e chamar o `ReceivedResponse` método do representante quando ele for concluído com êxito.

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

iOS automaticamente encaminhará a solicitação para a versão 'área restrita' ou 'produção' da loja de aplicativos, dependendo de qual perfil de provisionamento do aplicativo é executado com – portanto, quando você estiver desenvolvendo ou testar seu aplicativo a solicitação terá acesso a todos os produtos configurado na iTunes Connect (mesmo aqueles que ainda não foram enviadas ou aprovada pela Apple). Quando seu aplicativo estiver em produção, solicitações de StoreKit só retornará informações para **aprovado** produtos.   
   
   
   
 O `ReceivedResponse` método substituído é chamado depois que os servidores da Apple responderam com dados. Como isso é chamado em segundo plano, o código deve analisar os dados válidos e usar uma notificação para enviar as informações do produto a qualquer ViewControllers que são 'escutando' para essa notificação. O código para coletar informações de produto válida e enviar uma notificação é mostrado abaixo:

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

Embora não mostrado no diagrama, o `RequestFailed` método também deve ser substituído para que você pode fornecer seus comentários para o usuário no caso de servidores de loja de aplicativos não estão acessíveis (ou algum outro erro ocorre). O exemplo de código simplesmente grava no console, mas um aplicativo real pode optar por consultar para `error.Code` propriedade e implementar o comportamento personalizado (por exemplo, um alerta para o usuário).

```csharp
public override void RequestFailed (SKRequest request, NSError error)
{
   Console.WriteLine (" ** InAppPurchaseManager RequestFailed() " + error.LocalizedDescription);
}
```

Esta captura de tela mostra o aplicativo de exemplo imediatamente após o carregamento (quando não há informações de produto disponíveis):

 [![](store-kit-overview-and-retreiving-product-information-images/image24.png "O aplicativo de exemplo imediatamente após o carregamento quando não há informações de produto")](store-kit-overview-and-retreiving-product-information-images/image24.png#lightbox)

## <a name="invalid-products"></a>Produtos inválidos

Um `SKProductsRequest` também podem retornar uma lista de IDs de produto inválido. Produtos inválidos geralmente são retornados devido a um dos seguintes:   
   
   
   
 **ID do produto tiver sido digitada incorretamente** – são aceitas apenas as IDs de produto válida.   
   
 **Produto não foi aprovado** – durante o teste, todos os produtos que estão desmarcados para venda devem ser retornados por um `SKProductsRequest`; no entanto, em produção somente os produtos aprovados são retornados.   
   
 **ID do aplicativo não é explícita** – Wildcard App IDs (com um asterisco) não permitem a compra no aplicativo.   
   
 **Incorreta de perfil de provisionamento** – se você fizer alterações em sua configuração de aplicativo no portal de provisionamento (como habilitar compras no aplicativo), lembre-se gerar novamente e use o perfil de provisionamento correto ao compilar o aplicativo.   
   
 **contrato de aplicativos pagos iOS não está em vigor** – StoreKit recursos não funcionará em todos os menos que haja um contrato válido para a sua conta de desenvolvedor da Apple.   
   
 **O binário está no estado rejeitado** – se houver um binário enviado anteriormente no estado rejeitado (ou pela equipe de loja de aplicativos ou pelo desenvolvedor) StoreKit recursos não funcionará.

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

## <a name="displaying-localized-prices"></a>Exibindo os preços localizados

Camadas de preço especificar um preço específico para cada produto em todas as lojas de aplicativos internacionais. Para garantir que os preços são exibidos corretamente para cada moeda, use o seguinte método de extensão (definido em `SKProductExtension.cs`) em vez da propriedade de preço de cada `SKProduct`:

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

Usando duas contas de teste iTunes diferente (uma para o repositório American) e outra para o repositório de japonês resultam em capturas de tela as seguir:   
   
   
   
 [![](store-kit-overview-and-retreiving-product-information-images/image25.png "Dois iTunes diferentes testar contas mostrando resultados específicos de idioma")](store-kit-overview-and-retreiving-product-information-images/image25.png#lightbox)   
   
   
   
 Observe que o armazenamento afeta o idioma usado para a moeda de informações e o preço do produto, enquanto a configuração de idioma do dispositivo afeta rótulos e outro conteúdo localizado.   
   
   
   
 Lembre-se de que usar um repositório de diferente testar conta, você deve **sair** no **Configurações > iTunes e loja de aplicativos** e reinicie o aplicativo para entrar com uma conta diferente. Para alterar idioma do dispositivo, vá para **Configurações > Geral > internacional > idioma**.

