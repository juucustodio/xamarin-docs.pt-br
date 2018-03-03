---
title: Depurar em um dispositivo de desgaste
description: Este artigo explica como depurar um aplicativo xamarin desgaste em um dispositivo de desgaste.
ms.topic: article
ms.prod: xamarin
ms.assetid: 01668E4B-BB83-4C26-B23A-F788173FB823
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: 5ec627fad1695bab8d05d75a5089fe849ea2fd75
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="debug-on-a-wear-device"></a>Depurar em um dispositivo de desgaste

_Este artigo explica como depurar um aplicativo xamarin desgaste em um dispositivo de desgaste._


## <a name="overview"></a>Visão geral

Se você tiver um dispositivo Android desgaste como um Smartwatch de desgaste Android, você pode executar o aplicativo no dispositivo em vez de usar um emulador. (Se você não estiver familiarizado com o processo de implantação e execução de aplicativos Android desgaste, consulte [desgaste Hello,](~/android/wear/get-started/hello-wear.md).)

## <a name="prepare-the-wear-device"></a>Prepare o dispositivo de desgaste:

Use as etapas a seguir para habilitar a depuração no dispositivo Android desgaste:

1.  Abra o **configurações** menu no dispositivo Android desgaste.

2.  Role até a parte inferior do menu e toque **sobre**.

3.  Toque no número de compilação 7 vezes.

4.  Sobre o **configurações** menu, toque em **opções do desenvolvedor**.

5.  Confirme se **ADB depuração** está habilitado.


## <a name="debugging-over-usb"></a>Depuração em USB

Se o dispositivo de desgaste tiver uma porta USB, você pode conectar o dispositivo de desgaste em seu computador, implantar a ele e executar/depurar o aplicativo como se estivesse usando um telefone Android (para obter mais informações, consulte [depurar em um dispositivo](~/android/deploy-test/debugging/debug-on-device.md)).


## <a name="debugging-over-bluetooth"></a>Depuração via Bluetooth

Se o dispositivo de desgaste não tiver uma porta USB, você pode implantar o aplicativo para o dispositivo de desgaste via Bluetooth direcionando a saída de depuração do aplicativo para um telefone Android que está conectado ao seu computador. 

### <a name="prepare-your-phone"></a>Preparar seu telefone

Use as etapas a seguir para preparar seu telefone para fazer conexões de Bluetooth no dispositivo de desgaste: 

1.  Se você ainda não tiver feito isso, configure o telefone para desenvolvimento do xamarin conforme explicado em [definir o dispositivo para desenvolvimento](~/android/get-started/installation/set-up-device-for-development.md).

2.  Baixe e instale a versão gratuita [desgaste Android](https://play.google.com/store/apps/details?id=com.google.android.wearable.app) aplicativo da Google Play Store.

### <a name="connect-the-device"></a>Conecte o dispositivo

Use as etapas a seguir para conectar seu dispositivo desgaste a seu telefone:

1.  No telefone que será atuar como Bluetooth intermediário (configurado acima), inicie o aplicativo Android usam. 

2.  Toque na **configurações** ícone.

3.  Habilitar **depuração via Bluetooth**. Você verá os seguintes status exibido na tela do aplicativo Android usam:

        Host: disconnected
        Target: connected

4.  Conecte o telefone em seu computador por USB. Em seu computador, digite os seguintes comandos:

    ```shell
    adb forward tcp:4444 localabstract:/adb-hub
    adb connect 127.0.0.1:4444
    ```

    Se a porta 4444 não estiver disponível, você pode usar qualquer outra porta disponível para o qual você tem acesso. 

    **Observação**: se você reiniciar o Visual Studio ou o Visual Studio para Mac, você deve executar esses comandos novamente para configurar uma conexão para o dispositivo de desgaste.

5.  Quando o dispositivo de desgaste solicita, confirme que você está permitindo que **ADB depuração**. No aplicativo do Android desgaste, você deve ver o status de alterar para:

        Host: connected
        Target: connected

6.  Depois de concluir as etapas acima, executando `adb devices` mostra o status de telefone e o dispositivo Android desgaste:

        List of devices attached
        127.0.0.1:4444    device
        019ad61df0a69399  device

Neste ponto, você pode implantar seu aplicativo para o dispositivo de desgaste.

<a name="screenshots"/>

### <a name="taking-screenshots"></a>Fazer capturas de tela

Você pode executar uma captura de tela do dispositivo desgaste digitando o seguinte comando: 

```shell
adb -s 127.0.0.1:4444 shell screencap -p /sdcard/DCIM/screencap.png
```

Copie a captura de tela para seu computador digitando o seguinte comando:

```shell
adb -s 127.0.0.1:4444 pull /sdcard/DCIM/screencap.png
```

Exclua a captura de tela no dispositivo digitando o seguinte comando:

```shell
adb -s 127.0.0.1:4444 shell rm /sdcard/DCIM/screencap.png
```


### <a name="uninstalling-an-app"></a>Desinstalar um aplicativo

Você pode desinstalar um aplicativo do dispositivo desgaste digitando o seguinte comando:

```shell
adb -s 127.0.0.1:4444 uninstall <package name>
```

Por exemplo, para remover o aplicativo com o nome do pacote `com.xamarin.weartest`, digite o seguinte comando:

```shell
adb -s 127.0.0.1:4444 uninstall com.xamarin.weartest
```

Para obter mais informações sobre como depurar dispositivos Android desgaste via Bluetooth, consulte [depuração via Bluetooth](https://developer.android.com/training/wearables/apps/bt-debugging.html).


## <a name="debugging-a-wear-app-with-a-companion-phone-app"></a>Depurar um aplicativo de desgaste com um aplicativo de telefone complementar

Aplicativos do Android desgaste são empacotados com um aplicativo de telefone Android complementar para distribuição no Google Play (para obter mais informações, consulte [trabalhar com empacotamento](~/android/wear/deploy-test/packaging.md)). No entanto, você ainda desenvolver o aplicativo de desgaste e seu aplicativo complementar separadamente. Quando você liberar seu aplicativo por meio da loja do Google Play, o aplicativo de desgaste será ser empacotado com o aplicativo complementar e instalado automaticamente se possível.

Para depurar o aplicativo de desgaste com um aplicativo complementar: 

1.  Criar e implantar o aplicativo complementar para o telefone.

2.  Clique com botão direito no projeto de desgaste e defina-o como o projeto de início padrão.

3.  Implante o projeto de desgaste para o dispositivo de vestir.

4.  Executar e depurar o aplicativo de desgaste no dispositivo.

 
## <a name="summary"></a>Resumo

Este artigo explicou como configurar um dispositivo Android desgaste para depuração de desgaste do Visual Studio via Bluetooth e como depurar um aplicativo de desgaste com um aplicativo de telefone complementar. Também forneceu as dicas de depuração comuns para depurar um aplicativo de desgaste via Bluetooth.
