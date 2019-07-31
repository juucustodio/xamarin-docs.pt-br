---
title: Modo de cor herdado do visualelement no Android
description: Especificidades da plataforma permitem que você consumir funcionalidade só está disponível em uma plataforma específica, sem implementar renderizadores personalizados ou efeitos. Este artigo explica como consumir a plataforma Android específica que desabilita o modo de cor herdado do Xamarin. Forms.
ms.prod: xamarin
ms.assetid: 37D95A2D-74AC-488A-B903-2BDD799EAA5C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/10/2018
ms.openlocfilehash: ad7b5b7bae131a58e16c77eca73e24834a57bf3a
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68655695"
---
# <a name="visualelement-legacy-color-mode-on-android"></a>Modo de cor herdado do visualelement no Android

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Algumas das exibições de xamarin. Forms apresentam um modo de cor herdado. Nesse modo, quando o [ `IsEnabled` ](xref:Xamarin.Forms.VisualElement.IsEnabled) propriedade do modo de exibição é definida como `false`, o modo de exibição substituirá as cores definidas pelo usuário com os nativo as cores padrão para o estado desabilitado. Para versões anteriores compatibilidade, esse modo herdado cor permanece o comportamento padrão para modos de exibição com suporte.

Essa plataforma Android específica desabilita esse modo de cor herdado, para que as cores definidas em uma exibição pelo usuário permaneçam mesmo quando a exibição estiver desabilitada. Ele é consumido em XAML, definindo o [ `VisualElement.IsLegacyColorModeEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.VisualElement.IsLegacyColorModeEnabledProperty) anexado à propriedade `false`:

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

Como alternativa, ele pode ser consumido de C# usando a API fluente:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

_legacyColorModeDisabledButton.On<Android>().SetIsLegacyColorModeEnabled(false);
```

O `VisualElement.On<Android>` método Especifica que este específicos da plataforma serão executado apenas no Android. O [ `VisualElement.SetIsLegacyColorModeEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.VisualElement.SetIsLegacyColorModeEnabled(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.VisualElement},System.Boolean)) método, no [ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) namespace, é usada para controlar se o modo de cor herdados é desabilitado. Além disso, o [ `VisualElement.GetIsLegacyColorModeEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.VisualElement.GetIsLegacyColorModeEnabled(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.VisualElement})) método pode ser usado para retornar se o modo de cor herdado está desabilitado.

O resultado é que o modo herdado de cor pode ser desabilitado, para que as cores definidas em uma exibição pelo usuário permaneçam até mesmo quando o modo de exibição está desabilitado:

![](legacy-color-mode-images/legacy-color-mode-disabled.png "Modo herdado de cor desabilitado")

> [!NOTE]
> Ao definir uma [ `VisualStateGroup` ](xref:Xamarin.Forms.VisualStateGroup) em uma exibição, o modo herdado de cor é totalmente ignorado. Para obter mais informações sobre estados visuais, consulte [o Gerenciador de estado Visual xamarin. Forms](~/xamarin-forms/user-interface/visual-state-manager.md).

## <a name="related-links"></a>Links relacionados

- [PlatformSpecifics (amostra)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Criação de itens específicos à plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API AndroidSpecific](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [API AndroidSpecific. AppCompat](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)
