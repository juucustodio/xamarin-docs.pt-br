---
title: Solução de problemas de aplicativos do tvOS 10 criados com o Xamarin
description: Este artigo fornece várias dicas de solução de problemas para trabalhar com o tvOS 10 em aplicativos Xamarin. Ele descreve problemas relacionados à loja de aplicativos, compatibilidade binária, CFNetwork HttpProtocol, CloudKit, imagem principal, NSUserActivity e UIKit.
ms.prod: xamarin
ms.assetid: EA5564BB-C415-49A2-B70C-3DBF5E0F3FAB
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: 54a4bf2a6f575a55ce9dde8ec87c93e0d56acf9c
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68657408"
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

- Chamar `NSObject.ValueForKey` será uma `null` chave resultará em uma exceção.
- Fazer referência a uma fonte por nome `UIFont.WithName` ao chamar causará uma falha.
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

A `CIImageProcessor` API agora dá suporte a uma contagem de imagens de entrada arbitrária. `CIImageProcessor`A API que foi incluída no tvOS 10 beta 1 será removida.

<a name="NSUserActivity" />

## <a name="nsuseractivity"></a>NSUserActivity

Após uma operação de entrega, `UserInfo` a propriedade de `NSUserActivity` um objeto pode estar vazia. Chame `BecomeCurrent` explicitamente o objeto NSUserActivity ' como uma solução alternativa atual.

<a name="UIKit" />

## <a name="uikit"></a>UIKit

Problemas Conhecidos:

- Muda para a aparência `UINavigationBar` `UITabBar` em segundo plano ou `UIToolBar` pode resultar em uma passagem de layout para resolver a nova aparência. A tentativa de modificar essas aparências dentro de um `LayoutSubviews`evento `UpdateConstraints` `WillLayoutSubviews` , ou `DidUpdateSubviews` pode resultar em um loop de layout infinito.
- No tvOS 10, chamar o `RemoveGestureRecognizer` método de um `UIView` objeto cancela explicitamente qualquer reconhecedor de gesto em andamento.
- Os controladores de exibição apresentados agora podem afetar a aparência da barra de status.
- tvOS 10 exige que o desenvolvedor chame `base.AwakeFromNib` ao `UIViewController` subclasse e substitua o `AwakeFromNib` método.
- Aplicativos com subclasses personalizadas `UIView` que substituem `LayoutSubviews` e sujam o layout `base.LayoutSubviews` antes de chamar podem disparar um loop de layout infinito no tvOS 10.
- Os ativos de imagens invertidas ou de direção específicas não são invertidos quando atribuídos a `UIButton` objetos.

## <a name="related-links"></a>Links relacionados

- [Amostras do tvOS](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+tvOS)
- [O que há de novo no tvOS 10](https://developer.apple.com/library/prerelease/content/releasenotes/General/WhatsNewinTVOS/Articles/tvOS10.html#//apple_ref/doc/uid/TP40017259-SW1)
