---
Título: "referência de controles de DataPages" Descrição: "Este artigo apresenta os controles que estão disponíveis no Xamarin.Forms pacote NuGet de DataPages."
MS. Prod: xamarin MS. AssetID: 891615D0-E8BD-4ACC-A7F0-4C3725FBCC31 MS. Technology: xamarin-Forms autor: davidbritch MS. Author: dabritch MS. Date: 12/01/2017 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="datapages-controls-reference"></a>Referência de controles de páginas de DataPages

![](~/media/shared/preview.png "This API is currently in preview")

> [!IMPORTANT]
> As páginas de datarequer uma Xamarin.Forms referência de tema para renderizar. Isso envolve a instalação do [ Xamarin.Forms . ](https://www.nuget.org/packages/Xamarin.Forms.Theme.Base/)O pacote NuGet do Theme. base em seu projeto, seguido pelo [ Xamarin.Forms . Theme. Light](https://www.nuget.org/packages/Xamarin.Forms.Theme.Light/) ou [ Xamarin.Forms . ](https://www.nuget.org/packages/Xamarin.Forms.Theme.Dark/)Os pacotes NuGet. Dark do tema.

O Xamarin.Forms NuGet de DataPages inclui vários controles que podem tirar proveito da Associação de fonte de dados.

Para usar esses controles em XAML, verifique se o namespace foi incluído, por exemplo, consulte a `xmlns:pages` declaração abaixo:

```xaml
<ContentPage
    xmlns="http://xamarin.com/schemas/2014/forms"
    xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
    xmlns:pages="clr-namespace:Xamarin.Forms.Pages;assembly=Xamarin.Forms.Pages"
    x:Class="DataPagesDemo.Detail">
```

Os exemplos a seguir incluem `DynamicResource` referências que precisam existir no dicionário de recursos do projeto para funcionar. Também há um exemplo de como criar um [controle personalizado](#custom-control-example).

## <a name="built-in-controls"></a>Controles internos

* [HeroImage](#heroimage)
* [Item](#listitem)

### <a name="heroimage"></a>HeroImage

O `HeroImage` controle tem quatro propriedades:

* Text
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

![](controls-images/heroimage-light-android.png "Controle HeroImage no Android") ![](controls-images/heroimage-dark-android.png "Controle HeroImage no Android")

**iOS**

![](controls-images/heroimage-light-ios.png "Controle HeroImage no iOS") ![](controls-images/heroimage-dark-ios.png "Controle HeroImage no iOS")

### <a name="listitem"></a>Item

O `ListItem` layout do controle é semelhante às linhas nativas de lista ou tabela do IOS e do Android, no entanto, ele também pode ser usado como uma exibição regular. No código de exemplo abaixo, ele é mostrado hospedado dentro de um `StackLayout` , mas também pode ser usado em controles de lista de scolling associados a dados.

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

Essas capturas de tela mostram o `ListItem` nas plataformas Ios e Android usando os temas claro e escuro:

**Android**

![](controls-images/listitem-light-android.png "Controle de ListItem no Android") ![](controls-images/listitem-dark-android.png "Controle de ListItem no Android")

**iOS**

![](controls-images/listitem-light-ios.png "Controle de ListItem no iOS") ![](controls-images/listitem-dark-ios.png "Controle de ListItem no iOS")

## <a name="custom-control-example"></a>Exemplo de controle personalizado

O objetivo desse controle personalizado `CardView` é se assemelhar ao Android CardView nativo.

Ele conterá três propriedades:

* Text
* Detalhe
* ImageSource

O objetivo é um controle personalizado que se parecerá com o código abaixo (Observe que `xmlns:local` é necessário um personalizado que referencie o assembly atual):

```xaml
<local:CardView
  ImageSource="{ DynamicResource CardViewImage }"
  Text="CardView Text"
  Detail="CardView Detail"
/>
```

Ele deve ser semelhante às capturas de tela abaixo usando cores correspondentes aos temas internos leves e escuros:

**Android**

![](controls-images/cardview-light-android.png "CardView controle personalizado no Android") ![](controls-images/cardview-dark-android.png "CardView controle personalizado no Android")

**iOS**

![](controls-images/cardview-light-ios.png "CardView controle personalizado no iOS") ![](controls-images/cardview-dark-ios.png "CardView controle personalizado no iOS")

### <a name="building-the-custom-cardview"></a>Criando o CardView personalizado

1. [Subclasse de DataView](#1-dataview-subclass)
2. [Definir fonte, layout e margens](#2-define-font-layout-and-margins)
3. [Criar estilos para os filhos do controle](#3-create-styles-for-the-controls-children)
4. [Criar o modelo de layout de controle](#4-create-the-control-layout-template)
5. [Adicionar os recursos específicos do tema](#5-add-the-theme-specific-resources)
6. [Definir o ControlTemplate para a classe CardView](#6-set-the-controltemplate-for-the-cardview-class)
7. [Adicionar o controle a uma página](#7-add-the-control-to-a-page)

#### <a name="1-dataview-subclass"></a>subclasse 1. DataView

A subclasse C# de `DataView` define as propriedades vinculáveis do controle.

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

#### <a name="2-define-font-layout-and-margins"></a>2. definir a fonte, o layout e as margens

O designer de controle descobriria esses valores como parte do design da interface do usuário para o controle personalizado. Quando especificações específicas da plataforma são necessárias, o `OnPlatform` elemento é usado.

Observe que alguns valores se referem a `StaticResource` s – eles serão definidos na [etapa 5](#5-add-the-theme-specific-resources).

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

#### <a name="3-create-styles-for-the-controls-children"></a>3. criar estilos para os filhos do controle

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

#### <a name="4-create-the-control-layout-template"></a>4. criar o modelo de layout de controle

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

#### <a name="5-add-the-theme-specific-resources"></a>5. adicionar os recursos específicos ao tema

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

#### <a name="6-set-the-controltemplate-for-the-cardview-class"></a>6. defina o ControlTemplate para a classe CardView

Por fim, verifique se a classe C# criada na [etapa 1](#1-dataview-subclass) usa o modelo de controle definido na [etapa 4](#4-create-the-control-layout-template) usando um `Style` `Setter` elemento

```xml
<Style TargetType="local:CardView">
    <Setter Property="ControlTemplate" Value="{ StaticResource CardViewControlControlTemplate }" />
  ... some custom styling omitted
  <Setter Property="BackgroundColor" Value="{ StaticResource CardViewBackgroundColor }" />
</Style>
```

#### <a name="7-add-the-control-to-a-page"></a>7. Adicionar o controle a uma página

O `CardView` controle agora pode ser adicionado a uma página. O exemplo a seguir mostra que ele é hospedado em um `StackLayout` :

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
