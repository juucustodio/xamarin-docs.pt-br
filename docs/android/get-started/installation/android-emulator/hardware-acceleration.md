---
title: Aceleração de Hardware do Android Emulator
description: Como preparar seu computador para obter o desempenho máximo do Emulador do SDK do Android
ms.prod: xamarin
ms.assetid: 915874C3-2F0F-4D83-9C39-ED6B90BB2C8E
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 04/04/2018
ms.openlocfilehash: d5921c549c299197bdc442c9b883b49064655f76
ms.sourcegitcommit: 6f7033a598407b3e77914a85a3f650544a4b6339
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2018
---
# <a name="android-emulator-hardware-acceleration"></a>Aceleração de Hardware do Android Emulator

Como o Emulador do SDK do Android é extremamente lento sem a aceleração de hardware, o HAXM (Hardware Accelerated Execution Manager) da Intel é a forma recomendada para aprimorar o desempenho do Emulador do SDK do Android.



## <a name="haxm-overview"></a>Visão geral do HAXM

O HAXM é um mecanismo de virtualização assistido por hardware (hipervisor) que usa a VT (tecnologia de virtualização) da Intel para acelerar a emulação de aplicativos Android em um computador host. Em conjunto com as imagens de emulador x86 do Android fornecidas pela Intel e com o Gerenciador de SDK do Android oficial, o HAXM permite uma emulação mais rápida do Android em sistemas habilitados para VT. 

Se você estiver desenvolvendo em um computador com CPU Intel que tenha recursos de VT, aproveite o HAXM para acelerar significativamente o Emulador do SDK do Android (se não tiver certeza se a CPU é compatível com VT, consulte [Determine If Your Processor Supports Intel Virtualization Technology](https://www.intel.com/content/www/us/en/support/processors/000005486.html) (Determinar se o processador é compatível com a tecnologia de virtualização da Intel)).


> [!NOTE]
> Não é possível executar um emulador acelerado por VM dentro de outra VM, como uma VM hospedada por VirtualBox, VMWare ou Docker. Você deve executar Google Android Emulator [diretamente em seu hardware de sistema](https://developer.android.com/studio/run/emulator-acceleration.html#extensions).

O Emulador do SDK do Android utiliza o HAXM automaticamente quando ele está disponível. Ao selecionar um dispositivo virtual baseado em **x86** (conforme descrito em [Configuração e Uso](~/android/deploy-test/debugging/android-sdk-emulator/index.md)), o dispositivo virtual usará o HAXM para aceleração de hardware. Antes de usar o emulador do SDK do Android pela primeira vez, é uma boa ideia verificar se o HAXM está instalado e disponível para o emulador do SDK do Android.

## <a name="verifying-haxm-installation"></a>Verificando a instalação do HAXM

Você pode verificar se o HAXM está disponível olhando a janela **Iniciando o Emulador do Android** quando o emulador é iniciado. Para iniciar o Emulador do SDK do Android, faça o seguinte:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. Inicie o Gerenciador do Emulador do Android clicando em **Ferramentas > Android > Gerenciador do Emulador do Android**:

    [![Local do item de menu do Gerenciador do Emulador do Android](hardware-acceleration-images/win/01-avd-manager-menu-item-sml.png)](hardware-acceleration-images/win/01-avd-manager-menu-item.png#lightbox)

2. Se uma caixa de diálogo de **Aviso de Desempenho** semelhante à seguinte for exibida, significa que o HAXM ainda não foi instalado ou configurado corretamente em seu computador:
   

    ![Caixa de diálogo de Aviso de Desempenho dizendo que o HAXM não está pronto](hardware-acceleration-images/win/11-perf-warn.png)

   Se uma caixa de diálogo de **Aviso de Desempenho** como essa for mostrada, consulte os [Avisos de Desempenho](~/android/deploy-test/debugging/android-sdk-emulator/troubleshooting.md#perfwarn) para identificar a causa e resolva o problema subjacente.

3. Selecione uma imagem **x86** (por exemplo, **VisualStudio\_android-23\_x86\_phone**), clique em **Iniciar** e, em seguida, clique em **Inicializar**:

    ![Iniciando o Emulador do SDK do Android com uma imagem de dispositivo virtual padrão](hardware-acceleration-images/win/02-start-default-avd.png)

4. Observe a janela de diálogo **Iniciando o Emulador do Android** enquanto o emulador é iniciado. Se o HAXM estiver instalado, você verá a mensagem **O HAX está funcionando e o emulador é executado no modo virtual rápido**, conforme mostrada nesta captura de tela:

    ![O HAXM é exibido como disponível na caixa de diálogo Iniciando o Emulador do Android](hardware-acceleration-images/win/03-haxm-detected.png)

   Se você não vir essa mensagem, provavelmente o HAXM não está instalado. Por exemplo, esta é uma captura de tela de uma mensagem que poderá ser exibida se o HAXM não estiver disponível:

    ![O HAXM não está disponível neste computador](hardware-acceleration-images/win/04-haxm-error.png)

   Se HAXM não estiver disponível em seu computador, siga as etapas na próxima seção para instalá-lo.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

1. Inicie o Gerenciador do emulador do Android clicando em **Ferramentas > Gerenciador do emulador do Android**:

    [![Local do item de menu do Gerenciador do Emulador do Android](hardware-acceleration-images/mac/01-avd-manager-menu-item-sml.png)](hardware-acceleration-images/mac/01-avd-manager-menu-item.png#lightbox)

2. Se uma caixa de diálogo de **Aviso de Desempenho** semelhante à seguinte for exibida, significa que o HAXM ainda não foi instalado ou configurado corretamente em seu computador:
   

    ![Caixa de diálogo de Aviso de Desempenho dizendo que o HAXM não está pronto](hardware-acceleration-images/mac/04-avd-warning.png)

   Se uma caixa de diálogo de **Aviso de Desempenho** como essa for mostrada, consulte os [Avisos de Desempenho](~/android/deploy-test/debugging/android-sdk-emulator/troubleshooting.md#perfwarn) para identificar a causa e resolva o problema subjacente.

3. Selecione a imagem de **x86** (por exemplo, **Android\_Accelerated\_x86**), clique em **Iniciar**, em seguida, clique em **Inicializar**:

    [![Iniciando o Emulador do SDK do Android com uma imagem de dispositivo virtual padrão](hardware-acceleration-images/mac/02-start-default-avd-sml.png)](hardware-acceleration-images/mac/02-start-default-avd.png#lightbox)

3. Observe a janela de diálogo **Iniciando o Emulador do Android** enquanto o emulador é iniciado. Se o HAXM estiver instalado, você verá a mensagem **O HAX está funcionando e o emulador é executado no modo virtual rápido**, conforme mostrada nesta captura de tela:

    ![O HAXM é exibido como disponível na caixa de diálogo Iniciando o Emulador do Android](hardware-acceleration-images/mac/03-haxm-detected.png)

   Se o HAXM não estiver disponível no seu computador (por exemplo, se você vir uma mensagem de erro como _Verifique se o Intel HAXM está corretamente instalado e utilizável_), siga as etapas na próxima seção para instalar o HAXM.


-----

<a name="install-haxm" />

## <a name="installing-haxm"></a>Instalando o HAXM

Se o emulador não iniciar, talvez seja necessário instalar o HAXM manualmente. Pacotes de instalação do HAXM para Windows e macOS estão disponíveis na página [Intel Hardware Accelerated Execution Manager](https://software.intel.com/en-us/android/articles/intel-hardware-accelerated-execution-manager). Use as etapas a seguir para baixar e instalar o HAXM manualmente:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. No site da Intel, baixe a versão mais recente do [mecanismo de virtualização do HAXM](https://software.intel.com/en-us/android/articles/intel-hardware-accelerated-execution-manager/) para Windows. A vantagem de baixar o instalador do HAXM diretamente do site da Intel é a segurança de que você está usando a versão mais recente.
   

   Como alternativa, você pode usar o Gerenciador de SDK para baixar o instalador do HAXM (no Gerenciador do SDK, clique em **Ferramentas > Extras > Acelerador do Emulador Intel x86 (Instalador do HAXM)**). O SDK do Android normalmente baixa o instalador do HAXM no seguinte local:

   **C:\\Arquivos de Programas (x86)\\Android\\android-sdk\\extras\\intel\\Hardware\_Accelerated\_Execution\_Manager**

   Observe que o Gerenciador de SDK não instala o HAXM, ele simplesmente baixa o instalador do HAXM no local acima; você ainda precisará iniciá-lo manualmente.

2. Execute o arquivo **intelhaxm-android.exe** para iniciar o instalador do HAXM. Aceite os valores padrão nas caixas de diálogo do instalador:

   ![Janela de instalação do Intel Hardware Accelerated Execution Manager](hardware-acceleration-images/win/05-haxm-installer.png)

Se a seguinte caixa de diálogo de erro for exibida (_Este computador não dá suporte à VT-x (Tecnologia de Virtualização da Intel) ou está sendo usado exclusivamente pelo Hyper-V_), o Hyper-V deverá ser desabilitado antes de instalar o HAXM:

![O HAXM não pode ser instalado devido a conflito com o Hyper-V](hardware-acceleration-images/win/06-cant-install-haxm.png)

A próxima seção explica como desabilitar o Hyper-V.

<a name="disable-hyperv" />

## <a name="disabling-hyper-v"></a>Desabilitando o Hyper-V

Se você estiver usando o Windows com o Hyper-V habilitado, será necessário desabilitá-lo e reinicializar o computador para instalar e usar o HAXM. Você pode desabilitar o Hyper-V no Painel de Controle seguindo estas etapas:

1. Na caixa do Windows Search, digite **Programas e**, em seguida, clique no resultado da pesquisa **Programas e Recursos**.

2. Na caixa de diálogo **Programas e Recursos** do Painel de Controle, clique em **Ativar ou desativar recursos do Windows**:

    ![Ativar ou desativar recursos do Windows](hardware-acceleration-images/win/07-turn-windows-features.png)

3. Desmarque o **Hyper-V** e reinicie o computador:

    ![Desabilitando o Hyper-V na caixa de diálogo Recursos do Windows](hardware-acceleration-images/win/08-uncheck-hyper-v.png)

Como alternativa, você pode usar o seguinte cmdlet do Powershell para desabilitar o Hyper-v:

`Disable-WindowsOptionalFeature -Online -FeatureName Microsoft-Hyper-V-Hypervisor`

O Intel HAXM e o Microsoft Hyper-V não podem estar ativos ao mesmo tempo. Infelizmente, não há atualmente nenhuma maneira de mudar entre o Hyper-V e o HAXM sem reiniciar o computador. Se quiser usar o [Emulador do Visual Studio para Android](~/android/deploy-test/debugging/visual-studio-android-emulator.md) (que depende do Hyper-V), você não conseguirá usar o Emulador do SDK do Android sem reinicializar. Uma maneira de usar o Hyper-V e o HAXM é criar uma configuração de inicialização múltipla, conforme explicado em [Criando uma entrada de inicialização sem hipervisor](https://blogs.msdn.microsoft.com/virtual_pc_guy/2008/04/14/creating-a-no-hypervisor-boot-entry/).

Em alguns casos, as etapas acima não funcionarão para desabilitar o Hyper-V caso o Device Guard e o Credential Guard estiverem habilitados. Se não for possível desabilitar o Hyper-V (ou ele parecer estar desabilitado, mas ainda ocorrer falha na instalação do HAXM), siga as etapas da próxima seção para desabilitar o Device Guard e o Credential Guard.

<a name="disable-devguard" />

## <a name="disabling-device-guard"></a>Desabilitando o Device Guard

O Device Guard e o Credential Guard podem impedir que o Hyper-V seja desabilitado em computadores Windows. Normalmente, isso é um problema para computadores com domínio associado que são configurados e controlados por uma organização proprietária.
No Windows 10, use as etapas a seguir para ver se o **Device Guard** está em execução:

1. No **Windows Search**, digite **Informações do sistema** para iniciar o aplicativo **Informações do Sistema**.

2. No **Resumo do Sistema**, verifique se **Segurança com base em Virtualização de Device Guard** está presente e está no estado **Em execução**:

   [![O Device Guard está presente e em execução](hardware-acceleration-images/win/09-device-guard-sml.png)](hardware-acceleration-images/win/09-device-guard.png#lightbox)

Se o Device Guard estiver habilitado, use as seguintes etapas para desabilitá-lo:

1. Verifique se o **Hyper-V** está desabilitado (em **Ativar ou desativar recursos do Windows**) conforme descrito na seção anterior.

2. Na caixa do Windows Search, digite **gpedit** e selecione o resultado da pesquisa **Editar política de grupo**. Isso inicializa o **Editor de Política de Grupo Local**.

3. No **Editor de Política de Grupo Local**, navegue até **Configuração do Computador > Modelos Administrativos > Sistema > Device Guard**:

   [![Device Guard no Editor de Política de Grupo Local](hardware-acceleration-images/win/10-group-policy-editor-sml.png)](hardware-acceleration-images/win/10-group-policy-editor.png#lightbox)

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

## <a name="hardware-acceleration-and-amd-cpus"></a>Aceleração de hardware e CPUs AMD

Como o Google Android Emulator atualmente oferece suporte à aceleração de hardware AMD [somente no Linux](https://developer.android.com/studio/run/emulator-acceleration.html#dependencies), a aceleração de hardware não está disponível para computadores baseados em AMD que executam o Windows.


# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

1. No site da Intel, baixe a versão mais recente do [mecanismo de virtualização do HAXM](https://software.intel.com/en-us/android/articles/intel-hardware-accelerated-execution-manager/) para macOS.

2. Execute o instalador do HAXM. Aceite os valores padrão nas caixas de diálogo do instalador:

   [![Janela de instalação do Intel Hardware Accelerated Execution Manager](hardware-acceleration-images/mac/05-haxm-installer-sml.png)](hardware-acceleration-images/win/05-haxm-installer.png#lightbox)

-----
