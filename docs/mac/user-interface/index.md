---
title: controles de interface do usuário macOS no Xamarin. Mac
description: Este documento contém links para guias que descrevem vários controles de interface do usuário disponíveis para desenvolvedores do Xamarin. Mac. O conteúdo vinculado dá uma olhada no Windows, caixas de diálogo, alertas, menus, barras de ferramentas, exibições de tabela, exibições de estrutura de tópicos e muito mais.
ms.prod: xamarin
ms.assetid: 876B6EC2-E158-43F2-B9C9-03F54F3D2A49
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 03/27/2018
ms.openlocfilehash: 7ae33883b83a4080903b524ea78e0da009ad0a3a
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91431525"
---
# <a name="macos-user-interface-controls-in-xamarinmac"></a>controles de interface do usuário macOS no Xamarin. Mac

_Este artigo fornece links para guias que descrevem vários controles de interface do usuário do macOS._

Ao trabalhar com C# e .NET em um aplicativo Xamarin. Mac, você tem acesso aos mesmos controles de interface do usuário que um desenvolvedor trabalhando no *Objective-C e no* *Xcode* . Como o Xamarin. Mac se integra diretamente com o Xcode, você pode usar o _interface Builder_ do Xcode para criar e manter suas interfaces de usuário (ou, opcionalmente, criá-las diretamente no código C#).

Os guias listados abaixo fornecem informações detalhadas sobre como trabalhar com elementos da interface do usuário do macOS em um aplicativo Xamarin. Mac. É altamente recomendável que você trabalhe pelo artigo [Hello, Mac](~/mac/get-started/hello-mac.md) primeiro, especificamente a [introdução às seções Xcode e Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) e [ações](~/mac/get-started/hello-mac.md#outlets-and-actions) , pois ela aborda os principais conceitos e técnicas que usaremos em todos os artigos.

Talvez você queira dar uma olhada na seção [expondo classes/métodos C# para Objective-C](~/mac/internals/how-it-works.md#exposing-c-classes--methods-to-objective-c) do documento [interno do Xamarin. Mac](~/mac/internals/how-it-works.md) também, pois ele explica os `Register` `Export` atributos e usados para conectar suas classes C# a objetos Objective-C e elementos de interface do usuário.

## <a name="windows"></a>[Windows](~/mac/user-interface/window.md)

Este artigo aborda como trabalhar com janelas e painéis em um aplicativo Xamarin. Mac. Ele aborda a criação e a manutenção de janelas e painéis no Xcode e no Interface Builder, no carregamento de janelas e painéis de arquivos. Storyboard ou. xib, usando o Windows e respondendo ao Windows no código C#.

## <a name="dialogs"></a>[Diálogos](~/mac/user-interface/dialog.md)

Este artigo aborda como trabalhar com caixas de diálogo e janelas modais em um aplicativo Xamarin. Mac. Ele aborda a criação e a manutenção de janelas modais no Xcode e Interface Builder, trabalhando com caixas de diálogo padrão e exibindo e respondendo ao Windows no código C#.

## <a name="alerts"></a>[Alertas](~/mac/user-interface/alert.md)

Este artigo aborda como trabalhar com alertas em um aplicativo Xamarin. Mac. Ele aborda a criação e a exibição de alertas do código C# e a resposta a alertas.

## <a name="menus"></a>[Menus](~/mac/user-interface/menu.md)

Os menus são usados em várias partes da interface do usuário de um aplicativo Mac; no menu principal do aplicativo, na parte superior da tela, para menus pop-up e menus contextuais que podem aparecer em qualquer lugar em uma janela. Menus são uma parte integrante da experiência do usuário de um aplicativo Mac. Este artigo aborda como trabalhar com os menus do Cocoa em um aplicativo Xamarin. Mac.

## <a name="standard-controls"></a>[Controles padrão](~/mac/user-interface/standard-controls.md)

Trabalhar com controles AppKit padrão, como botões, rótulos, campos de texto, caixas de seleção e controles segmentados em um aplicativo Xamarin. Mac. Este guia aborda a adição deles a um design de interface do usuário na Interface Builder do Xcode, expondo-os a código por meio de ações e saídas e trabalhando com controles AppKit em código C#.

## <a name="toolbars"></a>[Barras de Ferramentas](~/mac/user-interface/toolbar.md)

Este artigo aborda como trabalhar com barras de ferramentas em um aplicativo Xamarin. Mac. Ele aborda a criação e a manutenção de barras de ferramentas no Xcode e Interface Builder, como expor os itens da barra de ferramentas para código usando saídas e ações, habilitando e desabilitando itens da barra de ferramentas e, finalmente, respondendo a itens da barra de ferramentas no código C#.

## <a name="table-views"></a>[Exibições de tabela](~/mac/user-interface/table-view.md)

Este artigo aborda como trabalhar com exibições de tabela em um aplicativo Xamarin. Mac. Ele aborda a criação e a manutenção de exibições de tabela no Xcode e Interface Builder, como expor os itens de exibição de tabela para código usando saídas e ações, preenchendo exibições de tabela e respondendo a itens de exibição de tabela no código C#.

## <a name="outline-views"></a>[Exibições de estrutura de tópicos](~/mac/user-interface/outline-view.md)

Este artigo aborda como trabalhar com exibições de estrutura de tópicos em um aplicativo Xamarin. Mac. Ele aborda a criação e a manutenção de exibições de contorno no Xcode e Interface Builder, como expor os itens da exibição de estrutura de tópicos para código usando saídas e ações, preenchendo exibições de contorno e respondendo a itens de exibição de estrutura de tópicos em código C#

## <a name="source-lists"></a>[Listas de origem](~/mac/user-interface/source-list.md)

Este artigo aborda como trabalhar com listas de origem em um aplicativo Xamarin. Mac. Ele aborda a criação e a manutenção de listas de origem no Xcode e no Interface Builder, como expor itens de lista de origem para código usando saídas e ações, preenchendo listas de origem e respondendo a itens de lista de origem em código C#.

## <a name="collection-views"></a>[Exibições de coleção](~/mac/user-interface/collection-view.md)

Este artigo aborda como trabalhar com exibições de coleção em um aplicativo Xamarin. Mac. Ele aborda a criação e a manutenção de exibições de coleção no Xcode e Interface Builder, como expor os itens de exibição de coleção para código usando saídas e ações, preenchendo exibições de coleção e respondendo a exibições de coleção no código C#.

## <a name="creating-custom-controls"></a>[Criando controles personalizados](~/mac/user-interface/custom-controls.md)

Este artigo aborda a criação de controles de interface do usuário personalizados (herdados de `NSControl` ), o desenho de uma interface personalizada para o controle e a criação de ações personalizadas que podem ser usadas com a interface Builder do Xcode.

## <a name="mac-samples-gallery"></a>Galeria de exemplos de Mac

Também sugerimos dar uma olhada na [Galeria de exemplos de Mac](/samples/browse/?products=xamarin&term=Xamarin.Mac). Ele inclui uma infinidade de código pronto para uso que pode ajudá-lo a obter um projeto Xamarin. Mac rapidamente.

## <a name="related-links"></a>Links relacionados

- [Diretrizes de Interface Humana do macOS](https://developer.apple.com/macos/human-interface-guidelines/overview/themes/)