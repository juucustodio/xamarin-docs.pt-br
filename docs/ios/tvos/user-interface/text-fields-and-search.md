---
title: Trabalhando com textos e campos de pesquisa
description: Este artigo aborda criando e trabalhando com textos e campos de pesquisa dentro de um aplicativo Xamarin.tvOS.
ms.topic: article
ms.prod: xamarin
ms.assetid: 9EE63CA6-2F31-4EE0-AAE5-82E18CFAC06C
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/16/2017
ms.openlocfilehash: cb6917f9cd0dc22cc32a2d32c203328f1d6d963b
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="working-with-text-and-search-fields"></a>Trabalhando com textos e campos de pesquisa

_Este artigo aborda criando e trabalhando com textos e campos de pesquisa dentro de um aplicativo Xamarin.tvOS._



Quando necessário, seu aplicativo Xamarin.tvOS pode solicitar pequenas partes de texto do usuário (como as IDs de usuário e senhas) usando um campo de texto e o teclado virtual:

[ ![](text-fields-and-search-images/intro01.png "Campo de pesquisa de exemplo")](text-fields-and-search-images/intro01.png)

Opcionalmente, você pode fornecer capacidade de pesquisa de palavra-chave de conteúdo do aplicativo usando um campo de pesquisa:

[ ![](text-fields-and-search-images/intro02.png "Resultados da pesquisa de exemplo")](text-fields-and-search-images/intro02.png)

Este documento aborda os detalhes de como trabalhar com texto e campos de pesquisa em um aplicativo Xamarin.tvOS.

<a name="About-Text-and-Search-Fields" />

## <a name="about-text-and-search-fields"></a>Sobre o texto e os campos de pesquisa

Como mencionado acima, se necessário, o Xamarin.tvOS pode apresentar um ou mais campos de texto para coletar pequenas quantidades de texto do usuário usando na tela (ou teclado bluetooth opcional dependendo da versão do tvOS o usuário instalou). 

Além disso, se seu aplicativo apresentar grandes volumes de conteúdo para o usuário (como uma música, filmes ou uma coleção de imagem), você talvez queira incluir um campo de pesquisa que permite que o usuário insira uma pequena quantidade de texto para filtrar a lista de itens disponíveis.

<a name="Text-Fields" />

## <a name="text-fields"></a>Campos de texto

TvOS, um campo de texto é apresentado como uma caixa de entrada de altura fixa, canto arredondado abrirá um teclado virtual quando o usuário clica nela:

[ ![](text-fields-and-search-images/text01.png "Texto campos em tvOS")](text-fields-and-search-images/text01.png)

Quando o usuário move [foco](~/ios/tvos/app-fundamentals/navigation-focus.md) para um determinado campo de texto, ele será crescem e exibirá uma sombra profunda. Você precisará ter isso em mente ao projetar sua Interface do usuário, como campos de texto podem se sobrepor a outros elementos de interface do usuário quando em foco.

Apple tem as seguintes sugestões para trabalhar com campos de texto:

- **Usar texto entrada moderadamente** - devido à natureza do teclado na tela, inserindo longo seções de texto ou preenchendo a vários campos de texto é entediante ao usuário. Uma solução melhor é limitar a quantidade de entrada de texto usando listas de seleção ou [botões](~/ios/tvos/user-interface/buttons.md).
- **Use dicas para finalidade de comunicação** -campo de texto pode exibir dicas"espaço reservado" quando estiver vazio. Onde aplicável, use dicas para descrever a finalidade do seu campo de texto em vez de um rótulo separado.
- **Selecione o tipo apropriado de teclado padrão** -tvOS fornece vários tipos teclado criado especificamente diferentes, que você pode especificar para o campo de texto. Por exemplo, o teclado de endereço de Email pode facilitar a entrada, permitindo que o usuário selecione de uma lista de endereços recentemente inseridos.
- **Quando apropriado, use campos de texto Secure** -campo de texto de proteger um apresenta os caracteres inseridos como pontos (em vez de letras reais). Sempre use um campo de texto segura ao coletar informações confidenciais, como senhas.

<a name="Keyboards" />

## <a name="keyboards"></a>Teclados

Sempre que o usuário clica em um campo de texto na Interface do usuário, um linear na tela teclado é exibido. O usuário usa a superfície de toque de [Siri remoto](~/ios/tvos/platform/remote-bluetooth.md#The-Siri-Remote) para selecionar as letras do teclado e insira as informações solicitadas:

[ ![](text-fields-and-search-images/keyboard01.png "O teclado remoto Siri")](text-fields-and-search-images/keyboard01.png)

Se houver mais de um campo de texto no modo de exibição atual, um **próximo** botão será automaticamente exibido para levar o usuário para o próximo campo de texto. Um **feito** botão será exibido para o último campo de texto que terminar a entrada de texto e retornar o usuário para a tela anterior. 

A qualquer momento, o usuário também pode pressionar a **Menu** botão no controle remoto Siri para terminar a entrada de texto e retornar novamente para a tela anterior.

Apple tem as seguintes sugestões para trabalhar com na tela teclados:

- **Selecione o tipo apropriado de teclado padrão** -tvOS fornece vários tipos teclado criado especificamente diferentes, que você pode especificar para o campo de texto. Por exemplo, o teclado de endereço de Email pode facilitar a entrada, permitindo que o usuário selecione de uma lista de endereços recentemente inseridos.
- **Quando apropriado, use o teclado acessório exibições** - além do padrão informações é sempre exibido, opcional modos de exibição de acessório (como imagens ou rótulos) podem ser adicionadas para o teclado na tela para esclarecer a finalidade de entrada de texto ou para Ajude o usuário a inserir as informações necessárias.

Para obter mais informações sobre como trabalhar com o teclado virtual, consulte da Apple [UIKeyboardType](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UITextInputTraits_Protocol/index.html#//apple_ref/c/tdef/UIKeyboardType), [Gerenciando o teclado](https://developer.apple.com/library/tvos/documentation/StringsTextFonts/Conceptual/TextAndWebiPhoneOS/KeyboardManagement/KeyboardManagement.html#//apple_ref/doc/uid/TP40009542-CH5-SW1), [modos de exibição personalizados para entrada de dados](https://developer.apple.com/library/tvos/documentation/StringsTextFonts/Conceptual/TextAndWebiPhoneOS/InputViews/InputViews.html#//apple_ref/doc/uid/TP40009542-CH12-SW1) e [ Guia de programação em texto para iOS](https://developer.apple.com/library/tvos/documentation/StringsTextFonts/Conceptual/TextAndWebiPhoneOS/Introduction/Introduction.html) documentação.

<a name="Search" />

## <a name="search"></a>Pesquisar

Um campo de pesquisa apresentar uma tela especializada fornecendo um campo de texto e o teclado virtual que permite ao usuário filtrar uma coleção de itens que são exibidos abaixo do teclado:

[ ![](text-fields-and-search-images/search01.png "Resultados da pesquisa de exemplo")](text-fields-and-search-images/search01.png)

Como o usuário insere letras no campo de pesquisa, os resultados abaixo refletem automaticamente os resultados da pesquisa. A qualquer momento, o usuário pode alternar o foco para os resultados e selecione um dos itens apresentados.

Apple tem as seguintes sugestões para trabalhar com campos de pesquisa:

- **Fornecer pesquisas recentes** - pois inserindo texto com Siri remoto pode ser entediante e os usuários tendem a repetir solicitações de pesquisa, considere a adição de uma seção de resultados de pesquisa recentes antes dos resultados atuais da área de teclado.
- **Quando possível, limitar o número de resultados** - porque uma grande lista de itens pode ser difícil para o usuário analisar e navegar, considere limitar o número de resultados retornados.
- **Se apropriado, filtros de resultado de pesquisa forneça** - se o conteúdo fornecido pelo seu aplicativo se presta, considere adicionar barras de escopo para permitir que o usuário filtrar ainda mais os resultados da pesquisa retornados.

Para obter mais informações, consulte da Apple [referência de classe UISearchController](https://developer.apple.com/library/tvos/documentation/UIKit/Reference/UISearchController/index.html).

<a name="Working-with-Text-Fields" />

## <a name="working-with-text-fields"></a>Trabalhando com campos de texto

É a maneira mais fácil para trabalhar com campos de texto em um aplicativo de Xamarin.tvOS para adicioná-los para o design de Interface do usuário usando o Designer do iOS.

Faça o seguinte:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

1. No **solução preenchimento**, clique duas vezes o `Main.storyboard` arquivo para abri-lo para edição.
1. Arraste um ou mais **campos de texto** int na superfície de design em um modo de exibição: 

    [ ![](text-fields-and-search-images/text02.png "Um campo de texto")](text-fields-and-search-images/text02.png)
1. Selecione o **campos de texto** e dê a cada um exclusivo **nome** no **Widget** guia do **propriedades de preenchimento**: 

    [ ![](text-fields-and-search-images/text03.png "A guia de Widget de painel de propriedades")](text-fields-and-search-images/text03.png)
1. No **campo de texto** seção, você pode definir elementos, como o **espaço reservado** dica e padrão **valor**: 

    [ ![](text-fields-and-search-images/text04.png "A seção de campo de texto")](text-fields-and-search-images/text04.png)
1. Role para baixo para definir propriedades, como **ortográfica**, **capitalização** e o padrão **teclado tipo**: 

    [ ![](text-fields-and-search-images/text05.png "Verificação de ortografia, maiusculas e minúsculas e o tipo de teclado padrão")](text-fields-and-search-images/text05.png) 
1. Salve as alterações para o Storyboard.
    
# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)
    
1. No **Gerenciador de Soluções**, clique duas vezes no arquivo `Main.storyboard` para abri-lo para edição.
1. Arraste um ou mais **campos de texto** int na superfície de design em um modo de exibição: 

    [ ![](text-fields-and-search-images/text02-vs.png "Um campo de texto")](text-fields-and-search-images/text02-vs.png)
1. Selecione o **campos de texto** e dê a cada um exclusivo **nome** no **Widget** guia do **propriedades Explorer**: 

    [ ![](text-fields-and-search-images/text03-vs.png "A guia de Widget")](text-fields-and-search-images/text03-vs.png)
1. No **campo de texto** seção, você pode definir elementos, como o **espaço reservado** dica e padrão **valor**: 

    [ ![](text-fields-and-search-images/text04-vs.png "A seção de campo de texto")](text-fields-and-search-images/text04-vs.png)
1. Role para baixo para definir propriedades, como **ortográfica**, **capitalização** e o padrão **teclado tipo**: 

    [ ![](text-fields-and-search-images/text05-vs.png "Verificação de ortografia, maiusculas e minúsculas e o tipo de teclado padrão")](text-fields-and-search-images/text05-vs.png) 
1. Salve as alterações para o Storyboard.
    
-----

No código, você pode obter ou definir o valor de um campo de texto usando seu `Text` propriedade:

```csharp
Console.WriteLine ("User ID {0} and Password {1}", UserId.Text, Password.Text);
```

Opcionalmente, você pode usar o `Started` e `Ended` eventos de campo de texto para responder a entrada de texto inicial e final.

<a name="Working-with-Search-Fields" />

## <a name="working-with-search-fields"></a>Trabalhando com campos de pesquisa

É a maneira mais fácil para trabalhar com campos de pesquisa em um aplicativo de Xamarin.tvOS para adicioná-los para o design de Interface do usuário usando o Designer de Interface.

Faça o seguinte:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)
    
1. No **solução preenchimento**, clique duas vezes o `Main.storyboard` arquivo para abri-lo para edição.
1. Arraste um novo controlador de exibição de coleção para o Storyboard para apresentar os resultados da pesquisa do usuário: 

    [ ![](text-fields-and-search-images/search02.png "Um controlador de exibição de coleção")](text-fields-and-search-images/search02.png)
1. No **Widget** guia do **propriedades de preenchimento**, use `SearchResultsViewController` para o **classe** e `SearchResults` para o **Storyboard ID**: 

    [ ![](text-fields-and-search-images/search03.png "A guia de Widget")](text-fields-and-search-images/search03.png)
1. Selecione o **protótipo de célula** na superfície de design.
1. No **Widget** guia do **propriedades Explorer**, use `SearchResultCell` para o **classe** e `ImageCell` para o **identificador**: 

    [ ![](text-fields-and-search-images/search04.png "A guia de Widget")](text-fields-and-search-images/search04.png)
1. O design de layout do **protótipo de célula** e expor cada elemento com um único **nome** no **Widget** guia do **Explorer propriedades**: 

    [ ![](text-fields-and-search-images/search05.png "O design do protótipo célula de layout")](text-fields-and-search-images/search05.png)
1. Salve as alterações para o Storyboard.
    
# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)
    
1. No **Gerenciador de Soluções**, clique duas vezes no arquivo `Main.storyboard` para abri-lo para edição.
1. Arraste um novo controlador de exibição de coleção para o Storyboard para apresentar os resultados da pesquisa do usuário: 

    [ ![](text-fields-and-search-images/seach02-vs.png "Um controlador de exibição de coleção")](text-fields-and-search-images/seach02-vs.png)
1. No **Widget** guia do **propriedades Explorer**, use `SearchResultsViewController` para o **classe** e `SearchResults` para o **Storyboard ID**: 

    [ ![](text-fields-and-search-images/search03-vs.png "A guia de Widget")](text-fields-and-search-images/search03-vs.png)
1. Selecione o **protótipo de célula** na superfície de design.
1. No **Widget** guia do **propriedades Explorer**, use `SearchResultCell` para o **classe** e `ImageCell` para o **identificador**: 

    [ ![](text-fields-and-search-images/search04-vs.png "A guia de Widget")](text-fields-and-search-images/search04-vs.png)
1. O design de layout do **protótipo de célula** e expor cada elemento com um único **nome** no **Widget** guia do **Explorer propriedades**: 

    [ ![](text-fields-and-search-images/search05-vs.png "O design do protótipo célula de layout")](text-fields-and-search-images/search05-vs.png)
1. Salve as alterações para o Storyboard.
    
-----

<a name="Provide-a-Data-Model" />

### <a name="provide-a-data-model"></a>Fornecer um modelo de dados

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

Em seguida, você precisará fornecer uma classe para atuar como o modelo de dados para os resultados que o usuário procurar. No **Solution Explorer**, clique no nome do projeto e selecione **adicionar** > **novo arquivo...**   >  **Geral** > **classe vazia** e fornecer um **nome**: 

[ ![](text-fields-and-search-images/search06.png "Selecione a classe vazia e forneça um nome")](text-fields-and-search-images/search06.png)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Em seguida, você precisará fornecer uma classe para atuar como o modelo de dados para os resultados que o usuário procurar. No **Solution Explorer**, clique no nome do projeto e selecione **adicionar** > **Novo Item...**   >  **Apple** > **Misc** > **classe** e fornecer um **nome**: 

[ ![](text-fields-and-search-images/search06-vs.png "Selecione a classe e forneça um nome")](text-fields-and-search-images/search06-vs.png)

-----

Por exemplo, um aplicativo que permite ao usuário pesquisar uma coleção de imagens, título e a palavra-chave pode parecer com o seguinte:

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

Com o modelo de dados no local, edite o **protótipo célula** (`SearchResultViewCell.cs`) e torná-lo aparência ficam o seguinte:

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

O `UpdateUI` método será usado para exibir campos individuais do **PictureInformation** itens (o `PictureInfo` propriedade) dos elementos de interface do usuário nomeado cada vez que a propriedade é atualizada. Por exemplo, a imagem e título associadas à imagem.

<a name="The-Collection-View-Controller" />

### <a name="the-collection-view-controller"></a>O controlador de exibição de coleção

Em seguida, edite o controlador de exibição de coleção de resultados pesquisa (`SearchResultsViewController.cs`) e torná-lo a aparência a seguir:

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

Primeiro, o `IUISearchResultsUpdating` Interface é adicionada à classe para lidar com o filtro de pesquisa controlador que está sendo atualizado pelo usuário:

```csharp
public partial class SearchResultsViewController : UICollectionViewController , IUISearchResultsUpdating
```

Uma constante também é definida para especificar a ID do **protótipo célula** (que corresponde à ID definida no Designer de Interface acima) que será usado mais tarde quando o controlador de coleção solicita uma nova célula:

```csharp
public const string CellID = "ImageCell";
```

Armazenamento é criado para a lista completa de itens que estão sendo pesquisados, o termo de filtro de pesquisa e uma lista de itens que correspondem a esse termo:

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

Quando o `SearchFilter` é alterada, a lista de itens correspondentes é atualizada e conteúdo do modo de exibição de coleção é recarregado. O `FindPictures` rotina é responsável por encontrar itens que correspondam ao novo termo de pesquisa:

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

O valor de `SearchFilter` será atualizado (que atualizará o modo de exibição de conjunto de resultados) quando o usuário altera o filtro no controlador de pesquisa:

```csharp
public void UpdateSearchResultsForSearchController (UISearchController searchController)
{
    // Save the search filter and update the Collection View
    SearchFilter = searchController.SearchBar.Text ?? string.Empty;
}
```

O `PopulatePictures` método inicialmente preenche a coleção de itens disponíveis:

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

Para este exemplo, todos os dados de exemplo está sendo criado na memória quando o controlador de exibição de coleção é carregado. Em um aplicativo real, provavelmente seriam lido esses dados de um serviço web ou de banco de dados e conforme necessário para impedir saturar o Apple TV memória limitada.

O `NumberOfSections` e `GetItemsCount` métodos fornecem o número de itens correspondentes:

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

O `GetCell` método retorna um novo **protótipo célula** (com base no `CellID` definida acima no Storyboard) para cada item na exibição de coleção:

```csharp
public override UICollectionViewCell GetCell (UICollectionView collectionView, NSIndexPath indexPath)
{
    // Get a new cell and return it
    var cell = collectionView.DequeueReusableCell (CellID, indexPath);
    return (UICollectionViewCell)cell;
}
```

O `WillDisplayCell` o método é chamado antes da célula que está sendo exibida para que ele pode ser configurado:

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

O `DidUpdateFocus` método fornece comentários visuais ao usuário conforme eles realçar itens na exibição de conjunto de resultados:

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

Por fim, o `ItemSelected` método trata o usuário selecionar um item (clicando na superfície de toque com Siri remoto) no modo de exibição de conjunto de resultados:

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

Se o campo de pesquisa foi apresentado como uma exibição da caixa de diálogo modal (na parte superior do modo de exibição chamando-o), use o `DismissViewController` método para ignorar o modo de exibição de pesquisa quando o usuário seleciona um item. Neste exemplo, o campo de pesquisa é apresentado como o conteúdo de uma guia de visualização da guia, para que ele não está sendo ignorado aqui.

Para obter mais informações sobre exibições de coleção, consulte nosso [trabalhando com exibições de coleção](~/ios/tvos/user-interface/collection-views.md) documentação.

<a name="Presenting the Search Field" />

### <a name="presenting-the-search-field"></a>Apresentando o campo de pesquisa

Há duas maneiras principais que um campo de pesquisa (e seus associados o teclado virtual e os resultados da pesquisa) pode ser apresentada ao usuário no tvOS: 

- **Exibição de diálogo modal** -o campo de pesquisa podem ser apresentado em atual modo de exibição e o controlador de exibição como uma exibição da caixa de diálogo modal de tela inteira. Geralmente, isso é feito em resposta, o usuário clica em um botão ou outro elemento de interface do usuário. A caixa de diálogo é fechada quando o usuário seleciona um item nos resultados da pesquisa.
- **Exibir conteúdo** -o campo de pesquisa é uma parte direta de um determinado modo de exibição. Por exemplo, como o conteúdo de uma guia de pesquisa em um controlador de exibição da guia.

O exemplo de uma lista de pesquisa de imagens acima, o campo de pesquisa é apresentado como exibir o conteúdo no guia de pesquisa e o controlador de exibição de guia de pesquisa é semelhante ao seguinte:

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

Primeiro, uma constante é definida que corresponde a **identificador de Storyboard** que foi atribuído ao controlador de exibição de coleção de resultados de pesquisa na Interface de Designer:

```csharp
public const string SearchResultsID = "SearchResults";
```

Em seguida, o `ShowSearchController` método cria um novo controlador de coleção de exibição de pesquisa e exibe era necessária:

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

O método acima, uma vez um `SearchResultsViewController` foi criada a partir do Storyboard, um novo `UISearchController` é criada para apresentar o campo de pesquisa e o teclado virtual para o usuário. O conjunto de resultados de pesquisa (conforme definido pelo `SearchResultsViewController`) será exibida sob esse teclado.

Em seguida, o `SearchBar` é configurado com informações como o **espaço reservado** dica. Isso fornece informações ao usuário sobre o tipo de pesquisa que está sendo executada.

Em seguida, o campo de pesquisa é apresentado ao usuário em uma das duas maneiras:

- **Exibição de diálogo modal** - o `PresentViewController` método é chamado para apresentar a pesquisa sobre o modo de exibição existente, tela inteira.
- **Exibir conteúdo** - um `UISearchContainerViewController` é criado para conter o controlador de pesquisa. Um `UINavigationController` é criado para conter o contêiner de pesquisa, em seguida, o controlador de navegação é adicionado ao controlador de exibição `AddChildViewController (navController)`e o modo de exibição apresentado `View.Add (navController.View)`.

Por fim e novamente com base no tipo de apresentação, ou o `ViewDidLoad` ou `ViewDidAppear` chamará o método de `ShowSearchController` método para apresentar a pesquisa para o usuário:

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

Quando o aplicativo é executado e o guia de pesquisa selecionado pelo usuário, a lista não filtrada completa de itens será apresentada ao usuário:

[ ![](text-fields-and-search-images/intro02.png "Resultados de pesquisa padrão")](text-fields-and-search-images/intro02.png)

Como o usuário começa a digitar um termo de pesquisa, a lista de resultados será filtrada pelo termo e atualizada automaticamente:

[ ![](text-fields-and-search-images/intro03.png "Resultados de pesquisa filtrados")](text-fields-and-search-images/intro03.png)

A qualquer momento, o usuário pode alternar o foco para um item nos resultados da pesquisa e clique na superfície de toque de remoto Siri para selecioná-la.

<a name="Summary" />

## <a name="summary"></a>Resumo

Este artigo abordou criando e trabalhando com textos e campos de pesquisa dentro de um aplicativo Xamarin.tvOS. Ele mostrou como criar conteúdo de texto e a coleção de pesquisa no Designer de Interface e ele mostrava de duas maneiras diferentes de um campo de pesquisa podem ser apresentado ao usuário no tvOS.



## <a name="related-links"></a>Links relacionados

- [Exemplos de tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS guias de Interface Humana](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guia de programação de aplicativo para tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
