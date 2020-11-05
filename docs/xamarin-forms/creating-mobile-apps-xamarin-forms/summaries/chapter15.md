---
title: Resumo do capítulo 15. A interface interativa
description: 'Criando aplicativos móveis com Xamarin.Forms : Resumo do capítulo 15. A interface interativa'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: F54E86F4-1CDA-474E-9B09-242060C2C13D
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 08c5152c5618b7edcfe22bc288a1a0354e3e5fed
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93374245"
---
# <a name="summary-of-chapter-15-the-interactive-interface"></a>Resumo do capítulo 15. A interface interativa

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15)

> [!NOTE]
> Este livro foi publicado na Primavera de 2016 e não foi atualizado desde então. Há muito no livro que permanece valioso, mas alguns materiais estão desatualizados e alguns tópicos não estão mais totalmente corretos ou completos.

Este capítulo explora oito `View` derivações que permitem a interação com o usuário.

## <a name="view-overview"></a>Exibir visão geral

Xamarin.Forms contém 20 classes instanciáveis que derivam de `View` , mas não `Layout` . Seis deles foram abordados nos capítulos anteriores:

- `Label`: [ **Capítulo 2. Anatomia de um aplicativo**](chapter02.md)
- `BoxView`: [ **Capítulo 3. Rolando a pilha**](chapter03.md)
- `Button`: [ **Capítulo 6. Cliques de botão**](chapter06.md)
- `Image`: [ **Capítulo 13. Bitmaps**](chapter13.md)
- `ActivityIndicator`: [ **Capítulo 13. Bitmaps**](chapter13.md)
- `ProgressBar`: [ **Capítulo 14. AbsoluteLayout**](chapter14.md)

Os oito modos de exibição deste capítulo efetivamente permitem que o usuário interaja com os tipos de dados básicos do .NET:

|Tipo de dados|Exibições|
|--- |--- |
|`Double`|[`Slider`](xref:Xamarin.Forms.Slider), [`Stepper`](xref:Xamarin.Forms.Stepper)|
|`Boolean`|[`Switch`](xref:Xamarin.Forms.Switch)|
|`String`|[`Entry`](xref:Xamarin.Forms.Entry), [`Editor`](xref:Xamarin.Forms.Editor), [`SearchBar`](xref:Xamarin.Forms.SearchBar)|
|`DateTime`|[`DatePicker`](xref:Xamarin.Forms.DatePicker), [`TimePicker`](xref:Xamarin.Forms.TimePicker)|

Você pode considerar essas exibições como representações interativas do Visual dos tipos de dados subjacentes. Esse conceito é explorado mais no próximo capítulo [**16. Associação de dados**](chapter16.md).

Os seis modos de exibição restantes são abordados nos seguintes capítulos:

- `WebView`: [ **Capítulo 16. Associação de dados**](chapter16.md)
- `Picker`: [ **Capítulo 19. Exibições de coleção**](chapter19.md)
- `ListView`: [ **Capítulo 19. Exibições de coleção**](chapter19.md)
- `TableView`: [ **Capítulo 19. Exibições de coleção**](chapter19.md)
- `Map`: [ **Capítulo 28. Local e mapas**](chapter28.md)
- `OpenGLView`: Não abordado neste livro (e não há suporte para plataformas Windows)

## <a name="slider-and-stepper"></a>Controle deslizante e stepper

Ambos [`Slider`](xref:Xamarin.Forms.Slider) e [`Stepper`](xref:Xamarin.Forms.Stepper) permitem que o usuário escolha um valor numérico de um intervalo. O `Slider` é um intervalo contínuo enquanto o `Stepper` envolve valores discretos.

### <a name="slider-basics"></a>Noções básicas do controle deslizante

O [`Slider`](xref:Xamarin.Forms.Slider) é uma barra horizontal que representa um intervalo de valores de um mínimo à esquerda para um máximo à direita. Ele define três propriedades públicas:

- [`Value`](xref:Xamarin.Forms.Slider.Value) do tipo `double` , valor padrão de 0
- [`Minimum`](xref:Xamarin.Forms.Slider.Minimum) do tipo `double` , valor padrão de 0
- [`Maximum`](xref:Xamarin.Forms.Slider.Maximum) do tipo `double` , valor padrão de 1

As propriedades vinculáveis que retornam essas propriedades garantem que elas sejam consistentes:

- Para todas as três propriedades, o [`coerceValue`](xref:Xamarin.Forms.BindableProperty.CoerceValueDelegate) método especificado para a propriedade vinculável garante que `Value` esteja entre `Minimum` e `Maximum` .
- O [`validateValue`](xref:Xamarin.Forms.BindableProperty.ValidateValueDelegate) método em `MinimumProperty` retorna `false` se `Minimum` está sendo definido como um valor maior ou igual a `Maximum` , e semelhante para `MaximumProperty` . Retornar `false` do `validateValue` método faz com que um seja `ArgumentException` gerado.

`Slider` dispara o [`ValueChanged`](xref:Xamarin.Forms.Slider.ValueChanged) evento com um [`ValueChangedEventArgs`](xref:Xamarin.Forms.ValueChangedEventArgs) argumento quando a `Value` propriedade é alterada, seja programaticamente ou quando o usuário manipula o `Slider` .

O exemplo [**SliderDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/SliderDemo) demonstra o uso simples do `Slider` .

### <a name="common-pitfalls"></a>Armadilhas comuns

Tanto no código quanto no XAML, as `Minimum` `Maximum` Propriedades e são definidas na ordem especificada. Certifique-se de inicializar essas propriedades para que `Maximum` seja sempre maior que `Minimum` . Se não uma exceção será lançada.

A inicialização das `Slider` Propriedades pode fazer com que a `Value` propriedade seja alterada e o `ValueChanged` evento seja acionado. Você deve garantir que o `Slider` manipulador de eventos não acesse exibições que ainda não foram criadas durante a inicialização da página.

O `ValueChanged` evento não é acionado durante `Slider` a inicialização, a menos que a `Value` propriedade seja alterada. Você pode chamar o `ValueChanged` manipulador diretamente do código.

### <a name="slider-color-selection"></a>Seleção de cor do controle deslizante

O programa [**RgbSliders**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/RgbSliders) contém três `Slider` elementos que permitem selecionar uma cor interativamente, ESPECIFICANDO seus valores RGB:

[![Captura de tela tripla de controles deslizantes do R G B](images/ch15fg03-small.png "Controles deslizantes RGB")](images/ch15fg03-large.png#lightbox "Controles deslizantes RGB")

O exemplo [**textfade**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/TextFade) usa dois `Slider` elementos para mover dois `Label` elementos em um `AbsoluteLayout` e esmaecer um para o outro.

### <a name="the-stepper-difference"></a>A diferença de stepper

O [`Stepper`](xref:Xamarin.Forms.Stepper) define as mesmas propriedades e eventos, `Slider` mas a `Maximum` propriedade é inicializada como 100 e `Stepper` define uma quarta Propriedade:

- [`Increment`](xref:Xamarin.Forms.Stepper.Increment) do tipo `double` , inicializado como 1

Visualmente, o `Stepper` consiste em dois botões rotulados **&ndash;** e **+** . Pressionar **&ndash;** diminui `Value` `Increment` em até um mínimo de `Minimum` . Pressionar **+** aumenta `Value` em `Increment` até um máximo de `Maximum` .

Isso é demonstrado pelo exemplo de [**StepperDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/StepperDemo) .

## <a name="switch-and-checkbox"></a>Alternar e caixa de seleção

O [`Switch`](xref:Xamarin.Forms.Switch) permite que o usuário especifique um valor booliano.

### <a name="switch-basics"></a>Opções básicas

Visualmente, o `Switch` consiste em uma alternância que pode ser desativada e ativada. A classe define uma propriedade:

- [`IsToggled`](xref:Xamarin.Forms.Switch.IsToggled) do tipo `bool`

`Switch` define um evento:

- [`Toggled`](xref:Xamarin.Forms.Switch.Toggled) acompanhado por um [`ToggledEventArgs`](xref:Xamarin.Forms.ToggledEventArgs) objeto, acionado quando a `IsToggled` propriedade é alterada.

O programa [**SwitchDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/SwitchDemo) demonstra o `Switch` .

### <a name="a-traditional-checkbox"></a>Uma caixa de seleção tradicional

Alguns desenvolvedores podem preferir um mais tradicional `CheckBox` para o `Switch` . A biblioteca [**Xamarin.Forms book. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) contém uma `CheckBox` classe derivada de `ContentView` . `CheckBox` é implementado pelos arquivos [CheckBox. XAML](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/CheckBox.xaml) e [CheckBox.XAML.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/CheckBox.xaml.cs) . `CheckBox` define três propriedades ( `Text` , `FontSize` , e `IsChecked` ) e um `CheckedChanged` evento.

O exemplo [**CheckBoxDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/CheckBoxDemo) demonstra isso `CheckBox` .

## <a name="typing-text"></a>Digitando texto

Xamarin.Forms define três exibições que permitem que o usuário insira e edite o texto:

- [`Entry`](xref:Xamarin.Forms.Entry) para uma única linha de texto
- [`Editor`](xref:Xamarin.Forms.Editor) para várias linhas de texto
- [`SearchBar`](xref:Xamarin.Forms.SearchBar) para uma única linha de texto para fins de pesquisa.

`Entry` e `Editor` derive de [`InputView`](xref:Xamarin.Forms.InputView) , que deriva de `View` . `SearchBar` deriva diretamente de `View` .

### <a name="keyboard-and-focus"></a>Teclado e foco

Em telefones e tablets sem teclados físicos, os `Entry` `Editor` elementos, e, `SearchBar` todos fazem com que uma barra virtual apareça. A presença desse teclado na tela está relacionada ao foco de entrada. Uma exibição deve ter as [`IsVisible`](xref:Xamarin.Forms.VisualElement.IsVisible) Propriedades e [`IsEnabled`](xref:Xamarin.Forms.VisualElement.IsEnabled) definidas como `true` para obter o foco de entrada.

Dois métodos, uma propriedade somente leitura e dois eventos estão envolvidos no foco de entrada. Todos eles são definidos por `VisualElement` :

- O [`Focus`](xref:Xamarin.Forms.VisualElement.Focus) método tenta definir o foco de entrada para um elemento e retorna `true` se for bem-sucedido
- O [`Unfocus`](xref:Xamarin.Forms.VisualElement.Unfocus) método remove o foco de entrada de um elemento
- A [`IsFocused`](xref:Xamarin.Forms.VisualElement.IsFocused) propriedade somente leitura indica se o elemento tem foco de entrada
- O [`Focused`](xref:Xamarin.Forms.VisualElement.Focused) evento indica quando um elemento Obtém o foco de entrada
- O [`Unfocused`](xref:Xamarin.Forms.VisualElement.Unfocused) evento indica quando um elemento perde o foco de entrada

### <a name="choosing-the-keyboard"></a>Escolhendo o teclado

A [`InputView`](xref:Xamarin.Forms.InputView) classe da qual `Entry` e `Editor` derivar define apenas uma propriedade:

- [`Keyboard`](xref:Xamarin.Forms.InputView.Keyboard) do tipo [`Keyboard`](xref:Xamarin.Forms.Keyboard)

Isso indica o tipo de teclado exibido. Alguns teclados são otimizados para URIs ou números.

A `Keyboard` classe permite definir um teclado com um static [ `Keyboard.Create` ] (xref: Xamarin.Forms . Keyboard. Create ( Xamarin.Forms . KeyboardFlags)) com um argumento do tipo [`KeyboardFlags`](xref:Xamarin.Forms.KeyboardFlags) , uma enumeração com os seguintes sinalizadores de bits:

- `None` definir como 0
- [`CapitalizeSentence`](xref:Xamarin.Forms.KeyboardFlags.CapitalizeSentence) definir como 1
- [`Spellcheck`](xref:Xamarin.Forms.KeyboardFlags.Spellcheck) definir como 2
- [`Suggestions`](xref:Xamarin.Forms.KeyboardFlags.Suggestions) definir como 4
- [`All`](xref:Xamarin.Forms.KeyboardFlags.All) definir como \xFFFFFFFF

Ao usar a multilinha [`Editor`](xref:Xamarin.Forms.Editor) quando um parágrafo ou mais texto é esperado, a chamada `Keyboard.Create` é uma boa abordagem para selecionar um teclado. Para a linha única [`Entry`](xref:Xamarin.Forms.Entry) , as seguintes propriedades somente leitura estáticas do `Keyboard` são úteis:

- [`Default`](xref:Xamarin.Forms.Keyboard.Default)
- [`Text`](xref:Xamarin.Forms.Keyboard.Text)
- [`Chat`](xref:Xamarin.Forms.Keyboard.Chat)
- [`Url`](xref:Xamarin.Forms.Keyboard.Url)
- [`Email`](xref:Xamarin.Forms.Keyboard.Email)
- [`Telephone`](xref:Xamarin.Forms.Keyboard.Telephone)
- [`Numeric`](xref:Xamarin.Forms.Keyboard.Numeric) para números positivos com ou sem um ponto decimal.

O [`KeyboardTypeConverter`](xref:Xamarin.Forms.KeyboardTypeConverter) permite especificar essas propriedades em XAML, conforme demonstrado pelo programa [**EntryKeyboards**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/EntryKeyboards) .

### <a name="entry-properties-and-events"></a>Propriedades de entrada e eventos

A linha única [`Entry`](xref:Xamarin.Forms.Entry) define as seguintes propriedades:

- [`Text`](xref:Xamarin.Forms.InputView.Text) do tipo `string` , o texto que aparece na `Entry`
- [`TextColor`](xref:Xamarin.Forms.InputView.TextColor) do tipo `Color`
- [`FontFamily`](xref:Xamarin.Forms.Entry.FontFamily) do tipo `string`
- [`FontSize`](xref:Xamarin.Forms.Entry.FontSize) do tipo `double`
- [`FontAttributes`](xref:Xamarin.Forms.Entry.FontAttributes) do tipo `FontAttributes`
- [`IsPassword`](xref:Xamarin.Forms.Entry.IsPassword) do tipo `bool` , que faz com que os caracteres sejam mascarados
- [`Placeholder`](xref:Xamarin.Forms.InputView.Placeholder) do tipo `string` , para Dimly texto colorido que aparece no `Entry` antes de qualquer coisa ser digitado
- [`PlaceholderColor`](xref:Xamarin.Forms.InputView.PlaceholderColor) do tipo `Color`

O `Entry` também define dois eventos:

- [`TextChanged`](xref:Xamarin.Forms.InputView.TextChanged) com um [`TextChangedEventArgs`](xref:Xamarin.Forms.TextChangedEventArgs) objeto, disparado sempre que a `Text` propriedade é alterada
- [`Completed`](xref:Xamarin.Forms.Entry.Completed), acionado quando o usuário é concluído e o teclado é Descartado. O usuário indica a conclusão de forma específica da plataforma

O exemplo [**QuadraticEquations**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/QuadaticEquations) demonstra esses dois eventos.

### <a name="the-editor-difference"></a>A diferença do editor

A Multiline [`Editor`](xref:Xamarin.Forms.Editor) define as mesmas `Text` `Font` Propriedades e `Entry` , mas não as outras propriedades. `Editor` também define as mesmas duas propriedades como `Entry` .

[**JustNotes**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/JustNotes) é um programa de anotações de forma livre que salva e restaura o conteúdo do `Editor` .

### <a name="the-searchbar"></a>O SearchBar

O não [`SearchBar`](xref:Xamarin.Forms.SearchBar) deriva de `InputView` , portanto, não tem uma `Keyboard` propriedade. Mas ele tem todas as `Text` Propriedades, `Font` e `Placeholder` que `Entry` define. Além disso, o `SearchBar` define três propriedades adicionais:

- [`CancelButtonColor`](xref:Xamarin.Forms.SearchBar.CancelButtonColor) do tipo `Color`
- [`SearchCommand`](xref:Xamarin.Forms.SearchBar.SearchCommand) do tipo [`ICommand`](xref:System.Windows.Input.ICommand) para uso com associações de dados e MVVM
- [`SearchCommandParameter`](xref:Xamarin.Forms.SearchBar.SearchCommandParameter) do tipo `Object` , para uso com `SearchCommand`

O botão Cancelar específico da plataforma apaga o texto. O `SearchBar` também tem um botão de pesquisa específico da plataforma. Pressionar qualquer um desses botões gera um dos dois eventos que `SearchBar` define:

- [`TextChanged`](xref:Xamarin.Forms.InputView.TextChanged) acompanhado por um [`TextChangedEventArgs`](xref:Xamarin.Forms.TextChangedEventArgs) objeto
- [`SearchButtonPressed`](xref:Xamarin.Forms.SearchBar.SearchButtonPressed)

O exemplo [**SearchBarDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/SearchBarDemo) demonstra o `SearchBar` .

## <a name="date-and-time-selection"></a>Seleção de data e hora

As [`DatePicker`](xref:Xamarin.Forms.DatePicker) [`TimePicker`](xref:Xamarin.Forms.TimePicker) exibições e implementam controles específicos da plataforma que permitem ao usuário especificar uma data ou hora.

### <a name="the-datepicker"></a>O DatePicker

[`DatePicker`](xref:Xamarin.Forms.DatePicker) define quatro propriedades:

- [`MinimumDate`](xref:Xamarin.Forms.DatePicker.MinimumDate) do tipo `DateTime` , inicializado para 1º de janeiro de 1900
- [`MaximumDate`](xref:Xamarin.Forms.DatePicker.MaximumDate) do tipo `DateTime` , inicializado para 31 de dezembro de 2100
- [`Date`](xref:Xamarin.Forms.DatePicker.Date) do tipo `DateTime` , inicializado para `DateTime.Today`
- [`Format`](xref:Xamarin.Forms.DatePicker.Format) do tipo `string` , a cadeia de caracteres de formatação .net inicializada como "d", o padrão de data abreviada, resultando em uma exibição de data como "7/20/1969" nos EUA.

Você pode definir as `DateTime` Propriedades em XAML, expressando as propriedades como elementos de propriedade e usando o formato de data invariável de cultura ("7/20/1969").   

O exemplo de [**DaysBetweenDates**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/DaysBetweenDates) calcula o número de dias entre duas datas selecionadas pelo usuário.

### <a name="the-timepicker-or-is-it-a-timespanpicker"></a>O TimeSeparator (ou é um TimeSpanPicker?)

[`TimePicker`](xref:Xamarin.Forms.TimePicker) define duas propriedades e nenhum evento:

- [`Time`](xref:Xamarin.Forms.TimePicker.Time) é do tipo `TimeSpan` em vez de `DateTime` , indicando o tempo decorrido desde a meia-noite
- [`Format`](xref:Xamarin.Forms.TimePicker.Format) do tipo `string` , a cadeia de caracteres de formatação .net inicializada como "t", o padrão de tempo curto, resultando em uma exibição de tempo como "1:45 PM" nos EUA.

O programa [**SetTimer**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/SetTimer) demonstra como usar o `TimePicker` para especificar um horário para um temporizador. O programa só funcionará se você o mantiver em primeiro plano.

**SetTimer** também demonstra o uso do [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert(System.String,System.String,System.String)) método de `Page` para exibir uma caixa de alerta.

## <a name="related-links"></a>Links relacionados

- [Capítulo 15 texto completo (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch15-Apr2016.pdf)
- [Capítulo 15 amostras](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15)
- [Controle deslizante](~/xamarin-forms/user-interface/slider.md)
- [Entrada](~/xamarin-forms/user-interface/text/entry.md)
- [Editor](~/xamarin-forms/user-interface/text/editor.md)
- [DatePicker](~/xamarin-forms/user-interface/datepicker.md)
