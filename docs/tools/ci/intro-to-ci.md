---
title: "Introdução à integração contínua com o Xamarin"
description: "Integração contínua é uma prática de engenharia de software no qual uma compilação automatizada compila e, opcionalmente, testa um aplicativo quando o código é adicionado ou alterado por desenvolvedores no repositório de controle de versão do projeto. Este artigo discutirá os conceitos gerais de integração contínua e algumas das opções disponíveis para a integração contínua com Xamarin projetos."
ms.topic: article
ms.prod: xamarin
ms.assetid: C034200E-2947-4309-9DDD-80DAC505C43F
ms.technology: xamarin-cross-platform
author: topgenorth
ms.author: toopge
ms.date: 07/19/2017
ms.openlocfilehash: 2a8849a75f3c26a83a030700878a3195fde7515f
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2018
---
# <a name="introduction-to-continuous-integration-with-xamarin"></a>Introdução à integração contínua com o Xamarin

_Integração contínua é uma prática de engenharia de software no qual uma compilação automatizada compila e, opcionalmente, testa um aplicativo quando o código é adicionado ou alterado por desenvolvedores no repositório de controle de versão do projeto. Este artigo discutirá os conceitos gerais de integração contínua e algumas das opções disponíveis para a integração contínua com Xamarin projetos._

É comum em projetos de software para os desenvolvedores trabalhem em paralelo. Em algum momento, é necessário integrar todos esses paralelos fluxos de trabalho em uma base de código que compõe o produto final. No início do desenvolvimento de software, essa integração foi executada do final de um projeto, que era um processo difícil e arriscado.

CI (integração contínua) para evitar tais complexidades, mesclando alterações de todos os desenvolvedores a base de código comum em uma base contínua, normalmente, sempre que verifica se todos os desenvolvedores alterações no projeto compartilhado repositório de código. Cada check-in dispara uma compilação automatizada e executa testes automatizados para verificar se o código introduzido recentemente não interromper qualquer código existente.  Dessa forma, CI revela erros e problemas imediatamente e assegura que todos os membros da equipe permaneçam atualizados com cada um dos outros trabalho. Isso resulta em uma base de código consistente e estável.

Os sistemas de integração contínua têm duas partes principais:

-  **Controle de versão** – versão de controle (VC), também chamado de controle de origem ou o gerenciamento de código fonte, consolida todo o código do projeto em um único repositório compartilhado e mantém um histórico completo de todas as alterações a todos os arquivos. Esse repositório, conhecida como o *mainline* ou *mestre* branch, contém o código-fonte que será usado, por fim, para a produção de compilação ou a versão do aplicativo. Há muitos código-fonte aberto e produtos comerciais para esta tarefa, que normalmente permite que as equipes ou pessoas físicas para dividir uma cópia do código em ramificações secundárias em que podem fazer alterações extensivas ou realizar experiências sem o risco para a ramificação mestre. Depois que as alterações em uma ramificação secundária forem validadas, eles podem ser todos juntos mesclados a ramificação mestre.
-  **Servidor de integração contínua** – o servidor de integração contínua é responsável por coletar todos os artefatos do projeto (código-fonte, imagens, vídeos, bancos de dados, testes automatizados, etc.), compilar o aplicativo e executar os testes automatizados. Novamente, há muitas código-fonte aberto e ferramentas de servidor CI comercial.

Normalmente, os desenvolvedores têm uma cópia de uma ou mais ramificações de trabalho em suas estações de trabalho, onde o trabalho é realizado inicialmente. Quando um conjunto apropriado de trabalho for concluído, as alterações são "check em" ou "confirmadas" para a ramificação apropriada, o que propaga para as cópias de trabalho de outros desenvolvedores. Isso é como uma equipe garante que eles está trabalhando no mesmo código.

Novamente, com a integração contínua, o ato de confirmar as alterações faz com que o servidor de CI compilar o projeto e executar testes automatizados para verificar a exatidão do código-fonte. Se houver erros de compilação ou falhas de teste, um servidor de CI notifica o desenvolvedor responsável (via email, mensagens Instantâneas, Twitter, Growl, etc.) para que ele ou ela pode corrigir o problema. (Servidores de CI podem até mesmo se recusar a confirmação se houver falhas, que é chamado de um "-check in restrito".)

O diagrama a seguir ilustra esse processo:

[![](intro-to-ci-images/intro01-small.png "Este diagrama ilustra esse processo")](intro-to-ci-images/intro01.png#lightbox)

Aplicativos móveis apresentam desafios exclusivos para a integração contínua. Aplicativos podem exigir sensores como a câmera ou GPS que estão disponíveis somente em dispositivos físicos. Além disso, simuladores emuladores são apenas uma aproximação do hardware e podem ocultar ou oculte problemas. No final, é necessário testar um aplicativo móvel no hardware real para ter certeza de que é realmente prontos para o cliente.

O [aplicativo Center teste](https://docs.microsoft.com/en-us/appcenter/test-cloud) cuida desse problema específico, teste de aplicativos diretamente em centenas de dispositivos físicos. Os desenvolvedores escrever testes automatizados aceitações que permitem para teste de interface do usuário avançada. Depois que esses testes são carregados no centro do aplicativo, o servidor de CI pode executá-los automaticamente como parte de um processo de CI conforme mostrado no diagrama a seguir:

[![](intro-to-ci-images/intro02-small.png "Depois que esses testes são carregados no centro do aplicativo, o servidor de CI pode executá-los automaticamente como parte de um processo de CI como mostrado neste diagrama")](intro-to-ci-images/intro02.png#lightbox)

# <a name="components-of-continuous-integration"></a>Componentes de integração contínua

Há um ecossistema amplo de ferramentas comerciais e de código-fonte aberto projetado para suporte de CI. Esta seção explica algumas das mais comuns.

## <a name="version-control"></a>Controle de versão

### <a name="visual-studio-team-services-and-team-foundation-server"></a>Visual Studio Team Services e o Team Foundation Server

[Visual Studio Team Services](https://www.visualstudio.com/products/visual-studio-team-services-vs) (VSTS) e [Team Foundation Server](http://msdn.microsoft.com/en-us/vstudio/ff637362.aspx) (TFS) são serviços, rastreamento de tarefas, planejamento do agile e relatório ferramentas e versão de compilação de ferramentas de colaboração da Microsoft para a integração contínua controle. Com controle de versão, VSTS e TFS pode trabalhar com seu próprio sistema (controle de versão do Team Foundation ou TFVC) ou com projetos hospedados no GitHub.

 - Visual Studio Team Services fornece serviços por meio da nuvem. Sua principal vantagem é que ele não requer hardware dedicado ou infraestrutura e pode ser acessado de qualquer lugar por meio de navegadores da web e ferramentas populares de desenvolvimento como o Visual Studio, tornando-o atraente para equipes que estão geograficamente distribuído. É gratuito para equipes de cinco desenvolvedores ou menos, após o qual licenças adicionais podem ser adquiridas para acomodar uma equipe crescente.
 - TFS é projetado para servidores do Windows local e acessado por meio de uma rede local ou uma conexão VPN para a rede. Sua vantagem principal é totalmente controlar a configuração dos servidores de compilação e pode instalar qualquer software ou serviço adicional é necessários. TFS tem uma edição Express básica gratuita para pequenas equipes.

TFS e VSTS é integrado com o Visual Studio e permitir que os desenvolvedores realizar tarefas de CI de dentro de um único IDE conforto e controle de versão de muitos. O plug-in do Team Explorer Everywhere para Eclipse (veja abaixo) também está disponível. O Visual Studio para Mac não oferece nenhum suporte para o TFS ou VSTS.

Sistema de compilação do Visual Studio Team Service tem suporte direto para projetos de Xamarin, em que você criar uma definição de compilação para cada plataforma de destino (Android, iOS e Windows). A licença apropriada do Xamarin é necessária para cada definição de compilação. Também é possível conectar-se a um local, compatíveis com o Xamarin TFS build server para Visual Studio Team Services para essa finalidade. Com essa configuração, compilações que estão na fila para VSTS serão delegadas ao servidor local. Para obter detalhes, consulte [implantar e configurar um servidor de compilação](https://msdn.microsoft.com/en-us/library/ms181712.aspx). Como alternativa, você pode usar outra ferramenta de compilação como Jenkins ou equipe cidade.

Um resumo de todos os recursos de gerenciamento de ciclo de vida do aplicativo (ALM) do Visual Studio, o Visual Studio Team Services e o Team Foundation Server, consulte [Application Lifecycle Management com aplicativos Xamarin](https://msdn.microsoft.com/en-us/library/mt162217(v=vs.140).aspx) no MSDN.


### <a name="team-explorer-everywhere"></a>Team Explorer Everywhere

[Team Explorer Everywhere](http://msdn.microsoft.com/en-us/library/gg413285.aspx) traz a potência do Team Foundation Server e do Visual Studio Team Services para equipes de desenvolvimento de fora do Visual Studio. Ela permite aos desenvolvedores para se conectar a projetos de equipe no local ou na nuvem do Eclipse ou o cliente de linha de comando de plataforma cruzada para OS X e Linux. Team Explorer Everywhere completa fornece acesso ao controle de versão (incluindo Git), itens de trabalho e criar recursos para plataformas não Windows.


### <a name="git"></a>Git

[Git](http://git-scm.com) é uma solução de controle de versão de software livre populares que foi originalmente desenvolvida para gerenciar o código-fonte para o kernel do Linux. É um sistema flexível muito rápido popular de projetos de software de todos os tamanhos. Ele é facilmente dimensionada de único desenvolvedores com acesso à Internet baixo a grandes equipes que abrangem todo o mundo. Git também facilita a ramificação muito fácil, que por sua vez pode encorajar os fluxos paralelos de desenvolvimento com o risco mínimo.

Git pode operar totalmente por meio de navegadores da web, ou pelo [clientes GUI](http://git-scm.com/downloads/guis) que são executados no Windows, Linux e Mac OSX. É gratuito para repositórios públicos; repositórios privados exigem um [paga plano](https://github.com/pricing).

Visual Studio 2015 e o Visual Studio para Mac dão suporte nativo para Git; para versões anteriores, a Microsoft fornece um [extensão que pode ser baixado para Git](http://visualstudiogallery.msdn.microsoft.com/abafc7d6-dcaa-40f4-8a5e-d6724bdb980c). Conforme observado acima, o Visual Studio Team Services e o TFS pode usar o Git para controle de versão em vez de TFVC.


### <a name="subversion"></a>Subversão

[Subversão](http://subversion.apache.org) (SVN) é um sistema de controle de versão de código aberto popular que tenha sido usado desde 2000. SVN é executado em todas as versões atuais do Windows, OS X, FreeBSD, Linux e Unix. O Visual Studio para Mac tem suporte nativo para SVN. Existem extensões de terceiros que oferecer suporte SVN para o Visual Studio.


## <a name="continuous-integration-environments"></a>Ambientes de integração contínua

Configurando um ambiente de integração contínua significa que a combinação de um sistema de controle de versão com um serviço de compilação.  Para o último, duas as mais comuns são:

- [Team Foundation Build](https://msdn.microsoft.com/Library/vs/alm/Build/overview) é o sistema de compilação do Visual Studio Team Services e o TFS. Ele é integrado com o Visual Studio, que faz com que seja conveniente para desenvolvedores disparar cria, automaticamente executar testes e ver os resultados.
- Jenkins é um servidor de CI do código-fonte aberto que, com um rico ecossistema de plug-ins para dar suporte a todos os tipos de desenvolvimento de software. Ele é executado no Windows e Mac OS X. Jenkins não está integrado com qualquer IDE específico. Em vez disso, ele é configurado e gerenciado por meio de uma interface da web. Jenkins CI também é fácil de instalar e configurar o que torna atraente para pequenas equipes.

Você pode usar o TFS/VSTS por si só, ou você pode usar Jenkins em combinação com o TFS/VSTS ou Git, conforme descrito nas seções a seguir.

### <a name="visual-studio-team-services-and-team-foundation-server"></a>Visual Studio Team Services e o Team Foundation Server

Conforme discutido, o Visual Studio Team Services e o Team Foundation Server fornece as versões de controle e criar serviços. Serviços de compilação sempre exigem uma licença Xamarin Business ou Enterprise para cada plataforma de destino.

Com o Visual Studio Team Services, você cria uma definição de compilação separada para cada plataforma de destino e insira a licença apropriada existe. Uma vez configurado, VSTS executará builds e testes na nuvem. Consulte [Team Foundation Build](https://msdn.microsoft.com/Library/vs/alm/Build/overview) para obter mais detalhes.

Com o Team Foundation Server, você deve configurar um computador de compilação da seguinte maneira para plataformas de destino específico:

- **Android e Windows:** instale o Visual Studio e o Xamarin ferramentas (para o Android e Windows ambos) e configurar com as licenças do Xamarin. Também é necessário mover o SDK do Android para um local compartilhado no servidor onde o agente de compilação do TFS possa localizá-lo. Para obter detalhes, consulte [TFVC configurando](https://docs.microsoft.com/vsts/tfvc/overview).
- **iOS e Xamarin:** instale o Visual Studio e as ferramentas Xamarin no servidor do Windows com a licença apropriada. Em seguida, instale o Visual Studio para Mac em um computador Mac OS X acessível pela rede, que servirá como um host de compilação e criar o pacote de aplicativo final (IPA para iOS, o aplicativo para OS X).

O diagrama a seguir ilustra essa topografia:

[![](intro-to-ci-images/intro03-small.png "Este diagrama ilustra este topografia")](intro-to-ci-images/intro03.png#lightbox)

Também é possível vincular a um servidor TFS local a um projeto do Visual Studio Team Services para que as compilações do VSTS são delegadas ao servidor local. Para obter detalhes, consulte [implantar e configurar um servidor de compilação](http://msdn.microsoft.com/en-us/library/ms181712.aspx) no MSDN.

### <a name="visual-studio-team-services-and-jenkins"></a>Jenkins e do visual Studio Team Services

Se você usar Jenkins para criar seus aplicativos, você pode armazenar o seu código no Visual Studio Team Services ou o Team Foundation Server e continuar a usar Jenkins para as compilações de CI. Você pode disparar um build Jenkins quando você enviar por push código ao seu projeto de equipe repositório Git ou opção de código ao TFVC. Para obter detalhes, consulte [Jenkins com o Visual Studio Team Services](https://www.visualstudio.com/en-us/docs/marketplace/integrate/service-hooks/services/jenkins).

[![](intro-to-ci-images/intro04-small.png "Se você usar Jenkins para criar seus aplicativos, você pode armazenar seu código no Visual Studio Team Services ou o Team Foundation Server e continuar a usar Jenkins para as compilações de CI")](intro-to-ci-images/intro04.png#lightbox)

### <a name="git-and-jenkins"></a>Git e Jenkins

Outro ambiente CI comum pode ser totalmente OS X com base. Este cenário envolve o uso do Git para controle do código fonte e Jenkins para o servidor de compilação. Ambos estão em execução em um único computador Mac OS X com o Visual Studio para Mac instalado. Isso é muito semelhante para o Visual Studio Team Services + ambiente Jenkins discutidos na seção anterior:

[![](intro-to-ci-images/intro05-small.png "Isso é muito semelhante para o Visual Studio Team Services + ambiente Jenkins discutidos na seção anterior")](intro-to-ci-images/intro05.png#lightbox)

> [!IMPORTANT]
> **Observação: Jenkins é [Xamarin não oferece suporte para](~/cross-platform/troubleshooting/questions/xamarin-jenkins.md).**


# <a name="summary"></a>Resumo

Este documento introduziu o conceito de integração contínua e as vantagens oferece para as equipes de desenvolvimento de software. A importância de controle de versão foi discutida juntamente com as funções e responsabilidades do servidor de compilação. O documento entrou em discutir algumas das ferramentas que podem ser usado para controle do código fonte e servidores de compilação. Também apresentamos aplicativo Centro de teste, que ajuda os desenvolvedores a publicar ótimos aplicativos executando testes automatizados que comprove a qualidade e a funcionalidade dos seus aplicativos. Documentação sobre como enviar a aplicativos e testes para centro de aplicativo podem ser encontrados mais detalhada [aqui](https://docs.microsoft.com/en-us/appcenter/test-cloud). Por fim, para ajudar a entender como todos esses componentes e ferramentas se encaixam, que descrevemos vários ambientes diferentes de CI que as organizações podem estabelecer para integração contínua. Para obter mais informações usando o Visual Studio Team Services e o Team Foundation Server com Xamarin projetos, consulte [configurar TFVC](https://docs.microsoft.com/vsts/tfvc/overview) e [introdução de integração contínua](https://docs.microsoft.com/en-us/vsts/build-release/actions/ci-cd-part-1). Da mesma forma, se você estiver usando Jenkins, consulte [Jenkins usando com o Xamarin](~/tools/ci/jenkins-walkthrough.md) para obter informações detalhadas sobre como configurar a integração contínua.