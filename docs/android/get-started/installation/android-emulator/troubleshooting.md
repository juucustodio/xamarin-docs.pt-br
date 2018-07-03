---
title: Solução de problemas do Android Emulator
description: Neste artigo, você vai aprender a diagnosticar e solucionar problemas que podem ocorrer durante o uso do Android Emulator.
ms.prod: xamarin
ms.assetid: 4F053CC9-9378-47CB-8002-978A6558C4D0
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 06/22/2018
ms.openlocfilehash: 241f38cbfe013776b2e36b8102ae4b90cf610d80
ms.sourcegitcommit: 26033c087f49873243751deded8037d2da701655
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/25/2018
ms.locfileid: "36935276"
---
# <a name="android-emulator-troubleshooting"></a>Solução de problemas do Android Emulator

_Neste artigo, são descritas as mensagens de aviso e os problemas mais comuns que ocorrem durante a execução do Android Emulator, juntamente com dicas e soluções alternativas._

<a name="perfwarn" />

## <a name="performance-warnings"></a>Avisos de desempenho

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

A partir do Visual Studio 2017 versão 15.4, uma caixa de diálogo de aviso de desempenho pode ser exibida quando você implanta seu aplicativo pela primeira vez no Android Emulator. Essas caixas de diálogo de aviso estão explicadas abaixo.

### <a name="computer-does-not-contain-an-intel-procesor"></a>O computador Não Contém um Processador Intel

![O computador não contém um processador Intel](troubleshooting-images/01-no-intel-processor.png)

Esta caixa de diálogo será exibida se seu computador não tiver um processador Intel, que é necessário para a aceleração do Emulador do SDK do Android. Se seu computador não tem um processador Intel, recomenda-se usar um dispositivo Android físico para desenvolvimento.

### <a name="hyper-v-is-installed-or-active"></a>O Hyper-V está Instalado ou Ativo

![O Hyper-V está instalado ou ativo](troubleshooting-images/02-hyper-v-active.png)

Esta caixa de diálogo será exibida quando o Hyper-V estiver instalado ou ativo, e deverá ser desabilitada. A seção [Desabilitar o Hyper-V](#disable-hyperv) explica como resolver esse problema.

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

Se o `STATE` não estiver definido como `RUNNING`, confira [Como usar o Hardware Accelerated Execution Manager da Intel](https://software.intel.com/en-us/android/articles/how-to-use-the-intel-hardware-accelerated-execution-manager-intel-haxm-android-emulator) para resolver o problema.


### <a name="other-failures"></a>Outras Falhas

![Outras falhas](troubleshooting-images/05-other-failure.png)

Esta caixa de diálogo será exibida se o computador tiver um processador Intel, o Hyper-V estiver desabilitado, o Intel HAXM estiver instalado, o processo HAXM estiver em execução, mas o emulador não tiver sido iniciado por algum motivo desconhecido.
Para ajudar a resolver este erro, consulte [How to Use the Intel Hardware Accelerated Execution Manager](https://software.intel.com/en-us/android/articles/how-to-use-the-intel-hardware-accelerated-execution-manager-intel-haxm-android-emulator) (Como usar o Hardware Accelerated Execution Manager da Intel).

### <a name="disabling-performance-warnings"></a>Desabilitar avisos de desempenho

Desabilite os avisos de desempenho se preferir não os ver. No Visual Studio, clique em **Ferramentas > Opções > Xamarin > Configurações do Android** e desabilite a opção **Avisar se não houver suporte para a aceleração de AVD (HAXM)**:

[![Desabilitando avisos de aceleração de AVD](troubleshooting-images/win/06-disable-perf-warnings-sml.png)](troubleshooting-images/win/06-disable-perf-warnings.png#lightbox)


# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

A partir do Visual Studio para Mac build 7.2 (build 559) ou posterior, uma caixa de diálogo de aviso de desempenho será exibida quando você implantar o aplicativo no Android Emulator pela primeira vez. Essas caixas de diálogo de aviso estão explicadas abaixo.

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

## <a name="deployment-issues"></a>Problemas de implantação

Se receber um erro sobre uma falha ao instalar o APK no emulador ou sobre uma falha ao executar o Android Debug Bridge (**adb**), verifique se o SDK do Android pode se conectar ao emulador. Para fazer isso, siga estas etapas:

1. Inicie o emulador no **Android Device Manager** (selecione seu dispositivo virtual e clique em **Iniciar**).

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


## <a name="haxm-issues"></a>Problemas do HAXM

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Se o Android Emulator não inicia corretamente, geralmente o problema é com o HAXM. Os problemas com o HAXM normalmente são causados por conflitos com outras tecnologias de virtualização, configurações incorretas ou um driver de HAXM desatualizado.

<a name="virt-conflicts" />

### <a name="haxm-virtualization-conflicts"></a>Conflitos de virtualização do HAXM

O HAXM pode entrar em conflito com outras tecnologias que usam virtualização, como o Hyper-V, o Windows Device Guard e alguns softwares antivírus:

- **Hyper-V** &ndash; Se você estiver usando uma versão do Windows anterior à **Atualização de abril de 2018 para o Windows 10 (build 1803)** e o Hyper-V estiver habilitado, siga as etapas em [Desabilitando o Hyper-V](#disable-hyperv).

- **Device Guard** &ndash; o Device Guard e o Credential Guard podem impedir que o Hyper-V seja desabilitado em máquinas Windows. Para desabilitar o Device Guard e o Credential Guard, consulte [Desabilitando o Device Guard](#disable-devguard).

- **Software antivírus** &ndash; se você estiver executando um software antivírus que usa virtualização assistida por hardware (como o Avast), desabilite ou desinstale esse software, reinicialize e tente novamente o iniciar o Emulador do SDK do Android.


### <a name="incorrect-bios-settings"></a>Configurações incorretas de BIOS

Se você estiver usando o HAXM em um computador Windows, o HAXM não funcionará a menos que a tecnologia de virtualização (Intel VT-x) esteja habilitada no BIOS. Se a VT-x estiver desabilitada, você verá um erro semelhante ao seguinte ao tentar iniciar o Android Emulator:

**Este computador atende aos requisitos para HAXM, mas a Intel Virtualization Technology (VT-x) não está ativada.**

Para corrigir esse erro, inicialize o computador no BIOS, habilite a VT-x e a SLAT (Conversão de Endereços de Segundo Nível) e reinicie o computador no Windows.

<a name="disable-hyperv" />

### <a name="disabling-hyper-v"></a>Desabilitando o Hyper-V

Se você estiver usando uma versão do Windows anterior à **Atualização de abril de 2018 para o Windows 10 (build 1803)** e o Hyper-V estiver habilitado, será necessário desabilitar o Hyper-V e reinicializar o computador para instalar e usar o HAXM. Se você estiver usando a **Atualização de abril de 2018 para o Windows 10 (build 1803)** ou posterior, a versão 27.2.7 do Android Emulator ou posterior poderá usar o Hyper-V (em vez do HAXM) para aceleração de hardware, por isso não é necessário desabilitar o Hyper-V.

Você pode desabilitar o Hyper-V no Painel de Controle seguindo estas etapas:

1. Na caixa do Windows Search, digite **Programas e**, em seguida, clique no resultado da pesquisa **Programas e Recursos**.

2. Na caixa de diálogo **Programas e Recursos** do Painel de Controle, clique em **Ativar ou desativar recursos do Windows**:

    ![Ativar ou desativar recursos do Windows](troubleshooting-images/win/07-turn-windows-features.png)

3. Desmarque o **Hyper-V** e reinicie o computador:

    ![Desabilitando o Hyper-V na caixa de diálogo Recursos do Windows](troubleshooting-images/win/08-uncheck-hyper-v.png)

Como alternativa, você pode usar o seguinte cmdlet do Powershell para desabilitar o Hyper-v:

`Disable-WindowsOptionalFeature -Online -FeatureName Microsoft-Hyper-V-Hypervisor`

O Intel HAXM e o Microsoft Hyper-V não podem estar ativos ao mesmo tempo. Infelizmente, não há nenhuma maneira de alternar entre o Hyper-V e o HAXM sem reiniciar o computador. 

Em alguns casos, as etapas acima não funcionarão para desabilitar o Hyper-V caso o Device Guard e o Credential Guard estiverem habilitados. Se não for possível desabilitar o Hyper-V (ou ele parecer estar desabilitado, mas ainda ocorrer falha na instalação do HAXM), siga as etapas da próxima seção para desabilitar o Device Guard e o Credential Guard.

<a name="disable-devguard" />

### <a name="disabling-device-guard"></a>Desabilitando o Device Guard

O Device Guard e o Credential Guard podem impedir que o Hyper-V seja desabilitado em computadores Windows. Normalmente, isso é um problema para computadores com domínio associado que são configurados e controlados por uma organização proprietária.
No Windows 10, use as etapas a seguir para ver se o **Device Guard** está em execução:

1. No **Windows Search**, digite **Informações do sistema** para iniciar o aplicativo **Informações do Sistema**.

2. No **Resumo do Sistema**, verifique se **Segurança com base em Virtualização de Device Guard** está presente e está no estado **Em execução**:

   [![O Device Guard está presente e em execução](troubleshooting-images/win/09-device-guard-sml.png)](troubleshooting-images/win/09-device-guard.png#lightbox)

Se o Device Guard estiver habilitado, use as seguintes etapas para desabilitá-lo:

1. Verifique se o **Hyper-V** está desabilitado (em **Ativar ou desativar recursos do Windows**) conforme descrito na seção anterior.

2. Na caixa do Windows Search, digite **gpedit** e selecione o resultado da pesquisa **Editar política de grupo**. Isso inicializa o **Editor de Política de Grupo Local**.

3. No **Editor de Política de Grupo Local**, navegue até **Configuração do Computador > Modelos Administrativos > Sistema > Device Guard**:

   [![Device Guard no Editor de Política de Grupo Local](troubleshooting-images/win/10-group-policy-editor-sml.png)](troubleshooting-images/win/10-group-policy-editor.png#lightbox)

4. Altere a opção **Ativar Segurança Baseada em Virtualização** para **Desabilitada** (conforme mostrado acima) e saia do **Editor de Política de Grupo Local**.

5. Na caixa do Windows Search, digite **cmd**. Quando o **Prompt de Comando** aparecer nos resultados da pesquisa, clique com o botão direito do mouse em **Prompt de Comando** e selecione **Executar como Administrador**.

6. Copie e cole os comandos a seguir na janela do prompt de comando (se unidade **Z:** estiver em uso, selecione uma letra da unidade não usada):

        mountvol Z: /s
        copy %WINDIR%\System32\SecConfig.efi Z:\EFI\Microsoft\Boot\SecConfig.efi /Y
        bcdedit /create {0cb3b571-2f2e-4343-a879-d86a476d7215} /d "DebugTool" /application osloader
        bcdedit /set {0cb3b571-2f2e-4343-a879-d86a476d7215} path "\EFI\Microsoft\Boot\SecConfig.efi"
        bcdedit /set {bootmgr} bootsequence {0cb3b571-2f2e-4343-a879-d86a476d7215}
        bcdedit /set {0cb3b571-2f2e-4343-a879-d86a476d7215} loadoptions DISABLE-LSA-ISO,DISABLE-VBS
        bcdedit /set {0cb3b571-2f2e-4343-a879-d86a476d7215} device partition=Z:
        mountvol Z: /d

7. Reinicie o computador. Na tela de inicialização, você verá um prompt semelhante ao seguinte:

   **Você deseja desabilitar o Credential Guard?**

   Pressione a tecla indicada para desabilitar o Credential Guard como solicitado.

8. Após a reinicialização do computador, verifique novamente a fim de garantir que o Hyper-V esteja desabilitado (conforme descrito nas etapas anteriores).

Se o Hyper-V ainda não estiver desabilitado, as políticas do computador ingressado no domínio podem impedir que você desabilite o Device Guard ou o Credential Guard. Nesse caso, você poderá solicitar uma isenção ao administrador de domínio a fim de permitir que você recuse o Credential Guard. Como alternativa, é possível usar um computador que não esteja ingressado no domínio para usar o HAXM.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

Se o Android Emulator não inicia corretamente, geralmente o problema é com o HAXM. Os problemas com o HAXM normalmente são causados por conflitos com outras tecnologias de virtualização, configurações incorretas ou um driver de HAXM desatualizado. Tente reinstalar o driver do HAXM seguindo as etapas detalhadas em [Instalando o HAXM](~/android/get-started/installation/android-emulator/hardware-acceleration.md#install-haxm).

-----

