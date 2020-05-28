---
title: Xamarin.FormsAssociações relativas
description: ''
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 8d4e2696e6027f07b7b8e638cd1e0f1d65a5503d
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/28/2020
ms.locfileid: "84139705"
---
# <a name="xamarinforms-relative-bindings"></a>Xamarin.FormsAssociações relativas

[![Baixar exemplo ](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/databindingdemos)

As associações relativas fornecem a capacidade de definir a origem da associação em relação à posição do destino da associação. Eles são criados com a `RelativeSource` extensão de marcação e definidos como a `Source` propriedade de uma expressão de associação.

A `RelativeSource` extensão de marcação é suportada pela `RelativeSourceExtension` classe, que define as seguintes propriedades:

- `Mode`, do tipo `RelativeBindingSourceMode` , descreve o local da origem da associação em relação à posição do destino da associação.
- `AncestorLevel`, do tipo `int` , um nível ancestral opcional para procurar, quando a `Mode` propriedade é `FindAncestor` . Um `AncestorLevel` de `n` ignora `n-1` instâncias do `AncestorType` .
- `AncestorType`, do tipo `Type` , o tipo de ancestral a ser procurado, quando a `Mode` propriedade é `FindAncestor` .

> [!NOTE]
> O analisador XAML permite que a `RelativeSourceExtension` classe seja abreviada como `RelativeSource` .

A `Mode` propriedade deve ser definida como um dos `RelativeBindingSourceMode` membros da enumeração:

- `TemplatedParent`indica o elemento no qual o modelo, no qual o elemento vinculado existe, é aplicado. Para obter mais informações, consulte [associar a um modelo pai](#bind-to-a-templated-parent).
- `Self`indica o elemento no qual a associação está sendo definida, permitindo que você vincule uma propriedade desse elemento a outra propriedade no mesmo elemento. Para obter mais informações, consulte [associar a si mesmo](#bind-to-self).
- `FindAncestor`indica o ancestral na árvore visual do elemento associado. Esse modo deve ser usado para associar a um controle ancestral representado pela `AncestorType` propriedade. Para obter mais informações, consulte [associar a um ancestral](#bind-to-an-ancestor).
- `FindAncestorBindingContext`indica o `BindingContext` do ancestral na árvore visual do elemento associado. Esse modo deve ser usado para associar ao `BindingContext` de um ancestral representado pela `AncestorType` propriedade. Para obter mais informações, consulte [associar a um ancestral](#bind-to-an-ancestor).

A `Mode` propriedade é a propriedade content da `RelativeSourceExtension` classe. Portanto, para expressões de marcação XAML expressas com chaves, você pode eliminar a `Mode=` parte da expressão.

Para obter mais informações sobre Xamarin.Forms extensões de marcação, consulte [extensões de marcação XAML](~/xamarin-forms/xaml/markup-extensions/index.md).

## <a name="bind-to-self"></a>Associar a si mesmo

O `Self` modo de associação relativo é usado para associar uma propriedade de um elemento a outra propriedade no mesmo elemento:

```xaml
<BoxView Color="Red"
         WidthRequest="200"
         HeightRequest="{Binding Source={RelativeSource Self}, Path=WidthRequest}"
         HorizontalOptions="Center" />
```

Neste exemplo, [`BoxView`](xref:Xamarin.Forms.BoxView) define sua `WidthRequest` propriedade como um tamanho fixo e a `HeightRequest` propriedade é associada à `WidthRequest` propriedade. Portanto, ambas as propriedades são iguais e, portanto, um quadrado é desenhado:

[![Captura de tela de uma associação relativa de Automodo, no iOS e no Android](relative-bindings-images/self-relative-binding.png "Modo de associação auto-relativo")](relative-bindings-images/self-relative-binding-large.png#lightbox "Modo de associação auto-relativo")

> [!IMPORTANT]
> Ao associar uma propriedade de um elemento a outra propriedade no mesmo elemento, as propriedades devem ser do mesmo tipo. Como alternativa, você pode especificar um conversor na associação para converter o valor.

Um uso comum desse modo de associação é definir um objeto `BindingContext` como uma propriedade em si. O código a seguir mostra um exemplo disso:

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

Neste exemplo, a `BindingContext` da página é definida como a `DefaultViewModel` propriedade de si mesma. Essa propriedade é definida no arquivo code-behind para a página e fornece uma instância de ViewModel. O é [`ListView`](xref:Xamarin.Forms.ListView) associado à `Employees` Propriedade do ViewModel.

## <a name="bind-to-an-ancestor"></a>Associar a um ancestral

Os `FindAncestor` `FindAncestorBindingContext` modos de associação relativo e são usados para associar a elementos pai, de um determinado tipo, na árvore visual. O `FindAncestor` modo é usado para associar a um elemento pai, que deriva do [`Element`](xref:Xamarin.Forms.Element) tipo. O `FindAncestorBindingContext` modo é usado para associar ao `BindingContext` de um elemento pai.

> [!WARNING]
> A `AncestorType` propriedade deve ser definida como um `Type` ao usar os `FindAncestor` `FindAncestorBindingContext` modos de associação e, caso contrário, `XamlParseException` será gerada uma.

Se a `Mode` propriedade não estiver definida explicitamente, definir a `AncestorType` propriedade como um tipo derivado de [`Element`](xref:Xamarin.Forms.Element) irá definir implicitamente a `Mode` propriedade como `FindAncestor` . Da mesma forma, definir a `AncestorType` propriedade para um tipo que não derive `Element` irá definir implicitamente a `Mode` propriedade como `FindAncestorBindingContext` .

> [!NOTE]
> As associações relativas que usam o `FindAncestorBindingContext` modo serão reaplicadas quando o `BindingContext` de qualquer um dos ancestrais for alterado.

O XAML a seguir mostra um exemplo em que a `Mode` propriedade será definida implicitamente como `FindAncestorBindingContext` :

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

Neste exemplo, a `BindingContext` da página é definida como a `DefaultViewModel` propriedade de si mesma. Essa propriedade é definida no arquivo code-behind para a página e fornece uma instância de ViewModel. O é [`ListView`](xref:Xamarin.Forms.ListView) associado à `Employees` Propriedade do ViewModel. O [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) , que define a aparência de cada item no `ListView` , contém um [`Button`](xref:Xamarin.Forms.Button) . A propriedade do botão `Command` está associada ao `DeleteEmployeeCommand` seu ViewModel do pai. Tocar em uma `Button` exclui um funcionário:

[![Captura de tela de uma associação relativa de modo FindAncestor, no iOS e no Android](relative-bindings-images/findancestor-relative-binding.png "Modo de associação relativa de FindAncestor")](relative-bindings-images/findancestor-relative-binding-large.png#lightbox "Modo de associação relativa de FindAncestor")

Além disso, a `AncestorLevel` propriedade opcional pode ajudar a desambiguar a pesquisa de ancestral em cenários em que há possivelmente mais de um ancestral desse tipo na árvore visual:

```xaml
<Label Text="{Binding Source={RelativeSource AncestorType={x:Type Entry}, AncestorLevel=2}, Path=Text}" />
```

Neste exemplo, a `Label.Text` propriedade é vinculada à `Text` Propriedade do segundo [`Entry`](xref:Xamarin.Forms.Entry) que é encontrada no caminho para cima, começando pelo elemento de destino da associação.

> [!NOTE]
> A `AncestorLevel` propriedade deve ser definida como 1 para localizar o ancestral mais próximo ao elemento de destino de associação.

## <a name="bind-to-a-templated-parent"></a>Associar a um pai com modelo

O `TemplatedParent` modo de associação relativo é usado para associar de um modelo de controle à instância do objeto de tempo de execução à qual o modelo é aplicado (conhecido como pai do modelo). Esse modo só será aplicável se a associação relativa estiver dentro de um modelo de controle e for semelhante à definição de um `TemplateBinding` .

O XAML a seguir mostra um exemplo do `TemplatedParent` modo de associação relativo:

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

Neste exemplo, o [`Frame`](xref:Xamarin.Forms.Frame) , que é o elemento raiz do `ControlTemplate` , tem seu `BindingContext` definido como a instância do objeto de tempo de execução à qual o modelo é aplicado. Portanto, o `Frame` e seus filhos resolvem suas expressões de associação em relação às propriedades de cada `CardView` objeto:

[![Captura de tela de uma associação relativa do modo TemplatedParent, no iOS e no Android](relative-bindings-images/templatedparent-relative-binding.png "Modo de associação relativa a TemplatedParent")](relative-bindings-images/templatedparent-relative-binding-large.png#lightbox "Modo de associação relativa a TemplatedParent")

Para obter mais informações sobre modelos de controle, consulte [ Xamarin.Forms modelos de controle](~/xamarin-forms/app-fundamentals/templates/control-template.md).

## <a name="related-links"></a>Links relacionados

- [Demonstrações de associação de dados (amostra)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/databindingdemos)
- [Extensões de marcação do XAML](~/xamarin-forms/xaml/markup-extensions/index.md)
- [Xamarin.FormsModelos de controle](~/xamarin-forms/app-fundamentals/templates/control-template.md)
