---
title: 'Xamarin.FormsPincéis: gradientes radiais'
description: A Xamarin.Forms classe RadialGradientBrush pinta uma área com um gradiente radial.
ms.prod: xamarin
ms.assetid: 099BA530-3B38-4005-9B19-A0EB4D4DEEFC
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/28/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: dd915384c630cc08a2bd79a52dd22ccb40835dfa
ms.sourcegitcommit: 08290d004d1a7e7ac579bf1f96abf8437921dc70
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/07/2020
ms.locfileid: "87918906"
---
# <a name="no-locxamarinforms-brushes-radial-gradients"></a>Xamarin.FormsPincéis: gradientes radiais

![API de visualização](~/media/shared/preview.png "Esta API está atualmente em pré-lançamento")

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-brushdemos/)

A `RadialGradientBrush` classe deriva da `GradientBrush` classe e pinta uma área com um gradiente radial, que combina duas ou mais cores em um círculo. `GradientStop`os objetos são usados para especificar as cores no gradiente e suas posições. Para obter mais informações sobre `GradientStop` objetos, consulte [ Xamarin.Forms pincéis: gradientes](gradient.md).

A `RadialGradientBrush` classe define as seguintes propriedades:

- `Center`, do tipo [`Point`](xref:Xamarin.Forms.Point) , que representa o ponto central do círculo para o gradiente radial. O valor padrão dessa propriedade é (0.5, 0,5).
- `Radius`, do tipo `double` , que representa o raio do círculo para o gradiente radial. O valor padrão dessa propriedade é 0,5.

Essas propriedades são apoiadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, o que significa que eles podem ser destinos de associações de dados e com estilo.

A `RadialGradientBrush` classe também tem um `IsEmpty` método que retorna um `bool` que representa se os objetos foram atribuídos a um pincel `GradientStop` .

> [!NOTE]
> Os gradientes radiais também podem ser criados com a `radial-gradient()` função CSS.

## <a name="create-a-radialgradientbrush"></a>Criar um RadialGradientBrush

As interrupções de gradiente de gradiente radial são posicionadas ao longo de um eixo de gradiente definido por um círculo. O eixo gradiente é radiado do centro do círculo para sua circunferência. A posição e o tamanho do círculo podem ser alterados usando as `Center` Propriedades e do pincel `Radius` . O círculo define o ponto final do gradiente. Portanto, uma parada de gradiente em 1,0 define a cor na circunferência do círculo. Uma parada de gradiente em 0,0 define a cor no centro do círculo.

Para criar um gradiente radial, crie um `RadialGradientBrush` objeto e defina `Center` suas `Radius` Propriedades e. Em seguida, adicione dois ou mais `GradientStop` objetos à `RadialGradientBrush.GradientStops` coleção, que especificam as cores no gradiente e suas posições.

O exemplo de XAML a seguir mostra um `RadialGradientBrush` que é definido como o `Background` de um [`Frame`](xref:Xamarin.Forms.Frame) :

```xaml    
<Frame BorderColor="LightGray"
       HasShadow="True"
       CornerRadius="12"
       HeightRequest="120"
       WidthRequest="120">
    <Frame.Background>
        <!-- Center defaults to (0.5,0.5)
             Radius defaults to (0.5) -->
        <RadialGradientBrush>
            <GradientStop Color="Red"
                          Offset="0.1" />
            <GradientStop Color="DarkBlue"
                          Offset="1.0" />
        </RadialGradientBrush>
    </Frame.Background>
</Frame>
```

Neste exemplo, o plano de fundo do [`Frame`](xref:Xamarin.Forms.Frame) é pintado com um `RadialGradientBrush` que interpola de vermelho para azul escuro. O centro do gradiente radial está posicionado no centro do `Frame` :

![Quadro pintado com um RadialGradientBrush centralizado](radialgradient-images/center.png)

O exemplo XAML a seguir move o centro do gradiente radial para o canto superior esquerdo do [`Frame`](xref:Xamarin.Forms.Frame) :

```xaml
<!-- Radius defaults to (0.5) -->
<RadialGradientBrush Center="0.0,0.0">
    <GradientStop Color="Red"
                  Offset="0.1" />
    <GradientStop Color="DarkBlue"
                  Offset="1.0" />
</RadialGradientBrush>
```

Neste exemplo, o plano de fundo do [`Frame`](xref:Xamarin.Forms.Frame) é pintado com um `RadialGradientBrush` que interpola de vermelho para azul escuro. O centro do gradiente radial está posicionado no canto superior esquerdo do `Frame` :

![Quadro pintado com um RadialGradientBrush superior esquerdo](radialgradient-images/top-left.png)

O exemplo XAML a seguir move o centro do gradiente radial para o canto inferior direito do [`Frame`](xref:Xamarin.Forms.Frame) :

```xaml
<!-- Radius defaults to (0.5) -->
<RadialGradientBrush Center="1.0,1.0">
    <GradientStop Color="Red"
                  Offset="0.1" />
    <GradientStop Color="DarkBlue"
                  Offset="1.0" />
</RadialGradientBrush>            
```

Neste exemplo, o plano de fundo do [`Frame`](xref:Xamarin.Forms.Frame) é pintado com um `RadialGradientBrush` que interpola de vermelho para azul escuro. O centro do gradiente radial está posicionado no canto inferior direito do `Frame` :

![Quadro pintado com um RadialGradientBrush inferior direito](radialgradient-images/bottom-right.png)

## <a name="related-links"></a>Links relacionados

- [BrushesDemos (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-brushdemos/)
- [Xamarin.FormsPincéis: gradientes](gradient.md)
