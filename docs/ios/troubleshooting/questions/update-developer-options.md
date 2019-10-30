---
title: Como posso reabilitar as opções de desenvolvedor depois de atualizar o iOS?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: F38BD21E-0C21-43FF-80A6-BB4A88DB88A5
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 04/03/2018
ms.openlocfilehash: e9ba068e9d68b6b8d6e90e927ec0252dd6e7637f
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73030898"
---
# <a name="how-can-i-reenable-developer-options-after-updating-ios"></a>Como posso reabilitar as opções de desenvolvedor depois de atualizar o iOS?

Um bug do iOS pode fazer com que as opções do desenvolvedor desapareçam depois de atualizar as versões do iOS, isso foi observado ao alternar para o iOS 8. x. Essas opções podem ser reabilitadas com as seguintes etapas:

1. Conecte o dispositivo iOS à sua máquina de desenvolvimento do Mac.
2. Inicie o Xcode.
3. Abra o **windows > dispositivos** & Selecione seu dispositivo.
4. Aguarde até que o Xcode termine de copiar símbolos e, em seguida, verifique as configurações do dispositivo. aplicativo para as configurações do desenvolvedor.
5. Reinicialize o dispositivo.
