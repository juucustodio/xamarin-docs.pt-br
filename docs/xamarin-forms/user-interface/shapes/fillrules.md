---
title: 'Xamarin.Forms Formas: regras de preenchimento'
description: Xamarin.Forms Regras de preenchimento de formas determinam se um ponto está na região de preenchimento de uma forma.
ms.prod: xamarin
ms.assetid: 5CABB22B-C6BE-43D1-91D9-6E90A4BD5622
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/24/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: a1277b0acda598128e69b39a99910a468f6baa11
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91559253"
---
# <a name="no-locxamarinforms-shapes-fill-rules"></a>Xamarin.Forms Formas: regras de preenchimento

![API de pré-lançamento](~/media/shared/preview.png)

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos/)

Várias Xamarin.Forms classes Shapes têm `FillRule` Propriedades, do tipo `FillRule` . Isso inclui `Polygon` , `Polyline` e `GeometryGroup` .

A `FillRule` enumeração define `EvenOdd` e `Nonzero` Membros. Cada membro representa uma regra diferente para determinar se um ponto está na região de preenchimento de uma forma.

> [!IMPORTANT]
> Todas as formas são consideradas fechadas para fins de regras de preenchimento.

## <a name="evenodd"></a>EvenOdd

A `EvenOdd` regra de preenchimento desenha um raio do ponto até o infinito em qualquer direção e conta o número de segmentos dentro da forma que o raio cruza. Se esse número for ímpar, o ponto estará dentro. Se esse número for par, o ponto estará fora do.

O exemplo XAML a seguir cria e renderiza uma forma composta, com o `FillRule` padrão para `EvenOdd` :

```xaml
<Path Stroke="Black"
      StrokeThickness="1"
      Fill="#CCCCFF"
      Aspect="Uniform"
      HorizontalOptions="Start">
    <Path.Data>
        <!-- FillRule doesn't need to be set, because EvenOdd is the default. -->
        <GeometryGroup>
            <EllipseGeometry RadiusX="50"
                             RadiusY="50"
                             Center="75,75" />
            <EllipseGeometry RadiusX="70"
                             RadiusY="70"
                             Center="75,75" />
            <EllipseGeometry RadiusX="100"
                             RadiusY="100"
                             Center="75,75" />
            <EllipseGeometry RadiusX="120"
                             RadiusY="120"
                             Center="75,75" />
        </GeometryGroup>
    </Path.Data>
</Path>
```

Neste exemplo, uma forma composta composta por uma série de anéis concêntricos é exibida:

![Forma composta com regra de preenchimento EvenOdd](fillrule-images/evenodd.png "Forma composta com regra de preenchimento EvenOdd")

Na forma composta, observe que o centro e os outros anéis não estão preenchidos. Isso ocorre porque um raio desenhado de qualquer ponto dentro de qualquer um desses dois anéis passa por um número par de segmentos:

![Forma composta com anotações com a regra de preenchimento EvenOdd](fillrule-images/evenodd-annotated.png "Forma composta com anotações com a regra de preenchimento EvenOdd")

Na imagem acima, os círculos vermelhos representam pontos e as linhas representam raios arbitrários. Para o ponto superior, os dois raios arbitrários passam por um número par de segmentos de linha. Portanto, o anel no qual o ponto está não está preenchido. Para o ponto inferior, os dois raios arbitrários passam por um número ímpar de segmentos de linha. Portanto, o anel em que o ponto está é preenchido.

## <a name="nonzero"></a>Diferente

A `Nonzero` regra de preenchimento desenha um raio do ponto até o infinito em qualquer direção e, em seguida, examina os locais em que um segmento da forma cruza o raio. Começando com uma contagem de zero, a contagem é incrementada toda vez que um segmento cruza o raio da esquerda para a direita e diminui cada vez que um segmento cruza o raio da direita para a esquerda. Depois de contar os cruzamentos, se o resultado for zero, o ponto estará fora do polígono. Caso contrário, ele estará dentro de.

O exemplo XAML a seguir cria e renderiza uma forma composta, com a `FillRule` definição para `Nonzero` :

```xaml
<Path Stroke="Black"
      StrokeThickness="1"
      Fill="#CCCCFF"
      Aspect="Uniform"
      HorizontalOptions="Start">
    <Path.Data>
        <GeometryGroup FillRule="Nonzero">
            <EllipseGeometry RadiusX="50"
                             RadiusY="50"
                             Center="75,75" />
            <EllipseGeometry RadiusX="70"
                             RadiusY="70"
                             Center="75,75" />
            <EllipseGeometry RadiusX="100"
                             RadiusY="100"
                             Center="75,75" />
            <EllipseGeometry RadiusX="120"
                             RadiusY="120"
                             Center="75,75" />
        </GeometryGroup>
    </Path.Data>
</Path>
```

Neste exemplo, uma forma composta composta por uma série de anéis concêntricos é exibida:

![Forma composta com regra de preenchimento diferente de zero](fillrule-images/nonzero.png "Forma composta com regra de preenchimento diferente de zero")

Na forma composta, observe que todos os anéis estão preenchidos. Isso ocorre porque todos os segmentos estão sendo executados na mesma direção e, portanto, um raio desenhado de qualquer ponto irá cruzar um ou mais segmentos e a soma dos cruzamentos não será igual a zero:

![Forma composta por anotação com regra de preenchimento diferente de zero](fillrule-images/nonzero-annotated.png "Forma composta por anotação com regra de preenchimento diferente de zero")

Na imagem acima, as setas vermelhas representam a direção em que os segmentos são desenhados e a seta preta representa um raio arbitrário em execução a partir de um ponto no anel mais interno. Iniciando com um valor de zero, para cada segmento que o raio cruza, um valor de um é adicionado, já que o segmento cruza o raio da esquerda para a direita.

Uma forma mais complexa com segmentos executados em direções diferentes é necessária para demonstrar melhor o comportamento da `Nonzero` regra de preenchimento. O exemplo de XAML a seguir cria uma forma semelhante ao exemplo anterior, exceto que ele é criado com um `PathGeometry` em vez de um `EllipseGeometry` :

```xaml
<Path Stroke="Black"
      StrokeThickness="1"
      Fill="#CCCCFF">
     <Path.Data>
         <GeometryGroup FillRule="Nonzero">
             <PathGeometry>
                 <PathGeometry.Figures>
                     <!-- Inner ring -->
                     <PathFigure StartPoint="120,120">
                         <PathFigure.Segments>
                             <PathSegmentCollection>
                                 <ArcSegment Size="50,50"
                                             IsLargeArc="True"
                                             SweepDirection="CounterClockwise"
                                             Point="140,120" />
                             </PathSegmentCollection>
                         </PathFigure.Segments>
                     </PathFigure>

                     <!-- Second ring -->
                     <PathFigure StartPoint="120,100">
                         <PathFigure.Segments>
                             <PathSegmentCollection>
                                 <ArcSegment Size="70,70"
                                             IsLargeArc="True"
                                             SweepDirection="CounterClockwise"
                                             Point="140,100" />
                             </PathSegmentCollection>
                         </PathFigure.Segments>
                     </PathFigure>

                     <!-- Third ring  -->
                         <PathFigure StartPoint="120,70">
                         <PathFigure.Segments>
                             <PathSegmentCollection>
                                 <ArcSegment Size="100,100"
                                             IsLargeArc="True"
                                             SweepDirection="CounterClockwise"
                                             Point="140,70" />
                             </PathSegmentCollection>
                         </PathFigure.Segments>
                     </PathFigure>

                     <!-- Outer ring -->
                     <PathFigure StartPoint="120,300">
                         <PathFigure.Segments>
                             <ArcSegment Size="130,130"
                                         IsLargeArc="True"
                                         SweepDirection="Clockwise"
                                         Point="140,300" />
                         </PathFigure.Segments>
                     </PathFigure>
                 </PathGeometry.Figures>
             </PathGeometry>
         </GeometryGroup>
     </Path.Data>
 </Path>
```

Neste exemplo, uma série de segmentos de arco são desenhados, que não são fechados:

![Forma composta com regra de preenchimento diferente de zero](fillrule-images/nonzero-gaps.png "Forma composta com regra de preenchimento diferente de zero")

Na imagem acima, o terceiro arco do centro não é preenchido. Isso ocorre porque a soma dos valores de um determinado raio atravessando os segmentos em seu caminho é zero:

![Forma composta por anotação com regra de preenchimento diferente de zero](fillrule-images/nonzero-gaps-annotated.png "Forma composta por anotação com regra de preenchimento diferente de zero")

Na imagem acima, o círculo vermelho representa um ponto, as linhas pretas representam raios arbitrários que se movem do ponto na região não preenchida e as setas vermelhas representam a direção em que os segmentos são desenhados. Como pode ser visto, a soma dos valores dos raios que cruzam os segmentos é zero:

- O raio arbitrário que viaja da diagonal para a direita cruza dois segmentos que são executados em direções diferentes. Portanto, os segmentos cancelam um ao outro, dando um valor igual a zero.
- O raio arbitrário que viaja da diagonal para a esquerda cruza um total de seis segmentos. No entanto, os cruzamentos cancelam um ao outro para que zero seja a soma final.

Uma soma de zero resultados no anel não está sendo preenchida.

## <a name="related-links"></a>Links relacionados

- [ShapeDemos (exemplo)](/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos/)
- [Xamarin.Forms Existentes](index.md)