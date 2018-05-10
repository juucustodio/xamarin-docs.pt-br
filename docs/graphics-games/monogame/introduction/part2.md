---
title: Parte 2 – implementar o WalkingGame
description: Este passo a passo mostra como adicionar lógica de jogo e conteúdo a um projeto MonoGame vazio para criar uma demonstração de uma entidade de gráfica animada movendo com entrada por toque.
ms.prod: xamarin
ms.assetid: F0622A01-DE7F-451A-A51F-129876AB6FFD
author: charlespetzold
ms.author: chape
ms.date: 03/28/2017
ms.openlocfilehash: 87678d9d77f75bccc68a667d3fb0f35b641b937c
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/09/2018
---
# <a name="part-2--implementing-the-walkinggame"></a>Parte 2 – implementar o WalkingGame

_Este passo a passo mostra como adicionar lógica de jogo e conteúdo a um projeto MonoGame vazio para criar uma demonstração de uma entidade de gráfica animada movendo com entrada por toque._

As partes anteriores deste passo a passo mostraram como criar projetos de MonoGame vazios. Vamos criar essas partes anterior, fazendo uma demonstração de jogo simple. Este artigo contém as seguintes seções:

- Descompactar nosso conteúdo de jogo
- Visão geral da classe MonoGame
- Nossa primeira entidade gráfica de renderização
- Criando o CharacterEntity
- Adicionando CharacterEntity ao jogo
- Criando a classe de animação
- Adicionando a primeira animação para CharacterEntity
- Adicionando a movimentação para o caractere
- Animação e movimentação de correspondência


## <a name="unzipping-our-game-content"></a>Descompactar nosso conteúdo de jogo

Antes de começarmos a escrever código, queremos Descompacte nosso jogo *conteúdo*. O termo geralmente usado por desenvolvedores de jogos *conteúdo* para se referir a arquivos que não é de código que geralmente são criados pelo visual artistas, designers de jogos ou áudio designers. Tipos de conteúdo comuns incluem arquivos usados para exibir elementos visuais, tocar um som ou controlar o comportamento de inteligência artificial (AI). De um desenvolvimento de jogos o conteúdo do ponto de vista da equipe geralmente é criado por programadores não.

O conteúdo usado aqui pode ser encontrado [no github](https://github.com/xamarin/mobile-samples/blob/master/WalkingGameMG/Resources/charactersheet.png?raw=true). Vamos precisar desses arquivos baixados em um local que podemos acessarão posteriormente neste passo a passo.

## <a name="monogame-class-overview"></a>Visão geral da classe MonoGame

Para começar, exploraremos as classes de MonoGame usadas na renderização básica:

- `SpriteBatch` – usado para desenhar gráficos 2D para a tela. *Sprites* são 2D elementos visuais que são usados para exibir imagens na tela. O `SpriteBatch` objeto pode desenhar uma única entidade de gráfica em um momento entre seus `Begin` e `End` métodos ou vários sprites podem ser agrupados, ou *em lote*.
- `Texture2D` – representa um objeto de imagem em tempo de execução. `Texture2D` instâncias geralmente são criadas a partir de formatos de arquivo como. png ou. bmp, embora também podem ser criados dinamicamente em tempo de execução. `Texture2D` instâncias são usadas ao renderizar com `SpriteBatch` instâncias.
- `Vector2` – representa uma posição em um sistema de coordenadas 2D que é geralmente usado para posicionar objetos visuais. Também inclui MonoGame `Vector3` e `Vector4` , mas apenas usaremos `Vector2` neste passo a passo.
- `Rectangle` – uma área quatro lados com posição, largura e altura. Usaremos isso para definir a parte de nosso `Texture2D` a renderização quando podemos começar a trabalhar com animações.

Podemos também deve observar MonoGame não é mantido pela Microsoft – apesar de seu namespace. O `Microsoft.Xna` namespace é usado em MonoGame para tornar mais fácil migrar projetos existentes do XNA para MonoGame.

## <a name="rendering-our-first-sprite"></a>Nossa primeira entidade gráfica de renderização

Em seguida, chamaremos uma única entidade de gráfica para a tela para mostrar como executar a renderização 2D em MonoGame.

### <a name="creating-a-texture2d"></a>Criando um Texture2D

É preciso criar um `Texture2D` instância a ser usada durante a renderização de nossa entidade gráfica. Todo o conteúdo do jogo, por fim, está contido em uma pasta chamada **conteúdo,** localizado no projeto da plataforma específica. Projetos MonoGame compartilhado não podem conter conteúdo, como o conteúdo deve usar ações de compilação específica para a plataforma. Os desenvolvedores de CocosSharp localizar a pasta de conteúdo um conceito familiar – eles estão localizados no mesmo local em projetos CocosSharp e MonoGame. A pasta de conteúdo pode ser encontrada no projeto do iOS e dentro da pasta ativos no projeto Android.

Para adicionar o conteúdo do nosso jogo, clique duas vezes no **conteúdo** pasta e selecione **Adicionar > Adicionar arquivos...** Navegue até o local onde o arquivo content.zip foi extraído e selecione o **charactersheet.png** arquivo. Se for perguntado sobre como adicionar o arquivo de pasta, deve selecionamos o **cópia** opção:

![](part2-images/image1.png "Se for perguntado sobre como adicionar o arquivo de pasta, selecione a opção de cópia")

A pasta de conteúdo agora contém o arquivo charactersheet.png:

![](part2-images/image2.png "A pasta de conteúdo agora contém o arquivo charactersheet.png")

Em seguida, vamos adicionar código para carregar o arquivo charactersheet.png e criar um `Texture2D`. Para fazer essa abrir o `Game1.cs` de arquivo e adicione o campo a seguir à classe Game1.cs:

```csharp
Texture2D characterSheetTexture;
```

Em seguida, vamos criar o `characterSheetTexture` no `LoadContent` método. Antes de qualquer modificação `LoadContent` método tem esta aparência:

```csharp
protected override void LoadContent()
{
    // Create a new SpriteBatch, which can be used to draw textures.
    spriteBatch = new SpriteBatch(GraphicsDevice);
    // TODO: use this.Content to load your game content here
}
```

Devemos observar que o projeto padrão já instancia o `spriteBatch` instância para nós. Usaremos isso mais tarde, mas nós não adicionar qualquer código adicional para preparar o `spriteBatch` para uso. Por outro lado, nossa `spriteSheetTexture` requerem inicialização, portanto é inicializá-lo após o `spriteBatch` é criado:

```csharp
protected override void LoadContent()
{
    // Create a new SpriteBatch, which can be used to draw textures.
    spriteBatch = new SpriteBatch(GraphicsDevice);
    using (var stream = TitleContainer.OpenStream ("Content/charactersheet.png"))
    {
        characterSheetTexture = Texture2D.FromStream (this.GraphicsDevice, stream);

    }
}
```

Agora que temos uma `SpriteBatch` instância e um `Texture2D` instância que podemos adicionar nosso código de renderização para o `Game1.Draw` método:

```csharp
protected override void Draw(GameTime gameTime)
{
    GraphicsDevice.Clear(Color.CornflowerBlue);

    spriteBatch.Begin ();

    Vector2 topLeftOfSprite = new Vector2 (50, 50);
    Color tintColor = Color.White;

    spriteBatch.Draw(characterSheetTexture, topLeftOfSprite, tintColor);

    spriteBatch.End ();

    base.Draw(gameTime);
}
```

Executando o jogo agora mostra uma única entidade de gráfica exibindo a textura criada a partir de charactersheet.png:

![](part2-images/image3.png "Executando o jogo agora mostra uma única entidade de gráfica exibindo a textura criada a partir de charactersheet.png")

## <a name="creating-the-characterentity"></a>Criando o CharacterEntity

Até agora que adicionamos código para processar uma única entidade de gráfica para a tela; No entanto, se tivéssemos desenvolver um jogo completo sem criar outras classes, seria executada em problemas de organização de código.

### <a name="what-is-an-entity"></a>O que é uma entidade?

É um padrão comum para a organização de código de jogo para criar uma nova classe para cada jogo *entidade* tipo. Uma entidade no desenvolvimento de jogos é um objeto que pode conter algumas das características a seguir (nem todas são necessárias):

- Um elemento visual, como uma entidade gráfica, o texto ou o modelo 3D
- Cada comportamento de quadro, como uma unidade patrolling um caminho de conjunto ou um caractere de player respondendo para que a entrada ou física
- Podem ser criados e destruídos dinamicamente, como uma ativação que aparecem e que estão sendo coletados pelo player

Sistemas de organização de entidade podem ser complexos e mecanismos de jogos muitos oferecem classes para ajudar a gerenciar entidades. Vai ser implementando um sistema de entidade muito simples, portanto, é importante observar que jogos completos geralmente requerem mais organização na parte do desenvolvedor.


### <a name="defining-the-characterentity"></a>Definindo o CharacterEntity

Nossa entidade, que chamaremos `CharacterEntity`, terá as seguintes características:

- A capacidade de carregar seu próprio `Texture2D`
- A capacidade de processamento, incluindo que contém métodos de chamada para atualizar a animação de movimento
- `X` propriedades e Y para controlar a posição do caractere.
- A capacidade de atualizar – especificamente, para ler os valores de toque de tela e ajustar a posição adequadamente.

Para adicionar o `CharacterEntity` nosso jogo, clique com botão direito ou Control, clique no **WalkingGame** do projeto e selecione **Adicionar > novo arquivo...** . Selecione o **classe vazia** opção e nomeie o novo arquivo **CharacterEntity**, em seguida, clique em **novo**.

Primeiro, vamos adicionar a capacidade do `CharacterEntity` para carregar um `Texture2D` como também para desenhar a próprio. Vamos modificar o recém-adicionado `CharacterEntity.cs` arquivos da seguinte maneira:

```csharp
using System;
using Microsoft.Xna.Framework.Graphics;
using Microsoft.Xna.Framework;
using Microsoft.Xna.Framework.Input.Touch;


namespace WalkingGame
{
    public class CharacterEntity
    {
        static Texture2D characterSheetTexture;

        public float X
        {
            get;
            set;
        }

        public float Y
        {
            get;
            set;
        }

        public CharacterEntity (GraphicsDevice graphicsDevice)
        {
            if (characterSheetTexture == null)
            {
                using (var stream = TitleContainer.OpenStream ("Content/charactersheet.png"))
                {
                    characterSheetTexture = Texture2D.FromStream (graphicsDevice, stream);
                }
            }
        }

        public void Draw(SpriteBatch spriteBatch)
        {
            Vector2 topLeftOfSprite = new Vector2 (this.X, this.Y);
            Color tintColor = Color.White;
            spriteBatch.Draw(characterSheetTexture, topLeftOfSprite, tintColor);
        }
    }
}
```

O código acima adiciona a responsabilidade de posicionamento, renderização e carregar o conteúdo para o `CharacterEntity`. Vamos destacar algumas considerações feitas no código acima.

### <a name="why-are-x-and-y-floats"></a>Por que são X e Y flutuações?

Os desenvolvedores familiarizados com programação jogo talvez esteja se perguntando por que o `float` tipo está sendo usado em vez de `int` ou `double`. O motivo é que um valor de 32 bits é mais comum para o posicionamento no código de renderização de baixo nível. O tipo double ocupa 64 bits de precisão, que raramente é necessário para o posicionamento de objetos. Enquanto uma diferença de 32 bits pode parecer insignificante, muitos jogos modernos incluem gráficos que exigem processamento dezenas de milhares de valores de posição de cada quadro (30 ou 60 vezes por segundo). Recortar a quantidade de memória que deve mover por meio de gráficos de pipeline pela metade pode ter um impacto significativo no desempenho de um jogo.

O `int` tipo de dados pode ser uma unidade apropriada de medida para posicionamento, mas ele pode colocar limitações na maneira como entidades são posicionadas. Por exemplo, usar valores inteiros torna mais difícil implementar movimentação smooth para jogos que oferecem suporte a aumentar o zoom ou câmeras 3D (que é permitido por `SpriteBatch`).

Finalmente, veremos que a lógica que move os caracteres na tela vai fazer isso usando os valores de tempo do jogo. O resultado da multiplicação velocidade por quanto tempo se passou em um período especificado raramente resultará em um número inteiro, portanto, precisamos usar `float` para `X` e `Y`.

### <a name="why-is-charactersheettexture-static"></a>Por que é characterSheetTexture estático?

O `characterSheetTexture` `Texture2D` instância é uma representação de tempo de execução do arquivo charactersheet.png. Em outras palavras, ela contém os valores de cor para cada pixel como extraídos da origem **charactersheet.png** arquivo. Se nosso jogo criar dois `CharacterEntity` instâncias, em seguida, cada uma delas precisaria acesso às informações charactersheet.png. Nessa situação não queremos incorrer em custos de desempenho de carregamento charactersheet.png duas vezes, nem seria queremos ter memória de textura duplicados armazenada na ram. Usando um `static` campo nos permite compartilhar o mesmo `Texture2D` em todos os `CharacterEntity` instâncias.

Usando `static` membros para a representação de tempo de execução de conteúdo é uma solução simples e ele não aborda problemas encontrados em jogos maiores, como conteúdo descarregando ao mover de um nível para outro. As soluções mais sofisticadas, que estão além do escopo deste passo a passo, incluem usando o pipeline de conteúdo do MonoGame ou criando um sistema de gerenciamento de conteúdo personalizado.

### <a name="why-is-spritebatch-passed-as-an-argument-instead-of-instantiated-by-the-entity"></a>Por que é SpriteBatch passado como um argumento Instead of instanciado pela entidade?

O `Draw` método conforme implementado acima usa um `SpriteBatch` argumento, mas por outro lado, o `characterSheetTexture` é instanciado pelo `CharacterEntity`.

A razão para isso é porque o processamento mais eficiente possível quando o mesmo `SpriteBatch` instância é usada para todos os `Draw` chamadas e quando todos os `Draw` chamadas estão sendo feitas entre um único conjunto de `Begin` e `End` chamadas. Obviamente, nosso jogo incluirá apenas uma instância de entidade única, mas jogos mais complicados se beneficiam padrão que permite que várias entidades usar a mesma `SpriteBatch` instância.


## <a name="adding-characterentity-to-the-game"></a>Adicionando CharacterEntity ao jogo

Agora que adicionamos nosso `CharacterEntity` com código de renderização em si, substituímos o código em `Game1.cs` para usar uma instância dessa entidade de novo. Para fazer isso substituiremos o `Texture2D` campo com um `CharacterEntity` campo `Game1`:

```csharp
public class Game1 : Game
{
    GraphicsDeviceManager graphics;
    SpriteBatch spriteBatch;
    // New code:
    CharacterEntity character;

    public Game1()
    {
    ...
```

Em seguida, adicionaremos a instanciação dessa entidade em `Game1.Initialize`:

```csharp
protected override void Initialize()
{
    character = new CharacterEntity (this.GraphicsDevice);

    base.Initialize();
}
```

Também é necessário remover o `Texture2D` criação de `LoadContent` desde que agora é manipulado dentro de nossa `CharacterEntity`. `Game1.LoadContent` deve ser assim:

```csharp
protected override void LoadContent()
{
    // Create a new SpriteBatch, which can be used to draw textures.
    spriteBatch = new SpriteBatch(GraphicsDevice);
}
```

Vale a pena observar que, apesar do nome de `LoadContent` método não é o único lugar onde o conteúdo pode ser carregado – muitos implementar jogos conteúdo carregando em seu método de inicialização, ou mesmo em seu código de atualização como conteúdo, pode não ser necessários até que o player atinja um determinado ponto do jogo.

Finalmente, pode modificar o método Draw da seguinte maneira:


```csharp
protected override void Draw(GameTime gameTime)
{
    GraphicsDevice.Clear(Color.CornflowerBlue);

    // We'll start all of our drawing here:
    spriteBatch.Begin ();

    // Now we can do any entity rendering:
    character.Draw(spriteBatch);
    // End renders all sprites to the screen:
    spriteBatch.End ();

    base.Draw(gameTime);
}
```

Se executarmos o jogo, veremos agora o caractere. Como X e Y padrão como 0, o caractere é posicionado no canto superior esquerdo da tela:

![](part2-images/image4.png "Como X e Y padrão como 0, em seguida, o caractere é posicionado no canto superior esquerdo da tela")

## <a name="creating-the-animation-class"></a>Criando a classe de animação

No momento nosso `CharacterEntity` exibe completa **charactersheet.png** arquivo. Essa organização de várias imagens em um arquivo é conhecida como um *folha da entidade gráfica*. Normalmente, uma entidade gráfica processará apenas uma parte da folha da entidade gráfica. Vamos modificar o `CharacterEntity` para processar uma parte desse **charactersheet.png**, e essa parte será alterado ao longo do tempo para exibir uma animação em movimento.

Vamos criar o `Animation` classe para controlar a lógica e estado da animação CharacterEntity. A classe de animação será uma classe geral que pode ser usada para qualquer entidade, não apenas `CharacterEntity` animações. Ultimate a `Animation` classe fornecerá uma `Rectangle` que o `CharacterEntity` usará ao próprio desenho. Vamos também criar um `AnimationFrame` classe que será usado para definir a animação.


### <a name="defining-animationframe"></a>Definindo AnimationFrame

`AnimationFrame` não conterá nenhuma lógica relacionada a animação. Usaremos-apenas para armazenar dados. Para adicionar o `AnimationFrame` classe, o botão direito do mouse ou Control, clique no **WalkingGame** compartilhado projeto e selecione **Adicionar > novo arquivo...** Digite o nome **AnimationFrame** e clique no **novo** botão. Modificaremos a `AnimationFrame.cs` arquivos de forma que ele contém o código a seguir:


```csharp
using System;
using Microsoft.Xna.Framework;

namespace WalkingGame
{
    public class AnimationFrame
    {
        public Rectangle SourceRectangle { get; set; }
        public TimeSpan Duration { get; set; }
    }
}
```

O `AnimationFrame` classe contém dois tipos de informações:

- `SourceRectangle` – Define a área do `Texture2D` que será exibido quando o `AnimationFrame`. Esse valor é medido em pixels, com a parte superior esquerda sendo (0, 0).
- `Duration` – Define por quanto tempo um `AnimationFrame` é exibida quando usada em um `Animation`.

### <a name="defining-animation"></a>Definição de animação

O `Animation` classe conterá um `List<AnimationFrame>` , bem como a lógica para alternar o quadro é exibido atualmente acordo com o tempo passou.

Para adicionar o `Animation` classe, o botão direito do mouse ou Control, clique no **WalkingGame** compartilhado projeto e selecione **Adicionar > novo arquivo...** . Digite o nome **animação** e clique no **novo** botão. Modificaremos a `Animation.cs` de arquivo para que ele contém o código a seguir:


```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using Microsoft.Xna.Framework;

namespace WalkingGame
{
    public class Animation
    {
        List<AnimationFrame> frames = new List<AnimationFrame>();
        TimeSpan timeIntoAnimation;

        TimeSpan Duration
        {
            get
            {
                double totalSeconds = 0;
                foreach (var frame in frames)
                {
                    totalSeconds += frame.Duration.TotalSeconds;
                }

                return TimeSpan.FromSeconds (totalSeconds);
            }
        }

        public void AddFrame(Rectangle rectangle, TimeSpan duration)
        {
            AnimationFrame newFrame = new AnimationFrame()
            {
                SourceRectangle = rectangle,
                Duration = duration
            };

            frames.Add(newFrame);
        }

        public void Update(GameTime gameTime)
        {
            double secondsIntoAnimation = 
                timeIntoAnimation.TotalSeconds + gameTime.ElapsedGameTime.TotalSeconds;


            double remainder = secondsIntoAnimation % Duration.TotalSeconds;

            timeIntoAnimation = TimeSpan.FromSeconds (remainder);
        }
    }
}
```

Vamos examinar alguns dos detalhes do `Animation` classe.

### <a name="frames-list"></a>Lista de quadros

O `frames` membro é o que armazena os dados para nossa animação. Adicione o código que instancia as animações `AnimationFrame` instâncias para o `frames` lista por meio do `AddFrame` método. Uma implementação mais completa pode oferecer `public` métodos ou propriedades para modificar `frames`, mas estamos limitará a funcionalidade de adição de quadros para este passo a passo.

### <a name="duration"></a>Duração

Duração retorna a duração total do `Animation,` que é obtido, adicionando a duração de todas as independente `AnimationFrame` instâncias. Esse valor pode ser armazenado em cache se `AnimationFrame` fosse um objeto imutável, mas uma vez que é implementado AnimationFrame como uma classe que pode ser alterada depois de serem adicionados a animação, precisamos para calcular esse valor sempre que a propriedade for acessada.

### <a name="update"></a>Atualização

O `Update` método deve ser chamado a cada quadro (ou seja, toda vez que o jogo inteiro é atualizado). Sua finalidade é aumentar a `timeIntoAnimation` membro que é usado para retornar o quadro exibido no momento. A lógica em `Update` impede o `timeIntoAnimation` nunca sejam maiores que a duração da animação inteira.

Desde que usaremos o `Animation` classe para exibir uma animação em movimento, em seguida, queremos fazer com que a animação repetir quando atingir o final. Podemos pode fazer isso verificando se o `timeIntoAnimation` é maior do que o `Duration` valor. Nesse caso ele volta ao início do ciclo e preservar o resto, resultando em uma animação em loop.

### <a name="obtaining-the-current-frames-rectangle"></a>Obtendo o retângulo do quadro atual

A finalidade de `Animation` classe é, essencialmente, fornecer um `Rectangle` que pode ser usado ao desenhar uma entidade gráfica. Atualmente o `Animation` classe contém a lógica para alterar o `timeIntoAnimation` membro, que será usado para obter que `Rectangle` devem ser exibidos. Faremos isso criando uma `CurrentRectangle` propriedade o `Animation` classe. Copie essa propriedade para o `Animation` classe:

```csharp
public Rectangle CurrentRectangle
{
    get
    {
        AnimationFrame currentFrame = null;

        // See if we can find the frame
        TimeSpan accumulatedTime;
        foreach(var frame in frames)
        {
            if (accumulatedTime + frame.Duration >= timeIntoAnimation)
            {
                currentFrame = frame;
                break;
            }
            else
            {
                accumulatedTime += frame.Duration;
            }
        }

        // If no frame was found, then try the last frame, 
        // just in case timeIntoAnimation somehow exceeds Duration
        if (currentFrame == null)
        {
            currentFrame = frames.LastOrDefault ();
        }

        // If we found a frame, return its rectangle, otherwise
        // return an empty rectangle (one with no width or height)
        if (currentFrame != null)
        {
            return currentFrame.SourceRectangle;
        }
        else
        {
            return Rectangle.Empty;
        }
    }
}
```

## <a name="adding-the-first-animation-to-characterentity"></a>Adicionando a primeira animação para CharacterEntity

O `CharacterEntity` conterá animações para percorrer e permanente, bem como uma referência ao atual `Animation` que está sendo exibido.

Primeiro, vamos adicionar nossa primeira `Animation,` que será usado para testar a funcionalidade como escrito até o momento. Vamos adicionar os seguintes membros para a classe CharacterEntity:

```csharp
Animation walkDown;
Animation currentAnimation;
```

Em seguida, vamos definir a `walkDown` animação. Para fazer isso modificar o `CharacterEntity` construtor da seguinte maneira:

```csharp
public CharacterEntity (GraphicsDevice graphicsDevice)
{
    if (characterSheetTexture == null)
    {
        using (var stream = TitleContainer.OpenStream ("Content/charactersheet.png"))
        {
            characterSheetTexture = Texture2D.FromStream (graphicsDevice, stream);
        }
    }

    walkDown = new Animation ();
    walkDown.AddFrame (new Rectangle (0, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkDown.AddFrame (new Rectangle (16, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkDown.AddFrame (new Rectangle (0, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkDown.AddFrame (new Rectangle (32, 0, 16, 16), TimeSpan.FromSeconds (.25));
}
```

Como mencionado anteriormente, é necessário chamar `Animation.Update` para animações baseada em tempo executar. Também é preciso atribuir a `currentAnimation`. Para agora podemos atribuirá o `currentAnimation` para `walkDown`, mas estamos substituirá esse código mais tarde quando implementamos nossa lógica de movimentação. Vamos adicionar o `Update` método `CharacterEntity` da seguinte maneira:


```csharp
public void Update(GameTime gameTime)
{
    // temporary - we'll replace this with logic based off of which way the
    // character is moving when we add movement logic
    currentAnimation = walkDown;

    currentAnimation.Update (gameTime);
}
```

Agora que temos o `currentAnimation` sendo atribuído e atualizados, podemos usá-lo para executar o nosso desenho. Modificaremos `CharacterEntity.Draw` da seguinte maneira:

```csharp
public void Draw(SpriteBatch spriteBatch)
{
    Vector2 topLeftOfSprite = new Vector2 (this.X, this.Y);
    Color tintColor = Color.White;
    var sourceRectangle = currentAnimation.CurrentRectangle;

    spriteBatch.Draw(characterSheetTexture, topLeftOfSprite, sourceRectangle, Color.White);
}
```

A última etapa para obter o `CharacterEntity` animação é chamar recém-adicionado `Update` método de `Game1`. Modificar `Game1.Update` da seguinte maneira:

```csharp
protected override void Update(GameTime gameTime)
{
    character.Update (gameTime);
    base.Update(gameTime);
}
```

Agora o `CharacterEntity` executará seu `walkDown` animação:

![](part2-images/image5.gif "Agora o CharacterEntity executará sua animação walkDown")

## <a name="adding-movement-to-the-character"></a>Adicionando a movimentação para o caractere

Em seguida, incluiremos movimentação para nosso caractere usando controles de toque. Quando o usuário toca a tela, o caractere será movido para o ponto em que a tela é alterada. Se nenhum ajustes forem detectados, o caractere dará suporte em vigor.

### <a name="defining-getdesiredvelocityfrominput"></a>Definindo GetDesiredVelocityFromInput

Usaremos do MonoGame `TouchPanel` classe, que fornece informações sobre o estado atual da tela sensível ao toque. Vamos adicionar um método que irá verificar se o `TouchPanel` e velocidade desejada do nosso caractere de retorno:


```csharp
Vector2 GetDesiredVelocityFromInput()
{
    Vector2 desiredVelocity = new Vector2 ();

    TouchCollection touchCollection = TouchPanel.GetState();

    if (touchCollection.Count > 0)
    {
        desiredVelocity.X = touchCollection [0].Position.X - this.X;
        desiredVelocity.Y = touchCollection [0].Position.Y - this.Y;

        if (desiredVelocity.X != 0 || desiredVelocity.Y != 0)
        {
            desiredVelocity.Normalize();
            const float desiredSpeed = 200;
            desiredVelocity *= desiredSpeed;
        }
    }

    return desiredVelocity;
}
```

O `TouchPanel.GetState` método retorna um `TouchCollection` que contém informações sobre onde o usuário é tocar na tela. Se o usuário não é tocar na tela, em seguida, o `TouchCollection` estará vazio, caso em que podemos não deve mover o caractere.

Se o usuário é tocar na tela, podemos moverá o caractere para o primeiro toque, em outras palavras, o `TouchLocation` no índice 0. Inicialmente, vamos definir a velocidade desejada para igualar a diferença entre o local do caractere e o local do toque primeiro:

```csharp
        desiredVelocity.X = touchCollection [0].Position.X - this.X;
        desiredVelocity.Y = touchCollection [0].Position.Y - this.Y;
```

O que vem a seguir é um pouco de matemática que manterá o caractere movendo na mesma velocidade. Para ajudar a explicar por que isso é importante, vamos considerar uma situação em que o usuário estiver tocando os pixels de 500 longe onde se encontra o caractere. A primeira linha onde `desiredVelocity.X` é conjunto atribua um valor de 500. No entanto, se o usuário foram tocar na tela em uma distância de apenas 100 unidades de caractere, o `desiredVelocity.X `deve ser definido como 100. O resultado seria que velocidade do movimento do caractere poderia responder como distante o ponto de toque é do caractere. Como queremos que o caractere para mover sempre na mesma velocidade, é preciso modificar o desiredVelocity.

O `if (desiredVelocity.X != 0 || desiredVelocity.Y != 0)` instrução está verificando se a velocidade é não-zero – em outras palavras, ele verifica para certificar-se de que o usuário não estiver tocando no mesmo ponto como a posição atual do caractere. Se não, em seguida, precisamos definir a velocidade do caractere seja muito distante constante, independentemente de como o toque é. Podemos fazer isso normalizar o vetor de velocidade que resulta em que ele é um comprimento de 1. Um vetor de velocidade de 1 significa que o caractere moverá a 1 pixel por segundo. Podemos vai acelerar isso multiplicando o valor pela velocidade desejada de 200.


### <a name="applying-velocity-to-position"></a>Aplicação de velocidade para posição

A velocidade retornada de `GetDesiredVelocityFromInput` precisa ser aplicada para o caractere `X` e `Y` valores ter nenhum efeito em tempo de execução. Modificaremos a `Update` método da seguinte maneira:

```csharp
public void Update(GameTime gameTime)
{

    var velocity = GetDesiredVelocityFromInput ();

    this.X += velocity.X * (float)gameTime.ElapsedGameTime.TotalSeconds;
    this.Y += velocity.Y * (float)gameTime.ElapsedGameTime.TotalSeconds;

    // temporary - we'll replace this with logic based off of which way the
    // character is moving when we add movement logic
    currentAnimation = walkDown;

    currentAnimation.Update (gameTime);
}
```

Aqui, implementamos é chamado *baseada em tempo* movimentação (em vez de *com base no quadro* movimentação). Movimentação de tempo multiplica um valor de velocidade (no nosso caso, os valores armazenados na `velocity` variável) por quanto tempo se passou desde a última atualização que é armazenada em `gameTime.ElapsedGameTime.TotalSeconds`. Se o jogo é executada em menos quadros por segundo, aumenta o tempo decorrido entre quadros – o resultado final é que os objetos usando movimentação com base em tempo sempre serão movidos na mesma velocidade, independentemente da taxa de quadros.

Se executarmos nosso jogo agora, veremos que o caractere está mudando para a localização de toque:

![](part2-images/image6.gif "O caractere está migrando para o local de toque")

## <a name="matching-movement-and-animation"></a>Animação e movimentação de correspondência

Assim que tivermos nosso caractere mover e executar uma única animação, podemos pode definir o restante da nossas animações e usá-los para refletir a movimentação do caractere. Quando concluído, teremos oito animações no total:

- Animações percorra, para baixo, esquerda e direita
- Animações para uma posição ainda e voltado para cima, para baixo, esquerda e direita

### <a name="defining-the-rest-of-the-animations"></a>Definindo o restante das animações

Vamos primeiro adicionar o `Animation` instâncias para o `CharacterEntity` classe para todos os nossos animações no mesmo lugar onde adicionamos `walkDown`. Feito isso, o `CharacterEntity` terá o seguinte `Animation` membros:

```csharp
Animation walkDown;
Animation walkUp;
Animation walkLeft;
Animation walkRight;

Animation standDown;
Animation standUp;
Animation standLeft;
Animation standRight;

Animation currentAnimation;
```

Agora vamos definir animações no `CharacterEntity` construtor da seguinte maneira:

```csharp
public CharacterEntity (GraphicsDevice graphicsDevice)
{
    if (characterSheetTexture == null)
    {
        using (var stream = TitleContainer.OpenStream ("Content/charactersheet.png"))
        {
            characterSheetTexture = Texture2D.FromStream (graphicsDevice, stream);
        }
    }

    walkDown = new Animation ();
    walkDown.AddFrame (new Rectangle (0, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkDown.AddFrame (new Rectangle (16, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkDown.AddFrame (new Rectangle (0, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkDown.AddFrame (new Rectangle (32, 0, 16, 16), TimeSpan.FromSeconds (.25));

    walkUp = new Animation ();
    walkUp.AddFrame (new Rectangle (144, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkUp.AddFrame (new Rectangle (160, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkUp.AddFrame (new Rectangle (144, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkUp.AddFrame (new Rectangle (176, 0, 16, 16), TimeSpan.FromSeconds (.25));

    walkLeft = new Animation ();
    walkLeft.AddFrame (new Rectangle (48, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkLeft.AddFrame (new Rectangle (64, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkLeft.AddFrame (new Rectangle (48, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkLeft.AddFrame (new Rectangle (80, 0, 16, 16), TimeSpan.FromSeconds (.25));

    walkRight = new Animation ();
    walkRight.AddFrame (new Rectangle (96, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkRight.AddFrame (new Rectangle (112, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkRight.AddFrame (new Rectangle (96, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkRight.AddFrame (new Rectangle (128, 0, 16, 16), TimeSpan.FromSeconds (.25));

    // Standing animations only have a single frame of animation:
    standDown = new Animation ();
    standDown.AddFrame (new Rectangle (0, 0, 16, 16), TimeSpan.FromSeconds (.25));

    standUp = new Animation ();
    standUp.AddFrame (new Rectangle (144, 0, 16, 16), TimeSpan.FromSeconds (.25));

    standLeft = new Animation ();
    standLeft.AddFrame (new Rectangle (48, 0, 16, 16), TimeSpan.FromSeconds (.25));

    standRight = new Animation ();
    standRight.AddFrame (new Rectangle (96, 0, 16, 16), TimeSpan.FromSeconds (.25));
}
```

Devemos observar que o código acima foi adicionado para o `CharacterEntity` construtor para manter este passo a passo mais curto. Jogos geralmente serão separar a definição de animações de caractere em suas próprias classes ou carregar essas informações de um formato de dados como XML ou JSON.

Em seguida, vamos vai ajustar a lógica para usar as animações de acordo com a direção em que o caractere é mover ou de acordo com a última animação se o caractere apenas foi interrompido. Para fazer isso, modificaremos a `Update` método:


```csharp
public void Update(GameTime gameTime)
{
    var velocity = GetDesiredVelocityFromInput ();

    this.X += velocity.X * (float)gameTime.ElapsedGameTime.TotalSeconds;
    this.Y += velocity.Y * (float)gameTime.ElapsedGameTime.TotalSeconds;


    if (velocity != Vector2.Zero)
    {
        bool movingHorizontally = Math.Abs (velocity.X) > Math.Abs (velocity.Y);
        if (movingHorizontally)
        {
            if (velocity.X > 0)
            {
                currentAnimation = walkRight;
            }
            else
            {
                currentAnimation = walkLeft;
            }
        }
        else
        {
            if (velocity.Y > 0)
            {
                currentAnimation = walkDown;
            }
            else
            {
                currentAnimation = walkUp;
            }
        }
    }
    else
    {
        // If the character was walking, we can set the standing animation
        // according to the walking animation that is playing:
        if (currentAnimation == walkRight)
        {
            currentAnimation = standRight;
        }
        else if (currentAnimation == walkLeft)
        {
            currentAnimation = standLeft;
        }
        else if (currentAnimation == walkUp)
        {
            currentAnimation = standUp;
        }
        else if (currentAnimation == walkDown)
        {
            currentAnimation = standDown;
        }
        else if (currentAnimation == null)
        {
            currentAnimation = standDown;
        }

        // if none of the above code hit then the character
        // is already standing, so no need to change the animation.
    }

    currentAnimation.Update (gameTime);
}
```

O código para alternar as animações é dividido em dois blocos. O primeiro verifica se a velocidade não é igual a `Vector2.Zero` – isso informa se o caractere é movido. Se o caractere é móvel, podemos ver o `velocity.X` e `velocity.Y` valores para determinar quais animação em movimento para executar.

Se o caractere não está movendo, queremos definir o caractere `currentAnimation` uma animação permanente – mas estamos apenas fazê-lo se o `currentAnimation` é um movimentação animação ou se uma animação não tiver sido definida. Se o `currentAnimation` não é uma das quatro animações deslocamento, em seguida, o caractere já está aguardando para que não seja necessário alterar `currentAnimation`.

O resultado desse código é que o caractere animar corretamente ao percorrer e voltadas para a direção do último que ele foi andando quando ela for interrompida, em seguida:

![](part2-images/image7.gif "O resultado desse código é que o caractere animar corretamente ao percorrer e voltadas para a direção do último que ele foi andando quando ela for interrompida, em seguida")

## <a name="summary"></a>Resumo

Este passo a passo mostrou como trabalhar com MonoGame para criar uma plataforma cruzada com sprites, movimentação de objetos, detecção de entrada e animação. Ele abrange a criação de uma classe de animação de uso geral. Ele também mostrou como criar uma entidade de caractere para a organização lógica de código.

## <a name="related-links"></a>Links relacionados

- [Recurso de imagem CharacterSheet (exemplo)](https://github.com/xamarin/mobile-samples/blob/master/WalkingGameMG/Resources/charactersheet.png?raw=true)
- [Verificação completa de jogo (exemplo)](https://developer.xamarin.com/samples/mobile/WalkingGameMG/)
