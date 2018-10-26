---
title: Desenho de gráficos 3D com vértices no MonoGame
description: MonoGame dá suporte ao uso de matrizes de vértices para definir como um objeto 3D é renderizado em uma base por ponto. Os usuários podem tirar proveito das matrizes de vértice para criar geometria dinâmica, implementar efeitos especiais e melhorar a eficiência de sua renderização por meio de remoção.
ms.prod: xamarin
ms.assetid: 932AF5C2-884D-46E1-9455-4C359FD7C092
author: conceptdev
ms.author: crdun
ms.date: 03/28/2017
ms.openlocfilehash: df36c149e98e8c0cbb16de4c2cf52def5713ec13
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50121432"
---
# <a name="drawing-3d-graphics-with-vertices-in-monogame"></a>Desenho de gráficos 3D com vértices no MonoGame

_MonoGame dá suporte ao uso de matrizes de vértices para definir como um objeto 3D é renderizado em uma base por ponto. Os usuários podem tirar proveito das matrizes de vértice para criar geometria dinâmica, implementar efeitos especiais e melhorar a eficiência de sua renderização por meio de remoção._

Os usuários que já tiverem lido por meio de [guia em modelos de renderização](~/graphics-games/monogame/3d/part1.md) estarão familiarizados com a renderização de um modelo 3D no MonoGame. O `Model` classe é uma maneira eficiente para renderizar elementos gráficos 3D ao trabalhar com dados definidos em um arquivo (por exemplo,. fbx) e ao lidar com dados estáticos. Alguns jogos requerem geometria 3D a ser definido ou manipulado dinamicamente em tempo de execução. Nesses casos, podemos usar matrizes de *vértices* para definir e renderizar a geometria. Um vértice é um termo geral para um ponto no espaço 3D que é parte de uma lista ordenada usada para definir a geometria. Normalmente os vértices são ordenados de forma de definir uma série de triângulos.

Para ajudar a visualizar como os vértices são usados para criar objetos 3D, vamos considerar a esfera a seguir:

![](part2-images/image1.png "Para ajudar a visualizar como os vértices são usados para criar objetos 3D, considere essa esfera")

Como mostrado acima, o círculo é claramente composto por vários triângulos. Podemos exibir um wireframe da esfera para ver como os vértices se conectam a triângulos de formulário:

![](part2-images/image2.png "Exibir um wireframe da esfera para ver como os vértices se conectam a triângulos de formulário")

Este passo a passo abordará os seguintes tópicos:

- Criar um projeto
- Criando os vértices
- Adicionando código de desenho
- Renderização com uma textura
- Modificando as coordenadas de textura
- Vértices com modelos de renderização

O projeto concluído conterá um piso Quadriculado que será desenhado usando uma matriz de vértice:

![](part2-images/image3.png "O projeto concluído conterá um piso Quadriculado que será desenhado usando uma matriz de vértice")

## <a name="creating-a-project"></a>Criando um projeto

Primeiro, vamos baixará um projeto que servirá como nosso ponto de partida. Vamos usar o projeto de modelo [que podem ser encontrado aqui](https://developer.xamarin.com/samples/mobile/ModelRenderingMG/).

Depois de baixado e descompactado, abra e execute o projeto. Esperamos que seis modelos de robô que está sendo desenhados na tela:

![](part2-images/image4.png "Seis modelos de robô que está sendo desenhados na tela")

No final deste projeto, vai combinando o nosso próprio processamento de vértice personalizado com o robô `Model`, portanto, não vamos excluir o código de renderização do robô. Em vez disso, podemos vou apenas limpar o `Game1.Draw` chamada para remover o desenho de 6 robôs por enquanto. Para fazer isso, abra o **Game1.cs** do arquivo e localize o `Draw` método. Modificá-lo para que ele contém o código a seguir:

```csharp
protected override void Draw(GameTime gameTime)
{
  GraphicsDevice.Clear(Color.CornflowerBlue);
  base.Draw(gameTime);
}
```

Isso resultará em nosso jogo exibindo uma tela azul vazia:

![](part2-images/image5.png "Isso resultará no jogo exibindo uma tela azul vazia")

## <a name="creating-the-vertices"></a>Criando os vértices

Vamos criar uma matriz de vértices para definir nossos geometria. Neste passo a passo, criaremos um plano 3D (um quadrado no espaço 3D, não um avião). Embora nosso plano tem quatro lados e quatro cantos, ele será ser composto de dois triângulos, cada um deles requer três vértices. Portanto, podemos definirá um total de seis pontos.

Até agora temos falado sobre vértices em um sentido geral, mas MonoGame fornece algumas estruturas padrão que podem ser usadas para vértices:

- `Microsoft.Xna.Framework.Graphics.VertexPositionColor`
- `Microsoft.Xna.Framework.Graphics.VertexPositionColorTexture`
- `Microsoft.Xna.Framework.Graphics.VertexPositionNormalTexture`
- `Microsoft.Xna.Framework.Graphics.VertexPositionTexture`

Nome do tipo de cada indica os componentes que ele contém. Por exemplo, `VertexPositionColor` contém valores de posição e cor. Vamos examinar cada um dos componentes:

- Posição – todos os tipos de vértice incluem um `Position` componente. O `Position` valores definem onde o vértice está localizado no espaço 3D (X, Y e Z).
- Cor – vértices podem opcionalmente especificar uma `Color` valor para executar a tonalidade personalizado.
- Normal – Normals definem como a superfície do objeto está enfrentando. Normais são necessárias que se um objeto com iluminação desde a direção de renderização uma superfície está voltada impactos quanta luz que recebe. Normals geralmente são especificados como uma *vetor de unidade* – um vetor 3D que tem um comprimento igual a 1.
- Textura – textura refere-se para as coordenadas de textura – ou seja, a parte de uma textura deve aparecer em um determinado vértice. Os valores de textura são necessários se a renderização de um objeto 3D com uma textura. As coordenadas de textura são coordenadas normalizadas, o que significa que valores serão ficam entre 0 e 1. Abordaremos as coordenadas de textura em mais detalhes mais adiante neste guia.

Nosso plano servirá como um piso, e queremos aplicar uma textura ao executar nosso renderização, então utilizaremos o `VertexPositionTexture` tipo para definir nossos vértices.

Em primeiro lugar, adicionaremos um membro à nossa classe Game1:

```csharp
VertexPositionTexture[] floorVerts; 
```

Em seguida, defina o nosso vértices em `Game1.Initialize`. Observe que o modelo fornecido mencionado anteriormente neste artigo não contém um `Game1.Initialize` método, portanto, precisamos adicionar todo o método para `Game1`:

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

Para ajudar a visualizar a aparência nosso vértices, considere o diagrama a seguir:

![](part2-images/image6.png "Para ajudar a visualizar a aparência de vértices, considere este diagrama")

Precisamos contar com nosso diagrama para visualizar os vértices até concluir a implementação de nosso código de renderização.

## <a name="adding-drawing-code"></a>Adicionar código de desenho

Agora que temos as posições para nosso geometry definido, podemos escrever nosso código de renderização.

Primeiro, precisamos definir um `BasicEffect` instância que irá conter parâmetros de renderização, como a posição e iluminação. Para fazer isso, adicione uma `BasicEffect` membro para o `Game1` classe abaixo de onde o `floorVerts` campo é definido:


```csharp
...
VertexPositionTexture[] floorVerts;
// new code:
BasicEffect effect;
```

Em seguida, modifique o `Initialize` método para definir o efeito:

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

Agora podemos adicionar código para executar o desenho:

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

Vamos precisar chamar `DrawGround` em nosso `Game1.Draw`:

```csharp
protected override void Draw (GameTime gameTime)
{
    GraphicsDevice.Clear (Color.CornflowerBlue);

    DrawGround ();

    base.Draw (gameTime);
}
```

O aplicativo exibirá o seguinte quando executado:

![](part2-images/image7.png "O aplicativo exiba esse quando executado")

Vamos examinar alguns dos detalhes no código acima.

### <a name="view-and-projection-properties"></a>Propriedades de projeção e exibição

O `View` e `Projection` propriedades de controle, como vemos a cena. Modificaremos este código mais tarde quando adicionamos o código de renderização de modelo novamente. Especificamente, `View` controla o local e a orientação da câmera, e `Projection` controles a *campo de visão* (que pode ser usado para aplicar zoom da câmera).

### <a name="techniques-and-passes"></a>Passagens e técnicas

Uma vez atribuímos propriedades no nosso efeito, que podemos executar o processamento real. 

Nós não alterará o `CurrentTechnique` propriedade neste passo a passo, mas jogos mais avançados pode ter um único efeito que pode executar o desenho de maneiras diferentes (por exemplo, como o valor de cor é aplicado). Cada um desses modos de renderização pode ser representada como uma técnica que pode ser atribuída antes da renderização. Além disso, cada técnica pode exigir várias passagens para renderizar corretamente. Efeitos talvez seja necessário várias passagens se visuais complexos como uma superfície brilhante ou fur de renderização.

O mais importante para ter em mente é que o `foreach` loop permite que o mesmo C# código para processar qualquer efeito independentemente da complexidade da subjacente `BasicEffect`.

### <a name="drawuserprimitives"></a>DrawUserPrimitives

`DrawUserPrimitives` é onde os vértices são renderizados. O primeiro parâmetro informa ao método como, organizamos nossos vértices. Podemos ter estruturado-los de forma que cada triângulo é definido por três vértices ordenadas, portanto, usamos o `PrimitiveType.TriangleList` valor.

O segundo parâmetro é a matriz de vértices que definimos anteriormente.

O terceiro parâmetro especifica o primeiro índice para desenhar. Como queremos que nossa matriz inteira de vértice a serem renderizados, vamos passar um valor de 0.

Por fim, podemos especificar quantos triângulos para renderizar. Nossa matriz de vértice contém dois triângulos, então, passe um valor de 2.

## <a name="rendering-with-a-texture"></a>Renderização com uma textura

Neste ponto, nosso aplicativo renderiza um plano branco (na perspectiva). Em seguida, adicionaremos uma textura ao nosso projeto a ser usado ao renderizar o nosso plano. 

Para manter as coisas simples, adicionaremos o PNG diretamente ao nosso projeto em vez de usar a ferramenta de Pipeline de MonoGame. Para fazer isso, baixe [esse arquivo. PNG](https://github.com/xamarin/mobile-samples/blob/master/ModelRenderingMG/Resources/checkerboard.png?raw=true) em seu computador. Após o download, clique com botão direito no **conteúdo** pasta no painel de soluções e selecione **Adicionar > Adicionar arquivos...**  . Se trabalhar no Android, em seguida, essa pasta estará localizada na **ativos** pasta no projeto do Android específica. Se, no iOS, em seguida, essa pasta será na raiz do projeto do iOS. Navegue até o local em que **checkerboard.png** é salvo e selecione esse arquivo. Selecione para copiar o arquivo para o diretório.

Em seguida, adicionaremos o código para criar nosso `Texture2D` instância. Primeiro, adicione a `Texture2D` como um membro de `Game1` sob o `BasicEffect` instância:

```csharp
...
BasicEffect effect;
// new code:
Texture2D checkerboardTexture;
```

Modificar `Game1.LoadContent` da seguinte maneira:


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

Em seguida, modifique o `DrawGround` método. A única modificação necessária é atribuir `effect.TextureEnabled` à `true` e definir o `effect.Texture` para `checkerboardTexture`:

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

Por fim, precisamos modificar o `Game1.Initialize` método também atribuir textura coordena a nossa vértices:


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

Se executarmos o código, podemos ver que o nosso plano agora exibe um padrão Quadriculado:

![](part2-images/image8.png "O plano agora exibe um padrão quadriculado")

## <a name="modifying-texture-coordinates"></a>Coordena a modificação de textura

MonoGame usa normalizados coordenadas de textura, que são coordenadas entre 0 e 1, em vez de entre 0 e a largura da textura ou a altura. O diagrama a seguir pode ajudar a visualizar coordenadas normalizadas:

![](part2-images/image9.png "Esse diagrama pode ajudar a visualizar coordenadas normalizadas")

As coordenadas de textura normalizado permitem o redimensionamento de textura sem a necessidade de reescrever o código ou recriar modelos (como arquivos. fbx). Isso é possível porque normalizadas coordenadas representam uma taxa, em vez de pixels específicos. Por exemplo, será (1,1) sempre representam o canto inferior direito, independentemente do tamanho da textura.

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

Isso resulta na textura 20 vezes de repetição:

![](part2-images/image10.png "Isso resulta na textura 20 vezes de repetição")


## <a name="rendering-vertices-with-models"></a>Vértices com modelos de renderização

Agora que nosso plano estiver renderizando corretamente, podemos adicionar novamente os modelos para exibir tudo juntos. Primeiro, novamente, adicionaremos o código de modelo ao nosso `Game1.Draw` método (com posições modificados):

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

Também criaremos uma `Vector3` em `Game1` para representar a posição da câmera, nossa. Vamos adicionar um campo em nosso `checkerboardTexture` declaração:

```csharp
...
Texture2D checkerboardTexture;
// new code:
Vector3 cameraPosition = new Vector3(0, 10, 10); 
```

Em seguida, remova o local `cameraPosition` variável do `DrawModel` método:

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

Da mesma forma, remover local `cameraPosition` variável do `DrawGround` método:

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

Agora se podemos executar o código podemos ver os modelos e zero ao mesmo tempo:

![](part2-images/image11.png "Os modelos e zero são exibidos ao mesmo tempo")

Se podemos modificar a posição da câmera (como, aumentando o valor de X, que nesse caso, move a câmera para a esquerda), podemos ver que o valor afeta o princípio e os modelos:

```csharp
Vector3 cameraPosition = new Vector3(15, 10, 10);
```

Esse código resulta no seguinte:

![](part2-images/image3.png "Esse código resulta neste modo de exibição")

## <a name="summary"></a>Resumo

Este passo a passo mostrou como usar uma matriz de vértice para realizar processamento personalizado. Nesse caso, criamos um piso Quadriculado combinando nosso processamento de vértice com uma textura e `BasicEffect`, mas o código apresentado aqui serve como base para qualquer processamento 3D. Também mostramos que o processamento de vértice com base pode ser misturado com modelos na cena mesmo.

## <a name="related-links"></a>Links relacionados

- [Arquivo de Quadriculado (amostra)](https://github.com/xamarin/mobile-samples/blob/master/ModelRenderingMG/Resources/checkerboard.png?raw=true)
- [Projeto concluído (amostra)](https://developer.xamarin.com/samples/mobile/ModelsAndVertsMG/)
