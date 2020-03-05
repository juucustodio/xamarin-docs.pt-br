---
title: Usar CocosSharp em xamarin. Forms
description: CocosSharp pode ser usado para adicionar de forma precisa, imagem e processamento de texto a um aplicativo para visualização avançada
ms.prod: xamarin
ms.assetid: E0F404D5-5C6B-4288-92EC-78996C674E4E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/03/2016
ms.openlocfilehash: 8ba9e4b119384db401fc631f58c37a28cd2b8004
ms.sourcegitcommit: db422e33438f1b5c55852e6942c3d1d75dc025c4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/24/2020
ms.locfileid: "78292391"
---
# <a name="using-cocossharp-in-xamarinforms"></a>Usar CocosSharp em xamarin. Forms

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://github.com/xamarin/xamarin-forms-samples/tree/master/CocosSharpForms)

_CocosSharp pode ser usado para adicionar processamento preciso de forma, imagem e texto a um aplicativo para visualização avançada_

> [!VIDEO https://youtube.com/embed/eYCx63FeqVU]

**Evolua 2016: Ilhas Cocos in Xamarin. Forms**

## <a name="overview"></a>Visão geral

CocosSharp é uma tecnologia flexível e poderosa para exibição de elementos gráficos, leitura de entrada por toque, reprodução de áudio e gerenciamento de conteúdo. Este guia explica como adicionar CocosSharp a um aplicativo xamarin. Forms. Ele aborda o seguinte:

- [O que é o CocosSharp?](#what)
- [Adicionando os pacotes NuGet do CocosSharp](#nuget)
- [Walkthrough: adicionando CocosSharp a um aplicativo Xamarin. Forms](#add)

<a name="what" />

## <a name="what-is-cocossharp"></a>O que é CocosSharp?

O [CocosSharp](https://github.com/xamarin/docs-archive/blob/master/Docs/CocosSharp/index.md) é um mecanismo de jogo de software livre que está disponível na plataforma Xamarin.
CocosSharp é uma biblioteca de tempo de execução eficiente que inclui os seguintes recursos:

- Renderização de imagem usando a classe `CCSprite`
- Renderização de forma usando a classe `CCDrawNode`
- Lógica de cada quadro usando a classe `CCNode.Schedule`
- Gerenciamento de conteúdo (carregamento e descarregamento de recursos, como arquivos. png) usando o `CCTextureCache`
- Animações usando a classe `CCAction`

O foco principal do CocosSharp é simplificar a criação de jogos 2D de plataforma cruzada; No entanto, ele também pode ser uma ótima adição à aplicativos de formulário do Xamarin. Como jogos geralmente exigem controle preciso sobre visuais e renderização eficiente, CocosSharp pode ser usado para adicionar visualização avançada e efeitos para os aplicativos não é de jogo.

Xamarin. Forms é compilado em sistemas de interface do usuário nativos, específicos da plataforma. Por exemplo, [`Button`s](xref:Xamarin.Forms.Button) aparecem de modo diferente no Ios e no Android, e podem até mesmo diferir pela versão do sistema operacional. Por outro lado, CocosSharp não usa quaisquer objetos visuais específicos da plataforma, portanto, todos os objetos visuais parecem idênticos em todas as plataformas. É claro que, resolução e taxa de proporção diferem entre os dispositivos, e isso pode afetar como CocosSharp processa seus visuais. Esses detalhes serão discutidos posteriormente neste guia.

Informações mais detalhadas podem ser encontradas na [seção CocosSharp](https://github.com/xamarin/docs-archive/blob/master/Docs/CocosSharp/index.md).

<a name="nuget" />

## <a name="adding-the-cocossharp-nuget-packages"></a>Adicionando os pacotes NuGet do CocosSharp

Antes de usar CocosSharp, os desenvolvedores precisam fazer algumas adições ao seu projeto xamarin. Forms.
Este guia pressupõe que um projeto xamarin. Forms com um iOS, Android e o .NET Standard projeto de biblioteca.
Todo o código será gravado no projeto da biblioteca .NET Standard; No entanto, as bibliotecas devem ser adicionadas para projetos Android e iOS.

O pacote NuGet CocosSharp contém todos os objetos necessários para criar objetos CocosSharp.
O pacote NuGet CocosSharp. Forms inclui a classe `CocosSharpView`, que é usada para hospedar o CocosSharp no Xamarin. Forms.
Adicione o **CocosSharp. Forms** NuGet e o **CocosSharp** também serão adicionados automaticamente.
Para fazer isso, clique com o botão direito do mouse na pasta **pacotes** no projeto de biblioteca .net Standard e selecione **adicionar pacotes...** . Insira o termo de pesquisa **CocosSharp. Forms**, selecione **CocosSharp para Xamarin. Forms**e clique em **Adicionar pacote**.

![](cocossharp-images/image1.png "Add Packages Dialog")

Os pacotes NuGet **CocosSharp** e **CocosSharp. Forms** serão adicionados ao projeto:

![](cocossharp-images/image2.png "Packages Folder")

Repita as etapas acima para projetos específicos da plataforma (por exemplo, iOS e Android).

<a name="add" />

## <a name="walkthrough-adding-cocossharp-to-a-xamarinforms-app"></a>Passo a passo: Adicionando CocosSharp em um aplicativo xamarin. Forms

Siga estas etapas para adicionar uma exibição de CocosSharp simples para um aplicativo xamarin. Forms:

1. [Criando uma página de formulários do Xamarin](#1)
1. [Adicionando um CocosSharpView](#2)
1. [Criando o GameScene](#3)
1. [Adicionando um círculo](#4)
1. [Interagindo com CocosSharp](#5)

Depois de adicionar com êxito uma exibição CocosSharp a um aplicativo Xamarin. Forms, visite a [documentação do CocosSharp](https://github.com/xamarin/docs-archive/blob/master/Docs/CocosSharp/index.md) para saber mais sobre como criar conteúdo com o CocosSharp.

<a name="1" />

### <a name="1-creating-a-xamarin-forms-page"></a>1. criando uma página de formulários do Xamarin

CocosSharp pode ser hospedado em qualquer contêiner de xamarin. Forms. Este exemplo para esta página usa uma página chamada `HomePage`. `HomePage` é dividido pela metade por uma `Grid` para mostrar como o Xamarin. Forms e o CocosSharp podem ser renderizados simultaneamente na mesma página.

Primeiro, configure a página para que ela contenha uma `Grid` e duas instâncias de `Button`:

```csharp
public class HomePage : ContentPage
{
public HomePage ()
    {
        // This is the top-level grid, which will split our page in half
        var grid = new Grid ();
        this.Content = grid;
        grid.RowDefinitions = new RowDefinitionCollection {
            // Each half will be the same size:
            new RowDefinition{ Height = new GridLength(1, GridUnitType.Star)},
            new RowDefinition{ Height = new GridLength(1, GridUnitType.Star)},
        };
        CreateTopHalf (grid);
        CreateBottomHalf (grid);
    }
    void CreateTopHalf(Grid grid)
    {
        // We'll be adding our CocosSharpView here:
    }
    void CreateBottomHalf(Grid grid)
    {
        // We'll use a StackLayout to organize our buttons
        var stackLayout = new StackLayout();
        // The first button will move the circle to the left when it is clicked:
        var moveLeftButton = new Button {
            Text = "Move Circle Left"
        };
        stackLayout.Children.Add (moveLeftButton);

        // The second button will move the circle to the right when clicked:
        var moveCircleRight = new Button {
            Text = "Move Circle Right"
        };
        stackLayout.Children.Add (moveCircleRight);
        // The stack layout will be in the bottom half (row 1):

        grid.Children.Add (stackLayout, 0, 1);
    }
}
```

No iOS, o `HomePage` aparece conforme mostrado na imagem a seguir:

![](cocossharp-images/image3.png "HomePage Screenshot")

<a name="2" />

### <a name="2-adding-a-cocossharpview"></a>2. adicionando um CocosSharpView

A classe `CocosSharpView` é usada para inserir CocosSharp em um aplicativo Xamarin. Forms. Como `CocosSharpView` herda da classe [Xamarin. Forms. View](xref:Xamarin.Forms.View) , ela fornece uma interface familiar para layout e pode ser usada em contêineres de layout como [Xamarin. Forms. Grid](xref:Xamarin.Forms.Grid). Adicione um novo `CocosSharpView` ao projeto concluindo o método `CreateTopHalf`:

```csharp
void CreateTopHalf(Grid grid)
{
    // This hosts our game view.
    var gameView = new CocosSharpView () {
        // Notice it has the same properties as other XamarinForms Views
        HorizontalOptions = LayoutOptions.FillAndExpand,
        VerticalOptions = LayoutOptions.FillAndExpand,
        // This gets called after CocosSharp starts up:
        ViewCreated = HandleViewCreated
    };
    // We'll add it to the top half (row 0)
    grid.Children.Add (gameView, 0, 0);
}
```

A inicialização do CocosSharp não é imediata, portanto, registre um evento para quando a `CocosSharpView` tiver concluído sua criação. Faça isso no método de `HandleViewCreated`:

```csharp
void HandleViewCreated (object sender, EventArgs e)
{
    var gameView = sender as CCGameView;
    if (gameView != null)
    {
        // This sets the game "world" resolution to 100x100:
        gameView.DesignResolution = new CCSizeI (100, 100);
        // GameScene is the root of the CocosSharp rendering hierarchy:
        gameScene = new GameScene (gameView);
        // Starts CocosSharp:
        gameView.RunWithScene (gameScene);
    }
}
```

O método `HandleViewCreated` tem dois detalhes importantes que vamos examinar. A primeira é a classe `GameScene`, que será criada na próxima seção. É importante observar que o aplicativo não será compilado até que o `GameScene` seja criado e a referência de instância de `gameScene` seja resolvida.

O segundo detalhe importante é a propriedade `DesignResolution`, que define a área visível do jogo para objetos CocosSharp. A propriedade `DesignResolution` será examinada após a criação de `GameScene`.

<a name="3" />

### <a name="3-creating-the-gamescene"></a>3. criando o GameScene

A classe `GameScene` herda do `CCScene`da CocosSharp. `GameScene` é o primeiro ponto em que lidamos puramente com CocosSharp. O código contido no `GameScene` funcionará em qualquer aplicativo CocosSharp, independentemente de ser hospedado em um projeto Xamarin. Forms ou não.

A classe `CCScene` é a raiz Visual de toda a renderização CocosSharp. Qualquer objeto CocosSharp visível deve estar contido em um `CCScene`. Mais especificamente, os objetos visuais devem ser adicionados a instâncias de `CCLayer`, e essas instâncias de `CCLayer` devem ser adicionadas a uma `CCScene`.

O gráfico a seguir pode ajudar a visualizar uma hierarquia de CocosSharp típica:

![](cocossharp-images/image4.png "Typical CocosSharp Hierarchy")

Somente um `CCScene` pode estar ativo ao mesmo tempo. A maioria dos jogos usa várias instâncias de `CCLayer` para classificar o conteúdo, mas nosso aplicativo usa apenas um. Da mesma forma, a maioria dos jogos usar múltiplos objetos visuais, mas temos que apenas um em nosso aplicativo. Uma discussão mais detalhada sobre a hierarquia visual do CocosSharp pode ser encontrada no [passo a passos BouncingGame](https://github.com/xamarin/docs-archive/blob/master/Docs/CocosSharp/bouncing-game.md).

Inicialmente, a classe `GameScene` ficará quase vazia – vamos criá-la para atender à referência em `HomePage`. Adicione uma nova classe ao seu projeto de biblioteca .NET Standard chamada `GameScene`. Ele deve herdar da classe `CCScene` da seguinte maneira:

```csharp
public class GameScene : CCScene
{
    public GameScene (CCGameView gameView) : base(gameView)
    {

    }
}
```

Agora que `GameScene` está definido, podemos retornar para `HomePage` e adicionar um campo:

```csharp
// Keep the GameScene at class scope
// so the button click events can access it:
GameScene gameScene;
```

Agora podemos compilar nosso projeto e executá-lo para ver CocosSharp em execução. Não adicionamos nada ao nosso `GameScene,` para que a metade superior da nossa página seja preta – a cor padrão de uma cena CocosSharp:

![](cocossharp-images/image5.png "Blank GameScene")

<a name="4" />

### <a name="4-adding-a-circle"></a>4. adicionando um círculo

O aplicativo atualmente tem uma instância em execução do mecanismo CocosSharp, exibindo uma `CCScene`vazia. Em seguida, vamos adicionar um objeto visual: um círculo. A classe `CCDrawNode` pode ser usada para desenhar uma variedade de formas geométricas, conforme descrito na [geometria de desenho com o guia CCDrawNode](https://github.com/xamarin/docs-archive/blob/master/Docs/CocosSharp/ccdrawnode.md).

Adicione um círculo à nossa classe `GameScene` e instancie-a no construtor, conforme mostrado no código a seguir:

```csharp
public class GameScene : CCScene
{
    CCDrawNode circle;
    public GameScene (CCGameView gameView) : base(gameView)
    {
        var layer = new CCLayer ();
        this.AddLayer (layer);
        circle = new CCDrawNode ();
        layer.AddChild (circle);
        circle.DrawCircle (
            // The center to use when drawing the circle,
            // relative to the CCDrawNode:
            new CCPoint (0, 0),
            radius:15,
            color:CCColor4B.White);
        circle.PositionX = 20;
        circle.PositionY = 50;
    }
}
```

Executando o aplicativo agora mostra um círculo no lado esquerdo da área de exibição CocosSharp:

![](cocossharp-images/image6.png "Circle in GameScene")

#### <a name="understanding-designresolution"></a>Noções básicas sobre DesignResolution

Agora que um objeto visual CocosSharp é exibido, podemos investigar a propriedade `DesignResolution`.

A `DesignResolution` representa a largura e a altura da área CocosSharp para colocação e dimensionamento de objetos. A resolução real da área é medida em *pixels* , enquanto o `DesignResolution` é medido em *unidades*mundiais. O diagrama a seguir mostra a resolução de várias partes da exibição conforme exibido em um iPhone 5 com uma tela com resolução de 640 x 1136 pixels:

![](cocossharp-images/image7.png "iPhone 5s Design Resolution")

O diagrama acima exibe as dimensões de pixel no lado de fora da tela em texto preto. Unidades são exibidas no interior do diagrama no texto em branco. Aqui estão alguns detalhes importantes, exibidas acima:

- A origem da exibição CocosSharp está na parte inferior esquerda. Movendo para a direita aumenta o valor de X e mover para cima aumenta o valor de Y. Observe que o valor de Y é invertido em comparação com alguns outros mecanismos de layout 2D, onde (0,0) é o canto superior esquerdo da tela.
- O comportamento padrão do CocosSharp é manter a taxa de proporção do modo de exibição. Como a primeira linha da grade é maior que a altura, CocosSharp não preenche toda a largura de suas células, conforme mostrado pelo retângulo branco pontilhado. Esse comportamento pode ser alterado, conforme descrito no [guia lidando com várias resoluções no CocosSharp](https://github.com/xamarin/docs-archive/blob/master/Docs/CocosSharp/resolutions.md).
- Neste exemplo, CocosSharp manterá uma área de exibição de 100 unidades de largura e altura independentemente do tamanho ou taxa de proporção de seu dispositivo. Isso significa que o código pode assumir que X = 100 representa a extrema direita associada a CocosSharp exibir Manter layout consistente em todos os dispositivos.

#### <a name="ccdrawnode-details"></a>Detalhes de CCDrawNode

Nosso aplicativo simples usa a classe `CCDrawNode` para desenhar um círculo. Essa classe pode ser muito útil para aplicativos de negócios, pois ela fornece renderização baseada em vetor geometria – um recurso ausente do xamarin. Forms. Além dos círculos, a classe `CCDrawNode` pode ser usada para desenhar retângulos, linhas e polígonos personalizados. `CCDrawNode` também é fácil de usar, pois não requer o uso de arquivos de imagem (como. png). Uma discussão mais detalhada do CCDrawNode pode ser encontrada na [geometria de desenho com o guia do CCDrawNode](https://github.com/xamarin/docs-archive/blob/master/Docs/CocosSharp/ccdrawnode.md).

<a name="5" />

### <a name="5-interacting-with-cocossharp"></a>5. interagindo com CocosSharp

CocosSharp elementos visuais (como `CCDrawNode`) herdam da classe `CCNode`. `CCNode` fornece duas propriedades que podem ser usadas para posicionar um objeto em relação ao seu pai: `PositionX` e `PositionY`. Nosso código atualmente usa essas duas propriedades para posicionar o centro do círculo, conforme mostrado neste trecho de código:

```csharp
circle.PositionX = 20;
circle.PositionY = 50;
```

É importante observar que os objetos de CocosSharp são posicionados por valores de posição explícitos, em vez da maioria das exibições de xamarin. Forms, que são posicionadas automaticamente acordo com o comportamento de seus controles de layout pai.

Vamos adicionar código para permitir que o usuário clique em um dos dois botões para mover o círculo para a esquerda ou à direita por 10 unidades (não em pixels, uma vez que o círculo desenha no espaço de unidade do mundo CocosSharp). Primeiro, criaremos dois métodos públicos na classe `GameScene`:

```csharp
public void MoveCircleLeft()
{
    circle.PositionX -= 10;
}

public void MoveCircleRight()
{
    circle.PositionX += 10;
}
```

Em seguida, adicionaremos manipuladores aos dois botões em `HomePage` para responder a cliques. Quando terminar, nosso método de `CreateBottomHalf` conterá o seguinte código:

```csharp
void CreateBottomHalf(Grid grid)
{
    // We'll use a StackLayout to organize our buttons
    var stackLayout = new StackLayout();

    // The first button will move the circle to the left when it is clicked:
    var moveLeftButton = new Button {
        Text = "Move Circle Left"
    };
    moveLeftButton.Clicked += (sender, e) => gameScene.MoveCircleLeft ();
    stackLayout.Children.Add (moveLeftButton);

    // The second button will move the circle to the right when clicked:
    var moveCircleRight = new Button {
        Text = "Move Circle Right"
    };
    moveCircleRight.Clicked += (sender, e) => gameScene.MoveCircleRight ();
    stackLayout.Children.Add (moveCircleRight);

    // The stack layout will be in the bottom half (row 1):
    grid.Children.Add (stackLayout, 0, 1);
}
```

Agora, o círculo CocosSharp move em resposta a cliques. Podemos ver também claramente os limites da tela de CocosSharp movendo o círculo suficiente para a esquerda ou direita:

![](cocossharp-images/image8.png "GameScene with Moving Circle")

## <a name="summary"></a>Resumo

Este guia mostra como adicionar CocosSharp a um xamarin. Forms existente do projeto, como criar a interação entre o xamarin. Forms e CocosSharp e discute várias considerações ao criar layouts no CocosSharp.

O mecanismo de jogo CocosSharp oferece muita funcionalidade e a profundidade, portanto, este guia aborda apenas do que CocosSharp pode fazer. Os desenvolvedores interessados em ler mais sobre o CocosSharp podem encontrar muitos artigos no [arquivo morto do CocosSharp](https://github.com/xamarin/docs-archive/blob/master/Docs/CocosSharp/).

## <a name="related-links"></a>Links relacionados

- [CocosSharpForms (exemplo)](https://github.com/xamarin/xamarin-forms-samples/tree/master/CocosSharpForms)
