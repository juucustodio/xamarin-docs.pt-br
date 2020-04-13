---
title: Xamarin.Forms dual-screen platform helpers
description: Este guia explica como usar a classe DualScreenHelper do Xamarin.Forms para otimizar sua experiência de aplicativo com dispositivos de tela dupla, como o Surface Duo e o Surface Neo.
ms.prod: xamarin
ms.assetid: 5aa184c2-5611-427d-85c7-1c56486c3e1b
ms.technology: xamarin-forms
author: davidortinau
ms.author: daortin
ms.date: 02/08/2020
ms.openlocfilehash: 9ab6106b8b92660d416e8a22cc3b1bdf1b41c5cf
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "80628272"
---
# <a name="xamarinforms-dual-screen-platform-helpers"></a>Xamarin.Forms dual-screen platform helpers

![](~/media/shared/preview.png "This API is currently pre-release")

[![Baixar](~/media/shared/download.png) amostra Baixar a amostra](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-dualscreendemos/)

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
