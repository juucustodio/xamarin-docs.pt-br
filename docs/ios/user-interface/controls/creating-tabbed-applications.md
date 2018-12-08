---
title: Barras de guia e controladores de barra de guias no xamarin. IOS
description: Este documento descreve os controladores de barra de guia de iOS e como usá-los com o xamarin. IOS. Ele demonstra como configurar um UITabBarController, trabalhar com imagens, defina valores de notificação, trabalhar com eventos e muito mais.
ms.prod: xamarin
ms.assetid: 7C772899-2900-F139-D642-F3C4F3F14DDC
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/21/2017
ms.openlocfilehash: 6f5af4cea0c223b9ddc7cd72331676f5f92f2509
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/07/2018
ms.locfileid: "53055191"
---
# <a name="tab-bars-and-tab-bar-controllers-in-xamarinios"></a>Barras de guia e controladores de barra de guias no xamarin. IOS

Aplicativos com guias são usados no iOS para dar suporte a interfaces do usuário em várias telas podem ser acessadas em nenhuma ordem específica. Por meio de `UITabBarController` classe, aplicativos podem incluir facilmente o suporte para esses cenários com várias telas. `UITabBarController` cuida do gerenciamento com várias tela, permitindo que o desenvolvedor do aplicativo para se concentrar nos detalhes de cada tela.

Normalmente, os aplicativos com guias são criados com o `UITabBarController` sendo o `RootViewController` da janela principal. No entanto, com um pouco de código adicional, com guias aplicativos também podem ser usados em sucessão para alguma outra tela inicial, como o cenário em que um aplicativo pela primeira vez apresenta uma tela de logon, seguida pela interface com guias.

Vamos examinar usando guias aqui, apresentando um passo a passo de um aplicativo simples. Em seguida, veremos como trabalhar com guias no não `RootViewController` cenário.

## <a name="introducing-uitabbarcontroller"></a>Introdução ao UITabBarController

O `UITabBarController` dá suporte a com guias de desenvolvimento de aplicativos a seguir:

-  Permitindo que vários controladores a ser adicionado a ele.
-  Fornecendo uma interface com guias do usuário por meio de `UITabBar` classe, para permitir que um usuário alterne entre controladores e as exibições. 


Controladores serão adicionados à `UITabBarController` por meio de seu `ViewControllers` propriedade, que é um `UIViewController` matriz. O `UITabBarController` em si lida com o controlador apropriado de carregamento e apresentando sua exibição com base no guia selecionada.

As guias são instâncias do `UITabBarItem` classe, que estão contidos em um `UITabBar` instância. Cada `UITabBar` instância está acessível por meio de `TabBarItem` propriedade do controlador em cada guia.

Para obter uma compreensão de como trabalhar com o `UITabBarController`, vamos percorrer criando um aplicativo simples que usa um.

## <a name="tabbed-application-walkthrough"></a>Instruções passo a passo do aplicativo com guias

Para este passo a passo, vamos criar o aplicativo a seguir:

[![](creating-tabbed-applications-images/00-app.png "Exemplo de aplicativo com guias")](creating-tabbed-applications-images/00-app.png#lightbox)

Embora já existe um modelo de aplicativo com guias disponíveis no Visual Studio para Mac, para este exemplo, vamos trabalhar a partir de um projeto vazio para obter uma melhor compreensão de como o aplicativo foi criado.

 <a name="Creating_the_Application" />


### <a name="creating-the-application"></a>Criando o aplicativo

Vamos começar criando um novo aplicativo.

Selecione o **arquivo > Novo > solução** item de menu no Visual Studio para Mac e selecione um **iOS > aplicativo > projeto vazio** modelo, nomeie o projeto `TabbedApplication`, conforme mostrado abaixo:

[![](creating-tabbed-applications-images/newsolution1.png "Selecione o modelo de projeto vazio")](creating-tabbed-applications-images/newsolution1.png#lightbox)

[![](creating-tabbed-applications-images/newsolution2.png "Nomeie o projeto TabbedApplication")](creating-tabbed-applications-images/newsolution2.png#lightbox)



### <a name="adding-the-uitabbarcontroller"></a>Adicionando o UITabBarController

Em seguida, adicione uma classe vazia, selecionando **arquivo > novo arquivo** e escolhendo a **gerais: classe vazia** modelo. Nomeie o arquivo `TabController` conforme mostrado abaixo:

[![](creating-tabbed-applications-images/02-newclass.png "Adicione a classe TabController")](creating-tabbed-applications-images/02-newclass.png#lightbox)

O `TabController` classe conterá a implementação do `UITabBarController` que irá gerenciar uma matriz de `UIViewControllers`. Quando o usuário seleciona uma guia, o `UITabBarController` se encarregará de apresentar o modo de exibição para o controlador de exibição apropriado.

Para implementar o `UITabBarController` que precisamos fazer o seguinte:

1.  Definir a classe base `TabController` para `UITabBarController` . 
1.  Crie `UIViewController` instâncias devem ser adicionadas para o `TabController` . 
1.  Adicionar o `UIViewController` instâncias para uma matriz atribuída para o `ViewControllers` propriedade do `TabController` . 


Adicione o seguinte código para o `TabController` classe para obter estas etapas:

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

Observe que para cada `UIViewController` instância, definimos o `Title` propriedade do `UIViewController`. Quando os controladores são adicionados à `UITabBarController`, o `UITabBarController` lerá o `Title` para cada controlador e exibi-lo no rótulo da guia associado, conforme mostrado abaixo:

[![](creating-tabbed-applications-images/00-app.png "A execução do aplicativo de exemplo")](creating-tabbed-applications-images/00-app.png#lightbox)

#### <a name="setting-the-tabcontroller-as-the-rootviewcontroller"></a>Configurando o TabController como o RootViewController

A ordem que os controladores são colocados nas guias corresponde à ordem em que eles são adicionados à `ViewControllers` matriz.

Para obter o `UITabController` para carregar como a primeira tela, precisamos torná-lo da janela `RootViewController`, conforme mostrado no código a seguir para o `AppDelegate`:

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

Se executarmos o aplicativo agora, o `UITabBarController` será carregado com a primeira guia selecionada por padrão. A seleção de qualquer uma das outras guias do controlador associado da exibição dos resultados que está sendo apresentado pelo `UITabBarController,` conforme mostrado abaixo em que o usuário final tiver selecionado a segunda guia:

[![](creating-tabbed-applications-images/03-secondtab.png "A segunda guia mostrada")](creating-tabbed-applications-images/03-secondtab.png#lightbox)

 <a name="Modifying_TabBarItems" />


### <a name="modifying-tabbaritems"></a>Modificando TabBarItems

Agora que temos um execução guia aplicativo, vamos modificar o `TabBarItem` para alterar a imagem e texto que é exibido, bem como para adicionar uma notificação para uma das guias.

 <a name="Setting_a_System_Item" />


#### <a name="setting-a-system-item"></a>Definir um Item do sistema

Primeiro, vamos definir a primeira guia para usar um item do sistema. No construtor do `TabController`, remova a linha que define o controlador `Title` para o `tab1` da instância e substituí-lo com o código a seguir para definir o controlador `TabBarItem` propriedade:

```csharp
tab1.TabBarItem = new UITabBarItem (UITabBarSystemItem.Favorites, 0);
```

Ao criar o `UITabBarItem` usando um `UITabBarSystemItem`, o título e a imagem são fornecidas automaticamente pelo iOS, como visto na captura de tela abaixo mostrando a **Favoritos** ícone e o título na primeira guia:

 ![](creating-tabbed-applications-images/04a-tabimage.png "A primeira guia com um ícone de estrela")

 <a name="Setting_the_Title_and_Image" />


#### <a name="setting-the-title-and-image"></a>Definindo o título e a imagem

Além de usar um item do sistema, o título e a imagem de um `UITabBarItem` pode ser definida com valores personalizados. Por exemplo, alterar o código que define o `TabBarItem` propriedade do controlador chamado `tab2` da seguinte maneira:

```csharp
tab2 = new UIViewController ();
tab2.TabBarItem = new UITabBarItem ();
tab2.TabBarItem.Image = UIImage.FromFile ("second.png");
tab2.TabBarItem.Title = "Second";
tab2.View.BackgroundColor = UIColor.Orange;
```

O código acima pressupõe que uma imagem chamada `second.png` foi adicionado à raiz do projeto no Visual Studio para Mac. Na verdade, adicionamos três imagens ao nosso projeto, para cobrir todas as resoluções de dispositivo, conforme mostrado abaixo:

 [![](creating-tabbed-applications-images/tabbedimages7new.png "As imagens adicionadas ao projeto")](creating-tabbed-applications-images/tabbedimages7new.png#lightbox)

A imagem de guia deve ser um arquivo png de 30x30 com transparência para a resolução normal, 60 x 60 de alta resolução e 90 x 90 para iPhone 6 Plus resolução. Em nosso código, só precisamos carregar o arquivo chamado `second.png` e iOS carregará automaticamente a alta resolução um em dispositivos com uma tela retina. Você pode ler mais sobre isso na [trabalhando com imagens](~/ios/app-fundamentals/images-icons/index.md) guias. Por padrão, itens da barra de guia são cinza, com uma tonalidade azul quando selecionado.

**Observação**

As imagens acima também podem ser adicionadas para o **recursos** diretório, que é um diretório especial cujo conteúdo será copiado automaticamente para a raiz do pacote do aplicativo:

[![](creating-tabbed-applications-images/tabbedapplication8.png "As imagens como recursos")](creating-tabbed-applications-images/tabbedapplication8.png#lightbox)

Além disso, quando definimos os `Title` propriedade diretamente na `TabBarItem`, ele substituiria qualquer valor definido para `Title` no controlador em si.

Ao executarmos o aplicativo agora, a segunda guia mostra nosso título personalizado e a imagem conforme mostrado abaixo:

[![](creating-tabbed-applications-images/05-customtab.png "A segunda guia com um ícone quadrado")](creating-tabbed-applications-images/05-customtab.png#lightbox)

 <a name="Setting_the_Badge_Value" />


#### <a name="setting-the-badge-value"></a>Definindo o valor de notificação

Uma guia também pode exibir uma notificação. Por exemplo, adicione a seguinte linha de código para definir uma notificação na terceira guia:

```csharp
tab3.TabBarItem.BadgeValue = "Hi";
```

Executando isso resulta em uma etiqueta vermelha com a cadeia de caracteres "Olá" no canto superior esquerdo da guia, conforme mostrado abaixo:

[![](creating-tabbed-applications-images/06-badge.png "A segunda guia com uma notificação de Olá")](creating-tabbed-applications-images/06-badge.png#lightbox)

O selo geralmente é usado para exibir uma indicação de número não lida, novos itens. Para remover o selo, defina o `BadgeValue` como nula, como mostrado abaixo:

```csharp
tab3.TabBarItem.BadgeValue = null;
```

 <a name="Tabs_in_Non-RootViewController_Scenarios" />


## <a name="tabs-in-non-rootviewcontroller-scenarios"></a>Guias em cenários de não-RootViewController

No exemplo acima, mostramos como trabalhar com um `UITabBarController` quando ele é o `RootViewController` da janela. Neste exemplo, vamos examinar como usar um `UITabBarController` quando não é o `RootViewController` e mostram como isso é criado usar Storyboards.

 <a name="Initial_Screen_Example" />


### <a name="initial-screen-example"></a>Exemplo da tela inicial

Para este cenário, a tela inicial é carregada de um controlador que não seja um `UITabBarController`. Quando o usuário interage com a tela tocando em um botão, o mesmo controlador de exibição será carregado em um `UITabBarController`, que é então apresentado ao usuário. Captura de tela a seguir mostra o fluxo do aplicativo:

[![](creating-tabbed-applications-images/inital-screen-application.png "Esta captura de tela mostra o fluxo do aplicativo")](creating-tabbed-applications-images/inital-screen-application.png#lightbox)

Vamos começar um novo aplicativo para este exemplo. Novamente, vamos usar o **iPhone > aplicativo > projeto vazio (C#)** modelo, desta vez de nomear o projeto `InitialScreenDemo`.


Neste exemplo, precisaremos de um Storyboard para manter nossos controladores de exibição. Para adicionar um Storyboard:

- Clique com botão direito no nome do projeto e selecione **Adicionar > novo arquivo**.

- Quando for exibida a caixa de diálogo Novo arquivo, navegue até **iOS > esvaziar Storyboard do iPhone**.

Vamos chamar esse novo Storyboard **MainStoryboard** , conforme ilustrado abaixo: 

[![](creating-tabbed-applications-images/new-file-dialog.png "Adicionar um arquivo MainStoryboard ao projeto")](creating-tabbed-applications-images/new-file-dialog.png#lightbox)

Há algumas etapas importantes a observar ao adicionar um Storyboard em um arquivo anteriormente não são do storyboard, que são abordados os [Introdução ao Storyboards](~/ios/user-interface/storyboards/index.md) guia. Elas são:

 
1. Adicionar seu nome do Storyboard para o **Interface principal** seção o `Info.plist`:

    [![](creating-tabbed-applications-images/project-options.png "Definir a Interface principal para MainStoryboard")](creating-tabbed-applications-images/project-options.png#lightbox)
1. No seu `App Delegate`, substitua o método de janela, com o código a seguir:

    ```csharp
    public override UIWindow Window {
        get;
        set;
    }
    ```

Vamos precisar de três controladores de exibição para este exemplo. Um, chamado `ViewController1`, será usado como nosso controlador de exibição inicial e na primeira guia. Os outros dois, denominados `ViewController2` e `ViewController3`, que será usado nas guias da segunda e terceira, respectivamente.

Abra o Designer, o arquivo Mainstoryboard de clicando duas vezes e arraste três controladores de exibição para a superfície de design. Queremos que cada um desses controladores de exibição para ter sua própria classe correspondente ao nome acima, então, sob **identidade > classe**, digite seu nome, conforme ilustrado na captura de tela abaixo:

[![](creating-tabbed-applications-images/class-name.png "Defina a classe para ViewController1")](creating-tabbed-applications-images/class-name.png#lightbox)

O Visual Studio para Mac gerará automaticamente as classes e os arquivos de designer necessários, isso pode ser visto no painel de soluções, conforme ilustrado abaixo:

[![](creating-tabbed-applications-images/solution-pad2.png "Arquivos gerados automaticamente no projeto")](creating-tabbed-applications-images/solution-pad2.png#lightbox)

 <a name="Creating_the_UI" />


#### <a name="creating-the-ui"></a>Criando a interface do usuário

Em seguida, vamos criar uma interface do usuário simples para cada um dos modos de exibição do ViewController, usando o Designer do iOS Xamarin.

Queremos arrastar uma `Label` e uma `Button` em ViewController1 do **caixa de ferramentas** no lado direito. Em seguida, usaremos o painel de propriedades para editar o nome e o texto dos controles para o seguinte:

-  **Rótulo** : `Text`  =  **um**
-  **Botão** : `Title`  =  **usuário executa alguma ação inicial**


Podemos estar controlando a visibilidade do nosso botão em um `TouchUpInside` precisa de evento e podemos fazer referência a ele no code-behind. Vamos identificá-lo com o **nome** `aButton` no painel de propriedades, conforme ilustrado na captura de tela a seguir:

[![](creating-tabbed-applications-images/abutton-properties.png "Defina o nome como aButton no painel de propriedades")](creating-tabbed-applications-images/abutton-properties.png#lightbox)

A superfície de Design agora deve ser semelhante à captura de tela abaixo:

[![](creating-tabbed-applications-images/design-surface1.png "A superfície de Design agora deve ser semelhante a esta captura de tela")](creating-tabbed-applications-images/design-surface1.png#lightbox)

Vamos adicionar um pouco mais detalhadamente a `ViewController2` e `ViewController3`, adicionando um rótulo para cada um e, em seguida, alterar o texto para 'Dois' e 'Três', respectivamente. Isso destaca ao usuário qual exibição/guia estamos observando.

#### <a name="wiring-up-the-button"></a>Conectando o botão

Vamos carregar `ViewController1` quando o aplicativo é iniciado pela primeira vez. Quando o usuário toca no botão, vamos ocultar o botão e carregar um `UITabBarController` com o `ViewController1` instância na primeira guia.

Quando o usuário libera o `aButton`, queremos que um evento TouchUpInside seja disparado. Vamos selecionar o botão e, na **guia eventos** do painel de propriedades, declare o manipulador de eventos – `InitialActionCompleted` – portanto, ele pode ser chamado no código. Isso é ilustrado na captura de tela abaixo:

[![](creating-tabbed-applications-images/event-handler.png "Quando o usuário libera o aButton, disparar um evento de TouchUpInside")](creating-tabbed-applications-images/event-handler.png#lightbox)

Agora, precisamos informar o controlador de exibição para ocultar o botão quando o evento é acionado `InitialActionCompleted`. No `ViewController1`, adicione o seguinte método parcial:

```csharp
partial void InitialActionCompleted (UIButton sender)
{
    aButton.Hidden = true;  
}
```

Salve o arquivo e execute o aplicativo. Veremos um aparecer de tela e o botão desaparecem na Retocar.

#### <a name="adding-the-tab-bar-controller"></a>Adição da barra de guia controlador

Agora temos nosso modo de exibição inicial funcionando conforme o esperado. Em seguida, queremos adicioná-lo a um `UITabBarController`, junto com exibições 2 e 3. Vamos abrir o Storyboard no Designer.

No **caixa de ferramentas**, pesquise o **controlador de barra de guia** em controladores de & objetos e arraste isso para a superfície de Design. Como você pode ver na captura de tela abaixo, o controlador de barra de guia está sem interface do usuário e, portanto, oferece dois controladores de exibição com ele por padrão:

[![](creating-tabbed-applications-images/tabbarcontroller.png "Adicionando um controlador de barra de guia para o layout")](creating-tabbed-applications-images/tabbarcontroller.png#lightbox)

Exclua esses novos controladores de exibição selecionando a barra preta na parte inferior e pressionando a tecla delete.

No nosso Storyboard, podemos usar transições para lidar com as transições entre a TabBarController e nossos controladores de exibição. Depois de interagir com a exibição inicial, queremos carregá-lo no TabBarController apresentada ao usuário. Vamos configurar isso no designer.

**CTRL + clique** e **arraste** do botão para o TabBarController. Sobre o mouse para cima, será exibido um menu de contexto. Queremos usar um segue modal. 
 
Para configurar cada um dos nossos guias **Ctrl + clique** do TabBarController a cada um dos nossos controladores de exibição de ordem de um a três e selecione a relação **guia** no menu de contexto, conforme ilustrado abaixo:

[![](creating-tabbed-applications-images/context-menu.png "Selecione a guia relação")](creating-tabbed-applications-images/context-menu.png#lightbox)

O Storyboard deve ser semelhante a captura de tela abaixo:

[![](creating-tabbed-applications-images/segue-layout.png "O Storyboard deve se parecer com esta captura de tela")](creating-tabbed-applications-images/segue-layout.png#lightbox)

Se clicar em um dos itens da barra de guia e explore o painel Propriedades, você pode ver um número de opções diferentes, conforme ilustrado abaixo:

[![](creating-tabbed-applications-images/properties-panel.png "Definindo as opções da guia no Gerenciador de propriedades")](creating-tabbed-applications-images/properties-panel.png#lightbox)

Podemos usar isso para editar certos atributos, como a notificação, o título e o iOS [identificador](https://developer.apple.com/library/ios/documentation/userexperience/conceptual/UIKitUICatalog/TabBarItem.html), entre outros

Se salvar e executar o aplicativo agora, podemos encontrará que o botão reaparece quando a instância de ViewController1 é carregada no TabBarController. Vamos corrigir isso para verificar se o modo de exibição atual tem um controlador de exibição de pai. Se isso acontecer, sabemos que estamos dentro de TabBarController e, portanto, o botão deve ser ocultado. Vamos adicionar o código a seguir à classe ViewController1:

```csharp
public override void ViewDidLoad ()
{
    if (ParentViewController != null){
        aButton.Hidden = true;
    }
}
```

Quando o aplicativo é executado e o usuário toca no botão na primeira tela, o UITabBarController é carregado, com a exibição na tela de primeiro colocada na primeira guia, conforme mostrado abaixo:

[![](creating-tabbed-applications-images/first-view.png "O exemplo de saída do aplicativo")](creating-tabbed-applications-images/first-view.png#lightbox)

<!--Save the files and run the application:

[![](creating-tabbed-applications-images/inital-screen-application.png "Save the files and run the application")](creating-tabbed-applications-images/inital-screen-application.png#lightbox)-->

## <a name="summary"></a>Resumo

Este artigo abordou como usar um `UITabBarController` em um aplicativo. Percorremos como carregar controladores em cada guia, bem como definir propriedades nas guias tais título, imagem e notificação. Em seguida, examinamos, uso de storyboards, como carregar um `UITabBarController` em tempo de execução quando não for o `RootViewController` da janela.


## <a name="related-links"></a>Links relacionados

- [Criando aplicativos com guias (amostra)](https://developer.xamarin.com/samples/monotouch/CreatingTabbedApplications/)
- [Images.zip](https://github.com/xamarin/ios-samples/blob/master/CreatingTabbedApplications/Resources/images.zip?raw=true)
- [Referência de classe UITabBarController](http://developer.apple.com/library/ios/#documentation/uikit/reference/UITabBarController_Class/Reference/Reference.html)
