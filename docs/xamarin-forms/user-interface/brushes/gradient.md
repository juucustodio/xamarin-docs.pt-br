---
title: 'Xamarin.Forms Pincéis: gradientes'
description: A Xamarin.Forms classe GradientBrush é uma classe abstrata que descreve um gradiente, composto por interrupções de gradiente.
ms.prod: xamarin
ms.assetid: 24763E56-74EC-4082-897B-E4EAACCADFEE
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/24/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 856f2f938316e1ee18b99a5df8fc7a43dc2433be
ms.sourcegitcommit: 044e8d7e2e53f366942afe5084316198925f4b03
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/06/2021
ms.locfileid: "97940402"
---
# <a name="no-locxamarinforms-brushes-gradients"></a>Xamarin.Forms Pincéis: gradientes

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](/samples/xamarin/xamarin-forms-samples/userinterface-brushdemos/)

A `GradientBrush` classe deriva da `Brush` classe e é uma classe abstrata que descreve um gradiente, que é composto de interrupções de gradiente. Um pincel de gradiente pinta uma área com várias cores que se mesclam ao longo de um eixo. As classes que derivam de `GradientBrush` descrevem diferentes maneiras de interpretar interrupções de gradiente e Xamarin.Forms fornecem os seguintes pincéis de gradiente:

- `LinearGradientBrush`, que pinta uma área com um gradiente linear. Para obter mais informações, consulte [ Xamarin.Forms pincéis: gradientes lineares](lineargradient.md).
- `RadialGradientBrush`, que pinta uma área com um gradiente radial. Para obter mais informações, consulte [ Xamarin.Forms pincéis: gradientes radiais](radialgradient.md).

A `GradientBrush` classe define a `GradientStops` propriedade, do tipo `GradientStopsCollection` , que representa as interrupções de gradiente do pincel, cada uma delas especifica uma cor e um deslocamento ao longo do eixo de gradiente do pincel. Um `GradientStopsCollection` é um `ObservableCollection` dos `GradientStop` objetos. A `GradientStops` propriedade é apoiada por um [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objeto, o que significa que ele pode ser o destino de associações de dados e com estilo.

> [!NOTE]
> A `GradientStops` propriedade é a `ContentProperty` da `GradientBrush` classe e, portanto, não precisa ser definida explicitamente a partir de XAML.

## <a name="gradient-stops"></a>Marcas de gradiente

As paradas de gradiente são os blocos de construção de um pincel de gradiente e especificam as cores no gradiente e sua localização ao longo do eixo de gradiente. As paradas de gradiente são especificadas usando `GradientStop` objetos.

A `GradientStop` classe define as seguintes propriedades:

- `Color`, do tipo [`Color`](xref:Xamarin.Forms.Color) , que representa a cor da parada do gradiente. O valor padrão dessa propriedade é `Color.Default`.
- `Offset`, do tipo `float` , que representa o local da parada do gradiente dentro do vetor de gradiente. O valor padrão dessa propriedade é 0 e os valores válidos estão no intervalo 0,0-1,0. Quanto mais próximo esse valor for 0, mais próximo será a cor do início do gradiente. Da mesma forma, quanto mais próximo esse valor for 1, mais próximo será a cor do final do gradiente.

Essas propriedades são apoiadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, o que significa que eles podem ser destinos de associações de dados e com estilo.

> [!IMPORTANT]
> O sistema de coordenadas usado por gradientes é relativo a uma caixa delimitadora para a área de saída. 0 indica 0% da caixa delimitadora e 1 indica 100% da caixa delimitadora. Portanto, (0.5, 0,5) descreve um ponto no meio da caixa delimitadora e (1, 1) descreve um ponto no canto inferior direito da caixa delimitadora.

O exemplo XAML a seguir cria uma diagonal `LinearGradientBrush` com quatro cores:

```xaml
<LinearGradientBrush StartPoint="0,0"
                     EndPoint="1,1">
    <GradientStop Color="Yellow"
                  Offset="0.0" />
    <GradientStop Color="Red"
                  Offset="0.25" />
    <GradientStop Color="Blue"
                  Offset="0.75" />             
    <GradientStop Color="LimeGreen"
                  Offset="1.0" />
</LinearGradientBrush>                                                       
```

A cor de cada ponto entre as interrupções de gradiente é interpolada como uma combinação da cor especificada pelas duas interrupções de gradiente delimitador. O diagrama a seguir mostra as interrupções de gradiente do exemplo anterior:

![Quadro pintado com um LinearGradientBrush diagonal](gradient-images/gradient-stops.png)

Neste diagrama, os círculos marcam a posição das interrupções de gradiente e a linha tracejada mostra o eixo gradiente. A primeira parada de gradiente especifica a cor amarela em um deslocamento de 0,0. A segunda parada de gradiente especifica a cor vermelha em um deslocamento de 0,25. Os pontos entre esses dois gradientes param gradualmente de amarelo para vermelho à medida que você passa da esquerda para a direita ao longo do eixo de gradiente. A terceira parada de gradiente especifica a cor azul em um deslocamento de 0,75. Os pontos entre a segunda e a terceira marca de gradiente mudam gradualmente de vermelho para azul. A quarta parada de gradiente especifica a cor verde-limão no deslocamento de 1,0. Os pontos entre a terceira e a quarta marca de gradiente mudam gradualmente de azul para verde-limão.

## <a name="related-links"></a>Links relacionados

- [BrushesDemos (exemplo)](/samples/xamarin/xamarin-forms-samples/userinterface-brushdemos/)
- [Xamarin.Forms Pincéis: gradientes lineares](lineargradient.md)
- [Xamarin.Forms Pincéis: gradientes radiais](radialgradient.md)
