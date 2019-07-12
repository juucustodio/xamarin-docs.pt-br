---
title: Introdução ao tvOS 10
description: Este artigo apresenta todas as APIs e recursos disponíveis no tvOS 10 novos e modificados para desenvolvedores do xamarin. tvos.
ms.prod: xamarin
ms.assetid: CB9C1EC8-6008-43AD-977E-976AE7C73DD8
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: 6bad05f62fa9168818aa9a2e73cc1e2830af03b3
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67830031"
---
# <a name="introduction-to-tvos-10"></a>Introdução ao tvOS 10

_Este artigo apresenta todas as APIs e recursos disponíveis no tvOS 10 novos e modificados para desenvolvedores do xamarin. tvos._

Com o novo tvOS 10 Apple do SDK inclui novas APIs e serviços que permitem ao desenvolvedor criar novas categorias de aplicativos e recursos. 

Para obter mais informações sobre tvOS 10, consulte da Apple [tvOS + aplicativos](https://developer.apple.com/tvos/) documentação.

## <a name="whats-new-in-tvos-10"></a>O que há de novo no tvOS 10

Apple adicionou várias novas APIs e serviços tvOS 10 junto com muitos aprimoramentos aos recursos existentes, incluindo:

## <a name="new-user-interface-styles"></a>Novos estilos de Interface do usuário

tvOS 10 agora dá suporte a tema um escuro e a Interface do usuário de luz que todos o build no UIKit controles serão automaticamente adaptar, com base nas preferências do usuário.

Ao criar e implementar novos controles personalizados da interface do usuário, o desenvolvedor deve usar o [UITraitCollection](https://developer.apple.com/reference/uikit/uitraitcollection) classe adaptação a tema selecionado do usuário.

Para obter mais informações, consulte nosso [novos estilos de Interface do usuário](~/ios/tvos/platform/user-interface-styles.md) documentação.

## <a name="security-and-privacy-enhancements"></a>Aperfeiçoamentos de segurança e privacidade

Apple fez várias melhorias de segurança e privacidade no tvOS 10 que ajudarão o desenvolvedor melhorar a segurança dos seus aplicativos e garantir a privacidade do usuário final.

Como resultado, os aplicativos em execução no watchOS 3 (ou posterior) estaticamente devem declarar sua intenção para acessar informações do usuário ou recursos específicos, inserindo uma ou mais chaves específicas de privacidade em seus `Info.plist` arquivos explicam ao usuário por que o aplicativo deseja acessar.

Uma vez que tvOS 10 compartilha essas alterações com iOS 10, consulte nosso iOS 10 [aperfeiçoamentos de segurança e privacidade](~/ios/app-fundamentals/security-privacy.md) guia para obter mais informações.

## <a name="video-subscriber-account"></a>Conta de assinante de vídeo

Novo para tvOS 10, a estrutura de conta de assinante de vídeo permite que os aplicativos que suporte autenticado streaming ou vídeo sob demanda para autenticar com seu provedor de TV cabo ou satélite usando uma experiência de logon único para o usuário final.

<!--To find out more, please see our [Video Subscriber Account](~/ios/platform-features/introduction-to-ios10/video-subscriber-account/) guide.-->

## <a name="wide-color"></a>Cores amplas

tvOS 10 estende o suporte para formatos de pixel de intervalo estendido e espaços de cores de toda a gama de cores em todo o sistema, incluindo estruturas como elementos gráficos principais, imagem principal, sistema operacional e AVFoundation. Suporte para dispositivos com telas de cores adicional é facilitado por fornecendo esse comportamento em toda a pilha inteira de gráficos.

Além disso, `UIKit` foi modificado funcionar no novo estendido **sRGB** espaço de cor, que facilita a combinação de cores em gamas de cores amplas sem perda de desempenho significativa.

A Apple oferece as seguintes práticas recomendadas ao trabalhar com cores amplas:

- `UIColor` agora usa o sRGB espaço de cores e não poderá mais clamp valores para o `0.0` para `1.0` intervalo. Se o aplicativo utiliza o comportamento de clamp anterior, ele precisará ser modificado para tvOS 10.
- Se o aplicativo executa o processamento personalizado de `UIImages`, use a nova [UIGraphicsImageRender](https://developer.apple.com/reference/uikit/uigraphicsimagerenderer) classe para especificar o uso de formatos de intervalo estendido ou o intervalo padrão.
- Ao usar uma API de nível inferior, como elementos gráficos principais ou Bare Metal para fornecer processamento de imagem, o aplicativo deve usar um intervalo estendido espaço e pixel formato de cor que dá suporte a valores de ponto flutuante de 16 bits. Quando for necessário, o aplicativo terá serem fixados manualmente os valores de componente de cor.
- Elementos gráficos principais, imagem principal e sombreadores de desempenho do sistema operacional fornecem novos métodos para converter entre os espaços de duas cores.

Para obter mais informações, consulte nosso [Introdução a cores amplas](~/ios/platform/wide-color.md) guia.

## <a name="newly-available-existing-frameworks"></a>Estruturas existentes disponibilizadas recentemente

Várias estruturas que estavam disponíveis no iOS (e não tvOS), foram disponibilizados para tvOS 10, como:

- Por ExternalAccessory
- HomeKit
- MultipeerConnectivity
- Fotos
- ReplayKit
- UserNotification

## <a name="additional-framework-changes"></a>Alterações de estrutura adicionais

Além das alterações de estrutura principais e adições listadas acima, a Apple fez muitas alterações de estrutura secundárias adicionais na tvOS 10.

Para obter mais informações, consulte nosso [alterações adicionais do Framework](~/ios/tvos/platform/introduction-to-tvos10/additional-framework-changes.md) guia.

## <a name="deprecated-apis"></a>APIs obsoletas

Não há APIs ou estruturas foram preteridas por tvOS 10. Consulte da Apple [tvOS 10 diferenças de API](https://developer.apple.com/library/prerelease/content/releasenotes/General/tvOS10APIDiffs/index.html) documentação para obter uma lista completa de modificações de API.



## <a name="related-links"></a>Links relacionados

- [Amostras do tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [O que há de novo no tvOS 10](https://developer.apple.com/library/prerelease/content/releasenotes/General/WhatsNewinTVOS/Articles/tvOS10.html#//apple_ref/doc/uid/TP40017259-SW1)
