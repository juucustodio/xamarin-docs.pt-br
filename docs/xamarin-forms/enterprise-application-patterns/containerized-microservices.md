---
title: Microsserviços em contêineres
description: Este capítulo explica como usar microsserviços e contêineres para compilar ágil, escalável e confiável modernos aplicativos em nuvem.
ms.prod: xamarin
ms.assetid: 5872ad92-04e0-4f1a-9691-79d5602f5683
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
ms.openlocfilehash: 33be84bc17f72c8b70d117a0742b001f1f763d3d
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61300678"
---
# <a name="containerized-microservices"></a>Microsserviços em contêineres

Desenvolvimento de aplicativos de cliente-servidor resultou em um foco na criação de aplicativos em camadas que usam tecnologias específicas em cada camada. Esses aplicativos são geralmente denominados *monolítico* aplicativos e são empacotados em um hardware dimensionado previamente para cargas de pico. As principais desvantagens dessa abordagem de desenvolvimento são o acoplamento rígido entre componentes em cada camada, que os componentes individuais não podem ser dimensionados com facilidade e o custo de testar. Uma atualização simples pode ter efeitos imprevisíveis no restante da camada, e, portanto, uma alteração em um componente de aplicativo requer sua camada inteira seja testado novamente e reimplantado.

Especialmente a respeito na era da nuvem, é que os componentes individuais não podem ser facilmente dimensionada. Um aplicativo monolítico contém funcionalidade específica do domínio e é normalmente dividido por camadas funcionais como front-end, lógica de negócios e armazenamento de dados. Um aplicativo monolítico é dimensionado por meio da clonagem todo o aplicativo em várias máquinas, conforme ilustrado na Figura 8-1.

![](containerized-microservices-images/monolithicapp.png "Abordagem de dimensionamento de aplicativos monolíticos")

**Figura 8-1**: Abordagem de dimensionamento de aplicativos monolíticos

## <a name="microservices"></a>Microsserviços

Microsserviços oferecem uma abordagem diferente para o desenvolvimento de aplicativo e implantação, uma abordagem é adequada para a agilidade, escala e necessidades de confiabilidade de aplicativos de nuvem modernos. Um aplicativo de microsserviço é decomposto em componentes independentes que trabalham juntos para fornecer a funcionalidade do aplicativo geral. O microsserviço de termo enfatiza que os aplicativos devem ser compostos por serviços pequenos o suficiente para refletir as preocupações independentes, para que cada microsserviço implementa uma única função. Além disso, cada microsserviço tem contratos bem definidos para que outros microsserviços podem se comunicar e compartilhar dados com ele. Exemplos típicos de microsserviços incluem carrinhos de compra, processamento de inventário, subsistemas e processamento de pagamentos de compra.

Microsserviços podem escalar horizontalmente de forma independente, em comparação com os aplicativos monolíticos gigantes que são dimensionados juntos. Isso significa que uma área funcional específica, que requer mais processamento de rede ou energia largura de banda para dar suporte à demanda, pode ser dimensionada em vez de desnecessariamente dimensionar outras áreas do aplicativo. A Figura 8-2 ilustra essa abordagem, onde os microsserviços são implantados e dimensionados de forma independente, criando instâncias de serviços entre máquinas.

![](containerized-microservices-images/microservicesapp.png "Abordagem de dimensionamento de aplicativos de Microsserviços")

**A Figura 8-2**: Abordagem de dimensionamento de aplicativos de Microsserviços

Expansão de Microsserviço pode ser quase instantânea, permitindo que um aplicativo para se adaptar às cargas. Por exemplo, um único microsserviço na funcionalidade de um aplicativo voltado para a web pode ser o microsserviço somente no aplicativo que precisa para escalar horizontalmente para lidar com o tráfego de entrada adicional.

O modelo clássico para escalabilidade do aplicativo é ter uma camada sem monitoração de estado, com balanceamento de carga com um armazenamento de dados compartilhado externo para armazenar dados persistentes. Microsserviços com estado gerenciam seus próprios dados persistentes, normalmente, armazenando-a localmente nos servidores nos quais eles são colocados, para evitar a sobrecarga de rede de acesso e a complexidade de entre serviços operações. Isso permite que o processamento mais rápido possível de dados e pode eliminar a necessidade para o cache de sistemas. Além disso, os microsserviços com estado escalonáveis geralmente particionar dados entre suas instâncias, para gerenciar a taxa de tamanho e a transferência de dados além do qual um único servidor pode dar suporte.

Microsserviços também dão suporte a atualizações independentes. Esse acoplamento flexível entre os microsserviços fornece uma evolução do aplicativo rápida e confiável. Sua natureza distribuída, independente dá suporte a atualizações sem interrupção, em que apenas um subconjunto das instâncias de um único microsserviço atualizará a qualquer momento. Portanto, se um problema for detectado, uma atualização com bugs pode ser revertida, antes de atualizar de todas as instâncias com o código com defeito ou a configuração. Da mesma forma, microsserviços geralmente usam controle de versão do esquema, para que os clientes verão uma versão consistente quando as atualizações estão sendo aplicadas, independentemente de qual microsserviço instância está sendo comunicada com.

Portanto, aplicativos de microsserviço tem muitos benefícios em aplicativos monolíticos:

-   Cada microsserviço é relativamente pequeno, fácil de gerenciar e evoluir.
-   Cada microsserviço pode ser desenvolvido e implantado independentemente de outros serviços.
-   Cada microsserviço pode ser dimensionado independentemente. Por exemplo, um serviço de catálogo ou o serviço do carrinho de compras talvez precise ser expandidos de mais de um serviço de pedidos. Portanto, a infra-estrutura resultante com mais eficiência consumirá recursos ao expandir.
-   Cada microsserviço isola os problemas. Por exemplo, se houver um problema em um serviço ele só afeta o serviço. Outros serviços podem continuar a tratar as solicitações.
-   Cada microsserviço pode usar as tecnologias mais recentes. Como os microsserviços são autônoma e execução lado a lado, as últimas tecnologias e estruturas podem ser usadas, em vez de ser forçado a usar uma estrutura mais antiga que pode ser usada por um aplicativo monolítico.

No entanto, uma solução de microsserviço com base também tem desvantagens:

-   Escolher como particionar um aplicativo em microsserviços pode ser um desafio, pois cada microsserviço deve ser completamente autônomo, de ponta a ponta, incluindo a responsabilidade por suas fontes de dados.
-   Os desenvolvedores devem implementar comunicação entre serviços, o que adiciona complexidade e a latência ao aplicativo.
-   Transações atômicas entre vários microsserviços geralmente não são possíveis. Portanto, os requisitos de negócios devem adotar consistência eventual entre os microsserviços.
-   Em produção, há uma complexidade operacional na implantação e gerenciamento de um sistema comprometido de muitos serviços independentes.
-   Comunicação direta do cliente e microsserviço pode dificultar a refatoração dos contratos de microsserviços. Por exemplo, ao longo do tempo como o sistema está particionado em serviços talvez precise alterar. Um único serviço pode ser dividido em dois ou mais serviços, e dois serviços podem mesclar. Quando os clientes se comunicam diretamente com microsserviços, esse trabalho de refatoração pode interromper a compatibilidade com aplicativos cliente.

## <a name="containerization"></a>Transporte em contêineres

Uso de contêineres é uma abordagem ao desenvolvimento de software em que um aplicativo e seu conjunto de versões de dependências, além de sua configuração de ambiente abstraídos como arquivos de manifesto de implantação são empacotados juntos como uma imagem de contêiner, testada como uma unidade, e implantado em um sistema operacional host.

Um contêiner é um, recurso portátil e controlado por ambiente operacional isolado, em que um aplicativo pode ser executado sem tocar os recursos de outros contêineres ou o host. Portanto, um contêiner se parece e age como uma máquina virtual ou de um computador físico recém-instalado.

Existem muitas semelhanças entre os contêineres e máquinas virtuais, conforme ilustrado na Figura 8-3.

![](containerized-microservices-images/containersvsvirtualmachines.png "Abordagem de dimensionamento de aplicativos de Microsserviços")

**Figura 8-3**: Comparação de máquinas virtuais e contêineres

Um contêiner executa um sistema operacional, tem um sistema de arquivos e pode ser acessado através de uma rede como se fosse uma máquina virtual ou física. No entanto, a tecnologia e os conceitos usados por contêineres são muito diferentes das máquinas virtuais. As máquinas virtuais incluem os aplicativos, as dependências necessárias e um sistema operacional convidado completo. Contêineres incluem o aplicativo e suas dependências, mas compartilham o sistema operacional com outros contêineres em execução como processos isolados no sistema operacional host (com exceção de contêineres do Hyper-V que são executados dentro de uma máquina virtual especial por contêiner). Portanto, os contêineres compartilham recursos e normalmente exigem menos recursos do que as máquinas virtuais.

A vantagem de uma abordagem de desenvolvimento e implantação orientada para o contêiner é que ele elimina a maioria dos problemas que surgem das configurações de ambiente inconsistente e os problemas que vêm com eles. Além disso, a contêineres permitem a funcionalidade de expansão rápida de aplicativos por novos contêineres conforme a necessidade de instanciação.

Os principais conceitos ao criar e trabalhar com contêineres são:

-   Host do contêiner: A máquina física ou virtual configurado para hospedar contêineres. O host do contêiner executará um ou mais contêineres.
-   Imagem de contêiner: Uma imagem consiste em uma união dos sistemas de arquivos em camadas empilhadas umas sobre as outras e é a base de um contêiner. Uma imagem não tem estado, e nunca muda conforme ele é implantado em ambientes diferentes.
-   Contêiner: Um contêiner é uma instância de tempo de execução de uma imagem.
-   Imagem do sistema operacional do contêiner: Contêineres são implantados de imagens. A imagem de sistema operacional do contêiner é a primeira camada potencialmente muitas camadas de imagem que compõem um contêiner. Um sistema de operacional do contêiner é imutável e não pode ser modificado.
-   Repositório de contêiner: Sempre que uma imagem de contêiner é criada, a imagem e suas dependências são armazenadas em um repositório local. Essas imagens podem ser reutilizadas várias vezes no host do contêiner. As imagens de contêiner também podem ser armazenadas em um registro público ou privado, tais como [Hub do Docker](https://hub.docker.com/), de modo que eles podem ser usados em hosts de contêiner diferentes.

As empresas estão adotando cada vez mais contêineres quando o Docker tornou-se a implementação do contêiner padrão que tem sido adotada pela maioria das plataformas de software e fornecedores de nuvem e aplicativos baseados em microsserviço de implementação.

O aplicativo eShopOnContainers de referência usa o Docker para hospedar quatro microsserviços em contêineres de back-end, conforme ilustrado na Figura 8-4.

![](containerized-microservices-images/microservicesarchitecture.png "microsserviços de back-end do aplicativo de referência eShopOnContainers")

**Figura 8-4**: microsserviços de back-end do aplicativo de referência eShopOnContainers

A arquitetura dos serviços de back-end do aplicativo de referência é decomposta em vários subsistemas independentes na forma de colaboração de microsserviços e contêineres. Cada microsserviço fornece uma única área de funcionalidade: um serviço de identidade, um serviço de catálogo, um serviço de pedidos e um serviço de carrinho de compras.

Cada microsserviço tem seu próprio banco de dados, permitindo que ele seja totalmente separado dos outros microsserviços. Onde for necessário, a consistência entre bancos de dados de diferentes microsserviços é obtida usando eventos de nível de aplicativo. Para obter mais informações, consulte [comunicação entre Microsserviços](#communication_between_microservices).

Para obter mais informações sobre o aplicativo de referência, consulte [Microsserviços do .NET: Arquitetura para aplicativos .NET em contêineres](https://aka.ms/microservicesebook).

<a name="communication_between_client_and_microservices" />

## <a name="communication-between-client-and-microservices"></a>Comunicação entre cliente e Microsserviços

O aplicativo móvel do eShopOnContainers se comunica com o uso de microsserviços em contêineres de back-end *direcionar o cliente e microsserviço* comunicação, o que é mostrada na Figura 8-5.

![](containerized-microservices-images/directclienttomicroservicecommunication.png "Abordagem de dimensionamento de aplicativos de Microsserviços")

**Figura 8-5**: Comunicação direta de cliente com microsserviço

Com a comunicação direta de cliente e microsserviço, o aplicativo móvel faz solicitações para cada microsserviço diretamente por meio de seu ponto de extremidade público, com uma porta TCP diferente por microsserviço. Em produção, o ponto de extremidade normalmente seria mapeados para o balanceador de carga do microsserviço, que distribui solicitações entre as instâncias disponíveis.

> [!TIP]
> Considere o uso de comunicações do gateway de API. Comunicação direta do cliente e microsserviço pode ter desvantagens quando a criação de um microsserviço grande e complexo com base no aplicativo, mas é mais do que adequado para um aplicativo pequeno. Quando Projetando um microsserviço grande aplicativo baseado em com dezenas de microsserviços, considere o uso de comunicações do gateway de API. Para obter mais informações, consulte [Microsserviços do .NET: Arquitetura para aplicativos .NET em contêineres](https://aka.ms/microservicesebook).

<a name="communication_between_microservices" />

## <a name="communication-between-microservices"></a>Comunicação entre Microsserviços

Um aplicativo de microsserviços com base é um sistema distribuído, potencialmente em execução em vários computadores. Cada instância de serviço geralmente é um processo. Portanto, os serviços devem interagir usando um protocolo de comunicação entre processos, como HTTP, TCP, AMQP Advanced Message Queuing Protocol () ou protocolos binários, dependendo da natureza de cada serviço.

As duas abordagens comuns para a comunicação de microsserviço para microsserviços são HTTP com base em comunicação do REST ao consultar dados e mensagens assíncronas leves, ao se comunicar atualizações entre vários microsserviços.

Comunicação assíncrona de mensagens com base controlada por evento é essencial ao propagar alterações entre vários microsserviços. Com essa abordagem, um microsserviço publica um evento quando algo notável acontece, por exemplo, quando ele atualiza uma entidade de negócios. Outros microsserviços assinam esses eventos. Em seguida, quando um microsserviço recebe um evento, ele atualiza suas próprias entidades de negócios, que por sua vez podem levar à publicação de mais eventos. Isso de publicação / assinatura funcionalidade geralmente é obtida com um barramento de evento.

Um barramento de eventos permite a comunicação entre os microsserviços, sem exigir que os componentes sejam explicitamente cientes uns dos outros, conforme mostrado na Figura 8-6 de publicação / assinatura.

![](containerized-microservices-images/eventbus.png "Publicação / assinatura com um barramento de evento")

**Figura 8-6:** Publicação / assinatura com um barramento de evento

Da perspectiva do aplicativo, o barramento de evento é simplesmente um publicar-assinar canal exposto por meio de uma interface. No entanto, a maneira que o barramento de eventos é implementado pode variar. Por exemplo, uma implementação de barramento de evento poderia usar RabbitMQ, barramento de serviço do Azure ou outros barramentos de serviço, como NServiceBus e MassTransit. Figura 8-7 mostra como um barramento de evento é usado no aplicativo eShopOnContainers de referência.

![](containerized-microservices-images/microservicesarchitecturewitheventbus.png "Comunicação assíncrona controlada por evento, no aplicativo de referência")

**Figura 8-7:** Comunicação assíncrona controlada por evento, no aplicativo de referência

O barramento de evento de eShopOnContainers, implementado usando RabbitMQ, fornece um-para-muitos assíncrono de publicação / assinatura funcionalidade. Isso significa que depois de publicar um evento, pode haver vários assinantes escuta para o mesmo evento. Figura 8 e 9 ilustra essa relação.

![](containerized-microservices-images/eventdrivencommunication.png "Comunicação de um-para-muitos")

**Figura 8 e 9**: Comunicação de um-para-muitos

Essa abordagem de comunicação um-para-muitos usa eventos para implementar transações comerciais que abranjam vários serviços, garantindo a consistência eventual entre os serviços. Uma transação eventual consistente consiste em uma série de etapas distribuídas. Portanto, quando o microsserviço de perfil do usuário recebe o comando UpdateUser, ele atualiza os detalhes do usuário em seu banco de dados e publica o evento UserUpdated no barramento de evento. O microsserviço carrinho de compras e o microsserviço de ordenação se inscreveu para receber esse evento e, em resposta, atualize as informações de comprador em seus respectivos bancos de dados.

> [!NOTE]
> O barramento de evento de eShopOnContainers, implementado usando RabbitMQ, destina-se a ser usado apenas como uma prova de conceito. Para sistemas de produção, as implementações de barramento de evento alternativo devem ser consideradas.

Para obter informações sobre a implementação do barramento de evento, consulte [Microsserviços do .NET: Arquitetura para aplicativos .NET em contêineres](https://aka.ms/microservicesebook).

## <a name="summary"></a>Resumo

Os Microsserviços oferecem uma abordagem ao desenvolvimento de aplicativos e implantação que é adequada para os requisitos de agilidade, escala e confiabilidade de aplicativos de nuvem modernos. Uma das principais vantagens de microsserviços é que eles podem ser escalados horizontalmente de forma independente, o que significa que uma área funcional específica pode ser dimensionada que que requer mais processamento de rede ou energia largura de banda para dar suporte à demanda, sem dimensionamento desnecessariamente áreas do o aplicativo que não estejam enfrentando o aumento na demanda.

Um contêiner é um, recurso portátil e controlado por ambiente operacional isolado, em que um aplicativo pode ser executado sem tocar os recursos de outros contêineres ou o host. As empresas estão adotando cada vez mais contêineres quando o Docker tornou-se a implementação do contêiner padrão que tem sido adotada pela maioria das plataformas de software e fornecedores de nuvem e aplicativos baseados em microsserviço de implementação.


## <a name="related-links"></a>Links relacionados

- [Baixe o livro eletrônico (PDF de 2Mb)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) (amostra)](https://github.com/dotnet-architecture/eShopOnContainers)
