---
title: Olá, tvOS guia de início rápido
description: Este guia explica como criar seu primeiro aplicativo Xamarin.tvOS e suas ferramentas de desenvolvimento. Ele também apresenta o Designer de Xamarin, que expõe os controles de interface do usuário ao código e demonstra como compilar, executar e testar um aplicativo Xamarin.tvOS.
ms.prod: xamarin
ms.assetid: 6E0AFE58-A13B-492F-861E-D5D73EB1C4A3
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 02/02/2018
ms.openlocfilehash: 0adf6e326dd29db15b6bd90626f424b803dc0bc9
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
ms.locfileid: "30785312"
---
# <a name="hello-tvos-quick-start-guide"></a>Olá, tvOS guia de início rápido

_Este guia explica como criar seu primeiro aplicativo Xamarin.tvOS e suas ferramentas de desenvolvimento. Ele também apresenta o Designer de Xamarin, que expõe os controles de interface do usuário ao código e demonstra como compilar, executar e testar um aplicativo Xamarin.tvOS._

Apple lançou a geração de 5 da Apple TV, a Apple TV 4K, que é executado tvOS 11.

A plataforma Apple TV está aberta para desenvolvedores, permitindo criar aplicativos sofisticados e envolventes e liberá-los por meio da loja de aplicativo interno Apple TV.

Se você estiver familiarizado com o desenvolvimento de xamarin, você deve considerar a transição para tvOS bastante simples. A maioria das APIs e recursos é os mesmos, no entanto, várias APIs comuns estão disponíveis (como WebKit). Além disso, trabalhando com o com o computador remoto Siri apresenta alguns desafios de design que não estão presentes nos dispositivos do iOS com base em tela sensível ao toque.

Este guia oferece uma introdução ao trabalhar com tvOS em um aplicativo Xamarin. Para obter mais informações sobre tvOS, consulte da Apple [se preparar para Apple TV 4K](https://developer.apple.com/tvos/) documentação.

## <a name="overview"></a>Visão geral

Xamarin.tvOS permite que você desenvolva aplicativos de Apple TV totalmente nativos em c# e .NET usando o mesmo bibliotecas de OS X e controles de interface que são usados durante o desenvolvimento em *Swift* (ou *Objective-C*) e *Xcode*.

Além disso, como aplicativos de Xamarin.tvOS são escritos em c# e .NET, comuns, código de back-end pode ser compartilhado com aplicativos xamarin, xamarin e Xamarin.Mac. ao mesmo tempo, oferecer uma experiência nativa em cada plataforma.

Este artigo apresenta a você os principais conceitos necessários para criar um aplicativo de TV Apple usando Xamarin.tvOS e o Visual Studio, você acompanhará o processo de construção básico **Hello, tvOS** aplicativo que conta o número de vezes que um botão tem foi clicado:

[![](hello-tvos-images/run05.png "Exemplo de execução do aplicativo")](hello-tvos-images/run05.png#lightbox)

Vamos abordar os seguintes conceitos:

-  **O Visual Studio para Mac** – Introdução ao Visual Studio para Mac e como criar aplicativos Xamarin.tvOS com ele.
-  **Anatomia de um App Xamarin.tvOS** – consiste em que Xamarin.tvOS um aplicativo.
-  **Criando uma Interface do usuário** – como usar o Designer de Xamarin para iOS para criar uma interface do usuário.
-  **Implantação e testes** – como executar e testar seu aplicativo no simulador tvOS e no hardware tvOS real.

## <a name="starting-a-new-xamarintvos-app-in-visual-studio-for-mac"></a>A partir de um App Xamarin.tvOS novo no Visual Studio para Mac

Como mencionado acima, vamos criar um aplicativo da Apple TV chamado `Hello-tvOS` que adiciona um único botão e o rótulo para a tela principal. Quando o botão for clicado, o rótulo exibirá o número de vezes que isso ocorreu.

Para começar, vamos fazer o seguinte:

1. Inicie o Visual Studio para Mac:

    [![](hello-tvos-images/setup01.png "O Visual Studio para Mac")](hello-tvos-images/setup01.png#lightbox)
2. Clique no **nova solução...**  link no canto superior esquerdo da tela para abrir o **novo projeto** caixa de diálogo.
3. Selecione **tvOS** > **aplicativo** > **único aplicativo de exibição** e clique no **próximo** botão:

    [![](hello-tvos-images/setup02.png "Selecione o aplicativo de modo único")](hello-tvos-images/setup02.png#lightbox)
4. Digite `Hello, tvOS` para o **nome do aplicativo**, insira seu **identificador de organização** e clique no **próximo** botão:

    [![](hello-tvos-images/setup04.png "Digite Hello, tvOS")](hello-tvos-images/setup04.png#lightbox)
5. Digite `Hello_tvOS` para o **nome do projeto** e clique no **criar** botão:

    [![](hello-tvos-images/setup03.png "Digite HellotvOS")](hello-tvos-images/setup03.png#lightbox)

O Visual Studio para Mac criar o novo aplicativo Xamarin.tvOS e exibir os arquivos padrão que são adicionados à solução do aplicativo:

 [![](hello-tvos-images/project01.png "A exibição de arquivos padrão")](hello-tvos-images/project01.png#lightbox)

O Visual Studio para Mac usa **soluções** e **projetos**, da mesma maneira exata que o Visual Studio. Uma solução é um contêiner que pode conter um ou mais projetos; projetos podem incluir aplicativos, bibliotecas de suporte, aplicativos de teste, etc. Nesse caso, o Visual Studio para Mac tenha criado uma solução e um projeto de aplicativo para você.

Se desejar, você pode criar código de um ou mais projetos de biblioteca que contém o código comum compartilhado. Esses projetos de biblioteca podem ser consumidos pelo projeto de aplicativo ou compartilhados com outros projetos de aplicativo Xamarin.tvOS (ou xamarin, xamarin e Xamarin.Mac com base no tipo de código), assim como faria se estivesse criando um aplicativo .NET padrão.

## <a name="anatomy-of-a-xamarintvos-app"></a>Anatomia de um aplicativo de Xamarin.tvOS

Se você estiver familiarizado com a programação do iOS, você perceberá muitas semelhanças aqui. Na verdade, tvOS 9 é um subconjunto do iOS 9, para que muitos dos conceitos cruzará aqui.

Vamos examinar os arquivos no projeto:

-   `Main.cs` – isso contém o ponto de entrada principal do aplicativo. Quando o aplicativo é iniciado, ele contém a primeira classe e método que é executado.
-   `AppDelegate.cs` – Este arquivo contém a classe principal do aplicativo que é responsável por escutar eventos do sistema operacional.
-   `Info.plist` – Este arquivo contém as propriedades do aplicativo, como o nome do aplicativo, ícones, etc.
-   `ViewController.cs` – Esta é a classe que representa a janela principal e controla o ciclo de vida dele.
-   `ViewController.designer.cs` – Este arquivo contém código estrutural que ajuda você a integrar a interface do usuário da tela principal.
-  `Main.storyboard` – A interface do usuário para a janela principal. Esse arquivo pode ser criado e mantido pelo Xamarin Designer para iOS.

Nas seções a seguir, vamos dar uma olhada rápida por meio de alguns desses arquivos. Vamos explorá-los em mais detalhes posteriormente, mas é uma boa ideia compreender seus fundamentos agora.

### <a name="maincs"></a>Main.cs

O `Main.cs` arquivo contém um estático `Main` método que cria uma nova instância de aplicativo Xamarin.tvOS e passa o nome da classe que tratará eventos de sistema operacional, que neste caso é a `AppDelegate` classe:

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

O `AppDelegate.cs` arquivo contém nosso `AppDelegate` classe, que é responsável por criar nossa janela e escutar eventos do sistema operacional:

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

Esse código é provavelmente familiarizado, a menos que criamos um aplicativo iOS antes, mas é bastante simples. Vamos examinar as linhas importantes.

Primeiro, vamos dar uma olhada na declaração de variável de nível de classe:

```csharp
public override UIWindow Window {
            get;
            set;
        }

```

O `Window` propriedade fornece acesso para a janela principal. tvOS usa o que é conhecido como o *Model View Controller* padrão (MVC). Em geral, para cada janela que você cria (e para muitas outras coisas dentro do windows) é um controlador, o que é responsável pelo ciclo de vida da janela, como exibir, adicionar novos modos de exibição (controles) a ele, etc.

Em seguida, temos o `FinishedLaunching` método. Este método é executado depois que o aplicativo foi instanciado e é responsável por criar a janela do aplicativo, na verdade e iniciar o processo de exibir o modo de exibição dentro dela. Como o nosso aplicativo usa um Storyboard para definir sua interface do usuário, sem código adicional é necessário aqui.

Há muitos outros métodos que são fornecidos no modelo como `DidEnterBackground` e `WillEnterForeground`. Eles podem ser removidos com segurança se os eventos de aplicativo não estão sendo usados em seu aplicativo.

### <a name="viewcontrollercs"></a>ViewController.cs

O `ViewController` classe é o controlador da nossa janela principal. Isso significa que ele é responsável por ciclo de vida da janela principal. Vamos para examinar isso detalhadamente mais adiante, para agora apenas vamos rápido-lo:

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

O arquivo de designer para a classe de janela principal está vazio no momento, mas ele será automaticamente preenchido pelo Visual Studio para Mac como podemos criar nossa Interface do usuário com o Designer do iOS:

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

Nós geralmente não relacionados a arquivos de designer, se estiverem apenas automaticamente gerenciados pelo Visual Studio para Mac e forneça apenas o código de encanamento requisito que permite acesso aos controles que podemos adicionar qualquer janela ou exibir em nosso aplicativo.

Agora que criamos nosso aplicativo Xamarin.tvOS e temos uma compreensão básica de seus componentes, vamos criar a interface do usuário.

<a name="Creating-the-User-Interface" />

## <a name="creating-the-user-interface"></a>Criando a interface do usuário

Você não precisa usar o Designer de Xamarin para iOS para criar a Interface do usuário para seu aplicativo Xamarin.tvOS, a interface do usuário pode ser criada diretamente no código do c#, mas que está além do escopo deste artigo. Para simplificar, usaremos o iOS Designer para criar nossa interface do usuário em todo o restante deste tutorial.

Para começar a criar sua interface do usuário, vamos clique duas vezes o `Main.storyboard` arquivo o **Gerenciador de soluções** para abri-lo para edição no Designer de iOS:

[![](hello-tvos-images/designer01.png "O arquivo Main.storyboard no Gerenciador de Soluções")](hello-tvos-images/designer01.png#lightbox)

Isso deve iniciar o Designer e a seguinte aparência:

[![](hello-tvos-images/designer02.png "O Designer")](hello-tvos-images/designer02.png#lightbox)

Para obter mais informações sobre o Designer do iOS e como ele funciona, consulte o [Introdução ao Xamarin Designer para iOS](~/ios/user-interface/designer/introduction.md) guia.

Agora podemos começar adicionando controles a superfície de design do nosso aplicativo Xamarin.tvOS.

Faça o seguinte:

1. Localize o **caixa de ferramentas**, que deve ser à direita da superfície de design:

    [![](hello-tvos-images/designer03.png "A caixa de ferramentas")](hello-tvos-images/designer03.png#lightbox)

    Se você não pode localizá-lo aqui, navegue até **exibição > preenche > ferramentas** para exibi-lo.
2. Arraste um **rótulo** do **caixa de ferramentas** à superfície de design:

    [![](hello-tvos-images/designer04.png "Arraste um rótulo da caixa de ferramentas")](hello-tvos-images/designer04.png#lightbox)
3. Clique no **título** propriedade no **teclado de propriedade** e altere o título do botão para `Hello, tvOS` e defina o **tamanho da fonte** a 128:

    [![](hello-tvos-images/designer05.png "Definir o título para Hello, tvOS e defina o tamanho da fonte como 128")](hello-tvos-images/designer05.png#lightbox)
4. Redimensione o rótulo para que todas as palavras são visíveis e colocá-lo centralizado na parte superior da janela:

    [![](hello-tvos-images/designer06.png "Redimensionar e centralizará o rótulo")](hello-tvos-images/designer06.png#lightbox)
5. O rótulo deverá ser restrito a sua posição, para que ele apareça como pretendido. independentemente do tamanho da tela. Para fazer isso, clique no rótulo até a *identificador em forma de T* aparece:

    [![](hello-tvos-images/designer07.png "O identificador em forma de T")](hello-tvos-images/designer07.png#lightbox)
6. Para restringir o rótulo horizontalmente, selecione o quadrado central e arraste-o para a linha vertical tracejada:

    [![](hello-tvos-images/designer08.png "Selecionar o quadrado central")](hello-tvos-images/designer08zoom.png#lightbox)

     O rótulo deve ativar laranja.
7. Selecione o identificador de T na parte superior do rótulo e arraste-o para a borda superior da janela:

    [![](hello-tvos-images/designer09.png "Arraste a alça na borda superior da janela")](hello-tvos-images/designer09.png#lightbox)
8. Em seguida, clique a largura e a altura *identificador bone* conforme ilustrado abaixo:

    [![](hello-tvos-images/designer10.png "A largura e a altura bone identificadores")](hello-tvos-images/designer10.png#lightbox)

     Quando cada *identificador bone* é clicado, selecione largura e altura respectivamente para definir dimensões fixas.
9. Quando concluído, as restrições devem ser semelhantes na guia Layout do painel de propriedades:

    [![](hello-tvos-images/designer11.png "Restrições de exemplo")](hello-tvos-images/designer11.png#lightbox)
8. Arraste um **botão** do **caixa de ferramentas** e coloque-o sob o rótulo.
9. Clique no **título** propriedade o **preenchimento de propriedade** e altere o título do botão para `Click Me`:

    [![](hello-tvos-images/designer12.png "Altere o título de botões para clique Me")](hello-tvos-images/designer12.png#lightbox)
10. Repita as etapas 5 a 8 acima para restringir o botão na janela tvOS. No entanto, em vez de arrastar a alça de T na parte superior da janela (como na etapa &#7;), arraste-o para a parte inferior do rótulo:

    [![](hello-tvos-images/designer14.png "Restringir o botão")](hello-tvos-images/designer14.png#lightbox)
11. Arraste outro rótulo do botão, dimensione-o para ser a mesma largura que o primeiro rótulo e defina seu **alinhamento** para **Center**:

    [![](hello-tvos-images/designer15.png "Arraste outro rótulo do botão, dimensione-o para ser a mesma largura que o primeiro rótulo e defina seu alinhamento ao centro")](hello-tvos-images/designer15.png#lightbox)
12. Como o primeiro botão e rótulo, defina este rótulo para centralizar e fixá-lo no local e o tamanho:

    [![](hello-tvos-images/designer16.png "O rótulo para o local e o tamanho do PIN")](hello-tvos-images/designer16.png#lightbox)
13. Salve suas alterações para a Interface do usuário.

Como você redimensionar e mover os controles, você deve ter notado que o designer fornece dicas úteis snap se baseiam em [diretrizes de Interface humana da Apple TV](https://developer.apple.com/tvos/human-interface-guidelines/). Essas diretrizes ajudarão você a criar aplicativos de alta qualidade que terão uma aparência familiar para os usuários da Apple TV.

Se você examinar o **esboço de documento** seção, observe como o layout e a hierarquia dos elementos que compõem a nossa Interface do usuário são mostrados:

[![](hello-tvos-images/designer17.png "A seção de estrutura de tópicos do documento")](hello-tvos-images/designer17.png#lightbox)

Aqui você pode selecionar itens para editar ou arraste-a para reordenar os elementos de interface do usuário, se necessário. Por exemplo, se um elemento de interface do usuário estava sendo coberto por outro elemento, você poderia arrastá-lo para a parte inferior da lista para torná-lo o item mais alto na janela de.

Agora que temos nossa Interface do usuário criado, é preciso para expor os itens de interface do usuário para que Xamarin.tvOS possa acessar e interagir com eles no código c#.

### <a name="accessing-the-controls-in-the-code-behind"></a>Acessando controles no code-behind

Há duas maneiras de acessar os controles que você adicionou no designer de iOS do código:

* Criar um manipulador de eventos em um controle.
* Nomeando o controle, para que nós poderá referenciá-lo mais tarde.

Quando qualquer uma delas são adicionados, a classe parcial dentro de `ViewController.designer.cs` será atualizada para refletir as alterações. Isso permitirá que você, em seguida, acesse os controles no controlador de exibição.

### <a name="creating-an-event-handler"></a>Criar um manipulador de eventos

Neste aplicativo de exemplo, quando o botão é clicado, desejamos _algo_ acontecer, para um manipulador de eventos deve ser adicionado a um evento específico no botão. Para configurar isso, faça o seguinte:

1. No iOS Xamarin Designer, selecione o botão no controlador de exibição.
2. No painel de propriedades, selecione o **eventos** guia:

    [![](hello-tvos-images/event1.png "Na guia eventos")](hello-tvos-images/event1.png#lightbox)
3. Localize o evento TouchUpInside e dê a ele um manipulador de eventos chamado `Clicked`:

    [![](hello-tvos-images/event2.png "O evento TouchUpInside")](hello-tvos-images/event2.png#lightbox)
4. Quando você pressiona **Enter**, o **ViewController**arquivo. cs será aberta, sugerindo locais para o manipulador de eventos no código. Use as teclas de direção do teclado para definir o local:

    [![](hello-tvos-images/event3.png "Definir o local")](hello-tvos-images/event3.png#lightbox)
5. Isso criará um método parcial, conforme mostrado abaixo:

    [![](hello-tvos-images/event4.png "O método parcial")](hello-tvos-images/event4.png#lightbox)

Agora você está pronto para começar a adicionar código para permitir que o botão de função.

### <a name="naming-a-control"></a>Um controle de nomenclatura

Quando o botão é clicado, o rótulo deve atualizar com base no número de cliques. Para fazer isso, precisaremos acessar o rótulo no código. Isso é feito, dando a ele um nome. Faça o seguinte:

1. Abra o Storyboard e selecione o rótulo na parte inferior do controlador de exibição.
2. No painel de propriedades, selecione o **Widget** guia:

    [![](hello-tvos-images/name1.png "Selecione a guia de Widget")](hello-tvos-images/name1.png#lightbox)
3. Em **identidade > nome**, adicionar `ClickedLabel`:

    [![](hello-tvos-images/name2.png "Definir ClickedLabel")](hello-tvos-images/name2.png#lightbox)

Agora está prontos para começar a atualizar o rótulo!

### <a name="how-controls-are-accessed"></a>Como os controles são acessados

Se você selecionar o `ViewController.designer.cs` no **Solution Explorer** você poderá ver como o o `ClickedLabel` rótulo e o `Clicked` manipulador de eventos foram mapeados para um **tomada** e  **Ação** em c#:

[![](hello-tvos-images/accesscontrol.png "Tomadas e ações")](hello-tvos-images/accesscontrol.png#lightbox)

Você também pode perceber que `ViewController.designer.cs` é uma classe parcial, para que o Visual Studio para Mac não precisa modificar `ViewController.cs` que substituiria as alterações feitas à classe.

Expondo os elementos de interface do usuário dessa maneira permite que você para acessá-los no controlador de exibição.

Normalmente nunca será necessário abrir o `ViewController.designer.cs` por conta própria, ele foi apresentado aqui apenas para fins educativos.

<a name="Writing-the-Code" />

## <a name="writing-the-code"></a>Escrevendo o código

Com nossa Interface do usuário criado e seus elementos de interface do usuário expostos ao código pelas **tomadas** e **ações**, estamos finalmente prontos para escrever o código para fornecer a funcionalidade do programa.

Em nosso aplicativo, toda vez que o primeiro botão é clicado, vamos atualizar nossos rótulo para mostrar o número de vezes que o botão foi clicado. Para fazer isso, é preciso abrir o `ViewController.cs` arquivo para edição, clique duas vezes no **solução preenchimento**:

[![](hello-tvos-images/code01.png "O preenchimento de solução")](hello-tvos-images/code01.png#lightbox)

Primeiro, precisamos criar uma variável de nível de classe em nossa `ViewController` classe para controlar o número de cliques que aconteceram. Edite a definição de classe e torne-a semelhante ao seguinte:

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

Em seguida, na mesma classe (`ViewController`), é preciso substituir o **ViewDidLoad** método e adicionar código para definir a mensagem inicial para nosso rótulo:

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();

    // Set the initial value for the label
    ClickedLabel.Text = "Button has not been clicked yet.";
}
```

Precisamos usar `ViewDidLoad `, em vez de outro método, como `Initialize`, pois `ViewDidLoad ` é chamado *depois* o sistema operacional foi carregado e instanciado a Interface do usuário da `.storyboard` arquivo. Se tentar acessar o controle de rótulo antes do `.storyboard` arquivo foi totalmente carregado e instanciado, obteremos um `NullReferenceException` erro porque o controle de rótulo não seria criado ainda.

Em seguida, precisamos adicionar o código para responder para o usuário clicar no botão. Adicione o seguinte para parcial de classe para que criamos:

```csharp
partial void Clicked (UIButton sender)
{
    ClickedLabel.Text = string.Format("The button has been clicked {0} time{1}.", ++numberOfTimesClicked, (numberOfTimesClicked
```

Esse código será chamado sempre que o usuário clicar em nosso botão.

Tudo no lugar, agora estamos prontos para compilar e testar o nosso aplicativo Xamarin.tvOS.

## <a name="testing-the-application"></a>Testando o aplicativo

É hora de criar e executar o nosso aplicativo para se certificar de que ele é executado conforme o esperado. Podemos criar e executar em uma etapa, ou podemos criá-lo sem executá-lo.

Sempre que criamos um aplicativo, podemos escolher o tipo de compilação que desejamos:

-   **Depurar** – uma compilação de depuração é compilada em um ' arquivo (aplicativo) com metadados extras que permitam depurar o que acontece enquanto o aplicativo está em execução.
-   **Versão** – uma compilação de versão também cria um ' arquivo, mas ele não inclui informações de depuração, portanto é menor e executa mais rapidamente.  

Você pode selecionar o tipo de compilação do **configuração seletor** no canto superior esquerdo do Visual Studio para a tela do Mac:

[![](hello-tvos-images/run01.png "Selecione o tipo de compilação")](hello-tvos-images/run01.png#lightbox)

### <a name="building-the-application"></a>Compilando o aplicativo

Em nosso caso, estamos deseja apenas uma compilação de depuração, portanto, vamos garantir que **depurar** está selecionado. Vamos criar primeiro o nosso aplicativo, ou pressionando **⌘B**, ou do **criar** menu, escolha **compilar tudo**.

Se não houver erros, você verá um **compilação bem-sucedida** mensagem no Visual Studio para a barra de status do Mac. Se houver erros, analise o projeto e certifique-se de que você seguiu as etapas corretamente. Iniciar, confirmando que o seu código (tanto no Xcode e no Visual Studio para Mac) corresponde ao código do tutorial.

### <a name="running-the-application"></a>Executando o aplicativo

Para executar o aplicativo, temos três opções:

-  Pressione **⌘+Enter**.
-  No menu **Executar**, escolha **Depuração**.
-  Clique no botão **Reproduzir** na barra de ferramentas do Visual Studio para Mac (imediatamente acima do **Gerenciador de Soluções**).

O aplicativo criará (se ele ainda não foi criado), iniciar no modo de depuração, o simulador tvOS será iniciado e iniciará o aplicativo e exibir a janela da interface principal:

[![A tela inicial do aplicativo de exemplo](hello-tvos-images/run03.png)](hello-tvos-images/run03.png#lightbox)

Do **Hardware** menu, selecione **Mostrar Apple TV remoto** para que você possa controlar o simulador.

[![](hello-tvos-images/run04.png "Selecione Mostrar Apple TV remoto")](hello-tvos-images/run04.png#lightbox)

Usar o controle remoto do simulador, se você clicar no botão algumas vezes, o rótulo deve ser atualizada com a contagem de:

[![](hello-tvos-images/run05.png "O rótulo de contagem atualizada")](hello-tvos-images/run05.png#lightbox)

Parabéns! Abordamos muitos assuntos aqui, mas se você seguiu neste tutorial do início ao fim, agora você deve ter uma compreensão sólida dos componentes de um aplicativo Xamarin.tvOS, bem como as ferramentas usadas para criá-los.

## <a name="where-to-next"></a>Onde Avançar?

Desenvolver apresenta de aplicativos Apple TV alguns desafios devido a desconexão entre o usuário e a interface (estiver na sala não no lado do usuário) e o tvOS limitações coloca no tamanho do aplicativo e armazenamento.

Como resultado, é altamente recomendável que sua leitura os seguintes documentos antes de ir para o design de um aplicativo Xamarin.tvOS:

- [Introdução às tvOS 9](~/ios/tvos/platform/tvos9.md) – este artigo apresenta todas as APIs e recursos disponíveis no tvOS 9 novos e modificados para desenvolvedores de Xamarin.tvOS.
- [Trabalhando com foco e navegação](~/ios/tvos/app-fundamentals/navigation-focus.md) – os usuários do seu aplicativo Xamarin.tvOS não interagirá com sua interface diretamente como com iOS onde eles tocar imagens na tela do dispositivo, mas indiretamente de toda sala usando Siri remoto. Este artigo aborda o conceito de foco e como ele é usado para controlar a navegação na Interface do usuário do aplicativo um Xamarin.tvOS.
- [Siri remoto e controladores Bluetooth](~/ios/tvos/platform/remote-bluetooth.md) – a principal forma que usuários interagirá com o Apple TV e seu aplicativo Xamarin.tvOS, é por meio de remoto Siri incluído. Se seu aplicativo for um jogo, você pode opcionalmente criar no suporte para 3ª parte, feita para controladores de jogo de Bluetooth do iOS (MFI) em seu aplicativo também. Este artigo aborda o suporte os novos controladores de jogo Siri remoto e Bluetooth em seus aplicativos Xamarin.tvOS.
- [Armazenamento de dados e recursos](~/ios/tvos/app-fundamentals/resources-data-storage.md) – ao contrário de dispositivos iOS, o novo Apple TV não oferece armazenamento persistente, local para tvOS aplicativos. Como resultado, se seu aplicativo Xamarin.tvOS precisa manter informações (como as preferências do usuário) ele deve armazenar e recuperar dados do iCloud. Este artigo aborda o trabalho com recursos e armazenamento de dados persistentes em um aplicativo Xamarin.tvOS.
- [Trabalhando com imagens e ícones](~/ios/tvos/app-fundamentals/icons-images.md) – criando cativante ícones e imagens são uma parte crítica do desenvolvimento de uma experiência de usuário envolventes para seus aplicativos da Apple TV. Este guia aborda as etapas necessárias para criar e incluir os ativos de gráficos necessários para seus aplicativos Xamarin.tvOS.
- [Interface do usuário](~/ios/tvos/user-interface/index.md) – cobertura de experiência de usuário geral (UX), incluindo controles de Interface do usuário (IU), use do Xcode Interface Builder e princípios de design UX ao trabalhar com Xamarin.tvOS.
- [Implantação e testes](~/ios/tvos/deploy-test/index.md) – esta seção aborda tópicos usados para testar um aplicativo, bem como distribuí-lo. Tópicos aqui incluem itens como as ferramentas usadas para depuração, a implantação para testadores e como publicar um aplicativo à loja de aplicativos Apple TV.

Se você tiver problemas ao trabalhar com Xamarin.tvOS, consulte nosso [solução de problemas](~/ios/tvos/troubleshooting.md) saber de documentação para uma lista de problemas e soluções.

## <a name="summary"></a>Resumo

Este artigo fornece um guia rápido para o desenvolvimento de aplicativos para tvOS com o Visual Studio para Mac, criando uma saudação simple, o aplicativo tvOS. Ele abordados os conceitos básicos do dispositivo tvOS provisionamento, criação de interface, codificação para tvOS e teste no simulador tvOS.


## <a name="related-links"></a>Links relacionados

- [Amostras do tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS guias de Interface Humana](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guia de programação de aplicativo para tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
- [Criando aplicativos para tvOS com Xamarin (vídeo)](https://university.xamarin.com/lightninglectures/tvos-with-xamarin)
