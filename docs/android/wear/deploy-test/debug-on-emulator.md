---
title: Depurar o desgaste do Android em um emulador
description: Estes artigos explicam como depurar um aplicativo de desgaste do Xamarin. Android em um emulador.
ms.prod: xamarin
ms.assetid: 225684B2-3122-4E3B-A028-A3A400976D31
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/21/2018
ms.openlocfilehash: f085aaffbedb2965222b98a22cf6a4bb2393642b
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70764035"
---
# <a name="debug-android-wear-on-an-emulator"></a>Depurar o desgaste do Android em um emulador

_Estes artigos explicam como depurar um aplicativo de desgaste do Xamarin. Android em um emulador._

## <a name="debug-wear-on-emulator-overview"></a>Visão geral de depuração de desgaste no emulador

Desenvolver aplicativos Android desgaste requer a execução do aplicativo, seja em hardware físico ou usando um emulador ou simulador. O uso de hardware é a melhor abordagem, mas nem sempre a mais prática. Em muitos casos, pode ser mais simples e mais econômico simular/emular hardware de desgaste do Android usando um emulador, conforme descrito abaixo. Se você ainda não estiver familiarizado com o processo de implantação e execução de aplicativos Android desgaste, consulte [Olá, desgaste](~/android/wear/get-started/hello-wear.md).

## <a name="configure-the-android-emulator"></a>Configurar o Android Emulator

Para executar seu aplicativo de desgaste em um emulador, você deve instalar o SDK do Android Android Emulator e configurá-lo para o desgaste do Android. Para obter informações gerais de instalação e configuração do emulador de SDK do Android, consulte [Android Emulator setup](~/android/get-started/installation/android-emulator/index.md).

Ao criar um dispositivo virtual de desgaste, selecione um perfil de dispositivo de desgaste do Android (como o **quadrado de desgaste do Android**). Para melhorar o desempenho, use a CPU/ABI **x86** de desgaste, conforme mostrado neste exemplo:

[![Exemplo de configuração de dispositivo virtual de desgaste](debug-on-emulator-images/01-wear-avd-example-sml.png)](debug-on-emulator-images/01-wear-avd-example.png#lightbox)

## <a name="launch-the-wear-virtual-device"></a>Iniciar o dispositivo virtual de desgaste 

Depois de criar um dispositivo virtual de desgaste do Android, você pode escolher no menu suspenso do dispositivo no IDE antes de iniciar a depuração. Se o seu dispositivo virtual não estiver disponível no pull-down do dispositivo, verifique se seu projeto é um projeto de aplicativo de *desgaste* do Android (não um projeto de aplicativo do Android) e se seu nível de API de destino está definido como o mesmo nível de API do dispositivo virtual. Por exemplo:

[![Escolhendo um AVD de desgaste no menu de dispositivo do Visual Studio](debug-on-emulator-images/vs/choose-wear-sim.png)](debug-on-emulator-images/vs/choose-wear-sim.png#lightbox)

Após o início do emulador do Android, o Xamarin. Android implantará o aplicativo de desgaste no emulador. O emulador executa o aplicativo com a imagem de dispositivo virtual configurada.

Não se surpreenda se você vir esta (ou outra tela intersticial) a princípio. O emulador de inspeção pode demorar um pouco para ser inicializado: 

![O emulador de inspeção exibe apenas um minuto...](debug-on-emulator-images/please-wait.png)

O emulador pode ser deixado em execução, não é necessário desligá-lo e reiniciá-lo toda vez que o aplicativo for executado.

## <a name="summary"></a>Resumo

Este guia explicou como configurar o Android Emulator para desenvolvimento de desgaste e iniciar um dispositivo virtual de desgaste para depuração.
