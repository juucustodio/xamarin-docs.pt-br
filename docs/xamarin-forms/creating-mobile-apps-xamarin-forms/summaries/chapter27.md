---
title: Resumo do Capítulo 27. Renderizadores personalizados
description: 'Criação de aplicativos móveis com xamarin. Forms: Resumo do Capítulo 27. Renderizadores personalizados'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 49961953-9336-4FD4-A42F-6D9B05FF52E7
author: davidbritch
ms.author: dabritch
ms.date: 07/18/2018
ms.openlocfilehash: fd4014fa4db4e90596c100d454cf0467512240a4
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2020
ms.locfileid: "70760500"
---
# <a name="summary-of-chapter-27-custom-renderers"></a>Resumo do Capítulo 27. Renderizadores personalizados

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter27)

> [!NOTE] 
> Observações sobre essa página indicam áreas onde o xamarin. Forms foi bifurcado do material apresentado no livro.

Um elemento Xamarin. Forms, como `Button`, é renderizado com um botão específico da plataforma encapsulado em uma classe chamada `ButtonRenderer`.  Aqui está a [versão do IOS do `ButtonRenderer`](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Platform.iOS/Renderers/ButtonRenderer.cs), a [versão do Android do `ButtonRenderer`](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Platform.Android/Renderers/ButtonRenderer.cs)e a [versão UWP do `ButtonRenderer`](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Platform.UAP/ButtonRenderer.cs).

Este capítulo discute como você pode escrever seus próprios renderizadores para criar exibições personalizadas que são mapeadas para objetos específicos da plataforma.

## <a name="the-complete-class-hierarchy"></a>A hierarquia de classe completo

Há quatro assemblies que contêm o código específico da plataforma xamarin. Forms.
Você pode exibir o código-fonte no GitHub usando estes links:

- [**Xamarin. Forms. Platform**](https://github.com/xamarin/Xamarin.Forms/tree/master/Xamarin.Forms.Platform) (muito pequeno)
- [**Xamarin. Forms. Platform. iOS**](https://github.com/xamarin/Xamarin.Forms/tree/master/Xamarin.Forms.Platform.iOS)
- [**Xamarin. Forms. Platform. Android**](https://github.com/xamarin/Xamarin.Forms/tree/master/Xamarin.Forms.Platform.Android)
- [**Xamarin. Forms. Platform. UAP**](https://github.com/xamarin/Xamarin.Forms/tree/master/Xamarin.Forms.Platform.UAP)

> [!NOTE]
> Os assemblies de `WinRT` mencionados no livro não fazem mais parte dessa solução. 

O exemplo [**PlatformClassHierarchy**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter27/PlatformClassHierarchy) exibe uma hierarquia de classe para os assemblies que são válidos para a plataforma em execução.

Você observará uma classe importante chamada `ViewRenderer`. Esta é a classe que você deriva ao criar um renderizador específica da plataforma. Existe nas três versões diferentes, pois ele está vinculado ao sistema de modo de exibição da plataforma de destino:

O [`ViewRenderer<TView, TNativeView>`](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Platform.iOS/ViewRenderer.cs#L25) do IOS tem argumentos genéricos:

- `TView` restrito a [`Xamarin.Forms.View`](xref:Xamarin.Forms.View)
- `TNativeView` restrito a [`UIKit.UIView`](xref:UIKit.UIView)

O [`ViewRenderer<TView, TNativeView>`](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Platform.Android/ViewRenderer.cs#L17) do Android tem argumentos genéricos:

- `TView` restrito a [`Xamarin.Forms.View`](xref:Xamarin.Forms.View)
- `TNativeView` restrito a [`Android.Views.View`](xref:Android.Views.View)

O [`ViewRenderer<TElement, TNativeElement>`](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Platform.UAP/ViewRenderer.cs#L6) UWP tem nomes de argumentos genéricos diferentes:

- `TElement` restrito a [`Xamarin.Forms.View`](xref:Xamarin.Forms.View)
- `TNativeElement` restrito a [`Windows.UI.Xaml.FrameworkElement`](/uwp/api/Windows.UI.Xaml.FrameworkElement)

Ao escrever um processador, você estará derivando uma classe de `View`e, em seguida, gravando várias classes de `ViewRenderer`, uma para cada plataforma com suporte. Cada implementação específica da plataforma fará referência a uma classe nativa que deriva do tipo especificado como o parâmetro `TNativeView` ou `TNativeElement`.

## <a name="hello-custom-renderers"></a>Olá, renderizadores personalizados!

O programa [**HelloRenderers**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter27/HelloRenderers) faz referência a uma exibição personalizada chamada `HelloView` em sua classe [`App`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter27/HelloRenderers/HelloRenderers/HelloRenderers/App.cs) .

A classe [`HelloView`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter27/HelloRenderers/HelloRenderers/HelloRenderers/HelloView.cs) está incluída no projeto **HelloRenderers** e simplesmente deriva de `View`.

A classe [`HelloViewRenderer`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter27/HelloRenderers/HelloRenderers/HelloRenderers.iOS/HelloViewRenderer.cs) no projeto **HelloRenderers. iOS** deriva de `ViewRenderer<HelloView, UILabel>`. Na substituição de `OnElementChanged`, ele cria um `UILabel` iOS nativo e chama `SetNativeControl`.

A classe [`HelloViewRenderer`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter27/HelloRenderers/HelloRenderers/HelloRenderers.Droid/HelloViewRenderer.cs) no projeto **HelloRenderers. Droid** deriva de `ViewRenderer<HelloView, TextView>`. Na substituição de `OnElementChanged`, ele cria um `TextView` Android e chama `SetNativeControl`.

A classe [`HelloViewRenderer`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter27/HelloRenderers/HelloRenderers/HelloRenderers.UWP/HelloViewRenderer.cs) no **HelloRenderers. UWP** e em outros projetos do Windows deriva de `ViewRenderer<HelloView, TextBlock>`. Na substituição de `OnElementChanged`, ele cria um `TextBlock` do Windows e chama `SetNativeControl`.

Todas as derivações de `ViewRenderer` contêm um atributo `ExportRenderer` no nível de assembly que associa a classe `HelloView` à classe `HelloViewRenderer` específica. Isso é como o xamarin. Forms localiza renderizadores nos projetos de plataforma individual:

[![Captura de tela tripla da exibição de saudação](images/ch27fg02-small.png "Renderizadores personalizados")](images/ch27fg02-large.png#lightbox "Renderizadores personalizados")

## <a name="renderers-and-properties"></a>Os renderizadores e propriedades

O próximo conjunto de renderizadores implementa o desenho em elipse e está localizado nos vários projetos da solução [**Xamarin. FormsBook. Platform**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform) .

A classe [`EllipseView`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform/EllipseView.cs) está na plataforma **Xamarin. FormsBook. Platform** . A classe é semelhante a `BoxView` e define apenas uma única propriedade: `Color` do tipo `Color`.

Os renderizadores podem transferir valores de propriedade definidos em um `View` para o objeto nativo substituindo o método `OnElementPropertyChanged` no processador. Dentro desse método (e, na maior parte do processador), duas propriedades estão disponíveis:

- `Element`, o elemento Xamarin. Forms
- `Control`, a exibição nativa ou o widget ou objeto de controle

Os tipos dessas propriedades são determinados pelos parâmetros genéricos para `ViewRenderer`. Neste exemplo, `Element` é do tipo `EllipseView`.

A substituição de `OnElementPropertyChanged` pode, portanto, transferir o valor de `Color` do `Element` para o objeto `Control` nativo, provavelmente com algum tipo de conversão. Os três renderizadores são:

- iOS: [`EllipseViewRenderer`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.iOS/EllipseViewRenderer.cs), que usa uma classe [`EllipseUIView`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.iOS/EllipseUIView.cs) para a elipse.
- Android: [`EllipseViewRenderer`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.Android/EllipseViewRenderer.cs), que usa uma classe [`EllipseDrawableView`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.Android/EllipseDrawableView.cs) para a elipse.
- UWP: [`EllipseViewRenderer`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.WinRT/EllipseViewRenderer.cs), que pode usar a classe nativa do Windows [`Ellipse`](/uwp/api/Windows.UI.Xaml.Shapes.Ellipse) .

A classe [**EllipseDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter27/EllipseDemo) exibe vários desses objetos `EllipseView`:

[![Captura de tela tripla de demonstração de elipse](images/ch27fg03-small.png "Renderizadores personalizados do EllipseView")](images/ch27fg03-large.png#lightbox "Renderizadores personalizados do EllipseView")

O [**BouncingBall**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter27/BouncingBall) salta um `EllipseView` dos lados da tela.

## <a name="renderers-and-events"></a>Os renderizadores e eventos

Também é possível que os renderizadores indiretamente gerar eventos. A classe [`StepSlider`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform/StepSlider.cs) é semelhante à `Slider` normal do Xamarin. Forms, mas permite especificar várias etapas discretas entre os valores `Minimum` e `Maximum`.

Os três renderizadores são:

- iOS: [`StepSliderRenderer`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.iOS/StepSliderRenderer.cs)
- Android: [`StepSliderRenderer`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.Android/StepSliderRenderer.cs)
- UWP: [`StepSliderRenderer`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.WinRT/StepSliderRenderer.cs)

Os renderizadores detectam alterações no controle nativo e, em seguida, chamam `SetValueFromRenderer`, que faz referência a uma propriedade vinculável definida no `StepSlider`, uma alteração que faz com que o `StepSlider` dispare um evento de `ValueChanged`.

O exemplo [**StepSliderDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter27/StepSliderDemo) demonstra esse novo controle deslizante.

## <a name="related-links"></a>Links relacionados

- [Capítulo 27 de texto completo (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch27-Apr2016.pdf)
- [Capítulo 27 amostras](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter27)
- [Renderizadores personalizados](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)
