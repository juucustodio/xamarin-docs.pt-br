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
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "79510725"
---
# <a name="set-up-device-for-development"></a>Configurar o dispositivo para desenvolvimento

_Este artigo explica como configurar um dispositivo Android e conectá-lo a um computador, de modo que o dispositivo possa ser usado para executar e depurar aplicativos Xamarin.Android._

Após o teste no Android Emulator, o ideal é ver e testar os aplicativos em execução em um dispositivo Android. Você precisará ativar a depuração e conectar o dispositivo ao computador.

Cada uma dessas etapas será abordada de modo detalhado nas seções a seguir.

## <a name="enable-debugging-on-the-device"></a>Habilitar a depuração remota no dispositivo

Um dispositivo deve ser habilitado para depuração para testar um aplicativo Android. As opções de desenvolvedor no Android foram ocultadas por padrão desde a versão 4.2, e a sua habilitação pode variar de acordo com a versão do Android.

### <a name="android-90"></a>Android 9.0+

Para android 9.0 ou superior, a depuração é ativada seguindo estas etapas:

1. Acesse a tela **Configurações**.
2. Selecione **Sobre o telefone** .
3. Toque **em Build Number** 7 vezes até que você seja agora um **desenvolvedor!** é visível.

### <a name="android-80-and-android-81"></a>Android 8.0 e Android 8.1

1. Vá para a tela **Configurações.**
2. Selecionar **sistema**.
3. Selecione **Sobre o telefone**
4. Toque **em Build Number** 7 vezes até que você seja agora um **desenvolvedor!** é visível.

### <a name="android-71-and-lower"></a>Android 7.1 e inferior

1. Vá para a tela **Configurações.**
2. Selecione **Sobre o telefone**.
3. Toque **em Build Number** 7 vezes até que você seja agora um **desenvolvedor!** é visível.

[![Tela de opções de desenvolvedor esnomila no Android 9.0](set-up-device-for-development-images/build-version-sml.png)](set-up-device-for-development-images/build-version.png#lightbox)

### <a name="verify-that-usb-debugging-is-enabled"></a>Verifique se a depuração USB está ativada

Depois de ativar o modo desenvolvedor no seu dispositivo, você deve garantir que a depuração USB esteja ativada no dispositivo. Isso também varia de acordo com a versão para Android.

### <a name="android-90"></a>Android 9.0+

Navegue até **configurações > sistema > opções avançadas de desenvolvedor de >** e habilite a **depuração USB**.

### <a name="android-80-and-android-81"></a>Android 8.0 e Android 8.1

Navegue até **Configurações > Opções de Desenvolvedor > do Sistema** e habilite a **depuração USB**.

### <a name="android-71-and-lower"></a>Android 7.1 e inferior

Navegue até **Configurações > Opções do Desenvolvedor** e habilite **a depuração USB**.

Uma vez que a guia **Opções do desenvolvedor** estiver disponível em **Configurações > Sistema**, abra-a para revelar as configurações do desenvolvedor:

[![Tela de opções de desenvolvedor esnomila no Android 9.0](set-up-device-for-development-images/usb-debugging-sml.png)](set-up-device-for-development-images/usb-debugging.png#lightbox)

Esse é o local para habilitar as opções do desenvolvedor, como a depuração USB e o modo permanecer ativo.

## <a name="connect-the-device-to-the-computer"></a>Conecte o dispositivo ao computador

A etapa final é conectar o dispositivo ao computador. A maneira mais fácil e confiável é fazê-lo por USB.

Você receberá um aviso para confiar no computador em seu dispositivo se você não o tiver usado para depuração antes. Você também pode verificar **Sempre permitir que este computador** evite exigir este prompt cada vez que você conectar o dispositivo.

![](set-up-device-for-development-images/trust-computer-for-usb-debugging.png "Google USB")

## <a name="alternate-connection-via-wifi"></a>Conexão alternativa via Wi-Fi

É possível conectar um dispositivo Android a um computador sem usar um cabo USB, via WiFi. Esta técnica requer mais esforço, mas pode ser útil quando o dispositivo está muito longe do computador para permanecer constantemente conectado via cabo. 

### <a name="connecting-over-wifi"></a>Conectando via WiFi

Por padrão, o [Android Debug Bridge](https://developer.android.com/tools/help/adb.html) (*ADB*) é configurado para se comunicar com um dispositivo Android via USB. É possível reconfigurá-lo para usar TCP/IP em vez de USB. Para fazer isso, o dispositivo e o computador devem estar na mesma rede WiFi. Para configurar seu ambiente para depurar via WiFi, complete as seguintes etapas da linha de comando:

1. Determine o endereço IP do seu dispositivo Android. Uma maneira de descobrir o endereço IP é procurar em **Configurações > Rede & internet > Wi-Fi,** em seguida, toque na rede WiFi à qual o dispositivo está conectado e, em seguida, toque em **Avançado**. Isso abrirá um dropdown mostrando informações sobre a conexão de rede, semelhante ao que é visto na captura de tela abaixo:

    [![Endereço IP](set-up-device-for-development-images/ip-settings-sml.png)](set-up-device-for-development-images/ip-settings.png#lightbox)

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

    Uma vez que este comando termina o dispositivo Android é conectado ao computador via WiFi.

    Quando você terminar a depuração via WiFi, é possível redefinir o ADB de volta ao modo USB com o seguinte comando:
    
    ```command
    adb usb
    ```
    
    É possível solicitar a ADB para listar os dispositivos conectados ao computador. Independentemente de como os dispositivos estejam conectados, você pode emitir o seguinte comando no prompt de comando para verificar o que está conectado:
    
    ```command
    adb devices
    ```

## <a name="troubleshooting"></a>Solução de problemas

Em alguns casos, você pode descobrir que seu dispositivo não pode se conectar ao computador. Neste caso, você pode querer verificar se os drivers USB estão instalados.

## <a name="install-usb-drivers"></a>Instalar Drivers USB

Esta etapa não é necessária para o macOS; basta conectar o dispositivo ao Mac com um cabo USB.

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

Verifique a caixa **do Driver USB** do Google e clique no botão Aplicar **alterações.**
Os arquivos de driver são baixados no seguinte local:

`[Android SDK install path]\extras\google\usb\_driver`

O caminho padrão para uma instalação do Xamarin.Android é:

`C:\Users\[username]\AppData\Local\Android\android-sdk\extras\google\usb_driver`

### <a name="installing-the-usb-driver"></a>Instalando o Driver USB

Depois que os drivers USB forem baixados, é necessário instalá-los.
Para instalar os drivers no Windows 7:

1. Conecte seu dispositivo ao computador com um cabo USB.

2. Clique com o botão direito do mouse no computador da sua área de trabalho ou do Windows Explorer e selecione **Gerenciar** .

3. Selecione **Dispositivos** no painel à esquerda.

4. Localize e expanda **Outros dispositivos** no painel à direita.

5. Clique com o botão direito do mouse no nome do dispositivo e selecione **Atualizar Software de Driver**.
    Isso inicializará o Assistente para Atualização de Hardware.

6. Selecione **Procurar meu computador para obter software de driver** e clique em **Next** .

7. Clique **em Procurar** e localize a pasta do driver USB (o driver USB do Google está localizado no caminho de instalação do Android **SDK]\extras\google\usb_driver**).

8. Clique em **Avançar** para instalar o driver.

## <a name="summary"></a>Resumo

Esse artigo discutiu como configurar um dispositivo Android para desenvolvimento ao habilitar a depuração no dispositivo. Ele também abordou como conectar o dispositivo a um computador usando USB ou WiFi.

## <a name="related-links"></a>Links relacionados

- [Android Debug Bridge](https://developer.android.com/tools/help/adb.html)
- [Usando Dispositivos de Hardware](https://developer.android.com/tools/device.html)
- [Downloads de Drivers Samsung](https://www.samsung.com/us/support/downloads/)
- [Drivers USB OEM](https://developer.android.com/tools/extras/oem-usb.html#Drivers)
- [Driver USB do Google](https://developer.android.com/sdk/win-usb.html)
- [Desenvolvedores XDA: Windows 8 – problema do ADB/driver de inicialização rápida resolvido](https://forum.xda-developers.com/showthread.php?t=1583801)
