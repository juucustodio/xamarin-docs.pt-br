---
title: Introdução ao tvOS 10
description: Este artigo apresenta todas as APIs e recursos novos e modificados disponíveis no tvOS 10 para desenvolvedores do Xamarin. tvOS.
ms.prod: xamarin
ms.assetid: CB9C1EC8-6008-43AD-977E-976AE7C73DD8
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/16/2017
ms.openlocfilehash: 02fda984d65bb89ac3dc8a4ae5e15e2c61ec7d90
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73030623"
---
# <a name="introduction-to-tvos-10"></a>Introdução ao tvOS 10

_Este artigo apresenta todas as APIs e recursos novos e modificados disponíveis no tvOS 10 para desenvolvedores do Xamarin. tvOS._

Com o novo SDK do tvOS 10, a Apple incluiu novas APIs e serviços que permitem ao desenvolvedor criar novas categorias de aplicativos e recursos. 

Para obter mais informações sobre o tvOS 10, consulte a documentação do [tvOS + aplicativos](https://developer.apple.com/tvos/) da Apple.

## <a name="whats-new-in-tvos-10"></a>O que há de novo no tvOS 10

A Apple adicionou várias novas APIs e serviços no tvOS 10, juntamente com vários aprimoramentos nos recursos existentes, incluindo:

## <a name="new-user-interface-styles"></a>Novos estilos de interface do usuário

o tvOS 10 agora dá suporte a um tema de interface do usuário escuro e leve que todos os controles de UIKit de compilação se adaptam automaticamente ao, com base nas preferências do usuário.

Ao criar e implementar novos controles de interface do usuário personalizados, o desenvolvedor deve usar a classe [UITraitCollection](https://developer.apple.com/reference/uikit/uitraitcollection) para se adaptar ao tema selecionado do usuário.

Para obter mais informações, consulte nossa [nova](~/ios/tvos/platform/user-interface-styles.md) documentação de estilos de interface do usuário.

## <a name="security-and-privacy-enhancements"></a>Aprimoramentos de segurança e privacidade

A Apple fez vários aprimoramentos na segurança e na privacidade no tvOS 10, que ajudarão o desenvolvedor a melhorar a segurança de seus aplicativos e garantir a privacidade do usuário final.

Como resultado, os aplicativos executados no watchOS 3 (ou posterior) devem declarar estaticamente sua intenção de acessar recursos específicos ou informações do usuário inserindo uma ou mais chaves de privacidade específicas em seus arquivos de `Info.plist` que explicam ao usuário por que o aplicativo deseja obter acesso.

Como o tvOS 10 compartilha essas alterações com o iOS 10, consulte nosso guia de [aprimoramentos de segurança e privacidade](~/ios/app-fundamentals/security-privacy.md) do IOS 10 para obter mais informações.

## <a name="video-subscriber-account"></a>Conta do assinante de vídeo

Novo para o tvOS 10, a estrutura de conta do assinante de vídeo permite que aplicativos que dão suporte a streaming autenticado ou vídeo sob demanda sejam autenticados com seu cabo ou provedor de TV por satélite usando uma experiência de logon único para o usuário final.

<!--To find out more, please see our [Video Subscriber Account](~/ios/platform-features/introduction-to-ios10/video-subscriber-account/) guide.-->

## <a name="wide-color"></a>Cores amplas

o tvOS 10 estende o suporte para formatos de pixel de intervalo estendido e espaços de cores de ampla gama em todo o sistema, incluindo estruturas como gráficos principais, imagem principal, metal e AVFoundation. O suporte para dispositivos com monitores largos de cores é mais facilitou, fornecendo esse comportamento em toda a pilha gráfica.

Além disso, `UIKit` foi modificado para funcionar no novo colorspace de **sRGB** estendido, facilitando a combinação de cores em gamas de cores amplas sem perda significativa de desempenho.

A Apple oferece as seguintes práticas recomendadas ao trabalhar com cores amplas:

- `UIColor` agora usa o espaço de cores sRGB e não fixe mais valores para a `0.0` para `1.0` intervalo. Se o aplicativo depender do comportamento anterior do fixe, ele precisará ser modificado para o tvOS 10.
- Se o aplicativo executar renderização personalizada de `UIImages`, use a nova classe [UIGraphicsImageRender](https://developer.apple.com/reference/uikit/uigraphicsimagerenderer) para especificar o uso dos formatos de intervalo estendido ou de intervalo padrão.
- Ao usar uma API de nível baixo, como gráficos principal ou metal para fornecer processamento de imagens, o aplicativo deve usar um espaço de cores de intervalo estendido e um formato de pixel que dê suporte a valores de ponto flutuante de 16 bits. Quando necessário, o aplicativo terá que fixe manualmente os valores de componente de cor.
- Os principais gráficos, a imagem principal e os sombreadores de desempenho de metal fornecem novos métodos para conversão entre os dois espaços de cores.

Para obter mais informações, consulte nosso [introdução ao guia de cores largo](~/ios/platform/wide-color.md) .

## <a name="newly-available-existing-frameworks"></a>Estruturas existentes recentemente disponíveis

Várias estruturas que estavam disponíveis no iOS (e não tvOS) foram disponibilizadas para o tvOS 10, como:

- ExternalAccessory
- HomeKit
- MultipeerConnectivity
- Fotos
- ReplayKit
- Usernotification

## <a name="additional-framework-changes"></a>Alterações de estrutura adicionais

Além das principais alterações de estrutura e adições listadas acima, a Apple fez muitas alterações de estrutura secundárias adicionais no tvOS 10.

Para saber mais, consulte nosso guia de [alterações de estrutura adicionais](~/ios/tvos/platform/introduction-to-tvos10/additional-framework-changes.md) .

## <a name="deprecated-apis"></a>APIs obsoletas

Nenhuma API ou estrutura foi preterida por tvOS 10. Consulte a documentação de [diferenças da API do tvOS 10](https://developer.apple.com/library/prerelease/content/releasenotes/General/tvOS10APIDiffs/index.html) da Apple para obter uma lista completa de modificações de API.

## <a name="related-links"></a>Links relacionados

- [Amostras do tvOS](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+tvOS)
- [O que há de novo no tvOS 10](https://developer.apple.com/library/prerelease/content/releasenotes/General/WhatsNewinTVOS/Articles/tvOS10.html#//apple_ref/doc/uid/TP40017259-SW1)
