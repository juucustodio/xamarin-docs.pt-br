---
title: Parte 5. De associações de dados a MVVM
description: O padrão MVVM impõe uma separação entre três camadas de software — a interface do usuário XAML, chamada de exibição; os dados subjacentes, chamados de modelo; e um intermediário entre a exibição e o modelo, chamado ViewModel.
ms.prod: xamarin
ms.custom: video
ms.assetid: 48B37D44-4FB1-41B2-9A5E-6D383B041F81
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/25/2017
ms.openlocfilehash: 80386780d52f9a28d421d2a83981085956d06ea5
ms.sourcegitcommit: efbc69acf4ea484d8815311b058114379c9db8a2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73842944"
---
# <a name="part-5-from-data-bindings-to-mvvm"></a>Parte 5. De associações de dados a MVVM

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xamlsamples)

_O padrão de arquitetura MVVM (Model-View-ViewModel) foi inventado com XAML em mente. O padrão impõe uma separação entre três camadas de software — a interface do usuário XAML, chamada de exibição; os dados subjacentes, chamados de modelo; e um intermediário entre a exibição e o modelo, chamado ViewModel. A exibição e o ViewModel geralmente são conectados por meio de associações de dados definidas no arquivo XAML. O BindingContext para a exibição geralmente é uma instância do ViewModel._

## <a name="a-simple-viewmodel"></a>Um ViewModel simples

Como uma introdução aos ViewModels, vamos primeiro examinar um programa sem um.
Anteriormente, você viu como definir uma nova declaração de namespace de XML para permitir que um arquivo XAML referencie classes em outros assemblies. Aqui está um programa que define uma declaração de namespace XML para o namespace `System`:

```csharp
xmlns:sys="clr-namespace:System;assembly=mscorlib"
```

O programa pode usar `x:Static` para obter a data e a hora atuais da propriedade estática `DateTime.Now` e definir que `DateTime` valor para a `BindingContext` em uma `StackLayout`:

```xaml
<StackLayout BindingContext="{x:Static sys:DateTime.Now}" …>
```

`BindingContext` é uma propriedade especial: quando você define o `BindingContext` em um elemento, ele é herdado por todos os filhos desse elemento. Isso significa que todos os filhos do `StackLayout` têm esse mesmo `BindingContext`e podem conter associações simples a propriedades desse objeto.

No programa **DateTime One-Shot** , dois dos filhos contêm associações a propriedades desse `DateTime` valor, mas dois outros filhos contêm associações que parecem estar faltando um caminho de associação. Isso significa que o próprio valor de `DateTime` é usado para o `StringFormat`:

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

O problema é que a data e hora são definidas uma vez quando a página é compilada pela primeira vez e nunca são alteradas:

[![](data-bindings-to-mvvm-images/oneshotdatetime.png "View Displaying Date and Time")](data-bindings-to-mvvm-images/oneshotdatetime-large.png#lightbox "View Displaying Date and Time")

Um arquivo XAML pode exibir um relógio que sempre mostra a hora atual, mas precisa de algum código para ajudá-lo. Ao pensar em termos de MVVM, o modelo e o ViewModel são classes escritas inteiramente no código. A exibição é geralmente um arquivo XAML que faz referência a propriedades definidas no ViewModel por meio de associações de dados.

Um modelo adequado é que desconhecem do ViewModel, e um ViewModel adequado é que desconhecem da exibição. No entanto, geralmente um programador cauda dos tipos de dados expostos pelo ViewModel para os tipos de dados associados a interfaces de usuário específicas. Por exemplo, se um modelo acessa um banco de dados que contém cadeias de caracteres ASCII de 8 bits, o ViewModel precisaria fazer a conversão entre essas cadeias para as cadeias Unicode para acomodar o uso exclusivo do Unicode na interface do usuário.

Em exemplos simples de MVVM (como os mostrados aqui), geralmente não há nenhum modelo, e o padrão envolve apenas uma exibição e ViewModel vinculados a associações de dados.

Aqui está um ViewModel para um relógio com apenas uma única propriedade chamada `DateTime`, que atualiza `DateTime` Propriedade a cada segundo:

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

Os ViewModels geralmente implementam a interface `INotifyPropertyChanged`, o que significa que a classe dispara um evento `PropertyChanged` sempre que uma de suas propriedades é alterada. O mecanismo de vinculação de dados no Xamarin. Forms anexa um manipulador a esse `PropertyChanged` evento para que possa ser notificado quando uma propriedade for alterada e manter o destino atualizado com o novo valor.

Um relógio baseado nesse ViewModel pode ser tão simples quanto:

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

Observe como a `ClockViewModel` é definida como a `BindingContext` do `Label` usando marcas de elemento de propriedade. Como alternativa, você pode instanciar o `ClockViewModel` em uma coleção de `Resources` e defini-lo como o `BindingContext` por meio de uma extensão de marcação `StaticResource`. Ou, o arquivo code-behind pode instanciar o ViewModel.

A extensão de marcação `Binding` na propriedade `Text` do `Label` formata a propriedade `DateTime`. Aqui está a tela:

[![](data-bindings-to-mvvm-images/clock.png "View Displaying Date and Time via ViewModel")](data-bindings-to-mvvm-images/clock-large.png#lightbox "View Displaying Date and Time via ViewModel")

Também é possível acessar propriedades individuais da propriedade `DateTime` do ViewModel, separando as propriedades com períodos:

```xaml
<Label Text="{Binding DateTime.Second, StringFormat='{0}'}" … >
```

## <a name="interactive-mvvm"></a>MVVM interativo

O MVVM é geralmente usado com associações de dados bidirecionais para uma exibição interativa com base em um modelo de dados subjacente.

Aqui está uma classe chamada `HslViewModel` que converte um valor de `Color` em valores `Hue`, `Saturation`e `Luminosity` e vice-versa:

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

As alterações nas propriedades `Hue`, `Saturation`e `Luminosity` fazem com que a propriedade `Color` seja alterada e as alterações em `Color` fazem com que as outras três propriedades sejam alteradas. Isso pode parecer um loop infinito, exceto que a classe não invoca o evento `PropertyChanged`, a menos que a propriedade tenha sido alterada. Isso coloca uma extremidade ao loop de comentários, caso contrário, não controlável.

O arquivo XAML a seguir contém um `BoxView` cuja propriedade `Color` está associada à propriedade `Color` do ViewModel e três `Slider` e três exibições `Label` associadas às propriedades `Hue`, `Saturation`e `Luminosity`:

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

A associação em cada `Label` é a `OneWay`padrão. Ele só precisa exibir o valor. Mas a associação em cada `Slider` é `TwoWay`. Isso permite que o `Slider` seja inicializado a partir do ViewModel. Observe que a propriedade `Color` é definida como `Aqua` quando o ViewModel é instanciado. Mas uma alteração na `Slider` também precisa definir um novo valor para a propriedade no ViewModel, que então calcula uma nova cor.

[![](data-bindings-to-mvvm-images/hslcolorscroll.png "MVVM using Two-Way Data Bindings")](data-bindings-to-mvvm-images/hslcolorscroll-large.png#lightbox "MVVM using Two-Way Data Bindings")

## <a name="commanding-with-viewmodels"></a>Comando com ViewModels

Em muitos casos, o padrão MVVM é restrito à manipulação de itens de dados: objetos de interface do usuário na exibição de objetos de dados paralelos no ViewModel.

No entanto, às vezes, a exibição precisa conter botões que disparam várias ações no ViewModel. Mas o ViewModel não deve conter `Clicked` manipuladores para os botões porque isso vincularia o ViewModel a um paradigma de interface de usuário específico.

Para permitir que ViewModels sejam mais independentes de objetos de interface do usuário específicos, mas que ainda permitem que os métodos sejam chamados dentro do ViewModel, existe uma interface de *comando* . Essa interface de comando é suportada pelos seguintes elementos no Xamarin. Forms:

- `Button`
- `MenuItem`
- `ToolbarItem`
- `SearchBar`
- `TextCell` (e, portanto, também `ImageCell`)
- `ListView`
- `TapGestureRecognizer`

Com exceção do elemento `SearchBar` e `ListView`, esses elementos definem duas propriedades:

- `Command` do tipo `System.Windows.Input.ICommand`
- `CommandParameter` do tipo `Object`

O `SearchBar` define `SearchCommand` e `SearchCommandParameter` Propriedades, enquanto o `ListView` define uma propriedade `RefreshCommand` do tipo `ICommand`.

A interface `ICommand` define dois métodos e um evento:

- `void Execute(object arg)`
- `bool CanExecute(object arg)`
- `event EventHandler CanExecuteChanged`

O ViewModel pode definir propriedades do tipo `ICommand`. Em seguida, você pode associar essas propriedades à propriedade `Command` de cada `Button` ou outro elemento, ou talvez uma exibição personalizada que implemente essa interface. Opcionalmente, você pode definir a propriedade `CommandParameter` para identificar objetos de `Button` individuais (ou outros elementos) associados a essa propriedade ViewModel. Internamente, o `Button` chama o método `Execute` sempre que o usuário toca no `Button`, passando para o método `Execute` seu `CommandParameter`.

O método de `CanExecute` e `CanExecuteChanged` evento são usados para casos em que um toque `Button` pode ser inválido no momento; nesse caso, o `Button` deve ser desabilitado. O `Button` chama `CanExecute` quando a propriedade `Command` é definida pela primeira vez e sempre que o evento `CanExecuteChanged` é acionado. Se `CanExecute` retornar `false`, o `Button` se desabilitará e não gerará chamadas `Execute`.

Para obter ajuda com a adição de comandos aos seus ViewModels, o Xamarin. Forms define duas classes que implementam `ICommand`: `Command` e `Command<T>` onde `T` é o tipo dos argumentos para `Execute` e `CanExecute`. Essas duas classes definem vários construtores mais um método `ChangeCanExecute` que o ViewModel pode chamar para forçar o objeto de `Command` a acionar o evento `CanExecuteChanged`.

Aqui está um ViewModel para um teclado simples que se destina a inserir números de telefone. Observe que o método `Execute` e `CanExecute` são definidos como funções lambda diretamente no construtor:

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

Esse ViewModel pressupõe que a propriedade `AddCharCommand` está associada à propriedade `Command` de vários botões (ou qualquer outra coisa que tenha uma interface de comando), cada um dos quais é identificado pelo `CommandParameter`. Esses botões adicionam caracteres a uma propriedade `InputString`, que é formatada como um número de telefone para a propriedade `DisplayText`.

Também há uma segunda Propriedade do tipo `ICommand` chamada `DeleteCharCommand`. Isso é associado a um botão de espaçamento de fundo, mas o botão deve ser desabilitado se não houver caracteres a serem excluídos.

O seguinte teclado não é tão visualmente sofisticado quanto poderia ser. Em vez disso, a marcação foi reduzida para um mínimo para demonstrar mais claramente o uso da interface de comando:

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

A propriedade `Command` da primeira `Button` que aparece nessa marcação está associada à `DeleteCharCommand`; o restante são associados à `AddCharCommand` com um `CommandParameter` igual ao caractere que aparece na face `Button`. Este é o programa em ação:

[![](data-bindings-to-mvvm-images/keypad.png "Calculator using MVVM and Commands")](data-bindings-to-mvvm-images/keypad-large.png#lightbox "Calculator using MVVM and Commands")

### <a name="invoking-asynchronous-methods"></a>Invocando métodos assíncronos

Os comandos também podem invocar métodos assíncronos. Isso é feito usando as palavras-chave `async` e `await` ao especificar o método `Execute`:

```csharp
DownloadCommand = new Command (async () => await DownloadAsync ());
```

Isso indica que o método `DownloadAsync` é um `Task` e deve ser aguardado:

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

## <a name="implementing-a-navigation-menu"></a>Implementando um menu de navegação

O programa [XamlSamples](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xamlsamples) que contém todo o código-fonte nesta série de artigos usa um ViewModel para sua Home Page. Este ViewModel é uma definição de uma classe curta com três propriedades chamadas `Type`, `Title`e `Description` que contêm o tipo de cada uma das páginas de exemplo, um título e uma breve descrição. Além disso, o ViewModel define uma propriedade estática chamada `All` que é uma coleção de todas as páginas no programa:

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

O arquivo XAML para `MainPage` define um `ListBox` cuja propriedade `ItemsSource` está definida como essa propriedade `All` e que contém um `TextCell` para exibir as propriedades `Title` e `Description` de cada página:

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

As páginas são mostradas em uma lista rolável:

[![](data-bindings-to-mvvm-images/mainpage.png "Scrollable list of pages")](data-bindings-to-mvvm-images/mainpage-large.png#lightbox "Scrollable list of pages")

O manipulador no arquivo code-behind é disparado quando o usuário seleciona um item. O manipulador define a propriedade `SelectedItem` do `ListBox` de volta para `null` e, em seguida, instancia a página selecionada e navega para ela:

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

## <a name="video"></a>Vídeo

> [!VIDEO https://youtube.com/embed/DYRLcqG2BAY]

**O xamarin evoluiu 2016: o MVVM tornou-se simples com Xamarin. Forms e Prism**

## <a name="summary"></a>Resumo

O XAML é uma ferramenta poderosa para definir interfaces de usuário em aplicativos Xamarin. Forms, especialmente quando a vinculação de dados e o MVVM são usados. O resultado é uma representação limpa, elegante e potencialmente passível de ferramenta de uma interface do usuário com todo o suporte em segundo plano no código.

## <a name="related-links"></a>Links relacionados

- [XamlSamples](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xamlsamples)
- [Parte 1. Introdução com XAML](~/xamarin-forms/xaml/xaml-basics/get-started-with-xaml.md)
- [Parte 2. Sintaxe XAML essencial](~/xamarin-forms/xaml/xaml-basics/essential-xaml-syntax.md)
- [Parte 3. Extensões de marcação XAML](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [Parte 4. Noções básicas de ligação de dados](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md)

## <a name="related-videos"></a>Vídeos relacionados

> [!Video https://channel9.msdn.com/Series/Xamarin-101/XamarinForms-MVVM-with-XAML-6-of-11/player]

> [!Video https://channel9.msdn.com/Series/Xamarin-101/XamarinForms-Navigation-with-XAML-7-of-11/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
