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
ms.openlocfilehash: 2abc255964df35fbdfeb4191911c57df9be99fd9
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/28/2020
ms.locfileid: "84128005"
---
# <a name="modal-page-presentation-style-on-ios"></a>Estilo de apresentação de página modal no iOS

[![Baixar exemplo ](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Essa plataforma específica do iOS é usada para definir o estilo de apresentação de uma página modal e, além disso, pode ser usada para exibir páginas modais que têm planos de fundo transparentes. Ele é consumido em XAML definindo a `Page.ModalPresentationStyle` propriedade vinculável como um `UIModalPresentationStyle` valor de enumeração:

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
             ios:Page.ModalPresentationStyle="OverFullScreen">
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
        On<iOS>().SetModalPresentationStyle(UIModalPresentationStyle.OverFullScreen);
        ...
    }
}
```

O `Page.On<iOS>` método especifica que essa plataforma específica será executada somente no Ios. O `Page.SetModalPresentationStyle` método, no [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) namespace, é usado para definir o estilo de apresentação modal em um [`Page`](xref:Xamarin.Forms.Page) especificando um dos seguintes valores de `UIModalPresentationStyle` enumeração:

- `FullScreen`, que define o estilo de apresentação modal para abranger toda a tela. Por padrão, as páginas modais são exibidas usando este estilo de apresentação.
- `FormSheet`, que define o estilo de apresentação modal a ser centralizado e menor que a tela.
- `Automatic`, que define o estilo de apresentação modal como o padrão escolhido pelo sistema. Para a maioria dos controladores de exibição, `UIKit` o mapeia para o `UIModalPresentationStyle.PageSheet` , mas alguns controladores de exibição do sistema podem mapeá-lo para um estilo diferente.
- `OverFullScreen`, que define o estilo de apresentação modal para cobrir a tela.
- `PageSheet`, que define o estilo de apresentação modal para cobrir o conteúdo subjacente.

Além disso, o `GetModalPresentationStyle` método pode ser usado para recuperar o valor atual da `UIModalPresentationStyle` Enumeração aplicada ao [`Page`](xref:Xamarin.Forms.Page) .

O resultado é que o estilo de apresentação modal em um [`Page`](xref:Xamarin.Forms.Page) pode ser definido:

[![](page-presentation-style-images/modal-presentation-style-small.png "Modal Presentation Styles")](page-presentation-style-images/modal-presentation-style-large.png#lightbox "Modal Presentation Styles")

> [!NOTE]
> As páginas que usam essa plataforma específica para definir o estilo de apresentação modal devem usar a navegação modal. Para obter mais informações, consulte [ Xamarin.Forms páginas modais](~/xamarin-forms/app-fundamentals/navigation/modal.md).

## <a name="related-links"></a>Links relacionados

- [PlatformSpecifics (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Criação de itens específicos à plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
