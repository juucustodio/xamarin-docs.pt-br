---
title: Usando a classe de modelo
description: A classe de modelo simplifica bastante a renderização 3D objetos complexos em comparação com o método tradicional de processamento de gráficos 3D. Objetos de modelo são criados a partir de arquivos de conteúdo, que permite fácil integração de conteúdo sem nenhum código personalizado.
ms.prod: xamarin
ms.assetid: AD0A7971-51B1-4E38-B412-7907CE43CDDF
ms.technology: xamarin-cross-platform
author: charlespetzold
ms.author: chape
ms.date: 03/28/2017
ms.openlocfilehash: 871e4b1ad058dd97635dab228522620850b229b7
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="using-the-model-class"></a>Usando a classe de modelo

_A classe de modelo simplifica bastante a renderização 3D objetos complexos em comparação com o método tradicional de processamento de gráficos 3D. Objetos de modelo são criados a partir de arquivos de conteúdo, que permite fácil integração de conteúdo sem nenhum código personalizado._

A API MonoGame inclui um `Model` classe que pode ser usado para armazenar dados carregados de um arquivo de conteúdo e para executar a renderização. Arquivos de modelo podem ser muito simples (por exemplo, um triângulo de cor sólido) ou podem incluir informações de processamento complexas, incluindo a textura e de iluminação.

Este passo a passo usa [um modelo 3D de um robô](https://github.com/xamarin/mobile-samples/blob/master/ModelRenderingMG/Resources/Content.zip?raw=true) e aborda o seguinte:

 - Iniciando um novo projeto de jogo
 - Criando XNBs para o modelo e sua textura
 - Incluindo os XNBs no projeto do jogo
 - Desenho de um modelo 3D
 - Vários modelos de desenho

Quando terminar, nosso projeto será exibida da seguinte maneira:

![](part1-images/image1.png "Quando terminar, o projeto será exibido assim")


# <a name="creating-an-empty-game-project"></a>Criando um projeto vazio de jogo

Precisaremos configurar um projeto de jogo primeiro chamado MonoGame3D. Para obter informações sobre como criar um novo projeto de MonoGame, consulte [este passo a passo sobre como criar um projeto de Monogame de plataforma cruzada](~/graphics-games/monogame/introduction/part1.md).

Antes de avançarmos, deve verificar se o projeto é aberto e implanta corretamente. Uma vez implantado, verá uma tela azul vazia:

![](part1-images/image2.png "Uma vez implantado, o desenvolvedor deve ver uma tela azul vazia")


# <a name="including-the-xnbs-in-the-game-project"></a>Incluindo os XNBs no projeto do jogo

O formato de arquivo .xnb é uma extensão padrão para o conteúdo criado (conteúdo que foi criado pelo [MonoGame Pipeline ferramenta](http://www.monogame.net/documentation/?page=Pipeline)). Todo o conteúdo criado tem um arquivo de origem (que é um arquivo. fbx no caso de nosso modelo) e um arquivo de destino (um arquivo .xnb). A. fbx é um formato de modelo 3D comuns que pode ser criado por aplicativos como [Maya](http://www.autodesk.com/products/maya/overview) e [Blender](http://www.blender.org/). 

O `Model` classe pode ser criada pelo carregamento de um arquivo de .xnb de um disco que contém dados de geometria 3D.   Este arquivo .xnb é criado por meio de um projeto de conteúdo. Modelos de Monogame incluem automaticamente um projeto de conteúdo (com a extensão de .mgcp) em nossa pasta de conteúdo. Para obter uma discussão detalhada sobre a ferramenta MonoGame Pipeline, consulte o [guia conteúdo Pipeline](~/graphics-games/cocossharp/content-pipeline/index.md).

Este guia não serão feitos usando o Pipeline de MonoGame ferramenta e usará o. Arquivos XNB incluídos aqui. Observe que o. Arquivos XNB diferem por plataforma, portanto certifique-se de usar o conjunto correto de arquivos XNB para qualquer plataforma que você está trabalhando.

Podemos será descompacte o [Content.zip arquivo](https://github.com/xamarin/mobile-samples/blob/master/ModelRenderingMG/Resources/Content.zip?raw=true) para que podemos usar os arquivos contidos .xnb em nosso jogo. Se trabalhar em um projeto do Android, clique duas vezes no **ativos** pasta o **WalkingGame.Android** projeto. Se trabalhar em um projeto do iOS, clique duas vezes no **WalkingGame.iOS** projeto. Selecione **Adicionar -> Adicionar arquivos...**  e selecione os dois arquivos .xnb na pasta para a plataforma, você está trabalhando.

Os dois arquivos devem fazer parte de nosso projeto agora:

![](part1-images/xnbsinxs.png "Os dois arquivos devem ser parte do projeto agora")

O Visual Studio para Mac não pode definir a ação de compilação para XNBs recentemente adicionado automaticamente. Para iOS, clique em cada um dos arquivos e selecione **ação de criação -> BundleResource**. Para o Android, clique em cada um dos arquivos e selecione **ação de criação -> AndroidAsset**.

# <a name="rendering-a-3d-model"></a>Renderização de um modelo 3D

É a última etapa necessária ver o modelo na tela Adicionar o carregamento e o código de desenho. Especificamente, podemos fará o seguinte:

 - Definindo um `Model` instância em nosso `Game1` classe
 - Carregando o `Model` de instância `Game1.LoadContent`
 - Desenho de `Model` de instância `Game1.Draw`

Substitua o `Game1.cs` arquivo de código (que está localizado no **WalkingGame** PCL) com o seguinte:


```csharp
public class Game1 : Game
{
    GraphicsDeviceManager graphics;

    // This is the model instance that we'll load
    // our XNB into:
    Model model;

    public Game1()
    {
        graphics = new GraphicsDeviceManager(this);
        graphics.IsFullScreen = true;
                    
        Content.RootDirectory = "Content";
    }
    protected override void LoadContent()
    {
        // Notice that loading a model is very similar
        // to loading any other XNB (like a Texture2D).
        // The only difference is the generic type.
        model = Content.Load<Model> ("robot");
    }

    protected override void Update(GameTime gameTime)
    {
        base.Update(gameTime);
    }

    protected override void Draw(GameTime gameTime)
    {
        GraphicsDevice.Clear(Color.CornflowerBlue);

        // A model is composed of "Meshes" which are
        // parts of the model which can be positioned
        // independently, which can use different textures,
        // and which can have different rendering states
        // such as lighting applied.
        foreach (var mesh in model.Meshes)
        {
            // "Effect" refers to a shader. Each mesh may
            // have multiple shaders applied to it for more
            // advanced visuals. 
            foreach (BasicEffect effect in mesh.Effects)
            {
                // We could set up custom lights, but this
                // is the quickest way to get somethign on screen:
                effect.EnableDefaultLighting ();
                // This makes lighting look more realistic on
                // round surfaces, but at a slight performance cost:
                effect.PreferPerPixelLighting = true;

                // The world matrix can be used to position, rotate
                // or resize (scale) the model. Identity means that
                // the model is unrotated, drawn at the origin, and
                // its size is unchanged from the loaded content file.
                effect.World = Matrix.Identity;

                // Move the camera 8 units away from the origin:
                var cameraPosition = new Vector3 (0, 8, 0);
                // Tell the camera to look at the origin:
                var cameraLookAtVector = Vector3.Zero;
                // Tell the camera that positive Z is up
                var cameraUpVector = Vector3.UnitZ;

                effect.View = Matrix.CreateLookAt (
                    cameraPosition, cameraLookAtVector, cameraUpVector);

                // We want the aspect ratio of our display to match
                // the entire screen's aspect ratio:
                float aspectRatio = 
                    graphics.PreferredBackBufferWidth / (float)graphics.PreferredBackBufferHeight;
                // Field of view measures how wide of a view our camera has.
                // Increasing this value means it has a wider view, making everything
                // on screen smaller. This is conceptually the same as "zooming out".
                // It also 
                float fieldOfView = Microsoft.Xna.Framework.MathHelper.PiOver4;
                // Anything closer than this will not be drawn (will be clipped)
                float nearClipPlane = 1;
                // Anything further than this will not be drawn (will be clipped)
                float farClipPlane = 200;

                effect.Projection = Matrix.CreatePerspectiveFieldOfView(
                    fieldOfView, aspectRatio, nearClipPlane, farClipPlane);

            }

            // Now that we've assigned our properties on the effects we can
            // draw the entire mesh
            mesh.Draw ();
        }
        base.Draw(gameTime);
    }
}
                                                                                                                 
```

Se executarmos esse código, que você verá o modelo na tela:

![](part1-images/image8.png "Se esse código for executado, o modelo será exibido na tela")

Vamos examinar algumas das partes mais importantes de código acima.


## <a name="model-class"></a>Classe de modelo

O `Model` classe é a classe principal para executar a renderização 3D de arquivos de conteúdo (como arquivos. fbx). Ele contém todas as informações necessárias para processamento, incluindo a geometria 3D, as referências de textura, e `BasicEffect` instâncias que controlam os valores de posicionamento, iluminação e câmera.

O `Model` classe em si não tem variáveis de posicionamento como uma instância única de modelo pode ser renderizada em vários locais, como mostraremos posteriormente neste guia diretamente.

Cada `Model` é composto de um ou mais `ModelMesh` instâncias que são expostas por meio de `Meshes` propriedade. Embora talvez consideramos uma `Model` como um jogo único objeto (como um robô ou um carro), cada `ModelMesh` podem ser desenhados com diferentes `BasicEffect` valores. Por exemplo, partes de malha individual podem representar os segmentos de um robô ou rodas de um carro e pode atribuir a `BasicEffect` valores para fazer a rotação de rodas ou os segmentos de movem. 


## <a name="basiceffect-class"></a>Classe BasicEffect

O `BasicEffect` classe fornece propriedades para controlar as opções de renderização. A primeira modificação que fizermos a `BasicEffect` é chamar o `EnableDefaultLighting` método. Como o nome implica, isso permite que a iluminação padrão, que é muito útil para verificar se um `Model` aparece em jogo conforme o esperado. Se nós comentar o `EnableDefaultLighting` chamar, em seguida, você verá o modelo renderizado com apenas sua textura, mas nenhum sombreamento ou especular brilho:


```csharp
//effect.EnableDefaultLighting (); 
```

![](part1-images/image9.png "O modelo renderizado com apenas sua textura, mas nenhum sombreamento ou especular brilho")

O `World` propriedade pode ser usada para ajustar a posição, rotação e escala do modelo. O código acima usa o `Matrix.Identity` valor, o que significa que o `Model` processará o jogo exatamente conforme especificado no arquivo. fbx. Falaremos sobre matrizes e coordenadas 3D mais detalhadamente em [parte 3](~/graphics-games/monogame/3d/part3.md), mas como um exemplo, podemos alterar a posição do `Model` alterando o `World` propriedade da seguinte maneira:


```csharp
// Z is up, so changing Z to 3 moves the object up 3 units:
var modelPosition = new Vector3 (0, 0, 3);
effect.World = Matrix.CreateTranslation (modelPosition); 
```

Esse código resulta no objeto que está sendo movido para cima por 3 unidades do mundo:

![](part1-images/image10.png "Esse código resulta no objeto que está sendo movido para cima por 3 unidades do mundo")

As duas propriedades finais atribuídas a `BasicEffect` são `View` e `Projection`. Falaremos sobre câmeras 3D em [parte 3](~/graphics-games/monogame/3d/part3.md), mas como um exemplo, podemos modificar a posição da câmera, alterando local `cameraPosition` variável:


```csharp
// The 8 has been changed to a 30 to move the Camera further back
var cameraPosition = new Vector3 (0, 30, 0);
```

Podemos ver a câmera foi movida de volta adicional, resultando no `Model` aparecendo menor devido a perspectiva:

![](part1-images/image11.png "A câmera tem mais movido, resultando no modelo de exibição menores devido a perspectiva")


# <a name="rendering-multiple-models"></a>Vários modelos de renderização

Como mencionado acima, um único `Model` podem ser desenhados várias vezes. Para facilitar essa tarefa será movendo o `Model` código de desenho em seu próprio método que utiliza o desejado `Model` posição como um parâmetro. Uma vez concluído, nosso `Draw` e `DrawModel` métodos terá a seguinte aparência:


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

Isso resulta no robô modelo desenhado seis vezes:

![](part1-images/image1.png "Isso resulta no robô modelo desenhado seis vezes")


# <a name="summary"></a>Resumo

Este passo a passo introduziu do MonoGame `Model` classe. Ele aborda a conversão de um arquivo. fbx em um .xnb, que pode por sua vez ser carregado em um `Model` classe. Ele também mostra como modificações para um `BasicEffect` instância pode afetar `Model` desenho.

## <a name="related-links"></a>Links relacionados

- [Referência de modelo de MonoGame](http://www.monogame.net/documentation/?page=T_Microsoft_Xna_Framework_Graphics_Model)
- [Content.zip](https://github.com/xamarin/mobile-samples/blob/master/ModelRenderingMG/Resources/Content.zip?raw=true)
- [Projeto concluído (exemplo)](https://developer.xamarin.com/samples/mobile/ModelRenderingMG/)
