---
title: Seleção de item do timeseparar no iOS
description: Especificidades da plataforma permitem que você consumir funcionalidade só está disponível em uma plataforma específica, sem implementar renderizadores personalizados ou efeitos. Este artigo explica como consumir a plataforma do iOS específica que controla quando a seleção de itens ocorre em um timeseparar.
ms.prod: xamarin
ms.assetid: 554AC877-8698-4B8C-A676-80DD64305A06
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/15/2020
ms.openlocfilehash: 818f368da8ebb375fbacd97d3d48185ba60470d4
ms.sourcegitcommit: 10b4d7952d78f20f753372c53af6feb16918555c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/26/2020
ms.locfileid: "78291674"
---
# <a name="timepicker-item-selection-on-ios"></a>Seleção de item do timeseparar no iOS

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Esses controles específicos da plataforma iOS quando a seleção de item ocorre em um [`TimePicker`](xref:Xamarin.Forms.TimePicker), permitindo que o usuário especifique a seleção de item que ocorre ao procurar itens no controle ou apenas uma vez que o botão **Done** é pressionado. Ele é consumido em XAML definindo a propriedade `TimePicker.UpdateMode` anexada como um valor da enumeração `UpdateMode`:

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

Como alternativa, ele pode ser consumido de c# usando a API fluente:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

timePicker.On<iOS>().SetUpdateMode(UpdateMode.WhenFinished);
```

O método `TimePicker.On<iOS>` especifica que essa plataforma específica será executada somente no iOS. O método `TimePicker.SetUpdateMode`, no namespace [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) , é usado para controlar quando ocorre a seleção de itens, com a enumeração `UpdateMode` fornecendo dois valores possíveis:

- `Immediately` – a seleção de item ocorre quando o usuário procura itens na [`TimePicker`](xref:Xamarin.Forms.TimePicker). Esse é o comportamento padrão no xamarin. Forms.
- `WhenFinished` – a seleção de item só ocorre depois que o usuário pressionou o botão **concluído** na [`TimePicker`](xref:Xamarin.Forms.TimePicker).

Além disso, o método `SetUpdateMode` pode ser usado para alternar os valores de enumeração chamando o método `UpdateMode`, que retorna a `UpdateMode`atual:

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

O resultado é que um `UpdateMode` especificado é aplicado ao [`TimePicker`](xref:Xamarin.Forms.TimePicker), que controla quando a seleção de item ocorre:

[![Captura de tela dos modos de atualização do timeseparar](timepicker-selection-images/timepicker-updatemode.png "Específico da plataforma UpdateMode")](timepicker-selection-images/timepicker-updatemode-large.png#lightbox "Específico da plataforma UpdateMode")

## <a name="related-links"></a>Links relacionados

- [PlatformSpecifics (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Criação de itens específicos à plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
