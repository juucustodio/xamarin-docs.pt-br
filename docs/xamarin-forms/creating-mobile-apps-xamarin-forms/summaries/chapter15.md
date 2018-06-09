---
title: Resumo do capítulo 15. A interface interativa
description: 'Criando aplicativos móveis com o xamarin. Forms: Resumo do capítulo 15. A interface interativa'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: F54E86F4-1CDA-474E-9B09-242060C2C13D
author: charlespetzold
ms.author: chape
ms.date: 11/07/2017
ms.openlocfilehash: aac49c9e74dd22642396ea8daf5ee3abd85de7bf
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/08/2018
ms.locfileid: "35241891"
---
# <a name="summary-of-chapter-15-the-interactive-interface"></a>Resumo do capítulo 15. A interface interativa

Este capítulo explora oito `View` derivados que permitem a interação com o usuário.

## <a name="view-overview"></a>Visão geral do modo de exibição

Xamarin. Forms contém 20 classes podem ser instanciados que derivam de `View` mas não `Layout`. Seis dessas foram abordadas nos capítulos anteriores:

- `Label`: [ **Capítulo 2. Anatomia de um aplicativo**](chapter02.md)
- `BoxView`: [ **Capítulo 3. A pilha de rolagem**](chapter03.md)
- `Button`: [ **Capítulo 6. Cliques de botão**](chapter06.md)
- `Image`: [ **Capítulo 13. Bitmaps**](chapter13.md)
- `ActivityIndicator`: [ **Capítulo 13. Bitmaps**](chapter13.md)
- `ProgressBar`: [ **Capítulo 14. AbsoluteLayout**](chapter14.md)

As exibições de oito neste capítulo efetivamente permitir que o usuário interaja com tipos de dados básicos do .NET:

|Tipo de dados|Exibições|
|--- |--- |
|`Double`|[`Slider`](https://developer.xamarin.com/api/type/Xamarin.Forms.Slider/), [`Stepper`](https://developer.xamarin.com/api/type/Xamarin.Forms.Stepper/)|
|`Boolean`|[`Switch`](https://developer.xamarin.com/api/type/Xamarin.Forms.Switch/)|
|`String`|[`Entry`](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/), [`Editor`](https://developer.xamarin.com/api/type/Xamarin.Forms.Editor/), [`SearchBar`](https://developer.xamarin.com/api/type/Xamarin.Forms.SearchBar/)|
|`DateTime`|[`DatePicker`](https://developer.xamarin.com/api/type/Xamarin.Forms.DatePicker/), [`TimePicker`](https://developer.xamarin.com/api/type/Xamarin.Forms.TimePicker/)|

Você pode pensar nesses modos de exibição como representações interativas dos tipos de dados subjacente. Esse conceito é explorado mais no próximo capítulo, [ **capítulo 16. Associação de dados**](chapter16.md).

As exibições de seis restantes são abordadas nos capítulos a seguir:

- `WebView`: [ **Capítulo 16. associação de dados**](chapter16.md)
- `Picker`: [ **Capítulo 19. Exibições de coleção**](chapter19.md)
- `ListView`: [ **Capítulo 19. Exibições de coleção**](chapter19.md)
- `TableView`: [ **Capítulo 19. Exibições de coleção**](chapter19.md)
- `Map`: [ **Capítulo 28. Local e mapas**](chapter28.md)
- `OpenGLView`: Não abordado neste catálogo (e não há suporte para plataformas do Windows)

## <a name="slider-and-stepper"></a>Controle deslizante e seletor

Ambos [ `Slider` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Slider/) e [ `Stepper` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Stepper/) permitir que o usuário escolha um valor numérico de um intervalo. O `Slider` é um intervalo contínuo enquanto o `Stepper` envolve valores discretos.

### <a name="slider-basics"></a>Noções básicas sobre o controle deslizante

O [ `Slider` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Slider/) é uma barra que representa um intervalo de valores no mínimo à esquerda até um máximo à direita horizontal. Ele define três propriedades públicas:

- [`Value`](https://developer.xamarin.com/api/property/Xamarin.Forms.Slider.Value/) tipo `double`, o valor de 0 padrão
- [`Minimum`](https://developer.xamarin.com/api/property/Xamarin.Forms.Slider.Minimum/) tipo `double`, o valor de 0 padrão
- [`Maximum`](https://developer.xamarin.com/api/property/Xamarin.Forms.Slider.Maximum/) tipo `double`, o valor de 1 padrão

As propriedades vinculáveis aos que dão suporte a essas propriedades Certifique-se de que eles são consistentes:

- Para todas as três propriedades, o [ `coerceValue` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableProperty+CoerceValueDelegate/) método especificado para a propriedade associável garante que `Value` entre `Minimum` e `Maximum`.
- O [ `validateValue` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableProperty+ValidateValueDelegate/) método `MinimumProperty` retorna `false` se `Minimum` está sendo definido como um valor maior que ou igual a `Maximum`e semelhante para `MaximumProperty`. Retornando `false` do `validateValue` método faz com que um `ArgumentException` a ser gerado.

`Slider` é acionado o [ `ValueChanged` ](https://developer.xamarin.com/api/event/Xamarin.Forms.Slider.ValueChanged/) evento com um [ `ValueChangedEventArgs` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ValueChangedEventArgs/) argumento quando o `Value` alterações de propriedade, programaticamente, ou quando o usuário manipula a `Slider`.

O [ **SliderDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/SliderDemo) exemplo demonstra o uso simple de `Slider`.

### <a name="common-pitfalls"></a>Armadilhas comuns

No código e em XAML, o `Minimum` e `Maximum` propriedades são definidas na ordem em que você especificar. Certifique-se de inicializar essas propriedades para que `Maximum` sempre é maior do que `Minimum`. Se não uma exceção será lançada.

Inicializando o `Slider` propriedades podem fazer com que o `Value` propriedade a ser alterada e o `ValueChanged` evento seja acionado. Você deve garantir que o `Slider` manipulador de eventos não acessar modos de exibição que ainda não tiver sido criados durante a inicialização da página.

O `ValueChanged` evento não é acionado durante `Slider` inicialização, a menos que o `Value` alterações de propriedade. Você pode chamar o `ValueChanged` manipulador diretamente no código.

### <a name="slider-color-selection"></a>Seleção de cor do controle deslizante

O [ **RgbSliders** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/RgbSliders) programa contém três `Slider` elementos que permitem que você selecione uma cor interativamente especificando seus valores RGB:

[![Captura de tela tripla de controles deslizantes de R G B](images/ch15fg03-small.png "seletores de RGB")](images/ch15fg03-large.png#lightbox "seletores de RGB")

O [ **TextFade** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/TextFade) exemplo usa dois `Slider` elementos para mover dois `Label` elementos em um `AbsoluteLayout` e atenuar um no outro.

### <a name="the-stepper-difference"></a>A diferença do seletor

O [ `Stepper` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Stepper/) define as mesmas propriedades e eventos como `Slider` , mas o `Maximum` propriedade é inicializada como 100 e `Stepper` define uma propriedade quarta:

- [`Increment`](https://developer.xamarin.com/api/property/Xamarin.Forms.Stepper.Increment/) tipo `double`inicializados como 1

Visualmente, o `Stepper` consiste em dois botões rotulados **&ndash;** e **+**. Pressionar **&ndash;** diminui `Value` por `Increment` para um mínimo de `Minimum`. Pressionar **+** aumenta `Value` por `Increment` até um máximo de `Maximum`.

Isso é demonstrado pelo [ **StepperDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/StepperDemo) exemplo.

## <a name="switch-and-checkbox"></a>Opção e caixa de seleção

O [ `Switch` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Switch/) permite ao usuário especificar um valor booleano.

### <a name="switch-basics"></a>Noções básicas de comutador

Visualmente, o `Switch` consiste em uma alternância que pode ser ativada logoff e logon. A classe define uma propriedade:

- [`IsToggled`](https://developer.xamarin.com/api/property/Xamarin.Forms.Switch.IsToggled/) do tipo `bool`

`Switch` define um evento:

- [`Toggled`](https://developer.xamarin.com/api/event/Xamarin.Forms.Switch.Toggled/) acompanhado por um [ `ToggledEventArgs` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ToggledEventArgs/) objeto, disparado quando o `IsToggled` alterações de propriedade.

O [ **SwitchDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/SwitchDemo) programa demonstra o `Switch`.

### <a name="a-traditional-checkbox"></a>Uma caixa de seleção tradicional

Alguns desenvolvedores talvez prefira mais tradicional `CheckBox` para o `Switch`. O [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) biblioteca contém um `CheckBox` classe que deriva de `ContentView`. `CheckBox` é implementado pelo [CheckBox.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/CheckBox.xaml) e [CheckBox.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/CheckBox.xaml.cs) arquivos. `CheckBox` define três propriedades (`Text`, `FontSize`, e `IsChecked`) e um `CheckedChanged` eventos.

O [ **CheckBoxDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/CheckBoxDemo) exemplo demonstra isso `CheckBox`.

## <a name="typing-text"></a>Digitar texto

Xamarin. Forms define três exibições que permitem ao usuário digitar e editar texto:

- [`Entry`](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/) para uma única linha de texto
- [`Editor`](https://developer.xamarin.com/api/type/Xamarin.Forms.Editor/) para várias linhas de texto
- [`SearchBar`](https://developer.xamarin.com/api/type/Xamarin.Forms.SearchBar/) para uma única linha de texto para fins de pesquisa.

`Entry` e `Editor` derivam [ `InputView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.InputView/), que é derivado de `View`. `SearchBar` deriva diretamente `View`.

### <a name="keyboard-and-focus"></a>Teclado e foco

Em telefones e tablets sem teclados físicos, o `Entry`, `Editor`, e `SearchBar` todos os elementos causam um teclado virtual para pop-up. A presença desse teclado na tela está relacionada a foco de entrada. Um modo de exibição deve ter seu [ `IsVisible` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.IsVisible/) e [ `IsEnabled` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.IsEnabled/) propriedades definidas para `true` para obter o foco de entrada.

Dois métodos, uma propriedade somente leitura e dois eventos estão envolvidos com foco de entrada. Eles são definidos por `VisualElement`:

- O [ `Focus` ](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.Focus()/) tenta definir o foco de entrada a um elemento de método e retorna `true` se bem-sucedido
- O [ `Unfocus` ](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.Unfocus()/) método Remove o foco de entrada de um elemento
- O [ `IsFocused` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.IsFocused/) propriedade somente leitura que indica se o elemento tem foco de entrada
- O [ `Focused` ](https://developer.xamarin.com/api/event/Xamarin.Forms.VisualElement.Focused/) evento indica quando um elemento obtém o foco de entrada
- O [ `Unfocused` ](https://developer.xamarin.com/api/event/Xamarin.Forms.VisualElement.Unfocused/) evento indica quando um elemento perde o foco de entrada

### <a name="choosing-the-keyboard"></a>Escolhendo o teclado

O [ `InputView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.InputView/) classe da qual `Entry` e `Editor` derivar define apenas uma propriedade:

- [`Keyboard`](https://developer.xamarin.com/api/property/Xamarin.Forms.InputView.Keyboard/) do tipo [`Keyboard`](https://developer.xamarin.com/api/type/Xamarin.Forms.Keyboard/)

Isso indica o tipo de teclado que é exibido. Alguns teclados são otimizados para URIs ou números.

O `Keyboard` classe permite definir um teclado com uma estática [ `Keyboard.Create` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Keyboard.Create/p/Xamarin.Forms.KeyboardFlags/) método com um argumento de tipo [ `KeyboardFlags` ](https://developer.xamarin.com/api/type/Xamarin.Forms.KeyboardFlags/), uma enumeração com os sinalizadores de bit a seguir:

- `None` definido como 0
- [`CapitalizeSentence`](https://developer.xamarin.com/api/field/Xamarin.Forms.KeyboardFlags.CapitalizeSentence/) definido como 1
- [`Spellcheck`](https://developer.xamarin.com/api/field/Xamarin.Forms.KeyboardFlags.Spellcheck/) definido como 2
- [`Suggestions`](https://developer.xamarin.com/api/field/Xamarin.Forms.KeyboardFlags.Suggestions/) definido como 4
- [`All`](https://developer.xamarin.com/api/field/Xamarin.Forms.KeyboardFlags.All/) definido como \xFFFFFFFF

Ao usar o multiline [ `Editor` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Editor/) quando se espera que um parágrafo ou mais de texto, chamando `Keyboard.Create` é uma boa abordagem para selecionar um teclado. Para a linha [ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/), as seguintes propriedades estáticas de somente leitura do `Keyboard` são úteis:

- [`Default`](https://developer.xamarin.com/api/property/Xamarin.Forms.Keyboard.Default/)
- [`Text`](https://developer.xamarin.com/api/property/Xamarin.Forms.Keyboard.Text/)
- [`Chat`](https://developer.xamarin.com/api/property/Xamarin.Forms.Keyboard.Chat/)
- [`Url`](https://developer.xamarin.com/api/property/Xamarin.Forms.Keyboard.Url/)
- [`Email`](https://developer.xamarin.com/api/property/Xamarin.Forms.Keyboard.Email/)
- [`Telephone`](https://developer.xamarin.com/api/property/Xamarin.Forms.Keyboard.Telephone/)
- [`Numeric`](https://developer.xamarin.com/api/property/Xamarin.Forms.Keyboard.Numeric/) para números positivos, com ou sem um ponto decimal.

O [ `KeyboardTypeConverter` ](https://developer.xamarin.com/api/type/Xamarin.Forms.KeyboardTypeConverter/) permite especificar essas propriedades em XAML, conforme demonstrado a [ **EntryKeyboards** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/EntryKeyboards) programa.

### <a name="entry-properties-and-events"></a>Eventos e propriedades de entrada

A linha [ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/) define as propriedades a seguir:

- [`Text`](https://developer.xamarin.com/api/property/Xamarin.Forms.Entry.Text/) tipo `string`, o texto que aparece no `Entry`
- [`TextColor`](https://developer.xamarin.com/api/property/Xamarin.Forms.Entry.TextColor/) do tipo `Color`
- [`FontFamily`](https://developer.xamarin.com/api/property/Xamarin.Forms.Entry.FontFamily/) do tipo `string`
- [`FontSize`](https://developer.xamarin.com/api/property/Xamarin.Forms.Entry.FontSize/) do tipo `double`
- [`FontAttributes`](https://developer.xamarin.com/api/property/Xamarin.Forms.Entry.FontAttributes/) do tipo `FontAttributes`
- [`IsPassword`](https://developer.xamarin.com/api/property/Xamarin.Forms.Entry.IsPassword/) tipo `bool`, que faz com que os caracteres a ser mascarado
- [`Placeholder`](https://developer.xamarin.com/api/property/Xamarin.Forms.Entry.Placeholder/) do tipo `string`, dimly colorida texto que aparece no `Entry` antes de qualquer coisa é digitada
- [`PlaceholderColor`](https://developer.xamarin.com/api/property/Xamarin.Forms.Entry.PlaceholderColor/) do tipo `Color`

O `Entry` também define dois eventos:

- [`TextChanged`](https://developer.xamarin.com/api/event/Xamarin.Forms.Entry.TextChanged/) com um [ `TextChangedEventArgs` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TextChangedEventArgs/) objeto, disparado sempre que o `Text` alterações de propriedade
- [`Completed`](https://developer.xamarin.com/api/event/Xamarin.Forms.Entry.Completed/), acionado quando o usuário for concluído e o teclado é descartado. O usuário indica a conclusão de uma maneira específica de plataforma

O [ **QuadraticEquations** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/QuadaticEquations) demonstra esses dois eventos.

### <a name="the-editor-difference"></a>A diferença de Editor

O multiline [ `Editor` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Editor/) define o mesmo `Text` e `Font` propriedades como `Entry` , mas não as outras propriedades. `Editor` também define as mesmas duas propriedades `Entry`.

[**JustNotes** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/JustNotes) é um programa de fazer anotações de forma livre que salva e restaura o conteúdo do `Editor`.

### <a name="the-searchbar"></a>O SearchBar

O [ `SearchBar` ](https://developer.xamarin.com/api/type/Xamarin.Forms.SearchBar/) não deriva de `InputView`, portanto, ele não tem um `Keyboard` propriedade. Mas ele tem todas as `Text`, `Font`, e `Placeholder` propriedades que `Entry` define. Além disso, `SearchBar` define três propriedades adicionais:

- [`CancelButtonColor`](https://developer.xamarin.com/api/property/Xamarin.Forms.SearchBar.CancelButtonColor/) do tipo `Color`
- [`SearchCommand`](https://developer.xamarin.com/api/property/Xamarin.Forms.SearchBar.SearchCommand/) tipo [ `ICommand` ](https://developer.xamarin.com/api/type/System.Windows.Input.ICommand/) para uso com associações de dados e MVVM
- [`SearchCommandParameter`](https://developer.xamarin.com/api/property/Xamarin.Forms.SearchBar.SearchCommandParameter/) tipo `Object`, para uso com `SearchCommand`

A plataforma específica Cancelar botão apaga o texto. O `SearchBar` também tem um botão de pesquisa específicos da plataforma. Pressionar qualquer um desses botões gera um dos dois eventos que `SearchBar` define:

- [`TextChanged`](https://developer.xamarin.com/api/event/Xamarin.Forms.SearchBar.TextChanged/) acompanhado por um [ `TextChangedEventArgs` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TextChangedEventArgs/) objeto
- [`SearchButtonPressed`](https://developer.xamarin.com/api/event/Xamarin.Forms.SearchBar.SearchButtonPressed/)

O [ **SearchBarDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/SearchBarDemo) demonstra o `SearchBar`.

## <a name="date-and-time-selection"></a>Seleção de data e hora

O [ `DatePicker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DatePicker/) e [ `TimePicker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TimePicker/) exibições implementam controles de específico de plataforma que permite que o usuário especifique uma data ou hora.

### <a name="the-datepicker"></a>O selecionador de data

[`DatePicker`](https://developer.xamarin.com/api/type/Xamarin.Forms.DatePicker/) define quatro propriedades:

- [`MinimumDate`](https://developer.xamarin.com/api/property/Xamarin.Forms.DatePicker.MinimumDate/) tipo `DateTime`, inicializado a partir de 1 de janeiro de 1900
- [`MaximumDate`](https://developer.xamarin.com/api/property/Xamarin.Forms.DatePicker.MaximumDate/) tipo `DateTime`, inicializado a 31 de dezembro de 2100
- [`Date`](https://developer.xamarin.com/api/property/Xamarin.Forms.DatePicker.Date/) tipo `DateTime`, inicializado para `DateTime.Today`
- [`Format`](https://developer.xamarin.com/api/property/Xamarin.Forms.DatePicker.Format/) tipo `string`, o .NET inicializada como "d", o padrão de data abreviada, de cadeia de caracteres de formatação, resultando em uma exibição de data como "7/20/1969" nos EUA.

Você pode definir o `DateTime` propriedades em XAML expressar as propriedades de elementos de propriedade e usando o cultura invariável abreviado de data de formato ("7/20/1969").   

O [ **DaysBetweenDates** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/DaysBetweenDates) exemplo calcula o número de dias entre duas datas selecionadas pelo usuário.

### <a name="the-timepicker-or-is-it-a-timespanpicker"></a>O TimePicker (ou é um TimeSpanPicker?)

[`TimePicker`](https://developer.xamarin.com/api/type/Xamarin.Forms.TimePicker/) define duas propriedades e não há eventos:

- [`Time`](https://developer.xamarin.com/api/property/Xamarin.Forms.TimePicker.Time/) é do tipo `TimeSpan` em vez de `DateTime`, que indica o tempo decorrido desde a meia-noite
- [`Format`](https://developer.xamarin.com/api/property/Xamarin.Forms.TimePicker.Format/) tipo `string`, o .NET inicializada como "t", o padrão de tempo curto, resultando em uma exibição de tempo como "1:45 PM" de cadeia de caracteres de formatação nos EUA.

O [ **SetTimer** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/SetTimer) programa demonstra como usar o `TimePicker` para especificar uma hora para um timer. O programa só funcionará se mantê-la em primeiro plano.

**SetTimer** também demonstra como usar o [ `DisplayAlert` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.DisplayAlert/p/System.String/System.String/System.String/) método `Page` para exibir uma caixa de alerta.



## <a name="related-links"></a>Links relacionados

- [Texto completo do capítulo 15 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch15-Apr2016.pdf)
- [Exemplos de capítulo 15](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15)
- [Entry](~/xamarin-forms/user-interface/text/entry.md)
- [Editor](~/xamarin-forms/user-interface/text/editor.md)
