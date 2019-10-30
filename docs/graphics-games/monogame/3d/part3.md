---
title: Coordenadas 3D em monojogo
description: Entender o sistema de coordenadas 3D é uma etapa importante no desenvolvimento de jogos 3D. O monogame fornece várias classes para posicionamento, orientação e dimensionamento de objetos no espaço 3D.
ms.prod: xamarin
ms.assetid: A4130995-48FD-4E2E-9C2B-ADCEFF35BE3A
author: conceptdev
ms.author: crdun
ms.date: 03/28/2017
ms.openlocfilehash: a38f4b81f684d6d416e6abe017bc463e3097c6b1
ms.sourcegitcommit: 93697a20e6fc7da547a8714ac109d7953b61d63f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/28/2019
ms.locfileid: "72980864"
---
# <a name="3d-coordinates-in-monogame"></a>Coordenadas 3D em monojogo

_Entender o sistema de coordenadas 3D é uma etapa importante no desenvolvimento de jogos 3D. O monogame fornece várias classes para posicionamento, orientação e dimensionamento de objetos no espaço 3D._

O desenvolvimento de jogos 3D requer uma compreensão de como manipular objetos em um sistema de coordenadas 3D. Este tutorial abordará como manipular objetos visuais (especificamente um modelo). Vamos criar os conceitos de controle de um modelo para criar uma classe de câmera 3D.

Os conceitos apresentados originam-se da Algebra linear, mas usaremos uma abordagem prática para que qualquer usuário sem um plano de fundo de matemática forte possa aplicar esses conceitos em seus próprios jogos.

Abordaremos os seguintes tópicos:

- Criando um projeto
- Criando uma entidade de robô
- Movendo a entidade de robô
- Multiplicação de matriz
- Criando a entidade de câmera
- Movendo a câmera com entrada

Quando terminar, teremos um projeto com um robô movendo em um círculo e uma câmera que pode ser controlada pela entrada por toque:

![](part3-images/image1.gif "Once finished, the app will include a project with a robot moving in a circle and a camera which can be controlled by touch input")

## <a name="creating-a-project"></a>Criando um projeto

Este tutorial explica como mover objetos no espaço 3D. Vamos começar com o projeto para renderizar modelos e matrizes de vértices [que podem ser encontrados aqui](https://docs.microsoft.com/samples/xamarin/mobile-samples/modelsandvertsmg/). Depois de baixado, descompacte e abra o projeto para verificar se ele é executado e devemos ver o seguinte:

![](part3-images/image2.png "Once downloaded, unzip and open the project to make sure it runs and this view should be displayed")

## <a name="creating-a-robot-entity"></a>Criando uma entidade de robô

Antes de começarmos a mover nosso robô, criaremos uma classe `Robot` para conter a lógica de desenho e movimento. Os desenvolvedores de jogos se referem a esse encapsulamento de lógica e dados como uma *entidade*.

Adicione um novo arquivo de classe vazio à biblioteca de classes portátil **MonoGame3D** (não o ModelAndVerts. Android específico da plataforma). Nomeie-o como **robô** e clique em **novo**:

![](part3-images/image3.png "Name it Robot and click New")

Modifique a classe `Robot` da seguinte maneira:

```csharp
using System;
using Microsoft.Xna.Framework;
using Microsoft.Xna.Framework.Graphics;
using Microsoft.Xna.Framework.Content;

namespace MonoGame3D
{
    public class Robot
    {
        Model model;

        public void Initialize(ContentManager contentManager)
        {
            model = contentManager.Load<Model> ("robot");

        }

        // For now we'll take these values in, eventually we'll
        // take a Camera object
        public void Draw(Vector3 cameraPosition, float aspectRatio)
        {
            foreach (var mesh in model.Meshes)
            {
                foreach (BasicEffect effect in mesh.Effects)
                {
                    effect.EnableDefaultLighting ();
                    effect.PreferPerPixelLighting = true;

                    effect.World = Matrix.Identity; 
                    var cameraLookAtVector = Vector3.Zero;
                    var cameraUpVector = Vector3.UnitZ;

                    effect.View = Matrix.CreateLookAt (
                        cameraPosition, cameraLookAtVector, cameraUpVector);

                    float fieldOfView = Microsoft.Xna.Framework.MathHelper.PiOver4;
                    float nearClipPlane = 1;
                    float farClipPlane = 200;

                    effect.Projection = Matrix.CreatePerspectiveFieldOfView(
                        fieldOfView, aspectRatio, nearClipPlane, farClipPlane);

                }

                // Now that we've assigned our properties on the effects we can
                // draw the entire mesh
                mesh.Draw ();
            }
        }
    }
}
```

O código de `Robot` é essencialmente o mesmo código em `Game1` para desenhar um `Model`. Para obter uma revisão sobre como carregar e desenhar `Model`, consulte [este guia sobre como trabalhar com modelos](~/graphics-games/monogame/3d/part1.md). Agora podemos remover todos os `Model` carregar e renderizar o código de `Game1`e substituí-lo por uma instância `Robot`:

```csharp
using Microsoft.Xna.Framework;
using Microsoft.Xna.Framework.Graphics;

namespace MonoGame3D
{
    public class Game1 : Game
    {
        GraphicsDeviceManager graphics;

        VertexPositionNormalTexture[] floorVerts;

        BasicEffect effect;

        Texture2D checkerboardTexture;

        Vector3 cameraPosition = new Vector3(15, 10, 10);

        Robot robot;

        public Game1()
        {
            graphics = new GraphicsDeviceManager(this);
            graphics.IsFullScreen = true;

            Content.RootDirectory = "Content";
        }

        protected override void Initialize ()
        {
            floorVerts = new VertexPositionNormalTexture[6];

            floorVerts [0].Position = new Vector3 (-20, -20, 0);
            floorVerts [1].Position = new Vector3 (-20,  20, 0);
            floorVerts [2].Position = new Vector3 ( 20, -20, 0);

            floorVerts [3].Position = floorVerts[1].Position;
            floorVerts [4].Position = new Vector3 ( 20,  20, 0);
            floorVerts [5].Position = floorVerts[2].Position;

            int repetitions = 20;

            floorVerts [0].TextureCoordinate = new Vector2 (0, 0);
            floorVerts [1].TextureCoordinate = new Vector2 (0, repetitions);
            floorVerts [2].TextureCoordinate = new Vector2 (repetitions, 0);

            floorVerts [3].TextureCoordinate = floorVerts[1].TextureCoordinate;
            floorVerts [4].TextureCoordinate = new Vector2 (repetitions, repetitions);
            floorVerts [5].TextureCoordinate = floorVerts[2].TextureCoordinate;

            effect = new BasicEffect (graphics.GraphicsDevice);

            robot = new Robot ();
            robot.Initialize (Content);

            base.Initialize ();
        }

        protected override void LoadContent()
        {
            using (var stream = TitleContainer.OpenStream ("Content/checkerboard.png"))
            {
                checkerboardTexture = Texture2D.FromStream (this.GraphicsDevice, stream);
            }
        }

        protected override void Update(GameTime gameTime)
        {
            base.Update(gameTime);
        }

        protected override void Draw(GameTime gameTime)
        {
            GraphicsDevice.Clear(Color.CornflowerBlue);

            DrawGround ();

            float aspectRatio = 
                graphics.PreferredBackBufferWidth / (float)graphics.PreferredBackBufferHeight;
            robot.Draw (cameraPosition, aspectRatio);

            base.Draw(gameTime);
        }

        void DrawGround()
        {
            var cameraLookAtVector = Vector3.Zero;
            var cameraUpVector = Vector3.UnitZ;

            effect.View = Matrix.CreateLookAt (
                cameraPosition, cameraLookAtVector, cameraUpVector);

            float aspectRatio = 
                graphics.PreferredBackBufferWidth / (float)graphics.PreferredBackBufferHeight;
            float fieldOfView = Microsoft.Xna.Framework.MathHelper.PiOver4;
            float nearClipPlane = 1;
            float farClipPlane = 200;

            effect.Projection = Matrix.CreatePerspectiveFieldOfView(
                fieldOfView, aspectRatio, nearClipPlane, farClipPlane);

            effect.TextureEnabled = true;
            effect.Texture = checkerboardTexture;

            foreach (var pass in effect.CurrentTechnique.Passes)
            {
                pass.Apply ();

                graphics.GraphicsDevice.DrawUserPrimitives (
                            PrimitiveType.TriangleList,
                    floorVerts,
                    0,
                    2);
            }
        }
    }
}
```

Se executarmos o código agora, teremos uma cena com apenas um robô que é desenhado na maior parte do piso:

![](part3-images/image4.png "If the code is run now, the app will display a scene with only one robot which is drawn mostly under the floor")

## <a name="moving-the-robot"></a>Movendo o robô

Agora que temos uma classe `Robot`, podemos adicionar lógica de movimentação ao robô. Nesse caso, simplesmente faremos a movimentação do robô em um círculo de acordo com o horário do jogo. Essa é uma implementação um pouco prática para um jogo real, uma vez que um caractere normalmente pode responder à entrada ou inteligência artificial, mas fornece um ambiente para explorar o posicionamento e a rotação 3D.

A única informação que precisaremos de fora da classe `Robot` é a hora atual do jogo. Adicionaremos um método `Update` que utilizará um parâmetro `GameTime`. Esse parâmetro de `GameTime` será usado para incrementar uma variável de ângulo que usaremos para determinar a posição final do robô.

Primeiro, adicionaremos o campo Angle à classe `Robot` sob o campo `model`:

```csharp
public class Robot
{
    public Model model;

    // new code:
    float angle;
    ...
```

 Agora, podemos incrementar esse valor em uma função `Update`:

```csharp
public void Update(GameTime gameTime)
{
    // TotalSeconds is a double so we need to cast to float
    angle += (float)gameTime.ElapsedGameTime.TotalSeconds;
}
```

Precisamos garantir que o método `Update` seja chamado de `Game1.Update`:

```csharp
protected override void Update(GameTime gameTime)
{
    robot.Update (gameTime);
    base.Update(gameTime);
}
```

É claro que neste ponto o campo de ângulo não faz nada – precisamos escrever código para usá-lo. Modificaremos o método `Draw` para que possamos calcular o `Matrix` mundial em um método dedicado: 

```csharp
public void Draw(Vector3 cameraPosition, float aspectRatio)
{
    foreach (var mesh in model.Meshes)
    {
        foreach (BasicEffect effect in mesh.Effects)
        {
            effect.EnableDefaultLighting ();
            effect.PreferPerPixelLighting = true;
            // We’ll be doing our calculations here...
            effect.World = GetWorldMatrix();

            var cameraLookAtVector = Vector3.Zero;
            var cameraUpVector = Vector3.UnitZ;

            effect.View = Matrix.CreateLookAt (
                cameraPosition, cameraLookAtVector, cameraUpVector);

            float fieldOfView = Microsoft.Xna.Framework.MathHelper.PiOver4;
            float nearClipPlane = 1;
            float farClipPlane = 200;

            effect.Projection = Matrix.CreatePerspectiveFieldOfView(
                fieldOfView, aspectRatio, nearClipPlane, farClipPlane);
        }

        mesh.Draw ();
    }
}
```

Em seguida, implementaremos o método `GetWorldMatrix` na classe `Robot`:

```csharp
Matrix GetWorldMatrix()
{
    const float circleRadius = 8;
    const float heightOffGround = 3;

    // this matrix moves the model "out" from the origin
    Matrix translationMatrix = Matrix.CreateTranslation (
        circleRadius, 0, heightOffGround);

    // this matrix rotates everything around the origin
    Matrix rotationMatrix = Matrix.CreateRotationZ (angle);

    // We combine the two to have the model move in a circle:
    Matrix combined = translationMatrix * rotationMatrix;

    return combined;
}
```

O resultado da execução deste código resulta na movimentação do robô em um círculo:

![](part3-images/image5.gif "Running this code results in the robot moving in a circle")

## <a name="matrix-multiplication"></a>Multiplicação de matriz

O código acima gira o robô criando uma `Matrix` no método `GetWorldMatrix`. O struct `Matrix` contém 16 valores float que podem ser usados para traduzir (definir posição), girar e dimensionar (definir tamanho). Quando atribuímos a propriedade `effect.World`, estamos dizendo ao sistema de renderização subjacente como posicionar, dimensionar e orientar qualquer coisa que estejamos desenhando (uma `Model` ou geometria de vértices). 

Felizmente, o struct `Matrix` inclui vários métodos que simplificam a criação de tipos comuns de matrizes. O primeiro usado no código acima é `Matrix.CreateTranslation`. A *tradução* de termo matemático se refere a uma operação que resulta em um ponto (ou em nosso caso um modelo) mudando de um local para outro sem nenhuma outra modificação (como rotação ou redimensionamento). A função usa um valor X, Y e Z para a tradução. Se exibirmos nossa cena de cima para baixo, nosso método de `CreateTranslation` (em isolamento) executará o seguinte:

![](part3-images/image6.png "The CreateTranslation method in isolation performs this action")

A segunda matriz que criamos é uma matriz de rotação usando a matriz de `CreateRotationZ`. Esse é um dos três métodos que podem ser usados para criar a rotação:

- `CreateRotationX`
- `CreateRoationY`
- `CreateRotationZ`

Cada método cria uma matriz de rotação girando sobre um determinado eixo. Em nosso caso, estamos girando sobre o eixo Z, que aponta para "up". O seguinte pode ajudar a visualizar como a rotação baseada em eixo funciona:

![](part3-images/image7.png "This can help visualize how axis-based rotation works")

Também estamos usando o método `CreateRotationZ` com o campo Angle, que é incrementado ao longo do tempo devido ao nosso método `Update` sendo chamado. O resultado é que o método `CreateRotationZ` faz com que nosso robô gire em volta da origem enquanto o tempo passa.

A linha final do código combina as duas matrizes em uma:

```csharp
Matrix combined = translationMatrix * rotationMatrix;
```

Isso é chamado de multiplicação de matriz, que funciona um pouco diferente do que a multiplicação regular. A *propriedade comutativa de multiplicação* indica que a ordem dos números em uma operação de multiplicação não altera o resultado. Ou seja, 3 \* 4 é equivalente a 4 \* 3. A multiplicação de matriz é diferente, pois não é comutador. Ou seja, a linha acima pode ser lida como "aplicar o translationMatrix para mover o modelo e, em seguida, girar tudo aplicando o rotationMatrix". Poderíamos Visualizar a maneira como a linha acima afeta a posição e a rotação da seguinte maneira:

![](part3-images/image8.png "A visualization pf the way that the above line affects the position and rotation")

Para ajudar a entender como a ordem de multiplicação de matriz pode afetar o resultado, considere o seguinte, em que a multiplicação de matriz é invertida:

```csharp
Matrix combined = rotationMatrix * translationMatrix;
```

O código acima primeiro giraria o modelo no local e, em seguida, o converteva:

![](part3-images/image9.png "The code above would first rotate the model in-place, then translate it")

Se executarmos o código com a multiplicação invertida, notaremos que, como a rotação é aplicada primeiro, afetará apenas a orientação do modelo e a posição do modelo permanecerá a mesma. Em outras palavras, o modelo gira em vigor:

![](part3-images/image10.gif "The model rotates in place")

## <a name="creating-the-camera-entity"></a>Criando a entidade de câmera

A entidade `Camera` conterá toda a lógica necessária para executar a movimentação baseada em entrada e fornecer propriedades para atribuir propriedades na classe `BasicEffect`.

Primeiro, implementaremos uma câmera estática (sem movimento baseado em entrada) e a integraremos ao nosso projeto existente. Adicione uma nova classe à biblioteca de classes portátil **MonoGame3D** (o mesmo projeto com `Robot.cs`) e nomeie-a como **câmera**. Substitua o conteúdo do arquivo pelo seguinte código:

```csharp
using System;
using Microsoft.Xna.Framework;
using Microsoft.Xna.Framework.Graphics;

namespace MonoGame3D
{
    public class Camera
    {
        // We need this to calculate the aspectRatio
        // in the ProjectionMatrix property.
        GraphicsDevice graphicsDevice;

        Vector3 position = new Vector3(15, 10, 10);

        public Matrix ViewMatrix
        {
            get
            {
                var lookAtVector = Vector3.Zero;
                var upVector = Vector3.UnitZ;

                return Matrix.CreateLookAt (
                    position, lookAtVector, upVector);
            }
        }

        public Matrix ProjectionMatrix
        {
            get
            {
                float fieldOfView = Microsoft.Xna.Framework.MathHelper.PiOver4;
                float nearClipPlane = 1;
                float farClipPlane = 200;
                float aspectRatio = graphicsDevice.Viewport.Width / (float)graphicsDevice.Viewport.Height;

                return Matrix.CreatePerspectiveFieldOfView(
                    fieldOfView, aspectRatio, nearClipPlane, farClipPlane);
            }
        }

        public Camera(GraphicsDevice graphicsDevice)
        {
            this.graphicsDevice = graphicsDevice;
        }

        public void Update(GameTime gameTime)
        {
            // We'll be doing some input-based movement here
        }
    }
}
```

O código acima é muito semelhante ao código de `Game1` e `Robot` que atribuem as matrizes no `BasicEffect`. 

Agora, podemos integrar a nova classe `Camera` em nossos projetos existentes. Primeiro, modificaremos a classe `Robot` para pegar uma instância `Camera` em seu método `Draw`, o que eliminará muito código duplicado. Substitua o método `Robot.Draw` pelo seguinte:

```csharp
public void Draw(Camera camera)
{
    foreach (var mesh in model.Meshes)
    {
        foreach (BasicEffect effect in mesh.Effects)
        {
            effect.EnableDefaultLighting ();
            effect.PreferPerPixelLighting = true;

            effect.World = GetWorldMatrix();
            effect.View = camera.ViewMatrix;
            effect.Projection = camera.ProjectionMatrix;
        }

        mesh.Draw ();
    }
}
```

Em seguida, modifique o arquivo de `Game1.cs`:

```csharp
using Microsoft.Xna.Framework;
using Microsoft.Xna.Framework.Graphics;

namespace MonoGame3D
{
    public class Game1 : Game
    {
        GraphicsDeviceManager graphics;

        VertexPositionNormalTexture[] floorVerts;

        BasicEffect effect;

        Texture2D checkerboardTexture;

        // New camera code
        Camera camera;

        Robot robot;

        public Game1()
        {
            graphics = new GraphicsDeviceManager(this);
            graphics.IsFullScreen = true;

            Content.RootDirectory = "Content";
        }

        protected override void Initialize ()
        {
            floorVerts = new VertexPositionNormalTexture[6];

            floorVerts [0].Position = new Vector3 (-20, -20, 0);
            floorVerts [1].Position = new Vector3 (-20,  20, 0);
            floorVerts [2].Position = new Vector3 ( 20, -20, 0);

            floorVerts [3].Position = floorVerts[1].Position;
            floorVerts [4].Position = new Vector3 ( 20,  20, 0);
            floorVerts [5].Position = floorVerts[2].Position;

            int repetitions = 20;

            floorVerts [0].TextureCoordinate = new Vector2 (0, 0);
            floorVerts [1].TextureCoordinate = new Vector2 (0, repetitions);
            floorVerts [2].TextureCoordinate = new Vector2 (repetitions, 0);

            floorVerts [3].TextureCoordinate = floorVerts[1].TextureCoordinate;
            floorVerts [4].TextureCoordinate = new Vector2 (repetitions, repetitions);
            floorVerts [5].TextureCoordinate = floorVerts[2].TextureCoordinate;

            effect = new BasicEffect (graphics.GraphicsDevice);

            robot = new Robot ();
            robot.Initialize (Content);

            // New camera code
            camera = new Camera (graphics.GraphicsDevice);

            base.Initialize ();
        }

        protected override void LoadContent()
        {
            using (var stream = TitleContainer.OpenStream ("Content/checkerboard.png"))
            {
                checkerboardTexture = Texture2D.FromStream (this.GraphicsDevice, stream);
            }
        }

        protected override void Update(GameTime gameTime)
        {
            robot.Update (gameTime);
            // New camera code
            camera.Update (gameTime);
            base.Update(gameTime);
        }

        protected override void Draw(GameTime gameTime)
        {
            GraphicsDevice.Clear(Color.CornflowerBlue);

            DrawGround ();

            // New camera code
            robot.Draw (camera);

            base.Draw(gameTime);
        }

        void DrawGround()
        {
            // New camera code
            effect.View = camera.ViewMatrix;
            effect.Projection = camera.ProjectionMatrix;

            effect.TextureEnabled = true;
            effect.Texture = checkerboardTexture;

            foreach (var pass in effect.CurrentTechnique.Passes)
            {
                pass.Apply ();

                graphics.GraphicsDevice.DrawUserPrimitives (
                            PrimitiveType.TriangleList,
                    floorVerts,
                    0,
                    2);
            }
        }
    }
}
```

As modificações na `Game1` da versão anterior (que são identificadas com `// New camera code`) são:

- `Camera` campo no `Game1`
- `Camera` instanciação no `Game1.Initialize`
- `Camera.Update` chamada em `Game1.Update`
- `Robot.Draw` agora usa um parâmetro `Camera`
- `Game1.Draw` agora usa `Camera.ViewMatrix` e `Camera.ProjectionMatrix`

## <a name="moving-the-camera-with-input"></a>Movendo a câmera com entrada

Até agora, adicionamos uma entidade `Camera`, mas não fizemos nada com ela para alterar o comportamento do tempo de execução. Adicionaremos um comportamento que permite ao usuário:

- Toque no lado esquerdo da tela para transformar a câmera em direção à esquerda
- Toque no lado direito da tela para transformar a câmera à direita
- Toque no centro da tela para mover a câmera para frente

### <a name="making-lookat-relative"></a>Tornando o ver relativo

Primeiro, atualizaremos a classe `Camera` para incluir um campo `angle` que será usado para definir a direção que o `Camera` está voltado. Atualmente, nosso `Camera` determina a direção que ele está enfrentando por meio do `lookAtVector`local, que é atribuído a `Vector3.Zero`. Em outras palavras, nossa `Camera` sempre examina a origem. Se a câmera for movida, o ângulo que a câmera voltará também será alterado:

![](part3-images/image11.gif "If the Camera moves, then the angle that the camera is facing will also change")

Queremos que o `Camera` esteja voltado para a mesma direção, independentemente de sua posição – pelo menos até implementarmos a lógica para girar o `Camera` usando a entrada. A primeira alteração será ajustar a variável `lookAtVector` para que ela se baseie em nosso local atual, em vez de examinar uma posição absoluta:

```csharp
public class Camera
{
    GraphicsDevice graphicsDevice;

    // Let's start at X = 0 so we're looking at things head-on
    Vector3 position = new Vector3(0, 20, 10);

    public Matrix ViewMatrix
    {
        get
        {
            var lookAtVector = new Vector3 (0, -1, -.5f);
            lookAtVector += position;

            var upVector = Vector3.UnitZ;

            return  Matrix.CreateLookAt (
                position, lookAtVector, upVector);
        }
    }
    ...
```

Isso resulta na `Camera` exibição do mundo diretamente. Observe que o valor inicial de `position` foi modificado para `(0, 20, 10)` para que o `Camera` seja centralizado no eixo X. A execução do jogo exibe:

![](part3-images/image12.png "Running the game displays this view")

### <a name="creating-an-angle-variable"></a>Criando uma variável de ângulo

A variável `lookAtVector` controla o ângulo que a câmera está exibindo. Atualmente, ele é fixado para exibir o eixo Y negativo e ligeiramente inclinado para baixo (do valor de `-.5f` Z). Criaremos uma variável `angle` que será usada para ajustar a propriedade `lookAtVector`. 

Nas seções anteriores deste passo a passos, mostramos que as matrizes podem ser usadas para girar a forma como os objetos são desenhados. Também podemos usar matrizes para girar vetores como o `lookAtVector` usando o método `Vector3.Transform`. 

Adicione um campo de `angle` e modifique a propriedade `ViewMatrix` da seguinte maneira:

```csharp
public class Camera
{
    GraphicsDevice graphicsDevice;

    Vector3 position = new Vector3(0, 20, 10);

    float angle;

    public Matrix ViewMatrix
    {
        get
        {
            var lookAtVector = new Vector3 (0, -1, -.5f);
            // We'll create a rotation matrix using our angle
            var rotationMatrix = Matrix.CreateRotationZ (angle);
            // Then we'll modify the vector using this matrix:
            lookAtVector = Vector3.Transform (lookAtVector, rotationMatrix);
            lookAtVector += position;

            var upVector = Vector3.UnitZ;

            return  Matrix.CreateLookAt (
                position, lookAtVector, upVector);
        }
    }
    ...
```

### <a name="reading-input"></a>Lendo entrada

Nossa entidade de `Camera` agora pode ser totalmente controlada por meio de suas variáveis position e Angle – precisamos apenas alterá-las de acordo com a entrada.

Primeiro, obteremos o `TouchPanel` estado para descobrir onde o usuário está tocando na tela. Para obter mais informações sobre como usar a classe `TouchPanel`, consulte [a referência da API TouchPanel](http://www.monogame.net/documentation/?page=T_Microsoft_Xna_Framework_Input_Touch_TouchPanel).

Se o usuário estiver tocando no terceiro lado esquerdo, ajustaremos o valor de `angle` para que o `Camera` gire para a esquerda e, se o usuário estiver tocando no terceiro, vamos girar da outra maneira. Se o usuário estiver tocando no meio do terceiro da tela, moveremos o `Camera` para frente.

Primeiro, adicione uma instrução using para qualificar as classes `TouchPanel` e `TouchCollection` no `Camera.cs`:

```csharp
using Microsoft.Xna.Framework.Input.Touch; 
```

Em seguida, modifique o método `Update` para ler o painel de toque e ajustar as `angle` e `position` variáveis adequadamente:

```csharp
public void Update(GameTime gameTime)
{
    TouchCollection touchCollection = TouchPanel.GetState();

    bool isTouchingScreen = touchCollection.Count > 0;
    if (isTouchingScreen)
    {
        var xPosition = touchCollection [0].Position.X;

        float xRatio = xPosition / (float)graphicsDevice.Viewport.Width;

        if (xRatio < 1 / 3.0f)
        {
            angle += (float)gameTime.ElapsedGameTime.TotalSeconds;
        }
        else if (xRatio < 2 / 3.0f)
        {
            var forwardVector = new Vector3 (0, -1, 0);

            var rotationMatrix = Matrix.CreateRotationZ (angle);
            forwardVector = Vector3.Transform (forwardVector, rotationMatrix);

            const float unitsPerSecond = 3;

            this.position += forwardVector * unitsPerSecond *
                (float)gameTime.ElapsedGameTime.TotalSeconds ;
        }
        else
        {
            angle -= (float)gameTime.ElapsedGameTime.TotalSeconds;
        }
    }
}
```

Agora, o `Camera` responderá à entrada por toque:

![](part3-images/image1.gif "Now the Camera will respond to touch input")

O método Update começa chamando `TouchPanel.GetState`, que retorna uma coleção de toques. Embora `TouchPanel.GetState` possa retornar vários pontos de toque, vamos nos preocupar apenas com o primeiro para simplificar.

Se o usuário estiver tocando na tela, o código verificará se o primeiro toque está no terceiro esquerdo, médio ou direito da tela. Os terços esquerdo e direito giram a câmera aumentando ou diminuindo a `angle` variável de acordo com o valor de `TotalSeconds` (para que o jogo se comportar da mesma forma que a taxa de quadros).

Se o usuário estiver tocando no terceiro centro da tela, a câmera avançará. Isso é feito primeiro obtendo o vetor de encaminhamento, que é inicialmente definido como apontando para o eixo Y negativo e, em seguida, girado por uma matriz criada usando `Matrix.CreateRotationZ` e o valor de `angle`. Por fim, a `forwardVector` é aplicada a `position` usando o coeficiente `unitsPerSecond`.

## <a name="summary"></a>Resumo

Este tutorial explica como mover e girar `Models` em espaço 3D usando `Matrices` e a propriedade `BasicEffect.World`. Essa forma de movimentação fornece a base para mover objetos em jogos 3D. Este passo a passos também aborda como implementar uma entidade `Camera` para exibir o mundo de qualquer posição e ângulo.

## <a name="related-links"></a>Links relacionados

- [Link da API monogame](http://www.monogame.net/documentation/?page=api)
- [Projeto concluído (exemplo)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/monogame3dcamera)
