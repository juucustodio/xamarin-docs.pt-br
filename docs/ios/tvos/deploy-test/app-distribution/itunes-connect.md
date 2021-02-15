---
title: Configurar seu aplicativo tvOS no iTunes Connect
description: Este artigo fornece um guia suplementar para o iOS configurar seu aplicativo no iTunes Connect para as configurações específicas do tvOS.
ms.prod: xamarin
ms.assetid: 86C7C5BD-C97D-4F1D-B611-A7694557BFDF
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/16/2017
ms.openlocfilehash: 43692bf2180887e7983cf35fb1812a91222dbc7a
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91435156"
---
# <a name="configure-your-tvos-app-in-itunes-connect"></a>Configurar seu aplicativo tvOS no iTunes Connect

_Este artigo fornece um guia suplementar para o iOS configurar seu aplicativo no iTunes Connect para as configurações específicas do tvOS._

Além das configurações e configuração que você precisará fazer seguindo o guia [configurar seu aplicativo no iTunes Connect](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md) , este documento abrange as configurações específicas que serão necessárias para liberar um aplicativo Xamarin. TvOS na loja de aplicativos da Apple TV.

<a name="Adding-a-tvOS-Release-Version"></a>

## <a name="adding-a-tvos-release-version"></a>Adicionando uma versão de lançamento do tvOS

Se você estiver criando um novo aplicativo a ser lançado na loja de aplicativos da Apple TV ou adicionando suporte à Apple TV a um aplicativo iOS existente, você precisará criar um registro do iTunes Connect e configurá-lo usando os seguintes guias específicos do iOS:

- [Criando um registro do iTunes Connect](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md#creating)
- [Gerenciamento de vídeos e capturas de tela de aplicativo](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md#managing)
- [Gerenciamento de nome, descrição, novidades, URLs e palavras-chave](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md#metadata)
- [Mantendo informações gerais](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md#general)

Opcionalmente, você também pode exigir:

- [Manutenção de informações do Game Center](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md#game-center)
- [Mantendo informações de compra no aplicativo](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md#iap)

Com todas as etapas acima concluídas, abra o registro do iTunes Connect do seu aplicativo e selecione para adicionar suporte a tvOS usando a barra lateral esquerda:

[![Adicionar suporte a tvOS usando a barra lateral esquerda](itunes-connect-images/connect01.png)](itunes-connect-images/connect01.png#lightbox)

As telas de informações específicas do tvOS estarão disponíveis para o registro do iTunes Connect fornecido:

[![A tela de informações específicas do tvOS](itunes-connect-images/connect02.png)](itunes-connect-images/connect02.png#lightbox)

<a name="tvOS-Version-Information"></a>

## <a name="tvos-version-information"></a>Informações de versão do tvOS

Na barra lateral esquerda, selecione **1,0 preparar para envio** na seção aplicativo tvOS:

[![Informações de versão do tvOS](itunes-connect-images/connect03.png)](itunes-connect-images/connect03.png#lightbox)

Nessa tela, forneça as seguintes informações:

- As capturas de tela, descrição, palavras-chave e URLs necessárias.
- Informações gerais do aplicativo, como número de versão, direitos autorais e classificação etária.
- Compras opcionais no aplicativo.
- Suporte opcional de Game Center com placares e realizações.
- Informações de revisão do aplicativo necessárias, como contato, contas de demonstração e notas.

Depois de inserir as informações necessárias, clique no botão **salvar** no canto superior direito da tela para salvar as alterações:

[![Informações de versão do tvOS prontas para envio](itunes-connect-images/connect04.png)](itunes-connect-images/connect04.png#lightbox)

<a name="Submitting-for-Review"></a>

## <a name="preparing-to-submit-for-review"></a>Preparando para enviar para revisão

Quando você finalmente estiver pronto para enviar seu aplicativo Xamarin. tvOS para a loja de aplicativos da Apple TV para revisão, retorne ao registro do iTunes Connect do aplicativo e clique no botão **Enviar para revisão** no canto superior direito da tela:

[![Enviar para revisão](itunes-connect-images/connect05.png)](itunes-connect-images/connect05.png#lightbox)

<a name="Summary"></a>

## <a name="summary"></a>Resumo

Este artigo forneceu uma visão geral da configuração específica do tvOS necessária no iTunes Connect para liberar um aplicativo tvOS para a loja de aplicativos da Apple TV.

## <a name="related-links"></a>Links Relacionados

- [Exemplos do tvOS](/samples/browse/?products=xamarin&term=Xamarin.iOS%2btvOS)
- [tvOS](https://developer.apple.com/tvos/)
- [Guias de interface humana do tvOS](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guia de programação de aplicativo para tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)