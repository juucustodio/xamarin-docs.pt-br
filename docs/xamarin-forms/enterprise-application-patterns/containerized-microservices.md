---
title: "Microservices em contêineres"
ms.topic: article
ms.prod: xamarin
ms.assetid: 5872ad92-04e0-4f1a-9691-79d5602f5683
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
ms.openlocfilehash: 3ecbd5a301a64417ab5fb27bd8632b6d9790a7ac
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/28/2018
---
# <a name="containerized-microservices"></a>Microservices em contêineres

Desenvolvimento de aplicativos de cliente-servidor resultou em um foco na criação de aplicativos em camadas que usam tecnologias específicas em cada camada. Esses aplicativos são referidos como *monolítico* aplicativos e são empacotados em um hardware previamente dimensionado para cargas de pico. As principais desvantagens dessa abordagem de desenvolvimento são a ligação forte entre componentes em cada nível, que componentes individuais não podem ser facilmente dimensionadas e o custo de testar. Uma atualização simple pode ter efeitos imprevisíveis no restante da camada, e, portanto, uma alteração em um componente de aplicativo requer sua camada inteira a ser testado novamente e reimplantado.

Particularmente respeito na idade da nuvem, é que os componentes individuais não podem ser facilmente dimensionada. Um aplicativo monolítico contém funcionalidade específica de domínio e normalmente é dividido por camadas funcionais como front-end, a lógica de negócios e o armazenamento de dados. Um aplicativo monolítico é dimensionado por meio da clonagem todo o aplicativo em vários computadores, conforme ilustrado na Figura 8-1.

![](containerized-microservices-images/monolithicapp.png "Abordagem de dimensionamento do aplicativo monolítico")

**Figura 8-1**: aplicativo monolítico abordagem de dimensionamento

## <a name="microservices"></a>Microservices

Microservices oferecem uma abordagem diferente para o desenvolvimento de aplicativo e implantação, uma abordagem é adequada para a agilidade, escala e requisitos de confiabilidade dos aplicativos de nuvem modernos. Um aplicativo microservices é decomposto em componentes independentes que trabalham juntos para fornecer funcionalidade geral do aplicativo. O termo microsserviço enfatiza que aplicativos devem ser compostos de serviços pequenos o suficiente para refletir as preocupações independentes, para que cada microsserviço implementa uma única função. Além disso, cada microsserviço tem contratos bem definidos para que outros microservices podem se comunicar e compartilhar dados com ele. Exemplos típicos de microservices incluem carrinhos de compra, processamento de inventário, subsistemas e processamento de pagamento de compra.

Microservices pode expansão de forma independente, em comparação com gigantes aplicativos monolíticos escalados juntos. Isso significa que uma área funcional específica, que requer mais processamento de energia ou rede largura de banda para dar suporte à demanda, pode ser aumentada em vez de desnecessariamente expansão outras áreas do aplicativo. Figura 8-2 mostra essa abordagem, onde microservices são implantados e dimensionadas de forma independente, criar instâncias de serviços em computadores.

![](containerized-microservices-images/microservicesapp.png "Abordagem de dimensionamento de aplicativo Microservices")

**Figura 8-2**: aplicativo Microservices abordagem de dimensionamento

Expansão de Microsserviço pode ser quase imediatos, permitindo que um aplicativo para se adaptar a alterações de cargas. Por exemplo, um único microsserviço na funcionalidade de um aplicativo voltado para a web pode ser o microsserviço somente no aplicativo que precisa ser expandidos para lidar com o tráfego de entrada adicional.

O modelo clássico para escalabilidade do aplicativo é ter uma camada com balanceamento de carga, sem monitoração de estado com um repositório de dados externo compartilhado para armazenar dados persistentes. Microservices com monitoração de estado gerenciar seus próprios dados persistentes, geralmente fazê-lo localmente nos servidores nos quais eles são colocados, para evitar a sobrecarga de rede de acesso e a complexidade de vários serviços operações. Isso permite o processamento mais rápido possível de dados e pode eliminar a necessidade de cache de sistemas. Além disso, escalonáveis microservices com monitoração de estado geralmente particionar dados entre suas instâncias, para gerenciar a transferência de tamanho e a transferência de dados além do qual um único servidor pode dar suporte.

Microservices também dão suporte a atualizações independentes. Esse acoplamento entre microservices fornece uma evolução do aplicativo rápida e confiável. Sua natureza distribuída, independente dá suporte a atualizações sem interrupção, em que apenas um subconjunto de instâncias de um único microsserviço atualizará a qualquer momento. Portanto, se um problema for detectado, uma atualização com bugs pode ser revertida, antes de atualizar todas as instâncias com o código com defeito ou a configuração. Da mesma forma, microservices normalmente usam controle de versão de esquema, para que os clientes verão uma versão consistente quando as atualizações são aplicadas, independentemente de qual microsserviço instância está sendo comunicada com.

Portanto, os aplicativos de microsserviço têm muitos benefícios aos aplicativos monolíticos:

-   Cada microsserviço é relativamente pequeno, fácil de gerenciar e desenvolver.
-   Cada microsserviço pode ser desenvolvido e implantado independentemente de outros serviços.
-   Cada microsserviço pode ser expandido independentemente. Por exemplo, um serviço de catálogo ou o serviço de carrinho de compras talvez precise ser expandido mais de um serviço de classificação. Portanto, a infraestrutura resultante com mais eficiência consumirá recursos ao expandir.
-   Cada microsserviço isola os problemas. Por exemplo, se houver um problema em um serviço ele afeta somente se o serviço. Outros serviços podem continuar a tratar as solicitações.
-   Cada microsserviço pode usar as tecnologias mais recentes. Como microservices são autônomo e execute-lado a lado, as últimas tecnologias e estruturas podem ser usadas, em vez de ser forçado a usar uma estrutura mais antiga que pode ser usada por um aplicativo monolítico.

No entanto, uma solução de microsserviço com base também tem possíveis desvantagens:

-   Escolher como um aplicativo em microservices de partição pode ser um desafio, pois cada microsserviço tem que ser completamente autônoma, de ponta a ponta, incluindo a responsabilidade de suas fontes de dados.
-   Os desenvolvedores devem implementar a comunicação entre serviços, o que adiciona complexidade e latência para o aplicativo.
-   Transações atômicas entre vários microservices geralmente não são possíveis. Portanto, os requisitos de negócios devem adotar consistência eventual entre microservices.
-   Em produção, há uma complexidade operacional na implantação e gerenciamento de um sistema comprometido de muitos serviços independentes.
-   Comunicação direta do cliente para microsserviço pode dificultar refatorar os contratos de microservices. Por exemplo, ao longo do tempo como o sistema está particionado em serviços talvez seja necessário alterar. Um único serviço pode ser dividido em dois ou mais serviços e mesclagem dois serviços. Quando os clientes se comunicam diretamente com microservices, esse trabalho refatoração pode quebrar a compatibilidade com aplicativos cliente.

## <a name="containerization"></a>Transporte em contêineres

Enormemente é uma abordagem para desenvolvimento de software no qual um aplicativo e seu conjunto com controle de versão de dependências, além de sua configuração de ambiente abstraídos como arquivos de manifesto de implantação são reunidos como uma imagem de contêiner, testada como uma unidade e implantado em um sistema operacional do host.

Um contêiner é um recurso portátil e controlado ambiente operacional isolado, onde um aplicativo pode ser executado sem tocar os recursos de outros contêineres, ou o host. Portanto, um contêiner de pesquisa e atua como uma máquina virtual ou de um computador físico recém-instalado.

Há muitas semelhanças entre contêineres e máquinas virtuais, conforme ilustrado na Figura 8-3.

![](containerized-microservices-images/containersvsvirtualmachines.png "Abordagem de dimensionamento de aplicativo Microservices")

**Figura 8-3**: comparação de máquinas virtuais e contêineres

Um contêiner executa um sistema operacional, tem um sistema de arquivos e pode ser acessado através de uma rede como se fosse um computador físico ou virtual. No entanto, a tecnologia e os conceitos usados pelos contêineres são muito diferentes das máquinas virtuais. Máquinas virtuais incluem os aplicativos, as dependências necessárias e um sistema operacional convidado completo. Contêineres incluem o aplicativo e suas dependências, mas compartilham o sistema operacional com outros contêineres em execução como processos isolados no sistema operacional do host (além de contêineres do Hyper-V que são executados dentro de uma máquina virtual especial por contêiner). Portanto, os contêineres compartilham recursos e geralmente exigem menos recursos do que as máquinas virtuais.

A vantagem de uma abordagem de desenvolvimento e implantação orientada por contêiner é o que elimina a maioria dos problemas que surgem das configurações de ambiente inconsistente e os problemas que vêm com eles. Além disso, contêineres de permitem a funcionalidade do aplicativo de rápida expansão por instância novos contêineres conforme necessário.

Os principais conceitos ao criar e trabalhar com contêineres são:

-   Host do contêiner: Físico ou máquina virtual configurada para contêineres do host. O host do contêiner executará um ou mais contêineres.
-   Imagem de contêiner: Uma imagem consiste em uma união dos sistemas de arquivos em camadas empilhados uns sobre os outros e é a base de um contêiner. Uma imagem não tem estado e nunca é alterado conforme ele é implantado em ambientes diferentes.
-   Contêiner: Um contêiner é uma instância de tempo de execução de uma imagem.
-   Imagem do sistema operacional do contêiner: Contêineres são implantados de imagens. A imagem de sistema operacional do contêiner é a primeira potencialmente de muitas camadas de imagem que compõem um contêiner. Um sistema operacional do contêiner é imutável e não pode ser modificado.
-   Repositório de contêiner: Cada vez que uma imagem de contêiner é criada, a imagem e suas dependências são armazenadas em um repositório local. Essas imagens podem ser reutilizadas várias vezes no host do contêiner. As imagens de contêiner também podem ser armazenadas em um registro público ou privado como [Hub do Docker](https://hub.docker.com/), de modo que eles podem ser usados em hosts de contêiner diferentes.

As empresas cada vez mais estão adotando contêineres quando implementar microsserviço com base em aplicativos e Docker tornou-se a implementação do contêiner padrão adotada pela maioria das plataformas de software e fornecedores de nuvem.

O aplicativo de referência eShopOnContainers usa Docker para hospedar quatro microservices em contêineres de back-end, conforme ilustrado na Figura 8-4.

![](containerized-microservices-images/microservicesarchitecture.png "referência de eShopOnContainers microservices de back-end do aplicativo")

**Figura 8-4**: eShopOnContainers Referência microservices de back-end do aplicativo

A arquitetura dos serviços de back-end do aplicativo de referência é decomposta em vários subsistemas autônomos na forma de colaboração microservices e contêineres. Cada microsserviço fornece uma única área de funcionalidade: um serviço de identidade, um serviço de catálogo, um serviço de pedido e um serviço da cesta.

Cada microsserviço tem seu próprio banco de dados, permitindo que ele seja totalmente dissociado de outros microservices. Quando necessário, consistência entre bancos de dados de diferentes microservices é obtida usando eventos do nível do aplicativo. Para obter mais informações, consulte [comunicação entre Microservices](#communication_between_microservices).

Para obter mais informações sobre o aplicativo de referência, consulte [Microservices .NET: arquitetura de aplicativos do .NET em contêineres](https://aka.ms/microservicesebook).

<a name="communication_between_client_and_microservices" />

## <a name="communication-between-client-and-microservices"></a>Comunicação entre cliente e Microservices

O aplicativo móvel eShopOnContainers se comunica com o microservices em contêineres de back-end usando *direcionar clientes para microsserviço* comunicação, o que é mostrada na Figura 8-5.

![](containerized-microservices-images/directclienttomicroservicecommunication.png "Abordagem de dimensionamento de aplicativo Microservices")

**Figura 8-5**: direcionar comunicação cliente-microsserviço

Com a comunicação de cliente para microsserviço direta, o aplicativo móvel faz solicitações para cada microsserviço diretamente por meio de seu ponto de extremidade público com uma porta TCP diferente por microsserviço. Em produção, o ponto de extremidade normalmente seria mapeados para o balanceador de carga do microsserviço que distribui solicitações entre as instâncias disponíveis.

> [!TIP]
> Considere o uso de comunicação de gateway de API. Comunicação direta do cliente para microsserviço pode ter desvantagens quando criar um microsserviço grande e complexo com base no aplicativo, mas é mais do que adequado para um aplicativo pequeno. Quando criar um microsserviço grande aplicativo baseado em com dezenas de microservices, considere o uso de comunicação de gateway de API. Para obter mais informações, consulte [Microservices .NET: arquitetura de aplicativos do .NET em contêineres](https://aka.ms/microservicesebook).

<a name="communication_between_microservices" />

## <a name="communication-between-microservices"></a>Comunicação entre Microservices

Um aplicativo de microservices com base é um sistema distribuído, potencialmente em execução em vários computadores. Cada instância de serviço geralmente é um processo. Portanto, os serviços devem interagir usando um protocolo de comunicação entre processos, como HTTP, TCP, Advanced Message Queuing Protocol (AMQP) ou protocolos binários, dependendo da natureza de cada serviço.

As duas abordagens comuns para comunicação de microsserviço para microsserviço são baseado em HTTP comunicação REST ao consultar dados e mensagens assíncronas leve, ao se comunicar atualizações em vários microservices.

Comunicação assíncrona de mensagens baseada em controlada por evento é essencial ao propagar alterações em várias microservices. Com essa abordagem, um microsserviço publica um evento quando algo notável ocorre, por exemplo, quando atualiza uma entidade de negócios. Outros microservices assinar esses eventos. Em seguida, quando um microsserviço recebe um evento, ele atualiza suas próprias entidades de negócios, que por sua vez podem levar mais eventos que está sendo publicado. Essa publicação / assinatura funcionalidade geralmente é feito com um barramento de evento.

Um barramento de eventos permite a comunicação entre microservices, sem exigir que os componentes sejam explicitamente conhecimento uns dos outros, conforme mostrado na Figura 8-6 de publicação / assinatura.

![](containerized-microservices-images/eventbus.png "Publicação / assinatura com um barramento de evento")

**Figura 8-6:** publicação-assinatura com um barramento de evento

Da perspectiva do aplicativo, o barramento de evento é simplesmente uma publicação-assinatura exposto por meio de uma interface de canal. No entanto, a barramento de evento é implementado de maneira pode variar. Por exemplo, uma implementação do barramento de evento pode usar RabbitMQ, barramento de serviço do Azure ou outros barramentos de serviço como NServiceBus e MassTransit. Figura 8-7 mostra como um barramento de evento é usado no aplicativo eShopOnContainers referência.

![](containerized-microservices-images/microservicesarchitecturewitheventbus.png "Comunicação assíncrona orientada a eventos do aplicativo de referência")

**Figura 8-7:** comunicação assíncrona orientada a eventos do aplicativo de referência

O barramento de evento eShopOnContainers, implementado usando RabbitMQ, fornece um-para-muitos assíncrono de publicação / assinatura funcionalidade. Isso significa que depois de publicar um evento, pode haver vários assinantes escuta para o mesmo evento. Figura 8 e 9 ilustra essa relação.

![](containerized-microservices-images/eventdrivencommunication.png "Comunicação de um-para-muitos")

**Figura 8 e 9**: um-para-muitos comunicação

Essa abordagem de comunicação um-para-muitos usa eventos para implementar transações comerciais que abrangem vários serviços, garantindo a consistência eventual entre os serviços. Uma transação eventual consistente consiste em uma série de etapas distribuídas. Portanto, quando o perfil de usuário microsserviço recebe o comando UpdateUser, ele atualiza os detalhes do usuário no banco de dados e publica o evento UserUpdated do barramento de evento. O microsserviço carrinho e o ordenação microsserviço se inscreveu para receber este evento e, em resposta atualizar suas informações de compra em seus respectivos bancos de dados.

> [!NOTE]
> O barramento de evento eShopOnContainers, implementado usando RabbitMQ, destina-se a ser usado apenas como uma prova de conceito. Para sistemas de produção, as implementações de barramento de evento alternativo devem ser consideradas.

Para obter informações sobre a implementação do barramento de evento, consulte [Microservices .NET: arquitetura de aplicativos do .NET em contêineres](https://aka.ms/microservicesebook).

## <a name="summary"></a>Resumo

Microservices oferecem uma abordagem de desenvolvimento de aplicativo e implantação é adequado para os requisitos de agilidade, a escala e a confiabilidade dos aplicativos de nuvem modernos. Uma das principais vantagens de microservices é que elas podem ser expandidas independentemente, o que significa que uma área funcional específica pode ser dimensionada que que requer mais processamento de energia ou rede largura de banda para dar suporte a demanda, sem escala desnecessariamente áreas do o aplicativo que não estão enfrentando o aumento da demanda.

Um contêiner é um recurso portátil e controlado ambiente operacional isolado, onde um aplicativo pode ser executado sem tocar os recursos de outros contêineres, ou o host. As empresas cada vez mais estão adotando contêineres quando implementar microsserviço com base em aplicativos e Docker tornou-se a implementação do contêiner padrão adotada pela maioria das plataformas de software e fornecedores de nuvem.


## <a name="related-links"></a>Links relacionados

- [Baixar eBook (2Mb PDF)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) (sample)](https://github.com/dotnet-architecture/eShopOnContainers)
