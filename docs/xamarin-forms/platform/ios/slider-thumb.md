---
title: Toque deslizante do slider no iOS
description: Especificidades da plataforma permitem que você consumir funcionalidade só está disponível em uma plataforma específica, sem implementar renderizadores personalizados ou efeitos. Este artigo explica como consumir a plataforma do iOS específica que permite que a Propriedade Slider. Value seja definida tocando na barra de controle deslizante.
ms.prod: xamarin
ms.assetid: D0915D37-9A59-4728-BB6A-FE094A661275
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
ms.openlocfilehash: 573b68097724c976ce73b51e3b7ba21b52f7a776
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68651774"
---
# <a name="slider-thumb-tap-on-ios"></a>Toque deslizante do slider no iOS

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Essa plataforma específica do IOS permite que [`Slider.Value`](xref:Xamarin.Forms.Slider.Value) a propriedade seja definida tocando em uma posição [`Slider`](xref:Xamarin.Forms.Slider) na barra, em vez de ter que arrastar o `Slider` polegar. Ele é consumido em XAML, definindo o [ `Slider.UpdateOnTap` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Slider.UpdateOnTapProperty) para a propriedade associável `true`:

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout ...>
        <Slider ... ios:Slider.UpdateOnTap="true" />
        ...
    </StackLayout>
</ContentPage>
```

Como alternativa, ele pode ser consumido de C# usando a API fluente:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

var slider = new Xamarin.Forms.Slider();
slider.On<iOS>().SetUpdateOnTap(true);
```

O `Slider.On<iOS>` método Especifica que este específicos da plataforma serão executado apenas no iOS. O [ `Slider.SetUpdateOnTap` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Slider.SetUpdateOnTap(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.Slider},System.Boolean)) método, no [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) namespace, é usada para controlar se um toque do `Slider` barra definirá o [ `Slider.Value` ](xref:Xamarin.Forms.Slider.Value) propriedade. Além disso, o [ `Slider.GetUpdateOnTap` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Slider.GetUpdateOnTap(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.Slider})) método pode ser usado para retornar se um toque dos `Slider` barra definirá o `Slider.Value` propriedade.

O resultado é que um toque do [ `Slider` ](xref:Xamarin.Forms.Slider) barra pode mover o `Slider` thumb e defina o [ `Slider.Value` ](xref:Xamarin.Forms.Slider.Value) propriedade:

![](slider-thumb-images/slider-updateontap.png "Atualização do controle deslizante em toque habilitado")

## <a name="related-links"></a>Links relacionados

- [PlatformSpecifics (amostra)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Criação de itens específicos à plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
