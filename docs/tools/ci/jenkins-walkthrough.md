---
title: Usando Jenkins com Xamarin
description: Este documento descreve como usar Jenkins para integração contínua com aplicativos Xamarin. Ele aborda como instalar, configurar e usar Jenkins.
ms.prod: xamarin
ms.assetid: 1E6825DF-1254-4FCB-B94D-ADD33D1B5309
author: topgenorth
ms.author: toopge
ms.date: 03/23/2017
ms.openlocfilehash: 7e9069c698f82e9ec9c5009286c2a36b6722b7a8
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/05/2018
ms.locfileid: "34793938"
---
# <a name="using-jenkins-with-xamarin"></a>Usando Jenkins com Xamarin

_Este guia mostra como configurar Jenkins como um servidor de integração contínua e automatizar a compilação de aplicativos móveis criados com o Xamarin. Descreve como instalar Jenkins nos X, configurá-lo e configurar os trabalhos para compilar aplicativos xamarin e xamarin quando as alterações são confirmadas para o sistema de gerenciamento de código fonte._

[Introdução à integração contínua com o Xamarin](~/tools/ci/intro-to-ci.md) apresenta a integração contínua, como uma prática de desenvolvimento de software útil que fornece avisos antecipados do código desfeito ou incompatível. CI permite aos desenvolvedores problemas e resolver problemas quando eles surgem e mantém o software em um estado adequado para a implantação. Este passo a passo aborda como usar o conteúdo de ambos os documentos juntos.

Este guia mostra como instalar Jenkins em um computador dedicado executando o OS X e configurá-lo para ser executado automaticamente quando o computador é inicializado. Depois de instalar Jenkins, vamos instalar plug-ins adicionais para dar suporte a Build MS. Jenkins suporta Git fora da caixa. Se o TFS está sendo usado para controle do código fonte, uma outros utilitários de linha de comando e de plug-in também devem ser instalado.

Depois de configurado Jenkins e qualquer plug-ins necessários foram instalados, vamos criar um ou mais trabalhos para compilar projetos xamarin e xamarin. Um trabalho é um conjunto de etapas e os metadados necessários para executar um trabalho. Um trabalho normalmente consiste no seguinte:

-  **Gerenciamento de código (SCM) de origem** – essa é uma entrada de metadados nos arquivos de configuração Jenkins que contém informações sobre como conectar-se ao controle do código fonte e os arquivos para recuperar.
-  **Gatilhos** – os gatilhos são usados para iniciar um trabalho com base em determinadas ações, como quando um desenvolvedor confirma as alterações para o repositório de código fonte.
-  **Instruções de compilação** – este é um plug-in ou um script que irá compilar o código-fonte e produzir um binário que pode ser instalado em dispositivos móveis.
-  **Ações de compilação opcional** – isso pode incluir a execução de testes de unidade, executando a análise estática do código, de assinatura de código, ou iniciando outro trabalho para realizar outras criar trabalho relacionado.
-  **Notificações** – um trabalho pode enviar algum tipo de notificação sobre o status de uma compilação.
-  **Segurança** – Embora opcional, é altamente recomendável que os recursos de segurança Jenkins também esteja habilitada.


Este guia orientará como configurar um servidor Jenkins que abrangem cada um desses pontos. No final dela, podemos deve ter uma boa compreensão de como instalar e configurar Jenkins para criar do APK e de IPA para nossos projetos móveis Xamarin.

## <a name="requirements"></a>Requisitos

O servidor de compilação ideal é um computador dedicado para o único propósito de criação e teste possivelmente o aplicativo. Um computador dedicado garante que os artefatos que podem ser necessários para outras funções (como de um servidor web) não contaminarem a compilação. Por exemplo, se o servidor de compilação também está agindo como um servidor web, o servidor web pode exigir uma versão conflitante de alguma biblioteca comum. Devido a conflito o servidor web pode não funcionar corretamente ou Jenkins pode criar compilações não funcionam quando implantado para usuários.

O servidor de compilação para aplicativos móveis Xamarin está definido muito semelhante a estação de trabalho do desenvolvedor. Ele tem uma conta de usuário na qual Jenkins, o Visual Studio para Mac, e xamarin e xamarin serão instalados. Todos o certificados, o provisionamento de perfis e repositórios de chaves de assinatura de código devem ser instalados também. *Normalmente, conta de usuário do servidor de compilação é separada de suas contas de desenvolvedor - Certifique-se de instalar e configurar o software, chaves e certificados de logon com a conta de usuário do servidor de compilação.*

O diagrama a seguir ilustra a todos esses elementos em um servidor de compilação Jenkins típico:

 [![](jenkins-walkthrough-images/image1.png "Este diagrama ilustra todos esses elementos em um servidor de compilação Jenkins típico")](jenkins-walkthrough-images/image1.png#lightbox)

aplicativos iOS só podem ser criados e assinados em um computador executando o Mac OS X. Um Mini Mac é uma opção de baixo custo razoável, mas qualquer computador capaz de executar OS X 10.10 (Yosemite) ou superior é suficiente.

Se o TFS está sendo usado para controle do código fonte, você deseja instalar [Team Explorer Everywhere](http://www.microsoft.com/en-ca/download/details.aspx?id=40785), disponível na Microsoft. Team Explorer Everywhere fornece acesso de plataforma cruzada para o TFS no Terminal nos X.

[!include[](~/tools/ci/includes/firewall-information.md)]

## <a name="installing-jenkins"></a>Instalando Jenkins

A primeira tarefa usando Jenkins é instalá-lo. Há três maneiras de executar Jenkins nos x:

-  Como um daemon, está em execução em segundo plano.
-  Dentro de um contêiner de servlet como Tomcat, Jetty ou JBoss.
-  Como um processo normal executado sob uma conta de usuário.


Aplicativos de integração contínua mais tradicionais são executados em segundo plano, como um daemon (nos X ou \*nix) ou como um serviço (no Windows). Isso é adequado para cenários onde não há nenhuma exigência de interação de GUI, e a configuração do ambiente de compilação pode ser facilmente realizada. Aplicativos móveis também exigem o repositório de chaves e certificados que podem ser um problema de acesso quando Jenkins está em execução como um daemon de assinatura. Devido a essas questões, este documento enfoca o terceiro cenário – executando Jenkins sob uma conta de usuário no servidor de compilação.

Jenkins.App é uma maneira prática para instalar Jenkins. É um wrapper AppleScript que simplifica o início e interrupção de um servidor Jenkins. Em vez de executar em um shell bash, Jenkins é executado como um aplicativo com ícone de encaixe, como mostrado na captura de tela a seguir:

 [![](jenkins-walkthrough-images/image2.png "Em vez de executar em um shell bash, Jenkins é executado como um aplicativo com ícone de encaixe, como mostrado nesta captura de tela")](jenkins-walkthrough-images/image2.png#lightbox)

Iniciando ou parando Jenkins é tão simple quanto iniciando ou parando Jenkins.App.

Para instalar o Jenkins.App, baixe a versão mais recente da página de download do projeto, mostrada na captura de tela abaixo:

 [![](jenkins-walkthrough-images/image3.png "Download de página, como mostrada na captura de tela de baixar a versão mais recente dos projetos de aplicativo")](jenkins-walkthrough-images/image3.png#lightbox)

Extraia o arquivo zip para o `/Applications` pasta no servidor de compilação e start-lo exatamente como qualquer outro aplicativo de OS X.
Na primeira vez que você inicia o Jenkins.App, ele apresentará uma caixa de diálogo informando que ele baixará Jenkins:

 [![](jenkins-walkthrough-images/image4.png "O aplicativo, ele apresentará uma caixa de diálogo informando que ele baixará Jenkins")](jenkins-walkthrough-images/image4.png#lightbox)

Quando tiver terminado de Jenkins.App seu download, ele exibirá outra caixa de diálogo perguntando se você deseja personalizar a inicialização Jenkins, como mostrado na seguinte captura de tela:

 [![](jenkins-walkthrough-images/image5.png "Aplicativo concluiu o download, ele exibirá outra caixa de diálogo perguntando se deseja personalizar inicialização Jenkins, como mostrado na captura de tela")](jenkins-walkthrough-images/image5.png#lightbox)

Personalizar Jenkins é opcional e não precisa ser executada cada vez que o aplicativo é iniciado – as configurações padrão para Jenkins funcionará na maioria das situações.

Se for necessário personalizar Jenkins, clique no **alterar padrões** botão. Isso apresentará duas caixas de diálogo consecutivas: um que solicita parâmetros de linha de comando do Java e outro que solicita parâmetros de linha de comando Jenkins. As seguintes duas capturas de tela mostram essas duas caixas de diálogo:

 [![](jenkins-walkthrough-images/image6.png "Esta captura de tela mostra as caixas de diálogo")](jenkins-walkthrough-images/image6.png#lightbox)

 [![](jenkins-walkthrough-images/image7.png "Esta captura de tela mostra as caixas de diálogo")](jenkins-walkthrough-images/image7.png#lightbox)

Quando Jenkins estiver em execução, convém defini-lo como um item de logon para que ela será iniciada a cada vez que os logons de usuário no computador. Você pode fazer isso clicando duas vezes no ícone Jenkins no compartimento e escolhendo **opções... Abrir no logon**, conforme mostrado na seguinte captura de tela:

 [![](jenkins-walkthrough-images/image8.png "Você pode fazer isso clicando no ícone Jenkins no compartimento e escolhendo OptionsOpen no logon, conforme mostrado nesta captura de tela")](jenkins-walkthrough-images/image8.png#lightbox)

Isso fará com que Jenkins.App iniciar automaticamente cada vez que o usuário fizer logon, mas não quando o computador é inicializado. É possível especificar uma conta de usuário que usa para login automaticamente com OS X no momento da inicialização. Abra o **preferências do sistema**e selecione o **& grupos de usuário** ícone conforme mostrado nesta captura de tela:

 [![](jenkins-walkthrough-images/image9.png "Abra as preferências de sistema e selecione o ícone de grupos de usuários, conforme mostrado nesta captura de tela")](jenkins-walkthrough-images/image9.png#lightbox)

Clique no **opções de logon** botão e, em seguida, escolha a conta que OS X usará para fazer logon no momento da inicialização.

Neste ponto Jenkins foi instalado. No entanto, se quisermos criar aplicativos móveis Xamarin, precisaremos instalar alguns plug-ins.


### <a name="installing-plugins"></a>Instalando o plug-ins

Quando o instalador Jenkins.App for concluída, ela será iniciar Jenkins e iniciar o navegador da web com a URL http://localhost:8080, conforme mostrado na captura de tela abaixo:

 [![](jenkins-walkthrough-images/image10.png "8080, conforme mostrado nesta captura de tela")](jenkins-walkthrough-images/image10.png#lightbox)

Nessa página, selecione **Jenkins > Gerenciar Jenkins > Gerenciar plug-ins** no menu no canto superior esquerdo, conforme mostrado na captura de tela abaixo:

 [![](jenkins-walkthrough-images/image11.png "Nessa página, selecione Jenkins gerenciar Jenkins gerenciar plug-ins no menu no canto superior esquerdo")](jenkins-walkthrough-images/image11.png#lightbox)

Isso exibirá a **Jenkins plug-in Gerenciador** página. Se você clicar na guia disponível, você verá uma lista de plug-ins mais de 600 que pode ser baixado e instalado. Isso é ilustrado na captura de tela abaixo:

 [![](jenkins-walkthrough-images/image12.png "Se você clicar na guia disponível, você verá uma lista de mais de 600 plug-ins que podem ser baixadas e instaladas")](jenkins-walkthrough-images/image12.png#lightbox)

Rolar por todos os plug-ins para localizar que algumas podem ser entediantes e propenso a 600. Jenkins fornece um campo de pesquisa do filtro no canto superior direito da interface. Usando este campo de filtro para pesquisar simplificará a localização e instalado um ou todos os seguintes plug-ins:

-  **Plug-in do MSBuild Jenkins** – este plug-in torna possível compilar o Visual Studio e o Visual Studio para soluções de Mac (. sln) e projetos (. csproj).
-  **Plug-in Injetor do ambiente** – este é um opcional, mas útil plug-in que torna possível definir variáveis de ambiente em que o trabalho e criar nível. Ele também oferece proteção extra para variáveis, como as senhas usadas no código assinar o aplicativo. Ele é muitas vezes abreviado como o *EnvInject Plugin* .
-  **Team Foundation Server Plugin** – este é um plug-in opcional que só é necessária se você estiver usando o Team Foundation Server ou o Team Foundation Services para controle do código fonte.

Jenkins suporta Git sem qualquer plug-ins extras.

Depois de instalar todos os plug-ins, você desejará reinicie Jenkins e definir as configurações globais para cada plug-in. As configurações globais para um plug-in podem ser encontradas selecionando **Jenkins > Gerenciar Jenkins > configurar o sistema** no canto superior esquerdo, conforme mostrado na captura de tela abaixo:

 [![](jenkins-walkthrough-images/image13.png "As configurações globais para um plug-in podem ser encontradas selecionando Jenkins / gerenciar Jenkins / entregá-configurar o sistema do canto superior esquerdo de canto")](jenkins-walkthrough-images/image13.png#lightbox)

Quando você seleciona essa opção de menu, você será levado para a **configurar o sistema [Jenkins]** página. Esta página contém seções para configurar Jenkins em si e para definir alguns dos valores globais de plug-in.  Captura de tela abaixo ilustra um exemplo dessa página:

 [![](jenkins-walkthrough-images/image14.png "Esta captura de tela ilustra um exemplo desta página")](jenkins-walkthrough-images/image14.png#lightbox)


#### <a name="configuring-the-msbuild-plugin"></a>Configurando o plug-in do MSBuild

O plug-in do MSBuild deve ser configurado para usar **/Library/Frameworks/Mono.framework/Commands/xbuild** para compilar o Visual Studio para arquivos de solução e projeto de Mac. Role para baixo o **configurar o sistema [Jenkins]** página até que o **adicionar MSBuild** botão é exibido, conforme mostrado na captura de tela abaixo:

 [![](jenkins-walkthrough-images/image15.png "Role a página Configurar Jenkins do sistema até que seja exibido no botão Adicionar MSBuild")](jenkins-walkthrough-images/image15.png#lightbox)

Clique neste botão e, em seguida, preencha o **nome** e **caminho** para **MSBuild** campos no formulário que aparece. O nome do seu **MSBuild** instalação deve ser algo significativo, enquanto o **caminho para o MSBuild** deve ser o caminho para `xbuild`, que normalmente é **/Library/estruturas / Mono.framework/Commands/xbuild**. Depois de salvar as alterações clicando em Salvar ou no botão Aplicar na parte inferior da página, Jenkins poderão usar `xbuild` para compilar suas soluções.

#### <a name="configuring-the-tfs-plugin"></a>Configurando o plug-in do TFS

Esta seção é obrigatória se você pretende usar TFS para o controle do código fonte.

Fim de uma estação de trabalho OS X interagir com um servidor do TFS, Team Explorer Everywhere deve ser instalado na estação de trabalho. Team Explorer Everywhere é um conjunto de ferramentas da Microsoft que inclui um cliente de linha de comando de plataforma cruzada para acessar o TFS. Team Explorer Everywhere pode ser baixado da Microsoft e instalado em três etapas:

1. Descompacte o arquivo para um diretório que seja acessível à conta de usuário. Por exemplo, você pode descompacte o arquivo **~/tee**.
2. Configure o caminho de shell ou do sistema para incluir a pasta que contém os arquivos foram descompactados na etapa acima. Por exemplo,

        echo export PATH~/tee/:$PATH' >> ~/.bash_profile

3. Para confirmar que o Team Explorer Everywhere está instalado, abra uma sessão do terminal e execute o `tf` comando. Se tf for configurado corretamente, você verá a seguinte saída na sua sessão de terminal:

        $ tf
        Team Explorer Everywhere Command Line Client (version 11.0.0.201306181526)

        Available commands and their options:

Depois de instalar o cliente de linha de comando para o TFS, Jenkins deve ser configurado com o caminho completo para o `tf` cliente de linha de comando. Role para baixo o **configurar o sistema [Jenkins]** página até encontrar a seção do Team Foundation Server, conforme mostrado na seguinte captura de tela:

 [![](jenkins-walkthrough-images/image17.png "Role para baixo a página Configurar sistema Jenkins até encontrar a seção do Team Foundation Server")](jenkins-walkthrough-images/image17.png#lightbox)

Insira o caminho completo para o `tf` de comando e, em seguida, clique no **salvar** botão.

### <a name="configure-jenkins-security"></a>Configurar a segurança Jenkins

Quando instalado pela primeira vez, Jenkins tem segurança desativada, portanto, é possível que qualquer usuário configurar e executar qualquer tipo de trabalho anonimamente. Esta seção aborda como configurar a segurança usando o banco de dados de usuário Jenkins para configurar a autenticação e autorização.

Configurações de segurança podem ser encontradas selecionando **Jenkins > Gerenciar Jenkins > configurar a segurança Global**, conforme mostrado nesta captura de tela:

 [![](jenkins-walkthrough-images/image18.png "Configurações de segurança podem ser encontradas selecionando Jenkins / gerenciar Jenkins / configurar a segurança Global")](jenkins-walkthrough-images/image18.png#lightbox)

No **configurar a segurança Global** página, verifique o **Ativar segurança** caixa de seleção e o **controle de acesso** formulário deve aparecer, semelhante à seguinte captura de tela:

 [![](jenkins-walkthrough-images/image19.png "Na página Configurar a segurança Global, verificar a segurança de habilitar a caixa de seleção e o formulário de controle de acesso devem ser, semelhantes a esta captura de tela")](jenkins-walkthrough-images/image19.png#lightbox)

Alternar o botão de opção **banco de dados de usuário dos Jenkins** no **seção de território de segurança**e certifique-se de que **permitem que os usuários se inscrevam** também estiver marcada, conforme ilustrado no captura de tela abaixo:

 [![](jenkins-walkthrough-images/image20.png "Alternar o botão de opção de banco de dados do Jenkins próprio usuário na seção segurança território e certifique-se de que também esteja marcada ao permitir que os usuários para se inscrever")](jenkins-walkthrough-images/image20.png#lightbox)

Finalmente, reinicie Jenkins e criar uma nova conta. A primeira conta criada é a conta raiz, e essa conta será promovida automaticamente a um administrador. Navegue de volta para o **configurar a segurança Global** página e marcar o **segurança baseada em matriz** botão de opção. A conta raiz deve ter acesso completo e a conta anônima deve receber acesso somente leitura, conforme mostrado na seguinte captura de tela:

 [![](jenkins-walkthrough-images/image21.png "A conta raiz deve ter acesso completo e a conta anônima deve receber acesso somente leitura")](jenkins-walkthrough-images/image21.png#lightbox)

Depois que essas configurações são salvas e Jenkins é reiniciado, a segurança será ativada.


#### <a name="disabling-security"></a>Desabilitando a segurança

No caso de uma senha esquecida ou bloqueio Jenkins todo, é possível desativar a segurança, seguindo estas etapas:

1. Pare Jenkins. Se você estiver usando Jenkins.app, você pode fazer isso clicando no ícone Jenkins.App no compartimento e selecionando Sair no menu pop-up:

    ![](jenkins-walkthrough-images/image19.png "Ícone do aplicativo de encaixe e selecionando Sair no menu pop-up")
2. Abra o arquivo **~/.jenkins/config.xml** em um editor de texto.
3. Alterar o valor da `<usesecurity></usesecurity>` elemento `true` para `false`.
4. Excluir o `<authorizationstrategy></authorizationstrategy>` e `<securityrealm></securityrealm>` elementos do arquivo.
5. Reinicie Jenkins.

## <a name="setting-up-a-job"></a>Configurar um trabalho

No nível superior, Jenkins organiza todas as várias tarefas necessárias para construir o software em um *trabalho*. Um trabalho também tem metadados associados a ele, fornecendo informações sobre a compilação como como obter o código-fonte, quantas vezes a compilação deve ser executado, quaisquer variáveis especiais que são necessárias para criar e como notificar os desenvolvedores se houver falha na criação.

Os trabalhos são criados selecionando **Jenkins > novo trabalho** no menu no canto superior direito, conforme mostrado na seguinte captura de tela:


![](jenkins-walkthrough-images/image22.png "Os trabalhos são criados selecionando Jenkins novo trabalho no menu no canto superior direito")

Isso exibirá a **novo trabalho [Jenkins]** página. Insira um nome para o trabalho e selecione o **compilar um projeto de software livre de estilo** botão de opção. Captura de tela a seguir mostra um exemplo disso:

![](jenkins-walkthrough-images/image23.png "Insira um nome para o trabalho e selecionar a criar um botão de opção de projeto de software livre-style")

Clique o **Okey** botão apresenta a página de configuração para o trabalho. Isso deve ser semelhante a captura de tela a seguir:

![](jenkins-walkthrough-images/image24.png "Isso deve ser semelhante a esta captura de tela")

Jenkins organiza os trabalhos em um diretório no disco rígido localizado no seguinte caminho: **~/.jenkins/jobs/[JOB nome]**

Esta pasta contém todos os arquivos e artefatos específicos para o trabalho, como logs, arquivos de configuração e o código-fonte que precisa ser compilado.

Quando o trabalho inicial tiver sido criado, ele deve ser configurado com uma ou mais das seguintes opções:

 - O sistema de gerenciamento de código de origem deve ser especificado.
 - Um ou mais *ações de construção* devem ser adicionados ao projeto. Essas são as etapas ou tarefas necessárias para criar o aplicativo.
 - O trabalho deve ser atribuído um *criar gatilho* – um conjunto de instruções informando Jenkins frequência para recuperar o código e compilar o projeto final.

### <a name="configuring-source-code-control"></a>Configurando o controle do código fonte

A primeira tarefa Jenkins é recuperar o código-fonte do sistema de gerenciamento de código de origem. Jenkins dá suporte a muitos dos sistemas de gerenciamento de código de populares de software disponíveis no momento. Esta seção aborda os dois sistemas populares, Git e o Team Foundation Server. Cada um desses sistemas de gerenciamento de código fonte é discutida mais detalhadamente nas seções a seguir.

#### <a name="using-git-for-source-code-control"></a>Usando o Git para controle do código fonte

Se você estiver usando o TFS para controle do código fonte, [ignorar](#Using_TFS_for_Source_Code_Management) esta seção e vá para a próxima seção usando o TFS.

Jenkins suporta Git predefinido – Nenhum plug-ins adicionais são necessárias. Para usar o Git, clique no **Git** botão de opção e digite a URL para o repositório do Git, conforme mostrado na seguinte captura de tela:

![](jenkins-walkthrough-images/image25.png "Para usar o Git, clique no botão de opção Git e insira a URL para o repositório do Git")

Depois que as alterações forem salvas, a configuração de Git está concluída.

#### <a name="using-tfs-for-source-code-management"></a>Usando o TFS para o gerenciamento de código fonte

Esta seção se aplica somente aos usuários do TFS.

Clique no **Team Foundation Server** botão de opção e a seção de configuração do TFS devem aparecer, semelhantes ao que está na seguinte captura de tela:


![](jenkins-walkthrough-images/image26.png "Clique no botão de opção do Team Foundation Server e a seção de configuração do TFS deve aparecer")

Forneça as informações necessárias para o TFS. Captura de tela a seguir mostra um exemplo do formulário concluído:

![](jenkins-walkthrough-images/image27.png "Esta captura de tela mostra um exemplo do formulário concluído")

#### <a name="testing-the-source-code-control-configuration"></a>Testar a configuração de controle do código fonte

Depois que o controle de código apropriada de origem tiver sido configurado, clique em **salvar** para salvar as alterações. Isso levará de volta para a home page do trabalho, que será parecida com a captura de tela a seguir:

![](jenkins-walkthrough-images/image28.png "Isso levará de volta para a home page do trabalho, que será parecida com esta captura de tela")

A maneira mais simples para validar que o controle do código fonte está configurado corretamente é disparar um build manualmente, mesmo que não há nenhuma ação de compilação especificada. Para iniciar uma compilação manualmente, a home page do trabalho tem um **criar agora** link no menu à esquerda, como mostrado na captura de tela abaixo:

![](jenkins-walkthrough-images/image29.png "Para iniciar uma compilação manualmente, a home page do trabalho tem um link criar agora no menu à esquerda")

Quando uma compilação tiver sido iniciada, a caixa de diálogo Criar histórico exibe um círculo azul piscando, uma barra de progresso, o número da compilação e a hora em que a compilação iniciada, semelhante à captura de tela a seguir:

![](jenkins-walkthrough-images/image30.png "Quando uma compilação tiver sido iniciada, a caixa de diálogo Criar histórico exibe um círculo azul de piscar, uma barra de progresso, o número da compilação e a hora em que a compilação iniciada")

Se o trabalho for bem-sucedido, será exibido um círculo azul. Se o trabalho falhar, um círculo vermelho será exibido.

Para ajudar na solução de problemas que podem surgir como parte da compilação, Jenkins irá capturar todos a saída do console para o trabalho. Para ver a saída do console, clique no trabalho na **histórico de compilação**e, em seguida, o **saída do Console** link no menu à esquerda. A captura de tela a seguir mostra o **saída do Console** link, bem como parte da saída de um trabalho com êxito:

![](jenkins-walkthrough-images/image31.png "Esta captura de tela mostra o link de saída do Console, bem como parte da saída de um trabalho com êxito")

#### <a name="location-of-build-artifacts"></a>Local dos artefatos de compilação

Jenkins recuperará o código-fonte inteiro em uma pasta especial chamada um *espaço de trabalho*. Este diretório pode ser encontrado dentro da pasta no seguinte local:

    ~/.jenkins/jobs/[JOB NAME]/workspace

O caminho para o espaço de trabalho será armazenado em uma variável de ambiente chamada `$WORKSPACE`.

É possível procurar a pasta de trabalho no Jenkins navegando até a página inicial de um trabalho, e, em seguida, clicando no **espaço de trabalho** link no menu à esquerda. Captura de tela a seguir mostra um exemplo do espaço de trabalho de um trabalho denominado **HelloWorld**:

![](jenkins-walkthrough-images/image32.png "Esta captura de tela mostra um exemplo do espaço de trabalho de um trabalho denominado HelloWorld")

### <a name="build-triggers"></a>Criar gatilhos

Há várias estratégias diferentes para iniciar compilações em Jenkins – eles são conhecidos como *criar gatilhos*. Um gatilho de compilação ajuda Jenkins decidir quando iniciar um trabalho e compilar o projeto. Dois dos gatilhos compilação mais comuns são:

- **Criar periodicamente** – esse disparador faz com que Jenkins iniciar um trabalho em intervalos especificados, como a cada duas horas ou à meia-noite, em dias da semana. A compilação serão iniciadas independentemente se houve alterações no repositório de código de origem.
- **Sondagem SCM** – esse disparador fará a sondagem de controle do código fonte regularmente. Se as alterações foram confirmadas para o repositório de código fonte, Jenkins iniciará uma nova compilação.

Sondagem SCM é um gatilho popular porque ele fornece um feedback rápido quando um desenvolvedor confirma as alterações que causam a compilação interromper. Isso é útil para equipes de alerta que algum código confirmado recentemente está causando problemas e permite que os desenvolvedores de solucionar o problema, enquanto as alterações são novas em mente.

Compilações periódicas geralmente são usadas para criar uma versão do aplicativo que pode ser distribuído para testadores. Por exemplo, uma compilação periódica pode ser agendada para sexta à noite para que os membros da equipe de controle de qualidade podem testar o trabalho da semana anterior.


### <a name="compiling-a-xamarinios-applications"></a>Compilando um aplicativo xamarin
Xamarin projetos podem ser compilados em linha de comando usando `xbuild` ou `msbuild`. O shell de comando será executado no contexto da conta de usuário que está executando Jenkins. É importante que a conta de usuário tem acesso ao perfil de provisionamento para que o aplicativo pode ser empacotado corretamente para distribuição. É possível adicionar esse comando do shell para a página de configuração de trabalho.

Role para baixo até o **criar** seção. Clique o **adicionar a etapa de compilação** botão e selecione **executar shell**, conforme ilustrado na captura de tela a seguir:

![](jenkins-walkthrough-images/image33.png "Clique no botão de etapa de compilação de adicionar e selecione Executar shell")


[!include[](~/tools/ci/includes/commandline-compile-of-xamarin-ios-ipa.md)]

### <a name="building-a-xamarinandroid-project"></a>Compilando um projeto do xamarin

Compilando um projeto do xamarin é muito semelhante ao conceito para compilar um projeto do xamarin. Para criar um APK de um projeto de xamarin, Jenkins deve ser configurado para executar as etapas a seguir:

 - Compilar o projeto usando o plug-in do MSBuild
 - Logon e zip alinham APK com uma chave de armazenamento de versão válido.

Essas duas etapas serão abordadas mais detalhadamente nas próximas duas seções.

### <a name="creating-the-apk"></a>Criando o APK

Clique no **adicionar a etapa de compilação** botão e, em seguida, selecione **compilar um projeto do Visual Studio ou a solução usando MSBuild**, conforme mostrado na captura de tela abaixo:

![](jenkins-walkthrough-images/image36.png "Criando o APK, clique no botão de etapa de compilação de adicionar e selecionar a compilação um projeto do Visual Studio ou a solução usando MSBuild")

Depois que a etapa de compilação é adicionada ao projeto, preencha os campos de formulário que aparecem. Captura de tela a seguir é um exemplo do formulário concluído:

![](jenkins-walkthrough-images/image37.png "Depois que a etapa de compilação é adicionada ao projeto, preencha os campos de formulário que aparecem")


Essa etapa de compilação executará `xbuild` no **$WORKSPACE** pasta. O arquivo de compilação do MSBuild é definido como o **Xamarin.Android.csproj** arquivo. O **argumentos de linha de comando** especificar uma compilação de versão do destino **PackageForAndroid**. O produto desta etapa serão um APK que no seguinte local:

    $WORKSPACE/[PROJECT NAME]/bin/Release

Captura de tela a seguir mostra um exemplo desse APK:

![](jenkins-walkthrough-images/image38.png "Esta captura de tela mostra um exemplo desse APK")

Este APK não está pronto para implantação, pois não foi assinado com um armazenamento de chaves privado e deve ser zip alinhado.

#### <a name="signing-and-zipaligning-the-apk-for-release"></a>Assinatura e Zipaligning APK versão

Assinatura e zipaligning o APK são tecnicamente duas tarefas separadas que são executadas por duas ferramentas de linha de comando separada do SDK do Android. No entanto, é conveniente para realizá-las em uma compilação ação. Para obter mais informações sobre como entrar e zipaligning um APK, consulte a documentação do Xamarin sobre a preparação de um aplicativo do Android para versão.

Os dois comandos exigem parâmetros de linha de comando que podem variar de um projeto para o projeto. Além disso, alguns desses parâmetros de linha de comando são senhas que não devem aparecer na saída do console quando a compilação estiver em execução. Armazenamos alguns desses parâmetros de linha de comando em variáveis de ambiente. As variáveis de ambiente necessárias para assinatura e/ou zip alinhando são descritas na tabela a seguir:

|Variável de ambiente|Descrição|
|--- |--- |
|KEYSTORE_FILE|Este é o caminho para o armazenamento de chaves para assinar o APK|
|KEYSTORE_ALIAS|A chave no armazenamento de chaves que será usada para assinar o APK.|
|INPUT_APK|O APK que é criado pela `xbuild`.|
|SIGNED_APK|O assinado APK produzido por `jarsigner`.|
|FINAL_APK|Este é o zip alinhado APK produzido por `zipalign`.|
|STORE_PASS|Esta é a senha que é usada para acessar o conteúdo do repositório de chaves para singing o arquivo.|

Conforme descrito na seção requisitos, essas variáveis de ambiente podem ser definidas durante a compilação usando o plug-in EnvInject. O trabalho deve ter uma nova compilação etapa adicionada com base em variáveis de ambiente de inserção, como mostrado na seguinte captura de tela:

![](jenkins-walkthrough-images/image39.png "O trabalho deve ter uma nova compilação etapa adicionada com base em variáveis de ambiente de inserção")

No **propriedades conteúdo** formam o campo que será exibido, são adicionadas a variáveis de ambiente, um por linha, no seguinte formato:

    ENVIRONMENT_VARIABLE_NAME = value

Captura de tela a seguir mostra as variáveis de ambiente que são necessárias para assinar o APK:

![](jenkins-walkthrough-images/image40.png "Esta captura de tela mostra as variáveis de ambiente que são necessárias para assinar o APK")

Observe que algumas das variáveis de ambiente para os arquivos APK são criadas no `WORKSPACE` variável de ambiente.

A variável de ambiente final é a senha para acessar o conteúdo do armazenamento de chaves: `STORE_PASS`. As senhas são os valores confidenciais que devem ser obscurecidos ou omitidos nos arquivos de log. O plug-in EnvInject pode ser configurado para proteger esses valores para que eles não aparecem nos logs.

Imediatamente antes do **criar** seção da configuração do trabalho é um **ambiente de compilação** seção. Quando o **injetar senhas** caixa de seleção é alternada, alguma forma de campos para aparecer. Esses campos de formulário são usados para capturar o nome e o valor da variável de ambiente. Captura de tela a seguir está um exemplo de como adicionar a `STORE_PASS` variável de ambiente:

![](jenkins-walkthrough-images/image41.png "Esta captura de tela é um exemplo de como adicionar a variável de ambiente STOREPASS")

Depois que as variáveis de ambiente tiverem sido inicializadas, a próxima etapa é adicionar uma etapa de compilação para a assinatura e zip alinhando o APK. Imediatamente após a etapa de compilação para inserir as variáveis de ambiente serão outro **executar shell** build de comando que será executado `jarsigner` e `zipalign`. Cada comando ocupará uma linha, conforme mostrado no trecho a seguir:


    jarsigner -verbose -sigalg MD5withRSA -digestalg SHA1 -keystore $KEYSTORE_FILE -storepass $STORE_PASS -signedjar $SIGNED_APK $INPUT_APK $KEYSTORE_ALIAS
    zipalign -f -v 4 $SIGNED_APK $FINAL_APK

Captura de tela a seguir mostra um exemplo de como inserir o `jarsigner` e `zipalign` comandos para a etapa:

![](jenkins-walkthrough-images/image42.png "Esta captura de tela mostra um exemplo de como inserir os comandos jarsigner e zipalign a etapa")

Depois que todas as ações de compilação estão em vigor, é uma boa prática para disparar um build manual para verificar se que tudo está funcionando. Se a compilação falhar, o **saída do Console** devem ser revisadas para obter informações sobre o que causou a compilação falhar.

### <a name="submitting-tests-to-test-cloud"></a>Envio de testes para a nuvem de teste

Testes automatizados podem ser enviados para a nuvem de teste usando comandos do shell. Para obter mais informações sobre como configurar uma execução de teste no Xamarin Test Cloud, temos guias para o uso de [Xamarin.UITest](https://developer.xamarin.com/guides/testcloud/uitest/working-with/submitting-tests-to-xamarin-test-cloud/) ou [Calabash](https://developer.xamarin.com/guides/testcloud/calabash/working-with/submitting-tests-to-xamarin-test-cloud/).


## <a name="summary"></a>Resumo

Neste guia introduzido Jenkins como um servidor de compilação no Mac OS X e configurado para compilar e preparar os aplicativos móveis para a versão do Xamarin. Em um computador Mac OS X, juntamente com vários plug-ins para oferecer suporte ao processo de compilação, implantamos Jenkins. É criado e configurado um trabalho que receber o código do TFS ou Git e, em seguida, compilar o código em um aplicativo pronto de versão. Também podemos explorou duas maneiras diferentes para agendar quando os trabalhos devem ser executados.

## <a name="related-links"></a>Links relacionados

- [Integração Contínua](https://developer.xamarin.com~/testcloud/ci/)
- [Envio de testes para Xamarin Test Cloud](https://developer.xamarin.com~/testcloud/submitting/)
- [Por que Jenkins não é suportado pelo Xamarin?](~/cross-platform/troubleshooting/questions/xamarin-jenkins.md)
