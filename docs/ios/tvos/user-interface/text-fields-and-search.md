---
title: Trabalhando com campos de pesquisa e texto tvOS no Xamarin
description: Este documento descreve como trabalhar com campos de pesquisa e texto em um aplicativo tvOS criado com o Xamarin. Ele fornece uma visão geral de alto nível dos campos de texto e de pesquisa e discute os teclados, a integração do storyboard, os modelos de dados de pesquisa e muito mais.
ms.prod: xamarin
ms.assetid: 9EE63CA6-2F31-4EE0-AAE5-82E18CFAC06C
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/16/2017
ms.openlocfilehash: cfe747d89b3ccbff390326c915dc1311e40a60fc
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73022160"
---
# <a name="working-with-tvos-text-and-search-fields-in-xamarin"></a>Trabalhando com campos de pesquisa e texto tvOS no Xamarin

Quando necessário, seu aplicativo Xamarin. tvOS pode solicitar pequenas partes de texto do usuário (como IDs de usuário e senhas) usando um campo de texto e o teclado na tela:

[![](text-fields-and-search-images/intro01.png "Sample Search Field")](text-fields-and-search-images/intro01.png#lightbox)

Opcionalmente, você pode fornecer a capacidade de pesquisa de palavra-chave do conteúdo do aplicativo usando um campo de pesquisa:

[![](text-fields-and-search-images/intro02.png "Sample Search Results")](text-fields-and-search-images/intro02.png#lightbox)

Este documento abordará os detalhes de como trabalhar com campos de pesquisa e texto em um aplicativo Xamarin. tvOS.

<a name="About-Text-and-Search-Fields" />

## <a name="about-text-and-search-fields"></a>Sobre texto e campos de pesquisa

Como mencionado acima, se necessário, o Xamarin. tvOS pode apresentar um ou mais campos de texto para coletar pequenas quantidades de texto do usuário usando uma tela (ou um teclado Bluetooth opcional, dependendo da versão do tvOS que o usuário instalou).

Além disso, se seu aplicativo apresentar grandes quantidades de conteúdo ao usuário (como música, filmes ou uma coleção de imagens), talvez você queira incluir um campo de pesquisa que permita ao usuário inserir uma pequena quantidade de texto para filtrar a lista de itens disponíveis.

<a name="Text-Fields" />

## <a name="text-fields"></a>Campos de texto

No tvOS, um campo de texto é apresentado como uma caixa de entrada de canto arredondado de altura fixa que abrirá um teclado virtual quando o usuário clicar nele:

[![](text-fields-and-search-images/text01.png "Text Fields In tvOS")](text-fields-and-search-images/text01.png#lightbox)

Quando o usuário move o [foco](~/ios/tvos/app-fundamentals/navigation-focus.md) para um determinado campo de texto, ele aumentará maior e exibirá uma sombra profunda. Você precisará ter isso em mente ao criar a interface do usuário, pois os campos de texto podem sobrepor outros elementos da interface de usuário quando estiverem em foco.

A Apple tem as seguintes sugestões para trabalhar com campos de texto:

- **Use a entrada de texto com moderação** devido à natureza do teclado na tela, inserindo seções longas de texto ou preenchendo vários campos de texto é entediante ao usuário. Uma solução melhor é limitar a quantidade de entrada de texto usando [botões](~/ios/tvos/user-interface/buttons.md)ou listas de seleção.
- **Usar dicas para comunicar** o campo de texto de finalidade pode exibir o espaço reservado "dicas" quando estiver vazio. Quando aplicável, use dicas para descrever a finalidade do campo de texto em vez de um rótulo separado.
- **Selecione o tipo de teclado padrão apropriado-o** tvOS fornece vários tipos de teclado diferentes para fins criados que você pode especificar para o campo de texto. Por exemplo, o teclado de endereço de email pode facilitar a entrada permitindo que o usuário selecione em uma lista de endereços inseridos recentemente.
- **Quando apropriado, use campos de texto seguro** – um campo de texto seguro apresenta os caracteres inseridos como pontos (em vez das letras reais). Sempre use um campo de texto seguro ao reunir informações confidenciais, como senhas.

<a name="Keyboards" />

## <a name="keyboards"></a>Teclados

Sempre que o usuário clica em um campo de texto na interface do usuário, um teclado na tela linear é exibido. O usuário usa a superfície de toque [Siri remoto](~/ios/tvos/platform/remote-bluetooth.md#The-Siri-Remote) para selecionar as letras individuais do teclado e inserir as informações solicitadas:

[![](text-fields-and-search-images/keyboard01.png "The Siri Remote keyboard")](text-fields-and-search-images/keyboard01.png#lightbox)

Se houver mais de um campo de texto na exibição atual, um botão **Avançar** será exibido automaticamente para levar o usuário para o próximo campo de texto. Um botão **concluído** será exibido para o último campo de texto que encerrará a entrada de texto e retornará o usuário para a tela anterior.

A qualquer momento, o usuário também pode pressionar o botão de **menu** na entrada de texto remoto Siri para extremidade e retornar novamente à tela anterior.

A Apple tem as seguintes sugestões para trabalhar com teclados na tela:

- **Selecione o tipo de teclado padrão apropriado-o** tvOS fornece vários tipos de teclado diferentes para fins criados que você pode especificar para o campo de texto. Por exemplo, o teclado de endereço de email pode facilitar a entrada permitindo que o usuário selecione em uma lista de endereços inseridos recentemente.
- **Quando apropriado, use exibições de acessório de teclado** – além das informações padrão que são sempre exibidas, exibições de acessório opcionais (como imagens ou rótulos) podem ser adicionadas ao teclado na tela para esclarecer a finalidade da entrada de texto ou para auxiliar o usuário está inserindo as informações necessárias.

Para obter mais informações sobre como trabalhar com o teclado virtual, consulte [UIKeyboardType](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UITextInputTraits_Protocol/index.html#//apple_ref/c/tdef/UIKeyboardType)da Apple, [Gerenciando o teclado](https://developer.apple.com/library/tvos/documentation/StringsTextFonts/Conceptual/TextAndWebiPhoneOS/KeyboardManagement/KeyboardManagement.html#//apple_ref/doc/uid/TP40009542-CH5-SW1), [modos de exibição personalizados para entrada de dados](https://developer.apple.com/library/tvos/documentation/StringsTextFonts/Conceptual/TextAndWebiPhoneOS/InputViews/InputViews.html#//apple_ref/doc/uid/TP40009542-CH12-SW1) e [Guia de programação de texto para a](https://developer.apple.com/library/tvos/documentation/StringsTextFonts/Conceptual/TextAndWebiPhoneOS/Introduction/Introduction.html) documentação do Ios.

<a name="Search" />

## <a name="search"></a>Pesquisar

Um campo de pesquisa apresenta uma tela especializada que fornece um campo de texto e um teclado na tela que permite ao usuário filtrar uma coleção de itens que são exibidos abaixo do teclado:

[![](text-fields-and-search-images/search01.png "Sample search results")](text-fields-and-search-images/search01.png#lightbox)

À medida que o usuário insere letras no campo de pesquisa, os resultados abaixo refletem automaticamente os resultados da pesquisa. A qualquer momento, o usuário pode deslocar o foco para os resultados e selecionar um dos itens apresentados.

A Apple tem as seguintes sugestões para trabalhar com campos de pesquisa:

- **Fornecer pesquisas recentes** – como a inserção de texto com o Siri remoto pode ser entediante e os usuários tendem a repetir solicitações de pesquisa, considere adicionar uma seção de resultados de pesquisa recentes antes dos resultados atuais na área do teclado.
- **Quando possível, limite o número de resultados** – como uma grande lista de itens pode ser difícil para o usuário analisar e navegar, considere limitar o número de resultados retornados.
- **Se apropriado, forneça filtros de resultados de pesquisa** – se o conteúdo fornecido pelo seu aplicativo se presta, considere adicionar barras de escopo para permitir que o usuário filtre ainda mais os resultados da pesquisa retornados.

Para obter mais informações, consulte a [referência de classe UISearchController](https://developer.apple.com/library/tvos/documentation/UIKit/Reference/UISearchController/index.html)da Apple.

<a name="Working-with-Text-Fields" />

## <a name="working-with-text-fields"></a>Trabalhando com campos de texto

A maneira mais fácil de trabalhar com campos de texto em um aplicativo Xamarin. tvOS é adicioná-los ao design da interface do usuário usando o designer do iOS.

Faça o seguinte:

<!-- markdownlint-disable MD001 -->

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

1. No **painel de soluções**, clique duas vezes no arquivo `Main.storyboard` para abri-lo para edição.
1. Arraste um ou mais **campos de texto** int a superfície de design para uma exibição:

    [![](text-fields-and-search-images/text02.png "A Text Field")](text-fields-and-search-images/text02.png#lightbox)
1. Selecione os **campos de texto** e dê a cada um um **nome** exclusivo na guia **widget** da **painel de propriedades**:

    [![](text-fields-and-search-images/text03.png "The Widget tab of the Properties Pad")](text-fields-and-search-images/text03.png#lightbox)
1. Na seção **campo de texto** , você pode definir elementos como a dica de **espaço reservado** e o **valor**padrão:

    [![](text-fields-and-search-images/text04.png "The Text Field section")](text-fields-and-search-images/text04.png#lightbox)
1. Role para baixo para definir propriedades, como **verificação ortográfica**, **maiúsculas e minúsculas** , **tipo de teclado**padrão:

    [![](text-fields-and-search-images/text05.png "Spell Checking, Capitalization and the default Keyboard Type")](text-fields-and-search-images/text05.png#lightbox)
1. Salve as alterações em seu Storyboard.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. No **Gerenciador de Soluções**, clique duas vezes no arquivo `Main.storyboard` para abri-lo para edição.
1. Arraste um ou mais **campos de texto** int a superfície de design para uma exibição:

    [![](text-fields-and-search-images/text02-vs.png "A Text Field")](text-fields-and-search-images/text02-vs.png#lightbox)
1. Selecione os **campos de texto** e dê a cada um um **nome** exclusivo na guia **widget** do **Gerenciador de propriedades**:

    [![](text-fields-and-search-images/text03-vs.png "The Widget tab")](text-fields-and-search-images/text03-vs.png#lightbox)
1. Na seção **campo de texto** , você pode definir elementos como a dica de **espaço reservado** e o **valor**padrão:

    [![](text-fields-and-search-images/text04-vs.png "The Text Field section")](text-fields-and-search-images/text04-vs.png#lightbox)
1. Role para baixo para definir propriedades, como **verificação ortográfica**, **maiúsculas e minúsculas** , **tipo de teclado**padrão:

    [![](text-fields-and-search-images/text05-vs.png "Spell Checking, Capitalization and the default Keyboard Type")](text-fields-and-search-images/text05-vs.png#lightbox)
1. Salve as alterações em seu Storyboard.

-----

No código, você pode obter ou definir o valor de um campo de texto usando sua propriedade `Text`:

```csharp
Console.WriteLine ("User ID {0} and Password {1}", UserId.Text, Password.Text);
```

Opcionalmente, você pode usar o `Started` e `Ended` eventos de campo de texto para responder à entrada de texto Iniciando e terminando.

<a name="Working-with-Search-Fields" />

## <a name="working-with-search-fields"></a>Trabalhando com campos de pesquisa

A maneira mais fácil de trabalhar com campos de pesquisa em um aplicativo Xamarin. tvOS é adicioná-los ao design da interface do usuário usando o designer de interface.

Faça o seguinte:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

1. No **painel de soluções**, clique duas vezes no arquivo `Main.storyboard` para abri-lo para edição.
1. Arraste um novo controlador de exibição de coleção para o storyboard para apresentar os resultados da pesquisa do usuário:

    [![](text-fields-and-search-images/search02.png "A Collection View Controller")](text-fields-and-search-images/search02.png#lightbox)
1. Na guia do **widget** do **Painel de Propriedades**, use `SearchResultsViewController` para a **classe** e `SearchResults` para a **ID do storyboard**:

    [![](text-fields-and-search-images/search03.png "The Widget tab")](text-fields-and-search-images/search03.png#lightbox)
1. Selecione o **protótipo de célula** na superfície de design.
1. Na guia **widget** do Gerenciador de **propriedades**, use `SearchResultCell` para a **classe** e `ImageCell` para o **identificador**:

    [![](text-fields-and-search-images/search04.png "The Widget tab")](text-fields-and-search-images/search04.png#lightbox)
1. Layout o design do **protótipo de célula** e expor cada elemento com um **nome** exclusivo na guia **widget** do Gerenciador de **Propriedades**:

    [![](text-fields-and-search-images/search05.png "Layout the design of the Cell Prototype")](text-fields-and-search-images/search05.png#lightbox)
1. Salve as alterações em seu Storyboard.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. No **Gerenciador de Soluções**, clique duas vezes no arquivo `Main.storyboard` para abri-lo para edição.
1. Arraste um novo controlador de exibição de coleção para o storyboard para apresentar os resultados da pesquisa do usuário:

    [![](text-fields-and-search-images/seach02-vs.png "A Collection View Controller")](text-fields-and-search-images/seach02-vs.png#lightbox)
1. Na guia **widget** do Gerenciador de **propriedades**, use `SearchResultsViewController` para a **classe** e `SearchResults` para a **ID do storyboard**:

    [![](text-fields-and-search-images/search03-vs.png "The Widget tab")](text-fields-and-search-images/search03-vs.png#lightbox)
1. Selecione o **protótipo de célula** na superfície de design.
1. Na guia **widget** do Gerenciador de **propriedades**, use `SearchResultCell` para a **classe** e `ImageCell` para o **identificador**:

    [![](text-fields-and-search-images/search04-vs.png "The Widget tab")](text-fields-and-search-images/search04-vs.png#lightbox)
1. Layout o design do **protótipo de célula** e expor cada elemento com um **nome** exclusivo na guia **widget** do Gerenciador de **Propriedades**:

    [![](text-fields-and-search-images/search05-vs.png "Layout the design of the Cell Prototype")](text-fields-and-search-images/search05-vs.png#lightbox)
1. Salve as alterações em seu Storyboard.

-----

<a name="Provide-a-Data-Model" />

### <a name="provide-a-data-model"></a>Fornecer um modelo de dados

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

Em seguida, você precisará fornecer uma classe para atuar como o modelo de dados para os resultados que o usuário será pesquisado. Na **Gerenciador de soluções**, clique com o botão direito do mouse no nome do projeto e selecione **Adicionar** > **novo arquivo...**  > **geral** > **classe vazia** e forneça um **nome**:

[![](text-fields-and-search-images/search06.png "Select Empty Class and provide a Name")](text-fields-and-search-images/search06.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Em seguida, você precisará fornecer uma classe para atuar como o modelo de dados para os resultados que o usuário será pesquisado. Na **Gerenciador de soluções**, clique com o botão direito do mouse no nome do projeto e selecione **Adicionar** > **novo item...**  > **classe** **Apple** > **misc** > e forneça um **nome**:

[![](text-fields-and-search-images/search06-vs.png "Select Class and provide a Name")](text-fields-and-search-images/search06-vs.png#lightbox)

-----

Por exemplo, um aplicativo que permite ao usuário Pesquisar uma coleção de imagens por título e palavra-chave pode ser semelhante ao seguinte:

```csharp
using System;
using Foundation;

namespace tvText
{
    public class PictureInformation : NSObject
    {
        #region Computed Properties
        public string Title { get; set;}
        public string ImageName { get; set;}
        public string Keywords { get; set;}
        #endregion

        #region Constructors
        public PictureInformation (string title, string imageName, string keywords)
        {
            // Initialize
            this.Title = title;
            this.ImageName = imageName;
            this.Keywords = keywords;
        }
        #endregion
    }
}
```

<a name="The-Collection-View-Cell" />

### <a name="the-collection-view-cell"></a>A célula de exibição de coleção

Com o modelo de dados no local, edite a **célula do protótipo** (`SearchResultViewCell.cs`) e faça com que a aparência seja a seguinte:

```csharp
using Foundation;
using System;
using UIKit;

namespace tvText
{
    public partial class SearchResultViewCell : UICollectionViewCell
    {
        #region Private Variables
        private PictureInformation _pictureInfo = null;
        #endregion

        #region Computed Properties
        public PictureInformation PictureInfo {
            get { return _pictureInfo; }
            set {
                _pictureInfo = value;
                UpdateUI ();
            }
        }
        #endregion

        #region Constructors
        public SearchResultViewCell (IntPtr handle) : base (handle)
        {
            // Initialize
            UpdateUI ();
        }
        #endregion

        #region Private Methods
        private void UpdateUI ()
        {
            // Anything to process?
            if (PictureInfo == null) return;

            try {
                Picture.Image = UIImage.FromBundle (PictureInfo.ImageName);
                Picture.AdjustsImageWhenAncestorFocused = true;
                Title.Text = PictureInfo.Title;
                TextColor = UIColor.LightGray;
            } catch {
                // Ignore errors if view isn't fully loaded
            }
        }
        #endregion
    }

}
```

O método `UpdateUI` será usado para exibir campos individuais dos itens **PictureInformation** (a propriedade `PictureInfo`) nos elementos de interface do usuário nomeados toda vez que a propriedade for atualizada. Por exemplo, a imagem e o título associados à imagem.

<a name="The-Collection-View-Controller" />

### <a name="the-collection-view-controller"></a>O controlador de exibição de coleção

Em seguida, edite o controlador de exibição da coleção de resultados da pesquisa (`SearchResultsViewController.cs`) e faça com que ele se pareça com o seguinte:

```csharp
using Foundation;
using System;
using UIKit;
using System.Collections.Generic;

namespace tvText
{
    public partial class SearchResultsViewController : UICollectionViewController , IUISearchResultsUpdating
    {
        #region Constants
        public const string CellID = "ImageCell";
        #endregion

        #region Private Variables
        private string _searchFilter = "";
        #endregion

        #region Computed Properties
        public List<PictureInformation> AllPictures { get; set;}
        public List<PictureInformation> FoundPictures { get; set; }
        public string SearchFilter {
            get { return _searchFilter; }
            set {
                _searchFilter = value.ToLower();
                FindPictures ();
                CollectionView?.ReloadData ();
            }
        }
        #endregion

        #region Constructors
        public SearchResultsViewController (IntPtr handle) : base (handle)
        {
            // Initialize
            this.AllPictures = new List<PictureInformation> ();
            this.FoundPictures = new List<PictureInformation> ();
            PopulatePictures ();
            FindPictures ();

        }
        #endregion

        #region Private Methods
        private void PopulatePictures ()
        {
            // Clear list
            AllPictures.Clear ();

            // Add images
            AllPictures.Add (new PictureInformation ("Antipasta Platter","Antipasta","cheese,grapes,tomato,coffee,meat,plate"));
            AllPictures.Add (new PictureInformation ("Cheese Plate", "CheesePlate", "cheese,plate,bread"));
            AllPictures.Add (new PictureInformation ("Coffee House", "CoffeeHouse", "coffee,people,menu,restaurant,cafe"));
            AllPictures.Add (new PictureInformation ("Computer and Expresso", "ComputerExpresso", "computer,coffee,expresso,phone,notebook"));
            AllPictures.Add (new PictureInformation ("Hamburger", "Hamburger", "meat,bread,cheese,tomato,pickle,lettus"));
            AllPictures.Add (new PictureInformation ("Lasagna Dinner", "Lasagna", "salad,bread,plate,lasagna,pasta"));
            AllPictures.Add (new PictureInformation ("Expresso Meeting", "PeopleExpresso", "people,bag,phone,expresso,coffee,table,tablet,notebook"));
            AllPictures.Add (new PictureInformation ("Soup and Sandwich", "SoupAndSandwich", "soup,sandwich,bread,meat,plate,tomato,lettus,egg"));
            AllPictures.Add (new PictureInformation ("Morning Coffee", "TabletCoffee", "tablet,person,man,coffee,magazine,table"));
            AllPictures.Add (new PictureInformation ("Evening Coffee", "TabletMagCoffee", "tablet,magazine,coffee,table"));
        }

        private void FindPictures ()
        {
            // Clear list
            FoundPictures.Clear ();

            // Scan each picture for a match
            foreach (PictureInformation picture in AllPictures) {
                if (SearchFilter == "") {
                    // If no search term, everything matches
                    FoundPictures.Add (picture);
                } else if (picture.Title.Contains (SearchFilter) || picture.Keywords.Contains (SearchFilter)) {
                    // If the search term is in the title or keywords, we've found a match
                    FoundPictures.Add (picture);
                }
            }
        }
        #endregion

        #region Override Methods
        public override nint NumberOfSections (UICollectionView collectionView)
        {
            // Only one section in this collection
            return 1;
        }

        public override nint GetItemsCount (UICollectionView collectionView, nint section)
        {
            // Return the number of matching pictures
            return FoundPictures.Count;
        }

        public override UICollectionViewCell GetCell (UICollectionView collectionView, NSIndexPath indexPath)
        {
            // Get a new cell and return it
            var cell = collectionView.DequeueReusableCell (CellID, indexPath);
            return (UICollectionViewCell)cell;
        }

        public override void WillDisplayCell (UICollectionView collectionView, UICollectionViewCell cell, NSIndexPath indexPath)
        {
            // Grab the cell
            var currentCell = cell as SearchResultViewCell;
            if (currentCell == null)
                throw new Exception ("Expected to display a `SearchResultViewCell`.");

            // Display the current picture info in the cell
            var item = FoundPictures [indexPath.Row];
            currentCell.PictureInfo = item;
        }

        public override void ItemSelected (UICollectionView collectionView, NSIndexPath indexPath)
        {
            // If this Search Controller was presented as a modal view, close
            // it before continuing
            // DismissViewController (true, null);

            // Grab the picture being selected and report it
            var picture = FoundPictures [indexPath.Row];
            Console.WriteLine ("Selected: {0}", picture.Title);
        }

        public void UpdateSearchResultsForSearchController (UISearchController searchController)
        {
            // Save the search filter and update the Collection View
            SearchFilter = searchController.SearchBar.Text ?? string.Empty;
        }

        public override void DidUpdateFocus (UIFocusUpdateContext context, UIFocusAnimationCoordinator coordinator)
        {
            var previousItem = context.PreviouslyFocusedView as SearchResultViewCell;
            if (previousItem != null) {
                UIView.Animate (0.2, () => {
                    previousItem.TextColor = UIColor.LightGray;
                });
            }

            var nextItem = context.NextFocusedView as SearchResultViewCell;
            if (nextItem != null) {
                UIView.Animate (0.2, () => {
                    nextItem.TextColor = UIColor.Black;
                });
            }
        }
        #endregion
    }
}
```

Primeiro, a interface `IUISearchResultsUpdating` é adicionada à classe para manipular o filtro do controlador de pesquisa que está sendo atualizado pelo usuário:

```csharp
public partial class SearchResultsViewController : UICollectionViewController , IUISearchResultsUpdating
```

Uma constante também é definida para especificar a ID da **célula de protótipo** (que corresponde à ID definida no designer de interface acima) que será usada posteriormente quando o controlador de coleção solicitar uma nova célula:

```csharp
public const string CellID = "ImageCell";
```

O armazenamento é criado para a lista completa de itens que estão sendo pesquisados, o termo de filtro de pesquisa e uma lista de itens que correspondem a esse termo:

```csharp
private string _searchFilter = "";
...

public List<PictureInformation> AllPictures { get; set;}
public List<PictureInformation> FoundPictures { get; set; }
public string SearchFilter {
    get { return _searchFilter; }
    set {
        _searchFilter = value.ToLower();
        FindPictures ();
        CollectionView?.ReloadData ();
    }
}
```

Quando a `SearchFilter` é alterada, a lista de itens correspondentes é atualizada e o conteúdo da exibição de coleção é recarregado. A rotina de `FindPictures` é responsável por localizar itens que correspondam ao novo termo de pesquisa:

```csharp
private void FindPictures ()
{
    // Clear list
    FoundPictures.Clear ();

    // Scan each picture for a match
    foreach (PictureInformation picture in AllPictures) {
        if (SearchFilter == "") {
            // If no search term, everything matches
            FoundPictures.Add (picture);
        } else if (picture.Title.Contains (SearchFilter) || picture.Keywords.Contains (SearchFilter)) {
            // If the search term is in the title or keywords, we've found a match
            FoundPictures.Add (picture);
        }
    }
}
```

O valor da `SearchFilter` será atualizado (o que atualizará o modo de exibição de coleção de resultados) quando o usuário alterar o filtro no controlador de pesquisa:

```csharp
public void UpdateSearchResultsForSearchController (UISearchController searchController)
{
    // Save the search filter and update the Collection View
    SearchFilter = searchController.SearchBar.Text ?? string.Empty;
}
```

Inicialmente, o método `PopulatePictures` preenche a coleção de itens disponíveis:

```csharp
private void PopulatePictures ()
{
    // Clear list
    AllPictures.Clear ();

    // Add images
    AllPictures.Add (new PictureInformation ("Antipasta Platter","Antipasta","cheese,grapes,tomato,coffee,meat,plate"));
    ...
}
```

Para fins deste exemplo, todos os dados de exemplo estão sendo criados na memória quando o controlador de exibição de coleção é carregado. Em um aplicativo real, esses dados provavelmente seriam lidos a partir de um site ou serviço Web, e somente conforme necessário para manter a execução da memória limitada da Apple TV.

Os métodos `NumberOfSections` e `GetItemsCount` fornecem o número de itens correspondentes:

```csharp
public override nint NumberOfSections (UICollectionView collectionView)
{
    // Only one section in this collection
    return 1;
}

public override nint GetItemsCount (UICollectionView collectionView, nint section)
{
    // Return the number of matching pictures
    return FoundPictures.Count;
}
```

O método `GetCell` retorna uma nova **célula de protótipo** (com base na `CellID` definida acima no storyboard) para cada item na exibição de coleção:

```csharp
public override UICollectionViewCell GetCell (UICollectionView collectionView, NSIndexPath indexPath)
{
    // Get a new cell and return it
    var cell = collectionView.DequeueReusableCell (CellID, indexPath);
    return (UICollectionViewCell)cell;
}
```

O método `WillDisplayCell` é chamado antes de a célula ser exibida para que possa ser configurada:

```csharp
public override void WillDisplayCell (UICollectionView collectionView, UICollectionViewCell cell, NSIndexPath indexPath)
{
    // Grab the cell
    var currentCell = cell as SearchResultViewCell;
    if (currentCell == null)
        throw new Exception ("Expected to display a `SearchResultViewCell`.");

    // Display the current picture info in the cell
    var item = FoundPictures [indexPath.Row];
    currentCell.PictureInfo = item;
}
```

O método `DidUpdateFocus` fornece comentários visuais ao usuário quando eles destacam itens na exibição de coleção de resultados:

```csharp
public override void DidUpdateFocus (UIFocusUpdateContext context, UIFocusAnimationCoordinator coordinator)
{
    var previousItem = context.PreviouslyFocusedView as SearchResultViewCell;
    if (previousItem != null) {
        UIView.Animate (0.2, () => {
            previousItem.TextColor = UIColor.LightGray;
        });
    }

    var nextItem = context.NextFocusedView as SearchResultViewCell;
    if (nextItem != null) {
        UIView.Animate (0.2, () => {
            nextItem.TextColor = UIColor.Black;
        });
    }
}
```

Por fim, o método `ItemSelected` manipula o usuário selecionando um item (clicando na superfície de toque com o Siri remoto) na exibição de coleção de resultados:

```csharp
public override void ItemSelected (UICollectionView collectionView, NSIndexPath indexPath)
{
    // If this Search Controller was presented as a modal view, close
    // it before continuing
    // DismissViewController (true, null);

    // Grab the picture being selected and report it
    var picture = FoundPictures [indexPath.Row];
    Console.WriteLine ("Selected: {0}", picture.Title);
}
```

Se o campo de pesquisa foi apresentado como uma exibição de caixa de diálogo modal (na parte superior da exibição que o chama), use o método `DismissViewController` para ignorar a exibição de pesquisa quando o usuário selecionar um item. Neste exemplo, o campo de pesquisa é apresentado como o conteúdo de uma guia de exibição de guia, portanto, não está sendo descartado aqui.

Para obter mais informações sobre exibições de coleção, consulte [a documentação trabalhando com exibições de coleção](~/ios/tvos/user-interface/collection-views.md) .

<a name="Presenting the Search Field" />

### <a name="presenting-the-search-field"></a>Apresentando o campo de pesquisa

Há duas maneiras principais de um campo de pesquisa (e seus resultados de pesquisa e teclado na tela associados) que podem ser apresentados ao usuário no tvOS:

- **Exibição de caixa de diálogo modal** -o campo de pesquisa pode ser apresentado sobre a exibição atual e o controlador de exibição como uma exibição de caixa de diálogo modal de tela inteira. Isso geralmente é feito em resposta ao usuário clicando em um botão ou outro elemento de interface do usuário. A caixa de diálogo é ignorada quando o usuário seleciona um item dos resultados da pesquisa.
- **Exibir conteúdo** – o campo de pesquisa é uma parte direta de uma determinada exibição. Por exemplo, como o conteúdo de uma guia de pesquisa em um controlador de exibição de guia.

Para obter o exemplo de uma lista pesquisável de imagens fornecidas acima, o campo de pesquisa é apresentado como exibir conteúdo na guia Pesquisar e o controlador de exibição da guia Pesquisar é semelhante ao seguinte:

```csharp
using System;
using UIKit;

namespace tvText
{
    public partial class SecondViewController : UIViewController
    {
        #region Constants
        public const string SearchResultsID = "SearchResults";
        #endregion

        #region Computed Properties
        public SearchResultsViewController ResultsController { get; set;}
        #endregion

        #region Constructors
        public SecondViewController (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Private Methods
        public void ShowSearchController ()
        {
            // Build an instance of the Search Results View Controller from the Storyboard
            ResultsController = Storyboard.InstantiateViewController (SearchResultsID) as SearchResultsViewController;
            if (ResultsController == null)
                throw new Exception ("Unable to instantiate a SearchResultsViewController.");

            // Create an initialize a new search controller
            var searchController = new UISearchController (ResultsController) {
                SearchResultsUpdater = ResultsController,
                HidesNavigationBarDuringPresentation = false
            };

            // Set any required search parameters
            searchController.SearchBar.Placeholder = "Enter keyword (e.g. coffee)";

            // The Search Results View Controller can be presented as a modal view
            // PresentViewController (searchController, true, null);

            // Or in the case of this sample, the Search View Controller is being
            // presented as the contents of the Search Tab directly. Use either one
            // or the other method to display the Search Controller (not both).
            var container = new UISearchContainerViewController (searchController);
            var navController = new UINavigationController (container);
            AddChildViewController (navController);
            View.Add (navController.View);
        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // If the Search Controller is being displayed as the content
            // of the search tab, include it here.
            ShowSearchController ();
        }

        public override void ViewDidAppear (bool animated)
        {
            base.ViewDidAppear (animated);

            // If the Search Controller is being presented as a modal view,
            // call it here to display it over the contents of the Search
            // tab.
            // ShowSearchController ();
        }
        #endregion
    }
}
```

Primeiro, uma constante é definida que corresponde ao **identificador de storyboard** que foi atribuído ao controlador de exibição de coleção de resultados de pesquisa no designer de interface:

```csharp
public const string SearchResultsID = "SearchResults";
```

Em seguida, o método `ShowSearchController` cria um novo controlador de coleção de exibição de pesquisa e exibe que ele era necessário:

```csharp
public void ShowSearchController ()
{
    // Build an instance of the Search Results View Controller from the Storyboard
    ResultsController = Storyboard.InstantiateViewController (SearchResultsID) as SearchResultsViewController;
    if (ResultsController == null)
        throw new Exception ("Unable to instantiate a SearchResultsViewController.");

    // Create an initialize a new search controller
    var searchController = new UISearchController (ResultsController) {
        SearchResultsUpdater = ResultsController,
        HidesNavigationBarDuringPresentation = false
    };

    // Set any required search parameters
    searchController.SearchBar.Placeholder = "Enter keyword (e.g. coffee)";

    // The Search Results View Controller can be presented as a modal view
    // PresentViewController (searchController, true, null);

    // Or in the case of this sample, the Search View Controller is being
    // presented as the contents of the Search Tab directly. Use either one
    // or the other method to display the Search Controller (not both).
    var container = new UISearchContainerViewController (searchController);
    var navController = new UINavigationController (container);
    AddChildViewController (navController);
    View.Add (navController.View);
}
```

No método acima, uma vez que um `SearchResultsViewController` foi instanciado do storyboard, um novo `UISearchController` é criado para apresentar o campo de pesquisa e o teclado virtual para o usuário. A coleção de resultados da pesquisa (conforme definido pelo `SearchResultsViewController`) será exibida sob esse teclado.

Em seguida, a `SearchBar` é configurada com informações como a dica de **espaço reservado** . Isso fornece informações para o usuário sobre o tipo de pesquisa que está sendo preformado.

Em seguida, o campo de pesquisa é apresentado ao usuário de uma das duas maneiras:

- **Exibição de caixa de diálogo modal** -o método `PresentViewController` é chamado para apresentar a pesquisa sobre a exibição existente, tela inteira.
- **Exibir conteúdo** -um `UISearchContainerViewController` é criado para conter o controlador de pesquisa. Um `UINavigationController` é criado para conter o contêiner de pesquisa, o controlador de navegação é adicionado ao controlador de exibição `AddChildViewController (navController)`e a exibição apresentada `View.Add (navController.View)`.

Por fim, e novamente com base no tipo de apresentação, o método `ViewDidLoad` ou `ViewDidAppear` chamará o método `ShowSearchController` para apresentar a pesquisa ao usuário:

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();

    // If the Search Controller is being displayed as the content
    // of the search tab, include it here.
    ShowSearchController ();
}

public override void ViewDidAppear (bool animated)
{
    base.ViewDidAppear (animated);

    // If the Search Controller is being presented as a modal view,
    // call it here to display it over the contents of the Search
    // tab.
    // ShowSearchController ();
}
```

Quando o aplicativo é executado e a guia Pesquisar selecionada pelo usuário, a lista completa de itens não filtrados será apresentada ao usuário:

[![](text-fields-and-search-images/intro02.png "Default search results")](text-fields-and-search-images/intro02.png#lightbox)

À medida que o usuário começa a inserir um termo de pesquisa, a lista de resultados será filtrada por esse termo e atualizada automaticamente:

[![](text-fields-and-search-images/intro03.png "Filtered search results")](text-fields-and-search-images/intro03.png#lightbox)

A qualquer momento, o usuário pode alternar o foco para um item nos resultados da pesquisa e clicar na superfície de toque do Siri remoto para selecioná-lo.

<a name="Summary" />

## <a name="summary"></a>Resumo

Este artigo abordou a criação e o trabalho com campos de texto e de pesquisa dentro de um aplicativo Xamarin. tvOS. Ele mostrou como criar texto e pesquisar o conteúdo da coleção no designer de interface e ele mostrou duas maneiras diferentes de um campo de pesquisa ser apresentado ao usuário no tvOS.

## <a name="related-links"></a>Links relacionados

- [Amostras do tvOS](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+tvOS)
- [tvOS](https://developer.apple.com/tvos/)
- [Guias de interface humana do tvOS](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guia de programação de aplicativo para tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
