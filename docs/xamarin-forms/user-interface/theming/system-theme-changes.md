---
title: Responder a alterações de tema do sistema em Xamarin.Forms aplicativos
description: Xamarin.Formsos aplicativos podem responder às alterações de tema do sistema operacional usando o tipo OnAppTheme e a extensão de marcação DynamicResource.
ms.assetid: D10506DD-BAA0-437F-A4AD-882D16E7B60D
ms.prod: xamarin
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/17/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: b451fe004ca21c8770658f31c9c38253e073c259
ms.sourcegitcommit: 82eabb0eaa4a674897aa6d5e64efb91fd580c330
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/08/2020
ms.locfileid: "86100177"
---
# <a name="respond-to-system-theme-changes-in-xamarinforms-applications"></a>Responder a alterações de tema do sistema em Xamarin.Forms aplicativos

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-systemthemesdemo/)

Os dispositivos normalmente incluem temas leves e escuros, que se referem a um amplo conjunto de preferências de aparência que podem ser definidas no nível do sistema operacional. Os aplicativos devem respeitar esses temas do sistema e responder imediatamente quando o tema do sistema for alterado.

O tema do sistema pode ser alterado por vários motivos, dependendo da configuração do dispositivo. Isso inclui o tema do sistema que está sendo explicitamente alterado pelo usuário, ele se altera devido à hora do dia e é alterado devido a fatores ambientais, como baixa luz.

Xamarin.Formsos aplicativos podem responder às alterações de tema do sistema, consumindo recursos com a `AppThemeBinding` extensão de marcação e os `SetAppThemeColor` métodos de `SetOnAppTheme<T>` extensão e.

> [!IMPORTANT]
> Responder a uma alteração de tema do sistema é experimental no momento e só pode ser usado pela definição do `AppTheme_Experimental` sinalizador. Para obter mais informações, consulte [sinalizadores experimentais](~/xamarin-forms/internals/experimental-flags.md).

Os requisitos a seguir devem ser atendidos para Xamarin.Forms que o responda a uma alteração de tema do sistema:

- Xamarin.Forms4.6.0.967 ou superior.
- iOS 13 ou superior.
- Android 10 (API 29) ou superior.
- O UWP Build 14393 ou superior.

As capturas de tela a seguir mostram páginas com tema, para temas de sistema leves e escuros no iOS e no Android:

[![Captura de tela da página principal de um aplicativo com tema, no Ios e no Android](system-theme-changes-images/main-page-both-themes.png "Página principal do aplicativo com tema")](system-theme-changes-images/main-page-both-themes-large.png#lightbox "Página principal do aplicativo com tema") 
 [ ![Captura de tela da página de detalhes de um aplicativo com tema, no Ios e no Android](system-theme-changes-images/detail-page-both-themes.png "Página de detalhes do aplicativo com tema")](system-theme-changes-images/detail-page-both-themes-large.png#lightbox "Página de detalhes do aplicativo com tema")

## <a name="define-and-consume-theme-resources"></a>Definir e consumir recursos de tema

Os recursos para temas claros e escuros podem ser consumidos com a `AppThemeBinding` extensão de marcação e os `SetAppThemeColor` métodos de `SetOnAppTheme<T>` extensão e. Com essas abordagens, os recursos são automaticamente aplicados com base no valor do tema atual do sistema. Além disso, os objetos que consumirem esses recursos serão atualizados automaticamente se o tema do sistema for alterado enquanto um aplicativo estiver em execução.

### <a name="appthemebinding-markup-extension"></a>Extensão de marcação AppThemeBinding

A `AppThemeBinding` extensão de marcação permite que você consuma um recurso, como uma imagem ou cor, com base no tema atual do sistema:

```xaml
<ContentPage ...>
    <StackLayout Margin="20">
        <Label Text="This text is green in light mode, and red in dark mode."
               TextColor="{AppThemeBinding Light=Green, Dark=Red}" />
        <Image Source="{AppThemeBinding Light=lightlogo.png, Dark=darklogo.png}" />
    </StackLayout>
</ContentPage>
```

Neste exemplo, a cor do texto do primeiro [`Label`](xref:Xamarin.Forms.Label) é definida como verde quando o dispositivo está usando seu tema claro e é definido como vermelho quando o dispositivo está usando seu tema escuro. Da mesma forma, o [`Image`](xref:Xamarin.Forms.Image) exibe um arquivo de imagem diferente baseado no tema atual do sistema.

Além disso, os recursos definidos em um [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) podem ser consumidos com a `StaticResource` extensão de marcação:

```xaml
<ContentPage ...>
    <ContentPage.Resources>

        <!-- Light colors -->
        <Color x:Key="LightPrimaryColor">WhiteSmoke</Color>
        <Color x:Key="LightSecondaryColor">Black</Color>

        <!-- Dark colors -->
        <Color x:Key="DarkPrimaryColor">Teal</Color>
        <Color x:Key="DarkSecondaryColor">White</Color>

        <Style x:Key="ButtonStyle"
               TargetType="Button">
            <Setter Property="BackgroundColor"
                    Value="{AppThemeBinding Light={StaticResource LightPrimaryColor}, Dark={StaticResource DarkPrimaryColor}}" />
            <Setter Property="TextColor"
                    Value="{AppThemeBinding Light={StaticResource LightSecondaryColor}, Dark={StaticResource DarkSecondaryColor}}" />
        </Style>

    </ContentPage.Resources>

    <Grid BackgroundColor="{AppThemeBinding Light={StaticResource LightPrimaryColor}, Dark={StaticResource DarkPrimaryColor}}">
      <Button Text="MORE INFO"
              Style="{StaticResource ButtonStyle}" />
    </Grid>    
</ContentPage>    
```

Neste exemplo, a cor do plano de fundo do [`Grid`](xref:Xamarin.Forms.Grid) e do [`Button`](xref:Xamarin.Forms.Button) estilo é alterado com base no fato de o dispositivo estar usando seu tema claro ou tema escuro.

Para obter mais informações sobre a `AppThemeBinding` extensão de marcação, consulte [AppThemeBinding Markup Extension](~/xamarin-forms/xaml/markup-extensions/consuming.md#appthemebinding-markup-extension).

### <a name="extension-methods"></a>Métodos de extensão

Xamarin.Formsinclui `SetAppThemeColor` e `SetOnAppTheme<T>` métodos de extensão que permitem que os [`VisualElement`](xref:Xamarin.Forms.VisualElement) objetos respondam às alterações do tema do sistema.

O `SetAppThemeColor` método permite [`Color`](xref:Xamarin.Forms.Color) que os objetos sejam especificados que serão definidos em uma propriedade de destino com base no tema atual do sistema:

```csharp
Label label = new Label();
label.SetAppThemeColor(Label.TextColorProperty, Color.Green, Color.Red);
```

Neste exemplo, a cor do texto do [`Label`](xref:Xamarin.Forms.Label) é definida como verde quando o dispositivo está usando seu tema claro e é definido como vermelho quando o dispositivo está usando seu tema escuro.

O `SetOnAppTheme<T>` método permite que objetos do tipo `T` sejam especificados que serão definidos em uma propriedade de destino com base no tema atual do sistema:

```csharp
Image image = new Image();
image.SetOnAppTheme<FileImageSource>(Image.SourceProperty, "lightlogo.png", "darklogo.png");
```

Neste exemplo, [`Image`](xref:Xamarin.Forms.Image) exibe `lightlogo.png` quando o dispositivo está usando seu tema claro e `darklogo.png` quando o dispositivo está usando seu tema escuro.

## <a name="detect-the-current-system-theme"></a>Detectar o tema atual do sistema

O tema do sistema atual pode ser detectado ao obter o valor da `Application.RequestedTheme` Propriedade:

```csharp
OSAppTheme currentTheme = Application.Current.RequestedTheme;
```

A `RequestedTheme` propriedade retorna um `OSAppTheme` membro de enumeração. A enumeração `OSAppTheme` define os seguintes membros:

- `Unspecified`, que indica que o dispositivo está usando um tema não especificado.
- `Light`, que indica que o dispositivo está usando seu tema claro.
- `Dark`, que indica que o dispositivo está usando seu tema escuro.

## <a name="set-the-current-user-theme"></a>Definir o tema do usuário atual

O tema usado pelo aplicativo pode ser definido com a `Application.UserAppTheme` propriedade, que é do tipo `OSAppTheme` , independentemente de qual tema do sistema está operacional no momento:

```csharp
Application.Current.UserAppTheme = OSAppTheme.Dark;
```

Neste exemplo, o aplicativo está definido para usar o tema definido para o modo escuro do sistema, independentemente de qual tema do sistema está operacional no momento.

> [!NOTE]
> Defina a `UserAppTheme` propriedade como `OSAppTheme.Unspecified` padrão para o tema do sistema operacional.

## <a name="react-to-theme-changes"></a>Reagir às alterações do tema

O tema do sistema em um dispositivo pode ser alterado por vários motivos, dependendo de como o dispositivo está configurado. Xamarin.Formsos aplicativos podem ser notificados quando o tema do sistema muda de acordo com a manipulação do `Application.RequestedThemeChanged` evento:

```csharp
Application.Current.RequestedThemeChanged += (s, a) =>
{
    // Respond to the theme change
};
```

O `AppThemeChangedEventArgs` objeto, que acompanha o `RequestedThemeChanged` evento, tem uma única propriedade chamada `RequestedTheme` , do tipo `OSAppTheme` . Essa propriedade pode ser examinada para detectar o tema do sistema solicitado.

> [!IMPORTANT]
> Para responder às alterações de tema no Android, você deve incluir o `ConfigChanges.UiMode` sinalizador no `Activity` atributo da sua `MainActivity` classe.

## <a name="related-links"></a>Links relacionados

- [SystemThemes (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-systemthemesdemo/)
- [Extensão de marcação AppThemeBinding](~/xamarin-forms/xaml/markup-extensions/consuming.md#appthemebinding-markup-extension)
- [Dicionários de recurso](~/xamarin-forms/xaml/resource-dictionaries.md)
- [Estilizando Xamarin.Forms aplicativos usando estilos XAML](~/xamarin-forms/user-interface/styles/xaml/index.md)
