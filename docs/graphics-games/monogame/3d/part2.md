---
title: "Desenho de gráficos 3D com vértices no MonoGame"
description: "MonoGame dá suporte ao uso de matrizes de vértices para definir como um objeto 3D é renderizado em uma base por ponto. Os usuários podem tirar proveito de matrizes de vértice para criar geometria dinâmica, implementar efeitos especiais e melhorar a eficiência de seu processamento por meio de remoção."
ms.topic: article
ms.prod: xamarin
ms.assetid: 932AF5C2-884D-46E1-9455-4C359FD7C092
ms.technology: xamarin-cross-platform
author: charlespetzold
ms.author: chape
ms.date: 03/28/2017
ms.openlocfilehash: 1b139e460a4841f2174df166bf50bb276802d8d3
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="drawing-3d-graphics-with-vertices-in-monogame"></a>Desenho de gráficos 3D com vértices no MonoGame

_MonoGame dá suporte ao uso de matrizes de vértices para definir como um objeto 3D é renderizado em uma base por ponto. Os usuários podem tirar proveito de matrizes de vértice para criar geometria dinâmica, implementar efeitos especiais e melhorar a eficiência de seu processamento por meio de remoção._

Os usuários que têm leia o [guia nos modelos de renderização](~/graphics-games/monogame/3d/part1.md) estarão familiarizados com a renderização de um modelo 3D em MonoGame. O `Model` classe é uma maneira eficiente para renderizar elementos gráficos 3D ao trabalhar com dados definidos em um arquivo (como. fbx) e ao lidar com dados estáticos. Alguns jogos requerem geometria 3D para ser definido ou manipulado dinamicamente em tempo de execução. Nesses casos, podemos usar matrizes de *vértices* para definir e renderizar geometry. Um vértice é um termo geral para um ponto no espaço 3D que faz parte de uma lista ordenada usada para definir a geometria. Normalmente os vértices são ordenados de forma a definir uma série de triângulos.

Para ajudar a visualizar como os vértices são usados para criar objetos 3D, vamos considerar o círculo a seguir:

![](part2-images/image1.png "Para ajudar a visualizar como os vértices são usados para criar objetos 3D, considere este esfera")

Como mostrado acima, o círculo é claramente composto por vários triângulos. Delineado da esfera para ver como os vértices se conectam a triângulos do formulário pode ser exibido:

![](part2-images/image2.png "Exibir wireframe da esfera para ver como os vértices se conectam a triângulos de formulário")

Este passo a passo abordará os seguintes tópicos:

 - Criar um projeto
 - Criando os vértices
 - Adicionar código de desenho
 - Renderizar com uma textura
 - Modificando as coordenadas de textura
 - Vértices com modelos de renderização

O projeto concluído conterá um andar Quadriculado que será desenhado usando uma matriz de vértice:

![](part2-images/image3.png "O projeto concluído conterá um andar Quadriculado que será desenhado usando uma matriz de vértice")


# <a name="creating-a-project"></a>Criando um projeto

Primeiro, vamos irá baixar um projeto que servirá como nosso ponto de partida. Vamos usar o projeto de modelo [que podem ser encontrado aqui](https://developer.xamarin.com/samples/mobile/ModelRenderingMG/).

Depois de baixado e descompactado, abrir e executar o projeto. Esperamos que seis modelos robô desenhados na tela:

![](part2-images/image4.png "Seis modelos robô desenhados na tela")

No final deste projeto será combinando o nosso próprio processamento de vértice personalizada com o robô `Model`, portanto, não vamos para excluir o código de renderização robô. Em vez disso, podemos vou apenas limpar o `Game1.Draw` chamada para remover o desenho de 6 robôs por enquanto. Para fazer isso, abra o **Game1.cs** de arquivo e localize o `Draw` método. Modificá-lo para que ele contém o código a seguir:


```csharp
protected override void Draw(GameTime gameTime)
{
    GraphicsDevice.Clear(Color.CornflowerBlue);
    base.Draw(gameTime);
}
```

Isso resultará em nosso jogo exibindo uma tela azul vazia:

![](part2-images/image5.png "Isso resultará em jogo exibindo uma tela azul vazia")


# <a name="creating-the-vertices"></a>Criando os vértices

Vamos criar uma matriz de vértices para definir nossos geometria. Neste passo a passo, estamos criará um plano 3D (um quadrado em espaço 3D, não um avião). Embora nosso plano tem quatro lados e quatro cantos, ele será composto de dois triângulos, cada um deles requer três vértices. Portanto, podemos definirá um total de seis pontos.

Até agora conversamos sobre vértices de forma geral, mas MonoGame fornece algumas estruturas padrão que podem ser usadas para vértices:

 - `Microsoft.Xna.Framework.Graphics.VertexPositionColor`
 - `Microsoft.Xna.Framework.Graphics.VertexPositionColorTexture`
 - `Microsoft.Xna.Framework.Graphics.VertexPositionNormalTexture`
 - `Microsoft.Xna.Framework.Graphics.VertexPositionTexture`

Nome do tipo de cada indica os componentes que ele contém. Por exemplo, `VertexPositionColor` contém valores de posição e cor. Vamos examinar cada um dos componentes:

 - Posição – todos os tipos de vértice incluir um `Position` componente. O `Position` valores definem onde o vértice está localizado no espaço 3D (X, Y e Z).
 - Cor – vértices podem especificar opcionalmente um `Color` valor para executar um tonalizar personalizado.
 - Normal – normais definem como a superfície do objeto está enfrentando. Normais são necessárias que se um objeto com iluminação desde a direção de processamento uma superfície está enfrentando impactos quanto leves que recebe. Normais geralmente são especificados como um *vetor unidade* – um vetor 3D que tem um comprimento de 1.
 - Textura – textura refere-se para as coordenadas de textura – ou seja, a parte de uma textura deve aparecer em um determinada vértice. Valores de textura serão necessários se a renderização de um objeto 3D com uma textura. As coordenadas de textura são coordenadas normalizadas, o que significa que valores cairá entre 0 e 1. Abordaremos as coordenadas de textura em mais detalhes posteriormente neste guia.

Nosso plano servirá como floor e queremos aplicar uma textura ao executar o nosso processamento, então utilizaremos o `VertexPositionTexture` tipo para definir os vértices.

Primeiro, adicionaremos um membro à nossa classe Game1:


```csharp
VertexPositionTexture[] floorVerts; 
```

Em seguida, defina os vértices em `Game1.Initialize`. Observe que o modelo fornecido mencionado anteriormente neste artigo não contém um `Game1.Initialize` método, portanto, precisamos adicionar todo o método para `Game1`:


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


# <a name="adding-drawing-code"></a>Adicionando o código de desenho

Agora que temos as posições para nosso geometry definido, podemos escrever o código de renderização.

Primeiro, precisamos definir um `BasicEffect` instância que contém parâmetros de renderização, como posição e iluminação. Para fazer isso, adicione um `BasicEffect` membro para o `Game1` classe abaixo onde o `floorVerts` campo está definido:


```csharp
...
VertexPositionTexture[] floorVerts;
// new code:
BasicEffect effect; 
```

Em seguida, modifique o `Initialize` método para definir o efeito:


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
    // new code:
    effect = new BasicEffect (graphics.GraphicsDevice);

    base.Initialize ();
} 
```

Agora podemos adicionar código para executar o desenho:


```csharp
void DrawGround()
{
    // The assignment of effect.View and effect.Projection
    // are nearly identical to the code in the Model drawing code.
    var cameraPosition = new Vector3 (0, 40, 20);
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


    foreach (var pass in effect.CurrentTechnique.Passes)
    {
        pass.Apply ();

        graphics.GraphicsDevice.DrawUserPrimitives (
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

![](part2-images/image7.png "O aplicativo será exibido isso quando executado")

Vamos examinar alguns dos detalhes no código acima.


## <a name="view-and-projection-properties"></a>Modo de exibição e as propriedades de projeção

O `View` e `Projection` propriedades controlam como vemos a cena. Podemos estará modificando esse código mais tarde quando podemos adicionar novamente o código de processamento do modelo. Especificamente, `View` controla o local e a orientação da câmera, e `Projection` controles de *campo de visão* (que pode ser usado para ampliar a câmera).


## <a name="techniques-and-passes"></a>Técnicas e passa

Uma vez atribuímos propriedades em nosso efeito que podemos executar o processamento real. 

Nós não será alterada de `CurrentTechnique` propriedade este passo a passo, mas mais avançados jogos pode ter um único efeito que pode executar o desenho de maneiras diferentes (por exemplo, como o valor de cor é aplicado). Cada um desses modos de renderização pode ser representada como uma técnica que pode ser atribuída antes da renderização. Além disso, cada técnica pode exigir várias passagens para processar corretamente. Efeitos talvez precise várias passagens se visuais complexas, como uma superfície brilhante ou pelo de renderização.

O mais importante para ter em mente é que o `foreach` loop permite que o código c# mesmo processar qualquer efeito, independentemente da complexidade dos subjacente `BasicEffect`.


## <a name="drawuserprimitives"></a>DrawUserPrimitives

`DrawUserPrimitives` é onde os vértices são renderizados. O primeiro parâmetro informa o método como, organizamos nossos vértices. Podemos ter estruturado-los de forma que cada triângulo é definido por três vértices ordenados, para que possamos usar o `PrimitiveType.TriangleList` valor.

O segundo parâmetro é a matriz de vértices que definimos anteriormente.

O terceiro parâmetro especifica o primeiro índice para desenhar. Como queremos que a matriz inteira de vértice para ser processado, podemos será passe um valor de 0.

Por fim, podemos especificar quantos triângulos para processar. Matriz vértice contém dois triângulos, então, passe um valor de 2.


# <a name="rendering-with-a-texture"></a>Renderizar com uma textura

Neste ponto, nosso aplicativo renderiza um plano branco (em perspectiva). Em seguida, adicionaremos uma textura ao nosso projeto a ser usado ao nosso plano de renderização. 

Para manter as coisas simples, adicionaremos o PNG diretamente ao nosso projeto em vez de usar a ferramenta MonoGame Pipeline. Para fazer isso, baixe [esse arquivo. PNG](https://github.com/xamarin/mobile-samples/blob/master/ModelRenderingMG/Resources/checkerboard.png?raw=true) em seu computador. Após o download, clique duas vezes no **conteúdo** pasta no painel de solução e selecione **Adicionar > Adicionar arquivos...**  . Se trabalhando no Android, em seguida, essa pasta estará localizada no **ativos** pasta no projeto específico do Android. Se estiver no iOS, em seguida, essa pasta será na raiz do projeto iOS. Navegue até o local onde **checkerboard.png** é salvo e selecione esse arquivo. Selecione para copiar o arquivo para o diretório.

Em seguida, vamos adicionar o código para criar nossa `Texture2D` instância. Primeiro, adicione o `Texture2D` como um membro de `Game1` sob o `BasicEffect` instância:


```csharp
...
BasicEffect effect;
// new code:
Texture2D checkerboardTexture;
```

Modificar `Game1.LoadContent` da seguinte maneira:


```csharp
protected override void LoadContent()
{
    // Notice that loading a model is very similar
    // to loading any other XNB (like a Texture2D).
    // The only difference is the generic type.
    model = Content.Load<Model> ("robot");

    // We aren't using the content pipeline, so we need
    // to access the stream directly:
    using (var stream = TitleContainer.OpenStream ("Content/checkerboard.png"))
    {
        checkerboardTexture = Texture2D.FromStream (this.GraphicsDevice, stream);
    }
} 
```

Em seguida, modifique o `DrawGround` método. A modificação somente necessário é atribuir `effect.TextureEnabled` para `true` e definir o `effect.Texture` para `checkerboardTexture`:


```csharp
void DrawGround()
{
    // The assignment of effect.View and effect.Projection
    // are nearly identical to the code in the Model drawing code.
    var cameraPosition = new Vector3 (0, 40, 20);
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

    // new code:
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
```

Por fim, é preciso modificar o `Game1.Initialize` método para atribuir também textura coordena em nosso vértices:


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

    // New code:
    floorVerts [0].TextureCoordinate = new Vector2 (0, 0);
    floorVerts [1].TextureCoordinate = new Vector2 (0, 1);
    floorVerts [2].TextureCoordinate = new Vector2 (1, 0);

    floorVerts [3].TextureCoordinate = floorVerts[1].TextureCoordinate;
    floorVerts [4].TextureCoordinate = new Vector2 (1, 1);
    floorVerts [5].TextureCoordinate = floorVerts[2].TextureCoordinate;

    effect = new BasicEffect (graphics.GraphicsDevice);

    base.Initialize ();
} 
```

Se executarmos o código, podemos ver que o nosso plano agora exibe um padrão Quadriculado:

![](part2-images/image8.png "O plano agora exibe um padrão quadriculado")


# <a name="modifying-texture-coordinates"></a>Modificando a textura coordenadas

Usa MonoGame normalizado coordenadas de textura, que são coordenadas entre 0 e 1, em vez de entre 0 e largura da textura ou altura. O diagrama a seguir pode ajudar a visualizar coordenadas normalizadas:

![](part2-images/image9.png "Este diagrama pode ajudar a visualizar coordenadas normalizadas")

As coordenadas de textura normalizado permitem o redimensionamento de textura sem a necessidade de reescrever o código ou recrie modelos (como arquivos. fbx). Isso é possível porque normalizadas coordenadas representam uma taxa, em vez de pixels específicos. Por exemplo, será (1,1) sempre representam o canto inferior direito, independentemente do tamanho da textura.

Podemos alterar a atribuição de coordenadas de textura para usar uma variável para o número de repetições:


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

    int repetitions = 20;

    floorVerts [0].TextureCoordinate = new Vector2 (0, 0);
    floorVerts [1].TextureCoordinate = new Vector2 (0, repetitions);
    floorVerts [2].TextureCoordinate = new Vector2 (repetitions, 0);

    floorVerts [3].TextureCoordinate = floorVerts[1].TextureCoordinate;
    floorVerts [4].TextureCoordinate = new Vector2 (repetitions, repetitions);
    floorVerts [5].TextureCoordinate = floorVerts[2].TextureCoordinate;

    effect = new BasicEffect (graphics.GraphicsDevice);

    base.Initialize ();
} 
```

Isso resulta na textura 20 vezes de repetição:

![](part2-images/image10.png "Isso resulta na textura 20 vezes de repetição")


# <a name="rendering-vertices-with-models"></a>Vértices com modelos de renderização

Agora que nosso plano estiver renderizando corretamente, podemos adicionar novamente os modelos para exibir tudo ao mesmo tempo. Primeiro, vamos novamente adicionar o código de modelo para nosso `Game1.Draw` método (com posições modificados):


```csharp
protected override void Draw(GameTime gameTime)
{
    GraphicsDevice.Clear(Color.CornflowerBlue);

    DrawGround ();

    DrawModel (new Vector3 (-4, 0, 3));
    DrawModel (new Vector3 ( 0, 0, 3));
    DrawModel (new Vector3 ( 4, 0, 3));

    DrawModel (new Vector3 (-4, 4, 3));
    DrawModel (new Vector3 ( 0, 4, 3));
    DrawModel (new Vector3 ( 4, 4, 3));

    base.Draw(gameTime);
} 
```

Vamos também criar um `Vector3` na `Game1` para representar a posição da câmera nossa. Vamos adicionar um campo em nosso `checkerboardTexture` declaração:


```csharp
...
Texture2D checkerboardTexture;
// new code:
Vector3 cameraPosition = new Vector3(0, 10, 10); 
```

Em seguida, remova o local `cameraPosition` variável do `DrawModel` método:


```csharp
void DrawModel(Vector3 modelPosition)
{
    foreach (var mesh in model.Meshes)
    {
        foreach (BasicEffect effect in mesh.Effects)
        {
            effect.EnableDefaultLighting ();
            effect.PreferPerPixelLighting = true;

            effect.World = Matrix.CreateTranslation (modelPosition);

            var cameraLookAtVector = Vector3.Zero;
            var cameraUpVector = Vector3.UnitZ;

            effect.View = Matrix.CreateLookAt (
                cameraPosition, cameraLookAtVector, cameraUpVector); 
            ...
```

Da mesma forma remove local `cameraPosition` variável do `DrawGround` método:


```csharp
void DrawGround()
{
    // The assignment of effect.View and effect.Projection
    // are nearly identical to the code in the Model drawing code.
    var cameraLookAtVector = Vector3.Zero;
    var cameraUpVector = Vector3.UnitZ;

    effect.View = Matrix.CreateLookAt (
        cameraPosition, cameraLookAtVector, cameraUpVector);
    ... 
```

Agora quando executamos o código podemos ver os modelos e zero ao mesmo tempo:

![](part2-images/image11.png "Os modelos e a base são exibidos ao mesmo tempo")

Se a posição da câmera (como aumentando o valor de X, que nesse caso move a câmera à esquerda), podemos ver que o valor afeta o início e os modelos:


```csharp
Vector3 cameraPosition = new Vector3(15, 10, 10);
```

Esse código resulta no seguinte:

![](part2-images/image3.png "Esse código resulta nesta exibição")


# <a name="summary"></a>Resumo

Este passo a passo mostrou como usar uma matriz de vértice para executar processamento personalizado. Nesse caso, criamos um andar Quadriculado combinando nosso processamento de vértice com uma textura e `BasicEffect`, mas o código apresentado aqui serve como base para qualquer renderização 3D. Também mostramos que o processamento de vértice com base pode ser combinado com os modelos da mesma cena.

## <a name="related-links"></a>Links relacionados

- [Arquivo de Quadriculado (exemplo)](https://github.com/xamarin/mobile-samples/blob/master/ModelRenderingMG/Resources/checkerboard.png?raw=true)
- [Projeto concluído (exemplo)](https://developer.xamarin.com/samples/mobile/ModelsAndVertsMG/)
