---
title: Introdução ao iOS 13, iPadOS 13, 13 de tvOS e watchOS 6
description: Este documento descreve como concluir a configuração até iOS build 13, iPadOS 13, 13 de tvOS e watchOS 6 aplicativos com o Xamarin. Ele aborda como baixar o Xcode 11 e atualizar o Visual Studio para Mac.
ms.prod: xamarin
ms.assetid: 97414545-85D2-433C-9246-63B6763F488A
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 07/01/2019
ms.openlocfilehash: 554cddf5717401f912cab38c78a6af17659a0cf7
ms.sourcegitcommit: 8ecfa339d0f3e7687977bfe4fc96448942690183
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/03/2019
ms.locfileid: "67558687"
---
# <a name="get-started-with-ios-13"></a>Introdução ao iOS 13

![Versão prévia do recurso](~/media/shared/preview.png)

Este documento descreve como começar a criar aplicativos Xamarin que chamam APIs lançadas com o Xcode 11, para iOS 13. Usando a visualização requer o macOS 10.14.4 (Mojave) ou mais recente.

## <a name="download-and-install"></a>Baixe e instale

1. **Instalar o Xcode 11 beta** – os desenvolvedores da Apple registrado podem baixar e instalar a versão mais recente do Xcode 11 beta do [Portal do desenvolvedor Apple](https://developer.apple.com/download/) ou o **App Store**.

2. **Executar Xcode 11 beta** – requer executar antes de atualizar e executando o Visual Studio para Mac, conforme ele instala algumas ferramentas que Xamarin 11 do Xcode.

3. No Visual Studio para Mac, selecione **Visual Studio > Verificar se há atualizações...** , selecione o **Xcode 11 visualizações** de canal e instalar as atualizações disponíveis.

4. No Visual Studio para Mac, selecione **Visual Studio > Preferências > projetos > locais do SDK > Apple** e selecione **Xcode beta.app**.

5. (Opcional) Se a avaliação dessa visualização usando _Xcode 11 beta 3_, você deve habilitar a vinculação. Clique em seu projeto, navegue até **opções > Build do iOS > comportamento do vinculador** e defina o valor de comportamento do vinculador como **vincular somente SDKs de estruturas**. Essa solução alternativa não é necessária em uma visualização futura.

6. (Opcional) **Instalar 13 do iOS nos dispositivos iOS** – para teste de dispositivos de aplicativos que usam APIs introduzidas com o 11 do Xcode, Apple registrada, os desenvolvedores podem [baixar](https://developer.apple.com/download) e instalar o sistema operacional em seus dispositivos. Porque o iOS está na versão beta, tenha cuidado de instalá-lo em seu dispositivo principal.

   > [!TIP]
   > Mesmo se seu aplicativo não usa novas APIs, certifique-se de criá-lo com os SDKs de 11 mais recente do Xcode e testá-lo para certificar-se de que tudo está funcionando conforme o esperado. Se um aplicativo não chamar novas APIs, você pode recompilá-lo com esses novos SDKs e testá-lo em dispositivos que ainda não foram atualizados para o novo sistema operacional.
   >
   > Antes de atualizar seus dispositivos para o sistema operacional mais recente versões da Apple para testar seus aplicativos Xamarin, não se esqueça:
   >
   > - Leia [notas de versão da Apple](https://developer.apple.com/download/) para as atualizações do sistema operacional.

## <a name="related-links"></a>Links relacionados

- [Baixar o Xcode](https://developer.apple.com/download/)
- [Notas de versão de visualização do xamarin. IOS](/xamarin/ios/release-notes/12/12.99)
