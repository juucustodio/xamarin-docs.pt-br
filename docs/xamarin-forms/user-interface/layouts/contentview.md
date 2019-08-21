---
title: ContentView Xamarin. Forms
description: Este artigo explica como usar a classe ContentView para criar um controle personalizado, como o exemplo CardView.
ms.prod: xamarin
ms.assetid: 638402E7-CA44-456B-863B-791F6B6B561D
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 08/14/2019
ms.openlocfilehash: e340b45148c7528eff1aa511ee9902a4ac2658c0
ms.sourcegitcommit: 9178e2e689f027212ea3e623b556b312985d79fe
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/21/2019
ms.locfileid: "69658153"
---
# <a name="xamarinforms-contentview"></a>ContentView Xamarin. Forms

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/en-us/samples/xamarin/xamarin-forms-samples/userinterface-cardview/)

A classe Xamarin. [`ContentView`](xref:Xamarin.Forms.ContentView) Forms é um `Layout` tipo que contém um único elemento filho e é normalmente usado para criar controles personalizados e reutilizáveis. A `ContentView` classe é herdada de [`TemplatedView`](xref:Xamarin.Forms.TemplatedView). Este artigo e um exemplo associado explicam como criar um controle personalizado `CardView` com base `ContentView` na classe.

A captura de tela a `CardView` seguir mostra um controle que deriva `ContentView` da classe:

[![Captura de tela do aplicativo de exemplo CardView](contentview-images/cardview-list-cropped.png)](contentview-images/cardview-list.png#lightbox)

A `ContentView` classe define uma única propriedade:

* [`Content`](xref:Xamarin.Forms.ContentView.Content)é um `View` objeto. Essa propriedade é apoiada por um [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objeto para que possa ser o destino de associações de dados.

O `ContentView` também herda uma propriedade `TemplatedView` da classe:

* [`ControlTemplate`](xref:Xamarin.Forms.TemplatedView.ControlTemplate)é um `ControlTemplate` que pode definir ou substituir a aparência do controle.

Para obter mais informações sobre `ControlTemplate` a propriedade, consulte [Personalizar a aparência com um ControlTemplate](#customize-appearance-with-a-controltemplate).

## <a name="create-a-custom-control"></a>Criar um controle personalizado

A `ContentView` classe oferece pouca funcionalidade por si só, mas pode ser usada para criar um controle personalizado. O projeto de exemplo define `CardView` um controle-um elemento de interface do usuário que exibe uma imagem, título e descrição em um layout do tipo cartão.

O processo para criar um controle personalizado é:

1. Crie uma nova classe usando o `ContentView` modelo no Visual Studio 2019.
1. Defina quaisquer propriedades ou eventos exclusivos no arquivo code-behind para o novo controle personalizado.
1. Crie a interface do usuário para o controle personalizado.

> [!NOTE]
> É possível criar um controle personalizado cujo layout é definido no código em vez de XAML. Para simplificar, o aplicativo de exemplo define apenas `CardView` uma única classe com um layout XAML. No entanto, o aplicativo de exemplo contém uma classe **CardViewCodePage** que mostra o processo de consumo do controle personalizado no código.

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

Cada propriedade é apoiada por uma `BindableProperty` instância. O backup `BindableProperty` permite que cada propriedade seja estilizada e vinculada, usando o padrão MVVM. Para obter mais informações, consulte [associar dados com MVVM](#bind-data-with-mvvm).

O exemplo a seguir mostra como criar um backup `BindableProperty`:

```csharp
public static readonly BindableProperty CardTitleProperty = BindableProperty.Create(
    "CardTitle",        // the name of the bindable property
    typeof(string),     // the bindable property type
    typeof(CardView),   // the parent object type
    string.Empty);      // the default value for the property
```

A propriedade personalizada usa os `GetValue` métodos `SetValue` e para obter e definir os `BindableProperty` valores de objeto:

```csharp
public string CardTitle
{
    get => (string)GetValue(CardView.CardTitleProperty);
    set => SetValue(CardView.CardTitleProperty, value);
}
```

Para obter mais informações `BindableProperty` sobre objetos, consulte [propriedades vinculáveis](~/xamarin-forms/xaml/bindable-properties.md).

### <a name="define-ui"></a>Definir interface do usuário

A interface do usuário do controle `ContentView` personalizado usa um como o elemento `CardView` raiz para o controle. O exemplo a seguir mostra `CardView` o XAML:

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
            <Frame BorderColor="{Binding BorderColor}"
                   BackgroundColor="{Binding IconBackgroundColor}"
                   ...>
                <Image Source="{Binding IconImageSource}"
                       .. />
            </Frame>
            <Label Text="{Binding CardTitle}"
                   ... />
            <BoxView BackgroundColor="{Binding BorderColor}"
                     ... />
            <Label Text="{Binding CardDescription}"
                   ... />
        </Grid>
    </Frame>
</ContentView>
```

O `ContentView` elemento define a `x:Name` Propriedade como **this**, que pode ser usada `CardView` para acessar o objeto associado à instância. Os elementos no conjunto de layouts vinculam suas propriedades a valores definidos no objeto associado.

Para obter mais informações sobre associação de dados, confira [Associação de Dados do Xamarin.Forms](~/xamarin-forms/app-fundamentals/data-binding/index.md).

## <a name="instantiate-a-custom-control"></a>Criar uma instância de um controle personalizado

Uma referência ao namespace de controle personalizado deve ser adicionada a uma página que instancia o controle personalizado. O exemplo a seguir mostra uma referência de namespace chamada **controles** adicionados `ContentPage` a uma instância em XAML:

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

### <a name="bind-data-with-mvvm"></a>Associar dados com o MVVM

Os `BindableProperty` objetos`CardView` na classe permitem associações de estilo Model-View-ViewModel (MVVM). O aplicativo de exemplo contém `PersonCollectionViewModel` uma classe que define uma única propriedade de coleção:

```csharp
public class PersonCollectionViewModel : INotifyPropertyChanged
{
    ...
    public List<PersonViewModel> Items
    {
        get
        {
            return items;
        }
        set
        {
            items = value;
            NotifyPropertyChanged();
        }
    }
    ...
}
```

A `PersonViewModel` classe representa um perfil pessoal:

```csharp
public class PersonViewModel : INotifyPropertyChanged
{
    ...

    string photo;
    public string Photo
    {
        get
        {
            return photo;
        }
        set
        {
            photo = value;
            NotifyPropertyChanged();
        }
    }

    string name;
    public string Name
    {
        get
        {
            return name;
        }
        set
        {
            name = value;
            NotifyPropertyChanged();
        }
    }

    string bio;
    public string Bio
    {
        get
        {
            return bio;
        }
        set
        {
            bio = value;
            NotifyPropertyChanged();
        }
    }
    ...
}
```

O `CardView` pode ser usado para renderizar a coleção `PersonViewModel` de objetos como uma lista de cartões. O exemplo a seguir mostra como associar uma `PersonViewCollection` instância a uma `StackLayout` instância em XAML:

```xaml
<StackLayout HorizontalOptions="Fill"
             VerticalOptions="Fill"
             BindableLayout.ItemsSource="{Binding Items}">
    <BindableLayout.ItemTemplate>
        <DataTemplate>
            <controls:CardView Margin="4"
                               BorderColor="DarkGray"
                               IconBackgroundColor="SlateGray"
                               BindingContext="{Binding .}"
                               CardTitle="{Binding Name}"
                               CardDescription="{Binding Bio}"
                               IconImageSource="{Binding Photo}"/>
        </DataTemplate>
    </BindableLayout.ItemTemplate>
</StackLayout>
```

A `Items` Propriedade em uma `PersonViewCollection` `StackLayout` instância está associada ao usando um layout vinculável. O `DataTemplate` define a aparência de cada `CardView` objeto e vincula os dados a propriedades em um `PersonViewModel`. Quando o `BindingContext` for definido, um `CardView` objeto será criado `Items` para cada `PersonView` objeto na coleção. O `BindingContext` é definido como mostrado no exemplo a seguir:

```csharp
public partial class CardViewMvvmPage : ContentPage
{
    public CardViewMvvmPage()
    {
        InitializeComponent();
        BindingContext = DataService.GetPersonCollection();
    }
}
```

Para obter mais informações sobre a vinculação de dados, consulte [Associação de dados do Xamarin. Forms](~/xamarin-forms/app-fundamentals/data-binding/index.md). Para obter mais informações `BindableProperty` sobre objetos, consulte [propriedades vinculáveis](~/xamarin-forms/xaml/bindable-properties.md).

## <a name="customize-appearance-with-a-controltemplate"></a>Personalizar a aparência com um ControlTemplate

Um controle personalizado que deriva da classe pode `ContentView` definir a aparência usando XAML, código ou não pode definir a aparência. Independentemente de como a aparência é definida, `ControlTemplate` um objeto pode substituir a aparência por um layout personalizado.

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

A vinculação de dados `ControlTemplate` em um `TemplateBinding` usa a extensão de marcação para especificar associações. A `ControlTemplate` propriedade pode então ser definida como o objeto ControlTemplate definido, usando seu `x:Key` valor. O exemplo a seguir mostra `ControlTemplate` a propriedade definida em `CardView` uma instância:

```xaml
<controls:CardView ControlTemplate="{StaticResource CardViewCompressed}"/>
```

As capturas de tela a seguir `CardView` mostram uma `CardView` instância `ControlTemplate` padrão e cujo foi substituído:

[![Captura de tela ControlTemplate CardView](contentview-images/cardview-controltemplates-cropped.png)](contentview-images/cardview-controltemplates.png#lightbox)

Para obter mais informações sobre modelos de controle, consulte [Xamarin. Forms Control templates](~/xamarin-forms/app-fundamentals/templates/control-templates/index.md).

## <a name="related-links"></a>Links relacionados

* [Aplicativo de exemplo CardView](https://docs.microsoft.com/en-us/samples/xamarin/xamarin-forms-samples/userinterface-cardview/)
* [Associação de dados do Xamarin. Forms](~/xamarin-forms/app-fundamentals/data-binding/index.md)
* [Propriedades vinculáveis](~/xamarin-forms/xaml/bindable-properties.md).
* [Modelos de controle do Xamarin. Forms](~/xamarin-forms/app-fundamentals/templates/control-templates/index.md)
