---
title: Introdução à integração contínua com o Xamarin
description: Este documento descreve a integração contínua com o Xamarin. Ele aborda vários ambientes de integração contínua e controle de versão.
ms.prod: xamarin
ms.assetid: C034200E-2947-4309-9DDD-80DAC505C43F
author: lobrien
ms.author: laobri
ms.date: 07/19/2017
ms.openlocfilehash: 48dd2e1f2f5dd9ffdad0b726c5066a9a968d2396
ms.sourcegitcommit: c1d85b2c62ad84c22bdee37874ad30128581bca6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/08/2019
ms.locfileid: "67650543"
---
# <a name="introduction-to-continuous-integration-with-xamarin"></a>Introdução à integração contínua com o Xamarin

_Integração contínua é uma prática de engenharia de software em que uma compilação automatizada é compilado e, opcionalmente, os testes de um aplicativo quando o código é adicionado ou alterado por desenvolvedores no repositório de controle de versão do projeto. Este artigo discutirá os conceitos gerais da integração contínua e algumas das opções disponíveis para a integração contínua com projetos do Xamarin._

É comum em projetos de software para que os desenvolvedores trabalhem em paralelo. Em algum momento, é necessário integrar todos esses fluxos paralelos de trabalho em uma base de código que compõe o produto final. Nos primórdios do desenvolvimento de software, essa integração foi realizada no final de um projeto, o que era um processo difícil e arriscado.

CI (integração contínua) para evitar tais complexidades, mesclando alterações de todos os desenvolvedores a base de código comum em uma base contínua, normalmente, sempre que qualquer desenvolvedor faz check-in alterações no projeto compartilhado repositório de código. Cada check-in dispara uma compilação automatizada e executa testes automatizados para verificar que o código recém-introduzidos não interrompeu nenhum código existente.  Dessa forma, CI revela erros e problemas imediatamente e assegura que todos os membros da equipe fiquem atualizados com trabalho uns dos outros. Isso resulta em uma base de código estável e coeso.

Sistemas de integração contínua tem duas partes principais:

- **Controle de versão** – versão de controle (VC), também chamado de controle de origem ou o gerenciamento de código-fonte, consolida todo o código de um projeto em um único repositório compartilhado e mantém um histórico completo de todas as alterações a todos os arquivos. Esse repositório, também conhecido como o *mainline* ou *mestre* ramificar, contém o código-fonte que será usado, por fim, para criar a produção ou versão do aplicativo. Há muitos código-fonte aberto e produtos comerciais para essa tarefa, que normalmente permite que equipes ou pessoas para dividir uma cópia do código em branches secundárias em que podem fazer alterações extensivas ou conduzir experimentos sem riscos para o branch mestre. Depois que as alterações em uma ramificação secundária forem validadas, eles podem ser reunidos mesclados novamente no branch mestre.
- **Servidor de integração contínua** – o servidor de integração contínua é responsável por coletar todos os artefatos do projeto (código-fonte, imagens, vídeos, bancos de dados, testes automatizados, etc.), Compilando o aplicativo e executando testes automatizados. Novamente, há muitas código-fonte aberto e ferramentas de servidor de CI comercial.

Normalmente, os desenvolvedores têm uma cópia de uma ou mais ramificações de trabalho em suas estações de trabalho, em que o trabalho seja inicialmente feito. Quando um conjunto apropriado de trabalho for concluído, as alterações são "verificadas em" ou "confirmadas" para a ramificação apropriada, o que propaga-os para as cópias de trabalho de outros desenvolvedores. Isso é como uma equipe garante que ele está trabalhando no mesmo código.

Novamente, com a integração contínua, o ato de confirmar as alterações faz com que o servidor de CI compilar o projeto e executar testes automatizados para verificar a exatidão do código-fonte. Se houver erros de compilação ou falhas de teste, um servidor de CI notifica o desenvolvedor responsável (por email, mensagens Instantâneas, Twitter, Growl, etc.) para que ele ou ela pode corrigir o problema. (Servidores de CI podem até mesmo se recusar a confirmação se houver falhas, que é chamado de um "check-in restringido".)

O diagrama a seguir ilustra esse processo:

[![](intro-to-ci-images/intro01-small.png "Este diagrama ilustra esse processo")](intro-to-ci-images/intro01.png#lightbox)

Aplicativos móveis apresentam desafios exclusivos para a integração contínua. Aplicativos podem exigir sensores, como o GPS ou a câmera que só estão disponíveis em dispositivos físicos. Além disso, simuladores ou emuladores são apenas uma aproximação de hardware e podem ocultar ou obscurecer problemas. No final, é necessário testar um aplicativo móvel em hardwares reais para ter certeza de que ele é verdadeiramente pronto para cliente.

O [teste do App Center](https://docs.microsoft.com/appcenter/test-cloud) cuida desse problema específico, teste aplicativos diretamente em centenas de dispositivos físicos. Os desenvolvedores escrevem testes de aceitação automatizados, permitem o teste de interface do usuário poderosa. Depois que esses testes são carregados para o App Center, o servidor de CI pode executá-los automaticamente como parte de um processo de CI conforme mostrado no diagrama a seguir:

[![](intro-to-ci-images/intro02-small.png "Depois que esses testes são carregados para o App Center, o servidor de CI pode executá-los automaticamente como parte de um processo de CI como mostrado neste diagrama")](intro-to-ci-images/intro02.png#lightbox)

## <a name="components-of-continuous-integration"></a>Componentes de integração contínua

Há um amplo ecossistema de ferramentas comerciais e de código-fonte aberto projetado para dar suporte a CI. Esta seção explica algumas das mais comuns.

### <a name="version-control"></a>Controle de versão

#### <a name="azure-devops-and-team-foundation-server"></a>DevOps do Azure e o Team Foundation Server

[DevOps do Azure](https://azure.microsoft.com/services/devops/) e [Team Foundation Server](https://visualstudio.microsoft.com/tfs/) (TFS) são ferramentas de colaboração da Microsoft para integração contínua criar serviços, tarefas de acompanhamento, agile planejamento e ferramentas de relatórios e controle de versão. Com controle de versão, DevOps do Azure e o TFS podem trabalhar com seu próprio sistema (Team Foundation Version Control ou TFVC) ou com projetos hospedados no GitHub.

- DevOps do Azure fornece serviços por meio da nuvem. Sua principal vantagem é que ele não requer hardware dedicado ou infraestrutura e pode ser acessado de qualquer lugar por meio de navegadores da web e ferramentas de desenvolvimento populares como o Visual Studio, tornando-o atraente para as equipes que são distribuídas geograficamente . Ele é gratuito para equipes de cinco desenvolvedores ou menos, após o quais licenças adicionais podem ser adquiridas para acomodar uma equipe em crescimento.
- O TFS é projetado para servidores do Windows local e acessado por meio de uma rede local ou uma conexão VPN à rede. Sua vantagem principal é totalmente controlam a configuração dos servidores de compilação e pode instalar qualquer software adicional ou serviços são necessários. TFS tem uma edição Express no nível de entrada do gratuita para pequenas equipes.

TFS e DevOps do Azure serão integrado com o Visual Studio e permitir que os desenvolvedores executar tarefas de CI de dentro a comodidade de um único IDE e controle de versão de muitos. O plug-in do Team Explorer Everywhere para Eclipse (veja abaixo) também está disponível. O Visual Studio para Mac tem [uma visualização do TFVC disponível](/visualstudio/mac/tf-version-control/).

[Pipelines de DevOps do Azure](https://docs.microsoft.com/azure/devops/pipelines/languages/xamarin/) tem suporte direto para projetos do Xamarin, no qual você criar uma definição de compilação para cada plataforma que você deseja o destino (Android, iOS e Windows). A licença apropriada do Xamarin é necessário para cada definição de compilação. Também é possível se conectar a um local, servidor do Azure DevOps compilação do TFS compatíveis com o Xamarin para essa finalidade. Com essa configuração, serão delegadas compilações enfileiradas para DevOps do Azure para o servidor local. Para obter detalhes, consulte [criar e lançar agentes](https://docs.microsoft.com/azure/devops/pipelines/agents/agents). Como alternativa, você pode usar outra ferramenta de compilação como Jenkins ou cidade de equipe.

Um resumo de todos os recursos de gerenciamento de ciclo de vida de aplicativos (ALM) do Visual Studio, DevOps do Azure e o Team Foundation Server, consulte [DevOps com aplicativos Xamarin](https://docs.microsoft.com/visualstudio/cross-platform/application-lifecycle-management-alm-with-xamarin-apps).

#### <a name="team-explorer-everywhere"></a>Team Explorer Everywhere

[Team Explorer Everywhere](https://docs.microsoft.com/azure/devops/java/download-eclipse-plug-in/) traz a potência do Team Foundation Server e DevOps do Azure para equipes de desenvolvimento fora do Visual Studio. Ele permite aos desenvolvedores para se conectar a projetos de equipe no local ou na nuvem com o Eclipse ou o cliente de linha de comando de plataforma cruzada para OS X e Linux. Team Explorer Everywhere fornece total acesso ao controle de versão (incluindo Git), os itens de trabalho e recursos de compilação para plataformas não Windows.

#### <a name="git"></a>Git

[Git](http://git-scm.com) é uma solução de controle de versão de software livre que foi originalmente desenvolvida para gerenciar o código-fonte para o kernel do Linux. É um sistema muito rápido e flexível que é popular com projetos de software de todos os tamanhos. Ele é facilmente dimensionada de única aos desenvolvedores acesso de Internet ruim para equipes grandes que abrangem todo o mundo. Git também torna a ramificação muito fácil, que por sua vez pode incentivar fluxos paralelos de desenvolvimento com risco mínimo.

Git pode operar inteiramente por meio de navegadores da web, ou [clientes de GUI](http://git-scm.com/downloads/guis) que são executados no Windows, Mac OSX e Linux. Ele é gratuito para repositórios públicos; repositórios privados exigem uma [plano pago](https://github.com/pricing).

As versões atuais do Visual Studio para Windows e Mac oferecem suporte nativo para o Git. A Microsoft fornece um [extensão que pode ser baixado para o Git](http://visualstudiogallery.msdn.microsoft.com/abafc7d6-dcaa-40f4-8a5e-d6724bdb980c) para versões mais antigas do Visual Studio. Conforme observado acima, DevOps do Azure e o TFS pode usar o Git para controle de versão em vez de TFVC.

#### <a name="subversion"></a>Subversion

[O Subversion](http://subversion.apache.org) (SVN) é um sistema de controle de versão de software livre popular que tenha sido usado desde 2000. SVN é executado em todas as versões atuais dos X, Windows, FreeBSD, Linux e Unix. O Visual Studio para Mac tem suporte nativo para SVN. Há extensões de terceiros que trazem o suporte do SVN para Visual Studio.

### <a name="continuous-integration-environments"></a>Ambientes de integração contínua

Como configurar um ambiente de contínua integração significa que a combinação de um sistema de controle de versão com um serviço de compilação.  Para a última opção, dois os mais comuns são:

- [Pipelines do Azure](https://docs.microsoft.com/azure/devops/pipelines/) é o sistema de compilação do DevOps do Azure e do TFS. Ele é totalmente integrado ao Visual Studio, que faz com que ele conveniente para desenvolvedores disparar a cria, automaticamente executar testes e ver os resultados.
- Jenkins é um servidor de CI do código-fonte aberto com um rico ecossistema de plug-ins para dar suporte a todos os tipos de desenvolvimento de software. Ele é executado no Windows e Mac OS X. Jenkins não está integrado com qualquer IDE específico. Em vez disso, ele é configurado e gerenciado por meio de uma interface da web. Jenkins CI também é fácil de instalar e configurar tornando-o atraente para pequenas equipes.

Você pode usar o TFS e do Azure DevOps por si só, ou você pode usar o Jenkins em combinação com o Git ou TFS e do Azure DevOps conforme descrito nas seções a seguir.

#### <a name="azure-devops-and-team-foundation-server"></a>DevOps do Azure e o Team Foundation Server

Conforme discutido, DevOps do Azure e o Team Foundation Server fornece tanto versão controlar e criação de serviços. Serviços de compilação sempre exigem uma licença Xamarin Business ou Enterprise para cada plataforma de destino.

Com DevOps do Azure, você cria uma definição de compilação separada para cada plataforma de destino e insira a licença apropriada existe. Depois de configurado, o Azure DevOps será executar compilações e testes na nuvem. Ver [Pipelines do Azure](https://docs.microsoft.com/azure/devops/pipelines/) para obter mais detalhes.

Com o Team Foundation Server, você deve configurar um computador de compilação da seguinte maneira para plataformas de destino específico:

- **Android e Windows:** Instalar o Visual Studio e as ferramentas do Xamarin (para Android e Windows ambos) e configurar com suas licenças do Xamarin. Também é necessário mover o SDK do Android em um local compartilhado no servidor onde o agente de compilação do TFS pode encontrá-lo. Para obter detalhes, consulte [TFVC configurando](https://docs.microsoft.com/azure/devops/repos/tfvc/overview).
- **iOS e Xamarin:** Instale o Visual Studio e as ferramentas do Xamarin no servidor do Windows com a licença apropriada. Em seguida, instale o Visual Studio para Mac em um computador Mac OS X acessível pela rede, que servirá como um host de build e criar o pacote final do aplicativo (IPA para o aplicativo para OS X, iOS).

O diagrama a seguir ilustra esse topografia:

[![](intro-to-ci-images/intro03-small.png "Este diagrama ilustra essa topografia")](intro-to-ci-images/intro03.png#lightbox)

Também é possível vincular a um servidor TFS local a um projeto de DevOps do Azure para que as compilações de DevOps do Azure são delegadas ao servidor local. Para obter detalhes, consulte [criar e lançar agentes](https://docs.microsoft.com/azure/devops/pipelines/agents/agents/).

#### <a name="azure-devops-and-jenkins"></a>Jenkins e DevOps do azure

Se você usar o Jenkins para compilar seus aplicativos, você pode armazenar o código no DevOps do Azure ou o Team Foundation Server e continuar a usar o Jenkins para os builds de CI. Quando você envia o código para o repositório do Git de seu projeto de equipe ou quando você verificar o código tfvc, você pode disparar um build do Jenkins. Para obter detalhes, consulte [Jenkins com o Azure DevOps](https://docs.microsoft.com/azure/devops/service-hooks/services/jenkins).

[![](intro-to-ci-images/intro04-small.png "Se você usar o Jenkins para compilar seus aplicativos, você pode armazenar o código no DevOps do Azure ou o Team Foundation Server e continuar a usar o Jenkins para os builds de CI")](intro-to-ci-images/intro04.png#lightbox)

#### <a name="git-and-jenkins"></a>Git e Jenkins

Outro ambiente de CI comuns pode ser totalmente OS X com base. Este cenário envolve usando o Git para controle do código fonte e o Jenkins para o servidor de compilação. Ambos estão em execução em um único computador Mac OS X com o Visual Studio para Mac instalados. Isso é muito semelhante do DevOps do Azure + ambiente Jenkins discutidos na seção anterior:

[![](intro-to-ci-images/intro05-small.png "Isso é muito semelhante do DevOps do Azure + ambiente Jenkins discutidos na seção anterior")](intro-to-ci-images/intro05.png#lightbox)

> [!IMPORTANT]
> **Jenkins é [não tem suporte da Microsoft](~/cross-platform/troubleshooting/questions/xamarin-jenkins.md).**
