---
title: Xamarin. Mac – solução de problemas de macOS Sierra
description: Este documento fornece várias dicas de solução de problemas para trabalhar com macOS Sierra em aplicativos Xamarin. Mac. As dicas estão relacionadas à Mac App Store, Apple Pay, compatibilidade binária, CFNetwork, CloudKit e muito mais.
ms.prod: xamarin
ms.assetid: 323DD5EE-87CE-48E4-B234-1CF61B45A019
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 09/22/2016
ms.openlocfilehash: a4e7f7169e4c7ec0ec2947e17b1434179f47488f
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73017030"
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

- Chamar `NSObject.ValueForKey` será uma chave de `null` resultará em uma exceção.
- Os `NSURLSession` e `NSURLConnection` não são mais conjuntos de codificação RC4 durante o handshake de TLS para URLs de `http://`.
- Os aplicativos podem travar se modificarem a geometria de uma superexibição nos métodos `ViewWillLayoutSubviews` ou `LayoutSubviews`.
- Para todas as conexões SSL/TLS, a cifra simétrica RC4 agora está desabilitada por padrão. Além disso, a API de transporte seguro não dá mais suporte a SSLv3 e é recomendável que o aplicativo pare de usar a criptografia SHA-1 e 3DES assim que possível.

<a name="CFNetwork-HTTP-Protocol" />

## <a name="cfnetwork-http-protocol"></a>Protocolo HTTP CFNetwork

A propriedade `HTTPBodyStream` da classe `NSMutableURLRequest` deve ser definida como um fluxo não aberto, pois `NSURLConnection` e `NSURLSession` agora impõem estritamente esse requisito.

<a name="CloudKit" />

## <a name="cloudkit"></a>CloudKit

As operações de longa execução retornarão um _"você não tem permissão para salvar o arquivo"._ Ao.

<a name="CoreImage" />

## <a name="core-image"></a>Imagem principal

A API de `CIImageProcessor` agora dá suporte a uma contagem de imagens de entrada arbitrária. `CIImageProcessor` API que foi incluída no macOS Sierra beta 1 será removida.

<a name="Notifications" />

## <a name="notifications"></a>Notificações

Ao trabalhar com as extensões de conteúdo de notificação, os controladores de exibição não estão sendo liberados corretamente e podem resultar em uma falha quando os limites de memória de extensão são atingidos.

<a name="NSUserActivity" />

## <a name="nsuseractivity"></a>NSUserActivity

Após uma operação de entrega, a propriedade `UserInfo` de um objeto `NSUserActivity` pode estar vazia. Chame explicitamente `BecomeCurrent` objeto `NSUserActivity` como uma solução alternativa atual.

<a name="Safari" />

## <a name="safari"></a>Safari

O geolocation requer uma URL segura (`https://`) para trabalhar no Ios 10 e MacOS Sierra para impedir o uso mal-intencionado de dados de localização.

## <a name="related-links"></a>Links relacionados

- [Amostras de Mac](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.Mac)
- [O que há de novo no macOS 10,12](https://developer.apple.com/library/prerelease/content/releasenotes/MacOSX/WhatsNewInOSX/Articles/OSXv10.html#//apple_ref/doc/uid/TP40017145-SW1)
