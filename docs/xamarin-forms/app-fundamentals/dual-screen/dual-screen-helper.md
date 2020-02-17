---
title: DualScreenHelper do Xamarin.Forms
description: Este guia explica como usar o DualScreenHelper do Xamarin.Forms para otimizar a experiência de aplicativo para dispositivos de tela dupla, como o Surface Duo e o Surface Neo.
ms.prod: xamarin
ms.assetid: 5aa184c2-5611-427d-85c7-1c56486c3e1b
ms.technology: xamarin-forms
author: davidortinau
ms.author: daortin
ms.date: 02/08/2020
ms.openlocfilehash: b06e105560de635a21b42e8366bb47842372cf6a
ms.sourcegitcommit: 07941cf9704ff88cf4087de5ebdea623ff54edb1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/11/2020
ms.locfileid: "77145540"
---
# <a name="xamarinforms-dualscreenhelper"></a>DualScreenHelper do Xamarin.Forms
[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://github.com/xamarin/xamarin-forms-samples/UserInterface/DualScreenDemos)
_O DualScreenHelper pode ser usado para detectar se um dispositivo é compatível com o modo Picture in Picture e permite que você abra um ContentPage como uma janela picture in picture. Se você estiver executando em um dispositivo Neo, isso abrirá a página no WonderBar quando estiver no modo de composição._
## <a name="properties"></a>Propriedades
- `HasCompactModeSupport` usado para verificar se a plataforma dá suporte à abertura de um ContentPage no CompactMode.
- `OpenCompactMode` se a plataforma der suporte a ele, isso abrirá um ContentPage no CompactMode.
## <a name="example-usage"></a>Exemplo de uso
```c#
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