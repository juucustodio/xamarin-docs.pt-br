---
title: Cores no xamarin. Forms
description: Xamarin. Forms fornece uma classe flexível de cor de plataforma cruzada. Este artigo explica a funcionalidade fornecida pela classe cor e como usá-lo.
ms.prod: xamarin
ms.assetid: 22288ABF-57BE-47A9-ACC3-AC604D787C46
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/18/2019
ms.openlocfilehash: 2a17b037803d1ca6e54000ea7ba3f05c8ce6034f
ms.sourcegitcommit: eedc6032eb5328115cb0d99ca9c8de48be40b6fa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/07/2020
ms.locfileid: "78912976"
---
# <a name="colors-in-xamarinforms"></a>Cores no xamarin. Forms

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithcolors)

_O Xamarin. Forms fornece uma classe de cor de plataforma cruzada flexível._

Este artigo apresenta as várias maneiras como a classe [`Color`](xref:Xamarin.Forms.Color) pode ser usada no Xamarin. Forms.

A classe [`Color`](xref:Xamarin.Forms.Color) fornece vários métodos para criar uma instância de cor:

- **Cores nomeadas** – uma coleção de cores nomeadas comuns, incluindo `Red`, `Green`e `Blue`.
- **FromHex** -valor de cadeia de caracteres semelhante à sintaxe usada em HTML, por exemplo, "00FF00". Alfa pode, opcionalmente, ser especificado como o primeiro par de caracteres ("CC00FF00").
- **FromHsla** -matiz, saturação e luminosidade `double` valores, com valor alfa opcional (0,0-1,0).
- **FromRgb** -valores de `int` vermelho, verde e azul (0-255).
- **FromRgba** -valores de `int` vermelho, verde, azul e alfa (0-255).
- **FromUint** -define um único valor `double` representando **ARGB**.

Veja algumas cores de exemplo, atribuídas à `BackgroundColor` de alguns rótulos usando diferentes variações da sintaxe permitida:

```csharp
var red    = new Label { Text = "Red",   BackgroundColor = Color.Red };
var orange = new Label { Text = "Orange",BackgroundColor = Color.FromHex("FF6A00") };
var yellow = new Label { Text = "Yellow",BackgroundColor = Color.FromHsla(0.167, 1.0, 0.5, 1.0) };
var green  = new Label { Text = "Green", BackgroundColor = Color.FromRgb (38, 127, 0) };
var blue   = new Label { Text = "Blue",  BackgroundColor = Color.FromRgba(0, 38, 255, 255) };
var indigo = new Label { Text = "Indigo",BackgroundColor = Color.FromRgb (0, 72, 255) };
var violet = new Label { Text = "Violet",BackgroundColor = Color.FromHsla(0.82, 1, 0.25, 1) };

var transparent = new Label { Text = "Transparent",BackgroundColor = Color.Transparent };
var @default = new Label    { Text = "Default",    BackgroundColor = Color.Default };
var accent = new Label      { Text = "Accent",     BackgroundColor = Color.Accent };
```

Essas cores são mostrados em cada plataforma abaixo. Observe que a cor final `Accent`-é uma cor azul-ish para iOS e Android; Esse valor é definido pelo Xamarin. Forms.

 [![Demonstração de cor](colors-images/colors-sml.png "Demonstração de cor")](colors-images/colors.png#lightbox "Demonstração de cor")

## <a name="colordefault"></a>Color.Default

Use a `Default` para definir (ou redefinir) um valor de cor de volta para o padrão de plataforma (entendendo que isso representa uma cor subjacente diferente em cada plataforma para cada propriedade).

Os desenvolvedores podem usar esse valor para definir uma propriedade `Color`, mas **não** devem consultar essa instância para seus valores RGB do componente (estão todas definidas como-1).

## <a name="colortransparent"></a>Color.Transparent

Defina a cor para limpar.

## <a name="coloraccent"></a>Color.Accent

No iOS e Android desta instância é definida como uma cor contrastante que está visível na tela de fundo padrão, mas não é o mesmo que a cor do texto padrão.

## <a name="additional-methods"></a>Métodos adicionais

[`Color`](xref:Xamarin.Forms.Color) instâncias incluem os seguintes métodos adicionais:

- **Addluminosidade** -retorna uma `Color` modificando a luminosidade pelo Delta fornecido.
- **MultiplyAlpha** -retorna um `Color` modificando o alfa, multiplicando-o pelo valor alfa fornecido.
- **ToHex** -retorna uma representação de `string` hexadecimal de um `Color`.
- **WithHue** -retorna um `Color`, substituindo o matiz pelo valor fornecido.
- **WithLuminosity** -retorna uma `Color`, substituindo a luminosidade pelo valor fornecido.
- **WithSaturation** -retorna um `Color`, substituindo a saturação pelo valor fornecido.

## <a name="implicit-conversions"></a>Conversões implícitas

A conversão implícita entre os tipos de `Xamarin.Forms.Color` e `System.Drawing.Color` pode ser executada:

```csharp
Xamarin.Forms.Color xfColor = Xamarin.Forms.Color.FromRgb(0, 72, 255);
System.Drawing.Color sdColor = System.Drawing.Color.FromArgb(38, 127, 0);

// Implicity convert from a Xamarin.Forms.Color to a System.Drawing.Color
System.Drawing.Color sdColor2 = xfColor;

// Implicitly convert from a System.Drawing.Color to a Xamarin.Forms.Color
Xamarin.Forms.Color xfColor2 = sdColor;
```

## <a name="deviceruntimeplatform"></a>Device.RuntimePlatform

Esse trecho de código usa a propriedade `Device.RuntimePlatform` para definir seletivamente a cor de um `ActivityIndicator`:

```csharp
ActivityIndicator activityIndicator = new ActivityIndicator
{
    Color = Device.RuntimePlatform == Device.iOS ? Color.Black : Color.Default,
    IsRunning = true
};
```

## <a name="using-from-xaml"></a>Usando do XAML

As cores também podem ser referenciadas em XAML usando os nomes de cor definidos ou as representações hexadecimais mostradas aqui:

```xaml
<Label Text="Sea color" BackgroundColor="Aqua" />
<Label Text="RGB" BackgroundColor="#00FF00" />
<Label Text="Alpha plus RGB" BackgroundColor="#CC00FF00" />
<Label Text="Tiny RGB" BackgroundColor="#0F0" />
<Label Text="Tiny Alpha plus RGB" BackgroundColor="#C0F0" />
```

> [!NOTE]
> Ao usar a compilação de XAML, nomes de cores diferenciam maiusculas de minúsculas e, portanto, podem ser escritos em letras minúsculas. Para obter mais informações sobre a compilação de XAML, confira [Compilação de XAML](~/xamarin-forms/xaml/xamlc.md).

## <a name="related-links"></a>Links relacionados

- [ColorsSample](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithcolors)
- [Seletor vinculável (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-bindablepicker)
