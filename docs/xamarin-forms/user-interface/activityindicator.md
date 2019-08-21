---
title: Indicador de atividade no Xamarin. Forms
description: O controle ActivityIndicator indica aos usuários que o aplicativo está envolvido em uma atividade demorada, sem dar qualquer indicação de progresso. Este artigo explica como usar um ActivityIndicator em XAML e código.
ms.prod: xamarin
ms.assetid: 4CEED02D-5CA3-4C3A-B7ED-3193FC272261
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 07/10/2019
ms.openlocfilehash: de93927728271e9f0d9d2ef850b70eeeee249c84
ms.sourcegitcommit: 9178e2e689f027212ea3e623b556b312985d79fe
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/21/2019
ms.locfileid: "69658106"
---
# <a name="xamarinforms-activityindicator"></a>ActivityIndicator Xamarin. Forms
[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-activityindicatordemos/)

O controle Xamarin.[`ActivityIndicator`](xref:Xamarin.Forms.ActivityIndicator) Forms exibe uma animação para mostrar que o aplicativo está envolvido em uma atividade demorada. Ao contrário [`ProgressBar`](xref:Xamarin.Forms.ProgressBar)do, `ActivityIndicator` o não fornece nenhuma indicação de progresso. O `ActivityIndicator` herda de [`View`](xref:Xamarin.Forms.View).

As capturas de tela a `ActivityIndicator` seguir mostram um controle no Ios e no Android:

![Captura de tela de ActivityIndicator no Ios e Android](activityindicator-images/activityindicators-default.png "Captura de tela de ActivityIndicator no Ios e Android")

O `ActivityIndicator` controle define as seguintes propriedades:

* [`Color`](xref:Xamarin.Forms.ActivityIndicator.Color)é um `Color` valor que define a cor `ActivityIndicator`de exibição do.
* [`IsRunning`](xref:Xamarin.Forms.ActivityIndicator.IsRunning)é um `bool` valor que indica se o `ActivityIndicator` deve estar visível e animado ou oculto. Quando o valor é `false` o `ActivityIndicator` não está visível.

Essas propriedades são apoiadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, o que significa que `ActivityIndicator` o pode ser estilizado e ser o destino de associações de dados.

## <a name="create-an-activityindicator"></a>Criar um ActivityIndicator

A `ActivityIndicator` classe pode ser instanciada em XAML. Sua `IsRunning` propriedade determina se o controle é visível e animado. A `IsRunning` propriedade usa como `false`padrão. O exemplo a seguir mostra como criar uma `ActivityIndicator` instância de em XAML com `IsRunning` o conjunto de propriedades opcional:

```xaml
<ActivityIndicator IsRunning="true" />
```

Um `ActivityIndicator` também pode ser criado no código:

```csharp
ActivityIndicator activityIndicator = new ActivityIndicator { IsRunning = true };
```

## <a name="activityindicator-appearance-properties"></a>Propriedades de aparência do ActivityIndicator

A `Color` propriedade define a `ActivityIndicator` cor. O exemplo a seguir mostra como criar uma `ActivityIndicator` instância de em XAML `Color` com o conjunto de propriedades:

```xaml
<ActivityIndicator Color="Orange" />
```

A `Color` Propriedade também pode ser definida ao criar um `ActivityIndicator` no código:

```csharp
ActivityIndicator activityIndicator = new ActivityIndicator { Color = Color.Orange };
```

As capturas de tela a `ActivityIndicator` seguir mostram `Color` o com a `Color.Orange` propriedade definida como em Ios e Android:

![Captura de tela de ActivityIndicator com estilo em Ios e Android](activityindicator-images/activityindicators-styled.png "Captura de tela de ActivityIndicator com estilo em Ios e Android")

## <a name="related-links"></a>Links relacionados

* [Demonstrações do ActivityIndicator](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-activityindicatordemos/)
* [ProgressBar](~/xamarin-forms/user-interface/progressbar.md)
