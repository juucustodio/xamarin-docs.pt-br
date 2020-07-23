---
title: Microsserviços em contêineres
description: Este capítulo explica como usar os microserviços e contêineres para criar aplicativos de nuvem modernos ágeis, escalonáveis e confiáveis.
ms.prod: xamarin
ms.assetid: 5872ad92-04e0-4f1a-9691-79d5602f5683
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 3f85c6528a1bf599c38a39b4e88400bc8b0c4f05
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/22/2020
ms.locfileid: "86931983"
---
# <a name="containerized-microservices"></a>Microsserviços em contêineres

O desenvolvimento de aplicativos cliente-servidor resultou em um foco na criação de aplicativos em camadas que usam tecnologias específicas em cada camada. Esses aplicativos costumam ser chamados de aplicativos *monolíticos* e empacotados em hardware previamente dimensionado para picos de carga. As principais desvantagens dessa abordagem de desenvolvimento são o acoplamento rígido entre os componentes de cada camada, que os componentes individuais não podem ser dimensionados com facilidade e o custo do teste. Uma atualização simples pode ter efeitos imprevistos no restante da camada e, portanto, uma alteração em um componente de aplicativo requer que sua camada inteira seja testada novamente e reimplantada.

Especialmente em relação à idade da nuvem, é que os componentes individuais não podem ser facilmente dimensionados. Um aplicativo monolítico contém funcionalidade específica de domínio e normalmente é dividido por camadas funcionais, como front-end, lógica de negócios e armazenamento de dados. Um aplicativo monolítico é dimensionado com a clonagem de todo o aplicativo em vários computadores, como ilustrado na Figura 8-1.

![Abordagem de dimensionamento de aplicativos monolítico](containerized-microservices-images/monolithicapp.png)

**Figura 8-1**: abordagem de dimensionamento de aplicativos monolítico

## <a name="microservices"></a>Microsserviços

Os microserviços oferecem uma abordagem diferente para desenvolvimento e implantação de aplicativos, uma abordagem adequada para os requisitos de agilidade, escala e confiabilidade dos aplicativos de nuvem modernos. Um aplicativo de microserviços é decomposto em componentes independentes que trabalham juntos para fornecer a funcionalidade geral do aplicativo. O termo Microservice enfatiza que os aplicativos devem ser compostos de serviços pequenos o suficiente para refletir as preocupações independentes, para que cada microserviço implemente uma única função. Além disso, cada microserviço tem contratos bem definidos para que outros microserviços possam se comunicar e compartilhar dados com ele. Exemplos típicos de microserviços incluem carrinhos de compras, processamento de estoque, subsistemas de compra e processamento de pagamentos.

Os microserviços podem escalar horizontalmente de forma independente, em comparação com aplicativos monolíticos gigantes que são dimensionados juntos. Isso significa que uma área funcional específica, que exige mais capacidade de processamento ou largura de banda de rede para dar suporte à demanda, pode ser dimensionada em vez de reduzir desnecessariamente outras áreas do aplicativo. A Figura 8-2 ilustra essa abordagem, em que os microserviços são implantados e dimensionados de forma independente, criando instâncias de serviços entre máquinas.

![Abordagem de dimensionamento de aplicativos de microserviços](containerized-microservices-images/microservicesapp.png)

**Figura 8-2**: abordagem de dimensionamento do aplicativo de microserviços

A expansão do microserviço pode ser quase instantânea, permitindo que um aplicativo se adapte às cargas em constante mudança. Por exemplo, um único microserviço na funcionalidade voltada para a Web de um aplicativo pode ser o único microserviço no aplicativo que precisa ser expandido para lidar com o tráfego de entrada adicional.

O modelo clássico para a escalabilidade do aplicativo é ter uma camada com balanceamento de carga e sem estado com um repositório de dados externo compartilhado para armazenar os dados persistentes. Os microserviços com estado gerenciam seus próprios dados persistentes, geralmente armazenando-os localmente nos servidores nos quais eles são colocados, para evitar a sobrecarga de acesso à rede e a complexidade de operações entre serviços. Isso permite o processamento mais rápido possível de dados e pode eliminar a necessidade de sistemas de cache. Além disso, os microserviços com estado escalonável geralmente particionam dados entre suas instâncias, para gerenciar o tamanho dos dados e transferir a taxa de transferência além da qual um único servidor pode dar suporte.

Os microserviços também oferecem suporte a atualizações independentes. Esse acoplamento flexível entre os microserviços oferece uma evolução de aplicativo rápida e confiável. Sua natureza independente, distribuída, dá suporte a atualizações sem interrupção, em que apenas um subconjunto de instâncias de um único microserviço será atualizado em um determinado momento. Portanto, se um problema for detectado, uma atualização de bugs poderá ser revertida, antes que todas as instâncias sejam atualizadas com o código ou a configuração com falha. Da mesma forma, os microserviços normalmente usam o controle de versão de esquema, para que os clientes vejam uma versão consistente quando as atualizações estão sendo aplicadas, independentemente de qual instância de microserviço está sendo comunicada.

Portanto, os aplicativos de microatendimento têm muitos benefícios em relação aos aplicativos monolíticos:

- Cada microserviço é relativamente pequeno, fácil de gerenciar e evoluir.
- Cada microserviço pode ser desenvolvido e implantado independentemente de outros serviços.
- Cada microserviço pode ser escalado horizontalmente de forma independente. Por exemplo, um serviço de catálogo ou serviço de cesta de compras pode precisar ser expandido mais do que um serviço de pedidos. Portanto, a infraestrutura resultante consumirá com mais eficiência os recursos ao escalar horizontalmente.
- Cada microserviço isola todos os problemas. Por exemplo, se houver um problema em um serviço, ele afetará apenas esse serviço. Os outros serviços podem continuar a lidar com solicitações.
- Cada Microservice pode usar as tecnologias mais recentes. Como os microserviços são autônomos e executados lado a lado, as tecnologias e as estruturas mais recentes podem ser usadas, em vez de serem forçadas a usar uma estrutura mais antiga que pode ser usada por um aplicativo monolítico.

No entanto, uma solução baseada em microatendimento também tem possíveis desvantagens:

- Escolher como particionar um aplicativo em microserviços pode ser desafiador, pois cada microserviço precisa ser completamente autônomo, de ponta a ponta, incluindo a responsabilidade por suas fontes de dados.
- Os desenvolvedores devem implementar a comunicação entre serviços, o que adiciona complexidade e latência ao aplicativo.
- Transações atômicas entre vários microserviço geralmente não são possíveis. Portanto, os requisitos de negócios devem adotar a consistência eventual entre os microserviços.
- Em produção, há uma complexidade operacional na implantação e gerenciamento de um sistema comprometido de muitos serviços independentes.
- A comunicação direta entre o cliente e o microserviço pode dificultar a refatoração dos contratos de microserviços. Por exemplo, ao longo do tempo como o sistema é particionado em serviços talvez precisem ser alterados. Um único serviço pode ser dividido em dois ou mais serviços, e dois serviços podem ser mesclados. Quando os clientes se comunicam diretamente com os microserviços, esse trabalho de refatoração pode interromper a compatibilidade com os aplicativos cliente.

## <a name="containerization"></a>Transporte em contêineres

A Containerização é uma abordagem para o desenvolvimento de software no qual um aplicativo e seu conjunto de dependências com controle de versão, além de sua configuração de ambiente abstraido como arquivos de manifesto de implantação, são empacotados como uma imagem de contêiner, testado como uma unidade e implantados em um sistema operacional do host.

Um contêiner é um ambiente operacional isolado, controlado por recursos e portátil, onde um aplicativo pode ser executado sem tocar nos recursos de outros contêineres ou no host. Portanto, um contêiner parece e age como um computador físico ou uma máquina virtual recentemente instalada.

Há muitas semelhanças entre contêineres e máquinas virtuais, como ilustrado na Figura 8-3.

![Abordagem de dimensionamento de aplicativos de microserviços](containerized-microservices-images/containersvsvirtualmachines.png)

**Figura 8-3**: comparação de máquinas virtuais e contêineres

Um contêiner executa um sistema operacional, tem um sistema de arquivos e pode ser acessado em uma rede como se fosse uma máquina virtual ou física. No entanto, a tecnologia e os conceitos usados por contêineres são muito diferentes das máquinas virtuais. As máquinas virtuais incluem os aplicativos, as dependências necessárias e um sistema operacional convidado completo. Os contêineres incluem o aplicativo e suas dependências, mas compartilham o sistema operacional com outros contêineres, executando como processos isolados no sistema operacional do host (além de contêineres do Hyper-V que são executados dentro de uma máquina virtual especial por contêiner). Portanto, os contêineres compartilham recursos e normalmente exigem menos recursos do que as máquinas virtuais.

A vantagem de uma abordagem de implantação e desenvolvimento orientada a contêiner é que ela elimina a maioria dos problemas que surgem de configurações de ambiente inconsistentes e os problemas que os acompanham. Além disso, os contêineres permitem a funcionalidade de expansão rápida do aplicativo por meio da instanciação de novos contêineres, conforme necessário.

Os principais conceitos ao criar e trabalhar com contêineres são:

- Host do contêiner: a máquina virtual ou física configurada para hospedar contêineres. O host do contêiner executará um ou mais contêineres.
- Imagem de contêiner: uma imagem consiste em uma União de sistemas de fileem camadas empilhados um em cima do outro e é a base de um contêiner. Uma imagem não tem estado e nunca muda conforme é implantada em ambientes diferentes.
- Contêiner: um contêiner é uma instância de tempo de execução de uma imagem.
- Imagem do sistema operacional do contêiner: contêineres são implantados a partir de imagens. A imagem do sistema operacional do contêiner é a primeira camada em potencialmente muitas camadas de imagem que compõem um contêiner. Um sistema operacional de contêiner é imutável e não pode ser modificado.
- Repositório de contêiner: cada vez que uma imagem de contêiner é criada, a imagem e suas dependências são armazenadas em um repositório local. Essas imagens podem ser reutilizadas várias vezes no host do contêiner. As imagens de contêiner também podem ser armazenadas em um registro público ou privado, como o [Hub do Docker](https://hub.docker.com/), para que possam ser usadas em diferentes hosts de contêiner.

As empresas estão cada vez mais adotando contêineres ao implementar aplicativos baseados em microserviço, e o Docker se tornou a implementação de contêiner padrão que foi adotada pela maioria das plataformas de software e fornecedores de nuvem.

O aplicativo de referência eShopOnContainers usa o Docker para hospedar quatro microserviços de back-end em contêineres, como ilustrado na Figura 8-4.

![eShopOnContainers fazer referência a microserviços de back-end do aplicativo](containerized-microservices-images/microservicesarchitecture.png)

**Figura 8-4**: eShopOnContainers de aplicativos de referência de back-end do aplicativo

A arquitetura dos serviços de back-end no aplicativo de referência é decomposta em vários subsistemas autônomos na forma de colaboração de microserviços e contêineres. Cada microserviço fornece uma única área de funcionalidade: um serviço de identidade, um serviço de catálogo, um serviço de pedidos e um serviço de cesta.

Cada Microservice tem seu próprio banco de dados, permitindo que ele seja totalmente dissociado dos outros microservices. Quando necessário, a consistência entre os bancos de dados de diferentes microservices é obtida usando eventos de nível de aplicativo. Para obter mais informações, consulte [comunicação entre os microserviços](#communication-between-microservices).

Para obter mais informações sobre o aplicativo de referência, consulte [microservices do .net: arquitetura para aplicativos .net em contêineres](https://aka.ms/microservicesebook).

## <a name="communication-between-client-and-microservices"></a>Comunicação entre o cliente e os microserviços

O aplicativo móvel eShopOnContainers comunica-se com os microserviços de back-end em contêineres usando a comunicação *direta de cliente para microserviço* , que é mostrada na Figura 8-5.

![Abordagem de dimensionamento de aplicativos de microserviços](containerized-microservices-images/directclienttomicroservicecommunication.png)

**Figura 8-5**: comunicação direta de cliente para microserviço

Com a comunicação direta entre cliente e microserviço, o aplicativo móvel faz solicitações para cada microserviço diretamente por meio de seu ponto de extremidade público, com uma porta TCP diferente por microserviço. Em produção, o ponto de extremidade normalmente se mapearia para o balanceador de carga do microserviço, que distribui solicitações entre as instâncias disponíveis.

> [!TIP]
> Considere o uso da comunicação do gateway de API. A comunicação direta entre o cliente e o microserviço pode ter desvantagens ao criar um aplicativo baseado em microatendimento grande e complexo, mas é mais do que adequado para um pequeno aplicativo. Ao criar um grande aplicativo baseado em microserviço com dezenas de microserviços, considere o uso da comunicação do gateway de API. Para obter mais informações, consulte [microservices do .net: arquitetura para aplicativos .net em contêineres](https://aka.ms/microservicesebook).

## <a name="communication-between-microservices"></a>Comunicação entre os microserviços

Um aplicativo baseado em microserviços é um sistema distribuído, potencialmente em execução em vários computadores. Cada instância de serviço geralmente é um processo. Portanto, os serviços devem interagir usando um protocolo de comunicação entre processos, como HTTP, TCP, Advanced Message Queuing Protocol (AMQP) ou protocolos binários, dependendo da natureza de cada serviço.

As duas abordagens comuns para a comunicação de microatendimento para microserviço são a comunicação REST baseada em HTTP ao consultar dados e uma mensagem assíncrona leve ao comunicar atualizações em vários microserviços.

A comunicação assíncrona baseada em eventos com base em mensagens é essencial ao propagar alterações em vários microserviços. Com essa abordagem, um microserviço publica um evento quando algo notável acontece, por exemplo, quando ele atualiza uma entidade de negócios. Outros microserviços assinam esses eventos. Em seguida, quando um microserviço recebe um evento, ele atualiza suas próprias entidades comerciais, o que pode, por sua vez, levar a uma publicação de mais eventos. Normalmente, essa funcionalidade de publicação e assinatura é obtida com um barramento de evento.

Um barramento de evento permite a comunicação de publicação/assinatura entre os microserviços, sem exigir que os componentes sejam explicitamente cientes uns dos outros, como mostra a Figura 8-6.

![Publicar-assinar com um barramento de evento](containerized-microservices-images/eventbus.png)

**Figura 8-6:** Publicar-assinar com um barramento de evento

Da perspectiva do aplicativo, o barramento de evento é simplesmente um canal de publicação-assinatura exposto por meio de uma interface. No entanto, a maneira como o barramento de evento é implementado pode variar. Por exemplo, uma implementação de barramento de evento poderia usar o RabbitMQ, o barramento de serviço do Azure ou outros barramentos de serviço, como NServiceBus e MassTransit. A Figura 8-7 mostra como um barramento de evento é usado no aplicativo de referência eShopOnContainers.

![Comunicação assíncrona controlada por evento no aplicativo de referência](containerized-microservices-images/microservicesarchitecturewitheventbus.png)

**Figura 8-7:** Comunicação assíncrona controlada por evento no aplicativo de referência

O barramento de evento eShopOnContainers, implementado usando o RabbitMQ, fornece a funcionalidade de publicação/assinatura assíncrona de um para muitos. Isso significa que depois de publicar um evento, pode haver vários assinantes ouvindo o mesmo evento. A Figura 8-9 ilustra essa relação.

![Comunicação um-para-muitos](containerized-microservices-images/eventdrivencommunication.png)

**Figura 8-9**: comunicação um-para-muitos

Essa abordagem de comunicação de um para muitos usa eventos para implementar transações de negócios que abrangem vários serviços, garantindo a consistência eventual entre os serviços. Uma transação de eventualidade consiste em uma série de etapas distribuídas. Portanto, quando o microserviço do perfil de usuário recebe o comando UpdateUser, ele atualiza os detalhes do usuário em seu banco de dados e publica o evento userupdate no barramento de evento. O microserviço da cesta e o microserviço de pedidos se inscreveram para receber esse evento e, em resposta, atualize suas informações de comprador em seus respectivos bancos de dados.

> [!NOTE]
> O barramento de evento eShopOnContainers, implementado usando RabbitMQ, destina-se a ser usado apenas como uma prova de conceito. Para sistemas de produção, as implementações alternativas de barramento de evento devem ser consideradas.

Para obter informações sobre a implementação do barramento de evento, consulte [microservices do .net: arquitetura para aplicativos .net em contêineres](https://aka.ms/microservicesebook).

## <a name="summary"></a>Resumo

Os microserviços oferecem uma abordagem ao desenvolvimento e à implantação de aplicativos adequados para os requisitos de agilidade, escala e confiabilidade dos aplicativos de nuvem modernos. Uma das principais vantagens dos microserviços é que eles podem ser expandidos de forma independente, o que significa que uma área funcional específica pode ser dimensionada para exigir mais capacidade de processamento ou largura de banda de rede para dar suporte à demanda, sem áreas de dimensionamento desnecessariamente do aplicativo que não estão sofrendo maior demanda.

Um contêiner é um ambiente operacional isolado, controlado por recursos e portátil, onde um aplicativo pode ser executado sem tocar nos recursos de outros contêineres ou no host. As empresas estão cada vez mais adotando contêineres ao implementar aplicativos baseados em microserviço, e o Docker se tornou a implementação de contêiner padrão que foi adotada pela maioria das plataformas de software e fornecedores de nuvem.

## <a name="related-links"></a>Links Relacionados

- [Download do eBook (PDF de 2Mb)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) (exemplo)](https://github.com/dotnet-architecture/eShopOnContainers)
