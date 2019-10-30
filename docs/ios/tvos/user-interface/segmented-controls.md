---
title: Trabalhando com controles segmentados tvOS no Xamarin
description: Este documento descreve como trabalhar com controles segmentados tvOS em um aplicativo criado com o Xamarin. Ele aborda ícones de segmento e texto, eventos, modificação da aparência do controle e muito mais.
ms.prod: xamarin
ms.assetid: 23AD94CC-E93A-40B1-8E2B-ECD21FA355BE
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/16/2017
ms.openlocfilehash: 0bedd898232b39898e6079a204bd7409587cde82
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73030402"
---
# <a name="working-with-tvos-segmented-controls-in-xamarin"></a>Trabalhando com controles segmentados tvOS no Xamarin

Um controle segmentado fornece um conjunto de elementos lineares, cada um dos quais pode conter um ícone ou texto, e é usado para fornecer um conjunto de opções relacionadas ao usuário.

[![](segmented-controls-images/segment01.png "Sample segment controls")](segmented-controls-images/segment01.png#lightbox)

A Apple tem as seguintes sugestões para trabalhar com controles segmentados:

- **Forneça um amplo** cuidado para fornecer um amplo espaço entre outros [itens com foco](~/ios/tvos/app-fundamentals/navigation-focus.md) e um controle segmentado. Um segmento individual é selecionado quando ele está em foco (não quando ele é clicado) e o usuário pode acidentalmente alterar os segmentos quando eles realmente desejam selecionar outro item com foco no segmento atual.
- **Usar exibições divididas para filtragem de conteúdo** -os controles segmentados não fazem boas escolhas para a filtragem de conteúdo, pois as exibições divididas foram projetadas para facilitar a navegação entre o conteúdo e os filtros.
- **Limite para sete segmentos máx** . você deve tentar manter o número máximo de segmentos abaixo de oito (8), já que isso é mais fácil de analisar em toda a sala do sofá e mais fácil de navegar.
- **Usar tamanho de conteúdo de segmento consistente** -todos os segmentos têm a mesma largura e, se possível, você deve tentar manter o conteúdo em cada segmento do mesmo tamanho. Isso não apenas faz com que os controles de segmento sejam mais visualmente agradáveis, mas facilita a leitura em um relance.
- **Evite a combinação de ícones e texto** -cada segmento individual pode conter um ícone ou texto, mas não ambos. Embora seja possível misturar ícones e texto no mesmo controle segmentado, isso deve ser evitado.

<a name="About-Segment-Icons" />

## <a name="about-segment-icons"></a>Sobre ícones de segmento

A Apple sugere o uso de imagens simples e reconhecíveis para ícones de segmento, como uma lupa para pesquisa. Ícones muito complexos são difíceis de reconhecer em uma tela de TV na sala, portanto, é melhor limitar os ícones a representações simples.

Você não pode misturar texto e ícones em um determinado segmento e deve evitar misturar ícones e texto em um único controle segmentado. Deve ser todos os ícones ou todo o texto.

<a name="Summary" />

## <a name="segment-text"></a>Texto do segmento

A Apple faz as seguintes sugestões para trabalhar com o texto do segmento:

- **Usar substantivos curtos e significativos** -o título do segmento deve declarar claramente o tipo de conteúdo que o usuário deve esperar ao selecionar o segmento fornecido. Por exemplo: música ou vídeos.
- **Usar capitalização de maiúsculas e minúsculas** – cada palavra do título dos segmentos deve ser capitalizada, exceto artigos, conconjuntos e preposições de menos de quatro (4) caracteres.
- **Usar títulos curtos e focados** – Mantenha os títulos, curtos e focados no tipo de conteúdo a ser esperado quando o segmento for selecionado.

Novamente, você não pode misturar texto e ícones em um determinado segmento e deve evitar misturar ícones e texto em um único controle segmentado.

<a name="Segment-Controls-and-Storyboards" />

## <a name="segment-controls-and-storyboards"></a>Controles de segmento e storyboards

A maneira mais fácil de trabalhar com controles de segmento em um aplicativo Xamarin. tvOS é adicioná-los à interface do usuário do aplicativo usando o designer do iOS.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

1. No **painel de soluções**, clique duas vezes no arquivo de `Main.storyboard` e abra-o para edição.
1. Arraste um **controle de segmento** da **caixa de ferramentas** e solte-o na exibição: 

    [![](segmented-controls-images/segment02.png "A Segment Control")](segmented-controls-images/segment02.png#lightbox)
1. Na **Guia Widget** do painel de **Propriedades**, você pode ajustar várias propriedades do controle de segmento, como seu **estilo** e **estado**: 

    [![](segmented-controls-images/segment03.png "The Widget Tab")](segmented-controls-images/segment03.png#lightbox)
1. Use o campo **segmentos** para controlar o número de segmentos no controlador.
1. Selecione um determinado segmento na **lista suspensa segmento** para ajustar suas propriedades individuais, como **título** ou **imagem** , e para controlar se um determinado segmento está **habilitado** ou **selecionado** quando o controle é exibido.
1. Por fim, atribua **nomes** aos controles para que você possa respondê-los no C# código. Por exemplo: 

    [![](segmented-controls-images/segment04.png "Assign a Name")](segmented-controls-images/segment04.png#lightbox)
1. Salve as alterações.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. No **Gerenciador de soluções**, clique duas vezes no arquivo de `Main.storyboard` e abra-o para edição.
1. Arraste um **controle de segmento** da **caixa de ferramentas** e solte-o na exibição: 

    [![](segmented-controls-images/segment02-vs.png "A Segment Control")](segmented-controls-images/segment02-vs.png#lightbox)
1. Na **Guia Widget** do Gerenciador de **Propriedades**, você pode ajustar várias propriedades do controle de segmento, como seu **estilo** e **estado**: 

    [![](segmented-controls-images/segment03-vs.png "The Widget Tab")](segmented-controls-images/segment03-vs.png#lightbox)
1. Use o campo **segmentos** para controlar o número de segmentos no controlador.
1. Selecione um determinado segmento na **lista suspensa segmento** para ajustar suas propriedades individuais, como **título** ou **imagem** , e para controlar se um determinado segmento está **habilitado** ou **selecionado** quando o controle é exibido.
1. Por fim, atribua **nomes** aos controles para que você possa respondê-los no C# código. Por exemplo: 

    [![](segmented-controls-images/segment04-vs.png "Assign a Name")](segmented-controls-images/segment04-vs.png#lightbox)
1. Salve as alterações.

-----

Para obter mais informações sobre como trabalhar com storyboards, consulte nossa [Guia de início rápido Olá, tvOS](~/ios/tvos/get-started/hello-tvos.md). 

<a name="Working-with-Segmented-Controls" />

## <a name="working-with-segmented-controls"></a>Trabalhando com controles segmentados

Conforme mencionado acima, o controle segmentado s fornece um conjunto de elementos lineares, cada um dos quais pode conter um ícone ou texto, e é usado para fornecer um conjunto de opções relacionadas ao usuário.

Há várias maneiras diferentes pelas quais você pode trabalhar com controles segmentados em seu aplicativo Xamarin. tvOS.

<a name="Exposed-as-Outlets-and-Actions" />

## <a name="exposed-as-names-and-events"></a>Exposto como nomes e eventos

Se você criou o controle de segmento no designer de interface e o expôs como um controle nomeado e um evento, poderá usar o seguinte código para responder à alteração de segmento:

```csharp
partial void PlayerCountChanged (Foundation.NSObject sender) {

    // Take action based on the number of players selected
    switch(PlayerCount.SelectedSegment) {
    case 0:
        // Do something if the segment is selected
        ...
        break;
    case 1:
        // Do something if the segment is selected
        ...
        break;
    case 2:
        // Do something if the segment is selected
        ...
        break;
    }
}
```

No caso do exemplo acima, o controle de segmento foi exposto como um nome de `PlayerCount` e uma ação de evento `PlayerCountChanged`. Para obter mais informações sobre como trabalhar com ações e saídas, consulte a seção [escrevendo o código com saídas e ações](~/ios/tvos/get-started/hello-tvos.md#Writing-the-Code) do nosso [olá, tvOS guia de início rápido](~/ios/tvos/get-started/hello-tvos.md).

A propriedade `SelectedSegment` Obtém ou define o segmento atualmente selecionado como um índice baseado em zero (0). Portanto, se você tiver cinco (5) segmentos, o primeiro segmento terá um índice de zero (0) e o último índice de quatro (4).

<a name="Modifying-Segments" />

## <a name="modifying-segments"></a>Modificando segmentos

A qualquer momento, você pode modificar o número e o conteúdo de seus controles segmentados. Use o código a seguir para inserir um novo segmento de ícone:

```csharp
// Icon Segment
SegmentedControl.InsertSegment(UIImage.FromFile("icon.png"), 0, true);

// Text Segment
SegmentedControl.InsertSegment("New Segment", 0, true);
```

O segundo parâmetro define onde o segmento será inserido usando um índice baseado em zero (0). Se o último parâmetro for `true` a inserção será animada.

Para remover um determinado segmento, use o seguinte:

```csharp
SegmentedControl.RemoveSegmentAtIndex(0, true);
```

Ou o seguinte para remover todos os segmentos:

```csharp
SegmentedControl.RemoveAllSegments();
```

Novamente, se o último parâmetro for `true`, a remoção será animada. Use a propriedade `NumberOfSegments` para retornar o número atual de segmentos.

Para obter o **título** ou o **ícone** de um determinado segmento, use o seguinte:

```csharp
// Get title
var title = SegmentedControl.TitleAt(0);

// Get icon
var icon = SegmentedControl.ImageAt(0);
```

E para alterar o **título** ou o **ícone**, use o seguinte:

```csharp
// Set title
SegmentedControl.SetTitle("New Title", 0);

// Set icon
SegmentedControl.SetImage(UIImage.FromFile("icon.png"), 0);
```

Para ver se um determinado segmento está **habilitado**, use o seguinte:

```csharp
if (SegmentedControl.IsEnabled(0)) {
    // Do something
    ...
}
```

E para **habilitar/desabilitar** um determinado segmento, use o seguinte:

```csharp
SegmentedControl.SetEnabled(false, 0);
```

<a name="Modifying-the-Segmented-Controls-Appearance" />

## <a name="modifying-the-segmented-controls-appearance"></a>Modificando a aparência do controle segmentado

Você pode usar o seguinte código para alterar o plano de fundo de um determinado segmento para uma imagem:

```csharp
SegmentedControl.SetBackgroundImage (UIImage.FromFile("background.png"), UIControlState.Normal, UIBarMetrics.Default);
```

Em que `UIControlState` especifica o estado do controle para o qual você está definindo a imagem como:

- Normal
- Taco
- Disabled
- Selecionado
- Focalizado

E `UIBarMetrics` especifica as métricas a serem usadas como:

- Padrão
- Compactá
- Defaultprompt
- CompactPrompt

Além disso, você pode definir o divisor entre os segmentos usando:

```csharp
SegmentedControl.SetDividerImage (UIImage.FromFile("divider.png"), UIControlState.Normal, UIControlState.Normal, UIBarMetrics.Default);
```

Em que a primeira `UIControlState` especifica o estado do segmento à esquerda do divisor e a segunda `UIControlState` especifica o estado do segmento à direita.

<a name="Summary" />

## <a name="summary"></a>Resumo

Este artigo abordou a criação e o trabalho com controle segmentado dentro de um aplicativo Xamarin. tvOS.

## <a name="related-links"></a>Links relacionados

- [Amostras do tvOS](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+tvOS)
- [tvOS](https://developer.apple.com/tvos/)
- [Guias de interface humana do tvOS](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guia de programação de aplicativo para tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
