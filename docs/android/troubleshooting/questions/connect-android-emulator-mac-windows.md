---
title: É possível se conectar ao emuladores Android em execução em um Mac de uma VM do Windows?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 7B6752BB-8E4C-4690-B275-7E425A051F45
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 06/21/2018
ms.openlocfilehash: f94c0966dd67940fc201df84a311db422d77b542
ms.sourcegitcommit: 26033c087f49873243751deded8037d2da701655
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/25/2018
ms.locfileid: "36935195"
---
# <a name="is-it-possible-to-connect-to-android-emulators-running-on-a-mac-from-a-windows-vm"></a>É possível se conectar ao emuladores Android em execução em um Mac de uma VM do Windows?

Para se conectar ao emulador do Android em execução em um Mac da máquina virtual do Windows, use as seguintes etapas:

1.  Iniciar o emulador no Mac.

2.  Eliminar a `adb` server no Mac:

    ```bash
    adb kill-server
    ```

3.  Observe que o emulador está escutando em 2 portas TCP na interface de loopback de rede:

    ```bash
    lsof -iTCP -sTCP:LISTEN -P | grep 'emulator\|qemu'

    emulator6 94105 macuser   20u  IPv4 0xa8dacfb1d4a1b51f      0t0  TCP localhost:5555 (LISTEN)
    emulator6 94105 macuser   21u  IPv4 0xa8dacfb1d845a51f      0t0  TCP localhost:5554 (LISTEN)
    ```

    A número ímpar é a porta usada para se conectar ao `adb`. Consulte também [ http://developer.android.com/tools/devices/emulator.html#emulatornetworking ](http://developer.android.com/tools/devices/emulator.html#emulatornetworking).

4.  _Opção 1_: Use [ `nc` ](https://developer.apple.com/library/mac/documentation/Darwin/Reference/ManPages/man1/nc.1.html) para encaminhar pacotes TCP de entrada recebidos externamente na porta 5555 (ou qualquer outra porta que você deseja) à porta ímpar na interface de loopback (**127.0.0.1 5555** neste exemplo), e para encaminhar os pacotes de saída de outra forma:

    ```bash
    cd /tmp
    mkfifo backpipe
    nc -kl 5555 0<backpipe | nc 127.0.0.1 5555 > backpipe
    ```

    Enquanto o `nc` comandos ficam em execução em uma janela de Terminal, os pacotes serão encaminhados conforme o esperado. Você pode digitar CTRL-C na janela do Terminal para encerrar o `nc` comandos assim que terminar usando o emulador.

    (Opção 1 é geralmente mais fácil do que a opção 2, especialmente se **preferências do sistema > segurança e privacidade > Firewall** está ativada.) 

    _Opção 2_: Use [ `pfctl` ](https://developer.apple.com/library/mac/documentation/Darwin/Reference/ManPages/man8/pfctl.8.html) para redirecionar pacotes TCP na porta `5555` (ou qualquer outra porta que você deseja) no [compartilhado de rede](http://kb.parallels.com/en/4948) interface para a porta ímpar no interface de loopback (`127.0.0.1:5555` neste exemplo):

    ```bash
    sed '/rdr-anchor/a rdr pass on vmnet8 inet proto tcp from any to any port 5555 -> 127.0.0.1 port 5555' /etc/pf.conf | sudo pfctl -ef -
    ```

    Este comando define o uso de encaminhamento de porta de `pf packet filter` o serviço do sistema. As quebras de linha são importantes. Certifique-se de mantê-los intactos quando copiar-colar. Você também precisará ajustar o nome da interface de *vmnet8* se você estiver usando o Parallels. `vmnet8` é o nome do especiais *dispositivo NAT* para o *compartilhado de rede* modo no VMWare fusão. A interface de rede apropriada no Parallels é provavelmente [vnic0](http://download.parallels.com/doc/psbm/en/Parallels_Server_Bare_Metal_Users_Guide/29258.htm).

5.  Conecte-se ao emulador do computador Windows:

    ```cmd
    C:\> adb connect ip-address-of-the-mac:5555
    ```

    Substitua "ip endereço-de-a-mac" com o endereço IP do Mac, por exemplo, como listados pelo `ifconfig vmnet8 | grep 'inet '`. Se necessário, substitua `5555` com a outra porta que você deseja da etapa 4\. (Observação: uma maneira de obter acesso de linha de comando para `adb` é por meio de [ **Ferramentas > Android > Prompt de comando do Android Adb** ](~/cross-platform/troubleshooting/questions/version-logs.md#adb-logcat) no Visual Studio.)

### <a name="alternate-technique-using-ssh"></a>Alternativa usando técnica `ssh`

Se você tiver habilitado _logon remoto_ no Mac, em seguida, você pode usar `ssh` encaminhamento para se conectar ao emulador de porta.

1.  Instale um cliente SSH no Windows. É uma opção instalar o [Git para Windows](https://git-for-windows.github.io/). O `ssh` comando estarão disponível na **Git Bash** prompt de comando.

2.  Siga as etapas 1 a 3 acima para iniciar o emulador, eliminar o `adb` server no Mac e identificar as portas do emulador.

3.  Executar `ssh` no Windows para configurar o encaminhamento de porta bidirecional entre uma porta local no Windows (`localhost:15555` neste exemplo) e a porta do emulador ímpar na interface de loopback do Mac (`127.0.0.1:5555` neste exemplo):

    ```cmd 
    C:\> ssh -L localhost:15555:127.0.0.1:5555 mac-username@ip-address-of-the-mac
    ```

    Substituir `mac-username` com seu nome de usuário do Mac conforme listado por `whoami`. Substituir `ip-address-of-the-mac` com o endereço IP do Mac.

4.  Conecte-se ao emulador usando a porta local no Windows:

    ```cmd
    C:\> adb connect localhost:15555
    ```

    (Observação: uma maneira fácil de obter acesso de linha de comando para `adb` é por meio de [ **Ferramentas > Android > Prompt de comando do Android Adb** no Visual Studio](~/cross-platform/troubleshooting/questions/version-logs.md#adb-logcat).)

Um pequeno cuidado: se você usar a porta `5555` para a porta local, `adb` será considerado que o emulador estiver em execução localmente no Windows. Isso não causa problemas no Visual Studio, mas no Visual Studio para Mac faz com que o aplicativo sair imediatamente após o lançamento.

### <a name="alternate-technique-using-adb--h-is-not-yet-supported"></a>Técnica alternativa usando `adb -H` ainda não é suportada

Em teoria, outra abordagem seria usar `adb`do recurso interno para se conectar a um `adb` server em execução em um computador remoto (consulte, por exemplo [ http://stackoverflow.com/a/18551325 ](http://stackoverflow.com/a/18551325)).
Mas as extensões de IDE xamarin atualmente não fornece uma maneira de configurar essa opção.

## <a name="contact-information"></a>Informações de contato

Este documento aborda o comportamento atual a partir de março de 2016. A técnica descrita neste documento não é parte do conjunto de teste estável para Xamarin, ele pode quebrar no futuro.

Se você perceber que a técnica não funciona, ou se você notar que os outros erros no documento, fique à vontade para adicionar a discussão no seguinte thread do fórum: [ http://forums.xamarin.com/discussion/33702/android-emulator-from-host-device-inside-windows-vm ](http://forums.xamarin.com/discussion/33702/android-emulator-from-host-device-inside-windows-vm).
Obrigado!

