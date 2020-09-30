---
title: Barra de guias translúcida TabbedPage no iOS
description: As especificações de plataforma permitem que você consuma a funcionalidade que só está disponível em uma plataforma específica, sem implementar renderizadores ou efeitos personalizados. Este artigo explica como consumir a plataforma específica do iOS que define o modo Translucency da barra de guias em um TabbedPage.
ms.prod: xamarin
ms.assetid: 9581AE81-9557-47AD-8B07-25A1AC5F055B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/16/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: bfd581eebba259ecf7e4e9a426f7382c8a3d1d5a
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91562542"
---
# <a name="tabbedpage-translucent-tab-bar-on-ios"></a>Barra de guias translúcida TabbedPage no iOS

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Essa plataforma específica do iOS é usada para definir o modo Translucency da barra de guias em um [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) . Ele é consumido em XAML definindo a `TabbedPage.TranslucencyMode` propriedade vinculável como um `TranslucencyMode` valor de enumeração:

```xaml
<TabbedPage ...
            xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
            ios:TabbedPage.TranslucencyMode="Opaque">
    ...
</TabbedPage>
```

Como alternativa, ele pode ser consumido em C# usando a API fluente:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

On<iOS>().SetTranslucencyMode(TranslucencyMode.Opaque);
```

O `TabbedPage.On<iOS>` método especifica que essa plataforma específica será executada somente no Ios. O `TabbedPage.SetTranslucencyMode` método, no [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) namespace, é usado para definir o modo Translucency da barra de guias em um [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) especificando um dos seguintes `TranslucencyMode` valores de enumeração:

- `Default`, que define a barra de guias para seu modo de Translucency padrão. Este é o valor padrão da propriedade `TabbedPage.TranslucencyMode`.
- `Translucent`, que define a barra de guias como translúcida.
- `Opaque`, que define a barra de guias como opaca.

Além disso, o `GetTranslucencyMode` método pode ser usado para recuperar o valor atual da `TranslucencyMode` Enumeração aplicada ao [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) .

O resultado é que o modo Translucency da barra de guias em um [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) pode ser definido:

![Captura de tela de barras de guias translúcidas e opacas no iOS](tabbedpage-translucent-tabbar-images/translucencymodes.png "Barras de guias translúcidas e opacas")

## <a name="related-links"></a>Links relacionados

- [PlatformSpecifics (exemplo)](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Criação de itens específicos à plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)