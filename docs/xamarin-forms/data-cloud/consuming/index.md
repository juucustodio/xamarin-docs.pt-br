---
title: Consumo de serviços Web
description: Este guia demonstra como se comunicar com serviços da web diferente para fornecer criar, ler, atualizar e excluir (CRUD) funcionalidade a um aplicativo xamarin. Forms. Os tópicos abordados incluem a comunicação com serviços ASMX, WCF services, serviços REST e aplicativos móveis do Azure.
ms.prod: xamarin
ms.assetid: 8B360BDA-E4E3-4A3F-9004-0E35362F49F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/20/2016
ms.openlocfilehash: 163b82590e95a1587836502883cd5f9f2afbe19c
ms.sourcegitcommit: 6e04246207aa743820029e8c217a43cfdd24f991
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/25/2019
ms.locfileid: "67352119"
---
# <a name="consuming-web-services"></a>Consumo de serviços Web

_Este guia demonstra como se comunicar com serviços da web diferente para fornecer criar, ler, atualizar e excluir (CRUD) funcionalidade a um aplicativo xamarin. Forms. Os tópicos abordados incluem a comunicação com serviços ASMX, WCF services, serviços REST e aplicativos móveis do Azure._

## <a name="consuming-an-aspnet-web-service-asmxxamarin-formsdata-cloudconsumingasmxmd"></a>[Consumir um serviço Web ASP.NET (ASMX)](~/xamarin-forms/data-cloud/consuming/asmx.md)

Serviços Web do ASP.NET (ASMX) fornecem a capacidade de criar serviços web que enviam mensagens via HTTP usando o protocolo de acesso a objeto simples (SOAP). SOAP é um protocolo independente de plataforma e linguagem para criar e acessar serviços web. Os consumidores de um serviço ASMX não precisam saber nada sobre a plataforma, um modelo de objeto ou uma linguagem de programação usada para implementar o serviço. Eles só precisam entender como enviar e receber mensagens SOAP. Este artigo demonstra como consumir um serviço da web ASMX de um aplicativo xamarin. Forms.

## <a name="consuming-a-windows-communication-foundation-wcf-web-servicexamarin-formsdata-cloudconsumingwcfmd"></a>[Consumir um serviço Web do Windows Communication Foundation (WCF)](~/xamarin-forms/data-cloud/consuming/wcf.md)

O WCF é a estrutura unificada da Microsoft para a criação de aplicativos orientados a serviço. Ele permite aos desenvolvedores compilar aplicativos distribuídos seguros, confiáveis, transacionados e interoperáveis. Há diferenças entre os serviços de Web do ASP.NET (ASMX) e WCF, mas é importante entender que o WCF oferece suporte os mesmos recursos que fornece ASMX – mensagens SOAP sobre HTTP. Este artigo demonstra como consumir um serviço WCF SOAP de um aplicativo xamarin. Forms.

## <a name="consuming-a-restful-web-servicexamarin-formsdata-cloudconsumingrestmd"></a>[Consumir um serviço Web RESTful](~/xamarin-forms/data-cloud/consuming/rest.md)

REST Representational State Transfer () é um estilo de arquitetura para a criação de serviços da web. Solicitações REST são feitas por HTTP, usando os mesmos verbos HTTP que navegadores da web usam para recuperar as páginas da web e para enviar dados para servidores. Este artigo demonstra como consumir um serviço web RESTful em um aplicativo xamarin. Forms.

## <a name="consuming-an-azure-mobile-appxamarin-formsdata-cloudconsumingazuremd"></a>[Consumir um aplicativo móvel do Azure](~/xamarin-forms/data-cloud/consuming/azure.md)

Aplicativos móveis do Azure permitem que você desenvolva aplicativos com back-ends dimensionável hospedado no serviço de aplicativo do Azure com suporte para autenticação móvel, sincronização offline e notificações por push. Neste artigo, que só é aplicável aos aplicativos móveis do Azure que usam um back-end do Node. js, explica como consultar, inserir, atualizar e excluir os dados armazenados em uma tabela em uma instância de aplicativos móveis do Azure.

## <a name="related-links"></a>Links relacionados

- [Introdução aos serviços Web](~/cross-platform/data-cloud/web-services/index.md)
- [Visão geral do suporte assíncrono](~/cross-platform/platform/async.md)
