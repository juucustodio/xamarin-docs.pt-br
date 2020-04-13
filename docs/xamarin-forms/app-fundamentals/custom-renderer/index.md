---
title: Renderizadores personalizados do Xamarin.Forms
description: Renderizadores personalizados substituem a renderização dos controles nativos em cada plataforma, para personalizar a aparência e o comportamento de controles do Xamarin.Forms.
ms.prod: xamarin
ms.assetid: BF1CF23A-3BC9-4226-92E6-DAEEB91422F1
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/03/2019
ms.openlocfilehash: b87e713f89951d03408fa559bcf6e02cdae65e28
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "74824247"
---
# <a name="xamarinforms-custom-renderers"></a>Renderizadores personalizados do Xamarin.Forms

_As interfaces de usuário do Xamarin.Forms são renderizadas usando os controles nativos da plataforma de destino, permitindo que os aplicativos Xamarin.Forms mantenham a aparência e a sensação apropriadas para cada plataforma. Renderizadores personalizados permitem que os desenvolvedores sobreponham esse processo para personalizar a aparência e o comportamento dos controles Xamarin.Forms em cada plataforma._

## <a name="introduction-to-custom-renderers"></a>[Introdução aos renderizadores personalizados](introduction.md)

Renderizadores personalizados fornecem uma abordagem eficiente para personalizar a aparência e o comportamento de controles do Xamarin.Forms. Eles podem ser usados para pequenas alterações de estilo ou personalização sofisticada de comportamento e de layout específico da plataforma. Este artigo fornece uma introdução aos renderizadores personalizados e descreve o processo de criação de um renderizador personalizado.

## <a name="renderer-base-classes-and-native-controls"></a>[Classes de base de renderizadores e controles nativos](renderers.md)

Cada um dos controles do Xamarin.Forms tem um renderizador que o acompanha para cada plataforma que cria uma instância de um controle nativo. Este artigo lista as classes de renderizador e controle nativo que implementam cada página, layout, exibição e célula do Xamarin.Forms.

## <a name="customizing-an-entry"></a>[Personalizar uma entrada](entry.md)

O controle Xamarin.Forms [`Entry`](xref:Xamarin.Forms.Entry) permite que uma única linha de texto seja editada. Este artigo demonstra como criar um renderizador personalizado para o controle `Entry`, permitindo que os desenvolvedores substituam a renderização nativa padrão com sua própria personalização específica a uma plataforma.

## <a name="customizing-a-contentpage"></a>[Personalizar um ContentPage](contentpage.md)

A [`ContentPage`](xref:Xamarin.Forms.ContentPage) é um elemento visual que exibe uma única visão e ocupa a maior parte da tela. Este artigo demonstra como criar um renderizador personalizado para a página `ContentPage`, permitindo que os desenvolvedores substituam a renderização nativa padrão com sua própria personalização específica a uma plataforma.

## <a name="customizing-a-map"></a>[Personalizar uma mapa](map/index.md)

O Xamarin.Forms.Maps fornece uma abstração multiplataforma para a exibição de mapas que usam as APIs de mapa nativo em cada plataforma, a fim de fornecer uma experiência de mapa rápida e familiar para os usuários. Este tópico demonstra como criar renderizadores personalizados para o controle `Map`, permitindo que os desenvolvedores substituam a renderização nativa padrão com sua própria personalização específica a uma plataforma.

## <a name="customizing-a-listview"></a>[Personalizar uma ListView](listview.md)

A Xamarin.Forms [`ListView`](xref:Xamarin.Forms.ListView) é uma exibição que exibe uma coleção de dados como uma lista vertical. Este artigo demonstra como criar um renderizador personalizado que encapsula os controles de lista e layouts de célula nativa específicos a uma plataforma, permitindo mais controle sobre o desempenho do controle de lista nativo.

## <a name="customizing-a-viewcell"></a>[Personalizar uma ViewCell](viewcell.md)

A Xamarin.Forms [`ViewCell`](xref:Xamarin.Forms.ViewCell) é uma célula que [`ListView`](xref:Xamarin.Forms.ListView) [`TableView`](xref:Xamarin.Forms.TableView)pode ser adicionada a uma ou , que contém uma visão definida pelo desenvolvedor. Este artigo demonstra como criar um renderizador personalizado para um `ViewCell` hospedado dentro de um controle `ListView` do Xamarin.Forms. Isso impede que os cálculos de layout do Xamarin.Forms sejam chamados repetidamente durante a rolagem de `ListView`.

## <a name="customizing-a-webview"></a>[Personalizando uma WebView](hybridwebview.md)

A Xamarin.Forms [`WebView`](xref:Xamarin.Forms.WebView) é uma exibição que exibe conteúdo web e HTML em seu aplicativo. Este artigo explica como criar um renderizador personalizado que estende o código para permitir que o `WebView` código C# seja invocado a partir de JavaScript.

## <a name="implementing-a-view"></a>[Implementar uma exibição](view.md)

Xamarin.Forms controles personalizados de interfaces [`View`](xref:Xamarin.Forms.View) de usuário devem derivar da classe, que é usada para colocar layouts e controles na tela. Este artigo demonstra como criar um renderizador personalizado para um controle personalizado do Xamarin.Forms, que é usado para exibir uma transmissão de vídeo de prévia com a câmera do dispositivo.

## <a name="implementing-a-video-player"></a>[Implementando um player de vídeo](video-player/index.md)

Este artigo mostra como gravar renderizadores para implementar um controle personalizado `VideoPlayer` que pode reproduzir vídeos da Web, vídeos inseridos como recursos de aplicativo ou vídeos armazenados na biblioteca de vídeos no dispositivo do usuário. Várias técnicas são demonstradas, incluindo a implementação de métodos e de propriedades vinculáveis somente leitura.
