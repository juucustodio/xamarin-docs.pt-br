---
title: Modo de associação
description: Controle o fluxo de informações entre origem e destino
ms.prod: xamarin
ms.assetid: D087C389-2E9E-47B9-A341-5B14AC732C45
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/05/2018
ms.openlocfilehash: fdcba9b680bd548371883788af9e4eda755d91df
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="binding-mode"></a>Modo de associação

No [artigo anterior](basic-bindings.md), o **alternativa código associação** e **alternativa XAML associação** páginas em Destaque um `Label` com seus `Scale` propriedade associado para o `Value` propriedade de um `Slider`. Porque o `Slider` valor inicial é 0, isso é causado o `Scale` propriedade o `Label` seja definida como 0, em vez de 1 e o `Label` desapareceu.

No [ **DataBindingDemos** ](https://developer.xamarin.com/samples/xamarin-forms/DataBindingDemos/) exemplo, o **associação inversa** página é semelhante aos programas no artigo anterior, exceto que a associação de dados é definida no `Slider` em vez de no `Label`:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="DataBindingDemos.ReverseBindingPage"
             Title="Reverse Binding">
    <StackLayout Padding="10, 0">

        <Label x:Name="label" 
               Text="TEXT"
               FontSize="80"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand" />

        <Slider x:Name="slider"
                VerticalOptions="CenterAndExpand"
                Value="{Binding Source={x:Reference label},
                                Path=Opacity}" />
    </StackLayout>
</ContentPage>
```

A princípio, isso pode parecer com versões anteriores: agora o `Label` é a origem de associação de dados e o `Slider` é o destino. As referências de associação a `Opacity` propriedade o `Label`, que tem um valor padrão de 1.

Como esperado, o `Slider` é inicializada com o valor 1 de inicial `Opacity` valor `Label`. Isso é mostrado na captura de tela de iOS à esquerda:

[![Inverter associação](binding-mode-images/reversebinding-small.png "Inverter associação")](binding-mode-images/reversebinding-large.png#lightbox "Inverter associação")

Mas você pode estar surpreso que o `Slider` continua a funcionar, como demonstram as capturas de tela do Android e UWP. Parece que sugerem que a associação de dados funciona melhor quando o `Slider` é o destino da associação em vez de `Label` porque a inicialização funciona como poderia ser esperado.

A diferença entre o **associação inversa** exemplo e os exemplos anteriores envolve o *modo de associação*.

## <a name="the-default-binding-mode"></a>O modo de associação padrão

O modo de associação é especificado com um membro do [ `BindingMode` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindingMode/) enumeração: 

- [`Default`](https://developer.xamarin.com/api/field/Xamarin.Forms.BindingMode.Default/) 
- [`TwoWay`](https://developer.xamarin.com/api/field/Xamarin.Forms.BindingMode.TwoWay/) &ndash; os dados inseridos ambas as direções entre origem e destino
- [`OneWay`](https://developer.xamarin.com/api/field/Xamarin.Forms.BindingMode.OneWay/) &ndash; dados vão de origem para destino
- [`OneWayToSource`](https://developer.xamarin.com/api/field/Xamarin.Forms.BindingMode.OneWayToSource/) &ndash; dados vai do destino para origem

Todas as propriedades vinculáveis tem um padrão de associação de modo que é definido quando a propriedade associável é criada, e que está disponível a [ `DefaultBindingMode` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BindableProperty.DefaultBindingMode/) propriedade o `BindableProperty` objeto. Esse modo de associação padrão indica o modo em vigor quando essa propriedade é um destino de associação de dados.

O modo de associação padrão para a maioria das propriedades como `Rotation`, `Scale`, e `Opacity` é `OneWay`. Quando essas propriedades são destinos de vinculação de dados, a propriedade de destino é definida da origem.

No entanto, o modo de associação padrão para o `Value` propriedade `Slider` é `TwoWay`. Isso significa que, quando o `Value` propriedade é um destino de associação de dados, e em seguida, o destino é definido da origem (como de costume) mas a origem também está definida de destino. Este é o que permite que o `Slider` a ser definido de inicial `Opacity` valor.

Essa associação bidirecional pode parecer criar um loop infinito, mas que não acontece. Propriedades vinculáveis não sinalizar uma alteração de propriedade, a menos que a propriedade for alterada. Isso evita um loop infinito.

### <a name="two-way-bindings"></a>Associações bidirecionais

Propriedades vinculáveis mais tem um modo de associação padrão de `OneWay` , mas as propriedades a seguir tem um modo de associação padrão do `TwoWay`:

- `Date` propriedade de `DatePicker`
- `Text` propriedade de `Editor`, `Entry`, `SearchBar`, e `EntryCell`
- `IsRefreshing` propriedade de `ListView`
- `SelectedItem` propriedade de `MultiPage`
- `SelectedIndex` e `SelectedItem` propriedades de `Picker`
- `Value` propriedade de `Slider` e `Stepper`
- `IsToggled` propriedade de `Switch` 
- `On` propriedade de `SwitchCell`
- `Time` propriedade de `TimePicker`

Essas propriedades específicas são definidas como `TwoWay` por um bom motivo: 

Quando associações de dados são usadas com a arquitetura do aplicativo Model-View-ViewModel (MVVM), a classe ViewModel é a origem de associação de dados e o modo de exibição, que consiste em modos de exibição, como `Slider`, são destinos de vinculação de dados. Associações de MVVM são semelhantes a **Inverter associação** exemplo mais do que as associações nos exemplos anteriores. É muito provável que você deseja que cada exibição na página a ser inicializado com o valor da propriedade correspondente no ViewModel, mas as alterações no modo de exibição também devem afetar a propriedade ViewModel.

As propriedades com modos de associação padrão de `TwoWay` são as propriedades mais prováveis de ser usado em cenários MVVM.

### <a name="one-way-to-source-bindings"></a>Associações de um-vias para a fonte

As propriedades vinculáveis somente leitura têm um modo de associação padrão de `OneWayToSource`. Há apenas uma propriedade de leitura/gravação associável que possui um modo de associação padrão do `OneWayToSource`:

- `SelectedItem` propriedade de `ListView`

A lógica que é uma associação no `SelectedItem` propriedade deve resultar na definição de fonte de associação. Um exemplo mais adiante neste artigo substitui esse comportamento.

## <a name="viewmodels-and-property-change-notifications"></a>ViewModels e notificações de alteração de propriedade

O **seletor de cores simples** página demonstra o uso de um ViewModel simple. Associações de dados permitem que o usuário selecione uma cor usando três `Slider` elementos para o matiz, saturação e luminosidade.

O ViewModel é a origem de associação de dados. O ViewModel *não* definir propriedades vinculáveis, mas ele implementa um mecanismo de notificação que permite que a infraestrutura de associação a ser notificado quando o valor de uma propriedade é alterado. Esse mecanismo de notificação é o [ `INotifyPropertyChanged` ](https://developer.xamarin.com/api/type/System.ComponentModel.INotifyPropertyChanged/) interface, que define uma propriedade única chamada [ `PropertyChanged` ](https://developer.xamarin.com/api/event/System.ComponentModel.INotifyPropertyChanged.PropertyChanged/). Uma classe que implementa essa interface em geral dispara o evento quando uma de suas propriedades públicas altera o valor. O evento não precisa ser disparado se a propriedade nunca é alterado. (O `INotifyPropertyChanged` interface também é implementada pelo `BindableObject` e um `PropertyChanged` evento é acionado sempre que uma propriedade ligável altera o valor.)

O `HslColorViewModel` classe define cinco propriedades: O `Hue`, `Saturation`, `Luminosity`, e `Color` propriedades estão correlacionadas. Quando qualquer um dos três componentes de cores altera o valor, o `Color` propriedade é recalculada, e `PropertyChanged` os eventos são disparados para todas as quatro propriedades:

```csharp
public class HslColorViewModel : INotifyPropertyChanged
{
    Color color;
    string name; 

    public event PropertyChangedEventHandler PropertyChanged;

    public double Hue
    {
        set
        {
            if (color.Hue != value)
            {
                Color = Color.FromHsla(value, color.Saturation, color.Luminosity);
            }
        }
        get 
        {
            return color.Hue;
        }
    }

    public double Saturation
    {
        set
        {
            if (color.Saturation != value)
            {
                Color = Color.FromHsla(color.Hue, value, color.Luminosity);
            }
        }
        get
        {
            return color.Saturation;
        }
    }

    public double Luminosity
    {
        set
        {
            if (color.Luminosity != value)
            {
                Color = Color.FromHsla(color.Hue, color.Saturation, value);
            }
        }
        get
        {
            return color.Luminosity;
        }
    }

    public Color Color
    {
        set
        {
            if (color != value)
            {
                color = value;
                PropertyChanged?.Invoke(this, new PropertyChangedEventArgs("Hue"));
                PropertyChanged?.Invoke(this, new PropertyChangedEventArgs("Saturation"));
                PropertyChanged?.Invoke(this, new PropertyChangedEventArgs("Luminosity"));
                PropertyChanged?.Invoke(this, new PropertyChangedEventArgs("Color"));

                Name = NamedColor.GetNearestColorName(color);
            }
        }
        get
        {
            return color;
        }
    }

    public string Name
    {
        private set
        {
            if (name != value)
            {
                name = value;
                PropertyChanged?.Invoke(this, new PropertyChangedEventArgs("Name"));
            }
        }
        get
        {
            return name;
        }
    }
}
```

Quando o `Color` alterações de propriedade, estático `GetNearestColorName` método o `NamedColor` classe (também está incluído no **DataBindingDemos** solução) obtém o nome de cor mais próximo e define o `Name` propriedade. Isso `Name` propriedade tem uma particular `set` acessador, portanto, não pode ser definida de fora da classe.

Quando um ViewModel é definido como uma origem de associação, a infraestrutura de associação anexa um manipulador para o `PropertyChanged` evento. Dessa forma, a associação pode ser notificada sobre as alterações nas propriedades e, em seguida, pode definir as propriedades de destino de valores alterados.

O **seletor de cores simples** arquivo XAML instancia o `HslColorViewModel` no dicionário de recursos e a inicializa a página de `Color` propriedade. O `BindingContext` propriedade o `Grid` é definido como um `StaticResource` extension para fazer referência a esse recurso de associação:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:DataBindingDemos"
             x:Class="DataBindingDemos.SimpleColorSelectorPage">

    <ContentPage.Resources>
        <ResourceDictionary>
            <local:HslColorViewModel x:Key="viewModel" 
                                     Color="MediumTurquoise" />

            <Style TargetType="Slider">
                <Setter Property="VerticalOptions" Value="CenterAndExpand" />
            </Style>
        </ResourceDictionary>
    </ContentPage.Resources>
        
    <Grid BindingContext="{StaticResource viewModel}">
        <Grid.RowDefinitions>
            <RowDefinition Height="*" />
            <RowDefinition Height="*" />
        </Grid.RowDefinitions>

        <BoxView Color="{Binding Color}"
                 Grid.Row="0" />

        <StackLayout Grid.Row="1"
                     Margin="10, 0">

            <Label Text="{Binding Name}"
                   HorizontalTextAlignment="Center" />

            <Slider Value="{Binding Hue}" />
    
            <Slider Value="{Binding Saturation}" />

            <Slider Value="{Binding Luminosity}" />
        </StackLayout>
    </Grid>
</ContentPage>
```

O `BoxView`, `Label`e três `Slider` exibições herdam o contexto de associação do `Grid`. Essas exibições são todos os destinos de associação que fazem referência a propriedades da fonte no ViewModel. Para o `Color` propriedade do `BoxView`e o `Text` propriedade o `Label`, as associações de dados são `OneWay`: configurar as propriedades no modo de exibição das propriedades no ViewModel.

O `Value` propriedade o `Slider`, no entanto, é `TwoWay`. Isso permite que cada `Slider` a ser definido no ViewModel e também para o ViewModel a ser definido de cada `Slider`. 

Quando o programa é executado pela primeira vez, o `BoxView`, `Label`e três `Slider` elementos estão definidos no ViewModel com base em inicial `Color` propriedade definida quando o ViewModel foi instanciado. Isso é mostrado na captura de tela de iOS à esquerda:

[![Seletor de cores simples](binding-mode-images/simplecolorselector-small.png "seletor de cores simples")](binding-mode-images/simplecolorselector-large.png#lightbox "seletor de cores simples")

Como você manipula os controles deslizantes, o `BoxView` e `Label` são atualizadas, conforme ilustrado pelas capturas de tela do Android e UWP.

Criando o ViewModel no dicionário de recursos é uma abordagem comum. Também é possível instanciar o ViewModel dentro de marcas de elemento de propriedade para o `BindingContext` propriedade. No **seletor de cores simples** XAML de arquivos, tente remover o `HslColorViewModel` dicionário de recursos e defina-a como o `BindingContext` propriedade do `Grid` assim:

```xaml
<Grid>
    <Grid.BindingContext>
        <local:HslColorViewModel Color="MediumTurquoise" />
    </Grid.BindingContext>
        
    ···

</Grid>
```

O contexto de associação pode ser definido em uma variedade de maneiras. Às vezes, o arquivo code-behind instancia o ViewModel e define como o `BindingContext` propriedade da página. Estas são as abordagens válidas.

## <a name="overriding-the-binding-mode"></a>Substituindo o modo de associação

Se o modo de associação padrão na propriedade de destino não é adequado para a associação de dados específico, é possível substituí-la definindo o [ `Mode` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BindingBase.Mode/) propriedade `Binding` (ou o [ `Mode` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Xaml.BindingExtension.Mode/) propriedade do `Binding` extensão de marcação) para um dos membros a `BindingMode` enumeração.

No entanto, definindo o `Mode` propriedade `TwoWay` nem sempre funciona como esperado. Por exemplo, tente modificar o **alternativa XAML associação** arquivo XAML para incluir `TwoWay` na definição de associação:

```xaml
<Label Text="TEXT"
       FontSize="40"
       HorizontalOptions="Center"
       VerticalOptions="CenterAndExpand"
       Scale="{Binding Source={x:Reference slider},
                       Path=Value,
                       Mode=TwoWay}" />
```

Ele pode ser esperado que o `Slider` deve ser inicializado para o valor inicial do `Scale` propriedade, que é 1, mas que não acontece. Quando um `TwoWay` associação é inicializada, o destino é definido da origem primeiro, o que significa que o `Scale` está definida como o `Slider` padrão de valor de 0. Quando o `TwoWay` associação está definida no `Slider`, o `Slider` é inicialmente definida da origem.

Você pode definir o modo de associação para `OneWayToSource` no **alternativa XAML associação** exemplo:

```xaml
<Label Text="TEXT"
       FontSize="40"
       HorizontalOptions="Center"
       VerticalOptions="CenterAndExpand"
       Scale="{Binding Source={x:Reference slider},
                       Path=Value,
                       Mode=OneWayToSource}" />
```

Agora o `Slider` é inicializado como 1 (o valor padrão de `Scale`) mas manipular o `Slider` não afeta o `Scale` propriedade, portanto não é muito útil.

Um aplicativo muito útil de substituir o modo de associação padrão com `TwoWay` envolve o `SelectedItem` propriedade `ListView`. O modo de associação padrão é `OneWayToSource`. Quando uma associação de dados é definida no `SelectedItem` propriedade para fazer referência a uma propriedade de origem em um ViewModel, então essa propriedade de origem é definida do `ListView` seleção. No entanto, em algumas circunstâncias, você também poderá a `ListView` a ser inicializado no ViewModel.

O **configurações de exemplo** página demonstra essa técnica. Esta página representa uma implementação simples de configurações do aplicativo, que frequentemente são definidos em um ViewModel, como esta `SampleSettingsViewModel` arquivo:

```csharp
public class SampleSettingsViewModel : INotifyPropertyChanged
{
    string name;
    DateTime birthDate;
    bool codesInCSharp;
    double numberOfCopies;
    NamedColor backgroundNamedColor;

    public event PropertyChangedEventHandler PropertyChanged;

    public SampleSettingsViewModel(IDictionary<string, object> dictionary)
    {
        Name = GetDictionaryEntry<string>(dictionary, "Name");
        BirthDate = GetDictionaryEntry(dictionary, "BirthDate", new DateTime(1980, 1, 1));
        CodesInCSharp = GetDictionaryEntry<bool>(dictionary, "CodesInCSharp");
        NumberOfCopies = GetDictionaryEntry(dictionary, "NumberOfCopies", 1.0);
        BackgroundNamedColor = NamedColor.Find(GetDictionaryEntry(dictionary, "BackgroundNamedColor", "White"));
    }

    public string Name
    {
        set { SetProperty(ref name, value); }
        get { return name; }
    }

    public DateTime BirthDate
    {
        set { SetProperty(ref birthDate, value); }
        get { return birthDate; }
    }

    public bool CodesInCSharp
    {
        set { SetProperty(ref codesInCSharp, value); }
        get { return codesInCSharp; }
    }

    public double NumberOfCopies
    {
        set { SetProperty(ref numberOfCopies, value); }
        get { return numberOfCopies; }
    }

    public NamedColor BackgroundNamedColor
    {
        set
        {
            if (SetProperty(ref backgroundNamedColor, value))
            {
                OnPropertyChanged("BackgroundColor");
            }
        }
        get { return backgroundNamedColor; }
    }

    public Color BackgroundColor
    {
        get { return BackgroundNamedColor?.Color ?? Color.White; }
    }

    public void SaveState(IDictionary<string, object> dictionary)
    {
        dictionary["Name"] = Name;
        dictionary["BirthDate"] = BirthDate;
        dictionary["CodesInCSharp"] = CodesInCSharp;
        dictionary["NumberOfCopies"] = NumberOfCopies;
        dictionary["BackgroundNamedColor"] = BackgroundNamedColor.Name;
    }

    T GetDictionaryEntry<T>(IDictionary<string, object> dictionary, string key, T defaultValue = default(T))
    {
        return dictionary.ContainsKey(key) ? (T)dictionary[key] : defaultValue;
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

Cada configuração de aplicativo é uma propriedade que é salvo no dicionário de propriedades xamarin. Forms em um método chamado `SaveState` e carregados a partir desse dicionário no construtor. Na parte inferior da classe são dois métodos que ajudam a agilizar ViewModels e torná-los menos propenso a erros. O `OnPropertyChanged` método na parte inferior tem um parâmetro opcional que é definido como a propriedade chamada. Isso evita erros de ortografia ao especificar o nome da propriedade como uma cadeia de caracteres. 

O `SetProperty` método na classe faz ainda mais: ele compara o valor que está sendo definido para a propriedade com o valor armazenado como um campo e apenas chama `OnPropertyChanged` quando os dois valores não são iguais. 

O `SampleSettingsViewModel` classe define duas propriedades para a cor de plano de fundo: O `BackgroundNamedColor` é de propriedade do tipo `NamedColor`, que uma classe também está incluída no **DataBindingDemos** solução. O `BackgroundColor` é de propriedade do tipo `Color`e é obtida a `Color` propriedade do `NamedColor` objeto.

O `NamedColor` classe usa reflexão do .NET para enumerar todos os campos públicos estáticos no xamarin. Forms `Color` estrutura e armazená-los com seus nomes em uma coleção acessível de estático `All` propriedade:

```csharp
public class NamedColor : IEquatable<NamedColor>, IComparable<NamedColor>
{
    // Instance members
    private NamedColor()
    {
    }

    public string Name { private set; get; }

    public string FriendlyName { private set; get; }

    public Color Color { private set; get; }

    public string RgbDisplay { private set; get; }

    public bool Equals(NamedColor other)
    {
        return Name.Equals(other.Name);
    }

    public int CompareTo(NamedColor other)
    {
        return Name.CompareTo(other.Name);
    }

    // Static members
    static NamedColor()
    {
        List<NamedColor> all = new List<NamedColor>();
        StringBuilder stringBuilder = new StringBuilder();

        // Loop through the public static fields of the Color structure.
        foreach (FieldInfo fieldInfo in typeof(Color).GetRuntimeFields())
        {
            if (fieldInfo.IsPublic &&
                fieldInfo.IsStatic &&
                fieldInfo.FieldType == typeof(Color))
            {
                // Convert the name to a friendly name.
                string name = fieldInfo.Name;
                stringBuilder.Clear();
                int index = 0;

                foreach (char ch in name)
                {
                    if (index != 0 && Char.IsUpper(ch))
                    {
                        stringBuilder.Append(' ');
                    }
                    stringBuilder.Append(ch);
                    index++;
                }

                // Instantiate a NamedColor object.
                Color color = (Color)fieldInfo.GetValue(null);

                NamedColor namedColor = new NamedColor
                {
                    Name = name,
                    FriendlyName = stringBuilder.ToString(),
                    Color = color,
                    RgbDisplay = String.Format("{0:X2}-{1:X2}-{2:X2}",
                                                (int)(255 * color.R),
                                                (int)(255 * color.G),
                                                (int)(255 * color.B))
                };

                // Add it to the collection.
                all.Add(namedColor);
            }
        }
        all.TrimExcess();
        all.Sort();
        All = all;
    }

    public static IList<NamedColor> All { private set; get; }

    public static NamedColor Find(string name)
    {
        return ((List<NamedColor>)All).Find(nc => nc.Name == name);
    }

    public static string GetNearestColorName(Color color)
    {
        double shortestDistance = 1000;
        NamedColor closestColor = null;

        foreach (NamedColor namedColor in NamedColor.All)
        {
            double distance = Math.Sqrt(Math.Pow(color.R - namedColor.Color.R, 2) +
                                        Math.Pow(color.G - namedColor.Color.G, 2) +
                                        Math.Pow(color.B - namedColor.Color.B, 2));

            if (distance < shortestDistance)
            {
                shortestDistance = distance;
                closestColor = namedColor;
            }
        }
        return closestColor.Name;
    }
}
```

O `App` classe no **DataBindingDemos** projeto define uma propriedade chamada `Settings` do tipo `SampleSettingsViewModel`. Essa propriedade é inicializada quando o `App` classe é instanciada e o `SaveState` método é chamado quando o `OnSleep` método é chamado:

```csharp
public partial class App : Application
{
    public App()
    {
        InitializeComponent();

        Settings = new SampleSettingsViewModel(Current.Properties);

        MainPage = new NavigationPage(new MainPage());
    }

    public SampleSettingsViewModel Settings { private set; get; }

    protected override void OnStart()
    {
        // Handle when your app starts
    }

    protected override void OnSleep()
    {
        // Handle when your app sleeps
        Settings.SaveState(Current.Properties);
    }

    protected override void OnResume()
    {
        // Handle when your app resumes
    }
}
```

Para obter mais informações sobre os métodos de ciclo de vida do aplicativo, consulte o artigo [ **ciclo de vida do aplicativo**](~/xamarin-forms/app-fundamentals/app-lifecycle.md).

Quase tudo é tratado no **SampleSettingsPage.xaml** arquivo. O `BindingContext` da página é definida usando um `Binding` extensão de marcação: A origem de associação é estático `Application.Current` propriedade, que é a instância do `App` classe no projeto e o `Path` é definido como o `Settings` propriedade, que é o `SampleSettingsViewModel` objeto:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:DataBindingDemos"
             x:Class="DataBindingDemos.SampleSettingsPage"
             Title="Sample Settings"
             BindingContext="{Binding Source={x:Static Application.Current},
                                      Path=Settings}">

    <StackLayout BackgroundColor="{Binding BackgroundColor}"
                 Padding="10"
                 Spacing="10">

        <StackLayout Orientation="Horizontal">
            <Label Text="Name: "
                   VerticalOptions="Center" />

            <Entry Text="{Binding Name}"
                   Placeholder="your name"
                   HorizontalOptions="FillAndExpand"
                   VerticalOptions="Center" />
        </StackLayout>

        <StackLayout Orientation="Horizontal">
            <Label Text="Birth Date: "
                   VerticalOptions="Center" />

            <DatePicker Date="{Binding BirthDate}"
                        HorizontalOptions="FillAndExpand"
                        VerticalOptions="Center" />
        </StackLayout>

        <StackLayout Orientation="Horizontal">
            <Label Text="Do you code in C#? "
                   VerticalOptions="Center" />

            <Switch IsToggled="{Binding CodesInCSharp}"
                    VerticalOptions="Center" />
        </StackLayout>

        <StackLayout Orientation="Horizontal">
            <Label Text="Number of Copies: "
                   VerticalOptions="Center" />

            <Stepper Value="{Binding NumberOfCopies}"
                     VerticalOptions="Center" />

            <Label Text="{Binding NumberOfCopies}"
                   VerticalOptions="Center" />
        </StackLayout>

        <Label Text="Background Color:" />

        <ListView x:Name="colorListView"
                  ItemsSource="{x:Static local:NamedColor.All}"
                  SelectedItem="{Binding BackgroundNamedColor, Mode=TwoWay}"
                  VerticalOptions="FillAndExpand"
                  RowHeight="40">
            <ListView.ItemTemplate>
                <DataTemplate>
                    <ViewCell>
                        <StackLayout Orientation="Horizontal">
                            <BoxView Color="{Binding Color}"
                                     HeightRequest="32"
                                     WidthRequest="32"
                                     VerticalOptions="Center" />

                            <Label Text="{Binding FriendlyName}"
                                   FontSize="24"
                                   VerticalOptions="Center" />
                        </StackLayout>                        
                    </ViewCell>
                </DataTemplate>
            </ListView.ItemTemplate>
        </ListView>
    </StackLayout>
</ContentPage>
```

Todos os filhos da página herdam o contexto de associação. A maioria das outras associações nesta página são propriedades no `SampleSettingsViewModel`. O `BackgroundColor` propriedade é usada para definir o `BackgroundColor` propriedade o `StackLayout`e o `Entry`, `DatePicker`, `Switch`, e `Stepper` propriedades são vinculadas a outras propriedades no ViewModel.

O `ItemsSource` propriedade o `ListView` é definido como estático `NamedColor.All` propriedade. Isso preenche o `ListView` com todos os `NamedColor` instâncias. Para cada item de `ListView`, o contexto de associação para o item é definido como um `NamedColor` objeto. O `BoxView` e `Label` no `ViewCell` são associados às propriedades de `NamedColor`.

O `SelectedItem` propriedade o `ListView` é do tipo `NamedColor`e está associado ao `BackgroundNamedColor` propriedade do `SampleSettingsViewModel`:

```xaml
SelectedItem="{Binding BackgroundNamedColor, Mode=TwoWay}"
```

O modo de associação padrão para `SelectedItem` é `OneWayToSource`, que define a propriedade ViewModel do item selecionado. O `TwoWay` modo permite que o `SelectedItem` seja inicializada em ViewModel. 

No entanto, quando o `SelectedItem` é definido dessa maneira, o `ListView` não rola automaticamente para mostrar o item selecionado. Um pouco de código no arquivo code-behind é necessário:

```csharp
public partial class SampleSettingsPage : ContentPage
{
    public SampleSettingsPage()
    {
        InitializeComponent();

        if (colorListView.SelectedItem != null)
        {
            colorListView.ScrollTo(colorListView.SelectedItem, 
                                   ScrollToPosition.MakeVisible, 
                                   false);
        }
    }
}
``` 

A captura de tela de iOS à esquerda mostra o programa quando ele for executado pela primeira vez. O construtor `SampleSettingsViewModel` inicializa o plano de fundo de cor para branco e o que é o que está selecionado no `ListView`:

[![Exemplos de configurações](binding-mode-images/samplesettings-small.png "exemplos de configurações")](binding-mode-images/samplesettings-large.png#lightbox "exemplos de configurações")

As outras duas capturas de tela mostram as configurações alteradas. Ao testar essa página, lembre-se de colocar o programa no estado de suspensão ou encerrá-lo no dispositivo ou emulador que está sendo executado. Encerrar o programa do depurador do Visual Studio não causará a `OnSleep` substituir no `App` classe a ser chamado.

No próximo artigo, você verá como especificar [ **cadeia de caracteres de formatação** ](string-formatting.md) das associações de dados que são definidas no `Text` propriedade `Label`.


## <a name="related-links"></a>Links relacionados

- [Demonstrações de associação de dados (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/DataBindingDemos/)
- [Capítulo de associação de dados do catálogo de xamarin. Forms](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter16.md)
