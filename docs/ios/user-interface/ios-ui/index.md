---
title: "Interface do usuário do iOS"
description: "Abrange a trabalhar com a Interface do usuário do iOS em um aplicativo xamarin."
ms.topic: article
ms.prod: xamarin
ms.assetid: 1BB46561-F503-491E-A27C-7878E7EBE00B
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 06/14/2017
ms.openlocfilehash: f456b54180d50cfc4b6b98ed8f3d4118c8397b37
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/12/2018
---
# <a name="user-interface-in-ios"></a>Interface do usuário do iOS

## <a name="appearance-apiintroduction-to-the-appearance-apimd"></a>[API de Aparência](introduction-to-the-appearance-api.md)

iOS permite que muitos atributos visual dos controles de interface de usuário para ser com tema usando as APIs UIAppearance.

## <a name="creating-user-interface-objectsiosuser-interfaceios-uicreating-ui-objectsmd"></a>[Criar objetos de interface do usuário](~/ios/user-interface/ios-ui/creating-ui-objects.md)

Grupos de Apple relacionadas das funções "estruturas", que correspondem aos namespaces xamarin. `UIKit` é o namespace que contém todos os controles de interface de usuário para iOS.

## <a name="layout-optionsiosuser-interfaceios-uilayout-optionsmd"></a>[Opções de layout](~/ios/user-interface/ios-ui/layout-options.md)

Existem dois mecanismos diferentes para controlar o layout quando uma exibição é redimensionada ou girada: dimensionando e Autolayout.

## <a name="providing-haptic-feedbackiosuser-interfaceios-uihaptic-feedbackmd"></a>[Fornecer comentários hápticos](~/ios/user-interface/ios-ui/haptic-feedback.md)

Este artigo aborda os novos tipos de comentários haptic disponível no iOS 10 e como implementá-los no xamarin.

## <a name="working-with-the-ui-threadiosuser-interfaceios-uiui-threadmd"></a>[Trabalhar com o thread de interface do usuário](~/ios/user-interface/ios-ui/ui-thread.md)

Seu código deve fazer apenas o thread de alterações para controles de interface na página principal do usuário (ou da interface do usuário). Todas as atualizações da interface do usuário que ocorrem em um thread diferente (por exemplo, um thread de retorno de chamada ou plano de fundo) não podem obter renderizadas na tela ou mesmo podem causar uma falha.




