---
title: Títulos de página grande no iOS
description: Especificidades da plataforma permitem que você consumir funcionalidade só está disponível em uma plataforma específica, sem implementar renderizadores personalizados ou efeitos. Este artigo explica como utilizar a plataforma específica do iOS que exibe o título da página como um título grande na barra de navegação de uma NavigationPage.
ms.prod: xamarin
ms.assetid: 45FD9145-8319-452C-9AE6-624431A4D43C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
ms.openlocfilehash: ad1c7f94467c6f32b9108ab7f6abe85d31679d3a
ms.sourcegitcommit: 395774577f7524b57035c5cca3c9034a4b636489
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/10/2019
ms.locfileid: "54208981"
---
# <a name="large-page-titles-on-ios"></a>Títulos de página grande no iOS

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)

Este específicos da plataforma iOS é usado para exibir o título da página como um título grande na barra de navegação de um [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage), para dispositivos que usam iOS 11 ou superior. Um título grande é alinhado à esquerda e usa uma fonte maior e faz a transição para um título padrão conforme o usuário começa a rolagem de conteúdo, para que o espaço na tela é usado com eficiência. No entanto, em orientação paisagem, o título retornará para o centro da barra de navegação para otimizar o layout do conteúdo. Ele é consumido em XAML, definindo o `NavigationPage.PrefersLargeTitles` anexado à propriedade um `boolean` valor:

```xaml
<NavigationPage xmlns="http://xamarin.com/schemas/2014/forms"
                xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
                ...
                ios:NavigationPage.PrefersLargeTitles="true">
  ...
</NavigationPage>
```

Como alternativa, ele pode ser consumido de C# usando a API fluente:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

var navigationPage = new Xamarin.Forms.NavigationPage(new iOSLargeTitlePageCS());
navigationPage.On<iOS>().SetPrefersLargeTitles(true);
```

O `NavigationPage.On<iOS>` método Especifica que este específicos da plataforma serão executado apenas no iOS. O `NavigationPage.SetPrefersLargeTitle` método, no [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) namespace, que controla se os títulos grandes estão habilitados.

Desde que os títulos grandes estão habilitados na [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage), todas as páginas na pilha de navegação serão exibir títulos grandes. Esse comportamento pode ser substituído nas páginas, definindo o `Page.LargeTitleDisplay` propriedade anexada a um valor da `LargeTitleDisplayMode` enumeração:

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
             Title="Large Title"
             ios:Page.LargeTitleDisplay="Never">
  ...
</ContentPage>
```

Como alternativa, o comportamento de página pode ser substituído no C# usando a API fluente:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

public class iOSLargeTitlePageCS : ContentPage
{
    public iOSLargeTitlePageCS(ICommand restore)
    {
        On<iOS>().SetLargeTitleDisplay(LargeTitleDisplayMode.Never);
        ...
    }
    ...
}
```

O `Page.On<iOS>` método Especifica que este específicos da plataforma serão executado apenas no iOS. O `Page.SetLargeTitleDisplay` método, no [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) namespace, controla o comportamento de título grande no [ `Page` ](xref:Xamarin.Forms.Page), com o `LargeTitleDisplayMode` fornecendo três possíveis de enumeração valores:

- `Always` – forçar a barra de navegação e a fonte tamanho para usar o formato grande.
- `Automatic` – use o mesmo estilo (pequeno ou grande) como o item anterior na pilha de navegação.
- `Never` – forçar o uso da barra de navegação de formato pequeno regular.

Além disso, o `SetLargeTitleDisplay` método pode ser usado para alternar os valores de enumeração por meio da chamada a `LargeTitleDisplay` método, que retorna atual `LargeTitleDisplayMode`:

```csharp
switch (On<iOS>().LargeTitleDisplay())
{
    case LargeTitleDisplayMode.Always:
        On<iOS>().SetLargeTitleDisplay(LargeTitleDisplayMode.Automatic);
        break;
    case LargeTitleDisplayMode.Automatic:
        On<iOS>().SetLargeTitleDisplay(LargeTitleDisplayMode.Never);
        break;
    case LargeTitleDisplayMode.Never:
        On<iOS>().SetLargeTitleDisplay(LargeTitleDisplayMode.Always);
        break;
}
```

O resultado é que o especificado `LargeTitleDisplayMode` é aplicada para o [ `Page` ](xref:Xamarin.Forms.Page), que controla o comportamento de título grande:

![](page-large-title-images/large-title.png "Específico da plataforma efeito de desfoque")

## <a name="related-links"></a>Links relacionados

- [PlatformSpecifics (amostra)](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)
- [Criação de itens específicos à plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [iOSSpecific API](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
