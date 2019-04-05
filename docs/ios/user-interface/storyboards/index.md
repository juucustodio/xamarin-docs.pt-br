---
title: Introdução ao Storyboards no xamarin. IOS
description: Este documento fornece uma introdução aos storyboards no xamarin. IOS. Ele descreve como um storyboard é usado para definir uma interface de usuário, segues e como usar o Designer do iOS para editar arquivos de storyboard.
ms.prod: xamarin
ms.assetid: A3339BD2-9F56-7965-25F5-4B7C991EB775
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/22/2017
ms.openlocfilehash: f24be635afcba181efcab85d81a984d93dae4bc8
ms.sourcegitcommit: 650458de1d362cd7de174cacef7838f0e74426f3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/15/2019
ms.locfileid: "58071106"
---
# <a name="introduction-to-storyboards-in-xamarinios"></a>Introdução ao Storyboards no xamarin. IOS

Neste guia, explicaremos o que um Storyboard é e examinar alguns dos principais componentes – como transições. Vamos examinar como os Storyboards podem ser criados e usados, e quais são as vantagens que eles têm para um desenvolvedor.

Antes do formato de arquivo de Storyboard foi introduzido pela Apple, como uma representação visual da interface do usuário de um aplicativo iOS, os desenvolvedores criou arquivos XIB para cada controlador de exibição e programado a navegação entre cada exibição manualmente.  Usar um Storyboard permite ao desenvolvedor definir controladores de exibição e a navegação entre elas em uma superfície de design e oferece a edição WYSIWYG da interface do usuário do aplicativo.

Pode ser criado, aberto e editado com o Designer do iOS Xamarin um Storyboard. Este guia será também instruções passo a passo como usar o Designer para criar storyboards usando o c# para programar a navegação.


## <a name="requirements"></a>Requisitos

Storyboards podem ser usados com o iOS Designer no Visual Studio para Mac ou com o Visual Studio 2017 com as cargas de trabalho do Xamarin instaladas.

## <a name="what-is-a-storyboard"></a>O que é um Storyboard?

Um Storyboard é a representação visual das telas em um aplicativo. Ele contém uma sequência de cenas, com cada cena que representa uma *controlador de exibição* e seu *modos de exibição*. Essas exibições podem conter objetos e [controles](~/ios/user-interface/controls/index.md) que permitirá que o usuário interagir com seu aplicativo. Esta coleção de exibições e controles (ou *subexibições*) é conhecido como um *hierarquia de exibição de conteúdo*. Nos bastidores são conectados por segue de objetos, que representam uma transição entre os controladores de exibição. Normalmente, isso é feito criando um segue entre um objeto na exibição inicial e o modo de exibição está se conectando. As relações na superfície de design são ilustradas na imagem abaixo:

 [![](images/storyboardsview.png "As relações na superfície de design são ilustradas nesta imagem")](images/storyboardsview.png#lightbox)

Conforme mostrado, o storyboard irá dispor cada uma das suas cenas com conteúdo que já foi processado e ilustra as conexões entre eles.  Vale a pena observar neste ponto, que, quando falamos sobre as cenas em um iPhone, é seguro supor que um *cena* no storyboard é igual a um *tela* de conteúdo no dispositivo. No entanto, com um iPad, é possível ter várias cenas aparecer ao mesmo tempo – por exemplo, usando um controlador de exibição pop-over.

Há muitas vantagens no uso de storyboards para criar a interface do usuário do seu aplicativo, especialmente ao usar o Xamarin. Em primeiro lugar, é uma representação visual da interface do usuário, como todos os objetos – inclusive [controles personalizados](~/ios/user-interface/designer/ios-designable-controls-overview.md) – são renderizados em tempo de design. Isso significa que, antes de criar ou implantar seu aplicativo, você pode visualizar sua aparência e o fluxo. Veja a imagem acima, por exemplo. Podemos dizer de uma rápida olhada o design surface quantas cenas lá estão, o layout de cada exibição e como tudo está relacionada. Isso é o que torna os Storyboards tão eficaz.

Os eventos são mais gerenciáveis com Storyboards, especialmente ao usar o Designer do iOS. A maioria dos controles de interface do usuário terá uma lista de possíveis eventos no painel de propriedades. O manipulador de eventos pode ser adicionado aqui e concluído em um método parcial na classe de controladores de exibição...

O conteúdo de um storyboard é armazenado como um arquivo XML. No momento, da compilação qualquer `.storyboard` arquivos são compilados em arquivos binários, conhecidos como nibs. Em tempo de execução, esses nibs são inicializados e instanciados para criar novos modos de exibição.

## <a name="segues"></a>Segues

Um *Segue*, ou *Segue o objeto*, é usado no desenvolvimento do iOS para representar uma transição entre as cenas. Para criar um segue, mantenha pressionada a **Ctrl** chave e clique e arraste de uma cena para outro. Como podemos arrastar nosso mouse, um conector azul é exibida, indicando onde o segue levará conforme demonstrado na imagem abaixo:

 [![](images/createsegue.png "Um conector azul é exibida, indicando onde o segue levará conforme demonstrado nesta imagem")](images/createsegue.png#lightbox)

Sobre o mouse para cima, será exibido um menu permitindo que nós escolha a ação para nosso segue. Ela deve ser semelhante para as imagens abaixo: 

**Classes de pré-iOS 8 e do tamanho**:

[![](images/segue1.png "A lista suspensa Segue de ação sem Classes de tamanho")](images/segue1.png#lightbox)

**Ao usar as Classes de tamanho e adaptável Segues**:

[![](images/16new.png "A lista suspensa Segue de ação com Classes de tamanho")](images/16new.png#lightbox)

> [!IMPORTANT]
> Se você estiver usando o VMWare para sua máquina Virtual do Windows, pressione a tecla CTRL é mapeado como o _com o botão direito_ botão do mouse por padrão. Para criar um Segue, edite suas preferências de teclado por meio **preferências** > **de teclado e Mouse** > **atalhos do Mouse** e remapear sua **Botão secundário** conforme ilustrado abaixo:
> 
> [![](images/image22.png "Configurações de preferência de teclado e Mouse")](images/image22.png#lightbox)
> 
> Agora, você poderá adicionar um segue entre seus controladores de exibição como de costume.

Há diferentes tipos de transições, cada controle dando sobre como um novo controlador de exibição é apresentado ao usuário e como ele interage com outros controladores de exibição no Storyboard. Eles são explicados abaixo. Também é possível criar subclasse de um objeto de segue para implementar uma transição personalizada:

-  **Mostrar / enviar por Push** – segue um envio por push adiciona o controlador de exibição para a pilha de navegação. Ele pressupõe que o controlador de exibição, o envio por push de origem faz parte do mesmo controlador de navegação que o controlador de exibição que está sendo adicionado à pilha. Isso faz a mesma coisa que `pushViewController` e geralmente é usado quando há alguma relação entre os dados nas telas. Usando o envio por push segue lhe dar ao luxo de ter uma barra de navegação com um botão Voltar e título adicionado para cada modo de exibição na pilha, permitindo a navegação pela hierarquia de exibição de busca detalhada.
-  **Modal** – um segue modal cria uma relação entre qualquer controlador de exibição de dois em seu projeto, com a opção de uma transição animada que está sendo mostrada. O controlador de exibição filho será obscurecer completamente o controlador de exibição de pai quando colocado na exibição. Ao contrário de um envio por push segue, que adiciona um botão Voltar para nós; Quando usar uma modal segue `DismissViewController` deve ser usado para retornar para o controlador de exibição anterior.
-  **Personalizado** – qualquer personalizado segue pode ser criado como uma subclasse de ` UIStoryboardSegue`.
-  **Desenrolar** – um desenrolamento segue pode ser usado para navegar de volta por meio de um envio por push ou modal segue – por exemplo, ignorando o controlador de exibição de forma restrita apresentada. Além disso, você poderá desenrolar por meio de não apenas um, mas uma série de envio por push e modal segues e voltar várias etapas na sua hierarquia de navegação com uma única ação de desenrolamento. Para entender como usar um desenrolamento segue no iOS, leia as [criação de desenrolamento Segues](https://github.com/xamarin/recipes/tree/master/Recipes/ios/general/storyboard/unwind_segue) receita.
-  **Sourceless** – um segue sourceless indica a cena que contém o controlador de exibição inicial e, portanto, qual exibir o usuário verá primeiro. Ele é representado pelo segue mostrado abaixo:  

    [![](images/sourcelesssegue.png "Um segue sourceless")](images/sourcelesssegue.png#lightbox)

### <a name="adaptive-segue-types"></a>Tipos de Segue adaptável

 iOS 8 introduzidas [Classes de tamanho](~/ios/user-interface/storyboards/unified-storyboards.md#size-classes) para permitir que um arquivo de storyboard do iOS trabalhar com todos os tamanhos de tela disponíveis, permitindo que os desenvolvedores criar uma interface do usuário para todos os dispositivos iOS. Por padrão, todos os novos aplicativos do xamarin. IOS usará as classes de tamanho. Para usar as classes de tamanho de um projeto mais antigo, consulte o [Introdução ao Storyboards unificados](~/ios/user-interface/storyboards/unified-storyboards.md) guia. 
 
Qualquer aplicativo usando as Classes de tamanho também usará o novo [ *Segues adaptável*](~/ios/user-interface/storyboards/unified-storyboards.md). Ao usar as classes de tamanho, lembre-se de que nós não são diretamente especificando se estamos usando um iPhone ou iPad. Em outras palavras que estamos criando uma interface do usuário que terá sempre a mesma aparência, independentemente de quanto espaço nele precisar funcionar com. Trabalho de transições adaptável julgar o ambiente e determinar a melhor maneira apresentar o conteúdo. A Adaptive Segues são mostrados abaixo: 

[![](images/adaptivesegue.png "A lista suspensa Segues adaptável")](images/adaptivesegue.png#lightbox)

|Segue|Descrição|
|--- |--- |
|programa|Isso é muito semelhante ao segue um envio por Push, mas ele considera o conteúdo da tela.|
|Mostrar detalhes|Se o aplicativo exibe um modo de exibição mestre e de detalhes (por exemplo, em um controlador de exibição de divisão em um iPad), o conteúdo substitui a exibição de detalhes. Se o aplicativo exibe apenas o mestre ou detalhes, o conteúdo substituirá o topo da pilha do controlador de exibição.|
|Apresentação|Isso é semelhante ao segue Modal e permite a seleção de estilos de apresentação e fazer a transição.|
|Apresentação de pop-over|Isso apresenta o conteúdo como um pop-over|

### <a name="transferring-data-with-segues"></a>Transferência de dados com Segues

Os benefícios de um segue não terminam com transições. Eles também podem ser usados para gerenciar a transferência de dados entre controladores de exibição. Isso é feito substituindo o `PrepareForSegue` método no controlador de exibição inicial e manipulando os dados nós mesmos. Quando o segue é acionado – por exemplo, com um pressionamento de botão – o aplicativo chama esse método, fornecendo uma oportunidade para preparar o novo controlador de exibição *antes de* qualquer navegação ocorre. O código a seguir, do [Phoneword](https://developer.xamarin.com/samples/monotouch/Hello_iOS/) de exemplo, demonstra isso: 


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

Neste exemplo, o `PrepareForSegue` método será chamado quando o segue é disparado pelo usuário. Primeiro precisamos criar uma instância do controlador de exibição 'receber' e defina isso como o destino do segue o controlador de exibição. Isso é feito pela linha de código a seguir:

```csharp
var callHistoryController = segue.DestinationViewController as CallHistoryController;
```

O método agora tem a capacidade de definir propriedades no `DestinationViewController`. Neste exemplo é que nós tiramos proveito disso, passando uma lista chamada `PhoneNumbers` para o `CallHistoryController` e atribuí-la a um objeto do mesmo nome:

```csharp
if (callHistoryController != null) {
        callHistoryController.PhoneNumbers = PhoneNumbers;
    }
```

Depois que a transição for concluída, o usuário verá o `CallHistoryController` com a lista preenchida.

## <a name="adding-a-storyboard-to-a-non-storyboard-project"></a>Adicionando um Storyboard a um projeto não são do Storyboard

Às vezes você precisa adicionar um Storyboard em um arquivo anteriormente não são do storyboard. Uma vez fazendo isso no Visual Studio para Mac pode ser simplificada, seguindo as etapas abaixo:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

1. Crie um novo arquivo de Storyboard, navegando até **arquivo > novo arquivo > iOS > Storyboard**, conforme ilustrado abaixo: 
    
    [![](images/new-storyboard-xs.png "A caixa de diálogo Novo arquivo")](images/new-storyboard-xs.png#lightbox)

2. Adicionar seu nome do Storyboard para o **Interface principal** seção o **Info. plist**, conforme mostrado abaixo:
    
    [![](images/infoplist.png "O editor de info. plist")](images/infoplist.png#lightbox)
    
    Isso faz o equivalente a instanciar o controlador de exibição inicial no `FinishedLaunching` método dentro do representante do aplicativo. Com essa opção definida, o aplicativo cria uma instância de uma janela (veja abaixo), carrega o storyboard principal e atribui uma instância do controlador de exibição inicial do storyboard (um ao lado o Segue sourceless) como o `RootViewController` propriedade da janela e, em seguida, faz a janela visível na tela.

3. No `AppDelegate`, substituir o padrão `Window` método, com o código a seguir para implementar a propriedade de janela:
        
        public override UIWindow Window {
            get;
            set;
            }
            
# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Crie um novo arquivo de Storyboard clicando com o projeto para **Adicionar > novo arquivo > iOS > Storyboard vazio**, conforme ilustrado abaixo: 
    
    [![](images/new-storyboard-vs.png "A caixa de diálogo novo item")](images/new-storyboard-vs.png#lightbox)

2. Adicionar seu nome do Storyboard para o **Interface principal** seção do iOS de aplicativo, conforme mostrado abaixo:
    
    [![](images/ios-app.png "O editor de info. plist")](images/ios-app.png#lightbox)
    
    Isso faz o equivalente a instanciar o controlador de exibição inicial no `FinishedLaunching` método dentro do representante do aplicativo. Com essa opção definida, o aplicativo cria uma instância de uma janela (veja abaixo), carrega o storyboard principal e atribui uma instância do controlador de exibição inicial do storyboard (um ao lado o Segue sourceless) como o `RootViewController` propriedade da janela e, em seguida, faz a janela visível na tela.

3. No `AppDelegate`, substituir o padrão `Window` método, com o código a seguir para implementar a propriedade de janela:

        public override UIWindow Window {
            get;
            set;
            }
            
-----

## <a name="creating-a-storyboard-with-the-ios-designer"></a>Criando um Storyboard com o Designer do iOS

Um Storyboard pode ser criado usando o Designer do Xamarin para iOS, que foi integrado perfeitamente com o Visual Studio para Mac e Visual Studio.

Para começar a usar o Designer do iOS para criar storyboards, siga as [Multitela Hello, iOS](~/ios/get-started/hello-ios-multiscreen/index.md) guia. Neste passo a passo, você irá explorar navegação entre controladores de exibição usando transições e como manipular eventos em seus controles.

## <a name="instantiate-storyboards-manually"></a>Criar uma instância de Storyboards manualmente

Storyboards substituem totalmente os arquivos XIB individuais em seu projeto, no entanto, os controladores de exibição individual em um Storyboard ainda podem ser instanciados usando `Storyboard.InstantiateViewController`.

Às vezes, os aplicativos têm requisitos especiais que não podem ser tratados com as transições de storyboard internas fornecidas pelo Designer. Por exemplo, se fôssemos criar um aplicativo que inicia a telas diferentes do mesmo botão, dependendo do estado atual de um aplicativo, podemos querer instanciar os controladores de exibição manualmente e programar a transição sozinhos.

Captura de tela abaixo mostra dois controladores de exibição na nossa superfície de design não segue entre eles. A próxima seção explica como essa transição pode ser configurada no código.

 [![](images/viewcontrollerspink.png "Esta captura de tela mostra dois controladores de exibição na superfície de design não segue entre eles")](images/viewcontrollerspink.png#lightbox)

1. Adicionar um _esvaziar Storyboard do iPhone_ a um projeto existente do projeto:
    
    [![](images/add-storyboard1.png "Adicionando o storyboard")](images/add-storyboard1.png#lightbox)

2. Clique duas vezes no storyboard criado recentemente para abri-lo e adicionar uma nova **controlador de navegação** à superfície de design. Como é o controlador de navegação sem interface do usuário, por padrão, ele virá com um controlador de exibição de raiz, conforme ilustrado abaixo:

    [![](images/uinavigationcontroller.png "Controladores de exibição com Segues")](images/uinavigationcontroller.png#lightbox)

3. Selecione o _controlador de exibição_ clicando na barra preta na parte inferior. No Designer de **propriedade Pad**, em **identidade** podemos especificar uma classe personalizada, bem como uma ID exclusiva para o controlador de exibição. Defina as **nome da classe** e **ID do Storyboard** para `MainViewController`.

    [![](images/identitypanelnew.png "Especifique a classe personalizada")](images/identitypanelnew.png#lightbox)

4. Posteriormente, precisamos criar uma instância de nossos controladores de exibição usando o storyboard e usará a ID do Storyboard para referenciá-los em nosso código. Definindo a ID de restauração para coincidir com a ID do Storyboard garante que o controlador de exibição obtém recriado corretamente se o estado precisa ser restaurado.

5. No momento, temos apenas um controlador de exibição. Arraste outro controlador de exibição para a superfície de design. No **propriedade Pad**, em identidade, defina a classe e a ID do Storyboard para `PinkViewController`, conforme ilustrado abaixo:

    [![](images/pinkvcnew.png "O painel de propriedade")](images/pinkvcnew.png#lightbox)
    
    O IDE criará essas classes personalizadas para os controladores de exibição. Eles podem ser exibidos na **painel de soluções**, conforme ilustrado na captura de tela abaixo:
    
    [![](images/solution-pad.png "Painel de soluções")](images/solution-pad.png#lightbox)

6. No `PinkViewController`, selecione o modo de exibição, clicando em direção ao centro do quadro do controlador. No painel de propriedades, em modo de exibição, alterar o **plano de fundo** para Magenta:
    
    [![](images/pinkcontroller.png "Definir a cor do plano de fundo")](images/pinkcontroller.png#lightbox)

7. Por fim, arraste um botão do **caixa de ferramentas** até o `MainViewController`. No painel de propriedades, atribua o nome `PinkButton` e GoToPink o título, conforme ilustrado abaixo:

    [![](images/pinkbutton.png "Defina o nome do botão")](images/pinkbutton.png#lightbox)

O storyboard for concluído, mas se podemos implantar o projeto agora, obteremos uma tela em branco. Isso ocorre porque precisamos mandar o IDE para usar nossos storyboard e configurar um controlador de exibição de raiz para servir como a primeira exibição. Normalmente isso pode ser feito por meio de nossas opções de projeto, conforme mostrado acima. No entanto neste exemplo estamos será obtido o mesmo resultado no código, adicionando o seguinte para o **AppDelegate**:

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
            window.MakeKeyAndVisible ();
            return true;
        }

    }
```

Isso é muito do código, mas não estiver familiarizadas com apenas algumas linhas. Em primeiro lugar, registramos nossa storyboard com o **AppDelegate** , passando o nome do storyboard, **MainStoryboard**. Em seguida, dizemos que o aplicativo para criar uma instância de um controlador de exibição inicial do storyboard chamando `InstantiateInitialViewController` em nossa storyboard e definimos esse controlador de exibição como controlador de exibição de raiz do nosso aplicativo. Este método determina a primeira tela que o usuário vê, e cria uma nova instância desse controlador de exibição.

Observe no painel de solução que o IDE tiver criado uma `MainViewcontroller.cs` classe e seu `corresponding designer.cs` quando adicionamos o nome de classe para o painel de propriedades na etapa 4. Podemos ver essa classe criada um construtor especial que inclui uma classe base:

```csharp
public MainViewController (IntPtr handle) : base (handle) 
{
}
```


Ao criar um Storyboard usando o Designer, o IDE adicionará automaticamente o [[registrar]](xref:Foundation.RegisterAttribute) atributo na parte superior do `designer.cs` de classe e passar um identificador de cadeia de caracteres, que é idêntico à ID do Storyboard especificado no etapa anterior. Isso vinculará o c# para a cena relevante no Storyboard.

Em algum momento, você talvez queira adicionar uma classe existente que foi **não** criados no designer. Nesse caso, se registrar essa classe como de costume:

```csharp
[Register ("MainViewController")]
public partial class MainViewController : UIViewController
{
public MainViewController (IntPtr handle) : base (handle) 
{
}

...
}
```

Para obter mais informações sobre como registrar classes e métodos, consulte o [registrador tipo](http://docs.xamarin.com/guides/ios/advanced_topics/registrar/) documentação.

A última etapa nessa classe é para conectar o botão e a transição para o controlador de exibição rosa. É uma instância de `PinkViewController` usando o Storyboard; em seguida, podemos irá programar um envio por push segue com `PushViewController`, conforme ilustrado pelo código de exemplo abaixo:

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

    public void Initialize(){

    //Instantiating View Controller with Storyboard ID 'PinkViewController'
    pinkViewController = Storyboard.InstantiateViewController ("PinkViewController") as PinkViewController;
    }

    public override void ViewDidLoad ()
    {
    base.ViewDidLoad ();

    //When we push the button, we will push the pinkViewController onto our current Navigation Stack
    PinkButton.TouchUpInside += (o, e) =&gt; {
        this.NavigationController.PushViewController (pinkViewController, true);
    };
    }

}
```

Executar o aplicativo produz um aplicativo de tela de 2:

![](images/finishedstoryboard.png "Telas de execução do aplicativo de exemplo")

## <a name="conditional-segues"></a>Segues condicional

Geralmente, a movimentação de um controlador de exibição para a próxima é dependente de uma determinada condição. Por exemplo, se estivermos fazendo uma tela de logon simples só queremos passar para a próxima tela *se* tinham sido verificados o nome de usuário e senha.

O exemplo a seguir, adicionaremos um campo de senha para o exemplo acima. O usuário só poderá acessar o *PinkViewController* se ele inserir a senha correta, caso contrário, um erro será exibido.

Antes de começar, siga etapas 1 a 8 acima. Nestas etapas podemos criar nossos storyboard, começar a criar nossa interface do usuário e informar qual controlador de exibição para usar ao nosso representante de aplicativo como ele é RootViewController.

1. Agora, vamos criar nossa interface do usuário e adicionar as exibições adicionais listadas para o `MainViewController` para que ela fique assim na captura de tela abaixo:

    - UITextField
        - Nome: PasswordTextField
        - Espaço reservado: 'Inserir a senha de segredo'
    - UILabel
        - Texto: ' Erro: Senha incorreta. Você não deve passar!'
        - Cor: Vermelho
        - Alinhamento: Centralizado 
        - Linhas: 2
        - Caixa de seleção 'Hidden' verificada 
        
    [![](images/passwordvc.png "Linhas de Center")](images/passwordvc.png#lightbox)
    
2. Criar um Segue entre o botão Ir para rosa e o controlador de exibição arrastando Ctrl-do *PinkButton* para o *PinkViewController*e selecionando **enviar por Push** no mouse para cima . 

3. Clique no Segue e dê a ele o *identificador* `SegueToPink`:

    [![](images/namesegue.png "Clique no Segue e dê a ele o identificador SegueToPink")](images/namesegue.png#lightbox)  
    

4. Por fim, adicione o seguinte método ShouldPerformSegue para o `MainViewController` classe:

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

Nesse código, podemos ter correspondido ao segueIdentifier para nosso `SegueToPink` segue, para que possamos testar uma condição; em seguida, uma senha válida neste caso. Se a condição de nosso retorna `true`, o Segue executará e apresentará o `PinkViewController`. Se `false`, o novo controlador de exibição não será apresentado.

Podemos aplicar essa abordagem para qualquer Segue esse controlador de exibição, verificando o argumento segueIdentifier para o método ShouldPerformSegue. Nesse caso, temos apenas um identificador de Segue – `SegueToPink`.

Consulte a solução Storyboards.Conditional na [amostra Manual Storyboards](https://developer.xamarin.com/samples/monotouch/ManualStoryboard/) para obter um exemplo de trabalho.

<a name="Using-Storyboard-References" />

## <a name="using-storyboard-references"></a>Usando referências de Storyboard

Uma referência de Storyboard permite que você usar um design de Storyboard grande e complexo e dividi-la em menores Storyboards que obterem referenciados a partir do original, portanto, remover a complexidade e tornando os Storyboards individuais resultantes mais fácil de desenvolver e manter.

Além disso, uma referência de Storyboard pode fornecer um _âncora_ à cena outro dentro do mesmo Storyboard ou uma cena específica em um diferente.

<a name="Referencing-an-External-Storyboard" />

### <a name="referencing-an-external-storyboard"></a>Fazendo referência a um Storyboard externo

Para adicionar uma referência a um Storyboard externo, faça o seguinte:

1. No **Gerenciador de soluções**, clique com botão direito no nome do projeto e selecione **Add** > **novo arquivo...**   >  **iOS** > **Storyboard**. Insira um **nome** para o novo Storyboard e clique o **New** botão:
    
    [![](images/ref01.png "A caixa de diálogo Novo arquivo")](images/ref01.png#lightbox)
    
2. Como você normalmente faria e salva suas alterações de design o layout dos bastidores do novo Storyboard: 
    
    [![](images/ref02.png "O layout da nova cena")](images/ref02.png#lightbox)
    
3. Abra o Storyboard que você pretende adicionar a referência a no Designer do iOS.

4. Arraste uma **referência do Storyboard** da **caixa de ferramentas** na superfície de Design: 
    
    [![](images/ref03.png "Uma referência de Storyboard")](images/ref03.png#lightbox)
    
5. No **Widget** guia da **Gerenciador de propriedades**, selecione o nome da **Storyboard** criado acima: 

    [![](images/ref04.png "Na guia de Widget")](images/ref04.png#lightbox)
    
6. CTRL + clique em um Widget de interface do usuário (como um botão) em uma cena existente e criar um novo Segue para a **referência de Storyboard** que você acabou de criar: 

    [![](images/ref05.png "Criando um segue")](images/ref05.png#lightbox) 
    
7. No menu pop-up, selecione **Mostrar** para concluir o Segue: 

    [![](images/ref06.png "Selecionando Mostrar para concluir o Segue")](images/ref06.png#lightbox) 
    
8. Salve suas alterações para o Storyboard.

Quando o aplicativo é executado e o usuário clica no elemento de interface do usuário que você criou o Segue do, o controlador de exibição inicial do Storyboard externo especificado na referência do Storyboard serão exibida.

<a name="Referencing-a-Specific-Scene-in-an-External-Storyboard" />

### <a name="referencing-a-specific-scene-in-an-external-storyboard"></a>Fazendo referência a uma cena específica em um Storyboard externo

Para adicionar uma referência a uma cena específica um Storyboard externo (e não o controlador de exibição inicial), faça o seguinte:

1. No **Gerenciador de soluções**, clique duas vezes o Storyboard externo para abri-lo para edição.

2. Adicione uma nova cena e projetar seu layout, como faria normalmente: 

    [![](images/ref07.png "O novo layout de cena")](images/ref07.png#lightbox)
    
3. No **Widget** guia da **Gerenciador de propriedades**, insira um **ID do Storyboard** para controlador de exibição da cena novo: 

    [![](images/ref08.png "Insira uma ID do Storyboard para o novo controlador de exibição de cenas")](images/ref08.png#lightbox)
    
3. Abra o Storyboard que você pretende adicionar a referência a no Designer do iOS.

4. Arraste uma **referência do Storyboard** da **caixa de ferramentas** na superfície de Design: 

    [![](images/ref03.png "Uma referência de Storyboard")](images/ref03.png#lightbox)
    
5. No **Widget** guia da **Gerenciador de propriedades**, selecione o nome da **Storyboard** e o **ID de referência** (ID do Storyboard) do Cena que você criou acima: 

    [![](images/ref09.png "Na guia de Widget ")](images/ref09.png#lightbox)
    
6. CTRL + clique em um Widget de interface do usuário (como um botão) em uma cena existente e criar um novo Segue para a **referência de Storyboard** que você acabou de criar: 

    [![](images/ref10.png "Criando um segue")](images/ref10.png#lightbox) 
    
7. No menu pop-up, selecione **Mostrar** para concluir o Segue: 

    [![](images/ref06.png "Selecionando Mostrar para concluir o Segue")](images/ref06.png#lightbox) 
    
8. Salve suas alterações para o Storyboard.

Quando o aplicativo é executado e o usuário clica no elemento de interface do usuário que você criou o Segue da cena com o determinado **ID do Storyboard** do Storyboard externo especificado na referência do Storyboard será exibida.

<a name="Referencing-a-Specific-Scene-in-the-Same-Storyboard" />

### <a name="referencing-a-specific-scene-in-the-same-storyboard"></a>Fazendo referência a uma cena específica no mesmo Storyboard

Para adicionar uma referência a uma cena específica o Storyboard mesmo, faça o seguinte:

1. No **Gerenciador de soluções**, clique duas vezes o Storyboard para abri-lo para edição.

2. Adicione uma nova cena e projetar seu layout, como faria normalmente: 

    [![](images/ref11.png "O novo layout de cena")](images/ref11.png#lightbox)

3. No **Widget** guia da **Gerenciador de propriedades**, insira um **ID do Storyboard** para controlador de exibição da cena novo: 

    [![](images/ref12.png "Na guia de Widget")](images/ref12.png#lightbox)
    
3. Arraste uma **referência do Storyboard** da **caixa de ferramentas** na superfície de Design: 

    [![](images/ref03.png "Uma referência de Storyboard")](images/ref03.png#lightbox)
    
5. No **Widget** guia o **Gerenciador de propriedades**, selecione **ID de referência** (ID do Storyboard) da cena que você criou acima: 

    [![](images/ref13.png "Na guia de Widget")](images/ref13.png#lightbox)
    
6. CTRL + clique em um Widget de interface do usuário (como um botão) em uma cena existente e criar um novo Segue para a **referência de Storyboard** que você acabou de criar: 

    [![](images/ref14.png "Criando um segue")](images/ref14.png#lightbox) 
    
7. No menu pop-up, selecione **Mostrar** para concluir o Segue: 

    [![](images/ref06.png "Selecionando Mostrar para concluir o Segue")](images/ref06.png#lightbox) 
    
8. Salve suas alterações para o Storyboard.

Quando o aplicativo é executado e o usuário clica no elemento de interface do usuário que você criou o Segue da cena com o determinado **ID do Storyboard** no mesmo Storyboard especificado na referência do Storyboard será exibida.

## <a name="summary"></a>Resumo

Este artigo apresenta o conceito de como eles podem ser úteis no desenvolvimento de aplicativos do iOS e Storyboards. Ele aborda cenas, controladores de exibição, modos de exibição e hierarquias de exibição e como os bastidores são vinculados junto com diferentes tipos de transições.  Ele também explora os controladores de exibição ao instanciar manualmente de um storyboard e criar transições condicional.



## <a name="related-links"></a>Links relacionados

- [Storyboard manual (amostra)](https://developer.xamarin.com/samples/ManualStoryboard/)
- [Introdução ao Designer do iOS](~/ios/user-interface/designer/introduction.md)
- [Convertendo em Storyboards](https://developer.apple.com/library/ios/#releasenotes/Miscellaneous/RN-AdoptingStoryboards/)
- [Referência de classe UIStoryboard](https://developer.apple.com/library/ios/#documentation/UIKit/Reference/UIStoryboard_Class/Reference/Reference.html)
