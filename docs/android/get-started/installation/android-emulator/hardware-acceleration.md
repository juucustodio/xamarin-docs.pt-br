---
title: Aceleração de Hardware do Android Emulator
description: Como preparar seu computador para obter o desempenho máximo do Google Android Emulator
ms.prod: xamarin
ms.assetid: 915874C3-2F0F-4D83-9C39-ED6B90BB2C8E
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 05/10/2018
ms.openlocfilehash: b5c20eb9f40bb4c4981d6b60b9fd4bc75fd29336
ms.sourcegitcommit: b0a1c3969ab2a7b7fe961f4f470d1aa57b1ff2c6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/10/2018
---
# <a name="android-emulator-hardware-acceleration"></a>Aceleração de Hardware do Android Emulator

Sem aceleração de hardware, o Google Android Emulator é extremamente lento. É possível melhorar drasticamente o desempenho do Google Android Emulator usando imagens especiais de hardware emulador direcionadas para hardware x86 e uma das duas tecnologias de virtualização:

1. **Hyper-V e Plataforma de Hipervisor da Microsoft** &ndash; o Hyper-V é um componente de virtualização disponível no Windows 10 que permite a execução de sistemas de computadores virtualizados em um host físico. Essa é a tecnologia de virtualização recomendada para as imagens aceleradas do Google Android Emulator. Para saber mais sobre o Hyper-V, consulte o [Guia do Hyper-V no Windows 10](https://docs.microsoft.com/en-us/virtualization/hyper-v-on-windows/).
2. **HAXM (Hardware Accelerated Execution Manager) da Intel** &ndash; mecanismo de virtualização para computadores que executam CPUs da Intel. Ele é o mecanismo de virtualização recomendado para desenvolvedores que não podem usar o Hyper-V.

O Gerenciador de SDK do Android usará automaticamente a aceleração de hardware quando disponível na execução de uma imagem de emulador, especificamente para um dispositivo virtual baseado em **x86** (conforme descrito em [Configuração e uso](~/android/deploy-test/debugging/android-sdk-emulator/index.md)).

## <a name="hyper-v-overview"></a>Visão geral do Hyper-V

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![](~/media/shared/preview.png)

> [!NOTE]
> No momento, o suporte do Hyper-V está em versão prévia.

Nós recomendamos os desenvolvedores usando o Windows 10 (atualização de abril de 2018) a usar o Microsoft Hyper-V. Com as Ferramentas do Visual Studio para Xamarin, os desenvolvedores podem realizar testes e depurações dos aplicativos Xamarin.Android com mais facilidade em situações de indisponibilidade ou de inviabilidade do uso de dispositivos Android.

Para começar a usar o Hyper-V e o Google Android Emulator:

1. **Atualize o sistema operacional com a Atualização de abril de 2018 do Windows 10 (build 1803)** &ndash; Para verificar qual a sua versão do Windows, clique na barra de pesquisa da Cortana e digite **Sobre**. Selecione **Sobre seu PC** nos resultados da pesquisa. Role a caixa de diálogo **Sobre** para baixo até a seção **Especificações do Windows**. O **Versão** deve ser pelo menos 1803:

    [![Especificações do Windows](hardware-acceleration-images/win/12-about-windows.w10-sml.png)](hardware-acceleration-images/win/12-about-windows.w10.png#lightbox)

2. **Habilite o Hyper-V e a Plataforma de Hipervisor do Windows** &ndash; Na barra de pesquisa da Cortana, digite **Ligar ou desligar recursos do Windows**.
   Role a caixa de diálogo **Recursos do Windows** para baixo e verifique se a opção **Plataforma de Hipervisor do Windows** está habilitada.

    [![Hyper-V e Plataforma de Hipervisor do Windows habilitados](hardware-acceleration-images/win/13-windows-features.w10-sml.png)](hardware-acceleration-images/win/13-windows-features.w10.png#lightbox)

    Talvez seja necessário reiniciar o Windows depois de habilitar o Hyper-V e a Plataforma de Hipervisor do Windows.

3. **Instale o [Visual Studio 15.8 Preview 1](https://aka.ms/hyperv-emulator-dl)** &ndash; Essa versão do Visual Studio dá suporte ao IDE para iniciar o Google Android Emulator com suporte para o Hyper-V.

4. **Instale o pacote do Google Android Emulator 27.2.7 ou superior** &ndash; Para instalar esse pacote, acesse **Ferramentas > Android > Gerenciador de SDK do Android** no Visual Studio. Selecione a guia **Ferramentas** e verifique se a versão do componente Android Emulator é pelo menos a 27.2.7.

    [![Caixa de diálogo SDKs e Ferramentas do Android](hardware-acceleration-images/win/14-sdk-manager.w158-sml.png)](hardware-acceleration-images/win/14-sdk-manager.w158.png#lightbox)

5. Se a versão do Android Emulator for inferior a 27.3.1, siga a etapa de solução alternativa adicional explicada em **Problemas conhecidos** (próxima sessão).


### <a name="known-issues"></a>Problemas Conhecidos

-   Se a versão do emulador for pelo menos 27.2.7, mas inferior a 27.3.1, a seguinte solução alternativa será necessária para usar o Hyper-V:
    1.  Na pasta **C:\\Usuários\\_nome de usuário_\\.android**, crie um arquivo chamado **advancedFeatures.ini** caso ele ainda não exista.
    2.  Adicione a seguinte linha a **advancedFeatures.ini**:
        ```
        WindowsHypervisorPlatform = on
        ```

-   O desempenho pode ser reduzido ao usar determinados processadores baseados na AMD e na Intel.

-   O aplicativo Android pode levar uma quantidade anormal de tempo para carregar na implantação.

-   O erro de acesso à MMIO pode impedir de maneira intermitente a inicialização do Android Emulator. A reinicialização do emulador deve resolver o problema.


# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

O suporte ao Hyper-V exige o Windows 10. Veja [Requisitos do Hyper-V](https://docs.microsoft.com/en-us/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v#check-requirements) para obter mais detalhes.

-----

## <a name="haxm-overview"></a>Visão geral do HAXM

O HAXM é um mecanismo de virtualização assistido por hardware (hipervisor) que usa a VT (tecnologia de virtualização) da Intel para acelerar a emulação de aplicativos Android em um computador host. Em conjunto com as imagens de emulador x86 do Android fornecidas pela Intel e com o Gerenciador de SDK do Android oficial, o HAXM permite uma emulação mais rápida do Android em sistemas habilitados para VT. 

Caso esteja desenvolvendo em um computador com CPU Intel com funcionalidades de VT, aproveite o HAXM para acelerar significativamente o Google Android Emulator (se não tiver certeza se a CPU é compatível com VT, veja [Determine se o processador é compatível com a tecnologia de virtualização da Intel](https://www.intel.com/content/www/us/en/support/processors/000005486.html) (em inglês)).

> [!NOTE]
> Não é possível executar um emulador acelerado por VM dentro de outra VM, como uma VM hospedada por VirtualBox, VMWare ou Docker. Você deve executar Google Android Emulator [diretamente em seu hardware de sistema](https://developer.android.com/studio/run/emulator-acceleration.html#extensions).

Antes de usar o Google Android Emulator pela primeira vez, verifique se o HAXM está instalado e disponível para o Google Android Emulator.

### <a name="verifying-haxm-installation"></a>Verificando a instalação do HAXM

Você pode verificar se o HAXM está disponível olhando a janela **Iniciando o Emulador do Android** quando o emulador é iniciado. Para iniciar o Google Android Emulator, siga as etapas abaixo:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. Inicie o Gerenciador do Emulador do Android clicando em **Ferramentas > Android > Gerenciador do Emulador do Android**:

    [![Local do item de menu do Gerenciador do Emulador do Android](hardware-acceleration-images/win/01-avd-manager-menu-item-sml.png)](hardware-acceleration-images/win/01-avd-manager-menu-item.png#lightbox)

2. Se uma caixa de diálogo de **Aviso de Desempenho** semelhante à seguinte for exibida, significa que o HAXM ainda não foi instalado ou configurado corretamente em seu computador:
   

    ![Caixa de diálogo de Aviso de Desempenho dizendo que o HAXM não está pronto](hardware-acceleration-images/win/11-perf-warn.png)

   Se uma caixa de diálogo de **Aviso de Desempenho** como essa for mostrada, consulte os [Avisos de Desempenho](~/android/deploy-test/debugging/android-sdk-emulator/troubleshooting.md#perfwarn) para identificar a causa e resolva o problema subjacente.

3. Selecione uma imagem **x86** (por exemplo, **VisualStudio\_android-23\_x86\_phone**), clique em **Iniciar** e, em seguida, clique em **Inicializar**:

    ![Iniciando o Google Android Emulator com uma imagem de dispositivo virtual padrão](hardware-acceleration-images/win/02-start-default-avd.png)

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

    [![Iniciando o Google Android Emulator com uma imagem de dispositivo virtual padrão](hardware-acceleration-images/mac/02-start-default-avd-sml.png)](hardware-acceleration-images/mac/02-start-default-avd.png#lightbox)

3. Observe a janela de diálogo **Iniciando o Emulador do Android** enquanto o emulador é iniciado. Se o HAXM estiver instalado, você verá a mensagem **O HAX está funcionando e o emulador é executado no modo virtual rápido**, conforme mostrada nesta captura de tela:

    ![O HAXM é exibido como disponível na caixa de diálogo Iniciando o Emulador do Android](hardware-acceleration-images/mac/03-haxm-detected.png)

   Se o HAXM não estiver disponível no seu computador (por exemplo, se você vir uma mensagem de erro como _Verifique se o Intel HAXM está corretamente instalado e utilizável_), siga as etapas na próxima seção para instalar o HAXM.


-----

<a name="install-haxm" />

### <a name="installing-haxm"></a>Instalando o HAXM

Se o emulador não iniciar, talvez seja necessário instalar o HAXM manualmente. Pacotes de instalação do HAXM para Windows e macOS estão disponíveis na página [Intel Hardware Accelerated Execution Manager](https://software.intel.com/en-us/android/articles/intel-hardware-accelerated-execution-manager). Use as etapas a seguir para baixar e instalar o HAXM manualmente:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. No site da Intel, baixe a versão mais recente do [mecanismo de virtualização do HAXM](https://software.intel.com/en-us/android/articles/intel-hardware-accelerated-execution-manager/) para Windows. A vantagem de baixar o instalador do HAXM diretamente do site da Intel é a segurança de que você está usando a versão mais recente.
   

   Como alternativa, você pode usar o Gerenciador de SDK para baixar o instalador do HAXM (no Gerenciador do SDK, clique em **Ferramentas > Extras > Acelerador do Emulador Intel x86 (Instalador do HAXM)**). O SDK do Android normalmente baixa o instalador do HAXM no seguinte local:

   **C:\\Arquivos de Programas (x86)\\Android\\android-sdk\\extras\\intel\\Hardware\_Accelerated\_Execution\_Manager**

   Observe que o Gerenciador de SDK não instala o HAXM, ele simplesmente baixa o instalador do HAXM no local acima; você ainda precisará iniciá-lo manualmente.

2. Execute o arquivo **intelhaxm-android.exe** para iniciar o instalador do HAXM. Aceite os valores padrão nas caixas de diálogo do instalador:

   ![Janela de instalação do Intel Hardware Accelerated Execution Manager](hardware-acceleration-images/win/05-haxm-installer.png)

## <a name="hardware-acceleration-and-amd-cpus"></a>Aceleração de hardware e CPUs AMD

Como o Google Android Emulator atualmente oferece suporte à aceleração de hardware AMD [somente no Linux](https://developer.android.com/studio/run/emulator-acceleration.html#dependencies), a aceleração de hardware não está disponível para computadores baseados em AMD que executam o Windows.


# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

1. No site da Intel, baixe a versão mais recente do [mecanismo de virtualização do HAXM](https://software.intel.com/en-us/android/articles/intel-hardware-accelerated-execution-manager/) para macOS.

2. Execute o instalador do HAXM. Aceite os valores padrão nas caixas de diálogo do instalador:

   [![Janela de instalação do Intel Hardware Accelerated Execution Manager](hardware-acceleration-images/mac/05-haxm-installer-sml.png)](hardware-acceleration-images/win/05-haxm-installer.png#lightbox)

-----


## <a name="related-links"></a>Links relacionados

* [Run Apps on the Android Emulator](https://developer.android.com/studio/run/emulator) (Executar aplicativos no Android Emulator)
