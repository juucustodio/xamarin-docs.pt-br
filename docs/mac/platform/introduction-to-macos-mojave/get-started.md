---
title: Guia de Introdução ao macOS Mojave
description: Este documento descreve como obter configurou a compilação macOS aplicativos Mojave com Xamarin.Mac. Ele aborda como baixar o Xcode 10 e atualizar o Visual Studio para Mac.
ms.prod: xamarin
ms.assetid: E9A7B68A-E164-4C5C-86AC-B2A3E7A30DA1
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 06/25/2018
ms.openlocfilehash: ee5269bf314401328d0184631e817b37ce091479
ms.sourcegitcommit: 3f2737f8abf9b855edf060474aa222e973abda3f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2018
ms.locfileid: "37067261"
---
# <a name="getting-started-with-macos-mojave"></a>Guia de Introdução ao macOS Mojave

![Visualizar](~/media/shared/preview.png)

> [!WARNING]
> MacOS do Xamarin suporte Mojave está atualmente em visualização, o que significa que pode conter bugs, não é concluída de recurso e pode ser alterado.
> Usá-lo para experimentação somente.

> [!NOTE]
> Para obter mais informações, leia o [notas de versão](https://releases.xamarin.com/preview-release-xcode-10-beta/) para o Xamarin versão de visualização.

Este documento descreve como obter configurou a compilação macOS aplicativos Mojave com Xamarin.Mac. Ele aborda como baixar o Xcode 10 e atualizar o Visual Studio para Mac.

## <a name="download-and-install"></a>Baixar e instalar

1. **Instalar o versão beta mais recente Xcode 10** – os desenvolvedores da Apple registrado podem baixar e instalar a versão mais recente do Xcode 10 do [Portal do desenvolvedor Apple](https://developer.apple.com/download/).

   > [!NOTE]
   > O macOS Mojave SDK é distribuído com o Xcode 10.

2. **Executar o Xcode 10** – execute o Xcode 10 antes de atualizar e executando o Visual Studio para Mac; ele instala algumas ferramentas que requer o Xamarin.

3. **Atualizar o Visual Studio para Mac** – siga as instruções de [notas de versão](https://releases.xamarin.com/preview-release-xcode-10-beta/) para instalar a visualização do Xamarin.

4. _(opcional)_  **Instalar a versão beta Mojave de macOS mais recente no seu Mac** – para testar aplicativos Xamarin.Mac que usam macOS introduzido recentemente Mojave APIs, pode de desenvolvedores registrado Apple [baixar](https://developer.apple.com/download/) e instalar o beta de desenvolvedor mais recente do Mojave macOS.

   > [!TIP]
   > Mesmo que seu aplicativo não usa qualquer macOS novas APIs Mojave, certifique-se de compilá-lo com o macOS Mojave SDK (instalado com o Xcode 10) e testá-lo para certificar-se de que tudo está funcionando conforme o esperado. Se um aplicativo não chamar quaisquer APIs novo, você pode recompilá-lo com o SDK Mojave macOS e testá-lo sem atualizar o sistema operacional do Mac.

   > [!IMPORTANT]
   > Antes de atualizar seu Mac para macOS Mojave para compilar e testar aplicativos Xamarin.Mac que chamam macOS novas APIs de Mojave:
   > - Leitura [notas de versão da Apple](https://developer.apple.com/download/) para a atualização do sistema operacional.
   > - Leitura de [notas de versão](https://releases.xamarin.com/preview-release-xcode-10-beta/) para o Xamarin versão de visualização. Observe que essa primeira preview não tem associações para o novo macOS Mojave AppKit APIs (como modo escuro).

## <a name="related-links"></a>Links relacionados

- [Baixe o Xcode 10](https://developer.apple.com/download/)
- Visualização de Xamarin [notas de versão](https://releases.xamarin.com/preview-release-xcode-10-beta/)
