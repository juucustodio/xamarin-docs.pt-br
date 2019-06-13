---
title: Solução de problemas de 13 de iOS e Xamarin
description: Esta seção contém dicas de solução de problemas para a funcionalidade do Xamarin relacionados ao iOS 13.
ms.prod: xamarin
ms.assetid: 00DE8C33-1407-45C0-A0C7-32AF1E490034
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 06/12/2019
ms.openlocfilehash: 99fd7e41e1521c6a9254d286bf989281658ccf24
ms.sourcegitcommit: 85c45dc28ab3625321c271804768d8e4fce62faf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67039783"
---
# <a name="troubleshooting-tips-for-ios-13-and-xamarinios"></a>Dicas de solução de 13 de iOS e xamarin. IOS

![Versão prévia do recurso](~/media/shared/preview.png)

## <a name="updating-to-xcode-11-stops-the-simulator-from-launching"></a>Atualizando para o Xcode 11 interrompe o simulador de iniciar

Depois de atualizar para **Xcode 11 beta 1** sempre que um simulador é aberto a seguinte exceção é lançada e o simulador não é iniciado. Isso acontece com todos os simuladores.

### <a name="exception"></a>Exceção

`Foundation.ObjCException: NSInvalidArgumentException: -[SimDevice registerNotificationHandler:]: unrecognized selector sent to instance 0x7ffbf5d1e110`

### <a name="workaround"></a>Solução alternativa

Até que haja uma [corrigir](https://github.com/xamarin/xamarin-macios/issues/6216), as etapas a seguir podem ser seguidas para reinstalar o framework de simulador antigo para permitir que os desenvolvedores continuem a funcionar:

> [!NOTE]
> Essas etapas pressupõem que você tem dois aplicativos de Xcode:
> - **Xcode11 beta1.app** – a versão beta, que não funciona com simuladores e o Visual Studio para Mac.
> - **Xcode102.App** – uma versão estável do Xcode 10. Sua também podem ser chamada **Xcode. App**.
>
> Altere os exemplos de linha de comando abaixo conforme apropriado para sua configuração.

1. Certifique-se de que você tenha selecionado por meio do xcode-select de 11 de Xcode:

   `sudo xcode-select -s /Applications/Xcode11-beta1.app/Contents/Developer/`

2. Executar, se necessário, a instalação das ferramentas pela primeira vez.

    `/Applications/Xcode11-beta1.app/Contents/Developer/usr/bin/xcodebuild -runFirstLaunch`

3. Remova a seguinte estrutura:

    `sudo rm -Rf  /Library/Developer/PrivateFrameworks/CoreSimulator.framework/Versions/*`

4. Volte para a versão antiga do Xcode

   `sudo xcode-select -s /Applications/Xcode102.app/Contents/Developer/`

5. Execute novamente a primeira ferramenta de inicialização para o _antigo_ você acabou de selecionar versão do Xcode

   `/Applications/Xcode102.app/Contents/Developer/usr/bin/xcodebuild -runFirstLaunch`

Depois de seguir essas etapas, você deve ser capaz de trabalhar com o simulador de iOS novamente.