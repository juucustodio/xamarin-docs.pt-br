---
title: Resumo do Capítulo 27. Renderizadores personalizados
description: 'Criação de aplicativos móveis com xamarin. Forms: Resumo do Capítulo 27. Renderizadores personalizados'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 49961953-9336-4FD4-A42F-6D9B05FF52E7
author: davidbritch
ms.author: dabritch
ms.date: 07/18/2018
ms.openlocfilehash: 1a992c192cb4d7fc24490257b67c1b851abcd949
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/07/2018
ms.locfileid: "53058338"
---
# <a name="summary-of-chapter-27-custom-renderers"></a>Resumo do Capítulo 27. Renderizadores personalizados

[![Baixar exemplo](~/media/shared/download.png) baixar o exemplo](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter27)

> [!NOTE] 
> Observações sobre essa página indicam áreas onde o xamarin. Forms foi bifurcado do material apresentado no livro.

Um elemento de xamarin. Forms, como `Button` é renderizado com um botão específico da plataforma encapsulado em uma classe chamada `ButtonRenderer`.  Aqui está o [versão iOS de `ButtonRenderer` ](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Platform.iOS/Renderers/ButtonRenderer.cs), o [versão Android `ButtonRenderer` ](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Platform.Android/Renderers/ButtonRenderer.cs)e o [versão UWP do `ButtonRenderer` ](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Platform.UAP/ButtonRenderer.cs).

Este capítulo discute como você pode escrever seus próprios renderizadores para criar exibições personalizadas que são mapeadas para objetos específicos da plataforma.

## <a name="the-complete-class-hierarchy"></a>A hierarquia de classe completo

Há quatro assemblies que contêm o código específico da plataforma xamarin. Forms.
Você pode exibir o código-fonte no GitHub usando estes links:

- [**Xamarin.Forms.Platform** ](https://github.com/xamarin/Xamarin.Forms/tree/master/Xamarin.Forms.Platform) (muito pequeno)
- [**Xamarin.Forms.Platform.iOS**](https://github.com/xamarin/Xamarin.Forms/tree/master/Xamarin.Forms.Platform.iOS)
- [**Xamarin.Forms.Platform.Android**](https://github.com/xamarin/Xamarin.Forms/tree/master/Xamarin.Forms.Platform.Android)
- [**Xamarin.Forms.Platform.UAP**](https://github.com/xamarin/Xamarin.Forms/tree/master/Xamarin.Forms.Platform.UAP)

> [!NOTE]
> O `WinRT` assemblies mencionados no livro não fazem mais parte dessa solução. 

O [ **PlatformClassHierarchy** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter27/PlatformClassHierarchy) exemplo exibe uma hierarquia de classe para os assemblies que são válidos para a plataforma em execução.

Você observará uma classe importante chamada `ViewRenderer`. Esta é a classe que você deriva ao criar um renderizador específica da plataforma. Existe nas três versões diferentes, pois ele está vinculado ao sistema de modo de exibição da plataforma de destino:

O iOS [ `ViewRenderer<TView, TNativeView>` ](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Platform.iOS/ViewRenderer.cs#L25) tem argumentos genéricos:

- `TView` restrito a [`Xamarin.Forms.View`](xref:Xamarin.Forms.View)
- `TNativeView` restrito a [`UIKit.UIView`](https://developer.xamarin.com/api/type/UIKit.UIView/)

O Android [ `ViewRenderer<TView, TNativeView>` ](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Platform.Android/ViewRenderer.cs#L17) tem argumentos genéricos:

- `TView` restrito a [`Xamarin.Forms.View`](xref:Xamarin.Forms.View)
- `TNativeView` restrito a [`Android.Views.View`](https://developer.xamarin.com/api/type/Android.Views.View/)

A UWP [ `ViewRenderer<TElement, TNativeElement>` ](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Platform.UAP/ViewRenderer.cs#L6) tem genéricos argumentos nomeados de forma diferente:

- `TElement` restrito a [`Xamarin.Forms.View`](xref:Xamarin.Forms.View)
- `TNativeElement` restrito a [`Windows.UI.Xaml.FrameworkElement`](/uwp/api/Windows.UI.Xaml.FrameworkElement)

Ao escrever um renderizador, você será derivando uma classe de `View`e, em seguida, gravar vários `ViewRenderer` classes, uma para cada plataforma com suporte. Cada implementação específica da plataforma fará referência a uma classe nativa que deriva do tipo que você especificar como o `TNativeView` ou `TNativeElement` parâmetro.

## <a name="hello-custom-renderers"></a>Olá, renderizadores personalizados!

O [ **HelloRenderers** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter27/HelloRenderers) programa faz referência a uma exibição personalizada chamada `HelloView` no seu [ `App` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter27/HelloRenderers/HelloRenderers/HelloRenderers/App.cs) classe.

O [ `HelloView` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter27/HelloRenderers/HelloRenderers/HelloRenderers/HelloView.cs) classe está incluída no **HelloRenderers** do projeto e simplesmente deriva `View`.

O [ `HelloViewRenderer` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter27/HelloRenderers/HelloRenderers/HelloRenderers.iOS/HelloViewRenderer.cs) classe o **HelloRenderers.iOS** projeto deriva `ViewRenderer<HelloView, UILabel>`. No `OnElementChanged` substituição, ele cria um iOS nativo `UILabel` e chama `SetNativeControl`.

O [ `HelloViewRenderer` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter27/HelloRenderers/HelloRenderers/HelloRenderers.Droid/HelloViewRenderer.cs) classe o **HelloRenderers.Droid** projeto deriva `ViewRenderer<HelloView, TextView>`. No `OnElementChanged` substituição, ele cria um Android `TextView` e chama `SetNativeControl`.

O [ `HelloViewRenderer` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter27/HelloRenderers/HelloRenderers/HelloRenderers.UWP/HelloViewRenderer.cs) classe o **HelloRenderers.UWP** e outros projetos Windows deriva `ViewRenderer<HelloView, TextBlock>`. No `OnElementChanged` substituição, ele cria um Windows `TextBlock` e chama `SetNativeControl`.

Todos os as `ViewRenderer` derivativos contêm um `ExportRenderer` atributo no nível do assembly que associa a `HelloView` classe com determinada `HelloViewRenderer` classe. Isso é como o xamarin. Forms localiza renderizadores nos projetos de plataforma individual:

[![Tripla captura de tela da exibição Hello](images/ch27fg02-small.png "renderizadores personalizados")](images/ch27fg02-large.png#lightbox "renderizadores personalizados")

## <a name="renderers-and-properties"></a>Os renderizadores e propriedades

O próximo conjunto de renderizadores implementa o desenho da elipse e está localizado em vários projetos do [ **Xamarin.FormsBook.Platform** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform) solução.

O [ `EllipseView` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform/EllipseView.cs) classe está no **Xamarin.FormsBook.Platform** plataforma. A classe é semelhante à `BoxView` e define apenas uma única propriedade: `Color` do tipo `Color`.

Os renderizadores podem transferir os valores de propriedade definidos em um `View` ao objeto nativo por meio da substituição de `OnElementPropertyChanged` método em que o renderizador. Dentro desse método (e, na maior parte do processador), duas propriedades estão disponíveis:

- `Element`, o elemento de xamarin. Forms
- `Control`, o modo nativo ou o objeto de controle ou widget

Os tipos dessas propriedades são determinados pelos parâmetros genéricos para `ViewRenderer`. Neste exemplo, `Element` é do tipo `EllipseView`.

O `OnElementPropertyChanged` substituição, portanto, pode transferir a `Color` valor da `Element` ao nativo `Control` objeto, provavelmente com algum tipo de conversão. Os três renderizadores são:

- iOS: [ `EllipseViewRenderer` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.iOS/EllipseViewRenderer.cs), que usa um [ `EllipseUIView` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.iOS/EllipseUIView.cs) classe para a elipse.
- Android: [ `EllipseViewRenderer` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.Android/EllipseViewRenderer.cs), que usa um [ `EllipseDrawableView` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.Android/EllipseDrawableView.cs) classe para a elipse.
- UWP: [ `EllipseViewRenderer` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.WinRT/EllipseViewRenderer.cs), que pode usar o Windows nativo [ `Ellipse` ](/uwp/api/Windows.UI.Xaml.Shapes.Ellipse) classe.

O [ **EllipseDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter27/EllipseDemo) classe exibe várias dessas `EllipseView` objetos:

[![Captura de tela tripla de demonstração de elipse](images/ch27fg03-small.png "renderizadores personalizados de EllipseView")](images/ch27fg03-large.png#lightbox "renderizadores personalizados de EllipseView")

O [ **BouncingBall** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter27/BouncingBall) bounces um `EllipseView` desativado os lados da tela.

## <a name="renderers-and-events"></a>Os renderizadores e eventos

Também é possível que os renderizadores indiretamente gerar eventos. O [ `StepSlider` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform/StepSlider.cs) classe é semelhante ao xamarin. Forms normal `Slider` , mas permite especificar um número de etapas separadas entre o `Minimum` e `Maximum` valores.

Os três renderizadores são:

- iOS: [`StepSliderRenderer`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.iOS/StepSliderRenderer.cs)
- Android: [`StepSliderRenderer`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.Android/StepSliderRenderer.cs)
- UWP: [`StepSliderRenderer`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.WinRT/StepSliderRenderer.cs)

Os renderizadores detectar alterações para o controle nativo e, em seguida, chame `SetValueFromRenderer`, que faz referência a uma propriedade associável definida no `StepSlider`, uma alteração que faz com que o `StepSlider` acionar um `ValueChanged` eventos.

O [ **StepSliderDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter27/StepSliderDemo) que demonstra esse controle deslizante de novo.



## <a name="related-links"></a>Links relacionados

- [Capítulo 27 de texto completo (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch27-Apr2016.pdf)
- [Exemplos do Capítulo 27](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter27)
- [Renderizadores personalizados](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)
