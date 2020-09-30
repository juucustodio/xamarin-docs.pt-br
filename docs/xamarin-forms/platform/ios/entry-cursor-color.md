---
title: Cor do cursor de entrada no iOS
description: As especificações de plataforma permitem que você consuma a funcionalidade que só está disponível em uma plataforma específica, sem implementar renderizadores ou efeitos personalizados. Este artigo explica como consumir a plataforma específica do iOS que define a cor do cursor de uma entrada.
ms.prod: xamarin
ms.assetid: 867D70BA-53F9-4434-8094-85D71DCECC2D
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: ee5c4baae0380c854bc6f1cf0f608e065924a157
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91563764"
---
# <a name="entry-cursor-color-on-ios"></a>Cor do cursor de entrada no iOS

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Essa plataforma do iOS específica define a cor do cursor de um [`Entry`](xref:Xamarin.Forms.Entry) para uma cor especificada. Ele é consumido em XAML definindo a [`Entry.CursorColor`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Entry.CursorColorProperty) propriedade vinculável como a [`Color`](xref:Xamarin.Forms.Color) :

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout>
        <Entry ... ios:Entry.CursorColor="LimeGreen" />
    </StackLayout>
</ContentPage>
```

Como alternativa, ele pode ser consumido em C# usando a API fluente:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

var entry = new Xamarin.Forms.Entry();
entry.On<iOS>().SetCursorColor(Color.LimeGreen);
```

O `Entry.On<iOS>` método especifica que essa plataforma específica será executada somente no Ios. O [ `Entry.SetCursorColor` ] (xref: Xamarin.Forms . PlatformConfiguration. iOSSpecific. entry. SetCursorColor ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. iOS, Xamarin.Forms . Entry}, Xamarin.Forms . Color)), no [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) namespace, define a cor do cursor como um especificado [`Color`](xref:Xamarin.Forms.Color) . Além disso, o [ `Entry.GetCursorColor` ] (xref: Xamarin.Forms . PlatformConfiguration. iOSSpecific. entry. GetCursorColor ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. iOS, Xamarin.Forms . Entry})), o método pode ser usado para recuperar a cor atual do cursor.

O resultado é que a cor do cursor em um [`Entry`](xref:Xamarin.Forms.Entry) pode ser definida como um específico [`Color`](xref:Xamarin.Forms.Color) :

![Cor do cursor de entrada](entry-cursor-color-images/entry-cursorcolor.png)

## <a name="related-links"></a>Links relacionados

- [PlatformSpecifics (exemplo)](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Criação de itens específicos à plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)