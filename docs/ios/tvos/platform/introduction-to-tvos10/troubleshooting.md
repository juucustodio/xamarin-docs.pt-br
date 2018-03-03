---
title: "Solução de problemas"
description: "Este artigo fornece várias dicas de solução de problemas para trabalhar com tvOS 10 em aplicativos de Xamarin.tvOS."
ms.topic: article
ms.prod: xamarin
ms.assetid: EA5564BB-C415-49A2-B70C-3DBF5E0F3FAB
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/16/2017
ms.openlocfilehash: d45b9ed04d3ae4815d408d82068e588a2cbcc6f8
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="troubleshooting"></a>Solução de problemas

_Este artigo fornece várias dicas de solução de problemas para trabalhar com tvOS 10 em aplicativos de Xamarin.tvOS._

As seções a seguir listam alguns problemas conhecidos que podem ocorrer ao usar tvOS 10 com Xamarin.tvOS e a solução para esses problemas:

- [Loja de aplicativos](#App-Store)
- [Compatibilidade binária](#Binary-Compatibility)
- [CFNetwork HTTP Protocol](#CFNetwork-HTTP-Protocol)
- [CloudKit](#CloudKit)
- [CoreImage](#CoreImage)
- [NSUserActivity](#NSUserActivity)
- [UIKit](#UIKit)

<a name="App-Store" />

## <a name="app-store"></a>Loja de aplicativos

Problemas Conhecidos:

 - Ao testar as compras no aplicativo no ambiente de área restrita, a caixa de diálogo de autenticação pode aparecer duas vezes.
 - Ao testar compras no aplicativo com conteúdo hospedado no ambiente de área restrita, aparecerá a caixa de diálogo senha toda vez que o aplicativo é colocado em primeiro plano até que o download de conteúdo seja concluída.

<a name="Binary-Compatibility" />

## <a name="binary-compatibility"></a>Compatibilidade binária

Problemas Conhecidos:

 - Chamando `NSObject.ValueForKey` será um `null` chave resultará em uma exceção.
 - Referenciando uma fonte por nome ao chamar `UIFont.WithName` causará uma falha.
 - Ambos `NSURLSession` e NSURLConnection` no longer RC4 cipher suites during the TLS handshake for `http://' URLs.
 - Aplicativos poderá ser interrompida se eles modificarem a geometria do superview em ambos os `ViewWillLayoutSubviews` ou `LayoutSubviews` métodos.
 - Para todas as conexões SSL/TLS, a criptografia simétrica RC4 agora é desabilitada por padrão. Além disso, a API de transporte seguro não oferece mais suporte SSLv3 e é recomendável que o aplicativo parar de usar criptografia SHA-1 e 3DES assim que possível.

<a name="CFNetwork-HTTP-Protocol" />

## <a name="cfnetwork-http-protocol"></a>CFNetwork HTTP Protocol

O `HTTPBodyStream` propriedade o `NSMutableURLRequest` classe deve ser definida como um fluxo não aberto desde `NSURLConnection` e `NSURLSession` agora estritamente impõe esse requisito.

<a name="CloudKit" />

## <a name="cloudkit"></a>CloudKit

Operações de execução demorada retornará um _"Você não tem permissão para salvar o arquivo"._ Erro.

<a name="CoreImage" />

## <a name="coreimage"></a>CoreImage

O `CIImageProcessor` API agora dá suporte a uma contagem de imagens de entrada arbitrário. `CIImageProcessor` API que foi incluído na versão beta do tvOS 10 1 será removida.

<a name="NSUserActivity" />

## <a name="nsuseractivity"></a>NSUserActivity

Após uma operação de entrega, o `UserInfo` propriedade de um `NSUserActivity` objeto pode estar vazio. Chamar explicitamente `BecomeCurrent` NSUserActivity' objeto como uma solução atual.

<a name="UIKit" />

## <a name="uikit"></a>UIKit

Problemas Conhecidos:

 - Muda para a aparência do plano de fundo de `UINavigationBar`, `UITabBar` ou `UIToolBar` pode resultar em uma passagem de layout para resolver a nova aparência. Tentando modificar essas aparências dentro de um `LayoutSubviews`, `UpdateConstraints`, `WillLayoutSubviews` ou `DidUpdateSubviews` evento pode resultar em um loop infinito de layout.
 - Em tvOS 10, chamando o `RemoveGestureRecognizer` método de um `UIView` objeto explicitamente cancela qualquer reconhecedor de gestos em andamento.
 - Exibir controladores apresentada agora podem afetar a aparência da barra de status.
 - tvOS 10 requer que o desenvolvedor chamar `base.AwakeFromNib` quando subclasses `UIViewController` e substituindo o `AwakeFromNib` método.
 - Aplicativos com personalizado `UIView` subclasses substituam `LayoutSubviews` e sujos o layout antes de chamar `base.LayoutSubviews` pode disparar um loop infinito de layout no tvOS 10.
 - Ativos de direção específica ou flippable imagens não são quando atribuído a nenhum inversão `UIButton` objetos.





## <a name="related-links"></a>Links relacionados

- [Exemplos de tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [O que há de novo no tvOS 10](https://developer.apple.com/library/prerelease/content/releasenotes/General/WhatsNewinTVOS/Articles/tvOS10.html#//apple_ref/doc/uid/TP40017259-SW1)
