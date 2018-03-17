---
title: "Introdução ao desenvolvimento de jogos com CocosSharp"
description: "Este passo a passo de várias parte mostra como criar um jogo 2D simple usando CocosSharp. Ele aborda os conceitos de programação jogos comuns, como gráficos, a entrada e a física."
ms.topic: article
ms.prod: xamarin
ms.assetid: BCA99A61-A48D-4207-9A35-4C62F10C9AA5
ms.technology: xamarin-cross-platform
author: charlespetzold
ms.author: chape
ms.date: 03/27/2017
ms.openlocfilehash: 5ab6f68aed791dd21516d663367ac5435e92d6cc
ms.sourcegitcommit: 5fc1c4d17cd9c755604092cf7ff038a6358f8646
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/17/2018
---
# <a name="introduction-to-game-development-with-cocossharp"></a>Introdução ao desenvolvimento de jogos com CocosSharp

_Este passo a passo de várias parte mostra como criar um jogo 2D simple usando CocosSharp. Ele aborda os conceitos de programação jogos comuns, como gráficos, a entrada e a física._

O mecanismo de jogos 2D CocosSharp fornece tecnologia para fazer jogos de plataforma cruzada. Para obter uma lista completa de plataformas com suporte, consulte o [CocosSharp wiki no GitHub](https://github.com/mono/CocosSharp/wiki). Este tutorial usará c# para obter exemplos de código, embora CocosSharp também é totalmente funcional com F #.

O núcleo do CocosSharp é fornecido pelo [MonoGame framework](http://www.monogame.net/), que é uma plataforma cruzada, fornecendo um pipeline de conteúdo, gerenciamento de estado de áudio, jogos, entrada e gráficos para a importação de ativos de API acelerada por hardware. CocosSharp é uma camada de abstração eficiente adequada para jogos 2D. Além disso, jogos maior podem executar seus próprios otimizações fora de suas bibliotecas de núcleo que jogos cresce em complexidade. Em outras palavras, CocosSharp fornece uma mistura de facilidade de uso e desempenho, permitindo que os desenvolvedores começar rapidamente sem limitar tamanho de jogo ou complexidade.

A primeira seção do foco passo a passo sobre como configurar um projeto vazio.  A segunda parte abrange escrever todos nossa lógica de jogo. 

No final deste passo a passo, será criado um jogo simple onde meta do player é slide uma raquete horizontalmente para tentar manter uma bola de cair fora da tela. Cada salto aumenta a pontuação do player, um ponto.

![](images/image1.png "Cada salto aumentará a pontuação do player por um ponto")

# <a name="walkthrough-parts"></a>Partes de instruções passo a passo

* [Parte 1 – criar um projeto de CocosSharp](~/graphics-games/cocossharp/first-game/part1.md)
* [Parte 2 – implementar o BouncingGame](~/graphics-games/cocossharp/first-game/part2.md)

## <a name="related-links"></a>Links relacionados

- [Conteúdo do jogo (exemplo)](https://github.com/xamarin/mobile-samples/blob/master/BouncingGame/Resources/Content.zip?raw=true)
- [Projeto concluído (exemplo)](https://developer.xamarin.com/samples/mobile/BouncingGame/)
- [PCL CocosSharp NuGet](http://www.nuget.org/packages/CocosSharp.PCL.Shared/)
- [Documentação da API do CocosSharp](https://developer.xamarin.com/api/namespace/CocosSharp/)
