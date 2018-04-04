---
title: Resumo do capítulo 5. Lidar com tamanhos
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 486800E9-C09F-4B95-9AC2-C0F8FE563BCF
author: charlespetzold
ms.author: chape
ms.date: 11/07/2017
ms.openlocfilehash: 71d40136d7c3fc780815471cb822e94a4fa704c5
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="summary-of-chapter-5-dealing-with-sizes"></a>Resumo do capítulo 5. Lidar com tamanhos

Vários tamanhos de xamarin. Forms foram encontrados até agora:

- A altura da barra de status do iOS é 20
- O `BoxView` tem uma largura e altura padrão de 40
- O padrão `Padding` em um `Frame` é 20
- O padrão `Spacing` no `StackLayout` é 6
- O `Device.GetNamedSize` método retorna um tamanho de fonte numérico

Esses tamanhos não são pixels. Em vez disso, eles são unidades independentes de dispositivo independentemente reconhecidas por cada plataforma.

## <a name="pixels-points-dps-dips-and-dius"></a>Pixels, pontos, dps, DIPs e DIUs

No início do histórico do Microsoft Windows e Apple Mac, os programadores trabalhou em unidades de pixels. No entanto, o surgimento de resolução mais alta exibe necessária uma abordagem mais abstrata e virtualizada em coordenadas da tela. No mundo Mac, os programadores trabalhado em unidades de *pontos*, tradicionalmente 1/72 polegada, enquanto os desenvolvedores do Windows usada *unidades independentes de dispositivo* (DIUs) com base em 1/96 polegada.

No entanto, dispositivos móveis geralmente são mantidos muito mais parecidos com a aparência e ter uma resolução mais alta que a área de trabalho telas, o que implica que pode ser tolerada uma maior densidade de pixels.

Os programadores direcionamento dispositivos iPhone e iPad da Apple continuam a funcionar em unidades de *pontos*, mas existem 160 desses pontos por polegada. Dependendo do dispositivo, pode haver 1, 2 ou 3 pixels para o ponto.

Android é semelhante. Os programadores trabalham em unidades de *independente de densidade de pixels* (dps), e a relação entre dps e pixels é baseada em 160 dps por polegada.

O tempo de execução do Windows também estabeleceu fatores de dimensionamento que implicam algo semelhante a 160 unidades independentes de dispositivo por polegada.

Em resumo, um programador xamarin. Forms direcionado para telefones e tablets pode assumir que todas as unidades de medida são baseadas nos seguintes critérios:

- 160 unidades por polegada, equivalente a
- 64 unidades para o centímetro

Somente leitura [ `Width` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Width/) e [ `Height` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Height/) propriedades definidas por `VisualElement` ter padrão "simular" valores de &ndash;1. Somente quando um elemento foi dimensionado e acomodado em layout essas propriedades refletirá o tamanho real do elemento em unidades independentes de dispositivo. Esse tamanho inclui qualquer `Padding` definida no elemento, mas não o `Margin`.

Um elemento visual dispara o [ `SizeChanged` ](https://developer.xamarin.com/api/event/Xamarin.Forms.VisualElement.SizeChanged/) evento quando seu `Width` ou `Height` foi alterado. O [ **WhatSize** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/WhatSize) exemplo usa esse evento para exibir o tamanho da tela do programa.

## <a name="metrical-sizes"></a>Tamanhos de métrica

O [ **MetricalBoxView** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/MetricalBoxView) usa [ `WidthRequest` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.WidthRequest/) e [ `HeightRequest` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.HeightRequest/) para exibir uma `BoxView` uma polegada alto e um centímetro amplo.

## <a name="estimated-font-sizes"></a>Tamanhos de fonte estimado

O [ **tamanhos de fonte** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/FontSizes) exemplo mostra como usar a regra de 160 unidades-para-a-polegadas para especificar os tamanhos de fonte em unidades de pontos. A consistência visual entre as plataformas usando essa técnica é melhor do que `Device.GetNamedSize`.

## <a name="fitting-text-to-available-size"></a>Ajuste de texto tamanho disponível

É possível ajustar um bloco de texto dentro de um retângulo específico, calculando um `FontSize` do `Label` usando os seguintes critérios:

- Espaçamento entre linhas é 120% do tamanho da fonte (% de 130 nas plataformas do Windows).
- Largura do caractere médio é 50% do tamanho da fonte.

O [ **EstimatedFontSize** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/EstimatedFontSize) demonstra essa técnica. Este programa foi gravado antes do [ `Margin` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.Margin/) propriedade estava disponível, portanto, ele usa um [ `ContentView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentView/) com um [ `Padding` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Layout.Padding/) configuração para simular um margem.

[![Captura de tela tripla do tamanho estimado da fonte](images/ch05fg07-small.png "texto ajustar ao tamanho disponível")](images/ch05fg07-large.png#lightbox "texto ajustar ao tamanho disponível")

## <a name="a-fit-to-size-clock"></a>Um relógio de ajuste de tamanho

O [ **FitToSizeClock** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/FitToSizeClock) exemplo demonstra como usar [ `Device.StartTimer` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Device.StartTimer/p/System.TimeSpan/System.Func%7BSystem.Boolean%7D/) para iniciar um temporizador que periodicamente notifica o aplicativo quando é hora de atualizar o relógio. O tamanho da fonte é definido para um sexto da largura da página para garantir a exibição tão grandes quanto possível.

## <a name="accessibility-issues"></a>Problemas de acessibilidade

O **EstimatedFontSize** programa e o **FitToSizeClock** programa ambos contêm uma falha sutil: se o usuário altera as configurações de acessibilidade do telefone Android ou Windows 10 Mobile, o programa não pode estimar o tamanho que o texto é renderizado com base no tamanho da fonte. O [ **AccessibilityTest** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/AccessibilityTest) demonstra esse problema.

## <a name="empirically-fitting-text"></a>Empiricamente ajustando o texto

Outra maneira de ajustar o texto a um retângulo é empiricamente calcular o tamanho do texto renderizado e ajustá-lo para cima ou para baixo. O programa nas chamadas catálogo [ `GetSizeRequest` ](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.GetSizeRequest/p/System.Double/System.Double/) em um elemento visual para obter o tamanho do elemento desejado. Que método foi substituído e programas em vez disso, devem chamar [ `Measure` ](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.Measure/p/System.Double/System.Double/Xamarin.Forms.MeasureFlags/).

Para uma `Label`, o primeiro argumento deve ser a largura do contêiner (para permitir o encapsulamento), enquanto o segundo argumento deve ser definido para `Double.PositiveInfinity` para tornar a altura irrestrita. O [ **EmpiricalFontSize** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/EmpiricalFontSize) demonstra essa técnica.



## <a name="related-links"></a>Links relacionados

- [Texto completo do capítulo 5 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch05-Apr2016.pdf)
- [Exemplos de capítulo 5](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05)
- [Exemplos de capítulo 5 F #](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/FS)
