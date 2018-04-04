---
title: design de interface do usuário.Storyboard/.XIB-less
description: Este artigo aborda a criação de interface do usuário do aplicativo um Xamarin.Mac diretamente a partir de código c#, sem .storyboard arquivos, arquivos .xib ou Construtor de Interface.
ms.prod: xamarin
ms.assetid: 02310F58-DCF1-4589-9F4A-065DF64FC0E1
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/14/2017
ms.openlocfilehash: 66725b02d3e351e74fa79ae5336a7db3a9f2b534
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="storyboardxib-less-user-interface-design"></a>design de interface do usuário.Storyboard/.XIB-less

_Este artigo aborda a criação de interface do usuário do aplicativo um Xamarin.Mac diretamente a partir de código c#, sem .storyboard arquivos, arquivos .xib ou Construtor de Interface._


## <a name="overview"></a>Visão geral

Ao trabalhar com c# e .NET em um aplicativo de Xamarin.Mac, você tem acesso para os mesmos elementos de interface de usuário e as ferramentas que um desenvolvedor trabalhando em *Objective-C* e *Xcode* does. Normalmente, ao criar um aplicativo de Xamarin.Mac, você usará Interface Builder do Xcode com arquivos .storyboard ou .xib para criar e manter a interface do usuário do aplicativo.

Você também tem a opção de criação de algumas ou todas de interface de usuário do seu aplicativo Xamarin.Mac diretamente no código do c#. Neste artigo, vamos abordar os fundamentos da criação de interfaces de usuário e elementos de interface do usuário em código c#.

[![O Visual Studio para o editor de códigos do Mac](xibless-ui-images/intro01.png "o Visual Studio para o editor de códigos do Mac")](xibless-ui-images/intro01-large.png#lightbox)

<a name="Switching_a_Window_to_use_Code" />

## <a name="switching-a-window-to-use-code"></a>Alternar uma janela para usar o código

Quando você cria um novo aplicativo de Xamarin.Mac Cocoa, obtém uma janela em branco, padrão por padrão. Esse windows é definido em um **Main.storyboard** (ou tradicionalmente um **MainWindow.xib**) incluído automaticamente no projeto do arquivo. Isso também inclui um **ViewController.cs** arquivo que gerencia o modo de exibição principal do aplicativo (ou tradicionalmente novamente um **MainWindow.cs** e um **MainWindowController.cs** arquivo).

Para alternar para uma janela de Xibless para um aplicativo, faça o seguinte:

1. Abra o aplicativo que você deseja parar de usar `.stroyboard` ou .xib arquivos para definir a interface do usuário no Visual Studio para Mac.
2. No **solução preenchimento**, com o botão direito no **Main.storyboard** ou **MainWindow.xib** de arquivo e selecione **remover**: 

    ![Removendo o storyboard principal ou a janela](xibless-ui-images/switch01.png "removendo o storyboard principal ou a janela")
3. Do **caixa de diálogo Remover**, clique no **excluir** botão para remover completamente o .storyboard ou .xib do projeto: 

    ![Confirmar a exclusão](xibless-ui-images/switch02.png "confirmar a exclusão")

Agora vamos precisar modificar a **MainWindow.cs** arquivo para definir o layout da janela e modificar o **ViewController.cs** ou **MainWindowController.cs** para criar um instância do nosso `MainWindow` classe desde que já não estiver usando o arquivo .storyboard ou .xib.

Aplicativos modernos de Xamarin.Mac que usam Storyboards para sua interface do usuário não pode incluir automaticamente o **MainWindow.cs**, **ViewController.cs** ou **MainWindowController.cs** arquivos. Conforme necessário, basta adicionar uma nova vazia c# classe ao projeto (**adicionar** > **novo arquivo...**   >  **Geral** > **classe vazia**) e nomeie-o mesmo que o arquivo está faltando. 


### <a name="defining-the-window-in-code"></a>Definir a janela de código

Em seguida, edite o **MainWindow.cs** de arquivo e torná-lo a aparência a seguir:

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

Vamos falar sobre alguns dos elementos principais.

Primeiro, adicionamos alguns _propriedades computadas_ que atuará como tomadas (como se a janela foi criada em um arquivo .storyboard ou .xib):

```csharp
public NSButton ClickMeButton { get; set;}
public NSTextField ClickMeLabel { get ; set;}
```

Esses dará nos acesso para os elementos de interface do usuário serão exibidos na janela. Desde que a janela não estiver sendo inflada de um arquivo .storyboard ou .xib, precisamos de uma maneira de instanciá-la (como você verá posteriormente no `MainWindowController` classe). É o que faz esse novo método de construtor:

```csharp
public MainWindow(CGRect contentRect, NSWindowStyle aStyle, NSBackingStore bufferingType, bool deferCreation): base (contentRect, aStyle,bufferingType,deferCreation) {
    ...
}
```

Isso é ali que podemos criar o layout da janela e colocar os elementos da interface do usuário necessários para criar a interface de usuário necessário. Antes que possamos adicionar elementos da interface do usuário para uma janela, é necessário um _exibição de conteúdo_ para conter os elementos:

```csharp
ContentView = new NSView (Frame);
```

Isso cria uma exibição de conteúdo que preencherá a janela. Agora podemos adicionar nosso primeiro elemento de interface do usuário, um `NSButton`, para a janela:

```csharp
ClickMeButton = new NSButton (new CGRect (10, Frame.Height-70, 100, 30)){
    AutoresizingMask = NSViewResizingMask.MinYMargin
};
ContentView.AddSubview (ClickMeButton);
```

A primeira coisa a observar aqui é que, ao contrário do iOS, macOS usa notação matemática para definir seu sistema de coordenadas de janela. Portanto, o ponto de origem é no canto inferior esquerdo da janela, com valores de aumento direita e, no canto superior direito da janela. Quando criamos o novo `NSButton`, podemos levar isso em conta como definimos seu tamanho e posição na tela.

O `AutoresizingMask = NSViewResizingMask.MinYMargin` propriedade instrui o botão que queremos que ele permaneça no mesmo local da parte superior da janela quando a janela é redimensionada verticalmente. Novamente, isso é necessário porque (0,0) está no canto inferior esquerdo da janela.

Por fim, o `ContentView.AddSubview (ClickMeButton)` método adiciona o `NSButton` para a exibição de conteúdo para que ele será exibido na tela quando o aplicativo é executado e a janela exibida.

Em seguida um rótulo é adicionado para a janela que exibirá o número de vezes que o `NSButton` foi clicado: 

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

Como macOS não tem um determinado _rótulo_ elemento de interface do usuário, adicionamos um especialmente estilo não editável `NSTextField` para atuar como um rótulo. Como o botão antes, o tamanho e local leva em conta que (0,0) está no canto inferior esquerdo da janela. O `AutoresizingMask = NSViewResizingMask.WidthSizable | NSViewResizingMask.MinYMargin` é de propriedade usando o **ou** operador combinar duas `NSViewResizingMask` recursos. Isso tornará o rótulo permanecer no mesmo local da parte superior da janela quando a janela é redimensionada verticalmente, reduzir e crescem em largura, como a janela é redimensionada horizontalmente.

Novamente, o `ContentView.AddSubview (ClickMeLabel)` método adiciona o `NSTextField` para a exibição de conteúdo para que ele será exibido na tela quando o aplicativo é executado e a janela aberta.


### <a name="adjusting-the-window-controller"></a>Ajustando o controlador de janela

Desde o design do `MainWindow` não está sendo carregado de um arquivo .storyboard ou .xib, vamos precisar fazer alguns ajustes para o controlador de janela. Editar o **MainWindowController.cs** de arquivo e torná-lo a aparência a seguir:

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

Primeiro, definimos uma nova instância do `MainWindow` classe e atribuí-la para o controlador de janela base `Window` propriedade:

```csharp
CGRect contentRect = new CGRect (0, 0, 1000, 500);
base.Window = new MainWindow(contentRect, (NSWindowStyle.Titled | NSWindowStyle.Closable | NSWindowStyle.Miniaturizable | NSWindowStyle.Resizable), NSBackingStore.Buffered, false);
```

Definimos o local da janela da tela com um `CGRect`. Assim como o sistema de coordenadas da janela, a tela define (0,0) como o canto inferior esquerdo. Em seguida, definimos o estilo da janela usando o **ou** operador combinar duas ou mais `NSWindowStyle` recursos:

```csharp
... (NSWindowStyle.Titled | NSWindowStyle.Closable | NSWindowStyle.Miniaturizable | NSWindowStyle.Resizable) ...
``` 

O seguinte `NSWindowStyle` recursos estão disponíveis:

- **Sem borda** -janela terá sem borda.
- **Intitulada** -janela terá uma barra de título.
- **Closable** -janela tem um botão Fechar e poderá ser fechada.
- **Miniaturizable** -janela tem um botão Miniaturize e pode ser minimizada.
- **Redimensionável** -janela terá um botão redimensionar e ser redimensionável.
- **Utilitário** -a janela é uma janela de estilo do utilitário (painel).
- **DocModal** -se a janela é um painel, ele será Modal do documento em vez de sistema Modal. 
- **NonactivatingPanel** -se a janela é um painel, ele não será feito a janela principal.
- **TexturedBackground** -janela terá um plano de fundo de textura.
- **Escala** -a janela não será dimensionada.
- **UnifiedTitleAndToolbar** -áreas de título e a barra de ferramentas da janela serão adicionados.
- **HUD** -janela será exibida como uma painel de exibição de direta.
- **FullScreenWindow** -janela pode entrar no modo de tela inteira.
- **FullSizeContentView** -exibição de conteúdo da janela está atrás do título e a área de barra de ferramentas.

As duas últimas propriedades definem o _buffer tipo_ para a janela e, se o desenho da janela será adiado. Para obter mais informações sobre `NSWindows`, consulte da Apple [Introdução ao Windows](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/WinPanel/Introduction.html#//apple_ref/doc/uid/10000031-SW1) documentação.

Por fim, como a janela não estiver sendo inflada de um arquivo .storyboard ou .xib, precisamos simular isso em nosso **MainWindowController.cs** chamando os windows `AwakeFromNib` método:

```csharp
Window.AwakeFromNib ();
```

Isso permitirá que você código na janela de apenas como uma janela padrão carregada de um arquivo .storyboard ou .xib.


### <a name="displaying-the-window"></a>Exibição da janela

Com o arquivo .storyboard ou .xib removida e o **MainWindow.cs** e **MainWindowController.cs** arquivos modificados, você usará a janela como faria com qualquer janela normal que tiver sido criada em Construtor da Interface do Xcode com um arquivo .xib.

O seguinte será criar uma nova instância da janela e o controlador e exibir a janela na tela:

```csharp
private MainWindowController mainWindowController;
...

mainWindowController = new MainWindowController ();
mainWindowController.Window.MakeKeyAndOrderFront (this);
```

Neste ponto, se o aplicativo é executado e o botão clicado duas vezes, a seguinte mensagem será exibida:

![Um aplicativo de exemplo executar](xibless-ui-images/run01.png "um aplicativo de exemplo executar")


## <a name="adding-a-code-only-window"></a>Adicionando uma janela única de código

Se queremos adicionar um somente código, a janela de xibless para um aplicativo Xamarin.Mac existente, clique com botão direito no projeto no **solução preenchimento** e selecione **adicionar** > **novo arquivo.** . No **novo arquivo** caixa de diálogo Escolher **Xamarin.Mac** > **Cocoa janela com controlador**, conforme ilustrado abaixo:

![Adicionar um novo controlador de janela](xibless-ui-images/add01.png "adicionando um novo controlador de janela") 

Assim como antes, iremos excluir o arquivo de .storyboard ou .xib padrão do projeto (nesse caso **SecondWindow.xib**) e siga as etapas a [alternar uma janela para usar o código](#Switching_a_Window_to_use_Code) seção acima para cobrir o definição da janela de código.


## <a name="adding-a-ui-element-to-a-window-in-code"></a>Adicionando um elemento de interface do usuário a uma janela de código

Se uma janela foi criada em código ou carregada de um arquivo .storyboard ou .xib, pode haver momentos em que queremos adicionar um elemento de interface do usuário para uma janela de código. Por exemplo:

```csharp
var ClickMeButton = new NSButton (new CGRect (10, 10, 100, 30)){
    AutoresizingMask = NSViewResizingMask.MinYMargin
};
MyWindow.ContentView.AddSubview (ClickMeButton);
```

O código acima cria um novo `NSButton` e adiciona-o para a `MyWindow` instância da janela de exibição. Basicamente qualquer elemento de interface do usuário que pode ser definido no construtor de Interface do Xcode em um arquivo .storyboard ou .xib pode ser criado em código e exibido em uma janela.


## <a name="defining-the-menu-bar-in-code"></a>Definindo a barra de menus no código

Devido a limitações atuais Xamarin.Mac, não recomenda-se que você crie a barra de Menu – do aplicativo Xamarin.Mac`NSMenuBar`– no código, mas continuar a usar o **Main.storyboard** ou **MainMenu.xib** arquivo para defini-lo. Dito isso, você pode adicionar e remover itens de Menu e Menus no código c#.

Por exemplo, edite o **appdelegate. CS** de arquivo e verifique o `DidFinishLaunching` método aparência semelhante ao seguinte:

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

Este artigo obteve uma visão detalhada da criação de interface do usuário do aplicativo um Xamarin.Mac no código c# em vez de usar o construtor de Interface do Xcode com .storyboard ou .xib arquivos.



## <a name="related-links"></a>Links relacionados

- [MacXibless (exemplo)](https://developer.xamarin.com/samples/mac/MacXibless/)
- [Windows](~/mac/user-interface/window.md)
- [Menus](~/mac/user-interface/menu.md)
- [macOS diretrizes de Interface Humana](https://developer.apple.com/macos/human-interface-guidelines/overview/themes/)
- [Introdução ao Windows](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/WinPanel/Introduction.html)
