---
title: Xamarin.Mac - macOS serra de solução de problemas
description: Este documento fornece várias dicas de solução de problemas para trabalhar com macOS Serra em aplicativos de Xamarin.Mac. Dicas se relacionam com Mac App Store, pagamento da Apple, compatibilidade binária, CFNetwork, CloudKit e muito mais.
ms.prod: xamarin
ms.assetid: 323DD5EE-87CE-48E4-B234-1CF61B45A019
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 09/22/2016
ms.openlocfilehash: 5b2571d9562fd137257e2dd0ea2ada8f071bab92
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/05/2018
ms.locfileid: "34792311"
---
# <a name="xamarinmac---macos-sierra-troubleshooting"></a>Xamarin.Mac - macOS serra de solução de problemas

_Este artigo fornece várias dicas de solução de problemas para trabalhar com macOS Serra em aplicativos de Xamarin.Mac._

As seções a seguir listam alguns problemas conhecidos que podem ocorrer ao usar macOS Serra com Xamarin.mac e a solução para esses problemas:

- [App Store](#App-Store)
- [Apple Pay](#Apple-Pay)
- [Compatibilidade binária](#Binary-Compatibility)
- [Protocolo HTTP de CFNetwork](#CFNetwork-HTTP-Protocol)
- [CloudKit](#CloudKit)
- [Imagem principal](#CoreImage)
- [Notificações](#Notifications)
- [NSUserActivity](#NSUserActivity)
- [Safari](#Safari)

<a name="App-Store" />

## <a name="app-store"></a>Loja de aplicativos

Problemas Conhecidos:

- Ao testar as compras no aplicativo no ambiente de área restrita, a caixa de diálogo de autenticação pode aparecer duas vezes.
- Ao testar compras no aplicativo com conteúdo hospedado no ambiente de área restrita, aparecerá a caixa de diálogo senha toda vez que o aplicativo é colocado em primeiro plano até que o download de conteúdo seja concluída.

<a name="Apple-Pay" />

## <a name="apple-pay"></a>Apple Pay

Se uma expiração incorreto data ou segurança código (CW) é inserido quando adicionar um novo cartão de pagamento para pagamento da Apple, o processo de provisionamento de cartão será encerrado.

<a name="Binary-Compatibility" />

## <a name="binary-compatibility"></a>Compatibilidade binária

Problemas Conhecidos:

- Chamando `NSObject.ValueForKey` será um `null` chave resultará em uma exceção.
- Ambos `NSURLSession` e NSURLConnection` no longer RC4 cipher suites during the TLS handshake for `http://' URLs.
- Aplicativos poderá ser interrompida se eles modificarem a geometria do superview em ambos os `ViewWillLayoutSubviews` ou `LayoutSubviews` métodos.
- Para todas as conexões SSL/TLS, a criptografia simétrica RC4 agora é desabilitada por padrão. Além disso, a API de transporte seguro não oferece mais suporte SSLv3 e é recomendável que o aplicativo parar de usar criptografia SHA-1 e 3DES assim que possível.

<a name="CFNetwork-HTTP-Protocol" />

## <a name="cfnetwork-http-protocol"></a>Protocolo HTTP de CFNetwork

O `HTTPBodyStream` propriedade o `NSMutableURLRequest` classe deve ser definida como um fluxo não aberto desde `NSURLConnection` e `NSURLSession` agora estritamente impõe esse requisito.

<a name="CloudKit" />

## <a name="cloudkit"></a>CloudKit

Operações de execução demorada retornará um _"Você não tem permissão para salvar o arquivo"._ Erro.

<a name="CoreImage" />

## <a name="core-image"></a>Imagem principal

O `CIImageProcessor` API agora dá suporte a uma contagem de imagens de entrada arbitrário. `CIImageProcessor` API que foi incluído no macOS beta Serra 1 será removida.

<a name="Notifications" />

## <a name="notifications"></a>Notificações

Ao trabalhar com extensões de conteúdo de notificação, exibir controladores não estão sendo lançados corretamente e pode resultar em uma falha quando os limites de memória de extensão são atingidos.

<a name="NSUserActivity" />

## <a name="nsuseractivity"></a>NSUserActivity

Após uma operação de entrega, o `UserInfo` propriedade de um `NSUserActivity` objeto pode estar vazio. Chamar explicitamente `BecomeCurrent` `NSUserActivity` objeto como uma solução atual.

<a name="Safari" />

## <a name="safari"></a>Safari

WebGeolocation requer um seguro (`https://`) a URL para funcionar em 10 de iOS e macOS serra para impedir o uso mal-intencionado de dados local.







## <a name="related-links"></a>Links relacionados

- [Amostras de Mac](https://developer.xamarin.com/samples/mac/)
- [O que há de novo nos X 10.12](https://developer.apple.com/library/prerelease/content/releasenotes/MacOSX/WhatsNewInOSX/Articles/OSXv10.html#//apple_ref/doc/uid/TP40017145-SW1)
