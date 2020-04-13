---
title: Associações relativas do Xamarin.Forms
description: Este artigo explica como criar vinculações relativas usando a extensão de marcação RelativeSource para definir a fonte de vinculação em relação à posição do alvo de vinculação.
ms.prod: xamarin
ms.assetid: CC64BB1D-8303-46B1-94B6-4EF2F20317A8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/04/2019
ms.openlocfilehash: 63ce27fc871da12eabb1baad568af167c860926f
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "75955807"
---
# <a name="xamarinforms-relative-bindings"></a>Associações relativas do Xamarin.Forms

[![Baixar](~/media/shared/download.png) amostra Baixar a amostra](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/databindingdemos)

As ligações relativas fornecem a capacidade de definir a fonte de ligação em relação à posição do alvo de ligação. Eles são criados `RelativeSource` com a extensão `Source` de marcação, e definidos como propriedade de uma expressão vinculante.

A `RelativeSource` extensão de marcação `RelativeSourceExtension` é suportada pela classe, que define as seguintes propriedades:

- `Mode`, do `RelativeBindingSourceMode`tipo, descreve a localização da fonte de ligação em relação à posição do alvo de ligação.
- `AncestorLevel`, do `int`tipo , um nível de ancestral opcional `Mode` para `FindAncestor`procurar, quando a propriedade é . Um `AncestorLevel` `n` dos `n-1` exemplos de `AncestorType`saltos do .
- `AncestorType`, do `Type`tipo , o tipo de ancestral a `Mode` procurar, quando a propriedade é `FindAncestor`.

> [!NOTE]
> O analisador XAML `RelativeSourceExtension` permite que a classe `RelativeSource`seja abreviada como .

A `Mode` propriedade deve ser definida `RelativeBindingSourceMode` como um dos membros da enumeração:

- `TemplatedParent`indica o elemento ao qual o modelo, no qual o elemento vinculado existe, é aplicado. Para obter mais informações, consulte [Vincular a um pai modelado](#bind-to-a-templated-parent).
- `Self`indica o elemento no qual a vinculação está sendo definida, permitindo que você vincule uma propriedade desse elemento a outra propriedade no mesmo elemento. Para obter mais informações, consulte [Bind to self](#bind-to-self).
- `FindAncestor`indica o ancestral na árvore visual do elemento vinculado. Este modo deve ser usado para vincular-se a `AncestorType` um controle ancestral representado pela propriedade. Para obter mais informações, consulte [Bind to a a ancer](#bind-to-an-ancestor).
- `FindAncestorBindingContext`indica `BindingContext` o do ancestral na árvore visual do elemento vinculado. Este modo deve ser usado `BindingContext` para se ligar ao `AncestorType` de um ancestral representado pela propriedade. Para obter mais informações, consulte [Bind to a a ancer](#bind-to-an-ancestor).

A `Mode` propriedade é propriedade `RelativeSourceExtension` do conteúdo da classe. Portanto, para expressões de marcação XAML expressas com `Mode=` chaves, você pode eliminar a parte da expressão.

Para obter mais informações sobre as extensões de marcação Xamarin.Forms, consulte [Extensões de marcação XAML](~/xamarin-forms/xaml/markup-extensions/index.md).

## <a name="bind-to-self"></a>Vincule-se a si mesmo

O `Self` modo de vinculação relativa é usado para vincular uma propriedade de um elemento a outra propriedade no mesmo elemento:

```xaml
<BoxView Color="Red"
         WidthRequest="200"
         HeightRequest="{Binding Source={RelativeSource Self}, Path=WidthRequest}"
         HorizontalOptions="Center" />
```

Neste exemplo, [`BoxView`](xref:Xamarin.Forms.BoxView) o `WidthRequest` conjunto de sua propriedade `HeightRequest` para um tamanho `WidthRequest` fixo, e a propriedade se liga ao imóvel. Portanto, ambas as propriedades são iguais e assim um quadrado é desenhado:

[![Captura de tela de uma vinculação relativa do modo Self, no iOS e Android](relative-bindings-images/self-relative-binding.png "Modo de vinculação auto-relativo")](relative-bindings-images/self-relative-binding-large.png#lightbox "Modo de vinculação auto-relativo")

> [!IMPORTANT]
> Ao vincular uma propriedade de um elemento a outra propriedade no mesmo elemento, as propriedades devem ser do mesmo tipo. Alternativamente, você pode especificar um conversor na vinculação para converter o valor.

Um uso comum deste modo de `BindingContext` vinculação é definido um objeto para uma propriedade em si mesmo. O código a seguir mostra um exemplo disso:

```xaml
<ContentPage ...
             BindingContext="{Binding Source={RelativeSource Self}, Path=DefaultViewModel}">
    <StackLayout>
        <ListView ItemsSource="{Binding Employees}">
            ...
        </ListView>
    </StackLayout>
</ContentPage>
```

Neste exemplo, `BindingContext` a página é definida `DefaultViewModel` como propriedade de si mesma. Essa propriedade é definida no arquivo de código atrás da página e fornece uma instância de modelo de exibição. A [`ListView`](xref:Xamarin.Forms.ListView) ligação à `Employees` propriedade do modelo de visualização.

## <a name="bind-to-an-ancestor"></a>Vincule-se a um ancestral

Os `FindAncestor` `FindAncestorBindingContext` modos de ligação relativa e relativos são usados para se ligar aos elementos-pai, de um certo tipo, na árvore visual. O `FindAncestor` modo é usado para se ligar a [`Element`](xref:Xamarin.Forms.Element) um elemento pai, que deriva do tipo. O `FindAncestorBindingContext` modo é usado `BindingContext` para se ligar ao de um elemento pai.

> [!WARNING]
> A `AncestorType` propriedade deve ser `Type` definida `FindAncestor` como `FindAncestorBindingContext` a ao usar os `XamlParseException` modos de ligação relativo e relativa, caso contrário, um é jogado.

Se `Mode` a propriedade não for explicitamente `AncestorType` definida, definir a propriedade [`Element`](xref:Xamarin.Forms.Element) para um `Mode` tipo `FindAncestor`derivado irá definir implicitamente a propriedade para . Da mesma forma, definir `AncestorType` a propriedade para `Element` um tipo `Mode` que `FindAncestorBindingContext`não derivairá definir implicitamente a propriedade para .

> [!NOTE]
> As ligações relativas `FindAncestorBindingContext` que usam o modo `BindingContext` serão reaplicadas quando a de qualquer ancestral mudar.

O XAML a seguir `Mode` mostra um exemplo onde `FindAncestorBindingContext`a propriedade será definida implicitamente para:

```xaml
<ContentPage ...
             BindingContext="{Binding Source={RelativeSource Self}, Path=DefaultViewModel}">
    <StackLayout>
        <ListView ItemsSource="{Binding Employees}">
            <ListView.ItemTemplate>
                <DataTemplate>
                    <ViewCell>
                        <StackLayout Orientation="Horizontal">
                            <Label Text="{Binding Fullname}"
                                   VerticalOptions="Center" />
                            <Button Text="Delete"
                                    Command="{Binding Source={RelativeSource AncestorType={x:Type local:PeopleViewModel}}, Path=DeleteEmployeeCommand}"
                                    CommandParameter="{Binding}"
                                    HorizontalOptions="EndAndExpand" />
                        </StackLayout>
                    </ViewCell>
                </DataTemplate>
            </ListView.ItemTemplate>
        </ListView>
    </StackLayout>
</ContentPage>
```

Neste exemplo, `BindingContext` a página é definida `DefaultViewModel` como propriedade de si mesma. Essa propriedade é definida no arquivo de código atrás da página e fornece uma instância de modelo de exibição. A [`ListView`](xref:Xamarin.Forms.ListView) ligação à `Employees` propriedade do modelo de visualização. O [`DataTemplate`](xref:Xamarin.Forms.DataTemplate), que define a aparência de `ListView`cada [`Button`](xref:Xamarin.Forms.Button)item no , contém um . A propriedade `Command` do botão está `DeleteEmployeeCommand` vinculada ao modelo de visão de seus pais. Tocar `Button` em um exclui um funcionário:

[![Captura de tela de uma vinculação relativa do modo FindAncestor, no iOS e Android](relative-bindings-images/findancestor-relative-binding.png "Modo de vinculação relativa findancestor")](relative-bindings-images/findancestor-relative-binding-large.png#lightbox "Modo de vinculação relativa findancestor")

Além disso, `AncestorLevel` a propriedade opcional pode ajudar a desambiguar a procuração ancestral em cenários onde possivelmente há mais de um ancestral desse tipo na árvore visual:

```xaml
<Label Text="{Binding Source={RelativeSource AncestorType={x:Type Entry}, AncestorLevel=2}, Path=Text}" />
```

Neste exemplo, `Label.Text` a propriedade se `Text` liga à [`Entry`](xref:Xamarin.Forms.Entry) propriedade do segundo que é encontrado no caminho para cima, começando pelo elemento alvo da vinculação.

> [!NOTE]
> A `AncestorLevel` propriedade deve ser definida como 1 para encontrar o ancestral mais próximo do elemento alvo de ligação.

## <a name="bind-to-a-templated-parent"></a>Vincule-se a um pai modelado

O `TemplatedParent` modo de vinculação relativa é usado para vincular de dentro de um modelo de controle à instância do objeto de tempo de execução ao qual o modelo é aplicado (conhecido como o pai modelado). Este modo só é aplicável se a vinculação relativa estiver dentro `TemplateBinding`de um modelo de controle e for semelhante à configuração de um .

O XAML a seguir `TemplatedParent` mostra um exemplo do modo de ligação relativa:

```xaml
<ContentPage ...>
    <ContentPage.Resources>
        <ControlTemplate x:Key="CardViewControlTemplate">
            <Frame BindingContext="{Binding Source={RelativeSource TemplatedParent}}"
                   BackgroundColor="{Binding CardColor}"
                   BorderColor="{Binding BorderColor}"
                   ...>
                <Grid>
                    ...
                    <Label Text="{Binding CardTitle}"
                           ... />
                    <BoxView BackgroundColor="{Binding BorderColor}"
                             ... />
                    <Label Text="{Binding CardDescription}"
                           ... />
                </Grid>
            </Frame>
        </ControlTemplate>
    </ContentPage.Resources>
    <StackLayout>        
        <controls:CardView BorderColor="DarkGray"
                           CardTitle="John Doe"
                           CardDescription="Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nulla elit dolor, convallis non interdum."
                           IconBackgroundColor="SlateGray"
                           IconImageSource="user.png"
                           ControlTemplate="{StaticResource CardViewControlTemplate}" />
        <controls:CardView BorderColor="DarkGray"
                           CardTitle="Jane Doe"
                           CardDescription="Phasellus eu convallis mi. In tempus augue eu dignissim fermentum. Morbi ut lacus vitae eros lacinia."
                           IconBackgroundColor="SlateGray"
                           IconImageSource="user.png"
                           ControlTemplate="{StaticResource CardViewControlTemplate}" />
        <controls:CardView BorderColor="DarkGray"
                           CardTitle="Xamarin Monkey"
                           CardDescription="Aliquam sagittis, odio lacinia fermentum dictum, mi erat scelerisque erat, quis aliquet arcu."
                           IconBackgroundColor="SlateGray"
                           IconImageSource="user.png"
                           ControlTemplate="{StaticResource CardViewControlTemplate}" />
    </StackLayout>
</ContentPage>
```

Neste exemplo, [`Frame`](xref:Xamarin.Forms.Frame)o , que é `ControlTemplate`o elemento `BindingContext` raiz do , tem seu conjunto para a instância do objeto de tempo de execução à qual o modelo é aplicado. Portanto, os `Frame` filhos e seus filhos resolvem suas `CardView` expressões vinculantes contra as propriedades de cada objeto:

[![Captura de tela de um modo TemplatedParent relativa vinculação, no iOS e Android](relative-bindings-images/templatedparent-relative-binding.png "Modo de vinculação relativa do ModeldParent")](relative-bindings-images/templatedparent-relative-binding-large.png#lightbox "Modo de vinculação relativa do ModeldParent")

Para obter mais informações sobre modelos de controle, consulte [Xamarin.Forms Control Templates](~/xamarin-forms/app-fundamentals/templates/control-template.md).

## <a name="related-links"></a>Links relacionados

- [Demonstrações de associação de dados (amostra)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/databindingdemos)
- [Extensões de marcação XAML](~/xamarin-forms/xaml/markup-extensions/index.md)
- [Modelos de controle xamarin.forms](~/xamarin-forms/app-fundamentals/templates/control-template.md)
