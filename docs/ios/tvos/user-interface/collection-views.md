---
title: Trabalhando com exibições de coleção no Xamarin do tvOS
description: Este documento descreve como trabalhar com exibições de coleção em um aplicativo tvOS criados com o Xamarin. Ele aborda a criação de células e modos de exibição suplementares, respondendo a eventos de usuário e muito mais, layouts de exibição de coleção.
ms.prod: xamarin
ms.assetid: 5125C4C7-2DDF-4C19-A362-17BB2B079178
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: f815afa6b1abb15348019b0c53333b4acb054008
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50108022"
---
# <a name="working-with-tvos-collection-views-in-xamarin"></a>Trabalhando com exibições de coleção no Xamarin do tvOS

Exibições de coleção permitem um grupo de conteúdo a ser exibido usando layouts arbitrários. Usando o suporte interno, eles permitem os layouts de grade ou linear criação fácil e também dar suporte a layouts personalizados.

[![](collection-views-images/collection01.png "Modo de exibição de coleção de exemplo")](collection-views-images/collection01.png#lightbox)

A exibição de coleção mantém uma coleção de itens usando um delegado e uma fonte de dados para fornecer a interação do usuário e o conteúdo da coleção. Como a exibição de coleção se baseia em um subsistema de Layout que é independente do modo de exibição em si, fornecer um Layout diferente pode alterar facilmente a apresentação de dados em interrupções do modo de exibição coleta.

<a name="About-Collection-Views" />

## <a name="about-collection-views"></a>Sobre exibições de coleção

Conforme mencionado acima, uma exibição de coleção (`UICollectionView`) gerencia uma coleção ordenada de itens e apresenta esses itens com layouts personalizáveis. Exibições de coleção funcionam de maneira semelhante às exibições de tabela (`UITableView`), exceto que eles podem usar layouts para itens presentes em mais do que apenas uma única coluna.

Ao usar uma exibição de coleção em tvOS, seu aplicativo é responsável por fornecer os dados associados à coleção usando uma fonte de dados (`UICollectionViewDataSource`). Exibir dados de coleção podem, opcionalmente, organizados e apresentados em grupos diferentes (seções).

A exibição de coleção apresenta os itens individuais na tela de uso de uma célula (`UICollectionViewCell`) que fornece a apresentação de uma determinada parte das informações da coleção (como uma imagem e seu título).

Opcionalmente, modos de exibição suplementares podem ser adicionados a apresentação do modo de exibição de coleção para atuar como cabeçalhos e rodapés para as seções e células. Layout do modo de exibição de coleção é responsável por definir o posicionamento dessas exibições, juntamente com as células individuais.

A exibição de coleção pode responder à interação do usuário usando um delegado (`UICollectionViewDelegate`). Este delegado também é responsável por determinar se uma determinada célula pode obter foco, se uma célula tiver sido realçada ou se foi selecionada. Em alguns casos, o delegado determina o tamanho das células individuais.

<a name="Collection-View-Layouts" />

## <a name="collection-view-layouts"></a>Layouts de exibição de coleção

Um recurso importante de uma exibição de coleção é sua separação entre os dados que ela está apresentando e seu Layout. Um Layout de exibição de coleção (`UICollectionViewLayout`) é responsável por fornecer a organização e o local de células (e quaisquer exibições suplementares) com apresentação de na tela do modo de exibição de coleção.

As células individuais são criadas pela exibição de coleção da fonte de dados anexados e, em seguida, são organizadas e exibidas pelo Layout de exibição de coleção determinada.

O Layout de exibição de coleção normalmente é fornecido quando a exibição de coleção é criada. No entanto, você pode alterar o Layout de exibição de coleção a qualquer momento e a apresentação na tela de dados do modo de exibição de coleção será atualizada automaticamente usando o novo layout fornecido.

O Layout de exibição de coleção fornece vários métodos que podem ser usados para animar a transição entre dois layouts diferentes (por padrão, que nenhuma animação é feita). Além disso, Layouts de exibição de coleção pode funcionar com reconhecedores de gestos para animar ainda mais a interação do usuário que resulta em uma alteração no layout.

<a name="Creating-Cells-and-Supplementary-Views" />

## <a name="creating-cells-and-supplementary-views"></a>Criando células e exibições de suplementares

Fonte de dados de uma exibição da coleção não só é responsável por fornecer os dados fazendo a coleção de itens, mas também as células que são usadas para exibir o conteúdo.

Como exibições de coleção foram projetadas para lidar com grandes coleções de itens, as células individuais podem ser removida da fila e reutilizadas para impedir que saturar limitações de memória. Há dois métodos diferentes para modos de exibição de retirar da fila:

- `DequeueReusableCell` -Cria ou retorna uma célula do tipo em questão (conforme especificado no Storyboard do aplicativo).
- `DequeueReusableSupplementaryView` -Cria ou retorna uma exibição suplementar do tipo em questão (conforme especificado no Storyboard do aplicativo).

Antes de chamar qualquer um desses métodos, você deve registrar a classe Storyboard ou `.xib` arquivo usado para criar o modo de exibição da célula com a exibição de coleção. Por exemplo:

```csharp
public CityCollectionView (IntPtr handle) : base (handle)
{
    // Initialize
    RegisterClassForCell (typeof(CityCollectionViewCell), CityViewDatasource.CardCellId);
    ...
}
```

Em que `typeof(CityCollectionViewCell)` fornece a classe que suporta a exibição e `CityViewDatasource.CardCellId` fornece a ID usada quando a célula (ou exibição) é removida da fila.

Depois que a célula é removida da fila, você pode configurá-lo com os dados para o item que ele é que representa e retorna à exibição de coleção para exibição.

<a name="About-Collection-View-Controllers" />

## <a name="about-collection-view-controllers"></a>Sobre controladores de exibição de coleção

Um controlador de exibição de coleção (`UICollectionViewController`) é um controlador de exibição especializados (`UIViewController`) que fornece o seguinte comportamento:

- Ele é responsável por carregar a exibição de coleção do seu Storyboard ou `.xib` arquivo e instanciar o modo de exibição. Se criada no código, ele cria automaticamente uma exibição de coleção nova e não configurado.
- Quando a exibição de coleção é carregada, o controlador tenta carregar sua fonte de dados e o delegado de Storyboard ou `.xib` arquivo. Se nenhuma estiver disponível, ele define, em si, como a origem de ambos.
- Garante que os dados são carregados antes que a exibição de coleção é preenchida no primeiro exibido e recarrega e desmarque select em cada vídeo subsequente.

Além disso, o controlador de exibição de coleção fornece métodos substituíveis que podem ser usados para gerenciar o ciclo de vida da exibição de coleção como `AwakeFromNib` e `ViewWillDisplay`.

<a name="Collection-Views-and-Storyboards" />

## <a name="collection-views-and-storyboards"></a>Exibições de coleção e storyboards

É a maneira mais fácil trabalhar com uma exibição de coleção em seu aplicativo tvos, adicionar uma ao seu Storyboard. Como um exemplo rápido, vamos criar um aplicativo de exemplo que apresenta uma imagem, título e um botão de seleção. Se o usuário clicar no botão Selecionar, uma exibição de coleção será exibida que permitem que o usuário escolha uma nova imagem. Quando uma imagem é escolhida, a exibição de coleção é fechada e a nova imagem e o título serão exibidos.

Vamos fazer o seguinte:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

    
1. Iniciar uma nova **aplicativo de modo de exibição único tvOS** no Visual Studio para Mac.
1. No **Gerenciador de soluções**, clique duas vezes o `Main.storyboard` de arquivo e abri-lo no Designer do iOS.
1. Adicionar um modo de exibição de imagem, um rótulo e um botão para o modo de exibição existente e configurá-los para a seguinte aparência: 

    [![](collection-views-images/collection02.png "Layout de exemplo")](collection-views-images/collection02.png#lightbox)
1. Atribuir uma **nome** para o modo de exibição de imagem e o rótulo na **guia de Widget** da **Gerenciador de propriedades**. Por exemplo: 

    [![](collection-views-images/collection03.png "O nome da configuração")](collection-views-images/collection03.png#lightbox)
1. Em seguida, arraste um controlador de exibição de coleção para o Storyboard: 

    [![](collection-views-images/collection04.png "Um controlador de exibição de coleção")](collection-views-images/collection04.png#lightbox)
1. Controle-arraste do botão para o controlador de exibição de coleção e selecione **Push** de pop-up: 

    [![](collection-views-images/collection05.png "Selecione o envio por Push de pop-up")](collection-views-images/collection05.png#lightbox)
1. Quando o aplicativo é executado, isso tornará o modo de exibição de coleção ser show sempre que o usuário clica no botão.
1. Selecione a exibição de coleção e insira os seguintes valores na **guia Layout** da **Gerenciador de propriedades**: 

    [![](collection-views-images/collection06.png "O Gerenciador de propriedades")](collection-views-images/collection06.png#lightbox)
1. Isso controla o tamanho de células individuais e as bordas entre células e a borda externa da exibição de coleção.
1. Selecione o controlador de exibição de coleção e defina sua classe como `CityCollectionViewController` no **guia de Widget**: 

    [![](collection-views-images/collection07.png "Defina a classe para CityCollectionViewController")](collection-views-images/collection07.png#lightbox)
1. Selecione a exibição de coleção e defina sua classe como `CityCollectionView` no **guia de Widget**: 

    [![](collection-views-images/collection08.png "Defina a classe para CityCollectionView")](collection-views-images/collection08.png#lightbox)
1. Selecione a célula de exibição de coleção e defina sua classe como `CityCollectionViewCell` no **guia de Widget**: 

    [![](collection-views-images/collection09.png "Defina a classe para CityCollectionViewCell")](collection-views-images/collection09.png#lightbox)
1. No **guia de Widget** Certifique-se de que o **Layout** é `Flow` e o **direção de rolagem** é `Vertical` para a exibição de coleção: 

    [![](collection-views-images/collection10.png "Na guia de Widget")](collection-views-images/collection10.png#lightbox)
1. Selecione a célula de exibição de coleção e defina suas **identidade** para `CityCell` no **guia Widget**: 

    [![](collection-views-images/collection11.png "Definir a identidade para CityCell")](collection-views-images/collection11.png#lightbox)
1. Salve as alterações.
    

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

    
1. Iniciar uma nova **aplicativo de modo de exibição único tvOS** no Visual Studio.
1. No **Gerenciador de soluções**, clique duas vezes o `Main.storyboard` de arquivo e abri-lo no Designer do iOS.
1. Adicionar um modo de exibição de imagem, um rótulo e um botão para o modo de exibição existente e configurá-los para a seguinte aparência: 

    [![](collection-views-images/collection02vs.png "Configure o layout")](collection-views-images/collection02vs.png#lightbox)
1. Atribuir uma **nome** para o modo de exibição de imagem e o rótulo na **guia de Widget** da **Gerenciador de propriedades**. Por exemplo: 

    [![](collection-views-images/collection03vs.png "O Gerenciador de propriedades")](collection-views-images/collection03vs.png#lightbox)
1. Em seguida, arraste um controlador de exibição de coleção para o Storyboard: 

    [![](collection-views-images/collection04vs.png "Um controlador de exibição de coleção")](collection-views-images/collection04vs.png#lightbox)
1. Controle-arraste do botão para o controlador de exibição de coleção e selecione **Push** de pop-up: 

    [![](collection-views-images/collection05vs.png "Selecione o envio por Push de pop-up")](collection-views-images/collection05vs.png#lightbox)
1. Quando o aplicativo é executado, isso tornará o modo de exibição de coleção ser show sempre que o usuário clica no botão.
1. Selecione a exibição de coleção e, na **guia Layout** da **Gerenciador de propriedades** inserir o **largura** como _361_ e  **Altura** como _256_ 
1. Isso controla o tamanho de células individuais e as bordas entre células e a borda externa da exibição de coleção.
1. Selecione o controlador de exibição de coleção e defina sua classe como `CityCollectionViewController` no **guia de Widget**: 

    [![](collection-views-images/collection07vs.png "Defina a classe para CityCollectionViewController")](collection-views-images/collection07vs.png#lightbox)
1. Selecione a exibição de coleção e defina sua classe como `CityCollectionView` no **guia de Widget**: 

    [![](collection-views-images/collection08vs.png "Defina a classe para CityCollectionView")](collection-views-images/collection08vs.png#lightbox)
1. Selecione a célula de exibição de coleção e defina sua classe como `CityCollectionViewCell` no **guia de Widget**: 

    [![](collection-views-images/collection09vs.png "Defina a classe para CityCollectionViewCell")](collection-views-images/collection09vs.png#lightbox)
1. No **guia de Widget** Certifique-se de que o **Layout** é `Flow` e o **direção de rolagem** é `Vertical` para a exibição de coleção: 

    [![](collection-views-images/collection10vs.png "Guia de Widget tNão")](collection-views-images/collection10vs.png#lightbox)
1. Selecione a célula de exibição de coleção e defina suas **identidade** para `CityCell` no **guia Widget**: 

    [![](collection-views-images/collection11vs.png "Definir a identidade para CityCell")](collection-views-images/collection11vs.png#lightbox)
1. Salve as alterações.
    

-----

Se tivéssemos escolhido `Custom` para o modo de exibição de coleção **Layout**, podemos ter especificado um layout personalizado. A Apple fornece uma interna `UICollectionViewFlowLayout` e `UICollectionViewDelegateFlowLayout` que pode facilmente apresentar dados em um layout de grade (eles são usados pelo `flow` estilo de layout). 

Para obter mais informações sobre como trabalhar com Storyboards, consulte nosso [Olá, guia de início rápido do tvOS](~/ios/tvos/get-started/hello-tvos.md).

<a name="Providing-Data-for-the-Collection-View" />

## <a name="providing-data-for-the-collection-view"></a>Fornece dados para a exibição de coleção

Agora que temos nossa exibição de coleção (e controlador de exibição de coleção) adicionado ao nosso Storyboard, precisamos fornecer os dados para a coleção. 

<a name="The-Data-Model" />

### <a name="the-data-model"></a>O modelo de dados

Primeiro, vamos criar um modelo para nossos dados que contém o nome do arquivo de imagem a ser exibida, o título e um sinalizador para permitir a cidade a ser selecionado.

Criar um `CityInfo` de classe e torná-lo semelhante ao seguinte:

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

Agora, precisamos definir como os dados serão apresentados para cada célula. Editar o `CityCollectionViewCell.cs` arquivo (criado para você automaticamente do seu arquivo de Storyboard) e torná-lo semelhante ao seguinte:

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

Para nosso aplicativo tvOS, podemos exibirá uma imagem e um título opcional. Se determinada cidade não pode ser selecionada, podemos são esmaecimento a exibição de imagem usando o seguinte código:

```csharp
CityView.Alpha = (City.CanSelect) ? 1.0f : 0.5f;
```

Quando a célula que contém a imagem é colocada em foco pelo usuário, que desejamos usar interno efeito Parallax nele definindo a propriedade a seguir:

```csharp
CityView.AdjustsImageWhenAncestorFocused = true;
```

Para obter mais informações sobre navegação e fogo, consulte nosso [trabalhando com navegação e fogo](~/ios/tvos/app-fundamentals/navigation-focus.md) e [Siri remoto e controladores de Bluetooth](~/ios/tvos/platform/remote-bluetooth.md) documentação.


<a name="The-Collection-View-Data-Provider" />

### <a name="the-collection-view-data-provider"></a>O provedor de dados de exibição de coleção

Com nosso modelo de dados criado e o layout de célula definida, vamos criar uma fonte de dados para nossa exibição de coleção. A fonte de dados será responsável por fornecer não somente os dados de backup, mas também retirar da fila as células para exibir as células individuais na tela.

Criar um `CityViewDatasource` de classe e torná-lo semelhante ao seguinte:

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

Deixe de examinar essa classe em detalhes. Primeiro, estamos herdam `UICollectionViewDataSource` e fornecer um atalho para a ID de células (que são atribuídos no Designer do iOS):

```csharp
public static NSString CardCellId = new NSString ("CityCell");
```

Em seguida, podemos fornecer armazenamento para nossos dados de coleção e fornecer uma classe para preencher os dados:

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

Em seguida, substituímos o `NumberOfSections` método e retornar o número de seções (grupos de itens) que exiba nossa coleção tem. Nesse caso, há apenas um:

```csharp
public override nint NumberOfSections (UICollectionView collectionView)
{
    return 1;
}
```

Em seguida, retornamos o número de itens em nossa coleção usando o seguinte código:

```csharp
public override nint GetItemsCount (UICollectionView collectionView, nint section)
{
    return Cities.Count;
}
```

Por fim, podemos remover da fila uma célula reutilizável quando a exibição de coleção de solicitação com o código a seguir:

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

Depois de recebermos uma célula de exibição de coleção de nosso `CityCollectionViewCell` tipo, podemos preenchê-lo com o item determinado.

<a name="Responding-to-User-Events" />

## <a name="responding-to-user-events"></a>Respondendo a eventos de usuário

Como queremos que o usuário seja capaz de selecionar um item de nossa coleção, precisamos fornecer um delegado de modo de exibição de coleção para lidar com essa interação. E é necessário fornecer uma maneira para deixar a nossa exibição chamada saber qual item o usuário selecionou.

<a name="The-App-Delegate" />

### <a name="the-app-delegate"></a>O representante do aplicativo

Precisamos de uma maneira para relacionar o item atualmente selecionado na exibição de coleção de volta para o modo de exibição de chamada. Usaremos uma propriedade personalizada no nosso `AppDelegate`. Editar o `AppDelegate.cs` arquivo e adicione o código a seguir:

```csharp
public CityInfo SelectedCity { get; set;} = new CityInfo("City02.jpg", "Turning Circle", true);
```

Isso define a propriedade e define a cidade de padrão que será exibida inicialmente. Posteriormente, podemos irá consumir essa propriedade para exibir a seleção do usuário e permitir que o select a ser alterado.

<a name="The-Collection-View-Delegate" />

### <a name="the-collection-view-delegate"></a>O delegado de modo de exibição de coleção

Em seguida, adicione um novo `CityViewDelegate` classe ao projeto e que ela fique semelhante ao seguinte:


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

Vamos examinar mais detalhadamente essa classe. Primeiro, estamos herdam `UICollectionViewDelegateFlowLayout`. O motivo pelo qual estamos herdam dessa classe e não a `UICollectionViewDelegate` é que estamos usando o interno `UICollectionViewFlowLayout` para apresentar os itens e não um tipo de layout personalizado.

Em seguida, retornamos o tamanho para os itens individuais usando este código:

```csharp
public override CGSize GetSizeForItem (UICollectionView collectionView, UICollectionViewLayout layout, NSIndexPath indexPath)
{
    return new CGSize (361, 256);
}
```

Em seguida, podemos decidir se uma determinada célula pode obter o foco usando o seguinte código: 

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

Verificamos para ver se uma determinada parte dos dados de backup tem seu `CanSelect` sinalizador definido como `true` e retornam esse valor. Para obter mais informações sobre navegação e fogo, consulte nosso [trabalhando com navegação e fogo](~/ios/tvos/app-fundamentals/navigation-focus.md) e [Siri remoto e controladores de Bluetooth](~/ios/tvos/platform/remote-bluetooth.md) documentação.

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

Aqui, definimos a `SelectedCity` propriedade do nosso `AppDelegate` para o item que o usuário selecionada e podemos fechar o controlador de exibição de coleção, retornando para o modo de exibição chamado conosco. Não definimos o `ParentController` propriedade nosso da exibição de coleção ainda, vamos fazer isto em seguida.

<a name="Configuring-the-Collection-View" />

## <a name="configuring-the-collection-view"></a>Configurando a exibição de coleção

Agora precisamos Editar nosso modo de exibição de coleção e atribuir nossa fonte de dados e o delegado. Editar o `CityCollectionView.cs` arquivo (criado para nós automaticamente do nosso Storyboard) e torná-lo semelhante ao seguinte:

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

Em seguida, fornecemos um atalho para fonte a exibição de coleção de dados e uma propriedade para acessar o controlador de exibição de coleção (usado pelo nosso delegado acima para fechar a coleção quando o usuário faz uma seleção):

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

Por fim, queremos que o título abaixo da imagem para só ficará visível quando o usuário tem que ele realçados (em foco). Fazemos isso com o código a seguir:

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

Definimos o transparence do item anterior perdendo o foco para zero (0) e o transparence do próximo item Obtenha o foco para 100%. Esses transição Introdução animada também.


## <a name="configuring-the-collection-view-controller"></a>Configurando o controlador de exibição de coleção

Agora precisamos fazer a configuração final na nossa exibição de coleção e permitir que o controlador definir a propriedade que definimos o modo de exibição de coleção pode ser fechada depois que o usuário faz uma seleção.

Editar o `CityCollectionViewController.cs` arquivo (criado automaticamente a partir de nossa Storyboard) e torná-lo semelhante ao seguinte:

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

## <a name="putting-it-all-together"></a>Juntando as peças 

Agora que temos todas as partes juntar para popular e controlar a exibição de nossa coleção, precisamos faça as edições no finais para nossa exibição principal para reunir tudo.

Editar o `ViewController.cs` arquivo (criado automaticamente a partir de nossa Storyboard) e torná-lo semelhante ao seguinte:

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

O código a seguir exibe inicialmente o item selecionado dos `SelectedCity` propriedade do `AppDelegate` e exibe-o novamente quando o usuário tiver feito uma seleção de exibição de coleção:

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

## <a name="testing-the-app"></a>Testar o aplicativo

Com tudo instalado, se você compilar e executa o aplicativo, o modo de exibição principal é exibido com a cidade de padrão:

[![](collection-views-images/run01.png "A tela principal")](collection-views-images/run01.png#lightbox)

Se o usuário clicar o **selecionar uma exibição** botão, a exibição de coleção será exibido:

[![](collection-views-images/run02.png "A exibição de coleção")](collection-views-images/run02.png#lightbox)

Qualquer cidade que tem seu `CanSelect` propriedade definida como `false` será exibido esmaecido e o usuário não será capaz de definir o foco para ele. Quando o usuário realça um item (torná-lo em foco) o título é exibido e eles podem usar o efeito da Parallax para inclinação sutileza a imagem em 3D.

Quando o usuário clica em uma seleção de imagem, a exibição de coleção é fechada e o modo de exibição principal é exibida novamente com a nova imagem:

[![](collection-views-images/run03.png "Uma nova imagem na tela inicial")](collection-views-images/run03.png#lightbox)

<a name="Creating-Custom-Layout-and-Reordering-Items" />

## <a name="creating-custom-layout-and-reordering-items"></a>Criando o Layout personalizado e reordenação de itens

Um dos principais recursos do uso de uma exibição de coleção é a capacidade de criar layouts personalizados. Uma vez que herda do tvOS do iOS, o processo para criar um layout personalizado é o mesmo. Consulte nosso [Introdução às exibições de coleção](~/ios/user-interface/controls/uicollectionview.md) documentação para obter mais informações.

Recentemente adicionada às exibições de coleção para iOS 9 foi a capacidade de permitir facilmente a reordenação de itens na coleção. Novamente, como tvOS 9 é um subconjunto do iOS 9, isso é feito-los mesma maneira. Consulte nosso [alterações de exibição de coleção](~/ios/user-interface/controls/uicollectionview.md) documento para obter mais detalhes.


<a name="Summary" />

## <a name="summary"></a>Resumo

Este artigo cobriu criando e trabalhando com exibições de coleção dentro de um aplicativo xamarin. tvos. Primeiro, ele discutiu a todos os elementos que compõem a exibição de coleção. Em seguida, ele mostrou como projetar e implementar uma exibição de coleção usando um Storyboard. Por fim, é fornecido links para informações sobre como criar layouts personalizados e reordenação de itens.



## <a name="related-links"></a>Links relacionados

- [Amostras do tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [Guias de Interface humana do tvOS](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guia de programação de aplicativo para tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
