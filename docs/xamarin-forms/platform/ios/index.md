---
title: recursos da plataforma iOS no xamarin. Forms
description: Adicionando funcionalidade específica do iOS para aplicativos xamarin. Forms.
ms.prod: xamarin
ms.assetid: 634AB62E-68C8-454C-838B-F1CC4E4E21BC
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/22/2019
ms.openlocfilehash: 7aa9d298c2219dff60ca8ca1f917c72194ad21ca
ms.sourcegitcommit: 0fd04ea3af7d6a6d6086525306523a5296eec0df
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2019
ms.locfileid: "67512678"
---
# <a name="ios-platform-features-in-xamarinforms"></a>recursos da plataforma iOS no xamarin. Forms

Desenvolvimento de aplicativos xamarin. Forms para iOS requer o Visual Studio. O [página de requisitos](~/get-started/requirements.md) contém mais informações sobre os pré-requisitos.

## <a name="platform-specifics"></a>Especificidades da plataforma

Especificidades da plataforma permitem que você consumir funcionalidade só está disponível em uma plataforma específica, sem implementar renderizadores personalizados ou efeitos.

A seguinte funcionalidade específica da plataforma é fornecida para exibições do xamarin. Forms, páginas e layouts no iOS:

- Suporte para qualquer um de desfoque [ `VisualElement` ](xref:Xamarin.Forms.VisualElement). Para obter mais informações, consulte [VisualElement desfoque no iOS](visualelement-blur.md).
- Desabilitar o modo de cor herdados em um com suporte [ `VisualElement` ](xref:Xamarin.Forms.VisualElement). Para obter mais informações, consulte [modo de cor VisualElement herdado no iOS](legacy-color-mode.md).
- Habilitando uma sombra em um [ `VisualElement` ](xref:Xamarin.Forms.VisualElement). Para obter mais informações, consulte [VisualElement Drop sombras no iOS](visualelement-drop-shadow.md).

A seguinte funcionalidade específica da plataforma é fornecida para modos de exibição do xamarin. Forms no iOS:

- Definindo o [ `Cell` ](xref:Xamarin.Forms.Cell) cor do plano de fundo. Para obter mais informações, consulte [cor de plano de fundo da célula no iOS](cell-background-color.md).
- Garantir que inseridos texto se adapta a um [ `Entry` ](xref:Xamarin.Forms.Entry) ajustando o tamanho da fonte. Para obter mais informações, consulte [tamanho da fonte de entrada no iOS](entry-font-size.md).
- Configurando a cor do cursor uma [ `Entry` ](xref:Xamarin.Forms.Entry). Para obter mais informações, consulte [entrada de cor do Cursor no iOS](entry-cursor-color.md).
- Controlando se [ `ListView` ](xref:Xamarin.Forms.ListView) células de cabeçalho float durante a rolagem. Para obter mais informações, consulte [ListView estilo do cabeçalho de grupo no iOS](listview-group-header-style.md).
- Controlar se as animações de linha estão desabilitadas quando o [ `ListView` ](xref:Xamarin.Forms.ListView) coleção de itens está sendo atualizada. Para obter mais informações, consulte [ListView linha animações no iOS](listview-row-animations.md).
- Definindo o estilo do separador em um [ `ListView` ](xref:Xamarin.Forms.ListView). Para obter mais informações, consulte [ListView estilo do separador no iOS](listview-separator-style.md).
- Controlando quando a seleção de item ocorre em um [ `Picker` ](xref:Xamarin.Forms.Picker). Para obter mais informações, consulte [seletor de seleção de Item no iOS](picker-selection.md).
- Habilitando a [ `Slider.Value` ](xref:Xamarin.Forms.Slider.Value) propriedade a ser definido tocando em uma posição no [ `Slider` ](xref:Xamarin.Forms.Slider) barra, em vez de precisar arrastar o `Slider` thumb. Para obter mais informações, consulte [controle deslizante Thumb toque no iOS](slider-thumb.md).

A seguinte funcionalidade específica da plataforma é fornecida para páginas do xamarin. Forms no iOS:

- Ocultando o separador de barra de navegação em um [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage). Para obter mais informações, consulte [NavigationPage separador de barra no iOS](navigation-bar-separator.md).
- Controlar se a barra de navegação é translúcida. Para obter mais informações, consulte [Translucência da barra de navegação no iOS](navigation-bar-translucent.md).
- Controlar se o texto da barra de status de cor em um [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage) é ajustado para coincidir com a luminosidade da barra de navegação. Para obter mais informações, consulte [modo de cor de texto NavigationPage barra no iOS](status-bar-text-color.md).
- Controlar se o título da página é exibido como um título grande na barra de navegação de página. Para obter mais informações, consulte [títulos de página grande no iOS](page-large-title.md).
- Definir a visibilidade do indicador inicial em um [ `Page` ](xref:Xamarin.Forms.Page). Para obter mais informações, consulte [página inicial de indicador de visibilidade no iOS](page-home-indicator.md).
- Definindo a visibilidade da barra de status em uma [ `Page` ](xref:Xamarin.Forms.Page). Para obter mais informações, consulte [página de visibilidade da barra de Status no iOS](page-status-bar-visibility.md).
- Garantir que esse conteúdo da página está posicionado em uma área da tela que é segura para todos os dispositivos iOS. Para obter mais informações, consulte [guia do Layout da área de segurança no iOS](page-safe-area-layout.md).
- Definindo o estilo de apresentação de páginas modais em um iPad. Para obter mais informações, consulte [iPad estilo de apresentação de página Modal](ipad-page-presentation-style.md).

A seguinte funcionalidade específica da plataforma é fornecida para layouts do xamarin. Forms no iOS:

- Controlar se uma [ `ScrollView` ](xref:Xamarin.Forms.ScrollView) manipula um gesto de toque ou passá-lo ao seu conteúdo. Para obter mais informações, consulte [ScrollView toques de conteúdo no iOS](scrollview-content-touches.md).

A seguinte funcionalidade específica da plataforma é fornecida para o xamarin. Forms [ `Application` ](xref:Xamarin.Forms.Application) classe no iOS:

- Desabilitando o dimensionamento para tamanhos de fonte nomeada de acessibilidade. Para obter mais informações, consulte [acessibilidade colocação em escala para tamanhos de fonte chamado no iOS](named-font-size-scaling.md).
- Habilitando o controle de layout e renderização atualizações a serem executadas no thread principal. Para obter mais informações, consulte [atualizações de controle do Thread principal no iOS](main-thread-updates-ui.md).
- Habilitando um [ `PanGestureRecognizer` ](xref:Xamarin.Forms.PanGestureRecognizer) em uma exibição de rolagem para capturar e compartilhar o gesto de panorâmica com o modo de exibição de rolagem. Para obter mais informações, consulte [simultâneas reconhecimento de gesto de panorâmica no iOS](application-pan-gesture.md).

## <a name="ios-specific-formatting"></a>formatação específica do iOS

Xamarin. Forms permite que os estilos de interface do usuário de plataforma cruzada e cores a ser definida - mas há outras opções para definir o tema do iOS usando as APIs da plataforma no projeto do iOS.

[Leia mais](formatting.md) sobre a formatação de interface do usuário usando as APIs do iOS específicas, como **Info. plist** configuração e o `UIAppearance` API.

![](images/status-white-sml.png "Temas do iOS")

## <a name="other-ios-features"></a>Outros recursos do iOS

Usando o [renderizadores personalizados](~/xamarin-forms/app-fundamentals/custom-renderer/index.md), o [DependencyService](~/xamarin-forms/app-fundamentals/dependency-service/index.md)e o [MessagingCenter](~/xamarin-forms/app-fundamentals/messaging-center.md), é possível incorporar uma ampla variedade de funcionalidade nativa em Aplicativos xamarin. Forms para iOS.
