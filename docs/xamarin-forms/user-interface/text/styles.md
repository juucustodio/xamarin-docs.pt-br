---
title: Estilos de texto do xamarin. Forms
description: Este artigo explica como o estilo do texto em aplicativos xamarin. Forms. Estilos podem ser definidos uma vez e usados por muitas exibições, mas um estilo só pode ser usado com modos de exibição de um tipo.
ms.prod: xamarin
ms.assetid: 57C0CFD6-A568-46B8-ADA1-BF25681893CF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/22/2017
ms.openlocfilehash: 3fe189c0ef524a44dcbb71c883b57063748a06e3
ms.sourcegitcommit: c9651cad80c2865bc628349d30e82721c01ddb4a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/03/2019
ms.locfileid: "70228014"
---
# <a name="xamarinforms-text-styles"></a>Estilos de texto do xamarin. Forms

[![Baixar exemplo](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text)

_Estilo do texto no xamarin. Forms_

Estilos podem ser usados para ajustar a aparência de rótulos, entradas e editores. Estilos podem ser definidos uma vez e usados por muitas exibições, mas um estilo só pode ser usado com modos de exibição de um tipo.
Estilos podem ser fornecidos um `Key` e aplicadas seletivamente usando um controle específico `Style` propriedade.

<a name="Built-In_Styles" />

## <a name="built-in-styles"></a>Estilos internos

Xamarin. Forms inclui vários [internos](xref:Xamarin.Forms.Device.Styles) estilos para cenários comuns:

- `BodyStyle`
- `CaptionStyle`
- `ListItemDetailTextStyle`
- `ListItemTextStyle`
- `SubtitleStyle`
- `TitleStyle`

Para aplicar um dos estilos internos, use o `DynamicResource` extensão de marcação para especificar o estilo:

```xaml
<Label Text="I'm a Title" Style="{DynamicResource TitleStyle}"/>
```

No c#, os estilos internos são selecionados de `Device.Styles`:

```csharp
label.Style = Device.Styles.TitleStyle;
```

![Exemplo de estilos de dispositivo](styles-images/builtinstyles.png)

<a name="Custom_Styles" />

## <a name="custom-styles"></a>Estilos personalizados

Estilos consistem em setters e setters consistem em propriedades e os valores de propriedades serão definidos como.

No c#, um estilo personalizado para um rótulo com texto vermelho de tamanho 30 seria definido da seguinte maneira:

```csharp
var LabelStyle = new Style (typeof(Label)) {
    Setters = {
        new Setter {Property = Label.TextColorProperty, Value = Color.Red},
        new Setter {Property = Label.FontSizeProperty, Value = 30}
    }
};

var label = new Label { Text = "Check out my style.", Style = LabelStyle };
```

No XAML:

```xaml
<ContentPage.Resources>
    <ResourceDictionary>
        <Style x:Key="LabelStyle" TargetType="Label">
            <Setter Property="TextColor" Value="Red"/>
            <Setter Property="FontSize" Value="30"/>
        </Style>
    </ResourceDictionary>
</ContentPage.Resources>

<ContentPage.Content>
    <StackLayout>
        <Label Text="Check out my style." Style="{StaticResource LabelStyle}" />
    </StackLayout>
</ContentPage.Content>
```

Observe que os recursos (incluindo todos os estilos) são definidos dentro `ContentPage.Resources`, que é um irmão de mais familiar `ContentPage.Content` elemento.

![Exemplo de estilos personalizados](styles-images/customstyle.png)

<a name="Applying_Styles" />

## <a name="applying-styles"></a>Aplicando estilos

Quando um estilo tiver sido criado, ele pode ser aplicado a qualquer correspondência de exibição seu `TargetType`.

No XAML, estilos personalizados são aplicados aos modos de exibição, fornecendo seus `Style` propriedade com um `StaticResource` referenciando o estilo desejado de extensão de marcação:

```xaml
<Label Text="Check out my style." Style="{StaticResource LabelStyle}" />
```

No c#, estilos podem ser aplicados diretamente a um modo de exibição ou adicionados ao e recuperados de uma página `ResourceDictionary`. Para adicionar diretamente:

```csharp
var label = new Label { Text = "Check out my style.", Style = LabelStyle };
```

Para adicionar e recuperar a partir da página `ResourceDictionary`:

```csharp
this.Resources.Add ("LabelStyle", LabelStyle);
label.Style = (Style)Resources["LabelStyle"];
```

Estilos internos são aplicados de forma diferente, pois eles precisam responder às configurações de acessibilidade. Para aplicar estilos internos em XAML, o `DynamicResource` extensão de marcação é usada:

```xaml
<Label Text="I'm a Title" Style="{DynamicResource TitleStyle}"/>
```

No c#, os estilos internos são selecionados de `Device.Styles`:

```csharp
label.Style = Device.Styles.TitleStyle;
```

## <a name="accessibility"></a>Acessibilidade

Os estilos internos existem para tornar mais fácil respeitar as preferências de acessibilidade. Ao usar qualquer um dos estilos internos, tamanhos de fonte aumentará automaticamente se um usuário define suas preferências de acessibilidade adequadamente.

Considere o exemplo a seguir da mesma página de modos de exibição estilizada com os estilos internos com as configurações de acessibilidade habilitados e desabilitados:

Desabilitado:

![Estilos de dispositivo com acessibilidade desabilitada](styles-images/pre-access.png)

Habilitado:

![Estilos de dispositivo com acessibilidade habilitada](styles-images/post-access.png)

Para garantir a acessibilidade, certifique-se de que os estilos internos são usados como base para todos os estilos relacionada ao texto dentro de seu aplicativo e que você está usando estilos de forma consistente. Ver [estilos](~/xamarin-forms/user-interface/styles/index.md) para obter mais detalhes sobre a extensão e trabalhar com estilos, em geral.


## <a name="related-links"></a>Links relacionados

- [Criação de aplicativos móveis com xamarin. Forms, capítulo 12](https://developer.xamarin.com/r/xamarin-forms/book/chapter12.pdf)
- [Estilos](~/xamarin-forms/user-interface/styles/index.md)
- [Texto (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text)
- [Estilo](xref:Xamarin.Forms.Style)
