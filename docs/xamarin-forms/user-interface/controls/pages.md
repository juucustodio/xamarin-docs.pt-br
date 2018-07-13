---
title: Páginas do xamarin. Forms
description: Páginas do xamarin. Forms representam telas de aplicativos móveis de plataforma cruzada. Este artigo lista as páginas que estão incluídas no xamarin. Forms.
ms.prod: xamarin
ms.assetid: 9C8C710F-E312-420B-9324-A7A20CEDB7EC
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/12/2016
ms.openlocfilehash: fea1db6c65e533692601439f4712d15371740644
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/12/2018
ms.locfileid: "38995892"
---
# <a name="xamarinforms-pages"></a>Páginas do xamarin. Forms

_Páginas do xamarin. Forms representam telas de aplicativos móveis de plataforma cruzada._

Todos os tipos de página que são descritos abaixo derivam da classe [`Page`](xref:Xamarin.Forms.Page) do Xamarin.Forms. Esses elementos visuais ocupam toda a tela ou a maior parte dela. Um objeto `Page` representa um `ViewController` no iOS e um `Page` na Plataforma Universal do Windows. No Android, cada página ocupa a tela como `Activity`, mas as páginas Xamarin.Forms *não* são objetos `Activity`.

[ ![](pages-images/pages-sml.png "Os tipos de página do xamarin. Forms")](pages-images/pages.png#lightbox "os tipos de página do xamarin. Forms")

## <a name="pages"></a>Páginas

Xamarin. Forms dá suporte aos seguintes tipos de página:

<a name="contentPage" />

### <a name="contentpage"></a>ContentPage

|     |     |
| --- | --- |
| [`ContentPage`](xref:Xamarin.Forms.ContentPage) é o tipo mais simples e mais comum da página. Defina a [ `Content` ](xref:Xamarin.Forms.ContentPage.Content) propriedade para um único [ `View` ](views.md) objeto, que é geralmente um [ `Layout` ](layouts.md) como [ `StackLayout` ](layouts.md#stackLayout), [ `Grid` ](layouts.md#grid), ou [ `ScrollView` ](layouts.md#scrollView).<br /><br />[Documentação da API](xref:Xamarin.Forms.ContentPage) | [![Exemplo de ContentPage](pages-images/ContentPage.png "exemplo ContentPage")](pages-images/ContentPage-Large.png#lightbox "exemplo ContentPage")<br />[O código c# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ContentPageDemoPage.cs) / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ContentPageDemoPage.xaml) |
|     |     |

### <a name="masterdetailpage"></a>MasterDetailPage

|     |     |
| --- | --- |
| Um [ `MasterDetailPage` ](xref:Xamarin.Forms.MasterDetailPage) gerencia dois painéis de informações. Defina as [ `Master` ](xref:Xamarin.Forms.MasterDetailPage.Master) propriedade a uma página em geral, mostrando uma lista ou menu. Defina as [ `Detail` ](xref:Xamarin.Forms.MasterDetailPage.Detail) propriedade para uma página mostrando um item selecionado da página mestra. O [ `IsPresented` ](xref:Xamarin.Forms.MasterDetailPage.IsPresented) propriedade controla se a página mestra ou de detalhes está visível.<br /><br />[Documentação da API](xref:Xamarin.Forms.MasterDetailPage) / [guia](~/xamarin-forms/app-fundamentals/navigation/master-detail-page.md) / [exemplo](https://developer.xamarin.com/samples/xamarin-forms/Navigation/MasterDetailPage/) | [![Exemplo de MasterDetailPage](pages-images/MasterDetailPage.png "exemplo MasterDetailPage")](pages-images/MasterDetailPage-Large.png#lightbox "MasterDetailPage exemplo")<br />[O código c# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/MasterDetailPageDemoPage.cs) / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/MasterDetailPageDemoPage.xaml) com [de lógica](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/MasterDetailPageDemoPage.xaml.cs) |
|     |     |

### <a name="navigationpage"></a>NavigationPage

|     |     |
| --- | --- |
| O [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage) gerencia a navegação entre outras páginas usando uma arquitetura baseada em pilha. Ao usar a navegação de página em seu aplicativo, uma instância da página inicial deve ser passada para o construtor de um `NavigationPage` objeto.<br /><br />[Documentação da API](xref:Xamarin.Forms.NavigationPage) / [guia](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md) / [exemplo 1](https://developer.xamarin.com/samples/xamarin-forms/Navigation/Hierarchical/), [2](https://developer.xamarin.com/samples/xamarin-forms/Navigation/PassingData/), e [3](https://developer.xamarin.com/samples/xamarin-forms/Navigation/LoginFlow/)  | [![Exemplo de NavigationPage](pages-images/NavigationPage.png "exemplo NavigationPage")](pages-images/NavigationPage-Large.png#lightbox "NavigationPage exemplo")<br />[O código c# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/NavigationPageDemoPage.cs) / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/NavigationPageDemoPage.xaml) com [código = atrás](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/NavigationPageDemoPage.xaml.cs) |
|     |     |

### <a name="tabbedpage"></a>TabbedPage

|     |     |
| --- | --- |
| [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) deriva de abstrata [ `MultiPage` ](xref:Xamarin.Forms.MultiPage`1) de classe e permite a navegação entre filho usando guias de páginas. Defina a [ `Children` ](xref:Xamarin.Forms.MultiPage`1.Children) propriedade a uma coleção de páginas ou conjunto a [ `ItemsSource` ](xref:Xamarin.Forms.MultiPage`1.ItemsSource) propriedade a uma coleção de objetos de dados e o [ `ItemTemplate` ](xref:Xamarin.Forms.MultiPage`1.ItemTemplate) propriedade para um [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate) que descreve como cada objeto deve ser visualmente representado.<br /><br />[Documentação da API](xref:Xamarin.Forms.TabbedPage) / [guia](~/xamarin-forms/app-fundamentals/navigation/tabbed-page.md) / [exemplo 1](https://developer.xamarin.com/samples/xamarin-forms/Navigation/TabbedPage/) e [2](https://developer.xamarin.com/samples/xamarin-forms/Navigation/TabbedPageWithNavigationPage) | [![Exemplo de TabbedPage](pages-images/TabbedPage.png "exemplo TabbedPage")](pages-images/TabbedPage-Large.png#lightbox "TabbedPage exemplo")<br />[O código c# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/TabbedPageDemoPage.cs) / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/TabbedPageDemoPage.xaml) |
|     |     |

### <a name="carouselpage"></a>CarouselPage

|     |     |
| --- | --- |
| [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) deriva de abstrata [ `MultiPage` ](xref:Xamarin.Forms.MultiPage`1) de classe e permite a navegação entre filho páginas por meio de passar o dedo dedo. Defina a [ `Children` ](xref:Xamarin.Forms.MultiPage`1.Children) propriedade a uma coleção de [ `ContentPage` ](#contentPage) objetos ou conjunto o [ `ItemsSource` ](xref:Xamarin.Forms.MultiPage`1.ItemsSource) propriedade a uma coleção de objetos de dados e o [ `ItemTemplate` ](xref:Xamarin.Forms.MultiPage`1.ItemTemplate) propriedade como um [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate) que descreve como cada objeto deve ser visualmente representado.<br /><br />[Documentação da API](xref:Xamarin.Forms.CarouselPage) / [guia](~/xamarin-forms/app-fundamentals/navigation/carousel-page.md) / [exemplo 1](https://developer.xamarin.com/samples/xamarin-forms/Navigation/CarouselPage/) e [2](https://developer.xamarin.com/samples/xamarin-forms/Navigation/CarouselPageTemplate/) | [![Exemplo de CarouselPage](pages-images/CarouselPage.png "exemplo CarouselPage")](pages-images/CarouselPage-Large.png#lightbox "CarouselPage exemplo")<br />[O código c# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/CarouselPageDemoPage.cs) / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/CarouselPageDemoPage.xaml) |
|     |     |

### <a name="templatedpage"></a>TemplatedPage

|     |     |
| --- | --- |
| [`TemplatedPage`](xref:Xamarin.Forms.TemplatedPage) Exibe o conteúdo de tela inteira com um modelo de controle, e é a classe base para [ `ContentPage` ](#contentPage).<br /><br />[Documentação da API](xref:Xamarin.Forms.TemplatedPage) / [guia](~/xamarin-forms/app-fundamentals/templates/control-templates/index.md) | [![Exemplo de TemplatedPage](pages-images/TemplatedPage.png "exemplo TemplatedPage")](pages-images/TemplatedPage.png "TemplatedPage exemplo") |
|     |     |

## <a name="related-links"></a>Links relacionados

- [Introdução ao Xamarin.Forms](~/xamarin-forms/get-started/introduction-to-xamarin-forms.md)
- [Exemplo de xamarin. Forms FormsGallery](https://developer.xamarin.com/samples/FormsGallery/)
- [Amostras do Xamarin.Forms](https://developer.xamarin.com/samples/xamarin-forms/all/)
- [Documentação da API de xamarin. Forms](https://docs.microsoft.com/dotnet/api/xamarin.forms?view=xamarin-forms)
