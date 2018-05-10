---
title: Desempenho e efeitos visuais com CCRenderTexture
description: CCRenderTexture permite que os desenvolvedores melhorar o desempenho de seus jogos CocosSharp reduzindo chamadas de desenho e pode ser usado para criar efeitos visuais. Este guia acompanha o exemplo CCRenderTexture para fornecer um exemplo prático de como usar essa classe efetivamente.
ms.prod: xamarin
ms.assetid: F02147C2-754B-4FB4-8BE0-8261F1C5F574
author: charlespetzold
ms.author: chape
ms.openlocfilehash: 82d39542b24c6b1669798a0b4e1fb14a81f6b44e
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/09/2018
---
# <a name="performance-and-visual-effects-with-ccrendertexture"></a>Desempenho e efeitos visuais com CCRenderTexture

_CCRenderTexture permite que os desenvolvedores melhorar o desempenho de seus jogos CocosSharp reduzindo chamadas de desenho e pode ser usado para criar efeitos visuais. Este guia acompanha o exemplo CCRenderTexture para fornecer um exemplo prático de como usar essa classe efetivamente._

O `CCRenderTexture` classe fornece funcionalidade para a renderização de vários objetos CocosSharp com uma textura única. Depois de criado, `CCRenderTexture` instâncias podem ser usadas para renderizar elementos gráficos com eficiência e implementar efeitos visuais. `CCRenderTexture` permite que vários objetos a serem renderizados com uma textura única uma vez. Em seguida, esse textura pode ser reutilizado cada quadro, reduzindo o número total de chamadas de desenho.

Este guia examina como usar o `CCRenderTexture` objeto para melhorar o desempenho de cartões de renderização em um jogo colecionável (CCG). Ele também demonstra como `CCRenderTexture` pode ser usado para tornar uma entidade inteira transparente. Este guia faz referência a `CCRenderTexture` [projeto de exemplo](https://developer.xamarin.com/samples/mobile/CCRenderTexture/).

![](ccrendertexture-images/image1.png "Este guia faz referência para o projeto de exemplo CCRenderTexture")


## <a name="card--a-typical-entity"></a>Placa – uma entidade típicas

Antes de examinar como usar `CCRenderTexture` de objeto, podemos primeiro familiarizar nós com o `Card` entidade que será usado em todo este projeto para explorar o `CCRenderTexture` classe. O `Card` classe é uma entidade típica, seguindo o padrão de entidade descrito a [guia entidade](~/graphics-games/cocossharp/entities.md). A classe de cartão tem todos os seus componentes visual (instâncias de `CCSprite` e `CCLabel`) listado como campos:


```csharp
class Card : CCNode
{
    bool usesRenderTexture;
    List<CCNode> visualComponents = new List<CCNode>();
    CCSprite background;
    CCSprite colorIcon;
    CCSprite monsterSprite;
    CCLabel monsterNameDisplay;
    CCLabel hpDisplay;
    CCLabel descriptionDisplay;
```

Instâncias de cartão podem ser renderizadas usando um `CCRenderTexture`, ou por cada componente do visual de desenho individualmente. Embora cada componente é um objeto independente, o `CCNode` domínio pai usado em entidades do sistema faz o `Card` se comportam como um único objeto – pelo menos na maior parte. Por exemplo, se um `Card` entidade é reposicionada, redimensionada ou girada, em seguida, todos os objetos de visual contidos são afetados para tornar o cartão parece ser um único objeto. Para ver as placas de se comportar como um único objeto, é possível modificar o `GameLayer.AddedToScene` método para definir o `useRenderTextures` variável para `false`:

    
```csharp
protected override void AddedToScene ()
{
    base.AddedToScene();
    GameView.Stats.Enabled = true;
    const bool useRenderTextures = false;
    ...
}
```

O `GameLayer` código não move cada elemento visual de forma independente, ainda cada elemento visual dentro de `Card` entidade é posicionada corretamente:

![](ccrendertexture-images/image1.png "O código de GameLayer não move cada elemento visual independentemente, mas cada elemento visual dentro da entidade do cartão é posicionado corretamente")

O exemplo é codificado para expor os dois problemas que podem ocorrer quando cada componente visual for renderizado:

- O desempenho pode sofrer devido a várias chamadas de desenho
- Determinados efeitos visuais, como transparência, não podem ser implementados com precisão, como exploraremos mais tarde


### <a name="card-draw-calls"></a>Chamadas de desenho de cartão

Nosso código é uma simplificação do que podem ser encontrado em um completo *jogo colecionável* (CCG) como "Magic: A coleta" ou "Hearthstone". Nosso jogo só exibe três cartões de uma vez e tem um pequeno número de unidades possíveis (azul, verde e laranja). Por outro lado, um jogo completo pode ter mais de vinte cartões na tela em um determinado momento, e players podem ter centenas de cartões para escolher ao criar suas coleções. Embora nosso jogo atualmente não sofrem problemas de desempenho, pode ser um jogo completo com implementação semelhante.

CocosSharp fornece informações sobre desempenho de renderização, expondo as chamadas de desenho executada por quadro. Nosso `GameLayer.AddedToScene` método define o `GameView.Stats.Enabled` para `true`, resultando em informações de desempenho mostradas na parte inferior da tela:

![](ccrendertexture-images/image2.png "O método GameLayer.AddedToScene define o GameView.Stats.Enabled como true, resultando em informações de desempenho mostradas na parte inferior esquerda da tela")

Observe que, apesar de ter três cartões na tela, temos dezenove chamadas de desenho (resultados cada cartão em seis desenhar chamadas, o texto exibindo as contas de informações de desempenho para um mais). Chamadas de desenho tem um impacto significativo no desempenho de um jogo, portanto CocosSharp fornece várias maneiras de reduzi-los. Uma técnica descrita a [CCSpriteSheet guia](~/graphics-games/cocossharp/ccspritesheet.md). Outra técnica é usar o `CCRenderTexture` para reduzir cada entidade para uma chamada, como examinaremos neste guia.


### <a name="card-transparency"></a>Transparência de cartão

Nosso `Card` entidade inclui um `Opacity` propriedade a transparência de controle, conforme mostrado no trecho de código a seguir:


```csharp
public override byte Opacity
{
    get
    {
        return base.Opacity;
    }
    set
    {
        base.Opacity = value;
        if (usesRenderTexture)
        {
            this.renderTexture.Sprite.Opacity = value;
        }
        else
        {
            foreach (var component in visualComponents)
            {
                component.Opacity = value;
            }
        }
    }
}
```

Observe que o setter oferece suporte ao uso renderizar texturas ou renderização de cada componente individualmente. Para ver seu efeito, altere o `opacity` valor `127` (cerca de metade opacidade) em `GameLayer.AddedToScene` que resultará em cada componente tem um `Opacity` valor de `127`:


```csharp
protected override void AddedToScene ()
{
    base.AddedToScene();
    GameView.Stats.Enabled = true;
    const bool useRenderTextures = false;
    const byte opacity = 127;
    ...
}
```

O jogo agora processará os cartões com alguns transparência, fazendo com que eles apareçam mais escuras, desde que o plano de fundo é preto:

![](ccrendertexture-images/image3.png "O jogo agora processará os cartões com alguns transparência, fazendo com que eles apareçam mais escuras, desde que o plano de fundo é preto")

À primeira vista pode ser algo como se nossos cartões foram corretamente feitos transparentes. No entanto, a captura de tela exibe um número de problemas de visual.

Como nosso plano de fundo preto, é esperado que cada parte de nosso cartão se tornarão mais escuro devido a transparência. Isto é, fica mais transparente de um cartão, quanto mais escuro torna-se. A opacidade 0, um `Card` será completamente transparente (totalmente preto). No entanto, algumas partes do nosso cartão não ficam mais escuros quando opacidade foi alterada para `127`. Pior, algumas partes do nosso cartão realmente tornou-se mais claros quando eles se tornou mais transparentes. Vamos dar uma olhada em partes de nossa cartão que estavam em pretos *antes de* fossem transparentes – especificamente o texto de detalhes e o gráfico monster contornos pretos. Podemos colocá-los lado a lado, podemos ver o impacto da aplicação de transparência:

![](ccrendertexture-images/image4.png "Se colocada lado a lado, o impacto da aplicação de transparência pode ser visto")

Conforme mencionado acima, todas as partes do cartão devem se tornar mais escuras quando se torne mais transparente, mas em um número de áreas não for o caso:

- Estrutura de tópicos do robô se torna mais clara (vai de preto em cinza)
- O texto de descrição se torna mais claro (vai de preto em cinza)
- A parte verde do robô se torna menos saturada, mas não se torne mais escura

Para ajudar a visualizar o motivo pelo qual isso ocorre, é preciso ter em mente que cada componente visual é desenhado independentemente, cada parcialmente transparente. O primeiro componente visual desenhado é o plano de fundo do cartão. Elementos de transparentes subsequentes serão desenhados sobre o cartão e serão afetados pelo plano de fundo do cartão. Se remover algum texto de nosso cartão e mover o gráfico de robô para baixo, podemos ver como o plano de fundo do cartão afeta o robô. Observe a linha laranja na caixa superior pode ser vista no robô, e que a área do robô que se sobrepõe a listra azul no centro do cartão é desenhada mais escuro:

![](ccrendertexture-images/image5.png "Observe a linha laranja na caixa superior pode ser vista no robô, e que a área do robô que se sobrepõe a listra azul no centro do cartão é desenhada mais escuro")

Usando um `CCRenderTexture` permite-nos tornar o cartão inteiro transparente sem afetar a renderização de componentes individuais do cartão como veremos mais adiante neste guia.


## <a name="using-ccrendertexture"></a>Usando CCRenderTexture

Agora que nós identificamos os problemas com a renderização de cada componente individualmente, voltaremos na renderização em um `CCRenderTexture` e comparar o comportamento.

Para habilitar a renderização de um `CCRenderTexture`, altere o `userRenderTextures` variável para `true` em `GameLayer.AddedToScene`:


```csharp
protected override void AddedToScene ()
{
    base.AddedToScene();
    GameView.Stats.Enabled = true;
    const bool useRenderTextures = true;
```


### <a name="card-draw-calls"></a>Chamadas de desenho de cartão

Se executarmos o jogo agora, vamos ver as chamadas de desenho reduzidas de dezenove para quatro (cada cartão reduzido de seis a uma):

![](ccrendertexture-images/image6.png "Se o jogo for executado agora, as chamadas de desenho reduzido de dezenove para quatro cada cartão reduzido de seis para um")

Como mencionado anteriormente, esse tipo de redução pode ter um impacto significativo em jogos com mais entidades visual na tela.


### <a name="card-transparency"></a>Transparência de cartão

Uma vez o `useRenderTextures` é definido como `true`, cartões transparentes serão renderizado de forma diferente:

![](ccrendertexture-images/image7.png "Depois que o useRenderTextures é definido como true, transparentes cartões serão renderizados diferente")

Vamos comparar o cartão robô transparente usando texturas de renderização (à esquerda) versus sem (direita):

![](ccrendertexture-images/image8.png "Comparar o cartão robô transparente usando renderização texturas (à esquerda) vs sem (direita)")

As diferenças mais óbvias são no texto detalhes (preto em vez de cinza claro) e a entidade de gráfica robô (removida e escura, em vez de luz).


## <a name="ccrendertexture-details"></a>Detalhes de CCRenderTexture

Agora que já vimos os benefícios de usar `CCRenderTexture`, vamos dar uma olhada em como ele é usado no `Card` entidade.

O `CCRenderTexture` é uma tela que pode ser o destino de renderização. Ele tem duas diferenças principais quando comparado a tela do jogo:

1. O `CCRenderTexture` persistir entre os quadros. Isso significa que um `CCRenderTexture` precisa apenas ser renderizado quando ocorrem alterações. Em nosso caso, o `Card` entidade nunca muda, para que ela é processada apenas uma vez. Se houver `Card` componentes alterados, o cartão precisariam ser redesenhado para seu `CCRenderTexture`. Por exemplo, se o valor de HP (pontos de integridade) é alterado quando atacado, o cartão seria necessárias para processar para refletir o novo valor de HP.
1. O `CCRenderTexture` dimensões de pixel não estão vinculados à tela. Um `CCRenderTexture` pode ser maior ou menor que a resolução do dispositivo. O `Card` código cria um `CCRenderTexture` usando o tamanho da sua entidade de gráfica do plano de fundo. O cartão contém uma referência a um `CCRenderTexture` chamado `renderTexture`:


```csharp
CCRenderTexture renderTexture;
```

O `renderTexture` instância permanece `null` até que o `UseRenderTexture` é atribuído a propriedade como true, que chama `SwitchToRenderTexture`:


```csharp
private void SwitchToRenderTexture()
{
    // The card needs to be moved to the origin (0,0) so it's rendered on the render target. 
    // After it's rendered to the CCRenderTexture, it will be moved back to its old position
    var oldPosition = this.Position;
    // Make sure visuals are part of the card so they get rendered
    bool areVisualComponentsAlreadyAdded = this.Children != null && this.Children.Contains(visualComponents[0]);
    if (!areVisualComponentsAlreadyAdded)
    {
        // Temporarily add them so we can render the object:
        foreach (var component in visualComponents)
        {
            this.AddChild(component);
        }
    }
    // Create the render texture if it hasn't yet been made:
    if (renderTexture == null)
    {
        // Even though the game is zoomed in to create a pixellated look, we are using
        // high-resolution textures. Therefore, we want to have our canvas be 2x as big as 
        // the background so fonts don't appear pixellated
        var pixelResolution = background.ContentSize * 2;
        var unitResolution = background.ContentSize;
        renderTexture = new CCRenderTexture(unitResolution, pixelResolution);
        //renderTexture.Sprite.Scale = .5f;
    }
    // We don't want the render target to be a child of this when we update it:
    if (this.Children != null && this.Children.Contains(renderTexture.Sprite))
    {
        this.Children.Remove(renderTexture.Sprite);
    }
    // Move this instance back to the origin so it is rendered inside the render texture:
    this.Position = CCPoint.Zero;
    // Clears the CCRenderTexture
    renderTexture.BeginWithClear(CCColor4B.Transparent);
    // Visit renders this object and all of its children
    this.Visit();
    // Ends the rendering, which means the CCRenderTexture's Sprite can be used
    renderTexture.End();
    // We no longer want the individual components to be drawn, so remove them:
    foreach (var component in visualComponents)
    {
        this.RemoveChild(component);
    }
    // Move this back to its original position:
    this.Position = oldPosition;
    // add the render texture sprite to this:
    renderTexture.Sprite.AnchorPoint = CCPoint.Zero;
    this.AddChild(renderTexture.Sprite);
}
```

O `SwitchToRenderTexture` método pode ser chamado sempre que a textura precisa ser atualizado. Ele pode ser chamado se o cartão já está usando seu `CCRenderTexture` ou está alternando para o `CCRenderTexture` pela primeira vez.

As seções a seguir exploram o `SwitchToRenderTexture` método. 


### <a name="ccrendertexture-size"></a>Tamanho de CCRenderTexture

O construtor CCRenderTexture requer dois conjuntos de dimensões. O primeiro controla o tamanho do `CCRenderTexture` quando ela é desenhada, e o segundo Especifica a largura e altura de seu conteúdo. O `Card` cria uma instância de entidade seu `CCRenderTexture` usando o plano de fundo [ContentSize](https://developer.xamarin.com/api/property/CocosSharp.CCSprite.ContentSize/). Nosso jogo tem um `DesignResolution` de 512 por 384, conforme mostrado no `ViewController.LoadGame` no iOS e `MainActivity.LoadGame` no Android:


```csharp
int width = 512;
int height = 384;
// Set world dimensions
gameView.DesignResolution = new CCSizeI(width, height);
```

O `CCRenderTexture` construtor for chamado com o `background.ContentSize` como o primeiro parâmetro, indicando que o `CCRenderTexture` deve ser tão grande quanto o plano de fundo `CCSprite`. Desde o plano de fundo do cartão `CCSprite` é 200 pixels de altura, o cartão ocupará aproximadamente metade da altura vertical da tela.

O segundo parâmetro passado para o `CCRenderTexture` construtor Especifica a resolução de pixel do `CCRenderTexture`. Como discutido o [guia resolução de CocosSharp](~/graphics-games/cocossharp/resolutions.md), a largura e altura da área visível em unidades de jogos geralmente não é o mesmo que a resolução de pixels da tela. Da mesma forma, um CCRenderTexture pode usar uma resolução maior do que seu tamanho para que visuais apareçam nítidos em dispositivos de alta resolução.

A resolução de pixel é duas vezes o tamanho de CCRenderTexture para impedir que o texto de como pixels aparência:


```csharp
var unitResolution = background.ContentSize;
var pixelResolution = background.ContentSize * 2;
renderTexture = new CCRenderTexture(unitResolution, pixelResolution);
```

Para comparar, podemos alterar o valor de pixelResolution para corresponder a `background.ContentSize` (sem sendo duplicado) e comparar o resultado: 


```csharp
var unitResolution = background.ContentSize;
var pixelResolution = background.ContentSize;
renderTexture = new CCRenderTexture(unitResolution, pixelResolution);
```

![](ccrendertexture-images/image9.png "Para comparar, pode alterar o valor de pixelResolution para coincidir com o plano de fundo. ContentSize sem sendo duplicado e comparar o resultado")


### <a name="rendering-to-a-ccrendertexture"></a>Renderização de um CCRenderTexture

Normalmente, os objetos visuais em CocosSharp não são renderizados explicitamente. Em vez disso, os objetos visual são adicionados a um `CCLayer` que é parte de um `CCScene`. CocosSharp automaticamente processa o `CCScene` e sua hierarquia visual em cada quadro sem qualquer código de renderização que está sendo chamado. 

Por outro lado, o `CCRenderTexture` devem ser definidas explicitamente para. Esse processamento pode ser dividido em três etapas:

1. `CCRenderTexture.BeginWithClear` é chamado, indicando que toda a renderização subsequente será direcionada a chamada `CCRenderTexture`.
1. Objetos herdando `CCNode` (como o `Card` entidade) são renderizados para o `CCRenderTexture` chamando `Visit`.
1. `CCRenderTexture.End` é chamado, indicando que a renderização para o `CCRenderTexture` for concluída.

Qualquer número de objetos pode ser renderizado em um `CCRenderTexture` entre seus `Begin` e `End` chamadas. Antes do processamento, todos os objetos visíveis necessários são adicionados como filhos:


```csharp
bool areVisualComponentsAlreadyAdded = this.Children != null && this.Children.Contains(visualComponents[0]);
if (!areVisualComponentsAlreadyAdded)
{
    // Temporarily add them so we can render the object:
    foreach (var component in visualComponents)
    {
        this.AddChild(component);
    }
}
```

O `renderTexture` não deve fazer parte do cartão durante a renderização, portanto, ele é removido:


```csharp
// We don't want the render texture to be a child of the card 
// when we call Visit
if (this.Children != null && this.Children.Contains(renderTexture.Sprite))
{
    this.RemoveChild(renderTexture.Sprite);
}
```

Agora o `Card` instância pode processar automaticamente para a `CCRenderTexture` instância:


```csharp
// Clears the CCRenderTexture
renderTexture.BeginWithClear(CCColor4B.Transparent);
// Visit renders this object and all of its children
this.Visit();
// Ends the rendering, which means the CCRenderTexture's Sprite can be used
renderTexture.End();
```

Quando o processamento for concluído, os componentes individuais são removidos e o `CCRenderTexture` é adicionado novamente.


```csharp
// We no longer want the individual components to be drawn, so remove them:
foreach (var component in visualComponents)
{
    this.RemoveChild(component);
}
// add the render target sprite to this:
this.AddChild(renderTexture.Sprite);
```

## <a name="summary"></a>Resumo

Abordadas neste guia de `CCRenderTexture` classe usando um `Card` entidade que pode ser usada em um jogo de cartão de coleção. Ele mostrou como usar o `CCRenderTexture` classe para melhorar a taxa de quadros e implementar adequadamente a transparência de toda a entidade.

Embora este guia utilizado um `CCRenderTexture` contido dentro de uma entidade, essa classe pode ser usado para processar várias entidades, ou até mesmo toda `CCLayer` instâncias para efeitos de toda a tela e melhorias de desempenho.

## <a name="related-links"></a>Links relacionados

- [Referência da API CCRenderTexture](https://developer.xamarin.com/api/type/CocosSharp.CCRenderTexture/)
- [Total do projeto (exemplo)](https://developer.xamarin.com/samples/mobile/CCRenderTexture/)
