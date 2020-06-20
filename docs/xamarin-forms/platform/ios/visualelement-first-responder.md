---
title: Primeiro respondente do visualelement no iOS
description: As especificações de plataforma permitem que você consuma a funcionalidade que só está disponível em uma plataforma específica, sem implementar renderizadores ou efeitos personalizados. Este artigo explica como consumir a plataforma do iOS específica que permite que um objeto do Visualelement se torne o primeiro Respondente para eventos de toque.
ms.prod: xamarin
ms.assetid: 3A77BA02-B87A-44EC-AC51-9D3130EF314C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/15/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: d8bd539c2bb0e8963afae3392b6f8e99d79af9af
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2020
ms.locfileid: "84136962"
---
# <a name="visualelement-first-responder-on-ios"></a>Primeiro respondente do visualelement no iOS

[![Baixar exemplo ](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Essa plataforma do iOS específica permite que um [`VisualElement`](xref:Xamarin.Forms.VisualElement) objeto se torne o primeiro Respondente para eventos de toque, em vez da página que contém o elemento. Ele é consumido em XAML definindo a `VisualElement.CanBecomeFirstResponder` propriedade vinculável como `true` :

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout>
        <Entry Placeholder="Enter text" />
        <Button ios:VisualElement.CanBecomeFirstResponder="True"
                Text="OK" />
    </StackLayout>
</ContentPage>
```

Como alternativa, ele pode ser consumido em C# usando a API fluente:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

Entry entry = new Entry { Placeholder = "Enter text" };
Button button = new Button { Text = "OK" };
button.On<iOS>().SetCanBecomeFirstResponder(true);
```

O `VisualElement.On<iOS>` método especifica que essa plataforma específica será executada somente no Ios. O `VisualElement.SetCanBecomeFirstResponder` método, no [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) namespace, é usado para definir o `VisualElement` para se tornar o primeiro Respondente para eventos de toque. Além disso, o `VisualElement.CanBecomeFirstResponder` método pode ser usado para retornar se o `VisualElement` é o primeiro Respondente a eventos de toque.

O resultado é que um [`VisualElement`](xref:Xamarin.Forms.VisualElement) pode se tornar o primeiro Respondente para eventos de toque, em vez da página que contém o elemento. Isso permite que cenários como aplicativos de chat não despercam um teclado quando um [`Button`](xref:Xamarin.Forms.Button) é tocado.

## <a name="related-links"></a>Links relacionados

- [PlatformSpecifics (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Criação de itens específicos à plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
