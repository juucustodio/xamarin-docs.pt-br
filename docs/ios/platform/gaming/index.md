---
title: APIs de jogos no xamarin. IOS do iOS
description: Este artigo aborda os novos aprimoramentos de jogos fornecidos pelo iOS 9 que podem ser usados para melhorar seu jogo de xamarin. IOS gráficos e recursos de áudio.
ms.prod: xamarin
ms.assetid: 958D38FD-9240-482E-9A42-D6671ED8F2B0
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/20/2017
ms.openlocfilehash: d8a531e495a19be7437d4a600e758028594248ab
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50115998"
---
# <a name="ios-gaming-apis-in-xamarinios"></a>APIs de jogos no xamarin. IOS do iOS

_Este artigo aborda os novos aprimoramentos de jogos fornecidos pelo iOS 9 que podem ser usados para melhorar seu jogo de xamarin. IOS gráficos e recursos de áudio._

Apple fez várias melhorias para as APIs de jogos em iOS 9 tecnológicas que tornam mais fácil de implementar os gráficos de jogo e áudio em um aplicativo xamarin. IOS.
Isso inclui tanto a facilidade de desenvolvimento por meio de estruturas de alto nível e aproveitando o poder de GPU do dispositivo iOS para maior velocidade e capacidades de gráfico.

[![](images/flocking01.png "Um exemplo de um aplicativo em execução pássaros")](images/flocking01.png#lightbox)

Isso inclui GameplayKit, ReplayKit, e/s do modelo, MetalKit e sombreadores de desempenho do sistema operacional, juntamente com recursos novos e aprimorados de Metal, SceneKit e SpriteKit.

Este artigo apresenta todas as maneiras de melhorar seu jogo do xamarin. IOS com iOS 9 novos aprimoramentos de jogos:

## <a name="introducing-gameplaykit"></a>Introdução ao GameplayKit

Nova estrutura de GameplayKit da Apple fornece um conjunto de tecnologias que torna mais fácil criar jogos para dispositivos iOS, reduzindo a quantidade de código repetitivo, comuns necessária para implementação. GameplayKit fornece ferramentas para desenvolver, rapidamente, a mecânica de jogo que, em seguida, pode ser combinada facilmente com um mecanismo de gráficos (como SceneKit ou SpriteKit) para entregar um jogo concluído.

GameplayKit inclui vários, comum, de jogo algoritmos, como:

- Com base um comportamento, simulação de agente que permite que você defina movimentos e objetivos que o AI se dedicará automaticamente.
- Uma minmax a inteligência artificial para jogo baseado em turno.
- Um sistema de regra para a lógica do jogo controlada por dados com o raciocínio difusa para fornecer um comportamento emergente.

Além disso, GameplayKit adota uma abordagem de bloco de construção para o desenvolvimento de jogos usando uma arquitetura modular que fornece os seguintes recursos:

- Máquina de estado para lidar com um código complexo e procedimento com base em sistemas no jogo.
- Ferramentas para fornecer randomizado jogo e a falta de previsibilidade sem causar problemas de depuração.
- Arquitetura baseada em uma entidade reutilizável e dividida em componentes.

Para saber mais sobre GameplayKit, consulte da Apple [guia de programação Gameplaykit](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/GameplayKit_Guide/index.html#//apple_ref/doc/uid/TP40015172) e [referência de estrutura GameplayKit](https://developer.apple.com/library/prerelease/ios/documentation/GameplayKit/Reference/GameplayKit_Framework/index.html#//apple_ref/doc/uid/TP40015199).

## <a name="gameplaykit-examples"></a>Exemplos de GameplayKit

Vamos dar uma olhada rápida na implementação de alguns mecânica de jogo simples em um aplicativo xamarin. IOS usando o kit de jogo.

### <a name="pathfinding"></a>Pathfinding

Pathfinding é a capacidade de um elemento de AI de um jogo de encontrar seu caminho no tabuleiro de jogo.
Por exemplo, um inimigo 2D Localizando sua maneira por um Labirinto ou um caractere 3D por meio de um terreno mundo de tiro em pessoa primeiro.

Considere o mapa a seguir:

[![](images/gkpathfindpath.png "Um mapa de pathfinding de exemplo")](images/gkpathfindpath.png#lightbox)

Usando pathfinding este C# código pode encontrar uma maneira através do mapa:

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

### <a name="classical-expert-system"></a>Sistema de especialista clássico

O trecho a seguir do C# código mostra como GameplayKit pode ser usado para implementar um sistema especialista clássico:

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

Com base em um determinado conjunto de regras (`GKRule`) e um conjunto conhecido de entradas, o sistema especialista (`GKRuleSystem`) criará saída previsível (`fizzbuzz` para nosso exemplo acima).

### <a name="flocking"></a>Pássaros

Pássaros permite que um grupo de AI controlado entidades jogos se comportar como um usam, onde o grupo responde aos movimentos e ações de uma entidade de cliente potencial, como um usam de pássaros em trânsito ou uma escola de um peixe nadando.

O trecho a seguir do C# código implementa o comportamento de pássaros usando GameplayKit e SpriteKit para exibem os elementos gráficos:

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

Quando executado, pouco animado _"Boids"_ será flock em torno de toques nosso dedo:

[![](images/flocking01.png "O pouco animado Boids será flock em torno de toques de dedo")](images/flocking01.png#lightbox)

### <a name="other-apple-examples"></a>Outros exemplos da Apple

Além dos exemplos apresentados a seguir, a Apple forneceu os seguintes aplicativos de exemplo que podem ser transcodificados para C# e xamarin. IOS:

- [FourInARow: Usando a estrategista GameplayKit Minmax para AI adversário](https://developer.apple.com/library/prerelease/ios/samplecode/FourInARow/Introduction/Intro.html#//apple_ref/doc/uid/TP40016142)
- [AgentsCatalog: Usando o sistema de agentes no GameplayKit](https://developer.apple.com/library/prerelease/ios/samplecode/AgentsCatalog/Introduction/Intro.html#//apple_ref/doc/uid/TP40016141)
- [DemoBots: Criando um jogo de plataforma cruzada com SpriteKit e GameplayKit](https://developer.apple.com/library/prerelease/ios/samplecode/DemoBots/Introduction/Intro.html#//apple_ref/doc/uid/TP40015179)

## <a name="metal"></a>Metal

No iOS 9, o Apple fez várias alterações e adições metal para fornecer acesso de baixa sobrecarga para a GPU. Usando o sistema operacional você pode maximizar os elementos gráficos e computação potencial dos seus aplicativos iOS.

A estrutura de Metal inclui os seguintes recursos novos:

- Novo privado e profundidade de texturas em estêncil para OS X.
- Qualidade aprimorada de sombra com front de compressão e separado de profundidade e os valores de estêncil de back.
- Melhorias de sombreamento de linguagem e biblioteca de padrão de Metal metal.
- Sombreadores computacionais dão suporte a uma vasta gama de formatos de pixel.

### <a name="the-metalkit-framework"></a>A estrutura MetalKit

O framework de MetalKit fornece um conjunto de classes de utilitário e recursos que reduzem a quantidade de trabalho necessária para usar o sistema operacional em um aplicativo iOS. MetalKit fornece suporte em três áreas principais:

1. Carregamento de uma variedade de fontes incluindo formatos comuns, como, PNG, JPEG, KTX e PVR de textura assíncrona.
2. Acesso fácil a e/s do modelo com base em ativos para a manipulação de Metal modelo específico. Esses recursos foram altamente otimizados para fornecer transferência de dados eficiente entre malhas de e/s do modelo e buffers de Metal.
3. Exibições predefinidas de Metal e gerenciamento de exibição que reduzem significativamente a quantidade de código necessária para exibir as renderizações de gráfico dentro de um aplicativo iOS.

Para saber mais sobre MetalKit, consulte da Apple [referência de estrutura MetalKit](https://developer.apple.com/library/prerelease/ios/documentation/MetalKit/Reference/MTKFrameworkReference/index.html#//apple_ref/doc/uid/TP40015356), [guia de programação de Metal](https://developer.apple.com/library/prerelease/ios/documentation/Miscellaneous/Conceptual/MetalProgrammingGuide/Introduction/Introduction.html#//apple_ref/doc/uid/TP40014221), [referência de estrutura de Metal](https://developer.apple.com/library/prerelease/ios/documentation/Metal/Reference/MetalFrameworkReference/index.html#//apple_ref/doc/uid/TP40014161) e [Bare Metal Guia de linguagem de sombreamento](https://developer.apple.com/library/prerelease/ios/documentation/Metal/Reference/MetalShadingLanguageGuide/Introduction/Introduction.html#//apple_ref/doc/uid/TP40014364).

### <a name="metal-performance-shaders-framework"></a>Estrutura de sombreadores de desempenho de metal

A estrutura do sombreador de desempenho do sistema operacional fornece um conjunto altamente otimizado de gráficos e computacionais com base em sombreadores para uso em seu sistema operacional com base em aplicativos do iOS. Cada sombreador no sombreador Metal desempenho framework foi ajustado especificamente para oferecer alto desempenho no sistema operacional com suporte a iOS GPUs.

Usando classes de sombreador de desempenho do sistema operacional, você pode obter o melhor desempenho possível em cada GPU específicas do iOS sem a necessidade de destino e manter as bases de código individuais. Metal sombreadores de desempenho podem ser usados com qualquer recurso de Metal, como texturas e buffers.

A estrutura do sombreador de desempenho do sistema operacional fornece um conjunto de sombreadores comuns, como:

- **Desfoque Gaussiano** (`MPSImageGaussianBlur`)
- **Detecção de borda Sobel** (`MPSImageSobel`)
- **Histograma da imagem** (`MPSImageHistogram`)

Para obter mais informações, consulte da Apple [guia de linguagem de sombreamento de Metal](https://developer.apple.com/library/prerelease/ios/documentation/Metal/Reference/MetalShadingLanguageGuide/Introduction/Introduction.html#//apple_ref/doc/uid/TP40014364).

## <a name="introducing-model-io"></a>Apresentando a e/s do modelo

Estrutura de e/s do modelo da Apple fornece uma compreensão profunda dos ativos 3D (como modelos e seus recursos relacionados). E/s de modelo fornece seus jogos de iOS com materiais com base em física, modelos e que pode ser usada com GameplayKit, Metal e SceneKit iluminação.

Com e/s de modelo, você pode dar suporte os seguintes tipos de tarefas:

- Importar de iluminação, materiais, dados, configurações de câmera e outras informações baseadas na cena de uma variedade de formatos de mecanismo de jogo e softwares populares de malha.
- Processar ou gerar informações com base em cena, tais como criar de maneira procedimental texturizado céu domes ou tortas iluminação em uma malha.
- Funciona com MetalKit, SceneKit e GLKit para carregar com eficiência ativos de jogos em buffers GPU para renderização.
- Exporte informações baseadas em cena a uma variedade de formatos de mecanismo de jogo e softwares populares.

Para saber mais sobre e/s do modelo, consulte da Apple [referência de estrutura do modelo de e/s](https://developer.apple.com/library/prerelease/ios/documentation/ModelIO/Reference/ModelIO_Framework/index.html#//apple_ref/doc/uid/TP40015421)

## <a name="introducing-replaykit"></a>Introdução ao ReplayKit

Nova estrutura de ReplayKit da Apple permite que você facilmente adicionar gravação de jogo para seu jogo para iOS e permitir que o usuário de forma rápida e fácil editar e compartilhar este vídeo de dentro do aplicativo.

Para obter mais informações, consulte da Apple [vai Social com vídeo ReplayKit e Game Center](https://developer.apple.com/videos/wwdc/2015/?id=605) e suas [DemoBots: Criando um jogo de plataforma cruzada com SpriteKit e GameplayKit](https://developer.apple.com/library/prerelease/ios/samplecode/DemoBots/Introduction/Intro.html#//apple_ref/doc/uid/TP40015179) aplicativo de exemplo.

## <a name="scenekit"></a>SceneKit

Kit de cena é uma API que simplifica o trabalho com gráficos 3D de grafo de cena 3D. Ele foi introduzido pela primeira vez nos X 10.8 e agora chegou ao iOS 8. Com o Kit de cena Criando visualizações 3D imersivas e casuais jogos 3D não requer experiência em OpenGL. Criando nos conceitos comuns de grafo de cena, Scene Kit abstrai as complexidades de OpenGL e OpenGL ES, tornando muito mais fácil adicionar 3D conteúdo a um aplicativo. No entanto, se você for um especialista do OpenGL, Scene Kit tem excelente suporte para juntar na diretamente com o OpenGL também. Ele também inclui vários recursos que complementam os gráficos 3D, como física e se integra muito bem com várias outras estruturas de Apple, como animação principal, a imagem principal e Sprite Kit.

Para obter mais informações, consulte nosso [SceneKit](~/ios/platform/gaming/scenekit.md) documentação.

### <a name="scenekit-changes"></a>Alterações do SceneKit

Apple adicionou os seguintes recursos novos para SceneKit para iOS 9:

- Xcode agora fornece um Editor de cena que permite que você crie rapidamente aplicativos 3D interativos e jogos editando cenas diretamente de dentro do Xcode.
- O `SCNView` e `SCNSceneRenderer` classes podem ser usadas para habilitar a renderização de Metal (em dispositivos iOS com suporte).
- O `SCNAudioPlayer` e `SCNNode` classes podem ser usadas para adicionar efeitos de áudio espaciais que controlam automaticamente uma posição do jogador em um aplicativo iOS.

Para obter mais informações, consulte nosso [documentação do SceneKit](~/ios/platform/introduction-to-ios8.md#scenekit) da Apple [referência de estrutura de SceneKit](https://developer.apple.com/library/prerelease/ios/documentation/SceneKit/Reference/SceneKit_Framework/index.html#//apple_ref/doc/uid/TP40012283) e [Fox: Criando um jogo do SceneKit com o Xcode cena Editor](https://developer.apple.com/library/prerelease/ios/samplecode/Fox/Introduction/Intro.html#//apple_ref/doc/uid/TP40016154)projeto de exemplo.

## <a name="spritekit"></a>SpriteKit

Sprite Kit, a estrutura de jogos 2D da Apple, tem alguns novos recursos interessantes no iOS 8 e nos X Yosemite. Isso inclui a integração com Scene Kit, suporte de sombreador, iluminação, sombras, restrições, geração de mapa normal e aprimoramentos de física. Em particular, os novos recursos de física tornam muito fácil adicionar efeitos realistas para um jogo.

Para obter mais informações, consulte nosso [SpriteKit](~/ios/platform/gaming/spritekit.md) documentação.

### <a name="spritekit-changes"></a>Alterações de SpriteKit

Apple adicionou os seguintes recursos novos para SpriteKit para iOS 9:

- Efeito de áudio espacial que controlar automaticamente a posição do jogador com o `SKAudioNode` classe.
- Xcode agora apresenta um Editor de cena e o Editor de ação para a criação fácil de aplicativo e o jogo 2D.
- Rolagem fácil suporte jogo com novos nós de câmera (`SKCameraNode`) objetos.
- Em dispositivos iOS que dão suporte ao sistema operacional, SpriteKit automaticamente usará para renderização, mesmo se você já estava usando sombreadores personalizados do OpenGL ES.

Para obter mais informações, consulte nosso [SpriteKit documentação](~/ios/platform/introduction-to-ios8.md#spritekit) da Apple [referência de estrutura de SpriteKit](https://developer.apple.com/library/prerelease/ios/documentation/SpriteKit/Reference/SpriteKitFramework_Ref/index.html#//apple_ref/doc/uid/TP40013041) e seus [DemoBots: Criando um jogo de plataforma cruzada com SpriteKit e GameplayKit](https://developer.apple.com/library/prerelease/ios/samplecode/DemoBots/Introduction/Intro.html#//apple_ref/doc/uid/TP40015179) aplicativo de exemplo.

## <a name="summary"></a>Resumo

Este artigo abordou os novos recursos de jogos se iOS 9 fornece para seus aplicativos xamarin. IOS.
Ele introduziu GameplayKit e e/s de modelo; os principais aperfeiçoamentos metal; e os novos recursos do SceneKit e SpriteKit.



## <a name="related-links"></a>Links relacionados

- [Amostras do iOS 9](https://developer.xamarin.com/samples/ios/iOS9/)
- [iOS 9 para desenvolvedores](https://developer.apple.com/ios/pre-release/)
- [iOS 9.0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
