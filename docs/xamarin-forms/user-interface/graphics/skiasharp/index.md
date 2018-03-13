---
title: Usando SkiaSharp em xamarin. Forms
description: "Use SkiaSharp para gráficos 2D em seus aplicativos xamarin. Forms"
ms.topic: article
ms.prod: xamarin
ms.assetid: 2C348BEA-81DF-4794-8857-EB1DFF5E11DB
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 09/11/2017
ms.openlocfilehash: eaebd8cbae996e9a5792d0a4898fafb72bdded47
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2018
---
# <a name="using-skiasharp-in-xamarinforms"></a>Usando SkiaSharp em xamarin. Forms

_Use SkiaSharp para gráficos 2D em seus aplicativos xamarin. Forms_

SkiaSharp é um sistema de gráficos 2D para .NET e c# ativada pelo mecanismo de gráficos Skia de código-fonte aberto que é usado extensivamente em produtos do Google. Você pode usar SkiaSharp em seus aplicativos xamarin. Forms para desenhar o texto, bitmaps e gráfico vetorial 2D. Consulte o [desenho 2D](~/graphics-games/skiasharp/index.md) guia para obter mais informações sobre a biblioteca de SkiaSharp e alguns outros tutoriais.

Este guia pressupõe que você esteja familiarizado com a programação do xamarin. Forms.

> [!VIDEO https://channel9.msdn.com/Events/Xamarin/Xamarin-University-Presents-Webinar-Series/SkiaSharp-Graphics-for-XamarinForms/player]

**Webinar: SkiaSharp para xamarin. Forms**

## <a name="skiasharp-preliminaries"></a>Etapas preliminares a SkiaSharp

SkiaSharp para xamarin. Forms é empacotado como um pacote do NuGet. Depois de criar uma solução xamarin. Forms no Visual Studio ou Visual Studio para Mac, você pode usar o NuGet package manager para procurar o **SkiaSharp.Views.Forms** empacotar e adicioná-lo à sua solução. Se você verificar o **referências** seção de cada projeto depois de adicionar SkiaSharp, você pode ver que várias **SkiaSharp** bibliotecas foram adicionadas para cada um dos projetos na solução.

Se seu aplicativo xamarin. Forms destinos iOS, use a página de propriedades do projeto para alterar o destino de implantação mínimo para o iOS 8.0.

Em qualquer página do c# que usa SkiaSharp você vai querer incluir um `using` diretiva para o [ `SkiaSharp` ](https://developer.xamarin.com/api/namespace/SkiaSharp/) namespace, que abrange todos os a SkiaSharp classes, estruturas e enumerações que você usará em gráficos programação. Você também deverá uma `using` diretiva para o [ `SkiaSharp.Views.Forms` ](https://developer.xamarin.com/api/namespace/SkiaSharp.Views.Forms/) namespace para as classes específicas para xamarin. Forms. Isso é um quantidade menor do espaço para nome, com a classe sendo a mais importante [ `SKCanvasView` ](https://developer.xamarin.com/api/type/SkiaSharp.Views.Forms.SKCanvasView/). Essa classe é derivada do xamarin. Forms `View` classe e hospeda a saída de gráficos SkiaSharp.

> [!IMPORTANT]
> O `SkiaSharp.Views.Forms` namespace também contém um `SKGLView` classe que deriva de `View` , mas usa OpenGL para processamento de gráficos. Para fins de simplicidade, este guia se restringe a `SKCanvasView`, mas usando `SKGLView` em vez disso, é bastante semelhante.

## <a name="skiasharp-drawing-basicsbasicsindexmd"></a>[Noções básicas de desenho do SkiaSharp](basics/index.md)

Alguns dos valores de elementos gráficos mais simples, que é possível desenhar com SkiaSharp são retângulos, elipses e círculos. Exibir esses valores, você aprenderá sobre coordenadas SkiaSharp, tamanhos e cores.

## <a name="skiasharp-lines-and-pathspathsindexmd"></a>[Caminhos e linhas de SkiaSharp](paths/index.md)

Um caminho gráfico é uma série de linhas retas conectadas e curvas. Caminhos podem ser traçados, preenchido, ou ambos. Este tópico abrange vários aspectos do desenho em linha, incluindo termina de traço e junções e tracejada e linhas pontilhadas, mas parar com pouca geometrias curva.

## <a name="skiasharp-transformstransformsindexmd"></a>[Transformações de SkiaSharp](transforms/index.md)

Transformações permite que os objetos gráficos ser uniformemente convertido, escala, girada ou em horários diferentes. Este artigo também mostra como você pode usar uma matriz de transformação de 3 por 3 padrão para criar transformações não afins e aplicar transformações aos caminhos.

## <a name="skiasharp-curves-and-pathscurvesindexmd"></a>[Curvas e caminhos de SkiaSharp](curves/index.md)

A exploração de caminhos continua com adicionando curvas a um objeto de caminho e explorar outros recursos avançados de caminho. Você verá como você pode especificar um caminho completo em uma cadeia de caracteres de texto concisa, como usar os efeitos de caminho e como examinar os recursos internos de caminho.


## <a name="related-links"></a>Links relacionados

- [APIs de SkiaSharp](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (sample)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/SkiaSharpFormsDemos/)
- [SkiaSharp com xamarin. Forms Webinar (vídeo)](https://channel9.msdn.com/Events/Xamarin/Xamarin-University-Presents-Webinar-Series/SkiaSharp-Graphics-for-XamarinForms)
