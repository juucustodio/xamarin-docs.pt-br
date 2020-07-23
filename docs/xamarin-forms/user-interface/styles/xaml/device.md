---
title: Estilos de dispositivo emXamarin.Forms
description: Xamarin.Formsinclui seis estilos dinâmicos, conhecidos como estilos de dispositivo, na classe Device. Styles. Este artigo explica como consumir os estilos de dispositivo em um Xamarin.Forms aplicativo.
ms.prod: xamarin
ms.assetid: 7FF19ED1-0822-4238-9435-AD970317A2F8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/17/2016
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 04669479bb321da4fee6c45fd0f2c00deb5bbf1a
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/22/2020
ms.locfileid: "86929721"
---
# <a name="device-styles-in-xamarinforms"></a>Estilos de dispositivo emXamarin.Forms

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-styles-dynamicstyles)

_Xamarin.Formsinclui seis estilos dinâmicos, conhecidos como estilos de dispositivo, na classe Device. Styles._

Os estilos de *dispositivo* são:

- [`BodyStyle`](xref:Xamarin.Forms.Device.Styles.BodyStyle)
- [`CaptionStyle`](xref:Xamarin.Forms.Device.Styles.CaptionStyle)
- [`ListItemDetailTextStyle`](xref:Xamarin.Forms.Device.Styles.ListItemDetailTextStyle)
- [`ListItemTextStyle`](xref:Xamarin.Forms.Device.Styles.ListItemTextStyle)
- [`SubtitleStyle`](xref:Xamarin.Forms.Device.Styles.SubtitleStyle)
- [`TitleStyle`](xref:Xamarin.Forms.Device.Styles.TitleStyle)

Todos os seis estilos só podem ser aplicados a [`Label`](xref:Xamarin.Forms.Label) instâncias. Por exemplo, um `Label` que está exibindo o corpo de um parágrafo pode definir sua [`Style`](xref:Xamarin.Forms.NavigableElement.Style) propriedade como [`BodyStyle`](xref:Xamarin.Forms.Device.Styles.BodyStyle) .

O exemplo de código a seguir demonstra como usar os estilos de *dispositivo* em uma página XAML:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="Styles.DeviceStylesPage" Title="Device" IconImageSource="xaml.png">
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

Os estilos de dispositivo são associados ao uso da `DynamicResource` extensão de marcação. A natureza dinâmica dos estilos pode ser vista no iOS alterando as configurações de **acessibilidade** para o tamanho do texto. A aparência dos estilos de *dispositivo* é diferente em cada plataforma, conforme mostrado nas capturas de tela a seguir:

![Estilos de dispositivo em cada plataforma](device-images/device-styles.png)

Os estilos de *dispositivo* também podem ser derivados do definindo a [`BaseResourceKey`](xref:Xamarin.Forms.Style.BaseResourceKey) propriedade como o nome da chave para o estilo do dispositivo. No exemplo de código acima, `myBodyStyle` herda de [`BodyStyle`](xref:Xamarin.Forms.Device.Styles.BodyStyle) e define uma cor de texto acentuada. Para obter mais informações sobre herança de estilo dinâmico, consulte [herança de estilo dinâmico](~/xamarin-forms/user-interface/styles/xaml/dynamic.md#dynamic-style-inheritance).

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
        IconImageSource = "csharp.png";
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

A [`Style`](xref:Xamarin.Forms.NavigableElement.Style) propriedade de cada [`Label`](xref:Xamarin.Forms.Label) instância é definida como a propriedade apropriada da [`Devices.Styles`](xref:Xamarin.Forms.Device.Styles) classe.

## <a name="accessibility"></a>Acessibilidade

Os estilos de *dispositivo* respeitam as preferências de acessibilidade, portanto, os tamanhos de fonte serão alterados conforme as preferências de acessibilidade forem alteradas em cada plataforma. Portanto, para dar suporte a texto acessível, verifique se os estilos de *dispositivo* são usados como base para qualquer estilo de texto dentro de seu aplicativo.

As capturas de tela a seguir demonstram os estilos de dispositivo em cada plataforma, com o menor tamanho de fonte acessível:

[![Pequenos estilos de dispositivo acessíveis em cada plataforma](device-images/minimum-size.png)](device-images/minimum-size-large.png#lightbox "Pequenos estilos de dispositivo acessíveis em cada plataforma")

As capturas de tela a seguir demonstram os estilos de dispositivo em cada plataforma, com o maior tamanho de fonte acessível:

![Estilos de dispositivo grandes acessíveis em cada plataforma](device-images/maximum-size.png)

## <a name="related-links"></a>Links relacionados

- [Estilos de texto](~/xamarin-forms/user-interface/text/styles.md)
- [Extensões de marcação XAML](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [Estilos dinâmicos (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-styles-dynamicstyles)
- [Trabalhando com estilos (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithstyles)
- [Device. Styles](xref:Xamarin.Forms.Device.Styles)
- [ResourceDictionary](xref:Xamarin.Forms.ResourceDictionary)
- [Estilo](xref:Xamarin.Forms.Style)
- [Setter](xref:Xamarin.Forms.Setter)
