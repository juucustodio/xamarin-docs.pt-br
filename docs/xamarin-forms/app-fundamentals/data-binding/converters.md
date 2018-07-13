---
title: Conversores de valor de associação do xamarin. Forms
description: Este artigo explica como converter valores dentro de uma associação de dados do xamarin. Forms, Implementando um conversor de valor (que é também conhecido como um conversor de associação ou um conversor de valor de associação).
ms.prod: xamarin
ms.assetid: 02B1BBE6-D804-490D-BDD4-8ACED8B70C92
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/05/2018
ms.openlocfilehash: 28892692133020de1fa5a6eb007bb3f9bcf2612b
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/12/2018
ms.locfileid: "38997472"
---
# <a name="xamarinforms-binding-value-converters"></a>Conversores de valor de associação do xamarin. Forms

Associações de dados geralmente transferir dados de uma propriedade de origem, uma propriedade de destino e, em alguns casos, a propriedade de origem da propriedade de destino. Essa transferência é simples quando as propriedades de origem e destino são do mesmo tipo, ou quando um tipo pode ser convertido para o outro tipo por meio de uma conversão implícita. Quando isso não é o caso, uma conversão de tipo deve ser realizadas.

No [ **cadeia de caracteres de formatação** ](string-formatting.md) artigo, você viu como é possível usar o `StringFormat` propriedade de associação de dados para converter qualquer tipo em uma cadeia de caracteres. Para outros tipos de conversões, você precisa escrever algum código especializado em uma classe que implementa o [ `IValueConverter` ](xref:Xamarin.Forms.IValueConverter) interface. (A plataforma Universal do Windows contém uma classe semelhante chamada [ `IValueConverter` ](/uwp/api/Windows.UI.Xaml.Data.IValueConverter/) no `Windows.UI.Xaml.Data` namespace, mas isso `IValueConverter` está no `Xamarin.Forms` namespace.) As classes que implementam `IValueConverter` são chamados *conversores de valor*, mas eles são também conhecidos como *conversores de associação* ou *conversores de valor de associação*.

## <a name="the-ivalueconverter-interface"></a>A Interface IValueConverter

Suponha que você deseja definir uma associação de dados onde a propriedade de origem é do tipo `int` mas a propriedade de destino é um `bool`. Você deseja que essa associação de dados para produzir um `false` valor quando o código-fonte inteiro é igual a 0, e `true` caso contrário.  

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

Você definir uma instância dessa classe para o [ `Converter` ](xref:Xamarin.Forms.Binding.Converter) propriedade do `Binding` classe ou para o [ `Converter` ](xref:Xamarin.Forms.Xaml.BindingExtension.Converter) propriedade do `Binding` extensão de marcação. Essa classe se torna parte da ligação de dados.

O `Convert` método é chamado quando dados são movidos da origem para o destino no `OneWay` ou `TwoWay` associações. O `value` parâmetro é o objeto ou o valor da fonte de vinculação de dados. O método deve retornar um valor do tipo de destino da associação de dados. O método mostrado aqui conversões de `value` parâmetro para um `int` e, em seguida, compara-o com 0 para um `bool` valor de retorno.

O `ConvertBack` método é chamado quando dados são movidos do destino para a origem na `TwoWay` ou `OneWayToSource` associações. `ConvertBack` executa a conversão oposta: ele pressupõe que o `value` parâmetro é um `bool` do destino e convertê-lo para um `int` retornar o valor para a fonte.

Se a associação de dados também inclui um `StringFormat` configuração, o conversor de valor for invocado antes que o resultado é formatado como uma cadeia de caracteres.

O **botões permitem** página na [ **demonstrações de associação de dados** ](https://developer.xamarin.com/samples/xamarin-forms/DataBindingDemos/) exemplo demonstra como usar esse conversor de valor em uma associação de dados. O `IntToBoolConverter` é instanciada no dicionário de recursos da página. Ele é referenciado com um `StaticResource` extensão de marcação para definir o `Converter` propriedade nas associações de dados de dois. É muito comum para compartilhar os conversores de dados entre várias associações de dados na página:

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

Se um conversor de valor é usado em várias páginas do seu aplicativo, você pode instanciá-la no dicionário de recursos do **App. XAML** arquivo.

O **habilitar botões** página demonstra um comum necessário quando um `Button` executa uma operação com base em texto que o usuário digita em um `Entry` modo de exibição. Se nada tiver sido tipado para o `Entry`, o `Button` deve ser desabilitado. Cada `Button` contém uma ligação de dados no seu `IsEnabled` propriedade. A origem da associação de dados é o `Length` propriedade do `Text` propriedade correspondente `Entry`. Se esse `Length` propriedade não é o conversor de valor de 0, retornará `true` e o `Button` está habilitado:

[![Habilitar os botões](converters-images/enablebuttons-small.png "habilitar botões")](converters-images/enablebuttons-large.png#lightbox "habilitar botões")

Observe que o `Text` propriedade em cada `Entry` é inicializada como uma cadeia de caracteres vazia. O `Text` é de propriedade `null` por padrão e os dados de associação não funcionará neste caso.

Alguns conversores de valor são escritos especificamente para aplicativos específicos, enquanto outros estão generalizados. Se você souber que um conversor de valor será usado apenas no `OneWay` associações, em seguida, a `ConvertBack` método pode simplesmente retornar `null`.

O `Convert` método mostrado acima implicitamente supõe que o `value` argumento é do tipo `int` e o valor de retorno deve ser do tipo `bool`. Da mesma forma, o `ConvertBack` método pressupõe que o `value` argumento é do tipo `bool` e o valor de retorno é `int`. Se não for o caso, ocorrerá uma exceção de tempo de execução.

Você pode escrever conversores de valor para ser mais generalizada e para aceitar vários tipos diferentes de dados. O `Convert` e `ConvertBack` métodos podem usar o `as` ou `is` operadores com o `value` parâmetro, ou pode chamar `GetType` nesse parâmetro para determinar seu tipo e, em seguida, executar algo apropriado. O tipo esperado do valor de retorno de cada método é determinado pelo `targetType` parâmetro. Às vezes, os conversores de valor são usados com associações de dados de diferentes tipos de destino; o conversor de valor pode usar o `targetType` argumento para executar uma conversão do tipo correto.

Se a conversão que está sendo executada é diferente para diferentes culturas, use o `culture` parâmetro para essa finalidade. O `parameter` argumento para `Convert` e `ConvertBack` é discutida posteriormente neste artigo.

## <a name="binding-converter-properties"></a>Propriedades de conversor de associação

Classes de conversor de valor podem ter propriedades e parâmetros genéricos. Este conversor de valor específico converte um `bool` da origem para um objeto do tipo `T` para o destino:

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

O **indicadores de comutador** página demonstra como ele pode ser usado para exibir o valor de um `Switch` modo de exibição. Embora seja comum para instanciar os conversores de valor como recursos em um dicionário de recursos, esta página demonstra uma alternativa: cada conversor de valor é instanciado entre `Binding.Converter` marcas de elemento de propriedade. O `x:TypeArguments` indica o argumento genérico, e `TrueObject` e `FalseObject` são configuradas para objetos desse tipo:

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

Nos últimos três `Switch` e `Label` pares, o argumento genérico é definido como `Style`e todo `Style` objetos são fornecidos para os valores de `TrueObject` e `FalseObject`. Eles substituem o estilo implícito para `Label` definida no dicionário de recursos, portanto, as propriedades de estilo são atribuídas explicitamente ao `Label`. Ativar/desativar a `Switch` faz com que o correspondente `Label` para refletir a alteração:

[![Alternar os indicadores](converters-images/switchindicators-small.png "alternar indicadores")](converters-images/switchindicators-large.png#lightbox "alternar indicadores")

Também é possível usar [ `Triggers` ](~/xamarin-forms/app-fundamentals/triggers.md) para implementar alterações semelhantes na interface do usuário com base em outros modos de exibição.

## <a name="binding-converter-parameters"></a>Parâmetros de conversor de associação

O `Binding` classe define um [ `ConverterParameter` ](xref:Xamarin.Forms.Binding.ConverterParameter) propriedade e o `Binding` extensão de marcação também define uma [ `ConverterParameter` ](xref:Xamarin.Forms.Xaml.BindingExtension.ConverterParameter) propriedade. Se essa propriedade for definida, o valor é passado para o `Convert` e `ConvertBack` métodos como o `parameter` argumento. Mesmo se a instância do conversor de valor é compartilhada entre várias associações de dados, o `ConverterParameter` podem ser diferentes para realizar conversões um pouco diferentes.

O uso de `ConverterParameter` é demonstrado com um programa de seleção de cor. Nesse caso, o `RgbColorViewModel` tem três propriedades do tipo `double` denominado `Red`, `Green`, e `Blue` que ele usa para construir um `Color` valor:

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

O `Red`, `Green`, e `Blue` intervalo de propriedades entre 0 e 1. No entanto, talvez você prefira que os componentes de ser exibidos como valores hexadecimais de dois dígitos.

Para exibi-los como valores hexadecimais em XAML, devem ser multiplicados por 255, convertidos em um inteiro e, em seguida, formatados com uma especificação de "X2" no `StringFormat` propriedade. As duas primeiras tarefas (multiplicação por 255 e conversão para um número inteiro) podem ser manipuladas pelo conversor de valor. Para tornar o conversor de valor como generalizada quanto possível, o fator de multiplicação pode ser especificado com o `ConverterParameter` propriedade, o que significa que ele entra a `Convert` e `ConvertBack` métodos como o `parameter` argumento:

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

O `Convert` converte de um `double` para `int` enquanto multiplicar pela `parameter` valor; o `ConvertBack` divide o inteiro `value` argumento por `parameter` e retorna um `double` resultado. (O programa mostrado abaixo, o conversor de valor é usado apenas com a cadeia de caracteres de formatação, portanto, `ConvertBack` não é usado.)

O tipo do `parameter` argumento é provavelmente será diferente dependendo se a associação de dados é definida no código ou XAML. Se o `ConverterParameter` propriedade de `Binding` é definido no código, é provável que seja definida como um valor numérico:

```csharp
binding.ConverterParameter = 255;
```

O `ConverterParameter` propriedade é do tipo `Object`, portanto, o compilador c# interpreta a 255 literal como um número inteiro e define a propriedade com esse valor.

No XAML, no entanto, o `ConverterParameter` é provável que seja definida como esta:

```xaml
<Label Text="{Binding Red,
                      Converter={StaticResource doubleToInt},
                      ConverterParameter=255,
                      StringFormat='Red = {0:X2}'}" />
```

O é de 255 semelhante a um número, mas porque `ConverterParameter` é do tipo `Object`, o analisador XAML trata a 255 como uma cadeia de caracteres.

Por esse motivo, o conversor de valor mostrado acima inclui um separado `GetParameter` método que lida com casos para `parameter` sendo do tipo `double`, `int`, ou `string`.  

O **seletor de cor RGB** cria uma instância de página `DoubleToIntConverter` em seu dicionário de recursos após a definição de dois estilos implícitos:

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

Os valores de `Red` e `Green` propriedades são exibidas com um `Binding` extensão de marcação. O `Blue` propriedade, no entanto, instancia o `Binding` para demonstrar como explícito `double` valor pode ser definido como `ConverterParameter` propriedade.

Aqui está o resultado:

[![Seletor de cores RGB](converters-images/rgbcolorselector-small.png "seletor de cor RGB")](converters-images/rgbcolorselector-large.png#lightbox "seletor de cores RGB")


## <a name="related-links"></a>Links relacionados

- [Demonstrações de associação de dados (amostra)](https://developer.xamarin.com/samples/xamarin-forms/DataBindingDemos/)
- [Capítulo de associação de dados do catálogo de xamarin. Forms](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter16.md)
