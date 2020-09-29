---
title: Usando Jenkins com Xamarin
description: Este documento descreve como usar o Jenkins para integração contínua com aplicativos Xamarin. Ele aborda como instalar, configurar e usar o Jenkins.
ms.prod: xamarin
ms.assetid: 1E6825DF-1254-4FCB-B94D-ADD33D1B5309
author: davidortinau
ms.author: daortin
ms.date: 03/23/2017
ms.openlocfilehash: 0ce1d4d0b74330b623b6d933e385222a71a38ec4
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91458154"
---
# <a name="using-jenkins-with-xamarin"></a>Usando Jenkins com Xamarin

_Este guia ilustra como configurar o Jenkins como um servidor de integração contínua e automatizar a compilação de aplicativos móveis criados com o Xamarin. Ele descreve como instalar o Jenkins no OS X, configurá-lo e configurar trabalhos para compilar aplicativos Xamarin. iOS e Xamarin. Android quando as alterações são confirmadas no sistema de gerenciamento de código-fonte._

[A introdução à integração contínua com o Xamarin introduz a](~/tools/ci/intro-to-ci.md) integração contínua como uma prática útil de desenvolvimento de software que fornece o aviso antecipado de código desfeito ou incompatível. O CI permite que os desenvolvedores resolvam problemas e problemas à medida que surgem e mantém o software em um estado adequado para implantação. Este tutorial explica como usar o conteúdo de ambos os documentos juntos.

Este guia mostra como instalar o Jenkins em um computador dedicado que executa o OS X e configurá-lo para ser executado automaticamente quando o computador for inicializado. Após a instalação do Jenkins, instalaremos plugins adicionais para dar suporte ao MS Build. O Jenkins dá suporte ao git pronto para uso. Se o TFS estiver sendo usado para controle de código-fonte, um plug-in adicional e utilitários de linha de comando também deverão ser instalados.

Quando o Jenkins estiver configurado e todos os plugins necessários tiverem sido instalados, criaremos um ou mais trabalhos para compilar os projetos Xamarin. Android e Xamarin. iOS. Um trabalho é uma coleção de etapas e metadados necessários para executar algum trabalho. Um trabalho geralmente consiste no seguinte:

- **SCM (gerenciamento de código-fonte)** – esta é uma entrada de metadados nos arquivos de configuração do Jenkins que contém informações sobre como se conectar ao controle do código-fonte e quais arquivos recuperar.
- **Gatilhos** – os gatilhos são usados para iniciar um trabalho com base em determinadas ações, como quando um desenvolvedor confirma as alterações no repositório de código-fonte.
- **Instruções de Build** – este é um plug-in ou um script que compilará o código-fonte e produzirá um binário que pode ser instalado em dispositivos móveis.
- **Ações de compilação opcionais** – isso pode incluir a execução de testes de unidade, a execução de análise estática no código, o código de assinatura ou a inicialização de outro trabalho para executar outro trabalho relacionado à compilação.
- **Notificações** – um trabalho pode enviar algum tipo de notificação sobre o status de uma compilação.
- **Segurança** – embora opcional, é altamente recomendável que os recursos de segurança do Jenkins também sejam habilitados.

Este guia mostrará como configurar um servidor Jenkins que abrange cada um desses pontos. Ao final de ti, devemos ter uma boa compreensão de como configurar e configurar o Jenkins para criar IPA e APK para nossos projetos móveis do Xamarin.

## <a name="requirements"></a>Requisitos

O servidor de compilação ideal é um computador autônomo dedicado ao único propósito de compilar e possivelmente testar o aplicativo. Um computador dedicado garante que os artefatos que podem ser necessários para outras funções (como a de um servidor Web) não contaminatem a compilação. Por exemplo, se o servidor de compilação também estiver agindo como um servidor Web, o servidor Web poderá exigir uma versão conflitante de alguma biblioteca comum. Devido a esse conflito, o servidor Web pode não funcionar corretamente ou o Jenkins pode criar compilações que não funcionam quando implantadas para os usuários.

O servidor de compilação para aplicativos móveis Xamarin é configurado de forma muito parecida com a estação de trabalho de um desenvolvedor. Ele tem uma conta de usuário na qual Jenkins, Visual Studio para Mac e Xamarin. iOS e Xamarin. Android serão instalados. Todos os certificados de assinatura de código, perfis de provisionamento e repositórios de chaves também devem ser instalados. *Normalmente, a conta de usuário do servidor de compilação é separada das suas contas de desenvolvedor – certifique-se de instalar e configurar todos os softwares, chaves e certificados enquanto estiver conectado com a conta de usuário do servidor de compilação.*

O diagrama a seguir ilustra todos esses elementos em um servidor de compilação Jenkins típico:

[![Este diagrama ilustra todos esses elementos em um servidor de compilação Jenkins típico](jenkins-walkthrough-images/image1.png)](jenkins-walkthrough-images/image1.png#lightbox)

os aplicativos iOS só podem ser compilados e assinados em um computador que esteja executando o macOS. Um Mac mini é uma opção razoável de menor custo, mas qualquer computador capaz de executar o OS X 10,10 (Yosemite) ou superior é suficiente.

Se o TFS estiver sendo usado para controle do código-fonte, você deverá instalar [Team Explorer Everywhere](/azure/devops/java/download-eclipse-plug-in/). O Team Explorer Everywhere fornece acesso de plataforma cruzada ao TFS no terminal no macOS.

[!include[](~/tools/ci/includes/firewall-information.md)]

## <a name="installing-jenkins"></a>Instalando o Jenkins

A primeira tarefa a usar o Jenkins é instalá-lo. Há três maneiras de executar o Jenkins no OS X:

- Como um daemon, em execução em segundo plano.
- Dentro de um contêiner de servlet, como Tomcat, Jetty ou JBoss.
- Como um processo normal em execução em uma conta de usuário.

Os aplicativos de integração contínua mais tradicionais são executados em segundo plano, seja como um daemon (no OS X ou \* Nix) ou como um serviço (no Windows). Isso é adequado para cenários em que não há nenhuma interação de GUI necessária e onde a configuração do ambiente de compilação pode ser facilmente executada. Os aplicativos móveis também exigem repositórios de chaves e certificados de assinatura que podem ser problemáticos de acessar quando o Jenkins está sendo executado como um daemon. Devido a essas preocupações, este documento se concentra no terceiro cenário – executando Jenkins em uma conta de usuário no servidor de compilação.

Jenkins. app é uma maneira útil de instalar o Jenkins. Esse é um wrapper do AppleScript que simplifica o início e a interrupção de um servidor Jenkins. Em vez de executar em um shell bash, o Jenkins é executado como um aplicativo com o ícone no Dock, conforme mostrado na seguinte captura de tela:

[![Em vez de executar em um shell bash, o Jenkins é executado como um aplicativo com o ícone no Dock, conforme mostrado nesta captura de tela](jenkins-walkthrough-images/image2.png)](jenkins-walkthrough-images/image2.png#lightbox)

Iniciar ou parar o Jenkins é tão simples quanto iniciar ou parar o Jenkins. app.

Para instalar o Jenkins. app, baixe a versão mais recente na página de download do projeto, configurada na captura de tela abaixo:

[![, Baixe a versão mais recente na página de download de projetos, configurada nesta captura de tela](jenkins-walkthrough-images/image3.png)](jenkins-walkthrough-images/image3.png#lightbox)

Extraia o arquivo zip para a `/Applications` pasta no servidor de compilação e inicie-o da mesma forma que qualquer outro aplicativo do os X.
Na primeira vez que você iniciar o Jenkins. app, ele apresentará uma caixa de diálogo informando que ele baixará o Jenkins:

[![Aplicativo, ele apresentará uma caixa de diálogo informando que ele baixará o Jenkins](jenkins-walkthrough-images/image4.png)](jenkins-walkthrough-images/image4.png#lightbox)

Quando o download do Jenkins. app for concluído, ele exibirá outra caixa de diálogo perguntando se você deseja personalizar a inicialização do Jenkins, como mostrado na captura de tela a seguir:

[![O download do aplicativo foi concluído, ele exibirá outra caixa de diálogo perguntando se você deseja personalizar a inicialização do Jenkins, como visto nesta captura de tela](jenkins-walkthrough-images/image5.png)](jenkins-walkthrough-images/image5.png#lightbox)

A personalização de Jenkins é opcional e não precisa ser realizada sempre que o aplicativo é iniciado – as configurações padrão para Jenkins funcionarão na maioria das situações.

Se for necessário personalizar o Jenkins, clique no botão **alterar padrões** . Isso apresentará duas caixas de diálogo consecutivas: uma que solicita parâmetros de linha de comando Java e outra que solicita parâmetros de linha de comando Jenkins. As duas capturas de tela a seguir mostram essas duas caixas de diálogo:

[![Esta captura de tela mostra as caixas de diálogo](jenkins-walkthrough-images/image6.png)](jenkins-walkthrough-images/image6.png#lightbox)

[![Esta captura de tela mostra as caixas de diálogo](jenkins-walkthrough-images/image7.png)](jenkins-walkthrough-images/image7.png#lightbox)

Depois que o Jenkins estiver em execução, talvez você queira defini-lo como um item de logon para que ele seja inicializado sempre que o usuário fizer logon no computador. Você pode fazer isso clicando com o botão direito do mouse no ícone de Jenkins no Dock e escolhendo **opções... > abrir no logon**, conforme mostrado na seguinte captura de tela:

[![Você pode fazer isso clicando com o botão direito do mouse no ícone de Jenkins no Dock e escolhendo OptionsOpen no logon, conforme mostrado nesta captura de tela](jenkins-walkthrough-images/image8.png)](jenkins-walkthrough-images/image8.png#lightbox)

Isso fará com que o Jenkins. app seja iniciado automaticamente cada vez que o usuário fizer logon, mas não quando o computador for inicializado. É possível especificar uma conta de usuário que o OS X usará para fazer logon automaticamente no momento da inicialização. Abra as **preferências do sistema**e selecione o ícone **usuários & grupos** , conforme mostrado nesta captura de tela:

[![Abra as preferências do sistema e selecione o ícone grupos de usuários, conforme mostrado nesta captura de tela](jenkins-walkthrough-images/image9.png)](jenkins-walkthrough-images/image9.png#lightbox)

Clique no botão **Opções de logon** e escolha a conta que o os X usará para fazer logon no momento da inicialização.

Neste ponto, o Jenkins foi instalado. No entanto, se quisermos criar aplicativos do Xamarin Mobile, precisaremos instalar alguns plug-ins.

### <a name="installing-plugins"></a>Instalando plug-ins

Quando o instalador do Jenkins. app for concluído, ele iniciará o Jenkins e iniciará o navegador da Web com a URL http://localhost:8080 , conforme mostrado na captura de tela abaixo:

[![8080, conforme mostrado nesta captura de tela](jenkins-walkthrough-images/image10.png)](jenkins-walkthrough-images/image10.png#lightbox)

Nessa página, selecione **Jenkins > gerenciar Jenkins > gerenciar plug-ins** no menu no canto superior esquerdo, conforme mostrado na captura de tela abaixo:

[![Nessa página, selecione Jenkins gerenciar Jenkins gerenciar plug-ins no menu no canto superior esquerdo](jenkins-walkthrough-images/image11.png)](jenkins-walkthrough-images/image11.png#lightbox)

Isso exibirá a página **Jenkins Plugin Manager** . Se você clicar na guia disponível, verá uma lista de mais de 600 plugins que podem ser baixados e instalados. Isso é mostrado na captura de tela abaixo:

[![Se você clicar na guia disponível, verá uma lista de mais de 600 plugins que podem ser baixados e instalados](jenkins-walkthrough-images/image12.png)](jenkins-walkthrough-images/image12.png#lightbox)

Rolar por todos os plugins de 600 para encontrar alguns pode ser entediante e propenso a erros. Jenkins fornece um campo de pesquisa de filtro no canto superior direito da interface. Usar esse campo de filtro para pesquisa simplificará a localização e instalação de um ou todos os seguintes plug-ins:

- **Plug-in do MSBuild do Jenkins** – esse plug-in possibilita a criação de soluções do Visual Studio e Visual Studio para Mac (. sln) e projetos (. csproj).
- **Plug-in de injetador de ambiente** – esse é um plug-in opcional, mas útil que possibilita definir variáveis de ambiente no nível de trabalho e de compilação. Ele também oferece proteção extra para variáveis como as senhas usadas para assinar o código do aplicativo. Às vezes, é abreviado como o  *plug-in EnvInject* .
- **Plug-in Team Foundation Server** – esse é um plug-in opcional que só é necessário se você estiver usando o Team Foundation Server ou Team Foundation Services para controle do código-fonte.

O Jenkins dá suporte ao git sem nenhum plug-in extra.

Depois de instalar todos os plug-ins, você desejará reiniciar o Jenkins e definir as configurações globais para cada plug-in. As configurações globais de um plug-in podem ser encontradas selecionando **Jenkins > gerenciar Jenkins > configurar o sistema** no canto superior esquerdo, conforme mostrado na captura de tela abaixo:

[![As configurações globais de um plug-in podem ser encontradas selecionando Jenkins/gerenciar Jenkins/configurar sistema no canto superior esquerdo](jenkins-walkthrough-images/image13.png)](jenkins-walkthrough-images/image13.png#lightbox)

Ao selecionar essa opção de menu, você será levado para a página **Configurar sistema [Jenkins]** . Esta página contém seções para configurar o próprio Jenkins e definir alguns dos valores globais do plug-in.  A captura de tela abaixo ilustra um exemplo desta página:

[![Esta captura de tela ilustra um exemplo desta página](jenkins-walkthrough-images/image14.png)](jenkins-walkthrough-images/image14.png#lightbox)

#### <a name="configuring-the-msbuild-plugin"></a>Configurando o plug-in do MSBuild

O plug-in do MSBuild deve ser configurado para usar o **/library/frameworks/mono.Framework/Commands/xbuild** para Compilar Visual Studio para Mac arquivos de solução e de projeto. Role para baixo na página **Configurar sistema [Jenkins]** até que o botão **Adicionar MSBuild** seja exibido, conforme mostrado na captura de tela abaixo:

 [![Role para baixo na página Configurar sistema Jenkins até que o botão Adicionar MSBuild seja exibido](jenkins-walkthrough-images/image15.png)](jenkins-walkthrough-images/image15.png#lightbox)

Clique nesse botão e preencha o **nome** e o **caminho** para os campos do **MSBuild** no formulário que aparece. O nome da sua instalação do **MSBuild** deve ser algo significativo, enquanto o **caminho para o MSBuild** deve ser o caminho para `xbuild` , que normalmente é **/library/frameworks/mono.Framework/Commands/xbuild**. Depois de salvar as alterações clicando no botão salvar ou aplicar na parte inferior da página, o Jenkins será capaz de usar `xbuild` para compilar suas soluções.

#### <a name="configuring-the-tfs-plugin"></a>Configurando o plug-in do TFS

Esta seção é obrigatória se você pretende usar o TFS para o controle do código-fonte.

Para que uma estação de trabalho macOS interaja com um servidor TFS, [Team Explorer Everywhere](/azure/devops/java/download-eclipse-plug-in/) deve ser instalada na estação de trabalho. Team Explorer Everywhere é um conjunto de ferramentas da Microsoft que inclui um cliente de linha de comando de plataforma cruzada para acessar o TFS. Team Explorer Everywhere pode ser baixado da Microsoft e instalado em três etapas:

1. Descompacte o arquivo morto em um diretório que possa ser acessado pela conta de usuário. Por exemplo, você pode descompactar o arquivo para **~/tee**.
2. Configure o Shell ou o caminho do sistema para incluir a pasta que contém os arquivos que foram descompactados na etapa um acima. Por exemplo,

    ```
    echo export PATH~/tee/:$PATH' >> ~/.bash_profile
    ```

3. Para confirmar se o Team Explorer Everywhere está instalado, abra uma sessão de terminal e execute o `tf` comando. Se o tf estiver configurado corretamente, você verá a seguinte saída em sua sessão de terminal:

    ```
    $ tf
    Team Explorer Everywhere Command Line Client (version 11.0.0.201306181526)

    Available commands and their options:
    ```

Depois que o cliente de linha de comando para TFS for instalado, Jenkins deverá ser configurado com o caminho completo para o `tf` cliente de linha de comando. Role para baixo a página **Configurar sistema [Jenkins]** até encontrar a seção Team Foundation Server, conforme mostrado na seguinte captura de tela:

[![Role para baixo na página Configurar sistema Jenkins até encontrar a seção Team Foundation Server](jenkins-walkthrough-images/image17.png)](jenkins-walkthrough-images/image17.png#lightbox)

Insira o caminho completo para o `tf` comando e clique no botão **salvar** .

### <a name="configure-jenkins-security"></a>Configurar a segurança do Jenkins

Quando instalado pela primeira vez, o Jenkins tem segurança desabilitada, portanto, é possível que qualquer usuário configure e execute qualquer tipo de trabalho anonimamente. Esta seção aborda como configurar a segurança usando o banco de dados de usuário do Jenkins para configurar a autenticação e a autorização.

As configurações de segurança podem ser encontradas selecionando **Jenkins > gerenciar Jenkins > configurar a segurança global**, conforme mostrado nesta captura de tela:

[![As configurações de segurança podem ser encontradas selecionando Jenkins/gerenciar Jenkins/configurar segurança global](jenkins-walkthrough-images/image18.png)](jenkins-walkthrough-images/image18.png#lightbox)

Na página **Configurar segurança global** , marque a caixa de seleção **habilitar segurança** e o formulário **controle de acesso** deve aparecer, semelhante à próxima captura de tela:

[![Na página Configurar segurança global, marque a caixa de seleção Habilitar segurança e o formulário controle de acesso deve aparecer, semelhante a esta captura de tela](jenkins-walkthrough-images/image19.png)](jenkins-walkthrough-images/image19.png#lightbox)

Alterne o botão de opção do **próprio banco de dados de usuário do Jenkins** na **seção realm de segurança**e verifique se a opção permitir que **os usuários se inscrevam** também está marcada, conforme ilustrado na captura de tela a seguir:

[![Alterne o botão de opção do banco de dados de usuário do Jenkins próprio na seção realm de segurança e verifique se a opção permitir que os usuários se inscrevam também está marcada](jenkins-walkthrough-images/image20.png)](jenkins-walkthrough-images/image20.png#lightbox)

Por fim, reinicie o Jenkins e crie uma nova conta. A primeira conta criada é a conta raiz e essa conta será promovida automaticamente a um administrador. Navegue de volta para a página **Configurar segurança global** e marque o botão de opção **segurança baseada em matriz** . A conta raiz deve receber acesso completo e a conta anônima deve receber acesso somente leitura, conforme mostrado na seguinte captura de tela:

[![A conta raiz deve receber acesso completo e a conta anônima deve receber acesso somente leitura](jenkins-walkthrough-images/image21.png)](jenkins-walkthrough-images/image21.png#lightbox)

Depois que essas configurações forem salvas e o Jenkins for reiniciado, a segurança será ativada.

#### <a name="disabling-security"></a>Desabilitando a segurança

No caso de uma senha esquecida ou de um bloqueio em todo o Jenkins, é possível desabilitar a segurança seguindo estas etapas:

1. Pare o Jenkins. Se você estiver usando o Jenkins. app, poderá fazer isso clicando com o botão direito do mouse no ícone Jenkins. app no Dock e selecionando sair no menu que aparece:

    ![Ícone do aplicativo no Dock e selecionando sair no menu que aparece](jenkins-walkthrough-images/image19.png)

2. Abra o arquivo **~/.jenkins/config.xml** em um editor de texto.
3. Altere o valor do `<usesecurity></usesecurity>` elemento de `true` para `false` .
4. Exclua os `<authorizationstrategy></authorizationstrategy>` `<securityrealm></securityrealm>` elementos e do arquivo.
5. Reinicie o Jenkins.

## <a name="setting-up-a-job"></a>Configurando um trabalho

No nível superior, o Jenkins organiza todas as várias tarefas necessárias para criar software em um *trabalho*. Um trabalho também tem metadados associados a ele, fornecendo informações sobre a compilação, como obter o código-fonte, com que frequência a compilação deve ser executada, quaisquer variáveis especiais necessárias para a compilação e como notificar os desenvolvedores se a compilação falhar.

Os trabalhos são criados selecionando **Jenkins > novo trabalho** no menu no canto superior direito, conforme mostrado na seguinte captura de tela:

![Os trabalhos são criados selecionando Jenkins novo trabalho no menu no canto superior direito](jenkins-walkthrough-images/image22.png)

Isso exibirá a página **novo trabalho [Jenkins]** . Insira um nome para o trabalho e selecione o botão de opção **criar um projeto de software de estilo livre** . A captura de tela a seguir mostra um exemplo disso:

![Insira um nome para o trabalho e selecione o botão de opção criar um projeto de software de estilo livre](jenkins-walkthrough-images/image23.png)

Clicar no botão **OK** apresenta a página de configuração para o trabalho. Isso deve ser semelhante à captura de tela a seguir:

![Isso deve ser semelhante a esta captura de tela](jenkins-walkthrough-images/image24.png)

Jenkins organiza trabalhos em um diretório no disco rígido localizado no seguinte caminho: **~/.Jenkins/Jobs/[nome do trabalho]**

Essa pasta contém todos os arquivos e artefatos específicos para o trabalho, como logs, arquivos de configuração e o código-fonte que precisa ser compilado.

Depois que o trabalho inicial tiver sido criado, ele deverá ser configurado com um ou mais dos seguintes itens:

- O sistema de gerenciamento de código-fonte deve ser especificado.
- Uma ou mais *ações de compilação* devem ser adicionadas ao projeto. Estas são as etapas ou tarefas necessárias para criar o aplicativo.
- O trabalho deve ser atribuído a um *gatilho de compilação* – um conjunto de instruções que informam Jenkins com que frequência recuperar o código e criar o projeto final.

### <a name="configuring-source-code-control"></a>Configurando o controle do código-fonte

A primeira tarefa que o Jenkins faz é recuperar o código-fonte do sistema de gerenciamento de código-fonte. O Jenkins dá suporte a muitos dos sistemas de gerenciamento de código-fonte populares disponíveis atualmente. Esta seção aborda dois sistemas populares, git e Team Foundation Server. Cada um desses sistemas de gerenciamento de código-fonte é discutido mais detalhadamente nas seções a seguir.

#### <a name="using-git-for-source-code-control"></a>Usando o Git para controle do código-fonte

Se você estiver usando o TFS para controle do código-fonte, [pule](#using-tfs-for-source-code-management) esta seção e vá para a próxima seção usando o TFS.

O Jenkins dá suporte ao git pronto para uso – nenhum plug-in extra é necessário. Para usar o Git, clique no botão de opção **git** e insira a URL para o repositório git, conforme mostrado na seguinte captura de tela:

![Para usar o Git, clique no botão de opção git e insira a URL para o repositório git](jenkins-walkthrough-images/image25.png)

Depois que as alterações forem salvas, a configuração do git será concluída.

#### <a name="using-tfs-for-source-code-management"></a>Usando o TFS para o gerenciamento de código-fonte

Esta seção se aplica somente aos usuários do TFS.

Clique no botão de opção **Team Foundation Server** e a seção configuração do TFS deverá aparecer, semelhante ao que está na seguinte captura de tela:

![Clique no botão de opção Team Foundation Server e a seção configuração do TFS deverá aparecer](jenkins-walkthrough-images/image26.png)

Forneça as informações necessárias para o TFS. A captura de tela a seguir mostra um exemplo do formulário concluído:

![Esta captura de tela mostra um exemplo do formulário concluído](jenkins-walkthrough-images/image27.png)

#### <a name="testing-the-source-code-control-configuration"></a>Testando a configuração de controle do código-fonte

Depois que o controle de código-fonte apropriado tiver sido configurado, clique em **salvar** para salvar as alterações. Isso retornará à home page para o trabalho, que será semelhante à captura de tela a seguir:

![Isso retornará à home page para o trabalho, que será semelhante a esta captura de tela](jenkins-walkthrough-images/image28.png)

A maneira mais simples de validar que o controle do código-fonte está configurado corretamente é disparar uma compilação manualmente, mesmo que não haja nenhuma ação de compilação especificada. Para iniciar uma compilação manualmente, o home page do trabalho tem um link **criar agora** no menu à esquerda, conforme mostrado na captura de tela abaixo:

![Para iniciar uma compilação manualmente, a home page do trabalho tem um link criar agora no menu no lado esquerdo](jenkins-walkthrough-images/image29.png)

Quando uma compilação é iniciada, a caixa de diálogo histórico de compilação exibe um círculo azul piscando, uma barra de progresso, o número da compilação e a hora em que a compilação foi iniciada, semelhante à captura de tela a seguir:

![Quando uma compilação é iniciada, a caixa de diálogo histórico de compilação exibe um círculo azul piscando, uma barra de progresso, o número da compilação e a hora em que a compilação foi iniciada](jenkins-walkthrough-images/image30.png)

Se o trabalho for executado com sucesso, um círculo azul será exibido. Se o trabalho falhar, um círculo vermelho será exibido.

Para ajudar na solução de problemas que possam surgir como parte da compilação, o Jenkins capturará toda a saída do console para o trabalho. Para ver a saída do console, clique no trabalho no **histórico de compilação**e, em seguida, no link **saída do console** , no menu à esquerda. A captura de tela a seguir mostra o link de **saída do console** , bem como algumas das saídas de um trabalho bem-sucedido:

![Esta captura de tela mostra o link de saída do console, bem como algumas das saídas de um trabalho bem-sucedido](jenkins-walkthrough-images/image31.png)

#### <a name="location-of-build-artifacts"></a>Local dos artefatos de compilação

Jenkins recuperará todo o código-fonte em uma pasta especial chamada *espaço de trabalho*. Esse diretório pode ser encontrado dentro da pasta no seguinte local:

```
~/.jenkins/jobs/[JOB NAME]/workspace
```

O caminho para o espaço de trabalho será armazenado em uma variável de ambiente chamada `$WORKSPACE` .

É possível procurar a pasta do espaço de trabalho em Jenkins navegando até a página de aterrissagem de um trabalho e, em seguida, clicando no link **espaço de trabalho** no menu à esquerda. A captura de tela a seguir mostra um exemplo do espaço de trabalho para um trabalho chamado **HelloWorld**:

![Esta captura de tela mostra um exemplo do espaço de trabalho para um trabalho chamado HelloWorld](jenkins-walkthrough-images/image32.png)

### <a name="build-triggers"></a>Gatilhos de compilação

Há várias estratégias diferentes para iniciar compilações no Jenkins – elas são conhecidas como *gatilhos de Build*. Um gatilho de compilação ajuda a Jenkins a decidir quando iniciar um trabalho e compilar o projeto. Dois dos gatilhos de compilação mais comuns são:

- **Compilar periodicamente** – esse gatilho faz com que o Jenkins inicie um trabalho em intervalos especificados, como a cada duas horas ou à meia-noite em dias da semana. A compilação será iniciada independentemente de houverem alterações no repositório do código-fonte.
- **Sondar SCM** – esse gatilho sondará o controle do código-fonte regularmente. Se alguma alteração tiver sido confirmada no repositório de código-fonte, o Jenkins iniciará uma nova compilação.

O SCM de sondagem é um gatilho popular porque fornece comentários rápidos quando um desenvolvedor confirma as alterações que fazem com que a compilação seja interrompida. Isso é útil para as equipes de alerta de que algum código confirmado recentemente está causando problemas e permite que os desenvolvedores resolvam o problema, enquanto as alterações ainda estão atualizadas em mente.

Compilações periódicas são geralmente usadas para criar uma versão do aplicativo que pode ser distribuída para testadores. Por exemplo, um Build periódico pode ser agendado para sexta-feira à noite para que os membros da equipe de QA possam testar o trabalho da semana anterior.

### <a name="compiling-a-xamarinios-applications"></a>Compilando aplicativos Xamarin. iOS
Os projetos do Xamarin. iOS podem ser compilados na linha de comando usando `xbuild` ou `msbuild` . O comando do shell será executado no contexto da conta de usuário que está executando o Jenkins. É importante que a conta de usuário tenha acesso ao perfil de provisionamento para que o aplicativo possa ser adequadamente empacotado para distribuição. É possível adicionar esse comando do Shell à página de configuração do trabalho.

Role para baixo até a seção **Build** . Clique no botão **Adicionar etapa de compilação** e selecione **executar Shell**, conforme ilustrado pela seguinte captura de tela:

![Clique no botão Adicionar etapa de compilação e selecione executar Shell](jenkins-walkthrough-images/image33.png)

[!include[](~/tools/ci/includes/commandline-compile-of-xamarin-ios-ipa.md)]

### <a name="building-a-xamarinandroid-project"></a>Criando um projeto Xamarin. Android

Criar um projeto Xamarin. Android é muito semelhante ao conceito de criar um projeto Xamarin. iOS. Para criar um APK de um projeto Xamarin. Android, o Jenkins deve ser configurado para executar as duas etapas a seguir:

- Compilar o projeto usando o plug-in do MSBuild
- Assine e zip alinhe o APK com um repositório de chaves de versão válido.

Essas duas etapas serão abordadas com mais detalhes nas próximas duas seções.

### <a name="creating-the-apk"></a>Criando o APK

Clique no botão **Adicionar etapa de compilação** e selecione **criar um projeto ou solução do Visual Studio usando o MSBuild**, conforme mostrado na captura de tela abaixo:

![Criando o APK clique no botão Adicionar etapa de compilação e selecione criar um projeto ou solução do Visual Studio usando o MSBuild](jenkins-walkthrough-images/image36.png)

Depois que a etapa de compilação for adicionada ao projeto, preencha os campos de formulário exibidos. A captura de tela a seguir é um exemplo do formulário concluído:

![Depois que a etapa de compilação for adicionada ao projeto, preencha os campos de formulário que aparecem](jenkins-walkthrough-images/image37.png)

Essa etapa de compilação será executada `xbuild` na pasta **$Workspace** . O arquivo de compilação do MSBuild é definido como o arquivo **Xamarin. Android. csproj** . Os **argumentos de linha de comando** especificam uma compilação de versão do **PackageForAndroid**de destino. O produto desta etapa será um APK no seguinte local:

```
$WORKSPACE/[PROJECT NAME]/bin/Release
```

A captura de tela a seguir mostra um exemplo desse APK:

![Esta captura de tela mostra um exemplo dessa APK](jenkins-walkthrough-images/image38.png)

Este APK não está pronto para implantação, pois não foi assinado com um keystore privado e deve estar alinhado em zip.

#### <a name="signing-and-zipaligning-the-apk-for-release"></a>Assinando e Zipaligningndo o APK da versão

Assinar e zipaligningr os APK são tecnicamente duas tarefas separadas que são executadas por duas ferramentas de linha de comando separadas do SDK do Android. No entanto, é conveniente executá-los em uma ação de compilação. Para obter mais informações sobre como assinar e zipaligning um APK, consulte a documentação do Xamarin sobre como preparar um aplicativo Android para liberação.

Ambos os comandos exigem parâmetros de linha de comando que podem variar de projeto para projeto. Além disso, alguns desses parâmetros de linha de comando são senhas que não devem aparecer na saída do console quando a compilação está em execução. Armazenaremos alguns desses parâmetros de linha de comando em variáveis de ambiente. As variáveis de ambiente necessárias para a assinatura e/ou o alinhamento do zip são descritas na tabela a seguir:

|Variável de ambiente|Descrição|
|--- |--- |
|KEYSTORE_FILE|Este é o caminho para o keystore para assinar o APK|
|KEYSTORE_ALIAS|A chave no repositório de chaves que será usada para assinar o APK.|
|INPUT_APK|O APK que é criado pelo `xbuild` .|
|SIGNED_APK|O APK assinado produzido por `jarsigner` .|
|FINAL_APK|Esse é o APK alinhado em zip que é produzido pelo `zipalign` .|
|STORE_PASS|Essa é a senha usada para acessar o conteúdo do keystore para assinar o arquivo.|

Conforme descrito na seção requisitos, essas variáveis de ambiente podem ser definidas durante a compilação usando o plug-in EnvInject. O trabalho deve ter uma nova etapa de compilação adicionada com base nas variáveis de ambiente de inserção, conforme mostrado na próxima captura de tela:

![O trabalho deve ter uma nova etapa de compilação adicionada com base nas variáveis de ambiente de inserção](jenkins-walkthrough-images/image39.png)

No campo formulário de **conteúdo de propriedades** que será exibido, as variáveis de ambiente são adicionadas, uma por linha, no seguinte formato:

```
ENVIRONMENT_VARIABLE_NAME = value
```

A captura de tela a seguir mostra as variáveis de ambiente que são necessárias para assinar o APK:

![Esta captura de tela mostra as variáveis de ambiente necessárias para assinar o APK](jenkins-walkthrough-images/image40.png)

Observe que algumas das variáveis de ambiente para os arquivos APK são criadas na `WORKSPACE` variável de ambiente.

A variável de ambiente final é a senha para acessar o conteúdo do keystore: `STORE_PASS` . As senhas são valores confidenciais que devem ser obscurecidos ou omitidos em arquivos de log. O plug-in EnvInject pode ser configurado para proteger esses valores para que eles não sejam mostrados nos logs.

Imediatamente antes da seção **Build** da configuração do trabalho é uma seção **ambiente de compilação** . Quando a caixa de seleção **injetar senhas** for alternada, alguns campos de formulário serão exibidos. Esses campos de formulário são usados para capturar o nome e o valor da variável de ambiente. A captura de tela a seguir é um exemplo de adição da `STORE_PASS` variável de ambiente:

![Esta captura de tela é um exemplo de adição da variável de ambiente STOREPASS](jenkins-walkthrough-images/image41.png)

Depois que as variáveis de ambiente tiverem sido inicializadas, a próxima etapa será adicionar uma etapa de compilação para assinar e compactar o APK. Imediatamente após a etapa de Build para inserir as variáveis de ambiente será outra compilação de comando **executar Shell** que será executada `jarsigner` e `zipalign` . Cada comando ocupará uma linha, conforme mostrado no trecho a seguir:

```
jarsigner -verbose -sigalg MD5withRSA -digestalg SHA1 -keystore $KEYSTORE_FILE -storepass $STORE_PASS -signedjar $SIGNED_APK $INPUT_APK $KEYSTORE_ALIAS
zipalign -f -v 4 $SIGNED_APK $FINAL_APK
```

A captura de tela a seguir mostra um exemplo de como inserir os `jarsigner` `zipalign` comandos e na etapa:

![Esta captura de tela mostra um exemplo de como inserir os comandos jarsigner e zipalign na etapa](jenkins-walkthrough-images/image42.png)

Depois que todas as ações de compilação estiverem em vigor, é uma boa prática disparar uma compilação manual para verificar se tudo está funcionando. Se a compilação falhar, a **saída do console** deverá ser examinada em busca de informações sobre o que causou a falha da compilação.

### <a name="submitting-tests-to-test-cloud"></a>Enviando testes para Test Cloud

Os testes automatizados podem ser enviados para Test Cloud usando comandos do Shell. Para obter mais informações sobre como configurar uma execução de teste no Xamarin Test Cloud, consulte [preparando aplicativos xamarin. Android](/appcenter/test-cloud/preparing-for-upload/xamarin-android-uitest) e [preparando aplicativos xamarin. Ios](/appcenter/test-cloud/preparing-for-upload/xamarin-ios-uitest).

## <a name="summary"></a>Resumo

Neste guia, introduzimos o Jenkins como um servidor de Build no macOS e o configuramos para compilar e preparar os aplicativos do Xamarin Mobile para o lançamento. Instalamos o Jenkins em um computador macOS junto com vários plug-ins para dar suporte ao processo de compilação. Criamos e configuramos um trabalho que receberá o código do TFS ou do git e, em seguida, compilará esse código em um aplicativo pronto para liberação. Também exploramos duas maneiras diferentes de agendar quando os trabalhos devem ser executados.

## <a name="related-links"></a>Links Relacionados

- [Integração contínua](~/tools/ci/index.md)
- [Teste do App Center](/appcenter/test-cloud/)