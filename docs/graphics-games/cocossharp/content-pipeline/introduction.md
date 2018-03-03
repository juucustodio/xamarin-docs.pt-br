---
title: "Introdução ao conteúdo Pipelines"
description: "Pipelines são aplicativos ou partes de aplicativos de conteúdo, que são usadas para converter arquivos em um formato que pode ser carregado por projetos do jogos. O Pipeline de conteúdo MonoGame é uma implementação de pipeline de conteúdo específicos para converter arquivos de projetos CocosSharp e MonoGame."
ms.topic: article
ms.prod: xamarin
ms.assetid: 40628B5F-FAF7-4FA7-A929-6C3FEA83F8EC
ms.technology: xamarin-cross-platform
author: charlespetzold
ms.author: chape
ms.date: 03/27/2017
ms.openlocfilehash: d51852924a4d909857659d38f8c19d520bb4c589
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="introduction-to-content-pipelines"></a>Introdução ao conteúdo Pipelines

_Pipelines são aplicativos ou partes de aplicativos de conteúdo, que são usadas para converter arquivos em um formato que pode ser carregado por projetos do jogos. O Pipeline de conteúdo MonoGame é uma implementação de pipeline de conteúdo específicos para converter arquivos de projetos CocosSharp e MonoGame._

Este artigo fornece uma compreensão conceitual de pipelines de conteúdo, principalmente se concentrar no *MonoGame conteúdo Pipeline*, que é uma implementação de pipeline conteúdo usada com CocosSharp e MonoGame.


# <a name="what-is-a-content-pipeline"></a>O que é um Pipeline de conteúdo?

O termo *pipeline conteúdo* é um termo geral para o processo de converter um arquivo de um formato para outro. O *entrada* do pipeline de conteúdo é geralmente um arquivo gerado por uma ferramenta de criação, como arquivos de imagem do Photoshop. O pipeline de conteúdo cria o *saída* arquivo em um formato que pode ser carregado diretamente por um projeto de jogo. Normalmente os arquivos de saída são otimizados para carregamento rápido e redução do tamanho do disco.

Conteúdo de pipelines podem ser de linha de comando executáveis, os aplicativos dedicados baseado em GUI ou plug-ins incorporado em outro aplicativo como o Visual Studio. Pipelines conteúdos geralmente são executados antes de executa o jogo. Se o pipeline de conteúdo é associado um aplicativo como o Visual Studio, ele poderá executar durante o tempo de compilação. Se o pipeline de conteúdo é um aplicativo autônomo, ele poderá executar quando explicitamente solicitado pelo usuário. O aplicativo ou lógica responsável pela conversão de um arquivo de entrada específico (como um **. PNG**) para uma saída de associado arquivo é conhecido como um *construtor*. 

É possível visualizar o caminho que usa um arquivo de criação e que está sendo carregado no tempo de execução da seguinte maneira:

![](introduction-images/image1.png "O caminho que usa um arquivo de criação que está sendo carregado no tempo de execução é visualizado nesse diagrama")

# <a name="why-use-a-content-pipeline"></a>Por que usar um Pipeline de conteúdo?

Pipelines conteúdos apresentam uma etapa extra entre o aplicativo de criação e o jogo, que pode aumentar o tempo de compilação e adicionar complexidade ao processo de desenvolvimento. Apesar dessas considerações, pipelines conteúdos introduzem uma série de benefícios para o desenvolvimento de jogos:


## <a name="converting-to-a-format-understood-by-the-game"></a>Converter em um formato entendido pelo jogo

CocosSharp e MonoGame fornecem métodos para carregar vários tipos de conteúdo. No entanto, o conteúdo deve ser formatado corretamente antes que está sendo carregado. A maioria dos tipos de conteúdo requerem algum tipo de conversão antes que está sendo carregado. Por exemplo, os efeitos de som no **. wav** formato deve ser convertido em uma **.xnb** arquivo a ser carregado no tempo de execução como CocosSharp e MonoGame não dão suporte ao carregar o **. wav** formato de arquivo.


## <a name="converting-to-a-format-native-to-the-hardware"></a>Converter em um formato do modo nativo para o Hardware

Hardware diferente pode tratar conteúdo diferente em tempo de execução. Por exemplo, jogos CocosSharp podem carregar arquivos de imagem ao criar um `CCSprite` instância. Embora o mesmo código pode ser usado para carregar os arquivos no iOS e Android, cada plataforma armazena o arquivo carregado diferente. Como consequência, o Pipeline de conteúdo MonoGame formatos de textura **.xnb** arquivos de maneira diferente dependendo da plataforma de destino.


## <a name="reducing-size-on-disk"></a>Reduzir o tamanho em disco 

Pipelines podem ser usados para remover informações de conteúdo, que é útil em tempo de criação, mas não é necessário em tempo de execução. O arquivo (entrado) original pode armazenar todas as informações que podem ajudar os criadores de conteúdo a manter o conteúdo existente, mas o arquivo de saída pode ser simplificado para manter o arquivo de jogo geral pequenos. Isso é especialmente útil para os jogos portáteis que são baixados em vez de ser distribuídos na mídia de instalação.


## <a name="reducing-load-time"></a>Reduzindo o tempo de carregamento

Jogos podem exigir modificações de conteúdo para melhorar o desempenho de tempo de execução, para aprimorar visuais, ou para adicionar novos recursos. Por exemplo, muitos jogos 3D calculam a iluminação uma vez e usam o resultado deste cálculo ao renderizar cenas complexas. Desde executar esses cálculos ao carregar o conteúdo pode ser extremamente dispendioso o cálculo em vez disso, pode ser executado quando o jogo é criado. Os cálculos resultantes podem ser incluídos no conteúdo, permitindo que o conteúdo a ser carregado muito mais rapidamente do que seria possível. 


# <a name="xnb-file-extension"></a>Extensão de arquivo XNB

O **.xnb** extensão de arquivo é a extensão para todos os arquivos gerados pelo Pipeline Monogame conteúdo. Isso coincide com a extensão dos arquivos gerados pelo Pipeline de conteúdo do Microsoft XNA.

O **.xnb** extensão é usada, independentemente do tipo de arquivo original. Em outras palavras, os arquivos de imagem (**. PNG**), arquivos de áudio (**. wav**), e qualquer tipo de arquivo personalizado será ser enviado como **.xnb** arquivos quando passado pelo pipeline de conteúdo. Como a extensão não pode ser usada para distinguir entre outros formatos de arquivo, em seguida, CocosSharp e MonoGame métodos que carregam **.xnb** arquivos não esperam extensões ao carregar o arquivo.

Arquivos de .xnb CocosSharp e MonoGame podem ser criados usando a ferramenta de Pipeline de Monogame que é abordada [neste passo a passo](~/graphics-games/cocossharp/content-pipeline/walkthrough.md).


# <a name="summary"></a>Resumo

Este artigo fornece uma visão geral e os benefícios de pipelines de conteúdo em geral, bem como uma introdução para o Pipeline de conteúdo MonoGame.

## <a name="related-links"></a>Links relacionados

- [Documentação do Pipeline de MonoGame](http://www.monogame.net/documentation/?page=Pipeline)
