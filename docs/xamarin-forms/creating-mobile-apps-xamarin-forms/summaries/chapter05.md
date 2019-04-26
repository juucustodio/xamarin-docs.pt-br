---
title: Resumo do capítulo 5. Lidando com tamanhos
description: 'Criando aplicativos móveis com xamarin. Forms: Resumo do capítulo 5. Lidando com tamanhos'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 486800E9-C09F-4B95-9AC2-C0F8FE563BCF
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2018
ms.openlocfilehash: fd6694de756938ff564bed0923427fe62153116a
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61334298"
---
# <a name="summary-of-chapter-5-dealing-with-sizes"></a>Resumo do capítulo 5. Lidando com tamanhos

[![Baixar exemplo](~/media/shared/download.png) baixar o exemplo](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05)

> [!NOTE]
> Observações sobre essa página indicam áreas onde o xamarin. Forms foi bifurcado do material apresentado no livro.

Vários tamanhos em xamarin. Forms até agora foram encontrados:

- A altura da barra de status iOS é 20
- O `BoxView` tem uma largura e altura padrão de 40
- O padrão `Padding` em um `Frame` é 20
- O padrão `Spacing` sobre o `StackLayout` é 6
- O `Device.GetNamedSize` método retorna um tamanho de fonte numérica

Esses tamanhos não são pixels. Em vez disso, eles são unidades independentes de dispositivo reconhecidas independentemente por cada plataforma.

## <a name="pixels-points-dps-dips-and-dius"></a>Pixels, pontos, dps, quedas e DIUs

Logo no início do histórico do Microsoft Windows e Apple Mac, os programadores trabalharam em unidades de pixels. No entanto, o advento de monitores de alta resolução necessária uma abordagem mais abstrata e virtualizada para coordenadas de tela. No mundo do Mac, os programadores trabalharam em unidades de *pontos*, tradicionalmente 1/72 polegada, enquanto os desenvolvedores do Windows usada *unidades independentes de dispositivo* (DIUs) com base em 1/96 polegada.

Dispositivos móveis, no entanto, geralmente são mantidos muito mais próximos à face e ter uma resolução mais alta que a área de trabalho telas, o que implica que uma maior densidade de pixel pode ser tolerada.

Os programadores direcionamento de dispositivos do Apple iPhone e iPad continuam a funcionar em unidades de *pontos de*, mas existem 160 desses pontos por polegada. Dependendo do dispositivo, pode haver 1, 2 ou 3 pixels para o ponto.

O Android é semelhante. Os programadores trabalham em unidades de *pixels independentes de densidade* (dps), e a relação entre o dps e pixels é baseada no 160 dps por polegada.

Telefones do Windows e dispositivos móveis também tem estabelecido os fatores de dimensionamento que implicam algo semelhante a 160 unidades independentes de dispositivo por polegada.

> [!NOTE]
> Xamarin. Forms não oferece suporte a qualquer telefone com base em Windows ou dispositivo móvel.

Em resumo, um programador de xamarin. Forms, telefones e tablets de direcionamento pode assumir que todas as unidades de medida são baseadas nos seguintes critérios:

- 160 unidades por polegada, equivalente a
- 64 unidades para o centímetro

Somente leitura [ `Width` ](xref:Xamarin.Forms.VisualElement.Width) e [ `Height` ](xref:Xamarin.Forms.VisualElement.Height) propriedades definidas por `VisualElement` têm o padrão "fictícios" valores de &ndash;1. Somente quando um elemento foi dimensionado e acomodado em layout essas propriedades refletirá o tamanho real do elemento em unidades independentes de dispositivo. Esse tamanho inclui qualquer `Padding` definido no elemento, mas não o `Margin`.

Um elemento visual é acionado o [ `SizeChanged` ](xref:Xamarin.Forms.VisualElement.SizeChanged) evento quando seu `Width` ou `Height` foi alterado. O [ **WhatSize** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/WhatSize) exemplo usa esse evento para exibir o tamanho da tela do programa.

## <a name="metrical-sizes"></a>Tamanhos de métrica

O [ **MetricalBoxView** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/MetricalBoxView) usa [ `WidthRequest` ](xref:Xamarin.Forms.VisualElement.WidthRequest) e [ `HeightRequest` ](xref:Xamarin.Forms.VisualElement.HeightRequest) para exibir um `BoxView` uma polegada tall e outro centímetro amplo.

## <a name="estimated-font-sizes"></a>Tamanhos de fonte estimado

O [ **tamanhos de fonte** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/FontSizes) exemplo mostra como usar a regra de 160 unidades-à-polegadas para especificar tamanhos de fonte em unidades de pontos. A consistência visual entre as plataformas usando essa técnica é melhor do que `Device.GetNamedSize`.

## <a name="fitting-text-to-available-size"></a>Ajuste de texto tamanho disponível

É possível ajustar um bloco de texto dentro de um retângulo determinado pelo cálculo de um `FontSize` do `Label` usando os seguintes critérios:

- Espaçamento entre linhas é 120% do tamanho da fonte (130% nas plataformas Windows).
- Largura do caractere média é de 50% do tamanho da fonte.

O [ **EstimatedFontSize** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/EstimatedFontSize) que demonstra essa técnica. Este programa foi escrito antes do [ `Margin` ](xref:Xamarin.Forms.View.Margin) propriedade estava disponível, portanto, ele usa um [ `ContentView` ](xref:Xamarin.Forms.ContentView) com um [ `Padding` ](xref:Xamarin.Forms.Layout.Padding) configuração para simular um margem.

[![Captura de tela tripla do tamanho da fonte estimado](images/ch05fg07-small.png "texto ajustar ao tamanho disponível")](images/ch05fg07-large.png#lightbox "texto ajustar ao tamanho disponível")

## <a name="a-fit-to-size-clock"></a>Um relógio de tamanho de ajuste

O [ **FitToSizeClock** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/FitToSizeClock) exemplo demonstra como usar [ `Device.StartTimer` ](xref:Xamarin.Forms.Device.StartTimer(System.TimeSpan,System.Func{System.Boolean})) para iniciar um timer que periodicamente notifica o aplicativo quando é hora de atualizar o relógio. O tamanho da fonte é definido como um sexto da largura da página para tornar a exibição tão grandes quanto possível.

## <a name="accessibility-issues"></a>Problemas de acessibilidade

O **EstimatedFontSize** programa e o **FitToSizeClock** os dois programas contêm uma falha sutil: Se o usuário altera as configurações de acessibilidade do telefone no Android ou Windows 10 Mobile, o programa não é mais possível estimar quanto o texto é renderizado com base no tamanho da fonte. O [ **AccessibilityTest** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/AccessibilityTest) que demonstra esse problema.

## <a name="empirically-fitting-text"></a>Empiricamente ajustando o texto

Outra maneira de ajustar o texto a um retângulo é empiricamente calcular o tamanho do texto renderizado e ajustá-lo para cima ou para baixo. O programa nas chamadas de livro [ `GetSizeRequest` ](xref:Xamarin.Forms.VisualElement.GetSizeRequest(System.Double,System.Double)) em um elemento visual para obter o tamanho do elemento desejado. Que método foi substituído e programas em vez disso, devem chamar [ `Measure` ](xref:Xamarin.Forms.VisualElement.Measure(System.Double,System.Double,Xamarin.Forms.MeasureFlags)).

Para um `Label`, o primeiro argumento deve ser a largura do contêiner (para permitir o encapsulamento), enquanto o segundo argumento deve ser definido para `Double.PositiveInfinity` para tornar a altura irrestrita. O [ **EmpiricalFontSize** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/EmpiricalFontSize) que demonstra essa técnica.



## <a name="related-links"></a>Links relacionados

- [Capítulo 5 de texto completo (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch05-Apr2016.pdf)
- [Exemplos do capítulo 5](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05)
- [Capítulo 5 F# amostras](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/FS)
