---
title: Storyboards no Xamarin. iOS
description: Este documento fornece uma introdução aos storyboards no Xamarin. iOS. Ele descreve como um Storyboard é usado para definir uma interface do usuário, continuações e como usar o designer do iOS para editar arquivos de storyboard.
ms.prod: xamarin
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/22/2017
ms.openlocfilehash: e6bbc9d7d404136984e3d1ec93186dc85691c751
ms.sourcegitcommit: d1f0e0a9100548cfe0960ed2225b979cc1d7c28f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/01/2020
ms.locfileid: "96439724"
---
# <a name="storyboards-in-xamarinios"></a>Storyboards no Xamarin. iOS

Neste guia, explicaremos o que é um storyboard e examinaremos alguns dos principais componentes, como continuações. Veremos como os storyboards podem ser criados e usados e quais vantagens eles têm para um desenvolvedor.

Antes de o formato de arquivo de storyboard ter sido introduzido pela Apple como uma representação visual da interface do usuário de um aplicativo iOS, os desenvolvedores criaram arquivos XIB para cada controlador de exibição e programaram a navegação entre cada exibição manualmente.  O uso de um storyboard permite que o desenvolvedor defina ambos os controladores de exibição e a navegação entre eles em uma superfície de design e ofereça edição WYSIWYG da interface do usuário do aplicativo.

Um storyboard pode ser criado e aberto com Visual Studio para Mac. Este guia também mostrará como usar o Interface Builder do Xcode para criar seus storyboards ao usar C# para programar a navegação.

## <a name="requirements"></a>Requisitos

Os storyboards podem ser usados com o Xcode e iniciados de dentro de um projeto Xamarin. iOS no Visual Studio para Mac.

## <a name="what-is-a-storyboard"></a>O que é um storyboard?

Um Storyboard é a representação visual de todas as telas em um aplicativo. Ele contém uma sequência de cenas, com cada cena que representa um *controlador de exibição* e suas *exibições*. Essas exibições podem conter objetos e [controles](~/ios/user-interface/controls/index.md) que permitem que o usuário interaja com seu aplicativo. Essa coleção de exibições e controles (ou *subexibições*) é conhecida como *hierarquia de exibição de conteúdo*. As cenas são conectadas por objetos transição, que representam uma transição entre os controladores de exibição. Isso normalmente é obtido com a criação de um transição entre um objeto na exibição inicial e a exibição de conexão. As relações na superfície de design são ilustradas na imagem a seguir:

 [![As relações na superfície de design são ilustradas nesta imagem](images/storyboardsview.png)](images/storyboardsview.png#lightbox)

Como mostrado, o storyboard apresenta cada um dos seus bastidores com conteúdo já renderizado e ilustra as conexões entre eles. Quando falamos sobre cenas em um iPhone, é seguro pressupor que uma *cena* no storyboard seja igual a uma *tela* de conteúdo no dispositivo. No entanto, com um iPad, é possível que várias cenas apareçam de uma só vez, por exemplo, usando um controlador de exibição popover.

Há muitas vantagens em usar storyboards para criar a interface do usuário do aplicativo, especialmente ao usar o Xamarin. Em primeiro lugar, é uma representação visual da interface do usuário, pois todos os objetos – incluindo [controles personalizados](../designer/ios-designable-controls-overview.md) – são renderizados em tempo de design.
Isso significa que, antes de criar ou implantar seu aplicativo, você pode visualizar sua aparência e fluxo. Veja a imagem anterior, por exemplo. Podemos perceber de uma visão rápida da superfície de design de quantas cenas há, o layout de cada exibição e como tudo está relacionado. Isso é o que torna os storyboards tão poderosos.

Você também pode gerenciar eventos com storyboards.  A maioria dos controles da interface do usuário tem uma lista de possíveis eventos no Painel de Propriedades. O manipulador de eventos pode ser adicionado aqui e concluído em um método parcial na classe View Controllers.

O conteúdo de um Storyboard é armazenado como um arquivo XML. No momento da compilação, todos os `.storyboard` arquivos são compilados em arquivos binários conhecidos como nibs. Em tempo de execução, esses nibs são inicializados e instanciados para criar novas exibições.

## <a name="segues"></a>Continuações

Um objeto *transição*, ou *transição*, é usado no desenvolvimento do IOS para representar uma transição entre cenas. Para criar um transição, mantenha pressionada a tecla **Ctrl** e clique-arraste de uma cena para outra. À medida que você arrasta o mouse, um conector azul é exibido indicando onde o transição levará. Isso é mostrado na imagem a seguir:

 [![Um conector azul é exibido, indicando onde o transição levará conforme demonstrado nesta imagem](images/createsegue.png)](images/createsegue.png#lightbox)

No mouse, um menu é exibido, permitindo que você escolha a ação para o transição. Pode ser semelhante às seguintes imagens:

**Classes anteriores ao Ios 8 e tamanho**:

[![O menu suspenso transição de ação sem classes de tamanho](images/segue1.png)](images/segue1.png#lightbox)

**Ao usar classes de tamanho e continuações adaptável**:

[![O menu suspenso da ação transição com classes de tamanho](images/16new.png)](images/16new.png#lightbox)

> [!IMPORTANT]
> Se você estiver usando o VMWare para sua máquina virtual do Windows, CTRL-clique será mapeado como o botão _direito_ do mouse, por padrão. Para criar um transição, edite suas preferências de teclado por meio do teclado de **preferências**  >  **&**  >  **atalhos do mouse** do mouse e remapeie o **botão secundário** , conforme ilustrado abaixo:
>
> [![Configurações de preferência de teclado e mouse](images/image22.png)](images/image22.png#lightbox)
>
> Agora você deve ser capaz de adicionar um transição entre seus controladores de exibição normalmente.

Há diferentes tipos de transições, cada uma dando controle sobre como um novo controlador de exibição é apresentado ao usuário e como ele interage com outros controladores de exibição no storyboard. Eles são explicados abaixo. Também é possível subclasse de um objeto transição para implementar uma transição personalizada:

- **Mostrar/enviar por** Push – um push transição adiciona o controlador de exibição à pilha de navegação. Ele pressupõe que o controlador de exibição que originou o Push faz parte do mesmo controlador de navegação que o controlador de exibição que está sendo adicionado à pilha. Isso faz a mesma coisa que  `pushViewController` e é geralmente usado quando há alguma relação entre os dados nas telas. O uso do transição de envio por push fornece a você o luxo de ter uma barra de navegação com um botão voltar e um título adicionado a cada exibição na pilha, permitindo a navegação detalhada na hierarquia de exibição.
- **Modal** – um transição modal crie uma relação entre quaisquer dois controladores de exibição em seu projeto, com a opção de mostrar uma transição animada. O controlador de exibição filho obscurecerá completamente o controlador de exibição pai quando colocado na exibição. Ao contrário de um transição de push, que adiciona um botão voltar para nós, você deve usar um `DismissViewController` ao usar um transição modal para retornar ao controlador de exibição anterior.
- **Personalizado** – qualquer transição personalizado pode ser criado como uma subclasse de `UIStoryboardSegue` .
- **Desenrolar** – um transição de desenrolamento pode ser usado para navegar de volta por um transição de Push ou modal, por exemplo, ignorando o controlador de exibição apresentado de modo restrito. Além disso, você pode desenrolar por não apenas um, mas uma série de continuações de push e modal e voltar várias etapas em sua hierarquia de navegação com uma única ação de desenrolamento. Para entender como usar um desenrolamento de transição no iOS, leia a receita  [criando continuações de desenrolamento](https://github.com/xamarin/recipes/tree/master/Recipes/ios/general/storyboard/unwind_segue) .
- De **origem** – um transição de origem informativa indica a cena que contém o controlador de exibição inicial e, portanto, a exibição que o usuário verá primeiro. Ele é representado pelo transição mostrado aqui:

    [![Um transição de origem](images/sourcelesssegue.png)](images/sourcelesssegue.png#lightbox)

### <a name="adaptive-segue-types"></a>Tipos de transição adaptável

 o iOS 8 introduziu [classes de tamanho](../storyboards/unified-storyboards.md#size-classes) para permitir que um arquivo de storyboard do IOS funcione com todos os tamanhos de tela disponíveis, permitindo que os desenvolvedores criem uma interface do usuário para todos os dispositivos IOS. Por padrão, todos os novos aplicativos Xamarin. iOS usam classes de tamanho. Para usar classes de tamanho de um projeto mais antigo, consulte o guia [introdução ao storyboards unificados](~/ios/user-interface/storyboards/unified-storyboards.md) .

Qualquer aplicativo usando classes de tamanho também usará o novo [*continuações adaptável*](unified-storyboards.md). Ao usar classes de tamanho, lembre-se de que você não está especificando diretamente se está usando um iPhone ou iPad. Em outras palavras, você está criando uma interface do usuário que sempre terá a mesma aparência, independentemente da quantidade de espaço real com a qual precisa trabalhar. O continuações adaptável funciona por julgamento o ambiente e determinando a melhor maneira de apresentar conteúdo. Os continuações adaptáveis são mostrados abaixo:

[![A lista suspensa continuações adaptável](images/adaptivesegue.png)](images/adaptivesegue.png#lightbox)

|Transição|Descrição|
|--- |--- |
|Mostrar|Isso é muito semelhante a um transição de push, mas leva o conteúdo da tela para conta.|
|Mostrar detalhes|Se o aplicativo exibir uma exibição mestre e de detalhes (por exemplo, em um controlador de exibição de divisão em um iPad), o conteúdo substituirá a exibição de detalhes. Se o aplicativo exibir apenas o mestre ou detalhes, o conteúdo substituirá a parte superior da pilha do controlador de exibição.|
|Apresentação|Isso é semelhante ao transição modal e permite a seleção de estilos de apresentação e de transição.|
|Apresentação do popover|Isso apresenta conteúdo como um popover.|

### <a name="transferring-data-with-segues"></a>Transferindo dados com continuações

Os benefícios de um transição não terminam com transições. Eles também podem ser usados para gerenciar a transferência de dados entre controladores de exibição. Isso é obtido substituindo o `PrepareForSegue` método no controlador de exibição inicial e manipulando os dados por conta própria. Quando o transição é disparado – por exemplo, com um pressionamento de botão – o aplicativo chamará esse método, fornecendo uma oportunidade de preparar o novo controlador de exibição *antes que* ocorra qualquer navegação. O código a seguir do exemplo [Phoneword](/samples/xamarin/ios-samples/hello-ios) demonstra isso:

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

Neste exemplo, o `PrepareForSegue` método será chamado quando o transição for disparado pelo usuário. Você deve primeiro criar uma instância do controlador de exibição "recebimento" e defini-la como o controlador de exibição de destino do transição. Isso é feito pela linha de código abaixo:

```csharp
var callHistoryController = segue.DestinationViewController as CallHistoryController;
```

O método agora tem a capacidade de definir propriedades no `DestinationViewController` . Este exemplo aproveita essa capacidade passando uma lista chamada `PhoneNumbers` para o `CallHistoryController` e atribuindo-a a um objeto de mesmo nome:

```csharp
if (callHistoryController != null) {
        callHistoryController.PhoneNumbers = PhoneNumbers;
    }
```

Depois que a transição for concluída, o usuário verá o `CallHistoryController` com a lista preenchida.

## <a name="adding-a-storyboard-to-a-non-storyboard-project"></a>Adicionando um storyboard a um projeto que não seja um storyboard

Na ocasião, talvez seja necessário adicionar um storyboard a um arquivo anterior que não seja um Storyboard. Você pode simplificar o processo em Visual Studio para Mac seguindo estas etapas:

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

1. Crie um novo arquivo de storyboard navegando até o **arquivo > novo arquivo > iOS > storyboard**.

    [![A caixa de diálogo novo arquivo](images/new-storyboard-xs.png)](images/new-storyboard-xs.png#lightbox)

2. Adicione o nome do storyboard à seção da **interface principal** do **info. plist**.

    [![O editor info. plist](images/infoplist.png)](images/infoplist.png#lightbox)

    Isso faz o equivalente à instanciação do controlador de exibição inicial no `FinishedLaunching` método dentro do delegado do aplicativo. Com essa opção definida, o aplicativo instancia uma janela (consulte a próxima etapa), carrega o storyboard principal e atribui uma instância do controlador de exibição inicial do storyboard (aquele ao lado do transição sem origem) como a `RootViewController` propriedade da janela. Em seguida, ele torna a janela visível na tela.

3. No `AppDelegate` , substitua o método padrão `Window` pelo código a seguir para implementar a Propriedade Window:

    ```csharp
    public override UIWindow Window {
        get;
        set;
    }
    ```

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. Crie um novo arquivo de storyboard clicando com o botão direito do mouse no projeto para **adicionar > novo arquivo > iOS > storyboard vazio**.

    [![A caixa de diálogo novo item](images/new-storyboard-vs.png)](images/new-storyboard-vs.png#lightbox)

2. Adicione o nome do storyboard à seção da **interface principal** do aplicativo Ios.

    [![O editor info. plist](images/ios-app.png)](images/ios-app.png#lightbox)

    Isso faz o equivalente à instanciação do controlador de exibição inicial no `FinishedLaunching` método dentro do delegado do aplicativo. Com essa opção definida, o aplicativo instancia uma janela (consulte a próxima etapa), carrega o storyboard principal e atribui uma instância do controlador de exibição inicial do storyboard (aquele ao lado do transição sem origem) como a `RootViewController` propriedade da janela. Em seguida, ele torna a janela visível na tela.

3. No `AppDelegate` , substitua o método padrão `Window` pelo código a seguir para implementar a Propriedade Window:

    ```csharp
    public override UIWindow Window {
        get;
        set;
    }
    ```

-----

## <a name="creating-a-storyboard-with-xcode"></a>Criando um storyboard com o Xcode

Um storyboard pode ser criado e modificado usando o Xcode para uso em seus aplicativos iOS desenvolvidos com Visual Studio para Mac.

Os storyboards totalmente substituem arquivos XIB individuais em seu projeto, no entanto, os controladores de exibição individuais em um storyboard ainda podem ser instanciados usando `Storyboard.InstantiateViewController` .

Às vezes, os aplicativos têm requisitos especiais que não podem ser tratados com as transições de storyboard internas fornecidas pelo designer. Por exemplo, se você criar um aplicativo que inicie telas diferentes do mesmo botão, dependendo do estado atual de um aplicativo, talvez você queira criar uma instância dos controladores de exibição manualmente e programar a transição por conta própria.

A captura de tela a seguir mostra dois controladores de exibição na superfície de design sem nenhum transição entre eles. A próxima seção percorre como essa transição pode ser configurada no código.

1. Adicionar um _storyboard vazio do iPhone_ a um projeto de projeto existente:

    [![Adicionando storyboard](images/add-storyboard2.png)](images/add-storyboard2.png#lightbox)

2. Clique com o botão direito do mouse no arquivo de storyboard e selecione **abrir com > Xcode Interface Builder** para abri-lo no Xcode.

   > Dicas Se preferir usar o compilador de interface do Xcode por padrão, você poderá escolher na Visual Studio para Mac preferências em **projetos > Ios**.  A partir do Visual Studio versão 16,9 e Visual Studio para Mac 8,9, o Xcode Interface Builder será a única opção. *

   ![Selecionando a ferramenta de designer preferida.](images/set-preferred-designer-tool.png)

3. No Xcode, abra a biblioteca (por meio da **exibição > mostrar biblioteca** ou **Shift + Command + L**) para mostrar uma lista de objetos que podem ser adicionados ao storyboard. Adicione um `Navigation Controller` ao storyboard arrastando o objeto da lista para o storyboard. Por padrão, o `Navigation Controller` fornecerá duas telas. A tela à direita é uma `TableViewController` que você substituirá por uma exibição mais simples para que possa ser removida clicando na exibição e pressionando a tecla Delete.

    [![Adicionando um NavigationController da biblioteca](images/add-navigation-controller.png)](images/add-navigation-controller.png#lightbox)

4. Esse controlador de exibição terá sua própria classe personalizada e também precisará de sua própria ID de storyboard. Quando você clica na caixa acima dessa exibição recém-adicionada, há três ícones, o mais à esquerda, que representa o controlador de exibição para a exibição. Ao selecionar esse ícone, você pode definir os valores de classe e ID na guia identidade do painel direito. Defina esses valores como `MainViewController` e certifique-se de verificar `Use Storyboard ID` .

    [![Configurando o MainViewController no painel de identidade](images/identity-panel.png)](images/identity-panel.png#lightbox)

5. Usando a biblioteca novamente, arraste um controle do controlador de exibição para a tela. Isso será definido como o controlador de exibição raiz. Segurando a chave de **controle** , clique e arraste do controlador de navegação à esquerda para o controlador de exibição recém-adicionado à direita e selecione controlador de **exibição raiz** no menu.

    [![Adicionando um NavigationController da biblioteca e definindo MainViewController como um controlador de exibição raiz](images/add-view-controller.png)](images/add-view-controller.png#lightbox)

6. Este aplicativo navegará para outra exibição, portanto, adicione mais uma exibição ao storyboard, assim como antes. Chame- `PinkViewController` o e defina esses valores da mesma maneira que com o `MainViewController` .

    [![Adicionando um controlador de exibição adicional](images/add-additional-view-controller.png)](images/add-additional-view-controller.png#lightbox)

7. Como o controlador de exibição terá um plano de fundo rosa, defina essa propriedade no painel atributos usando a lista suspensa ao lado de `Background` .

    [![Adicionando um controlador de exibição adicional](images/set-pink-background.png)](images/set-pink-background.png#lightbox)

8. Como queremos que o `MainViewController` navegue para o `PinkViewController` , o primeiro precisará de um botão para interagir. Use a biblioteca para adicionar um botão ao `MainViewController` .

    [![Adicionando um botão ao MainViewController](images/add-button.png)](images/add-button.png#lightbox)

O storyboard está concluído, mas se você implantar o projeto agora, você obterá uma tela em branco. Isso porque você ainda precisa dizer ao IDE para usar o storyboard e configurar um controlador de exibição raiz para servir como a primeira exibição. Normalmente, isso pode ser feito por meio de opções de projeto, conforme mostrado anteriormente. No entanto, neste exemplo, adicionaremos o seguinte código ao **AppDelegate** para obter o mesmo resultado:

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

Isso é uma grande quantidade de código, mas apenas algumas linhas não são familiares. Primeiro, você registra o storyboard com o **AppDelegate** passando o nome do storyboard, **MainStoryboard**. Em seguida, você informa ao aplicativo para criar uma instância de um controlador de exibição inicial do storyboard chamando `InstantiateInitialViewController` no storyboard e define esse controlador de exibição como o controlador de exibição raiz do aplicativo. Esse método determina a primeira tela que o usuário vê e cria uma nova instância desse controlador de exibição.

Observe no painel de solução que o IDE criou uma `MainViewcontroller.cs` classe e seu `*.designer.cs` arquivo correspondente quando você adicionou o nome de classe ao painel de propriedades na etapa 4. Essa classe criou um Construtor especial que inclui uma classe base:

```csharp
public MainViewController (IntPtr handle) : base (handle)
{
}
```

Ao criar um storyboard usando o Xcode, o IDE adicionará automaticamente o atributo [[Register]](xref:Foundation.RegisterAttribute) na parte superior da `*.designer.cs` classe e passará um identificador de cadeia de caracteres, que é idêntico à ID do storyboard especificado na etapa anterior. Isso vinculará o C# à cena relevante no storyboard.

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

Para obter mais informações sobre como registrar classes e métodos, consulte [registrar registrador](../../internals/registrar.md).

A última etapa dessa classe é conectar o botão e a transição para o controlador de exibição rosa. Você criará uma instância do `PinkViewController` storyboard e, em seguida, programará um push transição com `PushViewController` , conforme ilustrado pelo seguinte código de exemplo:

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

![Telas de execução do aplicativo de exemplo](images/finishedstoryboard.png)

## <a name="conditional-segues"></a>Continuações condicional

Geralmente, a mudança de um controlador de exibição para o próximo depende de uma determinada condição. Por exemplo, se estivermos fazendo uma tela de logon simples, só desejaremos mover para a próxima tela *se* o nome de usuário e a senha tiverem sido verificados.

No próximo exemplo, adicionaremos um campo de senha à amostra anterior. O usuário só poderá acessar o *PinkViewController* se inserir a senha correta; caso contrário, um erro será exibido.

Antes de começar, siga as etapas de 1 a 8 anteriores. Nestas etapas, criamos nosso storyboard, começamos a criar nossa interface do usuário e dizem ao nosso representante de aplicativo qual controlador de exibição usar como seu RootViewController.

1. Agora, vamos criar nossa interface do usuário e adicionar as exibições adicionais listadas ao `MainViewController` para que ela pareça que na captura de tela a seguir:

    - UITextField
        - Nome: PasswordTextField
        - Espaço reservado: ' Insira a senha secreta '
    - UILabel
        - Texto: ' erro: senha incorreta. Você não deve passar! '
        - Cor: vermelha
        - Alinhamento: centro
        - Linhas: 2
        - Caixa de seleção ' Hidden ' marcada    

    [![Linhas centrais](images/passwordvc.png)](images/passwordvc.png#lightbox)

2. Crie um transição entre o botão ir para rosa e o controlador de exibição por meio de controle – arrastando do *PinkButton* para o *PinkViewController* e, em seguida, selecionando **Push** no mouse-up.

3. Clique no transição e dê a ele o *identificador* `SegueToPink` :

    [![Clique no transição e dê a ele o identificador SegueToPink](images/namesegue.png)](images/namesegue.png#lightbox)  

4. Por fim, adicione o seguinte `ShouldPerformSegue` método à `MainViewController` classe:

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

Neste código, combinamos o segueIdentifier ao nosso `SegueToPink` transição, portanto, podemos testar uma condição; nesse caso, uma senha válida. Se a condição for retornada `true` , o transição executará e apresentará o `PinkViewController` . Se `false` , o novo controlador de exibição não será apresentado.

Podemos aplicar essa abordagem a qualquer transição nesse controlador de exibição, verificando o argumento segueIdentifier para o método ShouldPerformSegue. Nesse caso, temos apenas um identificador transição – `SegueToPink` .

Consulte a solução storyboards. Conditional no exemplo de [storyboards manual](/samples/xamarin/ios-samples/manualstoryboard) para obter um exemplo de trabalho.

## <a name="summary"></a>Resumo

Este artigo apresenta o conceito de storyboards e como eles podem ser benéficos no desenvolvimento de aplicativos iOS. Ele aborda cenas, exibir controladores, exibições e exibir hierarquias e como os bastidores são vinculados com diferentes tipos de continuações.  Ele também explora a instanciação de controladores de exibição manualmente de um storyboard e a criação de continuações condicional.

## <a name="related-links"></a>Links Relacionados

- [Storyboard manual (exemplo)](/samples/xamarin/ios-samples/manualstoryboard/)
- [Introdução ao designer do iOS](../designer/introduction.md)
- [Convertendo em storyboards](https://developer.apple.com/library/ios/#releasenotes/Miscellaneous/RN-AdoptingStoryboards/)
- [Referência de classe UIStoryboard](https://developer.apple.com/library/ios/#documentation/UIKit/Reference/UIStoryboard_Class/Reference/Reference.html)
