---
title: Trabalhando com controles segmentados de tvOS no Xamarin
description: Este documento descreve como trabalhar com tvOS controles segmentados em um aplicativo criado com o Xamarin. Ele aborda os ícones de segmento e o texto, eventos, modificando a aparência do controle e muito mais.
ms.prod: xamarin
ms.assetid: 23AD94CC-E93A-40B1-8E2B-ECD21FA355BE
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: 98a770d05014e0498b805ed9ffa0c84314efc765
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61374945"
---
# <a name="working-with-tvos-segmented-controls-in-xamarin"></a>Trabalhando com controles segmentados de tvOS no Xamarin

Um controle segmentado fornece um conjunto de elementos lineares, cada um deles pode conter um ícone ou texto e é usada para fornecer um conjunto de opções relacionadas ao usuário.

[![](segmented-controls-images/segment01.png "Controles de segmento de exemplo")](segmented-controls-images/segment01.png#lightbox)

Apple tem as sugestões a seguir para trabalhar com controles segmentados:

- **Fornecer um amplo espaço** -cuidado deve ser demoram para fornecer um amplo espaço entre outros [focalizável itens](~/ios/tvos/app-fundamentals/navigation-focus.md) e um controle segmentado. Um segmento individual será marcado quando ele estiver em foco (não quando ele é clicado) e o usuário pode alterar os segmentos acidentalmente quando eles realmente querem selecionar outro Item focalizável no segmento atual.
- **Usar modos de exibição de divisão para filtragem de conteúdo** -controles segmentados não fazer boas escolhas para filtragem como dividir modos de exibição foram projetados para facilitar a navegação entre o conteúdo e os filtros de conteúdo.
- **Limite máximo de segmentos de sete** -você deve tentar manter o número máximo de segmentos abaixo oito (8), como isso é mais fácil de analisar os dentro de uma sala, no sofá e mais fácil de navegar.
- **Usar tamanho de conteúdo do segmento consistente** - todos os segmentos têm a mesma largura e, se possível, você deve tentar manter o conteúdo de cada segmento do mesmo tamanho. Isso não apenas torna os controles do segmento mais visualmente agradável, mas torna mais fácil de ler em um relance.
- **Evite misturar ícones e textos** -cada segmento individual pode conter um ícone ou texto, mas não ambos. Embora seja possível misturar os ícones e texto no mesmo controle segmentado, isso deve ser evitado.

<a name="About-Segment-Icons" />

## <a name="about-segment-icons"></a>Sobre os ícones de segmento

Apple sugere o uso simples, reconhecíveis imagens para ícones de segmento, como uma lupa pesquisada. Ícones excessivamente complexas são difíceis reconhecer em uma tela de TV na sala, portanto, é melhor limitar seus ícones para representações simples.

Você não pode misturar texto e ícones em um determinado segmento e você deve evitar misturar ícones e texto em um único controle segmentado. Ele deve ser todos os ícones ou todo o texto.

<a name="Summary" />

## <a name="segment-text"></a>Texto de segmento

A Apple deixa as sugestões a seguir para trabalhar com texto de segmento:

- **Usar curto, substantivos significativo** -título o segmento claramente deve indicar o tipo de conteúdo que o usuário deve esperar ao selecionar um determinado segmento. Por exemplo: Música ou vídeos.
- **Usar a capitalização de capitalização de título** -todas as palavras do título de segmentos devem estar em letras maiusculas, exceto artigos, associações e preposições de menos de quatro (4) caracteres.
- **Usar curto, com foco títulos** -manter os títulos curtos e focados no tipo de conteúdo esperado quando o segmento está selecionado.

Novamente, você não pode misturar texto e ícones em um determinado segmento e você deve evitar misturar ícones e texto em um único controle segmentado.

<a name="Segment-Controls-and-Storyboards" />

## <a name="segment-controls-and-storyboards"></a>Controles de segmento e Storyboards

A maneira mais fácil trabalhar com controles de segmento em um aplicativo xamarin. tvos é adicioná-los à interface de usuário do aplicativo usando o Designer do iOS.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

1. No **painel de soluções**, clique duas vezes o `Main.storyboard` de arquivo e abri-lo para edição.
1. Arraste uma **controle de segmento** da **caixa de ferramentas** e solte-o no modo de exibição: 

    [![](segmented-controls-images/segment02.png "Um controle de segmento")](segmented-controls-images/segment02.png#lightbox)
1. No **guia de Widget** da **painel de propriedade**, você pode ajustar várias propriedades do controle de segmento, como seu **estilo** e **estado**: 

    [![](segmented-controls-images/segment03.png "Na guia de Widget")](segmented-controls-images/segment03.png#lightbox)
1. Use o **segmentos** campo para controlar o número de segmentos no controlador.
1. Selecione um determinado segmento do **lista suspensa de segmento** ajustar suas propriedades individuais, como **título** ou **imagem** e para controlar se um determinado segmento é  **Habilitada** ou **selecionados** quando o controle é exibido.
1. Por fim, ceda **nomes** para os controles para que você possa responder a elas dentro de C# código. Por exemplo: 

    [![](segmented-controls-images/segment04.png "Atribua um nome")](segmented-controls-images/segment04.png#lightbox)
1. Salve as alterações.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)
    
1. No **Gerenciador de soluções**, clique duas vezes o `Main.storyboard` de arquivo e abri-lo para edição.
1. Arraste uma **controle de segmento** da **caixa de ferramentas** e solte-o no modo de exibição: 

    [![](segmented-controls-images/segment02-vs.png "Um controle de segmento")](segmented-controls-images/segment02-vs.png#lightbox)
1. No **guia de Widget** da **Property Explorer**, você pode ajustar várias propriedades do controle de segmento, como seu **estilo** e **estado**: 

    [![](segmented-controls-images/segment03-vs.png "Na guia de Widget")](segmented-controls-images/segment03-vs.png#lightbox)
1. Use o **segmentos** campo para controlar o número de segmentos no controlador.
1. Selecione um determinado segmento do **lista suspensa de segmento** ajustar suas propriedades individuais, como **título** ou **imagem** e para controlar se um determinado segmento é  **Habilitada** ou **selecionados** quando o controle é exibido.
1. Por fim, ceda **nomes** para os controles para que você possa responder a elas dentro de C# código. Por exemplo: 

    [![](segmented-controls-images/segment04-vs.png "Atribua um nome")](segmented-controls-images/segment04-vs.png#lightbox)
1. Salve as alterações.
    
-----

Para obter mais informações sobre como trabalhar com Storyboards, consulte nosso [Olá, guia de início rápido do tvOS](~/ios/tvos/get-started/hello-tvos.md). 

<a name="Working-with-Segmented-Controls" />

## <a name="working-with-segmented-controls"></a>Trabalhando com controles segmentados

Conforme mencionado acima, s que controle segmentado fornece um conjunto de elementos lineares, cada um deles pode conter um ícone ou o texto e é usada para fornecer um conjunto de opções relacionadas ao usuário.

Há várias maneiras diferentes que você pode trabalhar com controles segmentados em seu aplicativo xamarin. tvos.

<a name="Exposed-as-Outlets-and-Actions" />

## <a name="exposed-as-names-and-events"></a>Exposto como nomes e eventos

Se você criou seu controle de segmento no Designer de Interface e expostos-lo como um controle nomeado e um evento, você pode usar o código a seguir para responder à alteração de segmento:

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

No caso do exemplo acima, o controle de segmento foi exposto como uma `PlayerCount` nome e uma `PlayerCountChanged` ação do evento. Para obter mais informações sobre como trabalhar com ações e as saídas, consulte o [escrevendo o código com saídas e ações](~/ios/tvos/get-started/hello-tvos.md#Writing-the-Code) seção do nosso [Olá, guia de início rápido do tvOS](~/ios/tvos/get-started/hello-tvos.md).

O `SelectedSegment` propriedade obtém ou define o índice do segmento selecionado no momento como zero (0) com base. Portanto, se você tiver cinco (5) segmentos, o primeiro segmento terá um índice de zero (0) e o último um índice de quatro (4).

<a name="Modifying-Segments" />

## <a name="modifying-segments"></a>Modificação de segmentos

A qualquer momento, você pode modificar o número e o conteúdo dos controles segmentados. Use o código a seguir para inserir um novo ícone de segmento:

```csharp
// Icon Segment
SegmentedControl.InsertSegment(UIImage.FromFile("icon.png"), 0, true);

// Text Segment
SegmentedControl.InsertSegment("New Segment", 0, true);
```

O segundo parâmetro define onde o segmento será inserido usando um índice baseado em zero (0). Se o último parâmetro é `true` a inserção será animada.

Para remover um determinado segmento, use o seguinte:

```csharp
SegmentedControl.RemoveSegmentAtIndex(0, true);
```

Ou a seguir para remover todos os segmentos:

```csharp
SegmentedControl.RemoveAllSegments();
```

Novamente, se o último parâmetro é `true`, a remoção será animada. Use o `NumberOfSegments` propriedade para retornar o número atual de segmentos.

Para obter o **Title** ou **ícone** para um determinado segmento, use o seguinte:

```csharp
// Get title
var title = SegmentedControl.TitleAt(0);

// Get icon
var icon = SegmentedControl.ImageAt(0);
```

E para alterar o **Title** ou **ícone**, use o seguinte:

```csharp
// Set title
SegmentedControl.SetTitle("New Title", 0);

// Set icon
SegmentedControl.SetImage(UIImage.FromFile("icon.png"), 0);
```

Para ver se um determinado segmento **Enabled**, use o seguinte:

```csharp
if (SegmentedControl.IsEnabled(0)) {
    // Do something
    ...
}
```

E, ao **habilitar/desabilitar** um segmento de dado, use o seguinte:

```csharp
SegmentedControl.SetEnabled(false, 0);
```

<a name="Modifying-the-Segmented-Controls-Appearance" />

## <a name="modifying-the-segmented-controls-appearance"></a>Modificando a aparência do controle segmentado

Você pode usar o código a seguir para alterar o plano de fundo de um determinado segmento de uma imagem:

```csharp
SegmentedControl.SetBackgroundImage (UIImage.FromFile("background.png"), UIControlState.Normal, UIBarMetrics.Default);
```

Onde `UIControlState` Especifica o estado do controle que você está definindo na imagem para como:

- Normal
- realçado
- Disabled
- Selecionado
- Focalizado

E `UIBarMetrics` Especifica a métrica a ser usado como:

- Padrão
- Compactar
- DefaultPrompt
- CompactPrompt

Além disso, você pode definir o divisor entre os segmentos usando:

```csharp
SegmentedControl.SetDividerImage (UIImage.FromFile("divider.png"), UIControlState.Normal, UIControlState.Normal, UIBarMetrics.Default);
```

Em que a primeira `UIControlState` Especifica o estado do segmento à esquerda do divisor e a segunda `UIControlState` Especifica o estado do segmento para a direita.

<a name="Summary" />

## <a name="summary"></a>Resumo

Este artigo abordou a projetar e trabalhar com o controle segmentado dentro de um aplicativo xamarin. tvos.



## <a name="related-links"></a>Links relacionados

- [Amostras do tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [Guias de Interface humana do tvOS](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guia de programação de aplicativo para tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
