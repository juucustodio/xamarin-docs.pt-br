---
title: Fornecer comentários Hápticos no xamarin. IOS
description: Este documento descreve como fornecer comentários hápticos em um aplicativo xamarin. IOS. Ele aborda UIImpactFeedbackGenerator, UINotificationFeedbackGenerator e UISelectionFeedbackGenerator.
ms.prod: xamarin
ms.assetid: 888106D1-58F4-453F-BACC-91D51FA39C80
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: b2c381c59ba1574e80babc2c7e68535a3deffe35
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61384613"
---
# <a name="providing-haptic-feedback-in-xamarinios"></a>Fornecer comentários Hápticos no xamarin. IOS

<a name="Overview" />

## <a name="overview"></a>Visão geral

No iPhone 7 e iPhone 7 Além disso, o Apple incluiu novas respostas hápticos que fornecem maneiras adicionais para envolver fisicamente o usuário. Comentários hápticos (geralmente chamado simplesmente como Haptics) usa o sentido de toque (por meio de força, vibrações ou movimento) no design de Interface do usuário. Use essas novas opções de comentários táteis para obter a atenção do usuário e reforçar suas ações.

Os tópicos a seguir serão abordados em detalhes:

- [Sobre comentários Hápticos](#About-Haptic-Feedback)
- [UIImpactFeedbackGenerator](#UIImpactFeedbackGenerator)
- [UINotificationFeedbackGenerator](#UINotificationFeedbackGenerator)
- [UISelectionFeedbackGenerator](#UISelectionFeedbackGenerator)

<a name="About-Haptic-Feedback" />

## <a name="about-haptic-feedback"></a>Sobre comentários Hápticos

Vários elementos de interface do usuário internos já fornecem comentários hápticos como selecionadores, comutadores e os controles deslizantes. iOS 10 agora adiciona a capacidade de disparar programaticamente haptics usando uma subclasse concreta do `UIFeedbackGenerator` classe.

O desenvolvedor pode usar um dos seguintes `UIFeedbackGenerator` subclasses programaticamente os comentários hápticos do gatilho:

- `UIImpactFeedbackGenerator` – Use este gerador de comentários para complementar a uma ação ou uma tarefa como apresentar "thud" quando uma exibição entra em vigor ou se dois objetos na tela entrarem em conflito.
- `UINotificationFeedbackGenerator` – Use este gerador de comentários para notificações, como um tipo de conclusão, falhando ou qualquer outra ação de aviso.
- `UISelectionFeedbackGenerator` – Use este gerador de comentários de uma seleção ativamente a alteração, como um item de uma lista de separação.

<a name="UIImpactFeedbackGenerator" />

### <a name="uiimpactfeedbackgenerator"></a>UIImpactFeedbackGenerator

Use este gerador de comentários para complementar a uma ação ou uma tarefa como apresentar "thud" quando uma exibição entra em vigor ou se dois objetos na tela entrarem em conflito.

Use o seguinte código ao gatilho impacto comentários:

```csharp
using UIKit;
...

// Initialize feedback
var impact = new UIImpactFeedbackGenerator (UIImpactFeedbackStyle.Heavy);
impact.Prepare ();

// Trigger feedback
impact.ImpactOccurred ();
```

Quando o desenvolvedor cria uma nova instância dos `UIImpactFeedbackGenerator` classe, eles fornecem um `UIImpactFeedbackStyle` especificando a força dos comentários como:

- `Heavy`
- `Medium`
- `Light`

O `Prepare` método da `UIImpactFeedbackGenerator` é chamado para informar o sistema que comentários hápticos estão prestes a ocorrer de modo que ele pode minimizar a latência.

O `ImpactOccurred` método dispara comentários hápticos.

<a name="UINotificationFeedbackGenerator" />

### <a name="uinotificationfeedbackgenerator"></a>UINotificationFeedbackGenerator

Use este gerador de comentários para notificações, como um tipo de conclusão, falhando ou qualquer outra ação de aviso.

Use o seguinte código para comentários de notificação do gatilho:

```csharp
using UIKit;
...

// Initialize feedback
var notification = new UINotificationFeedbackGenerator ();
notification.Prepare ();

// Trigger feedback
notification.NotificationOccurred (UINotificationFeedbackType.Error);
```

Uma nova instância dos `UINotificationFeedbackGenerator` classe é criada e sua `Prepare` método é chamado para informar o sistema que comentários hápticos estão prestes a ocorrer de modo que ele pode minimizar a latência.

O `NotificationOccurred` é chamado para acionar comentários hápticos com um determinado `UINotificationFeedbackType` de:

- `Success`
- `Warning`
- `Error`

<a name="UISelectionFeedbackGenerator" />

### <a name="uiselectionfeedbackgenerator"></a>UISelectionFeedbackGenerator

Use este gerador de comentários de uma seleção ativamente a alteração, como um item de uma lista de separação.

Use o seguinte código para comentários da seleção de gatilho:

```csharp
using UIKit;
...

// Initialize feedback
var selection = new UISelectionFeedbackGenerator ();
selection.Prepare ();

// Trigger feedback
selection.SelectionChanged ();
```

Uma nova instância dos `UISelectionFeedbackGenerator` classe é criada e sua `Prepare` método é chamado para informar o sistema que comentários hápticos estão prestes a ocorrer de modo que ele pode minimizar a latência.

O `SelectionChanged` método dispara comentários hápticos.

## <a name="summary"></a>Resumo

Este artigo abordou os novos tipos de comentários hápticos disponível no iOS 10 e como implementá-los no xamarin. IOS.

## <a name="related-links"></a>Links relacionados

- [Amostras do iOS 10](https://developer.xamarin.com/samples/ios/iOS10/)
