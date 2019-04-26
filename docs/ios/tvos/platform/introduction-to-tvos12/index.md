---
title: Introdução ao tvOS 12
description: Este documento fornece um alto nível de visão geral dos recursos novos e atualizados do tvOS 12 na versão prévia do qual Xamarin fornece associações c# no momento.
ms.prod: xamarin
ms.assetid: 037F7FFF-2155-4017-B99A-839CE7EC5C9C
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 10/05/2018
ms.openlocfilehash: f7fb8cc379a070b848c5154c9c1d4fbfc8186266
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60932530"
---
# <a name="introduction-to-tvos-12"></a>Introdução ao tvOS 12

Este documento fornece uma visão geral dos novo e atualizado tvOS 12.

Para começar a criar aplicativos de 12 de tvOS com o Xamarin, dê uma olhada a [guia de Introdução](~/ios/platform/introduction-to-ios12/get-started.md).

## <a name="tvuikit"></a>TVUIKit

tvOS 12 inclui TVUIKit, um conjunto de APIs que possibilitam que os desenvolvedores de tvOS usar controles comuns do tvOS, como modos de exibição de pôster, botões de legenda, modos de exibição do cartão e modos de exibição Monograma. tvOS 12 também apresenta uma propriedade que permite que os rótulos rolar o texto que é muito longo para ser completamente visível.

## <a name="password-autofill"></a>Preenchimento automático de senha

Com 12 do tvOS, os usuários podem usar seus dispositivos iOS para entrar um aplicativo tvOS com um único toque. Isso é habilitado por meio de uma combinação de `UITextContentType` campos de uso para especificar o nome de usuário e senha, domínios associados para estabelecer uma relação entre um aplicativo iOS e um aplicativo tvOS e ambientes de foco preferencial para selecionar um item para receber o foco depois que um usuário Fornece um nome de usuário e senha.

## <a name="focus-engine-enhancements"></a>Aprimoramentos do mecanismo de foco

tvOS 12 permite que todos os aplicativos, independentemente de como elas são renderizadas, para interagir com o mecanismo de foco. Por meio de interações do usuário com o Siri remoto, o mecanismo de foco pode ser usado com qualquer aplicativo para selecionar um item, Dica sobre alterações de foco possíveis e atualizar naturalmente o foco. Isso é habilitado em aplicativos personalizados por meio do UIKit `IUIFocusItemContainer` interface, o `UIFocusMovementHint` classe, o `IUIFocusItemScrollableContainer` interface e outras classes relacionadas e métodos.

## <a name="vision-framework"></a>Estrutura Vision

A estrutura de visão inclui um detector de face aprimorados que pode detectar faces em várias orientações. Além disso, as revisões de solicitação agora podem ser usadas para selecionar uma revisão do algoritmo de framework visão específica.

## <a name="natural-language-framework"></a>Estrutura de linguagem natural

A estrutura de linguagem Natural permite que os aplicativos executar vários tipos de análise de linguagem. Por exemplo, ele pode ser usado para identificar partes da fala e determinar o idioma representado por um bloco de texto.

## <a name="deprecations"></a>Substituições

Com tvOS 12, a Apple preteriu OpenGL ES [encoraja os desenvolvedores](https://developer.apple.com/tvos/whats-new/) adotar Bare Metal.

## <a name="related-links"></a>Links relacionados

- [Amostras do tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS – o desenvolvedor da Apple (Apple)](https://developer.apple.com/tvos/)
- [O que há de novo no tvOS 12 (Apple) (vídeo)](https://developer.apple.com/videos/play/wwdc2018/208/)
- [TV (Apple)](https://www.apple.com/tv/)