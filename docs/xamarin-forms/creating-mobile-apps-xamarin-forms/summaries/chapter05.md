---
title: Resumo do capítulo 5. Lidando com tamanhos
description: 'Criação de aplicativos móveis com xamarin. Forms: Resumo do capítulo 5. Lidando com tamanhos'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 486800E9-C09F-4B95-9AC2-C0F8FE563BCF
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2018
ms.openlocfilehash: c082bdb10732e42b37511cf050e50f46990a5b5b
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2020
ms.locfileid: "70771150"
---
# <a name="summary-of-chapter-5-dealing-with-sizes"></a>Resumo do capítulo 5. Lidando com tamanhos

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05)

> [!NOTE]
> Observações sobre essa página indicam áreas onde o xamarin. Forms foi bifurcado do material apresentado no livro.

Vários tamanhos em xamarin. Forms até agora foram encontrados:

- A altura da barra de status iOS é 20
- O `BoxView` tem uma largura e uma altura padrão de 40
- O `Padding` padrão em uma `Frame` é 20
- O `Spacing` padrão no `StackLayout` é 6
- O método `Device.GetNamedSize` retorna um tamanho de fonte numérico

Esses tamanhos não são pixels. Em vez disso, eles são unidades independentes de dispositivo reconhecidas independentemente por cada plataforma.

## <a name="pixels-points-dps-dips-and-dius"></a>Pixels, pontos, dps, quedas e DIUs

Logo no início do histórico do Microsoft Windows e Apple Mac, os programadores trabalharam em unidades de pixels. No entanto, o advento de monitores de alta resolução necessária uma abordagem mais abstrata e virtualizada para coordenadas de tela. No mundo do Mac, os programadores trabalharam em unidades de *pontos*, tradicionalmente 1/72 polegadas, enquanto os desenvolvedores do Windows usavam DIUs ( *unidades independentes de dispositivo* ) com base em 1/96 polegadas.

Dispositivos móveis, no entanto, geralmente são mantidos muito mais próximos à face e ter uma resolução mais alta que a área de trabalho telas, o que implica que uma maior densidade de pixel pode ser tolerada.

Os programadores visando dispositivos Apple iPhone e iPad continuam a funcionar em unidades de *pontos*, mas há 160 desses pontos em polegadas. Dependendo do dispositivo, pode haver 1, 2 ou 3 pixels para o ponto.

O Android é semelhante. Os programadores trabalham em unidades de pontos *independentes de densidade* (DPS) e a relação entre o DPS e os pixels é baseada no dps de 160 a polegada.

Telefones do Windows e dispositivos móveis também tem estabelecido os fatores de dimensionamento que implicam algo semelhante a 160 unidades independentes de dispositivo por polegada.

> [!NOTE]
> Xamarin. Forms não oferece suporte a qualquer telefone com base em Windows ou dispositivo móvel.

Em resumo, um programador de xamarin. Forms, telefones e tablets de direcionamento pode assumir que todas as unidades de medida são baseadas nos seguintes critérios:

- 160 unidades por polegada, equivalente a
- 64 unidades para o centímetro

As propriedades [`Width`](xref:Xamarin.Forms.VisualElement.Width) e [`Height`](xref:Xamarin.Forms.VisualElement.Height) somente leitura definidas por `VisualElement` têm valores "fictícios" padrão de &ndash;1. Somente quando um elemento foi dimensionado e acomodado em layout essas propriedades refletirá o tamanho real do elemento em unidades independentes de dispositivo. Esse tamanho inclui qualquer `Padding` definido no elemento, mas não no `Margin`.

Um elemento visual aciona o evento [`SizeChanged`](xref:Xamarin.Forms.VisualElement.SizeChanged) quando seu `Width` ou `Height` foi alterado. O exemplo [**WhatSize**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/WhatSize) usa esse evento para exibir o tamanho da tela do programa.

## <a name="metrical-sizes"></a>Tamanhos de métrica

O [**MetricalBoxView**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/MetricalBoxView) usa [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) e [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) para exibir um `BoxView` de uma polegada de altura e um centímetro de largura.

## <a name="estimated-font-sizes"></a>Tamanhos de fonte estimado

O exemplo de [**fontsizes**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/FontSizes) mostra como usar a regra 160-unidades para a polegada para especificar tamanhos de fonte em unidades de pontos. A consistência visual entre as plataformas que usam essa técnica é melhor do que a `Device.GetNamedSize`.

## <a name="fitting-text-to-available-size"></a>Ajuste de texto tamanho disponível

É possível ajustar um bloco de texto dentro de um retângulo específico, calculando um `FontSize` do `Label` usando os seguintes critérios:

- Espaçamento entre linhas é 120% do tamanho da fonte (130% nas plataformas Windows).
- Largura do caractere média é de 50% do tamanho da fonte.

O exemplo [**EstimatedFontSize**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/EstimatedFontSize) demonstra essa técnica. Este programa foi gravado antes da [`Margin`](xref:Xamarin.Forms.View.Margin) Propriedade estava disponível, portanto, ele usa um [`ContentView`](xref:Xamarin.Forms.ContentView) com uma configuração de [`Padding`](xref:Xamarin.Forms.Layout.Padding) para simular uma margem.

[![Captura de tela tripla do tamanho estimado da fonte](images/ch05fg07-small.png "O texto se ajusta ao tamanho disponível")](images/ch05fg07-large.png#lightbox "O texto se ajusta ao tamanho disponível")

## <a name="a-fit-to-size-clock"></a>Um relógio de tamanho de ajuste

O exemplo [**FitToSizeClock**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/FitToSizeClock) demonstra como usar [`Device.StartTimer`](xref:Xamarin.Forms.Device.StartTimer(System.TimeSpan,System.Func{System.Boolean})) para iniciar um temporizador que notifica periodicamente o aplicativo quando é hora de atualizar o relógio. O tamanho da fonte é definido como um sexto da largura da página para tornar a exibição tão grandes quanto possível.

## <a name="accessibility-issues"></a>Problemas de acessibilidade

O programa **EstimatedFontSize** e o programa **FitToSizeClock** contêm uma falha sutil: se o usuário alterar as configurações de acessibilidade do telefone no Android ou no Windows 10 Mobile, o programa não poderá mais estimar o quão grande o texto é renderizado com base no tamanho da fonte. O exemplo [**AccessibilityTest**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/AccessibilityTest) demonstra esse problema.

## <a name="empirically-fitting-text"></a>Empiricamente ajustando o texto

Outra maneira de ajustar o texto a um retângulo é empiricamente calcular o tamanho do texto renderizado e ajustá-lo para cima ou para baixo. O programa no livro chama [`GetSizeRequest`](xref:Xamarin.Forms.VisualElement.GetSizeRequest(System.Double,System.Double)) em um elemento visual para obter o tamanho desejado do elemento. Esse método foi preterido e, em vez disso, os programas devem chamar [`Measure`](xref:Xamarin.Forms.VisualElement.Measure(System.Double,System.Double,Xamarin.Forms.MeasureFlags)).

Para um `Label`, o primeiro argumento deve ser a largura do contêiner (para permitir o encapsulamento), enquanto o segundo argumento deve ser definido como `Double.PositiveInfinity` para tornar a altura irrestrita. O exemplo [**EmpiricalFontSize**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/EmpiricalFontSize) demonstra essa técnica.

## <a name="related-links"></a>Links relacionados

- [Capítulo 5 texto completo (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch05-Apr2016.pdf)
- [Exemplos do capítulo 5](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05)
- [Exemplos do F# capítulo 5](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/FS)
