---
title: Introdução aos efeitos
description: O Effects permite que os controles nativos em cada plataforma sejam personalizados e são geralmente usados para estilo pequenas alterações. Este artigo fornece uma introdução aos efeitos, descreve o limite entre os efeitos e renderizadores personalizados e descreve a classe PlatformEffect.
ms.prod: xamarin
ms.assetid: 30CB8615-8F39-4762-BDB7-333D2B57D112
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/08/2016
ms.openlocfilehash: d3fa958e999a10832d5fa15e4190077955b0e6df
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/12/2018
ms.locfileid: "38997369"
---
# <a name="introduction-to-effects"></a>Introdução aos efeitos

_O Effects permite que os controles nativos em cada plataforma sejam personalizados e são geralmente usados para estilo pequenas alterações. Este artigo fornece uma introdução aos efeitos, descreve o limite entre os efeitos e renderizadores personalizados e descreve a classe PlatformEffect._

Xamarin. Forms [páginas, Layouts e controles](~/xamarin-forms/user-interface/controls/index.md) apresenta uma API comum para descrever interfaces de usuário móvel de plataforma cruzada. Cada página, layout e controle é renderizado diferentemente em cada plataforma usando uma `Renderer` classe que por sua vez cria um controle nativo (correspondente à representação do xamarin. Forms), organiza sua disposição na tela e adiciona o comportamento especificado no compartilhado código.

Os desenvolvedores podem implementar suas próprias classes `Renderer` personalizadas para personalizar a aparência e/ou o comportamento de um controle. No entanto, implementando uma classe de renderizador personalizado para executar uma personalização de controle simples geralmente é uma resposta detalhada. Efeitos de simplificam esse processo, permitindo que os controles nativos em cada plataforma para ser mais facilmente personalizado.

Os efeitos são criados em projetos específicos da plataforma Subclassificando o `PlatformEffect` controle e, em seguida, os efeitos são consumidos anexando-os a um controle apropriado em um projeto de biblioteca compartilhada ou uma biblioteca do xamarin. Forms .NET Standard.

## <a name="why-use-an-effect-over-a-custom-renderer"></a>Por que usar um efeito ao longo de um renderizador personalizado?

Efeitos de simplificam a personalização de um controle, são reutilizáveis e podem ser parametrizados para aumentar ainda mais a reutilização.

Tudo o que pode ser obtida com um efeito também pode ser obtido com um renderizador personalizado. No entanto, os renderizadores personalizados oferecem mais flexibilidade e personalização de efeitos. As diretrizes a seguir listam as circunstâncias em que você pode escolher um efeito ao longo de um renderizador personalizado:

- Um efeito é recomendado quando alterando as propriedades de um controle específico da plataforma, obterá o resultado desejado.
- Um renderizador personalizado é necessário quando é necessário para substituir os métodos de um controle específico da plataforma.
- Um renderizador personalizado é necessário quando é necessário para substituir o controle específico da plataforma que implementa um controle xamarin. Forms.

## <a name="subclassing-the-platformeffect-class"></a>Criando subclasses da classe PlatformEffect

A tabela a seguir lista o namespace para o `PlatformEffect` classe em cada plataforma e os tipos de suas propriedades:

|Plataforma|Namespace|Contêiner|Controle|
|--- |--- |--- |--- |
|iOS|Xamarin.Forms.Platform.iOS|UIView|UIView|
|Android|Xamarin.Forms.Platform.Android|ViewGroup|Exibir|
|UWP (Plataforma Universal do Windows)|Xamarin.Forms.Platform.UWP|FrameworkElement|FrameworkElement|

Cada específicos da plataforma `PlatformEffect` classe expõe as propriedades a seguir:

- `Container` – faz referência ao controle específico da plataforma que está sendo usado para implementar o layout.
- `Control` – faz referência ao controle específico da plataforma que está sendo usado para implementar o controle do xamarin. Forms.
- `Element` – faz referência ao controle do xamarin. Forms que está sendo renderizado.

Efeitos não têm informações sobre o contêiner, controle ou elemento, que eles são anexados a porque eles podem ser anexados a qualquer elemento de tipo. Portanto, quando um efeito é anexado a um elemento que não dá suporte a ele deve degradar normalmente ou lançar uma exceção. No entanto, o `Container`, `Control`, e `Element` propriedades podem ser convertidas em seu tipo de implementação. Para obter mais informações sobre esses tipos de ver [Classes de Base do renderizador e controles nativos](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md).

Cada específicos da plataforma `PlatformEffect` classe expõe os métodos a seguir, que devem ser substituídos para implementar um efeito:

- [`OnAttached`](xref:Xamarin.Forms.Effect.OnAttached) – chamado quando um efeito é anexado a um controle xamarin. Forms. Uma versão substituída deste método, em cada classe de efeito de plataforma específica, é o lugar para realizar a personalização do controle, junto com a manipulação de exceção no caso do efeito não pode ser aplicado ao controle especificado do xamarin. Forms.
- [`OnDetached`](xref:Xamarin.Forms.Effect.OnDetached) – chamado quando um efeito é desanexado de um controle xamarin. Forms. Uma versão substituída deste método, em cada classe de efeito específico da plataforma é o lugar para executar qualquer limpeza de efeito, como cancelar o registro de um manipulador de eventos.

Além disso, o `PlatformEffect` expõe o [ `OnElementPropertyChanged` ](xref:Xamarin.Forms.PlatformEffect`2.OnElementPropertyChanged(System.ComponentModel.PropertyChangedEventArgs)) método, que também pode ser substituído. Esse método é chamado quando uma propriedade do elemento foi alterada. Uma versão substituída deste método, em cada classe de efeito específico da plataforma é o lugar para responder a alterações de propriedade associável no controle do xamarin. Forms. Uma verificação para a propriedade que é alterada sempre deve ser feita conforme essa substituição pode ser chamada várias vezes.


## <a name="related-links"></a>Links relacionados

- [Renderizadores personalizados](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)
