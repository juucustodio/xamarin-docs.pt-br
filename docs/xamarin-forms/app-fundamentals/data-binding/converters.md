---
title: " Xamarin.Forms conversão de valor de associação" Descrição: "Este artigo explica como converter ou converter valores em uma Xamarin.Forms Associação de dados implementando um conversor de valor (que também é conhecido como um conversor de associação ou conversor de valor de associação)".
MS. Prod: xamarin MS. AssetID: 02B1BBE6-D804-490D-BDD4-8ACED8B70C92 MS. Technology: xamarin-Forms autor: davidbritch MS. Author: dabritch MS. Date: 01/05/2018 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="xamarinforms-binding-value-converters"></a>Xamarin.FormsConversores de valor de associação

[![Baixar exemplo ](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/databindingdemos)

Normalmente, associações de dados transferem dados de uma propriedade de origem para uma propriedade de destino e, em alguns casos, da propriedade de destino para a propriedade de origem. Essa transferência é simples quando as propriedades de origem e de destino são do mesmo tipo ou quando um tipo pode ser convertido para outro por meio de uma conversão implícita. Quando não é esse o caso, é necessário realizar uma conversão de tipo.

No artigo [**Formatação de Cadeia de Caracteres**](string-formatting.md), você viu como é possível usar a propriedade `StringFormat` de uma associação de dados para converter qualquer tipo em uma cadeia de caracteres. Para outros tipos de conversões, você precisa escrever um código especializado em uma classe que implementa a [`IValueConverter`](xref:Xamarin.Forms.IValueConverter) interface. (O Plataforma Universal do Windows contém uma classe semelhante chamada [`IValueConverter`](/uwp/api/Windows.UI.Xaml.Data.IValueConverter/) no `Windows.UI.Xaml.Data` namespace, mas isso `IValueConverter` está no `Xamarin.Forms` namespace.) As classes que implementam `IValueConverter` são chamadas de *conversores de valor*, mas também são conhecidas como *conversores de associação* ou *conversores de valor de associação*.

## <a name="the-ivalueconverter-interface"></a>A interface IValueConverter

Digamos que você queira definir uma associação de dados em que a propriedade de origem é do tipo `int`, mas a propriedade de destino é um `bool`. Você quer que essa associação de dados produza um valor de `false` quando a origem do inteiro for igual a 0 e, caso contrário, `true`.  

É possível fazer isso com uma classe que implemente a interface `IValueConverter`:

```csharp
public class IntToBoolConverter : IValueConverter
{
    public object Convert(object value, Type targetType, object parameter, CultureInfo culture)
    {
        return (int)value != 0;
    }

    public object ConvertBack(object value, Type targetType, object parameter, CultureInfo culture)
    {
        return (bool)value ? 1 : 0;
    }
}
```

Você define uma instância dessa classe para a [`Converter`](xref:Xamarin.Forms.Binding.Converter) propriedade da `Binding` classe ou para a [`Converter`](xref:Xamarin.Forms.Xaml.BindingExtension.Converter) propriedade da `Binding` extensão de marcação. Essa classe se torna parte da associação de dados.

O método `Convert` é chamado quando dados são passados da origem para o destino nas associações `OneWay` ou `TwoWay`. O parâmetro `value` é o objeto ou o valor da origem da associação de dados. O método deve retornar um valor com o tipo do destino da associação de dados. O método mostrado aqui converte o parâmetro `value` para um `int` e, em seguida, o compara com 0 para um valor retornado de `bool`.

O método `ConvertBack` é chamado quando dados são passados do destino para a origem nas associações `TwoWay` ou `OneWayToSource`. `ConvertBack` realiza a conversão oposta: ele pressupõe que o parâmetro `value` é um `bool` do destino e o converte em um valor retornado de `int` para a fonte.

Se a associação de dados também incluir uma configuração de `StringFormat`, o conversor de valor será invocado antes que o resultado seja formatado como uma cadeia de caracteres.

A página **Habilitar Botões** no exemplo [**Demonstrações de Associação de Dados**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/databindingdemos) demonstra como usar esse conversor de valor em uma associação de dados. É criada uma instância de `IntToBoolConverter` no dicionário de recursos da página. Em seguida, ele é referenciado com uma extensão de marcação `StaticResource` para definir a propriedade `Converter` nas duas associações de dados. É muito comum compartilhar conversores de dados entre várias associações de dados na página:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:DataBindingDemos"
             x:Class="DataBindingDemos.EnableButtonsPage"
             Title="Enable Buttons">
    <ContentPage.Resources>
        <ResourceDictionary>
            <local:IntToBoolConverter x:Key="intToBool" />
        </ResourceDictionary>
    </ContentPage.Resources>

    <StackLayout Padding="10, 0">
        <Entry x:Name="entry1"
               Text=""
               Placeholder="enter search term"
               VerticalOptions="CenterAndExpand" />

        <Button Text="Search"
                HorizontalOptions="Center"
                VerticalOptions="CenterAndExpand"
                IsEnabled="{Binding Source={x:Reference entry1},
                                    Path=Text.Length,
                                    Converter={StaticResource intToBool}}" />

        <Entry x:Name="entry2"
               Text=""
               Placeholder="enter destination"
               VerticalOptions="CenterAndExpand" />

        <Button Text="Submit"
                HorizontalOptions="Center"
                VerticalOptions="CenterAndExpand"
                IsEnabled="{Binding Source={x:Reference entry2},
                                    Path=Text.Length,
                                    Converter={StaticResource intToBool}}" />
    </StackLayout>
</ContentPage>
```

Se um conversor de valor for usado em várias páginas de seu aplicativo, você poderá criar uma instância dele no dicionário de recursos no arquivo **App.xaml**.

A página **Habilitar Botões** demonstra uma necessidade comum quando um `Button` executa uma operação com base no texto que o usuário digita em uma exibição de `Entry`. Se nada tiver sido digitado no `Entry`, o `Button` deverá ser desabilitado. Cada `Button` contém uma associação de dados em sua propriedade `IsEnabled`. A origem da associação de dados é a propriedade `Length` da propriedade `Text` do `Entry` correspondente. Se essa propriedade `Length` não for 0, o conversor de valor retornará `true` e `Button` será habilitado:

[![Habilitar botões](converters-images/enablebuttons-small.png "Habilitar botões")](converters-images/enablebuttons-large.png#lightbox "Habilitar botões")

Observe que a propriedade `Text` em cada `Entry` é inicializada como uma cadeia de caracteres vazia. A propriedade `Text` é `null` por padrão e a associação de dados não funcionará nesse caso.

Alguns conversores de valor são escritos especificamente para aplicativos específicos, enquanto outros são generalizados. Se você souber que um conversor de valor será usado apenas em associações de `OneWay`, o método `ConvertBack` poderá simplesmente retornar `null`.

O método `Convert` mostrado acima supõe implicitamente que o argumento `value` é do tipo `int` e que o valor retornado deve ser do tipo `bool`. Da mesma forma, o método `ConvertBack` supõe que o argumento `value` é do tipo `bool` e que o valor retornado é `int`. Se não for esse o caso, ocorrerá uma exceção de runtime.

Você pode escrever conversores de valor de forma que eles sejam mais generalizados e aceitem vários tipos de dados diferentes. Os métodos `Convert` e `ConvertBack` podem usar os operadores `as` ou `is` com o parâmetro `value` ou podem chamar `GetType` nesse parâmetro para determinar seu tipo e, em seguida, fazer algo apropriado. O tipo esperado do valor retornado de cada método é determinado pelo parâmetro `targetType`. Às vezes, conversores de valor são usados com associações de dados com tipos de destino diferentes. O conversor de valor pode usar o argumento `targetType` para executar uma conversão para o tipo correto.

Se a conversão que está sendo executada for diferente para culturas diferentes, use o parâmetro `culture` para essa finalidade. O argumento `parameter` para `Convert` e `ConvertBack` é abordado posteriormente neste artigo.

## <a name="binding-converter-properties"></a>Propriedades do conversor de associação

As classes do conversor de valor podem ter propriedades e parâmetros genéricos. Este conversor de valor específico converte um `bool` da origem para um objeto do tipo `T` para o destino:

```csharp
public class BoolToObjectConverter<T> : IValueConverter
{
    public T TrueObject { set; get; }

    public T FalseObject { set; get; }

    public object Convert(object value, Type targetType, object parameter, CultureInfo culture)
    {
        return (bool)value ? TrueObject : FalseObject;
    }

    public object ConvertBack(object value, Type targetType, object parameter, CultureInfo culture)
    {
        return ((T)value).Equals(TrueObject);
    }
}
```

A página **Alternar Indicadores** demonstra como ele pode ser usado para exibir o valor de uma exibição de `Switch`. Embora seja comum instanciar conversores de valor como recursos em um dicionário de recursos, esta página demonstra uma alternativa: cada conversor de valor é instanciado entre marcas do elemento de propriedade `Binding.Converter`. O `x:TypeArguments` indica o argumento genérico e `TrueObject` e `FalseObject` são configurados como objetos desse tipo:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:DataBindingDemos"
             x:Class="DataBindingDemos.SwitchIndicatorsPage"
             Title="Switch Indicators">
    <ContentPage.Resources>
        <ResourceDictionary>
            <Style TargetType="Label">
                <Setter Property="FontSize" Value="18" />
                <Setter Property="VerticalOptions" Value="Center" />
            </Style>

            <Style TargetType="Switch">
                <Setter Property="VerticalOptions" Value="Center" />
            </Style>
        </ResourceDictionary>
    </ContentPage.Resources>

    <StackLayout Padding="10, 0">
        <StackLayout Orientation="Horizontal"
                     VerticalOptions="CenterAndExpand">
            <Label Text="Subscribe?" />
            <Switch x:Name="switch1" />
            <Label>
                <Label.Text>
                    <Binding Source="{x:Reference switch1}"
                             Path="IsToggled">
                        <Binding.Converter>
                            <local:BoolToObjectConverter x:TypeArguments="x:String"
                                                         TrueObject="Of course!"
                                                         FalseObject="No way!" />
                        </Binding.Converter>
                    </Binding>
                </Label.Text>
            </Label>
        </StackLayout>

        <StackLayout Orientation="Horizontal"
                     VerticalOptions="CenterAndExpand">
            <Label Text="Allow popups?" />
            <Switch x:Name="switch2" />
            <Label>
                <Label.Text>
                    <Binding Source="{x:Reference switch2}"
                             Path="IsToggled">
                        <Binding.Converter>
                            <local:BoolToObjectConverter x:TypeArguments="x:String"
                                                         TrueObject="Yes"
                                                         FalseObject="No" />
                        </Binding.Converter>
                    </Binding>
                </Label.Text>
                <Label.TextColor>
                    <Binding Source="{x:Reference switch2}"
                             Path="IsToggled">
                        <Binding.Converter>
                            <local:BoolToObjectConverter x:TypeArguments="Color"
                                                         TrueObject="Green"
                                                         FalseObject="Red" />
                        </Binding.Converter>
                    </Binding>
                </Label.TextColor>
            </Label>
        </StackLayout>

        <StackLayout Orientation="Horizontal"
                     VerticalOptions="CenterAndExpand">
            <Label Text="Learn more?" />
            <Switch x:Name="switch3" />
            <Label FontSize="18"
                   VerticalOptions="Center">
                <Label.Style>
                    <Binding Source="{x:Reference switch3}"
                             Path="IsToggled">
                        <Binding.Converter>
                            <local:BoolToObjectConverter x:TypeArguments="Style">
                                <local:BoolToObjectConverter.TrueObject>
                                    <Style TargetType="Label">
                                        <Setter Property="Text" Value="Indubitably!" />
                                        <Setter Property="FontAttributes" Value="Italic, Bold" />
                                        <Setter Property="TextColor" Value="Green" />
                                    </Style>                                    
                                </local:BoolToObjectConverter.TrueObject>

                                <local:BoolToObjectConverter.FalseObject>
                                    <Style TargetType="Label">
                                        <Setter Property="Text" Value="Maybe later" />
                                        <Setter Property="FontAttributes" Value="None" />
                                        <Setter Property="TextColor" Value="Red" />
                                    </Style>
                                </local:BoolToObjectConverter.FalseObject>
                            </local:BoolToObjectConverter>
                        </Binding.Converter>
                    </Binding>
                </Label.Style>
            </Label>
        </StackLayout>
    </StackLayout>
</ContentPage>
```

No último dos três pares de `Switch` e `Label`, o argumento genérico é definido como `Style` e objetos `Style` inteiros são fornecidos para os valores de `TrueObject` e `FalseObject`. Eles substituem o estilo implícito de `Label` definido no dicionário de recursos, portanto, as propriedades do estilo são atribuídas explicitamente ao `Label`. Ativar/desativar o `Switch` faz com que o `Label` correspondente reflita a alteração:

[![Alternar indicadores](converters-images/switchindicators-small.png "Alternar indicadores")](converters-images/switchindicators-large.png#lightbox "Alternar indicadores")

Também é possível usar [`Triggers`](~/xamarin-forms/app-fundamentals/triggers.md) o para implementar alterações semelhantes na interface do usuário com base em outras exibições.

## <a name="binding-converter-parameters"></a>Parâmetros do conversor de associação

A `Binding` classe define uma [`ConverterParameter`](xref:Xamarin.Forms.Binding.ConverterParameter) propriedade, e a `Binding` extensão de marcação também define uma [`ConverterParameter`](xref:Xamarin.Forms.Xaml.BindingExtension.ConverterParameter) propriedade. Se essa propriedade for definida, o valor será passado para os métodos `Convert` e `ConvertBack` como o argumento `parameter`. Mesmo se a instância do conversor de valor for compartilhada entre várias associações de dados, o `ConverterParameter` poderá ser diferente para realizar conversões de alguma forma diferentes.

O uso de `ConverterParameter` é demonstrado com um programa de seleção de cor. Nesse caso, o `RgbColorViewModel` tem três propriedades do tipo `double` denominadas `Red`, `Green`, e `Blue` que ele usa para construir um valor de `Color`:

```csharp
public class RgbColorViewModel : INotifyPropertyChanged
{
    Color color;
    string name;

    public event PropertyChangedEventHandler PropertyChanged;

    public double Red
    {
        set
        {
            if (color.R != value)
            {
                Color = new Color(value, color.G, color.B);
            }
        }
        get
        {
            return color.R;
        }
    }

    public double Green
    {
        set
        {
            if (color.G != value)
            {
                Color = new Color(color.R, value, color.B);
            }
        }
        get
        {
            return color.G;
        }
    }

    public double Blue
    {
        set
        {
            if (color.B != value)
            {
                Color = new Color(color.R, color.G, value);
            }
        }
        get
        {
            return color.B;
        }
    }

    public Color Color
    {
        set
        {
            if (color != value)
            {
                color = value;
                PropertyChanged?.Invoke(this, new PropertyChangedEventArgs("Red"));
                PropertyChanged?.Invoke(this, new PropertyChangedEventArgs("Green"));
                PropertyChanged?.Invoke(this, new PropertyChangedEventArgs("Blue"));
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

As propriedades `Red`, `Green` e `Blue` variam entre 0 e 1. No entanto, talvez você prefira que os componentes sejam exibidos como valores hexadecimais de dois dígitos.

Para exibi-los como valores hexadecimais em XAML, eles devem ser multiplicados por 255, convertidos em um inteiro e, em seguida, formatados com uma especificação de "X2" na propriedade `StringFormat`. As duas primeiras tarefas (multiplicar por 255 e converter em um número inteiro) podem ser realizadas pelo conversor de valor. Para tornar o conversor de valor tão generalizado quanto possível, o fator de multiplicação pode ser especificado com a propriedade `ConverterParameter`, o que significa que ele insere os métodos `Convert` e `ConvertBack` como o argumento `parameter`:

```csharp
public class DoubleToIntConverter : IValueConverter
{
    public object Convert(object value, Type targetType, object parameter, CultureInfo culture)
    {
        return (int)Math.Round((double)value * GetParameter(parameter));
    }

    public object ConvertBack(object value, Type targetType, object parameter, CultureInfo culture)
    {
        return (int)value / GetParameter(parameter);
    }

    double GetParameter(object parameter)
    {
        if (parameter is double)
            return (double)parameter;

        else if (parameter is int)
            return (int)parameter;

        else if (parameter is string)
            return double.Parse((string)parameter);

        return 1;
    }
}
```

O `Convert` é convertido de um `double` para `int` ao multiplicar pelo valor de `parameter`; o `ConvertBack` divide o argumento `value` inteiro pelo `parameter` e retorna um resultado `double`. (No programa mostrado abaixo, o conversor de valor é usado apenas para formatação da cadeia de caracteres e, portanto, `ConvertBack` não é usado.)

O tipo do argumento `parameter` provavelmente será diferente dependendo de a associação de dados estar definida no código ou em XAML. Se a propriedade `ConverterParameter` de `Binding` estiver definida no código, será provável que ela esteja definida como um valor numérico:

```csharp
binding.ConverterParameter = 255;
```

A propriedade `ConverterParameter` é do tipo `Object`, portanto, o compilador de C# interpreta o literal 255 como um número inteiro e define a propriedade com esse valor.

Em XAML, no entanto, é provável que o `ConverterParameter` esteja definido desta forma:

```xaml
<Label Text="{Binding Red,
                      Converter={StaticResource doubleToInt},
                      ConverterParameter=255,
                      StringFormat='Red = {0:X2}'}" />
```

O 255 é semelhante a um número, mas como `ConverterParameter` é do tipo `Object`, o analisador de XAML trata o 255 como uma cadeia de caracteres.

Por esse motivo, o conversor de valor mostrado acima inclui um método `GetParameter` separado que lida com os casos de `parameter` ser do tipo `double`, `int` ou `string`.  

A página **Seletor de Cor RGB** cria uma instância de `DoubleToIntConverter` em seu dicionário de recursos após a definição de dois estilos implícitos:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:DataBindingDemos"
             x:Class="DataBindingDemos.RgbColorSelectorPage"
             Title="RGB Color Selector">
    <ContentPage.Resources>
        <ResourceDictionary>
            <Style TargetType="Slider">
                <Setter Property="VerticalOptions" Value="CenterAndExpand" />
            </Style>

            <Style TargetType="Label">
                <Setter Property="HorizontalTextAlignment" Value="Center" />
            </Style>

            <local:DoubleToIntConverter x:Key="doubleToInt" />
        </ResourceDictionary>
    </ContentPage.Resources>

    <StackLayout>
        <StackLayout.BindingContext>
            <local:RgbColorViewModel Color="Gray" />
        </StackLayout.BindingContext>

        <BoxView Color="{Binding Color}"
                 VerticalOptions="FillAndExpand" />

        <StackLayout Margin="10, 0">
            <Label Text="{Binding Name}" />

            <Slider Value="{Binding Red}" />
            <Label Text="{Binding Red,
                                  Converter={StaticResource doubleToInt},
                                  ConverterParameter=255,
                                  StringFormat='Red = {0:X2}'}" />

            <Slider Value="{Binding Green}" />
            <Label Text="{Binding Green,
                                  Converter={StaticResource doubleToInt},
                                  ConverterParameter=255,
                                  StringFormat='Green = {0:X2}'}" />

            <Slider Value="{Binding Blue}" />
            <Label>
                <Label.Text>
                    <Binding Path="Blue"
                             StringFormat="Blue = {0:X2}"
                             Converter="{StaticResource doubleToInt}">
                        <Binding.ConverterParameter>
                            <x:Double>255</x:Double>
                        </Binding.ConverterParameter>
                    </Binding>
                </Label.Text>
            </Label>
        </StackLayout>
    </StackLayout>
</ContentPage>    
```

Os valores das propriedades `Red` e `Green` são exibidos com uma extensão de marcação `Binding`. A propriedade `Blue`, no entanto, instancia a classe `Binding` para demonstrar como um valor de `double` explícito pode ser definido como a propriedade `ConverterParameter`.

Eis o resultado:

[![Seletor de cores RGB](converters-images/rgbcolorselector-small.png "Seletor de cores RGB")](converters-images/rgbcolorselector-large.png#lightbox "Seletor de cores RGB")

## <a name="related-links"></a>Links relacionados

- [Demonstrações de associação de dados (amostra)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/databindingdemos)
- [Capítulo de vinculação de dados do Xamarin.Forms livro](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter16.md)
