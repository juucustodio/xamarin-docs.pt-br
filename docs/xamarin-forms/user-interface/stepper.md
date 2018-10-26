---
title: Seletor de xamarin. Forms
description: O seletor de xamarin. Forms permite que um usuário selecione um valor numérico de um intervalo de valores. Ele consiste em dois botões rotulados com sinais de adição e de subtração. Manipular os dois botões altera o valor selecionado incrementalmente.
ms.prod: xamarin
ms.assetid: 62571B3E-D84B-4F52-9FC7-C105D6733B16
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/17/2018
ms.openlocfilehash: 53485d0d948f31b69f74b6933d05c14f69fa64c0
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50111443"
---
# <a name="xamarinforms-stepper"></a>Seletor de xamarin. Forms

_Use um seletor para selecionar um valor numérico de um intervalo de valores._

O xamarin. Forms [ `Stepper` ](xref:Xamarin.Forms.Stepper) consiste em dois botões rotulados com sinais de adição e de subtração. Esses botões podem ser manipulados pelo usuário para selecionar incrementalmente uma `double` valor de um intervalo de valores.

O [ `Stepper` ](xref:Xamarin.Forms.Stepper) define quatro propriedades do tipo `double`:

- [`Increment`](xref:Xamarin.Forms.Stepper.Increment) é o valor para alterar o valor selecionado, com um valor padrão de 1.
- [`Minimum`](xref:Xamarin.Forms.Stepper.Minimum) é o mínimo do intervalo, com um valor padrão de 0.
- [`Maximum`](xref:Xamarin.Forms.Stepper.Maximum) é o máximo do intervalo, com um valor padrão de 100.
- [`Value`](xref:Xamarin.Forms.Stepper.Value) é o valor do seletor, o que pode variar entre `Minimum` e `Maximum` e tem um valor padrão de 0.

Todas essas propriedades têm o respaldo [ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty) objetos. O [ `Value` ](xref:Xamarin.Forms.Stepper.Value) propriedade tem um modo de associação padrão de [ `BindingMode.TwoWay` ](xref:Xamarin.Forms.BindingMode.TwoWay), que significa que ele é adequado como uma origem da associação em um aplicativo que usa o [ Model-View-ViewModel (MVVM)](~/xamarin-forms/enterprise-application-patterns/mvvm.md) arquitetura.

> [!WARNING]
> Internamente, o [ `Stepper` ](xref:Xamarin.Forms.Stepper) garante que [ `Minimum` ](xref:Xamarin.Forms.Stepper.Minimum) é menor que [ `Maximum` ](xref:Xamarin.Forms.Stepper.Maximum). Se `Minimum` ou `Maximum` nunca são definidas, de modo que `Minimum` é não é menor que `Maximum`, uma exceção será gerada. Para obter mais informações sobre como o `Minimum` e `Maximum` propriedades, consulte [precauções](#precautions) seção.

O [ `Stepper` ](xref:Xamarin.Forms.Stepper) impõe o [ `Value` ](xref:Xamarin.Forms.Stepper.Value) propriedade para que ele fique entre [ `Minimum` ](xref:Xamarin.Forms.Stepper.Minimum) e [ `Maximum` ](xref:Xamarin.Forms.Stepper.Maximum), inclusive. Se o `Minimum` propriedade é definida como um valor maior que o `Value` propriedade, o `Stepper` define o `Value` propriedade `Minimum`. Da mesma forma, se `Maximum` é definido como um valor menor que `Value`, em seguida, `Stepper` define o `Value` propriedade para `Maximum`.

[`Stepper`](xref:Xamarin.Forms.Stepper) define uma [ `ValueChanged` ](xref:Xamarin.Forms.Stepper.ValueChanged) evento que é disparado quando o [ `Value` ](xref:Xamarin.Forms.Stepper.Value) alterações, por meio da manipulação do usuário a `Stepper` ou quando o aplicativo define o `Value` propriedade diretamente. Um `ValueChanged` evento também é disparado quando o `Value` propriedade é forçada, conforme descrito no parágrafo anterior.

O [ `ValueChangedEventArgs` ](xref:Xamarin.Forms.ValueChangedEventArgs) objeto que acompanha o [ `ValueChanged` ](xref:Xamarin.Forms.Stepper.ValueChanged) evento tem duas propriedades, ambos do tipo `double`: [ `OldValue` ](xref:Xamarin.Forms.ValueChangedEventArgs.OldValue) e [ `NewValue`](xref:Xamarin.Forms.ValueChangedEventArgs.NewValue). No momento do evento é acionado, o valor de `NewValue` é igual a [ `Value` ](xref:Xamarin.Forms.Stepper.Value) propriedade do [ `Stepper` ](xref:Xamarin.Forms.Stepper) objeto.

## <a name="basic-stepper-code-and-markup"></a>Marcação e código de escalonador básico

O [ **StepperDemos** ](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/StepperDemos) exemplo contém três páginas que são funcionalmente idênticos, mas são implementadas de maneiras diferentes. A primeira página usa apenas C# código, o segundo usa XAML com um manipulador de eventos no código e o terceiro é capaz de evitar o manipulador de eventos usando a associação de dados no arquivo XAML.

### <a name="creating-a-stepper-in-code"></a>Criando um seletor em código

O **código básico de escalonador** página na [ **StepperDemos** ](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/StepperDemos) exemplo mostra como criar um [ `Stepper` ](xref:Xamarin.Forms.Stepper) e dois [ `Label` ](xref:Xamarin.Forms.Label) objetos no código:

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

O [ `Stepper` ](xref:Xamarin.Forms.Stepper) é inicializado para ter um [ `Maximum` ](xref:Xamarin.Forms.Stepper.Maximum) propriedade de 360 e um [ `Increment` ](xref:Xamarin.Forms.Stepper.Increment) propriedade de 30 anos. Manipulando o `Stepper` altera o valor selecionado de forma incremental entre [ `Minimum` ](xref:Xamarin.Forms.Stepper.Minimum) para `Maximum` com base no valor da `Increment` propriedade. O [ `ValueChanged` ](xref:Xamarin.Forms.Stepper.ValueChanged) manipulador da `Stepper` usa os [ `Value` ](xref:Xamarin.Forms.Stepper.Value) propriedade do `stepper` objeto para definir o [ `Rotation` ](xref:Xamarin.Forms.VisualElement.Rotation) propriedade do primeiro [ `Label` ](xref:Xamarin.Forms.Label) e usa o `string.Format` método com o `NewValue` propriedade dos argumentos do evento para definir o [ `Text` ](xref:Xamarin.Forms.Label.Text) propriedade das segundo `Label`. Essas duas abordagens para obter o valor atual do `Stepper` são intercambiáveis.

As capturas de tela a seguir mostram os **código básico de escalonador** página:

[![Código básico escalonador](stepper-images/basic-stepper-code.png "código escalonador básico")](stepper-images/basic-stepper-code-large.png#lightbox)

A segunda [ `Label` ](xref:Xamarin.Forms.Label) exibe o texto "(não inicializado)" até que o [ `Stepper` ](xref:Xamarin.Forms.Stepper) seja manipulado, o que faz com que o primeiro [ `ValueChanged` ](xref:Xamarin.Forms.Stepper.ValueChanged) evento para ser disparado.

### <a name="creating-a-stepper-in-xaml"></a>Criando um seletor em XAML

O **básico XAML de escalonador** página é funcionalmente igual **código básico de escalonador** implementado, mas principalmente no XAML:

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

O arquivo code-behind contém o manipulador para o [ `ValueChanged` ](xref:Xamarin.Forms.Stepper.ValueChanged) eventos:

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

Também é possível que o manipulador de eventos obter o [ `Stepper` ](xref:Xamarin.Forms.Stepper) que está disparando o evento por meio de `sender` argumento. O [ `Value` ](xref:Xamarin.Forms.Stepper.Value) propriedade contém o valor atual:

```csharp
double value = ((Stepper)sender).Value;
```

Se o [ `Stepper` ](xref:Xamarin.Forms.Stepper) objeto recebeu um nome no arquivo XAML com um `x:Name` atributo (por exemplo, "escalonador") e, em seguida, o manipulador de eventos pode fazer referência a esse objeto diretamente:

```csharp
double value = stepper.Value;
```

### <a name="data-binding-the-stepper"></a>O seletor de vinculação de dados

O **associações básicas de escalonador** página mostra como escrever um aplicativo quase equivalente que elimina a [ `Value` ](xref:Xamarin.Forms.Stepper.Value) manipulador de eventos usando [associação de dados](~/xamarin-forms/app-fundamentals/data-binding/index.md):

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

O [ `Rotation` ](xref:Xamarin.Forms.VisualElement.Rotation) propriedade do primeiro [ `Label` ](xref:Xamarin.Forms.Label) está associado a [ `Value` ](xref:Xamarin.Forms.Stepper.Value) propriedade do [ `Stepper` ](xref:Xamarin.Forms.Stepper), como é o [ `Text` ](xref:Xamarin.Forms.Label.Text) propriedade do segundo `Label` com um `StringFormat` especificação. O **associações básicas de escalonador** funções da página um pouco diferente das duas páginas anteriores: quando a página aparece pela primeira vez, o segundo `Label` exibe a cadeia de caracteres de texto com o valor. Esse é um benefício do uso de associação de dados. Para exibir texto sem a associação de dados, você precisa inicializar especificamente a `Text` propriedade do `Label` ou simular um acionamento da [ `ValueChanged` ](xref:Xamarin.Forms.Stepper.ValueChanged) evento chamando o manipulador de eventos do construtor de classe .

## <a name="precautions"></a>Precauções

O valor da [ `Minimum` ](xref:Xamarin.Forms.Stepper.Minimum) propriedade sempre deve ser menor que o valor da [ `Maximum` ](xref:Xamarin.Forms.Stepper.Maximum) propriedade. O código a seguir faz com que o trecho de código a [ `Stepper` ](xref:Xamarin.Forms.Stepper) para gerar uma exceção:

```csharp
// Throws an exception!
Stepper stepper = new Stepper
{
    Minimum = 180,
    Maximum = 360
};
```

O C# compilador gera código que define essas duas propriedades em sequência, e quando o [ `Minimum` ](xref:Xamarin.Forms.Stepper.Minimum) estiver definida como 180, é maior que o padrão [ `Maximum` ](xref:Xamarin.Forms.Stepper.Maximum) valor de 100. Você pode evitar a exceção nesse caso, definindo o `Maximum` propriedade primeiro:

```csharp
Stepper stepper = new Stepper
{
    Maximum = 360,
    Minimum = 180
};
```

Definindo [ `Maximum` ](xref:Xamarin.Forms.Stepper.Maximum) a 360 não é um problema porque ele é maior que o padrão [ `Minimum` ](xref:Xamarin.Forms.Stepper.Minimum) valor de 0. Quando `Minimum` estiver definido, o valor é menor do que o `Maximum` valor de 360.

O mesmo problema existe no XAML. Definir as propriedades em uma ordem que garante que [ `Maximum` ](xref:Xamarin.Forms.Stepper.Maximum) é sempre maior que `Minimum`:

```xaml
<Stepper Maximum="360"
         Minimum="180" ... />
```

Você pode definir as [ `Minimum` ](xref:Xamarin.Forms.Stepper.Minimum) e [ `Maximum` ](xref:Xamarin.Forms.Stepper.Maximum) valores para números negativos, mas apenas em uma ordem em que `Minimum` é sempre menor que `Maximum`:

```xaml
<Stepper Minimum="-360"
         Maximum="-180" ... />
```

O [ `Value` ](xref:Xamarin.Forms.Stepper.Value) propriedade é sempre maior que ou igual de [ `Minimum` ](xref:Xamarin.Forms.Stepper.Minimum) valor e menor ou igual a [ `Maximum` ](xref:Xamarin.Forms.Stepper.Maximum). Se `Value` é definido como um valor fora desse intervalo, o valor será ser forçado para ficar dentro do intervalo, mas nenhuma exceção for gerada. Por exemplo, esse código vai *não* gerar uma exceção:

```csharp
Stepper stepper = new Stepper
{
    Value = 180
};
```

Em vez disso, o [ `Value` ](xref:Xamarin.Forms.Stepper.Value) propriedade é forçada para o [ `Maximum` ](xref:Xamarin.Forms.Stepper.Maximum) valor de 100.

Aqui está um trecho de código mostrado acima:

```csharp
Stepper stepper = new Stepper
{
    Maximum = 360,
    Minimum = 180
};
```

Quando [ `Minimum` ](xref:Xamarin.Forms.Stepper.Minimum) é definido para 180, então [ `Value` ](xref:Xamarin.Forms.Stepper.Value) também é definido para 180.

Se um [ `ValueChanged` ](xref:Xamarin.Forms.Stepper.ValueChanged) manipulador de eventos foi anexado no momento em que o [ `Value` ](xref:Xamarin.Forms.Stepper.Value) propriedade é forçada para algo diferente de seu valor padrão de 0, em seguida, um `ValueChanged` evento é disparado. Aqui está um trecho de XAML:

```xaml
<Stepper ValueChanged="OnStepperValueChanged"
         Maximum="360"
         Minimum="180" />
```

Quando [ `Minimum` ](xref:Xamarin.Forms.Stepper.Minimum) é definido para 180, [ `Value` ](xref:Xamarin.Forms.Stepper.Value) também é definido para 180 e o [ `ValueChanged` ](xref:Xamarin.Forms.Stepper.ValueChanged) evento é disparado. Isso pode ocorrer antes que o restante da página foi construído, e o manipulador pode tentar fazer referência a outros elementos na página que ainda não tem sido criados. Você talvez queira adicionar algum código para o `ValueChanged` manipulador que verifica se há `null` valores de outros elementos na página. Ou, você pode definir a `ValueChanged` manipulador de eventos após os [ `Stepper` ](xref:Xamarin.Forms.Stepper) valores foram inicializados.

## <a name="related-links"></a>Links relacionados

- [Exemplo de demonstrações escalonador](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/StepperDemos)
- [Seletor de API](xref:Xamarin.Forms.Stepper)
