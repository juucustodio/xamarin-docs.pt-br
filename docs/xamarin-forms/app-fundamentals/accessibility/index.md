---
title: Xamarin.Forms Acessibilidade
description: ''
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.custom: ''
ms.openlocfilehash: 7ac8b305ae09e005013aea9f83fb4a3e4740f2b2
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/28/2020
ms.locfileid: "84129786"
---
# <a name="xamarinforms-accessibility"></a>Xamarin.Forms Acessibilidade

_Criar um aplicativo acessível garante que o aplicativo seja utilizável por pessoas que abordem a interface do usuário com uma variedade de necessidades e experiências._

Tornar um Xamarin.Forms aplicativo acessível significa pensar no layout e no design de muitos elementos da interface do usuário. Para obter diretrizes sobre problemas a serem considerados, consulte a [lista de verificação de acessibilidade](~/cross-platform/app-fundamentals/accessibility.md). Muitas questões de acessibilidade, como fontes grandes, e configurações adequadas de cor e contraste já podem ser abordadas por Xamarin.Forms APIs.

Os guias [Acessibilidade do Android](~/android/app-fundamentals/accessibility.md) e [Acessibilidade iOS](~/ios/app-fundamentals/accessibility.md) contêm detalhes das APIs nativas expostas pelo Xamarin, enquanto o [guia de acessibilidade da Plataforma Universal do Windows no MSDN](https://msdn.microsoft.com/windows/uwp/accessibility/basic-accessibility-information) explica a abordagem nativa nessa plataforma. Essas APIs são usadas para implementar totalmente aplicativos acessíveis em cada plataforma.

Xamarin.Formsno momento, o não tem suporte *interno* para todas as APIs de acessibilidade disponíveis em cada uma das plataformas subjacentes. No entanto, ele dá suporte a propriedades de automação de configuração em elementos de interface do usuário para dar suporte a ferramentas de assistência de navegação e de leitor de tela, uma das partes mais importantes da criação de aplicativos acessíveis. Para obter mais informações, veja [Propriedades de automação](~/xamarin-forms/app-fundamentals/accessibility/automation-properties.md).

Xamarin.Formsos aplicativos também podem ter a ordem de tabulação dos controles especificados, para melhorar a usabilidade e a acessibilidade. Para saber mais, confira [Acessibilidade do teclado](~/xamarin-forms/app-fundamentals/accessibility/keyboard.md).

Outras APIs de acessibilidade (assim como [PostNotification no iOS](~/ios/app-fundamentals/accessibility.md)) podem ser mais adequadas para uma implementação de [`DependencyService`](~/xamarin-forms/app-fundamentals/dependency-service/index.md) ou de [renderizador personalizado](~/xamarin-forms/app-fundamentals/custom-renderer/index.md). Elas não são abordadas neste guia.

## <a name="testing-accessibility"></a>Testando a acessibilidade

Xamarin.FormsNormalmente, os aplicativos visam várias plataformas, o que significa testar os recursos de acessibilidade de acordo com a plataforma. Siga estes links para saber como testar a acessibilidade em cada plataforma:

- [**Testes de iOS**](~/ios/app-fundamentals/accessibility.md)
- [**Testes de Android**](~/android/app-fundamentals/accessibility.md)
- [**Windows AccScope (MSDN)**](https://msdn.microsoft.com/library/windows/desktop/dn433239)

## <a name="related-links"></a>Links relacionados

- [Acessibilidade multiplataforma](~/cross-platform/app-fundamentals/accessibility.md)
- [Propriedades de automação](~/xamarin-forms/app-fundamentals/accessibility/automation-properties.md)
- [Acessibilidade do teclado](~/xamarin-forms/app-fundamentals/accessibility/keyboard.md)

## <a name="related-video"></a>Vídeo relacionados

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Making-Mobile-Apps-Accessible/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
