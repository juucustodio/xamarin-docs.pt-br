---
title: Toque deslizante do slider no iOS
description: As especificações de plataforma permitem que você consuma a funcionalidade que só está disponível em uma plataforma específica, sem implementar renderizadores ou efeitos personalizados. Este artigo explica como consumir a plataforma do iOS específica que permite que a Propriedade Slider. Value seja definida tocando na barra de controle deslizante.
ms.prod: xamarin
ms.assetid: D0915D37-9A59-4728-BB6A-FE094A661275
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: c3e74d52eee5da11dffc3c8a778fa2f691f1ca02
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91560930"
---
# <a name="slider-thumb-tap-on-ios"></a>Toque deslizante do slider no iOS

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

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

![Atualização do controle deslizante ao toque habilitado](slider-thumb-images/slider-updateontap.png)

## <a name="related-links"></a>Links relacionados

- [PlatformSpecifics (exemplo)](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Criação de itens específicos à plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)