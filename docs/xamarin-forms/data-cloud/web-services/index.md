---
title: Xamarin.Forms e Web Services
description: Este guia explica como se comunicar com diferentes serviços da Web para fornecer a funcionalidade de criação, leitura, atualização e exclusão (CRUD) a um aplicativo Xamarin.Forms. Os tópicos abordados incluem a comunicação com os serviços ASMX, serviços WCF, serviços REST.
ms.prod: xamarin
ms.assetid: 8B360BDA-E4E3-4A3F-9004-0E35362F49F8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/27/2019
ms.openlocfilehash: 799a0a97f7c1a212b10dc62f8b3e7bd2cf2060c8
ms.sourcegitcommit: bf3b5925018e1bd6a00505e9f37500761b66809d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2020
ms.locfileid: "80395450"
---
# <a name="xamarinforms-and-web-services"></a>Xamarin.Forms e Web Services

## <a name="introduction"></a>[Introdução](introduction.md)

Este artigo fornece um passo a passo do aplicativo de exemplo Xamarin.Forms que demonstra como se comunicar com diferentes serviços web. Os tópicos abordados incluem a anatomia do aplicativo, as páginas, o modelo de dados e a invocação de operações de serviços web.

## <a name="consume-an-aspnet-web-service-asmx"></a>[Consumir um Serviço Web ASP.NET (ASMX)](~/xamarin-forms/data-cloud/web-services/asmx.md)

ASP.NET Web Services (ASMX) fornecem a capacidade de criar serviços web que enviam mensagens através de HTTP usando o Simple Object Access Protocol (SOAP). Soap é um protocolo independente de plataforma e independente de linguagem para construir e acessar serviços web. Os consumidores de um serviço ASMX não precisam saber nada sobre a plataforma, modelo de objeto ou linguagem de programação usada para implementar o serviço. Eles só precisam entender como enviar e receber mensagens SOAP. Este artigo demonstra como consumir um serviço web ASMX a partir de um aplicativo Xamarin.Forms.

## <a name="consume-a-windows-communication-foundation-wcf-web-service"></a>[Consumir um Serviço Web da Windows Communication Foundation (WCF)](~/xamarin-forms/data-cloud/web-services/wcf.md)

O WCF é a estrutura unificada da Microsoft para a construção de aplicativos orientados a serviços. Ele permite que os desenvolvedores construam aplicativos distribuídos seguros, confiáveis, transacionados e interoperáveis. Existem diferenças entre ASP.NET Web Services (ASMX) e WCF, mas é importante entender que o WCF suporta os mesmos recursos que o ASMX fornece — mensagens SOAP via HTTP. Este artigo demonstra como consumir um serviço WCF SOAP a partir de um aplicativo Xamarin.Forms.

## <a name="consume-a-restful-web-service"></a>[Consumir um serviço web RESTful](~/xamarin-forms/data-cloud/web-services/rest.md)

Transferência de Estado Representacional (REST) é um estilo arquitetônico para a construção de serviços web. As solicitações REST são feitas através do HTTP usando os mesmos verbos HTTP que os navegadores da Web usam para recuperar páginas da Web e enviar dados para servidores. Este artigo demonstra como consumir um serviço web RESTful a partir de um aplicativo Xamarin.Forms.
