---
title: Exibições de coleção no Xamarin. Mac
description: Este artigo descreve como trabalhar com exibições de coleção em um aplicativo Xamarin. Mac. Ele aborda a criação e a manutenção de exibições de coleção no Xcode e Interface Builder e trabalhar com elas de forma programática.
ms.prod: xamarin
ms.assetid: 6EE32256-5948-4AE4-8133-6D0B3F4173E8
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 05/24/2017
ms.openlocfilehash: 432e875969164b6481671d769d488c5f34458fe0
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68657270"
---
# <a name="collection-views-in-xamarinmac"></a>Exibições de coleção no Xamarin. Mac

_Este artigo descreve como trabalhar com exibições de coleção em um aplicativo Xamarin. Mac. Ele aborda a criação e a manutenção de exibições de coleção no Xcode e Interface Builder e trabalhar com elas de forma programática._

Ao trabalhar com C# o e o .net em um aplicativo Xamarin. Mac, o desenvolvedor tem acesso aos mesmos controles de exibição de coleção AppKit que um desenvolvedor trabalhando no *Objective-C* e no *Xcode* . Como o Xamarin. Mac se integra diretamente com o Xcode, o desenvolvedor usa o _interface Builder_ do Xcode para criar e manter exibições de coleção.

Um `NSCollectionView` exibe uma grade de subexibições organizadas usando `NSCollectionViewLayout`um. Cada subexibição na grade é representada por um `NSCollectionViewItem` que gerencia o carregamento do conteúdo da exibição de um `.xib` arquivo.

[![Uma execução de aplicativo de exemplo](collection-view-images/intro01.png)](collection-view-images/intro01.png#lightbox)

Este artigo aborda as noções básicas de como trabalhar com exibições de coleção em um aplicativo Xamarin. Mac. É altamente recomendável que você trabalhe pelo artigo [Hello, Mac](~/mac/get-started/hello-mac.md) primeiro, especificamente a [introdução às seções Xcode e Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) e [ações](~/mac/get-started/hello-mac.md#outlets-and-actions) , pois ela aborda os principais conceitos e técnicas que são usados ao longo deste artigo.

Talvez você queira dar uma olhada na seção [expondo C# classes/métodos para Objective-C](~/mac/internals/how-it-works.md) do documento [interno do Xamarin. Mac](~/mac/internals/how-it-works.md) também, explica `Register` os comandos e `Export` usados para conectar suas C# classes ao Objetos Objective-C e elementos de interface do usuário.

<a name="About_Collection_Views"/>

## <a name="about-collection-views"></a>Sobre exibições de coleção

O objetivo principal de um modo de exibição`NSCollectionView`de coleção () é organizar visualmente um grupo de objetos de maneira organizada usando um layout de`NSCollectionViewLayout`exibição de coleção (), com`NSCollectionViewItem`cada objeto individual () obtendo sua própria exibição na coleção maior. As exibições de coleção funcionam por meio de associação de dados e técnicas de codificação de valor chave e, como tal, você deve ler a documentação de [codificação de dados e de código chave-valor](~/mac/app-fundamentals/databinding.md) antes de continuar com este artigo.

O modo de exibição de coleção não tem um item de exibição de coleção interna padrão (como uma exibição de estrutura de tópicos ou tabela), portanto, o desenvolvedor é responsável por projetar e implementar uma _exibição de protótipo_ usando outros controles AppKit, como campos de imagem, campos de texto, rótulos, diante. Essa exibição de protótipo será usada para exibir e trabalhar com cada item gerenciado pelo modo de exibição de coleção e armazenado em um `.xib` arquivo.

Como o desenvolvedor é responsável pela aparência de um item de exibição de coleção, o modo de exibição de coleção não tem suporte interno para realçar um item selecionado na grade. A implementação desse recurso será abordada neste artigo.

<a name="Defining_your_Data_Model"/>

## <a name="defining-the-data-model"></a>Definindo o modelo de dados

Antes de vincular dados a uma exibição de coleção no Interface Builder, uma classe compatível com KVC (codificação de valor de chave)/Key-Value de observação (KVO) deve ser definida no aplicativo Xamarin. Mac para atuar como o _modelo de dados_ para a associação. O modelo de dados fornece todos os dados que serão exibidos na coleção e recebe quaisquer modificações para os dados que o usuário faz na interface de usuário ao executar o aplicativo.

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

A associação de dados com uma exibição de coleção é muito semelhante à associação com uma exibição `NSCollectionViewDataSource` de tabela, como é usada para fornecer dados para a coleção. Como a exibição de coleção não tem um formato de exibição predefinido, mais trabalho é necessário para fornecer comentários de interação do usuário e para rastrear a seleção do usuário.

<a name="Creating-the-Cell-Prototype"/>

### <a name="creating-the-cell-prototype"></a>Criando o protótipo de célula

Como o modo de exibição de coleção não inclui um protótipo de célula padrão, o desenvolvedor precisará adicionar um `.xib` ou mais arquivos ao aplicativo Xamarin. Mac para definir o layout e o conteúdo das células individuais.

Faça o seguinte:

1. Na **Gerenciador de soluções**, clique com o botão direito do mouse no nome do projeto e selecione **Adicionar** > **novo arquivo...**
2. Selecione > **controlador de exibição**do Mac, dê a `EmployeeItem` ele um nome (como neste exemplo) e clique no botão **novo** para criar: 

    ![Adicionando um novo controlador de exibição](collection-view-images/proto01.png)

    Isso adicionará um `EmployeeItem.cs` `EmployeeItemController.cs` arquivo e `EmployeeItemController.xib` à solução do projeto.
3. Clique duas vezes no `EmployeeItemController.xib` arquivo para abri-lo para edição no interface Builder do Xcode.
4. Adicione um `NSBox` `NSImageView` e dois`NSLabel` controles à exibição e deite-os da seguinte maneira:

    ![Criando o layout do protótipo de célula](collection-view-images/proto02.png)
5. Abra o **Editor do assistente** e crie uma **tomada** para `NSBox` que ela possa ser usada para indicar o estado de seleção de uma célula:

    ![Expondo o NSBox em uma tomada](collection-view-images/proto03.png)
6. Retorne ao **editor padrão** e selecione o modo de exibição de imagem.
7. No **Inspetor de associação**, selecione **associar ao** > **proprietário do arquivo** e insira um **caminho de chave** do `self.Person.Icon`modelo de:

    ![Ligando o ícone](collection-view-images/proto04.png)
8. Selecione o primeiro rótulo e, no **Inspetor de associação**, selecione **associar ao** > **proprietário do arquivo** e insira um caminho de chave `self.Person.Name`do **modelo** de:

    ![Associando o nome](collection-view-images/proto05.png)
9. Selecione o segundo rótulo e, no **Inspetor de associação**, selecione **associar ao** > **proprietário do arquivo** e insira um caminho de chave `self.Person.Occupation`do **modelo** de:

    ![Associando a ocupação](collection-view-images/proto06.png)
10. Salve as alterações `.xib` no arquivo e retorne ao Visual Studio para sincronizar as alterações.

Edite `EmployeeItemController.cs` o arquivo e faça com que ele se pareça com o seguinte:

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

Observando esse código em detalhes, a classe é herdada `NSCollectionViewItem` de para que possa atuar como um protótipo para uma célula de exibição de coleção. A `Person` Propriedade expõe a classe que foi usada para associar dados ao modo de exibição de imagem e rótulos no Xcode. Esta é uma instância do `PersonModel` criada acima.

A `BackgroundColor` propriedade é um atalho para o `NSBox` controle `FillColor` que será usado para mostrar o status de seleção de uma célula. Ao substituir a `Selected` propriedade `NSCollectionViewItem`do, o código a seguir define ou limpa esse estado de seleção:

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

Uma fonte de dados de exibição`NSCollectionViewDataSource`de coleção () fornece todos os dados para uma exibição de coleção e cria e popula uma célula de exibição de `.xib` coleção (usando o protótipo) conforme necessário para cada item na coleção.

Adicione uma nova classe ao projeto, chame- `CollectionViewDataSource` a e faça com que ela se pareça com a seguinte:

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

Observando esse código em detalhes, a classe herda de `NSCollectionViewDataSource` e expõe uma lista de `PersonModel` instâncias por meio de `Data` sua propriedade.

Como essa coleção tem apenas uma seção, o código substitui o `GetNumberOfSections` método e sempre retorna `1`. Além disso, `GetNumberofItems` o método é substituído por ele retorna o número de itens `Data` na lista de propriedades.

O `GetItem` método é chamado sempre que uma nova célula é necessária e é semelhante ao seguinte:

```csharp
public override NSCollectionViewItem GetItem(NSCollectionView collectionView, NSIndexPath indexPath)
{
    var item = collectionView.MakeItem("EmployeeCell", indexPath) as EmployeeItemController;
    item.Person = Data[(int)indexPath.Item];

    return item;
}
```

O `MakeItem` método da exibição de coleção é chamado para criar ou retornar uma instância reutilizável `EmployeeItemController` do e sua `Person` propriedade é definida como item que está sendo exibido na célula solicitada. 

O `EmployeeItemController` deve ser registrado com o controlador de exibição de coleção com antecedência usando o seguinte código:

```csharp
EmployeeCollection.RegisterClassForItem(typeof(EmployeeItemController), "EmployeeCell");
``` 

O **identificador** (`EmployeeCell` `MakeItem` ) usado na chamada _deve_ corresponder ao nome do controlador de exibição que foi registrado com o modo de exibição de coleção. Esta etapa será abordada em detalhes abaixo.

<a name="Handling-Item-Selection"/>

### <a name="handling-item-selection"></a>Manipulação da seleção de item

Para lidar com a seleção e a desseleção de itens na coleção, um `NSCollectionViewDelegate` será necessário. Como este exemplo usará o tipo de `NSCollectionViewFlowLayout` layout interno, uma `NSCollectionViewDelegateFlowLayout` versão específica desse delegado será necessária.

Adicione uma nova classe ao projeto, chame- `CollectionViewDelegate` a e faça com que ela se pareça com a seguinte:

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

Os `ItemsSelected` métodos `ItemsDeselected` e são substituídos e usados para definir ou limpar `PersonSelected` a propriedade do controlador de exibição que está manipulando a exibição de coleção quando o usuário seleciona ou anula a seleção de um item. Isso será mostrado em detalhes abaixo.

<a name="Creating-the-Collection-View-in-Interface-Builder"/>

### <a name="creating-the-collection-view-in-interface-builder"></a>Criando o modo de exibição de coleção no Interface Builder

Com todas as partes de suporte necessárias em vigor, o storyboard principal pode ser editado e uma exibição de coleção adicionada a ele.

Faça o seguinte:

1. Clique duas vezes no `Main.Storyboard` arquivo no **Gerenciador de soluções** para abri-lo para edição no interface Builder do Xcode.
2. Arraste uma exibição de coleção para a exibição principal e redimensione-a para preencher a exibição:

    ![Adicionando uma exibição de coleção ao layout](collection-view-images/collection01.png)
3. Com o modo de exibição de coleção selecionado, use o editor de restrição para fixá-lo na exibição quando ele for redimensionado:

    ![Adicionando restrições](collection-view-images/collection02.png)
4. Verifique se a exibição de coleção está selecionada na **design Surface** (e não na **exibição** de rolagem com borda ou na **exibição de clipe** que a contém), alterne para o **Editor do assistente** e crie uma **tomada** para a exibição de coleção:

    ![Adicionando restrições](collection-view-images/collection03.png)
5. Salve as alterações e retorne ao Visual Studio para sincronização.

<a name="Bringing-it-all-Together"/>

## <a name="bringing-it-all-together"></a>Reunindo tudo isso

Todas as partes de suporte agora foram colocadas em vigor com uma classe para atuar como o modelo de dados (`PersonModel`), um `NSCollectionViewDataSource` foi adicionado para fornecer dados, um `NSCollectionViewDelegateFlowLayout` foi criado para tratar a seleção de itens e `NSCollectionView` um foi adicionado ao storyboard principal e é exposto como uma tomada`EmployeeCollection`().

A etapa final é editar o controlador de exibição que contém a exibição de coleção e reunir todas as partes para preencher a coleção e tratar a seleção de itens.

Edite `ViewController.cs` o arquivo e faça com que ele se pareça com o seguinte:

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

Dando uma olhada nesse código em detalhes, uma `Datasource` propriedade é definida para manter uma instância `CollectionViewDataSource` do que fornecerá os dados para o modo de exibição de coleção. Uma `PersonSelected` propriedade é definida para manter a `PersonModel` representação do item atualmente selecionado no modo de exibição de coleção. Essa propriedade também gera o `SelectionChanged` evento quando a seleção é alterada.

A `ConfigureCollectionView` classe é usada para registrar o controlador de exibição que atua como o protótipo de célula com o modo de exibição de coleção usando a seguinte linha:

```csharp
EmployeeCollection.RegisterClassForItem(typeof(EmployeeItemController), "EmployeeCell");
```

Observe que o **identificador** (`EmployeeCell`) usado para registrar o protótipo corresponde ao que foi chamado no `GetItem` método do `CollectionViewDataSource` definido acima:

```csharp
var item = collectionView.MakeItem("EmployeeCell", indexPath) as EmployeeItemController;
...
```

Além disso, o tipo do controlador de exibição **deve** corresponder ao nome do `.xib` arquivo que define o protótipo **exatamente**. No caso deste exemplo, `EmployeeItemController` e. `EmployeeItemController.xib`

O layout real dos itens na exibição de coleção é controlado por uma classe de layout de exibição de coleção e pode ser alterado dinamicamente no tempo de execução, atribuindo uma nova `CollectionViewLayout` instância à propriedade. A alteração dessa propriedade atualiza a aparência da exibição de coleção sem animar a alteração.

A Apple envia dois tipos de layout internos com o modo de exibição de coleção que manipulará os `NSCollectionViewFlowLayout` usos `NSCollectionViewGridLayout`mais comuns: e. Se o desenvolvedor precisar de um formato personalizado, como dispor os itens em um círculo, ele poderá criar uma instância personalizada do `NSCollectionViewLayout` e substituir os métodos necessários para obter o efeito desejado.

Este exemplo usa o layout de fluxo padrão para que ele crie uma instância `NSCollectionViewFlowLayout` da classe e a configure da seguinte maneira:

```csharp
var flowLayout = new NSCollectionViewFlowLayout()
{
    ItemSize = new CGSize(150, 150),
    SectionInset = new NSEdgeInsets(10, 10, 10, 20),
    MinimumInteritemSpacing = 10,
    MinimumLineSpacing = 10
};
```

A `ItemSize` propriedade define o tamanho de cada célula individual na coleção. A `SectionInset` propriedade define as indefinições da borda da coleção nas quais as células serão dispostas. `MinimumInteritemSpacing`define o espaçamento mínimo entre os `MinimumLineSpacing` itens e define o espaçamento mínimo entre as linhas na coleção.

O layout é atribuído à exibição de coleção e uma instância do `CollectionViewDelegate` é anexada para tratar a seleção de itens:

```csharp
// Setup collection view
EmployeeCollection.CollectionViewLayout = flowLayout;
EmployeeCollection.Delegate = new CollectionViewDelegate(this);
```

O `PopulateWithData` método cria uma nova instância `CollectionViewDataSource`do, popula-a com os dados, anexa-a à exibição de coleção e chama o `ReloadData` método para exibir os itens:

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

O `ViewDidLoad` método é substituído e chama os `ConfigureCollectionView` métodos `PopulateWithData` e para exibir a exibição de coleção final para o usuário:

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

Este artigo fez uma visão detalhada de como trabalhar com exibições de coleção em um aplicativo Xamarin. Mac. Primeiro, ele analisou a exposição de C# uma classe para Objective-C usando KVC (codificação de valor chave) e KVO (key-value Observation). Em seguida, ele mostrou como usar uma classe compatível com KVO e vincular dados a exibições de coleção no Interface Builder do Xcode. Por fim, ele mostrou como interagir com exibições de C# coleção no código.

## <a name="related-links"></a>Links relacionados

- [MacCollectionNew (exemplo)](https://docs.microsoft.com/samples/xamarin/mac-samples/maccollectionnew)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Associação de dados e a codificação de chave-valor](~/mac/app-fundamentals/databinding.md)
- [NSCollectionView](https://developer.apple.com/reference/appkit/nscollectionview)
- [Diretrizes de interface humana do OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)
