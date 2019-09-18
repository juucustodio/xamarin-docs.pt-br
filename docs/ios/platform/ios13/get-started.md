---
title: Introdução ao iOS 13, iPadOS 13, tvOS 13 e watchOS 6
description: Este documento descreve como ser configurado para compilar aplicativos iOS 13, iPadOS 13, tvOS 13 e watchOS 6 com o Xamarin. Ele discute como baixar o Xcode 11 e atualizar Visual Studio para Mac.
ms.prod: xamarin
ms.assetid: 97414545-85D2-433C-9246-63B6763F488A
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 07/01/2019
ms.openlocfilehash: 9dc6f234c4bc14c3644d953eef0d2e0f397436e5
ms.sourcegitcommit: 61a35d0643eb3bf5adb8f8831da54771d8dde626
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/17/2019
ms.locfileid: "71033070"
---
# <a name="get-started-with-ios-13"></a>Introdução ao iOS 13

![Versão prévia do recurso](~/media/shared/preview.png)

Este documento descreve como começar a criar aplicativos Xamarin que chamam APIs lançadas com o Xcode 11, para iOS 13. O uso da visualização requer o macOS 10.14.4 (Mojave) ou mais recente.

## <a name="download-and-install"></a>Baixar e instalar

1. **Instalar o Xcode 11 Beta** – os desenvolvedores da Apple registrados podem baixar e instalar a versão mais recente do Xcode 11 Beta no [portal do desenvolvedor da Apple](https://developer.apple.com/download/) ou na loja de **aplicativos**.

2. **Executar o Xcode 11 Beta** – execute o Xcode 11 antes de atualizar e executar o Visual Studio para Mac, pois ele instala algumas ferramentas exigidas pelo Xamarin.

3. Em Visual Studio para Mac, selecione **Visual Studio > verificar se há atualizações...** , selecione o canal **Xcode 11 visualizações** e instale as atualizações disponíveis. Se você não vir esse canal, verifique se você está conectado à sua conta do **Visual Studio > conta...** .

4. Em Visual Studio para Mac, selecione **Visual Studio > preferências > projetos > locais do SDK > Apple** e selecione **Xcode-beta. app**.

5. Adicional Se estiver avaliando essa visualização usando o _Xcode 11 Beta 3_, você deve habilitar a vinculação. Clique com o botão direito do mouse em seu projeto, navegue até **opções > o comportamento do vinculador do Build >** e defina o valor de comportamento do vinculador para **vincular somente os SDKs do Framework**. Essa solução alternativa não será necessária em uma visualização futura.

6. Adicional **Instale o Ios 13 em seus dispositivos IOS** – para testes de dispositivo de aplicativos que usam APIs introduzidas com o Xcode 11, os desenvolvedores da Apple registrados podem [baixar](https://developer.apple.com/download) e instalar o sistema operacional em seus dispositivos. Como o iOS está em beta, tenha cuidado ao instalá-lo em seu dispositivo primário.

   > [!TIP]
   > Mesmo que seu aplicativo não use nenhuma nova API, certifique-se de compilá-lo com os mais recentes SDKs do Xcode 11 e testá-lo para garantir que tudo funcione conforme o esperado. Se um aplicativo não chamar novas APIs, você poderá recompilá-lo com esses novos SDKs e testá-lo em dispositivos que ainda não foram atualizados para o novo sistema operacional.
   >
   > Antes de atualizar seus dispositivos para as versões mais recentes do sistema operacional da Apple para testar seus aplicativos Xamarin, certifique-se de:
   >
   > - Leia as [notas de versão da Apple](https://developer.apple.com/download/) para as atualizações do sistema operacional.

## <a name="related-links"></a>Links relacionados

- [Baixar o Xcode](https://developer.apple.com/download/)
- [Notas de versão de visualização do Xamarin. iOS](/xamarin/ios/release-notes/12/12.99)
