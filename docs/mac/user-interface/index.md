---
title: controles de interface de usuário do macOS no xamarin. Mac
description: Este documento leva a guias que descrevem os diversos controles de interface do usuário disponíveis para desenvolvedores do xamarin. Mac. Conteúdo vinculado examina o windows, as caixas de diálogo, alertas, menus, barras de ferramentas, modos de exibição de tabela, modos de exibição de estrutura de tópicos e muito mais.
ms.prod: xamarin
ms.assetid: 876B6EC2-E158-43F2-B9C9-03F54F3D2A49
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/27/2018
ms.openlocfilehash: b2392f05a03015f903918f15013919be14b99292
ms.sourcegitcommit: 47709db4d115d221e97f18bc8111c95723f6cb9b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/13/2018
ms.locfileid: "40251076"
---
# <a name="macos-user-interface-controls-in-xamarinmac"></a>controles de interface de usuário do macOS no xamarin. Mac

_Links neste artigo às guias que descrevem os diversos controles de interface do usuário do macOS._

Ao trabalhar com c# e .NET em um aplicativo xamarin. Mac, você tem acesso ao mesmo usuário controles de interface que um desenvolvedor que trabalha *Objective-C* e *Xcode* faz. Como o xamarin. Mac se integra diretamente com Xcode, você pode usar do Xcode _construtor de Interface_ para criar e manter suas interfaces de usuário (ou, opcionalmente, criá-los diretamente em código c#).

Os guias listados abaixo oferecem informações detalhadas sobre como trabalhar com elementos de interface do usuário do macOS em um aplicativo xamarin. Mac. É altamente recomendável que você trabalhe por meio de [Hello, Mac](~/mac/get-started/hello-mac.md) pela primeira vez, especificamente o artigo a [Introdução ao Interface Builder e Xcode](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) e [saídas e ações](~/mac/get-started/hello-mac.md#outlets-and-actions) seções, como ela aborda os principais conceitos e técnicas que usaremos em cada artigo.

Talvez você queira dar uma olhada a [expondo classes c# do / métodos para Objective-C](~/mac/internals/how-it-works.md#exposing-c-classes--methods-to-objective-c) seção o [recursos internos de xamarin. Mac](~/mac/internals/how-it-works.md) de documento, pois ele explica a `Register` e `Export` atributos usados para suas classes c# de transmissão-up para objetos de Objective-C e os elementos de interface do usuário.

## <a name="windowsmacuser-interfacewindowmd"></a>[Windows](~/mac/user-interface/window.md)

Este artigo aborda o trabalho com janelas e painéis em um aplicativo xamarin. Mac. Ele aborda a criação e manutenção de janelas e painéis no Xcode e Interface Builder, carregando janelas e painéis de arquivos storyboard ou. XIB, usando o windows e respondendo a janelas em código c#.

## <a name="dialogsmacuser-interfacedialogmd"></a>[Caixas de diálogo](~/mac/user-interface/dialog.md)

Este artigo aborda o trabalho com caixas de diálogo e janelas modais em um aplicativo xamarin. Mac. Ele aborda a criação e manutenção de janelas modais no Xcode e Interface Builder, trabalhando com caixas de diálogo padrão e exibir e respondendo a janelas em código c#.

## <a name="alertsmacuser-interfacealertmd"></a>[Alertas](~/mac/user-interface/alert.md)

Este artigo aborda o trabalho com alertas em um aplicativo xamarin. Mac. Ele aborda a criação e exibição de alertas do código c# e respondendo a alertas.

## <a name="menusmacuser-interfacemenumd"></a>[Menus](~/mac/user-interface/menu.md)

Menus são usados em várias partes da interface do usuário de um aplicativo Mac; do menu principal do aplicativo na parte superior da tela para menus pop-up e menus contextuais que podem aparecer em qualquer lugar em uma janela. Menus são parte integrante da experiência do usuário de um aplicativo Mac. Este artigo aborda o trabalho com menus Cocoa em um aplicativo xamarin. Mac.

## <a name="standard-controlsmacuser-interfacestandard-controlsmd"></a>[Controles padrão](~/mac/user-interface/standard-controls.md)

Trabalhando com controles AppKit padrão, como botões, rótulos, campos de texto, caixas de seleção e controles segmentados em um aplicativo xamarin. Mac. Este guia aborda adicioná-los para um design de interface do usuário no Interface Builder do Xcode, expô-las ao código por meio de saídas e ações e trabalhar com controles AppKit em código c#.

## <a name="toolbarsmacuser-interfacetoolbarmd"></a>[Barras de ferramentas](~/mac/user-interface/toolbar.md)

Este artigo aborda o trabalho com barras de ferramentas em um aplicativo xamarin. Mac. Ele aborda a criação e manutenção de barras de ferramentas do Xcode e Interface Builder, como expor os itens de barra de ferramentas ao código usando saídas e ações, habilitar e desabilitar itens de barra de ferramentas e, por fim, responder aos itens da barra de ferramentas em código c#.

## <a name="table-viewsmacuser-interfacetable-viewmd"></a>[Modos de exibição de tabela](~/mac/user-interface/table-view.md)

Este artigo aborda o trabalho com modos de exibição de tabela em um aplicativo xamarin. Mac. Ele aborda a criação e manutenção de modos de exibição de tabela no Xcode e Interface Builder, como expor o modo de exibição de tabela itens ao código usando saídas e ações, popular modos de exibição de tabela e responder aos itens de exibição de tabela em código c#.

## <a name="outline-viewsmacuser-interfaceoutline-viewmd"></a>[Modos de exibição de estrutura de tópicos](~/mac/user-interface/outline-view.md)

Este artigo aborda o trabalho com modos de exibição de estrutura de tópicos em um aplicativo xamarin. Mac. Ele aborda a criação e manutenção de modos de exibição de estrutura de tópicos no Xcode e Interface Builder, como expor o modo de exibição de estrutura de tópicos itens ao código usando saídas e ações, popular modos de exibição de estrutura de tópicos e responder para exibir itens no código c# de estrutura de tópicos.

## <a name="source-listsmacuser-interfacesource-listmd"></a>[Listas de origem](~/mac/user-interface/source-list.md)

Este artigo aborda o trabalho com listas de origem em um aplicativo xamarin. Mac. Ele aborda a criação e manutenção de listas de origem no Xcode e Interface Builder, como expor os itens de lista de origem para o código usando saídas e ações, popular listas de origem e responder aos itens de lista de origem em código c#.

## <a name="collection-viewsmacuser-interfacecollection-viewmd"></a>[Exibições de coleção](~/mac/user-interface/collection-view.md)

Este artigo aborda o trabalho com exibições de coleção em um aplicativo xamarin. Mac. Ele aborda a criação e manutenção de exibições de coleção no Xcode e Interface Builder, como expor a exibição de coleção de itens ao código usando saídas e ações, popular modos de exibição de coleção e responder aos modos de exibição de coleção em código c#.

## <a name="creating-custom-controlsmacuser-interfacecustom-controlsmd"></a>[Criação de controles personalizados](~/mac/user-interface/custom-controls.md)

Este artigo aborda Criando controles de interface de usuário personalizada (herdando de `NSControl`), uma interface personalizada para o controle de desenho e criação de ações personalizadas que podem ser usadas com o Interface Builder do Xcode.

## <a name="mac-samples-gallery"></a>Galeria de exemplos do Mac

Sugerimos também dar uma olhada a [Galeria de exemplos do Mac](https://developer.xamarin.com/samples/mac/all/). Ele inclui uma grande quantidade de código prontos para uso que pode ajudar você a obter um projeto xamarin. Mac deslocar o rapidamente.

## <a name="related-links"></a>Links relacionados

- [Diretrizes de Interface humana do macOS](https://developer.apple.com/macos/human-interface-guidelines/overview/themes/)
