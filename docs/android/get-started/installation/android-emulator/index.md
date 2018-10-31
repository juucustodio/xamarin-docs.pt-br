---
title: Instalação do Android Emulator
description: O Android Emulator pode ser executado em várias configurações para simular dispositivos diferentes. Este guia explica como preparar o Android Emulator para testar seu aplicativo.
ms.prod: xamarin
ms.assetid: 889963B7-F4DA-41D9-9B8D-B733BB71A329
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 08/27/2018
ms.openlocfilehash: 6ce8f633cdc0fd4616673eb047d640a8703b3a30
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50102523"
---
# <a name="android-emulator-setup"></a>Instalação do Android Emulator

_Este guia explica como preparar o Android Emulator para testar seu aplicativo._


## <a name="overview"></a>Visão geral

O Android Emulator pode ser executado em várias configurações para simular dispositivos diferentes. Cada configuração é chamada de _dispositivo virtual_. Ao implantar e testar seu aplicativo no emulador, você pode selecionar um dispositivo virtual pré-configurado ou personalizado que simula um dispositivo Android físico, como um telefone Nexus ou Pixel.

As seções listadas abaixo descrevem como acelerar o Android Emulator para máximo desempenho, como usar o Android Device Manager para criar e personalizar dispositivos virtuais e como personalizar as propriedades de perfil de um dispositivo virtual. Além disso, uma seção de solução de problemas explica problemas comuns do emulador e soluções alternativas.

## <a name="sections"></a>Seções

### <a name="hardware-acceleration-for-emulator-performanceandroidget-startedinstallationandroid-emulatorhardware-accelerationmd"></a>[Aceleração de hardware para o desempenho do emulador](~/android/get-started/installation/android-emulator/hardware-acceleration.md)

Como preparar seu computador para o desempenho máximo do Android Emulator usando a tecnologia de virtualização Hyper-V ou HAXM. Como o Android Emulator pode ser extremamente lento sem a aceleração de hardware, recomendamos que você habilite a aceleração no computador antes de usar o emulador.

### <a name="managing-virtual-devices-with-the-android-device-managerandroidget-startedinstallationandroid-emulatordevice-managermd"></a>[Gerenciando dispositivos virtuais com o Android Device Manager](~/android/get-started/installation/android-emulator/device-manager.md)

Como usar o Android Device Manager para criar e personalizar dispositivos virtuais.

### <a name="editing-android-virtual-device-propertiesandroidget-startedinstallationandroid-emulatordevice-propertiesmd"></a>[Editar propriedades do Dispositivo Virtual Android](~/android/get-started/installation/android-emulator/device-properties.md)

Como usar o Android Device Manager para editar as propriedades de perfil de um dispositivo virtual Android.

### <a name="android-emulator-troubleshootingandroidget-startedinstallationandroid-emulatortroubleshootingmd"></a>[Solução de problemas do Android Emulator](~/android/get-started/installation/android-emulator/troubleshooting.md)

Neste artigo, são descritas as mensagens de aviso e os problemas mais comuns que ocorrem durante a execução do Android Emulator, juntamente com soluções alternativas e dicas.

Após configurar o Android Emulator, consulte [Depurando no Android Emulator](~/android/deploy-test/debugging/debug-on-emulator.md) para obter informações de como iniciar o emulador e usá-lo para testar e depurar seu aplicativo.


> [!NOTE]
> Desde o Android SDK Tools versão **26.0.1** e posterior, o Google removeu o suporte para gerenciadores de AVD/SDK existentes em favor de suas novas ferramentas de CLI (interface de linha de comando). Por causa dessa alteração de preterimento, os Gerenciadores de Dispositivos/SDK do Xamarin agora são usados no lugar dos Gerenciadores de Dispositivos/SDK do Google nas Android Tools 26.0.1 e posterior. Para saber mais sobre o Gerenciador de SDK do Xamarin, consulte [Configurando o SDK do Android para Xamarin.Android](~/android/get-started/installation/android-sdk.md).

