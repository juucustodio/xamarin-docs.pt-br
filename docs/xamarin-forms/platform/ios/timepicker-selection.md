---
title: Seleção de item do timeseparar no iOS
description: As especificações de plataforma permitem que você consuma a funcionalidade que só está disponível em uma plataforma específica, sem implementar renderizadores ou efeitos personalizados. Este artigo explica como consumir a plataforma do iOS específica que controla quando a seleção de itens ocorre em um timeseparar.
ms.prod: xamarin
ms.assetid: 554AC877-8698-4B8C-A676-80DD64305A06
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/15/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 372c268c13c50719953ac63bcc43cc8d9bff4bdd
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91563712"
---
# <a name="timepicker-item-selection-on-ios"></a>Seleção de item do timeseparar no iOS

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Esses controles específicos da plataforma iOS quando a seleção de item ocorre em um [`TimePicker`](xref:Xamarin.Forms.TimePicker) , permitindo ao usuário especificar que a seleção de item ocorre durante a pesquisa de itens no controle ou apenas uma vez que o botão **Done** é pressionado. Ele é consumido em XAML definindo a `TimePicker.UpdateMode` Propriedade anexada como um valor da `UpdateMode` enumeração:

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout>
       <TimePicker Time="14:00:00"
                   ios:TimePicker.UpdateMode="WhenFinished" />
       ...
    </StackLayout>
</ContentPage>
```

Como alternativa, ele pode ser consumido em C# usando a API fluente:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

timePicker.On<iOS>().SetUpdateMode(UpdateMode.WhenFinished);
```

O `TimePicker.On<iOS>` método especifica que essa plataforma específica será executada somente no Ios. O `TimePicker.SetUpdateMode` método, no [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) namespace, é usado para controlar quando ocorre a seleção de item, com a `UpdateMode` Enumeração fornecendo dois valores possíveis:

- `Immediately` – a seleção de item ocorre quando o usuário procura itens no [`TimePicker`](xref:Xamarin.Forms.TimePicker) . Esse é o comportamento padrão no Xamarin.Forms .
- `WhenFinished` – a seleção de itens ocorre apenas quando o usuário pressionou o botão **concluído** no [`TimePicker`](xref:Xamarin.Forms.TimePicker) .

Além disso, o `SetUpdateMode` método pode ser usado para alternar os valores de enumeração chamando o `UpdateMode` método, que retorna o atual `UpdateMode` :

```csharp
switch (timePicker.On<iOS>().UpdateMode())
{
    case UpdateMode.Immediately:
        timePicker.On<iOS>().SetUpdateMode(UpdateMode.WhenFinished);
        break;
    case UpdateMode.WhenFinished:
        timePicker.On<iOS>().SetUpdateMode(UpdateMode.Immediately);
        break;
}
```

O resultado é que um especificado `UpdateMode` é aplicado ao [`TimePicker`](xref:Xamarin.Forms.TimePicker) , que controla quando a seleção de item ocorre:

[![Captura de tela dos modos de atualização do timeseparar](timepicker-selection-images/timepicker-updatemode.png "Específico da plataforma UpdateMode")](timepicker-selection-images/timepicker-updatemode-large.png#lightbox "Específico da plataforma UpdateMode")

## <a name="related-links"></a>Links relacionados

- [PlatformSpecifics (exemplo)](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Criação de itens específicos à plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)