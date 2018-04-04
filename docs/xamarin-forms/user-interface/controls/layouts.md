---
title: Layouts de xamarin. Forms
description: Xamarin. Forms Layouts são usados para compor a controles de interface do usuário em estruturas de visual.
ms.prod: xamarin
ms.assetid: F4180997-BA21-453A-9958-D1E2940DF050
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/12/2016
ms.openlocfilehash: 0a3164dc0da7534e6bffc011ad2fdde894d6c74a
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="xamarinforms-layouts"></a>Layouts de xamarin. Forms

_Xamarin. Forms Layouts são usados para compor a controles de interface do usuário em estruturas de visual._

O [ `Layout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Layout) e [ `Layout<T>` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Layout%3CT%3E/) classes no xamarin. Forms são especializados subtipos de exibições que atuam como contêineres para outros layouts e modos de exibição. O `Layout` própria classe derivada de [ `View` ](views.md). Um `Layout` derivado geralmente contém a lógica para definir a posição e tamanho dos elementos filho em aplicativos xamarin. Forms.

 [ ![](layouts-images/layouts-sml.png "Tipos de Layout xamarin. Forms")](layouts-images/layouts.png#lightbox "tipos de Layout xamarin. Forms")

As classes que derivam de `Layout` podem ser divididos em duas categorias:

## <a name="layouts-with-single-content"></a>Layouts com conteúdo simples

Essas classes derivam [ `Layout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Layout/), que define [ `Padding` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Layout.Padding/) e [ `IsClippedToBounds` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Layout.IsClippedToBounds/) propriedades.

<a name="contentView" />

### <a name="contentview"></a>ContentView

|     |     |
| --- | --- |
| [`ContentView`](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentView/) contém um único filho que é definido com o [ `Content` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ContentView.Content/) propriedade. O `Content` propriedade pode ser definida como qualquer `View` derivado, incluindo outros `Layout` derivados. `ContentView` é usada principalmente como um elemento estrutural e serve como uma classe base para [ `Frame` ](#frame).<br /><br />[Documentação da API](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentView/) | [![Exemplo de ContentView](layouts-images/ContentView.png "ContentView exemplo")](layouts-images/ContentView-Large.png#lightbox "ContentView exemplo")<br />[O código c# para essa página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ContentViewDemoPage.cs) / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ContentViewDemoPage.xaml) |
|     |     |

<a named="frame" />

### <a name="frame"></a>Quadro

|     |     |
| --- | --- |
| O [ `Frame` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Frame/) classe derivada de [ `ContentView` ](#contentView) e exibe um quadro retangular em torno de seu filho. `Frame` tem um padrão [ `Padding` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Layout.Padding/) valor 20 e também define [ `OutlineColor` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Frame.OutlineColor/), [ `CornerRadius` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Frame.CornerRadius/), e [ `HasShadow` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Frame.HasShadow/)propriedades.<br /><br />[Documentação da API](https://developer.xamarin.com/api/type/Xamarin.Forms.Frame/) | [![Exemplo de quadro](layouts-images/Frame.png "quadro exemplo")](layouts-images/Frame-Large.png#lightbox "exemplo do quadro")<br />[O código c# para essa página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/FrameDemoPage.cs) / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/FrameDemoPage.xaml) |
|     |     |

<a name="scrollView" />

### <a name="scrollview"></a>ScrollView

|     |     |
| --- | --- |
| [`ScrollView`](https://developer.xamarin.com/api/type/Xamarin.Forms.ScrollView/) é capaz de rolagem seu conteúdo. Definir o [ `Content` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ScrollView.Content/) propriedade a uma exibição ou layout muito grande para caber na tela. (O conteúdo de um `ScrollView` é frequentemente um [ `StackLayout` ](#stackLayout).) Definir o [ `Orientation` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ScrollView.Orientation/) propriedade para indicar se a rolagem deve ser vertical, horizontal, ou ambos.<br /><br />[Documentação da API](https://developer.xamarin.com/api/type/Xamarin.Forms.ScrollView/) / [guia](~/xamarin-forms/user-interface/layouts/scroll-view.md) / [exemplo](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Layout/) | [![Exemplo de ScrollView](layouts-images/ScrollView.png "ScrollView exemplo")](layouts-images/ScrollView-Large.png#lightbox "ScrollView exemplo")<br />[O código c# para essa página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ScrollViewDemoPage.cs) / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ScrollViewDemoPage.xaml) |
|     |     |

### <a name="templatedview"></a>TemplatedView

|     |     |
| --- | --- |
| [`TemplatedView`](https://developer.xamarin.com/api/type/Xamarin.Forms.TemplatedView/) Exibe o conteúdo com um modelo de controle, e é a classe base para [ `ContentView` ](#contentView).<br /><br />[Documentação da API](https://developer.xamarin.com/api/type/Xamarin.Forms.TemplatedView/) / [guia](~/xamarin-forms/app-fundamentals/templates/control-templates/index.md) | [![Exemplo de TemplatedView](layouts-images/TemplatedView.png "TemplatedView exemplo")](layouts-images/TemplatedView.png#lightbox "TemplatedView exemplo") |
|     |     |

### <a name="contentpresenter"></a>ContentPresenter

|     |     |
| --- | --- |
| [`ContentPresenter`](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPresenter/) é um Gerenciador de layout para exibições de modelo, usado em uma [ `ControlTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ControlTemplate/) para marcar onde o conteúdo deve ser apresentada aparece.<br /><br />[Documentação da API](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPresenter/) / [guia](~/xamarin-forms/app-fundamentals/templates/control-templates/index.md) | [![Exemplo de ContentPresenter](layouts-images/ContentPresenter.png "ContentPresenter exemplo")](layouts-images/ContentPresenter.png#lightbox "ContentPresenter exemplo") |
|     |     |

## <a name="layouts-with-multiple-children"></a>Layouts com vários filhos

Essas classes derivam [ `Layout<View>` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Layout%3CT%3E/).

<a name="stackLayout" />

### <a name="stacklayout"></a>StackLayout

|     |     |
| --- | --- |
| [`StackLayout`](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/) Posiciona elementos filho em uma pilha horizontal ou verticalmente com base no [ `Orientation` ](https://developer.xamarin.com/api/property/Xamarin.Forms.StackLayout.Orientation/) propriedade. O [ `Spacing` ](https://developer.xamarin.com/api/property/Xamarin.Forms.StackLayout.Spacing/) propriedade controla o espaçamento entre os filhos e tem um valor padrão de 6.<br /><br />[Documentação da API](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/) / [guia](~/xamarin-forms/user-interface/layouts/stack-layout.md) / [exemplo](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Layout/)| [![Exemplo de StackLayout](layouts-images/StackLayout.png "StackLayout exemplo")](layouts-images/StackLayout-Large.png#lightbox "StackLayout exemplo")<br />[O código c# para essa página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/StackLayoutDemoPage.cs) / [página XAML]((https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/StackLayoutDemoPage.xaml)) |
|     |     |

<a name="grid" />

### <a name="grid"></a>Grade

|     |     |
| --- | --- |
| [`Grid`](https://developer.xamarin.com/api/type/Xamarin.Forms.Grid/) posiciona seus elementos filho em uma grade de linhas e colunas. Posição do filho é indicada usando o [propriedades anexadas](~/xamarin-forms/xaml/attached-properties.md) [ `Row` ](https://developer.xamarin.com/api/field/Xamarin.Forms.Grid.RowProperty/), [ `Column` ](https://developer.xamarin.com/api/field/Xamarin.Forms.Grid.ColumnProperty/), [ `RowSpan` ](https://developer.xamarin.com/api/field/Xamarin.Forms.Grid.RowSpanProperty/), e [ `ColumnSpan` ](https://developer.xamarin.com/api/field/Xamarin.Forms.Grid.ColumnSpanProperty/).<br /><br />[Documentação da API](https://developer.xamarin.com/api/type/Xamarin.Forms.Grid/) / [guia](~/xamarin-forms/user-interface/layouts/grid.md) / [exemplo](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Layout/) | [![Exemplo de grade](layouts-images/Grid.png "exemplo grade")](layouts-images/Grid-Large.png#lightbox "exemplo de grade")<br />[O código c# para essa página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/GridDemoPage.cs) / [página XAML]((https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/GridDemoPage.xaml)) |
|     |     |

### <a name="absolutelayout"></a>AbsoluteLayout

|     |     |
| --- | --- |
| [`AbsoluteLayout`](https://developer.xamarin.com/api/type/Xamarin.Forms.AbsoluteLayout/) Posiciona elementos filho em locais específicos em relação ao seu pai. Posição do filho é indicada usando o [propriedades anexadas](~/xamarin-forms/xaml/attached-properties.md) [ `LayoutBounds` ](https://developer.xamarin.com/api/field/Xamarin.Forms.AbsoluteLayout.LayoutBoundsProperty/) e [ `LayoutFlags` ](https://developer.xamarin.com/api/field/Xamarin.Forms.AbsoluteLayout.LayoutFlagsProperty/). Um `AbsoluteLayout` é útil para as posições de modos de exibição de animação.<br /><br />[Documentação da API](https://developer.xamarin.com/api/type/Xamarin.Forms.AbsoluteLayout/) / [guia](~/xamarin-forms/user-interface/layouts/absolute-layout.md) / [exemplo](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Layout/) | [![Exemplo de AbsoluteLayout](layouts-images/AbsoluteLayout.png "AbsoluteLayout exemplo")](layouts-images/AbsoluteLayout-Large.png#lightbox "AbsoluteLayout exemplo")<br />[O código c# para essa página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/AbsoluteLayoutdDemoPage.cs) / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/AbsoluteLayout.xaml) com [por trás do código](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/AbsoluteLayout.xaml.cs) |
|     |     |

### <a name="relativelayout"></a>RelativeLayout

|     |     |
| --- | --- |
| [`RelativeLayout`](https://developer.xamarin.com/api/type/Xamarin.Forms.RelativeLayout/) Posiciona elementos filho relativo a `RelativeLayout` em si ou seus irmãos. Posição do filho é indicada usando o [propriedades anexadas](~/xamarin-forms/xaml/attached-properties.md) que são definidas para objetos do tipo [ `Constraint` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Constraint/) e [ `BoundsConstraint` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Constraint/).<br /><br />[Documentação da API](https://developer.xamarin.com/api/type/Xamarin.Forms.RelativeLayout/)/ [guia](~/xamarin-forms/user-interface/layouts/relative-layout.md) / [exemplo](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Layout/) | [![Exemplo de RelativeLayout](layouts-images/RelativeLayout.png "RelativeLayout exemplo")](layouts-images/RelativeLayout-Large.png#lightbox "RelativeLayout exemplo")<br />[O código c# para essa página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/RelativeLayoutDemoPage.cs) / [página XAML]((https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/RelativeLayoutDemoPage.xaml)) |
|     |     |

## <a name="related-links"></a>Links relacionados

- [Introdução ao Xamarin.Forms](~/xamarin-forms/get-started/introduction-to-xamarin-forms.md)
- [Exemplo de FormsGallery xamarin. Forms](https://developer.xamarin.com/samples/FormsGallery/)
- [Amostras do Xamarin.Forms](https://developer.xamarin.com/samples/xamarin-forms/all/)
- [Documentação da API de xamarin. Forms](https://developer.xamarin.com/api/root/Xamarin.Forms/)
