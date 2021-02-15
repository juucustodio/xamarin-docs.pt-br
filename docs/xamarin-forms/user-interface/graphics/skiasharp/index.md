---
title: SkiaSharp gráficos em Xamarin.Forms
description: O SkiaSharp é um sistema gráfico 2D para .NET e C# equipado com o mecanismo de gráficos skia de software livre que é amplamente usado em produtos Google. Este guia explica como usar o SkiaSharp para gráficos 2D em seus Xamarin.Forms aplicativos.
ms.prod: xamarin
ms.assetid: 2C348BEA-81DF-4794-8857-EB1DFF5E11DB
author: davidbritch
ms.author: dabritch
ms.date: 09/11/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: b91d8f459992d33f417e99f5d92ece63f887f691
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93373426"
---
# <a name="skiasharp-graphics-in-no-locxamarinforms"></a>SkiaSharp gráficos em Xamarin.Forms

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Use SkiaSharp para gráficos 2D em seus Xamarin.Forms aplicativos_

O SkiaSharp é um sistema gráfico 2D para .NET e C# equipado com o mecanismo de gráficos skia de software livre que é amplamente usado em produtos Google. Você pode usar o SkiaSharp em seus Xamarin.Forms aplicativos para desenhar gráficos de vetor 2D, bitmaps e texto.

Este guia pressupõe que você esteja familiarizado com a Xamarin.Forms programação.

> [!VIDEO https://channel9.msdn.com/Events/Xamarin/Xamarin-University-Presents-Webinar-Series/SkiaSharp-Graphics-for-XamarinForms/player]

**Webinar: SkiaSharp para Xamarin.Forms**

## <a name="skiasharp-preliminaries"></a>SkiaSharp etapas preliminares

O SkiaSharp para Xamarin.Forms é empacotado como um pacote NuGet. Depois de criar uma Xamarin.Forms solução no Visual Studio ou Visual Studio para Mac, você pode usar o Gerenciador de pacotes NuGet para pesquisar o pacote **SkiaSharp. views. Forms** e adicioná-lo à sua solução. Se você marcar a seção **referências** de cada projeto depois de adicionar SkiaSharp, poderá ver que várias bibliotecas **SkiaSharp** foram adicionadas a cada um dos projetos na solução.

Se o seu aplicativo tiver como Xamarin.Forms destino o Ios, edite seu arquivo **info. plist** para alterar o destino de implantação mínimo para IOS 8,0.

Em qualquer página C# que usa SkiaSharp, você desejará incluir uma `using` diretiva para o [`SkiaSharp`](xref:SkiaSharp) namespace, que abrange todas as classes, estruturas e enumerações do SkiaSharp que você usará em sua programação de gráficos. Você também desejará uma `using` diretiva para o [`SkiaSharp.Views.Forms`](xref:SkiaSharp.Views.Forms) namespace para as classes específicas do Xamarin.Forms . Esse é um namespace muito menor, com a classe mais importante [`SKCanvasView`](xref:SkiaSharp.Views.Forms.SKCanvasView) . Essa classe deriva da Xamarin.Forms `View` classe e hospeda sua saída de gráficos do SkiaSharp.

> [!IMPORTANT]
> O `SkiaSharp.Views.Forms` namespace também contém uma `SKGLView` classe que deriva de `View` , mas usa OpenGL para renderizar elementos gráficos. Para fins de simplicidade, este guia se restringe a `SKCanvasView` , mas usar `SKGLView` em vez disso é bastante semelhante.

## <a name="skiasharp-drawing-basics"></a>[Noções básicas de desenho do SkiaSharp](basics/index.md)

Alguns dos números gráficos mais simples que você pode desenhar com SkiaSharp são círculos, ovais e retângulos. Ao exibir esses números, você aprenderá sobre coordenadas, tamanhos e cores do SkiaSharp. A exibição de texto e bitmaps é mais complexa, mas esses artigos também apresentam essas técnicas.

## <a name="skiasharp-lines-and-paths"></a>[Caminhos e linhas de SkiaSharp](paths/index.md)

Um caminho gráfico é uma série de linhas retas e curvas conectadas. Os caminhos podem ser traçados, preenchidos ou ambos. Este artigo abrange muitos aspectos do desenho de linha, incluindo extremidades de traço e junções, e linhas tracejadas e pontilhadas, mas interrompe a abreviação de geometrias de curva.

## <a name="skiasharp-transforms"></a>[Transformações de SkiaSharp](transforms/index.md)

As transformações permitem que os objetos gráficos sejam traduzidos de forma uniforme, dimensionados, girados ou distorcidos. Este artigo também mostra como você pode usar uma matriz de transformação padrão 3-por-3 para criar transformações não afim e aplicar transformações a caminhos.

## <a name="skiasharp-curves-and-paths"></a>[Curvas e caminhos de SkiaSharp](curves/index.md)

A exploração de caminhos continua com a adição de curvas a objetos de caminho e a exploração de outros recursos de caminho avançados. Você verá como é possível especificar um caminho inteiro em uma cadeia de texto concisa, como usar efeitos de caminho e como se aprofundar em elementos internos do caminho.

## <a name="skiasharp-bitmaps"></a>[Bitmaps do SkiaSharp](bitmaps/index.md)

Os bitmaps são matrizes retangulares de bits correspondentes aos pixels de um dispositivo de vídeo. Esta série de artigos mostra como carregar, salvar, exibir, criar, desenhar, animar e acessar os bits de bitmaps do SkiaSharp.

## <a name="skiasharp-effects"></a>[Efeitos de SkiaSharp](effects/index.md)

Effects são propriedades que alteram a exibição normal de elementos gráficos, incluindo gradientes lineares e circulares, bitmaps em blocos gráficos, modos de mesclagem, Desfoque e outros.

## <a name="related-links"></a>Links Relacionados

- [APIs do SkiaSharp](/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (exemplo)](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
- [SkiaSharp com Xamarin.Forms Webinar (vídeo)](https://channel9.msdn.com/Events/Xamarin/Xamarin-University-Presents-Webinar-Series/SkiaSharp-Graphics-for-XamarinForms)