---
title: Modos de exibição de pilha no xamarin. IOS
description: Este artigo aborda usando o novo controle UIStackView em um aplicativo xamarin. IOS para gerenciar um conjunto de subexibições em qualquer um uma pilha organizada horizontal ou verticalmente.
ms.prod: xamarin
ms.assetid: 20246E87-2A49-438A-9BD7-756A1B50A617
ms.technology: xamarin-ios
ms.custom: xamu-video
author: lobrien
ms.author: laobri
ms.date: 03/20/2017
ms.openlocfilehash: d04ce6827f6695163c7d9a8d34acc3891d854c8d
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50122667"
---
# <a name="stack-views-in-xamarinios"></a>Modos de exibição de pilha no xamarin. IOS

_Este artigo aborda usando o novo controle UIStackView em um aplicativo xamarin. IOS para gerenciar um conjunto de subexibições em qualquer um uma pilha organizada horizontal ou verticalmente._

> [!IMPORTANT]
> Observe que embora StackView tenha suporte no iOS Designer, você poderá encontrar bugs de usabilidade ao usar o canal estável. Trocar os canais Beta ou alfa deve aliviar esse problema. Decidimos apresentar neste passo a passo usando o Xcode até que as correções necessárias são implementadas no canal estável.

O controle de exibição de pilha (`UIStackView`) aproveita o poder do Layout automático e Classes de tamanho para gerenciar uma pilha de subexibições, horizontal ou verticalmente, que responde dinamicamente para o tamanho de tela e a orientação do dispositivo iOS.

O layout de todas as subexibições anexado a uma exibição de pilha são gerenciadas por ela com base nas propriedades de desenvolvedor definido como eixo, distribuição, alinhamento e espaçamento:

[![](uistackview-images/stacked01.png "Diagrama de layout de exibição de pilha")](uistackview-images/stacked01.png#lightbox)

Ao usar um `UIStackView` em um aplicativo xamarin. IOS, o desenvolvedor pode definir as subexibições tanto dentro de um Storyboard no Designer do iOS, ou adicionando e removendo subexibições em código c#.

Este documento consiste em duas partes: um guia rápido para ajudá-lo a implementar sua pilha primeiro exibir e, em seguida, mais alguns detalhes técnicos sobre como ele funciona.

> [!VIDEO https://youtube.com/embed/p3po6507Ip8]

**UIStackView, por [Xamarin University](https://university.xamarin.com/)**

## <a name="uistackview-quickstart"></a>Guia de início rápido UIStackView

Como uma introdução rápida a `UIStackView` controle, vamos criar uma interface simples que permitem que o usuário insira uma classificação de 1 a 5. Estaremos usando dois modos de exibição de pilha: uma para organizar a interface verticalmente do dispositivo tela e outra para organizar os ícones de classificação de 1 a 5 horizontalmente pela tela.

### <a name="define-the-ui"></a>Definir a interface do usuário

Iniciar um novo projeto do xamarin. IOS e editar os **Main. Storyboard** arquivo no Interface Builder do Xcode. Primeiro, arraste um único **exibição de pilha Vertical** sobre o **controlador de exibição**:

[![](uistackview-images/quick01.png "Arraste uma única exibição de pilha Vertical no controlador de exibição")](uistackview-images/quick01.png#lightbox)

No **Inspetor de atributo**, defina as seguintes opções:

[![](uistackview-images/quick02.png "Definir as opções de exibição de pilha")](uistackview-images/quick02.png#lightbox)

Sendo que:

- **Eixo** – determina se o modo de exibição de pilha organiza as subexibições tanto **horizontalmente** ou **verticalmente**.
- **Alinhamento** – controla como as subexibições são alinhadas dentro da exibição de pilha.
- **Distribuição** – controla como as subexibições são dimensionadas dentro da exibição de pilha.
- **Espaçamento** – controla o espaço mínimo entre cada modo de exibição secundário na exibição da pilha.
- **Linha de base relativo** – se estiver marcada, o espaçamento vertical de cada modo de exibição secundário será derivado da sua linha de base.
- **Layout margens relativo** – coloca as subexibições relativa às margens do layout padrão.

Ao trabalhar com um modo de exibição de pilha, você pode pensar a **alinhamento** como o **X** e **Y** local do modo de exibição secundário e o **distribuição** como o **Altura** e **largura**.

> [!IMPORTANT]
> `UIStackView` foi projetado como uma exibição do contêiner de não renderização e como tal, ele não é desenhado à tela, como outras subclasses de `UIView`. Portanto, a definição de propriedades, como `BackgroundColor` ou a substituição de `DrawRect` terá nenhum efeito visual.

Continue para o layout a interface do aplicativo adicionando um rótulo, ImageView, dois botões e uma exibição Horizontal de pilha para que ele fique parecido com o seguinte:

[![](uistackview-images/quick03.png "Dispor a interface de usuário do modo de exibição de pilha")](uistackview-images/quick03.png#lightbox)

Configure o modo de exibição de pilha Horizontal com as seguintes opções:

[![](uistackview-images/quick04.png "Configure as opções de exibição Horizontal de pilha")](uistackview-images/quick04.png#lightbox)

Porque não queremos que o ícone que representa cada "ponto" na classificação de alongado quando ele é adicionado à exibição de pilha Horizontal, definimos a **alinhamento** à **Center** e o  **Distribuição** à **preencher igualmente**.

Por fim, conectar o seguinte **tomadas** e **ações**:

[![](uistackview-images/quick05.png "As saídas de modo de exibição de pilha e as ações")](uistackview-images/quick05.png#lightbox)

### <a name="populate-a-uistackview-from-code"></a>Preencher um UIStackView do código

Retorne ao Visual Studio para Mac e edite os **ViewController.cs** arquivo e adicione o código a seguir:

```csharp
public int Rating { get; set;} = 0;
...

partial void IncreaseRating (Foundation.NSObject sender) {

    // Maximum of 5 "stars"
    if (++Rating > 5 ) {
        // Abort
        Rating = 5;
        return;
    }

    // Create new rating icon and add it to stack
    var icon = new UIImageView (new UIImage("icon.png"));
    icon.ContentMode = UIViewContentMode.ScaleAspectFit;
    RatingView.AddArrangedSubview(icon);

    // Animate stack
    UIView.Animate(0.25, ()=>{
        // Adjust stack view
        RatingView.LayoutIfNeeded();
    });

}

partial void DecreaseRating (Foundation.NSObject sender) {

    // Minimum of zero "stars"
    if (--Rating < 0) {
        // Abort
        Rating =0;
        return;
    }

    // Get the last subview added
    var icon = RatingView.ArrangedSubviews[RatingView.ArrangedSubviews.Length-1];

    // Remove from stack and screen
    RatingView.RemoveArrangedSubview(icon);
    icon.RemoveFromSuperview();

    // Animate stack
    UIView.Animate(0.25, ()=>{
        // Adjust stack view
        RatingView.LayoutIfNeeded();
    });
}
```

Vamos dar uma olhada em algumas partes desse código em detalhes. Primeiro, usamos um `if` instruções para verificar que não há mais de cinco "estrelas" ou menor que zero.

Para adicionar uma nova "estrela" podemos carregar sua imagem e defina suas **modo de conteúdo** para **aspecto ajustar**:

```csharp
var icon = new UIImageView (new UIImage("icon.png"));
icon.ContentMode = UIViewContentMode.ScaleAspectFit;
```

Isso impede que o ícone de "estrela" sendo distorcida quando ele é adicionado à exibição de pilha.

Em seguida, adicionamos o novo ícone de "estrela" à coleção do modo de exibição de pilha de subexibições:

```csharp
RatingView.AddArrangedSubview(icon);
```

Você observará que adicionamos a `UIImageView` para o `UIStackView`do `ArrangedSubviews` propriedade e não como o `SubView`. Qualquer exibição que você deseja que o modo de exibição de pilha para controlar seu layout deve ser adicionada para o `ArrangedSubviews` propriedade.

Para remover um modo de exibição secundário de um modo de exibição de pilha, primeiro obtemos o modo de exibição de secundário para remover:

```csharp
var icon = RatingView.ArrangedSubviews[RatingView.ArrangedSubviews.Length-1];
```

Em seguida, precisamos removê-lo em ambos os `ArrangedSubviews` coleta e o modo de exibição de Super:

```csharp
// Remove from stack and screen
RatingView.RemoveArrangedSubview(icon);
icon.RemoveFromSuperview();
```

Removendo um modo de exibição secundário de apenas o `ArrangedSubviews` coleção leva fora do controle do modo de exibição de pilha, mas não o remove da tela.

### <a name="testing-the-ui"></a>Testando a interface do usuário

Todos os necessários elementos de interface do usuário e código no lugar, agora você pode executar e testar a interface. Quando a interface do usuário é exibido, todos os elementos no modo de exibição de pilha Vertical serão espaçados igualmente de cima para baixo.

Quando o usuário toca a **aumentar classificação** botão, outro "estrela" é adicionada à tela (até um máximo de 5):

[![](uistackview-images/intro01.png "A execução do aplicativo de exemplo")](uistackview-images/intro01.png#lightbox)

"Estrelas" serão automaticamente centralizadas e igualmente distribuídas na exibição de pilha Horizontal. Quando o usuário toca a **diminuir classificação** botão, uma "estrela" será removido (até que nenhum estão à esquerda).

## <a name="stack-view-details"></a>Exibir detalhes da pilha

Agora que temos uma ideia geral do que o `UIStackView` controle é e como ele funciona, vamos dar uma olhada mais profunda em alguns dos seus recursos e detalhes.

### <a name="auto-layout-and-size-classes"></a>Layout automático e Classes de tamanho

Como vimos anteriormente, quando um modo de exibição secundário é adicionado a um modo de exibição de pilha de seu layout é totalmente controlado pela pilha de exibição usando Classes de tamanho e o Layout automático para posicionar e dimensionar os modos de exibição organizados.

O modo de exibição de pilha serão _pin_ o primeiro e último modo de exibição secundário em sua coleção para o **superior** e **inferior** bordas para modos de exibição de pilha Vertical ou o **deixado**e **direita** bordas para modos de exibição de pilha Horizontal. Se você definir a `LayoutMarginsRelativeArrangement` propriedade para `true`, em seguida, o modo de exibição fixa as subexibições às margens relevantes, em vez de borda.

A exibição de pilha usa o modo de exibição de secundário `IntrinsicContentSize` propriedade ao calcular o tamanho de subexibições junto a definidos `Axis` (exceto para o `FillEqually Distribution`). O `FillEqually Distribution` redimensiona todas as subexibições para que eles têm o mesmo tamanho, preenchimento, portanto, o modo de exibição de pilha ao longo de `Axis`.

Com exceção do `Fill Alignment`, a exibição de pilha usa o modo de exibição de secundário `IntrinsicContentSize` propriedade para calcular o tamanho da exibição do perpendicular à determinado `Axis`. Para o `Fill Alignment`, todas as subexibições são dimensionadas para que elas preencham a exibição de pilha perpendicular à determinado `Axis`.

### <a name="positioning-and-sizing-the-stack-view"></a>Posicionar e dimensionar o modo de exibição de pilha

Enquanto o modo de exibição de pilha tem total controle sobre o layout de qualquer modo de exibição secundário (com base nas propriedades, como `Axis` e `Distribution`), você ainda precisa posicionar a exibição de pilha (`UIStackView`) em sua exibição pai usando Classes de tamanho e o Layout automático.

Em geral, isso significa que pelo menos duas bordas de exibição de pilha para expandir e contrair, definindo, portanto, sua posição de fixação. Sem nenhuma restrição adicional, o modo de exibição de pilha será redimensionado automaticamente para atender a todas as suas subexibições da seguinte maneira:

 - O tamanho ao longo de seu `Axis` será a soma de todos os tamanhos de modo de exibição secundário além de qualquer espaço que foi definido entre cada modo de exibição secundário.
 - Se o `LayoutMarginsRelativeArrangement` é de propriedade `true`, o tamanho da pilha de modos de exibição também incluirá o espaço para as margens.
 - O tamanho perpendicular ao `Axis` será definido como o modo de exibição secundário maior na coleção.

Além disso, você pode especificar restrições para o modo de exibição de pilha **altura** e **largura**. Nesse caso, as subexibições serão dispostas (tamanho) para preencher o espaço especificado pela exibição de pilha, conforme determinado pela `Distribution` e `Alignment` propriedades.

Se o `BaselineRelativeArrangement` é de propriedade `true`, as subexibições serão dispostas com base na linha de base do subexibição primeiro ou último, em vez de usar o **superior**, **inferior** ou **Center** -  **Y** posição. Eles são calculados sobre o conteúdo do modo de exibição de pilha da seguinte maneira:

 - Um modo de exibição de pilha Vertical retornará o primeiro modo de exibição de secundário para a primeira linha de base e o último para o último. Se alguma desses subexibições é próprios modos de exibição de pilha, em seguida, sua linha de base do primeira ou última será usada.
 - Um modo de exibição de pilha Horizontal usará sua subexibição mais alta para ambas as primeira e a última linha de base. Se o modo de exibição mais alto também é uma exibição de pilha, ele usará subexibição mais alta é como a linha de base.

> [!IMPORTANT]
> Alinhamento da linha de base não funciona em tamanhos de subexibição ampliada ou compactado como a linha de base será calculada para a posição errada. Para o alinhamento da linha de base, certifique-se de que o modo de exibição de secundário **altura** coincide com a exibição de conteúdo intrínseco **altura**.

### <a name="common-stack-view-uses"></a>Usos comuns de modo de exibição de pilha

Há vários tipos de layout que funcionam bem com controles de exibição de pilha. De acordo com a Apple, aqui estão alguns dos usos mais comuns:

- **Definir o tamanho ao longo do eixo** – fixando ambas as bordas ao longo do modo de exibição de pilha `Axis` e uma das bordas adjacentes para definir a posição, a pilha de modo de exibição aumentará ao longo do eixo de acordo com o espaço definido por suas subexibições.
 -  **Definir a posição do modo de exibição de secundário** – fixando bordas adjacentes da exibição da pilha para exibição do pai, o modo de exibição de pilha aumentará em ambas as dimensões de acordo com seu recipiente subexibições.
- **Definir o tamanho e posição da pilha** – fixando todas as quatro bordas do modo de exibição de pilha para o modo de exibição de pai, o modo de exibição de pilha organiza as subexibições com base no espaço de definido dentro da exibição de pilha.
 -  **Definir o tamanho Perpendicular do eixo** – fixando perpendicular ambas as bordas no modo de exibição de pilha `Axis` e uma das bordas ao longo do eixo para definir a posição, a pilha de modo de exibição crescerá perpendicular ao eixo de acordo com o espaço definido por seu subexibições.

### <a name="managing-the-appearance"></a>Gerenciando a aparência

O `UIStackView` foi projetado como uma exibição do contêiner de não renderização e como tal, ele não é desenhado à tela, como outras subclasses de `UIView`. Definir propriedades como `BackgroundColor` ou a substituição de `DrawRect` terá nenhum efeito visual.

Há várias propriedades que controlam como um modo de exibição de pilha será organizar sua coleção de subexibições:

- **Eixo** – determina se o modo de exibição de pilha organiza as subexibições tanto **horizontalmente** ou **verticalmente**.
- **Alinhamento** – controla como as subexibições são alinhadas dentro da exibição de pilha.
- **Distribuição** – controla como as subexibições são dimensionadas dentro da exibição de pilha.
- **Espaçamento** – controla o espaço mínimo entre cada modo de exibição secundário na exibição da pilha.
- **Linha de base relativa** – se `true`, o espaçamento vertical de cada modo de exibição secundário será derivado da sua linha de base.
- **Layout margens relativo** – coloca as subexibições relativa às margens do layout padrão.

Normalmente, você usará uma exibição de pilha para organizar um pequeno número de subexibições. Interfaces de usuário mais complexas podem ser criadas por um ou mais modos de exibição de pilha dentro uns aos outros de aninhamento (como fizemos na [UIStackView Quickstart](#UIStackView-Quickstart) acima).

Você pode ajustar ainda mais a aparência de interfaces do usuário adicionando restrições adicionais para as subexibições (por exemplo, para o controle a altura ou largura). No entanto, tome cuidado para não incluir restrições conflitantes aos introduzidos pela exibição de pilha em si.

### <a name="maintaining-arranged-views-and-sub-views-consistency"></a>Manter organizado modos de exibição e a consistência de modos de exibição de Sub

O modo de exibição de pilha para garantir que seu `ArrangedSubviews` propriedade é sempre um subconjunto de seus `Subviews` propriedade usando as seguintes regras:

 - Se um modo de exibição secundário for adicionado à `ArrangedSubviews` coleta, ele será automaticamente adicionado para o `Subviews` coleção (a menos que ele já é parte da coleção).
 - Se um modo de exibição secundário é removido do `Subviews` coleção (removida da exibição), ele também é removido do `ArrangedSubviews` coleção.
 - Removendo um modo de exibição secundário do `ArrangedSubviews` coleção não o remove do `Subviews` coleção. Portanto, ele não será disposto pela exibição de pilha, mas ainda estarão visível na tela.

O `ArrangedSubviews` coleção sempre é um subconjunto do `Subview` coleção, no entanto a ordem das subexibições individuais dentro de cada coleção é separada e controlado pelo seguinte:

 - A ordem das subexibições dentro a `ArrangedSubviews` coleção determinar sua ordem de exibição dentro da pilha.
 - A ordem das subexibições dentro a `Subview` coleção determina sua ordem Z (ou a disposição em camadas) no modo de exibição para a frente.

### <a name="dynamically-changing-content"></a>Alterar dinamicamente o conteúdo

Um modo de exibição de pilha se ajustará automaticamente o layout das subexibições sempre que um modo de exibição secundário é adicionado, removido ou oculto. O layout também será ajustado se qualquer propriedade da exibição de pilha é ajustada (como seu `Axis`).

As alterações de layout podem ser animadas, colocando-os dentro de um bloco de animação, por exemplo:

```csharp
// Animate stack
UIView.Animate(0.25, ()=>{
    // Adjust stack view
    RatingView.LayoutIfNeeded();
});
```

Muitas das propriedades do modo de exibição de pilha podem ser especificadas usando as Classes de tamanho em um Storyboard. Essas propriedades poderão ser automaticamente animada é a resposta às alterações de tamanho ou orientação.

## <a name="summary"></a>Resumo

Este artigo abordou a nova `UIStackView` controlar (para iOS 9) para gerenciar um conjunto de subexibições em uma pilha organizada horizontal ou verticalmente em um aplicativo xamarin. IOS.
Ele começou com um exemplo simples de usar modos de exibição de pilha para criar uma interface do usuário e terminar com uma visão mais detalhada de modos de exibição de pilha e suas propriedades e recursos.



## <a name="related-links"></a>Links relacionados

- [Amostras do iOS 9](https://developer.xamarin.com/samples/ios/iOS9/)
- [iOS 9 para desenvolvedores](https://developer.apple.com/ios/pre-release/)
- [O que há de novo no iOS 9.0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [Referência de UIStackView](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIStackView_Class_Reference/)
- [Introdução ao UIStackView (vídeo)](https://university.xamarin.com/lightninglectures/introducing-uistackview-on-ios9)
