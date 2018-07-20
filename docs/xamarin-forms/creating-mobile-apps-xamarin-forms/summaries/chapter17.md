---
title: Resumo do capítulo 17. Domínio da grade
description: 'Criação de aplicativos móveis com xamarin. Forms: Resumo do capítulo 17. Domínio da grade'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 71EDEF9C-4220-4D2E-A235-43F1EC8746C1
author: charlespetzold
ms.author: chape
ms.date: 11/07/2017
ms.openlocfilehash: 4ce734f629c13b1419611c0a8fea1dfe4bbaa4ef
ms.sourcegitcommit: 8555a4dd1a579b2206f86c867125ee20fbc3d264
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/19/2018
ms.locfileid: "39156737"
---
# <a name="summary-of-chapter-17-mastering-the-grid"></a>Resumo do capítulo 17. Domínio da grade

O [ `Grid` ](xref:Xamarin.Forms.Grid) é um mecanismo de layout avançados que organiza seus filhos em linhas e colunas de células. Ao contrário do HTML semelhante `table` elemento, o `Grid` é exclusivamente para fins de layout em vez de apresentação.

## <a name="the-basic-grid"></a>A grade básica

`Grid` deriva [ `Layout<View>` ](xref:Xamarin.Forms.Layout`1), que define uma [ `Children` ](xref:Xamarin.Forms.Layout`1.Children) propriedade que `Grid` herda. Você pode preencher essa coleção em XAML ou código.

### <a name="the-grid-in-xaml"></a>A grade em XAML

A definição de um `Grid` no XAML geralmente começa com preenchimento a [ `RowDefinitions` ](xref:Xamarin.Forms.Grid.RowDefinitions) e [ `ColumnDefinitions` ](xref:Xamarin.Forms.Grid.ColumnDefinitions) coleções do `Grid` com [ `RowDefinition` ](xref:Xamarin.Forms.RowDefinition) e [ `ColumnDefinition` ](xref:Xamarin.Forms.ColumnDefinition) objetos. Isso é como você o número de linhas e colunas de estabelece o `Grid`e suas propriedades.

`RowDefinition` tem um [ `Height` ](xref:Xamarin.Forms.RowDefinition.Height) propriedade e `ColumnDefinition` tem um [ `Width` ](xref:Xamarin.Forms.ColumnDefinition.Width) propriedade, ambos do tipo [ `GridLength` ](xref:Xamarin.Forms.GridLength), uma estrutura.

No XAML, o [ `GridLengthTypeConverter` ](xref:Xamarin.Forms.GridLengthTypeConverter) converte cadeias de caracteres de texto simples em `GridLength` valores. Nos bastidores, o [ `GridLength` construtor](xref:Xamarin.Forms.GridLength.%23ctor(System.Double,Xamarin.Forms.GridUnitType)) cria o `GridLength` valor com base em um número e um valor do tipo [ `GridUnitType` ](xref:Xamarin.Forms.GridUnitType), uma enumeração com três membros:

- [`Absolute`](xref:Xamarin.Forms.GridUnitType.Absolute) &mdash; a largura ou altura for especificada em unidades independentes de dispositivo (um número em XAML)
- [`Auto`](xref:Xamarin.Forms.GridUnitType.Auto) &mdash; a altura ou largura é dimensionado automaticamente com base no conteúdo da célula ("Auto" em XAML)
- [`Star`](xref:Xamarin.Forms.GridUnitType.Star) &mdash; largura ou altura sobra é alocada proporcionalmente (um número com "\*", chamado *estrela*, em XAML)

Cada filho do `Grid` também deve ser atribuído uma linha e coluna (explícita ou implicitamente). Se estende por linha e coluna spans são opcionais. Essas são todas especificadas usando propriedades vinculáveis anexadas &mdash; as propriedades que são definidas pela `Grid` mas definidas nos filhos do `Grid`. `Grid` define quatro propriedades estáticas de associáveis anexadas:

- [`RowProperty`](xref:Xamarin.Forms.Grid.RowProperty) &mdash; a linha de base zero; o padrão é 0
- [`ColumnProperty`](xref:Xamarin.Forms.Grid.ColumnProperty) &mdash; a coluna com base em zero; o padrão é 0
- [`RowSpanProperty`](xref:Xamarin.Forms.Grid.RowSpanProperty) &mdash; o número de linhas filho abrange; o padrão é 1
- [`ColumnSpanProperty`](xref:Xamarin.Forms.Grid.ColumnSpanProperty) &mdash; o número de colunas filho abrange; o padrão é 1

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

O [ **SimpleGridDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/SimpleGridDemo) exemplo demonstra como criar e inicializar um `Grid` em XAML.

O `Grid` herda a [ `Padding` ](xref:Xamarin.Forms.Layout.Padding) propriedade do `Layout` e define duas propriedades adicionais que fornecem o espaçamento entre linhas e colunas:

- [`RowSpacing`](xref:Xamarin.Forms.Grid.RowSpacing) tem um valor padrão de 6
- [`ColumnSpacing`](xref:Xamarin.Forms.Grid.ColumnSpacing) tem um valor padrão de 6

O `RowDefinitions` e `ColumnDefinitions` coleções não são estritamente necessárias. Caso esteja ausente, o `Grid` cria linhas e colunas para o `Grid` filhos e fornece um padrão a todos eles `GridLength` de "\*" (star).

### <a name="the-grid-in-code"></a>A grade no código

O [ **GridCodeDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/GridCodeDemo) exemplo demonstra como criar e popular um `Grid` no código. Você pode definir as propriedades anexadas para cada filho diretamente ou indiretamente por meio da chamada adicional `Add` métodos como [ `Add` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Grid+IGridList%3CT%3E.Add/p/Xamarin.Forms.View/System.Int32/System.Int32/System.Int32/System.Int32/) definidas pelo [Grid.IGridList<T> ](https://developer.xamarin.com/api/type/Xamarin.Forms.Grid+IGridList%3CT%3E/) interface.

### <a name="the-grid-bar-chart"></a>O gráfico de barras da grade

O [ **GridBarChart** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/GridBarChart) exemplo mostra como adicionar vários `BoxView` elementos a um `Grid` usando o bulk [ `AddHorizontal` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Grid+IGridList%3CT%3E.AddHorizontal/p/System.Collections.Generic.IEnumerable%7BXamarin.Forms.View%7D/) método. Por padrão, esses `BoxView` elementos têm a mesma largura. A altura de cada `BoxView` , em seguida, pode ser controlado para se parecer com um gráfico de barras.

O `Grid` no **GridBarChart** compartilhamentos de exemplo uma `AbsoluteLayout` pai com inicialmente invisível `Frame`. O programa também define uma `TapGestureRecognizer` em cada `BoxView` usar o `Frame` para exibir informações sobre a barra tocada.

### <a name="alignment-in-the-grid"></a>Alinhamento da grade

O [ **GridAlignment** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/GridAlignment) exemplo demonstra como usar o `VerticalOptions` e `HorizontalOptions` propriedades para alinhar filhos em um `Grid` célula.

O [ **SpacingButtons** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/SpacingButtons) exemplo igualmente espaços `Button` elementos centralizado em `Grid` células.

### <a name="cell-dividers-and-borders"></a>Divisores de célula e bordas

O `Grid` não inclui um recurso que desenha os divisores de célula ou bordas. No entanto, você pode criar seus próprios.

O [ **GridCellDividers** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/GridCellDividers) demonstra como definir linhas adicionais e na coluna especificamente para thin `BoxView` elementos para imitar linhas divisórias.

O [ **GridCellBorders** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/GridCellBorders) programa não cria nenhuma célula adicional, mas em vez disso, se alinha `BoxView` elementos em cada célula para simular uma borda da célula.

## <a name="almost-real-life-grid-examples"></a>Exemplos de grade quase reais

O [ **KeypadGrid** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/KeypadGrid) de exemplo usa um `Grid` para exibir um teclado numérico:

[![Captura de tela tripla da grade de teclado](images/ch17fg12-small.png "grade do teclado")](images/ch17fg12-large.png#lightbox "grade de teclado")

### <a name="responding-to-orientation-changes"></a>Respondendo a alterações de orientação

O `Grid` pode ajudar a estruturar um programa para responder a alterações de orientação. O [ **GridRgbSliders** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/GridRgbSliders) exemplo demonstra uma técnica que move um elemento entre uma segunda linha de um telefone e orientada a retrato e a segunda coluna de um telefone com orientação paisagem.

Inicializa o programa `Slider` elementos a um intervalo de 0 a 255 e ligações de dados usa para exibir o valor dos controles deslizantes em hexadecimal. Porque o `Slider` valores flutuantes, ponto e o .NET a cadeia de caracteres de formatação para hexadecimal funciona somente com números inteiros, uma [ `DoubleToIntConvert` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/DoubleToIntConverter.cs) classe o [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) biblioteca de Ajuda.



## <a name="related-links"></a>Links relacionados

- [Capítulo 17 de texto completo (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch17-Apr2016.pdf)
- [Exemplos do capítulo 17](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17)
- [Grade](~/xamarin-forms/user-interface/layouts/grid.md)
