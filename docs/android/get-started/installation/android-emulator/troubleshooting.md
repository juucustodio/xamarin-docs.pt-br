---
title: Solução de problemas do Android Emulator
description: Neste artigo, você vai aprender a diagnosticar e solucionar problemas que podem ocorrer durante o uso do Android Emulator.
zone_pivot_groups: platform
ms.prod: xamarin
ms.assetid: 4F053CC9-9378-47CB-8002-978A6558C4D0
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 08/27/2018
ms.openlocfilehash: 5f8d977c126cfe4bdfdb48470841ee17de6bda31
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50117727"
---
# <a name="android-emulator-troubleshooting"></a>Solução de problemas do Android Emulator

_Neste artigo, são descritas as mensagens de aviso e os problemas mais comuns que ocorrem durante a execução do Android Emulator. Além disso, são descritas soluções para resolver esses erros, bem como várias dicas de solução de problemas para ajudá-lo a diagnosticar problemas do emulador._

::: zone pivot="windows"

## <a name="deployment-issues-on-windows"></a>Problemas de implantação no Windows

Algumas mensagens de erro podem ser exibidas pelo emulador quando você implanta o aplicativo. Os erros e soluções mais comuns são explicados aqui.

### <a name="deployment-errors"></a>Erros de implantação

Se vir um erro sobre uma falha ao instalar o APK no emulador ou sobre uma falha ao executar o Android Debug Bridge (**adb**), verifique se o SDK do Android pode se conectar ao emulador. Para verificar a conectividade do emulador, use as seguintes etapas:

1. Inicie o emulador no **Android Device Manager** (selecione seu dispositivo virtual e clique em **Iniciar**).

2. Abra um prompt de comando e vá para a pasta em que o **adb** está instalado. Se o SDK do Android estiver instalado na localização padrão, o **adb** estará localizado em **C:\\Arquivos de Programas (x86)\\Android\\android-sdk\\platform-tools\\adb.exe**. Se não estiver, modifique esse caminho para a localização do SDK do Android em seu computador.

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


### <a name="mmio-access-error"></a>Erro ao acessar o MMIO

Se for exibida a mensagem **Ocorreu um erro de acesso de MMIO**, reinicie o emulador.


<a name="gps-win" />

## <a name="missing-google-play-services"></a>Google Play Services ausente

Se o dispositivo virtual em execução no emulador não tem o Google Play Services ou o Google Play Store instalado, essa condição geralmente é causada pela criação de um dispositivo virtual sem incluir esses pacotes. Ao criar um dispositivo virtual (confira [Gerenciando dispositivos virtuais com o Android Device Manager](~/android/get-started/installation/android-emulator/device-manager.md)), certifique-se de selecionar pelo menos uma das opções a seguir:

- **APIs do Google** &ndash; inclui o Google Play Services no dispositivo virtual.
- **Google Play Store** &ndash; inclui o Google Play Store no dispositivo virtual.

Por exemplo, este dispositivo virtual incluirá o Google Play Services e o Google Play Store:

[![Exemplo de AVD com o Google Play Services e o Google Play Store habilitados](troubleshooting-images/win/00-add-gps-w158-sml.png)](troubleshooting-images/win/00-add-gps-w158.png#lightbox)

> [!NOTE]
> Imagens do Google Play Store estão disponíveis apenas para alguns tipos de dispositivos básicos, como Pixel, Pixel 2, Nexus 5 e Nexus 5X.


<a name="perf-win" />

## <a name="performance-issues"></a>Problemas de desempenho

Problemas de desempenho normalmente são causados por um dos seguintes problemas:

- O emulador está em execução sem aceleração de hardware.

- O dispositivo virtual em execução no emulador não está usando uma imagem do sistema com base em x86.

As seções a seguir tratam desses cenários com mais detalhes.

### <a name="hardware-acceleration-is-not-enabled"></a>A aceleração de hardware não está habilitada

Se a aceleração de hardware não estiver habilitada, iniciar um dispositivo virtual do Device Manager gerará uma caixa de diálogo com uma mensagem de erro indicando que a Plataforma de Hipervisor do Windows (WHPX) não está configurada corretamente:

![Exemplo de aviso do Device Manager](troubleshooting-images/win/01-dev-mgr-warning-w158.png)

Se essa mensagem de erro for exibida, confira [Problemas de aceleração de hardware](#accel-issues-win) mais adiante para ver as etapas você pode seguir para verificar e habilitar a aceleração de hardware.


### <a name="acceleration-is-enabled-but-the-emulator-runs-too-slowly"></a>A aceleração de está habilitada, mas o emulador é executado muito lentamente 

Uma causa comum para esse problema é não estar usando uma imagem com base em x86 em seu dispositivo virtual (AVD). Ao criar um dispositivo virtual (confira [Gerenciando dispositivos virtuais com o Android Device Manager](~/android/get-started/installation/android-emulator/device-manager.md)), certifique-se de selecionar uma imagem de sistema baseada em x86:

[![Selecionar uma imagem de sistema x86 para um dispositivo virtual](troubleshooting-images/win/02-x86-virtual-device-w158-sml.png)](troubleshooting-images/win/02-x86-virtual-device-w158.png#lightbox)


<a name="accel-issues-win" />

## <a name="hardware-acceleration-issues"></a>Problemas de aceleração de hardware

Se estiver usando o Hyper-V ou o HAXM para aceleração de hardware, você poderá ter problemas de configuração ou conflitos com outros softwares no computador. Você pode verificar se a aceleração de hardware está habilitada (e qual método de aceleração está usando o emulador) abrindo um prompt de comando e digitando o seguinte comando:

```cmd
"C:\Program Files (x86)\Android\android-sdk\emulator\emulator-check.exe" accel
```

Esse comando presume que o SDK do Android está instalado na localização padrão do **C:\\Arquivos de Programa (x86)\\Android\\android-sdk**. Se não estiver, modifique o caminho acima para a localização do SDK do Android no computador.

### <a name="hardware-acceleration-not-available"></a>Aceleração de hardware não disponível

Se o Hyper-V estiver disponível, uma mensagem semelhante ao exemplo a seguir será retornada do comando **emulator-check.exe accel**:

```cmd
HAXM is not installed, but Windows Hypervisor Platform is available.
```

Se o HAXM estiver disponível, uma mensagem semelhante ao exemplo a seguir será retornada:

```cmd
HAXM version 6.2.1 (4) is installed and usable.
```

Se a aceleração de hardware não estiver disponível, uma mensagem semelhante ao exemplo a seguir será exibida (o emulador procurará pelo HAXM se não for possível encontrar o Hyper-V):

```cmd
HAXM is not installed on this machine
```

Se a aceleração de hardware não estiver disponível, confira [Acelerar com o Hyper-V](~/android/get-started/installation/android-emulator/hardware-acceleration.md?tabs=vswin#hyper-v-win) para saber como habilitar a aceleração de hardware em seu computador.

### <a name="incorrect-bios-settings"></a>Configurações incorretas de BIOS

Se o BIOS não tiver sido configurado corretamente para dar suporte à aceleração de hardware, uma mensagem semelhante ao exemplo a seguir será exibida quando você executar o comando **emulator-check.exe accel**:

```cmd
VT feature disabled in BIOS/UEFI
```

Para corrigir esse problema, reinicialize o BIOS do computador e habilite as seguintes opções:

- Tecnologia de virtualização (pode ter um rótulo diferente dependendo do fabricante da placa-mãe).
- Prevenção de Execução de Dados Baseada em Hardware.

Se a aceleração de hardware estiver habilitada e o BIOS estiver configurado corretamente, o emulador deverá ser executado com êxito com aceleração de hardware.
No entanto, ainda poderá haver problemas devido a questões específicas do Hyper-V e do HAXM, conforme explicado a seguir.


### <a name="hyper-v-issues"></a>Problemas do Hyper-V

Em alguns casos, habilitar o **Hyper-V** e a **Plataforma de Hipervisor do Windows** na caixa de diálogo **Ativar ou desativar recursos do Windows** pode não habilitar adequadamente o Hyper-V. Para verificar se o Hyper-V está habilitado, use as seguintes etapas:

1. Na caixa de pesquisa do Windows, digite **powershell**.

2. Clique com o botão direito do mouse em **Windows PowerShell** nos resultados da pesquisa e selecione **Executar como administrador**.

3. No console do PowerShell, insira o seguinte comando:

    ```powershell
    Get-WindowsOptionalFeature -FeatureName Microsoft-Hyper-V-All -Online
    ```

    Se o Hyper-V não estiver habilitado, uma mensagem semelhante ao exemplo a seguir será exibida para indicar que o estado do Hyper-V é **Desabilitado**:

    ```
    FeatureName      : Microsoft-Hyper-V-All
    DisplayName      : Hyper-V
    Description      : Provides services and management tools for creating and running virtual machines and their resources.
    RestartRequired  : Possible
    State            : Disabled
    CustomProperties : 
    ```

4. No console do PowerShell, insira o seguinte comando:

    ```powershell
    Get-WindowsOptionalFeature -FeatureName HypervisorPlatform -Online
    ```
    Se o Hipervisor não estiver habilitado, uma mensagem semelhante ao exemplo a seguir será exibida para indicar que o estado de HypervisorPlatform é **Desabilitado**:

    ```
    FeatureName      : HypervisorPlatform
    DisplayName      : Windows Hypervisor Platform
    Description      : Enables virtualization software to run on the Windows hypervisor
    RestartRequired  : Possible
    State            : Disabled
    CustomProperties : 
    ```

Se o Hyper-V e/ou a HypervisorPlatform não estiverem habilitados, use os seguintes comandos do PowerShell para habilitá-los:

```powershell
Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Hyper-V -All
Enable-WindowsOptionalFeature -Online -FeatureName HypervisorPlatform -All
```

Após a conclusão desses comandos, reinicialize. 

Para obter mais informações sobre como habilitar o Hyper-V (incluindo técnicas para habilitar o Hyper-V usando a ferramenta Gerenciamento e Manutenção de Imagens de Implantação ), confira [Instalar o Hyper-V](https://docs.microsoft.com/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v).


### <a name="haxm-issues"></a>Problemas do HAXM

Os problemas com o HAXM normalmente são causados por conflitos com outras tecnologias de virtualização, configurações incorretas ou um driver de HAXM desatualizado.

### <a name="haxm-process-is-not-running"></a>O processo HAXM não está em execução

Se o HAXM estiver instalado, você poderá verificar se o processo do HAXM está em execução abrindo um prompt de comando e digitando o seguinte comando:

```cmd
sc query intelhaxm
```

Se o processo do HAXM estiver em execução, você verá um resultado semelhante ao seguinte:

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

Se o `STATE` não estiver definido como `RUNNING`, confira [Como usar o Hardware Accelerated Execution Manager da Intel](https://software.intel.com/android/articles/how-to-use-the-intel-hardware-accelerated-execution-manager-intel-haxm-android-emulator) para resolver o problema.


<a name="virt-conflicts" />

#### <a name="haxm-virtualization-conflicts"></a>Conflitos de virtualização do HAXM

O HAXM pode entrar em conflito com outras tecnologias que usam virtualização, como o Hyper-V, o Windows Device Guard e alguns softwares antivírus:

- **Hyper-V** &ndash; Se você estiver usando uma versão do Windows anterior à **Atualização de abril de 2018 para o Windows 10 (build 1803)** e o Hyper-V estiver habilitado, siga as etapas em [Desabilitando o Hyper-V](#disable-hyperv) para que o HAXM possa ser habilitado.

- **Device Guard** &ndash; o Device Guard e o Credential Guard podem impedir que o Hyper-V seja desabilitado em máquinas Windows. Para desabilitar o Device Guard e o Credential Guard, consulte [Desabilitando o Device Guard](#disable-devguard).

- **Software antivírus** &ndash; se você estiver executando um software antivírus que usa virtualização assistida por hardware (como o Avast), desabilite ou desinstale esse software, reinicialize e tente novamente o iniciar o Android Emulator.


#### <a name="incorrect-bios-settings"></a>Configurações incorretas de BIOS

Se você estiver usando o HAXM em um computador Windows, o HAXM não funcionará a menos que a tecnologia de virtualização (Intel VT-x) esteja habilitada no BIOS. Se a VT-x estiver desabilitada, você verá um erro semelhante ao seguinte ao tentar iniciar o Android Emulator:

**Este computador atende aos requisitos para HAXM, mas a Intel Virtualization Technology (VT-x) não está ativada.**

Para corrigir esse erro, inicialize o computador no BIOS, habilite a VT-x e a SLAT (Conversão de Endereços de Segundo Nível) e reinicie o computador no Windows.

<a name="disable-hyperv" />

#### <a name="disabling-hyper-v"></a>Desabilitando o Hyper-V

Se você estiver usando uma versão do Windows anterior à **Atualização de abril de 2018 para o Windows 10 (build 1803)** e o Hyper-V estiver habilitado, será necessário desabilitar o Hyper-V e reinicializar o computador para instalar e usar o HAXM. Se você estiver usando a **Atualização de abril de 2018 para o Windows 10 (build 1803)** ou posterior, a versão 27.2.7 do Android Emulator ou posterior poderá usar o Hyper-V (em vez do HAXM) para aceleração de hardware, por isso não é necessário desabilitar o Hyper-V.

Você pode desabilitar o Hyper-V no Painel de Controle seguindo estas etapas:

1. Insira **recursos do windows** na caixa de pesquisa do Windows e selecione **Ativar ou desativar recursos do Windows** nos resultados da pesquisa.

2. Desmarque a opção **Hyper-V**:

    ![Desabilitando o Hyper-V na caixa de diálogo Recursos do Windows](troubleshooting-images/win/03-uncheck-hyper-v.png)

3. Reinicie o computador.

Como alternativa, você pode usar o seguinte comando do PowerShell para desabilitar o Hipervisor Hyper-V:

`Disable-WindowsOptionalFeature -Online -FeatureName Microsoft-Hyper-V-Hypervisor`

O Intel HAXM e o Microsoft Hyper-V não podem estar ativos ao mesmo tempo. Infelizmente, não há nenhuma maneira de alternar entre o Hyper-V e o HAXM sem reiniciar o computador. 

Em alguns casos, as etapas acima não funcionarão para desabilitar o Hyper-V caso o Device Guard e o Credential Guard estiverem habilitados. Se não for possível desabilitar o Hyper-V (ou ele parecer estar desabilitado, mas ainda ocorrer falha na instalação do HAXM), siga as etapas da próxima seção para desabilitar o Device Guard e o Credential Guard.

<a name="disable-devguard" />

#### <a name="disabling-device-guard"></a>Desabilitando o Device Guard

O Device Guard e o Credential Guard podem impedir que o Hyper-V seja desabilitado em computadores Windows. Normalmente, isso é um problema para computadores com domínio associado que são configurados e controlados por uma organização proprietária. No Windows 10, use as etapas a seguir para ver se o **Device Guard** está em execução:

1. Insira **Informações do sistema** na caixa de pesquisa do Windows e selecione **Informações do Sistema** nos resultados da pesquisa.

2. No **Resumo do Sistema**, verifique se **Segurança com base em Virtualização de Device Guard** está presente e está no estado **Em execução**:

   [![O Device Guard está presente e em execução](troubleshooting-images/win/04-device-guard-sml.png)](troubleshooting-images/win/04-device-guard.png#lightbox)

Se o Device Guard estiver habilitado, use as seguintes etapas para desabilitá-lo:

1. Verifique se o **Hyper-V** está desabilitado (em **Ativar ou desativar recursos do Windows**) conforme descrito na seção anterior.

2. Na caixa de pesquisa do Windows, digite **gpedit** e selecione o resultado da pesquisa **Editar política de grupo**. Essas etapas iniciam o **Editor de Política de Grupo Local**.

3. No **Editor de Política de Grupo Local**, navegue até **Configuração do Computador > Modelos Administrativos > Sistema > Device Guard**:

   [![Device Guard no Editor de Política de Grupo Local](troubleshooting-images/win/05-group-policy-editor-sml.png)](troubleshooting-images/win/05-group-policy-editor.png#lightbox)

4. Altere a opção **Ativar Segurança Baseada em Virtualização** para **Desabilitada** (conforme mostrado acima) e saia do **Editor de Política de Grupo Local**.

5. Na caixa de pesquisa do Windows, digite **cmd**. Quando o **Prompt de Comando** aparecer nos resultados da pesquisa, clique com o botão direito do mouse em **Prompt de Comando** e selecione **Executar como Administrador**.

6. Copie e cole os comandos a seguir na janela do prompt de comando (se unidade **Z:** estiver em uso, selecione uma letra da unidade não usada):

        mountvol Z: /s
        copy %WINDIR%\System32\SecConfig.efi Z:\EFI\Microsoft\Boot\SecConfig.efi /Y
        bcdedit /create {0cb3b571-2f2e-4343-a879-d86a476d7215} /d "DebugTool" /application osloader
        bcdedit /set {0cb3b571-2f2e-4343-a879-d86a476d7215} path "\EFI\Microsoft\Boot\SecConfig.efi"
        bcdedit /set {bootmgr} bootsequence {0cb3b571-2f2e-4343-a879-d86a476d7215}
        bcdedit /set {0cb3b571-2f2e-4343-a879-d86a476d7215} loadoptions DISABLE-LSA-ISO,DISABLE-VBS
        bcdedit /set {0cb3b571-2f2e-4343-a879-d86a476d7215} device partition=Z:
        mountvol Z: /d

7. Reinicie o computador. Na tela de inicialização, você verá um prompt semelhante à mensagem seguinte:

   **Você deseja desabilitar o Credential Guard?**

   Pressione a tecla indicada para desabilitar o Credential Guard como solicitado.

8. Após a reinicialização do computador, verifique novamente a fim de garantir que o Hyper-V esteja desabilitado (conforme descrito nas etapas anteriores).

Se o Hyper-V ainda não estiver desabilitado, as políticas do computador ingressado no domínio podem impedir que você desabilite o Device Guard ou o Credential Guard. Nesse caso, você poderá solicitar uma isenção ao administrador do domínio a fim de permitir que você recuse o Credential Guard. Como alternativa, você poderá usar um computador que não esteja ingressado no domínio se precisar usar o HAXM.


## <a name="additional-troubleshooting-tips"></a>Dicas adicionais de solução de problemas

As sugestões a seguir costumam ser úteis para diagnosticar problemas do Android Emulator.

### <a name="starting-the-emulator-from-the-command-line"></a>Iniciar o emulador da linha de comando

Se o emulador ainda não estiver em execução, você poderá iniciá-lo da linha de comando (em vez de dentro do Visual Studio) para exibir sua saída. Normalmente, as imagens do AVD do Android Emulator são armazenadas na seguinte localização (substitua o *nome de usuário* por seu nome de usuário do Windows):

**C:\\Usuários\\*nome de usuário*\\.android\\avd**

Você pode iniciar o emulador com uma imagem de AVD desta localização passando o nome da pasta do AVD. Por exemplo, esse comando inicia um AVD chamado **Pixel_API_27**:

```cmd
"C:\Program Files (x86)\Android\android-sdk\emulator\emulator.exe" -partition-size 512 -no-boot-anim -verbose -feature WindowsHypervisorPlatform -avd Pixel_API_27 -prop monodroid.avdname=Pixel_API_27
```

Esse exemplo presume que o SDK do Android está instalado na localização padrão do **C:\\Arquivos de Programa (x86)\\Android\\android-sdk**. Se não estiver, modifique o caminho acima para a localização do SDK do Android no computador.

Quando você executar esse comando, ele produzirá muitas linhas de saída enquanto o emulador é iniciado. Em particular, linhas como o exemplo a seguir serão impressas se a aceleração de hardware estiver habilitada e funcionando corretamente (neste exemplo, o HAXM é usado para a aceleração de hardware):

```cmd
emulator: CPU Acceleration: working
emulator: CPU Acceleration status: HAXM version 6.2.1 (4) is installed and usable.
```

### <a name="viewing-device-manager-logs"></a>Exibir logs do Device Manager

Com frequência, você pode diagnosticar problemas do emulador exibindo os logs do Device Manager. Esses logs são gravados na seguinte localização:

**C:\\Usuários\\*nome de usuário*\\AppData\\Roaming\\XamarinDeviceManager**

Você pode exibir cada arquivo **DeviceManager.log** usando um editor de texto como o Bloco de notas. A entrada de log de exemplo a seguir indica que o HAXM não foi encontrado no computador:

```cmd
Component Intel x86 Emulator Accelerator (HAXM installer) r6.2.1 [Extra: (Intel Corporation)] not present on the system
```



::: zone-end
::: zone pivot="macos"

## <a name="deployment-issues-on-macos"></a>Problemas de implantação no macOS

Algumas mensagens de erro podem ser exibidas pelo emulador quando você implanta o aplicativo. Os erros e soluções mais comuns são explicados abaixo.

### <a name="deployment-errors"></a>Erros de implantação

Se vir um erro sobre uma falha ao instalar o APK no emulador ou sobre uma falha ao executar o Android Debug Bridge (**adb**), verifique se o SDK do Android pode se conectar ao emulador. Para verificar a conectividade, use as seguintes etapas:

1. Inicie o emulador no **Android Device Manager** (selecione seu dispositivo virtual e clique em **Iniciar**).

2. Abra um prompt de comando e vá para a pasta em que o **adb** está instalado. Se o SDK do Android estiver instalado na localização padrão, o **adb** estará localizado no diretório **~/Library/Developer/Xamarin/android-sdk-macosx/platform-tools/adb**. Se não estiver, modifique o caminho para a localização do SDK do Android no computador.

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


### <a name="mmio-access-error"></a>Erro ao acessar o MMIO

Se a mensagem **Ocorreu um erro de acesso de MMIO** for exibida, reinicie o emulador.

<a name="gps-mac" />

## <a name="missing-google-play-services"></a>Google Play Services ausente

Se o dispositivo virtual em execução no emulador não tem o Google Play Services ou o Google Play Store instalado, essa condição normalmente é causada pela criação de um dispositivo virtual sem incluir esses pacotes. Ao criar um dispositivo virtual (confira [Gerenciando dispositivos virtuais com o Android Device Manager](~/android/get-started/installation/android-emulator/device-manager.md)), certifique-se de selecionar pelo menos uma destas opções:

- **APIs do Google** &ndash; inclui o Google Play Services no dispositivo virtual.
- **Google Play Store** &ndash; inclui o Google Play Store no dispositivo virtual.

Por exemplo, este dispositivo virtual incluirá o Google Play Services e o Google Play Store:

[![Exemplo de AVD com o Google Play Services e o Google Play Store habilitados](troubleshooting-images/mac/01-google-play-services-m75-sml.png)](troubleshooting-images/mac/01-google-play-services-m75.png#lightbox)

> [!NOTE]
> Imagens do Google Play Store estão disponíveis apenas para alguns tipos de dispositivos básicos, como Pixel, Pixel 2, Nexus 5 e Nexus 5X.


<a name="perf-mac" />

## <a name="performance-issues"></a>Problemas de desempenho

Problemas de desempenho normalmente são causados por um dos seguintes problemas:

- O emulador está em execução sem aceleração de hardware.

- O dispositivo virtual em execução no emulador não está usando uma imagem do sistema com base em x86.

As seções a seguir tratam desses cenários com mais detalhes.

### <a name="hardware-acceleration-is-not-enabled"></a>A aceleração de hardware não está habilitada

Se a aceleração de hardware não estiver habilitada, poderá aparecer uma caixa de diálogo com uma mensagem semelhante a **o dispositivo será executado sem aceleração** quando você implantar seu aplicativo no Android Emulator. Se você não tiver certeza se a aceleração de hardware está habilitada no computador (ou se quiser saber qual tecnologia está fornecendo a aceleração), confira [Problemas de aceleração de hardware](#accel-issues-mac) mais adiante para ver as etapas que pode seguir para verificar e habilitar a aceleração de hardware.


### <a name="acceleration-is-enabled-but-the-emulator-runs-too-slowly"></a>A aceleração de está habilitada, mas o emulador é executado muito lentamente 

Uma causa comum para esse problema é não estar usando uma imagem com base em x86 em seu dispositivo virtual. Ao criar um dispositivo virtual (confira [Gerenciando dispositivos virtuais com o Android Device Manager](~/android/get-started/installation/android-emulator/device-manager.md)), certifique-se de selecionar uma imagem de sistema baseada em x86:

[![Selecionar uma imagem de sistema x86 para um dispositivo virtual](troubleshooting-images/mac/02-x86-virtual-device-m75-sml.png)](troubleshooting-images/mac/02-x86-virtual-device-m75.png#lightbox)

<a name="accel-issues-mac" />

## <a name="hardware-acceleration-issues"></a>Problemas de aceleração de hardware

Quer esteja usando o Framework de Hipervisor ou o HAXM para a aceleração de hardware do emulador, você poderá ter problemas causados por falhas de instalação ou pelo uso de uma versão desatualizada do macOS. As seções a seguir podem ajudá-lo a resolver esse problema.

<a name="hypervisor-issues" />

### <a name="hypervisor-framework-issues"></a>Problemas do Framework de Hipervisor

Se você estiver usando o macOS 10.10 ou posterior em um Mac mais recente, o Android Emulator usará automaticamente o Framework de Hipervisor para aceleração de hardware. No entanto, alguns Macs mais antigos ou Macs que executam uma versão do macOS anterior à 10.10 poderão não ter suporte para o Framework de Hipervisor.

Para determinar se o seu Mac tem suporte para o Framework de Hipervisor ou não, abra um Terminal e digite o seguinte comando:

```bash
sysctl kern.hv_support
```

Se o Mac tiver suporte para o Framework de Hipervisor, o comando acima retornará o seguinte resultado:

```bash
kern.hv_support: 1
```

Se o Framework de Hipervisor não estiver disponível em seu Mac, você poderá seguir as etapas em [Acelerar com o HAXM](~/android/get-started/installation/android-emulator/hardware-acceleration.md?tabs=vsmac#haxm-mac) para usar o HAXM para aceleração.


### <a name="haxm-issues"></a>Problemas do HAXM

Se o Android Emulator não inicia corretamente, normalmente o problema é causado pelo HAXM. Os problemas com o HAXM normalmente são causados por conflitos com outras tecnologias de virtualização, configurações incorretas ou um driver de HAXM desatualizado. Tente reinstalar o driver do HAXM seguindo as etapas detalhadas em [Instalando o HAXM](~/android/get-started/installation/android-emulator/hardware-acceleration.md?tabs=vsmac#install-haxm-mac).


## <a name="additional-troubleshooting-tips"></a>Dicas adicionais de solução de problemas

As sugestões a seguir costumam ser úteis para diagnosticar problemas do Android Emulator.

### <a name="starting-the-emulator-from-the-command-line"></a>Iniciar o emulador da linha de comando

Se o emulador ainda não estiver em execução, você poderá iniciá-lo da linha de comando (em vez de dentro do Visual Studio para Mac) para exibir sua saída. Normalmente, as imagens do AVD do Android Emulator são armazenadas na seguinte localização:

**~/.android/avd**

Você pode iniciar o emulador com uma imagem de AVD desta localização passando o nome da pasta do AVD. Por exemplo, esse comando inicia um AVD chamado **Pixel_2_API_28**:

```cmd
~/Library/Developer/Xamarin/android-sdk-macosx/emulator/emulator -partition-size 512 -no-boot-anim -verbose -feature WindowsHypervisorPlatform -avd Pixel_2_API_28 -prop monodroid.avdname=Pixel_2_API_28
```

Se o SDK do Android estiver instalado na localização padrão, o emulador estará localizado no diretório **~/Library/Developer/Xamarin/android-sdk-macosx/emulator**. Se não estiver, modifique o caminho para a localização do SDK do Android no Mac.

Quando você executar esse comando, ele produzirá muitas linhas de saída enquanto o emulador é iniciado. Em particular, linhas como o exemplo a seguir serão impressas se a aceleração de hardware estiver habilitada e funcionando corretamente (neste exemplo, o Framework de Hipervisor é usado para a aceleração de hardware):

```cmd
emulator: CPU Acceleration: working
emulator: CPU Acceleration status: Hypervisor.Framework OS X Version 10.13
```

### <a name="viewing-device-manager-logs"></a>Exibir logs do Device Manager

Com frequência, você pode diagnosticar problemas do emulador exibindo os logs do Device Manager. Esses logs são gravados na seguinte localização:

**~/Library/Logs/XamarinDeviceManager**

Você pode exibir cada arquivo **Android Devices.log** clicando duas vezes nele para abri-lo no aplicativo de Console. A entrada de log de exemplo a seguir indica que o HAXM não foi encontrado:

```cmd
Component Intel x86 Emulator Accelerator (HAXM installer) r6.2.1 [Extra: (Intel Corporation)] not present on the system
```

::: zone-end