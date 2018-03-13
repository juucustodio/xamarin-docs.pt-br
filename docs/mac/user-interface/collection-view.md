---
title: "Exibições de coleção"
description: "Este artigo descreve o funcionamento com exibições de coleção em um aplicativo Xamarin.Mac. Ele abrange a criar e manter as exibições de coleção no Xcode e o construtor de Interface e trabalhar com eles por meio de programação."
ms.topic: article
ms.prod: xamarin
ms.assetid: 6EE32256-5948-4AE4-8133-6D0B3F4173E8
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 05/24/2017
ms.openlocfilehash: 9aa66a531b723f176b940ba35ee4e86eae711f7d
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2018
---
# <a name="collection-views"></a>Exibições de coleção

_Este artigo descreve o funcionamento com exibições de coleção em um aplicativo Xamarin.Mac. Ele abrange a criar e manter as exibições de coleção no Xcode e o construtor de Interface e trabalhar com eles por meio de programação._

Ao trabalhar com c# e .NET em um aplicativo de Xamarin.Mac, o desenvolvedor tenha acesso à mesma exibição de coleção AppKit controla a que um desenvolvedor trabalhando em *Objective-C* e *Xcode* does. Como Xamarin.Mac se integra diretamente com o Xcode, o desenvolvedor usa do Xcode _Interface construtor_ para criar e manter as exibições de coleção.

Um `NSCollectionView` exibe uma grade de sub-visualizações organizadas usando um `NSCollectionViewLayout`. Cada modo de exibição secundário na grade é representado por um `NSCollectionViewItem` que gerencia o carregamento do conteúdo do modo de exibição de um `.xib` arquivo.

[![Executar um aplicativo de exemplo](collection-view-images/intro01.png)](collection-view-images/intro01.png#lightbox)

Este artigo aborda os fundamentos de trabalhar com exibições de coleção em um aplicativo Xamarin.Mac. É altamente recomendável que você leia o [Hello, Mac](~/mac/get-started/hello-mac.md) artigo primeiro, especificamente o [Introdução ao construtor da Interface e Xcode](~/mac/get-started/hello-mac.md#Introduction_to_Xcode_and_Interface_Builder) e [tomadas e ações](~/mac/get-started/hello-mac.md#Outlets_and_Actions) seções, como ele aborda os principais conceitos e técnicas que são usadas neste artigo.

Talvez você queira dar uma olhada a [classes expondo c# / métodos para Objective-C](~/mac/internals/how-it-works.md) seção o [Xamarin.Mac internos](~/mac/internals/how-it-works.md) de documento, ele explica o `Register` e `Export` comandos usado com a transmissão suas classes c# objetos Objective-C e elementos de interface do usuário.

<a name="About_Collection_Views"/>

## <a name="about-collection-views"></a>Sobre exibições de coleção

O objetivo principal de uma exibição de coleção (`NSCollectionView`) é organizar visualmente um grupo de objetos de forma organizada usando um Layout de exibição de coleção (`NSCollectionViewLayout`), com cada objeto individual (`NSCollectionViewItem`) ao obter sua própria exibição da coleção maior. Exibições de coleção de trabalho por meio de técnicas de codificação de chave-valor e associação de dados e como tal, você deve ler o [associação de dados e a codificação de chave-valor](~/mac/app-fundamentals/databinding.md) documentação antes de continuar com este artigo.

A exibição de coleção não tem nenhum padrão e internos exibição de Item de coleta (como uma estrutura de tópicos ou o modo de exibição de tabela), para que o desenvolvedor é responsável por projetar e implementar um _exibição protótipo_ usando outros controles AppKit como campos de imagem , Campos de texto, rótulos, etc. Este modo de exibição de protótipo será usado para exibir e trabalhar com cada item que está sendo gerenciado pelo modo de exibição de coleção e é armazenado em um `.xib` arquivo.

Como o desenvolvedor é responsável pela aparência de um Item de exibição de coleção, o modo de exibição de coleção tem suporte interno para realçar um item selecionado na grade. Implementar esse recurso será abordado neste artigo.

<a name="Defining_your_Data_Model"/>

## <a name="defining-the-data-model"></a>Definindo o modelo de dados

Antes de uma exibição de coleção de associação no construtor de Interface, um valor de chave de codificação (KVC) de dados / classe compatível de chave-valor observando (KVO) deve ser definida no aplicativo Xamarin.Mac para atuar como o _modelo de dados_ para a associação. O modelo de dados fornece todos os dados que serão exibidos na coleção e recebe todas as modificações de dados que o usuário faz na interface do usuário ao executar o aplicativo.

Veja o exemplo de um aplicativo que gerencia um grupo de funcionários, a seguinte classe pode ser usada para definir o modelo de dados:

```csharp
using System;
using Foundation;
using AppKit;

namespace MacDatabinding
{
    [Register("PersonModel")]
    public class PersonModel : NSObject
    {
        #region Private Variables
        private string _name = "";
        private string _occupation = "";
        private bool _isManager = false;
        private NSMutableArray _people = new NSMutableArray();
        #endregion

        #region Computed Properties
        [Export("Name")]
        public string Name {
            get { return _name; }
            set {
                WillChangeValue ("Name");
                _name = value;
                DidChangeValue ("Name");
            }
        }

        [Export("Occupation")]
        public string Occupation {
            get { return _occupation; }
            set {
                WillChangeValue ("Occupation");
                _occupation = value;
                DidChangeValue ("Occupation");
            }
        }

        [Export("isManager")]
        public bool isManager {
            get { return _isManager; }
            set {
                WillChangeValue ("isManager");
                WillChangeValue ("Icon");
                _isManager = value;
                DidChangeValue ("isManager");
                DidChangeValue ("Icon");
            }
        }

        [Export("isEmployee")]
        public bool isEmployee {
            get { return (NumberOfEmployees == 0); }
        }

        [Export("Icon")]
        public NSImage Icon
        {
            get
            {
                if (isManager)
                {
                    return NSImage.ImageNamed("IconGroup");
                }
                else
                {
                    return NSImage.ImageNamed("IconUser");
                }
            }
        }

        [Export("personModelArray")]
        public NSArray People {
            get { return _people; }
        }

        [Export("NumberOfEmployees")]
        public nint NumberOfEmployees {
            get { return (nint)_people.Count; }
        }
        #endregion

        #region Constructors
        public PersonModel ()
        {
        }

        public PersonModel (string name, string occupation)
        {
            // Initialize
            this.Name = name;
            this.Occupation = occupation;
        }

        public PersonModel (string name, string occupation, bool manager)
        {
            // Initialize
            this.Name = name;
            this.Occupation = occupation;
            this.isManager = manager;
        }
        #endregion

        #region Array Controller Methods
        [Export("addObject:")]
        public void AddPerson(PersonModel person) {
            WillChangeValue ("personModelArray");
            isManager = true;
            _people.Add (person);
            DidChangeValue ("personModelArray");
        }

        [Export("insertObject:inPersonModelArrayAtIndex:")]
        public void InsertPerson(PersonModel person, nint index) {
            WillChangeValue ("personModelArray");
            _people.Insert (person, index);
            DidChangeValue ("personModelArray");
        }

        [Export("removeObjectFromPersonModelArrayAtIndex:")]
        public void RemovePerson(nint index) {
            WillChangeValue ("personModelArray");
            _people.RemoveObject (index);
            DidChangeValue ("personModelArray");
        }

        [Export("setPersonModelArray:")]
        public void SetPeople(NSMutableArray array) {
            WillChangeValue ("personModelArray");
            _people = array;
            DidChangeValue ("personModelArray");
        }
        #endregion
    }
}
```

O `PersonModel` modelo de dados será usado em todo o restante deste artigo.

<a name="Working_with_a_Collection_View"/>

## <a name="working-with-a-collection-view"></a>Trabalhando com uma exibição de coleção

Associação de dados com uma exibição de coleção é muito associação like com uma exibição de tabela, como `NSCollectionViewDataSource` é usado para fornecer dados para a coleção. Como o modo de exibição de coleção não tem um formato de exibição predefinido, mais trabalho é necessário para fornecer comentários de interação do usuário e controlar a seleção do usuário.

<a name="Creating-the-Cell-Prototype"/>

### <a name="creating-the-cell-prototype"></a>Criando o protótipo de célula

Como o modo de exibição de coleção não incluem um protótipo de célula padrão, o desenvolvedor precisa adicionar um ou mais `.xib` arquivos para o aplicativo Xamarin.Mac para definir o layout e o conteúdo das células individuais.

Faça o seguinte:

1. No **Solution Explorer**, com o botão direito no nome do projeto e selecione **adicionar** > **novo arquivo...**
2. Selecione **Mac** > **View Controller**, dê a ele um nome (como `EmployeeItem` neste exemplo) e clique no **novo** botão Criar: 

    ![Adicionar um novo controlador de exibição](collection-view-images/proto01.png)

    Isso adicionará uma `EmployeeItem.cs`, `EmployeeItemController.cs` e `EmployeeItemController.xib` arquivo à solução do projeto.
3. Clique duas vezes o `EmployeeItemController.xib` arquivo para abri-lo para edição no construtor de Interface do Xcode.
4. Adicionar uma `NSBox`, `NSImageView` e dois `NSLabel` controles para o modo de exibição e definir o layout da seguinte maneira:

    ![Criando o layout do protótipo de célula](collection-view-images/proto02.png)
5. Abra o **Assistente Editor** e criar um **tomada** para o `NSBox` para que ele pode ser usado para indicar o estado de seleção de uma célula:

    ![Expondo NSBox em uma tomada](collection-view-images/proto03.png)
6. Volte para o **Editor padrão** e selecione a exibição da imagem.
7. No **Inspetor associação**, selecione **ligar a** > **proprietário do arquivo** e insira um **caminho de chave do modelo** de `self.Person.Icon`:

    ![O ícone de associação](collection-view-images/proto04.png)
8. Selecione o primeiro rótulo e, no **Inspetor associação**, selecione **ligar a** > **proprietário do arquivo** e insira um **caminho de chave do modelo**de `self.Person.Name`:

    ![O nome de associação](collection-view-images/proto05.png)
9. Selecione o rótulo de segundo e no **Inspetor associação**, selecione **ligar a** > **proprietário do arquivo** e insira um **caminho de chave do modelo**de `self.Person.Occupation`:

    ![Associando a ocupação](collection-view-images/proto06.png)
10. Salvar as alterações para o `.xib` de arquivo e retornar ao Visual Studio para sincronizar as alterações.

Editar o `EmployeeItemController.cs` de arquivo e torná-lo a aparência a seguir:

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using Foundation;
using AppKit;

namespace MacCollectionNew
{
    /// <summary>
    /// The Employee item controller handles the display of the individual items that will
    /// be displayed in the collection view as defined in the associated .XIB file.
    /// </summary>
    public partial class EmployeeItemController : NSCollectionViewItem
    {
        #region Private Variables
        /// <summary>
        /// The person that will be displayed.
        /// </summary>
        private PersonModel _person;
        #endregion

        #region Computed Properties
        // strongly typed view accessor
        public new EmployeeItem View
        {
            get
            {
                return (EmployeeItem)base.View;
            }
        }

        /// <summary>
        /// Gets or sets the person.
        /// </summary>
        /// <value>The person that this item belongs to.</value>
        [Export("Person")]
        public PersonModel Person
        {
            get { return _person; }
            set
            {
                WillChangeValue("Person");
                _person = value;
                DidChangeValue("Person");
            }
        }

        /// <summary>
        /// Gets or sets the color of the background for the item.
        /// </summary>
        /// <value>The color of the background.</value>
        public NSColor BackgroundColor {
            get { return Background.FillColor; }
            set { Background.FillColor = value; }
        }

        /// <summary>
        /// Gets or sets a value indicating whether this <see cref="T:MacCollectionNew.EmployeeItemController"/> is selected.
        /// </summary>
        /// <value><c>true</c> if selected; otherwise, <c>false</c>.</value>
        /// <remarks>This also changes the background color based on the selected state
        /// of the item.</remarks>
        public override bool Selected
        {
            get
            {
                return base.Selected;
            }
            set
            {
                base.Selected = value;

                // Set background color based on the selection state
                if (value) {
                    BackgroundColor = NSColor.DarkGray;
                } else {
                    BackgroundColor = NSColor.LightGray;
                }
            }
        }
        #endregion

        #region Constructors
        // Called when created from unmanaged code
        public EmployeeItemController(IntPtr handle) : base(handle)
        {
            Initialize();
        }

        // Called when created directly from a XIB file
        [Export("initWithCoder:")]
        public EmployeeItemController(NSCoder coder) : base(coder)
        {
            Initialize();
        }

        // Call to load from the XIB/NIB file
        public EmployeeItemController() : base("EmployeeItem", NSBundle.MainBundle)
        {
            Initialize();
        }

        // Added to support loading from XIB/NIB
        public EmployeeItemController(string nibName, NSBundle nibBundle) : base(nibName, nibBundle) {

            Initialize();
        }

        // Shared initialization code
        void Initialize()
        {
        }
        #endregion
    }
}
```

Examinar este código detalhadamente, a classe herda de `NSCollectionViewItem` para ele possa agir como um protótipo para uma célula de exibição de coleção. O `Person` propriedade expõe a classe que foi usado para associação de dados para o modo de exibição de imagem e rótulos no Xcode. Essa é uma instância do `PersonModel` criado acima.

O `BackgroundColor` propriedade é um atalho para o `NSBox` do controle `FillColor` que será usado para mostrar o status de seleção de uma célula. Substituindo o `Selected` propriedade o `NSCollectionViewItem`, o código a seguir define ou limpa esse estado de seleção:

```csharp
public override bool Selected
{
    get
    {
        return base.Selected;
    }
    set
    {
        base.Selected = value;

        // Set background color based on the selection state
        if (value) {
            BackgroundColor = NSColor.DarkGray;
        } else {
            BackgroundColor = NSColor.LightGray;
        }
    }
}
```

<a name="Creating-the-Collection-View-Data-Source"/>

### <a name="creating-the-collection-view-data-source"></a>Criando a fonte de dados de exibição de coleção

Uma fonte de dados de exibição de coleção (`NSCollectionViewDataSource`) fornece todos os dados para uma exibição de coleção e cria e preenche uma célula de exibição de coleção (usando o `.xib` protótipo) conforme necessário para cada item na coleção.

Adicionar uma nova classe do projeto, chamá-lo `CollectionViewDataSource` e torná-lo a aparência a seguir:

```csharp
using System;
using System.Collections.Generic;
using AppKit;
using Foundation;

namespace MacCollectionNew
{
    /// <summary>
    /// Collection view data source provides the data for the collection view.
    /// </summary>
    public class CollectionViewDataSource : NSCollectionViewDataSource
    {
        #region Computed Properties
        /// <summary>
        /// Gets or sets the parent collection view.
        /// </summary>
        /// <value>The parent collection view.</value>
        public NSCollectionView ParentCollectionView { get; set; }

        /// <summary>
        /// Gets or sets the data that will be displayed in the collection.
        /// </summary>
        /// <value>A collection of PersonModel objects.</value>
        public List<PersonModel> Data { get; set; } = new List<PersonModel>();
        #endregion

        #region Constructors
        /// <summary>
        /// Initializes a new instance of the <see cref="T:MacCollectionNew.CollectionViewDataSource"/> class.
        /// </summary>
        /// <param name="parent">The parent collection that this datasource will provide data for.</param>
        public CollectionViewDataSource(NSCollectionView parent)
        {
            // Initialize
            ParentCollectionView = parent;

            // Attach to collection view
            parent.DataSource = this;

        }
        #endregion

        #region Override Methods
        /// <summary>
        /// Gets the number of sections.
        /// </summary>
        /// <returns>The number of sections.</returns>
        /// <param name="collectionView">The parent Collection view.</param>
        public override nint GetNumberOfSections(NSCollectionView collectionView)
        {
            // There is only one section in this view
            return 1;
        }

        /// <summary>
        /// Gets the number of items in the given section.
        /// </summary>
        /// <returns>The number of items.</returns>
        /// <param name="collectionView">The parent Collection view.</param>
        /// <param name="section">The Section number to count items for.</param>
        public override nint GetNumberofItems(NSCollectionView collectionView, nint section)
        {
            // Return the number of items
            return Data.Count;
        }

        /// <summary>
        /// Gets the item for the give section and item index.
        /// </summary>
        /// <returns>The item.</returns>
        /// <param name="collectionView">The parent Collection view.</param>
        /// <param name="indexPath">Index path specifying the section and index.</param>
        public override NSCollectionViewItem GetItem(NSCollectionView collectionView, NSIndexPath indexPath)
        {
            var item = collectionView.MakeItem("EmployeeCell", indexPath) as EmployeeItemController;
            item.Person = Data[(int)indexPath.Item];

            return item;
        }
        #endregion
    }
}
```

Examinar este código detalhadamente, a classe herda de `NSCollectionViewDataSource` e expõe uma lista de `PersonModel` instâncias por meio de seu `Data` propriedade.

Como essa coleção tem apenas uma seção, o código substitui o `GetNumberOfSections` método e sempre retorna `1`. Além disso, o `GetNumberofItems` método é substituído no retorna o número de itens em de `Data` lista de propriedades.

O `GetItem` método é chamado sempre que uma nova célula é necessária e é semelhante ao seguinte:

```csharp
public override NSCollectionViewItem GetItem(NSCollectionView collectionView, NSIndexPath indexPath)
{
    var item = collectionView.MakeItem("EmployeeCell", indexPath) as EmployeeItemController;
    item.Person = Data[(int)indexPath.Item];

    return item;
}
```

O `MakeItem` método da exibição de coleção é chamado para criar ou retornar uma instância reutilizável do `EmployeeItemController` e sua `Person` está definida como item que está sendo exibido na célula solicitada. 

O `EmployeeItemController` deve ser registrado com o controlador de exibição de coleção antes de usar o código a seguir:

```csharp
EmployeeCollection.RegisterClassForItem(typeof(EmployeeItemController), "EmployeeCell");
``` 

O **identificador** (`EmployeeCell`) usado no `MakeItem` chamar _deve_ corresponde ao nome do controlador de exibição que foi registrado com o modo de exibição de coleção. Esta etapa será abordada em detalhes abaixo.

<a name="Handling-Item-Selection"/>

### <a name="handling-item-selection"></a>Manipulação da seleção de item

Para lidar com a seleção e deselection de itens na coleção, um `NSCollectionViewDelegate` será necessária. Como este exemplo usará interna no `NSCollectionViewFlowLayout` tipo de layout, um `NSCollectionViewDelegateFlowLayout` versão específica deste delegado será necessária.

Adicionar uma nova classe ao projeto, chamá-lo `CollectionViewDelegate` e torná-lo a aparência a seguir:

```csharp
using System;
using Foundation;
using AppKit;

namespace MacCollectionNew
{
    /// <summary>
    /// Collection view delegate handles user interaction with the elements of the 
    /// collection view for the Flow-Based layout type.
    /// </summary>
    public class CollectionViewDelegate : NSCollectionViewDelegateFlowLayout
    {
        #region Computed Properties
        /// <summary>
        /// Gets or sets the parent view controller.
        /// </summary>
        /// <value>The parent view controller.</value>
        public ViewController ParentViewController { get; set; }
        #endregion

        #region Constructors
        /// <summary>
        /// Initializes a new instance of the <see cref="T:MacCollectionNew.CollectionViewDelegate"/> class.
        /// </summary>
        /// <param name="parentViewController">Parent view controller.</param>
        public CollectionViewDelegate(ViewController parentViewController)
        {
            // Initialize
            ParentViewController = parentViewController;
        }
        #endregion

        #region Override Methods
        /// <summary>
        /// Handles one or more items being selected.
        /// </summary>
        /// <param name="collectionView">The parent Collection view.</param>
        /// <param name="indexPaths">The Index paths of the items being selected.</param>
        public override void ItemsSelected(NSCollectionView collectionView, NSSet indexPaths)
        {
            // Dereference path
            var paths = indexPaths.ToArray<NSIndexPath>();
            var index = (int)paths[0].Item;

            // Save the selected item
            ParentViewController.PersonSelected = ParentViewController.Datasource.Data[index];

        }

        /// <summary>
        /// Handles one or more items being deselected.
        /// </summary>
        /// <param name="collectionView">The parent Collection view.</param>
        /// <param name="indexPaths">The Index paths of the items being deselected.</param>
        public override void ItemsDeselected(NSCollectionView collectionView, NSSet indexPaths)
        {
            // Dereference path
            var paths = indexPaths.ToArray<NSIndexPath>();
            var index = paths[0].Item;

            // Clear selection
            ParentViewController.PersonSelected = null;
        }
        #endregion
    }
}
``` 

O `ItemsSelected` e `ItemsDeselected` métodos são substituídos e usados para definir ou limpar o `PersonSelected` propriedade do controlador de exibição que está tratando a exibição de coleção quando o usuário seleciona ou anula a seleção de um item. Isso será mostrado em detalhes abaixo.

<a name="Creating-the-Collection-View-in-Interface-Builder"/>

### <a name="creating-the-collection-view-in-interface-builder"></a>Criar o modo de exibição de coleção no construtor de Interface

Com todas as partes de suporte necessárias em vigor, o storyboard principal pode ser editado e uma exibição de coleção adicionada a ele.

Faça o seguinte:

1. Clique duas vezes o `Main.Storyboard` de arquivos no **Gerenciador de soluções** para abri-lo para edição no Xcode do construtor de Interface.
2. Arraste uma exibição de coleção para o modo de exibição principal e redimensioná-la para preencher o modo de exibição:

    ![Adicionando uma exibição de coleção para o layout](collection-view-images/collection01.png)
3. Com o modo de exibição de coleção selecionado, use o Editor de restrição para fixá-lo no modo de exibição quando ele for redimensionado:

    ![Adicionar restrições](collection-view-images/collection02.png)
4. Certifique-se de que o modo de exibição de coleção é selecionado no **superfície de Design** (e não o **exibição com borda de rolagem** ou **Clip exibição** que o contém), alterne para o  **Assistente de Editor** e criar um **tomada** para a exibição de coleção:

    ![Adicionar restrições](collection-view-images/collection03.png)
5. Salve as alterações e retornar ao Visual Studio para sincronizar.

<a name="Bringing-it-all-Together"/>

## <a name="bringing-it-all-together"></a>Juntando tudo junto

Todas as partes suporte agora foi colocadas no local com uma classe para atuar como o modelo de dados (`PersonModel`), um `NSCollectionViewDataSource` foi adicionado à fonte de dados, um `NSCollectionViewDelegateFlowLayout` foi criado para lidar com a seleção de item e um `NSCollectionView` foi adicionado para o Storyboard principal e exposto como uma tomada (`EmployeeCollection`).

A etapa final será editar o controlador de exibição que contém a exibição de coleção e colocar todas as partes juntas para preencher a coleção e lidar com a seleção de item.

Editar o `ViewController.cs` de arquivo e torná-lo a aparência a seguir:

```csharp
using System;
using AppKit;
using Foundation;
using CoreGraphics;

namespace MacCollectionNew
{
    /// <summary>
    /// The View controller controls the main view that houses the Collection View.
    /// </summary>
    public partial class ViewController : NSViewController
    {
        #region Private Variables
        private PersonModel _personSelected;
        private bool shouldEdit = true;
        #endregion

        #region Computed Properties
        /// <summary>
        /// Gets or sets the datasource that provides the data to display in the 
        /// Collection View.
        /// </summary>
        /// <value>The datasource.</value>
        public CollectionViewDataSource Datasource { get; set; }

        /// <summary>
        /// Gets or sets the person currently selected in the collection view.
        /// </summary>
        /// <value>The person selected or <c>null</c> if no person is selected.</value>
        [Export("PersonSelected")]
        public PersonModel PersonSelected
        {
            get { return _personSelected; }
            set
            {
                WillChangeValue("PersonSelected");
                _personSelected = value;
                DidChangeValue("PersonSelected");
                RaiseSelectionChanged();
            }
        }
        #endregion

        #region Constructors
        /// <summary>
        /// Initializes a new instance of the <see cref="T:MacCollectionNew.ViewController"/> class.
        /// </summary>
        /// <param name="handle">Handle.</param>
        public ViewController(IntPtr handle) : base(handle)
        {
        }
        #endregion

        #region Override Methods
        /// <summary>
        /// Called after the view has finished loading from the Storyboard to allow it to
        /// be configured before displaying to the user.
        /// </summary>
        public override void ViewDidLoad()
        {
            base.ViewDidLoad();

            // Initialize Collection View
            ConfigureCollectionView();
            PopulateWithData();
        }
        #endregion

        #region Private Methods
        /// <summary>
        /// Configures the collection view.
        /// </summary>
        private void ConfigureCollectionView()
        {
            EmployeeCollection.RegisterClassForItem(typeof(EmployeeItemController), "EmployeeCell");

            // Create a flow layout
            var flowLayout = new NSCollectionViewFlowLayout()
            {
                ItemSize = new CGSize(150, 150),
                SectionInset = new NSEdgeInsets(10, 10, 10, 20),
                MinimumInteritemSpacing = 10,
                MinimumLineSpacing = 10
            };
            EmployeeCollection.WantsLayer = true;

            // Setup collection view
            EmployeeCollection.CollectionViewLayout = flowLayout;
            EmployeeCollection.Delegate = new CollectionViewDelegate(this);

        }

        /// <summary>
        /// Populates the Datasource with data and attaches it to the collection view.
        /// </summary>
        private void PopulateWithData()
        {
            // Make datasource
            Datasource = new CollectionViewDataSource(EmployeeCollection);

            // Build list of employees
            Datasource.Data.Add(new PersonModel("Craig Dunn", "Documentation Manager", true));
            Datasource.Data.Add(new PersonModel("Amy Burns", "Technical Writer"));
            Datasource.Data.Add(new PersonModel("Joel Martinez", "Web & Infrastructure"));
            Datasource.Data.Add(new PersonModel("Kevin Mullins", "Technical Writer"));
            Datasource.Data.Add(new PersonModel("Mark McLemore", "Technical Writer"));
            Datasource.Data.Add(new PersonModel("Tom Opgenorth", "Technical Writer"));
            Datasource.Data.Add(new PersonModel("Larry O'Brien", "API Docs Manager", true));
            Datasource.Data.Add(new PersonModel("Mike Norman", "API Documentor"));

            // Populate collection view
            EmployeeCollection.ReloadData();
        }
        #endregion

        #region Events
        /// <summary>
        /// Selection changed delegate.
        /// </summary>
        public delegate void SelectionChangedDelegate();

        /// <summary>
        /// Occurs when selection changed.
        /// </summary>
        public event SelectionChangedDelegate SelectionChanged;

        /// <summary>
        /// Raises the selection changed event.
        /// </summary>
        internal void RaiseSelectionChanged() {
            // Inform caller
            if (this.SelectionChanged != null) SelectionChanged();
        }
        #endregion
    }
}
```

Observando esse código detalhadamente, um `Datasource` propriedade é definida para manter uma instância das `CollectionViewDataSource` que fornecerá os dados para a exibição de coleção. Um `PersonSelected` propriedade está definida para conter o `PersonModel` que representa o item atualmente selecionado na exibição de coleção. Essa propriedade também gera o `SelectionChanged` evento quando a seleção é alterada.

O `ConfigureCollectionView` classe é usada para registrar o controlador de exibição que atua como o protótipo de célula com a exibição de coleção usando a seguinte linha:

```csharp
EmployeeCollection.RegisterClassForItem(typeof(EmployeeItemController), "EmployeeCell");
```

Observe que o **identificador** (`EmployeeCell`) usado para registrar as correspondências de protótipo de uma chamada no `GetItem` método o `CollectionViewDataSource` definida acima:

```csharp
var item = collectionView.MakeItem("EmployeeCell", indexPath) as EmployeeItemController;
...
```

Além disso, o tipo de controlador de exibição **deve** corresponder ao nome do `.xib` arquivo que define o protótipo **exatamente**. Neste exemplo, no caso de `EmployeeItemController` e `EmployeeItemController.xib`.

O layout real dos itens na exibição de coleção é controlado por uma classe de Layout do modo de exibição de coleção e pode ser alterado dinamicamente em tempo de execução, atribuindo uma nova instância para o `CollectionViewLayout` propriedade. A alteração dessa propriedade atualiza a aparência da exibição de coleção sem a alteração de animação.

Dois tipos de layout interno com o modo de exibição de coleção que tratará os usos mais comuns é fornecido Apple: `NSCollectionViewFlowLayout` e `NSCollectionViewGridLayout`. Se o desenvolvedor necessário um formato personalizado, como definir o layout de itens em um círculo, eles podem criar uma instância personalizada do `NSCollectionViewLayout` e substituir os métodos necessários para obter o efeito desejado.

Este exemplo usa o layout de fluxo padrão para que ele cria uma instância do `NSCollectionViewFlowLayout` classe e configura-o da seguinte maneira:

```csharp
var flowLayout = new NSCollectionViewFlowLayout()
{
    ItemSize = new CGSize(150, 150),
    SectionInset = new NSEdgeInsets(10, 10, 10, 20),
    MinimumInteritemSpacing = 10,
    MinimumLineSpacing = 10
};
```

O `ItemSize` propriedade define o tamanho de cada célula individual na coleção. O `SectionInset` propriedade define inserções a partir da borda da coleção de células serão dispostas em. `MinimumInteritemSpacing` Define o espaçamento mínimo entre itens e `MinimumLineSpacing` define o espaçamento mínimo entre as linhas na coleção.

O layout é atribuído para o modo de exibição de coleção e uma instância do `CollectionViewDelegate` está anexado para lidar com a seleção de item:

```csharp
// Setup collection view
EmployeeCollection.CollectionViewLayout = flowLayout;
EmployeeCollection.Delegate = new CollectionViewDelegate(this);
```

O `PopulateWithData` método cria uma nova instância do `CollectionViewDataSource`, preenche com dados, anexa-o para a exibição de coleção e chama o `ReloadData` método para exibir os itens:

```csharp
private void PopulateWithData()
{
    // Make datasource
    Datasource = new CollectionViewDataSource(EmployeeCollection);

    // Build list of employees
    Datasource.Data.Add(new PersonModel("Craig Dunn", "Documentation Manager", true));
    ...

    // Populate collection view
    EmployeeCollection.ReloadData();
}
```

O `ViewDidLoad` método é substituído e chama o `ConfigureCollectionView` e `PopulateWithData` métodos para exibir o modo de exibição de coleção final para o usuário:

```csharp
public override void ViewDidLoad()
{
    base.ViewDidLoad();

    // Initialize Collection View
    ConfigureCollectionView();
    PopulateWithData();
}
```

<a name="Summary"/>

## <a name="summary"></a>Resumo

Este artigo obteve uma visão detalhada de como trabalhar com exibições de coleção em um aplicativo Xamarin.Mac. Primeiro, ele observou expor uma classe do c# para Objective-C usando a codificação de chave-valor (KVC) e chave-valor observando (KVO). Em seguida, ele mostrou como usar uma classe compatível com KVO e dados associação-lo a exibições de coleção no construtor de Interface do Xcode. Finalmente, ele mostrou como interagir com exibições de coleção de código c#.

## <a name="related-links"></a>Links relacionados

- [MacCollectionNew (exemplo)](https://developer.xamarin.com/samples/mac/MacCollectionNew/)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Associação de dados e a codificação de chave-valor](~/mac/app-fundamentals/databinding.md)
- [NSCollectionView](https://developer.apple.com/reference/appkit/nscollectionview)
- [Diretrizes de interface humana do OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)
