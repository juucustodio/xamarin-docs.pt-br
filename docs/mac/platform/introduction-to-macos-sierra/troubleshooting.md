---
title: Xamarin. Mac – solução de problemas de macOS Sierra
description: Este documento fornece várias dicas de solução de problemas para trabalhar com macOS Sierra em aplicativos Xamarin. Mac. As dicas estão relacionadas à Mac App Store, Apple Pay, compatibilidade binária, CFNetwork, CloudKit e muito mais.
ms.prod: xamarin
ms.assetid: 323DD5EE-87CE-48E4-B234-1CF61B45A019
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 09/22/2016
ms.openlocfilehash: 3bb2acd5ef560afa787c2746133c05066a15cf9e
ms.sourcegitcommit: b07e0259d7b30413673a793ebf4aec2b75bb9285
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68511795"
---
# <a name="xamarinmac---macos-sierra-troubleshooting"></a>Xamarin. Mac – solução de problemas de macOS Sierra

_Este artigo fornece várias dicas de solução de problemas para trabalhar com macOS Sierra em aplicativos Xamarin. Mac._

As seções a seguir listam alguns problemas conhecidos que podem ocorrer ao usar o macOS Sierra com o Xamarin. Mac e a solução para esses problemas:

- [App Store](#App-Store)
- [Apple Pay](#Apple-Pay)
- [Compatibilidade binária](#Binary-Compatibility)
- [Protocolo HTTP CFNetwork](#CFNetwork-HTTP-Protocol)
- [CloudKit](#CloudKit)
- [Imagem principal](#CoreImage)
- [Notificações](#Notifications)
- [NSUserActivity](#NSUserActivity)
- [Safari](#Safari)

<a name="App-Store" />

## <a name="app-store"></a>App Store

Problemas Conhecidos:

- Ao testar compras no aplicativo no ambiente de área restrita, a caixa de diálogo de autenticação pode aparecer duas vezes.
- Ao testar compras no aplicativo com conteúdo hospedado no ambiente de área restrita, a caixa de diálogo de senha será exibida toda vez que o aplicativo for levado para o primeiro plano até que o download do conteúdo seja concluído.

<a name="Apple-Pay" />

## <a name="apple-pay"></a>Apple Pay

Se uma data de validade incorreta ou um código de segurança (CW) for inserido ao adicionar um novo cartão de pagamento ao Apple Pay, o processo de provisionamento do cartão será encerrado.

<a name="Binary-Compatibility" />

## <a name="binary-compatibility"></a>Compatibilidade binária

Problemas Conhecidos:

- Chamar `NSObject.ValueForKey` será uma `null` chave resultará em uma exceção.
- Ambos `NSURLSession` `http://` e `NSURLConnection` não mais conjuntos de codificação RC4 durante o handshake de TLS para URLs.
- Os `ViewWillLayoutSubviews` aplicativos podem parar se modificarem a geometria de uma superexibição nos métodos `LayoutSubviews` ou.
- Para todas as conexões SSL/TLS, a cifra simétrica RC4 agora está desabilitada por padrão. Além disso, a API de transporte seguro não dá mais suporte a SSLv3 e é recomendável que o aplicativo pare de usar a criptografia SHA-1 e 3DES assim que possível.

<a name="CFNetwork-HTTP-Protocol" />

## <a name="cfnetwork-http-protocol"></a>Protocolo HTTP CFNetwork

A `HTTPBodyStream` propriedade `NSURLConnection` `NSURLSession` da classe deve ser definida como um fluxo não aberto, já que e agora impõe estritamente esse requisito. `NSMutableURLRequest`

<a name="CloudKit" />

## <a name="cloudkit"></a>CloudKit

As operações de longa execução retornarão um _"você não tem permissão para salvar o arquivo"._ ao.

<a name="CoreImage" />

## <a name="core-image"></a>Imagem principal

A `CIImageProcessor` API agora dá suporte a uma contagem de imagens de entrada arbitrária. `CIImageProcessor`A API que foi incluída no macOS Sierra beta 1 será removida.

<a name="Notifications" />

## <a name="notifications"></a>Notificações

Ao trabalhar com as extensões de conteúdo de notificação, os controladores de exibição não estão sendo liberados corretamente e podem resultar em uma falha quando os limites de memória de extensão são atingidos.

<a name="NSUserActivity" />

## <a name="nsuseractivity"></a>NSUserActivity

Após uma operação de entrega, `UserInfo` a propriedade de `NSUserActivity` um objeto pode estar vazia. `BecomeCurrent` Chame`NSUserActivity` explicitamente o objeto como uma solução alternativa atual.

<a name="Safari" />

## <a name="safari"></a>Safari

O geolocation requer uma`https://`URL segura () para funcionar no Ios 10 e MacOS Sierra para impedir o uso mal-intencionado de dados de localização.

## <a name="related-links"></a>Links relacionados

- [Amostras de Mac](https://developer.xamarin.com/samples/mac/)
- [O que há de novo no macOS 10,12](https://developer.apple.com/library/prerelease/content/releasenotes/MacOSX/WhatsNewInOSX/Articles/OSXv10.html#//apple_ref/doc/uid/TP40017145-SW1)
