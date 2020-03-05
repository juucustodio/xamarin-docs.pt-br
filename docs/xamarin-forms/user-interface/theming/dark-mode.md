---
title: Detectar o modo escuro em aplicativos Xamarin. Forms
description: O modo escuro pode ter suporte em qualquer aplicativo Xamarin. Forms usando uma combinação de ResourceDictionaries, DynamicResources e conhecimento de plataforma.
ms.prod: xamarin
ms.assetid: D10506DD-BAA0-437F-A4AD-882D16E7B60D
ms.technology: xamarin-forms
author: davidortinau
ms.author: daortin
ms.date: 02/19/2020
ms.openlocfilehash: 7136e3240a39321b2d67ca29c16a0758cf5c4cfb
ms.sourcegitcommit: 524fc148bad17272bda83c50775771daa45bfd7e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/20/2020
ms.locfileid: "78292039"
---
# <a name="detect-dark-mode-in-xamarinforms-applications"></a>Detectar o modo escuro em aplicativos Xamarin. Forms

Os aplicativos Xamarin. Forms podem responder às alterações de tema do sistema operacional usando a mesma estratégia que permite dar suporte a [eles](theming.md). A principal diferença é em como a alteração do tema é disparada. O modo escuro se refere a um conjunto mais amplo de preferências de aparência que podem ser definidas no nível do sistema operacional e quais aplicativos devem respeitar e responder imediatamente.

Os requisitos mínimos para usar o modo escuro em seus aplicativos Xamarin. Forms são:

- iOS 13 ou superior.
- Android 9 ou superior (o Android 9 dá suporte a modos de aparência que você pode consultar).
- Android 10 ou superior (Android 10 notifica aplicativos de alterações no modo).
- UWP v 10.0.10240.0 ou superior.
- Xamarin. Forms (qualquer versão).

O processo de suporte a modos de aparência, incluindo leve e escuro, é o seguinte:

1. Defina os recursos compartilhados por todo o aplicativo em um [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary).
2. Defina um tema de dicionário de recursos para cada modo de aparência que forneça substituições específicas para cada estilo que você deseja alterar.
3. Defina o tema do modo de aparência padrão no arquivo **app. XAML** de seu aplicativo.
4. Estilizar seu aplicativo usando a extensão de marcação `DynamicResource` em que você deseja que os estilos reajam quando os modos de aparência mudam.
5. Adicione código para escutar alterações de tema do sistema operacional e carregue o tema correspondente do seu aplicativo.

As capturas de tela a seguir mostram páginas com tema, para o modo claro e escuro:

[![Captura de tela da página principal de um aplicativo com tema, no Ios e no android](theming-images/main-page-both-themes.png "Página principal do aplicativo com tema")](theming-images/main-page-both-themes-large.png#lightbox "Página principal do aplicativo com tema")
[ ![captura de tela da página de detalhes de um aplicativo com tema, no Ios e no Android](theming-images/detail-page-both-themes.png "Página de detalhes do aplicativo com tema")](theming-images/detail-page-both-themes-large.png#lightbox "Página de detalhes do aplicativo com tema")

## <a name="define-themes"></a>Definir temas

Siga o [Guia de temas](theming.md) para obter detalhes passo a passo sobre como criar temas escuros e leves.

## <a name="react-to-appearance-mode-changes"></a>Reagir às alterações no modo de aparência

O modo de aparência em um dispositivo pode ser alterado por vários motivos, dependendo de como o usuário configurou suas preferências, incluindo escolher explicitamente um modo, hora do dia ou fatores ambientais, como a baixa luz. Você precisará adicionar o código de plataforma para garantir que seu aplicativo possa reagir a essas alterações, e as seções a seguir discutirão isso mais detalhadamente.

### <a name="android"></a>Android

No arquivo **MainActivity.cs** do seu aplicativo, adicione o campo `ConfigChanges.UiMode` à propriedade `ConfigurationChanges` no atributo `Activity`, para que seu aplicativo seja notificado sobre as alterações no modo de interface do usuário:

```csharp
[Activity(
    // other settings
    ConfigurationChanges = ConfigChanges.ScreenSize | ConfigChanges.Orientation | ConfigChanges.UiMode)]
```

No mesmo arquivo **MainActivity.cs** , substitua o método `OnConfigurationChanged`:

```csharp
public override void OnConfigurationChanged(Configuration newConfig)
{
    base.OnConfigurationChanged(newConfig);

    if ((newConfig.UiMode & UiMode.NightNo) != 0)
    {
        // change to light theme
        // e.g. App.Current.Resources = new YourLightTheme();
    }
    else
    {
        // change to dark theme
        // e.g. App.Current.Resources = new YourDarkTheme();
    }
}
```

### <a name="ios"></a>iOS

No iOS, as alterações no modo de aparência são notificadas sobre o UIViewController, que no Xamarin. Forms é a `ContentPage`. Para substituir esse método, crie um renderizador personalizado em seu projeto do iOS chamado `PageRenderer.cs`:

```csharp
using System;
using UIKit;
using Xamarin.Forms;
using Xamarin.Forms.Platform.iOS;

[assembly: ExportRenderer(typeof(ContentPage), typeof(YourApp.iOS.Renderers.PageRenderer))]
namespace YourApp.iOS.Renderers
{
    public class PageRenderer : Xamarin.Forms.Platform.iOS.PageRenderer
    {
        protected override void OnElementChanged(VisualElementChangedEventArgs e)
        {
            base.OnElementChanged(e);

            if (e.OldElement != null || Element == null)
            {
                return;
            }

            try
            {
                SetAppTheme();
            }
            catch (Exception ex)
            {
                System.Diagnostics.Debug.WriteLine($"\t\t\tERROR: {ex.Message}");
            }
        }

        public override void TraitCollectionDidChange(UITraitCollection previousTraitCollection)
        {
            base.TraitCollectionDidChange(previousTraitCollection);

            if(this.TraitCollection.UserInterfaceStyle != previousTraitCollection.UserInterfaceStyle)
            {
                SetAppTheme();
            }
        }

        void SetAppTheme()
        {
            if (this.TraitCollection.UserInterfaceStyle == UIUserInterfaceStyle.Dark)
            {
                // change to dark theme
                // e.g. App.Current.Resources = new YourDarkTheme();
            }
            else
            {
                // change to light theme
                // e.g. App.Current.Resources = new YourLightTheme();
            }
        }
    }
}
```

Substituir o método `TraitCollectionDidChange` permite que você atue em uma alteração `UserInterfaceStyle`.

### <a name="uwp"></a>UWP

No UWP, adicione o seguinte código ao arquivo **MainPage.XAML.cs** do seu aplicativo:

```csharp
public sealed partial class MainPage
{

    UISettings uiSettings;

    public MainPage()
    {
        this.InitializeComponent();

        LoadApplication(new YourApp.App());

        uiSettings = new UISettings();
        uiSettings.ColorValuesChanged += ColorValuesChanged;
    }

    private void ColorValuesChanged(UISettings sender, object args)
    {
        var backgroundColor = sender.GetColorValue(UIColorType.Background);
        var isDarkMode = backgroundColor == Colors.Black;
        if(isDarkMode)
        {
            Xamarin.Essentials.MainThread.BeginInvokeOnMainThread(() =>
            {
                // change to dark theme
                // e.g. App.Current.Resources = new YourDarkTheme();
            });
        }
        else
        {
            Xamarin.Essentials.MainThread.BeginInvokeOnMainThread(() =>
            {
                // change to light theme
                // e.g. App.Current.Resources = new YourLightTheme();
            });
        }
    }
}
```

## <a name="set-dark-and-light-themes"></a>Definir temas escuros e claros

Depois de seguir o guia de [temas](theming.md) , você pode definir facilmente um novo tema para seu aplicativo no local apropriado do código de plataforma acima. Para carregar um novo tema, basta substituir o dicionário de recursos atual do aplicativo por um dicionário de recursos com tema de sua escolha:

```csharp
App.Current.Resources = new YourDarkTheme();
```

## <a name="related-links"></a>Links relacionados

- [Temas (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-theming/)
- [Dicionários de recurso](~/xamarin-forms/xaml/resource-dictionaries.md)
- [Estilos dinâmicos no Xamarin. Forms](~/xamarin-forms/user-interface/styles/xaml/dynamic.md)
- [Aplicar estilo a aplicativos do Xamarin.Forms usando os estilos XAML](~/xamarin-forms/user-interface/styles/xaml/index.md)
