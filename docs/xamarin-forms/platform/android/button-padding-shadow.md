---
title: Preenchimento de botão e sombras no Android
description: As especificações de plataforma permitem que você consuma a funcionalidade que só está disponível em uma plataforma específica, sem implementar renderizadores ou efeitos personalizados. Este artigo explica como consumir a plataforma Android específica que usa o preenchimento padrão e os valores de sombra dos botões do Android.
ms.prod: xamarin
ms.assetid: BD2B60D1-DE6E-4691-A777-8EC5F560A4E9
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/10/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: fa0bf466886309978743f4225b72520eb340fd1a
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91563803"
---
# <a name="button-padding-and-shadows-on-android"></a>Preenchimento de botão e sombras no Android

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Essa plataforma do Android controla se Xamarin.Forms os botões usam o preenchimento padrão e os valores de sombra dos botões do Android. Ele é consumido em XAML definindo [`Button.UseDefaultPadding`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.Button.UseDefaultPaddingProperty) as [`Button.UseDefaultShadow`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.Button.UseDefaultShadowProperty) Propriedades anexadas e como `boolean` valores:

```xaml
<ContentPage ...
            xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout>
        ...
        <Button ...
                android:Button.UseDefaultPadding="true"
                android:Button.UseDefaultShadow="true" />         
    </StackLayout>
</ContentPage>
```

Como alternativa, ele pode ser consumido em C# usando a API fluente:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

button.On<Android>().SetUseDefaultPadding(true).SetUseDefaultShadow(true);
```

O `Button.On<Android>` método especifica que essa plataforma específica será executada somente no Android. O [ `Button.SetUseDefaultPadding` ] (xref: Xamarin.Forms . PlatformConfiguration. AndroidSpecific. Button. SetUseDefaultPadding ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. Android, Xamarin.Forms . Button}, System. Boolean)) e [ `Button.SetUseDefaultShadow` ] (xref: Xamarin.Forms . PlatformConfiguration. AndroidSpecific. Button. SetUseDefaultShadow ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. Android, Xamarin.Forms . Botões}, System. Boolean)), no [`Xamarin.Forms.PlatformConfiguration.AndroidSpecific`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) namespace, são usadas para controlar se Xamarin.Forms os botões usam o preenchimento padrão e os valores de sombra dos botões do Android. Além disso, o [ `Button.UseDefaultPadding` ] (xref: Xamarin.Forms . PlatformConfiguration. AndroidSpecific. Button. UseDefaultPadding ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. Android, Xamarin.Forms . Button})) e [ `Button.UseDefaultShadow` ] (xref: Xamarin.Forms . PlatformConfiguration. AndroidSpecific. Button. UseDefaultShadow ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. Android, Xamarin.Forms . Button})), os métodos podem ser usados para retornar se um botão usa o valor de preenchimento padrão e o valor de sombra padrão, respectivamente.

O resultado é que os Xamarin.Forms botões podem usar o preenchimento padrão e os valores de sombra dos botões do Android:

![Preenchimento padrão e valores de sombra em botões Android](button-padding-shadow-images/button-padding-and-shadow.png)

Observe que, na captura de tela acima [`Button`](xref:Xamarin.Forms.Button) , cada uma tem definições idênticas, exceto que o lado direito `Button` usa os valores de preenchimento e sombra padrão dos botões do Android.

## <a name="related-links"></a>Links relacionados

- [PlatformSpecifics (exemplo)](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Criação de itens específicos à plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API AndroidSpecific](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [API AndroidSpecific. AppCompat](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)