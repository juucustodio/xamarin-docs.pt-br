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
ms.openlocfilehash: f8b98d103021864aeb589787224f90bdaed17146
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91563777"
---
# <a name="datepicker-item-selection-on-ios"></a>Seleção de item DatePicker no iOS

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

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

[![Captura de tela dos modos de atualização do DatePicker](datepicker-selection-images/datepicker-updatemode.png "DatePicker UpdateMode específico da plataforma")](datepicker-selection-images/datepicker-updatemode-large.png#lightbox "DatePicker UpdateMode específico da plataforma")

## <a name="related-links"></a>Links relacionados

- [PlatformSpecifics (exemplo)](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Criação de itens específicos à plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)