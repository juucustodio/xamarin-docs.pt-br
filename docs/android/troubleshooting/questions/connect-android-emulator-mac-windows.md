---
title: É possível se conectar aos emuladores de Android em execução em um Mac usando uma VM do Windows?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 7B6752BB-8E4C-4690-B275-7E425A051F45
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 06/21/2018
ms.openlocfilehash: 49d1eea60f766f4cb61484a6e441506cf8f046ff
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "78292383"
---
# <a name="is-it-possible-to-connect-to-android-emulators-running-on-a-mac-from-a-windows-vm"></a>É possível se conectar aos emuladores de Android em execução em um Mac usando uma VM do Windows?

Para se conectar ao Emulador Android em execução em um Mac a partir de uma máquina virtual do Windows, use as seguintes etapas:

1. Inicie o emulador no Mac.

2. Mate `adb` o servidor no Mac:

    ```bash
    adb kill-server
    ```

3. Observe que o emulador está ouvindo em 2 portas TCP na interface de rede de loopback:

    ```bash
    lsof -iTCP -sTCP:LISTEN -P | grep 'emulator\|qemu'

    emulator6 94105 macuser   20u  IPv4 0xa8dacfb1d4a1b51f      0t0  TCP localhost:5555 (LISTEN)
    emulator6 94105 macuser   21u  IPv4 0xa8dacfb1d845a51f      0t0  TCP localhost:5554 (LISTEN)
    ```

    A porta numerada ímpar é `adb`a usada para se conectar a . Veja [https://developer.android.com/tools/devices/emulator.html#emulatornetworking](https://developer.android.com/tools/devices/emulator.html#emulatornetworking)também .

4. _Opção 1_ `nc` : Usar para encaminhar pacotes TCP de entrada recebidos externamente na porta 5555 (ou qualquer outra porta que você gosta) para a porta numerada ímpar na interface de loopback **(127.0.0.1 5555** neste exemplo) e para encaminhar os pacotes de saída de volta para o outro lado:

    ```bash
    cd /tmp
    mkfifo backpipe
    nc -kl 5555 0<backpipe | nc 127.0.0.1 5555 > backpipe
    ```

    Enquanto os `nc` comandos permanecerem em execução em uma janela de Terminal, os pacotes serão encaminhados como esperado. Você pode digitar Control-C na janela `nc` Terminal para sair dos comandos assim que terminar usando o emulador.

    (A opção 1 geralmente é mais fácil do que a Opção 2, especialmente se **as preferências do sistema > segurança & privacidade > firewall** estiver ligada.)

    _Opção 2_ `pfctl` : Usar para redirecionar `5555` pacotes TCP da porta (ou qualquer outra porta que você goste) na`127.0.0.1:5555` interface De Rede [Compartilhada](https://kb.parallels.com/en/4948) para a porta numerada ímpar na interface de loopback (neste exemplo):

    ```bash
    sed '/rdr-anchor/a rdr pass on vmnet8 inet proto tcp from any to any port 5555 -> 127.0.0.1 port 5555' /etc/pf.conf | sudo pfctl -ef -
    ```

    Este comando configura o encaminhamento `pf packet filter` da porta usando o serviço do sistema. As quebras de linha são importantes. Certifique-se de mantê-los intactos ao copiar. Você também precisará ajustar o nome da interface do *vmnet8* se estiver usando Paralelos. `vmnet8`é o nome do *dispositivo NAT* especial para o modo *de rede compartilhada* no VMWare Fusion. A interface de rede apropriada em Paralelos é provavelmente [vnic0](https://download.parallels.com/doc/psbm/en/Parallels_Server_Bare_Metal_Users_Guide/29258.htm).

5. Conecte-se ao emulador a partir da máquina Windows:

    ```cmd
    C:\> adb connect ip-address-of-the-mac:5555
    ```

    Substitua o "ip-address-of-the-mac" pelo endereço IP do `ifconfig vmnet8 | grep 'inet '`Mac, por exemplo, conforme listado por . Se necessário, `5555` substitua-o pela outra porta que você gosta a partir do passo 4\. (Nota: uma maneira de obter `adb` acesso à linha de comando é através de [**ferramentas > Android > Prompt de comando Android Adb**](~/cross-platform/troubleshooting/questions/version-logs.md#adb-logcat) no Visual Studio.)

### <a name="alternate-technique-using-ssh"></a>Técnica alternativa usando`ssh`

Se você habilitou o _Login Remoto_ no `ssh` Mac, então você pode usar o encaminhamento da porta para se conectar ao emulador.

1. Instale um cliente SSH no Windows. Uma opção é instalar [o Git para Windows](https://git-for-windows.github.io/). O `ssh` comando estará disponível no prompt de comando **Git Bash.**

2. Siga as etapas 1-3 de cima para `adb` iniciar o emulador, mate o servidor no Mac e identifique as portas do emulador.

3. Execute `ssh` no Windows para configurar o encaminhamento de portas bidirecionais entre uma porta local no Windows (neste`localhost:15555` exemplo) e`127.0.0.1:5555` a porta do emulador numerada ímpar na interface de loopback do Mac (neste exemplo):

    ```cmd
    C:\> ssh -L localhost:15555:127.0.0.1:5555 mac-username@ip-address-of-the-mac
    ```

    Substitua com `mac-username` o nome `whoami`de usuário do Mac conforme listado por . Substitua pelo `ip-address-of-the-mac` endereço IP do Mac.

4. Conecte-se ao emulador usando a porta local no Windows:

    ```cmd
    C:\> adb connect localhost:15555
    ```

    (Nota: uma maneira fácil de `adb` obter acesso à linha de comando é através de [ **ferramentas > Android > Prompt de comando Android Adb** no Visual Studio](~/cross-platform/troubleshooting/questions/version-logs.md#adb-logcat).)

Um pequeno cuidado: se `5555` você usar `adb` a porta para a porta local, pensará que o emulador está sendo executado localmente no Windows. Isso não causa nenhum problema no Visual Studio, mas no Visual Studio para Mac faz com que o aplicativo saia imediatamente após o lançamento.

### <a name="alternate-technique-using-adb--h-is-not-yet-supported"></a>A técnica `adb -H` alternativa que usa ainda não é suportada

Em teoria, outra abordagem `adb`seria usar o recurso interno `adb` para se conectar a um [https://stackoverflow.com/a/18551325](https://stackoverflow.com/a/18551325)servidor em execução em uma máquina remota (veja, por exemplo).
Mas as extensões Xamarin.Android IDE não fornecem atualmente uma maneira de configurar essa opção.

## <a name="contact-information"></a>Informações de contato

Este documento discute o comportamento atual a partir de março de 2016. A técnica descrita neste documento não faz parte da suíte de testes estáveis para Xamarin, por isso pode quebrar no futuro.

Se você notar que a técnica não funciona mais, ou se você notar outros erros no documento, [http://forums.xamarin.com/discussion/33702/android-emulator-from-host-device-inside-windows-vm](https://forums.xamarin.com/discussion/33702/android-emulator-from-host-device-inside-windows-vm)sinta-se livre para adicionar à discussão sobre o seguinte segmento de fórum: .
Obrigado!
