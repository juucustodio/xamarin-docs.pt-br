---
title: Introdução ao iOS 12, tvOS 12 e watchOS 5
description: Este documento descreve como concluir a configuração até o  build de aplicativos para iOS 12, tvOS 12 e watchOS 5 com o Xamarin. Ele aborda como baixar o Xcode 10 e atualizar o Visual Studio para Mac e Visual Studio 2017.
ms.prod: xamarin
ms.assetid: 6C0F0133-1A5F-408B-8BCA-BDCA313A55C2
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 08/07/2018
ms.openlocfilehash: cb84ddc646933d253ca72fe8f9f581364aab698b
ms.sourcegitcommit: 12d48cdf99f0d916536d562e137d0e840d818fa1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/07/2018
ms.locfileid: "39615168"
---
# <a name="getting-started-with-ios-12-tvos-12-and-watchos-5"></a>Introdução ao iOS 12, tvOS 12 e watchOS 5

![Visualizar](~/media/shared/preview.png)

> [!WARNING]
> Suporte do Xamarin para iOS 12, tvOS 12 e SDKs do watchOS 5 distribuídos com o Xcode 10 está atualmente em versão prévia, o que significa que ele pode conter bugs, não está completo e pode ser alterado. Usá-lo apenas para experimentação.	

Este documento descreve como preparar-se para criar aplicativos Xamarin que chamam APIs lançadas com o Xcode 10. Ele aborda como baixar o Xcode 10 e atualizar o Visual Studio para Mac e Visual Studio 2017.

## <a name="download-and-install"></a>Baixe e instale

1. **Instalar o beta mais recente do Xcode 10** – os desenvolvedores da Apple registrado podem baixar e instalar a versão mais recente do Xcode 10 do [Portal do desenvolvedor Apple](https://developer.apple.com/download/).

2. **Executar o Xcode 10** – 10 de Xcode executar antes de atualizar e executando o Visual Studio para Mac ou Visual Studio 2017, conforme ele instala algumas ferramentas que Xamarin requer.

3. **Atualizar o Visual Studio para Mac e Visual Studio 2017** – siga as instruções do [blog de versão](https://releases.xamarin.com/preview-release-xcode-10-beta-5/) para instalar a versão prévia do Xamarin.

4. _(opcional)_  **Instalar o beta mais recente do iOS nos dispositivos iOS** – para teste de aplicativos que usam APIs introduzidas com o Xcode 10, desenvolvedores registrados na Apple podem  [baixar](https://developer.apple.com/download) e instalar a versão beta mais  recente para desenvolvedores em seus dispositivos.

   > [!TIP]
   > Mesmo se seu aplicativo não usa novas APIs, certifique-se de criá-lo com os SDKs mais recentes do Xcode 10 e testá-lo para certificar-se de que tudo está funcionando conforme o esperado. Se um aplicativo não chamar novas APIs, você pode recompilá-lo com esses novos SDKs e testá-lo em dispositivos que ainda não foram atualizados para o novo sistema operacional.
   >
   > Antes de atualizar seus dispositivos para o sistema operacional mais recente versões da Apple para testar seus aplicativos Xamarin, não se esqueça:
   >
   > - Leia [notas de versão da Apple](https://developer.apple.com/download/) para as atualizações do sistema operacional.
   > - Ler a versão prévia do Xamarin [postagem de blog de versão](https://releases.xamarin.com/preview-release-xcode-10-beta-5/).

## <a name="related-links"></a>Links relacionados

- [Baixar o Xcode 10](https://developer.apple.com/download/)
- Versão prévia do Xamarin [postagem no blog de versão](https://releases.xamarin.com/preview-release-xcode-10-beta-5/)
