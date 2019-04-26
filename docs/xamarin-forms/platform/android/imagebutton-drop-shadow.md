---
title: Sombras ImageButton no Android
description: Especificidades da plataforma permitem que você consumir funcionalidade só está disponível em uma plataforma específica, sem implementar renderizadores personalizados ou efeitos. Este artigo explica como utilizar o Android específicos da plataforma que permite uma sombra em um ImageButton.
ms.prod: xamarin
ms.assetid: D3604D87-9F9F-4FE2-8B10-DF3B143C0734
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/10/2018
ms.openlocfilehash: 56415aff251149aee01c2e2eb7e335e157180962
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61293764"
---
# <a name="imagebutton-drop-shadows-on-android"></a>Sombras ImageButton no Android

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)

Este específicos da plataforma Android é usado para habilitar uma sombra em um `ImageButton`. Ele é consumido em XAML, definindo o `ImageButton.IsShadowEnabled` para a propriedade associável `true`, junto com um número opcional associável propriedades adicionais que controlam a sombra:

```xaml
<ContentPage ...
             xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout Margin="20">
       <ImageButton ...
                    Source="XamarinLogo.png"
                    BackgroundColor="GhostWhite"
                    android:ImageButton.IsShadowEnabled="true"
                    android:ImageButton.ShadowColor="Gray"
                    android:ImageButton.ShadowRadius="12">
            <android:ImageButton.ShadowOffset>
                <Size>
                    <x:Arguments>
                        <x:Double>10</x:Double>
                        <x:Double>10</x:Double>
                    </x:Arguments>
                </Size>
            </android:ImageButton.ShadowOffset>
        </ImageButton>
        ...
    </StackLayout>
</ContentPage>
```

Como alternativa, ele pode ser consumido de C# usando a API fluente:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

var imageButton = new Xamarin.Forms.ImageButton { Source = "XamarinLogo.png", BackgroundColor = Color.GhostWhite, ... };
imageButton.On<Android>()
           .SetIsShadowEnabled(true)
           .SetShadowColor(Color.Gray)
           .SetShadowOffset(new Size(10, 10))
           .SetShadowRadius(12);
```

> [!IMPORTANT]
> Uma sombra é desenhada como parte do `ImageButton` em segundo plano e o plano de fundo é desenhada apenas se o `BackgroundColor` propriedade está definida. Portanto, uma sombra não será desenhada se o `ImageButton.BackgroundColor` propriedade não está definida.

O `ImageButton.On<Android>` método Especifica que este específicos da plataforma serão executado apenas no Android. O `ImageButton.SetIsShadowEnabled` método, no [ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) namespace, é usada para controlar se uma sombra está habilitada no `ImageButton`. Além disso, os métodos a seguir podem ser invocados para controlar a sombra:

- `SetShadowColor` – Define a cor da sombra. A cor padrão é [ `Color.Default` ](xref:Xamarin.Forms.Color.Default*).
- `SetShadowOffset` – Define o deslocamento da sombra. O deslocamento altera a direção da sombra é convertida e é especificada como uma [ `Size` ](xref:Xamarin.Forms.Size) valor. O `Size` valores de estrutura são expressos em unidades independentes de dispositivo, com o primeiro valor que está sendo a distância para a esquerda (valor negativo) ou a direita (valor positivo) e o segundo valor sendo a distância acima (valor negativo) ou abaixo (valor positivo) . O valor padrão dessa propriedade é (0,0, 0,0), que resulta na sombra que está sendo convertido em torno de cada lado do `ImageButton`.
- `SetShadowRadius`– Define o raio de desfoque usado para renderizar a sombra projetada. O valor de raio padrão é 10.0.

> [!NOTE]
> O estado de uma sombra pode ser consultado por meio da chamada a `GetIsShadowEnabled`, `GetShadowColor`, `GetShadowOffset`, e `GetShadowRadius` métodos.

O resultado é que uma sombra pode ser habilitada em um `ImageButton`:

![](imagebutton-drop-shadow-images/imagebutton-drop-shadow.png "ImageButton com sombra")

## <a name="related-links"></a>Links relacionados

- [PlatformSpecifics (amostra)](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)
- [Criação de itens específicos à plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [AndroidSpecific API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [AndroidSpecific.AppCompat API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)
