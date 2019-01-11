---
title: Recursos da plataforma Android
description: Este artigo explica como adicionar funcionalidade específica do Android para aplicativos xamarin. Forms.
ms.prod: xamarin
ms.assetid: E24168F3-0138-4814-86EA-B467F6B8A545
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/07/2018
ms.openlocfilehash: 72a55441658755c1e068b33f910f3b7c0db5502f
ms.sourcegitcommit: 395774577f7524b57035c5cca3c9034a4b636489
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/10/2019
ms.locfileid: "54207772"
---
# <a name="android-platform-features"></a>Recursos da plataforma Android

Desenvolvimento de aplicativos xamarin. Forms para Android requer o Visual Studio. O [página de requisitos](~/xamarin-forms/get-started/installation.md) contém mais informações sobre os pré-requisitos.

## <a name="platform-specifics"></a>Especificidades da plataforma

Especificidades da plataforma permitem que você consumir funcionalidade só está disponível em uma plataforma específica, sem implementar renderizadores personalizados ou efeitos.

A seguinte funcionalidade específica da plataforma é fornecida para modos de exibição, páginas e layouts no Android do xamarin. Forms:

- Controlando a ordem Z dos elementos visuais para determinar a ordem de desenho. Para obter mais informações, consulte [VisualElement elevação no Android](visualelement-elevation.md).
- Desabilitar o modo de cor herdados em um com suporte [ `VisualElement` ](xref:Xamarin.Forms.VisualElement). Para obter mais informações, consulte [modo de cor VisualElement herdado no Android](legacy-color-mode.md).

A seguinte funcionalidade específica da plataforma é fornecida para modos de exibição do xamarin. Forms no Android:

- Usando o preenchimento padrão e os valores de sombra dos botões do Android. Para obter mais informações, consulte [sombras no Android e o preenchimento do botão](button-padding-shadow.md).
- Configurando o método de entrada opções do editor para o teclado virtual para um [ `Entry` ](xref:Xamarin.Forms.Entry). Para obter mais informações, consulte [opções do Editor de método de entrada de entrada no Android](entry-ime-options.md).
- Habilitando uma sombra em um `ImageButton`. Para obter mais informações, consulte [ImageButton Drop sombras no Android](imagebutton-drop-shadow.md).
- Habilitar a rolagem rápida em um [ `ListView` ](xref:Xamarin.Forms.ListView) para obter mais informações, consulte [ListView rolagem rápida no Android](listview-fast-scrolling.md).
- Controlar se uma [ `WebView` ](xref:Xamarin.Forms.WebView) pode exibir o conteúdo misto. Para obter mais informações, consulte [WebView misto conteúdo no Android](webview-mixed-content.md).

A seguinte funcionalidade específica da plataforma é fornecida para páginas do xamarin. Forms no Android:

- Definir a altura da barra de navegação em um [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage). Para obter mais informações, consulte [NavigationPage altura da barra no Android](navigationpage-bar-height.md).
- Desabilitar animações de transição ao navegar entre páginas em um [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage). Para obter mais informações, consulte [animações de transição de página TabbedPage no Android](tabbedpage-transition-animations.md).
- Habilitando o dedo entre páginas em um [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage). Para obter mais informações, consulte [TabbedPage página passar o dedo no Android](tabbedpage-page-swiping.md).
- Definir o posicionamento da barra de ferramentas e a cor em um [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage). Para obter mais informações, consulte [TabbedPage posicionamento de barra de ferramentas e a cor no Android](tabbedpage-toolbar-placement-color.md).

A seguinte funcionalidade específica da plataforma é fornecida para o xamarin. Forms [ `Application` ](xref:Xamarin.Forms.Application) classe no Android:

- Definindo o modo de operação de um teclado virtual. Para obter mais informações, consulte [reversível modo de entrada de teclado no Android](soft-keyboard-input-mode.md).
- Desabilitando o [ `Disappearing` ](xref:Xamarin.Forms.Page.Appearing) e [ `Appearing` ](xref:Xamarin.Forms.Page.Appearing) página eventos de ciclo de vida em pausa e retomar, respectivamente, para aplicativos que usam AppCompat. Para obter mais informações, consulte [eventos de ciclo de vida da página no Android](page-lifecycle-events.md).

## <a name="platform-support"></a>Suporte de plataforma

Originalmente, o projeto do Android do xamarin. Forms padrão usado um estilo mais antigo de renderização de controle do que era comum antes do Android 5.0. Aplicativos criados usando o modelo tem `FormsApplicationActivity` como a classe base da sua atividade principal.

## <a name="material-design-via-appcompat"></a>Design de material via AppCompat

Projetos do Android do xamarin. Forms enquanto, use `FormsAppCompatActivity` como a classe base da sua atividade principal. Essa classe usa **AppCompat** recursos fornecidos pelo Android para implementar os temas de Design de Material.

Para adicionar os temas de Design de Material ao seu projeto do Android do xamarin. Forms, execute o [dar suporte a instruções de instalação para AppCompat](appcompat-material-design.md)

Aqui está o **Todo** exemplo com o padrão `FormsApplicationActivity`:

[![](images/before-appcompat-sml.png "Aplicativo de exemplo de tarefas pendentes sem AppCompat")](images/before-appcompat.png#lightbox "aplicativo de exemplo de tarefas pendentes sem AppCompat")

E isso é o mesmo código depois de atualizar o projeto para usar `FormsAppCompatActivity` (e adicionar as informações de tema adicionais):

[![](images/post-appcompat-sml.png "Aplicativo de exemplo de tarefas pendentes com AppCompat e temas")](images/post-appcompat.png#lightbox "aplicativo de exemplo de tarefas pendentes com AppCompat e temas")

> [!NOTE]
> Ao usar `FormsAppCompatActivity`, o [classes de base para alguns renderizadores personalizados Android](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md) será diferente.

## <a name="related-links"></a>Links relacionados

- [Adicionar suporte de Design de Material](appcompat-material-design.md)
