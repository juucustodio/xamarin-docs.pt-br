---
title: Usando o controle deslizante
description: "Use um controle deslizante para selecionar de um intervalo de valores contínuos."
ms.topic: article
ms.prod: xamarin
ms.assetid: 36B1C645-26E0-4874-B6B6-BDBF77662878
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 03/16/2018
ms.openlocfilehash: ce5d8c46282d3831edcf58af63b66d1f6292f75b
ms.sourcegitcommit: 73bd0c7e5f237f0a1be70a6c1384309bb26609d5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/22/2018
---
# <a name="using-slider"></a>Usando o controle deslizante

_Use um controle deslizante para selecionar de um intervalo de valores contínuos._

O xamarin. Forms [ `Slider` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Slider/) é uma barra horizontal que pode ser manipulada pelo usuário para selecionar um `double` valor de um intervalo contínuo. 

O `Slider` define três propriedades do tipo `double`:

- [`Minimum`](https://developer.xamarin.com/api/property/Xamarin.Forms.Slider.Minimum/) é o mínimo do intervalo, com um valor padrão de 0.
- [`Maximum`](https://developer.xamarin.com/api/property/Xamarin.Forms.Slider.Maximum/) é o máximo do intervalo, com um valor padrão de 1.
- [`Value`](https://developer.xamarin.com/api/property/Xamarin.Forms.Slider.Value/) é o valor do controle deslizante, que pode variar entre `Minimum` e `Maximum` e tem um valor padrão de 0.

Todas as três propriedades têm o respaldo `BindableProperty` objetos. O `Value` propriedade tem um modo de associação padrão de `BindingMode.TwoWay`, que significa que ele é adequado como uma fonte de associação em um aplicativo que usa o [Model-View-ViewModel (MVVM)](~/xamarin-forms/enterprise-application-patterns/mvvm.md) arquitetura. 

> [!WARNING]
> Internamente, o `Slider` garante que `Minimum` é menor que `Maximum`. Se `Minimum` ou `Maximum` nunca são definidas para que `Minimum` é não é menor que `Maximum`, uma exceção é gerada. Consulte o [ **precauções** ](#precautions) seção abaixo para obter mais informações sobre como configurar o `Minimum` e `Maximum` propriedades.

O `Slider` força o `Value` propriedade para que ela seja entre `Minimum` e `Maximum`, inclusive. Se o `Minimum` propriedade é definida como um valor maior do que o `Value` propriedade, o `Slider` define o `Value` propriedade `Minimum`. Da mesma forma, se `Maximum` é definido como um valor menor que `Value`, em seguida, `Slider` define o `Value` propriedade `Maximum`. 

`Slider` define uma [ `ValueChanged` ](https://developer.xamarin.com/api/event/Xamarin.Forms.Slider.ValueChanged/) evento é acionado quando o `Value` alterações, por meio de manipulação de usuário da `Slider` ou quando o programa define a `Value` propriedade diretamente. Um `ValueChanged` evento também é acionado quando o `Value` propriedade é forçada, conforme descrito no parágrafo anterior. 

O [ `ValueChangedEventArgs` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ValueChangedEventArgs/) objeto que acompanha o `ValueChanged` evento tem duas propriedades, ambos do tipo `double`: [ `OldValue` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ValueChangedEventArgs.OldValue/) e [ `NewValue` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ValueChangedEventArgs.NewValue/). No momento em que o evento é acionado, o valor de `NewValue` é igual a `Value` propriedade o `Slider` objeto.

> [!WARNING]
> Não use opções de layout horizontal irrestrita de `Center`, `Start`, ou `End` com `Slider`. No Android e UWP, o `Slider` recolhe a uma barra de comprimento zero e no iOS, a barra é muito curto. Mantenha o padrão `HorizontalOptions` de `Fill`e não usar uma largura de `Auto` ao colocar `Slider` em um `Grid` layout.

## <a name="basic-slider-code-and-markup"></a>Marcação e código de controle deslizante básico

O [ **SliderDemos** ](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/SliderDemos) exemplo começa com três páginas que são funcionalmente idênticos, mas são implementadas de maneiras diferentes. A primeira página usa apenas o código c#, o segundo usa XAML com um manipulador de eventos no código e o terceiro é evitar o manipulador de eventos usando associação de dados no arquivo XAML.

### <a name="creating-a-slider-in-code"></a>Criando um controle deslizante em código

O **código básico do controle deslizante** página o [ **SliderDemos** ](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/SliderDemos) exemplo mostra Mostrar para criar um `Slider` e dois `Label` objetos no código:

```csharp
public class BasicSliderCodePage : ContentPage
{
    public BasicSliderCodePage()
    {
        Label rotationLabel = new Label
        {
            Text = "ROTATING TEXT",
            FontSize = Device.GetNamedSize(NamedSize.Large, typeof(Label)),
            HorizontalOptions = LayoutOptions.Center,
            VerticalOptions = LayoutOptions.CenterAndExpand
        };

        Label displayLabel = new Label
        {
            Text = "(uninitialized)",
            HorizontalOptions = LayoutOptions.Center,
            VerticalOptions = LayoutOptions.CenterAndExpand
        };

        Slider slider = new Slider
        {
            Maximum = 360
        };
        slider.ValueChanged += (sender, args) =>
        {
            rotationLabel.Rotation = slider.Value;
            displayLabel.Text = String.Format("The Slider value is {0}", args.NewValue);
        };

        Title = "Basic Slider Code";
        Padding = new Thickness(10, 0);
        Content = new StackLayout
        {
            Children =
            {
                rotationLabel,
                slider,
                displayLabel
            }
        };
    }
}
```

O `Slider` é inicializado para ter um `Maximum` propriedade de 360. O `ValueChanged` manipulador do `Slider` usa o `Value` propriedade do `slider` objeto para definir o `Rotation` propriedade do primeiro `Label` e usa o `String.Format` método com o `NewValue` propriedade do argumentos de evento para definir o `Text` propriedade da segunda `Label`. Essas duas abordagens para obter o valor atual de `Slider` são intercambiáveis. 

Aqui está o programa em execução no iOS, Android e Windows UWP (plataforma Universal) dispositivos:

[![Código de controle deslizante básico](slider-images/BasicSliderCode.png "código de controle deslizante básico")](slider-images/BasicSliderCode-Large.png#lightbox)

A segunda `Label` exibe o texto "(não inicializado)" até que o `Slider` é manipulado, quais casos primeiro `ValueChanged` evento seja acionado. Observe que o número de casas decimais exibidas é diferente para as três plataformas. Essas diferenças estão relacionadas às implementações de plataforma de `Slider` e são discutidos neste artigo na seção [diferenças de implementação de plataforma](#implementations).

### <a name="creating-a-slider-in-xaml"></a>Criando um controle deslizante em XAML

O **básica XAML do controle deslizante** página é funcionalmente o mesmo que **código básico do controle deslizante** mas implementada principalmente em XAML:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="SliderDemos.BasicSliderXamlPage"
             Title="Basic Slider XAML"
             Padding="10, 0">
    <StackLayout>
        <Label x:Name="rotatingLabel" 
               Text="ROTATING TEXT"
               FontSize="Large"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand" />

        <Slider Maximum="360"
                ValueChanged="OnSliderValueChanged" />

        <Label x:Name="displayLabel"
               Text="(uninitialized)"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand" />
    </StackLayout>
</ContentPage>
```

O arquivo code-behind contém o manipulador para o `ValueChanged` evento:

```csharp
public partial class BasicSliderXamlPage : ContentPage
{
    public BasicSliderXamlPage()
    {
        InitializeComponent();
    }

    void OnSliderValueChanged(object sender, ValueChangedEventArgs args)
    {
        double value = args.NewValue;
        rotatingLabel.Rotation = value;
        displayLabel.Text = String.Format("The Slider value is {0}", value);
    }
}
```

Também é possível que o manipulador de eventos obter o `Slider` que está acionando o evento por meio de `sender` argumento. O `Value` propriedade contém o valor atual:

```csharp
double value = ((Slider)sender).Value;
```

Se o `Slider` objeto foi fornecido um nome no arquivo XAML com um `x:Name` atributo (por exemplo, "seletor") e, em seguida, o manipulador de eventos pode fazer referência a esse objeto diretamente:

```csharp
double value = slider.Value;
```

### <a name="data-binding-the-slider"></a>O controle deslizante de associação de dados

O **básica associações de controle deslizante** página mostra como escrever um programa quase equivalente que elimina o `Value` manipulador de eventos usando [associação de dados](~/xamarin-forms/app-fundamentals/data-binding/index.md):

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="SliderDemos.BasicSliderBindingsPage"
             Title="Basic Slider Bindings"
             Padding="10, 0">
    <StackLayout>
        <Label Text="ROTATING TEXT"
               Rotation="{Binding Source={x:Reference slider}, 
                                  Path=Value}"
               FontSize="Large"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand" />

        <Slider x:Name="slider"
                Maximum="360" />

        <Label x:Name="displayLabel"
               Text="{Binding Source={x:Reference slider}, 
                              Path=Value, 
                              StringFormat='The Slider value is {0:F0}'}"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand" />
    </StackLayout>
</ContentPage>
```

O `Rotation` propriedade do primeiro `Label` está associada ao `Value` propriedade do `Slider`, pois é o `Text` propriedade da segunda `Label` com um `StringFormat` especificação. O **básica associações de controle deslizante** funções da página um pouco diferente das duas páginas anteriores: quando a página é exibida pela primeira vez, a segunda `Label` exibe a cadeia de caracteres de texto com o valor. Essa é uma vantagem de usar a associação de dados. Para exibir texto sem associação de dados, você precisa inicializar especificamente o `Text` propriedade o `Label` ou simular um acionamento do `ValueChanged` evento chamando o manipulador de eventos a partir do construtor de classe.

<a name="precautions" />

## <a name="precautions"></a>Precauções

O valor de `Minimum` propriedade sempre deve ser menor que o valor da `Maximum` propriedade. O código a seguir faz com que o trecho de código a `Slider` para gerar uma exceção:

```csharp
// Throws an exception!
Slider slider = new Slider
{
    Minimum = 10,
    Maximum = 20
};
```

O compilador c# gera código que define essas duas propriedades em sequência, e quando o `Minimum` está definida como 10, é maior que o padrão `Maximum` valor de 1. Você pode evitar a exceção nesse caso, definindo o `Maximum` propriedade primeiro:

```csharp
Slider slider = new Slider
{
    Maximum = 20,
    Minimum = 10
};
```

Configuração `Maximum` a 20 não é um problema porque ele é maior que o padrão `Minimum` configuração de 0. Quando `Minimum` está definida, o valor é menor do que o `Maximum` valor de 20.

O mesmo problema existe em XAML. Definir as propriedades em uma ordem que garante que `Maximum` sempre é maior do que `Minimum`:

```xaml
<Slider Maximum="20"
        Minimum="10" ... />
```

Você pode definir o `Minimum` e `Maximum` valores para números negativos, mas apenas em uma ordem onde `Minimum` é sempre menor que `Maximum`:

```xaml
<Slider Minimum="-20"
        Maximum="-10" ... />
```

O `Value` propriedade é sempre maior que ou igual a `Minimum` valor e menor ou igual a `Maximum`. Se `Value` for definido como um valor fora do intervalo, o valor ser forçado para ficar dentro do intervalo, mas nenhuma exceção é gerada. Por exemplo, esse código vai *não* gerar uma exceção:

```csharp
Slider slider = new Slider
{
    Value = 10
};
```

Em vez disso, o `Value` propriedade é forçada para o `Maximum` valor de 1.

Aqui está um trecho de código mostrado acima: 

```csharp
Slider slider = new Slider
{
    Maximum = 20,
    Minimum = 10
};
```

Quando `Minimum` é definido como 10, em seguida, `Value` também é definido como 10. 

Se um `ValueChanged` manipulador de eventos foi anexado no momento em que o `Value` propriedade é forçada para algo diferente de seu valor padrão de 0, um `ValueChanged` evento é acionado. Aqui está um trecho do XAML: 

```xaml
<Slider ValueChanged="OnSliderValueChanged"
        Maximum="20"
        Minimum="10" />
```

Quando `Minimum` é definido como 10, `Value` também é definido como 10 e o `ValueChanged` evento é acionado. Isso pode ocorrer antes que o restante da página foi construído, e o manipulador pode tentar fazer referência a outros elementos na página que ainda não foi criados. Talvez você queira adicionar código para o `ValueChanged` manipulador verifica `null` valores de outros elementos na página. Ou, você pode definir o `ValueChanged` manipulador de eventos após o `Slider` valores foram inicializados.

<a name="implementations" />

## <a name="platform-implementation-differences"></a>Diferenças de implementação de plataforma

As capturas de tela mostradas anteriormente exibem o valor da `Slider` com um número diferente de pontos decimais. Isso se refere a como o `Slider` é implementado nas plataformas Android e UWP.

### <a name="the-android-implementation"></a>A implementação do Android 

A implementação do Android do `Slider` se baseia o Android [ `SeekBar` ](https://developer.xamarin.com/api/type/Android.Widget.SeekBar/) e sempre define o [ `Max` ](https://developer.xamarin.com/api/property/Android.Widget.ProgressBar.Max/) propriedade a 1000. Isso significa que o `Slider` no Android tem apenas 1.001 valores discretos. Se você definir o `Slider` ter um `Minimum` 0 e um `Maximum` de 5000, em seguida, como o `Slider` é manipulado, o `Value` propriedade tem valores de 0, 5, 10, 15 e assim por diante. 

### <a name="the-uwp-implementation"></a>A implementação de UWP

A implementação de UWP do `Slider` baseia-se a UWP [ `Slider` ](/uwp/api/windows.ui.xaml.controls.slider) controle. O `StepFrequency` propriedade o UWP `Slider` é definido como a diferença entre o `Maximum` e `Minimum` propriedades dividido por 10, mas não é maior que 1. 

Por exemplo, para o intervalo padrão de 0 a 1, o `StepFrequency` está definida como 0.1. Como o `Slider` é manipulado, o `Value` propriedade é restrita a 0, 0.1, 0.2, 0.3, 0,4, 0,5, 0,6, 0,7, 0,8, 0,9 e 1.0. (Isso fica evidente na última página do [ **SliderDemos** ](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/SliderDemos) exemplo.) Quando a diferença entre o `Maximum` e `Minimum` propriedades é 10 ou maior, em seguida, `StepFrequency` é definido como 1 e o `Value` propriedade tem valores integrais. 

### <a name="the-stepslider-solution"></a>A solução StepSlider

Mais versátil `StepSlider` é abordado em [Capítulo 27. Processadores personalizados](https://xamarin.azureedge.net/developer/xamarin-forms-book/XamarinFormsBook-Ch27-Apr2016.pdf) do catálogo de *criação de aplicativos móveis com o xamarin. Forms*. O `StepSlider` é semelhante a `Slider` , mas adiciona um `Steps` propriedade para especificar o número de valores entre `Minimum` e `Maximum`.

## <a name="sliders-for-color-selection"></a>Controles deslizantes para seleção de cor

O último duas páginas o [ **SliderDemos** ](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/SliderDemos) exemplo usam três `Slider` instâncias para seleção de cor. A primeira página trata todas as interações no arquivo code-behind, enquanto a segunda página mostra como usar a associação de dados com um ViewModel. 

### <a name="handling-sliders-in-the-code-behind-file"></a>Controles deslizantes no arquivo code-behind de tratamento 

O **seletores de cor RGB** página instancia um `BoxView` para exibir uma cor, três `Slider` instâncias para selecionar os componentes vermelhos, verdes e azuis da cor e três `Label` elementos para exibir essas cores valores:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="SliderDemos.RgbColorSlidersPage"
             Title="RGB Color Sliders">
    <ContentPage.Resources>
        <ResourceDictionary>
            <Style TargetType="Slider">
                <Setter Property="Maximum" Value="255" />
            </Style>
            
            <Style TargetType="Label">
                <Setter Property="HorizontalTextAlignment" Value="Center" />
            </Style>
        </ResourceDictionary>
    </ContentPage.Resources>

    <StackLayout Margin="10">
        <BoxView x:Name="boxView"
                 Color="Black"
                 VerticalOptions="FillAndExpand" />

        <Slider x:Name="redSlider"
                ValueChanged="OnSliderValueChanged" />

        <Label x:Name="redLabel" />

        <Slider x:Name="greenSlider" 
                ValueChanged="OnSliderValueChanged" />

        <Label x:Name="greenLabel" />

        <Slider x:Name="blueSlider" 
                ValueChanged="OnSliderValueChanged" />

        <Label x:Name="blueLabel" />
    </StackLayout>
</ContentPage>
```

Um `Style` fornece todos os três `Slider` elementos de um intervalo de 0 a 255. O `Slider` elementos compartilham o mesmo `ValueChanged` manipulador, que é implementado no arquivo code-behind:

```csharp
public partial class RgbColorSlidersPage : ContentPage
{
    public RgbColorSlidersPage()
    {
        InitializeComponent();
    }

    void OnSliderValueChanged(object sender, ValueChangedEventArgs args)
    {
        if (sender == redSlider)
        {
            redLabel.Text = String.Format("Red = {0:X2}", (int)args.NewValue);
        }
        else if (sender == greenSlider)
        {
            greenLabel.Text = String.Format("Green = {0:X2}", (int)args.NewValue);
        }
        else if (sender == blueSlider)
        {
            blueLabel.Text = String.Format("Blue = {0:X2}", (int)args.NewValue);
        }

        boxView.Color = Color.FromRgb((int)redSlider.Value,
                                      (int)greenSlider.Value,
                                      (int)blueSlider.Value);
    }
}
```

Os conjuntos de seção primeiro o `Text` propriedade de uma da `Label` instâncias de uma cadeia de caracteres de texto curto que indica o valor da `Slider` em hexadecimal. Em seguida, todos os três `Slider` instâncias são acessadas para criar um `Color` valor dos componentes RGB:

[![Seletores de cor RGB](slider-images/RgbColorSliders.png "seletores de cor RGB")](slider-images/RgbColorSliders-Large.png#lightbox)

### <a name="binding-the-slider-to-a-viewmodel"></a>O controle deslizante de associação para um ViewModel

O **seletores de cor HSL** página mostra como usar um ViewModel para executar os cálculos usados para criar um `Color` valor de valores de matiz, saturação e luminosidade. Como todos os ViewModels, o `HSLColorViewModel` classe implementa o `INotifyPropertyChanged` interface e aciona um `PropertyChanged` evento sempre que uma das propriedades de alterações:

```csharp
public class HslColorViewModel : INotifyPropertyChanged
{
    Color color;

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
            }
        }
        get
        {
            return color;
        }
    }
}
```

ViewModels e `INotifyPropertyChanged` interface são discutidos no artigo [associação de dados](~/xamarin-forms/app-fundamentals/data-binding/index.md).

O **HslColorSlidersPage.xaml** arquivo instancia o `HslColorViewModel` e define-o para a página `BindingContext` propriedade. Isso permite que todos os elementos no arquivo XAML para vincular a propriedades no ViewModel:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:SliderDemos"
             x:Class="SliderDemos.HslColorSlidersPage"
             Title="HSL Color Sliders">

    <ContentPage.BindingContext>
        <local:HslColorViewModel Color="Chocolate" />
    </ContentPage.BindingContext>

    <ContentPage.Resources>
        <ResourceDictionary>
            <Style TargetType="Label">
                <Setter Property="HorizontalTextAlignment" Value="Center" />
            </Style>
        </ResourceDictionary>
    </ContentPage.Resources>

    <StackLayout Margin="10">
        <BoxView Color="{Binding Color}"
                 VerticalOptions="FillAndExpand" />

        <Slider Value="{Binding Hue}" />
        <Label Text="{Binding Hue, StringFormat='Hue = {0:F2}'}" />

        <Slider Value="{Binding Saturation}" />
        <Label Text="{Binding Saturation, StringFormat='Saturation = {0:F2}'}" />

        <Slider Value="{Binding Luminosity}" />
        <Label Text="{Binding Luminosity, StringFormat='Luminosity = {0:F2}'}" />
    </StackLayout>
</ContentPage> 
```

Como o `Slider` elementos são manipulados, o `BoxView` e `Label` elementos são atualizados no ViewModel:

[![Controles deslizantes HSL](slider-images/HslColorSliders.png "HSL controles deslizantes")](slider-images/HslColorSliders-Large.png#lightbox)

O `StringFormat` componente do `Binding` extensão de marcação é definida para um formato de "F2" para exibir duas casas decimais. (Cadeia de caracteres de formatação em associações de dados é discutida no artigo [cadeia de caracteres de formatação](~/xamarin-forms/app-fundamentals/data-binding/string-formatting.md).) No entanto, a versão UWP do programa é limitada a valores de 0, 0.1, 0.2,... 0.9 e 1.0. Este é um resultado direto da implementação do UWP `Slider` conforme descrito acima na seção [diferenças de implementação de plataforma](#implementations).

## <a name="related-links"></a>Links relacionados

- [Exemplo de demonstrações do controle deslizante](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/SliderDemos)
- [Controle deslizante de API](https://developer.xamarin.com/api/type/Xamarin.Forms.Slider/)
