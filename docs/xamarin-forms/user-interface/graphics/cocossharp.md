---
title: Usando CocosSharp emXamarin.Forms
description: ''
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: cb2303eb91fe2aa332ed35131baa7f6dd3cfeff5
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/28/2020
ms.locfileid: "84129513"
---
# <a name="using-cocossharp-in-xamarinforms"></a>Usando CocosSharp emXamarin.Forms

[![Baixar exemplo ](~/media/shared/download.png) baixar o exemplo](https://github.com/xamarin/xamarin-forms-samples/tree/master/CocosSharpForms)

_CocosSharp pode ser usado para adicionar processamento preciso de forma, imagem e texto a um aplicativo para visualização avançada_

> [!VIDEO https://youtube.com/embed/eYCx63FeqVU]

**Evolua 2016: Ilhas Cocos inXamarin.Forms**

## <a name="overview"></a>Visão geral

O CocosSharp é uma tecnologia flexível e poderosa para a exibição de gráficos, a leitura de entrada por toque, a reprodução de áudio e o gerenciamento de conteúdo. Este guia explica como adicionar o CocosSharp a um Xamarin.Forms aplicativo. Ele abrange o seguinte:

- [O que é o CocosSharp?](#what)
- [Adicionando os pacotes NuGet do CocosSharp](#nuget)
- [Walkthrough: adicionando CocosSharp a um Xamarin.Forms aplicativo](#add)

<a name="what" />

## <a name="what-is-cocossharp"></a>O que é o CocosSharp?

O [CocosSharp](https://github.com/xamarin/docs-archive/blob/master/Docs/CocosSharp/index.md) é um mecanismo de jogo de software livre que está disponível na plataforma Xamarin.
CocosSharp é uma biblioteca com eficiência em tempo de execução que inclui os seguintes recursos:

- Renderização de imagem usando a `CCSprite` classe
- Renderização de forma usando a `CCDrawNode` classe
- Lógica de cada quadro usando a `CCNode.Schedule` classe
- Gerenciamento de conteúdo (carregamento e descarregamento de recursos, como arquivos. png) usando o`CCTextureCache`
- Animações usando a `CCAction` classe

O principal foco do CocosSharp é simplificar a criação de jogos 2D de plataforma cruzada; no entanto, também pode ser uma ótima adição aos aplicativos do Xamarin Form. Como os jogos normalmente exigem renderização eficiente e controle preciso sobre visuais, o CocosSharp pode ser usado para adicionar uma visualização avançada e efeitos a aplicativos que não são do jogo.

Xamarin.Formso se baseia em sistemas de interface do usuário nativos específicos da plataforma. Por exemplo, [ `Button` s](xref:Xamarin.Forms.Button) aparecem de modo diferente no Ios e no Android e podem até mesmo diferir pela versão do sistema operacional. Por outro lado, o CocosSharp não usa nenhum objeto visual específico da plataforma, de modo que todos os objetos visuais aparecem idênticos em todas as plataformas. Obviamente, a resolução e a taxa de proporção diferem entre os dispositivos, e isso pode afetar como o CocosSharp renderiza seus visuais. Esses detalhes serão discutidos posteriormente neste guia.

Informações mais detalhadas podem ser encontradas na [seção CocosSharp](https://github.com/xamarin/docs-archive/blob/master/Docs/CocosSharp/index.md).

<a name="nuget" />

## <a name="adding-the-cocossharp-nuget-packages"></a>Adicionando os pacotes NuGet do CocosSharp

Antes de usar o CocosSharp, os desenvolvedores precisam fazer algumas adições ao seu Xamarin.Forms projeto.
Este guia pressupõe um Xamarin.Forms projeto com um projeto de biblioteca do IOS, Android e .net Standard.
Todo o código será escrito no projeto de biblioteca de .NET Standard; no entanto, as bibliotecas devem ser adicionadas aos projetos iOS e Android.

O pacote NuGet CocosSharp contém todos os objetos necessários para criar objetos CocosSharp.
O pacote NuGet CocosSharp. Forms inclui a `CocosSharpView` classe, que é usada para hospedar o CocosSharp no Xamarin.Forms .
Adicione o **CocosSharp. Forms** NuGet e o **CocosSharp** também serão adicionados automaticamente.
Para fazer isso, clique com o botão direito do mouse na pasta **pacotes** no projeto de biblioteca .net Standard e selecione **adicionar pacotes...**. Insira o termo de pesquisa **CocosSharp. Forms**, selecione **CocosSharp Xamarin.Forms para **e clique em **Adicionar pacote**.

![](cocossharp-images/image1.png "Add Packages Dialog")

Os pacotes NuGet **CocosSharp** e **CocosSharp. Forms** serão adicionados ao projeto:

![](cocossharp-images/image2.png "Packages Folder")

Repita as etapas acima para projetos específicos da plataforma (como iOS e Android).

<a name="add" />

## <a name="walkthrough-adding-cocossharp-to-a-xamarinforms-app"></a>Walkthrough: adicionando CocosSharp a um Xamarin.Forms aplicativo

Siga estas etapas para adicionar uma exibição CocosSharp simples a um Xamarin.Forms aplicativo:

1. [Criando uma página de formulários do Xamarin](#1)
1. [Adicionando um CocosSharpView](#2)
1. [Criando o GameScene](#3)
1. [Adicionando um círculo](#4)
1. [Interagindo com CocosSharp](#5)

Depois de adicionar com êxito uma exibição do CocosSharp a um Xamarin.Forms aplicativo, visite a [documentação do CocosSharp](https://github.com/xamarin/docs-archive/blob/master/Docs/CocosSharp/index.md) para saber mais sobre como criar conteúdo com o CocosSharp.

<a name="1" />

### <a name="1-creating-a-xamarin-forms-page"></a>1. criando uma página de formulários do Xamarin

CocosSharp pode ser hospedado em qualquer Xamarin.Forms contêiner. Este exemplo para esta página usa uma página chamada `HomePage` . `HomePage`é dividido pela metade por um `Grid` para mostrar como Xamarin.Forms e CocosSharp pode ser renderizado simultaneamente na mesma página.

Primeiro, configure a página para que ela contenha uma `Grid` e duas `Button` instâncias:

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

A `CocosSharpView` classe é usada para inserir CocosSharp em um Xamarin.Forms aplicativo. Desde que `CocosSharpView` herda do [ Xamarin.Forms . ](xref:Xamarin.Forms.View)A classe View fornece uma interface familiar para layout e pode ser usada em contêineres de layout, como [ Xamarin.Forms . Grade](xref:Xamarin.Forms.Grid). Adicione um novo `CocosSharpView` ao projeto concluindo o `CreateTopHalf` método:

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

A inicialização do CocosSharp não é imediata, portanto, registre um evento para quando o `CocosSharpView` tiver concluído sua criação. Faça isso no `HandleViewCreated` método:

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

O `HandleViewCreated` método tem dois detalhes importantes que vamos examinar. A primeira é a `GameScene` classe, que será criada na próxima seção. É importante observar que o aplicativo não será compilado até que o `GameScene` seja criado e a `gameScene` referência de instância seja resolvida.

O segundo detalhe importante é a `DesignResolution` propriedade, que define a área visível do jogo para objetos CocosSharp. A `DesignResolution` propriedade será examinada após a criação `GameScene` .

<a name="3" />

### <a name="3-creating-the-gamescene"></a>3. criando o GameScene

A `GameScene` classe herda de CocosSharp `CCScene` . `GameScene`é o primeiro ponto em que lidamos puramente com CocosSharp. O código contido em funcionará `GameScene` em qualquer aplicativo CocosSharp, independentemente de ser hospedado em um Xamarin.Forms projeto ou não.

A `CCScene` classe é a raiz Visual de toda a renderização CocosSharp. Qualquer objeto CocosSharp visível deve estar contido em um `CCScene` . Mais especificamente, os objetos visuais devem ser adicionados a `CCLayer` instâncias e essas `CCLayer` instâncias devem ser adicionadas a um `CCScene` .

O grafo a seguir pode ajudar a visualizar uma hierarquia típica do CocosSharp:

![](cocossharp-images/image4.png "Typical CocosSharp Hierarchy")

Apenas um `CCScene` pode estar ativo ao mesmo tempo. A maioria dos jogos usa várias `CCLayer` instâncias para classificar conteúdo, mas nosso aplicativo usa apenas um. Da mesma forma, a maioria dos jogos usa vários objetos visuais, mas temos apenas um em nosso aplicativo. Uma discussão mais detalhada sobre a hierarquia visual do CocosSharp pode ser encontrada no [passo a passos BouncingGame](https://github.com/xamarin/docs-archive/blob/master/Docs/CocosSharp/bouncing-game.md).

Inicialmente, a `GameScene` classe estará quase vazia – vamos criá-la para atender à referência em `HomePage` . Adicione uma nova classe ao seu projeto de biblioteca .NET Standard chamada `GameScene` . Ele deve herdar da `CCScene` classe da seguinte maneira:

```csharp
public class GameScene : CCScene
{
    public GameScene (CCGameView gameView) : base(gameView)
    {

    }
}
```

Agora que `GameScene` está definido, podemos retornar ao `HomePage` e adicionar um campo:

```csharp
// Keep the GameScene at class scope
// so the button click events can access it:
GameScene gameScene;
```

Agora, podemos compilar nosso projeto e executá-lo para ver CocosSharp em execução. Não adicionamos nada à nossa `GameScene,` parte superior da nossa página é preta – a cor padrão de uma cena CocosSharp:

![](cocossharp-images/image5.png "Blank GameScene")

<a name="4" />

### <a name="4-adding-a-circle"></a>4. adicionando um círculo

O aplicativo atualmente tem uma instância em execução do mecanismo CocosSharp, exibindo um vazio `CCScene` . Em seguida, adicionaremos um objeto visual: um círculo. A `CCDrawNode` classe pode ser usada para desenhar uma variedade de formas geométricas, conforme descrito na [geometria de desenho com o guia CCDrawNode](https://github.com/xamarin/docs-archive/blob/master/Docs/CocosSharp/ccdrawnode.md).

Adicione um círculo à nossa `GameScene` classe e instancie-o no construtor, conforme mostrado no código a seguir:

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

A execução do aplicativo agora mostra um círculo no lado esquerdo da área de exibição CocosSharp:

![](cocossharp-images/image6.png "Circle in GameScene")

#### <a name="understanding-designresolution"></a>Entendendo o DesignResolution

Agora que um objeto visual CocosSharp é exibido, podemos investigar a `DesignResolution` propriedade.

O `DesignResolution` representa a largura e a altura da área CocosSharp para colocação e dimensionamento de objetos. A resolução real da área é medida em *pixels* , enquanto a `DesignResolution` é medida em *unidades*mundiais. O diagrama a seguir mostra a resolução de várias partes da exibição, conforme exibido em um iPhone 5, com uma resolução de tela de pixels de 640x1136:

![](cocossharp-images/image7.png "iPhone 5s Design Resolution")

O diagrama acima exibe as dimensões de pixel no exterior da tela em texto preto. As unidades são exibidas no interior do diagrama em texto branco. Aqui estão alguns detalhes importantes exibidos acima:

- A origem da exibição CocosSharp está na parte inferior esquerda. Mover para a direita aumenta o valor X e mover para cima aumenta o valor Y. Observe que o valor Y é invertido em comparação com alguns outros mecanismos de layout 2D, onde (0, 0) é o canto superior esquerdo da tela.
- O comportamento padrão de CocosSharp é manter a taxa de proporção de sua exibição. Como a primeira linha na grade é mais larga do que a altura, CocosSharp não preenche toda a largura de sua célula, conforme mostrado pelo retângulo branco pontilhado. Esse comportamento pode ser alterado, conforme descrito no [guia lidando com várias resoluções no CocosSharp](https://github.com/xamarin/docs-archive/blob/master/Docs/CocosSharp/resolutions.md).
- Neste exemplo, o CocosSharp manterá uma área de exibição de 100 unidades de largura e altura, independentemente do tamanho ou da taxa de proporção de seu dispositivo. Isso significa que o código pode assumir que X = 100 representa o limite da exibição do CocosSharp, mantendo o layout consistente em todos os dispositivos.

#### <a name="ccdrawnode-details"></a>Detalhes do CCDrawNode

Nosso aplicativo simples usa a `CCDrawNode` classe para desenhar um círculo. Essa classe pode ser muito útil para aplicativos de negócios, pois fornece renderização de geometria baseada em vetor – um recurso ausente do Xamarin.Forms . Além dos círculos, a `CCDrawNode` classe pode ser usada para desenhar retângulos, linhas e polígonos personalizados. `CCDrawNode`também é fácil de usar, pois não requer o uso de arquivos de imagem (como. png). Uma discussão mais detalhada do CCDrawNode pode ser encontrada na [geometria de desenho com o guia do CCDrawNode](https://github.com/xamarin/docs-archive/blob/master/Docs/CocosSharp/ccdrawnode.md).

<a name="5" />

### <a name="5-interacting-with-cocossharp"></a>5. interagindo com CocosSharp

Elementos visuais CocosSharp (como `CCDrawNode` ) herdam da `CCNode` classe. `CCNode`fornece duas propriedades que podem ser usadas para posicionar um objeto em relação ao seu pai: `PositionX` e `PositionY` . Nosso código atualmente usa essas duas propriedades para posicionar o centro do círculo, conforme mostrado neste trecho de código:

```csharp
circle.PositionX = 20;
circle.PositionY = 50;
```

É importante observar que os objetos CocosSharp são posicionados por valores de posição explícitos, em oposição à maioria dos Xamarin.Forms modos de exibição, posicionados automaticamente de acordo com o comportamento de seus controles de layout pai.

Vamos adicionar o código para permitir que o usuário clique em um dos dois botões para mover o círculo para a esquerda ou para a direita por 10 unidades (não pixels, já que o círculo desenha o espaço de unidade do CocosSharp World). Primeiro, criaremos dois métodos públicos na `GameScene` classe:

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

Em seguida, adicionaremos manipuladores aos dois botões em `HomePage` para responder a cliques. Quando terminar, nosso `CreateBottomHalf` método conterá o seguinte código:

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

O círculo CocosSharp agora se move em resposta a cliques. Também podemos ver claramente os limites da tela CocosSharp movendo o círculo para a esquerda ou para a direita:

![](cocossharp-images/image8.png "GameScene with Moving Circle")

## <a name="summary"></a>Resumo

Este guia mostra como adicionar o CocosSharp a um Xamarin.Forms projeto existente, como criar a interação entre o Xamarin.Forms e o CocosSharp e discute várias considerações ao criar layouts no CocosSharp.

O CocosSharp Game Engine oferece muita funcionalidade e profundidade, portanto, este guia só aborda a superfície do que o CocosSharp pode fazer. Os desenvolvedores interessados em ler mais sobre o CocosSharp podem encontrar muitos artigos no [arquivo morto do CocosSharp](https://github.com/xamarin/docs-archive/blob/master/Docs/CocosSharp/).

## <a name="related-links"></a>Links relacionados

- [CocosSharpForms (exemplo)](https://github.com/xamarin/xamarin-forms-samples/tree/master/CocosSharpForms)
