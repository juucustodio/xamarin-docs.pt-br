---
title: Estilos de dispositivo no xamarin. Forms
description: Xamarin. Forms inclui seis estilos dinâmicos, conhecidos como estilos de dispositivo, na classe Device.Styles. Este artigo explica como utilizar os estilos de dispositivo em um aplicativo xamarin. Forms.
ms.prod: xamarin
ms.assetid: 7FF19ED1-0822-4238-9435-AD970317A2F8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/17/2016
ms.openlocfilehash: fd5181040c1805d3fdabdae4803bbe32c6bb6652
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61345485"
---
# <a name="device-styles-in-xamarinforms"></a>Estilos de dispositivo no xamarin. Forms

[![Baixar exemplo](~/media/shared/download.png) baixar o exemplo](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Styles/DynamicStyles/)

_Xamarin. Forms inclui seis estilos dinâmicos, conhecidos como estilos de dispositivo, na classe Device.Styles._

O *dispositivo* estilos são:

- [`BodyStyle`](xref:Xamarin.Forms.Device.Styles.BodyStyle)
- [`CaptionStyle`](xref:Xamarin.Forms.Device.Styles.CaptionStyle)
- [`ListItemDetailTextStyle`](xref:Xamarin.Forms.Device.Styles.ListItemDetailTextStyle)
- [`ListItemTextStyle`](xref:Xamarin.Forms.Device.Styles.ListItemTextStyle)
- [`SubtitleStyle`](xref:Xamarin.Forms.Device.Styles.SubtitleStyle)
- [`TitleStyle`](xref:Xamarin.Forms.Device.Styles.TitleStyle)

Todos os estilos de seis só podem ser aplicados a [ `Label` ](xref:Xamarin.Forms.Label) instâncias. Por exemplo, uma `Label` que está exibindo o corpo de um parágrafo pode definir seus [ `Style` ](xref:Xamarin.Forms.VisualElement.Style) propriedade a ser [ `BodyStyle` ](xref:Xamarin.Forms.Device.Styles.BodyStyle).

O exemplo de código a seguir demonstra como usar o *dispositivo* estilos em uma página XAML:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="Styles.DeviceStylesPage" Title="Device" Icon="xaml.png">
    <ContentPage.Resources>
        <ResourceDictionary>
            <Style x:Key="myBodyStyle" TargetType="Label"
              BaseResourceKey="BodyStyle">
                <Setter Property="TextColor" Value="Accent" />
            </Style>
        </ResourceDictionary>
    </ContentPage.Resources>
    <ContentPage.Content>
        <StackLayout Padding="0,20,0,0">
            <Label Text="Title style"
              Style="{DynamicResource TitleStyle}" />
            <Label Text="Subtitle text style"
              Style="{DynamicResource SubtitleStyle}" />
            <Label Text="Body style"
              Style="{DynamicResource BodyStyle}" />
            <Label Text="Caption style"
              Style="{DynamicResource CaptionStyle}" />
            <Label Text="List item detail text style"
              Style="{DynamicResource ListItemDetailTextStyle}" />
            <Label Text="List item text style"
              Style="{DynamicResource ListItemTextStyle}" />
            <Label Text="No style" />
            <Label Text="My body style"
              Style="{StaticResource myBodyStyle}" />
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

Os estilos de dispositivo são associados ao uso de `DynamicResource` extensão de marcação. A natureza dinâmica dos estilos pode ser vista no iOS, alterando a **acessibilidade** configurações para o tamanho do texto. A aparência do *dispositivo* estilos é diferente em cada plataforma, conforme mostrado nas capturas de tela seguir:

![](device-images/device-styles.png "Estilos de dispositivo em cada plataforma")

*Dispositivo* estilos também podem ser derivados de definindo o [ `BaseResourceKey` ](xref:Xamarin.Forms.Style.BaseResourceKey) propriedade para o nome da chave para o estilo de dispositivo. No exemplo de código acima, `myBodyStyle` herda [ `BodyStyle` ](xref:Xamarin.Forms.Device.Styles.BodyStyle) e define uma cor do texto acentuados. Para obter mais informações sobre herança de estilo dinâmica, consulte [herança de estilo dinâmica](~/xamarin-forms/user-interface/styles/xaml/dynamic.md#dynamic-style-inheritance).

O exemplo de código a seguir demonstra a página equivalente em C#:

```csharp
public class DeviceStylesPageCS : ContentPage
{
    public DeviceStylesPageCS ()
    {
        var myBodyStyle = new Style (typeof(Label)) {
            BaseResourceKey = Device.Styles.BodyStyleKey,
            Setters = {
                new Setter {
                    Property = Label.TextColorProperty,
                    Value = Color.Accent
                }
            }
        };

        Title = "Device";
        Icon = "csharp.png";
        Padding = new Thickness (0, 20, 0, 0);

        Content = new StackLayout {
            Children = {
                new Label { Text = "Title style", Style = Device.Styles.TitleStyle },
                new Label { Text = "Subtitle style", Style = Device.Styles.SubtitleStyle },
                new Label { Text = "Body style", Style = Device.Styles.BodyStyle },
                new Label { Text = "Caption style", Style = Device.Styles.CaptionStyle },
                new Label { Text = "List item detail text style",
                  Style = Device.Styles.ListItemDetailTextStyle },
                new Label { Text = "List item text style", Style = Device.Styles.ListItemTextStyle },
                new Label { Text = "No style" },
                new Label { Text = "My body style", Style = myBodyStyle }
            }
        };
    }
}
```

O [ `Style` ](xref:Xamarin.Forms.VisualElement.Style) propriedade de cada [ `Label` ](xref:Xamarin.Forms.Label) instância é definida como a propriedade apropriada do [ `Devices.Styles` ](xref:Xamarin.Forms.Device.Styles) classe.

## <a name="accessibility"></a>Acessibilidade

O *dispositivo* estilos respeitarem as preferências de acessibilidade, para que os tamanhos de fonte serão alterado conforme as preferências de acessibilidade são alteradas em cada plataforma. Portanto, para dar suporte a texto acessível, certifique-se de que o *dispositivo* estilos são usados como base para qualquer estilo de texto dentro de seu aplicativo.

As capturas de tela a seguir demonstram os estilos de dispositivo em cada plataforma, com o menor tamanho de fonte acessível:

[![](device-images/minimum-size.png "Estilos de dispositivo pequeno acessível em cada plataforma")](device-images/minimum-size-large.png#lightbox "estilos de dispositivo pequeno acessível em cada plataforma")

As capturas de tela a seguir demonstram os estilos de dispositivo em cada plataforma, com o maior tamanho de fonte acessível:

![](device-images/maximum-size.png "Estilos de dispositivo grande acessível em cada plataforma")

## <a name="related-links"></a>Links relacionados

- [Estilos de texto](~/xamarin-forms/user-interface/text/styles.md)
- [Extensões de marcação XAML](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [Estilos dinâmicos (amostra)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Styles/DynamicStyles/)
- [Trabalhar com estilos (amostra)](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithStyles/)
- [Device.Styles](xref:Xamarin.Forms.Device.Styles)
- [Dicionário de recurso](xref:Xamarin.Forms.ResourceDictionary)
- [Estilo](xref:Xamarin.Forms.Style)
- [Setter](xref:Xamarin.Forms.Setter)
