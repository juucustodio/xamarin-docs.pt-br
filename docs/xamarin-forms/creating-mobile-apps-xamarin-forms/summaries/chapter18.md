---
title: Resumo do capítulo 18. MVVM
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 6A774510-7709-4F60-8EF5-29D478176F8F
author: charlespetzold
ms.author: chape
ms.date: 11/07/2017
ms.openlocfilehash: 392e683601726ed1219fd43786a9b50d2c14c9b5
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="summary-of-chapter-18-mvvm"></a>Resumo do capítulo 18. MVVM

Uma das melhores maneiras de criar um aplicativo é separando a interface do usuário, o código subjacente, que às vezes é chamado de *lógica de negócios*. Existem várias técnicas, mas que é desenvolvido para ambientes baseados em XAML é conhecido como Model-View-ViewModel ou MVVM.

## <a name="mvvm-interrelationships"></a>Relações MVVM

Um aplicativo MVVM tem três camadas:

- O modelo fornece dados subjacentes, às vezes, por meio de arquivos ou web acessa
- O modo de exibição é o usuário interface ou apresentação camada, geralmente implementada em XAML
- O ViewModel conecta-se o modelo e o modo de exibição

O modelo é com ignorância de ViewModel e ViewModel é ignorar o modo de exibição. Essas três camadas geralmente se conectar entre si usando os seguintes mecanismos:

![Modo de exibição, ViewModel e exibição](images/ch18fg03.png "MVVM")

Em muitos programas menores (e ainda maiores), geralmente o modelo está ausente ou sua funcionalidade é integrada ao ViewModel.

## <a name="viewmodels-and-data-binding"></a>ViewModels e associação de dados

Para participar de associações de dados, um ViewModel deve ser capaz de notificar o modo de exibição quando uma propriedade de ViewModel foi alterado. O ViewModel faz isso Implementando a [ `INotifyPropertyChanged` ](https://developer.xamarin.com/api/type/System.ComponentModel.INotifyPropertyChanged/) interface no `System.ComponentModel` namespace. Isso faz parte do .NET em vez de xamarin. Forms. (Geralmente ViewModels tenta manter a independência de plataforma.)

O `INotifyPropertyChanged` interface declara um evento único chamado [ `PropertyChanged` ](https://developer.xamarin.com/api/type/System.ComponentModel.INotifyPropertyChanged/) que indica a propriedade que foi alterada.

### <a name="a-viewmodel-clock"></a>Um relógio de ViewModel

O [ `DateTimeViewModel` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/DateTimeViewModel.cs) no [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit) biblioteca define uma propriedade de tipo `DateTime` que as alterações com base em um temporizador. A classe implementa `INotifyPropertyChanged` e aciona o `PropertyChanged` evento sempre que o `DateTime` alterações de propriedade.

O [ **MvvmClock** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18/MvvmClock) exemplo instancia esse ViewModel e usa associações de dados ao ViewModel para exibir informações de hora e data da atualização.

### <a name="interactive-properties-in-a-viewmodel"></a>Propriedades interativas em um ViewModel

Propriedades em um ViewModel podem ser mais interativas, conforme demonstrado a [ `SimpleMultiplierViewModel` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter18/SimpleMultiplier/SimpleMultiplier/SimpleMultiplier/SimpleMultiplierViewModel.cs) classe, que é parte do [ **SimpleMultiplier** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18/SimpleMultiplier) exemplo. As associações de dados fornecem valores multiplicando e multiplicador de dois `Slider` elementos e exibir o produto com um `Label`. No entanto, você pode fazer alterações extensivas para essa interface do usuário em XAML sem alterações resultante ViewModel ou o arquivo code-behind.

### <a name="a-color-viewmodel"></a>Um ViewModel de cor

O [ `ColorViewModel` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ColorViewModel.cs) no [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit) biblioteca integra os modelos de cor RGB e HSL. Ele é demonstrado no [ **HslSliders** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18/HslSliders) exemplo:

[![Captura de tela tripla de TK](images/ch18fg08-small.png "HSL cor modelo")](images/ch18fg08-large.png#lightbox "HSL cor modelo")

### <a name="streamlining-the-viewmodel"></a>Simplificando o ViewModel

O código em ViewModels pode ser otimizado com a definição de um `OnPropertyChanged` usando o método de [ `CallerMemberName` ](https://developer.xamarin.com/api/type/System.Runtime.CompilerServices.CallerMemberNameAttribute/) atributo, que obtém o nome da propriedade chamada automaticamente. O [ `ViewModelBase` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ViewModelBase.cs) classe no [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit) biblioteca faz isso e fornece uma classe base para ViewModels.

## <a name="the-command-interface"></a>A interface de comando

MVVM funciona com associações de dados e associações de dados trabalhar com propriedades, portanto MVVM parece ser deficiente quando se trata de tratamento de um `Clicked` eventos de um `Button` ou um `Tapped` eventos de um `TapGestureRecognizer`. Para permitir que ViewModels lidar com tais eventos, xamarin. Forms oferece suporte a *interface de comando*.

A interface de comando manifesta-se no `Button` com duas propriedades públicas:

- [`Command`](https://developer.xamarin.com/api/property/Xamarin.Forms.Button.Command/) tipo [ `ICommand` ](https://developer.xamarin.com/api/type/System.Windows.Input.ICommand/) (definido no `System.Windows.Input` namespace)
- [`CommandParameter`](https://developer.xamarin.com/api/property/Xamarin.Forms.Button.CommandParameter/) do tipo `Object`

Para dar suporte a interface de comando, um ViewModel deve definir uma propriedade do tipo `ICommand` isto é, em seguida, dados associados ao `Command` propriedade o `Button`. O `ICommand` interface declara um evento e dois métodos:

- Um [ `Execute` ](https://developer.xamarin.com/api/member/System.Windows.Input.ICommand.Execute/p/System.Object/) método com um argumento de tipo `object`
- Um [ `CanExecute` ](https://developer.xamarin.com/api/member/System.Windows.Input.ICommand.CanExecute/p/System.Object/) método com um argumento de tipo `object` que retorna `bool`
- Um [ `CanExecuteChanged` ](https://developer.xamarin.com/api/event/System.Windows.Input.ICommand.CanExecuteChanged/) evento

Internamente, um ViewModel define cada propriedade do tipo `ICommand` a uma instância de uma classe que implementa o `ICommand` interface. Por meio de associação de dados, o `Button` inicialmente chama o `CanExecute` método e desabilita a mesmo se o método retornar `false`. Ele também define um manipulador para o `CanExecuteChanged` eventos e chamadas `CanExecute` sempre que esse evento é disparado. Se o `Button` é habilitado, ele chama o `Execute` método sempre que o `Button` é clicado.

Você pode ter alguns ViewModels anteriores xamarin. Forms e estas já podem dar suporte à interface de comando. Para o novo ViewModels se destina a ser usada somente com xamarin. Forms, xamarin. Forms fornece uma [ `Command` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Command/) classe e um [ `Command<T>` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Command%3CT%3E/) classes que implementam o `ICommand` interface. O tipo genérico é o tipo do argumento para o `Execute` e `CanExecute` métodos.

### <a name="simple-method-executions"></a>Execuções de método simples

O [ **PowersOfThree** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18/PowersOfThree) exemplo demonstra como usar a interface de comando em um ViewModel. O [ `PowersViewModel` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter18/PowersOfThree/PowersOfThree/PowersOfThree/PowersViewModel.cs) classe define duas propriedades do tipo `ICommand` e também define duas propriedades privadas que ele passa para o mais simples [ `Command` construtor](https://developer.xamarin.com/api/constructor/Xamarin.Forms.Command.Command/p/System.Action/). O programa contém associações de dados desse ViewModel para o `Command` propriedades de dois `Button` elementos.

O `Button` elementos podem ser facilmente substituídos com `TapGestureRecognizer` objetos no XAML sem alterações de código.

### <a name="a-calculator-almost"></a>Uma calculadora, quase

O [ **AddingMachine** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18/AddingMachine) exemplo faz uso de ambos o `Execute` e `CanExecute` métodos de `ICommand`. Ele usa um [ `AdderViewModel` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/AdderViewModel.cs) classe no [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/AdderViewModel.cs) biblioteca. O ViewModel contém seis propriedades de tipo `ICommand`. Esses são inicializadas a partir de [ `Command` construtor](https://developer.xamarin.com/api/constructor/Xamarin.Forms.Command.Command/p/System.Action/) e [ `Command` construtor](https://developer.xamarin.com/api/constructor/Xamarin.Forms.Command.Command/p/System.Action/System.Func%7BSystem.Boolean%7D/) de `Command` e [ `Command<T>` construtor](https://developer.xamarin.com/api/constructor/Xamarin.Forms.Command%3CT%3E.Command%3CT%3E/p/System.Action%7BT%7D/System.Func%7BT,System.Boolean%7D/) de `Command<T>`. As teclas numéricas de adição de máquina são associadas à propriedade que é inicializada com `Command<T>`e um `string` argumento `Execute` e `CanExecute` identifica a chave particular.

## <a name="viewmodels-and-the-application-lifecycle"></a>ViewModels e o ciclo de vida do aplicativo

O `AdderViewModel` usado no **AddingMachine** exemplo também define dois métodos chamados `SaveState` e `RestoreState`. Esses métodos são chamados do aplicativo quando ele entra em suspensão e quando ele for iniciado novamente.



## <a name="related-links"></a>Links relacionados

- [Texto completo do capítulo 18 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch18-Apr2016.pdf)
- [Exemplos de capítulo 18](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18)
