---
title: Solução de problemas de aplicativos do tvOS 10 criados com o Xamarin
description: Este artigo fornece várias dicas de solução de problemas para trabalhar com o tvOS 10 em aplicativos Xamarin. Ele descreve problemas relacionados à loja de aplicativos, compatibilidade binária, CFNetwork HttpProtocol, CloudKit, imagem principal, NSUserActivity e UIKit.
ms.prod: xamarin
ms.assetid: EA5564BB-C415-49A2-B70C-3DBF5E0F3FAB
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/16/2017
ms.openlocfilehash: a6588dee675aee3e2580b70dfdea2920c6235775
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73030608"
---
# <a name="troubleshooting-tvos-10-apps-built-with-xamarin"></a>Solução de problemas de aplicativos do tvOS 10 criados com o Xamarin

As seções a seguir listam alguns problemas conhecidos que podem ocorrer ao usar o tvOS 10 com o Xamarin e a solução para esses problemas:

- [App Store](#App-Store)
- [Compatibilidade binária](#Binary-Compatibility)
- [Protocolo HTTP CFNetwork](#CFNetwork-HTTP-Protocol)
- [CloudKit](#CloudKit)
- [Imagem principal](#CoreImage)
- [NSUserActivity](#NSUserActivity)
- [UIKit](#UIKit)

<a name="App-Store" />

## <a name="app-store"></a>App Store

Problemas Conhecidos:

- Ao testar compras no aplicativo no ambiente de área restrita, a caixa de diálogo de autenticação pode aparecer duas vezes.
- Ao testar compras no aplicativo com conteúdo hospedado no ambiente de área restrita, a caixa de diálogo de senha será exibida toda vez que o aplicativo for levado para o primeiro plano até que o download do conteúdo seja concluído.

<a name="Binary-Compatibility" />

## <a name="binary-compatibility"></a>Compatibilidade binária

Problemas Conhecidos:

- Chamar `NSObject.ValueForKey` será uma chave de `null` resultará em uma exceção.
- Fazer referência a uma fonte por nome ao chamar `UIFont.WithName` causará uma falha.
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

A API de `CIImageProcessor` agora dá suporte a uma contagem de imagens de entrada arbitrária. `CIImageProcessor` API que foi incluída no tvOS 10 beta 1 será removida.

<a name="NSUserActivity" />

## <a name="nsuseractivity"></a>NSUserActivity

Após uma operação de entrega, a propriedade `UserInfo` de um objeto `NSUserActivity` pode estar vazia. Chame explicitamente `BecomeCurrent` objeto ' NSUserActivity ' como uma solução alternativa atual.

<a name="UIKit" />

## <a name="uikit"></a>UIKit

Problemas Conhecidos:

- Alterações na aparência em segundo plano de `UINavigationBar`, `UITabBar` ou `UIToolBar` podem resultar em uma passagem de layout para resolver a nova aparência. A tentativa de modificar essas aparências dentro de um `LayoutSubviews`, `UpdateConstraints`, `WillLayoutSubviews` ou `DidUpdateSubviews` evento pode resultar em um loop de layout infinito.
- No tvOS 10, chamar o método `RemoveGestureRecognizer` de um objeto `UIView` cancela explicitamente qualquer reconhecedor de gesto em andamento.
- Os controladores de exibição apresentados agora podem afetar a aparência da barra de status.
- o tvOS 10 exige que o desenvolvedor chame `base.AwakeFromNib` ao fazer a subclasse `UIViewController` e substituir o método `AwakeFromNib`.
- Aplicativos com subclasses `UIView` personalizadas que substituem `LayoutSubviews` e sujam o layout antes de chamar `base.LayoutSubviews` podem disparar um loop de layout infinito no tvOS 10.
- Os ativos de imagens invertidas ou de direção específicas não são invertidos quando atribuídos a objetos `UIButton`.

## <a name="related-links"></a>Links relacionados

- [Amostras do tvOS](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+tvOS)
- [O que há de novo no tvOS 10](https://developer.apple.com/library/prerelease/content/releasenotes/General/WhatsNewinTVOS/Articles/tvOS10.html#//apple_ref/doc/uid/TP40017259-SW1)
