---
title: Páginas xamarin. Forms
description: Páginas xamarin. Forms representam as telas de aplicativos móveis de plataforma cruzada.
ms.prod: xamarin
ms.assetid: 9C8C710F-E312-420B-9324-A7A20CEDB7EC
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/12/2016
ms.openlocfilehash: bc1345bfaaf02464e2cf1ea0b3aceb28eb91e1d4
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="xamarinforms-pages"></a>Páginas xamarin. Forms

_Páginas xamarin. Forms representam as telas de aplicativos móveis de plataforma cruzada._

Todos os tipos de página que são descritos abaixo derivam da classe [ `Page` ] do Xamarin.Forms (https://developer.xamarin.com/api/type/Xamarin.Forms.Page/). Esses elementos visuais ocupam toda a tela ou a maior parte dela. Um objeto `Page` representa um `ViewController` no iOS e um `Page` na Plataforma Universal do Windows. No Android, cada página ocupa a tela como `Activity`, mas as páginas Xamarin.Forms *não* são objetos `Activity`.

[ ![](pages-images/pages-sml.png "Os tipos de página xamarin. Forms")](pages-images/pages.png#lightbox "os tipos de página xamarin. Forms")

## <a name="pages"></a>Páginas

Xamarin. Forms suporta os seguintes tipos de página:

<a name="contentPage" />

### <a name="contentpage"></a>ContentPage

|     |     | 
| --- | --- | 
| [`ContentPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/) é o tipo mais simples e mais comuns da página. Definir o [ `Content` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ContentPage.Content/) propriedade para um único [ `View` ](views.md) objeto, que é geralmente um [ `Layout` ](layouts.md) como [ `StackLayout` ](layouts.md#stackLayout), [ `Grid` ](layouts.md#grid), ou [ `ScrollView` ](layouts.md#scrollView).<br /><br />[Documentação da API](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/) | [![Exemplo de ContentPage](pages-images/ContentPage.png "ContentPage exemplo")](pages-images/ContentPage-Large.png#lightbox "ContentPage exemplo")<br />[O código c# para essa página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ContentPageDemoPage.cs) / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ContentPageDemoPage.xaml) |
|     |     |

### <a name="masterdetailpage"></a>MasterDetailPage

|     |     | 
| --- | --- | 
| Um [ `MasterDetailPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MasterDetailPage/) gerencia dois painéis de informações. Definir o [ `Master` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MasterDetailPage.Master/) propriedade a uma página em geral, mostrando uma lista ou menu. Definir o [ `Detail` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MasterDetailPage.Detail/) propriedade a uma página mostrando um item selecionado da página mestra. O [ `IsPresented` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MasterDetailPage.IsPresented/) propriedade controla se a página mestra ou de detalhes está visível.<br /><br />[Documentação da API](https://developer.xamarin.com/api/type/Xamarin.Forms.MasterDetailPage/) / [guia](~/xamarin-forms/app-fundamentals/navigation/master-detail-page.md) / [exemplo](https://developer.xamarin.com/samples/xamarin-forms/Navigation/MasterDetailPage/) | [![Exemplo de MasterDetailPage](pages-images/MasterDetailPage.png "MasterDetailPage exemplo")](pages-images/MasterDetailPage-Large.png#lightbox "MasterDetailPage exemplo")<br />[O código c# para essa página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/MasterDetailPageDemoPage.cs) / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/MasterDetailPageDemoPage.xaml) com [por trás do código](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/MasterDetailPageDemoPage.xaml.cs) |
|     |     |

### <a name="navigationpage"></a>NavigationPage

|     |     | 
| --- | --- | 
| O [ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/) gerencia a navegação entre outras páginas usando uma arquitetura de pilha. Ao usar a navegação de página em seu aplicativo, uma instância da página inicial deve ser passada para o construtor de uma `NavigationPage` objeto.<br /><br />[Documentação da API](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/) / [guia](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md) / [exemplo 1](https://developer.xamarin.com/samples/xamarin-forms/Navigation/Hierarchical/), [2](https://developer.xamarin.com/samples/xamarin-forms/Navigation/PassingData/), e [3](https://developer.xamarin.com/samples/xamarin-forms/Navigation/LoginFlow/)  | [![Exemplo de NavigationPage](pages-images/NavigationPage.png "NavigationPage exemplo")](pages-images/NavigationPage-Large.png#lightbox "NavigationPage exemplo")<br />[O código c# para essa página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/NavigationPageDemoPage.cs) / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/NavigationPageDemoPage.xaml) com [código = atrás](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/NavigationPageDemoPage.xaml.cs) |
|     |     |

### <a name="tabbedpage"></a>TabbedPage

|     |     | 
| --- | --- | 
| [`TabbedPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/) deriva abstrata [ `MultiPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MultiPage%3CT%3E/) classe e permite a navegação entre filho usando guias de páginas. Definir o [ `Children` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MultiPage%3CT%3E.Children/) propriedade a uma coleção de páginas ou de conjunto a [ `ItemsSource` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MultiPage%3CT%3E.ItemsSource/) propriedade a uma coleção de objetos de dados e o [ `ItemTemplate` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MultiPage%3CT%3E.ItemTemplate/) propriedade para um [ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/) que descrevem como cada objeto a ser visualmente representado.<br /><br />[Documentação da API](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/) / [guia](~/xamarin-forms/app-fundamentals/navigation/tabbed-page.md) / [exemplo 1](https://developer.xamarin.com/samples/xamarin-forms/Navigation/TabbedPage/) e [2](https://developer.xamarin.com/samples/xamarin-forms/Navigation/TabbedPageWithNavigationPage) | [![Exemplo de TabbedPage](pages-images/TabbedPage.png "TabbedPage exemplo")](pages-images/TabbedPage-Large.png#lightbox "TabbedPage exemplo")<br />[O código c# para essa página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/TabbedPageDemoPage.cs) / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/TabbedPageDemoPage.xaml) |
|     |     |

### <a name="carouselpage"></a>CarouselPage

|     |     | 
| --- | --- | 
| [`CarouselPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.CarouselPage/) deriva abstrata [ `MultiPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MultiPage%3CT%3E/) classe e permite a navegação entre filho páginas por meio dedo passar o dedo. Definir o [ `Children` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MultiPage%3CT%3E.Children/) propriedade a uma coleção de [ `ContentPage` ](#contentPage) objetos ou conjunto o [ `ItemsSource` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MultiPage%3CT%3E.ItemsSource/) propriedade a uma coleção de objetos de dados e o [ `ItemTemplate` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MultiPage%3CT%3E.ItemTemplate/) propriedade para um [ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/) que descrevem como cada objeto a ser visualmente representado.<br /><br />[Documentação da API](https://developer.xamarin.com/api/type/Xamarin.Forms.CarouselPage/) / [guia](~/xamarin-forms/app-fundamentals/navigation/carousel-page.md) / [exemplo 1](https://developer.xamarin.com/samples/xamarin-forms/Navigation/CarouselPage/) e [2](https://developer.xamarin.com/samples/xamarin-forms/Navigation/CarouselPageTemplate/) | [![Exemplo de CarouselPage](pages-images/CarouselPage.png "CarouselPage exemplo")](pages-images/CarouselPage-Large.png#lightbox "CarouselPage exemplo")<br />[O código c# para essa página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/CarouselPageDemoPage.cs) / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/CarouselPageDemoPage.xaml) |
|     |     |

### <a name="templatedpage"></a>TemplatedPage

|     |     | 
| --- | --- | 
| [`TemplatedPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.TemplatedPage/) Exibe o conteúdo de tela inteira com um modelo de controle, e é a classe base para [ `ContentPage` ](#contentPage).<br /><br />[Documentação da API](https://developer.xamarin.com/api/type/Xamarin.Forms.TemplatedPage/) / [guia](~/xamarin-forms/app-fundamentals/templates/control-templates/index.md) | [![Exemplo de TemplatedPage](pages-images/TemplatedPage.png "TemplatedPage exemplo")](pages-images/TemplatedPage.png "TemplatedPage exemplo") |
|     |     |

## <a name="related-links"></a>Links relacionados

- [Introdução ao Xamarin.Forms](~/xamarin-forms/get-started/introduction-to-xamarin-forms.md)
- [Exemplo de FormsGallery xamarin. Forms](https://developer.xamarin.com/samples/FormsGallery/)
- [Amostras do Xamarin.Forms](https://developer.xamarin.com/samples/xamarin-forms/all/)
- [Documentação da API de xamarin. Forms](https://developer.xamarin.com/api/root/Xamarin.Forms/)
