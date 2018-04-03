---
title: Usando CocosSharp em xamarin. Forms
description: CocosSharp pode ser usado para adicionar forma precisa, imagem e processamento de texto a um aplicativo para visualização avançada
ms.topic: article
ms.prod: xamarin
ms.assetid: E0F404D5-5C6B-4288-92EC-78996C674E4E
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 05/03/2016
ms.openlocfilehash: 83852b6b2d7324ae6aaf6b1dbf86a6ef7f9ac509
ms.sourcegitcommit: 4f1b508caa8e7b6ccf85d167ea700a5d28b0347e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2018
---
# <a name="using-cocossharp-in-xamarinforms"></a>Usando CocosSharp em xamarin. Forms

_CocosSharp pode ser usado para adicionar forma precisa, imagem e processamento de texto a um aplicativo para visualização avançada_

> [!VIDEO https://youtube.com/embed/eYCx63FeqVU]

**Evoluir 2016: Cocos # no xamarin. Forms**

## <a name="overview"></a>Visão geral

CocosSharp é uma tecnologia flexível e poderosa para exibição de elementos gráficos, leitura de entrada por toque, reproduzir conteúdo de áudio e gerenciamento. Este guia explica como adicionar CocosSharp a um aplicativo xamarin. Forms. Ele aborda o seguinte:

* [O que é CocosSharp?](#what)
* [Adicionando os pacotes do CocosSharp Nuget](#nuget)
* [Passo a passo: Adicionando CocosSharp a um aplicativo xamarin. Forms](#add)

<a name="what" />

## <a name="what-is-cocossharp"></a>O que é CocosSharp?

[CocosSharp](~/graphics-games/cocossharp/index.md) é um mecanismo de jogo do código-fonte aberto que está disponível na plataforma do Xamarin.
CocosSharp é uma biblioteca de tempo de execução eficiente que inclui os seguintes recursos:

* Renderização de imagem usando o [CCSprite classe](https://developer.xamarin.com/api/type/CocosSharp.CCSprite/)
* Uso de renderização de forma a [CCDrawNode classe](https://developer.xamarin.com/api/type/CocosSharp.CCDrawNode/)
* Cada quadro lógica usando o [método CCNode.Schedule](https://developer.xamarin.com/api/member/CocosSharp.CCNode.Schedule/p/System.Action%7BSystem.Single%7D/)
* Gerenciamento de conteúdo (Carregando e descarregando de recursos, como arquivos. png) usando o [CCTextureCache classe](https://developer.xamarin.com/api/type/CocosSharp.CCTextureCache/)
* Animações usando o [CCAction classe](https://developer.xamarin.com/api/type/CocosSharp.CCAction/)

É o foco principal do CocosSharp para simplificar a criação de jogos 2D de plataforma cruzada; No entanto, ele também pode ser uma ótima adição a aplicativos Xamarin formulário. Como jogos normalmente exigem processamento eficiente e controle preciso sobre visuais, CocosSharp pode ser usado para adicionar visualização avançada e efeitos de aplicativos não é de jogo.

Xamarin. Forms se baseia em sistemas de interface do usuário nativos, específica de plataforma. Por exemplo, [ `Button`s](https://developer.xamarin.com/api/type/Xamarin.Forms.Button/) aparecer de maneira diferente no iOS e Android e ainda podem ser diferentes por versão do sistema operacional. Por outro lado, CocosSharp não usar qualquer objeto visual específica de plataforma, para que todos os objetos visuais são idênticos em todas as plataformas. Claro, resolução e taxa de proporção diferem entre dispositivos, e isso pode afetar como CocosSharp processa seus visuais. Esses detalhes serão discutidos posteriormente neste guia.

Informações mais detalhadas podem ser encontradas no [CocosSharp seção](~/graphics-games/cocossharp/index.md).

<a name="nuget" />

## <a name="adding-the-cocossharp-nuget-packages"></a>Adicionando os pacotes do CocosSharp Nuget

Antes de usar CocosSharp, os desenvolvedores precisam fazer algumas adições ao seu projeto xamarin. Forms.
Este guia pressupõe um projeto xamarin. Forms com um iOS, Android e PCL projeto.
Todo o código será gravado no projeto PCL; No entanto, bibliotecas devem ser adicionadas a projetos Android e iOS.

O pacote do CocosSharp Nuget contém todos os objetos necessários para criar objetos CocosSharp.
O pacote do nuget CocosSharp.Forms inclui o `CocosSharpView` classe, que é usada para hospedar CocosSharp no xamarin. Forms.
Adicionar o **CocosSharp.Forms** NuGet e **CocosSharp** serão adicionados automaticamente também.
Para fazer isso, clique com botão direito no PCL <span class="UIItem">pacotes</span> pasta e selecione <span class="UIItem">adicionar pacotes de... </span>. Digite o termo de pesquisa <span class="UIItem">CocosSharp.Forms</span>, selecione <span class="UIItem">CocosSharp para xamarin. Forms</span>, em seguida, clique em <span class="UIItem">Adicionar pacote</span>.

![](cocossharp-images/image1.png "Adicionar caixa de diálogo de pacotes")

Ambos **CocosSharp** e **CocosSharp.Forms** pacotes do NuGet serão adicionados ao projeto:

![](cocossharp-images/image2.png "Pasta de pacotes")

Repita as etapas acima para projetos específicos de plataforma (por exemplo, iOS e Android).

<a name="add" />

## <a name="walkthrough-adding-cocossharp-to-a-xamarinforms-app"></a>Passo a passo: Adicionando CocosSharp a um aplicativo xamarin. Forms

Siga estas etapas para adicionar um modo de exibição CocosSharp simple para um aplicativo xamarin. Forms:

1. [Criando um Xamarin Forms página](#1)
1. [Adicionando um CocosSharpView](#2)
1. [Criando o GameScene](#3)
1. [Adicionando um círculo](#4)
1. [Interagir com CocosSharp](#5)

Depois de adicionar um modo de exibição CocosSharp com êxito para um aplicativo xamarin. Forms, visite o [CocosSharp documentação](~/graphics-games/cocossharp/index.md) para saber mais sobre a criação de conteúdo com CocosSharp.

<a name="1" />

### <a name="1-creating-a-xamarin-forms-page"></a>1. Criando um Xamarin Forms página

CocosSharp pode ser hospedado em qualquer contêiner xamarin. Forms. Este exemplo para essa página usa uma página chamada `HomePage`. `HomePage` é dividido em metade por um `Grid` para mostrar como xamarin. Forms e CocosSharp podem ser renderizado simultaneamente na mesma página.

Primeiro, defina a página de forma que ele contém um `Grid` e dois `Button` instâncias:


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

O `CocosSharpView` classe é usada para inserir CocosSharp em um aplicativo xamarin. Forms. Como `CocosSharpView` herda o [Xamarin.Forms.View](https://developer.xamarin.com/api/type/Xamarin.Forms.View/) classe, ele fornece uma interface familiar para layout, e ele pode ser usado dentro de contêineres de layout, como [Xamarin.Forms.Grid](https://developer.xamarin.com/api/type/Xamarin.Forms.Grid/). Adicionar um novo `CocosSharpView` ao projeto executando o `CreateTopHalf` método:


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

Inicialização CocosSharp não for imediata, registre um evento quando o `CocosSharpView` concluiu a sua criação. Fazer isso no `HandleViewCreated` método:


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

O `HandleViewCreated` método tem dois detalhes importantes que vai ser temos. A primeira é a `GameScene` classe, que será criado na próxima seção. É importante observar que o aplicativo não será compilado até que o `GameScene` é criado e o `gameScene` referência de instância seja resolvida.

O segundo detalhe importante é o `DesignResolution` propriedade, que define a área visível do jogo para objetos CocosSharp. O `DesignResolution` propriedade será analisada depois de criar `GameScene`.

<a name="3" />

### <a name="3-creating-the-gamescene"></a>3. Criando o GameScene

O `GameScene` classe herda do CocosSharp `CCScene`. `GameScene` é o primeiro ponto em que lidamos puramente CocosSharp. Código contido em `GameScene` funcionará em qualquer aplicativo CocosSharp, se ele está hospedado em um projeto do xamarin. Forms ou não.

O `CCScene` classe é o visual raiz de todas as renderizações CocosSharp. Qualquer objeto CocosSharp visível deve estar contido em um `CCScene`. Mais especificamente, os objetos visuais devem ser adicionados ao `CCLayer` instâncias e aqueles `CCLayer` instâncias devem ser adicionadas a um `CCScene`.

O gráfico a seguir pode ajudar a visualizar uma hierarquia CocosSharp típica:

![](cocossharp-images/image4.png "Hierarquia de CocosSharp típico")

Apenas um `CCScene` podem estar ativas simultaneamente. A maioria dos jogos usar várias `CCLayer` instâncias de classificação de conteúdo, mas o aplicativo usa apenas um. Da mesma forma, a maioria dos jogos usar vários objetos visual, mas apenas teremos um em nosso aplicativo. Uma mais detalhada a discussão sobre o CocosSharp hierarquia visual pode ser encontrada no [BouncingGame passo a passo](~/graphics-games/cocossharp/bouncing-game.md).

Inicialmente o `GameScene` classe está quase vazia – vamos apenas criar para satisfazer a referência em `HomePage`. Adicionar uma nova classe ao seu PCL denominado `GameScene`. Ele deve herdar do `CCScene` classe da seguinte maneira:


```csharp
public class GameScene : CCScene
{
    public GameScene (CCGameView gameView) : base(gameView)
    {

    }
}
```

Agora que `GameScene` é definido, podemos voltar a `HomePage` e adicionar um campo de:


```csharp
// Keep the GameScene at class scope
// so the button click events can access it:
GameScene gameScene;
```

Agora podemos compilar nosso projeto e executá-lo para ver CocosSharp em execução. Ainda não adicionamos nada para nosso `GameScene,` a metade superior da página é preto – a cor padrão de uma cena CocosSharp:

![](cocossharp-images/image5.png "GameScene em branco")

<a name="4" />

### <a name="4-adding-a-circle"></a>4. Adicionando um círculo

No momento, o aplicativo tem uma instância em execução do mecanismo de CocosSharp, exibindo vazio `CCScene`. Em seguida, vamos adicionar um objeto visual: um círculo. O `CCDrawNode` classe pode ser usada para desenhar uma variedade de formas geométricas, conforme descrito no [geometria desenho com guia CCDrawNode](~/graphics-games/cocossharp/ccdrawnode.md).

Adicionar um círculo para nosso `GameScene` classe e instanciá-la no construtor, conforme mostrado no código a seguir:


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

Agora que um objeto CocosSharp visual é exibido, é possível investigar o `DesignResolution` propriedade.

O `DesignResolution` representa a largura e altura da área de CocosSharp para posicionamento e dimensionamento de objetos. A resolução real da área é medida em *pixels* enquanto o `DesignResolution` é medido no mundo *unidades*. O diagrama a seguir mostra a resolução de várias partes da exibição, conforme exibido em um iPhone 5 com uma resolução de tela de 1136 x 640 pixels:

![](cocossharp-images/image7.png "iPhone 5s resolução de Design")

O diagrama acima exibe as dimensões de pixel fora da tela em texto preto. Unidades são exibidas no interior do diagrama no texto em branco. Aqui estão alguns detalhes importantes exibidos acima:

* A origem da exibição CocosSharp fica na parte inferior esquerda. Mover para a direita aumenta o valor de X e mover para cima aumenta o valor de Y. Observe que o valor de Y é invertido em comparação com alguns outros mecanismos de layout 2D, onde (0,0) é a parte superior esquerda da tela.
* O comportamento padrão do CocosSharp é manter a taxa de proporção da sua exibição. Como a primeira linha da grade é maior que a altura, CocosSharp não preencher toda a largura da sua célula, conforme mostrado pelo retângulo branco pontilhado. Esse comportamento pode ser alterado, conforme descrito no [guia tratamento várias resoluções no CocosSharp](~/graphics-games/cocossharp/resolutions.md).
* Neste exemplo, CocosSharp manterá uma área de exibição de 100 unidades de largura e altura independentemente do tamanho ou a taxa de proporção de seu dispositivo. Isso significa que o código pode assumir que representa o X = 100 associada à direita do CocosSharp exibir Manter layout consistente em todos os dispositivos.


#### <a name="ccdrawnode-details"></a>Detalhes de CCDrawNode

Nosso aplicativo simples usa o `CCDrawNode` classe para desenhar um círculo. Essa classe pode ser muito útil para aplicativos de negócios como ele fornece renderização baseada em vetor geometria – um recurso ausente do xamarin. Forms. Além de círculos, a `CCDrawNode` classe pode ser usada para desenhar retângulos, splines, linhas e polígonos personalizados. `CCDrawNode` também é fácil de usar porque ele não requer o uso de arquivos de imagem (por exemplo,. png). Uma discussão mais detalhada de CCDrawNode pode ser encontrada na [geometria desenho com guia CCDrawNode](~/graphics-games/cocossharp/ccdrawnode.md).

<a name="5" />

### <a name="5-interacting-with-cocossharp"></a>5. Interagir com CocosSharp

Elementos visuais CocosSharp (como `CCDrawNode`) herdam o `CCNode` classe. `CCNode` fornece duas propriedades que podem ser usadas para posicionar um objeto em relação ao seu pai: `PositionX` e `PositionY`. Nosso código atualmente usa essas duas propriedades para posicionar o centro do círculo, conforme mostrado no trecho de código a este:


```csharp
circle.PositionX = 20;
circle.PositionY = 50;
```

É importante observar que os objetos CocosSharp são posicionados por valores de posição explícitos, em vez da maioria das exibições de xamarin. Forms, que são posicionados automaticamente acordo com o comportamento de seus controles de layout do pai.

Vamos adicionar código para permitir que o usuário clique em um dos dois botões Mover o círculo à esquerda ou à direita, 10 unidades (não em pixels, desde que o círculo desenha no espaço de unidade do mundo CocosSharp). Primeiro, vamos criar dois métodos públicos no `GameScene` classe:


```csharp
public void MoveCircleLeft()
{
    circle.PositionX -= 10;
}

public void MoveCircleRight()
{
    circle.PositionX += 10;
}
```

Em seguida, vamos adicionar manipuladores para os dois botões na `HomePage` para responder a cliques. Quando terminar, nosso `CreateBottomHalf` método contém o código a seguir:


```csharp
void CreateBottomHalf(Grid grid)
{
    // We'll use a StackLayout to organize our buttons
    var stackLayout = new StackLayout();

    // The first button will move the circle to the left when it is clicked:
    var moveLeftButton = new Button {
        Text = "Move Circle Left"
    };
    moveLeftButton.Clicked += (sender, e) => gameScene.MoveCircleLeft ();
    stackLayout.Children.Add (moveLeftButton);

    // The second button will move the circle to the right when clicked:
    var moveCircleRight = new Button {
        Text = "Move Circle Right"
    };
    moveCircleRight.Clicked += (sender, e) => gameScene.MoveCircleRight ();
    stackLayout.Children.Add (moveCircleRight);

    // The stack layout will be in the bottom half (row 1):
    grid.Children.Add (stackLayout, 0, 1);
}
```

O círculo CocosSharp agora se move em resposta aos cliques. Vemos também claramente os limites da tela de CocosSharp movendo o círculo distante o suficiente para a esquerda ou direita:

![](cocossharp-images/image8.png "GameScene mover círculo")

## <a name="summary"></a>Resumo

Este guia mostra como adicionar CocosSharp a um xamarin. Forms existentes do projeto, como criar a interação entre xamarin. Forms e CocosSharp e discute várias considerações ao criar layouts em CocosSharp.

O mecanismo de jogo CocosSharp oferece muito da funcionalidade e a profundidade, para que este guia aborda apenas do que CocosSharp pode fazer. Desenvolvedores interessados em ler mais sobre CocosSharp encontrará muitos artigos o [CocosSharp seção](~/graphics-games/cocossharp/index.md).



## <a name="related-links"></a>Links relacionados

- [APIs de CocosSharp](https://developer.xamarin.com/api/root/CocosSharp/)
- [CocosSharpForms (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/CocosSharpForms/)
