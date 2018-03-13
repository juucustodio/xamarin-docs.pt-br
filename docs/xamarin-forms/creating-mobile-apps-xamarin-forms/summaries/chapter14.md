---
title: "Resumo do capítulo 14. Layout absoluto"
ms.topic: article
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 88882A48-3226-42D1-96ED-241250B64A84
author: charlespetzold
ms.author: chape
ms.date: 11/07/2017
ms.openlocfilehash: 394e1722c79bac5f034e9ad88eb1fed7e5090f8c
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/12/2018
---
# <a name="summary-of-chapter-14-absolute-layout"></a>Resumo do capítulo 14. Layout absoluto

Como `StackLayout`, [ `AbsoluteLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.AbsoluteLayout/) deriva `Layout<View>` e herda um `Children` propriedade. `AbsoluteLayout` implementa um sistema de layout que exija o programador especificar as posições de seus filhos e, opcionalmente, seu tamanho. A posição é especificada, o canto superior esquerdo do filho em relação ao canto superior esquerdo do `AbsoluteLayout` em unidades independentes de dispositivo. `AbsoluteLayout` também implementa um posicionamento proporcional e o recurso de dimensionamento.

`AbsoluteLayout` deve ser considerado como um sistema de layout de finalidade especial a ser usado somente quando o programador pode impor um tamanho nos filhos (por exemplo, `BoxView` elementos) ou quando o tamanho do elemento não afeta o posicionamento dos filhos. O `HorizontalOptions` e `VerticalOptions` propriedades não têm nenhum efeito nos filhos de um `AbsoluteLayout`.

Este capítulo também apresenta o recurso importante de *anexado propriedades vinculáveis* que permitem que propriedades definidas em uma classe (nesse caso `AbsoluteLayout`) a ser anexado à outra classe (um filho do `AbsoluteLayout`).

## <a name="absolutelayout-in-code"></a>AbsoluteLayout no código

Você pode adicionar um filho de `Children` coleção de um `AbsoluteLayout` usando o padrão [ `Add` ](https://developer.xamarin.com/api/member/System.Collections.Generic.ICollection%3CT%3E.Add/p/T/) método, mas `AbsoluteLayout` também fornece um [ `Add` ](https://developer.xamarin.com/api/member/Xamarin.Forms.AbsoluteLayout+IAbsoluteList%3CT%3E.Add/p/Xamarin.Forms.View/Xamarin.Forms.Rectangle/Xamarin.Forms.AbsoluteLayoutFlags/) método que permite que você especifique um [ `Rectangle` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Rectangle/). Outro [ `Add` ](https://developer.xamarin.com/api/member/Xamarin.Forms.AbsoluteLayout+IAbsoluteList%3CT%3E.Add/p/Xamarin.Forms.View/Xamarin.Forms.Point/) método requer apenas um [ `Point` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Point/), caso em que o filho é irrestrito e tamanhos de si mesmo.

Você pode criar um `Rectangle` valor com um [construtor](https://developer.xamarin.com/api/constructor/Xamarin.Forms.Rectangle.Rectangle/p/System.Double/System.Double/System.Double/System.Double/) que requer quatro valores & #x 2014; as duas primeiras indicando a posição do canto superior esquerdo do filho em relação ao seu pai e os dois indicando o tamanho do filho. Ou você pode usar um [construtor](https://developer.xamarin.com/api/constructor/Xamarin.Forms.Rectangle.Rectangle/p/Xamarin.Forms.Point/Xamarin.Forms.Size/) que requer um `Point` e um [ `Size` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Size/) valor.

Essas `Add` métodos são demonstrados no [ **AbsoluteDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/AbsoluteDemo), quais posições `BoxView` elementos usando `Rectangle` valores e um `Label` elemento usando apenas um `Point` valor.

O [ **ChessboardFixed** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/ChessboardFixed) usa 32 `BoxView` elementos para criar o padrão de tabuleiro de xadrez. O programa fornece o `BoxView` tamanho elementos um embutidos de quadrado 35 unidades. O `AbsoluteLayout` tem seu `HorizontalOptions` e `VerticalOptions` definida como `LayoutOptions.Center`, que faz com que o `AbsoluteLayout` ter um tamanho total de quadrado 280 unidades.

## <a name="attached-bindable-properties"></a>Propriedades vinculáveis anexadas

Também é possível definir a posição e, opcionalmente, o tamanho de um filho de um `AbsoluteLayout` depois que ele foi adicionado para o `Children` coleção usando o método estático [ `AbsoluteLayout.SetLayoutBounds` ](https://developer.xamarin.com/api/member/Xamarin.Forms.AbsoluteLayout.SetLayoutBounds/p/Xamarin.Forms.BindableObject/Xamarin.Forms.Rectangle/). O primeiro argumento é o filho; o segundo é um `Rectangle` objeto. Você pode especificar que o filho dimensiona-se horizontalmente e/ou verticalmente, definindo valores de largura e altura para o [ `AbsoluteLayout.AutoSize` ](https://developer.xamarin.com/api/property/Xamarin.Forms.AbsoluteLayout.AutoSize/) constante.

O [ **ChessboardDynamic** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/ChessboardDynamic) exemplo coloca o `AbsoluteLayout` em uma `ContentView` com um `SizeChanged` manipulador para chamar `AbsoluteLayout.SetLayoutBounds` em todos os filhos para torná-los tão grandes quanto possível.  

A propriedade associável anexada que `AbsoluteLayout` define é o campo somente leitura estático do tipo `BindableProperty` chamado [ `AbsoluteLayout.LayoutBoundsProperty` ](https://developer.xamarin.com/api/field/Xamarin.Forms.AbsoluteLayout.LayoutBoundsProperty/). Estático `AbsoluteLayout.SetLayoutBounds` método é implementado por meio da chamada `SetValue` no filho com o `AbsoluteLayout.LayoutBoundsProperty`. O filho contém um dicionário no qual a propriedade associável anexada e seu valor são armazenados. Durante o layout, o `AbsoluteLayout` pode obter esse valor chamando [ `AbsoluteLayout.GetLayoutBounds` ](https://developer.xamarin.com/api/member/Xamarin.Forms.AbsoluteLayout.GetLayoutBounds/p/Xamarin.Forms.BindableObject/), que é implementado com um `GetValue` chamar.

## <a name="proportional-sizing-and-positioning"></a>Redimensionamento proporcional e posicionamento

`AbsoluteLayout` implementa um redimensionamento proporcional e o posicionamento do recurso. A classe define uma segunda propriedade associável anexada, [ `LayoutFlagsProperty` ](https://developer.xamarin.com/api/field/Xamarin.Forms.AbsoluteLayout.LayoutFlagsProperty/), com os métodos estáticos relacionados [ `AbsoluteLayout.SetLayoutFlags` ](https://developer.xamarin.com/api/member/Xamarin.Forms.AbsoluteLayout.SetLayoutFlags/p/Xamarin.Forms.BindableObject/Xamarin.Forms.AbsoluteLayoutFlags/) e [ `AbsoluteLayout.GetLayoutFlags` ](https://developer.xamarin.com/api/member/Xamarin.Forms.AbsoluteLayout.GetLayoutFlags/p/Xamarin.Forms.BindableObject/).

O argumento `AbsoluteLayout.SetLayoutFlags` e o valor de retorno `AbsoluteLayout.GetLayoutFlags` é um valor do tipo [ `AbsoluteLayoutFlags` ](https://developer.xamarin.com/api/type/Xamarin.Forms.AbsoluteLayoutFlags/), uma enumeração com os seguintes membros:

- [`None`](https://developer.xamarin.com/api/field/Xamarin.Forms.AbsoluteLayoutFlags.None/) (igual a 0)
- [`XProportional`](https://developer.xamarin.com/api/field/Xamarin.Forms.AbsoluteLayoutFlags.XProportional/) (1)
- [`YProportional`](https://developer.xamarin.com/api/field/Xamarin.Forms.AbsoluteLayoutFlags.YProportional/) (2)
- [`PositionProportional`](https://developer.xamarin.com/api/field/Xamarin.Forms.AbsoluteLayoutFlags.PositionProportional/) (3)
- [`WidthProportional`](https://developer.xamarin.com/api/field/Xamarin.Forms.AbsoluteLayoutFlags.WidthProportional/) (4)
- [`HeightProportional`](https://developer.xamarin.com/api/field/Xamarin.Forms.AbsoluteLayoutFlags.HeightProportional/) (8)
- [`SizeProportional`](https://developer.xamarin.com/api/field/Xamarin.Forms.AbsoluteLayoutFlags.SizeProportional/) (12)
- [`All`](https://developer.xamarin.com/api/field/Xamarin.Forms.AbsoluteLayoutFlags.All/) (\xFFFFFFFF)

Você pode combinar essas com o c# operador bit a bit OR.

Com esse conjunto de sinalizadores, certas propriedades do `Rectangle` layout usada para posicionar e dimensionar o filho de estrutura de limites são interpretados proporcionalmente.

Quando o `WidthProportional` sinalizador for definido, um `Width` valor 1 significa que o filho é a mesma largura que o `AbsoluteLayout`. Uma abordagem semelhante é usada para a altura.

O posicionamento proporcional considera o tamanho. Quando o `XProportional` sinalizador for definido, o `X` propriedade o `Rectangle` limites de layout é proporcional. Um valor de 0 significa que o filho margem esquerda é posicionado na borda esquerda do `AbsoluteLayout`, mas uma posição 1 significa que a borda direita do filho é posicionada na borda direita do `AbsoluteLayout`, não além da borda direita do `AbsoluteLayout` como expec t. Um `X` filho horizontalmente em centros de propriedade de 0,5 a `AbsoluteLayout`.

O [ **ChessboardProportional** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/ChessboardProportional) exemplo demonstra o uso de Redimensionamento proporcional e posicionamento.

## <a name="working-with-proportional-coordinates"></a>Trabalhando com coordenadas proporcionais

Às vezes, é mais fácil pensar de forma diferente de como ele é implementado no posicionamento proporcional a `AbsoluteLayout`. Você pode preferir trabalhar com coordenadas proporcionais onde um `X` propriedade 1 posiciona a borda esquerda do filho (ao invés da borda direita) em relação a borda direita do `AbsoluteLayout`.

Esse esquema de posicionamento alternativa pode ser chamada "filho fracionários coordenadas". Você pode converter de coordenadas fracionários filho para os limites de layout necessários para `AbsoluteLayout` usando as fórmulas a seguir:

layoutBounds.X = (fractionalChildCoordinate.X / (1 - layoutBounds.Width))

layoutBounds.Y = (fractionalChildCoordinate.Y / (1 - layoutBounds.Height))

O [ **ProportionalCoordinateCalc** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/PropCoordCalc) exemplo demonstra isso.

## <a name="absolutelayout-and-xaml"></a>AbsoluteLayout e XAML

Você pode usar um `AbsoluteLayout` em XAML e definir as propriedades vinculáveis anexadas os filhos de um `AbsoluteLayout` com os valores de atributo de `AbsoluteLayout.LayoutBounds` e `AbsoluteLayout.LayoutFlags`. Isso é demonstrado no [ **AbsoluteXamlDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/AbsoluteXamlDemo) e [ **ChessboardXaml** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/ChessboardXaml) exemplos. O último programa contém 32 `BoxView` elementos mas usa implícita `Style` que inclui o `AbsoluteLayout.LayoutFlags` propriedade para manter a marcação até um mínimo.

Um atributo em XAML que consiste em um nome de classe, um ponto e um nome de propriedade é *sempre* uma propriedade ligável anexada.

## <a name="overlays"></a>Sobreposições

Você pode usar `AbsoluteLayout` para construir um *sobreposição*, que abrange a página com outros controles, talvez para proteger o usuário interaja com os controles normais na página. 

O [ **SimpleOverlay** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/SimpleOverlay) exemplo demonstra essa técnica e também demonstra o [ `ProgressBar` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ProgressBar/), que exibe a extensão à qual um programa foi concluída uma tarefa.

## <a name="some-fun"></a>Divirta

O [ **DotMatrixClock** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/DotMatrixClock) exemplo exibe a hora atual com uma exibição simulada matricial de 5 7 dias por semana. Cada ponto é um `BoxView` (há 228 deles) dimensionado e posicionado sobre o `AbsoluteLayout`.

[![Captura de tela tripla do relógio de matriz de ponto](images/ch14fg08-small.png "matricial relógio")](images/ch14fg08-large.png#lightbox "matricial relógio")

O [ **BouncingText** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/BouncingText) programa anima dois `Label` objetos Elástico horizontalmente e verticalmente na tela.



## <a name="related-links"></a>Links relacionados

- [Texto completo do capítulo 14 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch14-Apr2016.pdf)
- [Exemplos de capítulo 14](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14)
- [AbsoluteLayout](~/xamarin-forms/user-interface/layouts/absolute-layout.md)
