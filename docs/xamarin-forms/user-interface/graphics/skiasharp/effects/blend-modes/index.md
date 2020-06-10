---
title: "SkiaSharp Blend Modes" Descrição: "Use modos de mesclagem para definir o que acontece quando objetos gráficos são empilhados uns aos outros".
MS. Prod: xamarin MS. Technology: xamarin-skiasharp MS. AssetID: CE1B222E-A2D0-4016-A532-EC1E59EE3D6B autor: davidbritch MS. Author: dabritch MS. Date: 08/23/2018 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="skiasharp-blend-modes"></a>Modos de mesclagem SkiaSharp

[![Baixar exemplo ](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

Esses artigos se concentram na [`BlendMode`](xref:SkiaSharp.SKPaint.BlendMode) propriedade de [`SKPaint`](xref:SkiaSharp.SKPaint) . A `BlendMode` propriedade é do tipo [`SKBlendMode`](xref:SkiaSharp.SKBlendMode) , uma enumeração com 29 membros.

A `BlendMode` propriedade determina o que acontece quando um objeto gráfico (geralmente chamado de _origem_) é renderizado sobre os objetos gráficos existentes (chamados de _destino_). Normalmente, esperamos que o novo objeto gráfico oculte os objetos abaixo dele. Mas isso ocorre apenas porque o modo de mesclagem padrão é `SKBlendMode.SrcOver` , o que significa que a origem é desenhada _sobre_ o destino. Os outros 28 membros de `SKBlendMode` causam outros efeitos. Na programação de gráficos, a técnica de combinar objetos gráficos de várias maneiras é conhecida como _composição_.

## <a name="the-skblendmodes-enumeration"></a>A enumeração SKBlendModes

Os modos de combinação de SkiaSharp correspondem de forma mais próxima aos descritos na especificação W3C de [**composição e mesclagem nível 1**](https://www.w3.org/TR/compositing-1/) . A [**visão geral**](https://skia.org/user/api/SkBlendMode_Overview) do skia SkBlendMode também fornece informações úteis sobre o plano de fundo. Para obter uma introdução geral aos modos de mesclagem, o artigo [**modos de mesclagem**](https://en.wikipedia.org/wiki/Blend_modes) na Wikipédia é um bom começo. Os modos de mesclagem têm suporte no Adobe Photoshop, portanto, há muitas informações online adicionais sobre modos de mesclagem nesse contexto.

Os 29 membros da `SKBlendMode` enumeração podem ser divididos em três categorias:

| Carregador-Duff | Separáveis    | Não separáveis |
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

Os nomes dessas três categorias terão mais significado nas discussões a seguir. A ordem em que os membros são listados aqui é igual à definição da `SKBlendMode` enumeração. Os 13 membros de enumeração na primeira coluna têm os valores inteiros de 0 a 12. A segunda coluna são membros de enumeração que correspondem a números inteiros de 13 a 24, e os membros na terceira coluna têm valores de 25 a 28.

Esses modos de mesclagem são discutidos em _aproximadamente_ a mesma ordem no documento de **composição e MESCLAgem** do W3C de nível 1, mas há algumas diferenças: o `Src` modo é chamado de _cópia_ no documento W3C e `Plus` é chamado _mais claro_. O documento W3C define um modo de mesclagem _normal_ que não está incluído no `SKBlendModes` porque seria o mesmo que o `SrcOver` . O `Modulate` modo de mesclagem (na parte superior da primeira coluna) não está incluído no documento W3C e a discussão sobre o `Multiply` modo precede `Screen` .

Como o `Modulate` modo de mesclagem é exclusivo do skia, ele será discutido como um modo carregador-Duff adicional e como um modo separáveis.

## <a name="the-importance-of-transparency"></a>A importância da transparência

Historicamente, a composição foi desenvolvida em conjunto com o conceito do _canal alfa_. Em uma superfície de exibição, como o `SKCanvas` objeto e um bitmap de cor completa, cada pixel consiste em 4 bytes: 1 byte cada para os componentes vermelho, verde e azul e um byte adicional para transparência. Esse componente alfa é 0 para transparência total e 0xFF para opacidade total, com diferentes níveis de transparência entre esses valores.

Muitos dos modos de mesclagem dependem da transparência. Normalmente, quando um `SKCanvas` é obtido pela primeira vez em um `PaintSurface` manipulador, ou quando um `SKCanvas` é criado para desenhar em um bitmap, a primeira etapa é esta chamada:

```csharp
canvas.Clear();
```

Esse método substitui todos os pixels da tela por pixels pretos transparentes, equivalentes a `new SKColor(0, 0, 0, 0)` ou o inteiro 0x00000000. Todos os bytes de todos os pixels são inicializados para zero.

A superfície de desenho de um `SKCanvas` que é obtida em um `PaintSurface` manipulador pode parecer ter um plano de fundo branco, mas isso só ocorre porque ele `SKCanvasView` tem um plano de fundo transparente e a página tem um plano de fundo branco. Você pode demonstrar esse fato, definindo a Xamarin.Forms `BackgroundColor` propriedade de `SKCanvasView` como uma Xamarin.Forms cor:

```csharp
canvasView.BackgroundColor = Color.Red;
```

Ou, em uma classe derivada de `ContentPage` , você pode definir a cor do plano de fundo da página:

```csharp
BackgroundColor = Color.Red;
```

Você verá esse plano de fundo vermelho por trás dos seus gráficos SkiaSharp porque a própria tela SkiaSharp é transparente.

O artigo [**SkiaSharp Transparency**](../../basics/transparency.md) mostrou algumas técnicas básicas de uso da transparência para organizar vários gráficos em uma imagem composta. Os modos de mesclagem vão além disso, mas a transparência permanece crucial para os modos de mesclagem.

## <a name="skiasharp-porter-duff-blend-modes"></a>[Modos de SkiaSharp carregador-Duff Blend](porter-duff.md)

Use os modos de mesclagem carregador-Duff para compor cenas com base em imagens de origem e de destino.

## <a name="skiasharp-separable-blend-modes"></a>[Modos de SkiaSharp separáveis Blend](separable.md)

Use os modos de combinação separáveis para alterar as cores vermelha, verde e azul.

## <a name="skiasharp-non-separable-blend-modes"></a>[Modos de mistura SkiaSharp não separáveis](non-separable.md)

Use os modos de combinação não separáveis para alterar matiz, saturação ou luminosidade.

## <a name="related-links"></a>Links relacionados

- [APIs do SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
