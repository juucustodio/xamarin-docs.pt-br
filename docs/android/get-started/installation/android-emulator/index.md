---
title: Instalação do Android Emulator
description: Esta seção descreve como preparar o Google Android Emulator para testar um aplicativo. Também é explicado como acelerar o emulador para obter o máximo desempenho e mostrado como usar um gerenciador de emulador para criar e personalizar dispositivos virtuais.
ms.prod: xamarin
ms.assetid: 889963B7-F4DA-41D9-9B8D-B733BB71A329
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 01/25/2018
ms.openlocfilehash: 215e298068b7a3a23b2e469e923f172c8303bbcb
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/10/2018
---
# <a name="android-emulator-setup"></a>Instalação do Android Emulator

_Esta seção descreve como preparar o Google Android Emulator para testar um aplicativo. Também é explicado como acelerar o emulador para obter o máximo desempenho e mostrado como usar um gerenciador de emulador para criar e personalizar dispositivos virtuais._


## <a name="overview"></a>Visão geral

Um emulador do SDK do Google pode ser executado em uma variedade de configurações para simular dispositivos diferentes. Cada uma dessas configurações é criada como um _dispositivo virtual_. Este guia mostrará como acelerar o Android Emulator para obter um desempenho melhor e como usar o Gerenciador do Xamarin Android Emulator ou o Google Emulator Manager herdado para criar dispositivos virtuais.


> [!NOTE]
> Desde o Android SDK Tools versão **26.0.1** e posterior, o Google removeu o suporte para gerenciadores de AVD/SDK existentes em favor de suas novas ferramentas de CLI (interface de linha de comando). Por causa dessa alteração na reprovação, os Gerenciadores de Dispositivos/SDK do Xamarin agora são usados no lugar dos Gerenciadores de Emulador/SDK do Google no Android Tools 26.0.1 e posterior. Para saber mais sobre o Gerenciador de SDK do Xamarin, consulte [Instalação do SDK do Android](~/android/get-started/installation/android-sdk.md).


## <a name="sections"></a>Seções

### <a name="hardware-accelerationandroidget-startedinstallationandroid-emulatorhardware-accelerationmd"></a>[Aceleração de hardware](~/android/get-started/installation/android-emulator/hardware-acceleration.md)

Como preparar seu computador para obter o desempenho máximo do Google Android Emulator. Como o Google Android Emulator pode ser extremamente lento sem a aceleração de hardware, recomendamos habilitar essa função no computador antes de usar o Google Android Emulator.

### <a name="xamarin-android-device-managerandroidget-startedinstallationandroid-emulatorxamarin-device-managermd"></a>[Gerenciador de Dispositivos do Xamarin Android](~/android/get-started/installation/android-emulator/xamarin-device-manager.md)

Como usar o Xamarin Android Device Manager para criar e personalizar dispositivos virtuais do Google Android Emulator. O **Gerenciador de Dispositivos Android do Xamarin** está em versão prévia atualmente e deve substituir o Google Emulator Manager herdado. Se seu alvo é o Android Oreo 8.0 ou posterior, é necessário utilizar o Gerenciador de Dispositivos Android do Xamarin em vez do Google Emulator Manager.

### <a name="google-emulator-managerandroidget-startedinstallationandroid-emulatorgoogle-emulator-managermd"></a>[Google Emulator Manager](~/android/get-started/installation/android-emulator/google-emulator-manager.md)

Como usar o Google Emulator Manager herdado para criar e personalizar dispositivos virtuais do Google Android Emulator. É possível continuar a executar o Google Android Emulator com o Gerenciador de Emulador original do Google continuando na versão 25.2.5 ou inferior do Android SDK Tools.

Após configurar o emulador do SDK do Android, confira [Google Android Emulator](~/android/deploy-test/debugging/android-sdk-emulator/index.md) para obter informações sobre como iniciar o emulador e usá-lo para testar e depurar seu aplicativo.
