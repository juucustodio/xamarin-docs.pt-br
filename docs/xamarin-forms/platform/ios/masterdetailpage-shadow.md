---
title: Sombra do MasterDetailPage no iOS
description: As especificações de plataforma permitem que você consuma a funcionalidade que só está disponível em uma plataforma específica, sem implementar renderizadores ou efeitos personalizados. Este artigo explica como consumir a plataforma do iOS específica que controla se a página de detalhes de um MasterDetailPage tem sombra aplicada a ele, ao revelar a página mestra.
ms.prod: xamarin
ms.assetid: FB907EA2-C00A-43A5-84B1-A43584C867A5
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/05/2020
ms.openlocfilehash: eaa82b5accae73e4c6e8eb8d3fbcc873fc111be9
ms.sourcegitcommit: 8d13d2262d02468c99c4e18207d50cd82275d233
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/29/2020
ms.locfileid: "82532705"
---
# <a name="masterdetailpage-shadow-on-ios"></a>Sombra do MasterDetailPage no iOS

[![Baixar exemplo](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Essa plataforma determina se a página de detalhes de um [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) tem sombra aplicada a ele, ao revelar a página mestra. Ele é consumido em XAML definindo `MasterDetailPage.ApplyShadow` a propriedade vinculável como `true`:

```xaml
<MasterDetailPage ...
                  xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
                  ios:MasterDetailPage.ApplyShadow="true">
    ...
</MasterDetailPage>
```

Como alternativa, ele pode ser consumido em C# usando a API fluente:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

public class iOSMasterDetailPageCS : MasterDetailPage
{
    public iOSMasterDetailPageCS(ICommand restore)
    {
        On<iOS>().SetApplyShadow(true);
        // ...
    }
}
```

O `MasterDetailPage.On<iOS>` método especifica que essa plataforma específica será executada somente no Ios. O `MasterDetailPage.SetApplyShadow` método, no [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) namespace, é usado para controlar se a página de detalhes de um [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) tem sombra aplicada a ele, ao revelar a página mestra. Além disso, o `GetApplyShadow` método pode ser usado para determinar se a sombra é aplicada à página de detalhes de `MasterDetailPage`um.

O resultado é que a página de detalhes de [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) um pode ter sombra aplicada a ele, ao revelar a página mestra:

[![Captura de tela de um MasterDetailPage com e sem sombra](masterdetailpage-shadow-images/shadow.png "MasterDetailPage com e sem sombra")](masterdetailpage-shadow-images/shadow-large.png#lightbox "MasterDetailPage com e sem sombra")

## <a name="related-links"></a>Links relacionados

- [PlatformSpecifics (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Criação de itens específicos à plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
