---
title: Referência de controles de páginas de DataPages
description: Este artigo apresenta os controles que estão disponíveis no pacote NuGet de DataPages do Xamarin. Forms.
ms.prod: xamarin
ms.assetid: 891615D0-E8BD-4ACC-A7F0-4C3725FBCC31
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/01/2017
ms.openlocfilehash: 2af4bc3bd4dc6ae47f573c58c84aad54f41d42cd
ms.sourcegitcommit: 41a029c69925e3a9d2de883751ebfd649e8747cd
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/13/2019
ms.locfileid: "68980810"
---
# <a name="datapages-controls-reference"></a>Referência de controles de páginas de DataPages

![](~/media/shared/preview.png "Essa API está atualmente em versão prévia")

> [!IMPORTANT]
> As páginas de datarequer uma referência de tema do Xamarin. Forms para renderizar. Isso envolve a instalação do pacote NuGet [xamarin. Forms. Theme. base](https://www.nuget.org/packages/Xamarin.Forms.Theme.Base/) em seu projeto, seguido pelos pacotes NuGet [xamarin. Forms. Theme. Light](https://www.nuget.org/packages/Xamarin.Forms.Theme.Light/) ou [Xamarin. Forms. Theme. Dark](https://www.nuget.org/packages/Xamarin.Forms.Theme.Dark/) .

O NuGet das páginas de dados Xamarin. Forms inclui vários controles que podem tirar proveito da Associação de fonte de dado.

Para usar esses controles em XAML, verifique se o namespace foi incluído, por exemplo, consulte `xmlns:pages` a declaração abaixo:

```xaml
<ContentPage
    xmlns="http://xamarin.com/schemas/2014/forms"
    xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
    xmlns:pages="clr-namespace:Xamarin.Forms.Pages;assembly=Xamarin.Forms.Pages"
    x:Class="DataPagesDemo.Detail">
```

Os exemplos a seguir `DynamicResource` incluem referências que precisam existir no dicionário de recursos do projeto para funcionar. Também há um exemplo de como criar um [controle personalizado](#custom)

## <a name="built-in-controls"></a>Controles internos

* [HeroImage](#heroimage)
* [ListItem](#listitem)

<a name="heroimage" />

### <a name="heroimage"></a>HeroImage

O `HeroImage` controle tem quatro propriedades:

* Texto
* Detalhe
* ImageSource
* Aspecto

```xaml
<pages:HeroImage
    ImageSource="{ DynamicResource HeroImageImage }"
    Text="Keith Ballinger"
    Detail="Xamarin"
/>
```

**Android**

![](controls-images/heroimage-light-android.png "Controle HeroImage no Android") ![](controls-images/heroimage-dark-android.png "HeroImage controle no Android")

**iOS**

![](controls-images/heroimage-light-ios.png "Controle HeroImage no iOS") ![](controls-images/heroimage-dark-ios.png "HeroImage controle no iOS")


<a name="listitem" />

### <a name="listitem"></a>ListItem

O `ListItem` layout do controle é semelhante às linhas nativas de lista ou tabela do IOS e do Android, no entanto, ele também pode ser usado como uma exibição regular. No código de exemplo abaixo, ele é mostrado hospedado dentro `StackLayout`de um, mas também pode ser usado em controles de lista de scolling associados a dados.

Há cinco propriedades:

* Título
* Detalhe
* ImageSource
* PlaceholdImageSource
* Aspecto

```xaml
<StackLayout Spacing="0">
    <pages:ListItemControl
        Detail="Xamarin"
        ImageSource="{ DynamicResource UserImage }"
        Title="Miguel de Icaza"
        PlaceholdImageSource="{ DynamicResource IconImage }"
    />
```

Essas capturas de tela `ListItem` mostram o nas plataformas Ios e Android usando os temas claro e escuro:

**Android**

![](controls-images/listitem-light-android.png "Controle de item de lista no Android") ![](controls-images/listitem-dark-android.png "controle ListItem no Android")

**iOS**

![](controls-images/listitem-light-ios.png "Controle de item de lista no iOS") ![](controls-images/listitem-dark-ios.png "controle ListItem no iOS")


## <a name="custom-control-example"></a>Exemplo de controle personalizado

O objetivo desse controle personalizado `CardView` é se assemelhar ao Android CardView nativo.

Ele conterá três propriedades:

* Texto
* Detalhe
* ImageSource

O objetivo é um controle personalizado que se parecerá com o código abaixo (Observe que é `xmlns:local` necessário um personalizado que referencie o assembly atual):

```xaml
<local:CardView
  ImageSource="{ DynamicResource CardViewImage }"
  Text="CardView Text"
  Detail="CardView Detail"
/>
```

Ele deve ser semelhante às capturas de tela abaixo usando cores correspondentes aos temas internos leves e escuros:

**Android**

![](controls-images/cardview-light-android.png "Controle personalizado de widgets CardView no Android") ![](controls-images/cardview-dark-android.png "controle personalizado de widgets CardView no Android")

**iOS**

![](controls-images/cardview-light-ios.png "Controle personalizado de widgets CardView no iOS") ![](controls-images/cardview-dark-ios.png "widgets CardView Custom Control no iOS")

<a name="custom" />

### <a name="building-the-custom-cardview"></a>Criando o CardView personalizado

1. [Subclasse de DataView](#1)
2. [Definir fonte, layout e margens](#2)
3. [Criar estilos para os filhos do controle](#3)
4. [Criar o modelo de layout de controle](#4)
5. [Adicionar os recursos específicos do tema](#5)
6. [Definir o ControlTemplate para a classe CardView](#6)
7. [Adicionar o controle a uma página](#7)

<a name="1" />

#### <a name="1-dataview-subclass"></a>1. Subclasse de DataView

A C# subclasse de `DataView` define as propriedades vinculáveis do controle.

```csharp
public class CardView : DataView
{
    public static readonly BindableProperty TextProperty =
        BindableProperty.Create ("Text", typeof (string), typeof (CardView), null, BindingMode.TwoWay);

    public string Text
    {
        get { return (string)GetValue (TextProperty); }
        set { SetValue (TextProperty, value); }
    }

    public static readonly BindableProperty DetailProperty =
        BindableProperty.Create ("Detail", typeof (string), typeof (CardView), null, BindingMode.TwoWay);

    public string Detail
    {
        get { return (string)GetValue (DetailProperty); }
        set { SetValue (DetailProperty, value); }
    }

    public static readonly BindableProperty ImageSourceProperty =
        BindableProperty.Create ("ImageSource", typeof (ImageSource), typeof (CardView), null, BindingMode.TwoWay);

    public ImageSource ImageSource
    {
        get { return (ImageSource)GetValue (ImageSourceProperty); }
        set { SetValue (ImageSourceProperty, value); }
    }

    public CardView()
    {
    }
}
```

<a name="2" />

#### <a name="2-define-font-layout-and-margins"></a>2. Definir fonte, layout e margens

O designer de controle descobriria esses valores como parte do design da interface do usuário para o controle personalizado. Quando especificações específicas da plataforma são necessárias, o `OnPlatform` elemento é usado.

Observe que alguns valores se referem a `StaticResource`s – eles serão definidos na [etapa 5](#5).

```xml
<!-- CARDVIEW FONT SIZES -->
<OnPlatform x:TypeArguments="x:Double" x:Key="CardViewTextFontSize">
        <On Platform="iOS, Android" Value="15" />
</OnPlatform>

<OnPlatform x:TypeArguments="x:Double" x:Key="CardViewDetailFontSize">
        <On Platform="iOS, Android" Value="13" />
</OnPlatform>

<OnPlatform x:TypeArguments="Color"    x:Key="CardViewTextTextColor">
        <On Platform="iOS" Value="{StaticResource iOSCardViewTextTextColor}" />
        <On Platform="Android" Value="{StaticResource AndroidCardViewTextTextColor}" />
</OnPlatform>

<OnPlatform x:TypeArguments="Thickness"    x:Key="CardViewTextlMargin">
        <On Platform="iOS" Value="12,10,12,4" />
        <On Platform="Android" Value="20,0,20,5" />
</OnPlatform>

<OnPlatform x:TypeArguments="Color"    x:Key="CardViewDetailTextColor">
        <On Platform="iOS" Value="{StaticResource iOSCardViewDetailTextColor}" />
        <On Platform="Android" Value="{StaticResource AndroidCardViewDetailTextColor}" />
</OnPlatform>

<OnPlatform x:TypeArguments="Thickness"    x:Key="CardViewDetailMargin">
        <On Platform="iOS" Value="12,0,10,12" />
        <On Platform="Android" Value="20,0,20,20" />
</OnPlatform>

<OnPlatform x:TypeArguments="Color"    x:Key="CardViewBackgroundColor">
        <On Platform="iOS" Value="{StaticResource iOSCardViewBackgroundColor}" />
        <On Platform="Android" Value="{StaticResource AndroidCardViewBackgroundColor}" />
</OnPlatform>

<OnPlatform x:TypeArguments="x:Double" x:Key="CardViewShadowSize">
        <On Platform="iOS" Value="2" />
        <On Platform="Android" Value="5" />
</OnPlatform>

<OnPlatform x:TypeArguments="x:Double" x:Key="CardViewCornerRadius">
        <On Platform="iOS" Value="0" />
        <On Platform="Android" Value="4" />
</OnPlatform>

<OnPlatform x:TypeArguments="Color"    x:Key="CardViewShadowColor">
        <On Platform="iOS, Android" Value="#CDCDD1" />
</OnPlatform>
```

<a name="3" />

#### <a name="3-create-styles-for-the-controls-children"></a>3. Criar estilos para os filhos do controle

Referencie todos os elementos definidos para criar os filhos que serão usados no controle personalizado:

```xml
<!-- EXPLICIT STYLES (will be Classes) -->
<Style TargetType="Label" x:Key="CardViewTextStyle">
    <Setter Property="FontSize" Value="{ StaticResource CardViewTextFontSize }" />
    <Setter Property="TextColor" Value="{ StaticResource CardViewTextTextColor }" />
    <Setter Property="HorizontalOptions" Value="Start" />
    <Setter Property="Margin" Value="{ StaticResource CardViewTextlMargin }" />
    <Setter Property="HorizontalTextAlignment" Value="Start" />
</Style>

<Style TargetType="Label" x:Key="CardViewDetailStyle">
    <Setter Property="HorizontalTextAlignment" Value="Start" />
    <Setter Property="TextColor" Value="{ StaticResource CardViewDetailTextColor }" />
    <Setter Property="FontSize" Value="{ StaticResource CardViewDetailFontSize }" />
    <Setter Property="HorizontalOptions" Value="Start" />
    <Setter Property="Margin" Value="{ StaticResource CardViewDetailMargin }" />
</Style>

<Style TargetType="Image" x:Key="CardViewImageImageStyle">
    <Setter Property="HorizontalOptions" Value="Center" />
    <Setter Property="VerticalOptions" Value="Center" />
    <Setter Property="WidthRequest" Value="220"/>
    <Setter Property="HeightRequest" Value="165"/>
</Style>
```

<a name="4" />

#### <a name="4-create-the-control-layout-template"></a>4. Criar o modelo de layout de controle

O design visual do controle personalizado é declarado explicitamente no modelo de controle, usando os recursos definidos acima:

```xml
<!--- CARDVIEW -->
<ControlTemplate x:Key="CardViewControlControlTemplate">
  <StackLayout
    Spacing="0"
    BackgroundColor="{ TemplateBinding BackgroundColor }"
  >

    <!-- CARDVIEW IMAGE -->
    <Image
      Source="{ TemplateBinding ImageSource }"
      HorizontalOptions="FillAndExpand"
      VerticalOptions="StartAndExpand"
      Aspect="AspectFill"
      Style="{ StaticResource CardViewImageImageStyle }"
    />

    <!-- CARDVIEW TEXT -->
    <Label
      Text="{ TemplateBinding Text }"
      LineBreakMode="WordWrap"
      VerticalOptions="End"
      Style="{ StaticResource CardViewTextStyle }"
    />


    <!-- CARDVIEW DETAIL -->
    <Label
      Text="{ TemplateBinding Detail }"
      LineBreakMode="WordWrap"
      VerticalOptions="End"
      Style="{ StaticResource CardViewDetailStyle }" />

  </StackLayout>

</ControlTemplate>
```

<a name="5" />

#### <a name="5-add-the-theme-specific-resources"></a>5. Adicionar os recursos específicos do tema

Como esse é um controle personalizado, adicione os recursos que correspondem ao tema que você está usando o dicionário de recursos:

##### <a name="light-theme-colors"></a>Cores do tema claro

```xaml
<Color x:Key="iOSCardViewBackgroundColor">#FFFFFF</Color>
<Color x:Key="AndroidCardViewBackgroundColor">#FFFFFF</Color>

<Color x:Key="AndroidCardViewTextTextColor">#030303</Color>
<Color x:Key="iOSCardViewTextTextColor">#030303</Color>

<Color x:Key="AndroidCardViewDetailTextColor">#8F8E94</Color>
<Color x:Key="iOSCardViewDetailTextColor">#8F8E94</Color>
```

##### <a name="dark-theme-colors"></a>Cores de tema escuro

```xaml
<!-- CARD VIEW COLORS -->
            <Color x:Key="iOSCardViewBackgroundColor">#404040</Color>
            <Color x:Key="AndroidCardViewBackgroundColor">#404040</Color>

            <Color x:Key="AndroidCardViewTextTextColor">#FFFFFF</Color>
            <Color x:Key="iOSCardViewTextTextColor">#FFFFFF</Color>

            <Color x:Key="AndroidCardViewDetailTextColor">#B5B4B9</Color>
            <Color x:Key="iOSCardViewDetailTextColor">#B5B4B9</Color>
```

<a name="6" />

#### <a name="6-set-the-controltemplate-for-the-cardview-class"></a>6. Definir o ControlTemplate para a classe CardView

Por fim, verifique C# se a classe criada na [etapa 1](#1) usa o modelo de controle definido na [etapa 4](#4) usando um `Style`elemento `Setter`

```xml
<Style TargetType="local:CardView">
    <Setter Property="ControlTemplate" Value="{ StaticResource CardViewControlControlTemplate }" />
  ... some custom styling omitted
  <Setter Property="BackgroundColor" Value="{ StaticResource CardViewBackgroundColor }" />
</Style>
```

<a name="7" />

#### <a name="7-add-the-control-to-a-page"></a>7. Adicionar o controle a uma página

O `CardView` controle agora pode ser adicionado a uma página. O exemplo a seguir mostra que ele é `StackLayout`hospedado em um:

```xaml
<StackLayout Spacing="0">
  <local:CardView
    Margin="12,6"
    ImageSource="{ DynamicResource CardViewImage }"
    Text="CardView Text"
    Detail="CardView Detail"
  />
</StackLayout>

```
