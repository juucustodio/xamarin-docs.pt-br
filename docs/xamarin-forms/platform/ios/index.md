---
title: recursos da plataforma iOS no Xamarin. Forms
description: Adicionar funcionalidade específica do iOS a aplicativos Xamarin. Forms.
ms.prod: xamarin
ms.assetid: 634AB62E-68C8-454C-838B-F1CC4E4E21BC
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/05/2020
ms.openlocfilehash: 97b9b70a1df61beb7b064c99721b4277e2570b41
ms.sourcegitcommit: 8d13d2262d02468c99c4e18207d50cd82275d233
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/29/2020
ms.locfileid: "82517052"
---
# <a name="ios-platform-features-in-xamarinforms"></a>recursos da plataforma iOS no Xamarin. Forms

O desenvolvimento de aplicativos Xamarin. Forms para iOS requer o Visual Studio. A [página plataformas com suporte](~/get-started/supported-platforms.md) contém mais informações sobre os pré-requisitos.

## <a name="platform-specifics"></a>Elementos específicos da plataforma

As especificações de plataforma permitem que você consuma a funcionalidade que só está disponível em uma plataforma específica, sem implementar renderizadores ou efeitos personalizados.

A funcionalidade específica da plataforma a seguir é fornecida para exibições, páginas e layouts do Xamarin. Forms no iOS:

- Suporte de desfoque [`VisualElement`](xref:Xamarin.Forms.VisualElement)para qualquer. Para obter mais informações, consulte o [Desfoque do visualelement no Ios](visualelement-blur.md).
- Desabilitando o modo de cor herdado [`VisualElement`](xref:Xamarin.Forms.VisualElement)em um com suporte. Para obter mais informações, consulte [modo de cor herdado do visualelement no Ios](legacy-color-mode.md).
- Habilitando uma sombra projetada em [`VisualElement`](xref:Xamarin.Forms.VisualElement)um. Para obter mais informações, consulte as [sombras de soltar do visualelement no Ios](visualelement-drop-shadow.md).
- Habilitando [`VisualElement`](xref:Xamarin.Forms.VisualElement) um objeto para se tornar o primeiro Respondente a eventos de toque. Para obter mais informações, consulte [visualelement primeiro Respondente](visualelement-first-responder.md).

A seguinte funcionalidade específica da plataforma é fornecida para exibições do Xamarin. Forms no iOS:

- Definindo a [`Cell`](xref:Xamarin.Forms.Cell) cor do plano de fundo. Para obter mais informações, consulte [cor do plano de fundo da célula no Ios](cell-background-color.md).
- Controlando quando a seleção de item [`DatePicker`](xref:Xamarin.Forms.DatePicker)ocorre em um. Para obter mais informações, consulte [DatePicker item selection on Ios](datepicker-selection.md).
- Garantir que o texto inserido caiba em um [`Entry`](xref:Xamarin.Forms.Entry) ajustando o tamanho da fonte. Para obter mais informações, consulte [tamanho da fonte de entrada em Ios](entry-font-size.md).
- Definindo a cor do cursor em [`Entry`](xref:Xamarin.Forms.Entry)um. Para obter mais informações, consulte [cor do cursor de entrada no Ios](entry-cursor-color.md).
- Controlando [`ListView`](xref:Xamarin.Forms.ListView) se as células de cabeçalho flutuam durante a rolagem. Para obter mais informações, consulte [estilo de cabeçalho do grupo ListView no Ios](listview-group-header-style.md).
- Controlando se as animações de linha são [`ListView`](xref:Xamarin.Forms.ListView) desabilitadas quando a coleção de itens está sendo atualizada. Para obter mais informações, consulte [animações de linha ListView no Ios](listview-row-animations.md).
- Definindo o estilo do separador em um [`ListView`](xref:Xamarin.Forms.ListView). Para obter mais informações, consulte [estilo do separador ListView no Ios](listview-separator-style.md).
- Controlando quando a seleção de item [`Picker`](xref:Xamarin.Forms.Picker)ocorre em um. Para obter mais informações, consulte [seleção de item do seletor no Ios](picker-selection.md).
- Controlando se [`SearchBar`](xref:Xamarin.Forms.SearchBar) um tem um plano de fundo. Para obter mais informações, consulte [estilo Searchbar no Ios](searchbar-style.md).
- Habilitando [`Slider.Value`](xref:Xamarin.Forms.Slider.Value) a propriedade a ser definida tocando em uma posição na [`Slider`](xref:Xamarin.Forms.Slider) barra, em vez de ter que arrastar a `Slider` miniatura. Para obter mais informações, consulte [controle deslizante do slider no Ios](slider-thumb.md).
- Controlando a transição que é usada ao abrir `SwipeView`um. Para obter mais informações, consulte [modo de transição de toque SwipeView](swipeview-swipetransitionmode.md).
- Controlando quando a seleção de item [`TimePicker`](xref:Xamarin.Forms.TimePicker)ocorre em um. Para obter mais informações, consulte [seleção de item de seletor de no Ios](timepicker-selection.md).

A seguinte funcionalidade específica de plataforma é fornecida para páginas Xamarin. Forms no iOS:

- Controlando se a página de detalhes [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) de um tem sombra aplicada a ele, ao revelar a página mestra. Para obter mais informações, consulte [sombra MasterDetailPage](masterdetailpage-shadow.md).
- Ocultando o separador de [`NavigationPage`](xref:Xamarin.Forms.NavigationPage)barra de navegação em um. Para obter mais informações, consulte [separador de barra NavigationPage no Ios](navigation-bar-separator.md).
- Controlando se a barra de navegação é translúcida. Para obter mais informações, consulte [barra de navegação Translucency no Ios](navigation-bar-translucent.md).
- Controlar se a cor do texto da barra de [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) status em a é ajustada para corresponder à luminosidade da barra de navegação. Para obter mais informações, consulte [modo de cor de texto da barra de NavigationPage no Ios](status-bar-text-color.md).
- Controlar se o título da página é exibido como um título grande na barra de navegação da página. Para obter mais informações, consulte [títulos de páginas grandes no Ios](page-large-title.md).
- Definindo a visibilidade do indicador inicial em um [`Page`](xref:Xamarin.Forms.Page). Para obter mais informações, consulte a [visibilidade do indicador inicial no Ios](page-home-indicator.md).
- Definindo a visibilidade da barra de status [`Page`](xref:Xamarin.Forms.Page)em um. Para obter mais informações, consulte [visibilidade da barra de status da página no Ios](page-status-bar-visibility.md).
- Garantir que o conteúdo da página seja posicionado em uma área da tela que seja segura para todos os dispositivos iOS. Para obter mais informações, consulte [Guia de layout de área segura no Ios](page-safe-area-layout.md).
- Definindo o estilo de apresentação das páginas modais. Para obter mais informações, consulte [estilo de apresentação de página modal](page-presentation-style.md).
- Definindo o modo de Translucency da barra de guias em [`TabbedPage`](xref:Xamarin.Forms.TabbedPage)um. Para obter mais informações, consulte [TabbedPage translúcida Tabbar no Ios](tabbedpage-translucent-tabbar.md).

A funcionalidade específica da plataforma a seguir é fornecida para layouts do Xamarin. Forms no iOS:

- Controlar se um [`ScrollView`](xref:Xamarin.Forms.ScrollView) gesto de toque é manipulado ou passado para seu conteúdo. Para obter mais informações, consulte [ScrollView Content Entoques on Ios](scrollview-content-touches.md).

A funcionalidade específica da plataforma a seguir é fornecida para a classe Xamarin [`Application`](xref:Xamarin.Forms.Application) . Forms no IOS:

- Desabilitando o dimensionamento de acessibilidade para tamanhos de fonte nomeados. Para obter mais informações, consulte [dimensionamento de acessibilidade para tamanhos de fonte nomeados no Ios](named-font-size-scaling.md).
- Habilitar o layout de controle e as atualizações de renderização a serem executadas no thread principal. Para obter mais informações, consulte [principais atualizações de controle de thread no Ios](main-thread-updates-ui.md).
- Habilitando [`PanGestureRecognizer`](xref:Xamarin.Forms.PanGestureRecognizer) um em uma exibição de rolagem para capturar e compartilhar o gesto de panorâmica com a exibição de rolagem. Para obter mais informações, consulte [reconhecimento de gesto de Pan simultânea no Ios](application-pan-gesture.md).

## <a name="ios-specific-formatting"></a>formatação específica do iOS

O Xamarin. Forms permite definir estilos e cores de interface do usuário de plataforma cruzada, mas há outras opções para definir o tema do seu iOS usando APIs de plataforma no projeto do iOS.

[Leia mais](formatting.md) sobre como formatar a interface do usuário usando APIs específicas do IOS, como a configuração **info. plist** e a `UIAppearance` API.

![](images/status-white-sml.png "iOS Theming")

## <a name="other-ios-features"></a>Outros recursos do iOS

Usando [renderizadores personalizados](~/xamarin-forms/app-fundamentals/custom-renderer/index.md), o [DependencyService](~/xamarin-forms/app-fundamentals/dependency-service/index.md)e o [MessagingCenter](~/xamarin-forms/app-fundamentals/messaging-center.md), é possível incorporar uma ampla variedade de funcionalidades nativas aos aplicativos Xamarin. Forms para Ios.
