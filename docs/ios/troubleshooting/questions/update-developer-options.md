---
title: Como é possível reabilitar as opções do desenvolvedor após a atualização do iOS?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: F38BD21E-0C21-43FF-80A6-BB4A88DB88A5
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 04/03/2018
ms.openlocfilehash: 42f7f63c1da54bc7596d52c78cbcc1ed76931424
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61417676"
---
# <a name="how-can-i-reenable-developer-options-after-updating-ios"></a>Como é possível reabilitar as opções do desenvolvedor após a atualização do iOS?

Um erro de iOS pode causar as opções do desenvolvedor desaparecer depois de atualizar as versões do iOS, isso foi observado ao alternar para o iOS 8. x. Essas opções podem ser habilitados novamente com as seguintes etapas:

1. Conecte o dispositivo iOS a seu computador de desenvolvimento do Mac.
2. Inicie o Xcode.
3. Abra **Janela > dispositivos** & Selecione seu dispositivo.
4. Aguarde até que o Xcode concluir a cópia de símbolos e verifique Settings.app do dispositivo para as configurações de desenvolvedor.
5. Reinicie o dispositivo.
