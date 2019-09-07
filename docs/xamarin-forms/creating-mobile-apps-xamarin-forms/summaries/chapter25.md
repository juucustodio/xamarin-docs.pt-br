---
title: Resumo do Capítulo 25. Variedades de página
description: 'Criando aplicativos móveis com Xamarin. Forms: Resumo do Capítulo 25. Variedades de página'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: D1D348F2-6A44-4781-ADCE-A0B7BB9AEF89
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2017
ms.openlocfilehash: b86f2d7216a6344b14fc4d8c538ea68871eda5ae
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70760541"
---
# <a name="summary-of-chapter-25-page-varieties"></a>Resumo do Capítulo 25. Variedades de página

[![Baixar exemplo](~/media/shared/download.png) baixar o exemplo](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25)

Até agora, você viu duas classes que derivam de `Page`: `ContentPage` e `NavigationPage`. Este capítulo apresenta dois outros:

- [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) gerencia duas páginas, um mestre e um detalhe
- [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) gerencia várias páginas filho acessadas por meio de guias

Esses tipos de página fornecem opções de navegação mais sofisticadas que o `NavagationPage` discutidas [capítulo 24. Navegação de página](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter24.md).

## <a name="master-and-detail"></a>Mestre e detalhes

O [ `MasterDetailPage` ](xref:Xamarin.Forms.MasterDetailPage) define duas propriedades do tipo `Page`: [ `Master` ](xref:Xamarin.Forms.MasterDetailPage.Master) e [ `Detail` ](xref:Xamarin.Forms.MasterDetailPage.Detail). Geralmente você define cada uma dessas propriedades para um `ContentPage`. O `MasterDetailPage` exibe e alterna entre essas duas páginas.

Há duas maneiras fundamentais para alternar entre essas duas páginas:

- *Dividir* em que o mestre e detalhes são lado a lado
- *pop-over* onde a página de detalhes aborda ou cobre parcialmente o mestre de página

Há diversas variações do *pop-over* abordagem (*slide*, *se sobrepõem*, e *permuta*), mas geralmente são plataforma dependentes. Você pode definir as [ `MasterDetailBehavior` ](xref:Xamarin.Forms.MasterDetailPage.MasterBehavior) propriedade do `MasterDetailPage` a um membro dos [ `MasterBehavior` ](xref:Xamarin.Forms.MasterBehavior) enumeração:

- [`Default`](xref:Xamarin.Forms.MasterBehavior.Default)
- [`Split`](xref:Xamarin.Forms.MasterBehavior.Split)
- [`SplitOnLandscape`](xref:Xamarin.Forms.MasterBehavior.SplitOnLandscape)
- [`SplitOnPortrait`](xref:Xamarin.Forms.MasterBehavior.SplitOnPortrait)
- [`Popover`](xref:Xamarin.Forms.MasterBehavior.Popover)

No entanto, essa propriedade não tem efeito em telefones. Telefones sempre têm um comportamento de pop-over. Somente os tablets e desktops windows podem ter um comportamento de divisão.

### <a name="exploring-the-behaviors"></a>Explorar os comportamentos

O [ **MasterDetailBehaviors** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25/MasterDetailBehaviors) exemplo permite que você pode experimentar com o comportamento padrão em dispositivos diferentes. O programa contém dois separado `ContentPage` derivados para o mestre e de detalhes (com um `Title` propriedade definida em ambos) e outra classe que deriva de `MasterDetailPage` que combina-os. A página de detalhes é colocada entre um `NavigationPage` porque o programa UWP não funcionará sem ela.

As plataformas Windows 8.1 e Windows Phone 8.1 exigem que um bitmap seja definida como o `Icon` propriedade da página mestra.

### <a name="back-to-school"></a>De volta às aulas

O [ **SchoolAndDetail** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25/SchoolAndDetail) exemplo adota uma abordagem um pouco diferente para construir o programa para exibir os alunos do [ **SchoolOfFineArt** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/SchoolOfFineArt) biblioteca.

O `Master` e `Detail` propriedades são definidas com as árvores visuais nos [SchoolAndDetailPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter25/SchoolAndDetail/SchoolAndDetail/SchoolAndDetail/SchoolAndDetailPage.xaml) arquivo, que deriva de `MasterDetailPage`. Essa organização permite que as associações de dados a ser definido entre as páginas mestre e de detalhes.

Arquivo XAML também define o [ `IsPresented` ](xref:Xamarin.Forms.MasterDetailPage.IsPresented) propriedade do `MasterDetailPage` para `True`. Isso faz com que a página mestra a ser exibida na inicialização; Por padrão, a página de detalhes é exibida. O [SchoolAndDetailPage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter25/SchoolAndDetail/SchoolAndDetail/SchoolAndDetail/SchoolAndDetailPage.xaml.cs) arquivo define `IsPresented` à `false` quando um item é selecionado do `ListView` na página mestra. A página de detalhes, em seguida, é exibida:

[![Captura de tela de detalhes de escola e tripla](images/ch25fg09-small.png "página de detalhes de um MasterDetailPage")](images/ch25fg09-large.png#lightbox "página de detalhes de um MasterDetailPage")

### <a name="your-own-user-interface"></a>Sua própria interface do usuário

Embora o xamarin. Forms fornece uma interface do usuário para alternar entre os modos de exibição mestre e de detalhes, você pode fornecer seu próprio. Para fazer isso:

- Defina a [ `IsGestureEnabled` ](xref:Xamarin.Forms.MasterDetailPage.IsGestureEnabled) propriedade `false` para desabilitar o dedo para
- Substituir a [ `ShouldShowToolbarButton` ](xref:Xamarin.Forms.MasterDetailPage.ShouldShowToolbarButton) método e retorne `false` para ocultar os botões da barra de ferramentas no Windows 8.1 e Windows Phone 8.1.

É necessário fornecer um meio para alternar entre as páginas mestre e de detalhes, como demonstrado pelo [ **ColorsDetail** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25/ColorsDetails) exemplo.

O [ **MasterDetailTaps** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25/MasterDetailTaps) demonstra outra abordagem usando um `TapGestureRecognizer` nas páginas mestre e de detalhes.

## <a name="tabbedpage"></a>TabbedPage

O [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage) é uma coleção de páginas que você pode alternar entre usando as guias. Ela é derivada da `MultiPage<Page>` e define não propriedades públicas nem os métodos do seu próprio. [`MultiPage<T>`](xref:Xamarin.Forms.MultiPage`1), no entanto, definir uma propriedade:

- [`Children`](xref:Xamarin.Forms.MultiPage`1.Children) propriedade de tipo `IList<T>`

Você preenche isso `Children` coleção com objetos de página.

Outra abordagem permite que você defina as `TabbedPage` filhos assim como um `ListView` usando essas duas propriedades que geram as páginas com guias automaticamente:

- [`ItemsSource`](xref:Xamarin.Forms.MultiPage`1.ItemsSource) do tipo `IEnumerable`
- [`ItemTemplate`](xref:Xamarin.Forms.MultiPage`1.ItemTemplate) do tipo `DataTemplate`

No entanto, essa abordagem não funciona bem no iOS quando a coleção contém mais de alguns itens.

`MultiPage<T>` define duas propriedades que permitem que você mantenha o controle de qual página é atualmente exibida:

- [`CurrentPage`](xref:Xamarin.Forms.MultiPage`1.CurrentPage) do tipo `T`, fazendo referência à página
- [`SelectedItem`](xref:Xamarin.Forms.MultiPage`1.SelectedItem) do tipo `Object`, fazendo referência ao objeto no `ItemsSource` coleção

`MultiPage<T>` também define dois eventos:

- [`PagesChanged`](xref:Xamarin.Forms.MultiPage`1.PagesChanged) Quando o `ItemsSource` alterações da coleção
- [`CurrentPageChanged`](xref:Xamarin.Forms.MultiPage`1.CurrentPageChanged) Quando a página exibida é alterado

### <a name="discrete-tab-pages"></a>Páginas de guia discretos

O [ **DiscreteTabbedColors** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25/DiscreteTabbedColors) exemplo consiste em três páginas com guias que exibem as cores de três maneiras diferentes. Cada guia é uma `ContentPage` derivativo e, em seguida, o `TabbedPage` derivativo [DiscreteTabbedColorsPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter25/DiscreteTabbedColors/DiscreteTabbedColors/DiscreteTabbedColors/DiscreteTabbedColorsPage.xaml) combina três páginas.

Para cada página que aparece em uma `TabbedPage`, o `Title` propriedade é necessária para especificar o texto na guia e a Apple Store requer que um ícone usado além disso, para que o `Icon` propriedade é definida para iOS:

[![Captura de tela tripla de cores distintas com guias](images/ch25fg13-small.png "TabbedPage")](images/ch25fg13-large.png#lightbox "TabbedPage")

O [ **StudentNotes** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25/StudentNotes) exemplo tem uma home page que lista todos os alunos. Quando um aluno é tocado, navega até um `TabbedPage` derivativo, [ `StudentNotesDataPage` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter25/StudentNotes/StudentNotes/StudentNotes/StudentNotesDataPage.xaml), que incorpora os três `ContentPage` objetos na sua árvore visual, uma delas permite inserir algumas anotações de aluno.

### <a name="using-an-itemtemplate"></a>Usando um ItemTemplate

O [ **MultiTabbedColor** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25/MultiTabbedColors) de exemplo usa o [ `NamedColor` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NamedColor.cs) classe o [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) biblioteca. O [MultiTabbedColorsPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter25/MultiTabbedColors/MultiTabbedColors/MultiTabbedColors/MultiTabbedColorsPage.xaml) arquivo define a `DataTemplate` propriedade do `TabbedPage` para que começa com uma árvore visual `ContentPage` que contém associações a propriedades de `NamedColor` (incluindo uma associação para o `Title` propriedade).

No entanto, isso é problemático no iOS. Apenas alguns dos itens que podem ser exibidos e não há nenhuma boa maneira de dar a eles ícones.

## <a name="related-links"></a>Links relacionados

- [Capítulo 25 de texto completo (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch25-Apr2016.pdf)
- [Exemplos do Capítulo 25](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter25)
- [Página Master-Detail](~/xamarin-forms/app-fundamentals/navigation/master-detail-page.md)
- [Página com guias](~/xamarin-forms/app-fundamentals/navigation/tabbed-page.md)
