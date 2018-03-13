---
title: "Transformações de SkiaSharp"
description: "Saiba mais sobre as transformações de exibição de gráficos SkiaSharp"
ms.topic: article
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: E9BE322E-ECB3-4395-AFE4-4474A0F25551
author: charlespetzold
ms.author: chape
ms.date: 03/10/2017
ms.openlocfilehash: 756261db8f82198e6c5a4330a663bbd9965e57f7
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/12/2018
---
# <a name="skiasharp-transforms"></a>Transformações de SkiaSharp

_Saiba mais sobre as transformações de exibição de gráficos SkiaSharp_

SkiaSharp suporta transformações de tradicional de gráficos que são implementadas como métodos para o [ `SKCanvas` ](https://developer.xamarin.com/api/type/SkiaSharp.SKCanvas/) objeto. Matematicamente, transformações alteram as coordenadas e tamanhos que você especificar na `SKCanvas` desenho funções como objetos gráficos são renderizados. As transformações são geralmente convenientes para desenhar gráficos repetitivos ou para animação. Algumas técnicas & #x 2014; como girar bitmaps ou texto & #x 2014; não é possível sem o uso de transformações.

Transformações de SkiaSharp suportam as seguintes operações:

- *Traduzir* em coordenadas de deslocamento de um local para outro
- *Escala* para aumentar ou diminuir as coordenadas e tamanhos
- *Girar* coordenadas em torno de um ponto de rotação
- *Distorcer* deslocar coordena horizontal ou verticalmente para que um retângulo se torna um paralelogramo

Eles são conhecidos como *afim* transformações. Transformações afins sempre preservam linhas paralelas e nunca causam uma coordenada ou tamanho se torne infinito. Um quadrado nunca é transformado em algo diferente de um paralelogramo e um círculo nunca é transformado em algo diferente de uma elipse.

SkiaSharp também oferece suporte a transformações não afins (também chamado de *projective* ou *perspectiva* transforma) com base em uma matriz de transformação de 3 por 3 padrão. Uma transformação não é afim permite que um quadrado para serem transformadas em qualquer quadrilátero convexo (um quatro lados figura com todos os ângulos interior menor que 180 graus). Transformações afins de não podem fazer com que coordenadas ou tamanhos se infinita, mas eles são vitais para efeitos 3D.

## <a name="differences-between-skiasharp-and-xamarinforms-transforms"></a>Diferenças entre SkiaSharp e xamarin. Forms transformações

Xamarin. Forms também oferece suporte a transformações que são semelhantes aos SkiaSharp. O xamarin. Forms [ `VisualElement` ](https://developer.xamarin.com/api/type/Xamarin.Forms.VisualElement/) classe define as seguintes propriedades de transformação:

- `TranslationX` e `TranslationY`
- `Scale`
- `Rotation`, `RotationX` e `RotationY`

O `RotationX` e `RotationY` propriedades são transformações de perspectiva que criam efeitos 3D aparentes.

Há várias diferenças essenciais entre transformações SkiaSharp e xamarin. Forms transformações:

A primeira diferença é que SkiaSharp são aplicadas a todo o `SKCanvas` objeto enquanto o xamarin. Forms são aplicadas a pessoa `VisualElement` derivados. (Você pode aplicar as transformações xamarin. Forms para o `SKCanvasView` o objeto em si, pois `SKCanvasView` deriva `VisualElement`, mas dentro desse `SKCanvasView`, aplicam as transformações SkiaSkarp.)

As transformações SkiaSharp são relativas ao canto superior esquerdo do `SKCanvas` enquanto xamarin. Forms transformações são relativas ao canto superior esquerdo do `VisualElement` para que elas são aplicadas. Essa diferença é importante ao aplicar a escala e rotação transforma porque essas transformações são sempre em relação a um ponto específico.

A realmente grande diferença é que as transformações de SKiaSharp são *métodos* enquanto as xamarin. Forms as transformações são *propriedades*. Essa é uma diferença semântica, além de diferença sintática: SkiaSharp transformações executam uma operação enquanto transformações xamarin. Forms definido um estado. Transformações de SkiaSharp se aplicam a objetos gráficos subsequentemente desenhado, mas não os objetos gráficos que são desenhados antes da transformação é aplicada. Em contraste, uma transformação xamarin. Forms aplica-se a um elemento anteriormente processado assim que a propriedade está definida. Transformações de SkiaSharp são cumulativas, como os métodos são chamados; Xamarin. Forms transformações são substituídas quando a propriedade é definida com outro valor.

Todos os programas de exemplo nesta seção aparecem sob o título **transforma** na home page do [ **SkiaSharpFormsDemos** ](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/SkiaSharpFormsDemos/) programa e no [ **Transforma** ](https://github.com/xamarin/xamarin-forms-samples/tree/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Transforms) pasta da solução.

## <a name="the-translate-transformtranslatemd"></a>[A transformação de conversão](translate.md)

Saiba como usar a transformação de conversão para deslocar SkiaSharp gráficos.

## <a name="the-scale-transformscalemd"></a>[A transformação de escala](scale.md)

Descobrir a transformação de escala SkiaSharp para dimensionamento de objetos para vários tamanhos.

## <a name="the-rotate-transformrotatemd"></a>[A transformação de rotação](rotate.md)

Explore os efeitos e animações possíveis com a transformação de rotação SkiaSharp.

## <a name="the-skew-transformskewmd"></a>[A transformação de distorção](skew.md)

Veja como a transformação de distorção pode criar objetos gráficos inclinados em SkiaSharp.

## <a name="matrix-transformsmatrixmd"></a>[Transformações de matriz](matrix.md)

Aprofundar-se em transformações SkiaSharp com a matriz de transformação versátil.

## <a name="touch-manipulationstouchmd"></a>[Manipulações de toque](touch.md)

Matriz de usar transformações para implementar manipulações de toque para arrastar, escala e rotação.

## <a name="non-affine-transformsnon-affinemd"></a>[Transformações não afins](non-affine.md)

Vá além oridinary com efeitos de transformação não é afim.

## <a name="3d-rotation3d-rotationmd"></a>[Rotação 3D](3d-rotation.md)

Use transformações não afins para girar objetos 2D em espaço 3D.


## <a name="related-links"></a>Links relacionados

- [APIs de SkiaSharp](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (sample)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/SkiaSharpFormsDemos/)
