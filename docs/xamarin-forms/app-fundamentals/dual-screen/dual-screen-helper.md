---
Título: " Xamarin.Forms auxiliares de plataforma de tela dupla" Descrição: "este guia explica como usar Xamarin.Forms a classe DualScreenHelper para otimizar sua experiência de aplicativo para dispositivos de tela dupla, como Surface Duo e Surface neo."
MS. Prod: xamarin MS. AssetID: 5aa184c2-5611-427d-85c7-1c56486c3e1b MS. Technology: xamarin-Forms autor: davidortinau MS. Author: daortin MS. Date: 02/08/2020 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="xamarinforms-dual-screen-platform-helpers"></a>Xamarin.Formsauxiliares de plataforma de tela dupla

![](~/media/shared/preview.png "This API is currently pre-release")

[![Baixar exemplo ](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-dualscreendemos/)

A classe `DualScreenHelper` pode ser usada para detectar se um dispositivo é compatível com o modo Picture in Picture e permite abrir uma `ContentPage` como uma janela picture in picture. Caso esteja executando em um dispositivo Neo, ele abrirá a página no WonderBar quando estiver no modo de redação.

## <a name="properties"></a>Propriedades

- `HasCompactModeSupport` é usada para verificar se a plataforma é compatível com a abertura de uma `ContentPage` no CompactMode.
- A `OpenCompactMode` abre uma `ContentPage` no CompactMode, caso seja compatível com a plataforma.

## <a name="example"></a>Exemplo

```csharp
async void OpenCompactWindowClicked(object sender, EventArgs e)
{
    if (!DualScreenHelper.HasCompactModeSupport())
    {
        await DisplayAlert("Unsupported", "This platform doesn't support this feature", "Ok");
        return;
    }

    ContentPage page = new ContentPage() { BackgroundColor = Color.Purple };

    var button = new Button()
    {
        Text = "Close this Window"
    };

    var layout = new StackLayout()
    {
        Children =
        {
            new Label(){ Text = "Welcome to your Compact Mode Window" },
            button
        },
        BackgroundColor = Color.Yellow,
        HorizontalOptions = LayoutOptions.Fill,
        VerticalOptions = LayoutOptions.Fill
    };

    page.Content = new ScrollView() { Content = layout };
    var args = await DualScreenHelper.OpenCompactMode(page);

    button.Command = new Command(async () =>
    {
        await args.CloseAsync();
    });
}
```
