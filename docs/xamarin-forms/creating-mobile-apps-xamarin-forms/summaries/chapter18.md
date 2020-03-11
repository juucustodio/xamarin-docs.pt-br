---
title: Resumo do capítulo 18. MVVM
description: 'Criação de aplicativos móveis com xamarin. Forms: Resumo do capítulo 18. MVVM'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 6A774510-7709-4F60-8EF5-29D478176F8F
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2017
ms.openlocfilehash: 32c16409f30d6b6d502b7cc074eafb182898594a
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2020
ms.locfileid: "70771069"
---
# <a name="summary-of-chapter-18-mvvm"></a>Resumo do capítulo 18. MVVM

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18)

Uma das melhores maneiras de arquitetar um aplicativo é separar a interface do usuário do código subjacente, que às vezes é chamado de *lógica de negócios*. Existem diversas técnicas, mas aquele que é destinada a ambientes baseados em XAML é conhecido como Model-View-ViewModel ou MVVM.

## <a name="mvvm-interrelationships"></a>Relações do MVVM

Um aplicativo MVVM tem três camadas:

- O modelo fornece dados subjacentes, às vezes, por meio de arquivos ou web acessa
- O modo de exibição é a usuário interface de camada de apresentação ou, geralmente implementada em XAML
- O ViewModel conecta-se o modelo e o modo de exibição

O modelo é com ignorância de ViewModel e o ViewModel é com ignorância da exibição. Essas três camadas geralmente se conectar entre si usando os seguintes mecanismos:

![Exibir, ViewModel e exibir](images/ch18fg03.png "MVVM")

Em muitos programas menores (e até mesmo maiores), muitas vezes o modelo está ausente ou sua funcionalidade é integrada ao ViewModel.

## <a name="viewmodels-and-data-binding"></a>ViewModels e vinculação de dados

Para participar de ligações de dados, um ViewModel deve ser capaz de notificar a exibição quando uma propriedade de ViewModel foi alterada. O ViewModel faz isso implementando a interface [`INotifyPropertyChanged`](xref:System.ComponentModel.INotifyPropertyChanged) no namespace `System.ComponentModel`. Isso faz parte do .NET em vez de xamarin. Forms. (Geralmente ViewModels tentar manter a independência de plataforma.)

A interface `INotifyPropertyChanged` declara um único evento chamado [`PropertyChanged`](xref:System.ComponentModel.INotifyPropertyChanged) que indica a propriedade que foi alterada.

### <a name="a-viewmodel-clock"></a>Um relógio de ViewModel

O [`DateTimeViewModel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/DateTimeViewModel.cs) na biblioteca [**Xamarin. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit) define uma propriedade do tipo `DateTime` que é alterada com base em um temporizador. A classe implementa `INotifyPropertyChanged` e dispara o evento `PropertyChanged` sempre que a propriedade `DateTime` é alterada.

O exemplo [**MvvmClock**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18/MvvmClock) instancia esse ViewModel e usa associações de dados para o ViewModel para exibir informações atualizadas de data e hora.

### <a name="interactive-properties-in-a-viewmodel"></a>Propriedades interativas em um ViewModel

As propriedades em um ViewModel podem ser mais interativas, conforme demonstrado pela classe [`SimpleMultiplierViewModel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter18/SimpleMultiplier/SimpleMultiplier/SimpleMultiplier/SimpleMultiplierViewModel.cs) , que faz parte do exemplo [**SimpleMultiplier**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18/SimpleMultiplier) . As associações de dados fornecem valores multiplicando e multiplicador de dois elementos `Slider` e exibem o produto com um `Label`. No entanto, você pode fazer alterações extensivas essa interface do usuário em XAML sem qualquer alteração resultante no ViewModel ou o arquivo code-behind.

### <a name="a-color-viewmodel"></a>Um ViewModel de cor

O [`ColorViewModel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ColorViewModel.cs) na biblioteca [**Xamarin. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit) integra os modelos de cores RGB e HSL. Ele é demonstrado no exemplo de [**HslSliders**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18/HslSliders) :

[![Captura de tela tripla de TK](images/ch18fg08-small.png "Modelo de cor HSL")](images/ch18fg08-large.png#lightbox "Modelo de cor HSL")

### <a name="streamlining-the-viewmodel"></a>Simplificando o ViewModel

O código em ViewModels pode ser simplificado definindo um método `OnPropertyChanged` usando o atributo [`CallerMemberName`](xref:System.Runtime.CompilerServices.CallerMemberNameAttribute) , que obtém o nome da propriedade de chamada automaticamente. A classe [`ViewModelBase`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ViewModelBase.cs) na biblioteca [**Xamarin. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit) faz isso e fornece uma classe base para ViewModels.

## <a name="the-command-interface"></a>A interface de comando

O MVVM funciona com associações de dados e as ligações de dados funcionam com propriedades, portanto, o MVVM parece ser deficientes quando se trata de manipular um evento de `Clicked` de um `Button` ou um evento `Tapped` de um `TapGestureRecognizer`. Para permitir que ViewModels manipulem esses eventos, o Xamarin. Forms dá suporte à *interface de comando*.

A interface de comando se manifesta no `Button` com duas propriedades públicas:

- [`Command`](xref:Xamarin.Forms.Button.Command) do tipo [`ICommand`](xref:System.Windows.Input.ICommand) (definido no namespace `System.Windows.Input`)
- [`CommandParameter`](xref:Xamarin.Forms.Button.CommandParameter) do tipo `Object`

Para dar suporte à interface de comando, um ViewModel deve definir uma propriedade do tipo `ICommand` que, em seguida, os dados são associados à propriedade `Command` da `Button`. A interface `ICommand` declara dois métodos e um evento:

- Um método [`Execute`](xref:System.Windows.Input.ICommand.Execute(System.Object)) com um argumento do tipo `object`
- Um método [`CanExecute`](xref:System.Windows.Input.ICommand.CanExecute(System.Object)) com um argumento do tipo `object` que retorna `bool`
- Um evento [`CanExecuteChanged`](xref:System.Windows.Input.ICommand.CanExecuteChanged)

Internamente, um ViewModel define cada propriedade do tipo `ICommand` como uma instância de uma classe que implementa a interface `ICommand`. Por meio da vinculação de dados, o `Button` inicialmente chama o método `CanExecute` e se desabilita se o método retornar `false`. Ele também define um manipulador para o evento `CanExecuteChanged` e chama `CanExecute` sempre que esse evento é acionado. Se a `Button` estiver habilitada, ela chamará o método `Execute` sempre que o `Button` for clicado.

Você pode ter alguns ViewModels, que são anteriores ao xamarin. Forms, e elas talvez já dão suporte à interface de comando. Para novos ViewModels destinados a serem usados somente com Xamarin. Forms, o Xamarin. Forms fornece uma classe de [`Command`](xref:Xamarin.Forms.Command) e uma classe [`Command<T>`](xref:Xamarin.Forms.Command`1) que implementa a interface `ICommand`. O tipo genérico é o tipo do argumento para os métodos `Execute` e `CanExecute`.

### <a name="simple-method-executions"></a>Execuções de método simples

O exemplo [**PowersOfThree**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18/PowersOfThree) demonstra como usar a interface de comando em um ViewModel. A classe [`PowersViewModel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter18/PowersOfThree/PowersOfThree/PowersOfThree/PowersViewModel.cs) define duas propriedades do tipo `ICommand` e também define duas propriedades particulares que ele passa para o construtor de [`Command`](xref:Xamarin.Forms.Command.%23ctor(System.Action))mais simples. O programa contém associações de dados desse ViewModel para as propriedades `Command` de dois elementos `Button`.

Os elementos de `Button` podem ser facilmente substituídos por `TapGestureRecognizer` objetos em XAML sem alterações de código.

### <a name="a-calculator-almost"></a>Uma calculadora, quase

O exemplo [**AddingMachine**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18/AddingMachine) usa os métodos `Execute` e `CanExecute` de `ICommand`. Ele usa uma classe [`AdderViewModel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/AdderViewModel.cs) na biblioteca [**Xamarin. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/AdderViewModel.cs) . O ViewModel contém seis propriedades do tipo `ICommand`. Eles são inicializados do [Construtor de`Command`](xref:Xamarin.Forms.Command.%23ctor(System.Action)) e do construtor de [`Command`](xref:Xamarin.Forms.Command.%23ctor(System.Action,System.Func{System.Boolean})) de `Command` e o [Construtor`Command<T>`](https://docs.microsoft.com/dotnet/api/xamarin.forms.command.-ctor?view=xamarin-forms#Xamarin_Forms_Command__ctor_System_Action_System_Object__System_Func_System_Object_System_Boolean__) de `Command<T>`. As chaves numéricas da máquina de adição são todas vinculadas à propriedade que é inicializada com `Command<T>`e um argumento `string` para `Execute` e `CanExecute` identifica a chave específica.

## <a name="viewmodels-and-the-application-lifecycle"></a>ViewModels e o ciclo de vida do aplicativo

O `AdderViewModel` usado no exemplo **AddingMachine** também define dois métodos chamados `SaveState` e `RestoreState`. Esses métodos são chamados do aplicativo quando ele entra em suspensão e quando ele for iniciado novamente.

## <a name="related-links"></a>Links relacionados

- [Capítulo 18 texto completo (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch18-Apr2016.pdf)
- [Capítulo 18 amostras](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18)
- [Padrões de aplicativos empresariais usando o Xamarin. Forms eBook](~/xamarin-forms/enterprise-application-patterns/index.md)
