---
title: "Resumo de capítulo 16. Associação de dados"
ms.topic: article
ms.prod: xamarin
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 11/07/2017
ms.openlocfilehash: 954d5d9e270db156f5ef2577706c667e05ab544c
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="summary-of-chapter-16-data-binding"></a>Resumo de capítulo 16. Associação de dados

Os programadores geralmente veem escrever manipuladores de eventos que detectam quando uma propriedade de um objeto foi alterado e usá-lo para alterar o valor de uma propriedade em outro objeto. Esse processo pode ser automatizado com a técnica de *associação de dados*. Associações de dados geralmente são definidas em XAML e se tornam parte da definição da interface do usuário.

Frequentemente, essas associações de dados se conectar a objetos de interface do usuário aos dados subjacentes. Essa é uma técnica que é explorada mais [ **capítulo 18. MVVM**](chapter18.md). No entanto, as associações de dados também podem se conectar a dois ou mais elementos de interface do usuário. A maioria dos exemplos antecipados de associação de dados neste capítulo demonstram essa técnica.

## <a name="binding-basics"></a>Noções básicas de associação

Várias propriedades, métodos e classes envolvidas na associação de dados:

- O [ `Binding` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Binding/) classe derivada de [ `BindingBase` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindingBase/) e encapsula muitas características de uma associação de dados
- O [ `BindingContext` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BindableObject.BindingContext/) propriedade é definida pelo [ `BindableObject` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableObject/) classe
- O [ `SetBinding` ](https://developer.xamarin.com/api/member/Xamarin.Forms.BindableObject.SetBinding/p/Xamarin.Forms.BindableProperty/Xamarin.Forms.BindingBase/) método também é definido pelo [ `BindableObject` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableObject/) classe
- O [ `BindableObjectExtensions` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableObjectExtensions/) classe define três adicionais `SetBinding` métodos

As seguintes classes oferecem suporte a extensões de marcação XAML para associações:

- [`BindingExtension`](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.BindingExtension/) oferece suporte a `Binding` extensão de marcação
- [`ReferenceExtension`](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.ReferenceExtension/) oferece suporte a `x:Reference` extensão de marcação

Duas interfaces envolvidas na associação de dados:

- [`INotifyPropertyChanged`](https://developer.xamarin.com/api/type/System.ComponentModel.INotifyPropertyChanged/) no `System.ComponentModel` namespace é para a implementação de notificação quando uma alteração de propriedade
- [`IValueConverter`](https://developer.xamarin.com/api/type/Xamarin.Forms.IValueConverter/) é usado para definir classes pequenos que converter valores de um tipo para outro em associações de dados

Uma associação de dados se conecta a duas propriedades do objeto do mesmo, ou (mais comumente) em dois objetos diferentes. Essas duas propriedades são denominadas de *fonte* e *destino*. Geralmente, uma alteração na propriedade de origem faz com que uma alteração ocorre na propriedade de destino, mas, às vezes, a direção é invertida. Independentemente:

- o *destino* propriedade deve ser feita por um [`BindableProperty`](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableProperty/)
- o *fonte* propriedade geralmente é um membro de uma classe que implementa [`INotifyPropertyChanged`](https://developer.xamarin.com/api/type/System.ComponentModel.INotifyPropertyChanged/)

Uma classe que implementa `INotifyPropertyChanged` dispara uma [ `PropertyChanged` ](https://developer.xamarin.com/api/event/System.ComponentModel.INotifyPropertyChanged.PropertyChanged/) evento quando uma propriedade de valor é alterado. `BindableObject` implementa `INotifyPropertyChanged` e dispara automaticamente um `PropertyChanged` evento quando uma propriedade com o apoio de um `BindableProperty` valores de alterações, mas você pode escrever suas próprias classes que implementam `INotifyPropertyChanged` sem derivando de `BindableObject`.

## <a name="code-and-xaml"></a>Código e XAML

O [ **OpacityBindingCode** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/OpacityBindingCode) demonstra como definir uma associação de dados no código:

- A fonte é o `Value` propriedade de um `Slider`
- O destino é o `Opacity` propriedade de um `Label`

Os dois objetos estão conectados, definindo o `BindingContext` do `Label` do objeto para o `Slider` objeto. As duas propriedades são conectadas por chamar um [ `SetBinding` ](https://developer.xamarin.com/api/member/Xamarin.Forms.BindableObjectExtensions.SetBinding/p/Xamarin.Forms.BindableObject/Xamarin.Forms.BindableProperty/System.String/) método de extensão no `Label` referenciando o `OpacityProperty` propriedade associável e o `Value` propriedade do `Slider` expresso como um cadeia de caracteres.

Manipulando o `Slider` , em seguida, faz com que o `Label` fade dentro e fora do modo de exibição.

O [ **OpacityBindingXaml** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/OpacityBindingXaml) é o mesmo programa com a associação de dados definido em XAML. O `BindingContext` do `Label` é definido como um `x:Reference` referência de extensão de marcação a `Slider`e o `Opacity` propriedade do `Label` é definido como o `Binding` extensão de marcação com seu [ `Path` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Binding.Path/) propriedade referenciando o `Value` propriedade o `Slider`.

## <a name="source-and-bindingcontext"></a>Origem e BindingContext

O [ **BindingSourceCode** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/BindingSourceCode) exemplo mostra uma abordagem alternativa no código. Um `Binding` objeto é criado definindo o [ `Source` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Binding.Source/) propriedade para o `Slider` objeto e o [ `Path` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Binding.Path/) a propriedade "Valor". O [ `SetBinding` ](https://developer.xamarin.com/api/member/Xamarin.Forms.BindableObject.SetBinding/p/Xamarin.Forms.BindableProperty/Xamarin.Forms.BindingBase/) método `BindableObject` é chamado no `Label` objeto.

O [ `Binding` construtor](https://developer.xamarin.com/api/constructor/Xamarin.Forms.Binding.Binding/p/System.String/Xamarin.Forms.BindingMode/Xamarin.Forms.IValueConverter/System.Object/System.String/System.Object/) também poderia ter sido usado para definir o `Binding` objeto.

O [ **BindingSourceXaml** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/BindingSourceXaml) exemplo mostra a técnica comparável em XAML. O `Opacity` propriedade do `Label` é definido como um `Binding` extensão de marcação com [ `Path` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Binding.Path/) definido como o `Value` propriedade e [ `Source` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Binding.Source/) definido como um incorporado `x:Reference` extensão de marcação.

Em resumo, há duas maneiras de fazer referência ao objeto de origem de associação:

- Por meio de `BindingContext` propriedade de destino
- Por meio de `Source` propriedade o `Binding` próprio objeto

Se ambos forem especificados, a segunda terá precedência. A vantagem do `BindingContext` é que ela é propagada por meio da árvore visual. Isso é *muito* útil se várias propriedades de destino estão associadas ao mesmo objeto de origem.

O [ **WebViewDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/WebViewDemo) programa demonstra essa técnica com o [ `WebView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.WebView/) elemento. Dois `Button` elementos para navegar para trás e frente herdam um `BindingContext` de seu pai que faz referência a `WebView`. O `IsEnabled` propriedades de dois botões tem simples `Binding` extensões de marcação que o botão de destino `IsEnabled` propriedades com base nas configurações do [ `CanGoBack` ](https://developer.xamarin.com/api/property/Xamarin.Forms.WebView.CanGoBack/) e [ `CanGoForward` ](https://developer.xamarin.com/api/property/Xamarin.Forms.WebView.CanGoForward/) propriedades somente leitura a `WebView`.

## <a name="the-binding-mode"></a>O modo de associação

Definir o [ `Mode` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BindingBase.Mode/) propriedade `Binding` a um membro do [ `BindingMode` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindingMode/) enumeração:

- [`OneWay`](https://developer.xamarin.com/api/field/Xamarin.Forms.BindingMode.OneWay/) para que as alterações na propriedade de origem afetam o destino
- [`OneWayToSource`](https://developer.xamarin.com/api/field/Xamarin.Forms.BindingMode.OneWayToSource/) para que as alterações na propriedade de destino afeta a fonte
- [`TwoWay`](https://developer.xamarin.com/api/field/Xamarin.Forms.BindingMode.TwoWay/) para que as alterações na origem e destino afetam uns aos outros
- [`Default`](https://developer.xamarin.com/api/field/Xamarin.Forms.BindingMode.Default/) Para usar o [ `DefaultBindingMode` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BindableProperty.DefaultBindingMode/) especificado quando o destino `BindableProperty` foi criado. Se nenhum for especificado, o padrão é `OneWay` para as propriedades vinculáveis normais, e `OneWayToSource` para as propriedades vinculáveis somente leitura.

Propriedades que devem ser os destinos de associações de dados em cenários MVVM geralmente têm um `DefaultBindingMode` de `TwoWay`. Elas são:

- `Value` propriedade de `Slider` e `Stepper`
- `IsToggled` propriedade de `Switch`
- `Text` propriedade de `Entry`, `Editor`, e `SearchBar`
- `Date` propriedade de `DatePicker`
- `Time` propriedade de `TimePicker`

O [ **BindingModes** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/BindingModes) demonstra os modos de quatro associação com uma associação de dados em que o destino é o `FontSize` propriedade de um `Label` e a origem é o `Value` propriedade de um `Slider`. Isso permite que cada `Slider` para controlar o tamanho da fonte do `Label`. Mas o `Slider` elementos não estão inicializados porque o `DefaultBindingMode` do `FontSize` é de propriedade `OneWay`.

O [ **ReverseBinding** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/ReverseBinding) exemplo define as associações a `Value` propriedade do `Slider` referenciando o `FontSize` propriedade de cada `Label`. Isso parece estar com versões anteriores, mas funciona melhor em initialzing o `Slider` elementos porque o `Value` propriedade o `Slider` tem um `DefaultBindingMode` de `TwoWay`.

[![Tripla captura de tela de associação inversa](images/ch16fg06-small.png "Inverter associação")](images/ch16fg06-large.png "Inverter associação")

Isso é semelhante a como as associações são definidas no modelo MVVM, e você usará esse tipo de associação com frequência.

## <a name="string-formatting"></a>Cadeia de caracteres de formatação

Quando a propriedade de destino é do tipo `string`, você pode usar o [ `StringFormat` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BindingBase.StringFormat/) propriedade definida pelo `BindingBase` para converter a origem para um `string`. Definir o `StringFormat` propriedade .NET formatação de cadeia de caracteres que você deseja usar com a estática [ `String.Format` ](https://developer.xamarin.com/api/member/System.String.Format/p/System.String/System.Object/) formato para exibir o objeto. Ao usar essa cadeia de caracteres de formatação dentro de uma extensão de marcação, coloque-o entre aspas simples para que as chaves não ser enganadas para uma extensão de marcação incorporado.

O [ **ShowViewValues** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/ShowViewValues) demonstra como usar `StringFormat` em XAML.

O [ **WhatSizeBindings** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/WhatSizeBindings) exemplo demonstra como exibir o tamanho da página com associações para o `Width` e `Height` propriedades da `ContentPage`.

## <a name="why-is-it-called-path"></a>Por que ele é chamado "Path"?

O [ `Path` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Binding.Path/) propriedade `Binding` é chamada assim porque é uma série de propriedades e indexadores separados por pontos. O [ **BindingPathDemos** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/BindingPathDemos) exemplo mostra vários exemplos.

## <a name="binding-value-converters"></a>Conversores de valor de associação

Quando as propriedades de origem e destino de uma associação de tipos diferentes, você pode converter entre os tipos de usando um conversor de associação. Esta é uma classe que implementa o [ `IValueConverter` ](https://developer.xamarin.com/api/type/Xamarin.Forms.IValueConverter/) de interface e contém dois métodos: [ `Convert` ](https://developer.xamarin.com/api/member/Xamarin.Forms.IValueConverter.Convert/p/System.Object/System.Type/System.Object/System.Globalization.CultureInfo/) para converter a origem para o destino, e [ `ConvertBack` ](https://developer.xamarin.com/api/member/Xamarin.Forms.IValueConverter.ConvertBack/p/System.Object/System.Type/System.Object/System.Globalization.CultureInfo/) Para converter o destino para a origem.

O [ `IntToBoolConverter` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/IntToBoolConverter.cs) classe no [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) biblioteca é um exemplo para converter um `int` para um `bool`. Ele é demonstrado pelo [ **ButtonEnabler** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/ButtonEnabler) exemplo, o que permite apenas o `Button` se pelo menos um caractere foi digitado em um `Entry`.

O [ `BoolToStringConverter` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BoolToStringConverter.cs) classe converte um `bool` para um `string` e define duas propriedades para especificar o texto que deve ser retornado para `false` e `true` valores.
O [ `BoolToColorConverter` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BoolToColorConverter.cs) é semelhante. O [ **SwitchText** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/SwitchText) exemplo demonstra como usar esses dois conversores para exibir diferentes textos em cores diferentes com base em um `Switch` configuração.

Genérica [ `BoolToObjectConverter` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BoolToObjectConverter.cs) pode substituir o `BoolToStringConverter` e `BoolToColorConverter` e servir como um generalizado `bool`-para-conversor de qualquer tipo de objeto.

## <a name="bindings-and-custom-views"></a>Associações e modos de exibição personalizados

Você pode simplificar controles personalizados usando associações de dados. O [ `NewCheckBox.cs` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NewCheckBox.xaml.cs) arquivo de código define `Text`, `TextColor`, `FontSize`, `FontAttributes`, e `IsChecked` propriedades, mas não tem lógica para os visuais do controle.
Em vez disso, o [ `NewCheckBox.cs.xaml` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NewCheckBox.xaml) arquivo contém todas as marcações para o Visual do controle por meio de ligações de dados no `Label` elementos com base nas propriedades definidas no arquivo code-behind.

O [ **NewCheckBoxDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/NewCheckBoxDemo) demonstra o `NewCheckBox` controle personalizado.



## <a name="related-links"></a>Links relacionados

- [Texto completo do capítulo 16 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch16-Apr2016.pdf)
- [Exemplos de capítulo 16](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16)
