---
title: Solução de problemas do Xamarin e iOS 13
description: Esta seção contém dicas de solução de problemas para a funcionalidade do Xamarin relacionada ao iOS 13.
ms.prod: xamarin
ms.assetid: 00DE8C33-1407-45C0-A0C7-32AF1E490034
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 06/12/2019
ms.openlocfilehash: d2a2146a0b7345475e2eb93d52fb02387c833224
ms.sourcegitcommit: 1e3a0d853669dcc57d5dee0894d325d40c7d8009
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/31/2019
ms.locfileid: "70200043"
---
# <a name="troubleshooting-tips-for-ios-13-and-xamarinios"></a>Dicas de solução de problemas para iOS 13 e Xamarin. iOS

![Versão prévia do recurso](~/media/shared/preview.png)

## <a name="updating-to-xcode-11-stops-the-simulator-from-launching"></a>A atualização para o Xcode 11 interrompe a inicialização do simulador

Após a atualização para o **Xcode 11 Beta 1** sempre que um simulador for iniciado, a seguinte exceção será lançada e o simulador não será iniciado. Isso acontece com todos os simuladores.

### <a name="exception"></a>Exceção

`Foundation.ObjCException: NSInvalidArgumentException: -[SimDevice registerNotificationHandler:]: unrecognized selector sent to instance 0x7ffbf5d1e110`

### <a name="workaround"></a>Solução alternativa

Até que haja uma [correção](https://github.com/xamarin/xamarin-macios/issues/6216), as etapas a seguir podem ser seguidas para reinstalar a estrutura do simulador anterior para permitir que os desenvolvedores continuem a funcionar:

> [!NOTE]
> Estas etapas pressupõem que você tenha dois aplicativos do Xcode:
> - **Xcode11-beta1. app** – a versão beta que não funciona com simuladores e Visual Studio para Mac.
> - **Xcode102. app** – uma versão estável do Xcode 10. Sua também pode ser chamada de **Xcode. app**.
>
> Altere os exemplos de linha de comando abaixo conforme apropriado para sua configuração.

1. Verifique se você tem o Xcode 11 selecionado por meio do Xcode – selecione:

   `sudo xcode-select -s /Applications/Xcode11-beta1.app/Contents/Developer/`

2. Execute, se necessário, as ferramentas de instalação pela primeira vez.

    `/Applications/Xcode11-beta1.app/Contents/Developer/usr/bin/xcodebuild -runFirstLaunch`

3. Remova a seguinte estrutura:

    `sudo rm -Rf  /Library/Developer/PrivateFrameworks/CoreSimulator.framework/Versions/*`

4. Voltar para a versão antiga do Xcode

   `sudo xcode-select -s /Applications/Xcode102.app/Contents/Developer/`

5. Execute novamente a primeira ferramenta de inicialização para a versão _antiga_ do Xcode que você acabou de selecionar

   `/Applications/Xcode102.app/Contents/Developer/usr/bin/xcodebuild -runFirstLaunch`

Depois de seguir essas etapas, você poderá trabalhar com o simulador de iOS novamente.
