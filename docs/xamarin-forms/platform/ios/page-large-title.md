---
title: Títulos de páginas grandes no iOS
description: As especificações de plataforma permitem que você consuma a funcionalidade que só está disponível em uma plataforma específica, sem implementar renderizadores ou efeitos personalizados. Este artigo explica como consumir a plataforma do iOS específica que exibe o título da página como um título grande na barra de navegação de um NavigationPage.
ms.prod: xamarin
ms.assetid: 45FD9145-8319-452C-9AE6-624431A4D43C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: cd1271df727b3b37c49f744e0893703b9c2460d5
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93373712"
---
# <a name="large-page-titles-on-ios"></a>Títulos de páginas grandes no iOS

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Essa plataforma específica do iOS é usada para exibir o título da página como um título grande na barra de navegação de um [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) , para dispositivos que usam o Ios 11 ou superior. Um título grande é alinhado à esquerda e usa uma fonte maior e faz a transição para um título padrão à medida que o usuário começa a rolar o conteúdo, de modo que o espaço da tela seja usado com eficiência. No entanto, na orientação paisagem, o título voltará ao centro da barra de navegação para otimizar o layout do conteúdo. Ele é consumido em XAML definindo a `NavigationPage.PrefersLargeTitles` Propriedade anexada como um `boolean` valor:

```xaml
<NavigationPage xmlns="http://xamarin.com/schemas/2014/forms"
                xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
                ...
                ios:NavigationPage.PrefersLargeTitles="true">
  ...
</NavigationPage>
```

Como alternativa, ele pode ser consumido em C# usando a API fluente:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

var navigationPage = new Xamarin.Forms.NavigationPage(new iOSLargeTitlePageCS());
navigationPage.On<iOS>().SetPrefersLargeTitles(true);
```

O `NavigationPage.On<iOS>` método especifica que essa plataforma específica será executada somente no Ios. O `NavigationPage.SetPrefersLargeTitle` método, no [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) namespace, controla se os títulos grandes estão habilitados.

Desde que os títulos grandes estejam habilitados no [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) , todas as páginas na pilha de navegação exibirão títulos grandes. Esse comportamento pode ser substituído em páginas definindo a `Page.LargeTitleDisplay` Propriedade anexada como um valor da `LargeTitleDisplayMode` enumeração:

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
             Title="Large Title"
             ios:Page.LargeTitleDisplay="Never">
  ...
</ContentPage>
```

Como alternativa, o comportamento da página pode ser substituído do C# usando a API Fluent:

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

O `Page.On<iOS>` método especifica que essa plataforma específica será executada somente no Ios. O `Page.SetLargeTitleDisplay` método, no [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) namespace, controla o comportamento de título grande no [`Page`](xref:Xamarin.Forms.Page) , com a `LargeTitleDisplayMode` Enumeração fornecendo três valores possíveis:

- `Always` – Force a barra de navegação e o tamanho da fonte para usar o formato grande.
- `Automatic` – Use o mesmo estilo (grande ou pequeno) que o item anterior na pilha de navegação.
- `Never` – Force o uso da barra de navegação de formato normal e pequena.

Além disso, o `SetLargeTitleDisplay` método pode ser usado para alternar os valores de enumeração chamando o `LargeTitleDisplay` método, que retorna o atual `LargeTitleDisplayMode` :

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

O resultado é que um especificado `LargeTitleDisplayMode` é aplicado ao [`Page`](xref:Xamarin.Forms.Page) , que controla o comportamento do título grande:

![Efeito de desfoque Platform-Specific](page-large-title-images/large-title.png)

## <a name="related-links"></a>Links relacionados

- [PlatformSpecifics (exemplo)](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Criação de itens específicos à plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)