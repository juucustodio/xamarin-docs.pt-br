---
title: Coordenadas 3D em monojogo
description: Entender o sistema de coordenadas 3D é uma etapa importante no desenvolvimento de jogos 3D. O monogame fornece várias classes para posicionamento, orientação e dimensionamento de objetos no espaço 3D.
ms.prod: xamarin
ms.assetid: A4130995-48FD-4E2E-9C2B-ADCEFF35BE3A
author: conceptdev
ms.author: crdun
ms.date: 03/28/2017
ms.openlocfilehash: 2f5e66ae58f471be62839de17fe4edd12ccef457
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70763994"
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

![](part3-images/image1.gif "Depois de concluído, o aplicativo incluirá um projeto com um robô que se move em um círculo e uma câmera que pode ser controlada pela entrada por toque")

## <a name="creating-a-project"></a>Criando um projeto

Este tutorial explica como mover objetos no espaço 3D. Vamos começar com o projeto para renderizar modelos e matrizes de vértices [que podem ser encontrados aqui](https://docs.microsoft.com/samples/xamarin/mobile-samples/modelsandvertsmg/). Depois de baixado, descompacte e abra o projeto para verificar se ele é executado e devemos ver o seguinte:

![](part3-images/image2.png "Depois de baixado, descompacte e abra o projeto para certificar-se de que ele é executado e esta exibição deve ser exibida")

## <a name="creating-a-robot-entity"></a>Criando uma entidade de robô

Antes de começarmos a mover nosso robô, criaremos uma `Robot` classe para conter a lógica de desenho e movimento. Os desenvolvedores de jogos se referem a esse encapsulamento de lógica e dados como uma *entidade*.

Adicione um novo arquivo de classe vazio à biblioteca de classes portátil **MonoGame3D** (não o ModelAndVerts. Android específico da plataforma). Nomeie-o como **robô** e clique em **novo**:

![](part3-images/image3.png "Nomeie-o como robô e clique em novo")

Modifique a `Robot` classe da seguinte maneira:

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

O `Robot` código é essencialmente o mesmo código no `Game1` para desenhar um `Model`. Para uma revisão sobre `Model` como carregar e desenhar, consulte [este guia sobre como trabalhar com modelos](~/graphics-games/monogame/3d/part1.md). Agora podemos remover todo o `Model` código de carregamento e renderização de `Game1`e substituí-lo por uma `Robot` instância:

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

![](part3-images/image4.png "Se o código for executado agora, o aplicativo exibirá uma cena com apenas um robô, que é desenhado na maior parte do chão")

## <a name="moving-the-robot"></a>Movendo o robô

Agora que temos uma `Robot` classe, podemos adicionar lógica de movimentação ao robô. Nesse caso, simplesmente faremos a movimentação do robô em um círculo de acordo com o horário do jogo. Essa é uma implementação um pouco prática para um jogo real, uma vez que um caractere normalmente pode responder à entrada ou inteligência artificial, mas fornece um ambiente para explorar o posicionamento e a rotação 3D.

A única informação que precisaremos de fora da `Robot` classe é a hora atual do jogo. Vamos adicionar um `Update` método que usará um `GameTime` parâmetro. Esse `GameTime` parâmetro será usado para incrementar uma variável de ângulo que usaremos para determinar a posição final do robô.

Primeiro, adicionaremos o campo Angle à `Robot` classe sob o `model` campo:

```csharp
public class Robot
{
    public Model model;

    // new code:
    float angle;
    ...
```

 Agora, podemos incrementar esse valor em `Update` uma função:

```csharp
public void Update(GameTime gameTime)
{
    // TotalSeconds is a double so we need to cast to float
    angle += (float)gameTime.ElapsedGameTime.TotalSeconds;
}
```

Precisamos garantir que o `Update` método seja chamado de: `Game1.Update`

```csharp
protected override void Update(GameTime gameTime)
{
    robot.Update (gameTime);
    base.Update(gameTime);
}
```

É claro que neste ponto o campo de ângulo não faz nada – precisamos escrever código para usá-lo. Modificaremos o `Draw` método para que possamos calcular o mundo `Matrix` em um método dedicado: 

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

Em seguida, implementaremos o `GetWorldMatrix` método `Robot` na classe:

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

![](part3-images/image5.gif "Executar esse código resulta na movimentação do robô em um círculo")

## <a name="matrix-multiplication"></a>Multiplicação de matriz

O código acima gira o robô criando um `Matrix` `GetWorldMatrix` no método. A `Matrix` struct contém 16 valores float que podem ser usados para traduzir (definir posição), girar e dimensionar (definir tamanho). Quando atribuímos a `effect.World` Propriedade, estamos dizendo ao sistema de renderização subjacente como posicionar, dimensionar e orientar qualquer coisa que estejamos desenhando ( `Model` a ou geometria de vértices). 

Felizmente, a `Matrix` estrutura inclui vários métodos que simplificam a criação de tipos comuns de matrizes. O primeiro usado no código acima é `Matrix.CreateTranslation`. A *tradução* de termo matemático se refere a uma operação que resulta em um ponto (ou em nosso caso um modelo) mudando de um local para outro sem nenhuma outra modificação (como rotação ou redimensionamento). A função usa um valor X, Y e Z para a tradução. Se exibirmos nossa cena de cima para baixo, nosso `CreateTranslation` método (em isolamento) executará o seguinte:

![](part3-images/image6.png "O método createtranslation em isolamento executa essa ação")

A segunda matriz que criamos é uma matriz de rotação usando `CreateRotationZ` a matriz. Esse é um dos três métodos que podem ser usados para criar a rotação:

- `CreateRotationX`
- `CreateRoationY`
- `CreateRotationZ`

Cada método cria uma matriz de rotação girando sobre um determinado eixo. Em nosso caso, estamos girando sobre o eixo Z, que aponta para "up". O seguinte pode ajudar a visualizar como a rotação baseada em eixo funciona:

![](part3-images/image7.png "Isso pode ajudar a visualizar como funciona a rotação baseada em eixo")

Também estamos usando o método `CreateRotationZ` com o campo Angle, que é incrementado ao longo do tempo devido `Update` ao nosso método sendo chamado. O resultado é que o `CreateRotationZ` método faz com que nosso robô gire em volta da origem à medida que o tempo passa.

A linha final do código combina as duas matrizes em uma:

```csharp
Matrix combined = translationMatrix * rotationMatrix;
```

Isso é chamado de multiplicação de matriz, que funciona um pouco diferente do que a multiplicação regular. A *propriedade comutativa de multiplicação* indica que a ordem dos números em uma operação de multiplicação não altera o resultado. Ou seja, 3 * 4 é equivalente a 4 * 3. A multiplicação de matriz é diferente, pois não é comutador. Ou seja, a linha acima pode ser lida como "aplicar o translationMatrix para mover o modelo e, em seguida, girar tudo aplicando o rotationMatrix". Poderíamos Visualizar a maneira como a linha acima afeta a posição e a rotação da seguinte maneira:

![](part3-images/image8.png "Uma visualização PF da forma como a linha acima afeta a posição e a rotação")

Para ajudar a entender como a ordem de multiplicação de matriz pode afetar o resultado, considere o seguinte, em que a multiplicação de matriz é invertida:

```csharp
Matrix combined = rotationMatrix * translationMatrix;
```

O código acima primeiro giraria o modelo no local e, em seguida, o converteva:

![](part3-images/image9.png "O código acima primeiro giraria o modelo no local e, em seguida, o converte")

Se executarmos o código com a multiplicação invertida, notaremos que, como a rotação é aplicada primeiro, afetará apenas a orientação do modelo e a posição do modelo permanecerá a mesma. Em outras palavras, o modelo gira em vigor:

![](part3-images/image10.gif "O modelo gira em vigor")

## <a name="creating-the-camera-entity"></a>Criando a entidade de câmera

A `Camera` entidade conterá toda a lógica necessária para executar a movimentação baseada em entrada e para fornecer propriedades para a `BasicEffect` atribuição de propriedades na classe.

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

O código acima é muito semelhante ao código de `Game1` e `Robot` `BasicEffect`que atribui as matrizes. 

Agora, podemos integrar a nova `Camera` classe em nossos projetos existentes. Primeiro, modificaremos a `Robot` classe para pegar uma `Camera` instância em seu `Draw` método, o que eliminará muito código duplicado. Substitua o `Robot.Draw` método pelo seguinte:

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

Em seguida, modifique `Game1.cs` o arquivo:

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

As modificações `Game1` no da versão anterior (que são identificadas com `// New camera code` ) são:

- `Camera`campo em`Game1`
- `Camera`instanciação em`Game1.Initialize`
- `Camera.Update`chamada em`Game1.Update`
- `Robot.Draw`Agora usa um `Camera` parâmetro
- `Game1.Draw`Agora usa `Camera.ViewMatrix` e`Camera.ProjectionMatrix`

## <a name="moving-the-camera-with-input"></a>Movendo a câmera com entrada

Até agora, adicionamos uma entidade `Camera` , mas não fizemos nada com ela para alterar o comportamento do tempo de execução. Adicionaremos um comportamento que permite ao usuário:

- Toque no lado esquerdo da tela para transformar a câmera em direção à esquerda
- Toque no lado direito da tela para transformar a câmera à direita
- Toque no centro da tela para mover a câmera para frente

### <a name="making-lookat-relative"></a>Tornando o ver relativo

Primeiro, atualizaremos a `Camera` classe para incluir um `angle` campo que será usado para definir a direção que o `Camera` está voltado. Atualmente, nosso `Camera` determina a direção que ele está enfrentando por meio `lookAtVector`do local, que é `Vector3.Zero`atribuído a. Em outras palavras, nossa `Camera` sempre examina a origem. Se a câmera for movida, o ângulo que a câmera voltará também será alterado:

![](part3-images/image11.gif "Se a câmera for movida, o ângulo que a câmera voltará também será alterado")

Queremos que o `Camera` esteja voltado para a mesma direção, independentemente de sua posição – pelo menos até implementarmos a lógica para `Camera` girar o uso da entrada. A primeira alteração será ajustar a variável para `lookAtVector` que ela se baseie em nosso local atual, em vez de examinar uma posição absoluta:

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

Isso resulta na `Camera` exibição do mundo diretamente. Observe que o valor `position` inicial foi modificado para `(0, 20, 10)` que o `Camera` esteja centralizado no eixo X. A execução do jogo exibe:

![](part3-images/image12.png "A execução do jogo exibe esta exibição")

### <a name="creating-an-angle-variable"></a>Criando uma variável de ângulo

A `lookAtVector` variável controla o ângulo que a câmera está exibindo. Atualmente, ele é fixado para exibir o eixo Y negativo e ligeiramente inclinado para baixo (do `-.5f` valor Z). Criaremos uma `angle` variável que será usada para ajustar a `lookAtVector` propriedade. 

Nas seções anteriores deste passo a passos, mostramos que as matrizes podem ser usadas para girar a forma como os objetos são desenhados. Também podemos usar matrizes para girar vetores como o `lookAtVector` usando o `Vector3.Transform` método. 

Adicione um `angle` campo e modifique a `ViewMatrix` propriedade da seguinte maneira:

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

Nossa `Camera` entidade agora pode ser totalmente controlada por meio de suas variáveis de posição e ângulo – precisamos apenas alterá-las de acordo com a entrada.

Primeiro, obteremos o `TouchPanel` estado para descobrir onde o usuário está tocando na tela. Para obter mais informações sobre como `TouchPanel` usar a classe, consulte [a referência da API do TouchPanel](http://www.monogame.net/documentation/?page=T_Microsoft_Xna_Framework_Input_Touch_TouchPanel).

Se o usuário estiver tocando no terceiro lado esquerdo, vamos ajustar o `angle` valor para que as girações para a `Camera` esquerda e, se o usuário estiver tocando no terceiro, vamos girar da outra maneira. Se o usuário estiver tocando no meio do terceiro da tela, `Camera` avançaremos.

Primeiro, adicione uma instrução using para qualificar `TouchPanel` as `TouchCollection` classes e `Camera.cs`em:

```csharp
using Microsoft.Xna.Framework.Input.Touch; 
```

Em seguida, modifique `Update` o método para ler o painel de toque e `angle` ajuste `position` as variáveis e adequadamente:

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

Agora, `Camera` o responderá à entrada por toque:

![](part3-images/image1.gif "Agora, a câmera responderá à entrada por toque")

O método Update começa chamando `TouchPanel.GetState`, que retorna uma coleção de toques. Embora `TouchPanel.GetState` possa retornar vários pontos de toque, vamos nos preocupar apenas com o primeiro para simplificar.

Se o usuário estiver tocando na tela, o código verificará se o primeiro toque está no terceiro esquerdo, médio ou direito da tela. Os terços esquerdo e direito giram a câmera aumentando ou diminuindo a `angle` variável de acordo com `TotalSeconds` o valor (de modo que o jogo se comporta da mesma forma, independentemente da taxa de quadros).

Se o usuário estiver tocando no terceiro centro da tela, a câmera avançará. Isso é feito primeiro obtendo o vetor de encaminhamento, que é inicialmente definido como apontador para o eixo Y negativo e, em seguida, girado `Matrix.CreateRotationZ` por uma `angle` matriz criada usando e o valor. Por fim `forwardVector` , o é `position` aplicado ao `unitsPerSecond` uso do coeficiente.

## <a name="summary"></a>Resumo

Este tutorial explica como mover e girar `Models` no espaço 3D usando `Matrices` e a `BasicEffect.World` propriedade. Essa forma de movimentação fornece a base para mover objetos em jogos 3D. Este passo a passos também aborda como implementar `Camera` uma entidade para exibir o mundo de qualquer posição e ângulo.

## <a name="related-links"></a>Links relacionados

- [Link da API monogame](http://www.monogame.net/documentation/?page=api)
- [Projeto concluído (exemplo)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/monogame3dcamera)
