---
title: Resumo do Capítulo 26. Layouts personalizados
description: 'Criando aplicativos móveis com o xamarin. Forms: Resumo do Capítulo 26. Layouts personalizados'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 2B7F4346-414E-49FF-97FB-B85E92D98A21
author: charlespetzold
ms.author: chape
ms.date: 11/07/2017
ms.openlocfilehash: 1c8fec34c0bc7f38d360f76122d851ae653ce15e
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/08/2018
ms.locfileid: "35241165"
---
# <a name="summary-of-chapter-26-custom-layouts"></a>Resumo do Capítulo 26. Layouts personalizados

Xamarin. Forms inclui várias classes derivadas de [ `Layout<View>` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Layout%3CT%3E/):

* `StackLayout`,
* `Grid`,
* `AbsoluteLayout`, e
* `RelativeLayout`.

Este capítulo descreve como criar suas próprias classes que derivam de `Layout<View>`.

## <a name="an-overview-of-layout"></a>Uma visão geral do layout

Há um sistema centralizado que manipula o layout do xamarin. Forms. Cada elemento é responsável por determinar o que deve ser seu próprio tamanho e como processamento em uma área específica.

### <a name="parents-and-children"></a>Os pais e filhos

Cada elemento que possui filhos é responsável para posicionar os filhos de si mesmo. É o pai que determina o tamanho de seus filhos deve ser baseado no tamanho tem disponível e o tamanho do filho deseja ser.

### <a name="sizing-and-positioning"></a>Redimensionar e posicionar

Layout começa na parte superior da árvore visual com a página e, em seguida, passa todas as ramificações. É o método mais importante público no layout [ `Layout` ](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.Layout/p/Xamarin.Forms.Rectangle/) definido pelo `VisualElement`. Cada elemento que é um pai para outras chamadas de elementos `Layout` para cada um dos seus filhos para dar o filho de um tamanho e posição em relação ao próprio na forma de um [ `Rectangle` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Rectangle/) valor. Essas `Layout` chamadas se propague através da árvore visual.

Uma chamada para `Layout` é necessário para um elemento seja exibido na tela e faz com que as seguintes propriedades somente leitura a ser definido. Eles são consistentes com o `Rectangle` passado para o método:

- [`Bounds`](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Bounds/) do tipo `Rectangle`
- [`X`](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.X/) do tipo `double`
- [`Y`](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Y/) do tipo `double`
- [`Width`](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Width/) do tipo `double`
- [`Height`](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Height/) do tipo `double`

Antes do `Layout` chamar, `Height` e `Width` têm valores fictícios de &ndash;1.

Uma chamada para `Layout` também irá disparar chamadas para os seguintes métodos protegidos:

- [`SizeAllocated`](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.SizeAllocated/p/System.Double/System.Double/), que chama
- [`OnSizeAllocated`](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.OnSizeAllocated/p/System.Double/System.Double/), que pode ser substituído.

Por fim, o seguinte evento é acionado:

- [`SizeChanged`](https://developer.xamarin.com/api/event/Xamarin.Forms.VisualElement.SizeChanged/)

O `OnSizeAllocated` método é substituído por `Page` e `Layout`, que são apenas duas classes no xamarin. Forms que podem ter filhos. As chamadas de método substituído

- [`UpdateChildrenLayout`](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.UpdateChildrenLayout()/) para `Page` derivados e [ `UpdateChildrenLayout` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Layout.UpdateChildrenLayout()/) para `Layout` derivados, que chama
- [`LayoutChildren`](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.LayoutChildren/p/System.Double/System.Double/System.Double/System.Double/) para `Page` derivados e [ `LayoutChildren` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Layout.LayoutChildren/p/System.Double/System.Double/System.Double/System.Double/) para `Layout` derivados.

`LayoutChildren` em seguida, chama `Layout` para cada um dos filhos do elemento. Se pelo menos um filho tem um novo `Bounds` configuração, em seguida, o seguinte evento é acionado:

- [`LayoutChanged`](https://developer.xamarin.com/api/event/Xamarin.Forms.Page.LayoutChanged/) para `Page` derivados e [ `LayoutChanged` ](https://developer.xamarin.com/api/event/Xamarin.Forms.Layout.LayoutChanged/) para `Layout` derivados

### <a name="constraints-and-size-requests"></a>Solicitações de tamanho e restrições

Para `LayoutChildren` inteligente chamar `Layout` em todos os seus filhos, ela deve saber um *preferencial* ou *desejado* tamanho para os filhos. Portanto, as chamadas para `Layout` para cada um dos filhos são geralmente precedido por chamadas para

- [`GetSizeRequest`](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.GetSizeRequest/p/System.Double/System.Double/)

Depois que o catálogo foi publicado, o `GetSizeRequest` método foi preterido e substituído por

- [`Measure`](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.Measure/p/System.Double/System.Double/Xamarin.Forms.MeasureFlags/)

O `Measure` método acomoda o [ `Margin` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.Margin/) propriedade e inclui um argumento de tipo [ `MeasureFlag` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MeasureFlags/), que tem dois membros:

- [`IncludeMargins`](https://developer.xamarin.com/api/field/Xamarin.Forms.MeasureFlags.IncludeMargins/)
- [`None`](https://developer.xamarin.com/api/field/Xamarin.Forms.MeasureFlags.None/) para não incluir as margens

Para muitos elementos, `GetSizeRequest` ou `Measure` obtém o tamanho nativo do elemento de seu processamento. Os dois métodos têm parâmetros de largura e altura *restrições*. Por exemplo, um `Label` usará a restrição de largura para determinar como encapsular várias linhas de texto.

Ambos `GetSizeRequest`e `Measure` retorna um valor do tipo [ `SizeRequest` ](https://developer.xamarin.com/api/type/Xamarin.Forms.SizeRequest/), que tem duas propriedades:

- [`Request`](https://developer.xamarin.com/api/property/Xamarin.Forms.SizeRequest.Request/) do tipo `Size`
- [`Minimum`](https://developer.xamarin.com/api/property/Xamarin.Forms.SizeRequest.Minimum/) do tipo `Size`

Com muita frequência, esses dois valores forem iguais e o `Minimum` valor pode ser geralmente ignorado.

`VisualElement` também define um método protegido semelhante ao `GetSizeRequest` que é chamado de `GetSizeRequest`:

- [`OnSizeRequest`](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.OnSizeRequest/p/System.Double/System.Double/) Retorna um `SizeRequest` valor

Agora, esse método é preterido e substituído por:

- [`OnMeasure`](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.OnMeasure/p/System.Double/System.Double/)

Cada classe que deriva de `Layout` ou `Layout<T>` deve substituir `OnSizeRequest` ou `OnMeasure`. É onde uma classe de layout determina seu próprio tamanho, que geralmente é baseado no tamanho de seus filhos, que obtém chamando `GetSizeRequest` ou `Measure` nos filhos. Antes e depois de chamar `OnSizeRequest` ou `OnMeasure`, `GetSizeRequest` ou `Measure` faz ajustes com base nas seguintes propriedades:

- [`WidthRequest`](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.WidthRequest/)tipo `double`, afeta o `Request` propriedade `SizeRequest`
- [`HeightRequest`](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.HeightRequest/) tipo `double`, afeta o `Request` propriedade `SizeRequest`
- [`MinimumWidthRequest`](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.MinimumWidthRequest/) tipo `double`, afeta o `Minimum` propriedade `SizeRequest`
- [`MinimumHeightRequest`](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.MinimumHeightRequest/) tipo `double`, afeta o `Minimum` propriedade `SizeRequest`

### <a name="infinite-constraints"></a>Restrições de infinitas

A restrição de argumentos passados para `GetSizeRequest` (ou `Measure`) e `OnSizeRequest` (ou `OnMeasure`) pode ser infinito (ou seja, os valores de `Double.PositiveInfinity`). No entanto, o `SizeRequest` retornado com esses métodos não podem conter dimensões infinitos.

Restrições de infinitas indicam que o tamanho solicitado deve refletir o tamanho do elemento natural. Vertical `StackLayout` chamadas `GetSizeRequest` (ou `Measure`) em seus filhos com uma restrição de altura infinito. Chama um layout horizontal pilha `GetSizeRequest` (ou `Measure`) em seus filhos com uma restrição de largura infinita. Um `AbsoluteLayout` chamadas `GetSizeRequest` (ou `Measure`) em seus filhos com restrições de largura e altura infinitos.

### <a name="peeking-inside-the-process"></a>Inspecionar dentro do processo

O [ **ExploreChildSize** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/ExploreChildSizes) exibe restrição e tamanho solicitam informações para um layout simples.

## <a name="deriving-from-layoutview"></a>Derivando de Layout<View>

Uma classe de layout personalizado derivado de `Layout<View>`. Ele tem duas responsabilidades:

- Substituir `OnMeasure` chamar `Measure` em filhos do todos os o layout. Retornar um tamanho solicitado para o layout em si
- Substituir `LayoutChildren` chamar `Layout` em filhos do todos os o layout

O `for` ou `foreach` loop nessas substituições deve ignorar qualquer filho cujo `IsVisible` está definida como `false`.

Uma chamada para `OnMeasure` não é garantida. `OnMeasure` não será chamado se o pai do layout é que controlam tamanho do layout (por exemplo, um layout que preenche uma página). Por esse motivo, `LayoutChildren` não pode confiar em tamanhos de filho obtidos durante a `OnMeasure` chamar. Frequentemente, `LayoutChildren` deve se chamar `Measure` em filhos do layout, ou você pode implementar algum tipo de tamanho do cache lógica (para ser abordada posteriormente).

### <a name="an-easy-example"></a>Um exemplo simples

O [ **VerticalStackDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/VerticalStackDemo) exemplo contém um simplificado [ `VerticalStack` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter26/VerticalStackDemo/VerticalStackDemo/VerticalStackDemo/VerticalStack.cs) classe e uma demonstração de uso.

### <a name="vertical-and-horizontal-positioning-simplified"></a>Vertical e horizontal de posicionamento simplificado

Um dos trabalhos que `VerticalStack` deve executar ocorre durante o `LayoutChildren` substituir. O método usa o filho `HorizontalOptions` propriedade para determinar como posicionar o filho dentro de slot no `VerticalStack`. Em vez disso, você pode chamar o método estático [ `Layout.LayoutChildIntoBoundingRect` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Layout.LayoutChildIntoBoundingRegion/p/Xamarin.Forms.VisualElement/Xamarin.Forms.Rectangle/). Este método chama `Measure` no filho e usa seu `HorizontalOptions` e `VerticalOptions` propriedades para posicionar o filho dentro do retângulo especificado.

### <a name="invalidation"></a>Invalidação

Geralmente, uma alteração na propriedade de um elemento afeta como esse elemento aparece no layout. O layout deve ser invalidado para disparar um novo layout.

`VisualElement` define um método protegido [ `InvalidateMeasure` ](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.InvalidateMeasure()/), que é geralmente chamado pelo manipulador de propriedade alterada de qualquer propriedade associável cuja alteração afeta o tamanho do elemento. O `InvalidateMeasure` método dispara uma [ `MeasureInvalidated` ](https://developer.xamarin.com/api/event/Xamarin.Forms.VisualElement.MeasureInvalidated/) eventos.

O `Layout` classe define um método protegido semelhante chamado [ `InvalidateLayout` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Layout.InvalidateLayout()/), que um `Layout` derivado deve chamar qualquer alteração que afeta a forma de posições e tamanhos de seus filhos.

### <a name="some-rules-for-coding-layouts"></a>Algumas regras para layouts de codificação

1. Propriedades definidas por `Layout<T>` derivados devem ser feitos por propriedades vinculáveis e os manipuladores de propriedade alterada devem chamar `InvalidateLayout`.

2. Um `Layout<T>` derivado que define as propriedades vinculáveis anexadas deve substituir [ `OnAdded` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Layout%3CT%3E.OnAdded/p/T/) para adicionar um manipulador de propriedade alterada para seus filhos e [ `OnRemoved` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Layout%3CT%3E.OnRemoved/p/T/) para remover manipulador. O manipulador deve procurar alterações nestas anexado propriedades vinculáveis e responder chamando `InvalidateLayout`.

3. Um `Layout<T>` derivada que implementa um cache de tamanhos de filho deve substituir `InvalidateLayout` e [ `OnChildMeasureInvalidated` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Layout.OnChildMeasureInvalidated()/) e limpar o cache quando esses métodos são chamados.

### <a name="a-layout-with-properties"></a>Um layout com propriedades

O [ `WrapLayout` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/WrapLayout.cs) classe no [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) assumirá que todos os seus filhos são o mesmo tamanho e ajusta os filhos de uma linha (ou coluna) para a próxima. Define uma `Orientation` propriedade como `StackLayout`, e `ColumnSpacing` e `RowSpacing` propriedades como `Grid`, e armazena em cache os tamanhos de filho.

O [ **PhotoWrap** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/PhotoWrap) exemplo coloca um `WrapLayout` em um `ScrollView` para a exibição de fotos em estoque.

### <a name="no-unconstrained-dimensions-allowed"></a>Não há dimensões irrestrita permitidos!

O [ `UniformGridLayout` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/UniformGridLayout.cs) no [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) biblioteca destina-se para exibir todos os seus filhos de si mesmo. Portanto, ele não pode lidar com dimensões irrestrita e gera uma exceção se um for encontrado.

O [ **PhotoGrid** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/PhotoGrid) demonstra `UniformGridLayout`:

[![Captura de tela tripla da grade de fotos](images/ch26fg08-small.png "Layout de grade uniforme")](images/ch26fg08-large.png#lightbox "uniforme Layout de grade")

### <a name="overlapping-children"></a>Sobreposição de filhos

Um `Layout<T>` derivado pode se sobrepor a seus filhos. No entanto, os filhos são processados na ordem no `Children` coleção e não a ordem na qual seus `Layout` métodos são chamados.

O `Layout` classe define dois métodos que permitem que você mova um filho dentro da coleção:

- [`LowerChild`](https://developer.xamarin.com/api/member/Xamarin.Forms.Layout.LowerChild/p/Xamarin.Forms.View/) Para mover um filho para o início da coleção
- [`RaiseChild`](https://developer.xamarin.com/api/member/Xamarin.Forms.Layout.RaiseChild/p/Xamarin.Forms.View/) Para mover um filho ao final da coleção

Para os filhos sobrepostos, os filhos no final da coleção visualmente aparecem na parte superior de filhos no início da coleção.

O [ `OverlapLayout` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/OverlapLayout.cs) classe no [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) biblioteca define uma propriedade anexada para indicar a ordem de processamento e assim permitir que um dos seus filhos para ser exibida na parte superior de outras pessoas. O [ **StudentCardFile** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/StudentCardFile) exemplo demonstra isso:

[![Tripla captura de tela da grade de arquivo de cartão de aluno](images/ch26fg10-small.png "sobreposição Layout filhos")](images/ch26fg10-large.png#lightbox "sobreposição filhos de Layout")

### <a name="more-attached-bindable-properties"></a>Mais anexado propriedades vinculáveis

O [ `CartesianLayout` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/CartesianLayout.cs) classe no [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) biblioteca define as propriedades vinculáveis anexadas para especificar duas `Point` valores e um valor da espessura e manipula `BoxView` elementos se pareça com linhas.

O [ **UnitCube** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/UnitCube) exemplo usa para desenhar um cubo 3D.

### <a name="layout-and-layoutto"></a>Layout e LayoutTo

Um `Layout<T>` derivada pode chamar `LayoutTo` em vez de `Layout` para animar o layout. O [ `AnimatedCartesianLayout` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/AnimatedCartesianLayout.cs) classe faz isso e o [ **AnimatedUnitCube** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/AnimatedUnitCube) demonstra a ele.



## <a name="related-links"></a>Links relacionados

- [Capítulo 26 de texto completo (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch26-Apr2016.pdf)
- [Exemplos de capítulo 26](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26)
- [Criar Layouts personalizados](~/xamarin-forms/user-interface/layouts/custom.md)
