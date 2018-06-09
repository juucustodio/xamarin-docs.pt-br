---
title: Conversores de valor de associação xamarin. Forms
description: Este artigo explica como converter valores dentro de uma associação de dados do xamarin. Forms implementando um conversor de valor (que é também conhecido como um conversor de associação ou conversor de valor de associação).
ms.prod: xamarin
ms.assetid: 02B1BBE6-D804-490D-BDD4-8ACED8B70C92
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/05/2018
ms.openlocfilehash: a5bd52d43ef93013537f30c7d5e0c31cbf336d07
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/08/2018
ms.locfileid: "35241823"
---
# <a name="xamarinforms-binding-value-converters"></a>Conversores de valor de associação xamarin. Forms

Associações de dados geralmente transferir dados de uma propriedade de origem para uma propriedade de destino e, em alguns casos de propriedade de destino para a propriedade de origem. Essa transferência é simples quando as propriedades de origem e destino são do mesmo tipo, ou quando um tipo pode ser convertido para o outro tipo por meio de uma conversão implícita. Quando esse não for o caso, uma conversão de tipo deve ser realizadas.

No [ **cadeia de caracteres de formatação** ](string-formatting.md) artigo, você viu como você pode usar o `StringFormat` propriedade de associação de dados para converter qualquer tipo em uma cadeia de caracteres. Para outros tipos de conversões, você precisa escrever um código especializado em uma classe que implementa o [ `IValueConverter` ](https://developer.xamarin.com/api/type/Xamarin.Forms.IValueConverter/) interface. (Plataforma Universal do Windows contém uma classe semelhante denominada [ `IValueConverter` ](/uwp/api/Windows.UI.Xaml.Data.IValueConverter/) no `Windows.UI.Xaml.Data` namespace, mas isso `IValueConverter` está no `Xamarin.Forms` namespace.) As classes que implementam `IValueConverter` são chamados *conversores de valor*, mas eles são também conhecidos como *associação conversores* ou *conversores de valor de associação*.

## <a name="the-ivalueconverter-interface"></a>A Interface IValueConverter

Suponha que você deseja definir uma associação de dados onde a propriedade de origem é do tipo `int` , mas a propriedade de destino é um `bool`. Você deseja que essa associação de dados para produzir um `false` valor quando a fonte de inteiro é igual a 0, e `true` caso contrário.  

Você pode fazer isso com uma classe que implementa o `IValueConverter` interface:

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

Definir uma instância dessa classe para o [ `Converter` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Binding.Converter/) propriedade do `Binding` classe ou o [ `Converter` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Xaml.BindingExtension.Converter/) propriedade do `Binding` extensão de marcação. Essa classe se torna parte da associação de dados.

O `Convert` método é chamado quando dados são movidos da origem para o destino no `OneWay` ou `TwoWay` associações. O `value` parâmetro é o objeto ou o valor da fonte de associação de dados. O método deve retornar um valor do tipo de destino da associação de dados. O método mostrado aqui conversões de `value` parâmetro para um `int` e o compara com 0 para um `bool` valor de retorno.

O `ConvertBack` método é chamado quando dados são movidos do destino para a fonte em `TwoWay` ou `OneWayToSource` associações. `ConvertBack` executa a conversão oposta: pressupõe a `value` parâmetro é um `bool` do destino e o converte em um `int` retornar um valor para a fonte.

Se a associação de dados também inclui um `StringFormat` configuração, o conversor de valor é chamado antes do resultado é formatado como uma cadeia de caracteres.

O **botões ativar** página o [ **demonstrações de associação de dados** ](https://developer.xamarin.com/samples/xamarin-forms/DataBindingDemos/) demonstra como usar este conversor de valor em uma associação de dados. O `IntToBoolConverter` é instanciado no dicionário de recursos da página. Depois é referenciado com um `StaticResource` extensão de marcação para definir o `Converter` propriedade nas associações de dados. É muito comum para compartilhar conversores de dados entre várias associações de dados na página:

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

Se um conversor de valor é usado em várias páginas do seu aplicativo, você pode instanciá-lo no dicionário de recursos no **App** arquivo.

O **botões ativar** página demonstra uma comum necessário quando um `Button` executa uma operação com base no texto que o usuário digita em um `Entry` exibição. Se nada foi digitado para o `Entry`, o `Button` devem ser desabilitados. Cada `Button` contém uma associação de dados em seu `IsEnabled` propriedade. A fonte de associação de dados é o `Length` propriedade o `Text` propriedade correspondente `Entry`. Se esse `Length` propriedade não é 0, o conversor de valor retorna `true` e `Button` está habilitado:

[![Habilitar botões](converters-images/enablebuttons-small.png "habilitar botões")](converters-images/enablebuttons-large.png#lightbox "habilitar botões")

Observe que o `Text` propriedade em cada `Entry` é inicializada com uma cadeia de caracteres vazia. O `Text` é de propriedade `null` por padrão e os dados de associação não funcionará nesse caso.

Alguns conversores de valor são escritos especificamente para aplicativos específicos, enquanto outros estão generalizados. Se você souber que um conversor de valor só será usado no `OneWay` associações, em seguida, o `ConvertBack` método pode simplesmente retornar `null`.

O `Convert` método mostrado acima implicitamente pressupõe que o `value` argumento é do tipo `int` e o valor de retorno deve ser do tipo `bool`. Da mesma forma, o `ConvertBack` método pressupõe que o `value` argumento é do tipo `bool` e o valor de retorno é `int`. Se esse não for o caso, ocorrerá uma exceção de tempo de execução.

Você pode criar conversores de valor para ser mais generalizada e aceitar diferentes tipos de dados. O `Convert` e `ConvertBack` métodos podem usar o `as` ou `is` operadores com o `value` parâmetro, ou pode chamar `GetType` no parâmetro para determinar seu tipo e, em seguida, executar algo apropriado. O tipo esperado de cada método valor de retorno é determinado pelo `targetType` parâmetro. Às vezes, conversores de valor são usados com associações de dados de diferentes tipos de destino; o conversor de valor pode usar o `targetType` argumento para executar uma conversão para o tipo correto.

Se a conversão que está sendo executada é diferente para diferentes culturas, use o `culture` parâmetro para essa finalidade. O `parameter` argumento `Convert` e `ConvertBack` é abordada posteriormente neste artigo.

## <a name="binding-converter-properties"></a>Propriedades de associação de conversor

Classes de conversor de valor podem ter propriedades e parâmetros genéricos. O conversor de valor específico converte um `bool` da origem para um objeto do tipo `T` para o destino:

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

O **Switch indicadores** página demonstra como ele pode ser usado para exibir o valor de um `Switch` exibição. Embora seja comum para instanciar conversores de valor como recursos em um dicionário de recursos, esta página demonstra uma alternativa: cada conversor de valor é instanciado entre `Binding.Converter` marcas de elemento de propriedade. O `x:TypeArguments` indica o argumento genérico, e `TrueObject` e `FalseObject` são configuradas para objetos desse tipo:

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

Nos últimos três `Switch` e `Label` pares, o argumento genérico é definido como `Style`e todo `Style` objetos são fornecidos para os valores de `TrueObject` e `FalseObject`. Eles substituem o estilo implícita para `Label` definida no dicionário de recursos, portanto as propriedades de estilo são atribuídas explicitamente para o `Label`. Alternando o `Switch` faz com que o correspondente `Label` para refletir a alteração:

[![Alternar indicadores](converters-images/switchindicators-small.png "alternar indicadores")](converters-images/switchindicators-large.png#lightbox "alternar indicadores")

Também é possível usar [ `Triggers` ](~/xamarin-forms/app-fundamentals/triggers.md) para implementar alterações semelhantes na interface do usuário com base em outros modos de exibição.

## <a name="binding-converter-parameters"></a>Parâmetros de associação de conversor

O `Binding` classe define um [ `ConverterParameter` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Binding.ConverterParameter/) propriedade e o `Binding` extensão de marcação também define uma [ `ConverterParameter` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Xaml.BindingExtension.ConverterParameter/) propriedade. Se essa propriedade for definida, o valor é passado para o `Convert` e `ConvertBack` métodos como o `parameter` argumento. Mesmo se a instância do conversor de valor é compartilhada entre várias associações de dados, o `ConverterParameter` pode ser diferente para realizar conversões um pouco diferentes.

O uso de `ConverterParameter` é demonstrado com um programa de seleção de cor. Nesse caso, o `RgbColorViewModel` tem três propriedades do tipo `double` chamado `Red`, `Green`, e `Blue` que ele usa para construir um `Color` valor:

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

O `Red`, `Green`, e `Blue` intervalo de propriedades entre 0 e 1. No entanto, você pode preferir que os componentes de ser exibidos como valores de dois dígitos hexadecimais.

Para exibi-los como valores hexadecimais em XAML, devem ser multiplicados por 255, convertidos em um inteiro e, em seguida, formatados com uma especificação de "X2" no `StringFormat` propriedade. As duas primeiras tarefas (multiplicando 255 e converter em um número inteiro) podem ser tratadas pelo conversor de valor. Para tornar o conversor de valor generalizado possível, o fator de multiplicação pode ser especificado com o `ConverterParameter` propriedade, o que significa que ele entra a `Convert` e `ConvertBack` métodos como o `parameter` argumento:

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

O `Convert` converte de um `double` para `int` ao mesmo tempo, multiplicando o `parameter` valor; o `ConvertBack` divide o número inteiro `value` argumento por `parameter` e retorna um `double` resultados. (O programa mostrado abaixo, o conversor de valor é usado apenas com a cadeia de caracteres de formatação, portanto `ConvertBack` não é usado.)

O tipo do `parameter` argumento é provavelmente será diferente dependendo se a associação de dados é definida em XAML ou de código. Se o `ConverterParameter` propriedade `Binding` é definido no código, é provável que seja definido como um valor numérico:

```csharp
binding.ConverterParameter = 255;
```

O `ConverterParameter` é de propriedade do tipo `Object`, portanto, o compilador c# interpreta a 255 literal como um inteiro e define a propriedade com esse valor.

Em XAML, no entanto, o `ConverterParameter` deve ser definido como este:

```xaml
<Label Text="{Binding Red,
                      Converter={StaticResource doubleToInt},
                      ConverterParameter=255,
                      StringFormat='Red = {0:X2}'}" />
```

A 255 parece um número, mas porque `ConverterParameter` é do tipo `Object`, o analisador XAML trata a 255 como uma cadeia de caracteres.

Por esse motivo, o conversor de valor mostrado acima inclui um separado `GetParameter` método que lida com casos para `parameter` sendo do tipo `double`, `int`, ou `string`.  

O **seletor de cor RGB** página instancia `DoubleToIntConverter` no dicionário de recurso após a definição de dois estilos implícita:

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

Os valores de `Red` e `Green` propriedades são exibidas com um `Binding` extensão de marcação. O `Blue` propriedade, no entanto, instancia o `Binding` classe para demonstrar como explícito `double` valor pode ser definido como `ConverterParameter` propriedade.

Aqui está o resultado:

[![Seletor de cores RGB](converters-images/rgbcolorselector-small.png "seletor de cor RGB")](converters-images/rgbcolorselector-large.png#lightbox "seletor de cores RGB")


## <a name="related-links"></a>Links relacionados

- [Demonstrações de associação de dados (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/DataBindingDemos/)
- [Capítulo de associação de dados do catálogo de xamarin. Forms](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter16.md)
