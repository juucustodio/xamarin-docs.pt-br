---
title: ''
description: Este capítulo fornece um prefácio para padrões de aplicativos empresariais usando o Xamarin.Forms .
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: b587bd8cacda9024103757a585c11fba2ed09fda
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/28/2020
ms.locfileid: "84130826"
---
# <a name="preface-to-enterprise-app-development"></a>Preceder para o desenvolvimento de aplicativos empresariais

Este livro eletrônico fornece orientação sobre a criação de aplicativos empresariais de plataforma cruzada usando o Xamarin.Forms . Xamarin.Formso é um kit de ferramentas de interface do usuário de plataforma cruzada que permite aos desenvolvedores criar facilmente layouts de interface nativa de usuários que podem ser compartilhados entre plataformas, incluindo iOS, Android e o Plataforma Universal do Windows (UWP). Ele fornece uma solução abrangente para os aplicativos Business to Employee (B2E), Business to Business (B2B) e Business to Consumer (B2C), fornecendo a capacidade de compartilhar código em todas as plataformas de destino e ajudando a reduzir o TCO (custo total de propriedade).

O guia fornece diretrizes arquitetônicas para o desenvolvimento de aplicativos empresariais adaptáveis, que podem ser mantidos e que podem ser testados Xamarin.Forms . São fornecidas diretrizes sobre como implementar o MVVM, injeção de dependência, navegação, validação e gerenciamento de configuração, mantendo o acoplamento flexível. Além disso, também há orientações sobre como executar a autenticação e a autorização com o IdentityServer, acessar dados de microservices em contêineres e testes de unidade.

O guia é fornecido com o código-fonte para o [aplicativo móvel eShopOnContainers](https://github.com/dotnet-architecture/eShopOnContainers/tree/master/src/Mobile)e o código-fonte para o [aplicativo de referência eShopOnContainers](https://github.com/dotnet-architecture/eShopOnContainers). O aplicativo móvel eShopOnContainers é um aplicativo empresarial multiplataforma desenvolvido usando Xamarin.Forms o, que se conecta a uma série de microserviço em contêineres conhecido como o aplicativo de referência eShopOnContainers. No entanto, o aplicativo móvel eShopOnContainers pode ser configurado para consumir dados de serviços fictícios para aqueles que desejam evitar a implantação de microservices em contêineres.

## <a name="whats-left-out-of-this-guides-scope"></a>O que saiu do escopo deste guia

Este guia destina-se a leitores que já estão familiarizados com o Xamarin.Forms . Para obter uma introdução detalhada Xamarin.Forms do, consulte a [ Xamarin.Forms documentação](~/xamarin-forms/index.yml)e [criando aplicativos móveis com Xamarin.Forms ](https://aka.ms/xamebook)o.

O guia é complementar aos [microserviços .net: arquitetura para aplicativos .net em contêineres](https://aka.ms/microservicesebook), que se concentra no desenvolvimento e na implantação de microservices em contêineres. Outros guias que vale a pena ler incluem [a arquitetura e o desenvolvimento de aplicativos Web modernos com ASP.NET Core e Microsoft Azure](https://aka.ms/WebAppEbook), o [ciclo de vida do aplicativo Docker em contêineres com a plataforma e as ferramentas da Microsoft](https://aka.ms/dockerlifecycleebook)e a [plataforma e ferramentas da Microsoft para o desenvolvimento de aplicativos móveis](https://aka.ms/MobAppDev/StndPDF).

## <a name="who-should-use-this-guide"></a>Quem deve usar este guia

O público-alvo deste guia é principalmente desenvolvedores e arquitetos que gostariam de aprender a arquitetar e implementar aplicativos empresariais de plataforma cruzada usando o Xamarin.Forms .

Um público-alvo secundário são os tomadores de decisões técnicas que desejam receber uma visão geral da arquitetura e da tecnologia antes de decidir qual abordagem deve ser selecionada para o desenvolvimento de aplicativos empresariais de plataforma cruzada usando o Xamarin.Forms .

## <a name="how-to-use-this-guide"></a>Como usar este guia

Este guia se concentra na criação de aplicativos empresariais de plataforma cruzada usando o Xamarin.Forms . Como tal, ele deve ser lido em sua totalidade para fornecer uma base de compreensão desses aplicativos e suas considerações técnicas. O guia, junto com seu aplicativo de exemplo, também pode servir como um ponto de partida ou referência para a criação de um novo aplicativo empresarial. Use o aplicativo de exemplo associado como um modelo para o novo aplicativo ou para ver como organizar as partes de componente de um aplicativo. Em seguida, consulte este guia para obter diretrizes arquitetônicas.

Sinta-se à vontade para encaminhar este guia aos membros da equipe para ajudar a garantir uma compreensão comum do desenvolvimento de aplicativos empresariais entre plataformas usando o Xamarin.Forms . Ter todos trabalhando em um conjunto comum de terminologias e princípios básicos ajudará a garantir uma aplicação consistente de padrões e práticas de arquitetura.

## <a name="related-links"></a>Links relacionados

- [Download do eBook (PDF de 2Mb)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) (exemplo)](https://github.com/dotnet-architecture/eShopOnContainers)
