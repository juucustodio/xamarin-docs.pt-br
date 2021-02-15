---
title: Reconhecimento de gesto de Pan simultânea no iOS
description: As especificações de plataforma permitem que você consuma a funcionalidade que só está disponível em uma plataforma específica, sem implementar renderizadores ou efeitos personalizados. Este artigo explica como consumir a plataforma específica do iOS, que permite que o reconhecimento de gesto de Pan simultâneo seja usado em um aplicativo.
ms.prod: xamarin
ms.assetid: 883D89DA-F8FF-4B97-9C3F-2DD05C96A495
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: f1baf5d71cc25a1f84ff683e9d0072f34715c0da
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93373231"
---
# <a name="simultaneous-pan-gesture-recognition-on-ios"></a>Reconhecimento de gesto de Pan simultânea no iOS

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Quando um [`PanGestureRecognizer`](xref:Xamarin.Forms.PanGestureRecognizer) é anexado a uma exibição dentro de uma exibição de rolagem, todos os gestos de panorâmica são capturados pelo `PanGestureRecognizer` e não são passados para a exibição de rolagem. Portanto, a exibição de rolagem não rolará mais.

Essa plataforma específica do iOS permite que um `PanGestureRecognizer` em uma exibição de rolagem Capture e compartilhe o gesto de panorâmica com a exibição de rolagem. Ele é consumido em XAML definindo a [`Application.PanGestureRecognizerShouldRecognizeSimultaneously`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Application.PanGestureRecognizerShouldRecognizeSimultaneouslyProperty) Propriedade anexada como `true` :

```xaml
<Application ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
             ios:Application.PanGestureRecognizerShouldRecognizeSimultaneously="true">
    ...
</Application>
```

Como alternativa, ele pode ser consumido em C# usando a API fluente:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

Xamarin.Forms.Application.Current.On<iOS>().SetPanGestureRecognizerShouldRecognizeSimultaneously(true);
```

O `Application.On<iOS>` método especifica que essa plataforma específica será executada somente no Ios. O [ `Application.SetPanGestureRecognizerShouldRecognizeSimultaneously` ] (xref: Xamarin.Forms . PlatformConfiguration. iOSSpecific. Application. SetPanGestureRecognizerShouldRecognizeSimultaneously ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. iOS, Xamarin.Forms . Application}, System. Boolean)), no [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) namespace, é usado para controlar se um reconhecedor de gesto panorâmico em uma exibição de rolagem capturará o gesto de panorâmica ou captura e compartilhará o gesto de panorâmica com a exibição de rolagem. Além disso, o [ `Application.GetPanGestureRecognizerShouldRecognizeSimultaneously` ] (xref: Xamarin.Forms . PlatformConfiguration. iOSSpecific. Application. GetPanGestureRecognizerShouldRecognizeSimultaneously ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. iOS, Xamarin.Forms . Application})), o método pode ser usado para retornar se o gesto de panorâmica é compartilhado com a exibição de rolagem que contém o [`PanGestureRecognizer`](xref:Xamarin.Forms.PanGestureRecognizer) .

Portanto, com essa plataforma específica habilitada, quando um [`ListView`](xref:Xamarin.Forms.ListView) contém um [`PanGestureRecognizer`](xref:Xamarin.Forms.PanGestureRecognizer) , o `ListView` e o `PanGestureRecognizer` receberão o gesto de Pan e o processarão. No entanto, com essa plataforma específica desabilitada, quando um `ListView` contém um `PanGestureRecognizer` , o `PanGestureRecognizer` irá capturar o gesto de Pan e processá-lo, e o `ListView` não receberá o gesto de panorâmica.

## <a name="related-links"></a>Links relacionados

- [PlatformSpecifics (exemplo)](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Criação de itens específicos à plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)