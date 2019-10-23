---
title: Recursos da plataforma Android
description: Este artigo explica como adicionar uma funcionalidade específica do Android aos aplicativos Xamarin. Forms.
ms.prod: xamarin
ms.assetid: E24168F3-0138-4814-86EA-B467F6B8A545
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/24/2019
ms.openlocfilehash: 73e838b3a63132230cf594a3461c9d7ee6f302b8
ms.sourcegitcommit: 21d8be9571a2fa89fb7d8ff0787ff4f957de0985
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/21/2019
ms.locfileid: "72696946"
---
# <a name="android-platform-features"></a>Recursos da plataforma Android

O desenvolvimento de aplicativos Xamarin. Forms para Android requer o Visual Studio. A [página requisitos](~/get-started/requirements.md) contém mais informações sobre os pré-requisitos.

## <a name="platform-specifics"></a>Especificações da plataforma

As especificações de plataforma permitem que você consuma a funcionalidade que só está disponível em uma plataforma específica, sem implementar renderizadores ou efeitos personalizados.

A funcionalidade específica da plataforma a seguir é fornecida para exibições, páginas e layouts do Xamarin. Forms no Android:

- Controlando a ordem Z de elementos visuais para determinar a ordem de desenho. Para obter mais informações, consulte [visualelement elevação no Android](visualelement-elevation.md).
- Desabilitando o modo de cor herdado em um [`VisualElement`](xref:Xamarin.Forms.VisualElement)com suporte. Para obter mais informações, consulte [modo de cor herdado do visualelement no Android](legacy-color-mode.md).

A funcionalidade específica da plataforma a seguir é fornecida para exibições do Xamarin. Forms no Android:

- Usando os valores de preenchimento e sombra padrão dos botões do Android. Para obter mais informações, consulte [preenchimento de botão e sombras no Android](button-padding-shadow.md).
- Definindo as opções do editor de método de entrada para o teclado soft de um [`Entry`](xref:Xamarin.Forms.Entry). Para obter mais informações, consulte [Opções do editor de método de entrada de entrada no Android](entry-ime-options.md).
- Habilitando uma sombra suspensa em um `ImageButton`. Para obter mais informações, consulte [ImageButton drop Shadows on Android](imagebutton-drop-shadow.md).
- Habilitando a rolagem rápida em um [`ListView`](xref:Xamarin.Forms.ListView) para obter mais informações, consulte a [rolagem rápida do ListView no Android](listview-fast-scrolling.md).
- Controlando se um [`WebView`](xref:Xamarin.Forms.WebView) pode exibir conteúdo misto. Para obter mais informações, consulte [conteúdo misto do WebView no Android](webview-mixed-content.md).
- Habilitar o zoom em um [`WebView`](xref:Xamarin.Forms.WebView). Para obter mais informações, consulte o [zoom do WebView no Android](webview-zoom-controls.md).

A seguinte funcionalidade específica de plataforma é fornecida para as células Xamarin. Forms no Android:

- Habilitar [`ViewCell`](xref:Xamarin.Forms.ViewCell) modo herdado de ações de contexto, para que o menu ações de contexto não seja atualizado quando o item selecionado em um [`ListView`](xref:Xamarin.Forms.ListView) for alterado. Para obter mais informações, consulte [ações de contexto ViewCell no Android](viewcell-context-actions.md).

A seguinte funcionalidade específica de plataforma é fornecida para páginas Xamarin. Forms no Android:

- Definindo a altura da barra de navegação em um [`NavigationPage`](xref:Xamarin.Forms.NavigationPage). Para obter mais informações, consulte [altura da barra de NavigationPage no Android](navigationpage-bar-height.md).
- Desabilitar animações de transição ao navegar pelas páginas de um [`TabbedPage`](xref:Xamarin.Forms.TabbedPage). Para obter mais informações, consulte [animações de transição de página TabbedPage no Android](tabbedpage-transition-animations.md).
- Habilitando o passar o dedo entre páginas em um [`TabbedPage`](xref:Xamarin.Forms.TabbedPage). Para obter mais informações, consulte [TabbedPageing Page Podedor no Android](tabbedpage-page-swiping.md).
- Definir o posicionamento e a cor da barra de ferramentas em um [`TabbedPage`](xref:Xamarin.Forms.TabbedPage). Para obter mais informações, consulte [posicionamento e cor da barra de ferramentas do TabbedPage no Android](tabbedpage-toolbar-placement-color.md).

A funcionalidade específica da plataforma a seguir é fornecida para a classe [`Application`](xref:Xamarin.Forms.Application) do Xamarin. Forms no Android:

- Definindo o modo de operação de um teclado virtual. Para obter mais informações, consulte [modo de entrada de teclado virtual no Android](soft-keyboard-input-mode.md).
- Desabilitar o [`Disappearing`](xref:Xamarin.Forms.Page.Appearing) e [`Appearing`](xref:Xamarin.Forms.Page.Appearing) eventos de ciclo de vida da página em pausa e retomada, respectivamente, para aplicativos que usam AppCompat. Para obter mais informações, consulte [eventos de ciclo de vida da página no Android](page-lifecycle-events.md).

## <a name="platform-support"></a>Suporte de plataforma

Originalmente, o projeto Android padrão do Xamarin. Forms usava um estilo mais antigo de renderização de controle que era comum antes do Android 5,0. Os aplicativos criados usando o modelo têm `FormsApplicationActivity` como a classe base de sua atividade principal.

## <a name="material-design-via-appcompat"></a>Design de material por AppCompat

Os projetos Android do Xamarin. Forms agora usam `FormsAppCompatActivity` como a classe base de sua atividade principal. Essa classe usa recursos de **AppCompat** fornecidos pelo Android para implementar temas de design de material.

Para adicionar temas de design de material ao seu projeto do Xamarin. Forms Android, siga as [instruções de instalação para suporte a AppCompat](appcompat-material-design.md)

Aqui está o exemplo de **todo** com o `FormsApplicationActivity` padrão:

[![](images/before-appcompat-sml.png "Todo Sample Application Without AppCompat")](images/before-appcompat.png#lightbox "Todo Sample Application Without AppCompat")

E esse é o mesmo código depois de atualizar o projeto para usar `FormsAppCompatActivity` (e adicionar as informações de tema adicionais):

[![](images/post-appcompat-sml.png "Todo Sample Application With AppCompat and Theming")](images/post-appcompat.png#lightbox "Todo Sample Application With AppCompat and Theming")

> [!NOTE]
> Ao usar `FormsAppCompatActivity`, as [classes base para alguns renderizadores personalizados do Android](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md) serão diferentes.

## <a name="related-links"></a>Links relacionados

- [Adicionar suporte ao design de material](appcompat-material-design.md)
