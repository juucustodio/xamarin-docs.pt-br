---
title: "Interface do usuário"
description: "Este artigo apresenta links para guias que descrevem vários controles de interface do usuário macOS."
ms.topic: article
ms.prod: xamarin
ms.assetid: 876B6EC2-E158-43F2-B9C9-03F54F3D2A49
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 10/19/2016
ms.openlocfilehash: a8cb9488849dafc2cd720ecf59d654009a9ad781
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="user-interface"></a>Interface do usuário

_Este artigo apresenta links para guias que descrevem vários controles de interface do usuário macOS._

Ao trabalhar com c# e .NET em um aplicativo de Xamarin.Mac, você tem acesso à mesma Interface do usuário que controla um desenvolvedor trabalhando em *Objective-C* e *Xcode* does. Como Xamarin.Mac se integra diretamente com o Xcode, você pode usar do Xcode _Interface construtor_ para criar e manter suas Interfaces de usuário (ou, opcionalmente, criá-los diretamente no código do c#). 

As guias listadas a seguir fornecem informações detalhadas sobre como trabalhar com elementos de interface do usuário macOS em um aplicativo Xamarin.Mac. É altamente recomendável que você leia o [Hello, Mac](~/mac/get-started/hello-mac.md) artigo primeiro, especificamente o [Introdução ao construtor da Interface e Xcode](~/mac/get-started/hello-mac.md#Introduction_to_Xcode_and_Interface_Builder) e [tomadas e ações](~/mac/get-started/hello-mac.md#Outlets_and_Actions) seções, como ele aborda os principais conceitos e técnicas que será usado em cada artigo.

Talvez você queira dar uma olhada a [classes expondo c# / métodos para Objective-C](~/mac/internals/how-it-works.md) seção o [Xamarin.Mac internos](~/mac/internals/how-it-works.md) de documento, ele explica o `Register` e `Export` comandos usado com a transmissão suas classes c# objetos Objective-C e elementos de interface do usuário.

## <a name="windowsmacuser-interfacewindowmd"></a>[Windows](~/mac/user-interface/window.md)

Este artigo aborda a trabalhar com janelas e painéis em um aplicativo Xamarin.Mac. Ele aborda a criação e manutenção de janelas e painéis no construtor de Interface e Xcode, carregar janelas e painéis de `.storyboard` ou `.xib` arquivos, usando o Windows e responder ao Windows em código c#.

## <a name="dialogsmacuser-interfacedialogmd"></a>[Caixas de diálogo](~/mac/user-interface/dialog.md)

Este artigo aborda a trabalhar com caixas de diálogo e janelas modais em um aplicativo Xamarin.Mac. Ele aborda a criação e manutenção de janelas modais no construtor de Interface e Xcode, o trabalho com caixas de diálogo padrão, a exibição de janelas e resposta a elas em código c#.

## <a name="alertsmacuser-interfacealertmd"></a>[Alertas](~/mac/user-interface/alert.md)

Este artigo aborda o trabalho com alertas em um aplicativo Xamarin.Mac. Ele aborda a criação e exibição de alertas por código C# e a resposta aos alertas.

## <a name="menusmacuser-interfacemenumd"></a>[Menus](~/mac/user-interface/menu.md)

Menus são usados em várias partes da interface do usuário de um aplicativo Mac; do menu principal do aplicativo na parte superior da tela para menus pop-up e contextuais que podem aparecer em qualquer lugar em uma janela. Menus são uma parte integrante da experiência do usuário de um aplicativo Mac. Este artigo aborda o trabalho com menus Cocoa em um aplicativo Xamarin.Mac.

## <a name="standard-controlsmacuser-interfacestandard-controlsmd"></a>[Controles padrão](~/mac/user-interface/standard-controls.md)

Trabalhando com os controles AppKit padrão como botões, rótulos, campos de texto, caixas de seleção e segmentado controles em um aplicativo Xamarin.Mac. Este guia aborda adicioná-los para um Design de Interface do usuário no construtor de Interface do Xcode, expondo-os para o código por meio de saídas e as ações e trabalhar com controles de AppKit no código c#.

 
## <a name="toolbarsmacuser-interfacetoolbarmd"></a>[Barras de ferramentas](~/mac/user-interface/toolbar.md)

Este artigo aborda o trabalho com barras de ferramentas em um aplicativo Xamarin.Mac. Ele aborda as ações de criar e manter barras de ferramentas no Interface Builder e Xcode, como expor os itens da barra de ferramentas ao código usando saídas e ações, habilitar e desabilitar de itens de barra de ferramentas e, por fim, responder aos itens da barra de ferramentas em código C#.

## <a name="table-viewsmacuser-interfacetable-viewmd"></a>[Modos de exibição de tabela](~/mac/user-interface/table-view.md)

Este artigo aborda a trabalhar com modos de exibição de tabela em um aplicativo Xamarin.Mac. Ele aborda a criação e manutenção de modos de exibição de tabela no construtor de Interface e Xcode, como expor os itens de exibição de tabela para o código usando tomadas e ações, preenchendo os modos de exibição de tabela e finalmente responder aos itens de exibição de tabela em código c#.

## <a name="outline-viewsmacuser-interfaceoutline-viewmd"></a>[Modos de exibição de estrutura de tópicos](~/mac/user-interface/outline-view.md)

Este artigo aborda a trabalhar com os modos de exibição em um aplicativo Xamarin.Mac. Ele aborda as ações de criar e manter modos de exibição de estrutura de tópicos no Interface Builder e Xcode, como expor os itens de modos de exibição de estrutura de tópicos ao código usando saídas e ações, popular itens de modos de exibição de estrutura de tópicos e, por fim, responder aos itens de modos de exibição de estrutura de tópicos em código C#.

## <a name="source-listsmacuser-interfacesource-listmd"></a>[Listas de origem](~/mac/user-interface/source-list.md)

Este artigo aborda a trabalhar com a lista de origem em um aplicativo Xamarin.Mac. Ele aborda as ações de criar e manter listas de origem de tópicos no Interface Builder e Xcode, como expor os itens de listas de origem ao código usando saídas e ações, popular itens de listas de origem e, por fim, responder aos itens de listas de origem em código C#.

## <a name="collection-viewsmacuser-interfacecollection-viewmd"></a>[Exibições de coleção](~/mac/user-interface/collection-view.md)

Este artigo aborda a trabalhar com exibições de coleção em um aplicativo Xamarin.Mac. Ele aborda as ações de criar e manter modos de exibição de coleção no Interface Builder e Xcode, como expor os itens de modos de exibição de coleção ao código usando saídas e ações, popular modos de exibição de coleção e, por fim, responder aos modos de exibição de coleção em código C#.

## <a name="creating-custom-user-controlsmacuser-interfacecustom-controlsmd"></a>[Criando controles de usuário personalizada](~/mac/user-interface/custom-controls.md)

Este artigo aborda a criação de controles de Interface do usuário personalizados (herdando de `NSControl`), uma interface personalizada para o controle de desenho e criação de ações personalizadas que podem ser usadas com o construtor de Interface do Xcode.

## <a name="mac-samples-gallery"></a>Galeria de amostras do Mac

Sugerimos também observando o [Galeria de amostras de Mac](http://developer.xamarin.com/samples/mac/all/), inclui uma grande quantidade de código prontos para uso que pode ajudá-lo a obter um projeto Xamarin.Mac o plano rapidamente.

## <a name="related-links"></a>Links relacionados

- [macOS diretrizes de Interface Humana](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)
