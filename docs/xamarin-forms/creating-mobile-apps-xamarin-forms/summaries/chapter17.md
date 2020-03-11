---
title: Resumo do capítulo 17. Domínio da grade
description: 'Criação de aplicativos móveis com xamarin. Forms: Resumo do capítulo 17. Domínio da grade'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 71EDEF9C-4220-4D2E-A235-43F1EC8746C1
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2017
ms.openlocfilehash: 37b5e2bbafa816de27390771ae6daa33c74f7651
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2020
ms.locfileid: "70760635"
---
# <a name="summary-of-chapter-17-mastering-the-grid"></a>Resumo do capítulo 17. Domínio da grade

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17)

O [`Grid`](xref:Xamarin.Forms.Grid) é um poderoso mecanismo de layout que organiza seus filhos em linhas e colunas de células. Ao contrário do elemento HTML `table` semelhante, o `Grid` é exclusivamente para fins de layout em vez de apresentação.

## <a name="the-basic-grid"></a>A grade básica

`Grid` deriva de [`Layout<View>`](xref:Xamarin.Forms.Layout`1), que define uma propriedade [`Children`](xref:Xamarin.Forms.Layout`1.Children) que `Grid` herda. Você pode preencher essa coleção em XAML ou código.

### <a name="the-grid-in-xaml"></a>A grade em XAML

A definição de um `Grid` em XAML geralmente começa com o preenchimento das coleções [`RowDefinitions`](xref:Xamarin.Forms.Grid.RowDefinitions) e [`ColumnDefinitions`](xref:Xamarin.Forms.Grid.ColumnDefinitions) do `Grid` com objetos [`RowDefinition`](xref:Xamarin.Forms.RowDefinition) [e`ColumnDefinition`.](xref:Xamarin.Forms.ColumnDefinition) É assim que você estabelece o número de linhas e colunas do `Grid`e suas propriedades.

`RowDefinition` tem uma propriedade [`Height`](xref:Xamarin.Forms.RowDefinition.Height) e `ColumnDefinition` tem uma propriedade [`Width`](xref:Xamarin.Forms.ColumnDefinition.Width) , ambas de tipo [`GridLength`](xref:Xamarin.Forms.GridLength), uma estrutura.

Em XAML, o [`GridLengthTypeConverter`](xref:Xamarin.Forms.GridLengthTypeConverter) converte cadeias de caracteres de texto simples em valores `GridLength`. Nos bastidores, o [construtor`GridLength`](xref:Xamarin.Forms.GridLength.%23ctor(System.Double,Xamarin.Forms.GridUnitType)) cria o valor de `GridLength` com base em um número e um valor do tipo [`GridUnitType`](xref:Xamarin.Forms.GridUnitType), uma enumeração com três membros:

- [`Absolute`](xref:Xamarin.Forms.GridUnitType.Absolute) &mdash; a largura ou a altura é especificada em unidades independentes do dispositivo (um número em XAML)
- [`Auto`](xref:Xamarin.Forms.GridUnitType.Auto) &mdash; a altura ou largura é autodimensionada com base no conteúdo da célula ("auto" em XAML)
- [`Star`](xref:Xamarin.Forms.GridUnitType.Star) &mdash; altura ou largura sobrando é alocada proporcionalmente (um número com "\*", chamado *estrela*, em XAML)

Cada filho da `Grid` também deve ser atribuído a uma linha e coluna (explícita ou implicitamente). Se estende por linha e coluna spans são opcionais. Todos eles são especificados usando as propriedades vinculáveis anexadas &mdash; propriedades definidas pelo `Grid`, mas definidas em filhos do `Grid`. `Grid` define quatro propriedades vinculáveis com conexão estática:

- [`RowProperty`](xref:Xamarin.Forms.Grid.RowProperty) &mdash; linha com base em zero; o padrão é 0
- [`ColumnProperty`](xref:Xamarin.Forms.Grid.ColumnProperty) &mdash; coluna de base zero; o padrão é 0
- [`RowSpanProperty`](xref:Xamarin.Forms.Grid.RowSpanProperty) &mdash; o número de linhas que o filho abrange; o padrão é 1
- [`ColumnSpanProperty`](xref:Xamarin.Forms.Grid.ColumnSpanProperty) &mdash; o número de colunas que o filho abrange; o padrão é 1

No código, um programa pode usar oito métodos estáticos para definir e obter esses valores:

- [`Grid.SetRow`](xref:Xamarin.Forms.Grid.SetRow(Xamarin.Forms.BindableObject,System.Int32)) e [`Grid.GetRow`](xref:Xamarin.Forms.Grid.GetRow(Xamarin.Forms.BindableObject))
- [`Grid.SetColumn`](xref:Xamarin.Forms.Grid.SetColumn(Xamarin.Forms.BindableObject,System.Int32)) e [`Grid.GetColumn`](xref:Xamarin.Forms.Grid.GetColumn(Xamarin.Forms.BindableObject))
- [`Grid.SetRowSpan`](xref:Xamarin.Forms.Grid.SetRowSpan(Xamarin.Forms.BindableObject,System.Int32)) e [`Grid.GetRowSpan`](xref:Xamarin.Forms.Grid.GetRowSpan(Xamarin.Forms.BindableObject))
- [`Grid.SetColumnSpan`](xref:Xamarin.Forms.Grid.SetColumnSpan(Xamarin.Forms.BindableObject,System.Int32)) e [`Grid.GetColumnSpan`](xref:Xamarin.Forms.Grid.GetColumnSpan(Xamarin.Forms.BindableObject))

No XAML você usa os seguintes atributos para definir esses valores:

- `Grid.Row`
- `Grid.Column`
- `Grid.RowSpan`
- `Grid.ColumnSpan`

O exemplo [**SimpleGridDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/SimpleGridDemo) demonstra como criar e inicializar um `Grid` em XAML.

O `Grid` herda a propriedade [`Padding`](xref:Xamarin.Forms.Layout.Padding) de `Layout` e define duas propriedades adicionais que fornecem o espaçamento entre as linhas e colunas:

- [`RowSpacing`](xref:Xamarin.Forms.Grid.RowSpacing) tem um valor padrão de 6
- [`ColumnSpacing`](xref:Xamarin.Forms.Grid.ColumnSpacing) tem um valor padrão de 6

As coleções `RowDefinitions` e `ColumnDefinitions` não são estritamente obrigatórias. Se estiver ausente, o `Grid` criará linhas e colunas para o `Grid` filhos e dará a eles todos um `GridLength` padrão de "\*" (estrela).

### <a name="the-grid-in-code"></a>A grade no código

O exemplo [**GridCodeDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/GridCodeDemo) demonstra como criar e popular um `Grid` no código. Você pode definir as propriedades anexadas para cada filho diretamente ou indiretamente chamando os métodos de `Add` adicionais, como [`Add`](xref:Xamarin.Forms.Grid.IGridList`1.Add*) definidos pela interface de [<T>Grid. IGridList](xref:Xamarin.Forms.Grid.IGridList`1) .

### <a name="the-grid-bar-chart"></a>O gráfico de barras da grade

O exemplo [**GridBarChart**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/GridBarChart) mostra como adicionar vários elementos de `BoxView` a um `Grid` usando o método de [`AddHorizontal`](xref:Xamarin.Forms.Grid.IGridList`1.AddHorizontal*) em massa. Por padrão, esses elementos de `BoxView` têm largura igual. A altura de cada `BoxView` pode ser controlada para se parecer com um gráfico de barras.

O `Grid` no exemplo **GridBarChart** compartilha um `AbsoluteLayout` pai com um `Frame`invisível inicialmente. O programa também define um `TapGestureRecognizer` em cada `BoxView` para usar o `Frame` para exibir informações sobre a barra de tocadas.

### <a name="alignment-in-the-grid"></a>Alinhamento da grade

O exemplo [**GridAlignment**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/GridAlignment) demonstra como usar as propriedades `VerticalOptions` e `HorizontalOptions` para alinhar filhos em uma célula `Grid`.

O exemplo de [**SpacingButtons**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/SpacingButtons) igualmente espaços `Button` elementos centralizados em `Grid` células.

### <a name="cell-dividers-and-borders"></a>Divisores de célula e bordas

O `Grid` não inclui um recurso que desenha divisórias ou bordas de células. No entanto, você pode criar seus próprios.

O [**GridCellDividers**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/GridCellDividers) demonstra como definir linhas e colunas adicionais especificamente para elementos de `BoxView` fino para imitar linhas divisórias.

O programa [**GridCellBorders**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/GridCellBorders) não cria nenhuma célula adicional, mas alinha `BoxView` elementos em cada célula para imitar uma borda de célula.

## <a name="almost-real-life-grid-examples"></a>Exemplos de grade quase reais

O exemplo [**KeypadGrid**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/KeypadGrid) usa um `Grid` para exibir um teclado:

[![Captura de tela tripla de grade de teclado](images/ch17fg12-small.png "Grade do teclado")](images/ch17fg12-large.png#lightbox "Grade do teclado")

### <a name="responding-to-orientation-changes"></a>Respondendo a alterações de orientação

O `Grid` pode ajudar a estruturar um programa para responder às alterações de orientação. O exemplo [**GridRgbSliders**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/GridRgbSliders) demonstra uma técnica que move um elemento entre uma segunda linha de um telefone orientado por retrato e a segunda coluna de um telefone orientado a paisagem.

O programa Inicializa `Slider` elementos em um intervalo de 0 a 255 e usa associações de dados para exibir o valor dos controles deslizantes em hexadecimal. Como os valores de `Slider` são de ponto flutuante e a cadeia de caracteres de formatação .NET para hexadecimal funciona apenas com números inteiros, uma classe [`DoubleToIntConvert`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/DoubleToIntConverter.cs) na biblioteca [**Xamarin. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) ajuda.

## <a name="related-links"></a>Links relacionados

- [Capítulo 17 texto completo (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch17-Apr2016.pdf)
- [Capítulo 17 amostras](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17)
- [Grade](~/xamarin-forms/user-interface/layouts/grid.md)
