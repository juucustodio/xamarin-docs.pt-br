---
title: Atualizações de controle do Thread principal no iOS
description: Especificidades da plataforma permitem que você consumir funcionalidade só está disponível em uma plataforma específica, sem implementar renderizadores personalizados ou efeitos. Este artigo explica como utilizar o iOS específicos da plataforma que permite controlar o layout e renderização de atualizações a serem executadas no thread principal.
ms.prod: xamarin
ms.assetid: 945E711D-9BD2-4BF9-9FB3-CBE0D5B25A49
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
ms.openlocfilehash: b9f39cd33d660999cfa00f2003edab7af731ca7c
ms.sourcegitcommit: b23a107b0fe3d2f814ae35b52a5855b6ce2a3513
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/20/2019
ms.locfileid: "65925710"
---
# <a name="main-thread-control-updates-on-ios"></a>Atualizações de controle do Thread principal no iOS

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/PlatformSpecifics/)

Este específicos da plataforma iOS permite controlar o layout e renderização de atualizações a serem executadas no thread principal, em vez de que está sendo executada em um thread em segundo plano. Ele deve ser raramente necessário, mas em alguns casos, pode impedir que falhas. Seu consumido em XAML, definindo o `Application.HandleControlUpdatesOnMainThread` para a propriedade associável `true`:

```xaml
<Application ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
             ios:Application.HandleControlUpdatesOnMainThread="true">
    ...
</Application>
```

Como alternativa, ele pode ser consumido de C# usando a API fluente:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

Xamarin.Forms.Application.Current.On<iOS>().SetHandleControlUpdatesOnMainThread(true);
```

O `Application.On<iOS>` método Especifica que este específicos da plataforma serão executado apenas no iOS. O `Application.SetHandleControlUpdatesOnMainThread` método, no [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) namespace, é usada para controlar se o controle de layout e renderização atualizações são executadas no thread principal, em vez de que está sendo executada em um thread em segundo plano. Além disso, o `Application.GetHandleControlUpdatesOnMainThread` método pode ser usado para retornar se o controle de layout e renderização atualizações estão sendo executadas no thread principal.

## <a name="related-links"></a>Links relacionados

- [PlatformSpecifics (amostra)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/PlatformSpecifics/)
- [Criação de itens específicos à plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [iOSSpecific API](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
