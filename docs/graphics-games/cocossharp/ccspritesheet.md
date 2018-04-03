---
title: Melhorando a taxa de quadros com CCSpriteSheet
description: CCSpriteSheet fornece funcionalidade para combinar e usar muitos arquivos de imagem em uma textura. Reduzir a contagem de textura pode melhorar os tempos de carregamento do jogo e taxa de quadros.
ms.topic: article
ms.prod: xamarin
ms.assetid: A1334030-750C-4C60-8B84-1A8A54B0D00E
ms.technology: xamarin-cross-platform
author: charlespetzold
ms.author: chape
ms.date: 03/24/2017
ms.openlocfilehash: 7e2bb5b98b5c93fb625ce645692d8a3ccb3d143b
ms.sourcegitcommit: 4f1b508caa8e7b6ccf85d167ea700a5d28b0347e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2018
---
# <a name="improving-frame-rate-with-ccspritesheet"></a>Melhorando a taxa de quadros com CCSpriteSheet

_CCSpriteSheet fornece funcionalidade para combinar e usar muitos arquivos de imagem em uma textura. Reduzir a contagem de textura pode melhorar os tempos de carregamento do jogo e taxa de quadros._

Muitos jogos exigem esforços de otimização está funcionando e carregar rapidamente em hardware móvel. O `CCSpriteSheet` classe pode ajudar a solucionar muitos problemas de desempenho comuns encontrados por CocosSharp jogos. Este guia aborda problemas comuns de desempenho e como resolvê-los usando o `CCSpriteSheet` classe.


## <a name="what-is-a-sprite-sheet"></a>O que é uma folha de entidade gráfica?

Um *folha da entidade gráfica*, que pode também ser chamado como uma *atlas textura*, é uma imagem que combina várias imagens em um arquivo. Isso pode melhorar o desempenho de tempo de execução, bem como os tempos de carregamento de conteúdo.

Por exemplo, a imagem a seguir é uma folha de entidade gráfica simples criada por três imagens separadas. As imagens individuais podem ser de qualquer tamanho, e a folha da entidade gráfica resultante não é necessária para ser totalmente preenchido:

![](ccspritesheet-images/image1.png "As imagens individuais podem ser de qualquer tamanho, e a folha da entidade gráfica resultante não é necessária para ser totalmente preenchido")


### <a name="render-states"></a>Renderizar Estados

Objetos CocosSharp Visual (como `CCSprite`) simplificar o código de renderização por código de renderização API gráfico tradicional como MonoGame ou OpenGL, que exigem a criação de buffers de vértices (conforme descrito no tópico a [desenho gráficos 3D com Vértices em MonoGame](~/graphics-games/monogame/3d/part2.md) guia). Apesar de sua simplicidade, CocosSharp não eliminar o custo da configuração de *renderizar estados*, que são o número de vezes que o código de renderização deve alternar texturas ou outros estados de renderização.

O código interno do CocosSharp renderiza cada elemento visual sequencialmente, percorrendo a árvore visual iniciando com o atual `CCScene`. Por exemplo, considere a cena a seguir:

![](ccspritesheet-images/image2.png "Considere esta cena")

CocosSharp geraria quatro estrelas na sequência:

![](ccspritesheet-images/image3.png "CocosSharp geraria quatro estrelas na sequência")

Desde que cada `CCSprite` usa a mesma textura, CocosSharp pode agrupar todos os quatro estrelas. Esse código requer processamento apenas uma atribuição de estado (atribuição da textura estrela) por quadro. Este cenário é muito eficiente.

Obviamente, jogos muito poucos usam apenas uma imagem. A cena a seguir apresenta um gráfico de planeta:

![](ccspritesheet-images/image4.png "Esta cena apresenta um gráfico do planeta")

O ideal é CocosSharp deve desenhar todos os objetos usando uma imagem primeiro (como estrelas), em seguida, o restante do sprites usando outra imagem (o planeta):

![](ccspritesheet-images/image5.png "O ideal é CocosSharp deve desenhar todos os objetos usando uma imagem primeiro como estrelas, em seguida, o restante dos sprites usando outra imagem do planeta")

A ordenação acima requer dois estados de renderizar: um em um asterisco, primeiro o planeta primeiro.

Se todos os `CCSprite` instâncias são filhos do mesmo `CCNode`, em seguida, CocosSharp otimizar a ordem de desenho para reduzir as alterações de estado de processamento. Se, por outro lado, o `CCSprite` instâncias são organizadas, de modo que CocosSharp é incapaz de otimizar o processamento (como se elas fazem parte de outra entidade `CCNode` instâncias), em seguida, a ordem não pode ser o ideal. O exemplo a seguir mostra a ordem de desenho possíveis pior, resultando em cinco estados de processamento:

![](ccspritesheet-images/image6.png "Isso mostra a ordem de desenho possíveis pior, resultando em cinco estados de renderização")

Estados de processamento podem ser difíceis de otimizar porque a ordem de desenho deve obedecer a árvore visual do `CCNode` instâncias. Essa árvore geralmente é estruturada para ser fácil trabalhar com (como entidades que contém seus filhos visual) ou organizada devido ao layout visual desejado, conforme definido por um artista.

Claro, a situação ideal é ter um estado de processamento único, mesmo tendo várias imagens. Jogos CocosSharp podem fazer isso por combinar todas as imagens em um único arquivo, o carregamento de um arquivo (juntamente com seu que acompanha **. plist** arquivo) em um `CCSpriteSheet`. Usando o `CCSpriteSheet` classe torna-se ainda mais importante para jogos que têm um grande número de imagens, ou que tenham muito complicado layouts. 

### <a name="load-times"></a>Tempos de carregamento

Combinando várias imagens em um arquivo também melhora os tempos de carregamento do jogo para vários motivos:

 - Combinar várias imagens em um único arquivo pode reduzir o número total de pixels usada por meio de remessa eficiente
 - Carregando arquivos menos significa menos sobrecarga por arquivo, como análise dos cabeçalhos. PNG
 - Carregando arquivos menos requer menos tempo de busca, que é importante para a mídia de disco como DVDs e unidades de disco rígido do computador tradicional

## <a name="using-ccspritesheet-in-code"></a>Usando CCSpriteSheet em código

Para criar um `CCSpriteSheet` instância, o código deve fornecer uma imagem e um arquivo que define as regiões de imagem a ser usado para cada quadro. A imagem pode ser carregada como um **. PNG** ou **.xnb** arquivo (se usar o [Pipeline conteúdo](~/graphics-games/cocossharp/content-pipeline/index.md)). O arquivo de definição de quadros é um **. plist** arquivo que pode ser criado manualmente ou *TexturePacker* (que discutiremos abaixo).

O aplicativo de exemplo que [pode ser baixado em](https://developer.xamarin.com/samples/mobile/SpriteSheetDemo/), cria o `CCSpriteSheet` de um **. PNG** e **. plist** arquivo usando o seguinte código:

```csharp
CCSpriteSheet sheet = new CCSpriteSheet ("sheet.plist", "sheet.png"); 
```

Uma vez carregado, o `CCSpriteSheet` contém um `List` de `CCSpriteFrame` instâncias – cada instância correspondente a uma das imagens de origem usadas para criar a planilha inteira. No caso do **SpriteSheetDemo** projeto, o `CCSpriteSheet` contém três imagens. O **. plist** arquivo pode ser inspecionado no Visual Studio para Mac ou em qualquer editor de texto para ver quais imagens disponíveis. Se vemos o **. plist** em um editor de texto, podemos ver os três quadros (seções omitidos para enfatizar os nomes de chave):


```csharp
...
<dict>
    <key>frames</key>
    <dict>
        <key>farBackground.png</key>
        ...
        <key>foreground.png</key>
        ...
<key>forestBackground.png</key>
...
```

Podemos usar o `Find` método localizar quadros por nome, da seguinte maneira (código omitido para enfatizar `CCSpriteFrame` uso):


```csharp
CCSpriteFrame frame;
...
frame = sheet.Frames.Find(item=>item.TextureFilename == "farBackground.png"); 
CCSprite sprite = new CCSprite (frame); 
...
```

Como o `CCSprite` construtor pode levar uma `CCSpriteFrame` parâmetro, o código nunca precisa investigar os detalhes do `CCSpriteFrame`, como quais textura usa ou a região da imagem na folha de entidade gráfica mestre.


## <a name="creating-a-sprite-sheet-plist"></a>Criando uma folha de entidade gráfica. plist

O arquivo. plist é um arquivo baseado em xml, que pode ser criado e editado manualmente. Da mesma forma, os programas de edição de imagem pode ser usada para combinar vários arquivos em um arquivo maior. Desde a criação e manutenção de folhas de entidade gráfica podem ser muito demoradas, veremos o programa TexturePacker que pode exportar os arquivos no formato CocosSharp. TexturePacker oferece um livre e uma versão de "Pro" e está disponível para Windows e Mac OS. O restante deste guia pode ser seguido usando a versão gratuita. 

É possível TexturePacker [baixado do site TexturePacker](https://www.codeandweb.com/texturepacker). Quando aberto, TexturePacker não tem um projeto carregado. A tela inicial permite que você adicione sprites, abrir projetos recentes (se outros projetos foram criados) e selecione o formato a ser usado para a folha da entidade gráfica. CocosSharp usa o formato de dados Cocos2D:

![](ccspritesheet-images/image7.png "CocosSharp usa o formato de dados Cocos2D")

Arquivos de imagem (como **. PNG**) podem ser adicionadas ao TexturePacker, arrastar-soltá-los do Windows Explorer no Windows ou o localizador no Mac. TexturePacker atualiza automaticamente a visualização de folha da entidade gráfica sempre que um arquivo é adicionado:

![](ccspritesheet-images/image8.png "TexturePacker atualiza automaticamente a visualização de folha da entidade gráfica sempre que um arquivo seja adicionado")

Para exportar uma folha de entidade gráfica, clique o **folha da entidade gráfica de publicação** botão e selecione um local para a folha da entidade gráfica. TexturePacker salvará um arquivo. plist e um arquivo de imagem.

Para usar os arquivos resultantes, adicione o. PNG e. plist para um projeto CocosSharp. Para obter informações sobre como adicionar arquivos a projetos de CocosSharp, consulte o [BouncingGame guia](~/graphics-games/cocossharp/bouncing-game.md). Depois que os arquivos são adicionados, podem ser carregados em um `CCSpriteSheet` conforme mostrado anteriormente no código acima:

```csharp
CCSpriteSheet sheet = new CCSpriteSheet ("sheet.plist", "sheet.png"); 
```

### <a name="considerations-for-maintaining-a-texturepacker-sprite-sheet"></a>Considerações para manter uma planilha de entidade gráfica TexturePacker

Como jogos são desenvolvidos, artistas podem adicionar, remover ou modificar arte. Qualquer alteração requer uma folha da entidade atualizada gráfica. As considerações a seguir podem facilitar a manutenção de folha da entidade gráfica:

 - Manter os arquivos originais (os arquivos usados para criar as entidade gráfica folhas) em uma pasta em seu projeto e verifique se que eles são adicionados ao controle de versão. Esses arquivos serão necessários para recriar a folha da entidade gráfica sempre que uma alteração é feita.
 - Não adicione os arquivos originais para o Visual Studio para Mac/Visual Studio, ou se eles são adicionados, defina o **ação de compilação** para **nenhum**. Se os arquivos são adicionados e têm o específico da plataforma **ação de compilação**, em seguida, eles desnecessariamente aumentará o tamanho de arquivo do aplicativo resultante.
 - Considere o uso de *inteligente pastas* em TexturePacker. Pastas inteligentes adicionam automaticamente imagens independentes para a folha da entidade gráfica. Esse recurso pode economizar muito tempo quando o desenvolvimento de jogos com um grande número de imagens. 

    ![](ccspritesheet-images/image9.png "Esse recurso pode economizar muito tempo quando o desenvolvimento de jogos com um grande número de imagens")
 - Fique atento tamanhos de textura de folha de entidade gráfica. Alguns dispositivos de hardware mais antigos phone não dá suporte a tamanhos de textura maiores do que 2048 x 2048. Além disso, uma imagem de 32 bits de 2048 x 2048 usa cerca de 17 MB de RAM – uma quantidade significativa de memória.
 - TexturePacker não incluir pastas em nomes de entidade gráfica por padrão, portanto possíveis conflitos de nome. É melhor decidir se incluir a pasta nomes ou não no início do desenvolvimento. Jogos maior considere o uso de nomes de pasta para evitar conflitos. Para incluir caminhos de pastas, clique em **Mostrar avançado** no **dados** seção e verificar **colocar nome da pasta**. 

    ![](ccspritesheet-images/image10.png "Para incluir caminhos de pastas, clique em Mostrar Avançado na seção de dados e verifique o nome da pasta coloque")

## <a name="summary"></a>Resumo

Este guia aborda como criar e usar o `CCSpriteSheet` classe. Ele também aborda como criar arquivos que podem ser carregados no `CCSpriteSheet` instâncias usando o programa TexturePacker.

## <a name="related-links"></a>Links relacionados

- [CCSpriteSheet](https://developer.xamarin.com/api/type/CocosSharp.CCSpriteSheet/)
- [Demonstração completa (exemplo)](https://developer.xamarin.com/samples/mobile/SpriteSheetDemo/)
