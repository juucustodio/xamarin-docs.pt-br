---
title: Coordenadas 3D em MonoGame
description: Noções básicas sobre o sistema de coordenadas 3D é uma etapa importante no desenvolvimento de jogos 3D. MonoGame fornece um número de classes para posicionamento, orientando e dimensionamento de objetos em espaço 3D.
ms.prod: xamarin
ms.assetid: A4130995-48FD-4E2E-9C2B-ADCEFF35BE3A
author: charlespetzold
ms.author: chape
ms.date: 03/28/2017
ms.openlocfilehash: 2f14d21302ed4295d16baa28723df6ef79863686
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/09/2018
---
# <a name="3d-coordinates-in-monogame"></a>Coordenadas 3D em MonoGame

_Noções básicas sobre o sistema de coordenadas 3D é uma etapa importante no desenvolvimento de jogos 3D. MonoGame fornece um número de classes para posicionamento, orientando e dimensionamento de objetos em espaço 3D._

Desenvolvimento de jogos 3D requer uma compreensão de como manipular objetos em um sistema de coordenadas 3D. Este passo a passo mostrará como manipular objetos visuais (especificamente, um modelo). Criaremos os conceitos de controle de um modelo para criar uma classe de câmera 3D.

Os conceitos apresentados originam álgebra linear, mas vamos dar uma abordagem prática para que qualquer usuário sem um plano de fundo de matemática forte pode aplicar esses conceitos em seus próprios jogos.

Falaremos sobre os tópicos a seguir:

- Criando um projeto
- Criar uma entidade de robô
- Movendo a entidade de robô
- Multiplicação de matrizes
- Criar a entidade de câmera
- Mover a câmera com entrada

Quando concluído, teremos um projeto com um robô móvel em um círculo e uma câmera, que pode ser controlada pela entrada por toque:

![](part3-images/image1.gif "Quando concluído, o aplicativo incluirá um projeto com um robô móvel em um círculo e uma câmera, que pode ser controlada pela entrada de toque")


## <a name="creating-a-project"></a>Criando um projeto

Este passo a passo se concentra na movimentação de objetos em espaço 3D. Vamos começar com o projeto para modelos de renderização e matrizes de vértice [que podem ser encontrado aqui](https://developer.xamarin.com/samples/mobile/ModelsAndVertsMG/). Após o download, descompacte e abra o projeto para verificar se ele é executado e veremos o seguinte:

![](part3-images/image2.png "Após o download, descompacte e abra o projeto para verificar se ele é executado e deve ser exibido neste modo de exibição")


## <a name="creating-a-robot-entity"></a>Criar uma entidade de robô

Antes de começar movendo nosso robô ao redor, criaremos um `Robot` classe para conter a lógica de desenho e a movimentação. Os desenvolvedores de jogos se referir a esse encapsulamento de lógica e os dados como um *entidade*.

Adicionar um novo arquivo de classe vazia para o **MonoGame3D** biblioteca de classes portátil (não o ModelAndVerts.Android específico da plataforma). Nomeie- **robô** e clique em **novo**:

![](part3-images/image3.png "Nomeie-o robô e clique em novo")

Modificar o `Robot` classe da seguinte maneira:

```csharp
using System;
using Microsoft.Xna.Framework;
using Microsoft.Xna.Framework.Graphics;
using Microsoft.Xna.Framework.Content;

namespace MonoGame3D
{
    public class Robot
    {
        Model model;

        public void Initialize(ContentManager contentManager)
        {
            model = contentManager.Load<Model> ("robot");

        }

        // For now we'll take these values in, eventually we'll
        // take a Camera object
        public void Draw(Vector3 cameraPosition, float aspectRatio)
        {
            foreach (var mesh in model.Meshes)
            {
                foreach (BasicEffect effect in mesh.Effects)
                {
                    effect.EnableDefaultLighting ();
                    effect.PreferPerPixelLighting = true;

                    effect.World = Matrix.Identity; 
                    var cameraLookAtVector = Vector3.Zero;
                    var cameraUpVector = Vector3.UnitZ;

                    effect.View = Matrix.CreateLookAt (
                        cameraPosition, cameraLookAtVector, cameraUpVector);

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
    }
}
```

O `Robot` código é essencialmente o mesmo código em `Game1` para desenhar uma `Model`. Para uma análise em `Model` carregar e desenho, consulte [neste guia sobre como trabalhar com modelos](~/graphics-games/monogame/3d/part1.md). Agora podemos remover todos os `Model` carregar e processar o código de `Game1`e substituí-lo por um `Robot` instância:

```csharp
using Microsoft.Xna.Framework;
using Microsoft.Xna.Framework.Graphics;

namespace MonoGame3D
{
    public class Game1 : Game
    {
        GraphicsDeviceManager graphics;

        VertexPositionNormalTexture[] floorVerts;

        BasicEffect effect;

        Texture2D checkerboardTexture;

        Vector3 cameraPosition = new Vector3(15, 10, 10);

        Robot robot;

        public Game1()
        {
            graphics = new GraphicsDeviceManager(this);
            graphics.IsFullScreen = true;

            Content.RootDirectory = "Content";
        }

        protected override void Initialize ()
        {
            floorVerts = new VertexPositionNormalTexture[6];

            floorVerts [0].Position = new Vector3 (-20, -20, 0);
            floorVerts [1].Position = new Vector3 (-20,  20, 0);
            floorVerts [2].Position = new Vector3 ( 20, -20, 0);

            floorVerts [3].Position = floorVerts[1].Position;
            floorVerts [4].Position = new Vector3 ( 20,  20, 0);
            floorVerts [5].Position = floorVerts[2].Position;

            int repetitions = 20;

            floorVerts [0].TextureCoordinate = new Vector2 (0, 0);
            floorVerts [1].TextureCoordinate = new Vector2 (0, repetitions);
            floorVerts [2].TextureCoordinate = new Vector2 (repetitions, 0);

            floorVerts [3].TextureCoordinate = floorVerts[1].TextureCoordinate;
            floorVerts [4].TextureCoordinate = new Vector2 (repetitions, repetitions);
            floorVerts [5].TextureCoordinate = floorVerts[2].TextureCoordinate;

            effect = new BasicEffect (graphics.GraphicsDevice);

            robot = new Robot ();
            robot.Initialize (Content);

            base.Initialize ();
        }

        protected override void LoadContent()
        {
            using (var stream = TitleContainer.OpenStream ("Content/checkerboard.png"))
            {
                checkerboardTexture = Texture2D.FromStream (this.GraphicsDevice, stream);
            }
        }

        protected override void Update(GameTime gameTime)
        {
            base.Update(gameTime);
        }

        protected override void Draw(GameTime gameTime)
        {
            GraphicsDevice.Clear(Color.CornflowerBlue);

            DrawGround ();

            float aspectRatio = 
                graphics.PreferredBackBufferWidth / (float)graphics.PreferredBackBufferHeight;
            robot.Draw (cameraPosition, aspectRatio);

            base.Draw(gameTime);
        }

        void DrawGround()
        {
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

            effect.TextureEnabled = true;
            effect.Texture = checkerboardTexture;

            foreach (var pass in effect.CurrentTechnique.Passes)
            {
                pass.Apply ();

                graphics.GraphicsDevice.DrawUserPrimitives (
                            PrimitiveType.TriangleList,
                    floorVerts,
                    0,
                    2);
            }
        }
    }
}
```

Se executarmos o código agora teremos uma cena com apenas um robô que é desenhada principalmente no chão de:

![](part3-images/image4.png "Se o código for executado agora, o aplicativo exibirá uma cena com apenas um robô que é desenhada principalmente no chão")

## <a name="moving-the-robot"></a>Mover o robô

Agora que temos uma `Robot` classe, podemos adicionar lógica de movimentação para o robô. Nesse caso, basta faremos o robô mover em um círculo de acordo com a hora de jogo. Isso é uma implementação muito pouco prática para um jogo real como um caractere pode responder normalmente para entrada ou inteligência artificial, mas ele fornece um ambiente para que possamos explorar posicionamento 3D e rotação.

A única informação que vamos precisar de fora do `Robot` classe é a hora atual do jogo. Vamos adicionar um `Update` método que o levará um `GameTime` parâmetro. Isso `GameTime` parâmetro será usado para incrementar a variável um ângulo que usaremos para determinar a posição final para o robô.

Primeiro, vamos adicionar o campo de ângulo para o `Robot` classe sob o `model` campo:

```csharp
public class Robot
{
    public Model model;

    // new code:
    float angle;
    ...
```

 Agora é possível incrementar este valor em uma `Update` função:

```csharp
public void Update(GameTime gameTime)
{
    // TotalSeconds is a double so we need to cast to float
    angle += (float)gameTime.ElapsedGameTime.TotalSeconds;
}
```

Precisamos nos certificar-se de que o `Update` método é chamado de `Game1.Update`:

```csharp
protected override void Update(GameTime gameTime)
{
    robot.Update (gameTime);
    base.Update(gameTime);
}
```

É claro que, neste momento, o campo de ângulo não fará nada – precisamos escrever código para usá-lo. Modificaremos a `Draw` método para que podemos calcular o mundo `Matrix` em um método dedicado: 

```csharp
public void Draw(Vector3 cameraPosition, float aspectRatio)
{
    foreach (var mesh in model.Meshes)
    {
        foreach (BasicEffect effect in mesh.Effects)
        {
            effect.EnableDefaultLighting ();
            effect.PreferPerPixelLighting = true;
            // We’ll be doing our calculations here...
            effect.World = GetWorldMatrix();

            var cameraLookAtVector = Vector3.Zero;
            var cameraUpVector = Vector3.UnitZ;

            effect.View = Matrix.CreateLookAt (
                cameraPosition, cameraLookAtVector, cameraUpVector);

            float fieldOfView = Microsoft.Xna.Framework.MathHelper.PiOver4;
            float nearClipPlane = 1;
            float farClipPlane = 200;

            effect.Projection = Matrix.CreatePerspectiveFieldOfView(
                fieldOfView, aspectRatio, nearClipPlane, farClipPlane);
        }

        mesh.Draw ();
    }
}
```

Em seguida, implementaremos o `GetWorldMatrix` método o `Robot` classe:

```csharp
Matrix GetWorldMatrix()
{
    const float circleRadius = 8;
    const float heightOffGround = 3;

    // this matrix moves the model "out" from the origin
    Matrix translationMatrix = Matrix.CreateTranslation (
        circleRadius, 0, heightOffGround);

    // this matrix rotates everything around the origin
    Matrix rotationMatrix = Matrix.CreateRotationZ (angle);

    // We combine the two to have the model move in a circle:
    Matrix combined = translationMatrix * rotationMatrix;

    return combined;
}
```

O resultado da execução desse código resulta no robô móvel em um círculo:

![](part3-images/image5.gif "Execução produz este código o robô móvel em um círculo")

## <a name="matrix-multiplication"></a>Multiplicação de matrizes

O código acima gira o robô criando um `Matrix` no `GetWorldMatrix` método. O `Matrix` struct contém 16 valores de ponto flutuante que podem ser usados para traduzir (posição do conjunto), girar e dimensionar (Definir tamanho). Quando nós atribuímos o `effect.World` propriedade, dizemos subjacente renderização sistema como posicionar, dimensionar e orientar tudo o que estamos ser desenho (um `Model` ou geometria de vértices). 

Felizmente, o `Matrix` struct inclui uma série de métodos que simplificam a criação dos tipos comuns de matrizes. A primeira usada no código acima é `Matrix.CreateTranslation`. O termo matemático *tradução* se refere a uma operação que resulta em um ponto (ou em nosso caso, um modelo) mover de um local para outro sem modificação (como girar ou redimensionar). A função pega um valor de X, Y e Z para conversão. Se vemos que nossa cena de cima para baixo, nosso `CreateTranslation` método (isoladamente) executa o seguinte:

![](part3-images/image6.png "O método CreateTranslation em isolamento para executar esta ação")

A segunda matriz que criamos é uma matriz de rotação usando o `CreateRotationZ` matriz. Este é um dos três métodos que podem ser usados para criar a rotação:

- `CreateRotationX`
- `CreateRoationY`
- `CreateRotationZ`

Cada método cria uma matriz de rotação, girando sobre um eixo especificado. Em nosso caso, nós está girando sobre o eixo Z, que aponta "backup". A seguir pode ajudar a visualizar como baseado no eixo de rotação funciona:

![](part3-images/image7.png "Isso pode ajudar a visualizar como baseado no eixo de rotação funciona")

Também estamos usando o `CreateRotationZ` método com o campo de ângulo, que é incrementado ao longo do tempo devido ao nosso `Update` método ser chamado. O resultado é que o `CreateRotationZ` método faz com que a nossa robô girar em torno de origem com o passar do tempo.

A linha final do código combina duas matrizes em um:

```csharp
Matrix combined = translationMatrix * rotationMatrix;
```

Isso é chamado de multiplicação de matriz, que funciona um pouco diferente de multiplicação regular. O *Propriedade Comutativa da multiplicação* indica que a ordem dos números em uma operação de multiplicação não altera o resultado. Ou seja, 3 * 4 é equivalente a 4 * 3. Multiplicação de matrizes é diferente porque não está comutativa. Ou seja, a linha acima pode ser lido como "Aplicar translationMatrix para mover o modelo e girar tudo, aplicando o rotationMatrix". Podemos pode visualizar o modo como a linha acima afeta a posição e a rotação da seguinte maneira:

![](part3-images/image8.png "Pf uma visualização da forma que a linha acima afeta a posição e a rotação")

Para ajudar a entender como a ordem de multiplicação de matriz pode afetar o resultado, considere o seguinte, onde a multiplicação de matriz é invertida:

```csharp
Matrix combined = rotationMatrix * translationMatrix;
```

O código acima seria primeiro girar o modelo no lugar, traduzi-lo:

![](part3-images/image9.png "O código acima seria primeiro girar o modelo no lugar, em seguida, traduzi-lo")

Se executarmos o código com a multiplicação invertida, você notará que desde que a rotação aplica-se pela primeira vez, ele afeta somente a orientação do modelo e a posição do modelo permaneça o mesmo. Em outras palavras, o modelo gira em vigor:

![](part3-images/image10.gif "Gira o modelo no local")

## <a name="creating-the-camera-entity"></a>Criar a entidade de câmera

O `Camera` entidade contém toda a lógica necessária para executar a movimentação de entrada e fornecem propriedades para a atribuição de propriedades no `BasicEffect` classe.

Primeiro vamos implementar uma câmera estática (nenhuma movimentação de entrada com base em) e integrá-lo em nosso projeto existente. Adicionar uma nova classe para o **MonoGame3D** biblioteca de classes portátil (o mesmo projeto com `Robot.cs`) e nomeie-o **câmera**. Substitua o conteúdo do arquivo pelo seguinte código:

```csharp
using System;
using Microsoft.Xna.Framework;
using Microsoft.Xna.Framework.Graphics;

namespace MonoGame3D
{
    public class Camera
    {
        // We need this to calculate the aspectRatio
        // in the ProjectionMatrix property.
        GraphicsDevice graphicsDevice;

        Vector3 position = new Vector3(15, 10, 10);

        public Matrix ViewMatrix
        {
            get
            {
                var lookAtVector = Vector3.Zero;
                var upVector = Vector3.UnitZ;

                return Matrix.CreateLookAt (
                    position, lookAtVector, upVector);
            }
        }

        public Matrix ProjectionMatrix
        {
            get
            {
                float fieldOfView = Microsoft.Xna.Framework.MathHelper.PiOver4;
                float nearClipPlane = 1;
                float farClipPlane = 200;
                float aspectRatio = graphicsDevice.Viewport.Width / (float)graphicsDevice.Viewport.Height;

                return Matrix.CreatePerspectiveFieldOfView(
                    fieldOfView, aspectRatio, nearClipPlane, farClipPlane);
            }
        }

        public Camera(GraphicsDevice graphicsDevice)
        {
            this.graphicsDevice = graphicsDevice;
        }

        public void Update(GameTime gameTime)
        {
            // We'll be doing some input-based movement here
        }
    }
}
```

O código acima é muito semelhante ao código do `Game1` e `Robot` que atribuir as matrizes em `BasicEffect`. 

Agora é possível integrar o novo `Camera` classe em nossos projetos existentes. Primeiro, modificaremos a `Robot` classe tenham um `Camera` instância em seu `Draw` método, o que eliminará muitos códigos duplicados. Substitua o `Robot.Draw` método com o seguinte:

```csharp
public void Draw(Camera camera)
{
    foreach (var mesh in model.Meshes)
    {
        foreach (BasicEffect effect in mesh.Effects)
        {
            effect.EnableDefaultLighting ();
            effect.PreferPerPixelLighting = true;

            effect.World = GetWorldMatrix();
            effect.View = camera.ViewMatrix;
            effect.Projection = camera.ProjectionMatrix;
        }

        mesh.Draw ();
    }
}
```

Em seguida, modifique o `Game1.cs` arquivo:

```csharp
using Microsoft.Xna.Framework;
using Microsoft.Xna.Framework.Graphics;

namespace MonoGame3D
{
    public class Game1 : Game
    {
        GraphicsDeviceManager graphics;

        VertexPositionNormalTexture[] floorVerts;

        BasicEffect effect;

        Texture2D checkerboardTexture;

        // New camera code
        Camera camera;

        Robot robot;

        public Game1()
        {
            graphics = new GraphicsDeviceManager(this);
            graphics.IsFullScreen = true;

            Content.RootDirectory = "Content";
        }

        protected override void Initialize ()
        {
            floorVerts = new VertexPositionNormalTexture[6];

            floorVerts [0].Position = new Vector3 (-20, -20, 0);
            floorVerts [1].Position = new Vector3 (-20,  20, 0);
            floorVerts [2].Position = new Vector3 ( 20, -20, 0);

            floorVerts [3].Position = floorVerts[1].Position;
            floorVerts [4].Position = new Vector3 ( 20,  20, 0);
            floorVerts [5].Position = floorVerts[2].Position;

            int repetitions = 20;

            floorVerts [0].TextureCoordinate = new Vector2 (0, 0);
            floorVerts [1].TextureCoordinate = new Vector2 (0, repetitions);
            floorVerts [2].TextureCoordinate = new Vector2 (repetitions, 0);

            floorVerts [3].TextureCoordinate = floorVerts[1].TextureCoordinate;
            floorVerts [4].TextureCoordinate = new Vector2 (repetitions, repetitions);
            floorVerts [5].TextureCoordinate = floorVerts[2].TextureCoordinate;

            effect = new BasicEffect (graphics.GraphicsDevice);

            robot = new Robot ();
            robot.Initialize (Content);

            // New camera code
            camera = new Camera (graphics.GraphicsDevice);

            base.Initialize ();
        }

        protected override void LoadContent()
        {
            using (var stream = TitleContainer.OpenStream ("Content/checkerboard.png"))
            {
                checkerboardTexture = Texture2D.FromStream (this.GraphicsDevice, stream);
            }
        }

        protected override void Update(GameTime gameTime)
        {
            robot.Update (gameTime);
            // New camera code
            camera.Update (gameTime);
            base.Update(gameTime);
        }

        protected override void Draw(GameTime gameTime)
        {
            GraphicsDevice.Clear(Color.CornflowerBlue);

            DrawGround ();

            // New camera code
            robot.Draw (camera);

            base.Draw(gameTime);
        }

        void DrawGround()
        {
            // New camera code
            effect.View = camera.ViewMatrix;
            effect.Projection = camera.ProjectionMatrix;

            effect.TextureEnabled = true;
            effect.Texture = checkerboardTexture;

            foreach (var pass in effect.CurrentTechnique.Passes)
            {
                pass.Apply ();

                graphics.GraphicsDevice.DrawUserPrimitives (
                            PrimitiveType.TriangleList,
                    floorVerts,
                    0,
                    2);
            }
        }
    }
}
```

As modificações de `Game1` da versão anterior (que é identificado com `// New camera code` ) são:

- `Camera` Campo `Game1`
- `Camera` instanciação em `Game1.Initialize`
- `Camera.Update` chamada em `Game1.Update`
- `Robot.Draw` agora entra em um `Camera` parâmetro
- `Game1.Draw` agora usa `Camera.ViewMatrix` e `Camera.ProjectionMatrix`

## <a name="moving-the-camera-with-input"></a>Mover a câmera com entrada

Até agora, adicionamos uma `Camera` entidade, mas ainda não fez nada com a mudança de comportamento de tempo de execução. Vamos adicionar comportamento que permite ao usuário para:

- Tocar o lado esquerdo da tela para ativar a câmera para a esquerda
- Tocar o lado direito da tela para ativar a câmera à direita
- O centro da tela para Avançar a câmera de toque

### <a name="making-lookat-relative"></a>Tornando lookAt relativo

Primeiro vamos atualizar o `Camera` classe para incluir um `angle` campo que será usado para definir a direção que a `Camera` está enfrentando. No momento, nossa `Camera` determina a direção em que ele é voltado para o local `lookAtVector`, que é atribuída ao `Vector3.Zero`. Em outras palavras, nosso `Camera` sempre procura na origem. Se a câmera for movido, o ângulo que a câmera está enfrentando também será alterada:

![](part3-images/image11.gif "Se a câmera move, em seguida, o ângulo que a câmera está enfrentando também será alterada")

Queremos que o `Camera` para ser voltados para a mesma direção, independentemente de sua posição – pelo menos até que implemente a lógica para girar o `Camera` usando a entrada. A primeira alteração será ajustar o `lookAtVector` variável ser baseado em nosso local atual, em vez de aparência em uma posição absoluta:

```csharp
public class Camera
{
    GraphicsDevice graphicsDevice;

    // Let's start at X = 0 so we're looking at things head-on
    Vector3 position = new Vector3(0, 20, 10);

    public Matrix ViewMatrix
    {
        get
        {
            var lookAtVector = new Vector3 (0, -1, -.5f);
            lookAtVector += position;

            var upVector = Vector3.UnitZ;

            return  Matrix.CreateLookAt (
                position, lookAtVector, upVector);
        }
    }
    ...
```

Isso resulta no `Camera` exibir o mundo diretamente no. Observe que o inicial `position` valor foi modificado para `(0, 20, 10)` as `Camera` é centralizada no eixo X. Executando o jogo exibe:

![](part3-images/image12.png "Executando o jogo exibe este modo de exibição")

### <a name="creating-an-angle-variable"></a>Criando um variável do ângulo

O `lookAtVector` variável controla o ângulo que nosso câmera está exibindo. No momento é fixa para exibir para baixo do eixo Y negativo e inclinada um pouco para baixo (da `-.5f` valor Z). Vamos criar um `angle` variável que será usado para ajustar o `lookAtVector` propriedade. 

Nas seções anteriores deste passo a passo, mostramos que matrizes podem ser usadas para girar como os objetos são desenhados. Podemos também usar matrizes para girar vetores como o `lookAtVector` usando o `Vector3.Transform` método. 

Adicionar uma `angle` campo e modificar o `ViewMatrix` propriedade da seguinte maneira:

```csharp
public class Camera
{
    GraphicsDevice graphicsDevice;

    Vector3 position = new Vector3(0, 20, 10);

    float angle;

    public Matrix ViewMatrix
    {
        get
        {
            var lookAtVector = new Vector3 (0, -1, -.5f);
            // We'll create a rotation matrix using our angle
            var rotationMatrix = Matrix.CreateRotationZ (angle);
            // Then we'll modify the vector using this matrix:
            lookAtVector = Vector3.Transform (lookAtVector, rotationMatrix);
            lookAtVector += position;

            var upVector = Vector3.UnitZ;

            return  Matrix.CreateLookAt (
                position, lookAtVector, upVector);
        }
    }
    ...
```

### <a name="reading-input"></a>Entrada de leitura

Nosso `Camera` entidade agora pode ser totalmente controlada pela sua posição e variáveis de ângulo – precisamos apenas para alterá-los de acordo com a entrada.

Primeiro, obterá o `TouchPanel` estado para localizar onde o usuário é tocar na tela. Para obter mais informações sobre como usar o `TouchPanel` de classe, consulte [a referência de API TouchPanel](http://www.monogame.net/documentation/?page=T_Microsoft_Xna_Framework_Input_Touch_TouchPanel).

Se o usuário estiver tocando na terceira esquerda e em seguida, poderá ser ajustado o `angle` valor as `Camera` gira à esquerda, e se o usuário estiver tocando na terceira direita, podemos girar de outra forma. Se o usuário estiver tocando no terceiro intermediário da tela, em seguida, vamos examinar o `Camera` para frente.

Primeiro, adicione um usando a instrução para qualificar o `TouchPanel` e `TouchCollection` classes em `Camera.cs`:

```csharp
using Microsoft.Xna.Framework.Input.Touch; 
```

Em seguida, modifique o `Update` método para ler o painel de toque e ajustar o `angle` e `position` variáveis adequadamente:

```csharp
public void Update(GameTime gameTime)
{
    TouchCollection touchCollection = TouchPanel.GetState();

    bool isTouchingScreen = touchCollection.Count > 0;
    if (isTouchingScreen)
    {
        var xPosition = touchCollection [0].Position.X;

        float xRatio = xPosition / (float)graphicsDevice.Viewport.Width;

        if (xRatio < 1 / 3.0f)
        {
            angle += (float)gameTime.ElapsedGameTime.TotalSeconds;
        }
        else if (xRatio < 2 / 3.0f)
        {
            var forwardVector = new Vector3 (0, -1, 0);

            var rotationMatrix = Matrix.CreateRotationZ (angle);
            forwardVector = Vector3.Transform (forwardVector, rotationMatrix);

            const float unitsPerSecond = 3;

            this.position += forwardVector * unitsPerSecond *
                (float)gameTime.ElapsedGameTime.TotalSeconds ;
        }
        else
        {
            angle -= (float)gameTime.ElapsedGameTime.TotalSeconds;
        }
    }
}
```

Agora o `Camera` responderá para entrada por toque:

![](part3-images/image1.gif "Agora a câmera responderá para entrada por toque")

O método de atualização começa chamando `TouchPanel.GetState`, que retorna uma coleção de ajustes. Embora `TouchPanel.GetState` pode retornar vários pontos de toque, podemos preocupe somente o primeiro para manter a simplicidade.

Se o usuário é tocar na tela, em seguida, o código verifica se o primeiro toque está à esquerda, meio ou terceiro à direita da tela. Os terços esquerdos e direito girar a câmera aumentando ou diminuindo o `angle` variável de acordo com o `TotalSeconds` valor (de forma que o jogo se comporta da mesma maneira, independentemente da taxa de quadros).

Se o usuário estiver tocando terceira o centro da tela, em seguida, a câmera será movido para frente. Isso é feito primeiro obtendo o vetor de encaminhamento, que é inicialmente definido como apontando para o eixo Y negativo, em seguida, girado em uma matriz criada usando `Matrix.CreateRotationZ` e `angle` valor. Por fim o `forwardVector` é aplicado a `position` usando o `unitsPerSecond` coeficiente.

## <a name="summary"></a>Resumo

Este passo a passo mostra como mover e girar `Models` em 3D espaço usando `Matrices` e `BasicEffect.World` propriedade. Essa forma de movimentação fornece a base para mover objetos em jogos 3D. Este passo a passo também aborda como implementar um `Camera` entidade para exibir o mundo de qualquer posição e ângulo.

## <a name="related-links"></a>Links relacionados

- [Link de API MonoGame](http://www.monogame.net/documentation/?page=api)
- [Projeto concluído (exemplo)](https://developer.xamarin.com/samples/monodroid/MonoGame3DCamera/)
