---
title: Resumo do Capítulo 25. Variedades de página
description: 'Criando aplicativos móveis com Xamarin.Forms: Resumo do Capítulo 25. Variedades de página'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: D1D348F2-6A44-4781-ADCE-A0B7BB9AEF89
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2017
ms.openlocfilehash: b86f2d7216a6344b14fc4d8c538ea68871eda5ae
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "70760541"
---
# <a name="summary-of-chapter-25-page-varieties"></a>Resumo do Capítulo 25. Variedades de página

[![Baixar](~/media/shared/download.png) amostra Baixar a amostra](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25)

Até agora você viu duas classes `Page`que `ContentPage` `NavigationPage`derivam de : e . Este capítulo apresenta outros dois:

- [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage)gerencia duas páginas, um mestre e um detalhe
- [`TabbedPage`](xref:Xamarin.Forms.TabbedPage)gerencia várias páginas de crianças acessadas através de guias

Esses tipos de página fornecem opções de navegação mais sofisticadas do que as `NavagationPage` discutidas no Capítulo [24. Navegação de página](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter24.md).

## <a name="master-and-detail"></a>Mestre e Detalhe

O [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) define duas propriedades `Page` [`Master`](xref:Xamarin.Forms.MasterDetailPage.Master) do [`Detail`](xref:Xamarin.Forms.MasterDetailPage.Detail)tipo : e . Geralmente você define cada uma `ContentPage`dessas propriedades como um . Os `MasterDetailPage` displays e os interruptores entre essas duas páginas.

Há duas maneiras fundamentais de alternar entre essas duas páginas:

- *dividir* onde o mestre e detalhes estão lado a lado
- *popover* onde a página de detalhes cobre ou cobre parcialmente a página mestre

Existem várias variações da abordagem *popover* *(slide,* *sobreposição*e *swap),* mas estas geralmente são dependentes da plataforma. Você pode [`MasterDetailBehavior`](xref:Xamarin.Forms.MasterDetailPage.MasterBehavior) definir `MasterDetailPage` a propriedade de [`MasterBehavior`](xref:Xamarin.Forms.MasterBehavior) um membro da enumeração:

- [`Default`](xref:Xamarin.Forms.MasterBehavior.Default)
- [`Split`](xref:Xamarin.Forms.MasterBehavior.Split)
- [`SplitOnLandscape`](xref:Xamarin.Forms.MasterBehavior.SplitOnLandscape)
- [`SplitOnPortrait`](xref:Xamarin.Forms.MasterBehavior.SplitOnPortrait)
- [`Popover`](xref:Xamarin.Forms.MasterBehavior.Popover)

No entanto, esta propriedade não tem efeito sobre os telefones. Telefones sempre têm um comportamento popover. Apenas tablets e janelas de desktop podem ter um comportamento dividido.

### <a name="exploring-the-behaviors"></a>Explorando os Comportamentos

A amostra [**MasterDetailBehaviors**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25/MasterDetailBehaviors) permite que você experimente o comportamento padrão em diferentes dispositivos. O programa contém `ContentPage` dois derivativos separados `Title` para o mestre e detalhe (com `MasterDetailPage` uma propriedade definida em ambos), e outra classe que deriva disso combina-los. A página de detalhes `NavigationPage` é incluída em um porque o programa UWP não funcionaria sem ele.

As plataformas Windows 8.1 e Windows Phone 8.1 exigem `Icon` que um bitmap seja definido para a propriedade da página-mestre.

### <a name="back-to-school"></a>De volta à escola

A amostra [**SchoolAndDetail**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25/SchoolAndDetail) adota uma abordagem um pouco diferente para construir o programa para exibir os alunos da biblioteca [**SchoolOfFineArt.**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/SchoolOfFineArt)

As `Master` `Detail` propriedades são definidas com árvores visuais no arquivo [SchoolAndDetailPage.xaml,](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter25/SchoolAndDetail/SchoolAndDetail/SchoolAndDetail/SchoolAndDetailPage.xaml) que deriva de `MasterDetailPage`. Este arranjo permite que as vinculações de dados sejam definidas entre as páginas mestre e de detalhes.

Esse arquivo XAML [`IsPresented`](xref:Xamarin.Forms.MasterDetailPage.IsPresented) também `MasterDetailPage` define `True`a propriedade de . Isso faz com que a página-mestre seja exibida na inicialização; por padrão, a página de detalhes é exibida. O [arquivo](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter25/SchoolAndDetail/SchoolAndDetail/SchoolAndDetail/SchoolAndDetailPage.xaml.cs) `IsPresented` SchoolAndDetailPage.xaml.cs `false` define para quando um `ListView` item é selecionado a partir da página-mestre. A página de detalhes é então exibida:

[![Captura de tela tripla de Escola e Detalhes](images/ch25fg09-small.png "Página de detalhes de uma Página MasterDetail")](images/ch25fg09-large.png#lightbox "Página de detalhes de uma Página MasterDetail")

### <a name="your-own-user-interface"></a>Sua própria interface de usuário

Embora o Xamarin.Forms forneça uma interface de usuário para alternar entre as visualizações mestre e detalhada, você pode fornecer a sua própria. Para fazer isso:

- Defina [`IsGestureEnabled`](xref:Xamarin.Forms.MasterDetailPage.IsGestureEnabled) a `false` propriedade para desativar o deslizamento
- Anular o [`ShouldShowToolbarButton`](xref:Xamarin.Forms.MasterDetailPage.ShouldShowToolbarButton) método `false` e retornar para ocultar os botões da barra de ferramentas no Windows 8.1 e no Windows Phone 8.1.

Em seguida, você deve fornecer um meio de alternar entre as páginas mestre e de detalhes, como demonstrado pela amostra [**ColorsDetail.**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25/ColorsDetails)

A amostra [**MasterDetailTaps**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25/MasterDetailTaps) demonstra `TapGestureRecognizer` outra abordagem usando uma nas páginas master e detail.

## <a name="tabbedpage"></a>TabbedPage

A [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) é uma coleção de páginas que você pode alternar entre as guias de uso. Deriva `MultiPage<Page>` e não define propriedades públicas ou métodos próprios. [`MultiPage<T>`](xref:Xamarin.Forms.MultiPage`1), no entanto, define uma propriedade:

- [`Children`](xref:Xamarin.Forms.MultiPage`1.Children)propriedade do tipo`IList<T>`

Você preenche `Children` esta coleção com objetos de página.

Outra abordagem permite `TabbedPage` definir as `ListView` crianças como um uso dessas duas propriedades que geram as páginas com guias automaticamente:

- [`ItemsSource`](xref:Xamarin.Forms.MultiPage`1.ItemsSource)de tipo`IEnumerable`
- [`ItemTemplate`](xref:Xamarin.Forms.MultiPage`1.ItemTemplate)de tipo`DataTemplate`

No entanto, essa abordagem não funciona bem no iOS quando a coleção contém mais do que alguns itens.

`MultiPage<T>`define mais duas propriedades que permitem acompanhar qual página é visualizada no momento:

- [`CurrentPage`](xref:Xamarin.Forms.MultiPage`1.CurrentPage)de `T`tipo, referindo-se à página
- [`SelectedItem`](xref:Xamarin.Forms.MultiPage`1.SelectedItem)de `Object`tipo , referindo-se `ItemsSource` ao objeto na coleção

`MultiPage<T>`também define dois eventos:

- [`PagesChanged`](xref:Xamarin.Forms.MultiPage`1.PagesChanged)quando `ItemsSource` a coleção muda
- [`CurrentPageChanged`](xref:Xamarin.Forms.MultiPage`1.CurrentPageChanged)quando a página visualizada muda

### <a name="discrete-tab-pages"></a>Páginas de guias discretas

A amostra [**DiscreteTabbedColors**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25/DiscreteTabbedColors) consiste em três páginas com guias que exibem cores de três maneiras diferentes. Cada guia `ContentPage` é uma derivada e, em seguida, a `TabbedPage` derivada [DiscreteTabbedColorsPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter25/DiscreteTabbedColors/DiscreteTabbedColors/DiscreteTabbedColors/DiscreteTabbedColorsPage.xaml) combina as três páginas.

Para cada página que `TabbedPage`aparece `Title` em um , a propriedade é necessária para especificar o texto na guia, e a Apple Store exige que um ícone seja usado também, de modo que a `Icon` propriedade seja definida para iOS:

[![Captura de tela tripla de cores discretas com abas](images/ch25fg13-small.png "TabbedPage")](images/ch25fg13-large.png#lightbox "TabbedPage")

A amostra [**StudentNotes**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25/StudentNotes) tem uma página inicial que lista todos os alunos. Quando um aluno é aproveitado, `TabbedPage` isso [`StudentNotesDataPage`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter25/StudentNotes/StudentNotes/StudentNotes/StudentNotesDataPage.xaml)navega para `ContentPage` um derivado, que incorpora três objetos em sua árvore visual, um dos quais permite inserir algumas notas para aquele aluno.

### <a name="using-an-itemtemplate"></a>Usando um modelo de itens

A amostra [**MultiTabbedColor**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25/MultiTabbedColors) usa a [`NamedColor`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NamedColor.cs) classe na biblioteca [**Xamarin.FormsBook.Toolkit.**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) O arquivo [MultiTabbedColorsPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter25/MultiTabbedColors/MultiTabbedColors/MultiTabbedColors/MultiTabbedColorsPage.xaml) `DataTemplate` define `TabbedPage` a propriedade de `ContentPage` uma árvore visual `NamedColor` que começa com `Title` a que contém vinculações às propriedades de (incluindo uma vinculação à propriedade).

No entanto, isso é problemático no iOS. Apenas alguns dos itens podem ser exibidos, e não há uma boa maneira de dar-lhes ícones.

## <a name="related-links"></a>Links relacionados

- [Capítulo 25 texto completo (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch25-Apr2016.pdf)
- [Capítulo 25 amostras](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25)
- [Página de detalhes-mestre](~/xamarin-forms/app-fundamentals/navigation/master-detail-page.md)
- [Página com guias](~/xamarin-forms/app-fundamentals/navigation/tabbed-page.md)
