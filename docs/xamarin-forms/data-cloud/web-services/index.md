---
title: Xamarin.Formse serviços Web
description: Este guia explica como se comunicar com diferentes serviços da Web para fornecer a funcionalidade CRUD (criar, ler, atualizar e excluir) a um Xamarin.Forms aplicativo. Os tópicos abordados incluem a comunicação com serviços ASMX, serviços WCF, serviços REST.
ms.prod: xamarin
ms.assetid: 8B360BDA-E4E3-4A3F-9004-0E35362F49F8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/27/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 5b2613b94d2c347d9bc6a94086f869b07ab8a55b
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2020
ms.locfileid: "84131879"
---
# <a name="xamarinforms-and-web-services"></a>Xamarin.Formse serviços Web

## <a name="introduction"></a>[Introdução](introduction.md)

Este artigo fornece uma explicação do Xamarin.Forms exemplo de aplicativo que demonstra como se comunicar com diferentes serviços Web. Os tópicos abordados incluem a anatomia do aplicativo, as páginas, o modelo de dados e a invocação de operações de serviço Web.

## <a name="consume-an-aspnet-web-service-asmx"></a>[Consumir um Serviço Web ASP.NET (ASMX)](~/xamarin-forms/data-cloud/web-services/asmx.md)

Os serviços Web ASP.NET (ASMX) fornecem a capacidade de criar serviços Web que enviam mensagens por HTTP usando SOAP (Simple Object Access Protocol). SOAP é um protocolo independente de plataforma e independente de linguagem para criar e acessar serviços Web. Os consumidores de um serviço ASMX não precisam saber nada sobre a plataforma, o modelo de objeto ou a linguagem de programação usada para implementar o serviço. Eles só precisam entender como enviar e receber mensagens SOAP. Este artigo demonstra como consumir um serviço Web ASMX de um Xamarin.Forms aplicativo.

## <a name="consume-a-windows-communication-foundation-wcf-web-service"></a>[Consumir um serviço Web Windows Communication Foundation (WCF)](~/xamarin-forms/data-cloud/web-services/wcf.md)

O WCF é a estrutura unificada da Microsoft para a criação de aplicativos orientados a serviços. Ele permite que os desenvolvedores criem aplicativos distribuídos seguros, confiáveis, transacionados e interoperáveis. Há diferenças entre o ASMX (ASP.NET Web Services) e o WCF, mas é importante entender que o WCF dá suporte aos mesmos recursos que o ASMX fornece — mensagens SOAP por HTTP. Este artigo demonstra como consumir um serviço SOAP do WCF de um Xamarin.Forms aplicativo.

## <a name="consume-a-restful-web-service"></a>[Consumir um serviço Web RESTful](~/xamarin-forms/data-cloud/web-services/rest.md)

REST é um estilo de arquitetura para a criação de serviços Web. As solicitações REST são feitas por HTTP usando os mesmos verbos HTTP que os navegadores da Web usam para recuperar páginas da Web e para enviar dados aos servidores. Este artigo demonstra como consumir um serviço Web RESTful de um Xamarin.Forms aplicativo.
