---
title: Desenho de gráficos 3D com vértices em monojogo
description: O monogame dá suporte ao uso de matrizes de vértices para definir como um objeto 3D é renderizado em uma base por ponto. Os usuários podem aproveitar as matrizes de vértice para criar uma geometria dinâmica, implementar efeitos especiais e melhorar a eficiência da renderização por meio de remoção.
ms.prod: xamarin
ms.assetid: 932AF5C2-884D-46E1-9455-4C359FD7C092
author: conceptdev
ms.author: crdun
ms.date: 03/28/2017
ms.openlocfilehash: 8bdef9bff975365172a4c215b21cbb07a37e8492
ms.sourcegitcommit: c9651cad80c2865bc628349d30e82721c01ddb4a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/03/2019
ms.locfileid: "70227729"
---
# <a name="drawing-3d-graphics-with-vertices-in-monogame"></a>Desenho de gráficos 3D com vértices em monojogo

_O monogame dá suporte ao uso de matrizes de vértices para definir como um objeto 3D é renderizado em uma base por ponto. Os usuários podem aproveitar as matrizes de vértice para criar uma geometria dinâmica, implementar efeitos especiais e melhorar a eficiência da renderização por meio de remoção._

Os usuários que tiverem lido o [guia sobre os modelos de renderização](~/graphics-games/monogame/3d/part1.md) estarão familiarizados com a renderização de um modelo 3D no monogame. A `Model` classe é uma maneira eficaz de renderizar gráficos 3D ao trabalhar com dados definidos em um arquivo (como. fbx) e ao lidar com dados estáticos. Alguns jogos exigem que a geometria 3D seja definida ou manipulada dinamicamente no tempo de execução. Nesses casos, podemos usar matrizes de *vértices* para definir e renderizar a geometria. Um vértice é um termo geral para um ponto no espaço 3D que faz parte de uma lista ordenada usada para definir a geometria. Normalmente, os vértices são ordenados de forma a definir uma série de triângulos.

Para ajudar a visualizar como os vértices são usados para criar objetos 3D, vamos considerar a seguinte esfera:

![](part2-images/image1.png "Para ajudar a visualizar como os vértices são usados para criar objetos 3D, considere esta esfera")

Como mostrado acima, a esfera é claramente composta por vários triângulos. Podemos exibir o wireframe da esfera para ver como os vértices se conectam aos triângulos do formulário:

![](part2-images/image2.png "Exiba o wireframe da esfera para ver como os vértices se conectam aos triângulos de formulário")

Este tutorial abordará os seguintes tópicos:

- Criar um projeto
- Criando os vértices
- Adicionando código de desenho
- Renderizando com uma textura
- Modificando coordenadas de textura
- Renderizando vértices com modelos

O projeto concluído conterá uma base quadriculada que será desenhada usando uma matriz de vértice:

![](part2-images/image3.png "O projeto concluído conterá uma base quadriculada que será desenhada usando uma matriz de vértice")

## <a name="creating-a-project"></a>Criando um projeto

Primeiro, baixaremos um projeto que servirá como nosso ponto de partida. Usaremos o projeto de modelo [que pode ser encontrado aqui](https://docs.microsoft.com/samples/xamarin/mobile-samples/modelrenderingmg/).

Depois de baixado e descompactado, abra e execute o projeto. Esperamos ver seis modelos de robô sendo desenhados na tela:

![](part2-images/image4.png "Seis modelos de robô sendo desenhados na tela")

Ao final deste projeto, vamos combinar nossa própria renderização de vértice personalizada com o robô `Model`, portanto, não vamos excluir o código de renderização de robô. Em vez disso, simplesmente desmarcaremos `Game1.Draw` a chamada para remover o desenho dos 6 robôs por enquanto. Para fazer isso, abra o arquivo **Game1.cs** e localize o `Draw` método. Modifique-o para que ele contenha o seguinte código:

```csharp
protected override void Draw(GameTime gameTime)
{
  GraphicsDevice.Clear(Color.CornflowerBlue);
  base.Draw(gameTime);
}
```

Isso fará com que nosso jogo exiba uma tela azul vazia:

![](part2-images/image5.png "Isso fará com que o jogo exiba uma tela azul vazia")

## <a name="creating-the-vertices"></a>Criando os vértices

Criaremos uma matriz de vértices para definir nossa geometria. Neste tutorial, vamos criar um plano 3D (um quadrado em espaço 3D, não um avião). Embora nosso plano tenha quatro lados e quatro cantos, ele será composto de dois triângulos, cada um dos quais requer três vértices. Portanto, iremos definir um total de seis pontos.

Até agora, falamos sobre os vértices de um sentido geral, mas o monogame fornece algumas estruturas padrão que podem ser usadas para vértices:

- `Microsoft.Xna.Framework.Graphics.VertexPositionColor`
- `Microsoft.Xna.Framework.Graphics.VertexPositionColorTexture`
- `Microsoft.Xna.Framework.Graphics.VertexPositionNormalTexture`
- `Microsoft.Xna.Framework.Graphics.VertexPositionTexture`

O nome de cada tipo indica os componentes que ele contém. Por exemplo, `VertexPositionColor` contém valores de posição e cor. Vamos examinar cada um dos componentes:

- Position – todos os tipos de vértice `Position` incluem um componente. Os `Position` valores definem onde o vértice está localizado no espaço 3D (X, Y e Z).
- Cor – os vértices podem, opcionalmente, especificar um `Color` valor para executar a tonalidade personalizada.
- Normal – os normais definem a maneira como a superfície do objeto está voltada. Os Normals são necessários para renderizar um objeto com iluminação, pois a direção que uma superfície está enfrentando afeta a quantidade de luz recebida. Os normais normalmente são especificados como um *vetor de unidade* – um vetor 3D que tem um comprimento de 1.
- Textura – textura refere-se a coordenadas de textura – ou seja, qual parte de uma textura deve aparecer em um determinado vértice. Os valores de textura são necessários ao renderizar um objeto 3D com uma textura. As coordenadas de textura são coordenadas normalizadas, o que significa que os valores ficarão entre 0 e 1. Abordaremos as coordenadas de textura em mais detalhes posteriormente neste guia.

Nosso plano servirá como um andar e queremos aplicar uma textura ao executar nossa renderização, portanto, usaremos o `VertexPositionTexture` tipo para definir nossos vértices.

Primeiro, vamos adicionar um membro à nossa classe Game1:

```csharp
VertexPositionTexture[] floorVerts;
```

Em seguida, defina nossos vértices em `Game1.Initialize`. Observe que o modelo fornecido mencionado anteriormente neste artigo não contém um `Game1.Initialize` método, portanto, precisamos adicionar o método inteiro a: `Game1`

```csharp
protected override void Initialize ()
{
    floorVerts = new VertexPositionTexture[6];
    floorVerts [0].Position = new Vector3 (-20, -20, 0);
    floorVerts [1].Position = new Vector3 (-20,  20, 0);
    floorVerts [2].Position = new Vector3 ( 20, -20, 0);
    floorVerts [3].Position = floorVerts[1].Position;
    floorVerts [4].Position = new Vector3 ( 20,  20, 0);
    floorVerts [5].Position = floorVerts[2].Position;
    // We’ll be assigning texture values later
    base.Initialize ();
}
```

Para ajudar a visualizar como serão os nossos vértices, considere o seguinte diagrama:

![](part2-images/image6.png "Para ajudar a visualizar a aparência dos vértices, considere este diagrama")

Precisamos contar com nosso diagrama para visualizar os vértices até que terminemos de implementar nosso código de renderização.

## <a name="adding-drawing-code"></a>Adicionando código de desenho

Agora que temos as posições para nossa geometria definida, podemos escrever nosso código de renderização.

Primeiro, precisaremos definir uma `BasicEffect` instância que conterá parâmetros para renderização, como posição e iluminação. Para fazer isso, adicione um `BasicEffect` membro `Game1` à classe abaixo, em que `floorVerts` o campo é definido:


```csharp
...
VertexPositionTexture[] floorVerts;
// new code:
BasicEffect effect;
```

Em seguida, modifique `Initialize` o método para definir o efeito:

```csharp
protected override void Initialize ()
{
    floorVerts = new VertexPositionTexture[6];

    floorVerts [0].Position = new Vector3 (-20, -20, 0);
    floorVerts [1].Position = new Vector3 (-20,  20, 0);
    floorVerts [2].Position = new Vector3 ( 20, -20, 0);

    floorVerts [3].Position = floorVerts[1].Position;
    floorVerts [4].Position = new Vector3 ( 20,  20, 0);
    floorVerts [5].Position = floorVerts[2].Position;
    // new code:
    effect = new BasicEffect (graphics.GraphicsDevice);

    base.Initialize ();
}
```

Agora, podemos adicionar código para executar o desenho:

```csharp
void DrawGround()
{
    // The assignment of effect.View and effect.Projection
    // are nearly identical to the code in the Model drawing code.
    var cameraPosition = new Vector3 (0, 40, 20);
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


    foreach (var pass in effect.CurrentTechnique.Passes)
    {
        pass.Apply ();

        graphics.GraphicsDevice.DrawUserPrimitives (
            // We’ll be rendering two trinalges
            PrimitiveType.TriangleList,
            // The array of verts that we want to render
            floorVerts,
            // The offset, which is 0 since we want to start
            // at the beginning of the floorVerts array
            0,
            // The number of triangles to draw
            2);
    }
}
```

Precisaremos chamar `DrawGround` em nosso `Game1.Draw`:

```csharp
protected override void Draw (GameTime gameTime)
{
    GraphicsDevice.Clear (Color.CornflowerBlue);

    DrawGround ();

    base.Draw (gameTime);
}
```

O aplicativo exibirá o seguinte quando for executado:

![](part2-images/image7.png "O aplicativo exibirá isso quando for executado")

Vejamos alguns dos detalhes no código acima.

### <a name="view-and-projection-properties"></a>Propriedades de exibição e projeção

As `View` propriedades `Projection` e controlam como exibimos a cena. Modificaremos esse código posteriormente quando adicionarmos novamente o código de renderização do modelo. Especificamente, `View` o controla o local e a orientação da câmera e `Projection` controla o *campo de exibição* (que pode ser usado para aplicar zoom na câmera).

### <a name="techniques-and-passes"></a>Técnicas e passagens

Depois de atribuirmos Propriedades ao nosso efeito, podemos executar a renderização real.

Não iremos alterar a `CurrentTechnique` Propriedade neste passo a passos, mas jogos mais avançados podem ter um único efeito que pode realizar desenhos de maneiras diferentes (por exemplo, como o valor de cor é aplicado). Cada um desses modos de renderização pode ser representado como uma técnica que pode ser atribuída antes da renderização. Além disso, cada técnica pode exigir que vários passos sejam processados corretamente. Os efeitos podem precisar de várias passagens se forem renderizando visuais complexos, como uma superfície brilhante ou pêlo.

A coisa importante a ser lembrada é que o `foreach` loop permite que o C# mesmo código processe qualquer efeito, independentemente da complexidade do subjacente `BasicEffect`.

### <a name="drawuserprimitives"></a>DrawUserPrimitives

`DrawUserPrimitives`é onde os vértices são renderizados. O primeiro parâmetro informa ao método como organizamos nossos vértices. Nós os estruturamos para que cada triângulo seja definido por três vértices ordenados, portanto, `PrimitiveType.TriangleList` usamos o valor.

O segundo parâmetro é a matriz de vértices que definimos anteriormente.

O terceiro parâmetro especifica o primeiro índice a ser desenhado. Como queremos que nossa matriz de vértice inteira seja renderizada, passaremos um valor de 0.

Por fim, especificamos a quantidade de triângulos a serem renderizados. Nossa matriz de vértices contém dois triângulos, portanto, passe um valor de 2.

## <a name="rendering-with-a-texture"></a>Renderizando com uma textura

Neste ponto, nosso aplicativo renderiza um plano branco (em perspectiva). Em seguida, vamos adicionar uma textura ao nosso projeto a ser usado ao renderizar nosso plano.

Para simplificar as coisas, adicionaremos o. png diretamente ao nosso projeto em vez de usar a ferramenta de pipeline de monojogo. Para fazer isso, baixe [esse arquivo. png](https://github.com/xamarin/mobile-samples/blob/master/ModelRenderingMG/Resources/checkerboard.png?raw=true) em seu computador. Depois de baixado, clique com o botão direito do mouse na pasta de **conteúdo** no painel de solução e selecione **Adicionar > Adicionar arquivos...** . Se estiver trabalhando no Android, essa pasta estará localizada na pasta **ativos** no projeto específico do Android. Se estiver no iOS, essa pasta estará na raiz do projeto do iOS. Navegue até o local onde o **xadrez. png** é salvo e selecione esse arquivo. Selecione para copiar o arquivo para o diretório.

Em seguida, adicionaremos o código para criar nossa `Texture2D` instância. Primeiro, adicione o `Texture2D` como um membro de `Game1` sob a `BasicEffect` instância:

```csharp
...
BasicEffect effect;
// new code:
Texture2D checkerboardTexture;
```

Modifique `Game1.LoadContent` da seguinte maneira:


```csharp
protected override void LoadContent()
{
    // Notice that loading a model is very similar
    // to loading any other XNB (like a Texture2D).
    // The only difference is the generic type.
    model = Content.Load<Model> ("robot");

    // We aren't using the content pipeline, so we need
    // to access the stream directly:
    using (var stream = TitleContainer.OpenStream ("Content/checkerboard.png"))
    {
        checkerboardTexture = Texture2D.FromStream (this.GraphicsDevice, stream);
    }
}
```

Em seguida, modifique `DrawGround` o método. A única `effect.TextureEnabled` modificação necessária é atribuir `true` a e para definir o `effect.Texture` para `checkerboardTexture`:

```csharp
void DrawGround()
{
    // The assignment of effect.View and effect.Projection
    // are nearly identical to the code in the Model drawing code.
    var cameraPosition = new Vector3 (0, 40, 20);
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

    // new code:
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
```

Por fim, precisamos modificar o `Game1.Initialize` método para também atribuir coordenadas de textura em nossos vértices:


```csharp
protected override void Initialize ()
{
    floorVerts = new VertexPositionTexture[6];

    floorVerts [0].Position = new Vector3 (-20, -20, 0);
    floorVerts [1].Position = new Vector3 (-20,  20, 0);
    floorVerts [2].Position = new Vector3 ( 20, -20, 0);

    floorVerts [3].Position = floorVerts[1].Position;
    floorVerts [4].Position = new Vector3 ( 20,  20, 0);
    floorVerts [5].Position = floorVerts[2].Position;

    // New code:
    floorVerts [0].TextureCoordinate = new Vector2 (0, 0);
    floorVerts [1].TextureCoordinate = new Vector2 (0, 1);
    floorVerts [2].TextureCoordinate = new Vector2 (1, 0);

    floorVerts [3].TextureCoordinate = floorVerts[1].TextureCoordinate;
    floorVerts [4].TextureCoordinate = new Vector2 (1, 1);
    floorVerts [5].TextureCoordinate = floorVerts[2].TextureCoordinate;

    effect = new BasicEffect (graphics.GraphicsDevice);

    base.Initialize ();
}
```

Se executarmos o código, podemos ver que nosso plano agora exibe um padrão quadriculado:

![](part2-images/image8.png "O plano agora exibe um padrão quadriculado")

## <a name="modifying-texture-coordinates"></a>Modificando coordenadas de textura

O monogame usa coordenadas de textura normalizadas, que são coordenadas entre 0 e 1 em vez de 0 e a largura ou altura da textura. O diagrama a seguir pode ajudar a visualizar as coordenadas normalizadas:

![](part2-images/image9.png "Este diagrama pode ajudar a visualizar as coordenadas normalizadas")

As coordenadas de textura normalizadas permitem o redimensionamento de textura sem a necessidade de reescrever código ou recriar modelos (como arquivos. fbx). Isso é possível porque as coordenadas normalizadas representam uma proporção em vez de pixels específicos. Por exemplo, (1, 1) sempre representará o canto inferior direito, independentemente do tamanho da textura.

Podemos alterar a atribuição de coordenadas de textura para usar uma única variável para o número de repetições:


```csharp
protected override void Initialize ()
{
    floorVerts = new VertexPositionTexture[6];

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

    base.Initialize ();
}
```

Isso resulta na repetição da textura 20 vezes:

![](part2-images/image10.png "Isso resulta na repetição da textura 20 vezes")


## <a name="rendering-vertices-with-models"></a>Renderizando vértices com modelos

Agora que nosso plano está sendo renderizado corretamente, podemos adicionar novamente os modelos para exibir tudo em conjunto. Primeiro, adicionaremos novamente o código do modelo ao nosso `Game1.Draw` método (com posições modificadas):

```csharp
protected override void Draw(GameTime gameTime)
{
    GraphicsDevice.Clear(Color.CornflowerBlue);

    DrawGround ();

    DrawModel (new Vector3 (-4, 0, 3));
    DrawModel (new Vector3 ( 0, 0, 3));
    DrawModel (new Vector3 ( 4, 0, 3));

    DrawModel (new Vector3 (-4, 4, 3));
    DrawModel (new Vector3 ( 0, 4, 3));
    DrawModel (new Vector3 ( 4, 4, 3));

    base.Draw(gameTime);
}
```

Também criaremos um `Vector3` no `Game1` para representar a posição de nossa câmera. Vamos adicionar um campo sob nossa `checkerboardTexture` declaração:

```csharp
...
Texture2D checkerboardTexture;
// new code:
Vector3 cameraPosition = new Vector3(0, 10, 10);
```

Em seguida, remova a `cameraPosition` variável local `DrawModel` do método:

```csharp
void DrawModel(Vector3 modelPosition)
{
    foreach (var mesh in model.Meshes)
    {
        foreach (BasicEffect effect in mesh.Effects)
        {
            effect.EnableDefaultLighting ();
            effect.PreferPerPixelLighting = true;

            effect.World = Matrix.CreateTranslation (modelPosition);

            var cameraLookAtVector = Vector3.Zero;
            var cameraUpVector = Vector3.UnitZ;

            effect.View = Matrix.CreateLookAt (
                cameraPosition, cameraLookAtVector, cameraUpVector);
            ...
```

Da mesma forma, `cameraPosition` remova a variável `DrawGround` local do método:

```csharp
void DrawGround()
{
    // The assignment of effect.View and effect.Projection
    // are nearly identical to the code in the Model drawing code.
    var cameraLookAtVector = Vector3.Zero;
    var cameraUpVector = Vector3.UnitZ;

    effect.View = Matrix.CreateLookAt (
        cameraPosition, cameraLookAtVector, cameraUpVector);
    ...
```

Agora, se executarmos o código, podemos ver ambos os modelos e o terra ao mesmo tempo:

![](part2-images/image11.png "Os modelos e o terra são exibidos ao mesmo tempo")

Se modificarmos a posição da câmera (por exemplo, aumentando seu valor X, que nesse caso move a câmera para a esquerda), podemos ver que o valor afeta o aterramento e os modelos:

```csharp
Vector3 cameraPosition = new Vector3(15, 10, 10);
```

Esse código resulta no seguinte:

![](part2-images/image3.png "Esse código resulta neste modo de exibição")

## <a name="summary"></a>Resumo

Este tutorial mostrou como usar uma matriz de vértice para executar a renderização personalizada. Nesse caso, criamos um piso quadriculado combinando nossa renderização baseada em vértice com uma textura e `BasicEffect`, mas o código apresentado aqui serve como base para qualquer renderização 3D. Também mostramos que a renderização baseada em vértice pode ser misturada com modelos na mesma cena.

## <a name="related-links"></a>Links relacionados

- [Arquivo de xadrez (exemplo)](https://github.com/xamarin/mobile-samples/blob/master/ModelRenderingMG/Resources/checkerboard.png?raw=true)
- [Projeto concluído (exemplo)](https://docs.microsoft.com/samples/xamarin/mobile-samples/modelsandvertsmg/)
