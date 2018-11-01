---
title: A Interface de comando do xamarin. Forms
description: Este artigo explica como implementar a propriedade Command com ligações de dados do xamarin. Forms. A interface de comando fornece uma abordagem alternativa à implementação de comandos que é muito mais adequada para a arquitetura MVVM.
ms.prod: xamarin
ms.assetid: 69922284-F398-45C3-B4CC-B8E29BB4C533
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/05/2018
ms.openlocfilehash: 68c7869254ae861cef8307431d925368082be921
ms.sourcegitcommit: 729035af392dc60edb9d99d3dc13d1ef69d5e46c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/31/2018
ms.locfileid: "50675233"
---
# <a name="the-xamarinforms-command-interface"></a>A Interface de comando do xamarin. Forms

Na arquitetura Model-View-ViewModel (MVVM), associações de dados são definidas entre as propriedades no ViewModel, que geralmente é uma classe que deriva de `INotifyPropertyChanged`e as propriedades no modo de exibição, que geralmente é o arquivo XAML. Às vezes, um aplicativo tem necessidades que vão além dessas associações de propriedade ao exigir que o usuário inicie os comandos que afetam algo no ViewModel. Esses comandos geralmente são sinalizados por cliques de botão ou dedo toques e tradicionalmente, eles são processados no código code-behind em um manipulador para o `Clicked` eventos do `Button` ou o `Tapped` eventos de um `TapGestureRecognizer`.

A interface de comando fornece uma abordagem alternativa à implementação de comandos que é muito mais adequada para a arquitetura MVVM. O ViewModel em si pode conter comandos, que são métodos que são executados em reação a uma atividade específica no modo de exibição, como um `Button` clique. Associações de dados são definidas entre esses dois comandos e o `Button`.

Para permitir que uma associação de dados entre um `Button` e um ViewModel, o `Button` define duas propriedades:

- [`Command`](xref:Xamarin.Forms.Button.Command) do tipo [`System.Windows.Input.ICommand`](xref:System.Windows.Input.ICommand)
- [`CommandParameter`](xref:Xamarin.Forms.Button.CommandParameter) do tipo `Object`

Para usar a interface de comando, você define uma associação de dados que tem como alvo o `Command` propriedade do `Button` onde a fonte é uma propriedade no ViewModel do tipo `ICommand`. O ViewModel contém código associado que `ICommand` propriedade que é executada quando o botão é clicado. Você pode definir `CommandParameter` para dados arbitrários para distinguir entre vários botões se eles são todos associados ao mesmo `ICommand` propriedade no ViewModel.

O `Command` e `CommandParameter` propriedades também são definidas pelos seguintes classes:

- [`MenuItem`](xref:Xamarin.Forms.MenuItem) e, portanto, [ `ToolbarItem` ](xref:Xamarin.Forms.ToolbarItem), que é derivada de `MenuItem`
- [`TextCell`](xref:Xamarin.Forms.TextCell) e, portanto, [ `ImageCell` ](xref:Xamarin.Forms.ImageCell), que é derivada de `TextCell`
- [`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer)

[`SearchBar`](xref:Xamarin.Forms.SearchBar) define uma [ `SearchCommand` ](xref:Xamarin.Forms.SearchBar.SearchCommand) propriedade do tipo `ICommand` e um [ `SearchCommandParameter` ](xref:Xamarin.Forms.SearchBar.SearchCommandParameter) propriedade. O [ `RefreshCommand` ](xref:Xamarin.Forms.ListView.RefreshCommand) propriedade do [ `ListView` ](xref:Xamarin.Forms.ListView) também é do tipo `ICommand`.

Todos esses comandos podem ser tratados dentro de um ViewModel, de forma que não depende do objeto específico da interface do usuário no modo de exibição.

## <a name="the-icommand-interface"></a>A Interface ICommand

O [ `System.Windows.Input.ICommand` ](xref:System.Windows.Input.ICommand) interface não faz parte do xamarin. Forms. Ela é definida em vez disso, nos [Input](xref:System.Windows.Input) namespace e consiste em dois métodos e um evento:

```csharp
public interface ICommand
{
    public void Execute (Object parameter);

    public bool CanExecute (Object parameter);

    public event EventHandler CanExecuteChanged;
}
```

Para usar a interface de comando, o ViewModel contém as propriedades de tipo `ICommand`:

```csharp
public ICommand MyCommand { private set; get; }
```

O ViewModel também deve fazer referência a uma classe que implementa o `ICommand` interface. Essa classe será descrita em breve. No modo de exibição, o `Command` propriedade de um `Button` é associado a essa propriedade:

```xaml
<Button Text="Execute command"
        Command="{Binding MyCommand}" />
```

Quando o usuário pressiona o `Button`, o `Button` chamadas a `Execute` método na `ICommand` objeto associado a seu `Command` propriedade. Essa é a parte mais simples da interface de comando.

O `CanExecute` método é mais complexo. Quando a associação for definida na `Command` propriedade do `Button`, e quando a associação de dados é alterado de alguma forma, o `Button` chamadas a `CanExecute` método no `ICommand` objeto. Se `CanExecute` retorna `false`, em seguida, a `Button` desabilita em si. Isso indica que o comando em particular está atualmente disponível ou é inválido.

O `Button` também anexa um manipulador em de `CanExecuteChanged` eventos de `ICommand`. O evento é acionado de dentro do ViewModel. Quando esse evento é disparado, o `Button` chamadas `CanExecute` novamente. O `Button` ativa em si se `CanExecute` retorna `true` e desativado se `CanExecute` retorna `false`.

> [!IMPORTANT]
> Não use o `IsEnabled` propriedade de `Button` se você estiver usando a interface de comando.  

## <a name="the-command-class"></a>A classe de comando

Quando seu ViewModel define uma propriedade do tipo `ICommand`, o ViewModel também deve conter ou fazer referência a uma classe que implementa o `ICommand` interface. Essa classe deve conter ou fazer referência a `Execute` e `CanExecute` métodos e acionar o `CanExecuteChanged` evento sempre que o `CanExecute` método pode retornar um valor diferente.

Você pode escrever uma classe desse tipo, ou você pode usar uma classe que alguém tenha gravado. Porque `ICommand` faz parte do Microsoft Windows, ele tem sido usado por anos com aplicativos MVVM do Windows. Usando uma classe do Windows que implementa `ICommand` permite que você compartilhe seus ViewModels entre aplicativos do Windows e aplicativos xamarin. Forms.

Se não for um problema de compartilhamento ViewModels entre o Windows e xamarin. Forms, você pode usar o [ `Command` ](xref:Xamarin.Forms.Command) ou [ `Command<T>` ](xref:Xamarin.Forms.Command`1) classe incluída no xamarin. Forms para implementar o `ICommand`interface. Essas classes permitem que você especifique os corpos do `Execute` e `CanExecute` métodos em construtores de classe. Use `Command<T>` quando você usa o `CommandParameter` propriedade para distinguir entre vários modos de exibição associada à mesma `ICommand` propriedade e o mais simples `Command` classe quando que não é um requisito.

## <a name="basic-commanding"></a>Comandos básicos

O **entrada pessoa** página de [ **demonstrações de associação de dados** ](https://developer.xamarin.com/samples/xamarin-forms/DataBindingDemos/) programa demonstra alguns comandos simples, implementados em um ViewModel.

O `PersonViewModel` define três propriedades chamadas `Name`, `Age`, e `Skills` que definem uma pessoa. Essa classe faz *não* conter nenhum `ICommand` propriedades:

```csharp
public class PersonViewModel : INotifyPropertyChanged
{
    string name;
    double age;
    string skills;

    public event PropertyChangedEventHandler PropertyChanged;

    public string Name
    {
        set { SetProperty(ref name, value); }
        get { return name; }
    }

    public double Age
    {
        set { SetProperty(ref age, value); }
        get { return age; }
    }

    public string Skills
    {
        set { SetProperty(ref skills, value); }
        get { return skills; }
    }

    public override string ToString()
    {
        return Name + ", age " + Age;
    }

    bool SetProperty<T>(ref T storage, T value, [CallerMemberName] string propertyName = null)
    {
        if (Object.Equals(storage, value))
            return false;

        storage = value;
        OnPropertyChanged(propertyName);
        return true;
    }

    protected void OnPropertyChanged([CallerMemberName] string propertyName = null)
    {
        PropertyChanged?.Invoke(this, new PropertyChangedEventArgs(propertyName));
    }
}
```

O `PersonCollectionViewModel` mostrado a seguir cria novos objetos do tipo `PersonViewModel` e permite que o usuário preencha os dados. Para essa finalidade, a classe define propriedades `IsEditing` do tipo `bool` e `PersonEdit` do tipo `PersonViewModel`. Além disso, a classe define três propriedades do tipo `ICommand` e uma propriedade chamada `Persons` do tipo `IList<PersonViewModel>`:

```csharp
public class PersonCollectionViewModel : INotifyPropertyChanged
{
    PersonViewModel personEdit;
    bool isEditing;

    public event PropertyChangedEventHandler PropertyChanged;

    ···

    public bool IsEditing
    {
        private set { SetProperty(ref isEditing, value); }
        get { return isEditing; }
    }

    public PersonViewModel PersonEdit
    {
        set { SetProperty(ref personEdit, value); }
        get { return personEdit; }
    }

    public ICommand NewCommand { private set; get; }

    public ICommand SubmitCommand { private set; get; }

    public ICommand CancelCommand { private set; get; }

    public IList<PersonViewModel> Persons { get; } = new ObservableCollection<PersonViewModel>();

    bool SetProperty<T>(ref T storage, T value, [CallerMemberName] string propertyName = null)
    {
        if (Object.Equals(storage, value))
            return false;

        storage = value;
        OnPropertyChanged(propertyName);
        return true;
    }

    protected void OnPropertyChanged([CallerMemberName] string propertyName = null)
    {
        PropertyChanged?.Invoke(this, new PropertyChangedEventArgs(propertyName));
    }
}
```

Essa listagem abreviada não inclui o construtor da classe, que é onde as três propriedades do tipo `ICommand` são definidos, que será mostrado em breve. Observe que muda para as três propriedades do tipo `ICommand` e o `Persons` propriedade não resultam em `PropertyChanged` eventos que estão sendo acionados. Essas propriedades são definidos quando a classe é criada e não são alterados depois disso.

Antes de examinar o construtor do `PersonCollectionViewModel` classe, vamos examinar o arquivo XAML para o **entrada pessoa** programa. Ele contém um `Grid` com seus `BindingContext` propriedade definida como o `PersonCollectionViewModel`. O `Grid` contém uma `Button` com o texto **New** com seu `Command` propriedade associada ao `NewCommand` um formulário de entrada com propriedades de propriedade no ViewModel, vinculada ao `IsEditing` propriedade, como bem como propriedades do `PersonViewModel`, e mais dois botões associados para o `SubmitCommand` e `CancelCommand` propriedades do ViewModel. O último `ListView` exibe a coleção de pessoas já inserido:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:DataBindingDemos"
             x:Class="DataBindingDemos.PersonEntryPage"
             Title="Person Entry">
    <Grid Margin="10">
        <Grid.BindingContext>
            <local:PersonCollectionViewModel />
        </Grid.BindingContext>

        <Grid.RowDefinitions>
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="*" />
        </Grid.RowDefinitions>

        <!-- New Button -->
        <Button Text="New"
                Grid.Row="0"
                Command="{Binding NewCommand}"
                HorizontalOptions="Start" />

        <!-- Entry Form -->
        <Grid Grid.Row="1"
              IsEnabled="{Binding IsEditing}">

            <Grid BindingContext="{Binding PersonEdit}">
                <Grid.RowDefinitions>
                    <RowDefinition Height="Auto" />
                    <RowDefinition Height="Auto" />
                    <RowDefinition Height="Auto" />
                </Grid.RowDefinitions>

                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="Auto" />
                    <ColumnDefinition Width="*" />
                </Grid.ColumnDefinitions>

                <Label Text="Name: " Grid.Row="0" Grid.Column="0" />
                <Entry Text="{Binding Name}"
                       Grid.Row="0" Grid.Column="1" />

                <Label Text="Age: " Grid.Row="1" Grid.Column="0" />
                <StackLayout Orientation="Horizontal"
                             Grid.Row="1" Grid.Column="1">
                    <Stepper Value="{Binding Age}"
                             Maximum="100" />
                    <Label Text="{Binding Age, StringFormat='{0} years old'}"
                           VerticalOptions="Center" />
                </StackLayout>

                <Label Text="Skills: " Grid.Row="2" Grid.Column="0" />
                <Entry Text="{Binding Skills}"
                       Grid.Row="2" Grid.Column="1" />

            </Grid>
        </Grid>

        <!-- Submit and Cancel Buttons -->
        <Grid Grid.Row="2">
            <Grid.ColumnDefinitions>
                <ColumnDefinition Width="*" />
                <ColumnDefinition Width="*" />
            </Grid.ColumnDefinitions>

            <Button Text="Submit"
                    Grid.Column="0"
                    Command="{Binding SubmitCommand}"
                    VerticalOptions="CenterAndExpand" />

            <Button Text="Cancel"
                    Grid.Column="1"
                    Command="{Binding CancelCommand}"
                    VerticalOptions="CenterAndExpand" />
        </Grid>

        <!-- List of Persons -->
        <ListView Grid.Row="3"
                  ItemsSource="{Binding Persons}" />
    </Grid>
</ContentPage>
```

Aqui está como ele funciona: O usuário primeiro pressionar o **New** botão. Isso permite que o formulário de entrada, mas desabilita os **New** botão. O usuário insere, em seguida, um nome, idade e habilidades. A qualquer momento durante a edição, o usuário pode pressionar o **Cancelar** botão recomeçar. Somente quando um nome e uma idade válida foram inseridos é o **enviar** botão habilitado. Pressionar isso **Submit** botão transfere a pessoa para a coleção exibida pelo `ListView`. Depois de uma a **Cancelar** ou **enviar** botão é pressionado, o formulário de entrada é limpo e o **New** botão é habilitado novamente.

A tela do iOS à esquerda mostra o layout antes de um tempo de vida válido for inserido. O Android e UWP telas mostram a **enviar** botão habilitado após a configuração de uma idade:

[![Entrada da pessoa](commanding-images/personentry-small.png "entrada pessoa")](commanding-images/personentry-large.png#lightbox "entrada pessoa")

O programa não tem nenhum recurso para editar entradas existentes e não salva as entradas quando você navega para fora da página.

Toda a lógica para o **New**, **enviar**, e **Cancelar** botões é tratado no `PersonCollectionViewModel` através de definições do `NewCommand`, `SubmitCommand`, e `CancelCommand` propriedades. O construtor do `PersonCollectionViewModel` define essas três propriedades para objetos do tipo `Command`.  

Um [construtor](xref:Xamarin.Forms.Command.%23ctor(System.Action,System.Func{System.Boolean})) da `Command` classe permite que você passe argumentos de tipo `Action` e `Func<bool>` correspondente para o `Execute` e `CanExecute` métodos. É mais fácil de definir essas ações e funções como diretamente de funções lambda no `Command` construtor. Aqui está a definição do `Command` do objeto para o `NewCommand` propriedade:

```csharp
public class PersonCollectionViewModel : INotifyPropertyChanged
{

    ···

    public PersonCollectionViewModel()
    {
        NewCommand = new Command(
            execute: () =>
            {
                PersonEdit = new PersonViewModel();
                PersonEdit.PropertyChanged += OnPersonEditPropertyChanged;
                IsEditing = true;
                RefreshCanExecutes();
            },
            canExecute: () =>
            {
                return !IsEditing;
            });

        ···

    }

    void OnPersonEditPropertyChanged(object sender, PropertyChangedEventArgs args)
    {
        (SubmitCommand as Command).ChangeCanExecute();
    }

    void RefreshCanExecutes()
    {
        (NewCommand as Command).ChangeCanExecute();
        (SubmitCommand as Command).ChangeCanExecute();
        (CancelCommand as Command).ChangeCanExecute();
    }

    ···

}
```

Quando o usuário clica o **New** botão, o `execute` função passada para o `Command` construtor é executado. Isso cria uma nova `PersonViewModel` de objeto, define um manipulador naquele objeto `PropertyChanged` evento, define `IsEditing` ao `true`e chama o `RefreshCanExecutes` método definido após o construtor.

Além de implementar o `ICommand` interface, o `Command` classe também define um método chamado `ChangeCanExecute`. O ViewModel deve chamar `ChangeCanExecute` para um `ICommand` propriedade sempre que qualquer coisa acontece que pode alterar o valor de retorno de `CanExecute` método. Uma chamada para `ChangeCanExecute` faz com que o `Command` classe para acionar o `CanExecuteChanged` método. O `Button` anexou um manipulador para o evento e responde chamando `CanExecute` novamente e, em seguida, permitindo que se baseia no valor de retorno desse método.

Quando o `execute` método de `NewCommand` chamadas `RefreshCanExecutes`, o `NewCommand` propriedade recebe uma chamada para `ChangeCanExecute`e o `Button` chamadas a `canExecute` método, que agora retorna `false` porque o `IsEditing`agora é de propriedade `true`.

O `PropertyChanged` manipulador para o novo `PersonViewModel` chamadas do objeto de `ChangeCanExecute` método de `SubmitCommand`. Aqui está como essa propriedade de comando é implementada:


```csharp
public class PersonCollectionViewModel : INotifyPropertyChanged
{

    ···

    public PersonCollectionViewModel()
    {

        ···

        SubmitCommand = new Command(
            execute: () =>
            {
                Persons.Add(PersonEdit);
                PersonEdit.PropertyChanged -= OnPersonEditPropertyChanged;
                PersonEdit = null;
                IsEditing = false;
                RefreshCanExecutes();
            },
            canExecute: () =>
            {
                return PersonEdit != null &&
                       PersonEdit.Name != null &&
                       PersonEdit.Name.Length > 1 &&
                       PersonEdit.Age > 0;
            });

        ···
    }

    ···

}
```

O `canExecute` função para `SubmitCommand` é chamado sempre que houver uma propriedade alterada no `PersonViewModel` do objeto que está sendo editado. Ele retorna `true` somente quando o `Name` propriedade é pelo menos um caractere de comprimento, e `Age` é maior que 0. Nesse momento, o **enviar** botão fica habilitado.

O `execute` funcionar por **enviar** remove o manipulador de propriedade alterada do `PersonViewModel`, adiciona o objeto para o `Persons` coleção e retorna todos os itens para condições iniciais.

O `execute` funcionar para o **Cancelar** botão faz tudo o que o **enviar** botão exceto adicionar o objeto à coleção:

```csharp
public class PersonCollectionViewModel : INotifyPropertyChanged
{

    ···

    public PersonCollectionViewModel()
    {

        ···

        CancelCommand = new Command(
            execute: () =>
            {
                PersonEdit.PropertyChanged -= OnPersonEditPropertyChanged;
                PersonEdit = null;
                IsEditing = false;
                RefreshCanExecutes();
            },
            canExecute: () =>
            {
                return IsEditing;
            });
    }

    ···

}
```

O `canExecute` retorn `true` a qualquer momento uma `PersonViewModel` está sendo editado.

Essas técnicas pode ser adaptadas para cenários mais complexos: uma propriedade em `PersonCollectionViewModel` pode ser associado ao `SelectedItem` propriedade do `ListView` para a edição de itens existentes e um **excluir** botão pode ser adicionado ao excluir Esses itens.

Não é necessário definir as `execute` e `canExecute` métodos como funções lambda. Você pode gravá-las em métodos privados regulares no ViewModel e referenciá-los no `Command` construtores. No entanto, essa abordagem tendem a resultar em uma grande quantidade de métodos que são referenciados apenas uma vez no ViewModel.

## <a name="using-command-parameters"></a>Usando parâmetros de comando

Às vezes, é conveniente para um ou mais botões (ou outros objetos de interface do usuário) compartilhar o mesmo `ICommand` propriedade no ViewModel. Nesse caso, você usa o `CommandParameter` propriedade para distinguir entre os botões.

Você pode continuar a usar o `Command` classe essas compartilhado `ICommand` propriedades. A classe define um [construtor alternativo](xref:Xamarin.Forms.Command.%23ctor(System.Action{System.Object},System.Func{System.Object,System.Boolean})) que aceita `execute` e `canExecute` métodos com parâmetros de tipo `Object`. Isso é como o `CommandParameter` é passado para esses métodos.

No entanto, ao usar `CommandParameter`, é mais fácil de usar o genérico [ `Command<T>` ](xref:Xamarin.Forms.Command`1) classe para especificar o tipo do objeto definido como `CommandParameter`. O `execute` e `canExecute` métodos que você especificar têm parâmetros desse tipo.

O **teclado Decimal** página ilustra essa técnica, mostrando como implementar um teclado numérico para inserir números decimais. O `BindingContext` para o `Grid` é um `DecimalKeypadViewModel`. O `Entry` propriedade desse ViewModel é associada ao `Text` propriedade de um `Label`. Todos os `Button` objetos são associados a vários comandos no ViewModel: `ClearCommand`, `BackspaceCommand`, e `DigitCommand`:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:DataBindingDemos"
             x:Class="DataBindingDemos.DecimalKeypadPage"
             Title="Decimal Keyboard">

    <Grid WidthRequest="240"
          HeightRequest="480"
          ColumnSpacing="2"
          RowSpacing="2"
          HorizontalOptions="Center"
          VerticalOptions="Center">

        <Grid.BindingContext>
            <local:DecimalKeypadViewModel />
        </Grid.BindingContext>

        <Grid.Resources>
            <ResourceDictionary>
                <Style TargetType="Button">
                    <Setter Property="FontSize" Value="32" />
                    <Setter Property="BorderWidth" Value="1" />
                    <Setter Property="BorderColor" Value="Black" />
                </Style>
            </ResourceDictionary>
        </Grid.Resources>

        <Label Text="{Binding Entry}"
               Grid.Row="0" Grid.Column="0" Grid.ColumnSpan="3"
               FontSize="32"
               LineBreakMode="HeadTruncation"
               VerticalTextAlignment="Center"
               HorizontalTextAlignment="End" />

        <Button Text="CLEAR"
                Grid.Row="1" Grid.Column="0" Grid.ColumnSpan="2"
                Command="{Binding ClearCommand}" />

        <Button Text="&#x21E6;"
                Grid.Row="1" Grid.Column="2"
                Command="{Binding BackspaceCommand}" />

        <Button Text="7"
                Grid.Row="2" Grid.Column="0"
                Command="{Binding DigitCommand}"
                CommandParameter="7" />

        <Button Text="8"
                Grid.Row="2" Grid.Column="1"
                Command="{Binding DigitCommand}"
                CommandParameter="8" />

        <Button Text="9"
                Grid.Row="2" Grid.Column="2"
                Command="{Binding DigitCommand}"
                CommandParameter="9" />

        <Button Text="4"
                Grid.Row="3" Grid.Column="0"
                Command="{Binding DigitCommand}"
                CommandParameter="4" />

        <Button Text="5"
                Grid.Row="3" Grid.Column="1"
                Command="{Binding DigitCommand}"
                CommandParameter="5" />

        <Button Text="6"
                Grid.Row="3" Grid.Column="2"
                Command="{Binding DigitCommand}"
                CommandParameter="6" />

        <Button Text="1"
                Grid.Row="4" Grid.Column="0"
                Command="{Binding DigitCommand}"
                CommandParameter="1" />

        <Button Text="2"
                Grid.Row="4" Grid.Column="1"
                Command="{Binding DigitCommand}"
                CommandParameter="2" />

        <Button Text="3"
                Grid.Row="4" Grid.Column="2"
                Command="{Binding DigitCommand}"
                CommandParameter="3" />

        <Button Text="0"
                Grid.Row="5" Grid.Column="0" Grid.ColumnSpan="2"
                Command="{Binding DigitCommand}"
                CommandParameter="0" />

        <Button Text="&#x00B7;"
                Grid.Row="5" Grid.Column="2"
                Command="{Binding DigitCommand}"
                CommandParameter="." />
    </Grid>
</ContentPage>
```

Os botões de 11 para o ponto decimal e de 10 dígitos compartilham uma associação a `DigitCommand`. O `CommandParameter` faz distinção entre esses botões. O valor definido como `CommandParameter` geralmente é o mesmo que o texto exibido pelo botão, exceto para o ponto decimal, que, para fins de esclarecimento, é exibido com um caractere de ponto no meio.

Aqui está o programa em ação:

[![Teclado decimal](commanding-images/decimalkeyboard-small.png "teclado Decimal")](commanding-images/decimalkeyboard-large.png#lightbox "teclado Decimal")

Observe que o botão para o ponto decimal em todos os três capturas de tela está desabilitado porque o número inserido já contém um ponto decimal.

O `DecimalKeypadViewModel` define uma `Entry` propriedade do tipo `string` (que é a única propriedade que dispara uma `PropertyChanged` eventos) e três propriedades do tipo `ICommand`:

```csharp
public class DecimalKeypadViewModel : INotifyPropertyChanged
{
    string entry = "0";

    public event PropertyChangedEventHandler PropertyChanged;

    ···

    public string Entry
    {
        private set
        {
            if (entry != value)
            {
                entry = value;
                PropertyChanged?.Invoke(this, new PropertyChangedEventArgs("Entry"));
            }
        }
        get
        {
            return entry;
        }
    }

    public ICommand ClearCommand { private set; get; }

    public ICommand BackspaceCommand { private set; get; }

    public ICommand DigitCommand { private set; get; }
}
```

O botão correspondente a `ClearCommand` está sempre habilitado e simplesmente define a entrada de volta para "0":

```csharp
public class DecimalKeypadViewModel : INotifyPropertyChanged
{

    ···

    public DecimalKeypadViewModel()
    {
        ClearCommand = new Command(
            execute: () =>
            {
                Entry = "0";
                RefreshCanExecutes();
            });

        ···

    }

    void RefreshCanExecutes()
    {
        ((Command)BackspaceCommand).ChangeCanExecute();
        ((Command)DigitCommand).ChangeCanExecute();
    }

    ···

}
```

Como o botão sempre está habilitado, não é necessário especificar uma `canExecute` argumento no `Command` construtor.

A lógica para inserir números e BACKSPACE é um pouco complicado, porque se nenhum dígito tiverem sido inserido, então o `Entry` propriedade é a cadeia de caracteres "0". Se o usuário digita mais zeros, em seguida, a `Entry` ainda contém apenas um zero. Se o usuário digitar qualquer outro dígito, esse dígito substitui o zero. Mas se o usuário digita um ponto decimal, antes de qualquer outro dígito, em seguida, `Entry` é a cadeia de caracteres "0".

O **Backspace** botão é habilitado apenas quando o tamanho da entrada é maior que 1, ou se `Entry` não é igual à cadeia de caracteres "0":

```csharp
public class DecimalKeypadViewModel : INotifyPropertyChanged
{

    ···

    public DecimalKeypadViewModel()
    {

        ···

        BackspaceCommand = new Command(
            execute: () =>
            {
                Entry = Entry.Substring(0, Entry.Length - 1);
                if (Entry == "")
                {
                    Entry = "0";
                }
                RefreshCanExecutes();
            },
            canExecute: () =>
            {
                return Entry.Length > 1 || Entry != "0";
            });

        ···

    }

    ···

}
```

A lógica para o `execute` funcionar para o **Backspace** botão garante que o `Entry` é pelo menos uma cadeia de caracteres "0".

O `DigitCommand` propriedade está associada aos 11 botões, cada um dos quais identifica-se com o `CommandParameter` propriedade. O `DigitCommand` poderia ser definido como uma instância normais `Command` classe, mas ele do mais fácil de usar o `Command<T>` classe genérica. Ao usar a interface de comando com XAML, o `CommandParameter` propriedades geralmente são cadeias de caracteres, e que é o tipo de argumento genérico. O `execute` e `canExecute` funções têm, em seguida, os argumentos de tipo `string`:

```csharp
public class DecimalKeypadViewModel : INotifyPropertyChanged
{

    ···

    public DecimalKeypadViewModel()
    {

        ···

        DigitCommand = new Command<string>(
            execute: (string arg) =>
            {
                Entry += arg;
                if (Entry.StartsWith("0") && !Entry.StartsWith("0."))
                {
                    Entry = Entry.Substring(1);
                }
                RefreshCanExecutes();
            },
            canExecute: (string arg) =>
            {
                return !(arg == "." && Entry.Contains("."));
            });
    }

    ···

}
```

O `execute` método acrescenta o argumento de cadeia de caracteres para o `Entry` propriedade. No entanto, se o resultado começa com um zero (mas não um zero e um ponto decimal), em seguida, esse zero inicial deve ser removido usando o `Substring` função.

O `canExecute` retorn `false` somente se o argumento é o ponto decimal (indicando que o ponto decimal está sendo pressionado) e `Entry` já contém um ponto decimal.

Todos os as `execute` chamada de métodos `RefreshCanExecutes`, que chama `ChangeCanExecute` para ambos `DigitCommand` e `ClearCommand`. Isso garante que o ponto decimal e botões backspace estão habilitados ou desabilitados com base na sequência de dígitos inseridas atual.

## <a name="adding-commands-to-existing-views"></a>Adicionando comandos a modos de exibição existentes

Se você quiser usar a interface de comando com modos de exibição que não dão suporte a ele, é possível usar um comportamento de xamarin. Forms que converte um evento em um comando. Isso é descrito no artigo [ **reutilizável EventToCommandBehavior**](~/xamarin-forms/app-fundamentals/behaviors/reusable/event-to-command-behavior.md).

## <a name="asynchronous-commanding-for-navigation-menus"></a>Assíncrona dos comandos para os Menus de navegação

Comandos são conveniente para implementar os menus de navegação, como a mostrada na [ **demonstrações de associação de dados** ](https://developer.xamarin.com/samples/xamarin-forms/DataBindingDemos/) de programa em si. Aqui está parte do **MainPage. XAML**:

```xaml
<?xml version="1.0" encoding="utf-8" ?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:DataBindingDemos"
             x:Class="DataBindingDemos.MainPage"
             Title="Data Binding Demos"
             Padding="10">
    <TableView Intent="Menu">
        <TableRoot>
            <TableSection Title="Basic Bindings">

                <TextCell Text="Basic Code Binding"
                          Detail="Define a data-binding in code"
                          Command="{Binding NavigateCommand}"
                          CommandParameter="{x:Type local:BasicCodeBindingPage}" />

                <TextCell Text="Basic XAML Binding"
                          Detail="Define a data-binding in XAML"
                          Command="{Binding NavigateCommand}"
                          CommandParameter="{x:Type local:BasicXamlBindingPage}" />

                <TextCell Text="Alternative Code Binding"
                          Detail="Define a data-binding in code without a BindingContext"
                          Command="{Binding NavigateCommand}"
                          CommandParameter="{x:Type local:AlternativeCodeBindingPage}" />

                ···

            </TableSection>
        </TableRoot>
    </TableView>
</ContentPage>
```

Ao usar comandos com XAML, `CommandParameter` propriedades geralmente são definidas como cadeias de caracteres. No entanto, nesse caso, uma extensão de marcação XAML é usada para que o `CommandParameter` é do tipo `System.Type`.

Cada `Command` propriedade é associada a uma propriedade chamada `NavigateCommand`. Se a propriedade está definida no arquivo code-behind, **MainPage.xaml.cs**:

```csharp
public partial class MainPage : ContentPage
{
    public MainPage()
    {
        InitializeComponent();

        NavigateCommand = new Command<Type>(
            async (Type pageType) =>
            {
                Page page = (Page)Activator.CreateInstance(pageType);
                await Navigation.PushAsync(page);
            });

        BindingContext = this;
    }

    public ICommand NavigateCommand { private set; get; }
}
```

O construtor define a `NavigateCommand` propriedade para um `execute` método que instancia o `System.Type` parâmetro e, em seguida, navega até ela. Porque o `PushAsync` chamada requer um `await` operador, o `execute` método deve ser sinalizado como assíncronos. Isso é feito com o `async` palavra-chave antes da lista de parâmetros.

O construtor também define o `BindingContext` da página para si mesmo para que as associações de fazer referência a `NavigateCommand` nessa classe.

A ordem do código nesse construtor faz uma diferença: os `InitializeComponent` chamada faz com que o XAML a ser analisada, mas no momento a associação a uma propriedade denominada `NavigateCommand` não pode ser resolvida porque `BindingContext` é definido como `null`. Se o `BindingContext` é definido no construtor *antes de* `NavigateCommand` for definido, a associação pode ser resolvido quando `BindingContext` estiver definido, mas, nesse momento, `NavigateCommand` ainda é `null`. Definindo `NavigateCommand` após `BindingContext` não terá nenhum efeito sobre a associação, porque uma alteração `NavigateCommand` não dispara uma `PropertyChanged` evento e a associação não sabe que `NavigateCommand` agora é válido.

Configurando ambos `NavigateCommand` e `BindingContext` (em qualquer ordem) antes da chamada a `InitializeComponent` funcionará porque ambos os componentes da associação são definidos quando o analisador XAML encontra a definição de associação.

Associações de dados, às vezes, podem ser complicadas, mas como você viu nesta série de artigos, eles são poderoso e versátil e ajudam muito para organizar seu código, separando a lógica subjacente da interface do usuário.

## <a name="related-links"></a>Links relacionados

- [Demonstrações de associação de dados (amostra)](https://developer.xamarin.com/samples/xamarin-forms/DataBindingDemos/)
- [Capítulo de associação de dados do catálogo de xamarin. Forms](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter18.md)
