---
title: Como posso reabilitar as opções de desenvolvedor depois de atualizar o iOS?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: F38BD21E-0C21-43FF-80A6-BB4A88DB88A5
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 04/03/2018
ms.openlocfilehash: 18651485ad53ca5493a0a47368608214e5f7c595
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70292067"
---
# <a name="how-can-i-reenable-developer-options-after-updating-ios"></a>Como posso reabilitar as opções de desenvolvedor depois de atualizar o iOS?

Um bug do iOS pode fazer com que as opções do desenvolvedor desapareçam depois de atualizar as versões do iOS, isso foi observado ao alternar para o iOS 8. x. Essas opções podem ser reabilitadas com as seguintes etapas:

1. Conecte o dispositivo iOS à sua máquina de desenvolvimento do Mac.
2. Inicie o Xcode.
3. Abra o **windows > dispositivos** & Selecione seu dispositivo.
4. Aguarde até que o Xcode termine de copiar símbolos e, em seguida, verifique as configurações do dispositivo. aplicativo para as configurações do desenvolvedor.
5. Reinicialize o dispositivo.
