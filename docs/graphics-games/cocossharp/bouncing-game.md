---
title: Detalhes do BouncingGame
description: Este documento fornece um passo a passo para a criação de BouncingGame, um jogo de bola saltitante simple criado com CocosSharp.
ms.prod: xamarin
ms.assetid: AC9FD56F-6E4A-40DA-8168-45A761D869FD
author: conceptdev
ms.author: crdun
ms.date: 03/29/2018
ms.openlocfilehash: 42155eb541849f365e7fab0a3d5c3ad583e760b7
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50109400"
---
# <a name="bouncinggame-details"></a>Detalhes do BouncingGame

> [!TIP]
> O código para BouncingGame pode ser encontrado na [exemplos de Xamarin](https://developer.xamarin.com/samples/mobile/BouncingGame/). Para acompanhar melhor neste guia, baixe e explore o código como o guia faz referência a ele.

Este passo a passo mostra como implementar um jogo de bola saltitante simples usando CocosSharp. 

 - Descompactação do conteúdo do jogo
 - Elementos visuais de CocosSharp comuns
 - Adicionando elementos visuais para `GameLayer`
 - Implementar a lógica de cada quadro

Nosso jogo concluído terá esta aparência:

![BouncingGame, concluída](bouncing-game-images/image1.png "BouncingGame, concluída")

## <a name="unzipping-game-content"></a>Descompactação do conteúdo do jogo

Os desenvolvedores de jogos geralmente usam o termo *conteúdo* para se referir a arquivos sem código, que geralmente são criados por visual artistas, criadores de jogos ou designers de áudio. Tipos comuns de conteúdo incluem arquivos usados para exibir elementos visuais, tocar um som ou controlar o comportamento de inteligência artificial (AI). Da perspectiva da equipe de desenvolvimento de jogos, o conteúdo é geralmente criado por não-programadores. Nosso jogo inclui dois tipos de conteúdo:

 - arquivos PNG para definir a aparecem da bola e uma raquete.
 - Um arquivo único xnb para definir a fonte usada pela exibição de pontuação (abordada em mais detalhes quando adicionamos o placar abaixo)

Este conteúdo usado aqui pode ser encontrado no [zip de conteúdo](https://github.com/xamarin/mobile-samples/blob/master/BouncingGame/Resources/Content.zip?raw=true). Precisaremos desses arquivos baixados e descompactados em um local que acessaremos posteriormente neste passo a passo.

## <a name="common-cocossharp-visual-elements"></a>Elementos visuais de CocosSharp comuns

CocosSharp fornece um número de classes usadas para exibir visuais. Alguns elementos são diretamente visíveis, enquanto outros são usados para a organização. Vamos usar o seguinte no jogo:

 - `CCNode` – Classe base para todos os objetos visuais em CocosSharp. O `CCNode` classe contém uma `AddChild` função que pode ser usada para construir uma hierarquia pai/filho, também conhecida como uma *árvore visual* ou *grafo da cena*. Todas as classes mencionadas abaixo herdam `CCNode`.
 - `CCScene` – Raiz da árvore visual de todos os jogos de CocosSharp. Todos os elementos visuais devem fazer parte de uma árvore visual com um `CCScene` na raiz, ou eles não ficarão visíveis.
 - `CCLayer` – Objetos contêiner para o visual, como `CCSprite`. Como o nome implica, a `CCLayer` classe é usada para controlar como o visual camada de elementos na parte superior uns aos outros.
 - `CCSprite` – Exibe uma imagem ou uma parte de uma imagem. `CCSprite` as instâncias podem ser posicionadas, redimensionado e fornecem um número de efeitos visuais.
 - `CCLabel` – Exibe uma cadeia de caracteres na tela. A fonte usada pelo `CCLabel` é definido em um arquivo xnb. Discutiremos xnbs em mais detalhes abaixo.

Para entender como os diferentes tipos são usados, consideraremos um único `CCSprite`. Elementos visuais devem ser adicionados a um `CCLayer`, e a árvore visual deve ter um `CCScene` na raiz. Portanto, a relação pai/filho para um único `CCSprite` seria `CCScene`  >  `CCLayer`  >  `CCSprite`.

## <a name="adding-visual-elements-to-gamelayer"></a>Adicionando elementos visuais a GameLayer

### <a name="adding-the-paddle-sprite"></a>Adicionando o sprite raquete

Inicialmente, definiremos o plano de fundo do jogo para preto e também adicionar uma única `CCSprite` renderização na tela. Modificar a `GameLayer` classe para adicionar um `CCSprite` da seguinte maneira:

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
            // Use the bounds to layout the positioning of the drawable assets
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

O código anterior cria uma única `CCSprite` e adiciona-o como um filho de `GameLayer`. O `CCSprite` construtor permite definir o arquivo de imagem a ser usada como uma cadeia de caracteres. Nosso código diz ao CocosSharp para procurar um arquivo chamado `paddle` (a extensão for omitida, que discutiremos posteriormente neste guia). CocosSharp irá procurar quaisquer nomes de arquivo `paddle` na pasta raiz do conteúdo (que é **conteúdo**), bem como qualquer pasta adicionada para o `gameView.ContentManager.SearchPaths` (discutido na seção anterior).

Vamos adicionar os arquivos diretamente para a raiz **conteúdo** pasta para iOS e Android. Para fazer isso, mouse ou clique no controle de **conteúdo** pasta no projeto do iOS e selecione **Add** > **adicionar arquivos...** Navegue até onde podemos descompactou o conteúdo anteriormente e selecione **paddle.png**. Se for perguntado sobre como adicionar o arquivo à pasta, devemos selecionar o **cópia** opção:

![Adicionar arquivo à caixa de diálogo de pasta](bouncing-game-images/image2.png "a adicionar arquivo à caixa de diálogo de pasta")

Em seguida, vamos adicionar o arquivo ao projeto do Android. Mouse ou CTRL + clique na pasta de conteúdo (que está na **ativos** pasta de projetos do Android) e selecione **Add** > **adicionar arquivos...** . Desta vez, navegue até o projeto do iOS **conteúdo** pasta. Quando perguntado sobre como adicionar o arquivo, selecione a **adicionar um link** opção:

![Adicionar arquivo à caixa de diálogo de pasta](bouncing-game-images/addalink.png "adicionar o arquivo à caixa de diálogo de pasta")

Falaremos sobre por que os arquivos tinham a ser adicionado aos dois projetos abaixo. Cada projeto **conteúdo** pasta agora contém o **paddle.png** arquivo:

![O conteúdo da pasta de conteúdo](bouncing-game-images/image3.png "o conteúdo da pasta de conteúdo")

Se executarmos o jogo, veremos o `CCSprite` que está sendo desenhado:

![A raquete desenhada na tela](bouncing-game-images/image4.png "a raquete desenhada na tela")

### <a name="file-details"></a>Detalhes do arquivo

Até agora, adicionamos um único arquivo ao projeto e o processo foi bastante simples. Nós apenas adicionamos a **paddle.png** do arquivo para o **conteúdo** pastas e referenciado no código. Vamos examinar algumas considerações ao trabalhar com arquivos em CocosSharp.

#### <a name="capitalization"></a>Uso de maiúsculas

Observe que o nome do arquivo e a cadeia de caracteres é usada no código para acessar o arquivo tanto em letras minúsculas. Isso ocorre porque algumas plataformas (por exemplo, o simulador de iOS e de área de trabalho do Windows) diferenciam maiusculas de minúsculas, enquanto outras plataformas (por exemplo, um dispositivo Android e iOS) diferenciam maiusculas de minúsculas. Vamos usar todos os arquivos em letras minúsculas para o restante deste tutorial para que os arquivos e código permaneçam como plataforma cruzada quanto possível.

#### <a name="extensions"></a>Extensões

O construtor para criar o Sprite não inclui a extensão ". png" ao referenciar o arquivo raquete. A extensão para arquivos normalmente é omitida quando trabalhando em projetos de CocosSharp como extensões de arquivo para o mesmo tipo de ativo pode diferir entre plataformas. Por exemplo, arquivos de áudio podem ser dos formatos de arquivo. aiff,. mp3 ou. wma, dependendo da plataforma. Omitindo a extensão permite que o mesmo código trabalhar em todas as plataformas, independentemente da extensão de arquivo.

#### <a name="content-in-platform-specific-projects"></a>Em projetos específicos da plataforma de conteúdo

Ao contrário da maioria dos arquivos de código que podem ser em uma PCL, arquivos de conteúdo devem ser adicionados a cada projeto específico da plataforma. CocosSharp exige isso por dois motivos:

1. Cada plataforma tem diferentes **ações de Build**. O conteúdo adicionado aos projetos do iOS deve usar o **BundleResource** ação de build. Conteúdo adicionado para projetos do Android deve usar o **AndroidAsset** ação de build.
2. Algumas plataformas exigem formatos de arquivo específicos da plataforma. Por exemplo, os arquivos de fonte .xnb diferem entre o iOS e Android, como veremos mais adiante neste passo a passo.

Se um formato de arquivo não é a diferença entre as plataformas (por exemplo,. png), cada plataforma pode usar o mesmo arquivo, em que uma ou mais plataformas podem vincular o arquivo do mesmo local.

## <a name="orientation"></a>{1&gt;Orientação&lt;1}

Assim como qualquer outro aplicativo, aplicativos de CocosSharp podem ser executados em orientações retrato ou paisagem. Podemos ajustará o jogo para execução no modo de retrato. Primeiro, vamos alterar o código de resolução no jogo para lidar com uma taxa de proporção de retrato. Para fazer isso, modifique a `width` e `height` os valores na `LoadGame` método na `ViewController` classe no iOS e `MainActivity` classe no Android:

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
    // ...
```

Em seguida, precisamos modificar cada projeto específico da plataforma para executar no modo retrato.

### <a name="ios-orientation"></a>orientação do iOS

Para modificar a orientação do projeto do iOS, selecione a **Info. plist** do arquivo na **BouncingGame.iOS** do projeto e altere **informações de implantação do iPhone/iPod** e o **informações de implantação do iPad** para incluir apenas as orientações retrato:

![Opções de orientação](bouncing-game-images/image5.png "opções de orientação")

### <a name="android-orientation"></a>Orientação do Android

Para modificar a orientação do projeto Android, abra o arquivo MainActivity.cs no projeto BouncingGame.Android. Modifique a definição do atributo atividade para que ele especifica apenas uma orientação de retrato, da seguinte maneira:

```csharp
[Activity (
    Label = "BouncingGame.Android",
    AlwaysRetainTaskState = true,
    Icon = "@drawable/icon",
    Theme = "@android:style/Theme.NoTitleBar",
    ScreenOrientation = ScreenOrientation.Portrait,
    LaunchMode = LaunchMode.SingleInstance,
    MainLauncher = true,
    ConfigurationChanges = ConfigChanges.Orientation | ConfigChanges.ScreenSize | ConfigChanges.Keyboard | ConfigChanges.KeyboardHidden)
]
public class MainActivity : Activity
{ 
...
```

## <a name="default-coordinate-system"></a>Sistema de coordenadas padrão

Nosso código, que cria uma instância de um `CCSprite`, define o `PositionX` e `PositionY` valores como 100. Por padrão, isso significa que o `CCSprite` center será posicionado com 100 pixels até e à direita da parte inferior esquerda da tela. O sistema de coordenadas podem ser modificado, anexando uma `CCCamera` para o `CCLayer`. Nós não estar trabalhando com `CCCamera` este projeto, mas mais informações sobre `CCCamera` podem ser encontradas na [documentos de API de CocosSharp](https://developer.xamarin.com/api/type/CocosSharp.CCLayer/).

Os 100 pixels mencionados acima "jogos" pixels em vez de pixels no hardware. Isso significa que a execução mesmo jogo em um dispositivo de uma resolução diferente (como um iPad versus um iPhone) resultará em objetos que estão sendo posicionados e dimensionados corretamente em relação à tela física. Especificamente, área visível do jogo sempre será 1024 pixels de altura e 768 pixels de largura, pois essa é a resolução, especificamos anteriormente no `LoadGame` método.

## <a name="adding-the-ball-sprite"></a>Adicionando o sprite bola

Agora que estamos familiarizados com os fundamentos de trabalhar com `CCSprite`, vamos adicionar o segundo `CCSprite` – uma bola. Podemos estará seguindo as etapas que são muito semelhantes a como criamos a raquete `CCSprite`. 

Primeiro, vamos adicionar o **ball.png** imagem na pasta descompactada para o projeto de iOS **conteúdo** pasta. Selecione esta opção para **cópia** o arquivo para o **conteúdo** directory. Siga as mesmas etapas acima para adicionar um link para o **ball.png** arquivo no projeto do Android.

Em seguida crie o `CCSprite` para essa bola adicionando um membro chamado `ballSprite` para o `GameScene` classe, bem como o código de instanciação para o `ballSprite`. Quando terminar o `GameLayer` classe terá esta aparência:

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

## <a name="adding-the-score-label"></a>Adicionando o rótulo de pontuação

O último elemento visual, adicionaremos ao jogo é um `CCLabel` para exibir o número de vezes que o usuário tem êxito bateu bola. O `CCLabel` usa uma fonte especificada no construtor para exibir cadeias de caracteres na tela.

Adicione o seguinte código para criar uma `CCLabel` da instância no `GameLayer`. Após a conclusão, o código deve ter esta aparência:

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
    // ...
```

Observe que o scoreLabel está usando um `AnchorPoint` dos `CCPoint.AnchorUpperLeft`, o que significa que o `PositionX` e `PositionY` valores definem a posição superior esquerda. Isso nos permite posição a `scoreLabel` em relação ao canto superior esquerdo da tela sem precisar considerar as dimensões do rótulo.

## <a name="implementing-every-frame-logic"></a>Implementar a lógica de cada quadro

Até agora, o jogo apresenta uma cena estática. Estaremos adicionando lógica para controlar o movimento de objetos na cena, adicionando código que atualiza a posição dos objetos com alta frequência. Nesse caso, o código será executado sessenta vezes por segundo – também conhecido como sessenta *quadros* por segundo (a menos que o hardware não pode tratar atualizar isso com frequência). Especificamente, estaremos adicionando lógica para fazer a bola se enquadram e Elástico em relação a raquete, para mover a raquete de acordo com a entrada e para atualizar a pontuação do jogador toda vez que a bola atinge a raquete.

O `Schedule` método, que é fornecido pelo `CCNode` de classe, nos permite adicionar lógica de cada quadro para o jogo. Vamos adicionar o código após `// New code` para o construtor GameLayer:

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

Agora, crie uma `RunGameLogic` método no `GameLayer` classe, que conterá toda a lógica de cada quadro:

```csharp
void RunGameLogic(float frameTimeInSeconds)
{
}
```

O parâmetro de float define quanto tempo o quadro é em segundos. Usaremos esse valor ao implementar o movimento da bola.

### <a name="making-the-ball-fall"></a>Fazer com que a bola se enquadram

Podemos fazer a bola se enquadram por qualquer código de gravidade a implementação manualmente ou usando a funcionalidade interna de Box2D em CocosSharp. O mecanismo de simulação de física Box2D está disponível para os jogos de CocosSharp. Ele é muito poderoso e eficiente, mas requer a gravação de código de configuração. Como a simulação de física é bastante simple, aqui ela será implementada manualmente.

Para implementar a gravidade precisaremos repositório atual X e Y de velocidade da bola. Vamos adicionar dois membros para o `GameLayer` classe:

```csharp
float ballXVelocity;
float ballYVelocity;
// How much to modify the ball's y velocity per second:
const float gravity = 140;
```

Em seguida podemos implementar a lógica de queda no `RunGameLogic`:

```csharp
void RunGameLogic(float frameTimeInSeconds)
{
    // This is a linear approximation, so not 100% accurate
    ballYVelocity += frameTimeInSeconds * -gravity;
    ballSprite.PositionX += ballXVelocity * frameTimeInSeconds;
    ballSprite.PositionY += ballYVelocity * frameTimeInSeconds;
}
```

### <a name="moving-the-paddle-with-touch-input"></a>Mover a raquete com entrada de toque

Agora que a bola está se atrasando, vamos adicionar movimento horizontal para a raquete usando o `CCEventListenerTouchAllAtOnce` objeto. Esse objeto fornece um número de eventos relacionados à entrada. Nesse caso, queremos ser notificado se quaisquer pontos de toque mover, portanto, é possível ajustar a posição da raquete. O `GameLayer` já cria uma instância de um `CCEventListenerTouchAllAtOnce`, portanto, precisamos apenas atribuir a `OnTouchesMoved` delegar. Para fazer isso, modifique o método AddedToScene da seguinte maneira:

```csharp
protected override void AddedToScene ()
{
    base.AddedToScene ();
    // Use the bounds to layout the positioning of the drawable assets
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

### <a name="implementing-ball-collision"></a>Implementação de colisão da bola

Se executarmos o jogo agora, vamos perceber que a bola cai completamente a raquete. Implementaremos *colisão* (lógica de reagir a sobreposição de objetos do jogo) no código de cada quadro. Como mover objetos alteração posiciona cada quadro, verificando a colisão costuma ser também executada a cada quadro. Também incluiremos velocidade no eixo X quando a bola acerta a raquete para adicionar alguns desafios ao jogo, mas isso significa que precisamos impedir que a bola passar as bordas da tela. Vamos fazer isso a `RunGameLogic` código após a aplicação de velocidade para o `ballSprite` depois `// New Code`:

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

## <a name="adding-scoring"></a>Adicionar pontuação

Agora que o jogo é reproduzível, a última etapa é adicionar lógica de pontuação. Primeiro, vamos adicionar um membro de pontuação para a classe GameLayer denominada `score`:

```csharp
int score;
```

Usaremos essa variável para controlar a pontuação do jogador e para exibi-lo usando o `scoreLabel`. Para fazer isso adicione o seguinte código dentro da declaração if no `RunGameLogic` quando a bola e uma raquete se sobrepõem:

```csharp
// ...
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
// ...
```

Agora podemos executar o jogo e ver que o jogo exibe uma pontuação que incrementa conforme a bola refletida fora a raquete:

![O jogo concluído, com uma pontuação de incremento](bouncing-game-images/image1.png "o jogo concluído, com uma pontuação de incremento")

## <a name="summary"></a>Resumo

Este passo a passo apresentado criando um jogo de plataforma cruzada com elementos gráficos, física e entrada usando CocosSharp. É a primeira etapa na guia de Introdução ao desenvolvimento de jogos CocosSharp. Nós examinamos algumas das classes mais comuns em CocosSharp, como construir uma árvore visual para que os objetos são renderizados corretamente e como implementar a lógica do jogo de cada quadro.

Este passo a passo abordou apenas uma pequena parte do que o mecanismo de jogo de CocosSharp oferece. Para obter informações e instruções passo a passo sobre outros tópicos CocosSharp, consulte [o restante dos guias de CocosSharp](~/graphics-games/cocossharp/index.md).

## <a name="related-links"></a>Links relacionados

- [Jogo concluído (amostra)](https://developer.xamarin.com/samples/mobile/BouncingGame/)
- [Conteúdo do jogo (amostra)](https://github.com/xamarin/mobile-samples/blob/master/BouncingGame/Resources/Content.zip?raw=true)
