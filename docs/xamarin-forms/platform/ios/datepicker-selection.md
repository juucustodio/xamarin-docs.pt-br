---
title: Seleção de item DatePicker no iOS
description: As especificações de plataforma permitem que você consuma a funcionalidade que só está disponível em uma plataforma específica, sem implementar renderizadores ou efeitos personalizados. Este artigo explica como consumir a plataforma do iOS específica que controla quando a seleção de itens ocorre em um DatePicker.
ms.prod: xamarin
ms.assetid: 847E69D1-6AE0-4E82-B9C8-919E009C2014
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/15/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 8668ef01e7fac02243934f145eb2e3f4ff4a6a8a
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93373205"
---
# <a name="datepicker-item-selection-on-ios"></a>Seleção de item DatePicker no iOS

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Esses controles específicos da plataforma iOS quando a seleção de item ocorre em um [`DatePicker`](xref:Xamarin.Forms.DatePicker) , permitindo ao usuário especificar que a seleção de item ocorre durante a pesquisa de itens no controle ou apenas uma vez que o botão **Done** é pressionado. Ele é consumido em XAML definindo a `DatePicker.UpdateMode` Propriedade anexada como um valor da `UpdateMode` enumeração:

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout>
       <DatePicker MinimumDate="01/01/2020"
                   MaximumDate="12/31/2020"
                   ios:DatePicker.UpdateMode="WhenFinished" />
       ...
    </StackLayout>
</ContentPage>
```

Como alternativa, ele pode ser consumido em C# usando a API fluente:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

datePicker.On<iOS>().SetUpdateMode(UpdateMode.WhenFinished);
```

O `DatePicker.On<iOS>` método especifica que essa plataforma específica será executada somente no Ios. O `DatePicker.SetUpdateMode` método, no [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) namespace, é usado para controlar quando ocorre a seleção de item, com a `UpdateMode` Enumeração fornecendo dois valores possíveis:

- `Immediately` – a seleção de item ocorre quando o usuário procura itens no [`DatePicker`](xref:Xamarin.Forms.DatePicker) . Esse é o comportamento padrão no Xamarin.Forms .
- `WhenFinished` – a seleção de itens ocorre apenas quando o usuário pressionou o botão **concluído** no [`DatePicker`](xref:Xamarin.Forms.DatePicker) .

Além disso, o `SetUpdateMode` método pode ser usado para alternar os valores de enumeração chamando o `UpdateMode` método, que retorna o atual `UpdateMode` :

```csharp
switch (datePicker.On<iOS>().UpdateMode())
{
    case UpdateMode.Immediately:
        datePicker.On<iOS>().SetUpdateMode(UpdateMode.WhenFinished);
        break;
    case UpdateMode.WhenFinished:
        datePicker.On<iOS>().SetUpdateMode(UpdateMode.Immediately);
        break;
}
```

O resultado é que um especificado `UpdateMode` é aplicado ao [`DatePicker`](xref:Xamarin.Forms.DatePicker) , que controla quando a seleção de item ocorre:

[![Captura de tela dos modos de atualização do DatePicker](datepicker-selection-images/datepicker-updatemode.png "DatePicker UpdateMode Platform-Specific")](datepicker-selection-images/datepicker-updatemode-large.png#lightbox "DatePicker UpdateMode Platform-Specific")

## <a name="related-links"></a>Links relacionados

- [PlatformSpecifics (exemplo)](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Criação de itens específicos à plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)