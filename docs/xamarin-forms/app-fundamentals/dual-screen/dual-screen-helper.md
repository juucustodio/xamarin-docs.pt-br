---
title: Xamarin.Formsauxiliares de plataforma de tela dupla
description: Este guia explica como usar a Xamarin.Forms classe DualScreenHelper para otimizar sua experiência de aplicativo para dispositivos de tela dupla, como Surface Duo e Surface neo.
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: d9daf5a24c0dcfd07d529955c411259f4c1359df
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/28/2020
ms.locfileid: "84138938"
---
# <a name="xamarinforms-dual-screen-platform-helpers"></a>Xamarin.Formsauxiliares de plataforma de tela dupla

![](~/media/shared/preview.png "This API is currently pre-release")

[![Baixar exemplo ](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-dualscreendemos/)

A classe `DualScreenHelper` pode ser usada para detectar se um dispositivo é compatível com o modo Picture in Picture e permite abrir uma `ContentPage` como uma janela picture in picture. Caso esteja executando em um dispositivo Neo, ele abrirá a página no WonderBar quando estiver no modo de redação.

## <a name="properties"></a>Propriedades

- `HasCompactModeSupport` é usada para verificar se a plataforma é compatível com a abertura de uma `ContentPage` no CompactMode.
- A `OpenCompactMode` abre uma `ContentPage` no CompactMode, caso seja compatível com a plataforma.

## <a name="example"></a>Exemplo

```csharp
async void OpenCompactWindowClicked(object sender, EventArgs e)
{
    if (!DualScreenHelper.HasCompactModeSupport())
    {
        await DisplayAlert("Unsupported", "This platform doesn't support this feature", "Ok");
        return;
    }

    ContentPage page = new ContentPage() { BackgroundColor = Color.Purple };

    var button = new Button()
    {
        Text = "Close this Window"
    };

    var layout = new StackLayout()
    {
        Children =
        {
            new Label(){ Text = "Welcome to your Compact Mode Window" },
            button
        },
        BackgroundColor = Color.Yellow,
        HorizontalOptions = LayoutOptions.Fill,
        VerticalOptions = LayoutOptions.Fill
    };

    page.Content = new ScrollView() { Content = layout };
    var args = await DualScreenHelper.OpenCompactMode(page);

    button.Command = new Command(async () =>
    {
        await args.CloseAsync();
    });
}
```
