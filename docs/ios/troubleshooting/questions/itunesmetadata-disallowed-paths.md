---
title: 'Por que o envio do meu aplicativo falha com: Caminhos não permitidos (iTunesMetadata. plist) encontrados em...?'
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: AE1BBDC6-4D3A-4471-876B-FE28B6E59A24
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 04/03/2018
ms.openlocfilehash: ad663c47520826cc549011c9e5fc7cbb078d6214
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70291028"
---
# <a name="why-does-my-app-submission-fail-with-disallowed-paths--itunesmetadataplist--found-at--"></a>Por que o envio do meu aplicativo falha com: Caminhos não permitidos (iTunesMetadata. plist) encontrados em...?

> AO ERRO ITMS-90047: "Caminhos não permitidos (" iTunesMetadata. plist ") encontrados em: Payload/iPhoneApp1. app "

Esse erro é o resultado de uma alteração no processo de verificação da loja de aplicativos da Apple para impedir que os usuários atinjam problemas como o [Bug 29180](https://bugzilla.xamarin.com/show_bug.cgi?id=29180). Esse erro específico _não_ está relacionado à versão específica do Xamarin que você instalou, portanto, o downgrade _não_ ajudará.

Consulte a discussão do fórum [aqui](https://forums.xamarin.com/discussion/40388/disallowed-paths-itunesmetadata-plist-found-at-when-submitting-to-app-store/p1) para obter informações de solução alternativa e as atualizações mais recentes para esse problema.
