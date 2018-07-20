---
title: Resumo do capítulo 14. Layout absoluto
description: 'Criação de aplicativos móveis com xamarin. Forms: Resumo do capítulo 14. Layout absoluto'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 88882A48-3226-42D1-96ED-241250B64A84
author: charlespetzold
ms.author: chape
ms.date: 07/19/2018
ms.openlocfilehash: 07774bb5d63b8c9fb9c48192744d383b37f64900
ms.sourcegitcommit: 8555a4dd1a579b2206f86c867125ee20fbc3d264
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/19/2018
ms.locfileid: "39156635"
---
# <a name="summary-of-chapter-14-absolute-layout"></a>Resumo do capítulo 14. Layout absoluto

Como o `StackLayout`, [ `AbsoluteLayout` ](xref:Xamarin.Forms.AbsoluteLayout) deriva `Layout<View>` e herda um `Children` propriedade. `AbsoluteLayout` implementa um sistema de layout que exige que o programador especificar as posições dos seus filhos e, opcionalmente, seu tamanho. A posição é especificada pelo canto superior esquerdo do filho em relação ao canto superior esquerdo do `AbsoluteLayout` em unidades independentes de dispositivo. `AbsoluteLayout` também implementa um posicionamento proporcional e o recurso de dimensionamento.

`AbsoluteLayout` deve ser considerado como um sistema de layout de finalidade especial a ser usado somente quando o programador pode impor um tamanho nos filhos (por exemplo, `BoxView` elementos) ou quando o tamanho do elemento não afeta o posicionamento dos outros filhos. O `HorizontalOptions` e `VerticalOptions` propriedades não têm efeito nos filhos de um `AbsoluteLayout`.

Esse capítulo também introduz o recurso importante da *anexado propriedades vinculáveis* que permitem que as propriedades definidas em uma classe (nesse caso `AbsoluteLayout`) a ser anexado a outra classe (um filho do `AbsoluteLayout`).

## <a name="absolutelayout-in-code"></a>AbsoluteLayout no código

Você pode adicionar um filho para o `Children` coleção de um `AbsoluteLayout` usando o padrão [ `Add` ](xref:System.Collections.Generic.ICollection`1.Add*) método, mas `AbsoluteLayout` também fornece um estendido [ `Add` ](https://developer.xamarin.com/api/member/Xamarin.Forms.AbsoluteLayout+IAbsoluteList%3CT%3E.Add/p/Xamarin.Forms.View/Xamarin.Forms.Rectangle/Xamarin.Forms.AbsoluteLayoutFlags/) método que permite que você especifique um [ `Rectangle` ](xref:Xamarin.Forms.Rectangle). Outra [ `Add` ](https://developer.xamarin.com/api/member/Xamarin.Forms.AbsoluteLayout+IAbsoluteList%3CT%3E.Add/p/Xamarin.Forms.View/Xamarin.Forms.Point/) método requer apenas um [ `Point` ](xref:Xamarin.Forms.Point), caso em que o filho é irrestrito e se dimensiona.

Você pode criar uma `Rectangle` valor com um [construtor](xref:Xamarin.Forms.Rectangle.%23ctor(System.Double,System.Double,System.Double,System.Double)) que requer quatro valores &mdash; as duas primeiras indicando a posição do canto superior esquerdo do filho em relação ao seu pai e os dois que indica o tamanho do filho. Ou você pode usar um [construtor](xref:Xamarin.Forms.Rectangle.%23ctor(Xamarin.Forms.Point,Xamarin.Forms.Size)) que requer uma `Point` e uma [ `Size` ](xref:Xamarin.Forms.Size) valor.

Eles `Add` métodos são demonstrados [ **AbsoluteDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/AbsoluteDemo), quais posições `BoxView` elementos usando `Rectangle` valores e um `Label` elemento usando apenas um `Point` valor.

O [ **ChessboardFixed** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/ChessboardFixed) de exemplo usa 32 `BoxView` elementos para criar o padrão xadrez. O programa oferece o `BoxView` tamanho elementos embutidos do quadrado 35 unidades. O `AbsoluteLayout` tem sua `HorizontalOptions` e `VerticalOptions` definido como `LayoutOptions.Center`, que faz com que o `AbsoluteLayout` para ter um tamanho total do quadrado de 280 unidades.

## <a name="attached-bindable-properties"></a>Propriedades vinculáveis anexadas

Também é possível definir a posição e, opcionalmente, o tamanho de um filho de um `AbsoluteLayout` depois que ele foi adicionado para o `Children` coleção usando o método estático [ `AbsoluteLayout.SetLayoutBounds` ](xref:Xamarin.Forms.AbsoluteLayout.SetLayoutBounds(Xamarin.Forms.BindableObject,Xamarin.Forms.Rectangle)). O primeiro argumento é o filho; o segundo é um `Rectangle` objeto. Você pode especificar que o filho se dimensiona horizontalmente e/ou verticalmente, definindo valores de largura e altura para o [ `AbsoluteLayout.AutoSize` ](xref:Xamarin.Forms.AbsoluteLayout.AutoSize) constante.

O [ **ChessboardDynamic** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/ChessboardDynamic) exemplo coloca o `AbsoluteLayout` em um `ContentView` com um `SizeChanged` manipulador a chamar `AbsoluteLayout.SetLayoutBounds` em todos os filhos para torná-los tão grandes quanto possível.  

A propriedade associável anexada que `AbsoluteLayout` define é o campo estático somente leitura do tipo `BindableProperty` denominado [ `AbsoluteLayout.LayoutBoundsProperty` ](xref:Xamarin.Forms.AbsoluteLayout.LayoutBoundsProperty). Estático `AbsoluteLayout.SetLayoutBounds` método é implementado por meio da chamada `SetValue` no filho com o `AbsoluteLayout.LayoutBoundsProperty`. O filho contém um dicionário no qual a propriedade associável anexada e seu valor são armazenados. Durante o layout, o `AbsoluteLayout` pode obter esse valor chamando [ `AbsoluteLayout.GetLayoutBounds` ](xref:Xamarin.Forms.AbsoluteLayout.GetLayoutBounds(Xamarin.Forms.BindableObject)), que é implementado com um `GetValue` chamar.

## <a name="proportional-sizing-and-positioning"></a>Dimensionamento proporcional e o posicionamento

`AbsoluteLayout` implementa um dimensionamento proporcional e o posicionamento do recurso. A classe define uma segunda associável propriedade anexada [ `LayoutFlagsProperty` ](xref:Xamarin.Forms.AbsoluteLayout.LayoutFlagsProperty), com os métodos estáticos relacionados [ `AbsoluteLayout.SetLayoutFlags` ](xref:Xamarin.Forms.AbsoluteLayout.SetLayoutFlags(Xamarin.Forms.BindableObject,Xamarin.Forms.AbsoluteLayoutFlags)) e [ `AbsoluteLayout.GetLayoutFlags` ](xref:Xamarin.Forms.AbsoluteLayout.GetLayoutFlags(Xamarin.Forms.BindableObject)).

O argumento `AbsoluteLayout.SetLayoutFlags` e o valor de retorno `AbsoluteLayout.GetLayoutFlags` é um valor do tipo [ `AbsoluteLayoutFlags` ](xref:Xamarin.Forms.AbsoluteLayoutFlags), uma enumeração com os seguintes membros:

- [`None`](xref:Xamarin.Forms.AbsoluteLayoutFlags.None) (igual a 0)
- [`XProportional`](xref:Xamarin.Forms.AbsoluteLayoutFlags.XProportional) (1)
- [`YProportional`](xref:Xamarin.Forms.AbsoluteLayoutFlags.YProportional) (2)
- [`PositionProportional`](xref:Xamarin.Forms.AbsoluteLayoutFlags.PositionProportional) (3)
- [`WidthProportional`](xref:Xamarin.Forms.AbsoluteLayoutFlags.WidthProportional) (4)
- [`HeightProportional`](xref:Xamarin.Forms.AbsoluteLayoutFlags.HeightProportional) (8)
- [`SizeProportional`](xref:Xamarin.Forms.AbsoluteLayoutFlags.SizeProportional) (12)
- [`All`](xref:Xamarin.Forms.AbsoluteLayoutFlags.All) (\xFFFFFFFF)

Você pode combinar tudo isso com o c# operador bit a bit OR.

Com esse conjunto de sinalizadores, determinadas propriedades do `Rectangle` estrutura dos limites de layout usada para posicionar e dimensionar o filho são interpretados proporcionalmente.

Quando o `WidthProportional` sinalizador estiver definido, um `Width` valor 1 significará que o filho é a mesma largura que o `AbsoluteLayout`. Uma abordagem semelhante é usada para a altura.

O posicionamento proporcional considera o tamanho. Quando o `XProportional` sinalizador estiver definido, o `X` propriedade do `Rectangle` dos limites de layout é proporcional. Um valor de 0 significa que o filho esquerdo do edge é posicionado na borda esquerda do `AbsoluteLayout`, mas uma posição 1 significa que a borda direita do filho seja posicionada na borda direita do `AbsoluteLayout`, não além da borda direita do `AbsoluteLayout` quanto você pensava expec t. Uma `X` filho horizontalmente em centros de propriedade de 0,5 a `AbsoluteLayout`.

O [ **ChessboardProportional** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/ChessboardProportional) exemplo demonstra o uso de dimensionamento proporcional e o posicionamento.

## <a name="working-with-proportional-coordinates"></a>Trabalhar com coordenadas proporcionais

Às vezes, é mais fácil pensar de forma diferente de como ele é implementado no posicionamento proporcional a `AbsoluteLayout`. Talvez você prefira trabalhar com coordenadas proporcionais em que um `X` propriedade 1 posiciona a borda esquerda de seu filho (em vez da borda direita) em relação a borda direita do `AbsoluteLayout`.

Esse esquema alternativo de posicionamento pode ser chamada "coordinates filho fracionários." Você pode converter de coordenadas fracionários filho para os limites de layout necessárias para `AbsoluteLayout` usando as fórmulas a seguir:

layoutBounds.X = (fractionalChildCoordinate.X / (1 - layoutBounds.Width))

layoutBounds.Y = (fractionalChildCoordinate.Y / (1 - layoutBounds.Height))

O [ **ProportionalCoordinateCalc** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/PropCoordCalc) demonstra isso.

## <a name="absolutelayout-and-xaml"></a>AbsoluteLayout e XAML

Você pode usar um `AbsoluteLayout` em XAML e defina as propriedades de associáveis anexadas nos filhos de um `AbsoluteLayout` usando valores de atributo `AbsoluteLayout.LayoutBounds` e `AbsoluteLayout.LayoutFlags`. Isso é demonstrado na [ **AbsoluteXamlDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/AbsoluteXamlDemo) e o [ **ChessboardXaml** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/ChessboardXaml) exemplos. O último programa contém 32 `BoxView` elementos, mas usa implícito `Style` que inclui o `AbsoluteLayout.LayoutFlags` propriedade para manter a marcação para baixo até um mínimo.

É um atributo em XAML que consiste em um nome de classe, um ponto e um nome de propriedade *sempre* uma propriedade associável anexada.

## <a name="overlays"></a>Sobreposições

Você pode usar `AbsoluteLayout` para construir uma *sobreposição*, que aborda a página com outros controles, talvez para proteger o usuário interaja com os controles normais na página.

O [ **SimpleOverlay** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/SimpleOverlay) exemplo demonstra essa técnica e também demonstra a [ `ProgressBar` ](xref:Xamarin.Forms.ProgressBar), que exibe a extensão para o qual um programa foi concluída uma tarefa.

## <a name="some-fun"></a>Alguma diversão

O [ **DotMatrixClock** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/DotMatrixClock) exemplo exibe a hora atual com uma exibição simulado matricial de 5, 7. Cada ponto é um `BoxView` (há 228 deles) dimensionado e posicionado sobre o `AbsoluteLayout`.

[![Captura de tela tripla de relógio de matriz de ponto](images/ch14fg08-small.png "matricial relógio")](images/ch14fg08-large.png#lightbox "matricial relógio")

O [ **BouncingText** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/BouncingText) programa anima dois `Label` objetos a serem Elástico horizontalmente e verticalmente na tela.



## <a name="related-links"></a>Links relacionados

- [Capítulo 14 de texto completo (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch14-Apr2016.pdf)
- [Exemplos do capítulo 14](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14)
- [AbsoluteLayout](~/xamarin-forms/user-interface/layouts/absolute-layout.md)
- [Propriedades anexadas](~/xamarin-forms/xaml/attached-properties.md)
