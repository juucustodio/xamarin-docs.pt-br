---
title: Acessibilidade
description: "Criando um aplicativo acessível garante que o aplicativo seja utilizável por pessoas que abordagem a interface do usuário com uma variedade de necessidades e experiências."
ms.topic: article
ms.prod: xamarin
ms.assetid: 99B8A8E8-6F5E-46BC-9639-1C4A6D301049
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2017
ms.openlocfilehash: 54ca72669926822e84cdb96b5195e7cffbe39b52
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/28/2018
---
# <a name="accessibility"></a>Acessibilidade

_Criando um aplicativo acessível garante que o aplicativo seja utilizável por pessoas que abordagem a interface do usuário com uma variedade de necessidades e experiências._

Fazendo com que um aplicativo xamarin. Forms acessível significa pensar sobre o layout e o design de muitos elementos de interface do usuário. Para obter diretrizes sobre problemas a serem considerados, consulte o [lista de verificação de acessibilidade](~/cross-platform/app-fundamentals/accessibility.md). Muitos problemas de acessibilidade, como fontes grandes e as configurações de cor e contraste adequadas já podem ser tratados por Xamarin.Forms APIs.

O [acessibilidade Android](~/android/app-fundamentals/accessibility.md) e [iOS acessibilidade](~/ios/app-fundamentals/accessibility.md) guias contêm detalhes das APIs nativas expostas pelo Xamarin e o [guia de acessibilidade UWP do MSDN](https://msdn.microsoft.com/windows/uwp/accessibility/basic-accessibility-information) explica o abordagem nativo nessa plataforma. Essas APIs são usadas para implementar aplicativos acessíveis por completo em cada plataforma.

Xamarin. Forms não tem *interna* suporte para todas as APIs disponíveis em cada uma das plataformas subjacentes de acessibilidade. No entanto, dá suporte a valores de acessibilidade de configuração em elementos de interface do usuário para dar suporte a ferramentas de assistência de leitor e navegação da tela, que é uma das partes mais importantes de criação de aplicativos acessíveis. Para obter mais informações, consulte [dos valores de configuração de acessibilidade em elementos de Interface de usuário](~/xamarin-forms/app-fundamentals/accessibility/setting-accessibility-values.md).

Outros APIs de acessibilidade (como [PostNotification no iOS](~/ios/app-fundamentals/accessibility.md)) pode ser mais adequado para um [ `DependencyService` ](~/xamarin-forms/app-fundamentals/dependency-service/index.md) ou [renderizador personalizado](~/xamarin-forms/app-fundamentals/custom-renderer/index.md) implementação. Eles não são abordados neste guia.

## <a name="testing-accessibility"></a>Teste de acessibilidade

Aplicativos xamarin. Forms normalmente usam múltiplas plataformas, o que significa que os recursos de acessibilidade de acordo com a plataforma de teste. Siga estes links para aprender a acessibilidade em cada plataforma de teste:

- [**iOS teste**](~/ios/app-fundamentals/accessibility.md)
- [**Teste de Android**](~/android/app-fundamentals/accessibility.md)
- [**Windows AccScope (MSDN)**](https://msdn.microsoft.com/library/windows/desktop/dn433239)


## <a name="related-links"></a>Links relacionados

- [Acessibilidade de plataforma cruzada](~/cross-platform/app-fundamentals/accessibility.md)
- [Configurando valores de acessibilidade em elementos de Interface do usuário](~/xamarin-forms/app-fundamentals/accessibility/setting-accessibility-values.md)
