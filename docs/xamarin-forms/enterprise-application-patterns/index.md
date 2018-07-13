---
title: Padrões de aplicativo empresarial usando o livro eletrônico do xamarin. Forms
description: Este livro eletrônico fornece diretrizes de arquitetura para o desenvolvimento de aplicativos empresariais de xamarin. Forms testáveis adaptáveis e sustentáveis.
ms.prod: xamarin
ms.assetid: 28cfed6c-6175-4223-a8cc-798d40bf0832
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
ms.openlocfilehash: ecfe99f66e16eafabc3117036ff065e3a35259c3
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/12/2018
ms.locfileid: "38994342"
---
# <a name="enterprise-application-patterns-using-xamarinforms-ebook"></a>Padrões de aplicativo empresarial usando o livro eletrônico do xamarin. Forms

_Diretrizes de arquitetura para o desenvolvimento de aplicativos empresariais de xamarin. Forms testáveis adaptáveis e sustentáveis_

![](images/cover-sml.png "Padrões de aplicativo empresarial usando o livro eletrônico do xamarin. Forms")

Este livro eletrônico fornece orientação sobre como implementar o padrão Model-View-ViewModel (MVVM), a injeção de dependência, a navegação, a validação e o gerenciamento de configuração, mantendo um acoplamento flexível. Além disso, há também orientações sobre como executar autenticação e autorização com IdentityServer, acessando dados através de microsserviços em contêineres e testes de unidade.

## <a name="prefaceprefacemd"></a>[Prefácio](preface.md)

Este capítulo explica a finalidade e o escopo do guia e que ele é destinado.

## <a name="introductionintroductionmd"></a>[Introdução](introduction.md)

Os desenvolvedores de aplicativos empresariais enfrentam vários desafios que podem alterar a arquitetura do aplicativo durante o desenvolvimento. Portanto, é importante compilar um aplicativo para que possa ser modificado ou estendido ao longo do tempo. A criação de tal adaptabilidade pode ser difícil, mas geralmente envolve o particionamento de um aplicativo em componentes discretos e flexíveis que podem ser facilmente integrados juntos em um aplicativo.

## <a name="mvvmmvvmmd"></a>[MVVM](mvvm.md)

O padrão Model-View-ViewModel (MVVM) ajuda a separar a lógica de negócios e apresentação de um aplicativo de sua interface do usuário (UI) de forma precisa. Mantendo uma separação clara entre a lógica do aplicativo e a interface do usuário ajuda a resolver vários problemas de desenvolvimento e pode tornar mais fácil de testar um aplicativo, manter e evoluem. Ele pode também melhoram muito a oportunidades de reutilização de código e permite que os desenvolvedores e designers de interface do usuário mais facilmente colaboram durante o desenvolvimento de suas respectivas partes de um aplicativo.

## <a name="dependency-injectiondependency-injectionmd"></a>[Injeção de dependência](dependency-injection.md)

Injeção de dependência permite desassociação de tipos concretos do código que depende desses tipos. Normalmente, ele usa um contêiner que mantém uma lista de registros e mapeamentos entre as interfaces e tipos abstratos e os tipos concretos que implementam ou estendem a esses tipos.

Contêineres de injeção de dependência reduzem o acoplamento entre objetos fornecendo um recurso para instanciar instâncias de classe e gerenciar seu tempo de vida com base na configuração do contêiner. Durante a criação de objetos, o contêiner injeta quaisquer dependências que exige que o objeto nele. Se essas dependências ainda não tem sido criadas, o contêiner cria e resolve as dependências entre primeiro.

## <a name="communicating-between-loosely-coupled-componentscommunicating-between-loosely-coupled-componentsmd"></a>[Comunicação entre componentes flexíveis](communicating-between-loosely-coupled-components.md)

O xamarin. Forms [ `MessagingCenter` ](xref:Xamarin.Forms.MessagingCenter) classe implementa a publicar-padrão, permitindo a comunicação baseada em mensagens entre os componentes são inconvenientes para vincular as referências de objeto e o tipo de assinatura. Esse mecanismo permite que os publicadores e assinantes para se comunicar sem a necessidade de uma referência entre si, ajudando a reduzir as dependências entre componentes, enquanto também permite que componentes de forma independente seja desenvolvido e testado.

## <a name="navigationnavigationmd"></a>[Navegação](navigation.md)

Xamarin. Forms inclui suporte para navegação de página, que geralmente resulta da interação do usuário com a interface do usuário ou do aplicativo em si, como resultado das alterações de estado interno controlado por lógica. No entanto, a navegação pode ser complexa para implementar em aplicativos que usam o padrão MVVM.

Este capítulo apresenta um `NavigationService` classe, que é usado para executar a navegação de model first de modo de exibição de modelos de exibição. Colocar a lógica de navegação no modo de exibição classes de modelo significa que a lógica pode ser exercida por meio de testes automatizados. Além disso, o modelo de exibição, em seguida, pode implementar a lógica para controlar a navegação para assegurar que determinadas regras de negócio são aplicadas.

## <a name="validationvalidationmd"></a>[Validação](validation.md)

Qualquer aplicativo que aceita entrada de usuários deve garantir que a entrada é válida. Sem validação, um usuário pode fornecer dados que faz com que o aplicativo falhe. Validação impõe regras de negócio e impede que um invasor injetando dados mal-intencionados.

No contexto do modelo modelo de ViewModel (MVVM) padrão, um modelo de exibição ou modelo geralmente precisará executar a validação de dados e sinalizar erros de validação para o modo de exibição para que o usuário possa corrigi-los.

## <a name="configuration-managementconfiguration-managementmd"></a>[Gerenciamento de configurações](configuration-management.md)

As configurações permitem a separação dos dados que configura o comportamento de um aplicativo do código, permitindo que o comportamento a ser alterado sem recompilar o aplicativo. Configurações do aplicativo são dados que um aplicativo cria e gerencia e configurações de usuário são as configurações personalizáveis de um aplicativo que afetam o comportamento do aplicativo e não exigem o ajuste re frequente.

## <a name="containerized-microservicescontainerized-microservicesmd"></a>[Microservices em contêineres](containerized-microservices.md)

Os Microsserviços oferecem uma abordagem ao desenvolvimento de aplicativos e implantação que é adequada para os requisitos de agilidade, escala e confiabilidade de aplicativos de nuvem modernos. Uma das principais vantagens de microsserviços é que eles podem ser escalados horizontalmente de forma independente, o que significa que uma área funcional específica pode ser dimensionada que que requer mais processamento de rede ou energia largura de banda para dar suporte à demanda, sem dimensionamento desnecessariamente áreas do o aplicativo que não estejam enfrentando o aumento na demanda.

## <a name="authentication-and-authorizationauthentication-and-authorizationmd"></a>[Autenticação e autorização](authentication-and-authorization.md)

Há muitas abordagens para integrar a autenticação e autorização em um aplicativo xamarin. Forms que se comunica com um aplicativo web ASP.NET MVC. Aqui, autenticação e autorização são executadas com um microsserviço de identidade em contêineres que usa 4 IdentityServer. IdentityServer é uma estrutura de OAuth 2.0 e OpenID Connect do código-fonte aberto para o ASP.NET Core se integra ao ASP.NET Core Identity para realizar a autenticação de token de portador.

## <a name="accessing-remote-dataaccessing-remote-datamd"></a>[Acesso a dados remotos](accessing-remote-data.md)

Muitas soluções modernas baseado na web fazer uso dos serviços da web, hospedados por servidores web, para fornecer funcionalidade para o cliente remoto a aplicativos. As operações que um serviço web expõe constituem uma API da web e aplicativos de cliente devem ser capazes de utilizar a API da web sem saber como os dados ou operações que expõe a API são implementadas.

## <a name="unit-testingunit-testingmd"></a>[Testes de Unidade](unit-testing.md)

Teste de modelos e modelos de exibição de aplicativos MVVM é idêntico ao teste de outras classes e as mesmas ferramentas e técnicas podem ser usadas. No entanto, há alguns padrões que são comuns ao modelo e classes de modelo de exibição, que podem se beneficiar das técnicas de teste de unidade específica.

## <a name="feedback"></a>Comentários

Este projeto tem um site da comunidade, no qual você pode postar perguntas e fornecer comentários. O site da comunidade está localizado em [GitHub](https://github.com/dotnet-architecture/eShopOnContainers). Como alternativa, comentários sobre o livro eletrônico podem ser enviados por email para [ dotnet-architecture-ebooks-feedback@service.microsoft.com ](mailto:dotnet-architecture-ebooks-feedback@service.microsoft.com).


## <a name="related-links"></a>Links relacionados

- [Baixe o livro eletrônico (PDF de 2Mb)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) (amostra)](https://github.com/dotnet-architecture/eShopOnContainers)
