---
title: Introdução ao macOS Mojave
description: Este documento descreve como concluir a configuração até macOS build Mojave aplicativos com xamarin. Mac. Ele aborda como baixar o Xcode 10 e atualizar o Visual Studio para Mac.
ms.prod: xamarin
ms.assetid: E9A7B68A-E164-4C5C-86AC-B2A3E7A30DA1
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 07/08/2018
ms.openlocfilehash: cf2725eafa18330a07a08db4235bad1a1ecd47b6
ms.sourcegitcommit: cfb72be633e335147d156af3ef9527151b9e31d9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/13/2018
ms.locfileid: "39030608"
---
# <a name="getting-started-with-macos-mojave"></a>Introdução ao macOS Mojave

![Visualizar](~/media/shared/preview.png)

> [!WARNING]
> MacOS do Xamarin suporte Mojave está atualmente em visualização, o que significa que ele pode conter bugs, não tem os recursos completos e pode ser alterado.
> Usá-lo apenas para experimentação.

> [!NOTE]
> Para obter mais informações, leia a visualização do Xamarin [postagem de blog de versão](https://releases.xamarin.com/preview-release-xcode-10-beta-3/).

Este documento descreve como concluir a configuração até macOS build Mojave aplicativos com xamarin. Mac. Ele aborda como baixar o Xcode 10 e atualizar o Visual Studio para Mac.

## <a name="download-and-install"></a>Baixe e instale

1. **Instalar o beta mais recente do Xcode 10** – os desenvolvedores da Apple registrado podem baixar e instalar a versão mais recente do Xcode 10 do [Portal do desenvolvedor Apple](https://developer.apple.com/download/).

   > [!NOTE]
   > O macOS Mojave SDK é distribuído com o Xcode 10.

2. **Executar o Xcode 10** – executar o Xcode 10 antes de atualizar e executando o Visual Studio para Mac; ela instala algumas ferramentas que requer o Xamarin.

3. **Atualizar o Visual Studio para Mac** – siga as instruções sobre a [blog de versão](https://releases.xamarin.com/preview-release-xcode-10-beta-3/) para instalar a versão prévia do Xamarin.

4. _(opcional)_  **Instalar o beta mais recente do Mojave macOS em seu Mac** – para testar aplicativos xamarin. MAC que usam o macOS introduzido recentemente Mojave APIs, can de desenvolvedores da Apple registrado [baixar](https://developer.apple.com/download/) e instalar o versão beta mais recente desenvolvedor de Mojave macOS.

   > [!TIP]
   > Mesmo se seu aplicativo não usa qualquer macOS novas APIs Mojave, certifique-se de criá-lo com o SDK Mojave (instalado com o Xcode 10) do macOS e testá-lo para certificar-se de que tudo está funcionando conforme o esperado. Se um aplicativo não chamar novas APIs, você pode recompilá-lo com o macOS Mojave SDK e testá-lo sem atualizar o sistema operacional do seu Mac.

   > [!IMPORTANT]
   > Antes de atualizar seu Mac para macOS Mojave para compilar e testar aplicativos xamarin. MAC que chamam o macOS novas APIs de Mojave:
   > - Leia [notas de versão da Apple](https://developer.apple.com/download/) para a atualização do sistema operacional.
   > - Ler a versão prévia do Xamarin [postagem de blog de versão](https://releases.xamarin.com/preview-release-xcode-10-beta-3/).

## <a name="related-links"></a>Links relacionados

- [Baixar o Xcode 10](https://developer.apple.com/download/)
- Versão prévia do Xamarin [postagem no blog de versão](https://releases.xamarin.com/preview-release-xcode-10-beta-3/)
