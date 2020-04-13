---
title: Resumo do Capítulo 15. A interface interativa
description: 'Criando aplicativos móveis com Xamarin.Forms: Resumo do Capítulo 15. A interface interativa'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: F54E86F4-1CDA-474E-9B09-242060C2C13D
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2017
ms.openlocfilehash: 5f96d2f4b619bbb10bb58e9b1b5dc7007c1ce888
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "78291734"
---
# <a name="summary-of-chapter-15-the-interactive-interface"></a>Resumo do Capítulo 15. A interface interativa

[![Baixar](~/media/shared/download.png) amostra Baixar a amostra](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15)

Este capítulo explora `View` oito derivativos que permitem a interação com o usuário.

## <a name="view-overview"></a>Ver visão geral

Xamarin.Forms contém 20 classes instantivas que derivam, `View` mas não `Layout`. Seis deles foram abordados em capítulos anteriores:

- `Label`: [ **Capítulo 2. Anatomia de um aplicativo**](chapter02.md)
- `BoxView`: [ **Capítulo 3. Rolando a pilha**](chapter03.md)
- `Button`: [ **Capítulo 6. Cliques de botão**](chapter06.md)
- `Image`: [ **Capítulo 13. Bitmaps**](chapter13.md)
- `ActivityIndicator`: [ **Capítulo 13. Bitmaps**](chapter13.md)
- `ProgressBar`: [ **Capítulo 14. AbsoluteLayout**](chapter14.md)

As oito visualizações deste capítulo permitem efetivamente que o usuário interaja com os tipos básicos de dados .NET:

|Tipo de dados|Exibições|
|--- |--- |
|`Double`|[`Slider`](xref:Xamarin.Forms.Slider), [`Stepper`](xref:Xamarin.Forms.Stepper)|
|`Boolean`|[`Switch`](xref:Xamarin.Forms.Switch)|
|`String`|[`Entry`](xref:Xamarin.Forms.Entry), [`Editor`](xref:Xamarin.Forms.Editor), [`SearchBar`](xref:Xamarin.Forms.SearchBar)|
|`DateTime`|[`DatePicker`](xref:Xamarin.Forms.DatePicker), [`TimePicker`](xref:Xamarin.Forms.TimePicker)|

Você pode pensar nessas visões como representações interativas visuais dos tipos de dados subjacentes. Este conceito é mais explorado no próximo capítulo, [**Capítulo 16. Vinculação de dados**](chapter16.md).

As seis visualizações restantes estão abordadas nos seguintes capítulos:

- `WebView`: [ **Capítulo 16. Vinculação de dados**](chapter16.md)
- `Picker`: [ **Capítulo 19. Visualizações de coleção**](chapter19.md)
- `ListView`: [ **Capítulo 19. Visualizações de coleção**](chapter19.md)
- `TableView`: [ **Capítulo 19. Visualizações de coleção**](chapter19.md)
- `Map`: [ **Capítulo 28. Localização e Mapas**](chapter28.md)
- `OpenGLView`: Não coberto neste livro (e sem suporte para plataformas Windows)

## <a name="slider-and-stepper"></a>Controle deslizante e passo

Ambos [`Slider`](xref:Xamarin.Forms.Slider) [`Stepper`](xref:Xamarin.Forms.Stepper) permitem que o usuário escolha um valor numérico de uma faixa. O `Slider` é um intervalo `Stepper` contínuo, enquanto o envolve valores discretos.

### <a name="slider-basics"></a>Noções básicas de controle deslizante

A [`Slider`](xref:Xamarin.Forms.Slider) é uma barra horizontal representando uma faixa de valores de um mínimo à esquerda para um máximo à direita. Define três propriedades públicas:

- [`Value`](xref:Xamarin.Forms.Slider.Value)de `double`tipo, valor padrão de 0
- [`Minimum`](xref:Xamarin.Forms.Slider.Minimum)de `double`tipo, valor padrão de 0
- [`Maximum`](xref:Xamarin.Forms.Slider.Maximum)de `double`tipo, valor padrão de 1

As propriedades vinculáveis que resfazem essas propriedades garantem que elas são consistentes:

- Para todas as [`coerceValue`](xref:Xamarin.Forms.BindableProperty.CoerceValueDelegate) três propriedades, o método especificado `Value` para `Minimum` `Maximum`a propriedade vinculável garante que esteja entre e .
- O [`validateValue`](xref:Xamarin.Forms.BindableProperty.ValidateValueDelegate) método `MinimumProperty` `false` de `Minimum` devolução se está sendo definido `Maximum`para um `MaximumProperty`valor maior ou igual a , e semelhante para . Voltar `false` do `validateValue` método faz `ArgumentException` com que um seja levantado.

`Slider`despede [`ValueChanged`](xref:Xamarin.Forms.Slider.ValueChanged) o [`ValueChangedEventArgs`](xref:Xamarin.Forms.ValueChangedEventArgs) evento `Value` com um argumento quando a propriedade muda, `Slider`seja programática ou quando o usuário manipula o .

A amostra [**SliderDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/SliderDemo) demonstra o `Slider`simples uso do .

### <a name="common-pitfalls"></a>Armadilhas comuns

Tanto em código quanto em `Minimum` XAML, as propriedades e propriedades `Maximum` são definidas na ordem que você especificar. Certifique-se de inicializar essas `Maximum` propriedades de `Minimum`modo que seja sempre maior do que . Se não uma exceção será lançada.

A inicialização das `Slider` `Value` propriedades pode fazer `ValueChanged` com que a propriedade mude e o evento seja acionado. Você deve garantir `Slider` que o manipulador de eventos não acesse visualizações que ainda não foram criadas durante a inicialização da página.

O `ValueChanged` evento não é `Slider` acionado durante `Value` a inicialização, a menos que a propriedade mude. Você pode `ValueChanged` ligar diretamente para o manipulador do código.

### <a name="slider-color-selection"></a>Seleção de cores deslizantes

O programa [**RgbSliders**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/RgbSliders) contém três `Slider` elementos que permitem selecionar interativamente uma cor especificando seus valores RGB:

[![Captura de tela tripla de controles deslizantes R G B](images/ch15fg03-small.png "Controles deslizantes RGB")](images/ch15fg03-large.png#lightbox "Controles deslizantes RGB")

A amostra [**TextFade**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/TextFade) usa `Slider` dois `Label` elementos `AbsoluteLayout` para mover dois elementos através de um e desaparecer um para o outro.

### <a name="the-stepper-difference"></a>A diferença de Passo

O [`Stepper`](xref:Xamarin.Forms.Stepper) conjunto define as mesmas propriedades e eventos, `Slider` mas a `Maximum` propriedade é inicializada em 100 e `Stepper` define uma quarta propriedade:

- [`Increment`](xref:Xamarin.Forms.Stepper.Increment)do `double`tipo, inicializado para 1

Visualmente, `Stepper` consiste em dois botões **&ndash;** rotulados e **+**. Pressionar **&ndash;** diminui `Value` `Increment` em até `Minimum`um mínimo de . Pressionar **+** `Value` aumenta `Increment` até um `Maximum`máximo de .

Isso é demonstrado pela amostra [**StepperDemo.**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/StepperDemo)

## <a name="switch-and-checkbox"></a>Switch e Caixa de Verificação

O [`Switch`](xref:Xamarin.Forms.Switch) permite que o usuário especifique um valor booleano.

### <a name="switch-basics"></a>Switch basics

Visualmente, `Switch` consiste em um alternador que pode ser desligado e ligado. A classe define uma propriedade:

- [`IsToggled`](xref:Xamarin.Forms.Switch.IsToggled)de tipo`bool`

`Switch`define um evento:

- [`Toggled`](xref:Xamarin.Forms.Switch.Toggled)acompanhado por [`ToggledEventArgs`](xref:Xamarin.Forms.ToggledEventArgs) um objeto, `IsToggled` disparado quando a propriedade muda.

O programa [**SwitchDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/SwitchDemo) demonstra o `Switch`.

### <a name="a-traditional-checkbox"></a>Uma Caixa de Verificação tradicional

Alguns desenvolvedores podem `CheckBox` preferir `Switch`um mais tradicional ao . A biblioteca [**Xamarin.FormsBook.Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) contém uma `CheckBox` `ContentView`classe que deriva de . `CheckBox`é implementado pelos arquivos [CheckBox.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/CheckBox.xaml) e [CheckBox.xaml.cs.](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/CheckBox.xaml.cs) `CheckBox`define três propriedades`Text` `FontSize`( `IsChecked`, e `CheckedChanged` ) e um evento.

A amostra [**CheckBoxDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/CheckBoxDemo) demonstra isso `CheckBox`.

## <a name="typing-text"></a>Digitando texto

Xamarin.Forms define três visualizações que permitem ao usuário inserir e editar texto:

- [`Entry`](xref:Xamarin.Forms.Entry)para uma única linha de texto
- [`Editor`](xref:Xamarin.Forms.Editor)para várias linhas de texto
- [`SearchBar`](xref:Xamarin.Forms.SearchBar)para uma única linha de texto para fins de pesquisa.

`Entry`e `Editor` derivam de [`InputView`](xref:Xamarin.Forms.InputView), `View`que deriva de . `SearchBar`deriva diretamente `View`de .

### <a name="keyboard-and-focus"></a>Teclado e foco

Em telefones e tablets sem `Entry` `Editor`teclados `SearchBar` físicos, todos os elementos fazem com que um teclado virtual apareça. A presença deste teclado na tela está relacionada ao foco de entrada. Uma vista deve [`IsVisible`](xref:Xamarin.Forms.VisualElement.IsVisible) ter [`IsEnabled`](xref:Xamarin.Forms.VisualElement.IsEnabled) suas `true` propriedades definidas para obter foco de entrada.

Dois métodos, uma propriedade somente leitura e dois eventos estão envolvidos com foco de entrada. Todos eles são `VisualElement`definidos por:

- O [`Focus`](xref:Xamarin.Forms.VisualElement.Focus) método tenta definir o foco `true` de entrada para um elemento e retorna se for bem sucedido
- O [`Unfocus`](xref:Xamarin.Forms.VisualElement.Unfocus) método remove o foco de entrada de um elemento
- A [`IsFocused`](xref:Xamarin.Forms.VisualElement.IsFocused) propriedade somente leitura indica se o elemento tem foco de entrada
- O [`Focused`](xref:Xamarin.Forms.VisualElement.Focused) evento indica quando um elemento obtém foco de entrada
- O [`Unfocused`](xref:Xamarin.Forms.VisualElement.Unfocused) evento indica quando um elemento perde o foco de entrada

### <a name="choosing-the-keyboard"></a>Escolhendo o teclado

A [`InputView`](xref:Xamarin.Forms.InputView) classe `Entry` a `Editor` partir da qual e derivam define apenas uma propriedade:

- [`Keyboard`](xref:Xamarin.Forms.InputView.Keyboard)de tipo[`Keyboard`](xref:Xamarin.Forms.Keyboard)

Isso indica o tipo de teclado exibido. Alguns teclados são otimizados para URIs ou números.

A `Keyboard` classe permite definir um [`Keyboard.Create`](xref:Xamarin.Forms.Keyboard.Create(Xamarin.Forms.KeyboardFlags)) teclado com um [`KeyboardFlags`](xref:Xamarin.Forms.KeyboardFlags)método estático com um argumento de tipo , uma enumeração com os seguintes sinalizadores de bits:

- `None`definido para 0
- [`CapitalizeSentence`](xref:Xamarin.Forms.KeyboardFlags.CapitalizeSentence)definido para 1
- [`Spellcheck`](xref:Xamarin.Forms.KeyboardFlags.Spellcheck)definido para 2
- [`Suggestions`](xref:Xamarin.Forms.KeyboardFlags.Suggestions)definido para 4
- [`All`](xref:Xamarin.Forms.KeyboardFlags.All)definido como \xFFFFFFFFFF

Ao usar a [`Editor`](xref:Xamarin.Forms.Editor) multilinha quando um parágrafo ou `Keyboard.Create` mais de texto é esperado, chamar é uma boa abordagem para selecionar um teclado. Para a linha [`Entry`](xref:Xamarin.Forms.Entry)única, as seguintes `Keyboard` propriedades estáticas somente de leitura são úteis:

- [`Default`](xref:Xamarin.Forms.Keyboard.Default)
- [`Text`](xref:Xamarin.Forms.Keyboard.Text)
- [`Chat`](xref:Xamarin.Forms.Keyboard.Chat)
- [`Url`](xref:Xamarin.Forms.Keyboard.Url)
- [`Email`](xref:Xamarin.Forms.Keyboard.Email)
- [`Telephone`](xref:Xamarin.Forms.Keyboard.Telephone)
- [`Numeric`](xref:Xamarin.Forms.Keyboard.Numeric)para números positivos com ou sem ponto decimal.

O [`KeyboardTypeConverter`](xref:Xamarin.Forms.KeyboardTypeConverter) permite especificar essas propriedades em XAML, conforme demonstrado pelo programa [**EntryKeyboards.**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/EntryKeyboards)

### <a name="entry-properties-and-events"></a>Propriedades de entrada e eventos

A linha [`Entry`](xref:Xamarin.Forms.Entry) única define as seguintes propriedades:

- [`Text`](xref:Xamarin.Forms.InputView.Text)tipo `string`, o texto que aparece no`Entry`
- [`TextColor`](xref:Xamarin.Forms.InputView.TextColor)de tipo`Color`
- [`FontFamily`](xref:Xamarin.Forms.Entry.FontFamily)de tipo`string`
- [`FontSize`](xref:Xamarin.Forms.Entry.FontSize)de tipo`double`
- [`FontAttributes`](xref:Xamarin.Forms.Entry.FontAttributes)de tipo`FontAttributes`
- [`IsPassword`](xref:Xamarin.Forms.Entry.IsPassword)do `bool`tipo , o que faz com que os personagens sejam mascarados
- [`Placeholder`](xref:Xamarin.Forms.InputView.Placeholder)do `string`tipo, para texto mal colorido `Entry` que aparece no antes de qualquer coisa ser digitada
- [`PlaceholderColor`](xref:Xamarin.Forms.InputView.PlaceholderColor)de tipo`Color`

O `Entry` também define dois eventos:

- [`TextChanged`](xref:Xamarin.Forms.InputView.TextChanged)com [`TextChangedEventArgs`](xref:Xamarin.Forms.TextChangedEventArgs) um objeto, `Text` demitido sempre que a propriedade muda
- [`Completed`](xref:Xamarin.Forms.Entry.Completed), demitido quando o usuário está acabado e o teclado é demitido. O usuário indica a conclusão de uma maneira específica da plataforma

A amostra [**QuadráticaEquations**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/QuadaticEquations) demonstra esses dois eventos.

### <a name="the-editor-difference"></a>A diferença editor

[`Editor`](xref:Xamarin.Forms.Editor) A multilinha define `Text` as `Font` mesmas propriedades como `Entry` mas não as outras propriedades. `Editor`também define as mesmas `Entry`duas propriedades que .

[**JustNotes**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/JustNotes) é um programa de tomada de notas de formulário `Editor`livre que salva e restaura o conteúdo do .

### <a name="the-searchbar"></a>A Barra de Pesquisa

O [`SearchBar`](xref:Xamarin.Forms.SearchBar) não deriva, `InputView`por isso não `Keyboard` tem uma propriedade. Mas tem todas `Text`as `Font`propriedades `Placeholder` que `Entry` definem. Além disso, `SearchBar` define três propriedades adicionais:

- [`CancelButtonColor`](xref:Xamarin.Forms.SearchBar.CancelButtonColor)de tipo`Color`
- [`SearchCommand`](xref:Xamarin.Forms.SearchBar.SearchCommand)de [`ICommand`](xref:System.Windows.Input.ICommand) tipo para uso com vinculações de dados e MVVM
- [`SearchCommandParameter`](xref:Xamarin.Forms.SearchBar.SearchCommandParameter)do `Object`tipo, para uso com`SearchCommand`

O botão de cancelamento específico da plataforma apaga o texto. O `SearchBar` também tem um botão de pesquisa específico da plataforma. Pressionar qualquer um desses botões levanta um `SearchBar` dos dois eventos que define:

- [`TextChanged`](xref:Xamarin.Forms.InputView.TextChanged)acompanhado por [`TextChangedEventArgs`](xref:Xamarin.Forms.TextChangedEventArgs) um objeto
- [`SearchButtonPressed`](xref:Xamarin.Forms.SearchBar.SearchButtonPressed)

A amostra [**SearchBarDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/SearchBarDemo) demonstra o `SearchBar`.

## <a name="date-and-time-selection"></a>Seleção de data e hora

Os [`DatePicker`](xref:Xamarin.Forms.DatePicker) [`TimePicker`](xref:Xamarin.Forms.TimePicker) pontos de vista implementam controles específicos da plataforma que permitem ao usuário especificar uma data ou hora.

### <a name="the-datepicker"></a>O DatePicker

[`DatePicker`](xref:Xamarin.Forms.DatePicker)define quatro propriedades:

- [`MinimumDate`](xref:Xamarin.Forms.DatePicker.MinimumDate)do `DateTime`tipo , iniciado em 1º de janeiro de 1900
- [`MaximumDate`](xref:Xamarin.Forms.DatePicker.MaximumDate)do `DateTime`tipo , inicializado até 31 de dezembro de 2100
- [`Date`](xref:Xamarin.Forms.DatePicker.Date)de `DateTime`tipo, inicializado para`DateTime.Today`
- [`Format`](xref:Xamarin.Forms.DatePicker.Format)do `string`tipo , a seqüência de formatação .NET inicializada para "d", o padrão de data curta, resultando em uma exibição de data como "7/20/1969" nos EUA.

Você pode `DateTime` definir as propriedades em XAML expressando as propriedades como elementos de propriedade e usando o formato de data curta invariante de cultura ("7/20/1969").   

A amostra [**DaysBetweenDates**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/DaysBetweenDates) calcula o número de dias entre duas datas selecionadas pelo usuário.

### <a name="the-timepicker-or-is-it-a-timespanpicker"></a>O TimePicker (ou é um TimeSpanPicker?)

[`TimePicker`](xref:Xamarin.Forms.TimePicker)define duas propriedades e nenhum evento:

- [`Time`](xref:Xamarin.Forms.TimePicker.Time)é do `TimeSpan` tipo `DateTime`em vez de , indicando o tempo decorrido desde a meia-noite
- [`Format`](xref:Xamarin.Forms.TimePicker.Format)do `string`tipo , a seqüência de formatação .NET inicializada para "t", o padrão de tempo curto, resultando em uma exibição de tempo como "1:45 PM" nos EUA.

O programa [**SetTimer**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/SetTimer) demonstra `TimePicker` como usar o para especificar um tempo para um temporizador. O programa só funciona se você mantê-lo em primeiro plano.

**SetTimer** também demonstra [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert(System.String,System.String,System.String)) usando `Page` o método de exibir uma caixa de alerta.

## <a name="related-links"></a>Links relacionados

- [Capítulo 15 texto completo (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch15-Apr2016.pdf)
- [Capítulo 15 amostras](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15)
- [Slider](~/xamarin-forms/user-interface/slider.md)
- [Entry](~/xamarin-forms/user-interface/text/entry.md)
- [Editor](~/xamarin-forms/user-interface/text/editor.md)
- [DatePicker](~/xamarin-forms/user-interface/datepicker.md)
