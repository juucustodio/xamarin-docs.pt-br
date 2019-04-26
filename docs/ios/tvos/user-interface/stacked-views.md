---
title: Trabalhando com exibições do tvOS empilhadas no Xamarin
description: Este documento descreve como trabalhou com tvOS empilhadas exibições em um aplicativo criado com o Xamarin. Ele fornece uma visão geral dos modos de exibição empilhadas e discute o Layout automático, posicionar e dimensionar uma exibição empilhada, usos comuns, integração com Storyboards e muito mais.
ms.prod: xamarin
ms.assetid: 00B07F85-F30B-4DD4-8664-A61D0A1CDB0E
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: f51ed3d6dbbfc8a7e430c2949485838a7471e545
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61161155"
---
# <a name="working-with-tvos-stacked-views-in-xamarin"></a>Trabalhando com exibições do tvOS empilhadas no Xamarin

O controle de exibição de pilha (`UIStackView`) aproveita o poder do Layout automático e Classes de tamanho para gerenciar uma pilha de subexibições, horizontal ou verticalmente, que responde dinamicamente às alterações de conteúdo e o tamanho da tela do dispositivo da Apple TV.

O layout de todas as subexibições anexado a uma exibição de pilha são gerenciadas por ela com base nas propriedades de desenvolvedor definido como eixo, distribuição, alinhamento e espaçamento:

[![](stacked-views-images/stacked01.png "Diagrama de layout do modo de exibição secundário")](stacked-views-images/stacked01.png#lightbox)

Ao usar um `UIStackView` em um aplicativo xamarin. tvos, o desenvolvedor pode definir as subexibições tanto dentro de um Storyboard no Designer do iOS ou adicionando e removendo subexibições no C# código.

## <a name="about-stacked-view-controls"></a>Sobre controles de exibição empilhada 

O `UIStackView` foi projetado como uma exibição do contêiner de não renderização e como tal, ele não é desenhado à tela, como outras subclasses de `UIView`. Definir propriedades como `BackgroundColor` ou a substituição de `DrawRect` terá nenhum efeito visual.

Há várias propriedades que controlam como um modo de exibição de pilha será organizar sua coleção de subexibições:

- **Eixo** – determina se o modo de exibição de pilha organiza as subexibições tanto **horizontalmente** ou **verticalmente**.
- **Alinhamento** – controla como as subexibições são alinhadas dentro da exibição de pilha.
- **Distribuição** – controla como as subexibições são dimensionadas dentro da exibição de pilha.
- **Espaçamento** – controla o espaço mínimo entre cada modo de exibição secundário na exibição da pilha.
- **Linha de base relativa** – se `true`, o espaçamento vertical de cada modo de exibição secundário será derivado da sua linha de base.
- **Layout margens relativo** – coloca as subexibições relativa às margens do layout padrão.

Normalmente, você usará uma exibição de pilha para organizar um pequeno número de subexibições. Interfaces de usuário mais complexas podem ser criadas por um ou mais modos de exibição de pilha dentro uns aos outros de aninhamento.

Você pode ajustar ainda mais a aparência de interfaces do usuário adicionando restrições adicionais para as subexibições (por exemplo, para o controle a altura ou largura). No entanto, tome cuidado para não incluir restrições conflitantes aos introduzidos pela exibição de pilha em si.

<a name="Auto-Layout-and-Size-Classes" />

## <a name="auto-layout-and-size-classes"></a>Layout automático e Classes de tamanho

Quando um modo de exibição secundário é adicionado a um modo de exibição de pilha seu layout é totalmente controlado pela pilha de exibição usando Classes de tamanho e o Layout automático para posicionar e dimensionar os modos de exibição organizados.

O modo de exibição de pilha serão _pin_ o primeiro e último modo de exibição secundário em sua coleção para o **superior** e **inferior** bordas para modos de exibição de pilha Vertical ou o **deixado**e **direita** bordas para modos de exibição de pilha Horizontal. Se você definir a `LayoutMarginsRelativeArrangement` propriedade para `true`, em seguida, o modo de exibição fixa as subexibições às margens relevantes, em vez de borda.

A exibição de pilha usa o modo de exibição de secundário `IntrinsicContentSize` propriedade ao calcular o tamanho de subexibições junto a definidos `Axis` (exceto para o `FillEqually Distribution`). O `FillEqually Distribution` redimensiona todas as subexibições para que eles têm o mesmo tamanho, preenchimento, portanto, o modo de exibição de pilha ao longo de `Axis`.

Com exceção do `Fill Alignment`, a exibição de pilha usa o modo de exibição de secundário `IntrinsicContentSize` propriedade para calcular o tamanho da exibição do perpendicular à determinado `Axis`. Para o `Fill Alignment`, todas as subexibições são dimensionadas para que elas preencham a exibição de pilha perpendicular à determinado `Axis`.

<a name="Positioning-and-Sizing-the-Stack-View" />

## <a name="positioning-and-sizing-the-stack-view"></a>Posicionar e dimensionar o modo de exibição de pilha

Enquanto o modo de exibição de pilha tem total controle sobre o layout de qualquer modo de exibição secundário (com base nas propriedades, como `Axis` e `Distribution`), você ainda precisa posicionar a exibição de pilha (`UIStackView`) em sua exibição pai usando Classes de tamanho e o Layout automático.

Em geral, isso significa que pelo menos duas bordas de exibição de pilha para expandir e contrair, definindo, portanto, sua posição de fixação. Sem nenhuma restrição adicional, o modo de exibição de pilha será redimensionado automaticamente para atender a todas as suas subexibições da seguinte maneira:

* O tamanho ao longo de seu `Axis` será a soma de todos os tamanhos de modo de exibição secundário além de qualquer espaço que foi definido entre cada modo de exibição secundário.
* Se o `LayoutMarginsRelativeArrangement` é de propriedade `true`, o tamanho da pilha de modos de exibição também incluirá o espaço para as margens.
* O tamanho perpendicular ao `Axis` será definido como o modo de exibição secundário maior na coleção.

Além disso, você pode especificar restrições para o modo de exibição de pilha **altura** e **largura**. Nesse caso, as subexibições serão dispostas (tamanho) para preencher o espaço especificado pela exibição de pilha, conforme determinado pela `Distribution` e `Alignment` propriedades.

Se o `BaselineRelativeArrangement` é de propriedade `true`, as subexibições serão dispostas com base na linha de base do subexibição primeiro ou último, em vez de usar o **superior**, **inferior** ou **Center* -  **Y** posição. Eles são calculados sobre o conteúdo do modo de exibição de pilha da seguinte maneira:

* Um modo de exibição de pilha Vertical retornará o primeiro modo de exibição de secundário para a primeira linha de base e o último para o último. Se alguma desses subexibições é próprios modos de exibição de pilha, em seguida, sua linha de base do primeira ou última será usada.
* Um modo de exibição de pilha Horizontal usará sua subexibição mais alta para ambas as primeira e a última linha de base. Se o modo de exibição mais alto também é uma exibição de pilha, ele usará subexibição mais alta é como a linha de base.

> [!IMPORTANT]
> Alinhamento da linha de base não funciona em tamanhos de subexibição ampliada ou compactado como a linha de base será calculada para a posição errada. Para o alinhamento da linha de base, certifique-se de que o modo de exibição de secundário **altura** coincide com a exibição de conteúdo intrínseco **altura**.




<a name="Common-Stack-View-Uses" />

## <a name="common-stack-view-uses"></a>Usos comuns de modo de exibição de pilha

Há vários tipos de layout que funcionam bem com controles de exibição de pilha. De acordo com a Apple, aqui estão alguns dos usos mais comuns:

- **Definir o tamanho ao longo do eixo** – fixando ambas as bordas ao longo do modo de exibição de pilha `Axis` e uma das bordas adjacentes para definir a posição, a pilha de modo de exibição aumentará ao longo do eixo de acordo com o espaço definido por suas subexibições.
*  **Definir a posição do modo de exibição de secundário** – fixando bordas adjacentes da exibição da pilha para exibição do pai, o modo de exibição de pilha aumentará em ambas as dimensões de acordo com seu recipiente subexibições.
- **Definir o tamanho e posição da pilha** – fixando todas as quatro bordas do modo de exibição de pilha para o modo de exibição de pai, o modo de exibição de pilha organiza as subexibições com base no espaço de definido dentro da exibição de pilha.
*  **Definir o tamanho Perpendicular do eixo** – fixando perpendicular ambas as bordas no modo de exibição de pilha `Axis` e uma das bordas ao longo do eixo para definir a posição, a pilha de modo de exibição crescerá perpendicular ao eixo de acordo com o espaço definido por seu subexibições.

<a name="Stack-Views-and-Storyboards" />

## <a name="stack-views-and-storyboards"></a>Modos de exibição de pilha e Storyboards

A maneira mais fácil trabalhar com modos de exibição de pilha em um aplicativo xamarin. tvos é adicioná-los à interface de usuário do aplicativo usando o Designer do iOS.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

1. No **painel de soluções**, clique duas vezes no `Main.storyboard` de arquivo e abri-lo para edição.
1. Crie o layout de seus elementos individuais que você pretende adicionar ao modo de exibição de pilha: 

    [![](stacked-views-images/layout01.png "Exemplo de layout do elemento")](stacked-views-images/layout01.png#lightbox)
1. Adicione quaisquer restrições necessárias para os elementos para garantir que eles dimensionadas corretamente. Esta etapa é importante, depois que o elemento é adicionado à exibição de pilha.
1. Verifique o número necessário de cópias (quatro, neste caso): 

    [![](stacked-views-images/layout02.png "O número necessário de cópias")](stacked-views-images/layout02.png#lightbox)
1. Arraste uma **exibição de pilha** da **caixa de ferramentas** e solte-o no modo de exibição: 

    [![](stacked-views-images/layout03.png "Um modo de exibição de pilha")](stacked-views-images/layout03.png#lightbox)
1. Selecione o modo de exibição de pilha, no **guia de Widget** da **painel de propriedades** selecionar **preencher** para o **alinhamento**, **preencher Igualmente** para o **distribuição** e insira `25` para o **espaçamento**: 

    [![](stacked-views-images/layout04.png "Na guia de Widget")](stacked-views-images/layout04.png#lightbox)
1. Posicione a exibição de pilha na tela onde você deseja que ele e adicionar restrições para mantê-lo no local necessário.
1. Selecione os elementos individuais e arrastá-los para o modo de exibição de pilha: 

    [![](stacked-views-images/layout05.png "Os elementos individuais na exibição da pilha")](stacked-views-images/layout05.png#lightbox)
1. O layout será ajustado e os elementos serão organizados na exibição da pilha, com base nos atributos definido acima.
1. Atribua **nomes** no **guia Widget** do **Gerenciador de propriedades** para trabalhar com os controles de interface do usuário em C# código.
1. Salve as alterações.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. No **Gerenciador de soluções**, clique duas vezes no `Main.storyboard` de arquivo e abri-lo para edição.
1. Crie o layout de seus elementos individuais que você pretende adicionar ao modo de exibição de pilha: 

    [![](stacked-views-images/layout01.png "Layout do elemento de exemplo")](stacked-views-images/layout01.png#lightbox)
1. Adicione quaisquer restrições necessárias para os elementos para garantir que eles dimensionadas corretamente. Esta etapa é importante, depois que o elemento é adicionado à exibição de pilha.
1. Verifique o número necessário de cópias (quatro, neste caso): 

    [![](stacked-views-images/layout02.png "O número necessário de cópias")](stacked-views-images/layout02.png#lightbox)
1. Arraste uma **exibição de pilha** da **caixa de ferramentas** e solte-o no modo de exibição: 

    [![](stacked-views-images/layout03-vs.png "Um modo de exibição de pilha")](stacked-views-images/layout03-vs.png#lightbox)
1. Selecione o modo de exibição de pilha, no **guia de Widget** da **Gerenciador de propriedades** selecionar **preencher** para o **alinhamento**, **preencher Igualmente** para o **distribuição** e insira `25` para o **espaçamento**: 

    [![](stacked-views-images/layout04-vs.png "Na guia de Widget")](stacked-views-images/layout04-vs.png#lightbox)
1. Posicione a exibição de pilha na tela onde você deseja que ele e adicionar restrições para mantê-lo no local necessário.
1. Selecione os elementos individuais e arrastá-los para o modo de exibição de pilha: 

    [![](stacked-views-images/layout05-vs.png "Os elementos individuais na exibição da pilha")](stacked-views-images/layout05-vs.png#lightbox)
1. O layout será ajustado e os elementos serão organizados na exibição da pilha, com base nos atributos definido acima.
1. Atribua **nomes** no **guia Widget** do **Gerenciador de propriedades** para trabalhar com os controles de interface do usuário em C# código.
1. Salve as alterações.

-----

> [!IMPORTANT]
> Embora seja possível atribuir ações, como `TouchUpInside` a um elemento de interface do usuário (como um `UIButton`) no iOS Designer ao criar um manipulador de eventos, ele nunca será chamado como Apple TV não tem um toque de tela ou dar suporte a eventos de toque. Você sempre deve usar o padrão `Action Type` durante a criação de ações para tvOS elementos da interface do usuário.

Para obter mais informações sobre como trabalhar com Storyboards, consulte nosso [Olá, guia de início rápido do tvOS](~/ios/tvos/get-started/hello-tvos.md).

No caso do nosso exemplo, expomos uma tomada e a ação para o controle de segmento e uma saída para cada cartão"player". No código, podemos ocultar e mostrar o player com base no segmento atual. Por exemplo:

```csharp
partial void PlayerCountChanged (Foundation.NSObject sender) {

    // Take Action based on the segment
    switch(PlayerCount.SelectedSegment) {
    case 0:
        Player1.Hidden = false;
        Player2.Hidden = true;
        Player3.Hidden = true;
        Player4.Hidden = true;
        break;
    case 1:
        Player1.Hidden = false;
        Player2.Hidden = false;
        Player3.Hidden = true;
        Player4.Hidden = true;
        break;
    case 2:
        Player1.Hidden = false;
        Player2.Hidden = false;
        Player3.Hidden = false;
        Player4.Hidden = true;
        break;
    case 3:
        Player1.Hidden = false;
        Player2.Hidden = false;
        Player3.Hidden = false;
        Player4.Hidden = false;
        break;
    }
}
```

Quando o aplicativo é executado, os quatro elementos serão distribuídos igualmente na nossa exibição de pilha:

[![](stacked-views-images/layout06.png "Quando o aplicativo é executado, quatro elementos serão igualmente distribuídos na nossa exibição de pilha")](stacked-views-images/layout06.png#lightbox)

Se o número de jogadores é reduzido, as exibições não utilizadas estão ocultos e o modo de exibição de pilha de ajustar o layout de acordo com:

[![](stacked-views-images/layout07.png "Se o número de jogadores é reduzido, as exibições não utilizadas estão ocultos e o modo de exibição de pilha de ajustar o layout para caber")](stacked-views-images/layout07.png#lightbox)

<a name="Populate-a-Stack-View-from-Code" />

### <a name="populate-a-stack-view-from-code"></a>Preencher uma exibição da pilha de código

Além de definir totalmente o conteúdo e o layout de um modo de pilha no iOS Designer, você pode criar e removê-lo dinamicamente a partir do C# código.

Veja o exemplo a seguir que usa um modo de exibição de pilha para lidar com "estrelas" em uma revisão (de 1 a 5):

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

<a name="Dynamically-Changing-Content" />

## <a name="dynamically-changing-content"></a>Alterar dinamicamente o conteúdo

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

<a name="Summary" />

## <a name="summary"></a>Resumo

Este artigo abordou a projetar e trabalhar com o modo de exibição empilhada dentro de um aplicativo xamarin. tvos.



## <a name="related-links"></a>Links relacionados

- [Amostras do tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [Guias de Interface humana do tvOS](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guia de programação de aplicativo para tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
