---
title: Resumo do Capítulo 27. Processadores personalizados
description: 'Criando aplicativos móveis com o xamarin. Forms: Resumo do Capítulo 27. Processadores personalizados'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 49961953-9336-4FD4-A42F-6D9B05FF52E7
author: charlespetzold
ms.author: chape
ms.date: 11/07/2017
ms.openlocfilehash: 844c9be72071cd307bd2330b54f8d70ddf28b9a3
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/08/2018
ms.locfileid: "35240876"
---
# <a name="summary-of-chapter-27-custom-renderers"></a>Resumo do Capítulo 27. Processadores personalizados

Um elemento xamarin. Forms como `Button` é processado com um botão específico da plataforma encapsulado em uma classe denominada `ButtonRenderer`.  Aqui está o [versão do iOS do `ButtonRenderer` ](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Platform.iOS/Renderers/ButtonRenderer.cs), o [versão Android do `ButtonRenderer` ](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Platform.Android/Renderers/ButtonRenderer.cs)e o [versão de tempo de execução do Windows do `ButtonRenderer` ](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Platform.WinRT/ButtonRenderer.cs).

Este capítulo discute como você pode escrever seus próprios renderizadores para criar exibições personalizadas que são mapeados para objetos específicos de plataforma.

## <a name="the-complete-class-hierarchy"></a>A hierarquia de classe completo

Há sete assemblies que contêm o código de plataforma específica do xamarin. Forms.
Você pode exibir a origem no GitHub usando esses links:

- [**Xamarin.Forms.Platform** ](https://github.com/xamarin/Xamarin.Forms/tree/master/Xamarin.Forms.Platform) (muito pequeno)
- [**Xamarin.Forms.Platform.iOS**](https://github.com/xamarin/Xamarin.Forms/tree/master/Xamarin.Forms.Platform.iOS)
- [**Xamarin.Forms.Platform.Android**](https://github.com/xamarin/Xamarin.Forms/tree/master/Xamarin.Forms.Platform.Android)
- [**Xamarin.Forms.Platform.WinRT** ](https://github.com/xamarin/Xamarin.Forms/tree/master/Xamarin.Forms.Platform.WinRT) (maior do que as próximas três)
- [**Xamarin.Forms.Platform.UAP**](https://github.com/xamarin/Xamarin.Forms/tree/master/Xamarin.Forms.Platform.UAP)
- [**Xamarin.Forms.Platform.WinRT.Tablet**](https://github.com/xamarin/Xamarin.Forms/tree/master/Xamarin.Forms.Platform.WinRT.Tablet)
- [**Xamarin.Forms.Platform.WinRT.Phone**](https://github.com/xamarin/Xamarin.Forms/tree/master/Xamarin.Forms.Platform.WinRT.Phone)

O [ **PlatformClassHierarchy** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter27/PlatformClassHierarchy) exemplo exibe uma hierarquia de classe para os assemblies que são válidos para a plataforma em execução.

Você observará que uma classe importante chamada `ViewRenderer`. Esta é a classe que deriva durante a criação de um processador específico da plataforma. Ele existe em três versões, porque ele está ligado ao sistema de exibição da plataforma de destino:

O iOS [ `ViewRenderer<TView, TNativeView>` ](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Platform.iOS/ViewRenderer.cs#L26) tem argumentos genéricos:

- `TView` restrito a [`Xamarin.Forms.View`](https://developer.xamarin.com/api/type/Xamarin.Forms.View/)
- `TNativeView` restrito a [`UIKit.UIView`](https://developer.xamarin.com/api/type/UIKit.UIView/)

O Android [ `ViewRenderer<TView, TNativeView>` ](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Platform.Android/ViewRenderer.cs#L14) tem argumentos genéricos:

- `TView` restrito a [`Xamarin.Forms.View`](https://developer.xamarin.com/api/type/Xamarin.Forms.View/)
- `TNativeView` restrito a [`Android.Views.View`](https://developer.xamarin.com/api/type/Android.Views.View/)

O tempo de execução do Windows [ `ViewRenderer<TElement, TNativeElement>` ](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Platform.WinRT/ViewRenderer.cs#L12) diferentemente nomeou argumentos genéricos:

- `TElement` restrito a [`Xamarin.Forms.View`](https://developer.xamarin.com/api/type/Xamarin.Forms.View/)
- `TNativeElement` restrito a [`Windows.UI.Xaml.FrameworkElement`](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.frameworkelement.aspx)

Ao escrever um processador, você será derivando uma classe de `View`e, em seguida, escrever várias `ViewRenderer` classes, uma para cada plataforma com suporte. Cada implementação específica de plataforma fará referência a uma classe nativo que deriva do tipo especificado como o `TNativeView` ou `TNativeElement` parâmetro.

## <a name="hello-custom-renderers"></a>Olá, renderizadores personalizados!

O [ **HelloRenderers** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter27/HelloRenderers) programa faz referência a uma exibição personalizada chamada `HelloView` no seu [ `App` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter27/HelloRenderers/HelloRenderers/HelloRenderers/App.cs) classe.

O [ `HelloView` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter27/HelloRenderers/HelloRenderers/HelloRenderers/HelloView.cs) classe está incluída no **HelloRenderers** do projeto e deriva de `View`.

O [ `HelloViewRenderer` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter27/HelloRenderers/HelloRenderers/HelloRenderers.iOS/HelloViewRenderer.cs) classe no **HelloRenderers.iOS** projeto deriva de `ViewRenderer<HelloView, UILabel>`. No `OnElementChanged` override, ele cria um iOS nativo `UILabel` e chamadas `SetNativeControl`.

O [ `HelloViewRenderer` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter27/HelloRenderers/HelloRenderers/HelloRenderers.Droid/HelloViewRenderer.cs) classe no **HelloRenderers.Droid** projeto deriva de `ViewRenderer<HelloView, TextView>`. No `OnElementChanged` override, ele cria um Android `TextView` e chamadas `SetNativeControl`.

O [ `HelloViewRenderer` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter27/HelloRenderers/HelloRenderers/HelloRenderers.UWP/HelloViewRenderer.cs) classe no **HelloRenderers.UWP** e deriva de outros projetos do Windows `ViewRenderer<HelloView, TextBlock>`. No `OnElementChanged` override, ele cria um Windows `TextBlock` e chamadas `SetNativeControl`.

Todos os a `ViewRenderer` derivados contêm um `ExportRenderer` atributo no nível de assembly que associa o `HelloView` classe com determinado `HelloViewRenderer` classe. Isso é como o xamarin. Forms localiza renderizadores nos projetos de plataforma individual:

[![Tripla captura de tela da exibição Hello](images/ch27fg02-small.png "personalizado renderizadores")](images/ch27fg02-large.png#lightbox "renderizadores personalizado")

## <a name="renderers-and-properties"></a>Processadores e propriedades

O próximo conjunto de renderizadores implementa o desenho de elipse e está localizado em vários projetos do [ **Xamarin.FormsBook.Platform** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform) solução.

O [ `EllipseView` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform/EllipseView.cs) classe é o **Xamarin.FormsBook.Platform** plataforma. A classe é semelhante a `BoxView` e define apenas uma única propriedade: `Color` do tipo `Color`.

Os renderizadores podem transferir os valores de propriedade definidos em um `View` para o objeto nativo, substituindo o `OnElementPropertyChanged` método no renderizador. Dentro desse método (e, na maior parte do renderizador), duas propriedades estão disponíveis:

- `Element`, o elemento xamarin. Forms
- `Control`, o modo nativo ou o objeto de controle ou widget

Os tipos dessas propriedades são determinados pelos parâmetros genéricos `ViewRenderer`. Neste exemplo, `Element` é do tipo `EllipseView`.

O `OnElementPropertyChanged` substituição, portanto, pode transferir o `Color` valor o `Element` para nativo `Control` objeto provavelmente com algum tipo de conversão. Os três renderizadores são:

- iOS: [ `EllipseViewRenderer` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.iOS/EllipseViewRenderer.cs), que usa um [ `EllipseUIView` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.iOS/EllipseUIView.cs) classe para a elipse.
- Android: [ `EllipseViewRenderer` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.Android/EllipseViewRenderer.cs), que usa um [ `EllipseDrawableView` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.Android/EllipseDrawableView.cs) classe para a elipse.
- Tempo de execução do Windows: [ `EllipseViewRenderer` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.WinRT/EllipseViewRenderer.cs), que pode usar o Windows nativo [ `Ellipse` ](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.shapes.ellipse.aspx) classe.

O [ **EllipseDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter27/EllipseDemo) classe exibe várias dessas `EllipseView` objetos:

[![Captura de tela tripla de demonstração de elipse](images/ch27fg03-small.png "EllipseView personalizado renderizadores")](images/ch27fg03-large.png#lightbox "EllipseView personalizado renderizadores")

O [ **BouncingBall** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter27/BouncingBall) bounces um `EllipseView` off os lados da tela.

## <a name="renderers-and-events"></a>Processadores e eventos

Também é possível para renderizadores indiretamente gerar eventos. O [ `StepSlider` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform/StepSlider.cs) classe é semelhante do xamarin. Forms normal `Slider` , mas permite especificar um número de etapas distintas entre o `Minimum` e `Maximum` valores.

Os três renderizadores são:

- iOS: [`StepSliderRenderer`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.iOS/StepSliderRenderer.cs)
- Android: [`StepSliderRenderer`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.Android/StepSliderRenderer.cs)
- Tempo de execução do Windows: [`StepSliderRenderer`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.WinRT/StepSliderRenderer.cs)

Os renderizadores detectar alterações ao controle nativo e, em seguida, chamar `SetValueFromRenderer`, que faz referência a uma propriedade ligável definida no `StepSlider`, uma alteração que faz com que o `StepSlider` para acionar um `ValueChanged` eventos.

O [ **StepSliderDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter27/StepSliderDemo) demonstra esse controle deslizante de novo.



## <a name="related-links"></a>Links relacionados

- [Capítulo 27 de texto completo (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch27-Apr2016.pdf)
- [Exemplos de capítulo 27](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter27)
- [Renderizadores personalizados](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)
