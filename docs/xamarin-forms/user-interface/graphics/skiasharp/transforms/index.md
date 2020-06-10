---
Título: "transformações de SkiaSharp" Descrição: "Este artigo explora as transformações para exibir gráficos SkiaSharp em Xamarin.Forms aplicativos e demonstra isso com o código de exemplo".
MS. Prod: xamarin MS. Technology: xamarin-skiasharp MS. AssetID: E9BE322E-ECB3-4395-AFE4-4474A0F25551 autor: davidbritch MS. Author: dabritch MS. Date: 03/10/2017 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="skiasharp-transforms"></a>Transformações de SkiaSharp

[![Baixar exemplo ](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Saiba mais sobre transformações para exibir gráficos SkiaSharp_

O SkiaSharp dá suporte a transformações gráficas tradicionais que são implementadas como métodos do [`SKCanvas`](xref:SkiaSharp.SKCanvas) objeto. Matematicamente, as transformações alteram as coordenadas e os tamanhos que você especifica em `SKCanvas` funções de desenho à medida que os objetos gráficos são renderizados. As transformações costumam ser convenientes para desenhar gráficos repetitivos ou animações. Algumas técnicas &mdash; , como bitmaps girados ou texto, &mdash; não são possíveis sem o uso de transformações.

As transformações do SkiaSharp dão suporte às seguintes operações:

- *Converter* para coordenadas de deslocamento de um local para outro
- *Dimensionar* para aumentar ou diminuir as coordenadas e os tamanhos
- *Girar* para girar coordenadas em um ponto
- *Distorcer* para as coordenadas de deslocamento horizontal ou verticalmente para que um retângulo se torne um paralelogramo

Elas são conhecidas como transformações *afim* . As transformações afim sempre preservam as linhas paralelas e nunca fazem com que uma coordenada ou tamanho se torne infinito. Um quadrado nunca é transformado em nada além de um paralelogramo, e um círculo nunca é transformado em nada além de uma elipse.

O SkiaSharp também dá suporte a transformações não-afim (também chamadas de transformações *projetadas* ou de *perspectiva* ) com base em uma matriz de transformação padrão 3-por-3. Uma transformação não afim permite que um quadrado seja transformado em qualquer diamante convexa, que é uma figura de quatro lados com todos os ângulos interiores inferiores a 180 graus. As transformações não afim podem fazer com que coordenadas ou tamanhos se tornem infinitas, mas são vitais para efeitos 3D.

## <a name="differences-between-skiasharp-and-xamarinforms-transforms"></a>Diferenças entre SkiaSharp e Xamarin.Forms transformações

Xamarin.Formstambém oferece suporte a transformações semelhantes às do SkiaSharp. A Xamarin.Forms [`VisualElement`](xref:Xamarin.Forms.VisualElement) classe define as seguintes propriedades de transformação:

- [`TranslationX`](xref:Xamarin.Forms.VisualElement.TranslationX)e[`TranslationY`](xref:Xamarin.Forms.VisualElement.TranslationY)
- [`Scale`](xref:Xamarin.Forms.VisualElement.Scale)
- [`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation), [`RotationX`](xref:Xamarin.Forms.VisualElement.RotationX) e[`RotationY`](xref:Xamarin.Forms.VisualElement.RotationY)

As `RotationX` `RotationY` Propriedades e são transformações em perspectiva que criam efeitos quase 3D.

Há várias diferenças cruciais entre transformações e transformações de SkiaSharp Xamarin.Forms :

A primeira diferença é que as transformações de SkiaSharp são aplicadas ao `SKCanvas` objeto inteiro, enquanto as Xamarin.Forms transformações são aplicadas a `VisualElement` derivações individuais. (Você pode aplicar as Xamarin.Forms transformações ao `SKCanvasView` próprio objeto, porque `SKCanvasView` deriva de `VisualElement` , mas dentro disso `SKCanvasView` , as transformações de SkiaSkarp se aplicam.)

As transformações SkiaSharp são relativas ao canto superior esquerdo do `SKCanvas` enquanto as Xamarin.Forms transformações são relativas ao canto superior esquerdo do `VisualElement` ao qual elas são aplicadas. Essa diferença é importante ao aplicar transformações de dimensionamento e rotação porque essas transformações são sempre relativas a um ponto específico.

A diferença realmente grande é que as transformações de SKiaSharp são *métodos* , enquanto as Xamarin.Forms transformações são *Propriedades*. Essa é uma diferença semântica além da diferença sintática: as transformações SkiaSharp executam uma operação enquanto as Xamarin.Forms transformações definem um estado. As transformações de SkiaSharp se aplicam a objetos gráficos desenhados subsequentemente, mas não a objetos gráficos que são desenhados antes da aplicação da transformação. Por outro lado, uma Xamarin.Forms transformação se aplica a um elemento renderizado anteriormente assim que a propriedade é definida. As transformações de SkiaSharp são cumulativas, pois os métodos são chamados; Xamarin.Formsas transformações são substituídas quando a propriedade é definida com outro valor.

Todos os programas de exemplo nesta seção aparecem na seção **transformações de SkiaSharp** do programa [**SkiaSharpFormsDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) . O código-fonte pode ser encontrado na pasta [**transformações**](https://github.com/xamarin/xamarin-forms-samples/tree/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms) da solução.

## <a name="the-translate-transform"></a>[A transformação de conversão](translate.md)

Saiba como usar a transformação converter para deslocar gráficos SkiaSharp.

## <a name="the-scale-transform"></a>[A transformação de escala](scale.md)

Descubra a transformação escala de SkiaSharp para dimensionar objetos para vários tamanhos.

## <a name="the-rotate-transform"></a>[A transformação de rotação](rotate.md)

Explore os efeitos e animações possíveis com a transformação SkiaSharp Rotate.

## <a name="the-skew-transform"></a>[A transformação de distorção](skew.md)

Veja como a transformação de distorção pode criar um objeto gráfico inclinado.

## <a name="matrix-transforms"></a>[Transformações de matriz](matrix.md)

Aprofunde-se em transformações de SkiaSharp com a matriz de transformação versátil.

## <a name="touch-manipulations"></a>[Manipulações de toque](touch.md)

Use transformações de matriz para implementar manipulações de toque para arrastar, dimensionar e girar.

## <a name="non-affine-transforms"></a>[Transformações não afins](non-affine.md)

Vá além do oridinary com efeitos de transformação não afim.

## <a name="3d-rotation"></a>[Rotação 3D](3d-rotation.md)

Use transformações não afim para girar objetos 2D no espaço 3D.

## <a name="related-links"></a>Links relacionados

- [APIs do SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
