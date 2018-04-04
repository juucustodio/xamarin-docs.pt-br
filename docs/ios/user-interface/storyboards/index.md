---
title: Introdução ao Storyboards
description: Um Storyboard é uma representação visual da aparência e o fluxo do aplicativo. Xamarin introduziu um Designer para permitir que aplicativos xamarin tirar proveito dos storyboards, você pode projetar a tela do aplicativo visualmente e acessar modos de exibição, controladores e segues com c# para obter mais controle.
ms.prod: xamarin
ms.assetid: A3339BD2-9F56-7965-25F5-4B7C991EB775
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/22/2017
ms.openlocfilehash: 647bd7d339dc56978752f7ab29de30cf8acb7e07
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="introduction-to-storyboards"></a>Introdução ao Storyboards

_Um Storyboard é uma representação visual da aparência e o fluxo do aplicativo. Xamarin introduziu um Designer para permitir que aplicativos xamarin tirar proveito dos storyboards, você pode projetar a tela do aplicativo visualmente e acessar modos de exibição, controladores e segues com c# para obter mais controle._

Neste guia, explicaremos um Storyboard que é e examinar alguns dos principais componentes – como Segues. Vamos examinar como Storyboards podem ser criados e usados, e quais são as vantagens que eles têm para um desenvolvedor.

Antes do formato de arquivo de Storyboard foi introduzido pela Apple como uma representação visual da interface do usuário de um aplicativo do iOS, os desenvolvedores criou arquivos XIB para cada controlador de exibição e programada a navegação entre cada exibição manualmente.  Usando um Storyboard permite que o desenvolvedor definir controladores de exibição e navegação entre eles em uma superfície de design e oferece edição WYSIWYG da interface de usuário do aplicativo.

Um Storyboard pode ser criado, aberto e editado com o Designer do Xamarin iOS. Este guia também serão passo a passo como usar o Designer para criar seu storyboards usando c# para programar o painel de navegação.


## <a name="requirements"></a>Requisitos

Storyboards podem ser usados com o iOS Designer no Visual Studio para Mac ou com o Visual Studio 2015 e 2017 com as cargas de trabalho do Xamarin instaladas.

## <a name="what-is-a-storyboard"></a>O que é um Storyboard?

Um Storyboard é a representação visual de todas as telas em um aplicativo. Ele contém uma sequência de segundo plano, com cada cena que representa um *View Controller* e sua *exibições*. Esses modos de exibição podem conter objetos e [controles](~/ios/user-interface/controls/index.md) que permite que o usuário interagir com seu aplicativo. Esta coleção de controles e modos de exibição (ou *sub-visualizações*) é conhecido como um *exibir hierarquia de conteúdo*. Segundo plano é conectados por atender objetos que representam uma transição entre os controladores de exibição. Normalmente, isso é obtido criando um segue entre um objeto no modo de exibição inicial e o modo de exibição de conexão. As relações na superfície de design são ilustradas na imagem abaixo:

 [![](images/storyboardsview.png "As relações na superfície de design são ilustradas nesta imagem")](images/storyboardsview.png#lightbox)

Conforme mostrado, o storyboard irá dispor cada uma de suas cenas com conteúdo que já foi processado e ilustra as conexões entre eles.  Vale a pena observar neste ponto, que, quando falamos em segundo plano em um iPhone, é seguro supor que um *cena* no storyboard é igual a um *tela* de conteúdo no dispositivo. No entanto, com um iPad, é possível ter várias cenas aparecer uma vez – por exemplo, usando um controlador de exibição Popover.

Há muitas vantagens em usar storyboards para criar a interface do usuário do seu aplicativo, especialmente ao usar Xamarin. Em primeiro lugar, é uma representação visual da interface do usuário, como todos os objetos – incluindo [controles personalizados](~/ios/user-interface/designer/ios-designable-controls-overview.md) – são renderizadas em tempo de design. Isso significa que, antes de criar ou implantar seu aplicativo, você pode visualizar sua aparência e o fluxo. Veja a imagem acima, por exemplo. Podemos dizer de uma visão geral o design de superfície cenas quantos existe são, o layout de cada exibição e como tudo está relacionado. Este é o que torna os Storyboards tão eficaz.

Os eventos são mais fáceis de gerenciar com Storyboards, especialmente ao usar o Designer do iOS. A maioria dos controles de interface do usuário terá uma lista de eventos possíveis no painel de propriedades. O manipulador de eventos pode ser adicionado aqui e concluído em um método parcial na classe controladores de exibição...

O conteúdo de um storyboard é armazenado como um arquivo XML. Ao criar o tempo, qualquer `.storyboard` arquivos são compilados em arquivos binários, conhecidos como nibs. Em tempo de execução, essas nibs são inicializados e instanciados para criar novos modos de exibição.

## <a name="segues"></a>Segues

Um *Segue*, ou *objeto atender*, é usado no desenvolvimento do iOS para representar uma transição entre nos bastidores. Para criar um segue, mantenha pressionada a **Ctrl** chave e clique e arraste de uma cena para outro. Como arrastamos nosso mouse, um conector azul é exibida, indicando que o segue levará conforme demonstrado na imagem abaixo:

 [![](images/createsegue.png "Um conector azul é exibida, indicando que o segue levará conforme demonstrado nesta imagem")](images/createsegue.png#lightbox)

No mouse, um menu será exibido permitindo que nós escolha a ação para nosso segue. Ela pode parecer com as imagens a seguir: 

**Classes de pré-iOS 8 e tamanho**:

[![](images/segue1.png "A lista suspensa ação atender sem tamanho Classes")](images/segue1.png#lightbox)

**Ao usar as Classes de tamanho e adaptável Segues**:

[![](images/16new.png "A lista suspensa ação atender com Classes de tamanho")](images/16new.png#lightbox)

> [!IMPORTANT]
> Se você estiver usando o VMWare para a máquina Virtual do Windows, clique em Ctrl for mapeada como o _clique_ botão do mouse por padrão. Para criar um Segue, edite suas preferências de teclado por meio de **preferências** > **de teclado e Mouse** > **atalhos do Mouse** e remapear o **Botão secundário** conforme ilustrado abaixo:
> 
> [![](images/image22.png "Configurações de preferência de teclado e Mouse")](images/image22.png#lightbox)
> 
> Agora, você poderá adicionar um segue entre os controladores de modo de exibição normal.

Há tipos diferentes de transições, cada oferecendo controle sobre como um novo controlador de exibição é apresentado ao usuário e como ele interage com outros controladores de exibição no Storyboard. Eles são explicados abaixo. Também é possível criar subclasse de um objeto segue para implementar uma transição personalizada:

-  **Mostrar / enviar por Push** – um envio por push atender adiciona o controlador de exibição para a pilha de navegação. Ele pressupõe que o controlador de exibição, o envio por push de origem faz parte do mesmo controlador de navegação que o controlador de exibição que está sendo adicionado à pilha. Isso faz a mesma coisa que `pushViewController` e geralmente é usado quando há alguma relação entre os dados nas telas. Usando o push atender lhe dar ao luxo de ter uma barra de navegação com um botão Voltar e título adicionado a cada exibição na pilha, permitindo a navegação pela hierarquia de exibição de busca detalhada.
-  **Modal** – um segue modal cria uma relação entre qualquer dois exibir controladores no seu projeto, com a opção de uma transição animada sendo mostrada. O controlador de exibição filho será obscurecer completamente o controlador de exibição pai quando colocado em modo de exibição. Ao contrário de um envio por push atender, que adiciona um botão Voltar para que possamos; Quando usar um modal atender `DismissViewController` deve ser usado para retornar para o controlador de exibição anterior.
-  **Personalizado** – qualquer personalizado segue pode ser criado como uma subclasse de ` UIStoryboardSegue`.
-  **Desenrolar** – um desenrolamento atender pode ser usado para navegar para trás no modal ou push atender – por exemplo, para ignorar o controlador de exibição modalmente apresentados. Além disso, você pode desenrolar não apenas um, mas uma série de push e modal segues e voltar desenrolamento de várias etapas em sua hierarquia de navegação com uma única ação. Para entender como usar um desenrolamento atender no iOS, leia o [criando desenrolar Segues](https://developer.xamarin.com/recipes/ios/general/storyboard/unwind_segue/) receita.
-  **Sourceless** – um segue sourceless indica a cena que contém o controlador de visualização inicial e, portanto, que exibir o usuário verá primeiro. Ela é representada pelo segue mostrado abaixo:  

    [![](images/sourcelesssegue.png "Segue um sourceless")](images/sourcelesssegue.png#lightbox)

### <a name="adaptive-segue-types"></a>Tipos de atender adaptável

 iOS 8 introduzida [tamanho Classes](~/ios/user-interface/storyboards/unified-storyboards.md#size-classes) para permitir que um arquivo de storyboard do iOS trabalhar com todos os tamanhos de tela disponíveis, permitindo que os desenvolvedores criem uma interface do usuário para todos os dispositivos iOS. Por padrão, todos os novos aplicativos xamarin usará as classes de tamanho. Para usar as classes de tamanho de um projeto mais antigo, consulte o [Introdução ao Unified Storyboards](~/ios/user-interface/storyboards/unified-storyboards.md) guia. 
 
Qualquer aplicativo usando as Classes de tamanho também usam o novo [ *Segues adaptável*](~/ios/user-interface/storyboards/unified-storyboards.md). Ao usar as classes de tamanho, lembre-se de que nós não são diretamente especificando se estamos usando um iPhone ou iPad. Em outras palavras, estamos criando uma interface do usuário será sempre têm a mesma aparência, independentemente de quanto espaço é necessário trabalhar com. Trabalho Segues adaptável a julgar o ambiente e determinar a melhor maneira apresentar o conteúdo. O Segues adaptável são mostrados abaixo: 

[![](images/adaptivesegue.png "A lista suspensa Segues adaptável")](images/adaptivesegue.png#lightbox)

|Atender|Descrição|
|--- |--- |
|programa|Isso é muito semelhante para atender um envio por Push, mas ele considera o conteúdo da tela.|
|Mostrar detalhes|Se o aplicativo exibe um modo de exibição mestre e de detalhes (por exemplo, em um controlador de exibição de divisão em um iPad), o conteúdo substituirá o modo de exibição de detalhes. Se o aplicativo exibe apenas o mestre ou detalhes, o conteúdo substituirá o topo da pilha de controlador de exibição.|
|Apresentação|Isso é semelhante ao que segue Modal e permite a seleção de estilos de apresentação e façam a transição.|
|Apresentação de popover|Isso apresenta conteúdo como um popover|

### <a name="transferring-data-with-segues"></a>Transferência de dados com Segues

Os benefícios de um segue não terminam com transições. Eles também podem ser usados para gerenciar a transferência de dados entre os controladores de exibição. Isso é conseguido por meio da substituição de `PrepareForSegue` método no controlador de exibição inicial e manipulando os dados nos. Quando o segue é disparado – por exemplo, com um pressionamento do botão – o aplicativo chama esse método, fornecendo uma oportunidade para preparar o novo controlador de exibição *antes de* qualquer navegação ocorre. O código a seguir, do [Phoneword](https://developer.xamarin.com/samples/monotouch/Hello_iOS/) de exemplo, demonstra isso: 


```csharp
public override void PrepareForSegue (UIStoryboardSegue segue, 
NSObject sender)
{
    base.PrepareForSegue (segue, sender);

    var callHistoryContoller = segue.DestinationViewController 
                                  as CallHistoryController;

    if (callHistoryContoller != null) {
        callHistoryContoller.PhoneNumbers = PhoneNumbers;
    }
}
```

Neste exemplo, o `PrepareForSegue` método será chamado quando o segue é disparada pelo usuário. Primeiro, é necessário criar uma instância do controlador 'recebimento' modo de exibição e defini-lo como destino do segue View Controller. Isso é feito pela linha de código a seguir:

```csharp
var callHistoryContoller = segue.DestinationViewController as CallHistoryController;
```

O método agora tem a capacidade de definir propriedades no `DestinationViewController`. Neste exemplo levamos aproveitar isso passando uma lista de chamada `PhoneNumbers` para o `CallHistoryController` e atribuí-la a um objeto do mesmo nome:

```csharp
if (callHistoryContoller != null) {
        callHistoryContoller.PhoneNumbers = PhoneNumbers;
    }
```

Quando a transição for concluída, o usuário verá o `CallHistoryController` com a lista preenchida.

## <a name="adding-a-storyboard-to-a-non-storyboard-project"></a>Adicionando um Storyboard a um projeto de Storyboard não

Ocasionalmente, você precisará adicionar um Storyboard em um arquivo anteriormente não sejam de storyboard. Fazer isso vez no Visual Studio para Mac pode ser otimizado, seguindo as etapas abaixo:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

1. Criar um novo arquivo de Storyboard, navegando para **arquivo > novo arquivo > iOS > Storyboard**, conforme ilustrado abaixo: 
    
    [![](images/new-storyboard-xs.png "A caixa de diálogo Novo arquivo")](images/new-storyboard-xs.png#lightbox)

2. Adicionar o nome do Storyboard para o **Interface principal** seção o **Info. plist**, conforme mostrado abaixo:
    
    [![](images/infoplist.png "O editor de info. plist")](images/infoplist.png#lightbox)
    
    Isto faz o equivalente a instanciar o controlador de exibição inicial no `FinishedLaunching` método dentro do representante do aplicativo. Com essa opção definida, o aplicativo cria uma janela (veja abaixo), carrega o storyboard principal e atribui uma instância do controlador de exibição inicial do storyboard (um ao lado de Segue sourceless) como o `RootViewController` propriedade da janela e, em seguida, faz a janela visível na tela.

3. No `AppDelegate`, substituir o padrão `Window` método, com o código a seguir para implementar a propriedade de janela:
        
        public override UIWindow Window {
            get;
            set;
            }
            
# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. Criar um novo arquivo de Storyboard clicando no projeto para **Adicionar > novo arquivo > iOS > Storyboard vazio**, conforme ilustrado abaixo: 
    
    [![](images/new-storyboard-vs.png "A caixa de diálogo novo item")](images/new-storyboard-vs.png#lightbox)

2. Adicionar o nome do Storyboard para o **Interface principal** seção do iOS aplicativo, conforme mostrado abaixo:
    
    [![](images/ios-app.png "O editor de info. plist")](images/ios-app.png#lightbox)
    
    Isto faz o equivalente a instanciar o controlador de exibição inicial no `FinishedLaunching` método dentro do representante do aplicativo. Com essa opção definida, o aplicativo cria uma janela (veja abaixo), carrega o storyboard principal e atribui uma instância do controlador de exibição inicial do storyboard (um ao lado de Segue sourceless) como o `RootViewController` propriedade da janela e, em seguida, faz a janela visível na tela.

3. No `AppDelegate`, substituir o padrão `Window` método, com o código a seguir para implementar a propriedade de janela:

        public override UIWindow Window {
            get;
            set;
            }
            
-----

## <a name="creating-a-storyboard-with-the-ios-designer"></a>Criando um Storyboard com o Designer do iOS

Um Storyboard pode ser criado usando o Designer de Xamarin para iOS, que foi integrado perfeitamente com o Visual Studio para Mac e Visual Studio.

Para começar a usar o Designer do iOS para criar storyboards, siga o [Hello, iOS Multiscreen](~/ios/get-started/hello-ios-multiscreen/index.md) guia. Este passo a passo, você explorará navegação entre os controladores de exibição usando Segues e como tratar eventos em seus controles.

## <a name="instantiate-storyboards-manually"></a>Instanciar Storyboards manualmente

Storyboards totalmente substituem arquivos XIB individuais em seu projeto, no entanto, os controladores de exibição individual em um Storyboard ainda podem ser instanciados usando `Storyboard.InstantiateViewController`.

Às vezes, os aplicativos têm requisitos especiais que não podem ser manipulados com as transições internas de storyboard fornecidas pelo Designer. Por exemplo, se foi criar um aplicativo que inicia a telas diferentes do mesmo botão, dependendo do estado atual de um aplicativo, pode queremos instanciar os controladores de exibição manualmente e um programa a transição nos.

Captura de tela abaixo mostra dois controladores de exibição na superfície de design do nosso não atender entre eles. A próxima seção guiará como essa transição pode ser configurada no código.

 [![](images/viewcontrollerspink.png "Esta captura de tela mostra dois controladores de exibição na superfície de design não atender entre eles")](images/viewcontrollerspink.png#lightbox)

1. Adicionar uma _vazio iPhone Storyboard_ a um projeto existente do projeto:
    
    [![](images/add-storyboard1.png "Adicionando o storyboard")](images/add-storyboard1.png#lightbox)

2. Clique duas vezes no storyboard criado recentemente para abri-lo e adicionar um novo **navegação controlador** à superfície de design. Como o controlador de navegação está sem interface do usuário, por padrão, ele será fornecido com um controlador de exibição de raiz, conforme ilustrado abaixo:

    [![](images/uinavigationcontroller.png "Exibir controladores com Segues")](images/uinavigationcontroller.png#lightbox)

3. Selecione o _View Controller_ clicando na barra preta na parte inferior. No Designer de **propriedade preenchimento**, em **identidade** podemos especificar uma classe personalizada, bem como uma ID exclusiva para o controlador de exibição. Definir o **nome da classe** e **Storyboard ID** para `MainViewController`.

    [![](images/identitypanelnew.png "Especifique a classe personalizada")](images/identitypanelnew.png#lightbox)

4. Posteriormente, precisaremos criar uma instância de nossos controladores de exibição de storyboard e usará a ID de Storyboard para referenciá-las em nosso código. Definir a ID de restauração para coincidir com a ID de Storyboard garante que o controlador de exibição obtém recriado corretamente se o estado precisa ser restaurado.

5. Atualmente só temos um controlador de exibição. Arraste o outro controlador de exibição para a superfície de design. No **propriedade preenchimento**, com a identidade, definir a classe e a ID de Storyboard para `PinkViewController`, conforme ilustrado abaixo:

    [![](images/pinkvcnew.png "O painel de propriedade")](images/pinkvcnew.png#lightbox)
    
    O IDE criará essas classes personalizadas para os controladores de exibição. Esses podem ser exibidos no **solução preenchimento**, conforme ilustrado na captura de tela abaixo:
    
    [![](images/solution-pad.png "Preenchimento de solução")](images/solution-pad.png#lightbox)

6. No `PinkViewController`, selecione o modo de exibição clicando em direção ao centro do quadro do controlador. No painel Propriedades, em modo de exibição, altere o **em segundo plano** para Magenta:
    
    [![](images/pinkcontroller.png "Definir a cor de plano de fundo")](images/pinkcontroller.png#lightbox)

7. Por fim, arraste um botão do **caixa de ferramentas** até o `MainViewController`. No painel de propriedades, dê a ele o nome `PinkButton` e GoToPink o título, conforme ilustrado abaixo:

    [![](images/pinkbutton.png "Nome do conjunto de botão")](images/pinkbutton.png#lightbox)

O storyboard é concluído, mas se podemos implantar o projeto agora, obteremos uma tela em branco. Isso ocorre porque precisamos informar o IDE para usar nosso storyboard e para configurar um controlador de exibição de raiz para servir como a primeira exibição. Normalmente isso pode ser feito por meio de nossas opções de projeto, como mostrado acima. No entanto neste exemplo, será obtida o mesmo resultado no código, adicionando o seguinte para o **AppDelegate**:

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

Isso é muito de código, mas não estiver familiarizadas com apenas algumas linhas. Primeiro, nós registramos nosso storyboard com o **AppDelegate** , passando o nome do storyboard, **MainStoryboard**. Em seguida, dizemos que o aplicativo para criar uma instância de um controlador de exibição inicial do storyboard chamando `InstantiateInitialViewController` em nosso storyboard, e vamos definir esse controlador de exibição como controlador de exibição de raiz do nosso aplicativo. Este método determina a primeira tela que o usuário vê, e cria uma nova instância desse controlador de exibição.

Observe no painel de solução que criou o IDE um `MainViewcontroller.cs` classe e sua `corresponding designer.cs` quando adicionamos o nome da classe para o painel de propriedades na etapa 4. Podemos ver essa classe criada um construtor especial, que inclui uma classe base:

```csharp
public MainViewController (IntPtr handle) : base (handle) 
{
}
```


Ao criar um Storyboard usando o Designer, o IDE adicionará automaticamente o [[registrar]](https://developer.xamarin.com/api/type/Foundation.RegisterAttribute/) atributo na parte superior do `designer.cs` classe e passar um identificador de cadeia de caracteres, que é idêntico à ID do Storyboard especificado no etapa anterior. Isso vinculará o c# para a cena relevante no Storyboard.

Em algum momento, talvez você queira adicionar uma classe existente que foi **não** criado no designer. Nesse caso, você deve registrar essa classe como normal:

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

Para obter mais informações sobre como registrar classes e métodos, consulte o [tipo registrador](http://docs.xamarin.com/guides/ios/advanced_topics/registrar/) documentação.

A última etapa dessa classe é para conectar o botão e a transição para o controlador de exibição rosa. Podemos vai criar uma instância de `PinkViewController` do Storyboard; em seguida, podemos programará um envio por push atender com `PushViewController`, conforme ilustrado pelo exemplo de código abaixo:

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

Executando o aplicativo gera um aplicativo de tela de 2:

![](images/finishedstoryboard.png "Aplicativo de exemplo executar telas")

## <a name="conditional-segues"></a>Segues condicional

Normalmente, movimentação de um controlador de exibição para o próximo é dependente de uma determinada condição. Por exemplo, se o estava fazendo uma tela de logon simples só queremos mover para a próxima tela *se* tiveram verificados o nome de usuário e senha.

O exemplo a seguir, adicionaremos um campo de senha para o exemplo acima. O usuário só poderá acessar o *PinkViewController* se ele inserir a senha correta, caso contrário, um erro será exibido.

Antes de começar, execute as etapas acima de 1 a 8. Nestas etapas, criar nossa storyboard, começar a criar nossa interface do usuário e informar delegado nosso aplicativo qual controlador de exibição a ser usado como está RootViewController.

1. Agora, vamos criar nossa interface do usuário e adicionar modos de exibição adicionais listados para o `MainViewController` para torná-lo como a captura de tela abaixo:

    - UITextField
        - Name: PasswordTextField
        - Espaço reservado: 'Inserir a senha secreta'
    - UILabel
        - Texto: ' erro: incorreto de senha. Você não deve transmitir!'
        - Cor: vermelha
        - Alinhamento: Center
        - Linhas: 2
        - Caixa de seleção 'Oculta' marcada 
        
    [![](images/passwordvc.png "Linhas de Center")](images/passwordvc.png#lightbox)
    
2. Criar um Segue entre o botão Ir para rosa e o controlador de exibição, arrastando Ctrl do *PinkButton* para o *PinkViewController*e selecionando **Push** em cima de mouse . 

3. Clique no Segue e dê a ele o *identificador* `SegueToPink`:

    [![](images/namesegue.png "Clique no Segue e dê a ele o identificador SegueToPink")](images/namesegue.png#lightbox)  
    

4. Finalmente, adicione o seguinte método ShouldPerformSegue para o `MainViewController` classe:

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

Nesse código coincida com o segueIdentifier para nosso `SegueToPink` atender, para que possamos, em seguida, testar uma condição; uma senha válida neste caso. Se nosso condição retorna `true`, executará o Segue e apresentará o `PinkViewController`. Se `false`, o novo controlador de exibição não será apresentado.

É possível aplicar essa abordagem para qualquer Segue nesse controlador de exibição, verificando o argumento segueIdentifier para o método ShouldPerformSegue. Neste caso temos somente um identificador Segue – `SegueToPink`.

Consulte a solução Storyboards.Conditional o [exemplo Storyboards Manual](https://developer.xamarin.com/samples/monotouch/ManualStoryboard/) para obter um exemplo de funcionamento.

<a name="Using-Storyboard-References" />

## <a name="using-storyboard-references"></a>Usando referências de Storyboard

Uma referência de Storyboard permite usar um design de Storyboard grande e complexo e dividi-lo em Storyboards menores do que obtenham referenciados do original, assim removendo remover a complexidade e fazer resultante individuais Storyboards mais fácil para design e Manter.

Além disso, uma referência de Storyboard pode fornecer um _âncora_ à cena outra no mesmo Storyboard ou uma cena específica em um diferente.

<a name="Referencing-an-External-Storyboard" />

### <a name="referencing-an-external-storyboard"></a>Referenciando um Storyboard externo

Para adicionar uma referência a um Storyboard externo, faça o seguinte:

1. No **Solution Explorer**, com o botão direito no nome do projeto e selecione **adicionar** > **novo arquivo...**   >  **iOS** > **Storyboard**. Insira um **nome** para o novo Storyboard e clique o **novo** botão:
    
    [![](images/ref01.png "A caixa de diálogo Novo arquivo")](images/ref01.png#lightbox)
    
2. Crie o layout de cenas do Storyboard novo como você faria normalmente e salva as alterações: 
    
    [![](images/ref02.png "O layout da nova cena")](images/ref02.png#lightbox)
    
3. Abra o Storyboard que você pretende adicionar a referência a no Designer de iOS.

4. Arraste um **referência de Storyboard** do **caixa de ferramentas** na superfície de Design: 
    
    [![](images/ref03.png "Uma referência de Storyboard")](images/ref03.png#lightbox)
    
5. No **Widget** guia do **propriedades Explorer**, selecione o nome do **Storyboard** criado anteriormente: 

    [![](images/ref04.png "A guia de Widget")](images/ref04.png#lightbox)
    
6. Controle, clique em um Widget de interface do usuário (como um botão) em uma cena existente e criar um novo Segue para a **Storyboard referência** que você acabou de criar: 

    [![](images/ref05.png "Criando um segue")](images/ref05.png#lightbox) 
    
7. No menu pop-up selecionar **Mostrar** para concluir o Segue: 

    [![](images/ref06.png "Selecionando Mostrar para concluir o Segue.")](images/ref06.png#lightbox) 
    
8. Salve suas alterações para o Storyboard.

Quando o aplicativo é executado e o usuário clica no elemento de interface do usuário que você criou o Segue do, o controlador de exibição inicial do Storyboard externo especificado na referência do Storyboard serão exibida.

<a name="Referencing-a-Specific-Scene-in-an-External-Storyboard" />

### <a name="referencing-a-specific-scene-in-an-external-storyboard"></a>Referenciando uma cena específica em um Storyboard externo

Para adicionar uma referência a uma cena específica um Storyboard externo (e não o controlador de exibição inicial), faça o seguinte:

1. No **Solution Explorer**, clique duas vezes o Storyboard externo para abri-lo para edição.

2. Adicionar uma nova cena e criar seu layout, como faria normalmente: 

    [![](images/ref07.png "O novo layout de cena")](images/ref07.png#lightbox)
    
3. No **Widget** guia do **propriedades Explorer**, insira um **Storyboard ID** controlador da cena novo de exibição de: 

    [![](images/ref08.png "Insira uma ID de Storyboard para o novo controlador de exibição de segundo plano")](images/ref08.png#lightbox)
    
3. Abra o Storyboard que você pretende adicionar a referência a no Designer de iOS.

4. Arraste um **referência de Storyboard** do **caixa de ferramentas** na superfície de Design: 

    [![](images/ref03.png "Uma referência de Storyboard")](images/ref03.png#lightbox)
    
5. No **Widget** guia do **propriedades Explorer**, selecione o nome do **Storyboard** e o **ID de referência** (ID de Storyboard) da Cena criado anteriormente: 

    [![](images/ref09.png "A guia de Widget ")](images/ref09.png#lightbox)
    
6. Controle, clique em um Widget de interface do usuário (como um botão) em uma cena existente e criar um novo Segue para a **Storyboard referência** que você acabou de criar: 

    [![](images/ref10.png "Criando um segue")](images/ref10.png#lightbox) 
    
7. No menu pop-up selecionar **Mostrar** para concluir o Segue: 

    [![](images/ref06.png "Selecionando Mostrar para concluir o Segue.")](images/ref06.png#lightbox) 
    
8. Salve suas alterações para o Storyboard.

Quando o aplicativo é executado e o usuário clica no elemento de interface do usuário que você criou o Segue de cena com o determinado **Storyboard ID** do Storyboard externo especificado na referência do Storyboard será exibida.

<a name="Referencing-a-Specific-Scene-in-the-Same-Storyboard" />

### <a name="referencing-a-specific-scene-in-the-same-storyboard"></a>Referenciando uma cena específica no mesmo Storyboard

Para adicionar uma referência a uma cena específica mesmo Storyboard, faça o seguinte:

1. No **Solution Explorer**, clique duas vezes o Storyboard para abri-lo para edição.

2. Adicionar uma nova cena e criar seu layout, como faria normalmente: 

    [![](images/ref11.png "O novo layout de cena")](images/ref11.png#lightbox)

3. No **Widget** guia do **propriedades Explorer**, insira um **Storyboard ID** controlador da cena novo de exibição de: 

    [![](images/ref12.png "A guia de Widget")](images/ref12.png#lightbox)
    
3. Arraste um **referência de Storyboard** do **caixa de ferramentas** na superfície de Design: 

    [![](images/ref03.png "Uma referência de Storyboard")](images/ref03.png#lightbox)
    
5. No **Widget** guia do **propriedades Explorer**, selecione **ID de referência** (ID de Storyboard) da cena criado acima: 

    [![](images/ref13.png "A guia de Widget")](images/ref13.png#lightbox)
    
6. Controle, clique em um Widget de interface do usuário (como um botão) em uma cena existente e criar um novo Segue para a **Storyboard referência** que você acabou de criar: 

    [![](images/ref14.png "Criando um segue")](images/ref14.png#lightbox) 
    
7. No menu pop-up selecionar **Mostrar** para concluir o Segue: 

    [![](images/ref06.png "Selecionando Mostrar para concluir o Segue.")](images/ref06.png#lightbox) 
    
8. Salve suas alterações para o Storyboard.

Quando o aplicativo é executado e o usuário clica no elemento de interface do usuário que você criou o Segue de cena com o determinado **Storyboard ID** no mesmo Storyboard especificado na referência do Storyboard será exibida.

## <a name="summary"></a>Resumo

Este artigo apresenta o conceito de Storyboards e como eles podem ser úteis no desenvolvimento de aplicativos do iOS. Ele discute nos bastidores, controladores de exibição, modos de exibição e hierarquias de modo de exibição e como cenas são vinculadas junto com tipos diferentes de Segues.  Ele explora também ao instanciar exibir controladores manualmente de um storyboard e criando Segues condicional.



## <a name="related-links"></a>Links relacionados

- [Storyboard manual (exemplo)](https://developer.xamarin.com/samples/ManualStoryboard/)
- [Introdução ao Designer de iOS](~/ios/user-interface/designer/introduction.md)
- [Convertendo em Storyboards](http://developer.apple.com/library/ios/#releasenotes/Miscellaneous/RN-AdoptingStoryboards/)
- [Referência de classe UIStoryboard](https://developer.apple.com/library/ios/#documentation/UIKit/Reference/UIStoryboard_Class/Reference/Reference.html)
