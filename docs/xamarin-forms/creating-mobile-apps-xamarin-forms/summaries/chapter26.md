---
title: Resumo do Capítulo 26. Layouts personalizados
description: 'Criando aplicativos móveis com xamarin. Forms: Resumo do Capítulo 26. Layouts personalizados'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 2B7F4346-414E-49FF-97FB-B85E92D98A21
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2017
ms.openlocfilehash: 9fa9802f94e10612c4b0fe02c84ddcabc89820a8
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61331542"
---
# <a name="summary-of-chapter-26-custom-layouts"></a>Resumo do Capítulo 26. Layouts personalizados

[![Baixar exemplo](~/media/shared/download.png) baixar o exemplo](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26)

Xamarin. Forms inclui várias classes derivadas [ `Layout<View>` ](xref:Xamarin.Forms.Layout`1):

* `StackLayout`,
* `Grid`,
* `AbsoluteLayout`, e
* `RelativeLayout`.

Este capítulo descreve como criar suas próprias classes que derivam de `Layout<View>`.

## <a name="an-overview-of-layout"></a>Uma visão geral do layout

Não há nenhum sistema centralizado que manipula o layout do xamarin. Forms. Cada elemento é responsável por determinar qual deve ser seu próprio tamanho e como renderizar em si em uma área específica.

### <a name="parents-and-children"></a>Pais e filhos

Cada elemento que tem filhos é responsável por esses filhos dentro do próprio de posicionamento. É o pai que determina o tamanho de seus filhos deve ser baseado no tamanho tem disponível e o tamanho do filho quer ser.

### <a name="sizing-and-positioning"></a>Dimensionamento e posicionamento

Layout começa na parte superior da árvore visual com a página e, em seguida, continua por meio de todas as ramificações. É o método público mais importante no layout [ `Layout` ](xref:Xamarin.Forms.VisualElement.Layout(Xamarin.Forms.Rectangle)) definidos por `VisualElement`. Cada elemento que é pai de outras chamadas de elementos `Layout` para cada um dos seus filhos para dar o filho de um tamanho e posição em relação ao próprio na forma de um [ `Rectangle` ](xref:Xamarin.Forms.Rectangle) valor. Eles `Layout` chamadas propagam através da árvore visual.

Uma chamada para `Layout` é necessária para um elemento seja exibido na tela e faz com que as seguintes propriedades somente leitura a ser definido. Eles são consistentes com o `Rectangle` passado para o método:

- [`Bounds`](xref:Xamarin.Forms.VisualElement.Bounds) do tipo `Rectangle`
- [`X`](xref:Xamarin.Forms.VisualElement.X) do tipo `double`
- [`Y`](xref:Xamarin.Forms.VisualElement.Y) do tipo `double`
- [`Width`](xref:Xamarin.Forms.VisualElement.Width) do tipo `double`
- [`Height`](xref:Xamarin.Forms.VisualElement.Height) do tipo `double`

Antes do `Layout` chamar, `Height` e `Width` têm valores fictícios de &ndash;1.

Uma chamada para `Layout` também dispara chamadas para os seguintes métodos protegidos:

- [`SizeAllocated`](xref:Xamarin.Forms.VisualElement.SizeAllocated(System.Double,System.Double)), que chama
- [`OnSizeAllocated`](xref:Xamarin.Forms.VisualElement.OnSizeAllocated(System.Double,System.Double)), que pode ser substituído.

Por fim, o seguinte evento é acionado:

- [`SizeChanged`](xref:Xamarin.Forms.VisualElement.SizeChanged)

O `OnSizeAllocated` método é substituído por `Page` e `Layout`, que são apenas duas classes no xamarin. Forms que pode ter filhos. As chamadas de método substituído

- [`UpdateChildrenLayout`](xref:Xamarin.Forms.Page.UpdateChildrenLayout) para `Page` derivativos e [ `UpdateChildrenLayout` ](xref:Xamarin.Forms.Layout.UpdateChildrenLayout) para `Layout` derivativos, que chama
- [`LayoutChildren`](xref:Xamarin.Forms.Page.LayoutChildren(System.Double,System.Double,System.Double,System.Double)) para `Page` derivativos e [ `LayoutChildren` ](xref:Xamarin.Forms.Layout.LayoutChildren(System.Double,System.Double,System.Double,System.Double)) para `Layout` derivados.

`LayoutChildren` em seguida, chama `Layout` para cada um dos filhos do elemento. Se pelo menos um filho tem um novo `Bounds` definindo, em seguida, o seguinte evento é acionado:

- [`LayoutChanged`](xref:Xamarin.Forms.Page.LayoutChanged) para `Page` derivativos e [ `LayoutChanged` ](xref:Xamarin.Forms.Layout.LayoutChanged) para `Layout` derivados

### <a name="constraints-and-size-requests"></a>Solicitações de tamanho e restrições

Para `LayoutChildren` chamar inteligentemente `Layout` em todos os seus filhos, ela deve saber um *preferencial* ou *desejado* tamanho para os filhos. Portanto, as chamadas para `Layout` para cada um dos filhos são geralmente precedido por chamadas para

- [`GetSizeRequest`](xref:Xamarin.Forms.VisualElement.GetSizeRequest(System.Double,System.Double))

Depois que o livro foi publicado, o `GetSizeRequest` método foi preterido e substituído por

- [`Measure`](xref:Xamarin.Forms.VisualElement.Measure(System.Double,System.Double,Xamarin.Forms.MeasureFlags))

O `Measure` método acomoda a [ `Margin` ](xref:Xamarin.Forms.View.Margin) propriedade e inclui um argumento do tipo [ `MeasureFlag` ](xref:Xamarin.Forms.MeasureFlags), que tem dois membros:

- [`IncludeMargins`](xref:Xamarin.Forms.MeasureFlags.IncludeMargins)
- [`None`](xref:Xamarin.Forms.MeasureFlags.None) para não incluir as margens

Para vários elementos, `GetSizeRequest` ou `Measure` obtém o tamanho nativo do elemento de seu renderizador. Os dois métodos têm parâmetros de largura e altura *restrições*. Por exemplo, um `Label` usará a restrição de largura para determinar como encapsular várias linhas de texto.

Ambos `GetSizeRequest`e `Measure` retornam um valor do tipo [ `SizeRequest` ](xref:Xamarin.Forms.SizeRequest), que tem duas propriedades:

- [`Request`](xref:Xamarin.Forms.SizeRequest.Request) do tipo `Size`
- [`Minimum`](xref:Xamarin.Forms.SizeRequest.Minimum) do tipo `Size`

Com muita frequência, esses dois valores são iguais e o `Minimum` valor pode ser geralmente ignorado.

`VisualElement` também define um método protegido semelhante à `GetSizeRequest` que é chamado de `GetSizeRequest`:

- [`OnSizeRequest`](xref:Xamarin.Forms.VisualElement.OnSizeRequest(System.Double,System.Double)) Retorna um `SizeRequest` valor

Agora, esse método é preterido e substituído por:

- [`OnMeasure`](xref:Xamarin.Forms.VisualElement.OnMeasure(System.Double,System.Double))

Cada classe que deriva de `Layout` ou `Layout<T>` deve substituir `OnSizeRequest` ou `OnMeasure`. Isso é onde uma classe de layout determina seu próprio tamanho, o que geralmente se baseia no tamanho de seus filhos, que obtém chamando `GetSizeRequest` ou `Measure` nos filhos. Antes e depois de chamar `OnSizeRequest` ou `OnMeasure`, `GetSizeRequest` ou `Measure` faz ajustes com base nas seguintes propriedades:

- [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest)do tipo `double`, afeta a `Request` propriedade de `SizeRequest`
- [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) do tipo `double`, afeta a `Request` propriedade de `SizeRequest`
- [`MinimumWidthRequest`](xref:Xamarin.Forms.VisualElement.MinimumWidthRequest) do tipo `double`, afeta a `Minimum` propriedade de `SizeRequest`
- [`MinimumHeightRequest`](xref:Xamarin.Forms.VisualElement.MinimumHeightRequest) do tipo `double`, afeta a `Minimum` propriedade de `SizeRequest`

### <a name="infinite-constraints"></a>Restrições de infinitas

Os argumentos de restrição é passado para `GetSizeRequest` (ou `Measure`) e `OnSizeRequest` (ou `OnMeasure`) pode ser infinito (ou seja, os valores de `Double.PositiveInfinity`). No entanto, o `SizeRequest` retornado com esses métodos não podem conter dimensões infinitas.

Restrições de infinitas indicam que o tamanho solicitado deve refletir o tamanho do elemento natural. Vertical `StackLayout` chamadas `GetSizeRequest` (ou `Measure`) em seus filhos com uma restrição de altura infinito. Um layout horizontal de pilha de chamadas `GetSizeRequest` (ou `Measure`) em seus filhos com uma restrição de largura infinita. Uma `AbsoluteLayout` chamadas `GetSizeRequest` (ou `Measure`) em seus filhos com restrições de largura e altura infinitos.

### <a name="peeking-inside-the-process"></a>Espiar dentro do processo

O [ **ExploreChildSize** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/ExploreChildSizes) restrição exibe e tamanho solicitam informações para um layout simples.

## <a name="deriving-from-layoutview"></a>Derivando de Layout<View>

Uma classe de layout personalizado deriva `Layout<View>`. Ele tem duas responsabilidades:

- Substituir `OnMeasure` chamar `Measure` em filhos de todos do layout. Retornar um tamanho solicitado para o layout em si
- Substituir `LayoutChildren` chamar `Layout` em filhos de todos do layout

O `for` ou `foreach` loop nessas alternativas deve ignorar qualquer filho cujo `IsVisible` estiver definida como `false`.

Uma chamada para `OnMeasure` não é garantida. `OnMeasure` não será chamado se o pai do layout é que regem a tamanho do layout (por exemplo, um layout que preenche uma página). Por esse motivo, `LayoutChildren` não pode contar com tamanhos de filho obtidos durante a `OnMeasure` chamar. Com muita frequência `LayoutChildren` em si deve chamar `Measure` nos filhos do layout, ou você pode implementar algum tipo de tamanho do cache lógica (a ser discutido posteriormente).

### <a name="an-easy-example"></a>Um exemplo simples

O [ **VerticalStackDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/VerticalStackDemo) exemplo contém um simplificada [ `VerticalStack` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter26/VerticalStackDemo/VerticalStackDemo/VerticalStackDemo/VerticalStack.cs) classe e uma demonstração de seu uso.

### <a name="vertical-and-horizontal-positioning-simplified"></a>Vertical e horizontal de posicionamento simplificado

Um dos trabalhos que `VerticalStack` deve executar ocorre durante o `LayoutChildren` substituir. O método usa o filho `HorizontalOptions` propriedade para determinar como posicionar o filho dentro de seu slot no `VerticalStack`. Em vez disso, você pode chamar o método estático [ `Layout.LayoutChildIntoBoundingRect` ](xref:Xamarin.Forms.Layout.LayoutChildIntoBoundingRegion(Xamarin.Forms.VisualElement,Xamarin.Forms.Rectangle)). Este método chama `Measure` sobre o filho e usa seus `HorizontalOptions` e `VerticalOptions` propriedades para posicionar o filho dentro do retângulo especificado.

### <a name="invalidation"></a>Invalidação

Geralmente, uma alteração na propriedade de um elemento afeta como esse elemento aparece no layout. O layout deve ser invalidado para disparar um novo layout.

`VisualElement` define um método protegido [ `InvalidateMeasure` ](xref:Xamarin.Forms.VisualElement.InvalidateMeasure), que é geralmente chamado pelo manipulador de propriedade alterada de qualquer propriedade associável cuja alteração afeta o tamanho do elemento. O `InvalidateMeasure` método dispara uma [ `MeasureInvalidated` ](xref:Xamarin.Forms.VisualElement.MeasureInvalidated) eventos.

O `Layout` classe define um método protegido semelhante denominado [ `InvalidateLayout` ](xref:Xamarin.Forms.Layout.InvalidateLayout), que um `Layout` derivado deve chamar qualquer alteração que afeta como ele posiciona e dimensiona seus filhos.

### <a name="some-rules-for-coding-layouts"></a>Algumas regras para layouts de codificação

1. Propriedades definidas por `Layout<T>` derivativos devem ser feitos por propriedades vinculáveis, e os manipuladores de propriedade alterada devem chamar `InvalidateLayout`.

2. Um `Layout<T>` derivativo que define as propriedades vinculáveis anexadas deve substituir [ `OnAdded` ](xref:Xamarin.Forms.Layout`1.OnAdded*) para adicionar um manipulador de propriedade alterada para seus filhos e [ `OnRemoved` ](xref:Xamarin.Forms.Layout`1.OnRemoved*) para remover os manipulador. O manipulador deve verificar para alterações nesses anexados propriedades vinculáveis e responder ao chamar `InvalidateLayout`.

3. Um `Layout<T>` derivativo que implementa um cache de tamanhos de filho deve substituir `InvalidateLayout` e [ `OnChildMeasureInvalidated` ](xref:Xamarin.Forms.Layout.OnChildMeasureInvalidated) e limpar o cache quando esses métodos são chamados.

### <a name="a-layout-with-properties"></a>Um layout com propriedades

O [ `WrapLayout` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/WrapLayout.cs) classe os [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) pressupõe que todos os seus filhos são o mesmo tamanho e encapsula os filhos de uma linha (ou coluna) para a próxima. Ele define uma `Orientation` propriedade, como `StackLayout`, e `ColumnSpacing` e `RowSpacing` propriedades como `Grid`, e ele armazena em cache em tamanhos de filho.

O [ **PhotoWrap** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/PhotoWrap) exemplo coloca um `WrapLayout` em um `ScrollView` para a exibição de fotos em estoque.

### <a name="no-unconstrained-dimensions-allowed"></a>Nenhuma dimensão irrestrita permitido!

O [ `UniformGridLayout` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/UniformGridLayout.cs) no [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) biblioteca destina-se para exibir todos os seus filhos em si mesmo. Portanto, ele não pode lidar com dimensões irrestritos e gera uma exceção se um for encontrado.

O [ **PhotoGrid** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/PhotoGrid) demonstra `UniformGridLayout`:

[![Captura de tela tripla da grade de fotos](images/ch26fg08-small.png "uniforme de Layout de grade")](images/ch26fg08-large.png#lightbox "uniforme de Layout de grade")

### <a name="overlapping-children"></a>Sobreposição de filhos

Um `Layout<T>` derivativo pode se sobrepor a seus filhos. No entanto, os filhos são renderizados em sua ordem na `Children` coleta e não a ordem na qual seus `Layout` métodos são chamados.

O `Layout` classe define dois métodos que permitem que você mova um filho dentro da coleção:

- [`LowerChild`](xref:Xamarin.Forms.Layout.LowerChild(Xamarin.Forms.View)) Para mover um filho para o início da coleção
- [`RaiseChild`](xref:Xamarin.Forms.Layout.RaiseChild(Xamarin.Forms.View)) Para mover um filho ao final da coleção

Para os filhos de sobreposição, filhos no final da coleção visualmente aparecem na parte superior de filhos no início da coleção.

O [ `OverlapLayout` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/OverlapLayout.cs) classe o [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) biblioteca define uma propriedade anexada para indicar a ordem de renderização e, portanto, permite que um dos seus filhos para ser exibida na parte superior de outras pessoas. O [ **StudentCardFile** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/StudentCardFile) demonstra isso:

[![Tripla captura de tela de grade de arquivo do cartão de aluno](images/ch26fg10-small.png "sobrepostos Layout filhos")](images/ch26fg10-large.png#lightbox "sobreposição de filhos de Layout")

### <a name="more-attached-bindable-properties"></a>Mais conectado propriedades vinculáveis

O [ `CartesianLayout` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/CartesianLayout.cs) classe os [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) biblioteca define propriedades vinculáveis anexadas para especificar duas `Point` valores e um valor de espessura e manipula `BoxView` elementos seja semelhante das linhas.

O [ **UnitCube** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/UnitCube) exemplo utiliza para desenhar um cubo 3D.

### <a name="layout-and-layoutto"></a>Layout e LayoutTo

Um `Layout<T>` derivativo pode chamar `LayoutTo` em vez de `Layout` para animar o layout. O [ `AnimatedCartesianLayout` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/AnimatedCartesianLayout.cs) classe faz isso e o [ **AnimatedUnitCube** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/AnimatedUnitCube) exemplo demonstra isso.



## <a name="related-links"></a>Links relacionados

- [Capítulo 26 de texto completo (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch26-Apr2016.pdf)
- [Exemplos do Capítulo 26](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26)
- [Criar Layouts personalizados](~/xamarin-forms/user-interface/layouts/custom.md)
