---
title: Recursos da plataforma Windows
description: Este artigo explica o suporte da plataforma Windows que está disponível no xamarin. Forms.
ms.prod: xamarin
ms.assetid: F6EA9E49-FB3E-442F-AF13-B7AD0C80D11F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/08/2018
ms.openlocfilehash: 9367e22ede733ee2d93feccc001836fb4dc02564
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60858077"
---
# <a name="windows-platform-features"></a>Recursos da plataforma Windows

Desenvolvimento de aplicativos xamarin. Forms para plataformas do Windows requer o Visual Studio. O [página de requisitos](~/get-started/requirements.md) contém mais informações sobre os pré-requisitos.

![](images/allhanselman.png "Aplicativos xamarin. Forms em execução no Windows")

## <a name="platform-specifics"></a>Especificidades da plataforma

Especificidades da plataforma permitem que você consumir funcionalidade só está disponível em uma plataforma específica, sem implementar renderizadores personalizados ou efeitos.

A seguinte funcionalidade específica da plataforma é fornecida para exibições do xamarin. Forms, páginas e layouts no Universal Windows Platform (UWP):

- Definindo uma tecla de acesso para um [ `VisualElement` ](xref:Xamarin.Forms.VisualElement). Para obter mais informações, consulte [VisualElement chaves de acesso no Windows](visualelement-access-keys.md).
- Desabilitar o modo de cor herdados em um com suporte [ `VisualElement` ](xref:Xamarin.Forms.VisualElement). Para obter mais informações, consulte [modo de cor VisualElement herdado no Windows](legacy-color-mode.md).

A seguinte funcionalidade específica da plataforma é fornecida para modos de exibição do xamarin. Forms na UWP:

- Detectando a ordem de leitura do conteúdo de texto [ `Entry` ](xref:Xamarin.Forms.Entry), [ `Editor` ](xref:Xamarin.Forms.Editor), e [ `Label` ](xref:Xamarin.Forms.Label) instâncias. Para obter mais informações, consulte [InputView a ordem de leitura no Windows](inputview-reading-order.md).
- Habilitando o suporte de gestos de toque em um [ `ListView` ](xref:Xamarin.Forms.ListView). Para obter mais informações, consulte [ListView SelectionMode no Windows](listview-selectionmode.md).
- Habilitando um [ `SearchBar` ](xref:Xamarin.Forms.SearchBar) para interagir com o mecanismo de verificação ortográfica. Para obter mais informações, consulte [SearchBar de verificação ortográfica no Windows](searchbar-spell-check.md).
- Habilitando um [ `WebView` ](xref:Xamarin.Forms.WebView) para exibir alertas de JavaScript em uma caixa de diálogo de mensagem UWP. Para obter mais informações, consulte [alertas de JavaScript do WebView no Windows](webview-javascript-alert.md).

A seguinte funcionalidade específica da plataforma é fornecida para páginas do xamarin. Forms na UWP:

- Recolher o [ `MasterDetailPage` ](xref:Xamarin.Forms.MasterDetailPage) barra de navegação. Para obter mais informações, consulte [MasterDetailPage barra de navegação no Windows](masterdetailpage-navigation-bar.md).
- Definindo opções de posicionamento da barra de ferramentas. Para obter mais informações, consulte [posicionamento da barra de ferramentas de página no Windows](page-toolbar-placement.md).
- Habilitar ícones de página a ser exibido em uma [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage) barra de ferramentas. Para obter mais informações, consulte [TabbedPage ícones no Windows](tabbedpage-icons.md).

## <a name="platform-support"></a>Suporte de plataforma

Os modelos do xamarin. Forms disponíveis no Visual Studio contêm um projeto de plataforma Universal do Windows (UWP).

> [!NOTE]
> Suporte de xamarin. Forms 1.x e 2.x _Windows Phone 8 Silverlight_, _Windows Phone 8.1_, e _Windows 8.1_ desenvolvimento de aplicativos. No entanto, esses tipos de projeto foram preteridos.

## <a name="getting-started"></a>Introdução

Vá para **arquivo > Novo > projeto** no Visual Studio e escolha uma da **plataforma cruzada > aplicativo em branco (xamarin. Forms)** modelos para começar.

Soluções antigas do xamarin. Forms, ou aqueles criados no macOS, não terá todos os projetos do Windows listados acima (mas eles precisam ser adicionados manualmente). Se a plataforma do Windows que você deseja direcionar ainda não estiver em sua solução, visite o [instruções de instalação](installation/index.md) para adicionar os Windows desejados tipo/s do projeto.

## <a name="samples"></a>Exemplos

[Todos os exemplos](https://github.com/xamarin/xamarin-forms-book-preview-2) para livro Charles Petzold [ *criação de aplicativos móveis com xamarin. Forms* ](~/xamarin-forms/creating-mobile-apps-xamarin-forms/index.md) incluir projetos de plataforma Universal do Windows (para Windows 10).

O [aplicativo de demonstração de "Scott Hanselman"](https://github.com/jamesmontemagno/Hanselman.Forms) está disponível separadamente e também inclui projetos de Apple Watch e o Android Wear (usando xamarin. IOS e xamarin. Android, respectivamente, xamarin. Forms não é executado nessas plataformas).

## <a name="related-links"></a>Links relacionados

- [Projetos de instalação do Windows](~/xamarin-forms/platform/windows/installation/index.md)
