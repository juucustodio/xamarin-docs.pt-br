---
title: Resumo do Capítulo 25. Variedades de página
description: 'Criação de aplicativos móveis com xamarin. Forms: Resumo do Capítulo 25. Variedades de página'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: D1D348F2-6A44-4781-ADCE-A0B7BB9AEF89
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2017
ms.openlocfilehash: b86f2d7216a6344b14fc4d8c538ea68871eda5ae
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2020
ms.locfileid: "70760541"
---
# <a name="summary-of-chapter-25-page-varieties"></a>Resumo do Capítulo 25. Variedades de página

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25)

Até agora, você viu duas classes que derivam de `Page`: `ContentPage` e `NavigationPage`. Este capítulo apresenta dois outros:

- [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) gerencia duas páginas, um mestre e um detalhe
- [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) gerencia várias páginas filho acessadas por meio de guias

Esses tipos de página fornecem opções de navegação mais sofisticadas do que as `NavagationPage` discutidas no [capítulo 24. Navegação de página](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter24.md).

## <a name="master-and-detail"></a>Mestre e detalhes

O [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) define duas propriedades do tipo `Page`: [`Master`](xref:Xamarin.Forms.MasterDetailPage.Master) e [`Detail`](xref:Xamarin.Forms.MasterDetailPage.Detail). Em geral, você define cada uma dessas propriedades para um `ContentPage`. O `MasterDetailPage` exibe e alterna entre essas duas páginas.

Há duas maneiras fundamentais para alternar entre essas duas páginas:

- *dividir* onde o mestre e os detalhes estão lado a lado
- *popover* onde a página de detalhes cobre ou aborda parcialmente a página mestra

Há várias variações da abordagem *popover* (*Slide*, *sobreposição*e *troca*), mas elas geralmente são dependentes da plataforma. Você pode definir a propriedade [`MasterDetailBehavior`](xref:Xamarin.Forms.MasterDetailPage.MasterBehavior) de `MasterDetailPage` como um membro da enumeração [`MasterBehavior`](xref:Xamarin.Forms.MasterBehavior) :

- [`Default`](xref:Xamarin.Forms.MasterBehavior.Default)
- [`Split`](xref:Xamarin.Forms.MasterBehavior.Split)
- [`SplitOnLandscape`](xref:Xamarin.Forms.MasterBehavior.SplitOnLandscape)
- [`SplitOnPortrait`](xref:Xamarin.Forms.MasterBehavior.SplitOnPortrait)
- [`Popover`](xref:Xamarin.Forms.MasterBehavior.Popover)

No entanto, essa propriedade não tem efeito em telefones. Telefones sempre têm um comportamento de pop-over. Somente os tablets e desktops windows podem ter um comportamento de divisão.

### <a name="exploring-the-behaviors"></a>Explorar os comportamentos

O exemplo [**MasterDetailBehaviors**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25/MasterDetailBehaviors) permite que você experimente o comportamento padrão em diferentes dispositivos. O programa contém duas derivações `ContentPage` separadas para o mestre e os detalhes (com uma propriedade `Title` definida em ambos) e outra classe que deriva de `MasterDetailPage` que as combina. A página de detalhes é colocada em uma `NavigationPage` porque o programa UWP não funcionaria sem ele.

As plataformas Windows 8.1 e Windows Phone 8,1 exigem que um bitmap seja definido como a propriedade `Icon` da página mestra.

### <a name="back-to-school"></a>De volta à escola

O exemplo de [**SchoolAndDetail**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25/SchoolAndDetail) usa uma abordagem um pouco diferente para construir o programa para exibir alunos da biblioteca [**SchoolOfFineArt**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/SchoolOfFineArt) .

As propriedades `Master` e `Detail` são definidas com árvores visuais no arquivo [SchoolAndDetailPage. XAML](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter25/SchoolAndDetail/SchoolAndDetail/SchoolAndDetail/SchoolAndDetailPage.xaml) , que deriva de `MasterDetailPage`. Essa organização permite que as associações de dados a ser definido entre as páginas mestre e de detalhes.

Esse arquivo XAML também define a propriedade [`IsPresented`](xref:Xamarin.Forms.MasterDetailPage.IsPresented) de `MasterDetailPage` como `True`. Isso faz com que a página mestra a ser exibida na inicialização; Por padrão, a página de detalhes é exibida. O arquivo [SchoolAndDetailPage.XAML.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter25/SchoolAndDetail/SchoolAndDetail/SchoolAndDetail/SchoolAndDetailPage.xaml.cs) define `IsPresented` para `false` quando um item é selecionado do `ListView` na página mestra. A página de detalhes, em seguida, é exibida:

[![Captura de tela tripla de escola e detalhes](images/ch25fg09-small.png "Página de detalhes de um MasterDetailPage")](images/ch25fg09-large.png#lightbox "Página de detalhes de um MasterDetailPage")

### <a name="your-own-user-interface"></a>Sua própria interface do usuário

Embora o xamarin. Forms fornece uma interface do usuário para alternar entre os modos de exibição mestre e de detalhes, você pode fornecer seu próprio. Para fazer isso:

- Defina a propriedade [`IsGestureEnabled`](xref:Xamarin.Forms.MasterDetailPage.IsGestureEnabled) como `false` para desabilitar o passar o dedo
- Substitua o método [`ShouldShowToolbarButton`](xref:Xamarin.Forms.MasterDetailPage.ShouldShowToolbarButton) e retorne `false` para ocultar os botões da barra de ferramentas em Windows 8.1 e Windows Phone 8,1.

Em seguida, você deve fornecer um meio para alternar entre as páginas mestras e detalhadas, como demonstradas pelo exemplo [**ColorsDetail**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25/ColorsDetails) .

O exemplo [**MasterDetailTaps**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25/MasterDetailTaps) demonstra outra abordagem usando uma `TapGestureRecognizer` nas páginas mestre e de detalhes.

## <a name="tabbedpage"></a>TabbedPage

O [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) é uma coleção de páginas que você pode alternar entre o uso de guias. Ele deriva de `MultiPage<Page>` e não define propriedades públicas ou métodos próprios. no entanto, [`MultiPage<T>`](xref:Xamarin.Forms.MultiPage`1)define uma propriedade:

- Propriedade [`Children`](xref:Xamarin.Forms.MultiPage`1.Children) do tipo `IList<T>`

Você preenche esse `Children` coleção com objetos de página.

Outra abordagem permite que você defina os `TabbedPage` filhos de forma muito semelhante a uma `ListView` usando essas duas propriedades que geram automaticamente as páginas com guias:

- [`ItemsSource`](xref:Xamarin.Forms.MultiPage`1.ItemsSource) do tipo `IEnumerable`
- [`ItemTemplate`](xref:Xamarin.Forms.MultiPage`1.ItemTemplate) do tipo `DataTemplate`

No entanto, essa abordagem não funciona bem no iOS quando a coleção contém mais de alguns itens.

`MultiPage<T>` define mais duas propriedades que permitem manter o controle de qual página está sendo exibida atualmente:

- [`CurrentPage`](xref:Xamarin.Forms.MultiPage`1.CurrentPage) do tipo `T`, referindo-se à página
- [`SelectedItem`](xref:Xamarin.Forms.MultiPage`1.SelectedItem) do tipo `Object`, referindo-se ao objeto na coleção de `ItemsSource`

`MultiPage<T>` também define dois eventos:

- [`PagesChanged`](xref:Xamarin.Forms.MultiPage`1.PagesChanged) quando a coleção de `ItemsSource` é alterada
- [`CurrentPageChanged`](xref:Xamarin.Forms.MultiPage`1.CurrentPageChanged) quando a página exibida é alterada

### <a name="discrete-tab-pages"></a>Páginas de guia discretos

O exemplo [**DiscreteTabbedColors**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25/DiscreteTabbedColors) consiste em três páginas com guias que exibem cores de três maneiras diferentes. Cada guia é uma derivação `ContentPage` e, em seguida, a `TabbedPage` derivada [DiscreteTabbedColorsPage. XAML](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter25/DiscreteTabbedColors/DiscreteTabbedColors/DiscreteTabbedColors/DiscreteTabbedColorsPage.xaml) combina as três páginas.

Para cada página que aparece em um `TabbedPage`, a propriedade `Title` é necessária para especificar o texto na guia, e a Apple Store requer que um ícone também seja usado, portanto, a propriedade `Icon` está definida para iOS:

[![Captura de tela tripla de cores separadas com guias](images/ch25fg13-small.png "TabbedPage")](images/ch25fg13-large.png#lightbox "TabbedPage")

O exemplo [**StudentNotes**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25/StudentNotes) tem um home page que lista todos os alunos. Quando um aluno é tocado, ele navega para um `TabbedPage` derivativo, [`StudentNotesDataPage`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter25/StudentNotes/StudentNotes/StudentNotes/StudentNotesDataPage.xaml), que incorpora três objetos `ContentPage` em sua árvore visual, um dos quais permite inserir algumas observações para esse aluno.

### <a name="using-an-itemtemplate"></a>Usando um ItemTemplate

O exemplo [**MultiTabbedColor**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25/MultiTabbedColors) usa a classe [`NamedColor`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NamedColor.cs) na biblioteca [**Xamarin. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) . O arquivo [MultiTabbedColorsPage. XAML](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter25/MultiTabbedColors/MultiTabbedColors/MultiTabbedColors/MultiTabbedColorsPage.xaml) define a propriedade `DataTemplate` de `TabbedPage` para uma árvore visual que começa com `ContentPage` que contém associações a propriedades de `NamedColor` (incluindo uma associação à propriedade `Title`).

No entanto, isso é problemático no iOS. Apenas alguns dos itens que podem ser exibidos e não há nenhuma boa maneira de dar a eles ícones.

## <a name="related-links"></a>Links relacionados

- [Capítulo 25 texto completo (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch25-Apr2016.pdf)
- [Exemplos do capítulo 25](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25)
- [Página mestre-detalhes](~/xamarin-forms/app-fundamentals/navigation/master-detail-page.md)
- [Página com guias](~/xamarin-forms/app-fundamentals/navigation/tabbed-page.md)
