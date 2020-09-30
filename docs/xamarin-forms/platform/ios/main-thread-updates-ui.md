---
title: Principais atualizações de controle de thread no iOS
description: As especificações de plataforma permitem que você consuma a funcionalidade que só está disponível em uma plataforma específica, sem implementar renderizadores ou efeitos personalizados. Este artigo explica como consumir a plataforma do iOS específica que permite que o layout de controle e as atualizações de renderização sejam executadas no thread principal.
ms.prod: xamarin
ms.assetid: 945E711D-9BD2-4BF9-9FB3-CBE0D5B25A49
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 844e04b91910152954973f7e6f10c0d56acadf22
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91563674"
---
# <a name="main-thread-control-updates-on-ios"></a>Principais atualizações de controle de thread no iOS

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Essa plataforma do iOS específica habilita o layout de controle e as atualizações de renderização a serem executadas no thread principal, em vez de serem executadas em um thread em segundo plano. Ele deve ser raramente necessário, mas, em alguns casos, pode evitar falhas. Seu consumido em XAML definindo a `Application.HandleControlUpdatesOnMainThread` propriedade vinculável como `true` :

```xaml
<Application ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
             ios:Application.HandleControlUpdatesOnMainThread="true">
    ...
</Application>
```

Como alternativa, ele pode ser consumido em C# usando a API fluente:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

Xamarin.Forms.Application.Current.On<iOS>().SetHandleControlUpdatesOnMainThread(true);
```

O `Application.On<iOS>` método especifica que essa plataforma específica será executada somente no Ios. O `Application.SetHandleControlUpdatesOnMainThread` método, no [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) namespace, é usado para controlar se o layout de controle e as atualizações de renderização são executadas no thread principal, em vez de serem executadas em um thread em segundo plano. Além disso, o `Application.GetHandleControlUpdatesOnMainThread` método pode ser usado para retornar se o layout de controle e as atualizações de renderização estão sendo executadas no thread principal.

## <a name="related-links"></a>Links relacionados

- [PlatformSpecifics (exemplo)](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Criação de itens específicos à plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)