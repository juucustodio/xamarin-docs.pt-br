---
title: Recursos da plataforma Windows
description: Este artigo explica o suporte à plataforma Windows que está disponível no Xamarin. Forms.
ms.prod: xamarin
ms.assetid: F6EA9E49-FB3E-442F-AF13-B7AD0C80D11F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/20/2019
ms.openlocfilehash: 0e2db2a054c871668b5787a53ffbe4464f982174
ms.sourcegitcommit: 21d8be9571a2fa89fb7d8ff0787ff4f957de0985
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/21/2019
ms.locfileid: "72696927"
---
# <a name="windows-platform-features"></a>Recursos da plataforma Windows

O desenvolvimento de aplicativos Xamarin. Forms para plataformas Windows requer o Visual Studio. A [página requisitos](~/get-started/requirements.md) contém mais informações sobre os pré-requisitos.

![](images/allhanselman.png "Xamarin.Forms Applications Running on Windows")

## <a name="platform-specifics"></a>Especificações da plataforma

As especificações de plataforma permitem que você consuma a funcionalidade que só está disponível em uma plataforma específica, sem implementar renderizadores ou efeitos personalizados.

A funcionalidade específica da plataforma a seguir é fornecida para exibições, páginas e layouts do Xamarin. Forms no Plataforma Universal do Windows (UWP):

- Definindo uma chave de acesso para um [`VisualElement`](xref:Xamarin.Forms.VisualElement). Para obter mais informações, consulte [as chaves de acesso do visualelement no Windows](visualelement-access-keys.md).
- Desabilitando o modo de cor herdado em um [`VisualElement`](xref:Xamarin.Forms.VisualElement)com suporte. Para obter mais informações, consulte [modo de cor herdado do visualelement no Windows](legacy-color-mode.md).

A seguinte funcionalidade específica da plataforma é fornecida para exibições do Xamarin. Forms no UWP:

- Detectando a ordem de leitura do conteúdo de texto nas instâncias [`Entry`](xref:Xamarin.Forms.Entry), [`Editor`](xref:Xamarin.Forms.Editor)e [`Label`](xref:Xamarin.Forms.Label) . Para obter mais informações, consulte [ordem de leitura do InputView no Windows](inputview-reading-order.md).
- Habilitando o suporte ao gesto de toque em um [`ListView`](xref:Xamarin.Forms.ListView). Para obter mais informações, consulte [ListView SelectionMode no Windows](listview-selectionmode.md).
- Habilitando a direção de pull de um `RefreshView` a ser alterado. Para obter mais informações, consulte [direção de pull do RefreshView no Windows](refreshview-pulldirection.md).
- Habilitar um [`SearchBar`](xref:Xamarin.Forms.SearchBar) para interagir com o mecanismo de verificação ortográfica. Para obter mais informações, consulte [SearchBar verificação ortográfica no Windows](searchbar-spell-check.md).
- Habilitar um [`WebView`](xref:Xamarin.Forms.WebView) para exibir alertas JavaScript em uma caixa de diálogo de mensagem UWP. Para obter mais informações, consulte [alertas de JavaScript do WebView no Windows](webview-javascript-alert.md).

A seguinte funcionalidade específica de plataforma é fornecida para páginas Xamarin. Forms no UWP:

- Recolhendo a barra de navegação [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) . Para obter mais informações, consulte [barra de navegação do MasterDetailPage no Windows](masterdetailpage-navigation-bar.md).
- Definindo opções de posicionamento da barra de ferramentas. Para obter mais informações, consulte [posicionamento da barra de ferramentas da página no Windows](page-toolbar-placement.md).
- Habilitando ícones de página a serem exibidos em uma barra de ferramentas [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) . Para obter mais informações, consulte [ícones de TabbedPage no Windows](tabbedpage-icons.md).

## <a name="platform-support"></a>Suporte de plataforma

Os modelos do Xamarin. Forms disponíveis no Visual Studio contêm um projeto Plataforma Universal do Windows (UWP).

> [!NOTE]
> O Xamarin. Forms 1. x e 2. x suporta _Windows Phone 8 Silverlight_, _Windows Phone 8,1_e desenvolvimento de aplicativos _Windows 8.1_ . No entanto, esses tipos de projeto foram preteridos.

## <a name="getting-started"></a>Introdução

Vá para **arquivo > novo projeto de >** no Visual Studio e escolha um dos modelos de **aplicativo > em branco entre plataformas (Xamarin. Forms)** para começar.

As soluções anteriores do Xamarin. Forms, ou aquelas criadas no macOS, não terão todos os projetos do Windows listados acima (mas precisam ser adicionadas manualmente). Se a plataforma Windows que você deseja direcionar ainda não estiver em sua solução, visite as [instruções de instalação](installation/index.md) para adicionar o tipo/s de projeto do Windows desejado.

## <a name="samples"></a>Exemplos

[Todos os exemplos](https://github.com/xamarin/xamarin-forms-book-preview-2) do livro de Charles Petzold [*criando aplicativos móveis com Xamarin. Forms*](~/xamarin-forms/creating-mobile-apps-xamarin-forms/index.md) incluem projetos plataforma universal do Windows (para Windows 10).

O [aplicativo de demonstração "Scott Hanselman"](https://github.com/jamesmontemagno/Hanselman.Forms) está disponível separadamente e também inclui projetos de desgaste de Apple Watch e Android (usando Xamarin. Ios e Xamarin. Android, respectivamente, o Xamarin. Forms não é executado nessas plataformas).

## <a name="related-links"></a>Links relacionados

- [Configurar projetos do Windows](~/xamarin-forms/platform/windows/installation/index.md)
