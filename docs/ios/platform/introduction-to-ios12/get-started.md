---
title: Introdução ao iOS 12, 12 de tvOS e watchOS 5
description: Este documento descreve como concluir a configuração até a compilação 12 aplicativos iOS e tvOS 12 com Xamarin. Ele aborda como baixar o Xcode 10 e atualizar o Visual Studio para Mac e Visual Studio 2017.
ms.prod: xamarin
ms.assetid: 6C0F0133-1A5F-408B-8BCA-BDCA313A55C2
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 06/25/2018
ms.openlocfilehash: 70f67f934c2503e6f6fa0d3bae1f37bcc1f6f0a4
ms.sourcegitcommit: cfb72be633e335147d156af3ef9527151b9e31d9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/13/2018
ms.locfileid: "39030646"
---
# <a name="getting-started-with-ios-12-tvos-12-and-watchos-5"></a>Introdução ao iOS 12, 12 de tvOS e watchOS 5

![Visualizar](~/media/shared/preview.png)

> [!WARNING]
> TvOS do Xamarin iOS 12, 12, e o suporte do watchOS 5 está atualmente em visualização, o que significa que ele pode conter bugs, não é todos os recursos, e pode ser alterado. Usá-lo apenas para experimentação.

> [!NOTE]
> Para obter mais informações, leia a visualização do Xamarin [postagem de blog de versão](https://releases.xamarin.com/preview-release-xcode-10-beta-3/).

Este documento descreve como concluir a configuração até iOS build 12, 12 de tvOS e watchOS 5 aplicativos com o Xamarin. Ele aborda como baixar o Xcode 10 e atualizar o Visual Studio para Mac e Visual Studio 2017.

## <a name="download-and-install"></a>Baixe e instale

1. **Instalar o beta mais recente do Xcode 10** – os desenvolvedores da Apple registrado podem baixar e instalar a versão mais recente do Xcode 10 do [Portal do desenvolvedor Apple](https://developer.apple.com/download/).

   > [!NOTE]
   > O iOS 12, 12 do tvOS e watchOS 5 SDKs são distribuídas com o Xcode 10.

2. **Executar 10 Xcode** – executar o Xcode 10 antes de atualizar e executando o Visual Studio para Mac ou Visual Studio 2017; ela instala algumas ferramentas que requer o Xamarin.

3. **Atualizar o Visual Studio para Mac e Visual Studio 2017** – siga as instruções sobre a [blog de versão](https://releases.xamarin.com/preview-release-xcode-10-beta-3/) para instalar a versão prévia do Xamarin.

4. _(opcional)_  **Instalar o beta mais recente do iOS nos dispositivos iOS** – para teste de dispositivos de aplicativos que usar introduzido recentemente iOS 12, 12 de tvOS ou watchOS 5 APIs, os desenvolvedores de Apple registrados podem [baixar](https://developer.apple.com/download) e Instale o iOS mais recente 12, tvOS 12 ou versões beta de desenvolvedor do watchOS 5 em seus dispositivos.

   > [!TIP]
   > Mesmo se seu aplicativo não usa qualquer novo 12 do iOS, tvOS 12 ou watchOS 5 APIs, certifique-se de criá-lo com o iOS 12, 12 de tvOS ou watchOS SDK 5 (instalado com o Xcode 10) e teste para certificar-se de que tudo está funcionando como esperado. Se um aplicativo não chamar novas APIs, recompilá-lo com o iOS 12, 12 de tvOS ou watchOS 5 SDK e testá-lo em dispositivos que ainda não foram atualizados para novos sistemas operacionais.

   > [!IMPORTANT]
   > Antes de atualizar seus dispositivos para 12 do iOS, tvOS 12 ou watchOS 5 para testar aplicativos do Xamarin que chamam o novo 12 do iOS, tvOS 12 ou watchOS 5 APIs:
   > - Leia [notas de versão da Apple](https://developer.apple.com/download/) para a atualização do sistema operacional.
   > - Ler a versão prévia do Xamarin [postagem de blog de versão](https://releases.xamarin.com/preview-release-xcode-10-beta-3/).

## <a name="related-links"></a>Links relacionados

- [Baixar o Xcode 10](https://developer.apple.com/download/)
- Versão prévia do Xamarin [postagem no blog de versão](https://releases.xamarin.com/preview-release-xcode-10-beta-3/)
