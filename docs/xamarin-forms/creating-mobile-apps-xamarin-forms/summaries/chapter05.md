---
title: Resumo do Capítulo 5. Lidar com tamanhos
description: 'Criando aplicativos móveis com Xamarin.Forms: Resumo do Capítulo 5. Lidar com tamanhos'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 486800E9-C09F-4B95-9AC2-C0F8FE563BCF
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2018
ms.openlocfilehash: c082bdb10732e42b37511cf050e50f46990a5b5b
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "70771150"
---
# <a name="summary-of-chapter-5-dealing-with-sizes"></a>Resumo do Capítulo 5. Lidar com tamanhos

[![Baixar](~/media/shared/download.png) amostra Baixar a amostra](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05)

> [!NOTE]
> As notas nesta página indicam áreas onde xamarin.forms divergiu do material apresentado no livro.

Vários tamanhos em Xamarin.Forms foram encontrados até agora:

- A altura da barra de status do iOS é de 20
- O `BoxView` tem uma largura padrão e altura de 40
- O `Padding` padrão `Frame` em a é 20
- O `Spacing` padrão `StackLayout` no é 6
- O `Device.GetNamedSize` método retorna um tamanho de fonte numérico

Esses tamanhos não são pixels. Em vez disso, são unidades independentes de dispositivos reconhecidas independentemente por cada plataforma.

## <a name="pixels-points-dps-dips-and-dius"></a>Pixels, pontos, dps, DIPs e DIUs

No início das histórias do Apple Mac e Microsoft Windows, os programadores trabalhavam em unidades de pixels. No entanto, o advento de displays de maior resolução exigiu uma abordagem mais virtualizada e abstrata das coordenadas de tela. No mundo Mac, os programadores trabalhavam em unidades de *pontos*, tradicionalmente 1/72 polegadas, enquanto os desenvolvedores do Windows usavam *unidades independentes de dispositivos* (DIUs) baseadas em 1/96 polegadas.

Os dispositivos móveis, no entanto, geralmente são mantidos muito mais próximos do rosto e têm uma resolução maior do que as telas de desktop, o que implica que uma maior densidade de pixels pode ser tolerada.

Programadores direcionados a dispositivos Apple iPhone e iPad continuam a trabalhar em unidades de *pontos,* mas há 160 desses pontos para a polegada. Dependendo do dispositivo, pode haver 1, 2 ou 3 pixels ao ponto.

Android é similar. Os programadores trabalham em unidades de *pixels independentes de densidade* (dps), e a relação entre dps e pixels é baseada em 160 dps para a polegada.

Os telefones Windows e dispositivos móveis também estabeleceram fatores de escala que implicam algo próximo de 160 unidades independentes de dispositivos para a polegada.

> [!NOTE]
> O Xamarin.Forms não suporta mais nenhum telefone ou dispositivo móvel baseado no Windows.

Em resumo, um programador Xamarin.Forms direcionado a telefones e tablets pode assumir que todas as unidades de medição são baseadas no seguinte critério:

- 160 unidades para a polegada, equivalente a
- 64 unidades para o centímetro

As propriedades [`Width`](xref:Xamarin.Forms.VisualElement.Width) somente [`Height`](xref:Xamarin.Forms.VisualElement.Height) leitura `VisualElement` e definidas por &ndash;têm valores padrão "mock" de 1. Somente quando um elemento tiver sido dimensionado e acomodado no layout, essas propriedades refletirão o tamanho real do elemento em unidades independentes de dispositivos. Este tamanho inclui `Padding` qualquer conjunto no `Margin`elemento, mas não o .

Um elemento visual [`SizeChanged`](xref:Xamarin.Forms.VisualElement.SizeChanged) dispara `Width` o `Height` evento quando é ou mudou. A amostra [**WhatSize**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/WhatSize) usa este evento para exibir o tamanho da tela do programa.

## <a name="metrical-sizes"></a>Tamanhos métricos

O [**MetricalBoxView**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/MetricalBoxView) usa [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) e [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) para exibir uma `BoxView` polegada de altura e um centímetro de largura.

## <a name="estimated-font-sizes"></a>Tamanhos estimados da fonte

A amostra [**FontSizes**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/FontSizes) mostra como usar a regra de 160 unidades para a polegada para especificar tamanhos de fonte em unidades de pontos. A consistência visual entre as plataformas que `Device.GetNamedSize`utilizam essa técnica é melhor do que .

## <a name="fitting-text-to-available-size"></a>Texto de montagem ao tamanho disponível

É possível encaixar um bloco de texto dentro de um `FontSize` retângulo específico calculando um dos `Label` seguintes critérios:

- O espaçamento da linha é de 120% do tamanho da fonte (130% nas plataformas Windows).
- A largura média do caractere é de 50% do tamanho da fonte.

A amostra [**EstimatedFontSize**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/EstimatedFontSize) demonstra essa técnica. Este programa foi [`Margin`](xref:Xamarin.Forms.View.Margin) escrito antes da propriedade estar [`ContentView`](xref:Xamarin.Forms.ContentView) disponível, então ele usa um com uma [`Padding`](xref:Xamarin.Forms.Layout.Padding) configuração para simular uma margem.

[![Captura de tela tripla do tamanho estimado da fonte](images/ch05fg07-small.png "Texto adequado ao tamanho disponível")](images/ch05fg07-large.png#lightbox "Texto adequado ao tamanho disponível")

## <a name="a-fit-to-size-clock"></a>Um relógio em tamanho real

A amostra [**FitToSizeClock**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/FitToSizeClock) demonstra o uso [`Device.StartTimer`](xref:Xamarin.Forms.Device.StartTimer(System.TimeSpan,System.Func{System.Boolean})) de um temporizador que notifica periodicamente o aplicativo na hora de atualizar o relógio. O tamanho da fonte é definido como um sexto da largura da página para tornar o display o maior possível.

## <a name="accessibility-issues"></a>Problemas de acessibilidade

O programa **EstimatedFontSize** e o programa **FitToSizeClock** contêm uma falha sutil: Se o usuário mudar as configurações de acessibilidade do telefone no Android ou Windows 10 Mobile, o programa não poderá mais estimar o tamanho do texto renderizado com base no tamanho da fonte. A amostra [**AccessibilityTest**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/AccessibilityTest) demonstra esse problema.

## <a name="empirically-fitting-text"></a>Texto empiricamente adequado

Outra maneira de encaixar o texto em um retângulo é calcular empiricamente o tamanho do texto renderizado e ajustá-lo para cima ou para baixo. O programa no [`GetSizeRequest`](xref:Xamarin.Forms.VisualElement.GetSizeRequest(System.Double,System.Double)) livro chama um elemento visual para obter o tamanho desejado do elemento. Esse método foi preterido, e os [`Measure`](xref:Xamarin.Forms.VisualElement.Measure(System.Double,System.Double,Xamarin.Forms.MeasureFlags))programas devem, em vez disso, chamar .

Para `Label`a , o primeiro argumento deve ser a largura do recipiente (para `Double.PositiveInfinity` permitir o embrulho), enquanto o segundo argumento deve ser definido para tornar a altura sem restrições. A [**amostra EmpiricalFontSize**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/EmpiricalFontSize) demonstra essa técnica.

## <a name="related-links"></a>Links relacionados

- [Capítulo 5 texto completo (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch05-Apr2016.pdf)
- [Capítulo 5 amostras](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05)
- [Capítulo 5 Amostras F#](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/FS)
