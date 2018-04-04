---
title: Introdução
ms.prod: xamarin
ms.assetid: cbce0659-fa03-447a-86ec-140438143230
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
ms.openlocfilehash: a4f1f6ba820221be7553405f570911d3dc66a657
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="introduction"></a>Introdução

Independentemente de plataforma, os desenvolvedores de aplicativos corporativos enfrentam vários desafios:

-   Requisitos do aplicativo que podem ser alterados ao longo do tempo.
-   Desafios e novas oportunidades de negócios.
-   Comentários em andamento durante o desenvolvimento que pode afetar significativamente o escopo e os requisitos do aplicativo.

Com isso em mente, é importante criar aplicativos que podem ser facilmente modificados ou estendidos ao longo do tempo. Criando para essa capacidade de adaptação pode ser difícil, pois exige uma arquitetura que permite que as partes individuais do aplicativo para ser independente desenvolvidos e testados em isolamento sem afetar o restante do aplicativo.

Muitos aplicativos de empresa são suficientemente complexos para exigir mais de um desenvolvedor. Ele pode ser um desafio significativo para decidir como criar um aplicativo para que vários desenvolvedores possam trabalhar efetivamente em diferentes partes do aplicativo independentemente, garantindo que as partes se unem perfeitamente quando integrado no aplicativo.

A abordagem tradicional para Projetando e criando um aplicativo resulta no que é conhecida como um *monolítico* aplicativo, onde os componentes estão estritamente ligados sem separação clara entre eles. Normalmente, essa abordagem monolítica leva a aplicativos que são difíceis e ineficaz manter, porque pode ser difícil resolver erros sem interromper a outros componentes no aplicativo e pode ser difícil para adicionar novos recursos ou substituir os recursos existentes.

Uma solução eficaz para esses desafios é dividir um aplicativo em componentes discretos e flexíveis que podem ser facilmente integrados juntos em um aplicativo. Essa abordagem oferece vários benefícios:

-   Ele permite funcionalidade individual sejam desenvolvidos, testados, estendido e mantidos por outras pessoas ou equipes.
-   Promover uma separação limpa de preocupações entre recursos horizontal do aplicativo, como autenticação e o acesso a dados e os recursos verticais, como a funcionalidade do aplicativo de negócios específico e reutilização. Isso permite que as dependências e as interações entre os componentes do aplicativo a ser gerenciados mais facilmente.
-   Ele ajuda a manter uma separação de funções, permitindo que diferentes pessoas ou equipes, para se concentrar em uma tarefa específica ou a parte da funcionalidade de acordo com sua experiência. Em particular, ele fornece uma separação mais clara entre a interface do usuário e a lógica de negócios do aplicativo.

No entanto, há muitos problemas que devem ser resolvidos ao particionar um aplicativo em componentes discretos e flexíveis. Elas incluem:

-   Decidir como fornecer uma separação limpa de preocupações entre os controles de interface de usuário e sua lógica. Uma das decisões mais importantes ao criar um aplicativo da empresa xamarin. Forms é a possibilidade de colocar a lógica de negócios em arquivos code-behind, ou se deseja criar uma separação limpa de preocupações entre os controles de interface de usuário e sua lógica, para tornar o aplicativo mais manutenção e teste. Para obter mais informações, consulte [Model-View-ViewModel](~/xamarin-forms/enterprise-application-patterns/mvvm.md).
-   Determinar se irá usar um contêiner de injeção de dependência. Contêineres de injeção de dependência reduzem a dependência acoplamento entre objetos, fornecendo um recurso para construir instâncias de classes com suas dependências injetadas e gerenciar seu tempo de vida com base na configuração do contêiner. Para obter mais informações, consulte [injeção de dependência](~/xamarin-forms/enterprise-application-patterns/dependency-injection.md).
-   Escolhendo entre eventos de plataforma fornecido e livremente acoplada comunicação baseada em mensagens entre componentes inconvenientes vincular por referências de objeto e do tipo. Para obter mais informações, consulte Introdução [se comunicando entre livremente acoplada componentes](~/xamarin-forms/enterprise-application-patterns/communicating-between-loosely-coupled-components.md).
-   Decidir como navegar entre páginas, incluindo como chamar a barra de navegação, e onde a lógica de navegação deve residir. Para obter mais informações, veja [Navegação](~/xamarin-forms/enterprise-application-patterns/navigation.md).
-   Determinar como validar a entrada do usuário estão corretos. A decisão deve incluir como validar a entrada do usuário e como notificar o usuário sobre erros de validação. Para obter mais informações, consulte [validação](~/xamarin-forms/enterprise-application-patterns/validation.md).
-   A decisão sobre como executar a autenticação e como proteger os recursos de autorização. Para obter mais informações, consulte [autenticação e autorização](~/xamarin-forms/enterprise-application-patterns/authentication-and-authorization.md).
-   Determinar como acessar dados remotos da web services, incluindo como recuperar os dados de forma confiável e como armazenar em cache os dados. Para obter mais informações, consulte [acessar dados remotos](~/xamarin-forms/enterprise-application-patterns/accessing-remote-data.md).
-   Decisão sobre como testar o aplicativo. Para obter mais informações, consulte [teste de unidade](~/xamarin-forms/enterprise-application-patterns/unit-testing.md).

Este guia fornece orientação sobre esses problemas e enfoca os padrões de núcleos e a arquitetura para a criação de um aplicativo corporativo de plataforma cruzada com xamarin. Forms. As diretrizes tem o objetivo de ajudar a gerar código adaptável, manutenção e podem ser testado, abordando comuns cenários de desenvolvimento do aplicativo corporativo de xamarin. Forms e separando as preocupações de apresentação, lógica de apresentação e entidades por meio do suporte para o Padrão Model-View-ViewModel (MVVM).

## <a name="sample-application"></a>Aplicativo de exemplo

Este guia inclui um aplicativo de exemplo, eShopOnContainers, que é uma loja online que inclui a seguinte funcionalidade:

-   Autenticação e autorização em um serviço de back-end.
-   Navegando em um catálogo de camisas, canecas e outros itens de marketing.
-   O catálogo de filtragem.
-   Ordenando itens do catálogo.
-   Exibindo o histórico de pedidos do usuário.
-   Definição das configurações.

### <a name="sample-application-architecture"></a>Arquitetura do aplicativo de exemplo

Figura 1-1 fornece uma visão geral da arquitetura do aplicativo de exemplo.

![](introduction-images/architecture.png "arquitetura de alto nível eShopOnContainers")

**Figura 1-1**: arquitetura de alto nível eShopOnContainers

O aplicativo de exemplo é fornecido com três aplicativos de cliente:

-   Um aplicativo MVC são desenvolvidos com ASP.NET Core.
-   Um aplicativo de página única (SPA) desenvolvido com 2 Angular e Typescript. Essa abordagem para aplicativos web evita executando uma viagem para o servidor com cada operação.
-   Um aplicativo móvel desenvolvidos com xamarin. Forms, que dá suporte ao iOS, Android e o Windows UWP (plataforma Universal).

Para obter informações sobre os aplicativos da web, consulte [projetar e desenvolver aplicativos de Web modernos com ASP.NET Core e Microsoft Azure](http://aka.ms/WebAppEbook).

O aplicativo de exemplo inclui os seguintes serviços de back-end:

-   Um microsserviço de identidade, que usa a identidade do ASP.NET Core e IdentityServer.
-   Um microsserviço do catálogo, que é uma criar controladas por dados, ler, atualizar e excluir o serviço (CRUD) que consome um banco de dados do SQL Server usando o EntityFramework Core.
-   Um ordenação microsserviço, que é um serviço orientado a domínio que usa os padrões de design controlado por domínio.
-   Um microsserviço carrinho, que é um serviço CRUD controladas por dados que usa o Cache Redis.

Esses serviços de back-end são implementados como microservices usando o ASP.NET MVC de núcleo e são implantados como contêineres exclusivos dentro de um único host do Docker. Coletivamente, esses serviços de back-end são referidos como os eShopOnContainers Referência de aplicativo. Aplicativos cliente se comunicar com os serviços de back-end por meio de uma interface da web REST Representational State Transfer (). Para obter mais informações sobre microservices e o Docker, consulte [Microservices em contêineres](~/xamarin-forms/enterprise-application-patterns/containerized-microservices.md).

Para obter informações sobre a implementação dos serviços de back-end, consulte [Microservices .NET: arquitetura de aplicativos do .NET em contêineres](https://aka.ms/microservicesebook).

### <a name="mobile-app"></a>Aplicativo móvel

Este guia se concentra na criação de aplicativos de empresa de plataforma cruzada usando xamarin. Forms e usa o aplicativo móvel eShopOnContainers como um exemplo. Figura 1-2 mostra as páginas do aplicativo móvel eShopOnContainers que fornecem a funcionalidade descrita anteriormente.

[![](introduction-images/screenshots.png "O aplicativo móvel eShopOnContainers")](introduction-images/screenshots-large.png#lightbox "o aplicativo móvel eShopOnContainers")

**Figura 1-2**: O aplicativo móvel eShopOnContainers

O aplicativo móvel consome os serviços de back-end fornecidos pelo aplicativo eShopOnContainers referência. No entanto, ele pode ser configurado para consumir dados de serviços de simulação para quem deseja evitar a implantação dos serviços de back-end.

O aplicativo móvel eShopOnContainers emprega a xamarin. Forms a seguinte funcionalidade:

-   XAML
-   Controles
-   Associações
-   Conversores
-   Estilos
-   Animations
-   Comandos
-   Comportamentos
-   Gatilhos
-   Efeitos
-   Processadores personalizados
-   MessagingCenter
-   Controles personalizados

Para obter mais informações sobre essa funcionalidade, consulte o [xamarin. Forms documentação](~/xamarin-forms/index.yml), e [criação de aplicativos móveis com o xamarin. Forms](https://aka.ms/xamebook).

Além disso, os testes de unidade são fornecidos para algumas das classes no aplicativo móvel do eShopOnContainers.

#### <a name="mobile-app-solution"></a>Solução de aplicativo móvel

A solução de aplicativo móvel eShopOnContainers organiza o código-fonte e outros recursos em projetos. Todos os projetos usam pastas para organizar o código-fonte e outros recursos em categorias. A tabela a seguir descreve os projetos que compõem o aplicativo móvel eShopOnContainers:

|Projeto|Descrição|
|--- |--- |
|eShopOnContainers.Core|Este projeto é um projeto de biblioteca (PCL) de classes portátil que contém o código compartilhado e a interface de usuário compartilhada.|
|eShopOnContainers.Droid|Este projeto contém o código específico do Android e é o ponto de entrada para o aplicativo do Android.|
|eShopOnContainers.iOS|Este projeto contém código específico de iOS e é o ponto de entrada para o aplicativo iOS.|
|eShopOnContainers.UWP|Este projeto contém o código específico do Windows UWP (plataforma Universal) e é o ponto de entrada para o aplicativo do Windows.|
|eShopOnContainers.TestRunner.Droid|Este projeto é o executor de teste Android para o projeto eShopOnContainers.UnitTests.|
|eShopOnContainers.TestRunner.iOS|Este projeto é o executor de teste do iOS para o projeto eShopOnContainers.UnitTests.|
|eShopOnContainers.TestRunner.Windows|Este projeto é o executor de testes de plataforma Universal do Windows para o projeto eShopOnContainers.UnitTests.|
|eShopOnContainers.UnitTests|Este projeto contém testes de unidade para o projeto eShopOnContainers.Core.|

As classes do aplicativo móvel eShopOnContainers podem ser reutilizadas em qualquer aplicativo xamarin. Forms com pouca ou nenhuma modificação.

##### <a name="eshoponcontainerscore-project"></a>eShopOnContainers.Core Project

O projeto PCL eShopOnContainers.Core contém as seguintes pastas:

|Pasta|Descrição|
|--- |--- |
|Animations|Contém classes que permitem animações a serem consumidos em XAML.|
|Comportamentos|Contém os comportamentos que são expostos para exibir as classes.|
|Controles|Contém controles personalizados usados pelo aplicativo.|
|Conversores|Contém conversores de valor que se aplicam a lógica personalizada para uma associação.|
|Efeitos|Contém o `EntryLineColorEffect` classe, que é usado para alterar a cor da borda de específicos `Entry` controles.|
|Exceções|Contém a custom `ServiceAuthenticationException`.|
|Extensões|Contém métodos de extensão para o `VisualElement` e `IEnumerable` classes.|
|Auxiliares|Contém classes auxiliares para o aplicativo.|
|Modelos|Contém as classes de modelo para o aplicativo.|
|Propriedades|Contém `AssemblyInfo.cs`, um arquivo de metadados de assembly do .NET.|
|Serviços|Contém interfaces e classes que implementam os serviços que são fornecidos para o aplicativo.|
|Gatilhos|Contém o `BeginAnimation` gatilho, que é usado para invocar uma animação em XAML.|
|Validações|Contém classes envolvidas na validação de entrada de dados.|
|ViewModels|Contém a lógica do aplicativo que é exposta a páginas.|
|Exibições|Contém as páginas do aplicativo.|

##### <a name="platform-projects"></a>Projetos de plataforma

Os projetos de plataforma contém implementações de efeito, implementações do renderizador personalizado e outros recursos específicos da plataforma.

## <a name="summary"></a>Resumo

Plataformas e ferramentas de desenvolvimento de aplicativo móvel de plataforma cruzada do Xamarin fornecem uma solução abrangente para clientes móveis B2E B2B e B2C aplicativos, fornecendo a capacidade de compartilhar o código em todas as plataformas de destino (iOS, Android e Windows) e ajuda a reduzir a custo total de propriedade. Aplicativos podem compartilhar o seu aplicativo e interface lógica código do usuário, enquanto mantém a aparência nativa de plataforma.

Os desenvolvedores de aplicativos corporativos enfrentam vários desafios que podem alterar a arquitetura do aplicativo durante o desenvolvimento. Portanto, é importante compilar um aplicativo para que podem ser modificado ou estendido ao longo do tempo. Criando para essa capacidade de adaptação pode ser difícil, mas geralmente envolve o particionamento de um aplicativo em componentes discretos e flexíveis que podem ser facilmente integrados juntos em um aplicativo.


## <a name="related-links"></a>Links relacionados

- [Baixar eBook (2Mb PDF)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) (sample)](https://github.com/dotnet-architecture/eShopOnContainers)
