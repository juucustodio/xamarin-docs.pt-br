---
title: Controles do XAML padrão (visualização)
description: Este artigo explora os controles XAML padrão disponíveis no xamarin. Forms.
ms.prod: xamarin
ms.assetid: 287E6631-D1C5-46C5-8905-AB53D34E365D
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 11/15/2017
ms.openlocfilehash: 1b01d0773f0c2150db575875b770957eb6452f41
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/08/2018
ms.locfileid: "35245560"
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

|Controles de xamarin. Forms com as propriedades atualizadas|Propriedade xamarin. Forms ou enum|XAML padrão equivalente|
|--- |--- |--- |
|Botão, entrada, rótulo, DatePicker, Editor, SearchBar, TimePicker|textColor|Primeiro plano|
|VisualElement|BackgroundColor|Plano de fundo *|
|Seletor de botão|BorderColor OutlineColor|BorderBrush|
|Botão|BorderWidth|BorderThickness|
|ProgressBar|Progresso|Valor|
|Botão, entrada, rótulo, Editor, SearchBar, alcance, fonte|FontAttributesBold, itálico, None|FontStyleItalic, Normal|
|Botão, entrada, rótulo, Editor, SearchBar, alcance, fonte|FontAttributes|FontWeights * negrito, Normal|
|InputView|KeyboardDefault, Url, número, telefone, texto, bate-papo, Email|InputScopeNameValue * padrão, Url, número, TelephoneNumber, texto, bate-papo, EmailNameOrAddress|
|StackPanel|StackOrientation|Orientação *|

> [!IMPORTANT]
> Itens marcados com * estão incompletos na visualização atual

## <a name="related-links"></a>Links relacionados

- [Visualização NuGet](https://aka.ms/xf-xamlstandard-nuget)
