---
title: Introdução à integração contínua com o Xamarin
description: Este documento descreve a integração contínua com o Xamarin. Ele aborda o controle de versão e vários ambientes de integração contínua.
ms.prod: xamarin
ms.assetid: C034200E-2947-4309-9DDD-80DAC505C43F
author: conceptdev
ms.author: crdun
ms.date: 07/19/2017
ms.openlocfilehash: d335a107d1520db3c76ee602d38adcb129f122b0
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70293100"
---
# <a name="introduction-to-continuous-integration-with-xamarin"></a>Introdução à integração contínua com o Xamarin

_A integração contínua é uma prática de engenharia de software na qual uma compilação automatizada compila e, opcionalmente, testa um aplicativo quando o código é adicionado ou alterado por desenvolvedores no repositório de controle de versão do projeto. Este artigo abordará os conceitos gerais de integração contínua e algumas das opções disponíveis para a integração contínua com os projetos do Xamarin._

É comum em projetos de software para que os desenvolvedores trabalhem em paralelo. Em algum momento, é necessário integrar todos esses fluxos paralelos de trabalho em uma base de código que compõe o produto final. Nos primórdios do desenvolvimento de software, essa integração foi realizada no final de um projeto, que era um processo difícil e arriscado.

A CI (integração contínua) evita essas complexidades ao mesclar as alterações de cada desenvolvedor na base de código comum em uma base contínua, geralmente sempre que os desenvolvedores verificam as alterações no repositório de código compartilhado do projeto. Cada check-in dispara uma compilação automatizada e executa testes automatizados para verificar se o código introduzido recentemente não interrompeu nenhum código existente.  Dessa forma, o CI superfícies erros e problemas imediatamente e garante que todos os membros da equipe permaneçam atualizados com o trabalho de cada um. Isso resulta em uma base de código coesa e estável.

Os sistemas de integração contínua têm duas partes principais:

- **Controle de versão** – controle de versão (VC), também chamado de controle de origem ou gerenciamento de código-fonte, consolida todo o código de um projeto em um único repositório compartilhado e mantém um histórico completo de cada alteração em cada arquivo. Esse repositório, geralmente chamado de Branch principal *ou* *mestre* , contém o código-fonte que será usado em última análise para criar a versão de produção ou de lançamento do aplicativo. Há muitos produtos comerciais e de software livre para essa tarefa, que normalmente permitem que equipes ou indivíduos forkm uma cópia do código em filiais secundárias, onde podem fazer grandes alterações ou conduzir experimentos sem risco para a ramificação mestre. Depois que as alterações em uma ramificação secundária são validadas, elas podem então ser mescladas novamente no Branch mestre.
- **Servidor de integração contínua** – o servidor de integração contínua é responsável por coletar todos os artefatos de um projeto (código-fonte, imagens, vídeos, bancos de dados, testes automatizados, etc.), compilando o aplicativo e executando os testes automatizados. Mais uma vez, há muitas ferramentas de servidor de CI comercial e de software livre.

Os desenvolvedores normalmente têm uma cópia de trabalho de uma ou mais ramificações em suas estações de trabalho, em que a tarefa é feita inicialmente. Depois que um conjunto apropriado de trabalhos é concluído, as alterações são "verificadas" ou "confirmadas" no Branch apropriado, que os propaga para as cópias de trabalho de outros desenvolvedores. É assim que a equipe garante que todos estejam trabalhando no mesmo código.

Novamente, com a integração contínua, o ato de confirmar as alterações faz com que o servidor de CI compile o projeto e execute testes automatizados para verificar a exatidão do código-fonte. Se houver erros de compilação ou falhas de teste, um servidor de CI notificará o desenvolvedor responsável (por email, mensagens instantâneas, Twitter, Growl, etc.) para que ele possa corrigir o problema. (Os servidores de CI podem até mesmo recusar a confirmação se houver falhas, que é chamada de "check-in restrito".)

O diagrama a seguir ilustra esse processo:

[![](intro-to-ci-images/intro01-small.png "Este diagrama ilustra esse processo")](intro-to-ci-images/intro01.png#lightbox)

Os aplicativos móveis apresentam desafios exclusivos para a integração contínua. Os aplicativos podem exigir sensores como o GPS ou a câmera que estão disponíveis somente em dispositivos físicos. Além disso, simuladores ou emuladores são apenas uma aproximação de hardware e podem ocultar ou obscurecer problemas. No final, é necessário testar um aplicativo móvel em hardware real para ter certeza de que ele está realmente pronto para o cliente.

O [teste de App Center](https://docs.microsoft.com/appcenter/test-cloud) resolve esse problema específico testando aplicativos diretamente em centenas de dispositivos físicos. Os desenvolvedores escrevem testes de aceitação automatizados, o que permite um teste de interface de usuário poderoso. Depois que esses testes são carregados no App Center, o servidor de CI pode executá-los automaticamente como parte de um processo de CI, conforme mostrado no diagrama a seguir:

[![](intro-to-ci-images/intro02-small.png "Depois que esses testes são carregados no App Center, o servidor de CI pode executá-los automaticamente como parte de um processo de CI, conforme mostrado neste diagrama")](intro-to-ci-images/intro02.png#lightbox)

## <a name="components-of-continuous-integration"></a>Componentes da integração contínua

Há um amplo ecossistema de ferramentas comerciais e de software livre projetadas para dar suporte ao CI. Esta seção explica algumas das mais comuns.

### <a name="version-control"></a>Controle de versão

#### <a name="azure-devops-and-team-foundation-server"></a>DevOps e Team Foundation Server do Azure

O [Azure DevOps](https://azure.microsoft.com/services/devops/) and [Team Foundation Server](https://visualstudio.microsoft.com/tfs/) (TFS) são ferramentas colaborativas da Microsoft para serviços de Build de integração contínua, rastreamento de tarefas, ferramentas de planejamento e relatório Agile e controle de versão. Com o controle de versão, o Azure DevOps e o TFS podem trabalhar com seu próprio sistema (Controle de Versão do Team Foundation ou TFVC) ou com projetos hospedados no GitHub.

- O Azure DevOps fornece serviços por meio da nuvem. Sua principal vantagem é que ele não requer hardware ou infraestrutura dedicada e pode ser acessado de qualquer lugar por meio de navegadores da Web e de ferramentas de desenvolvimento populares, como o Visual Studio, tornando-o atraente para as equipes distribuídas geograficamente . Ele é gratuito para equipes de cinco desenvolvedores ou menos, após o qual licenças adicionais podem ser compradas para acomodar uma equipe crescente.
- O TFS foi projetado para servidores Windows locais e acessado por meio de uma rede local ou de uma conexão VPN com essa rede. Sua principal vantagem é que você controla totalmente a configuração dos servidores de compilação e pode instalar qualquer software ou serviço adicional necessário. O TFS tem uma Express Edition de nível de entrada gratuita para pequenas equipes.

Tanto o TFS quanto o Azure DevOps são totalmente integrados ao Visual Studio e permitem que os desenvolvedores executem muitas tarefas de controle de versão e CI de dentro do conforto de um único IDE. O plug-in Team Explorer Everywhere para Eclipse (veja abaixo) também está disponível. Visual Studio para Mac tem [uma visualização do TFVC disponível](/visualstudio/mac/tf-version-control/).

Os [pipelines do Azure DevOps](https://docs.microsoft.com/azure/devops/pipelines/languages/xamarin/) têm suporte direto para projetos do Xamarin, dentro dos quais você cria uma definição de compilação para cada plataforma que deseja direcionar (Android, Ios e Windows). A licença apropriada do Xamarin é necessária para cada definição de compilação. Também é possível conectar um servidor de compilação TFS compatível com Xamarin local ao Azure DevOps para essa finalidade. Com essa configuração, as compilações enfileiradas no Azure DevOps serão delegadas ao servidor local. Para obter detalhes, consulte [Compilar e liberar agentes](https://docs.microsoft.com/azure/devops/pipelines/agents/agents). Como alternativa, você pode usar outra ferramenta de compilação, como Jenkins ou cidade da equipe.

Um resumo completo de todos os recursos de ALM (gerenciamento do ciclo de vida do aplicativo) do Visual Studio, do Azure DevOps e do Team Foundation Server, consulte [DevOps com aplicativos Xamarin](https://docs.microsoft.com/visualstudio/cross-platform/application-lifecycle-management-alm-with-xamarin-apps).

#### <a name="team-explorer-everywhere"></a>Team Explorer Everywhere

[Team Explorer Everywhere](https://docs.microsoft.com/azure/devops/java/download-eclipse-plug-in/) traz a potência do Team Foundation Server e do Azure DevOps para o desenvolvimento de equipes fora do Visual Studio. Ele permite que os desenvolvedores se conectem a projetos de equipe locais ou na nuvem do Eclipse ou do cliente de linha de comando de plataforma cruzada para OS X e Linux. Team Explorer Everywhere fornece acesso completo ao controle de versão (incluindo git), itens de trabalho e recursos de compilação para plataformas não Windows.

#### <a name="git"></a>Git

O [git](http://git-scm.com) é uma solução popular de controle de versão de software livre que foi originalmente desenvolvida para gerenciar o código-fonte do kernel do Linux. É um sistema muito rápido e flexível que é popular com projetos de software de todos os tamanhos. Ele é facilmente dimensionado de desenvolvedores únicos com acesso insatisfatório à Internet a grandes equipes que abrangem o mundo. O Git também torna a ramificação muito fácil, o que, por sua vez, pode incentivar fluxos paralelos de desenvolvimento com risco mínimo.

O Git pode operar totalmente por meio de navegadores da Web ou de [clientes de GUI](http://git-scm.com/downloads/guis) que são executados no Linux, no Mac OSX e no Windows. Ele é gratuito para repositórios públicos; os repositórios privados exigem um [plano pago](https://github.com/pricing).

As versões atuais do Visual Studio para Windows e Mac fornecem suporte nativo para git. A Microsoft fornece uma [extensão baixável para git](http://visualstudiogallery.msdn.microsoft.com/abafc7d6-dcaa-40f4-8a5e-d6724bdb980c) para versões mais antigas do Visual Studio. Conforme observado acima, o Azure DevOps e o TFS podem usar o Git para controle de versão em vez de TFVC.

#### <a name="subversion"></a>Subversion

[Subversão](http://subversion.apache.org) (SVN) é um sistema de controle de versão de software livre popular que está em uso desde 2000. O SVN é executado em todas as versões modernas do OS X, Windows, FreeBSD, Linux e UNIX. Visual Studio para Mac tem suporte nativo para SVN. Há extensões de terceiros que trazem suporte de SVN ao Visual Studio.

### <a name="continuous-integration-environments"></a>Ambientes de integração contínua

Configurar um ambiente de integração contínua significa combinar um sistema de controle de versão com um serviço de compilação.  Para o último, os dois mais comuns são:

- [Azure pipelines](https://docs.microsoft.com/azure/devops/pipelines/) é o sistema de compilação do Azure DevOps e do TFS. Ele é totalmente integrado ao Visual Studio, o que torna conveniente para os desenvolvedores disparar compilações, executar testes automaticamente e ver os resultados.
- O Jenkins é um servidor de CI de software livre com um rico ecossistema de plug-ins para dar suporte a todos os tipos de desenvolvimento de softwares. Ele é executado no Windows e no Mac OS X. o Jenkins não é integrado a nenhum IDE específico. Em vez disso, ele é configurado e gerenciado por meio de uma interface da Web. O Jenkins CI também é fácil de instalar e configurar, o que o torna atraente para pequenas equipes.

Você pode usar o TFS/Azure DevOps sozinho ou pode usar o Jenkins em combinação com TFS/Azure DevOps ou git, conforme descrito nas seções a seguir.

#### <a name="azure-devops-and-team-foundation-server"></a>DevOps e Team Foundation Server do Azure

Conforme discutido, o Azure DevOps e o Team Foundation Server fornecem o controle de versão e os serviços de compilação. Os serviços de compilação sempre exigem uma licença corporativa ou empresarial do Xamarin para cada plataforma de destino.

Com o Azure DevOps, você cria uma definição de compilação separada para cada plataforma de destino e insere a licença apropriada ali. Uma vez configurado, o Azure DevOps executará compilações e testes na nuvem. Consulte [Azure pipelines](https://docs.microsoft.com/azure/devops/pipelines/) para obter mais detalhes.

Com Team Foundation Server, você configura um computador de compilação da seguinte maneira para plataformas de destino específicas:

- **Android e Windows:** Instale o Visual Studio e as ferramentas do Xamarin (para Android e Windows ambas) e configure com suas licenças do Xamarin. Também é necessário mover o SDK do Android para um local compartilhado no servidor em que o agente de compilação do TFS possa encontrá-lo. Para obter detalhes, consulte [Configurando TFVC](https://docs.microsoft.com/azure/devops/repos/tfvc/overview).
- **iOS e Xamarin:** Instale o Visual Studio e as ferramentas do Xamarin no Windows Server com a licença apropriada. Em seguida, instale Visual Studio para Mac em uma máquina Mac OS X acessível pela rede, que servirá como um host de compilação e criará o pacote de aplicativo final (IPA para iOS, aplicativo para OS X).

O diagrama a seguir ilustra essa topografia:

[![](intro-to-ci-images/intro03-small.png "Este diagrama ilustra essa topografia")](intro-to-ci-images/intro03.png#lightbox)

Também é possível vincular um servidor TFS local a um projeto DevOps do Azure para que as compilações do Azure DevOps sejam delegadas ao servidor local. Para obter detalhes, consulte [Compilar e liberar agentes](https://docs.microsoft.com/azure/devops/pipelines/agents/agents/).

#### <a name="azure-devops-and-jenkins"></a>DevOps e Jenkins do Azure

Se você usar o Jenkins para criar seus aplicativos, poderá armazenar seu código no Azure DevOps ou Team Foundation Server e continuar a usar o Jenkins para suas compilações de CI. Você pode disparar uma compilação Jenkins ao enviar código por push para o repositório Git do projeto de equipe ou ao verificar o código em TFVC. Para obter detalhes, consulte [Jenkins com o Azure DevOps](https://docs.microsoft.com/azure/devops/service-hooks/services/jenkins).

[![](intro-to-ci-images/intro04-small.png "Se você usar o Jenkins para criar seus aplicativos, poderá armazenar seu código no Azure DevOps ou Team Foundation Server e continuar a usar o Jenkins para suas compilações de CI")](intro-to-ci-images/intro04.png#lightbox)

#### <a name="git-and-jenkins"></a>Git e Jenkins

Outro ambiente de CI comum pode ser totalmente baseado em OS X. Esse cenário envolve o uso do git para controle de código-fonte e Jenkins para o servidor de compilação. Ambos estão em execução em um único computador Mac OS X com Visual Studio para Mac instalado. Isso é muito semelhante ao ambiente do Azure DevOps + Jenkins abordado na seção anterior:

[![](intro-to-ci-images/intro05-small.png "Isso é muito semelhante ao ambiente do Azure DevOps + Jenkins discutido na seção anterior")](intro-to-ci-images/intro05.png#lightbox)

> [!IMPORTANT]
> **O Jenkins [não tem suporte da Microsoft](~/cross-platform/troubleshooting/questions/xamarin-jenkins.md).**
