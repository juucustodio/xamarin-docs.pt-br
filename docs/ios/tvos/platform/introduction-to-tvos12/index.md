---
title: Introdução às tvOS 12
description: Este documento fornece um alto nível de visão geral dos recursos novos e atualizados em tvOS 12 para a versão de visualização do qual Xamarin c# associações fornece no momento.
ms.prod: xamarin
ms.assetid: 037F7FFF-2155-4017-B99A-839CE7EC5C9C
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 06/25/2018
ms.openlocfilehash: 03841306ba54e511dbf2f2b86a7c17e9f4669bcd
ms.sourcegitcommit: 3f2737f8abf9b855edf060474aa222e973abda3f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2018
ms.locfileid: "37067254"
---
# <a name="introduction-to-tvos-12"></a>Introdução às tvOS 12

![Visualizar](~/media/shared/preview.png)

> [!WARNING]
> Suporte a 12 tvOS do Xamarin está atualmente em visualização, o que significa que ele pode conter bugs, não é concluída de recurso, e pode ser alterado. Usá-lo para experimentação somente.

> [!NOTE]
> - Examine o [Introdução](~/ios/platform/introduction-to-ios12/get-started.md) guia para obter instruções sobre como começar a criar aplicativos tvOS 12 com o Xamarin e iOS 12.
> - Para obter mais informações, leia o [notas de versão](https://releases.xamarin.com/preview-release-xcode-10-beta/) para o Xamarin versão de visualização.

Este documento fornece uma visão geral dos novos e atualizados tvOS 12 recursos de visualização do qual Xamarin versão atualmente fornece c# associações.

## <a name="password-autofill"></a>Preenchimento automático de senha

Com tvOS 12, os usuários podem usar seus dispositivos iOS para entrar um aplicativo tvOS com um único toque. Isso é habilitado por meio de uma combinação de `UITextContentType` campos de uso para especificar o nome de usuário e senha, os domínios associados para estabelecer uma relação entre um aplicativo iOS e um aplicativo tvOS e ambientes de foco preferencial para selecionar um item para receber o foco depois que um usuário Fornece um nome de usuário e senha.

## <a name="focus-engine-enhancements"></a>Aprimoramentos do mecanismo de foco

tvOS 12 permite que todos os aplicativos, independentemente de como eles são processados, para interagir com o mecanismo de foco. Por meio de interações do usuário com o Siri remoto, o mecanismo de foco pode ser usado com qualquer aplicativo para selecionar um item, dica em alterações de foco possíveis e atualizar naturalmente foco. Isso é habilitado em aplicativos personalizados por meio do UIKit `IUIFocusItemContainer` interface, o `UIFocusMovementHint` classe, o `IUIFocusItemScrollableContainer` interface e outras classes relacionadas e métodos.

## <a name="related-links"></a>Links relacionados

- [Amostras do tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS – desenvolvedor da Apple (Apple)](https://developer.apple.com/tvos/)
- [O que há de novo no tvOS 12 (Apple) (vídeo)](https://developer.apple.com/videos/play/wwdc2018/208/)
- [TV (Apple)](https://www.apple.com/tv/)
- Visualização de Xamarin [notas de versão](https://releases.xamarin.com/preview-release-xcode-10-beta/)
