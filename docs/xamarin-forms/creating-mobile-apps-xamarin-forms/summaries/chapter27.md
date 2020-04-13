---
title: Resumo do Capítulo 27. Renderizadores personalizados
description: 'Criando aplicativos móveis com Xamarin.Forms: Resumo do Capítulo 27. Renderizadores personalizados'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 49961953-9336-4FD4-A42F-6D9B05FF52E7
author: davidbritch
ms.author: dabritch
ms.date: 07/18/2018
ms.openlocfilehash: fd4014fa4db4e90596c100d454cf0467512240a4
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "70760500"
---
# <a name="summary-of-chapter-27-custom-renderers"></a>Resumo do Capítulo 27. Renderizadores personalizados

[![Baixar](~/media/shared/download.png) amostra Baixar a amostra](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter27)

> [!NOTE] 
> As notas nesta página indicam áreas onde xamarin.forms divergiu do material apresentado no livro.

Um elemento Xamarin.Forms, como `Button` é renderizado com um botão `ButtonRenderer`específico da plataforma encapsulado em uma classe chamada .  Aqui está a [versão `ButtonRenderer`para iOS de ](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Platform.iOS/Renderers/ButtonRenderer.cs), a [versão para Android de `ButtonRenderer` ](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Platform.Android/Renderers/ButtonRenderer.cs), e a versão [UWP `ButtonRenderer`de ](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Platform.UAP/ButtonRenderer.cs).

Este capítulo discute como você pode escrever seus próprios renderizadores para criar visualizações personalizadas que mapeiam para objetos específicos da plataforma.

## <a name="the-complete-class-hierarchy"></a>A hierarquia completa da classe

Existem quatro conjuntos que contêm o código específico da plataforma Xamarin.Forms.
Você pode visualizar a fonte no GitHub usando esses links:

- [**Xamarin.Forms.Platform**](https://github.com/xamarin/Xamarin.Forms/tree/master/Xamarin.Forms.Platform) (muito pequena)
- [**Xamarin.Forms.Platform.iOS**](https://github.com/xamarin/Xamarin.Forms/tree/master/Xamarin.Forms.Platform.iOS)
- [**Xamarin.Forms.Platform.Android**](https://github.com/xamarin/Xamarin.Forms/tree/master/Xamarin.Forms.Platform.Android)
- [**Xamarin.Forms.Platform.UAP**](https://github.com/xamarin/Xamarin.Forms/tree/master/Xamarin.Forms.Platform.UAP)

> [!NOTE]
> As `WinRT` assembléias mencionadas no livro não fazem mais parte dessa solução. 

A amostra [**PlatformClassHierarchy**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter27/PlatformClassHierarchy) exibe uma hierarquia de classe para os conjuntos válidos para a plataforma de execução.

Você vai notar uma `ViewRenderer`classe importante chamada . Esta é a classe da sua origem ao criar um renderizador específico da plataforma. Ele existe em três versões diferentes, porque está vinculado ao sistema de visualização da plataforma de destino:

O iOS [`ViewRenderer<TView, TNativeView>`](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Platform.iOS/ViewRenderer.cs#L25) tem argumentos genéricos:

- `TView`constrangido a[`Xamarin.Forms.View`](xref:Xamarin.Forms.View)
- `TNativeView`constrangido a[`UIKit.UIView`](xref:UIKit.UIView)

O [`ViewRenderer<TView, TNativeView>`](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Platform.Android/ViewRenderer.cs#L17) Android tem argumentos genéricos:

- `TView`constrangido a[`Xamarin.Forms.View`](xref:Xamarin.Forms.View)
- `TNativeView`constrangido a[`Android.Views.View`](xref:Android.Views.View)

O UWP [`ViewRenderer<TElement, TNativeElement>`](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Platform.UAP/ViewRenderer.cs#L6) tem argumentos genéricos de outra forma:

- `TElement`constrangido a[`Xamarin.Forms.View`](xref:Xamarin.Forms.View)
- `TNativeElement`constrangido a[`Windows.UI.Xaml.FrameworkElement`](/uwp/api/Windows.UI.Xaml.FrameworkElement)

Ao escrever um renderizador, você estará `View`derivando uma `ViewRenderer` classe de , e, em seguida, escrevendo várias classes, uma para cada plataforma suportada. Cada implementação específica da plataforma fará referência a uma classe `TNativeView` `TNativeElement` nativa que deriva do tipo especificado como ou parâmetro.

## <a name="hello-custom-renderers"></a>Olá, renderizadores personalizados!

O programa [**HelloRenderers**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter27/HelloRenderers) faz referência `HelloView` a [`App`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter27/HelloRenderers/HelloRenderers/HelloRenderers/App.cs) uma exibição personalizada nomeada em sua classe.

A [`HelloView`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter27/HelloRenderers/HelloRenderers/HelloRenderers/HelloView.cs) classe está incluída no projeto **HelloRenderers** e simplesmente deriva de `View`.

A [`HelloViewRenderer`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter27/HelloRenderers/HelloRenderers/HelloRenderers.iOS/HelloViewRenderer.cs) classe no projeto **HelloRenderers.iOS** deriva de `ViewRenderer<HelloView, UILabel>`. Na `OnElementChanged` substituição, ele cria um `UILabel` iOS `SetNativeControl`nativo e chama .

A [`HelloViewRenderer`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter27/HelloRenderers/HelloRenderers/HelloRenderers.Droid/HelloViewRenderer.cs) classe do projeto **HelloRenderers.Droid** deriva de `ViewRenderer<HelloView, TextView>`. Na `OnElementChanged` substituição, ele cria `TextView` um `SetNativeControl`Android e chama .

A [`HelloViewRenderer`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter27/HelloRenderers/HelloRenderers/HelloRenderers.UWP/HelloViewRenderer.cs) classe no **HelloRenderers.UWP** e outros `ViewRenderer<HelloView, TextBlock>`projetos do Windows deriva de . Na `OnElementChanged` substituição, ele cria `TextBlock` um `SetNativeControl`Windows e chamadas .

Todos `ViewRenderer` os derivativos contêm um `ExportRenderer` atributo no nível de montagem que associa a `HelloView` classe à classe específica. `HelloViewRenderer` É assim que o Xamarin.Forms localiza renderizadores nos projetos individuais da plataforma:

[![Captura de tela tripla do Hello View](images/ch27fg02-small.png "Renderizadores personalizados")](images/ch27fg02-large.png#lightbox "Renderizadores personalizados")

## <a name="renderers-and-properties"></a>Renderizadores e propriedades

O próximo conjunto de renderizadores implementa o desenho de elipse, e está localizado nos vários projetos da solução [**Xamarin.FormsBook.Platform.**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform)

A [`EllipseView`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform/EllipseView.cs) aula está na plataforma **Xamarin.FormsBook.Platform.** A classe é `BoxView` semelhante e define apenas `Color` uma `Color`única propriedade: do tipo .

Os renderizadores podem transferir valores de propriedade definidos em um `View` para o objeto nativo, substituindo o `OnElementPropertyChanged` método no renderizador. Dentro deste método (e dentro da maioria do renderizador), duas propriedades estão disponíveis:

- `Element`, o elemento Xamarin.Forms
- `Control`, a exibição nativa ou widget ou objeto de controle

Os tipos dessas propriedades são determinados `ViewRenderer`pelos parâmetros genéricos para . Neste exemplo, `Element` é `EllipseView`do tipo .

A `OnElementPropertyChanged` substituição pode, portanto, transferir o `Color` valor do `Element` objeto nativo, `Control` provavelmente com algum tipo de conversão. Os três renderizadores são:

- iOS: [`EllipseViewRenderer`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.iOS/EllipseViewRenderer.cs), que [`EllipseUIView`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.iOS/EllipseUIView.cs) usa uma classe para a elipse.
- Android: [`EllipseViewRenderer`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.Android/EllipseViewRenderer.cs), que [`EllipseDrawableView`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.Android/EllipseDrawableView.cs) usa uma classe para a elipse.
- UWP: [`EllipseViewRenderer`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.WinRT/EllipseViewRenderer.cs), que pode [`Ellipse`](/uwp/api/Windows.UI.Xaml.Shapes.Ellipse) usar a classe nativa do Windows.

A classe [**EllipseDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter27/EllipseDemo) exibe `EllipseView` vários desses objetos:

[![Captura de tela tripla de Elipse Demo](images/ch27fg03-small.png "Renderizadores personalizados ellipseview")](images/ch27fg03-large.png#lightbox "Renderizadores personalizados ellipseview")

A [**Bola de Bouncing ball**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter27/BouncingBall) salta um `EllipseView` fora dos lados da tela.

## <a name="renderers-and-events"></a>Renderizadores e eventos

Também é possível que os renderizadores gerem indiretamente eventos. A [`StepSlider`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform/StepSlider.cs) classe é semelhante à Xamarin.Forms `Slider` normal, mas permite especificar `Minimum` `Maximum` uma série de passos discretos entre os valores.

Os três renderizadores são:

- Ios:[`StepSliderRenderer`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.iOS/StepSliderRenderer.cs)
- Android:[`StepSliderRenderer`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.Android/StepSliderRenderer.cs)
- Uwp:[`StepSliderRenderer`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.WinRT/StepSliderRenderer.cs)

Os renderizadores detectam alterações no `SetValueFromRenderer`controle nativo e, em seguida, `StepSlider`chamam , que `StepSlider` faz referência `ValueChanged` a uma propriedade vinculável definida no , uma alteração à qual faz com que o evento seja acionado.

A amostra [**StepSliderDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter27/StepSliderDemo) demonstra este novo controle deslizante.

## <a name="related-links"></a>Links relacionados

- [Capítulo 27 texto completo (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch27-Apr2016.pdf)
- [Capítulo 27 amostras](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter27)
- [Renderizadores personalizados](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)
