---
title: Indicador de atividade no xamarin. Forms
description: O controle ActivityIndicator indica aos usuários que o aplicativo está envolvido em uma atividade demorada, sem fornecer nenhuma indicação de progresso. Este artigo explica como usar um ActivityIndicator em XAML e código.
ms.prod: xamarin
ms.assetid: 4CEED02D-5CA3-4C3A-B7ED-3193FC272261
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 07/10/2019
ms.openlocfilehash: abd8150e3aa4ec347c8d956004993340630208bf
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67837056"
---
# <a name="xamarinforms-activityindicator"></a>Xamarin. Forms ActivityIndicator
[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/ActivityIndicatorDemos)

O xamarin. Forms[ `ActivityIndicator` ](xref:Xamarin.Forms.ActivityIndicator) é um controle que exibe uma animação para mostrar que o aplicativo está envolvido em uma atividade demorada. Ao contrário do [ `ProgressBar` ](xref:Xamarin.Forms.ProgressBar), o `ActivityIndicator` não dá nenhuma indicação de progresso. O `ActivityIndicator` herda [ `View` ](xref:Xamarin.Forms.View).

A captura de tela a seguir mostra um `ActivityIndicator` controle no iOS e Android:

![Captura de tela de ActivityIndicator no iOS e Android](activityindicator-images/activityindicators-default.png "captura de tela de ActivityIndicator no iOS e Android")

O `ActivityIndicator` controle define as propriedades a seguir:

* [`IsRunning`](xref:Xamarin.Forms.ActivityIndicator.IsRunning) é um `bool` valor que indica se o `ActivityIndicator` deve ser visível e animação ou oculto. Quando o valor for `false` o `ActivityIndicator` não ficará visível.
* [`Color`](xref:Xamarin.Forms.ActivityIndicator.Color) é um `Color` valor que define a cor da tela do `ActivityIndicator`.

Essas propriedades têm o respaldo [ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty) objetos, o que significa que o `ActivityIndicator` podem ser estilizados e ser o destino de vinculações de dados.

## <a name="create-an-activityindicator"></a>Criar um ActivityIndicator

Um `ActivityIndicator` pode ser instanciada em XAML. Seu `IsRunning` propriedade pode ser definida para determinar se o controle está visível e animação. Se o `IsRunning` propriedade não está definida, o padrão será `false` e o `ActivityIndicator` não ficará visível. O exemplo a seguir mostra como instanciar uma `ActivityIndicator` no XAML com opcional `IsRunning` conjunto de propriedades:

```xaml
<ActivityIndicator IsRunning="true" />
```

Um `ActivityIndicator` também podem ser criados no código:

```csharp
ActivityIndicator activityIndicator = new ActivityIndicator { IsRunning = true };
```

## <a name="activityindicator-appearance-properties"></a>Propriedades de aparência ActivityIndicator

O `Color` propriedade pode ser definida para definir o `ActivityIndicator` cor. O exemplo a seguir mostra como instanciar uma `ActivityIndicator` no XAML com o `Color` conjunto de propriedades:

```xaml
<ActivityIndicator Color="Orange" />
```

O `Color` propriedade também pode ser definida durante a criação de um `ActivityIndicator` no código:

```csharp
ActivityIndicator activityIndicator = new ActivityIndicator { Color = Color.Orange };
```

A captura de tela a seguir mostra a `ActivityIndicator` com o `Color` propriedade definida como `Color.Orange` no iOS e Android:

![Captura de tela de ActivityIndicator com estilo definido no iOS e Android](activityindicator-images/activityindicators-styled.png "captura de tela de ActivityIndicator com estilo definido no iOS e Android")

## <a name="related-links"></a>Links relacionados

* [Demonstrações de ActivityIndicator](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/ActivityIndicatorDemos)
* [ProgressBar](~/xamarin-forms/user-interface/progressbar.md)
