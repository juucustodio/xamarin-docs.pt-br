---
title: APIs de jogos do iOS no Xamarin. iOS
description: Este artigo aborda os novos aprimoramentos de jogos fornecidos pelo iOS 9 que podem ser usados para melhorar os recursos gráficos e de áudio do seu jogo Xamarin. iOS.
ms.prod: xamarin
ms.assetid: 958D38FD-9240-482E-9A42-D6671ED8F2B0
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/20/2017
ms.openlocfilehash: 20efcf1af10b7c1d3d36e570bc838e396241ffee
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91436245"
---
# <a name="ios-gaming-apis-in-xamarinios"></a>APIs de jogos do iOS no Xamarin. iOS

_Este artigo aborda os novos aprimoramentos de jogos fornecidos pelo iOS 9 que podem ser usados para melhorar os recursos gráficos e de áudio do seu jogo Xamarin. iOS._

A Apple fez várias melhorias tecnológicas nas APIs de jogos no iOS 9 que facilitam a implementação de gráficos e áudio de jogos em um aplicativo Xamarin. iOS.
Isso inclui a facilidade de desenvolvimento por meio de estruturas de alto nível e o aproveitamento do poder da GPU do dispositivo iOS para melhorar a velocidade e os recursos gráficos.

[![Um exemplo de um aplicativo que executa o pássaros](images/flocking01.png)](images/flocking01.png#lightbox)

Isso inclui GameplayKit, ReplayKit, modelo de e/s, MetalKit e sombreadores de desempenho de metal juntamente com novos recursos aprimorados de metal, SceneKit e SpriteKit.

Este artigo apresentará todas as maneiras de melhorar seu jogo Xamarin. iOS com novos aprimoramentos de jogos do iOS 9:

## <a name="introducing-gameplaykit"></a>Apresentando o GameplayKit

A nova estrutura GameplayKit da Apple fornece um conjunto de tecnologias que facilita a criação de jogos para dispositivos iOS, reduzindo a quantidade de código repetitivo e comum necessário para a implementação. O GameplayKit fornece ferramentas para desenvolver a mecânica de jogos que pode ser facilmente combinada com um mecanismo gráfico (como SceneKit ou SpriteKit) para entregar rapidamente um jogo concluído.

O GameplayKit inclui vários algoritmos comuns de jogos, como:

- Um comportamento baseado na simulação do agente que permite que você defina movimentos e metas que o ia buscará automaticamente.
- Uma inteligência artificial por minMax para jogos com base em mudança.
- Um sistema de regras para lógica de jogos controlada por dados com motivo difuso para fornecer o comportamento de emergente.

Além disso, o GameplayKit usa uma abordagem de bloco de construção para o desenvolvimento de jogos usando uma arquitetura modular que fornece os seguintes recursos:

- Computador de estado para lidar com sistemas complexos de código de procedimentos, em jogo play.
- Ferramentas para fornecer reprodução aleatória de jogos e não predição sem causar problemas de depuração.
- Uma arquitetura com base em componente reutilizável.

Para saber mais sobre o GameplayKit, consulte o [Guia de programação do GameplayKit](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/GameplayKit_Guide/index.html#//apple_ref/doc/uid/TP40015172) da Apple e a referência da estrutura do [GameplayKit](https://developer.apple.com/library/prerelease/ios/documentation/GameplayKit/Reference/GameplayKit_Framework/index.html#//apple_ref/doc/uid/TP40015199).

## <a name="gameplaykit-examples"></a>Exemplos de GameplayKit

Vamos dar uma olhada rápida na implementação de uma mecânica de jogo simples em um aplicativo Xamarin. iOS usando o Game Play Kit.

### <a name="pathfinding"></a>Pathfinding

A pathfinding é a capacidade de um elemento de ia de um jogo encontrar seu caminho em todo o tabuleiro do jogo.
Por exemplo, um inimigo 2D encontra seu caminho por meio de um labirinto ou um caractere 3D por meio de um terreno mundo shooter de primeira pessoa.

Considere o seguinte mapa:

[![Um mapa pathfinding de exemplo](images/gkpathfindpath.png)](images/gkpathfindpath.png#lightbox)

Usando o pathfinding, esse código C# pode encontrar uma maneira por meio do mapa:

```csharp
var a = GKGraphNode2D.FromPoint (new Vector2 (0, 5));
var b = GKGraphNode2D.FromPoint (new Vector2 (3, 0));
var c = GKGraphNode2D.FromPoint (new Vector2 (2, 6));
var d = GKGraphNode2D.FromPoint (new Vector2 (4, 6));
var e = GKGraphNode2D.FromPoint (new Vector2 (6, 5));
var f = GKGraphNode2D.FromPoint (new Vector2 (6, 0));

a.AddConnections (new [] { b, c }, false);
b.AddConnections (new [] { e, f }, false);
c.AddConnections (new [] { d }, false);
d.AddConnections (new [] { e, f }, false);

var graph = GKGraph.FromNodes(new [] { a, b, c, d, e, f });

var a2e = graph.FindPath (a, e); // [ a, c, d, e ]
var a2f = graph.FindPath (a, f); // [ a, b, f ]

Console.WriteLine(String.Join ("->", (object[]) a2e));
Console.WriteLine(String.Join ("->", (object[]) a2f));
```

### <a name="classical-expert-system"></a>Sistema especialista clássico

O trecho de código C# a seguir mostra como o GameplayKit pode ser usado para implementar um sistema especialista clássico:

```csharp
string output = "";
bool reset = false;
int input = 15;

public override void ViewDidLoad ()
{
    base.ViewDidLoad ();

    /*
    If reset is true, clear the output and set reset to false
    */
    var clearRule = GKRule.FromPredicate ((rules) => reset, rules => {
        output = "";
        reset = false;
    });
    clearRule.Salience = 1;

    var fizzRule = GKRule.FromPredicate (mod (3), rules => {
        output += "fizz";
    });
    fizzRule.Salience = 2;

    var buzzRule = GKRule.FromPredicate (mod (5), rules => {
        output += "buzz";
    });
    buzzRule.Salience = 2;

    /*
    This *always* evaluates to true, but is higher Salience, so evaluates after lower-salience items
    (which is counter-intuitive). Print the output, and reset (thus triggering "ResetRule" next time)
    */
    var outputRule = GKRule.FromPredicate (rules => true, rules => {
        System.Console.WriteLine(output == "" ? input.ToString() : output);
        reset = true;
    });
    outputRule.Salience = 3;

    var rs = new GKRuleSystem ();
    rs.AddRules (new [] {
        clearRule,
        fizzRule,
        buzzRule,
        outputRule
    });

    for (input = 1; input < 16; input++) {
        rs.Evaluate ();
        rs.Reset ();
    }
}

protected Func<GKRuleSystem, bool> mod(int m)
{
    Func<GKRuleSystem,bool> partiallyApplied = (rs) => input % m == 0;
    return partiallyApplied;
}
```

Com base em um determinado conjunto de regras ( `GKRule` ) e um conjunto conhecido de entradas, o sistema especialista ( `GKRuleSystem` ) criará uma saída previsível ( `fizzbuzz` para o nosso exemplo acima).

### <a name="flocking"></a>Pássaros

O pássaros permite que um grupo de entidades de jogo controladas por ia se comporte como um Flock, em que o grupo responde aos movimentos e ações de uma entidade líder, como uma Flock de pássaros em voo ou uma escola de peixe nadare.

O trecho de código C# a seguir implementa o comportamento pássaros usando GameplayKit e SpriteKit para a exibição de gráficos:

```csharp
using System;
using SpriteKit;
using CoreGraphics;
using UIKit;
using GameplayKit;
using Foundation;
using System.Collections.Generic;
using System.Linq;
using OpenTK;

namespace FieldBehaviorExplorer
{
    public static class FlockRandom
    {
        private static GKARC4RandomSource rand = new GKARC4RandomSource ();

        static FlockRandom ()
        {
            rand.DropValues (769);
        }

        public static float NextUniform ()
        {
            return rand.GetNextUniform ();
        }
    }

    public class FlockingScene : SKScene
    {
        List<Boid> boids = new List<Boid> ();
        GKComponentSystem componentSystem;
        GKAgent2D trackingAgent; //Tracks finger on screen
        double lastUpdateTime = Double.NaN;
        //Hold on to behavior so it doesn't get GC'ed
        static GKBehavior flockingBehavior;
        static GKGoal seekGoal;

        public FlockingScene (CGSize size) : base (size)
        {
            AddRandomBoids (20);

            var scale = 0.4f;
            //Flocking system
            componentSystem = new GKComponentSystem (typeof(GKAgent2D));
            var behavior = DefineFlockingBehavior (boids.Select (boid => boid.Agent).ToArray<GKAgent2D>(), scale);
            boids.ForEach (boid => {
                boid.Agent.Behavior = behavior;
                componentSystem.AddComponent(boid.Agent);
            });

            trackingAgent = new GKAgent2D ();
            trackingAgent.Position = new Vector2 ((float) size.Width / 2.0f, (float) size.Height / 2.0f);
            seekGoal = GKGoal.GetGoalToSeekAgent (trackingAgent);
        }

        public override void TouchesBegan (NSSet touches, UIEvent evt)
        {
            boids.ForEach(boid => boid.Agent.Behavior.SetWeight(1.0f, seekGoal));
        }

        public override void TouchesEnded (NSSet touches, UIEvent evt)
        {
            boids.ForEach (boid => boid.Agent.Behavior.SetWeight (0.0f, seekGoal));
        }

        public override void TouchesMoved (NSSet touches, UIEvent evt)
        {
            var touch = (UITouch) touches.First();
            var loc = touch.LocationInNode (this);
            trackingAgent.Position = new Vector2((float) loc.X, (float) loc.Y);
        }

        private void AddRandomBoids (int count)
        {
            var scale = 0.4f;
            for (var i = 0; i < count; i++) {
                var b = new Boid (UIColor.Red, this.Size, scale);
                boids.Add (b);
                this.AddChild (b);
            }
        }

        internal static GKBehavior DefineFlockingBehavior(GKAgent2D[] boidBrains, float scale)
        {
            if (flockingBehavior == null) {
                var flockingGoals = new GKGoal[3];
                flockingGoals [0] = GKGoal.GetGoalToSeparate (boidBrains, 100.0f * scale, (float)Math.PI * 8.0f);
                flockingGoals [1] = GKGoal.GetGoalToAlign (boidBrains, 40.0f * scale, (float)Math.PI * 8.0f);
                flockingGoals [2] = GKGoal.GetGoalToCohere (boidBrains, 40.0f * scale, (float)Math.PI * 8.0f);

                flockingBehavior = new GKBehavior ();
                flockingBehavior.SetWeight (25.0f, flockingGoals [0]);
                flockingBehavior.SetWeight (10.0f, flockingGoals [1]);
                flockingBehavior.SetWeight (10.0f, flockingGoals [2]);
            }
            return flockingBehavior;
        }

        public override void Update (double currentTime)
        {
            base.Update (currentTime);
            if (Double.IsNaN(lastUpdateTime)) {
                lastUpdateTime = currentTime;
            }
            var delta = currentTime - lastUpdateTime;
            componentSystem.Update (delta);
        }
    }

    public class Boid : SKNode, IGKAgentDelegate
    {
        public GKAgent2D Agent { get { return brains; } }
        public SKShapeNode Sprite { get { return sprite; } }

        class BoidSprite : SKShapeNode
        {
            public BoidSprite (UIColor color, float scale)
            {
                var rot = CGAffineTransform.MakeRotation((float) (Math.PI / 2.0f));
                var path = new CGPath ();
                path.MoveToPoint (rot, new CGPoint (10.0, 0.0));
                path.AddLineToPoint (rot, new CGPoint (0.0, 30.0));
                path.AddLineToPoint (rot, new CGPoint (10.0, 20.0));
                path.AddLineToPoint (rot, new CGPoint (20.0, 30.0));
                path.AddLineToPoint (rot, new CGPoint (10.0, 0.0));
                path.CloseSubpath ();

                this.SetScale (scale);
                this.Path = path;
                this.FillColor = color;
                this.StrokeColor = UIColor.White;

            }
        }

        private GKAgent2D brains;
        private BoidSprite sprite;
        private static int boidId = 0;

        public Boid (UIColor color, CGSize size, float scale)
        {
            brains = BoidBrains (size, scale);
            sprite = new BoidSprite (color, scale);
            sprite.Position = new CGPoint(brains.Position.X, brains.Position.Y);
            sprite.ZRotation = brains.Rotation;
            sprite.Name = boidId++.ToString ();

            brains.Delegate = this;

            this.AddChild (sprite);
        }

        private GKAgent2D BoidBrains(CGSize size, float scale)
        {
            var brains = new GKAgent2D ();
            var x = (float) (FlockRandom.NextUniform () * size.Width);
            var y = (float) (FlockRandom.NextUniform () * size.Height);
            brains.Position = new Vector2 (x, y);

            brains.Rotation = (float)(FlockRandom.NextUniform () * Math.PI * 2.0);
            brains.Radius = 30.0f * scale;
            brains.MaxSpeed = 0.5f;
            return brains;
        }

        [Export ("agentDidUpdate:")]
        public void AgentDidUpdate (GameplayKit.GKAgent agent)
        {
        }

        [Export ("agentWillUpdate:")]
        public void AgentWillUpdate (GameplayKit.GKAgent agent)
        {
            var brainsIn = (GKAgent2D) agent;
            sprite.Position = new CGPoint(brainsIn.Position.X, brainsIn.Position.Y);
            sprite.ZRotation = brainsIn.Rotation;
            Console.WriteLine ($"{sprite.Name} -> [{sprite.Position}], {sprite.ZRotation}");
        }
    }
}
```

Em seguida, implemente essa cena em um controlador de exibição:

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();
        // Perform any additional setup after loading the view, typically from a nib.
        this.View = new SKView {
        ShowsFPS = true,
        ShowsNodeCount = true,
        ShowsDrawCount = true
    };
}

public override void ViewWillLayoutSubviews ()
{
    base.ViewWillLayoutSubviews ();

    var v = (SKView)View;
    if (v.Scene == null) {
        var scene = new FlockingScene (View.Bounds.Size);
        scene.ScaleMode = SKSceneScaleMode.AspectFill;
        v.PresentScene (scene);
    }
}
```

Quando executado, a pequena _"Boids"_ animada flockrá entre os toques dos dedos:

[![O pequeno Boids animado será flockdo em volta dos toques do dedo](images/flocking01.png)](images/flocking01.png#lightbox)

### <a name="other-apple-examples"></a>Outros exemplos da Apple

Além dos exemplos apresentados acima, a Apple forneceu os seguintes aplicativos de exemplo que podem ser transcodificados para C# e Xamarin. iOS:

- [FourInARow: usando o GameplayKit por minMax estrategista para o adversário AI](https://developer.apple.com/library/prerelease/ios/samplecode/FourInARow/Introduction/Intro.html#//apple_ref/doc/uid/TP40016142)
- [AgentsCatalog: usando o sistema de agentes no GameplayKit](https://developer.apple.com/library/prerelease/ios/samplecode/AgentsCatalog/Introduction/Intro.html#//apple_ref/doc/uid/TP40016141)
- [DemoBots: Criando um jogo de plataforma cruzada com SpriteKit e GameplayKit](https://developer.apple.com/library/prerelease/ios/samplecode/DemoBots/Introduction/Intro.html#//apple_ref/doc/uid/TP40015179)

## <a name="metal"></a>Metal

No iOS 9, a Apple fez várias alterações e adições ao metal para fornecer acesso de baixa sobrecarga à GPU. Usando o metal, você pode maximizar os gráficos e computar o potencial dos seus aplicativos iOS.

A estrutura de metal inclui os seguintes novos recursos:

- Novas texturas de estêncil particular e de profundidade para OS X.
- Qualidade de sombra aprimorada com profundidade fixação MSS e valores de estêncil frontal e traseiro separados.
- Melhorias na linguagem de sombreamento de metal e na biblioteca padrão de metal.
- Os sombreadores computacionais dão suporte a uma variedade maior de formatos de pixel.

### <a name="the-metalkit-framework"></a>A estrutura MetalKit

A estrutura MetalKit fornece um conjunto de classes e recursos de utilitário que reduzem a quantidade de trabalho necessária para usar o metal em um aplicativo iOS. O MetalKit fornece suporte em três áreas principais:

1. O carregamento assíncrono de textura de uma variedade de fontes, incluindo formatos comuns, como PNG, JPEG, KTX e PVR.
2. Acesso fácil aos ativos baseados em e/s de modelo para manipulação de modelo específico de metal. Esses recursos foram altamente otimizados para fornecer transferência de dados eficiente entre malhas de e/s de modelo e buffers metálicos.
3. Exibições de metal predefinidas e gerenciamento de exibição que reduzem consideravelmente a quantidade de código necessária para exibir renderizações gráficas em um aplicativo iOS.

Para saber mais sobre o MetalKit, consulte referência da [estrutura do MetalKit](https://developer.apple.com/library/prerelease/ios/documentation/MetalKit/Reference/MTKFrameworkReference/index.html#//apple_ref/doc/uid/TP40015356)da Apple, [Guia de programação de metal](https://developer.apple.com/library/prerelease/ios/documentation/Miscellaneous/Conceptual/MetalProgrammingGuide/Introduction/Introduction.html#//apple_ref/doc/uid/TP40014221), referência da [estrutura metálica](https://developer.apple.com/library/prerelease/ios/documentation/Metal/Reference/MetalFrameworkReference/index.html#//apple_ref/doc/uid/TP40014161) e [Guia de idiomas de sombreamento metálico](https://developer.apple.com/library/prerelease/ios/documentation/Metal/Reference/MetalShadingLanguageGuide/Introduction/Introduction.html#//apple_ref/doc/uid/TP40014364).

### <a name="metal-performance-shaders-framework"></a>Estrutura de sombreadores de desempenho de metal

A estrutura do sombreador de desempenho de metal fornece um conjunto altamente otimizado de gráficos e sombreadores com base em computação para uso em seus aplicativos iOS baseados em metal. Cada sombreador na estrutura do sombreador de desempenho de metal foi especificamente ajustado para fornecer alto desempenho em GPUs iOS com suporte de metal.

Usando classes de sombreador de desempenho de metal, você pode obter o maior desempenho possível em cada GPU do iOS específica sem ter que direcionar e manter as bases de código individuais. Os sombreadores de desempenho de metal podem ser usados com qualquer recurso de metal, como texturas e buffers.

A estrutura do sombreador de desempenho de metal fornece um conjunto de sombreadores comuns, como:

- **Desfoque Gaussiano** ( `MPSImageGaussianBlur` )
- **Detecção de borda do Sobel** ( `MPSImageSobel` )
- **Histograma de imagem** ( `MPSImageHistogram` )

Para obter mais informações, consulte o [Guia de idiomas de sombreamento metálico](https://developer.apple.com/library/prerelease/ios/documentation/Metal/Reference/MetalShadingLanguageGuide/Introduction/Introduction.html#//apple_ref/doc/uid/TP40014364)da Apple.

## <a name="introducing-model-io"></a>Introdução à e/s de modelo

A estrutura de e/s de modelo da Apple fornece uma compreensão profunda dos ativos 3D (como modelos e seus recursos relacionados). A e/s de modelo fornece aos jogos do iOS com materiais, modelos e iluminação baseados em físico que podem ser usados com GameplayKit, metal e SceneKit.

Com a e/s de modelo, você pode dar suporte aos seguintes tipos de tarefas:

- Importe a iluminação, os materiais, os dados de malha, as configurações de câmera e outras informações baseadas em cena de uma variedade de formatos populares de mecanismos de software e jogos.
- Processar ou gerar informações baseadas em cena, como criar domes de céu texturizado por procedimento ou estortar iluminação em uma malha.
- Funciona com MetalKit, SceneKit e GLKit para carregar ativos de jogos com eficiência em buffers de GPU para renderização.
- Exporte informações baseadas em cena para uma variedade de formatos populares de software e de mecanismo de jogos.

Para saber mais sobre e/s de modelo, consulte referência de [estrutura de e/s de modelo](https://developer.apple.com/library/prerelease/ios/documentation/ModelIO/Reference/ModelIO_Framework/index.html#//apple_ref/doc/uid/TP40015421) da Apple

## <a name="introducing-replaykit"></a>Apresentando o ReplayKit

A nova estrutura ReplayKit da Apple permite que você adicione facilmente a gravação do jogo play ao seu jogo iOS e permita que o usuário edite e compartilhe com rapidez e facilidade esse vídeo de dentro do aplicativo.

Para obter mais informações, consulte a introdução da Apple [com ReplayKit e Game Center vídeo](https://developer.apple.com/videos/wwdc/2015/?id=605) e seu [DemoBots: Criando um jogo de plataforma cruzada com](https://developer.apple.com/library/prerelease/ios/samplecode/DemoBots/Introduction/Intro.html#//apple_ref/doc/uid/TP40015179) o aplicativo de exemplo SpriteKit e GameplayKit.

## <a name="scenekit"></a>SceneKit

O Scene kit é uma API de grafo de cena 3D que simplifica o trabalho com gráficos 3D. Ele foi introduzido pela primeira vez no OS X 10,8 e chegou ao iOS 8. Com o kit de cena, a criação de visualizações 3D de imersão e jogos 3D casuais não requer experiência em OpenGL. Aproveitando os conceitos comuns de grafo de cena, o kit de cena abstrai as complexidades do OpenGL e do OpenGL ES, facilitando muito a adição de conteúdo 3D a um aplicativo. No entanto, se você for um especialista em OpenGL, o kit de cena também terá excelente suporte para ligar diretamente com OpenGL. Ele também inclui vários recursos que complementam gráficos 3D, como a física, e se integram muito bem com várias outras estruturas da Apple, como animação principal, imagem básica e kit de Sprite.

Para obter mais informações, consulte nossa documentação do [SceneKit](~/ios/platform/gaming/scenekit.md) .

### <a name="scenekit-changes"></a>SceneKit alterações

A Apple adicionou os seguintes novos recursos ao SceneKit para iOS 9:

- Agora, o Xcode fornece um editor de cena que permite criar rapidamente jogos e aplicativos 3D interativos editando cenas diretamente de dentro do Xcode.
- As `SCNView` `SCNSceneRenderer` classes e podem ser usadas para habilitar a renderização de metal (em dispositivos IOS com suporte).
- As `SCNAudioPlayer` `SCNNode` classes e podem ser usadas para adicionar efeitos de áudio espaciais que rastreiam automaticamente uma posição de Player para um aplicativo Ios.

Para obter mais informações, consulte nossa [documentação do SceneKit](~/ios/platform/introduction-to-ios8.md#scenekit) e a [referência da estrutura SceneKit](https://developer.apple.com/library/prerelease/ios/documentation/SceneKit/Reference/SceneKit_Framework/index.html#//apple_ref/doc/uid/TP40012283) da Apple e [Fox: Criando um jogo de SceneKit com o projeto de exemplo do Xcode Scene editor](https://developer.apple.com/library/prerelease/ios/samplecode/Fox/Introduction/Intro.html#//apple_ref/doc/uid/TP40016154) .

## <a name="spritekit"></a>SpriteKit

O sprite Kit, a estrutura de jogos 2D da Apple, tem alguns novos recursos interessantes no iOS 8 e no OS X Yosemite. Isso inclui a integração com o kit de cena, suporte a sombreador, iluminação, sombras, restrições, geração de mapa normal e aprimoramentos de física. Em particular, os novos recursos de física tornam muito fácil adicionar efeitos realistas a um jogo.

Para obter mais informações, consulte nossa documentação do [SpriteKit](~/ios/platform/gaming/spritekit.md) .

### <a name="spritekit-changes"></a>SpriteKit alterações

A Apple adicionou os seguintes novos recursos ao SpriteKit para iOS 9:

- Efeito de áudio espacial que rastreia automaticamente a posição do jogador com a `SKAudioNode` classe.
- Agora, o Xcode apresenta um editor de cena e um editor de ação para facilitar a criação de jogos e aplicativos 2D.
- Suporte de jogo de rolagem fácil com novos objetos de nós de câmera ( `SKCameraNode` ).
- Em dispositivos iOS que dão suporte a metal, o SpriteKit o usará automaticamente para renderização, mesmo que você já esteja usando sombreadores do OpenGL ES personalizados.

Para obter mais informações, consulte a [referência da estrutura SpriteKit](https://developer.apple.com/library/prerelease/ios/documentation/SpriteKit/Reference/SpriteKitFramework_Ref/index.html#//apple_ref/doc/uid/TP40013041) da Apple na [documentação da SpriteKit](~/ios/platform/introduction-to-ios8.md#spritekit) e sua [DemoBots: Criando um jogo de plataforma cruzada com](https://developer.apple.com/library/prerelease/ios/samplecode/DemoBots/Introduction/Intro.html#//apple_ref/doc/uid/TP40015179) o aplicativo de exemplo SpriteKit e GameplayKit.

## <a name="summary"></a>Resumo

Este artigo abordou os novos recursos de jogos que o iOS 9 fornece para seus aplicativos do Xamarin. iOS.
Ele introduziu GameplayKit e e/s de modelo; os principais aprimoramentos para o metal; e os novos recursos de SceneKit e SpriteKit.

## <a name="related-links"></a>Links Relacionados

- [Amostras do iOS 9](/samples/browse/?products=xamarin&term=Xamarin.iOS%2biOS9)
- [iOS 9 para desenvolvedores](https://developer.apple.com/ios/pre-release/)
- [iOS 9,0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)