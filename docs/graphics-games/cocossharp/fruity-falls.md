---
title: Detalhes de jogo vão Fruity
description: Este guia examina o jogo vão Fruity, que abordam conceitos de desenvolvimento de jogos como física, gerenciamento de conteúdo, estado do jogo e projeto de jogo e CocosSharp comuns.
ms.prod: xamarin
ms.assetid: A5664930-F9F0-4A08-965D-26EF266FED24
ms.technology: xamarin-cross-platform
author: charlespetzold
ms.author: chape
ms.date: 03/27/2017
ms.openlocfilehash: 2138e27c1097e014f302cc0b9de0fa411bed89fc
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="fruity-falls-game-details"></a>Detalhes de jogo vão Fruity

_Este guia examina o jogo vão Fruity, que abordam conceitos de desenvolvimento de jogos como física, gerenciamento de conteúdo, estado do jogo e projeto de jogo e CocosSharp comuns._

Fruity vão é um jogo simple baseado em física, em que o player classifica frutas vermelhas e amarelas em buckets coloridas para ganhar pontos. O objetivo do jogo é ganhar quantos pontos possível sem deixar uma queda de frutas para o agrupamento incorreto, terminando o jogo.

![](fruity-falls-images/image1.png "O objetivo do jogo é ganhar quantos pontos possível sem deixar uma queda de frutas para o agrupamento incorreto, terminando o jogo")

Fruity vão estende os conceitos apresentados a [BouncingGame guia](~/graphics-games/cocossharp/bouncing-game.md) adicionando o seguinte:

 - Conteúdo na forma de PNGs
 - Física avançadas
 - Estado do jogo (transição entre o segundo plano)
 - Capacidade de ajustar os coeficientes de jogos por meio de variáveis contidas em uma única classe
 - Suporte interno de depuração visual
 - Organização de código usando o jogos entidades
 - Projeto de jogo voltada para o valor de repetição e divertido

Enquanto o [BouncingGame guia](~/graphics-games/cocossharp/bouncing-game.md) foco apresentando os principais conceitos de CocosSharp, vão Fruity mostra como colocá-lo juntos em um produto acabado de jogo. Como este guia faz referência a BouncingGame, os leitores devem primeiro se familiarizar com o [BouncingGame guia](~/graphics-games/cocossharp/bouncing-game.md) antes de ler este guia.

Este guia abrange a implementação e design de Fruity vão para fornecer informações para ajudá-lo a fazer seu próprio jogo. Ele abrange os seguintes tópicos:


- [Classe GameController](#gamecontroller-class)
- [Entidades de jogo](#game-entities)
- [Gráficos de frutas](#fruit-graphics)
- [Física](#physics)
- [Conteúdo de jogo](#game-content)
- [GameCoefficients](#gamecoefficients)


## <a name="gamecontroller-class"></a>Classe GameController

O projeto Fruity PCL cai inclui um `GameController` classe que é responsável por criar uma instância do jogo e movendo entre nos bastidores. Essa classe é usada para o iOS e Android projetos para eliminar código duplicado.

O código dentro do `Initialize` método é semelhante ao código a`Initialize` método em um modelo de CocosSharp inalterado, mas ele contém um número de modificações.

Por padrão, o `GameView.ContentManager.SearchPaths` depende da resolução do dispositivo. Conforme explicado a seguir mais detalhadamente, vão Fruity usa o mesmo conteúdo, independentemente da resolução do dispositivo, portanto, o `Initialize` método adiciona o `Images` caminho (com não subpastas) para o `SearchPaths`:


```csharp
contentSearchPaths.Add ("Images");
```

Novos modelos de CocosSharp incluem uma classe herdada de `CCLayer`, indicando que visuais do jogos e lógica devem ser adicionados a essa classe. Em vez disso, Fruity vão usa vários `CCLayer` instâncias ao controle desenhar ordem. Essas `CCLayer` instâncias estão contidas dentro do `GameView` classe que herda de `CCScene`. Fruity vão também inclui várias cenas, sendo que a primeira é a `TitleScene`. Portanto, o `Initialize` método instancia um `TitleScene` instância que é passada para `RunWithScene`:


```csharp
var scene = new TitleScene (GameView);
GameView.Director.RunWithScene (scene);
```

O conteúdo fica Fruity foi criado como arte de pixel de baixa resolução por motivos de estéticos. O `GameView.DesignResolution` é definida para que o jogo só é 384 unidades de largura e altura 512:


```csharp
// We use a lower-resolution display to get a pixellated appearance
int width = 384;
int height = 512;
GameView.DesignResolution = new CCSizeI (width, height); 
```

Por fim, o `GameController` classe fornece um método estático que pode ser chamado por qualquer `CCGameScene` para fazer a transição para outro `CCScene`. Esse método é usado para percorrer o `TitleScene` e `GameScene`.


## <a name="game-entities"></a>Entidades de jogo

Fruity vão faz uso do padrão da entidade para a maioria dos objetos de jogo. Uma explicação detalhada sobre esse padrão pode ser encontrada no [guia de entidades em CocosSharp](~/graphics-games/cocossharp/entities.md).

Os objetos do jogo Implementando a entidade podem ser encontrados na pasta entidades no **FruityFalls.Common** projeto:

![](fruity-falls-images/image2.png "A pasta de entidades no projeto FruityFalls.Common")

Entidades são objetos que herdam de `CCNode`e pode ter elementos visuais, colisão e o comportamento de cada quadro.

O `Fruit` objeto representa uma entidade CocosSharp típico: ele contém um objeto visual, colisão e lógica de cada quadro. O construtor é responsável por inicializar o processo:


```csharp
public Fruit ()
{
    CreateFruitGraphic ();
    if (GameCoefficients.ShowCollisionAreas)
    {
        CreateDebugGraphic ();
    }
    CreateCollision ();
    CreateExtraPointsLabel ();
    Acceleration.Y = GameCoefficients.FruitGravity;
}
```


### <a name="fruit-graphics"></a>Gráficos de frutas

O `CreateFruitGraphic` método cria um `CCSprite` instância e adiciona-o para o `Fruit`. O `IsAntialiased` propriedade é definida como false para dar uma olhada em como pixels de jogo. Esse valor é definido como false em todos os `CCSprite` e `CCLabel` instâncias em todo o jogo:


```csharp
private void CreateFruitGraphic()
{
    graphic = new CCSprite ("cherry.png");
    graphic.IsAntialiased = false;
    this.AddChild (graphic);
}
```

Sempre que o player toca um `Fruit` instância com o `Paddle`, o valor do ponto de que frutas aumenta em um. Isso fornece um desafio extra ao players experientes para manipular frutas pontos extras. O valor do ponto de fruta é exibido usando o `extraPointsLabel` instância.

O `CreateExtraPointsLabel` método cria um `CCLabel` instância e adiciona-o para o `Fruit`:


```csharp
private void CreateExtraPointsLabel()
{
    extraPointsLabel = new CCLabel("", "Arial", 12, CCLabelFormat.SystemFont);
    extraPointsLabel.IsAntialiased = false;
    extraPointsLabel.Color = CCColor3B.Black;
    this.AddChild(extraPointsLabel);
}
```

Fruity vão inclui dois tipos diferentes de frutas – números errados e Limões. O `CreateFruitGraphic` atribui um visual padrão, mas os elementos visuais de frutas podem ser alterado por meio de `FruitColor` propriedade, que por sua vez chama `UpdateGraphics`:


```csharp
private void UpdateGraphics()
{
if (GameCoefficients.ShowCollisionAreas)
    {
        debugGrahic.Clear ();
        const float borderWidth = 4;
        debugGrahic.DrawSolidCircle (
            CCPoint.Zero,
            GameCoefficients.FruitRadius,
            CCColor4B.Black);
        debugGrahic.DrawSolidCircle (
            CCPoint.Zero,
            GameCoefficients.FruitRadius - borderWidth,
            fruitColor.ToCCColor ());
    }
    if (this.FruitColor == FruitColor.Yellow)
    {
        graphic.Texture = CCTextureCache.SharedTextureCache.AddImage ("lemon.png");
        extraPointsLabel.Color = CCColor3B.Black;
        extraPointsLabel.PositionY = 0;
    }
    else
    {
        graphic.Texture = CCTextureCache.SharedTextureCache.AddImage ("cherry.png");
        extraPointsLabel.Color = CCColor3B.White;
        extraPointsLabel.PositionY = -8;
    }
}
```

A primeira instrução if no `UpdateGraphics` atualiza os gráficos de depuração, que são usados para visualizar as áreas de colisão. Esses elementos visuais são desativados antes de uma versão final do jogo é feita, mas pode ser mantida em durante o desenvolvimento para depuração física. As alterações de parte segundo a `graphic.Texture` propriedade chamando `CCTextureCache.SharedTextureCache.AddImage`. Esse método fornece acesso a uma textura por nome de arquivo. Para obter mais informações sobre esse método podem ser encontradas na [guia cache de textura](~/graphics-games/cocossharp/texture-cache.md).

O `extraPointsLabel` cor é ajustada para manter o contraste com a imagem de frutas e sua `PositionY` valor é ajustado ao centro de `CCLabel` em de frutas `CCSprite`:

![](fruity-falls-images/image3.png "A cor de extraPointsLabel é ajustada para manter o contraste com a imagem de frutas e seu valor PositionY é ajustado para centralizar o CCLabel em de frutas CCSprite")

![](fruity-falls-images/image4.png "A cor de extraPointsLabel é ajustada para manter o contraste com a imagem de frutas e seu valor PositionY é ajustado para centralizar o CCLabel em de frutas CCSprite")


### <a name="collision"></a>Colisão

Fruity vão implementa uma solução de conflito personalizada usando objetos na pasta de geometria:

![](fruity-falls-images/image5.png "Fruity vão implementa uma solução de conflito personalizada usando objetos na pasta de geometria")

Colisão em vão Fruity é implementado usando o `Circle` ou `Polygon` objeto, normalmente com um destes dois tipos que está sendo adicionados como um filho de uma entidade. Por exemplo, o `Fruit` objeto tem um `Circle` chamado `Collision` que ele inicializa no seu `CreateCollision` método:


```csharp
private void CreateCollision()
{
    Collision = new Circle ();
    Collision.Radius = GameCoefficients.FruitRadius;
    this.AddChild (Collision);
}
```

Observe que o `Circle` classe herda o `CCNode` classe, portanto ele pode ser adicionado como um filho para entidades do nosso jogo:


```csharp
public class Circle : CCNode
{
    ...
}
```

`Polygon` criação é semelhante a `Circle` criação, como mostra a `Paddle` classe:


```csharp
private void CreateCollision()
{
    Polygon = Polygon.CreateRectangle(width, height);
    this.AddChild (Polygon);
}
```

Lógica de colisão é abordada [posteriormente neste guia](#collision).


## <a name="physics"></a>Física

Física em vão Fruity pode ser separadas em duas categorias: movimentação e a colisão. 


### <a name="movement-using-velocity-and-acceleration"></a>Usando a velocidade e aceleração de movimento

Usa vão Fruity `Velocity` e `Acceleration` valores para controlar o movimento de suas entidades, como o [BouncingGame](~/graphics-games/cocossharp/bouncing-game.md). Entidades implementam sua lógica de movimentação em um método chamado `Activity`, que é chamado uma vez por quadro. Por exemplo, podemos ver a implementação de movimentação no `Fruit` classe `Activity` método:

```csharp
public void Activity(float frameTimeInSeconds)
{
    timeUntilExtraPointsCanBeAdded -= frameTimeInSeconds;
    
    // linear approximation:
    this.Velocity += Acceleration * frameTimeInSeconds;

    // This is a linear approximation to drag. It's used to
    // keep the object from falling too fast, and eventually
    // to slow its horizontal movement. This makes the game easier
    this.Velocity -= Velocity * GameCoefficients.FruitDrag * frameTimeInSeconds;
    
    this.Position += Velocity * frameTimeInSeconds;
}
```
O `Fruit` objeto é exclusivo em sua implementação de arrastar – um valor que reduz a velocidade em relação a rapidez com que o processo está mudando. Essa implementação de arrastar fornece um *velocidade terminal*, que é a velocidade máxima que pode ser uma instância de frutas. Arraste também diminui a movimentação horizontal de frutas, o que torna o jogo um pouco mais fácil de executar.

O `Paddle` objeto também se aplica `Velocity` no seu `Activity` método, mas o `Velocity` é calculada usando uma `desiredLocation` valor:


```csharp
public void Activity(float frameTimeInSeconds)
{
    // This code will cause the cursor to lag behind the touch point
    // Increasing this value reduces how far the paddle lags
    // behind the player’s finger. 
    const float velocityCoefficient = 4;
    // Get the velocity from current location and touch location
    Velocity = (desiredLocation - this.Position) * velocityCoefficient;
    this.Position += Velocity * frameTimeInSeconds;
    ...
}
```

Normalmente, os jogos que usam `Velocity` controlar a posição de seu objetos não diretamente definir posições de entidade, pelo menos não após a inicialização. Em vez de definir diretamente a `Paddle` posição, o `Paddle.HandleInput` método atribui o `desiredLocation` valor:

```csharp
public void HandleInput(CCPoint touchPoint)
{
    desiredLocation = touchPoint;
}
```

### <a name="collision"></a>Colisão

Fruity vão implementa semirealista colisão entre fruta e outros objetos collidable, como o `Paddle` e `GameScene.Splitter`. Para ajudar a depurar colisão, áreas de colisão vão Fruity podem ficar visíveis, alterando o `GameCoefficients.ShowDebugInfo` no `GameCoefficients.cs` arquivo:


```csharp
public static class GameCoefficients
{
    ... 
    public const bool ShowCollisionAreas = true;
    ...
}
```

Definir esse valor como `true` habilita o processamento das áreas de colisão:  

![](fruity-falls-images/image6.png "Definir esse valor como true permite o processamento das áreas de colisão")

Lógica de colisão começa no `GameScene.Activity` método:


```csharp
private void Activity(float frameTimeInSeconds)
{
    if (hasGameEnded == false)
    {
        paddle.Activity(frameTimeInSeconds);
        foreach (var fruit in fruitList)
        {
            fruit.Activity(frameTimeInSeconds);
        }
        spawner.Activity(frameTimeInSeconds);
        DebugActivity();
        PerformCollision();
    }
}
```

`PerformCollision` manipula a colisão todos `Fruit` instâncias com outros objetos: 


```csharp
private void PerformCollision()
{
    // reverse for loop since fruit may be destroyed:
    for(int i = fruitList.Count - 1; i > -1; i--)
    {
        var fruit = fruitList[i];
        FruitVsPaddle(fruit);
        FruitPolygonCollision(fruit, splitter.Polygon, CCPoint.Zero);
        FruitVsBorders(fruit);
        FruitVsBins(fruit);
    }
}
```

#### <a name="fruitvsborders"></a>FruitVsBorders

`FruitVsBorders` colisão executa sua própria lógica de colisão em vez de depender da lógica contidas em uma classe diferente. Essa diferença existe porque a colisão entre frutas e bordas da tela não é perfeitamente sólida – é possível fruta sejam empurradas para fora da borda da tela pela movimentação raquete cuidado. Frutas serão Elástico fora da tela quando atingida com a raquete, mas se o player envia lentamente frutas moverá além da borda e da tela:


```csharp
private void FruitVsBorders(Fruit fruit)
{
    if(fruit.PositionX - fruit.Radius < 0 && fruit.Velocity.X < 0)
    {
        fruit.Velocity.X *= -1 * GameCoefficients.FruitCollisionElasticity;
    }
    if(fruit.PositionX + fruit.Radius > gameplayLayer.ContentSize.Width && fruit.Velocity.X > 0)
    {
        fruit.Velocity.X *= -1 * GameCoefficients.FruitCollisionElasticity;
    }
    if(fruit.PositionY + fruit.Radius > gameplayLayer.ContentSize.Height && fruit.Velocity.Y > 0)
    {
        fruit.Velocity.Y *= -1 * GameCoefficients.FruitCollisionElasticity;
    }
}
```

#### <a name="fruitvsbins"></a>FruitVsBins

O `FruitVsBins` método é responsável por verificar se qualquer frutas caiu em um os dois compartimentos. Nesse caso, o player concedido pontos (se a correspondência de cores de frutas/bin), ou o jogo termina (se as cores não correspondem):


```csharp
private void FruitVsBins(Fruit fruit)
{
    foreach(var bin in fruitBins)
    {
        if(bin.Polygon.CollideAgainst(fruit.Collision))
        {
            if(bin.FruitColor == fruit.FruitColor)
            {
                // award points:
                score += 1 + fruit.ExtraPointValue;
                scoreText.Score = score;
                Destroy(fruit);
            }
            else
            {
                EndGame();
            }
            break;
        }
    }
}
```

#### <a name="fruitvspaddle-and-fruitpolygoncollision"></a>FruitVsPaddle e FruitPolygonCollision

Frutas versus raquete e frutas versus divisor (a área separando os dois compartimentos) colisão ambos contam com o `FruitPolygonCollision` método. Esse método tem três partes:

1. Testar se os objetos colidem
1. Mover os objetos (apenas as frutas em vão Fruity) para que eles não se sobrepõem
1. Ajuste a velocidade dos objetos (apenas as frutas em vão Fruity) para simular um salto, que o código a seguir demonstra os pontos feitos anteriormente:


```csharp
private static bool FruitPolygonCollision(Fruit fruit, Polygon polygon, CCPoint polygonVelocity)
{
    // Test whether the fruit collides
    bool didCollide = polygon.CollideAgainst(fruit.Collision);
    if (didCollide)
    {
        var circle = fruit.Collision;
        // Get the separation vector to reposition the fruit so it doesn't overlap the polygon
        var separation = CollisionResponse.GetSeparationVector(circle, polygon);
        fruit.Position += separation;
        // Adjust the fruit's Velocity to make it bounce:
        var normal = separation;
        normal.Normalize();
        fruit.Velocity = CollisionResponse.ApplyBounce(
            fruit.Velocity, 
            polygonVelocity, 
            normal, 
            GameCoefficients.FruitCollisionElasticity);
    }
    return didCollide;
}
```

Resposta de colisão vão Fruity é unilateral – apenas as frutas velocidade e a posição são ajustados. Vale a pena observar que outros jogos podem ter colisão que afeta os dois objetos envolvidos, como um caractere de envio por push um boulder ou dois carros falhando em si.

A matemática por trás de lógica de colisão contidas no `Polygon` e `CollisionResponse` classes está além do escopo deste guia, mas como escrito, esses métodos podem ser usados para vários tipos de jogos. O polígono e `CollisionResponse` classes mesmo suportam não retangulares e convexos polígonos, portanto esse código pode ser usado para vários tipos de jogos.

 


## <a name="game-content"></a>Conteúdo de jogo

A arte do Fruity fica imediatamente distingue o jogo do BouncingGame. Enquanto os designs de jogos são semelhantes, players verá uma diferença na aparência dois jogos imediatamente. Gamers geralmente decida se deseja experimentar um jogo, seus visuais. Portanto, é extremamente importante que os desenvolvedores investirem recursos fazer visualmente atraentes jogo.

A arte do vão Fruity foi criada com as seguintes metas:

 - Tema consistente
 - Ênfase em apenas um caractere – monkey o Xamarin
 - Experiência de cores brilhantes para criar um relaxando agradável
 - Compare entre o primeiro e segundo plano para que objetos de jogo são fáceis de acompanhar visualmente
 - Capacidade de criar efeitos visuais simples sem animações intenso de recursos


### <a name="content-location"></a>Local do conteúdo

Fruity vão inclui todo seu conteúdo na pasta imagens no projeto Android:

![](fruity-falls-images/image7.png "Fruity vão inclui todo seu conteúdo na pasta imagens no projeto do Android")

Estes arquivos são vinculados no projeto de iOS para evitar a duplicação, e portanto alterações nos arquivos afetam os dois projetos:

![](fruity-falls-images/image8.png "Estes arquivos são vinculados no projeto de iOS para evitar a duplicação, e portanto alterações nos arquivos afetam os dois projetos")

Vale a pena observar que o conteúdo não está contido dentro de **Ld** ou **Hd** pastas, que fazem parte do modelo CocosSharp padrão. O **Ld** e **Hd** pastas destinam-se a ser usado para jogos que fornecem dois conjuntos de conteúdo — um para dispositivos de baixa resolução, como para dispositivos de resolução mais alta, como tablets e celulares. A arte vão Fruity é intencionalmente criada com um como pixels estética, portanto ele não precisa fornecer conteúdo para diferentes tamanhos de tela. Portanto, o **Ld** e **Hd** pastas foram completamente removidas do projeto.


### <a name="gamescene-layering"></a>GameScene camadas

Como mencionado anteriormente neste guia, o `GameScene` é responsável por todos os instanciação do objeto de jogo, posicionando e lógica entre objetos (como colisão). Todos os objetos são adicionados a um dos quatro `CCLayer` instâncias:


```csharp
CCLayer backgroundLayer;
CCLayer gameplayLayer;
CCLayer foregroundLayer;
CCLayer hudLayer;
```

Essas quatro camadas são criadas no `CreateLayers` método. Observe que eles são adicionados para o `GameScene` em ordem de trás para frente:


```csharp
private void CreateLayers()
{
    backgroundLayer = new CCLayer();
    this.AddLayer(backgroundLayer);
    gameplayLayer = new CCLayer();
    this.AddLayer(gameplayLayer);
    foregroundLayer = new CCLayer();
    this.AddLayer(foregroundLayer);
    hudLayer = new CCLayer();
    this.AddLayer(hudLayer);
}
```

Após as camadas são criadas, todos os objetos visuais são adicionados às camadas usando o `AddChild` método, como mostra o `CreateBackground` método:


```csharp
private void CreateBackground()
{
    var background = new CCSprite("background.png");
    background.AnchorPoint = new CCPoint(0, 0);
    background.IsAntialiased = false;
    backgroundLayer.AddChild(background);
}
```


### <a name="vine-entity"></a>Entidade videira

O `Vine` entidade é usada exclusivamente para conteúdo – ela não tem nenhum impacto em jogos. Ele é composto de vinte `CCSprite` instâncias, um número selecionado por tentativa e erro para certificar-se de que o videira sempre alcança a parte superior da tela:


```csharp
public Vine ()
{
    const int numberOfVinesToAdd = 20;
    for (int i = 0; i < numberOfVinesToAdd; i++)
    {
        var graphic = new CCSprite ("vine.png");
        graphic.AnchorPoint = new CCPoint(.5f, 0);
        graphic.PositionY = i * graphic.ContentSize.Height;
        this.AddChild (graphic);
    }
}
```

A primeira `CCSprite` é posicionado para a borda inferior corresponde à posição da videira. Isso é feito definindo o AnchorPoint como `new CCPoint(.5f, 0)`. O `AnchorPoint` usos de propriedade *normalizado coordenadas* qual o intervalo entre 0 e 1, independentemente do tamanho do `CCSprite`:

![](fruity-falls-images/image9.png "A propriedade AnchorPoint usa coordenadas normalizadas que intervalo entre 0 e 1, independentemente do tamanho do CCSprite")

Sprites videira subsequentes são posicionados usando o `graphic.ContentSize.Height` valor, que retorna a altura da imagem em pixels. O diagrama a seguir mostra como o gráfico de videira blocos para cima:

![](fruity-falls-images/image10.png "Este diagrama mostra como o gráfico de videira blocos para cima")

Desde que a origem do `Vine` entidade estiver na parte inferior de videira, posicionando-é simples, como é mostrado o `Paddle.CreateVines` método:


```csharp
private void CreateVines()
{
    // Increasing this value moves the vines closer to the 
    // center of the paddle.
    const int vineDistanceFromEdge = 4;
    leftVine = new Vine ();
    var leftEdge = -width / 2.0f;
    leftVine.PositionX = leftEdge + vineDistanceFromEdge;
    this.AddChild (leftVine);
    rightVine = new Vine ();
    var rightEdge = width / 2.0f;
    rightVine.PositionX = rightEdge - vineDistanceFromEdge;
    this.AddChild (rightVine);
}
```

Instâncias de videira o `Paddle` entidade também têm comportamento interessante de rotação. Como o `Paddle` entidade como um todo gira de acordo com a entrada do player (que este guia aborda em mais detalhes abaixo), o `Vine` instâncias também são afetadas por essa rotação. No entanto, girando o `Vine` instâncias junto com o `Paddle` produz um efeito visual indesejável, conforme mostrado na animação a seguir:

![](fruity-falls-images/image11.gif "No entanto, girar as instâncias de videira junto com a raquete produz um efeito visual indesejável conforme mostrado na seguinte animação")

Neutralizar o `Paddle` rotação, o `leftVine` e `rightVine` instâncias são giradas raquete, conforme mostrado na direção oposta a `Paddle.Activity` método:


```csharp
public void Activity(float frameTimeInSeconds)
{
    ...
    // This value adds a slight amount of rotation
    // to the vine. Having a small amount of tilt looks nice.
    float vineAngle = this.Velocity.X / 100.0f;
    leftVine.Rotation = -this.Rotation + vineAngle;
    rightVine.Rotation = -this.Rotation + vineAngle;
}
```

Observe que uma pequena quantidade de rotação é adicionada de volta ao videira por meio de `vineAngle` coeficiente. Esse valor pode ser alterado para ajustar a quantidade de vines girar.


## <a name="gamecoefficients"></a>GameCoefficients

Cada jogo bom é o produto de iteração, portanto vão Fruity inclui uma classe chamada `GameCoefficients` que controla como o jogo. Essa classe contém expressivas variáveis que são usadas em todo o jogo para controle física, layout, gerando e pontuação.

Por exemplo, a física de frutas é controlada pelas seguintes variáveis:


```csharp
public static class GameCoefficients
{
    ...
    
    // The strength of the gravity. Making this a 
    // smaller (bigger negative) number will make the
    // fruit fall faster. A larger (smaller negative) number
    // will make the fruit more floaty.
    public const float FruitGravity = -60;
    // The impact of "air drag" on the fruit, which gives
    // the fruit terminal velocity (max falling speed) and slows
    // the fruit's horizontal movement (makes the game easier)
    public const float FruitDrag = .1f;
    // Controls fruit collision bouncyness. A value of 1
    // means no momentum is lost. A value of 0 means all
    // momentum is lost. Values greater than 1 create a spring-like effect
    public const float FruitCollisionElasticity = .5f;
    
    ...
}
```

Como o nome sugere, essas variáveis podem ser usadas para ajustar a rapidez frutas vão, como horizontal movimentação diminui com o tempo e bounciness raquete.

Jogos coeficientes armazenados em arquivos de dados ou de código (como XML) podem ser especialmente útil para equipes com os designers de jogos que também não são programadores.

O `GameCoefficients` classe também incluem valores para ativar as informações de depuração como a geração de informações ou colisão áreas:


```csharp
public static class GameCoefficients
{
    ...
    // This controls whether debug information is displayed on screen.
    public const bool ShowDebugInfo = false;
    public const bool ShowCollisionAreas = false;
    ...
}
```


## <a name="conclusion"></a>Conclusão

Este guia explorou o jogo Fruity cairá. Ele coberto conceitos, incluindo conteúdo, física e gerenciamento de estado do jogo.

## <a name="related-links"></a>Links relacionados

- [Documentação da API do CocosSharp](https://developer.xamarin.com/api/namespace/CocosSharp/)
- [Projeto concluído (exemplo)](https://developer.xamarin.com/samples/mobile/FruityFalls/)
