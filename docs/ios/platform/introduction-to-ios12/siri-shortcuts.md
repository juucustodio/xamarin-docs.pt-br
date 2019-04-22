---
title: Atalhos do Siri no xamarin. IOS
description: Este documento descreve como usar atalhos do Siri no iOS 12. Ele aborda NSUserActivities, intenções personalizadas, atribuir atalhos de voz, atalhos relevantes e muito mais.
ms.prod: xamarin
ms.assetid: 86424F79-3A7D-436E-927D-9A3267DA333B
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 08/08/2018
ms.openlocfilehash: e37fd88f0d5fcf02ece0ae2f5e3164a507067e29
ms.sourcegitcommit: 3489c281c9eb5ada2cddf32d73370943342a1082
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/18/2019
ms.locfileid: "58869818"
---
# <a name="siri-shortcuts-in-xamarinios"></a>Atalhos do Siri no xamarin. IOS

Na [iOS 10](~/ios/platform/sirikit/index.md), a Apple introduziu SiriKit, tornando possível para compilação de mensagens, chamada VoIP, pagamentos, treinamentos, surfar reserva e pesquisar aplicativos que interagem com Siri de fotos.

Na [iOS 11](~/ios/platform/introduction-to-ios11/sirikit.md), SiriKit ganhou o suporte para mais tipos de aplicativos e maior flexibilidade para personalização da interface do usuário.

iOS 12 adiciona Siri atalhos, permitindo que todos os tipos de aplicativos exponham suas funcionalidades para Siri. Siri aprende quando determinadas tarefas com base no aplicativo são mais relevantes para o usuário e usa esse conhecimento para sugerir possíveis ações via _atalhos_. Tocando em um atalho ou invocá-lo com um comando de voz será abrir um aplicativo ou executar uma tarefa em segundo plano.

Atalhos de devem ser usados para acelerar a capacidade de um usuário para realizar uma tarefa comum – em muitos casos, sem precisar abrir o aplicativo em questão.

## <a name="sample-app-soup-chef"></a>Aplicativo de exemplo: Sopa Chef

Para entender melhor o Siri atalhos, dê uma olhada a [sopa Chef](https://developer.xamarin.com/samples/monotouch/ios12/SoupChef/) aplicativo de exemplo. Sopa Chef permite aos usuários fazer pedidos de um restaurante sopa imaginário, exibir histórico de seus pedidos e definir expressões para usar ao ordenar sopa interagindo com Siri.

> [!TIP]
> Antes de testar Chef sopa em um dispositivo ou simulador de iOS 12, habilite a duas configurações a seguir, que são úteis ao depurar atalhos:
>
> - No **configurações** aplicativo, habilite **desenvolvedor > atalhos recentes de exibição**.
> - No **configurações** aplicativo, habilite **desenvolvedor > doações de exibição na tela de bloqueio**.
>
> Eles depurar Verifique as configurações a encontrar-criado recentemente (em vez de previstos) atalhos no iOS tela de bloqueio e tela de pesquisa.

Para usar o aplicativo de exemplo:

- Instalar e executar o Chef Sopa de aplicativo de exemplo em um simulador de iOS 12 ou [dispositivo](#testing-on-device).
- Clique o **+** botão no canto superior direito para criar uma nova ordem.
- Selecione um tipo de sopa, especifique uma quantidade e as opções e toque **fazer pedido**.
- Sobre o **histórico de pedidos** tela, toque em ordem recém-criado para exibir seus detalhes.
- Na parte inferior da tela de detalhes do pedido, toque em **adicionar a Siri**.
- Registre uma frase de voz para associar com a ordem e toque **feito**.
- Minimizar sopa Chef, invocar Siri e fazer o pedido novamente usando a frase de voz que você acabou de gravar.
- Concluída a Siri o pedido, abra novamente o sopa Chef e observe que o novo pedido está listado na **histórico de pedidos** tela.

O aplicativo de exemplo demonstra como:

- [Use um atalho NSUserActivity para abrir um aplicativo](#using-an-nsuseractivity-shortcut-to-open-an-app).
- [Usar um atalho de intenção personalizado para executar uma tarefa](#using-a-custom-intent-shortcut-to-perform-a-task).
- [Fornecer uma interface de usuário para uma intenção personalizada](#providing-a-user-interface-for-a-custom-intent).
- [Criar um atalho de voz](#creating-a-voice-shortcut).

## <a name="infoplist-and-entitlementsplist"></a>Info. plist e Entitlements. plist

Antes de aprofundar mais profundamente os-para o código de sopa Chef, dar uma olhada no seu **Info. plist** e **Entitlements. plist** arquivos.

### <a name="infoplist"></a>Info.plist

O **Info. plist** arquivo na **SoupChef** projeto define o **identificador do pacote** como `com.xamarin.SoupChef`. Esse identificador de lote será usado como um prefixo para os identificadores de pacote do intenções e propósitos de interface do usuário do extensões discutidas neste documento.

O **Info. plist** arquivo também contém o seguinte:

```xml
<key>NSUserActivityTypes</key>
<array>
    <string>OrderSoupIntent</string>
    <string>com.xamarin.SoupChef.viewMenu</string>
</array>
```

Isso `NSUserActivityTypes` par chave/valor indica que o Chef sopa sabe como lidar com um `OrderSoupIntent`e um [ `NSUserActivity` ](xref:Foundation.NSUserActivity) tendo um [ `ActivityType` ](xref:Foundation.NSUserActivity.ActivityType) de "com.xamarin.SoupChef.viewMenu".

Atividades e intenções personalizadas passadas para o aplicativo em si, em vez de suas extensões são manipuladas na `AppDelegate` (uma [ `UIApplicationDelegate` ](xref:UIKit.UIApplicationDelegate) pela [ `ContinueUserActivity` ](xref:UIKit.UIApplicationDelegate.ContinueUserActivity*) método.

### <a name="entitlementsplist"></a>Entitlements.plist

O **Entitlements. plist** arquivo na **SoupChef** projeto contém o seguinte:

```xml
<key>com.apple.security.application-groups</key>
<array>
    <string>group.com.xamarin.SoupChef</string>
</array>
<key>com.apple.developer.siri</key>
<true/>
```

Essa configuração indica que o aplicativo usa o grupo de aplicativos "group.com.xamarin.SoupChef". O **SoupChefIntents** extensão do aplicativo usa o mesmo grupo de aplicativo, que permite que os dois projetos compartilhar [`NSUserDefaults`](xref:Foundation.NSUserDefaults)
dados.

O `com.apple.developer.siri` chave indica que o aplicativo interage com Siri.

> [!NOTE]
> O **SoupChef** conjuntos de configuração de build do projeto **direitos personalizados** para **Entitlements. plist**.

## <a name="using-an-nsuseractivity-shortcut-to-open-an-app"></a>Usando um atalho NSUserActivity para abrir um aplicativo

Para criar um atalho que abre um aplicativo para exibir o conteúdo específico, crie um `NSUserActivity` e anexá-lo para o controlador de exibição para a tela que você gostaria de atalho para abrir.

### <a name="setting-up-an-nsuseractivity"></a>Configurando um NSUserActivity

Na tela do menu, `SoupMenuViewController` cria um `NSUserActivity` e atribui a ele para o controlador de exibição [ `UserActivity` ](xref:UIKit.UIResponder.UserActivity) propriedade:

```csharp
public override void ViewDidLoad()
{
    base.ViewDidLoad();
    UserActivity = NSUserActivityHelper.ViewMenuActivity;
}
```

Definindo o `UserActivity` propriedade _doa_ a atividade a Siri. Dessa doação, Siri obtém informações sobre quando e onde essa atividade é relevante para o usuário e aprende para melhor sugeri-lo no futuro.

`NSUserActivityHelper` uma classe de utilitário que está incluída na **SoupChef** solução, no **SoupKit** biblioteca de classes. Ele cria um `NSUserActivity` e define várias propriedades relacionadas a Siri e pesquisa:

```csharp
public static string ViewMenuActivityType = "com.xamarin.SoupChef.viewMenu";

public static NSUserActivity ViewMenuActivity {
    get
    {
        var userActivity = new NSUserActivity(ViewMenuActivityType)
        {
            Title = NSBundleHelper.SoupKitBundle.GetLocalizedString("ORDER_LUNCH_TITLE", "View menu activity title"),
            EligibleForSearch = true,
            EligibleForPrediction = true
        };

        var attributes = new CSSearchableItemAttributeSet(NSUserActivityHelper.SearchableItemContentType)
        {
            ThumbnailData = UIImage.FromBundle("tomato").AsPNG(),
            Keywords = ViewMenuSearchableKeywords,
            DisplayName = NSBundleHelper.SoupKitBundle.GetLocalizedString("ORDER_LUNCH_TITLE", "View menu activity title"),
            ContentDescription = NSBundleHelper.SoupKitBundle.GetLocalizedString("VIEW_MENU_CONTENT_DESCRIPTION", "View menu content description")
        };
        userActivity.ContentAttributeSet = attributes;

        var phrase = NSBundleHelper.SoupKitBundle.GetLocalizedString("ORDER_LUNCH_SUGGESTED_PHRASE", "Voice shortcut suggested phrase");
        userActivity.SuggestedInvocationPhrase = phrase;
        return userActivity;
    }
}
```

Observe o seguinte em particular:

- Definindo `EligibleForPrediction` para `true` indica que o Siri pode prever esta atividade e fazê-lo como um atalho.
- O [ `ContentAttributeSet` ](xref:Foundation.NSUserActivity.ContentAttributeSet) array é um padrão [ `CSSearchableItemAttributeSet` ](xref:CoreSpotlight.CSSearchableItemAttributeSet) usado para incluir um `NSUserActivity` nos resultados da pesquisa de iOS.
- [`SuggestedInvocationPhrase`](xref:Foundation.NSUserActivity.SuggestedInvocationPhrase) é uma frase que Siri sugerirá ao usuário como uma escolha potencial durante a atribuição de uma frase em um atalho.

### <a name="handling-an-nsuseractivity-shortcut"></a>Tratamento de um atalho NSUserActivity

Para lidar com um `NSUserActivity` atalho invocado por um usuário, um aplicativo iOS deve substituir o `ContinueUserActivity` método da `AppDelegate` classe, respondendo com base no `ActivityType` campo do passado `NSUserActivity` objeto:

```csharp
public override bool ContinueUserActivity(UIApplication application, NSUserActivity userActivity, UIApplicationRestorationHandler completionHandler)
{
    // ...
    else if (userActivity.ActivityType == NSUserActivityHelper.ViewMenuActivityType)
    {
        HandleUserActivity();
        return true;
    }
    // ...
}
```

Este método chama `HandleUserActivity`, que localiza o segue para a tela do menu e o invoca:

```csharp
void HandleUserActivity()
{
    var rootViewController = Window?.RootViewController as UINavigationController;
    var orderHistoryViewController = rootViewController?.ViewControllers?.FirstOrDefault() as OrderHistoryTableViewController;
    if (orderHistoryViewController is null)
    {
        Console.WriteLine("Failed to access OrderHistoryTableViewController.");
        return;
    }
    var segue = OrderHistoryTableViewController.SegueIdentifiers.SoupMenu;
    orderHistoryViewController.PerformSegue(segue, null);
}
```

### <a name="assigning-a-phrase-to-an-nsuseractivity"></a>Atribuindo uma frase para um NSUserActivity

Para atribuir uma expressão para uma `NSUserActivity`, abra o iOS **configurações** aplicativo e escolha **Siri e pesquisa > Meus atalhos**. Em seguida, selecione o atalho (nesse caso, "Ordem de almoço") e registre uma frase.

Invocando Siri e usar essa frase serão aberto sopa Chef para a tela do menu.

## <a name="using-a-custom-intent-shortcut-to-perform-a-task"></a>Usando um atalho de intenção personalizado para executar uma tarefa

### <a name="defining-a-custom-intent"></a>Definindo uma intenção personalizada

Para fornecer um atalho que permite que um usuário concluir rapidamente a uma tarefa específica relacionada ao seu aplicativo, crie uma intenção personalizada. Uma intenção personalizada representa uma tarefa que um usuário pode querer para ser concluída, os parâmetros relevantes a essa tarefa e possíveis respostas resultantes da execução da tarefa. Dependendo de como uma intenção personalizada é definida, invocá-lo pode abrir seu aplicativo, ou executar uma tarefa em segundo plano.

Use 10 do Xcode para criar as intenções personalizadas. No [SoupChef repositório](https://github.com/xamarin/ios-samples/tree/master/ios12/SoupChef), a intenção personalizada é definida no **OrderSoupIntentCodeGen**, um projeto de Objective-C. Abrir este projeto e selecione o **Intents.intentdefinition** arquivo na **navegador de projeto** para exibir o **OrderSoup** intenção.

Observe o seguinte:

- A intenção tem um **categoria** dos **ordem**. Há várias categorias predefinidas que podem ser usadas para propósitos de personalizado; Selecione aquele que mais se aproxima da tarefa que permitirá que sua intenção personalizada. Como essa é uma mistura de ordenação do aplicativo, **OrderSoupIntent** usa **ordem**.
- O **confirmação** caixa de seleção indica se o Siri deve solicitar confirmação antes de executar a tarefa. Para o **Sopa de ordem** intencional no sopa Chef, essa opção é habilitada, pois o usuário está fazendo uma compra.
- O **parâmetros** seção do arquivo .intentdefinition define os parâmetros relevantes a um atalho. Para fazer um pedido sopa, Chef sopa deve saber o tipo de sopa, sua quantidade e qualquer opção associada.
Cada parâmetro tem um tipo; parâmetro não pode ser representado por um tipo predefinido está definido como **personalizado**.
- O **tipos de atalho** interface descreve as várias combinações de parâmetro Siri pode usar ao sugerir o atalho. Associado **Title** e **subtítulo** seções permitem que você defina as mensagens Siri usará ao apresentar um atalho sugerido para o usuário.
- O **dá suporte à execução de plano de fundo** caixa de seleção deve ser selecionada para qualquer atalho que pode ser executado sem abrir o aplicativo para interação adicional do usuário.

### <a name="defining-custom-intent-responses"></a>Definir respostas personalizadas de intenção

O **resposta** item aninhado abaixo de **OrderSoup** intenção representa as respostas possíveis que resultam de uma ordem de letrinhas.

No **OrderSoup** definição da intenção da resposta, observe o seguinte:

- Uma resposta **propriedades** pode ser usado para personalizar a mensagem de comunicado de volta para o usuário. O **OrderSoup** intenção resposta tem **sopa** e **waitTime** propriedades.
- O **modelos de resposta** especificar as várias mensagens de êxito e falha que podem ser usadas para indicar o status após a conclusão de tarefa de uma tentativa.
- O **sucesso** caixa de seleção deve ser selecionada para respostas que indicam o sucesso.
 - O **OrderSoupIntent** resposta de êxito usa o **sopa** e **waitTime** propriedades para fornecer uma mensagem amigável e útil que descrevem quando a ordem de sopa estará pronta.

### <a name="generating-code-for-the-custom-intent"></a>Geração de código para a intenção personalizado

Criando o projeto do Xcode que contém essa definição personalizada de intenção faz com que o Xcode gerar o código que pode ser usado para interagir programaticamente com a intenção personalizada e suas respostas.

Para exibir esse código gerado:

- Abra **Appdelegate**.
- Adicione uma importação para o arquivo de cabeçalho da intenção personalizado: `#import "OrderSoupIntent.h"`
- Em qualquer método na classe, adicione uma referência ao `OrderSoupIntent`.
- Clique duas vezes em `OrderSoupIntent` e escolha **ir para definição**.
- Clique com botão direito no arquivo de recém-aberta **OrderSoupIntent.h**e selecione **Mostrar no localizador**.
- Isso abrirá uma **Finder** janela que contém um arquivo. h e. m, que contém o código gerado.

Esse código gerado inclui:

- `OrderSoupIntent` – Uma classe que representa a intenção de personalizado.
- `OrderSoupIntentHandling` – Um protocolo que define os métodos que serão usados para confirmar que a intenção deve ser executada e o método que realmente executa-o.
- `OrderSoupIntentResponseCode` – Uma enumeração que define vários status de resposta.
- `OrderSoupIntentResponse` – uma classe que representa a resposta para a execução de uma intenção.

### <a name="creating-a-binding-to-the-custom-intent"></a>Criando uma associação à intenção personalizada

Para usar o código gerado pelo Xcode em um aplicativo xamarin. IOS, crie um C# de associação para ele.

#### <a name="creating-a-static-library-and-c-binding-definitions"></a>Criando uma biblioteca estática e C# definições de associação

No [SoupChef repositório](https://github.com/xamarin/ios-samples/tree/master/ios12/SoupChef), dê uma olhada **OrderSoupIntentStaticLib** pasta e abra o **OrderSoupIntentStaticLib.xcodeproj** projeto Xcode.

Isso **Cocoa Touch lib** projeto contém o **OrderSoupIntent.h** e **OrderSoupIntent.m** arquivos gerados pelo Xcode.

#### <a name="configuring-the-static-library-project-build-settings"></a>Configurando as configurações de build do projeto de biblioteca estática

No Xcode **navegador de projeto**, selecione o projeto de nível superior **OrderSoupIntentStaticLib**e navegue até **fases de compilação > compilar fontes**.
Observe que **OrderSoupIntent.m** (quais imports **OrderSoupIntent.h**) está listado aqui. Na **vincular binário com bibliotecas**, observe que **Intents.framework** e **Foundation.framework** são incluídos.
Com essas configurações em vigor, o framework seja compilado corretamente.

#### <a name="building-the-static-library-and-generating-c-bindings-definitions"></a>Criar a biblioteca estática e gerar C# definições de associações

Para compilar a biblioteca estática e gerar C# definições de associações para ele, siga estas etapas:

- [Instalar o objetivo Sharpie](https://docs.microsoft.com/xamarin/cross-platform/macios/binding/objective-sharpie/get-started?context=xamarin/mac#installing-objective-sharpie), a ferramenta usada para gerar definições de associações dos arquivos. h e. m criados pelo Xcode.

- Configure o sistema para usar as ferramentas de linha de comando do Xcode 10:

    > [!WARNING]
    > Atualizar as ferramentas de linha de comando selecionado afeta todas as versões instaladas do Xcode em seu sistema. Quando você terminar de usar o Chef Sopa de aplicativo de exemplo, não se esqueça de reverter isso definindo-se à sua configuração original.

    - No Xcode, escolha **Xcode > Preferências > locais** e defina **ferramentas de linha de comando** à instalação do Xcode 10 mais atual disponível no sistema.

- No terminal, `cd` para o **OrderSoupIntentStaticLib** directory.

- Tipo `make`, quais compilações:

    - A biblioteca estática, **libOrderSoupIntentStaticLib.a**
    - No **bo** diretório de saída, C# definições de associações:
        - **ApiDefinitions.cs**
        - **StructsAndEnums.cs**

O **OrderSoupIntentBindings** builds de projeto, que conta com essa biblioteca estática e suas definições de associações associadas, esses itens automaticamente.
No entanto, executar manualmente o processo acima garantirá que o se ele é compilado conforme o esperado.

Para obter mais informações sobre como criar uma biblioteca estática e usando o objetivo Sharpie para criar C# definições de associações, dê uma olhada a [associação de uma biblioteca do iOS Objective-C](https://docs.microsoft.com/xamarin/ios/platform/binding-objective-c/walkthrough?tabs=vsmac#creating-a-static-library) passo a passo.

#### <a name="creating-a-bindings-library"></a>Criando uma biblioteca de associações

Com a biblioteca estática e o C# as definições de associações criadas, a peça que falta é necessária consumir o Xcode gerado pelo código relacionado a intenção em um projeto xamarin. IOS uma biblioteca de associações.

No [repositório sopa Chef](https://github.com/xamarin/ios-samples/tree/master/ios12/SoupChef), abra o **SoupChef.sln** arquivo. Entre outras coisas, essa solução contém **OrderSoupIntentBinding**, uma biblioteca de associações para a biblioteca estática gerada acima.

Em particular, observe que esse projeto inclui:

- **ApiDefinitions.cs** – um arquivo gerado acima por objetivo Sharpie e adicionados a este projeto. Este arquivo **ação de compilação** é definido como **ObjcBindingApiDefinition**.
- **StructsAndEnums.cs** – outro arquivo gerado acima por objetivo Sharpie e adicionados a este projeto. Este arquivo **ação de compilação** é definido como **ObjcBindingCoreSource**.
- Um **referência nativa** à **libOrderSoupIntentStaticLib.a**, a biblioteca estática criada acima.

> [!NOTE]
> Ambos **ApiDefinitions.cs** e **StructsAndEnums.cs** contêm atributos como `[Watch (5,0), iOS (12,0)]`. Esses atributos, gerados pelo objetivo Sharpie, foram comentados, pois eles não são necessários para este projeto.

Para obter mais informações sobre como criar um C# biblioteca de associações, dê uma olhada nos [associação de um biblioteca Objective-C do iOS](https://docs.microsoft.com/xamarin/ios/platform/binding-objective-c/walkthrough?tabs=vsmac#create-a-xamarinios-binding-project) passo a passo.

Observe que o **SoupChef** projeto contém uma referência a **OrderSoupIntentBinding**, que significa que ele agora pode acessar, em C#, as classes, interfaces e enumerações que ele contém:

- `OrderSoupIntent`
- `OrderSoupIntentHandling`
- `OrderSoupIntentResponse`
- `OrderSoupIntenseResponseCode`

### <a name="adding-the-intentdefinition-file-to-your-solution"></a>Adicionando o arquivo de .intentdefinition à sua solução

No C# **SoupChef** solução, o **SoupKit** projeto contém o código compartilhado entre o aplicativo e suas extensões. O **Intents.intentdefinition** arquivo foi colocado na **Base.lproj** diretório de **SoupKit**, e ele tem um **Build Action** de **Conteúdo**. O processo de compilação copia esse arquivo em lote de aplicativo sopa Chef, onde ela é necessária para o aplicativo funcione corretamente.

### <a name="donating-an-intent"></a>Doar uma intenção

Em ordem para Siri sugerir um atalho, deve primeiro entender quando o atalho é relevante.

Para fornecer Siri essa compreensão, Chef sopa _doa_ uma tentativa de Siri cada vez que o usuário faz um pedido de letrinhas. Com base nessa doação – quando foram doado, onde ele foi doado, os parâmetros que ele contém – Siri aprende quando sugerir o atalho no futuro.

**SoupChef** usa o `SoupOrderDataManager` classe colocar doações.
Quando chamado para fazer um pedido sopa para um usuário, o `PlaceOrder` por sua vez chama um método [ `DonateInteraction` ](xref:Intents.INInteraction.DonateInteraction*):

```csharp
void DonateInteraction(Order order)
{
    var interaction = new INInteraction(order.Intent, null);
    interaction.Identifier = order.Identifier.ToString();
    interaction.DonateInteraction((error) =>
    {
        // ...
    });
}
```

Depois de buscar uma intenção, ele é encapsulado em um [ `INInteraction` ](xref:Intents.INInteraction).
O `INInteraction` recebe um [`Identifier`](xref:Intents.INInteraction.Identifier*)
que corresponde a ID exclusiva do pedido (Isso será útil mais tarde quando a exclusão de doações intencionais que não são mais válidas). Em seguida, a interação será doada a Siri.

A chamada para o `order.Intent` getter buscas uma `OrderSoupIntent` que representa a ordem, definindo seu `Quantity`, `Soup`, `Options`e a imagem e uma frase de invocação a ser usada como uma sugestão quando o usuário registra uma frase para Siri associar com a intenção:

```csharp
public OrderSoupIntent Intent
{
    get
    {
        var orderSoupIntent = new OrderSoupIntent();
        orderSoupIntent.Quantity = new NSNumber(Quantity);
        orderSoupIntent.Soup = new INObject(MenuItem.ItemNameKey, MenuItem.LocalizedString);

        var image = UIImage.FromBundle(MenuItem.IconImageName);
        if (!(image is null))
        {
            var data = image.AsPNG();
            orderSoupIntent.SetImage(INImage.FromData(data), "soup");
        }

        orderSoupIntent.Options = MenuItemOptions
            .ToArray<MenuItemOption>()
            .Select<MenuItemOption, INObject>(arg => new INObject(arg.Value, arg.LocalizedString))
            .ToArray<INObject>();

        var comment = "Suggested phrase for ordering a specific soup";
        var phrase = NSBundleHelper.SoupKitBundle.GetLocalizedString("ORDER_SOUP_SUGGESTED_PHRASE", comment);
        orderSoupIntent.SuggestedInvocationPhrase = String.Format(phrase, MenuItem.LocalizedString);

        return orderSoupIntent;
    }
}
```

#### <a name="removing-invalid-donations"></a>Remoção de doações inválidas

É importante remover doações que não são mais válidas para que o Siri não faz sugestões de atalho inúteis ou confusos.

No sopa Chef, o **Menu configurar** tela pode ser usada para marcar um menu de item como indisponível. Siri não deve sugerir um atalho para pedir um item de menu não está disponível, portanto, o `RemoveDonation` método de `SoupMenuManager` exclui doações para itens de menu que não estão mais disponíveis. Isso é feito:

- Localizando pedidos associados ao item de menu não está disponível agora.
- Pegando seus identificadores.
- Excluindo as interações que têm o mesmo identificador.

```csharp
void RemoveDonation(MenuItem menuItem)
{
    if (!menuItem.IsAvailable)
    {
        Order[] orderHistory = OrderManager?.OrderHistory.ToArray<Order>();
        if (orderHistory is null)
        {
            return;
        }

        string[] orderIdentifiersToRemove = orderHistory
            .Where<Order>((order) => order.MenuItem.ItemNameKey == menuItem.ItemNameKey)
            .Select<Order, string>((order) => order.Identifier.ToString())
            .ToArray<string>();

        INInteraction.DeleteInteractions(orderIdentifiersToRemove, (error) =>
        {
            if (!(error is null))
            {
                Console.WriteLine($"Failed to delete interactions with error {error.ToString()}");
            }
            else
            {
                Console.WriteLine("Successfully deleted interactions");
            }
        });
    }
}
```

### <a name="creating-an-intents-extension"></a>Criando uma extensão de tentativas

O código que é executado quando o Siri invoca uma intenção é colocado em uma extensão de tentativas, que pode ser adicionada como um novo projeto para a mesma solução que um aplicativo xamarin. IOS existente, como Chef de letrinhas. No **SoupChef** solução, a extensão é chamada **SoupChefIntents**.

#### <a name="soupchefintents-infoplist-and-entitlementsplist"></a>SoupChefIntents – Info. plist e Entitlements. plist

##### <a name="soupchefintents-infoplist"></a>SoupChefIntents – Info. plist

O **Info. plist** na **SoupChefIntents** projeto define o **identificador do pacote** como `com.xamarin.SoupChef.SoupChefIntents`.

O **Info. plist** arquivo também contém o seguinte:

```xml
<key>NSExtension</key>
<dict>
    <key>NSExtensionAttributes</key>
    <dict>
        <key>IntentsRestrictedWhileLocked</key>
        <array/>
        <key>IntentsSupported</key>
        <array>
            <string>OrderSoupIntent</string>
        </array>
        <key>IntentsRestrictedWhileProtectedDataUnavailable</key>
        <array/>
    </dict>
    <key>NSExtensionPointIdentifier</key>
    <string>com.apple.intents-service</string>
    <key>NSExtensionPrincipalClass</key>
    <string>IntentHandler</string>
</dict>
```

No acima **Info. plist**:

- `IntentsRestrictedWhileLocked` lista as intenções que só devem ser tratadas quando o dispositivo seja desbloqueado.
- `IntentsSupported` lista as tentativas manipuladas por esta extensão.
- `NSExtensionPointIdentifier` Especifica o tipo de extensão do aplicativo (consulte [documentação da Apple](https://developer.apple.com/library/archive/documentation/General/Reference/InfoPlistKeyReference/Articles/AppExtensionKeys.html#//apple_ref/doc/uid/TP40014212-SW15) para obter mais informações).
- `NSExtensionPrincipalClass` Especifica a classe que deve ser usada para lidar com as intenções essa extensão dá suportadas.

##### <a name="soupchefintents-entitlementsplist"></a>SoupChefIntents – Entitlements. plist

O **Entitlements. plist** na **SoupChefIntents** projeto tem o **grupos de aplicativos** funcionalidade. Esse recurso é configurado para usar o mesmo grupo de aplicativos como o **SoupChef** projeto:

```xml
<key>com.apple.security.application-groups</key>
<array>
    <string>group.com.xamarin.SoupChef</string>
</array>
```

Sopa Chef persistir dados com `NSUserDefaults`. Para compartilhar dados entre o aplicativo e a extensão do aplicativo, eles fazem referência o mesmo grupo de aplicativos em seus **Entitlements. plist** arquivos.

> [!NOTE]
> O **SoupChefIntents** conjuntos de configuração de build do projeto **direitos personalizados** para **Entitlements. plist**.

#### <a name="handling-an-ordersoupintent-background-task"></a>Tratamento de uma tarefa de plano de fundo OrderSoupIntent

Uma extensão de tentativas executa as tarefas em segundo plano necessários para um atalho com base em uma intenção personalizada.

Siri chamadas a [ `GetHandler` ](xref:Intents.INExtension.GetHandler*) método da `IntentHandler` classe (definido na **Info. plist** como o `NSExtensionPrincipalClass`) para obter uma instância de uma classe que estende o `OrderSoupIntentHandling`, que pode ser usado para lidar com um `OrderSoupIntent`:

```csharp
[Register("IntentHandler")]
public class IntentHandler : INExtension
{
    public override NSObject GetHandler(INIntent intent)
    {
        if (intent is OrderSoupIntent)
        {
            return new OrderSoupIntentHandler();
        }
        throw new Exception("Unhandled intent type: ${intent}");
    }

    protected IntentHandler(IntPtr handle) : base(handle) { }
}
```

`OrderSoupIntentHandler`, definido na **SoupKit** projeto de código compartilhado, implementa dois métodos de importantes:

- `ConfirmOrderSoup` – Confirma se a tarefa associada a intenção, na verdade, deve ser executada
- `HandleOrderSoup` – Faz o pedido de sopa e responde ao usuário chamando o manipulador de conclusão no passado

#### <a name="handling-an-ordersoupintent-that-opens-the-app"></a>Manipulando uma OrderSoupIntent que abre o aplicativo

Um aplicativo deve lidar adequadamente com as intenções que não são executados em segundo plano.
Eles são tratados da mesma forma como `NSUserActivity` atalhos, no `ContinueUserActivity` método `AppDelegate`:

```csharp
public override bool ContinueUserActivity(UIApplication application, NSUserActivity userActivity, UIApplicationRestorationHandler completionHandler)
{
    var intent = userActivity.GetInteraction()?.Intent as OrderSoupIntent;
    if (!(intent is null))
    {
        HandleIntent(intent);
        return true;
    }
    // ...
}  
```

## <a name="providing-a-user-interface-for-a-custom-intent"></a>Fornecendo uma interface de usuário para uma intenção personalizada

Uma extensão de tentativas da interface do usuário fornece uma interface de usuário personalizada para uma extensão de tentativas. No **SoupChef** solução **SoupChefIntentsUI** é uma extensão de tentativas da interface do usuário que fornece uma interface para **SoupChefIntents**.

### <a name="soupchefintentsui--infoplist-and-entitlementsplist"></a>SoupChefIntentsUI – Info. plist e Entitlements. plist

#### <a name="soupchefintentsui-infoplist"></a>SoupChefIntentsUI – Info.plist

O **Info. plist** na **SoupChefIntentsUI** projeto define o **identificador do pacote** como `com.xamarin.SoupChef.SoupChefIntentsui`.

O **Info. plist** arquivo também contém o seguinte:

```xml
<key>NSExtension</key>
<dict>
    <key>NSExtensionAttributes</key>
    <dict>
        <key>IntentsSupported</key>
        <array>
            <string>OrderSoupIntent</string>
        </array>
        <!-- ... -->
    </dict>
    <key>NSExtensionPointIdentifier</key>
    <string>com.apple.intents-ui-service</string>
    <key>NSExtensionMainStoryboard</key>
    <string>MainInterface</string>
</dict>
```

No acima **Info. plist**:

- `IntentsSupported` indica que o `OrderSoupIntent` é manipulada por esta extensão de tentativas da interface do usuário.
- `NSExtensionPointIdentifier` Especifica o tipo de extensão do aplicativo (consulte [documentação da Apple](https://developer.apple.com/library/archive/documentation/General/Reference/InfoPlistKeyReference/Articles/AppExtensionKeys.html#//apple_ref/doc/uid/TP40014212-SW15) para obter mais informações).
- `NSExtensionMainStoryboard` Especifica o storyboard que define a interface primária dessa extensão

#### <a name="soupchefintentsui-entitlementsplist"></a>SoupChefIntentsUI – Entitlements. plist

O **SoupChefIntentsUI** projeto não precisa de uma **Entitlements. plist** arquivo.

### <a name="creating-the-user-interface"></a>Criar a interface do usuário

Desde que o **Info. plist** para **SoupChefIntentsUI** define o `NSExtensionMainStoryboard` chave `MainInterface`, o **MainInterace.storyboard** arquivo define a interface para a extensão de tentativas da interface do usuário.

Nesse storyboard, há um controlador de exibição única, do tipo **IntentViewController**. Ele faz referência a dois modos de exibição:

- **invoiceView**, do tipo `InvoiceView`
- **confirmationView**, do tipo `ConfirmOrderView`

> [!NOTE]
> As interfaces **invoiceView** e **confirmationView** são definidos no **Main. Storyboard** como modos de exibição secundários. O iOS Designer no Visual Studio para Mac e Visual Studio 2017 não oferece suporte para exibir ou editar modos de exibição secundários; Para fazer isso, abra **Main. Storyboard** no Interface Builder do Xcode.

`IntentViewController` implementa o [`IINUIHostedViewControlling`](xref:IntentsUI.IINUIHostedViewControlling)
interface usada para fornecer uma interface personalizada ao trabalhar com as intenções do Siri. O [`ConfigureView`](xref:IntentsUI.INUIHostedViewControlling_Extensions.ConfigureView*)
método é chamado para personalizar a interface, exibindo a confirmação ou a nota fiscal, dependendo se a interação é que está sendo confirmada ([`INIntentHandlingStatus.Ready`](xref:Intents.INIntentHandlingStatus)) ou foi executado com êxito ([ `INIntentHandlingStatus.Success`](xref:Intents.INIntentHandlingStatus)):

```csharp
[Export("configureViewForParameters:ofInteraction:interactiveBehavior:context:completion:")]
public void ConfigureView(
    NSSet<INParameter> parameters,
    INInteraction interaction,
    INUIInteractiveBehavior interactiveBehavior,
    INUIHostedViewContext context,
    INUIHostedViewControllingConfigureViewHandler completion)
{
    // ...
    if (interaction.IntentHandlingStatus == INIntentHandlingStatus.Ready)
    {
        desiredSize = DisplayInvoice(order, intent);
    }
    else if(interaction.IntentHandlingStatus == INIntentHandlingStatus.Success)
    {
        var response = interaction.IntentResponse as OrderSoupIntentResponse;
        if (!(response is null))
        {
            desiredSize = DisplayOrderConfirmation(order, intent, response);
        }
    }
    completion(true, parameters, desiredSize);
}
```

> [!TIP]
> Para obter mais informações sobre o `ConfigureView` método, assista a apresentação de WWDC 2017 da Apple, [o que há de novo no SiriKit](https://developer.apple.com/videos/play/wwdc2017/214/).

## <a name="creating-a-voice-shortcut"></a>Criar um atalho de voz

Sopa Chef fornece uma interface para atribuir um atalho de voz para cada pedido, tornando possível a sopa de ordem com Siri. Na verdade, a interface usada para registrar e atribuir atalhos de voz é fornecida pelo iOS e exige pouco código personalizado.

Na `OrderDetailViewController`, quando um usuário toca a tabela **adicionar a Siri** linha, o [ `RowSelected` ](xref:UIKit.UITableViewSource.RowSelected*) método apresenta uma tela para adicionar ou editar um atalho de voz:

```csharp
public override void RowSelected(UITableView tableView, NSIndexPath indexPath)
{
    // ...
    else if (TableConfiguration.Sections[indexPath.Section].Type == OrderDetailTableConfiguration.SectionType.VoiceShortcut)
    {
        INVoiceShortcut existingShortcut = VoiceShortcutDataManager?.VoiceShortcutForOrder(Order);
        if (!(existingShortcut is null))
        {
            var editVoiceShortcutViewController = new INUIEditVoiceShortcutViewController(existingShortcut);
            editVoiceShortcutViewController.Delegate = this;
            PresentViewController(editVoiceShortcutViewController, true, null);
        }
        else
        {
            // Since the app isn't yet managing a voice shortcut for
            // this order, present the add view controller
            INShortcut newShortcut = new INShortcut(Order.Intent);
            if (!(newShortcut is null))
            {
                var addVoiceShortcutVC = new INUIAddVoiceShortcutViewController(newShortcut);
                addVoiceShortcutVC.Delegate = this;
                PresentViewController(addVoiceShortcutVC, true, null);
            }
        }
    }
}
```

Com base em se existe ou não um atalho de voz existente para a ordem exibida no momento, `RowSelected` apresenta um controlador de exibição do tipo [ `INUIEditVoiceShortcutViewController` ](xref:IntentsUI.INUIEditVoiceShortcutViewController) ou [ `INUIAddVoiceShortcutViewController` ](xref:IntentsUI.INUIAddVoiceShortcutViewController).
Em cada caso, `OrderDetailViewController` se define como o controlador de exibição `Delegate`, por isso, ele também implementa [`IINUIAddVoiceShortcutViewControllerDelegate`](xref:IntentsUI.IINUIAddVoiceShortcutViewControllerDelegate)
e [ `IINUIEditVoiceShortcutViewControllerDelegate` ](xref:IntentsUI.IINUIEditVoiceShortcutViewControllerDelegate).

## <a name="testing-on-device"></a>Teste no dispositivo

Para executar o Chef sopa em um dispositivo, siga as instruções abaixo. Leia também o [Observação sobre o provisionamento automático](#automatic-provisioning).

### <a name="app-group-app-ids-provisioning-profiles"></a>Grupo de aplicativos, IDs do aplicativo, perfis de provisionamento

No **certificados, IDs e perfis** seção o [Portal do desenvolvedor Apple](https://developer.apple.com/), faça o seguinte:

- Crie um grupo de aplicativos para compartilhar dados entre o aplicativo sopa Chef e suas extensões. Por exemplo: **group.com.yourcompanyname.SoupChef**

- Crie três IDs de aplicativo: um para o aplicativo em si, para a extensão de tentativas e outro para a extensão de tentativas da interface do usuário. Por exemplo:

    - Aplicativo: **com.yourcompanyname.SoupChef**
        - Para essa ID do aplicativo, atribua o SiriKit e **grupos de aplicativos** recursos.

    - Extensão de tentativas: **com.yourcompanyname.SoupChef.Intents**
        - Para essa ID do aplicativo, atribua o **grupos de aplicativos** funcionalidade.

    - Extensão de interface do usuário de tentativas: **com.yourcompanyname.SoupChef.Intentsui**
        - Essa ID do aplicativo não precisa de nenhuma recursos especiais.

- Depois de criar as IDs do aplicativo acima, edite o **grupos de aplicativos** capacidade atribuída ao aplicativo e a extensão de tentativas, especificando o grupo de aplicativos específico criado acima.

- Crie três novos desenvolvimento perfis de provisionamento, uma para cada uma das IDs do novo aplicativo.

- Baixe esse perfis de provisionamento e clique duas vezes em cada um para instalá-lo. Se o Visual Studio para Mac ou Visual Studio 2017 já está em execução, reinicie-o para certificar-se de que ele registra novos perfis de provisionamento.

### <a name="editing-infoplist-entitlementsplist-and-source-code"></a>Edição de código-fonte, Entitlements. plist e info. plist

No Visual Studio para Mac ou Visual Studio 2017, faça o seguinte:

- Atualizar os vários **Info. plist** arquivos na solução. Defina o aplicativo, a extensão de tentativas e a extensão de IU de Intents **identificador de pacote** para as IDs do aplicativo definida acima:

    - Aplicativo: **com.yourcompanyname.SoupChef**
    - Extensão de tentativas: **com.yourcompanyname.SoupChef.Intents**
    - Extensão de interface do usuário de tentativas: **com.yourcompanyname.SoupChef.Intentsui**

- Atualizar o **Entitlements. plist** do arquivo para o **SoupChef** projeto:
    - Para o **grupos de aplicativos** capacidade, defina o grupo para o novo grupo de aplicativos criado acima (no exemplo acima, ele era **group.com.yourcompanyname.SoupChef**).
    - Certifique-se de que **SiriKit** está habilitado.

- Atualizar o **Entitlements. plist** do arquivo para o **SoupChefIntents** projeto:
    - Para o **grupos de aplicativos** capacidade, defina o grupo para o novo grupo de aplicativos criado acima (no exemplo acima, ele era **group.com.yourcompanyname.SoupChef**).

- Por fim, abra **NSUserDefaultsHelper.cs**. Defina a `AppGroup` variável para o valor do seu novo grupo de aplicativos (por exemplo, defina-o `group.com.yourcompanyname.SoupChef`).

### <a name="configuring-the-build-settings"></a>Definindo as configurações de compilação

No Visual Studio para Mac ou Visual Studio 2017:

- Abra as opções/propriedades para o **SoupChef** projeto. Sobre o **assinatura do pacote iOS** guia, defina **identidade de assinatura** como automático e **perfil de provisionamento** para o novo provisionamento de específicos do aplicativo de perfil você criou anteriormente.

- Abra as opções/propriedades para o **SoupChefIntents** projeto. Sobre o **assinatura do pacote iOS** guia, defina **identidade de assinatura** como automático e **perfil de provisionamento** para as tentativas de novo perfil de provisionamento específicas à extensão você criou acima.

- Abra as opções/propriedades para o **SoupChefIntentsUI** projeto. Sobre o **assinatura do pacote iOS** guia, defina **identidade de assinatura** como automático e **perfil de provisionamento** para a nova interface do usuário de tentativas de provisionamento específicas da extensão de perfil você criado acima.

Com essas alterações realizadas, o aplicativo será executado em um dispositivo iOS.

### <a name="automatic-provisioning"></a>O provisionamento automático

Observe que você pode usar [provisionamento automático](https://docs.microsoft.com/xamarin/ios/get-started/installation/device-provisioning/automatic-provisioning) para realizar muitas dessas tarefas diretamente no IDE de provisionamento.
No entanto, o provisionamento automático não configurar grupos de aplicativos. Você precisará configurar manualmente o **Entitlements. plist** arquivos com o nome do grupo de aplicativo que você deseja usar, visite o Portal do desenvolvedor da Apple para criar o grupo de aplicativos, atribuir esse grupo de aplicativo para cada ID do aplicativo criado pelo automático provisionamento, gere novamente os perfis de provisionamento (aplicativo, extensão de tentativas, a extensão de tentativas da interface do usuário) para incluir o grupo de aplicativos criada recentemente e baixar e instalá-los.

## <a name="related-links"></a>Links relacionados

- [Sopa Chef (Xamarin)](https://developer.xamarin.com/samples/monotouch/ios12/SoupChef/)
- [Sopa Chef (Swift)](https://developer.apple.com/documentation/sirikit/accelerating_app_interactions_with_shortcuts?language=objc)
- [SiriKit (Apple)](https://developer.apple.com/sirikit/)
- [Introdução aos atalhos de Siri – WWDC 2018](https://developer.apple.com/videos/play/wwdc2018/211/)
- [Criando para voz com atalhos de Siri – WWDC 2018](https://developer.apple.com/videos/play/wwdc2018/214/)
- [Atalhos de Siri na Face do relógio de Siri – WWDC 2018](https://developer.apple.com/videos/play/wwdc2018/217/)
- [O que há de novo no SiriKit – WWDC 2017](https://developer.apple.com/videos/play/wwdc2017/214/)
- [Criar uma extensão de aplicativo de tentativas (Apple)](https://developer.apple.com/documentation/sirikit/creating_an_intents_app_extension?language=objc)
