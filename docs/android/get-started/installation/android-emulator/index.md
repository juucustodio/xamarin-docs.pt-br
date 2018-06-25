---
title: Configuração do Google Android Emulator
description: O Google Android Emulator pode ser executado em uma variedade de configurações para simular dispositivos diferentes. Este guia explica como preparar o Android Emulator para testar seu aplicativo.
ms.prod: xamarin
ms.assetid: 889963B7-F4DA-41D9-9B8D-B733BB71A329
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 06/01/2018
ms.openlocfilehash: e5ba2cc23ea9751ca60644d3eb5b7e3f31bbb6bb
ms.sourcegitcommit: a7febc19102209b21e0696256c324f366faa444e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/04/2018
ms.locfileid: "34732525"
---
# <a name="google-android-emulator-setup"></a>Configuração do Google Android Emulator

_Este guia explica como preparar o Google Android Emulator para testar seu aplicativo._


## <a name="overview"></a>Visão geral

O Google Android Emulator pode ser executado em uma variedade de configurações para simular dispositivos diferentes. Cada configuração é chamada de _dispositivo virtual_. Ao implantar e testar seu aplicativo no emulador, você pode selecionar um dispositivo virtual pré-configurado ou personalizado que simula um dispositivo Android físico, como um telefone Nexus ou Pixel.

As seções abaixo descrevem como acelerar o Google Android Emulator para obter um desempenho máximo, como usar o Android Device Manager para criar e personalizar dispositivos virtuais e como personalizar as propriedades do perfil de um dispositivo virtual. Além disso, há uma seção de solução de problemas que explica os problemas de configuração comuns e as soluções alternativas.

## <a name="sections"></a>Seções

### <a name="hardware-acceleration-for-emulator-performanceandroidget-startedinstallationandroid-emulatorhardware-accelerationmd"></a>[Aceleração de hardware para o desempenho do emulador](~/android/get-started/installation/android-emulator/hardware-acceleration.md)

Como preparar seu computador para obter o desempenho máximo do Android Emulator.
Como o Google Android Emulator pode ser extremamente lento sem a aceleração de hardware, recomendamos habilitar essa aceleração no computador antes de usar o emulador.

### <a name="managing-virtual-devices-with-the-android-device-managerandroidget-startedinstallationandroid-emulatordevice-managermd"></a>[Gerenciando dispositivos virtuais com o Android Device Manager](~/android/get-started/installation/android-emulator/device-manager.md)

Como usar o Android Device Manager para criar e personalizar dispositivos virtuais.

### <a name="editing-android-virtual-device-propertiesandroidget-startedinstallationandroid-emulatordevice-propertiesmd"></a>[Editar propriedades do Dispositivo Virtual Android](~/android/get-started/installation/android-emulator/device-properties.md)

Como usar o Android Device Manager para editar as propriedades de perfil de um dispositivo virtual Android.

### <a name="troubleshooting-emulator-setup-problemsandroidget-startedinstallationandroid-emulatortroubleshootingmd"></a>[Solução de problemas de configuração do emulador](~/android/get-started/installation/android-emulator/troubleshooting.md)

Como diagnosticar e corrigir problemas do Android Device Manager que podem ocorrer ao configurar o Android Emulator.


Após configurar o emulador do Android Emulator, confira [Depuração com o Google Android Emulator](~/android/deploy-test/debugging/android-sdk-emulator/index.md) para obter informações de como iniciar o emulador e usá-lo para testar e depurar seu aplicativo.


> [!NOTE]
> Desde o Android SDK Tools versão **26.0.1** e posterior, o Google removeu o suporte para gerenciadores de AVD/SDK existentes em favor de suas novas ferramentas de CLI (interface de linha de comando). Por causa dessa alteração de preterimento, os Gerenciadores de Dispositivos/SDK do Xamarin agora são usados no lugar dos Gerenciadores de Dispositivos/SDK do Google nas Android Tools 26.0.1 e posterior. Para saber mais sobre o Gerenciador de SDK do Xamarin, confira [Configuração do SDK do Android](~/android/get-started/installation/android-sdk.md).

