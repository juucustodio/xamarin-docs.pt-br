---
title: Lista de origem
description: "Este artigo aborda a trabalhar com listas de origem em um aplicativo Xamarin.Mac. Ele descreve a criar e manter listas de origem em Xcode e construtor de Interface e interagir com eles no código c#."
ms.topic: article
ms.prod: xamarin
ms.assetid: 651A3649-5AA8-4133-94D6-4873D99F7FCC
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/14/2017
ms.openlocfilehash: 1fba2219a10a83043097d1b5ca6fb293de4d6c70
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2018
---
# <a name="source-lists"></a>Lista de origem

_Este artigo aborda a trabalhar com listas de origem em um aplicativo Xamarin.Mac. Ele descreve a criar e manter listas de origem em Xcode e construtor de Interface e interagir com eles no código c#._

Ao trabalhar com c# e .NET em um aplicativo de Xamarin.Mac, você tem acesso à mesma fonte de lista que um desenvolvedor trabalhando em *Objective-C* e *Xcode* does. Como Xamarin.Mac se integra diretamente com o Xcode, você pode usar do Xcode _Interface construtor_ para criar e manter sua lista de origem (ou, opcionalmente, criá-los diretamente no código do c#).

Uma lista de origem é um tipo especial de exibição de estrutura de tópicos usada para mostrar a origem de uma ação, como a barra lateral no localizador ou iTunes.

[![](source-list-images/source05.png "Uma lista de origem de exemplo")](source-list-images/source05.png#lightbox)

Neste artigo, vamos abordar os fundamentos de trabalhar com listas de origem em um aplicativo Xamarin.Mac. É altamente recomendável que você leia o [Hello, Mac](~/mac/get-started/hello-mac.md) artigo primeiro, especificamente o [Introdução ao construtor da Interface e Xcode](~/mac/get-started/hello-mac.md#Introduction_to_Xcode_and_Interface_Builder) e [tomadas e ações](~/mac/get-started/hello-mac.md#Outlets_and_Actions) seções, como ele aborda os principais conceitos e técnicas que será usado neste artigo.

Talvez você queira dar uma olhada a [classes expondo c# / métodos para Objective-C](~/mac/internals/how-it-works.md) seção o [Xamarin.Mac internos](~/mac/internals/how-it-works.md) de documento, ele explica o `Register` e `Export` comandos usado com a transmissão suas classes c# objetos Objective-C e elementos de interface do usuário.

<a name="Introduction_to_Outline_Views" />

## <a name="introduction-to-source-lists"></a>Introdução à lista de origem

Como mencionado acima, uma lista de origem é usado um tipo especial de estrutura de tópicos para mostrar a origem de uma ação, como a barra lateral no localizador ou iTunes. Uma lista de origem é um tipo de tabela que permite que o usuário expandir ou recolher linhas de dados hierárquicos. Ao contrário de um modo de exibição de tabela, itens em uma lista de origem não estão em uma lista simples, eles são organizados em uma hierarquia, como arquivos e pastas em um disco rígido. Se um item em uma lista de origem contém outros itens, ele pode ser expandido ou recolhido pelo usuário.

A lista de origem é um modo de exibição de estrutura de tópicos com estilo especialmente (`NSOutlineView`), que por si só é uma subclasse da exibição de tabela (`NSTableView`) e como tal, herda muito de seu comportamento de sua classe pai. Como resultado, muitas operações com suporte por uma exibição de estrutura de tópicos, também têm suporte por uma lista de origem. Um aplicativo Xamarin.Mac tem controle sobre esses recursos e pode configurar os parâmetros de origem da lista (seja em código ou o construtor de Interface) para permitir ou impedir que determinadas operações.

Uma lista de origem não armazenar os dados de sua própria, em vez disso, ele se baseia em uma fonte de dados (`NSOutlineViewDataSource`) para fornecer linhas e colunas necessárias em uma base conforme necessário.

Comportamento de uma lista de origem pode ser personalizado, fornecendo uma subclasse do representante de exibição de estrutura de tópicos (`NSOutlineViewDelegate`) para dar suporte a tipo de estrutura de tópicos para selecionar a funcionalidade, item de seleção e editar, rastreamento personalizados e modos de exibição personalizados para itens individuais.

Como uma lista de origem compartilha grande parte da funcionalidade e seu comportamento com uma exibição de tabela e uma exibição de estrutura de tópicos, talvez você queira percorrer nosso [modos de exibição de tabela](~/mac/user-interface/table-view.md) e [modos de exibição de estrutura de tópicos](~/mac/user-interface/outline-view.md) documentação antes de continuar Este artigo.

<a name="Working_with_Source_Lists" />

## <a name="working-with-source-lists"></a>Trabalhando com listas de origem

Uma lista de origem é um tipo especial de exibição de estrutura de tópicos usada para mostrar a origem de uma ação, como a barra lateral no localizador ou iTunes. Ao contrário de modos de exibição de estrutura de tópicos, antes que definimos nossa lista de origem no construtor de Interface, vamos criar as classes de backup em Xamarin.Mac.

Primeiro, vamos criar um novo `SourceListItem` classe para manter os dados de nossa lista de origem. No **Solution Explorer**, clique com o botão direito e selecione **adicionar** > **novo arquivo...** Selecione **geral** > **classe vazia**, digite `SourceListItem` para o **nome** e clique no **novo** botão:

[![](source-list-images/source01.png "Adicionando uma classe vazia")](source-list-images/source01.png#lightbox)

Verifique o `SourceListItem.cs` arquivo aparência semelhante ao seguinte: 

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

No **Solution Explorer**, clique com o botão direito e selecione **adicionar** > **novo arquivo...** Selecione **geral** > **classe vazia**, digite `SourceListDataSource` para o **nome** e clique no **novo** botão. Verifique o `SourceListDataSource.cs` arquivo aparência semelhante ao seguinte:

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

Isso fornecerá os dados de nossa lista de origem.

No **Solution Explorer**, clique com o botão direito e selecione **adicionar** > **novo arquivo...** Selecione **geral** > **classe vazia**, digite `SourceListDelegate` para o **nome** e clique no **novo** botão. Verifique o `SourceListDelegate.cs` arquivo aparência semelhante ao seguinte:

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

Isso fornecerá o comportamento de nossa lista de origem.

Por fim, no **Solution Explorer**, clique com o botão direito e selecione **adicionar** > **novo arquivo...** Selecione **geral** > **classe vazia**, digite `SourceListView` para o **nome** e clique no **novo** botão. Verifique o `SourceListView.cs` arquivo aparência semelhante ao seguinte:

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

Isso cria uma subclasse personalizada, reutilizável de `NSOutlineView` (`SourceListView`) que podemos usar para orientar a lista de origem em qualquer aplicativo Xamarin.Mac que fazemos.

<a name="Creating_and_Maintaining_Source_Lists_in_Xcode" />

## <a name="creating-and-maintaining-source-lists-in-xcode"></a>Criar e manter listas de origem no Xcode

Agora, vamos criar nossa lista de origem no construtor de Interface. Clique duas vezes no `Main.storyboard` arquivo para abri-lo para edição no construtor de Interface e arraste uma exibição da divisão do **Inspetor de biblioteca**, adicioná-lo ao controlador de exibição e defina-a para redimensionar com o modo de exibição de **Constraints Editor** :

[![](source-list-images/source00.png "Restrições de edição")](source-list-images/source00.png#lightbox)

Em seguida, arraste uma lista de origem do **Inspetor de biblioteca**, adicioná-lo para o lado esquerdo do modo divisão e defini-lo para redimensionar com o modo de exibição de **Constraints Editor**:

[![](source-list-images/source02.png "Restrições de edição")](source-list-images/source02.png#lightbox)

Em seguida, alterne para o **exibição identidade**, selecione a lista de origem e alterá-la do **classe** para `SourceListView`:

[![](source-list-images/source03.png "Configurar o nome de classe")](source-list-images/source03.png#lightbox)

Finalmente, crie um **tomada** para chamada de nossa lista de origem `SourceList` no `ViewController.h` arquivo:

[![](source-list-images/source04.png "Configurando uma tomada")](source-list-images/source04.png#lightbox)

Salve suas alterações e retornar ao Visual Studio para Mac sincronizar com o Xcode.

<a name="Populating the Source List" />

## <a name="populating-the-source-list"></a>Preenchimento da lista de origem

Vamos editar o `RotationWindow.cs` de arquivo no Visual Studio para Mac e torná-lo do `AwakeFromNib` método aparência semelhante ao seguinte:

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

O `Initialize ()` método precisa ser chamado em nossa lista de origem **tomada** _antes de_ todos os itens são adicionados a ela. Para cada grupo de itens, podemos criar um item pai e, em seguida, adicionar os subitens para esse item de grupo. Cada grupo é adicionado à coleção da lista de origem `SourceList.AddItem (...)`. As duas últimas linhas carregar os dados para a lista de origem e expande todos os grupos:

```csharp
// Display side list
SourceList.ReloadData ();
SourceList.ExpandItem (null, true);
```

Por fim, edite o `AppDelegate.cs` de arquivo e verifique o `DidFinishLaunching` método aparência semelhante ao seguinte:

```csharp
public override void DidFinishLaunching (NSNotification notification)
{
    mainWindowController = new MainWindowController ();
    mainWindowController.Window.MakeKeyAndOrderFront (this);

    var rotation = new RotationWindowController ();
    rotation.Window.MakeKeyAndOrderFront (this);
}
```

Se executarmos nosso aplicativo, a seguinte mensagem será exibida:

[![](source-list-images/source05.png "Executar um aplicativo de exemplo")](source-list-images/source05.png#lightbox)

<a name="Summary" />

## <a name="summary"></a>Resumo

Este artigo obteve uma visão detalhada de como trabalhar com a lista de origem em um aplicativo Xamarin.Mac. Vimos como criar e manter a lista de origem no construtor de Interface do Xcode e como trabalhar com listas de origem no código c#.

## <a name="related-links"></a>Links relacionados

- [MacOutlines (exemplo)](https://developer.xamarin.com/samples/mac/MacOutlines/)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Modos de exibição de tabela](~/mac/user-interface/table-view.md)
- [Modos de exibição de estrutura de tópicos](~/mac/user-interface/outline-view.md)
- [Diretrizes de interface humana do OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)
- [Introdução aos modos de exibição de estrutura de tópicos](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/OutlineView/OutlineView.html#//apple_ref/doc/uid/10000023i)
- [NSOutlineView](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ApplicationKit/Classes/NSOutlineView_Class/index.html#//apple_ref/doc/uid/TP40004079)
- [NSOutlineViewDataSource](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ApplicationKit/Protocols/NSOutlineViewDataSource_Protocol/index.html#//apple_ref/doc/uid/TP40004175)
- [NSOutlineViewDelegate](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/NSOutlineViewDelegate_Protocol/index.html#//apple_ref/doc/uid/TP40008609)
