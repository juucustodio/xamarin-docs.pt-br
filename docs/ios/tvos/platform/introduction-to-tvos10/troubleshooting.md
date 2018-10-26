---
title: Solução de problemas de tvOS 10 aplicativos criados com o Xamarin
description: Este artigo fornece várias dicas de solução de problemas para trabalhar com tvOS 10 em aplicativos Xamarin. Ele descreve os problemas relacionados a App Store, compatibilidade binária, o CFNetwork HttpProtocol, CloudKit, imagem principal, NSUserActivity e UIKit.
ms.prod: xamarin
ms.assetid: EA5564BB-C415-49A2-B70C-3DBF5E0F3FAB
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: 3815790cfb73f93f399c14d3da44aa3210725388
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50119976"
---
# <a name="troubleshooting-tvos-10-apps-built-with-xamarin"></a>Solução de problemas de tvOS 10 aplicativos criados com o Xamarin

As seções a seguir listam alguns problemas conhecidos que podem ocorrer ao usar tvOS 10 com o Xamarin e a solução para esses problemas:

- [App Store](#App-Store)
- [Compatibilidade binária](#Binary-Compatibility)
- [Protocolo do HTTP CFNetwork](#CFNetwork-HTTP-Protocol)
- [CloudKit](#CloudKit)
- [Imagem principal](#CoreImage)
- [NSUserActivity](#NSUserActivity)
- [UIKit](#UIKit)

<a name="App-Store" />

## <a name="app-store"></a>App Store

Problemas Conhecidos:

 - Ao testar as compras no aplicativo no ambiente de área restrita, a caixa de diálogo de autenticação pode aparecer duas vezes.
 - Ao testar as compras no aplicativo com o conteúdo hospedado no ambiente de área restrita, a caixa de diálogo de senha será exibida sempre que o aplicativo é colocado em primeiro plano até que o download de conteúdo seja concluída.

<a name="Binary-Compatibility" />

## <a name="binary-compatibility"></a>Compatibilidade binária

Problemas Conhecidos:

 - Chamando `NSObject.ValueForKey` será um `null` chave resultará em uma exceção.
 - Referenciando uma fonte por nome ao chamar `UIFont.WithName` causará uma falha.
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

O `CIImageProcessor` API agora dá suporte a uma contagem de imagem de entrada arbitrário. `CIImageProcessor` API que foi incluído na versão beta do tvOS 10 1 será removida.

<a name="NSUserActivity" />

## <a name="nsuseractivity"></a>NSUserActivity

Após uma operação de entrega, o `UserInfo` propriedade de um `NSUserActivity` objeto pode estar vazio. Chamar explicitamente `BecomeCurrent` NSUserActivity' objeto como uma solução alternativa atual.

<a name="UIKit" />

## <a name="uikit"></a>UIKit

Problemas Conhecidos:

 - Alterações à aparência do plano de fundo `UINavigationBar`, `UITabBar` ou `UIToolBar` pode resultar em uma passagem de layout para resolver a nova aparência. A tentativa de modificar essas aparências dentro de um `LayoutSubviews`, `UpdateConstraints`, `WillLayoutSubviews` ou `DidUpdateSubviews` eventos podem resultar em um loop infinito de layout.
 - No tvOS 10, chamando o `RemoveGestureRecognizer` método de um `UIView` objeto explicitamente cancela qualquer reconhecedor de gestos em andamento.
 - Controladores de exibição apresentada agora pode afetar a aparência da barra de status.
 - tvOS 10 exige que o desenvolvedor chamar `base.AwakeFromNib` quando subclasses `UIViewController` e substituindo o `AwakeFromNib` método.
 - Aplicativos com personalizado `UIView` subclasses que substituem `LayoutSubviews` e sujos o layout antes de chamar `base.LayoutSubviews` podem disparar um loop infinito de layout no tvOS 10.
 - Ativos de imagens específicas de direção ou flippable não são quando atribuído a nenhum inversão `UIButton` objetos.

## <a name="related-links"></a>Links relacionados

- [Amostras do tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [O que há de novo no tvOS 10](https://developer.apple.com/library/prerelease/content/releasenotes/General/WhatsNewinTVOS/Articles/tvOS10.html#//apple_ref/doc/uid/TP40017259-SW1)
