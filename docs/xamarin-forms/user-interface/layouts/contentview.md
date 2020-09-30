---
title: Xamarin.Forms ContentView
description: Este artigo explica como usar a classe ContentView para criar um controle personalizado, como o exemplo CardView.
ms.prod: xamarin
ms.assetid: 638402E7-CA44-456B-863B-791F6B6B561D
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 08/14/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: eb94b2943cfe4659bb77764f810d45dc8b189005
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91555366"
---
# <a name="no-locxamarinforms-contentview"></a>Xamarin.Forms ContentView

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-contentviewdemos/)

A Xamarin.Forms [`ContentView`](xref:Xamarin.Forms.ContentView) classe é um tipo `Layout` que contém um único elemento filho e é normalmente usado para criar controles personalizados e reutilizáveis. A `ContentView` classe é herdada de [`TemplatedView`](xref:Xamarin.Forms.TemplatedView) . Este artigo e um exemplo associado explicam como criar um controle personalizado `CardView` com base na `ContentView` classe.

A captura de tela a seguir mostra um `CardView` controle que deriva da `ContentView` classe:

[![Captura de tela do aplicativo de exemplo CardView](contentview-images/cardview-list-cropped.png)](contentview-images/cardview-list.png#lightbox)

A `ContentView` classe define uma única propriedade:

* [`Content`](xref:Xamarin.Forms.ContentView.Content) é um `View` objeto. Essa propriedade é apoiada por um [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objeto para que possa ser o destino de associações de dados.

O `ContentView` também herda uma propriedade da `TemplatedView` classe:

* [`ControlTemplate`](xref:Xamarin.Forms.TemplatedView.ControlTemplate) é um `ControlTemplate` que pode definir ou substituir a aparência do controle.

Para obter mais informações sobre a `ControlTemplate` propriedade, consulte [Personalizar a aparência com um ControlTemplate](#customize-appearance-with-a-controltemplate).

## <a name="create-a-custom-control"></a>Criar um controle personalizado

A `ContentView` classe oferece pouca funcionalidade por si só, mas pode ser usada para criar um controle personalizado. O projeto de exemplo define um `CardView` controle-um elemento de interface do usuário que exibe uma imagem, título e descrição em um layout do tipo cartão.

O processo para criar um controle personalizado é:

1. Crie uma nova classe usando o `ContentView` modelo no Visual Studio 2019.
1. Defina quaisquer propriedades ou eventos exclusivos no arquivo code-behind para o novo controle personalizado.
1. Crie a interface do usuário para o controle personalizado.

> [!NOTE]
> É possível criar um controle personalizado cujo layout é definido no código em vez de XAML. Para simplificar, o aplicativo de exemplo define apenas uma única `CardView` classe com um layout XAML. No entanto, o aplicativo de exemplo contém uma classe **CardViewCodePage** que mostra o processo de consumo do controle personalizado no código.

### <a name="create-code-behind-properties"></a>Criar propriedades code-behind

O `CardView` controle personalizado define as seguintes propriedades:

* `CardTitle`: um `string` objeto que representa o título mostrado no cartão.
* `CardDescription`: um `string` objeto que representa a descrição mostrada no cartão.
* `IconImageSource`: um `ImageSource` objeto que representa a imagem mostrada no cartão.
* `IconBackgroundColor`: um `Color` objeto que representa a cor do plano de fundo da imagem mostrada no cartão.
* `BorderColor`: um `Color` objeto que representa a cor da borda do cartão, borda da imagem e linha divisória.
* `CardColor`: um `Color` objeto que representa a cor do plano de fundo do cartão.

> [!NOTE]
> A `BorderColor` propriedade afeta vários itens para fins de demonstração. Essa propriedade pode ser dividida em três propriedades, se necessário.

Cada propriedade é apoiada por uma `BindableProperty` instância. O backup `BindableProperty` permite que cada propriedade seja estilizada e vinculada, usando o padrão MVVM.

O exemplo a seguir mostra como criar um backup `BindableProperty` :

```csharp
public static readonly BindableProperty CardTitleProperty = BindableProperty.Create(
    "CardTitle",        // the name of the bindable property
    typeof(string),     // the bindable property type
    typeof(CardView),   // the parent object type
    string.Empty);      // the default value for the property
```

A propriedade personalizada usa os `GetValue` `SetValue` métodos e para obter e definir os `BindableProperty` valores de objeto:

```csharp
public string CardTitle
{
    get => (string)GetValue(CardView.CardTitleProperty);
    set => SetValue(CardView.CardTitleProperty, value);
}
```

Para obter mais informações sobre `BindableProperty` objetos, consulte [propriedades vinculáveis](~/xamarin-forms/xaml/bindable-properties.md).

### <a name="define-ui"></a>Definir interface do usuário

A interface do usuário do controle personalizado usa um `ContentView` como o elemento raiz para o `CardView` controle. O exemplo a seguir mostra o `CardView` XAML:

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

O `ContentView` elemento define a `x:Name` propriedade como **this**, que pode ser usada para acessar o objeto associado à `CardView` instância. Os elementos no conjunto de layouts vinculam suas propriedades a valores definidos no objeto associado.

Para obter mais informações sobre a vinculação de dados, consulte [ Xamarin.Forms vinculação de dados](~/xamarin-forms/app-fundamentals/data-binding/index.md).

> [!NOTE]
> A `FallbackValue` propriedade fornece um valor padrão caso a associação seja `null` . Isso também permite que o [Visualizador de XAML](~/xamarin-forms/xaml/xaml-previewer/index.md) no Visual Studio processe o `CardView` controle.

## <a name="instantiate-a-custom-control"></a>Criar uma instância de um controle personalizado

Uma referência ao namespace de controle personalizado deve ser adicionada a uma página que instancia o controle personalizado. O exemplo a seguir mostra uma referência de namespace chamada **controles** adicionados a uma `ContentPage` instância em XAML:

```xaml
<ContentPage ...
             xmlns:controls="clr-namespace:CardViewDemo.Controls" >
```

Depois que a referência tiver sido adicionada `CardView` , o pode ser instanciado em XAML e suas propriedades definidas:

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

Um controle personalizado que deriva da `ContentView` classe pode definir a aparência usando XAML, código ou não pode definir a aparência. Independentemente de como a aparência é definida, um `ControlTemplate` objeto pode substituir a aparência por um layout personalizado.

O `CardView` layout pode ocupar muito espaço para alguns casos de uso. Um `ControlTemplate` pode substituir o `CardView` layout para fornecer uma exibição mais compacta, adequada para uma lista condensada:

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

A vinculação de dados em um `ControlTemplate` usa a `TemplateBinding` extensão de marcação para especificar associações. A `ControlTemplate` propriedade pode então ser definida como o objeto ControlTemplate definido, usando seu `x:Key` valor. O exemplo a seguir mostra a `ControlTemplate` propriedade definida em uma `CardView` instância:

```xaml
<controls:CardView ControlTemplate="{StaticResource CardViewCompressed}"/>
```

As capturas de tela a seguir mostram uma `CardView` instância padrão e `CardView` cujo `ControlTemplate` foi substituído:

[![Captura de tela ControlTemplate CardView](contentview-images/cardview-controltemplates-cropped.png)](contentview-images/cardview-controltemplates.png#lightbox)

Para obter mais informações sobre modelos de controle, consulte [ Xamarin.Forms modelos de controle](~/xamarin-forms/app-fundamentals/templates/control-template.md).

## <a name="related-links"></a>Links relacionados

* [Aplicativo de exemplo ContentView](/samples/xamarin/xamarin-forms-samples/userinterface-contentviewdemos/)
* [Xamarin.Forms Associação de dados](~/xamarin-forms/app-fundamentals/data-binding/index.md)
* [Propriedades vinculáveis](~/xamarin-forms/xaml/bindable-properties.md).
* [Xamarin.Forms Modelos de controle](~/xamarin-forms/app-fundamentals/templates/control-template.md)