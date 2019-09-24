---
title: Emparelhar com Mac para o desenvolvimento do Xamarin.iOS
description: Este guia descreve como usar o Emparelhar com Mac para conectar o Visual Studio 2019 a um host de build do Mac. Ele aborda como habilitar o acesso remoto no Mac, conectar-se ao Mac com o Visual Studio 2019, adicionar manualmente um host de build do Mac no computador Windows e muito mais.
ms.prod: xamarin
ms.assetid: 39DD7B3F-3E69-4E2A-B743-4C26AF613025
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 05/29/2018
ms.openlocfilehash: 9296fa60a454fe83f9ec9b383067576b67e5f037
ms.sourcegitcommit: 76f930ce63b193ca3f7f85f768b031e59cb342ec
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/23/2019
ms.locfileid: "71198549"
---
# <a name="pair-to-mac-for-xamarinios-development"></a>Emparelhar com o Mac para desenvolvimento no Xamarin.iOS

_Este guia descreve como usar a funcionalidade Emparelhar com Mac para conectar o Visual Studio 2019 a um host de build do Mac. As mesmas instruções se aplicam ao Visual Studio 2017._

## <a name="overview"></a>Visão geral

A criação de aplicativos do iOS nativos requer acesso às ferramentas de build da Apple, que são executadas somente em Mac. Por isso, o Visual Studio 2019 deve se conectar a um Mac acessível na rede para criar aplicativos Xamarin.iOS.

O recurso Emparelhar com Mac do Visual Studio 2019 descobre, conecta, autentica e lembra de hosts de build do Mac para que os desenvolvedores de iOS baseados no Windows possam trabalhar produtivamente.

O Emparelhar com Mac permite o seguinte fluxo de trabalho de desenvolvimento:

- Os desenvolvedores podem escrever código do Xamarin.iOS no Visual Studio 2019.

- O Visual Studio 2019 abre uma conexão de rede para um host de build do Mac e usa as ferramentas de build no computador para compilar e assinar o aplicativo do iOS.

- Não é necessário executar um aplicativo separado no Mac – o Visual Studio 2019 invoca o Mac compila com segurança via SSH.

- O Visual Studio 2019 é notificado das alterações assim que elas ocorrem. Por exemplo, quando um dispositivo iOS está conectado ao Mac ou se torna disponível na rede, a barra de ferramentas do iOS é atualizada instantaneamente.

- Várias instâncias do Visual Studio 2019 podem se conectar ao Mac simultaneamente.

- É possível usar a linha de comando do Windows para criar aplicativos iOS.

> [!NOTE]
>
> Antes de seguir as instruções neste guia, conclua as seguintes etapas:
>
> - Em um computador Windows, [instale o Visual Studio 2019](~/get-started/installation/windows.md)
> - Em um Mac, [instale o Xcode](https://itunes.apple.com/us/app/xcode/id497799835?mt=12) e o [Visual Studio para Mac](https://docs.microsoft.com/visualstudio/mac/installation)
>   - _Você deve abrir o Xcode manualmente após a instalação_ para que ele inclua componentes adicionais.
>
> Se preferir não instalar o Visual Studio para Mac, o Visual Studio 2019 poderá configurar automaticamente o host de build do Mac com Xamarin.iOS e Mono.
> Ainda será necessário instalar e executar o Xcode.
> Para obter mais informações, veja [Provisionamento automático do Mac](#automatic-mac-provisioning).

## <a name="enable-remote-login-on-the-mac"></a>Habilitar acesso remoto no Mac

Para configurar o host de build do Mac, primeiramente habilite o acesso remoto:

1. No Mac, abra as Preferências do Sistema no painel **Compartilhamento**.

2. Marque **Acesso Remoto** na lista **Serviço**.

    ![Habilitar Acesso Remoto](images/sharing.png "Habilitar Acesso Remoto")

    Verifique se está definido para permitir o acesso de **Todos os usuários** ou se o nome de usuário ou grupo do Mac está incluído na lista de usuários permitidos.

3. Se solicitado, configure o firewall do macOS.

    Se tiver definido o firewall do macOS para bloquear conexões de entrada, poderá ser preciso permitir ao `mono-sgen` receber conexões de entrada. Um alerta será exibido para avisá-lo, se for o caso.

4. Se estiver na mesma rede que o computador Windows, o Mac deverá ser detectável pelo Visual Studio 2019. Se o Mac ainda não estiver detectável, tente [adicionar manualmente um Mac](#manually-add-a-mac) ou examinar o [guia de solução de problemas](~/ios/get-started/installation/windows/connecting-to-mac/troubleshooting.md).

## <a name="connect-to-the-mac-from-visual-studio-2019"></a>Conectar ao Mac por meio do Visual Studio 2019

Agora que o acesso remoto está habilitado, conecte o Visual Studio 2019 ao Mac.

1. No Visual Studio 2019, abra um projeto existente do iOS ou crie um escolhendo **Arquivo > Novo > Projeto** e, em seguida, selecione um modelo de projeto do iOS.

2. Abra a caixa de diálogo **Emparelhar com Mac**.

    - Use o botão **Emparelhar com Mac** na barra de ferramentas do iOS:

      ![A barra de ferramentas do iOS com o botão Emparelhar com Mac realçado](images/ios-toolbar.png "A barra de ferramentas do iOS com o botão Emparelhar com Mac realçado")

    - Ou selecione **Ferramentas > iOS > Emparelhar com Mac**.

    - A caixa de diálogo **Emparelhar com Mac** exibe uma lista de todos os hosts de build disponíveis do Mac conectados anteriormente e atualmente:

      ![A caixa de diálogo Emparelhar com Mac](images/pairtomac.png "A caixa de diálogo Emparelhar com Mac")

3. Selecione um Mac na lista. Clique em **Conectar**.

4. Insira o nome de usuário e a senha.

    - Na primeira vez que se conectar a um Mac, você precisará inserir seu nome de usuário e senha do computador:

      ![Inserir um nome de usuário e uma senha para o Mac](images/auth.png "Inserir um nome de usuário e uma senha para o Mac")

      > [!TIP]
      > Ao fazer logon, use seu nome de usuário do sistema em vez do nome completo.

    - O Emparelhar com Mac usa essas credenciais para criar uma conexão SSH com o Mac. Se tiver êxito, uma chave será adicionada ao arquivo **authorized_keys** no Mac. As conexões subsequentes com o mesmo Mac farão logon automaticamente.

5. O Emparelhar com Mac configura automaticamente o Mac.

    [Desde a versão 15.6 do Visual Studio 2019](https://docs.microsoft.com/visualstudio/releasenotes/vs2017-relnotes#automatic-macos-provisioning), o Visual Studio 2019 instala ou atualiza o Mono e o Xamarin.iOS em um host de build conectado do Mac conforme necessário (observe que o Xcode ainda deve ser instalado manualmente). Para obter mais informações, veja [Provisionamento automático do Mac](#automatic-mac-provisioning).

6. Procure o ícone de status de conexão.

    - Quando o Visual Studio 2019 é conectado a um Mac, o item do Mac na caixa de diálogo **Emparelhar com Mac** exibe um ícone que indica que ele está conectado no momento:

      ![Um Mac conectado](images/connected.png "Um Mac conectado")

      Pode haver apenas um Mac conectado por vez.

      > [!TIP]
      > Clicar com o botão direito do mouse em qualquer Mac na lista **Emparelhar com Mac** exibe um menu de contexto que permite **Conectar...** , **Esquecer este Mac** ou **Desconectar**:
      >
      > ![Os menus de contexto do Emparelhar com Mac](images/contextmenu.png "Os menus de contexto do Emparelhar com Mac")
      >
      > Se escolher **Esquecer este Mac**, suas credenciais para o Mac selecionado serão esquecidas. Para reconectar-se ao Mac, você precisará inserir novamente seu nome de usuário e senha.

Se tiver feito o emparelhamento com êxito a um host de build do Mac, você estará pronto para criar aplicativos Xamarin.iOS no Visual Studio 2019. Consulte o guia [Introdução ao Xamarin.iOS para Visual Studio](~/ios/get-started/installation/windows/introduction-to-xamarin-ios-for-visual-studio.md).

Se ainda não tiver feito o emparelhamento com um Mac, tente [adicionar manualmente um Mac](#manually-add-a-mac) ou consulte o [guia de solução de problemas](~/ios/get-started/installation/windows/connecting-to-mac/troubleshooting.md).

## <a name="manually-add-a-mac"></a>Adicionar um Mac manualmente

Se não vir um Mac específico listado na caixa de diálogo **Emparelhar com Mac**, adicione-o manualmente:

1. Localize o endereço IP do Mac.

    - Abra **Preferências do Sistema > Compartilhamento > Acesso Remoto** no seu Mac:

      [![O endereço IP do Mac em Preferências do Sistema > Compartilhamento](images/sharing-ipaddress.png "O endereço IP do Mac em Preferências do Sistema > Compartilhamento")](images/sharing.png#lightbox)

    - Como alternativa, use a linha de comando. No Terminal, execute este comando:

      ```bash
      $ ipconfig getifaddr en0
      196.168.1.8
      ```

      Dependendo de sua configuração de rede, talvez seja necessário usar um nome de interface diferente de `en0`. Por exemplo: `en1`, `en2`, etc.

2. Na caixa de diálogo **Emparelhar com Mac** do Visual Studio 2019, selecione **Adicionar Mac...** :

    [![O botão Adicionar Mac na caixa de diálogo Emparelhar com Mac](images/addtomac.png "O botão Adicionar Mac na caixa de diálogo Emparelhar com Mac")](images/addtomac-large.png#lightbox)

3. Insira o endereço IP do Mac e clique em **Adicionar**:

    ![Inserir o endereço IP do Mac](images/enteripaddress.png "Inserir o endereço IP do Mac")

4. Insira o nome de usuário e a senha para o Mac:

    ![Inserir um nome de usuário e senha](images/auth.png "Inserir um nome de usuário e senha")

   > [!TIP]
   > Ao fazer logon, use seu nome de usuário do sistema em vez do nome completo.

5. Clique em **Logon** para conectar o Visual Studio 2019 ao Mac via SSH e adicioná-lo à lista de computadores conhecidos.

## <a name="automatic-mac-provisioning"></a>Provisionamento automático do Mac

Começando com o [Visual Studio 2019 versão 15.6](https://docs.microsoft.com/visualstudio/releasenotes/vs2017-relnotes#automatic-macos-provisioning), a função Emparelhar com Mac provisiona um Mac automaticamente com o software necessário para a criação de aplicativos Xamarin.iOS: Mono, Xamarin.iOS (a estrutura de software, não o IDE do Visual Studio para Mac) e várias ferramentas relacionadas a Xcode (mas não o Xcode em si).

> [!IMPORTANT]
>
> - O Emparelhar com Mac não instala o Xcode. É necessário instalá-lo manualmente no host de build do Mac. Ele é necessário para o desenvolvimento do Xamarin.iOS.
> - O provisionamento automático do Mac exige que o acesso remoto esteja habilitado no Mac, e o Mac deve ser acessível pela rede para o computador Windows. Consulte [Habilitar acesso remoto no Mac](#enable-remote-login-on-the-mac) para obter mais detalhes.
> - O provisionamento automático do Mac requer 3 GB de espaço livre no Mac para instalar o Xamarin.iOS.

O Emparelhar com Mac executa instalações/atualizações de software necessárias quando o Visual Studio 2017 está [se conectando ao Mac](#connect-to-the-mac-from-visual-studio-2019).

### <a name="mono"></a>Mono

O Emparelhar com Mac fará a verificação para certificar-se de que o Mono está instalado. Se não estiver instalado, o Emparelhar com Mac baixará e instalará a versão estável mais recente do Mono no Mac.

O progresso é indicado por vários prompts, conforme mostrado pelas seguintes capturas de tela (clique para ampliar):

||Verificação de instalação|Baixando|Instalando o
|---|---|---|---|
|Mono|[![Instalação do Mono ausente](images/mono-missing.png "Instalação do Mono ausente")](images/mono-missing-large.png#lightbox)|[![Baixando o Mono](images/mono-downloading.png "Baixando o Mono")](images/mono-downloading-large.png#lightbox)|[![Instalando o Mono](images/mono-installing.png "Instalando o Mono")](images/mono-installing-large.png#lightbox)|

### <a name="xamarinios"></a>Xamarin.iOS

O Emparelhar com Mac faz upgrade do Xamarin.iOS no Mac para corresponder à versão instalada no computador Windows.

> [!IMPORTANT]
> O Emparelhar com Mac não fará o downgrade do Xamarin.iOS no Mac de alfa/beta para estável. Se tiver o Visual Studio para Mac instalado, defina seu [canal de versão](https://docs.microsoft.com/visualstudio/mac/update) da seguinte maneira:
>
> - Se for usar o Visual Studio 2019, selecione o canal de atualizações **Estável** no Visual Studio para Mac.
> - Se for usar o Visual Studio 2019 Preview, selecione o canal de atualizações **Alfa** no Visual Studio para Mac.

O progresso é indicado por vários prompts, conforme mostrado pelas seguintes capturas de tela (clique para ampliar):

||Verificação de instalação|Baixando|Instalando o
|---|---|---|---|
|Xamarin.iOS|[![Instalação do Xamarin.iOS ausente](images/xamios-missing.png "Instalação do Xamarin.iOS ausente")](images/xamios-missing-large.png#lightbox)|[![Baixando o Xamarin.iOS](images/xamios-downloading.png "Baixando o Xamarin.iOS")](images/xamios-downloading-large.png#lightbox)|[![Instalando o Xamarin.iOS](images/xamios-installing.png "Instalando o Xamarin.iOS")](images/xamios-installing-large.png#lightbox)|

### <a name="xcode-tools-and-license"></a>Licença e ferramentas Xcode

O Emparelhar com Mac também fará a verificação para determinar se o Xcode foi instalado e se a licença foi aceita. Embora o Emparelhar com Mac não instale o Xcode, ele solicita a aceitação da licença, conforme mostrado nas seguintes capturas de tela (clique para ampliar):

||Verificação de instalação|Aceitação da licença|
|---|---|---|
|Xcode|[![Instalação do Xcode ausente](images/xcode-missing.png "Instalação do Xcode ausente")](images/xcode-missing-large.png#lightbox)|[![Licença do Xcode](images/xcode-license.png "Licença do Xcode")](images/xcode-license-large.png#lightbox)|

Adicionalmente, o Emparelhar com Mac instalará ou atualizará vários pacotes distribuídos com o Xcode. Por exemplo:

- **MobileDeviceDevelopment.pkg**
- **XcodeExtensionSupport.pkg**
- **MobileDevice.pkg**
- **XcodeSystemResources.pkg**

A instalação desses pacotes ocorre rapidamente e sem um prompt.

> [!NOTE]
> Essas ferramentas são diferentes das ferramentas de linha de comando do Xcode, que, a partir do macOS 10.9, são [instaladas com o Xcode](https://developer.apple.com/library/content/technotes/tn2339/_index.html).

### <a name="troubleshooting-automatic-mac-provisioning"></a>Solução de problemas do provisionamento automático do Mac

Se tiver problemas para usar o provisionamento automático do Mac, examine os logs do IDE do Visual Studio 2019, armazenados em **%LOCALAPPDATA%\Xamarin\Logs\15.0**. Esses logs podem conter mensagens de erro para ajudá-lo a melhor diagnosticar a falha ou obter suporte.

## <a name="build-ios-apps-from-the-windows-command-line"></a>Crie aplicativos do iOS da linha de comando do Windows

O Emparelhar com Mac dá suporte à criação de aplicativos Xamarin.iOS da linha de comando. Por exemplo:

```bash
C:\samples\App1>msbuild App1.sln /p:ServerAddress=10.211.55.2 /p:ServerUser=xamUser /p:Platform=iPhoneSimulator /p:ServerPassword=mypassword
```

Os parâmetros passados para `msbuild` no exemplo acima são:

- `ServerAddress` – o endereço IP do host de build do Mac.
- `ServerUser` – o nome de usuário a ser usado ao fazer logon no host de build do Mac.
  Use seu nome de usuário do sistema em vez do nome completo.
- `ServerPassword` – a senha a ser usada ao fazer logon no host de build do Mac.

> [!NOTE]
> O Visual Studio 2019 armazena o `msbuild` no seguinte diretório: **C:\Arquivos de Programas (x86)\Microsoft Visual Studio\2017\\&lt;Version&gt;\MSBuild\15.0\Bin**

Na primeira vez em que o Emparelhar com Mac fizer logon em um determinado host de build do Mac usando o Visual Studio 2019 ou a linha de comando, ele configurará chaves SSH. Com essas chaves, logons futuros não exigirão um nome de usuário ou senha. As chaves recém-criadas são armazenadas em **%LOCALAPPDATA%\Xamarin\MonoTouch**.

Se o parâmetro `ServerPassword` for omitido de uma invocação de build da linha de comando, o Emparelhar com Mac tentará fazer logon no host de build do Mac usando as chaves SSH salvas.

## <a name="summary"></a>Resumo

Este artigo descreveu como usar o Emparelhar com Mac para conectar o Visual Studio 2019 a um host de build do Mac, permitindo que os desenvolvedores do Visual Studio 2019 criem aplicativos do iOS nativos com o Xamarin.iOS.

## <a name="next-steps"></a>Próximas etapas

- [Solução de problemas de conexão](~/ios/get-started/installation/windows/connecting-to-mac/troubleshooting.md)
- [Vídeo sobre o agente de build do Xamarin Mac](https://www.youtube.com/watch?v=MBAPBtxkjFQ)
- [Introdução ao Xamarin.iOS para Visual Studio](~/ios/get-started/installation/windows/introduction-to-xamarin-ios-for-visual-studio.md)
- [Simulador remoto de iOS para Windows](~/tools/ios-simulator/index.md)
- [Implantação sem fio](~/ios/deploy-test/wireless-deployment.md)
