---
title: Elementos gráficos de SkiaSharp em Xamarin.Forms
description: SkiaSharp é um sistema de gráficos em 2D para .NET e c# com o engine gráficos Skia de código-fonte aberto que é amplamente usado em produtos do Google. Este guia explica como usar SkiaSharp para gráficos 2D em seus aplicativos xamarin. Forms.
ms.prod: xamarin
ms.assetid: 2C348BEA-81DF-4794-8857-EB1DFF5E11DB
author: davidbritch
ms.author: dabritch
ms.date: 09/11/2017
ms.openlocfilehash: 9cd5a25c598a25500aee595439aeecd648d50526
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/07/2018
ms.locfileid: "53055835"
---
# <a name="skiasharp-graphics-in-xamarinforms"></a>Elementos gráficos de SkiaSharp em Xamarin.Forms

[![Baixar exemplo](~/media/shared/download.png) baixar o exemplo](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)

_Usar SkiaSharp para gráficos 2D em seus aplicativos xamarin. Forms_

SkiaSharp é um sistema de gráficos em 2D para .NET e c# com o engine gráficos Skia de código-fonte aberto que é amplamente usado em produtos do Google. Você pode usar SkiaSharp em seus aplicativos xamarin. Forms para desenhar o texto, bitmaps e gráficos vetoriais 2D. Consulte a [desenho 2D](~/graphics-games/skiasharp/index.md) guia para obter mais informações gerais sobre a biblioteca de SkiaSharp e alguns outros tutoriais.

Este guia pressupõe que você esteja familiarizado com a programação do xamarin. Forms.

> [!VIDEO https://channel9.msdn.com/Events/Xamarin/Xamarin-University-Presents-Webinar-Series/SkiaSharp-Graphics-for-XamarinForms/player]

**Webinar: SkiaSharp para xamarin. Forms**

## <a name="skiasharp-preliminaries"></a>Etapas preliminares de SkiaSharp

SkiaSharp para xamarin. Forms é empacotado como um pacote do NuGet. Depois de criar uma solução xamarin. Forms no Visual Studio ou Visual Studio para Mac, você pode usar o Gerenciador de pacotes do NuGet para pesquisar o **SkiaSharp.Views.Forms** empacotar e adicioná-lo à sua solução. Se você marcar a **referências** seção de cada projeto após a adição de SkiaSharp, você pode ver que várias **SkiaSharp** bibliotecas foram adicionadas para cada um dos projetos na solução.

Se seu aplicativo xamarin. Forms tem como alvo iOS, use a página de propriedades do projeto para alterar o destino de implantação mínimo para o iOS 8.0.

Em qualquer página do c# que usa SkiaSharp você desejará incluir uma `using` diretriz para a [ `SkiaSharp` ](xref:SkiaSharp) namespace, que abrange todos os de SkiaSharp classes, estruturas e enumerações que você usará em seus elementos gráficos a programação. Você também pode uma `using` diretriz para a [ `SkiaSharp.Views.Forms` ](xref:SkiaSharp.Views.Forms) namespace para as classes específicas para xamarin. Forms. Isso é um quantidade menor espaço para nome, com a classe mais importantes sendo [ `SKCanvasView` ](xref:SkiaSharp.Views.Forms.SKCanvasView). Essa classe deriva o xamarin. Forms `View` de classe e hospeda sua saída de gráficos de SkiaSharp.

> [!IMPORTANT]
> O `SkiaSharp.Views.Forms` namespace também contém um `SKGLView` classe que deriva de `View` mas utiliza o OpenGL para renderização de gráficos. Para fins de simplicidade, este guia restringe-se ao `SKCanvasView`, mas o uso `SKGLView` em vez disso, é bastante semelhante.

## <a name="skiasharp-drawing-basicsbasicsindexmd"></a>[Noções básicas de desenho do SkiaSharp](basics/index.md)

Algumas das estimativas de gráficos mais simples, que você pode desenhar com SkiaSharp são retângulos, elipses e círculos. Exibir esses números, você aprenderá sobre as coordenadas de SkiaSharp, tamanhos e cores. A exibição de texto e bitmaps é mais complexa, mas esses artigos também introduzem essas técnicas.

## <a name="skiasharp-lines-and-pathspathsindexmd"></a>[Caminhos e linhas de SkiaSharp](paths/index.md)

Um caminho gráfico é uma série de linhas retas conectadas e curvas. Caminhos podem tracejados, preenchidos, ou ambos. Este artigo abrange muitos aspectos do desenho de linha, incluindo o traço extremidades e uniões e tracejada e as linhas pontilhadas, mas parar com pouca geometrias de curva.

## <a name="skiasharp-transformstransformsindexmd"></a>[Transformações de SkiaSharp](transforms/index.md)

As transformações permitem que os objetos gráficos ser uniformemente traduzido, dimensionado, girados ou inclinados. Este artigo também mostra como você pode usar uma matriz de transformação de 3 por 3 padrão para criar transformações não afins e aplicar transformações aos caminhos.

## <a name="skiasharp-curves-and-pathscurvesindexmd"></a>[Curvas e caminhos de SkiaSharp](curves/index.md)

A exploração dos caminhos continua com adicionando curvas a um objeto de caminho e explorar outros recursos avançados de caminho. Você verá como você pode especificar um caminho inteiro em uma cadeia de caracteres de texto conciso, como usar efeitos de caminho e como se aprofundar em elementos internos do caminho.

## <a name="skiasharp-bitmapsbitmapsindexmd"></a>[Bitmaps do SkiaSharp](bitmaps/index.md)

Os bitmaps são matrizes retangulares de bits que correspondem aos pixels de um dispositivo de vídeo. Esta série de artigos mostra como carregar, salvar, exibir, criar, desenhar em, animar e acessar os bits de SkiaSharp bitmaps.

## <a name="skiasharp-effectseffectsindexmd"></a>[Efeitos de SkiaSharp](effects/index.md)

Os efeitos são propriedades que alteram a exibição normal de elementos gráficos, incluindo gradientes linear e circulares, lado a lado de bitmap, misturar modos de desfoque e outras pessoas.

## <a name="related-links"></a>Links relacionados

- [APIs de SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (amostra)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
- [SkiaSharp com xamarin. Forms Webinar (vídeo)](https://channel9.msdn.com/Events/Xamarin/Xamarin-University-Presents-Webinar-Series/SkiaSharp-Graphics-for-XamarinForms)
