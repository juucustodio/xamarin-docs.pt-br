---
title: design da interface do usuário.Storyboard/.XIB-Less no xamarin. Mac
description: Este artigo aborda a criação de interface do usuário de um aplicativo xamarin. MAC diretamente do C# código, sem os arquivos de storyboard, arquivos. XIB ou Interface Builder.
ms.prod: xamarin
ms.assetid: 02310F58-DCF1-4589-9F4A-065DF64FC0E1
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 03/14/2017
ms.openlocfilehash: 076c6464359a58c2b36d157d9620673b0644cd4a
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61042231"
---
# <a name="storyboardxib-less-user-interface-design-in-xamarinmac"></a>design da interface do usuário.Storyboard/.XIB-Less no xamarin. Mac

_Este artigo aborda a criação de interface do usuário de um aplicativo xamarin. MAC diretamente do C# código, sem os arquivos de storyboard, arquivos. XIB ou Interface Builder._

## <a name="overview"></a>Visão geral

Ao trabalhar com C# e do .NET em um aplicativo xamarin. Mac, você tem acesso para os mesmos elementos de interface do usuário e as ferramentas de que um desenvolvedor que trabalha *Objective-C* e *Xcode* faz. Normalmente, ao criar um aplicativo xamarin. Mac, você usará o Interface Builder do Xcode com arquivos. XIB ou de storyboard para criar e manter você a interface do usuário do aplicativo.

Você também tem a opção de criação de algumas ou todas da interface do usuário do seu aplicativo xamarin. MAC diretamente no C# código. Neste artigo, abordaremos os fundamentos da criação de interfaces do usuário e elementos de interface do usuário no C# código.

[![O Visual Studio para o editor de código do Mac](xibless-ui-images/intro01.png "do Visual Studio para o editor de código do Mac")](xibless-ui-images/intro01-large.png#lightbox)

<a name="Switching_a_Window_to_use_Code" />

## <a name="switching-a-window-to-use-code"></a>Alternar uma janela para usar o código

Quando você cria um novo aplicativo xamarin. Mac Cocoa, você obtém uma janela de padrão em branco, por padrão. Esse windows é definido em um **Main. Storyboard** (ou tradicionalmente um **MainWindow.xib**) automaticamente incluído no projeto do arquivo. Isso também inclui um **ViewController.cs** arquivo que gerencia o modo de exibição principal do aplicativo (ou tradicionalmente novamente uma **MainWindow.cs** e um **MainWindowController.cs** arquivo).

Para alternar para uma janela de Xibless para um aplicativo, faça o seguinte:

1. Abra o aplicativo que você deseja parar de usar `.storyboard` ou arquivos. XIB para definir a interface do usuário no Visual Studio para Mac.
2. No **painel de soluções**, clique com botão direito no **Main. Storyboard** ou **MainWindow.xib** arquivo e selecione **remover**: 

    ![Removendo o storyboard principal ou a janela](xibless-ui-images/switch01.png "removendo o storyboard principal ou a janela")
3. Dos **caixa de diálogo Remover**, clique no **excluir** botão para remover completamente o storyboard ou. XIB do projeto: 

    ![Confirmar a exclusão](xibless-ui-images/switch02.png "confirmar a exclusão")

Agora, precisamos modificar a **MainWindow.cs** arquivo para definir o layout da janela e modificar as **ViewController.cs** ou **MainWindowController.cs** arquivo para criar um instância do nosso `MainWindow` classe como não estamos usando o arquivo de storyboard ou. XIB.

Aplicativos modernos do xamarin. MAC que usam Storyboards para sua interface do usuário não pode incluir automaticamente o **MainWindow.cs**, **ViewController.cs** ou **MainWindowController.cs** arquivos. Conforme necessário, basta adicionar um novo vazio C# classe ao projeto (**Add** > **arquivo novo...**   >  **Gerais** > **classe vazia**) e nomeie-o mesmo que o arquivo está faltando. 


### <a name="defining-the-window-in-code"></a>Definir a janela de código

Em seguida, edite o **MainWindow.cs** de arquivo e torná-lo semelhante ao seguinte:

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

Vamos falar sobre alguns dos principais elementos.

Primeiro, adicionamos algumas _propriedades computadas_ que atuará como saídas (como se a janela foi criada em um arquivo de storyboard ou. XIB):

```csharp
public NSButton ClickMeButton { get; set;}
public NSTextField ClickMeLabel { get ; set;}
```

Eles nos fornecerá acesso aos elementos da interface do usuário que vamos exibir na janela. Como a janela não estiver sendo inflada de um arquivo de storyboard ou. XIB, precisamos de uma maneira de criar uma instância dele (como veremos posteriormente no `MainWindowController` classe). É o que faz esse novo método de construtor:

```csharp
public MainWindow(CGRect contentRect, NSWindowStyle aStyle, NSBackingStore bufferingType, bool deferCreation): base (contentRect, aStyle,bufferingType,deferCreation) {
    ...
}
```

Isso é onde podemos projetar o layout da janela e coloque quaisquer elementos de interface do usuário necessários para criar a interface do usuário necessária. Antes de podermos adicionar todos os elementos da interface do usuário para uma janela, ela precisa de um _exibição de conteúdo_ para conter os elementos:

```csharp
ContentView = new NSView (Frame);
```

Isso cria uma exibição de conteúdo que preencherá a janela. Agora vamos adicionar nosso primeiro elemento de interface do usuário, um `NSButton`, para a janela:

```csharp
ClickMeButton = new NSButton (new CGRect (10, Frame.Height-70, 100, 30)){
    AutoresizingMask = NSViewResizingMask.MinYMargin
};
ContentView.AddSubview (ClickMeButton);
```

A primeira coisa a observar aqui é que, ao contrário do iOS, macOS usa notação matemática para definir seu sistema de coordenadas da janela. Portanto, o ponto de origem está no canto inferior esquerdo da janela, com valores crescentes direita e na direção do canto superior direito da janela. Quando criamos o novo `NSButton`, podemos levar isso em conta conforme definimos sua posição e tamanho na tela.

O `AutoresizingMask = NSViewResizingMask.MinYMargin` propriedade informa o botão que queremos que ela permaneça no mesmo local da parte superior da janela quando a janela é redimensionada verticalmente. Novamente, isso é necessário porque (0,0) está na parte inferior esquerda da janela.

Por fim, o `ContentView.AddSubview (ClickMeButton)` método adiciona o `NSButton` para a exibição de conteúdo para que ela será exibida na tela quando o aplicativo é executado e a janela exibida.

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

Como o macOS não tem um determinado _rótulo_ elemento de interface do usuário, adicionamos um especialmente com estilo, não editável `NSTextField` para atuar como um rótulo. Assim como o botão antes, o tamanho e local leva em consideração esse (0,0) está na parte inferior esquerda da janela. O `AutoresizingMask = NSViewResizingMask.WidthSizable | NSViewResizingMask.MinYMargin` propriedade esteja usando o **ou** operador para combinar duas `NSViewResizingMask` recursos. Isso tornará o rótulo fique no mesmo local da parte superior da janela quando a janela é redimensionada verticalmente e reduzir e crescem em largura como a janela é redimensionada horizontalmente.

Novamente, o `ContentView.AddSubview (ClickMeLabel)` método adiciona o `NSTextField` para a exibição de conteúdo para que ela será exibida na tela quando o aplicativo é executado e a janela aberta.


### <a name="adjusting-the-window-controller"></a>Ajustando o controlador da janela

Desde o design do `MainWindow` não está sendo carregado de um arquivo de storyboard ou. XIB, precisaremos fazer alguns ajustes para o controlador da janela. Editar o **MainWindowController.cs** de arquivo e torná-lo semelhante ao seguinte:

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

Permitir que abordam os principais elementos dessa modificação.

Primeiro, definimos uma nova instância dos `MainWindow` de classe e atribuí-lo para o controlador de janela base `Window` propriedade:

```csharp
CGRect contentRect = new CGRect (0, 0, 1000, 500);
base.Window = new MainWindow(contentRect, (NSWindowStyle.Titled | NSWindowStyle.Closable | NSWindowStyle.Miniaturizable | NSWindowStyle.Resizable), NSBackingStore.Buffered, false);
```

Podemos definir o local da janela de tela com um `CGRect`. Assim como o sistema de coordenadas da janela, a tela define (0,0) como o canto inferior esquerdo. Em seguida, definimos o estilo da janela usando o **ou** operador para combinar duas ou mais `NSWindowStyle` recursos:

```csharp
... (NSWindowStyle.Titled | NSWindowStyle.Closable | NSWindowStyle.Miniaturizable | NSWindowStyle.Resizable) ...
``` 

O seguinte `NSWindowStyle` recursos estão disponíveis:

- **Sem borda** -a janela não terá nenhuma borda.
- **Intitulado** -a janela terá uma barra de título.
- **Closable** -a janela tem um botão de fechamento e pode ser fechada.
- **Miniaturizable** -a janela tem um botão Miniaturize e pode ser minimizada.
- **Redimensionável** -janela terá um botão de redimensionar e ser redimensionável.
- **Utilitário** -a janela é uma janela de estilo do utilitário (painel).
- **DocModal** -se a janela é um painel, eles poderão ser Modal de documento, em vez de sistema Modal. 
- **NonactivatingPanel** -se a janela é um painel, ele não será feito a janela principal.
- **TexturedBackground** -a janela terá um plano de fundo texturizado.
- **Sem escala** -a janela não será redimensionada.
- **UnifiedTitleAndToolbar** -áreas de título e a barra de ferramentas da janela serão associados.
- **HUD** -a janela será exibida como um painel de exibição Heads-Up.
- **FullScreenWindow** -a janela pode entrar no modo de tela inteira.
- **FullSizeContentView** -exibição de conteúdo da janela está por trás do título e a área da barra de ferramentas.

As duas últimas propriedades definem os _tipo de buffer_ para a janela e, se o desenho da janela será adiado. Para obter mais informações sobre `NSWindows`, consulte da Apple [Introdução ao Windows](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/WinPanel/Introduction.html#//apple_ref/doc/uid/10000031-SW1) documentação.

Por fim, como a janela não estiver sendo inflada de um arquivo de storyboard ou. XIB, precisamos simular isso em nossa **MainWindowController.cs** chamando o windows `AwakeFromNib` método:

```csharp
Window.AwakeFromNib ();
```

Isso permitirá que você codificar com base no janela apenas como uma janela padrão carregada de um arquivo de storyboard ou. XIB.


### <a name="displaying-the-window"></a>Exibição da janela

Com o arquivo de storyboard ou. XIB removido e o **MainWindow.cs** e **MainWindowController.cs** arquivos modificados, você estará usando a janela como faria qualquer janela normal que haviam sido criada no Interface Builder do Xcode com um arquivo. XIB.

A seguir criará uma nova instância da janela e seu controlador e exibir a janela na tela:

```csharp
private MainWindowController mainWindowController;
...

mainWindowController = new MainWindowController ();
mainWindowController.Window.MakeKeyAndOrderFront (this);
```

Neste ponto, se o aplicativo é executado e o botão clicado algumas vezes, a seguinte mensagem será exibida:

![Um execução do aplicativo de exemplo](xibless-ui-images/run01.png "um execução do aplicativo de exemplo")


## <a name="adding-a-code-only-window"></a>Adição de uma janela de código única

Se quisermos adicionar um código apenas, a janela de xibless a um aplicativo existente do xamarin. Mac, clique com botão direito no projeto na **painel de soluções** e selecione **Add** > **novo arquivo...** . No **novo arquivo** caixa de diálogo Escolher **xamarin. Mac** > **janela do Cocoa com controlador**, conforme ilustrado abaixo:

![Adicionando um novo controlador de janela](xibless-ui-images/add01.png "adicionando um novo controlador de janela") 

Assim como antes, iremos excluir o arquivo de storyboard ou. XIB padrão do projeto (nesse caso **SecondWindow.xib**) e siga as etapas a [alternar uma janela para usar código](#Switching_a_Window_to_use_Code) seção acima para cobrir o definição da janela de código.


## <a name="adding-a-ui-element-to-a-window-in-code"></a>Adicionando um elemento de interface do usuário a uma janela de código

Se uma janela foi criada em código ou carregada de um arquivo de storyboard ou. XIB, pode haver ocasiões em que queremos adicionar um elemento de interface do usuário para uma janela do código. Por exemplo:

```csharp
var ClickMeButton = new NSButton (new CGRect (10, 10, 100, 30)){
    AutoresizingMask = NSViewResizingMask.MinYMargin
};
MyWindow.ContentView.AddSubview (ClickMeButton);
```

O código acima cria um novo `NSButton` e adiciona-o para o `MyWindow` instância para exibição da janela. Basicamente, qualquer elemento de interface do usuário que pode ser definido no Interface Builder do Xcode, em um arquivo de storyboard ou. XIB pode ser criado no código e exibido em uma janela.


## <a name="defining-the-menu-bar-in-code"></a>Definir a barra de menus no código

Devido às limitações atuais do xamarin. Mac, ele não é sugerido que você crie barras de Menu – do seu aplicativo xamarin. Mac`NSMenuBar`– no código, mas continuar a usar o **Main. Storyboard** ou **MainMenu.xib** arquivo para defini-lo. Dito isso, você pode adicionar e remover Menus e itens de Menu no C# código.

Por exemplo, edite o **AppDelegate.cs** do arquivo e verifique o `DidFinishLaunching` método são semelhantes ao seguinte:

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

As opções acima cria um menu da barra de Status do código e o exibe quando o aplicativo é iniciado. Para obter mais informações sobre como trabalhar com Menus, consulte nosso [Menus](~/mac/user-interface/menu.md) documentação.


## <a name="summary"></a>Resumo

Este artigo apresentou uma visão detalhada de criação de interface do usuário de um aplicativo xamarin. Mac no C# código em vez de usar o Interface Builder do Xcode com arquivos. XIB ou de storyboard.



## <a name="related-links"></a>Links relacionados

- [MacXibless (amostra)](https://developer.xamarin.com/samples/mac/MacXibless/)
- [Windows](~/mac/user-interface/window.md)
- [Menus](~/mac/user-interface/menu.md)
- [Diretrizes de Interface Humana do macOS](https://developer.apple.com/macos/human-interface-guidelines/overview/themes/)
- [Introdução ao Windows](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/WinPanel/Introduction.html)
