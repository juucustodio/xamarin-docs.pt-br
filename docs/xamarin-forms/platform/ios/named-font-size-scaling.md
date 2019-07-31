---
title: Dimensionamento de acessibilidade para tamanhos de fonte nomeados no iOS
description: Especificidades da plataforma permitem que você consumir funcionalidade só está disponível em uma plataforma específica, sem implementar renderizadores personalizados ou efeitos. Este artigo explica como consumir a plataforma do iOS específica que desabilita o dimensionamento de acessibilidade para tamanhos de fonte nomeados.
ms.prod: xamarin
ms.assetid: B443BAF6-E6F6-4A0E-80B5-CAACE6B550EF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/28/2019
ms.openlocfilehash: 678ea2210389eb33bc3a184d758f2bfa07c60bd9
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68656728"
---
# <a name="accessibility-scaling-for-named-font-sizes-on-ios"></a>Dimensionamento de acessibilidade para tamanhos de fonte nomeados no iOS

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Essa plataforma do iOS específica desabilita o dimensionamento de acessibilidade para tamanhos de fonte nomeados. Ele é consumido em XAML, definindo o `Application.EnableAccessibilityScalingForNamedFontSizes` para a propriedade associável `false`:

```xaml
<Application ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
             ios:Application.EnableAccessibilityScalingForNamedFontSizes="false">
    ...
</Application>
```

Como alternativa, ele pode ser consumido de C# usando a API fluente:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

Xamarin.Forms.Application.Current.On<iOS>().SetEnableAccessibilityScalingForNamedFontSizes(false);
```

O `Application.On<iOS>` método Especifica que este específicos da plataforma serão executado apenas no iOS. O `Application.SetEnableAccessibilityScalingForNamedFontSizes` método, [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) no namespace, é usado para desabilitar os tamanhos de fonte nomeados que estão sendo dimensionados pelas configurações de acessibilidade do Ios. Além disso, o `Application.GetEnableAccessibilityScalingForNamedFontSizes` método pode ser usado para retornar se os tamanhos de fonte nomeados são dimensionados pelas configurações de acessibilidade do Ios.

## <a name="related-links"></a>Links relacionados

- [PlatformSpecifics (amostra)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Criação de itens específicos à plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
