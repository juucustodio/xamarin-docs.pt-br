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
ms.openlocfilehash: 7410386e10f605fdeed452fe37755c1e48e6b9b9
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/28/2020
ms.locfileid: "84136988"
---
# <a name="visualelement-drop-shadows-on-ios"></a>Sombras de soltar do visualelement no iOS

[![Baixar exemplo ](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Esse recurso específico da plataforma iOS é usado para habilitar uma sombra em um [`VisualElement`](xref:Xamarin.Forms.VisualElement) . Ele é consumido em XAML definindo a [`VisualElement.IsShadowEnabled`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.VisualElement.IsShadowEnabledProperty) Propriedade anexada como `true` , juntamente com várias propriedades adicionais anexadas opcionais que controlam a sombra:

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout Margin="20">
        <BoxView ...
                 ios:VisualElement.IsShadowEnabled="true"
                 ios:VisualElement.ShadowColor="Purple"
                 ios:VisualElement.ShadowOpacity="0.7"
                 ios:VisualElement.ShadowRadius="12">
            <ios:VisualElement.ShadowOffset>
                <Size>
                    <x:Arguments>
                        <x:Double>10</x:Double>
                        <x:Double>10</x:Double>
                    </x:Arguments>
                </Size>
            </ios:VisualElement.ShadowOffset>
         </BoxView>
        ...
    </StackLayout>
</ContentPage>
```

Como alternativa, ele pode ser consumido em C# usando a API fluente:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

var boxView = new BoxView { Color = Color.Aqua, WidthRequest = 100, HeightRequest = 100 };
boxView.On<iOS>()
       .SetIsShadowEnabled(true)
       .SetShadowColor(Color.Purple)
       .SetShadowOffset(new Size(10,10))
       .SetShadowOpacity(0.7)
       .SetShadowRadius(12);
```

O `VisualElement.On<iOS>` método especifica que essa plataforma específica será executada somente no Ios. O [ `VisualElement.SetIsShadowEnabled` ] (xref: Xamarin.Forms . PlatformConfiguration. iOSSpecific. Visualelement. SetIsShadowEnabled ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. iOS, Xamarin.Forms . Visualelement}, System. Boolean)), no [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) namespace, é usado para controlar se uma sombra de soltar está habilitada no `VisualElement` . Além disso, os seguintes métodos podem ser invocados para controlar a sombra:

- [ `SetShadowColor` ] (xref: Xamarin.Forms . PlatformConfiguration. iOSSpecific. Visualelement. SetShadowColor ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. iOS, Xamarin.Forms . Visualelement}, Xamarin.Forms . Color)) – define a cor da sombra de projeção. A cor padrão é [`Color.Default`](xref:Xamarin.Forms.Color.Default*) .
- [ `SetShadowOffset` ] (xref: Xamarin.Forms . PlatformConfiguration. iOSSpecific. Visualelement. SetShadowOffset ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. iOS, Xamarin.Forms . Visualelement}, Xamarin.Forms . Size)) – define o deslocamento da sombra de projeção. O deslocamento muda a direção em que a sombra é convertida e é especificada como um [`Size`](xref:Xamarin.Forms.Size) valor. Os `Size` valores de estrutura são expressos em unidades independentes de dispositivo, com o primeiro valor sendo a distância à esquerda (valor negativo) ou à direita (valor positivo) e o segundo valor sendo a distância acima (valor negativo) ou abaixo (valor positivo). O valor padrão dessa propriedade é (0,0, 0,0), que resulta na conversão da sombra em todos os lados do `VisualElement` .
- [ `SetShadowOpacity` ] (xref: Xamarin.Forms . PlatformConfiguration. iOSSpecific. Visualelement. SetShadowOpacity ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. iOS, Xamarin.Forms . Visualelement}, sistema. Double)) – define a opacidade da sombra projetada, com o valor que está no intervalo de 0,0 (transparente) a 1,0 (opaco). O valor de opacidade padrão é 0,5.
- [ `SetShadowRadius` ] (xref: Xamarin.Forms . PlatformConfiguration. iOSSpecific. Visualelement. SetShadowRadius ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. iOS, Xamarin.Forms . Visualelement}, sistema. Double)) – define o raio de desfoque usado para renderizar a sombra. O valor padrão de RADIUS é 10,0.

> [!NOTE]
> O estado de uma sombra de depósito pode ser consultado chamando o [ `GetIsShadowEnabled` ] (xref: Xamarin.Forms . PlatformConfiguration. iOSSpecific. Visualelement. GetIsShadowEnabled ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. iOS, Xamarin.Forms . Visualelement})), [ `GetShadowColor` ] (xref: Xamarin.Forms . PlatformConfiguration. iOSSpecific. Visualelement. GetShadowColor ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. iOS, Xamarin.Forms . Visualelement})), [ `GetShadowOffset` ] (xref: Xamarin.Forms . PlatformConfiguration. iOSSpecific. Visualelement. GetShadowOffset ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. iOS, Xamarin.Forms . Visualelement})), [ `GetShadowOpacity` ] (xref: Xamarin.Forms . PlatformConfiguration. iOSSpecific. Visualelement. GetShadowOpacity ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. iOS, Xamarin.Forms . Visualelement})), e [ `GetShadowRadius` ] (xref: Xamarin.Forms . PlatformConfiguration. iOSSpecific. Visualelement. GetShadowRadius ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. iOS, Xamarin.Forms . Visualelement})).

O resultado é que uma sombra pode ser habilitada em um [`VisualElement`](xref:Xamarin.Forms.VisualElement) :

![](drop-shadow-images/drop-shadow.png "Drop shadow enabled")

## <a name="related-links"></a>Links relacionados

- [PlatformSpecifics (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Criação de itens específicos à plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
