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
ms.openlocfilehash: 3f30a77975a9f42380ecf7efd73426763ec83ef0
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="xaml-standard-preview-controls"></a>Controles do XAML padrão (visualização)

![Visualizar](~/media/shared/preview.png)

Esta página lista os controles de XAML padrão disponível na visualização, juntamente com seu controle xamarin. Forms equivalente.

Também é uma lista de controles que têm novos nomes de propriedade e enumeração em XAML padrão.

## <a name="controls"></a>Controles

<table style="width:300px">
  <tr><th>Xamarin.Forms</th><th>Padrão XAML</th></tr>
  <tr><td>Quadro</td><td>Borda</td></tr>
  <tr><td>Seletor</td><td>ComboBox</td></tr>
  <tr><td>ActivityIndicator</td><td>ProgressRing</td></tr>
  <tr><td>StackLayout</td><td>StackPanel</td></tr>
  <tr><td>Rotular</td><td>TextBlock</td></tr>
  <tr><td>Entrada</td><td>TextBox</td></tr>
  <tr><td>Alternar</td><td>ToggleSwitch</td></tr>
  <tr><td>ContentView</td><td>UserControl</td></tr>
</table>

## <a name="properties-and-enumerations"></a>Propriedades e enumerações

<table>
  <tr><th>Xamarin.Forms<br/>Controles com as propriedades atualizadas</th><th>Xamarin.Forms<br/>Propriedade ou Enum</th><th>Padrão XAML<br/>Equivalente</th></tr>
  <tr><td>Button, Entry, Label, DatePicker, Editor, SearchBar, TimePicker</td><td>TextColor</td><td>Primeiro plano</td></tr>
  <tr><td>VisualElement</td><td>BackgroundColor</td><td><i>Plano de fundo *</i></td></tr>
  <tr><td>Seletor de botão</td><td>BorderColor OutlineColor</td><td>BorderBrush</td></tr>
  <tr><td>Botão</td><td>BorderWidth</td><td>BorderThickness</td></tr>
  <tr><td>ProgressBar</td><td>Progresso</td><td>Valor</td></tr>
  <tr><td>Button, Entry, Label, Editor, SearchBar, Span, Font</td><td>FontAttributes<br/>Negrito, itálico, None</td><td>FontStyle<br/>Itálico, Normal</td></tr>
  <tr><td>Button, Entry, Label, Editor, SearchBar, Span, Font</td><td>FontAttributes</td><td><i>FontWeights *</i><br/>Negrito, Normal</td></tr>
  <tr><td>InputView</td><td>Teclado<br/>Padrão, a Url, número, telefone, texto, bate-papo, Email</td><td><i>InputScopeNameValue *</i><br/>Padrão, a Url, número, TelephoneNumber, texto, bate-papo, EmailNameOrAddress</td></tr>
  <tr><td>StackPanel</td><td>StackOrientation</td><td><i>Orientação *</i></td></tr>
</table>

> [!IMPORTANT]
> Itens marcados com * estão incompletos na visualização atual


## <a name="related-links"></a>Links relacionados

- [Visualização NuGet](https://aka.ms/xf-xamlstandard-nuget)
