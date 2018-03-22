---
title: "Exibição de pilha"
description: "Este artigo aborda usando o novo controle de UIStackView em um aplicativo xamarin para gerenciar um conjunto de sub-visualizações em uma pilha organizada horizontal ou verticalmente."
ms.topic: article
ms.prod: xamarin
ms.assetid: 20246E87-2A49-438A-9BD7-756A1B50A617
ms.technology: xamarin-ios
ms.custom: xamu-video
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/20/2017
ms.openlocfilehash: b8d3fc920610b357520dfebddb79492c44d4781c
ms.sourcegitcommit: 73bd0c7e5f237f0a1be70a6c1384309bb26609d5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/22/2018
---
# <a name="stack-view"></a>Exibição de pilha

_Este artigo aborda usando o novo controle de UIStackView em um aplicativo xamarin para gerenciar um conjunto de sub-visualizações em uma pilha organizada horizontal ou verticalmente._

> [!IMPORTANT]
> Observe que enquanto StackView tem suporte no iOS Designer, você pode encontrar erros de usabilidade ao usar o canal estável. Alternando os canais de Beta ou alfa devem aliviar esse problema. Decidimos presentes neste passo a passo usando o Xcode até que as correções necessárias são implementadas no canal estável.

O controle de exibição de pilha (`UIStackView`) aproveita o poder do Layout automático e Classes de tamanho para gerenciar uma pilha de sub-visualizações, horizontal ou verticalmente, que responde dinamicamente para o tamanho da tela e a orientação do dispositivo iOS.

O layout de todos os sub-visualizações anexado a um modo de exibição de pilha são gerenciadas por ela com base nas propriedades de desenvolvedor definido como eixo, distribuição, alinhamento e espaçamento:

[![](uistackview-images/stacked01.png "Diagrama de layout do modo de exibição de pilha")](uistackview-images/stacked01.png#lightbox)

Ao usar um `UIStackView` em um aplicativo xamarin, o desenvolvedor pode definir sub-visualizações tanto dentro de um Storyboard no iOS Designer ou adicionando e removendo sub-visualizações em código c#.

Este documento consiste em duas partes: um início rápido para ajudá-lo a implementar exibir a pilha primeiro e, em seguida, alguns mais detalhes técnicos sobre como ele funciona.

> [!VIDEO https://youtube.com/embed/p3po6507Ip8]

**UIStackView, pelo [University Xamarin](https://university.xamarin.com/)**

## <a name="uistackview-quickstart"></a>Guia de início rápido UIStackView

Como uma rápida introdução ao `UIStackView` controle, vamos criar uma interface simples que permite que o usuário insira uma classificação de 1 a 5. Usaremos dois modos de exibição de pilha: uma para organizar a interface verticalmente o dispositivo tela e outra para organizar os ícones de classificação de 1 a 5 horizontalmente pela tela.

### <a name="define-the-ui"></a>Definir a interface do usuário

Iniciar um novo projeto de xamarin e editar o **Main.storyboard** arquivo no construtor de Interface do Xcode. Primeiro, arraste um único **exibição pilha Vertical** no **View Controller**:

[![](uistackview-images/quick01.png "Arraste uma única exibição de pilha Vertical no controlador de exibição")](uistackview-images/quick01.png#lightbox)

No **Inspetor de atributo**, defina as seguintes opções:

[![](uistackview-images/quick02.png "Definir as opções de exibição de pilha")](uistackview-images/quick02.png#lightbox)

Sendo que:

- **Eixo** – determina se o modo de exibição de pilha organiza as subexibições ou **horizontalmente** ou **verticalmente**.
- **Alinhamento** – controla como as subexibições são alinhadas dentro da exibição da pilha.
- **Distribuição** – controla como as subexibições são dimensionadas dentro da exibição da pilha.
- **Espaçamento** – controla o espaço mínimo entre cada subexibição na exibição da pilha.
- **Linha de base relativo** – se marcada, o espaçamento vertical de cada modo de exibição secundário será derivado da sua linha de base.
- **Layout margens relativo** – coloca as sub-visualizações em relação as margens de layout padrão.

Ao trabalhar com uma exibição de pilha, você pode pensar a **alinhamento** como o **X** e **Y** local do modo de exibição secundário e o **distribuição** como o **Altura** e **largura**.

> [!IMPORTANT]
> `UIStackView` foi projetado como um modo de exibição do contêiner não renderização e como tal, não é desenhado à tela como outros subclasses de `UIView`. Para definir propriedades, como `BackgroundColor` ou substituindo `DrawRect` não terá nenhum efeito visual.

Continue para layout de interface do aplicativo adicionando um rótulo, ImageView, dois botões e um modo de exibição de pilha Horizontal para que se assemelha ao seguinte:

[![](uistackview-images/quick03.png "Dispor a interface de usuário do modo de exibição de pilha")](uistackview-images/quick03.png#lightbox)

Configure o modo de exibição de pilha Horizontal com as seguintes opções:

[![](uistackview-images/quick04.png "Configurar as opções de exibição de pilha Horizontal")](uistackview-images/quick04.png#lightbox)

Porque não queremos que o ícone que representa cada "ponto" na classificação de alongado quando ele é adicionado à exibição de pilha Horizontal, definimos o **alinhamento** para **Center** e  **Distribuição** para **preencher igualmente**.

Por fim, conectar o seguinte **tomadas** e **ações**:

[![](uistackview-images/quick05.png "As saídas de modo de exibição de pilha e ações")](uistackview-images/quick05.png#lightbox)

### <a name="populate-a-uistackview-from-code"></a>Preencher um UIStackView do código

Retorne ao Visual Studio para Mac e editar o **ViewController.cs** de arquivo e adicione o seguinte código:

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

### <a name="testing-the-ui"></a>Teste de interface do usuário

Todos os necessário elementos de interface do usuário e código em vigor, você pode executar e testar a interface. Quando a interface do usuário é exibido, todos os elementos na exibição da pilha Vertical serão espaçados igualmente de cima para baixo.

Quando o usuário toca o **aumentar classificação** botão, outra "estrela" é adicionada à tela (até um máximo de 5):

[![](uistackview-images/intro01.png "Execute o aplicativo de exemplo")](uistackview-images/intro01.png#lightbox)

"Estrelas" serão automaticamente centralizadas e distribuídas igualmente na exibição da pilha Horizontal. Quando o usuário toca o **diminuir classificação** botão, uma "estrela" será removido (até que nenhum estão à esquerda).

## <a name="stack-view-details"></a>Exibir detalhes da pilha

Agora que temos uma ideia geral do que o `UIStackView` o controle é e como ele funciona, vamos dar uma análise mais profunda sobre alguns dos seus recursos e detalhes.

### <a name="auto-layout-and-size-classes"></a>Classes de tamanho e o Layout automático

Como vimos acima, quando um modo de exibição secundário é adicionado a uma exibição da pilha de seu layout é totalmente controlado pela pilha de exibição usando Classes de tamanho e Layout automático para posicionar e dimensionar os modos de exibição organizados.

Será o modo de exibição de pilha _pin_ a primeira e o última subexibição em sua coleção para o **superior** e **inferior** bordas para modos de exibição de pilha Vertical ou **deixado**e **direita** bordas para modos de exibição de pilha Horizontal. Se você definir o `LayoutMarginsRelativeArrangement` propriedade `true`, em seguida, o modo de exibição fixa sub-visualizações para as margens relevantes em vez da borda.

A exibição de pilha usa o modo de exibição de secundário `IntrinsicContentSize` propriedade ao calcular o tamanho de sub-visualizações junto a definido `Axis` (exceto para o `FillEqually Distribution`). O `FillEqually Distribution` redimensiona sub-visualizações todos para que eles têm o mesmo tamanho, preenchimento, portanto, o modo de exibição de pilha ao longo de `Axis`.

Com exceção do `Fill Alignment`, a exibição de pilha usa o modo de exibição de secundário `IntrinsicContentSize` propriedade para calcular o tamanho da exibição do perpendicular à determinado `Axis`. Para o `Fill Alignment`, sub-visualizações todos são dimensionadas para que preenchem a exibição de pilha perpendicular à determinado `Axis`.

### <a name="positioning-and-sizing-the-stack-view"></a>Posicionar e dimensionar o modo de exibição de pilha

Enquanto o modo de exibição de pilha tem total controle sobre o layout de qualquer modo de exibição secundário (com base em propriedades como `Axis` e `Distribution`), você ainda precisa posicionar a exibição de pilha (`UIStackView`) em sua exibição pai usando Classes de tamanho e o Layout automático.

Em geral, isso significa que pelo menos duas bordas de exibição de pilha para expandir e contrair, definindo, portanto, sua posição de fixação. Sem qualquer restrição adicional, o modo de exibição de pilha serão automaticamente redimensionado para atender a todas as suas sub-visualizações da seguinte maneira:

 - O tamanho ao longo de seu `Axis` será a soma de todos os tamanhos de subexibição mais qualquer espaço que foi definido entre cada modo de exibição secundário.
 - Se o `LayoutMarginsRelativeArrangement` é de propriedade `true`, o tamanho da pilha exibições também incluirá o espaço para as margens.
 - O tamanho perpendicular ao `Axis` será definida como ela é maior na coleção.

Além disso, você pode especificar restrições para o modo de exibição de pilha **altura** e **largura**. Nesse caso, as sub-visualizações serão dispostas (tamanho) para preencher o espaço especificado pela exibição de pilha, conforme determinado pelo `Distribution` e `Alignment` propriedades.

Se o `BaselineRelativeArrangement` é de propriedade `true`, as subexibições serão dispostas com base na linha de base do subexibição primeiro ou último, em vez de usar o **superior**, **inferior** ou **Center* -  **Y** posição. Esses são calculados sobre o conteúdo do modo de exibição de pilha da seguinte maneira:

 - Um modo de exibição de pilha Vertical retornará o primeiro modo de exibição secundário para a primeira linha de base e o último para o último. Se qualquer uma desses sub-visualizações próprios modos de exibição de pilha, sua primeira ou última linha de base será usada.
 - Um modo de exibição de pilha Horizontal usará seu subexibição mais alta para ambas as a primeira e a última linha de base. Se o modo de exibição mais alto também é uma exibição de pilha, ele usará sua subexibição mais alta como a linha de base.

> [!IMPORTANT]
> Alinhamento de linha de base não funciona em tamanhos de subexibição ampliada ou compactado como a linha de base será calculada para a posição errada. Para o alinhamento da linha de base, certifique-se de que o modo de exibição de secundário **altura** coincide com a exibição de conteúdo intrínseco **altura**.

### <a name="common-stack-view-uses"></a>Usos comuns de modo de exibição de pilha

Há vários tipos de layout que funcionam bem com controles de exibição de pilha. De acordo com a Apple, aqui estão alguns dos usos mais comuns:

- **Definir o tamanho ao longo do eixo** – fixando ambas as bordas ao longo do modo de exibição de pilha `Axis` e uma das bordas para definir a posição, a pilha de exibição aumentará ao longo do eixo para ajustar o espaço definido por seus sub-visualizações adjacentes.
 -  **Definir a posição do subexibição** – fixando bordas adjacentes da exibição da pilha para exibição do pai, o modo de exibição de pilha crescerá em ambas as dimensões de acordo com seu recipiente sub-visualizações.
- **Definir o tamanho e a posição da pilha** – fixando todas as quatro bordas de exibição de pilha para o modo de exibição do pai, o modo de exibição de pilha organiza as subexibições com base no espaço definido dentro da exibição da pilha.
 -  **Definir o tamanho Perpendicular eixo** – fixando perpendicular ambas as extremidades para o modo de exibição de pilha `Axis` e uma das bordas ao longo do eixo para definir a posição, a pilha de exibição crescerá perpendicular ao eixo de acordo com o espaço definido por seu sub-visualizações.

### <a name="managing-the-appearance"></a>Gerenciando a aparência

O `UIStackView` foi projetado como um modo de exibição do contêiner não renderização e como tal, não é desenhado à tela como outros subclasses de `UIView`. Definir propriedades como `BackgroundColor` ou substituindo `DrawRect` não terá nenhum efeito visual.

Há várias propriedades que controlam como um modo de exibição de pilha organizará sua coleção de sub-visualizações:

- **Eixo** – determina se o modo de exibição de pilha organiza as subexibições ou **horizontalmente** ou **verticalmente**.
- **Alinhamento** – controla como as subexibições são alinhadas dentro da exibição da pilha.
- **Distribuição** – controla como as subexibições são dimensionadas dentro da exibição da pilha.
- **Espaçamento** – controla o espaço mínimo entre cada subexibição na exibição da pilha.
- **Linha de base relativo** – se `true`, o espaçamento vertical de cada modo de exibição secundário será derivado da sua linha de base.
- **Layout margens relativo** – coloca as sub-visualizações em relação as margens de layout padrão.

Normalmente, você usará uma exibição de pilha para organizar um número pequeno de sub-visualizações. Interfaces de usuário mais complexas podem ser criadas de aninhamento de um ou mais modos de exibição de pilha em si (como foi o [UIStackView Quickstart](#UIStackView-Quickstart) acima).

Você pode ajustar a aparência de interfaces do usuário mais adicionando restrições adicionais para sub-visualizações (por exemplo, para controlar a altura ou largura). No entanto, tome cuidado para não incluir restrições conflitantes aos introduzido pela exibição de pilha em si.

### <a name="maintaining-arranged-views-and-sub-views-consistency"></a>Manter organizados modos de exibição e a consistência de modos de exibição de Sub

O modo de exibição de pilha para garantir que seu `ArrangedSubviews` propriedade é sempre um subconjunto de seus `Subviews` propriedade usando as seguintes regras:

 - Se um modo de exibição secundário é adicionado a `ArrangedSubviews` coleção, ele será automaticamente adicionado ao `Subviews` coleção (a menos que ela já faz parte da coleção).
 - Se um modo de exibição secundário é removido do `Subviews` coleção (removida da exibição), ele também será removido do `ArrangedSubviews` coleção.
 - Removendo um modo de exibição secundário do `ArrangedSubviews` coleção não removê-lo a `Subviews` coleção. Portanto ele não será disposto pela exibição de pilha, mas ainda estarão visível na tela.

O `ArrangedSubviews` coleção é sempre um subconjunto do `Subview` coleção, no entanto a ordem das subexibições individuais dentro de cada coleção é separada e controlado pelo seguinte:

 - A ordem dos sub-visualizações dentro de `ArrangedSubviews` coleção determinar sua ordem de exibição dentro da pilha.
 - A ordem dos sub-visualizações dentro de `Subview` coleção determina sua ordem Z (ou das camadas) no modo de exibição para a frente.

### <a name="dynamically-changing-content"></a>Alterando dinamicamente o conteúdo

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

## <a name="summary"></a>Resumo

Este artigo abordou novo `UIStackView` controlar (para iOS 9) para gerenciar um conjunto de sub-visualizações em uma pilha organizada horizontal ou verticalmente em um aplicativo xamarin.
Ele começa com um exemplo simples de usar modos de exibição de pilha para criar uma interface de usuário e terminar com uma visão mais detalhada de modos de exibição de pilha e suas propriedades e recursos.



## <a name="related-links"></a>Links relacionados

- [Exemplos do iOS 9](https://developer.xamarin.com/samples/ios/iOS9/)
- [iOS 9 para desenvolvedores](https://developer.apple.com/ios/pre-release/)
- [O que há de novo no iOS 9.0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [Referência de UIStackView](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIStackView_Class_Reference/)
- [Introdução ao UIStackView (vídeo)](https://university.xamarin.com/lightninglectures/introducing-uistackview-on-ios9)
