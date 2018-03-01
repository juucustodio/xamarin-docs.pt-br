---
title: "Introdução às tvOS 10"
description: "Este artigo apresenta todas as APIs e recursos disponíveis no tvOS 10 novos e modificados para desenvolvedores de Xamarin.tvOS."
ms.topic: article
ms.prod: xamarin
ms.assetid: CB9C1EC8-6008-43AD-977E-976AE7C73DD8
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/16/2017
ms.openlocfilehash: 8bc379e507287cd609dca8440b1210b7f1514114
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="introduction-to-tvos-10"></a>Introdução às tvOS 10

_Este artigo apresenta todas as APIs e recursos disponíveis no tvOS 10 novos e modificados para desenvolvedores de Xamarin.tvOS._

Com o novo tvOS 10 Apple SDK inclui novas APIs e serviços que permitem que o desenvolvedor criar novas categorias de aplicativos e recursos. 

Para obter mais informações sobre tvOS 10, consulte da Apple [tvOS + aplicativos](https://developer.apple.com/tvos/) documentação.

## <a name="whats-new-in-tvos-10"></a>O que há de novo no tvOS 10

Apple adicionou várias novas APIs e serviços tvOS 10 junto com muitas melhorias em recursos existentes, incluindo:

## <a name="new-user-interface-styles"></a>Novos estilos de Interface do usuário

tvOS 10 agora dá suporte a adaptar o tema um escuro e a Interface de usuário de luz todos UIKit interna controla será automaticamente, com base nas preferências do usuário.

Ao criar e implementar os novos controles de interface do usuário personalizados, o desenvolvedor deve usar o [UITraitCollection](https://developer.apple.com/reference/uikit/uitraitcollection) classe adaptar o tema selecionado do usuário.

Para obter mais informações, consulte nosso [novos estilos de Interface do usuário](~/ios/tvos/platform/user-interface-styles.md) documentação.

## <a name="security-and-privacy-enhancements"></a>Segurança e privacidade aprimoramentos

Apple fez diversos aprimoramentos de segurança e privacidade no tvOS 10 que podem ajudar o desenvolvedor de melhorar a segurança dos seus aplicativos e garantir a privacidade do usuário final.

Como resultado, os aplicativos em execução no watchOS 3 (ou posterior) estaticamente devem declarar sua intenção para acessar informações do usuário ou recursos específicos, inserindo uma ou mais chaves específica de privacidade em seus `Info.plist` arquivos explicam para o usuário por que o aplicativo deseja acessar.

Como tvOS 10 compartilha essas alterações com iOS 10, consulte nosso iOS 10 [segurança e privacidade aprimoramentos](~/ios/app-fundamentals/security-privacy.md) guia para obter mais informações.

## <a name="video-subscriber-account"></a>Conta do assinante de vídeo

Novo para tvOS 10, a estrutura de conta do assinante de vídeo permite que aplicativos que suporte autenticado streaming ou vídeo sob demanda para autenticar com seu provedor de cabo ou satélite de TV, usando uma experiência de Single-Sign-in para o usuário final.

<!--To find out more, please see our [Video Subscriber Account](~/ios/platform-features/introduction-to-ios10/video-subscriber-account/) guide.-->

## <a name="wide-color"></a>Cores de largura

tvOS 10 estende o suporte para formatos de pixel do intervalo estendido e espaços de toda a gama de cores em todo o sistema, incluindo estruturas, como gráficos de núcleo, imagem principal, sistema operacional e AVFoundation. Suporte para dispositivos com telas de cores de largura é facilitado mais fornecendo esse comportamento em toda a pilha do gráfico inteiro.

Além disso, `UIKit` foi modificado para funcionar no novo estendida **sRGB** espaço de cor, tornando mais fácil misturar cores gamas de cor ampla sem perda de desempenho significativa.

Apple oferece as seguintes práticas recomendadas ao trabalhar com cores amplas:

 - `UIColor` agora usa o sRGB espaço de cores e não poderá mais clamp valores para o `0.0` para `1.0` intervalo. Se o aplicativo utiliza o comportamento de fixação anterior, ele precisará ser modificado para tvOS 10.
 - Se o aplicativo executa processamento personalizado de `UIImages`, use o novo [UIGraphicsImageRender](https://developer.apple.com/reference/uikit/uigraphicsimagerenderer) classe para especificar o uso de formatos de intervalo estendido ou intervalo padrão.
 - Ao usar uma API de nível inferior, como gráficos de núcleo ou sistema operacional para fornecer processamento de imagem, o aplicativo deve usar um intervalo estendido cor espaço e pixel formato que oferece suporte a valores de ponto flutuante de 16 bits. Quando necessário, o aplicativo precisará clamp manualmente os valores de componente de cor.
 - Gráficos de núcleo, imagem principal e sombreadores de desempenho do sistema operacional fornecem novos métodos para converter entre os espaços de duas cores.

Para obter mais informações, consulte nosso [Introdução à cor ampla](~/ios/platform/wide-color.md) guia.

## <a name="newly-available-existing-frameworks"></a>Estruturas existentes recentemente disponíveis

Várias estruturas que estavam disponíveis no iOS (e não tvOS), foram disponibilizados para tvOS 10, como:

 - ExternalAccessory
 - HomeKit
 - MultipeerConnectivity
 - Fotos
 - ReplayKit
 - UserNotification

## <a name="additional-framework-changes"></a>Alterações de estrutura adicionais

Além das alterações de estrutura principais e adições listadas acima, a Apple fez muitas alterações de estrutura secundárias adicionais na tvOS 10.

Para obter mais informações, consulte nosso [alterações adicionais do Framework](~/ios/tvos/platform/introduction-to-tvos10/additional-framework-changes.md) guia.

## <a name="deprecated-apis"></a>APIs obsoletas

Nenhum APIs ou estruturas foram substituídas pelo tvOS 10. Consulte da Apple [tvOS 10 diferenças de API](https://developer.apple.com/library/prerelease/content/releasenotes/General/tvOS10APIDiffs/index.html) documentação para obter uma lista completa das modificações de API.



## <a name="related-links"></a>Links relacionados

- [Exemplos de tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [O que há de novo no tvOS 10](https://developer.apple.com/library/prerelease/content/releasenotes/General/WhatsNewinTVOS/Articles/tvOS10.html#//apple_ref/doc/uid/TP40017259-SW1)
