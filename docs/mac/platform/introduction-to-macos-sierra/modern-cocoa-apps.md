---
title: Criando macOS modernos aplicativos
description: "Este artigo aborda várias dicas, recursos e técnicas de que um desenvolvedor pode usar para compilar um aplicativo moderno macOS em Xamarin.Mac."
ms.topic: article
ms.prod: xamarin
ms.assetid: F20EE590-246E-40EB-B309-D9D8C090C7F1
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/14/2017
ms.openlocfilehash: 446db5c04849ac6fa320f3fe3b7e22b3d10bf9cf
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2018
---
# <a name="building-modern-macos-apps"></a>Criando macOS modernos aplicativos

_Este artigo aborda várias dicas, recursos e técnicas de que um desenvolvedor pode usar para compilar um aplicativo moderno macOS em Xamarin.Mac._

<a name="Building-Modern-Looks-with-Modern-Views" />

## <a name="building-modern-looks-with-modern-views"></a>Criação de aparência moderna com exibições modernas

Uma aparência moderna incluirá uma aparência moderna de janela e barra de ferramentas, como o aplicativo de exemplo mostrado abaixo:

[![](modern-cocoa-apps-images/content08.png "Um exemplo de um aplicativo do Mac moderno interface do usuário")](modern-cocoa-apps-images/content08.png#lightbox)

<a name="Enabling-Full-Sized-Content-Views" />

### <a name="enabling-full-sized-content-views"></a>Habilitar completo dimensionado modos de exibição de conteúdo

Para obter esse parece em um aplicativo de Xamarin.Mac, o desenvolvedor deve usar um _modo de exibição de conteúdo do tamanho total_, o que significa que o conteúdo se estende em áreas de ferramenta e a barra de título e será automaticamente ser manchadas por macOS.

Para habilitar esse recurso no código, crie uma classe personalizada para o `NSWindowController` e torná-lo a aparência a seguir:

```csharp
using System;
using Foundation;
using AppKit;

namespace MacModern
{
    public partial class MainWindowController : NSWindowController
    {
        #region Constructor
        public MainWindowController (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void WindowDidLoad ()
        {
            base.WindowDidLoad ();

            // Set window to use Full Size Content View
            Window.StyleMask = NSWindowStyle.FullSizeContentView;
        }
        #endregion
    }
}
```

Esse recurso também pode ser habilitado no construtor de Interface do Xcode selecionando a janela e verificando **exibição de conteúdo em tamanho completo**:

[![](modern-cocoa-apps-images/content01.png "Editando o storyboard principal no construtor de Interface do Xcode")](modern-cocoa-apps-images/content01.png#lightbox)

Ao usar um modo de exibição de conteúdo de tamanho total, o desenvolvedor pode precisar deslocar o conteúdo abaixo as áreas de barra de título e a ferramenta para que o conteúdo específico (como rótulos) não slide sob eles.

Para complicar a esse problema, as áreas de título e a barra de ferramentas podem ter uma altura dinâmica com base na ação que o usuário está executando no momento, a versão do macOS o usuário instalou e/ou o hardware de Mac que o aplicativo está em execução.

Como resultado, simplesmente hard-coding o deslocamento ao dispor a Interface do usuário não funcionarão. O desenvolvedor precisa adotar uma abordagem dinâmica.

Apple incluiu a [chave-valor observável](~/mac/app-fundamentals/databinding.md#Observing_Value_Changes) `ContentLayoutRect` propriedade do `NSWindow` classe para obter a área de conteúdo atual no código. O desenvolvedor pode usar esse valor para posicionar os elementos necessários manualmente quando a área de conteúdo é alterado.

A melhor solução é usar Classes de tamanho e Layout automático para posicionar os elementos de interface do usuário no código ou Construtor de Interface.

Código como o exemplo a seguir pode ser usado para posicionar elementos de interface do usuário usando Classes de tamanho e AutoLayout no controlador de exibição do aplicativo:

```csharp
using System;
using AppKit;
using Foundation;

namespace MacModern
{
    public partial class ViewController : NSViewController
    {
        #region Computed Properties
        public NSLayoutConstraint topConstraint { get; set; }
        #endregion

        ...

        #region Override Methods
        public override void UpdateViewConstraints ()
        {
            // Has the constraint already been set?
            if (topConstraint == null) {
                // Get the top anchor point
                var contentLayoutGuide = ItemTitle.Window?.ContentLayoutGuide as NSLayoutGuide;
                var topAnchor = contentLayoutGuide.TopAnchor;

                // Found?
                if (topAnchor != null) {
                    // Assemble constraint and activate it
                    topConstraint = topAnchor.ConstraintEqualToAnchor (topAnchor, 20);
                    topConstraint.Active = true;
                }
            }

            base.UpdateViewConstraints ();
        }
        #endregion
    }
}
```

Esse código cria o armazenamento para uma restrição superior que será aplicada a um rótulo (`ItemTitle`) para garantir que ele não adiada na área de título e a barra de ferramentas:

```csharp
public NSLayoutConstraint topConstraint { get; set; }
```

Com a substituição do controlador de exibição `UpdateViewConstraints` método, o desenvolvedor pode testar para ver se a restrição necessária já foi criada e criá-lo se necessário.

Se uma nova restrição precisa ser criado, o `ContentLayoutGuide` propriedade da janela do controle que precisa ser restrita é acessado e converter em um `NSLayoutGuide`:

```csharp
var contentLayoutGuide = ItemTitle.Window?.ContentLayoutGuide as NSLayoutGuide;
```

A propriedade TopAnchor o `NSLayoutGuide` é acessado e se ele estiver disponível, ele é usado para criar uma nova restrição com a quantidade de deslocamento desejada e a nova restrição fica ativa para aplicá-la:

```csharp
// Assemble constraint and activate it
topConstraint = topAnchor.ConstraintEqualToAnchor (topAnchor, 20);
topConstraint.Active = true;
```

<a name="Enabling-Streamlined-Toolbars" />

### <a name="enabling-streamlined-toolbars"></a>Habilitar barras de ferramentas simplificadas

Uma janela de macOS normal inclui um padrão da que barra de título na é exibida para a borda superior da janela. Se a janela também inclui uma barra de ferramentas, ele será exibido sob essa área de barra de título:

[![](modern-cocoa-apps-images/content02.png "Uma barra de ferramentas padrão do Mac")](modern-cocoa-apps-images/content02.png#lightbox)

Quando usar uma barra de ferramentas simplificado, desaparece da área de título e a barra de ferramentas se move para a posição da barra de título, na linha com os botões de janela fechar, minimizar e maximizar:

[![](modern-cocoa-apps-images/content03.png "Uma barra de ferramentas simplificado do Mac")](modern-cocoa-apps-images/content03.png#lightbox)

A barra de ferramentas simplificado é habilitada por meio da substituição de `ViewWillAppear` método do `NSViewController` e tornando-a aparência, como o seguinte:

```csharp
public override void ViewWillAppear ()
{
    base.ViewWillAppear ();

    // Enable streamlined Toolbars
    View.Window.TitleVisibility = NSWindowTitleVisibility.Hidden;
}
```

Esse efeito é normalmente usado para _caixa de sapatos aplicativos_ (uma janela aplicativos), como mapas, calendário, observações e preferências do sistema. 

<a name="Using-Accessory-View-Controllers" />

### <a name="using-accessory-view-controllers"></a>Usando controladores de exibição de acessório

Dependendo do design do aplicativo, o desenvolvedor também poderá complementar a barra de título área com um controlador de exibição de acessório que aparece à direita, abaixo da área de barra de título/ferramenta para fornecer sensíveis ao contexto controles para o usuário com base na atividade que eles são atualmente envolvido em:

[![](modern-cocoa-apps-images/content04.png "Um exemplo de acessório View Controller")](modern-cocoa-apps-images/content04.png#lightbox)

O controlador de exibição de acessório será automaticamente manchado e redimensionado pelo sistema sem intervenção do desenvolvedor.

Para adicionar um controlador de exibição de Acessórios, faça o seguinte:

1. No **Gerenciador de Soluções**, clique duas vezes no arquivo `Main.storyboard` para abri-lo para edição.
2. Arraste um **Custom View Controller** na hierarquia da janela: 

    [![](modern-cocoa-apps-images/content05.png "Adicionar um novo controlador de exibição personalizado")](modern-cocoa-apps-images/content05.png#lightbox)
3. Layout do modo de exibição de acessório interface do usuário: 

    [![](modern-cocoa-apps-images/content06.png "Criando a nova exibição")](modern-cocoa-apps-images/content06.png#lightbox)
4. Expor o modo de exibição de acessório como um **tomada** e qualquer outro **ações** ou **tomadas** para sua interface do usuário: 

    [![](modern-cocoa-apps-images/content07.png "Adicionando a tomada necessária")](modern-cocoa-apps-images/content07.png#lightbox)
5. Salve as alterações.
6. Retorne ao Visual Studio para Mac sincronizar as alterações.

Editar o `NSWindowController` e torná-lo a aparência a seguir:

```csharp
using System;
using Foundation;
using AppKit;

namespace MacModern
{
    public partial class MainWindowController : NSWindowController
    {
        #region Constructor
        public MainWindowController (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void WindowDidLoad ()
        {
            base.WindowDidLoad ();

            // Create a title bar accessory view controller and attach
            // the view created in Interface Builder
            var accessoryView = new NSTitlebarAccessoryViewController ();
            accessoryView.View = AccessoryViewGoBar;

            // Set the location and attach the accessory view to the
            // titlebar to be displayed
            accessoryView.LayoutAttribute = NSLayoutAttribute.Bottom;
            Window.AddTitlebarAccessoryViewController (accessoryView);

        }
        #endregion
    }
}
```

Os principais pontos do código são em que a exibição está definida para o modo de exibição personalizado que foi definido no construtor de Interface e exposto como um **tomada**:

```csharp
accessoryView.View = AccessoryViewGoBar;
```

E o `LayoutAttribute` que define _onde_ o acessório será exibido:

```csharp
accessoryView.LayoutAttribute = NSLayoutAttribute.Bottom;
```

Como macOS agora é totalmente localizados, o `Left` e `Right` `NSLayoutAttribute` propriedades foram substituídas e deve ser substituídas pelo `Leading` e `Trailing`.

<a name="Using-Tabbed-Windows" />

### <a name="using-tabbed-windows"></a>Usando janelas com guias

Além disso, o sistema macOS pode adicionar controladores de exibição de Acessórios para a janela do aplicativo. Por exemplo, para criar janelas com guias, onde várias do Windows do aplicativo são mescladas em uma janela virtual:

[![](modern-cocoa-apps-images/content08.png "Um exemplo de uma janela com guias do Mac")](modern-cocoa-apps-images/content08.png#lightbox)

Normalmente, o desenvolvedor precisa fazer uso limitado de ação com guias Windows em seus aplicativos Xamarin.Mac, o sistema-los cuidará automaticamente da seguinte maneira:

- Windows será automaticamente com guias quando o `OrderFront` método é invocado.
- Windows será automaticamente Untabbed quando o `OrderOut` método é invocado.
- No código de todas as janelas com guias ainda são consideradas "visíveis", no entanto guias não mais à frente ficam ocultos no sistema usando CoreGraphics.
- Use o `TabbingIdentifier` propriedade `NSWindow` grupo Windows juntos nas guias.
- Se for um `NSDocument` baseado em aplicativo, vários deles será habilitado automaticamente (por exemplo, no botão de adição que está sendo adicionado à barra de guias) sem qualquer ação de desenvolvedor.
- Não`NSDocument` aplicativos com base podem habilitar o botão "mais" no grupo de guia para adicionar um novo documento, substituindo o `GetNewWindowForTab` método o `NSWindowsController`.

Reunir todas as partes, o `AppDelegate` de um aplicativo que deseja usar o Windows com guias do sistema com base pode parecer com o seguinte:

```csharp
using AppKit;
using Foundation;

namespace MacModern
{
    [Register ("AppDelegate")]
    public class AppDelegate : NSApplicationDelegate
    {
        #region Computed Properties
        public int NewDocumentNumber { get; set; } = 0;
        #endregion

        #region Constructors
        public AppDelegate ()
        {
        }
        #endregion

        #region Override Methods
        public override void DidFinishLaunching (NSNotification notification)
        {
            // Insert code here to initialize your application
        }

        public override void WillTerminate (NSNotification notification)
        {
            // Insert code here to tear down your application
        }
        #endregion

        #region Custom Actions
        [Export ("newDocument:")]
        public void NewDocument (NSObject sender)
        {
            // Get new window
            var storyboard = NSStoryboard.FromName ("Main", null);
            var controller = storyboard.InstantiateControllerWithIdentifier ("MainWindow") as NSWindowController;

            // Display
            controller.ShowWindow (this);
        } 
        #endregion
    }
}
```

Onde o `NewDocumentNumber` propriedade mantém controle sobre o número de novos documentos criados e o `NewDocument` método cria um novo documento e o exibe.

O `NSWindowController` seria semelhante ao seguinte:

```csharp
using System;
using Foundation;
using AppKit;

namespace MacModern
{
    public partial class MainWindowController : NSWindowController
    {
        #region Application Access
        /// <summary>
        /// A helper shortcut to the app delegate.
        /// </summary>
        /// <value>The app.</value>
        public static AppDelegate App {
            get { return (AppDelegate)NSApplication.SharedApplication.Delegate; }
        }
        #endregion

        #region Constructor
        public MainWindowController (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Public Methods
        public void SetDefaultDocumentTitle ()
        {
            // Is this the first document?
            if (App.NewDocumentNumber == 0) {
                // Yes, set title and increment
                Window.Title = "Untitled";
                ++App.NewDocumentNumber;
            } else {
                // No, show title and count
                Window.Title = $"Untitled {App.NewDocumentNumber++}";
            }
        }
        #endregion

        #region Override Methods
        public override void WindowDidLoad ()
        {
            base.WindowDidLoad ();

            // Prefer Tabbed Windows
            Window.TabbingMode = NSWindowTabbingMode.Preferred;
            Window.TabbingIdentifier = "Main";

            // Set default window title
            SetDefaultDocumentTitle ();

            // Set window to use Full Size Content View
            // Window.StyleMask = NSWindowStyle.FullSizeContentView;

            // Create a title bar accessory view controller and attach
            // the view created in Interface Builder
            var accessoryView = new NSTitlebarAccessoryViewController ();
            accessoryView.View = AccessoryViewGoBar;

            // Set the location and attach the accessory view to the
            // titlebar to be displayed
            accessoryView.LayoutAttribute = NSLayoutAttribute.Bottom;
            Window.AddTitlebarAccessoryViewController (accessoryView);

        }

        public override void GetNewWindowForTab (NSObject sender)
        {
            // Ask app to open a new document window
            App.NewDocument (this);
        }
        #endregion
    }
}
```

Onde estático `App` propriedade fornece um atalho para obter o `AppDelegate`. O `SetDefaultDocumentTitle` método define um novo título de documentos com base no número de novos documentos criados.

O código a seguir, informa macOS que o aplicativo prefere usar guias e fornece uma cadeia de caracteres que permite que Windows do aplicativo a ser agrupada em guias:

```csharp
// Prefer Tabbed Windows
Window.TabbingMode = NSWindowTabbingMode.Preferred;
Window.TabbingIdentifier = "Main";
```

E o método de substituição a seguir adiciona um botão de adição para a barra de guias que criará um novo documento quando clicado pelo usuário:

```csharp
public override void GetNewWindowForTab (NSObject sender)
{
    // Ask app to open a new document window
    App.NewDocument (this);
}
```

<a name="Using-Core-Animation" />

### <a name="using-core-animation"></a>Usando animação de núcleo

Animação de núcleo é um mecanismo de processamento de gráficos alimentado alto macOS interno. Animação de núcleos foi otimizada para tirar proveito da GPU (unidade de processamento gráfico) disponível no hardware macOS modernos em vez de executar as operações de gráficos na CPU, o que pode causar lentidão na máquina.

O `CALayer`, fornecido pela animação de núcleo, podem ser usados para tarefas como rolagem rápido e fluido e animações. Interface do usuário do aplicativo deve ser composto de várias camadas para aproveitar ao máximo de animação de núcleo e sub-visualizações.

Um `CALayer` objeto fornece várias propriedades que permitem que o desenvolvedor controlar o que é apresentado na tela para o usuário, como:

- `Content` -Pode ser um `NSImage` ou `CGImage` que fornece o conteúdo da camada.
- `BackgroundColor` -Define a cor de plano de fundo da camada como uma `CGColor`
- `BorderWidth` -Define a largura da borda.
- `BorderColor` -Define a cor da borda.

Para utilizar os principais gráficos na interface de usuário do aplicativo, ele deve estar usando _camada feito_ exibições que Apple sugere que o desenvolvedor deve sempre ativar na exibição de conteúdo da janela. Dessa forma, todas as exibições filho herdarão automaticamente camada fazendo também.

Além disso, a Apple sugere usando modos de exibição de camada feito em vez de adicionar um novo `CALayer` como uma subcamada porque o sistema manipula automaticamente várias das configurações necessárias (como aqueles necessários para uma exibição de Retina).

Camada fazendo pode ser habilitada definindo o `WantsLayer` de um `NSView` para `true` ou dentro Interface Builder do Xcode sob o **Inspetor de efeitos de exibição** verificando **camada principal de animação**:

[![](modern-cocoa-apps-images/content09.png "O Inspetor de efeitos de exibição")](modern-cocoa-apps-images/content09.png#lightbox)

<a name="Redrawing-Views-with-Layers" />

#### <a name="redrawing-views-with-layers"></a>Redesenhar exibições com camadas

Outra etapa importante ao usar camada feito exibições em um aplicativo de Xamarin.Mac é definir o `LayerContentsRedrawPolicy` do `NSView` para `OnSetNeedsDisplay` no `NSViewController`. Por exemplo:

```csharp
public override void ViewWillAppear ()
{
    base.ViewWillAppear ();

    // Set the content redraw policy
    View.LayerContentsRedrawPolicy = NSViewLayerContentsRedrawPolicy.OnSetNeedsDisplay;
}
```

Se o desenvolvedor não definir essa propriedade, a exibição será redesenhada sempre que a origem do quadro é alterado, que não for desejado por motivos de desempenho. Com essa propriedade definida como `OnSetNeedsDisplay` o desenvolvedor manualmente terá que definir `NeedsDisplay` para `true` para forçar o conteúdo para redesenhar, no entanto.

Quando uma exibição é marcada como suja, o sistema verifica o `WantsUpdateLayer` propriedade da exibição. Se ele retorna `true` o `UpdateLayer` método é chamado, caso contrário o `DrawRect` método do modo de exibição é chamado para atualizar conteúdo a exibição.

Apple tem as seguintes sugestões para atualizar um conteúdo de modos de exibição quando necessário:

- Apple prefere usando `UpdateLater` sobre `DrawRect` sempre que possível, pois ele fornece um aumento significativo no desempenho.
- Use a mesma `layer.Contents` para elementos de interface do usuário que são semelhantes.
- Apple prefere também o desenvolvedor para compor sua interface do usuário usando modos de exibição padrão, como `NSTextField`, novamente sempre que possível.

Para usar `UpdateLayer`, crie uma classe personalizada para o `NSView` e fazer com que o código a seguinte aparência:

```csharp
using System;
using Foundation;
using AppKit;

namespace MacModern
{
    public partial class MainView : NSView
    {
        #region Computed Properties
        public override bool WantsLayer {
            get { return true; }
        }

        public override bool WantsUpdateLayer {
            get { return true; }
        }
        #endregion

        #region Constructor
        public MainView (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void DrawRect (CoreGraphics.CGRect dirtyRect)
        {
            base.DrawRect (dirtyRect);

        }

        public override void UpdateLayer ()
        {
            base.UpdateLayer ();

            // Draw view 
            Layer.BackgroundColor = NSColor.Red.CGColor;
        }
        #endregion
    }
}
```

<a name="Using-Modern-Drag-and-Drop" />

## <a name="using-modern-drag-and-drop"></a>Usando moderno arrastar e soltar

Para apresentar uma experiência moderna de arrastar e soltar para o usuário, o desenvolvedor deve adotar _arrastar Flocking_ em seu aplicativo operações arrastar e soltar. Arraste Flocking é onde cada arquivo individual ou um item que está sendo arrastado inicialmente aparece como um elemento individual que flocks (grupo sob o cursor com uma contagem do número de itens) como o usuário continua a operação de arrastar.

Se o usuário encerra a operação de arrastar, os elementos individuais serão Unflock e retornar para seus locais originais.

O código de exemplo a seguir habilita arraste Flocking em uma exibição personalizada:

```csharp
using System;
using System.Collections.Generic;
using Foundation;
using AppKit;

namespace MacModern
{
    public partial class MainView : NSView, INSDraggingSource, INSDraggingDestination
    {
        #region Constructor
        public MainView (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void MouseDragged (NSEvent theEvent)
        {
            // Create group of string to be dragged
            var string1 = new NSDraggingItem ((NSString)"Item 1");
            var string2 = new NSDraggingItem ((NSString)"Item 2");
            var string3 = new NSDraggingItem ((NSString)"Item 3");

            // Drag a cluster of items
            BeginDraggingSession (new [] { string1, string2, string3 }, theEvent, this);
        }
        #endregion
    }
}
```

O efeito flocking foi obtido por meio do envio de cada item que está sendo arrastado para o `BeginDraggingSession` método o `NSView` como um elemento separado em uma matriz.

Ao trabalhar com um `NSTableView` ou `NSOutlineView`, use o `PastboardWriterForRow` método o `NSTableViewDataSource` classe para iniciar a operação de arrastar:

```csharp
using System;
using System.Collections.Generic;
using Foundation;
using AppKit;

namespace MacModern
{
    public class ContentsTableDataSource: NSTableViewDataSource
    {
        #region Constructors
        public ContentsTableDataSource ()
        {
        }
        #endregion

        #region Override Methods
        public override INSPasteboardWriting GetPasteboardWriterForRow (NSTableView tableView, nint row)
        {
            // Return required pasteboard writer
            ...
            
            // Pasteboard writer failed
            return null;
        }
        #endregion
    }
}
```

Isso permite que o desenvolvedor fornecer um indivíduo `NSDraggingItem` para cada item na tabela que está sendo arrastada em vez do método mais antigo `WriteRowsWith` que gravar todas as linhas como um único grupo para a área de trabalho.

Ao trabalhar com `NSCollectionViews`, usar novamente o `PasteboardWriterForItemAt` método em vez do `WriteItemsAt` método ao arrastar começa.

O desenvolvedor deve sempre evitar colocar arquivos grandes na área de trabalho. Novo para macOS serra, _arquivo promessas_ permitem que o desenvolvedor coloque referências aos dado arquivos na área de trabalho que posteriormente será atendida quando o usuário termina a operação de soltar usando o novo `NSFilePromiseProvider` e `NSFilePromiseReceiver` classes.

<a name="Using-Modern-Event-Tracking" />

## <a name="using-modern-event-tracking"></a>Usando o rastreamento de evento modernos

Para um elemento de Interface do usuário (como uma `NSButton`) que foi adicionado a uma área de título ou a barra de ferramentas, o usuário deve ser capaz de clique no elemento e disparar um evento como normais (como exibir uma janela pop-up). No entanto, desde que o item também está na área de título ou a barra de ferramentas, o usuário deve ser capaz de clique e arraste o elemento para mover a janela também.

Para fazer isso no código, crie uma classe personalizada para o elemento (como `NSButton`) e substituir o `MouseDown` evento da seguinte maneira:

```csharp
public override void MouseDown (NSEvent theEvent)
{
    var shouldCallSuper = false;

    Window.TrackEventsMatching (NSEventMask.LeftMouseUp, 2000, NSRunLoop.NSRunLoopEventTracking, (NSEvent evt, ref bool stop) => {
        // Handle event as normal
        stop = true;
        shouldCallSuper = true;
    });

    Window.TrackEventsMatching(NSEventMask.LeftMouseDragged, 2000, NSRunLoop.NSRunLoopEventTracking, (NSEvent evt, ref bool stop) => {
        // Pass drag event to window
        stop = true;
        Window.PerformWindowDrag (evt);
    });

    // Call super to handle mousedown
    if (shouldCallSuper) {
        base.MouseDown (theEvent);
    }
}
```

Esse código usa o `TrackEventsMatching` método o `NSWindow` o elemento de interface do usuário conectado para interceptar o `LeftMouseUp` e `LeftMouseDragged` eventos. Para um `LeftMouseUp` evento, o elemento de interface do usuário responda como normal. Para o `LeftMouseDragged` evento, o evento é passado para o `NSWindow`do `PerformWindowDrag` método para mover a janela na tela.

Chamando o `PerformWindowDrag` método o `NSWindow` classe fornece os seguintes benefícios:

- Permite a janela se mova, mesmo se o aplicativo está suspenso (por exemplo, quando um loop profundo de processamento).
- Espaço de troca funcione conforme o esperado.
- A barra de espaços será exibida como normal.
- Janela de encaixe e alinhamento funcionam normalmente.

<a name="Using-Modern-Container-View-Controls" />

## <a name="using-modern-container-view-controls"></a>Usando controles de exibição de contêiner modernos

macOS Serra fornece muitas melhorias modernas para os controles de exibição de contêiner existentes disponíveis na versão anterior do sistema operacional.

<a name="Table View Enhancements" />

## <a name="table-view-enhancements"></a>Aprimoramentos do modo de tabela

O desenvolvedor deve sempre usar o novo `NSView` com base em versão de controles de exibição de contêiner, como `NSTableView`. Por exemplo:

```csharp
using System;
using System.Collections.Generic;
using Foundation;
using AppKit;

namespace MacModern
{
    public class ContentsTableDelegate : NSTableViewDelegate
    {
        #region Constructors
        public ContentsTableDelegate ()
        {
        }
        #endregion

        #region Override Methods
        public override NSView GetViewForItem (NSTableView tableView, NSTableColumn tableColumn, nint row)
        {
            // Build new view
            var view = new NSView ();
            ...

            // Return the view representing the item to display
            return view;
        }
        #endregion
    }
}
```

Isso permite ações personalizadas de linha de tabela a ser anexado à determinada linhas na tabela (por exemplo, passar o dedo da direita para excluir a linha). Para habilitar esse comportamento, substituir o `RowActions` método o `NSTableViewDelegate`:

```csharp
using System;
using System.Collections.Generic;
using Foundation;
using AppKit;

namespace MacModern
{
    public class ContentsTableDelegate : NSTableViewDelegate
    {
        #region Constructors
        public ContentsTableDelegate ()
        {
        }
        #endregion

        #region Override Methods
        public override NSView GetViewForItem (NSTableView tableView, NSTableColumn tableColumn, nint row)
        {
            // Build new view
            var view = new NSView ();
            ...

            // Return the view representing the item to display
            return view;
        }

        public override NSTableViewRowAction [] RowActions (NSTableView tableView, nint row, NSTableRowActionEdge edge)
        {
            // Take action based on the edge
            if (edge == NSTableRowActionEdge.Trailing) {
                // Create row actions
                var editAction = NSTableViewRowAction.FromStyle (NSTableViewRowActionStyle.Regular, "Edit", (action, rowNum) => {
                    // Handle row being edited
                    ...
                });

                var deleteAction = NSTableViewRowAction.FromStyle (NSTableViewRowActionStyle.Destructive, "Delete", (action, rowNum) => {
                    // Handle row being deleted
                    ...
                });

                // Return actions
                return new [] { editAction, deleteAction };
            } else {
                // No matching actions
                return null;
            }
        }
        #endregion
    }
}
```

Estático `NSTableViewRowAction.FromStyle` é usado para criar uma nova ação de linha de tabela dos seguintes estilos:

- `Regular` -Executa uma ação padrão não destrutiva como editar conteúdo da linha.
- `Destructive` -Executa uma ação destrutiva como excluir a linha da tabela. Essas ações serão renderizadas com um plano de fundo vermelho.

<a name="Scroll-View-Enhancements" />

## <a name="scroll-view-enhancements"></a>Aprimoramentos do modo de rolagem 

Ao usar um modo de exibição de rolagem (`NSScrollView`) diretamente, ou como parte de outro controle (como `NSTableView`), o conteúdo da exibição da rolagem pode deslizar sob os título e a barra de ferramentas áreas em um aplicativo de Xamarin.Mac usando uma aparência moderna e modos de exibição.

Como resultado, o primeiro item na área de conteúdo da exibição de rolagem pode ser parcialmente encoberto por área de título e a barra de ferramentas.

Para corrigir esse problema, Apple tenha adicionado duas novas propriedades para o `NSScrollView` classe:

- `ContentInsets` -Permite que o desenvolvedor fornecer uma `NSEdgeInsets` objeto que define o deslocamento que será aplicado à parte superior da exibição da rolagem.
- `AutomaticallyAdjustsContentInsets` -Se `true` manipula automaticamente a exibição da rolagem o `ContentInsets` do desenvolvedor.

Usando o `ContentInsets` o desenvolvedor pode ajustar o início da exibição da rolagem para permitir a inclusão de Acessórios, como:

- Um indicador de classificação, como mostrado no aplicativo de email.
- Um campo de pesquisa.
- Um botão de atualização ou atualização.

<a name="Auto-Layout-and-Localization-in-Modern-Apps" />

## <a name="auto-layout-and-localization-in-modern-apps"></a>Layout automático e a localização de aplicativos modernos

Apple incluiu várias tecnologias que permitem ao desenvolvedor criar facilmente um aplicativo macOS internacionalizados Xcode. Xcode agora permite que o desenvolvedor separar texto voltadas para o usuário de design de Interface do usuário do aplicativo em arquivos de Storyboard e fornece ferramentas para manter essa separação se altera a interface do usuário.

Para obter mais informações, consulte da Apple [internacionalização e localização guia](https://developer.apple.com/library/content/documentation/MacOSX/Conceptual/BPInternational/InternationalizingYourUserInterface/InternationalizingYourUserInterface.html).

<a name="Implementing-Base-Internationalization" />

### <a name="implementing-base-internationalization"></a>Implementação de Base de internacionalização 

Implementando a internacionalização de Base, o desenvolvedor pode fornecer um único arquivo de Storyboard para representar a interface de usuário do aplicativo e separar todas as cadeias de caracteres de voltado ao usuário. 

Quando o desenvolvedor está criando o arquivo de Storyboard inicial (ou arquivos) que definem a Interface do usuário do aplicativo, eles serão criados na internacionalização de Base (o idioma que o desenvolvedor fala).

Em seguida, o desenvolvedor pode exportar suas localizações e as cadeias de caracteres de internacionalização de Base (no design da interface do usuário do Storyboard) que podem ser convertidas em vários idiomas.

Posteriormente, essas localizações podem ser importadas e Xcode irá gerar os arquivos de cadeia de caracteres de idioma específico para o Storyboard.

<a name="Implementing-Auto-Layout-to-Support-Localization" />

### <a name="implementing-auto-layout-to-support-localization"></a>Implementando o Layout automático para oferecer suporte à localização

Porque as versões localizadas de cadeia de caracteres de valores podem ter tamanhos muito diferentes e/ou direção de leitura, o desenvolvedor deve usar Layout automático para posicionar e dimensionar a Interface do usuário do aplicativo em um arquivo de Storyboard.

Apple sugerir fazendo o seguinte:

- **Remover restrições de largura fixa** -todos os modos de exibição baseado em texto devem ter permissão para redimensionar com base em seu conteúdo. Largura fixa o modo de exibição pode cortar seu conteúdo em idiomas específicos.
- **Use intrínseco tamanhos de conteúdo** - por padrão com base em texto modos de exibição será dimensionar automaticamente para ajustar seu conteúdo. Para exibição com base em texto que não são de dimensionamento corretamente, selecioná-los no construtor de Interface do Xcode e escolha **editar** > **tamanho para ajustar conteúdo**.
- **Aplicar à esquerda e à direita atributos** - como a direção do texto pode ser alterado com base no idioma do usuário, use o novo `Leading` e `Trailing` atributos de restrição em vez de existente `Right` e `Left` atributos. `Leading` e `Trailing` ajustará automaticamente com base na direção de idiomas.
- **Exibições de PIN para exibições adjacentes** -Isso permite que os modos de exibição para reposicione e redimensione como alteram os modos de exibição ao redor na resposta para o idioma selecionado.
- **Não defina um mínimo de Windows e/ou os tamanhos máximos** -permitir janelas para alterar o tamanho do idioma selecionado redimensiona suas áreas de conteúdo.
- **Testar constantemente alterações de Layout** - durante o desenvolvimento de aplicativo deve ser testado constantemente em idiomas diferentes. Consulte da Apple [teste internacionalizados seu aplicativo](https://developer.apple.com/library/content/documentation/MacOSX/Conceptual/BPInternational/TestingYourInternationalApp/TestingYourInternationalApp.html#//apple_ref/doc/uid/10000171i-CH7-SW1) documentação para obter mais detalhes.
- **Use NSStackViews para fixar exibições juntas**  -  `NSStackViews` permite que o conteúdo e seu conteúdo para deslocar e crescimento de maneiras previsíveis alterar tamanho com base no idioma selecionado.

<a name="Localizing-in-Xcodes-Interface-Builder" />

### <a name="localizing-in-xcodes-interface-builder"></a>Localizando no construtor de Interface do Xcode

Apple fornece vários recursos no construtor de Interface do Xcode que o desenvolvedor pode usar durante a criação ou edição de interface de usuário do aplicativo para oferecer suporte à localização. O **a direção do texto** seção o **Inspetor de atributo** permite que o desenvolvedor fornecer dicas sobre como direção deve ser usada e atualizada em um modo de exibição baseado em texto, selecione (como `NSTextField`):

[![](modern-cocoa-apps-images/content10.png "As opções de direção do texto")](modern-cocoa-apps-images/content10.png#lightbox)

Há três valores possíveis para a **a direção do texto**:

- **Natural** -o layout é baseado na cadeia de caracteres atribuída ao controle.
- **Da esquerda para a direita** -o layout sempre é forçado para a esquerda para a direita.
- **Direita para a esquerda** -o layout sempre é forçado para a direita para esquerda.

Há dois valores possíveis para a **Layout**:

- **Da esquerda para a direita** -o layout sempre é da esquerda para a direita.
- **Direita para a esquerda** -o layout é sempre da direita para a esquerda.

Normalmente esses não devem ser alteradas a menos que um alinhamento específico é necessário.

O **espelho** propriedade informa ao sistema para inverter as propriedades de controle específicos (por exemplo, a posição da imagem de célula). Ele tem três valores possíveis:

- **Automaticamente** -a posição será alterado automaticamente com base na direção do idioma selecionado.
- **Na Interface de direita para esquerda** -a posição será alterada somente na direita para esquerda idiomas com base.
- **Nunca** -a posição nunca será alterado.

Se tiver especificado o desenvolvedor **Center**, **justificar** ou **completo** alinhamento do conteúdo de uma exibição com base em texto, eles nunca será invertidos com base no idioma selecionado.

Antes de macOS serra, os controles criados em código poderiam não ser espelhados automaticamente. O desenvolvedor tinha que usar código como o seguinte para lidar com o espelhamento:

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();

    // Setting a button's mirroring based on the layout direction
    var button = new NSButton ();
    if (button.UserInterfaceLayoutDirection == NSUserInterfaceLayoutDirection.LeftToRight) {
        button.Alignment = NSTextAlignment.Right;
        button.ImagePosition = NSCellImagePosition.ImageLeft;
    } else {
        button.Alignment = NSTextAlignment.Left;
        button.ImagePosition = NSCellImagePosition.ImageRight;
    }
}
```

Onde o `Alignment` e `ImagePosition` estão sendo definidos com base no `UserInterfaceLayoutDirection` do controle.

macOS Serra adiciona vários construtores de conveniência novo (via estático `CreateButton` método) que usam vários parâmetros (como título, imagem e ação) e automaticamente espelha corretamente. Por exemplo:

```csharp
var button2 = NSButton.CreateButton (myTitle, myImage, () => {
    // Take action when the button is pressed
    ...
});
```

<a name="Using-System-Appearances" />

## <a name="using-system-appearances"></a>Usando aparências de sistema

Aplicativos modernos macOS podem adotar uma nova aparência Interface escuro que funciona bem para aplicativos de criação, edição ou apresentação de imagem:

[![](modern-cocoa-apps-images/content11.png "Um exemplo de uma interface de usuário de janela Mac escuro")](modern-cocoa-apps-images/content11.png#lightbox)

Isso pode ser feito adicionando uma linha de código antes da janela é exibida. Por exemplo:

```csharp
using System;
using AppKit;
using Foundation;

namespace MacModern
{
    public partial class ViewController : NSViewController
    {
        ...
    
        #region Override Methods
        public override void ViewWillAppear ()
        {
            base.ViewWillAppear ();

            // Apply the Dark Interface Appearance
            View.Window.Appearance = NSAppearance.GetAppearance (NSAppearance.NameVibrantDark);

            ...
        }
        #endregion
    }
}
```

Estático `GetAppearance` método o `NSAppearance` classe é usada para obter uma aparência nomeada do sistema (neste caso `NSAppearance.NameVibrantDark`).

Apple tem as seguintes sugestões para o uso de aspectos do sistema:

- Prefira cores nomeadas valores embutidos em código (como `LabelColor` e `SelectedControlColor`).
- Use o estilo de controle padrão do sistema sempre que possível.

Um aplicativo macOS que usa o sistema aparências automaticamente irá funcionar corretamente para os usuários que tem habilitado os recursos de acessibilidade do aplicativo preferências do sistema. Como resultado, a Apple sugere que o desenvolvedor deve sempre usar aparências de sistema em seus aplicativos macOS.

<a name="Designing-UIs-with-Storyboards" />

## <a name="designing-uis-with-storyboards"></a>Criando interfaces do usuário com Storyboards

Storyboards permitem que o desenvolvedor não apenas os elementos individuais que fazem backup de Interface do usuário do aplicativo, mas para visualizar e projetar a interface do usuário de fluxo de design e a hierarquia de elementos fornecidos.

Controladores permitem que o desenvolvedor coletar elementos em uma unidade de composição e abstrato Segues e remova o típico "cola código" necessários para mover em toda a hierarquia do modo de exibição:

[![](modern-cocoa-apps-images/content12.png "Editando a interface do usuário no construtor de Interface do Xcode")](modern-cocoa-apps-images/content12.png#lightbox)

Para obter mais informações, consulte nosso [Introdução a Storyboards](~/mac/platform/storyboards/index.md) documentação.

Há muitas instâncias em que uma determinada cena definida em um storyboard exigirá dados de uma cena anterior na hierarquia do modo de exibição. Apple tem as seguintes sugestões para transmitir informações entre o segundo plano:

- Dependências de dados sempre devem propagar para baixo na hierarquia.
- Evite dependências estruturais de interface do usuário de codificação, pois isso limita a flexibilidade de interface do usuário.
- Use Interfaces c# para fornecer as dependências de dados genéricos.

O controlador de exibição que está atuando como a origem do Segue, pode substituir o `PrepareForSegue` método e não qualquer inicialização necessário (por exemplo, passar dados) antes do Segue é executado para exibir o controlador de exibição de destino. Por exemplo:

```csharp
public override void PrepareForSegue (NSStoryboardSegue segue, NSObject sender)
{
    base.PrepareForSegue (segue, sender);

    // Take action based on Segue ID
    switch (segue.Identifier) {
    case "MyNamedSegue":
        // Prepare for the segue to happen
        ...
        break;
    }
}
```

Para obter mais informações, consulte nosso [Segues](~/mac/platform/storyboards/indepth.md#Segues) documentação.

<a name="Propagating-Actions" />

## <a name="propagating-actions"></a>Propagação de ações

Com base no design do aplicativo macOS, pode haver vezes quando o manipulador recomendado para uma ação em um controle de interface do usuário pode estar em outro lugar na hierarquia de interface do usuário. Isso ocorre geralmente de Menus e itens de Menu que moram nos seu próprios cena, separada do restante da interface de usuário do aplicativo.

Para lidar com essa situação, o desenvolvedor pode criar uma ação personalizada e passar a ação a cadeia do Respondente. Para obter mais informações, consulte nosso [trabalhando com ações de janela personalizada](~/mac/user-interface/menu.md) documentação.

<a name="Modern-Mac-Features" />

## <a name="modern-mac-features"></a>Recursos de Mac modernos

Apple incluiu vários recursos voltados para o usuário macOS Serra que permitem que o desenvolvedor aproveitar ao máximo a plataforma de Mac, como:

- **NSUserActivity** -Isso permite que o aplicativo descrever a atividade que o usuário está atualmente envolvido no. `NSUserActivity` foi inicialmente criada para dar suporte à entrega, onde uma atividade iniciada em um dos dispositivos do usuário pode ser selecionada e continuação em outro dispositivo. `NSUserActivity` funciona a mesmo em macOS como ele faz no iOS, então consulte nossa [Introdução à entrega](~/ios/platform/handoff.md) iOS documentação para obter mais detalhes.
- **Siri no Mac** -Siri usa a atividade atual (`NSUserActivity`) para fornecer contexto para os comandos que um usuário pode emitir.
- **Restauração de estado** - quando o usuário fecha um aplicativo em macOS e depois relaunches-la, o aplicativo será automaticamente retornado para seu estado anterior. O desenvolvedor pode usar a API de restauração de estado para codificar e restaurar estados transitórios de interface do usuário antes da Interface do usuário é exibida ao usuário. Se o aplicativo for `NSDocument` com base, restauração de estado é tratada automaticamente. Para ativar a restauração do estado de não -`NSDocument` aplicativos com base, defina o `Restorable` do `NSWindow` classe para `true`.
- **Documentos na nuvem** -antes macOS serra, um aplicativo era a explicitamente optar para trabalhar com documentos em iCloud do usuário unidade. Em macOS Serra usuário **Desktop** e **documentos** pastas podem ser sincronizadas com o iCloud unidade automaticamente pelo sistema. Como resultado, as cópias locais de documentos podem ser excluídas para liberar espaço no computador do usuário. `NSDocument` aplicativos com base manipula automaticamente essa alteração. Todos os outros tipos de aplicativo serão necessário usar um `NSFileCoordinator` para sincronizar a leitura e gravação de documentos.

<a name="Summary" />

## <a name="summary"></a>Resumo

Este artigo abordou várias dicas, recursos e técnicas de que um desenvolvedor pode usar para compilar um aplicativo moderno macOS em Xamarin.Mac.



## <a name="related-links"></a>Links relacionados

- [Exemplos de macOS](https://developer.xamarin.com/samples/mac/)
