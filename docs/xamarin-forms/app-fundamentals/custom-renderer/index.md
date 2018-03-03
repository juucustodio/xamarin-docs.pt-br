---
title: Processadores personalizados
description: "Interfaces de usuário do xamarin. Forms renderizadas usando controles nativos da plataforma de destino, permitindo que os aplicativos xamarin. Forms manter a aparência apropriada para cada plataforma. Processadores personalizados permitem que os desenvolvedores substituem esse processo para personalizar a aparência e comportamento de controles xamarin. Forms em cada plataforma."
ms.topic: article
ms.prod: xamarin
ms.assetid: BF1CF23A-3BC9-4226-92E6-DAEEB91422F1
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/12/2018
ms.openlocfilehash: a92da0320addf1569c25ed05873aa11a198b1daa
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="custom-renderers"></a>Processadores personalizados

_Interfaces de usuário do xamarin. Forms renderizadas usando controles nativos da plataforma de destino, permitindo que os aplicativos xamarin. Forms manter a aparência apropriada para cada plataforma. Processadores personalizados permitem que os desenvolvedores substituem esse processo para personalizar a aparência e comportamento de controles xamarin. Forms em cada plataforma._

## <a name="introduction-to-custom-renderersintroductionmd"></a>[Introdução aos renderizadores personalizados](introduction.md)

Processadores personalizados fornecem uma abordagem avançada para personalizar a aparência e comportamento de controles xamarin. Forms. Eles podem ser usados para estilo pequenas alterações ou layout de específico de plataforma sofisticada e personalização de comportamento. Este artigo fornece uma introdução aos renderizadores personalizados e descreve o processo de criação de um renderizador personalizado.

## <a name="renderer-base-classes-and-native-controlsrenderersmd"></a>[Classes de base de processador e controles nativos](renderers.md)

Cada controle xamarin. Forms tem um processador que o acompanha para cada plataforma que cria uma instância de um controle nativo. Este artigo lista as classes de controle nativo que implementam cada página xamarin. Forms, layout, exibição e célula e processador.

## <a name="customizing-an-entryentrymd"></a>[Personalizando uma entrada](entry.md)

O xamarin. Forms [ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/) controle permite que uma única linha de texto a ser editado. Este artigo demonstra como criar um renderizador personalizado para o `Entry` controle, permitindo que os desenvolvedores substituir a renderização nativo padrão com sua próprias personalização específica de plataforma.

## <a name="customizing-a-contentpagecontentpagemd"></a>[Personalizando um ContentPage](contentpage.md)

Um [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/) é um elemento visual que exibe uma única e ocupa a maior parte da tela. Este artigo demonstra como criar um renderizador personalizado para o `ContentPage` página, permitindo que os desenvolvedores substituir a renderização nativo padrão com sua próprias personalização específica de plataforma.

## <a name="customizing-a-mapmapindexmd"></a>[Como personalizar um mapa](map/index.md)

Xamarin.Forms.Maps fornece uma abstração de plataforma cruzada para exibir mapas que usam o mapa nativo experiência de APIs em cada plataforma, para fornecer um mapa rápido e familiar para os usuários. Este tópico demonstra como criar renderizadores personalizados para o `Map` controle, permitindo que os desenvolvedores substituir a renderização nativo padrão com sua próprias personalização específica de plataforma.

## <a name="customizing-a-listviewlistviewmd"></a>[Personalizando uma ListView](listview.md)

Um xamarin. Forms [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) é uma exibição que exibe uma coleção de dados como uma lista vertical. Este artigo demonstra como criar um renderizador personalizado que encapsula os controles de lista específica de plataforma e layouts de célula nativo, permitindo mais controle sobre o desempenho do controle de lista nativo.

## <a name="customizing-a-viewcellviewcellmd"></a>[Personalizando um ViewCell](viewcell.md)

Um xamarin. Forms [ `ViewCell` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewCell/) é uma célula que pode ser adicionada a um [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) ou [ `TableView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TableView/), que contém uma exibição definida pelo desenvolvedor. Este artigo demonstra como criar um renderizador personalizado para um `ViewCell` que está hospedado dentro de um xamarin. Forms `ListView` controle. Isso impede que os cálculos de layout xamarin. Forms de ser chamado repetidamente durante `ListView` rolagem.

## <a name="implementing-a-viewviewmd"></a>[Implementando um modo de exibição](view.md)

Controles de interfaces de usuário personalizada do xamarin. Forms devem derivar de [ `View` ](https://developer.xamarin.com/api/type/Xamarin.Forms.View/) classe, que é usado para colocar controles na tela e layouts. Este artigo demonstra como criar um renderizador personalizado para um controle personalizado do xamarin. Forms que é usado para exibir um fluxo de vídeo de visualização da câmera do dispositivo.

## <a name="implementing-a-hybridwebviewhybridwebviewmd"></a>[Implementando um HybridWebView](hybridwebview.md)

Este artigo demonstra como criar um renderizador personalizado para um `HybridWebView` controle personalizado, que demonstra como melhorar os controles da web específico de plataforma para permitir que o código c# a ser chamado do JavaScript.

## <a name="implementing-a-video-playervideo-playerindexmd"></a>[Implementando um player de vídeo](video-player/index.md)

Este artigo mostra como gravar renderizadores para implementar um personalizado `VideoPlayer` controle que pode reproduzir vídeos da web, vídeos incorporados como recursos de aplicativo ou vídeos armazenados na biblioteca de vídeo no dispositivo do usuário. Várias técnicas são demonstradas, incluindo a implementação de métodos e propriedades vinculáveis somente leitura. 


## <a name="related-links"></a>Links relacionados

- [Efeitos](~/xamarin-forms/app-fundamentals/effects/index.md)
- [Processadores personalizados (vídeo do Xamarin University)](https://developer.xamarin.com/videos/cross-platform/xamarinforms-custom-renderers/)
- [Exemplo de renderizadores personalizado (vídeo do Xamarin University)](http://bit.ly/xf-customrenderer)
