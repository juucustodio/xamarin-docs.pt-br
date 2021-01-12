---
title: Resumo do capítulo 25. Variedades de página
description: 'Criando aplicativos móveis com Xamarin.Forms : Resumo do capítulo 25. Variedades de página'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: D1D348F2-6A44-4781-ADCE-A0B7BB9AEF89
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: d9d0d75a393cc30ffaf698625cd49cf0ce5b5741
ms.sourcegitcommit: 1decf2c65dc4c36513f7dd459a5df01e170a036f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/12/2021
ms.locfileid: "98115256"
---
# <a name="summary-of-chapter-25-page-varieties"></a>Resumo do capítulo 25. Variedades de página

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25)

> [!NOTE]
> Este livro foi publicado na Primavera de 2016 e não foi atualizado desde então. Há muito no livro que permanece valioso, mas alguns materiais estão desatualizados e alguns tópicos não estão mais totalmente corretos ou completos.

Até agora, você viu duas classes que derivam de `Page` : `ContentPage` e `NavigationPage` . Este capítulo apresenta dois outros:

- [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) gerencia duas páginas, um mestre e um detalhe
- [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) Gerencia várias páginas filho acessadas por meio de guias

Esses tipos de página fornecem opções de navegação mais sofisticadas do que as `NavagationPage` abordadas no [capítulo 24. Navegação de página](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter24.md).

## <a name="master-and-detail"></a>Mestre e detalhes

O [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) define duas propriedades do tipo `Page` : [`Master`](xref:Xamarin.Forms.MasterDetailPage.Master) e `Detail` . Em geral, você define cada uma dessas propriedades como um `ContentPage` . O `MasterDetailPage` exibe e alterna entre essas duas páginas.

Há duas maneiras fundamentais de alternar entre essas duas páginas:

- *dividir* onde o mestre e os detalhes estão lado a lado
- *popover* onde a página de detalhes cobre ou aborda parcialmente a página mestra

Há várias variações da abordagem *popover* (*Slide*, *sobreposição* e *troca*), mas elas geralmente são dependentes da plataforma. Você pode definir a [`MasterDetailBehavior`](xref:Xamarin.Forms.MasterDetailPage.MasterBehavior) propriedade de `MasterDetailPage` como um membro da [`MasterBehavior`](xref:Xamarin.Forms.MasterBehavior) enumeração:

- [`Default`](xref:Xamarin.Forms.MasterBehavior.Default)
- [`Split`](xref:Xamarin.Forms.MasterBehavior.Split)
- [`SplitOnLandscape`](xref:Xamarin.Forms.MasterBehavior.SplitOnLandscape)
- [`SplitOnPortrait`](xref:Xamarin.Forms.MasterBehavior.SplitOnPortrait)
- [`Popover`](xref:Xamarin.Forms.MasterBehavior.Popover)

No entanto, essa propriedade não tem nenhum efeito em telefones. Os telefones sempre têm um comportamento popover. Somente tablets e janelas de área de trabalho podem ter um comportamento dividido.

### <a name="exploring-the-behaviors"></a>Explorando os comportamentos

O exemplo [**MasterDetailBehaviors**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25/MasterDetailBehaviors) permite que você experimente o comportamento padrão em diferentes dispositivos. O programa contém dois `ContentPage` derivativos separados para o mestre e os detalhes (com uma `Title` propriedade definida em ambos) e outra classe derivada de `MasterDetailPage` que os combina. A página de detalhes é colocada em um `NavigationPage` porque o programa UWP não funcionaria sem ele.

As plataformas Windows 8.1 e Windows Phone 8,1 exigem que um bitmap seja definido como a `Icon` propriedade da página mestra.

### <a name="back-to-school"></a>De volta à escola

O exemplo de [**SchoolAndDetail**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25/SchoolAndDetail) usa uma abordagem um pouco diferente para construir o programa para exibir alunos da biblioteca [**SchoolOfFineArt**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/SchoolOfFineArt) .

As `Master` `Detail` Propriedades e são definidas com árvores visuais no arquivo [SchoolAndDetailPage. XAML](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter25/SchoolAndDetail/SchoolAndDetail/SchoolAndDetail/SchoolAndDetailPage.xaml) , que deriva de `MasterDetailPage` . Essa disposição permite que as ligações de dados sejam definidas entre as páginas mestras e de detalhes.

Esse arquivo XAML também define a `IsPresented` propriedade de `MasterDetailPage` como `True` . Isso faz com que a página mestra seja exibida na inicialização; Por padrão, a página de detalhes é exibida. O arquivo [SchoolAndDetailPage.XAML.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter25/SchoolAndDetail/SchoolAndDetail/SchoolAndDetail/SchoolAndDetailPage.xaml.cs) define `IsPresented` como `false` quando um item é selecionado no `ListView` na página mestra. A página de detalhes é exibida:

[![Captura de tela tripla de escola e detalhes](images/ch25fg09-small.png "Página de detalhes de um MasterDetailPage")](images/ch25fg09-large.png#lightbox "Página de detalhes de um MasterDetailPage")

### <a name="your-own-user-interface"></a>Sua própria interface do usuário

Embora Xamarin.Forms o forneça uma interface do usuário para alternar entre as exibições mestre e de detalhes, você pode fornecer a sua própria. Para fazer isso:

- Defina a `IsGestureEnabled` propriedade como `false` para desabilitar o passar o dedo
- Substitua o `ShouldShowToolbarButton` método e retorne `false` para ocultar os botões da barra de ferramentas em Windows 8.1 e Windows Phone 8,1.

Em seguida, você deve fornecer um meio para alternar entre as páginas mestras e detalhadas, como demonstradas pelo exemplo [**ColorsDetail**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25/ColorsDetails) .

O exemplo [**MasterDetailTaps**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25/MasterDetailTaps) demonstra outra abordagem usando um `TapGestureRecognizer` nas páginas mestre e de detalhes.

## <a name="tabbedpage"></a>TabbedPage

O [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) é uma coleção de páginas que você pode alternar entre o uso de guias. Ele deriva de `MultiPage<Page>` e define nenhuma propriedade pública ou métodos próprios. [`MultiPage<T>`](xref:Xamarin.Forms.MultiPage`1)no entanto, o define uma propriedade:

- [`Children`](xref:Xamarin.Forms.MultiPage`1.Children) Propriedade do tipo `IList<T>`

Você preenche essa `Children` coleção com objetos de página.

Outra abordagem permite que você defina os `TabbedPage` filhos como um `ListView` usando essas duas propriedades que geram automaticamente as páginas com guias:

- [`ItemsSource`](xref:Xamarin.Forms.MultiPage`1.ItemsSource) do tipo `IEnumerable`
- [`ItemTemplate`](xref:Xamarin.Forms.MultiPage`1.ItemTemplate) do tipo `DataTemplate`

No entanto, essa abordagem não funciona bem no iOS quando a coleção contém mais de alguns itens.

`MultiPage<T>` define mais duas propriedades que permitem manter o controle de qual página está sendo exibida atualmente:

- [`CurrentPage`](xref:Xamarin.Forms.MultiPage`1.CurrentPage) do tipo `T` , fazendo referência à página
- [`SelectedItem`](xref:Xamarin.Forms.MultiPage`1.SelectedItem) do tipo `Object` , fazendo referência ao objeto na `ItemsSource` coleção

`MultiPage<T>` também define dois eventos:

- [`PagesChanged`](xref:Xamarin.Forms.MultiPage`1.PagesChanged) Quando a `ItemsSource` coleção é alterada
- [`CurrentPageChanged`](xref:Xamarin.Forms.MultiPage`1.CurrentPageChanged) Quando a página exibida for alterada

### <a name="discrete-tab-pages"></a>Páginas de guias discretas

O exemplo [**DiscreteTabbedColors**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25/DiscreteTabbedColors) consiste em três páginas com guias que exibem cores de três maneiras diferentes. Cada guia é um `ContentPage` derivativo e, em seguida, o `TabbedPage` [DiscreteTabbedColorsPage. XAML](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter25/DiscreteTabbedColors/DiscreteTabbedColors/DiscreteTabbedColors/DiscreteTabbedColorsPage.xaml) derivado combina as três páginas.

Para cada página exibida em um `TabbedPage` , a `Title` propriedade é necessária para especificar o texto na guia, e a Apple Store requer que um ícone também seja usado, portanto, a `Icon` propriedade é definida para IOS:

[![Captura de tela tripla de cores separadas com guias](images/ch25fg13-small.png "TabbedPage")](images/ch25fg13-large.png#lightbox "TabbedPage")

O exemplo [**StudentNotes**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25/StudentNotes) tem um home page que lista todos os alunos. Quando um aluno é tocado, ele navega para um `TabbedPage` derivativo, [`StudentNotesDataPage`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter25/StudentNotes/StudentNotes/StudentNotes/StudentNotesDataPage.xaml) que incorpora três `ContentPage` objetos em sua árvore visual, um dos quais permite inserir algumas observações para esse aluno.

### <a name="using-an-itemtemplate"></a>Usando um ItemTemplate

O exemplo [**MultiTabbedColor**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25/MultiTabbedColors) usa a [`NamedColor`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NamedColor.cs) classe na biblioteca [**Xamarin.Forms book. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) . O arquivo [MultiTabbedColorsPage. XAML](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter25/MultiTabbedColors/MultiTabbedColors/MultiTabbedColors/MultiTabbedColorsPage.xaml) define a `DataTemplate` propriedade de `TabbedPage` como uma árvore visual que começa com `ContentPage` que contém associações a propriedades de `NamedColor` (incluindo uma associação à `Title` Propriedade).

No entanto, isso é problemático no iOS. Apenas alguns dos itens podem ser exibidos e não há uma boa maneira de dar a eles ícones.

## <a name="related-links"></a>Links relacionados

- [Capítulo 25 texto completo (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch25-Apr2016.pdf)
- [Exemplos do capítulo 25](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25)
- [Página mestre-detalhes](~/xamarin-forms/app-fundamentals/navigation/flyoutpage.md)
- [Página com guias](~/xamarin-forms/app-fundamentals/navigation/tabbed-page.md)
