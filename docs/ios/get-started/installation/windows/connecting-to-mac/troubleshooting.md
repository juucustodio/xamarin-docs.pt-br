---
title: Solução de problemas de conexão de um host de build do Xamarin.iOS
description: Este guia fornece etapas de solução de problemas que podem ser encontrados usando o novo gerenciador de conexões, incluindo problemas de conectividade e de SSH.
ms.prod: xamarin
ms.assetid: A1508A15-1997-4562-B537-E4A9F3DD1F06
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/19/2017
ms.openlocfilehash: 4abe0da2b75b6859c6547f0dc1c4cf8708491afb
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73022472"
---
# <a name="connection-troubleshooting-for-a-xamarinios-build-host"></a>Solução de problemas de conexão de um host de build do Xamarin.iOS

_Este guia fornece etapas de solução de problemas que podem ser encontrados usando o novo gerenciador de conexões, incluindo problemas de conectividade e de SSH._

## <a name="log-file-location"></a>Localização do Arquivo de Log

- **Mac** – ~/Library/Logs/Xamarin-[MAJOR.MINOR]
- **Windows** – %LOCALAPPDATA%\Xamarin\Logs

Os arquivos de log podem ser localizados, navegando até **Ajuda &gt; Xamarin &gt; Zip Logs** no Visual Studio.

## <a name="wheres-the-xamarin-build-host-app"></a>Onde está o Aplicativo de Host de Build do Xamarin?

O Host de Build do Xamarin das versões anteriores do Xamarin.iOS não é mais necessário. O Visual Studio agora implanta automaticamente o agente através de Logon Remoto e o executa em segundo plano. Não há nenhum aplicativo adicional que será executado em computadores Mac ou Windows.

## <a name="troubleshooting-remote-login"></a>Solução de Problemas de Logon Remoto

> [!IMPORTANT]
> Essas etapas de solução de problemas se destinam principalmente a problemas que ocorrem durante a configuração inicial em um novo sistema.  Se anteriormente você usar a conexão com êxito em um ambiente específico e, em seguida, a conexão parar de funcionar de repente ou intermitentemente, você poderá (na maioria dos casos) verificar diretamente se qualquer uma das seguintes ações ajuda: 
>
> - Elimine os processos restantes conforme descrito abaixo em [Erros devido a processos de host de build existentes](#errors). 
> - Desmarque os agentes conforme descrito em [limpando os agentes agente, IDB, compilação e designer](#clearing)e, em seguida, use uma conexão com a Internet com fio e conecte-se diretamente por meio do endereço IP, conforme descrito em [não foi possível conectar-se ao MacBuildHost. local. Tente novamente.](#tryagain)  
> Se nenhuma dessas opções solucionar o problema, siga as instruções na [etapa 9](#stepnine) para registrar um novo relatório de bug.

1. Verifique se você tem as versões compatíveis do Xamarin.iOS instaladas no seu Mac. Para fazer isso com o Visual Studio 2017, verifique se você está no canal de distribuição **Estável** no Visual Studio para Mac. No Visual Studio 2015 e anteriores, verifique se você está no mesmo canal de distribuição em ambos os IDEs.
    - No Visual Studio para Mac, vá para **Visual Studio para Mac > Verificar se há atualizações...**  para exibir ou alterar o **Canal de atualização**.
    - No Visual Studio 2015 e anteriores, verifique o canal de distribuição em **Ferramentas > Opções > Xamarin > Outros**.

2. Verifique se o **Logon remoto** está habilitado no Mac. Defina o acesso para **Apenas esses usuários** e verifique se o seu usuário Mac está incluído na lista ou no grupo:

    [![](troubleshooting-images/troubleshooting-image1.png "Set access for Only these users")](troubleshooting-images/troubleshooting-image1.png#lightbox)

3. Verifique se seu firewall permite conexões de entrada pela porta 22 – padrão para o SSH:

    [![](troubleshooting-images/troubleshooting-image2.png "Check that the firewall allows incoming connections through port 22")](troubleshooting-images/troubleshooting-image2.png#lightbox)

    Se você desabilitou **Permitir automaticamente que o software assinado receba conexões de entrada**, o OS X apresentará uma caixa de diálogo durante o processo de emparelhamento pedindo para permitir que `mono-sgen` ou `mono-sgen32` receba conexões de entrada. Certifique-se de clicar em **Permitir** nessa caixa de diálogo:

    [![](troubleshooting-images/troubleshooting-image4a.png "Click Allow on this dialog")](troubleshooting-images/troubleshooting-image4a.png#lightbox)

4. Verifique se você fez logon na conta do usuário nesse Mac e que tem uma sessão de GUI ativa.

5. Verifique se você está se conectando ao Mac com o _nome de usuário_ em vez do _Nome completo_. Isso evita uma limitação conhecida para nomes completos que incluam caracteres acentuados.

    Você pode encontrar o _nome de usuário_ executando o comando `whoami` no **Terminal.app**.

    Por exemplo, na captura de tela abaixo, o nome da conta será **amyb** e não **Amy Burns**:

    [![](troubleshooting-images/troubleshooting-image5a.png "Getting the account name from the Terminal app")](troubleshooting-images/troubleshooting-image5a.png#lightbox)

6. Verifique se o endereço IP que você está usando para o Mac está correto. Você pode encontrar o endereço IP em **Preferências do Sistema > Compartilhamento > Logon Remoto** no Mac.

    [![](troubleshooting-images/troubleshooting-image17.png "The IP address in the System Preferences app")](troubleshooting-images/troubleshooting-image17.png#lightbox)

7. Depois de confirmar o endereço IP do Mac, tente executar um `ping` nesse endereço no `cmd.exe` no Windows:

    ```
    ping 10.1.8.95
    ```
    
    Se o ping falhar, o Mac não estará _roteável_ do computador Windows. Esse problema precisa ser resolvido no nível de configuração de rede local entre os 2 computadores. Certifique-se de que os dois computadores estejam na mesma Rede Local.

8. Em seguida, teste se o cliente `ssh` do OpenSSH pode conectar-se com êxito no Mac do Windows. Uma maneira de instalar este programa é instalar o [Git para Windows](https://git-for-windows.github.io/). Em seguida, você pode iniciar um prompt de comando **Git Bash** e tentar fazer `ssh` no Mac com seu nome de usuário e endereço IP:

    ```bash
    ssh amyb@10.1.8.95
    ```

    <a name="stepnine" />

9. Se **a etapa 8 for bem-sucedida**, você poderá tentar executar um comando simples, como o `ls` através da conexão:

    ```bash
    ssh amyb@10.1.8.95 'ls'
    ```
    
    Isso deve listar o conteúdo de seu diretório base no Mac. Se o comando `ls` funcionar corretamente, mas a conexão do Visual Studio ainda falhar, você poderá verificar a seção "[Problemas conhecidos e limitações](#knownissues)" sobre as complicações específicas do Xamarin. Se nenhuma das opções servir para o seu problema, envie um novo relatório de bug na Comunidade de Desenvolvedores acessando **Ajuda > Enviar Comentários > Relatar um problema** no Visual Studio e anexe os logs descritos em [Verificar os Arquivos de Log Detalhados](#verboselogs).

10. Se **etapa 8 falhar**, você poderá executar o seguinte comando no Terminal do Mac para ver se o servidor SSH está aceitando _alguma_ conexão:

    ```bash
    ssh localhost
    ```
    
11. Se a etapa 8 falhar, mas a **etapa 10 tiver êxito**, então o problema mais provável é a porta 22 no host de build do Mac, que não está acessível no Windows devido à configuração da rede. Possíveis problemas de configuração incluem:

    - As configurações de firewall do OS X estão desautorizando a conexão. Certifique-se de verificar novamente a etapa 3.

        Ocasionalmente, a configuração por aplicativo para o firewall do OS X também pode terminar em um estado inválido, em que as configurações mostradas em Preferências do Sistema não refletem o comportamento real. Excluir o arquivo de configuração ( **/Library/Preferences/com.apple.alf.plist**) e reinicializar o computador pode ajudar a restaurar o comportamento padrão. Uma maneira de excluir o arquivo é inserir no Localizador **/biblioteca/preferências** em **Ir &gt; Ir para a pasta** e, em seguida, mover o arquivo **com.apple.alf.plist** para a Lixeira.

    - As configurações de firewall de um dos roteadores entre o Mac e o computador Windows está bloqueando a conexão.

    - O próprio Windows está impedindo conexões de saída na porta remota 22. Isso seria incomum. É possível configurar o Firewall do Windows para não permitir conexões de saída, mas a configuração padrão é permitir todas as conexões de saída.

    - O host de build do Mac não está permitindo acesso à porta 22 de todos os hosts externos por meio de uma regra `pfctl`. Isso é improvável, a menos que você se lembre de ter configurado a `pfctl` no passado.

12. Se a etapa 8 falhar e a **etapa 10 falhar**, é provável que o problema seja com o processo do servidor SSH no Mac, que não está em execução ou não está configurado para permitir que o usuário atual faça logon. Nesse caso certifique-se de verificar as configurações de Logon Remoto da etapa 2 antes de investigar outras possibilidades mais complicadas.

<a name="knownissues" />

### <a name="known-issues-and-limitations"></a>Limitações e problemas conhecidos

> [!NOTE]
> Esta seção se aplica apenas se você já se conectou com êxito ao host de build do Mac com seu nome de usuário e senha do Mac usando o cliente SSH OpenSSH, conforme discutido anteriormente nas etapas 8 e 9.

#### <a name="invalid-credentials-please-try-again"></a>"Credenciais inválidas. Tente novamente."

Causas conhecidas:

- **Limitação** – este erro pode ocorrer ao tentar fazer logon no host de build usando o _Nome Completo_ da conta, se o nome incluir um caractere acentuado. Esta é uma limitação da [Biblioteca SSH.NET](https://sshnet.codeplex.com/) que o Xamarin usa para a conexão SSH. **Solução alternativa**: consulte a etapa 5 acima.

#### <a name="unable-to-authenticate-with-ssh-keys-please-try-to-log-in-with-credentials-first"></a>"Não é possível autenticar com chaves SSH. Tente fazer logon com credenciais primeiro"

Causa conhecida:

- **Restrição de segurança SSH** – geralmente, essa mensagem significa que um dos arquivos ou diretórios no caminho totalmente qualificado do **$HOME/.ssh/authorized\_keys** no Mac tem permissões de gravação habilitadas para _outros_ ou para membros de _grupo_. **Correção comum**: executar `chmod og-w "$HOME"` em um prompt de comando do Terminal no Mac. Para obter detalhes sobre o arquivo ou diretório específico que está causando o problema, execute `grep sshd /var/log/system.log > "$HOME/Desktop/sshd.log"` no Terminal e, em seguida, abra o arquivo **sshd.log** da área de trabalho e procure por "Autenticação recusada: modos ou propriedade inválida".

#### <a name="trying-to-connect-never-completes"></a>"Tentando se conectar..." nunca é concluído

- **Bug [#52264](https://bugzilla.xamarin.com/show_bug.cgi?id=52264)**  – Esse problema poderá acontecer no Xamarin 4.1 se **Login shell (Shell de logon)** no menu de contexto **Opções Avançadas** para o usuário do Mac em **System Preferences (Preferências do sistema) &gt; Users (Usuários) &amp; Groups (Grupos)** estiver definido como um valor diferente de **/bin/bash**. (A partir do Xamarin 4,2, esse cenário leva à mensagem de erro "não foi possível conectar".) **Solução alternativa**: altere o **Shell de logon** de volta para o padrão original de **/bin/bash**.

<a name="tryagain" />

#### <a name="couldnt-connect-to-macbuildhostlocal-please-try-again"></a>"Não foi possível conectar ao MacBuildHost.local. Tente novamente."

Causas relatadas:

- **Bug** – alguns usuários viram essa mensagem de erro junto com um erro mais detalhado nos arquivos de log "ocorreu um erro inesperado ao configurar o ssh para o usuário... A operação de sessão atingiu o tempo limite "ao tentar fazer logon no host de compilação usando uma Active Directory ou outra conta de usuário de domínio de serviço de diretório. **Solução alternativa:** faça logon no host de build usando uma conta de usuário local como alternativa.

- **Bug** – alguns usuários viram esse erro ao tentar se conectar ao host de build clicando duas vezes no nome do Mac na caixa de diálogo de conexão. **Solução alternativa possível**: [adicionar manualmente o Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md#manually-add-a-mac) usando o endereço IP.

- **Bug [#35971](https://bugzilla.xamarin.com/show_bug.cgi?id=35971)**  – alguns usuários encontraram esse erro ao usar uma conexão de rede sem fio entre o host de build do Mac e o Windows. **Solução alternativa possível**: mover ambos os computadores para uma conexão de rede com fio.

- **Bug [#36642](https://bugzilla.xamarin.com/show_bug.cgi?id=36642)**  – no Xamarin 4.0, esta mensagem será exibida a qualquer momento que o arquivo **$HOME/.bashrc** no Mac contiver um erro. (A partir do Xamarin 4,1, os erros no arquivo **. bashrc** não afetarão mais o processo de conexão.) **Solução alternativa**: Mova o arquivo **. bashrc** para um local de backup (ou exclua-o se você souber que não precisa dele).

- **Bug [#52264](https://bugzilla.xamarin.com/show_bug.cgi?id=52264)** – esse erro poderá aparecer se **Shell de logon** no menu de contexto **Opções Avançadas** para o usuário do Mac em **Preferências do sistema > Usuários e Grupos** estiver definido como um valor diferente de **/bin/bash**. **Solução alternativa**: mude o **Login shell (Shell de logon)** para o padrão original de **/bin/bash**.

- **Limitação** – este erro pode ocorrer se o host de build do Mac estiver conectado a um roteador que não tem acesso à Internet (ou se o Mac estiver usando um servidor DNS que atinge o tempo limite ao ser solicitado para buscar o DNS reverso do computador Windows). O Visual Studio levará aproximadamente 30 segundos para recuperar a impressão digital de SSH e, eventualmente, falhará para se conectar.

    **Solução alternativa possível**: adicione "UseDNS no" ao arquivo **sshd\_config**. Certifique-se de ler sobre essa configuração de SSH antes de alterá-la. Consulte, por exemplo [unix.stackexchange.com/questions/56941/what-is-the-point-of-sshd-usedns-option](https://unix.stackexchange.com/questions/56941/what-is-the-point-of-sshd-usedns-option).

    As etapas a seguir descrevem uma forma para alterar a configuração. Você precisará fazer logon com uma conta de administrador no Mac para concluir as etapas.

    1. Confirme o local do arquivo **sshd\_config**, executando `ls /etc/ssh/sshd_config` e `ls /etc/sshd_config` em um prompt de comando do Terminal. Para todas as demais etapas, certifique-se de usar o local que _não_ retornar "Não há tal arquivo ou diretório".

        [![](troubleshooting-images/troubleshooting-image18.png "Running `ls /etc/ssh/sshd_config` and `ls /etc/sshd_config` in the Terminal")](troubleshooting-images/troubleshooting-image18.png#lightbox)

    2. Execute `cp /etc/ssh/sshd_config "$HOME/Desktop/"` no Terminal para copiar o arquivo para a sua área de trabalho.

    3. Abra o arquivo da área de trabalho em um editor de texto. Por exemplo, você pode executar `open -a TextEdit "$HOME/Desktop/sshd_config"` no Terminal.

    4. Adicione a seguinte linha na parte inferior do arquivo:

        ```
        UseDNS no
        ```

    5. Remova todas as linhas que dizem `UseDNS yes` para certificar-se de que a nova configuração entrará em vigor.

    6. Salve o arquivo.

    7. Execute `sudo cp "$HOME/Desktop/sshd_config" /etc/ssh/sshd_config` no Terminal para copiar o arquivo editado de volta para o lugar dele. Insira sua senha se solicitado.

    8. Desabilite e reabilite o **Logon Remoto** em **Preferências do Sistema &gt; Compartilhamento &gt; Logon Remoto** para reiniciar o servidor SSH.

<a name="clearing" />

#### <a name="clearing-the-broker-idb-build-and-designer-agents-on-the-mac"></a>Desmarcar o Agente, o IDB, a build e os Agentes de Designer no Mac

Se os arquivos de log mostram um problema durante as etapas "Instalação", "Carregamento" ou "Iniciar" para qualquer um dos agentes do Mac, tente excluir a pasta de cache **XMA** para forçar o Visual Studio a carregá-los novamente.

1. Execute o seguinte comando no Terminal no Mac:

    ```bash
    open "$HOME/Library/Caches/Xamarin"
    ```
    
2. Pressione Control e clique na pasta **XMA** e selecione **Mover para Lixeira**:

    [![](troubleshooting-images/troubleshooting-image8.png "Move the XMA folder to Trash")](troubleshooting-images/troubleshooting-image8.png#lightbox)

3. Há um cache no Windows também que pode ajudar a limpar. Abra um prompt de comando como administrador no Windows:

    ```
    del %localappdata%\Temp\Xamarin\XMA
    ```
    
### <a name="warning-messages"></a>Mensagens de aviso

Esta seção discute algumas mensagens que podem aparecer nas Janela de Saída e nos logs que você geralmente pode ignorar.

#### <a name="there-is-a-mismatch-between-the-installed-xamarinios--and-the-local-xamarinios"></a>"Há uma incompatibilidade entre o Xamarin.iOS instalado... e o Xamarin.iOS local"

Se você confirma que o Mac e Windows estão atualizados no mesmo canal de distribuição do Xamarin, esse aviso pode ser ignorado.

#### <a name="failed-to-execute-ls-usrbinmono-exitstatus1"></a>"Falha ao executar 'ls /usr/bin/mono': ExitStatus=1"

Essa mensagem pode ser ignorada se o Mac estiver executando o OS X 10.11 (El Capitan) ou mais recente. Esta mensagem não é um problema no OS X 10.11 porque o Xamarin também verifica **/usr/local/bin/mono**, que é o local correto esperado para o `mono` no OS X 10.11.

#### <a name="bonjour-service-macbuildhost-did-not-respond-with-its-ip-address"></a>"O serviço Bonjour 'MacBuildHost' não respondeu com seu endereço IP."

Essa mensagem pode ser ignorada, a menos que você observe que a caixa de diálogo de conexão não exibe o endereço IP do host de build do Mac. Se o endereço IP _está_ ausente nessa caixa de diálogo, você ainda poderá [adicionar manualmente o Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md#manually-add-a-mac).

#### <a name="invalid-user-a-from-101895-and-input_userauth_request-invalid-user-a-preauth"></a>"Usuário inválido a de 10.1.8.95" e "input\_userauth\_request: usuário inválido a [preauth]"

Você pode observar essas mensagens ao examinar o **sshd.log**. Essas mensagens fazem parte do processo normal de conexão. Elas aparecem porque o Xamarin usa o nome de usuário **a** temporariamente ao recuperar a _Impressão digital de SSH_.

### <a name="output-window-and-log-files"></a>Janela de Saída e Arquivos de Log

Se o Visual Studio encontrar um erro ao conectar-se ao host de build, há 2 locais para verificar se há mensagens adicionais: a Janela de Saída e os arquivos de log.

#### <a name="output-window"></a>Janela Saída

A Janela de Saída é o melhor lugar para começar. Ela exibe mensagens sobre as principais etapas de conexão e erros. Para exibir as mensagens do Xamarin na Janela de Saída:

1. Selecione **Exibir > Saída** nos menus ou clique na guia **Saída**.
2. Clique no menu suspenso **Mostrar saída de**.
3. Selecione **Xamarin**.

[![](troubleshooting-images/troubleshooting-image11.png "Select Xamarin in the Output tab")](troubleshooting-images/troubleshooting-image11.png#lightbox)

#### <a name="log-files"></a>Arquivos de log

Se a Janela de Saída não tem informações suficientes para diagnosticar o problema, os arquivos de log são o próximo lugar para se olhar. Os arquivos de log contêm mensagens de diagnóstico adicionais que não aparecem na Janela de Saída. Para exibir os arquivos de log:

1. Inicie o Visual Studio.

    > [!IMPORTANT]
    > Observe que **.svclogs** não são habilitados por padrão. Para acessá-los, será necessário iniciar o Visual Studio com logs detalhados, conforme explicado no guia [Logs de Versão](~/cross-platform/troubleshooting/questions/version-logs.md#visual-studio-startup-verbose-logs). Para obter mais informações, consulte o blog [Solução de problemas de extensões com o log de atividades](https://blogs.msdn.microsoft.com/visualstudio/2010/02/24/troubleshooting-extensions-with-the-activity-log/).

2. Tente conectar-se ao host de build.

3. Depois que o Visual Studio encontrar o erro de conexão, colete os logs em **Ajuda > Xamarin > Logs de Zip**:

    [![](troubleshooting-images/troubleshooting-image12.png "Collect the logs from Help > Xamarin > Zip Logs")](troubleshooting-images/troubleshooting-image12.png#lightbox)

4. Quando você abrir o arquivo .zip, verá uma lista de arquivos semelhante ao exemplo a seguir. Para erros de conexão, os arquivos mais importantes são os arquivos **\*Ide.log** e **\*Ide.svclog**. Esses arquivos contêm as mesmas mensagens em dois formatos um pouco diferentes. O **.svclog** é um XML e é útil se você deseja procurar nas mensagens. O **.log** será um texto sem formatação e é útil se você quiser filtrar as mensagens usando ferramentas de linha de comando.

    Para procurar em todas as mensagens, selecione e abra o arquivo **.svclog**:

    [![](troubleshooting-images/troubleshooting-image13.png "Select the svclog file")](troubleshooting-images/troubleshooting-image13.png#lightbox)

5. O arquivo **.svclog** será aberto no **Visualizador de Rastreamento de Serviço da Microsoft**. Você pode procurar as mensagens por thread para ver grupos de mensagens relacionados. Para procurar por thread, primeiro selecione a guia **Gráfico** e, em seguida, clique no menu suspenso **Modo de layout** e selecione **Thread**:

    [![](troubleshooting-images/troubleshooting-image14.png "Click the Layout Mode drop-down menu and select Thread")](troubleshooting-images/troubleshooting-image14.png#lightbox)

<a name="verboselogs" />

#### <a name="verbose-log-files"></a>Arquivos de Log detalhados

Se os arquivos de log normais ainda não fornecerem informações suficientes para diagnosticar o problema, a última técnica a se tentar é habilitar o registro em log detalhado. Os logs detalhados também são preferenciais em relatórios de bugs.

1. Encerre o Visual Studio.

2. Inicie um [**Prompt de Comando de Desenvolvedor**](https://msdn.microsoft.com/library/ms229859(v=vs.110).aspx).

3. Execute o seguinte comando no prompt de comando para inicializar o Visual Studio com o log detalhado:

    ```bash
    devenv /log
    ```

4. Tente conectar-se ao host de build do Visual Studio.

5. Depois que o Visual Studio encontrar o erro de conexão, colete os logs em **Ajuda > Xamarin > Logs de Zip**.

6. Execute o seguinte comando no Terminal no Mac para copiar as mensagens de log recentes do servidor SSH em um arquivo na área de trabalho:

    ```bash
    grep sshd /var/log/system.log > "$HOME/Desktop/sshd.log"
   ```

Se esses arquivos de log detalhados não fornecerem pistas suficientes para resolver diretamente o problema, [registre um novo relatório de bugs](https://bugzilla.xamarin.com/newbug) e anexe o arquivo .zip da etapa 5 e o arquivo .log da etapa 6.

## <a name="troubleshooting-automatic-mac-provisioning"></a>Solução de problemas do provisionamento automático do Mac

### <a name="ide-log-files"></a>Arquivos de log do IDE

Se tiver problemas para usar o [provisionamento automático do Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md#automatic-mac-provisioning), examine os logs do IDE do Visual Studio 2017, armazenados em **%LOCALAPPDATA%\Xamarin\Logs\15.0**.

## <a name="troubleshooting-build-and-deployment-errors"></a>Solução de problemas de Erros de build e Implantação

Esta seção aborda alguns problemas que podem ocorrer depois que o Visual Studio se conecta com êxito ao host de build.

### <a name="unable-to-connect-to-address1921681222-with-usermacuser"></a>"Não é possível se conectar ao Endereço = '192.168.1.2:22' com o usuário = 'macuser'"

Causas conhecidas:

- **Recurso de segurança do Xamarin 4.1** – este erro _acontecerá_ se você fizer downgrade para o Xamarin 4.0 depois de usar o Xamarin 4.1 ou superior. Nesse caso o erro será acompanhado pelo aviso adicional "A chave privada está criptografada, mas a frase secreta está vazia". Esse é uma alteração _intencional_ devida a um novo recurso de segurança do Xamarin 4.1. **Solução recomendada**: excluir **id\_rsa** e **id\_rsa.pub** de **%LOCALAPPDATA%\Xamarin\MonoTouch** e, em seguida, reconectar-se ao host de build do Mac.

- **Restrição de segurança SSH** – quando essa mensagem for acompanhada pelo aviso adicional "Não foi possível autenticar o usuário usando as chaves SSH existentes", geralmente indica que um dos arquivos ou diretórios no caminho totalmente qualificado de **$HOME/.ssh/authorized\_keys** no Mac tem permissões de gravação habilitadas para _outros_ ou para membros de _grupo_. **Correção comum**: executar `chmod og-w "$HOME"` em um prompt de comando do Terminal no Mac. Para obter detalhes sobre o arquivo ou diretório específico que está causando o problema, execute `grep sshd /var/log/system.log > "$HOME/Desktop/sshd.log"` no Terminal e, em seguida, abra o arquivo **sshd.log** da área de trabalho e procure por "Autenticação recusada: modos ou propriedade inválida".

### <a name="solutions-cannot-be-loaded-from-a-network-share"></a>As soluções não podem ser carregadas de um compartilhamento de rede

As soluções só serão compiladas se estiverem no sistema de arquivos local do Windows ou em uma unidade mapeada.

As soluções salvas em um compartilhamento de rede podem gerar erros ou recusarem-se completamente a compilar. Todos os arquivos **.sln** usados no Visual Studio devem ser salvos no sistema de arquivos local do Windows.

O seguinte erro será gerado por causa deste problema:

```bash
error : Building from a network share path is not supported at the moment. Please map a network drive to '\\SharedSources\HelloWorld\HelloWorld' or copy the source to a local directory.
```

bug relacionado: [#36195](https://bugzilla.xamarin.com/show_bug.cgi?id=36195)

### <a name="missing-provisioning-profiles-or-failed-to-create-the-a-fat-library-error"></a>Perfis de Provisionamento Ausentes ou Erro "Falha ao criar a uma biblioteca fat"

Inicialize o Xcode no Mac e verifique se fez logon na sua conta de desenvolvedor Apple e se o seu Perfil de Desenvolvimento do iOS foi baixado:

[![](troubleshooting-images/troubleshooting-image7.png "Ensuring that the Apple developer account is logged in and the iOS Development Profile is downloaded")](troubleshooting-images/troubleshooting-image7.png#lightbox)

### <a name="a-socket-operation-was-attempted-to-an-unreachable-network"></a>"Uma operação de soquete foi tentada em uma rede inacessível"

Causas relatadas:

- **Aprimoramento [#36118](https://bugzilla.xamarin.com/show_bug.cgi?id=36118)** – este erro poderá impedir que os builds sejam bem-sucedidos quando o Visual Studio estiver usando um endereço IPv6 para se conectar ao host de build. (A conexão de host de build ainda não dá suporte a endereços IPv6.)

### <a name="xamarinios-visual-studio-plugin-fails-to-load-after-reinstallation-of-betaalpha-channel"></a>O plug-in Xamarin.iOS do Visual Studio falha ao carregar após a reinstalação do canal alfa/beta

Bug relevante [#40781](https://bugzilla.xamarin.com/show_bug.cgi?id=40781).

Esse problema pode ocorrer quando o Visual Studio não consegue atualizar o cache de componente MEF. Se esse for o caso, instalar esta extensão do Visual Studio pode ajudar: [https://visualstudiogallery.msdn.microsoft.com/22b94661-70c7-4a93-9ca3-8b6dd45f47cd](https://visualstudiogallery.msdn.microsoft.com/22b94661-70c7-4a93-9ca3-8b6dd45f47cd)

Isso limpará o cache de componente do MEF do Visual Studio para corrigir problemas com a corrupção de cache.

<a name="errors" />

### <a name="errors-due-to-existing-build-host-processes-on-the-mac"></a>Erros devido a Processos de Host de Build existentes no Mac

Os processos de conexões de host de build anteriores, às vezes, podem interferir com o comportamento da conexão ativa atual. Para verificar todos os processos existentes, feche o Visual Studio e, em seguida, execute os seguintes comandos no Terminal no Mac:

```bash
ps -A | grep mono
```

[![](troubleshooting-images/troubleshooting-image10.png "Running commands in Terminal on the Mac")](troubleshooting-images/troubleshooting-image10.png#lightbox)

Para eliminar os processos existentes use o seguinte comando:

```bash
killall mono
```

### <a name="clearing-the-mac-build-cache"></a>Limpar o Cache de Build do Mac

Se você estiver solucionando um problema de build e deseja verificar se o comportamento não está relacionado a qualquer um dos arquivos de build temporários armazenados no Mac, você pode excluir a pasta de cache de build.

1. Execute o seguinte comando no Terminal no Mac:

    ```bash
    open "$HOME/Library/Caches/Xamarin"
    ```

2. Pressione Control e clique na pasta **mtbs** e selecione **Mover para Lixeira**:

    [![](troubleshooting-images/troubleshooting-image9.png "Move the mtbs folder to Trash")](troubleshooting-images/troubleshooting-image9.png#lightbox)

## <a name="related-links"></a>Links relacionados

- [Emparelhar com Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md)
- [Vídeo sobre o agente de build do Xamarin Mac](https://www.youtube.com/watch?v=MBAPBtxkjFQ)
