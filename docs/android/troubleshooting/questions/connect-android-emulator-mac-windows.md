---
title: É possível se conectar aos emuladores de Android em execução em um Mac usando uma VM do Windows?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 7B6752BB-8E4C-4690-B275-7E425A051F45
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/21/2018
ms.openlocfilehash: 35bfdb92ccfffe54f0ca10dc001d8919703a5bd8
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/08/2019
ms.locfileid: "57668147"
---
# <a name="is-it-possible-to-connect-to-android-emulators-running-on-a-mac-from-a-windows-vm"></a>É possível se conectar aos emuladores de Android em execução em um Mac usando uma VM do Windows?

Para se conectar ao emulador do Android em execução em um Mac de uma máquina virtual do Windows, use as seguintes etapas:

1.  Iniciar o emulador no Mac.

2.  Eliminar o `adb` server no Mac:

    ```bash
    adb kill-server
    ```

3.  Observe que o emulador está escutando em 2 portas TCP no adaptador de loopback de rede:

    ```bash
    lsof -iTCP -sTCP:LISTEN -P | grep 'emulator\|qemu'

    emulator6 94105 macuser   20u  IPv4 0xa8dacfb1d4a1b51f      0t0  TCP localhost:5555 (LISTEN)
    emulator6 94105 macuser   21u  IPv4 0xa8dacfb1d845a51f      0t0  TCP localhost:5554 (LISTEN)
    ```

    A porta de número ímpar é usada para se conectar ao `adb`. Consulte também [ https://developer.android.com/tools/devices/emulator.html#emulatornetworking ](https://developer.android.com/tools/devices/emulator.html#emulatornetworking).

4.  _Opção 1_: Use [`nc`](https://developer.apple.com/library/mac/documentation/Darwin/Reference/ManPages/man1/nc.1.html)
    para encaminhar pacotes TCP de entrada recebido externamente na porta 5555 (ou qualquer outra porta desejado) para a porta ímpares na interface de loopback (**127.0.0.1 5555** neste exemplo), e para encaminhar os pacotes de saída de outra forma de volta:

    ```bash
    cd /tmp
    mkfifo backpipe
    nc -kl 5555 0<backpipe | nc 127.0.0.1 5555 > backpipe
    ```

    Desde que o `nc` comandos permanecem em execução em uma janela de Terminal, os pacotes serão encaminhados conforme o esperado. Você pode digitar Control-C na janela do Terminal para encerrar o `nc` comandos quando você terminar usando o emulador.

    (Opção 1 é geralmente mais fácil do que a opção 2, especialmente se **preferências do sistema > segurança e privacidade > Firewall** está ativada.) 

    _Opção 2_: Use [`pfctl`](https://developer.apple.com/library/mac/documentation/Darwin/Reference/ManPages/man8/pfctl.8.html)
    Para redirecionar os pacotes TCP de porta `5555` (ou qualquer outra porta que você deseja) na [compartilhada de rede](http://kb.parallels.com/en/4948) interface para a porta ímpares na interface de loopback (`127.0.0.1:5555` neste exemplo):

    ```bash
    sed '/rdr-anchor/a rdr pass on vmnet8 inet proto tcp from any to any port 5555 -> 127.0.0.1 port 5555' /etc/pf.conf | sudo pfctl -ef -
    ```

    Este comando define a porta de encaminhamento usando o `pf packet filter` serviço do sistema. As quebras de linha são importantes. Certifique-se de mantê-los intactos quando copiando e colando. Você também precisará ajustar o nome da interface de *vmnet8* se você estiver usando o Parallels. `vmnet8` é o nome do especiais *dispositivo NAT* para o *compartilhadas de rede* modo no VMWare Fusion. A interface de rede apropriada no Parallels provavelmente [vnic0](http://download.parallels.com/doc/psbm/en/Parallels_Server_Bare_Metal_Users_Guide/29258.htm).

5.  Conecte-se ao emulador do computador Windows:

    ```cmd
    C:\> adb connect ip-address-of-the-mac:5555
    ```

    Substitua o "ip – endereço-do-do-mac" com o endereço IP do Mac, por exemplo, como listados pelo `ifconfig vmnet8 | grep 'inet '`. Se necessário, substitua `5555` com a outra porta que você gosta da etapa 4\. (Observação: é uma maneira de obter acesso de linha de comando ao `adb` é por meio [ **Ferramentas > Android > Prompt de comando do Adb do Android** ](~/cross-platform/troubleshooting/questions/version-logs.md#adb-logcat) no Visual Studio.)

### <a name="alternate-technique-using-ssh"></a>Alternativa usando técnica `ssh`

Se você habilitou _logon remoto_ no Mac, em seguida, você pode usar `ssh` encaminhamento para se conectar ao emulador de porta.

1.  Instale um cliente SSH no Windows. Uma opção é instalar [Git para Windows](https://git-for-windows.github.io/). O `ssh` comando estará disponível em de **Git Bash** prompt de comando.

2.  Siga as etapas 1 a 3 acima para iniciar o emulador, eliminar o `adb` server no Mac e identificar as portas do emulador.

3.  Execute `ssh` no Windows para configurar o encaminhamento de porta bidirecional entre uma porta local no Windows (`localhost:15555` neste exemplo) e a porta do emulador ímpares na interface de loopback do Mac (`127.0.0.1:5555` neste exemplo):

    ```cmd 
    C:\> ssh -L localhost:15555:127.0.0.1:5555 mac-username@ip-address-of-the-mac
    ```

    Substitua `mac-username` com seu nome de usuário do Mac conforme listado em `whoami`. Substitua `ip-address-of-the-mac` com o endereço IP do Mac.

4.  Conecte-se ao emulador usando a porta local no Windows:

    ```cmd
    C:\> adb connect localhost:15555
    ```

    (Observação: uma forma fácil de obter acesso de linha de comando ao `adb` é por meio [ **Ferramentas > Android > Prompt de comando do Adb do Android** no Visual Studio](~/cross-platform/troubleshooting/questions/version-logs.md#adb-logcat).)

Um pequeno cuidado: se você usar a porta `5555` para a porta local, `adb` pensará que o emulador está em execução localmente no Windows. Isso não causa problemas no Visual Studio, mas no Visual Studio para Mac faz com que o aplicativo sair imediatamente após a inicialização.

### <a name="alternate-technique-using-adb--h-is-not-yet-supported"></a>Técnica alternativa usando `adb -H` ainda não é suportado

Em teoria, outra abordagem seria usar `adb`do recurso interno para se conectar a um `adb` server em execução em um computador remoto (consulte, por exemplo [ https://stackoverflow.com/a/18551325 ](https://stackoverflow.com/a/18551325)).
Mas, atualmente, as extensões do IDE do xamarin. Android fornecem uma maneira de configurar essa opção.

## <a name="contact-information"></a>Informações de contato

Este documento aborda o comportamento atual a partir de março de 2016. A técnica descrita neste documento não é parte do conjunto de teste estável para Xamarin, ele pode ser desfeito no futuro.

Se você notar que a técnica não funciona mais ou se você observar quaisquer outros erros no documento, fique à vontade adicionar a discussão sobre o seguinte thread de fórum: [ http://forums.xamarin.com/discussion/33702/android-emulator-from-host-device-inside-windows-vm ](http://forums.xamarin.com/discussion/33702/android-emulator-from-host-device-inside-windows-vm).
Obrigado!

