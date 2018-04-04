---
title: A Interface de comando
description: Implementar o `Command` propriedade com associações de dados
ms.prod: xamarin
ms.assetid: 69922284-F398-45C3-B4CC-B8E29BB4C533
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/05/2018
ms.openlocfilehash: 7f8b40624b9434347f69a473eed3bdff5c1d3d33
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="the-command-interface"></a>A Interface de comando

Na arquitetura do Model-View-ViewModel (MVVM), associações de dados são definidas entre as propriedades no ViewModel, que geralmente é uma classe que deriva de `INotifyPropertyChanged`e as propriedades no modo de exibição, que geralmente é o arquivo XAML. Às vezes, um aplicativo tem necessidades que vão além essas associações de propriedade exigindo que o usuário inicie os comandos que afetam algo no ViewModel. Esses comandos geralmente são sinalizados por cliques de botão ou dedo toques e tradicionalmente, eles são processados no arquivo code-behind em um manipulador para o `Clicked` evento o `Button` ou o `Tapped` eventos de um `TapGestureRecognizer`. 

A interface de comando fornece uma abordagem alternativa à implementação de comandos que é muito mais adequada para a arquitetura MVVM. O ViewModel em si pode conter comandos, que são métodos que são executados em reação a uma atividade específica no modo de exibição, como um `Button` clique em. Associações de dados definidas entre esses comandos e o `Button`.

Para permitir que uma associação de dados entre um `Button` e um ViewModel, o `Button` define duas propriedades:

- [`Command`](https://developer.xamarin.com/api/property/Xamarin.Forms.Button.Command/) do tipo <xref:System.Windows.Input.ICommand>
- [`CommandParameter`](https://developer.xamarin.com/api/property/Xamarin.Forms.Button.CommandParameter/) do tipo `Object`

Para usar a interface de comando, você define uma associação de dados que tem como alvo o `Command` propriedade o `Button` onde a fonte é uma propriedade no ViewModel do tipo `ICommand`. O ViewModel contém código associado que `ICommand` propriedade que é executada quando o botão é clicado. Você pode definir `CommandParameter` para dados arbitrários para distinguir entre vários botões se todos eles estiverem associados ao mesmo `ICommand` propriedade no ViewModel.

O `Command` e `CommandParameter` propriedades também são definidas por classes a seguir:

- [`MenuItem`](https://developer.xamarin.com/api/type/Xamarin.Forms.MenuItem/) e, portanto, [ `ToolbarItem` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ToolbarItem/), que é derivado de `MenuItem`
- [`TextCell`](https://developer.xamarin.com/api/type/Xamarin.Forms.TextCell/) e, portanto, [ `ImageCell` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ImageCell/), que é derivado de `TextCell`
- [`TapGestureRecognizer`](https://developer.xamarin.com/api/type/Xamarin.Forms.TapGestureRecognizer/)

[`SearchBar`](https://developer.xamarin.com/api/type/Xamarin.Forms.SearchBar/) define uma [ `SearchCommand` ](https://developer.xamarin.com/api/property/Xamarin.Forms.SearchBar.SearchCommand/) propriedade do tipo `ICommand` e um [ `SearchCommandParameter` ](https://developer.xamarin.com/api/property/Xamarin.Forms.SearchBar.SearchCommandParameter/) propriedade. O [ `RefreshCommand` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ListView.RefreshCommand/) propriedade [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) também é do tipo `ICommand`. 

Todos esses comandos podem ser tratados dentro de um ViewModel de forma que não depende do objeto de interface de usuário específica no modo de exibição.

## <a name="the-icommand-interface"></a>A Interface ICommand

O <xref:System.Windows.Input.ICommand> interface não é parte do xamarin. Forms. Ele é definido em vez disso, no [Input](xref:System.Windows.Input) namespace e consiste em dois métodos e um evento:

```csharp
public interface ICommand
{
    public void Execute (Object parameter);

    public bool CanExecute (Object parameter);

    public event EventHandler CanExecuteChanged;
}
```

Para usar a interface de comando, o ViewModel contém propriedades do tipo `ICommand`:

```csharp
public ICommand MyCommand { private set; get; }
```

O ViewModel também deve fazer referência a uma classe que implementa o `ICommand` interface. Essa classe será descrita em breve. No modo de exibição, o `Command` propriedade de um `Button` está associado a essa propriedade: 

```xaml
<Button Text="Execute command"
        Command="{Binding MyCommand}" />
```

Quando o usuário pressiona o `Button`, o `Button` chama o `Execute` método no `ICommand` objeto associado a seu `Command` propriedade. Essa é a parte mais simples da interface de comando.

O `CanExecute` método é mais complexo. Quando a associação for definida no `Command` propriedade do `Button`, e quando a associação de dados é alterado de alguma forma, o `Button` chamadas a `CanExecute` método no `ICommand` objeto. Se `CanExecute` retorna `false`, em seguida, o `Button` desabilita a mesmo. Isso indica que o comando específico está disponível ou é inválido.

O `Button` também anexa um manipulador no `CanExecuteChanged` evento `ICommand`. O evento é acionado de dentro de ViewModel. Quando esse evento é disparado, o `Button` chamadas `CanExecute` novamente. O `Button` habilita a mesmo se `CanExecute` retorna `true` e desabilita a mesmo se `CanExecute` retorna `false`.

> [!IMPORTANT]
> Não use o `IsEnabled` propriedade `Button` se você estiver usando a interface de comando.  

## <a name="the-command-class"></a>A classe de comando

Quando o ViewModel define uma propriedade do tipo `ICommand`, ViewModel também deve conter ou fazer referência a uma classe que implementa o `ICommand` interface. Essa classe deve conter ou fazer referência a `Execute` e `CanExecute` métodos e acionar o `CanExecuteChanged` evento sempre que o `CanExecute` método pode retornar um valor diferente.

Você pode escrever essa classe, ou você pode usar uma classe que alguém tenha gravado. Porque `ICommand` faz parte do Microsoft Windows, ele está sendo usado por anos com aplicativos MVVM do Windows. Usando uma classe do Windows que implementa `ICommand` permite que você compartilhe seus ViewModels entre aplicativos do Windows e xamarin. Forms.

Se não for um problema de compartilhamento ViewModels entre o Windows e xamarin. Forms, você pode usar o [ `Command` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Command/) ou [ `Command<T>` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Command%3CT%3E/) classe incluída no xamarin. Forms para implementar o `ICommand`interface. Essas classes permitem que você especifique os corpos do `Execute` e `CanExecute` métodos em construtores de classe. Use `Command<T>` quando você usa o `CommandParameter` propriedade para distinguir entre vários modos de exibição associada ao mesmo `ICommand` propriedade e o mais simples `Command` classe quando que não é um requisito.

## <a name="basic-commanding"></a>Ordenar básico

O **entrada pessoa** página o [ **demonstrações de associação de dados** ](https://developer.xamarin.com/samples/xamarin-forms/DataBindingDemos/) programa demonstra alguns comandos simples implementados em um ViewModel.

O `PersonViewModel` define três propriedades chamadas `Name`, `Age`, e `Skills` que definem uma pessoa. Essa classe faz *não* conter `ICommand` propriedades:

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

O `PersonCollectionViewModel` mostrado abaixo cria novos objetos do tipo `PersonViewModel` e permite que o usuário preencher os dados. Para essa finalidade, a classe define propriedades `IsEditing` do tipo `bool` e `PersonEdit` do tipo `PersonViewModel`. Além disso, a classe define três propriedades do tipo `ICommand` e uma propriedade chamada `Persons` do tipo `IList<PersonViewModel>`: 

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

Essa lista abreviada não inclui o construtor da classe, que é onde as três propriedades de tipo `ICommand` são definidos, que será mostrado em breve. Observe que as alterações para as três propriedades de tipo `ICommand` e `Persons` propriedade não resultam em `PropertyChanged` eventos que está sendo acionados. Essas propriedades são definidas quando a classe é criada e não são alterados posteriormente.

Antes de examinar o construtor do `PersonCollectionViewModel` classe, vamos analisar o arquivo XAML para o **entrada pessoa** programa. Ele contém um `Grid` com seus `BindingContext` propriedade definida como o `PersonCollectionViewModel`. O `Grid` contém um `Button` com o texto **novo** com seu `Command` propriedade associada para o `NewCommand` um formulário de entrada com propriedades de propriedade no ViewModel, associada ao `IsEditing` propriedade, como bem como propriedades de `PersonViewModel`, e mais dois botões associado para o `SubmitCommand` e `CancelCommand` propriedades de ViewModel. O último `ListView` exibe a coleção de pessoas já inserido:

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

Aqui está como funciona: O usuário primeiro pressionar o **novo** botão. Isso permite que o formulário de entrada, mas desabilita a **novo** botão. Em seguida, o usuário insere um nome, idade e as habilidades. A qualquer momento durante a edição, o usuário pode pressionar a **Cancelar** botão para recomeçar. Somente quando um nome e um tempo de vida válido tem sido inseridos é o **enviar** botão habilitado. Pressionar isso **enviar** botão transfere a pessoa para a coleção exibida pelo `ListView`. Após um o **Cancelar** ou **enviar** botão for pressionado, o formulário de entrada é limpo e o **novo** botão é habilitado novamente.

A tela de iOS à esquerda mostra o layout antes de um tempo de vida válido é inserido. O Android e UWP telas mostram a **enviar** botão habilitada depois que tiver sido definida uma idade:

[![Person Entry](commanding-images/personentry-small.png "Person Entry")](commanding-images/personentry-large.png#lightbox "Person Entry")

O programa não tem nenhum recurso para edição de entradas existentes e não salvar as entradas quando você navegar para fora da página.

Toda a lógica para o **novo**, **enviar**, e **Cancelar** botões é tratado no `PersonCollectionViewModel` através de definições do `NewCommand`, `SubmitCommand`, e `CancelCommand` propriedades. O construtor do `PersonCollectionViewModel` define essas três propriedades para objetos do tipo `Command`.  

Um [construtor](https://developer.xamarin.com/api/constructor/Xamarin.Forms.Command.Command/p/System.Action/System.Func%7BSystem.Boolean%7D/) do `Command` classe permite que você passe os argumentos de tipo `Action` e `Func<bool>` correspondente para o `Execute` e `CanExecute` métodos. É mais fácil definir essas ações e funções como diretamente de funções de lambda no `Command` construtor. Aqui está a definição do `Command` de objeto para o `NewCommand` propriedade:

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

Quando o usuário clica o **novo** botão, o `execute` função passada para o `Command` construtor é executado. Isso cria um novo `PersonViewModel` de objeto, define um manipulador naquele objeto `PropertyChanged` , define `IsEditing` para `true`e chama o `RefreshCanExecutes` método definido após o construtor.

Além de implementar o `ICommand` interface, o `Command` classe também define um método chamado `ChangeCanExecute`. O ViewModel deve chamar `ChangeCanExecute` para um `ICommand` propriedade sempre que qualquer coisa acontece que pode alterar o valor de retorno de `CanExecute` método. Uma chamada para `ChangeCanExecute` faz com que o `Command` classe para disparar o `CanExecuteChanged` método. O `Button` anexou um manipulador para o evento e responde chamando `CanExecute` novamente e, em seguida, permitindo que se baseia no valor de retorno do método.

Quando o `execute` método `NewCommand` chamadas `RefreshCanExecutes`, o `NewCommand` propriedade obtém uma chamada para `ChangeCanExecute`e o `Button` chamadas a `canExecute` método, que agora retorna `false` porque o `IsEditing`propriedade agora é `true`.

O `PropertyChanged` manipulador para o novo `PersonViewModel` objeto chamadas a `ChangeCanExecute` método `SubmitCommand`. Aqui está como essa propriedade do comando é implementada:


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

O `canExecute` funcionar para `SubmitCommand` é chamado sempre que há uma propriedade alterada no `PersonViewModel` do objeto que está sendo editado. Ele retorna `true` somente quando o `Name` propriedade é pelo menos um caractere, e `Age` é maior que 0. Nesse momento, o **enviar** botão fica habilitado. 

O `execute` funcionar para **enviar** remove o manipulador de propriedade alterada a `PersonViewModel`, adiciona o objeto para o `Persons` coleção e retorna todos os itens a condições inicias.

O `execute` funcionar para o **Cancelar** botão faz tudo o que o **enviar** botão faz execept adicionar o objeto à coleção:

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

O `canExecute` método `true` a qualquer momento uma `PersonViewModel` está sendo editado.

Essas técnicas podem ser adaptadas para cenários mais complexos: uma propriedade em `PersonCollectionViewModel` pode ser associado ao `SelectedItem` propriedade o `ListView` para edição de itens existentes e um **excluir** botão foi adicionado para excluir Esses itens.

Não é necessário definir o `execute` e `canExecute` métodos como funções de lambda. Você pode gravá-los em métodos privados regulares no ViewModel e fazer referência a eles no `Command` construtores. No entanto, essa abordagem tendem a resultar em uma grande quantidade de métodos que são referenciados apenas uma vez no ViewModel.

## <a name="using-command-parameters"></a>Usando parâmetros de comando

Às vezes, é conveniente para um ou mais botões (ou outros objetos de interface do usuário) compartilhar o mesmo `ICommand` propriedade no ViewModel. Nesse caso, você usa o `CommandParameter` propriedade para distinguir entre os botões. 

Você pode continuar a usar o `Command` classe para esses `ICommand` propriedades. A classe define um [construtor alternativo](https://developer.xamarin.com/api/constructor/Xamarin.Forms.Command.Command/p/System.Action%7BSystem.Object%7D/System.Func%7BSystem.Object,System.Boolean%7D/) que aceita `execute` e `canExecute` métodos com parâmetros de tipo `Object`. Isso é como o `CommandParameter` é passado para esses métodos.

No entanto, ao usar `CommandParameter`, é mais fácil de usar o genérico [ `Command<T>` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Command%3CT%3E/) classe para especificar o tipo do objeto definido como `CommandParameter`. O `execute` e `canExecute` métodos que você especificar tem parâmetros de tipo.

O **teclado Decimal** página ilustra essa técnica, mostrando como implementar um teclado para inserir números decimais. O `BindingContext` para o `Grid` é um `DecimalKeypadViewModel`. O `Entry` propriedade esse ViewModel é associada ao `Text` propriedade de um `Label`. Todos os `Button` objetos estão associados a vários comandos no ViewModel: `ClearCommand`, `BackspaceCommand`, e `DigitCommand`:

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

Os botões de 11 para o ponto decimal e de 10 dígitos compartilham uma associação a `DigitCommand`. O `CommandParameter` faz distinção entre esses botões. O valor definido `CommandParameter` geralmente é o mesmo que o texto exibido pelo botão, exceto o ponto decimal, que é exibido com um caractere de ponto no meio para fins de esclarecimento.

Aqui está o programa em ação:

[![Teclado decimal](commanding-images/decimalkeyboard-small.png "teclado Decimal")](commanding-images/decimalkeyboard-large.png#lightbox "teclado Decimal")

Observe que o botão para o ponto decimal em todos os três capturas de tela está desabilitado porque o número inserido já contém um ponto decimal. 

O `DecimalKeypadViewModel` define uma `Entry` propriedade do tipo `string` (que é a única propriedade que dispara um `PropertyChanged` eventos) e três propriedades do tipo `ICommand`:

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

O botão correspondente a `ClearCommand` está sempre habilitada e simplesmente define a entrada de volta para "0":

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

Como o botão sempre está habilitado, não é necessário especificar um `canExecute` argumento o `Command` construtor.

A lógica para inserir números e BACKSPACE é um pouco difícil, porque se nenhum dígito tiverem sido inserido, então o `Entry` propriedade é a cadeia de caracteres "0". Se o usuário digita mais zeros, em seguida, o `Entry` ainda contém apenas um zero. Se o usuário digitar qualquer outro dígito, esse dígito substitui o zero. Porém, se o usuário digita um ponto decimal antes de qualquer dígito, em seguida, `Entry` é a cadeia de caracteres "0".

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

A lógica para o `execute` funcionar para o **Backspace** botão garante que o `Entry` é pelo menos uma cadeia de caracteres de "0".

O `DigitCommand` propriedade está associada aos botões de 11, cada um dos quais identifica-se com o `CommandParameter` propriedade. O `DigitCommand` pode ser definido como uma instância do normal `Command` classe, mas do mais fácil de usar o `Command<T>` classe genérica. Ao usar a interface de comando com XAML, o `CommandParameter` propriedades geralmente são cadeias de caracteres, e esse é o tipo do argumento genérico. O `execute` e `canExecute` funções, em seguida, tem argumentos de tipo `string`:

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

O `execute` método anexa o argumento de cadeia de caracteres para o `Entry` propriedade. No entanto, se o resultado começa com um zero (mas não um zero e um ponto decimal), em seguida, esse zero inicial deve ser removido usando o `Substring` função.

O `canExecute` método `false` somente se o argumento é o ponto decimal (indicando que o ponto decimal está sendo pressionado) e `Entry` já contém um ponto decimal. 

Todos os a `execute` chamada de métodos `RefreshCanExecutes`, que, em seguida, chama `ChangeCanExecute` para ambos `DigitCommand` e `ClearCommand`. Isso garante que o ponto decimal e botões backspace estão habilitados ou desabilitados com base na sequência atual de dígitos inseridos.

## <a name="adding-commands-to-existing-views"></a>Adicionando comandos a modos de exibição existentes

Se você quiser usar a interface de comando com modos de exibição que não dão suporte a ele, é possível usar um comportamento xamarin. Forms que converte um evento em um comando. Isso é descrito no artigo [ **reutilizável EventToCommandBehavior**](~/xamarin-forms/app-fundamentals/behaviors/reusable/event-to-command-behavior.md).

## <a name="asynchronous-commanding-for-navigation-menus"></a>Assíncrona de comandos de menu de navegação

Comando é conveniente para a implementação de menus de navegação, como no [ **demonstrações de associação de dados** ](https://developer.xamarin.com/samples/xamarin-forms/DataBindingDemos/) programa em si. Esta é uma parte de **MainPage. XAML**:


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

Ao usar o comando com XAML, `CommandParameter` propriedades geralmente são definidas como cadeias de caracteres. No entanto, nesse caso, uma extensão de marcação XAML é usada para que o `CommandParameter` é do tipo `System.Type`.

Cada `Command` propriedade é associada a uma propriedade denominada `NavigateCommand`. Se a propriedade está definida no arquivo code-behind, **MainPage.xaml.cs**:

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

Os conjuntos de construtor o `NavigateCommand` propriedade para um `execute` método que instancia o `System.Type` parâmetro e, em seguida, navega para ele. Porque o `PushAsync` chamada requer um `await` operador, o `execute` método deve ser sinalizado como assíncronas. Isso é feito com o `async` palavra-chave antes da lista de parâmetros. 

O construtor também define o `BindingContext` da página a mesmo para que as associações de referenciam a `NavigateCommand` nessa classe.

A ordem do código nesse construtor faz diferença: O `InitializeComponent` chamada faz com que o XAML ser analisada, mas no momento a associação a uma propriedade denominada `NavigateCommand` não pode ser resolvido porque `BindingContext` é definido como `null`. Se o `BindingContext` está definida no construtor *antes de* `NavigateCommand` for definida, a associação pode ser resolvido quando `BindingContext` for definido, mas nesse momento, `NavigateCommand` ainda é `null`. Configuração `NavigateCommand` depois `BindingContext` não tem efeito na associação porque uma alteração `NavigateCommand` não dispara uma `PropertyChanged` evento e a associação não sabe que `NavigateCommand` agora é válido.

Configurar ambos `NavigateCommand` e `BindingContext` (em qualquer ordem) antes da chamada para `InitializeComponent` funcionará, pois ambos os componentes da associação são definidos quando o analisador XAML encontra a definição da associação. 

Associações de dados às vezes podem ser difícil, mas como você viu nesta série de artigos, eles são poderoso e versátil e ajudam muito para organizar seu código, separando a lógica subjacente da interface do usuário.



## <a name="related-links"></a>Links relacionados

- [Demonstrações de associação de dados (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/DataBindingDemos/)
- [Capítulo de associação de dados do catálogo de xamarin. Forms](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter18.md)
