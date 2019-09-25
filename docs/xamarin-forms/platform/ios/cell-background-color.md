---
title: Cor do plano de fundo da célula no iOS
description: Especificidades da plataforma permitem que você consumir funcionalidade só está disponível em uma plataforma específica, sem implementar renderizadores personalizados ou efeitos. Este artigo explica como consumir a plataforma específica do iOS que define a cor de plano de fundo padrão de células no iOS.
ms.prod: xamarin
ms.assetid: 2A3FDACF-5AE2-40DE-8488-6FE41733712F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
ms.openlocfilehash: 24276dce97e4935ba41d7012cf6a9aa8fa2658a8
ms.sourcegitcommit: 699de58432b7da300ddc2c85842e5d9e129b0dc5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/25/2019
ms.locfileid: "68651372"
---
# <a name="cell-background-color-on-ios"></a>Cor do plano de fundo da célula no iOS

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Essa plataforma do IOS específica define a cor de plano de [`Cell`](xref:Xamarin.Forms.Cell) fundo padrão das instâncias. Ele é consumido em XAML definindo `Cell.DefaultBackgroundColor` a propriedade vinculável [`Color`](xref:Xamarin.Forms.Color)como a:

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout Margin="20">
        <ListView ItemsSource="{Binding GroupedEmployees}"
                  IsGroupingEnabled="true">
            <ListView.GroupHeaderTemplate>
                <DataTemplate>
                    <ViewCell ios:Cell.DefaultBackgroundColor="Teal">
                        <Label Margin="10,10"
                               Text="{Binding Key}"
                               FontAttributes="Bold" />
                    </ViewCell>
                </DataTemplate>
            </ListView.GroupHeaderTemplate>
            ...
        </ListView>
    </StackLayout>
</ContentPage>
```

Como alternativa, ele pode ser consumido de c# usando a API fluente:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

var viewCell = new ViewCell { View = ... };
viewCell.On<iOS>().SetDefaultBackgroundColor(Color.Teal);
```

O `ListView.On<iOS>` método Especifica que este específicos da plataforma serão executado apenas no iOS. O `Cell.SetDefaultBackgroundColor` método, [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) no namespace, define a cor do plano de fundo da célula [`Color`](xref:Xamarin.Forms.Color)como um especificado. Além disso, o `Cell.DefaultBackgroundColor` método pode ser usado para recuperar a cor de plano de fundo da célula atual.

O resultado é que a cor do plano de [`Cell`](xref:Xamarin.Forms.Cell) fundo em um pode ser definida [`Color`](xref:Xamarin.Forms.Color)como um específico:

[![Captura de tela das células do cabeçalho do grupo azul-petróleo, no Ios](cell-background-color-images/group-header-cell-color.png "ListView com células de cabeçalho de grupo azul-petróleo")](cell-background-color-images/group-header-cell-color-large.png#lightbox "ListView com células de cabeçalho de grupo azul-petróleo")

## <a name="related-links"></a>Links relacionados

- [PlatformSpecifics (amostra)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Criação de itens específicos à plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
