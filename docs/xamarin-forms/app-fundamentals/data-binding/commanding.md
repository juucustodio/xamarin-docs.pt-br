---
title: A interface de comando do Xamarin.Forms
description: Este artigo explica como implementar a propriedade Command com as associações de dados do Xamarin.Forms. A interface de comando oferece uma abordagem alternativa à implementação de comandos, que é bem mais adequada à arquitetura MVVM.
ms.prod: xamarin
ms.assetid: 69922284-F398-45C3-B4CC-B8E29BB4C533
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/05/2018
ms.openlocfilehash: 7d442d14589b35632bce2b6caec09235138ec585
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70771635"
---
# <a name="the-xamarinforms-command-interface"></a>A interface de comando do Xamarin.Forms

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/databindingdemos)

Na arquitetura MVVM (Model-View-ViewModel), as associações de dados são definidas entre propriedades no ViewModel, que geralmente é uma classe derivada de `INotifyPropertyChanged`, e propriedades no View, que geralmente é o arquivo XAML. Às vezes, um aplicativo tem necessidades que vão além dessas associações de propriedade ao exigir que o usuário inicie os comandos que afetam algo no ViewModel. Esses comandos geralmente são sinalizados por cliques de botões ou toques de dedos e são tradicionalmente processados no arquivo code-behind em um manipulador para o evento `Clicked` do `Button` ou o evento `Tapped` de um `TapGestureRecognizer`.

A interface de comando oferece uma abordagem alternativa à implementação de comandos, que é bem mais adequada à arquitetura MVVM. O próprio ViewModel pode conter comandos, que são métodos executados em reação a uma atividade específica no View, como um clique `Button`. Associações de dados são definidas entre esses comandos e o `Button`.

Para permitir uma associação de dados entre um `Button` e um ViewModel, o `Button` define duas propriedades:

- [`Command`](xref:Xamarin.Forms.Button.Command) do tipo [`System.Windows.Input.ICommand`](xref:System.Windows.Input.ICommand)
- [`CommandParameter`](xref:Xamarin.Forms.Button.CommandParameter) do tipo `Object`

Para usar a interface de comando, defina uma associação de dados que direcione a propriedade `Command` do `Button`, em que a origem é uma propriedade no ViewModel do tipo `ICommand`. O ViewModel contém um código associado a essa propriedade `ICommand` executada quando se clica no botão. É possível definir `CommandParameter` como dados arbitrários para distinguir entre vários botões se eles forem todos associados à mesma propriedade `ICommand` no ViewModel.

As propriedades `Command` e `CommandParameter` também são definidas pelas seguintes classes:

- [`MenuItem`](xref:Xamarin.Forms.MenuItem) e [`ToolbarItem`](xref:Xamarin.Forms.ToolbarItem), portanto, derivado de `MenuItem`
- [`TextCell`](xref:Xamarin.Forms.TextCell) e [`ImageCell`](xref:Xamarin.Forms.ImageCell), portanto, derivado de `TextCell`
- [`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer)

[`SearchBar`](xref:Xamarin.Forms.SearchBar) define uma propriedade [`SearchCommand`](xref:Xamarin.Forms.SearchBar.SearchCommand) do tipo `ICommand` e uma propriedade [`SearchCommandParameter`](xref:Xamarin.Forms.SearchBar.SearchCommandParameter). A propriedade [`RefreshCommand`](xref:Xamarin.Forms.ListView.RefreshCommand) de [`ListView`](xref:Xamarin.Forms.ListView) também é do tipo `ICommand`.

Todos esses comandos podem ser manipulados dentro de um ViewModel, de forma que ele não dependa do objeto específico da interface do usuário no View.

## <a name="the-icommand-interface"></a>A interface ICommand

A interface [`System.Windows.Input.ICommand`](xref:System.Windows.Input.ICommand) não faz parte do Xamarin.Forms. Ela é definida no lugar do namespace [System.Windows.Input](xref:System.Windows.Input) e consiste em dois métodos e um evento:

```csharp
public interface ICommand
{
    public void Execute (Object parameter);

    public bool CanExecute (Object parameter);

    public event EventHandler CanExecuteChanged;
}
```

Para usar a interface de comando, o ViewModel contém as propriedades do tipo `ICommand`:

```csharp
public ICommand MyCommand { private set; get; }
```

O ViewModel também deve referenciar uma classe que implementa a interface `ICommand`. Essa classe será descrita em breve. No View, a propriedade `Command` de um `Button` é associada a essa propriedade:

```xaml
<Button Text="Execute command"
        Command="{Binding MyCommand}" />
```

Quando o usuário pressiona o `Button`, o `Button` chama o método `Execute` no objeto `ICommand` associado a sua propriedade `Command`. Essa é a parte mais simples da interface de comando.

O método `CanExecute` é mais complexo. Quando a associação é definida na propriedade `Command` do `Button` pela primeira vez e quando a associação de dados é alterada de alguma forma, o `Button` chama o método `CanExecute` no objeto `ICommand`. Se `CanExecute` retornar `false`, então o `Button` se desabilitará. Isso indica que o comando específico não está disponível no momento ou é inválido.

O `Button` também anexa um manipulador no evento `CanExecuteChanged` de `ICommand`. O evento é acionado de dentro do ViewModel. Quando o evento é acionado, o `Button` chama o `CanExecute` novamente. O `Button` habilita a si mesmo se o `CanExecute` retorna `true` e desabilita a si mesmo se `CanExecute` retorna `false`.

> [!IMPORTANT]
> Não use a propriedade `IsEnabled` de `Button` se estiver usando a interface de comando.  

## <a name="the-command-class"></a>A classe de comando

Quando o ViewModel define uma propriedade do tipo `ICommand`, ele também deve conter ou referenciar uma classe que implementa a interface `ICommand`. Essa classe deverá conter ou referenciar os métodos `Execute` e `CanExecute` e acionar o evento `CanExecuteChanged` sempre que o método `CanExecute` puder retornar um valor diferente.

É possível gravar essa classe sozinho ou usar uma classe que alguém tenha gravado. Como o `ICommand` faz parte do Microsoft Windows, ele tem sido usado por anos com aplicativos da MVVM do Windows. Usar uma classe do Windows que implementa o `ICommand` permite que você compartilhe seus ViewModels entre aplicativos do Windows e aplicativos Xamarin.Forms.

Se o compartilhamento de ViewModels entre o Windows e o Xamarin.Forms não for uma preocupação, você poderá usar a classe [`Command`](xref:Xamarin.Forms.Command) ou [`Command<T>`](xref:Xamarin.Forms.Command`1) incluída no Xamarin.Forms para implementar a interface `ICommand`. Essas classes permitem que você especifique os corpos dos métodos `Execute` e `CanExecute` em construtores de classe. Use `Command<T>` quando você usar a propriedade `CommandParameter` para distinguir entre vários modos de exibição associados à mesma propriedade `ICommand`, e a classe `Command` mais simples quando isso não for um requisito.

## <a name="basic-commanding"></a>Comando básico

A página **Entrada de pessoa** no programa [**Demonstrações de associação de dados**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/databindingdemos) demonstra alguns comandos simples implementados em um ViewModel.

O `PersonViewModel` define três propriedades denominadas `Name`, `Age` e `Skills` que definem uma pessoa. Essa classe *não* contém nenhuma propriedade `ICommand`:

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

Essa listagem abreviada não inclui o construtor da classe, que é onde as três propriedades do tipo `ICommand` são definidas, que serão mostradas em breve. Observe que as alterações nas três propriedades do tipo `ICommand` e a propriedade `Persons` não faz os eventos `PropertyChanged` serem acionados. Todas essas propriedades são definidas quando a classe é criada pela primeira vez e não são alteradas depois disso.

Antes de examinar o construtor da classe `PersonCollectionViewModel`, vamos examinar se há o programa **Entrada de pessoa** no arquivo XAML. Ele contém um `Grid` com sua propriedade `BindingContext` definida como o `PersonCollectionViewModel`. O `Grid` contém um `Button` com o texto **Novo** com sua propriedade `Command` associada à propriedade `NewCommand` no ViewModel, um formulário de entrada com propriedades associadas à propriedade `IsEditing`, bem como propriedades de `PersonViewModel` e mais dois botões associados às propriedades `SubmitCommand` e `CancelCommand` do ViewModel. O último `ListView` exibe a coleção de pessoas já que já entraram:

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

Veja como funciona: O usuário primeiro pressiona o botão **Novo**. Isso habilita o formulário de entrada, mas desabilita o botão **Novo**. O usuário insere, então, um nome, idade e habilidades. A qualquer momento durante a edição, o usuário pode pressionar o botão **Cancelar** para recomeçar. Somente depois que um nome e uma idade válida forem inseridos, o botão **Enviar** será habilitado. Pressionar esse botão **Enviar** transfere a pessoa para a coleção exibida pelo `ListView`. Depois que o botão **Cancelar** ou **Enviar** é pressionado, o formulário de entrada é limpo e o botão **Novo** é habilitado novamente.

A tela do iOS à esquerda mostra o layout antes que uma idade válida seja inserida. As telas do Android e da UWP mostram o botão **Enviar** habilitado depois que uma idade é definida:

[![Entrada de pessoa](commanding-images/personentry-small.png "Entrada de pessoa")](commanding-images/personentry-large.png#lightbox "Entrada de pessoa")

O programa não tem nenhum recurso para editar entradas existentes nem salva as entradas quando você sai da página.

Toda a lógica dos botões **Novo**, **Enviar**, e **Cancelar** é tratado em `PersonCollectionViewModel` por meio das definições das propriedades `NewCommand`, `SubmitCommand` e `CancelCommand`. O construtor do `PersonCollectionViewModel` define essas três propriedades para objetos do tipo `Command`.  

Um [construtor](xref:Xamarin.Forms.Command.%23ctor(System.Action,System.Func{System.Boolean})) da classe `Command` permite que você passe argumentos do tipo `Action` e `Func<bool>` correspondentes aos métodos `Execute` e `CanExecute`. É mais fácil definir essas ações e funções como funções lambda direto no construtor `Command`. Veja a definição do objeto `Command` para a propriedade `NewCommand`:

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

Quando o usuário clica no botão **Novo**, a função `execute` passada para o construtor `Command` é executada. Isso cria um novo objeto `PersonViewModel`, define um manipulador no evento `PropertyChanged` desse objeto, define `IsEditing` como `true` e chama o método `RefreshCanExecutes` definido após o construtor.

Além de implementar a interface `ICommand`, a classe `Command` também define um método chamado `ChangeCanExecute`. O ViewModel deve chamar `ChangeCanExecute` para uma propriedade `ICommand` sempre que acontecer qualquer coisa que possa alterar o valor retornado do método `CanExecute`. Uma chamada ao `ChangeCanExecute` faz a classe `Command` acionar o método `CanExecuteChanged`. O `Button` anexou um manipulador para o evento, responde chamando `CanExecute` novamente e permitindo que se baseie no valor retornado desse método.

Quando o método `execute` de `NewCommand` chama `RefreshCanExecutes`, a propriedade `NewCommand` obtém uma chamada a `ChangeCanExecute`, e o `Button` chama o método `canExecute`, que agora retorna `false` porque a propriedade `IsEditing` agora é `true`.

O manipulador `PropertyChanged` do novo objeto `PersonViewModel` chama o método `ChangeCanExecute` de `SubmitCommand`. Veja como essa propriedade de comando é implementada:

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

A função `canExecute` para `SubmitCommand` é chamada sempre que há uma propriedade alterada no objeto `PersonViewModel` que está sendo editado. Ela retorna `true` somente quando a propriedade `Name` tem pelo menos um caractere de comprimento e `Age` é maior que 0. Nesse momento, o botão **Enviar** fica habilitado.

A função `execute` para **Enviar** remove o manipulador de propriedade alterada do `PersonViewModel`, adiciona o objeto à coleção `Persons` e retorna tudo para as condições iniciais.

A função `execute` para o botão **Cancelar** faz tudo o que o botão **Enviar** faz, exceto adicionar o objeto à coleção:

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

O método `canExecute` retorna `true` a qualquer momento que um `PersonViewModel` está sendo editado.

Essas técnicas poderiam ser adaptadas para cenários mais complexos: Uma propriedade no `PersonCollectionViewModel` poderia estar associada à propriedade `SelectedItem` do `ListView` para editar itens existentes e o botão **Excluir** poderia ser adicionado para excluir esses itens.

Não é necessário definir os métodos `execute` e `canExecute` como funções lambda. É possível gravá-los como métodos privados regulares no ViewModel e referenciá-los nos construtores `Command`. No entanto, essa abordagem tende a resultar em uma grande quantidade de métodos referenciados apenas uma vez no ViewModel.

## <a name="using-command-parameters"></a>Uso de parâmetros de comando

Às vezes, é conveniente para um ou mais botões (ou outros objetos da interface do usuário) compartilhar a mesma propriedade `ICommand` no ViewModel. Nesse caso, você usa a propriedade `CommandParameter` para distinguir entre os botões.

É possível continuar usando a classe `Command` para essas propriedades `ICommand` compartilhadas. A classe define um [construtor alternativo](xref:Xamarin.Forms.Command.%23ctor(System.Action{System.Object},System.Func{System.Object,System.Boolean})) que aceita os métodos `execute` e `canExecute` com parâmetros do tipo `Object`. Isso é como o `CommandParameter` é passado para esses métodos.

No entanto, ao usar `CommandParameter`, é mais fácil de usar a classe [`Command<T>`](xref:Xamarin.Forms.Command`1) genérica para especificar o tipo do objeto definido como `CommandParameter`. Os métodos `execute` e `canExecute` que você especificar têm parâmetros desse tipo.

A página **Teclado decimal** ilustra essa técnica mostrando como implementar um teclado numérico para inserir números decimais. O `BindingContext` para o `Grid` é um `DecimalKeypadViewModel`. A propriedade `Entry` desse ViewModel está associada à propriedade `Text` de um `Label`. Todos os objetos `Button` estão associados a vários comandos no ViewModel: `ClearCommand`, `BackspaceCommand` e `DigitCommand`:

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

Os 11 botões para os 10 dígitos e o ponto decimal compartilham uma associação a `DigitCommand`. O `CommandParameter` faz distinção entre esses botões. O valor definido como `CommandParameter` geralmente é o mesmo que o texto exibido pelo botão, exceto para o ponto decimal, que, para fins de esclarecimento, é exibido com um caractere de ponto no meio.

Veja o programa em ação:

[![Teclado decimal](commanding-images/decimalkeyboard-small.png "Teclado decimal")](commanding-images/decimalkeyboard-large.png#lightbox "Teclado decimal")

Observe que o botão para o ponto decimal em todas as três capturas de tela está desabilitado, porque o número inserido já contém um ponto decimal.

O `DecimalKeypadViewModel` define uma propriedade `Entry` do tipo `string` (que é a única propriedade que dispara um evento `PropertyChanged`) e três propriedades do tipo `ICommand`:

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

O botão correspondente ao `ClearCommand` está sempre habilitado e simplesmente define a entrada de volta para "0":

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

Como o botão sempre está habilitado, não é necessário especificar um argumento `canExecute` no construtor `Command`.

A lógica para inserir números e backspace é um pouco complicada, porque se nenhum dígito foi inserido, a propriedade `Entry` será a cadeia de caracteres "0". Se o usuário digitar mais zeros, então o `Entry` ainda conterá apenas um zero. Se o usuário digita qualquer outro dígito, esse dígito substitui o zero. Mas se o usuário digita um ponto decimal antes de qualquer outro dígito, então `Entry` é a cadeia de caracteres "0".

O botão **Backspace** é habilitado apenas quando o comprimento da entrada é maior que 1 ou se `Entry` não é igual à cadeia de caracteres "0":

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

A lógica para a função `execute` do botão **Backspace** garante que o `Entry` é pelo menos uma cadeia de caracteres de comprimento "0".

A propriedade `DigitCommand` está associada a 11 botões, cada um dos quais identifica-se com a propriedade `CommandParameter`. O `DigitCommand` poderia ser definido como uma instância da classe `Command` regular, mas é mais fácil usar a classe genérica `Command<T>`. Ao usar a interface de comando com o XAML, as propriedades `CommandParameter` geralmente são cadeias de caracteres e esse é o tipo de argumento genérico. As funções `execute` e `canExecute` têm argumentos do tipo `string`:

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

O método `execute` acrescenta o argumento de cadeia de caracteres à propriedade `Entry`. No entanto, se o resultado começar com um zero (mas não um zero e um ponto decimal), esse zero inicial deverá ser removido usando a função `Substring`.

O método `canExecute` retornará `false` somente se o argumento for o ponto decimal (que indica que o ponto decimal está sendo pressionado) e `Entry` já contém um ponto decimal.

Todos os métodos `execute` chamam `RefreshCanExecutes`, que chama `ChangeCanExecute` para `DigitCommand` e `ClearCommand`. Isso garante que o ponto decimal e os botões backspace estão habilitados ou desabilitados com base na sequência atual de dígitos inseridos.

## <a name="adding-commands-to-existing-views"></a>Como adicionar comandos a exibições existentes

Se desejar usar a interface de comando com exibições não compatíveis com ela, será possível usar um comportamento do Xamarin.Forms que converte um evento em um comando. Isso é descrito no artigo [**EventToCommandBehavior reutilizável**](~/xamarin-forms/app-fundamentals/behaviors/reusable/event-to-command-behavior.md).

## <a name="asynchronous-commanding-for-navigation-menus"></a>Comando assíncrono para menus de navegação

Comandos são convenientes para implementar os menus de navegação, como o do programa [**Demonstrações de associação de dados**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/databindingdemos) em si. Veja uma parte do **MainPage.xaml**:

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

Ao usar comandos com XAML, as propriedades `CommandParameter` geralmente são definidas como cadeias de caracteres. No entanto, nesse caso, uma extensão de marcação XAML é usada para que o `CommandParameter` seja do tipo `System.Type`.

Cada propriedade `Command` está associada a uma propriedade chamada `NavigateCommand`. Se a propriedade estiver definida no arquivo code-behind, **MainPage.xaml.cs**:

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

O construtor define a propriedade `NavigateCommand` como um método `execute` que cria a instância do parâmetro `System.Type` e navega até ela. Como a chamada `PushAsync` requer um operador `await`, o método `execute` deve ser sinalizado como assíncrono. Isso é feito com a palavra-chave `async` antes da lista de parâmetros.

O construtor também define o `BindingContext` da página como a si mesmo para que as associações referenciem o `NavigateCommand` nessa classe.

A ordem do código nesse construtor faz a diferença: A chamada `InitializeComponent` causa a análise do XAML, mas, nesse momento, a associação a uma propriedade denominada `NavigateCommand` não pode ser resolvida, porque `BindingContext` está definido como `null`. Se o `BindingContext` for definido no construtor *antes de* `NavigateCommand` ser definido, a associação poderá ser resolvida quando `BindingContext` for definido, mas, nesse momento, `NavigateCommand` ainda será `null`. Definir `NavigateCommand` após `BindingContext` não terá efeito na associação, porque uma alteração no `NavigateCommand` não dispara um evento `PropertyChanged`, e a associação não sabe que `NavigateCommand` é válido agora.

Definir `NavigateCommand` e `BindingContext` (em qualquer ordem) antes da chamada a `InitializeComponent` funcionará, porque os componentes da associação são definidos quando o analisador XAML encontra a definição da associação.

Às vezes, as associações de dados podem ser complicadas, mas, como você viu nessa série de artigos, elas são poderosas e versáteis e ajudam bastante a organizar seu código separando a lógica subjacente da interface do usuário.

## <a name="related-links"></a>Links relacionados

- [Demonstrações de associação de dados (amostra)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/databindingdemos)
- [Capítulo de associação de dados do catálogo de Xamarin.Forms](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter18.md)
