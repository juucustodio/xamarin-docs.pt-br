---
title: Cor do plano de fundo de célula no iOS
description: Especificidades da plataforma permitem que você consumir funcionalidade só está disponível em uma plataforma específica, sem implementar renderizadores personalizados ou efeitos. Este artigo explica como utilizar a plataforma específica do iOS que define a cor de plano de fundo padrão de células no iOS.
ms.prod: xamarin
ms.assetid: 2A3FDACF-5AE2-40DE-8488-6FE41733712F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
ms.openlocfilehash: 6b1e2fe534c8b7d0c3346a18d1b82d797e52dba1
ms.sourcegitcommit: b23a107b0fe3d2f814ae35b52a5855b6ce2a3513
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/20/2019
ms.locfileid: "65926773"
---
# <a name="cell-background-color-on-ios"></a>Cor do plano de fundo de célula no iOS

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/PlatformSpecifics/)

Este específicos da plataforma iOS define a cor de plano de fundo padrão do [ `Cell` ](xref:Xamarin.Forms.Cell) instâncias. Ele é consumido em XAML, definindo o `Cell.DefaultBackgroundColor` propriedade associável a uma [ `Color` ](xref:Xamarin.Forms.Color):

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

Como alternativa, ele pode ser consumido de C# usando a API fluente:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

var viewCell = new ViewCell { View = ... };
viewCell.On<iOS>().SetDefaultBackgroundColor(Color.Teal);
```

O `ListView.On<iOS>` método Especifica que este específicos da plataforma serão executado apenas no iOS. O `Cell.SetDefaultBackgroundColor` método, no [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) namespace, define a cor de plano de fundo da célula para determinado [ `Color` ](xref:Xamarin.Forms.Color). Além disso, o `Cell.DefaultBackgroundColor` método pode ser usado para recuperar a cor de plano de fundo da célula atual.

O resultado é que a cor do plano de fundo em um [ `Cell` ](xref:Xamarin.Forms.Cell) pode ser definido para um determinado [ `Color` ](xref:Xamarin.Forms.Color):

[![Captura de tela de células de cabeçalho de grupo azul-petróleo no iOS](cell-background-color-images/group-header-cell-color.png "ListView com células de cabeçalho de grupo azul-petróleo")](cell-background-color-images/group-header-cell-color-large.png#lightbox "ListView com células de cabeçalho de grupo azul-petróleo")

## <a name="related-links"></a>Links relacionados

- [PlatformSpecifics (amostra)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/PlatformSpecifics/)
- [Criação de itens específicos à plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [iOSSpecific API](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
