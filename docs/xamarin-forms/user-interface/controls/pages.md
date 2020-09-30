---
title: Xamarin.Forms Pages
description: Xamarin.Forms As páginas representam telas de aplicativos móveis de plataforma cruzada. Este artigo lista as páginas incluídas no Xamarin.Forms .
ms.prod: xamarin
ms.assetid: 9C8C710F-E312-420B-9324-A7A20CEDB7EC
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/12/2016
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 9164275b75c021a79b6ea5ea64194997ad804ece
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91563205"
---
# <a name="no-locxamarinforms-pages"></a>Xamarin.Forms Pages

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery/)

_Xamarin.Forms As páginas representam telas de aplicativos móveis de plataforma cruzada._

Todos os tipos de página descritos abaixo derivam da Xamarin.Forms [`Page`](xref:Xamarin.Forms.Page) classe. Esses elementos visuais ocupam toda a tela ou a maioria delas. Um `Page` objeto representa um `ViewController` no Ios e um `Page` no plataforma universal do Windows. No Android, cada página ocupa a tela como uma `Activity` , mas as Xamarin.Forms páginas *não* são `Activity` objetos.

[![::: no-Loc (Xamarin. Forms)::: tipos de página](pages-images/pages-sml.png)](pages-images/pages.png#lightbox "::: no-Loc (Xamarin. Forms)::: tipos de página")

## <a name="pages"></a>Pages (Páginas)

Xamarin.Forms o oferece suporte aos seguintes tipos de página:

| Tipo | Descrição | Aparência |
| --- | --- | --- |
| `ContentPage` | [`ContentPage`](xref:Xamarin.Forms.ContentPage) é o tipo mais simples e mais comum de página. Defina a [`Content`](xref:Xamarin.Forms.ContentPage.Content) propriedade como um único [`View`](views.md) objeto, que é geralmente um [`Layout`](layouts.md) [`StackLayout`](xref:Xamarin.Forms.StackLayout) , como, [`Grid`](xref:Xamarin.Forms.Grid) ou [`ScrollView`](xref:Xamarin.Forms.ScrollView) .<br /><br />[Documentação da API](xref:Xamarin.Forms.ContentPage) | [![Exemplo de ContentPage](pages-images/ContentPage.png "Exemplo de ContentPage")](pages-images/ContentPage-Large.png#lightbox "Exemplo de ContentPage")<br />[Código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ContentPageDemoPage.cs)  /  [Página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ContentPageDemoPage.xaml) |
| `MasterDetailPage` | Um [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) gerencia dois painéis de informações. Defina a [`Master`](xref:Xamarin.Forms.MasterDetailPage.Master) propriedade como uma página geralmente mostrando uma lista ou um menu. Defina a [`Detail`](xref:Xamarin.Forms.MasterDetailPage.Detail) propriedade como uma página mostrando um item selecionado da página mestra. A [`IsPresented`](xref:Xamarin.Forms.MasterDetailPage.IsPresented) propriedade determina se a página mestra ou de detalhes está visível.<br /><br />[Documentação](xref:Xamarin.Forms.MasterDetailPage)  /  da API [Guia](~/xamarin-forms/app-fundamentals/navigation/master-detail-page.md)  /  [Exemplo](/samples/xamarin/xamarin-forms-samples/navigation-masterdetailpage) de | [![Exemplo de MasterDetailPage](pages-images/MasterDetailPage.png "Exemplo de MasterDetailPage")](pages-images/MasterDetailPage-Large.png#lightbox "Exemplo de MasterDetailPage")<br />[Código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/MasterDetailPageDemoPage.cs)  /  [Página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/MasterDetailPageDemoPage.xaml) com [code-behind](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/MasterDetailPageDemoPage.xaml.cs) |
| `NavigationPage` | O [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) gerencia a navegação entre outras páginas usando uma arquitetura baseada em pilha. Ao usar a navegação de página em seu aplicativo, uma instância do home page deve ser passada para o construtor de um `NavigationPage` objeto.<br /><br />[Documentação](xref:Xamarin.Forms.NavigationPage)  /  da API [Guia](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md)  /  [Exemplo 1](/samples/xamarin/xamarin-forms-samples/navigation-hierarchical), [2](/samples/xamarin/xamarin-forms-samples/navigation-passingdata)e [3](/samples/xamarin/xamarin-forms-samples/navigation-loginflow)  | [![Exemplo de NavigationPage](pages-images/NavigationPage.png "Exemplo de NavigationPage")](pages-images/NavigationPage-Large.png#lightbox "Exemplo de NavigationPage")<br />[Código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/NavigationPageDemoPage.cs)  /  [Página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/NavigationPageDemoPage.xaml) com [código = por trás](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/NavigationPageDemoPage.xaml.cs) |
| `TabbedPage` | [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) deriva da [`MultiPage`](xref:Xamarin.Forms.MultiPage`1) classe abstract e permite a navegação entre páginas filho usando guias. Defina a [`Children`](xref:Xamarin.Forms.MultiPage`1.Children) propriedade como uma coleção de páginas ou defina a [`ItemsSource`](xref:Xamarin.Forms.MultiPage`1.ItemsSource) propriedade como uma coleção de objetos de dados e a [`ItemTemplate`](xref:Xamarin.Forms.MultiPage`1.ItemTemplate) propriedade como um [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) descrevendo como cada objeto será representado visualmente.<br /><br />[Documentação](xref:Xamarin.Forms.TabbedPage)  /  da API [Guia](~/xamarin-forms/app-fundamentals/navigation/tabbed-page.md)  /  [Exemplo 1](/samples/xamarin/xamarin-forms-samples/navigation-tabbedpage) e [2](/samples/xamarin/xamarin-forms-samples/navigation-tabbedpagewithnavigationpage) | [![Exemplo de TabbedPage](pages-images/TabbedPage.png "Exemplo de TabbedPage")](pages-images/TabbedPage-Large.png#lightbox "Exemplo de TabbedPage")<br />[Código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/TabbedPageDemoPage.cs)  /  [Página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/TabbedPageDemoPage.xaml) |
| `CarouselPage` | [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) deriva da [`MultiPage`](xref:Xamarin.Forms.MultiPage`1) classe abstract e permite a navegação entre páginas filhas por meio do dedo dedo. Defina a [`Children`](xref:Xamarin.Forms.MultiPage`1.Children) propriedade como uma coleção de [`ContentPage`](xref:Xamarin.Forms.ContentPage) objetos ou defina a [`ItemsSource`](xref:Xamarin.Forms.MultiPage`1.ItemsSource) propriedade como uma coleção de objetos de dados e a [`ItemTemplate`](xref:Xamarin.Forms.MultiPage`1.ItemTemplate) propriedade como um [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) descrevendo como cada objeto será representado visualmente.<br /><br />[Documentação](xref:Xamarin.Forms.CarouselPage)  /  da API [Guia](~/xamarin-forms/app-fundamentals/navigation/carousel-page.md)  /  [Exemplo 1](/samples/xamarin/xamarin-forms-samples/navigation-carouselpage) e [2](/samples/xamarin/xamarin-forms-samples/navigation-carouselpagetemplate) | [![Exemplo de CarouselPage](pages-images/CarouselPage.png "Exemplo de CarouselPage")](pages-images/CarouselPage-Large.png#lightbox "Exemplo de CarouselPage")<br />[Código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/CarouselPageDemoPage.cs)  /  [Página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/CarouselPageDemoPage.xaml) |
| `TemplatedPage` | [`TemplatedPage`](xref:Xamarin.Forms.TemplatedPage) exibe o conteúdo de tela inteira com um modelo de controle e é a classe base para [`ContentPage`](xref:Xamarin.Forms.ContentPage) .<br /><br />[Documentação](xref:Xamarin.Forms.TemplatedPage)  /  da API [Guia](~/xamarin-forms/app-fundamentals/templates/control-template.md) | [![Exemplo de TemplatedPage](pages-images/TemplatedPage.png "Exemplo de TemplatedPage")](pages-images/TemplatedPage.png "Exemplo de TemplatedPage") |
|     |     |     |

## <a name="related-links"></a>Links relacionados

- [Xamarin.Forms Exemplo de FormsGallery](/samples/xamarin/xamarin-forms-samples/formsgallery)
- [Xamarin.Forms Amostras](/samples/browse/?products=xamarin&term=Xamarin.Forms)
- [Xamarin.Forms Documentação da API](/dotnet/api/xamarin.forms?view=xamarin-forms)