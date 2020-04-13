---
title: Introdução aos efeitos
description: Efeitos permitem que os controles nativos em cada plataforma sejam personalizados e são geralmente usados para pequenas alterações de estilo. Este artigo fornece uma introdução aos efeitos, descreve o limite entre efeitos e renderizadores personalizados e descreve a classe PlatformEffect.
ms.prod: xamarin
ms.assetid: 30CB8615-8F39-4762-BDB7-333D2B57D112
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/08/2016
ms.openlocfilehash: e9325c34c645b75f28c7e2070f6bb095780ddb02
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "70771458"
---
# <a name="introduction-to-effects"></a>Introdução aos efeitos

_Os efeitos permitem que os controles nativos em cada plataforma sejam personalizados, e são normalmente usados para pequenas mudanças de estilo. Este artigo fornece uma introdução aos efeitos, descreve o limite entre efeitos e renderizadores personalizados e descreve a classe PlatformEffect._

[Páginas, Layouts e Controles](~/xamarin-forms/user-interface/controls/index.md) do Xamarin.Forms apresenta uma API comum para descrever interfaces do usuário móveis multiplataforma. Cada página, layout e controle é renderizado de maneira diferente em cada plataforma usando uma classe `Renderer`, que por sua vez cria um controle nativo (correspondente à representação no Xamarin.Forms), organiza sua disposição na tela e adiciona o comportamento especificado no código compartilhado.

Os desenvolvedores podem implementar suas próprias classes `Renderer` personalizadas para personalizar a aparência e/ou o comportamento de um controle. No entanto, implementar uma classe de renderizador personalizado para executar uma personalização de controle simples geralmente gera uma resposta pesada. Os efeitos simplificam esse processo, permitindo que os controles nativos em cada plataforma sejam personalizado mais facilmente.

Os efeitos são criados em projetos específicos da plataforma criando subclasses do controle `PlatformEffect` e, em seguida, os controles são consumidos anexando-os a um controle apropriado em uma biblioteca .NET Standard do Xamarin.Forms ou um projeto de Biblioteca compartilhada.

## <a name="why-use-an-effect-over-a-custom-renderer"></a>Por que usar um efeito em vez de um renderizador personalizado?

Os efeitos simplificam a personalização de um controle, são reutilizáveis e podem ser parametrizados para aumentar ainda mais a reutilização.

Tudo que pode ser feito com um efeito também pode ser feito com um renderizador personalizado. No entanto, os renderizadores personalizados oferecem mais flexibilidade e personalização do que os efeitos. As diretrizes a seguir listam as circunstâncias nas quais você pode escolher um efeito em vez de um renderizador personalizado:

- É recomendado usar um efeito quando alterar as propriedades de um controle específico da plataforma atinge o resultado desejado.
- Um renderizador personalizado é necessário quando há necessidade de substituir os métodos por um controle específico da plataforma.
- Um renderizador personalizado é necessário quando há necessidade de substituir o controle específico da plataforma que implementa um controle do Xamarin.Forms.

## <a name="subclassing-the-platformeffect-class"></a>Criando subclasses da classe PlatformEffect

A tabela a seguir lista o namespace para a classe `PlatformEffect` em cada plataforma, bem como os tipos de suas propriedades:

|Plataforma|Namespace|Contêiner|Control|
|--- |--- |--- |--- |
|iOS|Xamarin.Forms.Platform.iOS|UIView|UIView|
|Android|Xamarin.Forms.Platform.Android|ViewGroup|Visualizar|
|UWP (Plataforma Universal do Windows)|Xamarin.Forms.Platform.UWP|FrameworkElement|FrameworkElement|

Cada classe `PlatformEffect` específica da plataforma expõe as propriedades a seguir:

- `Container` – faz referência ao controle específico da plataforma que está sendo usado para implementar o layout.
- `Control` – faz referência ao controle específico da plataforma que está sendo usado para implementar o controle do Xamarin.Forms.
- `Element` – faz referência ao controle do Xamarin.Forms que está sendo renderizado.

Os efeitos não têm informações de tipo sobre o contêiner, o controle ou o elemento a que estão anexados porque eles podem ser anexados a qualquer elemento. Portanto, quando um efeito é anexado a um elemento incompatível, ele deve ter o desempenho reduzido discretamente ou lançar uma exceção. No entanto, as propriedades `Container`, `Control` e `Element` podem ser convertidas em seu tipo de implementação. Para obter mais informações sobre esses tipos, confira [Classes base do renderizador e controles nativos](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md).

Cada classe `PlatformEffect` específica da plataforma expõe os métodos a seguir, que devem ser substituídos para implementar um efeito:

- [`OnAttached`](xref:Xamarin.Forms.Effect.OnAttached)– chamado quando um efeito é anexado a um controle Xamarin.Forms. Uma versão de substituição desse método, em cada classe de efeito específica da plataforma, é o lugar certo para realizar a personalização do controle, bem como a manipulação de exceções caso o efeito não possa ser aplicado ao controle do Xamarin.Forms especificado.
- [`OnDetached`](xref:Xamarin.Forms.Effect.OnDetached)– chamado quando um efeito é desvinculado de um controle Xamarin.Forms. Uma versão de substituição deste método, em cada classe de efeito específica da plataforma, é o lugar para executar qualquer limpeza de efeito, como cancelar o registro de um manipulador de eventos.

Além disso, `PlatformEffect` o [`OnElementPropertyChanged`](xref:Xamarin.Forms.PlatformEffect`2.OnElementPropertyChanged(System.ComponentModel.PropertyChangedEventArgs)) método expõe, que também pode ser substituído. Esse método é chamado quando uma propriedade do elemento foi alterada. Uma versão de substituição do método, em cada classe de efeito específica da plataforma, é o lugar para responder a alterações de propriedade vinculáveis sobre o controle do Xamarin.Forms. Uma verificação da propriedade alterada sempre deve ser feita, pois essa substituição pode ser chamada várias vezes.

## <a name="related-links"></a>Links relacionados

- [Renderizadores personalizados](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)
