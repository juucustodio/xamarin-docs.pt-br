---
title: Resumo do capítulo 15. A interface interativa
description: 'Criação de aplicativos móveis com xamarin. Forms: Resumo do capítulo 15. A interface interativa'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: F54E86F4-1CDA-474E-9B09-242060C2C13D
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2017
ms.openlocfilehash: 5f96d2f4b619bbb10bb58e9b1b5dc7007c1ce888
ms.sourcegitcommit: ccbf914615c0ce6b3f308d930f7a77418aeb4dbc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/11/2020
ms.locfileid: "78291734"
---
# <a name="summary-of-chapter-15-the-interactive-interface"></a>Resumo do capítulo 15. A interface interativa

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15)

Este capítulo explora oito `View` derivativos que permitem a interação com o usuário.

## <a name="view-overview"></a>Visão geral do modo de exibição

O Xamarin. Forms contém 20 classes instanciáveis que derivam de `View`, mas não `Layout`. Seis delas foram abordados nos capítulos anteriores:

- `Label`: [ **capítulo 2. Anatomia de um aplicativo**](chapter02.md)
- `BoxView`: [ **capítulo 3. Rolando a pilha**](chapter03.md)
- `Button`: [ **capítulo 6. Cliques de botão**](chapter06.md)
- `Image`: [ **capítulo 13. Bitmaps**](chapter13.md)
- `ActivityIndicator`: [ **capítulo 13. Bitmaps**](chapter13.md)
- `ProgressBar`: [ **capítulo 14. AbsoluteLayout**](chapter14.md)

As exibições de oito neste capítulo efetivamente permitir que o usuário interagir com tipos de dados básicos do .NET:

|Tipo de dados|Exibições|
|--- |--- |
|`Double`|[`Slider`](xref:Xamarin.Forms.Slider), [`Stepper`](xref:Xamarin.Forms.Stepper)|
|`Boolean`|[`Switch`](xref:Xamarin.Forms.Switch)|
|`String`|[`Entry`](xref:Xamarin.Forms.Entry), [`Editor`](xref:Xamarin.Forms.Editor), [`SearchBar`](xref:Xamarin.Forms.SearchBar)|
|`DateTime`|[`DatePicker`](xref:Xamarin.Forms.DatePicker), [`TimePicker`](xref:Xamarin.Forms.TimePicker)|

Você pode pensar nesses modos de exibição como representações visuais de interativas dos tipos de dados subjacente. Esse conceito é explorado mais no próximo capítulo [**16. Associação de dados**](chapter16.md).

As exibições de seis restantes são abordadas nos capítulos a seguir:

- `WebView`: [ **capítulo 16. Associação de dados**](chapter16.md)
- `Picker`: [ **capítulo 19. Exibições de coleção**](chapter19.md)
- `ListView`: [ **capítulo 19. Exibições de coleção**](chapter19.md)
- `TableView`: [ **capítulo 19. Exibições de coleção**](chapter19.md)
- `Map`: [ **capítulo 28. Local e mapas**](chapter28.md)
- `OpenGLView`: não abordado neste livro (e não há suporte para plataformas Windows)

## <a name="slider-and-stepper"></a>Controle deslizante e escalonador

Tanto [`Slider`](xref:Xamarin.Forms.Slider) quanto [`Stepper`](xref:Xamarin.Forms.Stepper) permitem que o usuário escolha um valor numérico de um intervalo. O `Slider` é um intervalo contínuo enquanto o `Stepper` envolve valores discretos.

### <a name="slider-basics"></a>Noções básicas do controle deslizante

O [`Slider`](xref:Xamarin.Forms.Slider) é uma barra horizontal que representa um intervalo de valores de um mínimo à esquerda para um máximo à direita. Ele define três propriedades públicas:

- [`Value`](xref:Xamarin.Forms.Slider.Value) do tipo `double`, valor padrão de 0
- [`Minimum`](xref:Xamarin.Forms.Slider.Minimum) do tipo `double`, valor padrão de 0
- [`Maximum`](xref:Xamarin.Forms.Slider.Maximum) do tipo `double`, valor padrão de 1

As propriedades vinculáveis que apoiar essas propriedades Certifique-se de que eles sejam consistentes:

- Para todas as três propriedades, o método [`coerceValue`](xref:Xamarin.Forms.BindableProperty.CoerceValueDelegate) especificado para a propriedade vinculável garante que `Value` esteja entre `Minimum` e `Maximum`.
- O método [`validateValue`](xref:Xamarin.Forms.BindableProperty.ValidateValueDelegate) em `MinimumProperty` retorna `false` se `Minimum` está sendo definido como um valor maior ou igual a `Maximum`e semelhante para `MaximumProperty`. Retornar `false` do método `validateValue` faz com que um `ArgumentException` seja gerado.

`Slider` aciona o evento [`ValueChanged`](xref:Xamarin.Forms.Slider.ValueChanged) com um argumento [`ValueChangedEventArgs`](xref:Xamarin.Forms.ValueChangedEventArgs) quando a propriedade `Value` é alterada, seja de forma programática ou quando o usuário manipula a `Slider`.

O exemplo [**SliderDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/SliderDemo) demonstra o uso simples do `Slider`.

### <a name="common-pitfalls"></a>Armadilhas comuns

Tanto no código quanto no XAML, as propriedades `Minimum` e `Maximum` são definidas na ordem especificada. Certifique-se de inicializar essas propriedades para que `Maximum` seja sempre maior que `Minimum`. Se não uma exceção será lançada.

Inicializar as propriedades de `Slider` pode fazer com que a propriedade `Value` seja alterada e o evento de `ValueChanged` seja acionado. Você deve garantir que o manipulador de eventos `Slider` não acesse exibições que ainda não foram criadas durante a inicialização da página.

O evento `ValueChanged` não é acionado durante a inicialização `Slider`, a menos que a propriedade `Value` seja alterada. Você pode chamar o manipulador de `ValueChanged` diretamente do código.

### <a name="slider-color-selection"></a>Seleção de cor do controle deslizante

O programa [**RgbSliders**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/RgbSliders) contém três elementos `Slider` que permitem selecionar uma cor interativamente, especificando seus valores RGB:

[![Captura de tela tripla de controles deslizantes do R G B](images/ch15fg03-small.png "Controles deslizantes RGB")](images/ch15fg03-large.png#lightbox "Controles deslizantes RGB")

O exemplo [**textfade**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/TextFade) usa dois elementos `Slider` para mover dois elementos `Label` entre um `AbsoluteLayout` e esmaecer um para o outro.

### <a name="the-stepper-difference"></a>A diferença escalonador

O [`Stepper`](xref:Xamarin.Forms.Stepper) define as mesmas propriedades e eventos que `Slider`, mas a propriedade `Maximum` é inicializada como 100 e `Stepper` define uma quarta Propriedade:

- [`Increment`](xref:Xamarin.Forms.Stepper.Increment) do tipo `double`, inicializado como 1

Visualmente, a `Stepper` consiste em dois botões rotulados **&ndash;** e **+** . Pressionar **&ndash;** diminui `Value` `Increment` para um mínimo de `Minimum`. Pressionar **+** aumenta `Value` `Increment` para um máximo de `Maximum`.

Isso é demonstrado pelo exemplo de [**StepperDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/StepperDemo) .

## <a name="switch-and-checkbox"></a>Switch e a caixa de seleção

O [`Switch`](xref:Xamarin.Forms.Switch) permite que o usuário especifique um valor booliano.

### <a name="switch-basics"></a>Noções básicas do comutador

Visualmente, o `Switch` consiste em uma alternância que pode ser desativada e ativada. A classe define uma propriedade:

- [`IsToggled`](xref:Xamarin.Forms.Switch.IsToggled) do tipo `bool`

`Switch` define um evento:

- [`Toggled`](xref:Xamarin.Forms.Switch.Toggled) acompanhado por um objeto [`ToggledEventArgs`](xref:Xamarin.Forms.ToggledEventArgs) , disparado quando a propriedade `IsToggled` é alterada.

O programa [**SwitchDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/SwitchDemo) demonstra a `Switch`.

### <a name="a-traditional-checkbox"></a>Uma caixa de seleção tradicional

Alguns desenvolvedores podem preferir um `CheckBox` mais tradicional ao `Switch`. A biblioteca [**Xamarin. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) contém uma classe `CheckBox` que deriva de `ContentView`. `CheckBox` é implementada pelos arquivos [CheckBox. XAML](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/CheckBox.xaml) e [CheckBox.XAML.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/CheckBox.xaml.cs) . `CheckBox` define três propriedades (`Text`, `FontSize`e `IsChecked`) e um evento `CheckedChanged`.

O exemplo [**CheckBoxDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/CheckBoxDemo) demonstra essa `CheckBox`.

## <a name="typing-text"></a>Digitar texto

Xamarin. Forms define três modos de exibição que permitem ao usuário inserir e editar texto:

- [`Entry`](xref:Xamarin.Forms.Entry) para uma única linha de texto
- [`Editor`](xref:Xamarin.Forms.Editor) para várias linhas de texto
- [`SearchBar`](xref:Xamarin.Forms.SearchBar) para uma única linha de texto para fins de pesquisa.

`Entry` e `Editor` derivam de [`InputView`](xref:Xamarin.Forms.InputView), que deriva de `View`. `SearchBar` deriva diretamente de `View`.

### <a name="keyboard-and-focus"></a>Teclado e foco

Em telefones e tablets sem teclados físicos, os `Entry`, `Editor`e elementos de `SearchBar`, todos fazem com que uma barra virtual apareça. A presença desse teclado na tela está relacionada ao foco de entrada. Uma exibição deve ter as propriedades [`IsVisible`](xref:Xamarin.Forms.VisualElement.IsVisible) e [`IsEnabled`](xref:Xamarin.Forms.VisualElement.IsEnabled) definidas como `true` para obter o foco de entrada.

Dois métodos, uma propriedade somente leitura e dois eventos estão envolvidos com o foco de entrada. Todos eles são definidos por `VisualElement`:

- O método [`Focus`](xref:Xamarin.Forms.VisualElement.Focus) tenta definir o foco de entrada para um elemento e retorna `true` se for bem-sucedido
- O método [`Unfocus`](xref:Xamarin.Forms.VisualElement.Unfocus) remove o foco de entrada de um elemento
- A [`IsFocused`](xref:Xamarin.Forms.VisualElement.IsFocused) propriedade somente leitura indica se o elemento tem foco de entrada
- O evento [`Focused`](xref:Xamarin.Forms.VisualElement.Focused) indica quando um elemento Obtém o foco de entrada
- O evento [`Unfocused`](xref:Xamarin.Forms.VisualElement.Unfocused) indica quando um elemento perde o foco de entrada

### <a name="choosing-the-keyboard"></a>Escolhendo o teclado

A classe [`InputView`](xref:Xamarin.Forms.InputView) da qual `Entry` e `Editor` derivam definem apenas uma propriedade:

- [`Keyboard`](xref:Xamarin.Forms.InputView.Keyboard) do tipo [`Keyboard`](xref:Xamarin.Forms.Keyboard)

Isso indica o tipo de teclado que é exibido. Alguns teclados são otimizados para URIs ou números.

A classe `Keyboard` permite definir um teclado com um método estático [`Keyboard.Create`](xref:Xamarin.Forms.Keyboard.Create(Xamarin.Forms.KeyboardFlags)) com um argumento do tipo [`KeyboardFlags`](xref:Xamarin.Forms.KeyboardFlags), uma enumeração com os seguintes sinalizadores de bits:

- `None` definido como 0
- [`CapitalizeSentence`](xref:Xamarin.Forms.KeyboardFlags.CapitalizeSentence) definido como 1
- [`Spellcheck`](xref:Xamarin.Forms.KeyboardFlags.Spellcheck) definido como 2
- [`Suggestions`](xref:Xamarin.Forms.KeyboardFlags.Suggestions) definido como 4
- [`All`](xref:Xamarin.Forms.KeyboardFlags.All) definido como \xFFFFFFFF

Ao usar o [`Editor`](xref:Xamarin.Forms.Editor) multilinha quando um parágrafo ou mais texto é esperado, chamar `Keyboard.Create` é uma boa abordagem para selecionar um teclado. Para a [`Entry`](xref:Xamarin.Forms.Entry)de linha única, as seguintes propriedades somente leitura estáticas de `Keyboard` são úteis:

- [`Default`](xref:Xamarin.Forms.Keyboard.Default)
- [`Text`](xref:Xamarin.Forms.Keyboard.Text)
- [`Chat`](xref:Xamarin.Forms.Keyboard.Chat)
- [`Url`](xref:Xamarin.Forms.Keyboard.Url)
- [`Email`](xref:Xamarin.Forms.Keyboard.Email)
- [`Telephone`](xref:Xamarin.Forms.Keyboard.Telephone)
- [`Numeric`](xref:Xamarin.Forms.Keyboard.Numeric) para números positivos com ou sem um ponto decimal.

O [`KeyboardTypeConverter`](xref:Xamarin.Forms.KeyboardTypeConverter) permite especificar essas propriedades em XAML, conforme demonstrado pelo programa [**EntryKeyboards**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/EntryKeyboards) .

### <a name="entry-properties-and-events"></a>Propriedades de entrada e eventos

O [`Entry`](xref:Xamarin.Forms.Entry) de linha única define as seguintes propriedades:

- [`Text`](xref:Xamarin.Forms.InputView.Text) do tipo `string`, o texto que aparece na `Entry`
- [`TextColor`](xref:Xamarin.Forms.InputView.TextColor) do tipo `Color`
- [`FontFamily`](xref:Xamarin.Forms.Entry.FontFamily) do tipo `string`
- [`FontSize`](xref:Xamarin.Forms.Entry.FontSize) do tipo `double`
- [`FontAttributes`](xref:Xamarin.Forms.Entry.FontAttributes) do tipo `FontAttributes`
- [`IsPassword`](xref:Xamarin.Forms.Entry.IsPassword) do tipo `bool`, que faz com que os caracteres sejam mascarados
- [`Placeholder`](xref:Xamarin.Forms.InputView.Placeholder) do tipo `string`, para texto colorido Dimly que aparece na `Entry` antes de qualquer coisa ser digitada
- [`PlaceholderColor`](xref:Xamarin.Forms.InputView.PlaceholderColor) do tipo `Color`

O `Entry` também define dois eventos:

- [`TextChanged`](xref:Xamarin.Forms.InputView.TextChanged) com um objeto [`TextChangedEventArgs`](xref:Xamarin.Forms.TextChangedEventArgs) , disparado sempre que a propriedade `Text` for alterada
- [`Completed`](xref:Xamarin.Forms.Entry.Completed), acionado quando o usuário é concluído e o teclado é Descartado. O usuário indica a conclusão de uma maneira específica da plataforma

O exemplo [**QuadraticEquations**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/QuadaticEquations) demonstra esses dois eventos.

### <a name="the-editor-difference"></a>A diferença de Editor

As [`Editor`](xref:Xamarin.Forms.Editor) de várias linhas definem as mesmas propriedades `Text` e `Font` como `Entry`, mas não as outras propriedades. `Editor` também define as mesmas duas propriedades que `Entry`.

[**JustNotes**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/JustNotes) é um programa de anotações de forma livre que salva e restaura o conteúdo do `Editor`.

### <a name="the-searchbar"></a>O SearchBar

O [`SearchBar`](xref:Xamarin.Forms.SearchBar) não deriva de `InputView`, portanto, ele não tem uma propriedade `Keyboard`. Mas ele tem todas as propriedades `Text`, `Font`e `Placeholder` que `Entry` define. Além disso, `SearchBar` define três propriedades adicionais:

- [`CancelButtonColor`](xref:Xamarin.Forms.SearchBar.CancelButtonColor) do tipo `Color`
- [`SearchCommand`](xref:Xamarin.Forms.SearchBar.SearchCommand) do tipo [`ICommand`](xref:System.Windows.Input.ICommand) para uso com as associações de dados e o MVVM
- [`SearchCommandParameter`](xref:Xamarin.Forms.SearchBar.SearchCommandParameter) do tipo `Object`, para uso com `SearchCommand`

O específico da plataforma Cancelar botão apaga o texto. O `SearchBar` também tem um botão de pesquisa específico da plataforma. Pressionar qualquer um desses botões gera um dos dois eventos que `SearchBar` define:

- [`TextChanged`](xref:Xamarin.Forms.InputView.TextChanged) acompanhado por um objeto [`TextChangedEventArgs`](xref:Xamarin.Forms.TextChangedEventArgs)
- [`SearchButtonPressed`](xref:Xamarin.Forms.SearchBar.SearchButtonPressed)

O exemplo [**SearchBarDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/SearchBarDemo) demonstra a `SearchBar`.

## <a name="date-and-time-selection"></a>Seleção de data e hora

As exibições [`DatePicker`](xref:Xamarin.Forms.DatePicker) e [`TimePicker`](xref:Xamarin.Forms.TimePicker) implementam controles específicos da plataforma que permitem ao usuário especificar uma data ou hora.

### <a name="the-datepicker"></a>O DatePicker

[`DatePicker`](xref:Xamarin.Forms.DatePicker) define quatro propriedades:

- [`MinimumDate`](xref:Xamarin.Forms.DatePicker.MinimumDate) do tipo `DateTime`, inicializado para 1º de janeiro de 1900
- [`MaximumDate`](xref:Xamarin.Forms.DatePicker.MaximumDate) do tipo `DateTime`, inicializado para 31 de dezembro de 2100
- [`Date`](xref:Xamarin.Forms.DatePicker.Date) do tipo `DateTime`, inicializado para `DateTime.Today`
- [`Format`](xref:Xamarin.Forms.DatePicker.Format) do tipo `string`, a cadeia de caracteres de formatação .net inicializada como "d", o padrão de data abreviada, resultando em uma exibição de data como "7/20/1969" nos EUA.

Você pode definir as propriedades de `DateTime` em XAML, expressando as propriedades como elementos de propriedade e usando o formato de data invariável de cultura ("7/20/1969").   

O exemplo de [**DaysBetweenDates**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/DaysBetweenDates) calcula o número de dias entre duas datas selecionadas pelo usuário.

### <a name="the-timepicker-or-is-it-a-timespanpicker"></a>O TimePicker (ou é um TimeSpanPicker?)

[`TimePicker`](xref:Xamarin.Forms.TimePicker) define duas propriedades e nenhum evento:

- [`Time`](xref:Xamarin.Forms.TimePicker.Time) é do tipo `TimeSpan` em vez de `DateTime`, indicando o tempo decorrido desde a meia-noite
- [`Format`](xref:Xamarin.Forms.TimePicker.Format) do tipo `string`, a cadeia de caracteres de formatação .net inicializada como "t", o padrão de tempo curto, resultando em uma exibição de tempo como "1:45 PM" nos EUA.

O programa [**SetTimer**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/SetTimer) demonstra como usar o `TimePicker` para especificar um horário para um temporizador. O programa só funciona se mantê-lo em primeiro plano.

**SetTimer** também demonstra o uso do método [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert(System.String,System.String,System.String)) de `Page` para exibir uma caixa de alerta.

## <a name="related-links"></a>Links relacionados

- [Capítulo 15 texto completo (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch15-Apr2016.pdf)
- [Capítulo 15 amostras](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15)
- [Slider](~/xamarin-forms/user-interface/slider.md)
- [Entry](~/xamarin-forms/user-interface/text/entry.md)
- [Editor](~/xamarin-forms/user-interface/text/editor.md)
- [DatePicker](~/xamarin-forms/user-interface/datepicker.md)
