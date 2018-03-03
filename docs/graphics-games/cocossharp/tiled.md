---
title: Usando lado a lado com CocosSharp
description: "Lado a lado é uma poderosa, flexível e o aplicativo consolidado para a criação de bloco ortogonal e isométrico mapeia para jogos. CocosSharp fornece integração interna para o formato de arquivo nativo do lado a lado."
ms.topic: article
ms.prod: xamarin
ms.assetid: 804C042C-F62A-4E6C-B10F-06528637F0E2
ms.technology: xamarin-cross-platform
author: charlespetzold
ms.author: chape
ms.date: 03/28/2017
ms.openlocfilehash: 5a469a372a9299712be7aef46c51f3d644946535
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="using-tiled-with-cocossharp"></a>Usando lado a lado com CocosSharp

_Lado a lado é uma poderosa, flexível e o aplicativo consolidado para a criação de bloco ortogonal e isométrico mapeia para jogos. CocosSharp fornece integração interna para o formato de arquivo nativo do lado a lado._

O *lado a lado* aplicativo é um padrão para a criação de *bloco mapas* para uso em desenvolvimento de jogos. Este guia guiará como utilizar um arquivo existente de .tmx (arquivo criado pelo lado a lado) e usá-lo em um jogo de CocosSharp. Especificamente, este guia aborda:

 - A finalidade de mapas de bloco
 - Trabalhando com arquivos de .tmx
 - Considerações para a renderização de arte de pixel
 - Usando propriedades de bloco em tempo de execução

Quando terminar, terá a seguinte demonstração:

![](tiled-images/image1.png "O aplicativo de demonstração criado seguindo as etapas neste guia")


# <a name="the-purpose-of-tile-maps"></a>A finalidade de mapas de bloco

Mapas de bloco tem existido no desenvolvimento de jogos para décadas, mas ainda normalmente são usados em jogos 2D para eficiência e esthetics. Mapas de bloco são capazes de atingir um alto nível de eficiência por meio de seu uso de conjuntos de bloco – usado pelo bloco mapas de imagem de origem. Um conjunto de bloco é uma coleção de imagens combinados em um arquivo. Embora os conjuntos de bloco consultem imagens usadas em mapas de bloco, arquivos que contêm várias imagens menores também são chamados de folhas de entidade gráfica ou entidade gráfica mapas no desenvolvimento de jogos. É possível visualizar como conjuntos de bloco são usados com a adição de uma grade para o conjunto de bloco que será usado em nossa demonstração:

![](tiled-images/image2.png "Um modo de exibição visualizado de como conjuntos de bloco são usados com a adição de uma grade para o conjunto de bloco que será usado na demonstração")

Mapas de bloco organizar os blocos individuais de conjuntos de bloco. Devemos observar que cada mapa lado a lado não precisa armazenar sua própria cópia do bloco definido – em vez disso, vários mapas de bloco podem referenciar o mesmo conjunto de bloco. Isso significa que, além do conjunto de bloco, mapas de bloco exigem pouca memória. Isso permite a criação de um grande número de peças de mapas, mesmo quando eles são usados para criar uma área de jogo grande, como um [rolagem platformer](http://en.wikipedia.org/wiki/Platform_game) ambiente. A seguir mostra as combinações possíveis usando o mesmo conjunto de bloco:

![](tiled-images/image3.png "Esta imagem mostra as combinações possíveis usando o mesmo conjunto de bloco")

![](tiled-images/image4.png "Esta imagem mostra as combinações possíveis usando o mesmo conjunto de bloco")


# <a name="working-with-tmx-files"></a>Trabalhando com arquivos de .tmx

O formato de arquivo .tmx é um arquivo XML criado pelo aplicativo lado a lado, que pode ser [baixado gratuitamente no site do lado a lado](http://www.mapeditor.org/). O formato de arquivo .tmx armazena as informações de mapas de bloco. Normalmente, um jogo terá um arquivo de .tmx para cada área separada ou nível.

Este guia se concentra em como usar arquivos .tmx existentes em CocosSharp; No entanto, tutoriais adicionais podem ser encontradas online, inclusive [esta introdução para o editor de mapa lado a lado](http://gamedevelopment.tutsplus.com/tutorials/introduction-to-tiled-map-editor--gamedev-2838).

Você precisará descompacte o [arquivo zip conteúdo](https://github.com/xamarin/mobile-samples/blob/master/BouncingGame/Resources/Tiled.zip?raw=true) usá-la em nosso jogo. A primeira coisa a observar é que os mapas de bloco usam ambos os arquivos de .tmx (dungeon.tmx), bem como um ou mais arquivos de imagem que definem o bloco de conjunto de dados (dungeon_1.png). Para adicionar o jogo precisa incluir desses arquivos para carregar .tmx em tempo de execução, e para o projeto **conteúdo** pasta (que está contido no **ativos** pasta em projetos Android). Especificamente, adicionar os arquivos para uma pasta chamada **tilemaps** dentro de **conteúdo** pasta:

![](tiled-images/image5.png "Adicionar os arquivos para uma pasta chamada tilemaps dentro da pasta de conteúdo")

O **dungeon.tmx** arquivo agora pode ser carregado no tempo de execução em um `CCTileMap` objeto. Em seguida, modifique o `GameLayer` (ou o objeto de contêiner raiz equivalente) para conter um `CCTileMap` instância e adicioná-lo como um filho:


```csharp
public class GameLayer : CCLayer
{
    CCTileMap tileMap;

    public GameLayer ()
    {
    }

    protected override void AddedToScene ()
    {
        base.AddedToScene ();

        tileMap = new CCTileMap ("tilemaps/dungeon.tmx");
        this.AddChild (tileMap);
    }
} 
```

Se executarmos o jogo veremos o mapa de bloco aparecerá no canto inferior esquerdo da tela:

![](tiled-images/image6.png "Se o jogo for executado, o mapa de bloco aparecerá no canto inferior esquerdo da tela")


# <a name="considerations-for-rendering-pixel-art"></a>Considerações para a renderização de arte de Pixel

Arte de pixel, no contexto do desenvolvimento de jogos de vídeo, refere-se a arte visual 2D que é normalmente criado pelo disponível e geralmente é baixa resolução. Arte de pixel pode ser restrictively tempo para criar, para conjuntos de bloco de arte de pixel geralmente incluem blocos de baixa resolução, como 16 ou 32 pixels de largura e altura. Se não dimensionado em tempo de execução, arte de pixel geralmente é muito pequeno para tablets e celulares mais modernos.

É possível ajustar dimensões exibidas no arquivo de GameAppDelegate.cs do nosso jogo, em que vamos adicionar uma chamada para `CCScene.SetDefaultDesignResolution`:


```csharp
 public override void ApplicationDidFinishLaunching (CCApplication application, CCWindow mainWindow)
{
    application.PreferMultiSampling = false;
    application.ContentRootDirectory = "Content";
    application.ContentSearchPaths.Add ("animations");
    application.ContentSearchPaths.Add ("fonts");
    application.ContentSearchPaths.Add ("sounds");

    CCSize windowSize = mainWindow.WindowSizeInPixels;

    float desiredWidth = 1024.0f;
    float desiredHeight = 768.0f;
    
    // This will set the world bounds to be (0,0, w, h)
    // CCSceneResolutionPolicy.ShowAll will ensure that the aspect ratio is preserved
    CCScene.SetDefaultDesignResolution (desiredWidth, desiredHeight, CCSceneResolutionPolicy.ShowAll);
    
    // Determine whether to use the high or low def versions of our images
    // Make sure the default texel to content size ratio is set correctly
    // Of course you're free to have a finer set of image resolutions e.g (ld, hd, super-hd)
    if (desiredWidth < windowSize.Width)
    {
        application.ContentSearchPaths.Add ("images/hd");
        CCSprite.DefaultTexelToContentSizeRatio = 2.0f;
    }
    else
    {
        application.ContentSearchPaths.Add ("images/ld");
        CCSprite.DefaultTexelToContentSizeRatio = 1.0f;
    }

    // New code:
    CCScene.SetDefaultDesignResolution (380, 240, CCSceneResolutionPolicy.ShowAll);

    CCScene scene = new CCScene (mainWindow);
    GameLayer gameLayer = new GameLayer ();

    scene.AddChild (gameLayer);

    mainWindow.RunWithScene (scene);
} 
```

Para obter mais informações sobre `CCSceneResolutionPolicy`, consulte nosso guia em [tratamento resoluções CocosSharp](~/graphics-games/cocossharp/resolutions.md).

Se executarmos o jogo agora, veremos o jogo ocupem tela inteira do nosso dispositivo:

![](tiled-images/image7.png "A tela inteira do dispositivo ocupem jogo")

Por fim, desejamos desabilitar suavização em nosso mapa lado a lado. O `Antialiased` a propriedade se aplica a um efeito obscura durante a renderização de objetos que são ampliar. Suavização pode ser útil para reduzir a aparência de como pixels de objetos gráficos, mas também pode introduzir o seus próprio artefatos de renderização. Especificamente, a suavização Desfoca o conteúdo de cada bloco. No entanto, as extremidades de cada lado a lado não são indefinidas, o que torna os blocos individuais se destacam em vez de mesclagem com blocos adjacentes. Também deve observar que jogos de arte de pixel geralmente preservam sua aparência como pixels para manter um *Retrô* sinta-se.

Definir `Antialiased` para `false` após a criação de `tileMap`:


```csharp
protected override void AddedToScene ()
{
    base.AddedToScene ();

    tileMap = new CCTileMap ("tilemaps/dungeon.tmx");

    // new code:
    tileMap.Antialiased = false;

    this.AddChild (tileMap);
} 
```

Agora o mapa do bloco não aparecerão confuso:

![](tiled-images/image8.png "Agora o mapa do bloco não aparecerão indefinido")


# <a name="using-tile-properties-at-runtime"></a>Usando propriedades de bloco em tempo de execução

Até agora, temos um `CCTileMap` carregando um arquivo .tmx e exibi-lo, mas não temos como interagir com ele. Especificamente, determinados blocos (por exemplo, nossa tórax Tesouro) precisam ter lógica personalizada. Veremos passo a passo sobre como detectar propriedades personalizadas de bloco e várias maneiras de reagir a essas propriedades depois de identificado em tempo de execução.

Antes que podemos gravar qualquer código, precisaremos adicionar propriedades para o mapa de lado a lado a lado a lado. Para fazer isso, abra o arquivo dungeon.tmx no programa de lado a lado. Certifique-se de abrir o arquivo que está sendo usado no projeto do jogo.

Depois de abrir, selecione o tórax Tesouro no bloco definido para exibir suas propriedades:

![](tiled-images/image9.png "Depois de aberta, selecione o tórax Tesouro no bloco definido para exibir suas propriedades")

Se as propriedades de tórax Tesouro não aparecer, clique com botão direito no tórax Tesouro e selecione **propriedades da peça**:

![](tiled-images/image10.png "Se as propriedades de tórax Tesouro não aparecer, clique com botão direito no tórax Tesouro e selecione Propriedades de bloco")

Propriedades de lado a lado são implementadas com um nome e um valor. Para adicionar uma propriedade, clique o  **+**  botão, digite o nome **IsTreasure**, clique em **Okey**, em seguida, insira o valor **true**: 

![](tiled-images/image11.png "Para adicionar uma propriedade, clique no botão, digite o nome IsTreasure, clique em Okey e insira o valor true")

Não se esqueça de salvar o arquivo .tmx depois de modificar as propriedades.

Por fim, vamos adicionar código para procurar por nossa propriedade recém-adicionados. Podemos fará um loop em cada `CCTileMapLayer` (o mapa tem 2 camadas), em seguida, por meio de cada linha e coluna para procurar todos os blocos que têm o `IsTreasure` propriedade:


```csharp
public class GameLayer : CCLayer
{
    CCTileMap tileMap;

    public GameLayer ()
    {
    }

    protected override void AddedToScene ()
    {
        base.AddedToScene ();

        tileMap = new CCTileMap ("tilemaps/dungeon.tmx");

        // new code:
        tileMap.Antialiased = false;

        this.AddChild (tileMap);

        HandleCustomTileProperties (tileMap);
    }

    void HandleCustomTileProperties(CCTileMap tileMap)
    {
        // Width and Height are equal so we can use either
        int tileDimension = (int)tileMap.TileTexelSize.Width;

        // Find out how many rows and columns are in our tile map
        int numberOfColumns = (int)tileMap.MapDimensions.Size.Width;
        int numberOfRows = (int)tileMap.MapDimensions.Size.Height;

        // Tile maps can have multiple layers, so let's loop through all of them:
        foreach (CCTileMapLayer layer in tileMap.TileLayersContainer.Children)
        {
            // Loop through the columns and rows to find all tiles
            for (int column = 0; column < numberOfColumns; column++)
            {
                // We're going to add tileDimension / 2 to get the position
                // of the center of the tile - this will help us in 
                // positioning entities, and will eliminate the possibility
                // of floating point error when calculating the nearest tile:
                int worldX = tileDimension * column + tileDimension / 2;
                for (int row = 0; row < numberOfRows; row++)
                {
                    // See above on why we add tileDimension / 2
                    int worldY = tileDimension * row + tileDimension / 2;

                    HandleCustomTilePropertyAt (worldX, worldY, layer);
                }
            }
        }
    }

    void HandleCustomTilePropertyAt(int worldX, int worldY, CCTileMapLayer layer)
    {
        CCTileMapCoordinates tileAtXy = layer.ClosestTileCoordAtNodePosition (new CCPoint (worldX, worldY));

        CCTileGidAndFlags info = layer.TileGIDAndFlags (tileAtXy.Column, tileAtXy.Row);

        if (info != null)
        {
            Dictionary<string, string> properties = null;

            try
            {
                properties = tileMap.TilePropertiesForGID (info.Gid);
            }
            catch
            {
                // CocosSharp 
            }

            if (properties != null && properties.ContainsKey ("IsTreasure") && properties["IsTreasure"] == "true" )
            {
                layer.RemoveTile (tileAtXy);

                // todo: Create a treasure chest entity
            }
        }
    }
} 
```

A maioria do código é auto-explicativo, mas deve discutiremos a manipulação de Tesouro blocos. Nesse caso estamos removendo qualquer blocos que são identificados como peitos Tesouro. Isso ocorre porque peitos Tesouro provavelmente serão necessário código personalizado em tempo de execução para colisão do efeito e para conceder o player de conteúdo de Tesouro quando aberto. Além disso, talvez seja necessário o Tesouro reagir a ser aberto (alterando a aparência visual) e pode ter lógica para somente apareça quando tudo na tela inimigos tem sido desfeitos.

Em outras palavras, o tórax Tesouro beneficiará sendo uma entidade em vez de ser um bloco simple no `CCTileMap`. Para obter mais informações sobre entidades de jogos, consulte o [guia de entidades em CocosSharp](~/graphics-games/cocossharp/entities.md).


# <a name="summary"></a>Resumo

Este passo a passo aborda como carregar arquivos de .tmx criados pelo lado a lado em um aplicativo CocosSharp. Ele mostra como modificar a resolução de aplicativo para levar em conta a arte de pixel de baixa resolução e como localizar blocos por suas propriedades executar lógica personalizada, como a criação de instâncias de entidade.

## <a name="related-links"></a>Links relacionados

- [Site lado a lado](http://www.mapeditor.org/)
- [Conteúdo zip](https://github.com/xamarin/mobile-samples/blob/master/BouncingGame/Resources/Tiled.zip?raw=true)
