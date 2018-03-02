---
title: "Prefácio"
ms.topic: article
ms.prod: xamarin
ms.assetid: fbf32a44-1d33-4e16-a904-dc7ee5991e7c
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
ms.openlocfilehash: 5e10c331ef19efe04eab5e59702db07078b0624a
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/28/2018
---
# <a name="preface"></a>Prefácio

Este livro eletrônico fornece orientação sobre a criação de aplicativos de empresa de plataforma cruzada usando xamarin. Forms. Xamarin. Forms é um kit de ferramentas de interface do usuário de plataforma cruzada que permite aos desenvolvedores criar facilmente usuário nativo layouts de interface que podem ser compartilhados entre plataformas, incluindo o Windows UWP (plataforma Universal), iOS e Android. Ele fornece uma solução abrangente para os negócios ao funcionário (B2E), empresas (B2B) e empresas para aplicativos de consumidor (B2C), fornecendo a capacidade de compartilhar o código em todas as plataformas de destino e ajudando a reduzir o custo total de propriedade (TCO).

O guia fornece as diretrizes de arquitetura para o desenvolvimento de aplicativos de empresa xamarin. Forms testáveis adaptáveis e sustentáveis. São fornecidas orientações sobre como implementar MVVM, injeção de dependência, navegação, validação e gerenciamento de configuração, mantendo um acoplamento flexível. Além disso, há também orientações sobre como executar a autenticação e autorização com IdentityServer, acesso a dados de microservices em contêineres e testes de unidade.

O guia é fornecido com o código-fonte para o [aplicativo móvel eShopOnContainers](https://github.com/dotnet-architecture/eShopOnContainers/tree/master/src/Mobile)e código de origem a [eShopOnContainers Referência aplicativo](https://github.com/dotnet-architecture/eShopOnContainers). O aplicativo móvel eShopOnContainers é um aplicativo de empresa de plataforma cruzada desenvolvido usando xamarin. Forms, que se conecta a uma série de microservices em contêineres conhecidos como os eShopOnContainers Referência aplicativo. No entanto, o aplicativo móvel eShopOnContainers pode ser configurado para consumir dados de serviços de simulação para quem deseja evitar a distribuição de microservices em contêineres.

## <a name="whats-left-out-of-this-guides-scope"></a>O que fica fora do escopo deste guia

Este guia destina-se a leitores que já estão familiarizados com o xamarin. Forms. Para obter uma introdução detalhada xamarin. Forms, consulte o [xamarin. Forms documentação](~/xamarin-forms/index.yml), e [criação de aplicativos móveis com o xamarin. Forms](https://aka.ms/xamebook).

O guia é um complemento [Microservices .NET: arquitetura de aplicativos do .NET em contêineres](https://aka.ms/microservicesebook), que se concentra no desenvolvimento e implantação microservices em contêineres. Incluem outros guias vale a pena leitura [projetar e desenvolver aplicativos de Web modernos com ASP.NET Core e Microsoft Azure](http://aka.ms/WebAppEbook), [em contêineres Docker ciclo de vida do Microsoft Platform e ferramentas](http://aka.ms/dockerlifecycleebook), e [Microsoft Platform e ferramentas para desenvolvimento de aplicativos móveis](http://aka.ms/MobAppDev/StndPDF).

## <a name="who-should-use-this-guide"></a>Quem deve usar este guia

Este guia destina principalmente os desenvolvedores e arquitetos que gostariam de saber como projetar e implementar aplicativos de plataforma cruzada enterprise usando xamarin. Forms.

Um público-alvo secundário é tomadores de decisões técnicas que gostariam de receber uma visão geral de tecnologia e arquitetura antes de decidir qual abordagem para selecionar para desenvolvimento de aplicativos corporativos de plataforma cruzada com xamarin. Forms.

## <a name="how-to-use-this-guide"></a>Como usar este guia

Este guia se concentra na criação de aplicativos de empresa de plataforma cruzada usando xamarin. Forms. Como tal, ele deve ser lida em sua totalidade para fornecer uma base de compreender esses aplicativos e seus considerações técnicas. O guia, junto com seu aplicativo de exemplo, também poderá servir como um ponto inicial ou uma referência para a criação de um novo aplicativo empresarial. Use o aplicativo de exemplo associados como um modelo para o novo aplicativo, ou para ver como organizar as partes do componente do aplicativo. Em seguida, consulte este guia para obter diretrizes de arquitetura.

Fique à vontade para encaminhar este guia membros da equipe para ajudar a garantir um entendimento comum de desenvolvimento de aplicativos de plataforma cruzada enterprise usando xamarin. Forms. Com todas as pessoas trabalhando em um conjunto comum de terminologias e princípios de base ajuda a garantir uma aplicação consistente de padrões de arquitetura e práticas recomendadas.


## <a name="related-links"></a>Links relacionados

- [Baixar eBook (2Mb PDF)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) (sample)](https://github.com/dotnet-architecture/eShopOnContainers)
