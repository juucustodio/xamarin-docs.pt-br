---
title: Introdução ao desenvolvimento de jogos com monojogo
description: Este passo a passos de várias partes mostra como criar um aplicativo 2D simples usando monogame.  Ele aborda conceitos comuns de programação de jogos, como elementos gráficos, entrada, entidades de jogos e física.
ms.prod: xamarin
ms.assetid: D781401F-7A96-4098-9645-5F98AEAF7F71
author: conceptdev
ms.author: crdun
ms.date: 03/28/2017
ms.openlocfilehash: bdbce0b001d5baf5ef751e092a2083efdf3ca992
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91436264"
---
# <a name="introduction-to-game-development-with-monogame"></a>Introdução ao desenvolvimento de jogos com monojogo

_Este passo a passos de várias partes mostra como criar um aplicativo 2D simples usando monogame.  Ele aborda conceitos comuns de programação de jogos, como elementos gráficos, entrada, entidades de jogos e física._

Este artigo descreve a tecnologia de API monogame para fazer jogos de plataforma cruzada. Para obter uma lista completa de plataformas, consulte o [site do monojogo](http://www.monogame.net/). Este tutorial usará o C# para exemplos de código, embora o monogame também esteja totalmente funcional com F #.

O monogames é uma API de hardware acelerada e de várias plataformas que fornece gráficos, áudio, gerenciamento de estado de jogos, entrada e um pipeline de conteúdo para importar ativos. Ao contrário da maioria dos mecanismos de jogo, o monogame não fornece ou impõe qualquer estrutura de projeto ou padrão.  Embora isso signifique que os desenvolvedores são livres para organizar seu código como eles gostam, isso também significa que um pouco de código de configuração é necessário ao iniciar um novo projeto pela primeira vez.

A primeira seção deste passo a passos concentra-se na configuração de um projeto vazio. A última seção aborda a gravação de toda a lógica e o conteúdo do jogo – a maioria deles será a plataforma cruzada.

Até o final deste guia, criaremos um jogo simples em que o player pode controlar um caractere animado com entrada por toque.  Embora isso não seja tecnicamente um jogo completo (já que não tem condições de ganho ou perda), ele demonstra vários conceitos de desenvolvimento de jogos e pode ser usado como base para muitos tipos de jogos.

Veja a seguir o resultado deste passo a passos:

![Animação do caractere de jogo de exemplo após o mouse](images/image1.gif)

## <a name="monogame-and-xna"></a>Monogames e XNA

A biblioteca monogame destina-se a imitar a biblioteca XNA da Microsoft na sintaxe e na funcionalidade.  Todos os objetos monogames existem no namespace Microsoft. XNA – permitindo que a maioria dos códigos do XNA seja usada em monojogo sem modificação.

Os desenvolvedores familiarizados com o XNA já estarão familiarizados com a sintaxe do jogo, e os desenvolvedores que buscam informações adicionais sobre como trabalhar com monojogo poderão fazer referência a guias de instruções do XNA online existentes, documentação da API e discussões.

## <a name="walkthrough-parts"></a>Partes explicativas

- [Parte 1 – Criando um projeto Multijogo de plataforma cruzada](~/graphics-games/monogame/introduction/part1.md)
- [Parte 2 – implementando o WalkingGame](~/graphics-games/monogame/introduction/part2.md)

## <a name="related-links"></a>Links Relacionados

- [Projeto de monojogo WalkingGame (exemplo)](/samples/xamarin/mobile-samples/walkinggamemg/)
- [Android de monojogo no NuGet](https://www.nuget.org/packages/MonoGame.Framework.Android/)
- [IOS de jogos no NuGet](https://www.nuget.org/packages/MonoGame.Framework.iOS/)
- [Documentação da API monogame](http://www.monogame.net/documentation/?page=main)