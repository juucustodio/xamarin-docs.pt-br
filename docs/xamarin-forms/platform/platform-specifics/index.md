---
title: "Especificações de plataforma"
description: "Específicos de plataforma permitem consumir funcionalidade só está disponível em uma plataforma específica, sem implementar renderizadores personalizados ou efeitos."
ms.topic: article
ms.prod: xamarin
ms.assetid: 4729DB9C-8800-4E29-9D66-3BE13C5F8C94
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/17/2017
ms.openlocfilehash: e1598241e64e6ed3a77fec3803e7fa4d94ee7433
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="platform-specifics"></a>Especificações de plataforma

_Específicos de plataforma permitem consumir funcionalidade só está disponível em uma plataforma específica, sem implementar renderizadores personalizados ou efeitos._

A seguinte funcionalidade específica de plataforma é criada em xamarin. Forms:

<table>
  <thead>
    <tr>
      <td><strong>iOS</strong></td>
      <td><strong>Android</strong></td>
      <td><strong>Windows</strong></td>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td><a href="~/xamarin-forms/platform/platform-specifics/consuming/ios.md#blur">VisualElement.BlurEffect</a></td>
      <td><a href="~/xamarin-forms/platform/platform-specifics/consuming/android.md#soft_input_mode">Application.WindowSoftInputModeAdjust</a></td>
      <td><a href="~/xamarin-forms/platform/platform-specifics/consuming/windows.md#toolbar_placement">Page.ToolbarPlacement</a></td>
    </tr>
    <tr>
      <td><a href="~/xamarin-forms/platform/platform-specifics/consuming/ios.md#large_title">NavigationPage.PrefersLargeTitles</a></td>
      <td><a href="~/xamarin-forms/platform/platform-specifics/consuming/android.md#fastscroll">ListView.IsFastScrollEnabled</a></td>
      <td><a href="~/xamarin-forms/platform/platform-specifics/consuming/windows.md#collapsable_navigation_bar">MasterDetailPage.CollapsedPaneWidth e MasterDetailPage.CollapseStyle</a></td>
    </tr>    
    <tr>
      <td><a href="~/xamarin-forms/platform/platform-specifics/consuming/ios.md#safe_area_layout">Page.UseSafeArea</a></td>
      <td><a href="~/xamarin-forms/platform/platform-specifics/consuming/android.md#enable_swipe_paging">TabbedPage.IsSwipePagingEnabled</a></td>
      <td></td>
    </tr>
    <tr>
      <td><a href="~/xamarin-forms/platform/platform-specifics/consuming/ios.md#translucent_navigation_bar">NavigationPage.IsNavigationBarTranslucent</a></td>
      <td><a href="~/xamarin-forms/platform/platform-specifics/consuming/android.md#elevation">Elevation.Elevation</a></td>
      <td></td>
    </tr>
    <tr>
      <td><a href="~/xamarin-forms/platform/platform-specifics/consuming/ios.md#status_bar_color_mode">NavigationPage.StatusBarTextColorMode</a></td>
      <td><a href="~/xamarin-forms/platform/platform-specifics/consuming/android.md#disable_lifecycle_events">Application.SendDisappearingEventOnPause, Application.SendAppearingEventOnResume, and Application.ShouldPreserveKeyboardOnResume</a></td>
      <td></td>
    </tr>
    <tr>
      <td><a href="~/xamarin-forms/platform/platform-specifics/consuming/ios.md#adjust_font_size">Entry.AdjustsFontSizeToFitWidth</a></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <td><a href="~/xamarin-forms/platform/platform-specifics/consuming/ios.md#picker_update_mode">Picker.UpdateMode</a></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <td><a href="~/xamarin-forms/platform/platform-specifics/consuming/ios.md#set_status_bar_visibility">Page.PrefersStatusBarHidden e Page.PreferredStatusBarUpdateAnimation</a></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <td><a href="~/xamarin-forms/platform/platform-specifics/consuming/ios.md#delay_content_touches">ScrollView.ShouldDelayContentTouches</a></td>
      <td></td>
      <td></td>
    </tr>
  </tbody>
</table>

O processo para consumir um específico de plataforma por meio de XAML, ou por meio de uma API fluente de código é o seguinte:

1. Adicionar um `xmlns` declaração ou `using` diretiva para o [ `Xamarin.Forms.PlatformConfiguration` ](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration/) namespace.
1. Adicionar um `xmlns` declaração ou `using` diretiva para o namespace que contém a funcionalidade específica de plataforma:
    1. No iOS, essa é a [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration.iOSSpecific/) namespace.
    1. No Android, este é o [ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` ](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration.AndroidSpecific/) namespace. Para o Android AppCompat, esse é o [ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat` ](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat/) namespace.
    1. Na plataforma Universal do Windows, esse é o [ `Xamarin.Forms.PlatformConfiguration.WindowsSpecific` ](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration.WindowsSpecific/) namespace.
1. Aplicar o específico da plataforma de XAML ou de código com o `On<T>` API fluente. O valor de `T` pode ser o [ `iOS` ](https://developer.xamarin.com/api/type/Xamarin.Forms.PlatformConfiguration.iOS/), [ `Android` ](https://developer.xamarin.com/api/type/Xamarin.Forms.PlatformConfiguration.Android/), ou [ `Windows` ](https://developer.xamarin.com/api/type/Xamarin.Forms.PlatformConfiguration.Windows/) tipos do [ `Xamarin.Forms.PlatformConfiguration` ](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration/) namespace.

> [!NOTE]
> Observe que a tentativa de consumir uma plataforma específica em uma plataforma em que está disponível não resultará em erro. Em vez disso, o código será executado sem o específico da plataforma que está sendo aplicado.

Especificações da plataforma consumida por meio de `On<T>` fluente código de retorno da API [ `IPlatformElementConfiguration` ](https://developer.xamarin.com/api/type/Xamarin.Forms.IPlatformElementConfiguration%3CTPlatform,TElement%3E/) objetos. Isso permite que vários específicos de plataforma a ser invocado no mesmo objeto com método em cascata.

Para obter mais informações sobre especificações da plataforma, consulte [específicos de plataforma consumindo](~/xamarin-forms/platform/platform-specifics/consuming/index.md) e [específicos de plataforma criando](~/xamarin-forms/platform/platform-specifics/creating.md).


## <a name="related-links"></a>Links relacionados

- [Consumindo específicos de plataforma](~/xamarin-forms/platform/platform-specifics/consuming/index.md)
- [Criando específicos de plataforma](~/xamarin-forms/platform/platform-specifics/creating.md)
- [PlatformSpecifics (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)
- [PlatformConfiguration](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration/)
