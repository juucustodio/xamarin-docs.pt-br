---
title: Dimensionamento de acessibilidade para tamanhos de fonte nomeados no iOS
description: As especificações de plataforma permitem que você consuma a funcionalidade que só está disponível em uma plataforma específica, sem implementar renderizadores ou efeitos personalizados. Este artigo explica como consumir a plataforma do iOS específica que desabilita o dimensionamento de acessibilidade para tamanhos de fonte nomeados.
ms.prod: xamarin
ms.assetid: B443BAF6-E6F6-4A0E-80B5-CAACE6B550EF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/28/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: e693565bf34b2ab17992aed72d022315a33b87e3
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91563660"
---
# <a name="accessibility-scaling-for-named-font-sizes-on-ios"></a>Dimensionamento de acessibilidade para tamanhos de fonte nomeados no iOS

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Essa plataforma do iOS específica desabilita o dimensionamento de acessibilidade para tamanhos de fonte nomeados. Ele é consumido em XAML definindo a `Application.EnableAccessibilityScalingForNamedFontSizes` propriedade vinculável como `false` :

```xaml
<Application ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
             ios:Application.EnableAccessibilityScalingForNamedFontSizes="false">
    ...
</Application>
```

Como alternativa, ele pode ser consumido em C# usando a API fluente:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

Xamarin.Forms.Application.Current.On<iOS>().SetEnableAccessibilityScalingForNamedFontSizes(false);
```

O `Application.On<iOS>` método especifica que essa plataforma específica será executada somente no Ios. O `Application.SetEnableAccessibilityScalingForNamedFontSizes` método, no [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) namespace, é usado para desabilitar os tamanhos de fonte nomeados que estão sendo dimensionados pelas configurações de acessibilidade do Ios. Além disso, o `Application.GetEnableAccessibilityScalingForNamedFontSizes` método pode ser usado para retornar se os tamanhos de fonte nomeados são dimensionados pelas configurações de acessibilidade do Ios.

## <a name="related-links"></a>Links relacionados

- [PlatformSpecifics (exemplo)](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Criação de itens específicos à plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)