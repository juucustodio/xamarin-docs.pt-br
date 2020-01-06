---
title: Depurar aplicativos Xamarin. Android em dispositivos e emuladores
description: Como testar e depurar aplicativos Xamarin.Android
ms.prod: xamarin
ms.assetid: A355A471-8195-4391-93FE-0000BCB17923
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 06/22/2018
ms.openlocfilehash: 3b3fa14ec81bd4f06322197b7140654f9086ce73
ms.sourcegitcommit: 5821c9709bf5e06e6126233932f94f9cf3524577
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/31/2019
ms.locfileid: "75556477"
---
# <a name="debug-xamarinandroid-apps"></a>Depurar aplicativos Xamarin. Android

Esta seção discute como depurar um aplicativo Xamarin.Android em dispositivos ou emuladores.

## <a name="debugging-overview"></a>Visão geral da depuração

O desenvolvimento de aplicativos Android requer a execução do aplicativo seja em um hardware físico ou usando um emulador. O uso de hardware é a melhor abordagem, mas nem sempre a mais prática. Em muitos casos, pode ser mais simples e mais econômico simular/emular hardware do Android usando um dos emuladores descritos abaixo.

### <a name="debugging-on-the-android-emulatorandroiddeploy-testdebuggingdebug-on-emulatormd"></a>[Depurando no Android Emulator](~/android/deploy-test/debugging/debug-on-emulator.md)

Este artigo explica como iniciar o Android Emulator por meio do Visual Studio e executar seu aplicativo em um dispositivo virtual.

### <a name="debugging-on-a-deviceandroiddeploy-testdebuggingdebug-on-devicemd"></a>[Depurando em um dispositivo](~/android/deploy-test/debugging/debug-on-device.md)

Este artigo mostra como configurar um dispositivo Android físico para que o aplicativo Xamarin. Android possa ser implantado diretamente do Visual Studio ou Visual Studio para Mac.

### <a name="android-debug-logandroiddeploy-testdebuggingandroid-debug-logmd"></a>[Log de depuração do Android](~/android/deploy-test/debugging/android-debug-log.md)

Um truque muito comum que os desenvolvedores usam para depurar seus aplicativos é usar o `Console.WriteLine`. No entanto, em uma plataforma móvel como Android não há console. Dispositivos com Android fornecem um log que provavelmente você precisará utilizar durante a gravação de aplicativos. Às vezes é chamado de **logcat** devido ao comando digitado para recuperá-lo. Este artigo descreve como usar o **logcat**.
