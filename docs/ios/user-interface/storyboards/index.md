---
title: Introdução aos storyboards no Xamarin. iOS
description: Este documento fornece uma introdução aos storyboards no Xamarin. iOS. Ele descreve como um Storyboard é usado para definir uma interface do usuário, continuações e como usar o designer do iOS para editar arquivos de storyboard.
ms.prod: xamarin
ms.assetid: A3339BD2-9F56-7965-25F5-4B7C991EB775
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/22/2017
ms.openlocfilehash: 3d9531bbaa38a0a0a9bb42d22c79a63fda75c449
ms.sourcegitcommit: 926f9a44065b41c7d74794a94bee66b61e903bf2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/12/2020
ms.locfileid: "79134002"
---
# <a name="introduction-to-storyboards-in-xamarinios"></a>Introdução aos storyboards no Xamarin. iOS

Neste guia, explicaremos o que é um storyboard e examinaremos alguns dos principais componentes – como continuações. Veremos como os storyboards podem ser criados e usados e quais vantagens eles têm para um desenvolvedor.

Antes de o formato de arquivo de storyboard ter sido introduzido pela Apple como uma representação visual da interface do usuário de um aplicativo iOS, os desenvolvedores criaram arquivos XIB para cada controlador de exibição e programaram a navegação entre cada exibição manualmente.  O uso de um storyboard permite que o desenvolvedor defina ambos os controladores de exibição e a navegação entre eles em uma superfície de design e ofereça edição WYSIWYG da interface do usuário do aplicativo.

Um storyboard pode ser criado, aberto e editado com o Xamarin iOS designer. Este guia também explica como usar o designer para criar seus storyboards ao usar C# o para programar a navegação.

## <a name="requirements"></a>{1&gt;{2&gt;Requisitos&lt;2}&lt;1}

Os storyboards podem ser usados com o Xcode, o designer do iOS no Visual Studio para Mac e o Visual Studio 2019 com as cargas de trabalho do Xamarin instaladas.

## <a name="what-is-a-storyboard"></a>O que é um storyboard?

Um Storyboard é a representação visual de todas as telas em um aplicativo. Ele contém uma sequência de cenas, com cada cena que representa um *controlador de exibição* e suas *exibições*. Essas exibições podem conter objetos e [controles](~/ios/user-interface/controls/index.md) que permitirão que o usuário interaja com seu aplicativo. Essa coleção de exibições e controles (ou *subexibições*) é conhecida como *hierarquia de exibição de conteúdo*. As cenas são conectadas por objetos transição, que representam uma transição entre os controladores de exibição. Isso normalmente é obtido com a criação de um transição entre um objeto na exibição inicial e a exibição de conexão. As relações na superfície de design são ilustradas na imagem abaixo:

 [![](images/storyboardsview.png "The relationships on the design surface are illustrated in this image")](images/storyboardsview.png#lightbox)

Como mostrado, o storyboard fará o layout de cada um dos seus bastidores com conteúdo já renderizado e ilustra as conexões entre eles.  Vale a pena observar neste ponto que, quando falamos sobre cenas em um iPhone, é seguro pressupor que uma *cena* no storyboard seja igual a uma *tela* de conteúdo no dispositivo. No entanto, com um iPad, é possível ter várias cenas exibidas de uma vez – por exemplo, usando um controlador de exibição popover.

Há muitas vantagens em usar storyboards para criar a interface do usuário do aplicativo, especialmente ao usar o Xamarin. Em primeiro lugar, é uma representação visual da interface do usuário, pois todos os objetos – incluindo [controles personalizados](~/ios/user-interface/designer/ios-designable-controls-overview.md) – são renderizados em tempo de design.
Isso significa que, antes de criar ou implantar seu aplicativo, você pode visualizar sua aparência e fluxo. Veja a imagem acima, por exemplo. Podemos perceber de uma visão rápida da superfície de design de quantas cenas há, o layout de cada exibição e como tudo está relacionado. Isso é o que torna os storyboards tão poderosos.

Os eventos são mais gerenciáveis com storyboards, especialmente ao usar o designer do iOS. A maioria dos controles de interface do usuário terá uma lista de possíveis eventos no Painel de Propriedades. O manipulador de eventos pode ser adicionado aqui e concluído em um método parcial na classe View Controllers.

O conteúdo de um Storyboard é armazenado como um arquivo XML. No momento da compilação, qualquer arquivo de `.storyboard` é compilado em arquivos binários conhecidos como nibs. Em tempo de execução, esses nibs são inicializados e instanciados para criar novos modos de exibição.

## <a name="segues"></a>Continuações

Um objeto *transição*, ou *transição*, é usado no desenvolvimento do IOS para representar uma transição entre cenas. Para criar um transição, mantenha pressionada a tecla **Ctrl** e clique-arraste de uma cena para outra. À medida que arrastamos nosso mouse, um conector azul é exibido, indicando onde o transição será levado como demonstrado na imagem abaixo:

 [![](images/createsegue.png "A blue connector appears, indicating where the segue will lead as demonstrated in this image")](images/createsegue.png#lightbox)

No mouse, um menu será exibido, permitindo-nos escolher a ação para nosso transição. Pode ser semelhante às imagens abaixo:

**Classes anteriores ao Ios 8 e tamanho**:

[![](images/segue1.png "The Action Segue dropdown without Size Classes")](images/segue1.png#lightbox)

**Ao usar classes de tamanho e continuações adaptável**:

[![](images/16new.png "The Action Segue dropdown with Size Classes")](images/16new.png#lightbox)

> [!IMPORTANT]
> Se você estiver usando o VMWare para sua máquina virtual do Windows, CTRL-clique será mapeado como o botão _direito_ do mouse, por padrão. Para criar um transição, edite suas preferências de teclado por meio de **preferências** > **teclado & mouse** > **atalhos de mouse** e remapeie o **botão secundário** , conforme ilustrado abaixo:
>
> [![](images/image22.png "Keyboard and Mouse preference settings")](images/image22.png#lightbox)
>
> Agora você deve ser capaz de adicionar um transição entre seus controladores de exibição normalmente.

Há diferentes tipos de transições, cada uma dando controle sobre como um novo controlador de exibição é apresentado ao usuário e como ele interage com outros controladores de exibição no storyboard. Eles são explicados abaixo. Também é possível subclasse de um objeto transição para implementar uma transição personalizada:

- **Mostrar/enviar por** Push – um push transição adiciona o controlador de exibição à pilha de navegação. Ele pressupõe que o controlador de exibição que originou o Push faz parte do mesmo controlador de navegação que o controlador de exibição que está sendo adicionado à pilha. Isso faz a mesma coisa que `pushViewController` e geralmente é usado quando há alguma relação entre os dados nas telas. O uso do transição de envio por push fornece a você o luxo de ter uma barra de navegação com um botão voltar e um título adicionado a cada exibição na pilha, permitindo a navegação detalhada na hierarquia de exibição.
- **Modal** – um transição modal crie uma relação entre quaisquer dois controladores de exibição em seu projeto, com a opção de uma transição animada sendo mostrada. O controlador de exibição filho obscurecerá completamente o controlador de exibição pai quando colocado na exibição. Ao contrário de um transição de push, que adiciona um botão voltar para nós; ao usar um `DismissViewController` modal transição deve ser usado para retornar ao controlador de exibição anterior.
- **Personalizado** – qualquer transição personalizada pode ser criada como uma subclasse de `UIStoryboardSegue`.
- **Desenrolar** – um transição de liberação pode ser usado para navegar de volta por um transição de Push ou modal – por exemplo, ignorando o controlador de exibição apresentado de modo restrito. Além disso, você pode desenrolar por não apenas um, mas uma série de continuações de push e modal e voltar várias etapas em sua hierarquia de navegação com uma única ação de desenrolamento. Para entender como usar um desenrolamento de transição no iOS, leia a receita [criando continuações de desenrolamento](https://github.com/xamarin/recipes/tree/master/Recipes/ios/general/storyboard/unwind_segue) .
- De **origem** – um transição de origem informativa indica a cena que contém o controlador de exibição inicial e, portanto, a exibição que o usuário verá primeiro. Ele é representado pelo transição mostrado abaixo:  

    [![](images/sourcelesssegue.png "A sourceless segue")](images/sourcelesssegue.png#lightbox)

### <a name="adaptive-segue-types"></a>Tipos de transição adaptável

 o iOS 8 introduziu [classes de tamanho](~/ios/user-interface/storyboards/unified-storyboards.md#size-classes) para permitir que um arquivo de storyboard do IOS funcione com todos os tamanhos de tela disponíveis, permitindo que os desenvolvedores criem uma interface do usuário para todos os dispositivos IOS. Por padrão, todos os novos aplicativos Xamarin. iOS usarão classes de tamanho. Para usar classes de tamanho de um projeto mais antigo, consulte o guia [introdução ao storyboards unificados](~/ios/user-interface/storyboards/unified-storyboards.md) .

Qualquer aplicativo usando classes de tamanho também usará o novo [*continuações adaptável*](~/ios/user-interface/storyboards/unified-storyboards.md). Ao usar classes de tamanho, lembre-se de que não estamos especificando diretamente o clima que estamos usando um iPhone ou iPad. Em outras palavras, estamos criando uma interface do usuário que sempre terá a mesma aparência, independentemente da quantidade de espaço real com a qual precisa trabalhar. O continuações Adaptive funciona por julgamento o ambiente e determinando a melhor maneira de apresentar conteúdo. Os continuações adaptáveis são mostrados abaixo:

[![](images/adaptivesegue.png "The Adaptive Segues dropdown")](images/adaptivesegue.png#lightbox)

|Transição|Descrição|
|--- |--- |
|Mostrar|Isso é muito semelhante a um transição de push, mas leva o conteúdo da tela para conta.|
|Mostrar detalhes|Se o aplicativo exibir uma exibição mestre e de detalhes (por exemplo, em um controlador de exibição de divisão em um iPad), o conteúdo substituirá a exibição de detalhes. Se o aplicativo exibir apenas o mestre ou detalhes, o conteúdo substituirá a parte superior da pilha do controlador de exibição.|
|Apresentação|Isso é semelhante ao transição modal e permite a seleção de estilos de apresentação e de transição.|
|Apresentação do popover|Isso apresenta conteúdo como um popover|

### <a name="transferring-data-with-segues"></a>Transferindo dados com continuações

Os benefícios de um transição não terminam com transições. Eles também podem ser usados para gerenciar a transferência de dados entre controladores de exibição. Isso é feito substituindo o método `PrepareForSegue` no controlador de exibição inicial e manipulando os dados por si mesmos. Quando o transição é disparado – por exemplo, com um pressionamento de botão – o aplicativo chamará esse método, fornecendo uma oportunidade de preparar o novo controlador de exibição *antes que* ocorra qualquer navegação. O código a seguir, do exemplo [Phoneword](https://docs.microsoft.com/samples/xamarin/ios-samples/hello-ios) , demonstra isso:

```csharp
public override void PrepareForSegue (UIStoryboardSegue segue,
NSObject sender)
{
    base.PrepareForSegue (segue, sender);

    var callHistoryController = segue.DestinationViewController
                                  as CallHistoryController;

    if (callHistoryController != null) {
        callHistoryController.PhoneNumbers = PhoneNumbers;
    }
}
```

Neste exemplo, o método `PrepareForSegue` será chamado quando o transição for disparado pelo usuário. Primeiro, precisamos criar uma instância do controlador de exibição ' receptor ' e defini-la como o controlador de exibição de destino do transição. Isso é feito pela linha de código abaixo:

```csharp
var callHistoryController = segue.DestinationViewController as CallHistoryController;
```

O método agora tem a capacidade de definir propriedades no `DestinationViewController`. Neste exemplo, tiramos proveito disso passando uma lista chamada `PhoneNumbers` para a `CallHistoryController` e atribuí-la a um objeto com o mesmo nome:

```csharp
if (callHistoryController != null) {
        callHistoryController.PhoneNumbers = PhoneNumbers;
    }
```

Depois que a transição for concluída, o usuário verá o `CallHistoryController` com a lista preenchida.

## <a name="adding-a-storyboard-to-a-non-storyboard-project"></a>Adicionando um storyboard a um projeto que não seja um storyboard

Ocasionalmente, talvez seja necessário adicionar um storyboard a um arquivo anterior que não seja um Storyboard. Ao fazer isso no Visual Studio para Mac pode ser simplificado seguindo as etapas abaixo:

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

1. Crie um novo arquivo de storyboard navegando até o **arquivo > novo arquivo > iOS > storyboard**, conforme ilustrado abaixo:

    [![](images/new-storyboard-xs.png "The new file dialog")](images/new-storyboard-xs.png#lightbox)

2. Adicione o nome do storyboard à seção da **interface principal** do **info. plist**, conforme mostrado abaixo:

    [![](images/infoplist.png "The Info.plist editor")](images/infoplist.png#lightbox)

    Isso faz o equivalente à instanciação do controlador de exibição inicial no método `FinishedLaunching` dentro do delegado do aplicativo. Com essa opção definida, o aplicativo instancia uma janela (veja abaixo), carrega o storyboard principal e atribui uma instância do controlador de exibição inicial do storyboard (aquele ao lado do transição sem origem) como a propriedade `RootViewController` da janela e torna a janela visível na tela.

3. No `AppDelegate`, substitua o método de `Window` padrão, pelo seguinte código para implementar a Propriedade Window:

    ```csharp
    public override UIWindow Window {
        get;
        set;
    }
    ```

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. Crie um novo arquivo de storyboard clicando com o botão direito do mouse no projeto para **adicionar > novo arquivo > iOS > storyboard vazio**, conforme ilustrado abaixo:

    [![](images/new-storyboard-vs.png "The new item dialog")](images/new-storyboard-vs.png#lightbox)

2. Adicione o nome do storyboard à seção da **interface principal** do aplicativo IOS, conforme mostrado abaixo:

    [![](images/ios-app.png "The Info.plist editor")](images/ios-app.png#lightbox)

    Isso faz o equivalente à instanciação do controlador de exibição inicial no método `FinishedLaunching` dentro do delegado do aplicativo. Com essa opção definida, o aplicativo instancia uma janela (veja abaixo), carrega o storyboard principal e atribui uma instância do controlador de exibição inicial do storyboard (aquele ao lado do transição sem origem) como a propriedade `RootViewController` da janela e torna a janela visível na tela.

3. No `AppDelegate`, substitua o método de `Window` padrão, pelo seguinte código para implementar a Propriedade Window:

    ```csharp
    public override UIWindow Window {
        get;
        set;
    }
    ```

-----

## <a name="creating-a-storyboard-with-xcode"></a>Criando um storyboard com o Xcode

Um storyboard pode ser criado e modificado usando o Xcode para uso em seus aplicativos iOS desenvolvidos com Visual Studio para Mac.

Os storyboards totalmente substituem arquivos XIB individuais em seu projeto, no entanto, os controladores de exibição individuais em um storyboard ainda podem ser instanciados usando `Storyboard.InstantiateViewController`.

Às vezes, os aplicativos têm requisitos especiais que não podem ser tratados com as transições de storyboard internas fornecidas pelo designer. Por exemplo, se tivéssemos de criar um aplicativo que inicie telas diferentes do mesmo botão, dependendo do estado atual de um aplicativo, talvez queiramos criar uma instância dos controladores de exibição manualmente e programar a transição.

A captura de tela abaixo mostra dois controladores de exibição em nossa superfície de design sem nenhum transição entre eles. A próxima seção explicará como essa transição pode ser configurada no código.

1. Adicionar um _storyboard vazio do iPhone_ a um projeto de projeto existente:

    [![](images/add-storyboard2.png "Adding storyboard")](images/add-storyboard2.png#lightbox)

2. Clique com o botão direito do mouse no arquivo de storyboard e selecione **abrir com > Xcode Interface Builder** para abri-lo no Xcode.

    *Se preferir usar o compilador de interface do Xcode por padrão, você poderá escolher na Visual Studio para Mac preferências em **projetos > Ios**:*

![](images/set-preferred-designer-tool.png "Selecting the preferred designer tool")

3. No Xcode, abra a biblioteca (por meio da **exibição > mostrar biblioteca** ou *Shift + Command + L*) para mostrar uma lista de objetos que podem ser adicionados ao storyboard. Adicione um `Navigation Controller` ao storyboard arrastando o objeto da lista para o storyboard. Por padrão, a `Navigation Controller` fornecerá duas telas; a tela à direita é uma `TableViewController` que iremos substituir por uma exibição mais simples, para que possa ser removida clicando na exibição e pressionando a tecla Delete.

    [![](images/add-navigation-controller.png "Adding a NavigationController from the Library")](images/add-navigation-controller.png#lightbox)

4. Esse controlador de exibição terá sua própria classe personalizada e também precisará de sua própria ID de storyboard. Ao clicar na caixa acima dessa exibição recém-adicionada, haverá três ícones, o mais à esquerda, que representa o controlador de exibição para a exibição. Ao selecionar esse ícone, você pode definir os valores de classe e ID na guia identidade do painel direito. Defina esses valores como `MainViewController` e certifique-se de verificar `Use Storyboard ID`.

    [![](images/identity-panel.png "Setting the MainViewController in the identity panel")](images/identity-panel.png#lightbox)

5. Usando a biblioteca novamente, arraste um controlador de exibição para a tela. Isso será definido como o controlador de exibição raiz. Segurando a chave de controle, clique e arraste do controlador de navegação à esquerda para o controlador de exibição recém-adicionado à direita e clique em *controlador de exibição raiz* no menu.

    [![](images/add-view-controller.png "Adding a NavigationController from the Library and setting the MainViewController as a Root View Controller")](images/add-view-controller.png#lightbox)

6. Este aplicativo navegará para outra exibição, portanto, adicione mais uma exibição ao storyboard, assim como antes. Vamos chamá-lo de um `PinkViewController`, e esses valores podem ser definidos da mesma maneira que com o `MainViewController`.

    [![](images/add-additional-view-controller.png "Adding an additional View Controller")](images/add-additional-view-controller.png#lightbox)

7. Como o controlador de exibição terá um plano de fundo rosa, essa propriedade poderá ser definida no painel atributos usando a lista suspensa ao lado de `Background`.

    [![](images/set-pink-background.png "Adding an additional View Controller")](images/set-pink-background.png#lightbox)

8. Como queremos que o `MainViewController` navegue até o `PinkViewController`, o primeiro precisará de um botão com o qual interagir. Usando a biblioteca, podemos adicionar um botão à `MainViewController`.

    [![](images/add-button.png "Adding a Button to the MainViewController")](images/add-button.png#lightbox)

O storyboard está concluído, mas se implantarmos o projeto agora, obteremos uma tela em branco. Isso porque ainda precisamos dizer ao IDE para usar nosso storyboard e configurar um controlador de exibição raiz para servir como a primeira exibição. Normalmente, isso pode ser feito por meio de nossas opções de projeto, como mostrado acima. No entanto, neste exemplo, atingiremos o mesmo resultado no código, adicionando o seguinte ao **AppDelegate**:

```csharp
public partial class AppDelegate : UIApplicationDelegate
{
    UIWindow window;
    public static UIStoryboard Storyboard = UIStoryboard.FromName ("MainStoryboard", null);
    public static UIViewController initialViewController;

    public override bool FinishedLaunching (UIApplication app, NSDictionary options)
    {
        window = new UIWindow (UIScreen.MainScreen.Bounds);

        initialViewController = Storyboard.InstantiateInitialViewController () as UIViewController;

        window.RootViewController = initialViewController;
        window.AddSubview(initialViewController.View);
        window.MakeKeyAndVisible ();
        return true;
    }
}
```

Isso é uma grande quantidade de código, mas apenas algumas linhas não são familiares. Primeiro, registramos nosso storyboard com o **AppDelegate** passando o nome do storyboard, **MainStoryboard**. Em seguida, dizemos ao aplicativo para instanciar um controlador de exibição inicial do storyboard chamando `InstantiateInitialViewController` em nosso storyboard e definimos esse controlador de exibição como controlador de exibição raiz do nosso aplicativo. Esse método determina a primeira tela que o usuário vê e cria uma nova instância desse controlador de exibição.

Observe no painel da solução que o IDE criou uma classe `MainViewcontroller.cs` e sua `corresponding designer.cs` quando adicionamos o nome da classe à Painel de Propriedades na etapa 4. Podemos ver que essa classe criou um Construtor especial que inclui uma classe base:

```csharp
public MainViewController (IntPtr handle) : base (handle)
{
}
```

Ao criar um storyboard usando o Xcode, o IDE adicionará automaticamente o atributo [[Register]](xref:Foundation.RegisterAttribute) na parte superior da classe `designer.cs` e passará um identificador de cadeia de caracteres, que é idêntico à ID do storyboard especificado na etapa anterior. Isso vinculará o C# à cena relevante no storyboard.

```csharp
[Register ("MainViewController")]
public partial class MainViewController : UIViewController
{
    public MainViewController (IntPtr handle) : base (handle)
    {
    }
    //...
}
```

Para obter mais informações sobre como registrar classes e métodos, consulte a documentação do [registrador de tipos](https://docs.microsoft.com/xamarin/ios/internals/registrar) .

A última etapa dessa classe é conectar o botão e a transição para o controlador de exibição rosa. Criaremos uma instância do `PinkViewController` do storyboard; em seguida, programaremos um transição de push com `PushViewController`, conforme ilustrado pelo código de exemplo abaixo:

```csharp
public partial class MainViewController : UIViewController
{
    UIViewController pinkViewController;

    public MainViewController (IntPtr handle) : base (handle)
    {
    }

    public override void AwakeFromNib ()
    {
        // Called when loaded from xib or storyboard.
        this.Initialize ();
    }

    public void Initialize()
    {
        //Instantiating View Controller with Storyboard ID 'PinkViewController'
        pinkViewController = Storyboard.InstantiateViewController ("PinkViewController") as PinkViewController;
    }

    public override void ViewDidLoad ()
    {
        base.ViewDidLoad ();

        //When we push the button, we will push the pinkViewController onto our current Navigation Stack
        PinkButton.TouchUpInside += (o, e) =&gt;
        {
            this.NavigationController.PushViewController (pinkViewController, true);
        };
    }
}
```

Executar o aplicativo produz um aplicativo de duas telas:

![](images/finishedstoryboard.png "Sample app run screens")

## <a name="conditional-segues"></a>Continuações condicional

Geralmente, a mudança de um controlador de exibição para o próximo depende de uma determinada condição. Por exemplo, se estivermos fazendo uma tela de logon simples, só desejaremos mover para a próxima tela *se* o nome de usuário e a senha tiverem sido verificados.

No próximo exemplo, adicionaremos um campo de senha ao exemplo acima. O usuário só poderá acessar o *PinkViewController* se inserir a senha correta; caso contrário, um erro será exibido.

Antes de começar, siga as etapas 1 a 8 acima. Nestas etapas, criamos nosso storyboard, começamos a criar nossa interface do usuário e dizem ao nosso representante de aplicativo qual controlador de exibição usar como RootViewController.

1. Agora, vamos criar nossa interface do usuário e adicionar as exibições adicionais listadas ao `MainViewController` para que pareçam com isso na captura de tela abaixo:

    - UITextField
        - Name: PasswordTextField
        - Espaço reservado: ' Insira a senha secreta '
    - UILabel
        - Texto: ' erro: senha incorreta. Você não deve passar! '
        - Cor: vermelho
        - Alinhamento: centro
        - Linhas: 2
        - Caixa de seleção ' Hidden ' marcada    

    [![](images/passwordvc.png "Center Lines")](images/passwordvc.png#lightbox)

2. Crie um transição entre o botão ir para rosa e o controlador de exibição pressionando Ctrl-arrastar do *PinkButton* para o *PinkViewController*e selecionando **Push** no mouse-up.

3. Clique no transição e dê a ele o *identificador* `SegueToPink`:

    [![](images/namesegue.png "Click on the Segue and give it the Identifier SegueToPink")](images/namesegue.png#lightbox)  

4. Por fim, adicione o seguinte método ShouldPerformSegue à classe `MainViewController`:

    ```csharp
    public override bool ShouldPerformSegue (string segueIdentifier, NSObject sender)
    {

        if(segueIdentifier == "SegueToPink"){
            if (PasswordTextField.Text == "password") {
                PasswordTextField.ResignFirstResponder ();
                return true;
            }
            else{
                ErrorLabel.Hidden = false;
                return false;
            }
        }
        return base.ShouldPerformSegue (segueIdentifier, sender);
    }
    ```

Neste código, combinamos o segueIdentifier ao nosso `SegueToPink` transição, portanto, podemos testar uma condição; uma senha válida neste caso. Se nossa condição retornar `true`, o transição será executado e apresentará o `PinkViewController`. Se `false`, o novo controlador de exibição não será apresentado.

Podemos aplicar essa abordagem a qualquer transição nesse controlador de exibição, verificando o argumento segueIdentifier para o método ShouldPerformSegue. Nesse caso, temos apenas um identificador transição – `SegueToPink`.

Consulte a solução storyboards. Conditional no exemplo de [storyboards manual](https://docs.microsoft.com/samples/xamarin/ios-samples/manualstoryboard) para obter um exemplo de trabalho.

<a name="Using-Storyboard-References" />

## <a name="using-storyboard-references"></a>Usando referências de storyboard

Uma referência de storyboard permite que você use um design de storyboard grande e complexo e divida-o em storyboards menores que são referenciados do original, removendo a complexidade e tornando os storyboards individuais resultantes mais fáceis de criar e manter.

Além disso, uma referência de storyboard pode fornecer uma _âncora_ para outra cena dentro do mesmo storyboard ou de uma cena específica em um diferente.

<a name="Referencing-an-External-Storyboard" />

### <a name="referencing-an-external-storyboard"></a>Fazendo referência a um storyboard externo

Para adicionar uma referência a um storyboard externo, faça o seguinte:

1. Na **Gerenciador de soluções**, clique com o botão direito do mouse no nome do projeto e selecione **Adicionar** > **novo arquivo...**  > **Ios** > **storyboard**. Insira um **nome** para o novo storyboard e clique no botão **novo** :

    [![](images/ref01.png "The New File Dialog")](images/ref01.png#lightbox)

2. Projete o layout das cenas do novo storyboard como faria normalmente e salve suas alterações:

    [![](images/ref02.png "The layout of the new scene")](images/ref02.png#lightbox)

3. Abra o storyboard ao qual você vai adicionar a referência no designer do iOS.

4. Arraste uma **referência de storyboard** da **caixa de ferramentas** para a design Surface:

    [![](images/ref03.png "A Storyboard Reference")](images/ref03.png#lightbox)

5. Na guia **widget** do Gerenciador de **Propriedades**, selecione o nome do **storyboard** que você criou acima:

    [![](images/ref04.png "The Widget tab")](images/ref04.png#lightbox)

6. Controle-clique em um widget de interface do usuário (como um botão) em uma cena existente e crie um novo transição para a **referência do storyboard** que você acabou de criar:

    [![](images/ref05.png "Creating a segue")](images/ref05.png#lightbox)

7. No menu pop-up, selecione **Mostrar** para concluir o transição:

    [![](images/ref06.png "Selecting Show to complete the Segue")](images/ref06.png#lightbox)

8. Salve as alterações no storyboard.

Quando o aplicativo é executado e o usuário clica no elemento da interface do usuário do qual você criou o transição, o controlador de exibição inicial do storyboard externo especificado na referência do storyboard será exibido.

<a name="Referencing-a-Specific-Scene-in-an-External-Storyboard" />

### <a name="referencing-a-specific-scene-in-an-external-storyboard"></a>Referenciando uma cena específica em um storyboard externo

Para adicionar uma referência a uma cena específica um storyboard externo (e não o controlador de exibição inicial), faça o seguinte:

1. Na **Gerenciador de soluções**, clique duas vezes no storyboard externo para abri-lo para edição.

2. Adicione uma nova cena e projete seu layout como faria normalmente:

    [![](images/ref07.png "The new scene layout")](images/ref07.png#lightbox)

3. Na guia **widget** do Gerenciador de **Propriedades**, insira uma **ID de storyboard** para o controlador de exibição da nova cena:

    [![](images/ref08.png "Enter a Storyboard ID for the new Scenes View Controller")](images/ref08.png#lightbox)

4. Abra o storyboard ao qual você vai adicionar a referência no designer do iOS.

5. Arraste uma **referência de storyboard** da **caixa de ferramentas** para a design Surface:

    [![](images/ref03.png "A Storyboard Reference")](images/ref03.png#lightbox)

6. Na guia **widget** do Gerenciador de **Propriedades**, selecione o nome do **storyboard** e a ID de **referência** (ID do storyboard) da cena que você criou acima:

    [![](images/ref09.png "The Widget tab ")](images/ref09.png#lightbox)

7. Controle-clique em um widget de interface do usuário (como um botão) em uma cena existente e crie um novo transição para a **referência do storyboard** que você acabou de criar:

    [![](images/ref10.png "Creating a segue")](images/ref10.png#lightbox)

8. No menu pop-up, selecione **Mostrar** para concluir o transição:

    [![](images/ref06.png "Selecting Show to complete the Segue")](images/ref06.png#lightbox)

9. Salve as alterações no storyboard.

Quando o aplicativo é executado e o usuário clica no elemento de interface do usuário do qual você criou o transição, a cena com a **ID de storyboard** fornecida do storyboard externo especificado na referência do storyboard será exibida.

<a name="Referencing-a-Specific-Scene-in-the-Same-Storyboard" />

### <a name="referencing-a-specific-scene-in-the-same-storyboard"></a>Fazendo referência a uma cena específica no mesmo storyboard

Para adicionar uma referência a uma cena específica do mesmo storyboard, faça o seguinte:

1. Na **Gerenciador de soluções**, clique duas vezes no storyboard para abri-lo para edição.

2. Adicione uma nova cena e projete seu layout como faria normalmente:

    [![](images/ref11.png "The new scene layout")](images/ref11.png#lightbox)

3. Na guia **widget** do Gerenciador de **Propriedades**, insira uma **ID de storyboard** para o controlador de exibição da nova cena:

    [![](images/ref12.png "The Widget tab")](images/ref12.png#lightbox)

4. Arraste uma **referência de storyboard** da **caixa de ferramentas** para a design Surface:

   [![](images/ref03.png "A Storyboard Reference")](images/ref03.png#lightbox)

5. Na guia **widget** do Gerenciador de **Propriedades**, selecione **ID de referência** (ID do storyboard) da cena que você criou acima:

    [![](images/ref13.png "The Widget tab")](images/ref13.png#lightbox)

6. Controle-clique em um widget de interface do usuário (como um botão) em uma cena existente e crie um novo transição para a **referência do storyboard** que você acabou de criar:

    [![](images/ref14.png "Creating a segue")](images/ref14.png#lightbox)

7. No menu pop-up, selecione **Mostrar** para concluir o transição:

    [![](images/ref06.png "Selecting Show to complete the Segue")](images/ref06.png#lightbox)

8. Salve as alterações no storyboard.

Quando o aplicativo é executado e o usuário clica no elemento da interface do usuário do qual você criou o transição, a cena com a **ID do storyboard** fornecida no mesmo storyboard especificado na referência do storyboard será exibida.

## <a name="summary"></a>Resumo

Este artigo apresenta o conceito de storyboards e como eles podem ser benéficos no desenvolvimento de aplicativos iOS. Ele aborda cenas, exibir controladores, exibições e exibir hierarquias e como os bastidores são vinculados junto com tipos diferentes de continuações.  Ele também explora a instanciação de controladores de exibição manualmente de um storyboard e a criação de continuações condicional.

## <a name="related-links"></a>Links Relacionados

- [Storyboard manual (exemplo)](https://docs.microsoft.com/samples/xamarin/ios-samples/manualstoryboard/)
- [Introdução ao designer do iOS](~/ios/user-interface/designer/introduction.md)
- [Convertendo em storyboards](https://developer.apple.com/library/ios/#releasenotes/Miscellaneous/RN-AdoptingStoryboards/)
- [Referência de classe UIStoryboard](https://developer.apple.com/library/ios/#documentation/UIKit/Reference/UIStoryboard_Class/Reference/Reference.html)
