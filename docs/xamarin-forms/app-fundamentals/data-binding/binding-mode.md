---
title: Modo de associação do Xamarin.Forms
description: Este artigo explica como controlar o fluxo de informações entre a origem e o destino usando um modo de associação, que é especificado com um membro da enumeração BindingMode. Cada propriedade associável tem um modo de associação padrão, que indica o modo em vigor quando essa propriedade é um destino da associação de dados.
ms.prod: xamarin
ms.assetid: D087C389-2E9E-47B9-A341-5B14AC732C45
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/01/2018
ms.openlocfilehash: 4583b703d6c6b15105d60a98e7a1064e6a2e9263
ms.sourcegitcommit: bf18425f97b48661ab6b775195eac76b356eeba0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/01/2019
ms.locfileid: "64977783"
---
# <a name="xamarinforms-binding-mode"></a>Modo de associação do Xamarin.Forms

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://developer.xamarin.com/samples/xamarin-forms/DataBindingDemos/)

No [artigo anterior](basic-bindings.md), as páginas **Associação de código alternativa** e **Associação de XAML alternativa** apresentavam um `Label` com sua propriedade `Scale` associada à propriedade `Value` de um `Slider`. Como o valor inicial de `Slider` é 0, isso fez com que a propriedade `Scale` do `Label` fosse definida como 0 em vez de 1 e o `Label` desapareceu.

Na amostra [**DataBindingDemos**](https://developer.xamarin.com/samples/xamarin-forms/DataBindingDemos/), a página **Associação inversa** é semelhante aos programas do artigo anterior, exceto que a associação de dados é definida no `Slider` em vez de no `Label`:

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

A princípio, isso pode parecer invertido: Agora o `Label` é a origem de associação de dados e o `Slider` é o destino. A associação referencia a propriedade `Opacity` do `Label`, que tem um valor padrão igual a 1.

Como você pode esperar, o `Slider` é inicializada com o valor 1 do valor inicial `Opacity` de `Label`. Isso é mostrado na captura de tela do iOS à esquerda:

[![Associação inversa](binding-mode-images/reversebinding-small.png "Associação inversa")](binding-mode-images/reversebinding-large.png#lightbox "Associação inversa")

Mas você pode estar surpreso de que o `Slider` continuará funcionando, como demonstram as capturas de tela do Android e do UWP. Isso parece sugerir que a associação de dados funciona melhor quando o `Slider` é o destino da associação em vez de `Label`, porque a inicialização funciona como poderíamos esperar.

A diferença entre a amostra **Associação inversa** e as amostras anteriores envolve o *modo de associação*.

## <a name="the-default-binding-mode"></a>O modo de associação padrão

O modo de associação é especificado com um membro da enumeração [`BindingMode`](xref:Xamarin.Forms.BindingMode):

- [`Default`](xref:Xamarin.Forms.BindingMode.Default)
- [`TwoWay`](xref:Xamarin.Forms.BindingMode.TwoWay) &ndash; os dados fluem bidirecionalmente entre a origem e o destino
- [`OneWay`](xref:Xamarin.Forms.BindingMode.OneWay) &ndash; os dados vão da origem para o destino
- [`OneWayToSource`](xref:Xamarin.Forms.BindingMode.OneWayToSource) &ndash; os dados fluem do destino para a origem
- [`OneTime`](xref:Xamarin.Forms.BindingMode.OneWayToSource) &ndash; os dados fluem da origem para o destino, mas somente quando o `BindingContext` é alterado (novidade no Xamarin.Forms 3.0)

Cada propriedade associável tem um modo de associação padrão definido quando a propriedade associável é criada, que está disponível na propriedade [`DefaultBindingMode`](xref:Xamarin.Forms.BindableProperty.DefaultBindingMode) do objeto `BindableProperty`. Esse modo de associação padrão indica o modo em vigor quando essa propriedade é um destino da associação de dados.

O modo de associação padrão para a maioria das propriedades, como `Rotation`, `Scale` e `Opacity`, é `OneWay`. Quando essas propriedades são destinos de associação de dados, a propriedade de destino é definida na origem.

No entanto, o modo de associação padrão para a propriedade `Value` de `Slider` é `TwoWay`. Isso significa que, quando a propriedade `Value` é um destino da associação de dados, o destino é definido na origem (como de costume), mas a origem também é definida no destino. Isso é o que permite que o `Slider` seja definido com base no valor inicial `Opacity`.

Essa associação bidirecional pode parecer criar um loop infinito, mas isso não acontece. As propriedades vinculáveis não sinalizam uma alteração de propriedade, a menos que a propriedade seja, de fato, alterada. Isso impede um loop infinito.

### <a name="two-way-bindings"></a>Vinculações bidirecionais

A maioria das propriedades vinculáveis tem um modo de associação padrão igual a `OneWay`, mas as seguintes propriedades têm um modo de associação padrão igual a `TwoWay`:

- Propriedade `Date` de `DatePicker`
- Propriedade `Text` de `Editor`, `Entry`, `SearchBar` e `EntryCell`
- Propriedade `IsRefreshing` de `ListView`
- Propriedade `SelectedItem` de `MultiPage`
- Propriedades `SelectedIndex` e `SelectedItem` de `Picker`
- Propriedade `Value` de `Slider` e `Stepper`
- Propriedade `IsToggled` de `Switch`
- Propriedade `On` de `SwitchCell`
- Propriedade `Time` de `TimePicker`

Essas propriedades específicas são definidas como `TwoWay` por um motivo muito válido:

Quando as associações de dados são usadas com a arquitetura do aplicativo MVVM (Model-View-ViewModel), a classe ViewModel é a origem da associação de dados e a View, que consiste em exibições como `Slider`, são destinos de associação de dados. As associações de MVVM se parecem com a amostra **Associação inversa** mais do que as associações das amostras anteriores. É muito provável que você deseje que cada exibição na página seja inicializada com o valor da propriedade correspondente no ViewModel, mas as alterações na exibição também devem afetar a propriedade de ViewModel.

As propriedades com modos de associação padrão iguais a `TwoWay` são as propriedades mais prováveis de serem utilizadas em cenários de MVVM.

### <a name="one-way-to-source-bindings"></a>Associações unidirecionais para a origem

As propriedades vinculáveis somente leitura têm um modo de associação padrão igual a `OneWayToSource`. Há apenas uma propriedade associável de leitura/gravação que tem um modo de associação padrão igual a `OneWayToSource`:

- Propriedade `SelectedItem` de `ListView`

A lógica é que uma associação na propriedade `SelectedItem` deve resultar na definição da origem da associação. Um exemplo mais adiante neste artigo substitui esse comportamento.

### <a name="one-time-bindings"></a>Associações avulsas

Várias propriedades têm um modo de associação padrão do `OneTime`, incluindo a propriedade `IsTextPredictionEnabled` de `Entry`.

Propriedades de destino com um modo de associação igual a `OneTime` são atualizadas somente quando o contexto de associação é alterado. Para as associações nessas propriedades de destino, isso simplifica a infraestrutura de associação porque não é necessário monitorar as alterações nas propriedades de origem.

## <a name="viewmodels-and-property-change-notifications"></a>ViewModels e notificações de alteração de propriedade

A página **Seletor de cor simples** demonstra o uso de um ViewModel simples. As associações de dados permitem que o usuário selecione uma cor usando três elementos `Slider` para o matiz, a saturação e a luminosidade.

O ViewModel é a origem da associação de dados. O ViewModel *não* define propriedades vinculáveis, mas implementa um mecanismo de notificação que permite que a infraestrutura de associação seja notificada quando o valor de uma propriedade é alterado. Esse mecanismo de notificação é a interface [`INotifyPropertyChanged`](xref:System.ComponentModel.INotifyPropertyChanged), que define um único evento chamado [`PropertyChanged`](xref:System.ComponentModel.INotifyPropertyChanged.PropertyChanged). Em geral, uma classe que implementa essa interface dispara o evento quando uma de suas propriedades públicas altera o valor. O evento não precisa ser disparado se a propriedade nunca é alterada. (A interface `INotifyPropertyChanged` também é implementada por `BindableObject` e um evento `PropertyChanged` é disparado sempre que uma propriedade vinculável altera o valor.)

A classe `HslColorViewModel` define cinco propriedades: As propriedades `Hue`, `Saturation`, `Luminosity` e `Color` estão correlacionadas. Quando um dos três componentes de cor altera o valor, a propriedade `Color` é recalculada e os eventos `PropertyChanged` são disparados para todas as quatro propriedades:

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

Quando a propriedade `Color` é alterada, o método `GetNearestColorName` estático na classe `NamedColor` (também incluído na solução **DataBindingDemos**) obtém a cor nomeada mais próxima e define a propriedade `Name`. Essa propriedade `Name` tem um acessador `set` particular e, portanto, não pode ser definida fora da classe.

Quando um ViewModel é definido como uma origem da associação, a infraestrutura de associação anexa um manipulador ao evento `PropertyChanged`. Dessa forma, a associação pode ser notificada de alterações nas propriedades e pode então definir as propriedades de destino com base nos valores alterados.

No entanto, quando uma propriedade de destino (ou a definição `Binding` de uma propriedade de destino) tem um `BindingMode` igual a `OneTime`, não é necessário que a infraestrutura de associação anexe um manipulador no evento `PropertyChanged`. A propriedade de destino é atualizada somente quando o `BindingContext` é alterado e não quando a propriedade de origem em si é alterada.

O arquivo XAML **Seletor de cor simples** cria uma instância do `HslColorViewModel` no dicionário de recursos da página e inicializa a propriedade `Color`. A propriedade `BindingContext` da `Grid` é definida como uma extensão de associação `StaticResource` para referenciar esse recurso:

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

A `BoxView`, o `Label` e três exibições `Slider` herdam o contexto de associação da `Grid`. Essas exibições são destinos de associação que referenciam as propriedades de origem no ViewModel. Para a propriedade `Color` de `BoxView` e a propriedade `Text` de `Label`, as associações de dados são `OneWay`: As propriedades no modo de exibição são definidas nas propriedades do ViewModel.

No entanto, a propriedade `Value` do `Slider` é `TwoWay`. Isso permite que cada `Slider` seja definido no ViewModel e também que o ViewModel seja definido em cada `Slider`.

Quando o programa é executado pela primeira vez, a `BoxView`, o `Label` e três elementos `Slider` são definidos no ViewModel com base na propriedade `Color` inicial definida quando foi criada uma instância do ViewModel. Isso é mostrado na captura de tela do iOS à esquerda:

[![Seletor de cor simples](binding-mode-images/simplecolorselector-small.png "Seletor de cor simples")](binding-mode-images/simplecolorselector-large.png#lightbox "Seletor de cor simples")

Conforme você manipula os controles deslizantes, a `BoxView` e o `Label` são atualizados de acordo, conforme ilustrado pelas capturas de tela do Android e do UWP.

A criação de uma instância do ViewModel no dicionário de recursos é uma abordagem comum. Também é possível criar uma instância do ViewModel dentro de marcas de elemento de propriedade para a propriedade `BindingContext`. No arquivo XAML **Seletor de cor simples**, tente remover o `HslColorViewModel` do dicionário de recursos e defini-lo como a propriedade `BindingContext` da `Grid` desta forma:

```xaml
<Grid>
    <Grid.BindingContext>
        <local:HslColorViewModel Color="MediumTurquoise" />
    </Grid.BindingContext>

    ···

</Grid>
```

O contexto de associação pode ser definido em uma variedade de formas. Às vezes, o arquivo code-behind cria uma instância do ViewModel e o define como a propriedade `BindingContext` da página. Essas são todas abordagens válidas.

## <a name="overriding-the-binding-mode"></a>Substituindo o modo de associação

Se o modo de associação padrão na propriedade de destino não é adequado para uma associação de dados específica, é possível substituí-lo definindo a propriedade [`Mode`](xref:Xamarin.Forms.BindingBase.Mode) de `Binding` (ou a propriedade [`Mode`](xref:Xamarin.Forms.Xaml.BindingExtension.Mode) da extensão de marcação `Binding`) com um dos membros da enumeração `BindingMode`.

No entanto, a definição da propriedade `Mode` como `TwoWay` nem sempre funciona como esperado. Por exemplo, tente modificar o arquivo XAML **Associação de XAML alternativa** para incluir `TwoWay` na definição de associação:

```xaml
<Label Text="TEXT"
       FontSize="40"
       HorizontalOptions="Center"
       VerticalOptions="CenterAndExpand"
       Scale="{Binding Source={x:Reference slider},
                       Path=Value,
                       Mode=TwoWay}" />
```

Pode ser esperado que o `Slider` seja inicializado com o valor inicial da propriedade `Scale`, que é 1, mas isso não acontece. Quando uma associação `TwoWay` é inicializada, o destino é definido na origem primeiro, o que significa que a propriedade `Scale` é definida com o valor padrão de `Slider` igual a 0. Quando a associação `TwoWay` é definida no `Slider`, o `Slider` inicialmente é definido da origem.

Defina o modo de associação como `OneWayToSource` na amostra **Associação de XAML alternativa**:

```xaml
<Label Text="TEXT"
       FontSize="40"
       HorizontalOptions="Center"
       VerticalOptions="CenterAndExpand"
       Scale="{Binding Source={x:Reference slider},
                       Path=Value,
                       Mode=OneWayToSource}" />
```

Agora o `Slider` é inicializado como 1 (o valor padrão de `Scale`), mas a manipulação do `Slider` não afeta a propriedade `Scale` e, portanto, isso não é muito útil.

> [!NOTE]
> A classe [`VisualElement`](xref:Xamarin.Forms.VisualElement) define também as propriedades [`ScaleX`](xref:Xamarin.Forms.VisualElement.ScaleX) e [`ScaleY`](xref:Xamarin.Forms.VisualElement.ScaleY), que podem dimensionar o `VisualElement` de forma diferente nos sentidos horizontal e vertical.

Uma aplicação muito útil de substituir o modo de associação padrão com `TwoWay` envolve a propriedade `SelectedItem` de `ListView`. O modo de associação padrão é `OneWayToSource`. Quando uma associação de dados é definida na propriedade `SelectedItem` para referenciar uma propriedade de origem em um ViewModel, essa propriedade de origem é definida na seleção de `ListView`. No entanto, em algumas circunstâncias, também é recomendável inicializar a `ListView` no ViewModel.

A página **Configurações de exemplo** demonstra essa técnica. Essa página representa uma implementação simples de configurações de aplicativo, que frequentemente são definidas em um ViewModel, como este arquivo `SampleSettingsViewModel`:

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

Cada configuração de aplicativo é uma propriedade salva no dicionário de propriedades do Xamarin.Forms em um método chamado `SaveState` e carregado desse dicionário no construtor. Na parte inferior da classe estão dois métodos que ajudam a simplificar ViewModels e torná-lo menos propenso a erros. O método `OnPropertyChanged` na parte inferior tem um parâmetro opcional que é definido como a propriedade de chamada. Isso previne erros de ortografia ao especificar o nome da propriedade como uma cadeia de caracteres.

O método `SetProperty` na classe faz ainda mais: Ele compara o valor que está sendo definido como a propriedade com o valor armazenado como um campo e apenas chama `OnPropertyChanged` quando os dois valores não são iguais.

A classe `SampleSettingsViewModel` define duas propriedades para a cor do plano de fundo: A propriedade `BackgroundNamedColor` é do tipo `NamedColor`, que é uma classe que também está incluída na solução **DataBindingDemos**. A propriedade `BackgroundColor` é do tipo `Color` e é obtida da propriedade `Color` do objeto `NamedColor`.

A classe `NamedColor` usa a reflexão do .NET para enumerar todos os campos públicos estáticos na estrutura `Color` do Xamarin.Forms e armazená-los com seus nomes em uma coleção acessível na propriedade estática `All`:

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

A classe `App` no projeto **DataBindingDemos** define uma propriedade chamada `Settings` do tipo `SampleSettingsViewModel`. Essa propriedade é inicializada quando é criada uma instância da classe `App` e o método `SaveState` é chamado quando o método `OnSleep` é chamado:

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

Para obter mais informações sobre os métodos de ciclo de vida do aplicativo, confira o artigo [**Ciclo de vida do aplicativo**](~/xamarin-forms/app-fundamentals/app-lifecycle.md).

Quase todo o restante é manipulado no arquivo **SampleSettingsPage.xaml**. O `BindingContext` da página é definido usando uma extensão de marcação `Binding`: A origem da associação é a propriedade estática `Application.Current`, que é a instância da classe `App` no projeto e o `Path` é definido como a propriedade `Settings`, que é o objeto `SampleSettingsViewModel`:

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

Todos os filhos da página herdam o contexto de associação. A maioria das outras associações nesta página são propriedades em `SampleSettingsViewModel`. A propriedade `BackgroundColor` é usada para definir a propriedade `BackgroundColor` do `StackLayout` e as propriedades `Entry`, `DatePicker`, `Switch` e `Stepper` são associadas a outras propriedades no ViewModel.

A propriedade `ItemsSource` da `ListView` é definida como a propriedade estática `NamedColor.All`. Isso preenche a `ListView` com todas as instâncias de `NamedColor`. Para cada item em `ListView`, o contexto de associação para o item é definido como um objeto `NamedColor`. A `BoxView` e o `Label` na `ViewCell` estão associados às propriedades em `NamedColor`.

A propriedade `SelectedItem` da `ListView` é do tipo `NamedColor` e está associada à propriedade `BackgroundNamedColor` de `SampleSettingsViewModel`:

```xaml
SelectedItem="{Binding BackgroundNamedColor, Mode=TwoWay}"
```

O modo de associação padrão de `SelectedItem` é `OneWayToSource`, que define a propriedade de ViewModel do item selecionado. O modo `TwoWay` permite que o `SelectedItem` seja inicializado no ViewModel.

No entanto, quando o `SelectedItem` é definido desta forma, a `ListView` não rola a tela automaticamente para mostrar o item selecionado. Um pouco de código no arquivo code-behind é necessário:

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

A captura de tela do iOS à esquerda mostra o programa quando ele é executado pela primeira vez. O construtor em `SampleSettingsViewModel` inicializa a cor da tela de fundo como branca e isto é o que é selecionado na `ListView`:

[![Configurações de exemplo](binding-mode-images/samplesettings-small.png "Configurações de exemplo")](binding-mode-images/samplesettings-large.png#lightbox "Configurações de exemplo")

A outra captura de tela mostra as configurações alteradas. Ao experimentar com essa página, lembre-se de colocar o programa no modo de suspensão ou encerrá-lo no dispositivo ou no emulador em que está sendo executado. O encerramento do programa no depurador do Visual Studio não chamará a substituição `OnSleep` na classe `App`.

No próximo artigo, você verá como especificar a [**Formatação da cadeia de caracteres**](string-formatting.md) de associações de dados que são definidas na propriedade `Text` de `Label`.


## <a name="related-links"></a>Links relacionados

- [Demonstrações de associação de dados (amostra)](https://developer.xamarin.com/samples/xamarin-forms/DataBindingDemos/)
- [Capítulo de associação de dados do catálogo de Xamarin.Forms](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter16.md)
