---
title: CocosSharp
description: Links neste documento para vários artigos sobre desenvolvimento de jogos com CocosSharp.
ms.prod: xamarin
ms.assetid: 5E72869D-3541-408B-AB64-D34C777AFB79
author: charlespetzold
ms.author: chape
ms.date: 03/29/2018
ms.openlocfilehash: a188863cf57706e3f9dd6c8f4d2d3e60b2591e0b
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/09/2018
---
# <a name="cocossharp"></a>CocosSharp

_CocosSharp é uma biblioteca para a construção de jogos 2D usando c# e F #. É uma porta .NET do mecanismo de Cocos2D popular._

## <a name="introduction-to-cocossharp"></a>Introdução ao CocosSharp

O mecanismo de jogos 2D CocosSharp fornece tecnologia para fazer jogos de plataforma cruzada. Para obter uma lista completa de plataformas com suporte, consulte o [CocosSharp wiki no GitHub](https://github.com/mono/CocosSharp/wiki).
Esses guias usam c# para obter exemplos de código, embora CocosSharp também é totalmente funcional com F #.

O núcleo do CocosSharp é fornecido pelo [MonoGame framework](http://www.monogame.net/), que é uma plataforma cruzada, fornecendo um pipeline de conteúdo, gerenciamento de estado de áudio, jogos, entrada e gráficos para a importação de ativos de API acelerada por hardware.
CocosSharp é uma camada de abstração eficiente adequada para jogos 2D.
Além disso, jogos maior podem executar seus próprios otimizações fora de suas bibliotecas de núcleo que jogos cresce em complexidade. Em outras palavras, CocosSharp fornece uma mistura de facilidade de uso e desempenho, permitindo que os desenvolvedores começar rapidamente sem limitar tamanho de jogo ou complexidade.

Essa prático vídeo mostra como criar um simple CocosSharp de plataforma cruzada jogo:

> [!Video https://channel9.msdn.com/Shows/Visual-Studio-Toolbox/Developing-Cross-platform-2D-Games-in-C-and-CocosSharp/player]

## <a name="bouncinggamegraphics-gamescocossharpbouncing-gamemd"></a>[BouncingGame](~/graphics-games/cocossharp/bouncing-game.md)

![BouncingGame](images/bouncing-game.png "BouncingGame")

Este guia descreve BouncingGame, incluindo como trabalhar com conteúdo de jogo, vários elementos visuais usados para criar um jogo, adicionar lógica de jogo e muito mais.

## <a name="fruity-falls-gamegraphics-gamescocossharpfruity-fallsmd"></a>[Fruity vão jogo](~/graphics-games/cocossharp/fruity-falls.md)

![Captura de tela do jogo vão Fruity](images/fruity-falls.png "Fruity vão captura de tela de jogo")

Este guia descreve o jogo vão Fruity, que abordam conceitos de desenvolvimento de jogos como física, gerenciamento de conteúdo, estado do jogo e projeto de jogo e CocosSharp comuns.  

## <a name="coin-time-gamegraphics-gamescocossharpcointimemd"></a>[Jogo de tempo de moeda](~/graphics-games/cocossharp/cointime.md)

![Captura de tela de jogo do tempo de moeda](images/cointime.png "captura de tela de jogo do tempo de moeda")

Tempo de moeda é um platformer completo jogo para iOS e Android. O objetivo do jogo é coletar todas as moedas em um nível e, em seguida, acessar a porta de saída, evitando inimigos e obstáculos.

## <a name="drawing-geometry-with-ccdrawnodegraphics-gamescocossharpccdrawnodemd"></a>[Desenho de geometria com CCDrawNode](~/graphics-games/cocossharp/ccdrawnode.md)

![Formas desenhadas com CCDrawNode](images/ccdrawnode.png "formas desenhadas com CCDrawNode")

CCDrawNode fornece métodos para desenho primitivas de objetos, como linhas, círculos e triângulos.

## <a name="animating-with-ccactiongraphics-gamescocossharpccactionmd"></a>[Animação com CCAction](~/graphics-games/cocossharp/ccaction.md)

![Uma animação CCAction](images/ccaction.png "CCAction A animação")

`CCAction` é uma classe base que pode ser usada para animar objetos CocosSharp. Este guia aborda interno `CCAction` implementações para tarefas comuns, como posicionamento, escala e rotação. Examina também como criar implementações personalizadas ao herdar de `CCAction`.

## <a name="using-tiled-with-cocossharpgraphics-gamescocossharptiledmd"></a>[Usar lado a lado com CocosSharp](~/graphics-games/cocossharp/tiled.md)

![Um nível em um jogo](images/tiled.png "um nível em um jogo")

Lado a lado é uma poderosa, flexível e o aplicativo consolidado para a criação de bloco ortogonal e isométrico mapeia para jogos. CocosSharp fornece integração interna para o formato de arquivo nativo do lado a lado.

## <a name="entities-in-cocossharpgraphics-gamescocossharpentitiesmd"></a>[Entidades em CocosSharp](~/graphics-games/cocossharp/entities.md)

![Um espaçonave de um jogo](images/entities.png "um espaçonave de um jogo")

O padrão de entidade é uma maneira eficiente de organizar o código de jogo. Ele melhora a legibilidade, torna mais fácil de manter, o código e aproveita a funcionalidade interna de pai/filho.

## <a name="handling-multiple-resolutions-in-cocossharpgraphics-gamescocossharpresolutionsmd"></a>[Manipulando várias resoluções CocosSharp](~/graphics-games/cocossharp/resolutions.md)

![Uma grade que representa a resolução da tela](images/resolutions.png "uma grade que representa a resolução da tela")

Este guia mostra como trabalhar com CocosSharp para desenvolver jogos exibem corretamente em dispositivos de resoluções diferentes.

## <a name="cocossharp-content-pipelinegraphics-gamescocossharpcontent-pipelineindexmd"></a>[Pipeline de conteúdo do CocosSharp](~/graphics-games/cocossharp/content-pipeline/index.md)

![XNB](images/content-pipeline.png "XNB")

Pipelines conteúdos geralmente são usados no desenvolvimento de jogos para otimizar o conteúdo e formatá-lo, de modo que ele possa ser carregado em determinado hardware ou com determinadas estruturas de desenvolvimento de jogos.

## <a name="improving-frame-rate-with-ccspritesheetgraphics-gamescocossharpccspritesheetmd"></a>[Melhorando a taxa de quadros com CCSpriteSheet](~/graphics-games/cocossharp/ccspritesheet.md)

![Uma árvore de um CCSpriteSheet](images/ccspritesheet.png "uma árvore de um CCSpriteSheet")

`CCSpriteSheet` fornece funcionalidade para combinar e usar muitos arquivos de imagem em uma textura. Reduzir a contagem de textura pode melhorar os tempos de carregamento do jogo e taxa de quadros.

## <a name="texture-caching-using-cctexturecachegraphics-gamescocossharptexture-cachemd"></a>[Cache de textura usando CCTextureCache](~/graphics-games/cocossharp/texture-cache.md)

![Uma representação de como CocosSharp armazena em cache imagens](images/texture-cache.png "uma representação de como CocosSharp armazena em cache imagens")

Do CocosSharp `CCTextureCache` classe fornece uma maneira padrão para organizar, cache e descarregar conteúdo. 

## <a name="2d-math-with-cocossharpgraphics-gamescocossharpmathmd"></a>[Matemática 2D com CocosSharp](~/graphics-games/cocossharp/math.md)

![Uma imagem que está sendo girada](images/math.png "uma imagem que está sendo girada")

Este guia abrange 2D matemática para desenvolvimento de jogos. Ele usa CocosSharp para mostrar como executar tarefas comuns de desenvolvimento de jogos e explica a matemática por trás essas tarefas.

## <a name="performance-and-visual-effects-with-ccrendertexturegraphics-gamescocossharpccrendertexturemd"></a>[Desempenho e efeitos visuais com CCRenderTexture](~/graphics-games/cocossharp/ccrendertexture.md)

![Uma entidade gráfica de um jogo](images/ccrendertexture.png "uma entidade gráfica de um jogo")

O `CCRenderTexture` classe fornece funcionalidade para a renderização de vários objetos CocosSharp com uma textura única. Depois de criado, `CCRenderTexture` instâncias podem ser usadas para renderizar elementos gráficos com eficiência e implementar efeitos visuais.
