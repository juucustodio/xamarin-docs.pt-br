---
title: Introdução ao UrhoSharp
description: Este documento descreve a estrutura básica de um aplicativo UrhoSharp e links para vários guias e aplicativos de exemplo que demonstram como usar o UrhoSharp.
ms.prod: xamarin
ms.assetid: 18041443-5093-4AF7-8B20-03E00478EF35
author: conceptdev
ms.author: crdun
ms.date: 03/29/2017
ms.openlocfilehash: 441a3cc19b4246fb2bdea54508142a894af5c051
ms.sourcegitcommit: 9bfedf07940dad7270db86767eb2cc4007f2a59f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/21/2019
ms.locfileid: "67832543"
---
# <a name="introduction-to-urhosharp"></a>Introdução ao UrhoSharp

![Logotipo do UrhoSharp](introduction-images/urhosharp-icon.png)

O UrhoSharp é um poderoso mecanismo de jogo 3D para desenvolvedores do Xamarin e do .NET.  Ele é semelhante a SceneKit e SpriteKit da Apple e inclui física, navegação, rede e muito mais enquanto ainda está em uma plataforma cruzada.

É uma ligação do .NET com o mecanismo [Urho3D](http://urho3d.github.io/) e permite aos desenvolvedores escrever código de plataforma cruzada que pode ser direcionado para Android, Ios, Windows e Mac com a mesma base de código e pode ser processado para sistemas OpenGL e Direct3D.

UrhoSharp é um mecanismo de jogo com muita funcionalidade pronta para uso:

- Renderização de gráfico 3D eficiente
- Simulação de física (usando a biblioteca de marcadores)
- Manipulação de cena
- Suporte a Await/Async
- API de ações amigáveis
- integração 2D em cenas 3D
- Renderização de fonte com o FreeType
- Recursos de rede de cliente e servidor
- Importar uma ampla gama de ativos (com a biblioteca de ativos abertos)
- Malha de navegação e pathfinding (usando recast/desvio)
- Geração de envoltória convexa para detecção de colisão (usando StanHull)
- Reprodução de áudio (com **libvorbis**)

## <a name="get-started"></a>Introdução

O UrhoSharp é convenientemente distribuído como um [pacote NuGet](https://www.nuget.org/) e pode ser adicionado aos seus C# projetos F# do ou que se destinam ao Windows, Mac, Android ou Ios.  O NuGet vem com as duas bibliotecas necessárias para executar o programa, bem como os ativos básicos (CoreData) usados pelo mecanismo.

### <a name="urho-as-a-portable-class-library"></a>Urho como uma biblioteca de classes portátil

O pacote Urho pode ser consumido de um projeto específico da plataforma ou de um projeto de biblioteca de classes portátil, permitindo que você reutilize todo o seu código em todas as plataformas.  Isso significa que tudo o que você teria de fazer em cada plataforma é escrever o ponto de entrada específico da plataforma e, em seguida, transferir o controle para o seu código de jogo compartilhado.

### <a name="samples"></a>Exemplos

Você pode ter um gosto dos recursos do Urho abrindo em Visual Studio para Mac ou no Visual Studio a solução de exemplo de:

[https://github.com/xamarin/urho-samples](https://github.com/xamarin/urho-samples)

A solução padrão contém projetos para Android, iOS, Windows e Mac.  Estruturamos essa solução para que tenhamos um iniciador de plataforma muito pequeno, e todo o código de exemplo e o código de jogo residem em uma biblioteca de classes portátil, ilustrando como maximizar a reutilização de código em todas as plataformas.

Consulte a página [Urho e sua plataforma](~/graphics-games/urhosharp/platform/index.md) para obter mais informações sobre como criar suas próprias soluções.

Como todos os exemplos compartilham um conjunto comum de elementos da interface do usuário, os exemplos abstrairam a configuração básica neste arquivo:

[https://github.com/xamarin/urho-samples/blob/master/FeatureSamples/Core/Sample.cs](https://github.com/xamarin/urho-samples/blob/master/FeatureSamples/Core/Sample.cs)

Isso fornece uma classe base de exemplo que lida com alguns pressionamentos de tecla e eventos de toque básicos, configura uma câmera, fornece elementos básicos de interface do usuário, e isso permite que cada exemplo se concentre na funcionalidade específica que está sendo demonstrada.

O exemplo a seguir mostra o que o mecanismo é capaz de fazer:

- Com o [jogo](https://github.com/xamarin/urho-samples/tree/master/SamplyGame) de um clone simples de ShootySkies.

Enquanto os outros exemplos mostram propriedades individuais de cada amostra.

## <a name="basic-structure"></a>Estrutura básica

Seu jogo deve criar uma subclasse da classe `Application`, é aqui que você configurará seu jogo (no método `Setup`) e iniciará o jogo (no método `Start`).  Em seguida, você constrói a interface do usuário principal.  Vamos examinar um pequeno exemplo que mostra as APIs para configurar uma cena 3D, alguns elementos da interface do usuário e anexar um comportamento simples a ele.

```csharp
class MySample : Application {
    protected override void Start ()
    {
        CreateScene ();
        Input.KeyDown += (args) => {
            if (args.Key == Key.Esc) Exit ();
        };
    }

    async void CreateScene()
    {
        // UI text
        var helloText = new Text()
        {
            Value = "Hello World from MySample",
            HorizontalAlignment = HorizontalAlignment.Center,
            VerticalAlignment = VerticalAlignment.Center
        };
        helloText.SetColor(new Color(0f, 1f, 1f));
        helloText.SetFont(
            font: ResourceCache.GetFont("Fonts/Font.ttf"),
            size: 30);
        UI.Root.AddChild(helloText);

        // Create a top-level scene, must add the Octree
        // to visualize any 3D content.
        var scene = new Scene();
        scene.CreateComponent<Octree>();
        // Box
        Node boxNode = scene.CreateChild();
        boxNode.Position = new Vector3(0, 0, 5);
        boxNode.Rotation = new Quaternion(60, 0, 30);
        boxNode.SetScale(0f);
        StaticModel modelObject = boxNode.CreateComponent<StaticModel>();
        modelObject.Model = ResourceCache.GetModel("Models/Box.mdl");
        // Light
        Node lightNode = scene.CreateChild(name: "light");
        lightNode.SetDirection(new Vector3(0.6f, -1.0f, 0.8f));
        lightNode.CreateComponent<Light>();
        // Camera
        Node cameraNode = scene.CreateChild(name: "camera");
        Camera camera = cameraNode.CreateComponent<Camera>();
        // Viewport
        Renderer.SetViewport(0, new Viewport(scene, camera, null));
        // Perform some actions
        await boxNode.RunActionsAsync(
            new EaseBounceOut(new ScaleTo(duration: 1f, scale: 1)));
        await boxNode.RunActionsAsync(
            new RepeatForever(new RotateBy(duration: 1,
                deltaAngleX: 90, deltaAngleY: 0, deltaAngleZ: 0)));
     }
}
```

Se você executar esse aplicativo, descobrirá rapidamente que o tempo de execução está reclamando sobre seus ativos.  O que você precisa fazer é criar uma hierarquia em seu projeto que comece com o nome de diretório especial "dados" e, dentro disso, colocaria os ativos que você referencia em seu programa.  Em seguida, você deve definir as propriedades do item para cada ativo o "copiar para o diretório de saída" como "copiar se for mais recente", que garantirá que seus dados estejam lá.

Deixe-nos explicar o que está acontecendo aqui.

Para iniciar seu aplicativo, você chama a função de inicialização do mecanismo, seguida pela criação de uma nova instância da classe do aplicativo, como esta:

```csharp
new MySample().Run();
```

O tempo de execução invocará os métodos `Setup` e `Start` para você.  Se você substituir `Setup` você poderá configurar os parâmetros do mecanismo (não mostrar neste exemplo).

Você deve substituir `Start`, pois isso iniciará o jogo.  Nesse método, você carregará seus ativos, conectará manipuladores de eventos, configurará sua cena e iniciará as ações que desejar.  Em nosso exemplo, nós criamos um pouco de interface de usuário para mostrar ao usuário, bem como configurar uma cena 3D.

O trecho de código a seguir usa a estrutura de interface do usuário para criar um elemento de texto e adicioná-lo ao seu aplicativo:

```csharp
// UI text
var helloText = new Text()
{
    Value = "Hello World from UrhoSharp",
    HorizontalAlignment = HorizontalAlignment.Center,
    VerticalAlignment = VerticalAlignment.Center
};
helloText.SetColor(new Color(0f, 1f, 1f));
helloText.SetFont(
    font: ResourceCache.GetFont("Fonts/Font.ttf"),
    size: 30);
UI.Root.AddChild(helloText);
```

A estrutura da interface do usuário está lá para fornecer uma interface do usuário no jogo muito simples e funciona adicionando novos nós ao nó `UI.Root`.

A segunda parte do nosso exemplo configura a cena principal.  Isso envolve uma série de etapas, criando uma cena 3D, criando uma caixa 3D na tela, adicionando uma luz, uma câmera e um visor.  Eles são explorados com mais detalhes na seção [cena, nós, componentes e câmeras](~/graphics-games/urhosharp/using.md#scenenodescomponentsandcameras).

A terceira parte de nosso exemplo dispara algumas ações.  As ações são as receitas que descrevem um efeito específico e, uma vez criadas, podem ser executadas por um nó sob demanda chamando o método `RunActionAsync` em um `Node`.

A primeira ação dimensiona a caixa com um efeito de saltamento e a segunda gira a caixa para sempre:

```csharp
await boxNode.RunActionsAsync(
    new EaseBounceOut(new ScaleTo(duration: 1f, scale: 1)));
```

O acima mostra como a primeira ação que criamos é uma ação `ScaleTo`, isso é meramente uma receita que indica que você deseja Dimensionar por um segundo em direção ao valor de uma propriedade Scale de um nó.  Essa ação, por sua vez, é encapsulada em torno de uma ação de atenuação, a ação `EaseBounceOut`.  As ações de atenuação distorcem a execução linear de uma ação e aplicam um efeito, neste caso, ele fornece o efeito de saída.
Então, nossa receita poderia ser escrita como:

```csharp
var recipe = new EaseBounceOut(new ScaleTo(duration: 1f, scale: 1));
```

Depois que a receita tiver sido criada, executaremos a receita:

```csharp
await boxNode.RunActionsAsync (recipe)
```

O Await indica que o desejará retomar a execução após essa linha quando a ação for concluída.  Quando a ação for concluída, dispararemos a segunda animação.

O documento [usando UrhoSharp](~/graphics-games/urhosharp/using.md) explora mais detalhadamente os conceitos por trás de Urho e como estruturar seu código para criar um jogo.

## <a name="copyrights"></a>Direitos autorais

Esta documentação contém conteúdo original da Xamarin Inc, mas se desenha extensivamente a partir da documentação de software livre para o projeto Urho3D e contém capturas de tela do projeto Cocos2D.
