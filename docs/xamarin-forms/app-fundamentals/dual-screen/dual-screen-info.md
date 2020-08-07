---
title: Xamarin.Formsrecursos de dispositivo de tela dupla
description: Este guia explica como usar a Xamarin.Forms classe DualScreenInfo para otimizar sua experiência de aplicativo para dispositivos de tela dupla, como Surface Duo e Surface neo.
ms.prod: xamarin
ms.assetid: dd5eb074-f4cb-4ab4-b47d-76f862ac7cfa
ms.technology: xamarin-forms
author: davidortinau
ms.author: daortin
ms.date: 05/19/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: e1f0ee6b3c509fcdb15653789866a9ec2dc88791
ms.sourcegitcommit: 08290d004d1a7e7ac579bf1f96abf8437921dc70
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/07/2020
ms.locfileid: "87918213"
---
# <a name="no-locxamarinforms-dualscreeninfo-helper-class"></a>Xamarin.FormsClasse auxiliar DualScreenInfo

![API de pré-lançamento](~/media/shared/preview.png)

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-dualscreendemos/)

A classe `DualScreenInfo` permite determinar o painel de sua exibição, o tamanho máximo dele, a posição do dispositivo, o ângulo da dobradiça e muito mais.

## <a name="configure-dualscreeninfo"></a>Configurar o DualScreenInfo

Siga estas instruções para criar um layout de tela dupla em seu aplicativo:

1. Siga as instruções de [introdução](index.md) para adicionar o NuGet e configurar a classe do Android `MainActivity` .
1. Adicione `using Xamarin.Forms.DualScreen;` ao seu arquivo de classe.
1. Use a `DualScreenInfo.Current` classe em seu aplicativo.

## <a name="properties"></a>Propriedades

- `SpanningBounds` retorna, quando estendido em duas telas, dois retângulos indicando os limites de cada área visível. Se a janela não for estendida, ela retornará uma matriz vazia.
- `HingeBounds` indica a posição da dobradiça na tela.
- `IsLandscape` indica se o dispositivo está no modo paisagem. Isso é útil porque as APIs de orientação nativas não relatam corretamente a orientação quando o aplicativo é estendido.
- `SpanMode` indica se o layout está no modo alto, largo ou de painel único.

Além disso, o `PropertyChanged` evento é acionado quando qualquer propriedade é alterada e o `HingeAngleChanged` evento é acionado quando o ângulo da dobradiça é alterado.

## <a name="poll-hinge-angle-on-android-and-uwp"></a>Ângulo da dobradiça da sondagem no Android e no UWP

O método a seguir está disponível ao acessar os `DualScreenInfo` projetos de plataforma Android e UWP:

- `GetHingeAngleAsync` recupera o ângulo atual da dobradiça do dispositivo. Ao usar o simulador, o HingeAngle pode ser configurado modificando o sensor de Pressão.

Esse método pode ser invocado de renderizadores personalizados no Android e no UWP. O código a seguir mostra um exemplo de renderizador personalizado do Android:

```csharp
public class HingeAngleLabelRenderer : Xamarin.Forms.Platform.Android.FastRenderers.LabelRenderer
{
    System.Timers.Timer _hingeTimer;
    public HingeAngleLabelRenderer(Context context) : base(context)
    {
    }

    async void OnTimerElapsed(object sender, System.Timers.ElapsedEventArgs e)
    {
        if (_hingeTimer == null)
            return;

        _hingeTimer.Stop();
        var hingeAngle = await DualScreenInfo.Current.GetHingeAngleAsync();

        Device.BeginInvokeOnMainThread(() =>
        {
            if (_hingeTimer != null)
                Element.Text = hingeAngle.ToString();
        });

        if (_hingeTimer != null)
            _hingeTimer.Start();
    }

    protected override void OnElementChanged(ElementChangedEventArgs<Label> e)
    {
        base.OnElementChanged(e);

        if (_hingeTimer == null)
        {
            _hingeTimer = new System.Timers.Timer(100);
            _hingeTimer.Elapsed += OnTimerElapsed;
            _hingeTimer.Start();
        }
    }

    protected override void Dispose(bool disposing)
    {
        if (_hingeTimer != null)
        {
            _hingeTimer.Elapsed -= OnTimerElapsed;
            _hingeTimer.Stop();
            _hingeTimer = null;
        }

        base.Dispose(disposing);
    }
}
```

## <a name="access-dualscreeninfo-in-your-application-window"></a>Acessar DualScreenInfo na janela do aplicativo

O código a seguir mostra como acessar `DualScreenInfo` na janela do aplicativo:

```csharp
DualScreenInfo currentWindow = DualScreenInfo.Current;

// Retrieve absolute position of the hinge on the screen
var hingeBounds = currentWindow.HingeBounds;

// check if app window is spanned across two screens
if(currentWindow.SpanMode == TwoPaneViewMode.SinglePane)
{
    // window is only on one screen
}
else if(currentWindow.SpanMode == TwoPaneViewMode.Tall)
{
    // window is spanned across two screens and oriented top-bottom
}
else if(currentWindow.SpanMode == TwoPaneViewMode.Wide)
{
    // window is spanned across two screens and oriented side-by-side
}

// Detect if any of the properties on DualScreenInfo change.
// This is useful to detect if the app window gets spanned
// across two screens or put on only one  
currentWindow.PropertyChanged += OnDualScreenInfoChanged;
```

## <a name="apply-dualscreeninfo-to-layouts"></a>Aplicar DualScreenInfo aos layouts

A classe `DualScreenInfo` tem um construtor que pode assumir um layout e fornecer informações sobre ele em relação às duas telas dos dispositivos:

```xaml
<Grid x:Name="grid" ColumnSpacing="0">
    <Grid.ColumnDefinitions>
        <ColumnDefinition Width="{Binding Column1Width}" />
        <ColumnDefinition Width="{Binding Column2Width}" />
        <ColumnDefinition Width="{Binding Column3Width}" />
    </Grid.ColumnDefinitions>
    <Label FontSize="Large"
           VerticalOptions="Center"
           HorizontalOptions="End"
           Text="I should be on the left side of the hinge" />
    <Label FontSize="Large"
           VerticalOptions="Center"
           HorizontalOptions="Start"
           Grid.Column="2"
           Text="I should be on the right side of the hinge" />
</Grid>
```

```csharp
public partial class GridUsingDualScreenInfo : ContentPage
{
    public DualScreenInfo DualScreenInfo { get; }
    public double Column1Width { get; set; }
    public double Column2Width { get; set; }
    public double Column3Width { get; set; }

    public GridUsingDualScreenInfo()
    {
        InitializeComponent();
        DualScreenInfo = new DualScreenInfo(grid);
        BindingContext = this;
    }

    protected override void OnAppearing()
    {
        base.OnAppearing();
        DualScreenInfo.PropertyChanged += OnInfoPropertyChanged;
        UpdateColumns();
    }

    protected override void OnDisappearing()
    {
        base.OnDisappearing();
        DualScreenInfo.PropertyChanged -= OnInfoPropertyChanged;
    }

    void UpdateColumns()
    {
        // Check if grid is on two screens
        if (DualScreenInfo.SpanningBounds.Length > 0)
        {
            // set the width of the first column to the width of the layout
            // that's on the left screen
            Column1Width = DualScreenInfo.SpanningBounds[0].Width;

            // set the middle column to the width of the hinge
            Column2Width = DualScreenInfo.HingeBounds.Width;

            // set the width of the third column to the width of the layout
            // that's on the right screen
            Column3Width = DualScreenInfo.SpanningBounds[1].Width;
        }
        else
        {
            Column1Width = 100;
            Column2Width = 0;
            Column3Width = 100;
        }

        OnPropertyChanged(nameof(Column1Width));
        OnPropertyChanged(nameof(Column2Width));
        OnPropertyChanged(nameof(Column3Width));

    }

    void OnInfoPropertyChanged(object sender, System.ComponentModel.PropertyChangedEventArgs e)
    {
        UpdateColumns();
    }
}
```

A captura de tela a seguir mostra o layout resultante:

![Grade de Posicionamento em Duas Telas](dual-screen-info-images/grid-on-two-screens.png)

## <a name="related-links"></a>Links relacionados

- [DualScreen (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-dualscreendemos/)
