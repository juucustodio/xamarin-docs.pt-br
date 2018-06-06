---
title: Uma introdução ao UrhoSharp
description: Este documento descreve a estrutura básica de um aplicativo UrhoSharp e links para várias guias e aplicativos de exemplo que demonstram como usar UrhoSharp.
ms.prod: xamarin
ms.assetid: 18041443-5093-4AF7-8B20-03E00478EF35
author: charlespetzold
ms.author: chape
ms.date: 03/29/2017
ms.openlocfilehash: d39faabc0851e3e89b03ad58a7f1ead3894efc15
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/05/2018
ms.locfileid: "34783549"
---
# <a name="an-introduction-to-urhosharp"></a>Uma introdução ao UrhoSharp

![Logotipo de UrhoSharp](introduction-images/urhosharp-icon.png)

UrhoSharp é um poderoso mecanismo de jogo 3D para desenvolvedores do Xamarin e do .NET.  Ele é semelhante em espírito ao SceneKit e SpriteKit da Apple e incluir física, navegação, rede e muito mais enquanto ainda está sendo várias plataformas.

É uma associação de .NET para o [Urho3D](http://urho3d.github.io/) mecanismo e permite que os desenvolvedores a escrever código de plataforma cruzada que pode direcionar o Android, iOS, Windows e Mac com a mesma base de código e podem renderizar a sistemas OpenGL e Direct3D.

UrhoSharp é um mecanismo de jogo com muitas funções sem a necessidade de:

- Processamento de gráficos 3D Avançado
- [Física simulação](https://developer.xamarin.com/api/namespace/Urho.Physics/) (usando a biblioteca de marcador)
- [Tratamento de cena](https://developer.xamarin.com/api/type/Urho.Scene/)
- Suporte de Async/await
- [API de ações amigável](https://developer.xamarin.com/api/namespace/Urho.Actions/)
- [Integração 2D em segundo plano 3D](https://developer.xamarin.com/api/namespace/Urho.Urho2D/)
- [Renderização de texto com Free Type](https://developer.xamarin.com/api/type/Urho.Gui.FontFaceFreeType/)
- [Cliente e servidor, recursos de rede](https://developer.xamarin.com/api/namespace/Urho.Network/)
- [Importar uma grande variedade de ativos](https://developer.xamarin.com/api/namespace/Urho.Resources/) (com a biblioteca de ativos Open)
- [Malha de navegação e pathfinding](https://developer.xamarin.com/api/namespace/Urho.Navigation/) (usando reconvertida/desvio)
- [Geração de forma convexa para detecção de colisão](https://developer.xamarin.com/api/type/Urho.Physics.CollisionShape/) (usando StanHull)
- [Reprodução de áudio](https://developer.xamarin.com/api/namespace/Urho.Audio/) (com **libvorbis**)

## <a name="getting-started"></a>Guia de Introdução

UrhoSharp convenientemente é distribuído como um [pacote NuGet](https://www.nuget.org/) e ele pode ser adicionado a seus projetos c# ou F # destino Windows, Mac, Android ou iOS.  O NuGet inclui tanto as bibliotecas necessárias para executar o programa, bem como os ativos básicos (CoreData) usados pelo mecanismo.

### <a name="urho-as-a-portable-class-library"></a>Urho como uma biblioteca de classes portátil

O pacote de Urho pode ser consumido de um projeto específico da plataforma ou de um projeto de biblioteca de classes portátil, permitindo que você reutilize todo o código em todas as plataformas.  Isso significa que tudo o que você precisa fazer em cada plataforma gravar seu ponto de entrada específico de plataforma e, em seguida, transfere o controle para o código de jogo compartilhado.

### <a name="samples"></a>Exemplos

Você pode obter uma amostra para os recursos do Urho abrindo no Visual Studio para Mac ou o Visual Studio a solução de exemplo de:

[https://github.com/xamarin/urho-samples](https://github.com/xamarin/urho-samples)

A solução padrão contém projetos para o Android, iOS, Windows e Mac.  Vamos estruturar essa solução para que temos um iniciador específico de plataforma pequena e todos os códigos de exemplo e código de jogo reside em uma biblioteca de classes portátil, que ilustram como maximizar a reutilização de código em todas as plataformas.

Consulte o [Urho e sua plataforma](~/graphics-games/urhosharp/platform/index.md) para obter mais informações sobre como criar suas próprias soluções.

Como todos os exemplos compartilham um conjunto comum de elementos da interface do usuário, os exemplos têm abstraídos a configuração básica neste arquivo:

[https://github.com/xamarin/urho-samples/blob/master/FeatureSamples/Core/Sample.cs](https://github.com/xamarin/urho-samples/blob/master/FeatureSamples/Core/Sample.cs)

Isso fornece uma classe base de exemplo que manipula alguns pressionamentos de teclas básicos e toque eventos, configurações de uma câmera, fornece elementos da interface do usuário básica e isso permite que cada exemplo enfatizar a funcionalidade específica que está sendo exibida.

O exemplo a seguir mostra o que o mecanismo é capaz de fazer:

- [Jogos samply](https://github.com/xamarin/urho-samples/tree/master/SamplyGame) um clone simple de ShootySkies.

Enquanto os outros exemplos mostram as propriedades individuais de cada exemplo.

## <a name="basic-structure"></a>Estrutura básica

O jogo deve subclasse o [ `Application` ](https://developer.xamarin.com/api/type/Urho.Application/) classe, isso é onde você irá configurar seu jogo (no [ `Setup` ](https://developer.xamarin.com/api/member/Urho.Application.Setup/) método) e inicie o jogo (no [ `Start` ](https://developer.xamarin.com/api/member/Urho.Application.Start) método).  Em seguida, você pode construir sua interface de usuário principal.  Vamos percorrer uma pequena amostra que mostra as APIs para configurar uma cena 3D, alguns elementos de interface do usuário e anexar um comportamento simple a ela.

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

Se você executar este aplicativo, você rapidamente descobrirá que o tempo de execução está reclamando seus ativos, não existem.  O que você precisa fazer é criar uma hierarquia de seu projeto que começa com o nome do diretório especial "Dados" e, dentro de isso, coloque os ativos de referência em seu programa.  Em seguida, você deve definir as propriedades do item para cada ativo "Copiar para diretório de saída" para "Copiar se mais recente", que irá garantir que seus dados estão lá.

Vamos explica o que está acontecendo aqui.

Para iniciar seu aplicativo, você chama a função de inicialização do mecanismo, seguida por criar uma nova instância da classe seu aplicativo, como este:

```csharp
new MySample().Run();
```

O tempo de execução invocará o `Setup` e `Start` métodos para você.  Se você substituir `Setup` você pode configurar os parâmetros do mecanismo (não mostrado neste exemplo).

Você deve substituir `Start` como isso iniciará o jogo.  Esse método você carregar seus ativos, conectar manipuladores de eventos, a cena de instalação e iniciar as ações que você deseja.  Em nosso exemplo, ambos os criamos um pouco de interface do usuário para mostrar ao usuário, bem como configurar uma cena 3D.

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

A estrutura de interface do usuário está lá fornecer uma interface de usuário do jogo simples e funciona com a adição de novos nós para o [ `UI.Root` ](https://developer.xamarin.com/api/property/Urho.Gui.UI.Root/) nó.

A segunda parte de nossos exemplos de instalações de cena principal.  Isso envolve uma série de etapas, criar uma cena 3D, criando uma caixa 3D na tela, adicionando uma luz, uma câmera e um visor.  Esses são explorados mais detalhadamente na seção [cena, nós, componentes e câmeras](~/graphics-games/urhosharp/using.md#scenenodescomponentsandcameras).

A terceira parte de nosso exemplo dispara algumas das ações.  As ações são receitas que descrevem um efeito específico e criado uma vez que eles podem ser executadas por um nó sob demanda chamando o [ `RunActionAsync` ](https://developer.xamarin.com/api/member/Urho.Node.RunActionsAsync) método em um `Node`.

A primeira ação dimensiona a caixa com um efeito saltitante e segunda gira a caixa para sempre:

```csharp
await boxNode.RunActionsAsync(
    new EaseBounceOut(new ScaleTo(duration: 1f, scale: 1)));
```

Acima mostram como é a primeira ação que criamos um [ `ScaleTo` ](https://developer.xamarin.com/api/type/Urho.Actions.ScaleTo/) ação, isso é simplesmente uma receita que indica que você deseja dimensionar por um segundo para o valor de uma propriedade a escala de um nó.  Essa ação é encapsulada por sua vez com uma ação de atenuação, de [ `EaseBounceOut` ](https://developer.xamarin.com/api/type/Urho.Actions.EaseBounceInOut/) ação.  As ações de atenuação distorcem a execução de uma ação do linear e aplicam um efeito, nesse caso é o efeito de saltando-out.
Para que nossa receita poderia ser escrita como:

```csharp
var recipe = new EaseBounceOut(new ScaleTo(duration: 1f, scale: 1));
```

Quando a receita tiver sido criada, executamos a receita:

```csharp
await boxNode.RunActionsAsync (recipe)
```

A espera indica que o deseja retomar a execução após essa linha quando a ação é concluída.  Quando a ação for concluída, disparar a segunda animação.

O [UrhoSharp usando](~/graphics-games/urhosharp/using.md) documento explora mais detalhadamente os conceitos por trás de Urho e como estruturar seu código para criar um jogo.

## <a name="copyrights"></a>Direitos autorais

Esta documentação contém conteúdo original do Xamarin Inc, mas desenha extensivamente na documentação do código-fonte aberto para o projeto Urho3D e contém capturas de tela do projeto Cocos2D.

### <a name="related-links"></a>Links relacionados

- [Pasta de trabalho do planeta Terra](https://developer.xamarin.com/workbooks/graphics/urhosharp/planetearth/planetearth.workbook)
- [Explorar a pasta de trabalho de coordenadas](https://developer.xamarin.com/workbooks/graphics/urhosharp/coordinates/ExploringUrhoCoordinates.workbook)
