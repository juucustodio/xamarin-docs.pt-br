---
title: Grade de xamarin. Forms
description: Este artigo explica como usar a classe xamarin. Forms grade para apresentar modos de exibição em grade, que possuem linhas e colunas.
ms.prod: xamarin
ms.assetid: 762B1802-D185-494C-B643-74EED55882FE
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/26/2017
ms.openlocfilehash: a50144f5e0962bd74858bb7731e30cef5dd31b6d
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/08/2018
ms.locfileid: "35245147"
---
# <a name="xamarinforms-grid"></a>Grade de xamarin. Forms

[`Grid`](https://developer.xamarin.com/api/type/Xamarin.Forms.Grid/) dá suporte à organização exibições em linhas e colunas. Linhas e colunas podem ser definidas para ter tamanhos proporcionais ou tamanhos absolutos. O `Grid` layout não deve ser confundido com tabelas tradicionais e não se destina a apresentar dados tabulares. `Grid` não tem o conceito de linha, coluna ou célula de formatação. Ao contrário das tabelas HTML, `Grid` destina-se somente para dispor conteúdo.

[![](grid-images/layouts-sml.png "Layouts de xamarin. Forms")](grid-images/layouts.png#lightbox "xamarin. Forms Layouts")

Este artigo aborda:

- **[Finalidade](#Purpose)**  &ndash; usos comuns `Grid`.
- **[Uso](#Usage)**  &ndash; como usar `Grid` para obter o design desejado.
  - **[Linhas e colunas](#Rows_and_Columns)**  &ndash; especificar linhas e colunas para o `Grid`.
  - **[Colocando exibições](#Placing_Views)**  &ndash; adicionar modos de exibição em grade em linhas e colunas específicas.
  - **[Espaçamento](#Spacing)**  &ndash; configurar os espaços entre as linhas e colunas.
  - **[Intervalos](#Spans)**  &ndash; configurar elementos se estenda por várias linhas ou colunas.

![](grid-images/grid.png "Exploração de grade")

## <a name="purpose"></a>Finalidade

`Grid` pode ser usado para organizar exibições em uma grade. Isso é útil em um número de casos:

- Organizando botões em um aplicativo de cálculo
- Organizando botões/opções em uma grade, como o iOS ou Android telas inicial
- Organizando os modos de exibição para que eles sejam de tamanho igual em uma dimensão (como em algumas barras de ferramentas)

## <a name="usage"></a>Uso

Ao contrário das tabelas tradicionais, `Grid` não deduzir o número e os tamanhos de linhas e colunas do conteúdo. Em vez disso, `Grid` tem `RowDefinitions` e `ColumnDefinitions` coleções. Mantenha a definições de quantas linhas e colunas serão dispostas. As exibições são adicionadas à `Grid` com a linha especificada e os índices de coluna, que identificar quais linhas e colunas seja colocado em um modo de exibição.

<a name="Rows_and_Columns" />

### <a name="rows-and-columns"></a>Linhas e colunas

Informações de linha e coluna são armazenadas em `Grid`do `RowDefinitions`  &  `ColumnDefinitions` propriedades, que são cada coleções de [ `RowDefinition` ](https://developer.xamarin.com/api/type/Xamarin.Forms.RowDefinition/) e [ `ColumnDefinition` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ColumnDefinition/)objetos, respectivamente. `RowDefinition` tem uma propriedade única, `Height`, e `ColumnDefinition` tem uma propriedade única, `Width`. As opções para altura e largura são da seguinte maneira:

- **Auto** &ndash; automaticamente os tamanhos para ajustar o conteúdo da linha ou coluna. Especificado como [ `GridUnitType.Auto` ](https://developer.xamarin.com/api/type/Xamarin.Forms.GridUnitType/) em c# ou como `Auto` em XAML.
- **Proportional(*)** &ndash; tamanhos de linhas e colunas como uma proporção o espaço restante. Especificado como um valor e `GridUnitType.Star` em c# e como `#*` em XAML, com `#` sendo o valor desejado. Especificando uma linha ou coluna com `*` fará com que ele preencher o espaço disponível.
- **Absoluto** &ndash; tamanhos de colunas e linhas com valores de altura e largura fixas, específicos. Especificado como um valor e `GridUnitType.Absolute` em c# e como `#` em XAML, com `#` sendo o valor desejado.

> [!NOTE]
> Os valores de largura de colunas são definidos como ' * ' por padrão em xamarin. Forms, que garante que a coluna preencherá o espaço disponível.

Considere um aplicativo que precisa de três linhas e duas colunas. A linha inferior deve ser exatamente 200px altura e a linha superior precisa ser duas vezes altura da linha do meio. A coluna da esquerda deve ser grande o suficiente para ajustar o conteúdo e a coluna da direita deve preencher o espaço restante.

Em XAML:

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
var grid = new Grid();
grid.RowDefinitions.Add (new RowDefinition { Height = new GridLength(2, GridUnitType.Star) });
grid.RowDefinitions.Add (new RowDefinition { Height = new GridLength (1, GridUnitType.Star) });
grid.RowDefinitions.Add (new RowDefinition { Height = new GridLength(200)});
grid.ColumnDefinitions.Add (new ColumnDefinition{ Width = new GridLength (200) });
```

<a name="Placing_Views" />

### <a name="placing-views-in-a-grid"></a>Colocando exibições em uma grade

Para colocar os modos de exibição em um `Grid` você precisará adicioná-los como filhos na grade, em seguida, especificar quais linhas e colunas pertencem.

Em XAML, use `Grid.Row` e `Grid.Column` em cada exibição individual para especificar o posicionamento. Observe que `Grid.Row` e `Grid.Column` especificar local com base na lista de base zero de linhas e colunas. Isso significa que em uma 4x4 grade, a célula superior esquerda (0,0) e a célula inferior direita é (3,3).

O `Grid` mostrado abaixo contém quatro células:

![](grid-images/label-grid.png "Grade com quatro modos de exibição")

Em XAML:

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

No exemplo acima, as exibições são adicionadas para o [ `Grid.Children` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Grid.Children/) coleção usando o [ `Add` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Grid+IGridList%3CT%3E.Add/p/Xamarin.Forms.View/System.Int32/System.Int32/) sobrecarga que especifica os argumentos esquerdo e superiores. Ao usar o [ `Add` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Grid+IGridList%3CT%3E.Add/p/Xamarin.Forms.View/System.Int32/System.Int32/System.Int32/System.Int32/) durante a sobrecarga que especifica à esquerda, direita, superior e argumentos de parte inferior esquerda e superiores argumentos sempre fará referência às células dentro de [ `Grid` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Grid/), à direita e argumentos inferior podem aparecer para se referir a células que estão fora de `Grid`. Isso ocorre porque o argumento da direita sempre deve ser maior que o argumento da esquerda e o argumento inferior sempre deve ser maior que o argumento superior. O exemplo a seguir mostra o código equivalente usando `Add` sobrecargas:

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

`Grid` possui propriedades para controlar o espaçamento entre linhas e colunas.  As propriedades a seguir estão disponíveis para personalizar o `Grid`:

- **ColumnSpacing** &ndash; a quantidade de espaço entre colunas.
- **RowSpacing** &ndash; a quantidade de espaço entre as linhas.

O XAML a seguir especifica um `Grid` com duas colunas, uma linha e 5 px de espaçamento entre colunas:

```xaml
<Grid ColumnSpacing="5">
  <Grid.ColumnDefinitions>
    <ColumnDefinitions Width="*" />
    <ColumnDefinitions Width="*" />
  </Grid.ColumnDefinitions>
</Grid>
```

No C#:

```csharp
var grid = new Grid { ColumnSpacing = 5 };
grid.ColumnDefnitions.Add(new ColumnDefinition { Width = new GridLength (1, GridUnitType.Star)});
grid.ColumnDefnitions.Add(new ColumnDefinition { Width = new GridLength (1, GridUnitType.Star)});
```

### <a name="spans"></a>Intervalos

Geralmente, ao trabalhar com uma grade, há um elemento que deve ocupar mais de uma linha ou coluna. Considere um aplicativo de cálculo simples:

![](grid-images/calculator.png "Aplicativo de Calulator")

Observe que o botão 0 abrange duas colunas, assim como nas calculadoras internas para cada plataforma. Isso é feito usando o `ColumnSpan` propriedade, que especifica quantas colunas de um elemento deve ocupar. O XAML para esse botão:

```xaml
<Button Text = "0" Grid.Row="4" Grid.Column="0" Grid.ColumnSpan="2" />
```

E no c#:

```csharp
Button zeroButton = new Button { Text = "0" };
controlGrid.Children.Add (zeroButton, 0, 4);
Grid.SetColumnSpan (zeroButton, 2);
```

Observe que no código, métodos estáticos a `Grid` classe são usadas para realizar alterações posicionamento, inclusive nas `ColumnSpan` e `RowSpan`. Também Observe que, ao contrário de outras propriedades que podem ser definidas a qualquer momento, propriedades definidas usando os métodos estáticos devem já estar na grade antes que sejam alteradas.

O XAML completo para o aplicativo de calculadora acima é o seguinte:

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

Observe que tanto o rótulo na parte superior da grade e o botão zero são occuping mais de uma coluna. Embora um layout semelhante pode ser obtido usando a grade aninhada, o `ColumnSpan`  &  `RowSpan` abordagem é mais simples.

A implementação do c#:

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

- [Criando aplicativos móveis com o xamarin. Forms, capítulo 17](https://developer.xamarin.com/r/xamarin-forms/book/chapter17.pdf)
- [Grade](https://developer.xamarin.com/api/type/Xamarin.Forms.Grid/)
- [Layout (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Layout/)
- [Exemplo de BusinessTumble (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/BusinessTumble/)
