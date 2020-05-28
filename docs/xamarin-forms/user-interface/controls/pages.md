---
title: Xamarin.FormsPages
description: Xamarin.FormsAs páginas representam telas de aplicativos móveis de plataforma cruzada. Este artigo lista as páginas incluídas no Xamarin.Forms .
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: c576186dcfd598cb4fcfecd6d36edf04f73eee64
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/28/2020
ms.locfileid: "84132815"
---
# <a name="xamarinforms-pages"></a>Xamarin.FormsPages

[![Baixar exemplo ](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery/)

_As páginas Xamarin. Forms representam telas de aplicativos móveis de plataforma cruzada._

Todos os tipos de página descritos abaixo derivam da Xamarin.Forms [`Page`](xref:Xamarin.Forms.Page) classe. Esses elementos visuais ocupam toda a tela ou a maioria delas. Um `Page` objeto representa um `ViewController` no Ios e um `Page` no plataforma universal do Windows. No Android, cada página ocupa a tela como uma `Activity` , mas as Xamarin.Forms páginas *não* são `Activity` objetos.

[![](pages-images/pages-sml.png "Xamarin.Forms Page Types")](pages-images/pages.png#lightbox "Xamarin.Forms Page Types")

## <a name="pages"></a>Pages (Páginas)

Xamarin.Formso oferece suporte aos seguintes tipos de página:

<a name="contentPage" />

### <a name="contentpage"></a>ContentPage

|     |     |
| --- | --- |
| [`ContentPage`](xref:Xamarin.Forms.ContentPage)é o tipo mais simples e mais comum de página. Defina a [`Content`](xref:Xamarin.Forms.ContentPage.Content) propriedade como um único [`View`](views.md) objeto, que é geralmente um [`Layout`](layouts.md) [`StackLayout`](layouts.md#stackLayout) , como, [`Grid`](layouts.md#grid) ou [`ScrollView`](layouts.md#scrollView) .<br /><br />[Documentação da API](xref:Xamarin.Forms.ContentPage) | [![Exemplo de ContentPage](pages-images/ContentPage.png "Exemplo de ContentPage")](pages-images/ContentPage-Large.png#lightbox "Exemplo de ContentPage")<br />[Código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ContentPageDemoPage.cs)  /  [Página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ContentPageDemoPage.xaml) |
|     |     |

### <a name="masterdetailpage"></a>MasterDetailPage

|     |     |
| --- | --- |
| Um [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) gerencia dois painéis de informações. Defina a [`Master`](xref:Xamarin.Forms.MasterDetailPage.Master) propriedade como uma página geralmente mostrando uma lista ou um menu. Defina a [`Detail`](xref:Xamarin.Forms.MasterDetailPage.Detail) propriedade como uma página mostrando um item selecionado da página mestra. A [`IsPresented`](xref:Xamarin.Forms.MasterDetailPage.IsPresented) propriedade determina se a página mestra ou de detalhes está visível.<br /><br />[Documentação](xref:Xamarin.Forms.MasterDetailPage)  /  da API [Guia](~/xamarin-forms/app-fundamentals/navigation/master-detail-page.md)  /  [Exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-masterdetailpage) de | [![Exemplo de MasterDetailPage](pages-images/MasterDetailPage.png "Exemplo de MasterDetailPage")](pages-images/MasterDetailPage-Large.png#lightbox "Exemplo de MasterDetailPage")<br />[Código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/MasterDetailPageDemoPage.cs)  /  [Página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/MasterDetailPageDemoPage.xaml) com [code-behind](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/MasterDetailPageDemoPage.xaml.cs) |
|     |     |

### <a name="navigationpage"></a>NavigationPage

|     |     |
| --- | --- |
| O [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) gerencia a navegação entre outras páginas usando uma arquitetura baseada em pilha. Ao usar a navegação de página em seu aplicativo, uma instância do home page deve ser passada para o construtor de um `NavigationPage` objeto.<br /><br />[Documentação](xref:Xamarin.Forms.NavigationPage)  /  da API [Guia](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md)  /  [Exemplo 1](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-hierarchical), [2](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-passingdata)e [3](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-loginflow)  | [![Exemplo de NavigationPage](pages-images/NavigationPage.png "Exemplo de NavigationPage")](pages-images/NavigationPage-Large.png#lightbox "Exemplo de NavigationPage")<br />[Código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/NavigationPageDemoPage.cs)  /  [Página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/NavigationPageDemoPage.xaml) com [código = por trás](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/NavigationPageDemoPage.xaml.cs) |
|     |     |

### <a name="tabbedpage"></a>TabbedPage

|     |     |
| --- | --- |
| [`TabbedPage`](xref:Xamarin.Forms.TabbedPage)deriva da [`MultiPage`](xref:Xamarin.Forms.MultiPage`1) classe abstract e permite a navegação entre páginas filho usando guias. Defina a [`Children`](xref:Xamarin.Forms.MultiPage`1.Children) propriedade como uma coleção de páginas ou defina a [`ItemsSource`](xref:Xamarin.Forms.MultiPage`1.ItemsSource) propriedade como uma coleção de objetos de dados e a [`ItemTemplate`](xref:Xamarin.Forms.MultiPage`1.ItemTemplate) propriedade como um [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) descrevendo como cada objeto será representado visualmente.<br /><br />[Documentação](xref:Xamarin.Forms.TabbedPage)  /  da API [Guia](~/xamarin-forms/app-fundamentals/navigation/tabbed-page.md)  /  [Exemplo 1](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-tabbedpage) e [2](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-tabbedpagewithnavigationpage) | [![Exemplo de TabbedPage](pages-images/TabbedPage.png "Exemplo de TabbedPage")](pages-images/TabbedPage-Large.png#lightbox "Exemplo de TabbedPage")<br />[Código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/TabbedPageDemoPage.cs)  /  [Página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/TabbedPageDemoPage.xaml) |
|     |     |

### <a name="carouselpage"></a>CarouselPage

|     |     |
| --- | --- |
| [`CarouselPage`](xref:Xamarin.Forms.CarouselPage)deriva da [`MultiPage`](xref:Xamarin.Forms.MultiPage`1) classe abstract e permite a navegação entre páginas filhas por meio do dedo dedo. Defina a [`Children`](xref:Xamarin.Forms.MultiPage`1.Children) propriedade como uma coleção de [`ContentPage`](#contentPage) objetos ou defina a [`ItemsSource`](xref:Xamarin.Forms.MultiPage`1.ItemsSource) propriedade como uma coleção de objetos de dados e a [`ItemTemplate`](xref:Xamarin.Forms.MultiPage`1.ItemTemplate) propriedade como um [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) descrevendo como cada objeto será representado visualmente.<br /><br />[Documentação](xref:Xamarin.Forms.CarouselPage)  /  da API [Guia](~/xamarin-forms/app-fundamentals/navigation/carousel-page.md)  /  [Exemplo 1](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-carouselpage) e [2](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-carouselpagetemplate) | [![Exemplo de CarouselPage](pages-images/CarouselPage.png "Exemplo de CarouselPage")](pages-images/CarouselPage-Large.png#lightbox "Exemplo de CarouselPage")<br />[Código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/CarouselPageDemoPage.cs)  /  [Página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/CarouselPageDemoPage.xaml) |
|     |     |

### <a name="templatedpage"></a>TemplatedPage

|     |     |
| --- | --- |
| [`TemplatedPage`](xref:Xamarin.Forms.TemplatedPage)exibe o conteúdo de tela inteira com um modelo de controle e é a classe base para [`ContentPage`](#contentPage) .<br /><br />[Documentação](xref:Xamarin.Forms.TemplatedPage)  /  da API [Guia](~/xamarin-forms/app-fundamentals/templates/control-template.md) | [![Exemplo de TemplatedPage](pages-images/TemplatedPage.png "Exemplo de TemplatedPage")](pages-images/TemplatedPage.png "Exemplo de TemplatedPage") |
|     |     |

## <a name="related-links"></a>Links relacionados

- [Xamarin.FormsExemplo de FormsGallery](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery)
- [Xamarin.FormsAmostras](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.Forms)
- [Xamarin.FormsDocumentação da API](https://docs.microsoft.com/dotnet/api/xamarin.forms?view=xamarin-forms)
