---
title: 'Xamarin.Forms Pincéis: cores sólidas'
description: A Xamarin.Forms classe SolidColorBrush pinta uma área com uma cor sólida.
ms.prod: xamarin
ms.assetid: 4225D40A-16C1-40E1-ACBE-23E321E7FDE4
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/27/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 3260f934eb3cfe147b218b65e981928a4683555e
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93372698"
---
# <a name="no-locxamarinforms-brushes-solid-colors"></a>Xamarin.Forms Pincéis: cores sólidas

![API de visualização](~/media/shared/preview.png "Esta API está atualmente em pré-lançamento")

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](/samples/xamarin/xamarin-forms-samples/userinterface-brushdemos/)

A `SolidColorBrush` classe deriva da `Brush` classe e é usada para pintar uma área com uma cor sólida. Há uma variedade de abordagens para especificar a cor de um `SolidColorBrush` . Por exemplo, você pode especificar sua cor com um [`Color`](xref:Xamarin.Forms.Color) valor ou usando um dos objetos predefinidos `SolidColorBrush` fornecidos pela `Brush` classe.

A `SolidColorBrush` classe define a `Color` propriedade, do tipo [`Color`](xref:Xamarin.Forms.Color) , que representa a cor do pincel. Essa propriedade é apoiada por um [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objeto, o que significa que ele pode ser o destino de associações de dados e com estilo.

A `SolidColorBrush` classe também tem um `IsEmpty` método que retorna um `bool` que representa se o pincel foi atribuído a uma cor.

## <a name="create-a-solidcolorbrush"></a>Criar um SolidColorBrush

Há três técnicas principais para criar um `SolidColorBrush` . Você pode criar um `SolidColorBrush` de um [`Color`](xref:Xamarin.Forms.Color) , usar um pincel predefinido ou criar um `SolidColorBrush` usando notação hexadecimal.

### <a name="use-a-predefined-color"></a>Usar uma cor predefinida

Xamarin.Forms inclui um conversor de tipo que cria um `SolidColorBrush` de um [`Color`](xref:Xamarin.Forms.Color) valor. Em XAML, isso permite que um `SolidColorBrush` a seja criado com base em um valor predefinido `Color` :

```xaml
<Frame Background="DarkBlue"
       BorderColor="LightGray"
       HasShadow="True"
       CornerRadius="12"
       HeightRequest="120"
       WidthRequest="120" />
```

Neste exemplo, o plano de fundo do [`Frame`](xref:Xamarin.Forms.Frame) é pintado com um azul escuro `SolidColorBrush` :

![Quadro pintado com uma cor predefinida](solidcolor-images/predefined-color.png)

Como alternativa, o [`Color`](xref:Xamarin.Forms.Color) valor pode ser especificado usando a sintaxe da marca de propriedade:

```xaml
<Frame BorderColor="LightGray"
       HasShadow="True"
       CornerRadius="12"
       HeightRequest="120"
       WidthRequest="120">
       <Frame.Background>
           <SolidColorBrush Color="DarkBlue" />
       </Frame.Background>
</Frame>
```

Neste exemplo, o plano de fundo do [`Frame`](xref:Xamarin.Forms.Frame) é pintado com um `SolidColorBrush` cuja cor é especificada definindo a `SolidColorBrush.Color` propriedade.

### <a name="use-a-predefined-brush"></a>Usar um pincel predefinido

A `Brush` classe define um conjunto de objetos comumente usados `SolidColorBrush` . O exemplo a seguir usa um desses objetos predefinidos `SolidColorBrush` :

```xaml
<Frame Background="{x:Static Brush.Indigo}"
       BorderColor="LightGray"
       HasShadow="True"
       CornerRadius="12"
       HeightRequest="120"
       WidthRequest="120" />       
```

Este é o código C# equivalente:

```csharp
Frame frame = new Frame
{
    Background = Brush.Indigo,
    BorderColor = Color.LightGray,
    // ...
};
```

Neste exemplo, o plano de fundo do [`Frame`](xref:Xamarin.Forms.Frame) é pintado com uma Indigo `SolidColorBrush` :

![Quadro pintado com um SolidColorBrush predefinido](solidcolor-images/predefined-brush.png)

Para obter uma lista de objetos predefinidos `SolidColorBrush` fornecidos pela `Brush` classe, consulte [pincéis de cores sólidas](#solid-color-brushes).

### <a name="use-hexadecimal-notation"></a>Usar notação hexadecimal

`SolidColorBrush` os objetos também podem ser criados usando a notação hexadecimal. Com essa abordagem, uma cor é especificada em termos da quantidade de vermelho, verde e azul para combinar em uma única cor. O formato principal para especificar uma cor usando notação hexadecimal é `#rrggbb` , em que:

- `rr` é um número hexadecimal de dois dígitos que especifica a quantidade relativa de vermelho.
- `gg` é um número hexadecimal de dois dígitos que especifica a quantidade relativa de verde.
- `bb` é um número hexadecimal de dois dígitos que especifica a quantidade relativa de azul.

Além disso, uma cor pode ser especificada como `#aarrggbb` onde `aa` especifica o valor alfa, ou transparência, da cor. Essa abordagem permite que você crie cores parcialmente transparentes.

O exemplo a seguir define o valor de cor de um `SolidColorBrush` usando notação hexadecimal:

```xaml
<Frame Background="#FF9988"
       BorderColor="LightGray"
       HasShadow="True"
       CornerRadius="12"
       HeightRequest="120"
       WidthRequest="120" />
```

Neste exemplo, o plano de fundo do [`Frame`](xref:Xamarin.Forms.Frame) é pintado com uma cor de salmão `SolidColorBrush` :

![Quadro pintado com um SolidColorBrush criado usando notação hexadecimal](solidcolor-images/hex.png)

Para obter outras maneiras de descrever a cor, consulte [cores em Xamarin.Forms ](~/xamarin-forms/user-interface/colors.md).

## <a name="solid-color-brushes"></a>Pincéis de cor sólida

Para sua conveniência, a `Brush` classe fornece um conjunto de objetos comumente usados `SolidColorBrush` , como `AliceBlue` e `YellowGreen` . A imagem a seguir mostra a cor de cada pincel predefinido, seu nome e seu valor hexadecimal:

[![Tabela de cores, incluindo uma amostra de cor, um nome de cor e um valor hexadecimal](solidcolor-images/solidcolorbrushes.png)](solidcolor-images/solidcolorbrushes-large.png#lightbox)

## <a name="related-links"></a>Links relacionados

- [BrushesDemos (exemplo)](/samples/xamarin/xamarin-forms-samples/userinterface-brushdemos/)
- [Cores em Xamarin.Forms](~/xamarin-forms/user-interface/colors.md)