---
title: Depurando o Xamarin.Android em dispositivo e emuladores
description: Como testar e depurar aplicativos Xamarin.Android
ms.prod: xamarin
ms.assetid: A355A471-8195-4391-93FE-0000BCB17923
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/22/2018
ms.openlocfilehash: 8fb647e12de621fc0772ad5c18aac21e46758715
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50113099"
---
# <a name="debugging"></a>Depuração

Esta seção discute como depurar um aplicativo Xamarin.Android em dispositivos ou emuladores.

## <a name="debugging-overview"></a>Visão geral da depuração

O desenvolvimento de aplicativos Android requer a execução do aplicativo seja em um hardware físico ou usando um emulador. O uso de hardware é a melhor abordagem, mas nem sempre a mais prática. Em muitos casos, pode ser mais simples e mais econômico simular/emular hardware do Android usando um dos emuladores descritos abaixo.

### <a name="debugging-on-the-android-emulatorandroiddeploy-testdebuggingdebug-on-emulatormd"></a>[Depurando no Android Emulator](~/android/deploy-test/debugging/debug-on-emulator.md)

Este artigo explica como iniciar o Android Emulator por meio do Visual Studio e executar seu aplicativo em um dispositivo virtual.

### <a name="debugging-on-a-deviceandroiddeploy-testdebuggingdebug-on-devicemd"></a>[Depurando em um dispositivo](~/android/deploy-test/debugging/debug-on-device.md)

Este artigo mostra como configurar um dispositivo Android físico para que o aplicativo Xamarin.Android possa ser implantado nele diretamente, do Visual Studio para Mac ou Visual Studio.

### <a name="android-debug-logandroiddeploy-testdebuggingandroid-debug-logmd"></a>[Log de depuração do Android](~/android/deploy-test/debugging/android-debug-log.md)

Um truque muito comum que os desenvolvedores usam para depurar seus aplicativos é usar o `Console.WriteLine`. No entanto, em uma plataforma móvel como Android não há console. Dispositivos com Android fornecem um log que provavelmente você precisará utilizar durante a gravação de aplicativos. Às vezes é chamado de **logcat** devido ao comando digitado para recuperá-lo. Este artigo descreve como usar o **logcat**.

> [!WARNING]
> Observe que o **Xamarin Android Player** foi preterido. Para obter mais informações, consulte o [comunicado nesta postagem de blog](https://blog.xamarin.com/live-from-dotnetconf-cycle-7-xamarin-studio-6-and-more/). Além disso, o **Emulador do Microsoft Visual Studio para Android** foi preterido a partir do Visual Studio de 2017.
