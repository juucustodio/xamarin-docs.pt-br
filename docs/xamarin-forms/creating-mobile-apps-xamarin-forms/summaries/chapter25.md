---
title: Resumo do Capítulo 25. Variedades de página
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: D1D348F2-6A44-4781-ADCE-A0B7BB9AEF89
author: charlespetzold
ms.author: chape
ms.date: 11/07/2017
ms.openlocfilehash: 951ae41763d8338d5adf73fb46ebc6defa64f8f8
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="summary-of-chapter-25-page-varieties"></a>Resumo do Capítulo 25. Variedades de página

Até agora, você viu duas classes que derivam de `Page`: `ContentPage` e `NavigationPage`. Este capítulo apresenta dois outros:

- [`MasterDetailPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.MasterDetailPage/) gerencia duas páginas, um mestre e um detalhe
- [`TabbedPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/) gerencia várias páginas filho acessadas por meio de guias

Esses tipos de página fornecem opções de navegação mais sofisticadas do que o `NavagationPage` discutidos [capítulo 24. Navegação de página](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter24.md).

## <a name="master-and-detail"></a>Mestre e detalhes

O [ `MasterDetailPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MasterDetailPage/) define duas propriedades do tipo `Page`: [ `Master` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MasterDetailPage.Master/) e [ `Detail` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MasterDetailPage.Detail/). Geralmente você define cada uma dessas propriedades para um `ContentPage`. O `MasterDetailPage` exibe e alterna entre essas duas páginas.

Há duas maneiras fundamentais para alternar entre essas duas páginas:

- *Dividir* onde o mestre e detalhes estão lado a lado
- *popover* onde a página de detalhes abrange ou abrange parcialmente o mestre de página

Há diversas variações do *popover* abordagem (*slide*, *sobrepor*, e *permuta*), mas geralmente são plataforma dependentes. Você pode definir o [ `MasterDetailBehavior` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MasterDetailPage.MasterBehavior/) propriedade `MasterDetailPage` a um membro do [ `MasterBehavior` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MasterBehavior/) enumeração:

- [`Default`](https://developer.xamarin.com/api/field/Xamarin.Forms.MasterBehavior.Default/)
- [`Split`](https://developer.xamarin.com/api/field/Xamarin.Forms.MasterBehavior.Split/)
- [`SplitOnLandscape`](https://developer.xamarin.com/api/field/Xamarin.Forms.MasterBehavior.SplitOnLandscape/)
- [`SplitOnPortrait`](https://developer.xamarin.com/api/field/Xamarin.Forms.MasterBehavior.SplitOnPortrait/)
- [`Popover`](https://developer.xamarin.com/api/field/Xamarin.Forms.MasterBehavior.Popover/)

No entanto, essa propriedade não tem efeito em telefones. Telefones sempre têm um comportamento popover. Somente tablets e windows de área de trabalho podem ter um comportamento de divisão.

### <a name="exploring-the-behaviors"></a>Explorando os comportamentos

O [ **MasterDetailBehaviors** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25/MasterDetailBehaviors) exemplo permite fazer experiências com o comportamento padrão em diferentes dispositivos. O programa contém dois separado `ContentPage` derivados para o mestre e de detalhes (com um `Title` propriedade definida em ambos) e outra classe que deriva de `MasterDetailPage` que combina. A página de detalhes está contida em um `NavigationPage` porque o programa UWP não funcionar sem ele.

As plataformas Windows 8.1 e Windows Phone 8.1 exigem que um bitmap seja definida como o `Icon` propriedade da página mestra.

### <a name="back-to-school"></a>De volta à escola

O [ **SchoolAndDetail** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25/SchoolAndDetail) exemplo adota uma abordagem diferente para construir o programa para exibir os alunos do [ **SchoolOfFineArt** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/SchoolOfFineArt) biblioteca.

O `Master` e `Detail` propriedades são definidas com árvores visual a [SchoolAndDetailPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter25/SchoolAndDetail/SchoolAndDetail/SchoolAndDetail/SchoolAndDetailPage.xaml) arquivo, que deriva de `MasterDetailPage`. Essa organização permite associações de dados a ser definido entre as páginas mestre e de detalhes.

Arquivo XAML também define o [ `IsPresented` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MasterDetailPage.IsPresented/) propriedade `MasterDetailPage` para `True`. Isso faz com que a página mestra a ser exibida na inicialização; Por padrão, a página de detalhes é exibida. O [SchoolAndDetailPage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter25/SchoolAndDetail/SchoolAndDetail/SchoolAndDetail/SchoolAndDetailPage.xaml.cs) conjuntos de arquivos `IsPresented` para `false` quando um item é selecionado do `ListView` na página mestra. A página de detalhes, em seguida, é exibida:

[![Captura de tela tripla da escola e detalhes](images/ch25fg09-small.png "página de detalhes de um MasterDetailPage")](images/ch25fg09-large.png#lightbox "página de detalhes de um MasterDetailPage")

### <a name="your-own-user-interface"></a>Sua própria interface do usuário

Embora xamarin. Forms fornece uma interface de usuário para alternar entre os modos de exibição mestre e de detalhes, você pode fornecer seu próprio. Para fazer isso:

- Definir o [ `IsGestureEnabled` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MasterDetailPage.IsGestureEnabled/) propriedade `false` para desabilitar a passar o dedo
- Substituir o [ `ShouldShowToolbarButton` ](https://developer.xamarin.com/api/member/Xamarin.Forms.MasterDetailPage.ShouldShowToolbarButton()/) método e retornar `false` para ocultar os botões da barra de ferramentas no Windows 8.1 e Windows Phone 8.1.

Em seguida, forneça um meio para alternar entre as páginas mestre e de detalhes, como demonstrado pelo [ **ColorsDetail** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25/ColorsDetails) exemplo.

O [ **MasterDetailTaps** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25/MasterDetailTaps) demonstra outra abordagem usando um `TapGestureRecognizer` nas páginas mestre e de detalhes.

## <a name="tabbedpage"></a>TabbedPage

O [ `TabbedPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/) é uma coleção de páginas que você pode alternar entre usando as guias. Deriva de `MultiPage<Page>` e define sem propriedades públicas nem os métodos de seu próprio. [`MultiPage<T>`](https://developer.xamarin.com/api/type/Xamarin.Forms.MultiPage%3CT%3E/), no entanto, definir uma propriedade:

- [`Children`](https://developer.xamarin.com/api/property/Xamarin.Forms.MultiPage%3CT%3E.Children/) propriedade do tipo `IList<T>`

Você preencha `Children` coleção com objetos da página.

Outra abordagem permite que você defina o `TabbedPage` filhos assim como um `ListView` usando essas duas propriedades que geram as páginas com guias automaticamente:

- [`ItemsSource`](https://developer.xamarin.com/api/property/Xamarin.Forms.MultiPage%3CT%3E.ItemsSource/) do tipo `IEnumerable`
- [`ItemTemplate`](https://developer.xamarin.com/api/property/Xamarin.Forms.MultiPage%3CT%3E.ItemTemplate/) do tipo `DataTemplate`

No entanto, essa abordagem não funciona bem em iOS quando a coleção contém mais do que alguns itens.

`MultiPage<T>` define duas propriedades que permitem que você mantenha o controle de qual página é exibida no momento:

- [`CurrentPage`](https://developer.xamarin.com/api/property/Xamarin.Forms.MultiPage%3CT%3E.CurrentPage/) tipo `T`, fazendo referência à página
- [`SelectedItem`](https://developer.xamarin.com/api/property/Xamarin.Forms.MultiPage%3CT%3E.SelectedItem/) tipo `Object`, fazendo referência ao objeto no `ItemsSource` coleção

`MultiPage<T>` também define dois eventos:

- [`PagesChanged`](https://developer.xamarin.com/api/event/Xamarin.Forms.MultiPage%3CT%3E.PagesChanged/) Quando o `ItemsSource` alterações da coleção
- [`CurrentPageChanged`](https://developer.xamarin.com/api/event/Xamarin.Forms.MultiPage%3CT%3E.CurrentPageChanged/) Quando a página exibida é alterado

### <a name="discrete-tab-pages"></a>Páginas de guias discretos

O [ **DiscreteTabbedColors** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25/DiscreteTabbedColors) exemplo consiste em três páginas com guias que exibem as cores de três maneiras diferentes. Cada guia é um `ContentPage` derivado e, em seguida, o `TabbedPage` derivativo [DiscreteTabbedColorsPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter25/DiscreteTabbedColors/DiscreteTabbedColors/DiscreteTabbedColors/DiscreteTabbedColorsPage.xaml) combina três páginas.

Para cada página que aparece em uma `TabbedPage`, o `Title` propriedade é necessária para especificar o texto na guia e Apple Store requer que um ícone, assim a `Icon` propriedade está definida para iOS:

[![Captura de tela de tripla de cores distintas com guias](images/ch25fg13-small.png "TabbedPage")](images/ch25fg13-large.png#lightbox "TabbedPage")

O [ **StudentNotes** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25/StudentNotes) exemplo tem uma home page que lista todos os alunos. Quando um aluno é tocado, navega até um `TabbedPage` derivado, [ `StudentNotesDataPage` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter25/StudentNotes/StudentNotes/StudentNotes/StudentNotesDataPage.xaml), que incorpora três `ContentPage` objetos na árvore visual, um dos quais permite inserir algumas observações para esse aluno.

### <a name="using-an-itemtemplate"></a>Usando um ItemTemplate

O [ **MultiTabbedColor** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25/MultiTabbedColors) usa o [ `NamedColor` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NamedColor.cs) classe no [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) biblioteca. O [MultiTabbedColorsPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter25/MultiTabbedColors/MultiTabbedColors/MultiTabbedColors/MultiTabbedColorsPage.xaml) arquivo define o `DataTemplate` propriedade de `TabbedPage` para um início de árvore visual com `ContentPage` que contém associações a propriedades de `NamedColor` (incluindo uma associação para o `Title` propriedade).

No entanto, isso é problemático no iOS. Apenas alguns dos itens podem ser exibidos e não é possível dar a eles ícones BOM.



## <a name="related-links"></a>Links relacionados

- [Texto completo de 25 de capítulo (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch25-Apr2016.pdf)
- [Exemplos de capítulo 25](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25)
- [Página de detalhes mestre](~/xamarin-forms/app-fundamentals/navigation/master-detail-page.md)
- [Página com guias](~/xamarin-forms/app-fundamentals/navigation/tabbed-page.md)
