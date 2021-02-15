---
title: Resumo do capítulo 17. Domínio da grade
description: 'Criando aplicativos móveis com Xamarin.Forms : Resumo do capítulo 17. Domínio da grade'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 71EDEF9C-4220-4D2E-A235-43F1EC8746C1
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: d2904270b601b6602457873e8b0180c13a0d6d95
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93373452"
---
# <a name="summary-of-chapter-17-mastering-the-grid"></a>Resumo do capítulo 17. Domínio da grade

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17)

> [!NOTE]
> Este livro foi publicado na Primavera de 2016 e não foi atualizado desde então. Há muito no livro que permanece valioso, mas alguns materiais estão desatualizados e alguns tópicos não estão mais totalmente corretos ou completos.

O [`Grid`](xref:Xamarin.Forms.Grid) é um mecanismo de layout poderoso que organiza seus filhos em linhas e colunas de células. Diferentemente do elemento HTML semelhante `table` , o `Grid` é exclusivamente para fins de layout em vez de apresentação.

## <a name="the-basic-grid"></a>A grade básica

`Grid` deriva de [`Layout<View>`](xref:Xamarin.Forms.Layout`1) , que define uma [`Children`](xref:Xamarin.Forms.Layout`1.Children) propriedade que `Grid` herda. Você pode preencher essa coleção em XAML ou código.

### <a name="the-grid-in-xaml"></a>A grade em XAML

A definição de um `Grid` em XAML geralmente começa com o preenchimento [`RowDefinitions`](xref:Xamarin.Forms.Grid.RowDefinitions) das [`ColumnDefinitions`](xref:Xamarin.Forms.Grid.ColumnDefinitions) coleções e dos `Grid` [`RowDefinition`](xref:Xamarin.Forms.RowDefinition) objetos with [`ColumnDefinition`](xref:Xamarin.Forms.ColumnDefinition) . É assim que você estabelece o número de linhas e colunas do `Grid` e suas propriedades.

`RowDefinition` tem uma [`Height`](xref:Xamarin.Forms.RowDefinition.Height) propriedade e `ColumnDefinition` tem uma [`Width`](xref:Xamarin.Forms.ColumnDefinition.Width) propriedade, ambas de tipo [`GridLength`](xref:Xamarin.Forms.GridLength) , uma estrutura.

Em XAML, o [`GridLengthTypeConverter`](xref:Xamarin.Forms.GridLengthTypeConverter) converte cadeias de caracteres de texto simples em `GridLength` valores. Nos bastidores, o [ `GridLength` constructor] (xref: Xamarin.Forms . GridLength .% 23ctor (System. Double, Xamarin.Forms . GridUnitType)) cria o `GridLength` valor com base em um número e um valor do tipo [`GridUnitType`](xref:Xamarin.Forms.GridUnitType) , uma enumeração com três membros:

- [`Absolute`](xref:Xamarin.Forms.GridUnitType.Absolute)&mdash;a largura ou a altura é especificada em unidades independentes do dispositivo (um número em XAML)
- [`Auto`](xref:Xamarin.Forms.GridUnitType.Auto)&mdash;a altura ou a largura é autodimensionada com base no conteúdo da célula ("auto" em XAML)
- [`Star`](xref:Xamarin.Forms.GridUnitType.Star)&mdash;a altura ou largura de sobra é alocada proporcionalmente (um número com " \* ", chamado *estrela* , em XAML)

Cada filho de `Grid` também deve ser atribuído a uma linha e coluna (explícita ou implicitamente). As extensões de linha e as spans de coluna são opcionais. Todos eles são especificados usando propriedades de propriedades vinculáveis anexadas &mdash; que são definidas pelo `Grid` , mas definidas em filhos do `Grid` . `Grid` define quatro propriedades vinculáveis de conexão estática:

- [`RowProperty`](xref:Xamarin.Forms.Grid.RowProperty)&mdash;a linha com base em zero; o padrão é 0
- [`ColumnProperty`](xref:Xamarin.Forms.Grid.ColumnProperty)&mdash;a coluna de base zero; o padrão é 0
- [`RowSpanProperty`](xref:Xamarin.Forms.Grid.RowSpanProperty)&mdash;o número de linhas que o filho abrange; o padrão é 1
- [`ColumnSpanProperty`](xref:Xamarin.Forms.Grid.ColumnSpanProperty)&mdash;o número de colunas que o filho abrange; o padrão é 1

No código, um programa pode usar oito métodos estáticos para definir e obter esses valores:

- [ `Grid.SetRow` ] (xref: Xamarin.Forms . Grid. SetRow ( Xamarin.Forms . Vinculobject, System. Int32) e [ `Grid.GetRow` ] (xref: Xamarin.Forms . Grid. GetRow ( Xamarin.Forms . Bindobject))
- [ `Grid.SetColumn` ] (xref: Xamarin.Forms . Grid. SetColumn ( Xamarin.Forms . Vinculobject, System. Int32) e [ `Grid.GetColumn` ] (xref: Xamarin.Forms . Grid. GetColumn ( Xamarin.Forms . Bindobject))
- [ `Grid.SetRowSpan` ] (xref: Xamarin.Forms . Grid. SetRowSpan ( Xamarin.Forms . Vinculobject, System. Int32) e [ `Grid.GetRowSpan` ] (xref: Xamarin.Forms . Grid. GetRowSpan ( Xamarin.Forms . Bindobject))
- [ `Grid.SetColumnSpan` ] (xref: Xamarin.Forms . Grid. SetColumnSpan ( Xamarin.Forms . Vinculobject, System. Int32) e [ `Grid.GetColumnSpan` ] (xref: Xamarin.Forms . Grid. GetColumnSpan ( Xamarin.Forms . Bindobject))

Em XAML, você usa os seguintes atributos para definir esses valores:

- `Grid.Row`
- `Grid.Column`
- `Grid.RowSpan`
- `Grid.ColumnSpan`

O exemplo [**SimpleGridDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/SimpleGridDemo) demonstra como criar e inicializar um `Grid` em XAML.

O `Grid` herda a [`Padding`](xref:Xamarin.Forms.Layout.Padding) propriedade de `Layout` e define duas propriedades adicionais que fornecem o espaçamento entre as linhas e colunas:

- [`RowSpacing`](xref:Xamarin.Forms.Grid.RowSpacing) tem um valor padrão de 6
- [`ColumnSpacing`](xref:Xamarin.Forms.Grid.ColumnSpacing) tem um valor padrão de 6

As `RowDefinitions` `ColumnDefinitions` coleções e não são estritamente obrigatórias. Se estiver ausente, o `Grid` criará linhas e colunas para os `Grid` filhos e dará a eles todos um padrão `GridLength` de " \* " (estrela).

### <a name="the-grid-in-code"></a>A grade no código

O exemplo [**GridCodeDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/GridCodeDemo) demonstra como criar e popular um `Grid` código in. Você pode definir as propriedades anexadas para cada filho diretamente ou indiretamente chamando `Add` métodos adicionais, como [`Add`](xref:Xamarin.Forms.Grid.IGridList`1.Add*) definido pela interface [Grid. IGridList <T> ](xref:Xamarin.Forms.Grid.IGridList`1) .

### <a name="the-grid-bar-chart"></a>O gráfico de barras de grade

O exemplo [**GridBarChart**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/GridBarChart) mostra como adicionar vários `BoxView` elementos a um `Grid` usando o método Bulk [`AddHorizontal`](xref:Xamarin.Forms.Grid.IGridList`1.AddHorizontal*) . Por padrão, esses `BoxView` elementos têm largura igual. A altura de cada `BoxView` uma pode ser controlada para se parecer com um gráfico de barras.

O `Grid` no exemplo **GridBarChart** compartilha um `AbsoluteLayout` pai com um inicialmente invisível `Frame` . O programa também define um `TapGestureRecognizer` em cada `BoxView` para usar o `Frame` para exibir informações sobre a barra de tocadas.

### <a name="alignment-in-the-grid"></a>Alinhamento na grade

O exemplo [**GridAlignment**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/GridAlignment) demonstra como usar as `VerticalOptions` Propriedades e `HorizontalOptions` para alinhar os filhos em uma `Grid` célula.

Os elementos de exemplo de [**SpacingButtons**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/SpacingButtons) de espaço uniforme `Button` centralizados em `Grid` células.

### <a name="cell-dividers-and-borders"></a>Divisórias e bordas de célula

O `Grid` não inclui um recurso que desenha divisórias ou bordas de células. No entanto, você pode fazer o seu próprio.

O [**GridCellDividers**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/GridCellDividers) demonstra como definir linhas e colunas adicionais especificamente para elementos finos `BoxView` para imitar linhas de divisão.

O programa [**GridCellBorders**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/GridCellBorders) não cria nenhuma célula adicional, mas alinha os `BoxView` elementos em cada célula para imitar uma borda de célula.

## <a name="almost-real-life-grid-examples"></a>Exemplos de grade da vida quase real

O exemplo [**KeypadGrid**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/KeypadGrid) usa um `Grid` para exibir um teclado:

[![Captura de tela tripla de grade de teclado](images/ch17fg12-small.png "Grade do teclado")](images/ch17fg12-large.png#lightbox "Grade do teclado")

### <a name="responding-to-orientation-changes"></a>Respondendo às alterações de orientação

O `Grid` pode ajudar a estruturar um programa para responder às alterações de orientação. O exemplo [**GridRgbSliders**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/GridRgbSliders) demonstra uma técnica que move um elemento entre uma segunda linha de um telefone orientado por retrato e a segunda coluna de um telefone orientado a paisagem.

O programa Inicializa os `Slider` elementos em um intervalo de 0 a 255 e usa as ligações de dados para exibir o valor dos controles deslizantes em hexadecimal. Como os `Slider` valores são de ponto flutuante e a cadeia de caracteres de formatação .net para hexadecimal funciona apenas com números inteiros, uma [`DoubleToIntConvert`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/DoubleToIntConverter.cs) classe na biblioteca [**Xamarin.Forms book. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) ajuda.

## <a name="related-links"></a>Links relacionados

- [Capítulo 17 texto completo (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch17-Apr2016.pdf)
- [Capítulo 17 amostras](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17)
- [Grid](~/xamarin-forms/user-interface/layouts/grid.md)
