---
title: Xamarin.FormsRenderizadores personalizados
description: Os renderizadores personalizados permitem que os desenvolvedores substituam a renderização dos controles nativos em cada plataforma, para personalizar a aparência e o comportamento dos Xamarin.Forms controles.
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: be281f9b7987a8d23ba6ac93f0771e432f277d45
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/28/2020
ms.locfileid: "84138912"
---
# <a name="xamarinforms-custom-renderers"></a>Xamarin.FormsRenderizadores personalizados

_As interfaces de usuário do Xamarin. Forms são renderizadas usando os controles nativos da plataforma de destino, permitindo que os Xamarin.Forms aplicativos mantenham a aparência apropriada para cada plataforma. Os renderizadores personalizados permitem que os desenvolvedores substituam esse processo para personalizar a aparência e o comportamento dos Xamarin.Forms controles em cada plataforma._

## <a name="introduction-to-custom-renderers"></a>[Introdução aos renderizadores personalizados](introduction.md)

Os renderizadores personalizados fornecem uma abordagem poderosa para personalizar a aparência e o comportamento dos Xamarin.Forms controles. Eles podem ser usados para pequenas alterações de estilo ou personalização sofisticada de comportamento e de layout específico da plataforma. Este artigo fornece uma introdução aos renderizadores personalizados e descreve o processo de criação de um renderizador personalizado.

## <a name="renderer-base-classes-and-native-controls"></a>[Classes base do processador e controles nativos](renderers.md)

Cada Xamarin.Forms controle tem um renderizador que acompanha para cada plataforma que cria uma instância de um controle nativo. Este artigo lista as classes de processador e controle nativo que implementam cada Xamarin.Forms página, layout, exibição e célula.

## <a name="customizing-an-entry"></a>[Personalizando uma entrada](entry.md)

O Xamarin.Forms [`Entry`](xref:Xamarin.Forms.Entry) controle permite que uma única linha de texto seja editada. Este artigo demonstra como criar um renderizador personalizado para o controle `Entry`, permitindo que os desenvolvedores substituam a renderização nativa padrão com sua própria personalização específica a uma plataforma.

## <a name="customizing-a-contentpage"></a>[Personalizando uma ContentPage](contentpage.md)

Um [`ContentPage`](xref:Xamarin.Forms.ContentPage) é um elemento visual que exibe uma única exibição e ocupa a maior parte da tela. Este artigo demonstra como criar um renderizador personalizado para a página `ContentPage`, permitindo que os desenvolvedores substituam a renderização nativa padrão com sua própria personalização específica a uma plataforma.

## <a name="customizing-a-map-pin"></a>[Personalizando um marcador de mapa](map-pin.md)

Xamarin.Forms. O Maps fornece uma abstração de plataforma cruzada para exibir mapas que usam as APIs de mapa nativas em cada plataforma, a fim de fornecer uma experiência de mapa rápida e familiar para os usuários. Este tópico demonstra como criar um renderizador personalizado para o `Map` controle, permitindo que os desenvolvedores substituam a renderização nativa padrão por sua própria personalização específica da plataforma.

## <a name="customizing-a-listview"></a>[Personalizando uma ListView](listview.md)

Um Xamarin.Forms [`ListView`](xref:Xamarin.Forms.ListView) é uma exibição que exibe uma coleção de dados como uma lista vertical. Este artigo demonstra como criar um renderizador personalizado que encapsula os controles de lista e layouts de célula nativa específicos a uma plataforma, permitindo mais controle sobre o desempenho do controle de lista nativo.

## <a name="customizing-a-viewcell"></a>[Personalizando um ViewCell](viewcell.md)

Um Xamarin.Forms [`ViewCell`](xref:Xamarin.Forms.ViewCell) é uma célula que pode ser adicionada a um [`ListView`](xref:Xamarin.Forms.ListView) ou [`TableView`](xref:Xamarin.Forms.TableView) , que contém uma exibição definida pelo desenvolvedor. Este artigo demonstra como criar um renderizador personalizado para um `ViewCell` que está hospedado dentro de um Xamarin.Forms `ListView` controle. Isso impede Xamarin.Forms que os cálculos de layout sejam chamados repetidamente durante a `ListView` rolagem.

## <a name="customizing-a-webview"></a>[Personalizando uma WebView](hybridwebview.md)

Um Xamarin.Forms [`WebView`](xref:Xamarin.Forms.WebView) é uma exibição que exibe conteúdo Web e HTML em seu aplicativo. Este artigo explica como criar um renderizador personalizado que estende o `WebView` para permitir que o código C# seja invocado do JavaScript.

## <a name="implementing-a-view"></a>[Implementando uma exibição](view.md)

Xamarin.Formsos controles de interfaces de usuário personalizados devem derivar da [`View`](xref:Xamarin.Forms.View) classe, que é usada para posicionar layouts e controles na tela. Este artigo demonstra como criar um renderizador personalizado para um Xamarin.Forms controle personalizado que é usado para exibir um fluxo de vídeo de visualização da câmera do dispositivo.

## <a name="implementing-a-video-player"></a>[Implementando um player de vídeo](video-player/index.md)

Este artigo mostra como gravar renderizadores para implementar um controle personalizado `VideoPlayer` que pode reproduzir vídeos da Web, vídeos inseridos como recursos de aplicativo ou vídeos armazenados na biblioteca de vídeos no dispositivo do usuário. Várias técnicas são demonstradas, incluindo a implementação de métodos e de propriedades vinculáveis somente leitura.
