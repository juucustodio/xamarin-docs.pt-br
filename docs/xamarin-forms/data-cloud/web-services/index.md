---
title: Xamarin. Forms e serviços da Web
description: Este guia explica como se comunicar com serviços da web diferente para fornecer criar, ler, atualizar e excluir (CRUD) funcionalidade a um aplicativo xamarin. Forms. Os tópicos abordados incluem a comunicação com serviços ASMX, WCF services, serviços REST.
ms.prod: xamarin
ms.assetid: 8B360BDA-E4E3-4A3F-9004-0E35362F49F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/27/2019
ms.openlocfilehash: 1cf2714191528c5619b4f877bcb43e80464c44d1
ms.sourcegitcommit: c1d85b2c62ad84c22bdee37874ad30128581bca6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/08/2019
ms.locfileid: "67659003"
---
# <a name="xamarinforms-and-web-services"></a>Xamarin. Forms e serviços da Web

## <a name="introductionintroductionmd"></a>[Introdução](introduction.md)

Este artigo fornece um passo a passo do aplicativo de exemplo do xamarin. Forms que demonstra como se comunicar com serviços web diferentes. Os tópicos abordados incluem a anatomia de um aplicativo, as páginas, o modelo de dados e chamar operações de serviço web.

## <a name="consume-an-aspnet-web-service-asmxxamarin-formsdata-cloudweb-servicesasmxmd"></a>[Consumir um serviço Web do ASP.NET (ASMX)](~/xamarin-forms/data-cloud/web-services/asmx.md)

Serviços Web do ASP.NET (ASMX) fornecem a capacidade de criar serviços web que enviam mensagens via HTTP usando o protocolo de acesso a objeto simples (SOAP). SOAP é um protocolo independente de plataforma e linguagem para criar e acessar serviços web. Os consumidores de um serviço ASMX não precisam saber nada sobre a plataforma, um modelo de objeto ou uma linguagem de programação usada para implementar o serviço. Eles só precisam entender como enviar e receber mensagens SOAP. Este artigo demonstra como consumir um serviço da web ASMX de um aplicativo xamarin. Forms.

## <a name="consume-a-windows-communication-foundation-wcf-web-servicexamarin-formsdata-cloudweb-serviceswcfmd"></a>[Consumir um serviço Web do Windows Communication Foundation (WCF)](~/xamarin-forms/data-cloud/web-services/wcf.md)

O WCF é a estrutura unificada da Microsoft para a criação de aplicativos orientados a serviço. Ele permite aos desenvolvedores compilar aplicativos distribuídos seguros, confiáveis, transacionados e interoperáveis. Há diferenças entre os serviços de Web do ASP.NET (ASMX) e WCF, mas é importante entender que o WCF oferece suporte os mesmos recursos que fornece ASMX – mensagens SOAP sobre HTTP. Este artigo demonstra como consumir um serviço WCF SOAP de um aplicativo xamarin. Forms.

## <a name="consume-a-restful-web-servicexamarin-formsdata-cloudweb-servicesrestmd"></a>[Consumir um serviço Web RESTful](~/xamarin-forms/data-cloud/web-services/rest.md)

REST Representational State Transfer () é um estilo de arquitetura para a criação de serviços da web. Solicitações REST são feitas por HTTP, usando os mesmos verbos HTTP que navegadores da web usam para recuperar as páginas da web e para enviar dados para servidores. Este artigo demonstra como consumir um serviço web RESTful em um aplicativo xamarin. Forms.
