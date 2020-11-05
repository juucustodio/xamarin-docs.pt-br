---
title: NavigationPage bar Translucency no iOS
description: As especificações de plataforma permitem que você consuma a funcionalidade que só está disponível em uma plataforma específica, sem implementar renderizadores ou efeitos personalizados. Este artigo explica como consumir a plataforma específica do iOS que altera a transparência da barra de navegação em um NavigationPage.
ms.prod: xamarin
ms.assetid: 1150941B-56DB-4781-BE36-A4C4F9F2C500
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: c565467d3c42dbaa9f9d9fc40352efbc964503a9
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93372308"
---
# <a name="navigationpage-bar-translucency-on-ios"></a>NavigationPage bar Translucency no iOS

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Essa plataforma específica do iOS é usada para alterar a transparência da barra de navegação em um [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) e é consumida em XAML definindo a [`NavigationPage.IsNavigationBarTranslucent`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage.IsNavigationBarTranslucentProperty) Propriedade anexada como um `boolean` valor:

```xaml
<NavigationPage ...
                xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
                BackgroundColor="Blue"
                ios:NavigationPage.IsNavigationBarTranslucent="true">
  ...
</NavigationPage>
```

Como alternativa, ele pode ser consumido em C# usando a API fluente:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

(App.Current.MainPage as Xamarin.Forms.NavigationPage).BackgroundColor = Color.Blue;
(App.Current.MainPage as Xamarin.Forms.NavigationPage).On<iOS>().EnableTranslucentNavigationBar();
```

O `NavigationPage.On<iOS>` método especifica que essa plataforma específica será executada somente no Ios. O [ `NavigationPage.EnableTranslucentNavigationBar` ] (xref: Xamarin.Forms . PlatformConfiguration. iOSSpecific. NavigationPage. EnableTranslucentNavigationBar ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. iOS, Xamarin.Forms . NavigationPage})), no [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) namespace, é usado para tornar a barra de navegação translúcida. Além disso, a [`NavigationPage`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage) classe no `Xamarin.Forms.PlatformConfiguration.iOSSpecific` namespace também tem um [ `DisableTranslucentNavigationBar` ] (xref: Xamarin.Forms . PlatformConfiguration. iOSSpecific. NavigationPage. DisableTranslucentNavigationBar ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. iOS, Xamarin.Forms . NavigationPage})), o método que restaura a barra de navegação para seu estado padrão e um [ `SetIsNavigationBarTranslucent` ] (xref: Xamarin.Forms . PlatformConfiguration. iOSSpecific. NavigationPage. SetIsNavigationBarTranslucent ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. iOS, Xamarin.Forms . NavigationPage}, System. Boolean)) que pode ser usado para alternar a transparência da barra de navegação chamando o método [ `IsNavigationBarTranslucent` ] (xref: Xamarin.Forms . PlatformConfiguration. iOSSpecific. NavigationPage. IsNavigationBarTranslucent ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. iOS, Xamarin.Forms . NavigationPage})), método:

```csharp
(App.Current.MainPage as Xamarin.Forms.NavigationPage)
  .On<iOS>()
  .SetIsNavigationBarTranslucent(!(App.Current.MainPage as Xamarin.Forms.NavigationPage).On<iOS>().IsNavigationBarTranslucent());
```

O resultado é que a transparência da barra de navegação pode ser alterada:

![Barra de navegação translúcida Platform-Specific](navigation-bar-translucent-images/translucent-navigation-bar.png)

## <a name="related-links"></a>Links relacionados

- [PlatformSpecifics (exemplo)](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Criação de itens específicos à plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)