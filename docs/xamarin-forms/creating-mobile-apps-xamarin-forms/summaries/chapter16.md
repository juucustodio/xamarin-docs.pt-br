---
title: Resumo do Capítulo 16. Associação de dados
description: 'Criando aplicativos móveis com Xamarin.Forms: Resumo do Capítulo 16. Associação de dados'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: ED997DB0-C229-4868-A5FB-928703B377D6
author: davidbritch
ms.author: dabritch
ms.date: 07/18/2018
ms.openlocfilehash: 2d61413fb1d8c28a3957da53601d0ad682f35518
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "70771097"
---
# <a name="summary-of-chapter-16-data-binding"></a>Resumo do Capítulo 16. Associação de dados

[![Baixar](~/media/shared/download.png) amostra Baixar a amostra](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16)

> [!NOTE] 
> As notas nesta página indicam áreas onde xamarin.forms divergiu do material apresentado no livro.

Os programadores geralmente se encontram escrevendo manipuladores de eventos que detectam quando uma propriedade de um objeto foi alterada, e usam isso para alterar o valor de uma propriedade em outro objeto. Este processo pode ser automatizado com a técnica de vinculação de *dados.* As vinculações de dados geralmente são definidas em XAML e tornam-se parte da definição da interface do usuário.

Muitas vezes, essas vinculações de dados conectam objetos de interface de usuário a dados subjacentes. Esta é uma técnica que é mais explorada no [**Capítulo 18. MVVM**](chapter18.md). No entanto, as vinculações de dados também podem conectar dois ou mais elementos de interface do usuário. A maioria dos primeiros exemplos de vinculação de dados neste capítulo demonstram essa técnica.

## <a name="binding-basics"></a>Noções básicas de ligação

Várias propriedades, métodos e classes estão envolvidos na vinculação de dados:

- A [`Binding`](xref:Xamarin.Forms.Binding) classe deriva [`BindingBase`](xref:Xamarin.Forms.BindingBase) e encapsula muitas características de uma vinculação de dados
- A [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) propriedade é [`BindableObject`](xref:Xamarin.Forms.BindableObject) definida pela classe
- O [`SetBinding`](xref:Xamarin.Forms.BindableObject.SetBinding(Xamarin.Forms.BindableProperty,Xamarin.Forms.BindingBase)) método também é [`BindableObject`](xref:Xamarin.Forms.BindableObject) definido pela classe
- A [`BindableObjectExtensions`](xref:Xamarin.Forms.BindableObjectExtensions) classe define `SetBinding` três métodos adicionais

As duas classes a seguir suportam extensões de marcação XAML para vinculações:

- [`BindingExtension`](xref:Xamarin.Forms.Xaml.BindingExtension)suporta a `Binding` extensão de marcação
- [`ReferenceExtension`](xref:Xamarin.Forms.Xaml.ReferenceExtension)suporta a `x:Reference` extensão de marcação

Duas interfaces estão envolvidas na vinculação de dados:

- [`INotifyPropertyChanged`](xref:System.ComponentModel.INotifyPropertyChanged)no `System.ComponentModel` namespace é para implementar notificação quando uma propriedade muda
- [`IValueConverter`](xref:Xamarin.Forms.IValueConverter)é usado para definir classes pequenas que convertem valores de um tipo para outro em vinculações de dados

Uma vinculação de dados conecta duas propriedades do mesmo objeto, ou (mais comumente) dois objetos diferentes. Essas duas propriedades são referidas como a *fonte* e o *alvo*. Geralmente, uma mudança na propriedade de origem faz com que uma mudança ocorra na propriedade alvo, mas às vezes a direção é invertida. Independentemente:

- a propriedade *alvo* deve ser apoiado por um[`BindableProperty`](xref:Xamarin.Forms.BindableProperty)
- a propriedade *de origem* geralmente é um membro de uma classe que implementa[`INotifyPropertyChanged`](xref:System.ComponentModel.INotifyPropertyChanged)

Uma classe que `INotifyPropertyChanged` implementa dispara um [`PropertyChanged`](xref:System.ComponentModel.INotifyPropertyChanged.PropertyChanged) evento quando uma propriedade muda de valor. `BindableObject`implementa `INotifyPropertyChanged` e despede automaticamente `PropertyChanged` um evento `BindableProperty` quando uma propriedade apoiada por um `INotifyPropertyChanged` valores `BindableObject`alterados, mas você pode escrever suas próprias classes que implementam sem derivar de .

## <a name="code-and-xaml"></a>Código e XAML

A amostra [**OpacityBindingCode**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/OpacityBindingCode) demonstra como definir uma vinculação de dados em código:

- A fonte `Value` é propriedade de um`Slider`
- O alvo `Opacity` é a propriedade de um`Label`

Os dois objetos são `BindingContext` conectados `Label` definindo `Slider` o objeto para o objeto. As duas propriedades são [`SetBinding`](xref:Xamarin.Forms.BindableObjectExtensions.SetBinding*) conectadas chamando um `OpacityProperty` método de `Value` extensão `Slider` sobre o `Label` referenciamento da propriedade vinculável e a propriedade do expresso como uma string.

Manipular o `Slider` então `Label` faz com que o desvaneça dentro e fora da vista.

O [**OpacityBindingXaml**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/OpacityBindingXaml) é o mesmo programa com o conjunto de vinculação de dados em XAML. O `BindingContext` do `Label` é definido `x:Reference` como uma extensão de `Opacity` marcação `Label` referente ao `Binding` `Slider`, e a propriedade `Value` do é `Slider`definido para a extensão de marcação com sua [`Path`](xref:Xamarin.Forms.Binding.Path) propriedade referente à propriedade do .

## <a name="source-and-bindingcontext"></a>Source and BindingContext

A amostra [**BindingSourceCode**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/BindingSourceCode) mostra uma abordagem alternativa em código. Um `Binding` objeto é criado [`Source`](xref:Xamarin.Forms.Binding.Source) definindo `Slider` a propriedade [`Path`](xref:Xamarin.Forms.Binding.Path) para o objeto e a propriedade como "Valor". O [`SetBinding`](xref:Xamarin.Forms.BindableObject.SetBinding(Xamarin.Forms.BindableProperty,Xamarin.Forms.BindingBase)) método `BindableObject` de é `Label` então chamado no objeto.

O [ `Binding` construtor](xref:Xamarin.Forms.Binding.%23ctor(System.String,Xamarin.Forms.BindingMode,Xamarin.Forms.IValueConverter,System.Object,System.String,System.Object)) também poderia ter sido `Binding` usado para definir o objeto.

A amostra [**BindingSourceXaml**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/BindingSourceXaml) mostra a técnica comparável em XAML. A `Opacity` propriedade `Label` do é `Binding` definida como [`Path`](xref:Xamarin.Forms.Binding.Path) uma extensão de marcação com definido para a `Value` propriedade e [`Source`](xref:Xamarin.Forms.Binding.Source) definida como uma extensão de marcação incorporada. `x:Reference`

Em resumo, há duas maneiras de referenciar o objeto de origem vinculante:

- Através `BindingContext` da propriedade do alvo
- Através `Source` da propriedade `Binding` do próprio objeto

Se ambos forem especificados, o segundo prevalecerá. A vantagem `BindingContext` do é que ele é propagado através da árvore visual. Isso é *muito* útil se várias propriedades de destino estiverem vinculadas ao mesmo objeto de origem.

O programa [**WebViewDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/WebViewDemo) demonstra [`WebView`](xref:Xamarin.Forms.WebView) essa técnica com o elemento. Dois `Button` elementos para navegar para `BindingContext` trás e para frente `WebView`herdam um de seus pais que faz referência ao . As `IsEnabled` propriedades dos dois botões `Binding` têm então extensões de `IsEnabled` marcação simples que [`CanGoBack`](xref:Xamarin.Forms.WebView.CanGoBack) visam [`CanGoForward`](xref:Xamarin.Forms.WebView.CanGoForward) as propriedades dos `WebView`botões com base nas configurações das propriedades e somente leitura do .

## <a name="the-binding-mode"></a>O modo de ligação

Defina [`Mode`](xref:Xamarin.Forms.BindingBase.Mode) a `Binding` propriedade de [`BindingMode`](xref:Xamarin.Forms.BindingMode) um membro da enumeração:

- [`OneWay`](xref:Xamarin.Forms.BindingMode.OneWay)de modo que as mudanças na propriedade de origem afetam o alvo
- [`OneWayToSource`](xref:Xamarin.Forms.BindingMode.OneWayToSource)de modo que as mudanças na propriedade alvo afetam a fonte
- [`TwoWay`](xref:Xamarin.Forms.BindingMode.TwoWay)de modo que as mudanças na fonte e no alvo afetam uns aos outros
- [`Default`](xref:Xamarin.Forms.BindingMode.Default)para usar [`DefaultBindingMode`](xref:Xamarin.Forms.BindableProperty.DefaultBindingMode) o especificado `BindableProperty` quando o alvo foi criado. Se nenhuma foi especificada, `OneWay` o padrão é `OneWayToSource` para propriedades vinculáveis normais e para propriedades vinculáveis somente leitura.

> [!NOTE]
> A `BindingMode` enumeração agora `OnTime` também inclui a aplicação de uma vinculação somente quando o contexto de vinculação muda e não quando a propriedade de origem muda.

As propriedades que provavelmente serão alvos de vinculações de dados em `DefaultBindingMode` `TwoWay`cenários MVVM geralmente têm um de . Estes são:

- Propriedade `Value` de `Slider` e `Stepper`
- Propriedade `IsToggled` de `Switch`
- `Text`propriedade `Entry`de `Editor`, e`SearchBar`
- Propriedade `Date` de `DatePicker`
- Propriedade `Time` de `TimePicker`

A amostra [**BindingModes**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/BindingModes) demonstra os quatro modos de vinculação com `Label` uma vinculação `Value` de `Slider`dados onde o destino é propriedade `FontSize` de a e a fonte é propriedade de um . Isso permite `Slider` que cada um controle `Label`o tamanho da fonte do correspondente . Mas `Slider` os elementos não `DefaultBindingMode` são `FontSize` inicializados `OneWay`porque o da propriedade é .

A amostra [**De Vinculação Reversa**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/ReverseBinding) define `Slider` as `FontSize` vinculações `Label`na `Value` propriedade do referenciamento da propriedade de cada um . Isso parece ser ao contrário, mas funciona `Slider` melhor na `Value` inicialização `Slider` dos `DefaultBindingMode` `TwoWay`elementos porque a propriedade do tem um .

[![Captura de tela tripla de Vinculação Reversa](images/ch16fg06-small.png "Vinculação reversa")](images/ch16fg06-large.png#lightbox "Vinculação reversa")

Isso é análogo à forma como as vinculações são definidas no MVVM, e você usará esse tipo de ligação com freqüência.

## <a name="string-formatting"></a>Formatação de cordas

Quando a propriedade de `string`destino for [`StringFormat`](xref:Xamarin.Forms.BindingBase.StringFormat) do tipo, você pode usar a propriedade definida para `BindingBase` converter a fonte em um `string`. Defina `StringFormat` a propriedade como uma seqüência de [`String.Format`](xref:System.String.Format(System.String,System.Object)) formatação .NET que você usaria com o formato estático para exibir o objeto. Ao usar esta seqüência de formatação dentro de uma extensão de marcação, cerque-a com aspas simples para que as chaves não sejam confundidas com uma extensão de marcação incorporada.

A amostra [**ShowViewValues**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/ShowViewValues) demonstra `StringFormat` como usar em XAML.

A amostra [**WhatSizeBindings**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/WhatSizeBindings) demonstra a exibição do tamanho `Width` `Height` da página `ContentPage`com vinculações às propriedades do .

## <a name="why-is-it-called-path"></a>Por que se chama "Caminho"?

A [`Path`](xref:Xamarin.Forms.Binding.Path) propriedade `Binding` é assim chamada porque pode ser uma série de propriedades e indexadores separados por períodos. A amostra [**BindingPathDemos**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/BindingPathDemos) mostra vários exemplos.

## <a name="binding-value-converters"></a>Conversores de valor de vinculação

Quando as propriedades de origem e destino de uma ligação são tipos diferentes, você pode converter entre os tipos usando um conversor de vinculação. Esta é uma classe [`IValueConverter`](xref:Xamarin.Forms.IValueConverter) que implementa a [`Convert`](xref:Xamarin.Forms.IValueConverter.Convert(System.Object,System.Type,System.Object,System.Globalization.CultureInfo)) interface e contém dois métodos: converter a fonte para o destino e [`ConvertBack`](xref:Xamarin.Forms.IValueConverter.ConvertBack(System.Object,System.Type,System.Object,System.Globalization.CultureInfo)) converter o destino para a fonte.

A [`IntToBoolConverter`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/IntToBoolConverter.cs) classe na biblioteca [**Xamarin.FormsBook.Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) é um `int` exemplo `bool`para converter um para um . Ela é demonstrada pela amostra [**ButtonEnabler,**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/ButtonEnabler) que só permite que `Button` pelo `Entry`menos um caractere tenha sido digitado em um .

A [`BoolToStringConverter`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BoolToStringConverter.cs) classe `bool` converte `string` a para a e define duas propriedades `false` para `true` especificar para qual texto deve ser devolvido e valores.
O [`BoolToColorConverter`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BoolToColorConverter.cs) é similar. A amostra [**SwitchText**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/SwitchText) demonstra o uso desses dois conversores `Switch` para exibir textos diferentes em cores diferentes com base em uma configuração.

O [`BoolToObjectConverter`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BoolToObjectConverter.cs) genérico pode `BoolToStringConverter` `BoolToColorConverter` substituir o e `bool`servir como um conversor generalizado-para-objeto de qualquer tipo.

## <a name="bindings-and-custom-views"></a>Vinculações e visualizações personalizadas

Você pode simplificar controles personalizados usando vinculações de dados. O [`NewCheckBox.cs`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NewCheckBox.xaml.cs) arquivo de `Text` `TextColor`código `FontSize` `FontAttributes`define `IsChecked` , , , , e propriedades, mas não tem nenhuma lógica para o visual do controle.
Em [`NewCheckBox.cs.xaml`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NewCheckBox.xaml) vez disso, o arquivo contém toda a marcação `Label` para o visual do controle através de vinculações de dados nos elementos com base nas propriedades definidas no arquivo de código atrás.

A amostra [**NewCheckBoxDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/NewCheckBoxDemo) demonstra o `NewCheckBox` controle personalizado.

## <a name="related-links"></a>Links relacionados

- [Capítulo 16 texto completo (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch16-Apr2016.pdf)
- [Capítulo 16 amostras](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16)
- [Associação de dados](~/xamarin-forms/app-fundamentals/data-binding/index.md)
