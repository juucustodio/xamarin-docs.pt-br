---
title: recursos da plataforma iOS no Xamarin. Forms
description: Adicionar funcionalidade específica do iOS a aplicativos Xamarin. Forms.
ms.prod: xamarin
ms.assetid: 634AB62E-68C8-454C-838B-F1CC4E4E21BC
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/11/2019
ms.openlocfilehash: 5d0e289ddeb7eabef6d96c8882c772c704c54b34
ms.sourcegitcommit: d0e6436edbf7c52d760027d5e0ccaba2531d9fef
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75489720"
---
# <a name="ios-platform-features-in-xamarinforms"></a>recursos da plataforma iOS no Xamarin. Forms

O desenvolvimento de aplicativos Xamarin. Forms para iOS requer o Visual Studio. A [página requisitos](~/get-started/requirements.md) contém mais informações sobre os pré-requisitos.

## <a name="platform-specifics"></a>Especificações da plataforma

Especificidades da plataforma permitem que você consumir funcionalidade só está disponível em uma plataforma específica, sem implementar renderizadores personalizados ou efeitos.

A funcionalidade específica da plataforma a seguir é fornecida para exibições, páginas e layouts do Xamarin. Forms no iOS:

- Suporte para qualquer um de desfoque [ `VisualElement` ](xref:Xamarin.Forms.VisualElement). Para obter mais informações, consulte o [Desfoque do visualelement no Ios](visualelement-blur.md).
- Desabilitar o modo de cor herdados em um com suporte [ `VisualElement` ](xref:Xamarin.Forms.VisualElement). Para obter mais informações, consulte [modo de cor herdado do visualelement no Ios](legacy-color-mode.md).
- Habilitando uma sombra em um [ `VisualElement` ](xref:Xamarin.Forms.VisualElement). Para obter mais informações, consulte as [sombras de soltar do visualelement no Ios](visualelement-drop-shadow.md).

A seguinte funcionalidade específica da plataforma é fornecida para exibições do Xamarin. Forms no iOS:

- Definindo o [`Cell`](xref:Xamarin.Forms.Cell) cor do plano de fundo. Para obter mais informações, consulte [cor do plano de fundo da célula no Ios](cell-background-color.md).
- Garantir que inseridos texto se adapta a um [ `Entry` ](xref:Xamarin.Forms.Entry) ajustando o tamanho da fonte. Para obter mais informações, consulte [tamanho da fonte de entrada em Ios](entry-font-size.md).
- Configurando a cor do cursor uma [ `Entry` ](xref:Xamarin.Forms.Entry). Para obter mais informações, consulte [cor do cursor de entrada no Ios](entry-cursor-color.md).
- Controlando se [`ListView`](xref:Xamarin.Forms.ListView) células de cabeçalho flutuam durante a rolagem. Para obter mais informações, consulte [estilo de cabeçalho do grupo ListView no Ios](listview-group-header-style.md).
- Controlando se as animações de linha são desabilitadas quando a coleção de itens de [`ListView`](xref:Xamarin.Forms.ListView) está sendo atualizada. Para obter mais informações, consulte [animações de linha ListView no Ios](listview-row-animations.md).
- Definindo o estilo do separador em um [ `ListView` ](xref:Xamarin.Forms.ListView). Para obter mais informações, consulte [estilo do separador ListView no Ios](listview-separator-style.md).
- Controlando quando a seleção de item ocorre em um [ `Picker` ](xref:Xamarin.Forms.Picker). Para obter mais informações, consulte [seleção de item do seletor no Ios](picker-selection.md).
- Habilitando a [ `Slider.Value` ](xref:Xamarin.Forms.Slider.Value) propriedade a ser definido tocando em uma posição no [ `Slider` ](xref:Xamarin.Forms.Slider) barra, em vez de precisar arrastar o `Slider` thumb. Para obter mais informações, consulte [controle deslizante do slider no Ios](slider-thumb.md).
- Controlando a transição usada ao abrir um `SwipeView`. Para obter mais informações, consulte [modo de transição de toque SwipeView](swipeview-swipetransitionmode.md).

A seguinte funcionalidade específica de plataforma é fornecida para páginas Xamarin. Forms no iOS:

- Ocultando o separador de barra de navegação em um [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage). Para obter mais informações, consulte [separador de barra NavigationPage no Ios](navigation-bar-separator.md).
- Controlar se a barra de navegação é translúcida. Para obter mais informações, consulte [barra de navegação Translucency no Ios](navigation-bar-translucent.md).
- Controlar se o texto da barra de status de cor em um [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage) é ajustado para coincidir com a luminosidade da barra de navegação. Para obter mais informações, consulte [modo de cor de texto da barra de NavigationPage no Ios](status-bar-text-color.md).
- Controlar se o título da página é exibido como um título grande na barra de navegação de página. Para obter mais informações, consulte [títulos de páginas grandes no Ios](page-large-title.md).
- Definindo a visibilidade do indicador inicial em um [`Page`](xref:Xamarin.Forms.Page). Para obter mais informações, consulte a [visibilidade do indicador inicial no Ios](page-home-indicator.md).
- Definindo a visibilidade da barra de status em uma [ `Page` ](xref:Xamarin.Forms.Page). Para obter mais informações, consulte [visibilidade da barra de status da página no Ios](page-status-bar-visibility.md).
- Garantir que esse conteúdo da página está posicionado em uma área da tela que é segura para todos os dispositivos iOS. Para obter mais informações, consulte [Guia de layout de área segura no Ios](page-safe-area-layout.md).
- Definindo o estilo de apresentação das páginas modais. Para obter mais informações, consulte [estilo de apresentação de página modal](page-presentation-style.md).

A funcionalidade específica da plataforma a seguir é fornecida para layouts do Xamarin. Forms no iOS:

- Controlar se uma [ `ScrollView` ](xref:Xamarin.Forms.ScrollView) manipula um gesto de toque ou passá-lo ao seu conteúdo. Para obter mais informações, consulte [ScrollView Content Entoques on Ios](scrollview-content-touches.md).

A funcionalidade específica da plataforma a seguir é fornecida para a classe [`Application`](xref:Xamarin.Forms.Application) Xamarin. Forms no IOS:

- Desabilitando o dimensionamento de acessibilidade para tamanhos de fonte nomeados. Para obter mais informações, consulte [dimensionamento de acessibilidade para tamanhos de fonte nomeados no Ios](named-font-size-scaling.md).
- Habilitando o controle de layout e renderização atualizações a serem executadas no thread principal. Para obter mais informações, consulte [principais atualizações de controle de thread no Ios](main-thread-updates-ui.md).
- Habilitando um [ `PanGestureRecognizer` ](xref:Xamarin.Forms.PanGestureRecognizer) em uma exibição de rolagem para capturar e compartilhar o gesto de panorâmica com o modo de exibição de rolagem. Para obter mais informações, consulte [reconhecimento de gesto de Pan simultânea no Ios](application-pan-gesture.md).

## <a name="ios-specific-formatting"></a>formatação específica do iOS

O Xamarin. Forms permite definir estilos e cores de interface do usuário de plataforma cruzada, mas há outras opções para definir o tema do seu iOS usando APIs de plataforma no projeto do iOS.

[Leia mais](formatting.md) sobre como formatar a interface do usuário usando APIs específicas do IOS, como a configuração **info. plist** e a API `UIAppearance`.

![](images/status-white-sml.png "iOS Theming")

## <a name="other-ios-features"></a>Outros recursos do iOS

Usando [renderizadores personalizados](~/xamarin-forms/app-fundamentals/custom-renderer/index.md), o [DependencyService](~/xamarin-forms/app-fundamentals/dependency-service/index.md)e o [MessagingCenter](~/xamarin-forms/app-fundamentals/messaging-center.md), é possível incorporar uma ampla variedade de funcionalidades nativas aos aplicativos Xamarin. Forms para Ios.
