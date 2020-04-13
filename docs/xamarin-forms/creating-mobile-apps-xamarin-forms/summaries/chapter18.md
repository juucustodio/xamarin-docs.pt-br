---
title: Resumo do Capítulo 18. MVVM
description: 'Criando aplicativos móveis com Xamarin.Forms: Resumo do Capítulo 18. MVVM'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 6A774510-7709-4F60-8EF5-29D478176F8F
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2017
ms.openlocfilehash: 32c16409f30d6b6d502b7cc074eafb182898594a
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "70771069"
---
# <a name="summary-of-chapter-18-mvvm"></a>Resumo do Capítulo 18. MVVM

[![Baixar](~/media/shared/download.png) amostra Baixar a amostra](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18)

Uma das melhores maneiras de projetar um aplicativo é separando a interface do usuário do código subjacente, que às vezes é chamada de *lógica de negócios*. Várias técnicas existem, mas a que é adaptada para ambientes baseados em XAML é conhecida como Model-View-ViewModel ou MVVM.

## <a name="mvvm-interrelationships"></a>Inter-relações MVVM

Um aplicativo MVVM tem três camadas:

- O Modelo fornece dados subjacentes, às vezes através de arquivos ou acessos à Web
- O View é a interface do usuário ou a camada de apresentação, geralmente implementada em XAML
- O ViewModel conecta o Modelo e a Exibição

O Modelo é ignorante do ViewModel e o ViewModel é ignorante da Exibição. Essas três camadas geralmente se conectam entre si usando os seguintes mecanismos:

![Exibir, exibir modelo e exibir](images/ch18fg03.png "MVVM")

Em muitos programas menores (e até maiores), muitas vezes o Modelo está ausente ou sua funcionalidade é integrada ao ViewModel.

## <a name="viewmodels-and-data-binding"></a>VerModelos e vinculação de dados

Para se envolver em vinculações de dados, um ViewModel deve ser capaz de notificar a exibição quando uma propriedade do ViewModel foi alterada. O ViewModel faz isso [`INotifyPropertyChanged`](xref:System.ComponentModel.INotifyPropertyChanged) implementando `System.ComponentModel` a interface no namespace. Isso faz parte do .NET em vez de Xamarin.Forms. (Geralmente, os Modelos de Visualização tentam manter a independência da plataforma.)

A `INotifyPropertyChanged` interface declara um [`PropertyChanged`](xref:System.ComponentModel.INotifyPropertyChanged) único evento chamado que indica a propriedade que foi alterada.

### <a name="a-viewmodel-clock"></a>Um relógio ViewModel

A [`DateTimeViewModel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/DateTimeViewModel.cs) biblioteca [**Xamarin.FormsBook.Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit) define uma propriedade `DateTime` do tipo que muda com base em um temporizador. A classe `INotifyPropertyChanged` implementa `PropertyChanged` e demite o evento sempre que a `DateTime` propriedade muda.

A amostra [**MvvmClock**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18/MvvmClock) instancia este ViewModel e usa vinculações de dados ao ViewModel para exibir informações atualizadas de data e hora.

### <a name="interactive-properties-in-a-viewmodel"></a>Propriedades interativas em um ViewModel

As propriedades em um ViewModel podem ser [`SimpleMultiplierViewModel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter18/SimpleMultiplier/SimpleMultiplier/SimpleMultiplier/SimpleMultiplierViewModel.cs) mais interativas, como demonstrado pela classe, que faz parte da amostra [**SimpleMultiplier.**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18/SimpleMultiplier) As vinculações de dados fornecem valores `Slider` multiplicados e multiplicadores de dois elementos e exibem o produto com um `Label`. No entanto, você pode fazer alterações extensivas nesta interface de usuário em XAML sem alterações conseqüentes no ViewModel ou no arquivo de código atrás.

### <a name="a-color-viewmodel"></a>Um modelo de visualização de cores

A [`ColorViewModel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ColorViewModel.cs) biblioteca [**Xamarin.FormsBook.Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit) integra os modelos de cores RGB e HSL. É demonstrado na amostra [**HsisSliders:**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18/HslSliders)

[![Captura de tela tripla de TK](images/ch18fg08-small.png "Modelo de cor HSL")](images/ch18fg08-large.png#lightbox "Modelo de cor HSL")

### <a name="streamlining-the-viewmodel"></a>Simplificando o ViewModel

O código em ViewModels pode ser `OnPropertyChanged` simplificado [`CallerMemberName`](xref:System.Runtime.CompilerServices.CallerMemberNameAttribute) definindo um método usando o atributo, que obtém o nome da propriedade de chamada automaticamente. A [`ViewModelBase`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ViewModelBase.cs) classe na biblioteca [**Xamarin.FormsBook.Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit) faz isso e fornece uma classe base para ViewModels.

## <a name="the-command-interface"></a>A interface de comando

A MVVM trabalha com vinculações de dados, e as vinculações de dados funcionam com propriedades, de `Clicked` modo `Button` que `Tapped` o MVVM parece ser deficiente quando se trata de lidar com um evento de um ou um evento de um `TapGestureRecognizer`. Para permitir que o ViewModels lide com tais eventos, o Xamarin.Forms suporta a *interface de comando*.

A interface de comando `Button` se manifesta no com duas propriedades públicas:

- [`Command`](xref:Xamarin.Forms.Button.Command)do [`ICommand`](xref:System.Windows.Input.ICommand) tipo (definido `System.Windows.Input` no namespace)
- [`CommandParameter`](xref:Xamarin.Forms.Button.CommandParameter)de tipo`Object`

Para suportar a interface de comando, um `ICommand` ViewModel deve definir `Command` uma propriedade `Button`de tipo que é vinculada à propriedade do . A `ICommand` interface declara dois métodos e um evento:

- Um [`Execute`](xref:System.Windows.Input.ICommand.Execute(System.Object)) método com um argumento de tipo`object`
- Um [`CanExecute`](xref:System.Windows.Input.ICommand.CanExecute(System.Object)) método com um `object` argumento de tipo que retorna`bool`
- Um [`CanExecuteChanged`](xref:System.Windows.Input.ICommand.CanExecuteChanged) evento

Internamente, um ViewModel define `ICommand` cada propriedade do tipo como `ICommand` uma instância de uma classe que implementa a interface. Através da vinculação `Button` de `CanExecute` dados, o inicialmente chama `false`o método e se desativa se o método retornar . Ele também define um `CanExecuteChanged` manipulador `CanExecute` para o evento e chama sempre que esse evento é acionado. Se `Button` o estiver ativado, `Execute` ele chamará o método sempre que for `Button` clicado.

Você pode ter alguns ViewModels que antecedem xamarin.forms, e estes já podem suportar a interface de comando. Para novos Modelos de Visualização destinados a ser usados apenas com [`Command`](xref:Xamarin.Forms.Command) Xamarin.Forms, o Xamarin.Forms fornece uma classe e uma [`Command<T>`](xref:Xamarin.Forms.Command`1) classe que implementam a `ICommand` interface. O tipo genérico é o tipo `Execute` `CanExecute` de argumento para os métodos.

### <a name="simple-method-executions"></a>Execuções simples de método

A amostra [**PowersOfThree**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18/PowersOfThree) demonstra como usar a interface de comando em um ViewModel. A [`PowersViewModel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter18/PowersOfThree/PowersOfThree/PowersOfThree/PowersViewModel.cs) classe define duas `ICommand` propriedades de tipo e também define duas propriedades privadas que passa para o [ `Command` construtor](xref:Xamarin.Forms.Command.%23ctor(System.Action))mais simples. O programa contém vinculações de dados `Command` deste `Button` ViewModel às propriedades de dois elementos.

Os `Button` elementos podem ser `TapGestureRecognizer` facilmente substituídos por objetos em XAML sem alterações de código.

### <a name="a-calculator-almost"></a>Uma calculadora, quase

A amostra [**AddingMachine**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18/AddingMachine) faz `Execute` uso `CanExecute` tanto `ICommand`dos métodos quanto dos métodos de . Ele usa [`AdderViewModel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/AdderViewModel.cs) uma classe na biblioteca [**Xamarin.FormsBook.Toolkit.**](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/AdderViewModel.cs) O ViewModel contém seis `ICommand`propriedades do tipo . Estas são inicializadas a partir `Command` do [ `Command<T>` ](https://docs.microsoft.com/dotnet/api/xamarin.forms.command.-ctor?view=xamarin-forms#Xamarin_Forms_Command__ctor_System_Action_System_Object__System_Func_System_Object_System_Boolean__) [ `Command` construtor](xref:Xamarin.Forms.Command.%23ctor(System.Action)) `Command<T>`e [ `Command` construtor](xref:Xamarin.Forms.Command.%23ctor(System.Action,System.Func{System.Boolean})) e do construtor de . As chaves numéricas da máquina de adição estão todas `Command<T>`vinculadas `string` à `Execute` propriedade `CanExecute` com a qual é inicializada , e um argumento para e identifica a chave particular.

## <a name="viewmodels-and-the-application-lifecycle"></a>VerModels e o ciclo de vida do aplicativo

O `AdderViewModel` usado na amostra **AddingMachine** também define `SaveState` `RestoreState`dois métodos nomeados e . Esses métodos são chamados do aplicativo quando ele vai dormir e quando ele recomeça.

## <a name="related-links"></a>Links relacionados

- [Capítulo 18 texto completo (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch18-Apr2016.pdf)
- [Capítulo 18 amostras](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18)
- [Padrões de aplicativos corporativos usando o eBook Xamarin.Forms](~/xamarin-forms/enterprise-application-patterns/index.md)
