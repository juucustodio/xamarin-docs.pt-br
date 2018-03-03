---
title: "Parte 5. De associações de dados para o modelo MVVM"
description: "O padrão de arquitetura Model-View-ViewModel (MVVM) foi criado com XAML em mente. O padrão impõe uma separação entre três camadas de software, a interface do usuário XAML, chamada de exibição; os dados subjacentes, chamados de modelo; e um intermediário entre o modo de exibição e o modelo, chamado ViewModel. O modo de exibição e o ViewModel geralmente são conectados por meio de ligações de dados definidas no arquivo XAML. BindingContext para o modo de exibição normalmente é uma instância do ViewModel."
ms.topic: article
ms.prod: xamarin
ms.assetid: 1D6164F9-4ECE-43A6-B583-1F5D5EFC1DDF
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 10/25/2017
ms.openlocfilehash: b16aa2456cdae7a08f8f9ee8adbc32c124e78e18
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="part-5-from-data-bindings-to-mvvm"></a>Parte 5. De associações de dados para o modelo MVVM

_O padrão de arquitetura Model-View-ViewModel (MVVM) foi criado com XAML em mente. O padrão impõe uma separação entre três camadas de software, a interface do usuário XAML, chamada de exibição; os dados subjacentes, chamados de modelo; e um intermediário entre o modo de exibição e o modelo, chamado ViewModel. O modo de exibição e o ViewModel geralmente são conectados por meio de ligações de dados definidas no arquivo XAML. BindingContext para o modo de exibição normalmente é uma instância do ViewModel._

## <a name="a-simple-viewmodel"></a>Um ViewModel Simple

Como uma introdução ao ViewModels, vamos primeiro examinar um programa sem uma.
Anteriormente, você viu como definir uma nova declaração de namespace XML para permitir que um arquivo XAML para as classes de referência em outros assemblies. Aqui está um programa que define uma declaração de namespace XML para o `System` namespace:

```csharp
xmlns:sys="clr-namespace:System;assembly=mscorlib"
```

O programa pode usar `x:Static` para obter a data e hora atuais de estático `DateTime.Now` propriedade e defina-o `DateTime` o valor para o `BindingContext` em um `StackLayout`:

```xaml
<StackLayout BindingContext="{x:Static sys:DateTime.Now}" …>
```

`BindingContext` é uma propriedade muito especial: quando você define o `BindingContext` em um elemento, ela é herdada por todos os filhos desse elemento. Isso significa que todos os filhos do `StackLayout` têm esse mesmo `BindingContext`, e podem conter associações simples às propriedades desse objeto.

No **One-Shot DateTime** programa, dois filhos conter associações para propriedades que `DateTime` valor, mas dois outros filhos contenham associações que parecem estar faltando um caminho de associação. Isso significa que o `DateTime` valor em si é usado para o `StringFormat`:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:sys="clr-namespace:System;assembly=mscorlib"
             x:Class="XamlSamples.OneShotDateTimePage"
             Title="One-Shot DateTime Page">

    <StackLayout BindingContext="{x:Static sys:DateTime.Now}"
                 HorizontalOptions="Center"
                 VerticalOptions="Center">

        <Label Text="{Binding Year, StringFormat='The year is {0}'}" />
        <Label Text="{Binding StringFormat='The month is {0:MMMM}'}" />
        <Label Text="{Binding Day, StringFormat='The day is {0}'}" />
        <Label Text="{Binding StringFormat='The time is {0:T}'}" />

    </StackLayout>
</ContentPage>
```

Naturalmente, o grande problema é que a data e hora são definidos quando quando a página é compilada pela primeira vez e nunca alteração:

[ ![](data-bindings-to-mvvm-images/oneshotdatetime.png "Modo de exibição exibe a data e hora")](data-bindings-to-mvvm-images/oneshotdatetime-large.png "tela que mostra a data e hora")

Um arquivo XAML pode exibir um relógio de que sempre mostra a hora atual, mas precisa de algum código para ajudar. Ao pensar em termos de MVVM, o modelo e ViewModel são classes inteiramente escritas em código. A exibição é geralmente um arquivo XAML que faz referência a propriedades definidas no ViewModel por meio de ligações de dados.

Um modelo apropriado é com ignorância de ViewModel e um ViewModel adequado é ignorar o modo de exibição. No entanto, com muita frequência, um programador ajusta os tipos de dados expostos pelo ViewModel para os tipos de dados associados a interfaces de usuário específico. Por exemplo, se um modelo de acessar um banco de dados que contém cadeias de caracteres de ASCII de caracteres de 8 bits, o ViewModel precisa converter entre essas cadeias de caracteres em cadeias de caracteres Unicode para acomodar o uso exclusivo do Unicode na interface do usuário.

Nos exemplos simples de MVVM (como os mostrados aqui), geralmente há um modelo e o padrão envolve apenas um modo de exibição e ViewModel vinculado com associações de dados.

Aqui está um ViewModel para um clock com apenas uma única propriedade denominada `DateTime`, mas que atualizações `DateTime` propriedade por segundo:

```csharp
using System;
using System.ComponentModel;
using Xamarin.Forms;

namespace XamlSamples
{
    class ClockViewModel : INotifyPropertyChanged
    {
        DateTime dateTime;

        public event PropertyChangedEventHandler PropertyChanged;

        public ClockViewModel()
        {
            this.DateTime = DateTime.Now;

            Device.StartTimer(TimeSpan.FromSeconds(1), () =>
                {
                    this.DateTime = DateTime.Now;
                    return true;
                });
        }

        public DateTime DateTime
        {
            set
            {
                if (dateTime != value)
                {
                    dateTime = value;

                    if (PropertyChanged != null)
                    {
                        PropertyChanged(this, new PropertyChangedEventArgs("DateTime"));
                    }
                }
            }
            get
            {
                return dateTime;
            }
        }
    }
}
```

ViewModels geralmente implementa o `INotifyPropertyChanged` interface, o que significa que a classe dispara um `PropertyChanged` eventos sempre que for alterado em uma de suas propriedades. O mecanismo de associação de dados em xamarin. Forms anexa um manipulador a este `PropertyChanged` evento para ser notificado quando uma propriedade alterada e manter o destino atualizado com o novo valor.

Um relógio com base nesse ViewModel pode ser tão simple quanto esta:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:XamlSamples;assembly=XamlSamples"
             x:Class="XamlSamples.ClockPage"
             Title="Clock Page">

    <Label Text="{Binding DateTime, StringFormat='{0:T}'}"
           FontSize="Large"
           HorizontalOptions="Center"
           VerticalOptions="Center">
        <Label.BindingContext>
            <local:ClockViewModel />
        </Label.BindingContext>
    </Label>
</ContentPage>
```

Observe como o `ClockViewModel` é definido como o `BindingContext` do `Label` usando marcas de elemento de propriedade. Como alternativa, você pode instanciar o `ClockViewModel` em uma `Resources` coleção e defina-a como o `BindingContext` por meio de um `StaticResource` extensão de marcação. Ou então, o arquivo code-behind pode instanciar o ViewModel.

O `Binding` extensão de marcação no `Text` propriedade o `Label` formatos o `DateTime` propriedade. Aqui está a exibição:

[ ![](data-bindings-to-mvvm-images/clock.png "Modo de exibição exibe a data e hora por meio de ViewModel")](data-bindings-to-mvvm-images/clock-large.png "tela que mostra a data e hora por meio de ViewModel")

Também é possível acessar propriedades individuais do `DateTime` propriedade do ViewModel separando as propriedades com períodos:

```xaml
<Label Text="{Binding DateTime.Second, StringFormat='{0}'}" … >
```

## <a name="interactive-mvvm"></a>Interativo MVVM

MVVM com muita frequência, é usado com associações de dados bidirecional para uma exibição interativa com base em um modelo de dados subjacente.

Aqui está uma classe denominada `HslViewModel` que converte um `Color` valor em `Hue`, `Saturation`, e `Luminosity` valores e vice-versa:

```csharp
using System;
using System.ComponentModel;
using Xamarin.Forms;

namespace XamlSamples
{
    public class HslViewModel : INotifyPropertyChanged
    {
        double hue, saturation, luminosity;
        Color color;

        public event PropertyChangedEventHandler PropertyChanged;

        public double Hue
        {
            set
            {
                if (hue != value)
                {
                    hue = value;
                    OnPropertyChanged("Hue");
                    SetNewColor();
                }
            }
            get
            {
                return hue;
            }
        }

        public double Saturation
        {
            set
            {
                if (saturation != value)
                {
                    saturation = value;
                    OnPropertyChanged("Saturation");
                    SetNewColor();
                }
            }
            get
            {
                return saturation;
            }
        }

        public double Luminosity
        {
            set
            {
                if (luminosity != value)
                {
                    luminosity = value;
                    OnPropertyChanged("Luminosity");
                    SetNewColor();
                }
            }
            get
            {
                return luminosity;
            }
        }

        public Color Color
        {
            set
            {
                if (color != value)
                {
                    color = value;
                    OnPropertyChanged("Color");

                    Hue = value.Hue;
                    Saturation = value.Saturation;
                    Luminosity = value.Luminosity;
                }
            }
            get
            {
                return color;
            }
        }

        void SetNewColor()
        {
            Color = Color.FromHsla(Hue, Saturation, Luminosity);
        }

        protected virtual void OnPropertyChanged(string propertyName)
        {
            PropertyChanged?.Invoke(this, new PropertyChangedEventArgs(propertyName));
        }
    }
}
```

Altera para o `Hue`, `Saturation`, e `Luminosity` propriedades causa o `Color` propriedade a ser alterada e as alterações `Color` faz com que as outras três propriedades alterar. Isso pode parecer um loop infinito, exceto que a classe não chamar o `PropertyChanged` evento, a menos que a propriedade realmente foram alterados. Isso coloca um fim para o loop de comentários caso contrário incontrolável.

Contém o seguinte arquivo XAML um `BoxView` cujo `Color` propriedade está vinculada a `Color` propriedade ViewModel e três `Slider` e três `Label` modos de exibição associada ao `Hue`, `Saturation`e `Luminosity` propriedades:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:XamlSamples;assembly=XamlSamples"
             x:Class="XamlSamples.HslColorScrollPage"
             Title="HSL Color Scroll Page">
    <ContentPage.BindingContext>
        <local:HslViewModel Color="Aqua" />
    </ContentPage.BindingContext>

    <StackLayout Padding="10, 0">
        <BoxView Color="{Binding Color}"
                 VerticalOptions="FillAndExpand" />

        <Label Text="{Binding Hue, StringFormat='Hue = {0:F2}'}"
               HorizontalOptions="Center" />

        <Slider Value="{Binding Hue, Mode=TwoWay}" />

        <Label Text="{Binding Saturation, StringFormat='Saturation = {0:F2}'}"
               HorizontalOptions="Center" />

        <Slider Value="{Binding Saturation, Mode=TwoWay}" />

        <Label Text="{Binding Luminosity, StringFormat='Luminosity = {0:F2}'}"
               HorizontalOptions="Center" />

        <Slider Value="{Binding Luminosity, Mode=TwoWay}" />
    </StackLayout>
</ContentPage>
```

A associação em cada `Label` é o padrão `OneWay`. Ele só precisa exibir o valor. Mas a associação em cada `Slider` é `TwoWay`. Isso permite que o `Slider` seja inicializada em ViewModel. Observe que o `Color` está definida como `Blue` quando o ViewModel é instanciado. Uma alteração no, mas o `Slider` também precisa definir um novo valor para a propriedade no ViewModel, que, em seguida, calcula uma nova cor.

[ ![](data-bindings-to-mvvm-images/hslcolorscroll.png "MVVM usando associações de dados bidirecional")](data-bindings-to-mvvm-images/hslcolorscroll-large.png "MVVM usando associações de dados bidirecional")

## <a name="commanding-with-viewmodels"></a>Ordenar com ViewModels

Em muitos casos, o padrão MVVM é restrito a manipulação de itens de dados: objetos de dados no ViewModel paralelo de objetos de interface do usuário no modo de exibição.

No entanto, às vezes, a exibição precisa conter botões que acionam várias ações no ViewModel. Mas o ViewModel não deve conter `Clicked` manipuladores para os botões porque que seria empate ViewModel de um paradigma de interface de usuário específica.

Para permitir que ViewModels seja mais independente de objetos de interface de usuário específico, mas ainda permitir a ser chamado dentro de ViewModel métodos um *comando* interface existe. Os seguintes elementos no xamarin. Forms dá suporte para essa interface de comando:

-  `Button`
-  `MenuItem`
-  `ToolbarItem`
-  `SearchBar`
-  `TextCell` (e, portanto, também `ImageCell`)
-  `ListView`
-  `TapGestureRecognizer`

Com exceção do `SearchBar` e `ListView` elemento, esses elementos definem duas propriedades:

-  `Command` do tipo  `System.Windows.Input.ICommand`
-  `CommandParameter` do tipo  `Object`

O `SearchBar` define `SearchCommand` e `SearchCommandParameter` propriedades, enquanto o `ListView` define uma `RefreshCommand` propriedade do tipo `ICommand`.

O `ICommand` interface define dois métodos e um evento:

-  `void Execute(object arg)`
-  `bool CanExecute(object arg)`
-  `event EventHandler CanExecuteChanged`

O ViewModel pode definir as propriedades do tipo `ICommand`. Em seguida, você pode vincular essas propriedades para o `Command` propriedade de cada `Button` ou outro elemento, ou talvez uma exibição personalizada que implementa essa interface. Você pode definir opcionalmente o `CommandParameter` propriedade para identificar individuais `Button` objetos (ou outros elementos) que estão associados a essa propriedade ViewModel. Internamente, o `Button` chamadas a `Execute` método sempre que o usuário toca o `Button`, passando para o `Execute` método seu `CommandParameter`.

O `CanExecute` método e `CanExecuteChanged` são usados para casos onde um `Button` toque pode ser válido no momento, caso em que o `Button` deve desabilitar a mesmo. O `Button` chamadas `CanExecute` quando o `Command` propriedade é definida pela primeira vez e sempre que o `CanExecuteChanged` evento é acionado. Se `CanExecute` retorna `false`, o `Button` desabilita a mesmo e não gera `Execute` chamadas.

Para obter ajuda sobre como adicionar comandos à sua ViewModels, xamarin. Forms define duas classes que implementam `ICommand`: `Command` e `Command<T>` onde `T` é o tipo dos argumentos para `Execute` e `CanExecute`. Essas duas classes definem vários construtores mais um `ChangeCanExecute` método ViewModel pode chamar para forçar o `Command` objeto acionar o `CanExecuteChanged` evento.

Aqui está um ViewModel para um teclado simple que é destinado para inserir números de telefone. Observe que o `Execute` e `CanExecute` método são definidas como diretamente de funções de lambda no construtor:

```csharp
using System;
using System.ComponentModel;
using System.Windows.Input;
using Xamarin.Forms;

namespace XamlSamples
{
    class KeypadViewModel : INotifyPropertyChanged
    {
        string inputString = "";
        string displayText = "";
        char[] specialChars = { '*', '#' };

        public event PropertyChangedEventHandler PropertyChanged;

        // Constructor
        public KeypadViewModel()
        {
            AddCharCommand = new Command<string>((key) =>
                {
                    // Add the key to the input string.
                    InputString += key;
                });

            DeleteCharCommand = new Command(() =>
                {
                    // Strip a character from the input string.
                    InputString = InputString.Substring(0, InputString.Length - 1);
                },
                () =>
                {
                    // Return true if there's something to delete.
                    return InputString.Length > 0;
                });
        }

        // Public properties
        public string InputString
        {
            protected set
            {
                if (inputString != value)
                {
                    inputString = value;
                    OnPropertyChanged("InputString");
                    DisplayText = FormatText(inputString);

                    // Perhaps the delete button must be enabled/disabled.
                    ((Command)DeleteCharCommand).ChangeCanExecute();
                }
            }

            get { return inputString; }
        }

        public string DisplayText
        {
            protected set
            {
                if (displayText != value)
                {
                    displayText = value;
                    OnPropertyChanged("DisplayText");
                }
            }
            get { return displayText; }
        }

        // ICommand implementations
        public ICommand AddCharCommand { protected set; get; }

        public ICommand DeleteCharCommand { protected set; get; }

        string FormatText(string str)
        {
            bool hasNonNumbers = str.IndexOfAny(specialChars) != -1;
            string formatted = str;

            if (hasNonNumbers || str.Length < 4 || str.Length > 10)
            {
            }
            else if (str.Length < 8)
            {
                formatted = String.Format("{0}-{1}",
                                          str.Substring(0, 3),
                                          str.Substring(3));
            }
            else
            {
                formatted = String.Format("({0}) {1}-{2}",
                                          str.Substring(0, 3),
                                          str.Substring(3, 3),
                                          str.Substring(6));
            }
            return formatted;
        }

        protected void OnPropertyChanged(string propertyName)
        {
            PropertyChanged?.Invoke(this, new PropertyChangedEventArgs(propertyName));
        }
    }
}
```

Esse ViewModel supõe que o `AddCharCommand` propriedade está vinculada a `Command` propriedade de vários botões (ou qualquer outra coisa que tenha uma interface de comando), cada um deles é identificada pelo `CommandParameter`. Esses botões adicionam caracteres para um `InputString` propriedade, que é formatada como um número de telefone para o `DisplayText` propriedade.

Há também uma segunda propriedade do tipo `ICommand` chamado `DeleteCharCommand`. Isso é associado a um botão Voltar espaçamento, mas o botão deve ser desabilitado se não houver nenhum caractere para excluir.

O teclado a seguir não é sofisticado como visualmente como é possível. Em vez disso, a marcação foi reduzida ao mínimo para demonstrar mais claramente o uso da interface de comando:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:XamlSamples;assembly=XamlSamples"
             x:Class="XamlSamples.KeypadPage"
             Title="Keypad Page">

    <Grid HorizontalOptions="Center"
          VerticalOptions="Center">
        <Grid.BindingContext>
            <local:KeypadViewModel />
        </Grid.BindingContext>

        <Grid.RowDefinitions>
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
        </Grid.RowDefinitions>

        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="80" />
            <ColumnDefinition Width="80" />
            <ColumnDefinition Width="80" />
        </Grid.ColumnDefinitions>

        <!-- Internal Grid for top row of items -->
        <Grid Grid.Row="0" Grid.Column="0" Grid.ColumnSpan="3">
            <Grid.ColumnDefinitions>
                <ColumnDefinition Width="*" />
                <ColumnDefinition Width="Auto" />
            </Grid.ColumnDefinitions>

            <Frame Grid.Column="0"
                   OutlineColor="Accent">
                <Label Text="{Binding DisplayText}" />
            </Frame>

            <Button Text="&#x21E6;"
                    Command="{Binding DeleteCharCommand}"
                    Grid.Column="1"
                    BorderWidth="0" />
        </Grid>

        <Button Text="1"
                Command="{Binding AddCharCommand}"
                CommandParameter="1"
                Grid.Row="1" Grid.Column="0" />

        <Button Text="2"
                Command="{Binding AddCharCommand}"
                CommandParameter="2"
                Grid.Row="1" Grid.Column="1" />

        <Button Text="3"
                Command="{Binding AddCharCommand}"
                CommandParameter="3"
                Grid.Row="1" Grid.Column="2" />

        <Button Text="4"
                Command="{Binding AddCharCommand}"
                CommandParameter="4"
                Grid.Row="2" Grid.Column="0" />

        <Button Text="5"
                Command="{Binding AddCharCommand}"
                CommandParameter="5"
                Grid.Row="2" Grid.Column="1" />

        <Button Text="6"
                Command="{Binding AddCharCommand}"
                CommandParameter="6"
                Grid.Row="2" Grid.Column="2" />

        <Button Text="7"
                Command="{Binding AddCharCommand}"
                CommandParameter="7"
                Grid.Row="3" Grid.Column="0" />

        <Button Text="8"
                Command="{Binding AddCharCommand}"
                CommandParameter="8"
                Grid.Row="3" Grid.Column="1" />

        <Button Text="9"
                Command="{Binding AddCharCommand}"
                CommandParameter="9"
                Grid.Row="3" Grid.Column="2" />

        <Button Text="*"
                Command="{Binding AddCharCommand}"
                CommandParameter="*"
                Grid.Row="4" Grid.Column="0" />

        <Button Text="0"
                Command="{Binding AddCharCommand}"
                CommandParameter="0"
                Grid.Row="4" Grid.Column="1" />

        <Button Text="#"
                Command="{Binding AddCharCommand}"
                CommandParameter="#"
                Grid.Row="4" Grid.Column="2" />
    </Grid>
</ContentPage>
```

O `Command` propriedade do primeiro `Button` que aparece na marcação está associada ao `DeleteCharCommand`; o rest estão associados ao `AddCharCommand` com um `CommandParameter` que é o mesmo como o caractere que aparece no `Button` face. Aqui está o programa em ação:

[ ![](data-bindings-to-mvvm-images/keypad.png "Calculadora usando comandos e MVVM")](data-bindings-to-mvvm-images/keypad-large.png "calculadora usando comandos e MVVM")

### <a name="invoking-asynchronous-methods"></a>Chamando métodos assíncronos

Comandos também podem chamar métodos assíncronos. Isso é feito usando o `async` e `await` palavras-chave ao especificar o `Execute` método:

```csharp
DownloadCommand = new Command (async () => await DownloadAsync ());
```

Isso indica que o `DownloadAsync` método é um `Task` e deve ser colocada em espera:

```csharp
async Task DownloadAsync ()
{
    await Task.Run (() => Download ());
}

void Download ()
{
    ...
}
```

## <a name="implementing-a-navigation-menu"></a>Implementando um Menu de navegação

O [XamlSamples](https://developer.xamarin.com/samples/xamarin-forms/XamlSamples/) programa que contém o código-fonte da série de artigos usa um ViewModel para sua página inicial. Esse ViewModel é uma definição de uma classe curta com três propriedades chamadas `Type`, `Title`, e `Description` que contém o tipo de cada uma das páginas de exemplo, um título e uma breve descrição. Além disso, o ViewModel define uma propriedade estática chamada `All` que é uma coleção de todas as páginas do programa:

```csharp
public class PageDataViewModel
{
    public PageDataViewModel(Type type, string title, string description)
    {
        Type = type;
        Title = title;
        Description = description;
    }

    public Type Type { private set; get; }

    public string Title { private set; get; }

    public string Description { private set; get; }

    static PageDataViewModel()
    {
        All = new List<PageDataViewModel>
        {
            // Part 1. Getting Started with XAML
            new PageDataViewModel(typeof(HelloXamlPage), "Hello, XAML",
                                  "Display a Label with many properties set"),

            new PageDataViewModel(typeof(XamlPlusCodePage), "XAML + Code",
                                  "Interact with a Slider and Button"),

            // Part 2. Essential XAML Syntax
            new PageDataViewModel(typeof(GridDemoPage), "Grid Demo",
                                  "Explore XAML syntax with the Grid"),

            new PageDataViewModel(typeof(AbsoluteDemoPage), "Absolute Demo",
                                  "Explore XAML syntax with AbsoluteLayout"),

            // Part 3. XAML Markup Extensions
            new PageDataViewModel(typeof(SharedResourcesPage), "Shared Resources",
                                  "Using resource dictionaries to share resources"),

            new PageDataViewModel(typeof(StaticConstantsPage), "Static Constants",
                                  "Using the x:Static markup extensions"),

            new PageDataViewModel(typeof(RelativeLayoutPage), "Relative Layout",
                                  "Explore XAML markup extensions"),

            // Part 4. Data Binding Basics
            new PageDataViewModel(typeof(SliderBindingsPage), "Slider Bindings",
                                  "Bind properties of two views on the page"),

            new PageDataViewModel(typeof(SliderTransformsPage), "Slider Transforms",
                                  "Use Sliders with reverse bindings"),

            new PageDataViewModel(typeof(ListViewDemoPage), "ListView Demo",
                                  "Use a ListView with data bindings"),

            // Part 5. From Data Bindings to MVVM
            new PageDataViewModel(typeof(OneShotDateTimePage), "One-Shot DateTime",
                                  "Obtain the current DateTime and display it"),

            new PageDataViewModel(typeof(ClockPage), "Clock",
                                  "Dynamically display the current time"),

            new PageDataViewModel(typeof(HslColorScrollPage), "HSL Color Scroll",
                                  "Use a view model to select HSL colors"),

            new PageDataViewModel(typeof(KeypadPage), "Keypad",
                                  "Use a view model for numeric keypad logic")
        };
    }

    public static IList<PageDataViewModel> All { private set; get; } 
}
```

O arquivo XAML para `MainPage` define uma `ListBox` cujo `ItemsSource` está definida como que `All` propriedade e que contém um `TextCell` para exibir o `Title` e `Description` propriedades de cada página:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:XamlSamples"
             x:Class="XamlSamples.MainPage"
             Padding="5, 0"
             Title="XAML Samples">

    <ListView ItemsSource="{x:Static local:PageDataViewModel.All}"
              ItemSelected="OnListViewItemSelected">
        <ListView.ItemTemplate>
            <DataTemplate>
                <TextCell Text="{Binding Title}"
                          Detail="{Binding Description}" />
            </DataTemplate>
        </ListView.ItemTemplate>
    </ListView>
</ContentPage>
```

As páginas são mostradas em uma lista de rolagem:

[ ![](data-bindings-to-mvvm-images/mainpage.png "Lista de rolagem de páginas")](data-bindings-to-mvvm-images/mainpage-large.png "rolável lista de páginas")

O manipulador no arquivo code-behind é disparado quando o usuário seleciona um item. Define o manipulador a `SelectedItem` propriedade o `ListBox` para `null` e, em seguida, cria a página selecionada e navega para ele:

```csharp
private async void OnListViewItemSelected(object sender, SelectedItemChangedEventArgs args)
{
    (sender as ListView).SelectedItem = null;

    if (args.SelectedItem != null)
    {
        PageDataViewModel pageData = args.SelectedItem as PageDataViewModel;
        Page page = (Page)Activator.CreateInstance(pageData.Type);
        await Navigation.PushAsync(page);
    }
}
```

## <a name="summary"></a>Resumo

XAML é uma ferramenta poderosa para a definição de interfaces de usuário em aplicativos xamarin. Forms, especialmente quando a associação de dados e MVVM são usados. O resultado é uma representação limpa, elegante e potencialmente editável de uma interface de usuário com todo o suporte de plano de fundo no código.


## <a name="related-links"></a>Links relacionados

- [XamlSamples](https://developer.xamarin.com/samples/xamarin-forms/XamlSamples/)
- [Parte 1. Guia de Introdução com XAML](~/xamarin-forms/xaml/xaml-basics/get-started-with-xaml.md)
- [Parte 2. Sintaxe XAML essenciais](~/xamarin-forms/xaml/xaml-basics/essential-xaml-syntax.md)
- [Parte 3. Extensões de marcação XAML](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [Parte 4. Noções básicas de associação de dados](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md)
