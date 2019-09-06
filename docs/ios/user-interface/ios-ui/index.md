---
title: Interfaces do usuário no iOS
description: Este documento contém links para guias que descrevem como criar interfaces de usuário no aplicativo Xamarin. iOS. Os guias vinculados abordam a API de aparência, criando objetos de interface do usuário, opções de layout e muito mais.
ms.prod: xamarin
ms.assetid: 1BB46561-F503-491E-A27C-7878E7EBE00B
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 06/14/2017
ms.openlocfilehash: 106cadc6fef43c06597cf1e3b846bd48cecb6fa5
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70287049"
---
# <a name="user-interfaces-in-ios"></a>Interfaces do usuário no iOS

## <a name="appearance-apiintroduction-to-the-appearance-apimd"></a>[API de Aparência](introduction-to-the-appearance-api.md)

o iOS permite que muitos atributos visuais dos controles da interface do usuário sejam com o uso das APIs UIAppearance.

## <a name="creating-user-interface-objectsiosuser-interfaceios-uicreating-ui-objectsmd"></a>[Criar objetos de interface do usuário](~/ios/user-interface/ios-ui/creating-ui-objects.md)

A Apple agrupa partes de funcionalidade relacionadas a "estruturas" que são equivalentes aos namespaces do Xamarin. iOS. `UIKit`é o namespace que contém todos os controles de interface do usuário para iOS.

## <a name="layout-optionsiosuser-interfaceios-uilayout-optionsmd"></a>[Opções de layout](~/ios/user-interface/ios-ui/layout-options.md)

Há dois mecanismos diferentes para controlar o layout quando uma exibição é redimensionada ou girada: Dimensionamento automático e AutoLayout.

## <a name="providing-haptic-feedbackiosuser-interfaceios-uihaptic-feedbackmd"></a>[Fornecer comentários hápticos](~/ios/user-interface/ios-ui/haptic-feedback.md)

Este artigo aborda os novos tipos de comentários do Haptic disponíveis no iOS 10 e como implementá-los no Xamarin. iOS.

## <a name="working-with-the-ui-threadiosuser-interfaceios-uiui-threadmd"></a>[Trabalhar com o thread de interface do usuário](~/ios/user-interface/ios-ui/ui-thread.md)

Seu código só deve fazer alterações nos controles da interface do usuário do thread principal (ou da interface do usuário). Todas as atualizações de interface do usuário que ocorrem em um thread diferente (como um thread de retorno de chamada ou em segundo plano) podem não ser renderizadas para a tela ou podem até mesmo causar uma falha.




