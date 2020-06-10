---
Título: "Resumo do capítulo 16. Vinculação de dados "Descrição:" Criando aplicativos móveis com Xamarin.Forms : Resumo do capítulo 16. Vinculação de dados "MS. Prod: xamarin MS. Technology: xamarin-Forms MS. AssetID: ED997DB0-C229-4868-A5FB-928703B377D6 autor: davidbritch MS. Author: dabritch MS. Date: 07/18/2018 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="summary-of-chapter-16-data-binding"></a>Resumo do capítulo 16. Associação de dados

[![Baixar exemplo ](~/media/shared/download.png) baixar o exemplo](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16)

> [!NOTE] 
> As observações nesta página indicam áreas em que Xamarin.Forms houve uma divergência do material apresentado no livro.

Os programadores geralmente se encontram escrevendo manipuladores de eventos que detectam quando uma propriedade de um objeto foi alterada e o usam para alterar o valor de uma propriedade em outro objeto. Esse processo pode ser automatizado com a técnica de *vinculação de dados*. As associações de dados geralmente são definidas em XAML e se tornam parte da definição da interface do usuário.

Com muita frequência, essas vinculações de dados conectam objetos da interface do usuário a dados subjacentes. Essa é uma técnica que é explorada mais no [**capítulo 18. MVVM**](chapter18.md). No entanto, as associações de dados também podem conectar dois ou mais elementos da interface do usuário. A maioria dos exemplos iniciais de vinculação de dados neste capítulo demonstra essa técnica.

## <a name="binding-basics"></a>Noções básicas de associação

Várias propriedades, métodos e classes estão envolvidos na vinculação de dados:

- A [`Binding`](xref:Xamarin.Forms.Binding) classe deriva de [`BindingBase`](xref:Xamarin.Forms.BindingBase) e encapsula muitas características de uma associação de dados
- A [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) propriedade é definida pela [`BindableObject`](xref:Xamarin.Forms.BindableObject) classe
- O [ `SetBinding` ] (xref: Xamarin.Forms . Acopláble. SetBinding ( Xamarin.Forms . Vinculproperty, Xamarin.Forms . BindingBase)) também é definido pela [`BindableObject`](xref:Xamarin.Forms.BindableObject) classe
- A [`BindableObjectExtensions`](xref:Xamarin.Forms.BindableObjectExtensions) classe define três `SetBinding` métodos adicionais

As duas classes a seguir oferecem suporte a extensões de marcação XAML para associações:

- [`BindingExtension`](xref:Xamarin.Forms.Xaml.BindingExtension)dá suporte à `Binding` extensão de marcação
- [`ReferenceExtension`](xref:Xamarin.Forms.Xaml.ReferenceExtension)dá suporte à `x:Reference` extensão de marcação

Duas interfaces estão envolvidas na ligação de dados:

- [`INotifyPropertyChanged`](xref:System.ComponentModel.INotifyPropertyChanged)no `System.ComponentModel` namespace é para implementar a notificação quando uma propriedade é alterada
- [`IValueConverter`](xref:Xamarin.Forms.IValueConverter)é usado para definir classes pequenas que convertem valores de um tipo para outro em associações de dados

Uma ligação de dados conecta duas propriedades do mesmo objeto ou (mais comumente) dois objetos diferentes. Essas duas propriedades são referidas como a *origem* e o *destino*. Em geral, uma alteração na propriedade Source faz com que uma alteração ocorra na Propriedade Target, mas às vezes a direção é invertida. Considerar

- a propriedade de *destino* deve ser apoiada por um[`BindableProperty`](xref:Xamarin.Forms.BindableProperty)
- a propriedade *Source* geralmente é um membro de uma classe que implementa[`INotifyPropertyChanged`](xref:System.ComponentModel.INotifyPropertyChanged)

Uma classe que implementa `INotifyPropertyChanged` dispara um [`PropertyChanged`](xref:System.ComponentModel.INotifyPropertyChanged.PropertyChanged) evento quando uma propriedade muda de valor. `BindableObject`implementa `INotifyPropertyChanged` e dispara automaticamente um `PropertyChanged` evento quando uma propriedade apoiada por um `BindableProperty` valor de alterações, mas você pode escrever suas próprias classes que implementam `INotifyPropertyChanged` sem derivar de `BindableObject` .

## <a name="code-and-xaml"></a>Código e XAML

O exemplo [**OpacityBindingCode**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/OpacityBindingCode) demonstra como definir uma associação de dados no código:

- A origem é a `Value` propriedade de um`Slider`
- O destino é a `Opacity` propriedade de um`Label`

Os dois objetos são conectados definindo o `BindingContext` do `Label` objeto para o `Slider` objeto. As duas propriedades são conectadas chamando um [`SetBinding`](xref:Xamarin.Forms.BindableObjectExtensions.SetBinding*) método de extensão no `Label` referenciando a `OpacityProperty` propriedade ligável e a `Value` propriedade de `Slider` expressa como uma cadeia de caracteres.

Manipular o `Slider` , então, faz com que o `Label` apareça e fique fora da exibição.

O [**OpacityBindingXaml**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/OpacityBindingXaml) é o mesmo programa com a associação de dados definida em XAML. O `BindingContext` de `Label` é definido como uma `x:Reference` extensão de marcação referenciando `Slider` , e a `Opacity` propriedade de `Label` é definida como a extensão de `Binding` marcação com sua [`Path`](xref:Xamarin.Forms.Binding.Path) propriedade que faz referência à `Value` Propriedade do `Slider` .

## <a name="source-and-bindingcontext"></a>Origem e BindingContext

O exemplo [**BindingSourceCode**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/BindingSourceCode) mostra uma abordagem alternativa no código. Um `Binding` objeto é criado definindo a [`Source`](xref:Xamarin.Forms.Binding.Source) propriedade para o `Slider` objeto e a [`Path`](xref:Xamarin.Forms.Binding.Path) propriedade como "value". O [ `SetBinding` ] (xref: Xamarin.Forms . Acopláble. SetBinding ( Xamarin.Forms . Vinculproperty, Xamarin.Forms . BindingBase)), o método de `BindableObject` é chamado no `Label` objeto.

O [ `Binding` constructor] (xref: Xamarin.Forms . Associação .% 23ctor (System. String, Xamarin.Forms . BindingMode, Xamarin.Forms . IValueConverter, System. Object, System. String, System. Object) também pode ter sido usado para definir o `Binding` objeto.

O exemplo [**BindingSourceXaml**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/BindingSourceXaml) mostra a técnica comparável em XAML. A `Opacity` propriedade de `Label` é definida como uma `Binding` extensão de marcação com [`Path`](xref:Xamarin.Forms.Binding.Path) definida como a `Value` propriedade e [`Source`](xref:Xamarin.Forms.Binding.Source) definida como uma extensão de marcação inserida `x:Reference` .

Em resumo, há duas maneiras de fazer referência ao objeto de origem da associação:

- Por meio da `BindingContext` Propriedade do destino
- Por meio da `Source` Propriedade do `Binding` próprio objeto

Se ambos forem especificados, o segundo terá precedência. A vantagem do `BindingContext` é que ele é propagado por meio da árvore visual. Isso é *muito* útil se várias propriedades de destino estiverem associadas ao mesmo objeto de origem.

O programa [**WebViewDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/WebViewDemo) demonstra essa técnica com o [`WebView`](xref:Xamarin.Forms.WebView) elemento. Dois `Button` elementos para navegar para a frente e para trás herdam um `BindingContext` de seu pai que faz referência a `WebView` . As `IsEnabled` Propriedades dos dois botões têm `Binding` extensões de marcação simples direcionadas às propriedades do botão `IsEnabled` com base nas configurações das [`CanGoBack`](xref:Xamarin.Forms.WebView.CanGoBack) Propriedades e [`CanGoForward`](xref:Xamarin.Forms.WebView.CanGoForward) somente leitura do `WebView` .

## <a name="the-binding-mode"></a>O modo de associação

Defina a [`Mode`](xref:Xamarin.Forms.BindingBase.Mode) propriedade de `Binding` como um membro da [`BindingMode`](xref:Xamarin.Forms.BindingMode) enumeração:

- [`OneWay`](xref:Xamarin.Forms.BindingMode.OneWay)para que as alterações na propriedade Source afetem o destino
- [`OneWayToSource`](xref:Xamarin.Forms.BindingMode.OneWayToSource)para que as alterações na propriedade de destino afetem a origem
- [`TwoWay`](xref:Xamarin.Forms.BindingMode.TwoWay)para que as alterações na origem e no destino afetem umas às outras
- [`Default`](xref:Xamarin.Forms.BindingMode.Default)para usar o [`DefaultBindingMode`](xref:Xamarin.Forms.BindableProperty.DefaultBindingMode) especificado quando o destino `BindableProperty` foi criado. Se nenhum tiver sido especificado, o padrão será `OneWay` para propriedades vinculáveis normais e `OneWayToSource` para propriedades vinculáveis somente leitura.

> [!NOTE]
> A `BindingMode` Enumeração agora também inclui `OnTime` apenas para aplicar uma associação quando o contexto de associação é alterado e não quando a propriedade de origem é alterada.

As propriedades que provavelmente são os destinos de associações de dados em cenários MVVM geralmente têm um `DefaultBindingMode` de `TwoWay` . Estes são:

- Propriedade `Value` de `Slider` e `Stepper`
- Propriedade `IsToggled` de `Switch`
- `Text`Propriedade de `Entry` , `Editor` , e`SearchBar`
- Propriedade `Date` de `DatePicker`
- Propriedade `Time` de `TimePicker`

O exemplo [**BindingModes**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/BindingModes) demonstra os quatro modos de ligação com uma vinculação de dados em que o destino é a `FontSize` propriedade de a `Label` e a origem é a `Value` propriedade de um `Slider` . Isso permite que cada `Slider` controle o tamanho da fonte do correspondente `Label` . Mas os `Slider` elementos não são inicializados porque o `DefaultBindingMode` da `FontSize` propriedade é `OneWay` .

O exemplo [**Reversebinding**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/ReverseBinding) define as associações na `Value` Propriedade do que `Slider` faz referência à `FontSize` propriedade de cada uma `Label` . Isso parece ser retroativa, mas funciona melhor em initialzing os `Slider` elementos porque a `Value` propriedade de `Slider` tem um `DefaultBindingMode` de `TwoWay` .

[![Captura de tela tripla de associação inversa](images/ch16fg06-small.png "Associação inversa")](images/ch16fg06-large.png#lightbox "Associação inversa")

Isso é análogo a como as associações são definidas no MVVM e você usará esse tipo de associação com frequência.

## <a name="string-formatting"></a>Formatação de cadeia de caracteres

Quando a propriedade de destino for do tipo `string` , você poderá usar a [`StringFormat`](xref:Xamarin.Forms.BindingBase.StringFormat) propriedade definida por `BindingBase` para converter a origem em um `string` . Defina a `StringFormat` propriedade para uma cadeia de caracteres de formatação .NET que você usaria com o [`String.Format`](xref:System.String.Format(System.String,System.Object)) formato estático para exibir o objeto. Ao usar essa cadeia de caracteres de formatação em uma extensão de marcação, coloque-a entre aspas simples para que as chaves não sejam confundidas para uma extensão de marcação inserida.

O exemplo [**ShowViewValues**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/ShowViewValues) demonstra como usar `StringFormat` em XAML.

O exemplo [**WhatSizeBindings**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/WhatSizeBindings) demonstra como exibir o tamanho da página com associações às `Width` `Height` Propriedades e do `ContentPage` .

## <a name="why-is-it-called-path"></a>Por que ele é chamado de "Path"?

A [`Path`](xref:Xamarin.Forms.Binding.Path) propriedade de `Binding` é tão chamada porque pode ser uma série de propriedades e indexadores separados por pontos. O exemplo de [**BindingPathDemos**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/BindingPathDemos) mostra vários exemplos.

## <a name="binding-value-converters"></a>Conversores de valor de associação

Quando as propriedades de origem e destino de uma associação são de tipos diferentes, você pode converter entre os tipos usando um conversor de associação. Essa é uma classe que implementa a [`IValueConverter`](xref:Xamarin.Forms.IValueConverter) interface e contém dois métodos: [`Convert`](xref:Xamarin.Forms.IValueConverter.Convert(System.Object,System.Type,System.Object,System.Globalization.CultureInfo)) para converter a origem no destino e [`ConvertBack`](xref:Xamarin.Forms.IValueConverter.ConvertBack(System.Object,System.Type,System.Object,System.Globalization.CultureInfo)) para converter o destino para a origem.

A [`IntToBoolConverter`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/IntToBoolConverter.cs) classe na biblioteca [**Xamarin. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) é um exemplo para converter um `int` para um `bool` . Ele é demonstrado pelo exemplo [**ButtonEnabler**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/ButtonEnabler) , que habilita apenas a `Button` digitação de pelo menos um caractere em um `Entry` .

A [`BoolToStringConverter`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BoolToStringConverter.cs) classe converte um `bool` em a `string` e define duas propriedades para especificar a qual texto deve ser retornado `false` e `true` valores.
O [`BoolToColorConverter`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BoolToColorConverter.cs) é semelhante. O exemplo [**SwitchText**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/SwitchText) demonstra como usar esses dois conversores para exibir textos diferentes em cores diferentes com base em uma `Switch` configuração.

O genérico [`BoolToObjectConverter`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BoolToObjectConverter.cs) pode substituir `BoolToStringConverter` e `BoolToColorConverter` e servir como um conversor generalizado `bool` para objeto de qualquer tipo.

## <a name="bindings-and-custom-views"></a>Associações e modos de exibição personalizados

Você pode simplificar os controles personalizados usando associações de dados. O [`NewCheckBox.cs`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NewCheckBox.xaml.cs) arquivo de código define as `Text` `TextColor` Propriedades,, `FontSize` , `FontAttributes` e `IsChecked` , mas não tem nenhuma lógica para os elementos visuais do controle.
Em vez disso, o [`NewCheckBox.cs.xaml`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NewCheckBox.xaml) arquivo contém toda a marcação para os visuais do controle por meio de associações de dados nos `Label` elementos com base nas propriedades definidas no arquivo code-behind.

O exemplo [**NewCheckBoxDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/NewCheckBoxDemo) demonstra o `NewCheckBox` controle personalizado.

## <a name="related-links"></a>Links relacionados

- [Capítulo 16 texto completo (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch16-Apr2016.pdf)
- [Capítulo 16 amostras](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16)
- [Associação de dados](~/xamarin-forms/app-fundamentals/data-binding/index.md)
