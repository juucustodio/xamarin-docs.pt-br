---
title: Instalação do Android Emulator
description: O Android Emulator pode ser executado em várias configurações para simular dispositivos diferentes. Este guia explica como preparar o Android Emulator para testar seu aplicativo.
ms.prod: xamarin
ms.assetid: 889963B7-F4DA-41D9-9B8D-B733BB71A329
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 08/27/2018
ms.openlocfilehash: 148afe5354d7995f15dc19c6257ed2a1567162ec
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "73027954"
---
# <a name="android-emulator-setup"></a>Instalação do Android Emulator

_Este guia explica como preparar o Android Emulator para testar seu aplicativo._

## <a name="overview"></a>Visão geral

O Android Emulator pode ser executado em várias configurações para simular dispositivos diferentes. Cada configuração é chamada de _dispositivo virtual_. Ao implantar e testar seu aplicativo no emulador, você pode selecionar um dispositivo virtual pré-configurado ou personalizado que simula um dispositivo Android físico, como um telefone Nexus ou Pixel.

As seções listadas abaixo descrevem como acelerar o Android Emulator para máximo desempenho, como usar o Android Device Manager para criar e personalizar dispositivos virtuais e como personalizar as propriedades de perfil de um dispositivo virtual. Além disso, uma seção de solução de problemas explica problemas comuns do emulador e soluções alternativas.

## <a name="sections"></a>Seções

### <a name="hardware-acceleration-for-emulator-performance"></a>[Aceleração de hardware para o desempenho do emulador](~/android/get-started/installation/android-emulator/hardware-acceleration.md)

Como preparar seu computador para o desempenho máximo do Android Emulator usando a tecnologia de virtualização Hyper-V ou HAXM. Como o Android Emulator pode ser extremamente lento sem a aceleração de hardware, recomendamos que você habilite a aceleração no computador antes de usar o emulador.

### <a name="managing-virtual-devices-with-the-android-device-manager"></a>[Gerenciando dispositivos virtuais com o Android Device Manager](~/android/get-started/installation/android-emulator/device-manager.md)

Como usar o Android Device Manager para criar e personalizar dispositivos virtuais.

### <a name="editing-android-virtual-device-properties"></a>[Editar propriedades do Dispositivo Virtual Android](~/android/get-started/installation/android-emulator/device-properties.md)

Como usar o Android Device Manager para editar as propriedades de perfil de um dispositivo virtual Android.

### <a name="android-emulator-troubleshooting"></a>[Solução de problemas do Android Emulator](~/android/get-started/installation/android-emulator/troubleshooting.md)

Neste artigo, são descritas as mensagens de aviso e os problemas mais comuns que ocorrem durante a execução do Android Emulator, juntamente com soluções alternativas e dicas.

Após configurar o Android Emulator, consulte [Depurando no Android Emulator](~/android/deploy-test/debugging/debug-on-emulator.md) para obter informações de como iniciar o emulador e usá-lo para testar e depurar seu aplicativo.

> [!NOTE]
> Desde o Android SDK Tools versão **26.0.1** e posterior, o Google removeu o suporte para gerenciadores de AVD/SDK existentes em favor de suas novas ferramentas de CLI (interface de linha de comando). Por causa dessa alteração de preterimento, os Gerenciadores de Dispositivos/SDK do Xamarin agora são usados no lugar dos Gerenciadores de Dispositivos/SDK do Google nas Android Tools 26.0.1 e posterior. Para saber mais sobre o Gerenciador de SDK do Xamarin, consulte [Configurando o SDK do Android para Xamarin.Android](~/android/get-started/installation/android-sdk.md).
