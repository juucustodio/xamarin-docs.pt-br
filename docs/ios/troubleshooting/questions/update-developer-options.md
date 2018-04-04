---
title: Como é possível reabilitar opções do desenvolvedor após a atualização de iOS?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: F38BD21E-0C21-43FF-80A6-BB4A88DB88A5
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: 9eb25676cc4a3a0679d6a02fc64fef5632f76e33
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="how-can-i-reenable-developer-options-after-updating-ios"></a>Como é possível reabilitar opções do desenvolvedor após a atualização de iOS?

Um erro de iOS pode fazer com que as opções de desenvolvedor para desaparecer depois de atualizar as versões do iOS, isso foi observado ao alternar para o iOS 8. x. Essas opções podem ser habilitados novamente com as seguintes etapas:

1. Conecte o dispositivo iOS para sua máquina de desenvolvimento de Mac.
2. Inicie o Xcode.
3. Abra **Janela > dispositivos** & Selecionar seu dispositivo.
4. Aguarde o Xcode concluir a cópia de símbolos e verifique se Settings.app do dispositivo para as configurações do desenvolvedor.
5. Reinicie o dispositivo.
