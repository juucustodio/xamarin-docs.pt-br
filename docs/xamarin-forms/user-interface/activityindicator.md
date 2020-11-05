---
title: Indicador de atividade em Xamarin.Forms
description: O controle ActivityIndicator indica aos usuários que o aplicativo está envolvido em uma atividade demorada, sem dar qualquer indicação de progresso. Este artigo explica como usar um ActivityIndicator em XAML e código.
ms.prod: xamarin
ms.assetid: 4CEED02D-5CA3-4C3A-B7ED-3193FC272261
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 07/10/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 739fc90eda513e4627f5804283bdcab9c0cbacb4
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93373114"
---
# <a name="no-locxamarinforms-activityindicator"></a>Xamarin.Forms ActivityIndicator
[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](/samples/xamarin/xamarin-forms-samples/userinterface-activityindicatordemos/)

O Xamarin.Forms [`ActivityIndicator`](xref:Xamarin.Forms.ActivityIndicator) controle exibe uma animação para mostrar que o aplicativo está envolvido em uma atividade demorada. Ao contrário do [`ProgressBar`](xref:Xamarin.Forms.ProgressBar) , o `ActivityIndicator` não fornece nenhuma indicação de progresso. O `ActivityIndicator` herda de [`View`](xref:Xamarin.Forms.View) .

As capturas de tela a seguir mostram um `ActivityIndicator` controle no Ios e no Android:

![Captura de tela de ActivityIndicator no iOS e Android](activityindicator-images/activityindicators-default.png "Captura de tela de ActivityIndicator no iOS e Android")

O `ActivityIndicator` controle define as seguintes propriedades:

* [`Color`](xref:Xamarin.Forms.ActivityIndicator.Color) é um `Color` valor que define a cor de exibição do `ActivityIndicator` .
* [`IsRunning`](xref:Xamarin.Forms.ActivityIndicator.IsRunning) é um `bool` valor que indica se o `ActivityIndicator` deve estar visível e animado ou oculto. Quando o valor é `false` o `ActivityIndicator` não está visível.

Essas propriedades são apoiadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, o que significa que o `ActivityIndicator` pode ser estilizado e ser o destino de associações de dados.

## <a name="create-an-activityindicator"></a>Criar um ActivityIndicator

A `ActivityIndicator` classe pode ser instanciada em XAML. Sua `IsRunning` propriedade determina se o controle é visível e animado. A `IsRunning` propriedade usa como padrão `false` . O exemplo a seguir mostra como criar uma instância de `ActivityIndicator` em XAML com o `IsRunning` conjunto de propriedades opcional:

```xaml
<ActivityIndicator IsRunning="true" />
```

Um `ActivityIndicator` também pode ser criado no código:

```csharp
ActivityIndicator activityIndicator = new ActivityIndicator { IsRunning = true };
```

## <a name="activityindicator-appearance-properties"></a>Propriedades de aparência do ActivityIndicator

A `Color` propriedade define a `ActivityIndicator` cor. O exemplo a seguir mostra como criar uma instância de `ActivityIndicator` em XAML com o `Color` conjunto de propriedades:

```xaml
<ActivityIndicator Color="Orange" />
```

A `Color` propriedade também pode ser definida ao criar um `ActivityIndicator` no código:

```csharp
ActivityIndicator activityIndicator = new ActivityIndicator { Color = Color.Orange };
```

As capturas de tela a seguir mostram o `ActivityIndicator` com a `Color` propriedade definida como `Color.Orange` em Ios e Android:

![Captura de tela de ActivityIndicator com estilo em iOS e Android](activityindicator-images/activityindicators-styled.png "Captura de tela de ActivityIndicator com estilo em iOS e Android")

## <a name="related-links"></a>Links relacionados

* [Demonstrações do ActivityIndicator](/samples/xamarin/xamarin-forms-samples/userinterface-activityindicatordemos/)
* [ProgressBar](~/xamarin-forms/user-interface/progressbar.md)