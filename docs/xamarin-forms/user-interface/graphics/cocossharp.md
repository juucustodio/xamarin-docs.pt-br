---
title: Usar CocosSharp em xamarin. Forms
description: CocosSharp pode ser usado para adicionar de forma precisa, imagem e processamento de texto a um aplicativo para visualização avançada
ms.prod: xamarin
ms.assetid: E0F404D5-5C6B-4288-92EC-78996C674E4E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/03/2016
ms.openlocfilehash: 7b465391958a6e862bfed9fde8d9da1fdd52bee5
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70759762"
---
# <a name="using-cocossharp-in-xamarinforms"></a>Usar CocosSharp em xamarin. Forms

[![Baixar exemplo](~/media/shared/download.png) baixar o exemplo](h https://github.com/xamarin/xamarin-forms-samples/tree/master/CocosSharpForms)

_CocosSharp pode ser usado para adicionar de forma precisa, imagem e processamento de texto a um aplicativo para visualização avançada_

> [!VIDEO https://youtube.com/embed/eYCx63FeqVU]

**Evolve 2016: Ilhas Cocos no Xamarin. Forms**

## <a name="overview"></a>Visão geral

CocosSharp é uma tecnologia flexível e poderosa para exibição de elementos gráficos, leitura de entrada por toque, reprodução de áudio e gerenciamento de conteúdo. Este guia explica como adicionar CocosSharp a um aplicativo xamarin. Forms. Ele aborda o seguinte:

- [O que é CocosSharp?](#what)
- [Adicionando os pacotes do CocosSharp Nuget](#nuget)
- [Passo a passo: Adicionando CocosSharp a um aplicativo Xamarin. Forms](#add)

<a name="what" />

## <a name="what-is-cocossharp"></a>O que é CocosSharp?

[CocosSharp](https://github.com/xamarin/docs-archive/blob/master/Docs/CocosSharp/index.md) é um mecanismo de jogo do código-fonte aberto que está disponível na plataforma Xamarin.
CocosSharp é uma biblioteca de tempo de execução eficiente que inclui os seguintes recursos:

- Renderização de imagem usando `CCSprite` a classe
- Renderização de forma usando `CCDrawNode` a classe
- Lógica de cada quadro usando a `CCNode.Schedule` classe
- Gerenciamento de conteúdo (carregamento e descarregamento de recursos, como arquivos. png) usando o`CCTextureCache`
- Animações usando a `CCAction` classe

O foco principal do CocosSharp é simplificar a criação de jogos 2D de plataforma cruzada; No entanto, ele também pode ser uma ótima adição à aplicativos de formulário do Xamarin. Como jogos geralmente exigem controle preciso sobre visuais e renderização eficiente, CocosSharp pode ser usado para adicionar visualização avançada e efeitos para os aplicativos não é de jogo.

Xamarin. Forms é compilado em sistemas de interface do usuário nativos, específicos da plataforma. Por exemplo, [ `Button`s](xref:Xamarin.Forms.Button) aparecer de maneira diferente no iOS e Android e ainda podem ser diferentes por versão do sistema operacional. Por outro lado, CocosSharp não usa quaisquer objetos visuais específicos da plataforma, portanto, todos os objetos visuais parecem idênticos em todas as plataformas. É claro que, resolução e taxa de proporção diferem entre os dispositivos, e isso pode afetar como CocosSharp processa seus visuais. Esses detalhes serão discutidos posteriormente neste guia.

Informações mais detalhadas podem ser encontradas na [CocosSharp seção](https://github.com/xamarin/docs-archive/blob/master/Docs/CocosSharp/index.md).

<a name="nuget" />

## <a name="adding-the-cocossharp-nuget-packages"></a>Adicionando os pacotes do CocosSharp Nuget

Antes de usar CocosSharp, os desenvolvedores precisam fazer algumas adições ao seu projeto xamarin. Forms.
Este guia pressupõe que um projeto xamarin. Forms com um iOS, Android e o .NET Standard projeto de biblioteca.
Todo o código será gravado no projeto da biblioteca .NET Standard; No entanto, as bibliotecas devem ser adicionadas para projetos Android e iOS.

O pacote do CocosSharp Nuget contém todos os objetos necessários para criar objetos de CocosSharp.
O pacote do nuget CocosSharp.Forms inclui o `CocosSharpView` classe, que é usada para hospedar o CocosSharp em xamarin. Forms.
Adicione a **CocosSharp.Forms** NuGet e **CocosSharp** será adicionado automaticamente também.
Para fazer isso, clique com botão direito no **pacotes** pasta no projeto de biblioteca .NET Standard e selecione **adicionar pacotes...** . Insira o termo de pesquisa **CocosSharp.Forms**, selecione **CocosSharp para xamarin. Forms**, em seguida, clique em **Adicionar pacote**.

![](cocossharp-images/image1.png "Adicionar caixa de diálogo de pacotes")

Ambos **CocosSharp** e **CocosSharp.Forms** pacotes do NuGet serão adicionados ao projeto:

![](cocossharp-images/image2.png "Pasta de pacotes")

Repita as etapas acima para projetos específicos da plataforma (por exemplo, iOS e Android).

<a name="add" />

## <a name="walkthrough-adding-cocossharp-to-a-xamarinforms-app"></a>Passo a passo: Adicionando CocosSharp a um aplicativo Xamarin. Forms

Siga estas etapas para adicionar uma exibição de CocosSharp simples para um aplicativo xamarin. Forms:

1. [Criando um Xamarin Forms a página](#1)
1. [Adicionando um CocosSharpView](#2)
1. [Criando o GameScene](#3)
1. [Adição de um círculo](#4)
1. [Interagindo com CocosSharp](#5)

Depois que você adicionou com êxito um modo de exibição de CocosSharp em um aplicativo xamarin. Forms, visite o [CocosSharp documentação](https://github.com/xamarin/docs-archive/blob/master/Docs/CocosSharp/index.md) para saber mais sobre como criar conteúdo com CocosSharp.

<a name="1" />

### <a name="1-creating-a-xamarin-forms-page"></a>1. Criando um Xamarin Forms a página

CocosSharp pode ser hospedado em qualquer contêiner de xamarin. Forms. Este exemplo para essa página usa uma página chamada `HomePage`. `HomePage` é dividida ao meio por um `Grid` para mostrar como xamarin. Forms e CocosSharp podem ser renderizados simultaneamente na mesma página.

Primeiro, defina a página de modo que ele contém um `Grid` e duas `Button` instâncias:

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

No iOS, o `HomePage` aparece como mostrado na imagem a seguir:

![](cocossharp-images/image3.png "Captura de tela da home page")

<a name="2" />

### <a name="2-adding-a-cocossharpview"></a>2. Adicionando um CocosSharpView

O `CocosSharpView` classe é usada para inserir CocosSharp em um aplicativo do xamarin. Forms. Uma vez que `CocosSharpView` herda a [são](xref:Xamarin.Forms.View) classe, ele fornece uma interface familiar para layout, e ele pode ser usado dentro de contêineres de layout, como [Xamarin.Forms.Grid](xref:Xamarin.Forms.Grid). Adicione um novo `CocosSharpView` ao projeto completando o `CreateTopHalf` método:

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

Inicialização de CocosSharp não é imediata, registre um evento para quando o `CocosSharpView` terminou de sua criação. Fazer isso no `HandleViewCreated` método:

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

O `HandleViewCreated` método tem dois detalhes importantes que iremos procurar em. A primeira é a `GameScene` classe, que será criado na próxima seção. É importante observar que o aplicativo não será compilado até que o `GameScene` é criado e o `gameScene` referência de instância é resolvida.

O segundo detalhe importante é o `DesignResolution` propriedade, que define a área visível do jogo para objetos de CocosSharp. O `DesignResolution` propriedade será analisada depois de criar `GameScene`.

<a name="3" />

### <a name="3-creating-the-gamescene"></a>3. Criando o GameScene

O `GameScene` herda do CocosSharp `CCScene`. `GameScene` é o primeiro ponto em que lidamos puramente com CocosSharp. Código contido em `GameScene` funcionará em qualquer aplicativo CocosSharp, se ele está hospedado em um projeto xamarin. Forms ou não.

O `CCScene` classe é a raiz visual de CocosSharp toda a renderização. Qualquer objeto CocosSharp visível deve estar contido em um `CCScene`. Mais especificamente, os objetos visuais devem ser adicionados ao `CCLayer` instâncias e aqueles `CCLayer` instâncias devem ser adicionadas a um `CCScene`.

O gráfico a seguir pode ajudar a visualizar uma hierarquia de CocosSharp típica:

![](cocossharp-images/image4.png "Hierarquia de CocosSharp típico")

Apenas um `CCScene` podem estar ativas simultaneamente. A maioria dos jogos usar várias `CCLayer` instâncias de classificação de conteúdo, mas nosso aplicativo usa apenas um. Da mesma forma, a maioria dos jogos usar múltiplos objetos visuais, mas temos que apenas um em nosso aplicativo. Um mais detalhado discussão sobre o CocosSharp hierarquia visual pode ser encontrada na [passo a passo do BouncingGame](https://github.com/xamarin/docs-archive/blob/master/Docs/CocosSharp/bouncing-game.md).

Inicialmente o `GameScene` classe serão quase vazio – vamos apenas criá-la para satisfazer a referência no `HomePage`. Adicione uma nova classe ao seu projeto de biblioteca .NET Standard chamado `GameScene`. Ele deve herdar o `CCScene` classe da seguinte maneira:

```csharp
public class GameScene : CCScene
{
    public GameScene (CCGameView gameView) : base(gameView)
    {

    }
}
```

Agora que `GameScene` é definido, podemos voltar a `HomePage` e adicione um campo:

```csharp
// Keep the GameScene at class scope
// so the button click events can access it:
GameScene gameScene;
```

Agora podemos compilar nosso projeto e executá-lo para ver CocosSharp em execução. Ainda não adicionamos nada ao nosso `GameScene,` para que a metade superior da nossa página seja preto – a cor padrão de uma cena de CocosSharp:

![](cocossharp-images/image5.png "GameScene em branco")

<a name="4" />

### <a name="4-adding-a-circle"></a>4. Adição de um círculo

O aplicativo atualmente tem uma instância em execução do mecanismo de CocosSharp, exibindo um vazio `CCScene`. Em seguida, vamos adicionar um objeto visual: um círculo. O `CCDrawNode` classe pode ser usada para desenhar uma variedade de formas geométricas, conforme descrito na [desenho geometria com CCDrawNode guia](https://github.com/xamarin/docs-archive/blob/master/Docs/CocosSharp/ccdrawnode.md).

Adicionar um círculo ao nosso `GameScene` de classe e instanciá-lo no construtor, conforme mostrado no código a seguir:

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

![](cocossharp-images/image6.png "Círculo em GameScene")

#### <a name="understanding-designresolution"></a>Noções básicas sobre DesignResolution

Agora que um objeto CocosSharp visual é exibido, poderemos investigar o `DesignResolution` propriedade.

O `DesignResolution` representa a largura e altura da área de CocosSharp para posicionamento e dimensionamento de objetos. A resolução real da área é medida em *pixels* enquanto a `DesignResolution` é medido no mundo *unidades*. O diagrama a seguir mostra a resolução de várias partes da exibição conforme exibido em um iPhone 5 com uma tela com resolução de 640 x 1136 pixels:

![](cocossharp-images/image7.png "iPhone 5s resolução de Design")

O diagrama acima exibe as dimensões de pixel no lado de fora da tela em texto preto. Unidades são exibidas no interior do diagrama no texto em branco. Aqui estão alguns detalhes importantes, exibidas acima:

- A origem da exibição CocosSharp está na parte inferior esquerda. Movendo para a direita aumenta o valor de X e mover para cima aumenta o valor de Y. Observe que o valor de Y é invertido em comparação com alguns outros mecanismos de layout 2D, onde (0,0) é o canto superior esquerdo da tela.
- O comportamento padrão do CocosSharp é manter a taxa de proporção do modo de exibição. Como a primeira linha da grade é maior que a altura, CocosSharp não preenche toda a largura de suas células, conforme mostrado pelo retângulo branco pontilhado. Esse comportamento pode ser alterado, conforme descrito na [guia de manipular várias resoluções no CocosSharp](https://github.com/xamarin/docs-archive/blob/master/Docs/CocosSharp/resolutions.md).
- Neste exemplo, CocosSharp manterá uma área de exibição de 100 unidades de largura e altura independentemente do tamanho ou taxa de proporção de seu dispositivo. Isso significa que o código pode assumir que X = 100 representa a extrema direita associada a CocosSharp exibir Manter layout consistente em todos os dispositivos.

#### <a name="ccdrawnode-details"></a>Detalhes de CCDrawNode

Nosso simples aplicativo usa o `CCDrawNode` classe para desenhar um círculo. Essa classe pode ser muito útil para aplicativos de negócios, pois ela fornece renderização baseada em vetor geometria – um recurso ausente do xamarin. Forms. Além dos círculos, a `CCDrawNode` classe pode ser usada para desenhar retângulos, splines, linhas e polígonos personalizados. `CCDrawNode` também é fácil de usar, pois ele não requer o uso de arquivos de imagem (por exemplo,. png). Uma discussão mais detalhada de CCDrawNode pode ser encontrada na [desenho geometria com CCDrawNode guia](https://github.com/xamarin/docs-archive/blob/master/Docs/CocosSharp/ccdrawnode.md).

<a name="5" />

### <a name="5-interacting-with-cocossharp"></a>5. Interagindo com CocosSharp

Elementos visuais do CocosSharp (como `CCDrawNode`) herdam o `CCNode` classe. `CCNode` fornece duas propriedades que podem ser usadas para posicionar um objeto em relação ao seu pai: `PositionX` e `PositionY`. Nosso código atualmente usa essas duas propriedades para posicionar o centro do círculo, conforme mostrado neste trecho de código:

```csharp
circle.PositionX = 20;
circle.PositionY = 50;
```

É importante observar que os objetos de CocosSharp são posicionados por valores de posição explícitos, em vez da maioria das exibições de xamarin. Forms, que são posicionadas automaticamente acordo com o comportamento de seus controles de layout pai.

Vamos adicionar código para permitir que o usuário clique em um dos dois botões para mover o círculo para a esquerda ou à direita por 10 unidades (não em pixels, uma vez que o círculo desenha no espaço de unidade do mundo CocosSharp). Primeiro, vamos criar dois métodos públicos no `GameScene` classe:

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

Em seguida, vamos adicionar manipuladores para os dois botões na `HomePage` para responder a cliques. Quando concluído, nosso `CreateBottomHalf` método contém o código a seguir:

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

![](cocossharp-images/image8.png "GameScene com movimentação de círculo")

## <a name="summary"></a>Resumo

Este guia mostra como adicionar CocosSharp a um xamarin. Forms existente do projeto, como criar a interação entre o xamarin. Forms e CocosSharp e discute várias considerações ao criar layouts no CocosSharp.

O mecanismo de jogo CocosSharp oferece muita funcionalidade e a profundidade, portanto, este guia aborda apenas do que CocosSharp pode fazer. Os desenvolvedores interessados em ler mais sobre o CocosSharp podem encontrar muitos artigos no [arquivo morto do CocosSharp](https://github.com/xamarin/docs-archive/blob/master/Docs/CocosSharp/).

## <a name="related-links"></a>Links relacionados

- [CocosSharpForms (amostra)](https://github.com/xamarin/xamarin-forms-samples/tree/master/CocosSharpForms)
