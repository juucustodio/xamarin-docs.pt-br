---
title: Como é possível reabilitar as opções do desenvolvedor após a atualização do iOS?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: F38BD21E-0C21-43FF-80A6-BB4A88DB88A5
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 04/03/2018
ms.openlocfilehash: 80a705f64a1079ced204b677715f5566c410fd36
ms.sourcegitcommit: aa9b9b203ab4cd6a6b4fd51e27d865e2abf582c1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2018
ms.locfileid: "39350869"
---
# <a name="how-can-i-reenable-developer-options-after-updating-ios"></a>Como é possível reabilitar as opções do desenvolvedor após a atualização do iOS?

Um erro de iOS pode causar as opções do desenvolvedor desaparecer depois de atualizar as versões do iOS, isso foi observado ao alternar para o iOS 8. x. Essas opções podem ser habilitados novamente com as seguintes etapas:

1. Conecte o dispositivo iOS a seu computador de desenvolvimento do Mac.
2. Inicie o Xcode.
3. Abra **Janela > dispositivos** & Selecione seu dispositivo.
4. Aguarde até que o Xcode concluir a cópia de símbolos e verifique Settings.app do dispositivo para as configurações de desenvolvedor.
5. Reinicie o dispositivo.
