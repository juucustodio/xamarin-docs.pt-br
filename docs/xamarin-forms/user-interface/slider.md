---
title: Xamarin.FormsClassificação
description: O Xamarin.Forms controle deslizante é uma barra horizontal que pode ser manipulada pelo usuário para selecionar um valor duplo de um intervalo contínuo. Este artigo explica como usar a classe Slider para selecionar um valor de um intervalo de valores contínuos.
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 1cde999e6781f019b6abceee82caf259e1e5a710
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/28/2020
ms.locfileid: "84140147"
---
# <a name="xamarinforms-slider"></a>Xamarin.FormsClassificação

[![Baixar exemplo ](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-sliderdemos)

_Use um controle deslizante para selecionar um intervalo de valores contínuos._

O Xamarin.Forms [`Slider`](xref:Xamarin.Forms.Slider) é uma barra horizontal que pode ser manipulada pelo usuário para selecionar um `double` valor de um intervalo contínuo.

O `Slider` define três propriedades do tipo `double` :

- [`Minimum`](xref:Xamarin.Forms.Slider.Minimum)é o mínimo do intervalo, com um valor padrão de 0.
- [`Maximum`](xref:Xamarin.Forms.Slider.Maximum)é o máximo do intervalo, com um valor padrão de 1.
- [`Value`](xref:Xamarin.Forms.Slider.Value)é o valor do controle deslizante, que pode variar entre `Minimum` e `Maximum` e tem um valor padrão de 0.

Todas as três propriedades são apoiadas por `BindableProperty` objetos. A `Value` propriedade tem um modo de associação padrão de `BindingMode.TwoWay` , o que significa que ele é adequado como uma fonte de associação em um aplicativo que usa a arquitetura [MVVM (Model-View-ViewModel)](~/xamarin-forms/enterprise-application-patterns/mvvm.md) .

> [!WARNING]
> Internamente, o `Slider` garante que `Minimum` seja menor que `Maximum` . Se `Minimum` ou `Maximum` já estiverem definidos para que `Minimum` não seja menor que `Maximum` , uma exceção será gerada. Consulte a seção [**precauções**](#precautions) abaixo para obter mais informações sobre como definir as `Minimum` `Maximum` Propriedades e.

O `Slider` impõe a `Value` propriedade para que ela fique entre `Minimum` e `Maximum` , inclusive. Se a `Minimum` propriedade for definida como um valor maior que a `Value` propriedade, o `Slider` definirá a `Value` propriedade como `Minimum` . Da mesma forma, se `Maximum` for definido como um valor menor que `Value` , `Slider` o definirá a `Value` propriedade como `Maximum` .

`Slider`define um [`ValueChanged`](xref:Xamarin.Forms.Slider.ValueChanged) evento que é acionado quando as `Value` alterações são feitas por meio da manipulação de usuário do `Slider` ou quando o programa define a `Value` propriedade diretamente. Um `ValueChanged` evento também é acionado quando a `Value` propriedade é conforçada, conforme descrito no parágrafo anterior.

O [`ValueChangedEventArgs`](xref:Xamarin.Forms.ValueChangedEventArgs) objeto que acompanha o `ValueChanged` evento tem duas propriedades, ambas do tipo `double` : [`OldValue`](xref:Xamarin.Forms.ValueChangedEventArgs.OldValue) e [`NewValue`](xref:Xamarin.Forms.ValueChangedEventArgs.NewValue) . No momento em que o evento é acionado, o valor de `NewValue` é igual à `Value` Propriedade do `Slider` objeto.

`Slider`também define `DragStarted` e `DragCompleted` eventos, que são acionados no início e no final da ação de arrastar. Ao contrário do [`ValueChanged`](xref:Xamarin.Forms.Slider.ValueChanged) evento, os `DragStarted` eventos e são disparados `DragCompleted` apenas por meio da manipulação de usuário do `Slider` . Quando o `DragStarted` evento é acionado, o `DragStartedCommand` , do tipo `ICommand` , é executado. Da mesma forma, quando o `DragCompleted` evento é acionado, o `DragCompletedCommand` , do tipo `ICommand` , é executado.

> [!WARNING]
> Não use opções de layout horizontal irrestrito de `Center` , `Start` ou `End` com `Slider` . No Android e no UWP, o `Slider` colapso para uma barra de comprimento zero e no Ios, a barra é muito curta. Mantenha a `HorizontalOptions` configuração padrão de `Fill` e não use uma largura de `Auto` quando colocar `Slider` em um `Grid` layout.

O `Slider` também define várias propriedades que afetam sua aparência:

- [`MinimumTrackColor`](xref:Xamarin.Forms.Slider.MinimumTrackColorProperty)é a cor da barra no lado esquerdo do polegar.
- [`MaximumTrackColor`](xref:Xamarin.Forms.Slider.MaximumTrackColorProperty)é a cor da barra no lado direito do polegar.
- [`ThumbColor`](xref:Xamarin.Forms.Slider.ThumbColorProperty)é a cor da miniatura.
- [`ThumbImageSource`](xref:Xamarin.Forms.Slider.ThumbImageSourceProperty)é a imagem a ser usada para o Thumb, do tipo [`ImageSource`](xref:Xamarin.Forms.ImageSource) .

> [!NOTE]
> As `ThumbColor` `ThumbImageSource` Propriedades e são mutuamente exclusivas. Se ambas as propriedades forem definidas, a `ThumbImageSource` Propriedade terá precedência.

## <a name="basic-slider-code-and-markup"></a>Código e marcação do controle deslizante básico

O exemplo [**SliderDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-sliderdemos) começa com três páginas que são funcionalmente idênticas, mas são implementadas de maneiras diferentes. A primeira página usa apenas código C#, a segunda usa XAML com um manipulador de eventos no código e a terceira é capaz de evitar o manipulador de eventos usando a vinculação de dados no arquivo XAML.

### <a name="creating-a-slider-in-code"></a>Criando um controle deslizante no código

A página de **código do controle deslizante básico** no exemplo [**SliderDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-sliderdemos) mostra mostrar para criar um `Slider` e dois `Label` objetos no código:

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

O `Slider` é inicializado para ter uma `Maximum` propriedade de 360. O `ValueChanged` manipulador do `Slider` usa a `Value` Propriedade do `slider` objeto para definir a `Rotation` Propriedade do primeiro `Label` e usa o `String.Format` método com a `NewValue` propriedade dos argumentos do evento para definir a `Text` Propriedade do segundo `Label` . Essas duas abordagens para obter o valor atual de `Slider` são intercambiáveis.

Este é o programa em execução em dispositivos Android e iOS:

[![Código do controle deslizante básico](slider-images/BasicSliderCode.png "Código do controle deslizante básico")](slider-images/BasicSliderCode-Large.png#lightbox)

O segundo `Label` exibe o texto "(não inicializado)" até que o `Slider` seja manipulado, o que faz com que o primeiro `ValueChanged` evento seja acionado. Observe que o número de casas decimais exibidas é diferente para cada plataforma. Essas diferenças estão relacionadas às implementações de plataforma do `Slider` e são discutidas posteriormente neste artigo na seção [diferenças de implementação de plataforma](#implementations).

### <a name="creating-a-slider-in-xaml"></a>Criando um controle deslizante em XAML

A página **XAML básica do controle deslizante** é funcionalmente a mesma do **código Slider básico** , mas implementada principalmente em XAML:

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

Também é possível que o manipulador de eventos obtenha o `Slider` que está acionando o evento por meio do `sender` argumento. A `Value` propriedade contém o valor atual:

```csharp
double value = ((Slider)sender).Value;
```

Se o `Slider` objeto recebeu um nome no arquivo XAML com um `x:Name` atributo (por exemplo, "slider"), o manipulador de eventos poderá fazer referência a esse objeto diretamente:

```csharp
double value = slider.Value;
```

### <a name="data-binding-the-slider"></a>Vinculação de dados ao controle deslizante

A página **associações de controle deslizante básico** mostra como escrever um programa quase equivalente que elimina o `Value` manipulador de eventos usando a vinculação de [dados](~/xamarin-forms/app-fundamentals/data-binding/index.md):

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

A `Rotation` propriedade da primeira `Label` é associada à `Value` propriedade de `Slider` , como é a `Text` Propriedade do segundo `Label` com uma `StringFormat` especificação. O **controle deslizante básico vincula** funções de página um pouco diferente das duas páginas anteriores: quando a página é exibida pela primeira vez, a segunda `Label` exibe a cadeia de caracteres de texto com o valor. Essa é uma vantagem de usar a associação de dados. Para exibir texto sem Associação de dados, você precisaria inicializar especificamente a `Text` Propriedade do `Label` ou simular um acionamento do `ValueChanged` evento chamando o manipulador de eventos do construtor da classe.

<a name="precautions" />

## <a name="precautions"></a>Tomar

O valor da `Minimum` propriedade sempre deve ser menor que o valor da `Maximum` propriedade. O trecho de código a seguir faz com que o `Slider` gere uma exceção:

```csharp
// Throws an exception!
Slider slider = new Slider
{
    Minimum = 10,
    Maximum = 20
};
```

O compilador C# gera um código que define essas duas propriedades em sequência e quando a `Minimum` propriedade é definida como 10, ela é maior que o `Maximum` valor padrão 1. Você pode evitar a exceção nesse caso definindo a `Maximum` Propriedade primeiro:

```csharp
Slider slider = new Slider
{
    Maximum = 20,
    Minimum = 10
};
```

`Maximum`A configuração para 20 não é um problema porque é maior que o `Minimum` valor padrão de 0. Quando `Minimum` é definido, o valor é menor que o `Maximum` valor de 20.

O mesmo problema existe em XAML. Defina as propriedades em uma ordem que garanta que `Maximum` sempre seja maior que `Minimum` :

```xaml
<Slider Maximum="20"
        Minimum="10" ... />
```

Você pode definir os `Minimum` `Maximum` valores e como números negativos, mas apenas em uma ordem em que `Minimum` sempre é menor que `Maximum` :

```xaml
<Slider Minimum="-20"
        Maximum="-10" ... />
```

A `Value` propriedade é sempre maior ou igual ao `Minimum` valor e menor ou igual a `Maximum` . Se `Value` for definido como um valor fora desse intervalo, o valor será forçado a estar dentro do intervalo, mas nenhuma exceção será gerada. Por exemplo, esse código *não* gerará uma exceção:

```csharp
Slider slider = new Slider
{
    Value = 10
};
```

Em vez disso, a `Value` propriedade é forçada ao `Maximum` valor de 1.

Aqui está um trecho de código mostrado acima:

```csharp
Slider slider = new Slider
{
    Maximum = 20,
    Minimum = 10
};
```

Quando `Minimum` é definido como 10, `Value` também é definido como 10.

Se um `ValueChanged` manipulador de eventos tiver sido anexado no momento em que a `Value` propriedade for forçada a algo diferente do valor padrão 0, um `ValueChanged` evento será acionado. Aqui está um trecho de código XAML:

```xaml
<Slider ValueChanged="OnSliderValueChanged"
        Maximum="20"
        Minimum="10" />
```

Quando `Minimum` é definido como 10, `Value` também é definido como 10 e o `ValueChanged` evento é acionado. Isso pode ocorrer antes que o restante da página tenha sido construído e o manipulador possa tentar fazer referência a outros elementos na página que ainda não foram criados. Talvez você queira adicionar algum código ao `ValueChanged` manipulador que verifica os `null` valores de outros elementos na página. Ou, você pode definir o `ValueChanged` manipulador de eventos depois que os `Slider` valores tiverem sido inicializados.

<a name="implementations" />

## <a name="platform-implementation-differences"></a>Diferenças de implementação de plataforma

As capturas de tela mostradas anteriormente exibem o valor de `Slider` com um número diferente de pontos decimais. Isso se relaciona com o modo como o `Slider` é implementado nas plataformas Android e UWP.

### <a name="the-android-implementation"></a>A implementação do Android

A implementação do Android do `Slider` é baseada no Android [`SeekBar`](xref:Android.Widget.SeekBar) e sempre define a [`Max`](xref:Android.Widget.ProgressBar.Max) propriedade como 1000. Isso significa que o `Slider` no Android tem apenas 1.001 valores discretos. Se você definir o `Slider` para ter um `Minimum` de 0 e um `Maximum` de 5000, assim que o `Slider` for manipulado, a `Value` Propriedade terá valores de 0, 5, 10, 15 e assim por diante.

### <a name="the-uwp-implementation"></a>A implementação de UWP

A implementação de UWP do `Slider` é baseada no [`Slider`](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.slider) controle UWP. A `StepFrequency` propriedade de UWP `Slider` é definida como a diferença das `Maximum` `Minimum` Propriedades e dividida por 10, mas não é maior que 1.

Por exemplo, para o intervalo padrão de 0 a 1, a `StepFrequency` propriedade é definida como 0,1. Como o `Slider` é manipulado, a `Value` propriedade é restrita a 0, 0,1, 0,2, 0,3, 0,4, 0,5, 0,6, 0,7, 0,8, 0,9 e 1,0. (Isso é evidente na última página do exemplo de [**SliderDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-sliderdemos) .) Quando a diferença entre as `Maximum` `Minimum` Propriedades e é 10 ou maior, `StepFrequency` é definida como 1 e a `Value` propriedade tem valores integrais.

### <a name="the-stepslider-solution"></a>A solução StepSlider

Um mais versátil `StepSlider` é discutido no [capítulo 27. Renderizadores personalizados](https://xamarin.azureedge.net/developer/xamarin-forms-book/XamarinFormsBook-Ch27-Apr2016.pdf) do livro *criando aplicativos móveis com Xamarin.Forms *o. O `StepSlider` é semelhante a `Slider` , mas adiciona uma `Steps` propriedade para especificar o número de valores entre `Minimum` e `Maximum` .

## <a name="sliders-for-color-selection"></a>Controles deslizantes para seleção de cores

As duas páginas finais no exemplo [**SliderDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-sliderdemos) usam três `Slider` instâncias para seleção de cores. A primeira página manipula todas as interações no arquivo code-behind, enquanto a segunda página mostra como usar a vinculação de dados com um ViewModel.

### <a name="handling-sliders-in-the-code-behind-file"></a>Manipulando controles deslizantes no arquivo code-behind

A página de **controles deslizantes de cor RGB** instancia um `BoxView` para exibir uma cor, três `Slider` instâncias para selecionar os componentes vermelho, verde e azul da cor e três `Label` elementos para exibir esses valores de cor:

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

Um `Style` dá a todos os três `Slider` elementos um intervalo de 0 a 255. Os `Slider` elementos compartilham o mesmo `ValueChanged` manipulador, que é implementado no arquivo code-behind:

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

A primeira seção define a `Text` propriedade de uma das `Label` instâncias para uma cadeia de caracteres de texto curto que indica o valor de `Slider` em hexadecimal. Em seguida, todas as três `Slider` instâncias são acessadas para criar um `Color` valor dos componentes RGB:

[![Controles deslizantes de cor RGB](slider-images/RgbColorSliders.png "Controles deslizantes de cor RGB")](slider-images/RgbColorSliders-Large.png#lightbox)

### <a name="binding-the-slider-to-a-viewmodel"></a>Ligando o controle deslizante a um ViewModel

A página **deslizantes de cor HSL** mostra como usar um ViewModel para executar os cálculos usados para criar um `Color` valor de matiz, saturação e valores de luminosidade. Assim como todos os ViewModels, a `HSLColorViewModel` classe implementa a `INotifyPropertyChanged` interface e dispara um `PropertyChanged` evento sempre que uma das propriedades é alterada:

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

ViewModels e a `INotifyPropertyChanged` interface são discutidos no artigo [vinculação de dados](~/xamarin-forms/app-fundamentals/data-binding/index.md).

O arquivo **HslColorSlidersPage. XAML** instancia o `HslColorViewModel` e o define como a propriedade da página `BindingContext` . Isso permite que todos os elementos no arquivo XAML se associem às propriedades no ViewModel:

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

Como os `Slider` elementos são manipulados, os `BoxView` `Label` elementos e são atualizados a partir do ViewModel:

[![Controles deslizantes de cor HSL](slider-images/HslColorSliders.png "Controles deslizantes de cor HSL")](slider-images/HslColorSliders-Large.png#lightbox)

O `StringFormat` componente da `Binding` extensão de marcação é definido para um formato "F2" para exibir duas casas decimais. (A formatação de cadeia de caracteres nas associações de dados é discutida na [formatação da cadeia de caracteres](~/xamarin-forms/app-fundamentals/data-binding/string-formatting.md)do artigo.) No entanto, a versão UWP do programa está limitada a valores de 0, 0,1, 0,2,... 0,9 e 1,0. Esse é um resultado direto da implementação do UWP `Slider` , conforme descrito acima na seção diferenças de [implementação de plataforma](#implementations).

## <a name="related-links"></a>Links relacionados

- [Exemplo de demonstrações de controle deslizante](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-sliderdemos)
- [API Slider](xref:Xamarin.Forms.Slider)
