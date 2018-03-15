---
title: "Controles do XAML padrão (visualização)"
description: "Como começar a explorar a visualização de padrão de XAML no xamarin. Forms"
ms.topic: article
ms.prod: xamarin
ms.assetid: 287E6631-D1C5-46C5-8905-AB53D34E365D
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 11/15/2017
ms.openlocfilehash: b044cb849f9a8e591a8db5907211a55f77d6e45f
ms.sourcegitcommit: 8e722d72c5d1384889f70adb26c5675544897b1f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/15/2018
---
# <a name="xaml-standard-preview-controls"></a>Controles do XAML padrão (visualização)

![Visualizar](~/media/shared/preview.png)

Esta página lista os controles de XAML padrão disponível na visualização, juntamente com seu controle xamarin. Forms equivalente.

Também é uma lista de controles que têm novos nomes de propriedade e enumeração em XAML padrão.

## <a name="controls"></a>Controles

|Xamarin.Forms|Padrão XAML|
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

|Xamarin.FormsControls com propriedades atualizadas|Xamarin.FormsProperty ou Enum|StandardEquivalent XAML|
|--- |--- |--- |
|Button, Entry, Label, DatePicker, Editor, SearchBar, TimePicker|TextColor|Primeiro plano|
|VisualElement|BackgroundColor|Plano de fundo *|
|Seletor de botão|BorderColor OutlineColor|BorderBrush|
|Botão|BorderWidth|BorderThickness|
|ProgressBar|Progresso|Valor|
|Button, Entry, Label, Editor, SearchBar, Span, Font|FontAttributesBold, itálico, None|FontStyleItalic, Normal|
|Button, Entry, Label, Editor, SearchBar, Span, Font|FontAttributes|FontWeights * negrito, Normal|
|InputView|KeyboardDefault, Url, número, telefone, texto, bate-papo, Email|InputScopeNameValue * padrão, Url, número, TelephoneNumber, texto, bate-papo, EmailNameOrAddress|
|StackPanel|StackOrientation|Orientação *|

> [!IMPORTANT]
> Itens marcados com * estão incompletos na visualização atual

## <a name="related-links"></a>Links relacionados

- [Visualização NuGet](https://aka.ms/xf-xamlstandard-nuget)
