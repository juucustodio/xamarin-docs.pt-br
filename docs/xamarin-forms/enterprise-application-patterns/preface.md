---
title: Preceder para o desenvolvimento de aplicativos empresariais
description: Este capítulo fornece um prefácio para padrões de aplicativos empresariais usando o Xamarin. Forms.
ms.prod: xamarin
ms.assetid: fbf32a44-1d33-4e16-a904-dc7ee5991e7c
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
ms.openlocfilehash: 4ce04ec5216872cb56424e8847eec357a5b3ac0e
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70770751"
---
# <a name="preface-to-enterprise-app-development"></a>Preceder para o desenvolvimento de aplicativos empresariais

Este livro eletrônico fornece orientação sobre como criar aplicativos empresariais de plataforma cruzada usando o Xamarin. Forms. O Xamarin. Forms é um kit de ferramentas de interface do usuário de plataforma cruzada que permite que os desenvolvedores criem facilmente layouts de interface nativa de usuários que podem ser compartilhados entre plataformas, incluindo iOS, Android e o Plataforma Universal do Windows (UWP). Ele fornece uma solução abrangente para os aplicativos Business to Employee (B2E), Business to Business (B2B) e Business to Consumer (B2C), fornecendo a capacidade de compartilhar código em todas as plataformas de destino e ajudando a reduzir o TCO (custo total de propriedade).

O guia fornece diretrizes arquitetônicas para o desenvolvimento de aplicativos empresariais Xamarin. Forms adaptáveis e que podem ser testados. São fornecidas diretrizes sobre como implementar o MVVM, injeção de dependência, navegação, validação e gerenciamento de configuração, mantendo o acoplamento flexível. Além disso, também há orientações sobre como executar a autenticação e a autorização com o IdentityServer, acessar dados de microservices em contêineres e testes de unidade.

O guia é fornecido com o código-fonte para o [aplicativo móvel eShopOnContainers](https://github.com/dotnet-architecture/eShopOnContainers/tree/master/src/Mobile)e o código-fonte para o [aplicativo de referência eShopOnContainers](https://github.com/dotnet-architecture/eShopOnContainers). O aplicativo móvel eShopOnContainers é um aplicativo empresarial multiplataforma desenvolvido usando Xamarin. Forms, que se conecta a uma série de microservices em contêineres conhecida como o aplicativo de referência eShopOnContainers. No entanto, o aplicativo móvel eShopOnContainers pode ser configurado para consumir dados de serviços fictícios para aqueles que desejam evitar a implantação de microservices em contêineres.

## <a name="whats-left-out-of-this-guides-scope"></a>O que saiu do escopo deste guia

Este guia destina-se a leitores que já estão familiarizados com o Xamarin. Forms. Para obter uma introdução detalhada ao Xamarin. Forms, consulte a [documentação do xamarin. Forms](~/xamarin-forms/index.yml)e [criando aplicativos móveis com o Xamarin. Forms](https://aka.ms/xamebook).

O guia é complementar aos [microserviços do .net: Arquitetura para aplicativos](https://aka.ms/microservicesebook).net em contêineres, que se concentram no desenvolvimento e na implantação de microservices em contêineres. Outros guias que vale a pena ler incluem [a arquitetura e o desenvolvimento de aplicativos Web modernos com ASP.NET Core e Microsoft Azure](https://aka.ms/WebAppEbook), o [ciclo de vida do aplicativo Docker em contêineres com a plataforma e as ferramentas da Microsoft](https://aka.ms/dockerlifecycleebook)e a [plataforma Microsoft e Ferramentas para desenvolvimento de aplicativos móveis](https://aka.ms/MobAppDev/StndPDF).

## <a name="who-should-use-this-guide"></a>Quem deve usar este guia

O público-alvo deste guia é principalmente desenvolvedores e arquitetos que gostariam de aprender a arquitetar e implementar aplicativos empresariais de plataforma cruzada usando o Xamarin. Forms.

Um público-alvo secundário são tomadores de decisões técnicas que desejam receber uma visão geral da arquitetura e da tecnologia antes de decidir qual abordagem deve ser selecionada para o desenvolvimento de aplicativos empresariais entre plataformas usando Xamarin. Forms.

## <a name="how-to-use-this-guide"></a>Como usar este guia

Este guia se concentra na criação de aplicativos empresariais de plataforma cruzada usando o Xamarin. Forms. Como tal, ele deve ser lido em sua totalidade para fornecer uma base de compreensão desses aplicativos e suas considerações técnicas. O guia, junto com seu aplicativo de exemplo, também pode servir como um ponto de partida ou referência para a criação de um novo aplicativo empresarial. Use o aplicativo de exemplo associado como um modelo para o novo aplicativo ou para ver como organizar as partes de componente de um aplicativo. Em seguida, consulte este guia para obter diretrizes arquitetônicas.

Sinta-se à vontade para encaminhar este guia aos membros da equipe para ajudar a garantir uma compreensão comum do desenvolvimento de aplicativos empresariais entre plataformas usando o Xamarin. Forms. Ter todos trabalhando em um conjunto comum de terminologias e princípios básicos ajudará a garantir uma aplicação consistente de padrões e práticas de arquitetura.

## <a name="related-links"></a>Links relacionados

- [Download do eBook (PDF de 2Mb)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) (exemplo)](https://github.com/dotnet-architecture/eShopOnContainers)
