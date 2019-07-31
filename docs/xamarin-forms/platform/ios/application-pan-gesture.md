---
title: Reconhecimento de gesto de Pan simultânea no iOS
description: Especificidades da plataforma permitem que você consumir funcionalidade só está disponível em uma plataforma específica, sem implementar renderizadores personalizados ou efeitos. Este artigo explica como consumir a plataforma específica do iOS, que permite que o reconhecimento de gesto de Pan simultâneo seja usado em um aplicativo.
ms.prod: xamarin
ms.assetid: 883D89DA-F8FF-4B97-9C3F-2DD05C96A495
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
ms.openlocfilehash: 8e86141ac27999a71a84ae7150b19ef3f60c117f
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68655976"
---
# <a name="simultaneous-pan-gesture-recognition-on-ios"></a>Reconhecimento de gesto de Pan simultânea no iOS

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Quando um [ `PanGestureRecognizer` ](xref:Xamarin.Forms.PanGestureRecognizer) é anexado a um modo de exibição dentro de uma exibição de rolagem, todos a panorâmica de gestos são capturados pelo `PanGestureRecognizer` e não serão passados para o modo de exibição de rolagem. Portanto, o modo de exibição de rolagem não mais será rolada.

Essa plataforma específica do IOS permite que `PanGestureRecognizer` um em uma exibição de rolagem Capture e compartilhe o gesto de panorâmica com a exibição de rolagem. Ele é consumido em XAML, definindo o [ `Application.PanGestureRecognizerShouldRecognizeSimultaneously` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Application.PanGestureRecognizerShouldRecognizeSimultaneouslyProperty) anexado à propriedade `true`:

```xaml
<Application ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
             ios:Application.PanGestureRecognizerShouldRecognizeSimultaneously="true">
    ...
</Application>
```

Como alternativa, ele pode ser consumido de C# usando a API fluente:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

Xamarin.Forms.Application.Current.On<iOS>().SetPanGestureRecognizerShouldRecognizeSimultaneously(true);
```

O `Application.On<iOS>` método Especifica que este específicos da plataforma serão executado apenas no iOS. O [ `Application.SetPanGestureRecognizerShouldRecognizeSimultaneously` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Application.SetPanGestureRecognizerShouldRecognizeSimultaneously(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.Application},System.Boolean)) método, no [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) namespace, é usada para controlar se um reconhecedor de gestos em uma exibição da rolagem panorâmica capturar o gesto de panorâmica, ou capturar e compartilhar a panorâmica com o modo de exibição de rolagem do gesto. Além disso, o [ `Application.GetPanGestureRecognizerShouldRecognizeSimultaneously` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Application.GetPanGestureRecognizerShouldRecognizeSimultaneously(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.Application})) método pode ser usado para retornar se o gesto de Panorâmica é compartilhado com o modo de exibição de rolagem que contém o [ `PanGestureRecognizer` ](xref:Xamarin.Forms.PanGestureRecognizer).

Portanto, com essa específicos da plataforma habilitado, quando um [ `ListView` ](xref:Xamarin.Forms.ListView) contém um [ `PanGestureRecognizer` ](xref:Xamarin.Forms.PanGestureRecognizer), ambos o `ListView` e o `PanGestureRecognizer` receberá o gesto de Panorâmica e processá-lo. No entanto, com essa específicos da plataforma desabilitado, quando um `ListView` contém uma `PanGestureRecognizer`, o `PanGestureRecognizer` capturará o gesto de Panorâmica e processá-lo e o `ListView` não receberá o gesto de panorâmica.

## <a name="related-links"></a>Links relacionados

- [PlatformSpecifics (amostra)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Criação de itens específicos à plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
