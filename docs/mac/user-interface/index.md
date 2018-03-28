---
title: interface do usuário macOS
description: Este artigo apresenta links para guias que descrevem vários controles de interface do usuário macOS.
ms.topic: article
ms.prod: xamarin
ms.assetid: 876B6EC2-E158-43F2-B9C9-03F54F3D2A49
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/27/2018
ms.openlocfilehash: 2ad3d37e1a191423e0a9d744be2329870ba732d3
ms.sourcegitcommit: 20ca85ff638dbe3a85e601b5eb09b2f95bda2807
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2018
---
# <a name="macos-user-interface"></a>interface do usuário macOS

_Este artigo apresenta links para guias que descrevem vários controles de interface do usuário macOS._

Ao trabalhar com c# e .NET em um aplicativo de Xamarin.Mac, você tem acesso ao mesmo usuário interface controla o que um desenvolvedor trabalhando em *Objective-C* e *Xcode* does. Como Xamarin.Mac se integra diretamente com o Xcode, você pode usar do Xcode _Interface construtor_ para criar e manter suas interfaces de usuário (ou, opcionalmente, criá-los diretamente no código do c#).

As guias listadas a seguir fornecem informações detalhadas sobre como trabalhar com elementos de interface do usuário macOS em um aplicativo Xamarin.Mac. É altamente recomendável que você leia o [Hello, Mac](~/mac/get-started/hello-mac.md) artigo primeiro, especificamente o [Introdução ao construtor da Interface e Xcode](~/mac/get-started/hello-mac.md#Introduction_to_Xcode_and_Interface_Builder) e [tomadas e ações](~/mac/get-started/hello-mac.md#Outlets_and_Actions) seções, como ele aborda os principais conceitos e técnicas que será usado em cada artigo.

Talvez você queira dar uma olhada a [classes expondo c# / métodos para Objective-C](~/mac/internals/how-it-works.md#exposing-c-classes--methods-to-objective-c) seção o [Xamarin.Mac internos](~/mac/internals/how-it-works.md) de documentos, conforme explica o `Register` e `Export` atributos usados para transmissão suas classes c# objetos Objective-C e elementos de interface do usuário.

## <a name="windowsmacuser-interfacewindowmd"></a>[Windows](~/mac/user-interface/window.md)

Este artigo aborda a trabalhar com janelas e painéis em um aplicativo Xamarin.Mac. Ele aborda a criação e manutenção janelas e painéis em Xcode e construtor de Interface, janelas de carregamento e painéis de .storyboard ou .xib de arquivos, usando o windows e responder para janelas de código c#.

## <a name="dialogsmacuser-interfacedialogmd"></a>[Caixas de diálogo](~/mac/user-interface/dialog.md)

Este artigo aborda a trabalhar com caixas de diálogo e janelas modais em um aplicativo Xamarin.Mac. Ele aborda a criação e manutenção de janelas modais no Xcode e construtor de Interface, trabalhando com caixas de diálogo padrão e exibir e responder para janelas de código c#.

## <a name="alertsmacuser-interfacealertmd"></a>[Alertas](~/mac/user-interface/alert.md)

Este artigo aborda o trabalho com alertas em um aplicativo Xamarin.Mac. Ele abrange a criar e exibir alertas do código c# e responder aos alertas.

## <a name="menusmacuser-interfacemenumd"></a>[Menus](~/mac/user-interface/menu.md)

Menus são usados em várias partes da interface do usuário de um aplicativo Mac; do menu principal do aplicativo na parte superior da tela para menus pop-up e menus contextuais que podem aparecer em qualquer lugar em uma janela. Menus são uma parte integrante da experiência do usuário do aplicativo do Mac. Este artigo aborda o trabalho com menus Cocoa em um aplicativo Xamarin.Mac.

## <a name="standard-controlsmacuser-interfacestandard-controlsmd"></a>[Controles padrão](~/mac/user-interface/standard-controls.md)

Trabalhando com os controles AppKit padrão como botões, rótulos, campos de texto, caixas de seleção e segmentados controles em um aplicativo de Xamarin.Mac. Este guia abrange adicioná-los para um design de interface do usuário no construtor de Interface do Xcode, expondo-os para o código por meio de saídas e as ações e trabalhar com controles de AppKit em código c#.

## <a name="toolbarsmacuser-interfacetoolbarmd"></a>[Barras de ferramentas](~/mac/user-interface/toolbar.md)

Este artigo aborda o trabalho com barras de ferramentas em um aplicativo Xamarin.Mac. Ele aborda a criação e manutenção de barras de ferramentas Xcode e construtor de Interface, como expor os itens da barra de ferramentas para o código usando tomadas e ações, habilitando e desabilitando itens da barra de ferramentas e finalmente responder aos itens da barra de ferramentas no código c#.

## <a name="table-viewsmacuser-interfacetable-viewmd"></a>[Modos de exibição de tabela](~/mac/user-interface/table-view.md)

Este artigo aborda a trabalhar com modos de exibição de tabela em um aplicativo Xamarin.Mac. Ele aborda a criação e manutenção de modos de exibição de tabela no Xcode e construtor de Interface, como itens de código usando tomadas e ações para expor o modo de exibição de tabela, ao preencher os modos de exibição de tabela e responder aos itens de exibição de tabela em código c#.

## <a name="outline-viewsmacuser-interfaceoutline-viewmd"></a>[Modos de exibição de estrutura de tópicos](~/mac/user-interface/outline-view.md)

Este artigo aborda a trabalhar com modos de exibição de estrutura de tópicos em um aplicativo Xamarin.Mac. Ele aborda a criação e manutenção de modos de exibição de estrutura de tópicos no Xcode e construtor de Interface, como itens de código usando tomadas e ações para expor o modo de exibição de estrutura de tópicos, preenchendo os modos de exibição e responder para exibir itens no código do c# de estrutura de tópicos.

## <a name="source-listsmacuser-interfacesource-listmd"></a>[Lista de origem](~/mac/user-interface/source-list.md)

Este artigo aborda a trabalhar com listas de origem em um aplicativo Xamarin.Mac. Ele aborda a criação e manutenção de listas de origem em Xcode e construtor de Interface, como expor os itens de lista de origem para o código usando tomadas e ações, preenchendo listas de origem e responder aos itens de lista de origem no código c#.

## <a name="collection-viewsmacuser-interfacecollection-viewmd"></a>[Exibições de coleção](~/mac/user-interface/collection-view.md)

Este artigo aborda o trabalho com exibições de coleção em um aplicativo Xamarin.Mac. Ele aborda a criação e manutenção de exibições de coleção no Xcode e construtor de Interface, como para expor o modo de exibição de coleção itens usando tomadas e ações de código, preenchendo as exibições de coleção e responder às exibições de coleção em código c#.

## <a name="creating-custom-controlsmacuser-interfacecustom-controlsmd"></a>[Criação de controles personalizados](~/mac/user-interface/custom-controls.md)

Este artigo aborda Criando controles de interface de usuário personalizado (herdando de `NSControl`), uma interface personalizada para o controle de desenho e criar ações personalizadas que podem ser usadas com o construtor de Interface do Xcode.

## <a name="mac-samples-gallery"></a>Galeria de amostras do Mac

Sugerimos também observando o [Galeria de amostras de Mac](https://developer.xamarin.com/samples/mac/all/). Ele inclui uma grande quantidade de código prontos para uso que pode ajudá-lo a obter um projeto Xamarin.Mac o plano rapidamente.

## <a name="related-links"></a>Links relacionados

- [macOS diretrizes de Interface Humana](https://developer.apple.com/macos/human-interface-guidelines/overview/themes/)
