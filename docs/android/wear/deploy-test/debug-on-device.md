---
title: Depurar em um dispositivo Wear
description: Este artigo explica como depurar um aplicativo xamarin. Android Wear em um dispositivo Wear.
ms.prod: xamarin
ms.assetid: 01668E4B-BB83-4C26-B23A-F788173FB823
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: 232fcd1d369eba1daad170986f2e2c4c913a3649
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61307857"
---
# <a name="debug-on-a-wear-device"></a>Depurar em um dispositivo Wear

_Este artigo explica como depurar um aplicativo xamarin. Android Wear em um dispositivo Wear._


## <a name="overview"></a>Visão geral

Se você tiver um dispositivo Android Wear, como um Smartwatch de Android Wear, você pode executar o aplicativo no dispositivo em vez de usar um emulador. (Se você ainda não conhece o processo de implantação e execução de aplicativos Android Wear, consulte [Olá, Wear](~/android/wear/get-started/hello-wear.md).)

## <a name="prepare-the-wear-device"></a>Prepare o dispositivo de desgaste:

Use as etapas a seguir para habilitar a depuração no dispositivo Android Wear:

1.  Abra o **configurações** menu no dispositivo Android Wear.

2.  Role até a parte inferior do menu e toque **sobre**.

3.  Toque no número de build 7 vezes.

4.  Sobre o **as configurações** menu, toque em **opções do desenvolvedor**.

5.  Confirme **ADB depuração** está habilitado.


## <a name="debugging-over-usb"></a>Depuração em USB

Se o dispositivo de desgaste tiver uma porta USB, você pode conectar o dispositivo de desgaste ao seu computador, implantá-la e executar/depurar o aplicativo como se estivesse usando um telefone Android (para obter mais informações, consulte [depurar em um dispositivo](~/android/deploy-test/debugging/debug-on-device.md)).


## <a name="debugging-over-bluetooth"></a>Depuração via Bluetooth

Se seu dispositivo de desgaste não tiver uma porta USB, você pode implantar o aplicativo para o dispositivo de desgaste via Bluetooth pelo roteamento de saída de depuração do aplicativo para um telefone com Android que está conectado ao seu computador. 

### <a name="prepare-your-phone"></a>Preparar seu telefone

Use as etapas a seguir para preparar seu telefone para fazer conexões de Bluetooth no dispositivo de desgaste: 

1.  Se você ainda não fez isso, configure o seu telefone para o desenvolvimento do xamarin. Android conforme explicado em [configurar o dispositivo para desenvolvimento](~/android/get-started/installation/set-up-device-for-development.md).

2.  Baixe e instale a versão gratuita [Android Wear](https://play.google.com/store/apps/details?id=com.google.android.wearable.app) aplicativo a partir do Google Play Store.

### <a name="connect-the-device"></a>Conecte o dispositivo

Use as etapas a seguir para conectar seu dispositivo Wear para seu telefone:

1.  No telefone que atuam como intermediários de Bluetooth (configurado acima), inicie o aplicativo Android Wear. 

2.  Toque o **configurações** ícone.

3.  Habilitar **depuração via Bluetooth**. Você deve ver o status exibido na tela do aplicativo Android Wear a seguir:

        Host: disconnected
        Target: connected

4.  Conecte-se o telefone em seu computador por USB. Em seu computador, digite os seguintes comandos:

    ```shell
    adb forward tcp:4444 localabstract:/adb-hub
    adb connect 127.0.0.1:4444
    ```

    Se a porta 4444 não estiver disponível, você pode usar qualquer outra porta disponível para o qual você tem acesso. 

    **Observação**: Se você reiniciar o Visual Studio ou o Visual Studio para Mac, você deve executar esses comandos novamente para configurar uma conexão para o dispositivo Wear.

5.  Quando o dispositivo de desgaste solicita, confirme que você está permitindo **ADB depuração**. No aplicativo do Android Wear, você verá o status mudar para:

        Host: connected
        Target: connected

6.  Depois de concluir as etapas acima, executando `adb devices` mostra o status de telefone e o dispositivo Android Wear:

        List of devices attached
        127.0.0.1:4444    device
        019ad61df0a69399  device

Neste ponto, você pode implantar seu aplicativo para o dispositivo Wear.

<a name="screenshots" />

### <a name="taking-screenshots"></a>Fazer capturas de tela

Você pode tirar uma captura de tela do dispositivo Wear digitando o seguinte comando: 

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

Você pode desinstalar um aplicativo do dispositivo wear digitando o seguinte comando:

```shell
adb -s 127.0.0.1:4444 uninstall <package name>
```

Por exemplo, para remover o aplicativo com o nome do pacote `com.xamarin.weartest`, digite o seguinte comando:

```shell
adb -s 127.0.0.1:4444 uninstall com.xamarin.weartest
```

Para obter mais informações sobre como depurar o Android Wear dispositivos via Bluetooth, consulte [depuração via Bluetooth](https://developer.android.com/training/wearables/apps/bt-debugging.html).


## <a name="debugging-a-wear-app-with-a-companion-phone-app"></a>Depurar um aplicativo de desgaste com um aplicativo de telefone complementar

Aplicativos do Android Wear são empacotados com um aplicativo de telefone Android complementar para distribuição no Google Play (para obter mais informações, consulte [trabalhando com o empacotamento](~/android/wear/deploy-test/packaging.md)). No entanto, você ainda desenvolver o aplicativo de desgaste e seu aplicativo complementar separadamente. Quando você liberar seu aplicativo por meio do Google Play Store, o aplicativo de desgaste será empacotado com um aplicativo complementar e instalado automaticamente se possível.

Para depurar o aplicativo de desgaste com um aplicativo complementar: 

1.  Crie e implante o aplicativo complementar para o telefone.

2.  Clique com botão direito no projeto de desgaste e defini-lo como o projeto de início padrão.

3.  Implante o projeto de desgaste do dispositivo portátil.

4.  Executar e depurar o aplicativo de desgaste do dispositivo.

 
## <a name="summary"></a>Resumo

Este artigo explicou como configurar um dispositivo Android Wear para depuração de desgaste do Visual Studio via Bluetooth e como depurar um aplicativo de desgaste com um aplicativo de telefone complementar. Também forneceu as dicas de depuração comuns para depurar um aplicativo de desgaste via Bluetooth.
