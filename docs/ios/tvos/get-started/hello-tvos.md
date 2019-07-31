---
title: Olá, tvOS Guia de Início Rápido
description: Este guia orienta a criação do seu primeiro aplicativo Xamarin. tvOS e seu ferramentas de desenvolvimento. Ele também apresenta o Xamarin designer, que expõe os controles da interface do usuário ao código e ilustra como compilar, executar e testar um aplicativo Xamarin. tvOS.
ms.prod: xamarin
ms.assetid: 6E0AFE58-A13B-492F-861E-D5D73EB1C4A3
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 02/02/2018
ms.openlocfilehash: 209d8a61e6505e34fdf714b94ba40465f7f6aa4b
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68646368"
---
# <a name="hello-tvos-quick-start-guide"></a>Olá, tvOS Guia de Início Rápido

_Este guia orienta a criação do seu primeiro aplicativo Xamarin. tvOS e seu ferramentas de desenvolvimento. Ele também apresenta o Xamarin designer, que expõe os controles da interface do usuário ao código e ilustra como compilar, executar e testar um aplicativo Xamarin. tvOS._

A Apple lançou a 5ª geração da Apple TV, a Apple TV 4K, que executa o tvOS 11.

A plataforma de TV da Apple está aberta para os desenvolvedores, permitindo que eles criem aplicativos ricos e envolventes e os libere por meio da loja de aplicativos interna da Apple TV.

Se você estiver familiarizado com o desenvolvimento do Xamarin. iOS, deverá encontrar a transição para tvOS bastante simples. A maioria das APIs e recursos são os mesmos, no entanto, muitas APIs comuns não estão disponíveis (como WebKit). Além disso, trabalhar com o com o Siri Remote apresenta alguns desafios de design que não estão presentes em dispositivos iOS baseados em tela de toque.

Este guia apresentará uma introdução ao trabalho com o tvOS em um aplicativo Xamarin. Para obter mais informações sobre o tvOS, consulte a documentação de preparar-se [para a Apple TV 4K](https://developer.apple.com/tvos/) da Apple.

## <a name="overview"></a>Visão geral

O Xamarin. tvOS permite desenvolver aplicativos da Apple TV totalmente nativos no C# e no .NET usando as mesmas bibliotecas do os X e controles de interface que são usados ao desenvolver em *Swift* (ou *Objective-C*) e *Xcode*.

Além disso, como os aplicativos Xamarin. tvOS são C# escritos em e .net, comum, o código de back-end pode ser compartilhado com aplicativos Xamarin. Ios, Xamarin. Android e Xamarin. Mac; tudo isso proporcionando uma experiência nativa em cada plataforma.

Este artigo apresentará os principais conceitos necessários para criar um aplicativo de TV da Apple usando o Xamarin. tvOS e o Visual Studio, orientando você pelo processo de criação de um aplicativo **Hello básico, tvOS** que conta o número de vezes que um botão foi clicado:

[![](hello-tvos-images/run05.png "Exemplo de execução de aplicativo")](hello-tvos-images/run05.png#lightbox)

Abordaremos os seguintes conceitos:

-  **Visual Studio para Mac** – introdução ao Visual Studio para Mac e como criar aplicativos Xamarin. tvOS com ele.
-  **Anatomia de um aplicativo xamarin. tvOS** – o que é composto por um aplicativo Xamarin. tvOS.
-  **Criar uma interface do usuário** – como usar o para Xamarin designer para IOS para criar uma interface do usuário.
-  **Implantação e teste** – como executar e testar seu aplicativo no simulador de tvOS e no hardware tvOS real.

## <a name="starting-a-new-xamarintvos-app-in-visual-studio-for-mac"></a>Iniciando um novo aplicativo Xamarin. tvOS no Visual Studio para Mac

Como mencionado acima, criaremos um aplicativo de TV da Apple `Hello-tvOS` chamado que adiciona um único botão e um rótulo à tela principal. Quando o botão for clicado, o rótulo exibirá o número de vezes que isso ocorreu.

Para começar, vamos fazer o seguinte:

1. Inicie o Visual Studio para Mac:

    [![](hello-tvos-images/setup01.png "Visual Studio para Mac")](hello-tvos-images/setup01.png#lightbox)
2. Clique no link **nova solução...** no canto superior esquerdo da tela para abrir a caixa de diálogo **novo projeto** .
3. Selecione aplicativo de**exibição única** do**aplicativo** >  **tvOS** > e clique no botão **Avançar** :

    [![](hello-tvos-images/setup02.png "Selecionar aplicativo de modo de exibição único")](hello-tvos-images/setup02.png#lightbox)
4. Insira `Hello, tvOS` para o **nome do aplicativo**, insira o **identificador da organização** e clique no botão **Avançar** :

    [![](hello-tvos-images/setup04.png "Insira Olá, tvOS")](hello-tvos-images/setup04.png#lightbox)
5. Insira `Hello_tvOS` para o **nome do projeto** e clique no botão **criar** :

    [![](hello-tvos-images/setup03.png "Insira HellotvOS")](hello-tvos-images/setup03.png#lightbox)

Visual Studio para Mac criará o novo aplicativo Xamarin. tvOS e exibirá os arquivos padrão que são adicionados à solução do seu aplicativo:

 [![](hello-tvos-images/project01.png "A exibição de arquivos padrão")](hello-tvos-images/project01.png#lightbox)

O Visual Studio para Mac usa **soluções** e **projetos**, exatamente da mesma maneira que o Visual Studio. Uma solução é um contêiner que pode conter um ou mais projetos; projetos podem incluir aplicativos, bibliotecas de suporte, aplicativos de teste, etc. Nesse caso, Visual Studio para Mac criou uma solução e um projeto de aplicativo para você.

Se você quisesse, poderia criar um ou mais projetos de biblioteca de código que contêm código comum e compartilhado. Esses projetos de biblioteca podem ser consumidos pelo projeto de aplicativo ou compartilhados com outros projetos de aplicativo Xamarin. tvOS (ou Xamarin. iOS, Xamarin. Android e Xamarin. Mac com base no tipo de código), assim como você faria se estivesse criando um aplicativo .NET padrão.

## <a name="anatomy-of-a-xamarintvos-app"></a>Anatomia de um aplicativo Xamarin. tvOS

Se você estiver familiarizado com a programação do iOS, observará muitas semelhanças aqui. Na verdade, o tvOS 9 é um subconjunto do iOS 9, portanto, muitos conceitos serão cruzados aqui.

Vamos dar uma olhada nos arquivos no projeto:

-   `Main.cs` – isso contém o ponto de entrada principal do aplicativo. Quando o aplicativo é iniciado, ele contém a primeira classe e método que é executado.
-   `AppDelegate.cs`– Esse arquivo contém a classe de aplicativo principal que é responsável pela escuta de eventos do sistema operacional.
-   `Info.plist`– Esse arquivo contém propriedades de aplicativo, como o nome do aplicativo, ícones, etc.
-   `ViewController.cs`– Essa é a classe que representa a janela principal e controla o ciclo de vida dela.
-   `ViewController.designer.cs`– Esse arquivo contém um código de direcionamento que ajuda você a se integrar à interface do usuário da tela principal.
-  `Main.storyboard`– A interface do usuário para a janela principal. Esse arquivo pode ser criado e mantido pelo Xamarin Designer para iOS.

Nas seções a seguir, vamos dar uma olhada rápida em alguns desses arquivos. Vamos explorá-las mais detalhadamente mais tarde, mas é uma boa ideia entender suas noções básicas agora.

### <a name="maincs"></a>Main.cs

O `Main.cs` arquivo contém um método `Main` estático que cria uma nova instância do aplicativo Xamarin. tvOS e passa o nome da classe que manipulará os eventos do sistema operacional, que, em `AppDelegate` nosso caso, é a classe:

```csharp
using UIKit;

namespace Hello_tvOS
{
    public class Application
    {
        // This is the main entry point of the application.
        static void Main (string[] args)
        {
            // if you want to use a different Application Delegate class from "AppDelegate"
            // you can specify it here.
            UIApplication.Main (args, null, "AppDelegate");
        }
    }
}
```

### <a name="appdelegatecs"></a>AppDelegate.cs

O `AppDelegate.cs` arquivo contém nossa `AppDelegate` classe, que é responsável pela criação da nossa janela e pela escuta de eventos do sistema operacional:

```csharp
using Foundation;
using UIKit;

namespace Hello_tvOS
{
    // The UIApplicationDelegate for the application. This class is responsible for launching the
    // User Interface of the application, as well as listening (and optionally responding) to application events from iOS.
    [Register ("AppDelegate")]
    public class AppDelegate : UIApplicationDelegate
    {
        // class-level declarations

        public override UIWindow Window {
            get;
            set;
        }

        public override bool FinishedLaunching (UIApplication application, NSDictionary launchOptions)
        {
            // Override point for customization after application launch.
            // If not required for your application you can safely delete this method

            return true;
        }

        public override void OnResignActivation (UIApplication application)
        {
            // Invoked when the application is about to move from active to inactive state.
            // This can occur for certain types of temporary interruptions (such as an incoming phone call or SMS message)
            // or when the user quits the application and it begins the transition to the background state.
            // Games should use this method to pause the game.
        }

        public override void DidEnterBackground (UIApplication application)
        {
            // Use this method to release shared resources, save user data, invalidate timers and store the application state.
            // If your application supports background execution this method is called instead of WillTerminate when the user quits.
        }

        public override void WillEnterForeground (UIApplication application)
        {
            // Called as part of the transition from background to active state.
            // Here you can undo many of the changes made on entering the background.
        }

        public override void OnActivated (UIApplication application)
        {
            // Restart any tasks that were paused (or not yet started) while the application was inactive.
            // If the application was previously in the background, optionally refresh the user interface.
        }

        public override void WillTerminate (UIApplication application)
        {
            // Called when the application is about to terminate. Save data, if needed. See also DidEnterBackground.
        }
    }
}
```

Esse código provavelmente não é familiar, a menos que você tenha criado um aplicativo iOS antes, mas ele é razoavelmente simples. Vamos examinar as linhas importantes.

Primeiro, vamos dar uma olhada na declaração de variável em nível de classe:

```csharp
public override UIWindow Window {
            get;
            set;
        }

```

A `Window` propriedade fornece acesso à janela principal. o tvOS usa o que é conhecido como padrão MVC ( *Model View Controller* ). Em geral, para cada janela que você cria (e para muitas outras coisas no Windows), há um controlador, que é responsável pelo ciclo de vida da janela, como mostrá-lo, adicionar novos modos de exibição (controles) a ele, etc.

Em seguida, temos o `FinishedLaunching` método. Esse método é executado depois que o aplicativo tiver sido instanciado e for responsável por realmente criar a janela do aplicativo e iniciar o processo de exibição dela. Como nosso aplicativo usa um Storyboard para definir sua interface do usuário, nenhum código adicional é necessário aqui.

Há muitos outros métodos que são fornecidos no modelo, `DidEnterBackground` como e. `WillEnterForeground` Eles podem ser removidos com segurança se os eventos do aplicativo não estiverem sendo usados em seu aplicativo.

### <a name="viewcontrollercs"></a>ViewController.cs

A `ViewController` classe é o controlador da nossa janela principal. Isso significa que é responsável pelo ciclo de vida da janela principal. Vamos examinar isso detalhadamente mais tarde, por enquanto, vamos dar uma olhada rápida nele:

```csharp
using System;
using Foundation;
using UIKit;

namespace Hello_tvOS
{
    public partial class ViewController : UIViewController
    {
        public ViewController (IntPtr handle) : base (handle)
        {
        }

        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();
            // Perform any additional setup after loading the view, typically from a nib.
        }

        public override void DidReceiveMemoryWarning ()
        {
            base.DidReceiveMemoryWarning ();
            // Release any cached data, images, etc that aren't in use.
        }
    }
}
```

#### <a name="viewcontrollerdesignercs"></a>ViewController.Designer.cs

O arquivo de designer para a classe de janela principal está vazio no momento, mas ele será populado automaticamente pelo Visual Studio para Mac à medida que criamos nossa interface do usuário com o designer do iOS:

```csharp
using Foundation;

namespace HellotvOS
{
    [Register ("ViewController")]
    partial class ViewController
    {
        void ReleaseDesignerOutlets ()
        {
        }
    }
}
```

Normalmente, não estamos preocupados com arquivos de designer, pois eles são gerenciados automaticamente pelo Visual Studio para Mac e fornecem apenas o código de direcionamento necessário que permite o acesso a controles que adicionamos a qualquer janela ou exibição em nosso aplicativo.

Agora que criamos nosso aplicativo Xamarin. tvOS e temos uma compreensão básica de seus componentes, vamos examinar a criação da interface do usuário.

<a name="Creating-the-User-Interface" />

## <a name="creating-the-user-interface"></a>Criando a interface do usuário

Você não precisa usar Xamarin Designer para iOS para criar a interface do usuário para o seu aplicativo Xamarin. tvOS, a interface de usuário pode ser C# criada diretamente do código, mas isso está além do escopo deste artigo. Para simplificar, usaremos o designer do iOS para criar nossa interface do usuário no restante deste tutorial.

Para começar a criar sua interface do usuário, vamos clicar duas `Main.storyboard` vezes no arquivo no **Gerenciador de soluções** para abri-lo para edição no designer do IOS:

[![](hello-tvos-images/designer01.png "O arquivo Main.storyboard no Gerenciador de Soluções")](hello-tvos-images/designer01.png#lightbox)

Isso deve iniciar o designer e se parecer com o seguinte:

[![](hello-tvos-images/designer02.png "O designer")](hello-tvos-images/designer02.png#lightbox)

Para obter mais informações sobre o designer do iOS e como ele funciona, consulte a [introdução ao guia de Xamarin designer para IOS](~/ios/user-interface/designer/introduction.md) .

Agora podemos começar a adicionar controles à superfície de design do nosso aplicativo Xamarin. tvOS.

Faça o seguinte:

1. Localize a **caixa de ferramentas**, que deve estar à direita da superfície de design:

    [![](hello-tvos-images/designer03.png "A caixa de ferramentas")](hello-tvos-images/designer03.png#lightbox)

    Se você não conseguir localizá-lo aqui, navegue para **exibir > Pads > caixa de ferramentas** para exibi-lo.
2. Arraste um **rótulo** da **caixa de ferramentas** para a superfície de design:

    [![](hello-tvos-images/designer04.png "Arraste um rótulo da caixa de ferramentas")](hello-tvos-images/designer04.png#lightbox)
3. Clique na propriedade **título** no painel de **Propriedades** e altere o título do botão para `Hello, tvOS` e defina o **tamanho da fonte** como 128:

    [![](hello-tvos-images/designer05.png "Defina o título como Olá, tvOS e defina o tamanho da fonte como 128")](hello-tvos-images/designer05.png#lightbox)
4. Redimensione o rótulo para que todas as palavras fiquem visíveis e coloque-o centralizado próximo à parte superior da janela:

    [![](hello-tvos-images/designer06.png "Redimensionar e centralizar o rótulo")](hello-tvos-images/designer06.png#lightbox)
5. O rótulo agora precisará ser restrito a sua posição, para que ele seja exibido como pretendido. independentemente do tamanho da tela. Para fazer isso, clique no rótulo até que o *identificador em formato T* seja exibido:

    [![](hello-tvos-images/designer07.png "A alça em formato T")](hello-tvos-images/designer07.png#lightbox)
6. Para restringir o rótulo horizontalmente, selecione o quadrado central e arraste-o para a linha tracejada verticalmente:

    [![](hello-tvos-images/designer08.png "Selecione o quadrado central")](hello-tvos-images/designer08zoom.png#lightbox)

     O rótulo deve ativar laranja.
7. Selecione a alça T na parte superior do rótulo e arraste-a para a borda superior da janela:

    [![](hello-tvos-images/designer09.png "Arraste a alça até a borda superior da janela")](hello-tvos-images/designer09.png#lightbox)
8. Em seguida, clique na largura e na altura do *identificador Bone* , conforme ilustrado abaixo:

    [![](hello-tvos-images/designer10.png "As alças de largura e altura do Bone")](hello-tvos-images/designer10.png#lightbox)

     Quando cada *identificador de Bone* for clicado, selecione largura e altura, respectivamente, para definir dimensões fixas.
9. Quando concluído, suas restrições devem ser semelhantes àquelas na guia layout do painel de propriedades:

    [![](hello-tvos-images/designer11.png "Restrições de exemplo")](hello-tvos-images/designer11.png#lightbox)
10. Arraste um **botão** da **caixa de ferramentas** e coloque-o sob o rótulo.
11. Clique na propriedade **título** no painel de **Propriedades** e altere o título do botão para `Click Me`:

    [![](hello-tvos-images/designer12.png "Alterar o título dos botões para clicar em mim")](hello-tvos-images/designer12.png#lightbox)
12. Repita as etapas de 5 a 8 acima para restringir o botão na janela tvOS. No entanto, em vez de arrastar o identificador T para a parte superior da janela (como na etapa #7), arraste-o para a parte inferior do rótulo:

    [![](hello-tvos-images/designer14.png "Restringir o botão")](hello-tvos-images/designer14.png#lightbox)
13. Arraste outro rótulo sob o botão, dimensione-o para ter a mesma largura que o primeiro rótulo e defina seu **alinhamento** como **centralizado**:

    [![](hello-tvos-images/designer15.png "Arraste outro rótulo sob o botão, dimensione-o para ter a mesma largura que o primeiro rótulo e defina seu alinhamento como centralizado")](hello-tvos-images/designer15.png#lightbox)
14. Como o primeiro rótulo e botão, defina este rótulo como centralizado e fixe-o no local e no tamanho:

    [![](hello-tvos-images/designer16.png "Fixar o rótulo em local e tamanho")](hello-tvos-images/designer16.png#lightbox)
15. Salve as alterações na interface do usuário.

Como você estava redimensionando e movendo controles, você deve ter notado que o designer fornece dicas de ajuste úteis que se baseiam nas [diretrizes de interface humana da Apple TV](https://developer.apple.com/tvos/human-interface-guidelines/). Essas diretrizes ajudarão você a criar aplicativos de alta qualidade que terão uma aparência familiar para os usuários da Apple TV.

Se você examinar a seção **estrutura de tópicos do documento** , observe como o layout e a hierarquia dos elementos que compõem a interface do usuário são mostrados:

[![](hello-tvos-images/designer17.png "A seção estrutura de tópicos do documento")](hello-tvos-images/designer17.png#lightbox)

A partir daqui, você pode selecionar itens para editar ou arrastar para reordenar os elementos da interface do usuário, se necessário. Por exemplo, se um elemento de interface do usuário estava sendo coberto por outro elemento, você poderia arrastá-lo para a parte inferior da lista para torná-lo o item mais alto na janela.

Agora que temos a interface do usuário criada, precisamos expor os itens da interface do usuário para que o Xamarin. tvOS possa acessar e interagir com C# eles no código.

### <a name="accessing-the-controls-in-the-code-behind"></a>Acessando os controles no code-behind

Há duas maneiras principais de acessar os controles que você adicionou no designer do iOS a partir do código:

* Criando um manipulador de eventos em um controle.
* Dar um nome ao controle para que possamos referenciá-lo posteriormente.

Quando qualquer um deles for adicionado, a classe parcial dentro do `ViewController.designer.cs` será atualizada para refletir as alterações. Isso permitirá que você acesse os controles no controlador de exibição.

### <a name="creating-an-event-handler"></a>Criando um manipulador de eventos

Neste aplicativo de exemplo, quando o botão é clicado, queremos que _algo_ aconteça, portanto, um manipulador de eventos precisa ser adicionado a um evento específico no botão. Para configurar isso, faça o seguinte:

1. No Xamarin iOS designer, selecione o botão no controlador de exibição.
2. No painel Propriedades, selecione a guia **eventos** :

    [![](hello-tvos-images/event1.png "A guia eventos")](hello-tvos-images/event1.png#lightbox)
3. Localize o evento TouchUpInside e dê a ele um manipulador de eventos `Clicked`chamado:

    [![](hello-tvos-images/event2.png "O evento TouchUpInside")](hello-tvos-images/event2.png#lightbox)
4. Quando você pressiona **Enter**, o arquivo **ViewController**. cs será aberto, sugerindo locais para seu manipulador de eventos no código. Use as teclas de direção do teclado para definir o local:

    [![](hello-tvos-images/event3.png "Configurando o local")](hello-tvos-images/event3.png#lightbox)
5. Isso criará um método parcial, conforme mostrado abaixo:

    [![](hello-tvos-images/event4.png "O método parcial")](hello-tvos-images/event4.png#lightbox)

Agora estamos prontos para começar a adicionar algum código para permitir que o botão funcione.

### <a name="naming-a-control"></a>Nomeando um controle

Quando o botão é clicado, o rótulo deve ser atualizado com base no número de cliques. Para fazer isso, será necessário acessar o rótulo no código. Isso é feito dando um nome a ele. Faça o seguinte:

1. Abra o storyboard e selecione o rótulo na parte inferior do controlador de exibição.
2. No painel Propriedades, selecione a guia **widget** :

    [![](hello-tvos-images/name1.png "Selecione a guia widget")](hello-tvos-images/name1.png#lightbox)
3. Em **identidade > nome**, adicione `ClickedLabel`:

    [![](hello-tvos-images/name2.png "Definir ClickedLabel")](hello-tvos-images/name2.png#lightbox)

Agora estamos prontos para começar a atualizar o rótulo!

### <a name="how-controls-are-accessed"></a>Como os controles são acessados

Se você selecionar o `ViewController.designer.cs` no **Gerenciador de soluções** poderá ver como o `ClickedLabel` rótulo e o manipulador de `Clicked` eventos foram mapeados para uma **tomada** e **ação** em C#:

[![](hello-tvos-images/accesscontrol.png "Saídas e ações")](hello-tvos-images/accesscontrol.png#lightbox)

Você também pode observar que `ViewController.designer.cs` é uma classe parcial, para que Visual Studio para Mac não precise modificar `ViewController.cs` , o que substituiria as alterações feitas na classe.

Expor os elementos da interface do usuário dessa forma, permite que você os acesse no controlador de exibição.

Normalmente, você nunca precisará abrir o `ViewController.designer.cs` mesmo, ele foi apresentado aqui apenas para fins educacionais.

<a name="Writing-the-Code" />

## <a name="writing-the-code"></a>Escrevendo o código

Com a interface do usuário criada e seus elementos de interface do usuário expostos ao código por meio de **saídas** e **ações**, finalmente estamos prontos para escrever o código para fornecer a funcionalidade do programa.

Em nosso aplicativo, sempre que o primeiro botão for clicado, vamos atualizar nosso rótulo para mostrar quantas vezes o botão foi clicado. Para fazer isso, precisamos abrir o `ViewController.cs` arquivo para edição clicando duas vezes nele no **painel de soluções**:

[![](hello-tvos-images/code01.png "O painel de soluções")](hello-tvos-images/code01.png#lightbox)

Primeiro, precisamos criar uma variável em nível de classe em nossa `ViewController` classe para controlar o número de cliques que ocorreram. Edite a definição de classe e torne-a semelhante ao seguinte:

```csharp
using System;
using Foundation;
using UIKit;

namespace Hello_tvOS
{
    public partial class ViewController : UIViewController
    {
        private int numberOfTimesClicked = 0;
        ...
```

Em seguida, na mesma classe (`ViewController`), precisamos substituir o método **ViewDidLoad** e adicionar algum código para definir a mensagem inicial para nosso rótulo:

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();

    // Set the initial value for the label
    ClickedLabel.Text = "Button has not been clicked yet.";
}
```

Precisamos `ViewDidLoad`usar, em vez de outro método `Initialize`, como, porque `ViewDidLoad` é chamado *depois* que o sistema operacional `.storyboard` tiver carregado e instanciado a interface do usuário a partir do arquivo. Se tentamos acessar o controle rótulo antes que o `.storyboard` arquivo tenha sido totalmente carregado e instanciado, recebemos um `NullReferenceException` erro porque o controle rótulo não seria criado ainda.

Em seguida, precisamos adicionar o código para responder ao usuário clicando no botão. Adicione o seguinte à classe parcial para o que criamos:

```csharp
partial void Clicked (UIButton sender)
{
    ClickedLabel.Text = string.Format("The button has been clicked {0} time{1}.", ++numberOfTimesClicked, (numberOfTimesClicked
```

Esse código será chamado sempre que o usuário clicar em nosso botão.

Com tudo em vigor, agora estamos prontos para criar e testar nosso aplicativo Xamarin. tvOS.

## <a name="testing-the-application"></a>Testando o aplicativo

É hora de compilar e executar nosso aplicativo para garantir que ele seja executado conforme o esperado. Podemos criar e executar tudo em uma só etapa, ou podemos compilá-lo sem executá-lo.

Sempre que criamos um aplicativo, podemos escolher que tipo de compilação desejamos:

-   **Depuração** – uma compilação de depuração é compilada em um arquivo ' ' (aplicativo) com metadados extras que nos permite depurar o que está acontecendo enquanto o aplicativo está em execução.
-   **Versão** – uma compilação de versão também cria um arquivo ' ', mas não inclui informações de depuração, portanto, é menor e é executado mais rapidamente.  

Você pode selecionar o tipo de compilação no seletor de **configuração** no canto superior esquerdo da tela de Visual Studio para Mac:

[![](hello-tvos-images/run01.png "Selecione o tipo de compilação")](hello-tvos-images/run01.png#lightbox)

### <a name="building-the-application"></a>Compilando o aplicativo

Em nosso caso, queremos apenas uma compilação de depuração, portanto, vamos garantir que **debug** esteja selecionado. Primeiro, vamos criar nosso aplicativo pressionando **⌘ B**ou, no menu **Build** , escolha **compilar tudo**.

Se não houver erros, você verá uma mensagem de **compilação bem-sucedida** na barra de status do Visual Studio para Mac. Se houver erros, examine seu projeto e verifique se você seguiu as etapas corretamente. Comece confirmando que seu código (no Xcode e no Visual Studio para Mac) corresponde ao código no tutorial.

### <a name="running-the-application"></a>Executando o aplicativo

Para executar o aplicativo, temos três opções:

-  Pressione **⌘+Enter**.
-  No menu **Executar**, escolha **Depuração**.
-  Clique no botão **Reproduzir** na barra de ferramentas do Visual Studio para Mac (imediatamente acima do **Gerenciador de Soluções**).

O aplicativo será criado (se ainda não tiver sido criado), começar no modo de depuração, o simulador do tvOS será iniciado e o aplicativo será iniciado e exibirá a janela da interface principal:

[![A tela inicial do aplicativo de exemplo](hello-tvos-images/run03.png)](hello-tvos-images/run03.png#lightbox)

No menu **hardware** , selecione **Mostrar Apple TV Remote** para que você possa controlar o simulador.

[![](hello-tvos-images/run04.png "Selecione Mostrar Apple TV remoto")](hello-tvos-images/run04.png#lightbox)

Usando o remoto do simulador, se você clicar no botão algumas vezes o rótulo deverá ser atualizado com a contagem:

[![](hello-tvos-images/run05.png "O rótulo com contagem atualizada")](hello-tvos-images/run05.png#lightbox)

Parabéns! Abordamos muitos aspectos aqui, mas se você seguiu este tutorial do início ao fim, agora você deve ter uma compreensão sólida dos componentes de um aplicativo Xamarin. tvOS, bem como das ferramentas usadas para criá-los.

## <a name="where-to-next"></a>Para onde avançar?

Desenvolver aplicativos da Apple TV apresenta alguns desafios devido à desconexão entre o usuário e a interface (ele está na sala que não está na mão do usuário) e as limitações tvOSm o tamanho do aplicativo e o armazenamento.

Como resultado, é altamente recomendável que você leia os documentos a seguir antes de entrar em um design do aplicativo Xamarin. tvOS:

- [Introdução ao tvOS 9](~/ios/tvos/platform/tvos9.md) – este artigo apresenta todas as APIs e recursos novos e modificados disponíveis no tvOS 9 para desenvolvedores do Xamarin. tvOS.
- [Trabalhando com navegação e foco](~/ios/tvos/app-fundamentals/navigation-focus.md) – os usuários do seu aplicativo Xamarin. tvOS não serão interagindo com a interface diretamente como com Ios, onde tocam nas imagens na tela do dispositivo, mas indiretamente de toda a sala usando o Siri remoto. Este artigo aborda o conceito de foco e como ele é usado para manipular a navegação em uma interface do usuário do aplicativo Xamarin. tvOS.
- [Siri controladores remotos e Bluetooth](~/ios/tvos/platform/remote-bluetooth.md) – a principal maneira que os usuários vão interagir com a Apple TV e seu aplicativo Xamarin. tvOS, é por meio do Siri remoto incluído. Se seu aplicativo for um jogo, você poderá, opcionalmente, criar suporte para controladores de jogos Bluetooth do iOS (acessórios MFI) em seu aplicativo também. Este artigo aborda o suporte aos novos controladores de jogos Siri remotos e Bluetooth em seus aplicativos Xamarin. tvOS.
- [Recursos e armazenamento de dados](~/ios/tvos/app-fundamentals/resources-data-storage.md) – diferentemente dos dispositivos IOS, a nova Apple TV não fornece armazenamento local persistente para aplicativos tvOS. Como resultado, se seu aplicativo Xamarin. tvOS precisar manter informações (como preferências do usuário), ele deverá armazenar e recuperar esses dados do iCloud. Este artigo aborda como trabalhar com recursos e armazenamento de dados persistentes em um aplicativo Xamarin. tvOS.
- [Trabalhando com ícones e imagens](~/ios/tvos/app-fundamentals/icons-images.md) – a criação de ícones e cativantes de imagem é uma parte essencial do desenvolvimento de uma experiência de usuário imersiva para seus aplicativos da Apple TV. Este guia abordará as etapas necessárias para criar e incluir os ativos gráficos necessários para seus aplicativos Xamarin. tvOS.
- [Interface do usuário](~/ios/tvos/user-interface/index.md) – cobertura geral da experiência do usuário (UX), incluindo controles da interface do usuário, use os princípios de design Interface Builder e UX do Xcode ao trabalhar com o Xamarin. tvOS.
- [Implantação e teste](~/ios/tvos/deploy-test/index.md) – esta seção aborda os tópicos usados para testar um aplicativo, bem como distribuí-lo. Os tópicos aqui incluem coisas como ferramentas usadas para depuração, implantação para testadores e como publicar um aplicativo na loja de aplicativos da Apple TV.

Se você tiver problemas para trabalhar com o Xamarin. tvOS, consulte nossa documentação de [solução de problemas](~/ios/tvos/troubleshooting.md) para obter uma lista sobre problemas conhecidos e soluções.

## <a name="summary"></a>Resumo

Este artigo forneceu um início rápido para desenvolver aplicativos para tvOS com Visual Studio para Mac criando um aplicativo Hello, tvOS simples. Ele abordou os conceitos básicos do provisionamento de dispositivos tvOS, criação de interface, codificação para tvOS e testes no simulador de tvOS.


## <a name="related-links"></a>Links relacionados

- [Amostras do tvOS](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+tvOS)
- [tvOS](https://developer.apple.com/tvos/)
- [Guias de interface humana do tvOS](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guia de programação de aplicativo para tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
- [Criando aplicativos para tvOS com Xamarin (vídeo)](https://university.xamarin.com/lightninglectures/tvos-with-xamarin)
