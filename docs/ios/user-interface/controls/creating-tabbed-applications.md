---
title: Barras de guias e controladores de barra de guias no Xamarin. iOS
description: Este documento descreve os controladores da barra de guias do iOS e como usá-los com o Xamarin. iOS. Ele demonstra como configurar um UITabBarController, trabalhar com imagens, definir valores de notificação, trabalhar com eventos e muito mais.
ms.prod: xamarin
ms.assetid: 7C772899-2900-F139-D642-F3C4F3F14DDC
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/21/2017
ms.openlocfilehash: 9bd048239c404c0eb3309fdc74b26bcb94db4740
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91434128"
---
# <a name="tab-bars-and-tab-bar-controllers-in-xamarinios"></a>Barras de guias e controladores de barra de guias no Xamarin. iOS

Os aplicativos com guias são usados no iOS para dar suporte a interfaces de usuário em que várias telas podem ser acessadas em nenhuma ordem específica. Por meio da `UITabBarController` classe, os aplicativos podem facilmente incluir suporte para esses cenários de várias telas. `UITabBarController` cuida do gerenciamento de várias telas, permitindo que o desenvolvedor do aplicativo se concentre nos detalhes de cada tela.

Normalmente, os aplicativos com guias são criados com o `UITabBarController` que está sendo a `RootViewController` janela principal. No entanto, com um pouco de código adicional, os aplicativos com guias também podem ser usados sucessivamente em alguma outra tela inicial, como o cenário em que um aplicativo apresenta primeiro uma tela de logon, seguida pela interface com guias.

Esta página aborda os dois cenários: quando as guias estão na raiz da hierarquia de exibição do aplicativo e também no `RootViewController` cenário não.

## <a name="introducing-uitabbarcontroller"></a>Apresentando o UITabBarController

O `UITabBarController` oferece suporte ao desenvolvimento de aplicativos com guias pelo seguinte:

- Permitir que vários controladores sejam adicionados a ele.
- Fornecer uma interface do usuário com guias, por meio da  `UITabBar` classe, para permitir que um usuário alterne entre os controladores e suas exibições.

Os controladores são adicionados ao `UITabBarController` por meio de sua `ViewControllers` propriedade, que é uma `UIViewController` matriz. O `UITabBarController` próprio identificador carrega o controlador apropriado e apresenta sua exibição com base na guia selecionada.

As guias são instâncias da `UITabBarItem` classe, que estão contidas em uma `UITabBar` instância do. Cada `UITabBar` instância pode ser acessada por meio da `TabBarItem` Propriedade do controlador em cada guia.

Para entender como trabalhar com o `UITabBarController` , vamos examinar a criação de um aplicativo simples que usa um.

## <a name="tabbed-application-walkthrough"></a>Instruções do aplicativo com guias

Neste tutorial, vamos criar o seguinte aplicativo:

[![Exemplo de aplicativo com guias](creating-tabbed-applications-images/00-app.png)](creating-tabbed-applications-images/00-app.png#lightbox)

Embora já exista um modelo de aplicativo com guias disponível em Visual Studio para Mac, neste exemplo, essas instruções funcionam de um projeto vazio para obter uma melhor compreensão de como o aplicativo é construído.

### <a name="creating-the-application"></a>Criando o aplicativo

Comece criando um novo aplicativo.

Selecione o item de menu **> novo > solução** no Visual Studio para Mac e selecione um **aplicativo de > do IOS > modelo de projeto vazio** , nomeie o projeto `TabbedApplication` , conforme mostrado abaixo:

[![Selecionar o modelo de projeto vazio](creating-tabbed-applications-images/newsolution1.png)](creating-tabbed-applications-images/newsolution1.png#lightbox)

[![Nomeie o projeto TabbedApplication](creating-tabbed-applications-images/newsolution2.png)](creating-tabbed-applications-images/newsolution2.png#lightbox)

### <a name="adding-the-uitabbarcontroller"></a>Adicionando o UITabBarController

Em seguida, adicione uma classe vazia selecionando **arquivo > novo arquivo** e escolhendo o modelo de **classe geral: vazio** . Nomeie o arquivo `TabController` como mostrado abaixo:

[![Adicionar a classe TabController](creating-tabbed-applications-images/02-newclass.png)](creating-tabbed-applications-images/02-newclass.png#lightbox)

A `TabController` classe conterá a implementação do `UITabBarController` que gerenciará uma matriz de `UIViewControllers` . Quando o usuário seleciona uma guia, ele `UITabBarController` cuida da apresentação da exibição do controlador de exibição apropriado.

Para implementar o `UITabBarController` , precisamos fazer o seguinte:

1. Defina a classe base de  `TabController` como  `UITabBarController` .
1. Crie  `UIViewController` instâncias para adicionar ao  `TabController` .
1. Adicione as  `UIViewController` instâncias a uma matriz atribuída à  `ViewControllers` Propriedade do  `TabController` .

Adicione o seguinte código à `TabController` classe para obter estas etapas:

```csharp
using System;
using UIKit;

namespace TabbedApplication {
    public class TabController : UITabBarController {

        UIViewController tab1, tab2, tab3;

        public TabController ()
        {
            tab1 = new UIViewController();
            tab1.Title = "Green";
            tab1.View.BackgroundColor = UIColor.Green;

            tab2 = new UIViewController();
            tab2.Title = "Orange";
            tab2.View.BackgroundColor = UIColor.Orange;

            tab3 = new UIViewController();
            tab3.Title = "Red";
            tab3.View.BackgroundColor = UIColor.Red;

            var tabs = new UIViewController[] {
                tab1, tab2, tab3
            };

            ViewControllers = tabs;
        }
    }
}
```

Observe que, para cada `UIViewController` instância, definimos a `Title` propriedade de `UIViewController` . Quando os controladores forem adicionados ao `UITabBarController` , o lerá `UITabBarController` o `Title` para cada controlador e o exibirá no rótulo da guia associada, conforme mostrado abaixo:

[![A execução do aplicativo de exemplo](creating-tabbed-applications-images/00-app.png)](creating-tabbed-applications-images/00-app.png#lightbox)

#### <a name="setting-the-tabcontroller-as-the-rootviewcontroller"></a>Definindo o TabController como o RootViewController

A ordem em que os controladores são colocados nas guias corresponde à ordem em que são adicionados à `ViewControllers` matriz.

Para que o `UITabController` seja carregado como a primeira tela, precisamos torná-lo o da janela `RootViewController` , conforme mostrado no código a seguir para `AppDelegate` :

```csharp
[Register ("AppDelegate")]
public partial class AppDelegate : UIApplicationDelegate
{
    UIWindow window;
    TabController tabController;

    public override bool FinishedLaunching (UIApplication app, NSDictionary options)
    {
        window = new UIWindow (UIScreen.MainScreen.Bounds);

        tabController = new TabController ();
        window.RootViewController = tabController;

        window.MakeKeyAndVisible ();

        return true;
    }
}
```

Se executarmos o aplicativo agora, o `UITabBarController` será carregado com a primeira guia selecionada por padrão. A seleção de qualquer uma das outras guias faz com que o modo de exibição do controlador associado seja apresentado pelo `UITabBarController,` conforme mostrado abaixo, em que o usuário final selecionou a segunda guia:

![A segunda guia mostrada](creating-tabbed-applications-images/03-secondtab-sml.png)

### <a name="modifying-tabbaritems"></a>Modificando TabBarItems

Agora que temos um aplicativo de guia em execução, vamos modificar o `TabBarItem` para alterar a imagem e o texto que é exibido, bem como adicionar uma notificação a uma das guias.

#### <a name="setting-a-system-item"></a>Configurando um item do sistema

Primeiro, vamos definir a primeira guia para usar um item do sistema. No construtor do `TabController` , remova a linha que define o controlador da `Title` `tab1` instância e substitua-o pelo código a seguir para definir a propriedade do controlador `TabBarItem` :

```csharp
tab1.TabBarItem = new UITabBarItem (UITabBarSystemItem.Favorites, 0);
```

Ao criar o `UITabBarItem` usando um `UITabBarSystemItem` , o título e a imagem são fornecidos automaticamente pelo Ios, como visto na captura de tela abaixo, mostrando o ícone de **favoritos** e o título na primeira guia:

 ![A primeira guia com um ícone de estrela](creating-tabbed-applications-images/04a-tabimage-sml.png)

#### <a name="setting-the-image"></a>Configurando a imagem

Além de usar um item do sistema, o título e a imagem de um `UITabBarItem` podem ser definidos como valores personalizados. Por exemplo, altere o código que define a `TabBarItem` Propriedade do controlador chamado da `tab2` seguinte maneira:

```csharp
tab2 = new UIViewController ();
tab2.TabBarItem = new UITabBarItem ();
tab2.TabBarItem.Image = UIImage.FromFile ("second.png");
tab2.TabBarItem.Title = "Second";
tab2.View.BackgroundColor = UIColor.Orange;
```

O código acima pressupõe que uma imagem chamada `second.png` foi adicionada à raiz do projeto (ou a um diretório de **recursos** ). Para dar suporte a todas as densidades de tela, você precisa de três imagens, conforme mostrado abaixo:

![As imagens adicionadas ao projeto](creating-tabbed-applications-images/tabbedimages7new.png)

Consulte a seção **tamanho do ícone da barra de guias** da [página ícones personalizados da Apple](https://developer.apple.com/design/human-interface-guidelines/ios/icons-and-images/custom-icons/) para obter orientação sobre as dimensões corretas. O tamanho recomendado varia de acordo com o estilo da imagem (circular, quadrado, largo ou alto).

A `Image` propriedade só precisa ser definida como o nome de arquivo **second.png** , o Ios carregará automaticamente os arquivos de resolução mais alta quando necessário. Você pode ler mais sobre isso nos guias [trabalhando com imagens](~/ios/app-fundamentals/images-icons/index.md) . Por padrão, os itens da barra de guias são cinza, com uma tonalidade azul quando selecionado.

#### <a name="overriding-the-title"></a>Substituindo o título

Quando a `Title` propriedade é definida diretamente no `TabBarItem` , ela substituirá qualquer valor definido para `Title` no próprio controlador.

A segunda guia (intermediária) nesta captura de tela mostra um título e uma imagem personalizados:

![A segunda guia com um ícone quadrado](creating-tabbed-applications-images/05-customtab-sml.png)

#### <a name="setting-the-badge-value"></a>Definindo o valor de notificação

Uma guia também pode exibir uma notificação. Por exemplo, adicione a seguinte linha de código para definir um emblema na terceira guia:

```csharp
tab3.TabBarItem.BadgeValue = "Hi";
```

Executar isso resulta em um rótulo vermelho com a cadeia de caracteres "Hi" no canto superior esquerdo da guia, conforme mostrado abaixo:

![A segunda guia com uma notificação de alta](creating-tabbed-applications-images/06-badge-sml.png)

A notificação é geralmente usada para exibir uma indicação de número que não lida, novos itens. Para remover a notificação, defina `BadgeValue` como NULL, conforme mostrado abaixo:

```csharp
tab3.TabBarItem.BadgeValue = null;
```

## <a name="tabs-in-non-rootviewcontroller-scenarios"></a>Guias em cenários não RootViewController

No exemplo acima, mostramos como trabalhar com um `UITabBarController` quando ele é o `RootViewController` da janela. Neste exemplo, examinaremos como usar um `UITabBarController` quando não for o `RootViewController` e mostrar como isso é criado usando storyboards.

### <a name="initial-screen-example"></a>Exemplo de tela inicial

Para esse cenário, a tela inicial é carregada de um controlador que não é um `UITabBarController` . Quando o usuário interage com a tela tocando em um botão, o mesmo controlador de exibição será carregado em um `UITabBarController` , que é apresentado ao usuário. A captura de tela a seguir mostra o fluxo do aplicativo:

[![Esta captura de tela mostra o fluxo do aplicativo](creating-tabbed-applications-images/inital-screen-application.png)](creating-tabbed-applications-images/inital-screen-application.png#lightbox)

Vamos iniciar um novo aplicativo para este exemplo. Novamente, usaremos o **aplicativo > iPhone > modelo de projeto vazio (C#)** , desta vez nomeando o projeto `InitialScreenDemo` .

Neste exemplo, um Storyboard é usado para dispor os controladores de exibição. Para adicionar um storyboard:

- Clique com o botão direito do mouse no nome do projeto e selecione **adicionar > novo arquivo**.

- Quando a caixa de diálogo novo arquivo for exibida, navegue até **iOS > storyboard do iPhone vazio**.

Vamos chamar esse novo storyboard **MainStoryboard** , conforme ilustrado abaixo:

[![Adicionar um arquivo MainStoryboard ao projeto](creating-tabbed-applications-images/new-file-dialog.png)](creating-tabbed-applications-images/new-file-dialog.png#lightbox)

Há algumas etapas importantes a serem observadas ao adicionar um storyboard a um arquivo anterior que não é um storyboard, que é abordado no guia [introdução ao storyboards](~/ios/user-interface/storyboards/index.md) . Eles são:

1. Adicione o nome do storyboard à seção da **interface principal** do `Info.plist` :

    [![Defina a interface principal como MainStoryboard](creating-tabbed-applications-images/project-options.png)](creating-tabbed-applications-images/project-options.png#lightbox)
1. Em seu `App Delegate` , substitua o método de janela pelo seguinte código:

    ```csharp
    public override UIWindow Window {
        get;
        set;
    }
    ```

Vamos precisar de três controladores de exibição para este exemplo. Um, chamado `ViewController1` , será usado como nosso controlador de exibição inicial e na primeira guia. Os outros dois, chamados `ViewController2` e `ViewController3` , que serão usados na segunda e terceira guias, respectivamente.

Abra o Designer clicando duas vezes no arquivo MainStoryboard. Storyboard e arraste três controladores de exibição para a superfície de design. Queremos que cada um desses controladores de exibição tenha sua própria classe correspondente ao nome acima, portanto, em **identidade > classe**, digite seu nome, conforme ilustrado na captura de tela abaixo:

[![Definir a classe como ViewController1](creating-tabbed-applications-images/class-name.png)](creating-tabbed-applications-images/class-name.png#lightbox)

Visual Studio para Mac gerará automaticamente as classes e os arquivos de designer necessários, isso pode ser visto na Painel de Soluções, conforme ilustrado abaixo:

[![Arquivos gerados automaticamente no projeto](creating-tabbed-applications-images/solution-pad2.png)](creating-tabbed-applications-images/solution-pad2.png#lightbox)

#### <a name="creating-the-ui"></a>Criando a interface do usuário

Em seguida, criaremos uma interface do usuário simples para cada uma das exibições do ViewController, usando o Xamarin iOS designer.

Queremos arrastar um `Label` e um `Button` para ViewController1 da **caixa de ferramentas** no lado direito. Em seguida, usaremos o Painel de Propriedades para editar o nome e o texto dos controles para o seguinte:

- **Rótulo** : `Text`  =  **um**
- **Botão** : o `Title`  =  **usuário executa alguma ação inicial**

Iremos controlar a visibilidade do nosso botão em um `TouchUpInside` evento e precisamos consultá-lo no code-behind. Vamos identificá-lo com o **nome** `aButton` na painel de propriedades, conforme descrito na seguinte captura de tela:

[![Defina o nome como aButton no Painel de Propriedades](creating-tabbed-applications-images/abutton-properties.png)](creating-tabbed-applications-images/abutton-properties.png#lightbox)

O Design Surface agora deve ser semelhante à captura de tela abaixo:

[![Seu Design Surface agora deve ser semelhante a esta captura de tela](creating-tabbed-applications-images/design-surface1.png)](creating-tabbed-applications-images/design-surface1.png#lightbox)

Vamos adicionar um pouco mais de detalhes ao `ViewController2` e ao `ViewController3` Adicionar um rótulo a cada um deles e alterar o texto para ' Two ' e ' três ', respectivamente. Isso realça ao usuário qual guia/exibição está sendo observada.

#### <a name="wiring-up-the-button"></a>Conectando o botão

Vamos carregar `ViewController1` quando o aplicativo for iniciado pela primeira vez. Quando o usuário tocar no botão, vamos ocultar o botão e carregar um `UITabBarController` com a `ViewController1` instância na primeira guia.

Quando o usuário libera o `aButton` , queremos que um evento TouchUpInside seja disparado. Vamos selecionar o botão e, na **guia eventos** do painel Propriedades, declare o manipulador de eventos – para que `InitialActionCompleted` ele possa ser referenciado no código. Isso é ilustrado na captura de tela abaixo:

[![Quando o usuário libera o aButton, dispara um evento TouchUpInside](creating-tabbed-applications-images/event-handler.png)](creating-tabbed-applications-images/event-handler.png#lightbox)

Agora, precisamos dizer ao controlador de exibição para ocultar o botão quando o evento for disparado `InitialActionCompleted` . No `ViewController1` , adicione o seguinte método parcial:

```csharp
partial void InitialActionCompleted (UIButton sender)
{
    aButton.Hidden = true;  
}
```

Salve o arquivo e execute o aplicativo. Devemos ver a tela um aparecer e o botão desaparecerá em contato.

#### <a name="adding-the-tab-bar-controller"></a>Adicionando o controlador da barra de guias

Agora temos nossa exibição inicial funcionando conforme o esperado. Em seguida, queremos adicioná-lo a um `UITabBarController` , juntamente com as exibições 2 e 3. Vamos abrir o storyboard no designer.

Na **caixa de ferramentas**, procure o **controlador da barra de guias** em controladores & objetos e arraste-o para a design Surface. Como você pode ver na captura de tela abaixo, o controlador da barra de guias é sem interface do usuário e, portanto, traz dois controladores de exibição com ele por padrão:

[![Adicionando um controlador de barra de guias ao layout](creating-tabbed-applications-images/tabbarcontroller.png)](creating-tabbed-applications-images/tabbarcontroller.png#lightbox)

Exclua esses novos controladores de exibição selecionando a barra preta na parte inferior e pressionando DELETE.

Em nosso storyboard, podemos usar continuações para lidar com as transições entre o TabBarController e os nossos controladores de exibição. Depois de interagir com a exibição inicial, desejamos carregá-la no TabBarController apresentado ao usuário. Vamos configurar isso no designer.

**Ctrl + clique** e **Arraste** do botão para o TabBarController. No mouse, um menu de contexto será exibido. Queremos usar um transição modal.

Para configurar cada uma de nossas guias, **pressione CTRL e clique** em TabBarController para cada um de nossos controladores de exibição na ordem de um para três e selecione a **guia** relação no menu de contexto, conforme ilustrado abaixo:

[![Selecione a relação de tabulação](creating-tabbed-applications-images/context-menu.png)](creating-tabbed-applications-images/context-menu.png#lightbox)

O storyboard deve se parecer com a captura de tela abaixo:

[![O storyboard deve ser semelhante a esta captura de tela](creating-tabbed-applications-images/segue-layout.png)](creating-tabbed-applications-images/segue-layout.png#lightbox)

Se clicarmos em um dos itens da barra de guias e explorarmos o painel Propriedades, você poderá ver várias opções diferentes, conforme ilustrado abaixo:

[![Definindo as opções de guia no Gerenciador de propriedades](creating-tabbed-applications-images/properties-panel.png)](creating-tabbed-applications-images/properties-panel.png#lightbox)

Podemos usar isso para editar determinados atributos, como a notificação, o título e o identificador do iOS, entre outros.

Se salvarmos e executarmos o aplicativo agora, descobriremos que o botão reaparecerá quando a instância ViewController1 for carregada no TabBarController. Vamos corrigir isso verificando se a exibição atual tem um controlador de exibição pai. Se tiver, sabemos que estamos dentro do TabBarController e, portanto, o botão deve estar oculto. Vamos adicionar o código abaixo à classe ViewController1:

```csharp
public override void ViewDidLoad ()
{
    if (ParentViewController != null){
        aButton.Hidden = true;
    }
}
```

Quando o aplicativo é executado e o usuário toca no botão na primeira tela, o UITabBarController é carregado, com a exibição da primeira tela colocada na primeira guia, conforme mostrado abaixo:

[![A saída do aplicativo de exemplo](creating-tabbed-applications-images/first-view-sml.png)](creating-tabbed-applications-images/first-view.png#lightbox)

## <a name="summary"></a>Resumo

Este artigo abordou como usar um `UITabBarController` em um aplicativo. Percorremos como carregar controladores em cada guia, bem como definir propriedades em guias como título, imagem e crachá. Em seguida, examinamos, usando storyboards, como carregar um `UITabBarController` em tempo de execução quando não é o `RootViewController` da janela.

## <a name="related-links"></a>Links relacionados

- [Criando aplicativos com guias (exemplo)](/samples/xamarin/ios-samples/creatingtabbedapplications)
- [Images.zip](https://github.com/xamarin/ios-samples/blob/master/CreatingTabbedApplications/Resources/images.zip?raw=true)
- [Referência de classe UITabBarController](https://developer.apple.com/library/ios/#documentation/uikit/reference/UITabBarController_Class/Reference/Reference.html)