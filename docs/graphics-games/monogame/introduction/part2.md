---
title: Parte 2 – implementando o WalkingGame
description: Este tutorial mostra como adicionar a lógica e o conteúdo do jogo a um projeto monogames vazio para criar uma demonstração de um sprite animado movendo com a entrada por toque.
ms.prod: xamarin
ms.assetid: F0622A01-DE7F-451A-A51F-129876AB6FFD
author: conceptdev
ms.author: crdun
ms.date: 03/28/2017
ms.openlocfilehash: 7c7b58266b4f5168fdb231258390fa64278963f8
ms.sourcegitcommit: f255aa286bd52e8a80ffa620c2e93c97f069f8ec
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/31/2019
ms.locfileid: "68680950"
---
# <a name="part-2--implementing-the-walkinggame"></a>Parte 2 – implementando o WalkingGame

_Este tutorial mostra como adicionar a lógica e o conteúdo do jogo a um projeto monogames vazio para criar uma demonstração de um sprite animado movendo com a entrada por toque._

As partes anteriores deste passo a passos mostraram como criar projetos monogames vazios. Vamos criar nessas partes anteriores fazendo uma demonstração simples do jogo. Este artigo contém as seguintes seções:

- Descompactando nosso conteúdo do jogo
- Visão geral da classe monogame
- Renderizando nosso primeiro Sprite
- Criando o CharacterEntity
- Adicionando CharacterEntity ao jogo
- Criando a classe Animation
- Adicionando a primeira animação a CharacterEntity
- Adicionando movimento ao caractere
- Movimentação e animação correspondentes


## <a name="unzipping-our-game-content"></a>Descompactando nosso conteúdo do jogo

Antes de começarmos a escrever o código, queremos descompactar nosso *conteúdo*do jogo. Os desenvolvedores de jogos geralmente usam o *conteúdo* do termo para se referir a arquivos que não são de código, que geralmente são criados por artistas visuais, designers de jogos ou designers de áudio. Tipos comuns de conteúdo incluem arquivos usados para exibir elementos visuais, reproduzir som ou controlar o comportamento de ia (inteligência artificial). Do conteúdo da perspectiva de uma equipe de desenvolvimento de jogos geralmente é criado por não programadores.

O conteúdo usado aqui pode ser encontrado [no GitHub](https://github.com/xamarin/mobile-samples/blob/master/WalkingGameMG/Resources/charactersheet.png?raw=true). Precisaremos desses arquivos baixados em um local que acessaremos mais tarde neste passo a passos.

## <a name="monogame-class-overview"></a>Visão geral da classe monogame

Para iniciantes, exploraremos as classes de monojogo usadas na renderização básica:

- `SpriteBatch`– usado para desenhar gráficos 2D na tela. *Sprites* são elementos visuais 2D que são usados para exibir imagens na tela. O `SpriteBatch` objeto pode desenhar um único Sprite por vez entre seus `Begin` métodos e `End` , ou vários sprites podem ser agrupados juntos ou *em lote*.
- `Texture2D`– representa um objeto de imagem em tempo de execução. `Texture2D`Geralmente, as instâncias são criadas a partir de formatos de arquivo, como. png ou. bmp, embora elas também possam ser criadas dinamicamente no tempo de execução. `Texture2D`as instâncias são usadas durante a `SpriteBatch` renderização com instâncias.
- `Vector2`– representa uma posição em um sistema de coordenadas 2D que é geralmente usado para posicionar objetos visuais. O monogames também `Vector3` inclui `Vector4` e `Vector2` , mas usaremos apenas neste passo a passos.
- `Rectangle`– uma área de quatro lados com posição, largura e altura. Vamos usar isso para definir qual parte do nosso `Texture2D` para renderizar quando começarmos a trabalhar com animações.

Também devemos observar que o monogames não é mantido pela Microsoft – apesar de seu namespace. O `Microsoft.Xna` namespace é usado em monogames para facilitar a migração de projetos do XNA existentes para o monogame.

## <a name="rendering-our-first-sprite"></a>Renderizando nosso primeiro Sprite

Em seguida, vamos desenhar um único sprite na tela para mostrar como executar a renderização 2D no monogame.

### <a name="creating-a-texture2d"></a>Criando um Texture2D

Precisamos criar uma `Texture2D` instância para usar ao renderizar nosso sprite. Todo o conteúdo do jogo é, em última instância, contido em uma pasta chamada **conteúdo,** localizada no projeto específico da plataforma. Projetos compartilhados por jogo não podem conter conteúdo, pois o conteúdo deve usar ações de compilação específicas para a plataforma. A pasta de conteúdo pode ser encontrada no projeto do iOS e dentro da pasta ativos no projeto do Android.

Para adicionar o conteúdo do jogo, clique com o botão direito do mouse na pasta **conteúdo** e selecione **Adicionar > Adicionar arquivos...** Navegue até o local onde o arquivo Content. zip foi extraído e selecione o arquivo **charactersheet. png** . Se for perguntado sobre como adicionar o arquivo à pasta, devemos selecionar a opção de **cópia** :

![](part2-images/image1.png "Se for perguntado sobre como adicionar o arquivo à pasta, selecione a opção de cópia")

A pasta de conteúdo agora contém o arquivo charactersheet. png:

![](part2-images/image2.png "A pasta de conteúdo agora contém o arquivo charactersheet. png")

Em seguida, vamos adicionar o código para carregar o arquivo charactersheet. png e criar `Texture2D`um. Para fazer isso, abra `Game1.cs` o arquivo e adicione o seguinte campo à classe Game1.cs:

```csharp
Texture2D characterSheetTexture;
```

Em seguida, criaremos o `characterSheetTexture` no método.`LoadContent` Antes de qualquer `LoadContent` método de modificação ter esta aparência:

```csharp
protected override void LoadContent()
{
    // Create a new SpriteBatch, which can be used to draw textures.
    spriteBatch = new SpriteBatch(GraphicsDevice);
    // TODO: use this.Content to load your game content here
}
```

Devemos observar que o projeto padrão já instancia a `spriteBatch` instância para nós. Usaremos isso mais tarde, mas não adicionaremos nenhum código adicional para preparar o `spriteBatch` para uso. Por outro lado, nosso `spriteSheetTexture` requer inicialização, portanto, o inicializaremos depois que o `spriteBatch` for criado:

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

Agora que temos uma `SpriteBatch` instância e uma `Texture2D` instância, podemos adicionar `Game1.Draw` nosso código de renderização ao método:

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

A execução do jogo agora mostra um único Sprite exibindo a textura criada em charactersheet. png:

![](part2-images/image3.png "A execução do jogo agora mostra um único Sprite exibindo a textura criada em charactersheet. png")

## <a name="creating-the-characterentity"></a>Criando o CharacterEntity

Até agora, adicionamos código para renderizar um único sprite na tela; no entanto, se desenvolvermos um jogo completo sem criar nenhuma outra classe, teríaremos problemas de organização de código.

### <a name="what-is-an-entity"></a>O que é uma entidade?

Um padrão comum para organizar o código de jogo é criar uma nova classe para cada tipo de *entidade* de jogo. Uma entidade no desenvolvimento de jogos é um objeto que pode conter algumas das seguintes características (nem todas são necessárias):

- Um elemento visual, como um Sprite, um texto ou um modelo 3D
- O comportamento da física ou de cada quadro, como uma unidade de patrulha, um caminho definido ou um caractere de jogador respondendo à entrada
- Pode ser criado e destruído dinamicamente, como uma reinicialização que aparece e sendo coletada pelo jogador

Os sistemas da organização da entidade podem ser complexos, e muitos mecanismos de jogo oferecem classes para ajudar a gerenciar entidades. Vamos implementar um sistema de entidades muito simples, portanto, vale a pena observar que os jogos completos geralmente exigem mais organização na parte do desenvolvedor.


### <a name="defining-the-characterentity"></a>Definindo o CharacterEntity

Nossa entidade, que chamaremos `CharacterEntity`, terá as seguintes características:

- A capacidade de carregar seus próprios`Texture2D`
- A capacidade de se renderizar, incluindo o que contém os métodos de chamada para atualizar a animação de movimentação
- `X`e propriedades Y para controlar a posição do caractere.
- A capacidade de se atualizar – especificamente, para ler valores da tela sensível ao toque e ajustar a posição adequadamente.

Para adicionar o `CharacterEntity` ao nosso jogo, clique com o botão direito do mouse ou controle-clique no projeto **WalkingGame** e selecione **Adicionar > novo arquivo...** . Selecione a opção **classe vazia** e nomeie o novo arquivo **CharacterEntity**e clique em **novo**.

Primeiro, adicionaremos a capacidade para o `CharacterEntity` carregar um `Texture2D` , bem como para desenhar. Modificaremos o `CharacterEntity.cs` arquivo recém-adicionado da seguinte maneira:

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

O código acima adiciona a responsabilidade de posicionar, renderizar e carregar conteúdo `CharacterEntity`no. Vamos reservar um momento para destacar algumas considerações feitas no código acima.

### <a name="why-are-x-and-y-floats"></a>Por que os floats X e Y são flutuantes?

Os desenvolvedores que são novos na programação de jogos podem imaginar `float` por que o tipo está sendo usado `int` em `double`oposição a ou. O motivo é que um valor de 32 bits é mais comum para posicionamento em código de renderização de baixo nível. O tipo Double ocupa 64 bits para precisão, o que raramente é necessário para posicionar objetos. Embora uma diferença de 32 bits possa parecer insignificante, muitos jogos modernos incluem gráficos que exigem o processamento de dezenas de milhares de valores de posição em cada quadro (30 ou 60 vezes por segundo). Cortar a quantidade de memória que deve passar pelo pipeline de gráficos pela metade pode ter um impacto significativo no desempenho de um jogo.

O `int` tipo de dados pode ser uma unidade de medida apropriada para posicionamento, mas pode posicionar as limitações na forma como as entidades são posicionadas. Por exemplo, o uso de valores inteiros dificulta a implementação de uma movimentação suave para jogos que dão suporte ao zoom ou câmeras 3D (que são permitidas pelo `SpriteBatch`).

Por fim, veremos que a lógica que move nosso caractere pela tela fará isso usando os valores de tempo do jogo. O resultado da multiplicação da velocidade por quanto tempo você passou em um determinado quadro raramente resultará em um número inteiro, portanto, precisamos usar `float` para `X` e `Y`.

### <a name="why-is-charactersheettexture-static"></a>Por que o characterSheetTexture é estático?

`characterSheetTexture` A`Texture2D` instância é uma representação em tempo de execução do arquivo charactersheet. png. Em outras palavras, ele contém os valores de cor para cada pixel, como extraído do arquivo de origem **charactersheet. png** . Se o nosso jogo fosse criar duas `CharacterEntity` instâncias, cada uma delas precisaria de acesso às informações de charactersheet. png. Nessa situação, não queremos incorrer o custo de desempenho do carregamento de charactersheet. png duas vezes, nem desejamos ter memória de textura duplicada armazenada na RAM. O uso `static` de um campo nos permite compartilhar o `Texture2D` mesmo em `CharacterEntity` todas as instâncias.

Usar `static` Membros para a representação de conteúdo em tempo de execução é uma solução simples e não resolve problemas encontrados em jogos maiores, como descarregar conteúdo ao passar de um nível para outro. Soluções mais sofisticadas, que estão além do escopo deste passo a passos, incluem o uso do pipeline de conteúdo de monojogo ou a criação de um sistema de gerenciamento de conteúdo personalizado.

### <a name="why-is-spritebatch-passed-as-an-argument-instead-of-instantiated-by-the-entity"></a>Por que o SpriteBatch é passado como um argumento em vez de instanciado pela entidade?

O `Draw` método conforme implementado acima usa um `SpriteBatch` argumento, mas, por outro lado `characterSheetTexture` , o é instanciado `CharacterEntity`pelo.

O motivo disso é porque a renderização mais eficiente é possível quando `SpriteBatch` a mesma instância é usada para todas as `Draw` chamadas e quando todas `Draw` as chamadas estão sendo feitas entre um único conjunto de `Begin` chamadas `End` e. É claro que nosso jogo incluirá apenas uma única instância de entidade, mas jogos mais complicados se beneficiarão do padrão que permite que várias entidades `SpriteBatch` usem a mesma instância.


## <a name="adding-characterentity-to-the-game"></a>Adicionando CharacterEntity ao jogo

Agora que adicionamos nosso `CharacterEntity` código para a renderização, podemos substituir o código em `Game1.cs` para usar uma instância dessa nova entidade. Para fazer isso, vamos substituir o `Texture2D` campo por um `CharacterEntity` campo em `Game1`:

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

Também precisamos remover a `Texture2D` criação de `LoadContent` , já que isso agora é manipulado dentro de `CharacterEntity`nosso. `Game1.LoadContent`deve ter a seguinte aparência:

```csharp
protected override void LoadContent()
{
    // Create a new SpriteBatch, which can be used to draw textures.
    spriteBatch = new SpriteBatch(GraphicsDevice);
}
```

Vale a pena observar que, apesar de seu nome `LoadContent` , o método não é o único local em que o conteúdo pode ser carregado – muitos jogos implementam o carregamento de conteúdo em seu método Initialize, ou mesmo em seu código de atualização como conteúdo, podem não ser necessários até que o jogador atinja um determinado ponto do jogo.

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

Se executarmos o jogo, agora veremos o caractere. Como X e Y padrão como 0, o caractere é posicionado no canto superior esquerdo da tela:

![](part2-images/image4.png "Como X e Y padrão como 0, o caractere é posicionado no canto superior esquerdo da tela")

## <a name="creating-the-animation-class"></a>Criando a classe Animation

Atualmente, `CharacterEntity` nosso exibe o arquivo **charactersheet. png** completo. Essa organização de várias imagens em um arquivo é conhecida como uma *folha de Sprite*. Normalmente, um Sprite renderizará apenas uma parte da folha Sprite. Modificaremos o `CharacterEntity` para renderizar uma parte desse **charactersheet. png**, e essa parte será alterada ao longo do tempo para exibir uma animação de movimentação.

Criaremos a `Animation` classe para controlar a lógica e o estado da animação CharacterEntity. A classe Animation será uma classe geral que poderia ser usada para qualquer entidade, não apenas `CharacterEntity` para animações. Ultimate a `Animation` classe fornecerá um `Rectangle` que será `CharacterEntity` usado ao desenhar a si mesmo. Também criaremos uma `AnimationFrame` classe que será usada para definir a animação.


### <a name="defining-animationframe"></a>Definindo AnimationFrame

`AnimationFrame`não conterá nenhuma lógica relacionada à animação. Vamos usá-lo somente para armazenar dados. Para adicionar a `AnimationFrame` classe, clique com o botão direito do mouse ou controle-clique no projeto compartilhado **WalkingGame** e selecione **Adicionar > novo arquivo....** Insira o nome **AnimationFrame** e clique no botão **novo** . Modificaremos o `AnimationFrame.cs` arquivo para que ele contenha o seguinte código:


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

A `AnimationFrame` classe contém duas partes de informação:

- `SourceRectangle`– Define a área do `Texture2D` que será exibida `AnimationFrame`pelo. Esse valor é medido em pixels, com a parte superior esquerda sendo (0, 0).
- `Duration`– Define por quanto tempo `AnimationFrame` um é exibido quando usado em `Animation`um.

### <a name="defining-animation"></a>Definindo animação

A `Animation` classe conterá um `List<AnimationFrame>` , bem como a lógica para alternar qual quadro é exibido atualmente de acordo com o tempo que passou.

Para adicionar a `Animation` classe, clique com o botão direito do mouse ou controle-clique no projeto compartilhado **WalkingGame** e selecione **Adicionar > novo arquivo...** . Insira a **animação** de nome e clique no botão **novo** . Modificaremos o `Animation.cs` arquivo para que ele contenha o seguinte código:


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

Vamos examinar alguns dos detalhes da `Animation` classe.

### <a name="frames-list"></a>Lista de quadros

O `frames` membro é o que armazena os dados da nossa animação. O código que instancia as animações adicionará `AnimationFrame` instâncias `frames` à lista por meio do `AddFrame` método. Uma implementação mais completa pode oferecer `public` métodos ou propriedades para modificar `frames`, mas limitaremos a funcionalidade à adição de quadros para este passo a passos.

### <a name="duration"></a>Duração

Duration retorna a duração total do `Animation,` que é obtido adicionando-se a duração de todas as instâncias contidas. `AnimationFrame` Esse valor pode ser armazenado em cache `AnimationFrame` se fosse um objeto imutável, mas como implementamos AnimationFrame como uma classe que pode ser alterada após ser adicionada à animação, precisamos calcular esse valor sempre que a propriedade for acessada.

### <a name="update"></a>Atualização

O `Update` método deve ser chamado todos os quadros (ou seja, toda vez que o jogo inteiro for atualizado). Sua finalidade é aumentar o `timeIntoAnimation` membro que é usado para retornar o quadro atualmente exibido. A lógica no `Update` impede que `timeIntoAnimation` a partir de nunca seja maior do que a duração de toda a animação.

Como usaremos a `Animation` classe para exibir uma animação de movimentação, queremos que essa animação seja repetida quando atingir o final. Podemos fazer isso verificando se o `timeIntoAnimation` é maior do que o `Duration` valor. Nesse caso, ele voltará ao início e preservará o resto, resultando em uma animação de loop.

### <a name="obtaining-the-current-frames-rectangle"></a>Obtendo o retângulo do quadro atual

A finalidade da `Animation` classe é, por fim, fornecer `Rectangle` um que pode ser usado ao desenhar um Sprite. Atualmente, `Animation` a classe contém a lógica para `timeIntoAnimation` alterar o membro, que usaremos para obter o `Rectangle` que deve ser exibido. Faremos isso criando uma `CurrentRectangle` propriedade `Animation` na classe. Copie essa propriedade na `Animation` classe:

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

## <a name="adding-the-first-animation-to-characterentity"></a>Adicionando a primeira animação a CharacterEntity

O `CharacterEntity` conterá animações para movimentação e posição, bem como uma referência para a atual `Animation` sendo exibida.

Primeiro, vamos adicionar o nosso primeiro `Animation,` , que usaremos para testar a funcionalidade, conforme foi escrito até agora. Vamos adicionar os seguintes membros à classe CharacterEntity:

```csharp
Animation walkDown;
Animation currentAnimation;
```

Em seguida, vamos definir a `walkDown` animação. Para fazer isso, modifique `CharacterEntity` o construtor da seguinte maneira:

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

Como mencionado anteriormente, precisamos chamar `Animation.Update` a reprodução de animações baseadas em tempo. Também precisamos atribuir o `currentAnimation`. Por enquanto, atribuíremos o `currentAnimation` para `walkDown`, mas vamos substituir esse código posteriormente quando implementarmos nossa lógica de movimentação. Vamos adicionar o `Update` método ao `CharacterEntity` da seguinte maneira:


```csharp
public void Update(GameTime gameTime)
{
    // temporary - we'll replace this with logic based off of which way the
    // character is moving when we add movement logic
    currentAnimation = walkDown;

    currentAnimation.Update (gameTime);
}
```

Agora que temos o `currentAnimation` sendo atribuído e atualizado, podemos usá-lo para executar nosso desenho. Modificaremos `CharacterEntity.Draw` da seguinte maneira:

```csharp
public void Draw(SpriteBatch spriteBatch)
{
    Vector2 topLeftOfSprite = new Vector2 (this.X, this.Y);
    Color tintColor = Color.White;
    var sourceRectangle = currentAnimation.CurrentRectangle;

    spriteBatch.Draw(characterSheetTexture, topLeftOfSprite, sourceRectangle, Color.White);
}
```

A última etapa para obter a `CharacterEntity` animação é chamar o `Update` método recém-adicionado de `Game1`. Modifique `Game1.Update` da seguinte maneira:

```csharp
protected override void Update(GameTime gameTime)
{
    character.Update (gameTime);
    base.Update(gameTime);
}
```

Agora, `CharacterEntity` a `walkDown` animação será reproduzida:

![](part2-images/image5.gif "Agora, o CharacterEntity executará sua animação walkDown")

## <a name="adding-movement-to-the-character"></a>Adicionando movimento ao caractere

Em seguida, vamos adicionar movimento ao nosso caractere usando controles de toque. Quando o usuário toca na tela, o caractere se move para o ponto em que a tela é tocada. Se nenhum toque for detectado, o caractere permanecerá em vigor.

### <a name="defining-getdesiredvelocityfrominput"></a>Definindo GetDesiredVelocityFromInput

Usaremos a classe do `TouchPanel` jogo, que fornece informações sobre o estado atual da tela sensível ao toque. Vamos adicionar um método que irá verificar o e `TouchPanel` retornar a velocidade desejada do caractere:


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

O `TouchPanel.GetState` método retorna um `TouchCollection` que contém informações sobre onde o usuário está tocando na tela. Se o usuário não estiver tocando na tela, o `TouchCollection` estará vazio, caso em que não devemos mover o caractere.

Se o usuário estiver tocando na tela, moveremos o caractere para o primeiro toque, em outras palavras, `TouchLocation` no índice 0. Inicialmente, definiremos a velocidade desejada para igual à diferença entre o local do caractere e o local do primeiro toque:

```csharp
        desiredVelocity.X = touchCollection [0].Position.X - this.X;
        desiredVelocity.Y = touchCollection [0].Position.Y - this.Y;
```

O que vem a seguir é um pouco de matemática, o que manterá o caractere em movimento na mesma velocidade. Para ajudar a explicar por que isso é importante, vamos considerar uma situação em que o usuário está tocando na tela 500 pixels de distância de onde o caractere está localizado. A primeira linha em `desiredVelocity.X` que é definida atribuiria um valor de 500. No entanto, se o usuário estivesse tocando a tela em uma distância de apenas 100 unidades do caractere, o `desiredVelocity.X` seria definido como 100. O resultado seria que a velocidade de movimentação do caractere responderia até onde o ponto de toque é proveniente do caractere. Como queremos que o caractere sempre se mova na mesma velocidade, precisamos modificar o desiredVelocity.

A `if (desiredVelocity.X != 0 || desiredVelocity.Y != 0)` instrução está verificando se a velocidade é diferente de zero – em outras palavras, está verificando se o usuário não está se tocando no mesmo ponto que a posição atual do caractere. Caso contrário, precisamos definir a velocidade do caractere como constante, independentemente da distância do toque. Isso é feito normalizando o vetor de velocidade, que resulta em um comprimento de 1. Um vetor de velocidade de 1 significa que o caractere será movido em 1 pixel por segundo. Vamos acelerar isso multiplicando o valor pela velocidade desejada de 200.


### <a name="applying-velocity-to-position"></a>Aplicando a velocidade à posição

A velocidade retornada de `GetDesiredVelocityFromInput` precisa ser aplicada aos valores dos `X` caracteres e `Y` para ter qualquer efeito no tempo de execução. Modificaremos o método `Update` da seguinte maneira:

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

O que implementamos aqui é chamado de movimentação *baseada em tempo* (em oposição ao movimento *baseado em quadros* ). A movimentação baseada em tempo multiplica um valor de velocidade (em nosso caso, os valores armazenados `velocity` na variável) por quanto tempo passou desde a última atualização armazenada no. `gameTime.ElapsedGameTime.TotalSeconds` Se o jogo for executado em menos quadros por segundo, o tempo decorrido entre os quadros aumenta – o resultado final é que os objetos que usam movimento com base no tempo sempre serão movidos para a mesma velocidade, independentemente da taxa de quadros.

Se executarmos nosso jogo agora, veremos que o caractere está se movendo para o local do toque:

![](part2-images/image6.gif "O caractere está sendo movido para o local de toque")

## <a name="matching-movement-and-animation"></a>Movimentação e animação correspondentes

Assim que tivermos nosso caractere movendo e jogando uma única animação, podemos definir o restante de nossas animações e, em seguida, usá-las para refletir a movimentação do caractere. Quando terminar, teremos oito animações no total:

- Animações para movimentação, para baixo, para a esquerda e para a direita
- Animações que ainda estão em pé e voltadas para cima, para baixo, para a esquerda e para a direita

### <a name="defining-the-rest-of-the-animations"></a>Definindo o restante das animações

Primeiro, adicionaremos as `Animation` instâncias `CharacterEntity` à classe de todas as nossas animações no mesmo local em que adicionamos `walkDown`. Depois de fazer isso, o `CharacterEntity` terá os seguintes `Animation` Membros:

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

Agora, vamos definir as animações no `CharacterEntity` Construtor da seguinte maneira:

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

Devemos observar que o código acima foi adicionado ao `CharacterEntity` Construtor para manter este passo a passos mais curto. Normalmente, os jogos separarão a definição de animações de caracteres em suas próprias classes ou carregarão essas informações de um formato de dados como XML ou JSON.

Em seguida, ajustaremos a lógica para usar as animações de acordo com a direção em que o caractere está sendo movido ou de acordo com a última animação se o caractere acabou de ser interrompido. Para fazer isso, modificaremos o `Update` método:


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

O código para alternar as animações é dividido em dois blocos. O primeiro verifica se a velocidade não é igual a `Vector2.Zero` – isso nos informa se o caractere está sendo movido. Se o caractere estiver sendo movido, podemos examinar os `velocity.X` valores e `velocity.Y` para determinar qual animação de movimentação deve ser reproduzida.

Se o caractere não estiver sendo movido, queremos definir o caractere `currentAnimation` como uma animação de pé – mas só fazemos isso se o `currentAnimation` for uma animação de movimentação ou se uma animação não tiver sido definida. Se não `currentAnimation`for uma das quatro animações de movimentação, o caractere já estará posicionado, portanto, não precisaremos ser alterado. `currentAnimation`

O resultado desse código é que o caractere será animado corretamente durante a movimentação e, em seguida, a última direção que ele estava acompanhando ao parar:

![](part2-images/image7.gif "O resultado desse código é que o caractere será animado corretamente durante a movimentação e, em seguida, a última direção que ele estava acompanhando ao parar")

## <a name="summary"></a>Resumo

Este tutorial mostrou como trabalhar com monogame para criar um jogo de plataforma cruzada com sprites, movimentação de objetos, detecção de entrada e animação. Ele aborda a criação de uma classe de animação de uso geral. Ele também mostrou como criar uma entidade de caracteres para organizar a lógica do código.

## <a name="related-links"></a>Links relacionados

- [Recurso de imagem CharacterSheet (exemplo)](https://github.com/xamarin/mobile-samples/blob/master/WalkingGameMG/Resources/charactersheet.png?raw=true)
- [Jogo de movimentação concluído (exemplo)](https://docs.microsoft.com/samples/xamarin/mobile-samples/walkinggamemg/)
