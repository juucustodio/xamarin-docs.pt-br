---
title: Associações do xamarin. Forms Basic
description: Este artigo explica como usar o xamarin. Forms vinculação de dados, que vincula um par de propriedades entre dois objetos, pelo menos um dos quais é geralmente um objeto de interface do usuário. Esses dois objetos são chamados de destino e o código-fonte.
ms.prod: xamarin
ms.assetid: 96553DF7-12EA-4FB2-AE85-3D1D59382B40
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/05/2018
ms.openlocfilehash: 16d1970b5e9d8f9c2b7c8be875c81136525c4fb7
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/12/2018
ms.locfileid: "38998066"
---
# <a name="xamarinforms-basic-bindings"></a>Associações do xamarin. Forms Basic

Uma associação de dados do xamarin. Forms vincula um par de propriedades entre dois objetos, pelo menos um dos quais é geralmente um objeto de interface do usuário. Esses dois objetos são chamados de *alvo* e o *origem*:

- O *destino* é o objeto (e a propriedade) em que a associação de dados é definida.
- O *origem* é o objeto (e propriedade) referenciado pela associação de dados.

Essa distinção, às vezes, pode ser um pouco confusa: no caso mais simples, os dados fluem da origem para o destino, o que significa que o valor da propriedade de destino é definido do valor da propriedade da fonte. No entanto, em alguns casos, podem como alternativa, fluxo de dados de destino para a fonte ou em ambas as direções. Para evitar confusão, tenha em mente que o destino é sempre o objeto no qual a associação de dados está definida, mesmo se ele está fornecendo os dados em vez de receber dados.

## <a name="bindings-with-a-binding-context"></a>Associações com um contexto de associação

Embora as associações de dados geralmente são especificadas inteiramente em XAML, é instrutivo observar as associações de dados no código. O **código básico de associação** página contém um arquivo XAML com um `Label` e um `Slider`:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="DataBindingDemos.BasicCodeBindingPage"
             Title="Basic Code Binding">
    <StackLayout Padding="10, 0">
        <Label x:Name="label"
               Text="TEXT"
               FontSize="48"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand" />

        <Slider x:Name="slider"
                Maximum="360"
                VerticalOptions="CenterAndExpand" />
    </StackLayout>
</ContentPage>
```

O `Slider` é definido para um intervalo de 0 a 360. A intenção deste programa é girar o `Label` manipulando o `Slider`.

Sem associações de dados, você definiria o `ValueChanged` eventos do `Slider` para um manipulador de eventos que acessa o `Value` propriedade do `Slider` e define esse valor o `Rotation` propriedade do `Label`. A associação de dados automatiza esse trabalho; o manipulador de eventos e o código dentro dele não são mais necessários.

Você pode definir uma associação em uma instância de qualquer classe que deriva de [ `BindableObject` ](xref:Xamarin.Forms.BindableObject), que inclui `Element`, `VisualElement`, `View`, e `View` derivados.  A associação é sempre definida no objeto de destino. A associação faz referência ao objeto de origem. Para definir a associação de dados, use os seguintes dois membros da classe de destino:

- O [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext) propriedade especifica o objeto de origem.
- O [ `SetBinding` ](xref:Xamarin.Forms.BindableObject.SetBinding(Xamarin.Forms.BindableProperty,Xamarin.Forms.BindingBase)) método Especifica a propriedade de destino e a propriedade de origem.

Neste exemplo, o `Label` é o destino da associação e o `Slider` é a origem da associação. As alterações na `Slider` a rotação de afetar o código-fonte a `Label` destino. Fluxos de dados da origem ao destino.

O `SetBinding` método definido pelo `BindableObject` tem um argumento do tipo [ `BindingBase` ](xref:Xamarin.Forms.BindingBase) do qual o [ `Binding` ](xref:Xamarin.Forms.Binding) deriva da classe, mas há outros `SetBinding` métodos definido pelos [ `BindableObjectExtensions` ](xref:Xamarin.Forms.BindableObjectExtensions) classe. O arquivo code-behind na **código básico de associação** exemplo usa uma opção mais simples [ `SetBinding` ](xref:Xamarin.Forms.BindableObjectExtensions.SetBinding*) método de extensão dessa classe.

```csharp
public partial class BasicCodeBindingPage : ContentPage
{
    public BasicCodeBindingPage()
    {
        InitializeComponent();

        label.BindingContext = slider;
        label.SetBinding(Label.RotationProperty, "Value");
    }
}
```

O `Label` objeto é o destino da associação, portanto, que é o objeto no qual essa propriedade é definida e no qual o método é chamado. O `BindingContext` propriedade indica a origem da associação, que é o `Slider`.

O `SetBinding` método é chamado no destino da associação, mas Especifica a propriedade de destino e a propriedade de origem. A propriedade de destino é especificada como uma `BindableProperty` objeto: `Label.RotationProperty`. A propriedade de origem é especificada como uma cadeia de caracteres e indica os `Value` propriedade de `Slider`.

O `SetBinding` método revela uma das regras de associações de dados mais importantes:

*A propriedade de destino deve ser feita por uma propriedade associável.*

Essa regra implica que o objeto de destino deve ser uma instância de uma classe que deriva de `BindableObject`. Consulte a [ **propriedades vinculáveis** ](~/xamarin-forms/xaml/bindable-properties.md) artigo para obter uma visão geral de objetos vinculáveis e propriedades associáveis.

Não há nenhuma dessas regras para a propriedade de origem, que é especificada como uma cadeia de caracteres. Internamente, a reflexão é usada para acessar a propriedade real. Nesse caso específico, no entanto, o `Value` propriedade também é feita por uma propriedade associável.

O código pode ser simplificado um pouco: O `RotationProperty` propriedade associável é definida por `VisualElement`e herdada por `Label` e `ContentPage` também, portanto, o nome de classe não é necessária no `SetBinding` chamar:

```csharp
label.SetBinding(RotationProperty, "Value");
```

No entanto, incluindo o nome da classe é um bom lembrete do objeto de destino.

Como você manipula a `Slider`, o `Label` gira adequadamente:

[![Associação de código de Basice](basic-bindings-images/basiccodebinding-small.png "vinculação do código básico")](basic-bindings-images/basiccodebinding-large.png#lightbox "vinculação do código básico")

O **associações básicas de Xaml** página é idêntica à **associação básica do código** , exceto que ele define a associação de dados inteira em XAML:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="DataBindingDemos.BasicXamlBindingPage"
             Title="Basic XAML Binding">
    <StackLayout Padding="10, 0">
        <Label Text="TEXT"
               FontSize="80"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand"
               BindingContext="{x:Reference Name=slider}"
               Rotation="{Binding Path=Value}" />

        <Slider x:Name="slider"
                Maximum="360"
                VerticalOptions="CenterAndExpand" />
    </StackLayout>
</ContentPage>
```

Assim como no código, a associação de dados é definida no objeto de destino, que é o `Label`. Duas extensões de marcação XAML estão envolvidas. Estes são instantaneamente reconhecíveis pelos chaves como delimitadores:

- O `x:Reference` extensão de marcação é necessário para fazer referência ao objeto de origem, que é o `Slider` denominado `slider`.
- O `Binding` links de extensão de marcação a `Rotation` propriedade da `Label` para o `Value` propriedade do `Slider`.

Consulte o artigo [extensões de marcação XAML](~/xamarin-forms/xaml/markup-extensions/index.md) para obter mais informações sobre extensões de marcação XAML. O `x:Reference` extensão de marcação dá suporte a [ `ReferenceExtension` ](xref:Xamarin.Forms.Xaml.ReferenceExtension) classe; `Binding` é compatível com o [ `BindingExtension` ](xref:Xamarin.Forms.Xaml.BindingExtension) classe. Como o XML prefixos de namespace indicarem, `x:Reference` faz parte da especificação XAML 2009, enquanto `Binding` faz parte do xamarin. Forms. Observe que nenhuma marca de aspas simples aparecem dentro das chaves.

É fácil esquecer a `x:Reference` extensão de marcação ao definir o `BindingContext`. É comum, por engano, defina a propriedade diretamente para o nome da origem da associação como este:

```xaml
BindingContext="slider"
```

Mas isso não está correto. Essa marcação define o `BindingContext` propriedade para um `string` objeto cujos caracteres ortográfica "slider"!

Observe que a propriedade de origem é especificada com o [ `Path` ](xref:Xamarin.Forms.Xaml.BindingExtension.Path) propriedade do `BindingExtension`, que corresponde com o [ `Path` ](xref:Xamarin.Forms.Binding.Path) propriedade do [ `Binding` ](xref:Xamarin.Forms.Binding) classe.

A marcação mostrada na **XAML básico de associação** página pode ser simplificada: extensões de marcação XAML, como `x:Reference` e `Binding` pode ter *propriedade de conteúdo* atributos definidos, que, para XAML extensões de marcação significa que o nome da propriedade não precisa aparecer. O `Name` é a propriedade de conteúdo do `x:Reference`e o `Path` é a propriedade de conteúdo do `Binding`, o que significa que eles podem ser eliminados das expressões:

```xaml
<Label Text="TEXT"
       FontSize="80"
       HorizontalOptions="Center"
       VerticalOptions="CenterAndExpand"
       BindingContext="{x:Reference slider}"
       Rotation="{Binding Value}" />
```

## <a name="bindings-without-a-binding-context"></a>Associações sem um contexto de associação

O `BindingContext` propriedade é um componente importante de associações de dados, mas nem sempre é necessário. O objeto de origem em vez disso, pode ser especificado na `SetBinding` chamar ou o `Binding` extensão de marcação.

Isso é demonstrado na **associação de código alternativa** exemplo. O arquivo XAML é semelhante ao **código básico de associação** de exemplo, exceto que o `Slider` é definido para o controle a `Scale` propriedade do `Label`. Por esse motivo, o `Slider` está definido para um intervalo de &ndash;2 a 2:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="DataBindingDemos.AlternativeCodeBindingPage"
             Title="Alternative Code Binding">
    <StackLayout Padding="10, 0">
        <Label x:Name="label"
               Text="TEXT"
               FontSize="40"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand" />

        <Slider x:Name="slider"
                Minimum="-2"
                Maximum="2"
                VerticalOptions="CenterAndExpand" />
    </StackLayout>
</ContentPage>
```

O arquivo code-behind define a associação com o [ `SetBinding` ](xref:Xamarin.Forms.BindableObject.SetBinding(Xamarin.Forms.BindableProperty,Xamarin.Forms.BindingBase)) método definido pelo `BindableObject`. O argumento é uma [construtor](xref:Xamarin.Forms.Binding.%23ctor(System.String,Xamarin.Forms.BindingMode,Xamarin.Forms.IValueConverter,System.Object,System.String,System.Object)) para o [ `Binding` ](xref:Xamarin.Forms.Binding) classe:

```csharp
public partial class AlternativeCodeBindingPage : ContentPage
{
    public AlternativeCodeBindingPage()
    {
        InitializeComponent();

        label.SetBinding(Label.ScaleProperty, new Binding("Value", source: slider));
    }
}
```

O `Binding` construtor tem 6 parâmetros, portanto, o `source` parâmetro for especificado com um argumento nomeado. O argumento é o `slider` objeto.

Executar este programa pode ser um pouco surpreendente:

[![Associação de código alternativo](basic-bindings-images/alternativecodebinding-small.png "associação de código alternativo")](basic-bindings-images/alternativecodebinding-large.png#lightbox "associação de código alternativo")

A tela do iOS à esquerda mostra como a tela aparece quando a página é exibida. Onde está o `Label`?

O problema é que o `Slider` tem um valor inicial de 0. Isso faz com que o `Scale` propriedade do `Label` também seja definida como 0, substituindo o valor padrão de 1. Isso resulta no `Label` sendo inicialmente invisível. Como demonstram as capturas de tela do Android e plataforma Universal do Windows (UWP), você pode manipular o `Slider` para tornar o `Label` aparecer novamente, mas seu desaparecimento inicial é desconcertante.

Você descobrirá na [próximo artigo](binding-mode.md) como evitar esse problema, inicializando o `Slider` do valor padrão da `Scale` propriedade.

O **associação de XAML alternativa** página mostra a associação equivalente inteiramente em XAML:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="DataBindingDemos.AlternativeXamlBindingPage"
             Title="Alternative XAML Binding">
    <StackLayout Padding="10, 0">
        <Label Text="TEXT"
               FontSize="40"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand"
               Scale="{Binding Source={x:Reference slider},
                               Path=Value}" />

        <Slider x:Name="slider"
                Minimum="-2"
                Maximum="2"
                VerticalOptions="CenterAndExpand" />
    </StackLayout>
</ContentPage>
```

Agora o `Binding` extensão de marcação, que tem duas propriedades definidas, `Source` e `Path`, separados por uma vírgula. Se você preferir, eles podem aparecer na mesma linha:

```xaml
Scale="{Binding Source={x:Reference slider}, Path=Value}" />
```

O `Source` estiver definida para um embedded `x:Reference` extensão de marcação que caso contrário, tem a mesma sintaxe de configuração o `BindingContext`. Observe que sem aspas são exibidos entre chaves, e que as duas propriedades devem ser separadas por uma vírgula.

A propriedade content do `Binding` extensão de marcação é `Path`, mas o `Path=` parte da extensão de marcação só pode ser eliminada se for a primeira propriedade na expressão. Para eliminar o `Path=` parte, você precisa trocar as duas propriedades:

```xaml
Scale="{Binding Value, Source={x:Reference slider}}" />
```

Embora as extensões de marcação XAML geralmente são delimitadas por chaves, eles também podem ser expressas como elementos de objeto:

```xaml
<Label Text="TEXT"
       FontSize="40"
       HorizontalOptions="Center"
       VerticalOptions="CenterAndExpand">
    <Label.Scale>
        <Binding Source="{x:Reference slider}"
                 Path="Value" />
    </Label.Scale>
</Label>
```

Agora o `Source` e `Path` propriedades são os atributos XAML regulares: os valores aparecem entre aspas e os atributos não são separados por uma vírgula. O `x:Reference` extensão de marcação também pode se tornar um elemento de objeto:

```xaml
<Label Text="TEXT"
       FontSize="40"
       HorizontalOptions="Center"
       VerticalOptions="CenterAndExpand">
    <Label.Scale>
        <Binding Path="Value">
            <Binding.Source>
                <x:Reference Name="slider" />
            </Binding.Source>
        </Binding>
    </Label.Scale>
</Label>
```

Essa sintaxe não é comum, mas, às vezes, é necessário quando objetos complexos estão envolvidos.

Os exemplos mostrados até agora definem a `BindingContext` propriedade e o `Source` propriedade de `Binding` para um `x:Reference` extensão de marcação para fazer referência a outro modo de exibição na página. Essas duas propriedades são do tipo `Object`, e eles podem ser definidos para qualquer objeto que inclui as propriedades que são adequadas para fontes de associação.

Os artigos em frente, você descobrirá que você pode definir as `BindingContext` ou `Source` propriedade para um `x:Static` extensão de marcação para referenciar o valor de uma propriedade estática ou um campo, ou um `StaticResource` extensão de marcação para fazer referência a um objeto armazenado em um dicionário de recursos, ou diretamente a um objeto, o que é normalmente (mas nem sempre) uma instância de um ViewModel.

O `BindingContext` propriedade também pode ser definida como uma `Binding` objeto, de modo que o `Source` e `Path` as propriedades de `Binding` definir o contexto de associação.

## <a name="binding-context-inheritance"></a>Herança de contexto de associação

Neste artigo, você viu que você pode especificar o objeto de origem usando o `BindingContext` propriedade ou o `Source` propriedade do `Binding` objeto. Se ambos estiverem definidas, o `Source` propriedade do `Binding` terá precedência sobre o `BindingContext`.

O `BindingContext` propriedade tem uma característica muito importante:

*A configuração do `BindingContext` propriedade é herdada por meio da árvore visual.*

Como você verá, isso pode ser muito útil para simplificar expressões de associação e, em alguns casos &mdash; especialmente em cenários de Model-View-ViewModel (MVVM) &mdash; é essencial.

O **herança de contexto de associação** exemplo é uma demonstração simple do que a herança de contexto de associação:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="DataBindingDemos.BindingContextInheritancePage"
             Title="BindingContext Inheritance">
    <StackLayout Padding="10">

        <StackLayout VerticalOptions="FillAndExpand"
                     BindingContext="{x:Reference slider}">

            <Label Text="TEXT"
                   FontSize="80"
                   HorizontalOptions="Center"
                   VerticalOptions="EndAndExpand"
                   Rotation="{Binding Value}" />

            <BoxView Color="#800000FF"
                     WidthRequest="180"
                     HeightRequest="40"
                     HorizontalOptions="Center"
                     VerticalOptions="StartAndExpand"
                     Rotation="{Binding Value}" />
        </StackLayout>

        <Slider x:Name="slider"
                Maximum="360" />

    </StackLayout>
</ContentPage>
```

O `BindingContext` propriedade do `StackLayout` é definido como o `slider` objeto. Esse contexto de associação é herdado por ambos o `Label` e o `BoxView`, ambas dos quais têm seus `Rotation` as propriedades definidas para o `Value` propriedade do `Slider`:

[![Herança de contexto de associação](basic-bindings-images/bindingcontextinheritance-small.png "herança de contexto de associação")](basic-bindings-images/bindingcontextinheritance-large.png#lightbox "herança de contexto de associação")

No [próximo artigo](binding-mode.md), você verá como o *modo de associação* pode alterar o fluxo de dados entre objetos de origem e destino.


## <a name="related-links"></a>Links relacionados

- [Demonstrações de associação de dados (amostra)](https://developer.xamarin.com/samples/xamarin-forms/DataBindingDemos/)
- [Capítulo de associação de dados do catálogo de xamarin. Forms](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter16.md)
