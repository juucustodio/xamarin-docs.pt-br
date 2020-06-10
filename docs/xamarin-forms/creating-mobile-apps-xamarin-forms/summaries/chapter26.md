---
Título: "Resumo do capítulo 26. Layouts personalizados "Descrição:" Criando aplicativos móveis com Xamarin.Forms : Resumo do capítulo 26. Layouts personalizados "MS. Prod: xamarin MS. Technology: xamarin-Forms MS. AssetID: 2B7F4346-414E-49FF-97FB-B85E92D98A21 autor: davidbritch MS. Author: dabritch MS. Date: 11/07/2017 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="summary-of-chapter-26-custom-layouts"></a>Resumo do capítulo 26. Layouts personalizados

[![Baixar exemplo ](~/media/shared/download.png) baixar o exemplo](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26)

Xamarin.Formsinclui várias classes derivadas de [`Layout<View>`](xref:Xamarin.Forms.Layout`1) :

- `StackLayout`,
- `Grid`,
- `AbsoluteLayout`e
- `RelativeLayout`.

Este capítulo descreve como criar suas próprias classes que derivam de `Layout<View>` .

## <a name="an-overview-of-layout"></a>Uma visão geral do layout

Não há sistema centralizado que manipule Xamarin.Forms layout. Cada elemento é responsável por determinar o que seu próprio tamanho deve ser e como renderizar em uma área específica.

### <a name="parents-and-children"></a>Pais e filhos

Cada elemento que tem filhos é responsável por posicionar esses filhos dentro dele mesmo. É o pai que, por fim, determina qual tamanho seus filhos devem ter com base no tamanho disponível e o tamanho que o filho deseja ser.

### <a name="sizing-and-positioning"></a>Dimensionamento e posicionamento

O layout começa na parte superior da árvore visual com a página e, em seguida, prossegue em todas as ramificações. O método público mais importante no layout é [ `Layout` ] (xref: Xamarin.Forms . Visualelement. layout ( Xamarin.Forms . Rectangle)) definido por `VisualElement` . Cada elemento que é um pai para outros elementos chama `Layout` cada um de seus filhos para dar ao filho um tamanho e uma posição em relação a si mesmo na forma de um [`Rectangle`](xref:Xamarin.Forms.Rectangle) valor. Essas `Layout` chamadas se propagam por meio da árvore visual.

Uma chamada para `Layout` é necessária para que um elemento apareça na tela e faz com que as seguintes propriedades somente leitura sejam definidas. Eles são consistentes com o `Rectangle` passado para o método:

- [`Bounds`](xref:Xamarin.Forms.VisualElement.Bounds)do tipo`Rectangle`
- [`X`](xref:Xamarin.Forms.VisualElement.X)do tipo`double`
- [`Y`](xref:Xamarin.Forms.VisualElement.Y)do tipo`double`
- [`Width`](xref:Xamarin.Forms.VisualElement.Width)do tipo`double`
- [`Height`](xref:Xamarin.Forms.VisualElement.Height)do tipo`double`

Antes da `Layout` chamada, `Height` e `Width` têm valores fictícios de &ndash; 1.

Uma chamada para `Layout` também dispara chamadas para os seguintes métodos protegidos:

- [`SizeAllocated`](xref:Xamarin.Forms.VisualElement.SizeAllocated(System.Double,System.Double)), que chama
- [`OnSizeAllocated`](xref:Xamarin.Forms.VisualElement.OnSizeAllocated(System.Double,System.Double)), que pode ser substituído.

Por fim, o seguinte evento é acionado:

- [`SizeChanged`](xref:Xamarin.Forms.VisualElement.SizeChanged)

O `OnSizeAllocated` método é substituído por `Page` e `Layout` , que são as duas únicas classes no Xamarin.Forms que podem ter filhos. O método substituído chama

- [`UpdateChildrenLayout`](xref:Xamarin.Forms.Page.UpdateChildrenLayout)para `Page` derivativos e [`UpdateChildrenLayout`](xref:Xamarin.Forms.Layout.UpdateChildrenLayout) para `Layout` derivativos, que chama
- [`LayoutChildren`](xref:Xamarin.Forms.Page.LayoutChildren(System.Double,System.Double,System.Double,System.Double))para `Page` derivações e [`LayoutChildren`](xref:Xamarin.Forms.Layout.LayoutChildren(System.Double,System.Double,System.Double,System.Double)) para `Layout` derivações.

`LayoutChildren`em seguida, chama `Layout` para cada um dos filhos do elemento. Se pelo menos um filho tiver uma nova `Bounds` configuração, o seguinte evento será acionado:

- [`LayoutChanged`](xref:Xamarin.Forms.Page.LayoutChanged)para `Page` derivativos e [`LayoutChanged`](xref:Xamarin.Forms.Layout.LayoutChanged) para `Layout` derivativos

### <a name="constraints-and-size-requests"></a>Restrições e solicitações de tamanho

Para `LayoutChildren` chamar de forma inteligente `Layout` todos os seus filhos, ele deve saber um tamanho *preferencial* ou *desejado* para os filhos. Portanto, as chamadas para `Layout` para cada um dos filhos são geralmente precedidas por chamadas para

- [`GetSizeRequest`](xref:Xamarin.Forms.VisualElement.GetSizeRequest(System.Double,System.Double))

Depois que o livro foi publicado, o `GetSizeRequest` método foi preterido e substituído por

- [ `Measure` ] (xref: Xamarin.Forms . Visualelement. Measure (System. Double, System. Double, Xamarin.Forms . MeasureFlags))

O `Measure` método acomoda a [`Margin`](xref:Xamarin.Forms.View.Margin) propriedade e inclui um argumento do tipo [`MeasureFlag`](xref:Xamarin.Forms.MeasureFlags) , que tem dois membros:

- [`IncludeMargins`](xref:Xamarin.Forms.MeasureFlags.IncludeMargins)
- [`None`](xref:Xamarin.Forms.MeasureFlags.None)para não incluir margens

Para muitos elementos, `GetSizeRequest` ou `Measure` Obtém o tamanho nativo do elemento do seu renderizador. Ambos os métodos têm parâmetros para *restrições*de largura e altura. Por exemplo, um `Label` usará a restrição de largura para determinar como encapsular várias linhas de texto.

`GetSizeRequest`E `Measure` retornam um valor do tipo [`SizeRequest`](xref:Xamarin.Forms.SizeRequest) , que tem duas propriedades:

- [`Request`](xref:Xamarin.Forms.SizeRequest.Request)do tipo`Size`
- [`Minimum`](xref:Xamarin.Forms.SizeRequest.Minimum)do tipo`Size`

Geralmente, esses dois valores são os mesmos, e o `Minimum` valor geralmente pode ser ignorado.

`VisualElement`também define um método protegido semelhante ao `GetSizeRequest` que é chamado de `GetSizeRequest` :

- [`OnSizeRequest`](xref:Xamarin.Forms.VisualElement.OnSizeRequest(System.Double,System.Double))Retorna um `SizeRequest` valor

Esse método agora foi preterido e substituído por:

- [`OnMeasure`](xref:Xamarin.Forms.VisualElement.OnMeasure(System.Double,System.Double))

Todas as classes que derivam de `Layout` ou `Layout<T>` devem substituir `OnSizeRequest` ou `OnMeasure` . É aí que uma classe de layout determina seu próprio tamanho, que geralmente se baseia no tamanho de seus filhos, que é obtido chamando `GetSizeRequest` ou `Measure` nos filhos. Antes e depois de chamar `OnSizeRequest` ou `OnMeasure` , `GetSizeRequest` ou `Measure` faz ajustes com base nas seguintes propriedades:

- [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest)do tipo `double` , afeta a `Request` propriedade de`SizeRequest`
- [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest)do tipo `double` , afeta a `Request` propriedade de`SizeRequest`
- [`MinimumWidthRequest`](xref:Xamarin.Forms.VisualElement.MinimumWidthRequest)do tipo `double` , afeta a `Minimum` propriedade de`SizeRequest`
- [`MinimumHeightRequest`](xref:Xamarin.Forms.VisualElement.MinimumHeightRequest)do tipo `double` , afeta a `Minimum` propriedade de`SizeRequest`

### <a name="infinite-constraints"></a>Restrições infinitas

Os argumentos de restrição passados para `GetSizeRequest` (ou `Measure` ) e `OnSizeRequest` (ou `OnMeasure` ) podem ser infinitos (ou seja, valores de `Double.PositiveInfinity` ). No entanto, o `SizeRequest` retornado desses métodos não pode conter dimensões infinitas.

Restrições infinitas indicam que o tamanho solicitado deve refletir o tamanho natural do elemento. Uma `StackLayout` chamada vertical `GetSizeRequest` (ou `Measure` ) em seus filhos com uma restrição de altura infinita. Um layout de pilha horizontal chama `GetSizeRequest` (ou `Measure` ) em seus filhos com uma restrição de largura infinita. Uma `AbsoluteLayout` chamada `GetSizeRequest` (ou `Measure` ) em seus filhos com restrições de largura e altura infinitas.

### <a name="peeking-inside-the-process"></a>Exibindo dentro do processo

O [**ExploreChildSize**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/ExploreChildSizes) exibe informações de solicitação de restrição e de tamanho para um layout simples.

## <a name="deriving-from-layoutview"></a>Derivando do layout\<View>

Uma classe de layout personalizada é derivada de `Layout<View>` . Ele tem duas responsabilidades:

- Substituir `OnMeasure` para chamar `Measure` em todos os filhos do layout. Retornar um tamanho solicitado para o próprio layout
- Substituir `LayoutChildren` para chamar `Layout` em todos os filhos do layout

O `for` `foreach` loop ou nessas substituições deve ignorar qualquer filho cuja `IsVisible` Propriedade esteja definida como `false` .

Uma chamada para `OnMeasure` não é garantida. `OnMeasure`Não será chamado se o pai do layout estiver governando o tamanho do layout (por exemplo, um layout que preenche uma página). Por esse motivo, o `LayoutChildren` não pode depender de tamanhos filho obtidos durante a `OnMeasure` chamada. Com muita frequência, o `LayoutChildren` próprio deve chamar `Measure` os filhos do layout ou você pode implementar algum tipo de lógica de cache de tamanho (a ser discutido posteriormente).

### <a name="an-easy-example"></a>Um exemplo fácil

O exemplo [**VerticalStackDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/VerticalStackDemo) contém uma classe simplificada [`VerticalStack`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter26/VerticalStackDemo/VerticalStackDemo/VerticalStackDemo/VerticalStack.cs) e uma demonstração de seu uso.

### <a name="vertical-and-horizontal-positioning-simplified"></a>Posicionamento vertical e horizontal simplificado

Um dos trabalhos que `VerticalStack` devem ser executados ocorre durante a `LayoutChildren` substituição. O método usa a propriedade do filho `HorizontalOptions` para determinar como posicionar o filho dentro de seu slot no `VerticalStack` . Em vez disso, você pode chamar o método estático [ `Layout.LayoutChildIntoBoundingRect` ] (xref: Xamarin.Forms . Layout. LayoutChildIntoBoundingRegion ( Xamarin.Forms . Visualelement, Xamarin.Forms . Rectangle)). Esse método chama `Measure` o filho e usa suas `HorizontalOptions` Propriedades e `VerticalOptions` para posicionar o filho dentro do retângulo especificado.

### <a name="invalidation"></a>Invalidação

Muitas vezes, uma alteração na propriedade de um elemento afeta a forma como esse elemento aparece no layout. O layout deve ser invalidado para disparar um novo layout.

`VisualElement`define um método protegido [`InvalidateMeasure`](xref:Xamarin.Forms.VisualElement.InvalidateMeasure) , que geralmente é chamado pelo manipulador de propriedade alterada de qualquer propriedade ligável cuja alteração afeta o tamanho do elemento. O `InvalidateMeasure` método dispara um [`MeasureInvalidated`](xref:Xamarin.Forms.VisualElement.MeasureInvalidated) evento.

A `Layout` classe define um método protegido semelhante chamado [`InvalidateLayout`](xref:Xamarin.Forms.Layout.InvalidateLayout) , que um `Layout` derivado deve chamar para qualquer alteração que afete como ele posiciona e dimensiona seus filhos.

### <a name="some-rules-for-coding-layouts"></a>Algumas regras para layouts de codificação

1. As propriedades definidas por `Layout<T>` derivativos devem ser apoiadas por propriedades vinculáveis e os manipuladores de propriedade alterada devem chamar `InvalidateLayout` .

2. Um `Layout<T>` derivativo que define as propriedades vinculáveis anexadas deve substituir [`OnAdded`](xref:Xamarin.Forms.Layout`1.OnAdded*) para adicionar um manipulador de propriedade alterada a seus filhos e [`OnRemoved`](xref:Xamarin.Forms.Layout`1.OnRemoved*) remover esse manipulador. O manipulador deve verificar se há alterações nas propriedades vinculáveis anexadas e responder chamando `InvalidateLayout` .

3. Um `Layout<T>` derivativo que implementa um cache de tamanhos filho deve substituir `InvalidateLayout` e [`OnChildMeasureInvalidated`](xref:Xamarin.Forms.Layout.OnChildMeasureInvalidated) limpar o cache quando esses métodos são chamados.

### <a name="a-layout-with-properties"></a>Um layout com propriedades

A [`WrapLayout`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/WrapLayout.cs) classe no [**Xamarin. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) pressupõe que todos os seus filhos têm o mesmo tamanho e encapsula os filhos de uma linha (ou coluna) para a próxima. Ele define uma `Orientation` propriedade como `StackLayout` , e `ColumnSpacing` e `RowSpacing` Propriedades como e `Grid` armazena em cache os tamanhos filho.

O exemplo de foto [**Wrap**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/PhotoWrap) coloca um `WrapLayout` em um `ScrollView` para exibir fotos de estoque.

### <a name="no-unconstrained-dimensions-allowed"></a>Nenhuma dimensão irrestrita é permitida!

O [`UniformGridLayout`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/UniformGridLayout.cs) na biblioteca [**Xamarin. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) destina-se a exibir todos os seus filhos em si. Portanto, ele não pode lidar com dimensões irrestrita e gera uma exceção se uma for encontrada.

O exemplo de [**fotograde**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/PhotoGrid) demonstra `UniformGridLayout` :

[![Captura de tela tripla da grade de fotos](images/ch26fg08-small.png "Layout de grade uniforme")](images/ch26fg08-large.png#lightbox "Layout de grade uniforme")

### <a name="overlapping-children"></a>Sobrepondo filhos

Um `Layout<T>` derivativo pode sobrepor seus filhos. No entanto, os filhos são renderizados em sua ordem na `Children` coleção e não na ordem em que seus `Layout` métodos são chamados.

A `Layout` classe define dois métodos que permitem mover um filho dentro da coleção:

- [ `LowerChild` ] (xref: Xamarin.Forms . Layout. LowerChild ( Xamarin.Forms . View)) para mover um filho para o início da coleção
- [ `RaiseChild` ] (xref: Xamarin.Forms . Layout. RaiseChild ( Xamarin.Forms . View)) para mover um filho para o final da coleção

Para filhos sobrepostos, os filhos no final da coleção aparecem visualmente na parte superior dos filhos no início da coleção.

A [`OverlapLayout`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/OverlapLayout.cs) classe na biblioteca [**Xamarin. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) define uma propriedade anexada para indicar a ordem de renderização e, portanto, permitir que um de seus filhos seja exibido na parte superior das outras. O exemplo [**StudentCardFile**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/StudentCardFile) demonstra isso:

[![Captura de tela tripla da grade de arquivos do cartão do aluno](images/ch26fg10-small.png "Sobrepondo filhos de layout")](images/ch26fg10-large.png#lightbox "Sobrepondo filhos de layout")

### <a name="more-attached-bindable-properties"></a>Mais propriedades vinculáveis anexadas

A [`CartesianLayout`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/CartesianLayout.cs) classe na biblioteca [**Xamarin. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) define as propriedades vinculáveis anexadas para especificar dois `Point` valores e um valor de espessura e manipula `BoxView` elementos para se assemelhar a linhas.

O exemplo [**UnitCube**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/UnitCube) usa isso para desenhar um cubo 3D.

### <a name="layout-and-layoutto"></a>Layout e Layoutto

Um `Layout<T>` derivativo pode chamar `LayoutTo` em vez de `Layout` animar o layout. A [`AnimatedCartesianLayout`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/AnimatedCartesianLayout.cs) classe faz isso e o exemplo [**AnimatedUnitCube**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/AnimatedUnitCube) demonstra isso.

## <a name="related-links"></a>Links relacionados

- [CAPÍTULO 26 texto completo (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch26-Apr2016.pdf)
- [Exemplos de capítulo 26](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26)
- [Criando layouts personalizados](~/xamarin-forms/user-interface/layouts/custom.md)
