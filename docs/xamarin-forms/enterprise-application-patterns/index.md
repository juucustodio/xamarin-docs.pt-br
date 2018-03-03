---
title: "Padrões de aplicativo empresarial usando eBook xamarin. Forms"
description: "Diretrizes de arquitetura para desenvolvimento de aplicativos de empresa xamarin. Forms testáveis adaptáveis e sustentáveis"
ms.topic: article
ms.prod: xamarin
ms.assetid: 28cfed6c-6175-4223-a8cc-798d40bf0832
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
ms.openlocfilehash: 7ed546ac975ce1956d94d509486e4cfb25d28100
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/28/2018
---
# <a name="enterprise-application-patterns-using-xamarinforms-ebook"></a>Padrões de aplicativo empresarial usando eBook xamarin. Forms

_Diretrizes de arquitetura para desenvolvimento de aplicativos de empresa xamarin. Forms testáveis adaptáveis e sustentáveis_

![](images/cover-sml.png "Padrões de aplicativo empresarial usando eBook xamarin. Forms")

Este livro eletrônico fornece orientação sobre como implementar o padrão Model-View-ViewModel (MVVM) injeção de dependência, navegação, validação e gerenciamento de configuração, mantendo um acoplamento flexível. Além disso, há também orientações sobre como executar a autenticação e autorização com IdentityServer, acesso a dados de microservices em contêineres e testes de unidade.

## <a name="prefaceprefacemd"></a>[Prefácio](preface.md)

Este capítulo explica a finalidade e o escopo do guia e que ele é destinado a.

## <a name="introductionintroductionmd"></a>[Introdução](introduction.md)

Os desenvolvedores de aplicativos corporativos enfrentam vários desafios que podem alterar a arquitetura do aplicativo durante o desenvolvimento. Portanto, é importante compilar um aplicativo para que podem ser modificado ou estendido ao longo do tempo. Criando para essa capacidade de adaptação pode ser difícil, mas geralmente envolve o particionamento de um aplicativo em componentes discretos e flexíveis que podem ser facilmente integrados juntos em um aplicativo.

## <a name="mvvmmvvmmd"></a>[MVVM](mvvm.md)

O padrão Model-View-ViewModel (MVVM) ajuda a separar corretamente a lógica de negócios e apresentação de um aplicativo em sua interface de usuário (IU). Manter uma separação clara entre a lógica do aplicativo e a interface do usuário ajuda a resolver vários problemas de desenvolvimento e pode tornar mais fácil de testar, manter e desenvolver um aplicativo. Ele pode melhorar significativamente o oportunidades de reutilização de código e permite que os desenvolvedores e designers de interface do usuário mais facilmente colaboram ao desenvolver suas respectivas partes de um aplicativo.

## <a name="dependency-injectiondependency-injectionmd"></a>[Injeção de dependência](dependency-injection.md)

Injeção de dependência habilita desacoplar tipos concretos do código que depende desses tipos. Normalmente, ele usa um contêiner que mantém uma lista de registros e os mapeamentos entre as interfaces e tipos abstratos e os tipos concretos que implementam ou estendem esses tipos.

Contêineres de injeção de dependência reduzem o acoplamento entre objetos, fornecendo um recurso de instância de classe e gerenciar seu tempo de vida com base na configuração do contêiner. Durante a criação de objetos, o contêiner injeta quaisquer dependências que requer que o objeto nele. Se essas dependências ainda não tem sido criadas, o contêiner cria e suas dependências é resolvido primeiro.

## <a name="communicating-between-loosely-coupled-componentscommunicating-between-loosely-coupled-componentsmd"></a>[Comunicação entre livremente acoplada componentes](communicating-between-loosely-coupled-components.md)

O xamarin. Forms [ `MessagingCenter` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MessagingCenter/) classe implementa a publicação-assinatura padrão, permitindo que a comunicação baseada em mensagens entre componentes inconvenientes vincular por referências de objeto e do tipo. Esse mecanismo permite Publicadores e assinantes para se comunicar sem ter uma referência uns aos outros, ajudando a reduzir as dependências entre componentes, permitindo também que os componentes ser desenvolvidos e testados independentemente.

## <a name="navigationnavigationmd"></a>[Navegação](navigation.md)

Xamarin. Forms inclui suporte para a navegação de página, que geralmente resulta da interação do usuário com a interface do usuário ou do aplicativo em si, como resultado das alterações de estado interno controlada por lógica. No entanto, a navegação pode ser complexa para implementar em aplicativos que usam o padrão MVVM.

Este capítulo apresenta um `NavigationService` classe, que é usada para executar a navegação de primeiro do modelo de exibição de modelos de exibição. Colocar a lógica de navegação no modo de exibição classes de modelo significa que a lógica pode ser desempenhada por meio de testes automatizados. Além disso, o modelo de exibição, em seguida, pode implementar a lógica para controlar a navegação para assegurar que certas regras de negócio são aplicadas.

## <a name="validationvalidationmd"></a>[Validação](validation.md)

Qualquer aplicativo que aceita a entrada de usuários deve garantir que a entrada é válida. Sem validação, um usuário pode fornecer dados que faz com que o aplicativo falhe. Validação impõe regras de negócio e impede que um invasor insira dados mal-intencionados.

No contexto do modelo do modelo-ViewModel (MVVM) padrão, um modelo de exibição ou modelo geralmente precisará executar a validação de dados e sinalizar erros de validação para o modo de exibição para que o usuário poderá corrigi-los.

## <a name="configuration-managementconfiguration-managementmd"></a>[Gerenciamento de configuração](configuration-management.md)

As configurações permitem a separação de dados que define o comportamento de um aplicativo de código, permitindo que o comportamento a ser alterado sem recriar o aplicativo. Configurações do aplicativo são dados que um aplicativo cria e gerencia e as configurações do usuário são as configurações personalizáveis de um aplicativo que afetam o comportamento do aplicativo e não requerem frequente ajuste novamente.

## <a name="containerized-microservicescontainerized-microservicesmd"></a>[Microservices em contêineres](containerized-microservices.md)

Microservices oferecem uma abordagem de desenvolvimento de aplicativo e implantação é adequado para os requisitos de agilidade, a escala e a confiabilidade dos aplicativos de nuvem modernos. Uma das principais vantagens de microservices é que elas podem ser expandidas independentemente, o que significa que uma área funcional específica pode ser dimensionada que que requer mais processamento de energia ou rede largura de banda para dar suporte a demanda, sem escala desnecessariamente áreas do o aplicativo que não estão enfrentando o aumento da demanda.

## <a name="authentication-and-authorizationauthentication-and-authorizationmd"></a>[Autenticação e autorização](authentication-and-authorization.md)

Há várias abordagens para a integração de autenticação e autorização em um aplicativo xamarin. Forms que se comunica com um aplicativo ASP.NET MVC. Aqui, autenticação e autorização são realizadas com um microsserviço de identidade em contêineres que usa 4 IdentityServer. IdentityServer é uma estrutura de OpenID Connect e OAuth 2.0 do código-fonte aberto para ASP.NET Core que se integra a identidade do ASP.NET Core para realizar a autenticação de token de portador.

## <a name="accessing-remote-dataaccessing-remote-datamd"></a>[Acesso a dados remotos](accessing-remote-data.md)

Muitas soluções baseadas na web modernas fazer uso dos serviços web, hospedadas por servidores web, para fornecer funcionalidade de cliente remoto a aplicativos. As operações que expõe um serviço web constituem uma API da web e aplicativos de cliente devem ser capazes de usar a API da web sem saber como os dados ou operações que expõe a API são implementadas.

## <a name="unit-testingunit-testingmd"></a>[Testes de Unidade](unit-testing.md)

Testando modelos e exibir modelos de aplicativos MVVM é idêntico de outras classes de teste e as mesmas ferramentas e técnicas podem ser usadas. No entanto, há alguns padrões comuns para o modelo e classes de modelo de exibição, que podem se beneficiar das técnicas de teste de unidade específica.

## <a name="feedback"></a>Comentários

Este projeto tem um site da comunidade, no qual você pode postar perguntas e fornecer comentários. O site da comunidade está localizado em [GitHub](https://github.com/dotnet-architecture/eShopOnContainers). Como alternativa, comentários sobre o eBook podem ser enviados por email para [ dotnet-architecture-ebooks-feedback@service.microsoft.com ](mailto:dotnet-architecture-ebooks-feedback@service.microsoft.com).


## <a name="related-links"></a>Links relacionados

- [Baixar eBook (2Mb PDF)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) (sample)](https://github.com/dotnet-architecture/eShopOnContainers)
