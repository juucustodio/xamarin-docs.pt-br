---
title: Resumo do Capítulo 26. Layouts personalizados
description: 'Criando aplicativos móveis com Xamarin.Forms: Resumo do Capítulo 26. Layouts personalizados'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 2B7F4346-414E-49FF-97FB-B85E92D98A21
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2017
ms.openlocfilehash: 1eb5153f8ab295696e373f4fdb65a4f8820a05bc
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "70770935"
---
# <a name="summary-of-chapter-26-custom-layouts"></a>Resumo do Capítulo 26. Layouts personalizados

[![Baixar](~/media/shared/download.png) amostra Baixar a amostra](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26)

Xamarin.Forms inclui várias classes [`Layout<View>`](xref:Xamarin.Forms.Layout`1)derivadas de :

- `StackLayout`,
- `Grid`,
- `AbsoluteLayout`E
- `RelativeLayout`.

Este capítulo descreve como criar suas próprias classes que derivam de `Layout<View>`.

## <a name="an-overview-of-layout"></a>Uma visão geral do layout

Não há um sistema centralizado que lida com o layout Xamarin.Forms. Cada elemento é responsável por determinar qual deve ser seu próprio tamanho e como se render dentro de uma determinada área.

### <a name="parents-and-children"></a>Pais e filhos

Cada elemento que tem filhos é responsável por posicionar essas crianças dentro de si. É o pai que, em última análise, determina o tamanho de seus filhos devem ser baseados no tamanho que ele tem disponível e no tamanho que a criança quer ser.

### <a name="sizing-and-positioning"></a>Dimensionamento e posicionamento

O layout começa no topo da árvore visual com a página e, em seguida, prossegue através de todos os ramos. O método público mais [`Layout`](xref:Xamarin.Forms.VisualElement.Layout(Xamarin.Forms.Rectangle)) importante `VisualElement`no layout é definido por . Cada elemento que é pai `Layout` de outros elementos exige que cada um de seus filhos [`Rectangle`](xref:Xamarin.Forms.Rectangle) dê à criança um tamanho e posição em relação a si mesmo na forma de um valor. Essas `Layout` chamadas se propagam através da árvore visual.

Uma chamada `Layout` para é necessária para que um elemento apareça na tela e faz com que as seguintes propriedades somente de leitura sejam definidas. Eles são consistentes com o `Rectangle` passado para o método:

- [`Bounds`](xref:Xamarin.Forms.VisualElement.Bounds)de tipo`Rectangle`
- [`X`](xref:Xamarin.Forms.VisualElement.X)de tipo`double`
- [`Y`](xref:Xamarin.Forms.VisualElement.Y)de tipo`double`
- [`Width`](xref:Xamarin.Forms.VisualElement.Width)de tipo`double`
- [`Height`](xref:Xamarin.Forms.VisualElement.Height)de tipo`double`

Antes da `Layout` `Height` chamada, `Width` e ter &ndash;valores simulados de 1.

Uma chamada `Layout` para também aciona chamadas para os seguintes métodos protegidos:

- [`SizeAllocated`](xref:Xamarin.Forms.VisualElement.SizeAllocated(System.Double,System.Double)), que chama
- [`OnSizeAllocated`](xref:Xamarin.Forms.VisualElement.OnSizeAllocated(System.Double,System.Double)), que pode ser substituído.

Finalmente, o seguinte evento é disparado:

- [`SizeChanged`](xref:Xamarin.Forms.VisualElement.SizeChanged)

O `OnSizeAllocated` método é substituído `Page` `Layout`por e , que são as duas únicas classes em Xamarin.Forms que podem ter filhos. O método substituído chama

- [`UpdateChildrenLayout`](xref:Xamarin.Forms.Page.UpdateChildrenLayout)para `Page` derivativos e [`UpdateChildrenLayout`](xref:Xamarin.Forms.Layout.UpdateChildrenLayout) derivativos, `Layout` o que chama
- [`LayoutChildren`](xref:Xamarin.Forms.Page.LayoutChildren(System.Double,System.Double,System.Double,System.Double))para `Page` derivativos e [`LayoutChildren`](xref:Xamarin.Forms.Layout.LayoutChildren(System.Double,System.Double,System.Double,System.Double)) derivativos. `Layout`

`LayoutChildren`em `Layout` seguida, chama para cada um dos filhos do elemento. Se pelo menos uma `Bounds` criança tiver uma nova configuração, então o seguinte evento será acionado:

- [`LayoutChanged`](xref:Xamarin.Forms.Page.LayoutChanged)para `Page` derivativos e [`LayoutChanged`](xref:Xamarin.Forms.Layout.LayoutChanged) derivativos `Layout`

### <a name="constraints-and-size-requests"></a>Restrições e solicitações de tamanho

Para `LayoutChildren` chamar `Layout` inteligentemente todos os seus filhos, ele deve saber um tamanho *preferido* ou *desejado* para as crianças. Portanto, as `Layout` chamadas para cada uma das crianças são geralmente precedidos por chamadas para

- [`GetSizeRequest`](xref:Xamarin.Forms.VisualElement.GetSizeRequest(System.Double,System.Double))

Depois que o livro `GetSizeRequest` foi publicado, o método foi preterido e substituído por

- [`Measure`](xref:Xamarin.Forms.VisualElement.Measure(System.Double,System.Double,Xamarin.Forms.MeasureFlags))

O `Measure` método acomoda [`Margin`](xref:Xamarin.Forms.View.Margin) a propriedade e inclui [`MeasureFlag`](xref:Xamarin.Forms.MeasureFlags)um argumento de tipo, que tem dois membros:

- [`IncludeMargins`](xref:Xamarin.Forms.MeasureFlags.IncludeMargins)
- [`None`](xref:Xamarin.Forms.MeasureFlags.None)para não incluir margens

Para muitos `GetSizeRequest` elementos, ou `Measure` obtém o tamanho nativo do elemento a partir de seu renderizador. Ambos os métodos possuem parâmetros para *restrições de*largura e altura. Por exemplo, `Label` um usará a restrição de largura para determinar como envolver várias linhas de texto.

Ambos `GetSizeRequest` `Measure` e retornar um [`SizeRequest`](xref:Xamarin.Forms.SizeRequest)valor de tipo, que tem duas propriedades:

- [`Request`](xref:Xamarin.Forms.SizeRequest.Request)de tipo`Size`
- [`Minimum`](xref:Xamarin.Forms.SizeRequest.Minimum)de tipo`Size`

Muitas vezes esses dois valores `Minimum` são os mesmos, e o valor geralmente pode ser ignorado.

`VisualElement`também define um método `GetSizeRequest` protegido semelhante `GetSizeRequest`ao que é chamado de :

- [`OnSizeRequest`](xref:Xamarin.Forms.VisualElement.OnSizeRequest(System.Double,System.Double))retorna `SizeRequest` um valor

Esse método é agora preterido e substituído por:

- [`OnMeasure`](xref:Xamarin.Forms.VisualElement.OnMeasure(System.Double,System.Double))

Todas as classes `Layout` que `Layout<T>` derivam `OnSizeRequest` `OnMeasure`ou devem substituir ou . É aí que uma classe de layout determina seu próprio tamanho, que geralmente é baseado `GetSizeRequest` `Measure` no tamanho de seus filhos, que obtém chamando ou sobre as crianças. Antes e `OnSizeRequest` depois `OnMeasure` `GetSizeRequest` de `Measure` ligar ou fazer ajustes com base nas seguintes propriedades:

- [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest)do `double`tipo, afeta `Request` a propriedade de`SizeRequest`
- [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest)do `double`tipo, afeta `Request` a propriedade de`SizeRequest`
- [`MinimumWidthRequest`](xref:Xamarin.Forms.VisualElement.MinimumWidthRequest)do `double`tipo, afeta `Minimum` a propriedade de`SizeRequest`
- [`MinimumHeightRequest`](xref:Xamarin.Forms.VisualElement.MinimumHeightRequest)do `double`tipo, afeta `Minimum` a propriedade de`SizeRequest`

### <a name="infinite-constraints"></a>Restrições infinitas

Os argumentos de `GetSizeRequest` restrição `Measure`passados para (ou ) e `OnSizeRequest` (ou `OnMeasure` `Double.PositiveInfinity`) podem ser infinitos (ou seja, valores de ). No entanto, o `SizeRequest` retorno desses métodos não pode conter dimensões infinitas.

Restrições infinitas indicam que o tamanho solicitado deve refletir o tamanho natural do elemento. Uma `StackLayout` chamada `GetSizeRequest` vertical `Measure`(ou ) em seus filhos com uma restrição de altura infinita. Um layout de `GetSizeRequest` pilha `Measure`horizontal chama (ou ) em suas crianças com uma restrição de largura infinita. Uma `AbsoluteLayout` `GetSizeRequest` chamada `Measure`(ou ) em suas crianças com restrições infinitas de largura e altura.

### <a name="peeking-inside-the-process"></a>Espiando dentro do processo

O [**ExploreChildSize**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/ExploreChildSizes) exibe informações de solicitação de restrição e tamanho para um layout simples.

## <a name="deriving-from-layoutview"></a>Derivada do\<layout view>

Uma classe de layout `Layout<View>`personalizada deriva de . Tem duas responsabilidades:

- Anular `OnMeasure` para `Measure` chamar todas as crianças do layout. Retorne um tamanho solicitado para o layout em si
- Anular `LayoutChildren` para `Layout` chamar todas as crianças do layout

O `for` `foreach` loop nessas substituições deve pular `IsVisible` qualquer criança `false`cuja propriedade está definida para .

Uma chamada `OnMeasure` não é garantida. `OnMeasure`não será chamado se o pai do layout estiver regendo o tamanho do layout (por exemplo, um layout que preenche uma página). Por essa `LayoutChildren` razão, não pode contar com `OnMeasure` tamanhos de crianças obtidos durante a chamada. Muitas vezes, `LayoutChildren` deve `Measure` chamar os filhos do layout, ou você pode implementar algum tipo de lógica de cache de tamanho (a ser discutido mais tarde).

### <a name="an-easy-example"></a>Um exemplo fácil

A amostra [**VerticalStackDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/VerticalStackDemo) contém [`VerticalStack`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter26/VerticalStackDemo/VerticalStackDemo/VerticalStackDemo/VerticalStack.cs) uma classe simplificada e uma demonstração de seu uso.

### <a name="vertical-and-horizontal-positioning-simplified"></a>Posicionamento vertical e horizontal simplificado

Um dos trabalhos que `VerticalStack` devem `LayoutChildren` ser realizados ocorre durante a substituição. O método usa a `HorizontalOptions` propriedade da criança para determinar como posicionar `VerticalStack`a criança dentro de seu slot no . Em vez disso, [`Layout.LayoutChildIntoBoundingRect`](xref:Xamarin.Forms.Layout.LayoutChildIntoBoundingRegion(Xamarin.Forms.VisualElement,Xamarin.Forms.Rectangle))você pode chamar o método estático . Este método `Measure` chama a criança `HorizontalOptions` `VerticalOptions` e usa suas propriedades para posicionar a criança dentro do retângulo especificado.

### <a name="invalidation"></a>Invalidação

Muitas vezes, uma mudança na propriedade de um elemento afeta a forma como esse elemento aparece no layout. O layout deve ser invalidado para ativar um novo layout.

`VisualElement`define um método [`InvalidateMeasure`](xref:Xamarin.Forms.VisualElement.InvalidateMeasure)protegido , que é geralmente chamado pelo manipulador alterado de propriedade de qualquer propriedade vinculável cuja alteração afeta o tamanho do elemento. O `InvalidateMeasure` método [`MeasureInvalidated`](xref:Xamarin.Forms.VisualElement.MeasureInvalidated) dispara um evento.

A `Layout` classe define um método [`InvalidateLayout`](xref:Xamarin.Forms.Layout.InvalidateLayout)protegido `Layout` semelhante chamado , que um derivativo deve exigir qualquer mudança que afete a forma como posiciona e dimensiona seus filhos.

### <a name="some-rules-for-coding-layouts"></a>Algumas regras para layouts de codificação

1. As propriedades `Layout<T>` definidas por derivativos devem ser apoiadas por propriedades `InvalidateLayout`vinculáveis, e os manipuladores alterados por propriedade devem chamar .

2. Um `Layout<T>` derivativo que define propriedades vinculáveis [`OnAdded`](xref:Xamarin.Forms.Layout`1.OnAdded*) anexadas deve substituir para adicionar [`OnRemoved`](xref:Xamarin.Forms.Layout`1.OnRemoved*) um manipulador alterado por propriedade aos seus filhos e remover esse manipulador. O manipulador deve verificar se há alterações nessas `InvalidateLayout`propriedades vinculadas anexadas e responder ligando .

3. Um `Layout<T>` derivativo que implementa um cache `InvalidateLayout` de [`OnChildMeasureInvalidated`](xref:Xamarin.Forms.Layout.OnChildMeasureInvalidated) tamanhos de crianças deve substituir e limpar o cache quando esses métodos são chamados.

### <a name="a-layout-with-properties"></a>Um layout com propriedades

A [`WrapLayout`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/WrapLayout.cs) classe no [**Xamarin.FormsBook.Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) assume que todos os seus filhos são do mesmo tamanho, e envolve as crianças de uma linha (ou coluna) para a próxima. Ele define `Orientation` uma `StackLayout`propriedade `ColumnSpacing` como `RowSpacing` , `Grid`e propriedades como , e armazena tamanhos de crianças.

A amostra [**PhotoWrap**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/PhotoWrap) coloca um `WrapLayout` em um `ScrollView` para exibir fotos de estoque.

### <a name="no-unconstrained-dimensions-allowed"></a>Não são permitidas dimensões sem restrições!

A [`UniformGridLayout`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/UniformGridLayout.cs) biblioteca [**Xamarin.FormsBook.Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) destina-se a exibir todos os seus filhos dentro de si. Portanto, não pode lidar com dimensões sem restrições e abre uma exceção se for encontrado.

A amostra do `UniformGridLayout` [**PhotoGrid**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/PhotoGrid) demonstra:

[![Captura de tela tripla de Photo Grid](images/ch26fg08-small.png "Layout da grade uniforme")](images/ch26fg08-large.png#lightbox "Layout da grade uniforme")

### <a name="overlapping-children"></a>Crianças sobrepostas

Uma `Layout<T>` derivada pode sobrepor seus filhos. No entanto, as crianças são `Children` prestadas em sua ordem `Layout` na coleta, e não a ordem em que seus métodos são chamados.

A `Layout` classe define dois métodos que permitem mover uma criança dentro da coleção:

- [`LowerChild`](xref:Xamarin.Forms.Layout.LowerChild(Xamarin.Forms.View))para mover uma criança para o início da coleção
- [`RaiseChild`](xref:Xamarin.Forms.Layout.RaiseChild(Xamarin.Forms.View))para mover uma criança para o final da coleção

Para crianças sobrepostas, as crianças no final da coleção aparecem visualmente em cima das crianças no início da coleção.

A [`OverlapLayout`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/OverlapLayout.cs) classe na biblioteca [**Xamarin.FormsBook.Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) define uma propriedade anexada para indicar a ordem de renderização e, assim, permitir que um de seus filhos seja exibido em cima dos outros. A amostra [**StudentCardFile**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/StudentCardFile) demonstra isso:

[![Captura de tela tripla da grade de arquivos do cartão de estudante](images/ch26fg10-small.png "Crianças de layout sobrepostos")](images/ch26fg10-large.png#lightbox "Crianças de layout sobrepostos")

### <a name="more-attached-bindable-properties"></a>Propriedades mais anexadas

A [`CartesianLayout`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/CartesianLayout.cs) classe na biblioteca [**Xamarin.FormsBook.Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) define propriedades vinculáveis `Point` anexadas para especificar `BoxView` dois valores e um valor de espessura e manipula elementos para se assemelhar a linhas.

A amostra [**UnitCube**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/UnitCube) usa isso para desenhar um cubo 3D.

### <a name="layout-and-layoutto"></a>Layout e LayoutTo

Uma `Layout<T>` derivada `LayoutTo` pode `Layout` chamar ao invés de animar o layout. A [`AnimatedCartesianLayout`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/AnimatedCartesianLayout.cs) classe faz isso, e a amostra [**AnimatedUnitCube**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/AnimatedUnitCube) demonstra isso.

## <a name="related-links"></a>Links relacionados

- [Capítulo 26 texto completo (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch26-Apr2016.pdf)
- [Capítulo 26 amostras](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26)
- [Criando layouts personalizados](~/xamarin-forms/user-interface/layouts/custom.md)
