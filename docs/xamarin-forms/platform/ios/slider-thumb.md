---
title: ''
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
ms.openlocfilehash: 93b4dba3e8543bd2cc2a4f2187f617aae5daff77
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/28/2020
ms.locfileid: "84137066"
---
# <a name="slider-thumb-tap-on-ios"></a>Toque deslizante do slider no iOS

[![Baixar exemplo ](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Essa plataforma específica do iOS permite que a [`Slider.Value`](xref:Xamarin.Forms.Slider.Value) propriedade seja definida tocando em uma posição na [`Slider`](xref:Xamarin.Forms.Slider) barra, em vez de ter que arrastar o `Slider` polegar. Ele é consumido em XAML definindo a [`Slider.UpdateOnTap`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Slider.UpdateOnTapProperty) propriedade vinculável como `true` :

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout ...>
        <Slider ... ios:Slider.UpdateOnTap="true" />
        ...
    </StackLayout>
</ContentPage>
```

Como alternativa, ele pode ser consumido em C# usando a API fluente:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

var slider = new Xamarin.Forms.Slider();
slider.On<iOS>().SetUpdateOnTap(true);
```

O `Slider.On<iOS>` método especifica que essa plataforma específica será executada somente no Ios. O [ `Slider.SetUpdateOnTap` ] (xref: Xamarin.Forms . PlatformConfiguration. iOSSpecific. Slider. SetUpdateOnTap ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. iOS, Xamarin.Forms . Slider), o método System. Boolean), no [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) namespace, é usado para controlar se um toque na `Slider` barra definirá a [`Slider.Value`](xref:Xamarin.Forms.Slider.Value) propriedade. Além disso, o [ `Slider.GetUpdateOnTap` ] (xref: Xamarin.Forms . PlatformConfiguration. iOSSpecific. Slider. GetUpdateOnTap ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. iOS, Xamarin.Forms . Slider})), o método pode ser usado para retornar se um toque na `Slider` barra definirá a `Slider.Value` propriedade.

O resultado é que um toque na [`Slider`](xref:Xamarin.Forms.Slider) barra pode mover o `Slider` polegar e definir a [`Slider.Value`](xref:Xamarin.Forms.Slider.Value) Propriedade:

![](slider-thumb-images/slider-updateontap.png "Slider Update on Tap enabled")

## <a name="related-links"></a>Links relacionados

- [PlatformSpecifics (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Criação de itens específicos à plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
