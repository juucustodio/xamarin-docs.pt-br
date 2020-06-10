---
Título: "introdução aos Xamarin.Forms estilos" Descrição: "os estilos permitem que a aparência de elementos visuais seja personalizada. Os estilos são definidos para um tipo específico e contêm valores para as propriedades disponíveis nesse tipo. "
MS. Prod: xamarin MS. AssetID: 3FF899C0-6CFB-4C1D-837D-9E9E10181967 MS. Technology: xamarin-Forms autor: davidbritch MS. Author: dabritch MS. Date: 04/27/2016 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="introduction-to-xamarinforms-styles"></a>Introdução aos Xamarin.Forms estilos

_Os estilos permitem que a aparência de elementos visuais seja personalizada. Os estilos são definidos para um tipo específico e contêm valores para as propriedades disponíveis nesse tipo._

Xamarin.Formsos aplicativos geralmente contêm vários controles que têm uma aparência idêntica. Por exemplo, um aplicativo pode ter várias [`Label`](xref:Xamarin.Forms.Label) instâncias que têm as mesmas opções de fonte e opções de layout, conforme mostrado no exemplo de código XAML a seguir:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
    xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
    x:Class="Styles.NoStylesPage"
    Title="No Styles"
    IconImageSource="xaml.png">
    <ContentPage.Content>
        <StackLayout Padding="0,20,0,0">
            <Label Text="These labels"
                   HorizontalOptions="Center"
                   VerticalOptions="CenterAndExpand"
                   FontSize="Large" />
            <Label Text="are not"
                   HorizontalOptions="Center"
                   VerticalOptions="CenterAndExpand"
                   FontSize="Large" />
            <Label Text="using styles"
                   HorizontalOptions="Center"
                   VerticalOptions="CenterAndExpand"
                   FontSize="Large" />
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

O exemplo de código a seguir mostra a página equivalente criada em C#:

```csharp
public class NoStylesPageCS : ContentPage
{
    public NoStylesPageCS ()
    {
        Title = "No Styles";
        IconImageSource = "csharp.png";
        Padding = new Thickness (0, 20, 0, 0);

        Content = new StackLayout {
            Children = {
                new Label {
                    Text = "These labels",
                    HorizontalOptions = LayoutOptions.Center,
                    VerticalOptions = LayoutOptions.CenterAndExpand,
                    FontSize = Device.GetNamedSize (NamedSize.Large, typeof(Label))
                },
                new Label {
                    Text = "are not",
                    HorizontalOptions = LayoutOptions.Center,
                    VerticalOptions = LayoutOptions.CenterAndExpand,
                    FontSize = Device.GetNamedSize (NamedSize.Large, typeof(Label))
                },
                new Label {
                    Text = "using styles",
                    HorizontalOptions = LayoutOptions.Center,
                    VerticalOptions = LayoutOptions.CenterAndExpand,
                    FontSize = Device.GetNamedSize (NamedSize.Large, typeof(Label))
                }
            }
        };
    }
}
```

Cada [`Label`](xref:Xamarin.Forms.Label) instância tem valores de propriedade idênticos para controlar a aparência do texto exibido pelo `Label` . Isso resulta na aparência mostrada nas capturas de tela seguir:

[![Aparência de rótulo sem estilos](introduction-images/no-styles.png)](introduction-images/no-styles-large.png#lightbox)

Definir a aparência de cada controle individual pode ser repetitivo e propenso a erros. Em vez disso, um estilo pode ser criado para definir a aparência e, em seguida, aplicado aos controles necessários.

## <a name="create-a-style"></a>Criar um estilo

A [`Style`](xref:Xamarin.Forms.Style) classe agrupa uma coleção de valores de propriedade em um objeto que pode ser aplicada a várias instâncias de elementos visuais. Isso ajuda a reduzir a marcação repetitiva e permite que a aparência de aplicativos seja alterada com mais facilidade.

Embora os estilos tenham sido projetados principalmente para aplicativos baseados em XAML, eles também podem ser criados em C#:

- [`Style`](xref:Xamarin.Forms.Style)as instâncias criadas em XAML normalmente são definidas em um [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) que é atribuído à [`Resources`](xref:Xamarin.Forms.VisualElement.Resources) coleção de um controle, página ou à [`Resources`](xref:Xamarin.Forms.Application.Resources) coleção do aplicativo.
- [`Style`](xref:Xamarin.Forms.Style)as instâncias criadas em C# normalmente são definidas na classe da página ou em uma classe que pode ser acessada globalmente.

Escolhendo onde definir um [`Style`](xref:Xamarin.Forms.Style) impacto onde ele pode ser usado:

- [`Style`](xref:Xamarin.Forms.Style)as instâncias definidas no nível de controle só podem ser aplicadas ao controle e a seus filhos.
- [`Style`](xref:Xamarin.Forms.Style)as instâncias definidas no nível de página só podem ser aplicadas à página e a seus filhos.
- [`Style`](xref:Xamarin.Forms.Style)as instâncias definidas no nível do aplicativo podem ser aplicadas em todo o aplicativo.

Cada [`Style`](xref:Xamarin.Forms.Style) instância contém uma coleção de um ou mais [`Setter`](xref:Xamarin.Forms.Setter) objetos, com cada `Setter` um com um [`Property`](xref:Xamarin.Forms.Setter.Property) e um [`Value`](xref:Xamarin.Forms.Setter.Value) . O `Property` é o nome da propriedade vinculável do elemento ao qual o estilo é aplicado e o `Value` é o valor aplicado à propriedade.

Cada [`Style`](xref:Xamarin.Forms.Style) instância pode ser *explícita*ou *implícita*:

- Uma *explicit* [`Style`](xref:Xamarin.Forms.Style) instância explícita é definida especificando um [`TargetType`](xref:Xamarin.Forms.Style.TargetType) valor e um e `x:Key` definindo a propriedade do elemento de destino [`Style`](xref:Xamarin.Forms.NavigableElement.Style) como a `x:Key` referência. Para obter mais informações sobre estilos *explícitos* , consulte [estilos explícitos](~/xamarin-forms/user-interface/styles/explicit.md).
- Uma instância *implícita* [`Style`](xref:Xamarin.Forms.Style) é definida especificando apenas um [`TargetType`](xref:Xamarin.Forms.Style.TargetType) . A `Style` instância será aplicada automaticamente a todos os elementos desse tipo. Observe que as subclasses de não `TargetType` têm automaticamente a `Style` aplicação. Para obter mais informações sobre estilos *implícitos* , consulte [estilos implícitos](~/xamarin-forms/user-interface/styles/implicit.md).

Ao criar um [`Style`](xref:Xamarin.Forms.Style) , a [`TargetType`](xref:Xamarin.Forms.Style.TargetType) propriedade é sempre necessária. O exemplo de código a seguir mostra um estilo *explícito* (Observe o `x:Key` ) criado em XAML:

```xaml
<Style x:Key="labelStyle" TargetType="Label">
    <Setter Property="HorizontalOptions" Value="Center" />
    <Setter Property="VerticalOptions" Value="CenterAndExpand" />
    <Setter Property="FontSize" Value="Large" />
</Style>
```

Para aplicar um `Style` , o objeto de destino deve ser um [`VisualElement`](xref:Xamarin.Forms.VisualElement) que corresponda ao [`TargetType`](xref:Xamarin.Forms.Style.TargetType) valor da Propriedade do `Style` , conforme mostrado no exemplo de código XAML a seguir:

```xaml
<Label Text="Demonstrating an explicit style" Style="{StaticResource labelStyle}" />
```

Estilos inferiores na hierarquia de exibição têm precedência sobre aqueles definidos acima. Por exemplo, definir um [`Style`](xref:Xamarin.Forms.Style) que define [`Label.TextColor`](xref:Xamarin.Forms.Label.TextColor) como `Red` no nível do aplicativo será substituído por um estilo de nível de página que define `Label.TextColor` como `Green` . Da mesma forma, um estilo de nível de página será substituído por um estilo de nível de controle. Além disso, se `Label.TextColor` o for definido diretamente em uma propriedade de controle, isso terá precedência sobre qualquer estilo.

Os artigos nesta seção demonstram e explicam como criar e aplicar estilos *explícitos* e *implícitos* , como criar estilos globais, herança de estilo, como responder a alterações de estilo em tempo de execução e como usar os estilos internos incluídos no Xamarin.Forms .

> [!NOTE]
> **O que é StyleID?**
>
> Antes de Xamarin.Forms 2,2, a [`StyleId`](xref:Xamarin.Forms.Element.StyleId) propriedade foi usada para identificar elementos individuais em um aplicativo para identificação no teste de interface do usuário e em mecanismos de tema como Pixate. No entanto, Xamarin.Forms 2,2 introduziu a [`AutomationId`](xref:Xamarin.Forms.Element.AutomationId) propriedade, que substituiu a [`StyleId`](xref:Xamarin.Forms.Element.StyleId) propriedade.

## <a name="related-links"></a>Links relacionados

- [Extensões de marcação do XAML](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [Estilo](xref:Xamarin.Forms.Style)
- [Setter](xref:Xamarin.Forms.Setter)
