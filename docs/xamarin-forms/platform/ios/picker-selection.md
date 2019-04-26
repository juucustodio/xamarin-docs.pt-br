---
title: Seleção de Item de seletor no iOS
description: Especificidades da plataforma permitem que você consumir funcionalidade só está disponível em uma plataforma específica, sem implementar renderizadores personalizados ou efeitos. Este artigo explica como consumir do iOS específicas da plataforma que controla quando a seleção de item ocorre em um seletor.
ms.prod: xamarin
ms.assetid: 26B0604A-BD30-49FD-83A6-F0EDFBB0524B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
ms.openlocfilehash: 21c4c289a3fd30db890be6811875412ce4913cf5
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61250429"
---
# <a name="picker-item-selection-on-ios"></a>Seleção de Item de seletor no iOS

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)

Este específicos da plataforma iOS controla quando a seleção de item ocorre em um [ `Picker` ](xref:Xamarin.Forms.Picker), permitindo que o usuário especifique que a seleção de item ocorre ao navegar por itens no controle, ou apenas uma vez a **feito** botão é pressionado. Ele é consumido em XAML, definindo o `Picker.UpdateMode` propriedade anexada a um valor da `UpdateMode` enumeração:

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

Como alternativa, ele pode ser consumido de C# usando a API fluente:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

picker.On<iOS>().SetUpdateMode(UpdateMode.WhenFinished);
```

O `Picker.On<iOS>` método Especifica que este específicos da plataforma serão executado apenas no iOS. O `Picker.SetUpdateMode` método, no [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) namespace, é usado para controlar quando a seleção de item ocorre, com o `UpdateMode` enumeração fornecendo dois valores possíveis:

- `Immediately` – seleção de item ocorre conforme o usuário navega por itens na [ `Picker` ](xref:Xamarin.Forms.Picker). Esse é o comportamento padrão no xamarin. Forms.
- `WhenFinished` – seleção de item ocorrerá somente depois que o usuário pressiona o **feito** botão na [ `Picker` ](xref:Xamarin.Forms.Picker).

Além disso, o `SetUpdateMode` método pode ser usado para alternar os valores de enumeração por meio da chamada a `UpdateMode` método, que retorna atual `UpdateMode`:

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

O resultado é que a especificada `UpdateMode` é aplicada para o [ `Picker` ](xref:Xamarin.Forms.Picker), que controla quando ocorre a seleção de item:

[![](picker-selection-images/picker-updatemode.png "Específico da plataforma seletor UpdateMode")](picker-selection-images/picker-updatemode-large.png#lightbox "seletor UpdateMode específicos da plataforma")

## <a name="related-links"></a>Links relacionados

- [PlatformSpecifics (amostra)](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)
- [Criação de itens específicos à plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [iOSSpecific API](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
