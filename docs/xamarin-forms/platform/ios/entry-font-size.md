---
title: Tamanho da fonte de entrada no iOS
description: Especificidades da plataforma permitem que você consumir funcionalidade só está disponível em uma plataforma específica, sem implementar renderizadores personalizados ou efeitos. Este artigo explica como consumir a plataforma do iOS específica que dimensiona o tamanho da fonte de uma entrada.
ms.prod: xamarin
ms.assetid: E8881D4E-902B-4397-A43E-916B2885EC87
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
ms.openlocfilehash: f0f5ca8aa7959d5c305c29c96f972dccc43a65ff
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68648135"
---
# <a name="entry-font-size-on-ios"></a>Tamanho da fonte de entrada no iOS

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Essa plataforma específica do IOS é usada para dimensionar o tamanho da fonte de [`Entry`](xref:Xamarin.Forms.Entry) um para garantir que o texto inserido caiba no controle. Ele é consumido em XAML, definindo o [ `Entry.AdjustsFontSizeToFitWidth` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Entry.AdjustsFontSizeToFitWidthProperty) anexado à propriedade um `boolean` valor:

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
    <StackLayout Margin="20">
        <Entry x:Name="entry"
               Placeholder="Enter text here to see the font size change"
               FontSize="22"
               ios:Entry.AdjustsFontSizeToFitWidth="true" />
        ...
    </StackLayout>
</ContentPage>
```

Como alternativa, ele pode ser consumido de C# usando a API fluente:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

entry.On<iOS>().EnableAdjustsFontSizeToFitWidth();
```

O `Entry.On<iOS>` método Especifica que este específicos da plataforma serão executado apenas no iOS. O [ `Entry.EnableAdjustsFontSizeToFitWidth` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Entry.EnableAdjustsFontSizeToFitWidth(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.Entry})) método, no [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) namespace, é usado para dimensionar o tamanho da fonte do texto para garantir que ela se encaixa valores de [ `Entry` ](xref:Xamarin.Forms.Entry). Além disso, o [ `Entry` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Entry) classe o `Xamarin.Forms.PlatformConfiguration.iOSSpecific` namespace também tem um [ `DisableAdjustsFontSizeToFitWidth` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Entry.DisableAdjustsFontSizeToFitWidth(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.Entry})) método que desabilita essa específicos da plataforma, e um [ `SetAdjustsFontSizeToFitWidth` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Entry.SetAdjustsFontSizeToFitWidth(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.Entry},System.Boolean)) método que pode ser usado para ativar/desativar o tamanho da fonte dimensionamento chamando o [ `AdjustsFontSizeToFitWidth` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Entry.AdjustsFontSizeToFitWidth(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.Entry})) método:

```csharp
entry.On<iOS>().SetAdjustsFontSizeToFitWidth(!entry.On<iOS>().AdjustsFontSizeToFitWidth());
```

O resultado é que o tamanho da fonte de [ `Entry` ](xref:Xamarin.Forms.Entry) é dimensionado para garantir que o texto de valores se encaixa no controle:

![](entry-font-size-images/entry-font-size.png "Ajustar específicos de plataforma de tamanho de fonte de entrada")

## <a name="related-links"></a>Links relacionados

- [PlatformSpecifics (amostra)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Criação de itens específicos à plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
