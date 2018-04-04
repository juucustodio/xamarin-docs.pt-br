---
title: Trabalhando com controles segmentados
description: Este artigo aborda a criação e trabalhar com controles segmentado dentro de um aplicativo Xamarin.tvOS.
ms.prod: xamarin
ms.assetid: 23AD94CC-E93A-40B1-8E2B-ECD21FA355BE
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/16/2017
ms.openlocfilehash: d4eac932c7fad628a0a65127bceb641f34ea5d79
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="working-with-segmented-controls"></a>Trabalhando com controles segmentados

_Este artigo aborda a criação e trabalhar com controles segmentado dentro de um aplicativo Xamarin.tvOS._


Um controle segmentado fornece um conjunto de elementos lineares, cada um deles pode conter um ícone ou texto e é usada para fornecer um conjunto de opções relacionadas ao usuário.

[![](segmented-controls-images/segment01.png "Controles de segmento de exemplo")](segmented-controls-images/segment01.png#lightbox)

Apple tem as seguintes sugestões para trabalhar com controles segmentado:

- **Fornecer espaço suficiente** -cuidado deve ser para fornecer espaço suficiente entre outros [itens Focusable é](~/ios/tvos/app-fundamentals/navigation-focus.md) e o controle segmentados. Um segmento individual é selecionado quando ele estiver em foco (não quando ele for clicado) e o usuário pode alterar os segmentos acidentalmente quando eles realmente desejam selecionar outro Item de controle no segmento atual.
- **Usar modos de exibição de divisão para a filtragem de conteúdo** -segmentado controles não faça uma ótima opção para filtragem, como divisão exibições foram projetadas para facilitar a navegação entre o conteúdo e os filtros de conteúdo.
- **Limite máximo de segmentos de sete** -você deve tentar manter o número máximo de segmentos abaixo oito (8) como isso é mais fácil de analisar de dentro de uma sala no sofá e mais fácil para navegar.
- **Usar o tamanho de conteúdo do segmento consistente** - todos os segmentos têm a mesma largura e, se possível, você deve tentar manter o conteúdo de cada segmento do mesmo tamanho. Isso não só torna os controles de segmento mais visualmente agradável, mas torna mais fácil de ler em um relance.
- **Evitar a mistura de ícones e texto** -cada segmento individual pode conter um ícone ou texto, mas não ambos. Embora seja possível misturar ícones e o texto no mesmo controle segmentado, isso deve ser evitado.

<a name="About-Segment-Icons" />

## <a name="about-segment-icons"></a>Sobre os ícones de segmento

Apple sugere o uso simples, reconhecíveis imagens para ícones de segmento, como uma lupa para pesquisa. Ícones excessivamente complexas são difíceis de reconhecer em uma tela de TV na sala, portanto, é melhor limitar os ícones para representações simples.

Você não pode misturar ícones em um determinado segmento e texto e você deve evitar a combinação de ícones e texto em um controle segmentado único. Ele deve ser todos os ícones ou todo o texto.

<a name="Summary" />

## <a name="segment-text"></a>Texto de segmento

Apple torna as sugestões a seguir para trabalhar com texto de segmento:

- **Use curto, nomes significativos** -título o segmento claramente deve indicar o tipo de conteúdo que o usuário deve esperar ao selecionar um determinado segmento. Por exemplo: música ou vídeos.
- **Usar a capitalização de título caso** -todas as palavras do título segmentos devem estar em letras maiusculas exceto artigos, conjunções e preposições de menos de quatro (4) caracteres.
- **Use curto, títulos de foco** -manter os títulos de curtos e voltada para o tipo de conteúdo esperado quando o segmento é selecionado.

Novamente, você não pode misturar ícones em um determinado segmento e texto e você deve evitar a combinação de ícones e texto em um controle segmentado único.

<a name="Segment-Controls-and-Storyboards" />

## <a name="segment-controls-and-storyboards"></a>Controles de segmento e Storyboards

É a maneira mais fácil trabalhar com controles de segmento em um aplicativo de Xamarin.tvOS para adicioná-los à interface de usuário do aplicativo usando o Designer do iOS.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

1. No **solução preenchimento**, clique duas vezes no `Main.storyboard` de arquivo e abri-lo para edição.
1. Arraste um **segmento controle** do **caixa de ferramentas** e solte-o no modo de exibição: 

    [![](segmented-controls-images/segment02.png "Um controle de segmento")](segmented-controls-images/segment02.png#lightbox)
1. No **Widget guia** do **propriedade preenchimento**, você pode ajustar várias propriedades do controle de segmento, como seu **estilo** e **estado**: 

    [![](segmented-controls-images/segment03.png "A guia de Widget")](segmented-controls-images/segment03.png#lightbox)
1. Use o **segmentos** campo para controlar o número de segmentos no controlador.
1. Selecione um determinado segmento do **segmento suspenso** ajustar suas propriedades individuais, como **título** ou **imagem** e para controlar se um determinado segmento é  **Habilitado** ou **selecionados** quando o controle é exibido.
1. Por fim, atribuir **nomes** para os controles para que você pode responder a eles no código c#. Por exemplo: 

    [![](segmented-controls-images/segment04.png "Atribua um nome")](segmented-controls-images/segment04.png#lightbox)
1. Salve as alterações.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)
    
1. No **Solution Explorer**, clique duas vezes o `Main.storyboard` de arquivo e abri-lo para edição.
1. Arraste um **segmento controle** do **caixa de ferramentas** e solte-o no modo de exibição: 

    [![](segmented-controls-images/segment02-vs.png "Um controle de segmento")](segmented-controls-images/segment02-vs.png#lightbox)
1. No **Widget guia** do **propriedade Explorer**, você pode ajustar várias propriedades do controle de segmento, como seu **estilo** e **estado**: 

    [![](segmented-controls-images/segment03-vs.png "A guia de Widget")](segmented-controls-images/segment03-vs.png#lightbox)
1. Use o **segmentos** campo para controlar o número de segmentos no controlador.
1. Selecione um determinado segmento do **segmento suspenso** ajustar suas propriedades individuais, como **título** ou **imagem** e para controlar se um determinado segmento é  **Habilitado** ou **selecionados** quando o controle é exibido.
1. Por fim, atribuir **nomes** para os controles para que você pode responder a eles no código c#. Por exemplo: 

    [![](segmented-controls-images/segment04-vs.png "Atribua um nome")](segmented-controls-images/segment04-vs.png#lightbox)
1. Salve as alterações.
    
-----

Para obter mais informações sobre como trabalhar com Storyboards, consulte nosso [Hello, tvOS Quick Start Guide](~/ios/tvos/get-started/hello-tvos.md). 

<a name="Working-with-Segmented-Controls" />

## <a name="working-with-segmented-controls"></a>Trabalhando com controles segmentados

Conforme mencionado acima, s que controle segmentado fornece um conjunto de elementos lineares, cada um deles pode conter um ícone ou texto e é usada para fornecer um conjunto de opções relacionadas ao usuário.

Há várias maneiras diferentes que você pode trabalhar com controles segmentado em seu aplicativo Xamarin.tvOS.

<a name="Exposed-as-Outlets-and-Actions" />

## <a name="exposed-as-names-and-events"></a>Expostos como nomes e eventos

Se você criou seu controle de segmento no Designer de Interface e expostos-lo como um controle chamado e um evento, você pode usar o seguinte código para responder à alteração de segmento:

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

No caso do exemplo acima, o controle de segmento foi exposto como um `PlayerCount` nome e uma `PlayerCountChanged` ação de evento. Para obter mais informações sobre como trabalhar com ações e tomadas, consulte o [escrever o código com tomadas e ações](~/ios/tvos/get-started/hello-tvos.md#Writing-the-Code) seção do nosso [Hello, tvOS Quick Start Guide](~/ios/tvos/get-started/hello-tvos.md).

O `SelectedSegment` propriedade obtém ou define o índice do segmento atualmente selecionado como um zero (0) com base. Portanto, se você tiver segmentos de cinco (5), o primeiro segmento terá um índice de zero (0) e a última um índice de quatro (4).

<a name="Modifying-Segments" />

## <a name="modifying-segments"></a>Modificando segmentos

A qualquer momento, você pode modificar o número e o conteúdo dos controles segmentados. Use o código a seguir para inserir um novo ícone de segmento:

```csharp
// Icon Segment
SegmentedControl.InsertSegment(UIImage.FromFile("icon.png"), 0, true);

// Text Segment
SegmentedControl.InsertSegment("New Segment", 0, true);
```

O segundo parâmetro define onde o segmento será inserido usando um índice com base zero (0). Se for o último parâmetro `true` a inserção será animada.

Para remover um determinado segmento, use o seguinte:

```csharp
SegmentedControl.RemoveSegmentAtIndex(0, true);
```

Ou a seguir para remover todos os segmentos:

```csharp
SegmentedControl.RemoveAllSegments();
```

Novamente, se for o último parâmetro `true`, a remoção será animada. Use o `NumberOfSegments` propriedade para retornar o número atual de segmentos.

Para obter o **título** ou **ícone** para um determinado segmento, use o seguinte:

```csharp
// Get title
var title = SegmentedControl.TitleAt(0);

// Get icon
var icon = SegmentedControl.ImageAt(0);
```

E para alterar o **título** ou **ícone**, use o seguinte:

```csharp
// Set title
SegmentedControl.SetTitle("New Title", 0);

// Set icon
SegmentedControl.SetImage(UIImage.FromFile("icon.png"), 0);
```

Para ver se um determinado segmento **habilitado**, use o seguinte:

```csharp
if (SegmentedControl.IsEnabled(0)) {
    // Do something
    ...
}
```

E **habilitar/desabilitar** um dado segmento, use o seguinte:

```csharp
SegmentedControl.SetEnabled(false, 0);
```

<a name="Modifying-the-Segmented-Controls-Appearance" />

## <a name="modifying-the-segmented-controls-appearance"></a>Modificando a aparência do controle segmentado

Você pode usar o código a seguir para alterar o plano de fundo de um determinado segmento de uma imagem:

```csharp
SegmentedControl.SetBackgroundImage (UIImage.FromFile("background.png"), UIControlState.Normal, UIBarMetrics.Default);
```

Onde `UIControlState` Especifica o estado do controle que você está definindo a imagem para como:

- Normal
- realçado
- Disabled
- Selecionado
- Focalizado

E `UIBarMetrics` Especifica as métricas para usar como:

- Padrão
- Compact
- DefaultPrompt
- CompactPrompt

Além disso, você pode definir o divisor entre os segmentos usando:

```csharp
SegmentedControl.SetDividerImage (UIImage.FromFile("divider.png"), UIControlState.Normal, UIControlState.Normal, UIBarMetrics.Default);
```

Onde a primeira `UIControlState` Especifica o estado do segmento para a esquerda o divisor e o segundo `UIControlState` Especifica o estado do segmento para a direita.

<a name="Summary" />

## <a name="summary"></a>Resumo

Este artigo abordou criando e trabalhando com controle segmentado dentro de um aplicativo Xamarin.tvOS.



## <a name="related-links"></a>Links relacionados

- [Amostras do tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS guias de Interface Humana](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guia de programação de aplicativo para tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
