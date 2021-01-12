---
title: Sombra do FlyoutPage no iOS
description: As especificações de plataforma permitem que você consuma a funcionalidade que só está disponível em uma plataforma específica, sem implementar renderizadores ou efeitos personalizados. Este artigo explica como consumir a plataforma do iOS específica que controla se a página de detalhes de um FlyoutPage tem sombra aplicada a ele, ao revelar a página do submenu.
ms.prod: xamarin
ms.assetid: FB907EA2-C00A-43A5-84B1-A43584C867A5
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/05/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 9f601ed1cfd7938a2340650604b925339328f29d
ms.sourcegitcommit: 1decf2c65dc4c36513f7dd459a5df01e170a036f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/12/2021
ms.locfileid: "98115217"
---
# <a name="flyoutpage-shadow-on-ios"></a>Sombra do FlyoutPage no iOS

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Essa plataforma determina se a página de detalhes de um [`FlyoutPage`](xref:Xamarin.Forms.FlyoutPage) tem sombra aplicada a ele, ao revelar a página do submenu. Ele é consumido em XAML definindo a `FlyoutPage.ApplyShadow` propriedade vinculável como `true` :

```xaml
<FlyoutPage ...
                  xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
                  ios:FlyoutPage.ApplyShadow="true">
    ...
</FlyoutPage>
```

Como alternativa, ele pode ser consumido em C# usando a API fluente:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

public class iOSFlyoutPageCS : FlyoutPage
{
    public iOSFlyoutPageCS(ICommand restore)
    {
        On<iOS>().SetApplyShadow(true);
        // ...
    }
}
```

O `FlyoutPage.On<iOS>` método especifica que essa plataforma específica será executada somente no Ios. O `FlyoutPage.SetApplyShadow` método, no [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) namespace, é usado para controlar se a página de detalhes de um [`FlyoutPage`](xref:Xamarin.Forms.FlyoutPage) tem sombra aplicada a ele, ao revelar a página do submenu. Além disso, o `GetApplyShadow` método pode ser usado para determinar se a sombra é aplicada à página de detalhes de um `FlyoutPage` .

O resultado é que a página de detalhes de um [`FlyoutPage`](xref:Xamarin.Forms.FlyoutPage) pode ter sombra aplicada a ele, ao revelar a página do submenu:

[![Captura de tela de um FlyoutPage com e sem sombra](flyoutpage-shadow-images/shadow.png "FlyoutPage com e sem sombra")](flyoutpage-shadow-images/shadow-large.png#lightbox "FlyoutPage com e sem sombra")

## <a name="related-links"></a>Links relacionados

- [PlatformSpecifics (exemplo)](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Criação de itens específicos à plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)