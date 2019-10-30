---
title: Configurar o dispositivo para desenvolvimento
description: Esse artigo discutirá como configurar um dispositivo Android e conectá-lo a um computador de modo que ele possa ser usado para executar e depurar aplicativos Xamarin.Android.
ms.prod: xamarin
ms.assetid: 9116A3AA-EA00-56AF-AE70-BAEEC045EF11
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 06/22/2018
ms.openlocfilehash: f5b92bc202cbf9765a63219ad653442152c3eb24
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73020711"
---
# <a name="set-up-device-for-development"></a>Configurar o dispositivo para desenvolvimento

_Este artigo explica como configurar um dispositivo Android e conectá-lo a um computador, de modo que o dispositivo possa ser usado para executar e depurar aplicativos Xamarin.Android._

Após o teste no Android Emulator, o ideal é ver e testar os aplicativos em execução em um dispositivo Android. Aqui estão as etapas incluídas na conexão de um dispositivo em um computador para depuração:

1. **Habilitar a depuração no dispositivo** – por padrão, não será possível depurar aplicativos em um dispositivo Android.

2. **Instalar drivers USB** – essa etapa não é necessária para computadores macOS. Pode ser necessário instalar drivers USB em computadores com Windows.

3. **Conectar o dispositivo ao computador** – a etapa final envolve a conexão do dispositivo com o computador por USB ou Wi-Fi.

Cada uma dessas etapas será abordada de modo detalhado nas seções a seguir.

## <a name="enable-debugging-on-the-device"></a>Habilitar a depuração remota no dispositivo

É possível usar qualquer dispositivo Android para testar um aplicativo Android. No entanto, o dispositivo deve ser configurado corretamente antes que a depuração seja realizada. As etapas abrangidas são um pouco diferentes, dependendo da versão do Android em execução no dispositivo.

### <a name="android-40-to-android-41"></a>Android 4.0 para Android 4.1

Do Android 4.0.x para o Android 4.1.x, a depuração é habilitada seguindo essas etapas:

1. Acesse a tela **Configurações**.
2. Selecione **Opções do desenvolvedor**.
3. Marque a opção **Depuração USB**.

Essa captura de tela mostra a tela **Opções do desenvolvedor** em um dispositivo que está executando o Android 4.0.3:

[![Opções do desenvolvedor](set-up-device-for-development-images/developer-options-sml.png)](set-up-device-for-development-images/developer-options.png#lightbox)

### <a name="android-42-and-higher"></a>Android 4.2 e posterior

No Android 4.2 e nas versões posteriores, as **Opções do desenvolvedor** estão ocultas por padrão. Para torná-las disponíveis, acesse **Configurações > Sobre o telefone** e toque no item **Número de build** sete vezes para revelar a guia **Opções do desenvolvedor**:

[![Item de número de build](set-up-device-for-development-images/about-phone-sml.png)](set-up-device-for-development-images/about-phone.png#lightbox)

Uma vez que a guia **Opções do desenvolvedor** estiver disponível em **Configurações > Sistema**, abra-a para revelar as configurações do desenvolvedor:

[![Tela de configurações do desenvolvedor](set-up-device-for-development-images/developer3.png)](set-up-device-for-development-images/developer3.png#lightbox)

Esse é o local para habilitar as opções do desenvolvedor, como a depuração USB e o modo permanecer ativo.

## <a name="install-usb-drivers"></a>Instalar Drivers USB

Essa etapa não é necessária para o macOS. Basta conectar o dispositivo ao Mac com um cabo USB.

Pode ser necessário instalar alguns drivers adicionais antes que um computador Windows reconheça um dispositivo Android conectado por USB.

> [!NOTE]
> Essas são as etapas para configurar um dispositivo Google Nexus e são fornecidas como uma referência. As etapas para o seu dispositivo específico podem variar, mas seguem um padrão semelhante. Se você tiver problemas, pesquise sobre seu dispositivo na Internet.

Execute o aplicativo **android.bat** no diretório **[caminho de instalação do SDK do Android] \ferramentas**. Por padrão, o instalador do Xamarin.Android colocará o SDK do Android no local a seguir em um computador Windows:

`C:\Users\[username]\AppData\Local\Android\android-sdk`

### <a name="download-the-usb-drivers"></a>Baixar os Drivers USB

Os dispositivos Google Nexus (exceto o Galaxy Nexus) exigem o Driver USB do Google. O driver para o Galaxy Nexus é [distribuído pela Samsung](https://www.samsung.com/us/support/downloads/).
Todos os outros dispositivos Android devem usar o [driver USB do seu respectivo fabricante](https://developer.android.com/tools/extras/oem-usb.html#Drivers).

Instale o pacote do **Driver USB do Google** iniciando o Gerenciador de SDK do Android e expandindo a pasta **Extras**, como pode ser visto na captura de tela a seguir:

[![Pacote do Driver USB do Google selecionado](set-up-device-for-development-images/usbdriverpackage.png)](set-up-device-for-development-images/usbdriverpackage.png#lightbox)

Marque a caixa **Driver USB do Google** e clique no botão **Instalar**.
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

### <a name="installing-unverified-drivers-in-windows-8"></a>Instalando drivers não verificados no Windows 8

Etapas adicionais podem ser necessárias para instalar um driver não verificado no Windows 8. As etapas a seguir descrevem como instalar os drivers em um Galaxy Nexus:

1. **Acessar as Opções de Inicialização Avançadas do Windows 8** – Essa etapa abrange a reinicialização do computador para acessar as Opções de Inicialização Avançadas. Inicie um prompt de linha de comando e reinicie o computador usando o seguinte comando:

    ```command
    shutdown.exe /r /o
    ```

2. **Conectar o dispositivo** – Conecte o dispositivo ao computador

3. **Iniciar o Device Manager** – execute **devmgmt.msc**; você deverá ver seu dispositivo listado com um triângulo amarelo sobre ele.

4. **Instalar os drivers de dispositivo** – instale os drivers de dispositivo, conforme descrito acima.

## <a name="connect-the-device-to-the-computer"></a>Conectar o dispositivo ao computador

A etapa final é conectar o dispositivo ao computador. Há duas formas de fazer isso:

- **Cabo USB** – Essa é a maneira mais fácil e mais comum. Basta conectar o cabo USB no dispositivo e, em seguida, no computador.

- **WiFi** – É possível conectar um dispositivo Android a um computador sem usar um cabo USB, via WiFi. Essa técnica exige um pouco mais de esforço, mas pode ser útil quando o dispositivo está muito longe do computador para permanecer constantemente conectado por cabo. A conexão via WiFi será abordada na próxima seção.

### <a name="connecting-over-wifi"></a>Conectando via WiFi

Por padrão, o [Android Debug Bridge](https://developer.android.com/tools/help/adb.html) (*ADB*) é configurado para se comunicar com um dispositivo Android via USB. É possível reconfigurá-lo para usar TCP/IP em vez de USB. Para fazer isso, o dispositivo e o computador devem estar na mesma rede WiFi. Para configurar o ambiente para depurar via Wi-Fi, execute estas etapas da linha de comando:

1. Determine o endereço IP do seu dispositivo Android. Uma maneira de descobrir o endereço IP é procurar em **Configurações > WiFi** e, em seguida, tocar na rede WiFi na qual o dispositivo está conectado. Isso abrirá uma tela de configurações que mostra as informações sobre a conexão de rede, de modo semelhante ao que é visto na captura de tela abaixo:

    ![Endereço IP](set-up-device-for-development-images/ip-settings.png)

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

    Após a conclusão desse comando, o dispositivo Android será conectado ao computador via WiFi.

Ao terminar a depuração via WiFi, você poderá redefinir o ADB de volta ao modo USB com o seguinte comando:

```command
adb usb
```

É possível solicitar que o ADB liste os dispositivos que estão conectados ao computador. Independentemente de como os dispositivos estejam conectados, você pode emitir o seguinte comando no prompt de comando para verificar o que está conectado:

```command
adb devices
```

## <a name="summary"></a>Resumo

Esse artigo discutiu como configurar um dispositivo Android para desenvolvimento ao habilitar a depuração no dispositivo. Ele também abordou como conectar o dispositivo a um computador usando USB ou WiFi.

## <a name="related-links"></a>Links relacionados

- [Android Debug Bridge](https://developer.android.com/tools/help/adb.html)
- [Usando Dispositivos de Hardware](https://developer.android.com/tools/device.html)
- [Downloads de Drivers Samsung](https://www.samsung.com/us/support/downloads/)
- [Drivers USB OEM](https://developer.android.com/tools/extras/oem-usb.html#Drivers)
- [Driver USB do Google](https://developer.android.com/sdk/win-usb.html)
- [Desenvolvedores XDA: Windows 8 – problema do ADB/driver de inicialização rápida resolvido](https://forum.xda-developers.com/showthread.php?t=1583801)
