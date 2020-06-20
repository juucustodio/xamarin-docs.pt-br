---
title: Escolher um Xamarin.Forms layout
description: Xamarin.Formsas classes de layout permitem que você organize e agrupe controles de interface do usuário em seu aplicativo.
ms.prod: xamarin
ms.assetid: 05A39752-A174-447E-A30D-3CC9EF98CB96
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/21/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 16a48423d05ce1cede75c0020bf18f4f398f5adc
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2020
ms.locfileid: "84138496"
---
# <a name="choose-a-xamarinforms-layout"></a>Escolher um Xamarin.Forms layout

[![Baixar exemplo ](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layout)

Xamarin.Formsas classes de layout permitem que você organize e agrupe controles de interface do usuário em seu aplicativo. Escolher uma classe de layout requer o conhecimento de como o layout posiciona seus elementos filho e como o layout dimensiona seus elementos filho. Além disso, pode ser necessário aninhar layouts para criar o layout desejado.

A imagem a seguir mostra layouts típicos que podem ser obtidos com as Xamarin.Forms classes de layout principais:

[![As classes de layout principal noXamarin.Forms](images/layouts.png "[! Parar. Classes de layout não LOC (Xamarin. Forms)]")](images/layouts-large.png#lightbox "[! Parar. Classes de layout não LOC (Xamarin. Forms)]")

## <a name="stacklayout"></a>StackLayout

Um [`StackLayout`](xref:Xamarin.Forms.StackLayout) organiza elementos em uma pilha unidimensional, tanto horizontal quanto verticalmente. A [`Orientation`](xref:Xamarin.Forms.StackLayout.Orientation) propriedade especifica a direção dos elementos e a orientação padrão é [`Vertical`](xref:Xamarin.Forms.StackOrientation) . `StackLayout`normalmente é usado para organizar uma subseção da interface do usuário em uma página.

O XAML a seguir mostra como criar um vertical [`StackLayout`](xref:Xamarin.Forms.StackLayout) contendo três [`Label`](xref:Xamarin.Forms.Label) objetos:

```xaml
<StackLayout Margin="20,35,20,25">
    <Label Text="The StackLayout has its Margin property set, to control the rendering position of the StackLayout." />
    <Label Text="The Padding property can be set to specify the distance between the StackLayout and its children." />
    <Label Text="The Spacing property can be set to specify the distance between views in the StackLayout." />
</StackLayout>
```

Em um [`StackLayout`](xref:Xamarin.Forms.StackLayout) , se o tamanho de um elemento não for definido explicitamente, ele se expandirá para preencher a largura disponível ou a altura se a [`Orientation`](xref:Xamarin.Forms.StackLayout.Orientation) propriedade for definida como [`Horizontal`](xref:Xamarin.Forms.StackOrientation) .

Um [`StackLayout`](xref:Xamarin.Forms.StackLayout) é geralmente usado como layout pai, que contém outros layouts filho. No entanto, um `StackLayout` não deve ser usado para reproduzir um [`Grid`](xref:Xamarin.Forms.Grid) layout usando uma combinação de `StackLayout` objetos. O código a seguir mostra um exemplo dessa prática inadequada:

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

Isso é um desperdício porque cálculos de layout desnecessário são executados. Em vez disso, o layout desejado pode ser melhor obtido com o uso de um [`Grid`](xref:Xamarin.Forms.Grid) .

> [!TIP]
> Ao usar um [`StackLayout`](xref:Xamarin.Forms.StackLayout) , verifique se apenas um elemento filho está definido como [`LayoutOptions.Expands`](xref:Xamarin.Forms.LayoutOptions.Expands) . Essa propriedade garante que o filho especificado ocupe o maior espaço que o `StackLayout` pode dar a ele e é um desperdício executar esses cálculos mais de uma vez.

Para obter mais informações, consulte [ Xamarin.Forms StackLayout](stacklayout.md).

## <a name="grid"></a>Grade

Um [`Grid`](xref:Xamarin.Forms.Grid) é usado para exibir elementos em linhas e colunas, que podem ter tamanhos proporcionais ou absolutos. As linhas e colunas de uma grade são especificadas com [`RowDefinitions`](xref:Xamarin.Forms.Grid.RowDefinitions) as [`ColumnDefinitions`](xref:Xamarin.Forms.Grid.ColumnDefinitions) Propriedades e.

Para posicionar elementos em [`Grid`](xref:Xamarin.Forms.Grid) células específicas, use [`Grid.Column`](xref:Xamarin.Forms.Grid.ColumnProperty) as [`Grid.Row`](xref:Xamarin.Forms.Grid.RowProperty) Propriedades anexadas e. Para fazer com que os elementos se estendam por várias linhas e colunas, use as [`Grid.RowSpan`](xref:Xamarin.Forms.Grid.RowSpanProperty) [`Grid.ColumnSpan`](xref:Xamarin.Forms.Grid.ColumnSpanProperty) Propriedades anexadas e.

> [!NOTE]
> Um [`Grid`](xref:Xamarin.Forms.Grid) layout não deve ser confundido com tabelas e não tem o objetivo de apresentar dados tabulares. Ao contrário das tabelas HTML, um `Grid` é destinado a dispor conteúdo. Para exibir dados tabulares, considere usar [ListView](~/xamarin-forms/user-interface/listview/index.md), [CollectionView](~/xamarin-forms/user-interface/collectionview/index.md)ou [Tableview](~/xamarin-forms/user-interface/tableview.md).

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
- A largura da primeira coluna é definida como [`Auto`](xref:Xamarin.Forms.GridLength.Auto) e, portanto, é tão larga quanto a necessária para seus filhos. Nesse caso, são 200 unidades independentes de dispositivo de largura para acomodar a largura do primeiro [`Label`](xref:Xamarin.Forms.Label) .

O espaço pode ser distribuído em uma coluna ou linha usando o dimensionamento automático, o que permite que colunas e linhas se ajustem ao seu conteúdo. Isso é feito definindo a altura de a [`RowDefinition`](xref:Xamarin.Forms.RowDefinition) , ou a largura de a [`ColumnDefinition`](xref:Xamarin.Forms.ColumnDefinition) , para [`Auto`](xref:Xamarin.Forms.GridLength.Auto) . O dimensionamento proporcional também pode ser usado para distribuir o espaço disponível entre as linhas e colunas da grade por proporções ponderadas. Isso é feito definindo a altura de um `RowDefinition` , ou a largura de um `ColumnDefinition` , com um valor que usa o `*` operador.

> [!CAUTION]
> Tente garantir que o menor número possível de linhas e colunas seja definido como [`Auto`](xref:Xamarin.Forms.GridLength.Auto) tamanho. Cada linha ou coluna dimensionada automaticamente fará o mecanismo de layout realizar cálculos de layout adicionais. Em vez disso, use linhas e colunas de tamanho fixo, se possível. Como alternativa, defina linhas e colunas para ocupar uma quantidade proporcional de espaço com o valor de [`GridUnitType.Star`](xref:Xamarin.Forms.GridUnitType.Star) enumeração.

Para obter mais informações, consulte [ Xamarin.Forms grade](grid.md).

## <a name="flexlayout"></a>FlexLayout

Um [`FlexLayout`](xref:Xamarin.Forms.FlexLayout) é semelhante a um [`StackLayout`](xref:Xamarin.Forms.StackLayout) em que ele exibe elementos filho horizontal ou verticalmente em uma pilha. No entanto, um `FlexLayout` também pode encapsular seus filhos se houver muitos para caber em uma única linha ou coluna, além de permitir um controle mais granular do tamanho, da orientação e do alinhamento de seus elementos filho.

O XAML a seguir mostra como criar um [`FlexLayout`](xref:Xamarin.Forms.FlexLayout) que exibe seus modos de exibição em uma única coluna:

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

- A [`Direction`](xref:Xamarin.Forms.FlexLayout.Direction) propriedade é definida como `Column` , o que faz com que os filhos do `FlexLayout` sejam organizados em uma única coluna de itens.
- A [`AlignItems`](xref:Xamarin.Forms.FlexLayout.AlignItems) propriedade é definida como `Center` , o que faz com que cada item seja centralizado horizontalmente.
- A [`JustifyContent`](xref:Xamarin.Forms.FlexLayout.JustifyContent) propriedade é definida como `SpaceEvenly` , que aloca todos os espaços verticais restantes igualmente entre todos os itens e acima do primeiro item e abaixo do último item.

Para obter mais informações, consulte [ Xamarin.Forms FlexLayout](flex-layout.md).

## <a name="relativelayout"></a>RelativeLayout

Um [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) é usado para posicionar e dimensionar elementos em relação às propriedades dos elementos de layout ou irmãos. Por padrão, um elemento é posicionado no canto superior esquerdo do layout. Um `RelativeLayout` pode ser usado para criar UIs que são dimensionadas proporcionalmente entre os tamanhos de dispositivo.

Em um [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) , posições e tamanhos são especificados como restrições. As restrições têm [`Factor`](xref:Xamarin.Forms.ConstraintExpression.Factor) e [`Constant`](xref:Xamarin.Forms.ConstraintExpression.Constant) Propriedades, que podem ser usadas para definir posições e tamanhos como múltiplos (ou frações) de propriedades de outros objetos, além de uma constante. Além disso, as constantes podem ser negativas.

> [!NOTE]
> Um [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) dá suporte ao posicionamento de elementos fora de seus próprios limites.

O XAML a seguir mostra como organizar elementos em um [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) :

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

- O azul [`BoxView`](xref:Xamarin.Forms.BoxView) recebe um tamanho explícito de unidades independentes do dispositivo 50x50. Ele é colocado no canto superior esquerdo do layout, que é a posição padrão.
- O vermelho [`BoxView`](xref:Xamarin.Forms.BoxView) recebe um tamanho explícito de unidades independentes de dispositivo 50x50. Ele é colocado no canto superior direito do layout.
- O cinza [`BoxView`](xref:Xamarin.Forms.BoxView) recebe uma largura explícita de 15 unidades independentes de dispositivo e sua altura é definida como 75% da altura de seu pai.
- O verde [`BoxView`](xref:Xamarin.Forms.BoxView) não recebe um tamanho explícito. Sua posição é definida com relação ao `BoxView` nome `pole` .

> [!WARNING]
> Evite usar um `RelativeLayout` sempre que possível. Isso resultará em a CPU precisar realizar significativamente mais trabalho.

Para obter mais informações, consulte [ Xamarin.Forms RelativeLayout](relative-layout.md).

## <a name="absolutelayout"></a>AbsoluteLayout

Um [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout) é usado para posicionar e dimensionar elementos usando valores explícitos, ou valores relativos ao tamanho do layout. A posição é especificada pelo canto superior esquerdo do filho em relação ao canto superior esquerdo do `AbsoluteLayout` .

Um [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout) deve ser considerado um layout de finalidade especial para ser usado somente quando você puder impor um tamanho em filhos ou quando o tamanho do elemento não afetar o posicionamento de outros filhos. Um uso padrão desse layout é criar uma sobreposição, que aborda a página com outros controles, talvez para proteger o usuário de interagir com os controles normais na página.

> [!IMPORTANT]
> As `HorizontalOptions` `VerticalOptions` Propriedades e não têm efeito sobre os filhos de um `AbsoluteLayout` .

Dentro de um [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout) , a [`AbsoluteLayout.LayoutBounds`](xref:Xamarin.Forms.AbsoluteLayout.LayoutBoundsProperty) Propriedade anexada é usada para especificar a posição horizontal, a posição vertical, a largura e a altura de um elemento. Além disso, a [`AbsoluteLayout.LayoutFlags`](xref:Xamarin.Forms.AbsoluteLayout.LayoutFlagsProperty) Propriedade anexada Especifica como os limites de layout serão interpretados.

O XAML a seguir mostra como organizar elementos em um [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout) :

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

- Cada [`BoxView`](xref:Xamarin.Forms.BoxView) um recebe um tamanho explícito de 100x100 e é exibido na mesma posição, horizontalmente centralizada.
- O vermelho [`BoxView`](xref:Xamarin.Forms.BoxView) é girado em 30 graus e o verde `BoxView` é girado em 60 graus.
- Em cada [`BoxView`](xref:Xamarin.Forms.BoxView) , a [`AbsoluteLayout.LayoutFlags`](xref:Xamarin.Forms.AbsoluteLayout.LayoutFlagsProperty) Propriedade anexada é definida como `PositionProportional` , indicando que a posição é proporcional ao espaço restante depois que a largura e a altura são contabilizadas.

> [!CAUTION]
> Evite usar a [`AbsoluteLayout.AutoSize`](xref:Xamarin.Forms.AbsoluteLayout.AutoSize) propriedade sempre que possível, pois isso fará com que o mecanismo de layout execute cálculos de layout adicionais.

Para obter mais informações, consulte [ Xamarin.Forms AbsoluteLayout](absolute-layout.md).

## <a name="input-transparency"></a>Transparência de entrada

Cada elemento visual tem uma [`InputTransparent`](xref:Xamarin.Forms.VisualElement.InputTransparent) propriedade que é usada para definir se o elemento recebe entrada. Seu valor padrão é `false` , garantindo que o elemento receba a entrada.

Quando essa propriedade é definida em uma classe de layout, seu valor é transferido para elementos filho. Portanto, definir a [`InputTransparent`](xref:Xamarin.Forms.VisualElement.InputTransparent) propriedade como `true` em uma classe de layout resultará em todos os elementos dentro do layout não receberem a entrada.

## <a name="layout-performance"></a>Desempenho do layout

Para obter o melhor desempenho de layout possível, siga as diretrizes em [otimizar o desempenho do layout](~/xamarin-forms/deploy-test/performance.md#optimize-layout-performance).

Além disso, o desempenho de renderização de página também pode ser melhorado usando a compactação de layout, que remove os layouts especificados da árvore visual. Para obter mais informações, consulte [compactação de layout](layout-compression.md).

## <a name="related-links"></a>Links relacionados

- [Layout (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layout)
- [Xamarin.FormsLayouts (vídeo)](https://youtu.be/4HlLjTZQzjM)
- [Xamarin.FormsStackLayout](stacklayout.md)
- [Xamarin.FormsGrade](grid.md)
- [Xamarin.FormsFlexLayout](flex-layout.md)
- [Xamarin.FormsAbsoluteLayout](absolute-layout.md)
- [Xamarin.FormsRelativeLayout](relative-layout.md)
- [Otimizar o desempenho do layout](~/xamarin-forms/deploy-test/performance.md#optimize-layout-performance)
- [Compactação de layout](layout-compression.md)
