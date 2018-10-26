---
title: Xamarin. Mac - macOS serra de solução de problemas
description: Este documento fornece várias dicas de solução de problemas para trabalhar com macOS Sierra em aplicativos xamarin. Mac. Dicas se relacionam com o Mac App Store, Apple Pay, compatibilidade binária, CFNetwork, CloudKit e muito mais.
ms.prod: xamarin
ms.assetid: 323DD5EE-87CE-48E4-B234-1CF61B45A019
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 09/22/2016
ms.openlocfilehash: 1b379bef98e498df4c58ba7209aa46b0b2542fe1
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50108828"
---
# <a name="xamarinmac---macos-sierra-troubleshooting"></a>Xamarin. Mac - macOS serra de solução de problemas

_Este artigo fornece várias dicas de solução de problemas para trabalhar com macOS Sierra em aplicativos xamarin. Mac._

As seções a seguir listam alguns problemas conhecidos que podem ocorrer ao usar o macOS Sierra com xamarin. Mac e a solução para esses problemas:

- [App Store](#App-Store)
- [Apple Pay](#Apple-Pay)
- [Compatibilidade binária](#Binary-Compatibility)
- [Protocolo do HTTP CFNetwork](#CFNetwork-HTTP-Protocol)
- [CloudKit](#CloudKit)
- [Imagem principal](#CoreImage)
- [Notificações](#Notifications)
- [NSUserActivity](#NSUserActivity)
- [Safari](#Safari)

<a name="App-Store" />

## <a name="app-store"></a>App Store

Problemas Conhecidos:

- Ao testar as compras no aplicativo no ambiente de área restrita, a caixa de diálogo de autenticação pode aparecer duas vezes.
- Ao testar as compras no aplicativo com o conteúdo hospedado no ambiente de área restrita, a caixa de diálogo de senha será exibida sempre que o aplicativo é colocado em primeiro plano até que o download de conteúdo seja concluída.

<a name="Apple-Pay" />

## <a name="apple-pay"></a>Apple Pay

Se uma expiração incorreto data ou segurança código (CW) é inserido ao adicionar um novo cartão de pagamento para o Apple Pay, o processo de provisionamento de cartão será encerrado.

<a name="Binary-Compatibility" />

## <a name="binary-compatibility"></a>Compatibilidade binária

Problemas Conhecidos:

- Chamando `NSObject.ValueForKey` será um `null` chave resultará em uma exceção.
- Ambos `NSURLSession` e NSURLConnection` no longer RC4 cipher suites during the TLS handshake for `http://' URLs.
- Aplicativos podem parar de responder se eles modificam a geometria de uma visão em qualquer um de `ViewWillLayoutSubviews` ou `LayoutSubviews` métodos.
- Para todas as conexões SSL/TLS, a criptografia simétrica de RC4 agora é desabilitada por padrão. Além disso, a API de transporte seguro não oferece suporte a SSLv3 e é recomendável que o aplicativo parar de usar criptografia SHA-1 e 3DES assim que possível.

<a name="CFNetwork-HTTP-Protocol" />

## <a name="cfnetwork-http-protocol"></a>Protocolo do HTTP CFNetwork

O `HTTPBodyStream` propriedade do `NSMutableURLRequest` classe deve ser definida como um fluxo não aberto desde `NSURLConnection` e `NSURLSession` agora estritamente impor esse requisito.

<a name="CloudKit" />

## <a name="cloudkit"></a>CloudKit

Operações de longa execução retornará um _"Você não tem permissão para salvar o arquivo."_ Erro.

<a name="CoreImage" />

## <a name="core-image"></a>Imagem principal

O `CIImageProcessor` API agora dá suporte a uma contagem de imagem de entrada arbitrário. `CIImageProcessor` API que foi incluído no macOS beta Sierra 1 será removida.

<a name="Notifications" />

## <a name="notifications"></a>Notificações

Ao trabalhar com extensões de conteúdo de notificação, controladores de exibição não estão sendo lançados corretamente e pode resultar em uma falha quando os limites de memória de extensão são atingidos.

<a name="NSUserActivity" />

## <a name="nsuseractivity"></a>NSUserActivity

Após uma operação de entrega, o `UserInfo` propriedade de um `NSUserActivity` objeto pode estar vazio. Chamar explicitamente `BecomeCurrent` `NSUserActivity` objeto como uma solução alternativa atual.

<a name="Safari" />

## <a name="safari"></a>Safari

WebGeolocation requer um seguro (`https://`) URL do trabalho no iOS 10 e o macOS Sierra para impedir o uso mal-intencionado de dados local.







## <a name="related-links"></a>Links relacionados

- [Amostras de Mac](https://developer.xamarin.com/samples/mac/)
- [O que há de novo nos X 10.12](https://developer.apple.com/library/prerelease/content/releasenotes/MacOSX/WhatsNewInOSX/Articles/OSXv10.html#//apple_ref/doc/uid/TP40017145-SW1)
