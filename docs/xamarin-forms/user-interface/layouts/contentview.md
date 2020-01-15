---
title: ContentView Xamarin. Forms
description: Este artigo explica como usar a classe ContentView para criar um controle personalizado, como o exemplo CardView.
ms.prod: xamarin
ms.assetid: 638402E7-CA44-456B-863B-791F6B6B561D
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 08/14/2019
ms.openlocfilehash: 712aa43fb4959b766786c8fd0969ef2c2c8f00ef
ms.sourcegitcommit: 211fed94fb96127a3e158ae1ff5d7eb831a203d8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/15/2020
ms.locfileid: "75955730"
---
# <a name="xamarinforms-contentview"></a>ContentView Xamarin. Forms

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-contentviewdemos/)

A classe [`ContentView`](xref:Xamarin.Forms.ContentView) Xamarin. Forms é um tipo de `Layout` que contém um único elemento filho e normalmente é usada para criar controles personalizados e reutilizáveis. A classe de `ContentView` herda de [`TemplatedView`](xref:Xamarin.Forms.TemplatedView). Este artigo e um exemplo associado explicam como criar um controle de `CardView` personalizado com base na classe `ContentView`.

A captura de tela a seguir mostra um controle `CardView` derivado da classe `ContentView`:

[captura de tela do aplicativo de exemplo ![CardView](contentview-images/cardview-list-cropped.png)](contentview-images/cardview-list.png#lightbox)

A classe `ContentView` define uma única propriedade:

* [`Content`](xref:Xamarin.Forms.ContentView.Content) é um objeto `View`. Essa propriedade é apoiada por um objeto [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) para que possa ser o destino de associações de dados.

O `ContentView` também herda uma propriedade da classe `TemplatedView`:

* [`ControlTemplate`](xref:Xamarin.Forms.TemplatedView.ControlTemplate) é uma `ControlTemplate` que pode definir ou substituir a aparência do controle.

Para obter mais informações sobre a propriedade `ControlTemplate`, consulte [Personalizar a aparência com um ControlTemplate](#customize-appearance-with-a-controltemplate).

## <a name="create-a-custom-control"></a>Criar um controle personalizado

A classe `ContentView` oferece pouca funcionalidade por si só, mas pode ser usada para criar um controle personalizado. O projeto de exemplo define um controle de `CardView` – um elemento de interface do usuário que exibe uma imagem, um título e uma descrição em um layout do tipo cartão.

O processo para criar um controle personalizado é:

1. Crie uma nova classe usando o modelo de `ContentView` no Visual Studio 2019.
1. Defina quaisquer propriedades ou eventos exclusivos no arquivo code-behind para o novo controle personalizado.
1. Crie a interface do usuário para o controle personalizado.

> [!NOTE]
> É possível criar um controle personalizado cujo layout é definido no código em vez de XAML. Para simplificar, o aplicativo de exemplo define apenas uma única classe de `CardView` com um layout XAML. No entanto, o aplicativo de exemplo contém uma classe **CardViewCodePage** que mostra o processo de consumo do controle personalizado no código.

### <a name="create-code-behind-properties"></a>Criar propriedades code-behind

O controle personalizado `CardView` define as seguintes propriedades:

* `CardTitle`: um objeto `string` que representa o título mostrado no cartão.
* `CardDescription`: um objeto `string` que representa a descrição mostrada no cartão.
* `IconImageSource`: um objeto `ImageSource` que representa a imagem mostrada no cartão.
* `IconBackgroundColor`: um objeto `Color` que representa a cor do plano de fundo da imagem mostrada no cartão.
* `BorderColor`: um objeto `Color` que representa a cor da borda do cartão, borda da imagem e linha divisória.
* `CardColor`: um objeto `Color` que representa a cor do plano de fundo do cartão.

> [!NOTE]
> A propriedade `BorderColor` afeta vários itens para fins de demonstração. Essa propriedade pode ser dividida em três propriedades, se necessário.

Cada propriedade é apoiada por uma instância de `BindableProperty`. O `BindableProperty` de backup permite que cada propriedade seja estilizada e associada, usando o padrão MVVM.

O exemplo a seguir mostra como criar um `BindableProperty`de backup:

```csharp
public static readonly BindableProperty CardTitleProperty = BindableProperty.Create(
    "CardTitle",        // the name of the bindable property
    typeof(string),     // the bindable property type
    typeof(CardView),   // the parent object type
    string.Empty);      // the default value for the property
```

A propriedade personalizada usa os métodos `GetValue` e `SetValue` para obter e definir os valores do objeto `BindableProperty`:

```csharp
public string CardTitle
{
    get => (string)GetValue(CardView.CardTitleProperty);
    set => SetValue(CardView.CardTitleProperty, value);
}
```

Para obter mais informações sobre objetos `BindableProperty`, consulte [propriedades vinculáveis](~/xamarin-forms/xaml/bindable-properties.md).

### <a name="define-ui"></a>Definir interface do usuário

A interface do usuário do controle personalizado usa um `ContentView` como o elemento raiz para o controle de `CardView`. O exemplo a seguir mostra o `CardView` XAML:

```XAML
<ContentView ...
             x:Name="this"
             x:Class="CardViewDemo.Controls.CardView">
    <Frame BindingContext="{x:Reference this}"
            BackgroundColor="{Binding CardColor}"
            BorderColor="{Binding BorderColor}"
            ...>
        <Grid>
            ...
            <Frame BorderColor="{Binding BorderColor, FallbackValue='Black'}"
                   BackgroundColor="{Binding IconBackgroundColor, FallbackValue='Grey'}"
                   ...>
                <Image Source="{Binding IconImageSource}"
                       .. />
            </Frame>
            <Label Text="{Binding CardTitle, FallbackValue='Card Title'}"
                   ... />
            <BoxView BackgroundColor="{Binding BorderColor, FallbackValue='Black'}"
                     ... />
            <Label Text="{Binding CardDescription, FallbackValue='Card description text.'}"
                   ... />
        </Grid>
    </Frame>
</ContentView>
```

O elemento `ContentView` define a propriedade `x:Name` como **this**, que pode ser usada para acessar o objeto associado à instância de `CardView`. Os elementos no conjunto de layouts vinculam suas propriedades a valores definidos no objeto associado.

Para obter mais informações sobre associação de dados, confira [Associação de Dados do Xamarin.Forms](~/xamarin-forms/app-fundamentals/data-binding/index.md).

> [!NOTE]
> A propriedade `FallbackValue` fornece um valor padrão caso a associação seja `null`. Isso também permite que o [Visualizador de XAML](~/xamarin-forms/xaml/xaml-previewer/index.md) no Visual Studio processe o controle de `CardView`.

## <a name="instantiate-a-custom-control"></a>Criar uma instância de um controle personalizado

Uma referência ao namespace de controle personalizado deve ser adicionada a uma página que instancia o controle personalizado. O exemplo a seguir mostra uma referência de namespace chamada **controles** adicionados a uma instância de `ContentPage` em XAML:

```xaml
<ContentPage ...
             xmlns:controls="clr-namespace:CardViewDemo.Controls" >
```

Depois que a referência tiver sido adicionada, a `CardView` poderá ser instanciada em XAML e suas propriedades definidas:

```xaml
<controls:CardView BorderColor="DarkGray"
                   CardTitle="Slavko Vlasic"
                   CardDescription="Lorem ipsum dolor sit..."
                   IconBackgroundColor="SlateGray"
                   IconImageSource="user.png"/>
```

Um `CardView` também pode ser instanciado no código:

```csharp
CardView card = new CardView
{
    BorderColor = Color.DarkGray,
    CardTitle = "Slavko Vlasic",
    CardDescription = "Lorem ipsum dolor sit...",
    IconBackgroundColor = Color.SlateGray,
    IconImageSource = ImageSource.FromFile("user.png")
};
```

## <a name="customize-appearance-with-a-controltemplate"></a>Personalizar a aparência com um ControlTemplate

Um controle personalizado que deriva da classe `ContentView` pode definir a aparência usando XAML, código ou não pode definir a aparência. Independentemente de como a aparência é definida, um objeto `ControlTemplate` pode substituir a aparência por um layout personalizado.

O layout de `CardView` pode ocupar muito espaço para alguns casos de uso. Um `ControlTemplate` pode substituir o layout de `CardView` para fornecer uma exibição mais compacta, adequada para uma lista condensada:

```xaml
<ContentPage.Resources>
    <ResourceDictionary>
        <ControlTemplate x:Key="CardViewCompressed">
            <Grid>
                <Grid.RowDefinitions>
                    <RowDefinition Height="100" />
                </Grid.RowDefinitions>
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="100" />
                    <ColumnDefinition Width="100*" />
                </Grid.ColumnDefinitions>
                <Image Grid.Row="0"
                       Grid.Column="0"
                       Source="{TemplateBinding IconImageSource}"
                       BackgroundColor="{TemplateBinding IconBackgroundColor}"
                       WidthRequest="100"
                       HeightRequest="100"
                       Aspect="AspectFill"
                       HorizontalOptions="Center"
                       VerticalOptions="Center"/>
                <StackLayout Grid.Row="0"
                             Grid.Column="1">
                    <Label Text="{TemplateBinding CardTitle}"
                           FontAttributes="Bold" />
                    <Label Text="{TemplateBinding CardDescription}" />
                </StackLayout>
            </Grid>
        </ControlTemplate>
    </ResourceDictionary>
</ContentPage.Resources>
```

A associação de dados em um `ControlTemplate` usa a extensão de marcação `TemplateBinding` para especificar associações. A propriedade `ControlTemplate` pode então ser definida como o objeto ControlTemplate definido, usando seu valor `x:Key`. O exemplo a seguir mostra a propriedade `ControlTemplate` definida em uma instância de `CardView`:

```xaml
<controls:CardView ControlTemplate="{StaticResource CardViewCompressed}"/>
```

As capturas de tela a seguir mostram uma instância de `CardView` padrão e `CardView` cujo `ControlTemplate` foi substituído:

[captura de tela de ControlTemplate ![CardView](contentview-images/cardview-controltemplates-cropped.png)](contentview-images/cardview-controltemplates.png#lightbox)

Para obter mais informações sobre modelos de controle, consulte [Xamarin. Forms Control templates](~/xamarin-forms/app-fundamentals/templates/control-template.md).

## <a name="related-links"></a>Links relacionados

* [Aplicativo de exemplo ContentView](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-contentviewdemos/)
* [Associação de dados do Xamarin. Forms](~/xamarin-forms/app-fundamentals/data-binding/index.md)
* [Propriedades vinculáveis](~/xamarin-forms/xaml/bindable-properties.md).
* [Modelos de controle do Xamarin. Forms](~/xamarin-forms/app-fundamentals/templates/control-template.md)
