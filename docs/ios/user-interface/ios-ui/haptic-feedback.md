---
title: Fornecendo comentários de Haptic no Xamarin. iOS
description: Este documento descreve como fornecer comentários de Haptic em um aplicativo Xamarin. iOS. Ele discute UIImpactFeedbackGenerator, UINotificationFeedbackGenerator e UISelectionFeedbackGenerator.
ms.prod: xamarin
ms.assetid: 888106D1-58F4-453F-BACC-91D51FA39C80
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/16/2017
ms.openlocfilehash: 156af7a5336ac091c0202e38a3a59a32846e281a
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73003353"
---
# <a name="providing-haptic-feedback-in-xamarinios"></a>Fornecendo comentários de Haptic no Xamarin. iOS

<a name="Overview" />

## <a name="overview"></a>Visão Geral

No iPhone 7 e no iPhone 7 Plus, a Apple incluiu novas respostas Haptic que fornecem maneiras adicionais de envolver fisicamente o usuário. Os comentários de Haptic (muitas vezes conhecidos como haptics) usam a sensação de toque (por meio de força, vibraçãos ou movimento) no design da interface do usuário. Use essas novas opções de comentários do tactile para obter a atenção do usuário e reforçar suas ações.

Os tópicos a seguir serão abordados em detalhes:

- [Sobre comentários do Haptic](#About-Haptic-Feedback)
- [UIImpactFeedbackGenerator](#UIImpactFeedbackGenerator)
- [UINotificationFeedbackGenerator](#UINotificationFeedbackGenerator)
- [UISelectionFeedbackGenerator](#UISelectionFeedbackGenerator)

<a name="About-Haptic-Feedback" />

## <a name="about-haptic-feedback"></a>Sobre comentários do Haptic

Vários elementos internos da interface do usuário já fornecem comentários haptics, como seletores, interruptores e controles deslizantes. Agora, o iOS 10 adiciona a capacidade de programaticamente disparar haptics usando uma subclasse concreta da classe `UIFeedbackGenerator`.

O desenvolvedor pode usar uma das seguintes subclasses `UIFeedbackGenerator` para disparar programaticamente comentários de Haptic:

- `UIImpactFeedbackGenerator`-Use este gerador de comentários para complementar uma ação ou tarefa, como apresentar um "Thud" quando um modo de exibição deslizar para o local ou se dois objetos na tela colidirem.
- `UINotificationFeedbackGenerator`-Use este gerador de comentários para notificações como uma ação concluída, falha ou qualquer outro tipo de aviso.
- `UISelectionFeedbackGenerator`-Use este gerador de comentários para uma seleção que é alterada ativamente, como escolher um item de uma lista.

<a name="UIImpactFeedbackGenerator" />

### <a name="uiimpactfeedbackgenerator"></a>UIImpactFeedbackGenerator

Use este gerador de comentários para complementar uma ação ou tarefa, como apresentar um "Thud" quando um modo de exibição estiver em vigor ou se dois objetos na tela colidirem.

Use o código a seguir para disparar os comentários de impacto:

```csharp
using UIKit;
...

// Initialize feedback
var impact = new UIImpactFeedbackGenerator (UIImpactFeedbackStyle.Heavy);
impact.Prepare ();

// Trigger feedback
impact.ImpactOccurred ();
```

Quando o desenvolvedor cria uma nova instância da classe `UIImpactFeedbackGenerator` ela fornece um `UIImpactFeedbackStyle` especificando a força dos comentários como:

- `Heavy`
- `Medium`
- `Light`

O método `Prepare` da `UIImpactFeedbackGenerator` é chamado para informar ao sistema que os comentários de Haptic estão prestes a ocorrer para que ele possa minimizar a latência.

O método `ImpactOccurred`, em seguida, dispara comentários Haptic.

<a name="UINotificationFeedbackGenerator" />

### <a name="uinotificationfeedbackgenerator"></a>UINotificationFeedbackGenerator

Use este gerador de comentários para notificações como uma ação concluída, falha ou qualquer outro tipo de aviso.

Use o código a seguir para disparar comentários de notificação:

```csharp
using UIKit;
...

// Initialize feedback
var notification = new UINotificationFeedbackGenerator ();
notification.Prepare ();

// Trigger feedback
notification.NotificationOccurred (UINotificationFeedbackType.Error);
```

Uma nova instância da classe `UINotificationFeedbackGenerator` é criada e seu método `Prepare` é chamado para informar ao sistema que os comentários de Haptic estão prestes a ocorrer para que ele possa minimizar a latência.

O `NotificationOccurred` é chamado para disparar comentários de Haptic com um determinado `UINotificationFeedbackType` de:

- `Success`
- `Warning`
- `Error`

<a name="UISelectionFeedbackGenerator" />

### <a name="uiselectionfeedbackgenerator"></a>UISelectionFeedbackGenerator

Use este gerador de comentários para uma seleção que está sendo alterada ativamente, como escolher um item de uma lista.

Use o código a seguir para disparar comentários de seleção:

```csharp
using UIKit;
...

// Initialize feedback
var selection = new UISelectionFeedbackGenerator ();
selection.Prepare ();

// Trigger feedback
selection.SelectionChanged ();
```

Uma nova instância da classe `UISelectionFeedbackGenerator` é criada e seu método `Prepare` é chamado para informar ao sistema que os comentários de Haptic estão prestes a ocorrer para que ele possa minimizar a latência.

O método `SelectionChanged`, em seguida, dispara comentários Haptic.

## <a name="summary"></a>Resumo

Este artigo abordou os novos tipos de comentários de Haptic disponíveis no iOS 10 e como implementá-los no Xamarin. iOS.

## <a name="related-links"></a>Links relacionados

- [Amostras do iOS 10](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+iOS10)
