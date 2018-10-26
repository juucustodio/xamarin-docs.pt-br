---
title: Detalhes de tempo de jogo de moeda
description: Este guia aborda detalhes de implementação no jogo de tempo de moeda, incluindo trabalhar com mapas de bloco, criação de entidades, animando sprites e implementando colisão eficiente.
ms.prod: xamarin
ms.assetid: 5D285684-0417-4E16-BD14-2D1F6DEFBB8B
author: conceptdev
ms.author: crdun
ms.date: 03/24/2017
ms.openlocfilehash: af914e10eb93aa0668743a113ffe647a939fea75
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50112618"
---
# <a name="coin-time-game-details"></a>Detalhes de tempo de jogo de moeda

_Este guia aborda detalhes de implementação no jogo de tempo de moeda, incluindo trabalhar com mapas de bloco, criação de entidades, animando sprites e implementando colisão eficiente._

Tempo de moeda é uma plataforma completa para jogo para iOS e Android. O objetivo do jogo é coletar todas as moedas em um nível e, em seguida, acessar a porta de saída, evitando inimigos e obstáculos.

![](cointime-images/image1.png "O objetivo do jogo é coletar todas as moedas em um nível e, em seguida, acessar a porta de saída, evitando inimigos e obstáculos")

Este guia aborda detalhes de implementação em tempo de moeda, que abrangem os tópicos a seguir:

- [Trabalhando com arquivos tmx](#working-with-tmx-files)
- [Carregamento de nível](#level-loading)
- [Adicionar novas entidades](#adding-new-entities)
- [Entidades animadas](#animated-entities)


## <a name="content-in-coin-time"></a>Conteúdo em tempo de moeda

Tempo de moeda é um projeto de exemplo que representa como um projeto de CocosSharp completo pode ser organizado. Coin Time estrutura tem como objetivo simplificar a adição e a manutenção de conteúdo. Ele usa **.tmx** arquivos criados pelo [lado a lado](http://www.mapeditor.org) para níveis e arquivos XML para definir animações. Modificando ou adicionando novos conteúdos pode ser obtido com um mínimo de esforço. 

Embora essa abordagem torna a um projeto em vigor para o aprendizado e experimentação de tempo de moeda, ele também reflete a jogos profissionais como são feitas. Este guia explica algumas das abordagens usadas para simplificar a adição e modificação de conteúdo.


## <a name="working-with-tmx-files"></a>Trabalhando com arquivos tmx

Níveis de tempo de moeda são definidos usando o formato de arquivo .tmx, que é a saída pela [lado a lado](http://www.mapeditor.org) editor de mapa de bloco. Para obter uma discussão detalhada de como trabalhar com lado a lado, consulte o [usando o lado a lado com o Cocos Sharp guia](~/graphics-games/cocossharp/tiled.md). 

Cada nível é definido em seu próprio arquivo .tmx contido na **CoinTime/ativos/conteúdo/níveis** pasta. Todos os níveis de tempo de moeda compartilham um arquivo de tileset, que é definido na **mastersheet.tsx** arquivo. Esse arquivo define as propriedades personalizadas para cada bloco, como se o bloco tem sólida colisão ou se o bloco deve ser substituído por uma instância de entidade. O arquivo mastersheet.tsx permite que as propriedades ser definido apenas uma vez e usado em todos os níveis. 


### <a name="editing-a-tile-map"></a>Editar um mapa de bloco

Para editar um mapa de bloco, abra o arquivo de .tmx no lado a lado por duas vezes no arquivo .tmx ou abri-lo por meio do menu arquivo no lado a lado. Coin Time mapas de nível de bloco contêm três camadas: 

- **Entidades** – essa camada contém blocos que serão substituídos com instâncias de entidades em tempo de execução. Exemplos incluem moedas, o jogador, inimigos e a porta final de nível.
- **Terreno** – essa camada contém blocos que geralmente têm colisão sólida. Colisão sólida permite que o jogador para guiá-lo desses blocos sem queda. Blocos com sólida colisão também podem atuar como paredes e tetos.
- **Plano de fundo** – a camada de plano de fundo contém blocos que são usados como plano de fundo estático. Essa camada não rola quando a câmera se move em todo o nível, criando a aparência de profundidade por meio da parallax.

Como vamos explorar mais tarde, o código de carregamento de nível de espera que essas três camadas em todos os níveis de tempo de moeda.

#### <a name="editing-terrain"></a>Edição de terreno

Blocos podem ser colocados clicando na **mastersheet** tileset e, em seguida, clicar no bloco de mapa. Por exemplo, para pintar o terreno novo em um nível:

1. Selecione a camada de terreno
1. Clique no bloco para desenhar
1. Clique ou enviar por push e arraste-a sobre o mapa para pintar o bloco

    ![](cointime-images/image2.png "Clique no bloco para desenhar 1")

O canto superior esquerdo do tileset contém todos os do terreno em vez de moeda. Inclui o terreno, que é sólido, o **SolidCollision** propriedade, conforme mostrado nas propriedades de bloco à esquerda da tela:

![](cointime-images/image3.png "Terreno, que é sólido, inclui a propriedade SolidCollision, conforme mostrado nas propriedades de bloco à esquerda da tela")

#### <a name="editing-entities"></a>Editar entidades

Entidades podem ser adicionadas ou removidas de um nível – assim como o terreno. O **mastersheet** tileset tem todas as entidades colocadas sobre na metade horizontalmente, portanto, eles podem não estar visíveis sem necessidade de rolagem à direita:

![](cointime-images/image4.png "O tileset mastersheet tem todas as entidades colocadas sobre na metade horizontalmente, para que eles podem não estar visíveis sem necessidade de rolagem à direita")

Novas entidades devem ser colocadas na **entidades** camada.

![](cointime-images/image5.png "Novas entidades devem ser colocadas na camada de entidades")

CoinTime código procura as **EntityType** quando um nível é carregado para identificar os blocos que devem ser substituídos por entidades: 

![](cointime-images/image6.png "Código de CoinTime procura o EntityType quando um nível é carregado para identificar os blocos que devem ser substituídos por entidades")

Depois que o arquivo foi modificado e salvo, as alterações serão exibidos automaticamente se o projeto é compilado e executado:

![](cointime-images/image7.png "Depois que o arquivo foi modificado e salvo, as alterações serão exibidos automaticamente se o projeto é compilado e executado")


### <a name="adding-new-levels"></a>Adicionar novos níveis

O processo de adicionar níveis à hora de moeda requer nenhuma alteração de código e apenas, algumas pequenas alterações ao projeto. Para adicionar um novo nível:

1. Abra a pasta de nível localizado em <**CoinTime Root > \CoinTime\Assets\Content\levels**
1. Copie e cole um dos níveis, como **level0.tmx**
1. Renomeie o novo arquivo de .tmx, portanto, ele continua a sequência numérica nível com os níveis existentes, tais como **level8.tmx**
1. No Visual Studio ou Visual Studio para Mac, adicione o novo arquivo .tmx para a pasta de níveis de Android. Verifique se o arquivo usa o **AndroidAsset** ação de build.

    ![](cointime-images/image8.png "Verifique se o arquivo usa a ação de compilação AndroidAsset")

1. Adicione o novo arquivo .tmx até a pasta de níveis de iOS. Certifique-se de vincular o arquivo de seu local original e verifique se ele usa o **BundleResource** ação de build.

    ![](cointime-images/image9.png "Certifique-se de vincular o arquivo de seu local original e verifique se ele usa a ação de compilação BundleResource")

O novo nível deve aparecer na tela Selecionar nível como nível 9 (nomes de arquivo de nível começam em 0, mas os botões de nível começam com o número 1):

![](cointime-images/image10.png "O novo nível deve aparecer na tela Selecionar nível como início de nomes de arquivo de nível de nível 9 em 0, mas os botões de nível começam com o número 1")


## <a name="level-loading"></a>Carregamento de nível

Como mostrado anteriormente, os novos níveis de exigem nenhuma alteração no código – o jogo detecta automaticamente os níveis se eles são nomeados corretamente e adicionados à **níveis** pasta com a ação de build correto (**BundleResource**ou **AndroidAsset**).

A lógica para determinar o número de níveis está contida no `LevelManager` classe. Quando uma instância das `LevelManager` é construído (usando o padrão singleton), o `DetermineAvailbleLevels` método é chamado:


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

CocosSharp não fornece uma abordagem de plataforma cruzada para detectar se os arquivos estão presentes, portanto, temos que dependem de `TitleContainer` classe para tentar abrir um fluxo. Se o código para abrir um fluxo lança uma exceção e, em seguida, o arquivo existe e o loop while quebras. Quando o loop for concluído, o `NumberOfLevels` propriedade informa quantos níveis válidos é parte do projeto.

O `LevelSelectScene` classe usa o `LevelManager.NumberOfLevels` para determinar quantos botões para criar o `CreateLevelButtons` método:


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

O `NumberOflevels` propriedade é usada para determinar quais botões devem ser criados. Esse código considera qual página que o usuário está exibindo no momento e cria apenas um máximo de seis botões por página. Quando clicado, o botão de instâncias de chamada a `HandleButtonClicked` método:


```csharp
private void HandleButtonClicked(object sender, EventArgs args)
{
    // levelNumber is 1-based, so subtract 1:
    var levelIndex = (sender as Button).LevelNumber - 1;
    LevelManager.Self.CurrentLevel = levelIndex;
    CoinTime.GameAppDelegate.GoToGameScene ();
}
```

Esse método atribui a `CurrentLevel` propriedade que é usada pelo `GameScene` durante o carregamento de um nível. Depois de definir a `CurrentLevel`, o `GoToGameScene` método é gerado, alternância de cena `LevelSelectScene` para `GameScene`.

O `GameScene` chamadas de construtor `GoToLevel`, que executa a lógica de carregamento de nível:


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


### <a name="loadlevel"></a>LoadLevel

O `LoadLevel` método é responsável por carregar o arquivo .tmx e adicioná-lo para o `GameScene`. Esse método não cria todos os objetos interativos como colisão ou entidades – ele apenas cria os elementos visuais para o nível, também conhecido como o *ambiente*.


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

O `CCTileMap` construtor aceita um nome de arquivo, que é criado usando o número do nível passado para `LoadLevel`. Para obter mais informações sobre como criar e trabalhar com `CCTileMap` instâncias, consulte a [usando o lado a lado com CocosSharp guia](~/graphics-games/cocossharp/tiled.md).

Atualmente, CocosSharp não permitir a reordenação das camadas sem remover e adicionando-os novamente para seus pais `CCScene` (que é o `GameScene` nesse caso), de modo que as últimas linhas do método são necessárias para reordenar as camadas.


### <a name="createcollision"></a>CreateCollision

O `CreateCollision` construções de método uma `LevelCollision` instância que é usada para realizar *colisão sólida* entre o ambiente e o player.


```csharp
private void CreateCollision()
{
    levelCollision = new LevelCollision();
    levelCollision.PopulateFrom(currentLevel);
}
```

Sem essa colisão, o player seria passar o nível e o jogo seria podem fazê-lo. Colisão sólida permite que o jogador percorrer no chão e impede que o player percorrendo paredes ou saltar backup por meio de tetos.

Colisão em vez de moeda pode ser adicionado sem nenhum código adicional – apenas as modificações em arquivos lado a lado. 


### <a name="processtileproperties"></a>ProcessTileProperties

Depois que um nível é carregado e a colisão é criada, `ProcessTileProperties` é chamado para executar a lógica com base nas propriedades do bloco. Tempo de moeda inclui um `PropertyLocation` struct para definir as propriedades e as coordenadas do bloco com estas propriedades:


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

Esse struct é usado para construir criar instâncias de entidade e remover blocos desnecessários no `ProcessTileProperties` método:


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

O loop foreach avalia cada propriedade de lado a lado, verificando se a chave seja `EntityType` ou `RemoveMe`. `EntityType` indica que uma instância de entidade deve ser criada. `RemoveMe` indica que o bloco deve ser completamente removido em tempo de execução.

Se uma propriedade com o `EntityType` chave for encontrada, então `TryCreateEntity` é chamado, quais tentativas de criar uma entidade usando a correspondência de propriedade a `EntityType` chave:


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


## <a name="adding-new-entities"></a>Adicionar novas entidades

Tempo de moeda usa o padrão de entidade para seus objetos do jogo (que é abordado na [guia de entidades em CocosSharp](~/graphics-games/cocossharp/entities.md)). Todas as entidades herdam `CCNode`, que significa que eles podem ser adicionados como filhos a `gameplayLayer`.

Cada tipo de entidade também é referenciado diretamente por meio de uma lista ou uma única instância. Por exemplo, o `Player` é referenciada pela `player` campo e todos os `Coin` instâncias são referenciadas em um `coins` lista. Manter referências diretas para entidades (em vez de fazer referência a eles por meio de `gameLayer.Children` lista) torna o código que acessa essas entidades mais fáceis de ler e elimina a conversão de tipo potencialmente cara.

O código existente fornece um número de tipos de entidade como exemplos de como criar novas entidades. As etapas a seguir podem ser usadas para criar uma nova entidade:


### <a name="1---define-a-new-class-using-the-entity-pattern"></a>1 - definir uma nova classe usando o padrão de entidade

O único requisito para a criação de uma entidade é criar uma classe que herda de `CCNode`. A maioria das entidades têm algumas visual, como um `CCSprite`, que deve ser adicionado como um filho da entidade em seu construtor.

CoinTime fornece o `AnimatedSpriteEntity` classe que simplifica a criação de entidades animadas. As animações serão abordadas mais detalhadamente a [seção animada entidades](#animated-entities).


### <a name="2--add-a-new-entry-to-the-trycreateentity-switch-statement"></a>2 – adicionar uma nova entrada para a instrução switch TryCreateEntity

Instâncias da nova entidade devem ser instanciadas no `TryCreateEntity`. Se a entidade exigir lógica de cada quadro, como colisão, inteligência Artificial ou ler a entrada, então o `GameScene` precisa manter uma referência ao objeto. Se várias instâncias forem necessários (como `Coin` ou `Enemy` instâncias), em seguida, um novo `List` devem ser adicionados ao `GameScene` classe.


### <a name="3--modify-tile-properties-for-the-new-entity"></a>3 – modificar as propriedades de bloco para a nova entidade

Depois que o código suporta a criação da nova entidade, a nova entidade precisa ser adicionada para o tileset. O tileset pode ser editado, abrindo a qualquer nível `.tmx` arquivo. 

O tileset é armazenado separadamente o .tmx na **mastersheet.tsx** arquivo, para que ele deve ser importado antes que ele possa ser editado:

![](cointime-images/image11.png "O tileset é armazenada separado do arquivo de. TSX, para que ele deve ser importado antes que ele possa ser editado")

Depois de importados, as propriedades no blocos selecionados são editáveis e EntityType pode ser adicionado:

![](cointime-images/image12.png "Depois de importados, as propriedades no blocos selecionados são editáveis e EntityType pode ser adicionado")

Depois que a propriedade é criada, seu valor pode ser definido para corresponder à nova `case` em `TryCreateEntity`:

![](cointime-images/image13.png "Depois que a propriedade é criada, seu valor pode ser definido para corresponder ao novo caso no TryCreateEntity")

Depois que o tileset tiver sido alterada, ele deve ser exportado – isso faz as alterações disponíveis para todos os outros níveis:

![](cointime-images/image14.png "Depois que o tileset tiver sido alterada, ele deve ser exportado")

O tileset deve substituir o existente **mastersheet.tsx** tileset:

![](cointime-images/image15.png "he tileset deve substituir o existente tileset mastersheet.tsx")


## <a name="entity-tile-removal"></a>Remoção do bloco de entidade

Quando um mapa de bloco é carregado em um jogo, os blocos individuais são objetos estáticos. Como entidades exigem um comportamento personalizado, como a movimentação, o código de moeda tempo remove blocos quando as entidades são criadas.

`ProcessTileProperties` inclui lógica para remover blocos que criam entidades usando o `RemoveTile` método:


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

Essa remoção automática de blocos é suficiente para entidades que ocupam apenas um bloco no tileset, como moedas e inimigos. Entidades maiores exigem as propriedades e lógica adicional.

A porta requer dois blocos a ser desenhado completamente:

![](cointime-images/image16.png "A porta requer dois blocos a ser desenhado completamente")

O bloco inferior na porta contém as propriedades para a criação de uma entidade (**EntityType** definido como **porta**):

![](cointime-images/image17.png "O bloco inferior na porta contém as propriedades para a criação de um conjunto de EntityType à porta de entidades")

Como apenas o bloco inferior na porta é removido quando a instância de porta é criada, uma lógica adicional é necessária para remover o bloco superior no tempo de execução. O lado superior tem uma **RemoveMe** propriedade definida como **verdadeiro**:

![](cointime-images/image18.png "O lado superior tem uma propriedade RemoveMe definida como true")



Essa propriedade é usada para remover blocos no `ProcessTileProperties`:


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


## <a name="entity-offsets"></a>Deslocamentos de entidade

Entidades criadas a partir de blocos são posicionadas ao alinhar o centro da entidade com o centro do bloco. Entidades maiores, como `Door`, use as propriedades adicionais e lógica sejam colocados corretamente. 

O bloco de acesso à parte inferior, que define o `Door` Especifica o posicionamento de entidade, uma **deslocamento y** valor 4. Sem essa propriedade, o `Door` instância é colocada no centro do bloco:

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


## <a name="animated-entities"></a>Entidades animadas

Tempo de moeda inclui várias entidades animadas. O `Player` e `Enemy` entidades executar animações de movimentação e a `Door` entidade desempenha uma animação de abertura depois que todas as moedas foram coletadas.


### <a name="achx-files"></a>arquivos .achx

Animações de tempo de moeda são definidas em arquivos de .achx. Cada animação é definida entre `AnimationChain` marcas, conforme mostrado na seguinte animação definida no **propanimations.achx**:


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

Essa animação contém apenas um único quadro, resultando na entidade de pico, exibindo uma imagem estática. Entidades podem usar arquivos .achx se eles exibem animações únicas ou vários quadros. Quadros adicionais podem ser adicionados aos arquivos .achx sem exigir nenhuma alteração no código. 

Quadros definem qual imagem a ser exibida na `TextureName` parâmetro e as coordenadas da tela na `LeftCoordinate`, `RightCoordinate`, `TopCoordinate`, e `BottomCoordinate` marcas. Eles representam as coordenadas de pixel do quadro de animação na imagem que está sendo usada – **mastersheet.png** nesse caso.

![](cointime-images/image20.png "Eles representam as coordenadas de pixel do quadro de animação na imagem")

O `FrameLength` propriedade define o número de segundos que um quadro em uma animação deve ser exibido. Animações de quadro único ignoram esse valor.

Todas as outras propriedades no arquivo .achx AnimationChain são ignoradas pelo tempo de moeda.


### <a name="animatedspriteentity"></a>AnimatedSpriteEntity

Animação lógica está contida na `AnimatedSpriteEntity` classe, que serve como classe base para a maioria das entidades usadas no `GameScene`. Ele fornece a seguinte funcionalidade:

 - Carregamento de `.achx` arquivos
 - Cache de animação das animações carregadas
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

O **propAnimations.achx** contém apenas uma animação, portanto, o construtor acessa essa animação por índice. Se um arquivo .achx contém várias animações, animações podem ser referenciadas por nome, conforme mostrado no `Enemy` construtor:


```csharp
walkLeftAnimation = animations.Find (item => item.Name == "WalkLeft");
walkRightAnimation = animations.Find (item => item.Name == "WalkRight");
```


## <a name="summary"></a>Resumo

Este guia aborda os detalhes da implementação de tempo de moeda. Tempo de moeda é criado para ser um jogo completo, mas também é um projeto que pode ser facilmente modificado e expandido. Os leitores são incentivados a gastar realizar modificações de tempo aos níveis, adicionar novos níveis e criar novas entidades para entender melhor como o tempo de moeda é implementado.

## <a name="related-links"></a>Links relacionados

- [Projeto de jogo (amostra)](https://developer.xamarin.com/samples/mobile/CoinTime/)
