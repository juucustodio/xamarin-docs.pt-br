---
title: Eventos de ciclo de vida da página no Android
description: As especificações de plataforma permitem que você consuma a funcionalidade que só está disponível em uma plataforma específica, sem implementar renderizadores ou efeitos personalizados. Este artigo explica como consumir a plataforma Android específica que desabilita os eventos de página que desaparecem e aparecem em pausa e retomada do aplicativo, respectivamente.
ms.prod: xamarin
ms.assetid: F6E3759C-D347-407A-91A2-CF9B3B7D4CBD
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/10/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 76724ff17613fcebe35cb68518a1c932eee8aad7
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2020
ms.locfileid: "84128717"
---
# <a name="page-lifecycle-events-on-android"></a>Eventos de ciclo de vida da página no Android

[![Baixar exemplo ](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Essa plataforma do Android específica é usada para desabilitar os [`Disappearing`](xref:Xamarin.Forms.Page.Appearing) [`Appearing`](xref:Xamarin.Forms.Page.Appearing) eventos de página e em pausa do aplicativo e retomar, respectivamente, para aplicativos que usam AppCompat. Além disso, ele inclui a capacidade de controlar se o teclado virtual é exibido no currículo, se ele foi exibido na pausa, desde que o modo operacional do teclado soft esteja definido como [`WindowSoftInputModeAdjust.Resize`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WindowSoftInputModeAdjust.Resize) .

> [!NOTE]
> Observe que esses eventos são habilitados por padrão para preservar o comportamento existente para aplicativos que dependem dos eventos. A desabilitação desses eventos faz com que o ciclo de eventos AppCompat corresponda ao ciclo de evento anterior ao AppCompat.

Essa plataforma específica pode ser consumida em XAML definindo as [`Application.SendDisappearingEventOnPause`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat.Application.SendDisappearingEventOnPauseProperty) [`Application.SendAppearingEventOnResume`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat.Application.SendAppearingEventOnResumeProperty) Propriedades, e [`Application.ShouldPreserveKeyboardOnResume`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat.Application.ShouldPreserveKeyboardOnResumeProperty) anexadas para `boolean` valores:

```xaml
<Application ...
             xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core"             xmlns:androidAppCompat="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat;assembly=Xamarin.Forms.Core"
             android:Application.WindowSoftInputModeAdjust="Resize"
             androidAppCompat:Application.SendDisappearingEventOnPause="false"
             androidAppCompat:Application.SendAppearingEventOnResume="false"
             androidAppCompat:Application.ShouldPreserveKeyboardOnResume="true">
  ...
</Application>
```

Como alternativa, ele pode ser consumido em C# usando a API fluente:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat;
...

Xamarin.Forms.Application.Current.On<Android>()
     .UseWindowSoftInputModeAdjust(WindowSoftInputModeAdjust.Resize)
     .SendDisappearingEventOnPause(false)
     .SendAppearingEventOnResume(false)
     .ShouldPreserveKeyboardOnResume(true);
```

O `Application.Current.On<Android>` método especifica que essa plataforma específica será executada somente no Android. O [ `Application.SendDisappearingEventOnPause` ] (xref: Xamarin.Forms . PlatformConfiguration. AndroidSpecific. AppCompat. Application. SendDisappearingEventOnPause ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. Android, Xamarin.Forms . Application}, System. Boolean)), no [`Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat) namespace, é usado para habilitar ou desabilitar o acionamento do [`Disappearing`](xref:Xamarin.Forms.Page.Appearing) evento Page, quando o aplicativo entra no plano de fundo. O [ `Application.SendAppearingEventOnResume` ] (xref: Xamarin.Forms . PlatformConfiguration. AndroidSpecific. AppCompat. Application. SendAppearingEventOnResume ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. Android, Xamarin.Forms . Application}, System. Boolean)) é usado para habilitar ou desabilitar o acionamento do [`Appearing`](xref:Xamarin.Forms.Page.Appearing) evento Page, quando o aplicativo é retomado do plano de fundo. O [ `Application.ShouldPreserveKeyboardOnResume` ] (xref: Xamarin.Forms . PlatformConfiguration. AndroidSpecific. AppCompat. Application. ShouldPreserveKeyboardOnResume ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. Android, Xamarin.Forms . Aplicativo}, System. Boolean)) é usado para controlar se o teclado virtual é exibido no currículo, se ele foi exibido na pausa, desde que o modo operacional do teclado suave esteja definido como [`WindowSoftInputModeAdjust.Resize`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WindowSoftInputModeAdjust.Resize) .

O resultado é que os [`Disappearing`](xref:Xamarin.Forms.Page.Appearing) eventos de página e não serão disparados [`Appearing`](xref:Xamarin.Forms.Page.Appearing) no aplicativo Pause e resume, respectivamente, e que, se o teclado virtual tiver sido exibido quando o aplicativo foi pausado, ele também será exibido quando o aplicativo for retomado:

[![](page-lifecycle-events-images/keyboard-on-resume.png "Lifecycle Events Platform-Specific")](page-lifecycle-events-images/keyboard-on-resume-large.png#lightbox "Lifecycle Events Platform-Specific")

## <a name="related-links"></a>Links relacionados

- [PlatformSpecifics (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Criação de itens específicos à plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API AndroidSpecific](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [API AndroidSpecific. AppCompat](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)
