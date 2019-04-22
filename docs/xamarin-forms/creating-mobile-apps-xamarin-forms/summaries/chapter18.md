---
title: Resumo do capítulo 18. MVVM
description: 'Criando aplicativos móveis com xamarin. Forms: Resumo do capítulo 18. MVVM'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 6A774510-7709-4F60-8EF5-29D478176F8F
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2017
ms.openlocfilehash: 6379bafb8c879237171951756441d1227f65b825
ms.sourcegitcommit: 3489c281c9eb5ada2cddf32d73370943342a1082
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/18/2019
ms.locfileid: "58870216"
---
# <a name="summary-of-chapter-18-mvvm"></a>Resumo do capítulo 18. MVVM

[![Baixar exemplo](~/media/shared/download.png) baixar o exemplo](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18)

Uma das melhores maneiras de projetar um aplicativo é, separando a interface do usuário do código subjacente, que é chamado, às vezes, o *lógica de negócios*. Existem diversas técnicas, mas aquele que é destinada a ambientes baseados em XAML é conhecido como Model-View-ViewModel ou MVVM.

## <a name="mvvm-interrelationships"></a>Relações do MVVM

Um aplicativo MVVM tem três camadas:

- O modelo fornece dados subjacentes, às vezes, por meio de arquivos ou web acessa
- O modo de exibição é a usuário interface de camada de apresentação ou, geralmente implementada em XAML
- O ViewModel conecta-se o modelo e o modo de exibição

O modelo é com ignorância de ViewModel e o ViewModel é com ignorância da exibição. Essas três camadas geralmente se conectar entre si usando os seguintes mecanismos:

![Modo de exibição, ViewModel e exibição](images/ch18fg03.png "MVVM")

Em muitos programas menores (e até mesmo maiores), muitas vezes o modelo está ausente ou sua funcionalidade é integrada ao ViewModel.

## <a name="viewmodels-and-data-binding"></a>ViewModels e vinculação de dados

Para participar de ligações de dados, um ViewModel deve ser capaz de notificar a exibição quando uma propriedade de ViewModel foi alterada. O ViewModel faz isso Implementando a [ `INotifyPropertyChanged` ](xref:System.ComponentModel.INotifyPropertyChanged) interface no `System.ComponentModel` namespace. Isso faz parte do .NET em vez de xamarin. Forms. (Geralmente ViewModels tentar manter a independência de plataforma.)

O `INotifyPropertyChanged` interface declara um evento único chamado [ `PropertyChanged` ](xref:System.ComponentModel.INotifyPropertyChanged) que indica a propriedade que foi alterado.

### <a name="a-viewmodel-clock"></a>Um relógio de ViewModel

O [ `DateTimeViewModel` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/DateTimeViewModel.cs) no [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit) biblioteca define uma propriedade de tipo `DateTime` que as alterações com base em um temporizador. A classe implementa `INotifyPropertyChanged` e dispara o `PropertyChanged` evento sempre que o `DateTime` alterações de propriedade.

O [ **MvvmClock** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18/MvvmClock) exemplo instancia esse ViewModel e usa as associações de dados ao ViewModel para exibir informações atualizadas de data e hora.

### <a name="interactive-properties-in-a-viewmodel"></a>Propriedades interativas em um ViewModel

Propriedades de uma ViewModel podem ser mais interativas, como demonstrado pelo [ `SimpleMultiplierViewModel` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter18/SimpleMultiplier/SimpleMultiplier/SimpleMultiplier/SimpleMultiplierViewModel.cs) classe, que é parte do [ **SimpleMultiplier** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18/SimpleMultiplier) exemplo. As associações de dados fornecem valores multiplicando e multiplicador de duas `Slider` elementos e exibir o produto com um `Label`. No entanto, você pode fazer alterações extensivas essa interface do usuário em XAML sem qualquer alteração resultante no ViewModel ou o arquivo code-behind.

### <a name="a-color-viewmodel"></a>Um ViewModel de cor

O [ `ColorViewModel` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ColorViewModel.cs) no [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit) biblioteca integra-se a modelos de cores RGB e HSL. Ele é demonstrado na [ **HslSliders** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18/HslSliders) exemplo:

[![Captura de tela tripla de TK](images/ch18fg08-small.png "modelo de cor HSL")](images/ch18fg08-large.png#lightbox "modelo de cor HSL")

### <a name="streamlining-the-viewmodel"></a>Simplificando o ViewModel

O código em ViewModels pode ser simplificado com a definição de um `OnPropertyChanged` usando o método de [ `CallerMemberName` ](xref:System.Runtime.CompilerServices.CallerMemberNameAttribute) atributo, que obtém o nome da propriedade chamada automaticamente. O [ `ViewModelBase` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ViewModelBase.cs) classe os [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit) biblioteca faz isso e fornece uma classe base para ViewModels.

## <a name="the-command-interface"></a>A interface de comando

MVVM funciona com associações de dados e associações de dados funcionam com propriedades, portanto, MVVM parece ser deficientes quando se trata de tratamento de um `Clicked` eventos de um `Button` ou um `Tapped` eventos de um `TapGestureRecognizer`. Para permitir que ViewModels lidar com tais eventos, xamarin. Forms dá suporte a *interface de comando*.

A interface de comando se manifesta no `Button` com duas propriedades públicas:

- [`Command`](xref:Xamarin.Forms.Button.Command) do tipo [ `ICommand` ](xref:System.Windows.Input.ICommand) (definido no `System.Windows.Input` namespace)
- [`CommandParameter`](xref:Xamarin.Forms.Button.CommandParameter) do tipo `Object`

Para dar suporte a interface de comando, um ViewModel deve definir uma propriedade do tipo `ICommand` que é então associada a dados para o `Command` propriedade do `Button`. O `ICommand` interface declara dois métodos e um evento:

- Uma [ `Execute` ](xref:System.Windows.Input.ICommand.Execute(System.Object)) método com um argumento de tipo `object`
- Um [ `CanExecute` ](xref:System.Windows.Input.ICommand.CanExecute(System.Object)) método com um argumento do tipo `object` que retorna `bool`
- Um [ `CanExecuteChanged` ](xref:System.Windows.Input.ICommand.CanExecuteChanged) evento

Internamente, um ViewModel define cada propriedade do tipo `ICommand` a uma instância de uma classe que implementa o `ICommand` interface. Por meio da associação de dados, o `Button` inicialmente chama o `CanExecute` método e desabilita a mesmo se o método retornar `false`. Ele também define um manipulador para o `CanExecuteChanged` eventos e chamadas `CanExecute` sempre que esse evento é disparado. Se o `Button` está habilitado, ele chama o `Execute` método sempre que o `Button` é clicado.

Você pode ter alguns ViewModels, que são anteriores ao xamarin. Forms, e elas talvez já dão suporte à interface de comando. Para o novo ViewModels se destina a ser usada somente com o xamarin. Forms, xamarin. Forms fornece um [ `Command` ](xref:Xamarin.Forms.Command) classe e uma [ `Command<T>` ](xref:Xamarin.Forms.Command`1) classe que implementam o `ICommand` interface. O tipo genérico é o tipo do argumento para o `Execute` e `CanExecute` métodos.

### <a name="simple-method-executions"></a>Execuções de método simples

O [ **PowersOfThree** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18/PowersOfThree) que demonstra como usar a interface de comando em um ViewModel. O [ `PowersViewModel` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter18/PowersOfThree/PowersOfThree/PowersOfThree/PowersViewModel.cs) classe define duas propriedades do tipo `ICommand` e também define duas propriedades privadas que ele passa para o mais simples [ `Command` construtor](xref:Xamarin.Forms.Command.%23ctor(System.Action)). O programa contém associações de dados desse ViewModel para a `Command` propriedades de dois `Button` elementos.

O `Button` elementos podem ser facilmente substituídos por `TapGestureRecognizer` objetos no XAML sem alterações no código.

### <a name="a-calculator-almost"></a>Uma calculadora, quase

O [ **AddingMachine** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18/AddingMachine) exemplo faz uso de ambos os o `Execute` e `CanExecute` métodos de `ICommand`. Ele usa um [ `AdderViewModel` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/AdderViewModel.cs) classe os [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/AdderViewModel.cs) biblioteca. O ViewModel contém seis propriedades do tipo `ICommand`. Eles são inicializados do [ `Command` construtor](xref:Xamarin.Forms.Command.%23ctor(System.Action)) e [ `Command` construtor](xref:Xamarin.Forms.Command.%23ctor(System.Action,System.Func{System.Boolean})) de `Command` e o [ `Command<T>` construtor](https://docs.microsoft.com/dotnet/api/xamarin.forms.command.-ctor?view=xamarin-forms#Xamarin_Forms_Command__ctor_System_Action_System_Object__System_Func_System_Object_System_Boolean__) de `Command<T>`. As chaves numéricas da Calculadora são associadas à propriedade que é inicializada com `Command<T>`e uma `string` argumento `Execute` e `CanExecute` identifica a chave particular.

## <a name="viewmodels-and-the-application-lifecycle"></a>ViewModels e o ciclo de vida do aplicativo

O `AdderViewModel` usado em de **AddingMachine** exemplo também define dois métodos chamados `SaveState` e `RestoreState`. Esses métodos são chamados do aplicativo quando ele entra em suspensão e quando ele for iniciado novamente.



## <a name="related-links"></a>Links relacionados

- [Capítulo 18 de texto completo (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch18-Apr2016.pdf)
- [Exemplos do capítulo 18](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18)
- [Padrões de aplicativo empresarial usando o livro eletrônico do xamarin. Forms](~/xamarin-forms/enterprise-application-patterns/index.md)
