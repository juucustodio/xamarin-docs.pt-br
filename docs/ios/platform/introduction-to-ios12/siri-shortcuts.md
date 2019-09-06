---
title: Atalhos do Siri no Xamarin. iOS
description: Este documento descreve como usar atalhos do Siri no iOS 12. Ele discute NSUserActivities, tentativas personalizadas, atribuição de atalhos de voz, atalhos relevantes e muito mais.
ms.prod: xamarin
ms.assetid: 86424F79-3A7D-436E-927D-9A3267DA333B
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 08/08/2018
ms.openlocfilehash: a2ae80946cb94b6c81b87a88c91cd9bf1706186f
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70291765"
---
# <a name="siri-shortcuts-in-xamarinios"></a>Atalhos do Siri no Xamarin. iOS

No [Ios 10](~/ios/platform/sirikit/index.md), a Apple introduziu o SiriKit, possibilitando a criação de mensagens, chamadas de VoIP, pagamentos, treinamentos, reserva de Rides e aplicativos de pesquisa de fotos que interagem com siri.

No [Ios 11](~/ios/platform/introduction-to-ios11/sirikit.md), o SiriKit ganhou suporte para mais tipos de aplicativos e maior flexibilidade para a personalização da interface do usuário.

o iOS 12 adiciona atalhos do Siri, permitindo que todos os tipos de aplicativos exponham sua funcionalidade ao Siri. O Siri aprende quando determinadas tarefas baseadas em aplicativo são mais relevantes para o usuário e usa esse conhecimento para sugerir ações potenciais por meio de _atalhos_. Tocar em um atalho ou chamá-lo com um comando de voz abrirá um aplicativo ou executará uma tarefa em segundo plano.

Os atalhos devem ser usados para acelerar a capacidade do usuário de realizar uma tarefa comum – em muitos casos, sem abrir o aplicativo em questão.

## <a name="sample-app-soup-chef"></a>Aplicativo de exemplo: Chefe de sopa

Para entender melhor os atalhos do Siri, dê uma olhada no aplicativo de exemplo do [chefe de sopa](https://docs.microsoft.com/samples/xamarin/ios-samples/ios12-soupchef) . A sopa chefe permite que os usuários façam pedidos de um restaurante de sopa imaginário, exibam seu histórico de pedidos e definam frases a serem usadas ao ordenar a sopa interagindo com o Siri.

> [!TIP]
> Antes de testar o chefe de sopa em um dispositivo ou simulador do iOS 12, habilite as duas configurações a seguir, que são úteis ao depurar atalhos:
>
> - No aplicativo **configurações** , habilite o **desenvolvedor > exibir atalhos recentes**.
> - No aplicativo **configurações** , habilite o **desenvolvedor > exibir doações na tela de bloqueio**.
>
> Essas configurações de depuração facilitam a localização de atalhos criados recentemente (em vez de previsto) na tela de bloqueio e na tela de pesquisa do iOS.

Para usar o aplicativo de exemplo:

- Instale e execute o aplicativo de exemplo do chefe de sopa em um [dispositivo](#testing-on-device)ou simulador do IOS 12.
- Clique no **+** botão no canto superior direito para criar um novo pedido.
- Selecione um tipo de sopa, especifique uma quantidade e opções e toque em **fazer ordem**.
- Na tela **histórico do pedido** , toque no pedido criado recentemente para exibir seus detalhes.
- Na parte inferior da tela detalhes do pedido, toque em **Adicionar a Siri**.
- Registre uma frase de voz para associar ao pedido e toque em **concluído**.
- Minimize o chefe de sopa, invoque Siri e faça o pedido novamente usando a frase de voz que você acabou de gravar.
- Depois que o Siri concluir a ordem, abra novamente o sopa chefe e observe que o novo pedido está listado na tela **histórico do pedido** .

O aplicativo de exemplo demonstra como:

- [Use um atalho NSUserActivity para abrir um aplicativo](#using-an-nsuseractivity-shortcut-to-open-an-app).
- [Use um atalho de intenção personalizado para executar uma tarefa](#using-a-custom-intent-shortcut-to-perform-a-task).
- [Forneça uma interface do usuário para uma intenção personalizada](#providing-a-user-interface-for-a-custom-intent).
- [Crie um atalho de voz](#creating-a-voice-shortcut).

## <a name="infoplist-and-entitlementsplist"></a>Info. plist e direitos. plist

Antes de se aprofundar no código do chefe de sopa, dê uma olhada em seus arquivos **info. plist** e **intitulados. plist** .

### <a name="infoplist"></a>Info.plist

O arquivo **info. plist** no projeto **SoupChef** define o **identificador de pacote** como `com.xamarin.SoupChef`. Esse identificador de pacote será usado como um prefixo para os identificadores de pacote das extensões de interface do usuário de tentativas e tentativas discutidas posteriormente neste documento.

O arquivo **info. plist** também contém o seguinte:

```xml
<key>NSUserActivityTypes</key>
<array>
    <string>OrderSoupIntent</string>
    <string>com.xamarin.SoupChef.viewMenu</string>
</array>
```

Esse `NSUserActivityTypes` par chave/valor indica que a sopa chefe sabe como lidar com `OrderSoupIntent`um e [`NSUserActivity`](xref:Foundation.NSUserActivity) com [`ActivityType`](xref:Foundation.NSUserActivity.ActivityType) um de "com. xamarin. SoupChef. viewMenu".

As atividades e as intenções personalizadas passadas para o aplicativo em si, ao contrário de suas extensões, são tratadas `AppDelegate` no [`UIApplicationDelegate`](xref:UIKit.UIApplicationDelegate) (um [`ContinueUserActivity`](xref:UIKit.UIApplicationDelegate.ContinueUserActivity*) pelo método.

### <a name="entitlementsplist"></a>Entitlements.plist

O arquivo **. plist de direitos** no projeto **SoupChef** contém o seguinte:

```xml
<key>com.apple.security.application-groups</key>
<array>
    <string>group.com.xamarin.SoupChef</string>
</array>
<key>com.apple.developer.siri</key>
<true/>
```

Essa configuração indica que o aplicativo usa o grupo de aplicativos "Group. com. xamarin. SoupChef". A extensão do aplicativo **SoupChefIntents** usa esse mesmo grupo de aplicativos, o que permite que os dois projetos compartilhem[`NSUserDefaults`](xref:Foundation.NSUserDefaults)
dados.

A `com.apple.developer.siri` chave indica que o aplicativo interage com siri.

> [!NOTE]
> A configuração de Build do projeto **SoupChef** define **direitos personalizados** para **direitos. plist**.

## <a name="using-an-nsuseractivity-shortcut-to-open-an-app"></a>Usando um atalho NSUserActivity para abrir um aplicativo

Para criar um atalho que abre um aplicativo para exibir um conteúdo específico, crie `NSUserActivity` um e anexe-o ao controlador de exibição da tela que você deseja que o atalho abra.

### <a name="setting-up-an-nsuseractivity"></a>Configurando um NSUserActivity

Na tela do menu, `SoupMenuViewController` cria um `NSUserActivity` e o atribui à propriedade do [`UserActivity`](xref:UIKit.UIResponder.UserActivity) controlador de exibição:

```csharp
public override void ViewDidLoad()
{
    base.ViewDidLoad();
    UserActivity = NSUserActivityHelper.ViewMenuActivity;
}
```

Definir a `UserActivity` propriedade _donate_ a atividade como Siri. Dessa doação, o Siri Obtém informações sobre quando e onde essa atividade é relevante para o usuário e aprende a sugerir isso melhor no futuro.

`NSUserActivityHelper`é uma classe de utilitário incluída na solução **SoupChef** , na biblioteca de classes **SoupKit** . Ele cria um `NSUserActivity` e define várias propriedades relacionadas ao Siri e à pesquisa:

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

- Configuração `EligibleForPrediction` para`true` indicar que Siri pode prever essa atividade e Surface-la como um atalho.
- A [`ContentAttributeSet`](xref:Foundation.NSUserActivity.ContentAttributeSet) matriz é um padrão [`CSSearchableItemAttributeSet`](xref:CoreSpotlight.CSSearchableItemAttributeSet) usado para incluir um `NSUserActivity` nos resultados da pesquisa do Ios.
- [`SuggestedInvocationPhrase`](xref:Foundation.NSUserActivity.SuggestedInvocationPhrase)é uma frase que o Siri irá sugerir para o usuário como uma opção potencial ao atribuir uma frase a um atalho.

### <a name="handling-an-nsuseractivity-shortcut"></a>Manipulando um atalho NSUserActivity

Para manipular um `NSUserActivity` atalho invocado por um usuário, um aplicativo IOS deve substituir `ContinueUserActivity` o método da `AppDelegate` classe, respondendo com base `ActivityType` `NSUserActivity` no campo do objeto passado:

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

Esse método chama `HandleUserActivity`, que localiza o transição na tela de menu e o invoca:

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

### <a name="assigning-a-phrase-to-an-nsuseractivity"></a>Atribuindo uma frase a um NSUserActivity

Para atribuir uma frase a um `NSUserActivity`, abra o aplicativo **configurações** do IOS e escolha **Siri & Pesquisar > meus atalhos**. Em seguida, selecione o atalho (neste caso, "pedido de almoço") e registre uma frase.

Invocar Siri e usar essa frase abrirá o chefe de sopa na tela do menu.

## <a name="using-a-custom-intent-shortcut-to-perform-a-task"></a>Usando um atalho de intenção personalizado para executar uma tarefa

### <a name="defining-a-custom-intent"></a>Definindo uma intenção personalizada

Para fornecer um atalho que permita que um usuário conclua rapidamente uma tarefa específica relacionada ao seu aplicativo, crie uma intenção personalizada. Uma intenção personalizada representa uma tarefa que um usuário pode querer concluir, parâmetros relevantes para essa tarefa e respostas potenciais resultantes da execução da tarefa. Dependendo de como uma intenção personalizada é definida, chamá-la pode abrir seu aplicativo ou executar uma tarefa em segundo plano.

Use o Xcode 10 para criar tentativas personalizadas. No [repositório SoupChef](https://github.com/xamarin/ios-samples/tree/master/ios12/SoupChef), a intenção personalizada é definida em **OrderSoupIntentCodeGen**, um projeto Objective-C. Abra este projeto e selecione o arquivo **retenções. intentdefinition** no navegador de **projeto** para exibir a intenção de **OrderSoup** .

Observe o seguinte:

- A intenção tem uma **categoria** de **ordem**. Há várias categorias predefinidas que podem ser usadas para tentativas personalizadas; Selecione aquela que mais se aproximará da tarefa que sua intenção personalizada habilitará. Como esse é um aplicativo de ordenação de sopa, o **OrderSoupIntent** usa **Order**.
- A caixa de seleção de **confirmação** indica se Siri deve ou não solicitar confirmação antes de executar a tarefa. Para a tentativa de **sopa de pedido** no chefe de sopa, essa opção é habilitada porque o usuário está fazendo uma compra.
- A seção de **parâmetros** do arquivo. intentdefinition define os parâmetros relevantes para um atalho. Para fazer uma ordem de sopa, o chefe de sopa deve saber o tipo de sopa, sua quantidade e quaisquer opções associadas.
Cada parâmetro tem um tipo; o parâmetro que não pode ser representado por um tipo predefinido é definido como **personalizado**.
- A interface de **tipos de atalho** descreve as várias combinações de parâmetros que o Siri pode usar ao sugerir seu atalho. As seções **título** e **subtítulo** associadas permitem que você defina as mensagens que o Siri usará ao apresentar um atalho sugerido para o usuário.
- A caixa de seleção **dá suporte à execução em segundo plano** deve ser selecionada para qualquer atalho que possa ser executado sem abrir o aplicativo para maior interação do usuário.

### <a name="defining-custom-intent-responses"></a>Definindo respostas de intenção personalizada

O item de **resposta** aninhado abaixo da intenção **OrderSoup** representa as respostas em potencial resultantes de uma ordem de sopa.

Na definição de resposta da intenção de **OrderSoup** , observe o seguinte:

- **As propriedades** de uma resposta podem ser usadas para personalizar a mensagem comunicada de volta ao usuário. A resposta da tentativa **OrderSoup** tem as propriedades **sopa** e **waittime** .
- Os **modelos de resposta** especificam as várias mensagens de êxito e de falha que podem ser usadas para indicar o status após a conclusão da tarefa de uma intenção.
- A caixa de seleção **êxito** deve ser selecionada para respostas que indicam êxito.
- A resposta de sucesso do **OrderSoupIntent** usa as propriedades de **sopa** e de **espera** para fornecer uma mensagem amigável e útil que descreve quando a ordem de sopa estará pronta.

### <a name="generating-code-for-the-custom-intent"></a>Gerando código para a intenção personalizada

A criação do projeto Xcode que contém essa definição de intenção personalizada faz com que o Xcode gere código que possa ser usado para interagir programaticamente com a intenção personalizada e suas respostas.

Para exibir este código gerado:

- Abra **AppDelegate. m**.
- Adicione uma importação ao arquivo de cabeçalho da intenção personalizada:`#import "OrderSoupIntent.h"`
- Em qualquer método na classe, adicione uma referência a `OrderSoupIntent`.
- Clique com o botão `OrderSoupIntent` direito do mouse em e escolha **ir para definição**.
- Clique com o botão direito do mouse no arquivo recentemente aberto, **OrderSoupIntent. h**, e selecione **Mostrar no Finder**.
- Isso abrirá uma janela do **Finder** que contém um arquivo. h e. m contendo o código gerado.

Esse código gerado inclui:

- `OrderSoupIntent`– Uma classe que representa a intenção personalizada.
- `OrderSoupIntentHandling`– Um protocolo que define os métodos que serão usados para confirmar que a intenção deve ser executada e o método que realmente a executa.
- `OrderSoupIntentResponseCode`– Uma enumeração que define vários status de resposta.
- `OrderSoupIntentResponse`– uma classe que representa a resposta à execução de uma intenção.

### <a name="creating-a-binding-to-the-custom-intent"></a>Criando uma associação à intenção personalizada

Para usar o código gerado pelo Xcode em um aplicativo Xamarin. iOS, crie uma C# Associação para ele.

#### <a name="creating-a-static-library-and-c-binding-definitions"></a>Criando uma biblioteca estática e C# definições de associação

No [repositório SoupChef](https://github.com/xamarin/ios-samples/tree/master/ios12/SoupChef), dê uma olhada na pasta **OrderSoupIntentStaticLib** e abra o projeto Xcode **OrderSoupIntentStaticLib. xcodeproj** .

Esse projeto de **biblioteca estática Cocoa Touch** contém os arquivos **OrderSoupIntent. h** e **OrderSoupIntent. m** gerados pelo Xcode.

#### <a name="configuring-the-static-library-project-build-settings"></a>Definindo as configurações de compilação do projeto de biblioteca estática

No navegador de **projeto**do Xcode, selecione o projeto de nível superior, **OrderSoupIntentStaticLib**, e navegue até **fases de compilação > compilar fontes**.
Observe que **OrderSoupIntent. m** (que importa **OrderSoupIntent. h**) está listado aqui. No **link Binary com bibliotecas**, observe que o **retenções. Framework** e o **Foundation. Framework** estão incluídos.
Com essas configurações em vigor, a estrutura será criada corretamente.

#### <a name="building-the-static-library-and-generating-c-bindings-definitions"></a>Criando a biblioteca estática e gerando C# definições de associações

Para criar a biblioteca estática e gerar C# definições de associações para ela, siga estas etapas:

- [Instale a nitidez do objetivo](https://docs.microsoft.com/xamarin/cross-platform/macios/binding/objective-sharpie/get-started?context=xamarin/mac#installing-objective-sharpie), a ferramenta usada para gerar definições de associações dos arquivos. h e. m criados pelo Xcode.

- Configure seu sistema para usar as ferramentas de linha de comando do Xcode 10:

  > [!WARNING]
  > A atualização das ferramentas de linha de comando selecionadas afeta todas as versões instaladas do Xcode no seu sistema. Quando você terminar de usar o aplicativo de exemplo do chefe de sopa, certifique-se de reverter essa configuração para sua configuração original.

  - No Xcode, escolha **Xcode > preferências > locais** e defina **ferramentas de linha de comando** para a instalação mais recente do Xcode 10 disponível no sistema.

- No terminal, `cd` para o diretório **OrderSoupIntentStaticLib**

- Tipo `make`, que compila:

  - A biblioteca estática, **libOrderSoupIntentStaticLib. a**
  - No diretório de saída de Bo C# , definições de associações:
    - **ApiDefinitions.cs**
    - **StructsAndEnums.cs**

O projeto **OrderSoupIntentBindings** , que se baseia nessa biblioteca estática e suas definições de associações associadas, compila esses itens automaticamente.
No entanto, a execução manual por meio do processo acima garantirá que ele seja criado conforme o esperado.

Para obter mais informações sobre como criar uma biblioteca estática e usar a nitidez objetiva C# para criar definições de associações, dê uma olhada na explicação sobre a [Associação de uma biblioteca de Objective do IOS-C](https://docs.microsoft.com/xamarin/ios/platform/binding-objective-c/walkthrough?tabs=vsmac#creating-a-static-library) .

#### <a name="creating-a-bindings-library"></a>Criando uma biblioteca de associações

Com a biblioteca estática e as C# definições de associações criadas, a parte restante necessária para consumir o código relacionado à intenção gerado pelo Xcode em um projeto Xamarin. Ios é uma biblioteca de associações.

No [repositório chefe de sopa](https://github.com/xamarin/ios-samples/tree/master/ios12/SoupChef), abra o arquivo **SoupChef. sln** . Entre outras coisas, essa solução contém **OrderSoupIntentBinding**, uma biblioteca de associações para a biblioteca estática gerada acima.

Observe que, especificamente, esse projeto inclui:

- **ApiDefinitions.cs** – um arquivo gerado acima por nitidez objetiva e adicionado a este projeto. A ação de **Build** deste arquivo está definida como **ObjcBindingApiDefinition**.
- **StructsAndEnums.cs** – outro arquivo gerado acima por nitidez objetiva e adicionado a este projeto. A ação de **Build** deste arquivo está definida como **ObjcBindingCoreSource**.
- Uma **referência nativa** para **libOrderSoupIntentStaticLib.** a, a biblioteca estática criada acima.

> [!NOTE]
> **ApiDefinitions.cs** e **StructsAndEnums.cs** contêm `[Watch (5,0), iOS (12,0)]`atributos como. Esses atributos, gerados por nitidez objetiva, foram comentados, pois não são necessários para este projeto.

Para obter mais informações sobre como C# criar uma biblioteca de associações, dê uma olhada na explicação sobre a [vinculação de uma biblioteca de Objective do IOS-C](https://docs.microsoft.com/xamarin/ios/platform/binding-objective-c/walkthrough?tabs=vsmac#create-a-xamarinios-binding-project) .

Observe que o projeto **SoupChef** contém uma referência a **OrderSoupIntentBinding**, o que significa que agora ele pode acessar, C#no, as classes, interfaces e enumerações que ele contém:

- `OrderSoupIntent`
- `OrderSoupIntentHandling`
- `OrderSoupIntentResponse`
- `OrderSoupIntenseResponseCode`

### <a name="adding-the-intentdefinition-file-to-your-solution"></a>Adicionando o arquivo. intentdefinition à sua solução

C# Na solução **SoupChef** , o projeto **SoupKit** contém o código compartilhado entre o aplicativo e suas extensões. O arquivo **retenções. intentdefinition** foi colocado no diretório **base. lproj** de **SoupKit**e tem uma **ação de compilação** de **conteúdo**. O processo de compilação copia esse arquivo para o grupo de aplicativos de sopa do meio-chefe, onde é necessário que o aplicativo funcione corretamente.

### <a name="donating-an-intent"></a>Doação de uma intenção

Para que o Siri sugira um atalho, ele deve primeiro entender quando o atalho é relevante.

Para dar a Siri essa compreensão, a sopa chefe _donate_ é uma intenção de Siri cada vez que o usuário coloca um pedido de sopa. Com base nessa doação – quando foi donate, em que foi donate, os parâmetros que ele contém – Siri aprende quando sugerir o atalho no futuro.

**SoupChef** usa a `SoupOrderDataManager` classe para inserir doações.
Quando chamado para colocar uma ordem de sopa para um usuário, `PlaceOrder` o método, por [`DonateInteraction`](xref:Intents.INInteraction.DonateInteraction*)sua vez, chama:

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

Depois de buscar uma intenção, ela é encapsulada em [`INInteraction`](xref:Intents.INInteraction)um.
O `INInteraction` recebe um[`Identifier`](xref:Intents.INInteraction.Identifier*)
que corresponde à ID exclusiva da ordem (isso será útil posteriormente ao excluir doações de intenção que não são mais válidas). Em seguida, a interação é donate a siri.

`order.Intent` A chamada para getter busca um `Quantity` `OrderSoupIntent` que representa a ordem definindo sua imagem, `Soup`, `Options`, e uma frase de invocação a ser usada como sugestão quando o usuário registra uma frase para Siri para associar com a intenção:

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

#### <a name="removing-invalid-donations"></a>Removendo doações inválidas

É importante remover doações que não são mais válidas para que o Siri não faça sugestões de atalho não auxiliares ou confusas.

No chefe de sopa, a tela de **menu Configurar** pode ser usada para marcar um item de menu como não disponível. Siri não deve mais sugerir um atalho para solicitar um item de menu indisponível, portanto `RemoveDonation` , o `SoupMenuManager` método de excluir doações para itens de menu que não estão mais disponíveis. Ele faz isso por meio de:

- Localizando pedidos associados ao item de menu Now-inavailable.
- Captando seus identificadores.
- Excluindo interações que têm esse mesmo identificador.

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

O código que é executado quando Siri invoca uma intenção é colocado em uma extensão de intenções, que pode ser adicionada como um novo projeto à mesma solução que um aplicativo Xamarin. iOS existente, como a sopa chefe. Na solução **SoupChef** , a extensão é chamada de **SoupChefIntents**.

#### <a name="soupchefintents-infoplist-and-entitlementsplist"></a>SoupChefIntents – info. plist e direitos. plist

##### <a name="soupchefintents-infoplist"></a>SoupChefIntents – info. plist

O **info. plist** no projeto **SoupChefIntents** define o **identificador de pacote** como `com.xamarin.SoupChef.SoupChefIntents`.

O arquivo **info. plist** também contém o seguinte:

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

No **info. plist**acima:

- `IntentsRestrictedWhileLocked`lista as tentativas que só devem ser tratadas quando o dispositivo for desbloqueado.
- `IntentsSupported`lista as tentativas tratadas por essa extensão.
- `NSExtensionPointIdentifier`Especifica o tipo de extensão do aplicativo (consulte a [documentação da Apple](https://developer.apple.com/library/archive/documentation/General/Reference/InfoPlistKeyReference/Articles/AppExtensionKeys.html#//apple_ref/doc/uid/TP40014212-SW15) para obter mais informações).
- `NSExtensionPrincipalClass`Especifica a classe que deve ser usada para lidar com tentativas com suporte nesta extensão.

##### <a name="soupchefintents-entitlementsplist"></a>SoupChefIntents – direitos. plist

O **direitos. plist** no projeto **SoupChefIntents** tem a capacidade de **grupos de aplicativos** . Essa funcionalidade é configurada para usar o mesmo grupo de aplicativos que o projeto **SoupChef** :

```xml
<key>com.apple.security.application-groups</key>
<array>
    <string>group.com.xamarin.SoupChef</string>
</array>
```

A sopa chefe mantém os dados `NSUserDefaults`com. Para compartilhar dados entre o aplicativo e a extensão do aplicativo, eles fazem referência ao mesmo grupo de aplicativos em seus arquivos **. plist de direitos** .

> [!NOTE]
> A configuração de Build do projeto **SoupChefIntents** define **direitos personalizados** para **direitos. plist**.

#### <a name="handling-an-ordersoupintent-background-task"></a>Manipulando uma tarefa em segundo plano do OrderSoupIntent

Uma extensão de tentativas executa as tarefas em segundo plano necessárias para um atalho com base em uma intenção personalizada.

Siri chama o [`GetHandler`](xref:Intents.INExtension.GetHandler*) método `IntentHandler` da classe (definido em **info. plist** como o `NSExtensionPrincipalClass`) para obter uma instância de uma classe que estende `OrderSoupIntentHandling`, que pode ser usada para lidar com um `OrderSoupIntent`:

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

`OrderSoupIntentHandler`, definido no projeto **SoupKit** do código compartilhado, implementa dois métodos importantes:

- `ConfirmOrderSoup`– Confirma se a tarefa associada à intenção deve ser realmente executada
- `HandleOrderSoup`– Coloca a ordem de sopa e responde ao usuário chamando o manipulador de conclusão passado

#### <a name="handling-an-ordersoupintent-that-opens-the-app"></a>Manipulando um OrderSoupIntent que abre o aplicativo

Um aplicativo deve lidar corretamente com tentativas que não são executadas em segundo plano.
Eles são tratados da mesma forma que `NSUserActivity` os atalhos, `ContinueUserActivity` no método de `AppDelegate`:

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

## <a name="providing-a-user-interface-for-a-custom-intent"></a>Fornecendo uma interface do usuário para uma intenção personalizada

Uma extensão de interface de usuário de tentativas fornece uma interface de usuário personalizada para uma extensão de tentativas. Na solução **SoupChef** , **SoupChefIntentsUI** é uma extensão de interface do usuário de tentativas que fornece uma interface para **SoupChefIntents**.

### <a name="soupchefintentsui--infoplist-and-entitlementsplist"></a>SoupChefIntentsUI – info. plist e direitos. plist

#### <a name="soupchefintentsui-infoplist"></a>SoupChefIntentsUI – info. plist

O **info. plist** no projeto **SoupChefIntentsUI** define o **identificador de pacote** como `com.xamarin.SoupChef.SoupChefIntentsui`.

O arquivo **info. plist** também contém o seguinte:

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

No **info. plist**acima:

- `IntentsSupported`indica que o `OrderSoupIntent` é tratado por essa extensão de interface do usuário de tentativas.
- `NSExtensionPointIdentifier`Especifica o tipo de extensão do aplicativo (consulte a [documentação da Apple](https://developer.apple.com/library/archive/documentation/General/Reference/InfoPlistKeyReference/Articles/AppExtensionKeys.html#//apple_ref/doc/uid/TP40014212-SW15) para obter mais informações).
- `NSExtensionMainStoryboard`Especifica o storyboard que define a interface primária desta extensão

#### <a name="soupchefintentsui-entitlementsplist"></a>SoupChefIntentsUI – direitos. plist

O projeto **SoupChefIntentsUI** não precisa de um arquivo **. plist de direitos** .

### <a name="creating-the-user-interface"></a>Criar a interface do usuário

Como o **info. plist** para **SoupChefIntentsUI** define a `NSExtensionMainStoryboard` chave como `MainInterface`, o arquivo **MainInterace. Storyboard** define a interface para a extensão da interface do usuário de tentativas.

Neste storyboard, há um único controlador de exibição, do tipo **IntentViewController**. Ele faz referência a dois modos de exibição:

- **invoiceView**, do tipo`InvoiceView`
- **confirmationView**, do tipo`ConfirmOrderView`

> [!NOTE]
> As interfaces para **invoiceView** e **confirmationView** são definidas em **Main. Storyboard** como exibições secundárias. O designer do iOS no Visual Studio para Mac e no Visual Studio 2017 não oferece suporte para exibição ou edição de exibições secundárias; para fazer isso, abra **Main. Storyboard** no interface Builder do Xcode.

`IntentViewController`implementa o[`IINUIHostedViewControlling`](xref:IntentsUI.IINUIHostedViewControlling)
interface, usada para fornecer uma interface personalizada ao trabalhar com tentativas de siri. Dos[`ConfigureView`](xref:IntentsUI.INUIHostedViewControlling_Extensions.ConfigureView*)
o método é chamado para personalizar a interface, exibindo a confirmação ou a fatura, dependendo se a interação está sendo confirmada[`INIntentHandlingStatus.Ready`](xref:Intents.INIntentHandlingStatus)() ou se foi executada com êxito[`INIntentHandlingStatus.Success`](xref:Intents.INIntentHandlingStatus)():

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
> Para obter mais informações sobre `ConfigureView` o método, Assista à apresentação do WWDC 2017 da Apple, [o que há de novo no SiriKit](https://developer.apple.com/videos/play/wwdc2017/214/).

## <a name="creating-a-voice-shortcut"></a>Criando um atalho de voz

A sopa chefe fornece uma interface para atribuir um atalho de voz a cada pedido, possibilitando a ordem de sopa com siri. Na verdade, a interface usada para registrar e atribuir atalhos de voz é fornecida pelo iOS e requer pouco código personalizado.

No `OrderDetailViewController`, quando um usuário toca a linha **Add to Siri** da tabela, o [`RowSelected`](xref:UIKit.UITableViewSource.RowSelected*) método apresenta uma tela para adicionar ou editar um atalho de voz:

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

Com base em se um atalho de voz existente existe ou não para a ordem exibida atualmente `RowSelected` , apresenta um controlador de exibição [`INUIEditVoiceShortcutViewController`](xref:IntentsUI.INUIEditVoiceShortcutViewController) do [`INUIAddVoiceShortcutViewController`](xref:IntentsUI.INUIAddVoiceShortcutViewController)tipo ou.
Em cada caso, `OrderDetailViewController` ele se define como o `Delegate`controlador de exibição, que é o motivo pelo qual ele também implementa[`IINUIAddVoiceShortcutViewControllerDelegate`](xref:IntentsUI.IINUIAddVoiceShortcutViewControllerDelegate)
e [`IINUIEditVoiceShortcutViewControllerDelegate`](xref:IntentsUI.IINUIEditVoiceShortcutViewControllerDelegate).

## <a name="testing-on-device"></a>Testando no dispositivo

Para executar o chefe de sopa em um dispositivo, siga as instruções abaixo. Leia também a [Observação sobre o provisionamento automático](#automatic-provisioning).

### <a name="app-group-app-ids-provisioning-profiles"></a>Grupo de aplicativos, IDs de aplicativo, perfis de provisionamento

Na seção **certificados, IDs & perfis** do portal do [desenvolvedor da Apple](https://developer.apple.com/), faça o seguinte:

- Crie um grupo de aplicativos para compartilhar dados entre o aplicativo chefe de sopa e suas extensões. Por exemplo: **Group. com. Yourcompanyname pela. SoupChef**

- Crie três IDs de aplicativo: uma para o aplicativo em si, uma para a extensão de intenções e outra para a extensão de interface do usuário de tentativas. Por exemplo:

  - Aplicativo: **com. Yourcompanyname pela. SoupChef**
    - Para essa ID do aplicativo, atribua os recursos SiriKit e **grupos de aplicativos** .

  - Extensão de tentativas: **com. Yourcompanyname pela. SoupChef. intenções**
    - Para essa ID de aplicativo, atribua a capacidade de **grupos de aplicativos** .

  - Extensão da interface do usuário de tentativas: **com. Yourcompanyname pela. SoupChef. Intentsui**
    - Esta ID de aplicativo não precisa de funcionalidades especiais.

- Depois de criar as IDs de aplicativo acima, edite a funcionalidade de **grupos de aplicativos** atribuída ao aplicativo e a extensão de tentativas, especificando o grupo de aplicativos específico criado acima.

- Crie três novos perfis de provisionamento de desenvolvimento, um para cada um dos novos IDs de aplicativo.

- Baixe esses perfis de provisionamento e clique duas vezes em cada um para instalá-lo. Se Visual Studio para Mac ou o Visual Studio 2017 já estiver em execução, reinicie-o para certificar-se de que ele registra os novos perfis de provisionamento.

### <a name="editing-infoplist-entitlementsplist-and-source-code"></a>Edição do info. plist, direitos. plist e código-fonte

No Visual Studio para Mac ou no Visual Studio 2017, faça o seguinte:

- Atualize os vários arquivos **info. plist** na solução. Defina o **identificador do pacote** do aplicativo, a extensão de tentativas e a extensão da interface do usuário de tentativas para as IDs de aplicativo definidas acima:

  - Aplicativo: **com. Yourcompanyname pela. SoupChef**
  - Extensão de tentativas: **com. Yourcompanyname pela. SoupChef. intenções**
  - Extensão da interface do usuário de tentativas: **com. Yourcompanyname pela. SoupChef. Intentsui**

- Atualize o arquivo **. plist de direitos** para o projeto **SoupChef** :
  - Para o recurso de **grupos de aplicativos** , defina o grupo para o novo grupo de aplicativos criado acima (no exemplo acima, era **Group. com. Yourcompanyname pela. SoupChef**).
  - Verifique se o **SiriKit** está habilitado.

- Atualize o arquivo **. plist de direitos** para o projeto **SoupChefIntents** :
  - Para o recurso de **grupos de aplicativos** , defina o grupo para o novo grupo de aplicativos criado acima (no exemplo acima, era **Group. com. Yourcompanyname pela. SoupChef**).

- Por fim, abra **NSUserDefaultsHelper.cs**. Defina a `AppGroup` variável para o valor do novo grupo de aplicativos (por exemplo, defina-a `group.com.yourcompanyname.SoupChef`como).

### <a name="configuring-the-build-settings"></a>Definindo as configurações de compilação

No Visual Studio para Mac ou no Visual Studio 2017:

- Abra as opções/Propriedades do projeto **SoupChef** . Na guia **assinatura do pacote do IOS** , defina **identidade de assinatura** como automática e perfil de **provisionamento** para o novo perfil de provisionamento específico do aplicativo criado acima.

- Abra as opções/Propriedades do projeto **SoupChefIntents** . Na guia **assinatura do pacote do IOS** , defina **identidade de assinatura** como automática e perfil de **provisionamento** para o novo perfil de provisionamento específico da extensão de tentativas que você criou acima.

- Abra as opções/Propriedades do projeto **SoupChefIntentsUI** . Na guia **assinatura do pacote do IOS** , defina **identidade de assinatura** como automática e perfil de **provisionamento** para o novo perfil de provisionamento específico da extensão de interface do usuário de novas intenções que você criou acima.

Com essas alterações em vigor, o aplicativo será executado em um dispositivo iOS.

### <a name="automatic-provisioning"></a>Provisionamento automático

Observe que você pode usar o [provisionamento automático](https://docs.microsoft.com/xamarin/ios/get-started/installation/device-provisioning/automatic-provisioning) para realizar muitas dessas tarefas de provisionamento diretamente no IDE.
No entanto, o provisionamento automático não configura grupos de aplicativos. Você precisará configurar manualmente os arquivos **. plist de direitos** com o nome do grupo de aplicativos que deseja usar, visitar o portal do desenvolvedor da Apple para criar o grupo de aplicativos, atribuir esse grupo de aplicativos a cada ID de aplicativo criada pelo provisionamento automático, regenerar os perfis de provisionamento (aplicativo, extensão de intenções, extensão de interface do usuário de tentativas) para incluir o grupo de aplicativos recém-criado e baixá-los e instalá-los.

## <a name="related-links"></a>Links relacionados

- [Chefe de sopa (Xamarin)](https://docs.microsoft.com/samples/xamarin/ios-samples/ios12-soupchef)
- [Chefe de sopa (Swift)](https://developer.apple.com/documentation/sirikit/accelerating_app_interactions_with_shortcuts?language=objc)
- [SiriKit (Apple)](https://developer.apple.com/sirikit/)
- [Introdução aos atalhos do Siri – WWDC 2018](https://developer.apple.com/videos/play/wwdc2018/211/)
- [Criando para voz com atalhos de Siri – WWDC 2018](https://developer.apple.com/videos/play/wwdc2018/214/)
- [Atalhos do Siri na superfície do Siri Watch – WWDC 2018](https://developer.apple.com/videos/play/wwdc2018/217/)
- [O que há de novo no SiriKit – WWDC 2017](https://developer.apple.com/videos/play/wwdc2017/214/)
- [Criar uma extensão de aplicativo de tentativas (Apple)](https://developer.apple.com/documentation/sirikit/creating_an_intents_app_extension?language=objc)
