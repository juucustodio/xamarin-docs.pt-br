---
title: DualScreenHelper do Xamarin.Forms
description: Este guia explica como usar a classe DualScreenHelper do Xamarin.Forms para otimizar sua experiência de aplicativo com dispositivos de tela dupla, como o Surface Duo e o Surface Neo.
ms.prod: xamarin
ms.assetid: 5aa184c2-5611-427d-85c7-1c56486c3e1b
ms.technology: xamarin-forms
author: davidortinau
ms.author: daortin
ms.date: 02/08/2020
ms.openlocfilehash: 26e6389dc7effd52fad8307e1e3191fc4b760fe6
ms.sourcegitcommit: 524fc148bad17272bda83c50775771daa45bfd7e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/20/2020
ms.locfileid: "77480578"
---
# <a name="xamarinforms-dualscreenhelper"></a>DualScreenHelper do Xamarin.Forms

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://github.com/xamarin/xamarin-forms-samples/UserInterface/DualScreenDemos)

A classe `DualScreenHelper` pode ser usada para detectar se um dispositivo é compatível com o modo Picture in Picture e permite abrir uma `ContentPage` como uma janela picture in picture. Caso esteja executando em um dispositivo Neo, ele abrirá a página no WonderBar quando estiver no modo de redação.

## <a name="properties"></a>Propriedades

- `HasCompactModeSupport` é usada para verificar se a plataforma é compatível com a abertura de uma `ContentPage` no CompactMode.
- A `OpenCompactMode` abre uma `ContentPage` no CompactMode, caso seja compatível com a plataforma.

## <a name="example"></a>Exemplo

```csharp
async void OpenCompactWindowClicked(object sender, EventArgs e)
{
    if(!DualScreenHelper.HasCompactModeSupport())
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
