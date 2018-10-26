---
title: Melhorando a taxa de quadros com CCSpriteSheet
description: CCSpriteSheet fornece funcionalidade para combinar e usar muitos arquivos de imagem em uma textura. Reduzir a contagem de textura pode melhorar os tempos de carregamento de um jogo e taxa de quadros.
ms.prod: xamarin
ms.assetid: A1334030-750C-4C60-8B84-1A8A54B0D00E
author: conceptdev
ms.author: crdun
ms.date: 03/24/2017
ms.openlocfilehash: eab6153653a8c8df2068aaaf879d84d35473c541
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50123551"
---
# <a name="improving-frame-rate-with-ccspritesheet"></a>Melhorando a taxa de quadros com CCSpriteSheet

_CCSpriteSheet fornece funcionalidade para combinar e usar muitos arquivos de imagem em uma textura. Reduzir a contagem de textura pode melhorar os tempos de carregamento de um jogo e taxa de quadros._

Muitos jogos exigem esforços de otimização para execução perfeita do e carregar rapidamente em hardware móvel. O `CCSpriteSheet` classe pode ajudar a resolver muitos problemas de desempenho comuns encontrados por CocosSharp jogos. Este guia aborda problemas comuns de desempenho e como resolvê-los usando o `CCSpriteSheet` classe.


## <a name="what-is-a-sprite-sheet"></a>O que é uma folha sprite?

Um *folha sprite*, que pode também ser conhecido como um *atlas de textura*, é uma imagem que combina várias imagens em um arquivo. Isso pode melhorar o desempenho de tempo de execução, bem como os tempos de carregamento de conteúdo.

Por exemplo, a imagem a seguir é uma folha de sprite simples criada por três imagens separadas. As imagens individuais podem ser de qualquer tamanho e a folha de sprite resultante não é necessária para ser totalmente preenchido:

![](ccspritesheet-images/image1.png "As imagens individuais podem ser de qualquer tamanho e a folha de sprite resultante não é necessária para ser totalmente preenchido")


### <a name="render-states"></a>Renderizar Estados

Objetos visuais de CocosSharp (como `CCSprite`) simplificam o código de renderização por código de renderização API gráfico tradicional, como MonoGame ou OpenGL, que exigem a criação de buffers de vértice (conforme descrito no [desenhar gráficos 3D com Vértices no MonoGame](~/graphics-games/monogame/3d/part2.md) guia). Apesar de sua simplicidade, CocosSharp não elimina o custo da configuração *renderizar estados*, que são o número de vezes que o código de renderização deve alternar texturas ou outros estados de renderização.

Código de interno do CocosSharp renderiza cada elemento visual em sequência, percorrendo a árvore visual iniciando com o atual `CCScene`. Por exemplo, considere a cena do seguinte:

![](ccspritesheet-images/image2.png "Considere esta cena")

CocosSharp seria renderizadas quatro estrelas em sequência:

![](ccspritesheet-images/image3.png "CocosSharp seria renderizadas quatro estrelas em sequência")

Já que cada `CCSprite` usa a mesma textura, CocosSharp pode agrupar todas as quatro estrelas. Esse código requer atribuição de estado (atribuição de textura em estrela) apenas uma renderização por quadro. Esse cenário é muito eficiente.

É claro que, muito poucos jogos usam apenas uma imagem. A cena a seguir apresenta um gráfico do planeta:

![](ccspritesheet-images/image4.png "Esta cena apresenta um gráfico do planeta")

O ideal é que CocosSharp deve desenhar todos os sprites usando uma imagem pela primeira vez (por exemplo, as estrelas), em seguida, o restante dos sprites usando outra imagem (o planeta):

![](ccspritesheet-images/image5.png "O ideal é que CocosSharp deve desenhar todos os sprites usando uma imagem primeiro como estrelas, em seguida, o restante dos sprites usando outra imagem do planeta")

A ordenação acima exige que dois estados de renderizar: uma em um asterisco, primeiro o planeta primeiro.

Se todos os `CCSprite` instâncias são filhos do mesmo `CCNode`, em seguida, CocosSharp otimizará a ordem de desenho para reduzir as alterações de estado de renderização. Se, por outro lado, o `CCSprite` instâncias são organizadas, de modo que não é capaz de otimizar a renderização CocosSharp (por exemplo, se eles fazem parte da entidade diferente `CCNode` instâncias), em seguida, a ordem não pode ser ideal. O exemplo a seguir mostra a ordem de desenho possíveis pior, resultando em cinco estados de renderização:

![](ccspritesheet-images/image6.png "Isso mostra a ordem de desenho possíveis pior, resultando em cinco estados de renderização")

Estados de processamento podem ser difícil otimizar porque a ordem de desenho deve obedecer a árvore visual de `CCNode` instâncias. Esta árvore geralmente é estruturada para ser fácil de trabalhar com (como entidades que contém seus filhos visuais) ou organizada devido ao layout visual desejado, conforme definido por um artista.

Obviamente, a situação ideal é ter um estado de processamento único, apesar de ter várias imagens. Jogos de CocosSharp para fazer isso, combinando todas as imagens em um único arquivo e, em seguida, carregamento de um arquivo (juntamente com acompanham **. plist** arquivo) em um `CCSpriteSheet`. Usando o `CCSpriteSheet` classe se torna ainda mais importante para jogos que têm um grande número de imagens, ou que tenham muito complicado layouts. 

### <a name="load-times"></a>Tempos de carregamento

Também combinando várias imagens em um arquivo melhora os tempos de carregamento de um jogo para vários motivos:

 - Combinar várias imagens em um único arquivo pode reduzir o número total de pixels usada por meio de empacotamento eficiente
 - Carregar menos arquivos significa menos sobrecarga por arquivo, como analisar os cabeçalhos. PNG
 - Carregar menos arquivos requer menos tempo de busca, que é importante para mídia baseadas em disco, como DVDs e unidades de disco rígido do computador tradicional

## <a name="using-ccspritesheet-in-code"></a>Usando CCSpriteSheet em código

Para criar um `CCSpriteSheet` instância, o código deve fornecer uma imagem e um arquivo que define as regiões da imagem a ser usado para cada quadro. A imagem pode ser carregada como um **. PNG** ou **.xnb** arquivo (se usando o [Pipeline de conteúdo](~/graphics-games/cocossharp/content-pipeline/index.md)). O arquivo de definição de quadros é um **. plist** arquivo que pode ser criado manualmente ou *TexturePacker* (que abordaremos abaixo).

O aplicativo de exemplo, qual [pode ser baixado aqui](https://developer.xamarin.com/samples/mobile/SpriteSheetDemo/), cria a `CCSpriteSheet` de uma **. PNG** e **. plist** arquivo usando o seguinte código:

```csharp
CCSpriteSheet sheet = new CCSpriteSheet ("sheet.plist", "sheet.png"); 
```

Uma vez carregado, o `CCSpriteSheet` contém uma `List` de `CCSpriteFrame` instâncias – cada instância correspondente a uma das imagens de origem usadas para criar a planilha inteira. No caso do **SpriteSheetDemo** projeto, o `CCSpriteSheet` contém três imagens. O **. plist** arquivo pode ser inspecionado no Visual Studio para Mac ou em qualquer editor de texto para ver quais imagens estão disponíveis. Se podemos exibir o **. plist** em um editor de texto, podemos ver três quadros (seções omitidos para enfatizar os nomes de chave):


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

Podemos usar o `Find` método para localizar os quadros por nome, da seguinte maneira (código omitido para enfatizar `CCSpriteFrame` uso):


```csharp
CCSpriteFrame frame;
...
frame = sheet.Frames.Find(item=>item.TextureFilename == "farBackground.png"); 
CCSprite sprite = new CCSprite (frame); 
...
```

Uma vez que o `CCSprite` construtor pode levar uma `CCSpriteFrame` parâmetro, o código nunca deve investigar os detalhes do `CCSpriteFrame`, como qual textura, ele usa, ou na região da imagem na folha de sprite mestre.


## <a name="creating-a-sprite-sheet-plist"></a>Criando uma folha de sprite. plist

O arquivo. plist é um arquivo baseado em xml, que pode ser criado e editado manualmente. Da mesma forma, os programas de edição de imagem pode ser usada para combinar vários arquivos em um arquivo maior. Desde a criação e manutenção de folhas de sprite podem consumir muito tempo, vamos examinar o programa de TexturePacker que pode exportar os arquivos no formato CocosSharp. TexturePacker oferece um gratuita e uma versão "Profissional" e está disponível para Windows e Mac OS. O restante deste guia pode ser seguido ao usar a versão gratuita. 

É possível TexturePacker [baixado do site da TexturePacker](https://www.codeandweb.com/texturepacker). Quando aberto, TexturePacker não tem um projeto carregado. A tela inicial permite que você adicione sprites, abra projetos recentes (se outros projetos tem sido criados) e selecione o formato a ser usado para a folha sprite. CocosSharp usa o formato de dados Cocos2D:

![](ccspritesheet-images/image7.png "CocosSharp usa o formato de dados Cocos2D")

Arquivos de imagem (como **PNG**) pode ser adicionado à TexturePacker ao arrastar-soltá-los do Windows Explorer no Windows ou o Finder no Mac. TexturePacker atualiza automaticamente a visualização de folha de sprite, sempre que um arquivo for adicionado:

![](ccspritesheet-images/image8.png "TexturePacker atualiza automaticamente a visualização de folha de sprite sempre que um arquivo for adicionado")

Para exportar uma folha sprite, clique o **folha de sprite publicar** botão e selecione um local para a folha sprite. TexturePacker salvará um arquivo. plist e um arquivo de imagem.

Para usar os arquivos resultantes, adicione o. PNG e. plist para um projeto de CocosSharp. Para obter informações sobre como adicionar arquivos a projetos de CocosSharp, consulte o [BouncingGame guia](~/graphics-games/cocossharp/bouncing-game.md). Depois que os arquivos são adicionados, eles podem ser carregados em um `CCSpriteSheet` como foi mostrado anteriormente no código acima:

```csharp
CCSpriteSheet sheet = new CCSpriteSheet ("sheet.plist", "sheet.png"); 
```

### <a name="considerations-for-maintaining-a-texturepacker-sprite-sheet"></a>Considerações para manter uma folha de sprite TexturePacker

Como os jogos são desenvolvidos, artistas podem adicionar, remover ou modificar a arte. Qualquer alteração requer uma folha sprite atualizado. As considerações a seguir podem facilitar a manutenção de folha de sprite:

 - Mantenha os arquivos originais (os arquivos usados para criar as folhas de sprite) em uma pasta em seu projeto e certifique-se de que eles são adicionados ao controle de versão. Esses arquivos serão necessários para recriar a sprite sheet, sempre que uma alteração é feita.
 - Não adicione os arquivos originais para o Visual Studio para Mac/Visual Studio, ou se eles forem adicionados, defina as **ação de compilação** para **None**. Se os arquivos são adicionados e têm o específicos da plataforma **ação de compilação**, em seguida, eles aumentará desnecessariamente o tamanho do arquivo do aplicativo resultante.
 - Considere o uso de *inteligente pastas* em TexturePacker. Pastas inteligentes adicionam automaticamente todas as imagens independentes para a folha sprite. Esse recurso pode economizar muito tempo quando o desenvolvimento de jogos com um grande número de imagens. 

    ![](ccspritesheet-images/image9.png "Esse recurso pode economizar muito tempo quando o desenvolvimento de jogos com um grande número de imagens")
 - Fique de olho em tamanhos de textura de folha de sprite. Alguns hardware mais antigo do telefone não oferece suporte a tamanhos de textura maiores do que 2048 x 2048. Além disso, uma imagem de 32 bits do 2048x2048 usa quase 17 MB de RAM – uma quantidade significativa de memória.
 - TexturePacker não inclua pastas sprite nomes por padrão, portanto, conflitos de nome são possíveis. É melhor decidir se para incluir a pasta nomes ou não no início do desenvolvimento. Jogos de maiores devem considerar usar nomes de pasta para evitar conflitos. Para incluir caminhos de pasta, clique em **Mostrar opções avançadas** na **dados** seção e marque **nome da pasta Prepend**. 

    ![](ccspritesheet-images/image10.png "Para incluir caminhos de pasta, clique em Mostrar opções avançada na seção de dados e verifique o nome da pasta Prepend")

## <a name="summary"></a>Resumo

Este guia aborda como criar e usar o `CCSpriteSheet` classe. Ele também aborda como construir os arquivos que podem ser carregados no `CCSpriteSheet` instâncias usando o programa TexturePacker.

## <a name="related-links"></a>Links relacionados

- [CCSpriteSheet](https://developer.xamarin.com/api/type/CocosSharp.CCSpriteSheet/)
- [Demonstração completa (amostra)](https://developer.xamarin.com/samples/mobile/SpriteSheetDemo/)
