---
title: Interfaces do usuário no iOS
description: Este documento contém links para guias que descrevem como criar interfaces de usuário no aplicativo Xamarin. iOS. Os guias vinculados abordam a API de aparência, criando objetos de interface do usuário, opções de layout e muito mais.
ms.prod: xamarin
ms.assetid: 1BB46561-F503-491E-A27C-7878E7EBE00B
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 06/14/2017
ms.openlocfilehash: 325898b3c934e25ae1610a3437f787476dcd22cb
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73003334"
---
# <a name="user-interfaces-in-ios"></a>Interfaces do usuário no iOS

## <a name="appearance-apiintroduction-to-the-appearance-apimd"></a>[API de Aparência](introduction-to-the-appearance-api.md)

o iOS permite que muitos atributos visuais dos controles da interface do usuário sejam com o uso das APIs UIAppearance.

## <a name="creating-user-interface-objectsiosuser-interfaceios-uicreating-ui-objectsmd"></a>[Criar objetos de interface do usuário](~/ios/user-interface/ios-ui/creating-ui-objects.md)

A Apple agrupa partes de funcionalidade relacionadas a "estruturas" que são equivalentes aos namespaces do Xamarin. iOS. `UIKit` é o namespace que contém todos os controles de interface do usuário para iOS.

## <a name="layout-optionsiosuser-interfaceios-uilayout-optionsmd"></a>[Opções de layout](~/ios/user-interface/ios-ui/layout-options.md)

Há dois mecanismos diferentes para controlar o layout quando uma exibição é redimensionada ou girada: dimensionamento automático e AutoLayout.

## <a name="providing-haptic-feedbackiosuser-interfaceios-uihaptic-feedbackmd"></a>[Fornecer comentários hápticos](~/ios/user-interface/ios-ui/haptic-feedback.md)

Este artigo aborda os novos tipos de comentários do Haptic disponíveis no iOS 10 e como implementá-los no Xamarin. iOS.

## <a name="working-with-the-ui-threadiosuser-interfaceios-uiui-threadmd"></a>[Trabalhar com o thread de interface do usuário](~/ios/user-interface/ios-ui/ui-thread.md)

Seu código só deve fazer alterações nos controles da interface do usuário do thread principal (ou da interface do usuário). Todas as atualizações de interface do usuário que ocorrem em um thread diferente (como um thread de retorno de chamada ou em segundo plano) podem não ser renderizadas para a tela ou podem até mesmo causar uma falha.
