---
title: Compilando aplicativos macOS modernos
description: Este artigo aborda várias dicas, recursos e técnicas que um desenvolvedor pode usar para criar um aplicativo macOS moderno no Xamarin. Mac.
ms.prod: xamarin
ms.assetid: F20EE590-246E-40EB-B309-D9D8C090C7F1
ms.technology: xamarin-mac
author: conceptdev
ms.author: crdun
ms.date: 03/14/2017
ms.openlocfilehash: 04bcf1012800ef2883fef80580bcec869bd8e5a3
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70291478"
---
# <a name="building-modern-macos-apps"></a>Compilando aplicativos macOS modernos

_Este artigo aborda várias dicas, recursos e técnicas que um desenvolvedor pode usar para criar um aplicativo macOS moderno no Xamarin. Mac._

<a name="Building-Modern-Looks-with-Modern-Views" />

## <a name="building-modern-looks-with-modern-views"></a>Criando aparências modernas com exibições modernas

Uma aparência moderna incluirá uma janela moderna e aparência da barra de ferramentas, como o aplicativo de exemplo mostrado abaixo:

[![](modern-cocoa-apps-images/content08.png "Um exemplo de uma interface do usuário de aplicativo Mac moderna")](modern-cocoa-apps-images/content08.png#lightbox)

<a name="Enabling-Full-Sized-Content-Views" />

### <a name="enabling-full-sized-content-views"></a>Habilitando exibições de conteúdo de tamanho completo

Para fazer isso parecer um aplicativo Xamarin. Mac, o desenvolvedor desejará usar uma _exibição de conteúdo de tamanho completo_, o que significa que o conteúdo se estende sob as áreas de ferramenta e barra de título e será automaticamente desfocado pelo MacOS.

Para habilitar esse recurso no código, crie uma classe personalizada para o `NSWindowController` e faça com que ela fique parecida com a seguinte:

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

Esse recurso também pode ser habilitado no Interface Builder do Xcode selecionando a janela e verificando a **exibição de conteúdo de tamanho completo**:

[![](modern-cocoa-apps-images/content01.png "Editando o storyboard principal no Interface Builder do Xcode")](modern-cocoa-apps-images/content01.png#lightbox)

Ao usar uma exibição de conteúdo de tamanho completo, o desenvolvedor pode precisar deslocar o conteúdo abaixo das áreas de título e barra de ferramentas para que o conteúdo específico (como rótulos) não deslize para eles.

Para complicar esse problema, as áreas de título e barra de ferramentas podem ter uma altura dinâmica com base na ação que o usuário está executando no momento, a versão do macOS que o usuário instalou e/ou o hardware do Mac no qual o aplicativo está sendo executado.

Como resultado, simplesmente embutir o deslocamento ao fazer o layout da interface do usuário não funcionará. O desenvolvedor precisará usar uma abordagem dinâmica.

A Apple incluiu a propriedade `ContentLayoutRect` [ObservableCollection de chave-valor](~/mac/app-fundamentals/databinding.md#Observing_Value_Changes) da `NSWindow` classe para obter a área de conteúdo atual no código. O desenvolvedor pode usar esse valor para posicionar manualmente os elementos necessários quando a área de conteúdo é alterada.

A melhor solução é usar o layout automático e as classes de tamanho para posicionar os elementos da interface do usuário no código ou Interface Builder.

Um código como o exemplo a seguir pode ser usado para posicionar elementos da interface do usuário usando classes de tamanho e AutoLayout no controlador de exibição do aplicativo:

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

Esse código cria o armazenamento para uma restrição Top que será aplicada a um rótulo (`ItemTitle`) para garantir que ele não seja guiado na área título e barra de ferramentas:

```csharp
public NSLayoutConstraint topConstraint { get; set; }
```

Ao substituir o método do `UpdateViewConstraints` controlador de exibição, o desenvolvedor pode testar para ver se a restrição necessária já foi criada e criá-la, se necessário.

Se uma nova restrição precisar ser criada, a `ContentLayoutGuide` propriedade da janela do controle que precisa ser restringido será acessada e convertida em um: `NSLayoutGuide`

```csharp
var contentLayoutGuide = ItemTitle.Window?.ContentLayoutGuide as NSLayoutGuide;
```

A propriedade TopAnchor do `NSLayoutGuide` é acessada e, se estiver disponível, ela é usada para criar uma nova restrição com o valor de deslocamento desejado e a nova restrição é ativada para aplicá-la:

```csharp
// Assemble constraint and activate it
topConstraint = topAnchor.ConstraintEqualToAnchor (topAnchor, 20);
topConstraint.Active = true;
```

<a name="Enabling-Streamlined-Toolbars" />

### <a name="enabling-streamlined-toolbars"></a>Habilitando barras de ferramentas simplificadas

Uma janela do macOS normal inclui uma barra de título padrão em execuções na borda superior da janela. Se a janela também incluir uma barra de ferramentas, ela será exibida nessa área da barra de título:

[![](modern-cocoa-apps-images/content02.png "Uma barra de ferramentas Mac padrão")](modern-cocoa-apps-images/content02.png#lightbox)

Ao usar uma barra de ferramentas simplificada, a área de título desaparece e a barra de ferramentas é movida para cima na posição da barra de título, alinhada com a janela fechar, minimizar e maximizar os botões:

[![](modern-cocoa-apps-images/content03.png "Uma barra de ferramentas Mac simplificada")](modern-cocoa-apps-images/content03.png#lightbox)

A barra `ViewWillAppear` `NSViewController` de ferramentas simplificada é habilitada substituindo o método do e fazendo com que ele se pareça com o seguinte:

```csharp
public override void ViewWillAppear ()
{
    base.ViewWillAppear ();

    // Enable streamlined Toolbars
    View.Window.TitleVisibility = NSWindowTitleVisibility.Hidden;
}
```

Esse efeito é normalmente usado para _aplicativos sapatos_ (um aplicativo de janela) como mapas, calendário, observações e preferências do sistema. 

<a name="Using-Accessory-View-Controllers" />

### <a name="using-accessory-view-controllers"></a>Usando controladores de exibição de acessório

Dependendo do design do aplicativo, o desenvolvedor também pode desejar complementar a área da barra de título com um controlador de exibição de acessório que aparece logo abaixo da área de título/barra de ferramentas para fornecer controles sensíveis ao contexto para o usuário com base na atividade em que eles estão atualmente envolvido em:

[![](modern-cocoa-apps-images/content04.png "Um controlador de exibição de acessório de exemplo")](modern-cocoa-apps-images/content04.png#lightbox)

O controlador de exibição de acessório será automaticamente desfocado e redimensionado pelo sistema sem a intervenção do desenvolvedor.

Para adicionar um controlador de exibição de acessório, faça o seguinte:

1. No **Gerenciador de Soluções**, clique duas vezes no arquivo `Main.storyboard` para abri-lo para edição.
2. Arraste um **controlador de exibição personalizado** para a hierarquia da janela: 

    [![](modern-cocoa-apps-images/content05.png "Adicionando um novo controlador de exibição personalizado")](modern-cocoa-apps-images/content05.png#lightbox)
3. Layout da interface do usuário da exibição de acessório: 

    [![](modern-cocoa-apps-images/content06.png "Criando a nova exibição")](modern-cocoa-apps-images/content06.png#lightbox)
4. Expor a exibição de acessório como uma **tomada** e quaisquer outras **ações** ou **saídas** para sua interface do usuário: 

    [![](modern-cocoa-apps-images/content07.png "Adicionando a tomada necessária")](modern-cocoa-apps-images/content07.png#lightbox)
5. Salve as alterações.
6. Retorne para Visual Studio para Mac para sincronizar as alterações.

Edite `NSWindowController` o e faça com que ele se pareça com o seguinte:

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

Os pontos principais desse código são onde a exibição é definida como a exibição personalizada que foi definida em Interface Builder e exposta como uma **tomada**:

```csharp
accessoryView.View = AccessoryViewGoBar;
```

E o `LayoutAttribute` que define _onde_ o acessório será exibido:

```csharp
accessoryView.LayoutAttribute = NSLayoutAttribute.Bottom;
```

Como o MacOS agora está totalmente localizado, `Left` as `Right` Propriedades e `NSLayoutAttribute` foram preteridas e devem ser substituídas `Trailing`por `Leading` e.

<a name="Using-Tabbed-Windows" />

### <a name="using-tabbed-windows"></a>Usando janelas com guias

Além disso, o sistema macOS pode adicionar controladores de exibição de acessório à janela do aplicativo. Por exemplo, para criar janelas com guias em que várias das janelas do aplicativo são mescladas em uma janela virtual:

[![](modern-cocoa-apps-images/content08.png "Um exemplo de uma janela Mac com guias")](modern-cocoa-apps-images/content08.png#lightbox)

Normalmente, o desenvolvedor precisará tomar uma ação limitada usar janelas com guias em seus aplicativos Xamarin. Mac, o sistema as tratará automaticamente da seguinte maneira:

- O Windows será automaticamente tabulado quando o `OrderFront` método for invocado.
- O Windows não terá Tabulação automaticamente quando o `OrderOut` método for invocado.
- No código, todas as janelas com guias ainda são consideradas "visíveis", no entanto, todas as guias não na extremidade frontal ficam ocultas pelo sistema usando CoreGraphics.
- Use a `TabbingIdentifier` propriedade de `NSWindow` para agrupar janelas em guias.
- Se for um `NSDocument` aplicativo baseado, vários desses recursos serão habilitados automaticamente (como o botão de adição que está sendo adicionado à barra de guias) sem nenhuma ação do desenvolvedor.
- Aplicativos sem `GetNewWindowForTab` `NSWindowsController`base podem habilitar o botão "mais" no grupo de guias para adicionar um novo documento, substituindo o método do.`NSDocument`

Reunindo todas as partes, o `AppDelegate` de um aplicativo que desejava usar janelas com guias baseadas em sistema poderia ser semelhante ao seguinte:

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

Onde a `NewDocumentNumber` Propriedade mantém o controle do número de novos documentos criados e o `NewDocument` método cria um novo documento e o exibe.

Em `NSWindowController` seguida, o poderia ter a seguinte aparência:

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

Em que a `App` propriedade estática fornece um atalho para chegar `AppDelegate`ao. O `SetDefaultDocumentTitle` método define um novo título de documentos com base no número de novos documentos criados.

O código a seguir informa ao macOS que o aplicativo prefere usar guias e fornece uma cadeia de caracteres que permite que as janelas do aplicativo sejam agrupadas em guias:

```csharp
// Prefer Tabbed Windows
Window.TabbingMode = NSWindowTabbingMode.Preferred;
Window.TabbingIdentifier = "Main";
```

E o método override a seguir adiciona um botão de adição à barra de guias que criará um novo documento quando clicado pelo usuário:

```csharp
public override void GetNewWindowForTab (NSObject sender)
{
    // Ask app to open a new document window
    App.NewDocument (this);
}
```

<a name="Using-Core-Animation" />

### <a name="using-core-animation"></a>Usando a animação de núcleo

A animação principal é um mecanismo de processamento gráfico de alta potência que é incorporado ao macOS. A animação principal foi otimizada para tirar proveito da GPU (unidade de processamento gráfico) disponível em hardware moderno do macOS em vez de executar as operações de gráficos na CPU, o que pode retardar a máquina.

O `CALayer`, fornecido pela animação de núcleo, pode ser usado para tarefas como rolagem rápida e fluida e animações. A interface do usuário de um aplicativo deve ser composta por várias subexibições e camadas para aproveitar totalmente a animação principal.

Um `CALayer` objeto fornece várias propriedades que permitem ao desenvolvedor controlar o que é apresentado na tela ao usuário, como:

- `Content`-Pode ser um `NSImage` ou `CGImage` que fornece o conteúdo da camada.
- `BackgroundColor`-Define a cor do plano de fundo da camada como um`CGColor`
- `BorderWidth`-Define a largura da borda.
- `BorderColor`-Define a cor da borda.

Para utilizar gráficos principais na interface do usuário do aplicativo, ele deve usar exibições de _camada com suporte_ , que a Apple sugere que o desenvolvedor sempre deve habilitar na exibição de conteúdo da janela. Dessa forma, todas as exibições filho herdarão automaticamente o suporte de camada também.

Além disso, a Apple sugere o uso de exibições de camada com suporte `CALayer` em oposição à adição de uma nova subcamada, pois o sistema tratará automaticamente várias das configurações necessárias (como as necessárias para uma tela de retina).

O backup de camada pode ser habilitado definindo o `WantsLayer` de um `NSView` para `true` ou dentro do Interface Builder do Xcode no **Inspetor de efeitos de exibição** , verificando a camada de **animação principal**:

[![](modern-cocoa-apps-images/content09.png "O Inspetor de efeitos de exibição")](modern-cocoa-apps-images/content09.png#lightbox)

<a name="Redrawing-Views-with-Layers" />

#### <a name="redrawing-views-with-layers"></a>Redesenhando exibições com camadas

Outra etapa importante ao usar exibições com suporte de camada em um aplicativo Xamarin. Mac é `LayerContentsRedrawPolicy` definir o `NSView` do `OnSetNeedsDisplay` para no `NSViewController`. Por exemplo:

```csharp
public override void ViewWillAppear ()
{
    base.ViewWillAppear ();

    // Set the content redraw policy
    View.LayerContentsRedrawPolicy = NSViewLayerContentsRedrawPolicy.OnSetNeedsDisplay;
}
```

Se o desenvolvedor não definir essa propriedade, a exibição será redesenhada sempre que sua origem de quadro for alterada, o que não é desejado por motivos de desempenho. No entanto, com `OnSetNeedsDisplay` essa propriedade definida para o desenvolvedor, `NeedsDisplay` será `true` necessário definir manualmente como para forçar o conteúdo a redesenhar.

Quando uma exibição é marcada como suja, o sistema verifica a `WantsUpdateLayer` propriedade da exibição. Se ele retornar `true` , o `UpdateLayer` método será chamado, caso contrário `DrawRect` , o método da exibição será chamado para atualizar o conteúdo da exibição.

A Apple tem as seguintes sugestões para atualizar um conteúdo de exibições quando necessário:

- A Apple prefere usar `UpdateLater` o `DrawRect` over sempre que possível, pois oferece um aumento significativo no desempenho.
- Use o mesmo `layer.Contents` para elementos da interface do usuário que parecem semelhantes.
- A Apple também prefere que o desenvolvedor crie sua interface do usuário usando modos de exibição `NSTextField`padrão, como novamente, sempre que possível.

Para usar `UpdateLayer`o, crie uma classe personalizada para `NSView` o e faça com que o código se pareça com o seguinte:

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

## <a name="using-modern-drag-and-drop"></a>Usando arrastar e soltar moderno

Para apresentar uma experiência moderna de arrastar e soltar para o usuário, o desenvolvedor deve adotar o _recurso arrastar pássaros_ nas operações de arrastar e soltar do aplicativo. Arraste pássaros é onde cada arquivo individual ou item que está sendo arrastado inicialmente aparece como um elemento individual que flocks (agrupado sob o cursor com uma contagem do número de itens) à medida que o usuário continua a operação de arrastar.

Se o usuário encerrar a operação de arrastar, os elementos individuais serão Unflock e retornarão aos seus locais originais.

O código de exemplo a seguir habilita o arrastar pássaros em uma exibição personalizada:

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

O efeito pássaros foi obtido enviando-se cada item que está sendo `BeginDraggingSession` arrastado para `NSView` o método do como um elemento separado em uma matriz.

Ao trabalhar com um `NSTableView` ou `NSOutlineView` `PastboardWriterForRow` ,`NSTableViewDataSource` use o método da classe para iniciar a operação de arrastar:

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

Isso permite que o desenvolvedor forneça um indivíduo `NSDraggingItem` para cada item na tabela que está sendo arrastada em oposição ao método `WriteRowsWith` mais antigo que grava todas as linhas como um único grupo na área de ti.

Ao trabalhar com `NSCollectionViews`o, use novamente `PasteboardWriterForItemAt` o método, em oposição `WriteItemsAt` ao método quando o arrasto for iniciado.

O desenvolvedor sempre deve evitar colocar arquivos grandes na área de colagem. Novo no MacOS Sierra, as _promessas de arquivo_ permitem que o desenvolvedor Coloque referências a determinados arquivos na área de armazenamento que posteriormente serão atendidos quando o usuário concluir a operação de `NSFilePromiseProvider` soltar `NSFilePromiseReceiver` usando as novas classes e.

<a name="Using-Modern-Event-Tracking" />

## <a name="using-modern-event-tracking"></a>Usando o rastreamento de eventos moderno

Para um elemento de interface do usuário (como `NSButton`um) que foi adicionado a uma área de título ou barra de ferramentas, o usuário deve ser capaz de clicar no elemento e fazê-lo disparar um evento como normal (como exibir uma janela pop-up). No entanto, como o item também está no título ou na área da barra de ferramentas, o usuário deve ser capaz de clicar e arrastar o elemento para mover a janela também.

Para fazer isso no código, crie uma classe personalizada para o elemento ( `NSButton`como) e substitua o `MouseDown` evento da seguinte maneira:

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

Esse código usa o `TrackEventsMatching` método `NSWindow` do qual o elemento de interface do usuário está anexado para `LeftMouseUp` interceptar os eventos e `LeftMouseDragged` . Para um `LeftMouseUp` evento, o elemento de interface do usuário responde normalmente. Para o `LeftMouseDragged` evento, o evento é passado para o `NSWindow`método `PerformWindowDrag` do para mover a janela na tela.

Chamar o `PerformWindowDrag` método `NSWindow` da classe fornece os seguintes benefícios:

- Ele permite que a janela se mova, mesmo se o aplicativo estiver suspenso (por exemplo, ao processar um loop profundo).
- A alternância de espaço funcionará conforme o esperado.
- A barra de espaços será exibida normalmente.
- O ajuste e alinhamento da janela funcionam normalmente.

<a name="Using-Modern-Container-View-Controls" />

## <a name="using-modern-container-view-controls"></a>Usando controles de exibição de contêiner modernos

macOS Sierra fornece muitos aprimoramentos modernos para os controles de exibição de contêiner existentes disponíveis na versão anterior do sistema operacional.

<a name="Table View Enhancements" />

## <a name="table-view-enhancements"></a>Aprimoramentos de exibição de tabela

O desenvolvedor sempre deve usar a nova `NSView` versão baseada em controles de exibição de contêiner `NSTableView`, como. Por exemplo:

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

Isso permite que ações de linha da tabela personalizada sejam anexadas a linhas dadas na tabela (como passar o dedo para a direita para excluir a linha). Para habilitar esse comportamento, substitua o `RowActions` método `NSTableViewDelegate`do:

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

O estático `NSTableViewRowAction.FromStyle` é usado para criar uma nova ação de linha de tabela dos seguintes estilos:

- `Regular`-Executa uma ação não destrutiva padrão, como editar o conteúdo da linha.
- `Destructive`-Executa uma ação destrutiva, como excluir a linha da tabela. Essas ações serão renderizadas com um plano de fundo vermelho.

<a name="Scroll-View-Enhancements" />

## <a name="scroll-view-enhancements"></a>Aprimoramentos da exibição de rolagem 

Ao usar uma exibição de rolagem (`NSScrollView`) diretamente ou como parte de outro controle ( `NSTableView`como), o conteúdo da exibição de rolagem pode deslizar nas áreas de título e barra de ferramentas em um aplicativo Xamarin. Mac usando uma aparência e exibições modernas.

Como resultado, o primeiro item na área de conteúdo da exibição de rolagem pode ser parcialmente obscurecido pelo título e pela área da barra de ferramentas.

Para corrigir esse problema, a Apple adicionou duas novas propriedades à `NSScrollView` classe:

- `ContentInsets`– Permite que o desenvolvedor forneça um `NSEdgeInsets` objeto que define o deslocamento que será aplicado à parte superior da exibição de rolagem.
- `AutomaticallyAdjustsContentInsets`-Se `true` a exibição de rolagem manipulará `ContentInsets` automaticamente o para o desenvolvedor.

Usando o `ContentInsets` desenvolvedor, é possível ajustar o início da exibição de rolagem para permitir a inclusão de acessórios, como:

- Um indicador de classificação como aquele mostrado no aplicativo de email.
- Um campo de pesquisa.
- Um botão atualizar ou atualizar.

<a name="Auto-Layout-and-Localization-in-Modern-Apps" />

## <a name="auto-layout-and-localization-in-modern-apps"></a>Layout automático e localização em aplicativos modernos

A Apple incluiu várias tecnologias no Xcode que permitem ao desenvolvedor criar facilmente um aplicativo macOS internacionalizado. Agora, o Xcode permite que o desenvolvedor separe o texto voltado para o usuário do design da interface do usuário do aplicativo em seus arquivos de storyboard e fornece ferramentas para manter essa separação se a interface do usuário for alterada.

Para obter mais informações, consulte o [Guia de internacionalização e localização](https://developer.apple.com/library/content/documentation/MacOSX/Conceptual/BPInternational/InternationalizingYourUserInterface/InternationalizingYourUserInterface.html)da Apple.

<a name="Implementing-Base-Internationalization" />

### <a name="implementing-base-internationalization"></a>Implementando a internacionalização de base 

Ao implementar a internacionalização de base, o desenvolvedor pode fornecer um único arquivo de storyboard para representar a interface do usuário do aplicativo e separar todas as cadeias de caracteres voltadas para o usuário. 

Quando o desenvolvedor estiver criando o arquivo de storyboard inicial (ou arquivos) que definem a interface do usuário do aplicativo, eles serão criados na internacionalização de base (a linguagem que o desenvolvedor fala).

Em seguida, o desenvolvedor pode exportar localizações e as cadeias de caracteres de internacionalização de base (no design da interface do usuário do storyboard) que podem ser convertidas em vários idiomas.

Posteriormente, essas localizações podem ser importadas e o Xcode gerará os arquivos de cadeia de caracteres específicos do idioma para o storyboard.

<a name="Implementing-Auto-Layout-to-Support-Localization" />

### <a name="implementing-auto-layout-to-support-localization"></a>Implementando layout automático para oferecer suporte à localização

Como as versões localizadas de valores de cadeia de caracteres podem ter tamanhos e/ou direção de leitura amplamente diferentes, o desenvolvedor deve usar o layout automático para posicionar e dimensionar a interface do usuário do aplicativo em um arquivo de storyboard.

A Apple sugere o seguinte:

- **Remover restrições de largura fixa** -todas as exibições baseadas em texto devem ter permissão para redimensionar com base em seu conteúdo. A exibição de largura fixa pode cortar seu conteúdo em idiomas específicos.
- **Usar tamanhos de conteúdo intrínsecos** -por padrão, as exibições baseadas em texto serão dimensionadas automaticamente para ajustar seu conteúdo. Para exibição baseada em texto que não está dimensionando corretamente, selecione-as no interface Builder do Xcode e escolha **Editar** > **tamanho para ajustar o conteúdo**.
- **Aplicar atributos à esquerda e à direita** – como a direção do texto pode ser alterada com base no idioma do usuário, use os `Leading` atributos `Trailing` novo e de restrição, em oposição `Right` aos `Left` existentes e atributos. `Leading`e `Trailing` será ajustado automaticamente com base na direção dos idiomas.
- **Fixar exibições nas exibições adjacentes** – isso permite que as exibições sejam reposicionadas e redimensionadas à medida que as exibições relacionadas a elas são alteradas em resposta ao idioma selecionado.
- **Não defina os tamanhos mínimo e/ou máximo do Windows** – permita que o Windows altere o tamanho conforme o idioma selecionado redimensiona suas áreas de conteúdo.
- **Alterações no layout de teste constantemente** -durante o desenvolvimento no aplicativo devem ser testadas constantemente em idiomas diferentes. Consulte a documentação de teste da Apple em [seu aplicativo internacionalizado](https://developer.apple.com/library/content/documentation/MacOSX/Conceptual/BPInternational/TestingYourInternationalApp/TestingYourInternationalApp.html#//apple_ref/doc/uid/10000171i-CH7-SW1) para obter mais detalhes.
- **Usar NSStackViews para fixar exibições em conjunto**  -  `NSStackViews` permite que seu conteúdo seja deslocado e cresça de maneiras previsíveis e o tamanho da alteração de conteúdo com base no idioma selecionado.

<a name="Localizing-in-Xcodes-Interface-Builder" />

### <a name="localizing-in-xcodes-interface-builder"></a>Localizando no Interface Builder do Xcode

A Apple forneceu vários recursos no Interface Builder do Xcode que o desenvolvedor pode usar ao criar ou editar a interface do usuário de um aplicativo para dar suporte à localização. A seção **direção do texto** do **Inspetor de atributo** permite que o desenvolvedor forneça dicas sobre como a direção deve ser usada e atualizada em uma exibição de seleção baseada em texto `NSTextField`(como):

[![](modern-cocoa-apps-images/content10.png "As opções de direção do texto")](modern-cocoa-apps-images/content10.png#lightbox)

Há três valores possíveis para a **direção do texto**:

- **Natural** -o layout é baseado na cadeia de caracteres atribuída ao controle.
- Da **esquerda para a direita** -o layout é sempre forçado à esquerda para a direita.
- Da **direita para a esquerda** -o layout é sempre forçado à direita para a esquerda.

Há dois valores possíveis para o **layout**:

- Da **esquerda para a direita** -o layout é sempre da esquerda para a direita.
- Da **direita para a esquerda** -o layout é sempre da direita para a esquerda.

Normalmente, eles não devem ser alterados, a menos que um alinhamento específico seja necessário.

A propriedade **Mirror** instrui o sistema a inverter Propriedades de controle específicas (como a posição da imagem da célula). Ele tem três valores possíveis:

- **Automaticamente** -a posição será alterada automaticamente com base na direção do idioma selecionado.
- **Na interface da direita para a esquerda** -a posição só será alterada em idiomas com base na direita para a esquerda.
- **Nunca** -a posição nunca será alterada.

Se o desenvolvedor tiver especificado **Center**, **Justify** ou alinhamento **total** no conteúdo de uma exibição baseada em texto, eles nunca serão invertidos com base no idioma selecionado.

Antes da macOS Sierra, os controles criados no código não seriam espelhados automaticamente. O desenvolvedor precisava usar um código como o seguinte para lidar com o espelhamento:

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

Em que `Alignment` e `ImagePosition` estão`UserInterfaceLayoutDirection` sendo definidos com base no controle.

MacOS Sierra adiciona vários novos construtores de conveniência (por meio do `CreateButton` método estático) que usam vários parâmetros (como título, imagem e ação) e serão automaticamente espelhados corretamente. Por exemplo:

```csharp
var button2 = NSButton.CreateButton (myTitle, myImage, () => {
    // Take action when the button is pressed
    ...
});
```

<a name="Using-System-Appearances" />

## <a name="using-system-appearances"></a>Usando as aparências do sistema

Os aplicativos macOS modernos podem adotar uma nova aparência de interface escura que funciona bem para aplicativos de criação, edição ou apresentação de imagens:

[![](modern-cocoa-apps-images/content11.png "Um exemplo de uma interface do usuário de janela de Mac escuro")](modern-cocoa-apps-images/content11.png#lightbox)

Isso pode ser feito adicionando uma linha de código antes que a janela seja apresentada. Por exemplo:

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

O método `GetAppearance` estático `NSAppearance` da classe é usado para obter uma aparência nomeada do sistema (nesse caso `NSAppearance.NameVibrantDark`).

A Apple tem as seguintes sugestões para usar as aparências do sistema:

- Prefira cores nomeadas sobre valores codificados ( `LabelColor` como e `SelectedControlColor`).
- Use o estilo de controle padrão do sistema sempre que possível.

Um aplicativo macOS que usa as aparências do sistema funcionará automaticamente corretamente para os usuários que habilitaram os recursos de acessibilidade do aplicativo Preferências do sistema. Como resultado, a Apple sugere que o desenvolvedor sempre deve usar as aparências do sistema em seus aplicativos macOS.

<a name="Designing-UIs-with-Storyboards" />

## <a name="designing-uis-with-storyboards"></a>Projetando interfaces de projeto com storyboards

Os storyboards permitem que o desenvolvedor não apenas projete os elementos individuais que compõem a interface do usuário de um aplicativo, mas visualizem e projetem o fluxo da interface de usuário e a hierarquia dos elementos determinados.

Os controladores permitem que o desenvolvedor colete elementos em uma unidade de composição e continuações abstrato e remova o "código de União" típico necessário para se mover em toda a hierarquia de exibição:

[![](modern-cocoa-apps-images/content12.png "Editando a interface do usuário no Interface Builder do Xcode")](modern-cocoa-apps-images/content12.png#lightbox)

Para obter mais informações, consulte nossa [introdução à documentação de storyboards](~/mac/platform/storyboards/index.md) .

Há muitas instâncias em que uma determinada cena definida em um storyboard exigirá dados de uma cena anterior na hierarquia de exibição. A Apple tem as seguintes sugestões para passar informações entre cenas:

- Os dependancies de dados sempre devem ser colocados em cascata na hierarquia.
- Evite codificar o dependancies estrutural da interface do usuário, pois isso limita a flexibilidade da interface do usuário.
- Use C# interfaces para fornecer dependancies de dados genéricos.

O controlador de exibição que está agindo como a origem do transição pode substituir o `PrepareForSegue` método e fazer qualquer inicialização necessária (como passar dados) antes de o transição ser executado para exibir o controlador de exibição de destino. Por exemplo:

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

Para obter mais informações, consulte nossa documentação do [continuações](~/mac/platform/storyboards/indepth.md#Segues) .

<a name="Propagating-Actions" />

## <a name="propagating-actions"></a>Propagando ações

Com base no design do aplicativo macOS, pode haver ocasiões em que o melhor manipulador para uma ação em um controle de interface do usuário pode estar em outro lugar na hierarquia da interface do usuário. Isso é normalmente verdadeiro para menus e itens de menu que residem em sua própria cena, separados do restante da interface do usuário do aplicativo.

Para lidar com essa situação, o desenvolvedor pode criar uma ação personalizada e passar a ação da cadeia de respondentes. Para obter mais informações, consulte [a documentação trabalhando com ações de janela personalizada](~/mac/user-interface/menu.md) .

<a name="Modern-Mac-Features" />

## <a name="modern-mac-features"></a>Recursos de Mac modernos

A Apple incluiu vários recursos voltados para o usuário em macOS Sierra que permitem ao desenvolvedor aproveitar ao máximo a plataforma Mac, como:

- **NSUserActivity** – isso permite que o aplicativo descreva a atividade na qual o usuário está envolvido no momento. `NSUserActivity`foi inicialmente criado para dar suporte à entrega, em que uma atividade iniciada em um dos dispositivos do usuário pode ser escolhida e continuada em outro dispositivo. `NSUserActivity`funciona da mesma forma no macOS que no iOS, portanto, consulte nossa [introdução à](~/ios/platform/handoff.md) documentação do IOS para obter mais detalhes.
- **Siri no Mac** -Siri usa a atividade atual (`NSUserActivity`) para fornecer contexto aos comandos que um usuário pode emitir.
- **Restauração de estado** – quando o usuário sai de um aplicativo no MacOS e depois o reinicia, o aplicativo será retornado automaticamente ao seu estado anterior. O desenvolvedor pode usar a API de restauração de estado para codificar e restaurar os Estados da interface do usuário transitórias antes que a interface seja exibida para o usuário. Se o aplicativo for `NSDocument` baseado, a restauração de estado será manipulada automaticamente. Para habilitar a restauração de estado para`NSDocument` aplicativos não baseados, defina `Restorable` o da `NSWindow` classe como `true`.
- **Documentos na nuvem** -antes da MacOS Sierra, um aplicativo tinha que optar explicitamente por trabalhar com documentos na unidade icloud do usuário. No macOS Sierra as pastas **área de trabalho** e **documentos** do usuário podem ser sincronizadas com a unidade icloud automaticamente pelo sistema. Como resultado, cópias locais de documentos podem ser excluídas para liberar espaço na máquina do usuário. `NSDocument`os aplicativos baseados irão lidar automaticamente com essa alteração. Todos os outros tipos de aplicativo precisarão usar `NSFileCoordinator` um para sincronizar a leitura e a gravação de documentos.

<a name="Summary" />

## <a name="summary"></a>Resumo

Este artigo abordou várias dicas, recursos e técnicas que um desenvolvedor pode usar para criar um aplicativo macOS moderno no Xamarin. Mac.



## <a name="related-links"></a>Links relacionados

- [Exemplos do macOS](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.Mac)
