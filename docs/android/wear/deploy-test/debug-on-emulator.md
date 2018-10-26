---
title: Depurar Android Wear em um emulador
description: Estes artigos explicam como depurar um aplicativo xamarin. Android Wear em um emulador.
ms.prod: xamarin
ms.assetid: 225684B2-3122-4E3B-A028-A3A400976D31
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/21/2018
ms.openlocfilehash: 699fb3cc3a5730e8ab2c677feb7cdfbdcf106aeb
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50120548"
---
# <a name="debug-android-wear-on-an-emulator"></a>Depurar Android Wear em um emulador

_Estes artigos explicam como depurar um aplicativo xamarin. Android Wear em um emulador._

## <a name="debug-wear-on-emulator-overview"></a>Depurar o desgaste de visão geral do emulador

Desenvolver aplicativos Android Wear requer a execução do aplicativo, em hardware físico ou usando um emulador ou simulador. O uso de hardware é a melhor abordagem, mas nem sempre a mais prática. Em muitos casos, pode ser mais simples e mais econômico simular/emular hardware Android Wear, usando um emulador, conforme descrito abaixo. Se você ainda não conhece o processo de implantação e execução de aplicativos Android Wear, consulte [Olá, Wear](~/android/wear/get-started/hello-wear.md).

## <a name="configure-the-android-emulator"></a>Configurar o emulador do Android

Para executar seu aplicativo de desgaste em um emulador, você deve instalar o emulador do Android SDK do Android e configurá-lo para o Android Wear. Para informações de geral de emulador do SDK Android instalação e configuração, consulte [configuração do Android Emulator](~/android/get-started/installation/android-emulator/index.md).

Quando você cria um dispositivo virtual de desgaste, selecione um perfil de dispositivo do Android Wear (como **Android Wear quadrado**). Para melhorar o desempenho, use o desgaste **x86** CPU/ABI como visto neste exemplo:

[![Exemplo de configuração de dispositivo virtual de desgaste](debug-on-emulator-images/01-wear-avd-example-sml.png)](debug-on-emulator-images/01-wear-avd-example.png#lightbox)


## <a name="launch-the-wear-virtual-device"></a>Inicie o dispositivo Virtual de desgaste 

Depois que você criou um dispositivo virtual Android Wear, escolha-o no menu suspenso dispositivo no IDE antes de iniciar a depuração. Se seu dispositivo virtual não está disponível no dispositivo pendente, verifique se seu projeto é um Android *Wear* no nível de projeto de aplicativo (não um projeto de aplicativo do Android) e que seu nível de API de destino está definido como a mesma API que o dispositivo virtual. Por exemplo:

[![Escolhendo um AVD Wear no menu de dispositivo do Visual Studio](debug-on-emulator-images/vs/choose-wear-sim.png)](debug-on-emulator-images/vs/choose-wear-sim.png#lightbox)

Após inicia o emulador do Android, xamarin. Android implantará o aplicativo de desgaste no emulador. O emulador executa o aplicativo com a imagem de dispositivo virtual configurada.

Não fique surpreso se você vir isso (ou outra tela intersticial) no início. O emulador de inspeção pode levar algum tempo para ser inicializado: 

![Assista ao emulador exibe apenas um minuto...](debug-on-emulator-images/please-wait.png)

O emulador pode ser deixado em execução, não é necessário desligá-lo e reiniciá-lo toda vez que o aplicativo for executado.

 
## <a name="summary"></a>Resumo
 
Este guia explicou como configurar o emulador do Android para desenvolvimento de desgaste e inicializar um dispositivo virtual de desgaste para depuração.
