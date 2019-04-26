---
title: Guia do Layout da área de segurança no iOS
description: Especificidades da plataforma permitem que você consumir funcionalidade só está disponível em uma plataforma específica, sem implementar renderizadores personalizados ou efeitos. Este artigo explica como utilizar o iOS específicos da plataforma que garante que o conteúdo da página é posicionado em uma área da tela que é segura para todos os dispositivos que usam iOS 11 e superior.
ms.prod: xamarin
ms.assetid: 2B6789C1-39B4-4C16-ADE1-3ED3378EAC63
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
ms.openlocfilehash: bc18f1e1f051e18a970464b134733f2af39681ae
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61250895"
---
# <a name="safe-area-layout-guide-on-ios"></a>Guia do Layout da área de segurança no iOS

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)

Este específicos da plataforma iOS é usado para garantir que o conteúdo da página é posicionado em uma área da tela que é segura para todos os dispositivos que usam iOS 11 e superior. Especificamente, ele ajudará a garantir que o conteúdo não é recortado pelo cantos arredondados de dispositivo, o indicador de base ou a estrutura do sensor em um iPhone X. Ele é consumido em XAML, definindo o `Page.UseSafeArea` anexado à propriedade um `boolean` valor:

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
             Title="Safe Area"
             ios:Page.UseSafeArea="true">
    <StackLayout>
        ...
    </StackLayout>
</ContentPage>
```

Como alternativa, ele pode ser consumido de C# usando a API fluente:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

On<iOS>().SetUseSafeArea(true);
```

O `Page.On<iOS>` método Especifica que este específicos da plataforma serão executado apenas no iOS. O `Page.SetUseSafeArea` método, no [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) namespace, que controla se o guia do layout da área de segurança está habilitado.

O resultado é que o conteúdo da página pode ser posicionado em uma área da tela que é segura para todos os iPhones:

[![](page-safe-area-images/safe-area-layout.png "Guia do Layout da área de segurança")](page-safe-area-images/safe-area-layout-large.png#lightbox "guia do Layout da área de segurança")

> [!NOTE]
> Área de segurança definida pela Apple é usada no xamarin. Forms para definir a [ `Page.Padding` ](xref:Xamarin.Forms.Page.Padding) propriedade e substituirá qualquer anteriores valores dessa propriedade que foram definidos.

Área de segurança pode ser personalizada, recuperando seu [ `Thickness` ](xref:Xamarin.Forms.Thickness) de valor com o `Page.SafeAreaInsets` método da [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) namespace. Em seguida, pode ser modificado como necessário e reatribuído à `Padding` propriedade no construtor da página ou [ `OnAppearing` ](xref:Xamarin.Forms.Page.OnAppearing) substituir:

```csharp
protected override void OnAppearing()
{
    base.OnAppearing();

    var safeInsets = On<iOS>().SafeAreaInsets();
    safeInsets.Left = 20;
    Padding = safeInsets;
}
```

## <a name="related-links"></a>Links relacionados

- [PlatformSpecifics (amostra)](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)
- [Criação de itens específicos à plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [iOSSpecific API](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
