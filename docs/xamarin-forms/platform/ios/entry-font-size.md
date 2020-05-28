---
title: ''
description: ''
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 57498811d8789d8ef9ef775f8f39f141b77659c8
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/28/2020
ms.locfileid: "84138522"
---
# <a name="entry-font-size-on-ios"></a>Tamanho da fonte de entrada no iOS

[![Baixar exemplo ](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Essa plataforma específica do iOS é usada para dimensionar o tamanho da fonte de um [`Entry`](xref:Xamarin.Forms.Entry) para garantir que o texto inserido caiba no controle. Ele é consumido em XAML definindo a [`Entry.AdjustsFontSizeToFitWidth`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Entry.AdjustsFontSizeToFitWidthProperty) Propriedade anexada como um `boolean` valor:

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

Como alternativa, ele pode ser consumido em C# usando a API fluente:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

entry.On<iOS>().EnableAdjustsFontSizeToFitWidth();
```

O `Entry.On<iOS>` método especifica que essa plataforma específica será executada somente no Ios. O [ `Entry.EnableAdjustsFontSizeToFitWidth` ] (xref: Xamarin.Forms . PlatformConfiguration. iOSSpecific. entry. EnableAdjustsFontSizeToFitWidth ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. iOS, Xamarin.Forms . Entry})), no [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) namespace, é usado para dimensionar o tamanho da fonte do texto inserido para garantir que ele caiba no [`Entry`](xref:Xamarin.Forms.Entry) . Além disso, a [`Entry`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Entry) classe no `Xamarin.Forms.PlatformConfiguration.iOSSpecific` namespace também tem um [ `DisableAdjustsFontSizeToFitWidth` ] (xref: Xamarin.Forms . PlatformConfiguration. iOSSpecific. entry. DisableAdjustsFontSizeToFitWidth ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. iOS, Xamarin.Forms . Entry})) que desabilita esse método específico da plataforma e um [ `SetAdjustsFontSizeToFitWidth` ] (xref: Xamarin.Forms . PlatformConfiguration. iOSSpecific. entry. SetAdjustsFontSizeToFitWidth ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. iOS, Xamarin.Forms . Entry}, System. Boolean)) que pode ser usado para alternar o dimensionamento do tamanho da fonte chamando [ `AdjustsFontSizeToFitWidth` ] (xref: Xamarin.Forms . PlatformConfiguration. iOSSpecific. entry. AdjustsFontSizeToFitWidth ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. iOS, Xamarin.Forms . Entry})). método:

```csharp
entry.On<iOS>().SetAdjustsFontSizeToFitWidth(!entry.On<iOS>().AdjustsFontSizeToFitWidth());
```

O resultado é que o tamanho da fonte do [`Entry`](xref:Xamarin.Forms.Entry) é dimensionado para garantir que o texto inserido caiba no controle:

![](entry-font-size-images/entry-font-size.png "Adjust Entry Font Size Platform-Specific")

## <a name="related-links"></a>Links relacionados

- [PlatformSpecifics (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Criação de itens específicos à plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
