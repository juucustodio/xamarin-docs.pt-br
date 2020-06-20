---
title: Xamarin.FormsAssociações básicas
description: Este artigo explica como usar a Xamarin.Forms vinculação de dados, que vincula um par de propriedades entre dois objetos, pelo menos um dos quais geralmente é um objeto de interface de usuário. Esses dois objetos são chamados de destino e origem.
ms.prod: xamarin
ms.assetid: 96553DF7-12EA-4FB2-AE85-3D1D59382B40
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/22/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.custom: video
ms.openlocfilehash: c0c6bc6e1005997548952aedc09cd83a451e7caa
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2020
ms.locfileid: "84133517"
---
# <a name="xamarinforms-basic-bindings"></a>Xamarin.FormsAssociações básicas

[![Baixar exemplo ](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/databindingdemos)

Uma Xamarin.Forms ligação de dados vincula um par de propriedades entre dois objetos, pelo menos um dos quais geralmente é um objeto de interface do usuário. Esses dois objetos são chamados de *destino* e *origem*:

- O *destino* é o objeto (e a propriedade) no qual a associação de dados é definida.
- A *origem* é o objeto (e propriedade) referenciado pela associação de dados.

Essa distinção, às vezes, pode ser um pouco confusa: no caso mais simples, os dados fluem da origem para o destino, o que significa que o valor da propriedade de destino é definido pelo valor da propriedade da origem. No entanto, em alguns casos, os dados podem fluir do destino para a origem ou em ambas os sentidos. Para evitar confusão, tenha em mente que o destino é sempre o objeto no qual a associação de dados está definida, mesmo se estiver fornecendo os dados em vez de recebendo.

## <a name="bindings-with-a-binding-context"></a>Associações com um contexto de associação

Embora as associações de dados geralmente sejam especificadas inteiramente em XAML, é instrutivo observar as associações de dados no código. A página **Associação de código básica** contém um arquivo XAML com um `Label` e um `Slider`:

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

Sem associações de dados, você definiria o evento `ValueChanged` do `Slider` para um manipulador de eventos que acessa a propriedade `Value` do `Slider` e define esse valor para a propriedade `Rotation` do `Label`. A associação de dados automatiza esse trabalho. O manipulador de eventos e o código dentro dele não são mais necessários.

Você pode definir uma associação em uma instância de qualquer classe derivada de [`BindableObject`](xref:Xamarin.Forms.BindableObject) , que inclui,, `Element` `VisualElement` `View` e `View` derivações.  A associação é sempre definida no objeto de destino. A associação faz referência ao objeto de origem. Para definir a associação de dados, use os seguintes dois membros da classe de destino:

- A [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) propriedade especifica o objeto de origem.
- O [ `SetBinding` ] (xref: Xamarin.Forms . Acopláble. SetBinding ( Xamarin.Forms . Vinculproperty, Xamarin.Forms . BindingBase)), o método especifica a propriedade de destino e a propriedade de origem.

Neste exemplo, o `Label` é o destino da associação e o `Slider` é a origem da associação. As alterações na origem `Slider` afetam a rotação do destino `Label`. Os dados fluem da origem para o destino.

O `SetBinding` método definido por `BindableObject` tem um argumento do tipo [`BindingBase`](xref:Xamarin.Forms.BindingBase) do qual a [`Binding`](xref:Xamarin.Forms.Binding) classe deriva, mas há outros `SetBinding` métodos definidos pela [`BindableObjectExtensions`](xref:Xamarin.Forms.BindableObjectExtensions) classe. O arquivo code-behind no exemplo da **Associação de código básica** usa um método de extensão [`SetBinding`](xref:Xamarin.Forms.BindableObjectExtensions.SetBinding*) mais simples de sua classe.

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

O objeto `Label` é o destino da associação, portanto, é o objeto no qual essa propriedade é definida e no qual o método é chamado. A propriedade `BindingContext` indica a origem da associação, que é o `Slider`.

O método `SetBinding` é chamado no destino da associação, mas especifica a propriedade de destino e a propriedade de origem. A propriedade de destino é especificada como um objeto `BindableProperty`: `Label.RotationProperty`. A propriedade de origem é especificada como uma cadeia de caracteres e indica a propriedade `Value` de `Slider`.

O método `SetBinding` revela uma das regras de associações de dados mais importantes:

*A propriedade de destino deve ter suporte de uma propriedade associável.*

Essa regra implica que o objeto de destino deve ser uma instância de uma classe que deriva de `BindableObject`. Confira o artigo [**Propriedades associáveis**](~/xamarin-forms/xaml/bindable-properties.md) para obter uma visão geral de objetos associáveis e propriedades associáveis.

Não há uma regra para a propriedade de origem, que é especificada como uma cadeia de caracteres. Internamente, a reflexão é usada para acessar a propriedade real. Nesse caso específico, no entanto, a propriedade `Value` também tem suporte de uma propriedade associável.

O código pode ser simplificado um pouco: a propriedade associável `RotationProperty` é definida por `VisualElement`e herdada por `Label` e também por `ContentPage`, portanto, o nome de classe não é necessário na chamada `SetBinding`:

```csharp
label.SetBinding(RotationProperty, "Value");
```

No entanto, incluir o nome de classe é um bom lembrete do objeto de destino.

Ao manipular `Slider`, o `Label` gira de acordo:

[![Vinculação de código básica](basic-bindings-images/basiccodebinding-small.png "Vinculação de código básica")](basic-bindings-images/basiccodebinding-large.png#lightbox "Vinculação de código básica")

A página **Associação de XAML básica** é idêntica à **Associação de código básica**, exceto que ela define a associação de dados inteira em XAML:

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

Assim como no código, a associação de dados é definida no objeto de destino, que é o `Label`. Duas extensões de marcação XAML estão envolvidas. Estas são instantaneamente reconhecíveis pelos delimitadores de chaves:

- A extensão de marcação `x:Reference` é necessária para fazer referência ao objeto de origem, que é o `Slider` denominado `slider`.
- A extensão de marcação `Binding` vincula a propriedade `Rotation` do `Label` à propriedade `Value` do `Slider`.

Para obter mais informações sobre extensões de marcação XAML, confira o artigo [Extensões de marcação XAML](~/xamarin-forms/xaml/markup-extensions/index.md). A classe não dá suporte à `x:Reference` extensão de marcação [`ReferenceExtension`](xref:Xamarin.Forms.Xaml.ReferenceExtension) `Binding` [`BindingExtension`](xref:Xamarin.Forms.Xaml.BindingExtension) . Como os prefixos de namespace XML indicam, faz `x:Reference` parte da especificação XAML 2009, enquanto `Binding` faz parte do Xamarin.Forms . Observe que não há aspas dentro das chaves.

É fácil esquecer-se da extensão de marcação `x:Reference` ao definir o `BindingContext`. É comum definir de modo incorreto a propriedade diretamente para o nome da origem da associação, do seguinte modo:

```xaml
BindingContext="slider"
```

Mas isso não é certo. Essa marcação define a propriedade `BindingContext` para um objeto `string` cujos caracteres formam a palavra "slider".

Observe que a propriedade Source é especificada com a [`Path`](xref:Xamarin.Forms.Xaml.BindingExtension.Path) propriedade de `BindingExtension` , que corresponde à [`Path`](xref:Xamarin.Forms.Binding.Path) propriedade da [`Binding`](xref:Xamarin.Forms.Binding) classe.

A marcação mostrada na página **Associação de XAML básica** pode ser simplificada: extensões de marcação XAML, como `x:Reference` e `Binding`, podem ter atributos de *propriedade de conteúdo* definidos, que, para extensões de marcação XAML, significa que o nome da propriedade não precisa aparecer. A propriedade `Name` é a propriedade de conteúdo do `x:Reference`, e a propriedade `Path` é a propriedade de conteúdo do `Binding`, o que significa que elas podem ser eliminadas das expressões:

```xaml
<Label Text="TEXT"
       FontSize="80"
       HorizontalOptions="Center"
       VerticalOptions="CenterAndExpand"
       BindingContext="{x:Reference slider}"
       Rotation="{Binding Value}" />
```

## <a name="bindings-without-a-binding-context"></a>Associações sem um contexto de associação

A propriedade `BindingContext` é um componente importante de associações de dados, mas nem sempre é necessária. O objeto de origem pode ser especificado na chamada `SetBinding` ou na extensão de marcação `Binding`.

Isso é demonstrado no exemplo **Associação de código alternativa**. O arquivo XAML é semelhante ao exemplo **Associação de código básica**, exceto que o `Slider` é definido para controlar a propriedade `Scale` do `Label`. Por esse motivo, o `Slider` é definido para um intervalo de &ndash;2 a 2:

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

O arquivo code-behind define a associação com o [ `SetBinding` ] (xref: Xamarin.Forms . Acopláble. SetBinding ( Xamarin.Forms . Vinculproperty, Xamarin.Forms . BindingBase)) método definido por `BindableObject` . O argumento é um [constructor] (xref: Xamarin.Forms . Associação .% 23ctor (System. String, Xamarin.Forms . BindingMode, Xamarin.Forms . IValueConverter, System. Object, System. String, System. Object) para a [`Binding`](xref:Xamarin.Forms.Binding) classe:

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

O construtor `Binding` tem seis parâmetros, portanto o parâmetro `source` é especificado com um argumento nomeado. O argumento é o objeto `slider`.

Executar este programa pode ser um pouco surpreendente:

[![Associação de código alternativo](basic-bindings-images/alternativecodebinding-small.png "Associação de código alternativo")](basic-bindings-images/alternativecodebinding-large.png#lightbox "Associação de código alternativo")

A tela do iOS à esquerda mostra como a tela aparece quando a página é exibida pela primeira vez. Onde está o `Label`?

O problema é que o `Slider` tem um valor inicial de 0. Isso faz com que a propriedade `Scale` do `Label` também seja definida como 0, substituindo o valor padrão de 1. Isso resulta no `Label` sendo inicialmente invisível. Como demonstra a captura de tela do Android, você pode manipular o `Slider` para que ele `Label` apareça novamente, mas sua desexibição inicial está desconcerta.

Você descobrirá no [próximo artigo](binding-mode.md) como evitar esse problema ao inicializar `Slider` do valor padrão da propriedade `Scale`.

> [!NOTE]
> A [`VisualElement`](xref:Xamarin.Forms.VisualElement) classe também define [`ScaleX`](xref:Xamarin.Forms.VisualElement.ScaleX) as [`ScaleY`](xref:Xamarin.Forms.VisualElement.ScaleY) Propriedades e, que podem ser dimensionadas de `VisualElement` forma diferente nas direções horizontal e vertical.

A página **Associação de XAML alternativa** mostra a associação equivalente inteiramente em XAML:

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

Agora a extensão de marcação `Binding` tem duas propriedades definidas, `Source` e `Path`, separadas por uma vírgula. Se você preferir, elas poderão aparecer na mesma linha:

```xaml
Scale="{Binding Source={x:Reference slider}, Path=Value}" />
```

A propriedade `Source` está definida para uma extensão de marcação `x:Reference` inserida que normalmente tem a mesma sintaxe que a configuração de `BindingContext`. Observe que não há aspas entre as chaves e as duas propriedades devem ser separadas por uma vírgula.

A propriedade de conteúdo da extensão de marcação `Binding` é `Path`, mas a parte `Path=` da extensão de marcação só poderá ser eliminada se for a primeira propriedade na expressão. Para eliminar a parte `Path=`, você precisa trocar as duas propriedades:

```xaml
Scale="{Binding Value, Source={x:Reference slider}}" />
```

Embora as extensões de marcação XAML geralmente sejam delimitadas por chaves, elas também podem ser expressas como elementos de objeto:

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

Agora as propriedades `Source` e `Path` são os atributos XAML regulares: os valores aparecem entre aspas e os atributos não são separados por uma vírgula. A extensão de marcação `x:Reference` também pode se tornar um elemento de objeto:

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

Essa sintaxe não é comum, mas, às vezes, é necessária quando objetos complexos estão envolvidos.

Os exemplos mostrados até agora definem a propriedade `BindingContext` e a propriedade `Source` de `Binding` para uma extensão de marcação `x:Reference` para fazer referência a outra exibição na página. Essas duas propriedades são do tipo `Object`, e elas podem ser definidas para qualquer objeto que inclua as propriedades que são adequadas para as origens da associação.

Nos próximos artigos, você descobrirá que pode definir a propriedade `BindingContext` ou `Source` para uma extensão de marcação `x:Static` para fazer referência ao valor de uma propriedade estática ou campo ou para uma extensão de marcação `StaticResource` para fazer referência a um objeto armazenado em um dicionário de recursos ou diretamente a um objeto que é normalmente (mas nem sempre) uma instância de um ViewModel.

A propriedade `BindingContext` também pode ser definida como um objeto `Binding`, de modo que as propriedades `Source` e `Path` de `Binding` definem o contexto de associação.

## <a name="binding-context-inheritance"></a>Herança de contexto de associação

Neste artigo, você viu que pode especificar o objeto de origem usando a propriedade `BindingContext` ou a propriedade `Source` do objeto `Binding`. Se ambas estiverem definidas, a propriedade `Source` do `Binding` terá precedência em relação a `BindingContext`.

A propriedade `BindingContext` tem uma característica muito importante:

*A configuração da propriedade `BindingContext` é herdada por meio da árvore visual.*

Como você verá, isso pode ser muito útil para simplificar expressões de associação e, em alguns casos, &mdash; especialmente em cenários de MVVM (Model-View-ViewModel), &mdash; é essencial.

O exemplo **Herança de contexto de associação** é uma demonstração simples da herança de contexto de associação:

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

A propriedade `BindingContext` do `StackLayout` é definida para o objeto `slider`. Esse contexto de associação é herdado por `Label` e `BoxView`, que têm suas propriedades `Rotation` definidas para a propriedade `Value` de `Slider`:

[![Herança de contexto de associação](basic-bindings-images/bindingcontextinheritance-small.png "Herança de contexto de associação")](basic-bindings-images/bindingcontextinheritance-large.png#lightbox "Herança de contexto de associação")

No [próximo artigo](binding-mode.md), você verá como o *modo de associação* pode alterar o fluxo de dados entre objetos de origem e destino.

## <a name="related-links"></a>Links relacionados

- [Demonstrações de associação de dados (amostra)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/databindingdemos)
- [Capítulo de vinculação de dados do Xamarin.Forms livro](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter16.md)

## <a name="related-video"></a>Vídeo relacionados

> [!Video https://channel9.msdn.com/Shows/XamarinShow/XamarinForms-101-Data-Binding/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
