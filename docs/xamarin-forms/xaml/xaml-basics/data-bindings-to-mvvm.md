---
title: Parte 5. De associações de dados a MVVM
description: O padrão MVVM impõe uma separação entre três camadas de software — a interface do usuário XAML, chamada de exibição; os dados subjacentes, chamados de modelo; e um intermediário entre o modo de exibição e o modelo, chamado o ViewModel.
ms.prod: xamarin
ms.assetid: 48B37D44-4FB1-41B2-9A5E-6D383B041F81
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/25/2017
ms.openlocfilehash: f81a9d232e1702d112e837a80d35403162e3adca
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/16/2019
ms.locfileid: "69529336"
---
# <a name="part-5-from-data-bindings-to-mvvm"></a>Parte 5. De associações de dados a MVVM

[![Baixar exemplo](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xamlsamples)

_O padrão de arquitetura do Model-View-ViewModel (MVVM) foi inventado com XAML em mente. O padrão impõe uma separação entre três camadas de software — a interface do usuário XAML, chamada de exibição; os dados subjacentes, chamados de modelo; e um intermediário entre o modo de exibição e o modelo, chamado o ViewModel. O View e ViewModel são frequentemente conectadas por meio de ligações de dados definidas no arquivo XAML. O BindingContext para o modo de exibição geralmente é uma instância do ViewModel._

## <a name="a-simple-viewmodel"></a>Um ViewModel Simple

Como uma introdução aos ViewModels, vamos primeiro examinar um programa sem uma.
Anteriormente, você viu como definir uma nova declaração de namespace XML para permitir que um arquivo XAML para classes de referência em outros assemblies. Este é um programa que define uma declaração de namespace XML para o `System` namespace:

```csharp
xmlns:sys="clr-namespace:System;assembly=mscorlib"
```

O programa pode usar `x:Static` para obter a data e hora atuais do estático `DateTime.Now` propriedade e defina-as `DateTime` de valor para o `BindingContext` em um `StackLayout`:

```xaml
<StackLayout BindingContext="{x:Static sys:DateTime.Now}" …>
```

`BindingContext`é uma propriedade muito especial: Quando você define o `BindingContext` em um elemento, ele é herdado por todos os filhos desse elemento. Isso significa que todos os filhos do `StackLayout` ter esse mesmo `BindingContext`, e podem conter associações simples às propriedades desse objeto.

No **DateTime One-Shot** programa, dois dos filhos contém associações a propriedades desse `DateTime` valor, mas dois outros filhos contenham associações que parecem estar faltando um caminho de associação. Isso significa que o `DateTime` próprio valor é usado para o `StringFormat`:

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

É claro, o grande problema é que a data e hora são definidos após a criação de quando a página é pela primeira vez e nunca alteração:

[![](data-bindings-to-mvvm-images/oneshotdatetime.png "Tela que mostra a data e hora")](data-bindings-to-mvvm-images/oneshotdatetime-large.png#lightbox "tela que mostra a data e hora")

Um arquivo XAML pode exibir um relógio que sempre mostra a hora atual, mas ele precisa de algum código para ajudar. Ao pensar em termos de MVVM, o Model e ViewModel são classes escritas inteiramente no código. O modo de exibição geralmente é um arquivo XAML que faz referência a propriedades definidas no ViewModel por meio de ligações de dados.

Um modelo apropriado é com ignorância de ViewModel e um ViewModel adequado é com ignorância da exibição. No entanto, com muita frequência um programador personalizam os tipos de dados expostos pelo ViewModel para os tipos de dados associados a interfaces de usuário específico. Por exemplo, se um modelo de acessar um banco de dados que contém cadeias de caracteres do caractere de 8 bits ASCII, o ViewModel precisa converter entre essas cadeias de caracteres para cadeias de caracteres Unicode para acomodar o uso exclusivo do Unicode na interface do usuário.

Nos exemplos simples do MVVM (como aqueles mostrados aqui), geralmente, não há nenhum modelo e o padrão envolve apenas um modo de exibição e ViewModel vinculado com associações de dados.

Aqui está um ViewModel para um relógio com apenas uma única propriedade chamada `DateTime`, mas quais atualizações que `DateTime` propriedade cada segundo:

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

ViewModels geralmente implementa o `INotifyPropertyChanged` interface, o que significa que a classe dispara um `PropertyChanged` evento sempre que uma de suas propriedades é alterada. O mecanismo de ligação de dados no xamarin. Forms anexa um manipulador a este `PropertyChanged` eventos para que ele possa ser notificado quando uma propriedade é alterada e mantenha o destino atualizado com o novo valor.

Um relógio com base nesse ViewModel pode ser tão simple como este:

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

Observe como o `ClockViewModel` é definido como o `BindingContext` da `Label` usando marcas de elemento de propriedade. Como alternativa, você pode instanciar a `ClockViewModel` em um `Resources` coleção e defini-lo como o `BindingContext` por meio de um `StaticResource` extensão de marcação. Ou então, o arquivo code-behind pode instanciar o ViewModel.

O `Binding` extensão de marcação na `Text` propriedade da `Label` formatos o `DateTime` propriedade. Aqui está a exibição:

[![](data-bindings-to-mvvm-images/clock.png "Tela que mostra a data e hora por meio do ViewModel")](data-bindings-to-mvvm-images/clock-large.png#lightbox "tela que mostra a data e hora por meio do ViewModel")

Também é possível acessar as propriedades individuais do `DateTime` propriedade de ViewModel, separando as propriedades com períodos:

```xaml
<Label Text="{Binding DateTime.Second, StringFormat='{0}'}" … >
```

## <a name="interactive-mvvm"></a>MVVM interativo

MVVM é frequentemente usada com ligações de dados bidirecional para uma exibição interativa com base em um modelo de dados subjacente.

Aqui está uma classe chamada `HslViewModel` que converte um `Color` valor na `Hue`, `Saturation`, e `Luminosity` valores e vice-versa:

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

Altera para o `Hue`, `Saturation`, e `Luminosity` propriedades causa o `Color` propriedade a ser alterada e as alterações `Color` faz com que as outras três propriedades alterar. Isso pode parecer um loop infinito, exceto que a classe não chamar o `PropertyChanged` evento, a menos que a propriedade realmente foram alterados. Isso acaba com o loop de comentários caso contrário incontrolável.

Contém o arquivo XAML a seguir um `BoxView` cujos `Color` propriedade está associada a `Color` propriedade de ViewModel e três `Slider` e três `Label` modos de exibição associado ao `Hue`, `Saturation`e `Luminosity` propriedades:

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

A associação em cada `Label` é o padrão `OneWay`. Ele só precisa para exibir o valor. Mas a associação em cada `Slider` é `TwoWay`. Isso permite que o `Slider` ser inicializados do ViewModel. Observe que o `Color` estiver definida como `Aqua` quando o ViewModel é instanciado. Mas uma alteração no `Slider` também precisa definir um novo valor para a propriedade no ViewModel, que, em seguida, calcula uma nova cor.

[![](data-bindings-to-mvvm-images/hslcolorscroll.png "MVVM usando associações de dados bidirecional")](data-bindings-to-mvvm-images/hslcolorscroll-large.png#lightbox "MVVM usando associações de dados bidirecional")

## <a name="commanding-with-viewmodels"></a>Comandos com ViewModels

Em muitos casos, o padrão MVVM é restrito à manipulação de itens de dados: Objetos de interface do usuário no modo de exibição objetos de dados paralelos no ViewModel.

No entanto, às vezes, o modo de exibição precisa conter botões que disparam várias ações no ViewModel. Mas o ViewModel não deve conter `Clicked` manipuladores para os botões porque o que seria vincular o ViewModel para um paradigma de interface de usuário específica.

Para permitir que ViewModels ser mais independente de objetos de interface do usuário específico, mas ainda permitir que métodos sejam chamados dentro do ViewModel, uma *comando* interface existe. Essa interface de comando é suportado pelos seguintes elementos no xamarin. Forms:

- `Button`
- `MenuItem`
- `ToolbarItem`
- `SearchBar`
- `TextCell` (e, portanto, também `ImageCell`)
- `ListView`
- `TapGestureRecognizer`

Com exceção do `SearchBar` e `ListView` elemento, esses elementos definem duas propriedades:

- `Command` do tipo  `System.Windows.Input.ICommand`
- `CommandParameter` do tipo  `Object`

O `SearchBar` define `SearchCommand` e `SearchCommandParameter` propriedades, enquanto o `ListView` define uma `RefreshCommand` propriedade do tipo `ICommand`.

O `ICommand` interface define dois métodos e um evento:

- `void Execute(object arg)`
- `bool CanExecute(object arg)`
- `event EventHandler CanExecuteChanged`

O ViewModel pode definir as propriedades de tipo `ICommand`. Em seguida, você pode associar essas propriedades para o `Command` propriedade de cada `Button` ou outro elemento, ou talvez uma exibição personalizada que implementa essa interface. Opcionalmente, você pode definir as `CommandParameter` propriedade para identificar individuais `Button` objetos (ou outros elementos) que estão associados a essa propriedade de ViewModel. Internamente, o `Button` chamadas a `Execute` método sempre que o usuário toca o `Button`, passando para o `Execute` método seu `CommandParameter`.

O `CanExecute` método e `CanExecuteChanged` são usados para casos em que um `Button` tap pode ser válido no momento, caso em que o `Button` deve desabilitar em si. O `Button` chamadas `CanExecute` quando o `Command` propriedade é definida pela primeira vez e sempre que o `CanExecuteChanged` evento é disparado. Se `CanExecute` retorna `false`, o `Button` desabilita a mesmo e não gera `Execute` chamadas.

Para obter ajuda sobre como adicionar comandos aos seus ViewModels, xamarin. Forms define duas classes que implementam `ICommand`: `Command` e `Command<T>` onde `T` é o tipo dos argumentos para `Execute` e `CanExecute`. Essas duas classes definem vários construtores além de um `ChangeCanExecute` método que o ViewModel pode chamar para forçar o `Command` objeto para acionar o `CanExecuteChanged` eventos.

Aqui está um ViewModel para um teclado simple que é destinado para inserir números de telefone. Observe que o `Execute` e `CanExecute` método são definidos como diretamente de funções lambda no construtor:

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

Esse ViewModel supõe que o `AddCharCommand` propriedade está associada a `Command` propriedade de vários botões (ou qualquer outra coisa que tenha uma interface de comando), cada um deles é identificada pelo `CommandParameter`. Esses botões adicionam caracteres para um `InputString` propriedade, que é formatada como um número de telefone para o `DisplayText` propriedade.

Também há uma segunda propriedade do tipo `ICommand` chamado `DeleteCharCommand`. Isso é associado a um botão back espaçamento, mas o botão deve ser desabilitado se houver caracteres a serem excluídos.

O teclado a seguir não é tão visualmente sofisticado quanto poderia ser. Em vez disso, a marcação foi reduzida ao mínimo para demonstrar mais claramente o uso da interface de comando:

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

O `Command` propriedade do primeiro `Button` que aparece neste marcação está associada ao `DeleteCharCommand`; o restante são associados ao `AddCharCommand` com um `CommandParameter` que é o mesmo como o caractere que aparece no `Button` face. Aqui está o programa em ação:

[![](data-bindings-to-mvvm-images/keypad.png "Calculadora usando comandos e MVVM")](data-bindings-to-mvvm-images/keypad-large.png#lightbox "calculadora usando comandos e MVVM")

### <a name="invoking-asynchronous-methods"></a>Invocar métodos assíncronos

Comandos também podem invocar métodos assíncronos. Isso é feito usando o `async` e `await` palavras-chave ao especificar o `Execute` método:

```csharp
DownloadCommand = new Command (async () => await DownloadAsync ());
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

O [XamlSamples](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xamlsamples) programa que contém o código-fonte desta série de artigos usa um ViewModel para sua página inicial. Esse ViewModel é uma definição de uma classe curta com três propriedades denominadas `Type`, `Title`, e `Description` que contém o tipo de cada uma das páginas de exemplo, um título e uma breve descrição. Além disso, o ViewModel define uma propriedade estática chamada `All` que é uma coleção de todas as páginas no programa:

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

O arquivo XAML para `MainPage` define uma `ListBox` cuja `ItemsSource` estiver definida como que `All` propriedade e que contém um `TextCell` para exibir o `Title` e `Description` propriedades de cada página:

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

[![](data-bindings-to-mvvm-images/mainpage.png "Lista rolável de páginas")](data-bindings-to-mvvm-images/mainpage-large.png#lightbox "lista rolável de páginas")

O manipulador no arquivo code-behind é disparado quando o usuário seleciona um item. O manipulador define o `SelectedItem` propriedade do `ListBox` voltar ao `null` e, em seguida, cria uma instância de página selecionada e navega até ela:

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

**Xamarin Evolve 2016: O MVVM tornou-se simples com o Xamarin. Forms e o Prism**

## <a name="summary"></a>Resumo

XAML é uma ferramenta poderosa para a definição de interfaces do usuário em aplicativos xamarin. Forms, especialmente quando a associação de dados e MVVM são usados. O resultado é uma representação limpa, elegante e potencialmente editável de uma interface do usuário com todo o suporte de plano de fundo no código.


## <a name="related-links"></a>Links relacionados

- [XamlSamples](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xamlsamples)
- [Parte 1. Introdução ao XAML](~/xamarin-forms/xaml/xaml-basics/get-started-with-xaml.md)
- [Parte 2. Sintaxe essencial de XAML](~/xamarin-forms/xaml/xaml-basics/essential-xaml-syntax.md)
- [Parte 3. Extensões de Marcação XAML](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [Parte 4. Conceitos básicos da associação de dados](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md)
