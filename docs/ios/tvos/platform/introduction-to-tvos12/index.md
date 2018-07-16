---
title: Introdução ao tvOS 12
description: Este documento fornece um alto nível de visão geral dos recursos novos e atualizados do tvOS 12 na versão prévia do qual Xamarin fornece associações c# no momento.
ms.prod: xamarin
ms.assetid: 037F7FFF-2155-4017-B99A-839CE7EC5C9C
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 06/25/2018
ms.openlocfilehash: e45d9944a2f4fc392b5a78efb4a7751d19641c73
ms.sourcegitcommit: cfb72be633e335147d156af3ef9527151b9e31d9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/13/2018
ms.locfileid: "39030659"
---
# <a name="introduction-to-tvos-12"></a>Introdução ao tvOS 12

![Visualizar](~/media/shared/preview.png)

> [!WARNING]
> Suporte de 12 de tvOS do Xamarin está atualmente em visualização, o que significa que ele pode conter bugs, não é todos os recursos, e pode ser alterado. Usá-lo apenas para experimentação.

> [!NOTE]
> - Examine os [guia de Introdução](~/ios/platform/introduction-to-ios12/get-started.md) guia para obter instruções sobre como começar a compilar 12 aplicativos iOS e tvOS 12 com Xamarin.
> - Para obter mais informações, leia a visualização do Xamarin [postagem de blog de versão](https://releases.xamarin.com/preview-release-xcode-10-beta-3/).

Este documento fornece uma visão geral dos novo e atualizado do tvOS 12 recursos para visualização do Xamarin que a versão atualmente fornece associações c#.

## <a name="password-autofill"></a>Preenchimento automático de senha

Com 12 do tvOS, os usuários podem usar seus dispositivos iOS para entrar um aplicativo tvOS com um único toque. Isso é habilitado por meio de uma combinação de `UITextContentType` campos de uso para especificar o nome de usuário e senha, domínios associados para estabelecer uma relação entre um aplicativo iOS e um aplicativo tvOS e ambientes de foco preferencial para selecionar um item para receber o foco depois que um usuário Fornece um nome de usuário e senha.

## <a name="focus-engine-enhancements"></a>Aprimoramentos do mecanismo de foco

tvOS 12 permite que todos os aplicativos, independentemente de como elas são renderizadas, para interagir com o mecanismo de foco. Por meio de interações do usuário com o Siri remoto, o mecanismo de foco pode ser usado com qualquer aplicativo para selecionar um item, Dica sobre alterações de foco possíveis e atualizar naturalmente o foco. Isso é habilitado em aplicativos personalizados por meio do UIKit `IUIFocusItemContainer` interface, o `UIFocusMovementHint` classe, o `IUIFocusItemScrollableContainer` interface e outras classes relacionadas e métodos.

## <a name="related-links"></a>Links relacionados

- [Amostras do tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS – o desenvolvedor da Apple (Apple)](https://developer.apple.com/tvos/)
- [O que há de novo no tvOS 12 (Apple) (vídeo)](https://developer.apple.com/videos/play/wwdc2018/208/)
- [TV (Apple)](https://www.apple.com/tv/)
- Versão prévia do Xamarin [postagem no blog de versão](https://releases.xamarin.com/preview-release-xcode-10-beta-3/)
