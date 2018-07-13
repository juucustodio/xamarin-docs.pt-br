---
title: Cores no xamarin. Forms
description: Xamarin. Forms fornece uma classe flexível de cor de plataforma cruzada. Este artigo explica a funcionalidade fornecida pela classe cor e como usá-lo.
ms.prod: xamarin
ms.assetid: 22288ABF-57BE-47A9-ACC3-AC604D787C46
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/15/2017
ms.openlocfilehash: 1017f108d6808155cac84e98a811a30d09afa134
ms.sourcegitcommit: be4da0cd7e1a915e3b8932a7e3d6bcd74c7055be
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/12/2018
ms.locfileid: "38986077"
---
# <a name="colors-in-xamarinforms"></a>Cores no xamarin. Forms

_Xamarin. Forms fornece uma classe flexível de cor de plataforma cruzada._

Este artigo apresenta as várias maneiras do `Color` classe pode ser usada no xamarin. Forms.

O `Color` classe fornece vários métodos para criar uma instância de cor

-  **Chamado Colors** -uma coleção de comuns cores nomeadas, incluindo `Red`, `Green`, e `Blue`.
-  **FromHex** -valor semelhante à sintaxe usada em HTML, por exemplo, "00FF00" cadeia de caracteres. Alfa é opcionalmente pode ser especificado como o primeiro par de caracteres ("CC00FF00").
-  **FromHsla** -matiz, saturação e luminosidade `double` valores, com valor de alfa opcional (0.0-1.0).
-  **FromRgb** -vermelho, verde e azul `int` valores (0 a 255).
-  **FromRgba** -vermelho, verde, azul e alfa `int` valores (0 a 255).
-  **FromUint** -definir uma única `double` valor que representa **argb**.

Eis aqui algumas cores de exemplo, atribuídos para o `BackgroundColor` de alguns rótulos usando diferentes variações da sintaxe permitido:

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

Essas cores são mostrados em cada plataforma abaixo. Observe a cor final - `Accent` -é uma cor blue-ish para iOS e Android; esse valor é definido pelo xamarin. Forms.

 [![Demonstração de cor](colors-images/colors-sml.png "demonstração de cor")](colors-images/colors.png#lightbox "demonstração de cor")

## <a name="colordefault"></a>Color.Default

Use o `Default` para definir um valor de cor para o padrão de plataforma (Noções básicas sobre que isso representa uma cor subjacente diferente em cada plataforma para cada propriedade) (ou defina novamente uma).

Os desenvolvedores podem usar esse valor para definir um `Color` propriedade, mas deve **não** consultar essa instância para o seu componente valores RGB (eles são todos definidos como -1).

## <a name="colortransparent"></a>Color.Transparent

Defina a cor para limpar.

## <a name="coloraccent"></a>Color.Accent

No iOS e Android desta instância é definida como uma cor contrastante que está visível na tela de fundo padrão, mas não é o mesmo que a cor do texto padrão.

## <a name="additional-methods"></a>Métodos adicionais

`Color` instâncias incluem métodos adicionais que podem ser usados para criar novas cores:

-  **AddLuminosity** -retorna uma nova cor, modificando a luminosidade pelo delta fornecido.
-  **WithHue** -retorna uma nova cor, substituindo o hue com o valor fornecido.
-  **WithLuminosity** -retorna uma nova cor, substituindo a luminosidade com o valor fornecido.
-  **WithSaturation** -retorna uma nova cor, substituindo a saturação com o valor fornecido.
-  **MultiplyAlpha** -retorna uma nova cor, modificando o alfa, multiplicá-lo pelo valor alfa fornecido.

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

Este trecho de código usa o `Device.RuntimePlatform` propriedade para definir seletivamente a cor de um `ActivityIndicator`:

```csharp
ActivityIndicator activityIndicator = new ActivityIndicator
{
    Color = Device.RuntimePlatform == Device.iOS ? Color.Black : Color.Default,
    IsRunning = true
};
```

## <a name="using-from-xaml"></a>Usando do XAML

Cores também podem ser facilmente referenciadas em XAML usando os nomes de cor definidos ou as representações hexa mostradas aqui:

```xaml
<Label Text="Sea color" BackgroundColor="Aqua" />
<Label Text="RGB" BackgroundColor="#00FF00" />
<Label Text="Alpha plus RGB" BackgroundColor="#CC00FF00" />
<Label Text="Tiny RGB" BackgroundColor="#0F0" />
<Label Text="Tiny Alpha plus RGB" BackgroundColor="#C0F0" />
```

> [!NOTE]
> Ao usar a compilação de XAML, nomes de cores diferenciam maiusculas de minúsculas e, portanto, podem ser escritos em letras minúsculas. Para obter mais informações sobre a compilação de XAML, consulte [compilação XAML](~/xamarin-forms/xaml/xamlc.md).

## <a name="summary"></a>Resumo

O xamarin. Forms `Color` classe é usada para criar referências de cores de reconhecimento de plataforma. Ele pode ser usado no código compartilhado e XAML.


## <a name="related-links"></a>Links relacionados

- [ColorsSample](https://developer.xamarin.com/samples/WorkingWithColors)
- [Seletor de associável (amostra)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/BindablePicker/)
