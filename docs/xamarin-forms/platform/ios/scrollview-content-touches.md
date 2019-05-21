---
title: ScrollView toques de conteúdo no iOS
description: Especificidades da plataforma permitem que você consumir funcionalidade só está disponível em uma plataforma específica, sem implementar renderizadores personalizados ou efeitos. Este artigo explica como consumir do iOS específicas da plataforma que controla se um ScrollView lida com um gesto de toque ou passá-lo ao seu conteúdo.
ms.prod: xamarin
ms.assetid: 99F823DB-B379-40F0-A343-A9783C341120
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
ms.openlocfilehash: 19a683fbd4aeb7060d97406a89c1e9308fb56b03
ms.sourcegitcommit: b23a107b0fe3d2f814ae35b52a5855b6ce2a3513
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/20/2019
ms.locfileid: "65926002"
---
# <a name="scrollview-content-touches-on-ios"></a>ScrollView toques de conteúdo no iOS

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/PlatformSpecifics/)

Um temporizador implícito é disparado quando um gesto de toque começa em um [ `ScrollView` ](xref:Xamarin.Forms.ScrollView) no iOS e o `ScrollView` decide, com base na ação de usuário na extensão de timer, se ele deve tratar o gesto ou passá-lo ao seu conteúdo. Por padrão, o iOS `ScrollView` toques de conteúdo de atrasos, mas isso pode causar problemas em algumas circunstâncias com o `ScrollView` conteúdo não ganhando o gesto quando deveria. Portanto, esse controles específicos da plataforma se um `ScrollView` manipula um gesto de toque ou passá-lo ao seu conteúdo. Ele é consumido em XAML, definindo o `ScrollView.ShouldDelayContentTouches` anexado à propriedade um `boolean` valor:

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

Como alternativa, ele pode ser consumido de C# usando a API fluente:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

scrollView.On<iOS>().SetShouldDelayContentTouches(false);
```

O `ScrollView.On<iOS>` método Especifica que este específicos da plataforma serão executado apenas no iOS. O `ScrollView.SetShouldDelayContentTouches` método, no [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) namespace, é usada para controlar se uma [ `ScrollView` ](xref:Xamarin.Forms.ScrollView) manipula um gesto de toque ou passá-lo ao seu conteúdo. Além disso, o `SetShouldDelayContentTouches` método pode ser usado para ativar/desativar atrasando o conteúdo de toques, chamando o `ShouldDelayContentTouches` método para retornar se o conteúdo de toques estão atrasadas:

```csharp
scrollView.On<iOS>().SetShouldDelayContentTouches(!scrollView.On<iOS>().ShouldDelayContentTouches());
```

O resultado é que um [ `ScrollView` ](xref:Xamarin.Forms.ScrollView) pode desabilitar atrasando o recebimento de toques de conteúdo, portanto, que neste cenário o [ `Slider` ](xref:Xamarin.Forms.Slider) recebe o gesto em vez de [ `Detail` ](xref:Xamarin.Forms.MasterDetailPage.Detail) página do [ `MasterDetailPage` ](xref:Xamarin.Forms.MasterDetailPage):

[![](scrollview-content-touches-images/scrollview-delay-content-touches.png "Conteúdo de atraso de ScrollView toca específicos da plataforma")](scrollview-content-touches-images/scrollview-delay-content-touches-large.png#lightbox "conteúdo de atraso de ScrollView toca específicos da plataforma")

## <a name="related-links"></a>Links relacionados

- [PlatformSpecifics (amostra)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/PlatformSpecifics/)
- [Criação de itens específicos à plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [iOSSpecific API](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
