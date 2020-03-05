---
title: Escolha um layout do Xamarin. Forms
description: As classes de layout Xamarin. Forms permitem que você organize e agrupe controles de interface do usuário em seu aplicativo.
ms.prod: xamarin
ms.assetid: 05A39752-A174-447E-A30D-3CC9EF98CB96
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/21/2018
ms.openlocfilehash: d029e679400b9523df8e03d509230849fa0c96c4
ms.sourcegitcommit: 9ae537efc106f56aeec562773004c6f708704ae9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2020
ms.locfileid: "78291480"
---
# <a name="choose-a-xamarinforms-layout"></a>Escolha um layout do Xamarin. Forms

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layout)

As classes de layout Xamarin. Forms permitem que você organize e agrupe controles de interface do usuário em seu aplicativo. Escolher uma classe de layout requer o conhecimento de como o layout posiciona seus elementos filho e como o layout dimensiona seus elementos filho. Além disso, pode ser necessário aninhar layouts para criar o layout desejado.

A imagem a seguir mostra layouts típicos que podem ser obtidos com as principais classes de layout do Xamarin. Forms:

[![As classes de layout principal no Xamarin. Forms](images/layouts.png "Classes de layout do Xamarin. Forms")](images/layouts-large.png#lightbox "Classes de layout do Xamarin. Forms")

## <a name="stacklayout"></a>StackLayout

Uma [`StackLayout`](xref:Xamarin.Forms.StackLayout) organiza elementos em uma pilha unidimensional, tanto horizontal quanto verticalmente. A propriedade [`Orientation`](xref:Xamarin.Forms.StackLayout.Orientation) especifica a direção dos elementos e a orientação padrão é [`Vertical`](xref:Xamarin.Forms.StackOrientation). `StackLayout` normalmente é usado para organizar uma subseção da interface do usuário em uma página.

O XAML a seguir mostra como criar um [`StackLayout`](xref:Xamarin.Forms.StackLayout) vertical contendo três objetos [`Label`](xref:Xamarin.Forms.Label) :

```xaml
<StackLayout Margin="20,35,20,25">
    <Label Text="The StackLayout has its Margin property set, to control the rendering position of the StackLayout." />
    <Label Text="The Padding property can be set to specify the distance between the StackLayout and its children." />
    <Label Text="The Spacing property can be set to specify the distance between views in the StackLayout." />
</StackLayout>
```

Em uma [`StackLayout`](xref:Xamarin.Forms.StackLayout), se o tamanho de um elemento não for definido explicitamente, ele se expandirá para preencher a largura disponível ou a altura se a propriedade [`Orientation`](xref:Xamarin.Forms.StackLayout.Orientation) for definida como [`Horizontal`](xref:Xamarin.Forms.StackOrientation).

Uma [`StackLayout`](xref:Xamarin.Forms.StackLayout) geralmente é usada como layout pai, que contém outros layouts filho. No entanto, um `StackLayout` não deve ser usado para reproduzir um layout de [`Grid`](xref:Xamarin.Forms.Grid) usando uma combinação de objetos `StackLayout`. O código a seguir mostra um exemplo dessa prática inadequada:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="Details.HomePage"
             Padding="0,20,0,0">
    <StackLayout>
        <StackLayout Orientation="Horizontal">
            <Label Text="Name:" />
            <Entry Placeholder="Enter your name" />
        </StackLayout>
        <StackLayout Orientation="Horizontal">
            <Label Text="Age:" />
            <Entry Placeholder="Enter your age" />
        </StackLayout>
        <StackLayout Orientation="Horizontal">
            <Label Text="Occupation:" />
            <Entry Placeholder="Enter your occupation" />
        </StackLayout>
        <StackLayout Orientation="Horizontal">
            <Label Text="Address:" />
            <Entry Placeholder="Enter your address" />
        </StackLayout>
    </StackLayout>
</ContentPage>
```

Isso é um desperdício porque cálculos de layout desnecessário são executados. Em vez disso, o layout desejado pode ser melhor obtido com o uso de um [`Grid`](xref:Xamarin.Forms.Grid).

> [!TIP]
> Ao usar um [`StackLayout`](xref:Xamarin.Forms.StackLayout), verifique se apenas um elemento filho está definido como [`LayoutOptions.Expands`](xref:Xamarin.Forms.LayoutOptions.Expands). Essa propriedade garante que o filho especificado ocupe o maior espaço que o `StackLayout` pode dar a ele e é um desperdício executar esses cálculos mais de uma vez.

Para obter mais informações, consulte [Xamarin. Forms StackLayout](stack-layout.md).

## <a name="grid"></a>Grade

Uma [`Grid`](xref:Xamarin.Forms.Grid) é usada para exibir elementos em linhas e colunas, que podem ter tamanhos proporcionais ou absolutos. As linhas e colunas de uma grade são especificadas com as propriedades [`RowDefinitions`](xref:Xamarin.Forms.Grid.RowDefinitions) e [`ColumnDefinitions`](xref:Xamarin.Forms.Grid.ColumnDefinitions) .

Para posicionar elementos em células [`Grid`](xref:Xamarin.Forms.Grid) específicas, use as propriedades [`Grid.Column`](xref:Xamarin.Forms.Grid.ColumnProperty) e [`Grid.Row`](xref:Xamarin.Forms.Grid.RowProperty) anexadas. Para fazer com que os elementos se estendam por várias linhas e colunas, use as propriedades anexadas [`Grid.RowSpan`](xref:Xamarin.Forms.Grid.RowSpanProperty) e [`Grid.ColumnSpan`](xref:Xamarin.Forms.Grid.ColumnSpanProperty) .

> [!NOTE]
> Um layout de [`Grid`](xref:Xamarin.Forms.Grid) não deve ser confundido com tabelas e não tem o objetivo de apresentar dados tabulares. Ao contrário das tabelas HTML, um `Grid` destina-se a dispor conteúdo. Para exibir dados tabulares, considere usar [ListView](~/xamarin-forms/user-interface/listview/index.md), [CollectionView](~/xamarin-forms/user-interface/collectionview/index.md)ou [Tableview](~/xamarin-forms/user-interface/tableview.md).

O XAML a seguir mostra como criar um [`Grid`](xref:Xamarin.Forms.Grid) com duas linhas e duas colunas:

```xaml
<Grid>
    <Grid.RowDefinitions>
        <RowDefinition Height="50" />
        <RowDefinition Height="50" />
    </Grid.RowDefinitions>
    <Grid.ColumnDefinitions>
        <ColumnDefinition Width="Auto" />
        <ColumnDefinition />
    </Grid.ColumnDefinitions>    
    <Label Text="Column 0, Row 0"
           WidthRequest="200" />
    <Label Grid.Column="1"
           Text="Column 1, Row 0" />
    <Label Grid.Row="1"
           Text="Column 0, Row 1" />
    <Label Grid.Column="1"
           Grid.Row="1"
           Text="Column 1, Row 1" />
</Grid>
```

Neste exemplo, o dimensionamento funciona da seguinte maneira:

- Cada linha tem uma altura explícita de unidades independentes de dispositivo 50.
- A largura da primeira coluna é definida como [`Auto`](xref:Xamarin.Forms.GridLength.Auto)e, portanto, é tão larga quanto a necessária para seus filhos. Nesse caso, são 200 unidades independentes de dispositivo de largura para acomodar a largura da primeira [`Label`](xref:Xamarin.Forms.Label).

O espaço pode ser distribuído em uma coluna ou linha usando o dimensionamento automático, o que permite que colunas e linhas se ajustem ao seu conteúdo. Isso é feito definindo a altura de um [`RowDefinition`](xref:Xamarin.Forms.RowDefinition)ou a largura de um [`ColumnDefinition`](xref:Xamarin.Forms.ColumnDefinition), para [`Auto`](xref:Xamarin.Forms.GridLength.Auto). O dimensionamento proporcional também pode ser usado para distribuir o espaço disponível entre as linhas e colunas da grade por proporções ponderadas. Isso é feito definindo a altura de um `RowDefinition`ou a largura de um `ColumnDefinition`, com um valor que usa o operador de `*`.

> [!CAUTION]
> Tente garantir que o menor número possível de linhas e colunas seja definido como [`Auto`](xref:Xamarin.Forms.GridLength.Auto) tamanho. Cada linha ou coluna dimensionada automaticamente fará o mecanismo de layout realizar cálculos de layout adicionais. Em vez disso, use linhas e colunas de tamanho fixo, se possível. Como alternativa, defina linhas e colunas para ocupar uma quantidade proporcional de espaço com o valor de enumeração [`GridUnitType.Star`](xref:Xamarin.Forms.GridUnitType.Star) .

Para obter mais informações, consulte a [grade Xamarin. Forms](grid.md).

## <a name="flexlayout"></a>FlexLayout

Uma [`FlexLayout`](xref:Xamarin.Forms.FlexLayout) é semelhante a uma [`StackLayout`](xref:Xamarin.Forms.StackLayout) , pois ela exibe elementos filho horizontal ou verticalmente em uma pilha. No entanto, um `FlexLayout` também pode encapsular seus filhos se houver muitos para caber em uma única linha ou coluna, além de permitir um controle mais granular do tamanho, da orientação e do alinhamento de seus elementos filho.

O XAML a seguir mostra como criar um [`FlexLayout`](xref:Xamarin.Forms.FlexLayout) que exibe suas exibições em uma única coluna:

```xaml
<FlexLayout Direction="Column"
            AlignItems="Center"
            JustifyContent="SpaceEvenly">
    <Label Text="FlexLayout in Action" />
    <Button Text="Button" />
    <Label Text="Another Label" />
</FlexLayout>
```

Neste exemplo, o layout funciona da seguinte maneira:

- A propriedade [`Direction`](xref:Xamarin.Forms.FlexLayout.Direction) é definida como `Column`, o que faz com que os filhos da `FlexLayout` sejam organizados em uma única coluna de itens.
- A propriedade [`AlignItems`](xref:Xamarin.Forms.FlexLayout.AlignItems) é definida como `Center`, o que faz com que cada item seja centralizado horizontalmente.
- A propriedade [`JustifyContent`](xref:Xamarin.Forms.FlexLayout.JustifyContent) é definida como `SpaceEvenly`, que aloca todos os espaços verticais restantes igualmente entre todos os itens e acima do primeiro item e abaixo do último item.

Para obter mais informações, consulte [Xamarin. Forms FlexLayout](flex-layout.md).

## <a name="relativelayout"></a>RelativeLayout

Uma [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) é usada para posicionar e dimensionar elementos em relação às propriedades dos elementos de layout ou irmãos. Por padrão, um elemento é posicionado no canto superior esquerdo do layout. Um `RelativeLayout` pode ser usado para criar UIs que são dimensionadas proporcionalmente entre os tamanhos de dispositivo.

Dentro de um [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout), posições e tamanhos são especificados como restrições. As restrições têm propriedades [`Factor`](xref:Xamarin.Forms.ConstraintExpression.Factor) e [`Constant`](xref:Xamarin.Forms.ConstraintExpression.Constant) , que podem ser usadas para definir posições e tamanhos como múltiplos (ou frações) de propriedades de outros objetos, além de uma constante. Além disso, as constantes podem ser negativas.

> [!NOTE]
> Um [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) dá suporte ao posicionamento de elementos fora de seus próprios limites.

O XAML a seguir mostra como organizar elementos em uma [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout):

```xaml
<RelativeLayout>
    <BoxView Color="Blue"
             HeightRequest="50"
             WidthRequest="50"
             RelativeLayout.XConstraint="{ConstraintExpression Type=RelativeToParent, Property=Width, Factor=0}"
             RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToParent, Property=Height, Factor=0}" />
    <BoxView Color="Red"
             HeightRequest="50"
             WidthRequest="50"
             RelativeLayout.XConstraint="{ConstraintExpression Type=RelativeToParent, Property=Width, Factor=.85}"
             RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToParent, Property=Height, Factor=0}" />
    <BoxView x:Name="pole"
             Color="Gray"
             WidthRequest="15"
             RelativeLayout.HeightConstraint="{ConstraintExpression Type=RelativeToParent, Property=Height, Factor=.75}"
             RelativeLayout.XConstraint="{ConstraintExpression Type=RelativeToParent, Property=Width, Factor=.45}"
             RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToParent, Property=Height, Factor=.25}" />
    <BoxView Color="Green"
             RelativeLayout.HeightConstraint="{ConstraintExpression Type=RelativeToParent, Property=Height, Factor=.10, Constant=10}"
             RelativeLayout.WidthConstraint="{ConstraintExpression Type=RelativeToParent, Property=Width, Factor=.2, Constant=20}"
             RelativeLayout.XConstraint="{ConstraintExpression Type=RelativeToView, ElementName=pole, Property=X, Constant=15}"
             RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToView, ElementName=pole, Property=Y, Constant=0}" />
</RelativeLayout>
```

Neste exemplo, o layout funciona da seguinte maneira:

- O [`BoxView`](xref:Xamarin.Forms.BoxView) azul recebe um tamanho explícito de unidades independentes do dispositivo 50x50. Ele é colocado no canto superior esquerdo do layout, que é a posição padrão.
- O [`BoxView`](xref:Xamarin.Forms.BoxView) vermelho recebe um tamanho explícito de unidades independentes do dispositivo 50x50. Ele é colocado no canto superior direito do layout.
- O [`BoxView`](xref:Xamarin.Forms.BoxView) cinza recebe uma largura explícita de 15 unidades independentes de dispositivo e a altura é definida como 75% da altura de seu pai.
- O [`BoxView`](xref:Xamarin.Forms.BoxView) verde não recebe um tamanho explícito. Sua posição é definida com relação ao `BoxView` nomeado `pole`.

> [!WARNING]
> Evite usar um `RelativeLayout` sempre que possível. Isso resultará em a CPU precisar realizar significativamente mais trabalho.

Para obter mais informações, consulte [Xamarin. Forms RelativeLayout](relative-layout.md).

## <a name="absolutelayout"></a>AbsoluteLayout

Um [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout) é usado para posicionar e dimensionar elementos usando valores explícitos ou valores relativos ao tamanho do layout. A posição é especificada pelo canto superior esquerdo do filho em relação ao canto superior esquerdo da `AbsoluteLayout`.

Um [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout) deve ser considerado como um layout de finalidade especial para ser usado somente quando você pode impor um tamanho em filhos ou quando o tamanho do elemento não afeta o posicionamento de outros filhos. Um uso padrão desse layout é criar uma sobreposição, que aborda a página com outros controles, talvez para proteger o usuário de interagir com os controles normais na página.

> [!IMPORTANT]
> As propriedades `HorizontalOptions` e `VerticalOptions` não têm efeito sobre os filhos de um `AbsoluteLayout`.

Dentro de um [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout), a propriedade [`AbsoluteLayout.LayoutBounds`](xref:Xamarin.Forms.AbsoluteLayout.LayoutBoundsProperty) anexada é usada para especificar a posição horizontal, a posição vertical, a largura e a altura de um elemento. Além disso, a propriedade [`AbsoluteLayout.LayoutFlags`](xref:Xamarin.Forms.AbsoluteLayout.LayoutFlagsProperty) anexada Especifica como os limites de layout serão interpretados.

O XAML a seguir mostra como organizar elementos em um [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout):

```xaml
<AbsoluteLayout Margin="40">
    <BoxView Color="Red"
             AbsoluteLayout.LayoutFlags="PositionProportional"
             AbsoluteLayout.LayoutBounds="0.5, 0, 100, 100"
             Rotation="30" />
    <BoxView Color="Green"
             AbsoluteLayout.LayoutFlags="PositionProportional"
             AbsoluteLayout.LayoutBounds="0.5, 0, 100, 100"
             Rotation="60" />
    <BoxView Color="Blue"
             AbsoluteLayout.LayoutFlags="PositionProportional"
             AbsoluteLayout.LayoutBounds="0.5, 0, 100, 100" />
</AbsoluteLayout>
```

Neste exemplo, o layout funciona da seguinte maneira:

- Cada [`BoxView`](xref:Xamarin.Forms.BoxView) recebe um tamanho explícito de 100x100 e é exibido na mesma posição, horizontalmente centralizada.
- A [`BoxView`](xref:Xamarin.Forms.BoxView) vermelha é girada em 30 graus e a `BoxView` verde é girada em 60 graus.
- Em cada [`BoxView`](xref:Xamarin.Forms.BoxView), a propriedade anexada [`AbsoluteLayout.LayoutFlags`](xref:Xamarin.Forms.AbsoluteLayout.LayoutFlagsProperty) é definida como `PositionProportional`, indicando que a posição é proporcional ao espaço restante depois que a largura e a altura são contabilizadas.

> [!CAUTION]
> Evite usar a propriedade [`AbsoluteLayout.AutoSize`](xref:Xamarin.Forms.AbsoluteLayout.AutoSize) sempre que possível, pois isso fará com que o mecanismo de layout execute cálculos de layout adicionais.

Para obter mais informações, consulte [Xamarin. Forms AbsoluteLayout](absolute-layout.md).

## <a name="input-transparency"></a>Transparência de entrada

Cada elemento visual tem uma propriedade [`InputTransparent`](xref:Xamarin.Forms.VisualElement.InputTransparent) que é usada para definir se o elemento recebe entrada. Seu valor padrão é `false`, garantindo que o elemento receba entrada.

Quando essa propriedade é definida em uma classe de layout, seu valor é transferido para elementos filho. Portanto, definir a propriedade [`InputTransparent`](xref:Xamarin.Forms.VisualElement.InputTransparent) como `true` em uma classe de layout fará com que todos os elementos dentro do layout não recebam entrada.

## <a name="layout-performance"></a>Desempenho do layout

Para obter o melhor desempenho de layout possível, siga as diretrizes em [otimizar o desempenho do layout](~/xamarin-forms/deploy-test/performance.md#optimize-layout-performance).

Além disso, o desempenho de renderização de página também pode ser melhorado usando a compactação de layout, que remove os layouts especificados da árvore visual. Para obter mais informações, consulte [compactação de layout](layout-compression.md).

## <a name="related-links"></a>Links relacionados

- [Layout (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layout)
- [Layouts do Xamarin. Forms (vídeo)](https://youtu.be/4HlLjTZQzjM)
- [StackLayout Xamarin. Forms](stack-layout.md)
- [Grade do Xamarin. Forms](grid.md)
- [FlexLayout Xamarin. Forms](flex-layout.md)
- [AbsoluteLayout Xamarin. Forms](absolute-layout.md)
- [RelativeLayout Xamarin. Forms](relative-layout.md)
- [Otimizar o desempenho do layout](~/xamarin-forms/deploy-test/performance.md#optimize-layout-performance)
- [Compactação de layout](layout-compression.md)
