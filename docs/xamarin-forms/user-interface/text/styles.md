---
title: Xamarin.FormsEstilos de texto
description: Este artigo explica como estilizar o texto em Xamarin.Forms aplicativos. Os estilos podem ser definidos uma vez e usados por muitas exibições, mas um estilo só pode ser usado com exibições de um tipo.
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 79a86fd7a2c0f5b82ca4b3e22b3ecedf42c5a0ba
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/28/2020
ms.locfileid: "84136143"
---
# <a name="xamarinforms-text-styles"></a>Xamarin.FormsEstilos de texto

[![Baixar exemplo ](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text)

_Estilo de texto no Xamarin. Forms_

Os estilos podem ser usados para ajustar a aparência de rótulos, entradas e editores. Os estilos podem ser definidos uma vez e usados por muitas exibições, mas um estilo só pode ser usado com exibições de um tipo.
Os estilos podem ser atribuídos a `Key` e aplicados seletivamente usando uma propriedade de controle específica `Style` .

<a name="Built-In_Styles" />

## <a name="built-in-styles"></a>Estilos internos

Xamarin.Formsinclui vários estilos [internos](xref:Xamarin.Forms.Device.Styles) para cenários comuns:

- `BodyStyle`
- `CaptionStyle`
- `ListItemDetailTextStyle`
- `ListItemTextStyle`
- `SubtitleStyle`
- `TitleStyle`

Para aplicar um dos estilos internos, use a `DynamicResource` extensão de marcação para especificar o estilo:

```xaml
<Label Text="I'm a Title" Style="{DynamicResource TitleStyle}"/>
```

Em C#, os estilos internos são selecionados de `Device.Styles` :

```csharp
label.Style = Device.Styles.TitleStyle;
```

![Exemplo de estilos de dispositivo](styles-images/builtinstyles.png)

<a name="Custom_Styles" />

## <a name="custom-styles"></a>Estilos personalizados

Os estilos consistem em setters e setters que consistem em Propriedades e os valores para os quais as propriedades serão definidas.

Em C#, um estilo personalizado para um rótulo com texto vermelho de tamanho 30 seria definido da seguinte maneira:

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

Observe que os recursos (incluindo todos os estilos) são definidos em `ContentPage.Resources` , que é um irmão do elemento mais familiar `ContentPage.Content` .

![Exemplo de estilos personalizados](styles-images/customstyle.png)

<a name="Applying_Styles" />

## <a name="applying-styles"></a>Aplicando estilos

Depois que um estilo é criado, ele pode ser aplicado a qualquer exibição correspondente `TargetType` .

No XAML, os estilos personalizados são aplicados aos modos de exibição fornecendo sua `Style` propriedade com uma `StaticResource` extensão de marcação que faz referência ao estilo desejado:

```xaml
<Label Text="Check out my style." Style="{StaticResource LabelStyle}" />
```

No C#, os estilos podem ser aplicados diretamente a uma exibição ou adicionados e recuperados de uma página `ResourceDictionary` . Para adicionar diretamente:

```csharp
var label = new Label { Text = "Check out my style.", Style = LabelStyle };
```

Para adicionar e recuperar a partir da página `ResourceDictionary` :

```csharp
this.Resources.Add ("LabelStyle", LabelStyle);
label.Style = (Style)Resources["LabelStyle"];
```

Os estilos internos são aplicados de forma diferente, pois precisam responder às configurações de acessibilidade. Para aplicar estilos internos em XAML, a extensão de `DynamicResource` marcação é usada:

```xaml
<Label Text="I'm a Title" Style="{DynamicResource TitleStyle}"/>
```

Em C#, os estilos internos são selecionados de `Device.Styles` :

```csharp
label.Style = Device.Styles.TitleStyle;
```

## <a name="accessibility"></a>Acessibilidade

Os estilos internos existem para facilitar o respeitar as preferências de acessibilidade. Ao usar qualquer um dos estilos internos, os tamanhos de fonte aumentarão automaticamente se um usuário definir suas preferências de acessibilidade de forma adequada.

Considere o exemplo a seguir da mesma página de exibições estilizadas com os estilos internos com as configurações de acessibilidade habilitadas e desabilitadas:

Desabilitado:

![Estilos de dispositivo com acessibilidade desabilitada](styles-images/pre-access.png)

Habilitado:

![Estilos de dispositivo com acessibilidade habilitada](styles-images/post-access.png)

Para garantir a acessibilidade, verifique se os estilos internos são usados como base para qualquer estilo relacionado a texto em seu aplicativo e se você está usando os estilos de forma consistente. Consulte os [estilos](~/xamarin-forms/user-interface/styles/index.md) para obter mais detalhes sobre como estender e trabalhar com estilos em geral.

## <a name="related-links"></a>Links relacionados

- [Criando aplicativos móveis com o Xamarin.Forms , capítulo 12](https://developer.xamarin.com/r/xamarin-forms/book/chapter12.pdf)
- [Estilos](~/xamarin-forms/user-interface/styles/index.md)
- [Texto (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text)
- [Estilo](xref:Xamarin.Forms.Style)
