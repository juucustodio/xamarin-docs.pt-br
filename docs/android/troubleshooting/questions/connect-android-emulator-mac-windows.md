---
title: É possível se conectar aos emuladores de Android em execução em um Mac usando uma VM do Windows?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 7B6752BB-8E4C-4690-B275-7E425A051F45
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/21/2018
ms.openlocfilehash: af32f4af3951eff3b8b5412908e35c4cdef09ae4
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70757269"
---
# <a name="is-it-possible-to-connect-to-android-emulators-running-on-a-mac-from-a-windows-vm"></a>É possível se conectar aos emuladores de Android em execução em um Mac usando uma VM do Windows?

Para se conectar ao Android Emulator em execução em um Mac de uma máquina virtual do Windows, use as seguintes etapas:

1. Inicie o emulador no Mac.

2. Eliminar o `adb` servidor no Mac:

    ```bash
    adb kill-server
    ```

3. Observe que o emulador está escutando duas portas TCP na interface de rede de loopback:

    ```bash
    lsof -iTCP -sTCP:LISTEN -P | grep 'emulator\|qemu'

    emulator6 94105 macuser   20u  IPv4 0xa8dacfb1d4a1b51f      0t0  TCP localhost:5555 (LISTEN)
    emulator6 94105 macuser   21u  IPv4 0xa8dacfb1d845a51f      0t0  TCP localhost:5554 (LISTEN)
    ```

    A porta ímpar é aquela usada para se conectar ao `adb`. Consulte também [https://developer.android.com/tools/devices/emulator.html#emulatornetworking](https://developer.android.com/tools/devices/emulator.html#emulatornetworking).

4. _Opção 1_: Utilizá[`nc`](https://developer.apple.com/library/mac/documentation/Darwin/Reference/ManPages/man1/nc.1.html)
    para encaminhar pacotes TCP de entrada recebidos externamente na porta 5555 (ou qualquer outra porta que você desejar) para a porta ímpar na interface de loopback (**127.0.0.1 5555** neste exemplo) e para encaminhar os pacotes de saída de outra maneira:

    ```bash
    cd /tmp
    mkfifo backpipe
    nc -kl 5555 0<backpipe | nc 127.0.0.1 5555 > backpipe
    ```

    Desde que os `nc` comandos permaneçam em execução em uma janela de terminal, os pacotes serão encaminhados conforme o esperado. Você pode digitar Control-C na janela do terminal para encerrar os `nc` comandos quando terminar de usar o emulador.

    (A opção 1 é geralmente mais fácil do que a opção 2, especialmente se as **preferências do sistema > segurança & privacidade > o firewall** for ativado.) 

    _Opção 2_: Utilizá[`pfctl`](https://developer.apple.com/library/mac/documentation/Darwin/Reference/ManPages/man8/pfctl.8.html)
    para redirecionar pacotes TCP `5555` da porta (ou qualquer outra porta que desejar) na interface de [rede compartilhada](http://kb.parallels.com/en/4948) para a porta ímpar na interface de loopback (`127.0.0.1:5555` neste exemplo):

    ```bash
    sed '/rdr-anchor/a rdr pass on vmnet8 inet proto tcp from any to any port 5555 -> 127.0.0.1 port 5555' /etc/pf.conf | sudo pfctl -ef -
    ```

    Esse comando configura o encaminhamento de porta usando `pf packet filter` o serviço do sistema. As quebras de linha são importantes. Lembre-se de mantê-los intactos quando copiar e colar. Você também precisará ajustar o nome da interface de *vmnet8* se estiver usando paralelos. `vmnet8`é o nome do *dispositivo NAT* especial para o modo de *rede compartilhado* no VMware Fusion. A interface de rede apropriada em paralelos é provavelmente [vnic0](http://download.parallels.com/doc/psbm/en/Parallels_Server_Bare_Metal_Users_Guide/29258.htm).

5. Conecte-se ao emulador do computador Windows:

    ```cmd
    C:\> adb connect ip-address-of-the-mac:5555
    ```

    Substitua "IP-address-of-the-Mac" pelo endereço IP do Mac, por exemplo, conforme listado por `ifconfig vmnet8 | grep 'inet '`. Se necessário, substitua `5555` pela outra porta que você deseja na etapa 4\. (Observação: uma maneira de obter acesso de linha de comando `adb` ao é por meio de [**Ferramentas > prompt de comando do > de ADB do Android**](~/cross-platform/troubleshooting/questions/version-logs.md#adb-logcat) no Visual Studio.)

### <a name="alternate-technique-using-ssh"></a>Técnica alternativa usando`ssh`

Se você tiver habilitado _acesso remoto_ no Mac, poderá usar `ssh` o encaminhamento de porta para se conectar ao emulador.

1. Instale um cliente SSH no Windows. Uma opção é instalar o [git para Windows](https://git-for-windows.github.io/). O `ssh` comando estará disponível no prompt de comando do **git bash** .

2. Siga as etapas 1-3 acima para iniciar o emulador, eliminar `adb` o servidor no Mac e identificar as portas do emulador.

3. Execute `ssh` no Windows para configurar o encaminhamento de porta bidirecional entre uma porta local no Windows (`localhost:15555` neste exemplo) e a porta do emulador ímpar na interface de loopback do Mac (`127.0.0.1:5555` neste exemplo):

    ```cmd 
    C:\> ssh -L localhost:15555:127.0.0.1:5555 mac-username@ip-address-of-the-mac
    ```

    Substitua `mac-username` pelo nome de usuário do Mac, `whoami`conforme listado por. Substitua `ip-address-of-the-mac` pelo endereço IP do Mac.

4. Conecte-se ao emulador usando a porta local no Windows:

    ```cmd
    C:\> adb connect localhost:15555
    ```

    (Observação: uma maneira fácil de obter acesso de linha de comando `adb` ao é por meio de [ **Ferramentas > prompt de comando de ADB do Android > Android** no Visual Studio](~/cross-platform/troubleshooting/questions/version-logs.md#adb-logcat).)

Um pequeno cuidado: se você usar a `5555` porta para a porta local `adb` , o irá considerar que o emulador está sendo executado localmente no Windows. Isso não causa problemas no Visual Studio, mas em Visual Studio para Mac ele faz com que o aplicativo saia imediatamente após a inicialização.

### <a name="alternate-technique-using-adb--h-is-not-yet-supported"></a>A técnica alternativa `adb -H` que usa ainda não tem suporte

Teoricamente, outra abordagem seria usar `adb`a funcionalidade interna do para se conectar a um `adb` servidor em execução em um computador remoto (Veja, por exemplo [https://stackoverflow.com/a/18551325](https://stackoverflow.com/a/18551325)).
Mas, no momento, as extensões do Xamarin. Android IDE não fornecem uma maneira de configurar essa opção.

## <a name="contact-information"></a>Informações de contato

Este documento discute o comportamento atual a partir de março de 2016. A técnica descrita neste documento não faz parte do pacote de testes estável para o Xamarin, portanto, ele pode ser interrompido no futuro.

Se você observar que a técnica não funciona mais, ou se você notar outros erros no documento, sinta-se à vontade para adicionar à discussão sobre o seguinte thread de fórum [http://forums.xamarin.com/discussion/33702/android-emulator-from-host-device-inside-windows-vm](http://forums.xamarin.com/discussion/33702/android-emulator-from-host-device-inside-windows-vm):.
Pelo!
