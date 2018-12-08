---
title: Resumo do capítulo 15. A interface interativa
description: 'Criação de aplicativos móveis com xamarin. Forms: Resumo do capítulo 15. A interface interativa'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: F54E86F4-1CDA-474E-9B09-242060C2C13D
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2017
ms.openlocfilehash: 6b519b15199373762965a8eeee6c9ae73f125cf3
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/07/2018
ms.locfileid: "53060427"
---
# <a name="summary-of-chapter-15-the-interactive-interface"></a>Resumo do capítulo 15. A interface interativa

[![Baixar exemplo](~/media/shared/download.png) baixar o exemplo](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15)

Este capítulo explora oito `View` derivados que permitem a interação com o usuário.

## <a name="view-overview"></a>Visão geral do modo de exibição

Xamarin. Forms contém classes que pode ser instanciadas 20 que derivam de `View` , mas não `Layout`. Seis delas foram abordados nos capítulos anteriores:

- `Label`: [ **Capítulo 2. Anatomia de um aplicativo**](chapter02.md)
- `BoxView`: [ **Capítulo 3. Rolagem da pilha**](chapter03.md)
- `Button`: [ **Capítulo 6. Cliques de botão**](chapter06.md)
- `Image`: [ **Capítulo 13. Bitmaps**](chapter13.md)
- `ActivityIndicator`: [ **Capítulo 13. Bitmaps**](chapter13.md)
- `ProgressBar`: [ **Capítulo 14. AbsoluteLayout**](chapter14.md)

As exibições de oito neste capítulo efetivamente permitir que o usuário interagir com tipos de dados básicos do .NET:

|Tipo de dados|Exibições|
|--- |--- |
|`Double`|[`Slider`](xref:Xamarin.Forms.Slider), [`Stepper`](xref:Xamarin.Forms.Stepper)|
|`Boolean`|[`Switch`](xref:Xamarin.Forms.Switch)|
|`String`|[`Entry`](xref:Xamarin.Forms.Entry), [`Editor`](xref:Xamarin.Forms.Editor), [`SearchBar`](xref:Xamarin.Forms.SearchBar)|
|`DateTime`|[`DatePicker`](xref:Xamarin.Forms.DatePicker), [`TimePicker`](xref:Xamarin.Forms.TimePicker)|

Você pode pensar nesses modos de exibição como representações visuais de interativas dos tipos de dados subjacente. Esse conceito é explorado mais no próximo capítulo [ **capítulo 16. Associação de dados**](chapter16.md).

As exibições de seis restantes são abordadas nos capítulos a seguir:

- `WebView`: [ **Capítulo 16. Associação de dados**](chapter16.md)
- `Picker`: [ **Capítulo 19. Exibições de coleção**](chapter19.md)
- `ListView`: [ **Capítulo 19. Exibições de coleção**](chapter19.md)
- `TableView`: [ **Capítulo 19. Exibições de coleção**](chapter19.md)
- `Map`: [ **Capítulo 28. Localização e mapas**](chapter28.md)
- `OpenGLView`: Não abordado neste livro (e não há suporte para plataformas do Windows)

## <a name="slider-and-stepper"></a>Controle deslizante e escalonador

Ambos [ `Slider` ](xref:Xamarin.Forms.Slider) e [ `Stepper` ](xref:Xamarin.Forms.Stepper) permitir que o usuário escolha um valor numérico de um intervalo. O `Slider` é um intervalo contínuo durante o `Stepper` envolve valores discretos.

### <a name="slider-basics"></a>Noções básicas do controle deslizante

O [ `Slider` ](xref:Xamarin.Forms.Slider) é uma barra que representa um intervalo de valores de um mínimo à esquerda até um máximo à direita horizontal. Ele define três propriedades públicas:

- [`Value`](xref:Xamarin.Forms.Slider.Value) do tipo `double`, o valor de 0 padrão
- [`Minimum`](xref:Xamarin.Forms.Slider.Minimum) do tipo `double`, o valor de 0 padrão
- [`Maximum`](xref:Xamarin.Forms.Slider.Maximum) do tipo `double`, o valor de 1 padrão

As propriedades vinculáveis que apoiar essas propriedades Certifique-se de que eles sejam consistentes:

- Para todas as três propriedades, o [ `coerceValue` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableProperty+CoerceValueDelegate/) método especificado para a propriedade associável garante que `Value` entre `Minimum` e `Maximum`.
- O [ `validateValue` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableProperty+ValidateValueDelegate/) método `MinimumProperty` retorna `false` se `Minimum` está sendo definida como um valor maior que ou igual a `Maximum`e semelhante para `MaximumProperty`. Retornando `false` do `validateValue` faz com que o método um `ArgumentException` a ser gerado.

`Slider` é acionado o [ `ValueChanged` ](xref:Xamarin.Forms.Slider.ValueChanged) evento com um [ `ValueChangedEventArgs` ](xref:Xamarin.Forms.ValueChangedEventArgs) argumento quando o `Value` alterações de propriedade, por meio de programação ou quando o usuário manipula o `Slider`.

O [ **SliderDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/SliderDemo) exemplo demonstra o uso simple dos `Slider`.

### <a name="common-pitfalls"></a>Armadilhas comuns

No código e no XAML, o `Minimum` e `Maximum` são definidas na ordem em que você especificar. Não se esqueça de inicializar essas propriedades, de modo que `Maximum` é sempre maior que `Minimum`. Se não uma exceção será lançada.

Inicializando o `Slider` propriedades podem fazer com que o `Value` propriedade a ser alterada e o `ValueChanged` evento seja acionado. Você deve garantir que o `Slider` manipulador de eventos não acessar modos de exibição que ainda não tiver sido criados durante a inicialização da página.

O `ValueChanged` evento não é acionado durante `Slider` inicialização, a menos que o `Value` alterações de propriedade. Você pode chamar o `ValueChanged` manipulador diretamente no código.

### <a name="slider-color-selection"></a>Seleção de cor do controle deslizante

O [ **RgbSliders** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/RgbSliders) programa contém três `Slider` elementos que permitem que você selecionar uma cor, de forma interativa, especificando seus valores RGB:

[![Captura de tela dos controles deslizantes de R G B tripla](images/ch15fg03-small.png "controles deslizantes de RGB")](images/ch15fg03-large.png#lightbox "controles deslizantes de RGB")

O [ **TextFade** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/TextFade) exemplo usa dois `Slider` elementos a ser movido de dois `Label` elementos em um `AbsoluteLayout` e esmaece um para o outro.

### <a name="the-stepper-difference"></a>A diferença escalonador

O [ `Stepper` ](xref:Xamarin.Forms.Stepper) define as propriedades e eventos como a mesma `Slider` , mas o `Maximum` propriedade é inicializada como 100 e `Stepper` define uma quarta propriedade:

- [`Increment`](xref:Xamarin.Forms.Stepper.Increment) do tipo `double`inicializado como 1

Visualmente, o `Stepper` consiste em dois botões rotulados **&ndash;** e **+**. Pressionar **&ndash;** diminui `Value` pela `Increment` em um mínimo de `Minimum`. Pressionar **+** aumenta `Value` pela `Increment` a um máximo de `Maximum`.

Isso é demonstrado pelo [ **StepperDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/StepperDemo) exemplo.

## <a name="switch-and-checkbox"></a>Switch e a caixa de seleção

O [ `Switch` ](xref:Xamarin.Forms.Switch) permite que o usuário especifique um valor booliano.

### <a name="switch-basics"></a>Noções básicas do comutador

Visualmente, o `Switch` consiste em uma alternância que pode ser ativada e ativar. A classe define uma propriedade:

- [`IsToggled`](xref:Xamarin.Forms.Switch.IsToggled) do tipo `bool`

`Switch` define um evento:

- [`Toggled`](xref:Xamarin.Forms.Switch.Toggled) acompanhada por uma [ `ToggledEventArgs` ](xref:Xamarin.Forms.ToggledEventArgs) objeto, disparado quando o `IsToggled` alterações de propriedade.

O [ **SwitchDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/SwitchDemo) programa demonstra o `Switch`.

### <a name="a-traditional-checkbox"></a>Uma caixa de seleção tradicional

Alguns desenvolvedores talvez prefiram mais tradicional `CheckBox` para o `Switch`. O [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) biblioteca contém um `CheckBox` classe que deriva de `ContentView`. `CheckBox` é implementado pelos [CheckBox.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/CheckBox.xaml) e [CheckBox.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/CheckBox.xaml.cs) arquivos. `CheckBox` define três propriedades (`Text`, `FontSize`, e `IsChecked`) e um `CheckedChanged` eventos.

O [ **CheckBoxDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/CheckBoxDemo) demonstra isso `CheckBox`.

## <a name="typing-text"></a>Digitar texto

Xamarin. Forms define três modos de exibição que permitem ao usuário inserir e editar texto:

- [`Entry`](xref:Xamarin.Forms.Entry) para uma única linha de texto
- [`Editor`](xref:Xamarin.Forms.Editor) para várias linhas de texto
- [`SearchBar`](xref:Xamarin.Forms.SearchBar) para uma única linha de texto para fins de pesquisa.

`Entry` e `Editor` derivam [ `InputView` ](xref:Xamarin.Forms.InputView), que é derivada de `View`. `SearchBar` deriva diretamente de `View`.

### <a name="keyboard-and-focus"></a>Teclado e foco

Em telefones e tablets sem teclados físicos, o `Entry`, `Editor`, e `SearchBar` todos os elementos fazem com que um teclado virtual para o pop-up. A presença desse teclado na tela está relacionada ao foco de entrada. Um modo de exibição deve ter sua [ `IsVisible` ](xref:Xamarin.Forms.VisualElement.IsVisible) e [ `IsEnabled` ](xref:Xamarin.Forms.VisualElement.IsEnabled) propriedades definidas como `true` para obter o foco de entrada.

Dois métodos, uma propriedade somente leitura e dois eventos estão envolvidos com o foco de entrada. Estes são definidos pelos `VisualElement`:

- O [ `Focus` ](xref:Xamarin.Forms.VisualElement.Focus) tenta definir o foco de entrada para um elemento de método e retorna `true` se for bem-sucedido
- O [ `Unfocus` ](xref:Xamarin.Forms.VisualElement.Unfocus) método Remove o foco de entrada de um elemento
- O [ `IsFocused` ](xref:Xamarin.Forms.VisualElement.IsFocused) propriedade somente leitura que indica se o elemento tem foco de entrada
- O [ `Focused` ](xref:Xamarin.Forms.VisualElement.Focused) evento indica quando um elemento obtém o foco de entrada
- O [ `Unfocused` ](xref:Xamarin.Forms.VisualElement.Unfocused) evento indica quando um elemento perde o foco de entrada

### <a name="choosing-the-keyboard"></a>Escolhendo o teclado

O [ `InputView` ](xref:Xamarin.Forms.InputView) classe da qual `Entry` e `Editor` derivar define apenas uma propriedade:

- [`Keyboard`](xref:Xamarin.Forms.InputView.Keyboard) do tipo [`Keyboard`](xref:Xamarin.Forms.Keyboard)

Isso indica o tipo de teclado que é exibido. Alguns teclados são otimizados para URIs ou números.

O `Keyboard` classe permite definir um teclado com um estático [ `Keyboard.Create` ](xref:Xamarin.Forms.Keyboard.Create(Xamarin.Forms.KeyboardFlags)) método com um argumento do tipo [ `KeyboardFlags` ](xref:Xamarin.Forms.KeyboardFlags), uma enumeração com os sinalizadores de bit a seguir:

- `None` definido como 0
- [`CapitalizeSentence`](xref:Xamarin.Forms.KeyboardFlags.CapitalizeSentence) definido como 1
- [`Spellcheck`](xref:Xamarin.Forms.KeyboardFlags.Spellcheck) definido como 2
- [`Suggestions`](xref:Xamarin.Forms.KeyboardFlags.Suggestions) definido como 4
- [`All`](xref:Xamarin.Forms.KeyboardFlags.All) definido como \xFFFFFFFF

Ao usar o multiline [ `Editor` ](xref:Xamarin.Forms.Editor) quando se espera que um parágrafo ou mais de texto, chamando `Keyboard.Create` é uma boa abordagem para selecionar um teclado. Para a linha única [ `Entry` ](xref:Xamarin.Forms.Entry), as seguintes propriedades estáticas de somente leitura do `Keyboard` são úteis:

- [`Default`](xref:Xamarin.Forms.Keyboard.Default)
- [`Text`](xref:Xamarin.Forms.Keyboard.Text)
- [`Chat`](xref:Xamarin.Forms.Keyboard.Chat)
- [`Url`](xref:Xamarin.Forms.Keyboard.Url)
- [`Email`](xref:Xamarin.Forms.Keyboard.Email)
- [`Telephone`](xref:Xamarin.Forms.Keyboard.Telephone)
- [`Numeric`](xref:Xamarin.Forms.Keyboard.Numeric) para números positivos com ou sem um ponto decimal.

O [ `KeyboardTypeConverter` ](xref:Xamarin.Forms.KeyboardTypeConverter) permite especificar essas propriedades em XAML, como demonstrado pelo [ **EntryKeyboards** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/EntryKeyboards) programa.

### <a name="entry-properties-and-events"></a>Propriedades de entrada e eventos

A linha única [ `Entry` ](xref:Xamarin.Forms.Entry) define as propriedades a seguir:

- [`Text`](xref:Xamarin.Forms.Entry.Text) do tipo `string`, o texto que aparece no `Entry`
- [`TextColor`](xref:Xamarin.Forms.Entry.TextColor) do tipo `Color`
- [`FontFamily`](xref:Xamarin.Forms.Entry.FontFamily) do tipo `string`
- [`FontSize`](xref:Xamarin.Forms.Entry.FontSize) do tipo `double`
- [`FontAttributes`](xref:Xamarin.Forms.Entry.FontAttributes) do tipo `FontAttributes`
- [`IsPassword`](xref:Xamarin.Forms.Entry.IsPassword) do tipo `bool`, que faz com que os caracteres a ser mascarado
- [`Placeholder`](xref:Xamarin.Forms.Entry.Placeholder) do tipo `string`, para texto dimly colorido que aparece no `Entry` antes de qualquer coisa é digitada
- [`PlaceholderColor`](xref:Xamarin.Forms.Entry.PlaceholderColor) do tipo `Color`

O `Entry` também define dois eventos:

- [`TextChanged`](xref:Xamarin.Forms.Entry.TextChanged) com uma [ `TextChangedEventArgs` ](xref:Xamarin.Forms.TextChangedEventArgs) objeto, acionado sempre que o `Text` alterações de propriedade
- [`Completed`](xref:Xamarin.Forms.Entry.Completed), disparado quando o usuário for concluído e o teclado é descartado. O usuário indica a conclusão de uma maneira específica da plataforma

O [ **QuadraticEquations** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/QuadaticEquations) que demonstra esses dois eventos.

### <a name="the-editor-difference"></a>A diferença de Editor

O multiline [ `Editor` ](xref:Xamarin.Forms.Editor) define a mesma `Text` e `Font` propriedades como `Entry` , mas não as outras propriedades. `Editor` também define as mesmas duas propriedades `Entry`.

[**JustNotes** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/JustNotes) é um programa de anotações em notas de forma livre que salva e restaura o conteúdo do `Editor`.

### <a name="the-searchbar"></a>O SearchBar

O [ `SearchBar` ](xref:Xamarin.Forms.SearchBar) não é derivado de `InputView`, de modo que ele não tem um `Keyboard` propriedade. Mas tem todos os `Text`, `Font`, e `Placeholder` propriedades que `Entry` define. Além disso, `SearchBar` define três propriedades adicionais:

- [`CancelButtonColor`](xref:Xamarin.Forms.SearchBar.CancelButtonColor) do tipo `Color`
- [`SearchCommand`](xref:Xamarin.Forms.SearchBar.SearchCommand) do tipo [ `ICommand` ](xref:System.Windows.Input.ICommand) para uso com o MVVM e associações de dados
- [`SearchCommandParameter`](xref:Xamarin.Forms.SearchBar.SearchCommandParameter) do tipo `Object`, para uso com `SearchCommand`

O específico da plataforma Cancelar botão apaga o texto. O `SearchBar` também tem um botão de pesquisa específicos da plataforma. Pressionar qualquer um desses botões gera um dos dois eventos que `SearchBar` define:

- [`TextChanged`](xref:Xamarin.Forms.SearchBar.TextChanged) acompanhada por uma [ `TextChangedEventArgs` ](xref:Xamarin.Forms.TextChangedEventArgs) objeto
- [`SearchButtonPressed`](xref:Xamarin.Forms.SearchBar.SearchButtonPressed)

O [ **SearchBarDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/SearchBarDemo) exemplo demonstra o `SearchBar`.

## <a name="date-and-time-selection"></a>Seleção de data e hora

O [ `DatePicker` ](xref:Xamarin.Forms.DatePicker) e [ `TimePicker` ](xref:Xamarin.Forms.TimePicker) exibições implementam controles específicos da plataforma que permitem que o usuário especifique uma data ou hora.

### <a name="the-datepicker"></a>O DatePicker

[`DatePicker`](xref:Xamarin.Forms.DatePicker) define quatro propriedades:

- [`MinimumDate`](xref:Xamarin.Forms.DatePicker.MinimumDate) do tipo `DateTime`inicializado para 1 de janeiro de 1900
- [`MaximumDate`](xref:Xamarin.Forms.DatePicker.MaximumDate) do tipo `DateTime`inicializado a 31 de dezembro de 2100
- [`Date`](xref:Xamarin.Forms.DatePicker.Date) do tipo `DateTime`inicializado para `DateTime.Today`
- [`Format`](xref:Xamarin.Forms.DatePicker.Format) do tipo `string`, o .NET inicializada como "d", o padrão de data abreviada, de cadeia de caracteres de formatação, resultando em uma exibição de data como "7/20/1969" nos EUA.

Você pode definir o `DateTime` Formatar de propriedades no XAML expressar as propriedades como elementos de propriedade e usando a cultura invariável data abreviada ("7/20/1969").   

O [ **DaysBetweenDates** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/DaysBetweenDates) exemplo calcula o número de dias entre duas datas selecionadas pelo usuário.

### <a name="the-timepicker-or-is-it-a-timespanpicker"></a>O TimePicker (ou é um TimeSpanPicker?)

[`TimePicker`](xref:Xamarin.Forms.TimePicker) define duas propriedades e não há eventos:

- [`Time`](xref:Xamarin.Forms.TimePicker.Time) é do tipo `TimeSpan` em vez de `DateTime`, que indica o tempo decorrido desde a meia-noite
- [`Format`](xref:Xamarin.Forms.TimePicker.Format) do tipo `string`, o .NET inicializada como "t", o padrão de hora abreviada, resultando em uma exibição de tempo como "1 6:45 PM" de cadeia de caracteres de formatação nos EUA.

O [ **SetTimer** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/SetTimer) programa demonstra como usar o `TimePicker` para especificar uma hora para um timer. O programa só funciona se mantê-lo em primeiro plano.

**SetTimer** também demonstra como usar o [ `DisplayAlert` ](xref:Xamarin.Forms.Page.DisplayAlert(System.String,System.String,System.String)) método de `Page` para exibir uma caixa de alerta.



## <a name="related-links"></a>Links relacionados

- [Texto completo do capítulo 15 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch15-Apr2016.pdf)
- [Exemplos do capítulo 15](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15)
- [Slider](~/xamarin-forms/user-interface/slider.md)
- [Entry](~/xamarin-forms/user-interface/text/entry.md)
- [Editor](~/xamarin-forms/user-interface/text/editor.md)
- [DatePicker](~/xamarin-forms/user-interface/datepicker.md)
