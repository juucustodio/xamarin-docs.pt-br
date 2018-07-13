---
title: Renderizadores personalizados do xamarin. Forms
description: Renderizadores personalizados permitem que os desenvolvedores substituam a renderização dos controles nativos em cada plataforma, para personalizar a aparência e comportamento de controles do xamarin. Forms.
ms.prod: xamarin
ms.assetid: BF1CF23A-3BC9-4226-92E6-DAEEB91422F1
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/12/2018
ms.openlocfilehash: c7ae25688b2f8635a9a89318e0b307e58add7a5a
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/12/2018
ms.locfileid: "38998731"
---
# <a name="xamarinforms-custom-renderers"></a>Renderizadores personalizados do xamarin. Forms

_Interfaces de usuário do xamarin. Forms são renderizadas usando controles nativos da plataforma de destino, permitindo que os aplicativos xamarin. Forms manter a aparência apropriada para cada plataforma. Renderizadores personalizados permitem que os desenvolvedores substituem esse processo para personalizar a aparência e comportamento de controles do xamarin. Forms em cada plataforma._

## <a name="introduction-to-custom-renderersintroductionmd"></a>[Introdução ao renderizadores personalizados](introduction.md)

Renderizadores personalizados fornecem uma abordagem eficiente para personalizar a aparência e comportamento de controles do xamarin. Forms. Eles podem ser usados para estilo pequenas alterações ou layout de específico da plataforma sofisticada e personalização de comportamento. Este artigo fornece uma introdução aos renderizadores personalizados e descreve o processo para criar um renderizador personalizado.

## <a name="renderer-base-classes-and-native-controlsrenderersmd"></a>[Classes base do renderizador e controles nativos](renderers.md)

Todos os controles xamarin. Forms tem um renderizador que acompanha este artigo para cada plataforma que cria uma instância de um controle nativo. Este artigo lista as classes de controle nativo que implementam cada página do xamarin. Forms, layout, exibição e célula e o renderizador.

## <a name="customizing-an-entryentrymd"></a>[Personalizar uma entrada](entry.md)

O xamarin. Forms [ `Entry` ](xref:Xamarin.Forms.Entry) controle permite que uma única linha de texto a ser editado. Este artigo demonstra como criar um renderizador personalizado para o `Entry` controle, permitindo que os desenvolvedores substituam a renderização de nativa padrão com sua próprias personalização específicas da plataforma.

## <a name="customizing-a-contentpagecontentpagemd"></a>[Personalizar um ContentPage](contentpage.md)

Um [ `ContentPage` ](xref:Xamarin.Forms.ContentPage) é um elemento visual que exibe uma única exibição e ocupa a maior parte da tela. Este artigo demonstra como criar um renderizador personalizado para o `ContentPage` página, permitindo que os desenvolvedores substituam a renderização de nativa padrão com sua próprias personalização específicas da plataforma.

## <a name="customizing-a-mapmapindexmd"></a>[Personalizar uma mapa](map/index.md)

Xamarin.Forms.Maps fornece uma abstração de plataforma cruzada para a exibição de mapas que usam o mapa nativo a experiência de APIs em cada plataforma para fornecer um mapa rápido e familiar para os usuários. Este tópico demonstra como criar renderizadores personalizados para o `Map` controle, permitindo que os desenvolvedores substituam a renderização de nativa padrão com sua próprias personalização específicas da plataforma.

## <a name="customizing-a-listviewlistviewmd"></a>[Personalizar uma ListView](listview.md)

Um xamarin. Forms [ `ListView` ](xref:Xamarin.Forms.ListView) é uma exibição que exibe uma coleção de dados como uma lista vertical. Este artigo demonstra como criar um renderizador personalizado que encapsula os controles de lista específico da plataforma e layouts de célula nativo, permitindo mais controle sobre o desempenho do controle de lista nativo.

## <a name="customizing-a-viewcellviewcellmd"></a>[Personalizar uma ViewCell](viewcell.md)

Um xamarin. Forms [ `ViewCell` ](xref:Xamarin.Forms.ViewCell) é uma célula que pode ser adicionada a um [ `ListView` ](xref:Xamarin.Forms.ListView) ou [ `TableView` ](xref:Xamarin.Forms.TableView), que contém uma exibição definida pelo desenvolvedor. Este artigo demonstra como criar um renderizador personalizado para um `ViewCell` que é hospedado dentro de um xamarin. Forms `ListView` controle. Isso interrompe os cálculos de layout do xamarin. Forms seja chamado várias vezes durante `ListView` rolagem.

## <a name="implementing-a-viewviewmd"></a>[Implementar uma exibição](view.md)

Controles de interfaces de usuário personalizada do xamarin. Forms devem derivar de [ `View` ](xref:Xamarin.Forms.View) classe, que é usado para colocar os layouts e controles na tela. Este artigo demonstra como criar um renderizador personalizado para um controle personalizado do xamarin. Forms que é usado para exibir uma transmissão de vídeo de visualização da câmera do dispositivo.

## <a name="implementing-a-hybridwebviewhybridwebviewmd"></a>[Implementar um HybridWebView](hybridwebview.md)

Este artigo demonstra como criar um renderizador personalizado para um `HybridWebView` controle personalizado, que demonstra como aprimorar os controles específicos da plataforma web para permitir que o código c# a ser chamado do JavaScript.

## <a name="implementing-a-video-playervideo-playerindexmd"></a>[Implementando um player de vídeo](video-player/index.md)

Este artigo mostra como gravar renderizadores para implementar um personalizado `VideoPlayer` controle que pode reproduzir vídeos da web, vídeos inseridos como recursos de aplicativo ou vídeos armazenados na biblioteca de vídeos no dispositivo do usuário. Várias técnicas são demonstradas, incluindo a implementação de métodos e propriedades vinculáveis somente leitura.


## <a name="related-links"></a>Links relacionados

- [Efeitos](~/xamarin-forms/app-fundamentals/effects/index.md)
- [Renderizadores personalizados (vídeo do Xamarin University)](https://developer.xamarin.com/videos/cross-platform/xamarinforms-custom-renderers/)
- [Exemplo de renderizadores personalizados (vídeo do Xamarin University)](http://bit.ly/xf-customrenderer)
