---
title: Interfaces do usuário no iOS
description: Este documento leva a guias que descrevem como criar interfaces do usuário no aplicativo xamarin. IOS. Os guias vinculados abrangem a API de aparência, criando objetos de interface do usuário, opções de layout e muito mais.
ms.prod: xamarin
ms.assetid: 1BB46561-F503-491E-A27C-7878E7EBE00B
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 06/14/2017
ms.openlocfilehash: efb88ada8a4b4c36dd49de137eb64acd63552968
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50115665"
---
# <a name="user-interfaces-in-ios"></a>Interfaces do usuário no iOS

## <a name="appearance-apiintroduction-to-the-appearance-apimd"></a>[API de Aparência](introduction-to-the-appearance-api.md)

iOS permite que muitos atributos visuais de controles da interface do usuário para ser o tema usando as APIs de UIAppearance.

## <a name="creating-user-interface-objectsiosuser-interfaceios-uicreating-ui-objectsmd"></a>[Criar objetos de interface do usuário](~/ios/user-interface/ios-ui/creating-ui-objects.md)

Partes relacionadas de grupos de Apple de funcionalidade em "estruturas", que correspondem aos namespaces do xamarin. IOS. `UIKit` é o namespace que contém todos os controles de interface do usuário para iOS.

## <a name="layout-optionsiosuser-interfaceios-uilayout-optionsmd"></a>[Opções de layout](~/ios/user-interface/ios-ui/layout-options.md)

Há dois mecanismos diferentes para controlar o layout quando um modo de exibição é redimensionado ou girado: dimensionamento automático e o layout automático.

## <a name="providing-haptic-feedbackiosuser-interfaceios-uihaptic-feedbackmd"></a>[Fornecer comentários hápticos](~/ios/user-interface/ios-ui/haptic-feedback.md)

Este artigo aborda os novos tipos de comentários hápticos disponível no iOS 10 e como implementá-los no xamarin. IOS.

## <a name="working-with-the-ui-threadiosuser-interfaceios-uiui-threadmd"></a>[Trabalhar com o thread de interface do usuário](~/ios/user-interface/ios-ui/ui-thread.md)

Seu código deve fazer apenas o thread de alterações em controles de interface na página principal do usuário (ou da interface do usuário). Todas as atualizações da interface do usuário que ocorrem em um thread diferente (por exemplo, um thread de retorno de chamada ou em segundo plano) não podem obter renderizadas na tela ou até mesmo poderia causar uma falha.




