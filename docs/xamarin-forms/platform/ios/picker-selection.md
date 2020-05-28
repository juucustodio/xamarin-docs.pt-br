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
ms.openlocfilehash: 75ef118b642a8c6a66205c6f7e3bc03089c6593c
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/28/2020
ms.locfileid: "84127888"
---
# <a name="picker-item-selection-on-ios"></a>Seleção de item do seletor no iOS

[![Baixar exemplo ](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Esses controles específicos da plataforma iOS quando a seleção de item ocorre em um [`Picker`](xref:Xamarin.Forms.Picker) , permitindo ao usuário especificar que a seleção de item ocorre durante a pesquisa de itens no controle ou apenas uma vez que o botão **Done** é pressionado. Ele é consumido em XAML definindo a `Picker.UpdateMode` Propriedade anexada como um valor da `UpdateMode` enumeração:

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout Margin="20">
        <Picker ... Title="Select a monkey" ios:Picker.UpdateMode="WhenFinished">
          ...
        </Picker>
        ...
    </StackLayout>
</ContentPage>
```

Como alternativa, ele pode ser consumido em C# usando a API fluente:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

picker.On<iOS>().SetUpdateMode(UpdateMode.WhenFinished);
```

O `Picker.On<iOS>` método especifica que essa plataforma específica será executada somente no Ios. O `Picker.SetUpdateMode` método, no [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) namespace, é usado para controlar quando ocorre a seleção de item, com a `UpdateMode` Enumeração fornecendo dois valores possíveis:

- `Immediately`– a seleção de item ocorre quando o usuário procura itens no [`Picker`](xref:Xamarin.Forms.Picker) . Esse é o comportamento padrão no Xamarin.Forms .
- `WhenFinished`– a seleção de itens ocorre apenas quando o usuário pressionou o botão **concluído** no [`Picker`](xref:Xamarin.Forms.Picker) .

Além disso, o `SetUpdateMode` método pode ser usado para alternar os valores de enumeração chamando o `UpdateMode` método, que retorna o atual `UpdateMode` :

```csharp
switch (picker.On<iOS>().UpdateMode())
{
    case UpdateMode.Immediately:
        picker.On<iOS>().SetUpdateMode(UpdateMode.WhenFinished);
        break;
    case UpdateMode.WhenFinished:
        picker.On<iOS>().SetUpdateMode(UpdateMode.Immediately);
        break;
}
```

O resultado é que um especificado `UpdateMode` é aplicado ao [`Picker`](xref:Xamarin.Forms.Picker) , que controla quando a seleção de item ocorre:

[![](picker-selection-images/picker-updatemode.png "Picker UpdateMode Platform-Specific")](picker-selection-images/picker-updatemode-large.png#lightbox "Picker UpdateMode Platform-Specific")

## <a name="related-links"></a>Links relacionados

- [PlatformSpecifics (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Criação de itens específicos à plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
