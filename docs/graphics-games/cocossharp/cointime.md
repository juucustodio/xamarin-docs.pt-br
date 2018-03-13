---
title: "Detalhes de implementação de tempo de moeda"
description: "Este guia descreve detalhes da implementação do jogo moeda tempo, incluindo trabalhar com mapas de bloco, criar entidades, animação sprites e implementando colisão eficiente."
ms.topic: article
ms.prod: xamarin
ms.assetid: 5D285684-0417-4E16-BD14-2D1F6DEFBB8B
ms.technology: xamarin-cross-platform
author: charlespetzold
ms.author: chape
ms.date: 03/24/2017
ms.openlocfilehash: b3827d05ae9e563ae04dd4ab1e303577f6c9d82a
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/12/2018
---
# <a name="coin-time-implementation-details"></a>Detalhes de implementação de tempo de moeda

_Este guia descreve detalhes da implementação do jogo moeda tempo, incluindo trabalhar com mapas de bloco, criar entidades, animação sprites e implementando colisão eficiente._

Tempo de moeda é um platformer completo jogo para iOS e Android. O objetivo do jogo é coletar todas as moedas em um nível e, em seguida, acessar a porta de saída, evitando inimigos e obstáculos.

![](cointime-images/image1.png "O objetivo do jogo é coletar todas as moedas em um nível e, em seguida, acessar a porta de saída, evitando inimigos e obstáculos")

Este guia aborda detalhes da implementação em tempo de moeda, que abrangem os seguintes tópicos:

- [Trabalhando com arquivos TMX](#Working_with_TMX_Files)
- [Nível de carregamento](#Level_Loading)
- [Adicionar novas entidades](#Adding_New_Entities)
- [Entidades animadas](#Animated_Entities)


# <a name="content-in-coin-time"></a>Conteúdo em vez de moeda

Tempo de moeda é um projeto de exemplo que representa como um projeto CocosSharp completo pode ser organizado. Moeda de hora a estrutura tem como objetivo para simplificar a adição e a manutenção de conteúdo. Ele usa **.tmx** arquivos criados por [lado a lado](http://www.mapeditor.org) para níveis e arquivos XML para definir animações. Modificando ou adicionando um novo conteúdo pode ser obtido com um mínimo de esforço. 

Enquanto essa abordagem torna moeda um projeto eficaz para aprendizado e experimentação, ele também reflete como profissionais jogos são feitas. Este guia explica algumas das abordagens usadas para simplificar a adicionar e modificar o conteúdo.


# <a name="working-with-tmx-files"></a>Trabalhando com arquivos TMX

Níveis de tempo de moeda são definidos usando o formato de arquivo do .tmx, gerado pelo [lado a lado](http://www.mapeditor.org) editor do mapa lado a lado. Para obter uma discussão detalhada de como trabalhar com o lado a lado, consulte o [usando lado a lado com guia Cocos curva](~/graphics-games/cocossharp/tiled.md). 

Cada nível é definido em seu próprio arquivo .tmx contido a **CoinTime/ativos/conteúdo/níveis** pasta. Todos os níveis de tempo de moeda compartilham um arquivo de tileset, que é definido no **mastersheet.tsx** arquivo. Esse arquivo define as propriedades personalizadas para cada bloco, como se o bloco tem sólida colisão, ou se o bloco deve ser substituído por uma instância de entidade. O arquivo mastersheet.tsx permite que propriedades ser definido apenas uma vez e usado em todos os níveis. 


## <a name="editing-a-tile-map"></a>Edição de um mapa de bloco

Para editar um mapa de bloco, abra o arquivo de .tmx no lado a lado clicando duas vezes no arquivo .tmx ou abri-lo por meio do menu Arquivo do lado a lado. Tempo de moeda mapas de nível de bloco contém três camadas: 

- **Entidades** – essa camada contém blocos que serão substituídos por instâncias de entidades em tempo de execução. Exemplos incluem o player, moedas, inimigos e a porta final de nível.
- **Tipos de terrenos** – essa camada contém blocos que normalmente têm colisão sólido. Colisão sólida permite que o player para guiá-lo nesses blocos sem cair. Blocos com colisão sólida também podem atuar como paredes e tetos.
- **Plano de fundo** – a camada de plano de fundo contém blocos que são usados como plano de fundo estático. Essa camada não rola quando move a câmera em todo o nível, criando a aparência de profundidade por meio da parallax.

Como exploraremos posteriormente, o código de carregamento de nível de espera essas três camadas em todos os níveis de tempo de moeda.

### <a name="editing-terrain"></a>Edição de terreno
Blocos podem ser colocados clicando no **mastersheet** tileset e, em seguida, clicar no bloco de mapa. Por exemplo, para novos tipos de terrenos em um nível de pintura:

1. Selecione a camada de terreno
1. Clique no bloco para desenhar
1. Clique em ou por push e arraste o mapa para pintar o bloco


    ![](cointime-images/image2.png "Clique no bloco para desenhar 1")

 

Parte superior esquerda do tileset contém todos os tipos de terrenos em vez de moeda. Tipos de terrenos, que é sólido, incluem o **SolidCollision** propriedade, conforme mostrado nas propriedades de bloco à esquerda da tela:

![](cointime-images/image3.png "Tipos de terrenos, que é sólido, incluem a propriedade de SolidCollision, conforme mostrado nas propriedades de bloco à esquerda da tela")

### <a name="editing-entities"></a>Edição de entidades
Entidades podem ser adicionadas ou removidas de um nível – assim como tipos de terrenos. O **mastersheet** tileset tem todas as entidades colocadas sobre metade horizontalmente, assim eles podem não estar visíveis sem fazer rolagem à direita:

![](cointime-images/image4.png "O tileset mastersheet tem todas as entidades colocadas sobre metade horizontalmente, eles podem não estar visíveis sem fazer rolagem à direita")

Novas entidades devem ser colocadas no **entidades** camada.

![](cointime-images/image5.png "Novas entidades devem ser colocadas na camada de entidades")

Código de CoinTime procura o **EntityType** quando um nível é carregado para identificar os blocos que devem ser substituídos por entidades: 

![](cointime-images/image6.png "Código de CoinTime procura o EntityType quando um nível é carregado para identificar os blocos que devem ser substituídos por entidades")

Depois que o arquivo foi modificado e salvo, as alterações serão automaticamente exibida se o projeto é criado e execute:

![](cointime-images/image7.png "Depois que o arquivo foi modificado e salvo, as alterações serão automaticamente exibida se o projeto é criado e executado")


## <a name="adding-new-levels"></a>Adicionando novos níveis

O processo de adição de níveis em vez de moeda requer nenhuma alteração de código e apenas, algumas pequenas alterações ao projeto. Para adicionar um novo nível:

1. Abra a pasta de nível localizado em <**CoinTime Root > \CoinTime\Assets\Content\levels**
1. Copie e cole um dos níveis, como **level0.tmx**
1. Renomeie o novo arquivo de .tmx para que ela continua a sequência numérica nível com os níveis existentes, como **level8.tmx**
1. No Visual Studio ou Visual Studio para Mac, adicione o novo arquivo de .tmx para a pasta de níveis Android. Verifique se o arquivo usa o **AndroidAsset** ação de compilação.


    ![](cointime-images/image8.png "Verifique se o arquivo usa a ação de compilação AndroidAsset")


1. Adicione o novo arquivo de .tmx para a pasta de níveis de iOS. Certifique-se de vincular o arquivo de seu local original e verifique se que ele usa o **BundleResource** ação de compilação.


    ![](cointime-images/image9.png "Certifique-se de vincular o arquivo de seu local original e verifique se que ele usa a ação de compilação BundleResource")


O novo nível deve aparecer na tela de seleção de nível como nível 9 (nomes de arquivo de nível começam em 0, mas os botões de nível começam com o número 1):

![](cointime-images/image10.png "O novo nível deve aparecer na tela de seleção de nível como nível 9 Iniciar de nomes de arquivo de nível 0, mas os botões de nível começam com o número 1")


# <a name="level-loading"></a>Nível de carregamento

Como mostrado anteriormente, novos níveis não exigem nenhuma alteração no código – o jogo detecta automaticamente os níveis de se eles são nomeados corretamente e adicionados ao **níveis** pasta com a ação de compilação correta (**BundleResource**ou **AndroidAsset**).

A lógica para determinar o número de níveis está contida no `LevelManager` classe. Quando uma instância do `LevelManager` é construído (usando o padrão de singleton), o `DetermineAvailbleLevels` método é chamado:


```csharp
private void DetermineAvailableLevels()
{
    // This game relies on levels being named "levelx.tmx" where x is an integer beginning with
    // 1. We have to rely on MonoGame's TitleContainer which doesn't give us a GetFiles method - we simply
    // have to check if a file exists, and if we get an exception on the call then we know the file doesn't
    // exist. 
    NumberOfLevels = 0;
    while (true)
    {
        bool fileExists = false;
        try
        {
            using(var stream = TitleContainer.OpenStream("Content/levels/level" + NumberOfLevels + ".tmx"))
            {
            }
            // if we got here then the file exists!
            fileExists = true;
        }
        catch
        {
            // do nothing, fileExists will remain false
        }
        if (!fileExists)
        {
            break;
        }
        else
        {
            NumberOfLevels++;
        }
    }
}
```

CocosSharp não oferece uma abordagem de plataforma cruzada para detectar se os arquivos estiverem presentes, para que tenhamos depender de `TitleContainer` classe para tentar abrir um fluxo. Se o código para abrir um fluxo lança uma exceção e, em seguida, o arquivo não existir e o loop while quebras. Depois que o loop for concluído, o `NumberOfLevels` propriedade informa quantos níveis válidos é parte do projeto.

O `LevelSelectScene` classe usa a `LevelManager.NumberOfLevels` para determinar quantos botões para criar o `CreateLevelButtons` método:


```csharp
private void CreateLevelButtons()
{
    const int buttonsPerPage = 6;
    int levelIndex0Based = buttonsPerPage * pageNumber;
    int maxLevelExclusive = System.Math.Min (levelIndex0Based + 6, LevelManager.Self.NumberOfLevels);
    int buttonIndex = 0;
    float centerX = this.ContentSize.Center.X;
    const float topRowOffsetFromCenter = 16;
    float topRowY = this.ContentSize.Center.Y + topRowOffsetFromCenter;
    for (int i = levelIndex0Based; i < maxLevelExclusive; i++)
    {
        ...
    }
}
```

O `NumberOflevels` propriedade é usada para determinar quais botões devem ser criadas. Esse código considera a página que o usuário está exibindo no momento e só cria um máximo de seis botões por página. Quando clicado, o botão instâncias chamada a `HandleButtonClicked` método:


```csharp
private void HandleButtonClicked(object sender, EventArgs args)
{
    // levelNumber is 1-based, so subtract 1:
    var levelIndex = (sender as Button).LevelNumber - 1;
    LevelManager.Self.CurrentLevel = levelIndex;
    CoinTime.GameAppDelegate.GoToGameScene ();
}
```

Esse método atribui o `CurrentLevel` propriedade que é usada pelo `GameScene` quando o carregamento de um nível. Depois de definir o `CurrentLevel`, o `GoToGameScene` é gerado pelo método alternando a cena de `LevelSelectScene` para `GameScene`.

O `GameScene` chamadas de construtor `GoToLevel`, que executa a lógica de nível de carregamento:


```csharp
private void GoToLevel(int levelNumber)
{
    LoadLevel (levelNumber);
    CreateCollision();
    ProcessTileProperties ();
    touchScreen = new TouchScreenInput(gameplayLayer);
    secondsLeft = secondsPerLevel;
}
```

Em seguida, vamos dar uma olhada em métodos chamados `GoToLevel`.


## <a name="loadlevel"></a>LoadLevel

O `LoadLevel` método é responsável por carregar o arquivo .tmx e adicioná-lo para o `GameScene`. Este método não cria todos os objetos interativos como colisão ou entidades – ela simplesmente cria os visuais para o nível, também conhecido como o *ambiente*.


```csharp
private void LoadLevel(int levelNumber)
{
    currentLevel = new CCTileMap ("level" + levelNumber + ".tmx");
    currentLevel.Antialiased = false;
    backgroundLayer = currentLevel.LayerNamed ("Background");
    // CCTileMap is a CCLayer, so we'll just add it under all entities
    this.AddChild (currentLevel);
    // put the game layer after
    this.RemoveChild(gameplayLayer);
    this.AddChild(gameplayLayer);
    this.RemoveChild (hudLayer);
    this.AddChild (hudLayer);
}
```

O `CCTileMap` construtor aceita um nome de arquivo, que é criado usando o número do nível passado para `LoadLevel`. Para obter mais informações sobre como criar e trabalhar com `CCTileMap` instâncias, consulte o [usando lado a lado com guia CocosSharp](~/graphics-games/cocossharp/tiled.md).

Atualmente, CocosSharp não permite a reorganização de camadas sem remover e adicioná-los novamente para seu pai `CCScene` (que é o `GameScene` nesse caso), de modo que as últimas linhas do método são necessárias para reordenar as camadas.


## <a name="createcollision"></a>CreateCollision

O `CreateCollision` método construções um `LevelCollision` instância que é usada para executar *sólido colisão* entre o player e o ambiente.


```csharp
private void CreateCollision()
{
    levelCollision = new LevelCollision();
    levelCollision.PopulateFrom(currentLevel);
}
```

Sem essa colisão, o player deve passar o nível e o jogo deve ser reproduzido. Colisão sólida permite que o player orientá-lo no chão e impede que o player acompanhará paredes ou saltar backup por meio de tetos.

Colisão em vez de moeda pode ser adicionado sem nenhum código adicional – apenas as modificações arquivos lado a lado. 


## <a name="processtileproperties"></a>ProcessTileProperties

Depois que um nível é carregado e a colisão é criada, `ProcessTileProperties` é chamado para executar lógica com base nas propriedades de bloco. Tempo de moeda inclui um `PropertyLocation` estrutura para definir as propriedades e as coordenadas do bloco com essas propriedades:


```csharp
public struct PropertyLocation
{
    public CCTileMapLayer Layer;
    public CCTileMapCoordinates TileCoordinates;
    public float WorldX;
    public float WorldY;
    public Dictionary<string, string> Properties;
}
```

Esse struct é usado para construir criar instâncias de entidade e remova blocos desnecessários no `ProcessTileProperties` método:


```csharp
private void ProcessTileProperties()
{
    TileMapPropertyFinder finder = new TileMapPropertyFinder (currentLevel);
    foreach (var propertyLocation in finder.GetPropertyLocations())
    {
        var properties = propertyLocation.Properties;
        if (properties.ContainsKey ("EntityType"))
        {
            float worldX = propertyLocation.WorldX;
            float worldY = propertyLocation.WorldY;
            if (properties.ContainsKey ("YOffset"))
            {
                string yOffsetAsString = properties ["YOffset"];
                float yOffset = 0;
                float.TryParse (yOffsetAsString, out yOffset);
                worldY += yOffset;
            }
            bool created = TryCreateEntity (properties ["EntityType"], worldX, worldY);
            if (created)
            {
                propertyLocation.Layer.RemoveTile (propertyLocation.TileCoordinates);
            }
        }
        else if (properties.ContainsKey ("RemoveMe"))
        {
            propertyLocation.Layer.RemoveTile (propertyLocation.TileCoordinates);
        }
    }
}
```

O loop foreach avalia cada propriedade de bloco, verificando se a chave for `EntityType` ou `RemoveMe`. `EntityType` indica que uma instância de entidade deve ser criada. `RemoveMe` indica que o bloco deve ser removido completamente em tempo de execução.

Se uma propriedade com o `EntityType` chave for encontrada, em seguida, `TryCreateEntity` é chamado, que tenta criar uma entidade usando a correspondência de propriedade de `EntityType` chave:


```csharp
private bool TryCreateEntity(string entityType, float worldX, float worldY)
{
    CCNode entityAsNode = null;
    switch (entityType)
    {
    case "Player":
        player = new Player ();
        entityAsNode = player;
        break;
    case "Coin":
        Coin coin = new Coin ();
        entityAsNode = coin;
        coins.Add (coin);
        break;
    case "Door":
        door = new Door ();
        entityAsNode = door;
        break;
    case "Spikes":
        var spikes = new Spikes ();
        this.damageDealers.Add (spikes);
        entityAsNode = spikes;
        break;
    case "Enemy":
        var enemy = new Enemy ();
        this.damageDealers.Add (enemy);
        this.enemies.Add (enemy);
        entityAsNode = enemy;
        break;
    }
    if(entityAsNode != null)
    {
        entityAsNode.PositionX = worldX;
        entityAsNode.PositionY = worldY;
        gameplayLayer.AddChild (entityAsNode);
    }
    return entityAsNode != null;
}
```


# <a name="adding-new-entities"></a>Adicionar novas entidades

Tempo de moeda usa o padrão de entidade para seus objetos de jogos (que é abordado no [entidades CocosSharp guia](~/graphics-games/cocossharp/entities.md)). Todas as entidades herdam `CCNode`, que significa que eles podem ser adicionados como filhos do `gameplayLayer`.

Cada tipo de entidade também é referenciado diretamente por meio de uma lista ou uma única instância. Por exemplo, o `Player` é referenciado pelo `player` campo e todos os `Coin` instâncias são referenciadas em um `coins` lista. Manter referências diretas a entidades (em vez de referência-los por meio de `gameLayer.Children` lista) torna o código que acessa a essas entidades mais fáceis de ler e elimina a conversão de tipo potencialmente cara.

O código existente fornece uma variedade de tipos de entidade como exemplos de como criar novas entidades. As etapas a seguir podem ser usadas para criar uma nova entidade:


## <a name="1---define-a-new-class-using-the-entity-pattern"></a>1 - definir uma nova classe usando o padrão de entidade

O único requisito para a criação de uma entidade é criar uma classe que herda de `CCNode`. A maioria das entidades têm alguns visual, como um `CCSprite`, que deve ser adicionado como um filho da entidade em seu construtor.

CoinTime fornece o `AnimatedSpriteEntity` classe que simplifica a criação de entidades animadas. Animações serão abordadas mais detalhadamente o [seção animado entidades](#Animated_Entities).


## <a name="2--add-a-new-entry-to-the-trycreateentity-switch-statement"></a>2 – adicionar uma nova entrada à instrução switch TryCreateEntity

Instâncias da entidade nova devem ser instanciadas no `TryCreateEntity`. Se a entidade requer lógica de cada quadro como colisão, AI ou entrada de leitura, em seguida, o `GameScene` precisa manter uma referência ao objeto. Se várias instâncias forem necessários (como `Coin` ou `Enemy` instâncias), em seguida, um novo `List` devem ser adicionados ao `GameScene` classe.


## <a name="3--modify-tile-properties-for-the-new-entity"></a>3 – modificar as propriedades de bloco para a nova entidade

Depois que o código de suporte para a criação da nova entidade, a nova entidade precisa ser adicionada para o tileset. O tileset pode ser editado, abrindo qualquer nível `.tmx` arquivo. 

O tileset é armazenado separado do .tmx no **mastersheet.tsx** de arquivo, para que ele deve ser importado antes que possa ser editada:

![](cointime-images/image11.png "O tileset é armazenado separado do arquivo. TSX, ele deve ser importado antes que ele possa ser editado")

Quando importado, propriedades em blocos selecionados são editáveis, e o EntityType pode ser adicionado:

![](cointime-images/image12.png "Quando importado, propriedades em blocos selecionados são editáveis, e o EntityType pode ser adicionado")

Depois que a propriedade é criada, seu valor pode ser definido para corresponder à nova `case` em `TryCreateEntity`:

![](cointime-images/image13.png "Depois que a propriedade é criada, seu valor pode ser definido para corresponder ao novo caso no TryCreateEntity")

Depois que o tileset tiver sido alterado, ele deve ser exportado – Isso disponibiliza as alterações para todos os outros níveis:

![](cointime-images/image14.png "Depois que o tileset tiver sido alterado, ele deve ser exportado")

O tileset deve substituir a existente **mastersheet.tsx** tileset:

![](cointime-images/image15.png "he tileset deve substituir o tileset mastersheet.tsx existente")


# <a name="entity-tile-removal"></a>Remoção do bloco de entidade

Quando um mapa de bloco é carregado em um jogo, os blocos individuais são objetos estáticos. Como entidades exigem o comportamento personalizado, como a movimentação, o código de moeda tempo remove blocos quando as entidades são criadas.

`ProcessTileProperties` inclui a lógica para remover os blocos que criam entidades usando o `RemoveTile` método:


```csharp
private void ProcessTileProperties()
{
    TileMapPropertyFinder finder = new TileMapPropertyFinder (currentLevel);
    foreach (var propertyLocation in finder.GetPropertyLocations())
    {
        var properties = propertyLocation.Properties;
        if (properties.ContainsKey ("EntityType"))
        {
            ...
            bool created = TryCreateEntity (properties ["EntityType"], worldX, worldY);
            if (created)
            {
                propertyLocation.Layer.RemoveTile (propertyLocation.TileCoordinates);
            }
        }
        ...
    }
}
```

Essa remoção automática de blocos é suficiente para entidades que ocupam apenas um bloco em tileset, como moedas e inimigos. Entidades maiores exigem propriedades e lógica adicional.

A porta requer dois blocos a ser desenhado completamente:

![](cointime-images/image16.png "A porta requer dois blocos a ser desenhado completamente")

O bloco inferior na porta contém as propriedades para a criação de uma entidade (**EntityType** definida como **porta**):

![](cointime-images/image17.png "O bloco inferior na porta contém as propriedades para a criação de uma conjunto de entidades EntityType a porta")

Como apenas a lado a lado inferior na porta é removida quando a instância de porta é criada, lógica adicional é necessária para remover o bloco superior no tempo de execução. O bloco superior tem um **RemoveMe** propriedade definida como **true**:

![](cointime-images/image18.png "O bloco superior tem uma propriedade RemoveMe definida como true")



Esta propriedade é usada para remover os blocos no `ProcessTileProperties`:


```csharp
private void ProcessTileProperties()
{
    TileMapPropertyFinder finder = new TileMapPropertyFinder (currentLevel);
    foreach (var propertyLocation in finder.GetPropertyLocations())
    {
        var properties = propertyLocation.Properties;
        ...
        else if (properties.ContainsKey ("RemoveMe"))
        {
            propertyLocation.Layer.RemoveTile (propertyLocation.TileCoordinates);
        }
    }
}
```


# <a name="entity-offsets"></a>Deslocamentos de entidade

Entidades criadas a partir de blocos são posicionadas ao alinhar o centro da entidade com o centro do bloco. Entidades maiores, como `Door`, use as propriedades adicionais e lógica para ser posicionado corretamente. 

O bloco de acesso à parte inferior, que define o `Door` Especifica o posicionamento de entidade, uma **deslocamento y** valor de 4. Sem essa propriedade, o `Door` instância é colocada no centro do bloco:

![](cointime-images/image19.png "Sem essa propriedade, a instância de porta é colocada no centro do bloco")

 

Isso seja corrigido, aplicando o **deslocamento y** valor em `ProcessTileProperties`:


```csharp
private void ProcessTileProperties()
{
    TileMapPropertyFinder finder = new TileMapPropertyFinder (currentLevel);
    foreach (var propertyLocation in finder.GetPropertyLocations())
    {
        var properties = propertyLocation.Properties;
        if (properties.ContainsKey ("EntityType"))
        {
            float worldX = propertyLocation.WorldX;
            float worldY = propertyLocation.WorldY;
            if (properties.ContainsKey ("YOffset"))
            {
                string yOffsetAsString = properties ["YOffset"];
                float yOffset = 0;
                float.TryParse (yOffsetAsString, out yOffset);
                worldY += yOffset;
            }
            bool created = TryCreateEntity (properties ["EntityType"], worldX, worldY);
            ...
        }
...
    }
}
```


# <a name="animated-entities"></a>Entidades animadas

Tempo de moeda inclui várias entidades animadas. O `Player` e `Enemy` entidades reproduzir animações de exame e `Door` entidade desempenha uma animação de abertura depois que todas as moedas foram coletadas.


## <a name="achx-files"></a>arquivos de .achx

Moeda tempo animações são definidas em arquivos de .achx. Cada animação estiver definida entre `AnimationChain` marcas, conforme mostrado na seguinte animação definida no **propanimations.achx**:


```xml
<AnimationChain>
  <Name>Spikes</Name>
  <ColorKey>0</ColorKey>
  <Frame>
    <FlipHorizontal>false</FlipHorizontal>
    <FlipVertical>false</FlipVertical>
    <TextureName>..\images\mastersheet.png</TextureName>
    <FrameLength>0.1</FrameLength>
    <LeftCoordinate>1152</LeftCoordinate>
    <RightCoordinate>1168</RightCoordinate>
    <TopCoordinate>128</TopCoordinate>
    <BottomCoordinate>144</BottomCoordinate>
    <RelativeX>0</RelativeX>
    <RelativeY>0</RelativeY>
  </Frame>
</AnimationChain> 
```

Esta animação contém apenas uma única estrutura, resultando na entidade de pico, exibindo uma imagem estática. Entidades podem usar arquivos .achx se eles exibirem animações único ou vários quadros. Quadros adicionais podem ser adicionados aos arquivos .achx sem exigir alterações no código. 

Quadros definem qual imagem para exibir o `TextureName` parâmetro e as coordenadas da tela no `LeftCoordinate`, `RightCoordinate`, `TopCoordinate`, e `BottomCoordinate` marcas. Eles representam as coordenadas de pixel do quadro de animação da imagem que está sendo usado – **mastersheet.png** nesse caso.

![](cointime-images/image20.png "Eles representam as coordenadas de pixel do quadro de animação na imagem")

O `FrameLength` propriedade define o número de segundos que um quadro em uma animação deve ser exibido. Apenas um quadro animações ignoram esse valor.

Todas as outras propriedades de AnimationChain no arquivo .achx serão ignoradas pelo tempo de moeda.


## <a name="animatedspriteentity"></a>AnimatedSpriteEntity

Lógica de animação está contida no `AnimatedSpriteEntity` classe, que serve como a classe base para a maioria das entidades usadas no `GameScene`. Ele fornece a seguinte funcionalidade:

 - Carregamento de `.achx` arquivos
 - Cache de animação de animações carregadas
 - Instância de CCSprite para exibir a animação
 - Lógica para alterar o quadro atual

O construtor de picos fornece um exemplo simples de como carregar e usar animações:


```csharp
public Spikes ()
{
    LoadAnimations ("Content/animations/propanimations.achx");
    CurrentAnimation = animations [0];
}
```

O **propAnimations.achx** contém apenas uma animação, para o construtor acessa esse animação por índice. Se um arquivo .achx contém várias animações, animações podem ser referenciadas por nome, como mostra o `Enemy` construtor:


```csharp
walkLeftAnimation = animations.Find (item => item.Name == "WalkLeft");
walkRightAnimation = animations.Find (item => item.Name == "WalkRight");
```


# <a name="summary"></a>Resumo

Este guia aborda os detalhes de implementação de tempo de moeda. Tempo de moeda é criado para ser um jogo completo, mas também é um projeto que pode ser facilmente modificado e expandido. Os leitores são incentivados a gastar fazer modificações tempo níveis, adicionar novos níveis e criar novas entidades para compreender melhor como o tempo de moeda é implementado.

## <a name="related-links"></a>Links relacionados

- [Projeto de jogo (exemplo)](https://developer.xamarin.com/samples/mobile/CoinTime/)
