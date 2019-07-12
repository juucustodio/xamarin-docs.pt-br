---
title: Introdução ao UrhoSharp
description: Este documento descreve a estrutura básica de um aplicativo de UrhoSharp e links para várias guias e aplicativos de exemplo que demonstram como usar UrhoSharp.
ms.prod: xamarin
ms.assetid: 18041443-5093-4AF7-8B20-03E00478EF35
author: conceptdev
ms.author: crdun
ms.date: 03/29/2017
ms.openlocfilehash: 441a3cc19b4246fb2bdea54508142a894af5c051
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67832543"
---
# <a name="introduction-to-urhosharp"></a>Introdução ao UrhoSharp

![Logotipo do UrhoSharp](introduction-images/urhosharp-icon.png)

UrhoSharp é um poderoso mecanismo de jogo 3D para desenvolvedores do Xamarin e .NET.  Ele é semelhante ao espírito de SceneKit e SpriteKit da Apple e incluir física, navegação, rede e muito mais enquanto ainda está sendo várias plataformas.

É uma associação do .NET para o [Urho3D](http://urho3d.github.io/) do mecanismo e permite aos desenvolvedores escrever código de plataforma cruzada que pode direcionar o Android, iOS, Windows e Mac com a mesma base de código e podem processar em sistemas OpenGL e Direct3D.

UrhoSharp é um mecanismo de jogo com muita funcionalidade prontos:

- Renderização de gráficos 3D poderosa
- Simulação de física (usando a biblioteca de marcador)
- Tratamento de cena
- Suporte a Async/await
- API de ações amigável
- Integração de 2D em cenas 3D
- Renderização de fonte com Free Type
- Cliente e servidor, recursos de rede
- Importar uma ampla gama de ativos (com a biblioteca de ativos aberto)
- Malha de navegação e pathfinding (usando reconvertida/desvio)
- Geração de envoltória convexa para detecção de colisão (usando StanHull)
- Reprodução de áudio (com **libvorbis**)

## <a name="get-started"></a>Introdução

UrhoSharp convenientemente é distribuído como um [pacote do NuGet](https://www.nuget.org/) e ele pode ser adicionado ao seu C# ou F# projetos destinados a Windows, Mac, Android ou iOS.  O NuGet é fornecido com tanto as bibliotecas necessárias para executar seu programa, bem como os ativos básicos (CoreData) usados pelo mecanismo.

### <a name="urho-as-a-portable-class-library"></a>Urho como uma biblioteca de classes portátil

O pacote Urho pode ser consumido de um projeto específico da plataforma ou de um projeto de biblioteca de classes portátil, permitindo que você reutilize todo o código em todas as plataformas.  Isso significa que tudo o que você teria que fazer em cada plataforma é escrever seu ponto de entrada específico de plataforma e, em seguida, transferir o controle ao seu código de jogo compartilhado.

### <a name="samples"></a>Exemplos

Você pode obter uma amostra dos recursos das Urho abrindo no Visual Studio para Mac ou Visual Studio a solução de exemplo do:

[https://github.com/xamarin/urho-samples](https://github.com/xamarin/urho-samples)

A solução padrão contém projetos para Android, iOS, Windows e Mac.  Vamos estruturar essa solução para que nós temos um iniciador de específico de plataforma pequena e todos os códigos de exemplo e código de jogo reside em uma biblioteca de classes portátil, ilustrando como maximizar a reutilização de código em todas as plataformas.

Consulte a [Urho e sua plataforma](~/graphics-games/urhosharp/platform/index.md) página para obter mais informações sobre como criar suas próprias soluções.

Como todas as amostras compartilham um conjunto comum de elementos da interface do usuário, os exemplos abstrai a configuração básica neste arquivo:

[https://github.com/xamarin/urho-samples/blob/master/FeatureSamples/Core/Sample.cs](https://github.com/xamarin/urho-samples/blob/master/FeatureSamples/Core/Sample.cs)

Isso fornece uma classe base de exemplo que lida com alguns pressionamentos de teclas básicos e uma câmera de eventos, configurações de toque, fornece elementos da interface do usuário básica e isso permite que cada exemplo para se concentrar na funcionalidade específica que está sendo apresentada.

O exemplo a seguir mostra o que é capaz de fazer o mecanismo:

- [Jogos samply](https://github.com/xamarin/urho-samples/tree/master/SamplyGame) um clone simple do ShootySkies.

Enquanto os outros exemplos mostram as propriedades individuais de cada exemplo.

## <a name="basic-structure"></a>Estrutura básica

Seu jogo deve subclasse de `Application` classe, isso é onde você irá configurar seu jogo (no `Setup` método) e comece seu jogo (no `Start` método).  Em seguida, você pode construir sua interface do usuário principal.  Vamos examinar um exemplo pequeno que mostra as APIs para configurar uma cena 3D, alguns elementos de interface do usuário e anexar um comportamento simple a ele.

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

Se você executar esse aplicativo, você rapidamente descobrirá que o tempo de execução está reclamando seus ativos, não existem.  O que você precisa fazer é criar uma hierarquia em seu projeto que começa com o nome do diretório especial "Dados" e, nesse ponto, você colocaria os ativos que você faz referência em seu programa.  Em seguida, você deve definir as propriedades do item para cada ativo "Copiar para diretório de saída" para "Copiar se mais recente", que garantirá que seus dados estarão lá.

Vamos explica o que está acontecendo aqui.

Para iniciar o aplicativo, você chama a função de inicialização do mecanismo, seguida por criar uma nova instância da classe de aplicativo, como este:

```csharp
new MySample().Run();
```

O tempo de execução invocará o `Setup` e `Start` métodos para você.  Se você substituir `Setup` você pode configurar os parâmetros do mecanismo (não mostrar neste exemplo).

Você deve substituir `Start` como isso inicializará seu jogo.  Esse método você carregue seus ativos, conectar manipuladores de eventos, sua cena de instalação e iniciar as ações que desejar.  Em nosso exemplo, ambos os criamos um pouco de interface do usuário para mostrar ao usuário, bem como configurar uma cena 3D.

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

A estrutura de interface do usuário está lá para fornecer uma interface do usuário do jogo muito simples e funciona com a adição de novos nós para o `UI.Root` nó.

A segunda parte de nossos exemplos de instalações a cena principal.  Isso envolve várias etapas, criando uma cena 3D, criando uma caixa 3D na tela, a adição de uma luz, uma câmera e um visor.  Esses são explorados mais detalhadamente na seção [cena, nós, componentes e câmeras](~/graphics-games/urhosharp/using.md#scenenodescomponentsandcameras).

A terceira parte da nossa amostra aciona algumas das ações.  As ações são receitas que descrevem um efeito específico e criado uma vez que eles podem ser executadas por um nó sob demanda chamando o `RunActionAsync` método em um `Node`.

A primeira ação dimensiona a caixa com um efeito de devolução e um segundo gira a caixa para sempre:

```csharp
await boxNode.RunActionsAsync(
    new EaseBounceOut(new ScaleTo(duration: 1f, scale: 1)));
```

As opções acima mostra como é a primeira ação que criamos um `ScaleTo` ação, isso é simplesmente uma receita que indica que você deseja dimensionar por um segundo para o valor de uma propriedade a escala de um nó.  Essa ação por sua vez é encapsulada em torno de uma ação de easing, o `EaseBounceOut` ação.  As ações de atenuação distorcem a execução linear de uma ação e aplicam um efeito, nesse caso, ele fornece o efeito de movimento horizontal.
Portanto, nossa receita poderia ser escrita como:

```csharp
var recipe = new EaseBounceOut(new ScaleTo(duration: 1f, scale: 1));
```

Depois que a receita tiver sido criada, executamos a receita:

```csharp
await boxNode.RunActionsAsync (recipe)
```

A expressão await indica que o deseja retomar a execução após essa linha quando a ação for concluída.  Depois que a ação for concluída, podemos disparar a segunda animação.

O [UrhoSharp usando](~/graphics-games/urhosharp/using.md) documento explora os conceitos por trás de Urho e como estruturar seu código para criar um jogo em mais detalhes.

## <a name="copyrights"></a>Direitos autorais

Esta documentação contém o conteúdo original do Xamarin Inc, mas desenha amplamente na documentação do código-fonte aberto para o projeto Urho3D e contém as capturas de tela do projeto Cocos2D.
