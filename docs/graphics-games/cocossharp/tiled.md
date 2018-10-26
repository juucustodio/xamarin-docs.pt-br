---
title: Usar lado a lado com CocosSharp
description: Lado a lado é uma poderosa, flexível e um aplicativo consolidado para a criação de bloco ortogonal e isométrico mapeia para jogos. CocosSharp fornece integração interna para o formato de arquivo nativo do lado a lado.
ms.prod: xamarin
ms.assetid: 804C042C-F62A-4E6C-B10F-06528637F0E2
author: conceptdev
ms.author: crdun
ms.date: 03/28/2017
ms.openlocfilehash: 4582b59a8a441c9e22761d498126898e66db08c1
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50117922"
---
# <a name="using-tiled-with-cocossharp"></a>Usar lado a lado com CocosSharp

_Lado a lado é uma poderosa, flexível e um aplicativo consolidado para a criação de bloco ortogonal e isométrico mapeia para jogos. CocosSharp fornece integração interna para o formato de arquivo nativo do lado a lado._

O *lado a lado* aplicativo é um padrão para a criação *bloco mapas* para uso em desenvolvimento de jogos. Este guia explica como usar um arquivo .tmx existente (arquivo criado pelo lado a lado) e usá-lo em um jogo de CocosSharp. Especificamente, este guia abordará:

 - A finalidade de mapas de bloco
 - Trabalhando com arquivos .tmx
 - Considerações para a renderização de arte de pixel
 - Usando propriedades de bloco em tempo de execução

Quando terminar, teremos a demonstração a seguir:

![](tiled-images/image1.png "O aplicativo de demonstração criado seguindo as etapas neste guia")


## <a name="the-purpose-of-tile-maps"></a>A finalidade de mapas de bloco

Mapas de bloco tem existido no desenvolvimento de jogos por décadas, mas ainda normalmente usados em jogos 2D para eficiência e esthetics. Mapas de bloco são capazes de atingir um alto nível de eficiência por meio do uso de conjuntos de bloco – a imagem de origem usada pelo bloco mapas. Um conjunto de bloco é uma coleção de imagens combinados em um arquivo. Embora consultem conjuntos de bloco de imagens usadas em mapas de bloco, arquivos que contêm várias imagens menores também são chamados de folhas de sprite ou sprite mapas no desenvolvimento de jogos. É possível visualizar como os conjuntos de bloco são usados pela adição de uma grade para o conjunto de bloco que usaremos em nossa demonstração:

![](tiled-images/image2.png "Um modo de exibição visualizado como conjuntos de bloco são usados com a adição de uma grade para o conjunto de bloco que será usado na demonstração")

Mapas de bloco organizar os blocos individuais de conjuntos de bloco. Devemos observar que cada mapa lado a lado não precisa armazenar sua própria cópia do bloco definido – em vez disso, vários mapas de bloco podem referenciar o mesmo conjunto de bloco. Isso significa que, além do conjunto de lado a lado, os mapas de bloco exigem muito pouca memória. Isso permite a criação de um grande número de mapas de bloco, mesmo quando eles são usados para criar uma área de grande jogo, como um [rolagem plataforma](http://en.wikipedia.org/wiki/Platform_game) ambiente. O exemplo a seguir mostra possíveis organizações que usam o mesmo conjunto de bloco:

![](tiled-images/image3.png "Esta imagem mostra possíveis organizações que usam o mesmo conjunto de bloco")

![](tiled-images/image4.png "Esta imagem mostra possíveis organizações que usam o mesmo conjunto de bloco")


## <a name="working-with-tmx-files"></a>Trabalhando com arquivos .tmx

O formato de arquivo .tmx é um arquivo XML criado pelo aplicativo lado a lado, que pode ser [baixado gratuitamente no site do lado a lado](http://www.mapeditor.org/). O formato de arquivo .tmx armazena as informações para mapas de bloco. Normalmente um jogo terá um arquivo de .tmx para cada área separada ou nível.

Este guia se concentra em como usar os arquivos existentes .tmx CocosSharp; No entanto, os tutoriais adicionais podem ser encontrados online, inclusive [esta introdução para o editor do mapa lado a lado](http://gamedevelopment.tutsplus.com/tutorials/introduction-to-tiled-map-editor--gamedev-2838).

Você precisará descompactar os [arquivo zip conteúdo](https://github.com/xamarin/mobile-samples/blob/master/BouncingGame/Resources/Tiled.zip?raw=true) usá-lo em nosso jogo. A primeira coisa a observar é que o arquivo .tmx (dungeon.tmx) do uso de mapas de lado a lado, bem como um ou mais arquivos de imagem que definem o bloco definir dados (dungeon_1.png). O jogo precisa incluir ambos os arquivos para carregar o .tmx em tempo de execução, portanto, adicione tanto para o projeto **conteúdo** pasta (que está contido na **ativos** pasta em projetos Android). Especificamente, adicione os arquivos em uma pasta chamada **tilemaps** dentro de **conteúdo** pasta:

![](tiled-images/image5.png "Adicione os arquivos em uma pasta chamada tilemaps dentro da pasta de conteúdo")

O **dungeon.tmx** agora é possível carregar o arquivo em tempo de execução em um `CCTileMap` objeto. Em seguida, modifique a `GameLayer` (ou objeto contêiner raiz equivalente) para conter um `CCTileMap` instância e adicioná-lo como um filho:


```csharp
public class GameLayer : CCLayer
{
    CCTileMap tileMap;

    public GameLayer ()
    {
    }

    protected override void AddedToScene ()
    {
        base.AddedToScene ();

        tileMap = new CCTileMap ("tilemaps/dungeon.tmx");
        this.AddChild (tileMap);
    }
} 
```

Se executarmos o jogo, veremos o mapa de bloco são exibidos no canto inferior esquerdo da tela:

![](tiled-images/image6.png "Se o jogo é executado, o mapa de bloco são exibidos no canto inferior esquerdo da tela")


## <a name="considerations-for-rendering-pixel-art"></a>Considerações para a renderização de arte de pixel

Arte de pixel, no contexto do desenvolvimento de jogos de vídeo, refere-se a arte de visual 2D que é normalmente criado pela mão e geralmente é baixa resolução. Arte de pixel pode ser restritivamente demorado para criar, portanto, conjuntos de bloco de arte de pixel geralmente incluem blocos de baixa resolução, como 16 ou 32 pixels de largura e altura. Se não tiver dimensionado em tempo de execução, arte de pixel costuma ser muito pequeno para a maioria dos telefones e tablets.

Podemos ajustar as dimensões exibidas no arquivo de GameAppDelegate.cs do nosso jogo, onde, adicionaremos uma chamada para `CCScene.SetDefaultDesignResolution`:


```csharp
 public override void ApplicationDidFinishLaunching (CCApplication application, CCWindow mainWindow)
{
    application.PreferMultiSampling = false;
    application.ContentRootDirectory = "Content";
    application.ContentSearchPaths.Add ("animations");
    application.ContentSearchPaths.Add ("fonts");
    application.ContentSearchPaths.Add ("sounds");

    CCSize windowSize = mainWindow.WindowSizeInPixels;

    float desiredWidth = 1024.0f;
    float desiredHeight = 768.0f;
    
    // This will set the world bounds to be (0,0, w, h)
    // CCSceneResolutionPolicy.ShowAll will ensure that the aspect ratio is preserved
    CCScene.SetDefaultDesignResolution (desiredWidth, desiredHeight, CCSceneResolutionPolicy.ShowAll);
    
    // Determine whether to use the high or low def versions of our images
    // Make sure the default texel to content size ratio is set correctly
    // Of course you're free to have a finer set of image resolutions e.g (ld, hd, super-hd)
    if (desiredWidth < windowSize.Width)
    {
        application.ContentSearchPaths.Add ("images/hd");
        CCSprite.DefaultTexelToContentSizeRatio = 2.0f;
    }
    else
    {
        application.ContentSearchPaths.Add ("images/ld");
        CCSprite.DefaultTexelToContentSizeRatio = 1.0f;
    }

    // New code:
    CCScene.SetDefaultDesignResolution (380, 240, CCSceneResolutionPolicy.ShowAll);

    CCScene scene = new CCScene (mainWindow);
    GameLayer gameLayer = new GameLayer ();

    scene.AddChild (gameLayer);

    mainWindow.RunWithScene (scene);
} 
```

Para obter mais informações sobre `CCSceneResolutionPolicy`, consulte nosso guia sobre [tratamento resoluções no CocosSharp](~/graphics-games/cocossharp/resolutions.md).

Se executarmos o jogo agora, veremos o jogo ocupem tela inteira do nosso dispositivo:

![](tiled-images/image7.png "O jogo ocupem a tela inteira do dispositivo")

Finalmente, vai querer desativar a suavização em nosso mapa lado a lado. O `Antialiased` propriedade aplica-se um efeito de desfoque durante a renderização de objetos que são ampliados. A suavização pode ser útil para reduzir a aparência de pixelada de objetos gráficos, mas também pode introduzir a sua própria renderização de artefatos. Especificamente, a suavização Desfoca o conteúdo de cada bloco. No entanto, as bordas de cada lado a lado não são indefinidas, que faz com que os blocos individuais se destacam em vez de mesclagem com blocos adjacentes. Observe também que jogos de arte de pixel geralmente preservam sua aparência pixelada para manter uma *Retrô* sinta-se.

Definir `Antialiased` à `false` depois de construir o `tileMap`:


```csharp
protected override void AddedToScene ()
{
    base.AddedToScene ();

    tileMap = new CCTileMap ("tilemaps/dungeon.tmx");

    // new code:
    tileMap.Antialiased = false;

    this.AddChild (tileMap);
} 
```

Agora nosso mapa lado a lado não aparecerá desfocado:

![](tiled-images/image8.png "Agora o mapa de bloco não aparecerá desfocado")


## <a name="using-tile-properties-at-runtime"></a>Usando propriedades de bloco em tempo de execução

Até agora temos um `CCTileMap` carregando um arquivo de .tmx e exibi-lo, mas não temos nenhuma maneira de interagir com ele. Especificamente, determinados blocos (por exemplo, o nosso conjunto de ferramentas do Tesouro) precisam ter uma lógica personalizada. Vamos ver como detectar as propriedades do bloco personalizado, além de várias maneiras para reagir a essas propriedades depois de identificado em tempo de execução.

Antes, podemos escrever qualquer código, precisaremos adicionar propriedades ao nosso mapa de bloco por meio de lado a lado. Para fazer isso, abra o arquivo de dungeon.tmx no programa lado a lado. Certifique-se de abrir o arquivo que está sendo usado no projeto do jogo.

Depois de abrir, selecione o conjunto de ferramentas do Tesouro no bloco definido para exibir suas propriedades:

![](tiled-images/image9.png "Depois de abrir, selecione o conjunto de ferramentas do Tesouro no bloco definido para exibir suas propriedades")

Se as propriedades de conjunto de ferramentas do Tesouro não aparecer, clique duas vezes em que o conjunto de ferramentas do Tesouro e selecione **propriedades da peça**:

![](tiled-images/image10.png "Se as propriedades de conjunto de ferramentas do Tesouro não aparecer, clique duas vezes em que o conjunto de ferramentas do Tesouro e selecione Propriedades do bloco")

Propriedades de lado a lado são implementadas com um nome e um valor. Para adicionar uma propriedade, clique o **+** botão, digite o nome **IsTreasure**, clique em **Okey**, em seguida, insira o valor **true**: 

![](tiled-images/image11.png "Para adicionar uma propriedade, clique no botão, digite o nome IsTreasure, clique em Okey e insira o valor true")

Não se esqueça de salvar o arquivo .tmx depois de modificar as propriedades.

Por fim, vamos adicionar código para procurar nossa propriedade recém-adicionado. Podemos fará um loop em cada `CCTileMapLayer` (o nosso mapa tem camadas de 2), em seguida, em cada linha e coluna para procurar os blocos que têm o `IsTreasure` propriedade:


```csharp
public class GameLayer : CCLayer
{
    CCTileMap tileMap;

    public GameLayer ()
    {
    }

    protected override void AddedToScene ()
    {
        base.AddedToScene ();

        tileMap = new CCTileMap ("tilemaps/dungeon.tmx");

        // new code:
        tileMap.Antialiased = false;

        this.AddChild (tileMap);

        HandleCustomTileProperties (tileMap);
    }

    void HandleCustomTileProperties(CCTileMap tileMap)
    {
        // Width and Height are equal so we can use either
        int tileDimension = (int)tileMap.TileTexelSize.Width;

        // Find out how many rows and columns are in our tile map
        int numberOfColumns = (int)tileMap.MapDimensions.Size.Width;
        int numberOfRows = (int)tileMap.MapDimensions.Size.Height;

        // Tile maps can have multiple layers, so let's loop through all of them:
        foreach (CCTileMapLayer layer in tileMap.TileLayersContainer.Children)
        {
            // Loop through the columns and rows to find all tiles
            for (int column = 0; column < numberOfColumns; column++)
            {
                // We're going to add tileDimension / 2 to get the position
                // of the center of the tile - this will help us in 
                // positioning entities, and will eliminate the possibility
                // of floating point error when calculating the nearest tile:
                int worldX = tileDimension * column + tileDimension / 2;
                for (int row = 0; row < numberOfRows; row++)
                {
                    // See above on why we add tileDimension / 2
                    int worldY = tileDimension * row + tileDimension / 2;

                    HandleCustomTilePropertyAt (worldX, worldY, layer);
                }
            }
        }
    }

    void HandleCustomTilePropertyAt(int worldX, int worldY, CCTileMapLayer layer)
    {
        CCTileMapCoordinates tileAtXy = layer.ClosestTileCoordAtNodePosition (new CCPoint (worldX, worldY));

        CCTileGidAndFlags info = layer.TileGIDAndFlags (tileAtXy.Column, tileAtXy.Row);

        if (info != null)
        {
            Dictionary<string, string> properties = null;

            try
            {
                properties = tileMap.TilePropertiesForGID (info.Gid);
            }
            catch
            {
                // CocosSharp 
            }

            if (properties != null && properties.ContainsKey ("IsTreasure") && properties["IsTreasure"] == "true" )
            {
                layer.RemoveTile (tileAtXy);

                // todo: Create a treasure chest entity
            }
        }
    }
} 
```

A maioria do código é auto-explicativo, mas deve discutiremos a manipulação de Tesouro de blocos. Nesse caso, estamos removendo os blocos que são identificados como peitos Tesouro. Isso ocorre porque o Tesouro peitos provavelmente precisará de código personalizado em tempo de execução a colisão de efeito e recompense o conteúdo do Tesouro quando aberto com o player. Além disso, o Tesouro talvez seja necessário reagir a que está sendo aberto (alterando sua aparência visual) e pode ter lógica para somente que aparecem quando tudo na tela inimigos tem sido derrotados.

Em outras palavras, o conjunto de ferramentas do Tesouro irão se beneficiar sendo uma entidade em vez de ser um bloco simples no `CCTileMap`. Para obter mais informações sobre entidades de jogos, consulte o [guia de entidades em CocosSharp](~/graphics-games/cocossharp/entities.md).


## <a name="summary"></a>Resumo

Este passo a passo aborda como carregar arquivos de .tmx criados pelo lado a lado em um aplicativo de CocosSharp. Ele mostra como modificar a resolução de aplicativo para levar em conta a arte de pixel de resolução mais baixa e como encontrar blocos por suas propriedades para executar lógica personalizada, como a criação de instâncias de entidade.

## <a name="related-links"></a>Links relacionados

- [Site lado a lado](http://www.mapeditor.org/)
- [Zip de conteúdo](https://github.com/xamarin/mobile-samples/blob/master/BouncingGame/Resources/Tiled.zip?raw=true)
