---
title: Modo de cor herdado do visualelement no Android
description: As especificações de plataforma permitem que você consuma a funcionalidade que só está disponível em uma plataforma específica, sem implementar renderizadores ou efeitos personalizados. Este artigo explica como consumir a plataforma Android específica que desabilita o Xamarin.Forms modo de cor herdado.
ms.prod: xamarin
ms.assetid: 37D95A2D-74AC-488A-B903-2BDD799EAA5C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/10/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: ca8fdf101d521bf5cc58edecc523a035577be09b
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91563361"
---
# <a name="visualelement-legacy-color-mode-on-android"></a>Modo de cor herdado do visualelement no Android

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Algumas das Xamarin.Forms exibições apresentam um modo de cor herdado. Nesse modo, quando a [`IsEnabled`](xref:Xamarin.Forms.VisualElement.IsEnabled) propriedade da exibição é definida como `false` , a exibição substituirá as cores definidas pelo usuário com as cores nativas padrão para o estado desabilitado. Para compatibilidade com versões anteriores, esse modo de cores herdado permanece como o comportamento padrão para exibições com suporte.

Essa plataforma Android específica desabilita esse modo de cor herdado, para que as cores definidas em uma exibição pelo usuário permaneçam mesmo quando a exibição estiver desabilitada. Ele é consumido em XAML definindo a [`VisualElement.IsLegacyColorModeEnabled`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.VisualElement.IsLegacyColorModeEnabledProperty) Propriedade anexada como `false` :

```xaml
<ContentPage ...
             xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout>
        ...
        <Button Text="Button"
                TextColor="Blue"
                BackgroundColor="Bisque"
                android:VisualElement.IsLegacyColorModeEnabled="False" />
        ...
    </StackLayout>
</ContentPage>
```

Como alternativa, ele pode ser consumido em C# usando a API fluente:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

_legacyColorModeDisabledButton.On<Android>().SetIsLegacyColorModeEnabled(false);
```

O `VisualElement.On<Android>` método especifica que essa plataforma específica será executada somente no Android. O [ `VisualElement.SetIsLegacyColorModeEnabled` ] (xref: Xamarin.Forms . PlatformConfiguration. AndroidSpecific. Visualelement. SetIsLegacyColorModeEnabled ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. Android, Xamarin.Forms . Visualelement}, System. Boolean)), no [`Xamarin.Forms.PlatformConfiguration.AndroidSpecific`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) namespace, é usado para controlar se o modo de cor herdado está desabilitado. Além disso, o [ `VisualElement.GetIsLegacyColorModeEnabled` ] (xref: Xamarin.Forms . PlatformConfiguration. AndroidSpecific. Visualelement. GetIsLegacyColorModeEnabled ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. Android, Xamarin.Forms . Visualelement})), o método pode ser usado para retornar se o modo de cor herdado está desabilitado.

O resultado é que o modo de cor herdado pode ser desabilitado, de modo que as cores definidas em uma exibição pelo usuário permaneçam mesmo quando a exibição estiver desabilitada:

![Modo de cor herdado desabilitado](legacy-color-mode-images/legacy-color-mode-disabled.png)

> [!NOTE]
> Ao definir um [`VisualStateGroup`](xref:Xamarin.Forms.VisualStateGroup) em uma exibição, o modo de cor herdado é completamente ignorado. Para obter mais informações sobre os Estados visuais, consulte [o Xamarin.Forms Visual State Manager](~/xamarin-forms/user-interface/visual-state-manager.md).

## <a name="related-links"></a>Links relacionados

- [PlatformSpecifics (exemplo)](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Criação de itens específicos à plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API AndroidSpecific](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [API AndroidSpecific. AppCompat](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)