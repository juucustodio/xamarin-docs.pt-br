---
title: Padrões de aplicativos empresariais usando o Xamarin.Forms ebook
description: Este livro eletrônico fornece diretrizes arquitetônicas para o desenvolvimento de aplicativos empresariais adaptáveis, que podem ser mantidos e que podem ser testados Xamarin.Forms .
ms.prod: xamarin
ms.assetid: 28cfed6c-6175-4223-a8cc-798d40bf0832
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 1b8d33ba98dfeda8db23e516a989ebbfe1db9a66
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93372997"
---
# <a name="enterprise-application-patterns-using-no-locxamarinforms-ebook"></a>Padrões de aplicativos empresariais usando o Xamarin.Forms ebook

_Diretrizes arquitetônicas para o desenvolvimento de aplicativos empresariais adaptáveis, que podem ser mantidos e que podem ser testados Xamarin.Forms_

![Padrões de aplicativos empresariais usando::: no-Loc (Xamarin. Forms)::: eBook](images/cover-sml.png)

> [!NOTE]
> Este livro eletrônico foi publicado na Primavera de 2017 e não foi atualizado desde então. Há muito no livro que permanece valioso, mas parte do material está desatualizada.

Este eBook fornece orientação sobre como implementar o padrão MVVM (Model-View-ViewModel), injeção de dependência, navegação, validação e gerenciamento de configuração, mantendo o acoplamento flexível. Além disso, também há orientações sobre como executar a autenticação e a autorização com o IdentityServer, acessar dados de microservices em contêineres e testes de unidade.

## <a name="preface"></a>[Prefácio](preface.md)

Este capítulo explica a finalidade e o escopo do guia e a quem ele é destinado.

## <a name="introduction"></a>[Introdução](introduction.md)

Os desenvolvedores de aplicativos empresariais enfrentam vários desafios que podem alterar a arquitetura do aplicativo durante o desenvolvimento. Portanto, é importante criar um aplicativo para que ele possa ser modificado ou estendido ao longo do tempo. A criação de tal adaptabilidade pode ser difícil, mas normalmente envolve o particionamento de um aplicativo em componentes discretos e livremente acoplados que podem ser facilmente integrados em um aplicativo.

## <a name="mvvm"></a>[MVVM](mvvm.md)

O padrão MVVM (Model-View-ViewModel) ajuda a separar corretamente a lógica de negócios e de apresentação de um aplicativo da interface do usuário. Manter uma separação clara entre a lógica do aplicativo e a interface do usuário ajuda a resolver inúmeros problemas de desenvolvimento e pode facilitar o teste, a manutenção e a evolução de um aplicativo. Ele também pode melhorar consideravelmente as oportunidades de reutilização de código e permite que desenvolvedores e designers de interface do usuário colaborem mais facilmente ao desenvolver suas respectivas partes de um aplicativo.

## <a name="dependency-injection"></a>[Injeção de dependência](dependency-injection.md)

A injeção de dependência permite o desacoplamento de tipos concretos do código que depende desses tipos. Normalmente, ele usa um contêiner que contém uma lista de registros e mapeamentos entre interfaces e tipos abstratos e os tipos concretos que implementam ou estendem esses tipos.

Os contêineres de injeção de dependência reduzem o acoplamento entre objetos, fornecendo um recurso para instanciar instâncias de classe e gerenciar sua vida útil com base na configuração do contêiner. Durante a criação de objetos, o contêiner injeta todas as dependências que o objeto exige. Se essas dependências ainda não tiverem sido criadas, o contêiner criará e resolverá suas dependências primeiro.

## <a name="communicating-between-loosely-coupled-components"></a>[Comunicação entre componentes flexíveis](communicating-between-loosely-coupled-components.md)

A Xamarin.Forms [`MessagingCenter`](xref:Xamarin.Forms.MessagingCenter) classe implementa o padrão de publicação-assinatura, permitindo a comunicação baseada em mensagem entre componentes que são inconvenientes de vincular por referências de objeto e tipo. Esse mecanismo permite que publicadores e assinantes se comuniquem sem ter uma referência entre si, ajudando a reduzir as dependências entre os componentes, permitindo também que os componentes sejam desenvolvidos e testados de forma independente.

## <a name="navigation"></a>[Navegação](navigation.md)

Xamarin.Forms inclui suporte para a navegação de página, que normalmente resulta da interação do usuário com a interface do usuário ou do próprio aplicativo, como resultado de alterações de estado controladas pela lógica interna. No entanto, a navegação pode ser complexa para ser implementada em aplicativos que usam o padrão MVVM.

Este capítulo apresenta uma `NavigationService` classe, que é usada para executar o modelo de exibição-primeira navegação de modelos de exibição. Colocar a lógica de navegação em classes de modelo de exibição significa que a lógica pode ser exercitada por meio de testes automatizados. Além disso, o modelo de exibição pode então implementar a lógica para controlar a navegação a fim de garantir que determinadas regras de negócio sejam impostas.

## <a name="validation"></a>[Validação](validation.md)

Qualquer aplicativo que aceite entrada de usuários deve garantir que a entrada seja válida. Sem a validação, um usuário pode fornecer dados que causam falha no aplicativo. A validação impõe regras de negócio e impede que um invasor insira dados mal-intencionados.

No contexto do padrão Model-View-ViewModel (MVVM), um modelo de exibição ou modelo geralmente será necessário para executar a validação de dados e sinalizar quaisquer erros de validação para o modo de exibição para que o usuário possa corrigi-los.

## <a name="configuration-management"></a>[Gerenciamento de configuração](configuration-management.md)

As configurações permitem a separação de dados que configuram o comportamento de um aplicativo do código, permitindo que o comportamento seja alterado sem recriar o aplicativo. As configurações do aplicativo são dados que um aplicativo cria e gerencia, e as configurações de usuário são as configurações personalizáveis de um aplicativo que afetam o comportamento do aplicativo e não exigem um reajuste frequente.

## <a name="containerized-microservices"></a>[Microservices em contêineres](containerized-microservices.md)

Os microserviços oferecem uma abordagem ao desenvolvimento e à implantação de aplicativos adequados para os requisitos de agilidade, escala e confiabilidade dos aplicativos de nuvem modernos. Uma das principais vantagens dos microserviços é que eles podem ser expandidos de forma independente, o que significa que uma área funcional específica pode ser dimensionada para exigir mais capacidade de processamento ou largura de banda de rede para dar suporte à demanda, sem áreas de dimensionamento desnecessariamente do aplicativo que não estão sofrendo maior demanda.

## <a name="authentication-and-authorization"></a>[Autenticação e autorização](authentication-and-authorization.md)

Há muitas abordagens para integrar a autenticação e a autorização em um Xamarin.Forms aplicativo que se comunica com um aplicativo web ASP.NET MVC. Aqui, a autenticação e a autorização são executadas com um microserviço de identidade em contêiner que usa o IdentityServer 4. IdentityServer é uma estrutura de software livre do OpenID Connect e do OAuth 2,0 para ASP.NET Core que se integra com a identidade ASP.NET Core para executar a autenticação de token de portador.

## <a name="accessing-remote-data"></a>[Acesso a dados remotos](accessing-remote-data.md)

Muitas soluções modernas baseadas na Web fazem uso de serviços Web, hospedados por servidores Web, para fornecer funcionalidade para aplicativos cliente remotos. As operações que um serviço Web expõe constituem uma API Web, e os aplicativos cliente devem ser capazes de utilizar a API Web sem saber como os dados ou as operações que a API expõe são implementados.

## <a name="unit-testing"></a>[Testes de unidade](unit-testing.md)

Os modelos de teste e os modelos de exibição dos aplicativos MVVM são idênticos ao teste de todas as outras classes, e as mesmas ferramentas e técnicas podem ser usadas. No entanto, há alguns padrões típicos para modelar e exibir classes de modelo, que podem se beneficiar de técnicas de teste de unidade específicas.

## <a name="community-site"></a>Site da Comunidade

Este projeto tem um site de comunidade, no qual você pode postar perguntas e fornecer comentários. O site da Comunidade está localizado no [GitHub](https://github.com/dotnet-architecture/eShopOnContainers). Como alternativa, comentários sobre o eBook podem ser enviados por email [dotnet-architecture-ebooks-feedback@service.microsoft.com](mailto:dotnet-architecture-ebooks-feedback@service.microsoft.com) .

## <a name="related-links"></a>Links Relacionados

- [Download do eBook (PDF de 2Mb)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) (exemplo)](https://github.com/dotnet-architecture/eShopOnContainers)
