---
title: Listas de origem no Xamarin. Mac
description: Este artigo aborda como trabalhar com listas de origem em um aplicativo Xamarin. Mac. Ele descreve como criar e manter listas de origem no Xcode e Interface Builder e interagir com elas em código C#.
ms.prod: xamarin
ms.assetid: 651A3649-5AA8-4133-94D6-4873D99F7FCC
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 03/14/2017
ms.openlocfilehash: ae82918c1f87e47be9446e80f99ebc4dc967c078
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91431941"
---
# <a name="source-lists-in-xamarinmac"></a>Listas de origem no Xamarin. Mac

_Este artigo aborda como trabalhar com listas de origem em um aplicativo Xamarin. Mac. Ele descreve como criar e manter listas de origem no Xcode e Interface Builder e interagir com elas em código C#._

Ao trabalhar com C# e .NET em um aplicativo Xamarin. Mac, você tem acesso às mesmas listas de origem que um desenvolvedor trabalhando no *Objective-C* e no *Xcode* . Como o Xamarin. Mac se integra diretamente com o Xcode, você pode usar o _interface Builder_ do Xcode para criar e manter suas listas de origem (ou, opcionalmente, criá-las diretamente no código C#).

Uma lista de origem é um tipo especial de exibição de estrutura de tópicos usada para mostrar a origem de uma ação, como a barra lateral no Finder ou no iTunes.

[![Uma lista de origem de exemplo](source-list-images/source05.png)](source-list-images/source05.png#lightbox)

Neste artigo, abordaremos as noções básicas de como trabalhar com listas de origem em um aplicativo Xamarin. Mac. É altamente recomendável que você trabalhe pelo artigo [Hello, Mac](~/mac/get-started/hello-mac.md) primeiro, especificamente a [introdução às seções Xcode e Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) e [ações](~/mac/get-started/hello-mac.md#outlets-and-actions) , pois ela aborda os principais conceitos e técnicas que usaremos neste artigo.

Talvez você queira dar uma olhada na seção [expondo classes/métodos C# para Objective-C](~/mac/internals/how-it-works.md) do documento [interno do Xamarin. Mac](~/mac/internals/how-it-works.md) também, explica os `Register` `Export` comandos e usados para conectar suas classes C# a objetos Objective-C e elementos de interface do usuário.

<a name="Introduction_to_Outline_Views"></a>

## <a name="introduction-to-source-lists"></a>Introdução às listas de origem

Como mencionado acima, uma lista de origem é um tipo especial de exibição de estrutura de tópicos usada para mostrar a origem de uma ação, como a barra lateral no Finder ou no iTunes. Uma lista de origem é um tipo de tabela que permite ao usuário expandir ou recolher linhas de dados hierárquicos. Ao contrário de uma exibição de tabela, os itens em uma lista de origem não estão em uma lista simples, eles são organizados em uma hierarquia, como arquivos e pastas em um disco rígido. Se um item em uma lista de origem contiver outros itens, ele poderá ser expandido ou recolhido pelo usuário.

A lista de origem é uma exibição de estrutura de tópicos especialmente estilizada ( `NSOutlineView` ), que é uma subclasse da exibição de tabela ( `NSTableView` ) e, dessa forma, herda grande parte de seu comportamento de sua classe pai. Como resultado, muitas operações com suporte em um modo de exibição de estrutura de tópicos também são compatíveis com uma lista de origem. Um aplicativo Xamarin. Mac tem controle desses recursos e pode configurar os parâmetros da lista de origem (no código ou Interface Builder) para permitir ou impedir determinadas operações.

Uma lista de origem não armazena seus próprios dados, em vez disso, ela se baseia em uma fonte de dados ( `NSOutlineViewDataSource` ) para fornecer as linhas e colunas necessárias, conforme necessário.

O comportamento de uma lista de origem pode ser personalizado fornecendo-se uma subclasse do delegado da exibição de estrutura de tópicos ( `NSOutlineViewDelegate` ) para dar suporte ao tipo de estrutura de tópicos para selecionar funcionalidade, seleção de itens e edição, acompanhamento personalizado e exibições personalizadas para itens individuais.

Como uma lista de origem compartilha grande parte do comportamento e da funcionalidade com uma exibição de tabela e uma exibição de estrutura de tópicos, talvez você queira percorrer as [exibições de tabela](~/mac/user-interface/table-view.md) e a documentação de [exibições de tópicos](~/mac/user-interface/outline-view.md) antes de continuar com este artigo.

<a name="Working_with_Source_Lists"></a>

## <a name="working-with-source-lists"></a>Trabalhando com listas de origem

Uma lista de origem é um tipo especial de exibição de estrutura de tópicos usada para mostrar a origem de uma ação, como a barra lateral no Finder ou no iTunes. Ao contrário das exibições de contorno, antes de definirmos nossa lista de origem em Interface Builder, vamos criar as classes de apoio no Xamarin. Mac.

Primeiro, vamos criar uma nova `SourceListItem` classe para manter os dados da nossa lista de origem. Na **Gerenciador de soluções**, clique com o botão direito do mouse no projeto e selecione **Adicionar**  >  **novo arquivo...** Selecione **General**  >  **classe vazia**geral, insira `SourceListItem` para o **nome** e clique no botão **novo** :

[![Adicionando uma classe vazia](source-list-images/source01.png)](source-list-images/source01.png#lightbox)

Faça com que o `SourceListItem.cs` arquivo se pareça com o seguinte: 

```csharp
using System;
using System.Collections;
using System.Collections.Generic;
using AppKit;
using Foundation;

namespace MacOutlines
{
    public class SourceListItem: NSObject, IEnumerator, IEnumerable
    {
        #region Private Properties
        private string _title;
        private NSImage _icon;
        private string _tag;
        private List<SourceListItem> _items = new List<SourceListItem> ();
        #endregion

        #region Computed Properties
        public string Title {
            get { return _title; }
            set { _title = value; }
        }

        public NSImage Icon {
            get { return _icon; }
            set { _icon = value; }
        }

        public string Tag {
            get { return _tag; }
            set { _tag=value; }
        }
        #endregion

        #region Indexer
        public SourceListItem this[int index]
        {
            get
            {
                return _items[index];
            }

            set
            {
                _items[index] = value;
            }
        }

        public int Count {
            get { return _items.Count; }
        }

        public bool HasChildren {
            get { return (Count > 0); }
        }
        #endregion

        #region Enumerable Routines
        private int _position = -1;

        public IEnumerator GetEnumerator()
        {
            _position = -1;
            return (IEnumerator)this;
        }

        public bool MoveNext()
        {
            _position++;
            return (_position < _items.Count);
        }

        public void Reset()
        {_position = -1;}

        public object Current
        {
            get 
            { 
                try 
                {
                    return _items[_position];
                }

                catch (IndexOutOfRangeException)
                {
                    throw new InvalidOperationException();
                }
            }
        }
        #endregion

        #region Constructors
        public SourceListItem ()
        {
        }

        public SourceListItem (string title)
        {
            // Initialize
            this._title = title;
        }

        public SourceListItem (string title, string icon)
        {
            // Initialize
            this._title = title;
            this._icon = NSImage.ImageNamed (icon);
        }

        public SourceListItem (string title, string icon, ClickedDelegate clicked)
        {
            // Initialize
            this._title = title;
            this._icon = NSImage.ImageNamed (icon);
            this.Clicked = clicked;
        }

        public SourceListItem (string title, NSImage icon)
        {
            // Initialize
            this._title = title;
            this._icon = icon;
        }

        public SourceListItem (string title, NSImage icon, ClickedDelegate clicked)
        {
            // Initialize
            this._title = title;
            this._icon = icon;
            this.Clicked = clicked;
        }

        public SourceListItem (string title, NSImage icon, string tag)
        {
            // Initialize
            this._title = title;
            this._icon = icon;
            this._tag = tag;
        }

        public SourceListItem (string title, NSImage icon, string tag, ClickedDelegate clicked)
        {
            // Initialize
            this._title = title;
            this._icon = icon;
            this._tag = tag;
            this.Clicked = clicked;
        }
        #endregion

        #region Public Methods
        public void AddItem(SourceListItem item) {
            _items.Add (item);
        }

        public void AddItem(string title) {
            _items.Add (new SourceListItem (title));
        }

        public void AddItem(string title, string icon) {
            _items.Add (new SourceListItem (title, icon));
        }

        public void AddItem(string title, string icon, ClickedDelegate clicked) {
            _items.Add (new SourceListItem (title, icon, clicked));
        }

        public void AddItem(string title, NSImage icon) {
            _items.Add (new SourceListItem (title, icon));
        }

        public void AddItem(string title, NSImage icon, ClickedDelegate clicked) {
            _items.Add (new SourceListItem (title, icon, clicked));
        }

        public void AddItem(string title, NSImage icon, string tag) {
            _items.Add (new SourceListItem (title, icon, tag));
        }

        public void AddItem(string title, NSImage icon, string tag, ClickedDelegate clicked) {
            _items.Add (new SourceListItem (title, icon, tag, clicked));
        }

        public void Insert(int n, SourceListItem item) {
            _items.Insert (n, item);
        }

        public void RemoveItem(SourceListItem item) {
            _items.Remove (item);
        }

        public void RemoveItem(int n) {
            _items.RemoveAt (n);
        }

        public void Clear() {
            _items.Clear ();
        }
        #endregion

        #region Events
        public delegate void ClickedDelegate();
        public event ClickedDelegate Clicked;

        internal void RaiseClickedEvent() {
            // Inform caller
            if (this.Clicked != null)
                this.Clicked ();
        }
        #endregion
    }
}
```

Na **Gerenciador de soluções**, clique com o botão direito do mouse no projeto e selecione **Adicionar**  >  **novo arquivo...** Selecione **General**  >  **classe vazia**geral, insira `SourceListDataSource` para o **nome** e clique no botão **novo** . Faça com que o `SourceListDataSource.cs` arquivo se pareça com o seguinte:

```csharp
using System;
using System.Collections;
using System.Collections.Generic;
using AppKit;
using Foundation;

namespace MacOutlines
{
    public class SourceListDataSource : NSOutlineViewDataSource
    {
        #region Private Variables
        private SourceListView _controller;
        #endregion

        #region Public Variables
        public List<SourceListItem> Items = new List<SourceListItem>();
        #endregion

        #region Constructors
        public SourceListDataSource (SourceListView controller)
        {
            // Initialize
            this._controller = controller;
        }
        #endregion

        #region Override Properties
        public override nint GetChildrenCount (NSOutlineView outlineView, Foundation.NSObject item)
        {
            if (item == null) {
                return Items.Count;
            } else {
                return ((SourceListItem)item).Count;
            }
        }

        public override bool ItemExpandable (NSOutlineView outlineView, Foundation.NSObject item)
        {
            return ((SourceListItem)item).HasChildren;
        }

        public override NSObject GetChild (NSOutlineView outlineView, nint childIndex, Foundation.NSObject item)
        {
            if (item == null) {
                return Items [(int)childIndex];
            } else {
                return ((SourceListItem)item) [(int)childIndex]; 
            }
        }

        public override NSObject GetObjectValue (NSOutlineView outlineView, NSTableColumn tableColumn, NSObject item)
        {
            return new NSString (((SourceListItem)item).Title);
        }
        #endregion

        #region Internal Methods
        internal SourceListItem ItemForRow(int row) {
            int index = 0;

            // Look at each group
            foreach (SourceListItem item in Items) {
                // Is the row inside this group?
                if (row >= index && row <= (index + item.Count)) {
                    return item [row - index - 1];
                }

                // Move index
                index += item.Count + 1;
            }

            // Not found 
            return null;
        }
        #endregion
    }
}
```

Isso fornecerá os dados para nossa lista de origem.

Na **Gerenciador de soluções**, clique com o botão direito do mouse no projeto e selecione **Adicionar**  >  **novo arquivo...** Selecione **General**  >  **classe vazia**geral, insira `SourceListDelegate` para o **nome** e clique no botão **novo** . Faça com que o `SourceListDelegate.cs` arquivo se pareça com o seguinte:

```csharp
using System;
using AppKit;
using Foundation;

namespace MacOutlines
{
    public class SourceListDelegate : NSOutlineViewDelegate
    {
        #region Private variables
        private SourceListView _controller;
        #endregion

        #region Constructors
        public SourceListDelegate (SourceListView controller)
        {
            // Initialize
            this._controller = controller;
        }
        #endregion

        #region Override Methods
        public override bool ShouldEditTableColumn (NSOutlineView outlineView, NSTableColumn tableColumn, Foundation.NSObject item)
        {
            return false;
        }

        public override NSCell GetCell (NSOutlineView outlineView, NSTableColumn tableColumn, Foundation.NSObject item)
        {
            nint row = outlineView.RowForItem (item);
            return tableColumn.DataCellForRow (row);
        }

        public override bool IsGroupItem (NSOutlineView outlineView, Foundation.NSObject item)
        {
            return ((SourceListItem)item).HasChildren;
        }

        public override NSView GetView (NSOutlineView outlineView, NSTableColumn tableColumn, NSObject item)
        {
            NSTableCellView view = null;

            // Is this a group item?
            if (((SourceListItem)item).HasChildren) {
                view = (NSTableCellView)outlineView.MakeView ("HeaderCell", this);
            } else {
                view = (NSTableCellView)outlineView.MakeView ("DataCell", this);
                view.ImageView.Image = ((SourceListItem)item).Icon;
            }

            // Initialize view
            view.TextField.StringValue = ((SourceListItem)item).Title;

            // Return new view
            return view;
        }

        public override bool ShouldSelectItem (NSOutlineView outlineView, Foundation.NSObject item)
        {
            return (outlineView.GetParent (item) != null);
        }

        public override void SelectionDidChange (NSNotification notification)
        {
            NSIndexSet selectedIndexes = _controller.SelectedRows;

            // More than one item selected?
            if (selectedIndexes.Count > 1) {
                // Not handling this case
            } else {
                // Grab the item
                var item = _controller.Data.ItemForRow ((int)selectedIndexes.FirstIndex);

                // Was an item found?
                if (item != null) {
                    // Fire the clicked event for the item
                    item.RaiseClickedEvent ();

                    // Inform caller of selection
                    _controller.RaiseItemSelected (item);
                }
            }
        }
        #endregion
    }
}
```

Isso fornecerá o comportamento da nossa lista de origem.

Por fim, na **Gerenciador de soluções**, clique com o botão direito do mouse no projeto e selecione **Adicionar**  >  **novo arquivo...** Selecione **General**  >  **classe vazia**geral, insira `SourceListView` para o **nome** e clique no botão **novo** . Faça com que o `SourceListView.cs` arquivo se pareça com o seguinte:

```csharp
using System;
using AppKit;
using Foundation;

namespace MacOutlines
{
    [Register("SourceListView")]
    public class SourceListView : NSOutlineView
    {
        #region Computed Properties
        public SourceListDataSource Data {
            get {return (SourceListDataSource)this.DataSource; }
        }
        #endregion

        #region Constructors
        public SourceListView ()
        {

        }

        public SourceListView (IntPtr handle) : base(handle)
        {

        }

        public SourceListView (NSCoder coder) : base(coder)
        {

        }

        public SourceListView (NSObjectFlag t) : base(t)
        {

        }
        #endregion

        #region Override Methods
        public override void AwakeFromNib ()
        {
            base.AwakeFromNib ();
        }
        #endregion

        #region Public Methods
        public void Initialize() {

            // Initialize this instance
            this.DataSource = new SourceListDataSource (this);
            this.Delegate = new SourceListDelegate (this);

        }
        
        public void AddItem(SourceListItem item) {
            if (Data != null) {
                Data.Items.Add (item);
            }
        }
        #endregion

        #region Events
        public delegate void ItemSelectedDelegate(SourceListItem item);
        public event ItemSelectedDelegate ItemSelected;

        internal void RaiseItemSelected(SourceListItem item) {
            // Inform caller
            if (this.ItemSelected != null) {
                this.ItemSelected (item);
            }
        }
        #endregion
    }
}
```

Isso cria uma subclasse personalizada e reutilizável de `NSOutlineView` ( `SourceListView` ) que podemos usar para direcionar a lista de origem em qualquer aplicativo Xamarin. Mac que criamos.

<a name="Creating_and_Maintaining_Source_Lists_in_Xcode"></a>

## <a name="creating-and-maintaining-source-lists-in-xcode"></a>Criando e mantendo listas de origem no Xcode

Agora, vamos projetar nossa lista de origem em Interface Builder. Clique duas vezes no `Main.storyboard` arquivo para abri-lo para edição no interface Builder e arraste um modo de exibição de divisão do **Inspetor de biblioteca**, adicione-o ao controlador de exibição e defina-o para redimensionar com o modo de exibição no **Editor de restrições**:

[![Restrições de edição](source-list-images/source00.png)](source-list-images/source00.png#lightbox)

Em seguida, arraste uma lista de origem do **Inspetor de biblioteca**, adicione-a à esquerda da exibição de divisão e defina-a para redimensionar com a exibição no editor de **restrições**:

[![Restrições de edição](source-list-images/source02.png)](source-list-images/source02.png#lightbox)

Em seguida, alterne para o **modo de exibição de identidade**, selecione a lista origem e altere a **classe** para `SourceListView` :

[![Definindo o nome da classe](source-list-images/source03.png)](source-list-images/source03.png#lightbox)

Por fim, crie uma **tomada** para nossa lista de origem chamada `SourceList` no `ViewController.h` arquivo:

[![Configurando uma tomada](source-list-images/source04.png)](source-list-images/source04.png#lightbox)

Salve as alterações e retorne ao Visual Studio para Mac para sincronizar com o Xcode.

<a name="Populating the Source List"></a>

## <a name="populating-the-source-list"></a>Populando a lista de origem

Vamos editar o `RotationWindow.cs` arquivo em Visual Studio para Mac e fazer com que `AwakeFromNib` o método seja semelhante ao seguinte:

```csharp
public override void AwakeFromNib ()
{
    base.AwakeFromNib ();

    // Populate source list
    SourceList.Initialize ();

    var library = new SourceListItem ("Library");
    library.AddItem ("Venues", "house.png", () => {
        Console.WriteLine("Venue Selected");
    });
    library.AddItem ("Singers", "group.png");
    library.AddItem ("Genre", "cards.png");
    library.AddItem ("Publishers", "box.png");
    library.AddItem ("Artist", "person.png");
    library.AddItem ("Music", "album.png");
    SourceList.AddItem (library);

    // Add Rotation 
    var rotation = new SourceListItem ("Rotation"); 
    rotation.AddItem ("View Rotation", "redo.png");
    SourceList.AddItem (rotation);

    // Add Kiosks
    var kiosks = new SourceListItem ("Kiosks");
    kiosks.AddItem ("Sign-in Station 1", "imac");
    kiosks.AddItem ("Sign-in Station 2", "ipad");
    SourceList.AddItem (kiosks);

    // Display side list
    SourceList.ReloadData ();
    SourceList.ExpandItem (null, true);

}
```

O `Initialize ()` método precisa ser chamado em relação à **tomada** da nossa lista de origem _antes que_ qualquer item seja adicionado a ela. Para cada grupo de itens, criamos um item pai e, em seguida, adicionamos os subitens a esse item de grupo. Em seguida, cada grupo é adicionado à coleção da lista de origem `SourceList.AddItem (...)` . As duas últimas linhas carregam os dados para a lista de origem e expandem todos os grupos:

```csharp
// Display side list
SourceList.ReloadData ();
SourceList.ExpandItem (null, true);
```

Por fim, edite o `AppDelegate.cs` arquivo e faça com que o `DidFinishLaunching` método fique semelhante ao seguinte:

```csharp
public override void DidFinishLaunching (NSNotification notification)
{
    mainWindowController = new MainWindowController ();
    mainWindowController.Window.MakeKeyAndOrderFront (this);

    var rotation = new RotationWindowController ();
    rotation.Window.MakeKeyAndOrderFront (this);
}
```

Se executarmos nosso aplicativo, o seguinte será exibido:

[![Uma execução de aplicativo de exemplo](source-list-images/source05.png)](source-list-images/source05.png#lightbox)

<a name="Summary"></a>

## <a name="summary"></a>Resumo

Este artigo deu uma visão detalhada de como trabalhar com listas de origem em um aplicativo Xamarin. Mac. Vimos como criar e manter listas de origem no Interface Builder do Xcode e como trabalhar com listas de origem em código C#.

## <a name="related-links"></a>Links Relacionados

- [MacOutlines (exemplo)](/samples/xamarin/mac-samples/macoutlines)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Exibições de tabela](~/mac/user-interface/table-view.md)
- [Exibições de estrutura de tópicos](~/mac/user-interface/outline-view.md)
- [Diretrizes de interface humana do OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)
- [Introdução aos modos de exibição de estrutura de tópicos](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/OutlineView/OutlineView.html#//apple_ref/doc/uid/10000023i)
- [NSOutlineView](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ApplicationKit/Classes/NSOutlineView_Class/index.html#//apple_ref/doc/uid/TP40004079)
- [NSOutlineViewDataSource](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ApplicationKit/Protocols/NSOutlineViewDataSource_Protocol/index.html#//apple_ref/doc/uid/TP40004175)
- [NSOutlineViewDelegate](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/NSOutlineViewDelegate_Protocol/index.html#//apple_ref/doc/uid/TP40008609)