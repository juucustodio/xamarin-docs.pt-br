---
title: Seleção de item DatePicker no iOS
description: Especificidades da plataforma permitem que você consumir funcionalidade só está disponível em uma plataforma específica, sem implementar renderizadores personalizados ou efeitos. Este artigo explica como consumir a plataforma do iOS específica que controla quando a seleção de itens ocorre em um DatePicker.
ms.prod: xamarin
ms.assetid: 847E69D1-6AE0-4E82-B9C8-919E009C2014
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/15/2020
ms.openlocfilehash: df84cf01909cec564edc9c6c8bb55382a2b9dfe3
ms.sourcegitcommit: 10b4d7952d78f20f753372c53af6feb16918555c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/26/2020
ms.locfileid: "78292117"
---
# <a name="datepicker-item-selection-on-ios"></a>Seleção de item DatePicker no iOS

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Esses controles específicos da plataforma iOS quando a seleção de item ocorre em um [`DatePicker`](xref:Xamarin.Forms.DatePicker), permitindo que o usuário especifique a seleção de item que ocorre ao procurar itens no controle ou apenas uma vez que o botão **Done** é pressionado. Ele é consumido em XAML definindo a propriedade `DatePicker.UpdateMode` anexada como um valor da enumeração `UpdateMode`:

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

Como alternativa, ele pode ser consumido de c# usando a API fluente:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

datePicker.On<iOS>().SetUpdateMode(UpdateMode.WhenFinished);
```

O método `DatePicker.On<iOS>` especifica que essa plataforma específica será executada somente no iOS. O método `DatePicker.SetUpdateMode`, no namespace [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) , é usado para controlar quando ocorre a seleção de itens, com a enumeração `UpdateMode` fornecendo dois valores possíveis:

- `Immediately` – a seleção de item ocorre quando o usuário procura itens na [`DatePicker`](xref:Xamarin.Forms.DatePicker). Esse é o comportamento padrão no xamarin. Forms.
- `WhenFinished` – a seleção de item só ocorre depois que o usuário pressionou o botão **concluído** na [`DatePicker`](xref:Xamarin.Forms.DatePicker).

Além disso, o método `SetUpdateMode` pode ser usado para alternar os valores de enumeração chamando o método `UpdateMode`, que retorna a `UpdateMode`atual:

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

O resultado é que um `UpdateMode` especificado é aplicado ao [`DatePicker`](xref:Xamarin.Forms.DatePicker), que controla quando a seleção de item ocorre:

[![Captura de tela dos modos de atualização do DatePicker](datepicker-selection-images/datepicker-updatemode.png "DatePicker UpdateMode específico da plataforma")](datepicker-selection-images/datepicker-updatemode-large.png#lightbox "DatePicker UpdateMode específico da plataforma")

## <a name="related-links"></a>Links relacionados

- [PlatformSpecifics (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Criação de itens específicos à plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
