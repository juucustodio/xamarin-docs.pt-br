---
title: Resumo do capítulo 16. Associação de dados
description: 'Criação de aplicativos móveis com xamarin. Forms: Resumo do capítulo 16. Associação de dados'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: ED997DB0-C229-4868-A5FB-928703B377D6
author: charlespetzold
ms.author: chape
ms.date: 11/07/2017
ms.openlocfilehash: c4ad067778203759a54ed8141db0b82602e40f6c
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/12/2018
ms.locfileid: "38997446"
---
# <a name="summary-of-chapter-16-data-binding"></a>Resumo do capítulo 16. Associação de dados

Os programadores muitas vezes descobrem escrever manipuladores de eventos que detectam quando uma propriedade de um objeto foi alterado e usá-lo para alterar o valor de uma propriedade em outro objeto. Esse processo pode ser automatizado com a técnica de *vinculação de dados*. Associações de dados geralmente são definidas em XAML e se tornam parte da definição da interface do usuário.

Com muita frequência, essas associações de dados se conectar a objetos de interface do usuário aos dados subjacentes. Essa é uma técnica que é explorada mais nos [ **capítulo 18. MVVM**](chapter18.md). No entanto, as associações de dados também podem se conectar a dois ou mais elementos de interface do usuário. A maioria dos exemplos antecipadas de vinculação de dados neste capítulo demonstra essa técnica.

## <a name="binding-basics"></a>Noções básicas de vinculação

Várias propriedades, métodos e classes envolvidas na associação de dados:

- O [ `Binding` ](xref:Xamarin.Forms.Binding) classe deriva [ `BindingBase` ](xref:Xamarin.Forms.BindingBase) e encapsula muitas características de uma associação de dados
- O [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext) propriedade é definida pelo [ `BindableObject` ](xref:Xamarin.Forms.BindableObject) classe
- O [ `SetBinding` ](xref:Xamarin.Forms.BindableObject.SetBinding(Xamarin.Forms.BindableProperty,Xamarin.Forms.BindingBase)) método também é definido pela [ `BindableObject` ](xref:Xamarin.Forms.BindableObject) classe
- O [ `BindableObjectExtensions` ](xref:Xamarin.Forms.BindableObjectExtensions) classe define três adicionais `SetBinding` métodos

As duas classes a seguir dão suporte a extensões de marcação XAML para associações:

- [`BindingExtension`](xref:Xamarin.Forms.Xaml.BindingExtension) oferece suporte a `Binding` extensão de marcação
- [`ReferenceExtension`](xref:Xamarin.Forms.Xaml.ReferenceExtension) oferece suporte a `x:Reference` extensão de marcação

Duas interfaces envolvidas na associação de dados:

- [`INotifyPropertyChanged`](xref:System.ComponentModel.INotifyPropertyChanged) no `System.ComponentModel` namespace é para a implementação de notificação quando uma propriedade é alterada
- [`IValueConverter`](xref:Xamarin.Forms.IValueConverter) é usado para definir classes pequenas que convertem valores de um tipo para outro em associações de dados

Uma associação de dados se conecta a duas propriedades do mesmo objeto ou (mais comumente) em dois objetos diferentes. Essas duas propriedades são conhecidas como o *fonte* e o *destino*. Em geral, uma alteração na propriedade de origem faz com que uma alteração ocorra na propriedade de destino, mas, às vezes, a direção é invertida. Independentemente disso:

- o *destino* propriedade deve ser suportada por um [`BindableProperty`](xref:Xamarin.Forms.BindableProperty)
- o *origem* propriedade geralmente é um membro de uma classe que implementa [`INotifyPropertyChanged`](xref:System.ComponentModel.INotifyPropertyChanged)

Uma classe que implementa `INotifyPropertyChanged` dispara uma [ `PropertyChanged` ](xref:System.ComponentModel.INotifyPropertyChanged.PropertyChanged) evento quando uma propriedade de valor é alterado. `BindableObject` implementa `INotifyPropertyChanged` e dispara automaticamente uma `PropertyChanged` evento quando uma propriedade apoiado por um `BindableProperty` valores de alterações, mas você pode escrever suas próprias classes que implementam `INotifyPropertyChanged` sem derivar da `BindableObject`.

## <a name="code-and-xaml"></a>Código e XAML

O [ **OpacityBindingCode** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/OpacityBindingCode) que demonstra como definir uma associação de dados no código:

- A fonte é o `Value` propriedade de um `Slider`
- O destino é o `Opacity` propriedade de um `Label`

Os dois objetos estão conectados, definindo o `BindingContext` do `Label` do objeto para o `Slider` objeto. As duas propriedades são conectadas por meio da chamada um [ `SetBinding` ](xref:Xamarin.Forms.BindableObjectExtensions.SetBinding*) método de extensão no `Label` fazendo referência a `OpacityProperty` propriedade associável e o `Value` propriedade do `Slider` expresso como um cadeia de caracteres.

Manipulando o `Slider` , em seguida, faz com que o `Label` para esmaecer e sair do modo de exibição.

O [ **OpacityBindingXaml** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/OpacityBindingXaml) é o mesmo programa com a vinculação de dados definida em XAML. O `BindingContext` do `Label` é definido como um `x:Reference` referência de extensão de marcação a `Slider`e o `Opacity` propriedade do `Label` é definido como o `Binding` extensão de marcação com seus [ `Path` ](xref:Xamarin.Forms.Binding.Path) propriedade fazendo referência a `Value` propriedade do `Slider`.

## <a name="source-and-bindingcontext"></a>Código-fonte e BindingContext

O [ **BindingSourceCode** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/BindingSourceCode) exemplo mostra uma abordagem alternativa no código. Um `Binding` objeto é criado, definindo o [ `Source` ](xref:Xamarin.Forms.Binding.Source) propriedade para o `Slider` objeto e o [ `Path` ](xref:Xamarin.Forms.Binding.Path) a propriedade "Valor". O [ `SetBinding` ](xref:Xamarin.Forms.BindableObject.SetBinding(Xamarin.Forms.BindableProperty,Xamarin.Forms.BindingBase)) método `BindableObject` é chamado no `Label` objeto.

O [ `Binding` construtor](xref:Xamarin.Forms.Binding.%23ctor(System.String,Xamarin.Forms.BindingMode,Xamarin.Forms.IValueConverter,System.Object,System.String,System.Object)) também poderia ter sido usado para definir o `Binding` objeto.

O [ **BindingSourceXaml** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/BindingSourceXaml) exemplo mostra a técnica comparável no XAML. O `Opacity` propriedade do `Label` é definido como um `Binding` extensão de marcação com [ `Path` ](xref:Xamarin.Forms.Binding.Path) definido como o `Value` propriedade e [ `Source` ](xref:Xamarin.Forms.Binding.Source) definido como um Embedded `x:Reference` extensão de marcação.

Em resumo, há duas maneiras de fazer referência ao objeto de origem de associação:

- Por meio de `BindingContext` propriedade de destino
- Por meio de `Source` propriedade do `Binding` próprio objeto

Se ambos forem especificados, o segundo tem precedência. A vantagem do `BindingContext` é que ela é propagada por meio da árvore visual. Isso é *muito* útil se várias propriedades de destino são associadas ao mesmo objeto de origem.

O [ **WebViewDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/WebViewDemo) programa demonstra essa técnica com o [ `WebView` ](xref:Xamarin.Forms.WebView) elemento. Duas `Button` elementos para navegar para trás e encaminhará herdam uma `BindingContext` de seu pai que faz referência a `WebView`. O `IsEnabled` , em seguida, as propriedades dos dois botões têm simples `Binding` extensões de marcação que direcionam o botão `IsEnabled` propriedades com base nas configurações dos [ `CanGoBack` ](xref:Xamarin.Forms.WebView.CanGoBack) e [ `CanGoForward` ](xref:Xamarin.Forms.WebView.CanGoForward) propriedades somente leitura do `WebView`.

## <a name="the-binding-mode"></a>O modo de ligação

Defina a [ `Mode` ](xref:Xamarin.Forms.BindingBase.Mode) propriedade do `Binding` a um membro da [ `BindingMode` ](xref:Xamarin.Forms.BindingMode) enumeração:

- [`OneWay`](xref:Xamarin.Forms.BindingMode.OneWay) para que as alterações na propriedade de origem afetam o destino
- [`OneWayToSource`](xref:Xamarin.Forms.BindingMode.OneWayToSource) para que as alterações na propriedade de destino afeta a fonte
- [`TwoWay`](xref:Xamarin.Forms.BindingMode.TwoWay) para que as alterações na origem e destino afetam uns aos outros
- [`Default`](xref:Xamarin.Forms.BindingMode.Default) Para usar o [ `DefaultBindingMode` ](xref:Xamarin.Forms.BindableProperty.DefaultBindingMode) especificado quando o destino `BindableProperty` foi criado. Se nenhum for especificado, o padrão será `OneWay` para propriedades vinculáveis normais, e `OneWayToSource` para propriedades vinculáveis somente leitura.

As propriedades que devem ser os destinos das vinculações de dados em cenários do MVVM geralmente têm uma `DefaultBindingMode` de `TwoWay`. Elas são:

- `Value` propriedade de `Slider` e `Stepper`
- `IsToggled` propriedade de `Switch`
- `Text` propriedade de `Entry`, `Editor`, e `SearchBar`
- `Date` propriedade de `DatePicker`
- `Time` propriedade de `TimePicker`

O [ **BindingModes** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/BindingModes) exemplo demonstra os modos de quatro ligação com uma associação de dados em que o destino é o `FontSize` propriedade de um `Label` e a origem é o `Value` propriedade de um `Slider`. Isso permite que cada `Slider` para controlar o tamanho da fonte de correspondente `Label`. Mas o `Slider` elementos não são inicializados, porque o `DefaultBindingMode` da `FontSize` é de propriedade `OneWay`.

O [ **ReverseBinding** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/ReverseBinding) exemplo define as associações no `Value` propriedade do `Slider` referenciando o `FontSize` propriedade de cada `Label`. Isso parece ser com versões anteriores, mas ele funciona melhor em initialzing a `Slider` elementos porque o `Value` propriedade da `Slider` tem um `DefaultBindingMode` de `TwoWay`.

[![Tripla captura de tela da associação inversa](images/ch16fg06-small.png "associação inversa")](images/ch16fg06-large.png#lightbox "reversa de associação")

Isso é análogo ao como associações são definidas no MVVM, e você usará esse tipo de associação com frequência.

## <a name="string-formatting"></a>Cadeia de caracteres de formatação

Quando a propriedade de destino é do tipo `string`, você pode usar o [ `StringFormat` ](xref:Xamarin.Forms.BindingBase.StringFormat) propriedade definida pelo `BindingBase` para converter a fonte para um `string`. Defina as `StringFormat` propriedade para um .NET formatação de cadeia de caracteres que você deseja usar com a estática [ `String.Format` ](xref:System.String.Format(System.String,System.Object)) formato para exibir o objeto. Ao usar essa cadeia de caracteres de formatação dentro de uma extensão de marcação, coloque-o entre aspas simples para que as chaves não ser seja confundidas com uma extensão de marcação incorporado.

O [ **ShowViewValues** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/ShowViewValues) exemplo demonstra como usar `StringFormat` em XAML.

O [ **WhatSizeBindings** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/WhatSizeBindings) exemplo demonstra como exibir o tamanho da página com associações para o `Width` e `Height` propriedades do `ContentPage`.

## <a name="why-is-it-called-path"></a>Por que ele é chamado "Caminho"?

O [ `Path` ](xref:Xamarin.Forms.Binding.Path) propriedade `Binding` é chamada assim porque ela pode ser uma série de propriedades e indexadores, separados por pontos. O [ **BindingPathDemos** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/BindingPathDemos) exemplo mostra vários exemplos.

## <a name="binding-value-converters"></a>Conversores de valor de associação

Quando as propriedades de origem e destino de uma associação de tipos diferentes, você pode converter entre os tipos que usam um conversor de associação. Essa é uma classe que implementa o [ `IValueConverter` ](xref:Xamarin.Forms.IValueConverter) interface e contém dois métodos: [ `Convert` ](xref:Xamarin.Forms.IValueConverter.Convert(System.Object,System.Type,System.Object,System.Globalization.CultureInfo)) para converter a fonte para o destino, e [ `ConvertBack` ](xref:Xamarin.Forms.IValueConverter.ConvertBack(System.Object,System.Type,System.Object,System.Globalization.CultureInfo)) Para converter o destino para a origem.

O [ `IntToBoolConverter` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/IntToBoolConverter.cs) classe o [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) biblioteca é um exemplo para a conversão de um `int` para um `bool`. Demonstrado pelo [ **ButtonEnabler** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/ButtonEnabler) sample, que permite apenas que o `Button` se pelo menos um caractere tenha sido digitado em um `Entry`.

O [ `BoolToStringConverter` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BoolToStringConverter.cs) classe converte um `bool` para um `string` e define duas propriedades para especificar que o texto deve ser retornado para `false` e `true` valores.
O [ `BoolToColorConverter` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BoolToColorConverter.cs) é semelhante. O [ **SwitchText** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/SwitchText) exemplo demonstra como usar esses dois conversores para exibir textos diferentes em cores diferentes com base em um `Switch` configuração.

Genérica [ `BoolToObjectConverter` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BoolToObjectConverter.cs) pode substituir o `BoolToStringConverter` e `BoolToColorConverter` e servir como um generalizado `bool`-para-conversor de objeto de qualquer tipo.

## <a name="bindings-and-custom-views"></a>Associações e modos de exibição personalizados

Você pode simplificar a controles personalizados usando associações de dados. O [ `NewCheckBox.cs` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NewCheckBox.xaml.cs) arquivo de código define `Text`, `TextColor`, `FontSize`, `FontAttributes`, e `IsChecked` propriedades, mas não tem lógica para os elementos visuais do controle.
Em vez disso, o [ `NewCheckBox.cs.xaml` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NewCheckBox.xaml) arquivo contém toda a marcação para elementos visuais do controle por meio de ligações de dados sobre o `Label` elementos com base nas propriedades definidas no arquivo code-behind.

O [ **NewCheckBoxDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/NewCheckBoxDemo) exemplo demonstra o `NewCheckBox` controle personalizado.



## <a name="related-links"></a>Links relacionados

- [Capítulo 16 de texto completo (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch16-Apr2016.pdf)
- [Exemplos do capítulo 16](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16)
