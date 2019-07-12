---
title: Prefácio para desenvolvimento de aplicativos empresariais
description: Este capítulo fornece um prefácio para padrões de aplicativo empresarial usando o xamarin. Forms.
ms.prod: xamarin
ms.assetid: fbf32a44-1d33-4e16-a904-dc7ee5991e7c
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
ms.openlocfilehash: 82f1455ff5e5ac06b50664e1d4d533d4964b7a0e
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67831124"
---
# <a name="preface-to-enterprise-app-development"></a>Prefácio para desenvolvimento de aplicativos empresariais

Este livro eletrônico fornece orientação sobre a criação de aplicativos empresariais de plataforma cruzada usando xamarin. Forms. Xamarin. Forms é um kit de ferramentas de interface do usuário de plataforma cruzada que permite aos desenvolvedores criar facilmente usuário nativo layouts de interface que podem ser compartilhados entre plataformas, incluindo iOS, Android e Universal Windows Platform (UWP). Ele fornece uma solução abrangente para a empresa ao funcionário (B2E), empresas (B2B) e Business para aplicativos de consumidor (B2C), fornecendo a capacidade de compartilhar código entre todas as plataformas de destino e ajudando a reduzir o custo total de propriedade (TCO).

O guia fornece diretrizes de arquitetura para o desenvolvimento de aplicativos empresariais de xamarin. Forms testáveis adaptáveis e sustentáveis. São fornecidas orientações sobre como implementar o MVVM, injeção de dependência, navegação, validação e gerenciamento de configuração, mantendo um acoplamento flexível. Além disso, há também orientações sobre como executar autenticação e autorização com IdentityServer, acessando dados através de microsserviços em contêineres e testes de unidade.

O guia é fornecido com o código-fonte para o [aplicativo móvel do eShopOnContainers](https://github.com/dotnet-architecture/eShopOnContainers/tree/master/src/Mobile)e o código-fonte do [aplicativo de referência eShopOnContainers](https://github.com/dotnet-architecture/eShopOnContainers). O aplicativo móvel do eShopOnContainers é um aplicativo de plataforma cruzada enterprise desenvolvido usando o xamarin. Forms, que se conecta a uma série de microsserviços em contêineres, conhecidos como aplicativo de referência eShopOnContainers. No entanto, o aplicativo móvel do eShopOnContainers pode ser configurado para consumir dados de serviços fictícios para aqueles que desejam evitar a implantação de microsserviços em contêineres.

## <a name="whats-left-out-of-this-guides-scope"></a>O que fica fora do escopo deste guia

Este guia destina-se a leitores que já estão familiarizados com o xamarin. Forms. Para obter uma introdução detalhada ao xamarin. Forms, consulte a [documentação do xamarin. Forms](~/xamarin-forms/index.yml), e [criação de aplicativos móveis com xamarin. Forms](https://aka.ms/xamebook).

O guia é um complemento [Microsserviços do .NET: Arquitetura para aplicativos .NET em contêineres](https://aka.ms/microservicesebook), que se concentra no desenvolvimento e implantação de microsserviços em contêineres. Outros guias que vale a pena ler incluem [projetar e desenvolver aplicativos Web modernos com o ASP.NET Core e Microsoft Azure](https://aka.ms/WebAppEbook), [em contêineres Docker ciclo de vida do Microsoft Platform e ferramentas](https://aka.ms/dockerlifecycleebook), e [plataforma e ferramentas para desenvolvimento de aplicativos móveis Microsoft](https://aka.ms/MobAppDev/StndPDF).

## <a name="who-should-use-this-guide"></a>Quem deve usar este guia

O público-alvo deste guia é principalmente os desenvolvedores e arquitetos que gostaria de saber como arquitetar e implementar aplicativos empresariais de plataforma cruzada usando xamarin. Forms.

Um público-alvo secundário é tomadores de decisões técnicas que gostariam de receber uma visão geral de arquitetura e tecnologia antes de decidir sobre qual abordagem selecionar para desenvolvimento de aplicativos empresariais de plataforma cruzada usando xamarin. Forms.

## <a name="how-to-use-this-guide"></a>Como usar este guia

Este guia se concentra na criação de aplicativos empresariais de plataforma cruzada usando xamarin. Forms. Como tal, ele deve ser lido em sua totalidade para fornecer uma base de Noções básicas sobre esses aplicativos e suas considerações técnicas. O guia, junto com seu aplicativo de exemplo, também pode servir como um ponto de partida ou a referência para criar um novo aplicativo enterprise. Use o aplicativo de exemplo associado como um modelo para o novo aplicativo ou para ver como organizar as partes do componente do aplicativo. Em seguida, voltar para este guia para diretrizes de arquitetura.

Fique à vontade para encaminhar este guia para os membros da equipe para ajudar a garantir um entendimento comum de desenvolvimento de aplicativos empresariais de plataforma cruzada usando xamarin. Forms. Ter todas as pessoas trabalhando em um conjunto comum de terminologias e princípios subjacentes ajudará a garantir uma aplicação consistente dos padrões de arquitetura e práticas recomendadas.


## <a name="related-links"></a>Links relacionados

- [Baixe o livro eletrônico (PDF de 2Mb)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) (amostra)](https://github.com/dotnet-architecture/eShopOnContainers)
