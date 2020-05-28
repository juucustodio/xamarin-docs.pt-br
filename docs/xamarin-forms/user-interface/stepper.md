---
title: Xamarin.FormsStepper
description: O Xamarin.Forms stepper permite que um usuário selecione um valor numérico de um intervalo de valores. Ele consiste em dois botões rotulados com sinais de menos e mais. A manipulação dos dois botões altera o valor selecionado de forma incremental.
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 4f071530fb17de44d8ede786ca1b42f5e11f4f7c
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/28/2020
ms.locfileid: "84130540"
---
# <a name="xamarinforms-stepper"></a>Xamarin.FormsStepper

[![Baixar exemplo ](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-stepperdemos)

_Use um stepper para selecionar um valor numérico de um intervalo de valores._

O Xamarin.Forms [`Stepper`](xref:Xamarin.Forms.Stepper) consiste em dois botões rotulados com sinais de menos e mais. Esses botões podem ser manipulados pelo usuário para selecionar incrementalmente um `double` valor de um intervalo de valores.

O [`Stepper`](xref:Xamarin.Forms.Stepper) define quatro propriedades do tipo `double` :

- [`Increment`](xref:Xamarin.Forms.Stepper.Increment)é a quantidade pela qual alterar o valor selecionado, com um valor padrão de 1.
- [`Minimum`](xref:Xamarin.Forms.Stepper.Minimum)é o mínimo do intervalo, com um valor padrão de 0.
- [`Maximum`](xref:Xamarin.Forms.Stepper.Maximum)é o máximo do intervalo, com um valor padrão de 100.
- [`Value`](xref:Xamarin.Forms.Stepper.Value)é o valor de stepper, que pode variar entre `Minimum` e `Maximum` e tem um valor padrão de 0.

Todas essas propriedades são apoiadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos. A [`Value`](xref:Xamarin.Forms.Stepper.Value) propriedade tem um modo de associação padrão de [`BindingMode.TwoWay`](xref:Xamarin.Forms.BindingMode.TwoWay) , o que significa que ele é adequado como uma fonte de associação em um aplicativo que usa a arquitetura [MVVM (Model-View-ViewModel)](~/xamarin-forms/enterprise-application-patterns/mvvm.md) .

> [!WARNING]
> Internamente, o [`Stepper`](xref:Xamarin.Forms.Stepper) garante que [`Minimum`](xref:Xamarin.Forms.Stepper.Minimum) seja menor que [`Maximum`](xref:Xamarin.Forms.Stepper.Maximum) . Se `Minimum` ou `Maximum` já estiverem definidos para que `Minimum` não seja menor que `Maximum` , uma exceção será gerada. Para obter mais informações sobre como definir as `Minimum` `Maximum` Propriedades e, consulte a seção [precauções](#precautions) .

O [`Stepper`](xref:Xamarin.Forms.Stepper) impõe a [`Value`](xref:Xamarin.Forms.Stepper.Value) propriedade para que ela fique entre [`Minimum`](xref:Xamarin.Forms.Stepper.Minimum) e [`Maximum`](xref:Xamarin.Forms.Stepper.Maximum) , inclusive. Se a `Minimum` propriedade for definida como um valor maior que a `Value` propriedade, o `Stepper` definirá a `Value` propriedade como `Minimum` . Da mesma forma, se `Maximum` for definido como um valor menor que `Value` , `Stepper` o definirá a `Value` propriedade como `Maximum` .

[`Stepper`](xref:Xamarin.Forms.Stepper)define um [`ValueChanged`](xref:Xamarin.Forms.Stepper.ValueChanged) evento que é acionado quando as [`Value`](xref:Xamarin.Forms.Stepper.Value) alterações são feitas por meio da manipulação de usuário do `Stepper` ou quando o aplicativo define a `Value` propriedade diretamente. Um `ValueChanged` evento também é acionado quando a `Value` propriedade é conforçada, conforme descrito no parágrafo anterior.

O [`ValueChangedEventArgs`](xref:Xamarin.Forms.ValueChangedEventArgs) objeto que acompanha o [`ValueChanged`](xref:Xamarin.Forms.Stepper.ValueChanged) evento tem duas propriedades, ambas do tipo `double` : [`OldValue`](xref:Xamarin.Forms.ValueChangedEventArgs.OldValue) e [`NewValue`](xref:Xamarin.Forms.ValueChangedEventArgs.NewValue) . No momento em que o evento é acionado, o valor de `NewValue` é igual à [`Value`](xref:Xamarin.Forms.Stepper.Value) Propriedade do [`Stepper`](xref:Xamarin.Forms.Stepper) objeto.

## <a name="basic-stepper-code-and-markup"></a>Código e marcação do stepper básico

O exemplo [**StepperDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-stepperdemos) contém três páginas que são funcionalmente idênticas, mas são implementadas de maneiras diferentes. A primeira página usa apenas código C#, a segunda usa XAML com um manipulador de eventos no código e a terceira é capaz de evitar o manipulador de eventos usando a vinculação de dados no arquivo XAML.

### <a name="creating-a-stepper-in-code"></a>Criando um stepper no código

A página de **código stepper básica** no exemplo de [**StepperDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-stepperdemos) mostra como criar um [`Stepper`](xref:Xamarin.Forms.Stepper) e dois [`Label`](xref:Xamarin.Forms.Label) objetos no código:

```csharp
public class BasicStepperCodePage : ContentPage
{
    public BasicStepperCodePage()
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

        Stepper stepper = new Stepper
        {
            Maximum = 360,
            Increment = 30,
            HorizontalOptions = LayoutOptions.Center
        };
        stepper.ValueChanged += (sender, e) =>
        {
            rotationLabel.Rotation = stepper.Value;
            displayLabel.Text = string.Format("The Stepper value is {0}", e.NewValue);
        };

        Title = "Basic Stepper Code";
        Content = new StackLayout
        {
            Margin = new Thickness(20),
            Children = { rotationLabel, stepper, displayLabel }
        };
    }
}
```

O [`Stepper`](xref:Xamarin.Forms.Stepper) é inicializado para ter uma [`Maximum`](xref:Xamarin.Forms.Stepper.Maximum) propriedade de 360 e uma [`Increment`](xref:Xamarin.Forms.Stepper.Increment) propriedade de 30. Manipular as `Stepper` alterações do valor selecionado incrementalmente entre [`Minimum`](xref:Xamarin.Forms.Stepper.Minimum) `Maximum` o com base no valor da `Increment` propriedade. O [`ValueChanged`](xref:Xamarin.Forms.Stepper.ValueChanged) manipulador do `Stepper` usa a [`Value`](xref:Xamarin.Forms.Stepper.Value) Propriedade do `stepper` objeto para definir a [`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation) Propriedade do primeiro [`Label`](xref:Xamarin.Forms.Label) e usa o `string.Format` método com a `NewValue` propriedade dos argumentos do evento para definir a [`Text`](xref:Xamarin.Forms.Label.Text) Propriedade do segundo `Label` . Essas duas abordagens para obter o valor atual de `Stepper` são intercambiáveis.

As capturas de tela a seguir mostram a página de **código básica do stepper** :

[![Código stepper básico](stepper-images/basic-stepper-code.png "Código stepper básico")](stepper-images/basic-stepper-code-large.png#lightbox)

O segundo [`Label`](xref:Xamarin.Forms.Label) exibe o texto "(não inicializado)" até que o [`Stepper`](xref:Xamarin.Forms.Stepper) seja manipulado, o que faz com que o primeiro [`ValueChanged`](xref:Xamarin.Forms.Stepper.ValueChanged) evento seja acionado.

### <a name="creating-a-stepper-in-xaml"></a>Criando um stepper em XAML

A página **XAML básica do stepper** é funcionalmente o mesmo que o **código stepper básico** , mas implementada principalmente em XAML:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="StepperDemo.BasicStepperXAMLPage"
             Title="Basic Stepper XAML">
    <StackLayout Margin="20">
        <Label x:Name="_rotatingLabel"
               Text="ROTATING TEXT"
               FontSize="Large"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand" />
        <Stepper Maximum="360"
                 Increment="30"
                 HorizontalOptions="Center"
                 ValueChanged="OnStepperValueChanged" />
        <Label x:Name="_displayLabel"
               Text="(uninitialized)"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand" />        
    </StackLayout>
</ContentPage>
```

O arquivo code-behind contém o manipulador para o [`ValueChanged`](xref:Xamarin.Forms.Stepper.ValueChanged) evento:

```csharp
public partial class BasicStepperXAMLPage : ContentPage
{
    public BasicStepperXAMLPage()
    {
        InitializeComponent();
    }

    void OnStepperValueChanged(object sender, ValueChangedEventArgs e)
    {
        double value = e.NewValue;
        _rotatingLabel.Rotation = value;
        _displayLabel.Text = string.Format("The Stepper value is {0}", value);
    }
}
```

Também é possível que o manipulador de eventos obtenha o [`Stepper`](xref:Xamarin.Forms.Stepper) que está acionando o evento por meio do `sender` argumento. A [`Value`](xref:Xamarin.Forms.Stepper.Value) propriedade contém o valor atual:

```csharp
double value = ((Stepper)sender).Value;
```

Se o [`Stepper`](xref:Xamarin.Forms.Stepper) objeto recebeu um nome no arquivo XAML com um `x:Name` atributo (por exemplo, "stepper"), o manipulador de eventos poderá fazer referência a esse objeto diretamente:

```csharp
double value = stepper.Value;
```

### <a name="data-binding-the-stepper"></a>Vinculação de dados ao stepper

A página **ligações básicas do stepper** mostra como escrever um aplicativo quase equivalente que elimina o [`Value`](xref:Xamarin.Forms.Stepper.Value) manipulador de eventos usando a [vinculação de dados](~/xamarin-forms/app-fundamentals/data-binding/index.md):

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="StepperDemo.BasicStepperBindingsPage"
             Title="Basic Stepper Bindings">
    <StackLayout Margin="20">
        <Label Text="ROTATING TEXT"
               Rotation="{Binding Source={x:Reference _stepper}, Path=Value}"
               FontSize="Large"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand" />
        <Stepper x:Name="_stepper"
                 Maximum="360"
                 Increment="30"
                 HorizontalOptions="Center" />
        <Label Text="{Binding Source={x:Reference _stepper}, Path=Value, StringFormat='The Stepper value is {0:F0}'}"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand" />
    </StackLayout>
</ContentPage>
```

A [`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation) propriedade da primeira [`Label`](xref:Xamarin.Forms.Label) é associada à [`Value`](xref:Xamarin.Forms.Stepper.Value) propriedade de [`Stepper`](xref:Xamarin.Forms.Stepper) , como é a [`Text`](xref:Xamarin.Forms.Label.Text) Propriedade do segundo `Label` com uma `StringFormat` especificação. A página de **associações de stepper básica** funciona um pouco diferente das duas páginas anteriores: quando a página é exibida pela primeira vez, a segunda `Label` exibe a cadeia de caracteres de texto com o valor. Essa é uma vantagem de usar a associação de dados. Para exibir texto sem Associação de dados, você precisaria inicializar especificamente a `Text` Propriedade do `Label` ou simular um acionamento do [`ValueChanged`](xref:Xamarin.Forms.Stepper.ValueChanged) evento chamando o manipulador de eventos do construtor da classe.

## <a name="precautions"></a>Tomar

O valor da [`Minimum`](xref:Xamarin.Forms.Stepper.Minimum) propriedade sempre deve ser menor que o valor da [`Maximum`](xref:Xamarin.Forms.Stepper.Maximum) propriedade. O trecho de código a seguir faz com que o [`Stepper`](xref:Xamarin.Forms.Stepper) gere uma exceção:

```csharp
// Throws an exception!
Stepper stepper = new Stepper
{
    Minimum = 180,
    Maximum = 360
};
```

O compilador C# gera código que define essas duas propriedades em sequência e quando a [`Minimum`](xref:Xamarin.Forms.Stepper.Minimum) propriedade é definida como 180, ela é maior que o valor padrão [`Maximum`](xref:Xamarin.Forms.Stepper.Maximum) de 100. Você pode evitar a exceção nesse caso definindo a `Maximum` Propriedade primeiro:

```csharp
Stepper stepper = new Stepper
{
    Maximum = 360,
    Minimum = 180
};
```

[`Maximum`](xref:Xamarin.Forms.Stepper.Maximum)A configuração para 360 não é um problema porque é maior que o [`Minimum`](xref:Xamarin.Forms.Stepper.Minimum) valor padrão de 0. Quando `Minimum` é definido, o valor é menor que o `Maximum` valor de 360.

O mesmo problema existe em XAML. Defina as propriedades em uma ordem que garanta que [`Maximum`](xref:Xamarin.Forms.Stepper.Maximum) sempre seja maior que `Minimum` :

```xaml
<Stepper Maximum="360"
         Minimum="180" ... />
```

Você pode definir os [`Minimum`](xref:Xamarin.Forms.Stepper.Minimum) [`Maximum`](xref:Xamarin.Forms.Stepper.Maximum) valores e como números negativos, mas apenas em uma ordem em que `Minimum` sempre é menor que `Maximum` :

```xaml
<Stepper Minimum="-360"
         Maximum="-180" ... />
```

A [`Value`](xref:Xamarin.Forms.Stepper.Value) propriedade é sempre maior ou igual ao [`Minimum`](xref:Xamarin.Forms.Stepper.Minimum) valor e menor ou igual a [`Maximum`](xref:Xamarin.Forms.Stepper.Maximum) . Se `Value` for definido como um valor fora desse intervalo, o valor será forçado a estar dentro do intervalo, mas nenhuma exceção será gerada. Por exemplo, esse código *não* gerará uma exceção:

```csharp
Stepper stepper = new Stepper
{
    Value = 180
};
```

Em vez disso, a [`Value`](xref:Xamarin.Forms.Stepper.Value) propriedade é forçada ao [`Maximum`](xref:Xamarin.Forms.Stepper.Maximum) valor de 100.

Aqui está um trecho de código mostrado acima:

```csharp
Stepper stepper = new Stepper
{
    Maximum = 360,
    Minimum = 180
};
```

Quando [`Minimum`](xref:Xamarin.Forms.Stepper.Minimum) é definido como 180, [`Value`](xref:Xamarin.Forms.Stepper.Value) também é definido como 180.

Se um [`ValueChanged`](xref:Xamarin.Forms.Stepper.ValueChanged) manipulador de eventos tiver sido anexado no momento em que a [`Value`](xref:Xamarin.Forms.Stepper.Value) propriedade for forçada a algo diferente do valor padrão 0, um `ValueChanged` evento será acionado. Aqui está um trecho de código XAML:

```xaml
<Stepper ValueChanged="OnStepperValueChanged"
         Maximum="360"
         Minimum="180" />
```

Quando [`Minimum`](xref:Xamarin.Forms.Stepper.Minimum) é definido como 180, [`Value`](xref:Xamarin.Forms.Stepper.Value) também é definido como 180 e o [`ValueChanged`](xref:Xamarin.Forms.Stepper.ValueChanged) evento é acionado. Isso pode ocorrer antes que o restante da página tenha sido construído e o manipulador possa tentar fazer referência a outros elementos na página que ainda não foram criados. Talvez você queira adicionar algum código ao `ValueChanged` manipulador que verifica os `null` valores de outros elementos na página. Ou, você pode definir o `ValueChanged` manipulador de eventos depois que os [`Stepper`](xref:Xamarin.Forms.Stepper) valores tiverem sido inicializados.

## <a name="related-links"></a>Links relacionados

- [Exemplo de demonstrações do stepper](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-stepperdemos)
- [API stepper](xref:Xamarin.Forms.Stepper)
