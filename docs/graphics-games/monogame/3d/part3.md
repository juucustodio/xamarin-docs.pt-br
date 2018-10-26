---
title: Coordenadas 3D no MonoGame
description: Noções básicas sobre o sistema de coordenadas 3D é uma etapa importante no desenvolvimento de jogos 3D. MonoGame fornece uma série de classes para posicionamento, a orientação e dimensionando objetos no espaço 3D.
ms.prod: xamarin
ms.assetid: A4130995-48FD-4E2E-9C2B-ADCEFF35BE3A
author: conceptdev
ms.author: crdun
ms.date: 03/28/2017
ms.openlocfilehash: dc21228f1daa74a90ff8f0ea346bc01b109f0987
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50107632"
---
# <a name="3d-coordinates-in-monogame"></a>Coordenadas 3D no MonoGame

_Noções básicas sobre o sistema de coordenadas 3D é uma etapa importante no desenvolvimento de jogos 3D. MonoGame fornece uma série de classes para posicionamento, a orientação e dimensionando objetos no espaço 3D._

Desenvolvimento de jogos 3D requer uma compreensão de como manipular objetos em um sistema de coordenadas 3D. Este passo a passo abordará como manipular objetos visuais (especificamente, um modelo). Criaremos os conceitos de controle de um modelo para criar uma classe de câmera 3D.

Os conceitos apresentados se originam de álgebra linear, mas vamos dar uma abordagem prática para que qualquer usuário sem um plano de fundo de matemática forte pode aplicar esses conceitos em seus próprios jogos.

Falaremos sobre os tópicos a seguir:

- Criando um projeto
- Criar uma entidade de robô
- Movendo a entidade de robô
- Multiplicação de matriz
- Criar a entidade de câmera
- Mover a câmera com a entrada

Depois de concluído, teremos um projeto com um robô movendo em um círculo e uma câmera que pode ser controlada pela entrada de toque:

![](part3-images/image1.gif "Quando terminar, o aplicativo incluirá um projeto com um robô movendo em um círculo e uma câmera que pode ser controlada pela entrada de toque")


## <a name="creating-a-project"></a>Criando um projeto

Este passo a passo se concentra na movimentação de objetos no espaço 3D. Vamos começar com o projeto para modelos de renderização e matrizes de vértice [que podem ser encontrado aqui](https://developer.xamarin.com/samples/mobile/ModelsAndVertsMG/). Após o download, descompacte e abra o projeto para garantir que ele é executado e deveremos ver o seguinte:

![](part3-images/image2.png "Após o download, descompacte e abra o projeto para garantir que ele é executado e essa exibição deve ser exibida.")


## <a name="creating-a-robot-entity"></a>Criar uma entidade de robô

Antes de começarmos a mover nossa robô ao redor, criaremos um `Robot` classe para conter a lógica de desenho e a movimentação. Os desenvolvedores de jogos se referir a esse encapsulamento da lógica e os dados como uma *entidade*.

Adicione um novo arquivo de classe vazia para o **MonoGame3D** biblioteca de classes portátil (não o ModelAndVerts.Android específico da plataforma). Denomine **robô** e clique em **New**:

![](part3-images/image3.png "Nomeie-o robô e clique em novo")

Modificar o `Robot` classe da seguinte maneira:

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

O `Robot` código é basicamente o mesmo código no `Game1` para desenhar um `Model`. Para uma revisão no `Model` Carregando e de desenho, consulte [neste guia sobre como trabalhar com modelos](~/graphics-games/monogame/3d/part1.md). Agora podemos remover todos os `Model` carregamento e processamento de código do `Game1`e substituí-lo com um `Robot` instância:

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

Se executarmos o código agora temos uma cena com apenas um robô que é desenhada sobretudo sob o chão:

![](part3-images/image4.png "Se o código for executado agora, o aplicativo exibirá uma cena com apenas um robô que é desenhada sobretudo sob o piso")

## <a name="moving-the-robot"></a>Mover o robô

Agora que temos um `Robot` classe, podemos adicionar lógica de movimentação para o robô. Nesse caso, podemos simplesmente fazer o robô mover em um círculo de acordo com o tempo de jogo. Isso é uma implementação de um pouco impraticável para um jogo real, pois um caractere normalmente pode reagir à entrada ou de inteligência artificial, mas ela fornece um ambiente para que possamos explorar o posicionamento 3D e rotação.

A única informação que vamos precisar de fora do `Robot` classe é a hora atual do jogo. Vamos adicionar um `Update` método que o levará um `GameTime` parâmetro. Isso `GameTime` parâmetro será usado para incrementar uma variável de ângulo que usaremos para determinar a posição final para o robô.

Em primeiro lugar, adicionaremos o campo de ângulo para o `Robot` classe sob o `model` campo:

```csharp
public class Robot
{
    public Model model;

    // new code:
    float angle;
    ...
```

 Agora podemos pode incrementar esse valor em uma `Update` função:

```csharp
public void Update(GameTime gameTime)
{
    // TotalSeconds is a double so we need to cast to float
    angle += (float)gameTime.ElapsedGameTime.TotalSeconds;
}
```

Precisamos ter certeza de que o `Update` método é chamado de `Game1.Update`:

```csharp
protected override void Update(GameTime gameTime)
{
    robot.Update (gameTime);
    base.Update(gameTime);
}
```

É claro que, neste ponto, o campo de ângulo não fará nada – precisamos escrever código para usá-lo. Vamos modificar o `Draw` método para que podemos calcular o mundo `Matrix` em um método dedicado: 

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

Em seguida, implementaremos o `GetWorldMatrix` método no `Robot` classe:

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

O resultado da execução desse código resulta no robô mover em círculo:

![](part3-images/image5.gif "Em execução desse código resulta no robô movendo em um círculo")

## <a name="matrix-multiplication"></a>Multiplicação de matriz

O código acima gira o robô com a criação de um `Matrix` no `GetWorldMatrix` método. O `Matrix` struct contém 16 valores de float que podem ser usados para traduzir (conjunto de posição), girar e dimensionar (Definir tamanho). Quando nós atribuímos a `effect.World` propriedade, estamos informando subjacente sistema de renderização como posicionar, tamanho e orientação sobre tudo o que por acaso está desenhando (um `Model` ou geometria de vértices). 

Felizmente, o `Matrix` struct inclui uma série de métodos que simplificam a criação dos tipos comuns de matrizes. A primeira usada no código acima é `Matrix.CreateTranslation`. O termo matemático *tradução* refere-se a uma operação que resulta em um ponto (ou em nosso caso, um modelo) movendo de um local para outro sem qualquer modificação outra (por exemplo, girar ou redimensionar). A função utiliza um valor de X, Y e Z para tradução. Se podemos exibir nossa cena de cima para baixo, nosso `CreateTranslation` método (isoladamente) executará o seguinte:

![](part3-images/image6.png "O método CreateTranslation em isolamento para executar esta ação")

A segunda matriz que criamos é uma matriz de rotação usando a `CreateRotationZ` matriz. Esse é um dos três métodos que podem ser usados para criar a rotação:

- `CreateRotationX`
- `CreateRoationY`
- `CreateRotationZ`

Cada método cria uma matriz de rotação, girando sobre um eixo. Em nosso caso, estamos está girando sobre o eixo Z, que aponta "para cima". A seguir pode ajudar a visualizar como com base no eixo de rotação funciona:

![](part3-images/image7.png "Isso pode ajudar a visualizar como com base no eixo de rotação funciona")

Também estamos usando o `CreateRotationZ` método com o campo de ângulo, o que aumenta ao longo do tempo devido ao nosso `Update` método sendo chamado. O resultado é que o `CreateRotationZ` método faz com que nosso robô girar em torno da origem passar do tempo.

A linha de código final combina as duas matrizes em uma:

```csharp
Matrix combined = translationMatrix * rotationMatrix;
```

Isso é chamado de multiplicação de matriz, que funciona um pouco diferente de multiplicação regular. O *Propriedade Comutativa da multiplicação* afirma que a ordem dos números em uma operação de multiplicação não altera o resultado. Ou seja, 3 * 4 é equivalente a 4 * 3. Multiplicação de matriz é diferente em que não é comutativa. Ou seja, a linha acima pode ser lido como "Se aplicam a translationMatrix para mover o modelo e girar tudo ao aplicar o rotationMatrix". Podemos pode visualizar a forma como a linha acima afeta a posição e rotação da seguinte maneira:

![](part3-images/image8.png "Uma visualização pf da maneira que a linha acima afeta a posição e rotação")

Para ajudar a entender como a ordem de multiplicação de matriz pode afetar o resultado, considere o seguinte, em que a multiplicação de matriz é invertida:

```csharp
Matrix combined = rotationMatrix * translationMatrix;
```

O código acima seria primeiro girar o modelo no lugar, e em seguida, convertê-lo:

![](part3-images/image9.png "O código acima seria primeiro girar o modelo no lugar e convertê-lo")

Se executarmos o código com a multiplicação invertida, você notará que como a rotação aplica-se pela primeira vez, ele afeta somente a orientação do modelo e a posição do modelo permaneça o mesmo. Em outras palavras, o modelo gira em vigor:

![](part3-images/image10.gif "O modelo gira em vigor")

## <a name="creating-the-camera-entity"></a>Criar a entidade de câmera

O `Camera` entidade conterá toda a lógica necessária para realizar a movimentação com base em entrada e para fornecer as propriedades para atribuir propriedades no `BasicEffect` classe.

Primeiro vamos implementar uma câmera estática (nenhum movimento baseado em entrada) e integrá-lo ao nosso projeto existente. Adicione uma nova classe para o **MonoGame3D** biblioteca de classes portátil (o mesmo projeto com `Robot.cs`) e nomeie-o **câmera**. Substitua o conteúdo do arquivo pelo seguinte código:

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

O código acima é muito semelhante ao código do `Game1` e `Robot` que atribuir as matrizes na `BasicEffect`. 

Agora podemos pode integrar o novo `Camera` classe em nossos projetos existentes. Primeiro, vamos modificar o `Robot` classe tirar uma `Camera` da instância no seu `Draw` método, o que eliminará muito código duplicado. Substitua o `Robot.Draw` método com o seguinte:

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

Em seguida, modifique o `Game1.cs` arquivo:

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

As modificações a `Game1` da versão anterior (que é identificado com `// New camera code` ) são:

- `Camera` Campo em `Game1`
- `Camera` instanciação em `Game1.Initialize`
- `Camera.Update` chamar em `Game1.Update`
- `Robot.Draw` agora usa um `Camera` parâmetro
- `Game1.Draw` agora usa `Camera.ViewMatrix` e `Camera.ProjectionMatrix`

## <a name="moving-the-camera-with-input"></a>Mover a câmera com a entrada

Até agora, adicionamos um `Camera` entidade, mas ainda não fez nada com a mudança de comportamento de tempo de execução. Vamos adicionar comportamento que permite ao usuário para:

- O lado esquerdo da tela para ativar a câmera em direção à esquerda de toque
- O lado direito da tela para ativar a câmera à direita de toque
- Toque o centro da tela para mover a câmera para frente

### <a name="making-lookat-relative"></a>Tornando lookAt relativo

Primeiro vamos atualizar o `Camera` classe para incluir uma `angle` campo que será usado para definir a direção que o `Camera` está enfrentando. Atualmente, nossos `Camera` determina a direção em que ele é voltado para a por meio do local `lookAtVector`, que é atribuído à `Vector3.Zero`. Em outras palavras, nossa `Camera` sempre foca a origem. Se a câmera for movida, o ângulo que é voltado para a câmera também será alterado:

![](part3-images/image11.gif "Se move a câmera, em seguida, o ângulo que é voltado para a câmera também será alterado")

Queremos que o `Camera` para ser voltada para a mesma direção, independentemente de sua posição – pelo menos até que podemos implementar a lógica para girar o `Camera` usando entrada. A primeira alteração será ajustar o `lookAtVector` variável ser baseado em nosso local atual, em vez de aparência em uma posição absoluta:

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

Isso resulta no `Camera` ver o mundo em estreita. Observe que o inicial `position` valor tiver sido modificado para `(0, 20, 10)` para que o `Camera` é centralizada no eixo X. Executando o jogo exibe:

![](part3-images/image12.png "Executar o jogo exibe este modo de exibição")

### <a name="creating-an-angle-variable"></a>Criação de um ângulo de variável

O `lookAtVector` variável controla o ângulo que nosso câmera está exibindo. Atualmente, ele é fixo para exibir para baixo do eixo Y negativo e Inclinado um pouco para baixo (da `-.5f` valor Z). Vamos criar uma `angle` variável que será usado para ajustar o `lookAtVector` propriedade. 

Nas seções anteriores deste passo a passo, mostramos que matrizes podem ser usadas para girar a como os objetos são desenhados. Também podemos usar matrizes para girar os vetores como o `lookAtVector` usando o `Vector3.Transform` método. 

Adicionar um `angle` do campo e modificar o `ViewMatrix` propriedade da seguinte maneira:

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

### <a name="reading-input"></a>Ler a entrada

Nosso `Camera` entidade agora pode ser totalmente controlada por meio de sua posição e as variáveis de ângulo – precisamos apenas alterá-los de acordo com a entrada.

Primeiro, falaremos sobre o `TouchPanel` estado para localizar onde o usuário está tocando a tela. Para obter mais informações sobre como usar o `TouchPanel` classe, consulte [referência da API TouchPanel](http://www.monogame.net/documentation/?page=T_Microsoft_Xna_Framework_Input_Touch_TouchPanel).

Se o usuário toca na terceira à esquerda, em seguida, podemos ajustar o `angle` valor para que o `Camera` gira à esquerda, e se o usuário toca na terceira certa, podemos irá girar de outra forma. Se o usuário toca no terceiro intermediário da tela, então, veremos o `Camera` para frente.

Primeiro, adicione um usando a instrução para qualificar os `TouchPanel` e `TouchCollection` as classes no `Camera.cs`:

```csharp
using Microsoft.Xna.Framework.Input.Touch; 
```

Em seguida, modifique a `Update` método para ler o painel de toque e ajustar as `angle` e `position` variáveis adequadamente:

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

Agora o `Camera` responderá à entrada por toque:

![](part3-images/image1.gif "Agora a câmera responderá à entrada por toque")

O método de atualização começa chamando `TouchPanel.GetState`, que retorna uma coleção de toques. Embora `TouchPanel.GetState` pode retornar vários pontos de toque, vou apenas nos preocupamos o primeiro para manter a simplicidade.

Se o usuário toca a tela, em seguida, o código verifica se o primeiro toque está à esquerda, meio ou terceiro à direita da tela. Os terços left e right girar a câmera, aumentando ou diminuindo o `angle` variável de acordo com o `TotalSeconds` valor (de modo que o jogo se comporta da mesma, independentemente da taxa de quadros).

Se o usuário toca o centro em terceiro lugar da tela, em seguida, a câmera é movida para frente. Isso é feito pela primeira vez, obtendo o vetor de avanço, que é inicialmente definido como apontando para o eixo Y negativo, então, girado em uma matriz criada usando `Matrix.CreateRotationZ` e o `angle` valor. Por fim o `forwardVector` é aplicada ao `position` usando o `unitsPerSecond` coeficiente.

## <a name="summary"></a>Resumo

Este passo a passo aborda como mover e girar `Models` em 3D de espaço usando `Matrices` e o `BasicEffect.World` propriedade. Essa forma de movimentação fornece a base para a movimentação de objetos em jogos 3D. Este passo a passo também aborda como implementar um `Camera` entidade para ver o mundo de qualquer posição e um ângulo.

## <a name="related-links"></a>Links relacionados

- [Link da API MonoGame](http://www.monogame.net/documentation/?page=api)
- [Projeto concluído (amostra)](https://developer.xamarin.com/samples/monodroid/MonoGame3DCamera/)
