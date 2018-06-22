---
title: Animação com CCAction
description: A classe CCAction simplifica adicionar animações CocosSharp jogos. Essas animações podem ser usadas para implementar a funcionalidade de ou para adicionar polonês.
ms.prod: xamarin
ms.assetid: 74DBD02A-6F10-4104-A61B-08CB49B733FB
author: charlespetzold
ms.author: chape
ms.date: 03/24/2017
ms.openlocfilehash: b6209816f741423f40945a0fe4391fe921cb35de
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/09/2018
ms.locfileid: "33921781"
---
# <a name="animating-with-ccaction"></a>Animação com CCAction

_A classe CCAction simplifica adicionar animações CocosSharp jogos. Essas animações podem ser usadas para implementar a funcionalidade de ou para adicionar polonês._

`CCAction` é uma classe base que pode ser usada para animar objetos CocosSharp. Este guia aborda interno `CCAction` implementações para tarefas comuns, como posicionamento, escala e rotação. Examina também como criar implementações personalizadas ao herdar de `CCAction`.

Este guia usa um projeto chamado **ActionProject** que [pode ser baixado em](https://developer.xamarin.com/samples/mobile/CCAction). Este guia usa o `CCDrawNode` classe, que é abordado a [geometria desenho com CCDrawNode](~/graphics-games/cocossharp/ccdrawnode.md) guia.


## <a name="running-the-actionproject"></a>Executando o ActionProject

**ActionProject** é uma solução de CocosSharp que pode ser criada para iOS e Android. Ele serve como um exemplo de código de como usar o `CCAction` classe e como uma demonstração em tempo real do common `CCAction` implementações.

Quando em execução, ActionProject exibe três `CCLabel` instâncias no lado esquerdo da tela e um objeto visual desenhada por dois `CCDrawNode` instâncias para exibir as diversas ações:

![](ccaction-images/image1.png "ActionProject exibe três instâncias de CCLabel à esquerda da tela e um objeto visual desenhadas por duas instâncias de CCDrawNode para exibir as diversas ações")

Os rótulos no lado esquerdo indicam que tipo de `CCAction` será criado quando tocar na tela. Por padrão, o **posição** valor estiver selecionado, resultando em um `CCMove` ação que está sendo criada e aplicada ao círculo vermelho:

![](ccaction-images/image2.gif "O valor da posição é selecionado, resultando em uma ação de CCMove está sendo criado e aplicado ao círculo vermelho")

Quando você clica os rótulos no lado esquerdo altera o tipo de `CCAction` é executada em um círculo. Por exemplo, clicar no **posição** rótulo percorrerá os diferentes valores que podem ser alterados:

![](ccaction-images/image3.gif "Clique o rótulo de posição para percorrer os diferentes valores que podem ser alterados")

## <a name="common-variable-changing-ccaction-classes"></a>Classes de CCAction comuns para alterar a variável

O **ActionProject** usa os seguintes `CCAction`-herança de classes, que fazem parte do CocosSharp:

 - `CCMoveTo` – Altera um `CCNode` da instância `Position` propriedade
 - `CCScaleTo` – Altera um `CCNode` da instância `Scale` propriedade
 - `CCRotateTo` – Altera um `CCNode` da instância `Rotation` propriedade

Este guia se refere a essas ações como *alterar a variável*, o que significa que eles afetam diretamente a variável do `CCNode` que eles são adicionados a. Outros tipos de ações são chamados de *atenuação* ações, que serão abordadas posteriormente neste guia.

O **ActionProject** demonstra que a finalidade dessas ações é modificar uma variável ao longo do tempo. Especificamente, eles `CCActions` construtores levam dois argumentos: comprimento de hora e o valor a ser atribuído. O trecho de código a seguir mostra como os três tipos de ações são criados:


```csharp
switch (VariableOptions [currentVariableIndex])
{
    case "Position":
        coreAction = new CCMoveTo(timeToTake, touch.Location);

        break;
    case "Scale":
        var distance = CCPoint.Distance (touch.Location, drawNodeRoot.Position);
        var desiredScale = distance / DefaultCircleRadius;
        coreAction = new CCScaleTo(timeToTake, desiredScale);

        break;
    case "Rotation":
        float differenceY = touch.Location.Y - drawNodeRoot.PositionY;
        float differenceX = touch.Location.X - drawNodeRoot.PositionX;

        float angleInDegrees = -1 * CCMathHelper.ToDegrees(
            (float)System.Math.Atan2(differenceY, differenceX));

        coreAction = new CCRotateTo (timeToTake, angleInDegrees);

        break; 
...
}
```

Quando a ação é criada, ele é adicionado a um CCNode da seguinte maneira:


```csharp
nodeToAddTo.AddAction (coreAction); 
```

`AddAction` Inicia o `CCAction` comportamento da instância e ele executará automaticamente sua lógica após a quadro até a conclusão.

Cada um dos tipos listados acima termina com a palavra *para* que significa que o `CCAction` modificará o `CCNode` para que o valor do argumento representa o estado final quando a ação for concluída. Por exemplo, criando uma `CCMoveTo` com uma posição X = 100 e Y = 200 resulta no `CCNode` da instância `Position` sendo definida como X = 100, Y = 200 no final do tempo especificado, independentemente do `CCNode` local inicial da instância.

"A" classe também possuem uma versão "Por", que adicionará o valor do argumento para o valor atual no `CCNode`. Por exemplo, criando uma `CCMoveBy` com uma posição X = 100 e Y = 200 resultará no `CCNode` instância que está sendo movida para as unidades de direito 100 e 200 unidades da posição em que ele se encontrava quando a ação foi iniciada.


## <a name="easing-actions"></a>Ações de atenuação

Por padrão, irá realizar ações de alteração de variável *interpolação linear* – a ação será movido para o valor desejado a uma taxa constante. Se a interpolação *posição* linearmente, o objeto mover imediatamente será iniciar e parar de se mover no início e no final da ação, e sua velocidade permanecerá constante como a ação é executada. 

Interpolação não linear é menos gritante e adiciona um elemento de polonês, portanto CocosSharp oferece uma variedade de atenuação as ações que podem ser usadas para modificar ações de alteração de variável.

No **ActionProject** exemplo, é possível alternar entre esses tipos de ações de atenuação clicando no rótulo da segunda (que assume como padrão **<None>**):

![](ccaction-images/image4.gif "O usuário pode alternar entre esses tipos de ações de atenuação clicando no rótulo da segunda")

Ações de atenuação são especialmente eficientes porque eles não estão vinculados a qualquer ação específica de configuração de variável. Isso significa que a mesma ação de atenuação pode ser usada para atribuir posição, rotação, escala ou ações personalizadas (como será mostrado neste documento).

Ações de atenuação wrap ações de configuração de variável (desde que a ação de configuração de variável herda de `CCFiniteTimeAction`) ao aceitar uma ação de configuração de variável como um argumento em seus construtores.

Por exemplo, se os rótulos são definidos como **posição**, **CCEaseElastic**, em seguida, o código a seguir será executado quando for detectado um toque (Observe que o código foi omitido para realçar as linhas relevantes):


```csharp
CCFiniteTimeAction coreAction = null; 
...
coreAction = new CCMoveTo(timeToTake, touch.Location); 
...
CCAction easing = null; 
...
easing = new CCEaseSineOut (coreAction); 
...
nodeToAddTo.AddAction (easing); 
```

Conforme mostrado pelo aplicativo, facilitando mesmo exata pode ser aplicada a outras ações de configuração de variável, como `CCRotateTo`:

![](ccaction-images/image5.gif "Atenuação mesmo exata pode ser aplicada a outras ações de configuração de variável como CCRotateTo")


## <a name="easing-in-out-and-inout"></a>Atenuação In, Out e InOut

Todas as ações de atenuação tem `In`, `Out`, ou `InOut` anexado ao tipo de atenuação. Esses termos referem-se quando a atenuação é aplicado: `In` significa atenuação será aplicada no início, `Out` significa no final, e `InOut` significa tanto no início e término.

Um `In` facilitando a ação afetará a forma como uma variável é aplicada em toda a interpolação inteira (tanto no início e no final), mas geralmente as características mais reconhecíveis da ação de atenuação ocorrerá no início. Da mesma forma, `Out` atenuação ações são caracterizadas por seu comportamento no final de interpolação. Por exemplo, `CCEaseBounceOut` resultará em um objeto saltando no final da ação.


### <a name="out"></a>Out

`Out` atenuação geralmente aplica as alterações mais perceptíveis no final de interpolação. Por exemplo, `CCEaseExponentialOut` reduzirá a taxa de alteração da variável de alteração que se aproximar o valor de destino:

![](ccaction-images/image6.gif "CCEaseExponentialOut reduzirá a taxa de alteração da variável de alteração conforme ela se aproxima o valor de destino")


### <a name="in"></a>No

`In` atenuação geralmente se aplica a alteração mais perceptível no início de interpolação. Por exemplo, `CCEaseExponentialIn` moverá mais lentamente no início da ação:

![](ccaction-images/image7.gif "CCEaseExponentialIn moverá mais lentamente no início da ação")


### <a name="inout"></a>Entrada/saída

`InOut` Geralmente aplica as alterações mais perceptíveis no início e fim. `InOut` atenuação é geralmente simétrica. Por exemplo, `CCEaseExponentialInOut` moverá lentamente no início e no final da ação:

![](ccaction-images/image8.gif "CCEaseExponentialInOut moverá lentamente no início e no final da ação")


## <a name="implementing-a-custom-ccaction"></a>Implementando um CCAction personalizado

Todas as classes que já discutimos até agora são incluídas no CocosSharp para fornecer funcionalidade comum. Personalizar `CCAction` implementações podem fornecer flexibilidade adicional. Por exemplo, um `CCAction` que controla a taxa de preenchimento de uma barra de experiência pode ser usado para que a barra de experiência perfeita aumenta sempre que o usuário ganha experiência.

`CCAction` implementações normalmente requerem duas classes:

 - `CCFiniteTimeAction` implementação – a classe de ação de tempo finito é responsável por iniciar a ação. É a classe que é instanciada e seja adicionada diretamente para um `CCNode` ou uma ação de atenuação. Ele deve instanciar e retornar um `CCFiniteTimeActionState`, que irá executar atualizações.
 - `CCFiniteTimeActionState` implementação – a classe de estado de ação de tempo finito é responsável por atualizar as variáveis envolvidas na ação. Ele deve implementar uma função de atualização, que atribui o valor de destino de acordo com um valor de tempo. Essa classe não for referenciada explicitamente fora do `CCFiniteTimeAction` que o cria. Ele simplesmente funciona "nos bastidores".

**ActionProject** fornece um personalizado `CCFiniteTimeAction` implementação chamado `LineWidthAction,` que é usada para ajustar a largura da linha amarela desenhada na parte superior de um círculo vermelho. Observe que o trabalho só é instanciar e retornar um `LineWidthState` instância:


```csharp
public class LineWidthAction : CCFiniteTimeAction
{
    float endWidth;

    public LineWidthAction (float duration, float width) : base(duration)
    {
        endWidth = width;
    }

    public override CCFiniteTimeAction Reverse ()
    {
        throw new NotImplementedException ();
    }

    protected override CCActionState StartAction (CCNode target)
    {
        return new LineWidthState (this, target, endWidth);
    }
}
```

Como mencionado acima, o `LineWidthState` faz o trabalho de atribuição da linha `Width` propriedade de acordo com a quantidade `time` passou:


```csharp
public class LineWidthState : CCFiniteTimeActionState
{
    float deltaWidth;
    float startWidth;

    LineNode castedTarget;

    public LineWidthState(LineWidthAction action, CCNode target, float endWidth) : base(action, target)
    {
        castedTarget = target as LineNode;

        if (castedTarget == null)
        {
            throw new InvalidOperationException ("The argument target must be a LineNode");
        }

        startWidth = castedTarget.Width;
        deltaWidth = endWidth - startWidth;
    }

    public override void Update (float time)
    {
        castedTarget.Width = startWidth + deltaWidth * time;
    }
} 
```

O LineWidthAction pode ser combinado com qualquer ação atenuação para alterar a largura da linha de várias maneiras, conforme mostrado na animação a seguir:

![](ccaction-images/image9.gif "O LineWidthAction pode ser combinado com qualquer ação atenuação para alterar a largura da linha de várias maneiras, conforme mostrado nesta animação")


### <a name="interpolation-and-the-update-method"></a>Interpolação e o método de atualização

A lógica única, além de armazenar valores nas classes acima, reside no `LineWidthState.Update` método. O `startWidth` variável armazena a largura do destino `LineNode` no início da ação e o `deltaWidth` variável armazena quanto o valor será alterado ao longo da ação.

Substituindo o `time` variável com um valor de 0, podemos ver que o destino `LineNode` em sua posição inicial:


```csharp
castedTarget.Width = startWidth + deltaWidth * 0; 
```

Da mesma forma, podemos ver que o destino `LineNode` estarão em seu destino, substituindo a variável de tempo com um valor de 1:


```csharp
castedTarget.Width = startWidth + deltaWidth * 1; 
```

O `time` geralmente será valor entre 0 e 1 - mas não sempre - e `Update` implementações não devem presumir que esses limites. Alguns métodos de atenuação (como `CCEaseBackIn` e `CCEaseBackOut`) fornecerá um valor de hora fora do intervalo de 0 a 1.


## <a name="conclusion"></a>Conclusão

Interpolação e atenuação são uma parte fundamental da criação de um jogo elegante, especialmente ao criar interfaces do usuário. Este guia aborda como usar `CCActions` para interpolar valores padrão, como a posição e a rotação, bem como valores personalizados. O `LineWidthState` e `LineWidthAction` classes mostram como implementar uma ação personalizada.

## <a name="related-links"></a>Links relacionados

- [CCAction](https://developer.xamarin.com/api/type/CocosSharp.CCAction)
- [CCMoveTo](https://developer.xamarin.com/api/type/CocosSharp.CCMoveTo)
- [CCScaleTo](https://developer.xamarin.com/api/type/CocosSharp.CCScaleTo)
- [CCRotateTo](https://developer.xamarin.com/api/type/CocosSharp.CCRotateTo)
- [CCDrawNode](https://developer.xamarin.com/api/type/CocosSharp.CCDrawNode)
- [Exemplo completo](https://developer.xamarin.com/samples/mobile/CCAction/)
