---
title: Depurando o Xamarin.Android em dispositivo e emuladores
description: Como testar e depurar aplicativos Xamarin.Android
ms.prod: xamarin
ms.assetid: A355A471-8195-4391-93FE-0000BCB17923
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 05/30/2018
ms.openlocfilehash: e1c2a591450d8a5fd0aebe2bceb1d914a711512e
ms.sourcegitcommit: a7febc19102209b21e0696256c324f366faa444e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/04/2018
ms.locfileid: "34732213"
---
# <a name="debugging"></a>Depuração

Esta seção discute como depurar um aplicativo Xamarin.Android em dispositivos ou emuladores.

## <a name="debugging-overview"></a>Visão geral da depuração

O desenvolvimento de aplicativos Android requer a execução do aplicativo seja em um hardware físico ou usando um emulador. O uso de hardware é a melhor abordagem, mas nem sempre a mais prática. Em muitos casos, pode ser mais simples e mais econômico simular/emular hardware do Android usando um dos emuladores descritos abaixo.

### <a name="debugging-with-the-google-android-emulatorandroiddeploy-testdebuggingandroid-sdk-emulatorindexmd"></a>[Depurando com o Google Android Emulator](~/android/deploy-test/debugging/android-sdk-emulator/index.md)

Estes artigos explicam como usar o emulador padrão, fornecido com o SDK do Android. Este emulador está disponível para o Visual Studio para Windows e para o Visual Studio para Mac.

### <a name="visual-studio-android-emulatorandroiddeploy-testdebuggingvisual-studio-android-emulatormd"></a>[Android Emulator do Visual Studio](~/android/deploy-test/debugging/visual-studio-android-emulator.md)

Este artigo explica como depurar e testar aplicativos Xamarin.Android usando o Android Emulator incorporado ao Visual Studio 2015. Esse emulador é uma boa opção se você estiver usando o Visual Studio 2015 e não precisa de perfis de dispositivo personalizados.

### <a name="debugging-on-a-deviceandroiddeploy-testdebuggingdebug-on-devicemd"></a>[Depurando em um dispositivo](~/android/deploy-test/debugging/debug-on-device.md)

Este artigo mostra como configurar um dispositivo Android físico para que o aplicativo Xamarin.Android possa ser implantado nele diretamente, do Visual Studio para Mac ou Visual Studio.

### <a name="android-debug-logandroiddeploy-testdebuggingandroid-debug-logmd"></a>[Log de depuração do Android](~/android/deploy-test/debugging/android-debug-log.md)

Um truque muito comum que os desenvolvedores usam para depurar seus aplicativos é usar o `Console.WriteLine`. No entanto, em uma plataforma móvel como Android não há console. Dispositivos com Android fornecem um log que provavelmente você precisará utilizar durante a gravação de aplicativos. Às vezes é chamado de **logcat** devido ao comando digitado para recuperá-lo. Este artigo descreve como usar o **logcat**.

> [!WARNING]
> Observe que o **Xamarin Android Player** foi preterido. Para obter mais informações, consulte o [comunicado nesta postagem de blog](https://blog.xamarin.com/live-from-dotnetconf-cycle-7-xamarin-studio-6-and-more/).
