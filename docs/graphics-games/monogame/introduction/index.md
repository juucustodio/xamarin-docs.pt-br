---
title: Introdução ao desenvolvimento de jogos com MonoGame
description: Este passo a passo de várias parte mostra como criar um aplicativo de 2D simples usando o MonoGame.  Ele aborda o jogo comuns conceitos de programação, como gráficos, de entrada, entidades e física de jogos.
ms.prod: xamarin
ms.assetid: D781401F-7A96-4098-9645-5F98AEAF7F71
author: conceptdev
ms.author: crdun
ms.date: 03/28/2017
ms.openlocfilehash: 4ab98d59bc74672f9531f4dbd3c33a6270582612
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61386252"
---
# <a name="introduction-to-game-development-with-monogame"></a>Introdução ao desenvolvimento de jogos com MonoGame

_Este passo a passo de várias parte mostra como criar um aplicativo de 2D simples usando o MonoGame.  Ele aborda o jogo comuns conceitos de programação, como gráficos, de entrada, entidades e física de jogos._

Este artigo descreve a tecnologia de MonoGame de API para criar jogos de plataforma cruzada. Para obter uma lista completa das plataformas, consulte a [MonoGame site](http://www.monogame.net/). Este tutorial usa o C# para obter exemplos de código, embora MonoGame é completamente funcional com F# também.

MonoGame é uma plataforma cruzada, acelerada por hardware API fornecendo elementos gráficos, gerenciamento de estado do jogo, áudio, entrada e um pipeline de conteúdo para a importação de ativos. Ao contrário da maioria dos mecanismos de jogos, MonoGame não fornecer ou impor qualquer estrutura de projeto padrão.  Enquanto isso significa que os desenvolvedores são livres para organizar seu código que desejarem, também significa que um pouco de código de configuração é necessária quando começando em um novo projeto.

A primeira seção deste passo a passo se concentra em como configurar um projeto vazio. A última seção aborda a escrever todos os nossos lógica do jogo e conteúdo – mais do que será várias plataformas.

No final deste passo a passo, podemos terá criado um jogo simples em que o player pode controlar um caractere animado com entrada de toque.  Embora isso não seja tecnicamente um jogo completo (já que não tem nenhuma ganhou ou perdeu condições), ele demonstra vários conceitos de desenvolvimento de jogos e pode ser usado como a base para muitos tipos de jogos. 

O exemplo a seguir mostra o resultado deste passo a passo:

![Animação de caractere de jogo de exemplo após o mouse](images/image1.gif)

## <a name="monogame-and-xna"></a>Monogame e XNA

A biblioteca de MonoGame destina-se para imitar a biblioteca da Microsoft e XNA em sintaxe e funcionalidade.  Todos os objetos de MonoGame existem sob o namespace Microsoft.Xna – permitindo que a maioria dos códigos do XNA ser usado no MonoGame sem modificação. 

Os desenvolvedores familiarizados com o XNA já estará familiarizados com a sintaxe do MonoGame e desenvolvedores que buscam informações adicionais sobre como trabalhar com MonoGame poderão de referência existente on-line XNA passo a passo, documentação da API e discussões.


## <a name="walkthrough-parts"></a>Partes de instruções passo a passo

- [Parte 1 – criar um projeto de MonoGame de plataforma cruzada](~/graphics-games/monogame/introduction/part1.md)
- [Parte 2 – implementar o WalkingGame](~/graphics-games/monogame/introduction/part2.md)

## <a name="related-links"></a>Links relacionados

- [Projeto de MonoGame WalkingGame (amostra)](https://developer.xamarin.com/samples/mobile/WalkingGameMG/)
- [Fontes XNB iOS](https://github.com/mono/CocosSharp/tree/master/Samples/GameStarterKit/GameStarterKit/Content/fonts)
- [XNB Fonts Android](https://github.com/mono/CocosSharp/tree/master/Samples/GameStarterKit/GameStarterKit/Assets/Content/fonts)
- [MonoGame Android no NuGet](https://www.nuget.org/packages/MonoGame.Framework.Android/)
- [MonoGame iOS no NuGet](https://www.nuget.org/packages/MonoGame.Framework.iOS/)
- [Documentação do MonoGame API](http://www.monogame.net/documentation/?page=main)
