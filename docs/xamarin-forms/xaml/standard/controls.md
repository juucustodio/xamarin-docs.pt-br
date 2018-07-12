---
title: Controles do XAML Standard (visualização)
description: Este artigo explora os controles XAML padrão disponíveis no xamarin. Forms.
ms.prod: xamarin
ms.assetid: 287E6631-D1C5-46C5-8905-AB53D34E365D
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 11/15/2017
ms.openlocfilehash: 1b01d0773f0c2150db575875b770957eb6452f41
ms.sourcegitcommit: 632955f8cdb80712abd8dcc30e046cb9c435b922
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2018
ms.locfileid: "38843937"
---
# <a name="xaml-standard-preview-controls"></a>Controles do XAML Standard (visualização)

![Visualizar](~/media/shared/preview.png)

Esta página lista os controles XAML padrão disponíveis na visualização, juntamente com seu controle equivalente do xamarin. Forms.

Também é uma lista de controles que têm novos nomes de propriedade e de enumeração no XAML padrão.

## <a name="controls"></a>Controles

|Xamarin.Forms|XAML Standard|
|--- |--- |
|Quadro|Borda|
|Seletor|ComboBox|
|ActivityIndicator|ProgressRing|
|StackLayout|StackPanel|
|Rotular|TextBlock|
|Entrada|TextBox|
|Alternar|ToggleSwitch|
|ContentView|UserControl|


## <a name="properties-and-enumerations"></a>Propriedades e enumerações

|Controles do xamarin. Forms com as propriedades atualizadas|Propriedade do xamarin. Forms ou enumeração|Equivalente XAML padrão|
|--- |--- |--- |
|Botão, entrada, rótulo, DatePicker, Editor, SearchBar, TimePicker|TextColor|Primeiro plano|
|VisualElement|BackgroundColor|Plano de fundo *|
|Seletor de botão|BorderColor, OutlineColor|BorderBrush|
|Botão|BorderWidth|BorderThickness|
|ProgressBar|Progresso|Valor|
|Botão, entrada, rótulo, Editor, SearchBar, Span, fonte|FontAttributesBold, itálico, None|FontStyleItalic, Normal|
|Botão, entrada, rótulo, Editor, SearchBar, Span, fonte|FontAttributes|FontWeights * negrito, Normal|
|InputView|KeyboardDefault, Url, número, telefone, texto, bate-papo, envie um Email|InputScopeNameValue * padrão, Url, número, TelephoneNumber, texto, bate-papo, EmailNameOrAddress|
|StackPanel|StackOrientation|Orientação *|

> [!IMPORTANT]
> Itens marcados com * estão incompletos na visualização atual

## <a name="related-links"></a>Links relacionados

- [NuGet de visualização](https://aka.ms/xf-xamlstandard-nuget)
