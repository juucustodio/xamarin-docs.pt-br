---
title: Primeiro respondente do visualelement no iOS
description: Especificidades da plataforma permitem que você consumir funcionalidade só está disponível em uma plataforma específica, sem implementar renderizadores personalizados ou efeitos. Este artigo explica como consumir a plataforma do iOS específica que permite que um objeto do Visualelement se torne o primeiro Respondente para eventos de toque.
ms.prod: xamarin
ms.assetid: 3A77BA02-B87A-44EC-AC51-9D3130EF314C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/15/2020
ms.openlocfilehash: be6c233b63d172d2fcacb1cea7f5e9aeeb7faed1
ms.sourcegitcommit: 10b4d7952d78f20f753372c53af6feb16918555c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/26/2020
ms.locfileid: "78291814"
---
# <a name="visualelement-first-responder-on-ios"></a>Primeiro respondente do visualelement no iOS

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Essa plataforma específica do iOS permite que um objeto de [`VisualElement`](xref:Xamarin.Forms.VisualElement) se torne o primeiro Respondente para eventos de toque, em vez da página que contém o elemento. Ele é consumido em XAML definindo a propriedade vinculável `VisualElement.CanBecomeFirstResponder` como `true`:

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

Como alternativa, ele pode ser consumido de c# usando a API fluente:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

Entry entry = new Entry { Placeholder = "Enter text" };
Button button = new Button { Text = "OK" };
button.On<iOS>().SetCanBecomeFirstResponder(true);
```

O método `VisualElement.On<iOS>` especifica que essa plataforma específica será executada somente no iOS. O método `VisualElement.SetCanBecomeFirstResponder`, no namespace [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) , é usado para definir o `VisualElement` para se tornar o primeiro Respondente para eventos de toque. Além disso, o método `VisualElement.CanBecomeFirstResponder` pode ser usado para retornar se o `VisualElement` é o primeiro Respondente a eventos de toque.

O resultado é que um [`VisualElement`](xref:Xamarin.Forms.VisualElement) pode se tornar o primeiro Respondente para eventos de toque, em vez da página que contém o elemento. Isso permite que cenários como aplicativos de chat não despercam um teclado quando um [`Button`](xref:Xamarin.Forms.Button) é tocado.

## <a name="related-links"></a>Links relacionados

- [PlatformSpecifics (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Criação de itens específicos à plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
