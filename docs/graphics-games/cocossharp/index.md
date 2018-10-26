---
title: Mecanismo de jogos 2D CocosSharp
description: Este documento leva a vários artigos sobre desenvolvimento de jogos com CocosSharp. Conteúdo vinculado descreve aplicativos de exemplo, desenho, animação e muito mais.
ms.prod: xamarin
ms.assetid: 5E72869D-3541-408B-AB64-D34C777AFB79
author: conceptdev
ms.author: crdun
ms.date: 03/29/2018
ms.openlocfilehash: add73360ea98d8c516e413f0cc0264f68c58d79d
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50107008"
---
# <a name="cocossharp-2d-game-engine"></a>Mecanismo de jogos 2D CocosSharp

_CocosSharp é uma biblioteca para compilar jogos 2D usando C# e F#. É uma porta .NET do mecanismo de Cocos2D popular._

## <a name="introduction-to-cocossharp"></a>Introdução ao CocosSharp

O mecanismo de jogos 2D CocosSharp fornece tecnologia para criar jogos de plataforma cruzada. Para obter uma lista completa das plataformas com suporte, consulte o [CocosSharp wiki do GitHub](https://github.com/mono/CocosSharp/wiki).
Usam esses guias C# para obter exemplos de código, embora CocosSharp é completamente funcional com F# também.

O núcleo do CocosSharp é fornecido pelo [MonoGame framework](http://www.monogame.net/), que em si é uma plataforma cruzada, acelerada por hardware API fornecendo elementos gráficos, gerenciamento de estado do jogo, áudio, entrada e um pipeline de conteúdo para a importação de ativos.
CocosSharp é uma camada de abstração eficiente adequada para jogos 2D.
Além disso, os jogos de maiores podem executar seus próprios otimizações fora de suas bibliotecas principais à medida que aumenta de jogos em complexidade. Em outras palavras, CocosSharp fornece uma mistura de facilidade de uso e desempenho, permitindo que os desenvolvedores a começar a usar rapidamente sem limitar o tamanho de jogo ou a complexidade.

Este vídeo prático mostra como criar um simple CocosSharp de plataforma cruzada jogo:

> [!Video https://channel9.msdn.com/Shows/Visual-Studio-Toolbox/Developing-Cross-platform-2D-Games-in-C-and-CocosSharp/player]

## <a name="bouncinggamegraphics-gamescocossharpbouncing-gamemd"></a>[BouncingGame](~/graphics-games/cocossharp/bouncing-game.md)

![BouncingGame](images/bouncing-game.png "BouncingGame")

Este guia descreve BouncingGame, inclusive sobre como trabalhar com conteúdo do jogo, vários elementos visuais usados para criar um jogo, adicionando lógica do jogo e muito mais.

## <a name="fruity-falls-gamegraphics-gamescocossharpfruity-fallsmd"></a>[Jogo Fruity Falls](~/graphics-games/cocossharp/fruity-falls.md)

![Captura de tela do jogo Fruity Falls](images/fruity-falls.png "captura de tela do jogo Fruity Falls")

Este guia descreve o jogo Fruity cai, que abordam conceitos de desenvolvimento de jogos, como física, o gerenciamento de conteúdo, o estado do jogo e o projeto de jogo e CocosSharp comuns.  

## <a name="coin-time-gamegraphics-gamescocossharpcointimemd"></a>[Jogo de tempo de moeda](~/graphics-games/cocossharp/cointime.md)

![Inventam captura de tela de jogo do tempo](images/cointime.png "captura de tela de jogo do tempo de moeda")

Tempo de moeda é uma plataforma completa para jogo para iOS e Android. O objetivo do jogo é coletar todas as moedas em um nível e, em seguida, acessar a porta de saída, evitando inimigos e obstáculos.

## <a name="drawing-geometry-with-ccdrawnodegraphics-gamescocossharpccdrawnodemd"></a>[Desenho de geometria com CCDrawNode](~/graphics-games/cocossharp/ccdrawnode.md)

![Formas desenhadas com CCDrawNode](images/ccdrawnode.png "formas desenhadas com CCDrawNode")

CCDrawNode fornece métodos para objetos de primitivos de desenho, como linhas, círculos e triângulos.

## <a name="animating-with-ccactiongraphics-gamescocossharpccactionmd"></a>[Animação com CCAction](~/graphics-games/cocossharp/ccaction.md)

![Uma animação CCAction](images/ccaction.png "CCAction uma animação")

`CCAction` é uma classe base que pode ser usada para animar objetos CocosSharp. Este guia aborda interno `CCAction` implementações para tarefas comuns, como posicionamento, dimensionamento e rotação. Ele também explica como criar implementações personalizadas herdando de `CCAction`.

## <a name="using-tiled-with-cocossharpgraphics-gamescocossharptiledmd"></a>[Usar lado a lado com CocosSharp](~/graphics-games/cocossharp/tiled.md)

![Um nível em um jogo](images/tiled.png "um nível em um jogo")

Lado a lado é uma poderosa, flexível e um aplicativo consolidado para a criação de bloco ortogonal e isométrico mapeia para jogos. CocosSharp fornece integração interna para o formato de arquivo nativo do lado a lado.

## <a name="entities-in-cocossharpgraphics-gamescocossharpentitiesmd"></a>[Entidades em CocosSharp](~/graphics-games/cocossharp/entities.md)

![Uma nave espacial de um jogo](images/entities.png "uma nave espacial de um jogo")

O padrão de entidade é uma maneira eficiente para organizar o código de jogo. Ele melhora a legibilidade, o que torna o código mais fácil de manter e aproveita a funcionalidade interna de pai/filho.

## <a name="handling-multiple-resolutions-in-cocossharpgraphics-gamescocossharpresolutionsmd"></a>[Tratar várias resoluções no CocosSharp](~/graphics-games/cocossharp/resolutions.md)

![Uma grade que representa a resolução de tela](images/resolutions.png "uma grade que representa a resolução da tela")

Este guia mostra como trabalhar com CocosSharp para desenvolver jogos que são exibem corretamente nos dispositivos de resoluções diferentes.

## <a name="cocossharp-content-pipelinegraphics-gamescocossharpcontent-pipelineindexmd"></a>[Pipeline de conteúdo do CocosSharp](~/graphics-games/cocossharp/content-pipeline/index.md)

![XNB](images/content-pipeline.png "XNB")

Pipelines de conteúdo geralmente é usado no desenvolvimento de jogos para otimizar o conteúdo e formatá-la, de modo que possa ser carregado em determinado hardware ou com determinadas estruturas de desenvolvimento de jogos.

## <a name="improving-frame-rate-with-ccspritesheetgraphics-gamescocossharpccspritesheetmd"></a>[Melhorando a taxa de quadros com CCSpriteSheet](~/graphics-games/cocossharp/ccspritesheet.md)

![Uma árvore de um CCSpriteSheet](images/ccspritesheet.png "uma árvore de um CCSpriteSheet")

`CCSpriteSheet` fornece funcionalidade para combinar e usar muitos arquivos de imagem em uma textura. Reduzir a contagem de textura pode melhorar os tempos de carregamento de um jogo e taxa de quadros.

## <a name="texture-caching-using-cctexturecachegraphics-gamescocossharptexture-cachemd"></a>[Cache de textura usando CCTextureCache](~/graphics-games/cocossharp/texture-cache.md)

![Uma representação de como CocosSharp armazena em cache imagens](images/texture-cache.png "uma representação de como CocosSharp armazena em cache imagens")

Do CocosSharp `CCTextureCache` classe fornece uma maneira padrão para organizar, armazenar em cache e descarregar conteúdo. 

## <a name="2d-math-with-cocossharpgraphics-gamescocossharpmathmd"></a>[Matemática 2D com CocosSharp](~/graphics-games/cocossharp/math.md)

![Uma imagem que está sendo girada](images/math.png "uma imagem que está sendo girada")

Este guia aborda matemática 2D para desenvolvimento de jogos. Ele usa CocosSharp para mostrar como executar tarefas comuns de desenvolvimento de jogos e explica a matemática por trás dessas tarefas.

## <a name="performance-and-visual-effects-with-ccrendertexturegraphics-gamescocossharpccrendertexturemd"></a>[Desempenho e efeitos visuais com CCRenderTexture](~/graphics-games/cocossharp/ccrendertexture.md)

![Um sprite de um jogo](images/ccrendertexture.png "um sprite de um jogo")

O `CCRenderTexture` classe fornece funcionalidade para a renderização de vários objetos de CocosSharp em uma textura única. Depois de criada, `CCRenderTexture` instâncias podem ser usadas para renderizar elementos gráficos com eficiência e implementar efeitos visuais.
