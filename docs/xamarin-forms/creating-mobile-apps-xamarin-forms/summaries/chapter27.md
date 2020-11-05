---
title: Resumo do capítulo 27. Renderizadores personalizados
description: 'Criando aplicativos móveis com Xamarin.Forms : Resumo do capítulo 27. Renderizadores personalizados'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 49961953-9336-4FD4-A42F-6D9B05FF52E7
author: davidbritch
ms.author: dabritch
ms.date: 07/18/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 6b117688e1bf85266c274a44e6a4b1fda1ef5134
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93375142"
---
# <a name="summary-of-chapter-27-custom-renderers"></a>Resumo do capítulo 27. Renderizadores personalizados

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter27)

> [!NOTE]
> Este livro foi publicado na Primavera de 2016 e não foi atualizado desde então. Há muito no livro que permanece valioso, mas alguns materiais estão desatualizados e alguns tópicos não estão mais totalmente corretos ou completos.

Um Xamarin.Forms elemento como `Button` é renderizado com um botão específico da plataforma encapsulado em uma classe chamada `ButtonRenderer` .  Aqui está a [ `ButtonRenderer` versão do IOS ](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Platform.iOS/Renderers/ButtonRenderer.cs)do, a [versão do `ButtonRenderer` Android do ](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Platform.Android/Renderers/ButtonRenderer.cs)e a [versão UWP `ButtonRenderer` do ](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Platform.UAP/ButtonRenderer.cs).

Este capítulo discute como você pode escrever seus próprios renderizadores para criar exibições personalizadas que são mapeadas para objetos específicos da plataforma.

## <a name="the-complete-class-hierarchy"></a>A hierarquia de classe completa

Há quatro assemblies que contêm o Xamarin.Forms código específico da plataforma.
Você pode exibir a origem no GitHub usando estes links:

- [**Xamarin.Forms. Plataforma**](https://github.com/xamarin/Xamarin.Forms/tree/master/Xamarin.Forms.Platform) (muito pequena)
- [**Xamarin.Forms. Platform. iOS**](https://github.com/xamarin/Xamarin.Forms/tree/master/Xamarin.Forms.Platform.iOS)
- [**Xamarin.Forms. Platform. Android**](https://github.com/xamarin/Xamarin.Forms/tree/master/Xamarin.Forms.Platform.Android)
- [**Xamarin.Forms. Platform. UAP**](https://github.com/xamarin/Xamarin.Forms/tree/master/Xamarin.Forms.Platform.UAP)

> [!NOTE]
> Os `WinRT` assemblies mencionados no livro não fazem mais parte dessa solução.

O exemplo [**PlatformClassHierarchy**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter27/PlatformClassHierarchy) exibe uma hierarquia de classe para os assemblies que são válidos para a plataforma em execução.

Você observará uma classe importante denominada `ViewRenderer` . Essa é a classe que você deriva ao criar um renderizador específico da plataforma. Ele existe em três versões diferentes, pois está vinculado ao sistema de exibição da plataforma de destino:

O iOS [`ViewRenderer<TView, TNativeView>`](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Platform.iOS/ViewRenderer.cs#L25) tem argumentos genéricos:

- `TView` restrito a [`Xamarin.Forms.View`](xref:Xamarin.Forms.View)
- `TNativeView` restrito a [`UIKit.UIView`](xref:UIKit.UIView)

O Android [`ViewRenderer<TView, TNativeView>`](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Platform.Android/ViewRenderer.cs#L17) tem argumentos genéricos:

- `TView` restrito a [`Xamarin.Forms.View`](xref:Xamarin.Forms.View)
- `TNativeView` restrito a [`Android.Views.View`](xref:Android.Views.View)

O UWP [`ViewRenderer<TElement, TNativeElement>`](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Platform.UAP/ViewRenderer.cs#L6) tem nomes de argumentos genéricos diferentes:

- `TElement` restrito a [`Xamarin.Forms.View`](xref:Xamarin.Forms.View)
- `TNativeElement` restrito a [`Windows.UI.Xaml.FrameworkElement`](/uwp/api/Windows.UI.Xaml.FrameworkElement)

Ao escrever um processador, você estará derivando uma classe de `View` e, em seguida, gravando várias `ViewRenderer` classes, uma para cada plataforma com suporte. Cada implementação específica da plataforma fará referência a uma classe nativa que deriva do tipo especificado como o `TNativeView` `TNativeElement` parâmetro ou.

## <a name="hello-custom-renderers"></a>Olá, renderizadores personalizados!

O programa [**HelloRenderers**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter27/HelloRenderers) faz referência a uma exibição personalizada chamada `HelloView` em sua [`App`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter27/HelloRenderers/HelloRenderers/HelloRenderers/App.cs) classe.

A [`HelloView`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter27/HelloRenderers/HelloRenderers/HelloRenderers/HelloView.cs) classe é incluída no projeto **HelloRenderers** e simplesmente deriva de `View` .

A [`HelloViewRenderer`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter27/HelloRenderers/HelloRenderers/HelloRenderers.iOS/HelloViewRenderer.cs) classe no projeto **HelloRenderers. Ios** deriva de `ViewRenderer<HelloView, UILabel>` . Na `OnElementChanged` substituição, ele cria um Ios nativo `UILabel` e chama `SetNativeControl` .

A [`HelloViewRenderer`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter27/HelloRenderers/HelloRenderers/HelloRenderers.Droid/HelloViewRenderer.cs) classe no projeto **HelloRenderers. Droid** deriva de `ViewRenderer<HelloView, TextView>` . Na `OnElementChanged` substituição, ele cria um Android `TextView` e chama `SetNativeControl` .

A [`HelloViewRenderer`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter27/HelloRenderers/HelloRenderers/HelloRenderers.UWP/HelloViewRenderer.cs) classe no **HELLORENDERERS. UWP** e em outros projetos do Windows deriva de `ViewRenderer<HelloView, TextBlock>` . Na `OnElementChanged` substituição, ele cria um Windows `TextBlock` e chama `SetNativeControl` .

Todos os `ViewRenderer` derivativos contêm um `ExportRenderer` atributo no nível de assembly que associa a `HelloView` classe à classe específica `HelloViewRenderer` . É assim que o Xamarin.Forms localiza renderizadores nos projetos da plataforma individual:

[![Captura de tela tripla da exibição de saudação](images/ch27fg02-small.png "Renderizadores personalizados")](images/ch27fg02-large.png#lightbox "Renderizadores personalizados")

## <a name="renderers-and-properties"></a>Renderizadores e propriedades

O próximo conjunto de renderizadores implementa o desenho em elipse e está localizado nos vários projetos da solução [**Xamarin.Forms book. Platform**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform) .

A [`EllipseView`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform/EllipseView.cs) classe está na plataforma **Xamarin.Forms book. Platform** . A classe é semelhante a `BoxView` e define apenas uma única propriedade: `Color` do tipo `Color` .

Os renderizadores podem transferir valores de propriedade definidos em um `View` para o objeto nativo substituindo o `OnElementPropertyChanged` método no renderizador. Dentro desse método (e dentro da maior parte do renderizador), duas propriedades estão disponíveis:

- `Element`, o Xamarin.Forms elemento
- `Control`, a exibição nativa ou o widget ou objeto de controle

Os tipos dessas propriedades são determinados pelos parâmetros genéricos para `ViewRenderer` . Neste exemplo, `Element` é do tipo `EllipseView` .

`OnElementPropertyChanged`Portanto, a substituição pode transferir o `Color` valor do `Element` para o `Control` objeto nativo, provavelmente com algum tipo de conversão. Os três renderizadores são:

- iOS: [`EllipseViewRenderer`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.iOS/EllipseViewRenderer.cs) , que usa uma [`EllipseUIView`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.iOS/EllipseUIView.cs) classe para a elipse.
- Android: [`EllipseViewRenderer`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.Android/EllipseViewRenderer.cs) , que usa uma [`EllipseDrawableView`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.Android/EllipseDrawableView.cs) classe para a elipse.
- UWP: [`EllipseViewRenderer`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.WinRT/EllipseViewRenderer.cs) , que pode usar a classe nativa do Windows [`Ellipse`](/uwp/api/Windows.UI.Xaml.Shapes.Ellipse) .

A classe [**EllipseDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter27/EllipseDemo) exibe vários desses `EllipseView` objetos:

[![Captura de tela tripla de demonstração de elipse](images/ch27fg03-small.png "Renderizadores personalizados do EllipseView")](images/ch27fg03-large.png#lightbox "Renderizadores personalizados do EllipseView")

O [**BouncingBall**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter27/BouncingBall) salta um `EllipseView` dos lados da tela.

## <a name="renderers-and-events"></a>Renderizadores e eventos

Também é possível que os renderizadores gerem eventos indiretamente. A [`StepSlider`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform/StepSlider.cs) classe é semelhante à normal Xamarin.Forms `Slider` , mas permite especificar várias etapas discretas entre os `Minimum` valores e `Maximum` .

Os três renderizadores são:

- Cisco [`StepSliderRenderer`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.iOS/StepSliderRenderer.cs)
- Android [`StepSliderRenderer`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.Android/StepSliderRenderer.cs)
- UWP [`StepSliderRenderer`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.WinRT/StepSliderRenderer.cs)

Os renderizadores detectam alterações no controle nativo e, em seguida, chamam `SetValueFromRenderer` , que referenciam uma propriedade vinculável definida no `StepSlider` , uma alteração que faz com que o `StepSlider` dispare um `ValueChanged` evento.

O exemplo [**StepSliderDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter27/StepSliderDemo) demonstra esse novo controle deslizante.

## <a name="related-links"></a>Links relacionados

- [Capítulo 27 de texto completo (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch27-Apr2016.pdf)
- [Capítulo 27 amostras](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter27)
- [Renderizadores personalizados](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)
