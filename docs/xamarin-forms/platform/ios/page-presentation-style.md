---
title: Estilo de apresentação de página modal no iOS
description: As especificações de plataforma permitem que você consuma a funcionalidade que só está disponível em uma plataforma específica, sem implementar renderizadores ou efeitos personalizados. Este artigo explica como consumir os conjuntos específicos da plataforma iOS, o estilo de apresentação de uma página modal.
ms.prod: xamarin
ms.assetid: C791F7CF-330A-44BA-987A-4CFCCBB9278B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/02/2020
ms.openlocfilehash: 5078b280499929e0e2e3691539cf1927b4c79fe7
ms.sourcegitcommit: 8d13d2262d02468c99c4e18207d50cd82275d233
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/29/2020
ms.locfileid: "82517533"
---
# <a name="modal-page-presentation-style-on-ios"></a>Estilo de apresentação de página modal no iOS

[![Baixar exemplo](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Essa plataforma específica do iOS é usada para definir o estilo de apresentação de uma página modal. Ele é consumido em XAML definindo `Page.ModalPresentationStyle` a propriedade vinculável `UIModalPresentationStyle` como um valor de enumeração:

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
             ios:Page.ModalPresentationStyle="FormSheet">
    ...
</ContentPage>
```

Como alternativa, ele pode ser consumido em C# usando a API fluente:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

public class iOSModalFormSheetPageCS : ContentPage
{
    public iOSModalFormSheetPageCS()
    {
        On<iOS>().SetModalPresentationStyle(UIModalPresentationStyle.FormSheet);
        ...
    }
}
```

O `Page.On<iOS>` método especifica que essa plataforma específica será executada somente no Ios. O `Page.SetModalPresentationStyle` método, no [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) namespace, é usado para definir o estilo de apresentação modal em um [`Page`](xref:Xamarin.Forms.Page) especificando um dos seguintes `UIModalPresentationStyle` valores de enumeração:

- `FullScreen`, que define o estilo de apresentação modal para abranger toda a tela. Por padrão, as páginas modais são exibidas usando este estilo de apresentação.
- `FormSheet`, que define o estilo de apresentação modal a ser centralizado e menor que a tela.
- `Automatic`, que define o estilo de apresentação modal como o padrão escolhido pelo sistema. Para a maioria dos controladores `UIKit` de exibição, `UIModalPresentationStyle.PageSheet`o mapeia para o, mas alguns controladores de exibição do sistema podem mapeá-lo para um estilo diferente.
- `OverFullScreen`, que define o estilo de apresentação modal para cobrir a tela.
- `PageSheet`, que define o estilo de apresentação modal para cobrir o conteúdo subjacente.

Além disso, o `GetModalPresentationStyle` método pode ser usado para recuperar o valor atual da `UIModalPresentationStyle` Enumeração aplicada ao. [`Page`](xref:Xamarin.Forms.Page)

O resultado é que o estilo de apresentação modal em [`Page`](xref:Xamarin.Forms.Page) um pode ser definido:

[![](page-presentation-style-images/modal-presentation-style-small.png "Modal Presentation Styles")](page-presentation-style-images/modal-presentation-style-large.png#lightbox "Modal Presentation Styles")

> [!NOTE]
> As páginas que usam essa plataforma específica para definir o estilo de apresentação modal devem usar a navegação modal. Para obter mais informações, consulte [páginas modais do Xamarin. Forms](~/xamarin-forms/app-fundamentals/navigation/modal.md).

## <a name="related-links"></a>Links relacionados

- [PlatformSpecifics (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Criação de itens específicos à plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
