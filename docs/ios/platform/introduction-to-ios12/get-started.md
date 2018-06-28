---
title: Guia de Introdução com iOS 12 e tvOS 12
description: Este documento descreve como obter configurou a compilação iOS 12 e tvOS 12 aplicativos com o Xamarin. Ele aborda como baixar o Xcode 10 e atualizar o Visual Studio para Mac e 2017 do Visual Studio.
ms.prod: xamarin
ms.assetid: 6C0F0133-1A5F-408B-8BCA-BDCA313A55C2
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 06/25/2018
ms.openlocfilehash: c2ce57934d39b82725722d4e49051cbf3ded5df1
ms.sourcegitcommit: 3f2737f8abf9b855edf060474aa222e973abda3f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2018
ms.locfileid: "37067259"
---
# <a name="getting-started-with-ios-12-and-tvos-12"></a>Guia de Introdução com iOS 12 e tvOS 12

![Visualizar](~/media/shared/preview.png)

> [!WARNING]
> IOS 12 tvOS 12 suporte e do Xamarin está atualmente em visualização, o que significa que ele pode conter bugs, não é concluída de recurso, e pode ser alterado. Usá-lo para experimentação somente.

> [!NOTE]
> Para obter mais informações, leia o [notas de versão](https://releases.xamarin.com/preview-release-xcode-10-beta/) para o Xamarin versão de visualização.

Este documento descreve como obter configurou a compilação iOS 12 e tvOS 12 aplicativos com o Xamarin. Ele aborda como baixar o Xcode 10 e atualizar o Visual Studio para Mac e 2017 do Visual Studio.

## <a name="download-and-install"></a>Baixar e instalar

1. **Instalar o versão beta mais recente Xcode 10** – os desenvolvedores da Apple registrado podem baixar e instalar a versão mais recente do Xcode 10 do [Portal do desenvolvedor Apple](https://developer.apple.com/download/).

   > [!NOTE]
   > O iOS 12 e tvOS 12 SDKs são distribuídas com o Xcode 10.

2. **Executar o Xcode 10** – execute o Xcode 10 antes de atualizar e executando o Visual Studio para Mac ou o Visual Studio de 2017; ele instala algumas ferramentas que requer o Xamarin.

3. **Atualizar o Visual Studio para Mac e o Visual Studio de 2017** – siga as instruções a [notas de versão](https://releases.xamarin.com/preview-release-xcode-10-beta/) para instalar a visualização do Xamarin.

4. _(opcional)_  **Instalar o beta mais recente do iOS em dispositivos iOS** – para testes de dispositivos de aplicativos que use introduzido recentemente iOS 12 ou tvOS 12 APIs, os desenvolvedores de Apple registrados podem [baixar](https://developer.apple.com/download) e instalar o iOS mais recente 12 ou tvOS 12 betas de desenvolvedor em seus dispositivos iOS e tvOS.

   > [!TIP]
   > Mesmo se seu aplicativo não usa qualquer nova iOS 12 ou tvOS 12 APIs, certifique-se para compilá-lo com o iOS 12 ou tvOS 12 SDK (instalado com o Xcode 10) e teste para certificar-se de que tudo está funcionando como esperado. Se um aplicativo não chamar quaisquer novas APIs, recompilá-lo com o iOS 12 ou tvOS 12 SDK e testá-lo em dispositivos que ainda não foram atualizados para os novos sistemas operacionais.

   > [!IMPORTANT]
   > Antes de atualizar seus dispositivos iOS 12 ou tvOS 12 para testar aplicativos Xamarin que chamam iOS novo 12 ou tvOS 12 APIs:
   > - Leitura [notas de versão da Apple](https://developer.apple.com/download/) para a atualização do sistema operacional.
   > - Leitura de [notas de versão](https://releases.xamarin.com/preview-release-xcode-10-beta/) para o Xamarin versão de visualização.

## <a name="related-links"></a>Links relacionados

- [Baixe o Xcode 10](https://developer.apple.com/download/)
- Visualização de Xamarin [notas de versão](https://releases.xamarin.com/preview-release-xcode-10-beta/)
