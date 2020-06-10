---
Título: "responder a alterações de tema do sistema em Xamarin.Forms aplicativos" Descrição: " Xamarin.Forms os aplicativos podem responder às alterações de tema do sistema operacional usando o tipo OnAppTheme e a extensão de marcação DynamicResource."
MS. AssetID: D10506DD-BAA0-437F-A4AD-882D16E7B60D MS. Prod: xamarin MS. Technology: xamarin-Forms autor: davidbritch MS. Author: dabritch MS. Date: 04/22/2020 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="respond-to-system-theme-changes-in-xamarinforms-applications"></a>Responder a alterações de tema do sistema em Xamarin.Forms aplicativos

[![Baixar exemplo ](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-systemthemesdemo/)

Os dispositivos normalmente incluem temas leves e escuros, que se referem a um amplo conjunto de preferências de aparência que podem ser definidas no nível do sistema operacional. Os aplicativos devem respeitar esses temas do sistema e responder imediatamente quando o tema do sistema for alterado.

O tema do sistema pode ser alterado por vários motivos, dependendo da configuração do dispositivo. Isso inclui o tema do sistema que está sendo explicitamente alterado pelo usuário, ele se altera devido à hora do dia e é alterado devido a fatores ambientais, como baixa luz.

Xamarin.Formsos aplicativos podem responder às alterações de tema do sistema definindo recursos com a `AppThemeColor` classe, a `OnAppTheme<T>` classe e a `OnAppTheme` extensão de marcação. Esses recursos devem ser consumidos com a `DynamicResource` extensão de marcação.

> [!IMPORTANT]
> Responder a uma alteração de tema do sistema é experimental no momento e só pode ser usado pela definição do `AppTheme_Experimental` sinalizador. Para obter mais informações, consulte [sinalizadores experimentais](~/xamarin-forms/internals/experimental-flags.md).

Os requisitos a seguir devem ser atendidos para Xamarin.Forms que o responda a uma alteração de tema do sistema:

- Xamarin.Forms4,6 ou superior.
- iOS 13 ou superior.
- Android 10 (API 29) ou superior.
- O UWP Build 14393 ou superior.

As capturas de tela a seguir mostram páginas com tema, para temas de sistema leves e escuros no iOS e no Android:

[![Captura de tela da página principal de um aplicativo com tema, no Ios e no Android](system-theme-changes-images/main-page-both-themes.png "Página principal do aplicativo com tema")](system-theme-changes-images/main-page-both-themes-large.png#lightbox "Página principal do aplicativo com tema") 
 [ ![Captura de tela da página de detalhes de um aplicativo com tema, no Ios e no Android](system-theme-changes-images/detail-page-both-themes.png "Página de detalhes do aplicativo com tema")](system-theme-changes-images/detail-page-both-themes-large.png#lightbox "Página de detalhes do aplicativo com tema")

## <a name="define-and-consume-theme-resources"></a>Definir e consumir recursos de tema

Os recursos para temas claros e escuros podem ser definidos com a `AppThemeColor` classe, a `OnAppTheme<T>` classe e a `OnAppTheme` extensão de marcação. Com cada abordagem, esses recursos são automaticamente aplicados com base no valor do tema atual do sistema. Além disso, os objetos que consumirem esses recursos serão atualizados automaticamente se o tema do sistema for alterado enquanto um aplicativo estiver em execução.

### <a name="appthemecolor"></a>AppThemeColor

A `AppThemeColor` classe é usada para definir [`Color`](xref:Xamarin.Forms.Color) recursos para os temas de sistema claro e escuro. `AppThemeColor`os recursos devem ser definidos em um [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) :

```xaml
<Application ...>
    <Application.Resources>
        <AppThemeColor x:Key="PageBackgroundColor"
                       Light="White"
                       Dark="Black" />
        <AppThemeColor x:Key="NavigationBarColor"
                       Light="WhiteSmoke"
                       Dark="Teal" />
        <AppThemeColor x:Key="PrimaryColor"
                       Light="WhiteSmoke"
                       Dark="Teal" />
        <AppThemeColor x:Key="SecondaryColor"
                       Light="Black"
                       Dark="White" />
        <AppThemeColor x:Key="PrimaryTextColor"
                       Light="Black"
                       Dark="White" />
        <AppThemeColor x:Key="SecondaryTextColor"
                       Light="White"
                       Dark="White" />
        <AppThemeColor x:Key="TertiaryTextColor"
                       Light="Gray"
                       Dark="WhiteSmoke" />
        <AppThemeColor x:Key="TransparentColor"
                       Light="Transparent"
                       Dark="Transparent" />
    </Application.Resources>
</Application>
```

Cada `AppThemeColor` recurso deve ter um `x:Key` atributo, que fornece uma chave descritiva no [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) . O valor das `Light` Propriedades e `Dark` deve ser [`Color`](xref:Xamarin.Forms.Color) Objects. Além disso, uma `Default` propriedade pode ser definida como a `Color` para ser usada pelo objeto de consumo por padrão.

`AppThemeColor`os recursos podem ser consumidos em linha:

```xaml
<Label Text="This monkey reacts appropriately to ridiculous assertions and actions"
       TextColor="{DynamicResource PrimaryTextColor}" />
```

Como alternativa, os `AppThemeColor` recursos podem ser consumidos por objetos implícitos ou explícitos [`Style`](xref:Xamarin.Forms.Style) :

```xaml
<Style TargetType="NavigationPage">
    <Setter Property="BarBackgroundColor"
            Value="{DynamicResource NavigationBarColor}" />
    <Setter Property="BarTextColor"
            Value="{DynamicResource SecondaryColor}" />
</Style>
```

> [!IMPORTANT]
> `AppThemeColor`os recursos devem ser consumidos com a `DynamicResource` extensão de marcação. Isso garante que a aparência do objeto de consumo seja atualizada quando o tema do sistema for alterado.

### <a name="onappthemelttgt"></a>OnAppTheme &lt; T&gt;

A `OnAppTheme<T>` classe é usada para definir recursos de qualquer tipo para os temas de sistema claro e escuro. `OnAppTheme<T>`os recursos devem ser definidos em um [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) , com o `T` argumento especificado como o valor do `x:TypeArguments` atributo:

```xaml
<Application ...>
    <Application.Resources>
        <OnAppTheme x:Key="ImageLogo"
                    x:TypeArguments="FileImageSource"
                    Light="lightlogo.png"
                    Dark="darklogo.png" />
    </Application.Resources>
</Application>
```

Cada `OnAppTheme<T>` recurso deve ter um `x:Key` atributo, que fornece uma chave descritiva no [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) . O valor das `Light` Propriedades e `Dark` deve ser objetos do tipo definido como o `x:TypeArguments` atributo. Além disso, uma `Default` propriedade pode ser definida como um objeto do tipo `T` a ser usado pelo objeto de consumo por padrão.

`OnAppTheme<T>`os recursos podem ser consumidos em linha:

```xaml
<Image Source="{DynamicResource ImageLogo}"
       Aspect="AspectFit"
       HeightRequest="200" /
```

Como alternativa, os `OnAppTheme<T>` recursos podem ser consumidos por objetos implícitos ou explícitos [`Style`](xref:Xamarin.Forms.Style) :

```xaml
<Style x:Key="imageLogoStyle"
       TargetType="Image">
    <Setter Property="Source"
            Value="{DynamicResource ImageLogo}" />
    <Setter Property="Aspect"
            Value="AspectFit" />
</Style>
```

> [!IMPORTANT]
> `OnAppTheme<T>`os recursos devem ser consumidos com a `DynamicResource` extensão de marcação. Isso garante que a aparência do objeto de consumo seja atualizada quando o tema do sistema for alterado.

### <a name="onapptheme-markup-extension"></a>Extensão de marcação OnAppTheme

A `OnAppTheme` extensão de marcação permite que você especifique um recurso a ser consumido, como uma imagem ou cor, com base no tema atual do sistema. Ele fornece a mesma funcionalidade que a `OnAppTheme<T>` classe, mas com uma representação mais concisa:

```xaml
<ContentPage ...>
    <StackLayout Margin="20">
        <Label Text="This text is green in light mode, and red in dark mode."
               TextColor="{OnAppTheme Light=Green, Dark=Red}" />
        <Image Source="{OnAppTheme Light=lightlogo.png, Dark=darklogo.png}" />
    </StackLayout>
</ContentPage>
```

Neste exemplo, a cor do texto do primeiro [`Label`](xref:Xamarin.Forms.Label) é definida como verde quando o dispositivo está usando seu tema claro e é definido como vermelho quando o dispositivo está usando seu tema escuro. Da mesma forma, o [`Image`](xref:Xamarin.Forms.Image) exibe um arquivo de imagem diferente baseado no tema atual do sistema.

Para obter mais informações sobre a `OnAppTheme` extensão de marcação, consulte [OnAppTheme Markup Extension](~/xamarin-forms/xaml/markup-extensions/consuming.md#onapptheme-markup-extension).

## <a name="detect-the-current-system-theme"></a>Detectar o tema atual do sistema

O tema do sistema atual pode ser detectado ao obter o valor da `Application.RequestedTheme` Propriedade:

```csharp
OSAppTheme currentTheme = Application.Current.RequestedTheme;
```

A `RequestedTheme` propriedade retorna um `OSAppTheme` membro de enumeração. A enumeração `OSAppTheme` define os seguintes membros:

- `Unspecified`, que indica que o dispositivo está usando um tema não especificado.
- `Light`, que indica que o dispositivo está usando seu tema claro.
- `Dark`, que indica que o dispositivo está usando seu tema escuro.

## <a name="react-to-theme-changes"></a>Reagir às alterações do tema

O tema do sistema em um dispositivo pode ser alterado por vários motivos, dependendo de como o dispositivo está configurado. Xamarin.Formsos aplicativos podem ser notificados quando o tema do sistema muda de acordo com a manipulação do `Application.RequestedThemeChanged` evento:

```csharp
Application.Current.RequestedThemeChanged += (s, a) =>
{
    // Respond to the theme change
};
```

O `AppThemeChangedEventArgs` objeto, que acompanha o `RequestedThemeChanged` evento, tem uma única propriedade chamada `RequestedTheme` , do tipo `OSAppTheme` . Essa propriedade pode ser examinada para detectar o tema do sistema solicitado.

## <a name="related-links"></a>Links relacionados

- [SystemThemes (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-systemthemesdemo/)
- [Extensão de marcação OnAppTheme](~/xamarin-forms/xaml/markup-extensions/consuming.md#onapptheme-markup-extension)
- [Dicionários de recurso](~/xamarin-forms/xaml/resource-dictionaries.md)
- [Estilos dinâmicos emXamarin.Forms](~/xamarin-forms/user-interface/styles/xaml/dynamic.md)
- [Estilizando Xamarin.Forms aplicativos usando estilos XAML](~/xamarin-forms/user-interface/styles/xaml/index.md)
