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
ms.openlocfilehash: aaf94536d41da47aec10fc655f9d053b753da5a2
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/28/2020
ms.locfileid: "84135961"
---
# <a name="masterdetailpage-shadow-on-ios"></a>Sombra do MasterDetailPage no iOS

[![Baixar exemplo ](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Essa plataforma determina se a página de detalhes de um [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) tem sombra aplicada a ele, ao revelar a página mestra. Ele é consumido em XAML definindo a `MasterDetailPage.ApplyShadow` propriedade vinculável como `true` :

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

O `MasterDetailPage.On<iOS>` método especifica que essa plataforma específica será executada somente no Ios. O `MasterDetailPage.SetApplyShadow` método, no [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) namespace, é usado para controlar se a página de detalhes de um [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) tem sombra aplicada a ele, ao revelar a página mestra. Além disso, o `GetApplyShadow` método pode ser usado para determinar se a sombra é aplicada à página de detalhes de um `MasterDetailPage` .

O resultado é que a página de detalhes de um [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) pode ter sombra aplicada a ele, ao revelar a página mestra:

[![Captura de tela de um MasterDetailPage com e sem sombra](masterdetailpage-shadow-images/shadow.png "MasterDetailPage com e sem sombra")](masterdetailpage-shadow-images/shadow-large.png#lightbox "MasterDetailPage com e sem sombra")

## <a name="related-links"></a>Links relacionados

- [PlatformSpecifics (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Criação de itens específicos à plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
