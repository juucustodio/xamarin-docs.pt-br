---
title: É possível se conectar aos emuladores de Android em execução em um Mac usando uma VM do Windows?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 7B6752BB-8E4C-4690-B275-7E425A051F45
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 06/21/2018
ms.openlocfilehash: 336fb2ae1f1619994b11b630a6cb6726f17d0758
ms.sourcegitcommit: 6d347e1d7641ac1d2b389fb1dc7a6882a08f7c00
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/08/2020
ms.locfileid: "91851516"
---
# <a name="is-it-possible-to-connect-to-android-emulators-running-on-a-mac-from-a-windows-vm"></a>É possível se conectar aos emuladores de Android em execução em um Mac usando uma VM do Windows?

Para se conectar ao Android Emulator em execução em um Mac de uma máquina virtual do Windows, use as seguintes etapas:

> [!NOTE]
> É recomendável usar um Android Emulator que não inclua o Google Play Store.

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

    A porta ímpar é aquela usada para se conectar ao `adb` . Consulte também [https://developer.android.com/tools/devices/emulator.html#emulatornetworking](https://developer.android.com/tools/devices/emulator.html#emulatornetworking) .

4. _Opção 1_: use `nc` para ENcaminhar pacotes TCP de entrada recebidos externamente na porta 5555 (ou qualquer outra porta que desejar) para a porta ímpar na interface de loopback (**127.0.0.1 5555** neste exemplo) e para encaminhar os pacotes de saída de outra maneira:

    ```bash
    cd /tmp
    mkfifo backpipe
    nc -kl 5555 0<backpipe | nc 127.0.0.1 5555 > backpipe
    ```

    Desde que os `nc` comandos permaneçam em execução em uma janela de terminal, os pacotes serão encaminhados conforme o esperado. Você pode digitar Control-C na janela do terminal para encerrar os `nc` comandos quando terminar de usar o emulador.

    (A opção 1 é geralmente mais fácil do que a opção 2, especialmente se as **preferências do sistema > segurança & privacidade > o firewall** for ativado.)

    _Opção 2_: use `pfctl` para redirecionar pacotes TCP da porta `5555` (ou qualquer outra porta que desejar) na interface de  [rede compartilhada](https://kb.parallels.com/en/4948) para a porta ímpar na interface de loopback ( `127.0.0.1:5555` neste exemplo):

    ```bash
    sed '/rdr-anchor/a rdr pass on vmnet8 inet proto tcp from any to any port 5555 -> 127.0.0.1 port 5555' /etc/pf.conf | sudo pfctl -ef -
    ```

    Esse comando configura o encaminhamento de porta usando o `pf packet filter` serviço do sistema. As quebras de linha são importantes. Lembre-se de mantê-los intactos quando copiar e colar. Você também precisará ajustar o nome da interface de *vmnet8* se estiver usando paralelos. `vmnet8` é o nome do *dispositivo NAT* especial para o modo de *rede compartilhado* no VMware Fusion. A interface de rede apropriada em paralelos é provavelmente [vnic0](https://download.parallels.com/doc/psbm/en/Parallels_Server_Bare_Metal_Users_Guide/29258.htm).

5. Conecte-se ao emulador do computador Windows:

    ```cmd
    C:\> adb connect ip-address-of-the-mac:5555
    ```

    Substitua "IP-address-of-the-Mac" pelo endereço IP do Mac, por exemplo, conforme listado por `ifconfig vmnet8 | grep 'inet '` . Se necessário, substitua `5555` pela outra porta que você deseja na etapa 4\. (Observação: uma maneira de obter acesso de linha de comando ao `adb` é por meio de [**ferramentas > prompt de comando do > de ADB do Android**](~/cross-platform/troubleshooting/questions/version-logs.md#adb-logcat) no Visual Studio.)

### <a name="alternate-technique-using-ssh"></a>Técnica alternativa usando `ssh`

Se você tiver habilitado _acesso remoto_ no Mac, poderá usar o `ssh` encaminhamento de porta para se conectar ao emulador.

1. Instale um cliente SSH no Windows. Uma opção é instalar o [git para Windows](https://git-for-windows.github.io/). O `ssh` comando estará disponível no prompt de comando do **git bash** .

2. Siga as etapas 1-3 acima para iniciar o emulador, eliminar o  `adb` servidor no Mac e identificar as portas do emulador.

3. Execute `ssh` no Windows para configurar o encaminhamento de porta bidirecional entre uma porta local no Windows ( `localhost:15555` neste exemplo) e a porta do emulador ímpar na interface de loopback do Mac ( `127.0.0.1:5555` neste exemplo):

    ```cmd
    C:\> ssh -L localhost:15555:127.0.0.1:5555 mac-username@ip-address-of-the-mac
    ```

    Substitua `mac-username` pelo nome de usuário do Mac, conforme listado por `whoami` . Substitua `ip-address-of-the-mac` pelo endereço IP do Mac.

4. Conecte-se ao emulador usando a porta local no Windows:

    ```cmd
    C:\> adb connect localhost:15555
    ```

    (Observação: uma maneira fácil de obter acesso de linha de comando ao `adb` é por meio de [ **ferramentas > prompt de comando de Adb do Android > Android** no Visual Studio](~/cross-platform/troubleshooting/questions/version-logs.md#adb-logcat).)

Um pequeno cuidado: se você usar `5555` a porta para a porta local, `adb` o irá considerar que o emulador está sendo executado localmente no Windows. Isso não causa problemas no Visual Studio, mas em Visual Studio para Mac ele faz com que o aplicativo saia imediatamente após a inicialização.

### <a name="alternate-technique-using-adb--h-is-not-yet-supported"></a>A técnica alternativa que usa `adb -H` ainda não tem suporte

Teoricamente, outra abordagem seria usar a `adb` funcionalidade interna do para se conectar a um `adb` servidor em execução em um computador remoto (Veja, por exemplo [https://stackoverflow.com/a/18551325](https://stackoverflow.com/a/18551325) ).
Mas, no momento, as extensões do Xamarin. Android IDE não fornecem uma maneira de configurar essa opção.

## <a name="contact-information"></a>Informações de contato

Este documento discute o comportamento atual a partir de março de 2016. A técnica descrita neste documento não faz parte do pacote de testes estável para o Xamarin, portanto, ele pode ser interrompido no futuro.

Se você observar que a técnica não funciona mais, ou se você notar outros erros no documento, sinta-se à vontade para adicionar à discussão sobre o seguinte thread de Fórum: [http://forums.xamarin.com/discussion/33702/android-emulator-from-host-device-inside-windows-vm](https://forums.xamarin.com/discussion/33702/android-emulator-from-host-device-inside-windows-vm) .
Obrigado!
