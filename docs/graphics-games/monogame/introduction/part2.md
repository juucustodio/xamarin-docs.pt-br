---
title: Parte 2 – implementar o WalkingGame
description: Este passo a passo mostra como adicionar a lógica do jogo e o conteúdo para um projeto de MonoGame vazio para criar uma demonstração de um sprite animado movendo com entrada por toque.
ms.prod: xamarin
ms.assetid: F0622A01-DE7F-451A-A51F-129876AB6FFD
author: conceptdev
ms.author: crdun
ms.date: 03/28/2017
ms.openlocfilehash: c9e0cf2f29d304f042bc56ee91029adadcaba570
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67832499"
---
# <a name="part-2--implementing-the-walkinggame"></a>Parte 2 – implementar o WalkingGame

_Este passo a passo mostra como adicionar a lógica do jogo e o conteúdo para um projeto de MonoGame vazio para criar uma demonstração de um sprite animado movendo com entrada por toque._

As partes anteriores deste passo a passo mostraram como criar projetos vazios do MonoGame. Vamos criar nessas partes anteriores, fazendo uma demonstração de jogo simple. Este artigo contém as seguintes seções:

- Descompactando o nosso conteúdo de jogo
- Visão geral da classe MonoGame
- Nossa primeira Sprite de renderização
- Criando o CharacterEntity
- Adicionando CharacterEntity ao jogo
- Criando a classe de animação
- Adicionar a primeira animação ao CharacterEntity
- Adição de movimentação para o caractere
- Correspondência de animação e movimentação


## <a name="unzipping-our-game-content"></a>Descompactando o nosso conteúdo de jogo

Antes de começarmos a escrever código, queremos Descompacte nosso jogo *conteúdo*. Os desenvolvedores de jogos geralmente usam o termo *conteúdo* para se referir a arquivos sem código, que geralmente são criados por visual artistas, criadores de jogos ou designers de áudio. Tipos comuns de conteúdo incluem arquivos usados para exibir elementos visuais, tocar um som ou controlar o comportamento de inteligência artificial (AI). De um jogo de desenvolvimento de conteúdo do ponto de vista da equipe geralmente é criado por não-programadores.

O conteúdo usado aqui pode ser encontrado [no github](https://github.com/xamarin/mobile-samples/blob/master/WalkingGameMG/Resources/charactersheet.png?raw=true). Precisaremos desses arquivos baixados para um local que acessaremos posteriormente neste passo a passo.

## <a name="monogame-class-overview"></a>Visão geral da classe MonoGame

Para os iniciantes, exploraremos as classes de MonoGame usadas na renderização básica:

- `SpriteBatch` – usado para desenhar gráficos em 2D para a tela. *Sprites* são elementos visuais 2D que são usados para exibir imagens na tela. O `SpriteBatch` objeto pode desenhar um sprite de único cada vez entre seu `Begin` e `End` métodos ou vários sprites podem ser agrupados, ou *em lote*.
- `Texture2D` – representa um objeto de imagem em tempo de execução. `Texture2D` instâncias geralmente são criadas a partir de formatos de arquivo como. png ou. bmp, embora eles também podem ser criados dinamicamente em tempo de execução. `Texture2D` instâncias são usadas ao renderizar com `SpriteBatch` instâncias.
- `Vector2` – representa uma posição em um sistema de coordenadas 2D que geralmente é usado para posicionar objetos visuais. Também inclui MonoGame `Vector3` e `Vector4` , mas vamos apenas usar `Vector2` neste passo a passo.
- `Rectangle` – uma área com quatro lados com a posição, largura e altura. Usaremos isso para definir qual parte do nosso `Texture2D` a renderização quando começamos a trabalhar com animações.

Observe também que o MonoGame não é mantido pela Microsoft – apesar de seu namespace. O `Microsoft.Xna` namespace é usado no MonoGame para facilitar a migração de projetos existentes do XNA para MonoGame.

## <a name="rendering-our-first-sprite"></a>Nossa primeira Sprite de renderização

Em seguida, podemos desenhará um sprite de único para a tela para mostrar como executar a renderização 2D no MonoGame.

### <a name="creating-a-texture2d"></a>Criando uma Texture2D

É necessário criar um `Texture2D` instância a ser usado ao renderizar nossa sprite. Todo o conteúdo do jogo, por fim, está contido em uma pasta chamada **conteúdo,** localizado no projeto específico da plataforma. Projetos de MonoGame compartilhado não podem conter conteúdo, como o conteúdo deve usar ações de build específico da plataforma. A pasta de conteúdo pode ser encontrada no projeto do iOS e dentro da pasta ativos no projeto do Android.

Para adicionar o conteúdo do nosso jogo, clique com botão direito no **conteúdo** pasta e selecione **Adicionar > Adicionar arquivos...** Navegue até o local onde o arquivo de content.zip foi extraído e selecione o **charactersheet.png** arquivo. Se for perguntado sobre como adicionar o arquivo à pasta, devemos selecionar o **cópia** opção:

![](part2-images/image1.png "Se for perguntado sobre como adicionar o arquivo à pasta, selecione a opção de cópia")

A pasta de conteúdo agora contém o arquivo charactersheet.png:

![](part2-images/image2.png "A pasta de conteúdo agora contém o arquivo charactersheet.png")

Em seguida, vamos adicionar código para carregar o arquivo charactersheet.png e criar um `Texture2D`. Para fazer isso, abra o `Game1.cs` arquivo e adicione o seguinte campo à classe Game1.cs:

```csharp
Texture2D characterSheetTexture;
```

Em seguida, vamos criar o `characterSheetTexture` no `LoadContent` método. Antes de qualquer modificação `LoadContent` método tem esta aparência:

```csharp
protected override void LoadContent()
{
    // Create a new SpriteBatch, which can be used to draw textures.
    spriteBatch = new SpriteBatch(GraphicsDevice);
    // TODO: use this.Content to load your game content here
}
```

Devemos observar que o projeto padrão já instancia o `spriteBatch` instância para nós. Usaremos isso mais tarde, mas não podemos adicionar qualquer código adicional para preparar o `spriteBatch` para uso. Por outro lado, nossa `spriteSheetTexture` exigem a inicialização, portanto, podemos será inicializá-lo após o `spriteBatch` é criado:

```csharp
protected override void LoadContent()
{
    // Create a new SpriteBatch, which can be used to draw textures.
    spriteBatch = new SpriteBatch(GraphicsDevice);
    using (var stream = TitleContainer.OpenStream ("Content/charactersheet.png"))
    {
        characterSheetTexture = Texture2D.FromStream (this.GraphicsDevice, stream);

    }
}
```

Agora que temos uma `SpriteBatch` instância e um `Texture2D` instância podemos adicionar nosso código de renderização para o `Game1.Draw` método:

```csharp
protected override void Draw(GameTime gameTime)
{
    GraphicsDevice.Clear(Color.CornflowerBlue);

    spriteBatch.Begin ();

    Vector2 topLeftOfSprite = new Vector2 (50, 50);
    Color tintColor = Color.White;

    spriteBatch.Draw(characterSheetTexture, topLeftOfSprite, tintColor);

    spriteBatch.End ();

    base.Draw(gameTime);
}
```

Executar o jogo agora mostra um sprite de único exibindo a textura criada a partir charactersheet.png:

![](part2-images/image3.png "Executar o jogo agora mostra um sprite de único exibindo a textura criada a partir charactersheet.png")

## <a name="creating-the-characterentity"></a>Criando o CharacterEntity

Até agora adicionamos código para renderizar um sprite de único para a tela; No entanto, se fôssemos desenvolver um jogo completo sem criar outras classes, executaríamos em problemas de organização do código.

### <a name="what-is-an-entity"></a>O que é uma entidade?

Um padrão comum para organizar o código de jogo é criar uma nova classe para cada jogo *entidade* tipo. Uma entidade no desenvolvimento de jogos é um objeto que pode conter algumas das características a seguir (nem todos são obrigatório):

- Um elemento visual como um sprite, o texto ou o modelo 3D
- Física ou comportamento cada quadro, como uma unidade patrolling um caminho de conjunto ou um caractere de player responder a entradas
- Podem ser criados e destruídos dinamicamente, como uma power-up que aparece e o que está sendo coletado pelo player

Sistemas de organização de entidade podem ser complexos e muitos mecanismos de jogos oferecem classes para ajudar a gerenciar entidades. Podemos implementar um sistema de entidade bem simples, portanto, vale a pena observar que completo jogos geralmente exigem mais organização na parte do desenvolvedor.


### <a name="defining-the-characterentity"></a>Definindo o CharacterEntity

Nossa entidade, que chamaremos `CharacterEntity`, terá as seguintes características:

- A capacidade de carregar seu próprio `Texture2D`
- A capacidade de processar a mesmo, incluindo que contém métodos de chamada para atualizar a animação caminhar
- `X` propriedades e Y para controlar a posição do caractere.
- A capacidade de atualizar em si – especificamente, para ler os valores de toque de tela e ajustam a posição adequadamente.

Para adicionar o `CharacterEntity` ao nosso jogo, o botão direito do mouse ou CTRL + clique em de **WalkingGame** do projeto e selecione **Adicionar > novo arquivo...** . Selecione o **classe vazia** opção e nomeie o novo arquivo **CharacterEntity**, em seguida, clique em **New**.

Primeiro, vamos adicionar a capacidade para o `CharacterEntity` para carregar um `Texture2D` como também para desenhar a mesmo. Vamos modificar o recém-adicionado `CharacterEntity.cs` arquivos da seguinte maneira:

```csharp
using System;
using Microsoft.Xna.Framework.Graphics;
using Microsoft.Xna.Framework;
using Microsoft.Xna.Framework.Input.Touch;


namespace WalkingGame
{
    public class CharacterEntity
    {
        static Texture2D characterSheetTexture;

        public float X
        {
            get;
            set;
        }

        public float Y
        {
            get;
            set;
        }

        public CharacterEntity (GraphicsDevice graphicsDevice)
        {
            if (characterSheetTexture == null)
            {
                using (var stream = TitleContainer.OpenStream ("Content/charactersheet.png"))
                {
                    characterSheetTexture = Texture2D.FromStream (graphicsDevice, stream);
                }
            }
        }

        public void Draw(SpriteBatch spriteBatch)
        {
            Vector2 topLeftOfSprite = new Vector2 (this.X, this.Y);
            Color tintColor = Color.White;
            spriteBatch.Draw(characterSheetTexture, topLeftOfSprite, tintColor);
        }
    }
}
```

O código acima adiciona a responsabilidade de posicionamento, renderização e carregamento de conteúdo para o `CharacterEntity`. Reservemos um momento para destacar algumas considerações feitas no código acima.

### <a name="why-are-x-and-y-floats"></a>Por que são X e Y Floats?

Os desenvolvedores não familiarizados com programação de jogo deve estar imaginando por que o `float` tipo está sendo usado em vez `int` ou `double`. O motivo é que um valor de 32 bits é mais comum para posicionamento no código de renderização de baixo nível. O tipo double ocupa 64 bits de precisão, que raramente é necessário para o posicionamento de objetos. Embora uma diferença de 32 bits pode parecer insignificante, muitos jogos modernos incluem elementos gráficos que exigem processamento dezenas de milhares de valores de posição de cada quadro (30 ou 60 vezes por segundo). Reduzindo a quantidade de memória que deve mover os elementos gráficos por meio do pipeline pela metade pode ter um impacto significativo no desempenho de um jogo.

O `int` tipo de dados pode ser uma unidade apropriada de medida para posicionamento, mas ele pode colocar as limitações na maneira como entidades são posicionadas. Por exemplo, usar valores inteiros torna mais difícil implementar um movimento suave para jogos que dão suporte a aumentar o zoom ou câmeras 3D (que é permitido por `SpriteBatch`).

Por fim, veremos que a lógica que move o personagem pela tela vai fazer isso usando os valores de medição de tempo do jogo. O resultado da multiplicação de velocidade por quanto tempo se passou em um determinado quadro raramente resultará em um número inteiro, portanto, precisamos usar `float` para `X` e `Y`.

### <a name="why-is-charactersheettexture-static"></a>Por que é characterSheetTexture estático?

O `characterSheetTexture` `Texture2D` instância é uma representação de tempo de execução do arquivo charactersheet.png. Em outras palavras, ele contém os valores de cor para cada pixel como extraídos da fonte de **charactersheet.png** arquivo. Se nosso jogo fosse criar duas `CharacterEntity` instâncias, em seguida, cada um deles precisaria de acesso a informações de charactersheet.png. Nessa situação não queremos aumentará o custo de desempenho de carregamento charactersheet.png duas vezes, nem iríamos querer ter memória de textura duplicados armazenada na ram. Usando um `static` campo permite compartilhar o mesmo `Texture2D` em todos os `CharacterEntity` instâncias.

Usando `static` membros para a representação de tempo de execução de conteúdo é uma solução simplista e não aborda os problemas encontrados em jogos maiores, como conteúdo descarregando ao mover de um nível para outro. As soluções mais sofisticadas, que estão além do escopo deste passo a passo, incluem usando o pipeline de conteúdo do MonoGame ou criando um sistema de gerenciamento de conteúdo personalizado.

### <a name="why-is-spritebatch-passed-as-an-argument-instead-of-instantiated-by-the-entity"></a>Por que é SpriteBatch passado como um argumento Instead of instanciado pela entidade?

O `Draw` método conforme implementado acima usa um `SpriteBatch` argumento, mas por outro lado, o `characterSheetTexture` é instanciada pelo `CharacterEntity`.

A razão para isso é porque o processamento mais eficiente é possível quando o mesmo `SpriteBatch` instância é usada para todos os `Draw` chamadas e quando todos os `Draw` chamadas estão sendo feitas entre um único conjunto de `Begin` e `End` chamadas. Obviamente, nosso jogo incluirá apenas uma instância de entidade única, mas jogos mais complicados se beneficiarão do padrão que permite que várias entidades usar a mesma `SpriteBatch` instância.


## <a name="adding-characterentity-to-the-game"></a>Adicionando CharacterEntity ao jogo

Agora que adicionamos nossos `CharacterEntity` com o código para a renderização em si, podemos pode substituir o código em `Game1.cs` para usar uma instância dessa nova entidade. Para fazer isso, vamos substituir a `Texture2D` campo com um `CharacterEntity` campo `Game1`:

```csharp
public class Game1 : Game
{
    GraphicsDeviceManager graphics;
    SpriteBatch spriteBatch;
    // New code:
    CharacterEntity character;

    public Game1()
    {
    ...
```

Em seguida, adicionaremos a instanciação dessa entidade em `Game1.Initialize`:

```csharp
protected override void Initialize()
{
    character = new CharacterEntity (this.GraphicsDevice);

    base.Initialize();
}
```

Também é necessário remover o `Texture2D` criação a partir `LoadContent` desde que agora é tratado dentro de nossa `CharacterEntity`. `Game1.LoadContent` deve ser assim:

```csharp
protected override void LoadContent()
{
    // Create a new SpriteBatch, which can be used to draw textures.
    spriteBatch = new SpriteBatch(GraphicsDevice);
}
```

Vale a pena observar que, apesar do nome de `LoadContent` método não é o único lugar em que o conteúdo pode ser carregado – implementar em muitos jogos conteúdo carregando em seu método Initialize, ou mesmo em seu código de atualização como conteúdo, pode não ser necessárias até que o jogador atinja um determinado ponto do jogo.

Por fim, podemos modificar o método Draw da seguinte maneira:


```csharp
protected override void Draw(GameTime gameTime)
{
    GraphicsDevice.Clear(Color.CornflowerBlue);

    // We'll start all of our drawing here:
    spriteBatch.Begin ();

    // Now we can do any entity rendering:
    character.Draw(spriteBatch);
    // End renders all sprites to the screen:
    spriteBatch.End ();

    base.Draw(gameTime);
}
```

Se executarmos o jogo, agora veremos o caractere. Uma vez que X e Y padrão como 0, o caractere é posicionado no canto superior esquerdo da tela:

![](part2-images/image4.png "Uma vez que X e Y padrão como 0, em seguida, o caractere é posicionado no canto superior esquerdo da tela")

## <a name="creating-the-animation-class"></a>Criando a classe de animação

Atualmente, nossos `CharacterEntity` exibe todo **charactersheet.png** arquivo. Essa disposição de várias imagens em um arquivo é conhecida como um *folha sprite*. Normalmente, um sprite renderizará somente uma parte da planilha sprite. Vamos modificar o `CharacterEntity` para processar uma parte desse **charactersheet.png**, e essa parte será alterado ao longo do tempo para exibir uma animação de movimentação.

Vamos criar o `Animation` classe para controlar a lógica e estado da animação CharacterEntity. A classe de animação será uma classe geral que poderia ser usada para qualquer entidade, não apenas `CharacterEntity` animações. Ultimate a `Animation` classe fornecerá uma `Rectangle` que o `CharacterEntity` será usado ao desenhar em si. Também vamos criar um `AnimationFrame` classe que será usado para definir a animação.


### <a name="defining-animationframe"></a>Definindo AnimationFrame

`AnimationFrame` não conterá nenhuma lógica relacionada à animação. Usaremos ele apenas para armazenar dados. Para adicionar o `AnimationFrame` classe, o botão direito do mouse ou CTRL + clique em de **WalkingGame** compartilhado do projeto e selecione **Adicionar > novo arquivo...** Insira o nome **AnimationFrame** e clique no **New** botão. Vamos modificar o `AnimationFrame.cs` arquivo para que ele contém o código a seguir:


```csharp
using System;
using Microsoft.Xna.Framework;

namespace WalkingGame
{
    public class AnimationFrame
    {
        public Rectangle SourceRectangle { get; set; }
        public TimeSpan Duration { get; set; }
    }
}
```

O `AnimationFrame` classe contém duas informações:

- `SourceRectangle` – Define a área do `Texture2D` que será exibido quando o `AnimationFrame`. Esse valor é medido em pixels, com o superior esquerdo que está sendo (0, 0).
- `Duration` – Define por quanto tempo uma `AnimationFrame` é exibida quando usada em um `Animation`.

### <a name="defining-animation"></a>Definindo a animação

O `Animation` classe conterá um `List<AnimationFrame>` , bem como a lógica para alternar qual quadro é exibido no momento de acordo com o tempo passou.

Para adicionar o `Animation` classe, o botão direito do mouse ou CTRL + clique em de **WalkingGame** compartilhado do projeto e selecione **Adicionar > novo arquivo...** . Insira o nome **animação** e clique no **New** botão. Vamos modificar o `Animation.cs` arquivo para que ele contém o código a seguir:


```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using Microsoft.Xna.Framework;

namespace WalkingGame
{
    public class Animation
    {
        List<AnimationFrame> frames = new List<AnimationFrame>();
        TimeSpan timeIntoAnimation;

        TimeSpan Duration
        {
            get
            {
                double totalSeconds = 0;
                foreach (var frame in frames)
                {
                    totalSeconds += frame.Duration.TotalSeconds;
                }

                return TimeSpan.FromSeconds (totalSeconds);
            }
        }

        public void AddFrame(Rectangle rectangle, TimeSpan duration)
        {
            AnimationFrame newFrame = new AnimationFrame()
            {
                SourceRectangle = rectangle,
                Duration = duration
            };

            frames.Add(newFrame);
        }

        public void Update(GameTime gameTime)
        {
            double secondsIntoAnimation = 
                timeIntoAnimation.TotalSeconds + gameTime.ElapsedGameTime.TotalSeconds;


            double remainder = secondsIntoAnimation % Duration.TotalSeconds;

            timeIntoAnimation = TimeSpan.FromSeconds (remainder);
        }
    }
}
```

Vamos examinar alguns detalhes sobre o `Animation` classe.

### <a name="frames-list"></a>Lista de quadros

O `frames` membro é o que armazena os dados para nossa animação. Adicionará o código que instancia as animações `AnimationFrame` instâncias para o `frames` lista por meio do `AddFrame` método. Uma implementação mais completa pode oferecer `public` métodos ou propriedades para modificar `frames`, mas Limitaremos a funcionalidade de adição de quadros para este passo a passo.

### <a name="duration"></a>Duração

Duração retorna a duração total dos `Animation,` que é obtido, adicionando a duração de todas as independente `AnimationFrame` instâncias. Esse valor pode ser armazenado em cache se `AnimationFrame` eram um objeto imutável, mas como implementamos AnimationFrame como uma classe que pode ser alterada após ser adicionado a animação, precisamos calcular esse valor sempre que a propriedade é acessada.

### <a name="update"></a>Atualização

O `Update` método deve ser chamado a cada quadro (ou seja, toda vez que todo o jogo é atualizado). Sua finalidade é aumentar a `timeIntoAnimation` membro que é usado para retornar o quadro atualmente exibido. A lógica em `Update` impede que o `timeIntoAnimation` nunca sejam maiores do que a duração de toda a animação.

Uma vez que usaremos o `Animation` classe para exibir uma animação de caminhar e em seguida, queremos ter essa animação se repetem quando ele atingiu o fim. Podemos pode fazer isso verificando se o `timeIntoAnimation` é maior do que o `Duration` valor. Nesse caso, ele será voltar ao início do ciclo e preservar o restante, resultando em uma animação de loop.

### <a name="obtaining-the-current-frames-rectangle"></a>Obtendo o retângulo do quadro atual

A finalidade de `Animation` classe é, por fim, fornecer um `Rectangle` que pode ser usado ao desenhar um sprite. Atualmente, o `Animation` classe contém a lógica para alterar o `timeIntoAnimation` membro, que usaremos para obter que `Rectangle` deve ser exibido. Faremos isso criando uma `CurrentRectangle` propriedade no `Animation` classe. Copie essa propriedade para o `Animation` classe:

```csharp
public Rectangle CurrentRectangle
{
    get
    {
        AnimationFrame currentFrame = null;

        // See if we can find the frame
        TimeSpan accumulatedTime = new TimeSpan();
        foreach(var frame in frames)
        {
            if (accumulatedTime + frame.Duration >= timeIntoAnimation)
            {
                currentFrame = frame;
                break;
            }
            else
            {
                accumulatedTime += frame.Duration;
            }
        }

        // If no frame was found, then try the last frame, 
        // just in case timeIntoAnimation somehow exceeds Duration
        if (currentFrame == null)
        {
            currentFrame = frames.LastOrDefault ();
        }

        // If we found a frame, return its rectangle, otherwise
        // return an empty rectangle (one with no width or height)
        if (currentFrame != null)
        {
            return currentFrame.SourceRectangle;
        }
        else
        {
            return Rectangle.Empty;
        }
    }
}
```

## <a name="adding-the-first-animation-to-characterentity"></a>Adicionar a primeira animação ao CharacterEntity

O `CharacterEntity` irá conter animações para percorrer e permanente, bem como uma referência ao atual `Animation` que está sendo exibido.

Primeiro, vamos adicionar nosso primeiro `Animation,` que usaremos para testar a funcionalidade como escrito até o momento. Vamos adicionar os seguintes membros à classe CharacterEntity:

```csharp
Animation walkDown;
Animation currentAnimation;
```

Em seguida, vamos definir o `walkDown` animação. Para fazer isso modificar o `CharacterEntity` construtor da seguinte maneira:

```csharp
public CharacterEntity (GraphicsDevice graphicsDevice)
{
    if (characterSheetTexture == null)
    {
        using (var stream = TitleContainer.OpenStream ("Content/charactersheet.png"))
        {
            characterSheetTexture = Texture2D.FromStream (graphicsDevice, stream);
        }
    }

    walkDown = new Animation ();
    walkDown.AddFrame (new Rectangle (0, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkDown.AddFrame (new Rectangle (16, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkDown.AddFrame (new Rectangle (0, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkDown.AddFrame (new Rectangle (32, 0, 16, 16), TimeSpan.FromSeconds (.25));
}
```

Como mencionado anteriormente, precisamos chamar `Animation.Update` para animações com base no tempo executar. Também é necessário atribuir a `currentAnimation`. Por agora vamos atribuir o `currentAnimation` para `walkDown`, mas estamos substituirá esse código mais tarde quando podemos implementar nossa lógica de movimentação. Vamos adicionar o `Update` método para `CharacterEntity` da seguinte maneira:


```csharp
public void Update(GameTime gameTime)
{
    // temporary - we'll replace this with logic based off of which way the
    // character is moving when we add movement logic
    currentAnimation = walkDown;

    currentAnimation.Update (gameTime);
}
```

Agora que temos o `currentAnimation` que está sendo atribuído e atualizados, podemos usar isso para executar nosso desenho. Vamos modificar `CharacterEntity.Draw` da seguinte maneira:

```csharp
public void Draw(SpriteBatch spriteBatch)
{
    Vector2 topLeftOfSprite = new Vector2 (this.X, this.Y);
    Color tintColor = Color.White;
    var sourceRectangle = currentAnimation.CurrentRectangle;

    spriteBatch.Draw(characterSheetTexture, topLeftOfSprite, sourceRectangle, Color.White);
}
```

A última etapa para obter o `CharacterEntity` animando é chamar o recém-adicionado `Update` método de `Game1`. Modificar `Game1.Update` da seguinte maneira:

```csharp
protected override void Update(GameTime gameTime)
{
    character.Update (gameTime);
    base.Update(gameTime);
}
```

Agora o `CharacterEntity` reproduzirá seu `walkDown` animação:

![](part2-images/image5.gif "Agora o CharacterEntity reproduzirá sua animação walkDown")

## <a name="adding-movement-to-the-character"></a>Adição de movimentação para o caractere

Em seguida, estaremos adicionando movimento para nossa personagem usando controles de toque. Quando o usuário toca na tela, o caractere será movido para o ponto em que a tela é atingida. Se não há toques forem detectados, o caractere dará suporte em vigor.

### <a name="defining-getdesiredvelocityfrominput"></a>Defining GetDesiredVelocityFromInput

Usaremos do MonoGame `TouchPanel` classe, que fornece informações sobre o estado atual da tela sensível ao toque. Vamos adicionar um método que verificará o `TouchPanel` e velocidade desejada do nosso do caractere de retorno:


```csharp
Vector2 GetDesiredVelocityFromInput()
{
    Vector2 desiredVelocity = new Vector2 ();

    TouchCollection touchCollection = TouchPanel.GetState();

    if (touchCollection.Count > 0)
    {
        desiredVelocity.X = touchCollection [0].Position.X - this.X;
        desiredVelocity.Y = touchCollection [0].Position.Y - this.Y;

        if (desiredVelocity.X != 0 || desiredVelocity.Y != 0)
        {
            desiredVelocity.Normalize();
            const float desiredSpeed = 200;
            desiredVelocity *= desiredSpeed;
        }
    }

    return desiredVelocity;
}
```

O `TouchPanel.GetState` método retorna um `TouchCollection` que contém informações sobre onde o usuário está tocando a tela. Se o usuário não está tocando a tela, em seguida, a `TouchCollection` estará vazia, caso em que nós não deve mover o caractere.

Se o usuário toca a tela, mudaremos o caractere para o primeiro toque, em outras palavras, o `TouchLocation` no índice 0. Inicialmente, vamos definir a velocidade desejada para igualar a diferença entre o local do caractere e o local do toque de primeira:

```csharp
        desiredVelocity.X = touchCollection [0].Position.X - this.X;
        desiredVelocity.Y = touchCollection [0].Position.Y - this.Y;
```

O que vem a seguir é um pouco de matemática que manterá o caractere de movimentação na mesma velocidade. Para ajudar a explicar por que isso é importante, vamos considerar uma situação em que o usuário toca os 500 pixels de tela para longe de onde se encontra o caractere. A primeira linha onde `desiredVelocity.X` é conjunto atribuiria um valor de 500. No entanto, se o usuário foram tocam a tela em uma distância de apenas 100 unidades de caractere, em seguida, a `desiredVelocity.X` seria definido como 100. O resultado seria que velocidade do movimento do caractere responderia como distante o ponto de toque é de caractere. Como queremos que o caractere para sempre mover na mesma velocidade, precisamos modificar o desiredVelocity.

O `if (desiredVelocity.X != 0 || desiredVelocity.Y != 0)` instrução está verificando se a velocidade é de não-zero – em outras palavras, ele verifica para certificar-se de que o usuário não toca mesmo lugar como a posição atual do caractere. Se não, em seguida, precisamos definir a velocidade do caractere seja constante, independentemente de como longe o toque é. Podemos fazer isso ao normalizar o vetor de velocidade que resulta em que ele está sendo um comprimento de 1. Um vetor de velocidade de 1 significa que o caractere serão movidos em 1 pixel por segundo. Podemos vai acelerar isso multiplicando o valor pela velocidade desejada de 200.


### <a name="applying-velocity-to-position"></a>Aplicação de velocidade para posição

A velocidade retornada de `GetDesiredVelocityFromInput` precisa ser aplicada para o caractere `X` e `Y` valores tenha efeito em tempo de execução. Vamos modificar o `Update` método da seguinte maneira:

```csharp
public void Update(GameTime gameTime)
{

    var velocity = GetDesiredVelocityFromInput ();

    this.X += velocity.X * (float)gameTime.ElapsedGameTime.TotalSeconds;
    this.Y += velocity.Y * (float)gameTime.ElapsedGameTime.TotalSeconds;

    // temporary - we'll replace this with logic based off of which way the
    // character is moving when we add movement logic
    currentAnimation = walkDown;

    currentAnimation.Update (gameTime);
}
```

Aqui, implementamos é chamado *com base no tempo* movimentação (em oposição a *baseados em quadro* movimentação). Com base no tempo de movimentação multiplica um valor de velocidade (no nosso caso, os valores armazenados na `velocity` variável) por quanto tempo se passou desde a última atualização, que é armazenada em `gameTime.ElapsedGameTime.TotalSeconds`. Se o jogo é executada em menos quadros por segundo, o tempo decorrido entre quadros sobe – o resultado final é que os objetos usando o movimento com base no tempo sempre serão movidos na mesma velocidade, independentemente da taxa de quadros.

Se executarmos nosso jogo agora, veremos que o caractere está mudando para o local de toque:

![](part2-images/image6.gif "O caractere está se movendo em direção a localização de toque")

## <a name="matching-movement-and-animation"></a>Correspondência de animação e movimentação

Assim que tivermos a nossa personagem mover e executar uma única animação, podemos pode definir o restante de nosso animações e usá-los para refletir a movimentação do caractere. Quando terminar de temos oito animações no total:

- Animações para caminhando para cima, para baixo, esquerda e direita
- Animações para uma posição ainda e voltada para cima para baixo, esquerda e direita

### <a name="defining-the-rest-of-the-animations"></a>Definindo o restante das animações

Vamos primeiro adicionar o `Animation` instâncias para o `CharacterEntity` classe para todos os nossos animações no mesmo local em que adicionamos `walkDown`. Depois que podemos fazer isso, o `CharacterEntity` terá o seguinte `Animation` membros:

```csharp
Animation walkDown;
Animation walkUp;
Animation walkLeft;
Animation walkRight;

Animation standDown;
Animation standUp;
Animation standLeft;
Animation standRight;

Animation currentAnimation;
```

Agora, vamos definir as animações no `CharacterEntity` construtor da seguinte maneira:

```csharp
public CharacterEntity (GraphicsDevice graphicsDevice)
{
    if (characterSheetTexture == null)
    {
        using (var stream = TitleContainer.OpenStream ("Content/charactersheet.png"))
        {
            characterSheetTexture = Texture2D.FromStream (graphicsDevice, stream);
        }
    }

    walkDown = new Animation ();
    walkDown.AddFrame (new Rectangle (0, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkDown.AddFrame (new Rectangle (16, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkDown.AddFrame (new Rectangle (0, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkDown.AddFrame (new Rectangle (32, 0, 16, 16), TimeSpan.FromSeconds (.25));

    walkUp = new Animation ();
    walkUp.AddFrame (new Rectangle (144, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkUp.AddFrame (new Rectangle (160, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkUp.AddFrame (new Rectangle (144, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkUp.AddFrame (new Rectangle (176, 0, 16, 16), TimeSpan.FromSeconds (.25));

    walkLeft = new Animation ();
    walkLeft.AddFrame (new Rectangle (48, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkLeft.AddFrame (new Rectangle (64, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkLeft.AddFrame (new Rectangle (48, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkLeft.AddFrame (new Rectangle (80, 0, 16, 16), TimeSpan.FromSeconds (.25));

    walkRight = new Animation ();
    walkRight.AddFrame (new Rectangle (96, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkRight.AddFrame (new Rectangle (112, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkRight.AddFrame (new Rectangle (96, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkRight.AddFrame (new Rectangle (128, 0, 16, 16), TimeSpan.FromSeconds (.25));

    // Standing animations only have a single frame of animation:
    standDown = new Animation ();
    standDown.AddFrame (new Rectangle (0, 0, 16, 16), TimeSpan.FromSeconds (.25));

    standUp = new Animation ();
    standUp.AddFrame (new Rectangle (144, 0, 16, 16), TimeSpan.FromSeconds (.25));

    standLeft = new Animation ();
    standLeft.AddFrame (new Rectangle (48, 0, 16, 16), TimeSpan.FromSeconds (.25));

    standRight = new Animation ();
    standRight.AddFrame (new Rectangle (96, 0, 16, 16), TimeSpan.FromSeconds (.25));
}
```

Devemos observar que o código acima foi adicionado para o `CharacterEntity` construtor para manter este passo a passo mais curta. Jogos geralmente serão separar a definição de animações de caractere em suas próprias classes ou carregar essas informações de um formato de dados como XML ou JSON.

Em seguida, podemos ajustará a lógica para usar as animações de acordo com a direção que está se movendo o caractere ou de acordo com a última animação se o caractere apenas foi interrompido. Para fazer isso, vamos modificar o `Update` método:


```csharp
public void Update(GameTime gameTime)
{
    var velocity = GetDesiredVelocityFromInput ();

    this.X += velocity.X * (float)gameTime.ElapsedGameTime.TotalSeconds;
    this.Y += velocity.Y * (float)gameTime.ElapsedGameTime.TotalSeconds;


    if (velocity != Vector2.Zero)
    {
        bool movingHorizontally = Math.Abs (velocity.X) > Math.Abs (velocity.Y);
        if (movingHorizontally)
        {
            if (velocity.X > 0)
            {
                currentAnimation = walkRight;
            }
            else
            {
                currentAnimation = walkLeft;
            }
        }
        else
        {
            if (velocity.Y > 0)
            {
                currentAnimation = walkDown;
            }
            else
            {
                currentAnimation = walkUp;
            }
        }
    }
    else
    {
        // If the character was walking, we can set the standing animation
        // according to the walking animation that is playing:
        if (currentAnimation == walkRight)
        {
            currentAnimation = standRight;
        }
        else if (currentAnimation == walkLeft)
        {
            currentAnimation = standLeft;
        }
        else if (currentAnimation == walkUp)
        {
            currentAnimation = standUp;
        }
        else if (currentAnimation == walkDown)
        {
            currentAnimation = standDown;
        }
        else if (currentAnimation == null)
        {
            currentAnimation = standDown;
        }

        // if none of the above code hit then the character
        // is already standing, so no need to change the animation.
    }

    currentAnimation.Update (gameTime);
}
```

O código para alternar as animações é dividido em dois blocos. O primeiro verifica se a velocidade não é igual a `Vector2.Zero` – isso nos diz se o caractere está se movendo. Se o caractere está mudando, podemos ver o `velocity.X` e `velocity.Y` valores para determinar qual caminhar animação reproduzir.

Se o caractere não está se movendo, queremos definir o caractere `currentAnimation` a uma animação de uma posição – mas estamos apenas fazê-lo se o `currentAnimation` é uma movimentação de animação ou se uma animação não tiver sido definida. Se o `currentAnimation` não é uma das quatro caminhar animações, em seguida, o caractere já está aguardando, portanto, não precisamos alterar `currentAnimation`.

O resultado desse código é que o caractere animar corretamente ao mover e, em seguida, enfrentam a direção do último visitava quando ele é interrompido:

![](part2-images/image7.gif "O resultado desse código é que o caractere animar corretamente ao mover e, em seguida, enfrentam a direção do último visitava quando ele é interrompido")

## <a name="summary"></a>Resumo

Este passo a passo mostrou como trabalhar com MonoGame para criar uma plataforma cruzada jogo com sprites, objetos em movimento, detecção de entrada e animação. Ele aborda a criação de uma classe de animação para fins gerais. Ele também mostrou como criar uma entidade de caracteres para organizar a lógica de código.

## <a name="related-links"></a>Links relacionados

- [Recurso de imagem CharacterSheet (amostra)](https://github.com/xamarin/mobile-samples/blob/master/WalkingGameMG/Resources/charactersheet.png?raw=true)
- [Movimentação de jogo completo (exemplo)](https://developer.xamarin.com/samples/mobile/WalkingGameMG/)
