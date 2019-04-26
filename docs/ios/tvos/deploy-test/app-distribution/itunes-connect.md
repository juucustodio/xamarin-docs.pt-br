---
title: Configurar seu aplicativo tvOS no iTunes Connect
description: Este artigo fornece um guia complementar para o iOS configurar seu aplicativo no iTunes Connect para as configurações específicas do tvOS.
ms.prod: xamarin
ms.assetid: 86C7C5BD-C97D-4F1D-B611-A7694557BFDF
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: 3f4ef00cfe990de2d5afd461d7a110d32bc4a236
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61413179"
---
# <a name="configure-your-tvos-app-in-itunes-connect"></a>Configurar seu aplicativo tvOS no iTunes Connect

_Este artigo fornece um guia complementar para o iOS configurar seu aplicativo no iTunes Connect para as configurações específicas do tvOS._


Além das configurações e a configuração que você precisará fazer seguindo o iOS [configurar seu aplicativo no iTunes Connect](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md) guia, este documento aborda as configurações específicas que serão necessários para liberar um xamarin. tvos aplicativo na Store de aplicativo da Apple TV.

<a name="Adding-a-tvOS-Release-Version" />

## <a name="adding-a-tvos-release-version"></a>Adição de um versão de lançamento do tvOS

Se você estiver criando um novo aplicativo para ser lançada em de Store de aplicativo da Apple TV ou adicionar suporte a Apple TV a um aplicativo iOS existente, você precisará ter criado um iTunes Connect registro e configurá-lo usando o seguinte iOS guias específicos:

- [Criando um registro do iTunes Connect](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md#creating)
- [Gerenciamento de vídeos e capturas de tela de aplicativo](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md#managing)
- [Gerenciamento de nome, descrição, novidades, URLs e palavras-chave](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md#metadata)
- [Manutenção de informações gerais](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md#general)

Opcionalmente, você também pode exigir:

- [Manutenção de informações do Game Center](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md#game-center)
- [Manutenção de informações de compra no aplicativo](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md#iap)

Com todas as etapas acima concluídas, abra o registro do iTunes Connect e selecione Adicionar suporte de tvOS usando a barra lateral à esquerda de seu aplicativo:

[![](itunes-connect-images/connect01.png "Adicionar suporte de tvOS usando a barra lateral esquerda")](itunes-connect-images/connect01.png#lightbox)

As telas de informações específicas do tvOS, em seguida, estarão disponíveis para o determinado registro do iTunes Connect:

[![](itunes-connect-images/connect02.png "A tela de informações específicas do tvOS")](itunes-connect-images/connect02.png#lightbox)

<a name="tvOS-Version-Information" />

## <a name="tvos-version-information"></a>Informações de versão do tvOS

Na barra lateral à esquerda, selecione **1.0 preparação para envio** sob a seção de aplicativo tvOS:

[![](itunes-connect-images/connect03.png "Informações de versão do tvOS")](itunes-connect-images/connect03.png#lightbox)

Nessa tela, forneça as seguintes informações:

- O necessária capturas de tela, descrição, as palavras-chave e URLs.
- Informações gerais do aplicativo, como o número de versão, direitos autorais e a classificação etária.
- Compras de opcionais no aplicativo.
- Suporte opcional Game Center com placares de líderes e conquistas.
- Informações de revisão do aplicativo, como, entre em contato com contas de demonstração e notas necessárias.

Depois que você inseriu as informações necessárias, clique no **salvar** botão no canto superior direito da tela para salvar suas alterações:

[![](itunes-connect-images/connect04.png "pronto para envio de informações de versão do tvOS")](itunes-connect-images/connect04.png#lightbox)

<a name="Submitting-for-Review" />

## <a name="preparing-to-submit-for-review"></a>Preparando para enviar para revisão

Quando você está finalmente pronto para enviar seu aplicativo tvos para a Store do Apple TV App para revisão, volte para registro do iTunes do aplicativo Connect e clique o **enviar para revisão** botão no canto superior direito da tela:

[![](itunes-connect-images/connect05.png "Enviar para revisão")](itunes-connect-images/connect05.png#lightbox)

<a name="Summary" />

## <a name="summary"></a>Resumo

Este artigo forneceu uma visão geral da configuração específica do tvOS necessária no iTunes Connect para lançar um aplicativo tvOS de Store de aplicativo da Apple TV.



## <a name="related-links"></a>Links relacionados

- [Amostras do tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [Guias de Interface humana do tvOS](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guia de programação de aplicativo para tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
