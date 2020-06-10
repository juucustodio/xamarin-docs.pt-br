---
title: Xamarin.Essentials Descrição de "conversores de cor": "a classe ColorConverters no Xamarin.Essentials fornece vários métodos auxiliares e métodos de extensão para trabalhar com System. Drawing. Color."
MS. AssetID: B10428D6-89E2-4714-A39F-7E6E626391B2 autor: jamesmontemagno MS. Author: Jamont MS. Data: 01/06/2020 MS. Custom: vídeo no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="xamarinessentials-color-converters"></a>Xamarin.Essentials: Conversores de cor

A classe **ColorConverters** no Xamarin.Essentials fornece vários métodos auxiliares para System. Drawing. Color.

## <a name="get-started"></a>Introdução

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-color-converters"></a>Usando conversores de cor

Adicione uma referência a Xamarin.Essentials em sua classe:

```csharp
using Xamarin.Essentials;
```

Ao trabalhar com `System.Drawing.Color` , você pode usar os conversores internos de Xamarin.Forms para criar uma cor de HSL, Hex ou uint.

```csharp
var blueHex = ColorConverters.FromHex("#3498db");
var blueHsl = ColorConverters.FromHsl(204, 70, 53);
var blueUInt = ColorConverters.FromUInt(3447003);
```

## <a name="using-color-extensions"></a>Usando extensões de cor

Os métodos de extensão em `System.Drawing.Color` permitem aplicar propriedades diferentes:

```csharp
var blue = ColorConverters.FromHex("#3498db");

// Multiplies the current alpha by 50%
var blueWithAlpha = blue.MultiplyAlpha(.5f);
```

Há vários outros métodos de extensão incluindo:

- Getcomplemento
- MultiplyAlpha
- ToUInt
- WithAlpha
- WithHue
- WithLuminosity
- WithSaturation

## <a name="using-platform-extensions"></a>Usando as extensões de plataforma

Além disso, você pode converter System.Drawing.Color para a estrutura de cor específica da plataforma. Esses métodos só podem ser chamados do iOS, do Android e dos projetos UWP.

```csharp
var system = System.Drawing.Color.FromArgb(255, 52, 152, 219);

// Extension to convert to Android.Graphics.Color, UIKit.UIColor, or Windows.UI.Color
var platform = system.ToPlatformColor();
```

```csharp
var platform = new Android.Graphics.Color(52, 152, 219, 255);

// Back to System.Drawing.Color
var system = platform.ToSystemColor();
```

O método `ToSystemColor` se aplica a Android.Graphics.Color, UIKit.UIColor e Windows.UI.Color.

## <a name="api"></a>API

- [Código-fonte de conversores de cor](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Types/ColorConverters.shared.cs)
- [Documentação da API de conversores de cor](xref:Xamarin.Essentials.ColorConverters)
- [Código-fonte de extensões de cor](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Types/ColorConverters.shared.cs)
- [Documentação da API de extensões de cor](xref:Xamarin.Essentials.ColorExtensions)

## <a name="related-video"></a>Vídeo relacionados

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Color-Converters-XamarinEssentials-API-of-the-Week/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
