---
title: Detectar o modo escuro em aplicativos Xamarin.Forms
description: O modo escuro pode ser suportado em qualquer aplicativo Xamarin.Forms usando uma combinação de ResourceDictionaries, DynamicResources e conhecimento da plataforma.
ms.prod: xamarin
ms.assetid: D10506DD-BAA0-437F-A4AD-882D16E7B60D
ms.technology: xamarin-forms
author: davidortinau
ms.author: daortin
ms.date: 03/13/2020
ms.openlocfilehash: 7fe1a98e6a497a5791f26df2fc96d41781b71ef6
ms.sourcegitcommit: b93754b220fca3d6e3d131341e3cfbe233d10f84
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80628305"
---
# <a name="detect-dark-mode-in-xamarinforms-applications"></a>Detectar o modo escuro em aplicativos Xamarin.Forms

Os aplicativos Xamarin.Forms podem responder às alterações do tema do sistema operacional usando a mesma estratégia que permite que você suporte [a tema](theming.md). A principal diferença está na forma como a mudança de tema é desencadeada. O modo escuro refere-se a um conjunto mais amplo de preferências de aparência que podem ser definidas no nível do sistema operacional e quais aplicativos devem respeitar e responder imediatamente.

Os requisitos mínimos para usar o modo escuro em seus aplicativos Xamarin.Forms são:

- iOS 13 ou superior.
- Android 9 ou superior (Android 9 suporta modos de aparência que você pode consultar).
- Android 10 ou superior (Android 10 notifica apps de mudanças de modo).
- UWP v10.0.10240.0 ou superior.
- Xamarin.Forms (qualquer versão).

O processo de suporte aos modos de aparência, incluindo luz e escuridão, é o seguinte:

1. Defina os recursos compartilhados [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)por toda a sua aplicação em um .
2. Defina um tema de dicionário de recursos para cada modo de aparência que fornece substituições específicas para cada estilo que você deseja alterar.
3. Defina o tema do modo de aparência padrão no arquivo **App.xaml** do aplicativo.
4. Estilize seu `DynamicResource` aplicativo usando a extensão de marcação onde você deseja que os estilos reajam quando os modos de aparência mudam.
5. Adicione código para ouvir as alterações do tema do SISTEMA OPERACIONAL e carregue o tema correspondente do aplicativo.

As capturas de tela a seguir mostram páginas temáticas, para o modo claro e escuro:

[![Captura de tela da página principal de um aplicativo temático, no iOS e Android](theming-images/main-page-both-themes.png "Página principal do aplicativo temático")](theming-images/main-page-both-themes-large.png#lightbox "Página principal do aplicativo temático")
[![Screenshot da página de detalhes de um aplicativo temático, no iOS e Android](theming-images/detail-page-both-themes.png "Página de detalhes do aplicativo temático")](theming-images/detail-page-both-themes-large.png#lightbox "Página de detalhes do aplicativo temático")

## <a name="define-themes"></a>Definir temas

Siga o [guia temático](theming.md) para detalhes passo a passo sobre a criação de temas escuros e claros.

Você pode facilmente definir um novo tema para sua aplicação no local apropriado do código da sua plataforma. Para carregar um novo tema, basta substituir o dicionário de recursos atual do aplicativo por um dicionário de recursos temático de sua escolha:

```csharp
App.Current.Resources = new YourDarkTheme();
```

## <a name="detect-and-apply-theme"></a>Detectar e aplicar tema

A detecção do tema em execução [`RequestedTheme`](~/essentials/app-theme.md) pode ser alcançada usando o recurso do [Xamarin.Essentials](~/essentials/index.md) (versão 1.4.0 ou mais recente). Em seguida, você pode criar um método `App` auxiliar em uma nova classe ou na classe para configurar o tema:

```csharp
public partial class App : Application
{
    public static void ApplyTheme()
    {
        if (AppInfo.RequestedTheme == AppTheme.Dark)
        {
            // Change to dark theme
            // e.g. App.Current.Resources = new YourDarkTheme();
        }
        else
        {
            // Change to light theme
            // e.g. App.Current.Resources = new YourLightTheme();
        }
    }
}
```

## <a name="react-to-appearance-mode-changes"></a>Reaja às mudanças no modo de aparência

O modo de aparência em um dispositivo pode mudar por uma variedade de razões, dependendo de como o usuário configurou suas preferências, incluindo escolher explicitamente um modo, hora do dia ou fatores ambientais, como pouca luz. Você terá que adicionar código de plataforma para garantir que seu aplicativo possa reagir a essas alterações, e as seções a seguir discutem isso com mais detalhes.

### <a name="android"></a>Android

Para suportar o modo escuro em seu aplicativo, você deve atualizar `Resources/values/styles.xml`o tema `DayNight` do seu aplicativo, que pode ser encontrado em , para herdar de um tema:

```xml
<style name="MainTheme.Base" parent="Theme.AppCompat.DayNight">
```

Se você tiver atualizado para os [Componentes materiais](https://www.nuget.org/packages/Xamarin.Google.Android.Material/) do AndroidX (1.1.0-rc2) ou mais recente, você pode usar:

```xml
<style name="MainTheme.Base" parent="Theme.MaterialComponents.DayNight">
```

No arquivo **MainActivity.cs** do aplicativo, `ConfigChanges.UiMode` adicione `ConfigurationChanges` o campo `Activity` à propriedade no atributo, para que seu aplicativo seja notificado sobre alterações no modo de interface do usuário:

```csharp
[Activity(
    // other settings
    ConfigurationChanges = ConfigChanges.ScreenSize | ConfigChanges.Orientation | ConfigChanges.UiMode)]
```

No mesmo **arquivo MainActivity.cs,** `OnConfigurationChanged` anule o método:

```csharp
public override void OnConfigurationChanged(Configuration newConfig)
{
    base.OnConfigurationChanged(newConfig);
    App.ApplyTheme();
}
```

### <a name="ios"></a>iOS

No iOS, as alterações no modo de aparência são notificadas no `ContentPage`UIViewController, que no Xamarin.Forms é o . Para substituir esse método, crie um renderizador personalizado no `PageRenderer.cs`seu projeto iOS chamado :

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
                App.ApplyTheme();
            }
            catch (Exception ex)
            {
                System.Diagnostics.Debug.WriteLine($"\t\t\tERROR: {ex.Message}");
            }
        }

        public override void TraitCollectionDidChange(UITraitCollection previousTraitCollection)
        {
            base.TraitCollectionDidChange(previousTraitCollection);

            App.ApplyTheme();
        }
    }
}
```

A substituição `TraitCollectionDidChange` do método permite `UserInterfaceStyle` que você aja em uma mudança.

### <a name="uwp"></a>UWP

No UWP, adicione o seguinte código ao arquivo **MainPage.xaml.cs** do aplicativo:

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
        Xamarin.Essentials.MainThread.BeginInvokeOnMainThread(() =>
        {
            App.ApplyTheme();
        });
    }
}
```

## <a name="related-links"></a>Links relacionados

- [Tema (amostra)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-theming/)
- [Dicionários de recurso](~/xamarin-forms/xaml/resource-dictionaries.md)
- [Estilos dinâmicos em Xamarin.Forms](~/xamarin-forms/user-interface/styles/xaml/dynamic.md)
- [Aplicar estilo a aplicativos do Xamarin.Forms usando os estilos XAML](~/xamarin-forms/user-interface/styles/xaml/index.md)
