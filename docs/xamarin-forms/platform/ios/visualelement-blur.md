---
title: Desfoque de visualelement no iOS
description: As especificações de plataforma permitem que você consuma a funcionalidade que só está disponível em uma plataforma específica, sem implementar renderizadores ou efeitos personalizados. Este artigo explica como consumir a plataforma específica do iOS que aplica o desfoque a um Visualelement.
ms.prod: xamarin
ms.assetid: 2DE3B65E-B96E-4ECD-92DF-AA42D5205C44
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 4c688e6f2e4ac29eea9298f5e0f3c58403cd2ec1
ms.sourcegitcommit: 044e8d7e2e53f366942afe5084316198925f4b03
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/06/2021
ms.locfileid: "97940298"
---
# <a name="visualelement-blur-on-ios"></a>Desfoque de visualelement no iOS

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Essa plataforma específica do iOS é usada para desfocar o conteúdo em camadas abaixo dele e pode ser aplicada a qualquer um [`VisualElement`](xref:Xamarin.Forms.VisualElement) . Ele é consumido em XAML definindo a [`VisualElement.BlurEffect`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.VisualElement.BlurEffectProperty) Propriedade anexada como um valor da [`BlurEffectStyle`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.BlurEffectStyle) enumeração:

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
  ...
  <Image Source="monkeyface.png"
         ios:VisualElement.BlurEffect="ExtraLight" />
  ...
</ContentPage>
```

Como alternativa, ele pode ser consumido em C# usando a API fluente:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

image.On<iOS>().UseBlurEffect(BlurEffectStyle.ExtraLight);
```

O `Image.On<iOS>` método especifica que essa plataforma específica será executada somente no Ios. O [ `VisualElement.UseBlurEffect` ] (xref: Xamarin.Forms . PlatformConfiguration. iOSSpecific. Visualelement. UseBlurEffect ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. iOS, Xamarin.Forms . Visualelement}, Xamarin.Forms . PlatformConfiguration. iOSSpecific. BlurEffectStyle)), no [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) namespace, é usado para aplicar o efeito de desfoque, com a [`BlurEffectStyle`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.BlurEffectStyle) Enumeração fornecendo quatro valores:

- [`None`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.BlurEffectStyle.None)
- [`ExtraLight`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.BlurEffectStyle.ExtraLight)
- [`Light`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.BlurEffectStyle.Light)
- [`Dark`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.BlurEffectStyle.Dark)

O resultado é que um especificado [`BlurEffectStyle`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.BlurEffectStyle) é aplicado ao [`Image`](xref:Xamarin.Forms.Image) :

![Efeito de desfoque Platform-Specific](applying-blur-images/blur-effect.png)

> [!NOTE]
> Ao adicionar um efeito de desfoque a um [`VisualElement`](xref:Xamarin.Forms.VisualElement) , os eventos de toque ainda serão recebidos pelo `VisualElement` .

## <a name="related-links"></a>Links relacionados

- [PlatformSpecifics (exemplo)](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Criação de itens específicos à plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)