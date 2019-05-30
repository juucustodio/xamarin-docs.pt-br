---
title: Acessibilidade do Xamarin.Forms
description: Criar um aplicativo acessível garante que o aplicativo seja utilizável por pessoas que abordem a interface do usuário com uma variedade de necessidades e experiências.
ms.prod: xamarin
ms.assetid: 99B8A8E8-6F5E-46BC-9639-1C4A6D301049
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/28/2019
ms.custom: video
ms.openlocfilehash: 2aaa61400d3775c4d622b805d24ff0b338017de5
ms.sourcegitcommit: 4a1520dee7759f8355ea65c8bb3d1bac8ba58122
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/29/2019
ms.locfileid: "66354037"
---
# <a name="xamarinforms-accessibility"></a>Acessibilidade do Xamarin.Forms

_Criar um aplicativo acessível garante que o aplicativo seja utilizável por pessoas que abordem a interface do usuário com uma variedade de necessidades e experiências._

Tornar um aplicativo Xamarin.Forms disponível significa pensar sobre o layout e o design de muitos elementos da interface do usuário. Para obter diretrizes sobre problemas a serem considerados, consulte a [lista de verificação de acessibilidade](~/cross-platform/app-fundamentals/accessibility.md). Muitas preocupações de acessibilidade, tais como fontes grandes e configurações de cor e contraste adequadas, já podem ser tratadas por APIs do Xamarin.Forms.

Os guias [Acessibilidade do Android](~/android/app-fundamentals/accessibility.md) e [Acessibilidade iOS](~/ios/app-fundamentals/accessibility.md) contêm detalhes das APIs nativas expostas pelo Xamarin, enquanto o [guia de acessibilidade da Plataforma Universal do Windows no MSDN](https://msdn.microsoft.com/windows/uwp/accessibility/basic-accessibility-information) explica a abordagem nativa nessa plataforma. Essas APIs são usadas para implementar totalmente aplicativos acessíveis em cada plataforma.

O Xamarin.Forms não tem suporte *interno* para todas as APIs de acessibilidade disponíveis em cada uma das plataformas subjacentes. No entanto, ele dá suporte a propriedades de automação de configuração em elementos de interface do usuário para dar suporte a ferramentas de assistência de navegação e de leitor de tela, uma das partes mais importantes da criação de aplicativos acessíveis. Para obter mais informações, veja [Propriedades de automação](~/xamarin-forms/app-fundamentals/accessibility/automation-properties.md).

Os aplicativos Xamarin.Forms também podem ter a ordem de tabulação de controles especificada, a fim de melhorar a usabilidade e a acessibilidade. Para saber mais, confira [Acessibilidade do teclado](~/xamarin-forms/app-fundamentals/accessibility/keyboard.md).

Outras APIs de acessibilidade (assim como [PostNotification no iOS](~/ios/app-fundamentals/accessibility.md)) podem ser mais adequadas para uma implementação de [`DependencyService`](~/xamarin-forms/app-fundamentals/dependency-service/index.md) ou de [renderizador personalizado](~/xamarin-forms/app-fundamentals/custom-renderer/index.md). Elas não são abordadas neste guia.

## <a name="testing-accessibility"></a>Testando a acessibilidade

Aplicativos Xamarin.Forms normalmente são direcionados a várias plataformas, o que significa testar os recursos de acessibilidade de acordo com a plataforma. Siga estes links para saber como testar a acessibilidade em cada plataforma:

- [**Testes de iOS**](~/ios/app-fundamentals/accessibility.md)
- [**Testes de Android**](~/android/app-fundamentals/accessibility.md)
- [**Windows AccScope (MSDN)** ](https://msdn.microsoft.com/library/windows/desktop/dn433239)

## <a name="related-links"></a>Links relacionados

- [Acessibilidade multiplataforma](~/cross-platform/app-fundamentals/accessibility.md)
- [Propriedades de automação](~/xamarin-forms/app-fundamentals/accessibility/automation-properties.md)
- [Acessibilidade do teclado](~/xamarin-forms/app-fundamentals/accessibility/keyboard.md)

## <a name="related-video"></a>Vídeo relacionados

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Making-Mobile-Apps-Accessible/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
