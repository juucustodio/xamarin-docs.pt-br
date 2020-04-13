---
title: Resumo do Capítulo 17. Domínio da grade
description: 'Criando aplicativos móveis com Xamarin.Forms: Resumo do Capítulo 17. Domínio da grade'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 71EDEF9C-4220-4D2E-A235-43F1EC8746C1
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2017
ms.openlocfilehash: 37b5e2bbafa816de27390771ae6daa33c74f7651
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "70760635"
---
# <a name="summary-of-chapter-17-mastering-the-grid"></a>Resumo do Capítulo 17. Domínio da grade

[![Baixar](~/media/shared/download.png) amostra Baixar a amostra](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17)

O [`Grid`](xref:Xamarin.Forms.Grid) é um poderoso mecanismo de layout que organiza seus filhos em fileiras e colunas de células. Ao contrário `table` do elemento `Grid` HTML semelhante, o é apenas para fins de layout em vez de apresentação.

## <a name="the-basic-grid"></a>A grade básica

`Grid`deriva de [`Layout<View>`](xref:Xamarin.Forms.Layout`1), que [`Children`](xref:Xamarin.Forms.Layout`1.Children) define `Grid` uma propriedade que herda. Você pode preencher esta coleção em XAML ou código.

### <a name="the-grid-in-xaml"></a>A grade em XAML

A definição `Grid` de a in XAML [`RowDefinitions`](xref:Xamarin.Forms.Grid.RowDefinitions) geralmente [`ColumnDefinitions`](xref:Xamarin.Forms.Grid.ColumnDefinitions) começa com `Grid` [`RowDefinition`](xref:Xamarin.Forms.RowDefinition) o [`ColumnDefinition`](xref:Xamarin.Forms.ColumnDefinition) preenchimento das coleções e com e objetos. É assim que você estabelece o número de `Grid`linhas e colunas do , e suas propriedades.

`RowDefinition`tem [`Height`](xref:Xamarin.Forms.RowDefinition.Height) uma `ColumnDefinition` propriedade [`Width`](xref:Xamarin.Forms.ColumnDefinition.Width) e tem uma [`GridLength`](xref:Xamarin.Forms.GridLength)propriedade, tanto de tipo, uma estrutura.

Em XAML, [`GridLengthTypeConverter`](xref:Xamarin.Forms.GridLengthTypeConverter) o converte strings de texto simples em `GridLength` valores. Nos bastidores, [ `GridLength` ](xref:Xamarin.Forms.GridLength.%23ctor(System.Double,Xamarin.Forms.GridUnitType)) o construtor `GridLength` cria o valor com base [`GridUnitType`](xref:Xamarin.Forms.GridUnitType)em um número e um valor de tipo , uma enumeração com três membros:

- [`Absolute`](xref:Xamarin.Forms.GridUnitType.Absolute)&mdash; a largura ou altura é especificada em unidades independentes do dispositivo (um número em XAML)
- [`Auto`](xref:Xamarin.Forms.GridUnitType.Auto)&mdash; a altura ou largura é autodimensionada com base no conteúdo celular ("Auto" em XAML)
- [`Star`](xref:Xamarin.Forms.GridUnitType.Star)&mdash; a altura ou largura restantes é alocada\*proporcionalmente (um número com " ", chamado *estrela*, em XAML)

Cada filho `Grid` do também deve ser atribuído uma linha e coluna (explicitamente ou implicitamente). Os vãos de linha e os vãos das colunas são opcionais. Todas elas são especificadas usando &mdash; propriedades de propriedades `Grid` vinculadas anexadas `Grid`definidas pelo mas definido em filhos do . `Grid`define quatro propriedades anexáveis ancestão estáticas:

- [`RowProperty`](xref:Xamarin.Forms.Grid.RowProperty)&mdash; a linha baseada em zero; padrão é 0
- [`ColumnProperty`](xref:Xamarin.Forms.Grid.ColumnProperty)&mdash; a coluna baseada em zero; padrão é 0
- [`RowSpanProperty`](xref:Xamarin.Forms.Grid.RowSpanProperty)&mdash; o número de linhas que a criança abrange; padrão é 1
- [`ColumnSpanProperty`](xref:Xamarin.Forms.Grid.ColumnSpanProperty)&mdash; o número de colunas que a criança abrange; padrão é 1

Em código, um programa pode usar oito métodos estáticos para definir e obter esses valores:

- [`Grid.SetRow`](xref:Xamarin.Forms.Grid.SetRow(Xamarin.Forms.BindableObject,System.Int32))E[`Grid.GetRow`](xref:Xamarin.Forms.Grid.GetRow(Xamarin.Forms.BindableObject))
- [`Grid.SetColumn`](xref:Xamarin.Forms.Grid.SetColumn(Xamarin.Forms.BindableObject,System.Int32))E[`Grid.GetColumn`](xref:Xamarin.Forms.Grid.GetColumn(Xamarin.Forms.BindableObject))
- [`Grid.SetRowSpan`](xref:Xamarin.Forms.Grid.SetRowSpan(Xamarin.Forms.BindableObject,System.Int32))E[`Grid.GetRowSpan`](xref:Xamarin.Forms.Grid.GetRowSpan(Xamarin.Forms.BindableObject))
- [`Grid.SetColumnSpan`](xref:Xamarin.Forms.Grid.SetColumnSpan(Xamarin.Forms.BindableObject,System.Int32))E[`Grid.GetColumnSpan`](xref:Xamarin.Forms.Grid.GetColumnSpan(Xamarin.Forms.BindableObject))

No XAML você usa os seguintes atributos para definir esses valores:

- `Grid.Row`
- `Grid.Column`
- `Grid.RowSpan`
- `Grid.ColumnSpan`

A amostra [**SimpleGridDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/SimpleGridDemo) demonstra a `Grid` criação e inicialização de um em XAML.

O `Grid` herda [`Padding`](xref:Xamarin.Forms.Layout.Padding) a `Layout` propriedade e define duas propriedades adicionais que fornecem espaçamento entre as linhas e colunas:

- [`RowSpacing`](xref:Xamarin.Forms.Grid.RowSpacing)tem um valor padrão de 6
- [`ColumnSpacing`](xref:Xamarin.Forms.Grid.ColumnSpacing)tem um valor padrão de 6

As `RowDefinitions` `ColumnDefinitions` coletas não são estritamente necessárias. Se ausente, `Grid` cria linhas e colunas para as crianças e dá a `Grid` todos um padrão `GridLength` de "\*(estrela).

### <a name="the-grid-in-code"></a>A grade em código

A amostra [**GridCodeDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/GridCodeDemo) demonstra como `Grid` criar e preencher um código. Você pode definir as propriedades anexadas para cada filho `Add` diretamente ou [`Add`](xref:Xamarin.Forms.Grid.IGridList`1.Add*) indiretamente, chamando métodos adicionais, como definido pela interface [Grid.IGridList.<T> ](xref:Xamarin.Forms.Grid.IGridList`1)

### <a name="the-grid-bar-chart"></a>Gráfico de barras de Grade

A amostra [**GridBarChart**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/GridBarChart) mostra `BoxView` como adicionar `Grid` vários [`AddHorizontal`](xref:Xamarin.Forms.Grid.IGridList`1.AddHorizontal*) elementos a um método de uso em massa. Por padrão, `BoxView` esses elementos têm largura igual. A altura `BoxView` de cada um pode então ser controlada para se assemelhar a um gráfico de barras.

A `Grid` amostra no **GridBarChart** compartilha um `AbsoluteLayout` `Frame`pai com um inicialmente invisível . O programa também `TapGestureRecognizer` define `BoxView` um `Frame` em cada um para usar as informações para exibir sobre a barra grampeada.

### <a name="alignment-in-the-grid"></a>Alinhamento na grade

A amostra [**GridAlignment**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/GridAlignment) demonstra `VerticalOptions` como `HorizontalOptions` usar as propriedades `Grid` e alinhar as crianças em uma célula.

Os [**Botões de Espaçamento**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/SpacingButtons) amostram elementos igualmente espaços `Button` centrados em `Grid` células.

### <a name="cell-dividers-and-borders"></a>Divisórias e fronteiras de células

O `Grid` não inclui um recurso que desenha divisórias de células ou bordas. No entanto, você pode fazer o seu próprio.

O [**GridCellDividers**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/GridCellDividers) demonstra como definir linhas adicionais `BoxView` e colunaespecificamente para elementos finos para imitar linhas divisórias.

O programa [**GridCellBorders**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/GridCellBorders) não cria células adicionais, mas alinha elementos `BoxView` em cada célula para imitar uma borda celular.

## <a name="almost-real-life-grid-examples"></a>Exemplos de grade quase reais

A amostra [**do KeypadGrid**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/KeypadGrid) usa um `Grid` para exibir um teclado:

[![Captura de tela tripla da grade do teclado](images/ch17fg12-small.png "Grade do teclado")](images/ch17fg12-large.png#lightbox "Grade do teclado")

### <a name="responding-to-orientation-changes"></a>Respondendo a mudanças de orientação

O `Grid` pode ajudar a estruturar um programa para responder a mudanças de orientação. A amostra [**GridRgbSliders**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/GridRgbSliders) demonstra uma técnica que move um elemento entre uma segunda linha de um telefone orientado para retratos e a segunda coluna de um telefone orientado para a paisagem.

O programa inicializa elementos `Slider` para uma faixa de 0 a 255, e usa vinculações de dados para exibir o valor dos controles deslizantes em hexadecimal. Como `Slider` os valores são de ponto flutuante e a seqüência de formatação .NET [`DoubleToIntConvert`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/DoubleToIntConverter.cs) para hexadecimal só funciona com inteiros, uma classe na biblioteca [**Xamarin.FormsBook.Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) ajuda.

## <a name="related-links"></a>Links relacionados

- [Capítulo 17 texto completo (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch17-Apr2016.pdf)
- [Capítulo 17 amostras](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17)
- [Grade](~/xamarin-forms/user-interface/layouts/grid.md)
