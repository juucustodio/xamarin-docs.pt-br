---
title: 'Por que o envio do meu aplicativo falha com: caminhos não permitidos (iTunesMetadata. plist) encontrados em...?'
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: AE1BBDC6-4D3A-4471-876B-FE28B6E59A24
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 04/03/2018
ms.openlocfilehash: 0255c918f7d6e984c9af2b396d9a2a00286286e4
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73030990"
---
# <a name="why-does-my-app-submission-fail-with-disallowed-paths--itunesmetadataplist--found-at--"></a>Por que o envio do meu aplicativo falha com: caminhos não permitidos (iTunesMetadata. plist) encontrados em...?

> ERRO: erro ITMS-90047: "caminhos não permitidos (" iTunesMetadata. plist ") encontrados em: Payload/iPhoneApp1. app"

Esse erro é o resultado de uma alteração no processo de verificação da loja de aplicativos da Apple para impedir que os usuários atinjam problemas como o [Bug 29180](https://bugzilla.xamarin.com/show_bug.cgi?id=29180). Esse erro específico _não_ está relacionado à versão específica do Xamarin que você instalou, portanto, o downgrade _não_ ajudará.

Consulte a discussão do fórum [aqui](https://forums.xamarin.com/discussion/40388/disallowed-paths-itunesmetadata-plist-found-at-when-submitting-to-app-store/p1) para obter informações de solução alternativa e as atualizações mais recentes para esse problema.
