---
title: Serviços de dados e de nuvem
description: Aplicativos xamarin. Forms podem consumir serviços web implementados usando uma ampla variedade de tecnologias, e este guia irá examinar como fazer isso.
ms.prod: xamarin
ms.assetid: 0601D9D0-C8D2-4C3B-A749-A340BDBF64A4ß
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/28/2017
ms.openlocfilehash: 6e67820fa83ddea46f934b4eaedde2c6334f9cc6
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61327500"
---
# <a name="data--cloud-services"></a>Serviços de dados e de nuvem

_Aplicativos xamarin. Forms podem consumir serviços web implementados usando uma ampla variedade de tecnologias, e este guia irá examinar como fazer isso._

Para obter uma introdução para consumo do serviço web de plataforma cruzada na plataforma Xamarin, consulte [Introdução aos serviços Web](~/cross-platform/data-cloud/web-services/index.md).

## <a name="understanding-the-samplexamarin-formsdata-cloudwalkthroughmd"></a>[Noções básicas da amostra](~/xamarin-forms/data-cloud/walkthrough.md)

Este artigo fornece um passo a passo do aplicativo de exemplo do xamarin. Forms que demonstra como se comunicar com serviços web diferentes. Os tópicos abordados incluem a anatomia de um aplicativo, as páginas, o modelo de dados e chamar operações de serviço web.

## <a name="consuming-web-servicesxamarin-formsdata-cloudconsumingindexmd"></a>[Consumo de Serviços Web](~/xamarin-forms/data-cloud/consuming/index.md)

Este guia demonstra como se comunicar com serviços da web diferente para fornecer criar, ler, atualizar e excluir (CRUD) funcionalidade a um aplicativo xamarin. Forms. Os tópicos abordados incluem comunicando [serviços ASMX](consuming/asmx.md), [serviços WCF](consuming/wcf.md), [serviços REST](consuming/rest.md), e [aplicativos móveis do Azure](consuming/azure.md).

## <a name="authenticating-access-to-web-servicesxamarin-formsdata-cloudauthenticationindexmd"></a>[Autenticar o acesso as Serviços Web](~/xamarin-forms/data-cloud/authentication/index.md)

Este guia explica como integrar serviços de autenticação em um aplicativo xamarin. Forms para permitir que os usuários compartilhem um back-end tendo apenas o acesso a seus próprios dados. Os tópicos abordados incluem [usando a autenticação básica com um serviço REST](authentication/rest.md), [usando o componente de AUTH para autenticar em relação a provedores de identidade OAuth](authentication/oauth.md)e usando a autenticação interna mecanismos oferecidos pelo [aplicativos móveis do Azure](authentication/azure.md).

## <a name="synchronizing-data-with-web-servicessyncindexmd"></a>[Sincronizar dados com Serviços Web](sync/index.md)

Este artigo explica como adicionar funcionalidade de sincronização offline para um aplicativo xamarin. Forms. Sincronização offline permite que os usuários interajam com um aplicativo móvel, exibindo, adicionando ou modificando dados, mesmo que não tem uma conexão de rede. As alterações são armazenadas em um banco de dados local e, depois que o dispositivo estiver online, as alterações podem ser sincronizadas com o serviço web.

## <a name="sending-push-notificationspush-notificationsindexmd"></a>[Enviar notificações por push](push-notifications/index.md)

Este artigo demonstra como adicionar notificações por push a um aplicativo xamarin. Forms. Os Hubs de notificação do Azure fornecem uma infraestrutura de envio por push escalonável para enviar notificações por push de qualquer back-end para qualquer plataforma móvel, enquanto elimina a complexidade de um back-end precisar se comunicar com sistemas de notificação de plataforma diferente.

## <a name="storing-files-in-the-cloudstorageindexmd"></a>[Armazenar arquivos na nuvem](storage/index.md)

Este artigo demonstra como usar o xamarin. Forms para armazenar texto e dados binários no armazenamento do Azure e como acessar os dados. Armazenamento do Azure é uma solução de armazenamento de nuvem escalonável que pode ser usada para armazenar dados não estruturados e estruturados.

## <a name="searching-data-in-the-cloudsearchindexmd"></a>[Pesquisar dados na nuvem](search/index.md)

Este artigo demonstra como usar a biblioteca do Microsoft Azure Search para integrar o Azure Search em um aplicativo xamarin. Forms. O Azure Search é um serviço de nuvem que fornece a indexação e consulta recursos para os dados carregados. Isso remove as complexidades do algoritmo de pesquisa tradicionalmente associadas ao implementar a funcionalidade de pesquisa em um aplicativo e requisitos de infraestrutura.

## <a name="storing-data-in-a-document-databasecosmosdbindexmd"></a>[Armazenar dados em um banco de dados de documento](cosmosdb/index.md)

Este guia demonstra como usar a biblioteca de cliente .NET Standard do Azure Cosmos DB para integrar um banco de dados de documentos do Azure Cosmos DB em um aplicativo xamarin. Forms. Um banco de dados de documentos do Azure Cosmos DB é um banco de dados NoSQL que fornece acesso de baixa latência para documentos JSON, oferecendo um serviço de banco de dados rápida, altamente disponível e escalonável para aplicativos que exigem dimensionamento perfeito e replicação global.

## <a name="adding-intelligence-with-cognitive-servicescognitive-servicesindexmd"></a>[Adicionar inteligência com Serviços Cognitivos](cognitive-services/index.md)

Este guia explica como usar alguns das APIs de serviços Cognitivos da Microsoft em um aplicativo xamarin. Forms. Os serviços cognitivos são um conjunto de APIs, SDKs e serviços disponíveis para desenvolvedores para tornar seus aplicativos mais inteligentes com a adição de recursos, como reconhecimento facial, reconhecimento de fala e reconhecimento vocal.
