---
title: Configurar seu aplicativo tvOS no iTunes conectar
description: Este artigo fornece um guia complementar para o iOS configurar seu aplicativo na iTunes conectar-se para as configurações específicas de tvOS.
ms.prod: xamarin
ms.assetid: 86C7C5BD-C97D-4F1D-B611-A7694557BFDF
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/16/2017
ms.openlocfilehash: d082a980572349da1b7e6155b3aa4de41512796f
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
ms.locfileid: "30779820"
---
# <a name="configure-your-tvos-app-in-itunes-connect"></a>Configurar seu aplicativo tvOS no iTunes conectar

_Este artigo fornece um guia complementar para o iOS configurar seu aplicativo na iTunes conectar-se para as configurações específicas de tvOS._


Além das configurações e a configuração que você precisará fazer seguindo o iOS [configurar seu aplicativo na iTunes conectar](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md) guia, este documento aborda as configurações específicas que serão necessários para liberar uma Xamarin.tvOS aplicativo na loja de aplicativos Apple TV.

<a name="Adding-a-tvOS-Release-Version" />

## <a name="adding-a-tvos-release-version"></a>Adicionando um versão de lançamento do tvOS

Se você estiver criando um novo aplicativo a ser liberado na loja de aplicativos Apple TV, ou adicionando suporte a Apple TV a um aplicativo iOS existente, você precisará ter criado um iTunes conectar registro e configurado usando o seguinte iOS orienta específico:

- [Criando um registro do iTunes Connect](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md#creating)
- [Gerenciamento de vídeos e capturas de tela de aplicativo](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md#managing)
- [Gerenciamento de nome, descrição, novidades, URLs e palavras-chave](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md#metadata)
- [Manter as informações gerais](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md#general)

Opcionalmente, você também pode exigir:

- [Manutenção de informações do Game Center](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md#game-center)
- [Manutenção de informações de compra no aplicativo](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md#iap)

Com todas as etapas acima concluídas, abra o iTunes conectar registro de seu aplicativo e selecione para adicionar suporte tvOS usando a barra lateral esquerda:

[![](itunes-connect-images/connect01.png "Adicionar suporte tvOS usando a barra lateral esquerda")](itunes-connect-images/connect01.png#lightbox)

As telas de informações específicas de tvOS, em seguida, estarão disponíveis para determinado iTunes conectar registro:

[![](itunes-connect-images/connect02.png "A tela de informações específicas de tvOS")](itunes-connect-images/connect02.png#lightbox)

<a name="tvOS-Version-Information" />

## <a name="tvos-version-information"></a>tvOS informações de versão

Na barra lateral esquerda, selecione **1.0 preparação para envio** na seção tvOS aplicativo:

[![](itunes-connect-images/connect03.png "tvOS informações de versão")](itunes-connect-images/connect03.png#lightbox)

Nessa tela, forneça as seguintes informações:

- O necessário capturas de tela, descrição, palavras-chave e URLs.
- Informações de aplicativo geral como o número de versão, direitos autorais e classificação de idade.
- Compras de opcionais no aplicativo.
- Suporte com tabelas e conquistas opcional Game Center.
- Informações de revisão do aplicativo como contato, contas de demonstração e observações necessárias.

Depois que você inseriu as informações necessárias, clique no **salvar** botão no canto superior direito da tela para salvar as alterações:

[![](itunes-connect-images/connect04.png "tvOS pronto para envio de informações de versão")](itunes-connect-images/connect04.png#lightbox)

<a name="Submitting-for-Review" />

## <a name="preparing-to-submit-for-review"></a>Preparando para enviar para revisão

Quando estiver pronto, por fim, para enviar seu aplicativo Xamarin.tvOS para Apple TV App Store para revisão, retorne ao iTunes do aplicativo conectar registro e clique no **enviar para revisão** botão no canto superior direito da tela:

[![](itunes-connect-images/connect05.png "Enviar para revisão")](itunes-connect-images/connect05.png#lightbox)

<a name="Summary" />

## <a name="summary"></a>Resumo

Este artigo deu a visão geral da configuração específica tvOS necessária no iTunes Connect para um aplicativo tvOS a loja de aplicativos Apple TV de versão.



## <a name="related-links"></a>Links relacionados

- [Amostras do tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS guias de Interface Humana](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guia de programação de aplicativo para tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
