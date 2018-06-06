---
title: Trabalhando com exibições de empilhada tvOS em Xamarin
description: Este documento descreve como trabalhou com tvOS empilhadas exibições em um aplicativo compilado com o Xamarin. Ele fornece uma visão geral dos modos de exibição empilhadas e descreve o Layout automático, posicionar e dimensionar uma exibição empilhada, usos comuns, integração com Storyboards e mais.
ms.prod: xamarin
ms.assetid: 00B07F85-F30B-4DD4-8664-A61D0A1CDB0E
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/16/2017
ms.openlocfilehash: 7e718e525c23e78fbf846209602a07bf0f3f386e
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/05/2018
ms.locfileid: "34789366"
---
# <a name="working-with-tvos-stacked-views-in-xamarin"></a>Trabalhando com exibições de empilhada tvOS em Xamarin

O controle de exibição de pilha (`UIStackView`) aproveita o poder do Layout automático e Classes de tamanho para gerenciar uma pilha de sub-visualizações, horizontal ou verticalmente, que responde dinamicamente às alterações de conteúdo e o tamanho da tela do dispositivo Apple TV.

O layout de todos os sub-visualizações anexado a um modo de exibição de pilha são gerenciadas por ela com base nas propriedades de desenvolvedor definido como eixo, distribuição, alinhamento e espaçamento:

[![](stacked-views-images/stacked01.png "Diagrama de layout do modo de exibição secundário")](stacked-views-images/stacked01.png#lightbox)

Ao usar um `UIStackView` em um aplicativo de Xamarin.tvOS, o desenvolvedor pode definir sub-visualizações tanto dentro de um Storyboard no iOS Designer ou adicionando e removendo sub-visualizações em código c#.

## <a name="about-stacked-view-controls"></a>Sobre controles de exibição empilhada 

O `UIStackView` foi projetado como um modo de exibição do contêiner não renderização e como tal, não é desenhado à tela como outros subclasses de `UIView`. Definir propriedades como `BackgroundColor` ou substituindo `DrawRect` não terá nenhum efeito visual.

Há várias propriedades que controlam como um modo de exibição de pilha organizará sua coleção de sub-visualizações:

- **Eixo** – determina se o modo de exibição de pilha organiza as subexibições ou **horizontalmente** ou **verticalmente**.
- **Alinhamento** – controla como as subexibições são alinhadas dentro da exibição da pilha.
- **Distribuição** – controla como as subexibições são dimensionadas dentro da exibição da pilha.
- **Espaçamento** – controla o espaço mínimo entre cada subexibição na exibição da pilha.
- **Linha de base relativo** – se `true`, o espaçamento vertical de cada modo de exibição secundário será derivado da sua linha de base.
- **Layout margens relativo** – coloca as sub-visualizações em relação as margens de layout padrão.

Normalmente, você usará uma exibição de pilha para organizar um número pequeno de sub-visualizações. Interfaces de usuário mais complexos podem ser criadas de aninhamento de um ou mais modos de exibição de pilha em si.

Você pode ajustar a aparência de interfaces do usuário mais adicionando restrições adicionais para sub-visualizações (por exemplo, para controlar a altura ou largura). No entanto, tome cuidado para não incluir restrições conflitantes aos introduzido pela exibição de pilha em si.

<a name="Auto-Layout-and-Size-Classes" />

## <a name="auto-layout-and-size-classes"></a>Classes de tamanho e o Layout automático

Quando um modo de exibição secundário é adicionado a uma exibição da pilha de seu layout totalmente é controlado pela pilha de exibição usando Classes de tamanho e Layout automático para posicionar e dimensionar os modos de exibição organizados.

Será o modo de exibição de pilha _pin_ a primeira e o última subexibição em sua coleção para o **superior** e **inferior** bordas para modos de exibição de pilha Vertical ou **deixado**e **direita** bordas para modos de exibição de pilha Horizontal. Se você definir o `LayoutMarginsRelativeArrangement` propriedade `true`, em seguida, o modo de exibição fixa sub-visualizações para as margens relevantes em vez da borda.

A exibição de pilha usa o modo de exibição de secundário `IntrinsicContentSize` propriedade ao calcular o tamanho de sub-visualizações junto a definido `Axis` (exceto para o `FillEqually Distribution`). O `FillEqually Distribution` redimensiona sub-visualizações todos para que eles têm o mesmo tamanho, preenchimento, portanto, o modo de exibição de pilha ao longo de `Axis`.

Com exceção do `Fill Alignment`, a exibição de pilha usa o modo de exibição de secundário `IntrinsicContentSize` propriedade para calcular o tamanho da exibição do perpendicular à determinado `Axis`. Para o `Fill Alignment`, sub-visualizações todos são dimensionadas para que preenchem a exibição de pilha perpendicular à determinado `Axis`.

<a name="Positioning-and-Sizing-the-Stack-View" />

## <a name="positioning-and-sizing-the-stack-view"></a>Posicionar e dimensionar o modo de exibição de pilha

Enquanto o modo de exibição de pilha tem total controle sobre o layout de qualquer modo de exibição secundário (com base em propriedades como `Axis` e `Distribution`), você ainda precisa posicionar a exibição de pilha (`UIStackView`) em sua exibição pai usando Classes de tamanho e o Layout automático.

Em geral, isso significa que pelo menos duas bordas de exibição de pilha para expandir e contrair, definindo, portanto, sua posição de fixação. Sem qualquer restrição adicional, o modo de exibição de pilha serão automaticamente redimensionado para atender a todas as suas sub-visualizações da seguinte maneira:

* O tamanho ao longo de seu `Axis` será a soma de todos os tamanhos de subexibição mais qualquer espaço que foi definido entre cada modo de exibição secundário.
* Se o `LayoutMarginsRelativeArrangement` é de propriedade `true`, o tamanho da pilha exibições também incluirá o espaço para as margens.
* O tamanho perpendicular ao `Axis` será definida como ela é maior na coleção.

Além disso, você pode especificar restrições para o modo de exibição de pilha **altura** e **largura**. Nesse caso, as sub-visualizações serão dispostas (tamanho) para preencher o espaço especificado pela exibição de pilha, conforme determinado pelo `Distribution` e `Alignment` propriedades.

Se o `BaselineRelativeArrangement` é de propriedade `true`, as subexibições serão dispostas com base na linha de base do subexibição primeiro ou último, em vez de usar o **superior**, **inferior** ou **Center* -  **Y** posição. Esses são calculados sobre o conteúdo do modo de exibição de pilha da seguinte maneira:

* Um modo de exibição de pilha Vertical retornará o primeiro modo de exibição secundário para a primeira linha de base e o último para o último. Se qualquer uma desses sub-visualizações próprios modos de exibição de pilha, sua primeira ou última linha de base será usada.
* Um modo de exibição de pilha Horizontal usará seu subexibição mais alta para ambas as a primeira e a última linha de base. Se o modo de exibição mais alto também é uma exibição de pilha, ele usará sua subexibição mais alta como a linha de base.

> [!IMPORTANT]
> Alinhamento de linha de base não funciona em tamanhos de subexibição ampliada ou compactado como a linha de base será calculada para a posição errada. Para o alinhamento da linha de base, certifique-se de que o modo de exibição de secundário **altura** coincide com a exibição de conteúdo intrínseco **altura**.




<a name="Common-Stack-View-Uses" />

## <a name="common-stack-view-uses"></a>Usos comuns de modo de exibição de pilha

Há vários tipos de layout que funcionam bem com controles de exibição de pilha. De acordo com a Apple, aqui estão alguns dos usos mais comuns:

- **Definir o tamanho ao longo do eixo** – fixando ambas as bordas ao longo do modo de exibição de pilha `Axis` e uma das bordas para definir a posição, a pilha de exibição aumentará ao longo do eixo para ajustar o espaço definido por seus sub-visualizações adjacentes.
*  **Definir a posição do subexibição** – fixando bordas adjacentes da exibição da pilha para exibição do pai, o modo de exibição de pilha crescerá em ambas as dimensões de acordo com seu recipiente sub-visualizações.
- **Definir o tamanho e a posição da pilha** – fixando todas as quatro bordas de exibição de pilha para o modo de exibição do pai, o modo de exibição de pilha organiza as subexibições com base no espaço definido dentro da exibição da pilha.
*  **Definir o tamanho Perpendicular eixo** – fixando perpendicular ambas as extremidades para o modo de exibição de pilha `Axis` e uma das bordas ao longo do eixo para definir a posição, a pilha de exibição crescerá perpendicular ao eixo de acordo com o espaço definido por seu sub-visualizações.

<a name="Stack-Views-and-Storyboards" />

## <a name="stack-views-and-storyboards"></a>Modos de exibição de pilha e Storyboards

É a maneira mais fácil trabalhar com exibições de pilha em um aplicativo de Xamarin.tvOS para adicioná-los à interface de usuário do aplicativo usando o Designer do iOS.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

1. No **solução preenchimento**, clicando duas vezes no `Main.storyboard` de arquivo e abri-lo para edição.
1. Crie o layout de seus elementos individuais que você pretende adicionar ao modo de exibição de pilha: 

    [![](stacked-views-images/layout01.png "Exemplo de layout do elemento")](stacked-views-images/layout01.png#lightbox)
1. Adicione quaisquer restrições necessárias para os elementos para garantir que eles dimensionadas corretamente. Esta etapa é importante quando o elemento é adicionado à exibição de pilha.
1. Verifique o número necessário de cópias (quatro neste caso): 

    [![](stacked-views-images/layout02.png "O número de cópias necessárias")](stacked-views-images/layout02.png#lightbox)
1. Arraste um **exibição pilha** do **caixa de ferramentas** e solte-o no modo de exibição: 

    [![](stacked-views-images/layout03.png "Um modo de exibição de pilha")](stacked-views-images/layout03.png#lightbox)
1. Selecione o modo de exibição de pilha, o **Widget guia** do **propriedades de preenchimento** selecione **preencher** para o **alinhamento**, **preenchimento Igualmente** para o **distribuição** e digite `25` para o **espaçamento**: 

    [![](stacked-views-images/layout04.png "A guia de Widget")](stacked-views-images/layout04.png#lightbox)
1. Posicione o modo de exibição de pilha na tela onde você desejar e adicionar restrições para mantê-lo no local necessário.
1. Selecione os elementos individuais e arraste-os para o modo de exibição de pilha: 

    [![](stacked-views-images/layout05.png "Os elementos individuais na exibição da pilha")](stacked-views-images/layout05.png#lightbox)
1. O layout será ajustado e os elementos serão organizados na exibição da pilha com base nos atributos definida acima.
1. Atribuir **nomes** no **Widget guia** do **propriedades Explorer** para trabalhar com os controles de interface do usuário em código c#.
1. Salve as alterações.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. No **Solution Explorer**, clicando duas vezes no `Main.storyboard` de arquivo e abri-lo para edição.
1. Crie o layout de seus elementos individuais que você pretende adicionar ao modo de exibição de pilha: 

    [![](stacked-views-images/layout01.png "Exemplo de layout de elemento")](stacked-views-images/layout01.png#lightbox)
1. Adicione quaisquer restrições necessárias para os elementos para garantir que eles dimensionadas corretamente. Esta etapa é importante quando o elemento é adicionado à exibição de pilha.
1. Verifique o número necessário de cópias (quatro neste caso): 

    [![](stacked-views-images/layout02.png "O número de cópias necessárias")](stacked-views-images/layout02.png#lightbox)
1. Arraste um **exibição pilha** do **caixa de ferramentas** e solte-o no modo de exibição: 

    [![](stacked-views-images/layout03-vs.png "Um modo de exibição de pilha")](stacked-views-images/layout03-vs.png#lightbox)
1. Selecione o modo de exibição de pilha, o **Widget guia** do **propriedades Explorer** selecione **preencher** para o **alinhamento**, **preencher Igualmente** para o **distribuição** e digite `25` para o **espaçamento**: 

    [![](stacked-views-images/layout04-vs.png "A guia de Widget")](stacked-views-images/layout04-vs.png#lightbox)
1. Posicione o modo de exibição de pilha na tela onde você desejar e adicionar restrições para mantê-lo no local necessário.
1. Selecione os elementos individuais e arraste-os para o modo de exibição de pilha: 

    [![](stacked-views-images/layout05-vs.png "Os elementos individuais na exibição da pilha")](stacked-views-images/layout05-vs.png#lightbox)
1. O layout será ajustado e os elementos serão organizados na exibição da pilha com base nos atributos definida acima.
1. Atribuir **nomes** no **Widget guia** do **propriedades Explorer** para trabalhar com os controles de interface do usuário em código c#.
1. Salve as alterações.

-----

> [!IMPORTANT]
> Embora seja possível atribuir ações como `TouchUpInside` para um elemento de interface do usuário (como um `UIButton`) no iOS Designer ao criar um manipulador de eventos, ele nunca será chamado porque Apple TV não tem um toque de tela ou oferecer suporte a eventos de toque. Você sempre deve usar o padrão `Action Type` durante a criação de ações para tvOS elementos da interface do usuário.

Para obter mais informações sobre como trabalhar com Storyboards, consulte nosso [Hello, tvOS Quick Start Guide](~/ios/tvos/get-started/hello-tvos.md).

No caso do nosso exemplo, podemos já exposto uma loja e a ação para o controle de segmento e uma tomada para cada cartão"player". No código, podemos ocultar e mostrar player com base no segmento atual. Por exemplo:

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

Quando o aplicativo é executado, quatro elementos serão igualmente distribuídos em nossa visualização da pilha:

[![](stacked-views-images/layout06.png "Quando o aplicativo é executado, quatro elementos serão igualmente distribuídos em nossa visualização da pilha")](stacked-views-images/layout06.png#lightbox)

Se o número de players é reduzido, os modos de exibição não utilizados são ocultados, e a exibição da pilha de ajustar o layout de acordo com:

[![](stacked-views-images/layout07.png "Se o número de players é reduzido, os modos de exibição não utilizados são ocultados, e a exibição da pilha de ajustar o layout de acordo com")](stacked-views-images/layout07.png#lightbox)

<a name="Populate-a-Stack-View-from-Code" />

### <a name="populate-a-stack-view-from-code"></a>Preencher uma exibição da pilha de código

Além de definir totalmente o conteúdo e o layout de um modo de pilha no iOS Designer, você pode criar e removê-lo dinamicamente do código c#.

Execute o exemplo a seguir que usa um modo de exibição de pilha para lidar com "estrelas" em uma revisão (de 1 a 5):

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

Vamos dar uma olhada em algumas partes do código em detalhes. Primeiro, usamos um `if` instruções para verificar que não há mais de cinco "estrelas" ou menor que zero.

Para adicionar uma nova "estrela" podemos carregar sua imagem e definir seu **modo conteúdo** para **aspecto ajustar**:

```csharp
var icon = new UIImageView (new UIImage("icon.png"));
icon.ContentMode = UIViewContentMode.ScaleAspectFit;
```

Isso impede que o ícone de "estrela" sendo distorcido quando ele é adicionado à exibição de pilha.

Em seguida, adicionamos no novo ícone de "estrela" à coleção do modo de exibição de pilha sub-visualizações:

```csharp
RatingView.AddArrangedSubview(icon);
```

Você observará que adicionamos a `UIImageView` para o `UIStackView`do `ArrangedSubviews` propriedade e não para o `SubView`. Qualquer exibição que você deseja que a exibição de pilha para controlar o layout deve ser adicionada para o `ArrangedSubviews` propriedade.

Para remover um modo de exibição secundário de um modo de exibição de pilha, primeiro obtemos o modo de exibição secundário para remover:

```csharp
var icon = RatingView.ArrangedSubviews[RatingView.ArrangedSubviews.Length-1];
```

Em seguida, é preciso removê-lo de ambos os `ArrangedSubviews` coleta e a exibição de Super:

```csharp
// Remove from stack and screen
RatingView.RemoveArrangedSubview(icon);
icon.RemoveFromSuperview();
```

Removendo um modo de exibição secundário de apenas o `ArrangedSubviews` coleção leva fora do controle do modo de exibição de pilha, mas não a remove da tela.

<a name="Dynamically-Changing-Content" />

## <a name="dynamically-changing-content"></a>Alterando dinamicamente o conteúdo

Um modo de exibição de pilha ajustará automaticamente o layout das subexibições sempre que um modo de exibição secundário é adicionado, removido ou oculto. O layout também será ajustado se qualquer propriedade da exibição de pilha é ajustada (como seu `Axis`).

Alterações de layout podem ser animadas, colocando-os em um bloco de animação, por exemplo:

```csharp
// Animate stack
UIView.Animate(0.25, ()=>{
    // Adjust stack view
    RatingView.LayoutIfNeeded();
});
```

Muitas das propriedades da exibição de pilha podem ser especificadas usando as Classes de tamanho em um Storyboard. Essas propriedades poderão ser automaticamente animado é a resposta a alterações de tamanho ou a orientação.

<a name="Summary" />

## <a name="summary"></a>Resumo

Este artigo abordou criando e trabalhando com exibição empilhada dentro de um aplicativo Xamarin.tvOS.



## <a name="related-links"></a>Links relacionados

- [Amostras do tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS guias de Interface Humana](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guia de programação de aplicativo para tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
