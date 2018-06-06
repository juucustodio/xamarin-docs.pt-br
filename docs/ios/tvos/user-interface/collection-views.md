---
title: Trabalhando com tvOS exibições de coleção em Xamarin
description: Este documento descreve como trabalhar com exibições de coleção em um aplicativo de tvOS criados com o Xamarin. Ele aborda layouts do modo de coleta, criação de células e modos de exibição suplementares, respondendo a eventos de usuário e muito mais.
ms.prod: xamarin
ms.assetid: 5125C4C7-2DDF-4C19-A362-17BB2B079178
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/16/2017
ms.openlocfilehash: 9b411ac6bb8d1492511f9e5d2234731ae64c3a82
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/05/2018
ms.locfileid: "34789272"
---
# <a name="working-with-tvos-collection-views-in-xamarin"></a>Trabalhando com tvOS exibições de coleção em Xamarin

Exibições de coleção permitem para um grupo de conteúdo deve ser exibido usando layouts arbitrários. Usando o suporte interno, que permitem os layouts de grade ou linear de facilitar a criação, enquanto também suporta layouts personalizados.

[![](collection-views-images/collection01.png "Exibição de coleção de exemplo")](collection-views-images/collection01.png#lightbox)

O modo de exibição de coleção mantém uma coleção de itens usando um delegado e uma fonte de dados para fornecer a interação do usuário e o conteúdo da coleção. Como o modo de exibição de coleção se baseia em um subsistema de Layout que é independente do modo de exibição em si, fornecer um Layout diferente pode alterar facilmente a apresentação de dados em interrupções do modo de exibição coleta.

<a name="About-Collection-Views" />

## <a name="about-collection-views"></a>Sobre exibições de coleção

Como mencionado anteriormente, uma exibição de coleção (`UICollectionView`) gerencia uma coleção ordenada de itens e apresenta os itens com layouts personalizáveis. As exibições de coleção funcionam de forma semelhante aos modos de exibição de tabela (`UITableView`), exceto que eles podem usar layouts de itens presentes em mais do que apenas uma única coluna.

Ao usar uma exibição de coleção em tvOS, seu aplicativo é responsável por fornecer os dados associados à coleção usando uma fonte de dados (`UICollectionViewDataSource`). Exibir dados de coleção podem opcionalmente organizados e apresentados em grupos diferentes (seções).

O modo de exibição de coleção apresenta os itens individuais na tela de uso de uma célula (`UICollectionViewCell`) que fornece a apresentação de uma determinada parte de informações da coleção (como uma imagem e seu título).

Opcionalmente, modos de exibição adicionais podem ser adicionados a apresentação do modo de exibição de coleção para atuar como cabeçalhos e rodapés para as seções e as células. Layout do modo de exibição de coleção é responsável por definir o posicionamento desses modos junto com as células individuais.

O modo de exibição de coleção pode responder à interação do usuário usando um delegado (`UICollectionViewDelegate`). Este delegado também é responsável por determinar se uma determinada célula pode receber o foco, se uma célula realçou ou se foi selecionada. Em alguns casos, o representante determina o tamanho das células individuais.

<a name="Collection-View-Layouts" />

## <a name="collection-view-layouts"></a>Layouts do modo de exibição de coleção

Um recurso importante de uma exibição de coleção é sua separação entre os dados que está apresentando e seu Layout. Um Layout de exibição de coleção (`UICollectionViewLayout`) é responsável por fornecer a organização e o local de células (e qualquer exibição suplementares) com apresentação na tela do modo de exibição de coleção.

As células individuais são criadas pela exibição de coleção da fonte de dados anexado e, em seguida, são organizadas e exibidas no layout de exibição de coleção fornecido.

O Layout do modo de exibição de coleção normalmente é fornecido quando o modo de exibição de coleção é criado. No entanto, você pode alterar o Layout do modo de exibição de coleção a qualquer momento e a apresentação na tela dos dados da exibição de coleção será atualizada automaticamente usando o novo layout fornecido.

O Layout do modo de exibição de coleção fornece vários métodos que podem ser usados para animar a transição entre dois layouts diferentes (por padrão, que nenhuma animação é feita). Além disso, os Layouts de modo de exibição de coleção pode trabalhar com reconhecedores de gestos para animar ainda mais a interação do usuário que resulta em uma alteração no layout.

<a name="Creating-Cells-and-Supplementary-Views" />

## <a name="creating-cells-and-supplementary-views"></a>Criando células e exibições suplementares

Fonte de dados de uma exibição da coleção apenas não é responsável por fornecer os dados fazendo a coleção de itens, mas também as células que são usadas para exibir o conteúdo.

Como exibições de coleção foram projetadas para lidar com grandes coleções de itens, as células individuais podem ser removida da fila e reutilizadas para evitar saturar limitações de memória. Há dois métodos diferentes para dequeueing exibições:

- `DequeueReusableCell` -Cria ou retorna uma célula do tipo em questão (como especificado no Storyboard do aplicativo).
- `DequeueReusableSupplementaryView` -Cria ou retorna uma exibição suplementar de determinado tipo (conforme especificado no Storyboard do aplicativo).

Antes de chamar qualquer um desses métodos, você deve registrar a classe Storyboard ou `.xib` arquivo usado para criar o modo de exibição da célula com a exibição de coleção. Por exemplo:

```csharp
public CityCollectionView (IntPtr handle) : base (handle)
{
    // Initialize
    RegisterClassForCell (typeof(CityCollectionViewCell), CityViewDatasource.CardCellId);
    ...
}
```

Onde `typeof(CityCollectionViewCell)` fornece a classe que dá suporte ao modo de exibição e `CityViewDatasource.CardCellId` fornece a ID usada quando a célula (ou exibição) é removida da fila.

Depois que a célula é removida da fila, você configurá-lo com os dados para o item que está representando e retorna para o modo de exibição de coleção para exibição.

<a name="About-Collection-View-Controllers" />

## <a name="about-collection-view-controllers"></a>Sobre controladores de exibição de coleção

Um controlador de exibição de coleção (`UICollectionViewController`) é um controlador de exibição especializados (`UIViewController`) que fornece o seguinte comportamento:

- Ele é responsável por carregar o modo de exibição de coleção do seu Storyboard ou `.xib` arquivo e criando o modo de exibição. Se criada no código, ele cria automaticamente uma exibição de coleção nova e não configurado.
- Quando o modo de exibição de coleção é carregado, o controlador tenta carregar sua fonte de dados e o delegado de Storyboard ou `.xib` arquivo. Se nenhum estiver disponível, se ele define como a origem de ambos.
- Garante que os dados são carregados antes que o modo de exibição de coleção é preenchido primeiro exibido e recarrega e desmarque a seleção em cada exibição subsequente.

Além disso, o controlador de exibição de coleção fornece métodos substituíveis que podem ser usados para gerenciar o ciclo de vida da exibição de coleção, como `AwakeFromNib` e `ViewWillDisplay`.

<a name="Collection-Views-and-Storyboards" />

## <a name="collection-views-and-storyboards"></a>Storyboards e exibições de coleção

É a maneira mais fácil para trabalhar com uma exibição de coleção em seu aplicativo Xamarin.tvOS, adicione um para o Storyboard. Como um exemplo rápido, vamos criar um aplicativo de exemplo que apresenta uma imagem, título e um botão de seleção. Se o usuário clicar no botão de seleção, uma exibição de coleção será exibida que permitem que o usuário escolha uma nova imagem. Quando uma imagem é escolhida, o modo de exibição de coleção é fechado e serão exibidas a nova imagem e título.

Vamos fazer o seguinte:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

    
1. Iniciar uma nova **tvOS exibição único aplicativo** no Visual Studio para Mac.
1. No **Solution Explorer**, clique duas vezes o `Main.storyboard` de arquivo e abri-lo no Designer de iOS.
1. Adicionar uma imagem de exibição, um rótulo e um botão para o modo de exibição existente e configurá-los para a seguinte aparência: 

    [![](collection-views-images/collection02.png "Layout de exemplo")](collection-views-images/collection02.png#lightbox)
1. Atribuir um **nome** para o modo de exibição de imagem e o rótulo no **Widget guia** do **propriedades Explorer**. Por exemplo: 

    [![](collection-views-images/collection03.png "O nome da configuração")](collection-views-images/collection03.png#lightbox)
1. Em seguida, arraste um controlador de exibição de coleção para o Storyboard: 

    [![](collection-views-images/collection04.png "Um controlador de exibição de coleção")](collection-views-images/collection04.png#lightbox)
1. Controle-arraste o botão para o controlador de exibição de coleção e selecione **Push** de pop-up: 

    [![](collection-views-images/collection05.png "Selecione por Push o pop-up")](collection-views-images/collection05.png#lightbox)
1. Quando o aplicativo é executado, isso tornará o modo de exibição de coleção ser Mostrar sempre que o usuário clica no botão.
1. Selecione o modo de exibição de coleção e insira os seguintes valores no **guia Layout** do **propriedades Explorer**: 

    [![](collection-views-images/collection06.png "O Gerenciador de propriedades")](collection-views-images/collection06.png#lightbox)
1. Isso controla o tamanho das células individuais e as bordas entre as células e da borda externa da exibição de coleção.
1. Selecione o controlador de exibição de coleção e defina sua classe como `CityCollectionViewController` no **guia Widget**: 

    [![](collection-views-images/collection07.png "Definir a classe como CityCollectionViewController")](collection-views-images/collection07.png#lightbox)
1. Selecione o modo de exibição de coleção e defina sua classe como `CityCollectionView` no **guia Widget**: 

    [![](collection-views-images/collection08.png "Definir a classe como CityCollectionView")](collection-views-images/collection08.png#lightbox)
1. Selecione a célula de exibição de coleção e defina sua classe como `CityCollectionViewCell` no **guia Widget**: 

    [![](collection-views-images/collection09.png "Definir a classe como CityCollectionViewCell")](collection-views-images/collection09.png#lightbox)
1. No **Widget guia** Certifique-se de que o **Layout** é `Flow` e **direção da rolagem** é `Vertical` para o modo de exibição de coleção: 

    [![](collection-views-images/collection10.png "A guia de Widget")](collection-views-images/collection10.png#lightbox)
1. Selecione a célula de exibição de coleção e defina seu **identidade** para `CityCell` no **guia Widget**: 

    [![](collection-views-images/collection11.png "Definir a identidade para CityCell")](collection-views-images/collection11.png#lightbox)
1. Salve as alterações.
    

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

    
1. Iniciar uma nova **tvOS exibição único aplicativo** no Visual Studio.
1. No **Solution Explorer**, clique duas vezes o `Main.storyboard` de arquivo e abri-lo no Designer de iOS.
1. Adicionar uma imagem de exibição, um rótulo e um botão para o modo de exibição existente e configurá-los para a seguinte aparência: 

    [![](collection-views-images/collection02vs.png "Configure o layout")](collection-views-images/collection02vs.png#lightbox)
1. Atribuir um **nome** para o modo de exibição de imagem e o rótulo no **Widget guia** do **propriedades Explorer**. Por exemplo: 

    [![](collection-views-images/collection03vs.png "O Gerenciador de propriedades")](collection-views-images/collection03vs.png#lightbox)
1. Em seguida, arraste um controlador de exibição de coleção para o Storyboard: 

    [![](collection-views-images/collection04vs.png "Um controlador de exibição de coleção")](collection-views-images/collection04vs.png#lightbox)
1. Controle-arraste o botão para o controlador de exibição de coleção e selecione **Push** de pop-up: 

    [![](collection-views-images/collection05vs.png "Selecione por Push o pop-up")](collection-views-images/collection05vs.png#lightbox)
1. Quando o aplicativo é executado, isso tornará o modo de exibição de coleção ser Mostrar sempre que o usuário clica no botão.
1. Selecione o modo de exibição de coleção e, no **guia Layout** do **propriedades Explorer** insira o **largura** como _361_ e  **Altura** como _256_ 
1. Isso controla o tamanho das células individuais e as bordas entre as células e da borda externa da exibição de coleção.
1. Selecione o controlador de exibição de coleção e defina sua classe como `CityCollectionViewController` no **guia Widget**: 

    [![](collection-views-images/collection07vs.png "Definir a classe como CityCollectionViewController")](collection-views-images/collection07vs.png#lightbox)
1. Selecione o modo de exibição de coleção e defina sua classe como `CityCollectionView` no **guia Widget**: 

    [![](collection-views-images/collection08vs.png "Definir a classe como CityCollectionView")](collection-views-images/collection08vs.png#lightbox)
1. Selecione a célula de exibição de coleção e defina sua classe como `CityCollectionViewCell` no **guia Widget**: 

    [![](collection-views-images/collection09vs.png "Definir a classe como CityCollectionViewCell")](collection-views-images/collection09vs.png#lightbox)
1. No **Widget guia** Certifique-se de que o **Layout** é `Flow` e **direção da rolagem** é `Vertical` para o modo de exibição de coleção: 

    [![](collection-views-images/collection10vs.png "Guia de Widget de seção")](collection-views-images/collection10vs.png#lightbox)
1. Selecione a célula de exibição de coleção e defina seu **identidade** para `CityCell` no **guia Widget**: 

    [![](collection-views-images/collection11vs.png "Definir a identidade para CityCell")](collection-views-images/collection11vs.png#lightbox)
1. Salve as alterações.
    

-----

Se tivéssemos escolhido `Custom` para o modo de exibição de coleção **Layout**, é possível especificar um layout personalizado. Apple fornece um interno `UICollectionViewFlowLayout` e `UICollectionViewDelegateFlowLayout` que pode facilmente apresentar dados em um layout de grade (eles são usados pelo `flow` estilo de layout). 

Para obter mais informações sobre como trabalhar com Storyboards, consulte nosso [Hello, tvOS Quick Start Guide](~/ios/tvos/get-started/hello-tvos.md).

<a name="Providing-Data-for-the-Collection-View" />

## <a name="providing-data-for-the-collection-view"></a>Fornece dados para o modo de exibição de coleção

Agora que temos nosso exibição de coleção (e coleção View Controller) adicionado à nossa Storyboard, é preciso fornecer os dados para a coleção. 

<a name="The-Data-Model" />

### <a name="the-data-model"></a>O modelo de dados

Primeiro, vamos criar um modelo para os dados que contém o nome do arquivo da imagem exibir, o título e um sinalizador para permitir a cidade a ser selecionado.

Criar um `CityInfo` classe e torná-lo a aparência a seguir:

```csharp
using System;

namespace tvCollection
{
    public class CityInfo
    {
        #region Computed Properties
        public string ImageFilename { get; set; }
        public string Title { get; set; }
        public bool CanSelect{ get; set; }
        #endregion

        #region Constructors
        public CityInfo (string filename, string title, bool canSelect)
        {
            // Initialize
            this.ImageFilename = filename;
            this.Title = title;
            this.CanSelect = canSelect;
        }
        #endregion
    }
}
```

### <a name="the-collection-view-cell"></a>A célula de exibição de coleção

Agora, precisamos definir como os dados serão apresentados para cada célula. Editar o `CityCollectionViewCell.cs` arquivo (criado para você automaticamente do seu arquivo de Storyboard) e torná-lo a aparência a seguir:

```csharp
using System;
using Foundation;
using UIKit;
using CoreGraphics;

namespace tvCollection
{
    public partial class CityCollectionViewCell : UICollectionViewCell
    {
        #region Private Variables
        private CityInfo _city;
        #endregion

        #region Computed Properties
        public UIImageView CityView { get ; set; }
        public UILabel CityTitle { get; set; }

        public CityInfo City {
            get { return _city; }
            set {
                _city = value;
                CityView.Image = UIImage.FromFile (City.ImageFilename);
                CityView.Alpha = (City.CanSelect) ? 1.0f : 0.5f;
                CityTitle.Text = City.Title;
            }
        }
        #endregion

        #region Constructors
        public CityCollectionViewCell (IntPtr handle) : base (handle)
        {
            // Initialize
            CityView = new UIImageView(new CGRect(22, 19, 320, 171));
            CityView.AdjustsImageWhenAncestorFocused = true;
            AddSubview (CityView);

            CityTitle = new UILabel (new CGRect (22, 209, 320, 21)) {
                TextAlignment = UITextAlignment.Center,
                TextColor = UIColor.White,
                Alpha = 0.0f
            };
            AddSubview (CityTitle);
        }
        #endregion
    

    }
}
```

Para nosso aplicativo tvOS, podemos exibirá uma imagem e um título opcional. Se a determinada cidade não pode ser selecionada, nós são esmaecimento a visualização da imagem usando o seguinte código:

```csharp
CityView.Alpha = (City.CanSelect) ? 1.0f : 0.5f;
```

Quando a célula que contém a imagem é colocada em foco pelo usuário, queremos usar interno efeito da Parallax nele definindo a propriedade a seguir:

```csharp
CityView.AdjustsImageWhenAncestorFocused = true;
```

Para obter mais informações sobre a navegação e foco, consulte nosso [trabalhando com foco e navegação](~/ios/tvos/app-fundamentals/navigation-focus.md) e [Siri remoto e controladores Bluetooth](~/ios/tvos/platform/remote-bluetooth.md) documentação.


<a name="The-Collection-View-Data-Provider" />

### <a name="the-collection-view-data-provider"></a>O provedor de dados de exibição de coleção

Com nosso modelo de dados criados e nossa layout de célula definida, vamos criar uma fonte de dados para nosso exibição de coleção. A fonte de dados será responsável por fornecer não apenas os dados de backup, mas também dequeueing as células para exibir as células individuais na tela.

Criar um `CityViewDatasource` classe e torná-lo a aparência a seguir:

```csharp
using System;
using System.Collections.Generic;
using UIKit;
using Foundation;
using CoreGraphics;
using ObjCRuntime;

namespace tvCollection
{
    public class CityViewDatasource : UICollectionViewDataSource
    {
        #region Application Access
        public static AppDelegate App {
            get { return (AppDelegate)UIApplication.SharedApplication.Delegate; }
        }
        #endregion

        #region Static Constants
        public static NSString CardCellId = new NSString ("CityCell");
        #endregion

        #region Computed Properties
        public List<CityInfo> Cities { get; set; } = new List<CityInfo>();
        public CityCollectionView ViewController { get; set; }
        #endregion

        #region Constructors
        public CityViewDatasource (CityCollectionView controller)
        {
            // Initialize
            this.ViewController = controller;
            PopulateCities ();
        }
        #endregion

        #region Public Methods
        public void PopulateCities() {

            // Clear existing cities
            Cities.Clear();

            // Add new cities
            Cities.Add(new CityInfo("City01.jpg", "Houses by Water", false));
            Cities.Add(new CityInfo("City02.jpg", "Turning Circle", true));
            Cities.Add(new CityInfo("City03.jpg", "Skyline at Night", true));
            Cities.Add(new CityInfo("City04.jpg", "Golden Gate Bridge", true));
            Cities.Add(new CityInfo("City05.jpg", "Roads by Night", true));
            Cities.Add(new CityInfo("City06.jpg", "Church Domes", true));
            Cities.Add(new CityInfo("City07.jpg", "Mountain Lights", true));
            Cities.Add(new CityInfo("City08.jpg", "City Scene", false));
            Cities.Add(new CityInfo("City09.jpg", "House in Winter", true));
            Cities.Add(new CityInfo("City10.jpg", "By the Lake", true));
            Cities.Add(new CityInfo("City11.jpg", "At the Dome", true));
            Cities.Add(new CityInfo("City12.jpg", "Cityscape", true));
            Cities.Add(new CityInfo("City13.jpg", "Model City", true));
            Cities.Add(new CityInfo("City14.jpg", "Taxi, Taxi!", true));
            Cities.Add(new CityInfo("City15.jpg", "On the Sidewalk", true));
            Cities.Add(new CityInfo("City16.jpg", "Midnight Walk", true));
            Cities.Add(new CityInfo("City17.jpg", "Lunchtime Cafe", true));
            Cities.Add(new CityInfo("City18.jpg", "Coffee Shop", true));
            Cities.Add(new CityInfo("City19.jpg", "Rustic Tavern", true));
        }
        #endregion

        #region Override Methods
        public override nint NumberOfSections (UICollectionView collectionView)
        {
            return 1;
        }

        public override nint GetItemsCount (UICollectionView collectionView, nint section)
        {
            return Cities.Count;
        }

        public override UICollectionViewCell GetCell (UICollectionView collectionView, NSIndexPath indexPath)
        {
            var cityCell = (CityCollectionViewCell)collectionView.DequeueReusableCell (CardCellId, indexPath);
            var city = Cities [indexPath.Row];

            // Initialize city
            cityCell.City = city;

            return cityCell;
        }
        #endregion
    }
}
```

Permitem ver essa classe em detalhes. Primeiro, estamos herdam `UICollectionViewDataSource` e fornecer um atalho para a ID de células (que são atribuídos no Designer de iOS):

```csharp
public static NSString CardCellId = new NSString ("CityCell");
```

Em seguida, podemos fornecer armazenamento para os dados de coleta e fornecer uma classe para preencher os dados:

```csharp
public List<CityInfo> Cities { get; set; } = new List<CityInfo>();
...

public void PopulateCities() {

    // Clear existing cities
    Cities.Clear();

    // Add new cities
    Cities.Add(new CityInfo("City01.jpg", "Houses by Water", false));
    Cities.Add(new CityInfo("City02.jpg", "Turning Circle", true));
    ...
}
```

Em seguida, podemos substituir o `NumberOfSections` método e retornar o número de seções (grupos de itens) que nossa coleção exibir. Nesse caso, há apenas um:

```csharp
public override nint NumberOfSections (UICollectionView collectionView)
{
    return 1;
}
```

Em seguida, nós retornar o número de itens na nossa coleção usando o código a seguir:

```csharp
public override nint GetItemsCount (UICollectionView collectionView, nint section)
{
    return Cities.Count;
}
```

Por fim, podemos dequeue uma célula reutilizável quando o modo de exibição de coleção de solicitação com o código a seguir:

```csharp
public override UICollectionViewCell GetCell (UICollectionView collectionView, NSIndexPath indexPath)
{
    var cityCell = (CityCollectionViewCell)collectionView.DequeueReusableCell (CardCellId, indexPath);
    var city = Cities [indexPath.Row];

    // Initialize city
    cityCell.City = city;

    return cityCell;
}
```

Depois de recebermos uma célula de exibição de coleção de nosso `CityCollectionViewCell` tipo, podemos preenchê-lo com o item fornecido.

<a name="Responding-to-User-Events" />

## <a name="responding-to-user-events"></a>Respondendo a eventos de usuário

Como queremos que o usuário seja capaz de selecionar um item de nossa coleção, é preciso fornecer um representante da exibição de coleção para lidar com essa interação. E é preciso fornecer uma maneira de informar nossa chamada exibir qual item o usuário selecionou.

<a name="The-App-Delegate" />

### <a name="the-app-delegate"></a>O representante do aplicativo

Precisamos de uma maneira para relacionar o item atualmente selecionado na exibição de coleção de volta para o modo de exibição de chamada. Usaremos uma propriedade personalizada em nosso `AppDelegate`. Editar o `AppDelegate.cs` de arquivo e adicione o seguinte código:

```csharp
public CityInfo SelectedCity { get; set;} = new CityInfo("City02.jpg", "Turning Circle", true);
```

Isso define a propriedade e define a cidade do padrão que será exibida inicialmente. Posteriormente, podemos vai consumir esta propriedade para exibir a seleção do usuário e permitir que a seleção a ser alterado.

<a name="The-Collection-View-Delegate" />

### <a name="the-collection-view-delegate"></a>O delegado de modo de exibição de coleção

Em seguida, adicione um novo `CityViewDelegate` classe ao projeto e torná-lo a aparência a seguir:


```csharp
using System;
using System.Collections.Generic;
using UIKit;
using Foundation;
using CoreGraphics;

namespace tvCollection
{
    public class CityViewDelegate : UICollectionViewDelegateFlowLayout
    {
        #region Application Access
        public static AppDelegate App {
            get { return (AppDelegate)UIApplication.SharedApplication.Delegate; }
        }
        #endregion

        #region Constructors
        public CityViewDelegate ()
        {
        }
        #endregion

        #region Override Methods
        public override CGSize GetSizeForItem (UICollectionView collectionView, UICollectionViewLayout layout, NSIndexPath indexPath)
        {
            return new CGSize (361, 256);
        }

        public override bool CanFocusItem (UICollectionView collectionView, NSIndexPath indexPath)
        {
            if (indexPath == null) {
                return false;
            } else {
                var controller = collectionView as CityCollectionView;
                return controller.Source.Cities[indexPath.Row].CanSelect;
            }
        }

        public override void ItemSelected (UICollectionView collectionView, NSIndexPath indexPath)
        {
            var controller = collectionView as CityCollectionView;
            App.SelectedCity = controller.Source.Cities [indexPath.Row];

            // Close Collection
            controller.ParentController.DismissViewController(true,null);
        }
        #endregion
    }
}
```

Vamos examinar mais detalhadamente essa classe. Primeiro, estamos herdam `UICollectionViewDelegateFlowLayout`. O motivo pelo qual estamos herdar desta classe e não o `UICollectionViewDelegate` é que estamos usando o interno `UICollectionViewFlowLayout` para apresentar os itens e não é um tipo de layout personalizado.

Em seguida, vamos retornar o tamanho para os itens individuais usando este código:

```csharp
public override CGSize GetSizeForItem (UICollectionView collectionView, UICollectionViewLayout layout, NSIndexPath indexPath)
{
    return new CGSize (361, 256);
}
```

Em seguida, decidimos se uma determinada célula pode receber o foco usando o seguinte código: 

```csharp
public override bool CanFocusItem (UICollectionView collectionView, NSIndexPath indexPath)
{
    if (indexPath == null) {
        return false;
    } else {
        var controller = collectionView as CityCollectionView;
        return controller.Source.Cities[indexPath.Row].CanSelect;
    }
}
```

Verificamos para ver se uma determinada parte dos dados de backup tem seu `CanSelect` sinalizador definido como `true` e retornará o valor. Para obter mais informações sobre a navegação e foco, consulte nosso [trabalhando com foco e navegação](~/ios/tvos/app-fundamentals/navigation-focus.md) e [Siri remoto e controladores Bluetooth](~/ios/tvos/platform/remote-bluetooth.md) documentação.

Por fim, podemos responder ao usuário selecionar um item com o código a seguir:

```csharp
public override void ItemSelected (UICollectionView collectionView, NSIndexPath indexPath)
{
    var controller = collectionView as CityCollectionView;
    App.SelectedCity = controller.Source.Cities [indexPath.Row];

    // Close Collection
    controller.ParentController.DismissViewController(true,null);
}
```

Aqui, definimos o `SelectedCity` propriedade do nosso `AppDelegate` para o item que o usuário selecionada e fechar o controlador de exibição de coleção, retornar para o modo de exibição chamado nos. Não definimos o `ParentController` propriedade nosso de exibição de coleção ainda, faremos ou próximo.

<a name="Configuring-the-Collection-View" />

## <a name="configuring-the-collection-view"></a>Configurando o modo de exibição de coleção

Agora precisamos Editar nossos exibição de coleção e atribuir nossa fonte de dados e o delegado. Editar o `CityCollectionView.cs` arquivo (criado automaticamente em nossa Storyboard) e torná-lo a aparência a seguir:

```csharp
using System;
using Foundation;
using UIKit;

namespace tvCollection
{
    public partial class CityCollectionView : UICollectionView
    {
        #region Application Access
        public static AppDelegate App {
            get { return (AppDelegate)UIApplication.SharedApplication.Delegate; }
        }
        #endregion

        #region Computed Properties
        public CityViewDatasource Source {
            get { return DataSource as CityViewDatasource;}
        }

        public CityCollectionViewController ParentController { get; set;}
        #endregion

        #region Constructors
        public CityCollectionView (IntPtr handle) : base (handle)
        {
            // Initialize
            RegisterClassForCell (typeof(CityCollectionViewCell), CityViewDatasource.CardCellId);
            DataSource = new CityViewDatasource (this);
            Delegate = new CityViewDelegate ();
        }
        #endregion

        #region Override Methods
        public override nint NumberOfSections ()
        {
            return 1;
        }

        public override void DidUpdateFocus (UIFocusUpdateContext context, UIFocusAnimationCoordinator coordinator)
        {
            var previousItem = context.PreviouslyFocusedView as CityCollectionViewCell;
            if (previousItem != null) {
                Animate (0.2, () => {
                    previousItem.CityTitle.Alpha = 0.0f;
                });
            }

            var nextItem = context.NextFocusedView as CityCollectionViewCell;
            if (nextItem != null) {
                Animate (0.2, () => {
                    nextItem.CityTitle.Alpha = 1.0f;
                });
            }
        }
        #endregion
    }
}
```

Primeiro, fornecemos um atalho para acessar nosso `AppDelegate`: 

```csharp
public static AppDelegate App {
    get { return (AppDelegate)UIApplication.SharedApplication.Delegate; }
}
```

Em seguida, fornecemos um atalho para a fonte de dados do modo de exibição de coleção e uma propriedade para acessar o controlador de exibição de coleção (usado pelo nosso representante acima para fechar a coleção quando o usuário faz uma seleção):

```csharp
public CityViewDatasource Source {
    get { return DataSource as CityViewDatasource;}
}

public CityCollectionViewController ParentController { get; set;}
```

Em seguida, usamos o seguinte código para inicializar a exibição de coleção e atribuir a nossa classe de célula, a fonte de dados e o delegado:

```csharp
public CityCollectionView (IntPtr handle) : base (handle)
{
    // Initialize
    RegisterClassForCell (typeof(CityCollectionViewCell), CityViewDatasource.CardCellId);
    DataSource = new CityViewDatasource (this);
    Delegate = new CityViewDelegate ();
}
```

Por fim, queremos que o título abaixo da imagem para só ficará visível quando o usuário tem realçado (em foco). Podemos fazer isso com o código a seguir:

```csharp
public override void DidUpdateFocus (UIFocusUpdateContext context, UIFocusAnimationCoordinator coordinator)
{
    var previousItem = context.PreviouslyFocusedView as CityCollectionViewCell;
    if (previousItem != null) {
        Animate (0.2, () => {
            previousItem.CityTitle.Alpha = 0.0f;
        });
    }

    var nextItem = context.NextFocusedView as CityCollectionViewCell;
    if (nextItem != null) {
        Animate (0.2, () => {
            nextItem.CityTitle.Alpha = 1.0f;
        });
    }
}
```

Vamos definir transparence do item anterior perdendo o foco para zero (0) e transparence do próximo item Obtenha o foco para 100%. Esses transição obter animado também.


## <a name="configuring-the-collection-view-controller"></a>Configurar o controlador de exibição de coleção

Agora, precisamos fazer a configuração final em nosso exibição de coleção e permitir que o controlador definir a propriedade que são definidas para que o modo de exibição de coleção pode ser fechado depois que o usuário faz uma seleção.

Editar o `CityCollectionViewController.cs` arquivo (criado automaticamente de nosso Storyboard) e torná-lo a aparência a seguir:

```csharp
// This file has been autogenerated from a class added in the UI designer.

using System;

using Foundation;
using UIKit;

namespace tvCollection
{
    public partial class CityCollectionViewController : UICollectionViewController
    {
        #region Computed Properties
        public CityCollectionView Collection {
            get { return CollectionView as CityCollectionView; }
        }
        #endregion

        #region Constructors
        public CityCollectionViewController (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void AwakeFromNib ()
        {
            base.AwakeFromNib ();

            // Save link to controller
            Collection.ParentController = this;
        }
        #endregion
    }
}

```

## <a name="putting-it-all-together"></a>Colocando tudo junto 

Agora que temos todas das partes juntar para preencher e controlar nossos exibição de coleção, é necessário fazer as edições finais para o nosso modo principal para reunir tudo.

Editar o `ViewController.cs` arquivo (criado automaticamente de nosso Storyboard) e torná-lo a aparência a seguir:

```csharp
using System;
using Foundation;
using UIKit;
using tvCollection;

namespace MySingleView
{
    public partial class ViewController : UIViewController
    {
        #region Application Access
        public static AppDelegate App {
            get { return (AppDelegate)UIApplication.SharedApplication.Delegate; }
        }
        #endregion

        #region Constructors
        public ViewController (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();
            // Perform any additional setup after loading the view, typically from a nib.
        }

        public override void ViewWillAppear (bool animated)
        {
            base.ViewWillAppear (animated);

            // Update image with the currently selected one
            CityView.Image = UIImage.FromFile(App.SelectedCity.ImageFilename);
            BackgroundView.Image = CityView.Image;
            CityTitle.Text = App.SelectedCity.Title;
        }

        public override void DidReceiveMemoryWarning ()
        {
            base.DidReceiveMemoryWarning ();
            // Release any cached data, images, etc that aren't in use.
        }
        #endregion
    }
}
```

Inicialmente, o código a seguir exibe o item selecionado do `SelectedCity` propriedade o `AppDelegate` e exibe-o novamente quando o usuário faz uma seleção de exibição de coleção:

```csharp
public override void ViewWillAppear (bool animated)
{
    base.ViewWillAppear (animated);

    // Update image with the currently selected one
    CityView.Image = UIImage.FromFile(App.SelectedCity.ImageFilename);
    BackgroundView.Image = CityView.Image;
    CityTitle.Text = App.SelectedCity.Title;
}
```

<a name="Testing-the-app" />

## <a name="testing-the-app"></a>O aplicativo de teste

Tudo no lugar, se você compilar e executa o aplicativo, o modo de exibição principal será exibido com a cidade do padrão:

[![](collection-views-images/run01.png "A tela principal")](collection-views-images/run01.png#lightbox)

Se o usuário clicar o **selecionar uma exibição** botão, o modo de exibição de coleção será exibido:

[![](collection-views-images/run02.png "O modo de exibição de coleção")](collection-views-images/run02.png#lightbox)

Qualquer cidade que tem seu `CanSelect` propriedade definida como `false` será exibido esmaecido e o usuário não poderá definir o foco para ele. Quando o usuário realça um item (torná-la em foco) o título é exibido e eles podem usar o efeito da Parallax inclinação sutilmente a imagem em 3D.

Quando o usuário clica selecione a imagem, o modo de exibição de coleção é fechado e o modo de exibição principal será exibida novamente com a nova imagem:

[![](collection-views-images/run03.png "Uma nova imagem na tela inicial")](collection-views-images/run03.png#lightbox)

<a name="Creating-Custom-Layout-and-Reordering-Items" />

## <a name="creating-custom-layout-and-reordering-items"></a>Criar um Layout personalizado e reordenação de itens

Um dos principais recursos do uso de uma exibição de coleção é a capacidade de criar layouts personalizados. Como tvOS herda do iOS, o processo para criar um layout personalizado é o mesmo. Confira nosso [Introdução às exibições de coleção](~/ios/user-interface/controls/uicollectionview.md) documentação para obter mais informações.

Recentemente adicionado à coleção de exibições para iOS 9 foi a capacidade de permitir facilmente a reordenação de itens na coleção. Novamente, como tvOS 9 é um subconjunto do iOS 9, isso é feito-los mesmo modo. Confira nosso [alterações de exibição de coleção](~/ios/user-interface/controls/uicollectionview.md) documento para obter mais detalhes.


<a name="Summary" />

## <a name="summary"></a>Resumo

Este artigo abordou criando e trabalhando com exibições de coleção dentro de um aplicativo Xamarin.tvOS. Primeiro, discutido todos os elementos que compõem a exibição de coleção. Em seguida, ele mostrou como projetar e implementar uma exibição de coleção usando um Storyboard. Por fim, é fornece links para informações sobre como criar layouts personalizados e reordenação de itens.



## <a name="related-links"></a>Links relacionados

- [Amostras do tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS guias de Interface Humana](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guia de programação de aplicativo para tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
