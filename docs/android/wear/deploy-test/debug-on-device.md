---
title: Depurar em um dispositivo Wear
description: Este artigo explica como depurar um aplicativo de desgaste do Xamarin. Android em um dispositivo de desgaste.
ms.prod: xamarin
ms.assetid: 01668E4B-BB83-4C26-B23A-F788173FB823
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: 2a0c21f5a985e7a0bbe5b2afac1520280a0bd5e8
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/16/2019
ms.locfileid: "69522135"
---
# <a name="debug-on-a-wear-device"></a>Depurar em um dispositivo Wear

_Este artigo explica como depurar um aplicativo de desgaste do Xamarin. Android em um dispositivo de desgaste._


## <a name="overview"></a>Visão geral

Se você tiver um dispositivo de desgaste do Android, como um SmartWatch de desgaste do Android, poderá executar o aplicativo no dispositivo em vez de usar um emulador. (Se você ainda não estiver familiarizado com o processo de implantação e execução de aplicativos de desgaste do Android, consulte [Olá, desgaste](~/android/wear/get-started/hello-wear.md).)

## <a name="prepare-the-wear-device"></a>Preparar o dispositivo de desgaste:

Use as etapas a seguir para habilitar a depuração no dispositivo Android desgaste:

1. Abra o menu **configurações** no dispositivo Android desgaste.

2. Role até a parte inferior do menu e toque em **sobre**.

3. Toque no número de Build 7 vezes.

4. No menu **configurações** , toque em **Opções do desenvolvedor**.

5. Confirme se a **depuração ADB** está habilitada.


## <a name="debugging-over-usb"></a>Depurando por USB

Se o dispositivo de desgaste tiver uma porta USB, você poderá conectar o dispositivo de desgaste ao seu computador, implantá-lo e executar/depurar o aplicativo como faria usando um telefone Android (para obter mais informações, consulte [Depurar em um dispositivo](~/android/deploy-test/debugging/debug-on-device.md)).


## <a name="debugging-over-bluetooth"></a>Depuração via Bluetooth

Se o dispositivo de desgaste não tiver uma porta USB, você poderá implantar o aplicativo no dispositivo de desgaste via Bluetooth roteando a saída de depuração do aplicativo para um telefone Android conectado ao seu computador. 

### <a name="prepare-your-phone"></a>Preparar seu telefone

Use as etapas a seguir para preparar seu telefone para fazer conexões Bluetooth com o dispositivo de desgaste: 

1. Se você ainda não tiver feito isso, configure seu telefone para o desenvolvimento do Xamarin. Android, conforme explicado em [Configurar dispositivo para desenvolvimento](~/android/get-started/installation/set-up-device-for-development.md).

2. Baixe e instale o aplicativo de [desgaste do Android](https://play.google.com/store/apps/details?id=com.google.android.wearable.app) gratuito do Google Play Store.

### <a name="connect-the-device"></a>Conectar o dispositivo

Use as etapas a seguir para conectar seu dispositivo de desgaste ao seu telefone:

1. No telefone que atuará como intermediário do Bluetooth (configurado acima), inicie o aplicativo Android desgaste. 

2. Toque no ícone **configurações** .

3. Habilite a **depuração via Bluetooth**. Você deve ver o status a seguir exibido na tela do aplicativo Android desgaste:

    ```
    Host: disconnected
    Target: connected
    ```

4. Conecte o telefone ao seu computador por USB. Em seu computador, digite os seguintes comandos:

    ```shell
    adb forward tcp:4444 localabstract:/adb-hub
    adb connect 127.0.0.1:4444
    ```

    Se a porta 4444 não estiver disponível, você poderá usar qualquer outra porta disponível à qual tenha acesso. 

    > [!NOTE]
    > Se você reiniciar o Visual Studio ou o Visual Studio para Mac, deverá executar esses comandos novamente para configurar uma conexão com o dispositivo de desgaste.

5. Quando o dispositivo de desgaste solicitar, confirme que você está permitindo a **depuração do ADB**. No aplicativo Android desgaste, você deve ver o status alterar para:

    ```
    Host: connected
    Target: connected
    ```

6. Depois de concluir as etapas acima, a `adb devices` execução mostra o status do telefone e do dispositivo de desgaste do Android:

    ```
    List of devices attached
    127.0.0.1:4444    device
    019ad61df0a69399  device
    ```

Neste ponto, você pode implantar seu aplicativo no dispositivo de desgaste.

<a name="screenshots" />

### <a name="taking-screenshots"></a>Tirando capturas de tela

Você pode tirar uma captura de tela do dispositivo de desgaste digitando o seguinte comando: 

```shell
adb -s 127.0.0.1:4444 shell screencap -p /sdcard/DCIM/screencap.png
```

Copie a captura de tela em seu computador digitando o seguinte comando:

```shell
adb -s 127.0.0.1:4444 pull /sdcard/DCIM/screencap.png
```

Exclua a captura de tela no dispositivo digitando o seguinte comando:

```shell
adb -s 127.0.0.1:4444 shell rm /sdcard/DCIM/screencap.png
```


### <a name="uninstalling-an-app"></a>Desinstalando um aplicativo

Você pode desinstalar um aplicativo do dispositivo de desgaste digitando o seguinte comando:

```shell
adb -s 127.0.0.1:4444 uninstall <package name>
```

Por exemplo, para remover o aplicativo com o nome `com.xamarin.weartest`do pacote, digite o seguinte comando:

```shell
adb -s 127.0.0.1:4444 uninstall com.xamarin.weartest
```

Para obter mais informações sobre como depurar dispositivos Android de desgaste via Bluetooth, consulte Depurando [sobre Bluetooth](https://developer.android.com/training/wearables/apps/bt-debugging.html).


## <a name="debugging-a-wear-app-with-a-companion-phone-app"></a>Depurando um aplicativo de desgaste com um aplicativo de telefone complementar

Os aplicativos Android desgaste são empacotados com um aplicativo de telefone Android complementar para distribuição no Google Play (para obter mais informações, consulte [trabalhando com empacotamento](~/android/wear/deploy-test/packaging.md)). No entanto, você ainda desenvolve o aplicativo de desgaste e seu aplicativo complementar separadamente. Quando você libera seu aplicativo por meio do Google Play Store, o aplicativo de desgaste será empacotado com o aplicativo complementar e instalado automaticamente, se possível.

Para depurar o aplicativo de desgaste com um aplicativo complementar: 

1. Crie e implante o aplicativo complementar para o telefone.

2. Clique com o botão direito do mouse no projeto de desgaste e defina-o como o projeto de início padrão.

3. Implante o projeto de desgaste no dispositivo portátil.

4. Execute e depure o aplicativo de desgaste no dispositivo.

 
## <a name="summary"></a>Resumo

Este artigo explicou como configurar um dispositivo de desgaste do Android para depuração de desgaste do Visual Studio via Bluetooth e como depurar um aplicativo de desgaste com um aplicativo de telefone complementar. Ele também forneceu dicas de depuração comuns para depurar um aplicativo de desgaste via Bluetooth.
