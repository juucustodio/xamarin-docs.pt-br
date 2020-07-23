---
title: Recursos da plataforma Windows
description: Este artigo explica o suporte à plataforma Windows que está disponível no Xamarin.Forms .
ms.prod: xamarin
ms.assetid: F6EA9E49-FB3E-442F-AF13-B7AD0C80D11F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/16/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 0cdbd39f0217b0e4d175eb2443250eb3df8dd046
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/22/2020
ms.locfileid: "86936169"
---
# <a name="windows-platform-features"></a>Recursos da plataforma Windows

O desenvolvimento Xamarin.Forms de aplicativos para plataformas Windows requer o Visual Studio. A [página plataformas com suporte](~/get-started/supported-platforms.md) contém mais informações sobre os pré-requisitos.

![Xamarin.FormsAplicativos em execução no Windows](images/allhanselman.png)

## <a name="platform-specifics"></a>Elementos específicos da plataforma

As especificações de plataforma permitem que você consuma a funcionalidade que só está disponível em uma plataforma específica, sem implementar renderizadores ou efeitos personalizados.

A seguinte funcionalidade específica da plataforma é fornecida para Xamarin.Forms exibições, páginas e layouts no plataforma universal do Windows (UWP):

- Definindo uma chave de acesso para um [`VisualElement`](xref:Xamarin.Forms.VisualElement) . Para obter mais informações, consulte [as chaves de acesso do visualelement no Windows](visualelement-access-keys.md).
- Desabilitando o modo de cor herdado em um com suporte [`VisualElement`](xref:Xamarin.Forms.VisualElement) . Para obter mais informações, consulte [modo de cor herdado do visualelement no Windows](legacy-color-mode.md).

A seguinte funcionalidade específica da plataforma é fornecida para Xamarin.Forms exibições no UWP:

- Detectando a ordem de leitura do conteúdo de texto nas [`Entry`](xref:Xamarin.Forms.Entry) [`Editor`](xref:Xamarin.Forms.Editor) instâncias, e [`Label`](xref:Xamarin.Forms.Label) . Para obter mais informações, consulte [ordem de leitura do InputView no Windows](inputview-reading-order.md).
- Habilitando o suporte ao gesto de toque em um [`ListView`](xref:Xamarin.Forms.ListView) . Para obter mais informações, consulte [ListView SelectionMode no Windows](listview-selectionmode.md).
- Habilitando a direção de pull de um `RefreshView` a ser alterado. Para obter mais informações, consulte [direção de pull do RefreshView no Windows](refreshview-pulldirection.md).
- Habilitar um [`SearchBar`](xref:Xamarin.Forms.SearchBar) para interagir com o mecanismo de verificação ortográfica. Para obter mais informações, consulte [SearchBar verificação ortográfica no Windows](searchbar-spell-check.md).
- Habilitar um [`WebView`](xref:Xamarin.Forms.WebView) para exibir alertas JavaScript em uma caixa de diálogo de mensagem UWP. Para obter mais informações, consulte [alertas de JavaScript do WebView no Windows](webview-javascript-alert.md).

A seguinte funcionalidade específica da plataforma é fornecida para Xamarin.Forms páginas no UWP:

- Recolhendo a [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) barra de navegação. Para obter mais informações, consulte [barra de navegação do MasterDetailPage no Windows](masterdetailpage-navigation-bar.md).
- Definindo opções de posicionamento da barra de ferramentas. Para obter mais informações, consulte [posicionamento da barra de ferramentas da página no Windows](page-toolbar-placement.md).
- Habilitando ícones de página a serem exibidos em uma [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) barra de ferramentas. Para obter mais informações, consulte [ícones de TabbedPage no Windows](tabbedpage-icons.md).

A seguinte funcionalidade específica da plataforma é fornecida para a Xamarin.Forms [`Application`](xref:Xamarin.Forms.Application) classe no UWP:

- Especificar o diretório no projeto do qual os ativos de imagem serão carregados. Para obter mais informações, consulte [diretório de imagem padrão no Windows](default-image-directory.md).

## <a name="platform-support"></a>Suporte a plataforma

Os Xamarin.Forms modelos disponíveis no Visual Studio contêm um projeto plataforma universal do Windows (UWP).

> [!NOTE]
> Xamarin.Formssuporte a 1. x e 2. x _Windows Phone 8 Silverlight_, _Windows Phone 8,1_e desenvolvimento de aplicativos _Windows 8.1_ . No entanto, esses tipos de projeto foram preteridos.

## <a name="getting-started"></a>Introdução

Vá para **arquivo > novo projeto de >** no Visual Studio e escolha um dos modelos de **aplicativo > em branco entre Xamarin.Forms plataformas** para começar.

Xamarin.FormsAs soluções mais antigas, ou aquelas criadas no MacOS, não terão todos os projetos do Windows listados acima (mas precisam ser adicionadas manualmente). Se a plataforma Windows que você deseja direcionar ainda não estiver em sua solução, visite as [instruções de instalação](installation/index.md) para adicionar o tipo/s de projeto do Windows desejado.

## <a name="samples"></a>Amostras

[Todos os exemplos](https://github.com/xamarin/xamarin-forms-book-preview-2) do livro de Charles Petzold [*criando aplicativos móveis com Xamarin.Forms *](~/xamarin-forms/creating-mobile-apps-xamarin-forms/index.md) os projetos include plataforma universal do Windows (para Windows 10).

O [aplicativo de demonstração "Scott Hanselman"](https://github.com/jamesmontemagno/Hanselman.Forms) está disponível separadamente e também inclui projetos de desgaste de Apple Watch e Android (usando Xamarin. Ios e Xamarin. Android, respectivamente, Xamarin.Forms não é executado nessas plataformas).

## <a name="related-links"></a>Links relacionados

- [Configurar projetos do Windows](~/xamarin-forms/platform/windows/installation/index.md)
