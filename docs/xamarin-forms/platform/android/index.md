---
title: Recursos da plataforma Android
description: Este artigo explica como adicionar uma funcionalidade específica do Android aos Xamarin.Forms aplicativos.
ms.prod: xamarin
ms.assetid: E24168F3-0138-4814-86EA-B467F6B8A545
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/11/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 76b0d9eff175755d6fc13178a864ae99b345efd3
ms.sourcegitcommit: 044e8d7e2e53f366942afe5084316198925f4b03
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/06/2021
ms.locfileid: "97940194"
---
# <a name="android-platform-features"></a>Recursos da plataforma Android

O desenvolvimento Xamarin.Forms de aplicativos para Android requer o Visual Studio. A [página plataformas com suporte](~/get-started/supported-platforms.md) contém mais informações sobre os pré-requisitos.

## <a name="platform-specifics"></a>Elementos específicos da plataforma

As especificações de plataforma permitem que você consuma a funcionalidade que só está disponível em uma plataforma específica, sem implementar renderizadores ou efeitos personalizados.

A funcionalidade específica da plataforma a seguir é fornecida para Xamarin.Forms exibições, páginas e layouts no Android:

- Controlando a ordem Z de elementos visuais para determinar a ordem de desenho. Para obter mais informações, consulte [visualelement elevação no Android](visualelement-elevation.md).
- Desabilitando o modo de cor herdado em um com suporte [`VisualElement`](xref:Xamarin.Forms.VisualElement) . Para obter mais informações, consulte [modo de cor herdado do visualelement no Android](legacy-color-mode.md).

A seguinte funcionalidade específica da plataforma é fornecida para Xamarin.Forms exibições no Android:

- Usando os valores de preenchimento e sombra padrão dos botões do Android. Para obter mais informações, consulte [preenchimento de botão e sombras no Android](button-padding-shadow.md).
- Definindo as opções do editor de método de entrada para o teclado soft para um [`Entry`](xref:Xamarin.Forms.Entry) . Para obter mais informações, consulte [Opções do editor de método de entrada de entrada no Android](entry-ime-options.md).
- Habilitando uma sombra projetada em um `ImageButton` . Para obter mais informações, consulte [ImageButton drop Shadows on Android](imagebutton-drop-shadow.md).
- Habilitando a rolagem rápida em um [`ListView`](xref:Xamarin.Forms.ListView) . Para obter mais informações, consulte a [rolagem rápida do ListView no Android](listview-fast-scrolling.md).
- Controlando a transição que é usada ao abrir um `SwipeView` . Para obter mais informações, consulte [modo de transição de toque SwipeView](swipeview-swipetransitionmode.md).
- Controlando se um [`WebView`](xref:Xamarin.Forms.WebView) pode exibir conteúdo misto. Para obter mais informações, consulte [conteúdo misto do WebView no Android](webview-mixed-content.md).
- Habilitando o zoom em um [`WebView`](xref:Xamarin.Forms.WebView) . Para obter mais informações, consulte o [zoom do WebView no Android](webview-zoom-controls.md).

A seguinte funcionalidade específica da plataforma é fornecida para Xamarin.Forms células no Android:

- Habilitação [`ViewCell`](xref:Xamarin.Forms.ViewCell) do modo herdado de ações de contexto, para que o menu ações de contexto não seja atualizado quando o item selecionado for [`ListView`](xref:Xamarin.Forms.ListView) alterado. Para obter mais informações, consulte [ações de contexto ViewCell no Android](viewcell-context-actions.md).

A seguinte funcionalidade específica da plataforma é fornecida para Xamarin.Forms páginas no Android:

- Definindo a altura da barra de navegação em um [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) . Para obter mais informações, consulte [altura da barra de NavigationPage no Android](navigationpage-bar-height.md).
- Desabilitar animações de transição ao navegar pelas páginas em um [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) . Para obter mais informações, consulte [animações de transição de página TabbedPage no Android](tabbedpage-transition-animations.md).
- Habilitando o passe do dedo entre as páginas em um [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) . Para obter mais informações, consulte [TabbedPageing Page Podedor no Android](tabbedpage-page-swiping.md).
- Definir o posicionamento e a cor da barra de ferramentas em um [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) . Para obter mais informações, consulte [posicionamento e cor da barra de ferramentas do TabbedPage no Android](tabbedpage-toolbar-placement-color.md).

A seguinte funcionalidade específica da plataforma é fornecida para a Xamarin.Forms [`Application`](xref:Xamarin.Forms.Application) classe no Android:

- Definindo o modo de operação de um teclado virtual. Para obter mais informações, consulte [modo de entrada de teclado virtual no Android](soft-keyboard-input-mode.md).
- Desabilitando os [`Disappearing`](xref:Xamarin.Forms.Page.Appearing) [`Appearing`](xref:Xamarin.Forms.Page.Appearing) eventos de ciclo de vida da página e em pausa e retomada, respectivamente, para aplicativos que usam AppCompat. Para obter mais informações, consulte [eventos de ciclo de vida da página no Android](page-lifecycle-events.md).

## <a name="platform-support"></a>Suporte a plataforma

Originalmente, o Xamarin.Forms projeto Android padrão usava um estilo mais antigo de renderização de controle que era comum antes do Android 5,0. Os aplicativos criados usando o modelo têm `FormsApplicationActivity` como a classe base de sua atividade principal.

## <a name="material-design-via-appcompat"></a>Design de material por AppCompat

Xamarin.Forms Os projetos do Android agora usam `FormsAppCompatActivity` como a classe base de sua atividade principal. Essa classe usa recursos de **AppCompat** fornecidos pelo Android para implementar temas de design de material.

Aqui está o exemplo de **todo** com o padrão `FormsApplicationActivity` :

[![Aplicativo de exemplo todo sem AppCompat](images/before-appcompat-sml.png)](images/before-appcompat.png#lightbox "Aplicativo de exemplo todo sem AppCompat")

E esse é o mesmo código depois de atualizar o projeto para usar `FormsAppCompatActivity` (e adicionar as informações de tema adicionais):

[![Aplicativo de exemplo todo com AppCompat e temas](images/post-appcompat-sml.png)](images/post-appcompat.png#lightbox "Aplicativo de exemplo todo com AppCompat e temas")

> [!NOTE]
> Ao usar `FormsAppCompatActivity` o, as [classes base para alguns renderizadores personalizados do Android](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md) serão diferentes.

## <a name="androidx-migration"></a>Migração AndroidX

AndroidX substitui a biblioteca de suporte do Android. Para saber mais sobre o AndroidX e como migrar um Xamarin.Forms aplicativo para usar bibliotecas do AndroidX, consulte [migração do AndroidX no Xamarin.Forms ](~/xamarin-forms/platform/android/androidx-migration.md).
