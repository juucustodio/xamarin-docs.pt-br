---
title: Exibições de pilha no Xamarin. iOS
description: Este artigo aborda o uso do novo controle UIStackView em um aplicativo Xamarin. iOS para gerenciar um conjunto de subexibições em uma pilha organizada horizontal ou verticalmente.
ms.prod: xamarin
ms.assetid: 20246E87-2A49-438A-9BD7-756A1B50A617
ms.technology: xamarin-ios
ms.custom: xamu-video
author: lobrien
ms.author: laobri
ms.date: 03/20/2017
ms.openlocfilehash: 5519f6e5b1bb0b63ab3169dd8f48f2f87a025ba5
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68642806"
---
# <a name="stack-views-in-xamarinios"></a>Exibições de pilha no Xamarin. iOS

_Este artigo aborda o uso do novo controle UIStackView em um aplicativo Xamarin. iOS para gerenciar um conjunto de subexibições em uma pilha organizada horizontal ou verticalmente._

> [!IMPORTANT]
> Observe que, embora o StackView tenha suporte no designer do iOS, você poderá encontrar bugs de usabilidade ao usar o canal estável. Alternar os canais beta ou alfa deve aliviar esse problema. Decidimos apresentar este passo a passos usando o Xcode até que as correções necessárias sejam implementadas no canal estável.

O controle de exibição de`UIStackView`pilha () aproveita o poder do layout automático e de classes de tamanho para gerenciar uma pilha de subexibições, tanto horizontal quanto verticalmente, que responde dinamicamente à orientação e ao tamanho da tela do dispositivo IOS.

O layout de todas as subexibições anexadas a uma exibição de pilha é gerenciado por ela com base nas propriedades definidas pelo desenvolvedor, como eixo, distribuição, alinhamento e espaçamento:

[![](uistackview-images/stacked01.png "Diagrama de layout de exibição de pilha")](uistackview-images/stacked01.png#lightbox)

Ao usar um `UIStackView` em um aplicativo Xamarin. Ios, o desenvolvedor pode definir as subexibições dentro de um storyboard no designer do Ios ou adicionando e removendo subexibições no C# código.

Este documento consiste em duas partes: um início rápido para ajudá-lo a implementar sua primeira exibição de pilha e, em seguida, alguns detalhes técnicos sobre como ele funciona.

> [!VIDEO https://youtube.com/embed/p3po6507Ip8]

**Vídeo do UIStackView**

## <a name="uistackview-quickstart"></a>Início rápido do UIStackView

Como uma rápida introdução ao `UIStackView` controle, vamos criar uma interface simples que permita que o usuário insira uma classificação de 1 a 5. Vamos usar duas exibições de pilha: uma para organizar a interface verticalmente na tela do dispositivo e outra para organizar os ícones de classificação 1-5 horizontalmente na tela.

### <a name="define-the-ui"></a>Definir a interface do usuário

Inicie um novo projeto Xamarin. iOS e edite o arquivo **Main. Storyboard** no interface Builder do Xcode. Primeiro, arraste uma **exibição de pilha vertical** única no **controlador de exibição**:

[![](uistackview-images/quick01.png "Arraste uma exibição de pilha vertical única no controlador de exibição")](uistackview-images/quick01.png#lightbox)

No **Inspetor de atributo**, defina as seguintes opções:

[![](uistackview-images/quick02.png "Definir as opções de exibição de pilha")](uistackview-images/quick02.png#lightbox)

Sendo que:

- **Axis** – determina se a exibição de pilha organiza as subexibições tanto **horizontal** quanto **verticalmente**.
- **Alinhamento** – controla como as subexibições são alinhadas dentro da exibição de pilha.
- **Distribuição** – controla como as subexibições são dimensionadas na exibição de pilha.
- **Espaçamento** – controla o espaço mínimo entre cada subexibição na exibição de pilha.
- **Relativa à linha de base** – se marcada, o espaçamento vertical de cada subexibição será derivado da linha de base.
- **Margens de layout relativas** – coloca as subexibições em relação às margens de layout padrão.

Ao trabalhar com uma exibição de pilha, você pode considerar o **alinhamento** como o local **X** e **Y** da subexibição e a **distribuição** como a **altura** e a **largura**.

> [!IMPORTANT]
> `UIStackView`é projetado como uma exibição de contêiner que não é de renderização e, como tal, não é desenhado para a tela como outras subclasses de `UIView`. Portanto, definir propriedades como `BackgroundColor` ou substituir `DrawRect` não terá nenhum efeito visual.

Continue a fazer o layout da interface do aplicativo adicionando um rótulo, ImageView, dois botões e uma exibição de pilha horizontal para que seja semelhante ao seguinte:

[![](uistackview-images/quick03.png "Definindo a interface do usuário da exibição de pilha")](uistackview-images/quick03.png#lightbox)

Configure a exibição de pilha horizontal com as seguintes opções:

[![](uistackview-images/quick04.png "Configurar opções de exibição de pilha horizontal")](uistackview-images/quick04.png#lightbox)

Como não queremos que o ícone que representa cada "ponto" na classificação seja alongado quando adicionado à exibição de pilha horizontal, definimos o **alinhamento** como **centralizado** e a **distribuição** para **preencher igualmente**.

Por fim, conecte as seguintes **saídas** e **ações**:

[![](uistackview-images/quick05.png "As ações e saídas da exibição de pilha")](uistackview-images/quick05.png#lightbox)

### <a name="populate-a-uistackview-from-code"></a>Popular um UIStackView a partir do código

Volte para Visual Studio para Mac e edite o arquivo **ViewController.cs** e adicione o seguinte código:

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

Vamos dar uma olhada em algumas partes desse código em detalhes. Primeiro, `if` usamos instruções para verificar se não há mais de cinco "estrelas" ou menos de zero.

Para adicionar uma nova "estrela", carregamos sua imagem e definimos seu **modo de conteúdo** para **dimensionar o ajuste de aspecto**:

```csharp
var icon = new UIImageView (new UIImage("icon.png"));
icon.ContentMode = UIViewContentMode.ScaleAspectFit;
```

Isso mantém a distorção do ícone "Star" quando ele é adicionado à exibição de pilha.

Em seguida, adicionamos o novo ícone de "estrela" à coleção de subexibições da exibição de pilha:

```csharp
RatingView.AddArrangedSubview(icon);
```

Você observará que adicionamos o `UIImageView` `UIStackView`à propriedade do `ArrangedSubviews` e não ao `SubView`. Qualquer exibição que você deseja que a exibição de pilha controle seu layout deve ser adicionada à `ArrangedSubviews` propriedade.

Para remover uma subexibição de uma exibição de pilha, primeiro obtemos a subexibição a ser removida:

```csharp
var icon = RatingView.ArrangedSubviews[RatingView.ArrangedSubviews.Length-1];
```

Em seguida, precisamos removê-lo da `ArrangedSubviews` coleção e da exibição super:

```csharp
// Remove from stack and screen
RatingView.RemoveArrangedSubview(icon);
icon.RemoveFromSuperview();
```

Remover uma subexibição apenas da coleção `ArrangedSubviews` a retira do controle da exibição de pilha, mas não a remove da tela.

### <a name="testing-the-ui"></a>Testando a interface do usuário

Com todos os elementos de interface do usuário necessários e o código em vigor, agora você pode executar e testar a interface. Quando a interface do usuário é exibida, todos os elementos na exibição de pilha vertical serão igualmente espaçados de cima para baixo.

Quando o usuário toca no botão **aumentar classificação** , outra "estrela" é adicionada à tela (até um máximo de 5):

[![](uistackview-images/intro01.png "A execução do aplicativo de exemplo")](uistackview-images/intro01.png#lightbox)

As "estrelas" serão centralizadas automaticamente e distribuídas igualmente na exibição de pilha horizontal. Quando o usuário toca no botão **diminuir classificação** , uma "estrela" é removida (até que nenhum seja deixado).

## <a name="stack-view-details"></a>Detalhes da exibição da pilha

Agora que temos uma ideia geral do que é o `UIStackView` controle e como ele funciona, vamos dar uma olhada mais detalhada em alguns dos seus recursos e detalhes.

### <a name="auto-layout-and-size-classes"></a>Classes de layout e tamanho automático

Como vimos acima, quando uma subexibição é adicionada a uma exibição de pilha, seu layout é totalmente controlado por essa exibição de pilha usando o layout automático e as classes de tamanho para posicionar e dimensionar as exibições organizadas.

A exibição de pilha _fixará_ a primeira e a última subexibição em sua coleção nas bordas **superior** e **inferior** para exibições de pilha vertical ou bordas **esquerda** e **direita** para exibições de pilha horizontal. Se você definir a `LayoutMarginsRelativeArrangement` Propriedade como `true`, a exibição fixará as subexibições às margens relevantes, em vez da borda.

A exibição de pilha usa a propriedade da `IntrinsicContentSize` subexibição ao calcular o tamanho das subexibições ao `Axis` longo do definido ( `FillEqually Distribution`exceto para o). O `FillEqually Distribution` redimensiona todas as subexibições para que elas tenham o mesmo tamanho, preenchendo o modo de exibição `Axis`de pilha ao longo do.

Com exceção do `Fill Alignment`, a exibição de pilha usa a propriedade da `IntrinsicContentSize` subexibição para calcular o tamanho da exibição perpendicular ao especificado `Axis`. Para o `Fill Alignment`, todas as subexibições são dimensionadas para preencher a exibição de pilha perpendicular ao determinado `Axis`.

### <a name="positioning-and-sizing-the-stack-view"></a>Posicionando e dimensionando a exibição de pilha

Embora o modo de exibição de pilha tenha controle total sobre o layout de qualquer subexibição (com base `Axis` em `Distribution`propriedades como e), você ainda precisa posicionar o`UIStackView`modo de exibição de pilha () dentro de sua exibição pai usando o layout automático e as classes de tamanho.

Em geral, isso significa fixar pelo menos duas bordas da exibição de pilha para expandir e contratar, definindo, portanto, sua posição. Sem restrições adicionais, o modo de exibição de pilha será redimensionado automaticamente para se ajustar a todas as suas subexibições da seguinte maneira:

- O tamanho ao longo `Axis` de sua será a soma de todos os tamanhos de subexibição, além de qualquer espaço que tenha sido definido entre cada subexibição.
- Se a `LayoutMarginsRelativeArrangement` propriedade for `true`, o tamanho das exibições da pilha também incluirá espaço para as margens.
- O tamanho perpendicular ao `Axis` será definido como a maior subexibição na coleção.

Além disso, você pode especificar restrições para a **altura** e a **largura**da exibição de pilha. Nesse caso, as subexibições serão dispostas (dimensionadas) para preencher o espaço especificado pela exibição de pilha conforme determinado pelas `Distribution` Propriedades e. `Alignment`

Se a `BaselineRelativeArrangement` propriedade for `true`, as subexibições serão dispostas com base na primeira ou na última linha de base da subexibição, em vez de usar a posição **superior**, **inferior** ou **central**- **Y** . Eles são calculados no conteúdo da exibição de pilha da seguinte maneira:

- Uma exibição de pilha vertical retornará a primeira subexibição para a primeira linha de base e a última para a última. Se qualquer uma dessas subexibições estiver em modo de exibição de pilha, a primeira ou a última linha de base será usada.
- Uma exibição de pilha horizontal usará sua subexibição mais alta para a primeira e a última linha de base. Se a exibição mais alta também for uma exibição de pilha, ela usará a subexibição mais alta que a linha de base.

> [!IMPORTANT]
> O alinhamento de linha de base não funciona em tamanhos de subexibições ampliados ou compactados, pois a linha de base será calculada para a posição incorreta. Para alinhamento de linha de base, verifique se a **altura** da subexibição corresponde à **altura**do modo de exibição de conteúdo intrínseco.

### <a name="common-stack-view-uses"></a>Uso de exibição de pilha comum

Há vários tipos de layout que funcionam bem com controles de exibição de pilha. De acordo com a Apple, aqui estão alguns dos usos mais comuns:

- **Defina o tamanho ao longo do eixo** – fixando ambas as bordas ao longo da `Axis` exibição da pilha e uma das bordas adjacentes para definir a posição, a exibição da pilha aumentará ao longo do eixo para se ajustar ao espaço definido por suas subexibições.
- **Definir a posição da subexibição** – ao fixar as bordas adjacentes da exibição de pilha à sua exibição pai, a exibição de pilha aumentará em ambas as dimensões para ajustá-la a ela contendo subexibições.
- **Definir o tamanho e a posição da pilha** – fixando todas as quatro bordas da exibição de pilha para a exibição pai, a exibição de pilha organiza as subexibições com base no espaço definido na exibição de pilha.
- **Defina o tamanho perpendicular ao eixo** – fixando ambas as bordas perpendiculares à exibição da `Axis` pilha e uma das bordas ao longo do eixo para definir a posição, a exibição da pilha aumentará perpendicular ao eixo para se ajustar ao espaço definido por suas subexibições.

### <a name="managing-the-appearance"></a>Gerenciando a aparência

O `UIStackView` é projetado como uma exibição de contêiner que não é de renderização e, como tal, não é desenhado para a tela como outras subclasses de `UIView`. Definir propriedades como `BackgroundColor` ou substituir `DrawRect` não terá nenhum efeito visual.

Há várias propriedades que controlam como uma exibição de pilha irá organizar sua coleção de subexibições:

- **Axis** – determina se a exibição de pilha organiza as subexibições tanto **horizontal** quanto **verticalmente**.
- **Alinhamento** – controla como as subexibições são alinhadas dentro da exibição de pilha.
- **Distribuição** – controla como as subexibições são dimensionadas na exibição de pilha.
- **Espaçamento** – controla o espaço mínimo entre cada subexibição na exibição de pilha.
- **Relativa à linha** de `true`base – se, o espaçamento vertical de cada subexibição será derivado da linha de base.
- **Margens de layout relativas** – coloca as subexibições em relação às margens de layout padrão.

Normalmente, você usará uma exibição de pilha para organizar um pequeno número de subexibições. Interfaces de usuário mais complexas podem ser criadas aninhando-se uma ou mais exibições de pilha umas nas outras (como fizemos no guia de [início rápido UIStackView](#uistackview-quickstart) acima).

Você pode ajustar ainda mais a aparência das UIs adicionando restrições adicionais às subexibições (por exemplo, para controlar a altura ou largura). No entanto, deve-se ter cuidado para não incluir restrições conflitantes para as apresentadas pelo modo de exibição de pilha em si.

### <a name="maintaining-arranged-views-and-sub-views-consistency"></a>Mantendo a consistência de exibições organizadas e de subexibições

A exibição de pilha garantirá que `ArrangedSubviews` sua propriedade seja sempre um subconjunto `Subviews` de sua propriedade usando as seguintes regras:

- Se uma subexibição for adicionada à `ArrangedSubviews` coleção, ela será automaticamente adicionada `Subviews` à coleção (a menos que ela já faça parte dessa coleção).
- Se uma subexibição for removida da `Subviews` coleção (removida da exibição), ela também será removida `ArrangedSubviews` da coleção.
- A remoção de uma subexibição `ArrangedSubviews` da coleção não a remove `Subviews` da coleção. Portanto, ele não será mais disposto pelo modo de exibição de pilha, mas ainda estará visível na tela.

A `ArrangedSubviews` coleção é sempre um subconjunto `Subview` da coleção, no entanto, a ordem das subexibições individuais dentro de cada coleção é separada e controlada pelo seguinte:

- A ordem das subexibições na `ArrangedSubviews` coleção determinam sua ordem de exibição na pilha.
- A ordem das subexibições na `Subview` coleção determina sua ordem Z (ou camadas) na exibição de volta ao início.

### <a name="dynamically-changing-content"></a>Alteração dinâmica de conteúdo

Uma exibição de pilha ajustará automaticamente o layout das subexibições sempre que uma subexibição for adicionada, removida ou oculta. O layout também será ajustado se qualquer propriedade da exibição de pilha for ajustada (como a sua `Axis`).

As alterações de layout podem ser animadas colocando-as em um bloco de animação, por exemplo:

```csharp
// Animate stack
UIView.Animate(0.25, ()=>{
    // Adjust stack view
    RatingView.LayoutIfNeeded();
});
```

Muitas das propriedades da exibição da pilha podem ser especificadas usando classes de tamanho dentro de um Storyboard. Essas propriedades serão automaticamente animadas é resposta a alterações de tamanho ou orientação.

## <a name="summary"></a>Resumo

Este artigo abordou o novo `UIStackView` controle (para o Ios 9) para gerenciar um conjunto de subexibições em uma pilha organizada horizontal ou verticalmente em um aplicativo Xamarin. Ios.
Ele começou com um exemplo simples de uso de exibições de pilha para criar uma interface do usuário e concluiu com uma visão mais detalhada das exibições de pilha e suas propriedades e recursos.



## <a name="related-links"></a>Links relacionados

- [Amostras do iOS 9](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+iOS9)
- [iOS 9 para desenvolvedores](https://developer.apple.com/ios/pre-release/)
- [O que há de novo no iOS 9,0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [Referência de UIStackView](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIStackView_Class_Reference/)
- [Introdução ao UIStackView (vídeo)](https://university.xamarin.com/lightninglectures/introducing-uistackview-on-ios9)
