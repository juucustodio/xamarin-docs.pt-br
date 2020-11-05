---
title: Várias associações do Xamarin. Forms
description: Este artigo explica como anexar uma coleção de objetos de associação a uma única propriedade de destino de associação usando a classe.
ms.prod: xamarin
ms.assetid: E73AE622-664C-4A90-B5B2-BD47D0E7A1A7
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/26/2020
ms.openlocfilehash: 79d27cf9d6ba01235962b43ee7f05c904c265c2f
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93370839"
---
# <a name="xamarinforms-multi-bindings"></a>Várias associações do Xamarin. Forms

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](/samples/xamarin/xamarin-forms-samples/databindingdemos)

As várias associações fornecem a capacidade de anexar uma coleção de [`Binding`](xref:Xamarin.Forms.Binding) objetos a uma única propriedade de destino de associação. Eles são criados com a `MultiBinding` classe, que avalia todos os seus `Binding` objetos e retorna um único valor por meio de uma `IMultiValueConverter` instância fornecida pelo seu aplicativo. Além disso, `MultiBinding` o reavalia todos os seus `Binding` objetos quando qualquer um dos dados ligados é alterado.

A `MultiBinding` classe define as seguintes propriedades:

- `Bindings`, do tipo `IList<BindingBase>` , que representa a coleção de [`Binding`](xref:Xamarin.Forms.Binding) objetos dentro da `MultiBinding` instância.
- `Converter`, do tipo `IMultiValueConverter` , que representa o conversor a ser usado para converter os valores de origem de ou para o valor de destino.
- `ConverterParameter`, do tipo `object` , que representa um parâmetro opcional a ser passado para o `Converter` .

A `Bindings` propriedade é a propriedade content da `MultiBinding` classe e, portanto, não precisa ser definida explicitamente a partir de XAML.

Além disso, a `MultiBinding` classe herda as seguintes propriedades da `BindingBase` classe:

- `FallbackValue`, do tipo `object` , que representa o valor a ser usado quando a associação múltipla não puder retornar um valor.
- `Mode`, do tipo [`BindingMode`](xref:Xamarin.Forms.BindingMode) , que indica a direção do fluxo de dados da Associação múltipla.
- `StringFormat`, do tipo `string` , que especifica como formatar o resultado de várias ligações se ele for exibido como uma cadeia de caracteres.
- `TargetNullValue`, do tipo `object` , que representa o valor que é usado no Wen de destino. o valor da origem é `null` .

Um `MultiBinding` deve usar um `IMultiValueConverter` para produzir um valor para o destino de associação, com base no valor das associações na `Bindings` coleção. Por exemplo, um [`Color`](xref:Xamarin.Forms.Color) pode ser computado de valores vermelho, azul e verde, que podem ser valores dos mesmos ou de objetos de origem de associação diferentes. Quando um valor é movido do destino para as fontes, o valor da propriedade de destino é convertido em um conjunto de valores que são devolvidos para as associações.

> [!IMPORTANT]
> Associações individuais na `Bindings` coleção podem ter seus próprios conversores de valor.

O valor da `Mode` propriedade determina a funcionalidade do `MultiBinding` e é usado como o modo de associação para todas as associações na coleção, a menos que uma associação individual substitua a propriedade. Por exemplo, se a `Mode` propriedade em um `MultiBinding` objeto for definida como [`TwoWay`](xref:Xamarin.Forms.BindingMode.TwoWay) , todas as associações na coleção serão consideradas, `TwoWay` a menos que você defina explicitamente um `Mode` valor diferente em uma das associações.

## <a name="define-a-imultivalueconverter"></a>Definir um IMultiValueConverter

A `IMultiValueConverter` interface permite que a lógica personalizada seja aplicada a um `MultiBinding` . Para associar um conversor a um `MultiBinding` , crie uma classe que implemente a interface e, `IMultiValueConverter` em seguida, implemente os `Convert` `ConvertBack` métodos e:

```csharp
public class AllTrueMultiConverter : IMultiValueConverter
{
    public object Convert(object[] values, Type targetType, object parameter, CultureInfo culture)
    {
        if (values == null || !targetType.IsAssignableFrom(typeof(bool)))
        {
            return false;
            // Alternatively, return BindableProperty.UnsetValue to use the binding FallbackValue
        }

        foreach (var value in values)
        {
            if (!(value is bool b))
            {
                return false;
                // Alternatively, return BindableProperty.UnsetValue to use the binding FallbackValue
            }
            else if (!b)
            {
                return false;
            }
        }
        return true;
    }

    public object[] ConvertBack(object value, Type[] targetTypes, object parameter, CultureInfo culture)
    {
        if (!(value is bool b) || targetTypes.Any(t => !t.IsAssignableFrom(typeof(bool))))
        {
            // Return null to indicate conversion back is not possible
            return null;
        }

        if (b)
        {
            return targetTypes.Select(t => (object)true).ToArray();
        }
        else
        {
            // Can't convert back from false because of ambiguity
            return null;
        }
    }
}
```

O `Convert` método converte valores de origem em um valor para o destino de associação. O Xamarin. Forms chama esse método quando propaga valores de associações de origem para o destino de associação. Esse método aceita quatro argumentos:

- `values`, do tipo `object[]` , é uma matriz de valores que a origem vincula em `MultiBinding` produz.
- `targetType`, do tipo `Type` , é o tipo da propriedade de destino de associação.
- `parameter`, do tipo `object` , é o parâmetro do conversor a ser usado.
- `culture`, do tipo `CultureInfo` , é a cultura a ser usada no conversor.

O `Convert` método retorna um `object` que representa um valor convertido. Esse método deve retornar:

- `BindableProperty.UnsetValue` para indicar que o conversor não produziu um valor e que a associação usará o `FallbackValue` .
- `Binding.DoNothing` para instruir o Xamarin. Forms a não executar nenhuma ação. Por exemplo, para instruir o Xamarin. Forms a não transferir um valor para o destino de associação, ou não usar o `FallbackValue` .
- `null` para indicar que o conversor não pode executar a conversão e que a associação usará o `TargetNullValue` .

> [!IMPORTANT]
> Um `MultiBinding` que recebe `BindableProperty.UnsetValue` de um `Convert` método deve definir sua [`FallbackValue`](xref:Xamarin.Forms.BindingBase.FallbackValue) propriedade. Da mesma forma, um `MultiBinding` que recebe `null` de um `Convert` método deve definir seu [`TargetNullValue`](xref:Xamarin.Forms.BindingBase.TargetNullValue) propety.

O `ConvertBack` método converte um destino de associação para os valores de associação de origem. Esse método aceita quatro argumentos:

- `value`, do tipo `object` , é o valor que o destino de associação produz.
- `targetTypes`, do tipo `Type[]` , é a matriz de tipos para converter. O comprimento da matriz indica o número e tipos de valores sugeridos para o método retornar.
- `parameter`, do tipo `object` , é o parâmetro do conversor a ser usado.
- `culture`, do tipo `CultureInfo` , é a cultura a ser usada no conversor.

O `ConvertBack` método retorna uma matriz de valores, do tipo `object[]` , que foram convertidos do valor de destino de volta para os valores de origem. Esse método deve retornar:

- `BindableProperty.UnsetValue` na posição `i` para indicar que o conversor não pode fornecer um valor para a associação de origem no índice `i` e que nenhum valor deve ser definido nele.
- `Binding.DoNothing` na posição `i` para indicar que nenhum valor deve ser definido na associação de origem no índice `i` .
- `null` para indicar que o conversor não pode executar a conversão ou que não dá suporte à conversão nesta direção.

## <a name="consume-a-imultivalueconverter"></a>Consumir um IMultiValueConverter

Um `IMultiValueConverter` é consumido instanciando-se em um dicionário de recursos e, em seguida, fazendo referência a ele usando a `StaticResource` extensão de marcação para definir a `MultiBinding.Converter` Propriedade:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:DataBindingDemos"
             x:Class="DataBindingDemos.MultiBindingConverterPage"
             Title="MultiBinding Converter demo">

    <ContentPage.Resources>
        <local:AllTrueMultiConverter x:Key="AllTrueConverter" />
        <local:InverterConverter x:Key="InverterConverter" />
    </ContentPage.Resources>

    <CheckBox>
        <CheckBox.IsChecked>
            <MultiBinding Converter="{StaticResource AllTrueConverter}">
                <Binding Path="Employee.IsOver16" />
                <Binding Path="Employee.HasPassedTest" />
                <Binding Path="Employee.IsSuspended"
                         Converter="{StaticResource InverterConverter}" />
            </MultiBinding>
        </CheckBox.IsChecked>
    </CheckBox>
</ContentPage>    
```

Neste exemplo, o `MultiBinding` objeto usa a `AllTrueMultiConverter` instância para a qual definir a [`CheckBox.IsChecked`](xref:Xamarin.Forms.CheckBox.IsChecked) propriedade `true` , desde que os três [`Binding`](xref:Xamarin.Forms.Binding) objetos sejam avaliados como `true` . Caso contrário, a `CheckBox.IsChecked` propriedade será definida como `false` .

Por padrão, a [`CheckBox.IsChecked`](xref:Xamarin.Forms.CheckBox.IsChecked) propriedade usa uma [`TwoWay`](xref:Xamarin.Forms.BindingMode.TwoWay) associação. Portanto, o `ConvertBack` método da `AllTrueMultiConverter` instância é executado quando o [`CheckBox`](xref:Xamarin.Forms.CheckBox) é desmarcado pelo usuário, que define os valores de associação de origem como o valor da `CheckBox.IsChecked` propriedade.

O código C# equivalente é mostrado abaixo:

```csharp
public class MultiBindingConverterCodePage : ContentPage
{
    public MultiBindingConverterCodePage()
    {
        BindingContext = new GroupViewModel();

        CheckBox checkBox = new CheckBox();
        checkBox.SetBinding(CheckBox.IsCheckedProperty, new MultiBinding
        {
            Bindings = new Collection<BindingBase>
            {
                new Binding("Employee1.IsOver16"),
                new Binding("Employee1.HasPassedTest"),
                new Binding("Employee1.IsSuspended", converter: new InverterConverter())
            },
            Converter = new AllTrueMultiConverter()
        });

        Title = "MultiBinding converter demo";
        Content = checkBox;
    }
}
```

## <a name="format-strings"></a>Formatar cadeias de caracteres

Um `MultiBinding` pode formatar qualquer resultado de várias ligações que é exibido como uma cadeia de caracteres, com a `StringFormat` propriedade. Essa propriedade pode ser definida como uma cadeia de caracteres de formatação .NET padrão, com espaços reservados, que especifica como formatar o resultado de várias associações:

```xaml
<Label>
    <Label.Text>
        <MultiBinding StringFormat="{}{0} {1} {2}">
            <Binding Path="Employee1.Forename" />
            <Binding Path="Employee1.MiddleName" />
            <Binding Path="Employee1.Surname" />
        </MultiBinding>
    </Label.Text>
</Label>
```

Neste exemplo, a `StringFormat` Propriedade combina os três valores associados em uma única cadeia de caracteres que é exibida pelo [`Label`](xref:Xamarin.Forms.Label) .

O código C# equivalente é mostrado abaixo:

```csharp
Label label = new Label();
label.SetBinding(Label.TextProperty, new MultiBinding
{
    Bindings = new Collection<BindingBase>
    {
        new Binding("Employee1.Forename"),
        new Binding("Employee1.MiddleName"),
        new Binding("Employee1.Surname")
    },
    StringFormat = "{0} {1} {2}"
});
```

> [!IMPORTANT]
> O número de parâmetros em um formato de cadeia de caracteres composta não pode exceder o número de `Binding` objetos filho no `MultiBinding` .

Ao definir as `Converter` `StringFormat` Propriedades e, o conversor é aplicado primeiro ao valor de dados e, em seguida, o `StringFormat` é aplicado.

Para obter mais informações sobre formatação de cadeia de caracteres no Xamarin. Forms, consulte [formatação de cadeias de caracteres xamarin. Forms](string-formatting.md).

## <a name="provide-fallback-values"></a>Fornecer valores de fallback

As associações de dados podem se tornar mais robustas definindo valores de fallback a serem usados se o processo de associação falhar. Isso pode ser feito por meio da definição opcional [`FallbackValue`](xref:Xamarin.Forms.BindingBase.FallbackValue) das [`TargetNullValue`](xref:Xamarin.Forms.BindingBase.TargetNullValue) Propriedades e em um `MultiBinding` objeto.

Um `MultiBinding` usará seu [`FallbackValue`](xref:Xamarin.Forms.BindingBase.FallbackValue) quando o `Convert` método de uma `IMultiValueConverter` instância retornar `BindableProperty.UnsetValue` , o que indica que o conversor não produziu um valor. Um `MultiBinding` usará seu [`TargetNullValue`](xref:Xamarin.Forms.BindingBase.TargetNullValue) quando o `Convert` método de uma `IMultiValueConverter` instância retornar `null` , o que indica que o conversor não pode executar a conversão.

Para obter mais informações sobre fallbacks de associação, consulte [fallbacks de associação do Xamarin. Forms](binding-fallbacks.md).

## <a name="nest-multibinding-objects"></a>Aninhar objetos MultiBinding

`MultiBinding` os objetos podem ser aninhados para que vários `MultiBinding` objetos sejam avaliados para retornar um valor por meio de uma `IMultiValueConverter` instância:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:DataBindingDemos"
             x:Class="DataBindingDemos.NestedMultiBindingPage"
             Title="Nested MultiBinding demo">

    <ContentPage.Resources>
        <local:AllTrueMultiConverter x:Key="AllTrueConverter" />
        <local:AnyTrueMultiConverter x:Key="AnyTrueConverter" />
        <local:InverterConverter x:Key="InverterConverter" />
    </ContentPage.Resources>

    <CheckBox>
        <CheckBox.IsChecked>
            <MultiBinding Converter="{StaticResource AnyTrueConverter}">
                <MultiBinding Converter="{StaticResource AllTrueConverter}">
                    <Binding Path="Employee.IsOver16" />
                    <Binding Path="Employee.HasPassedTest" />
                    <Binding Path="Employee.IsSuspended" Converter="{StaticResource InverterConverter}" />                        
                </MultiBinding>
                <Binding Path="Employee.IsMonarch" />
            </MultiBinding>
        </CheckBox.IsChecked>
    </CheckBox>
</ContentPage>
```

Neste exemplo, o `MultiBinding` objeto usa sua `AnyTrueMultiConverter` instância para definir a [`CheckBox.IsChecked`](xref:Xamarin.Forms.CheckBox.IsChecked) propriedade `true` , desde que todos os [`Binding`](xref:Xamarin.Forms.Binding) objetos no `MultiBinding` objeto interno avaliem `true` ou desde que o `Binding` objeto no `MultiBinding` objeto externo seja avaliado como `true` . Caso contrário, a `CheckBox.IsChecked` propriedade será definida como `false` .

## <a name="use-a-relativesource-binding-in-a-multibinding"></a>Usar uma associação RelativeSource em uma MultiBinding

`MultiBinding` os objetos dão suporte a associações relativas, que fornecem a capacidade de definir a origem da associação em relação à posição do destino de associação:

```xaml
<ContentPage ...
             xmlns:local="clr-namespace:DataBindingDemos">
    <ContentPage.Resources>
        <local:AllTrueMultiConverter x:Key="AllTrueConverter" />

        <ControlTemplate x:Key="CardViewExpanderControlTemplate">
            <Expander BindingContext="{Binding Source={RelativeSource TemplatedParent}}"
                      IsExpanded="{Binding IsExpanded, Source={RelativeSource TemplatedParent}}"
                      BackgroundColor="{Binding CardColor}">
                <Expander.IsVisible>
                    <MultiBinding Converter="{StaticResource AllTrueConverter}">
                        <Binding Path="IsExpanded" />
                        <Binding Path="IsEnabled" />
                    </MultiBinding>
                </Expander.IsVisible>
                <Expander.Header>
                    <Grid>
                        <!-- XAML that defines Expander header goes here -->
                    </Grid>
                </Expander.Header>
                <Grid>
                    <!-- XAML that defines Expander content goes here -->
                </Grid>
            </Expander>
        </ControlTemplate>
    </ContentPage.Resources>

    <StackLayout>
        <controls:CardViewExpander BorderColor="DarkGray"
                                   CardTitle="John Doe"
                                   CardDescription="Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nulla elit dolor, convallis non interdum."
                                   IconBackgroundColor="SlateGray"
                                   IconImageSource="user.png"
                                   ControlTemplate="{StaticResource CardViewExpanderControlTemplate}"
                                   IsEnabled="True"
                                   IsExpanded="True" />
    </StackLayout>
</ContentPage>
```

Neste exemplo, o `TemplatedParent` modo de associação relativo é usado para associar de dentro de um modelo de controle à instância do objeto de tempo de execução à qual o modelo é aplicado. O `Expander` , que é o elemento raiz do [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) , tem seu `BindingContext` definido como a instância do objeto de tempo de execução à qual o modelo é aplicado. Portanto, o `Expander` e seus filhos resolvem suas expressões de associação e [`Binding`](xref:Xamarin.Forms.Binding) objetos, em relação às propriedades do `CardViewExpander` objeto. O `MultiBinding` usa a `AllTrueMultiConverter` instância para definir a `Expander.IsVisible` propriedade como `true` desde que os dois [`Binding`](xref:Xamarin.Forms.Binding) objetos sejam avaliados como `true` . Caso contrário, a `Expander.IsVisible` propriedade será definida como `false` .

Para obter mais informações sobre associações relativas, veja [Associações Relativas do Xamarin.Forms](relative-bindings.md). Para obter mais informações sobre modelos de controle, consulte [Xamarin. Forms Control templates](~/xamarin-forms/app-fundamentals/templates/control-template.md).

## <a name="related-links"></a>Links relacionados

- [Demonstrações de associação de dados (amostra)](/samples/xamarin/xamarin-forms-samples/databindingdemos)
- [Formatação de cadeia de caracteres do Xamarin.Forms](string-formatting.md)
- [Fallbacks de associação do Xamarin.Forms](binding-fallbacks.md)
- [Associações relativas do Xamarin.Forms](relative-bindings.md)
- [Modelos de controle do Xamarin. Forms](~/xamarin-forms/app-fundamentals/templates/control-template.md)