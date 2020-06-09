---
title: Solução de problemas de aplicativos do tvOS 10 criados com o Xamarin
description: Este artigo fornece várias dicas de solução de problemas para trabalhar com o tvOS 10 em aplicativos Xamarin. Ele descreve problemas relacionados à loja de aplicativos, compatibilidade binária, CFNetwork HttpProtocol, CloudKit, imagem principal, NSUserActivity e UIKit.
ms.prod: xamarin
ms.assetid: EA5564BB-C415-49A2-B70C-3DBF5E0F3FAB
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/16/2017
ms.openlocfilehash: cd04450d1429092453e6d8b65278d87b5d52e45e
ms.sourcegitcommit: 93e6358aac2ade44e8b800f066405b8bc8df2510
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/09/2020
ms.locfileid: "84571617"
---
# <a name="troubleshooting-tvos-10-apps-built-with-xamarin"></a>Solução de problemas de aplicativos do tvOS 10 criados com o Xamarin

As seções a seguir listam alguns problemas conhecidos que podem ocorrer ao usar o tvOS 10 com o Xamarin e a solução para esses problemas:

- [Loja de Aplicativos](#App-Store)
- [Compatibilidade binária](#Binary-Compatibility)
- [Protocolo HTTP CFNetwork](#CFNetwork-HTTP-Protocol)
- [CloudKit](#CloudKit)
- [Imagem principal](#CoreImage)
- [NSUserActivity](#NSUserActivity)
- [UIKit](#UIKit)

<a name="App-Store"></a>

## <a name="app-store"></a>Loja de aplicativos

Problemas Conhecidos:

- Ao testar compras no aplicativo no ambiente de área restrita, a caixa de diálogo de autenticação pode aparecer duas vezes.
- Ao testar compras no aplicativo com conteúdo hospedado no ambiente de área restrita, a caixa de diálogo de senha será exibida toda vez que o aplicativo for levado para o primeiro plano até que o download do conteúdo seja concluído.

<a name="Binary-Compatibility"></a>

## <a name="binary-compatibility"></a>Compatibilidade binária

Problemas Conhecidos:

- Chamar `NSObject.ValueForKey` será uma `null` chave resultará em uma exceção.
- Fazer referência a uma fonte por nome ao chamar causará `UIFont.WithName` uma falha.
- Ambos `NSURLSession` e `NSURLConnection` não mais conjuntos de codificação RC4 durante o HANDSHAKE de TLS para `http://` URLs.
- Os aplicativos podem parar se modificarem a geometria de uma superexibição nos `ViewWillLayoutSubviews` `LayoutSubviews` métodos ou.
- Para todas as conexões SSL/TLS, a cifra simétrica RC4 agora está desabilitada por padrão. Além disso, a API de transporte seguro não dá mais suporte a SSLv3 e é recomendável que o aplicativo pare de usar a criptografia SHA-1 e 3DES assim que possível.

<a name="CFNetwork-HTTP-Protocol"></a>

## <a name="cfnetwork-http-protocol"></a>Protocolo HTTP CFNetwork

A `HTTPBodyStream` propriedade da `NSMutableURLRequest` classe deve ser definida como um fluxo não aberto, já que `NSURLConnection` e `NSURLSession` agora impõe estritamente esse requisito.

<a name="CloudKit"></a>

## <a name="cloudkit"></a>CloudKit

As operações de longa execução retornarão um _"você não tem permissão para salvar o arquivo"._ erro.

<a name="CoreImage"></a>

## <a name="core-image"></a>Imagem principal

A `CIImageProcessor` API agora dá suporte a uma contagem de imagens de entrada arbitrária. `CIImageProcessor`A API que foi incluída no tvOS 10 beta 1 será removida.

<a name="NSUserActivity"></a>

## <a name="nsuseractivity"></a>NSUserActivity

Após uma operação de entrega, a `UserInfo` propriedade de um `NSUserActivity` objeto pode estar vazia. Chame explicitamente o `BecomeCurrent` objeto NSUserActivity ' como uma solução alternativa atual.

<a name="UIKit"></a>

## <a name="uikit"></a>UIKit

Problemas Conhecidos:

- Muda para a aparência em segundo `UINavigationBar` plano `UITabBar` ou `UIToolBar` pode resultar em uma passagem de layout para resolver a nova aparência. A tentativa de modificar essas aparências dentro de um `LayoutSubviews` `UpdateConstraints` evento, `WillLayoutSubviews` ou `DidUpdateSubviews` pode resultar em um loop de layout infinito.
- No tvOS 10, chamar o `RemoveGestureRecognizer` método de um `UIView` objeto cancela explicitamente qualquer reconhecedor de gesto em andamento.
- Os controladores de exibição apresentados agora podem afetar a aparência da barra de status.
- tvOS 10 exige que o desenvolvedor chame `base.AwakeFromNib` ao subclasse `UIViewController` e substitua o `AwakeFromNib` método.
- Aplicativos com `UIView` subclasses personalizadas que substituem `LayoutSubviews` e sujam o layout antes de chamar `base.LayoutSubviews` podem disparar um loop de layout infinito no tvOS 10.
- Os ativos de imagens invertidas ou de direção específicas não são invertidos quando atribuídos a `UIButton` objetos.

## <a name="related-links"></a>Links relacionados

- [Exemplos do tvOS](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+tvOS)
- [O que há de novo no tvOS 10](https://developer.apple.com/library/prerelease/content/releasenotes/General/WhatsNewinTVOS/Articles/tvOS10.html#//apple_ref/doc/uid/TP40017259-SW1)
