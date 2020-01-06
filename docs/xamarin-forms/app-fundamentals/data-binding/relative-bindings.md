---
title: Associações relativas ao Xamarin. Forms
description: Este artigo explica como criar associações relativas usando a extensão de marcação RelativeSource para definir a origem da associação em relação à posição do destino da associação.
ms.prod: xamarin
ms.assetid: CC64BB1D-8303-46B1-94B6-4EF2F20317A8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/04/2019
ms.openlocfilehash: e115014728cce9252a92740b6db5beab582f61ed
ms.sourcegitcommit: d0e6436edbf7c52d760027d5e0ccaba2531d9fef
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75489863"
---
# <a name="xamarinforms-relative-bindings"></a>Associações relativas ao Xamarin. Forms

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/databindingdemos)

As associações relativas fornecem a capacidade de definir a origem da associação em relação à posição do destino da associação. Eles são criados com a extensão de marcação `RelativeSource` e definidos como a propriedade `Source` de uma expressão de associação.

A extensão de marcação de `RelativeSource` é suportada pela classe `RelativeSourceExtension`, que define as seguintes propriedades:

- `Mode`, do tipo `RelativeBindingSourceMode`, descreve o local da origem da associação em relação à posição do destino da associação.
- `AncestorLevel`, do tipo `int`, um nível ancestral opcional a ser pesquisado quando a propriedade `Mode` for `FindAncestor`. Um `AncestorLevel` de `n` ignora `n-1` instâncias do `AncestorType`.
- `AncestorType`, do tipo `Type`, o tipo de ancestral a ser procurado, quando a propriedade `Mode` for `FindAncestor`.

> [!NOTE]
> O analisador XAML permite que a classe `RelativeSourceExtension` seja abreviada como `RelativeSource`.

A propriedade `Mode` deve ser definida como um dos membros da enumeração `RelativeBindingSourceMode`:

- `TemplatedParent` indica o elemento no qual o modelo, no qual o elemento vinculado existe, é aplicado. Para obter mais informações, consulte [associar a um modelo pai](#bind-to-a-templated-parent).
- `Self` indica o elemento no qual a associação está sendo definida, permitindo que você vincule uma propriedade desse elemento a outra propriedade no mesmo elemento. Para obter mais informações, consulte [associar a si mesmo](#bind-to-self).
- `FindAncestor` indica o ancestral na árvore visual do elemento associado. Esse modo deve ser usado para associar a um controle ancestral representado pela propriedade `AncestorType`. Para obter mais informações, consulte [associar a um ancestral](#bind-to-an-ancestor).
- `FindAncestorBindingContext` indica a `BindingContext` do ancestral na árvore visual do elemento associado. Esse modo deve ser usado para associar ao `BindingContext` de um ancestral representado pela propriedade `AncestorType`. Para obter mais informações, consulte [associar a um ancestral](#bind-to-an-ancestor).

A propriedade `Mode` é a propriedade content da classe `RelativeSourceExtension`. Portanto, para expressões de marcação XAML expressas com chaves, você pode eliminar a `Mode=` parte da expressão.

Para obter mais informações sobre extensões de marcação do Xamarin. Forms, consulte [extensões de marcação XAML](~/xamarin-forms/xaml/markup-extensions/index.md).

## <a name="bind-to-self"></a>Associar a si mesmo

O modo de associação relativa `Self` é usado para associar uma propriedade de um elemento a outra propriedade no mesmo elemento:

```xaml
<BoxView Color="Red"
         WidthRequest="200"
         HeightRequest="{Binding Source={RelativeSource Self}, Path=WidthRequest}"
         HorizontalOptions="Center" />
```

Neste exemplo, o [`BoxView`](xref:Xamarin.Forms.BoxView) define sua propriedade `WidthRequest` como um tamanho fixo, e a propriedade `HeightRequest` associa-se à propriedade `WidthRequest`. Portanto, ambas as propriedades são iguais e, portanto, um quadrado é desenhado:

[![Captura de tela de uma associação relativa de Automodo, no iOS e no Android](relative-bindings-images/self-relative-binding.png "Modo de associação auto-relativo")](relative-bindings-images/self-relative-binding-large.png#lightbox "Modo de associação auto-relativo")

> [!IMPORTANT]
> Ao associar uma propriedade de um elemento a outra propriedade no mesmo elemento, as propriedades devem ser do mesmo tipo. Como alternativa, você pode especificar um conversor na associação para converter o valor.

Um uso comum desse modo de ligação é definir o `BindingContext` de um objeto como uma propriedade em si. O código a seguir mostra um exemplo disso:

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

Neste exemplo, a `BindingContext` da página é definida como a propriedade `DefaultViewModel` de si mesma. Essa propriedade é definida no arquivo code-behind para a página e fornece uma instância de ViewModel. O [`ListView`](xref:Xamarin.Forms.ListView) é associado à propriedade `Employees` do ViewModel.

## <a name="bind-to-an-ancestor"></a>Associar a um ancestral

Os modos de associação `FindAncestor` e `FindAncestorBindingContext` são usados para associar a elementos pai, de um determinado tipo, na árvore visual. O modo de `FindAncestor` é usado para associar a um elemento pai, que deriva do tipo [`Element`](xref:Xamarin.Forms.Element) . O modo de `FindAncestorBindingContext` é usado para associar ao `BindingContext` de um elemento pai.

> [!WARNING]
> A propriedade `AncestorType` deve ser definida como um `Type` ao usar os modos de associação `FindAncestor` e `FindAncestorBindingContext` relativa, caso contrário, uma `XamlParseException` será lançada.

Se a propriedade `Mode` não estiver definida explicitamente, definir a propriedade `AncestorType` como um tipo que deriva de [`Element`](xref:Xamarin.Forms.Element) definirá implicitamente a propriedade `Mode` como `FindAncestor`. Da mesma forma, definir a propriedade `AncestorType` como um tipo que não é derivado de `Element` definirá implicitamente a propriedade `Mode` como `FindAncestorBindingContext`.

> [!NOTE]
> As associações relativas que usam o modo de `FindAncestorBindingContext` serão reaplicadas quando a `BindingContext` de quaisquer ancestrais mudarem.

O XAML a seguir mostra um exemplo em que a propriedade `Mode` será definida implicitamente como `FindAncestorBindingContext`:

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

Neste exemplo, a `BindingContext` da página é definida como a propriedade `DefaultViewModel` de si mesma. Essa propriedade é definida no arquivo code-behind para a página e fornece uma instância de ViewModel. O [`ListView`](xref:Xamarin.Forms.ListView) é associado à propriedade `Employees` do ViewModel. O [`DataTemplate`](xref:Xamarin.Forms.DataTemplate), que define a aparência de cada item na `ListView`, contém uma [`Button`](xref:Xamarin.Forms.Button). A propriedade `Command` do botão é associada à `DeleteEmployeeCommand` no ViewModel de seu pai. Tocar em um `Button` exclui um funcionário:

[![Captura de tela de uma associação relativa de modo FindAncestor, no iOS e no Android](relative-bindings-images/findancestor-relative-binding.png "Modo de associação relativa de FindAncestor")](relative-bindings-images/findancestor-relative-binding-large.png#lightbox "Modo de associação relativa de FindAncestor")

Além disso, a propriedade opcional `AncestorLevel` pode ajudar a desambiguar a pesquisa de ancestral em cenários em que há possivelmente mais de um ancestral desse tipo na árvore visual:

```xaml
<Label Text="{Binding Source={RelativeSource AncestorType={x:Type Entry}, AncestorLevel=2}, Path=Text}" />
```

Neste exemplo, a propriedade `Label.Text` é vinculada à propriedade `Text` da segunda [`Entry`](xref:Xamarin.Forms.Entry) que é encontrada no caminho para cima, começando no elemento de destino da associação.

> [!NOTE]
> A propriedade `AncestorLevel` deve ser definida como 1 para localizar o ancestral mais próximo ao elemento de destino de associação.

## <a name="bind-to-a-templated-parent"></a>Associar a um pai com modelo

O modo de associação relativa de `TemplatedParent` é usado para associar de um modelo de controle à instância de objeto de tempo de execução à qual o modelo é aplicado (conhecido como pai de modelo). Esse modo só será aplicável se a associação relativa estiver dentro de um modelo de controle e for semelhante a definir um `TemplateBinding`.

O XAML a seguir mostra um exemplo do modo de associação relativa do `TemplatedParent`:

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

Neste exemplo, o [`Frame`](xref:Xamarin.Forms.Frame), que é o elemento raiz da `ControlTemplate`, tem seu `BindingContext` definido como a instância do objeto de tempo de execução à qual o modelo é aplicado. Portanto, o `Frame` e seus filhos resolvem suas expressões de associação em relação às propriedades de cada objeto de `CardView`:

[![Captura de tela de uma associação relativa do modo TemplatedParent, no iOS e no Android](relative-bindings-images/templatedparent-relative-binding.png "Modo de associação relativa a TemplatedParent")](relative-bindings-images/templatedparent-relative-binding-large.png#lightbox "Modo de associação relativa a TemplatedParent")

Para obter mais informações sobre modelos de controle, consulte [Xamarin. Forms Control templates](~/xamarin-forms/app-fundamentals/templates/control-templates/index.md).

## <a name="related-links"></a>Links relacionados

- [Demonstrações de associação de dados (amostra)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/databindingdemos)
- [Extensões de marcação XAML](~/xamarin-forms/xaml/markup-extensions/index.md)
- [Modelos de controle do Xamarin. Forms](~/xamarin-forms/app-fundamentals/templates/control-templates/index.md)
