---
title: Introdução ao desenvolvimento de aplicativos empresariais
description: Este capítulo fornece uma introdução ao desenvolvimento de aplicativos empresariais e apresenta o aplicativo móvel eShopOnContainers.
ms.prod: xamarin
ms.assetid: cbce0659-fa03-447a-86ec-140438143230
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
ms.openlocfilehash: 9bde1140f6590daa4b1d40a8b56edec314bfc66d
ms.sourcegitcommit: 699de58432b7da300ddc2c85842e5d9e129b0dc5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/25/2019
ms.locfileid: "70760233"
---
# <a name="introduction-to-enterprise-app-development"></a>Introdução ao desenvolvimento de aplicativos empresariais

Independentemente da plataforma, os desenvolvedores de aplicativos empresariais enfrentam vários desafios:

- Requisitos de aplicativo que podem mudar ao longo do tempo.
- Novos desafios e oportunidades de negócios.
- Comentários contínuos durante o desenvolvimento que podem afetar significativamente o escopo e os requisitos do aplicativo.

Com isso em mente, é importante criar aplicativos que podem ser facilmente modificados ou estendidos ao longo do tempo. A criação de tal adaptabilidade pode ser difícil, pois requer uma arquitetura que permita que partes individuais do aplicativo sejam desenvolvidas e testadas de forma independente, sem afetar o restante do aplicativo.

Muitos aplicativos empresariais são suficientemente complexos para exigir mais de um desenvolvedor. Pode ser um desafio significativo decidir como criar um aplicativo para que vários desenvolvedores possam trabalhar com eficiência em diferentes partes do aplicativo de forma independente, garantindo que as partes sejam juntas diretamente quando integradas ao aplicativo.

A abordagem tradicional para criar e criar um aplicativo resulta no que é conhecido como um aplicativo *monolítico* , em que os componentes são rigidamente acoplados sem nenhuma separação clara entre eles. Normalmente, essa abordagem monolítica leva a aplicativos difíceis e ineficientes de manter, pois pode ser difícil resolver bugs sem interromper outros componentes no aplicativo, e pode ser difícil adicionar novos recursos ou substituir os recursos existentes.

Uma solução eficaz para esses desafios é particionar um aplicativo em componentes discretos e livremente acoplados que podem ser facilmente integrados em um aplicativo. Essa abordagem oferece vários benefícios:

- Ele permite que a funcionalidade individual seja desenvolvida, testada, estendida e mantida por indivíduos ou equipes diferentes.
- Ele promove a reutilização e uma separação limpa das preocupações entre os recursos horizontais do aplicativo, como autenticação e acesso a dados e os recursos verticais, como a funcionalidade comercial específica do aplicativo. Isso permite que as dependências e as interações entre os componentes do aplicativo sejam gerenciadas mais facilmente.
- Ele ajuda a manter uma separação de funções permitindo que pessoas diferentes, ou equipes, se concentrem em uma tarefa específica ou uma parte da funcionalidade de acordo com sua experiência. Em particular, ele fornece uma separação mais clara entre a interface do usuário e a lógica de negócios do aplicativo.

No entanto, há muitos problemas que devem ser resolvidos ao particionar um aplicativo em componentes discretos e livremente acoplados. Elas incluem:

- Decidindo como fornecer uma separação clara de preocupações entre os controles de interface do usuário e sua lógica. Uma das decisões mais importantes ao criar um aplicativo do Xamarin. Forms Enterprise é estabelecer a lógica de negócios em arquivos code-behind ou criar uma separação clara de preocupações entre os controles de interface do usuário e sua lógica, para tornar o aplicativo mais passível de manutenção e de teste. Para obter mais informações, consulte [Model-View-ViewModel](~/xamarin-forms/enterprise-application-patterns/mvvm.md).
- Determinando se um contêiner de injeção de dependência deve ser usado. Contêineres de injeção de dependência reduzem a dependência entre objetos fornecendo um recurso para construir instâncias de classes com suas dependências injetadas e gerenciar seu tempo de vida com base na configuração do contêiner. Para obter mais informações, consulte [injeção de dependência](~/xamarin-forms/enterprise-application-patterns/dependency-injection.md).
- Escolher entre a plataforma fornecida eventos e a comunicação menos rígida baseada em mensagens entre componentes que são inconvenientes de vincular por referências de objeto e tipo. Para obter mais informações, consulte Introdução à [comunicação entre componentes livremente acoplados](~/xamarin-forms/enterprise-application-patterns/communicating-between-loosely-coupled-components.md).
- Decidindo como navegar entre páginas, incluindo como invocar a navegação e onde a lógica de navegação deve residir. Para obter mais informações, veja [Navegação](~/xamarin-forms/enterprise-application-patterns/navigation.md).
- Determinando como validar a entrada do usuário para exatidão. A decisão deve incluir como validar a entrada do usuário e como notificar o usuário sobre erros de validação. Para obter mais informações, consulte [validação](~/xamarin-forms/enterprise-application-patterns/validation.md).
- Decidindo como realizar a autenticação e como proteger recursos com autorização. Para obter mais informações, consulte [autenticação e autorização](~/xamarin-forms/enterprise-application-patterns/authentication-and-authorization.md).
- Determinando como acessar dados remotos de serviços da Web, incluindo como recuperar dados de maneira confiável e como armazenar dados em cache. Para obter mais informações, consulte [acessando dados remotos](~/xamarin-forms/enterprise-application-patterns/accessing-remote-data.md).
- Decidindo como testar o aplicativo. Para obter mais informações, consulte [testes de unidade](~/xamarin-forms/enterprise-application-patterns/unit-testing.md).

Este guia fornece orientação sobre esses problemas e se concentra nos padrões básicos e na arquitetura para criar um aplicativo empresarial multiplataforma usando o Xamarin. Forms. As diretrizes visam ajudar a produzir código adaptável, passível de manutenção e de teste, abordando cenários comuns de desenvolvimento de aplicativos empresariais Xamarin. Forms e separando as preocupações de apresentação, lógica de apresentação e entidades por meio de suporte para o Padrão Model-View-ViewModel (MVVM).

## <a name="sample-application"></a>Aplicativo de exemplo

Este guia inclui um aplicativo de exemplo, eShopOnContainers, que é uma loja online que inclui a seguinte funcionalidade:

- Autenticação e autorização em um serviço de back-end.
- Navegação em um catálogo de camisas, café Mugs e outros itens de marketing.
- Filtrando o catálogo.
- Ordenando itens do catálogo.
- Exibindo o histórico do pedido do usuário.
- Configuração de configurações.

### <a name="sample-application-architecture"></a>Arquitetura de aplicativo de exemplo

A Figura 1-1 fornece uma visão geral de alto nível da arquitetura do aplicativo de exemplo.

![](introduction-images/architecture.png "arquitetura de alto nível do eShopOnContainers")

**Figura 1-1**: arquitetura de alto nível do eShopOnContainers

O aplicativo de exemplo é fornecido com três aplicativos cliente:

- Um aplicativo MVC desenvolvido com ASP.NET Core.
- Um aplicativo de página única (SPA) desenvolvido com angular 2 e typescript. Essa abordagem para aplicativos Web evita executar uma viagem de ida e volta ao servidor com cada operação.
- Um aplicativo móvel desenvolvido com Xamarin. Forms, que dá suporte a iOS, Android e a Plataforma Universal do Windows (UWP).

Para obter informações sobre os aplicativos Web, consulte [arquitetando e desenvolvendo aplicativos Web modernos com ASP.NET Core e Microsoft Azure](https://aka.ms/WebAppEbook).

O aplicativo de exemplo inclui os seguintes serviços de back-end:

- Um microserviço de identidade, que usa ASP.NET Core Identity e IdentityServer.
- Um microserviço de catálogo, que é um serviço CRUD (criação, leitura, atualização, exclusão controlado por dados) que consome um banco de dado SQL Server usando o EntityFramework Core.
- Um microserviço de pedidos, que é um serviço orientado por domínio que usa padrões de design controlados por domínio.
- Um microserviço de cesta, que é um serviço CRUD controlado por dados que usa o cache Redis.

Esses serviços de back-end são implementados como microservices usando ASP.NET Core MVC e são implantados como contêineres exclusivos em um único host do Docker. Coletivamente, esses serviços de back-end são chamados de aplicativo de referência eShopOnContainers. Os aplicativos cliente se comunicam com os serviços de back-end por meio de uma interface da Web de transferência de estado de reapresentação (REST). Para obter mais informações sobre os microserviços e o Docker, consulte [microservices em contêineres](~/xamarin-forms/enterprise-application-patterns/containerized-microservices.md).

Para obter informações sobre a implementação dos serviços de back- [end, consulte microservices do .net: Arquitetura para aplicativos .NET em contêineres](https://aka.ms/microservicesebook).

### <a name="mobile-app"></a>Aplicativo móvel

Este guia concentra-se na criação de aplicativos empresariais de plataforma cruzada usando Xamarin. Forms e usa o aplicativo móvel eShopOnContainers como exemplo. A Figura 1-2 mostra as páginas do aplicativo móvel eShopOnContainers que fornecem a funcionalidade descrita anteriormente.

[![](introduction-images/screenshots.png "O aplicativo móvel eShopOnContainers")](introduction-images/screenshots-large.png#lightbox "O aplicativo móvel eShopOnContainers")

**Figura 1-2**: O aplicativo móvel eShopOnContainers

O aplicativo móvel consome os serviços de back-end fornecidos pelo aplicativo de referência eShopOnContainers. No entanto, ele pode ser configurado para consumir dados de serviços fictícios para aqueles que desejam evitar a implantação dos serviços de back-end.

O aplicativo móvel eShopOnContainers exercita a seguinte funcionalidade do Xamarin. Forms:

- XAML
- Controles
- Associações
- Conversores
- Estilos
- Animations
- Comandos
- Comportamentos
- Gatilhos
- Efeitos
- Renderizadores personalizados
- MessagingCenter
- Controles personalizados

Para obter mais informações sobre essa funcionalidade, consulte a [documentação do xamarin. Forms](~/xamarin-forms/index.yml)e [criando aplicativos móveis com Xamarin. Forms](https://aka.ms/xamebook).

Além disso, os testes de unidade são fornecidos para algumas das classes no aplicativo móvel eShopOnContainers.

#### <a name="mobile-app-solution"></a>Solução de aplicativo móvel

A solução de aplicativo móvel eShopOnContainers organiza o código-fonte e outros recursos em projetos. Todos os projetos usam pastas para organizar o código-fonte e outros recursos em categorias. A tabela a seguir descreve os projetos que compõem o aplicativo móvel eShopOnContainers:

|Projeto|Descrição|
|--- |--- |
|eShopOnContainers.Core|Este projeto é o projeto de PCL (biblioteca de classes portátil) que contém o código compartilhado e a interface do usuário compartilhada.|
|eShopOnContainers.Droid|Este projeto mantém o código específico do Android e é o ponto de entrada para o aplicativo do Android.|
|eShopOnContainers.iOS|Esse projeto mantém o código específico do iOS e é o ponto de entrada para o aplicativo iOS.|
|eShopOnContainers.UWP|Este projeto mantém o código específico Plataforma Universal do Windows (UWP) e é o ponto de entrada para o aplicativo do Windows.|
|eShopOnContainers.TestRunner.Droid|Este projeto é o executor de teste do Android para o projeto eShopOnContainers. UnitTests.|
|eShopOnContainers.TestRunner.iOS|Este projeto é o executor de teste do iOS para o projeto eShopOnContainers. UnitTests.|
|eShopOnContainers.TestRunner.Windows|Este projeto é o executor de teste Plataforma Universal do Windows para o projeto eShopOnContainers. UnitTests.|
|eShopOnContainers.UnitTests|Este projeto contém testes de unidade para o projeto eShopOnContainers. Core.|

As classes do aplicativo móvel eShopOnContainers podem ser usadas novamente em qualquer aplicativo Xamarin. Forms com pouca ou nenhuma modificação.

##### <a name="eshoponcontainerscore-project"></a>Projeto eShopOnContainers. Core

O projeto eShopOnContainers. Core PCL contém as seguintes pastas:

|Pasta|Descrição|
|--- |--- |
|Animations|Contém classes que permitem que as animações sejam consumidas em XAML.|
|Comportamentos|Contém comportamentos que são expostos para exibir classes.|
|Controles|Contém controles personalizados usados pelo aplicativo.|
|Conversores|Contém conversores de valor que aplicam lógica personalizada a uma associação.|
|Efeitos|Contém a `EntryLineColorEffect` classe, que é usada para alterar a cor da borda de `Entry` controles específicos.|
|Exceções|Contém o personalizado `ServiceAuthenticationException`.|
|Extensões|Contém métodos de extensão para `VisualElement` as `IEnumerable` classes e.|
|Auxiliares|Contém classes auxiliares para o aplicativo.|
|Modelos|Contém as classes de modelo para o aplicativo.|
|Propriedades|Contém `AssemblyInfo.cs`, um arquivo de metadados do assembly .net.|
|Serviços|Contém interfaces e classes que implementam serviços que são fornecidos ao aplicativo.|
|Gatilhos|Contém o `BeginAnimation` gatilho, que é usado para invocar uma animação em XAML.|
|Validações|Contém classes envolvidas na validação de entrada de dados.|
|ViewModels|Contém a lógica do aplicativo que é exposta a páginas.|
|Exibições|Contém as páginas do aplicativo.|

##### <a name="platform-projects"></a>Projetos de plataforma

Os projetos de plataforma contêm implementações de efeito, implementações de processador personalizado e outros recursos específicos da plataforma.

## <a name="summary"></a>Resumo

As ferramentas e plataformas de desenvolvimento de aplicativo móvel de plataforma cruzada do Xamarin fornecem uma solução abrangente para aplicativos cliente móveis B2E, B2B e B2C, fornecendo a capacidade de compartilhar código em todas as plataformas de destino (iOS, Android e Windows) e ajudar a reduzir o custo total de propriedade. Os aplicativos podem compartilhar sua interface do usuário e o código da lógica do aplicativo, mantendo a aparência da plataforma nativa.

Os desenvolvedores de aplicativos empresariais enfrentam vários desafios que podem alterar a arquitetura do aplicativo durante o desenvolvimento. Portanto, é importante criar um aplicativo para que ele possa ser modificado ou estendido ao longo do tempo. A criação de tal adaptabilidade pode ser difícil, mas normalmente envolve o particionamento de um aplicativo em componentes discretos e livremente acoplados que podem ser facilmente integrados em um aplicativo.

## <a name="related-links"></a>Links relacionados

- [Download do eBook (PDF de 2Mb)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) (exemplo)](https://github.com/dotnet-architecture/eShopOnContainers)
