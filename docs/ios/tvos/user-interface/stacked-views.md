---
title: Trabalhando com exibições empilhadas do tvOS no Xamarin
description: Este documento descreve como trabalhar com exibições empilhadas do tvOS em um aplicativo criado com o Xamarin. Ele fornece uma visão geral de alto nível das exibições empilhadas e discute o layout automático, o posicionamento e o dimensionamento de uma exibição empilhada, usos comuns, integração com storyboards e muito mais.
ms.prod: xamarin
ms.assetid: 00B07F85-F30B-4DD4-8664-A61D0A1CDB0E
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: 646a26b4c9b2d44595315a6a32b7294b18c42d7a
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68648958"
---
# <a name="working-with-tvos-stacked-views-in-xamarin"></a>Trabalhando com exibições empilhadas do tvOS no Xamarin

O controle de exibição de`UIStackView`pilha () aproveita o poder do layout automático e de classes de tamanho para gerenciar uma pilha de subexibições, horizontal ou verticalmente, que responde dinamicamente às alterações de conteúdo e ao tamanho da tela do dispositivo da Apple TV.

O layout de todas as subexibições anexadas a uma exibição de pilha é gerenciado por ela com base nas propriedades definidas pelo desenvolvedor, como eixo, distribuição, alinhamento e espaçamento:

[![](stacked-views-images/stacked01.png "Diagrama de layout de subexibição")](stacked-views-images/stacked01.png#lightbox)

Ao usar um `UIStackView` em um aplicativo Xamarin. tvOS, o desenvolvedor pode definir as subexibições dentro de um storyboard no designer do Ios ou adicionando e removendo subexibições no C# código.

## <a name="about-stacked-view-controls"></a>Sobre controles de exibição empilhados 

O `UIStackView` é projetado como uma exibição de contêiner que não é de renderização e, como tal, não é desenhado para a tela como outras subclasses de `UIView`. Definir propriedades como `BackgroundColor` ou substituir `DrawRect` não terá nenhum efeito visual.

Há várias propriedades que controlam como uma exibição de pilha irá organizar sua coleção de subexibições:

- **Axis** – determina se a exibição de pilha organiza as subexibições tanto **horizontal** quanto **verticalmente**.
- **Alinhamento** – controla como as subexibições são alinhadas dentro da exibição de pilha.
- **Distribuição** – controla como as subexibições são dimensionadas na exibição de pilha.
- **Espaçamento** – controla o espaço mínimo entre cada subexibição na exibição de pilha.
- **Relativa à linha** de `true`base – se, o espaçamento vertical de cada subexibição será derivado da linha de base.
- **Margens de layout relativas** – coloca as subexibições em relação às margens de layout padrão.

Normalmente, você usará uma exibição de pilha para organizar um pequeno número de subexibições. Interfaces de usuário mais complexas podem ser criadas aninhando-se uma ou mais exibições de pilha umas nas outras.

Você pode ajustar ainda mais a aparência das UIs adicionando restrições adicionais às subexibições (por exemplo, para controlar a altura ou largura). No entanto, deve-se ter cuidado para não incluir restrições conflitantes para as apresentadas pelo modo de exibição de pilha em si.

<a name="Auto-Layout-and-Size-Classes" />

## <a name="auto-layout-and-size-classes"></a>Classes de layout e tamanho automático

Quando uma subexibição é adicionada a uma exibição de pilha, seu layout é totalmente controlado por essa exibição de pilha usando classes de tamanho e layout automático para posicionar e dimensionar as exibições organizadas.

A exibição de pilha _fixará_ a primeira e a última subexibição em sua coleção nas bordas **superior** e **inferior** para exibições de pilha vertical ou bordas **esquerda** e **direita** para exibições de pilha horizontal. Se você definir a `LayoutMarginsRelativeArrangement` Propriedade como `true`, a exibição fixará as subexibições às margens relevantes, em vez da borda.

A exibição de pilha usa a propriedade da `IntrinsicContentSize` subexibição ao calcular o tamanho das subexibições ao `Axis` longo do definido ( `FillEqually Distribution`exceto para o). O `FillEqually Distribution` redimensiona todas as subexibições para que elas tenham o mesmo tamanho, preenchendo o modo de exibição `Axis`de pilha ao longo do.

Com exceção do `Fill Alignment`, a exibição de pilha usa a propriedade da `IntrinsicContentSize` subexibição para calcular o tamanho da exibição perpendicular ao especificado `Axis`. Para o `Fill Alignment`, todas as subexibições são dimensionadas para preencher a exibição de pilha perpendicular ao determinado `Axis`.

<a name="Positioning-and-Sizing-the-Stack-View" />

## <a name="positioning-and-sizing-the-stack-view"></a>Posicionando e dimensionando a exibição de pilha

Embora o modo de exibição de pilha tenha controle total sobre o layout de qualquer subexibição (com base `Axis` em `Distribution`propriedades como e), você ainda precisa posicionar o`UIStackView`modo de exibição de pilha () dentro de sua exibição pai usando o layout automático e as classes de tamanho.

Em geral, isso significa fixar pelo menos duas bordas da exibição de pilha para expandir e contratar, definindo, portanto, sua posição. Sem restrições adicionais, o modo de exibição de pilha será redimensionado automaticamente para se ajustar a todas as suas subexibições da seguinte maneira:

* O tamanho ao longo `Axis` de sua será a soma de todos os tamanhos de subexibição, além de qualquer espaço que tenha sido definido entre cada subexibição.
* Se a `LayoutMarginsRelativeArrangement` propriedade for `true`, o tamanho das exibições da pilha também incluirá espaço para as margens.
* O tamanho perpendicular ao `Axis` será definido como a maior subexibição na coleção.

Além disso, você pode especificar restrições para a **altura** e a **largura**da exibição de pilha. Nesse caso, as subexibições serão dispostas (dimensionadas) para preencher o espaço especificado pela exibição de pilha conforme determinado pelas `Distribution` Propriedades e. `Alignment`

Se a `BaselineRelativeArrangement` propriedade for `true`, as subexibições serão dispostas com base na primeira ou na última linha de base da subexibição, em vez de usar a posição **superior**, **inferior** ou **central*- **Y** . Eles são calculados no conteúdo da exibição de pilha da seguinte maneira:

* Uma exibição de pilha vertical retornará a primeira subexibição para a primeira linha de base e a última para a última. Se qualquer uma dessas subexibições estiver em modo de exibição de pilha, a primeira ou a última linha de base será usada.
* Uma exibição de pilha horizontal usará sua subexibição mais alta para a primeira e a última linha de base. Se a exibição mais alta também for uma exibição de pilha, ela usará a subexibição mais alta que a linha de base.

> [!IMPORTANT]
> O alinhamento de linha de base não funciona em tamanhos de subexibições ampliados ou compactados, pois a linha de base será calculada para a posição incorreta. Para alinhamento de linha de base, verifique se a **altura** da subexibição corresponde à **altura**do modo de exibição de conteúdo intrínseco.




<a name="Common-Stack-View-Uses" />

## <a name="common-stack-view-uses"></a>Uso de exibição de pilha comum

Há vários tipos de layout que funcionam bem com controles de exibição de pilha. De acordo com a Apple, aqui estão alguns dos usos mais comuns:

- **Defina o tamanho ao longo do eixo** – fixando ambas as bordas ao longo da `Axis` exibição da pilha e uma das bordas adjacentes para definir a posição, a exibição da pilha aumentará ao longo do eixo para se ajustar ao espaço definido por suas subexibições.
*  **Definir a posição da subexibição** – ao fixar as bordas adjacentes da exibição de pilha à sua exibição pai, a exibição de pilha aumentará em ambas as dimensões para ajustá-la a ela contendo subexibições.
- **Definir o tamanho e a posição da pilha** – fixando todas as quatro bordas da exibição de pilha para a exibição pai, a exibição de pilha organiza as subexibições com base no espaço definido na exibição de pilha.
*  **Defina o tamanho perpendicular ao eixo** – fixando ambas as bordas perpendiculares à exibição da `Axis` pilha e uma das bordas ao longo do eixo para definir a posição, a exibição da pilha aumentará perpendicular ao eixo para se ajustar ao espaço definido por suas subexibições.

<a name="Stack-Views-and-Storyboards" />

## <a name="stack-views-and-storyboards"></a>Exibições de pilha e storyboards

A maneira mais fácil de trabalhar com exibições de pilha em um aplicativo Xamarin. tvOS é adicioná-las à interface do usuário do aplicativo usando o designer do iOS.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

1. Na **painel de soluções**, clique duas vezes no arquivo `Main.storyboard` e abra-o para edição.
1. Projete o layout de seus elementos individuais que você vai adicionar à exibição de pilha: 

    [![](stacked-views-images/layout01.png "Exemplo de layout de elemento")](stacked-views-images/layout01.png#lightbox)
1. Adicione todas as restrições necessárias aos elementos para garantir que elas sejam dimensionadas corretamente. Esta etapa é importante quando o elemento é adicionado à exibição de pilha.
1. Faça o número necessário de cópias (quatro, neste caso): 

    [![](stacked-views-images/layout02.png "O número necessário de cópias")](stacked-views-images/layout02.png#lightbox)
1. Arraste uma **exibição de pilha** da **caixa de ferramentas** e solte-a na exibição: 

    [![](stacked-views-images/layout03.png "Uma exibição de pilha")](stacked-views-images/layout03.png#lightbox)
1. Selecione o modo de exibição de pilha, na **Guia Widget** do **painel de propriedades** selecione **preencher** para o **alinhamento**, **preencha igualmente** para a **distribuição** e `25` insira para o **espaçamento**: 

    [![](stacked-views-images/layout04.png "A guia do widget")](stacked-views-images/layout04.png#lightbox)
1. Posicione o modo de exibição de pilha na tela onde você deseja e adicione restrições para mantê-lo no local necessário.
1. Selecione os elementos individuais e arraste-os para a exibição de pilha: 

    [![](stacked-views-images/layout05.png "Os elementos individuais na exibição de pilha")](stacked-views-images/layout05.png#lightbox)
1. O layout será ajustado e os elementos serão organizados no modo de exibição de pilha com base nos atributos definidos acima.
1. Atribua **nomes** na **Guia Widget** do Gerenciador de **Propriedades** para trabalhar com os controles de interface do C# usuário no código.
1. Salve as alterações.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Na **Gerenciador de soluções**, clique duas vezes no arquivo `Main.storyboard` e abra-o para edição.
1. Projete o layout de seus elementos individuais que você vai adicionar à exibição de pilha: 

    [![](stacked-views-images/layout01.png "Exemplo de layout de elemento")](stacked-views-images/layout01.png#lightbox)
1. Adicione todas as restrições necessárias aos elementos para garantir que elas sejam dimensionadas corretamente. Esta etapa é importante quando o elemento é adicionado à exibição de pilha.
1. Faça o número necessário de cópias (quatro, neste caso): 

    [![](stacked-views-images/layout02.png "O número necessário de cópias")](stacked-views-images/layout02.png#lightbox)
1. Arraste uma **exibição de pilha** da **caixa de ferramentas** e solte-a na exibição: 

    [![](stacked-views-images/layout03-vs.png "Uma exibição de pilha")](stacked-views-images/layout03-vs.png#lightbox)
1. Selecione o modo de exibição de pilha, na **Guia Widget** do **Gerenciador de propriedades** , selecione **preenchimento** para o **alinhamento**, **preencha igualmente** para a `25` **distribuição** e insira para o **espaçamento**: 

    [![](stacked-views-images/layout04-vs.png "A guia do widget")](stacked-views-images/layout04-vs.png#lightbox)
1. Posicione o modo de exibição de pilha na tela onde você deseja e adicione restrições para mantê-lo no local necessário.
1. Selecione os elementos individuais e arraste-os para a exibição de pilha: 

    [![](stacked-views-images/layout05-vs.png "Os elementos individuais na exibição de pilha")](stacked-views-images/layout05-vs.png#lightbox)
1. O layout será ajustado e os elementos serão organizados no modo de exibição de pilha com base nos atributos definidos acima.
1. Atribua **nomes** na **Guia Widget** do Gerenciador de **Propriedades** para trabalhar com os controles de interface do C# usuário no código.
1. Salve as alterações.

-----

> [!IMPORTANT]
> Embora seja possível atribuir ações como `TouchUpInside` a um elemento de interface do usuário (como a `UIButton`) no designer do Ios ao criar um manipulador de eventos, ele nunca será chamado porque a Apple TV não tem uma tela sensível ao toque ou dá suporte a eventos de toque. Você sempre deve usar o padrão `Action Type` ao criar ações para elementos da interface do usuário do tvOS.

Para obter mais informações sobre como trabalhar com storyboards, consulte nossa [Guia de início rápido Olá, tvOS](~/ios/tvos/get-started/hello-tvos.md).

No caso de nosso exemplo, expõemos uma tomada e uma ação para o controle de segmento e uma tomada para cada "cartão de jogador". No código, ocultamos e mostramos o player com base no segmento atual. Por exemplo:

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

Quando o aplicativo for executado, os quatro elementos serão igualmente distribuídos em nossa exibição de pilha:

[![](stacked-views-images/layout06.png "Quando o aplicativo for executado, os quatro elementos serão igualmente distribuídos em nossa exibição de pilha")](stacked-views-images/layout06.png#lightbox)

Se o número de players for reduzido, as exibições não usadas serão ocultadas e a exibição de pilha ajustará o layout para ajustar:

[![](stacked-views-images/layout07.png "Se o número de players for reduzido, as exibições não usadas serão ocultadas e a exibição de pilha ajustará o layout para caber")](stacked-views-images/layout07.png#lightbox)

<a name="Populate-a-Stack-View-from-Code" />

### <a name="populate-a-stack-view-from-code"></a>Preencher uma exibição de pilha do código

Além de definir totalmente o conteúdo e o layout de uma exibição de pilha no designer do iOS, você pode criar e removê-lo C# dinamicamente do código.

Veja o exemplo a seguir que usa uma exibição de pilha para lidar com "estrelas" em uma revisão (1 a 5):

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

<a name="Dynamically-Changing-Content" />

## <a name="dynamically-changing-content"></a>Alteração dinâmica de conteúdo

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

<a name="Summary" />

## <a name="summary"></a>Resumo

Este artigo abordou a criação e o trabalho com a exibição empilhada dentro de um aplicativo Xamarin. tvOS.



## <a name="related-links"></a>Links relacionados

- [Amostras do tvOS](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+tvOS)
- [tvOS](https://developer.apple.com/tvos/)
- [Guias de interface humana do tvOS](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guia de programação de aplicativo para tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
