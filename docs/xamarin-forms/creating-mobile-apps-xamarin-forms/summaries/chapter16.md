---
title: Resumo do capítulo 16. Associação de dados
description: 'Criação de aplicativos móveis com xamarin. Forms: Resumo do capítulo 16. Associação de dados'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: ED997DB0-C229-4868-A5FB-928703B377D6
author: davidbritch
ms.author: dabritch
ms.date: 07/18/2018
ms.openlocfilehash: 2d61413fb1d8c28a3957da53601d0ad682f35518
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2020
ms.locfileid: "70771097"
---
# <a name="summary-of-chapter-16-data-binding"></a>Resumo do capítulo 16. Associação de dados

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16)

> [!NOTE] 
> Observações sobre essa página indicam áreas onde o xamarin. Forms foi bifurcado do material apresentado no livro.

Os programadores muitas vezes descobrem escrever manipuladores de eventos que detectam quando uma propriedade de um objeto foi alterado e usá-lo para alterar o valor de uma propriedade em outro objeto. Esse processo pode ser automatizado com a técnica de *vinculação de dados*. Associações de dados geralmente são definidas em XAML e se tornam parte da definição da interface do usuário.

Com muita frequência, essas associações de dados se conectar a objetos de interface do usuário aos dados subjacentes. Essa é uma técnica que é explorada mais no [**capítulo 18. MVVM**](chapter18.md). No entanto, as associações de dados também podem se conectar a dois ou mais elementos de interface do usuário. A maioria dos exemplos antecipadas de vinculação de dados neste capítulo demonstra essa técnica.

## <a name="binding-basics"></a>Noções básicas de vinculação

Várias propriedades, métodos e classes envolvidas na associação de dados:

- A classe [`Binding`](xref:Xamarin.Forms.Binding) deriva de [`BindingBase`](xref:Xamarin.Forms.BindingBase) e encapsula muitas características de uma associação de dados
- A propriedade [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) é definida pela classe [`BindableObject`](xref:Xamarin.Forms.BindableObject)
- O método [`SetBinding`](xref:Xamarin.Forms.BindableObject.SetBinding(Xamarin.Forms.BindableProperty,Xamarin.Forms.BindingBase)) também é definido pela classe [`BindableObject`](xref:Xamarin.Forms.BindableObject)
- A classe [`BindableObjectExtensions`](xref:Xamarin.Forms.BindableObjectExtensions) define três métodos de `SetBinding` adicionais

As duas classes a seguir dão suporte a extensões de marcação XAML para associações:

- [`BindingExtension`](xref:Xamarin.Forms.Xaml.BindingExtension) dá suporte à extensão de marcação de `Binding`
- [`ReferenceExtension`](xref:Xamarin.Forms.Xaml.ReferenceExtension) dá suporte à extensão de marcação de `x:Reference`

Duas interfaces envolvidas na associação de dados:

- [`INotifyPropertyChanged`](xref:System.ComponentModel.INotifyPropertyChanged) no namespace `System.ComponentModel` é para implementar a notificação quando uma propriedade é alterada
- [`IValueConverter`](xref:Xamarin.Forms.IValueConverter) é usado para definir classes pequenas que convertem valores de um tipo para outro em associações de dados

Uma associação de dados se conecta a duas propriedades do mesmo objeto ou (mais comumente) em dois objetos diferentes. Essas duas propriedades são referidas como a *origem* e o *destino*. Em geral, uma alteração na propriedade de origem faz com que uma alteração ocorra na propriedade de destino, mas, às vezes, a direção é invertida. Independentemente disso:

- a propriedade de *destino* deve ser apoiada por um [`BindableProperty`](xref:Xamarin.Forms.BindableProperty)
- a propriedade *Source* geralmente é um membro de uma classe que implementa [`INotifyPropertyChanged`](xref:System.ComponentModel.INotifyPropertyChanged)

Uma classe que implementa `INotifyPropertyChanged` aciona um evento de [`PropertyChanged`](xref:System.ComponentModel.INotifyPropertyChanged.PropertyChanged) quando um valor de propriedade é alterado. `BindableObject` implementa `INotifyPropertyChanged` e dispara automaticamente um evento de `PropertyChanged` quando uma propriedade apoiada por um `BindableProperty` altera os valores, mas você pode escrever suas próprias classes que implementam `INotifyPropertyChanged` sem derivar de `BindableObject`.

## <a name="code-and-xaml"></a>Código e XAML

O exemplo [**OpacityBindingCode**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/OpacityBindingCode) demonstra como definir uma associação de dados no código:

- A origem é a propriedade `Value` de um `Slider`
- O destino é a propriedade `Opacity` de um `Label`

Os dois objetos são conectados definindo a `BindingContext` do objeto `Label` ao objeto `Slider`. As duas propriedades são conectadas chamando um método de extensão [`SetBinding`](xref:Xamarin.Forms.BindableObjectExtensions.SetBinding*) no `Label` referenciando a propriedade `OpacityProperty` vinculável e a propriedade `Value` da `Slider` expressa como uma cadeia de caracteres.

Manipular a `Slider`, em seguida, faz com que o `Label` apareça e fique mais à vista.

O [**OpacityBindingXaml**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/OpacityBindingXaml) é o mesmo programa com a associação de dados definida em XAML. O `BindingContext` da `Label` é definido como uma `x:Reference` extensão de marcação que faz referência à `Slider`, e a propriedade `Opacity` da `Label` é definida como a `Binding` extensão de marcação com sua propriedade [`Path`](xref:Xamarin.Forms.Binding.Path) referenciando a propriedade `Value` do `Slider`.

## <a name="source-and-bindingcontext"></a>Código-fonte e BindingContext

O exemplo [**BindingSourceCode**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/BindingSourceCode) mostra uma abordagem alternativa no código. Um objeto `Binding` é criado definindo a propriedade [`Source`](xref:Xamarin.Forms.Binding.Source) para o objeto `Slider` e a propriedade [`Path`](xref:Xamarin.Forms.Binding.Path) como "valor". O método [`SetBinding`](xref:Xamarin.Forms.BindableObject.SetBinding(Xamarin.Forms.BindableProperty,Xamarin.Forms.BindingBase)) de `BindableObject` é chamado no objeto `Label`.

O [Construtor de`Binding`](xref:Xamarin.Forms.Binding.%23ctor(System.String,Xamarin.Forms.BindingMode,Xamarin.Forms.IValueConverter,System.Object,System.String,System.Object)) também poderia ter sido usado para definir o objeto de `Binding`.

O exemplo [**BindingSourceXaml**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/BindingSourceXaml) mostra a técnica comparável em XAML. A propriedade `Opacity` da `Label` é definida como uma `Binding` extensão de marcação com [`Path`](xref:Xamarin.Forms.Binding.Path) definido como a propriedade `Value` e [`Source`](xref:Xamarin.Forms.Binding.Source) definida como uma extensão de marcação `x:Reference` inserida.

Em resumo, há duas maneiras de fazer referência ao objeto de origem de associação:

- Por meio da propriedade `BindingContext` do destino
- Por meio da propriedade `Source` do próprio objeto `Binding`

Se ambos forem especificados, o segundo tem precedência. A vantagem do `BindingContext` é que ele é propagado por meio da árvore visual. Isso é *muito* útil se várias propriedades de destino estiverem associadas ao mesmo objeto de origem.

O programa [**WebViewDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/WebViewDemo) demonstra essa técnica com o elemento [`WebView`](xref:Xamarin.Forms.WebView) . Dois elementos `Button` para navegar para a frente e para trás herdam uma `BindingContext` de seu pai que faz referência à `WebView`. As propriedades `IsEnabled` dos dois botões têm extensões de marcação de `Binding` simples direcionadas ao botão `IsEnabled` Propriedades com base nas configurações das propriedades [`CanGoBack`](xref:Xamarin.Forms.WebView.CanGoBack) e [`CanGoForward`](xref:Xamarin.Forms.WebView.CanGoForward) somente leitura do `WebView`.

## <a name="the-binding-mode"></a>O modo de ligação

Defina a propriedade [`Mode`](xref:Xamarin.Forms.BindingBase.Mode) de `Binding` como um membro da enumeração [`BindingMode`](xref:Xamarin.Forms.BindingMode) :

- [`OneWay`](xref:Xamarin.Forms.BindingMode.OneWay) para que as alterações na propriedade Source afetem o destino
- [`OneWayToSource`](xref:Xamarin.Forms.BindingMode.OneWayToSource) para que as alterações na propriedade de destino afetem a origem
- [`TwoWay`](xref:Xamarin.Forms.BindingMode.TwoWay) para que as alterações na origem e no destino afetem uma à outra
- [`Default`](xref:Xamarin.Forms.BindingMode.Default) usar o [`DefaultBindingMode`](xref:Xamarin.Forms.BindableProperty.DefaultBindingMode) especificado quando o `BindableProperty` de destino foi criado. Se nenhum tiver sido especificado, o padrão será `OneWay` para propriedades vinculáveis normais e `OneWayToSource` para propriedades vinculáveis somente leitura.

> [!NOTE]
> A enumeração `BindingMode` agora também inclui `OnTime` para aplicar uma associação somente quando o contexto de associação é alterado e não quando a propriedade de origem é alterada.

As propriedades que provavelmente são os destinos de associações de dados em cenários MVVM geralmente têm uma `DefaultBindingMode` de `TwoWay`. Estes são:

- Propriedade `Value` de `Slider` e `Stepper`
- Propriedade `IsToggled` de `Switch`
- `Text` propriedade de `Entry`, `Editor`e `SearchBar`
- Propriedade `Date` de `DatePicker`
- Propriedade `Time` de `TimePicker`

O exemplo [**BindingModes**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/BindingModes) demonstra os quatro modos de ligação com uma associação de dados em que o destino é a propriedade `FontSize` de um `Label` e a origem é a propriedade `Value` de um `Slider`. Isso permite que cada `Slider` controle o tamanho da fonte do `Label`correspondente. Mas os elementos de `Slider` não são inicializados porque o `DefaultBindingMode` da propriedade `FontSize` é `OneWay`.

O exemplo [**Reversebinding**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/ReverseBinding) define as associações na propriedade `Value` do `Slider` referenciando a propriedade `FontSize` de cada `Label`. Isso parece ser retroativa, mas funciona melhor em initialzing os elementos de `Slider` porque a propriedade `Value` da `Slider` tem uma `DefaultBindingMode` de `TwoWay`.

[![Captura de tela tripla de associação inversa](images/ch16fg06-small.png "Associação inversa")](images/ch16fg06-large.png#lightbox "Associação inversa")

Isso é análogo ao como associações são definidas no MVVM, e você usará esse tipo de associação com frequência.

## <a name="string-formatting"></a>Cadeia de caracteres de formatação

Quando a propriedade de destino é do tipo `string`, você pode usar a propriedade [`StringFormat`](xref:Xamarin.Forms.BindingBase.StringFormat) definida por `BindingBase` para converter a origem em uma `string`. Defina a propriedade `StringFormat` para uma cadeia de caracteres de formatação .NET que você usaria com o formato de [`String.Format`](xref:System.String.Format(System.String,System.Object)) estático para exibir o objeto. Ao usar essa cadeia de caracteres de formatação dentro de uma extensão de marcação, coloque-o entre aspas simples para que as chaves não ser seja confundidas com uma extensão de marcação incorporado.

O exemplo [**ShowViewValues**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/ShowViewValues) demonstra como usar `StringFormat` em XAML.

O exemplo [**WhatSizeBindings**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/WhatSizeBindings) demonstra como exibir o tamanho da página com associações às propriedades `Width` e `Height` da `ContentPage`.

## <a name="why-is-it-called-path"></a>Por que ele é chamado "Caminho"?

A propriedade [`Path`](xref:Xamarin.Forms.Binding.Path) de `Binding` é tão chamada porque pode ser uma série de propriedades e indexadores separados por pontos. O exemplo de [**BindingPathDemos**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/BindingPathDemos) mostra vários exemplos.

## <a name="binding-value-converters"></a>Conversores de valor de associação

Quando as propriedades de origem e destino de uma associação de tipos diferentes, você pode converter entre os tipos que usam um conversor de associação. Essa é uma classe que implementa a interface [`IValueConverter`](xref:Xamarin.Forms.IValueConverter) e contém dois métodos: [`Convert`](xref:Xamarin.Forms.IValueConverter.Convert(System.Object,System.Type,System.Object,System.Globalization.CultureInfo)) para converter a origem no destino e [`ConvertBack`](xref:Xamarin.Forms.IValueConverter.ConvertBack(System.Object,System.Type,System.Object,System.Globalization.CultureInfo)) para converter o destino para a origem.

A classe [`IntToBoolConverter`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/IntToBoolConverter.cs) na biblioteca [**Xamarin. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) é um exemplo de conversão de um `int` para um `bool`. Ele é demonstrado pelo exemplo [**ButtonEnabler**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/ButtonEnabler) , que habilita apenas a `Button` se pelo menos um caractere tiver sido digitado em um `Entry`.

A classe [`BoolToStringConverter`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BoolToStringConverter.cs) converte um `bool` em um `string` e define duas propriedades para especificar qual texto deve ser retornado para os valores de `false` e `true`.
O [`BoolToColorConverter`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BoolToColorConverter.cs) é semelhante. O exemplo [**SwitchText**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/SwitchText) demonstra como usar esses dois conversores para exibir textos diferentes em cores diferentes com base em uma configuração de `Switch`.

O [`BoolToObjectConverter`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BoolToObjectConverter.cs) genérico pode substituir o `BoolToStringConverter` e `BoolToColorConverter` e servir como um conversor de `bool`para objeto generalizado de qualquer tipo.

## <a name="bindings-and-custom-views"></a>Associações e modos de exibição personalizados

Você pode simplificar a controles personalizados usando associações de dados. O arquivo de código de [`NewCheckBox.cs`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NewCheckBox.xaml.cs) define as propriedades `Text`, `TextColor`, `FontSize`, `FontAttributes`e `IsChecked`, mas não tem nenhuma lógica para os elementos visuais do controle.
Em vez disso, o arquivo de [`NewCheckBox.cs.xaml`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NewCheckBox.xaml) contém toda a marcação dos visuais do controle por meio de associações de dados nos elementos `Label` com base nas propriedades definidas no arquivo code-behind.

O exemplo [**NewCheckBoxDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/NewCheckBoxDemo) demonstra o `NewCheckBox` controle personalizado.

## <a name="related-links"></a>Links relacionados

- [Capítulo 16 texto completo (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch16-Apr2016.pdf)
- [Capítulo 16 amostras](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16)
- [Associação de dados](~/xamarin-forms/app-fundamentals/data-binding/index.md)
