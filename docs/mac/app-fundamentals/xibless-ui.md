---
title: . Storyboard/. xib-o design de interface do usuário menos no Xamarin. Mac
description: Este artigo aborda a criação de uma interface do usuário do aplicativo Xamarin. C# Mac diretamente do código, sem arquivos. Storyboard, arquivos. xib ou Interface Builder.
ms.prod: xamarin
ms.assetid: 02310F58-DCF1-4589-9F4A-065DF64FC0E1
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 03/14/2017
ms.openlocfilehash: b189f80e2875e1e025128fee372e732f3ef28f22
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73021720"
---
# <a name="storyboardxib-less-user-interface-design-in-xamarinmac"></a>. Storyboard/. xib-o design de interface do usuário menos no Xamarin. Mac

_Este artigo aborda a criação de uma interface do usuário do aplicativo Xamarin. C# Mac diretamente do código, sem arquivos. Storyboard, arquivos. xib ou Interface Builder._

## <a name="overview"></a>Visão Geral

Ao trabalhar com C# o e o .net em um aplicativo Xamarin. Mac, você tem acesso aos mesmos elementos e ferramentas da interface do usuário que um desenvolvedor que trabalha no *Objective-C* e no *Xcode* . Normalmente, ao criar um aplicativo Xamarin. Mac, você usará Interface Builder do Xcode com arquivos. Storyboard ou. xib para criar e manter a interface do usuário do aplicativo.

Você também tem a opção de criar parte ou toda a interface do usuário do aplicativo Xamarin. Mac diretamente C# no código. Neste artigo, abordaremos os conceitos básicos da criação de interfaces de usuário e elementos de C# interface do usuário no código.

[![O editor de código Visual Studio para Mac](xibless-ui-images/intro01.png "O editor de código Visual Studio para Mac")](xibless-ui-images/intro01-large.png#lightbox)

<a name="Switching_a_Window_to_use_Code" />

## <a name="switching-a-window-to-use-code"></a>Alternando uma janela para usar código

Ao criar um novo aplicativo Xamarin. Mac Cocoa, você obtém uma janela padrão em branco, por padrão. Essas janelas são definidas em um arquivo **Main. Storyboard** (ou tradicionalmente um **MainWindow. xib**) incluído automaticamente no projeto. Isso também inclui um arquivo **ViewController.cs** que gerencia a exibição principal do aplicativo (ou novamente tradicionalmente um **MainWindow.cs** e um arquivo **MainWindowController.cs** ).

Para alternar para uma janela do Xibless para um aplicativo, faça o seguinte:

1. Abra o aplicativo que você deseja interromper usando os arquivos `.storyboard` ou. xib para definir a interface do usuário no Visual Studio para Mac.
2. Na **painel de soluções**, clique com o botão direito do mouse no arquivo **Main. Storyboard** ou **MainWindow. xib** e selecione **remover**:

    ![Removendo o storyboard ou a janela principal](xibless-ui-images/switch01.png "Removendo o storyboard ou a janela principal")
3. Na **caixa de diálogo remover**, clique no botão **excluir** para remover o. Storyboard ou. xib completamente do projeto:

    ![Confirmando a exclusão](xibless-ui-images/switch02.png "Confirmando a exclusão")

Agora, precisaremos modificar o arquivo **MainWindow.cs** para definir o layout da janela e modificar o arquivo **ViewController.cs** ou **MainWindowController.cs** para criar uma instância de nossa classe de `MainWindow`, já que não estamos mais usando o. Storyboard ou arquivo. xib.

Os aplicativos Xamarin. Mac modernos que usam storyboards para a interface do usuário podem não incluir automaticamente os arquivos **MainWindow.cs**, **ViewController.cs** ou **MainWindowController.cs** . Conforme necessário, basta adicionar uma nova classe C# vazia ao projeto (**Adicionar**  > **novo arquivo...**  > **geral**  > **classe vazia**) e nomeá-la como o arquivo ausente.

### <a name="defining-the-window-in-code"></a>Definindo a janela no código

Em seguida, edite o arquivo **MainWindow.cs** e faça parecer com o seguinte:

```csharp
using System;
using Foundation;
using AppKit;
using CoreGraphics;

namespace MacXibless
{
    public partial class MainWindow : NSWindow
    {
        #region Private Variables
        private int NumberOfTimesClicked = 0;
        #endregion

        #region Computed Properties
        public NSButton ClickMeButton { get; set;}
        public NSTextField ClickMeLabel { get ; set;}
        #endregion

        #region Constructors
        public MainWindow (IntPtr handle) : base (handle)
        {
        }

        [Export ("initWithCoder:")]
        public MainWindow (NSCoder coder) : base (coder)
        {
        }

        public MainWindow(CGRect contentRect, NSWindowStyle aStyle, NSBackingStore bufferingType, bool deferCreation): base (contentRect, aStyle,bufferingType,deferCreation) {
            // Define the user interface of the window here
            Title = "Window From Code";

            // Create the content view for the window and make it fill the window
            ContentView = new NSView (Frame);

            // Add UI elements to window
            ClickMeButton = new NSButton (new CGRect (10, Frame.Height-70, 100, 30)){
                AutoresizingMask = NSViewResizingMask.MinYMargin
            };
            ContentView.AddSubview (ClickMeButton);

            ClickMeLabel = new NSTextField (new CGRect (120, Frame.Height - 65, Frame.Width - 130, 20)) {
                BackgroundColor = NSColor.Clear,
                TextColor = NSColor.Black,
                Editable = false,
                Bezeled = false,
                AutoresizingMask = NSViewResizingMask.WidthSizable | NSViewResizingMask.MinYMargin,
                StringValue = "Button has not been clicked yet."
            };
            ContentView.AddSubview (ClickMeLabel);
        }
        #endregion

        #region Override Methods
        public override void AwakeFromNib ()
        {
            base.AwakeFromNib ();

            // Wireup events
            ClickMeButton.Activated += (sender, e) => {
                // Update count
                ClickMeLabel.StringValue = (++NumberOfTimesClicked == 1) ? "Button clicked one time." : string.Format("Button clicked {0} times.",NumberOfTimesClicked);
            };
        }
        #endregion

    }
}
```

Vamos discutir alguns dos elementos principais.

Primeiro, adicionamos algumas _Propriedades computadas_ que funcionarão como saídas (como se a janela fosse criada em um arquivo. Storyboard ou. xib):

```csharp
public NSButton ClickMeButton { get; set;}
public NSTextField ClickMeLabel { get ; set;}
```

Eles fornecerão acesso aos elementos da interface do usuário que vamos exibir na janela. Como a janela não está sendo replanada de um arquivo. Storyboard ou. xib, precisamos de uma maneira de instanciá-la (como veremos mais adiante na classe `MainWindowController`). Isso é o que esse novo método de construtor faz:

```csharp
public MainWindow(CGRect contentRect, NSWindowStyle aStyle, NSBackingStore bufferingType, bool deferCreation): base (contentRect, aStyle,bufferingType,deferCreation) {
    ...
}
```

É aí que projetaremos o layout da janela e colocaremos todos os elementos da interface do usuário necessários para criar a interface necessária. Antes que possamos adicionar qualquer elemento de interface do usuário a uma janela, ele precisa de uma _exibição de conteúdo_ para conter os elementos:

```csharp
ContentView = new NSView (Frame);
```

Isso cria uma exibição de conteúdo que preencherá a janela. Agora, adicionamos nosso primeiro elemento de interface do usuário, um `NSButton`, à janela:

```csharp
ClickMeButton = new NSButton (new CGRect (10, Frame.Height-70, 100, 30)){
    AutoresizingMask = NSViewResizingMask.MinYMargin
};
ContentView.AddSubview (ClickMeButton);
```

A primeira coisa a observar aqui é que, ao contrário do iOS, o macOS usa notação matemática para definir seu sistema de coordenadas de janela. Portanto, o ponto de origem está no canto inferior esquerdo da janela, com valores crescentes à direita e em direção ao canto superior direito da janela. Quando criamos o novo `NSButton`, levamos isso em conta à medida que definimos sua posição e tamanho na tela.

A propriedade `AutoresizingMask = NSViewResizingMask.MinYMargin` informa o botão que queremos que ele permaneça no mesmo local da parte superior da janela quando a janela é redimensionada verticalmente. Novamente, isso é necessário porque (0, 0) está na parte inferior esquerda da janela.

Por fim, o método `ContentView.AddSubview (ClickMeButton)` adiciona o `NSButton` ao modo de exibição de conteúdo para que ele seja exibido na tela quando o aplicativo é executado e a janela é exibida.

Em seguida, um rótulo é adicionado à janela que exibirá o número de vezes que o `NSButton` foi clicado:

```csharp
ClickMeLabel = new NSTextField (new CGRect (120, Frame.Height - 65, Frame.Width - 130, 20)) {
    BackgroundColor = NSColor.Clear,
    TextColor = NSColor.Black,
    Editable = false,
    Bezeled = false,
    AutoresizingMask = NSViewResizingMask.WidthSizable | NSViewResizingMask.MinYMargin,
    StringValue = "Button has not been clicked yet."
};
ContentView.AddSubview (ClickMeLabel);
```

Como o macOS não tem um elemento de interface do usuário de _rótulo_ específico, adicionamos um `NSTextField` com estilo especialmente e não editável para atuar como um rótulo. Assim como o botão antes, o tamanho e o local levam em conta que (0,0) está na parte inferior esquerda da janela. A propriedade `AutoresizingMask = NSViewResizingMask.WidthSizable | NSViewResizingMask.MinYMargin` está usando o operador **or** para combinar dois recursos de `NSViewResizingMask`. Isso fará com que o rótulo permaneça no mesmo local da parte superior da janela quando a janela for redimensionada verticalmente, reduzida e aumenta em largura, uma vez que a janela é redimensionada horizontalmente.

Novamente, o método `ContentView.AddSubview (ClickMeLabel)` adiciona o `NSTextField` ao modo de exibição de conteúdo para que ele seja exibido na tela quando o aplicativo é executado e a janela é aberta.

### <a name="adjusting-the-window-controller"></a>Ajustando o controlador de janela

Como o design do `MainWindow` não está mais sendo carregado de um arquivo. Storyboard ou. xib, precisaremos fazer alguns ajustes no controlador da janela. Edite o arquivo **MainWindowController.cs** e faça parecer com o seguinte:

```csharp
using System;

using Foundation;
using AppKit;
using CoreGraphics;

namespace MacXibless
{
    public partial class MainWindowController : NSWindowController
    {
        public MainWindowController (IntPtr handle) : base (handle)
        {
        }

        [Export ("initWithCoder:")]
        public MainWindowController (NSCoder coder) : base (coder)
        {
        }

        public MainWindowController () : base ("MainWindow")
        {
            // Construct the window from code here
            CGRect contentRect = new CGRect (0, 0, 1000, 500);
            base.Window = new MainWindow(contentRect, (NSWindowStyle.Titled | NSWindowStyle.Closable | NSWindowStyle.Miniaturizable | NSWindowStyle.Resizable), NSBackingStore.Buffered, false);

            // Simulate Awaking from Nib
            Window.AwakeFromNib ();
        }

        public override void AwakeFromNib ()
        {
            base.AwakeFromNib ();
        }

        public new MainWindow Window {
            get { return (MainWindow)base.Window; }
        }
    }
}

```

Vamos discutir os principais elementos dessa modificação.

Primeiro, definimos uma nova instância da classe `MainWindow` e a atribuímos à propriedade `Window` do controlador da janela base:

```csharp
CGRect contentRect = new CGRect (0, 0, 1000, 500);
base.Window = new MainWindow(contentRect, (NSWindowStyle.Titled | NSWindowStyle.Closable | NSWindowStyle.Miniaturizable | NSWindowStyle.Resizable), NSBackingStore.Buffered, false);
```

Definimos o local da janela da tela com um `CGRect`. Assim como o sistema de coordenadas da janela, a tela define (0, 0) como o canto inferior esquerdo. Em seguida, definimos o estilo da janela usando o operador **or** para combinar dois ou mais recursos de `NSWindowStyle`:

```csharp
... (NSWindowStyle.Titled | NSWindowStyle.Closable | NSWindowStyle.Miniaturizable | NSWindowStyle.Resizable) ...
```

Os seguintes recursos de `NSWindowStyle` estão disponíveis:

- Sem **borda** -a janela não terá borda.
- **Título** -a janela terá uma barra de título.
- **Closable** -a janela tem um botão fechar e pode ser fechada.
- **Miniaturizable** -a janela tem um botão Miniaturize e pode ser minimizada.
- **Redimensionável** -a janela terá um botão de redimensionamento e será redimensionável.
- **Utilitário** – a janela é uma janela de estilo do utilitário (painel).
- **DocModal** -se a janela for um painel, ela será modal de documento em vez de modal do sistema.
- **NonactivatingPanel** -se a janela for um painel, ela não se tornará a janela principal.
- **TexturedBackground** -a janela terá um plano de fundo texturizado.
- Não **dimensionado** – a janela não será dimensionada.
- **UnifiedTitleAndToolbar** -as áreas de título e barra de ferramentas da janela serão Unidas.
- **HUD** -a janela será exibida como um painel de exibição de cabeçalhos.
- **FullScreenWindow** -a janela pode entrar no modo de tela inteira.
- **FullSizeContentView** -a exibição de conteúdo da janela está atrás da área de título e da barra de ferramentas.

As duas últimas propriedades definem o _tipo de buffer_ para a janela e se o desenho da janela será adiado. Para obter mais informações sobre `NSWindows`, consulte [a introdução](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/WinPanel/Introduction.html#//apple_ref/doc/uid/10000031-SW1) da Apple à documentação do Windows.

Por fim, como a janela não está sendo replanada de um arquivo. Storyboard ou. xib, precisamos simular em nosso **MainWindowController.cs** chamando o método de `AwakeFromNib` do Windows:

```csharp
Window.AwakeFromNib ();
```

Isso permitirá que você codifique na janela, assim como uma janela padrão carregada de um arquivo. Storyboard ou. xib.

### <a name="displaying-the-window"></a>Exibindo a janela

Com o arquivo. Storyboard ou. xib removido e os arquivos **MainWindow.cs** e **MainWindowController.cs** modificados, você usará a janela da mesma forma que faria com qualquer janela normal que tivesse sido criada no interface Builder do Xcode com um arquivo. xib.

O seguinte criará uma nova instância da janela e seu controlador e exibirá a janela na tela:

```csharp
private MainWindowController mainWindowController;
...

mainWindowController = new MainWindowController ();
mainWindowController.Window.MakeKeyAndOrderFront (this);
```

Neste ponto, se o aplicativo for executado e o botão tiver clicado duas vezes, será exibido o seguinte:

![Uma execução de aplicativo de exemplo](xibless-ui-images/run01.png "Uma execução de aplicativo de exemplo")

## <a name="adding-a-code-only-window"></a>Adicionando uma janela somente código

Se quisermos adicionar apenas uma janela de código, xibless a um aplicativo Xamarin. Mac existente, clique com o botão direito do mouse no projeto na **painel de soluções** e selecione **Adicionar**  > **novo arquivo..** . Na caixa de diálogo **novo arquivo** , escolha **Xamarin. Mac**  > **janela Cocoa com controlador**, conforme ilustrado abaixo:

![Adicionando um novo controlador de janela](xibless-ui-images/add01.png "Adicionando um novo controlador de janela")

Assim como antes, vamos excluir o arquivo default. Storyboard ou. xib do projeto (neste caso, **SecondWindow. xib**) e seguir as etapas na seção [alternando uma janela para usar código](#Switching_a_Window_to_use_Code) acima para cobrir a definição da janela para código.

## <a name="adding-a-ui-element-to-a-window-in-code"></a>Adicionando um elemento de interface do usuário a uma janela no código

Se uma janela foi criada no código ou carregada a partir de um arquivo. Storyboard ou. xib, pode haver ocasiões em que desejamos adicionar um elemento de interface do usuário a uma janela a partir do código. Por exemplo:

```csharp
var ClickMeButton = new NSButton (new CGRect (10, 10, 100, 30)){
    AutoresizingMask = NSViewResizingMask.MinYMargin
};
MyWindow.ContentView.AddSubview (ClickMeButton);
```

O código acima cria um novo `NSButton` e o adiciona à instância da janela de `MyWindow` para exibição. Basicamente, qualquer elemento de interface do usuário que possa ser definido no Interface Builder do Xcode em um arquivo. Storyboard ou. xib pode ser criado no código e exibido em uma janela.

## <a name="defining-the-menu-bar-in-code"></a>Definindo a barra de menus no código

Devido às limitações atuais no Xamarin. Mac, não é recomendável que você crie a barra de menus do aplicativo Xamarin. Mac – `NSMenuBar` – no código, mas continue a usar o arquivo **Main. Storyboard** ou **MainMenu. xib** para defini-lo. Dito isso, você pode adicionar e remover menus e itens de menu C# no código.

Por exemplo, edite o arquivo **AppDelegate.cs** e faça com que o método `DidFinishLaunching` se pareça com o seguinte:

```csharp
public override void DidFinishLaunching (NSNotification notification)
{
    mainWindowController = new MainWindowController ();
    mainWindowController.Window.MakeKeyAndOrderFront (this);

    // Create a Status Bar Menu
    NSStatusBar statusBar = NSStatusBar.SystemStatusBar;

    var item = statusBar.CreateStatusItem (NSStatusItemLength.Variable);
    item.Title = "Phrases";
    item.HighlightMode = true;
    item.Menu = new NSMenu ("Phrases");

    var address = new NSMenuItem ("Address");
    address.Activated += (sender, e) => {
        Console.WriteLine("Address Selected");
    };
    item.Menu.AddItem (address);

    var date = new NSMenuItem ("Date");
    date.Activated += (sender, e) => {
        Console.WriteLine("Date Selected");
    };
    item.Menu.AddItem (date);

    var greeting = new NSMenuItem ("Greeting");
    greeting.Activated += (sender, e) => {
        Console.WriteLine("Greetings Selected");
    };
    item.Menu.AddItem (greeting);

    var signature = new NSMenuItem ("Signature");
    signature.Activated += (sender, e) => {
        Console.WriteLine("Signature Selected");
    };
    item.Menu.AddItem (signature);
}
```

O acima cria um menu da barra de status a partir do código e o exibe quando o aplicativo é iniciado. Para obter mais informações sobre como trabalhar com menus, consulte a documentação de nossos [menus](~/mac/user-interface/menu.md) .

## <a name="summary"></a>Resumo

Este artigo fez uma visão detalhada da criação de uma interface do usuário do aplicativo Xamarin. Mac C# no código em oposição ao uso de interface Builder do Xcode com arquivos. Storyboard ou. xib.

## <a name="related-links"></a>Links relacionados

- [MacXibless (exemplo)](https://docs.microsoft.com/samples/xamarin/mac-samples/macxibless)
- [Windows](~/mac/user-interface/window.md)
- [Menus](~/mac/user-interface/menu.md)
- [Diretrizes de Interface Humana do macOS](https://developer.apple.com/macos/human-interface-guidelines/overview/themes/)
- [Introdução ao Windows](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/WinPanel/Introduction.html)
