---
title: Cores emXamarin.Forms
description: Xamarin.Formsfornece uma classe de cor de plataforma cruzada flexível. Este artigo explica a funcionalidade fornecida pela classe Color e como usá-la.
ms.prod: xamarin
ms.assetid: 22288ABF-57BE-47A9-ACC3-AC604D787C46
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/02/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: a02fe7451702367d85d322b756df4a547a009454
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2020
ms.locfileid: "84137339"
---
# <a name="colors-in-xamarinforms"></a>Cores emXamarin.Forms

[![Baixar exemplo ](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithcolors)

_O Xamarin. Forms fornece uma classe de cor de plataforma cruzada flexível._

Este artigo apresenta as várias maneiras pelas quais a [`Color`](xref:Xamarin.Forms.Color) classe pode ser usada Xamarin.Forms .

A [`Color`](xref:Xamarin.Forms.Color) classe fornece vários métodos para criar uma instância do `Color` :

- **Cores nomeadas** – uma coleção de cores nomeadas comuns, incluindo `Red` , `Green` e `Blue` .
- `FromHex`-valor da cadeia de caracteres semelhante à sintaxe usada em HTML, por exemplo, "00FF00". Alfa pode, opcionalmente, ser especificado como o primeiro par de caracteres ("CC00FF00").
- `FromHsla`-Valores de matiz, saturação e luminosidade `double` , com valor alfa opcional (0,0-1,0).
- `FromHsv`-Matiz, saturação e valor `int` ou `double` valores.
- `FromHsva`-Matiz, saturação e valor `int` ou `double` valores.
- `FromRgb`-Valores vermelho, verde e azul `int` (0-255).
- `FromRgba`-Valores vermelho, verde, azul e alfa `int` (0-255).
- `FromUint`-Defina um único `double` valor representando **ARGB**.

Veja algumas cores de exemplo, atribuídas ao `BackgroundColor` de alguns rótulos usando diferentes variações da sintaxe permitida:

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

Essas cores são mostradas em cada plataforma abaixo. Observe que a cor final – `Accent` é uma cor azul-ish para IOS e Android; esse valor é definido por Xamarin.Forms .

 [![Demonstração de cor](colors-images/colors-sml.png "Demonstração de cor")](colors-images/colors.png#lightbox "Demonstração de cor")

## <a name="colordefault"></a>Cor. padrão

Use o `Default` para definir (ou redefinir) um valor de cor de volta para o padrão de plataforma (entendendo que isso representa uma cor subjacente diferente em cada plataforma para cada propriedade).

Os desenvolvedores podem usar esse valor para definir uma `Color` propriedade, mas **não** devem consultar essa instância para seus valores RGB do componente (estão todas definidas como-1).

## <a name="colortransparent"></a>Color. Transparent

Defina a cor como limpar.

## <a name="coloraccent"></a>Cor. acento

No iOS e Android, essa instância é definida como uma cor de contraste que é visível no plano de fundo padrão, mas não é a mesma que a cor de texto padrão.

## <a name="additional-methods"></a>Métodos adicionais

[`Color`](xref:Xamarin.Forms.Color)as instâncias incluem os seguintes métodos adicionais:

- `AddLuminosity`-Retorna um `Color` modificando a luminosidade pelo Delta fornecido.
- `MultiplyAlpha`-Retorna um `Color` modificando o alfa, multiplicando-o pelo valor alfa fornecido.
- `ToHex`-Retorna uma `string` representação hexadecimal de um `Color` .
- `WithHue`-Retorna um `Color` , substituindo o matiz pelo valor fornecido.
- `WithLuminosity`-Retorna um `Color` , substituindo a luminosidade pelo valor fornecido.
- `WithSaturation`-Retorna um `Color` , substituindo a saturação pelo valor fornecido.

## <a name="implicit-conversions"></a>Conversões implícitas

A conversão implícita entre `Xamarin.Forms.Color` os `System.Drawing.Color` tipos e pode ser executada:

```csharp
Xamarin.Forms.Color xfColor = Xamarin.Forms.Color.FromRgb(0, 72, 255);
System.Drawing.Color sdColor = System.Drawing.Color.FromArgb(38, 127, 0);

// Implicity convert from a Xamarin.Forms.Color to a System.Drawing.Color
System.Drawing.Color sdColor2 = xfColor;

// Implicitly convert from a System.Drawing.Color to a Xamarin.Forms.Color
Xamarin.Forms.Color xfColor2 = sdColor;
```

## <a name="deviceruntimeplatform"></a>Device. RuntimePlatform

Esse trecho de código usa a `Device.RuntimePlatform` propriedade para definir seletivamente a cor de um `ActivityIndicator` :

```csharp
ActivityIndicator activityIndicator = new ActivityIndicator
{
    Color = Device.RuntimePlatform == Device.iOS ? Color.Black : Color.Default,
    IsRunning = true
};
```

## <a name="use-from-xaml"></a>Usar do XAML

As cores também podem ser referenciadas em XAML usando os nomes de cor definidos ou as representações hexadecimais mostradas aqui:

```xaml
<Label Text="Sea color" BackgroundColor="Aqua" />
<Label Text="RGB" BackgroundColor="#00FF00" />
<Label Text="Alpha plus RGB" BackgroundColor="#CC00FF00" />
<Label Text="Tiny RGB" BackgroundColor="#0F0" />
<Label Text="Tiny Alpha plus RGB" BackgroundColor="#C0F0" />
```

> [!NOTE]
> Ao usar a compilação XAML, os nomes de cor não diferenciam maiúsculas de minúsculas e, portanto, podem ser escritos em minúsculas. Para obter mais informações sobre a compilação de XAML, confira [Compilação de XAML](~/xamarin-forms/xaml/xamlc.md).

## <a name="related-links"></a>Links relacionados

- [ColorsSample](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithcolors)
- [Seletor vinculável (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-bindablepicker)
