---
title: Introdução ao desenvolvimento de jogos com MonoGame
description: Este passo a passo de várias parte mostra como criar um aplicativo simples de 2D usando MonoGame.  Ele aborda o jogo comuns do jogo conceitos de programação, como gráficos, de entrada, entidades e física.
ms.prod: xamarin
ms.assetid: D781401F-7A96-4098-9645-5F98AEAF7F71
ms.technology: xamarin-cross-platform
author: charlespetzold
ms.author: chape
ms.date: 03/28/2017
ms.openlocfilehash: 9fb19b86ca303f8be3506d267dd75dc9db6cfca6
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="introduction-to-game-development-with-monogame"></a>Introdução ao desenvolvimento de jogos com MonoGame

_Este passo a passo de várias parte mostra como criar um aplicativo simples de 2D usando MonoGame.  Ele aborda o jogo comuns do jogo conceitos de programação, como gráficos, de entrada, entidades e física._

Este artigo descreve a tecnologia MonoGame API para tornar os jogos de plataforma cruzada. Para obter uma lista completa das plataformas, consulte o [MonoGame site](http://www.monogame.net/). Este tutorial usará c# para obter exemplos de código, embora MonoGame também é totalmente funcional com F #.

MonoGame é uma plataforma cruzada, fornecendo um pipeline de conteúdo, gerenciamento de estado de áudio, jogos, entrada e gráficos para a importação de ativos de API acelerada por hardware. Ao contrário da maioria dos mecanismos de jogo, MonoGame não fornecer ou impor qualquer estrutura de projeto padrão.  Enquanto isso significa que os desenvolvedores estão livres para organizar seu código desejem, isso também significa que um trecho de código de instalação é necessária ao iniciar pela primeira vez um novo projeto.

A primeira seção deste passo a passo se concentra na configuração de um projeto vazio. A última seção abrange gravar todos os nossos lógica de jogo e conteúdo – mais do que será várias plataformas.

No final deste passo a passo, estamos terá criado um jogo simple em que o player pode controlar um caractere animado com a entrada por toque.  Embora não seja tecnicamente um jogo completo (já que não possui ganhando ou perdendo condições), ele demonstra vários conceitos de desenvolvimento de jogos e pode ser usado como a base para vários tipos de jogos. 

O exemplo a seguir mostra o resultado deste passo a passo:

![](images/image1.gif "O aplicativo que será criado no passo a passo")

# <a name="monogame-and-xna"></a>Monogame e XNA

A biblioteca MonoGame destina-se para simular a biblioteca do XNA da Microsoft em sintaxe e funcionalidade.  Todos os objetos MonoGame existem sob o namespace Microsoft.Xna – permitindo que grande parte do código XNA ser usado em MonoGame sem modificação. 

Os desenvolvedores familiarizados com o XNA já estarão familiarizados com a sintaxe do MonoGame e os desenvolvedores para obter informações adicionais sobre como trabalhar com MonoGame será capazes de fazer referência existente on-line XNA passo a passo, documentação da API e discussões.


# <a name="walkthrough-parts"></a>Partes de instruções passo a passo

- [Parte 1 – criar um projeto de MonoGame de plataforma cruzada](~/graphics-games/monogame/introduction/part1.md)
- [Parte 2 – implementar o WalkingGame](~/graphics-games/monogame/introduction/part2.md)

## <a name="related-links"></a>Links relacionados

- [Projeto de MonoGame WalkingGame (exemplo)](https://developer.xamarin.com/samples/mobile/WalkingGameMG/)
- [IOS XNB fontes](https://github.com/mono/CocosSharp/tree/master/Samples/GameStarterKit/GameStarterKit/Content/fonts)
- [XNB Fonts Android](https://github.com/mono/CocosSharp/tree/master/Samples/GameStarterKit/GameStarterKit/Assets/Content/fonts)
- [MonoGame Android NuGet](https://www.nuget.org/packages/MonoGame.Framework.Android/)
- [IOS MonoGame NuGet](https://www.nuget.org/packages/MonoGame.Framework.iOS/)
- [Documentação da API do MonoGame](http://www.monogame.net/documentation/?page=main)
