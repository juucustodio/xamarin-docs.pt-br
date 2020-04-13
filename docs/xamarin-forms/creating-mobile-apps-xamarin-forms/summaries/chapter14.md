---
title: Resumo do Capítulo 14. Layout absoluto
description: 'Criando aplicativos móveis com Xamarin.Forms: Resumo do Capítulo 14. Layout absoluto'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 88882A48-3226-42D1-96ED-241250B64A84
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2018
ms.openlocfilehash: c489bf244396cf180ed8e1272308048a14b67300
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "70771127"
---
# <a name="summary-of-chapter-14-absolute-layout"></a>Resumo do Capítulo 14. Layout absoluto

[![Baixar](~/media/shared/download.png) amostra Baixar a amostra](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14)

`StackLayout`Como, [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout) deriva `Layout<View>` e herda `Children` uma propriedade. `AbsoluteLayout`implementa um sistema de layout que exige que o programador especifique as posições de seus filhos e, opcionalmente, seu tamanho. A posição é especificada pelo canto superior esquerdo da criança em `AbsoluteLayout` relação ao canto superior esquerdo das unidades independentes do dispositivo. `AbsoluteLayout`também implementa um recurso de posicionamento e dimensionamento proporcional.

`AbsoluteLayout`deve ser considerado como um sistema de layout de uso especial para ser usado apenas `BoxView` quando o programador pode impor um tamanho às crianças (por exemplo, elementos) ou quando o tamanho do elemento não afeta o posicionamento de outras crianças. As `HorizontalOptions` `VerticalOptions` propriedades e não têm `AbsoluteLayout`efeito sobre as crianças de um .

Este capítulo também introduz a característica importante das *propriedades vinculadas anexadas* que `AbsoluteLayout`permitem que propriedades definidas em uma `AbsoluteLayout`classe (neste caso) sejam anexadas a outra classe (uma criança da ).

## <a name="absolutelayout-in-code"></a>AbsoluteLayout em código

Você pode adicionar uma `Children` criança `AbsoluteLayout` à coleção [`Add`](xref:System.Collections.Generic.ICollection`1.Add*) de `AbsoluteLayout` um usando [`Add`](xref:Xamarin.Forms.AbsoluteLayout.IAbsoluteList`1.Add*) o método padrão, [`Rectangle`](xref:Xamarin.Forms.Rectangle)mas também fornece um método estendido que permite especificar um . Outro [`Add`](xref:Xamarin.Forms.AbsoluteLayout.IAbsoluteList`1.Add*) método requer [`Point`](xref:Xamarin.Forms.Point)apenas um , nesse caso a criança é desconstrangida e tamanhos próprios.

Você pode `Rectangle` criar um valor com um &mdash; [construtor](xref:Xamarin.Forms.Rectangle.%23ctor(System.Double,System.Double,System.Double,System.Double)) que requer quatro valores os dois primeiros indicando a posição do canto superior esquerdo da criança em relação ao seu pai, e os dois segundos indicando o tamanho da criança. Ou você pode [constructor](xref:Xamarin.Forms.Rectangle.%23ctor(Xamarin.Forms.Point,Xamarin.Forms.Size)) usar um `Point` construtor [`Size`](xref:Xamarin.Forms.Size) que requer um valor.

Esses `Add` métodos são demonstrados em `BoxView` [**AbsoluteDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/AbsoluteDemo), que posiciona `Point` elementos usando `Rectangle` valores, e um `Label` elemento usando apenas um valor.

A amostra [**ChessboardFixed**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/ChessboardFixed) `BoxView` usa 32 elementos para criar o padrão de tabuleiro de xadrez. O programa `BoxView` dá aos elementos um tamanho codificado de 35 unidades quadradas. O `AbsoluteLayout` tem `HorizontalOptions` `VerticalOptions` seu `LayoutOptions.Center`e definido `AbsoluteLayout` para , o que faz com que o tamanho total de 280 unidades quadradas.

## <a name="attached-bindable-properties"></a>Propriedades vinculáveis anexadas

Também é possível definir a posição e, opcionalmente, o `AbsoluteLayout` tamanho de uma `Children` criança de um [`AbsoluteLayout.SetLayoutBounds`](xref:Xamarin.Forms.AbsoluteLayout.SetLayoutBounds(Xamarin.Forms.BindableObject,Xamarin.Forms.Rectangle))após ter sido adicionado à coleção usando o método estático . O primeiro argumento é a criança; o segundo `Rectangle` é um objeto. Você pode especificar que o filho se dimensiona horizontalmente e/ou [`AbsoluteLayout.AutoSize`](xref:Xamarin.Forms.AbsoluteLayout.AutoSize) verticalmente definindo valores de largura e altura para a constante.

A amostra [**ChessboardDynamic**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/ChessboardDynamic) `AbsoluteLayout` coloca `ContentView` o `SizeChanged` em `AbsoluteLayout.SetLayoutBounds` um com um manipulador para chamar todas as crianças para torná-las o maior possível.  

A propriedade vinculada anexada que `AbsoluteLayout` define é o campo `BindableProperty` [`AbsoluteLayout.LayoutBoundsProperty`](xref:Xamarin.Forms.AbsoluteLayout.LayoutBoundsProperty)de leitura estática do tipo nomeado . O `AbsoluteLayout.SetLayoutBounds` método estático `SetValue` é implementado chamando a criança com o `AbsoluteLayout.LayoutBoundsProperty`. A criança contém um dicionário no qual a propriedade vinculada e seu valor são armazenados. Durante o `AbsoluteLayout` layout, o pode [`AbsoluteLayout.GetLayoutBounds`](xref:Xamarin.Forms.AbsoluteLayout.GetLayoutBounds(Xamarin.Forms.BindableObject))obter esse valor `GetValue` ligando, que é implementado com uma chamada.

## <a name="proportional-sizing-and-positioning"></a>Dimensionamento e posicionamento proporcionais

`AbsoluteLayout`implementa um recurso de dimensionamento e posicionamento proporcional. A classe define uma segunda propriedade [`LayoutFlagsProperty`](xref:Xamarin.Forms.AbsoluteLayout.LayoutFlagsProperty)vinculável anexada, [`AbsoluteLayout.SetLayoutFlags`](xref:Xamarin.Forms.AbsoluteLayout.SetLayoutFlags(Xamarin.Forms.BindableObject,Xamarin.Forms.AbsoluteLayoutFlags)) com [`AbsoluteLayout.GetLayoutFlags`](xref:Xamarin.Forms.AbsoluteLayout.GetLayoutFlags(Xamarin.Forms.BindableObject))os métodos estáticos relacionados e .

O argumento `AbsoluteLayout.SetLayoutFlags` e o `AbsoluteLayout.GetLayoutFlags` valor de retorno [`AbsoluteLayoutFlags`](xref:Xamarin.Forms.AbsoluteLayoutFlags)é um valor de tipo , uma enumeração com os seguintes membros:

- [`None`](xref:Xamarin.Forms.AbsoluteLayoutFlags.None)(igual a 0)
- [`XProportional`](xref:Xamarin.Forms.AbsoluteLayoutFlags.XProportional)(1)
- [`YProportional`](xref:Xamarin.Forms.AbsoluteLayoutFlags.YProportional)(2)
- [`PositionProportional`](xref:Xamarin.Forms.AbsoluteLayoutFlags.PositionProportional)(3)
- [`WidthProportional`](xref:Xamarin.Forms.AbsoluteLayoutFlags.WidthProportional)(4)
- [`HeightProportional`](xref:Xamarin.Forms.AbsoluteLayoutFlags.HeightProportional)(8)
- [`SizeProportional`](xref:Xamarin.Forms.AbsoluteLayoutFlags.SizeProportional)(12)
- [`All`](xref:Xamarin.Forms.AbsoluteLayoutFlags.All)(\xFFFFFFFF)

Você pode combiná-los com o operador C# bitwise OR.

Com essas bandeiras definidas, certas propriedades da estrutura dos limites de `Rectangle` layout usadas para posicionar e dimensionar a criança são interpretadas proporcionalmente.

Quando `WidthProportional` a bandeira é `Width` definida, um valor de 1 significa `AbsoluteLayout`que a criança tem a mesma largura que a . Uma abordagem semelhante é usada para a altura.

O posicionamento proporcional leva em conta o tamanho. Quando `XProportional` a bandeira é `X` definida, `Rectangle` a propriedade dos limites do layout é proporcional. Um valor de 0 significa que a borda esquerda da criança `AbsoluteLayout`está posicionada na borda esquerda do , mas uma posição `AbsoluteLayout`de 1 significa que `AbsoluteLayout` a borda direita da criança está posicionada na borda direita do , não além da borda direita do como você pode esperar. Uma `X` propriedade de 0,5 centraliza `AbsoluteLayout`a criança horizontalmente no .

A amostra [**ChessboardProportional**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/ChessboardProportional) demonstra o uso de dimensionamento e posicionamento proporcionais.

## <a name="working-with-proportional-coordinates"></a>Trabalhando com coordenadas proporcionais

Às vezes, é mais fácil pensar em posicionamento proporcional de `AbsoluteLayout`forma diferente do que é implementado no . Você pode preferir trabalhar com coordenadas proporcionais onde uma `X` propriedade de 1 posição da borda esquerda `AbsoluteLayout`da criança (em vez da borda direita) contra a borda direita do .

Este esquema de posicionamento alternativo pode ser chamado de "coordenadas fracionadas de crianças". Você pode converter de coordenadas fracionadas de `AbsoluteLayout` crianças para os limites de layout necessários para usar as seguintes fórmulas:

layoutBounds.X = (fractionalChildCoordinate.X / (1 - layoutBounds.Width))

layoutBounds.Y = (fractionalChildCoordinate.Y / (1 - layoutBounds.Height))

A amostra [**ProportionalCoordinateCalc**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/PropCoordCalc) demonstra isso.

## <a name="absolutelayout-and-xaml"></a>AbsoluteLayout e XAML

Você pode `AbsoluteLayout` usar um em XAML e definir as propriedades `AbsoluteLayout` vinculadas `AbsoluteLayout.LayoutBounds` anexadas nos filhos de um uso de valores de atributo e `AbsoluteLayout.LayoutFlags`. Isso é demonstrado nas amostras [**AbsoluteXamlDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/AbsoluteXamlDemo) e [**ChessboardXaml.**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/ChessboardXaml) Este último programa `BoxView` contém 32 `Style` elementos, `AbsoluteLayout.LayoutFlags` mas usa um implícito que inclui a propriedade para manter a marcação abaixo do mínimo.

Um atributo em XAML que consiste em um nome de classe, um ponto e um nome de propriedade é *sempre* uma propriedade vinculada.

## <a name="overlays"></a>Sobreposições

Você pode `AbsoluteLayout` usar para construir uma *sobreposição,* que cobre a página com outros controles, talvez para proteger o usuário de interagir com os controles normais da página.

A amostra [**SimpleOverlay**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/SimpleOverlay) demonstra essa técnica [`ProgressBar`](xref:Xamarin.Forms.ProgressBar)e também demonstra o , que exibe até que ponto um programa completou uma tarefa.

## <a name="some-fun"></a>Um pouco de diversão

A amostra [**DotMatrixClock**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/DotMatrixClock) exibe o tempo atual com uma tela de matriz de ponto 5x7 simulada. Cada dot `BoxView` é um (são 228 deles) dimensionado e posicionado no `AbsoluteLayout`.

[![Captura de tela tripla do relógio de matriz de ponto](images/ch14fg08-small.png "Relógio da Matriz de Ponto")](images/ch14fg08-large.png#lightbox "Relógio da Matriz de Ponto")

O programa [**BouncingText**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/BouncingText) anima `Label` dois objetos para saltar horizontal e verticalmente através da tela.

## <a name="related-links"></a>Links relacionados

- [Capítulo 14 texto completo (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch14-Apr2016.pdf)
- [Capítulo 14 amostras](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14)
- [AbsoluteLayout](~/xamarin-forms/user-interface/layouts/absolute-layout.md)
- [Propriedades anexadas](~/xamarin-forms/xaml/attached-properties.md)
