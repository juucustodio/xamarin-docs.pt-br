---
title: Elevação de visualelement no Android
description: As especificações de plataforma permitem que você consuma a funcionalidade que só está disponível em uma plataforma específica, sem implementar renderizadores ou efeitos personalizados. Este artigo explica como consumir a plataforma Android específica que controla a elevação de VisualElements em aplicativos que visam a API 21 ou superior.
ms.prod: xamarin
ms.assetid: 5BFD6175-2BBD-41CD-B8F9-521B4750B708
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/10/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 19f09025e44bb7deddbb8a9e6ae326d2137a5ce0
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93367524"
---
# <a name="visualelement-elevation-on-android"></a>Elevação de visualelement no Android

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Essa plataforma do Android específica é usada para controlar a elevação ou a ordem Z de elementos visuais em aplicativos direcionados à API 21 ou superior. A elevação de um elemento visual determina sua ordem de desenho, com elementos visuais com valores Z mais altos occluding elementos visuais com valores Z inferiores. Ele é consumido em XAML definindo a `VisualElement.Elevation` Propriedade anexada como um `boolean` valor:

```xaml
<ContentPage ...
             xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core"
             Title="Elevation">
    <StackLayout>
        <Grid>
            <Button Text="Button Beneath BoxView" />
            <BoxView Color="Red" Opacity="0.2" HeightRequest="50" />
        </Grid>        
        <Grid Margin="0,20,0,0">
            <Button Text="Button Above BoxView - Click Me" android:VisualElement.Elevation="10"/>
            <BoxView Color="Red" Opacity="0.2" HeightRequest="50" />
        </Grid>
    </StackLayout>
</ContentPage>
```

Como alternativa, ele pode ser consumido em C# usando a API fluente:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

public class AndroidElevationPageCS : ContentPage
{
    public AndroidElevationPageCS()
    {
        ...
        var aboveButton = new Button { Text = "Button Above BoxView - Click Me" };
        aboveButton.On<Android>().SetElevation(10);

        Content = new StackLayout
        {
            Children =
            {
                new Grid
                {
                    Children =
                    {
                        new Button { Text = "Button Beneath BoxView" },
                        new BoxView { Color = Color.Red, Opacity = 0.2, HeightRequest = 50 }
                    }
                },
                new Grid
                {
                    Margin = new Thickness(0,20,0,0),
                    Children =
                    {
                        aboveButton,
                        new BoxView { Color = Color.Red, Opacity = 0.2, HeightRequest = 50 }
                    }
                }
            }
        };
    }
}
```

O `Button.On<Android>` método especifica que essa plataforma específica será executada somente no Android. O `VisualElement.SetElevation` método, no [`Xamarin.Forms.PlatformConfiguration.AndroidSpecific`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) namespace, é usado para definir a elevação do elemento visual para um valor nulo `float` . Além disso, o `VisualElement.GetElevation` método pode ser usado para recuperar o valor de elevação de um elemento visual.

O resultado é que a elevação de elementos visuais pode ser controlada para que elementos visuais com valores Z mais altos occlude elementos visuais com valores Z inferiores. Portanto, neste exemplo, o segundo [`Button`](xref:Xamarin.Forms.Button) é processado acima do [`BoxView`](xref:Xamarin.Forms.BoxView) porque ele tem um valor de elevação maior:

![Captura de tela de elevação do visualelement](visualelement-elevation-images/elevation.png)

## <a name="related-links"></a>Links relacionados

- [PlatformSpecifics (exemplo)](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Criação de itens específicos à plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API AndroidSpecific](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [API AndroidSpecific. AppCompat](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)