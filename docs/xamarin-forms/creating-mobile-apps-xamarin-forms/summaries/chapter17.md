---
title: Resumo do capítulo 17. Controle de grade
description: 'Criando aplicativos móveis com o xamarin. Forms: Resumo do capítulo 17. Controle de grade'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 71EDEF9C-4220-4D2E-A235-43F1EC8746C1
author: charlespetzold
ms.author: chape
ms.date: 11/07/2017
ms.openlocfilehash: 55418f24b1519dcab3b107f23976d50b401c813b
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/08/2018
ms.locfileid: "35240942"
---
# <a name="summary-of-chapter-17-mastering-the-grid"></a>Resumo do capítulo 17. Controle de grade

O [ `Grid` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Grid/) é um mecanismo de layout eficiente que organiza seus filhos em linhas e colunas de células. Ao contrário de HTML semelhante `table` elemento, o `Grid` é somente para fins de layout em vez de apresentação.

## <a name="the-basic-grid"></a>A grade básica

`Grid` deriva [ `Layout<View>` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Layout%3CT%3E/), que define uma [ `Children` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Layout%3CT%3E.Children/) propriedade que `Grid` herda. Você pode preencher essa coleção em XAML ou código.

### <a name="the-grid-in-xaml"></a>A grade em XAML

A definição de um `Grid` em XAML geralmente começa com preenchimento de [ `RowDefinitions` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Grid.RowDefinitions/) e [ `ColumnDefinitions` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Grid.ColumnDefinitions/) coleções do `Grid` com [ `RowDefinition` ](https://developer.xamarin.com/api/type/Xamarin.Forms.RowDefinition/) e [ `ColumnDefinition` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ColumnDefinition/) objetos. Isso é como você estabelece o número de linhas e colunas do `Grid`e suas propriedades.

`RowDefinition` tem um [ `Height` ](https://developer.xamarin.com/api/property/Xamarin.Forms.RowDefinition.Height/) propriedade e `ColumnDefinition` tem um [ `Width` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ColumnDefinition.Width/) propriedade do tipo [ `GridLength` ](https://developer.xamarin.com/api/type/Xamarin.Forms.GridLength/), uma estrutura.

Em XAML, o [ `GridLengthTypeConverter` ](https://developer.xamarin.com/api/type/Xamarin.Forms.GridLengthTypeConverter/) converte cadeias de caracteres de texto simples em `GridLength` valores. Nos bastidores, o [ `GridLength` construtor](https://developer.xamarin.com/api/constructor/Xamarin.Forms.GridLength.GridLength/p/System.Double/Xamarin.Forms.GridUnitType/) cria o `GridLength` valor com base em um número e um valor do tipo [ `GridUnitType` ](https://developer.xamarin.com/api/type/Xamarin.Forms.GridUnitType/), uma enumeração com três membros:

- [`Absolute`](https://developer.xamarin.com/api/field/Xamarin.Forms.GridUnitType.Absolute/) &mdash; a largura ou altura é especificada em unidades independentes de dispositivo (um número em XAML)
- [`Auto`](https://developer.xamarin.com/api/field/Xamarin.Forms.GridUnitType.Auto/) &mdash; a altura ou largura é dimensionado automaticamente com base no conteúdo da célula ("Auto" em XAML)
- [`Star`](https://developer.xamarin.com/api/field/Xamarin.Forms.GridUnitType.Star/) &mdash; sobra altura ou largura é alocada proporcionalmente (um número com "\*", chamado *estrela*, em XAML)

Cada filho do `Grid` também é necessário atribuir uma linha e coluna (explícita ou implicitamente). Abrange de linha e coluna intervalos são opcionais. São todas especificadas usando as propriedades vinculáveis anexadas &mdash; propriedades que são definidas pelo `Grid` mas definidas em filhos a `Grid`. `Grid` define quatro propriedades estáticas de associáveis anexadas:

- [`RowProperty`](https://developer.xamarin.com/api/field/Xamarin.Forms.Grid.RowProperty/) &mdash; a linha de base zero. o padrão é 0
- [`ColumnProperty`](https://developer.xamarin.com/api/field/Xamarin.Forms.Grid.ColumnProperty/) &mdash; a coluna com base em zero; o padrão é 0
- [`RowSpanProperty`](https://developer.xamarin.com/api/field/Xamarin.Forms.Grid.RowSpanProperty/) &mdash; o número de linhas filho abrange; o padrão é 1
- [`ColumnSpanProperty`](https://developer.xamarin.com/api/field/Xamarin.Forms.Grid.ColumnSpanProperty/) &mdash; o número de colunas filho abrange; o padrão é 1

No código, um programa pode usar oito métodos estáticos para definir e obter estes valores:

- [`Grid.SetRow`](https://developer.xamarin.com/api/member/Xamarin.Forms.Grid.SetRow/p/Xamarin.Forms.BindableObject/System.Int32/) e [`Grid.GetRow`](https://developer.xamarin.com/api/member/Xamarin.Forms.Grid.GetRow/p/Xamarin.Forms.BindableObject/)
- [`Grid.SetColumn`](https://developer.xamarin.com/api/member/Xamarin.Forms.Grid.SetColumn/p/Xamarin.Forms.BindableObject/System.Int32/) e [`Grid.GetColumn`](https://developer.xamarin.com/api/member/Xamarin.Forms.Grid.GetColumn/p/Xamarin.Forms.BindableObject/)
- [`Grid.SetRowSpan`](https://developer.xamarin.com/api/member/Xamarin.Forms.Grid.SetRowSpan/p/Xamarin.Forms.BindableObject/System.Int32/) e [`Grid.GetRowSpan`](https://developer.xamarin.com/api/member/Xamarin.Forms.Grid.GetRowSpan/p/Xamarin.Forms.BindableObject/)
- [`Grid.SetColumnSpan`](https://developer.xamarin.com/api/member/Xamarin.Forms.Grid.SetColumnSpan/p/Xamarin.Forms.BindableObject/System.Int32/) e [`Grid.GetColumnSpan`](https://developer.xamarin.com/api/member/Xamarin.Forms.Grid.GetColumnSpan/p/Xamarin.Forms.BindableObject/)

No XAML, você usa os seguintes atributos para definir esses valores:

- `Grid.Row`
- `Grid.Column`
- `Grid.RowSpan`
- `Grid.ColumnSpan`

O [ **SimpleGridDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/SimpleGridDemo) exemplo demonstra como criar e inicializar um `Grid` em XAML.

O `Grid` herda o [ `Padding` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Layout.Padding/) propriedade `Layout` e define duas propriedades adicionais que fornecem o espaçamento entre as linhas e colunas:

- [`RowSpacing`](https://developer.xamarin.com/api/property/Xamarin.Forms.Grid.RowSpacing/) tem um valor padrão de 6
- [`ColumnSpacing`](https://developer.xamarin.com/api/property/Xamarin.Forms.Grid.ColumnSpacing/) tem um valor padrão de 6

O `RowDefinitions` e `ColumnDefinitions` coleções não são estritamente necessárias. Caso esteja ausente, o `Grid` cria linhas e colunas para o `Grid` filhos e fornece um padrão a todos eles `GridLength` de "\*" (estrela).

### <a name="the-grid-in-code"></a>A grade no código

O [ **GridCodeDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/GridCodeDemo) demonstra como criar e popular um `Grid` no código. Você pode definir as propriedades anexadas para cada filho diretamente ou indiretamente chamando adicionais `Add` métodos como [ `Add` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Grid+IGridList%3CT%3E.Add/p/Xamarin.Forms.View/System.Int32/System.Int32/System.Int32/System.Int32/) definido pelo [Grid.IGridList<T> ](https://developer.xamarin.com/api/type/Xamarin.Forms.Grid+IGridList%3CT%3E/) interface.

### <a name="the-grid-bar-chart"></a>O gráfico de barras da grade

O [ **GridBarChart** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/GridBarChart) exemplo mostra como adicionar vários `BoxView` elementos para um `Grid` usando o bulk [ `AddHorizontal` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Grid+IGridList%3CT%3E.AddHorizontal/p/System.Collections.Generic.IEnumerable%7BXamarin.Forms.View%7D/) método. Por padrão, esses `BoxView` elementos têm a mesma largura. A altura de cada `BoxView` , em seguida, pode ser controlado para se parecer com um gráfico de barras.

O `Grid` no **GridBarChart** compartilhamentos de exemplo um `AbsoluteLayout` pai com inicialmente invisível `Frame`. O programa também define uma `TapGestureRecognizer` em cada `BoxView` para usar o `Frame` para exibir informações sobre a barra tocada.

### <a name="alignment-in-the-grid"></a>Alinhamento da grade

O [ **GridAlignment** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/GridAlignment) demonstra como usar o `VerticalOptions` e `HorizontalOptions` propriedades para alinhar filhos em um `Grid` célula.

O [ **SpacingButtons** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/SpacingButtons) exemplo igualmente espaços `Button` elementos centralizado em `Grid` células.

### <a name="cell-dividers-and-borders"></a>Bordas e divisores de célula

O `Grid` não inclui um recurso que desenha divisores de célula ou bordas. No entanto, você pode criar seus próprios.

O [ **GridCellDividers** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/GridCellDividers) demonstra como definir linhas adicionais e coluna especificamente para thin `BoxView` elementos para simular a linhas de divisão.

O [ **GridCellBorders** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/GridCellBorders) programa não cria todas as células adicionais, mas em vez disso, se alinha `BoxView` elementos em cada célula para simular uma borda de célula.

## <a name="almost-real-life-grid-examples"></a>Exemplos de grade quase reais

O [ **KeypadGrid** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/KeypadGrid) usa um `Grid` para exibir um teclado numérico:

[![Captura de tela tripla da grade de teclado](images/ch17fg12-small.png "teclado grade")](images/ch17fg12-large.png#lightbox "grade de teclado")

### <a name="responding-to-orientation-changes"></a>Responder a alterações de orientação

O `Grid` pode ajudar a estrutura de um programa para responder às alterações de orientação. O [ **GridRgbSliders** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/GridRgbSliders) demonstra uma técnica que move um elemento entre uma segunda linha de um telefone orientadas por retrato e a segunda coluna de um telefone com orientação paisagem.

Inicializa o programa `Slider` elementos para um intervalo de 0 a 255 e associações de dados usa para exibir o valor dos controles deslizantes em hexadecimal. Porque o `Slider` valores flutuantes ponto e o .NET cadeia de caracteres de formatação para hexadecimal funciona apenas com números inteiros, um [ `DoubleToIntConvert` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/DoubleToIntConverter.cs) classe no [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) biblioteca de Ajuda.



## <a name="related-links"></a>Links relacionados

- [17 de capítulo de texto completo (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch17-Apr2016.pdf)
- [Exemplos de capítulo 17](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17)
- [Grade](~/xamarin-forms/user-interface/layouts/grid.md)
