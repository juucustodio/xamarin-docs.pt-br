---
title: Modo de associação do xamarin. Forms
description: Este artigo explica como controlar o fluxo de informações entre a origem e destino usando um modo de associação, que é especificado com um membro da enumeração BindingMode. Cada propriedade associável tem um modo de associação padrão, que indica o modo em vigor quando essa propriedade é um destino de associação de dados.
ms.prod: xamarin
ms.assetid: D087C389-2E9E-47B9-A341-5B14AC732C45
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 05/01/2018
ms.openlocfilehash: a6eaf08d17f70c43f451361e27555a09c39f26a9
ms.sourcegitcommit: 3e980fbf92c69c3dd737554e8c6d5b94cf69ee3a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/10/2018
ms.locfileid: "37935661"
---
# <a name="xamarinforms-binding-mode"></a>Modo de associação do xamarin. Forms

No [artigo anterior](basic-bindings.md), o **alternativa de código de associação** e **alternativa de associação de XAML** páginas em destaque uma `Label` com seu `Scale` propriedade associado para o `Value` propriedade de um `Slider`. Porque o `Slider` valor inicial é 0, isso causou a `Scale` propriedade da `Label` seja definida como 0 em vez de 1 e o `Label` desapareceu.

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

A princípio, isso pode parecer com versões anteriores: agora o `Label` é a origem de associação de dados e o `Slider` é o destino. As referências de associação a `Opacity` propriedade do `Label`, que tem um valor padrão de 1.

Como você pode esperar, o `Slider` é inicializada com o valor 1 da inicial `Opacity` valor `Label`. Isso é mostrado na captura de tela iOS à esquerda:

[![Reverter a associação](binding-mode-images/reversebinding-small.png "Inverter associação")](binding-mode-images/reversebinding-large.png#lightbox "Inverter associação")

Mas você pode estar surpresos de que o `Slider` continuará a funcionar, como demonstram as capturas de tela do Android e UWP. Isso parece sugerir que a associação de dados funciona melhor quando o `Slider` é o destino da associação em vez de `Label` porque a inicialização funciona como o que poderíamos esperar.

A diferença entre o **associação inversa** envolve a amostra e os exemplos anteriores a *modo de associação*.

## <a name="the-default-binding-mode"></a>O modo de associação padrão

O modo de associação é especificado com um membro do [ `BindingMode` ](xref:Xamarin.Forms.BindingMode) enumeração:

- [`Default`](xref:Xamarin.Forms.BindingMode.Default)
- [`TwoWay`](xref:Xamarin.Forms.BindingMode.TwoWay) &ndash; as duas maneiras dos dados são inseridos entre origem e destino
- [`OneWay`](xref:Xamarin.Forms.BindingMode.OneWay) &ndash; dados vão da fonte para o destino
- [`OneWayToSource`](xref:Xamarin.Forms.BindingMode.OneWayToSource) &ndash; dados vão do destino para origem
- [`OneTime`](xref:Xamarin.Forms.BindingMode.OneWayToSource) &ndash; dados vão da fonte para o destino, mas somente quando o `BindingContext` alterações (novas com o xamarin. Forms 3.0)

Cada propriedade associável tem um padrão de modo de associação que é definido quando a propriedade associável é criada, e que está disponível na [ `DefaultBindingMode` ](xref:Xamarin.Forms.BindableProperty.DefaultBindingMode) propriedade o `BindableProperty` objeto. Esse modo de associação padrão indica o modo em vigor quando essa propriedade é um destino de associação de dados.

O modo de associação padrão para a maioria das propriedades, como `Rotation`, `Scale`, e `Opacity` é `OneWay`. Quando essas propriedades são destinos de vinculação de dados, a propriedade de destino é definida da origem.

No entanto, o modo de associação padrão para o `Value` propriedade de `Slider` é `TwoWay`. Isso significa que, quando o `Value` propriedade é um destino de associação de dados, e em seguida, o destino é definido da origem (como de costume), mas a fonte também está definida de destino do. Isso é o que permite que o `Slider` seja definida de inicial `Opacity` valor.

Essa associação bidirecional pode parecer criar um loop infinito, mas isso não acontecer. Propriedades vinculáveis não sinalizar uma alteração de propriedade, a menos que a propriedade é alterada. Isso impede que um loop infinito.

### <a name="two-way-bindings"></a>Vinculações bidirecionais

Propriedades vinculáveis mais tem um modo de associação padrão do `OneWay` , mas as propriedades a seguir tem um modo de associação padrão do `TwoWay`:

- `Date` propriedade de `DatePicker`
- `Text` propriedade de `Editor`, `Entry`, `SearchBar`, e `EntryCell`
- `IsRefreshing` propriedade de `ListView`
- `SelectedItem` propriedade de `MultiPage`
- `SelectedIndex` e `SelectedItem` propriedades de `Picker`
- `Value` propriedade de `Slider` e `Stepper`
- `IsToggled` propriedade de `Switch`
- `On` propriedade de `SwitchCell`
- `Time` propriedade de `TimePicker`

Essas propriedades específicas são definidas como `TwoWay` por um motivo muito bom:

Quando as associações de dados são usadas com a arquitetura do aplicativo Model-View-ViewModel (MVVM), a classe ViewModel é a origem da associação de dados e o modo de exibição, que consiste em modos de exibição, como `Slider`, são destinos de vinculação de dados. Associações do MVVM se parecer com o **Inverter associação** amostra mais do que as associações nos exemplos anteriores. É muito provável que você deseja que cada modo de exibição na página a ser inicializado com o valor da propriedade correspondente no ViewModel, mas as alterações no modo de exibição também devem afetar a propriedade de ViewModel.

As propriedades com modos de ligação padrão de `TwoWay` são as propriedades mais prováveis de serem utilizados em cenários do MVVM.

### <a name="one-way-to-source-bindings"></a>Associações de única-Way-to-Source

Propriedades vinculáveis somente leitura têm um modo de associação padrão do `OneWayToSource`. Há apenas uma propriedade de leitura/gravação associável que tem um modo de associação padrão do `OneWayToSource`:

- `SelectedItem` propriedade de `ListView`

A lógica que é uma associação a `SelectedItem` propriedade deve resultar em definir a origem da associação. Um exemplo mais adiante neste artigo substitui esse comportamento.

### <a name="one-time-bindings"></a>Associações de uso únicas

Várias propriedades têm um modo de associação padrão do `OneTime`. Elas são:

- `IsTextPredictionEnabled` propriedade de `Entry`
- `Text`, `BackgroundColor`, e `Style` propriedades de `Span`.

Direcionar propriedades com um modo de associação de `OneTime` são atualizados somente quando o contexto de associação é alterado. Para associações nessas propriedades de destino, isso simplifica a infra-estrutura de ligação porque não é necessário monitorar as alterações nas propriedades do código-fonte.

## <a name="viewmodels-and-property-change-notifications"></a>ViewModels e notificações de alteração de propriedade

O **seletor de cor simples** página demonstra o uso de um ViewModel simple. Associações de dados permitem que o usuário selecione uma cor usando três `Slider` elementos para o matiz, saturação e luminosidade.

O ViewModel é a origem da associação de dados. Faz o ViewModel *não* definir propriedades vinculáveis, mas ele implementa um mecanismo de notificação que permite que a infraestrutura de associação ser notificado quando o valor de uma propriedade é alterado. Esse mecanismo de notificação é o [ `INotifyPropertyChanged` ](xref:System.ComponentModel.INotifyPropertyChanged) interface, que define uma propriedade única chamada [ `PropertyChanged` ](xref:System.ComponentModel.INotifyPropertyChanged.PropertyChanged). Uma classe que implementa essa interface em geral dispara o evento quando uma das suas propriedades públicas altera o valor. O evento não precisa ser disparado se a propriedade nunca é alterado. (O `INotifyPropertyChanged` interface também é implementada por `BindableObject` e um `PropertyChanged` evento é acionado sempre que uma propriedade vinculável altera o valor.)

O `HslColorViewModel` classe define cinco propriedades: O `Hue`, `Saturation`, `Luminosity`, e `Color` propriedades estejam correlacionadas. Quando qualquer um dos três componentes de cor altera o valor, o `Color` propriedade é recalculada, e `PropertyChanged` os eventos são disparados para todas as quatro propriedades:

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

Quando o `Color` alterações de propriedade, estático `GetNearestColorName` método na `NamedColor` classe (também incluído no **DataBindingDemos** solução) obtém a cor mais próxima de nomeada e define o `Name` propriedade. Isso `Name` propriedade tem uma privada `set` acessador, portanto, não pode ser definida de fora da classe.

Quando um ViewModel é definido como uma origem da associação, a infraestrutura de associação anexa um manipulador para o `PropertyChanged` eventos. Dessa forma, a associação pode ser notificada de alterações para as propriedades e pode definir as propriedades de destino de valores alterados.

No entanto, quando uma propriedade de destino (ou o `Binding` definição de uma propriedade de destino) tem um `BindingMode` dos `OneTime`, não é necessário para a infraestrutura de associação anexar um manipulador no `PropertyChanged` eventos. A propriedade de destino é atualizada somente quando o `BindingContext` alterações e não quando a propriedade de origem é alterado.

O **seletor de cor simples** arquivo XAML cria uma instância de `HslColorViewModel` no dicionário de recursos da página e o inicializa o `Color` propriedade. O `BindingContext` propriedade do `Grid` é definido como um `StaticResource` extensão para fazer referência a esse recurso de associação:

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

O `BoxView`, `Label`e três `Slider` modos de exibição herdam o contexto de associação do `Grid`. Essas exibições são todos os destinos de associação que fazem referência a propriedades da fonte no ViewModel. Para o `Color` propriedade do `BoxView`e o `Text` propriedade do `Label`, as associações de dados são `OneWay`: configurar as propriedades no modo de exibição das propriedades no ViewModel.

O `Value` propriedade do `Slider`, no entanto, é `TwoWay`. Isso permite que cada `Slider` a ser definido no ViewModel e também para o ViewModel seja definido de cada `Slider`.

Quando o programa é executado pela primeira vez, o `BoxView`, `Label`e três `Slider` elementos são definidos no ViewModel com base em inicial `Color` propriedade definida quando o ViewModel foi instanciado. Isso é mostrado na captura de tela iOS à esquerda:

[![Seletor de cores simples](binding-mode-images/simplecolorselector-small.png "seletor de cores simples")](binding-mode-images/simplecolorselector-large.png#lightbox "seletor de cor simples")

Como você manipula os controles deslizantes, o `BoxView` e `Label` sejam atualizadas de acordo, conforme ilustrado pelas capturas de tela do Android e UWP.

Instanciar o ViewModel no dicionário de recursos é uma abordagem comum. Também é possível instanciar o ViewModel dentro de marcas de elemento de propriedade para o `BindingContext` propriedade. No **seletor de cor simples** XAML de arquivos, tente remover o `HslColorViewModel` do dicionário de recursos e defini-lo como o `BindingContext` propriedade do `Grid` semelhante a esta:

```xaml
<Grid>
    <Grid.BindingContext>
        <local:HslColorViewModel Color="MediumTurquoise" />
    </Grid.BindingContext>

    ···

</Grid>
```

O contexto de associação pode ser definido em uma variedade de formas. Às vezes, o arquivo code-behind instancia o ViewModel e o define como o `BindingContext` propriedade da página. Esses são todos válidas abordagens.

## <a name="overriding-the-binding-mode"></a>Substituindo o modo de ligação

Se o modo de associação padrão na propriedade de destino não é adequado para uma associação de dados específico, é possível substituí-la definindo a [ `Mode` ](xref:Xamarin.Forms.BindingBase.Mode) propriedade do `Binding` (ou o [ `Mode` ](xref:Xamarin.Forms.Xaml.BindingExtension.Mode) propriedade do `Binding` extensão de marcação) para um dos membros a `BindingMode` enumeração.

No entanto, definir a `Mode` propriedade para `TwoWay` nem sempre funciona como esperado. Por exemplo, tente modificar as **associação de XAML alternativa** arquivo XAML para incluir `TwoWay` na definição de associação:

```xaml
<Label Text="TEXT"
       FontSize="40"
       HorizontalOptions="Center"
       VerticalOptions="CenterAndExpand"
       Scale="{Binding Source={x:Reference slider},
                       Path=Value,
                       Mode=TwoWay}" />
```

Ele pode ser esperado que o `Slider` seria inicializado como o valor inicial do `Scale` propriedade, que é 1, mas isso não acontecer. Quando um `TwoWay` associação é inicializada, o destino é definido da origem pela primeira vez, o que significa que o `Scale` estiver definida como o `Slider` padrão o valor de 0. Quando o `TwoWay` associação é definida na `Slider`, o `Slider` inicialmente é definida da origem.

Você pode definir o modo de ligação para `OneWayToSource` no **associação de XAML alternativa** exemplo:

```xaml
<Label Text="TEXT"
       FontSize="40"
       HorizontalOptions="Center"
       VerticalOptions="CenterAndExpand"
       Scale="{Binding Source={x:Reference slider},
                       Path=Value,
                       Mode=OneWayToSource}" />
```

Agora o `Slider` é inicializado como 1 (o valor padrão de `Scale`), mas manipulando o `Slider` não afeta o `Scale` propriedade, portanto, isso não é muito útil.

Um aplicativo muito útil de substituir o modo de associação padrão com `TwoWay` envolve o `SelectedItem` propriedade de `ListView`. O modo de associação padrão é `OneWayToSource`. Quando uma associação de dados é definida na `SelectedItem` propriedade para fazer referência a uma propriedade de origem em um ViewModel, então essa propriedade de origem é definida do `ListView` seleção. No entanto, em algumas circunstâncias, você também poderá o `ListView` ser inicializados do ViewModel.

O **exemplos de definições de** página demonstra essa técnica. Esta página representa uma implementação simples de configurações do aplicativo, que frequentemente são definidas em um ViewModel, como este `SampleSettingsViewModel` arquivo:

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

Cada configuração de aplicativo é uma propriedade que é salvo no dicionário de propriedades de xamarin. Forms em um método chamado `SaveState` e carregados a partir desse dicionário no construtor. Na parte inferior da classe são dois métodos que ajudam a simplificar ViewModels e torná-los menos propenso a erros. O `OnPropertyChanged` método na parte inferior tem um parâmetro opcional que é definido como a propriedade chamada. Isso evita erros de ortografia, ao especificar o nome da propriedade como uma cadeia de caracteres.

O `SetProperty` método na classe faz ainda mais: ele compara o valor que está sendo definido para a propriedade com o valor armazenado como um campo e apenas chama `OnPropertyChanged` quando os dois valores não são iguais.

O `SampleSettingsViewModel` classe define duas propriedades para a cor do plano de fundo: O `BackgroundNamedColor` propriedade é do tipo `NamedColor`, que uma classe também está incluída no **DataBindingDemos** solução. O `BackgroundColor` propriedade é do tipo `Color`e é obtida a `Color` propriedade do `NamedColor` objeto.

O `NamedColor` classe usa a reflexão do .NET para enumerar todos os campos públicos estáticos no xamarin. Forms `Color` estrutura e para armazená-los com seus nomes em uma coleção acessível de estático `All` propriedade:

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

O `App` classe de **DataBindingDemos** projeto define uma propriedade chamada `Settings` do tipo `SampleSettingsViewModel`. Esta propriedade é inicializada quando o `App` classe é instanciada e o `SaveState` método é chamado quando o `OnSleep` método é chamado:

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

Quase todo o resto é manipulado na **SampleSettingsPage.xaml** arquivo. O `BindingContext` da página é definido usando um `Binding` extensão de marcação: A origem da associação é estática `Application.Current` propriedade, que é a instância da `App` classe no projeto e o `Path` é definido como o `Settings` propriedade, que é o `SampleSettingsViewModel` objeto:

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

Todos os filhos da página herdam o contexto de associação. A maioria das outras associações nesta página são propriedades em `SampleSettingsViewModel`. O `BackgroundColor` propriedade é usada para definir o `BackgroundColor` propriedade da `StackLayout`e o `Entry`, `DatePicker`, `Switch`, e `Stepper` propriedades são vinculadas a outras propriedades no ViewModel.

O `ItemsSource` propriedade do `ListView` é definido como estático `NamedColor.All` propriedade. Isso preenche o `ListView` com todos os `NamedColor` instâncias. Para cada item de `ListView`, o contexto de associação para o item é definido como um `NamedColor` objeto. O `BoxView` e `Label` na `ViewCell` estão associados às propriedades no `NamedColor`.

O `SelectedItem` propriedade do `ListView` é do tipo `NamedColor`e está vinculado ao `BackgroundNamedColor` propriedade do `SampleSettingsViewModel`:

```xaml
SelectedItem="{Binding BackgroundNamedColor, Mode=TwoWay}"
```

O modo de associação padrão para `SelectedItem` é `OneWayToSource`, que define a propriedade de ViewModel do item selecionado. O `TwoWay` modo permite que o `SelectedItem` ser inicializados do ViewModel.

No entanto, quando o `SelectedItem` é definido desta forma, o `ListView` não rola automaticamente para mostrar o item selecionado. Um pouco de código no arquivo code-behind é necessário:

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

A captura de tela do iOS à esquerda mostra o programa quando ele é executado pela primeira vez. O construtor `SampleSettingsViewModel` inicializa o plano de fundo de cor para branco e que é o que está selecionado no `ListView`:

[![Configurações de exemplo](binding-mode-images/samplesettings-small.png "configurações de exemplo")](binding-mode-images/samplesettings-large.png#lightbox "configurações de exemplo")

As outras duas capturas de tela mostram as configurações alteradas. Ao experimentar com essa página, lembre-se de colocar o programa no modo de suspensão ou encerrá-lo no dispositivo ou emulador que está sendo executado. Encerrando o programa do depurador do Visual Studio não fará com que o `OnSleep` substituir no `App` classe a ser chamado.

No próximo artigo, você verá como especificar [ **cadeia de caracteres de formatação** ](string-formatting.md) de ligações de dados que são definidas na `Text` propriedade do `Label`.


## <a name="related-links"></a>Links relacionados

- [Demonstrações de associação de dados (amostra)](https://developer.xamarin.com/samples/xamarin-forms/DataBindingDemos/)
- [Capítulo de associação de dados do catálogo de xamarin. Forms](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter16.md)
