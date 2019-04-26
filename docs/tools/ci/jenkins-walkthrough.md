---
title: Usando Jenkins com Xamarin
description: Este documento descreve como usar o Jenkins para integração contínua com aplicativos Xamarin. Ele aborda como instalar, configurar e usar o Jenkins.
ms.prod: xamarin
ms.assetid: 1E6825DF-1254-4FCB-B94D-ADD33D1B5309
author: lobrien
ms.author: laobri
ms.date: 03/23/2017
ms.openlocfilehash: 2e6a75fa3c4c63e8dea402c6761f8ef753908540
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61047422"
---
# <a name="using-jenkins-with-xamarin"></a>Usando Jenkins com Xamarin

_Este guia mostra como configurar o Jenkins como um servidor de integração contínua e automatizar a compilação de aplicativos móveis criados com Xamarin. Ele descreve como instalar o Jenkins nos X, configurá-lo e configurar os trabalhos para compilar aplicativos xamarin. IOS e xamarin. Android quando as alterações são confirmadas para o sistema de gerenciamento de código-fonte._

[Introdução à integração contínua com o Xamarin](~/tools/ci/intro-to-ci.md) apresenta a integração contínua como uma prática de desenvolvimento de software útil que fornece avisos antecipados do código quebrado ou incompatível. CI permite aos desenvolvedores solucionar problemas e problemas conforme eles surgem e mantém o software em um estado adequado para a implantação. Este passo a passo aborda como usar o conteúdo de ambos os documentos em conjunto.

Este guia mostra como instalar o Jenkins em um computador dedicado executando o OS X e configurá-lo para ser executado automaticamente quando o computador é inicializado. Depois de instalar o Jenkins, instalamos plug-ins adicionais para dar suporte a MS Build. Jenkins dá suporte ao Git fora da caixa. Se o TFS está sendo usado para controle do código-fonte, um outros utilitários de linha de comando e plug-in também devem ser instalado.

Depois que o Jenkins estiver configurado e qualquer plug-ins necessários foram instalados, vamos criar um ou mais trabalhos para compilar os projetos xamarin. Android e xamarin. IOS. Um trabalho é um conjunto de etapas e os metadados necessários para executar algum trabalho. Um trabalho normalmente consiste no seguinte:

- **Código de SCM (gerenciamento) da fonte** – essa é uma entrada de metadados nos arquivos de configuração do Jenkins que contém informações sobre como conectar-se ao controle do código-fonte e de quais arquivos para recuperar.
- **Gatilhos** – gatilhos são usados para iniciar um trabalho com base em determinadas ações, como quando um desenvolvedor confirmar alterações no repositório de código fonte.
- **Instruções de Build** – este é um plug-in ou um script que compilará o código-fonte e produzem um binário que pode ser instalado em dispositivos móveis.
- **Ações de Build opcionais** – isso pode incluir testes de unidade, executar análise estática no código, assinatura de código, ou iniciando outro trabalho para realizar outras criar trabalho relacionado.
- **Notificações** – um trabalho pode enviar algum tipo de notificação sobre o status de uma compilação.
- **Segurança** – Embora seja opcional, é altamente recomendável que os recursos de segurança do Jenkins também esteja habilitada.

Este guia explica como configurar um servidor Jenkins que abrangem cada um desses pontos. No final dele, podemos deve ter uma boa compreensão de como instalar e configurar o Jenkins para criar o IPA e APKS para os nossos projetos móveis do Xamarin.

## <a name="requirements"></a>Requisitos

O servidor de compilação ideal é um computador autônomo dedicado para o único propósito de criação e, possivelmente, teste o aplicativo. Um computador dedicado garante que os artefatos que podem ser necessários para outras funções (como aquele de um servidor web) não contaminar a compilação. Por exemplo, se o servidor de compilação também estiver atuando como um servidor web, o servidor web pode exigir uma versão conflitante do alguma biblioteca comum. Devido a esse conflito de servidor web pode não funcionar corretamente ou Jenkins pode criar compilações que não funcionam quando implantado em usuários.

O servidor de compilação para aplicativos móveis do Xamarin é configurar de maneira muito semelhante a estação de trabalho do desenvolvedor. Ele tem uma conta de usuário no qual Jenkins, Visual Studio para Mac, e xamarin. IOS e xamarin. Android serão instalado. Todos o certificados, repositórios de chaves e perfis de provisionamento de assinatura de código devem ser instalados também. *Normalmente a conta de usuário do servidor de compilação é separada de suas contas de desenvolvedor - Certifique-se de instalar e configurar o software, as chaves e certificados, enquanto estiver conectado com a conta de usuário do servidor de compilação.*

O diagrama a seguir ilustra a todos esses elementos em um servidor de compilação Jenkins típico:

[![](jenkins-walkthrough-images/image1.png "Este diagrama ilustra a todos esses elementos em um servidor típico de build do Jenkins")](jenkins-walkthrough-images/image1.png#lightbox)

aplicativos do iOS só podem ser compilados e assinados em um computador executando o macOS. Um Mac Mini é uma opção de menor custo razoável, mas qualquer computador capaz de executar o OS X 10.10 (Yosemite) ou superior é suficiente.

Se o TFS está sendo usado para controle do código-fonte, você vai querer instalar [Team Explorer Everywhere](https://docs.microsoft.com/azure/devops/java/download-eclipse-plug-in/). Team Explorer Everywhere fornece acesso de plataforma cruzada para o TFS no Terminal no macOS.

[!include[](~/tools/ci/includes/firewall-information.md)]

## <a name="installing-jenkins"></a>Instalar o Jenkins

É a primeira tarefa para usar o Jenkins para instalá-lo. Há três maneiras de executá-lo em OS x:

- Como um daemon, está em execução em segundo plano.
- Dentro de um contêiner de servlet, como o Tomcat, Jetty ou JBoss.
- Como um processo normal em execução sob uma conta de usuário.

Os aplicativos mais tradicionais de integração contínua são executados em segundo plano, como um daemon (nos X ou \*nix) ou como um serviço (no Windows). Isso é adequado para cenários onde há nenhuma interação da interface gráfica do usuário necessária e onde a configuração do ambiente de compilação pode ser executada facilmente. Aplicativos móveis também exigem o repositório de chaves e certificados que podem ser um problemas para o acesso ao Jenkins está em execução como um daemon de assinatura. Devido a essas preocupações, este documento enfoca o terceiro cenário – Jenkins em execução sob uma conta de usuário no servidor de compilação.

Jenkins.App é uma maneira prática para instalar o Jenkins. Isso é um wrapper AppleScript que simplifica o início e parada de um servidor Jenkins. Em vez de executar em um shell bash, Jenkins é executado como um aplicativo com o ícone no encaixe, conforme mostrado na seguinte captura de tela:

[![](jenkins-walkthrough-images/image2.png "Em vez de executar em um shell bash, Jenkins é executado como um aplicativo com o ícone no encaixe, conforme mostrado nesta captura de tela")](jenkins-walkthrough-images/image2.png#lightbox)

Iniciando ou parando Jenkins é tão simple quanto iniciando ou parando Jenkins.App.

Para instalar Jenkins.App, baixe a versão mais recente na página de download do projeto, mostrada na captura de tela abaixo:

[![](jenkins-walkthrough-images/image3.png "Aplicativo de página, como mostrada nesta captura de tela de baixar a versão mais recente dos projetos de download")](jenkins-walkthrough-images/image3.png#lightbox)

Extraia o arquivo zip para o `/Applications` pasta no servidor de compilação e start-lo exatamente como qualquer outro aplicativo OS X.
Na primeira vez que você iniciar Jenkins.App, ele apresentará uma caixa de diálogo informando que ele baixará Jenkins:

[![](jenkins-walkthrough-images/image4.png "Aplicativo, ele apresentará uma caixa de diálogo informando que ele baixará o Jenkins")](jenkins-walkthrough-images/image4.png#lightbox)

Quando Jenkins.App tiver terminado o download, ele exibirá outra caixa de diálogo perguntando se você deseja personalizar a inicialização do Jenkins, conforme mostrado na seguinte captura de tela:

[![](jenkins-walkthrough-images/image5.png "Aplicativo tiver terminado o download, ela exibirá outra caixa de diálogo perguntando se você deseja personalizar a inicialização do Jenkins, conforme mostrado nesta captura de tela")](jenkins-walkthrough-images/image5.png#lightbox)

Personalizar o Jenkins é opcional e não precisa ser executada sempre que o aplicativo é iniciado – as configurações padrão para o Jenkins funcionarão na maioria das situações.

Se for necessário personalizar Jenkins, clique no **alterar padrões** botão. Isso apresentará duas caixas de diálogo consecutivas: um que solicita parâmetros de linha de comando do Java e outro que solicita parâmetros de linha de comando do Jenkins. As duas capturas de tela a seguir mostram duas caixas de diálogo:

[![](jenkins-walkthrough-images/image6.png "Esta captura de tela mostra as caixas de diálogo")](jenkins-walkthrough-images/image6.png#lightbox)

[![](jenkins-walkthrough-images/image7.png "Esta captura de tela mostra as caixas de diálogo")](jenkins-walkthrough-images/image7.png#lightbox)

Depois que o Jenkins está em execução, convém defini-lo como um item de logon para que ele iniciará a cada vez que o usuário faz logon no computador. Você pode fazer isso clicando duas vezes no ícone do Jenkins no Dock e escolhendo **opções... > Abrir no logon**, conforme mostrado na seguinte captura de tela:

[![](jenkins-walkthrough-images/image8.png "Você pode fazer isso clicando duas vezes no ícone do Jenkins no Dock e escolhendo OptionsOpen no logon, conforme mostrado nesta captura de tela")](jenkins-walkthrough-images/image8.png#lightbox)

Isso fará com que Jenkins.App iniciar automaticamente sempre que o usuário fizer logon, mas não quando o computador é inicializado. É possível especificar uma conta de usuário que OS X usará para automaticamente fazer logon no momento da inicialização. Abra o **preferências do sistema**e selecione o **usuários e grupos** ícone conforme mostrado nesta captura de tela:

[![](jenkins-walkthrough-images/image9.png "Abra as preferências do sistema e selecione o ícone de grupos de usuários, conforme mostrado nesta captura de tela")](jenkins-walkthrough-images/image9.png#lightbox)

Clique no **opções de logon** botão e, em seguida, escolha a conta que o OS X usará para logon no momento da inicialização.

Neste ponto Jenkins foi instalado. No entanto, se quisermos criar aplicativos móveis do Xamarin, precisamos instalar alguns plug-ins.

### <a name="installing-plugins"></a>Instalar o plug-ins

Quando o instalador Jenkins.App for concluída, ele começará a Jenkins e iniciar o navegador da web com a URL http://localhost:8080, conforme mostrado na captura de tela abaixo:

[![](jenkins-walkthrough-images/image10.png "8080, conforme mostrado nesta captura de tela")](jenkins-walkthrough-images/image10.png#lightbox)

Nessa página, selecione **Jenkins > Gerenciar Jenkins > Gerenciar plug-ins** no menu no canto superior esquerdo, conforme mostrado na captura de tela abaixo:

[![](jenkins-walkthrough-images/image11.png "Nessa página, selecione Gerenciar Jenkins gerenciar plug-ins Jenkins no menu no canto superior esquerdo")](jenkins-walkthrough-images/image11.png#lightbox)

Isso exibirá a **Gerenciador de plug-in do Jenkins** página. Se você clicar na guia disponível, você verá uma lista de mais de 600 plug-ins que podem ser baixados e instalados. Isso é mostrado na captura de tela abaixo:

[![](jenkins-walkthrough-images/image12.png "Se você clicar na guia disponível, você verá uma lista de mais de 600 plug-ins que podem ser baixados e instalados")](jenkins-walkthrough-images/image12.png#lightbox)

Rolar por todos os plug-ins para localizar que algumas podem ser entediantes e propenso a 600. Jenkins fornece um campo de pesquisa do filtro no canto superior direito da interface. Usando este campo de filtro para pesquisar simplificará a localização e instalado de um ou todos os seguintes plug-ins:

- **Plug-in do Jenkins MSBuild** – esse plug-in torna possível criar o Visual Studio e o Visual Studio para soluções de Mac (. sln) e projetos (. csproj).
- **Plug-in Injetor do ambiente** – isso é um opcional mas útil plug-in que torna possível definir variáveis de ambiente em que o trabalho e compilar o nível. Ele também oferece proteção extra para variáveis, como as senhas usadas para o código de assinar o aplicativo. Ele é, às vezes, abreviado como o *EnvInject Plugin* .
- **Team Foundation Server Plugin** – isso é um plug-in opcional que só é necessária se você estiver usando o Team Foundation Server ou o Team Foundation Services para controle do código-fonte.

Jenkins dá suporte ao Git sem qualquer plug-ins extras.

Depois de instalar todos os plug-ins, você vai querer reinicie o Jenkins e defina as configurações globais para cada plug-in. As configurações globais para um plug-in podem ser encontradas selecionando **Jenkins > Gerenciar Jenkins > configurar o sistema** no canto superior esquerdo, conforme mostrado na captura de tela abaixo:

[![](jenkins-walkthrough-images/image13.png "As configurações globais para um plug-in podem ser encontradas selecionando o Jenkins / gerenciar Jenkins / configurar o sistema do canto superior esquerdo entregar canto")](jenkins-walkthrough-images/image13.png#lightbox)

Quando você seleciona essa opção de menu, você será levado para o **configurar o sistema [Jenkins]** página. Esta página contém seções para configurar o Jenkins em si e para definir alguns dos valores globais de plug-in.  Captura de tela abaixo ilustra um exemplo desta página:

[![](jenkins-walkthrough-images/image14.png "Esta captura de tela ilustra um exemplo desta página")](jenkins-walkthrough-images/image14.png#lightbox)

#### <a name="configuring-the-msbuild-plugin"></a>Configurar o plug-in do MSBuild

O plug-in do MSBuild deve ser configurado para usar **/Library/Frameworks/Mono.framework/Commands/xbuild** para compilar o Visual Studio para arquivos de solução e projeto do Mac. Role para baixo a **configurar o sistema [Jenkins]** página até que o **adicionar MSBuild** botão é exibido, conforme mostrado na captura de tela abaixo:

 [![](jenkins-walkthrough-images/image15.png "Role para baixo na página Configurar sistema Jenkins até que apareça no botão Adicionar MSBuild")](jenkins-walkthrough-images/image15.png#lightbox)

Clique neste botão e preencha a **nome** e **caminho** para **MSBuild** campos no formulário que aparece. O nome do seu **MSBuild** instalação deve ser algo significativo, enquanto o **caminho do MSBuild** deve ser o caminho para `xbuild`, que é normalmente **/Library/estruturas / Mono.framework/Commands/xbuild**. Depois de salvar as alterações clicando em Salvar ou no botão Aplicar na parte inferior da página, Jenkins poderão usar `xbuild` para compilar suas soluções.

#### <a name="configuring-the-tfs-plugin"></a>Configurar o plug-in do TFS

Esta seção é obrigatória se você pretende usar o TFS para o controle do código fonte.

Para que uma estação de trabalho do macOS interagir com um servidor do TFS [Team Explorer Everywhere](https://docs.microsoft.com/azure/devops/java/download-eclipse-plug-in/) deve ser instalado na estação de trabalho. Team Explorer Everywhere é um conjunto de ferramentas da Microsoft que inclui um cliente de linha de comando de plataforma cruzada para acessar o TFS. Team Explorer Everywhere pode ser baixado da Microsoft e instalado em três etapas:

1. Descompacte o arquivo morto para um diretório acessível à conta de usuário. Por exemplo, você pode descompactar o arquivo para **~/tee**.
2. Configure o caminho do sistema ou shell para incluir a pasta que contém os arquivos que foram descompactados na etapa acima. Por exemplo,

    ```
    echo export PATH~/tee/:$PATH' >> ~/.bash_profile
    ```

3. Para confirmar que o Team Explorer Everywhere está instalado, abra uma sessão de terminal e execute o `tf` comando. Sua sessão de terminal se tf está configurado corretamente, você verá a seguinte saída:

    ```
    $ tf
    Team Explorer Everywhere Command Line Client (version 11.0.0.201306181526)

    Available commands and their options:
    ```

Depois de instalar o cliente de linha de comando para o TFS, o Jenkins deve ser configurado com o caminho completo para o `tf` cliente de linha de comando. Role para baixo a **configurar o sistema [Jenkins]** página até encontrar a seção do Team Foundation Server, conforme mostrado na seguinte captura de tela:

[![](jenkins-walkthrough-images/image17.png "Role para baixo na página Configurar sistema Jenkins até encontrar a seção do Team Foundation Server")](jenkins-walkthrough-images/image17.png#lightbox)

Insira o caminho completo para o `tf` de comando e, em seguida, clique em de **salvar** botão.

### <a name="configure-jenkins-security"></a>Configurar a segurança do Jenkins

Quando instalado pela primeira vez, o Jenkins tem segurança desabilitada, portanto, é possível que qualquer usuário configurar e executar qualquer tipo de trabalho de forma anônima. Esta seção aborda como configurar a segurança usando o banco de dados de usuário do Jenkins para configurar a autenticação e autorização.

Configurações de segurança podem ser encontradas selecionando **Jenkins > Gerenciar Jenkins > configurar a segurança Global**, conforme mostrado nesta captura de tela:

[![](jenkins-walkthrough-images/image18.png "Configurações de segurança podem ser encontradas selecionando o Jenkins / gerenciar Jenkins / configurar a segurança Global")](jenkins-walkthrough-images/image18.png#lightbox)

No **configurar a segurança Global** página, verifique o **Ativar segurança** caixa de seleção e o **controle de acesso** formulário deve aparecer, semelhante à seguinte captura de tela:

[![](jenkins-walkthrough-images/image19.png "Na página Configurar a segurança Global, verificar a segurança de habilitar a caixa de seleção e o formulário de controle de acesso devem ser, semelhantes a esta captura de tela")](jenkins-walkthrough-images/image19.png#lightbox)

Alternar o botão de opção para **banco de dados de usuário dos Jenkins** na **seção de Realm de segurança**e certifique-se de que **permitem que os usuários se inscrevam** também é verificado, conforme ilustrado no captura de tela a seguir:

[![](jenkins-walkthrough-images/image20.png "Alternar o botão de opção de banco de dados de usuário próprios de Jenkins na seção segurança Realm e certifique-se de que também esteja marcada ao permitir que os usuários para se inscrever")](jenkins-walkthrough-images/image20.png#lightbox)

Por fim, reinicie o Jenkins e crie uma nova conta. A primeira conta criada é a conta raiz, e essa conta será promovida automaticamente a um administrador. Navegue de volta para o **configurar a segurança Global** da página e verifique a **segurança baseada em matriz** botão de opção. A conta raiz deve ter acesso completo e a conta anônima deve receber acesso somente leitura, conforme mostrado na seguinte captura de tela:

[![](jenkins-walkthrough-images/image21.png "A conta raiz deve ter acesso completo e a conta anônima deve receber acesso somente leitura")](jenkins-walkthrough-images/image21.png#lightbox)

Depois que essas configurações são salvas e Jenkins é reiniciado, a segurança será ativada.

#### <a name="disabling-security"></a>Desabilitar a segurança

No caso de uma senha esquecida ou bloqueio de todo o Jenkins, é possível desativar a segurança, seguindo estas etapas:

1. Pare o Jenkins. Se você estiver usando Jenkins.app, você pode fazer isso clicando duas vezes no ícone de Jenkins.App no Dock e selecionando Quit no menu pop-up:

    ![Ícone do aplicativo no Dock e selecionando Quit no menu pop-up](jenkins-walkthrough-images/image19.png)

2. Abra o arquivo **~/.jenkins/config.xml** em um editor de texto.
3. Altere o valor da `<usesecurity></usesecurity>` elemento de `true` para `false`.
4. Excluir o `<authorizationstrategy></authorizationstrategy>` e o `<securityrealm></securityrealm>` elementos do arquivo.
5. Reinicie o Jenkins.

## <a name="setting-up-a-job"></a>Como configurar um trabalho

No nível superior, o Jenkins organiza todas as várias tarefas necessárias para compilar software em um *trabalho*. Um trabalho também tem metadados associados a ele, fornecendo informações sobre a compilação, como como obter o código-fonte, a frequência com que a compilação deve ser executado, quaisquer variáveis especiais que são necessários para compilar e como notificar os desenvolvedores se a compilação falhar.

Trabalhos são criados, selecionando **Jenkins > novo trabalho** no menu no canto superior direito, conforme mostrado na seguinte captura de tela:

![](jenkins-walkthrough-images/image22.png "Trabalhos são criados, selecionando o novo trabalho do Jenkins no menu no canto superior direito")

Isso exibirá a **novo trabalho [Jenkins]** página. Insira um nome para o trabalho e selecione o **compilar um projeto de software de estilo livre** botão de opção. Captura de tela a seguir mostra um exemplo disso:

![](jenkins-walkthrough-images/image23.png "Insira um nome para o trabalho e selecione o Build de um botão de opção de projeto de software de estilo livre")

Clicar a **Okey** botão apresenta a página de configuração para o trabalho. Isso deve ser semelhante a captura de tela a seguir:

![](jenkins-walkthrough-images/image24.png "Isso deve se parecer com esta captura de tela")

Jenkins organiza os trabalhos em um diretório no disco rígido que está localizado no seguinte caminho: **~/.jenkins/jobs/[JOB nome]**

Esta pasta contém todos os arquivos e artefatos específicos para o trabalho, como logs, arquivos de configuração e o código-fonte que precisa ser compilado.

Quando o trabalho inicial tiver sido criado, ele deve ser configurado com um ou mais dos seguintes:

- O sistema de gerenciamento de código-fonte deve ser especificado.
- Um ou mais *ações de build* deve ser adicionado ao projeto. Essas são as etapas ou tarefas necessárias para compilar o aplicativo.
- O trabalho deve ser atribuído um *criar um gatilho* – um conjunto de instruções informando a frequência com que o Jenkins para recuperar o código e compile o projeto final.

### <a name="configuring-source-code-control"></a>Configurando o controle do código fonte

A primeira tarefa Jenkins faz é recuperar o código-fonte do sistema de gerenciamento de código de origem. Jenkins dá suporte a muitos dos sistemas de gerenciamento de código do código-fonte populares disponíveis hoje. Esta seção aborda os dois sistemas populares, Git e o Team Foundation Server. Cada um desses sistemas de gerenciamento de código fonte é abordada mais detalhadamente nas seções a seguir.

#### <a name="using-git-for-source-code-control"></a>Usando o Git para controle do código-fonte

Se você estiver usando o TFS para controle do código-fonte, [ignorar](#using-tfs-for-source-code-management) esta seção e vá para a próxima seção usando o TFS.

Jenkins dá suporte ao Git fora da caixa – Nenhum plug-ins extras são necessárias. Para usar Git, clique no **Git** botão de opção e digite a URL do repositório Git, conforme mostrado na seguinte captura de tela:

![](jenkins-walkthrough-images/image25.png "Para usar o Git, clique no botão de rádio Git e insira a URL para o repositório Git")

Depois que as alterações são salvas, a configuração Git foi concluída.

#### <a name="using-tfs-for-source-code-management"></a>Usando o TFS para gerenciamento de código-fonte

Esta seção se aplica somente a usuários do TFS.

Clique no **Team Foundation Server** botão de opção e a seção de configuração do TFS devem ser, semelhantes ao que está na seguinte captura de tela:

![](jenkins-walkthrough-images/image26.png "Clique no botão de opção do Team Foundation Server e a seção de configuração do TFS deve aparecer")

Forneça as informações necessárias para o TFS. Captura de tela a seguir mostra um exemplo do formulário concluído:

![](jenkins-walkthrough-images/image27.png "Esta captura de tela mostra um exemplo do formulário concluído")

#### <a name="testing-the-source-code-control-configuration"></a>Testando a configuração de controle do código fonte

Depois que o controle do código fonte apropriado foi configurado, clique em **salvar** para salvar as alterações. Você voltará para a home page para o trabalho, que será parecida com a seguinte captura de tela:

![](jenkins-walkthrough-images/image28.png "Você voltará para a home page para o trabalho, que será parecida com esta captura de tela")

A maneira mais simples para validar se o controle do código fonte está configurado corretamente é disparar uma compilação manualmente, mesmo que não haja nenhuma ação de compilação especificada. Para iniciar manualmente uma compilação, a home page do trabalho tem um **compilar agora** vincular no menu à esquerda, conforme mostrado na captura de tela abaixo:

![](jenkins-walkthrough-images/image29.png "Para iniciar manualmente uma compilação, a home page do trabalho tem um link criar agora no menu à esquerda")

Quando um build tiver sido iniciado, a caixa de diálogo Criar histórico exibe um círculo azul piscando, uma barra de progresso, o número da compilação e a hora de início do build, semelhante à seguinte captura de tela:

![](jenkins-walkthrough-images/image30.png "Quando um build tiver sido iniciado, a caixa de diálogo Criar histórico exibe um círculo azul piscante, uma barra de progresso, o número da compilação e a hora de início do build")

Se o trabalho for bem-sucedido, um círculo azul será exibido. Se o trabalho falhar, um círculo vermelho será exibido.

Para ajudar a solucionar problemas que podem surgir como parte da compilação, o Jenkins irá capturar toda a saída do console para o trabalho. Para ver a saída do console, clique no trabalho na **histórico de Build**e, em seguida, o **saída do Console** link no menu à esquerda. A captura de tela a seguir mostra a **saída do Console** link, bem como parte da saída de um trabalho com êxito:

![](jenkins-walkthrough-images/image31.png "Esta captura de tela mostra o link de saída do Console, bem como parte da saída de um trabalho com êxito")

#### <a name="location-of-build-artifacts"></a>Localização de artefatos de compilação

Jenkins irá recuperar o código fonte inteiro em uma pasta especial chamada de um *espaço de trabalho*. Esse diretório pode ser encontrado dentro da pasta no seguinte local:

    ```
    ~/.jenkins/jobs/[JOB NAME]/workspace
    ```

O caminho para o espaço de trabalho será armazenado em uma variável de ambiente chamada `$WORKSPACE`.

É possível procurar a pasta de espaço de trabalho no Jenkins navegando até a página de aterrissagem para um trabalho, e, em seguida, clicar na **espaço de trabalho** link no menu à esquerda. Captura de tela a seguir mostra um exemplo do espaço de trabalho de um trabalho denominado **HelloWorld**:

![](jenkins-walkthrough-images/image32.png "Esta captura de tela mostra um exemplo do espaço de trabalho para um trabalho chamado HelloWorld")

### <a name="build-triggers"></a>Criar gatilhos

Há várias estratégias diferentes para iniciar os builds no Jenkins – eles são conhecidos como *crie gatilhos*. Um disparador de compilação ajuda a Jenkins decidir quando iniciar um trabalho e compile o projeto. Dois dos gatilhos de build mais comuns são:

- **Criar periodicamente** – esse gatilho faz com que o Jenkins iniciar um trabalho em intervalos especificados, como a cada duas horas ou à meia-noite nos dias da semana. A compilação será iniciada independentemente se houve alterações no repositório de código de origem.
- **Sondar SCM** – esse gatilho fará a sondagem controle do código fonte regularmente. Se todas as alterações foram confirmadas no repositório de código fonte, o Jenkins iniciará uma nova compilação.

Sondar SCM é um gatilho popular porque fornece comentários rápidos quando um desenvolvedor confirma as alterações que causam falha na compilação. Isso é útil para equipes de alertas que algum código confirmado recentemente está causando problemas e permite que os desenvolvedores solucionar o problema, enquanto as alterações são novas em mente.

Compilações periódicas são geralmente usadas para criar uma versão do aplicativo que pode ser distribuído aos testadores. Por exemplo, uma compilação periódica pode ser agendada para noite de sexta-feira para que os membros da equipe de QA podem testar o trabalho da semana anterior.

### <a name="compiling-a-xamarinios-applications"></a>Compilando um aplicativo xamarin. IOS
Projetos xamarin. IOS podem ser compilados usando a linha de comando `xbuild` ou `msbuild`. O comando de shell será executado no contexto da conta de usuário que está executando Jenkins. É importante que a conta de usuário tem acesso ao perfil de provisionamento para que o aplicativo pode ser empacotado corretamente para distribuição. É possível adicionar esse comando do shell para a página de configuração de trabalho.

Role para baixo até a **Build** seção. Clique o **Adicionar etapa de compilação** botão e selecione **executar shell**, conforme ilustrado pela captura de tela a seguir:

![](jenkins-walkthrough-images/image33.png "Clique no botão de etapa de compilação de adicionar e selecione Executar shell")

[!include[](~/tools/ci/includes/commandline-compile-of-xamarin-ios-ipa.md)]

### <a name="building-a-xamarinandroid-project"></a>Compilando um projeto xamarin. Android

Compilando um projeto xamarin. Android é muito semelhante em conceito ao compilar um projeto xamarin. IOS. Para criar um APK de um projeto xamarin. Android, o Jenkins deve ser configurado para executar as duas etapas a seguir:

- Compilar o projeto usando o plug-in do MSBuild
- Entrada e zip alinham o APK com um repositório de chaves de versão válido.

Essas duas etapas serão abordadas mais detalhadamente nas próximas duas seções.

### <a name="creating-the-apk"></a>Criando o APK

Clique no **Adicionar etapa de compilação** botão e selecione **criar um projeto do Visual Studio ou uma solução usando o MSBuild**, conforme mostrado na captura de tela abaixo:

![](jenkins-walkthrough-images/image36.png "Criando o APK, clique no botão de etapa de compilação de adicionar e selecionar Build um projeto do Visual Studio ou uma solução usando o MSBuild")

Depois que a etapa de compilação é adicionada ao projeto, preencha os campos de formulário que aparecem. Captura de tela a seguir é um exemplo do formulário concluído:

![](jenkins-walkthrough-images/image37.png "Depois que a etapa de compilação é adicionada ao projeto, preencha os campos de formulário que aparecem")

Essa etapa de compilação executará `xbuild` no **$WORKSPACE** pasta. O arquivo de Build do MSBuild é definido como o **Xamarin.Android.csproj** arquivo. O **argumentos de linha de comando** especificar um build de versão de destino **PackageForAndroid**. O produto dessa etapa será um APK que, no seguinte local:

    ```
    $WORKSPACE/[PROJECT NAME]/bin/Release
    ```

Captura de tela a seguir mostra um exemplo de como esse APK:

![](jenkins-walkthrough-images/image38.png "Nesta captura de tela mostra um exemplo de como esse APK")

Esse APK não está pronto para implantação, pois não foi assinado com um repositório de chaves privado e deve ser zip alinhado.

#### <a name="signing-and-zipaligning-the-apk-for-release"></a>Assinatura e usar Zipalign no APK para lançamento

Assinando e usar zipalign no APK são tecnicamente duas tarefas separadas que são executadas por duas ferramentas de linha de comando separada do SDK do Android. No entanto, é conveniente para realizá-las em ação de uma compilação. Para obter mais informações sobre como entrar e alinhar um APK, consulte a documentação do Xamarin sobre como preparar um aplicativo Android para lançamento.

Esses dois comandos exigem parâmetros de linha de comando que podem variar de um projeto a projeto. Além disso, alguns desses parâmetros de linha de comando são senhas que não devem aparecer na saída do console quando a compilação está em execução. Vamos armazenar alguns desses parâmetros de linha de comando em variáveis de ambiente. As variáveis de ambiente necessárias para a assinatura e/ou zip alinhando são descritas na tabela a seguir:

|Variável de ambiente|Descrição|
|--- |--- |
|KEYSTORE_FILE|Esse é o caminho para o repositório de chaves para assinar o APK|
|KEYSTORE_ALIAS|A chave no repositório de chaves que será usado para assinar o APK.|
|INPUT_APK|O APK criado por `xbuild`.|
|SIGNED_APK|O APK assinado produzido pelo `jarsigner`.|
|FINAL_APK|Esse é o zip alinhado APK produzido por `zipalign`.|
|STORE_PASS|Esta é a senha que é usada para acessar o conteúdo do repositório de chaves para assinar o arquivo.|

Conforme descrito na seção de requisitos, essas variáveis de ambiente podem ser definidas durante a compilação usando o plug-in EnvInject. O trabalho deve ter uma nova compilação etapa adicionada com base em variáveis de ambiente a inserção, conforme mostrado na seguinte captura de tela:

![](jenkins-walkthrough-images/image39.png "O trabalho deve ter uma nova compilação etapa adicionada com base nas variáveis de ambiente de inserção")

No **propriedades do conteúdo** formam o campo que será exibido, são adicionadas a variáveis de ambiente, um por linha, no seguinte formato:

    ```
    ENVIRONMENT_VARIABLE_NAME = value
    ```

Captura de tela a seguir mostra as variáveis de ambiente que são necessárias para assinar o APK:

![](jenkins-walkthrough-images/image40.png "Esta captura de tela mostra as variáveis de ambiente que são necessárias para assinar o APK")

Observe que algumas das variáveis de ambiente para os arquivos APK são criadas no `WORKSPACE` variável de ambiente.

A variável de ambiente final é a senha para acessar o conteúdo do repositório de chaves: `STORE_PASS`. As senhas são valores confidenciais que devem ser obscurecidos ou omitidos nos arquivos de log. O plug-in EnvInject pode ser configurado para proteger esses valores para que eles não aparecem nos logs.

Imediatamente antes do **construir** seção da configuração do trabalho é um **ambiente de Build** seção. Quando o **injetar senhas** caixa de seleção é alternada, campos de alguma forma sejam exibidos. Esses campos de formulário são usados para capturar o nome e valor da variável de ambiente. Captura de tela a seguir está um exemplo de como adicionar o `STORE_PASS` variável de ambiente:

![](jenkins-walkthrough-images/image41.png "Esta captura de tela é um exemplo de como adicionar a variável de ambiente STOREPASS")

Depois que as variáveis de ambiente terem sido inicializadas, a próxima etapa é adicionar uma etapa de compilação para a assinatura e zip alinhando o APK. Imediatamente após a etapa de compilação para inserir as variáveis de ambiente será outra **executar shell** compilação de comando que será executado `jarsigner` e `zipalign`. Cada comando executará uma linha acima, conforme mostrado no trecho a seguir:

    ```
    jarsigner -verbose -sigalg MD5withRSA -digestalg SHA1 -keystore $KEYSTORE_FILE -storepass $STORE_PASS -signedjar $SIGNED_APK $INPUT_APK $KEYSTORE_ALIAS
    zipalign -f -v 4 $SIGNED_APK $FINAL_APK
    ```

Captura de tela a seguir mostra um exemplo de como inserir os `jarsigner` e `zipalign` comandos para a etapa:

![](jenkins-walkthrough-images/image42.png "Esta captura de tela mostra um exemplo de como inserir os comandos jarsigner e zipalign a etapa")

Depois que todas as ações de compilação estão em vigor, é uma boa prática para disparar uma compilação manual para verificar se que tudo está funcionando. Se a compilação falhar, o **saída do Console** deve ser revisado para obter informações sobre o que causou a compilação falhar.

### <a name="submitting-tests-to-test-cloud"></a>Enviar testes ao Test Cloud

Testes automatizados podem ser enviados para a nuvem de teste usando comandos do shell. Para obter mais informações sobre como configurar uma execução de teste no Xamarin Test Cloud, consulte este guia para usar [uitest](/appcenter/test-cloud/preparing-for-upload/uitest/).

## <a name="summary"></a>Resumo

Neste guia introduziu o Jenkins como um servidor de compilação no macOS e configurá-lo para compilar e preparar os aplicativos móveis do Xamarin para lançamento. Instalamos o Jenkins em um computador macOS, juntamente com vários plug-ins para dar suporte ao processo de compilação. É criado e configurado um trabalho que extrairá o código do TFS ou Git e, em seguida, compilar esse código em um aplicativo pronto de versão. Também exploramos duas maneiras diferentes para agendar quando os trabalhos devem ser executados.

## <a name="related-links"></a>Links relacionados

- [Integração Contínua](~/tools/ci/index.md)
- [Teste do App Center](https://docs.microsoft.com/appcenter/test-cloud/)
