---
title: ''
description: ''
Creating Mobile Apps with Xamarin.Forms: Summary of Chapter 14. Absolute layout''
ms.prod: ''
ms.technology: ''
ms.assetid: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 72ee9c4a481388e69aeeb52dbd5b8eeaabb164f6
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/28/2020
ms.locfileid: "84136754"
---
# <a name="summary-of-chapter-14-absolute-layout"></a>Resumo do capítulo 14. Layout absoluto

[![Baixar exemplo ](~/media/shared/download.png) baixar o exemplo](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14)

Como `StackLayout` , [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout) deriva de `Layout<View>` e herda uma `Children` propriedade. `AbsoluteLayout`implementa um sistema de layout que exige que o programador especifique as posições de seus filhos e, opcionalmente, seu tamanho. A posição é especificada pelo canto superior esquerdo do filho em relação ao canto superior esquerdo do `AbsoluteLayout` em unidades independentes de dispositivo. `AbsoluteLayout`também implementa um recurso de posicionamento e dimensionamento proporcional.

`AbsoluteLayout`deve ser considerado um sistema de layout de finalidade especial para ser usado somente quando o programador puder impor um tamanho nos filhos (por exemplo, `BoxView` elementos) ou quando o tamanho do elemento não afetar o posicionamento de outros filhos. As `HorizontalOptions` `VerticalOptions` Propriedades e não têm efeito sobre os filhos de um `AbsoluteLayout` .

Este capítulo também apresenta o recurso importante das *propriedades vinculáveis anexadas* que permitem que as propriedades definidas em uma classe (nesse caso `AbsoluteLayout` ) sejam anexadas a outra classe (um filho do `AbsoluteLayout` ).

## <a name="absolutelayout-in-code"></a>AbsoluteLayout no código

Você pode adicionar um filho à `Children` coleção de um `AbsoluteLayout` usando o método padrão [`Add`](xref:System.Collections.Generic.ICollection`1.Add*) , mas `AbsoluteLayout` também fornece um método estendido [`Add`](xref:Xamarin.Forms.AbsoluteLayout.IAbsoluteList`1.Add*) que permite especificar um [`Rectangle`](xref:Xamarin.Forms.Rectangle) . Outro [`Add`](xref:Xamarin.Forms.AbsoluteLayout.IAbsoluteList`1.Add*) método requer apenas um [`Point`](xref:Xamarin.Forms.Point) , caso em que o filho é irrestrito e tem seu próprio tamanho.

Você pode criar um `Rectangle` valor com um [Construtor](xref:Xamarin.Forms.Rectangle.%23ctor(System.Double,System.Double,System.Double,System.Double)) que exige quatro valores &mdash; os dois primeiros indicando a posição do canto superior esquerdo do filho em relação ao seu pai e os dois que indicam o tamanho do filho. Ou você pode usar um [constructor] (xref: Xamarin.Forms . Retângulo .% 23ctor ( Xamarin.Forms . Ponto, Xamarin.Forms . Tamanho)) que requer um `Point` e um [`Size`](xref:Xamarin.Forms.Size) valor.

Esses `Add` métodos são demonstrados em [**AbsoluteDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/AbsoluteDemo), que posiciona os `BoxView` elementos usando `Rectangle` valores e um `Label` elemento usando apenas um `Point` valor.

O exemplo [**ChessboardFixed**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/ChessboardFixed) usa 32 `BoxView` elementos para criar o padrão xadrez. O programa fornece aos `BoxView` elementos um tamanho embutido em código de 35 unidades. O `AbsoluteLayout` tem seu `HorizontalOptions` e `VerticalOptions` definido como `LayoutOptions.Center` , o que faz com que o `AbsoluteLayout` tenha um tamanho total de 280 unidades quadrado.

## <a name="attached-bindable-properties"></a>Propriedades vinculáveis anexadas

Também é possível definir a posição e, opcionalmente, o tamanho de um filho de um `AbsoluteLayout` após ele ter sido adicionado à `Children` coleção usando o método estático [ `AbsoluteLayout.SetLayoutBounds` ] (xref: Xamarin.Forms . AbsoluteLayout. SetLayoutBounds ( Xamarin.Forms . Vinculobject, Xamarin.Forms . Rectangle)). O primeiro argumento é o filho; o segundo é um `Rectangle` objeto. Você pode especificar que os tamanhos filho são horizontal e/ou verticalmente, definindo valores de largura e altura para a [`AbsoluteLayout.AutoSize`](xref:Xamarin.Forms.AbsoluteLayout.AutoSize) constante.

O exemplo [**ChessboardDynamic**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/ChessboardDynamic) coloca o `AbsoluteLayout` em um `ContentView` com um `SizeChanged` manipulador para chamar `AbsoluteLayout.SetLayoutBounds` em todos os filhos para torná-los o mais amplo possível.  

A propriedade vinculável anexada que `AbsoluteLayout` define é o campo somente leitura estático do tipo `BindableProperty` nomeado [`AbsoluteLayout.LayoutBoundsProperty`](xref:Xamarin.Forms.AbsoluteLayout.LayoutBoundsProperty) . O `AbsoluteLayout.SetLayoutBounds` método estático é implementado chamando `SetValue` o filho com o `AbsoluteLayout.LayoutBoundsProperty` . O filho contém um dicionário no qual a propriedade ligável anexada e seu valor são armazenados. Durante o layout, o `AbsoluteLayout` pode obter esse valor chamando [ `AbsoluteLayout.GetLayoutBounds` ] (xref: Xamarin.Forms . AbsoluteLayout. GetLayoutBounds ( Xamarin.Forms . Bindobject)), que é implementado com uma `GetValue` chamada.

## <a name="proportional-sizing-and-positioning"></a>Dimensionamento e posicionamento proporcional

`AbsoluteLayout`implementa um recurso de dimensionamento e posicionamento proporcional. A classe define uma segunda propriedade vinculável anexada, [`LayoutFlagsProperty`](xref:Xamarin.Forms.AbsoluteLayout.LayoutFlagsProperty) , com os métodos estáticos relacionados [ `AbsoluteLayout.SetLayoutFlags` ] (xref: Xamarin.Forms . AbsoluteLayout. SetLayoutFlags ( Xamarin.Forms . Vinculobject, Xamarin.Forms . AbsoluteLayoutFlags)) e [ `AbsoluteLayout.GetLayoutFlags` ] (xref: Xamarin.Forms . AbsoluteLayout. GetLayoutFlags ( Xamarin.Forms . Bindobject)).

O argumento para `AbsoluteLayout.SetLayoutFlags` e o valor de retorno de `AbsoluteLayout.GetLayoutFlags` é um valor do tipo [`AbsoluteLayoutFlags`](xref:Xamarin.Forms.AbsoluteLayoutFlags) , uma enumeração com os seguintes membros:

- [`None`](xref:Xamarin.Forms.AbsoluteLayoutFlags.None)(igual a 0)
- [`XProportional`](xref:Xamarin.Forms.AbsoluteLayoutFlags.XProportional)uma
- [`YProportional`](xref:Xamarin.Forms.AbsoluteLayoutFlags.YProportional)2
- [`PositionProportional`](xref:Xamarin.Forms.AbsoluteLayoutFlags.PositionProportional)Beta
- [`WidthProportional`](xref:Xamarin.Forms.AbsoluteLayoutFlags.WidthProportional)quatro
- [`HeightProportional`](xref:Xamarin.Forms.AbsoluteLayoutFlags.HeightProportional)8
- [`SizeProportional`](xref:Xamarin.Forms.AbsoluteLayoutFlags.SizeProportional)12
- [`All`](xref:Xamarin.Forms.AbsoluteLayoutFlags.All)(\xFFFFFFFF)

Você pode combiná-los com o operador OR de bit de C#.

Com esses sinalizadores definidos, determinadas propriedades da `Rectangle` estrutura de limites de layout usados para posicionar e dimensionar o filho são interpretadas proporcionalmente.

Quando o `WidthProportional` sinalizador é definido, um `Width` valor de 1 significa que o filho tem a mesma largura que o `AbsoluteLayout` . Uma abordagem semelhante é usada para a altura.

O posicionamento proporcional leva o tamanho em conta. Quando o `XProportional` sinalizador é definido, a `X` propriedade dos `Rectangle` limites de layout é proporcional. Um valor de 0 significa que a borda esquerda do filho está posicionada na borda esquerda do `AbsoluteLayout` , mas uma posição de 1 significa que a borda direita do filho está posicionada na borda direita do `AbsoluteLayout` , e não além da borda direita do `AbsoluteLayout` que você pode esperar. Uma `X` propriedade de 0,5 centraliza o filho horizontalmente no `AbsoluteLayout` .

O exemplo [**ChessboardProportional**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/ChessboardProportional) demonstra o uso de dimensionamento e posicionamento proporcionais.

## <a name="working-with-proportional-coordinates"></a>Trabalhando com coordenadas proporcionais

Às vezes, é mais fácil imaginar o posicionamento proporcional de maneira diferente de como ele é implementado no `AbsoluteLayout` . Você pode preferir trabalhar com coordenadas proporcionais em que uma `X` propriedade de 1 posiciona a borda esquerda do filho (em vez da borda direita) na borda direita do `AbsoluteLayout` .

Esse esquema de posicionamento alternativo pode ser chamado de "coordenadas de filho fracionários". Você pode converter de coordenadas de filho fracionários para os limites de layout necessários para `AbsoluteLayout` usar as seguintes fórmulas:

layoutBounds. X = (fractionalChildCoordinate. X/(1-layoutBounds. Width))

layoutBounds. Y = (fractionalChildCoordinate. Y/(1-layoutBounds. Height))

O exemplo [**ProportionalCoordinateCalc**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/PropCoordCalc) demonstra isso.

## <a name="absolutelayout-and-xaml"></a>AbsoluteLayout e XAML

Você pode usar um `AbsoluteLayout` em XAML e definir as propriedades vinculáveis anexadas nos filhos de um `AbsoluteLayout` usando valores de atributo de `AbsoluteLayout.LayoutBounds` e `AbsoluteLayout.LayoutFlags` . Isso é demonstrado nos exemplos de [**AbsoluteXamlDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/AbsoluteXamlDemo) e [**ChessboardXaml**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/ChessboardXaml) . O último programa contém 32 `BoxView` elementos, mas usa um implícito `Style` que inclui a `AbsoluteLayout.LayoutFlags` propriedade para reduzir o mínimo da marcação.

Um atributo em XAML que consiste em um nome de classe, um ponto e um nome de propriedade é *sempre* uma propriedade vinculável anexada.

## <a name="overlays"></a>Sobreposições

Você pode usar `AbsoluteLayout` para construir uma *sobreposição*, que aborda a página com outros controles, talvez para proteger o usuário de interagir com os controles normais na página.

O exemplo [**SimpleOverlay**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/SimpleOverlay) demonstra essa técnica e também demonstra o [`ProgressBar`](xref:Xamarin.Forms.ProgressBar) , que exibe a extensão para a qual um programa concluiu uma tarefa.

## <a name="some-fun"></a>Um pouco divertido

O exemplo [**DotMatrixClock**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/DotMatrixClock) exibe a hora atual com uma exibição de matriz de pontos de 5x7 simulada. Cada ponto é um `BoxView` (há 228 deles) dimensionado e posicionado no `AbsoluteLayout` .

[![Captura de tela tripla de relógio de matriz de ponto](images/ch14fg08-small.png "Relógio de matriz de ponto")](images/ch14fg08-large.png#lightbox "Relógio de matriz de ponto")

O programa [**BouncingText**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/BouncingText) anima dois `Label` objetos para saltar horizontalmente e verticalmente na tela.

## <a name="related-links"></a>Links relacionados

- [Capítulo 14 texto completo (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch14-Apr2016.pdf)
- [Exemplos do capítulo 14](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14)
- [AbsoluteLayout](~/xamarin-forms/user-interface/layouts/absolute-layout.md)
- [Propriedades Anexadas](~/xamarin-forms/xaml/attached-properties.md)
