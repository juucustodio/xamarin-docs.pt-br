---
title: Usando a classe Model
description: A classe Model simplifica muito a renderização de objetos 3D complexos em comparação com o método tradicional de renderização de gráficos 3D. Os objetos de modelo são criados a partir de arquivos de conteúdo, permitindo a fácil integração de conteúdo sem código personalizado.
ms.prod: xamarin
ms.assetid: AD0A7971-51B1-4E38-B412-7907CE43CDDF
author: conceptdev
ms.author: crdun
ms.date: 03/28/2017
ms.openlocfilehash: 909594fe86c9718d9922470d7fca36155e33aed3
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73005237"
---
# <a name="using-the-model-class"></a>Usando a classe Model

_A classe Model simplifica muito a renderização de objetos 3D complexos em comparação com o método tradicional de renderização de gráficos 3D. Os objetos de modelo são criados a partir de arquivos de conteúdo, permitindo a fácil integração de conteúdo sem código personalizado._

A API monogame inclui uma classe `Model` que pode ser usada para armazenar dados carregados de um arquivo de conteúdo e para executar a renderização. Arquivos de modelo podem ser muito simples (como um triângulo colorido sólido) ou podem incluir informações para renderização complexa, incluindo texturing e iluminação.

Este passo a passos usa [um modelo 3D de um robô](https://github.com/xamarin/mobile-samples/blob/master/ModelRenderingMG/Resources/Content.zip?raw=true) e cobre o seguinte:

- Iniciando um novo projeto de jogo
- Criando XNBs para o modelo e sua textura
- Incluindo o XNBs no projeto do jogo
- Desenhando um modelo 3D
- Desenhando vários modelos

Quando terminar, nosso projeto será exibido da seguinte maneira:

![Exemplo concluído mostrando seis robôs](part1-images/image1.png)

## <a name="creating-an-empty-game-project"></a>Criando um projeto de jogo vazio

Precisaremos configurar um projeto de jogo primeiro chamado MonoGame3D. Para saber mais sobre a criação de um novo projeto de monojogo, confira [este tutorial sobre como criar um projeto Multijogo de plataforma cruzada](~/graphics-games/monogame/introduction/part1.md).

Antes de prosseguir, devemos verificar se o projeto é aberto e implantado corretamente. Depois de implantado, devemos ver uma tela azul vazia:

![Tela de jogo azul em branco](part1-images/image2.png)

## <a name="including-the-xnbs-in-the-game-project"></a>Incluindo o XNBs no projeto do jogo

O formato de arquivo. XNB é uma extensão padrão para conteúdo criado (conteúdo que foi criado pela [ferramenta de pipeline de monojogo](http://www.monogame.net/documentation/?page=Pipeline)). Todo o conteúdo criado tem um arquivo de origem (que é um arquivo. FBX no caso de nosso modelo) e um arquivo de destino (um arquivo. XNB). O formato. FBX é um formato de modelo 3D comum que pode ser criado por aplicativos como [Maya](https://www.autodesk.com/products/maya/overview) e [Blender](https://www.blender.org/). 

A classe `Model` pode ser construída com o carregamento de um arquivo. XNB de um disco que contém dados geométricos 3D.   Esse arquivo. XNB é criado por meio de um projeto de conteúdo. Modelos de monojogo incluem automaticamente um projeto de conteúdo (com a extensão. MGCP) em nossa pasta de conteúdo. Para obter uma discussão detalhada sobre a ferramenta de pipeline de monojogo, consulte o [Guia de pipeline de conteúdo](https://github.com/xamarin/docs-archive/blob/master/Docs/CocosSharp/content-pipeline/introduction.md).

Para este guia, vamos ignorar usando a ferramenta de pipeline de monojogo e usaremos o. Arquivos XNB incluídos aqui. Observe que o. Os arquivos XNB diferem por plataforma, portanto, certifique-se de usar o conjunto correto de arquivos XNB para qualquer plataforma com a qual você esteja trabalhando.

Descompacto o [arquivo Content. zip](https://github.com/xamarin/mobile-samples/blob/master/ModelRenderingMG/Resources/Content.zip?raw=true) para que possamos usar os arquivos. XNB contidos em nosso jogo. Se estiver trabalhando em um projeto do Android, clique com o botão direito do mouse na pasta **ativos** no projeto **WalkingGame. Android** . Se estiver trabalhando em um projeto do iOS, clique com o botão direito do mouse no projeto **WalkingGame. Ios** . Selecione **Adicionar-> adicionar arquivos...** e selecione ambos os arquivos. XNB na pasta para a plataforma em que você está trabalhando.

Os dois arquivos devem fazer parte do nosso projeto agora:

![Pasta de conteúdo do Gerenciador de soluções com arquivos XNB](part1-images/xnbsinxs.png)

Visual Studio para Mac pode não definir automaticamente a ação de Build para XNBs recém-adicionados. Para iOS, clique com o botão direito do mouse em cada um dos arquivos e selecione **ação de Build-> BundleResource**. Para o Android, clique com o botão direito do mouse em cada um dos arquivos e selecione **ação de compilação-> AndroidAsset**.

## <a name="rendering-a-3d-model"></a>Renderizando um modelo 3D

A última etapa necessária para ver o modelo na tela é adicionar o código de carregamento e de desenho. Especificamente, faremos o seguinte:

- Definindo uma instância de `Model` em nossa classe `Game1`
- Carregando a instância de `Model` no `Game1.LoadContent`
- Desenhando a instância de `Model` no `Game1.Draw`

Substitua o arquivo de código `Game1.cs` (que está localizado no **WalkingGame** PCL) pelo seguinte:

```csharp
public class Game1 : Game
{
    GraphicsDeviceManager graphics;

    // This is the model instance that we'll load
    // our XNB into:
    Model model;

    public Game1()
    {
        graphics = new GraphicsDeviceManager(this);
        graphics.IsFullScreen = true;

        Content.RootDirectory = "Content";
    }
    protected override void LoadContent()
    {
        // Notice that loading a model is very similar
        // to loading any other XNB (like a Texture2D).
        // The only difference is the generic type.
        model = Content.Load<Model> ("robot");
    }

    protected override void Update(GameTime gameTime)
    {
        base.Update(gameTime);
    }

    protected override void Draw(GameTime gameTime)
    {
        GraphicsDevice.Clear(Color.CornflowerBlue);

        // A model is composed of "Meshes" which are
        // parts of the model which can be positioned
        // independently, which can use different textures,
        // and which can have different rendering states
        // such as lighting applied.
        foreach (var mesh in model.Meshes)
        {
            // "Effect" refers to a shader. Each mesh may
            // have multiple shaders applied to it for more
            // advanced visuals. 
            foreach (BasicEffect effect in mesh.Effects)
            {
                // We could set up custom lights, but this
                // is the quickest way to get somethign on screen:
                effect.EnableDefaultLighting ();
                // This makes lighting look more realistic on
                // round surfaces, but at a slight performance cost:
                effect.PreferPerPixelLighting = true;

                // The world matrix can be used to position, rotate
                // or resize (scale) the model. Identity means that
                // the model is unrotated, drawn at the origin, and
                // its size is unchanged from the loaded content file.
                effect.World = Matrix.Identity;

                // Move the camera 8 units away from the origin:
                var cameraPosition = new Vector3 (0, 8, 0);
                // Tell the camera to look at the origin:
                var cameraLookAtVector = Vector3.Zero;
                // Tell the camera that positive Z is up
                var cameraUpVector = Vector3.UnitZ;

                effect.View = Matrix.CreateLookAt (
                    cameraPosition, cameraLookAtVector, cameraUpVector);

                // We want the aspect ratio of our display to match
                // the entire screen's aspect ratio:
                float aspectRatio = 
                    graphics.PreferredBackBufferWidth / (float)graphics.PreferredBackBufferHeight;
                // Field of view measures how wide of a view our camera has.
                // Increasing this value means it has a wider view, making everything
                // on screen smaller. This is conceptually the same as "zooming out".
                // It also 
                float fieldOfView = Microsoft.Xna.Framework.MathHelper.PiOver4;
                // Anything closer than this will not be drawn (will be clipped)
                float nearClipPlane = 1;
                // Anything further than this will not be drawn (will be clipped)
                float farClipPlane = 200;

                effect.Projection = Matrix.CreatePerspectiveFieldOfView(
                    fieldOfView, aspectRatio, nearClipPlane, farClipPlane);

            }

            // Now that we've assigned our properties on the effects we can
            // draw the entire mesh
            mesh.Draw ();
        }
        base.Draw(gameTime);
    }
}
```

Se executarmos esse código, veremos o modelo na tela:

![Modelo exibido na tela](part1-images/image8.png "Se esse código for executado, o modelo será exibido na tela")

### <a name="model-class"></a>Classe de modelo

A classe `Model` é a classe principal para executar a renderização 3D de arquivos de conteúdo (como arquivos. fbx). Ele contém todas as informações necessárias para a renderização, incluindo a geometria 3D, as referências de textura e `BasicEffect` instâncias que controlam os valores de posicionamento, iluminação e câmera.

A própria classe `Model` não tem diretamente variáveis para posicionamento, uma vez que uma única instância de modelo pode ser renderizada em vários locais, como mostraremos posteriormente neste guia.

Cada `Model` é composta de uma ou mais instâncias de `ModelMesh`, que são expostas por meio da propriedade `Meshes`. Embora seja possível considerar uma `Model` como um único objeto de jogo (como um robô ou um carro), cada `ModelMesh` pode ser desenhada com diferentes valores de `BasicEffect`. Por exemplo, partes de malha individuais podem representar as pernas de um robô ou as rodas em um carro, e podemos atribuir os valores de `BasicEffect` para fazer as rodas girarem ou as pernas se moverem. 

### <a name="basiceffect-class"></a>Classe BasicEffect

A classe `BasicEffect` fornece propriedades para controlar as opções de renderização. A primeira modificação que fazemos para o `BasicEffect` é chamar o método `EnableDefaultLighting`. Como o nome indica, isso permite a iluminação padrão, que é muito útil para verificar se um `Model` aparece em jogo conforme o esperado. Se comentarmos a chamada `EnableDefaultLighting`, veremos o modelo renderizado com apenas sua textura, mas sem sombreamento ou brilho especulativo:

```csharp
//effect.EnableDefaultLighting ();
```

![O modelo é renderizado com apenas sua textura, mas sem sombreamento ou brilho especulativo](part1-images/image9.png "O modelo é renderizado com apenas sua textura, mas sem sombreamento ou brilho especulativo")

A propriedade `World` pode ser usada para ajustar a posição, a rotação e a escala do modelo. O código acima usa o valor `Matrix.Identity`, o que significa que o `Model` será renderizado no jogo exatamente como especificado no arquivo. FBX. Abordaremos matrizes e coordenadas 3D mais detalhadamente na [parte 3](~/graphics-games/monogame/3d/part3.md), mas, como exemplo, podemos alterar a posição da `Model` alterando a propriedade `World` da seguinte maneira:

```csharp
// Z is up, so changing Z to 3 moves the object up 3 units:
var modelPosition = new Vector3 (0, 0, 3);
effect.World = Matrix.CreateTranslation (modelPosition); 
```

Esse código resulta no objeto sendo movido para cima por três unidades mundiais:

![Esse código resulta no objeto sendo movido para cima por 3 unidades mundiais](part1-images/image10.png "Esse código resulta no objeto sendo movido para cima por 3 unidades mundiais")

As duas propriedades finais atribuídas na `BasicEffect` são `View` e `Projection`. Abordaremos câmeras 3D na [parte 3](~/graphics-games/monogame/3d/part3.md), mas, como exemplo, podemos modificar a posição da câmera alterando a variável de `cameraPosition` local:

```csharp
// The 8 has been changed to a 30 to move the Camera further back
var cameraPosition = new Vector3 (0, 30, 0);
```

Podemos ver que a câmera foi movida mais de volta, resultando na `Model` aparecendo menor devido à perspectiva:

![A câmera se moveu mais para trás, resultando na exibição do modelo menor devido à perspectiva](part1-images/image11.png "A câmera se moveu mais para trás, resultando na exibição do modelo menor devido à perspectiva")

## <a name="rendering-multiple-models"></a>Renderizando vários modelos

Conforme mencionado acima, uma única `Model` pode ser desenhada várias vezes. Para facilitar isso, vamos mover o `Model` o código de desenho para seu próprio método que usa a posição de `Model` desejada como um parâmetro. Depois de concluído, nossos métodos de `Draw` e `DrawModel` se parecerão com:

```csharp
protected override void Draw(GameTime gameTime)
{
    GraphicsDevice.Clear(Color.CornflowerBlue);
    DrawModel (new Vector3 (-4, 0, 0));
    DrawModel (new Vector3 ( 0, 0, 0));
    DrawModel (new Vector3 ( 4, 0, 0));
    DrawModel (new Vector3 (-4, 0, 3));
    DrawModel (new Vector3 ( 0, 0, 3));
    DrawModel (new Vector3 ( 4, 0, 3));
    base.Draw(gameTime);
}
void DrawModel(Vector3 modelPosition)
{
    foreach (var mesh in model.Meshes)
    {
        foreach (BasicEffect effect in mesh.Effects)
        {
            effect.EnableDefaultLighting ();
            effect.PreferPerPixelLighting = true;
            effect.World = Matrix.CreateTranslation (modelPosition);
            var cameraPosition = new Vector3 (0, 10, 0);
            var cameraLookAtVector = Vector3.Zero;
            var cameraUpVector = Vector3.UnitZ;
            effect.View = Matrix.CreateLookAt (
                cameraPosition, cameraLookAtVector, cameraUpVector);
            float aspectRatio = 
                graphics.PreferredBackBufferWidth / (float)graphics.PreferredBackBufferHeight;
            float fieldOfView = Microsoft.Xna.Framework.MathHelper.PiOver4;
            float nearClipPlane = 1;
            float farClipPlane = 200;
            effect.Projection = Matrix.CreatePerspectiveFieldOfView(
                fieldOfView, aspectRatio, nearClipPlane, farClipPlane);
        }
        // Now that we've assigned our properties on the effects we can
        // draw the entire mesh
        mesh.Draw ();
    }
}
```

Isso resulta no desenho do modelo robô seis vezes:

![Isso resulta no desenho do modelo de robô seis vezes](part1-images/image1.png "Isso resulta no desenho do modelo de robô seis vezes")

## <a name="summary"></a>Resumo

Este tutorial introduziu a classe de `Model` de jogos. Ele aborda a conversão de um arquivo. FBX em um. XNB, que pode, por sua vez, ser carregado em uma classe de `Model`. Ele também mostra como as modificações em uma instância de `BasicEffect` podem afetar `Model` desenho.

## <a name="related-links"></a>Links relacionados

- [Referência de modelo monogame](http://www.monogame.net/documentation/?page=T_Microsoft_Xna_Framework_Graphics_Model)
- [Content. zip](https://github.com/xamarin/mobile-samples/blob/master/ModelRenderingMG/Resources/Content.zip?raw=true)
- [Projeto concluído (exemplo)](https://docs.microsoft.com/samples/xamarin/mobile-samples/modelrenderingmg/)
