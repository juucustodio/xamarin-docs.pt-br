---
title: Consumir serviços da Web
description: Este guia demonstra como se comunicar com serviços web diferentes para fornecer criar, ler, atualizar e excluir (CRUD) funcionalidade a um aplicativo xamarin. Forms. Os tópicos abordados estão se comunicando ASMX serviços WCF serviços, REST dos serviços, os aplicativos móveis do Azure e Amazon Web Services.
ms.prod: xamarin
ms.assetid: 8B360BDA-E4E3-4A3F-9004-0E35362F49F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/20/2016
ms.openlocfilehash: 530b57c009a1f76d3756d7315856f74b6cda2f66
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="consuming-web-services"></a>Consumir serviços da Web

_Este guia demonstra como se comunicar com serviços web diferentes para fornecer criar, ler, atualizar e excluir (CRUD) funcionalidade a um aplicativo xamarin. Forms. Os tópicos abordados estão se comunicando ASMX serviços WCF serviços, REST dos serviços, os aplicativos móveis do Azure e Amazon Web Services._

## <a name="consuming-an-aspnet-web-service-asmxxamarin-formsdata-cloudconsumingasmxmd"></a>[Consumir um serviço Web do ASP.NET (ASMX)](~/xamarin-forms/data-cloud/consuming/asmx.md)

Serviços Web do ASP.NET (ASMX) fornecem a capacidade de criar serviços web que enviam mensagens via HTTP usando o protocolo de acesso a objeto simples (SOAP). SOAP é um protocolo independente de plataforma e independente de linguagem para criar e acessar serviços da web. Não é necessário que os consumidores de um serviço ASMX sabe nada sobre a plataforma, um modelo de objeto ou uma linguagem de programação usada para implementar o serviço. Eles só precisam entender como enviar e receber mensagens SOAP. Este artigo demonstra como consumir um serviço da web ASMX de um aplicativo xamarin. Forms.

## <a name="consuming-a-windows-communication-foundation-wcf-web-servicexamarin-formsdata-cloudconsumingwcfmd"></a>[Consumir um serviço Web do Windows Communication Foundation (WCF)](~/xamarin-forms/data-cloud/consuming/wcf.md)

O WCF é a estrutura unificada da Microsoft para criar aplicativos orientados a serviços. Ele permite aos desenvolvedores compilar aplicativos distribuídos seguros, confiáveis, transacionados e interoperáveis. Há diferenças entre os serviços de Web do ASP.NET (ASMX) e WCF, mas é importante entender que o WCF suporta as mesmas capacidades ASMX fornece — mensagens SOAP sobre HTTP. Este artigo demonstra como consumir um serviço WCF SOAP de um aplicativo xamarin. Forms.

## <a name="consuming-a-restful-web-servicexamarin-formsdata-cloudconsumingrestmd"></a>[Consumir um serviço Web RESTful](~/xamarin-forms/data-cloud/consuming/rest.md)

REST Representational State Transfer () é um estilo de arquitetura para a criação de serviços web. Solicitações REST são feitas via HTTP usando os mesmos verbos HTTP que navegadores da web usam para recuperar as páginas da web e para enviar dados para servidores. Este artigo demonstra como consumir um serviço web RESTful a partir de um aplicativo xamarin. Forms.

## <a name="consuming-an-azure-mobile-appxamarin-formsdata-cloudconsumingazuremd"></a>[Consumo de um aplicativo móvel do Azure](~/xamarin-forms/data-cloud/consuming/azure.md)

Aplicativos móveis do Azure permitem que você desenvolva aplicativos com o back-ends escalonável hospedado no serviço de aplicativo do Azure, com suporte para autenticação móvel, sincronização offline e notificações por push. Neste artigo, que só é aplicável a aplicativos móveis do Azure que usam um back-end node. js, explica como consultar, inserir, atualizar e excluir dados armazenados em uma tabela em uma instância dos aplicativos móveis do Azure.

## <a name="consuming-an-amazon-simpledb-servicexamarin-formsdata-cloudconsumingawsmd"></a>[Consumir um serviço de SimpleDB Amazon](~/xamarin-forms/data-cloud/consuming/aws.md)

Amazon SimpleDB é um serviço web que fornece a capacidade de armazenar e consultar dados na nuvem da Amazon. Este artigo explica como usar o AWS SDK para .NET para consultar, criar e substitua e excluir dados armazenados em um serviço SimpleDB.


## <a name="related-links"></a>Links relacionados

- [Introdução aos serviços Web](~/cross-platform/data-cloud/web-services/index.md)
- [Visão geral do suporte assíncrono](~/cross-platform/platform/async.md)
