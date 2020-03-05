---
title: Barra de guias translúcida TabbedPage no iOS
description: Especificidades da plataforma permitem que você consumir funcionalidade só está disponível em uma plataforma específica, sem implementar renderizadores personalizados ou efeitos. Este artigo explica como consumir a plataforma específica do iOS que define o modo Translucency da barra de guias em um TabbedPage.
ms.prod: xamarin
ms.assetid: 9581AE81-9557-47AD-8B07-25A1AC5F055B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/16/2020
ms.openlocfilehash: c321884039674d3abb1a4b510ddfe2c062c28211
ms.sourcegitcommit: 10b4d7952d78f20f753372c53af6feb16918555c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/26/2020
ms.locfileid: "78292788"
---
# <a name="tabbedpage-translucent-tab-bar-on-ios"></a>Barra de guias translúcida TabbedPage no iOS

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Essa plataforma específica do iOS é usada para definir o modo Translucency da barra de guias em uma [`TabbedPage`](xref:Xamarin.Forms.TabbedPage). Ele é consumido em XAML definindo a propriedade vinculável `TabbedPage.TranslucencyMode` como um valor de enumeração `TranslucencyMode`:

```xaml
<TabbedPage ...
            xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
            ios:TabbedPage.TranslucencyMode="Opaque">
    ...
</TabbedPage>
```

Como alternativa, ele pode ser consumido de c# usando a API fluente:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

On<iOS>().SetTranslucencyMode(TranslucencyMode.Opaque);
```

O método `TabbedPage.On<iOS>` especifica que essa plataforma específica será executada somente no iOS. O método `TabbedPage.SetTranslucencyMode`, no namespace [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) , é usado para definir o modo Translucency da barra de guias em uma [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) especificando um dos seguintes valores de enumeração `TranslucencyMode`:

- `Default`, que define a barra de guias para seu modo de Translucency padrão. Este é o valor padrão da propriedade `TabbedPage.TranslucencyMode`.
- `Transparent`, que define a barra de guias como translúcida.
- `Opaque`, que define a barra de guias como opaca.

Além disso, o método `GetTranslucencyMode` pode ser usado para recuperar o valor atual da enumeração `TranslucencyMode` que é aplicada ao [`TabbedPage`](xref:Xamarin.Forms.TabbedPage).

O resultado é que o modo Translucency da barra de guias em uma [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) pode ser definido:

![Captura de tela de barras de guias translúcidas e opacas no iOS](tabbedpage-translucent-tabbar-images/translucencymodes.png "Barras de guias translúcidas e opacas")

## <a name="related-links"></a>Links relacionados

- [PlatformSpecifics (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Criação de itens específicos à plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
