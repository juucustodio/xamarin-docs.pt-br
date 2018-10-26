---
title: Introdução aos pipelines de conteúdo
description: Pipelines são aplicativos ou partes de aplicativos de conteúdo, que são usados para converter arquivos em um formato que pode ser carregado por projetos de jogos. O Pipeline de conteúdo MonoGame é uma implementação de pipeline de conteúdo específicos para a conversão de arquivos para projetos CocosSharp e MonoGame.
ms.prod: xamarin
ms.assetid: 40628B5F-FAF7-4FA7-A929-6C3FEA83F8EC
author: conceptdev
ms.author: crdun
ms.date: 03/27/2017
ms.openlocfilehash: 712c430fb6309ba0f5c3e573267c59e422de8ad2
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50104135"
---
# <a name="introduction-to-content-pipelines"></a>Introdução aos pipelines de conteúdo

_Pipelines são aplicativos ou partes de aplicativos de conteúdo, que são usados para converter arquivos em um formato que pode ser carregado por projetos de jogos. O Pipeline de conteúdo MonoGame é uma implementação de pipeline de conteúdo específicos para a conversão de arquivos para projetos CocosSharp e MonoGame._

Este artigo fornece um entendimento conceitual de pipelines de conteúdo, enfoca principalmente os *MonoGame Content Pipeline*, que é uma implementação de pipeline de conteúdo usada com CocosSharp e MonoGame.


## <a name="what-is-a-content-pipeline"></a>O que é um pipeline de conteúdo?

O termo *pipeline de conteúdo* é um termo geral para o processo de converter um arquivo de um formato para outro. O *entrada* do pipeline de conteúdo normalmente é um arquivo gerado por uma ferramenta de criação, como arquivos de imagem do Photoshop. Cria o pipeline de conteúdo a *saída* arquivo em um formato que pode ser carregado diretamente por um projeto de jogo. Normalmente, os arquivos de saída são otimizados para o carregamento rápido e reduziu o tamanho do disco.

Conteúdo de pipelines podem ser de linha de comando executáveis, dedicado de aplicativos baseados em GUI ou plug-ins inserido em outro aplicativo como o Visual Studio. Pipelines de conteúdo geralmente são executados antes de executa o jogo. Se o pipeline de conteúdo estiver associado a algum aplicativo como o Visual Studio, em seguida, ele pode executar durante o tempo de compilação. Se o pipeline de conteúdo é um aplicativo autônomo, em seguida, ele poderá executar quando explicitamente solicitado pelo usuário. O aplicativo ou a lógica responsável por converter um arquivo de entrada específico (como uma **. PNG**) para uma saída associada arquivo é conhecido como um *construtor*. 

É possível visualizar o caminho de um arquivo leva da criação ao que está sendo carregado em tempo de execução da seguinte maneira:

![](introduction-images/image1.png "O caminho de um arquivo leva da criação ao que está sendo carregado em tempo de execução é visualizado neste diagrama")

## <a name="why-use-a-content-pipeline"></a>Por que usar um pipeline de conteúdo?

Pipelines de conteúdo introduzir uma etapa extra entre o aplicativo de criação e o jogo, que pode aumentar o tempo de compilação e adicionam complexidade ao processo de desenvolvimento. Apesar dessas considerações, pipelines de conteúdo apresentam uma série de benefícios para o desenvolvimento de jogos:


### <a name="converting-to-a-format-understood-by-the-game"></a>Conversão em um formato entendido pelo jogo

CocosSharp e MonoGame fornecem métodos para carregar vários tipos de conteúdo; No entanto, o conteúdo deve ser formatado corretamente antes que está sendo carregado. A maioria dos tipos de conteúdo requerem um tipo de conversão antes que está sendo carregado. Por exemplo, os efeitos de som na **. wav** formato deve ser convertido em uma **.xnb** arquivo a ser carregado no tempo de execução, pois CocosSharp e MonoGame não dão suporte a carregamento a **. wav** formato de arquivo.


### <a name="converting-to-a-format-native-to-the-hardware"></a>Conversão em um formato nativo para o hardware

Um hardware diferente pode tratar conteúdo de forma diferente no tempo de execução. Por exemplo, jogos CocosSharp podem carregar arquivos de imagem ao criar um `CCSprite` instância. Embora o mesmo código pode ser usado para carregar os arquivos no iOS e Android, cada plataforma armazena o arquivo carregado diferente. Como consequência, o Pipeline de conteúdo MonoGame formatos de textura **.xnb** arquivos de forma diferente, dependendo da plataforma de destino.


### <a name="reducing-size-on-disk"></a>Reduzindo o tamanho em disco 

Pipelines podem ser usados para remover informações de conteúdo, que é útil em tempo de autor, mas não será necessário no tempo de execução. Arquivo original (entrado) pode armazenar todas as informações que podem ajudar a criadores de conteúdo manter o conteúdo existente, mas o arquivo de saída pode ser simplificado para manter o arquivo de um todo pequeno. Isso é especialmente útil para jogos móveis que são baixados em vez de ser distribuídos em mídia de instalação.


### <a name="reducing-load-time"></a>Reduzindo o tempo de carregamento

Jogos podem necessitar de modificações de conteúdo para melhorar o desempenho de tempo de execução, para melhorar os visuais ou adicionar novos recursos. Por exemplo, muitos jogos 3D calculam a iluminação uma vez, e usam o resultado deste cálculo durante a renderização de cenas complexas. Desde executar esses cálculos ao carregar o conteúdo pode ser proibitivamente caro o cálculo em vez disso, pode ser executado quando o jogo é construído. Os cálculos resultantes podem ser incluídos no conteúdo, permitindo que o conteúdo a ser carregado muito mais rapidamente do que seria possível. 


## <a name="xnb-file-extension"></a>extensão de arquivo xnb

O **.xnb** extensão de arquivo é a extensão para todos os arquivos produzidos pelo Pipeline de conteúdo de Monogame. Isso coincide com a extensão dos arquivos gerados pelo Pipeline de conteúdo do Microsoft XNA.

O **.xnb** extensão é usada independentemente do tipo de arquivo original. Em outras palavras, os arquivos de imagem (**. PNG**), arquivos de áudio (**wav**), e qualquer tipo de arquivo personalizado será ser enviado como **.xnb** quando passado pelo pipeline de conteúdo de arquivos. Uma vez que a extensão não pode ser usada para distinguir entre diferentes formatos, em seguida, CocosSharp e MonoGame métodos que carregam **.xnb** arquivos não espera que as extensões ao carregar o arquivo.

Arquivos de .xnb CocosSharp e MonoGame podem ser criados usando a ferramenta de Pipeline de Monogame que é abordada [neste passo a passo](~/graphics-games/cocossharp/content-pipeline/walkthrough.md).


## <a name="summary"></a>Resumo

Este artigo fornece uma visão geral e os benefícios de pipelines de conteúdo em geral, bem como uma introdução para o Pipeline de conteúdo do MonoGame.

## <a name="related-links"></a>Links relacionados

- [Documentação do MonoGame de Pipeline](http://www.monogame.net/documentation/?page=Pipeline)
