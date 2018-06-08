---
title: Cores
description: Xamarin. Forms fornece uma classe de cor de plataforma cruzada flexível.
ms.prod: xamarin
ms.assetid: 22288ABF-57BE-47A9-ACC3-AC604D787C46
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/15/2017
ms.openlocfilehash: 7a304790213bcebe50a3f39295b5b1d1fb052879
ms.sourcegitcommit: d80d93957040a14b4638a91b0eac797cfaade840
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/07/2018
ms.locfileid: "34848337"
---
# <a name="colors"></a>Cores

_Xamarin. Forms fornece uma classe de cor de plataforma cruzada flexível._

Este artigo apresenta as várias maneiras de `Color` classe pode ser usada em xamarin. Forms.

O `Color` classe fornece vários métodos para criar uma instância de cor

-  **Chamado cores** -uma coleção de comuns cores nomeadas, inclusive `Red`, `Green`, e `Blue`.
-  **FromHex** -valor semelhante à sintaxe usada em HTML, por exemplo, "00FF00" da cadeia de caracteres. Alfa é opcionalmente pode ser especificado como o primeiro par de caracteres ("CC00FF00").
-  **FromHsla** -matiz, saturação e luminosidade `double` valores, com um valor alfa opcional (0,0 1.0).
-  **FromRgb** -vermelho, verde e azul `int` valores (0-255).
-  **FromRgba** -verde, vermelho, azul e alfa `int` valores (0-255).
-  **FromUint** -definir uma única `double` valor que representa **argb**.

Eis algumas cores de exemplo, com o `BackgroundColor` de alguns rótulos usando diferentes variações da sintaxe permitido:

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

Essas cores são mostradas em cada plataforma abaixo. Observe a cor final - `Accent` -é uma cor blue-ish para iOS e Android; esse valor é definido por xamarin. Forms.

 [![Demonstração de cor](colors-images/colors-sml.png "demonstração de cor")](colors-images/colors.png#lightbox "demonstração de cor")

## <a name="colordefault"></a>Color.Default

Use o `Default` para definir (ou defina novamente) um valor de cor para o padrão de plataforma (Noções básicas sobre que representa uma cor subjacente diferente em cada plataforma para cada propriedade).

Os desenvolvedores podem usar esse valor para definir um `Color` propriedade mas deve **não** consultar essa instância para seu componente valores RGB (eles prontos para -1).

## <a name="colortransparent"></a>Color.Transparent

Defina a cor para limpar.

## <a name="coloraccent"></a>Color.Accent

No iOS e Android, esta instância é definida como uma cor de contraste que é visível no plano de fundo padrão, mas não é o mesmo que a cor de texto padrão.

## <a name="additional-methods"></a>Métodos adicionais

`Color` instâncias incluem métodos adicionais que podem ser usados para criar novas cores:

-  **AddLuminosity** -retorna uma nova cor modificando a luminosidade pelo delta fornecido.
-  **WithHue** -retorna uma nova cor, substituindo o matiz com o valor fornecido.
-  **WithLuminosity** -retorna uma nova cor, substituindo a luminosidade com o valor fornecido.
-  **WithSaturation** -retorna uma nova cor, substituindo a saturação com o valor fornecido.
-  **MultiplyAlpha** -retorna uma nova cor modificando alfa, multiplicá-lo pelo valor alfa fornecido.

## <a name="implicit-conversions"></a>Conversões implícitas

Conversão implícita entre o `Xamarin.Forms.Color` e `System.Drawing.Color` tipos podem ser executados:

```csharp
Xamarin.Forms.Color xfColor = Xamarin.Forms.Color.FromRgb(0, 72, 255);
System.Drawing.Color sdColor = System.Drawing.Color.FromArgb(38, 127, 0);

// Implicity convert from a Xamarin.Forms.Color to a System.Drawing.Color
System.Drawing.Color sdColor2 = xfColor;

// Implicitly convert from a System.Drawing.Color to a Xamarin.Forms.Color
Xamarin.Forms.Color xfColor2 = sdColor;
```

## <a name="deviceruntimeplatform"></a>Device.RuntimePlatform

Este trecho de código usa o `Device.RuntimePlatform` propriedade seletivamente definir a cor de um `ActivityIndicator`:

```csharp
ActivityIndicator activityIndicator = new ActivityIndicator
{
    Color = Device.RuntimePlatform == Device.iOS ? Color.Black : Color.Default,
    IsRunning = true
};
```

## <a name="using-from-xaml"></a>Uso do XAML

Também podem ser referenciadas facilmente cores em XAML usando nomes de cor definida ou as representações hexadecimais mostradas aqui:

```xaml
<Label Text="Sea color" BackgroundColor="Aqua" />
<Label Text="RGB" BackgroundColor="#00FF00" />
<Label Text="Alpha plus RGB" BackgroundColor="#CC00FF00" />
<Label Text="Tiny RGB" BackgroundColor="#0F0" />
<Label Text="Tiny Alpha plus RGB" BackgroundColor="#C0F0" />
```

## <a name="summary"></a>Resumo

O xamarin. Forms `Color` classe é usada para criar referências de cores com reconhecimento de plataforma. Ele pode ser usado no código compartilhado e XAML.


## <a name="related-links"></a>Links relacionados

- [ColorsSample](https://developer.xamarin.com/samples/WorkingWithColors)
- [Seletor de associável (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/BindablePicker/)
