---
title: Implementando o jogo saltitante
description: Este passo a passo mostra como adicionar lógica de jogo e conteúdo a um modelo vazio para criar um jogo completo chamado BouncingGame.
ms.topic: article
ms.prod: xamarin
ms.assetid: AC9FD56F-6E4A-40DA-8168-45A761D869FD
ms.technology: xamarin-cross-platform
author: charlespetzold
ms.author: chape
ms.date: 03/27/2017
ms.openlocfilehash: 584ae03a3a773ae7e16fa7a24b9dbfa6c5056342
ms.sourcegitcommit: 7b88081a979381094c771421253d8a388b2afc16
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2018
---
# <a name="implementing-the-bouncing-game"></a>Implementando o jogo saltitante

_Este passo a passo mostra como adicionar lógica de jogo e conteúdo a um modelo vazio para criar um jogo completo chamado BouncingGame._

A parte anterior deste passo a passo mostrou como criar uma solução CocosSharp com projetos de desenvolvimento do Android e iOS. Este guia continuará com a implementação de um jogo completo, que abrangem o seguinte:

 - Descompactar nosso conteúdo de jogo
 - Elementos visuais de CocosSharp comuns
 - Adicionando elementos visuais para `GameLayer`
 - Implementar a lógica de cada quadro

Nosso jogo concluído será assim:

![](part2-images/image1.png "O jogo concluído se parecerá com")


# <a name="unzipping-our-game-content"></a>Descompactar nosso conteúdo de jogo

O termo geralmente usado por desenvolvedores de jogos *conteúdo* para se referir a arquivos que não é de código que geralmente são criados pelo visual artistas, designers de jogos ou áudio designers. Tipos de conteúdo comuns incluem arquivos usados para exibir elementos visuais, tocar um som ou controlar o comportamento de inteligência artificial (AI). Da perspectiva de uma equipe de desenvolvimento de jogos conteúdo é geralmente criado por programadores não. Nosso jogo inclui dois tipos de conteúdo:

 - Arquivos PNG para definir como nosso bola e raquete exibidos.
 - Um único arquivo XNB para definir a fonte usada pelo nosso vídeo de pontuação (discutido em mais detalhes quando podemos adicionar nossa exibição pontuação abaixo)

Este conteúdo usado aqui pode ser encontrado no [conteúdo zip](https://github.com/xamarin/mobile-samples/blob/master/BouncingGame/Resources/Content.zip?raw=true). Vamos precisar desses arquivos baixados e descompactado para um local que podemos acessarão posteriormente neste passo a passo.


# <a name="common-cocossharp-visual-elements"></a>Elementos de CocosSharp Visual comuns

CocosSharp fornece um número de classes usadas para exibir elementos visuais. Alguns elementos são diretamente visíveis, enquanto outros são usados para organização. Vamos usar o seguinte no nosso jogo:

 - `CCNode` -Classe base para todos os objetos visuais em CocosSharp. O `CCNode` classe contém um `AddChild` função que pode ser usada para construir uma hierarquia pai/filho, também conhecida como uma *árvore visual* ou *gráfico de cena*. Todas as classes mencionadas abaixo herdam `CCNode`.
 - `CCScene` – Raiz da árvore visual de todos os jogos CocosSharp. Todos os elementos visuais devem ser parte de uma árvore visual com um `CCScene` na raiz, ou eles não ficarão visíveis.
 - `CCLayer` – Objetos de contêiner Visual, como `CCSprite`. Como o nome implica, a `CCLayer` classe é usada para controlar como o visual camada elementos uns sobre os outros.
 - `CCSprite` – Exibe uma imagem ou uma parte de uma imagem. `CCSprite` as instâncias podem ser posicionadas, redimensionadas e fornecem um número de efeitos visuais.
 - `CCLabel` – Exibe uma cadeia de caracteres na tela. A fonte usada pelo `CCLabel` é definido em um arquivo XNB. Discutiremos XNBs em mais detalhes abaixo.

Para entender como os diferentes tipos são usados consideraremos um único `CCSprite`. Elementos visuais devem ser adicionados a um `CCLayer`, e a árvore visual deve ter um `CCScene` na raiz. Portanto, a relação pai/filho para um único `CCSprite` seria `CCScene`  >  `CCLayer`  >  `CCSprite`.


# <a name="adding-visual-elements-to-gamelayer"></a>Adicionando elementos visuais para GameLayer


## <a name="adding-the-paddlesprite"></a>Adicionando o paddleSprite

Inicialmente, vamos definir plano de fundo do jogo para preto e também adicionar uma única `CCSprite` renderização na tela. Modificar o `GameLayer` classe para adicionar um `CCSprite` da seguinte maneira:


```csharp
using System;
using System.Collections.Generic;
using CocosSharp;
namespace BouncingGame
{
    public class GameLayer : CCLayer
    {
        CCSprite paddleSprite;
        public GameLayer () : base(CCColor4B.Black)
        {
            // "paddle" refers to the paddle.png image
            paddleSprite = new CCSprite ("paddle");
            paddleSprite.PositionX = 100;
            paddleSprite.PositionY = 100;
            AddChild (paddleSprite);
        }
        protected override void AddedToScene ()
        {
            base.AddedToScene ();
            // Use the bounds to layout the positioning of our drawable assets
            CCRect bounds = VisibleBoundsWorldspace;
            // Register for touch events
            var touchListener = new CCEventListenerTouchAllAtOnce ();
            touchListener.OnTouchesEnded = OnTouchesEnded;
            AddEventListener (touchListener, this);
        }
        void OnTouchesEnded (List<CCTouch> touches, CCEvent touchEvent)
        {
            if (touches.Count > 0)
            {
                // Perform touch handling here
            }
        }
    }
}
```

O código acima cria um único `CCSprite` e adiciona-o como um filho de `GameLayer`. O `CCSprite` construtor permite definir o arquivo de imagem para usar como uma cadeia de caracteres. Nosso código informa CocosSharp para procurar um arquivo chamado `paddle` (a extensão for omitida, que discutiremos posteriormente neste guia). CocosSharp procurará por quaisquer nomes de arquivo `paddle` na pasta raiz de conteúdo (que é **conteúdo**), bem como qualquer pasta adicionada para o `gameView.ContentManager.SearchPaths` (discutido na seção anterior).

Vamos adicionar nossos arquivos diretamente para a raiz **conteúdo** pasta para iOS e Android. Para fazer isso, clique com botão direito ou Control, clique no **conteúdo** pasta no projeto de iOS e selecione **adicionar** > **adicionar arquivos...** Navegue até onde podemos descompactou o conteúdo anteriormente e selecione **paddle.png**. Se for perguntado sobre como adicionar o arquivo de pasta, deve selecionamos o **cópia** opção:

![](part2-images/image2.png "Se for perguntado sobre como adicionar o arquivo de pasta, selecione a opção de cópia")

Em seguida, adicionaremos o arquivo para o projeto Android. Clique ou CTRL + clique na pasta de conteúdo (que é o **ativos** pasta em projetos Android) e selecione **adicionar** > **adicionar arquivos...** . Neste momento, navegue até o projeto de iOS **conteúdo** pasta. Quando perguntado sobre como adicionar o arquivo, selecione o **adicionar um link** opção:

![](part2-images/addalink.png "Quando perguntado sobre como adicionar o arquivo, selecione Adicionar uma opção de link")

Discutiremos por que os arquivos tinham a ser adicionado a ambos os projetos abaixo. Cada projeto **conteúdo** pasta agora contém o **paddle.png** arquivo:

![](part2-images/image3.png "Pasta de conteúdo de cada projeto agora contém o arquivo paddle.png")

Se executarmos o jogo veremos nosso `CCSprite` desenhado:

![](part2-images/image4.png "Se o jogo é executado, o CCSprite será desenhada")


## <a name="file-details"></a>Detalhes do arquivo

Até agora adicionamos um único arquivo para o projeto e o processo foi bastante direta. Adicionamos simplesmente o **paddle.png** de arquivos para nosso **conteúdo** pastas e referenciado-la no código. Vamos examinar algumas considerações ao trabalhar com arquivos em CocosSharp.

### <a name="capitalization"></a>Uso de maiúsculas

Observe que o nome do arquivo e a cadeia de caracteres é usada no código para acessar o arquivo estejam em letras minúsculas. Isso ocorre porque algumas plataformas (como o Windows simulator de área de trabalho e iOS) diferenciam maiusculas de minúsculas, enquanto outras plataformas (como o dispositivo Android e iOS) diferenciam maiusculas de minúsculas. Usaremos todos os arquivos em letras minúsculas para o restante deste tutorial para que os arquivos e o código permaneçam como plataforma cruzada possível.

### <a name="extensions"></a>Extensões

O construtor para criar a entidade gráfica não inclui a extensão "PNG" ao referenciar o arquivo raquete. A extensão de arquivos normalmente é omitida ao trabalhar em projetos de CocosSharp como extensões de arquivo para o mesmo tipo de ativo pode diferir entre plataformas. Por exemplo, arquivos de áudio podem ser dos formatos de arquivo. wma,. mp3 ou. aiff, dependendo da plataforma. Omitindo a extensão permite que o mesmo código funcione em todas as plataformas, independentemente da extensão de arquivo.

### <a name="content-in-platform-specific-projects"></a>Conteúdo em projetos específicos de plataforma

Ao contrário da maioria de nossos arquivos de código que podem ser um PCL, conteúdo de arquivos devem ser adicionados para cada projeto específico de plataforma. CocosSharp requer isso por dois motivos:

1. Cada plataforma tem diferentes **ações de construção**. Conteúdo adicionado a projetos de iOS deve usar o **BundleResource** ação de compilação. Conteúdo adicionado a projetos Android deve usar o **AndroidAsset** ação de compilação.
2. Algumas plataformas exigem formatos específicos de plataforma. Por exemplo, arquivos de fonte de .xnb diferem entre iOS e Android, conforme veremos mais adiante neste passo a passo.

Se um formato de arquivo não é a diferença entre as plataformas (por exemplo,. png), cada plataforma pode usar o mesmo arquivo, em que uma ou mais plataformas podem vincular o arquivo do mesmo local.

# <a name="orientation"></a>{1&gt;Orientação&lt;1}

Assim como qualquer outro aplicativo, CocosSharp aplicativos podem executar em orientações retrato ou paisagem. Podemos ajustará nosso jogo para execução no modo de retrato. Primeiro, vamos alterar o código de resolução em nosso jogo para lidar com uma taxa de proporção de retrato. Para fazer isso, modifique o `width` e `height` valores no `LoadGame` método o `ViewController` classe no iOS e `MainActivity` classe no Android:


```csharp
void LoadGame (object sender, EventArgs e)
{
    CCGameView gameView = sender as CCGameView;

    if (gameView != null)
    {
        var contentSearchPaths = new List<string> () { "Fonts", "Sounds" };
        CCSizeI viewSize = gameView.ViewSize;

        int width = 768;
        int height = 1027;
    ...
```

Em seguida, será preciso modificar cada projeto específico de plataforma para executar no modo retrato.


## <a name="ios-orientation"></a>Orientação do iOS

Para modificar a orientação do iOS do projeto, selecione o **Info. plist** arquivo o **BouncingGame.iOS** do projeto e alterar **iPhone/iPod informações de implantação** e o **iPad informações de implantação** para incluir apenas as orientações de retrato:

![](part2-images/image5.png "Alterar iPhone/iPod informações de implantação e informações de implantação para incluir apenas as orientações de retrato iPad")


## <a name="android-orientation"></a>Orientação do Android

Para modificar a orientação do projeto Android, abra o arquivo MainActivity.cs no projeto BouncingGame.Android. Modificar a definição do atributo de atividade para que ela especifica uma orientação de retrato da seguinte maneira:


```csharp
[Activity (
    Label = "BouncingGame.Android",
    AlwaysRetainTaskState = true,
    Icon = "@drawable/icon",
    Theme = "@android:style/Theme.NoTitleBar",
    ScreenOrientation = ScreenOrientation.Portrait,
    LaunchMode = LaunchMode.SingleInstance,
    MainLauncher = true,
    ConfigurationChanges = ConfigChanges.Orientation | ConfigChanges.ScreenSize | ConfigChanges.Keyboard | ConfigChanges.KeyboardHidden)
]
public class MainActivity : Activity
{ 
...
```


# <a name="default-coordinate-system"></a>Sistema de coordenadas padrão

Nosso código, que instancia um `CCSprite`, define o `PositionX` e `PositionY` valores como 100. Por padrão, isso significa que o `CCSprite` center será posicionado com 100 pixels até e à direita da parte inferior esquerda da tela. O sistema de coordenadas podem ser modificado, anexando um `CCCamera` para o `CCLayer`. Nós não estar funcionando com `CCCamera` no projeto, mas mais informações sobre `CCCamera` pode ser encontrado no [documentos CocosSharp API](https://developer.xamarin.com/api/type/CocosSharp.CCLayer/).

100 pixels mencionados acima pixels "jogos" em vez de pixels no hardware. Isso significa que a execução mesmo jogo em um dispositivo de uma resolução diferente (como um iPad versus um iPhone) resultará em objetos que estão sendo posicionados e dimensionados corretamente em relação à tela física. Especificamente, área visível do nosso jogo sempre será 1024 pixels de altura e 768 pixels de largura, pois essa é a resolução especificamos anteriormente no `LoadGame` método.


# <a name="adding-the-ball-ccsprite"></a>Adicionando a bola CCSprite

Agora que estamos familiarizados com os conceitos básicos de como trabalhar com `CCSprite`, vamos adicionar nosso segundo `CCSprite` – um indicador. Podemos seguir etapas que são muito semelhantes às como criamos a raquete `CCSprite`. 

Primeiro, vamos adicionar o **ball.png** imagem da pasta descompactada em nosso projeto de iOS **conteúdo** pasta. Selecione para **cópia** o arquivo para nosso **conteúdo** directory. Siga as etapas acima para adicionar um link para o **ball.png** arquivo no projeto Android.

Em seguida, crie o `CCSprite` para esta bola adicionando um membro chamado `ballSprite` para nosso `GameScene` classe, bem como o código de instanciação para o `ballSprite`. Quando terminar de nosso `GameLayer` classe terá esta aparência:


```csharp
public class GameLayer : CCLayer
{
    CCSprite paddleSprite;
    CCSprite ballSprite;
    public GameLayer () : base (CCColor4B.Black)
    {
        // "paddle" refers to the paddle.png image
        paddleSprite = new CCSprite ("paddle");
        paddleSprite.PositionX = 100;
        paddleSprite.PositionY = 100;
        AddChild (paddleSprite);
        ballSprite = new CCSprite ("ball");
        ballSprite.PositionX = 320;
        ballSprite.PositionY = 600;
        AddChild (ballSprite);
    }
```


# <a name="adding-the-score-cclabel"></a>Adicionando o CCLabel de pontuação

O último elemento visual, adicionaremos ao nosso jogo é um `CCLabel` para exibir o número de vezes que o usuário tem devolvida com êxito a bola. O `CCLabel` usa uma fonte especificada no construtor de cadeias de caracteres de exibição na tela.

Adicione o seguinte código para criar um `CCLabel` de instância `GameLayer`. Uma vez concluído, nosso código deve ter esta aparência:


```csharp
public class GameLayer : CCLayer
{
    CCSprite paddleSprite;
    CCSprite ballSprite;
    CCLabel scoreLabel;
    public GameLayer () : base (CCColor4B.Black)
    {
        // "paddle" refers to the paddle.png image
        paddleSprite = new CCSprite ("paddle");
        paddleSprite.PositionX = 100;
        paddleSprite.PositionY = 100;
        AddChild (paddleSprite);
        ballSprite = new CCSprite ("ball");
        ballSprite.PositionX = 320;
        ballSprite.PositionY = 600;
        AddChild (ballSprite);
        scoreLabel = new CCLabel ("Score: 0", "Arial", 20, CCLabelFormat.SystemFont);
        scoreLabel.PositionX = 50;
        scoreLabel.PositionY = 1000;
        scoreLabel.AnchorPoint = CCPoint.AnchorUpperLeft;
        AddChild (scoreLabel);
    }
    ...
```

Observe que o scoreLabel está usando um `AnchorPoint` de `CCPoint.AnchorUpperLeft`, o que significa que o `PositionX` e `PositionY` valores definem a posição superior esquerda. Isso nos permite posição a `scoreLabel` em relação ao canto superior esquerdo da tela sem ter que considerar as dimensões do rótulo.


# <a name="implementing-every-frame-logic"></a>Implementar a lógica de cada quadro

Até agora, nosso jogo apresenta uma cena estática. Incluiremos lógica para controlar a movimentação de objetos em nosso cena adicionando o código que atualiza a posição de nossos objetos em uma frequência alta. Nesse caso, o código será executado sessenta vezes por segundo - também conhecido como sessenta *quadros* por segundo (a menos que o hardware não pode tratar atualizar isso frequentemente). Especificamente, incluiremos lógica para fazer a bola fallback e Elástico em relação a raquete para mover a raquete de acordo com a entrada e para atualizar a pontuação do player toda vez que a bola atinja o raquete.

O `Schedule` método, que é fornecido pelo `CCNode` da classe, nos permite adicionar lógica de cada quadro ao nosso jogo. Vamos adicionar o código após `// New code` ao construtor GameLayer:


```csharp
public GameLayer () : base (CCColor4B.Black)
{
    // "paddle" refers to the paddle.png image
    paddleSprite = new CCSprite ("paddle");
    paddleSprite.PositionX = 100;
    paddleSprite.PositionY = 100;
    AddChild (paddleSprite);
    ballSprite = new CCSprite ("ball");
    ballSprite.PositionX = 320;
    ballSprite.PositionY = 600;
    AddChild (ballSprite);
        scoreLabel = new CCLabel ("Score: 0", "arial", 22, CCLabelFormat.SpriteFont);
    scoreLabel.PositionX = 50;
    scoreLabel.PositionY = 1000;
    scoreLabel.AnchorPoint = CCPoint.AnchorUpperLeft;
    AddChild (scoreLabel);
    // New code:
    Schedule (RunGameLogic);
}
```

Agora, crie um `RunGameLogic` método em nosso `GameLayer` classe, que armazenará todas nossa lógica de cada quadro:


```csharp
void RunGameLogic(float frameTimeInSeconds)
{
}
```

O parâmetro float define quanto tempo o nosso quadro é em segundos. Usaremos esse valor ao implementar a movimentação da bola.


## <a name="making-the-ball-fall"></a>Fazendo a queda de bola

Podemos tornar a bola estão implementando manualmente nosso próprio código de gravidade ou usando a funcionalidade interna de Box2D em CocosSharp. O mecanismo de simulação Box2D física está disponível para jogos CocosSharp. Ele é muito eficiente e eficiente, mas requer a criação de código de instalação. Como nossa simulação física é bem simple, vamos implementá-lo manualmente.

Para implementar a gravidade precisaremos repositório atual X e Y velocidade da nossa bola. Vamos adicionar dois membros para nosso `GameLayer` classe:


```csharp
float ballXVelocity;
float ballYVelocity;
// How much to modify the ball's y velocity per second:
const float gravity = 140;
```

Em seguida podemos implementar a lógica de queda em `RunGameLogic`:

```csharp
void RunGameLogic(float frameTimeInSeconds)
{
    // This is a linear approximation, so not 100% accurate
    ballYVelocity += frameTimeInSeconds * -gravity;
    ballSprite.PositionX += ballXVelocity * frameTimeInSeconds;
    ballSprite.PositionY += ballYVelocity * frameTimeInSeconds;
}
```

## <a name="moving-the-paddle-with-touch-input"></a>Mover a raquete com entrada de toque

Agora que a bola é caindo, adicionaremos movimento horizontal para nosso raquete usando o `CCEventListenerTouchAllAtOnce` objeto. Esse objeto fornece um número de eventos de entrada. Em nosso caso, queremos ser notificado se quaisquer pontos de toque mover para que possa ajustar a posição da raquete. O `GameLayer` já instancia um `CCEventListenerTouchAllAtOnce`, portanto, precisamos simplesmente atribuir o `OnTouchesMoved` delegate. Para fazer isso, modifique o método AddedToScene da seguinte maneira:


```csharp
protected override void AddedToScene ()
{
    base.AddedToScene ();
    // Use the bounds to layout the positioning of our drawable assets
    CCRect bounds = VisibleBoundsWorldspace;
    // Register for touch events
    var touchListener = new CCEventListenerTouchAllAtOnce ();
    touchListener.OnTouchesEnded = OnTouchesEnded;
    // new code:
    touchListener.OnTouchesMoved = HandleTouchesMoved;
    AddEventListener (touchListener, this);
}
```

Em seguida, implementaremos `HandleTouchesMoved`:


```csharp
void HandleTouchesMoved (System.Collections.Generic.List<CCTouch> touches, CCEvent touchEvent)
{
    // we only care about the first touch:
    var locationOnScreen = touches [0].Location;
    paddleSprite.PositionX = locationOnScreen.X;
}
```


## <a name="implementing-ball-collision"></a>Implementação de colisão de bola

Se executarmos o jogo agora, você notará que a bola passará a raquete. Implementaremos *colisão* (lógica de reagir a sobreposição de objetos do jogo) em nosso código de cada quadro. Como mover objetos alteração posiciona cada quadro, verificação de colisão é normalmente executada também todos os quadros. Também incluiremos velocidade no eixo X quando a bola raquete para adicionar alguns desafio ao jogo, mas isso significa que precisamos impedir que a bola movendo após as bordas da tela. Faremos isso `RunGameLogic` código após a aplicação de velocidade para o `ballSprite` depois `// New Code`:


```csharp
void RunGameLogic(float frameTimeInSeconds)
{
    // This is a linear approximation, so not 100% accurate
    ballYVelocity += frameTimeInSeconds * -gravity;
    ballSprite.PositionX += ballXVelocity * frameTimeInSeconds;
    ballSprite.PositionY += ballYVelocity * frameTimeInSeconds;
    // New Code:
    // Check if the two CCSprites overlap...
    bool doesBallOverlapPaddle = ballSprite.BoundingBoxTransformedToParent.IntersectsRect(
        paddleSprite.BoundingBoxTransformedToParent);
    // ... and if the ball is moving downward.
    bool isMovingDownward = ballYVelocity < 0;
    if (doesBallOverlapPaddle && isMovingDownward)
    {
        // First let's invert the velocity:
        ballYVelocity *= -1;
        // Then let's assign a random value to the ball's x velocity:
        const float minXVelocity = -300;
        const float maxXVelocity = 300;
        ballXVelocity = CCRandom.GetRandomFloat (minXVelocity, maxXVelocity);
    }
    // First let’s get the ball position:   
    float ballRight = ballSprite.BoundingBoxTransformedToParent.MaxX;
    float ballLeft = ballSprite.BoundingBoxTransformedToParent.MinX;
    // Then let’s get the screen edges
    float screenRight = VisibleBoundsWorldspace.MaxX;
    float screenLeft = VisibleBoundsWorldspace.MinX;
    // Check if the ball is either too far to the right or left:    
    bool shouldReflectXVelocity = 
        (ballRight > screenRight && ballXVelocity > 0) ||
        (ballLeft < screenLeft && ballXVelocity < 0);
    if (shouldReflectXVelocity)
    {
        ballXVelocity *= -1;
    }
}
```


## <a name="adding-scoring"></a>Adição de pontuação

Agora que o nosso jogo é executável, a última etapa é adicionar lógica de pontuação. Primeiro, adicionaremos um membro de pontuação para a classe GameLayer denominada `score`:


```csharp
int score;
```

Vamos usar essa variável para manter o controle de pontuação do player e para exibi-lo usando nosso `scoreLabel`. Para fazer isso adicione o seguinte código dentro a instrução if em `RunGameLogic` quando se sobrepõem a bola e raquete:


```csharp
...
if (doesBallOverlapPaddle && isMovingDownward)
{
    // First let's invert the velocity:
    ballYVelocity *= -1;
    // Then let's assign a random to the ball's x velocity:
    const float minXVelocity = -300;
    const float maxXVelocity = 300;
    ballXVelocity = CCRandom.GetRandomFloat (minXVelocity, maxXVelocity);
    // New code:
    score++;
    scoreLabel.Text = "Score: " + score;
}
...
```

Agora podemos executar o jogo e consulte o nosso jogo exibe uma pontuação que incrementa conforme a bola devoluções fora a raquete:

![](part2-images/image1.png "Executar o jogo e ver que ele exibe uma pontuação que incrementa conforme a bola devoluções fora a raquete")


# <a name="summary"></a>Resumo

Este passo a passo apresentados criar um jogo de plataforma cruzada com elementos gráficos, a física e a entrada usando CocosSharp. É a primeira etapa na guia de Introdução ao desenvolvimento de jogos CocosSharp. Fizemos algumas das classes mais comuns em CocosSharp, como construir uma árvore visual para que objetos renderizado corretamente e como implementar a lógica de jogo cada quadro.

Este passo a passo coberto apenas uma pequena parte do que o mecanismo de jogo CocosSharp oferece. Para obter informações e instruções passo a passo em outros tópicos CocosSharp, consulte [o restante da nossos guias CocosSharp](~/graphics-games/cocossharp/index.md).

## <a name="related-links"></a>Links relacionados

- [Jogo concluído (exemplo)](https://developer.xamarin.com/samples/mobile/BouncingGame/)
- [Conteúdo do jogo (exemplo)](https://github.com/xamarin/mobile-samples/blob/master/BouncingGame/Resources/Content.zip?raw=true)
