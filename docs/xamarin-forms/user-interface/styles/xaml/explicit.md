---
title: "estilos explícitos em Xamarin.Forms " Descrição: "um estilo explícito é aquele aplicado seletivamente a controles definindo suas propriedades de estilo. Este artigo explica como consumir estilos explícitos em um Xamarin.Forms aplicativo.
MS. Prod: xamarin MS. AssetID: C0DF9F8F-B431-4374-A574-325BC3C41A3B MS. Technology: xamarin-Forms autor: davidbritch MS. Author: dabritch MS. Date: 02/17/2016 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="explicit-styles-in-xamarinforms"></a>Estilos explícitos emXamarin.Forms

[![Baixar exemplo ](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-styles-basicstyles)

_Um estilo explícito é aquele aplicado seletivamente aos controles definindo suas propriedades de estilo._

## <a name="create-an-explicit-style-in-xaml"></a>Criar um estilo explícito em XAML

Para declarar um [`Style`](xref:Xamarin.Forms.Style) no nível da página, um [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) deve ser adicionado à página e uma ou mais `Style` declarações podem ser incluídas no `ResourceDictionary` . Um `Style` é *explicitado* , dando a sua declaração um `x:Key` atributo, que fornece uma chave descritiva no `ResourceDictionary` . Estilos *explícitos* devem ser aplicados a elementos visuais específicos definindo suas [`Style`](xref:Xamarin.Forms.NavigableElement.Style) Propriedades.

O exemplo de código a seguir mostra estilos *explícitos* declarados em XAML em uma página `ResourceDictionary` e aplicados às instâncias da página [`Label`](xref:Xamarin.Forms.Label) :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="Styles.ExplicitStylesPage" Title="Explicit" IconImageSource="xaml.png">
    <ContentPage.Resources>
        <ResourceDictionary>
            <Style x:Key="labelRedStyle" TargetType="Label">
                <Setter Property="HorizontalOptions"
                        Value="Center" />
                <Setter Property="VerticalOptions"
                        Value="CenterAndExpand" />
                <Setter Property="FontSize" Value="Large" />
                <Setter Property="TextColor" Value="Red" />
            </Style>
            <Style x:Key="labelGreenStyle" TargetType="Label">
                ...
                <Setter Property="TextColor" Value="Green" />
            </Style>
            <Style x:Key="labelBlueStyle" TargetType="Label">
                ...
                <Setter Property="TextColor" Value="Blue" />
            </Style>
        </ResourceDictionary>
    </ContentPage.Resources>
    <ContentPage.Content>
        <StackLayout Padding="0,20,0,0">
            <Label Text="These labels"
                   Style="{StaticResource labelRedStyle}" />
            <Label Text="are demonstrating"
                   Style="{StaticResource labelGreenStyle}" />
            <Label Text="explicit styles,"
                   Style="{StaticResource labelBlueStyle}" />
            <Label Text="and an explicit style override"
                   Style="{StaticResource labelBlueStyle}"
                   TextColor="Teal" />
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

O [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) define três estilos *explícitos* que são aplicados às instâncias da página [`Label`](xref:Xamarin.Forms.Label) . Cada uma `Style` é usada para exibir texto em uma cor diferente, enquanto também define as opções de tamanho da fonte e layout horizontal e vertical. Cada `Style` um é aplicado a um diferente `Label` definindo suas [`Style`](xref:Xamarin.Forms.NavigableElement.Style) Propriedades usando a `StaticResource` extensão de marcação. Isso resulta na aparência mostrada nas capturas de tela seguir:

[![Exemplo de estilos explícitos](explicit-images/explicit-styles.png)](explicit-images/explicit-styles-large.png#lightbox)

Além disso, o final [`Label`](xref:Xamarin.Forms.Label) tem um [`Style`](xref:Xamarin.Forms.Style) aplicado a ele, mas também substitui a [`TextColor`](xref:Xamarin.Forms.Label.TextColor) Propriedade por um `Color` valor diferente.

### <a name="create-an-explicit-style-at-the-control-level"></a>Criar um estilo explícito no nível de controle

Além de criar estilos *explícitos* no nível de página, eles também podem ser criados no nível de controle, conforme mostrado no exemplo de código a seguir:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="Styles.ExplicitStylesPage" Title="Explicit" IconImageSource="xaml.png">
    <ContentPage.Content>
        <StackLayout Padding="0,20,0,0">
            <StackLayout.Resources>
                <ResourceDictionary>
                    <Style x:Key="labelRedStyle" TargetType="Label">
                      ...
                    </Style>
                    ...
                </ResourceDictionary>
            </StackLayout.Resources>
            <Label Text="These labels" Style="{StaticResource labelRedStyle}" />
            ...
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

Neste exemplo, as instâncias *explícitas* [`Style`](xref:Xamarin.Forms.Style) são atribuídas à [`Resources`](xref:Xamarin.Forms.VisualElement.Resources) coleção do [`StackLayout`](xref:Xamarin.Forms.StackLayout) controle. Os estilos podem ser aplicados ao controle e a seus filhos.

Para obter informações sobre como criar estilos em um aplicativo [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) , consulte [estilos globais](~/xamarin-forms/user-interface/styles/application.md).

## <a name="create-an-explicit-style-in-c35"></a>Criar um estilo explícito em C&#35;

[`Style`](xref:Xamarin.Forms.Style)as instâncias podem ser adicionadas à coleção de uma página [`Resources`](xref:Xamarin.Forms.VisualElement.Resources) em C# criando um novo [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) e, em seguida, adicionando as `Style` instâncias `ResourceDictionary` ao, conforme mostrado no exemplo de código a seguir:

```csharp
public class ExplicitStylesPageCS : ContentPage
{
    public ExplicitStylesPageCS ()
    {
        var labelRedStyle = new Style (typeof(Label)) {
            Setters = {
                ...
                new Setter { Property = Label.TextColorProperty, Value = Color.Red    }
            }
        };
        var labelGreenStyle = new Style (typeof(Label)) {
            Setters = {
                ...
                new Setter { Property = Label.TextColorProperty, Value = Color.Green }
            }
        };
        var labelBlueStyle = new Style (typeof(Label)) {
            Setters = {
                ...
                new Setter { Property = Label.TextColorProperty, Value = Color.Blue }
            }
        };

        Resources = new ResourceDictionary ();
        Resources.Add ("labelRedStyle", labelRedStyle);
        Resources.Add ("labelGreenStyle", labelGreenStyle);
        Resources.Add ("labelBlueStyle", labelBlueStyle);
        ...

        Content = new StackLayout {
            Children = {
                new Label { Text = "These labels",
                            Style = (Style)Resources ["labelRedStyle"] },
                new Label { Text = "are demonstrating",
                            Style = (Style)Resources ["labelGreenStyle"] },
                new Label { Text = "explicit styles,",
                            Style = (Style)Resources ["labelBlueStyle"] },
                new Label {    Text = "and an explicit style override",
                            Style = (Style)Resources ["labelBlueStyle"], TextColor = Color.Teal }
            }
        };
    }
}
```

O construtor define três estilos *explícitos* que são aplicados às instâncias da página [`Label`](xref:Xamarin.Forms.Label) . Cada *Explicit* [`Style`](xref:Xamarin.Forms.Style) é adicionado ao [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) usando o [`Add`](xref:Xamarin.Forms.ResourceDictionary.Add(System.String,System.Object)) método, especificando uma `key` cadeia de caracteres para fazer referência à `Style` instância. Cada `Style` um é aplicado a um diferente `Label` definindo suas [`Style`](xref:Xamarin.Forms.NavigableElement.Style) Propriedades.

No entanto, não há nenhuma vantagem em usar [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) aqui. Em vez disso, [`Style`](xref:Xamarin.Forms.Style) as instâncias podem ser atribuídas diretamente às [`Style`](xref:Xamarin.Forms.NavigableElement.Style) Propriedades dos elementos visuais necessários e o `ResourceDictionary` pode ser removido, conforme mostrado no exemplo de código a seguir:

```csharp
public class ExplicitStylesPageCS : ContentPage
{
    public ExplicitStylesPageCS ()
    {
        var labelRedStyle = new Style (typeof(Label)) {
            ...
        };
        var labelGreenStyle = new Style (typeof(Label)) {
            ...
        };
        var labelBlueStyle = new Style (typeof(Label)) {
            ...
        };
        ...
        Content = new StackLayout {
            Children = {
                new Label { Text = "These labels", Style = labelRedStyle },
                new Label { Text = "are demonstrating", Style = labelGreenStyle },
                new Label { Text = "explicit styles,", Style = labelBlueStyle },
                new Label { Text = "and an explicit style override", Style = labelBlueStyle,
                            TextColor = Color.Teal }
            }
        };
    }
}
```

O construtor define três estilos *explícitos* que são aplicados às instâncias da página [`Label`](xref:Xamarin.Forms.Label) . Cada uma `Style` é usada para exibir texto em uma cor diferente, enquanto também define as opções de tamanho da fonte e layout horizontal e vertical. Cada `Style` um é aplicado a um diferente `Label` definindo suas [`Style`](xref:Xamarin.Forms.NavigableElement.Style) Propriedades. Além disso, o final `Label` tem um `Style` aplicado a ele, mas também substitui a `TextColor` Propriedade por um `Color` valor diferente.

## <a name="related-links"></a>Links relacionados

- [Extensões de marcação do XAML](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [Estilos básicos (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-styles-basicstyles)
- [Trabalhando com estilos (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithstyles)
- [ResourceDictionary](xref:Xamarin.Forms.ResourceDictionary)
- [Estilo](xref:Xamarin.Forms.Style)
- [Setter](xref:Xamarin.Forms.Setter)
