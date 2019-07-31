---
title: Transformações de SkiaSharp
description: Este artigo explora as transformações para exibir gráficos de SkiaSharp em aplicativos xamarin. Forms e demonstra isso com o código de exemplo.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: E9BE322E-ECB3-4395-AFE4-4474A0F25551
author: davidbritch
ms.author: dabritch
ms.date: 03/10/2017
ms.openlocfilehash: 39de35c8e43ebf2b3db25724ad761e3576ba5057
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68644950"
---
# <a name="skiasharp-transforms"></a>Transformações de SkiaSharp

[![Baixar exemplo](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Saiba mais sobre as transformações de exibição de gráficos de SkiaSharp_

SkiaSharp dá suporte a transformações de tradicional de gráficos que são implementadas como métodos do [ `SKCanvas` ](xref:SkiaSharp.SKCanvas) objeto. Matematicamente, transformações alteram as coordenadas e tamanhos que você especificar na `SKCanvas` desenho funções como objetos gráficos são renderizados. Transformações geralmente são convenientes para desenho de gráficos repetitivos ou animação. Algumas técnicas &mdash; , como girar texto ou bitmaps &mdash; não são possíveis sem o uso de transformações.

Transformações de SkiaSharp compatíveis com as seguintes operações:

- *Traduzir* em coordenadas de deslocamento de um local para outro
- *Escala* para aumentar ou diminuir as coordenadas e tamanhos
- *Girar* girar coordenadas em torno de um ponto
- *Distorcer* deslocar as coordenadas horizontal ou verticalmente para que um retângulo se torna um paralelogramo

Eles são conhecidos como *afim* transforma. Transformações afins sempre preservam linhas paralelas e nunca causam uma coordenada ou tamanho para se tornar infinito. Um quadrado nunca é transformado em algo diferente de um paralelogramo, e um círculo nunca é transformado em algo diferente de uma elipse.

SkiaSharp também dá suporte a transformações não afins (também chamado de *projective* ou *perspectiva* transforma) com base em uma matriz de transformação de 3 por 3 padrão. Uma transformação não afim permite que um quadrado ser transformadas em qualquer diamante convexo, que é uma figura de quatro lados com todos os ângulos interior menor que 180 graus. Transformações não afins podem fazer com que as coordenadas ou tamanhos para se tornar infinito, mas elas são vitais para efeitos 3D.

## <a name="differences-between-skiasharp-and-xamarinforms-transforms"></a>Diferenças entre SkiaSharp e transformações do xamarin. Forms

Xamarin. Forms também oferece suporte a transformações que são semelhantes do SkiaSharp. O xamarin. Forms [ `VisualElement` ](xref:Xamarin.Forms.VisualElement) classe define as propriedades de transformação a seguir:

- [`TranslationX`](xref:Xamarin.Forms.VisualElement.TranslationX) e [`TranslationY`](xref:Xamarin.Forms.VisualElement.TranslationY)
- [`Scale`](xref:Xamarin.Forms.VisualElement.Scale)
- [`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation), [ `RotationX` ](xref:Xamarin.Forms.VisualElement.RotationX), e [`RotationY`](xref:Xamarin.Forms.VisualElement.RotationY)

O `RotationX` e `RotationY` propriedades são as transformações de perspectiva que criar efeitos 3D aparentes.

Há várias diferenças essenciais entre transformações de SkiaSharp e transformações do xamarin. Forms:

A primeira diferença é que as transformações de SkiaSharp são aplicadas a todo `SKCanvas` do objeto enquanto o xamarin. Forms são aplicadas ao indivíduo `VisualElement` derivados. (Você pode aplicar as transformações de xamarin. Forms para o `SKCanvasView` do objeto em si, pois `SKCanvasView` deriva `VisualElement`, mas dentro dessa `SKCanvasView`, aplicam as transformações SkiaSkarp.)

As transformações de SkiaSharp são relativas ao canto superior esquerdo dos `SKCanvas` enquanto as transformações de xamarin. Forms são relativas ao canto superior esquerdo do `VisualElement` ao qual eles são aplicados. Essa diferença é importante ao aplicar o dimensionamento e rotação transforma porque essas transformações são sempre relativas a um ponto específico.

A realmente grande diferença é que são de transformações de SKiaSharp *métodos* enquanto as transformações do xamarin. Forms são *propriedades*. Essa é uma diferença semântica além da diferença sintática: Transformações de SkiaSharp executam uma operação enquanto as transformações do Xamarin. Forms definem um estado. Transformações de SkiaSharp se aplicam a objetos de gráficos subsequentemente desenhado, mas não a objetos de gráficos são desenhados antes que a transformação é aplicada. Em contraste, uma transformação do xamarin. Forms aplica-se a um elemento de renderizado anteriormente assim que a propriedade está definida. Transformações de SkiaSharp são cumulativas, como os métodos são chamados; Transformações de xamarin. Forms são substituídas quando a propriedade é definida com outro valor.

Todos os programas de exemplo nesta seção são exibidos na **transformações de SkiaSharp** seção o [ **SkiaSharpFormsDemos** ](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) programa. Código-fonte pode ser encontrado na [ **transforma** ](https://github.com/xamarin/xamarin-forms-samples/tree/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms) pasta da solução.

## <a name="the-translate-transformtranslatemd"></a>[A transformação de conversão](translate.md)

Saiba como usar a transformação de conversão para deslocar SkiaSharp gráficos.

## <a name="the-scale-transformscalemd"></a>[A transformação de escala](scale.md)

Descubra a transformação de escala de SkiaSharp para dimensionar objetos para vários tamanhos.

## <a name="the-rotate-transformrotatemd"></a>[A transformação de rotação](rotate.md)

Explore os efeitos e animações possíveis com a transformação de rotação de SkiaSharp.

## <a name="the-skew-transformskewmd"></a>[A transformação de distorção](skew.md)

Veja como a transformação de distorção pode criar o objeto de gráfico inclinado.

## <a name="matrix-transformsmatrixmd"></a>[Transformações de matriz](matrix.md)

Aprofunde-se em transformações de SkiaSharp com a matriz de transformação versátil.

## <a name="touch-manipulationstouchmd"></a>[Manipulações de toque](touch.md)

Transforma a matriz de uso para implementar as manipulações de toque para arrastar, dimensionamento e rotação.

## <a name="non-affine-transformsnon-affinemd"></a>[Transformações não afins](non-affine.md)

Vá além de oridinary com efeitos de transformação não afim.

## <a name="3d-rotation3d-rotationmd"></a>[Rotação 3D](3d-rotation.md)

Use transformações não afins para girar objetos 2D no espaço 3D.


## <a name="related-links"></a>Links relacionados

- [APIs de SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (amostra)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
