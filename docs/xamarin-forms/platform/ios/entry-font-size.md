---
title: Tamanho da fonte de entrada no iOS
description: As especificações de plataforma permitem que você consuma a funcionalidade que só está disponível em uma plataforma específica, sem implementar renderizadores ou efeitos personalizados. Este artigo explica como consumir a plataforma do iOS específica que dimensiona o tamanho da fonte de uma entrada.
ms.prod: xamarin
ms.assetid: E8881D4E-902B-4397-A43E-916B2885EC87
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 22e020ad585c39671d3e3cc8ec47f55c8c41c90c
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91563751"
---
# <a name="entry-font-size-on-ios"></a>Tamanho da fonte de entrada no iOS

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

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

![Ajustar o tamanho da fonte de entrada específico da plataforma](entry-font-size-images/entry-font-size.png)

## <a name="related-links"></a>Links relacionados

- [PlatformSpecifics (exemplo)](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Criação de itens específicos à plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)