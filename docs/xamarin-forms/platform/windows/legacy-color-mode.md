---
title: Modo de cor herdado do visualelement no Windows
description: As especificações de plataforma permitem que você consuma a funcionalidade que só está disponível em uma plataforma específica, sem implementar renderizadores ou efeitos personalizados. Este artigo explica como consumir a plataforma Windows específica que desabilita o Xamarin.Forms modo de cor herdado.
ms.prod: xamarin
ms.assetid: B8759309-07C7-4DCA-A18A-C1A198A7951B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 8e7abfd91a95386fac79004c4a17c307bf7f9370
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93372230"
---
# <a name="visualelement-legacy-color-mode-on-windows"></a>Modo de cor herdado do visualelement no Windows

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Algumas das Xamarin.Forms exibições apresentam um modo de cor herdado. Nesse modo, quando a [`IsEnabled`](xref:Xamarin.Forms.VisualElement.IsEnabled) propriedade da exibição é definida como `false` , a exibição substituirá as cores definidas pelo usuário com as cores nativas padrão para o estado desabilitado. Para compatibilidade com versões anteriores, esse modo de cores herdado permanece como o comportamento padrão para exibições com suporte.

Essa Plataforma Universal do Windows específica da plataforma desabilita esse modo de cor herdado, para que as cores definidas em uma exibição pelo usuário permaneçam mesmo quando a exibição estiver desabilitada. Ele é consumido em XAML definindo a [`VisualElement.IsLegacyColorModeEnabled`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.IsLegacyColorModeEnabledProperty) Propriedade anexada como `false` :

```xaml
<ContentPage ...
             xmlns:windows="clr-namespace:Xamarin.Forms.PlatformConfiguration.WindowsSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout>
        ...
        <Editor Text="Enter text here"
                TextColor="Blue"
                BackgroundColor="Bisque"
                windows:VisualElement.IsLegacyColorModeEnabled="False" />
        ...
    </StackLayout>
</ContentPage>
```

Como alternativa, ele pode ser consumido em C# usando a API fluente:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.WindowsSpecific;
...

_legacyColorModeDisabledEditor.On<Windows>().SetIsLegacyColorModeEnabled(false);
```

O `VisualElement.On<Windows>` método especifica que essa plataforma específica será executada somente no Windows. O [ `VisualElement.SetIsLegacyColorModeEnabled` ] (xref: Xamarin.Forms . PlatformConfiguration. WindowsSpecific. Visualelement. SetIsLegacyColorModeEnabled ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. Windows, Xamarin.Forms . Visualelement}, System. Boolean)), no [`Xamarin.Forms.PlatformConfiguration.WindowsSpecific`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) namespace, é usado para controlar se o modo de cor herdado está desabilitado. Além disso, o [ `VisualElement.GetIsLegacyColorModeEnabled` ] (xref: Xamarin.Forms . PlatformConfiguration. WindowsSpecific. Visualelement. GetIsLegacyColorModeEnabled ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. Windows, Xamarin.Forms . Visualelement})), o método pode ser usado para retornar se o modo de cor herdado está desabilitado.

O resultado é que o modo de cor herdado pode ser desabilitado, de modo que as cores definidas em uma exibição pelo usuário permaneçam mesmo quando a exibição estiver desabilitada:

![Modo de cor herdado desabilitado](legacy-color-mode-images/legacy-color-mode-disabled.png)

> [!NOTE]
> Ao definir um [`VisualStateGroup`](xref:Xamarin.Forms.VisualStateGroup) em uma exibição, o modo de cor herdado é completamente ignorado. Para obter mais informações sobre os Estados visuais, consulte [o Xamarin.Forms Visual State Manager](~/xamarin-forms/user-interface/visual-state-manager.md).

## <a name="related-links"></a>Links relacionados

- [PlatformSpecifics (exemplo)](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Criação de itens específicos à plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API WindowsSpecific](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific)