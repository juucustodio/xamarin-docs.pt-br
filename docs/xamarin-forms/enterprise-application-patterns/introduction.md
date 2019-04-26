---
title: Introdução ao desenvolvimento de aplicativos empresariais
description: Este capítulo fornece uma introdução ao desenvolvimento de aplicativos da empresa e apresenta o aplicativo móvel do eShopOnContainers.
ms.prod: xamarin
ms.assetid: cbce0659-fa03-447a-86ec-140438143230
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
ms.openlocfilehash: 9deb685c92092ceb0e1c775a1e53ac1bce5a4a57
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61299962"
---
# <a name="introduction-to-enterprise-app-development"></a>Introdução ao desenvolvimento de aplicativos empresariais

Independentemente da plataforma, os desenvolvedores de aplicativos empresariais enfrentam vários desafios:

-   Requisitos de aplicativo que podem ser alterados ao longo do tempo.
-   Novas oportunidades de negócios e desafios.
-   Comentários em andamento durante o desenvolvimento pode afetar significativamente o escopo e os requisitos do aplicativo.

Com isso em mente, é importante criar aplicativos que podem ser facilmente modificados ou estendidos ao longo do tempo. A criação de tal adaptabilidade pode ser difícil, pois exige uma arquitetura que permite que as partes individuais do aplicativo seja desenvolvido e testado isoladamente sem afetar o restante do aplicativo de forma independente.

Muitos aplicativos empresariais são suficientemente complexos para exigir mais de um desenvolvedor. Ele pode ser um desafio significativo para decidir como criar um aplicativo de modo que vários desenvolvedores podem trabalhar efetivamente em diferentes partes do aplicativo de forma independente, garantindo que as partes se reúnem perfeitamente quando integrado ao aplicativo.

A abordagem tradicional para projetar e criar um aplicativo resulta no que é conhecida como um *monolítico* aplicativo, em que os componentes estejam intimamente ligados a nenhuma separação clara entre eles. Normalmente, essa abordagem monolítica leva a aplicativos que são difíceis e ineficiente para manter, porque ele pode ser difícil resolver bugs sem interromper outros componentes no aplicativo, e pode ser difícil para adicionar novos recursos ou substituir recursos existentes.

Um remédio eficaz para esses desafios é dividir um aplicativo em componentes discretos e flexíveis que podem ser facilmente integrados juntos em um aplicativo. Essa abordagem oferece vários benefícios:

-   Ele permite que a funcionalidade individual ser desenvolvidos, testados, estendido e mantidos por outras pessoas ou equipes.
-   Ela promove a reutilização e uma separação limpa de preocupações entre as funcionalidades do aplicativo horizontal, como autenticação e acesso a dados e os recursos verticais, como funcionalidades de negócios específicas do aplicativo. Isso permite que as dependências e as interações entre componentes de aplicativo a ser gerenciados mais facilmente.
-   Ele ajuda a manter uma separação de funções, permitindo que diferentes pessoas ou equipes, para se concentrar em uma tarefa específica ou a parte da funcionalidade de acordo com seus conhecimentos. Em particular, ele fornece uma separação mais clara entre a interface do usuário e a lógica de negócios do aplicativo.

No entanto, há muitos problemas que devem ser resolvidos ao particionar um aplicativo em componentes discretos, livremente acoplados. Elas incluem:

-   Decidir como fornecer uma separação limpa de preocupações entre os controles de interface do usuário e sua lógica. Uma das decisões mais importantes ao criar um aplicativo empresarial de xamarin. Forms é se é necessário colocar a lógica de negócios em arquivos code-behind, ou se é necessário criar uma separação limpa de preocupações entre os controles de interface do usuário e a respectiva lógica, para tornar o aplicativo mais fácil de manter e que podem ser testados. Para obter mais informações, consulte [Model-View-ViewModel](~/xamarin-forms/enterprise-application-patterns/mvvm.md).
-   Determinar se é necessário usar um contêiner de injeção de dependência. Contêineres de injeção de dependência reduzem a dependência entre objetos fornecendo um recurso para construir instâncias de classes com suas dependências injetadas o acoplamento e gerenciar seu tempo de vida com base na configuração do contêiner. Para obter mais informações, consulte [injeção de dependência](~/xamarin-forms/enterprise-application-patterns/dependency-injection.md).
-   Escolhendo entre eventing fornecida pela plataforma e fracamente acoplado a comunicação baseada em mensagens entre os componentes são inconvenientes para vincular as referências de objeto e o tipo. Para obter mais informações, consulte Introdução [comunicando-se entre fracamente acoplados componentes](~/xamarin-forms/enterprise-application-patterns/communicating-between-loosely-coupled-components.md).
-   Decidir como navegar entre páginas, incluindo como invocar o painel de navegação, e onde a lógica de navegação deve residir. Para obter mais informações, veja [Navegação](~/xamarin-forms/enterprise-application-patterns/navigation.md).
-   Determinando como validar a entrada do usuário estão corretos. A decisão deve incluir como validar a entrada do usuário e como notificar o usuário sobre erros de validação. Para obter mais informações, consulte [validação](~/xamarin-forms/enterprise-application-patterns/validation.md).
-   A decisão sobre como realizar a autenticação e como proteger recursos com a autorização. Para obter mais informações, consulte [autenticação e autorização](~/xamarin-forms/enterprise-application-patterns/authentication-and-authorization.md).
-   Determinar como acessar dados remotos da web services, incluindo como recuperar os dados de forma confiável e como armazenar em cache os dados. Para obter mais informações, consulte [acessar dados remotos](~/xamarin-forms/enterprise-application-patterns/accessing-remote-data.md).
-   Decidindo como testar o aplicativo. Para obter mais informações, consulte [Unit Testing](~/xamarin-forms/enterprise-application-patterns/unit-testing.md).

Este guia fornece orientação sobre esses problemas e se concentra nos principais padrões e arquitetura para a criação de um aplicativo empresarial de plataforma cruzada usando xamarin. Forms. As diretrizes tem o objetivo de ajudar a produzir código testável adaptável e sustentável, abordando comuns de desenvolvimento de aplicativo xamarin. Forms enterprise e ao separar as preocupações de apresentação, lógica de apresentação e entidades por meio do suporte para o Padrão Model-View-ViewModel (MVVM).

## <a name="sample-application"></a>Aplicativo de exemplo

Este guia inclui um aplicativo de exemplo eShopOnContainers, que é uma loja online que inclui as seguintes funcionalidades:

-   Autenticando e autorizando em relação a um serviço de back-end.
-   Navegando em um catálogo de camisas, canecas e outros itens de marketing.
-   O catálogo de filtragem.
-   Ordenando itens do catálogo.
-   Exibindo o histórico de pedidos do usuário.
-   Configuração de configurações.

### <a name="sample-application-architecture"></a>Arquitetura do aplicativo de exemplo

Figura 1-1 fornece uma visão geral da arquitetura do aplicativo de exemplo.

![](introduction-images/architecture.png "arquitetura de alto nível do eShopOnContainers")

**Figura 1-1**: arquitetura de alto nível do eShopOnContainers

O aplicativo de exemplo é fornecido com três aplicativos de cliente:

-   Um aplicativo MVC são desenvolvidos com o ASP.NET Core.
-   Um aplicativo de página única (SPA) desenvolvidos com o Angular 2 e Typescript. Essa abordagem para aplicativos web evita executando uma ida e volta ao servidor com cada operação.
-   Um aplicativo móvel desenvolvido com o xamarin. Forms, que dá suporte a iOS, Android e Universal Windows Platform (UWP).

Para obter informações sobre os aplicativos web, consulte [projetar e desenvolver aplicativos Web modernos com o ASP.NET Core e Microsoft Azure](http://aka.ms/WebAppEbook).

O aplicativo de exemplo inclui os seguintes serviços de back-end:

-   Um microsserviço de identidade, que usa a identidade do ASP.NET Core e IdentityServer.
-   Um microsserviço de catálogo, que é um controlado por dados criar, ler, atualizar, excluir (CRUD) de serviço que consome um banco de dados do SQL Server usando o EntityFramework Core.
-   Um microsserviço de pedidos, que é um serviço orientado a domínio que usa os padrões de design controlado por domínio.
-   Um microsserviço de cesta de compras, que é um serviço CRUD controlada por dados que usa o Cache Redis.

Esses serviços de back-end são implementados como microsserviços usando o ASP.NET Core MVC e são implantados como contêineres exclusivos dentro de um único host do Docker. Coletivamente, esses serviços de back-end são referenciados como aplicativo de referência eShopOnContainers. Aplicativos cliente se comunicar com os serviços de back-end por meio de uma interface da web REST Representational State Transfer (). Para obter mais informações sobre microsserviços e Docker, consulte [Microsserviços em contêineres](~/xamarin-forms/enterprise-application-patterns/containerized-microservices.md).

Para obter informações sobre a implementação dos serviços de back-end, consulte [Microsserviços do .NET: Arquitetura para aplicativos .NET em contêineres](https://aka.ms/microservicesebook).

### <a name="mobile-app"></a>Aplicativo móvel

Este guia se concentra na criação de aplicativos empresariais de plataforma cruzada usando xamarin. Forms e usa o aplicativo móvel do eShopOnContainers como um exemplo. Figura 1-2 mostra as páginas do aplicativo móvel do eShopOnContainers que fornecem a funcionalidade descrita anteriormente.

[![](introduction-images/screenshots.png "O aplicativo móvel do eShopOnContainers")](introduction-images/screenshots-large.png#lightbox "o aplicativo móvel do eShopOnContainers")

**Figura 1-2**: O aplicativo móvel do eShopOnContainers

O aplicativo móvel consome os serviços de back-end fornecidos pelo aplicativo eShopOnContainers de referência. No entanto, ele pode ser configurado para consumir dados de serviços fictícios para aqueles que desejam evitar a implantação dos serviços de back-end.

O aplicativo móvel do eShopOnContainers exercita a seguinte funcionalidade do xamarin. Forms:

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
-   Renderizadores personalizados
-   MessagingCenter
-   Controles personalizados

Para obter mais informações sobre essa funcionalidade, consulte o [documentação do xamarin. Forms](~/xamarin-forms/index.yml), e [criação de aplicativos móveis com xamarin. Forms](https://aka.ms/xamebook).

Além disso, os testes de unidade são fornecidos para algumas das classes no aplicativo móvel do eShopOnContainers.

#### <a name="mobile-app-solution"></a>Solução de aplicativo móvel

A solução de aplicativo móvel do eShopOnContainers organiza o código-fonte e outros recursos em projetos. Todos os projetos usam pastas para organizar o código-fonte e outros recursos em categorias. A tabela a seguir descreve os projetos que compõem o aplicativo móvel do eShopOnContainers:

|Projeto|Descrição|
|--- |--- |
|eShopOnContainers.Core|Este projeto é o projeto PCL (biblioteca) de classes portátil que contém o código compartilhado e a interface do usuário compartilhada.|
|eShopOnContainers.Droid|Este projeto contém código específico do Android e é o ponto de entrada para o aplicativo do Android.|
|eShopOnContainers.iOS|Este projeto contém o código específico do iOS e é o ponto de entrada para o aplicativo iOS.|
|eShopOnContainers.UWP|Este projeto contém o código específico da plataforma Universal do Windows (UWP) e é o ponto de entrada para o aplicativo do Windows.|
|eShopOnContainers.TestRunner.Droid|Esse projeto é o executor de teste de Android para o projeto eShopOnContainers.UnitTests.|
|eShopOnContainers.TestRunner.iOS|Esse projeto é o executor de teste do iOS para o projeto eShopOnContainers.UnitTests.|
|eShopOnContainers.TestRunner.Windows|Esse projeto é o executor de teste da plataforma Universal do Windows para o projeto eShopOnContainers.UnitTests.|
|eShopOnContainers.UnitTests|Este projeto contém testes de unidade para o projeto eShopOnContainers.Core.|

As classes do aplicativo móvel do eShopOnContainers podem ser reutilizadas em qualquer aplicativo xamarin. Forms com pouca ou nenhuma modificação.

##### <a name="eshoponcontainerscore-project"></a>eShopOnContainers.Core projeto

O projeto PCL eShopOnContainers.Core contém as seguintes pastas:

|Pasta|Descrição|
|--- |--- |
|Animations|Contém classes que permitem que as animações a serem consumidos em XAML.|
|Comportamentos|Contém os comportamentos que são expostos para exibir as classes.|
|Controles|Contém os controles personalizados usados pelo aplicativo.|
|Conversores|Contém os conversores de valor que se aplicam a lógica personalizada a uma associação.|
|Efeitos|Contém o `EntryLineColorEffect` classe, que é usado para alterar a cor da borda de determinado `Entry` controles.|
|Exceções|Contém o personalizado `ServiceAuthenticationException`.|
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

Os projetos de plataforma contêm implementações de efeito, implementações de renderizador personalizado e outros recursos específicos da plataforma.

## <a name="summary"></a>Resumo

Plataformas e ferramentas de desenvolvimento de aplicativo móvel de plataforma cruzada do Xamarin fornecem uma solução abrangente para o cliente móvel B2E, B2B e B2C aplicativos, fornecendo a capacidade de compartilhar código em todas as plataformas de destino (iOS, Android e Windows) e ajudando a diminuir o custo total de propriedade. Aplicativos podem compartilhar o seu aplicativo e interface lógica código do usuário, enquanto retém a aparência plataforma nativa.

Os desenvolvedores de aplicativos empresariais enfrentam vários desafios que podem alterar a arquitetura do aplicativo durante o desenvolvimento. Portanto, é importante compilar um aplicativo para que possa ser modificado ou estendido ao longo do tempo. A criação de tal adaptabilidade pode ser difícil, mas geralmente envolve o particionamento de um aplicativo em componentes discretos e flexíveis que podem ser facilmente integrados juntos em um aplicativo.


## <a name="related-links"></a>Links relacionados

- [Baixe o livro eletrônico (PDF de 2Mb)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) (amostra)](https://github.com/dotnet-architecture/eShopOnContainers)
