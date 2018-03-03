---
title: "Dados e serviços de nuvem"
description: "Aplicativos xamarin. Forms podem consumir serviços da web implementado usando uma ampla variedade de tecnologias e este guia examinará como fazer isso."
ms.topic: article
ms.prod: xamarin
ms.assetid: "0601D9D0-C8D2-4C3B-A749-A340BDBF64A4ß"
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/28/2017
ms.openlocfilehash: 28007ee702c66f3b819430b544465d3470d571d9
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="data--cloud-services"></a>Dados e serviços de nuvem

_Aplicativos xamarin. Forms podem consumir serviços da web implementado usando uma ampla variedade de tecnologias e este guia examinará como fazer isso._

Para obter uma introdução para o consumo do serviço web de plataforma cruzada na plataforma Xamarin, consulte [Introdução aos serviços Web](~/cross-platform/data-cloud/web-services/index.md).

## <a name="understanding-the-samplexamarin-formsdata-cloudwalkthroughmd"></a>[Noções básicas sobre o exemplo](~/xamarin-forms/data-cloud/walkthrough.md)

Este artigo fornece uma explicação detalhada sobre o aplicativo xamarin. Forms de exemplo que demonstra como se comunicar com serviços web diferentes. Os tópicos abordados incluem a anatomia do aplicativo, as páginas, o modelo de dados e chamar operações de serviço web.

## <a name="consuming-web-servicesxamarin-formsdata-cloudconsumingindexmd"></a>[Consumir serviços da Web](~/xamarin-forms/data-cloud/consuming/index.md)

Este guia demonstra como se comunicar com serviços web diferentes para fornecer criar, ler, atualizar e excluir (CRUD) funcionalidade a um aplicativo xamarin. Forms. Os tópicos abordados estão se comunicando [serviços ASMX](consuming/asmx.md), [serviços WCF](consuming/wcf.md), [serviços REST](consuming/rest.md), [aplicativos móveis do Azure](consuming/azure.md)e [ Amazon Web Services](consuming/aws.md).

## <a name="authenticating-access-to-web-servicesxamarin-formsdata-cloudauthenticationindexmd"></a>[Autenticando o acesso a serviços Web](~/xamarin-forms/data-cloud/authentication/index.md)

Este guia explica como integrar serviços de autenticação em um aplicativo xamarin. Forms para permitir que os usuários compartilham um back-end tendo apenas o acesso a seus próprios dados. Os tópicos abordados incluem [usando a autenticação básica com um serviço REST](authentication/rest.md), [usando o componente Xamarin.Auth para autenticar em relação a provedores de identidade OAuth](authentication/oauth.md)e o uso da autenticação embutida mecanismos oferecidos pelo [aplicativos móveis do Azure](authentication/azure.md), e [Amazon Web Services](authentication/aws.md).

## <a name="synchronizing-data-with-web-servicessyncindexmd"></a>[Sincronização de dados com serviços Web](sync/index.md)

Este artigo explica como adicionar a funcionalidade de sincronização offline para um aplicativo xamarin. Forms. Sincronização offline permite que os usuários interagem com um aplicativo móvel, exibir, adicionar ou modificar dados, mesmo que não tem uma conexão de rede. As alterações são armazenadas em um banco de dados local e, depois que o dispositivo estiver online, as alterações podem ser sincronizadas com o serviço web.

## <a name="sending-push-notificationspush-notificationsindexmd"></a>[Enviar notificações por Push](push-notifications/index.md)

Este artigo demonstra como adicionar notificações por push a um aplicativo xamarin. Forms. Hubs de notificação do Azure oferecem uma infraestrutura de push escalonável para enviar notificações por push de qualquer back-end para qualquer plataforma móvel, eliminando a complexidade de um back-end precisar se comunicar com sistemas de notificação de plataforma diferente.

## <a name="storing-files-in-the-cloudstorageindexmd"></a>[Armazenando arquivos na nuvem](storage/index.md)

Este artigo demonstra como usar o xamarin. Forms para armazenar texto e dados binários no armazenamento do Azure e como acessar os dados. Armazenamento do Azure é uma solução de armazenamento de nuvem escalonáveis que pode ser usada para armazenar dados estruturados e não estruturados.

## <a name="searching-data-in-the-cloudsearchindexmd"></a>[Pesquisando dados na nuvem](search/index.md)

Este artigo demonstra como usar a biblioteca de pesquisa do Microsoft Azure para integrar a pesquisa do Azure em um aplicativo xamarin. Forms. Pesquisa do Azure é um serviço de nuvem que fornece a indexação e consulta de recursos para os dados carregados. Isso remove os requisitos de infraestrutura e as complexidades de algoritmo de pesquisa normalmente associadas ao implementar a funcionalidade de pesquisa em um aplicativo.

## <a name="storing-data-in-a-document-databasecosmosdbindexmd"></a>[Armazenamento de dados em um banco de dados de documento](cosmosdb/index.md)

Este guia demonstra como usar a biblioteca de cliente de documentos do Microsoft Azure para integrar um banco de dados de documento de banco de dados do Azure Cosmos em um aplicativo xamarin. Forms. Um banco de dados de documento de banco de dados do Azure Cosmos é um banco de dados NoSQL que fornece acesso de baixa latência para documentos JSON, oferecendo um serviço de banco de dados rápida, altamente disponível e dimensionável para aplicativos que necessitam de replicação global e escala contínua.

## <a name="adding-intelligence-with-cognitive-servicescognitive-servicesindexmd"></a>[Adicionando inteligência com serviços Cognitivos](cognitive-services/index.md)

Este guia explica como usar algumas das APIs Microsoft cognitivas serviços em um aplicativo xamarin. Forms. Serviços cognitivos são um conjunto de APIs, SDKs e serviços disponíveis para os desenvolvedores a criar aplicativos mais inteligente adicionando recursos, como reconhecimento facial, reconhecimento de fala e compreensão de idioma.
