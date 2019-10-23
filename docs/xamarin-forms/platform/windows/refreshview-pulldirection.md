---
title: Direção de pull do RefreshView no Windows
description: As especificações de plataforma permitem que você consuma a funcionalidade que só está disponível em uma plataforma específica, sem implementar renderizadores ou efeitos personalizados. Este artigo explica como consumir a plataforma Windows específica que permite que a direção de pull de um RefreshView seja alterada.
ms.prod: xamarin
ms.assetid: 407A862B-281E-4384-9696-C0655830B84D
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/20/2019
ms.openlocfilehash: cf2ab38bed7b45a48fcf0b5f86add49c0d4cc21f
ms.sourcegitcommit: 21d8be9571a2fa89fb7d8ff0787ff4f957de0985
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/21/2019
ms.locfileid: "72697314"
---
# <a name="refreshview-pull-direction-on-windows"></a>Direção de pull do RefreshView no Windows

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Essa Plataforma Universal do Windows específica à plataforma permite que a direção de pull de uma `RefreshView` seja alterada para corresponder à orientação do controle rolável que está exibindo dados. Ele é consumido em XAML definindo a propriedade `RefreshView.RefreshPullDirection` vinculável como um valor da enumeração `RefreshPullDirection`:

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

Como alternativa, ele pode ser consumido C# usando a API fluente:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.WindowsSpecific;
...
refreshView.On<Windows>().SetRefreshPullDirection(RefreshPullDirection.LeftToRight);
```

O método `RefreshView.On<Windows>` especifica que essa plataforma específica será executada somente no Plataforma Universal do Windows. O método `RefreshView.SetRefreshPullDirection`, no namespace [`Xamarin.Forms.PlatformConfiguration.WindowsSpecific`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) , é usado para definir a direção de pull do `RefreshView`, com a enumeração `RefreshPullDirection` fornecendo quatro valores possíveis:

- `LeftToRight` indica que um pull da esquerda para a direita inicia uma atualização.
- `TopToBottom` indica que um pull de cima para baixo inicia uma atualização e é a direção de pull padrão de um `RefreshView`.
- `RightToLeft` indica que um pull da direita para a esquerda inicia uma atualização.
- `BottomToTop` indica que um pull da parte inferior para a superior inicia uma atualização.

Além disso, o método `GetRefreshPullDirection` pode ser usado para retornar o `RefreshPullDirection` atual do `RefreshView`.

O resultado é que um `RefreshPullDirection` especificado é aplicado ao `RefreshView`, para definir a direção de pull para corresponder à orientação do controle rolável que está exibindo dados. A captura de tela a seguir mostra uma `RefreshView` com uma direção de pull `LeftToRight`:

[![Captura de tela de um RefreshView com uma direção de pull da esquerda para a direita, em UWP](refreshview-pulldirection-images/refreshview-pulldirection.png "RefreshView com a direção de pull da esquerda para a direita")](refreshview-pulldirection-images/refreshview-pulldirection-large.png#lightbox "RefreshView com a direção de pull da esquerda para a direita")

> [!NOTE]
> Quando você altera a direção de pull, a posição inicial do círculo de progresso gira automaticamente para que a seta comece na posição apropriada para a direção de pull.

## <a name="related-links"></a>Links relacionados

- [PlatformSpecifics (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Criação de itens específicos à plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API WindowsSpecific](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific)
