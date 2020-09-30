---
title: Sombras suspensas de ImageButton no Android
description: As especificações de plataforma permitem que você consuma a funcionalidade que só está disponível em uma plataforma específica, sem implementar renderizadores ou efeitos personalizados. Este artigo explica como consumir a plataforma Android específica que habilita uma sombra em um ImageButton.
ms.prod: xamarin
ms.assetid: D3604D87-9F9F-4FE2-8B10-DF3B143C0734
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/10/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: c355816ec1b89801814b217cad8a23be2f453226
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91563790"
---
# <a name="imagebutton-drop-shadows-on-android"></a>Sombras suspensas de ImageButton no Android

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Esse recurso específico da plataforma Android é usado para habilitar uma sombra em um `ImageButton` . Ele é consumido em XAML definindo- `ImageButton.IsShadowEnabled` se a propriedade vinculável como `true` , juntamente com várias propriedades adicionais vinculáveis opcionais que controlam a sombra projetada:

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

Como alternativa, ele pode ser consumido em C# usando a API fluente:

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
> Uma sombra de projeção é desenhada como parte do `ImageButton` plano de fundo e o plano de fundo só será desenhado se a `BackgroundColor` propriedade estiver definida. Portanto, uma sombra não será desenhada se a `ImageButton.BackgroundColor` propriedade não estiver definida.

O `ImageButton.On<Android>` método especifica que essa plataforma específica será executada somente no Android. O `ImageButton.SetIsShadowEnabled` método, no [`Xamarin.Forms.PlatformConfiguration.AndroidSpecific`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) namespace, é usado para controlar se uma sombra de soltar está habilitada no `ImageButton` . Além disso, os seguintes métodos podem ser invocados para controlar a sombra:

- `SetShadowColor` – define a cor da sombra de projeção. A cor padrão é [`Color.Default`](xref:Xamarin.Forms.Color.Default*) .
- `SetShadowOffset` – define o deslocamento da sombra de projeção. O deslocamento muda a direção em que a sombra é convertida e é especificada como um [`Size`](xref:Xamarin.Forms.Size) valor. Os `Size` valores de estrutura são expressos em unidades independentes de dispositivo, com o primeiro valor sendo a distância à esquerda (valor negativo) ou à direita (valor positivo) e o segundo valor sendo a distância acima (valor negativo) ou abaixo (valor positivo). O valor padrão dessa propriedade é (0,0, 0,0), que resulta na conversão da sombra em todos os lados do `ImageButton` .
- `SetShadowRadius`– define o raio de desfoque usado para renderizar a sombra. O valor padrão de RADIUS é 10,0.

> [!NOTE]
> O estado de uma sombra de depósito pode ser consultado chamando os `GetIsShadowEnabled` `GetShadowColor` métodos,, `GetShadowOffset` e `GetShadowRadius` .

O resultado é que uma sombra pode ser habilitada em um `ImageButton` :

![ImageButton com sombra suspensa](imagebutton-drop-shadow-images/imagebutton-drop-shadow.png)

## <a name="related-links"></a>Links relacionados

- [PlatformSpecifics (exemplo)](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Criação de itens específicos à plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API AndroidSpecific](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [API AndroidSpecific. AppCompat](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)