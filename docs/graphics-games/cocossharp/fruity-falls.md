---
title: Detalhes do jogos Fruity Falls
description: Este guia examina o jogo Fruity cai, que abordam conceitos de desenvolvimento de jogos, como física, o gerenciamento de conteúdo, o estado do jogo e o projeto de jogo e CocosSharp comuns.
ms.prod: xamarin
ms.assetid: A5664930-F9F0-4A08-965D-26EF266FED24
author: conceptdev
ms.author: crdun
ms.date: 03/27/2017
ms.openlocfilehash: 959f5eb149ad375d686b17a85eb3d3b8fbdf3659
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61159956"
---
# <a name="fruity-falls-game-details"></a>Detalhes do jogos Fruity Falls

_Este guia examina o jogo Fruity cai, que abordam conceitos de desenvolvimento de jogos, como física, o gerenciamento de conteúdo, o estado do jogo e o projeto de jogo e CocosSharp comuns._

Fruity Falls é um jogo simple, baseado na física, em que o jogador classifica frutas amarelas e vermelhas em buckets coloridas para ganhar pontos. O objetivo do jogo é ganhar pontos tantos quanto possível sem deixar que uma queda de frutas no compartimento de errado, finalizar o jogo.

![](fruity-falls-images/image1.png "O objetivo do jogo é ganhar pontos tantos quanto possível sem deixar que uma queda de frutas no compartimento de errado, finalizar o jogo")

Fruity Falls estende os conceitos apresentados a [BouncingGame guia](~/graphics-games/cocossharp/bouncing-game.md) adicionando o seguinte:

 - De conteúdo na forma de PNGs
 - Física avançada
 - Estado do jogo (transição entre cenas)
 - Capacidade de ajustar os coeficientes de jogos por meio de variáveis contidas em uma única classe
 - Suporte de depuração visual interno
 - Organização do código usando entidades do jogos
 - Projeto de jogo voltada para o valor de repetição e divertido

Enquanto o [BouncingGame guia](~/graphics-games/cocossharp/bouncing-game.md) focado introduzindo conceitos fundamentais do CocosSharp, Enquadrado Fruity mostra como colocá-los juntos em um produto acabado de jogo. Uma vez que este guia faz referência o BouncingGame, os leitores devem primeiro se familiarizar com o [BouncingGame guia](~/graphics-games/cocossharp/bouncing-game.md) antes de ler este guia.

Este guia aborda a implementação e design de Fruity cai para fornecer informações para ajudá-lo a criar seu próprio jogo. Ele aborda os seguintes tópicos:


- [Classe GameController](#gamecontroller-class)
- [Entidades de jogo](#game-entities)
- [Gráficos de frutas](#fruit-graphics)
- [Física](#physics)
- [Conteúdo do jogo](#game-content)
- [GameCoefficients](#gamecoefficients)


## <a name="gamecontroller-class"></a>Classe GameController

O projeto de PCL Fruity cai inclui um `GameController` classe que é responsável por instanciar o jogo e a movimentação entre as cenas. Essa classe é usada por projetos do Android e iOS para eliminar código duplicado.

O código contido dentro de `Initialize` método é semelhante ao código no`Initialize` método em um modelo de CocosSharp inalterado, mas ele contém um número de modificações.

Por padrão, o `GameView.ContentManager.SearchPaths` depende da resolução do dispositivo. Conforme explicado abaixo em mais detalhes, Enquadrado Fruity usa o mesmo conteúdo, independentemente da resolução do dispositivo, portanto, o `Initialize` método adiciona o `Images` caminho (com não subpastas) para o `SearchPaths`:


```csharp
contentSearchPaths.Add ("Images");
```

Novos modelos de CocosSharp incluem uma classe que herda de `CCLayer`, implicando que elementos visuais de jogos e a lógica devem ser adicionados a essa classe. Em vez disso, Fruity cai usa vários `CCLayer` instâncias para o controle desenhe ordem. Esses `CCLayer` instâncias estão contidas dentro de `GameView` classe, que herda de `CCScene`. Fruity Falls também inclui várias cenas, o primeiro sendo o `TitleScene`. Portanto, o `Initialize` método instancia uma `TitleScene` instância que é passada para `RunWithScene`:


```csharp
var scene = new TitleScene (GameView);
GameView.Director.RunWithScene (scene);
```

O conteúdo para Fruity cai foi criado como arte de pixel de baixa resolução por motivos de estéticos. O `GameView.DesignResolution` é definido para que o jogo é apenas grande de 384 unidades e 512 de altura:


```csharp
// We use a lower-resolution display to get a pixellated appearance
int width = 384;
int height = 512;
GameView.DesignResolution = new CCSizeI (width, height); 
```

Por fim, o `GameController` classe fornece um método estático que pode ser chamado por qualquer `CCGameScene` para fazer a transição para outro `CCScene`. Esse método é usado para mover entre o `TitleScene` e o `GameScene`.


## <a name="game-entities"></a>Entidades de jogo

Fruity Falls faz uso do padrão de entidade para a maioria dos objetos do jogo. Obter uma explicação detalhada desse padrão pode ser encontrada na [guia de entidades em CocosSharp](~/graphics-games/cocossharp/entities.md).

Os objetos do jogo Implementando a entidade podem ser encontrados na pasta entidades na **FruityFalls.Common** projeto:

![](fruity-falls-images/image2.png "A pasta de entidades no projeto FruityFalls.Common")

Entidades são objetos que herdam de `CCNode`e pode ter elementos visuais, colisão e o comportamento de cada quadro.

O `Fruit` objeto representa uma entidade CocosSharp típico: ele contém um objeto visual, colisão e lógica de cada quadro. Seu construtor é responsável por inicializar fruta:


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

O `CreateFruitGraphic` método cria uma `CCSprite` da instância e adiciona-o para o `Fruit`. O `IsAntialiased` propriedade é definida como false para dar uma aparência de pixelada de jogo. Esse valor é definido como false em todos os `CCSprite` e `CCLabel` instâncias durante todo o jogo:


```csharp
private void CreateFruitGraphic()
{
    graphic = new CCSprite ("cherry.png");
    graphic.IsAntialiased = false;
    this.AddChild (graphic);
}
```

Sempre que o jogador toca um `Fruit` de instância com o `Paddle`, o valor de ponto de que fruta aumenta em um. Isso fornece um desafio adicional para os jogadores experientes fazer malabarismos nos frutas pontos extras. O valor do ponto da fruta é exibido usando o `extraPointsLabel` instância.

O `CreateExtraPointsLabel` método cria uma `CCLabel` da instância e adiciona-o para o `Fruit`:


```csharp
private void CreateExtraPointsLabel()
{
    extraPointsLabel = new CCLabel("", "Arial", 12, CCLabelFormat.SystemFont);
    extraPointsLabel.IsAntialiased = false;
    extraPointsLabel.Color = CCColor3B.Black;
    this.AddChild(extraPointsLabel);
}
```

Fruity Falls inclui dois tipos diferentes de frutas – números errados e Limões. O `CreateFruitGraphic` atribui um visual padrão, mas os visuais de frutas podem ser alterado por meio de `FruitColor` propriedade, que por sua vez chama `UpdateGraphics`:


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

A primeira instrução if no `UpdateGraphics` atualiza os gráficos de depuração, que são usados para visualizar áreas de colisão. Esses elementos visuais estão desativadas antes de uma versão final do jogo é realizada, mas pode ser mantida durante o desenvolvimento para depuração física. As alterações de parte segundo a `graphic.Texture` propriedade chamando `CCTextureCache.SharedTextureCache.AddImage`. Esse método fornece acesso a uma textura por nome de arquivo. Para obter mais informações sobre esse método podem ser encontradas na [guia de cache de textura](~/graphics-games/cocossharp/texture-cache.md).

O `extraPointsLabel` cor é ajustada para manter o contraste com a imagem de frutas e sua `PositionY` valor é ajustado à Central a `CCLabel` da fruta `CCSprite`:

![](fruity-falls-images/image3.png "A cor de extraPointsLabel é ajustada para manter o contraste com a imagem de fruta e seu valor PositionY é ajustado para centralizar o CCLabel nas frutas CCSprite")

![](fruity-falls-images/image4.png "A cor de extraPointsLabel é ajustada para manter o contraste com a imagem de fruta e seu valor PositionY é ajustado para centralizar o CCLabel nas frutas CCSprite")


### <a name="collision"></a>Colisão

Fruity Falls implementa uma solução de colisão personalizado usando os objetos na pasta de geometria:

![](fruity-falls-images/image5.png "Fruity Falls implementa uma solução de colisão personalizado usando os objetos na pasta de geometria")

Colisão em Fruity cai é implementado usando o `Circle` ou `Polygon` objeto, geralmente com um destes dois tipos que está sendo adicionados como um filho de uma entidade. Por exemplo, o `Fruit` objeto tem uma `Circle` chamado `Collision` que ele inicializa no seu `CreateCollision` método:


```csharp
private void CreateCollision()
{
    Collision = new Circle ();
    Collision.Radius = GameCoefficients.FruitRadius;
    this.AddChild (Collision);
}
```

Observe que o `Circle` herda o `CCNode` de classe, portanto, pode ser adicionado como um filho para entidades do nosso jogo:


```csharp
public class Circle : CCNode
{
    ...
}
```

`Polygon` criação é semelhante à `Circle` criação, como mostra a `Paddle` classe:


```csharp
private void CreateCollision()
{
    Polygon = Polygon.CreateRectangle(width, height);
    this.AddChild (Polygon);
}
```

Lógica de colisão é abordada [mais adiante neste guia](#collision).


## <a name="physics"></a>Física

A física em Fruity cai pode ser separada em duas categorias: movimentação e a colisão. 


### <a name="movement-using-velocity-and-acceleration"></a>Usando a velocidade e aceleração de movimentação

Usa Fruity Falls `Velocity` e `Acceleration` valores para controlar o movimento de suas entidades, semelhantes ao [BouncingGame](~/graphics-games/cocossharp/bouncing-game.md). Entidades de implementam sua lógica de movimentação em um método chamado `Activity`, que é chamado uma vez por quadro. Por exemplo, podemos ver a implementação de movimentação na `Fruit` classe `Activity` método:

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
O `Fruit` objeto é exclusivo em sua implementação de arrastar – um valor que reduz a velocidade em relação à velocidade fruta está se movendo. Essa implementação de arrastar fornece um *velocidade terminal*, que é a velocidade máxima que uma instância de frutas pode se enquadrar. Arraste também diminui o movimento horizontal de fruta, o que torna o jogo um pouco mais fácil de reproduzir.

O `Paddle` objeto também se aplica `Velocity` no seu `Activity` método, mas sua `Velocity` é calculada usando uma `desiredLocation` valor:


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

Normalmente, os jogos que usam `Velocity` controlar a posição de seu objetos não diretamente, definir posições de entidade, pelo menos não após a inicialização. Em vez de definir diretamente a `Paddle` posição, o `Paddle.HandleInput` método atribui o `desiredLocation` valor:

```csharp
public void HandleInput(CCPoint touchPoint)
{
    desiredLocation = touchPoint;
}
```

### <a name="collision"></a>Colisão

Fruity Falls implementa semirealista colisão entre fruta e outros objetos collidable, como o `Paddle` e `GameScene.Splitter`. Para ajudar a depurar colisão, áreas de colisão Fruity cai podem ficar visíveis, alterando a `GameCoefficients.ShowDebugInfo` no `GameCoefficients.cs` arquivo:


```csharp
public static class GameCoefficients
{
    ... 
    public const bool ShowCollisionAreas = true;
    ...
}
```

Definir esse valor como `true` permite a renderização de áreas de colisão:  

![](fruity-falls-images/image6.png "Definir esse valor como true permite o processamento das áreas de colisão")

Começa a lógica de colisão no `GameScene.Activity` método:


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

`PerformCollision` Controla se Unindo todos `Fruit` instâncias com outros objetos: 


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

`FruitVsBorders` colisão executa sua própria lógica de colisão em vez de depender da lógica contida em uma classe diferente. Essa diferença existe porque a colisão entre fruta e bordas da tela não é perfeitamente sólida – é possível para frutas sejam empurradas para fora da borda da tela, por um movimento de cuidado raquete. Frutas serão ricocheteada fora da tela quando atingida com a raquete, mas se o player envia lentamente frutas moverá além da borda e fora da tela:


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

O `FruitVsBins` método é responsável por verificar se qualquer frutas caiu em um dos dois compartimentos. Nesse caso, em seguida, o player é receberá pontos (se a correspondência de cores a fruta/bin) ou o jogo termina (se as cores não coincidem):


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

Frutas versus raquete e frutas versus divisor (a área separando os dois compartimentos) dependem de colisão de `FruitPolygonCollision` método. Esse método tem três partes:

1. Testar se os objetos colidem
1. Mover os objetos (apenas as frutas na Fruity cai) para que eles não se sobrepõem
1. Ajuste a velocidade dos objetos (apenas as frutas na Fruity cai) para simular um retorno de mensagem que o código a seguir demonstra os pontos que criou acima:


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

Resposta de colisão Fruity Falls é unilateral – apenas as frutas velocidade e a posição são ajustados. Vale a pena observar que outros jogos podem ter colisão que afeta a ambos os objetos envolvidos, como um caractere de envio por push um boulder ou dois carros falhando em si.

A matemática por trás da lógica de colisão contida na `Polygon` e `CollisionResponse` classes está além do escopo deste guia, mas como escrito, esses métodos podem ser usados para muitos tipos de jogos. O polígono e `CollisionResponse` classes até mesmo suportam não retangulares e convexos polígonos, portanto, esse código pode ser usado para vários tipos de jogos.

 


## <a name="game-content"></a>Conteúdo do jogo

A arte no Fruity cai imediatamente distingue o jogo do BouncingGame. Enquanto os designs de jogos são semelhantes, os jogadores perceberão imediatamente uma diferença na aparência dos jogos de dois. Jogadores geralmente decidem se deseja experimentar um jogo, seus visuais. Portanto, é extremamente importante que os desenvolvedores investirem recursos em tornando um visualmente atraente jogo.

A arte para Fruity cai foi criada com as seguintes metas:

 - Tema consistente
 - Ênfase em apenas um caractere – monkey Xamarin
 - Experiência de cores vivas para criar um relaxar agradável
 - Contraste entre o primeiro e segundo plano para que os objetos de jogo são fáceis de acompanhar visualmente
 - Capacidade de criar efeitos visuais simples sem animações com uso intenso de recursos


### <a name="content-location"></a>Local do conteúdo

Fruity Falls inclui todo o seu conteúdo na pasta de imagens no projeto do Android:

![](fruity-falls-images/image7.png "Fruity Falls inclui todo o seu conteúdo na pasta imagens no projeto do Android")

Estes arquivos estão vinculados no projeto do iOS para evitar a duplicação, e então para os arquivos afetam os dois projetos:

![](fruity-falls-images/image8.png "Estes arquivos estão vinculados no projeto do iOS para evitar a duplicação, e então para os arquivos afetam os dois projetos")

Vale a pena observar que o conteúdo não está contido dentro de **Ld** ou **Hd** pastas, que fazem parte do modelo de CocosSharp padrão. O **Ld** e **Hd** pastas devem ser usados para jogos que fornecem dois conjuntos de conteúdo – um para dispositivos de resolução mais baixa, como telefones e outra para dispositivos com resolução superior, como tablets. A arte de Fruity cai é intencionalmente criada com uma pixelada estética, para que ele não precisa fornecer o conteúdo de diferentes tamanhos de tela. Portanto, o **Ld** e **Hd** pastas foram completamente removidas do projeto.


### <a name="gamescene-layering"></a>Disposição em camadas de GameScene

Como mencionado anteriormente neste guia, o `GameScene` é responsável por todos os instanciação de objeto do jogo, posicionando e lógica entre objetos (como colisão). Todos os objetos são adicionados a um dos quatro `CCLayer` instâncias:


```csharp
CCLayer backgroundLayer;
CCLayer gameplayLayer;
CCLayer foregroundLayer;
CCLayer hudLayer;
```

Esses quatro camadas são criadas no `CreateLayers` método. Observe que eles são adicionados à `GameScene` em ordem de trás para frente:


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

Depois que as camadas são criadas, todos os objetos visuais são adicionados para as camadas usando o `AddChild` método, conforme mostrado no `CreateBackground` método:


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

O `Vine` entidade é usada exclusivamente para conteúdo – ele não tem impacto no jogo. Ele é composto de vinte `CCSprite` instâncias, um número selecionado por tentativa e erro para garantir a videira sempre atinja a parte superior da tela:


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

A primeira `CCSprite` é posicionado para a posição da videira corresponda a sua borda inferior. Isso é feito definindo o AnchorPoint como `new CCPoint(.5f, 0)`. O `AnchorPoint` usos de propriedade *normalizados coordenadas* qual o intervalo entre 0 e 1, independentemente do tamanho do `CCSprite`:

![](fruity-falls-images/image9.png "A propriedade AnchorPoint usa coordenadas normalizadas qual o intervalo entre 0 e 1, independentemente do tamanho do CCSprite")

Sprites videira subsequentes são posicionados usando o `graphic.ContentSize.Height` valor, que retorna a altura da imagem em pixels. O diagrama a seguir mostra como o gráfico de videira organiza lado a lado para cima:

![](fruity-falls-images/image10.png "Este diagrama mostra como o gráfico de videira organiza lado a lado para cima")

Desde a origem do `Vine` entidade for na parte inferior da videira, posicionando-é simples, conforme mostrado no `Paddle.CreateVines` método:


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

A videira instâncias no `Paddle` entidade também têm o comportamento de rotação interessante. Uma vez que o `Paddle` entidade como um todo gira de acordo com a entrada do jogador (que este guia aborda em mais detalhes abaixo), o `Vine` instâncias também são afetadas por essa rotação. No entanto, girando os `Vine` instâncias juntamente com o `Paddle` produz um efeito visual indesejado, conforme mostrado na animação a seguir:

![](fruity-falls-images/image11.gif "No entanto, girar as instâncias de videira junto com a raquete produz um efeito visual indesejado conforme mostrado na seguinte animação")

Para neutralizar os `Paddle` rotação, o `leftVine` e `rightVine` instâncias são giradas a raquete, conforme mostrado na direção oposta a `Paddle.Activity` método:


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

Observe que uma pequena quantidade de rotação é adicionada novamente para a videira por meio de `vineAngle` coeficiente. Esse valor pode ser alterado para ajustar o quanto os vines girar.


## <a name="gamecoefficients"></a>GameCoefficients

Cada jogo bom é o produto de iteração, portanto, Fruity cai inclui uma classe chamada `GameCoefficients` que controla como o jogo. Essa classe contém expressivas variáveis que são usadas em todo o jogo para o controle física, layout, gerando e pontuação.

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

Como os nomes sugerem, essas variáveis podem ser usadas para ajustar o quão rápidas frutas cai, como horizontal desaceleração do movimento para baixo ao longo do tempo e bounciness raquete.

Jogos coeficientes armazenados em arquivos de código ou dados (como XML) podem ser especialmente útil para equipes com os designers de jogos que também não são programadores.

O `GameCoefficients` classe também inclui valores para ativar informações de depuração, como a geração de informações ou colisão áreas:


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

Este guia explorou o jogo Fruity cai. Ele abordou os conceitos, incluindo conteúdo, física e gerenciamento de estado do jogo.

## <a name="related-links"></a>Links relacionados

- [Documentação da API do CocosSharp](https://developer.xamarin.com/api/namespace/CocosSharp/)
- [Projeto concluído (amostra)](https://developer.xamarin.com/samples/mobile/FruityFalls/)
