---
title: Trabalhando com exibições de coleção tvOS no Xamarin
description: Este documento descreve como trabalhar com exibições de coleção em um aplicativo tvOS criado com o Xamarin. Ele aborda layouts de exibição de coleção, criação de células e exibições complementares, resposta a eventos de usuário e muito mais.
ms.prod: xamarin
ms.assetid: 5125C4C7-2DDF-4C19-A362-17BB2B079178
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/16/2017
ms.openlocfilehash: 2adc161742e7891fcce65cf2025a1b7c531c7a39
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91435237"
---
# <a name="working-with-tvos-collection-views-in-xamarin"></a>Trabalhando com exibições de coleção tvOS no Xamarin

As exibições de coleção permitem que um grupo de conteúdo seja exibido usando layouts arbitrários. Usando o suporte interno, eles permitem a criação fácil de layouts de grade ou lineares, além de oferecer suporte a layouts personalizados.

[![Exibição de coleção de exemplo](collection-views-images/collection01.png)](collection-views-images/collection01.png#lightbox)

A exibição de coleção mantém uma coleção de itens usando um delegado e uma fonte de dados para fornecer interação do usuário e o conteúdo da coleção. Como o modo de exibição de coleção é baseado em um subsistema de layout que é independente da própria exibição, fornecer um layout diferente pode facilmente alterar a apresentação dos dados do modo de exibição de coleção.

<a name="About-Collection-Views"></a>

## <a name="about-collection-views"></a>Sobre exibições de coleção

Como mencionado acima, um modo de exibição de coleção ( `UICollectionView` ) gerencia uma coleção ordenada de itens e apresenta esses itens com layouts personalizáveis. As exibições de coleção funcionam de maneira semelhante às exibições de tabela ( `UITableView` ), exceto pelo fato de poderem usar layouts para apresentar itens em mais do que apenas uma única coluna.

Ao usar uma exibição de coleção no tvOS, seu aplicativo é responsável por fornecer os dados associados à coleção usando uma fonte de dados ( `UICollectionViewDataSource` ). Os dados de exibição de coleção podem, opcionalmente, ser organizados e apresentados em grupos diferentes (seções).

A exibição de coleção apresenta os itens individuais na tela usando uma célula ( `UICollectionViewCell` ) que fornece a apresentação de uma determinada informação da coleção (como uma imagem e seu título).

Opcionalmente, exibições suplementares podem ser adicionadas à apresentação da exibição de coleção para atuar como cabeçalho e rodapé para as seções e células. O layout da exibição de coleção é responsável por definir o posicionamento dessas exibições junto com as células individuais.

O modo de exibição de coleção pode responder à interação do usuário usando um delegate ( `UICollectionViewDelegate` ). Esse delegado também é responsável por determinar se uma determinada célula pode ser focalizada, se uma célula tiver sido realçada ou se uma tiver sido selecionada. Em alguns casos, o delegado determina o tamanho das células individuais.

<a name="Collection-View-Layouts"></a>

## <a name="collection-view-layouts"></a>Layouts de exibição de coleção

Um recurso importante de uma exibição de coleção é sua separação entre os dados que ele está apresentando e seu layout. Um layout de exibição de coleção ( `UICollectionViewLayout` ) é responsável por fornecer a organização e o local das células (e quaisquer exibições complementares) com na apresentação na tela da exibição de coleção.

As células individuais são criadas pela exibição de coleção de sua fonte de dados anexada e, em seguida, são organizadas e exibidas pelo layout de exibição de coleção fornecido.

O layout de exibição de coleção normalmente é fornecido quando a exibição de coleção é criada. No entanto, você pode alterar o layout da exibição de coleção a qualquer momento e a apresentação na tela dos dados da exibição de coleção será atualizada automaticamente usando o novo layout fornecido.

O layout de exibição de coleção fornece vários métodos que podem ser usados para animar a transição entre dois layouts diferentes (por padrão, nenhuma animação é feita). Além disso, os layouts de exibição de coleção podem funcionar com reconhecedores de gesto para animar ainda mais a interação do usuário que resulta em uma alteração no layout.

<a name="Creating-Cells-and-Supplementary-Views"></a>

## <a name="creating-cells-and-supplementary-views"></a>Criando células e exibições suplementares

A fonte de dados de uma exibição de coleção não é apenas responsável por fornecer os dados de backup do item da coleção, mas também as células usadas para exibir o conteúdo.

Como as exibições de coleção foram projetadas para lidar com grandes coleções de itens, as células individuais podem ser removidas da fila e reutilizadas para evitar a sobreexecução de limitações de memória. Há dois métodos diferentes para remover as exibições da fila:

- `DequeueReusableCell` – Cria ou retorna uma célula do tipo fornecido (conforme especificado no storyboard do aplicativo).
- `DequeueReusableSupplementaryView` -Cria ou retorna uma exibição suplementar do tipo fornecido (conforme especificado no storyboard do aplicativo).

Antes de chamar qualquer um desses métodos, você deve registrar a classe, o storyboard ou o `.xib` arquivo usado para criar a exibição da célula com o modo de exibição de coleção. Por exemplo:

```csharp
public CityCollectionView (IntPtr handle) : base (handle)
{
    // Initialize
    RegisterClassForCell (typeof(CityCollectionViewCell), CityViewDatasource.CardCellId);
    ...
}
```

Onde `typeof(CityCollectionViewCell)` fornece a classe que dá suporte à exibição e `CityViewDatasource.CardCellId` fornece a ID usada quando a célula (ou exibição) é removida da fila.

Depois que a célula é removida da fila, você a configura com os dados do item que está representando e retorna à exibição de coleção para exibição.

<a name="About-Collection-View-Controllers"></a>

## <a name="about-collection-view-controllers"></a>Sobre os controladores de exibição de coleção

Um controlador de exibição de coleção ( `UICollectionViewController` ) é um controlador de exibição especializado ( `UIViewController` ) que fornece o seguinte comportamento:

- É responsável por carregar o modo de exibição de coleção de seu storyboard ou `.xib` arquivo e instanciar a exibição. Se criado no código, ele cria automaticamente uma nova exibição de coleção não configurada.
- Quando o modo de exibição de coleção é carregado, o controlador tenta carregar sua fonte de dados e delegar a partir do storyboard ou do `.xib` arquivo. Se nenhum estiver disponível, ele se definirá como a origem de ambos.
- Garante que os dados sejam carregados antes que a exibição de coleção seja preenchida na primeira exibição e recarrega e desmarque a seleção em cada exibição subsequente.

Além disso, o controlador de exibição de coleção fornece métodos substituíveis que podem ser usados para gerenciar o ciclo de vida da exibição de coleção, como `AwakeFromNib` e `ViewWillDisplay` .

<a name="Collection-Views-and-Storyboards"></a>

## <a name="collection-views-and-storyboards"></a>Exibições de coleção e storyboards

A maneira mais fácil de trabalhar com uma exibição de coleção em seu aplicativo Xamarin. tvOS é adicionar uma a seu Storyboard. Como um exemplo rápido, vamos criar um aplicativo de exemplo que apresenta uma imagem, um título e um botão de seleção. Se o usuário clicar no botão Selecionar, será exibido um modo de exibição de coleção que permitirá que o usuário escolha uma nova imagem. Quando uma imagem é escolhida, a exibição de coleção é fechada e a nova imagem e o título serão exibidos.

Vamos fazer o seguinte:

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

1. Inicie um novo **aplicativo tvOS de exibição única** no Visual Studio para Mac.
1. Na **Gerenciador de soluções**, clique duas vezes no `Main.storyboard` arquivo e abra-o no designer do Ios.
1. Adicione um modo de exibição de imagem, um rótulo e um botão à exibição existente e configure-os para que se pareçam com o seguinte: 

    [![Layout de exemplo](collection-views-images/collection02.png)](collection-views-images/collection02.png#lightbox)
1. Atribua um **nome** à exibição de imagem e o rótulo na **Guia Widget** do Gerenciador de **Propriedades**. Por exemplo: 

    [![Definindo o nome](collection-views-images/collection03.png)](collection-views-images/collection03.png#lightbox)
1. Em seguida, arraste um controlador de exibição de coleção para o storyboard: 

    [![Um controlador de exibição de coleção](collection-views-images/collection04.png)](collection-views-images/collection04.png#lightbox)
1. Controle-arraste do botão para o controlador de exibição de coleção e selecione **Push** do Popup: 

    [![Selecionar push do pop-up](collection-views-images/collection05.png)](collection-views-images/collection05.png#lightbox)
1. Quando o aplicativo for executado, isso fará com que o modo de exibição de coleção seja exibido sempre que o usuário clicar no botão.
1. Selecione o modo de exibição de coleção e insira os seguintes valores na **guia layout** do **Gerenciador de propriedades**: 

    [![O Gerenciador de propriedades](collection-views-images/collection06.png)](collection-views-images/collection06.png#lightbox)
1. Isso controla o tamanho das células individuais e as bordas entre as células e a borda externa da exibição de coleção.
1. Selecione o controlador de exibição de coleção e defina sua classe como `CityCollectionViewController` na **Guia do widget**: 

    [![Definir a classe como CityCollectionViewController](collection-views-images/collection07.png)](collection-views-images/collection07.png#lightbox)
1. Selecione o modo de exibição de coleção e defina sua classe como `CityCollectionView` na **Guia Widget**: 

    [![Definir a classe como CityCollectionView](collection-views-images/collection08.png)](collection-views-images/collection08.png#lightbox)
1. Selecione a célula de exibição de coleção e defina sua classe como `CityCollectionViewCell` na **Guia Widget**: 

    [![Definir a classe como CityCollectionViewCell](collection-views-images/collection09.png)](collection-views-images/collection09.png#lightbox)
1. Na **Guia do widget** , verifique se o **layout** é `Flow` e se a direção da **rolagem** é `Vertical` para o modo de exibição de coleção: 

    [![A guia do widget](collection-views-images/collection10.png)](collection-views-images/collection10.png#lightbox)
1. Selecione a célula de exibição de coleção e defina sua **identidade** como `CityCell` na **Guia do widget**: 

    [![Defina a identidade como CityCell](collection-views-images/collection11.png)](collection-views-images/collection11.png#lightbox)
1. Salve as alterações.

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. Inicie um novo **aplicativo tvOS de exibição única** no Visual Studio.
1. Na **Gerenciador de soluções**, clique duas vezes no `Main.storyboard` arquivo e abra-o no designer do Ios.
1. Adicione um modo de exibição de imagem, um rótulo e um botão à exibição existente e configure-os para que se pareçam com o seguinte: 

    [![Configurar o layout](collection-views-images/collection02vs.png)](collection-views-images/collection02vs.png#lightbox)
1. Atribua um **nome** à exibição de imagem e o rótulo na **Guia Widget** do Gerenciador de **Propriedades**. Por exemplo: 

    [![O Gerenciador de propriedades](collection-views-images/collection03vs.png)](collection-views-images/collection03vs.png#lightbox)
1. Em seguida, arraste um controlador de exibição de coleção para o storyboard: 

    [![Um controlador de exibição de coleção](collection-views-images/collection04vs.png)](collection-views-images/collection04vs.png#lightbox)
1. Controle-arraste do botão para o controlador de exibição de coleção e selecione **Push** do Popup: 

    [![Selecionar push do pop-up](collection-views-images/collection05vs.png)](collection-views-images/collection05vs.png#lightbox)
1. Quando o aplicativo for executado, isso fará com que o modo de exibição de coleção seja exibido sempre que o usuário clicar no botão.
1. Selecione o modo de exibição de coleção e, na **guia layout** do **Gerenciador de propriedades** , insira a **largura** como _361_ e **altura** como _256_ 
1. Isso controla o tamanho das células individuais e as bordas entre as células e a borda externa da exibição de coleção.
1. Selecione o controlador de exibição de coleção e defina sua classe como `CityCollectionViewController` na **Guia do widget**: 

    [![Definir a classe como CityCollectionViewController](collection-views-images/collection07vs.png)](collection-views-images/collection07vs.png#lightbox)
1. Selecione o modo de exibição de coleção e defina sua classe como `CityCollectionView` na **Guia Widget**: 

    [![Definir a classe como CityCollectionView](collection-views-images/collection08vs.png)](collection-views-images/collection08vs.png#lightbox)
1. Selecione a célula de exibição de coleção e defina sua classe como `CityCollectionViewCell` na **Guia Widget**: 

    [![Definir a classe como CityCollectionViewCell](collection-views-images/collection09vs.png)](collection-views-images/collection09vs.png#lightbox)
1. Na **Guia do widget** , verifique se o **layout** é `Flow` e se a direção da **rolagem** é `Vertical` para o modo de exibição de coleção: 

    [![Guia do widget Tnão](collection-views-images/collection10vs.png)](collection-views-images/collection10vs.png#lightbox)
1. Selecione a célula de exibição de coleção e defina sua **identidade** como `CityCell` na **Guia do widget**: 

    [![Defina a identidade como CityCell](collection-views-images/collection11vs.png)](collection-views-images/collection11vs.png#lightbox)
1. Salve as alterações.

-----

Se tivéssemos escolhido `Custom` o **layout**do modo de exibição de coleção, poderíamos ter especificado um layout personalizado. A Apple fornece um interno `UICollectionViewFlowLayout` e `UICollectionViewDelegateFlowLayout` que pode apresentar facilmente dados em um layout baseado em grade (eles são usados pelo estilo de `flow` layout). 

Para obter mais informações sobre como trabalhar com storyboards, consulte nossa [Guia de início rápido Olá, tvOS](~/ios/tvos/get-started/hello-tvos.md).

<a name="Providing-Data-for-the-Collection-View"></a>

## <a name="providing-data-for-the-collection-view"></a>Fornecendo dados para o modo de exibição de coleção

Agora que temos nossa exibição de coleção (e o controlador de exibição de coleção) adicionados ao nosso storyboard, precisamos fornecer os dados para a coleção. 

<a name="The-Data-Model"></a>

### <a name="the-data-model"></a>O modelo de dados

Primeiro, vamos criar um modelo para nossos dados que contêm o nome do arquivo para a imagem a ser exibida, o título e um sinalizador para permitir que a cidade seja selecionada.

Crie uma `CityInfo` classe e faça com que ela fique parecida com a seguinte:

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

Agora, precisamos definir como os dados serão apresentados para cada célula. Edite o `CityCollectionViewCell.cs` arquivo (criado para você automaticamente do seu arquivo de storyboard) e faça com que ele fique semelhante ao seguinte:

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

Para nosso aplicativo tvOS, exibiremos uma imagem e um título opcional. Se a cidade fornecida não puder ser selecionada, estamos desativando o modo de exibição de imagem usando o código a seguir:

```csharp
CityView.Alpha = (City.CanSelect) ? 1.0f : 0.5f;
```

Quando a célula que contém a imagem é colocada em foco pelo usuário, queremos usar o efeito interno de da Parallax em ti para definir a seguinte propriedade:

```csharp
CityView.AdjustsImageWhenAncestorFocused = true;
```

Para obter mais informações sobre navegação e foco, consulte nossa documentação sobre [como trabalhar com navegação e foco](~/ios/tvos/app-fundamentals/navigation-focus.md) e [controladores de Siri remotos e Bluetooth](~/ios/tvos/platform/remote-bluetooth.md) .

<a name="The-Collection-View-Data-Provider"></a>

### <a name="the-collection-view-data-provider"></a>O modo de exibição de coleção Provedor de Dados

Com nosso modelo de dados criado e nosso layout de célula definido, vamos criar uma fonte de dados para nossa exibição de coleção. A fonte de dados será responsável por não apenas fornecer os dados de backup, mas também removerá a fila das células para exibir as células individuais na tela.

Crie uma `CityViewDatasource` classe e faça com que ela fique parecida com a seguinte:

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

Vamos examinar essa classe detalhadamente. Primeiro, herdamos de `UICollectionViewDataSource` e fornecemos um atalho para a ID de células (que atribuímos no designer do IOS):

```csharp
public static NSString CardCellId = new NSString ("CityCell");
```

Em seguida, fornecemos armazenamento para nossos dados de coleta e fornecemos uma classe para popular os dados:

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

Em seguida, substituimos o `NumberOfSections` método e retornamos o número de seções (grupos de itens) que nossa exibição de coleção tem. Nesse caso, há apenas um:

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

Por fim, desenfileiramos uma célula reutilizável quando a solicitação de exibição de coleção com o seguinte código:

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

Depois de obtermos uma célula de exibição de coleção do nosso `CityCollectionViewCell` tipo, preenchemos isso com o item fornecido.

<a name="Responding-to-User-Events"></a>

## <a name="responding-to-user-events"></a>Respondendo a eventos de usuário

Como queremos que o usuário seja capaz de selecionar um item da nossa coleção, precisamos fornecer um delegado de exibição de coleção para lidar com essa interação. E precisamos fornecer uma maneira de permitir que nossa exibição de chamada saiba qual item o usuário selecionou.

<a name="The-App-Delegate"></a>

### <a name="the-app-delegate"></a>O representante do aplicativo

Precisamos de uma maneira de relacionar o item atualmente selecionado da exibição de coleção de volta ao modo de exibição de chamada. Vamos usar uma propriedade personalizada em nosso `AppDelegate` . Edite o `AppDelegate.cs` arquivo e adicione o seguinte código:

```csharp
public CityInfo SelectedCity { get; set;} = new CityInfo("City02.jpg", "Turning Circle", true);
```

Isso define a propriedade e define a cidade padrão que será mostrada inicialmente. Posteriormente, vamos consumir essa propriedade para exibir a seleção do usuário e permitir que o Select seja alterado.

<a name="The-Collection-View-Delegate"></a>

### <a name="the-collection-view-delegate"></a>O delegado de exibição de coleção

Em seguida, adicione uma nova `CityViewDelegate` classe ao projeto e faça com que seja semelhante ao seguinte:

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

Vamos examinar mais de perto essa classe. Primeiro, herdamos de `UICollectionViewDelegateFlowLayout` . O motivo pelo qual herdamos dessa classe e não `UICollectionViewDelegate` é que estamos usando o interno `UICollectionViewFlowLayout` para apresentar nossos itens e não um tipo de layout personalizado.

Em seguida, retornamos o tamanho dos itens individuais usando este código:

```csharp
public override CGSize GetSizeForItem (UICollectionView collectionView, UICollectionViewLayout layout, NSIndexPath indexPath)
{
    return new CGSize (361, 256);
}
```

Em seguida, decidimos se uma determinada célula pode obter foco usando o seguinte código: 

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

Verificamos se uma determinada parte dos dados de backup tem seu `CanSelect` sinalizador definido como `true` e retorna esse valor. Para obter mais informações sobre navegação e foco, consulte nossa documentação sobre [como trabalhar com navegação e foco](~/ios/tvos/app-fundamentals/navigation-focus.md) e [controladores de Siri remotos e Bluetooth](~/ios/tvos/platform/remote-bluetooth.md) .

Por fim, respondemos ao usuário selecionando um item com o seguinte código:

```csharp
public override void ItemSelected (UICollectionView collectionView, NSIndexPath indexPath)
{
    var controller = collectionView as CityCollectionView;
    App.SelectedCity = controller.Source.Cities [indexPath.Row];

    // Close Collection
    controller.ParentController.DismissViewController(true,null);
}
```

Aqui, definimos a `SelectedCity` propriedade de nosso `AppDelegate` para o item selecionado pelo usuário e fechamos o controlador de exibição de coleção, retornando à exibição que nos chamou. Ainda não definimos a `ParentController` propriedade de nossa exibição de coleção, faremos isso em seguida.

<a name="Configuring-the-Collection-View"></a>

## <a name="configuring-the-collection-view"></a>Configurando o modo de exibição de coleção

Agora, precisamos editar nossa exibição de coleção e atribuir nossa fonte de dados e delegar. Edite o `CityCollectionView.cs` arquivo (criado para nós automaticamente de nosso storyboard) e faça com que ele se pareça com o seguinte:

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

Primeiro, fornecemos um atalho para acessar nosso `AppDelegate` : 

```csharp
public static AppDelegate App {
    get { return (AppDelegate)UIApplication.SharedApplication.Delegate; }
}
```

Em seguida, fornecemos um atalho para a fonte de dados da exibição de coleção e uma propriedade para acessar o controlador de exibição de coleção (usado pelo nosso delegado acima para fechar a coleção quando o usuário faz uma seleção):

```csharp
public CityViewDatasource Source {
    get { return DataSource as CityViewDatasource;}
}

public CityCollectionViewController ParentController { get; set;}
```

Em seguida, usamos o seguinte código para inicializar a exibição de coleção e atribuir nossa classe de célula, a fonte de dados e o delegado:

```csharp
public CityCollectionView (IntPtr handle) : base (handle)
{
    // Initialize
    RegisterClassForCell (typeof(CityCollectionViewCell), CityViewDatasource.CardCellId);
    DataSource = new CityViewDatasource (this);
    Delegate = new CityViewDelegate ();
}
```

Por fim, queremos que o título sob a imagem fique visível apenas quando o usuário o tiver realçado (em foco). Fazemos isso com o código a seguir:

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

Definimos a transparência do item anterior que está perdendo o foco para zero (0) e a transparência do próximo item se concentrará em 100%. Essas transições também são animadas.

## <a name="configuring-the-collection-view-controller"></a>Configurando o controlador de exibição de coleção

Agora, precisamos fazer a configuração final em nossa exibição de coleção e permitir que o controlador defina a propriedade que definimos para que o modo de exibição de coleção possa ser fechado depois que o usuário fizer uma seleção.

Edite o `CityCollectionViewController.cs` arquivo (criado automaticamente de nosso storyboard) e faça com que ele se pareça com o seguinte:

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

## <a name="putting-it-all-together"></a>Juntando tudo 

Agora que temos todas as partes reunidas para preencher e controlar nossa exibição de coleção, precisamos fazer as edições finais em nosso modo de exibição principal para reunir tudo.

Edite o `ViewController.cs` arquivo (criado automaticamente de nosso storyboard) e faça com que ele se pareça com o seguinte:

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

O código a seguir inicialmente exibe o item selecionado da `SelectedCity` Propriedade do `AppDelegate` e o exibe novamente quando o usuário fez uma seleção na exibição de coleção:

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

<a name="Testing-the-app"></a>

## <a name="testing-the-app"></a>Testando o aplicativo

Com tudo em vigor, se você criar e executar o aplicativo, o modo de exibição principal será exibido com a cidade padrão:

[![A tela principal](collection-views-images/run01.png)](collection-views-images/run01.png#lightbox)

Se o usuário clicar no botão **selecionar uma exibição** , o modo de exibição de coleção será exibido:

[![O modo de exibição de coleção](collection-views-images/run02.png)](collection-views-images/run02.png#lightbox)

Qualquer cidade que tenha sua `CanSelect` propriedade definida como `false` será exibida esmaecida e o usuário não poderá definir o foco para ela. Quando o usuário realça um item (torná-lo em foco), o título é exibido e eles podem usar o efeito de da Parallax para inclinar a imagem em 3D.

Quando o usuário clica em uma imagem de seleção, o modo de exibição de coleção é fechado e o modo de exibição principal é exibido novamente com a nova imagem:

[![Uma nova imagem na tela inicial](collection-views-images/run03.png)](collection-views-images/run03.png#lightbox)

<a name="Creating-Custom-Layout-and-Reordering-Items"></a>

## <a name="creating-custom-layout-and-reordering-items"></a>Criando layout personalizado e reordenando itens

Um dos principais recursos do uso de uma exibição de coleção é a capacidade de criar layouts personalizados. Como o tvOS é herdado do iOS, o processo de criação de um layout personalizado é o mesmo. Consulte nossa [introdução à documentação de exibições de coleção](~/ios/user-interface/controls/uicollectionview.md) para obter mais informações.

Recentemente adicionados às exibições de coleção para o iOS 9 foi a capacidade de permitir facilmente a reordenação de itens na coleção. Novamente, como o tvOS 9 é um subconjunto do iOS 9, isso é feito da mesma maneira. Consulte nosso documento de [alterações de exibição de coleção](~/ios/user-interface/controls/uicollectionview.md) para obter mais detalhes.

<a name="Summary"></a>

## <a name="summary"></a>Resumo

Este artigo abordou a criação e o trabalho com exibições de coleção dentro de um aplicativo Xamarin. tvOS. Primeiro, ele abordou todos os elementos que compõem o modo de exibição de coleção. Em seguida, ele mostrou como projetar e implementar uma exibição de coleção usando um Storyboard. Por fim, o recebe links para informações sobre a criação de layouts personalizados e a reordenação de itens.

## <a name="related-links"></a>Links Relacionados

- [Exemplos do tvOS](/samples/browse/?products=xamarin&term=Xamarin.iOS%2btvOS)
- [tvOS](https://developer.apple.com/tvos/)
- [Guias de interface humana do tvOS](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guia de programação de aplicativo para tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)