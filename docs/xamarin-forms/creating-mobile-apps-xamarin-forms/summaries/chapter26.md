---
title: Resumo do Capítulo 26. Layouts personalizados
description: 'Criação de aplicativos móveis com xamarin. Forms: Resumo do Capítulo 26. Layouts personalizados'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 2B7F4346-414E-49FF-97FB-B85E92D98A21
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2017
ms.openlocfilehash: 1eb5153f8ab295696e373f4fdb65a4f8820a05bc
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2020
ms.locfileid: "70770935"
---
# <a name="summary-of-chapter-26-custom-layouts"></a>Resumo do Capítulo 26. Layouts personalizados

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26)

O Xamarin. Forms inclui várias classes derivadas de [`Layout<View>`](xref:Xamarin.Forms.Layout`1):

- `StackLayout`,
- `Grid`,
- `AbsoluteLayout`e
- `RelativeLayout`.

Este capítulo descreve como criar suas próprias classes que derivam de `Layout<View>`.

## <a name="an-overview-of-layout"></a>Uma visão geral do layout

Não há nenhum sistema centralizado que manipula o layout do xamarin. Forms. Cada elemento é responsável por determinar qual deve ser seu próprio tamanho e como renderizar em si em uma área específica.

### <a name="parents-and-children"></a>Pais e filhos

Cada elemento que tem filhos é responsável por esses filhos dentro do próprio de posicionamento. É o pai que determina o tamanho de seus filhos deve ser baseado no tamanho tem disponível e o tamanho do filho quer ser.

### <a name="sizing-and-positioning"></a>Dimensionamento e posicionamento

Layout começa na parte superior da árvore visual com a página e, em seguida, continua por meio de todas as ramificações. O método público mais importante no layout é [`Layout`](xref:Xamarin.Forms.VisualElement.Layout(Xamarin.Forms.Rectangle)) definido por `VisualElement`. Cada elemento que é um pai de outros elementos chama `Layout` para cada um de seus filhos para dar ao filho um tamanho e uma posição em relação a si mesmo na forma de um valor de [`Rectangle`](xref:Xamarin.Forms.Rectangle) . Essas chamadas `Layout` são propagadas por meio da árvore visual.

Uma chamada para `Layout` é necessária para que um elemento apareça na tela e faz com que as seguintes propriedades somente leitura sejam definidas. Eles são consistentes com o `Rectangle` passado para o método:

- [`Bounds`](xref:Xamarin.Forms.VisualElement.Bounds) do tipo `Rectangle`
- [`X`](xref:Xamarin.Forms.VisualElement.X) do tipo `double`
- [`Y`](xref:Xamarin.Forms.VisualElement.Y) do tipo `double`
- [`Width`](xref:Xamarin.Forms.VisualElement.Width) do tipo `double`
- [`Height`](xref:Xamarin.Forms.VisualElement.Height) do tipo `double`

Antes da chamada `Layout`, `Height` e `Width` têm valores fictícios de &ndash;1.

Uma chamada para `Layout` também dispara chamadas para os seguintes métodos protegidos:

- [`SizeAllocated`](xref:Xamarin.Forms.VisualElement.SizeAllocated(System.Double,System.Double)), que chama
- [`OnSizeAllocated`](xref:Xamarin.Forms.VisualElement.OnSizeAllocated(System.Double,System.Double)), que pode ser substituído.

Por fim, o seguinte evento é acionado:

- [`SizeChanged`](xref:Xamarin.Forms.VisualElement.SizeChanged)

O método `OnSizeAllocated` é substituído por `Page` e `Layout`, que são as duas únicas classes no Xamarin. Forms que podem ter filhos. As chamadas de método substituído

- [`UpdateChildrenLayout`](xref:Xamarin.Forms.Page.UpdateChildrenLayout) para derivações de `Page` e [`UpdateChildrenLayout`](xref:Xamarin.Forms.Layout.UpdateChildrenLayout) para derivativos de `Layout`, que chama
- [`LayoutChildren`](xref:Xamarin.Forms.Page.LayoutChildren(System.Double,System.Double,System.Double,System.Double)) para derivações de `Page` e [`LayoutChildren`](xref:Xamarin.Forms.Layout.LayoutChildren(System.Double,System.Double,System.Double,System.Double)) para derivativos de `Layout`.

`LayoutChildren`, em seguida, chama `Layout` para cada um dos filhos do elemento. Se pelo menos um filho tiver uma nova configuração de `Bounds`, o seguinte evento será acionado:

- [`LayoutChanged`](xref:Xamarin.Forms.Page.LayoutChanged) para derivações de `Page` e [`LayoutChanged`](xref:Xamarin.Forms.Layout.LayoutChanged) para derivativos de `Layout`

### <a name="constraints-and-size-requests"></a>Solicitações de tamanho e restrições

Para `LayoutChildren` chamar de forma inteligente `Layout` em todos os seus filhos, ele deve saber um tamanho *preferencial* ou *desejado* para os filhos. Portanto, as chamadas para `Layout` para cada um dos filhos são geralmente precedidas por chamadas para

- [`GetSizeRequest`](xref:Xamarin.Forms.VisualElement.GetSizeRequest(System.Double,System.Double))

Depois que o livro foi publicado, o método `GetSizeRequest` foi preterido e substituído por

- [`Measure`](xref:Xamarin.Forms.VisualElement.Measure(System.Double,System.Double,Xamarin.Forms.MeasureFlags))

O método `Measure` acomoda a propriedade [`Margin`](xref:Xamarin.Forms.View.Margin) e inclui um argumento do tipo [`MeasureFlag`](xref:Xamarin.Forms.MeasureFlags), que tem dois membros:

- [`IncludeMargins`](xref:Xamarin.Forms.MeasureFlags.IncludeMargins)
- [`None`](xref:Xamarin.Forms.MeasureFlags.None) não incluir margens

Para muitos elementos, `GetSizeRequest` ou `Measure` Obtém o tamanho nativo do elemento do seu renderizador. Ambos os métodos têm parâmetros para *restrições*de largura e altura. Por exemplo, um `Label` usará a restrição de largura para determinar como encapsular várias linhas de texto.

Ambos `GetSizeRequest`e `Measure` retornam um valor do tipo [`SizeRequest`](xref:Xamarin.Forms.SizeRequest), que tem duas propriedades:

- [`Request`](xref:Xamarin.Forms.SizeRequest.Request) do tipo `Size`
- [`Minimum`](xref:Xamarin.Forms.SizeRequest.Minimum) do tipo `Size`

Geralmente, esses dois valores são os mesmos, e o valor de `Minimum` geralmente pode ser ignorado.

`VisualElement` também define um método protegido semelhante a `GetSizeRequest` chamado de `GetSizeRequest`:

- [`OnSizeRequest`](xref:Xamarin.Forms.VisualElement.OnSizeRequest(System.Double,System.Double)) retorna um valor de `SizeRequest`

Agora, esse método é preterido e substituído por:

- [`OnMeasure`](xref:Xamarin.Forms.VisualElement.OnMeasure(System.Double,System.Double))

Todas as classes que derivam de `Layout` ou `Layout<T>` devem substituir `OnSizeRequest` ou `OnMeasure`. É aí que uma classe de layout determina seu próprio tamanho, que geralmente se baseia no tamanho de seus filhos, que é obtido chamando `GetSizeRequest` ou `Measure` nos filhos. Antes e depois de chamar `OnSizeRequest` ou `OnMeasure`, `GetSizeRequest` ou `Measure` faz ajustes com base nas seguintes propriedades:

- [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest)do tipo `double`, afeta a propriedade `Request` de `SizeRequest`
- [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) do tipo `double`, afeta a propriedade `Request` de `SizeRequest`
- [`MinimumWidthRequest`](xref:Xamarin.Forms.VisualElement.MinimumWidthRequest) do tipo `double`, afeta a propriedade `Minimum` de `SizeRequest`
- [`MinimumHeightRequest`](xref:Xamarin.Forms.VisualElement.MinimumHeightRequest) do tipo `double`, afeta a propriedade `Minimum` de `SizeRequest`

### <a name="infinite-constraints"></a>Restrições de infinitas

Os argumentos de restrição passados para `GetSizeRequest` (ou `Measure`) e `OnSizeRequest` (ou `OnMeasure`) podem ser infinitos (ou seja, valores de `Double.PositiveInfinity`). No entanto, o `SizeRequest` retornado desses métodos não pode conter dimensões infinitas.

Restrições de infinitas indicam que o tamanho solicitado deve refletir o tamanho do elemento natural. Uma `StackLayout` vertical chama `GetSizeRequest` (ou `Measure`) em seus filhos com uma restrição de altura infinita. Um layout de pilha horizontal chama `GetSizeRequest` (ou `Measure`) em seus filhos com uma restrição de largura infinita. Um `AbsoluteLayout` chama `GetSizeRequest` (ou `Measure`) em seus filhos com restrições de largura e altura infinitas.

### <a name="peeking-inside-the-process"></a>Espiar dentro do processo

O [**ExploreChildSize**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/ExploreChildSizes) exibe informações de solicitação de restrição e de tamanho para um layout simples.

## <a name="deriving-from-layoutview"></a>Derivando da exibição de\<de layout >

Uma classe de layout personalizada deriva de `Layout<View>`. Ele tem duas responsabilidades:

- Substitua `OnMeasure` para chamar `Measure` em todos os filhos do layout. Retornar um tamanho solicitado para o layout em si
- Substituir `LayoutChildren` para chamar `Layout` em todos os filhos do layout

O loop `for` ou `foreach` nessas substituições deve ignorar qualquer filho cuja propriedade `IsVisible` esteja definida como `false`.

Uma chamada para `OnMeasure` não é garantida. `OnMeasure` não será chamado se o pai do layout estiver governando o tamanho do layout (por exemplo, um layout que preenche uma página). Por esse motivo, `LayoutChildren` não pode depender de tamanhos filho obtidos durante a chamada de `OnMeasure`. Com muita frequência, `LayoutChildren` deve chamar `Measure` nos filhos do layout ou você pode implementar algum tipo de lógica de cache de tamanho (a ser discutido posteriormente).

### <a name="an-easy-example"></a>Um exemplo simples

O exemplo [**VerticalStackDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/VerticalStackDemo) contém uma classe de [`VerticalStack`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter26/VerticalStackDemo/VerticalStackDemo/VerticalStackDemo/VerticalStack.cs) simplificada e uma demonstração de seu uso.

### <a name="vertical-and-horizontal-positioning-simplified"></a>Vertical e horizontal de posicionamento simplificado

Um dos trabalhos que `VerticalStack` deve executar ocorre durante a substituição de `LayoutChildren`. O método usa a propriedade `HorizontalOptions` do filho para determinar como posicionar o filho dentro de seu slot na `VerticalStack`. Em vez disso, você pode chamar o método estático [`Layout.LayoutChildIntoBoundingRect`](xref:Xamarin.Forms.Layout.LayoutChildIntoBoundingRegion(Xamarin.Forms.VisualElement,Xamarin.Forms.Rectangle)). Esse método chama `Measure` no filho e usa suas propriedades `HorizontalOptions` e `VerticalOptions` para posicionar o filho dentro do retângulo especificado.

### <a name="invalidation"></a>Invalidação

Geralmente, uma alteração na propriedade de um elemento afeta como esse elemento aparece no layout. O layout deve ser invalidado para disparar um novo layout.

`VisualElement` define um método protegido [`InvalidateMeasure`](xref:Xamarin.Forms.VisualElement.InvalidateMeasure), que geralmente é chamado pelo manipulador de propriedade alterada de qualquer propriedade vinculável cuja alteração afete o tamanho do elemento. O método `InvalidateMeasure` aciona um evento [`MeasureInvalidated`](xref:Xamarin.Forms.VisualElement.MeasureInvalidated) .

A classe `Layout` define um método protegido semelhante chamado [`InvalidateLayout`](xref:Xamarin.Forms.Layout.InvalidateLayout), que um derivativo `Layout` deve chamar para qualquer alteração que afete como ele posiciona e dimensiona seus filhos.

### <a name="some-rules-for-coding-layouts"></a>Algumas regras para layouts de codificação

1. As propriedades definidas por `Layout<T>` derivativos devem ser apoiadas pelas propriedades vinculáveis e os manipuladores de propriedade alterada devem chamar `InvalidateLayout`.

2. Um `Layout<T>` derivativo que define as propriedades vinculáveis anexadas deve substituir [`OnAdded`](xref:Xamarin.Forms.Layout`1.OnAdded*) para adicionar um manipulador de propriedade alterada a seus filhos e [`OnRemoved`](xref:Xamarin.Forms.Layout`1.OnRemoved*) para remover esse manipulador. O manipulador deve verificar se há alterações nas propriedades vinculáveis anexadas e responder chamando `InvalidateLayout`.

3. Um `Layout<T>` derivativo que implementa um cache de tamanhos filho deve substituir `InvalidateLayout` e [`OnChildMeasureInvalidated`](xref:Xamarin.Forms.Layout.OnChildMeasureInvalidated) e limpar o cache quando esses métodos são chamados.

### <a name="a-layout-with-properties"></a>Um layout com propriedades

A classe [`WrapLayout`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/WrapLayout.cs) no [**Xamarin. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) pressupõe que todos os seus filhos tenham o mesmo tamanho e encapsula os filhos de uma linha (ou coluna) para a próxima. Ele define uma propriedade `Orientation` como `StackLayout`e as propriedades `ColumnSpacing` e `RowSpacing` como `Grid`e armazena em cache os tamanhos filho.

O exemplo de foto [**Wrap**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/PhotoWrap) coloca um `WrapLayout` em um `ScrollView` para exibir fotos de estoque.

### <a name="no-unconstrained-dimensions-allowed"></a>Nenhuma dimensão irrestrita permitido!

O [`UniformGridLayout`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/UniformGridLayout.cs) na biblioteca [**Xamarin. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) destina-se a exibir todos os seus filhos dentro dele mesmo. Portanto, ele não pode lidar com dimensões irrestritos e gera uma exceção se um for encontrado.

O exemplo de [**fotograde**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/PhotoGrid) demonstra `UniformGridLayout`:

[![Captura de tela tripla da grade de fotos](images/ch26fg08-small.png "Layout de grade uniforme")](images/ch26fg08-large.png#lightbox "Layout de grade uniforme")

### <a name="overlapping-children"></a>Sobreposição de filhos

Um `Layout<T>` derivativo pode sobrepor seus filhos. No entanto, os filhos são renderizados em sua ordem na coleção de `Children`, e não na ordem em que seus métodos de `Layout` são chamados.

A classe `Layout` define dois métodos que permitem que você mova um filho dentro da coleção:

- [`LowerChild`](xref:Xamarin.Forms.Layout.LowerChild(Xamarin.Forms.View)) mover um filho para o início da coleção
- [`RaiseChild`](xref:Xamarin.Forms.Layout.RaiseChild(Xamarin.Forms.View)) mover um filho para o final da coleção

Para os filhos de sobreposição, filhos no final da coleção visualmente aparecem na parte superior de filhos no início da coleção.

A classe [`OverlapLayout`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/OverlapLayout.cs) na biblioteca [**Xamarin. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) define uma propriedade anexada para indicar a ordem de renderização e, portanto, permite que um de seus filhos seja exibido na parte superior das outras. O exemplo [**StudentCardFile**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/StudentCardFile) demonstra isso:

[![Captura de tela tripla da grade de arquivos do cartão do aluno](images/ch26fg10-small.png "Sobrepondo filhos de layout")](images/ch26fg10-large.png#lightbox "Sobrepondo filhos de layout")

### <a name="more-attached-bindable-properties"></a>Mais conectado propriedades vinculáveis

A classe [`CartesianLayout`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/CartesianLayout.cs) na biblioteca [**Xamarin. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) define as propriedades vinculáveis anexadas para especificar dois valores de `Point` e um valor de espessura e manipula os elementos `BoxView` para se assemelhar às linhas.

O exemplo [**UnitCube**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/UnitCube) usa isso para desenhar um cubo 3D.

### <a name="layout-and-layoutto"></a>Layout e LayoutTo

Um `Layout<T>` derivativo pode chamar `LayoutTo` em vez de `Layout` para animar o layout. A classe [`AnimatedCartesianLayout`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/AnimatedCartesianLayout.cs) faz isso e o exemplo [**AnimatedUnitCube**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/AnimatedUnitCube) demonstra isso.

## <a name="related-links"></a>Links relacionados

- [CAPÍTULO 26 texto completo (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch26-Apr2016.pdf)
- [Exemplos de capítulo 26](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26)
- [Criando layouts personalizados](~/xamarin-forms/user-interface/layouts/custom.md)
