---
title: Olá, guia de início rápido do tvOS
description: Este guia ensina a criar seu primeiro aplicativo xamarin. tvos e sua cadeia de ferramentas de desenvolvimento. Ele também apresenta o Designer do Xamarin, que expõe controles de interface do usuário ao código e ilustra como criar, executar e testar um aplicativo xamarin. tvos.
ms.prod: xamarin
ms.assetid: 6E0AFE58-A13B-492F-861E-D5D73EB1C4A3
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 02/02/2018
ms.openlocfilehash: 6b404d7c27fba38c53ff07207003849445d51118
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67832473"
---
# <a name="hello-tvos-quick-start-guide"></a>Olá, guia de início rápido do tvOS

_Este guia ensina a criar seu primeiro aplicativo xamarin. tvos e sua cadeia de ferramentas de desenvolvimento. Ele também apresenta o Designer do Xamarin, que expõe controles de interface do usuário ao código e ilustra como criar, executar e testar um aplicativo xamarin. tvos._

Apple lançou 5a geração da Apple TV, Apple TV 4K, que é executado tvOS 11.

A plataforma de Apple TV está aberta para desenvolvedores, permitindo que eles criar aplicativos avançados e imersivos e liberá-los por meio de Store de aplicativo interno a Apple TV.

Se você estiver familiarizado com o desenvolvimento do xamarin. IOS, você deve encontrar a transição para tvOS bastante simples. A maioria das APIs e recursos é os mesmos, no entanto, muitas APIs comuns estão disponíveis (como WebKit). Além disso, trabalhar com o com o Siri remoto apresenta alguns desafios de design que não estão presentes nos dispositivos iOS de tela sensível ao toque com base.

Este guia lhe dará uma introdução ao trabalhar com tvOS em um aplicativo Xamarin. Para obter mais informações sobre tvOS, consulte da Apple [Prepare-se para Apple TV 4K](https://developer.apple.com/tvos/) documentação.

## <a name="overview"></a>Visão geral

Tvos permite que você desenvolva aplicativos totalmente nativos para Apple TV em C# e .NET usando as mesmas bibliotecas dos X e controles de interface que são usados ao desenvolver em *Swift* (ou *Objective-C*) e  *Xcode*.

Além disso, desde o xamarin. tvos aplicativos são escritos em C# e o código de back-end comum, .NET, pode ser compartilhado com aplicativos xamarin. IOS, xamarin. Android e xamarin. Mac. ao mesmo tempo, fornece uma experiência nativa em cada plataforma.

Este artigo apresentará os principais conceitos necessários para criar um aplicativo de TV da Apple usando o Visual Studio e xamarin. tvos ao guiá-lo pelo processo de criação de um basic **Olá, tvOS** tem do aplicativo que conta o número de vezes que um botão foi clicado:

[![](hello-tvos-images/run05.png "Execução do aplicativo de exemplo")](hello-tvos-images/run05.png#lightbox)

Abordaremos os seguintes conceitos:

-  **O Visual Studio para Mac** – Introdução ao Visual Studio para Mac e como criar aplicativos xamarin. tvos com ele.
-  **Anatomia de um App Xamarin.tvOS** – consiste em aplicativo o que um xamarin. tvos.
-  **Criando uma Interface do usuário** – como usar o Designer do Xamarin para iOS para criar uma interface do usuário.
-  **Implantação e teste** – como executar e testar seu aplicativo no simulador do tvOS e no hardware real tvOS.

## <a name="starting-a-new-xamarintvos-app-in-visual-studio-for-mac"></a>Iniciando um App Xamarin.tvOS novo no Visual Studio para Mac

Como mencionado acima, vamos criar um aplicativo de Apple TV chamado `Hello-tvOS` que adiciona um único botão e um rótulo para a tela principal. Quando o botão for clicado, o rótulo exibirá o número de vezes que isso ocorreu.

Para começar, vamos fazer o seguinte:

1. Inicie o Visual Studio para Mac:

    [![](hello-tvos-images/setup01.png "O Visual Studio para Mac")](hello-tvos-images/setup01.png#lightbox)
2. Clique no **nova solução...**  link no canto superior esquerdo da tela para abrir o **novo projeto** caixa de diálogo.
3. Selecione **tvOS** > **App** > **aplicativo de exibição única** e clique no **próxima** botão:

    [![](hello-tvos-images/setup02.png "Selecione o aplicativo de exibição única")](hello-tvos-images/setup02.png#lightbox)
4. Insira `Hello, tvOS` para o **nome do aplicativo**, insira sua **identificador de organização** e clique no **próximo** botão:

    [![](hello-tvos-images/setup04.png "Insira Olá, tvOS")](hello-tvos-images/setup04.png#lightbox)
5. Insira `Hello_tvOS` para o **nome do projeto** e clique no **criar** botão:

    [![](hello-tvos-images/setup03.png "Insira HellotvOS")](hello-tvos-images/setup03.png#lightbox)

O Visual Studio para Mac criará o novo aplicativo xamarin. tvos e exibir os arquivos padrão que são adicionados à solução do seu aplicativo:

 [![](hello-tvos-images/project01.png "A exibição de arquivos padrão")](hello-tvos-images/project01.png#lightbox)

O Visual Studio para Mac usa **soluções** e **projetos**, em exatamente da mesma maneira que o Visual Studio. Uma solução é um contêiner que pode conter um ou mais projetos; projetos podem incluir aplicativos, bibliotecas de suporte, aplicativos de teste, etc. Nesse caso, o Visual Studio para Mac criou uma solução e um projeto de aplicativo para você.

Se você quisesse, você pode criar código de um ou mais projetos de biblioteca que contêm código comum compartilhado. Esses projetos de biblioteca podem ser consumidos pelo projeto de aplicativo ou compartilhados com outros projetos de aplicativo tvos (ou xamarin. IOS, xamarin. Android e xamarin. Mac com base no tipo de código), assim como você faria se você estivesse criando um aplicativo .NET padrão.

## <a name="anatomy-of-a-xamarintvos-app"></a>Anatomia de um aplicativo xamarin. tvos

Se você estiver familiarizado com a programação do iOS, você perceberá muitas semelhanças aqui. Na verdade, tvOS 9 é um subconjunto do iOS 9, portanto, muitos conceitos serão transpostos aqui.

Vamos examinar os arquivos no projeto:

-   `Main.cs` – isso contém o ponto de entrada principal do aplicativo. Quando o aplicativo é iniciado, ele contém a primeira classe e método que é executado.
-   `AppDelegate.cs` – Esse arquivo contém a classe principal do aplicativo que é responsável por escutar eventos do sistema operacional.
-   `Info.plist` – Esse arquivo contém as propriedades do aplicativo, como o nome do aplicativo, ícones, etc.
-   `ViewController.cs` – Esta é a classe que representa a janela principal e controla o ciclo de vida dele.
-   `ViewController.designer.cs` – Esse arquivo contém o código minucioso que ajuda você a integrar a interface do usuário da tela principal.
-  `Main.storyboard` – A interface do usuário para a janela principal. Esse arquivo pode ser criado e mantido pelo Designer do Xamarin para iOS.

Nas seções a seguir, vamos dar uma olhada rápida por meio de alguns desses arquivos. Vamos explorá-los mais detalhadamente mais adiante, mas é uma boa ideia compreender seus fundamentos agora.

### <a name="maincs"></a>Main.cs

O `Main.cs` arquivo contém um estático `Main` método que cria uma nova instância do aplicativo tvos e passa o nome da classe que manipulará eventos de sistema operacional, que, em nosso caso, é o `AppDelegate` classe:

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

O `AppDelegate.cs` arquivo contém nosso `AppDelegate` classe, que é responsável por criar a nossa janela e escutar eventos do sistema operacional:

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

Esse código é provavelmente é desconhecido, a menos que você criou um aplicativo iOS antes, mas é bastante simples. Vamos examinar as linhas importantes.

Primeiro, vamos dar uma olhada na declaração da variável de nível de classe:

```csharp
public override UIWindow Window {
            get;
            set;
        }

```

O `Window` propriedade fornece acesso à janela principal. tvOS usa o que é conhecido como o *Model View Controller* padrão (MVC). Em geral, para cada janela que você cria (e para muitas outras coisas dentro do windows), há um controlador, que é responsável pelo ciclo de vida da janela, como exibi-la, adicionar novos modos de exibição (controles) a ela, etc.

Em seguida, temos o `FinishedLaunching` método. Esse método é executado depois que o aplicativo foi instanciado e é responsável por realmente criar a janela do aplicativo e iniciar o processo de exibir o modo de exibição nele. Como nosso aplicativo usa um Storyboard para definir sua interface do usuário, sem nenhum código adicional é necessário aqui.

Há muitos outros métodos que são fornecidos no modelo, como `DidEnterBackground` e `WillEnterForeground`. Eles podem ser removidos com segurança se os eventos do aplicativo não estão sendo usados em seu aplicativo.

### <a name="viewcontrollercs"></a>ViewController.cs

O `ViewController` classe é o controlador da nossa janela principal. Isso significa que ele é responsável por ciclo de vida da janela principal. Vamos então para examinar isso detalhadamente mais adiante, agora vamos dar uma rápida olhada nele:

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

O arquivo de designer para a classe de janela principal está vazio agora, mas ele será ser automaticamente preenchido pelo Visual Studio para Mac à medida que criarmos nossa Interface do usuário com o Designer do iOS:

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

Podemos geralmente não estão preocupados com arquivos de designer, como eles são gerenciados apenas automaticamente pelo Visual Studio para Mac e fornecem apenas o código requerido que permite acesso aos controles que podemos adicionar a qualquer janela ou exibição em nosso aplicativo.

Agora que criamos nosso aplicativo tvos e temos uma compreensão básica de seus componentes, vamos dar uma olhada na criação de interface do usuário.

<a name="Creating-the-User-Interface" />

## <a name="creating-the-user-interface"></a>Criando a interface do usuário

Você não precisa usar o Designer de Xamarin para iOS para criar a Interface do usuário para seu aplicativo tvos, a interface do usuário pode ser criada diretamente no C# código mas que está além do escopo deste artigo. Para simplificar, usaremos o Designer do iOS para criar nossa interface do usuário em todo o restante deste tutorial.

Para começar a criar sua interface do usuário, vamos clicar duas vezes o `Main.storyboard` arquivo o **Gerenciador de soluções** para abri-lo para edição no Designer do iOS:

[![](hello-tvos-images/designer01.png "O arquivo Main.storyboard no Gerenciador de Soluções")](hello-tvos-images/designer01.png#lightbox)

Isso deve iniciar o Designer e a seguinte aparência:

[![](hello-tvos-images/designer02.png "O Designer")](hello-tvos-images/designer02.png#lightbox)

Para obter mais informações sobre o Designer do iOS e como ele funciona, consulte o [Introdução ao Designer de Xamarin para iOS](~/ios/user-interface/designer/introduction.md) guia.

Agora podemos começar adicionando controles à superfície de design do nosso aplicativo tvos.

Faça o seguinte:

1. Localize o **caixa de ferramentas**, que deve ser à direita da superfície de design:

    [![](hello-tvos-images/designer03.png "A caixa de ferramentas")](hello-tvos-images/designer03.png#lightbox)

    Se você não pode localizá-lo aqui, navegue até **exibição > painéis > caixa de ferramentas** para exibi-lo.
2. Arraste uma **etiqueta** da **caixa de ferramentas** à superfície de design:

    [![](hello-tvos-images/designer04.png "Arraste um rótulo da caixa de ferramentas")](hello-tvos-images/designer04.png#lightbox)
3. Clique no **título** propriedade no **painel de propriedade** e altere o título do botão para `Hello, tvOS` e defina a **Font Size** para 128:

    [![](hello-tvos-images/designer05.png "Definir o título para Olá, tvOS e defina o tamanho da fonte para 128")](hello-tvos-images/designer05.png#lightbox)
4. Redimensione o rótulo para que todas as palavras são visíveis e colocá-lo centralizado na parte superior da janela:

    [![](hello-tvos-images/designer06.png "Redimensionar e centralizará o rótulo")](hello-tvos-images/designer06.png#lightbox)
5. O rótulo agora precisará ser restrito a sua posição, para que ele apareça como pretendido. independentemente do tamanho da tela. Para fazer isso, clique no rótulo até que o *alça em forma de T* aparece:

    [![](hello-tvos-images/designer07.png "O identificador em forma de T")](hello-tvos-images/designer07.png#lightbox)
6. Para restringir o rótulo horizontalmente, selecione o quadrado central e arraste-o para a linha tracejada verticalmente:

    [![](hello-tvos-images/designer08.png "Selecione o quadrado central")](hello-tvos-images/designer08zoom.png#lightbox)

     O rótulo deverá ficar laranja.
7. Selecione a alça de T na parte superior do rótulo e arraste-o para a borda superior da janela:

    [![](hello-tvos-images/designer09.png "Arraste a alça até a borda superior da janela")](hello-tvos-images/designer09.png#lightbox)
8. Em seguida, clique a largura e a altura *identificador bem-acabados* conforme ilustrado abaixo:

    [![](hello-tvos-images/designer10.png "A largura e a altura bem-acabadosm alças")](hello-tvos-images/designer10.png#lightbox)

     Quando cada *identificador bem-acabados* é clicado, selecione ambos Width e Height respectivamente para definir dimensões fixas.
9. Quando concluído, suas restrições devem ser semelhantes na guia Layout do painel de propriedades:

    [![](hello-tvos-images/designer11.png "Restrições de exemplo")](hello-tvos-images/designer11.png#lightbox)
8. Arraste uma **botão** da **caixa de ferramentas** e coloque-o sob o rótulo.
9. Clique no **Title** propriedade no **painel de propriedade** e altere o título do botão para `Click Me`:

    [![](hello-tvos-images/designer12.png "Altere o título de botões para Click Me")](hello-tvos-images/designer12.png#lightbox)
10. Repita as etapas 5 a 8 acima para restringir o botão na janela do tvOS. No entanto, em vez de arrastar a alça de T na parte superior da janela (como etapa #7), arraste-o até a parte inferior do rótulo:

    [![](hello-tvos-images/designer14.png "Restringir o botão")](hello-tvos-images/designer14.png#lightbox)
11. Arraste outro rótulo do botão, dimensione-o para ser a mesma largura que o primeiro rótulo e defina suas **alinhamento** à **Center**:

    [![](hello-tvos-images/designer15.png "Arraste outro rótulo do botão, dimensione-o para ser a mesma largura que o primeiro rótulo e defina seu alinhamento ao centro")](hello-tvos-images/designer15.png#lightbox)
12. Como o primeiro botão e label, defina esse rótulo para centralizar e fixá-lo no local e o tamanho:

    [![](hello-tvos-images/designer16.png "O rótulo no local e o tamanho do PIN")](hello-tvos-images/designer16.png#lightbox)
13. Salve suas alterações à Interface do usuário.

Conforme você foram redimensionar e mover controles, você deve ter notado que o designer fornece dicas úteis de ajuste que se baseiam [diretrizes de Interface humana da Apple TV](https://developer.apple.com/tvos/human-interface-guidelines/). Essas diretrizes ajudarão você a criar aplicativos de alta qualidade que terão uma aparência familiar para os usuários de Apple TV.

Se você olhar o **Document Outline** seção, observe como o layout e a hierarquia dos elementos que compõem nossa Interface do usuário são exibidos:

[![](hello-tvos-images/designer17.png "A seção de estrutura de tópicos do documento")](hello-tvos-images/designer17.png#lightbox)

A partir daqui, você pode selecionar itens para editar ou arrastar para reordenar os elementos de interface do usuário se necessário. Por exemplo, se um elemento de interface do usuário estava sendo coberto por outro elemento, você poderia arrastá-lo até a parte inferior da lista para torná-lo o item superior na janela.

Agora que temos nossa Interface do usuário criado, é necessário para expor os itens de interface do usuário para que o xamarin. tvos pode acessar e interagir com eles no C# código.

### <a name="accessing-the-controls-in-the-code-behind"></a>Acessando os controles no code-behind

Há duas maneiras de acessar os controles que você adicionou no designer de iOS do código:

* Criando um manipulador de eventos em um controle.
* Dando o controle de um nome, para que podemos fazer referência a ele mais tarde.

Quando qualquer uma delas são adicionados, a classe parcial dentro de `ViewController.designer.cs` será atualizada para refletir as alterações. Isso permitirá que você, em seguida, acesse os controles no controlador de exibição.

### <a name="creating-an-event-handler"></a>Criando um manipulador de eventos

Neste aplicativo de exemplo, quando o botão é clicado queremos _algo_ acontecer, portanto, precisa de um manipulador de eventos a ser adicionado a um evento específico no botão. Para configurar isso, faça o seguinte:

1. No Designer de iOS do Xamarin, selecione o botão no controlador de exibição.
2. No painel de propriedades, selecione a **eventos** guia:

    [![](hello-tvos-images/event1.png "A guia eventos")](hello-tvos-images/event1.png#lightbox)
3. Localize o evento TouchUpInside e dê a ele um manipulador de eventos chamado `Clicked`:

    [![](hello-tvos-images/event2.png "O evento TouchUpInside")](hello-tvos-images/event2.png#lightbox)
4. Quando você pressiona **Enter**, o **ViewController**arquivo. cs será aberta, sugerindo locais para o manipulador de eventos no código. Use as teclas de direção no teclado para definir o local:

    [![](hello-tvos-images/event3.png "A localização da configuração")](hello-tvos-images/event3.png#lightbox)
5. Isso criará um método parcial, conforme mostrado abaixo:

    [![](hello-tvos-images/event4.png "O método parcial")](hello-tvos-images/event4.png#lightbox)

Agora estamos prontos para começar a adicionar algum código para permitir que o botão de função.

### <a name="naming-a-control"></a>Um controle de nomenclatura

Quando o botão é clicado, o rótulo deve atualizar com base no número de cliques. Para fazer isso, é necessário acessar o rótulo no código. Isso é feito dando a ele um nome. Faça o seguinte:

1. Abra o Storyboard e selecione o rótulo na parte inferior do controlador de exibição.
2. No painel de propriedades, selecione a **Widget** guia:

    [![](hello-tvos-images/name1.png "Selecione a guia de Widget")](hello-tvos-images/name1.png#lightbox)
3. Sob **identidade > nome**, adicione `ClickedLabel`:

    [![](hello-tvos-images/name2.png "Set ClickedLabel")](hello-tvos-images/name2.png#lightbox)

Agora estamos prontos para começar a atualizar o rótulo!

### <a name="how-controls-are-accessed"></a>Como os controles são acessados

Se você selecionar o `ViewController.designer.cs` no **Gerenciador de soluções** você poderá ver como o `ClickedLabel` rótulo e o `Clicked` manipulador de eventos tiver sido mapeado para um **tomada** e **Ação** em C#:

[![](hello-tvos-images/accesscontrol.png "Saídas e ações")](hello-tvos-images/accesscontrol.png#lightbox)

Você pode perceber que também `ViewController.designer.cs` é uma classe parcial, para que o Visual Studio para Mac não precise modificar `ViewController.cs` que substituiria as alterações feitas à classe.

Expondo os elementos de interface do usuário dessa forma, permite que você acessá-los no controlador de exibição.

Você normalmente nunca precisará abrir o `ViewController.designer.cs` por conta própria, ele foi apresentado aqui apenas para fins educacionais.

<a name="Writing-the-Code" />

## <a name="writing-the-code"></a>Escrevendo o código

Com nossa Interface do usuário criada e seus elementos expostos ao código pelas **tomadas** e **ações**, estamos finalmente prontos para escrever o código para fornecer a funcionalidade do programa.

Em nosso aplicativo, sempre que o primeiro botão é clicado, vamos atualizar nosso rótulo para mostrar quantas vezes o botão foi clicado. Para fazer isso, é preciso abrir o `ViewController.cs` arquivo para edição clicando nele duas vezes no **painel de soluções**:

[![](hello-tvos-images/code01.png "O painel de soluções")](hello-tvos-images/code01.png#lightbox)

Primeiro, precisamos criar uma variável de nível de classe em nossa `ViewController` classe para controlar o número de cliques que ocorreram. Edite a definição de classe e torne-a semelhante ao seguinte:

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

Em seguida, na mesma classe (`ViewController`), precisamos substituir o **ViewDidLoad** método e adicione algum código para definir a mensagem inicial para nosso rótulo:

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();

    // Set the initial value for the label
    ClickedLabel.Text = "Button has not been clicked yet.";
}
```

Precisamos usar `ViewDidLoad`, em vez de outro método como `Initialize`, porque `ViewDidLoad` é chamado *após* o sistema operacional ter carregado e instanciado a Interface do usuário do `.storyboard` arquivo. Se tentar acessar o controle de rótulo antes do `.storyboard` arquivo ter sido totalmente carregado e instanciado, obteremos um `NullReferenceException` erro porque o controle de rótulo não seria criado ainda.

Em seguida, precisamos adicionar o código para responder ao usuário clicar no botão. Adicione o seguinte ao parcial de classe para que criamos:

```csharp
partial void Clicked (UIButton sender)
{
    ClickedLabel.Text = string.Format("The button has been clicked {0} time{1}.", ++numberOfTimesClicked, (numberOfTimesClicked
```

Esse código será chamado sempre que o usuário clica em nosso botão.

Com tudo instalado, agora estamos prontos para compilar e testar o aplicativo xamarin. tvos.

## <a name="testing-the-application"></a>Testando o aplicativo

É hora de criar e executar nosso aplicativo para garantir que ele funciona conforme esperado. Podemos compilar e executar tudo em uma única etapa, ou podemos pode compilá-lo sem executá-lo.

Sempre que compilamos um aplicativo, podemos escolher o tipo de compilação desejamos:

-   **Depurar** – um build de depuração é compilado em um ' arquivo (aplicativo) com metadados extras que nos permite depurar o que está acontecendo enquanto o aplicativo está em execução.
-   **Versão** – um build de versão também cria um ' arquivo, mas ele não inclui informações de depuração, portanto, é menor e executa mais rapidamente.  

Você pode selecionar o tipo de build do **seletor de configuração** no canto superior esquerdo do Visual Studio para a tela do Mac:

[![](hello-tvos-images/run01.png "Selecione o tipo de compilação")](hello-tvos-images/run01.png#lightbox)

### <a name="building-the-application"></a>Compilando o aplicativo

Em nosso caso, estamos deseja apenas uma compilação de depuração, então, vamos garantir que **depurar** está selecionado. Vamos criar nosso aplicativo pela primeira vez pressionando **⌘B**, ou o **Build** menu, escolha **compilar tudo**.

Se não houver erros, você verá uma **Build bem-sucedido** mensagem no Visual Studio para a barra de status do Mac. Se houver erros, analise o projeto e verifique se você seguiu as etapas corretamente. Comece confirmando que o seu código (tanto no Xcode quanto no Visual Studio para Mac) corresponde ao código no tutorial.

### <a name="running-the-application"></a>Executando o aplicativo

Para executar o aplicativo, temos três opções:

-  Pressione **⌘+Enter**.
-  No menu **Executar**, escolha **Depuração**.
-  Clique no botão **Reproduzir** na barra de ferramentas do Visual Studio para Mac (imediatamente acima do **Gerenciador de Soluções**).

O aplicativo será criado (se ele ainda não foi criado), iniciar no modo de depuração, o simulador do tvOS será iniciado e iniciará o aplicativo e exibir a janela da interface principal:

[![A tela inicial do aplicativo de exemplo](hello-tvos-images/run03.png)](hello-tvos-images/run03.png#lightbox)

Dos **Hardware** menu, selecione **Mostrar Apple TV remoto** para que você pode controlar o simulador.

[![](hello-tvos-images/run04.png "Selecione Mostrar Apple TV remoto")](hello-tvos-images/run04.png#lightbox)

Usar o remote do simulador, se você clicar no botão algumas vezes, o rótulo deve ser atualizado com a contagem:

[![](hello-tvos-images/run05.png "O rótulo com contagem atualizada")](hello-tvos-images/run05.png#lightbox)

Parabéns! Abordamos muitos aspectos básicos aqui, mas se você seguiu o tutorial do início ao fim, agora você deve ter uma compreensão sólida dos componentes de um aplicativo xamarin. tvos, bem como as ferramentas usadas para criá-los.

## <a name="where-to-next"></a>Onde Avançar?

Desenvolver apresenta de aplicativos Apple TV alguns desafios de por causa a desconexão entre o usuário e a interface (ele está dentro de uma sala não em mãos do usuário) e o limitações do tvOS coloca em armazenamento e tamanho do aplicativo.

Como resultado, é altamente recomendável que sua leitura seguintes documentos antes de passar para o design de um aplicativo xamarin. tvos:

- [Introdução ao tvOS 9](~/ios/tvos/platform/tvos9.md) – este artigo apresenta todas as APIs e recursos disponíveis no tvOS 9 novos e modificados para desenvolvedores do xamarin. tvos.
- [Trabalhando com navegação e fogo](~/ios/tvos/app-fundamentals/navigation-focus.md) – os usuários do seu aplicativo tvos não interagirá com sua interface diretamente como com o iOS onde eles toque imagens na tela do dispositivo, mas indiretamente de toda a sala usando Siri remoto. Este artigo aborda o conceito de foco e como ele é usado para tratar a navegação na Interface do usuário de um aplicativo xamarin. tvos.
- [Siri remoto e controladores de Bluetooth](~/ios/tvos/platform/remote-bluetooth.md) – a maneira principal que os usuários interagirá com o Apple TV e seu aplicativo tvos, é por meio do Siri remoto incluído. Se seu aplicativo é um jogo, você pode, opcionalmente, criar no suporte para o 3º partido, feitas por controladores de jogo do iOS (MFI) Bluetooth em seu aplicativo também. Este artigo aborda o suporte os novos controladores de jogo Siri remoto e Bluetooth em seus aplicativos xamarin. tvos.
- [Armazenamento de dados e recursos](~/ios/tvos/app-fundamentals/resources-data-storage.md) – ao contrário de dispositivos iOS, Apple TV novo não fornece armazenamento persistente e local para aplicativos de tvOS. Como resultado, se seu aplicativo tvos precisa manter informações (como as preferências do usuário) ele deve armazenar e recuperar dados do iCloud. Este artigo aborda o trabalho com recursos e o armazenamento de dados persistentes em um aplicativo xamarin. tvos.
- [Trabalhando com imagens e ícones](~/ios/tvos/app-fundamentals/icons-images.md) – criando cativante ícones e imagens são uma parte crítica do desenvolvimento de uma experiência de usuário envolventes para seus aplicativos da Apple TV. Este guia aborda as etapas necessárias para criar e incluir os ativos gráficos necessários para seus aplicativos xamarin. tvos.
- [Interface do usuário](~/ios/tvos/user-interface/index.md) – cobertura de experiência de usuário geral (UX), incluindo controles de Interface do usuário (IU), use do Xcode Interface Builder e princípios de design da UX ao trabalhar com o xamarin. tvos.
- [Implantação e teste](~/ios/tvos/deploy-test/index.md) – esta seção aborda tópicos usados para testar um aplicativo, bem como para distribuí-lo. Os tópicos aqui incluem coisas como ferramentas usadas para depuração, implantação em testadores e como publicar um aplicativo para a Store de aplicativo da Apple TV.

Se você tiver problemas ao trabalhar com o xamarin. tvos, consulte nosso [solução de problemas](~/ios/tvos/troubleshooting.md) documentação para obter uma lista em Conheça os problemas e soluções.

## <a name="summary"></a>Resumo

Este artigo fornece um início rápido para desenvolver aplicativos para tvOS com o Visual Studio para Mac com a criação de um simple aplicativo Hello, tvOS. Ele abordou os conceitos básicos do dispositivo tvOS provisionamento, a criação da interface, de codificação para tvOS e testes no simulador tvOS.


## <a name="related-links"></a>Links relacionados

- [Amostras do tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [Guias de Interface humana do tvOS](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guia de programação de aplicativo para tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
- [Criação de aplicativos para tvOS com o Xamarin (vídeo)](https://university.xamarin.com/lightninglectures/tvos-with-xamarin)
