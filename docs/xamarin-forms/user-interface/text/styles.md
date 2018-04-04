---
title: Estilos
description: Estilo do texto em xamarin. Forms
ms.prod: xamarin
ms.assetid: 57C0CFD6-A568-46B8-ADA1-BF25681893CF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/22/2017
ms.openlocfilehash: f38e4bc9ecd66c1dc33e53fa5c9046ff363802e6
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="styles"></a>Estilos

_Estilo do texto em xamarin. Forms_


Estilos podem ser usados para ajustar a aparência de rótulos, entradas e editores. Estilos podem ser definidos uma vez e usados por vários modos de exibição, mas um estilo só pode ser usado com modos de exibição de um tipo.
Estilos podem ser atribuídos um `Key` e aplicadas seletivamente usando um controle específico `Style` propriedade.

Este artigo aborda os seguintes tópicos:

- **[Os estilos internos](#Built-In_Styles)**  &ndash; usar estilos internos para exibições com base em texto de estilo em todo o seu aplicativo.
- **[Estilos personalizados](#Custom_Styles)**  &ndash; definir estilos personalizados quando as opções integradas não são suficientes.
- **[Aplicando estilos](#Applying_Styles)**  &ndash; aplicar estilos internos e personalizados a seus modos de exibição.
- **[Acessibilidade](#Accessibility)**  &ndash; Certifique-se de que o texto respeita as configurações de acessibilidade.

<a name="Built-In_Styles" />

## <a name="built-in-styles"></a>Estilos internos

Xamarin. Forms inclui vários [interna](http://developer.xamarin.com/api/type/Xamarin.Forms.Device+Styles/) estilos para cenários comuns:

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

![](styles-images/builtinstyles.png "Exemplo de estilos de dispositivo")

<a name="Custom_Styles" />

## <a name="custom-styles"></a>Estilos personalizados

Estilos consistem setters e setters consistem em propriedades e definirá os valores de propriedades.

No c#, um estilo personalizado para um rótulo com o texto vermelho do tamanho 30 seria definido da seguinte maneira:

```csharp
var LabelStyle = new Style (typeof(Label)) {
    Setters = {
        new Setter {Property = Label.TextColorProperty, Value = Color.Red},
        new Setter {Property = Label.FontSizeProperty, Value = 30}
    }
};

var label = new Label { Text = "Check out my style.", Style = LabelStyle };
```

Em XAML:

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

Observe que os recursos (incluindo todos os estilos) são definidos em `ContentPage.Resources`, que é um irmão de mais familiar `ContentPage.Content` elemento.

![](styles-images/customstyle.png "Exemplo de estilos personalizados")

<a name="Applying_Styles" />

## <a name="applying-styles"></a>Aplicando estilos

Quando um estilo tiver sido criado, ele pode ser aplicado a correspondência de exibição seu `TargetType`.

Em XAML, estilos personalizados são aplicados aos modos de exibição, fornecendo seus `Style` propriedade com um `StaticResource` extensão de marcação referenciando o estilo desejado:

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

Os estilos internos são aplicados de forma diferente, pois eles precisam para responder às configurações de acessibilidade. Para aplicar estilos internos em XAML, o `DynamicResource` extensão de marcação é usada:

```xaml
<Label Text="I'm a Title" Style="{DynamicResource TitleStyle}"/>
```

No c#, os estilos internos são selecionados de `Device.Styles`:

```csharp
label.Style = Device.Styles.TitleStyle;
```

## <a name="accessibility"></a>Acessibilidade

Os estilos internos existem para tornar mais fácil de respeitar as preferências de acessibilidade. Ao usar qualquer um dos estilos internos, tamanhos de fonte aumentará automaticamente se o usuário define suas preferências de acessibilidade adequadamente.

Considere o exemplo a seguir da mesma página de modos de exibição formatado com os estilos internos com as configurações de acessibilidade habilitados e desabilitados:

Desabilitado:

![](styles-images/pre-access.png "Estilos de dispositivo com acessibilidade desabilitado")

Habilitado:

![](styles-images/post-access.png "Estilos de dispositivo com acessibilidade habilitado")

Para garantir a acessibilidade, certifique-se de que os estilos internos são usados como base para todos os estilos relacionada ao texto dentro de seu aplicativo, e que você está usando estilos de forma consistente. Consulte [estilos](~/xamarin-forms/user-interface/styles/index.md) para obter mais detalhes sobre a extensão e trabalhar com estilos em geral.


## <a name="related-links"></a>Links relacionados

- [Criando aplicativos móveis com o xamarin. Forms, capítulo 12](https://developer.xamarin.com/r/xamarin-forms/book/chapter12.pdf)
- [Estilos](~/xamarin-forms/user-interface/styles/index.md)
- [Texto (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Text)
- [Style](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/)
