---
title: InputView ordem de leitura no Windows
description: Especificidades da plataforma permitem que você consumir funcionalidade só está disponível em uma plataforma específica, sem implementar renderizadores personalizados ou efeitos. Este artigo explica como consumir a plataforma específica do Windows que permite que a ordem de leitura do texto bidirecional seja detectada dinamicamente.
ms.prod: xamarin
ms.assetid: E61BAEE0-C8B7-4F33-8DDC-FA1B9CA8E81D
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
ms.openlocfilehash: c184424a982aa82712685dbc33ad57422f2f8338
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68651431"
---
# <a name="inputview-reading-order-on-windows"></a>InputView ordem de leitura no Windows

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Essa plataforma universal do Windows específica da plataforma habilita a ordem de leitura (da esquerda para a direita ou da direita para a esquerda) de texto bidirecional [`Entry`](xref:Xamarin.Forms.Entry)nas [`Editor`](xref:Xamarin.Forms.Editor)instâncias do [`Label`](xref:Xamarin.Forms.Label) , e que serão detectadas dinamicamente. Ele é consumido em XAML, definindo o [ `InputView.DetectReadingOrderFromContent` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.InputView.DetectReadingOrderFromContentProperty) (para `Entry` e `Editor` instâncias) ou [ `Label.DetectReadingOrderFromContent` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.Label.DetectReadingOrderFromContentProperty) anexado à propriedade um `boolean` valor:

```xaml
<ContentPage ...
             xmlns:windows="clr-namespace:Xamarin.Forms.PlatformConfiguration.WindowsSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout>
        <Editor ... windows:InputView.DetectReadingOrderFromContent="true" />
        ...
    </StackLayout>
</ContentPage>
```

Como alternativa, ele pode ser consumido de C# usando a API fluente:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.WindowsSpecific;
...

editor.On<Windows>().SetDetectReadingOrderFromContent(true);
```

O `Editor.On<Windows>` método Especifica que este específicos da plataforma só serão executado na plataforma Universal do Windows. O [ `InputView.SetDetectReadingOrderFromContent` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.InputView.SetDetectReadingOrderFromContent(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.InputView},System.Boolean)) método, no [ `Xamarin.Forms.PlatformConfiguration.WindowsSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) namespace, é usada para controlar se a ordem de leitura é detectada de conteúdo a [ `InputView` ](xref:Xamarin.Forms.InputView). Além disso, o `InputView.SetDetectReadingOrderFromContent` método pode ser usado para ativar ou desativar a ordem de leitura é detectada do conteúdo por meio da chamada a [ `InputView.GetDetectReadingOrderFromContent` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.InputView.GetDetectReadingOrderFromContent(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.InputView})) método para retornar o valor atual:

```csharp
editor.On<Windows>().SetDetectReadingOrderFromContent(!editor.On<Windows>().GetDetectReadingOrderFromContent());
```

O resultado é que [ `Entry` ](xref:Xamarin.Forms.Entry), [ `Editor` ](xref:Xamarin.Forms.Editor), e [ `Label` ](xref:Xamarin.Forms.Label) instâncias podem ter a ordem de leitura de seus conteúdos detectadas dinamicamente:

[![InputView detectando a ordem de leitura do conteúdo específico da plataforma](inputview-reading-order-images/editor-readingorder.png "InputView detectando a ordem de leitura do conteúdo específico da plataforma")](inputview-reading-order-images/editor-readingorder-large.png#lightbox "InputView detectando a ordem de leitura do conteúdo específico da plataforma")

> [!NOTE]
> Ao contrário da configuração de [ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection) propriedade, a lógica para modos de exibição que detectar que a ordem de leitura do seu conteúdo de texto não afetará o alinhamento do texto dentro da exibição. Em vez disso, ele ajusta a ordem na qual os blocos de texto bidirecional são apresentados.

## <a name="related-links"></a>Links relacionados

- [PlatformSpecifics (amostra)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Criação de itens específicos à plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API WindowsSpecific](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific)
