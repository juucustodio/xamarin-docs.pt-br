---
title: Acessibilidade do xamarin. Forms
description: Criando um aplicativo acessível garante que o aplicativo seja utilizável por pessoas que abordagem a interface do usuário com uma variedade de necessidades e experiências.
ms.prod: xamarin
ms.assetid: 99B8A8E8-6F5E-46BC-9639-1C4A6D301049
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/15/2018
ms.openlocfilehash: ac0ffbdce6b0c55e8ad9d774d80e3d9b8bf84089
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50116440"
---
# <a name="xamarinforms-accessibility"></a>Acessibilidade do xamarin. Forms

_Criando um aplicativo acessível garante que o aplicativo seja utilizável por pessoas que abordagem a interface do usuário com uma variedade de necessidades e experiências._

Tornando acessível um aplicativo xamarin. Forms significa a pensar sobre o layout e design de muitos elementos de interface do usuário. Para obter diretrizes sobre problemas a serem considerados, consulte o [lista de verificação de acessibilidade](~/cross-platform/app-fundamentals/accessibility.md). Muitas preocupações de acessibilidade, como fontes grandes e as configurações de cor e contraste adequadas já podem ser tratadas por Xamarin.Forms APIs.

O [Android acessibilidade](~/android/app-fundamentals/accessibility.md) e [iOS acessibilidade](~/ios/app-fundamentals/accessibility.md) guias contêm detalhes das APIs nativas expostas pelo Xamarin e o [guia de acessibilidade UWP do MSDN](https://msdn.microsoft.com/windows/uwp/accessibility/basic-accessibility-information) explica o abordagem nativa nessa plataforma. Essas APIs são usadas para implementar totalmente os aplicativos acessíveis em cada plataforma.

Xamarin. Forms não tem *internos* dão suporte para todas as APIs disponíveis em cada uma das plataformas subjacentes de acessibilidade. No entanto, oferece suporte a propriedades de automação de configuração em elementos de interface do usuário para dar suporte a ferramentas de assistência de leitor e de navegação da tela, que é uma das partes mais importantes da criação de aplicativos acessíveis. Para obter mais informações, consulte [propriedades de automação](~/xamarin-forms/app-fundamentals/accessibility/automation-properties.md).

Aplicativos xamarin. Forms também podem ter a ordem de tabulação de controles especificados. Para obter mais informações, consulte [navegação por teclado](~/xamarin-forms/app-fundamentals/accessibility/keyboard.md).

Outras APIs de acessibilidade (como [PostNotification no iOS](~/ios/app-fundamentals/accessibility.md)) pode ser mais adequado para um [ `DependencyService` ](~/xamarin-forms/app-fundamentals/dependency-service/index.md) ou [renderizador personalizado](~/xamarin-forms/app-fundamentals/custom-renderer/index.md) implementação. Eles não são abordados neste guia.

## <a name="testing-accessibility"></a>Teste de acessibilidade

Aplicativos xamarin. Forms normalmente direcionam várias plataformas, o que significa que o teste de acordo com a plataforma, os recursos de acessibilidade. Siga estes links para saber como testar a acessibilidade em cada plataforma:

- [**iOS testes**](~/ios/app-fundamentals/accessibility.md)
- [**Teste de Android**](~/android/app-fundamentals/accessibility.md)
- [**Windows AccScope (MSDN)**](https://msdn.microsoft.com/library/windows/desktop/dn433239)

## <a name="related-links"></a>Links relacionados

- [Acessibilidade de plataforma cruzada](~/cross-platform/app-fundamentals/accessibility.md)
- [Propriedades de automação](~/xamarin-forms/app-fundamentals/accessibility/automation-properties.md)
- [Acessibilidade do teclado](~/xamarin-forms/app-fundamentals/accessibility/keyboard.md)
