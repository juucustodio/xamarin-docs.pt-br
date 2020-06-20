---
title: Resumo do capítulo 18. MVVM
description: 'Criando aplicativos móveis com Xamarin.Forms : Resumo do capítulo 18. MVVM'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 6A774510-7709-4F60-8EF5-29D478176F8F
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 1f180173a42654c54c5686e423ba20d9586271ea
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2020
ms.locfileid: "84136702"
---
# <a name="summary-of-chapter-18-mvvm"></a>Resumo do capítulo 18. MVVM

[![Baixar exemplo ](~/media/shared/download.png) baixar o exemplo](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18)

Uma das melhores maneiras de arquitetar um aplicativo é separar a interface do usuário do código subjacente, que às vezes é chamado de *lógica de negócios*. Existem várias técnicas, mas a que é personalizada para ambientes baseados em XAML é conhecida como Model-View-ViewModel ou MVVM.

## <a name="mvvm-interrelationships"></a>Interrelações MVVM

Um aplicativo MVVM tem três camadas:

- O modelo fornece dados subjacentes, às vezes por meio de arquivos ou acessos à Web
- A exibição é a interface do usuário ou a camada de apresentação, geralmente implementada em XAML
- O ViewModel conecta o modelo e a exibição

O modelo é que desconhecem do ViewModel e o ViewModel é que desconhecem da exibição. Essas três camadas geralmente se conectam entre si usando os seguintes mecanismos:

![Exibir, ViewModel e exibir](images/ch18fg03.png "MVVM")

Em muitos programas menores (e ainda maiores), geralmente o modelo está ausente ou sua funcionalidade está integrada ao ViewModel.

## <a name="viewmodels-and-data-binding"></a>ViewModels e vinculação de dados

Para se envolver em associações de dados, um ViewModel deve ser capaz de notificar a exibição quando uma propriedade de ViewModel tiver mudado. O ViewModel faz isso implementando a [`INotifyPropertyChanged`](xref:System.ComponentModel.INotifyPropertyChanged) interface no `System.ComponentModel` namespace. Isso faz parte do .NET em vez de Xamarin.Forms . (Geralmente, os ViewModels tentam manter a independência da plataforma.)

A `INotifyPropertyChanged` interface declara um único evento chamado [`PropertyChanged`](xref:System.ComponentModel.INotifyPropertyChanged) que indica a propriedade que foi alterada.

### <a name="a-viewmodel-clock"></a>Um relógio ViewModel

O [`DateTimeViewModel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/DateTimeViewModel.cs) na biblioteca [**Xamarin. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit) define uma propriedade do tipo `DateTime` que muda com base em um temporizador. A classe implementa `INotifyPropertyChanged` e dispara o `PropertyChanged` evento sempre que a `DateTime` propriedade é alterada.

O exemplo [**MvvmClock**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18/MvvmClock) instancia esse ViewModel e usa associações de dados para o ViewModel para exibir informações atualizadas de data e hora.

### <a name="interactive-properties-in-a-viewmodel"></a>Propriedades interativas em um ViewModel

As propriedades em um ViewModel podem ser mais interativas, conforme demonstrado pela [`SimpleMultiplierViewModel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter18/SimpleMultiplier/SimpleMultiplier/SimpleMultiplier/SimpleMultiplierViewModel.cs) classe, que faz parte do exemplo [**SimpleMultiplier**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18/SimpleMultiplier) . As associações de dados fornecem valores de multiplicando e multiplicador de dois `Slider` elementos e exibem o produto com um `Label` . No entanto, você pode fazer grandes alterações nessa interface do usuário em XAML sem alterações resultantes no ViewModel ou no arquivo code-behind.

### <a name="a-color-viewmodel"></a>Um ViewModel de cores

O [`ColorViewModel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ColorViewModel.cs) na biblioteca [**Xamarin. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit) integra os modelos de cores RGB e HSL. Ele é demonstrado no exemplo de [**HslSliders**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18/HslSliders) :

[![Captura de tela tripla de TK](images/ch18fg08-small.png "Modelo de cor HSL")](images/ch18fg08-large.png#lightbox "Modelo de cor HSL")

### <a name="streamlining-the-viewmodel"></a>Simplificando o ViewModel

O código em ViewModels pode ser simplificado pela definição de um `OnPropertyChanged` método usando o [`CallerMemberName`](xref:System.Runtime.CompilerServices.CallerMemberNameAttribute) atributo, que obtém o nome da propriedade de chamada automaticamente. A [`ViewModelBase`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ViewModelBase.cs) classe na biblioteca [**Xamarin. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit) faz isso e fornece uma classe base para ViewModels.

## <a name="the-command-interface"></a>A interface de comando

O MVVM funciona com associações de dados, e as ligações de dados funcionam com propriedades, portanto, o MVVM parece ser deficientes quando se trata de tratar um `Clicked` evento de um ou de um `Button` `Tapped` evento de um `TapGestureRecognizer` . Para permitir que ViewModels manipulem esses eventos, Xamarin.Forms o oferece suporte à *interface de comando*.

A interface de comando se manifesta no `Button` com duas propriedades públicas:

- [`Command`](xref:Xamarin.Forms.Button.Command)do tipo [`ICommand`](xref:System.Windows.Input.ICommand) (definido no `System.Windows.Input` namespace)
- [`CommandParameter`](xref:Xamarin.Forms.Button.CommandParameter)do tipo`Object`

Para dar suporte à interface de comando, um ViewModel deve definir uma propriedade do tipo `ICommand` que é, então, dados associados à `Command` Propriedade do `Button` . A `ICommand` interface declara dois métodos e um evento:

- Um [`Execute`](xref:System.Windows.Input.ICommand.Execute(System.Object)) método com um argumento do tipo`object`
- Um [`CanExecute`](xref:System.Windows.Input.ICommand.CanExecute(System.Object)) método com um argumento do tipo `object` que retorna`bool`
- Um [`CanExecuteChanged`](xref:System.Windows.Input.ICommand.CanExecuteChanged) evento

Internamente, um ViewModel define cada propriedade do tipo `ICommand` como uma instância de uma classe que implementa a `ICommand` interface. Por meio da vinculação de dados, o `Button` inicialmente chama o `CanExecute` método e se desabilita se o método retornar `false` . Ele também define um manipulador para o `CanExecuteChanged` evento e chama `CanExecute` sempre que esse evento é acionado. Se o `Button` estiver habilitado, ele chamará o `Execute` método sempre que o `Button` for clicado.

Você pode ter alguns ViewModels que são preexistentes Xamarin.Forms , e eles podem já dar suporte à interface de comando. Para novos ViewModels destinados a serem usados apenas com Xamarin.Forms Xamarin.Forms o, fornece uma [`Command`](xref:Xamarin.Forms.Command) classe e uma [`Command<T>`](xref:Xamarin.Forms.Command`1) classe que implementam a `ICommand` interface. O tipo genérico é o tipo do argumento para os `Execute` métodos e `CanExecute` .

### <a name="simple-method-executions"></a>Execuções de método simples

O exemplo [**PowersOfThree**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18/PowersOfThree) demonstra como usar a interface de comando em um ViewModel. A [`PowersViewModel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter18/PowersOfThree/PowersOfThree/PowersOfThree/PowersViewModel.cs) classe define duas propriedades do tipo `ICommand` e também define duas propriedades particulares que ele passa para o [ `Command` Construtor](xref:Xamarin.Forms.Command.%23ctor(System.Action))mais simples. O programa contém associações de dados desse ViewModel às `Command` Propriedades de dois `Button` elementos.

Os `Button` elementos podem ser facilmente substituídos por `TapGestureRecognizer` objetos em XAML sem alterações de código.

### <a name="a-calculator-almost"></a>Uma calculadora, quase

O exemplo [**AddingMachine**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18/AddingMachine) usa os `Execute` `CanExecute` métodos e de `ICommand` . Ele usa uma [`AdderViewModel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/AdderViewModel.cs) classe na biblioteca [**Xamarin. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/AdderViewModel.cs) . O ViewModel contém seis propriedades do tipo `ICommand` . Eles são inicializados a partir do [ `Command` Construtor](xref:Xamarin.Forms.Command.%23ctor(System.Action)) e do [ `Command` Construtor](xref:Xamarin.Forms.Command.%23ctor(System.Action,System.Func{System.Boolean})) de `Command` e do [ `Command<T>` Construtor](https://docs.microsoft.com/dotnet/api/xamarin.forms.command.-ctor?view=xamarin-forms#Xamarin_Forms_Command__ctor_System_Action_System_Object__System_Func_System_Object_System_Boolean__) de `Command<T>` . As chaves numéricas da máquina de adição são todas vinculadas à propriedade que é inicializada com `Command<T>` , e um `string` argumento para `Execute` e `CanExecute` identifica a chave específica.

## <a name="viewmodels-and-the-application-lifecycle"></a>ViewModels e o ciclo de vida do aplicativo

O `AdderViewModel` usado no exemplo **AddingMachine** também define dois métodos chamados `SaveState` e `RestoreState` . Esses métodos são chamados do aplicativo quando ele entra em suspensão e quando ele é iniciado novamente.

## <a name="related-links"></a>Links relacionados

- [Capítulo 18 texto completo (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch18-Apr2016.pdf)
- [Capítulo 18 amostras](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18)
- [Padrões de aplicativos empresariais usando o Xamarin.Forms ebook](~/xamarin-forms/enterprise-application-patterns/index.md)
