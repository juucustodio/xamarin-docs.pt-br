---
title: Fornecendo comentários Haptic
description: Este artigo aborda os novos tipos de comentários haptic disponível no iOS 10 e como implementá-los no xamarin.
ms.prod: xamarin
ms.assetid: 888106D1-58F4-453F-BACC-91D51FA39C80
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/16/2017
ms.openlocfilehash: f2d1bd73ea764cd5bf56775abd7c7357b039bc79
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="providing-haptic-feedback"></a>Fornecendo comentários Haptic

_Este artigo aborda os novos tipos de comentários haptic disponível no iOS 10 e como implementá-los no xamarin._

<a name="Overview" />

## <a name="overview"></a>Visão geral

No iPhone e iPhone 7 7 +, Apple inclui novas respostas haptic que fornecem maneiras adicionais de fisicamente envolver o usuário. Comentários haptic (conhecido simplesmente como Haptics) usa o sentido de toque (por meio de força, vibrações ou movimento) no design de Interface do usuário. Use essas novas opções de comentários táteis para chamar a atenção do usuário e reforçar suas ações.

Os tópicos a seguir serão abordados em detalhes:

- [Sobre comentários Haptic](#About-Haptic-Feedback)
- [UIImpactFeedbackGenerator](#UIImpactFeedbackGenerator)
- [UINotificationFeedbackGenerator](#UINotificationFeedbackGenerator)
- [UISelectionFeedbackGenerator](#UISelectionFeedbackGenerator)

<a name="About-Haptic-Feedback" />

## <a name="about-haptic-feedback"></a>Sobre comentários Haptic

Vários elementos de interface do usuário internos já fornecem comentários haptic como selecionadores, comutadores e os controles deslizantes. iOS 10 agora inclui a capacidade de disparar programaticamente haptics usando uma subclasse concreta do `UIFeedbackGenerator` classe.

O desenvolvedor pode usar um dos seguintes `UIFeedbackGenerator` subclasses programaticamente gatilho haptic comentários:

- `UIImpactFeedbackGenerator` -Use esse gerador de comentários para complementar a uma ação ou uma tarefa como apresentar "thud" quando um modo de exibição slides no lugar ou se dois objetos na tela entrar em conflito.
- `UINotificationFeedbackGenerator` -Use esse gerador de comentários para notificações, como um tipo de conclusão, falhando ou qualquer outra ação de aviso.
- `UISelectionFeedbackGenerator` -Use esse gerador de comentários para uma seleção ativamente alteração como um item de uma lista de separação.

<a name="UIImpactFeedbackGenerator" />

### <a name="uiimpactfeedbackgenerator"></a>UIImpactFeedbackGenerator

Use este gerador de comentários para complementar a uma ação ou uma tarefa como apresentar "thud" quando um modo de exibição slides no lugar ou se dois objetos na tela entrar em conflito.

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

Quando o desenvolvedor cria uma nova instância do `UIImpactFeedbackGenerator` classe fornecem um `UIImpactFeedbackStyle` especificando a intensidade de comentários como:

- `Heavy`
- `Medium`
- `Light`

O `Prepare` método o `UIImpactFeedbackGenerator` é chamado para informar o sistema que comentários haptic estão prestes a ocorrer de forma que ele pode minimizar a latência.

O `ImpactOccurred` método dispara haptic comentários.

<a name="UINotificationFeedbackGenerator" />

### <a name="uinotificationfeedbackgenerator"></a>UINotificationFeedbackGenerator

Use este gerador de comentários para notificações, como um tipo de conclusão, falhando ou qualquer outra ação de aviso.

Use o seguinte código para comentários de notificação de gatilho:

```csharp
using UIKit;
...

// Initialize feedback
var notification = new UINotificationFeedbackGenerator ();
notification.Prepare ();

// Trigger feedback
notification.NotificationOccurred (UINotificationFeedbackType.Error);
```

Uma nova instância do `UINotificationFeedbackGenerator` classe é criada e sua `Prepare` método é chamado para informar o sistema que comentários haptic estão prestes a ocorrer de forma que ele pode minimizar a latência.

O `NotificationOccurred` é chamado para acionar haptic comentários com um determinado `UINotificationFeedbackType` de:

- `Success`
- `Warning`
- `Error`

<a name="UISelectionFeedbackGenerator" />

### <a name="uiselectionfeedbackgenerator"></a>UISelectionFeedbackGenerator

Use este gerador de comentários para uma seleção ativamente alteração como um item de uma lista de separação.

Use o seguinte código para comentários de seleção de gatilho:

```csharp
using UIKit;
...

// Initialize feedback
var selection = new UISelectionFeedbackGenerator ();
selection.Prepare ();

// Trigger feedback
selection.SelectionChanged ();
```

Uma nova instância do `UISelectionFeedbackGenerator` classe é criada e sua `Prepare` método é chamado para informar o sistema que comentários haptic estão prestes a ocorrer de forma que ele pode minimizar a latência.

O `SelectionChanged` método dispara haptic comentários.

## <a name="summary"></a>Resumo

Este artigo abordou os novos tipos de comentários haptic disponível no iOS 10 e como implementá-los no xamarin.

## <a name="related-links"></a>Links relacionados

- [Exemplos de iOS 10](https://developer.xamarin.com/samples/ios/iOS10/)
