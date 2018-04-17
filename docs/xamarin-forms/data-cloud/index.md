---
title: Serviços de dados e de nuvem
description: Aplicativos xamarin. Forms podem consumir serviços da web implementado usando uma ampla variedade de tecnologias e este guia examinará como fazer isso.
ms.prod: xamarin
ms.assetid: 0601D9D0-C8D2-4C3B-A749-A340BDBF64A4ß
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/28/2017
ms.openlocfilehash: 6e67820fa83ddea46f934b4eaedde2c6334f9cc6
ms.sourcegitcommit: bc39d85b4585fcb291bd30b8004b3f7edcac4602
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2018
---
# <a name="data--cloud-services"></a>Serviços de dados e de nuvem

_Aplicativos xamarin. Forms podem consumir serviços da web implementado usando uma ampla variedade de tecnologias e este guia examinará como fazer isso._

Para obter uma introdução para o consumo do serviço web de plataforma cruzada na plataforma Xamarin, consulte [Introdução aos serviços Web](~/cross-platform/data-cloud/web-services/index.md).

## <a name="understanding-the-samplexamarin-formsdata-cloudwalkthroughmd"></a>[Noções básicas da amostra](~/xamarin-forms/data-cloud/walkthrough.md)

Este artigo fornece uma explicação detalhada sobre o aplicativo xamarin. Forms de exemplo que demonstra como se comunicar com serviços web diferentes. Os tópicos abordados incluem a anatomia do aplicativo, as páginas, o modelo de dados e chamar operações de serviço web.

## <a name="consuming-web-servicesxamarin-formsdata-cloudconsumingindexmd"></a>[Consumo de Serviços Web](~/xamarin-forms/data-cloud/consuming/index.md)

Este guia demonstra como se comunicar com serviços web diferentes para fornecer criar, ler, atualizar e excluir (CRUD) funcionalidade a um aplicativo xamarin. Forms. Os tópicos abordados estão se comunicando [serviços ASMX](consuming/asmx.md), [serviços WCF](consuming/wcf.md), [serviços REST](consuming/rest.md), e [aplicativos móveis do Azure](consuming/azure.md).

## <a name="authenticating-access-to-web-servicesxamarin-formsdata-cloudauthenticationindexmd"></a>[Autenticar o acesso as Serviços Web](~/xamarin-forms/data-cloud/authentication/index.md)

Este guia explica como integrar serviços de autenticação em um aplicativo xamarin. Forms para permitir que os usuários compartilham um back-end tendo apenas o acesso a seus próprios dados. Os tópicos abordados incluem [usando a autenticação básica com um serviço REST](authentication/rest.md), [usando o componente Xamarin.Auth para autenticar em relação a provedores de identidade OAuth](authentication/oauth.md)e o uso da autenticação embutida mecanismos oferecidos pelo [aplicativos móveis do Azure](authentication/azure.md).

## <a name="synchronizing-data-with-web-servicessyncindexmd"></a>[Sincronizar dados com Serviços Web](sync/index.md)

Este artigo explica como adicionar a funcionalidade de sincronização offline para um aplicativo xamarin. Forms. Sincronização offline permite que os usuários interagem com um aplicativo móvel, exibir, adicionar ou modificar dados, mesmo que não tem uma conexão de rede. As alterações são armazenadas em um banco de dados local e, depois que o dispositivo estiver online, as alterações podem ser sincronizadas com o serviço web.

## <a name="sending-push-notificationspush-notificationsindexmd"></a>[Enviar notificações por push](push-notifications/index.md)

Este artigo demonstra como adicionar notificações por push a um aplicativo xamarin. Forms. Hubs de notificação do Azure oferecem uma infraestrutura de push escalonável para enviar notificações por push de qualquer back-end para qualquer plataforma móvel, eliminando a complexidade de um back-end precisar se comunicar com sistemas de notificação de plataforma diferente.

## <a name="storing-files-in-the-cloudstorageindexmd"></a>[Armazenar arquivos na nuvem](storage/index.md)

Este artigo demonstra como usar o xamarin. Forms para armazenar texto e dados binários no armazenamento do Azure e como acessar os dados. Armazenamento do Azure é uma solução de armazenamento de nuvem escalonáveis que pode ser usada para armazenar dados estruturados e não estruturados.

## <a name="searching-data-in-the-cloudsearchindexmd"></a>[Pesquisar dados na nuvem](search/index.md)

Este artigo demonstra como usar a biblioteca de pesquisa do Microsoft Azure para integrar a pesquisa do Azure em um aplicativo xamarin. Forms. Pesquisa do Azure é um serviço de nuvem que fornece a indexação e consulta de recursos para os dados carregados. Isso remove os requisitos de infraestrutura e as complexidades de algoritmo de pesquisa normalmente associadas ao implementar a funcionalidade de pesquisa em um aplicativo.

## <a name="storing-data-in-a-document-databasecosmosdbindexmd"></a>[Armazenar dados em um banco de dados de documento](cosmosdb/index.md)

Este guia demonstra como usar a biblioteca de cliente .NET padrão do Azure Cosmos DB para integrar um banco de dados de documento de banco de dados do Azure Cosmos em um aplicativo xamarin. Forms. Um banco de dados de documento de banco de dados do Azure Cosmos é um banco de dados NoSQL que fornece acesso de baixa latência para documentos JSON, oferecendo um serviço de banco de dados rápida, altamente disponível e dimensionável para aplicativos que necessitam de replicação global e escala contínua.

## <a name="adding-intelligence-with-cognitive-servicescognitive-servicesindexmd"></a>[Adicionar inteligência com Serviços Cognitivos](cognitive-services/index.md)

Este guia explica como usar algumas das APIs Microsoft cognitivas serviços em um aplicativo xamarin. Forms. Serviços cognitivos são um conjunto de APIs, SDKs e serviços disponíveis para os desenvolvedores a criar aplicativos mais inteligente adicionando recursos, como reconhecimento facial, reconhecimento de fala e compreensão de idioma.
