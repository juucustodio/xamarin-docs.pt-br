---
title: Indicador de atividade no Xamarin. Forms
description: O controle ActivityIndicator indica aos usuários que o aplicativo está envolvido em uma atividade demorada, sem dar qualquer indicação de progresso. Este artigo explica como usar um ActivityIndicator em XAML e código.
ms.prod: xamarin
ms.assetid: 4CEED02D-5CA3-4C3A-B7ED-3193FC272261
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 07/10/2019
ms.openlocfilehash: e13a46e1022f4e33ace6f9f19bb5cea5d1ac784b
ms.sourcegitcommit: c6e56545eafd8ff9e540d56aba32aa6232c5315f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/02/2019
ms.locfileid: "68739164"
---
# <a name="xamarinforms-activityindicator"></a>ActivityIndicator Xamarin. Forms
[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-activityindicatordemos/)

O Xamarin. Forms[`ActivityIndicator`](xref:Xamarin.Forms.ActivityIndicator) é um controle que exibe uma animação para mostrar que o aplicativo está envolvido em uma atividade demorada. Ao contrário [`ProgressBar`](xref:Xamarin.Forms.ProgressBar)do, `ActivityIndicator` o não fornece nenhuma indicação de progresso. O `ActivityIndicator` herda de [`View`](xref:Xamarin.Forms.View).

A captura de tela a `ActivityIndicator` seguir mostra um controle no Ios e no Android:

![Captura de tela de ActivityIndicator no Ios e Android](activityindicator-images/activityindicators-default.png "Captura de tela de ActivityIndicator no Ios e Android")

O `ActivityIndicator` controle define as seguintes propriedades:

* [`IsRunning`](xref:Xamarin.Forms.ActivityIndicator.IsRunning)é um `bool` valor que indica se o `ActivityIndicator` deve estar visível e animado ou oculto. Quando o valor for `false` o `ActivityIndicator` , não estará visível.
* [`Color`](xref:Xamarin.Forms.ActivityIndicator.Color)é um `Color` valor que define a cor `ActivityIndicator`de exibição do.

Essas propriedades são apoiadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, o que significa que `ActivityIndicator` o pode ser estilizado e ser o destino de associações de dados.

## <a name="create-an-activityindicator"></a>Criar um ActivityIndicator

Um `ActivityIndicator` pode ser instanciado em XAML. Sua `IsRunning` propriedade pode ser definida para determinar se o controle está visível e animando. Se a `IsRunning` propriedade não estiver definida, o padrão `false` será e o `ActivityIndicator` não será visível. O exemplo a seguir mostra como criar uma `ActivityIndicator` instância de em XAML com `IsRunning` o conjunto de propriedades opcional:

```xaml
<ActivityIndicator IsRunning="true" />
```

Um `ActivityIndicator` também pode ser criado no código:

```csharp
ActivityIndicator activityIndicator = new ActivityIndicator { IsRunning = true };
```

## <a name="activityindicator-appearance-properties"></a>Propriedades de aparência do ActivityIndicator

A `Color` propriedade pode ser definida para definir a `ActivityIndicator` cor. O exemplo a seguir mostra como criar uma `ActivityIndicator` instância de em XAML `Color` com o conjunto de propriedades:

```xaml
<ActivityIndicator Color="Orange" />
```

A `Color` Propriedade também pode ser definida ao criar um `ActivityIndicator` no código:

```csharp
ActivityIndicator activityIndicator = new ActivityIndicator { Color = Color.Orange };
```

A captura de tela a `ActivityIndicator` seguir mostra `Color` o com a `Color.Orange` propriedade definida como em Ios e Android:

![Captura de tela de ActivityIndicator com estilo em Ios e Android](activityindicator-images/activityindicators-styled.png "Captura de tela de ActivityIndicator com estilo em Ios e Android")

## <a name="related-links"></a>Links relacionados

* [Demonstrações do ActivityIndicator](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-activityindicatordemos/)
* [ProgressBar](~/xamarin-forms/user-interface/progressbar.md)
