---
title: Configurar o dispositivo para desenvolvimento
description: Esse artigo discutirá como configurar um dispositivo Android e conectá-lo a um computador de modo que ele possa ser usado para executar e depurar aplicativos Xamarin.Android.
ms.prod: xamarin
ms.assetid: 9116A3AA-EA00-56AF-AE70-BAEEC045EF11
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 06/22/2018
ms.openlocfilehash: 72e0a2adc79796b3df7b6fb4eca62448f1a1a7a4
ms.sourcegitcommit: 997f7b6a1a1bc50b98c3ca5bbc75d6875ba2ae9a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/18/2020
ms.locfileid: "79510725"
---
# <a name="set-up-device-for-development"></a>Configurar o dispositivo para desenvolvimento

_Este artigo explica como configurar um dispositivo Android e conectá-lo a um computador, de modo que o dispositivo possa ser usado para executar e depurar aplicativos Xamarin.Android._

Após o teste no Android Emulator, o ideal é ver e testar os aplicativos em execução em um dispositivo Android. Será necessário habilitar a depuração e conectar o dispositivo ao computador.

Cada uma dessas etapas será abordada de modo detalhado nas seções a seguir.

## <a name="enable-debugging-on-the-device"></a>Habilitar a depuração remota no dispositivo

Um dispositivo deve ser habilitado para depuração a fim de testar um aplicativo Android. As opções de desenvolvedor no Android foram ocultadas por padrão desde a versão 4,2, e habilitá-las pode variar com base na versão do Android.

### <a name="android-90"></a>Android 9.0 +

Para Android 9,0 e superior, a depuração é habilitada seguindo estas etapas:

1. Acesse a tela **Configurações**.
2. Selecione **sobre o telefone** .
3. Toque em **número de Build** 7 vezes até **que você agora seja um desenvolvedor!** está visível.

### <a name="android-80-and-android-81"></a>Android 8,0 e Android 8,1

1. Vá para a tela **configurações** .
2. Selecione **sistema**.
3. Selecione **sobre o telefone**
4. Toque em **número de Build** 7 vezes até **que você agora seja um desenvolvedor!** está visível.

### <a name="android-71-and-lower"></a>Android 7,1 e inferior

1. Vá para a tela **configurações** .
2. Selecione **sobre o telefone**.
3. Toque em **número de Build** 7 vezes até **que você agora seja um desenvolvedor!** está visível.

[tela de opções de desenvolvedor de ![no Android 9,0](set-up-device-for-development-images/build-version-sml.png)](set-up-device-for-development-images/build-version.png#lightbox)

### <a name="verify-that-usb-debugging-is-enabled"></a>Verificar se a depuração de USB está habilitada

Depois de habilitar o modo de desenvolvedor em seu dispositivo, você deve garantir que a depuração de USB esteja habilitada no dispositivo. Isso também varia de acordo com a versão do Android.

### <a name="android-90"></a>Android 9.0 +

Navegue até **configurações > sistema > opções avançadas de desenvolvedor de >** e habilite a **depuração de USB**.

### <a name="android-80-and-android-81"></a>Android 8,0 e Android 8,1

Navegue até **configurações > sistema > opções do desenvolvedor** e habilite a **depuração de USB**.

### <a name="android-71-and-lower"></a>Android 7,1 e inferior

Navegue até **configurações > opções do desenvolvedor** e habilite a **depuração de USB**.

Uma vez que a guia **Opções do desenvolvedor** estiver disponível em **Configurações > Sistema**, abra-a para revelar as configurações do desenvolvedor:

[tela de opções de desenvolvedor de ![no Android 9,0](set-up-device-for-development-images/usb-debugging-sml.png)](set-up-device-for-development-images/usb-debugging.png#lightbox)

Esse é o local para habilitar as opções do desenvolvedor, como a depuração USB e o modo permanecer ativo.

## <a name="connect-the-device-to-the-computer"></a>Conectar o dispositivo ao computador

A etapa final é conectar o dispositivo ao computador. A maneira mais fácil e confiável é fazer isso em vez de USB.

Você receberá uma solicitação para confiar no computador em seu dispositivo, caso não o tenha usado para depuração antes. Você também pode marcar **sempre permitir neste computador** para evitar a necessidade desse prompt sempre que conectar o dispositivo.

![](set-up-device-for-development-images/trust-computer-for-usb-debugging.png "Google USB")

## <a name="alternate-connection-via-wifi"></a>Conexão alternativa via WiFi

É possível conectar um dispositivo Android a um computador sem usar um cabo USB, em Wi-Fi. Essa técnica requer mais esforço, mas pode ser útil quando o dispositivo está muito longe do computador para permanecer conectado constantemente via cabo. 

### <a name="connecting-over-wifi"></a>Conectando via WiFi

Por padrão, o [Android Debug Bridge](https://developer.android.com/tools/help/adb.html) (*ADB*) é configurado para se comunicar com um dispositivo Android via USB. É possível reconfigurá-lo para usar TCP/IP em vez de USB. Para fazer isso, o dispositivo e o computador devem estar na mesma rede WiFi. Para configurar seu ambiente para depurar em WiFi, conclua as seguintes etapas na linha de comando:

1. Determine o endereço IP do seu dispositivo Android. Uma maneira de descobrir o endereço IP é examinar **as configurações > rede & internet > Wi-Fi**, em seguida, toque na rede WiFi à qual o dispositivo está conectado e, em seguida, toque em **avançado**. Isso abrirá uma lista suspensa mostrando informações sobre a conexão de rede, semelhante ao que é visto na captura de tela abaixo:

    [Endereço IP ![](set-up-device-for-development-images/ip-settings-sml.png)](set-up-device-for-development-images/ip-settings.png#lightbox)

    Em algumas versões do Android, o endereço IP não estará listado nessa opção, mas pode ser encontrado em **Configurações > Sobre o telefone > Status**.

2. Conecte seu dispositivo Android ao computador via USB.

3. Em seguida, reinicie o ADB assim que ele estiver usando o TCP na porta 5555. De um prompt de comando, digite o seguinte comando:

    ```command
    adb tcpip 5555
    ```

    Depois que esse comando for emitido, seu computador não poderá detectar dispositivos que estão conectados via USB.

4. Desconecte o cabo USB que está conectando o seu dispositivo ao computador.

5. Configure o ADB para que ele se conecte ao seu dispositivo Android na porta especificada na etapa 1 acima:

    ```command
    adb connect 192.168.1.28:5555
    ```

    Depois que esse comando for concluído, o dispositivo Android será conectado ao computador via WiFi.

    Quando você terminar a depuração via WiFi, será possível redefinir o ADB de volta para o modo USB com o seguinte comando:
    
    ```command
    adb usb
    ```
    
    É possível solicitar o ADB para listar os dispositivos que estão conectados ao computador. Independentemente de como os dispositivos estejam conectados, você pode emitir o seguinte comando no prompt de comando para verificar o que está conectado:
    
    ```command
    adb devices
    ```

## <a name="troubleshooting"></a>Solução de problemas

Em alguns casos, você pode achar que seu dispositivo não pode se conectar ao computador. Nesse caso, talvez você queira verificar se os drivers USB estão instalados.

## <a name="install-usb-drivers"></a>Instalar Drivers USB

Esta etapa não é necessária para macOS; Basta conectar o dispositivo ao Mac com um cabo USB.

Pode ser necessário instalar alguns drivers adicionais antes que um computador Windows reconheça um dispositivo Android conectado por USB.

> [!NOTE]
> Essas são as etapas para configurar um dispositivo Google Nexus e são fornecidas como uma referência. As etapas para o seu dispositivo específico podem variar, mas seguem um padrão semelhante. Se você tiver problemas, pesquise sobre seu dispositivo na Internet.

Execute o aplicativo **android.bat** no diretório **[caminho de instalação do SDK do Android] \ferramentas**. Por padrão, o instalador do Xamarin.Android colocará o SDK do Android no local a seguir em um computador Windows:

`C:\Users\[username]\AppData\Local\Android\android-sdk`

### <a name="download-the-usb-drivers"></a>Baixar os Drivers USB

Os dispositivos Google Nexus (exceto o Galaxy Nexus) exigem o Driver USB do Google. O driver para o Galaxy Nexus é [distribuído pela Samsung](https://www.samsung.com/us/support/downloads/).
Todos os outros dispositivos Android devem usar o [driver USB do seu respectivo fabricante](https://developer.android.com/tools/extras/oem-usb.html#Drivers).

Instale o pacote do **Driver USB do Google** iniciando o Gerenciador de SDK do Android e expandindo a pasta **Extras**, como pode ser visto na captura de tela a seguir:

![](set-up-device-for-development-images/google-usb-driver.png "Google USB driver selected")

Marque a caixa **driver USB do Google** e clique no botão **aplicar alterações** .
Os arquivos de driver são baixados no seguinte local:

`[Android SDK install path]\extras\google\usb\_driver`

O caminho padrão para uma instalação do Xamarin.Android é:

`C:\Users\[username]\AppData\Local\Android\android-sdk\extras\google\usb_driver`

### <a name="installing-the-usb-driver"></a>Instalando o Driver USB

Depois que os drivers USB forem baixados, é necessário instalá-los.
Para instalar os drivers no Windows 7:

1. Conecte seu dispositivo ao computador com um cabo USB.

2. Clique com o botão direito do mouse em Computador na área de trabalho ou no Windows Explorer e selecione **Gerenciar**.

3. Selecione **Dispositivos** no painel esquerdo.

4. Localize e expanda **Outros dispositivos** no painel à direita.

5. Clique com o botão direito do mouse no nome do dispositivo e selecione **Atualizar Software de Driver**.
    Isso inicializará o Assistente para Atualização de Hardware.

6. Selecione **Procurar o software de driver no computador** e clique em **Avançar**.

7. Clique em **Procurar** e localize a pasta do driver USB (o driver USB do Google está localizado em **[caminho de instalação do SDK do Android ]\extras\google\usb_driver**).

8. Clique em **Avançar** para instalar o driver.

## <a name="summary"></a>Resumo

Esse artigo discutiu como configurar um dispositivo Android para desenvolvimento ao habilitar a depuração no dispositivo. Ele também abordou como conectar o dispositivo a um computador usando USB ou WiFi.

## <a name="related-links"></a>Links Relacionados

- [Android Debug Bridge](https://developer.android.com/tools/help/adb.html)
- [Usando Dispositivos de Hardware](https://developer.android.com/tools/device.html)
- [Downloads de Drivers Samsung](https://www.samsung.com/us/support/downloads/)
- [Drivers USB OEM](https://developer.android.com/tools/extras/oem-usb.html#Drivers)
- [Driver USB do Google](https://developer.android.com/sdk/win-usb.html)
- [Desenvolvedores XDA: Windows 8 – problema do ADB/driver de inicialização rápida resolvido](https://forum.xda-developers.com/showthread.php?t=1583801)
