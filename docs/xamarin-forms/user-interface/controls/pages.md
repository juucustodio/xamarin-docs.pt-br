---
title: Páginas do Xamarin. Forms
description: As páginas Xamarin. Forms representam telas de aplicativos móveis de plataforma cruzada. Este artigo lista as páginas incluídas no Xamarin. Forms.
ms.prod: xamarin
ms.assetid: 9C8C710F-E312-420B-9324-A7A20CEDB7EC
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/12/2016
ms.openlocfilehash: 278256f75d94fe47510ae4d15f12a3ff3a6a2b19
ms.sourcegitcommit: 9bfedf07940dad7270db86767eb2cc4007f2a59f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/21/2019
ms.locfileid: "69976442"
---
# <a name="xamarinforms-pages"></a>Páginas do Xamarin. Forms

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery/)

_As páginas Xamarin. Forms representam telas de aplicativos móveis de plataforma cruzada._

Todos os tipos de página descritos abaixo derivam da classe [`Page`](xref:Xamarin.Forms.Page) Xamarin. Forms. Esses elementos visuais ocupam toda a tela ou a maioria delas. Um objeto `Page` representa uma `ViewController` no iOS e um `Page` no Plataforma Universal do Windows. No Android, cada página ocupa a tela como uma `Activity`, mas as páginas Xamarin. Forms *não* são `Activity` objetos.

[![](pages-images/pages-sml.png "Xamarin.Forms Page Types")](pages-images/pages.png#lightbox "Xamarin.Forms Page Types")

## <a name="pages"></a>Pages (Páginas)

O Xamarin. Forms dá suporte aos seguintes tipos de página:

<a name="contentPage" />

### <a name="contentpage"></a>ContentPage

|     |     |
| --- | --- |
| [`ContentPage`](xref:Xamarin.Forms.ContentPage) é o tipo mais simples e mais comum de página. Defina a [propriedade `Content`](xref:Xamarin.Forms.ContentPage.Content) como um único [objeto `View`](views.md) , que é geralmente um [`Layout`](layouts.md) como [`StackLayout`](layouts.md#stackLayout), [`Grid`](layouts.md#grid)ou [1](layouts.md#scrollView).<br /><br />[Documentação da API](xref:Xamarin.Forms.ContentPage) | [![Exemplo de ContentPage](pages-images/ContentPage.png "Exemplo de ContentPage")](pages-images/ContentPage-Large.png#lightbox "Exemplo de ContentPage")<br />código desta página  / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ContentPageDemoPage.xaml) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ContentPageDemoPage.cs) |
|     |     |

### <a name="masterdetailpage"></a>MasterDetailPage

|     |     |
| --- | --- |
| Um [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) gerencia dois painéis de informações. Defina a propriedade [`Master`](xref:Xamarin.Forms.MasterDetailPage.Master) como uma página geralmente mostrando uma lista ou um menu. Defina a propriedade [`Detail`](xref:Xamarin.Forms.MasterDetailPage.Detail) como uma página mostrando um item selecionado da página mestra. A propriedade [`IsPresented`](xref:Xamarin.Forms.MasterDetailPage.IsPresented) rege se a página mestra ou de detalhes está visível.<br /><br />[Documentação da API](xref:Xamarin.Forms.MasterDetailPage)  / [guia](~/xamarin-forms/app-fundamentals/navigation/master-detail-page.md)  / [exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-masterdetailpage) | [![Exemplo de MasterDetailPage](pages-images/MasterDetailPage.png "Exemplo de MasterDetailPage")](pages-images/MasterDetailPage-Large.png#lightbox "Exemplo de MasterDetailPage")<br />código para esta página  / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/MasterDetailPageDemoPage.xaml) com [code-behind](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/MasterDetailPageDemoPage.xaml.cs) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/MasterDetailPageDemoPage.cs) |
|     |     |

### <a name="navigationpage"></a>NavigationPage

|     |     |
| --- | --- |
| O [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) gerencia a navegação entre outras páginas usando uma arquitetura baseada em pilha. Ao usar a navegação de página em seu aplicativo, uma instância do home page deve ser passada para o construtor de um objeto `NavigationPage`.<br /><br />[Documentação da API](xref:Xamarin.Forms.NavigationPage)  / [guia](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md)  / [amostra 1](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-hierarchical), [2](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-passingdata)e [3](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-loginflow)  | [![Exemplo de NavigationPage](pages-images/NavigationPage.png "Exemplo de NavigationPage")](pages-images/NavigationPage-Large.png#lightbox "Exemplo de NavigationPage")<br />código para esta página  / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/NavigationPageDemoPage.xaml) com [código = por trás](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/NavigationPageDemoPage.xaml.cs) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/NavigationPageDemoPage.cs) |
|     |     |

### <a name="tabbedpage"></a>TabbedPage

|     |     |
| --- | --- |
| [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) deriva da classe abstrata [`MultiPage`](xref:Xamarin.Forms.MultiPage`1) e permite a navegação entre páginas filho usando guias. Defina a propriedade [`Children`](xref:Xamarin.Forms.MultiPage`1.Children) como uma coleção de páginas ou defina a propriedade [`ItemsSource`](xref:Xamarin.Forms.MultiPage`1.ItemsSource) como uma coleção de objetos de dados e a propriedade [`ItemTemplate`](xref:Xamarin.Forms.MultiPage`1.ItemTemplate) como um [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) descrevendo como cada objeto será representado visualmente.<br /><br />[Documentação da API](xref:Xamarin.Forms.TabbedPage)  / [guia](~/xamarin-forms/app-fundamentals/navigation/tabbed-page.md)  / [amostra 1](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-tabbedpage) e [2](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-tabbedpagewithnavigationpage) | [![Exemplo de TabbedPage](pages-images/TabbedPage.png "Exemplo de TabbedPage")](pages-images/TabbedPage-Large.png#lightbox "Exemplo de TabbedPage")<br />código desta página  / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/TabbedPageDemoPage.xaml) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/TabbedPageDemoPage.cs) |
|     |     |

### <a name="carouselpage"></a>CarouselPage

|     |     |
| --- | --- |
| [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) deriva da classe abstrata [`MultiPage`](xref:Xamarin.Forms.MultiPage`1) e permite a navegação entre páginas filhas por meio do dedo dedo. Defina a propriedade [`Children`](xref:Xamarin.Forms.MultiPage`1.Children) como uma coleção de objetos [`ContentPage`](#contentPage) ou defina a propriedade [`ItemsSource`](xref:Xamarin.Forms.MultiPage`1.ItemsSource) como uma coleção de objetos de dados e a propriedade [`ItemTemplate`](xref:Xamarin.Forms.MultiPage`1.ItemTemplate) como um [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) descrevendo como cada objeto deve ser visualmente representa.<br /><br />[Documentação da API](xref:Xamarin.Forms.CarouselPage)  / [guia](~/xamarin-forms/app-fundamentals/navigation/carousel-page.md)  / [amostra 1](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-carouselpage) e [2](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-carouselpagetemplate) | [![Exemplo de CarouselPage](pages-images/CarouselPage.png "Exemplo de CarouselPage")](pages-images/CarouselPage-Large.png#lightbox "Exemplo de CarouselPage")<br />código desta página  / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/CarouselPageDemoPage.xaml) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/CarouselPageDemoPage.cs) |
|     |     |

### <a name="templatedpage"></a>TemplatedPage

|     |     |
| --- | --- |
| [`TemplatedPage`](xref:Xamarin.Forms.TemplatedPage) exibe o conteúdo de tela inteira com um modelo de controle e é a classe base para [`ContentPage`](#contentPage).<br /><br />[Guia](~/xamarin-forms/app-fundamentals/templates/control-templates/index.md) de  /  de [documentação da API](xref:Xamarin.Forms.TemplatedPage) | [![Exemplo de TemplatedPage](pages-images/TemplatedPage.png "Exemplo de TemplatedPage")](pages-images/TemplatedPage.png "Exemplo de TemplatedPage") |
|     |     |

## <a name="related-links"></a>Links relacionados

- [Exemplo de FormsGallery do Xamarin. Forms](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery)
- [Amostras do Xamarin.Forms](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.Forms)
- [Documentação da API do Xamarin.Forms](https://docs.microsoft.com/dotnet/api/xamarin.forms?view=xamarin-forms)
