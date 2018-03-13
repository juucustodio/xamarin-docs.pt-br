---
title: "Solução de Problemas do Emulador do SDK do Android"
description: Como identificar e resolver problemas do Emulador do SDK do Android
ms.topic: article
ms.prod: xamarin
ms.assetid: 4B05C3C5-E1F6-47A9-B098-C31E630194F6
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: 486df3bbee3f8af511140e2d287f9f95571c7b3d
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2018
---
# <a name="android-sdk-emulator-troubleshooting"></a>Solução de Problemas do Emulador do SDK do Android

Neste artigo, as mensagens de aviso e os problemas (e as respectivas soluções) mais comuns no Emulador do SDK do Android serão explicados.
 
<a name="perfwarn" />

## <a name="performance-warnings"></a>Avisos de desempenho

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

A partir do Visual Studio 2017 versão 15.4, uma caixa de diálogo de aviso de desempenho será exibida quando você implantar o aplicativo no Emulador do SDK do Android. Essas caixas de diálogo de aviso estão explicadas abaixo.

### <a name="computer-does-not-contain-an-intel-procesor"></a>O computador Não Contém um Processador Intel

![O computador não contém um processador Intel](troubleshooting-images/01-no-intel-processor.png)

Esta caixa de diálogo será exibida se seu computador não tiver um processador Intel, que é necessário para a aceleração do Emulador do SDK do Android. Se seu computador não tem um processador Intel, recomenda-se usar um dispositivo Android físico para desenvolvimento.

### <a name="hyper-v-is-installed-or-active"></a>O Hyper-V está Instalado ou Ativo

![O Hyper-V está instalado ou ativo](troubleshooting-images/02-hyper-v-active.png)

Esta caixa de diálogo será exibida quando o Hyper-V estiver instalado ou ativo, e deverá ser desabilitada. A seção [Desabilitar o Hyper-V](~/android/get-started/installation/android-emulator/hardware-acceleration.md#disable-hyperv) explica como resolver esse problema. 

### <a name="haxm-is-not-installed"></a>O HAXM não Está Instalado

![O HAXM não está instalado](troubleshooting-images/03-haxm-not-installed.png)

Essa caixa de diálogo indica que seu computador tem um processador Intel, que o Hyper-V está desabilitado, mas o HAXM não está instalado.
A seção [Instalando o HAXM](~/android/get-started/installation/android-emulator/hardware-acceleration.md#install-haxm) descreve as etapas de instalação do HAXM.

### <a name="haxm-process-not-running"></a>O Processo HAXM Não Está Em Execução

![O processo HAXM não está em execução](troubleshooting-images/04-haxm-process-not-running.png)

Esta caixa de diálogo será exibida se seu computador tiver um processador Intel, o Hyper-V estiver desabilitado, o Intel HAXM estiver instalado, mas o processo HAXM não estiver em execução. Para resolver este problema, abra uma janela do prompt de comando e digite o seguinte comando:

```cmd
sc query intelhaxm
```

Se o processo HAXM estiver em execução, você verá um resultado semelhante ao seguinte:


```cmd
SERVICE_NAME: intelhaxm
    TYPE               : 1  KERNEL_DRIVER
    STATE              : 4  RUNNING
                            (STOPPABLE, NOT_PAUSABLE, IGNORES_SHUTDOWN)
    WIN32_EXIT_CODE    : 0  (0x0)
    SERVICE_EXIT_CODE  : 0  (0x0)
    CHECKPOINT         : 0x0
    WAIT_HINT          : 0x0
```


Se o **ESTADO** não estiver definido como **EM EXECUÇÃO**, consulte [How to Use the Intel Hardware Accelerated Execution Manager](https://software.intel.com/en-us/android/articles/how-to-use-the-intel-hardware-accelerated-execution-manager-intel-haxm-android-emulator) (Como usar o Hardware Accelerated Execution Manager da Intel) para resolver o problema.


### <a name="other-failures"></a>Outras Falhas

![Outras falhas](troubleshooting-images/05-other-failure.png)

Esta caixa de diálogo será exibida se o computador tiver um processador Intel, o Hyper-V estiver desabilitado, o Intel HAXM estiver instalado, o processo HAXM estiver em execução, mas o emulador não tiver sido iniciado por algum motivo desconhecido.
Para ajudar a resolver este erro, consulte [How to Use the Intel Hardware Accelerated Execution Manager](https://software.intel.com/en-us/android/articles/how-to-use-the-intel-hardware-accelerated-execution-manager-intel-haxm-android-emulator) (Como usar o Hardware Accelerated Execution Manager da Intel).

### <a name="disabling-performance-warnings"></a>Desabilitar avisos de desempenho

Desabilite os avisos de desempenho se preferir não os ver. No Visual Studio, clique em **Ferramentas > Opções > Xamarin > Configurações do Android** e desabilite a opção **Avisar se não houver suporte para a aceleração de AVD (HAXM)**:

[![Desabilitando avisos de aceleração de AVD](troubleshooting-images/win/06-disable-perf-warnings-sml.png)](troubleshooting-images/win/06-disable-perf-warnings.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

A partir do Visual Studio para Mac build 7.2 (build 559) ou posterior, uma caixa de diálogo de aviso de desempenho será exibida quando você implantar o aplicativo no Emulador do SDK do Android pela primeira vez. Essas caixas de diálogo de aviso estão explicadas abaixo.

### <a name="haxm-is-not-installed"></a>O HAXM não Está Instalado

![O HAXM não está instalado](troubleshooting-images/03-haxm-not-installed.png)

Esta caixa de diálogo indica que o HAXM não está instalado.
A seção [Instalando o HAXM](~/android/get-started/installation/android-emulator/hardware-acceleration.md#install-haxm) descreve as etapas de instalação do HAXM.

### <a name="haxm-process-not-running"></a>O Processo HAXM Não Está Em Execução

![O processo HAXM não está em execução](troubleshooting-images/04-haxm-process-not-running.png)

Esta caixa de diálogo será exibida se o processo HAXM não estiver em execução. Para obter informações detalhadas para ajudá-lo a resolver esse problema, consulte [How to Use the Intel Hardware Accelerated Execution Manager](https://software.intel.com/en-us/android/articles/how-to-use-the-intel-hardware-accelerated-execution-manager-intel-haxm-android-emulator) (Como usar o Hardware Accelerated Execution Manager da Intel).

### <a name="other-failures"></a>Outras Falhas

![Outras falhas](troubleshooting-images/05-other-failure.png)

Esta caixa de diálogo será exibida se o emulador não for iniciado por algum motivo desconhecido. Para ajudar a resolver esse erro, consulte [How to Use the Intel Hardware Accelerated Execution Manager](https://software.intel.com/en-us/android/articles/how-to-use-the-intel-hardware-accelerated-execution-manager-intel-haxm-android-emulator) (Como usar o Hardware Accelerated Execution Manager da Intel) para resolver o problema.

-----


## <a name="solutions-to-common-problems"></a>Soluções para Problemas Comuns

Muitos problemas comuns no Emulador do SDK do Android podem ser resolvidos ao fazer alterações de configuração em seu computador ou ao instalar um software adicional. As seções a seguir descrevem esses problemas e fornecem soluções.


### <a name="deployment-issues"></a>Problemas de implantação

Se receber um erro sobre uma falha ao instalar o APK no emulador ou sobre uma falha ao executar o Android Debug Bridge (**adb**), verifique se o SDK do Android pode se conectar ao emulador. Para fazer isso, siga estas etapas:

1. Inicie o emulador no **Gerenciador de dispositivos virtuais Android (AVD)** (selecione seu dispositivo virtual e clique em **Iniciar**).

2. Abra um prompt de comando e vá para a pasta em que o **adb** está instalado. Por exemplo, no Windows, ele pode estar em: **C:\\Arquivos de Programas (x86)\\Android\\android-sdk\\platform-tools\\adb.exe**.

3. Digite o seguinte comando:

   ```shell
   adb devices
   ```

4. Se o emulador puder ser acessado pelo SDK do Android, ele deverá aparecer na lista de dispositivos anexados. Por exemplo:

   ```shell
   List of devices attached
   emulator-5554   device
   ```

5. Se o emulador não aparecer nessa lista, inicie o **Gerenciador de SDK do Android**, aplique todas as atualizações e tente iniciar o emulador novamente.



### <a name="haxm-issues"></a>Problemas do HAXM

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Se o Emulador do SDK do Android não for iniciado corretamente, isso costuma ser causado por problemas com o HAXM. Os problemas com o HAXM normalmente são causados por conflitos com outras tecnologias de virtualização, configurações incorretas ou um driver de HAXM desatualizado.

<a name="virt-conflicts" />

#### <a name="haxm-virtualization-conflicts"></a>Conflitos de virtualização do HAXM

O HAXM pode entrar em conflito com outras tecnologias que usam virtualização, como o Hyper-V, o Windows Device Guard e alguns softwares antivírus:

- **Hyper-V** &ndash; Se você estiver usando o Windows com o Hyper-V habilitado, siga as etapas em [Desabilitando o Hyper-V](~/android/get-started/installation/android-emulator/hardware-acceleration.md#disable-hyperv).

- **Device Guard** &ndash; o Device Guard e o Credential Guard podem impedir que o Hyper-V seja desabilitado em máquinas Windows. Para desabilitar o Device Guard e o Credential Guard, consulte [Desabilitando o Device Guard](~/android/get-started/installation/android-emulator/hardware-acceleration.md#disable-devguard).

- **Software antivírus** &ndash; se você estiver executando um software antivírus que usa virtualização assistida por hardware (como o Avast), desabilite ou desinstale esse software, reinicialize e tente novamente o iniciar o Emulador do SDK do Android.


#### <a name="incorrect-bios-settings"></a>Configurações incorretas de BIOS

Se você estiver usando o HAXM em um computador Windows, o HAXM não funcionará a menos que a tecnologia de virtualização (Intel VT-x) esteja habilitada no BIOS. Se a VT-x estiver desabilitada, você verá um erro semelhante ao seguinte quando tentar iniciar o Emulador do SDK do Android:

**Este computador atende aos requisitos para HAXM, mas a Intel Virtualization Technology (VT-x) não está ativada.**

Para corrigir esse erro, inicialize o computador no BIOS, habilite a VT-x e a SLAT (Conversão de Endereços de Segundo Nível) e reinicie o computador no Windows.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

Se o Emulador do SDK do Android não for iniciado corretamente, isso costuma ser causado por problemas com o HAXM. Os problemas com o HAXM normalmente são causados por conflitos com outras tecnologias de virtualização, configurações incorretas ou um driver de HAXM desatualizado. Tente reinstalar o driver do HAXM seguindo as etapas detalhadas em [Instalando o HAXM](~/android/get-started/installation/android-emulator/hardware-acceleration.md#install-haxm).

-----
