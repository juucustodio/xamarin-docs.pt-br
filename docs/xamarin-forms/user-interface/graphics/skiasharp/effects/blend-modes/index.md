---
title: Modos de mesclagem de SkiaSharp
description: Use o blend modos para definir o que acontece quando os objetos gráficos são empilhados em uma da outra.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: CE1B222E-A2D0-4016-A532-EC1E59EE3D6B
author: davidbritch
ms.author: dabritch
ms.date: 08/23/2018
ms.openlocfilehash: b2083a819bc688e1742bc4cecccfd24c4d89ac39
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50111465"
---
# <a name="skiasharp-blend-modes"></a>Modos de mesclagem de SkiaSharp

Esses artigos enfocam o [ `BlendMode` ](xref:SkiaSharp.SKPaint.BlendMode) propriedade do [ `SKPaint` ](xref:SkiaSharp.SKPaint). O `BlendMode` propriedade é do tipo [ `SKBlendMode` ](xref:SkiaSharp.SKBlendMode), uma enumeração com membros de 29.

O `BlendMode` propriedade determina o que acontece quando um objeto gráfico (geralmente chamado do _código-fonte_) é renderizado sobre objetos gráficos existentes (chamado o _destino_). Normalmente, esperamos que o novo objeto de gráfico para ocultar objetos debaixo dele. Mas o que acontece apenas porque é o modo de mesclagem padrão `SKBlendMode.SrcOver`, o que significa que a fonte é desenhada _sobre_ o destino. Os outros membros de 28 `SKBlendMode` causar outros efeitos. Na programação de gráficos, a técnica de objetos gráficos de várias maneiras de combinação é conhecida como _composição_.

## <a name="the-skblendmodes-enumeration"></a>A enumeração SKBlendModes

Os modos do blend SkiaSharp intimamente correspondem aos descritos no W3C [ **composição e a combinação do nível 1** ](https://www.w3.org/TR/compositing-1/) especificação. O Skia [ **referência SkBlendMode** ](https://skia.org/user/api/SkBlendMode_Reference) também fornece informações úteis em segundo plano. Para obter uma introdução geral misturar modos, o [ **modos de mesclagem** ](https://en.wikipedia.org/wiki/Blend_modes) artigo na Wikipédia é um bom começo. Modos de mesclagem têm suporte do Adobe Photoshop, portanto, não há muito mais informações online sobre os modos de mesclagem nesse contexto.

Os membros de 29 do `SKBlendMode` enumeração pode ser dividida em três categorias:

| Porter Duff | Separáveis    | Não separáveis |
| ----------- | ------------ | ------------- |
| `Clear`     | `Modulate`   | `Hue`         |
| `Src`       | `Screen`     | `Saturation`  |
| `Dst`       | `Overlay`    | `Color`       |
| `SrcOver`   | `Darken`     | `Luminosity`  |
| `DstOver`   | `Lighten`    |               |
| `SrcIn`     | `ColorDodge` |               |
| `DstIn`     | `ColorBurn`  |               |
| `SrcOut`    | `HardLight`  |               |
| `DstOut`    | `SoftLight`  |               |
| `SrcATop`   | `Difference` |               |
| `DstATop`   | `Exclusion`  |               |
| `Xor`       | `Multiply`   |               |
| `Plus`      |              |               |

Os nomes dessas três categorias levará mais significado nas discussões que seguem. A ordem que os membros são listados aqui é o mesmo na definição do `SKBlendMode` enumeração. Os membros de 13 enumeração na primeira coluna tem os valores de inteiro de 0 a 12. A segunda coluna são membros de enumeração que correspondem aos números inteiros de 13 a 24, e os membros na terceira coluna têm valores de 25 a 28.

Esses blend modos são discutidos na _aproximadamente_ a mesma ordem em que o W3C **composição e a combinação do nível 1** documento, mas há algumas diferenças: O `Src` modo é chamado _Cópia_ no documento do W3C, e `Plus` é chamado _mais leve_. O documento do W3C define uma _Normal_ modo de mesclagem que não está incluído no `SKBlendModes` pois ele seria o mesmo que `SrcOver`. O `Modulate` modo de mesclagem (na parte superior da primeira coluna) não está incluído no documento do W3C e discussão sobre o `Multiply` modo precede `Screen`.

Porque o `Modulate` modo blend é exclusivo para Skia, serão discutida como um modo de Porter Duff adicional e como um modo separável.

## <a name="the-importance-of-transparency"></a>A importância de transparência

Historicamente, a composição foi desenvolvida em conjunto com o conceito do _canal alfa_. Em uma exibição da superfície como o `SKCanvas` objeto e um bitmap colorido, cada pixel consiste em 4 bytes: 1 byte cada para os componentes vermelhos, verdes e azuis e um adicional para transparência. Esse componente alfa é 0 para transparência total e 0xFF para opacidade total, com diferentes níveis de transparência entre esses valores.

Muitos dos modos de mesclagem se baseiam na transparência. Geralmente, quando um `SKCanvas` é obtido pela primeira vez em um `PaintSurface` manipulador, ou quando um `SKCanvas` é criado para desenhar em um bitmap, a primeira etapa é essa chamada:

```csharp
canvas.Clear();
```

Esse método substitui todos os pixels da tela com pixels pretos transparentes, equivalentes a `new SKColor(0, 0, 0, 0)` ou o inteiro 0x00000000. Todos os bytes de todos os pixels são inicializados em zero.

A superfície de desenho de um `SKCanvas` que é obtido em um `PaintSurface` manipulador pode parecer ter um plano de fundo branco, mas só porque o `SKCanvasView` em si tem um plano de fundo transparente, e a página tem um plano de fundo branco. Você pode demonstrar esse fato para si mesmo, definindo o xamarin. Forms `BackgroundColor` propriedade de `SKCanvasView` para uma cor do xamarin. Forms:

```csharp
canvasView.BackgroundColor = Color.Red;
```

Ou, em uma classe que deriva de `ContentPage`, você pode definir a cor de plano de fundo da página:

```csharp
BackgroundColor = Color.Red;
```

Você verá que esta tela de fundo vermelha atrás de seus elementos gráficos de SkiaSharp porque o SkiaSharp tela em si é transparente.

O artigo [ **SkiaSharp transparência** ](../../basics/transparency.md) mostrou algumas técnicas básicas usando transparência para organizar vários elementos gráficos em uma imagem composta. Os modos do blend ir além disso, mas transparência permanece crucial para os modos de mesclagem. 

## <a name="skiasharp-porter-duff-blend-modesporter-duffmd"></a>[Modos de mesclagem Porter-Duff SkiaSharp](porter-duff.md)

Use os modos do blend Porter Duff para compor cenas com base em imagens de origem e destino.

## <a name="skiasharp-separable-blend-modesseparablemd"></a>[Modos de mesclagem separáveis de SkiaSharp](separable.md)

Use os modos do blend separáveis para alterar as cores vermelhas, verdes e azuis.

## <a name="skiasharp-non-separable-blend-modesnon-separablemd"></a>[Modos de mesclagem não separáveis de SkiaSharp](non-separable.md)

Use os modos de mesclagem não separáveis para alterar o matiz, saturação ou luminosidade.

## <a name="related-links"></a>Links relacionados

- [APIs de SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (amostra)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)