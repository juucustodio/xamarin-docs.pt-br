---
title: Aceleração de hardware para o desempenho do emulador (Hyper-V e HAXM)
description: Este artigo explica como usar os recursos de aceleração de hardware do computador para maximizar o desempenho do Android Emulator.
zone_pivot_groups: platform
ms.prod: xamarin
ms.assetid: 915874C3-2F0F-4D83-9C39-ED6B90BB2C8E
ms.technology: xamarin-android
author: jondouglas
ms.author: jodou
ms.date: 02/13/2020
ms.openlocfilehash: 4956e0dc4961065650fc9289884c24cd5b5f702c
ms.sourcegitcommit: 6d347e1d7641ac1d2b389fb1dc7a6882a08f7c00
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/08/2020
ms.locfileid: "91851529"
---
# <a name="hardware-acceleration-for-emulator-performance-hyper-v--haxm"></a>Aceleração de hardware para o desempenho do emulador (Hyper-V e HAXM)

_Este artigo explica como usar os recursos de aceleração de hardware do computador para maximizar o desempenho do Android Emulator._

Com o Visual Studio, os desenvolvedores podem testar e depurar mais facilmente seus aplicativos Xamarin.Android usando o Android Emulator em situações em que um dispositivo Android não está disponível ou é impraticável.
No entanto, o Android Emulator será executado muito lentamente se a aceleração de hardware não estiver disponível no computador em que será executado. Você pode melhorar significativamente o desempenho do Android Emulator usando imagens de dispositivo virtual x86 especiais, em conjunto com os recursos de virtualização do computador.

| Cenário    | HAXM        | WHPX       | Hypervisor.Framework |
| ----------- | ----------- | -----------| ----------- |
| Você tem um Processador Intel | X | X | X |
| Você tem um Processador AMD   |   | X |   |
| Você deseja dar suporte ao Hyper-V |   | X |   |
| Você deseja dar suporte à virtualização aninhada |   | Limitado |   |
| Você deseja usar tecnologias como o Docker  |   | X | X |

::: zone pivot="windows"

## <a name="accelerating-android-emulators-on-windows"></a>Acelerar emuladores de Android no Windows

As tecnologias de virtualização a seguir estão disponíveis para acelerar o Android Emulator:

1. **Hyper-V e WHPX (Plataforma de Hipervisor do Windows) da Microsoft**.
   O [Hyper-V](/virtualization/hyper-v-on-windows/) é um recurso de virtualização do Windows que torna possível executar sistemas de computador virtualizados em um computador host físico.

2. **HAXM (Hardware Accelerated Execution Manager) da Intel**.
   O HAXM é um mecanismo de virtualização para computadores que executam CPUs Intel.

Para obter a melhor experiência no Windows, recomendamos usar o WHPX para acelerar o Android Emulator. Caso o WHPX não esteja disponível em seu computador, o HAXM poderá ser usado. A aceleração de hardware será usada automaticamente pelo Android Emulator se os seguintes critérios forem atendidos:

- A aceleração de hardware está disponível e habilitada em seu computador de desenvolvimento.

- O emulador está executando uma imagem do sistema criada para um dispositivo virtual com base em **x86**.

> [!IMPORTANT]
> Você não pode executar um emulador com aceleração de VM dentro de outra VM, como uma VM hospedada por VirtualBox, VMware ou Docker. Execute o Android Emulator [diretamente no hardware do sistema](https://developer.android.com/studio/run/emulator-acceleration.html#extensions).

Para obter informações sobre como iniciar e depurar com o Android Emulator, consulte [Depurando no Android Emulator](~/android/deploy-test/debugging/debug-on-emulator.md).

<a name="hyper-v-win"></a>

## <a name="accelerating-with-hyper-v"></a>Acelerar com Hyper-V

Antes de habilitar o Hyper-V, leia a seção a seguir para verificar se seu computador tem suporte para ele.

### <a name="verifying-support-for-hyper-v"></a>Verificar o suporte para Hyper-V

O Hyper-V é executado na Plataforma de Hipervisor do Windows. Para usar o Android Emulator com o Hyper-V, o computador precisa atender aos seguintes critérios para dar suporte à Plataforma de Hipervisor do Windows:

- O hardware do computador deve atender aos seguintes requisitos:

  - Uma CPU Intel ou AMD Ryzen de 64 bits com SLAT (Conversão de Endereços de Segundo Nível).
  - Suporte de CPU para a extensão do modo de monitor da VM (VT-c em CPUs Intel).
  - Mínimo de 4 GB de memória.

- Na BIOS do computador, os itens a seguir devem estar habilitados:

  - Tecnologia de virtualização (pode ter um rótulo diferente dependendo do fabricante da placa-mãe).
  - Prevenção de Execução de Dados Baseada em Hardware.

- O computador deve estar atualizado para com a atualização do Windows 10 de abril de 2018 (build 1803) ou posterior. Você pode verificar sua versão do Windows está atualizada executando as seguintes etapas:

  1. Na caixa de pesquisa do Windows, digite **Sobre**.
  2. Selecione **Sobre seu PC** nos resultados da pesquisa.
  3. Role para baixo na caixa de diálogo **sobre** para a seção **especificações do Windows** .
  4. Verifique se a **versão** é, pelo menos, a 1803:

      [![Especificações do Windows](hardware-acceleration-images/win/01-about-windows-w10-sml.png)](hardware-acceleration-images/win/01-about-windows-w10.png#lightbox)

Para verificar se o hardware e o software do computador são compatíveis com Hyper-V, abra um prompt de comando e digite o seguinte comando:

```cmd
systeminfo
```

Se todos os requisitos listados do Hyper-V tiverem um valor de **Sim**, o computador poderá dar suporte a Hyper-V. Por exemplo:

[![Saída de SystemInfo de exemplo](hardware-acceleration-images/win/02-systeminfo-w158-sml.png)](hardware-acceleration-images/win/02-systeminfo-w158.png#lightbox)

### <a name="enabling-hyper-v-acceleration"></a>Habilitar a aceleração do Hyper-V

Se o computador atender aos critérios acima, use as seguintes etapas para acelerar o Android Emulator com o Hyper-V:

1. Insira **recursos do windows** na caixa de pesquisa do Windows e selecione **Ativar ou desativar recursos do Windows** nos resultados da pesquisa. Na caixa de diálogo **Recursos do Windows**, habilite **Hyper-V** e **Plataforma de Hipervisor do Windows**:

    [![Habilitando o Hyper-V e a plataforma de hipervisor do Windows](hardware-acceleration-images/win/03-hyper-v-settings-w158-sml.png)](hardware-acceleration-images/win/03-hyper-v-settings-w158.png#lightbox)

   Depois de fazer essas alterações, reinicie o computador.
   
> [!IMPORTANT]
>
> Na atualização de outubro de 2018 do Windows 10 (RS5) e posteriores, será necessário habilitar somente o Hyper-V, já que ele usará o WHPX (Plataforma de Hipervisor do Windows) automaticamente.

2. **Instale [o Visual Studio 15.8 ou posterior](https://visualstudio.microsoft.com/vs/)** (esta versão do Visual Studio dá suporte ao IDE para execução do Android Emulator com Hyper-V).

3. **Instale o pacote 27.2.7 ou posterior do Android Emulator**. Para instalar este pacote, navegue até **Ferramentas > Android > Gerenciador de SDK do Android** no Visual Studio. Selecione a guia **ferramentas** e verifique se a versão do Android Emulator é pelo menos 27.2.7. Também verifique se a versão do Android SDK Tools é a 26.1.1 ou posterior:

    [![Caixa de diálogo ferramentas e SDKs do Android](hardware-acceleration-images/win/04-sdk-manager-w158-sml.png)](hardware-acceleration-images/win/04-sdk-manager-w158.png#lightbox)

Ao criar um dispositivo virtual (consulte [Gerenciando dispositivos virtuais com o Android Device Manager](~/android/get-started/installation/android-emulator/device-manager.md)), certifique-se de selecionar uma imagem de sistema baseada em **x86**. Se você usar uma imagem de sistema baseada em ARM, o dispositivo virtual não será acelerado e será executado lentamente.

Agora, o Hyper-V deve ser habilitado e você pode executar o emulador do Android acelerado.

## <a name="accelerating-with-haxm"></a>Acelerar com HAXM

Se o computador não oferecer suporte ao Hyper-V, você poderá usar o HAXM para acelerar o emulador do Android. Você deve [desabilitar o Device Guard](~/android/get-started/installation/android-emulator/troubleshooting.md?tabs=vswin#disable-devguard) se quiser usar o HAXM.

### <a name="verifying-haxm-support"></a>Verificar o suporte para HAXM

Para determinar se o hardware dá suporte ao HAXM, siga as etapas em [Meu processador dá suporte à Tecnologia de Virtualização da Intel?](https://www.intel.com/content/www/us/en/support/processors/000005486.html).
Se o hardware der suporte ao HAXM, você poderá verificar se o HAXM já está instalado usando as seguintes etapas:

1. Abra uma janela de prompt de comando e digite o seguinte comando:

    ```cmd
    sc query intelhaxm
    ```

2. Examine a saída para ver se o processo HAXM está em execução. Se ele estiver, você deverá ver lista de saída do estado `intelhaxm` como `RUNNING`. Por exemplo:

    ![Saída do comando de consulta de sc quando o HAXM está disponível](hardware-acceleration-images/win/05-sc_query-w158.png)

   Se `STATE` não estiver definido como `RUNNING`, o HAXM não está instalado.

Se o computador tiver suporte para HAXM, mas o HAXM não estiver instalado, siga as etapas na próxima seção para instalá-lo.

<a name="install-haxm-win"></a>

### <a name="installing-haxm"></a>Instalando o HAXM

Os pacotes de instalação do HAXM para Windows estão disponíveis na página de versões do GitHub do [Intel hardware Accelerated Execution Manager](https://github.com/intel/haxm/releases) . Use as etapas a seguir para baixar e instalar o HAXM:

1. No site da Intel, baixe a versão mais recente do [mecanismo de virtualização do HAXM](https://github.com/intel/haxm/releases) para o Windows. A vantagem de baixar o instalador do HAXM diretamente do site da Intel é que você pode ter certeza de usar a versão mais recente.

2. Execute o arquivo **intelhaxm-android.exe** para iniciar o instalador do HAXM. Aceite os valores padrão nas caixas de diálogo do instalador:

   ![Janela de instalação do Intel Hardware Accelerated Execution Manager](hardware-acceleration-images/win/06-haxm-installer.png)

Ao criar um dispositivo virtual (consulte [Gerenciando dispositivos virtuais com o Android Device Manager](~/android/get-started/installation/android-emulator/device-manager.md)), certifique-se de selecionar uma imagem de sistema baseada em **x86**. Se você usar uma imagem de sistema baseada em ARM, o dispositivo virtual não será acelerado e será executado lentamente.

## <a name="troubleshooting"></a>Solução de problemas

Para obter ajuda para solucionar problemas de aceleração de hardware, confira o guia de [solução de problemas](~/android/get-started/installation/android-emulator/troubleshooting.md?tabs=vswin#accel-issues-win) do Android Emulator.

::: zone-end
::: zone pivot="macos"

## <a name="accelerating-android-emulators-on-macos"></a>Acelerar emuladores de Android no macOS

As tecnologias de virtualização a seguir estão disponíveis para acelerar o Android Emulator:

1. **Framework de Hipervisor da Apple**.
   O [Hipervisor](https://developer.apple.com/documentation/hypervisor) é um recurso do macOS 10.10 e de versões posteriores que possibilita executar máquinas virtuais em um Mac.

2. **HAXM (Hardware Accelerated Execution Manager) da Intel**.
   [HAXM](https://software.intel.com/articles/intel-hardware-accelerated-execution-manager-intel-haxm) é um mecanismo de virtualização para computadores que executam CPUs Intel.

Recomendamos usar o Framework de Hipervisor para acelerar o Android Emulator. Se o Framework de Hipervisor não estiver disponível em seu Mac, o HAXM poderá ser usado. A aceleração de hardware será usada automaticamente pelo Android Emulator se os seguintes critérios forem atendidos:

- A aceleração de hardware está disponível e habilitada no computador de desenvolvimento.

- O emulador está executando uma imagem do sistema criada para um dispositivo virtual com base em **x86**.

> [!IMPORTANT]
>
> Você não pode executar um emulador com aceleração de VM dentro de outra VM, como uma VM hospedada por VirtualBox, VMware ou Docker. Execute o Android Emulator [diretamente no hardware do sistema](https://developer.android.com/studio/run/emulator-acceleration.html#extensions).

Para obter informações sobre como iniciar e depurar com o Android Emulator, consulte [Depurando no Android Emulator](~/android/deploy-test/debugging/debug-on-emulator.md).

<a name="hypervisor"></a>

## <a name="accelerating-with-the-hypervisor-framework"></a>Acelerar o com o Framework de Hipervisor

Para usar o Android Emulator com o Framework de Hipervisor, seu Mac precisa atender aos seguintes critérios:

- O Mac precisa estar executando o macOS 10.10 ou posterior.

- A CPU do Mac precisa ser capaz de dar suporte ao Framework de Hipervisor.

Caso o Mac atenda a esses critérios, o Android Emulator usará automaticamente o Framework de Hipervisor para aceleração. Se não tiver certeza de que o Framework do Hipervisor tenha suporte em seu Mac, confira o guia de [Solução de problemas](~/android/get-started/installation/android-emulator/troubleshooting.md?tabs=vsmac#hypervisor-issues) para ver maneiras de verificar se seu Mac tem suporte para o Hipervisor.

Se o Framework de Hipervisor não tiver suporte em seu Mac, você poderá usar o HAXM para acelerar o Android Emulator (descrito a seguir).

<a name="haxm-mac"></a>

## <a name="accelerating-with-haxm"></a>Acelerar com HAXM

Se o Mac não tiver suporte para o Framework de Hipervisor (ou se você estiver usando uma versão do macOS anterior à 10.10), você poderá usar o **Hardware Accelerated Execution Manager da Intel** ([HAXM](https://software.intel.com/articles/intel-hardware-accelerated-execution-manager-intel-haxm)) para acelerar o Android Emulator.

Para usar o Android Emulator com o HAXM pela primeira vez, verifique se o HAXM está instalado e disponível para uso do Android Emulator.

### <a name="verifying-haxm-support"></a>Verificar o suporte para HAXM

Você pode verificar se o HAXM já está instalado usando as seguintes etapas:

1. Abra um Terminal e insira o seguinte comando:

    ```bash
    ~/Library/Developer/Xamarin/android-sdk-macosx/tools/emulator -accel-check
    ```

   Esse comando presume que o SDK do Android está instalado na localização padrão do **~/Library/Developer/Xamarin/android-sdk-macosx**. Se não estiver, modifique o caminho acima para a localização do SDK do Android no Mac.

2. Se o HAXM estiver instalado, o comando acima retornará uma mensagem com um resultado semelhante ao seguinte:

    ```bash
    HAXM version 7.2.0 (3) is installed and usable.
    ```

   Se o HAXM *não* estiver instalado, será retornada uma mensagem semelhante à saída a seguir:

    ```bash
    HAXM is not installed on this machine (/dev/HAX is missing).
    ```

Se HAXM não estiver instalado, siga as etapas na próxima seção para instalá-lo.

<a name="install-haxm-mac"></a>

### <a name="installing-haxm"></a>Instalando o HAXM

Pacotes de instalação do HAXM para macOS estão disponíveis na página [Hardware Accelerated Execution Manager da Intel](https://software.intel.com/android/articles/intel-hardware-accelerated-execution-manager). Use as etapas a seguir para baixar e instalar o HAXM:

1. No site da Intel, baixe a versão mais recente do [mecanismo de virtualização do HAXM](https://software.intel.com/android/articles/intel-hardware-accelerated-execution-manager/) para o macOS.

2. Execute o instalador do HAXM. Aceite os valores padrão nas caixas de diálogo do instalador:

   [![Janela de instalação do Intel Hardware Accelerated Execution Manager](hardware-acceleration-images/mac/01-haxm-installer-sml.png)](hardware-acceleration-images/mac/01-haxm-installer.png#lightbox)

## <a name="troubleshooting"></a>Solução de problemas

Para obter ajuda para solucionar problemas de aceleração de hardware, confira o guia de [solução de problemas](~/android/get-started/installation/android-emulator/troubleshooting.md?tabs=vsmac#accel-issues-mac) do Android Emulator.

::: zone-end

## <a name="related-links"></a>Links Relacionados

- [Run Apps on the Android Emulator](https://developer.android.com/studio/run/emulator) (Executar aplicativos no Android Emulator)
