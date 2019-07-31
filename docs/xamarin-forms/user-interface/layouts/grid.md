---
title: Grade de xamarin. Forms
description: Este artigo explica como usar a classe de grade do xamarin. Forms para apresentar exibições em grades, que possuem linhas e colunas.
ms.prod: xamarin
ms.assetid: 762B1802-D185-494C-B643-74EED55882FE
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/26/2017
ms.openlocfilehash: 38206812fbd746a63c5bff18111779c25deedc66
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68648530"
---
# <a name="xamarinforms-grid"></a>Grade de xamarin. Forms

[![Baixar exemplo](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layout)

[`Grid`](xref:Xamarin.Forms.Grid) dá suporte à organização exibições em linhas e colunas. Linhas e colunas podem ser definidas para ter tamanhos proporcionais ou tamanhos absolutos. O `Grid` layout não deve ser confundido com tabelas tradicionais e não se destina a apresentar dados tabulares. `Grid` não tem o conceito de linha, coluna ou célula de formatação. Ao contrário das tabelas HTML, `Grid` destina-se puramente para dispor o conteúdo.

[![](grid-images/layouts-sml.png "Xamarin. Forms Layouts")](grid-images/layouts.png#lightbox "Layouts do xamarin. Forms")

Este artigo abordará:

- **[Finalidade](#purpose)**  &ndash; usos comuns do `Grid`.
- **[Uso](#usage)**  &ndash; como usar `Grid` para alcançar o design desejado.
  - **[Linhas e colunas](#rows-and-columns)**  &ndash; especificar linhas e colunas para o `Grid`.
  - **[Colocação de modos de exibição](#placing-views-in-a-grid)**  &ndash; adicionar modos de exibição para a grade em linhas e colunas específicas.
  - **[Espaçamento](#spacing)**  &ndash; configurar os espaços entre linhas e colunas.
  - **[Spans](#spans)**  &ndash; configurar elementos para abranger várias linhas ou colunas.

![](grid-images/grid.png "Exploração de grade")

## <a name="purpose"></a>Finalidade

`Grid` pode ser usado para organizar os modos de exibição em uma grade. Isso é útil em um número de casos:

- Organizando botões em um aplicativo de Calculadora
- Organizando botões/escolhas em uma grade, como o iOS ou Android telas iniciais
- Organizando os modos de exibição para que eles sejam de tamanho igual em uma dimensão (como em algumas barras de ferramentas)

## <a name="usage"></a>Uso

Ao contrário das tabelas tradicionais, `Grid` não deduz o número e os tamanhos de linhas e colunas do conteúdo. Em vez disso, `Grid` tem `RowDefinitions` e `ColumnDefinitions` coleções. Eles mantêm as definições de quantas linhas e colunas serão dispostas. As exibições são adicionadas à `Grid` com a linha especificada e os índices de coluna, que identificam quais linhas e colunas de um modo de exibição deve ser colocado em.

### <a name="rows-and-columns"></a>Linhas e colunas

Informações de linha e coluna são armazenadas no `Grid`do `RowDefinitions`  &  `ColumnDefinitions` propriedades, que são coleções de cada de [ `RowDefinition` ](xref:Xamarin.Forms.RowDefinition) e [ `ColumnDefinition` ](xref:Xamarin.Forms.ColumnDefinition)objetos, respectivamente. `RowDefinition` tem uma única propriedade, `Height`, e `ColumnDefinition` tem uma propriedade única, `Width`. As opções para altura e largura são da seguinte maneira:

- **Auto** &ndash; automaticamente os tamanhos para ajustar o conteúdo da linha ou coluna. Especificado como [ `GridUnitType.Auto` ](xref:Xamarin.Forms.GridUnitType) em C# ou como `Auto` em XAML.
- **Proportional(*)** &ndash; dimensiona as colunas e linhas como uma proporção de espaço restante. Especificado como um valor e `GridUnitType.Star` em C# e como `#*` em XAML, com `#` sendo o valor desejado. Especificando uma linha/coluna com `*` fará com que ela preencher o espaço disponível.
- **Absoluto** &ndash; dimensiona as colunas e linhas com valores de altura e largura fixas, específicos. Especificado como um valor e `GridUnitType.Absolute` em C# e como `#` em XAML, com `#` sendo o valor desejado.

> [!NOTE]
> Os valores de largura para colunas são definidos `*` como por padrão no Xamarin. Forms, o que garante que a coluna preencherá o espaço disponível. Os valores de altura das linhas também são definidos `*` como por padrão.

Considere um aplicativo que precisa de três linhas e duas colunas. A linha inferior precisa ser exatamente 200px altura e a linha superior precisa ser duas vezes com a altura da linha intermediária. A coluna à esquerda deve ser grande o suficiente para caber o conteúdo e a coluna à direita precisa preencher o espaço restante.

No XAML:

```xaml
<Grid>
  <Grid.RowDefinitions>
    <RowDefinition Height="2*" />
    <RowDefinition Height="*" />
    <RowDefinition Height="200" />
  </Grid.RowDefinitions>
  <Grid.ColumnDefinitions>
    <ColumnDefinition Width="Auto" />
    <ColumnDefinition Width="*" />
  </Grid.ColumnDefinitions>
</Grid>
```

No C#:

```csharp
Grid grid = new Grid();
grid.RowDefinitions.Add (new RowDefinition { Height = new GridLength(2, GridUnitType.Star) });
grid.RowDefinitions.Add (new RowDefinition { Height = new GridLength (1, GridUnitType.Star) });
grid.RowDefinitions.Add (new RowDefinition { Height = new GridLength(200)});
grid.ColumnDefinitions.Add (new ColumnDefinition{ Width = new GridLength (200) });
grid.ColumnDefinitions.Add(new ColumnDefinition { Width = new GridLength(1, GridUnitType.Auto) });
```

### <a name="placing-views-in-a-grid"></a>Colocando exibições em uma grade

Para colocar os modos de exibição em um `Grid` você precisará adicioná-los como filhos na grade, e em seguida, especificar quais linhas e colunas eles pertencem.

No XAML, use `Grid.Row` e `Grid.Column` em cada exibição individual para especificar o posicionamento. Observe que `Grid.Row` e `Grid.Column` especificar local com base nas listas de base zero de linhas e colunas. Isso significa que, em uma 4x4 grade, a célula superior esquerda é (0,0) e a célula inferior direita é (3,3).

O `Grid` mostrado a seguir contém quatro células:

![](grid-images/label-grid.png "Grade com quatro modos de exibição")

No XAML:

```xaml
<Grid>
  <Grid.RowDefinitions>
    <RowDefinition Height="*" />
    <RowDefinition Height="*" />
  </Grid.RowDefinitions>
  <Grid.ColumnDefinitions>
    <ColumnDefinition Width="*" />
    <ColumnDefinition Width="*" />
  </Grid.ColumnDefinitions>
  <Label Text="Top Left" Grid.Row="0" Grid.Column="0" />
  <Label Text="Top Right" Grid.Row="0" Grid.Column="1" />
  <Label Text="Bottom Left" Grid.Row="1" Grid.Column="0" />
  <Label Text="Bottom Right" Grid.Row="1" Grid.Column="1" />
</Grid>
```

No C#:

```csharp
var grid = new Grid();

grid.RowDefinitions.Add(new RowDefinition { Height = new GridLength(1, GridUnitType.Star)});
grid.RowDefinitions.Add(new RowDefinition { Height = new GridLength(1, GridUnitType.Star)});
grid.ColumnDefinitions.Add(new ColumnDefinition { Width = new GridLength(1, GridUnitType.Star)});
grid.ColumnDefinitions.Add(new ColumnDefinition { Width = new GridLength(1, GridUnitType.Star)});

var topLeft = new Label { Text = "Top Left" };
var topRight = new Label { Text = "Top Right" };
var bottomLeft = new Label { Text = "Bottom Left" };
var bottomRight = new Label { Text = "Bottom Right" };

grid.Children.Add(topLeft, 0, 0);
grid.Children.Add(topRight, 1, 0);
grid.Children.Add(bottomLeft, 0, 1);
grid.Children.Add(bottomRight, 1, 1);
```

O código acima cria a grade com quatro rótulos, duas colunas e duas linhas. Observe que cada rótulo terá o mesmo tamanho e que as linhas se expandem para usar todo o espaço disponível.

No exemplo acima, as exibições são adicionadas para o [ `Grid.Children` ](xref:Xamarin.Forms.Grid.Children) coleção usando o [ `Add` ](xref:Xamarin.Forms.Grid.IGridList`1.Add*) sobrecarga que especifica os argumentos left e top. Ao usar o [ `Add` ](xref:Xamarin.Forms.Grid.IGridList`1.Add*) durante a sobrecarga que especifica para a esquerda, direita, superior e argumentos de parte inferior, esquerda e argumentos superior sempre fará referência às células dentro a [ `Grid` ](xref:Xamarin.Forms.Grid), à direita e argumentos de parte inferior podem aparecer para se referir às células que estão fora de `Grid`. Isso ocorre porque o argumento à direita sempre deve ser maior que o argumento esquerdo e o argumento inferior sempre deve ser maior que o argumento superior. O exemplo a seguir mostra o código equivalente usando os dois `Add` sobrecargas:

```csharp
// left, top
grid.Children.Add(topLeft, 0, 0);
grid.Children.Add(topRight, 1, 0);
grid.Children.Add(bottomLeft, 0, 1);
grid.Children.Add(bottomRight, 1, 1);

// left, right, top, bottom
grid.Children.Add(topLeft, 0, 1, 0, 1);
grid.Children.Add(topRight, 1, 2, 0, 1);
grid.Children.Add(bottomLeft, 0, 1, 1, 2);
grid.Children.Add(bottomRight, 1, 2, 1, 2);
```

### <a name="spacing"></a>Espaçamento

`Grid` tem propriedades para controlar o espaçamento entre linhas e colunas. As seguintes propriedades estão disponíveis para personalizar o `Grid`:

- **ColumnSpacing** &ndash; a quantidade de espaço entre colunas. O valor padrão dessa propriedade é 6.
- **RowSpacing** &ndash; a quantidade de espaço entre as linhas. O valor padrão dessa propriedade é 6.

O XAML a seguir especifica um `Grid` com duas colunas, uma linha e 5 px de espaçamento entre colunas:

```xaml
<Grid ColumnSpacing="5">
  <Grid.ColumnDefinitions>
    <ColumnDefinition Width="*" />
    <ColumnDefinition Width="*" />
  </Grid.ColumnDefinitions>
</Grid>
```

No C#:

```csharp
var grid = new Grid { ColumnSpacing = 5 };
grid.ColumnDefinitions.Add(new ColumnDefinition { Width = new GridLength (1, GridUnitType.Star)});
grid.ColumnDefinitions.Add(new ColumnDefinition { Width = new GridLength (1, GridUnitType.Star)});
```

### <a name="spans"></a>Intervalos

Geralmente, ao trabalhar com uma grade, há um elemento que deve ocupar mais de uma linha ou coluna. Considere um aplicativo de calculadora simples:

![](grid-images/calculator.png "Aplicativo Calulator")

Observe que o botão 0 abrange duas colunas, assim como em que as calculadoras internos para cada plataforma. Isso é feito usando o `ColumnSpan` propriedade, que especifica quantas colunas um elemento deve ocupar. O XAML para esse botão:

```xaml
<Button Text = "0" Grid.Row="4" Grid.Column="0" Grid.ColumnSpan="2" />
```

E, em C#:

```csharp
Button zeroButton = new Button { Text = "0" };
controlGrid.Children.Add (zeroButton, 0, 4);
Grid.SetColumnSpan (zeroButton, 2);
```

Observe que no código, métodos estáticos do `Grid` classe são usados para executar alterações de posicionamento, incluindo as alterações `ColumnSpan` e `RowSpan`. Também Observe que, ao contrário de outras propriedades que podem ser definidas a qualquer momento, as propriedades definidas usando os métodos estáticos já devem estar na grade antes que sejam alteradas.

O XAML completo para o aplicativo Calculadora acima é da seguinte maneira:

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
x:Class="LayoutSamples.CalculatorGridXAML"
Title = "Calculator - XAML"
BackgroundColor="#404040">
    <ContentPage.Resources>
        <ResourceDictionary>
            <Style x:Key="plainButton" TargetType="Button">
                <Setter Property="BackgroundColor" Value="#eee"/>
                <Setter Property="TextColor" Value="Black" />
                <Setter Property="BorderRadius" Value="0"/>
                <Setter Property="FontSize" Value="40" />
            </Style>
            <Style x:Key="darkerButton" TargetType="Button">
                <Setter Property="BackgroundColor" Value="#ddd"/>
                <Setter Property="TextColor" Value="Black" />
                <Setter Property="BorderRadius" Value="0"/>
                <Setter Property="FontSize" Value="40" />
            </Style>
            <Style x:Key="orangeButton" TargetType="Button">
                <Setter Property="BackgroundColor" Value="#E8AD00"/>
                <Setter Property="TextColor" Value="White" />
                <Setter Property="BorderRadius" Value="0"/>
                <Setter Property="FontSize" Value="40" />
            </Style>
        </ResourceDictionary>
    </ContentPage.Resources>
    <ContentPage.Content>
        <Grid x:Name="controlGrid" RowSpacing="1" ColumnSpacing="1">
            <Grid.RowDefinitions>
                <RowDefinition Height="150" />
                <RowDefinition Height="*" />
                <RowDefinition Height="*" />
                <RowDefinition Height="*" />
                <RowDefinition Height="*" />
                <RowDefinition Height="*" />
            </Grid.RowDefinitions>
            <Grid.ColumnDefinitions>
                <ColumnDefinition Width="*" />
                <ColumnDefinition Width="*" />
                <ColumnDefinition Width="*" />
                <ColumnDefinition Width="*" />
            </Grid.ColumnDefinitions>
            <Label Text="0" Grid.Row="0" HorizontalTextAlignment="End" VerticalTextAlignment="End" TextColor="White"
        FontSize="60" Grid.ColumnSpan="4" />
            <Button Text = "C" Grid.Row="1" Grid.Column="0"
        Style="{StaticResource darkerButton}" />
            <Button Text = "+/-" Grid.Row="1" Grid.Column="1"
        Style="{StaticResource darkerButton}" />
            <Button Text = "%" Grid.Row="1" Grid.Column="2"
        Style="{StaticResource darkerButton}" />
            <Button Text = "div" Grid.Row="1" Grid.Column="3"
        Style="{StaticResource orangeButton}" />
            <Button Text = "7" Grid.Row="2" Grid.Column="0"
        Style="{StaticResource plainButton}" />
            <Button Text = "8" Grid.Row="2" Grid.Column="1"
        Style="{StaticResource plainButton}" />
            <Button Text = "9" Grid.Row="2" Grid.Column="2"
        Style="{StaticResource plainButton}" />
            <Button Text = "X" Grid.Row="2" Grid.Column="3"
        Style="{StaticResource orangeButton}" />
            <Button Text = "4" Grid.Row="3" Grid.Column="0"
        Style="{StaticResource plainButton}" />
            <Button Text = "5" Grid.Row="3" Grid.Column="1"
        Style="{StaticResource plainButton}" />
            <Button Text = "6" Grid.Row="3" Grid.Column="2"
        Style="{StaticResource plainButton}" />
            <Button Text = "-" Grid.Row="3" Grid.Column="3"
        Style="{StaticResource orangeButton}" />
            <Button Text = "1" Grid.Row="4" Grid.Column="0"
        Style="{StaticResource plainButton}" />
            <Button Text = "2" Grid.Row="4" Grid.Column="1"
        Style="{StaticResource plainButton}" />
            <Button Text = "3" Grid.Row="4" Grid.Column="2"
        Style="{StaticResource plainButton}" />
            <Button Text = "+" Grid.Row="4" Grid.Column="3"
        Style="{StaticResource orangeButton}" />
            <Button Text = "0" Grid.ColumnSpan="2"
        Grid.Row="5" Grid.Column="0" Style="{StaticResource plainButton}" />
            <Button Text = "." Grid.Row="5" Grid.Column="2"
        Style="{StaticResource plainButton}" />
            <Button Text = "=" Grid.Row="5" Grid.Column="3"
        Style="{StaticResource orangeButton}" />
        </Grid>
    </ContentPage.Content>
</ContentPage>
```

Observe que tanto o rótulo na parte superior da grade e o botão zero são occuping mais de uma coluna. Embora um layout semelhante pode ser obtido usando grades aninhadas, a `ColumnSpan`  &  `RowSpan` abordagem é mais simples.

A implementação do C#:

```csharp
public CalculatorGridCode ()
{
  Title = "Calculator - C#";
  BackgroundColor = Color.FromHex ("#404040");

  var plainButton = new Style (typeof(Button)) {
    Setters = {
      new Setter { Property = Button.BackgroundColorProperty, Value = Color.FromHex ("#eee") },
      new Setter { Property = Button.TextColorProperty, Value = Color.Black },
      new Setter { Property = Button.BorderRadiusProperty, Value = 0 },
      new Setter { Property = Button.FontSizeProperty, Value = 40 }
    }
  };
  var darkerButton = new Style (typeof(Button)) {
    Setters = {
      new Setter { Property = Button.BackgroundColorProperty, Value = Color.FromHex ("#ddd") },
      new Setter { Property = Button.TextColorProperty, Value = Color.Black },
      new Setter { Property = Button.BorderRadiusProperty, Value = 0 },
      new Setter { Property = Button.FontSizeProperty, Value = 40 }
    }
  };
  var orangeButton = new Style (typeof(Button)) {
    Setters = {
      new Setter { Property = Button.BackgroundColorProperty, Value = Color.FromHex ("#E8AD00") },
      new Setter { Property = Button.TextColorProperty, Value = Color.White },
      new Setter { Property = Button.BorderRadiusProperty, Value = 0 },
      new Setter { Property = Button.FontSizeProperty, Value = 40 }
    }
  };

  var controlGrid = new Grid { RowSpacing = 1, ColumnSpacing = 1 };
  controlGrid.RowDefinitions.Add (new RowDefinition { Height = new GridLength (150) });
  controlGrid.RowDefinitions.Add (new RowDefinition { Height = new GridLength (1, GridUnitType.Star) });
  controlGrid.RowDefinitions.Add (new RowDefinition { Height = new GridLength (1, GridUnitType.Star) });
  controlGrid.RowDefinitions.Add (new RowDefinition { Height = new GridLength (1, GridUnitType.Star) });
  controlGrid.RowDefinitions.Add (new RowDefinition { Height = new GridLength (1, GridUnitType.Star) });
  controlGrid.RowDefinitions.Add (new RowDefinition { Height = new GridLength (1, GridUnitType.Star) });

  controlGrid.ColumnDefinitions.Add (new ColumnDefinition { Width = new GridLength (1, GridUnitType.Star) });
  controlGrid.ColumnDefinitions.Add (new ColumnDefinition { Width = new GridLength (1, GridUnitType.Star) });
  controlGrid.ColumnDefinitions.Add (new ColumnDefinition { Width = new GridLength (1, GridUnitType.Star) });
  controlGrid.ColumnDefinitions.Add (new ColumnDefinition { Width = new GridLength (1, GridUnitType.Star) });

  var label = new Label {
    Text = "0",
    HorizontalTextAlignment = TextAlignment.End,
    VerticalTextAlignment = TextAlignment.End,
    TextColor = Color.White,
    FontSize = 60
  };
  controlGrid.Children.Add (label, 0, 0);

  Grid.SetColumnSpan (label, 4);

  controlGrid.Children.Add (new Button { Text = "C", Style = darkerButton }, 0, 1);
  controlGrid.Children.Add (new Button { Text = "+/-", Style = darkerButton }, 1, 1);
  controlGrid.Children.Add (new Button { Text = "%", Style = darkerButton }, 2, 1);
  controlGrid.Children.Add (new Button { Text = "div", Style = orangeButton }, 3, 1);
  controlGrid.Children.Add (new Button { Text = "7", Style = plainButton }, 0, 2);
  controlGrid.Children.Add (new Button { Text = "8", Style = plainButton }, 1, 2);
  controlGrid.Children.Add (new Button { Text = "9", Style = plainButton }, 2, 2);
  controlGrid.Children.Add (new Button { Text = "X", Style = orangeButton }, 3, 2);
  controlGrid.Children.Add (new Button { Text = "4", Style = plainButton }, 0, 3);
  controlGrid.Children.Add (new Button { Text = "5", Style = plainButton }, 1, 3);
  controlGrid.Children.Add (new Button { Text = "6", Style = plainButton }, 2, 3);
  controlGrid.Children.Add (new Button { Text = "-", Style = orangeButton }, 3, 3);
  controlGrid.Children.Add (new Button { Text = "1", Style = plainButton }, 0, 4);
  controlGrid.Children.Add (new Button { Text = "2", Style = plainButton }, 1, 4);
  controlGrid.Children.Add (new Button { Text = "3", Style = plainButton }, 2, 4);
  controlGrid.Children.Add (new Button { Text = "+", Style = orangeButton }, 3, 4);
  controlGrid.Children.Add (new Button { Text = ".", Style = plainButton }, 2, 5);
  controlGrid.Children.Add (new Button { Text = "=", Style = orangeButton }, 3, 5);

  var zeroButton = new Button { Text = "0", Style = plainButton };
  controlGrid.Children.Add (zeroButton, 0, 5);
  Grid.SetColumnSpan (zeroButton, 2);

  Content = controlGrid;
}
```


## <a name="related-links"></a>Links relacionados

- [Criação de aplicativos móveis com xamarin. Forms, capítulo 17](https://developer.xamarin.com/r/xamarin-forms/book/chapter17.pdf)
- [Grade](xref:Xamarin.Forms.Grid)
- [Layout (amostra)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layout)
- [Exemplo de BusinessTumble (amostra)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-businesstumble)
