---
title: O conteúdo do ScrollView se toca no iOS
description: As especificações de plataforma permitem que você consuma a funcionalidade que só está disponível em uma plataforma específica, sem implementar renderizadores ou efeitos personalizados. Este artigo explica como consumir a plataforma específica do iOS que controla se um ScrollView manipula um gesto de toque ou o passa para seu conteúdo.
ms.prod: xamarin
ms.assetid: 99F823DB-B379-40F0-A343-A9783C341120
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: e028c506745bfd61aaff8e530a4f13d2429864ff
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93373907"
---
# <a name="scrollview-content-touches-on-ios"></a>O conteúdo do ScrollView se toca no iOS

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Um temporizador implícito é disparado quando um gesto de toque começa em um [`ScrollView`](xref:Xamarin.Forms.ScrollView) no Ios e o `ScrollView` decide, com base na ação do usuário dentro do alcance do temporizador, se ele deve lidar com o gesto ou passá-lo para seu conteúdo. Por padrão, o iOS `ScrollView` atrasa os toques de conteúdo, mas isso pode causar problemas em algumas circunstâncias com o `ScrollView` conteúdo que não ganha o gesto quando deveria. Portanto, essa plataforma específica controla se um `ScrollView` gesto de toque ou o passa para o seu conteúdo. Ele é consumido em XAML definindo a `ScrollView.ShouldDelayContentTouches` Propriedade anexada como um `boolean` valor:

```xaml
<MasterDetailPage ...
                  xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
    <MasterDetailPage.Master>
        <ContentPage Title="Menu" BackgroundColor="Blue" />
    </MasterDetailPage.Master>
    <MasterDetailPage.Detail>
        <ContentPage>
            <ScrollView x:Name="scrollView" ios:ScrollView.ShouldDelayContentTouches="false">
                <StackLayout Margin="0,20">
                    <Slider />
                    <Button Text="Toggle ScrollView DelayContentTouches" Clicked="OnButtonClicked" />
                </StackLayout>
            </ScrollView>
        </ContentPage>
    </MasterDetailPage.Detail>
</MasterDetailPage>
```

Como alternativa, ele pode ser consumido em C# usando a API fluente:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

scrollView.On<iOS>().SetShouldDelayContentTouches(false);
```

O `ScrollView.On<iOS>` método especifica que essa plataforma específica será executada somente no Ios. O `ScrollView.SetShouldDelayContentTouches` método, no [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) namespace, é usado para controlar se um deles [`ScrollView`](xref:Xamarin.Forms.ScrollView) manipula um gesto de toque ou o passa para seu conteúdo. Além disso, o `SetShouldDelayContentTouches` método pode ser usado para alternar o atraso de toques de conteúdo chamando o `ShouldDelayContentTouches` método para retornar se os toques de conteúdo estão atrasados:

```csharp
scrollView.On<iOS>().SetShouldDelayContentTouches(!scrollView.On<iOS>().ShouldDelayContentTouches());
```

O resultado é que um [`ScrollView`](xref:Xamarin.Forms.ScrollView) pode desabilitar o atraso de recebimento de conteúdo, de modo que, nesse cenário, o [`Slider`](xref:Xamarin.Forms.Slider) recebe o gesto em vez da [`Detail`](xref:Xamarin.Forms.MasterDetailPage.Detail) página do [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) :

[![ScrollViews de conteúdo de atraso da plataforma](scrollview-content-touches-images/scrollview-delay-content-touches.png)](scrollview-content-touches-images/scrollview-delay-content-touches-large.png#lightbox "ScrollView de conteúdo em atraso Platform-Specific")

## <a name="related-links"></a>Links relacionados

- [PlatformSpecifics (exemplo)](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Criação de itens específicos à plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)