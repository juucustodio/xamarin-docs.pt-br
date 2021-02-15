---
title: Direção de pull de RefreshView no Windows
description: As especificações de plataforma permitem que você consuma a funcionalidade que só está disponível em uma plataforma específica, sem implementar renderizadores ou efeitos personalizados. Este artigo explica como consumir a plataforma Windows específica que permite que a direção de pull de um RefreshView seja alterada.
ms.prod: xamarin
ms.assetid: 407A862B-281E-4384-9696-C0655830B84D
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/20/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 2aebd86b4b15dec1a79cb60057e0c04f2e196ac5
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93372893"
---
# <a name="refreshview-pull-direction-on-windows"></a>Direção de pull de RefreshView no Windows

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Essa Plataforma Universal do Windows específica à plataforma permite que a direção de pull de um seja `RefreshView` alterada para corresponder à orientação do controle rolável que está exibindo dados. Ele é consumido em XAML definindo a `RefreshView.RefreshPullDirection` propriedade vinculável como um valor da `RefreshPullDirection` enumeração:

```xaml
<ContentPage ...
             xmlns:windows="clr-namespace:Xamarin.Forms.PlatformConfiguration.WindowsSpecific;assembly=Xamarin.Forms.Core">
    <RefreshView windows:RefreshView.RefreshPullDirection="LeftToRight"
                 IsRefreshing="{Binding IsRefreshing}"
                 Command="{Binding RefreshCommand}">
        <ScrollView>
            ...
        </ScrollView>
    </RefreshView>
 </ContentPage>
```

Como alternativa, ele pode ser consumido em C# usando a API fluente:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.WindowsSpecific;
...
refreshView.On<Windows>().SetRefreshPullDirection(RefreshPullDirection.LeftToRight);
```

O `RefreshView.On<Windows>` método especifica que essa plataforma específica será executada somente no plataforma universal do Windows. O `RefreshView.SetRefreshPullDirection` método, no [`Xamarin.Forms.PlatformConfiguration.WindowsSpecific`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) namespace, é usado para definir a direção de pull do `RefreshView` , com a `RefreshPullDirection` Enumeração fornecendo quatro valores possíveis:

- `LeftToRight` indica que um pull da esquerda para a direita inicia uma atualização.
- `TopToBottom` indica que um pull de cima para baixo inicia uma atualização e é a direção de pull padrão de um `RefreshView` .
- `RightToLeft` indica que um pull da direita para a esquerda inicia uma atualização.
- `BottomToTop` indica que um pull da parte inferior para a superior inicia uma atualização.

Além disso, o `GetRefreshPullDirection` método pode ser usado para retornar o atual `RefreshPullDirection` do `RefreshView` .

O resultado é que um especificado `RefreshPullDirection` é aplicado ao `RefreshView` , para definir a direção de pull para corresponder à orientação do controle rolável que está exibindo dados. A captura de tela a seguir mostra uma `RefreshView` com uma `LeftToRight` direção de pull:

[![Captura de tela de um RefreshView com uma direção de pull da esquerda para a direita, em UWP](refreshview-pulldirection-images/refreshview-pulldirection.png "RefreshView com a direção de pull da esquerda para a direita")](refreshview-pulldirection-images/refreshview-pulldirection-large.png#lightbox "RefreshView com a direção de pull da esquerda para a direita")

> [!NOTE]
> Quando você altera a direção de pull, a posição inicial do círculo de progresso gira automaticamente para que a seta comece na posição apropriada para a direção de pull.

## <a name="related-links"></a>Links relacionados

- [PlatformSpecifics (exemplo)](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Criação de itens específicos à plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API WindowsSpecific](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific)