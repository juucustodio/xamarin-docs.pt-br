---
title: recursos da plataforma iOS no Xamarin. Forms
description: Adicionar funcionalidade específica do iOS a aplicativos Xamarin. Forms.
ms.prod: xamarin
ms.assetid: 634AB62E-68C8-454C-838B-F1CC4E4E21BC
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/15/2020
ms.openlocfilehash: 015db40ce983d979109d4cce32c011f8c61a729c
ms.sourcegitcommit: 10b4d7952d78f20f753372c53af6feb16918555c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/26/2020
ms.locfileid: "78291867"
---
# <a name="ios-platform-features-in-xamarinforms"></a>recursos da plataforma iOS no Xamarin. Forms

O desenvolvimento de aplicativos Xamarin. Forms para iOS requer o Visual Studio. A [página plataformas com suporte](~/get-started/supported-platforms.md) contém mais informações sobre os pré-requisitos.

## <a name="platform-specifics"></a>Elementos específicos da plataforma

Especificidades da plataforma permitem que você consumir funcionalidade só está disponível em uma plataforma específica, sem implementar renderizadores personalizados ou efeitos.

A funcionalidade específica da plataforma a seguir é fornecida para exibições, páginas e layouts do Xamarin. Forms no iOS:

- Suporte a desfoque para qualquer [`VisualElement`](xref:Xamarin.Forms.VisualElement). Para obter mais informações, consulte o [Desfoque do visualelement no Ios](visualelement-blur.md).
- Desabilitando o modo de cor herdado em um [`VisualElement`](xref:Xamarin.Forms.VisualElement)com suporte. Para obter mais informações, consulte [modo de cor herdado do visualelement no Ios](legacy-color-mode.md).
- Habilitando uma sombra suspensa em um [`VisualElement`](xref:Xamarin.Forms.VisualElement). Para obter mais informações, consulte as [sombras de soltar do visualelement no Ios](visualelement-drop-shadow.md).
- Habilitar um objeto [`VisualElement`](xref:Xamarin.Forms.VisualElement) para se tornar o primeiro Respondente para eventos de toque. Para obter mais informações, consulte [visualelement primeiro Respondente](visualelement-first-responder.md).

A seguinte funcionalidade específica da plataforma é fornecida para exibições do Xamarin. Forms no iOS:

- Definindo o [`Cell`](xref:Xamarin.Forms.Cell) cor do plano de fundo. Para obter mais informações, consulte [cor do plano de fundo da célula no Ios](cell-background-color.md).
- Controlando quando a seleção de item ocorre em um [`DatePicker`](xref:Xamarin.Forms.DatePicker). Para obter mais informações, consulte [DatePicker item selection on Ios](datepicker-selection.md).
- Garantir que o texto inserido caiba em um [`Entry`](xref:Xamarin.Forms.Entry) ajustando o tamanho da fonte. Para obter mais informações, consulte [tamanho da fonte de entrada em Ios](entry-font-size.md).
- Definindo a cor do cursor em um [`Entry`](xref:Xamarin.Forms.Entry). Para obter mais informações, consulte [cor do cursor de entrada no Ios](entry-cursor-color.md).
- Controlando se [`ListView`](xref:Xamarin.Forms.ListView) células de cabeçalho flutuam durante a rolagem. Para obter mais informações, consulte [estilo de cabeçalho do grupo ListView no Ios](listview-group-header-style.md).
- Controlando se as animações de linha são desabilitadas quando a coleção de itens de [`ListView`](xref:Xamarin.Forms.ListView) está sendo atualizada. Para obter mais informações, consulte [animações de linha ListView no Ios](listview-row-animations.md).
- Definindo o estilo do separador em um [`ListView`](xref:Xamarin.Forms.ListView). Para obter mais informações, consulte [estilo do separador ListView no Ios](listview-separator-style.md).
- Controlando quando a seleção de item ocorre em um [`Picker`](xref:Xamarin.Forms.Picker). Para obter mais informações, consulte [seleção de item do seletor no Ios](picker-selection.md).
- Habilitando a propriedade [`Slider.Value`](xref:Xamarin.Forms.Slider.Value) a ser definida tocando em uma posição na barra de [`Slider`](xref:Xamarin.Forms.Slider) , em vez de ter que arrastar o `Slider` Thumb. Para obter mais informações, consulte [controle deslizante do slider no Ios](slider-thumb.md).
- Controlando a transição usada ao abrir um `SwipeView`. Para obter mais informações, consulte [modo de transição de toque SwipeView](swipeview-swipetransitionmode.md).
- Controlando quando a seleção de item ocorre em um [`TimePicker`](xref:Xamarin.Forms.TimePicker). Para obter mais informações, consulte [seleção de item de seletor de no Ios](timepicker-selection.md).

A seguinte funcionalidade específica de plataforma é fornecida para páginas Xamarin. Forms no iOS:

- Ocultando o separador de barra de navegação em um [`NavigationPage`](xref:Xamarin.Forms.NavigationPage). Para obter mais informações, consulte [separador de barra NavigationPage no Ios](navigation-bar-separator.md).
- Controlar se a barra de navegação é translúcida. Para obter mais informações, consulte [barra de navegação Translucency no Ios](navigation-bar-translucent.md).
- Controlar se a cor do texto da barra de status em uma [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) é ajustada para corresponder à luminosidade da barra de navegação. Para obter mais informações, consulte [modo de cor de texto da barra de NavigationPage no Ios](status-bar-text-color.md).
- Controlar se o título da página é exibido como um título grande na barra de navegação de página. Para obter mais informações, consulte [títulos de páginas grandes no Ios](page-large-title.md).
- Definindo a visibilidade do indicador inicial em um [`Page`](xref:Xamarin.Forms.Page). Para obter mais informações, consulte a [visibilidade do indicador inicial no Ios](page-home-indicator.md).
- Definindo a visibilidade da barra de status em uma [`Page`](xref:Xamarin.Forms.Page). Para obter mais informações, consulte [visibilidade da barra de status da página no Ios](page-status-bar-visibility.md).
- Garantir que esse conteúdo da página está posicionado em uma área da tela que é segura para todos os dispositivos iOS. Para obter mais informações, consulte [Guia de layout de área segura no Ios](page-safe-area-layout.md).
- Definindo o estilo de apresentação das páginas modais. Para obter mais informações, consulte [estilo de apresentação de página modal](page-presentation-style.md).
- Definindo o modo Translucency da barra de guias em uma [`TabbedPage`](xref:Xamarin.Forms.TabbedPage). Para obter mais informações, consulte [TabbedPage translúcida Tabbar no Ios](tabbedpage-translucent-tabbar.md).

A funcionalidade específica da plataforma a seguir é fornecida para layouts do Xamarin. Forms no iOS:

- Controlar se um [`ScrollView`](xref:Xamarin.Forms.ScrollView) manipula um gesto de toque ou o passa para seu conteúdo. Para obter mais informações, consulte [ScrollView Content Entoques on Ios](scrollview-content-touches.md).

A funcionalidade específica da plataforma a seguir é fornecida para a classe [`Application`](xref:Xamarin.Forms.Application) Xamarin. Forms no IOS:

- Desabilitando o dimensionamento de acessibilidade para tamanhos de fonte nomeados. Para obter mais informações, consulte [dimensionamento de acessibilidade para tamanhos de fonte nomeados no Ios](named-font-size-scaling.md).
- Habilitando o controle de layout e renderização atualizações a serem executadas no thread principal. Para obter mais informações, consulte [principais atualizações de controle de thread no Ios](main-thread-updates-ui.md).
- Habilitar uma [`PanGestureRecognizer`](xref:Xamarin.Forms.PanGestureRecognizer) em uma exibição de rolagem para capturar e compartilhar o gesto de panorâmica com a exibição de rolagem. Para obter mais informações, consulte [reconhecimento de gesto de Pan simultânea no Ios](application-pan-gesture.md).

## <a name="ios-specific-formatting"></a>formatação específica do iOS

O Xamarin. Forms permite definir estilos e cores de interface do usuário de plataforma cruzada, mas há outras opções para definir o tema do seu iOS usando APIs de plataforma no projeto do iOS.

[Leia mais](formatting.md) sobre como formatar a interface do usuário usando APIs específicas do IOS, como a configuração **info. plist** e a API `UIAppearance`.

![](images/status-white-sml.png "iOS Theming")

## <a name="other-ios-features"></a>Outros recursos do iOS

Usando [renderizadores personalizados](~/xamarin-forms/app-fundamentals/custom-renderer/index.md), o [DependencyService](~/xamarin-forms/app-fundamentals/dependency-service/index.md)e o [MessagingCenter](~/xamarin-forms/app-fundamentals/messaging-center.md), é possível incorporar uma ampla variedade de funcionalidades nativas aos aplicativos Xamarin. Forms para Ios.
