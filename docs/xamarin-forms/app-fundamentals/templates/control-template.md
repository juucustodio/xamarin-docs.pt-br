---
title: Xamarin.Formsmodelos de controle
description: Xamarin.Formsos modelos de controle definem a estrutura visual de controles personalizados derivados do ContentView e páginas derivadas de ContentPage.
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 44eebed2a49fbdda5504f9a09873f93466d0326c
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/28/2020
ms.locfileid: "84132542"
---
# <a name="xamarinforms-control-templates"></a>Xamarin.Formsmodelos de controle

[![Baixar exemplo ](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/templates-controltemplatedemos)

Xamarin.Formsos modelos de controle permitem que você defina a estrutura visual de [`ContentView`](xref:Xamarin.Forms.ContentView) controles personalizados derivados e [`ContentPage`](xref:Xamarin.Forms.ContentPage) páginas derivadas. Os modelos de controle separam a IU (interface do usuário) para uma página ou controle personalizado da lógica que implementa o controle ou a página. Também é possível inserir conteúdo adicional no controle modelo personalizado ou na página modelo, em um local predefinido.

Por exemplo, é possível criar um modelo de controle que redefine a interface do usuário fornecida por um controle personalizado. O modelo de controle pode então ser consumido pela instância de controle personalizado necessária. Como alternativa, um modelo de controle pode ser criado para definir qualquer interface do usuário comum que será usada por várias páginas em um aplicativo. O modelo de controle pode, então, ser consumido por várias páginas, com cada página ainda exibindo o conteúdo exclusivo.

## <a name="create-a-controltemplate"></a>Criar um ControlTemplate

O exemplo a seguir mostra o código de um controle personalizado de `CardView`:

```csharp
public class CardView : ContentView
{
    public static readonly BindableProperty CardTitleProperty = BindableProperty.Create(nameof(CardTitle), typeof(string), typeof(CardView), string.Empty);
    public static readonly BindableProperty CardDescriptionProperty = BindableProperty.Create(nameof(CardDescription), typeof(string), typeof(CardView), string.Empty);
    // ...

    public string CardTitle
    {
        get => (string)GetValue(CardTitleProperty);
        set => SetValue(CardTitleProperty, value);
    }

    public string CardDescription
    {
        get => (string)GetValue(CardDescriptionProperty);
        set => SetValue(CardDescriptionProperty, value);
    }
    // ...
}
```

A `CardView` classe, que deriva da [`ContentView`](xref:Xamarin.Forms.ContentView) classe, representa um controle personalizado que exibe dados em um layout do tipo cartão. A classe contém propriedades, que são apoiadas por propriedades associáveis, para os dados que ela exibe. No entanto, a classe `CardView` não define nenhuma interface do usuário. Em vez disso, a interface do usuário será definida com um modelo de controle. Para obter mais informações sobre como criar `ContentView` controles personalizados derivados, consulte [ Xamarin.Forms ContentView](~/xamarin-forms/user-interface/layouts/contentview.md).

Um modelo de controle é criado com o [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) tipo. Quando você cria um `ControlTemplate` , você combina [`View`](xref:Xamarin.Forms.View) objetos para criar a interface do usuário para um controle personalizado ou página. Um `ControlTemplate` deve ter apenas um `View` como seu elemento raiz. No entanto, o elemento raiz geralmente contém outros objetos `View`. A combinação dos objetos compõe a estrutura visual do controle.

Embora um [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) possa ser definido em linha, a abordagem típica para declarar um [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) é como um recurso em um dicionário de recursos. Como os modelos de controle são recursos, eles obedecem às mesmas regras de escopo que se aplicam a todos os recursos. Por exemplo, se você declarar um modelo de controle no elemento raiz do arquivo do seu arquivo XAML de definição de aplicativo, o modelo poderá ser utilizado em qualquer lugar no aplicativo. Se você definir o modelo em uma página, somente essa página poderá usar o modelo de controle. Para obter mais informações sobre recursos, consulte [ Xamarin.Forms dicionários de recursos](~/xamarin-forms/xaml/resource-dictionaries.md).

O exemplo de XAML a seguir mostra um [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) para `CardView` objetos:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             ...>
    <ContentPage.Resources>
      <ControlTemplate x:Key="CardViewControlTemplate">
          <Frame BindingContext="{Binding Source={RelativeSource TemplatedParent}}"
                 BackgroundColor="{Binding CardColor}"
                 BorderColor="{Binding BorderColor}"
                 CornerRadius="5"
                 HasShadow="True"
                 Padding="8"
                 HorizontalOptions="Center"
                 VerticalOptions="Center">
              <Grid>
                  <Grid.RowDefinitions>
                      <RowDefinition Height="75" />
                      <RowDefinition Height="4" />
                      <RowDefinition Height="Auto" />
                  </Grid.RowDefinitions>
                  <Grid.ColumnDefinitions>
                      <ColumnDefinition Width="75" />
                      <ColumnDefinition Width="200" />
                  </Grid.ColumnDefinitions>
                  <Frame IsClippedToBounds="True"
                         BorderColor="{Binding BorderColor}"
                         BackgroundColor="{Binding IconBackgroundColor}"
                         CornerRadius="38"
                         HeightRequest="60"
                         WidthRequest="60"
                         HorizontalOptions="Center"
                         VerticalOptions="Center">
                      <Image Source="{Binding IconImageSource}"
                             Margin="-20"
                             WidthRequest="100"
                             HeightRequest="100"
                             Aspect="AspectFill" />
                  </Frame>
                  <Label Grid.Column="1"
                         Text="{Binding CardTitle}"
                         FontAttributes="Bold"
                         FontSize="Large"
                         VerticalTextAlignment="Center"
                         HorizontalTextAlignment="Start" />
                  <BoxView Grid.Row="1"
                           Grid.ColumnSpan="2"
                           BackgroundColor="{Binding BorderColor}"
                           HeightRequest="2"
                           HorizontalOptions="Fill" />
                  <Label Grid.Row="2"
                         Grid.ColumnSpan="2"
                         Text="{Binding CardDescription}"
                         VerticalTextAlignment="Start"
                         VerticalOptions="Fill"
                         HorizontalOptions="Fill" />
              </Grid>
          </Frame>
      </ControlTemplate>
    </ContentPage.Resources>
    ...
</ContentPage>
```

Quando um [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) é declarado como um recurso, ele deve ter uma chave especificada com o `x:Key` atributo para que ele possa ser identificado no dicionário de recursos. Neste exemplo, o elemento raiz do `CardViewControlTemplate` é um objeto [`Frame`](xref:Xamarin.Forms.Frame). O objeto `Frame` usa a extensão de marcação `RelativeSource` para definir o próprio `BindingContext` para a instância de objeto de runtime à qual o modelo será aplicado, que é conhecida como o *pai modelo*. O `Frame` objeto usa uma combinação de [`Grid`](xref:Xamarin.Forms.Grid) objetos,, `Frame` [`Image`](xref:Xamarin.Forms.Image) , [`Label`](xref:Xamarin.Forms.Label) e [`BoxView`](xref:Xamarin.Forms.BoxView) para definir a estrutura visual de um `CardView` objeto. As expressões de associação desses objetos são resolvidas em relação às propriedades `CardView`, devido à herança do `BindingContext` do elemento raiz `Frame`. Para obter mais informações sobre a `RelativeSource` extensão de marcação, consulte [ Xamarin.Forms associações relativas](~/xamarin-forms/app-fundamentals/data-binding/relative-bindings.md).

## <a name="consume-a-controltemplate"></a>Consumir um ControlTemplate

Um [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) pode ser aplicado a um [`ContentView`](xref:Xamarin.Forms.ContentView) controle personalizado derivado definindo sua [`ControlTemplate`](xref:Xamarin.Forms.TemplatedView.ControlTemplate) propriedade como o objeto de modelo de controle. Da mesma forma, um [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) pode ser aplicado a uma [`ContentPage`](xref:Xamarin.Forms.ContentPage) página derivada definindo sua [`ControlTemplate`](xref:Xamarin.Forms.TemplatedPage.ControlTemplate) propriedade como o objeto de modelo de controle. Em tempo de execução, quando um [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) é aplicado, todos os controles definidos no `ControlTemplate` são adicionados à árvore visual do controle personalizado modelo ou página modelo.

O exemplo a seguir mostra o `CardViewControlTemplate` que está sendo atribuído à [`ControlTemplate`](xref:Xamarin.Forms.TemplatedView.ControlTemplate) propriedade de cada `CardView` objeto:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:controls="clr-namespace:ControlTemplateDemos.Controls"
             ...>
    <StackLayout Margin="30">
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

Neste exemplo, os controles no `CardViewControlTemplate` tornam-se parte da árvore visual para cada objeto `CardView`. Como o [`Frame`](xref:Xamarin.Forms.Frame) objeto raiz do modelo de controle define seu `BindingContext` para o pai do modelo, o `Frame` e seus filhos resolvem suas expressões de associação em relação às propriedades de cada `CardView` objeto.

As capturas de tela a seguir mostram o `CardViewControlTemplate` aplicado aos três objetos `CardView`:

[![Capturas de tela de objetos CardView modelos, no iOS e no Android](control-template-images/relativesource-controltemplate.png "Objetos CardView modelados")](control-template-images/relativesource-controltemplate-large.png#lightbox "Objetos CardView modelados")

> [!IMPORTANT]
> O ponto no tempo em que um [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) é aplicado a uma instância de controle pode ser detectado pela substituição do `OnApplyTemplate` método no controle personalizado modelo ou na página modelo. Para obter mais informações, veja [Obter um elemento nomeado de um modelo](#get-a-named-element-from-a-template).

## <a name="pass-parameters-with-templatebinding"></a>Passar parâmetros com TemplateBinding

A `TemplateBinding` extensão de marcação associa uma propriedade de um elemento que está em um [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) a uma propriedade pública que é definida pelo controle personalizado modelo ou página de modelo. Quando você usa um `TemplateBinding`, você habilita propriedades do controle para atuar como parâmetros para o modelo. Portanto, quando uma propriedade é definida em um controle modelo personalizado ou em uma página modelo, esse valor é passado para o elemento que contém o `TemplateBinding`.

> [!IMPORTANT]
> A `TemplateBinding` extensão de marcação é uma alternativa à criação de um [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) que usa a `RelativeSource` extensão de marcação para definir o `BindingContext` do elemento raiz no modelo como pai de modelo. A extensão de marcação `TemplateBinding` elimina a associação de `RelativeSource` e substitui as expressões `Binding` por expressões `TemplateBinding`.

A extensão de marcação `TemplateBinding` define as seguintes propriedades:

- `Path`, do tipo `string`, o caminho para a propriedade.
- `Mode`, do tipo `BindingMode`, a direção em que as alterações se propagam entre a *origem* e o *destino*.
- `Converter`, do tipo `IValueConverter`, o conversor de valor de associação.
- `ConverterParameter`, do tipo `object`, o parâmetro do conversor de valor de associação.
- `StringFormat`, do tipo `string`, o formato da cadeia de caracteres para a associação.

O `ContentProperty` para a extensão de marcação `TemplateBinding` é `Path`. Portanto, a parte "Path=" da extensão de marcação poderá ser omitida se o caminho for o primeiro item na expressão `TemplateBinding`. Para obter mais informações sobre como usar essas propriedades em uma expressão de associação, consulte [ Xamarin.Forms vinculação de dados](~/xamarin-forms/app-fundamentals/data-binding/index.md).

> [!WARNING]
> A `TemplateBinding` extensão de marcação só deve ser usada dentro de um [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) . No entanto, tentar usar uma expressão `TemplateBinding` fora de um `ControlTemplate` não resultará em um erro de build nem na geração de uma exceção.

O exemplo de XAML a seguir mostra um [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) para `CardView` objetos, que usa a `TemplateBinding` extensão de marcação:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             ...>
    <ContentPage.Resources>
        <ControlTemplate x:Key="CardViewControlTemplate">
            <Frame BackgroundColor="{TemplateBinding CardColor}"
                   BorderColor="{TemplateBinding BorderColor}"
                   CornerRadius="5"
                   HasShadow="True"
                   Padding="8"
                   HorizontalOptions="Center"
                   VerticalOptions="Center">
                <Grid>
                    <Grid.RowDefinitions>
                        <RowDefinition Height="75" />
                        <RowDefinition Height="4" />
                        <RowDefinition Height="Auto" />
                    </Grid.RowDefinitions>
                    <Grid.ColumnDefinitions>
                        <ColumnDefinition Width="75" />
                        <ColumnDefinition Width="200" />
                    </Grid.ColumnDefinitions>
                    <Frame IsClippedToBounds="True"
                           BorderColor="{TemplateBinding BorderColor}"
                           BackgroundColor="{TemplateBinding IconBackgroundColor}"
                           CornerRadius="38"
                           HeightRequest="60"
                           WidthRequest="60"
                           HorizontalOptions="Center"
                           VerticalOptions="Center">
                        <Image Source="{TemplateBinding IconImageSource}"
                               Margin="-20"
                               WidthRequest="100"
                               HeightRequest="100"
                               Aspect="AspectFill" />
                    </Frame>
                    <Label Grid.Column="1"
                           Text="{TemplateBinding CardTitle}"
                           FontAttributes="Bold"
                           FontSize="Large"
                           VerticalTextAlignment="Center"
                           HorizontalTextAlignment="Start" />
                    <BoxView Grid.Row="1"
                             Grid.ColumnSpan="2"
                             BackgroundColor="{TemplateBinding BorderColor}"
                             HeightRequest="2"
                             HorizontalOptions="Fill" />
                    <Label Grid.Row="2"
                           Grid.ColumnSpan="2"
                           Text="{TemplateBinding CardDescription}"
                           VerticalTextAlignment="Start"
                           VerticalOptions="Fill"
                           HorizontalOptions="Fill" />
                </Grid>
            </Frame>
        </ControlTemplate>
    </ContentPage.Resources>
    ...
</ContentPage>
```

Neste exemplo, a extensão de marcação `TemplateBinding` resolve expressões de associação em relação às propriedades de cada objeto `CardView`. As capturas de tela a seguir mostram o `CardViewControlTemplate` aplicado aos três objetos `CardView`:

[![Capturas de tela de objetos CardView modelos, no iOS e no Android](control-template-images/templatebinding-controltemplate.png "Objetos CardView modelados")](control-template-images/templatebinding-controltemplate-large.png#lightbox "Objetos CardView modelados")

> [!IMPORTANT]
> O uso da extensão de marcação `TemplateBinding` é equivalente a definir o `BindingContext` do elemento raiz no modelo para o respectivo pai modelo com a extensão de marcação `RelativeSource` e, em seguida, resolver associações de objetos filho com a extensão de marcação `Binding`. Na verdade, a extensão de marcação `TemplateBinding` cria um `Binding` cujo `Source` é `RelativeBindingSource.TemplatedParent`.

## <a name="apply-a-controltemplate-with-a-style"></a>Aplicar um ControlTemplate com um estilo

Os modelos de controle também podem ser aplicados com estilos. Isso é alcançado pela criação de um estilo *implícito* ou *explícito* que consome o [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate).

O seguinte exemplo de XAML mostra um estilo *implícito* que consome o `CardViewControlTemplate`:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:controls="clr-namespace:ControlTemplateDemos.Controls"
             ...>
    <ContentPage.Resources>
        <ControlTemplate x:Key="CardViewControlTemplate">
            ...
        </ControlTemplate>

        <Style TargetType="controls:CardView">
            <Setter Property="ControlTemplate"
                    Value="{StaticResource CardViewControlTemplate}" />
        </Style>
    </ContentPage.Resources>
    <StackLayout Margin="30">
        <controls:CardView BorderColor="DarkGray"
                           CardTitle="John Doe"
                           CardDescription="Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nulla elit dolor, convallis non interdum."
                           IconBackgroundColor="SlateGray"
                           IconImageSource="user.png" />
        <controls:CardView BorderColor="DarkGray"
                           CardTitle="Jane Doe"
                           CardDescription="Phasellus eu convallis mi. In tempus augue eu dignissim fermentum. Morbi ut lacus vitae eros lacinia."
                           IconBackgroundColor="SlateGray"
                           IconImageSource="user.png"/>
        <controls:CardView BorderColor="DarkGray"
                           CardTitle="Xamarin Monkey"
                           CardDescription="Aliquam sagittis, odio lacinia fermentum dictum, mi erat scelerisque erat, quis aliquet arcu."
                           IconBackgroundColor="SlateGray"
                           IconImageSource="user.png" />
    </StackLayout>
</ContentPage>
```

Neste exemplo, o *implícito* [`Style`](xref:Xamarin.Forms.Style) é aplicado automaticamente a cada `CardView` objeto e define a [`ControlTemplate`](xref:Xamarin.Forms.TemplatedView.ControlTemplate) propriedade de cada `CardView` para `CardViewControlTemplate` .

Para obter mais informações sobre estilos, consulte [ Xamarin.Forms estilos](~/xamarin-forms/user-interface/styles/index.md).

## <a name="redefine-a-controls-ui"></a>Redefinir a interface do usuário de um controle

Quando uma [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) é instanciada e atribuída à `ControlTemplate` propriedade de um [`ContentView`](xref:Xamarin.Forms.ContentView) controle personalizado derivado ou a uma [`ContentPage`](xref:Xamarin.Forms.ContentPage) página derivada, a estrutura visual definida para o controle ou página personalizado é substituída pela estrutura visual definida no `ControlTemplate` .

Por exemplo, o controle personalizado `CardViewUI` define a própria interface do usuário usando o seguinte XAML:

```xaml
<ContentView xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="ControlTemplateDemos.Controls.CardViewUI"
             x:Name="this">
    <Frame BindingContext="{x:Reference this}"
           BackgroundColor="{Binding CardColor}"
           BorderColor="{Binding BorderColor}"
           CornerRadius="5"
           HasShadow="True"
           Padding="8"
           HorizontalOptions="Center"
           VerticalOptions="Center">
        <Grid>
            <Grid.RowDefinitions>
                <RowDefinition Height="75" />
                <RowDefinition Height="4" />
                <RowDefinition Height="Auto" />
            </Grid.RowDefinitions>
            <Grid.ColumnDefinitions>
                <ColumnDefinition Width="75" />
                <ColumnDefinition Width="200" />
            </Grid.ColumnDefinitions>
            <Frame IsClippedToBounds="True"
                   BorderColor="{Binding BorderColor, FallbackValue='Black'}"
                   BackgroundColor="{Binding IconBackgroundColor, FallbackValue='Gray'}"
                   CornerRadius="38"
                   HeightRequest="60"
                   WidthRequest="60"
                   HorizontalOptions="Center"
                   VerticalOptions="Center">
                <Image Source="{Binding IconImageSource}"
                       Margin="-20"
                       WidthRequest="100"
                       HeightRequest="100"
                       Aspect="AspectFill" />
            </Frame>
            <Label Grid.Column="1"
                   Text="{Binding CardTitle, FallbackValue='Card title'}"
                   FontAttributes="Bold"
                   FontSize="Large"
                   VerticalTextAlignment="Center"
                   HorizontalTextAlignment="Start" />
            <BoxView Grid.Row="1"
                     Grid.ColumnSpan="2"
                     BackgroundColor="{Binding BorderColor, FallbackValue='Black'}"
                     HeightRequest="2"
                     HorizontalOptions="Fill" />
            <Label Grid.Row="2"
                   Grid.ColumnSpan="2"
                   Text="{Binding CardDescription, FallbackValue='Card description'}"
                   VerticalTextAlignment="Start"
                   VerticalOptions="Fill"
                   HorizontalOptions="Fill" />
        </Grid>
    </Frame>
</ContentView>
```

No entanto, os controles que compõem essa interface do usuário podem ser substituídos definindo-se uma nova estrutura visual em um [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) e atribuindo-o à [`ControlTemplate`](xref:Xamarin.Forms.TemplatedView.ControlTemplate) propriedade de um `CardViewUI` objeto:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             ...>
    <ContentPage.Resources>
        <ControlTemplate x:Key="CardViewCompressed">
            <Grid>
                <Grid.RowDefinitions>
                    <RowDefinition Height="100" />
                </Grid.RowDefinitions>
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="100" />
                    <ColumnDefinition Width="*" />
                </Grid.ColumnDefinitions>
                <Image Source="{TemplateBinding IconImageSource}"
                        BackgroundColor="{TemplateBinding IconBackgroundColor}"
                        WidthRequest="100"
                        HeightRequest="100"
                        Aspect="AspectFill"
                        HorizontalOptions="Center"
                        VerticalOptions="Center" />
                <StackLayout Grid.Column="1">
                    <Label Text="{TemplateBinding CardTitle}"
                           FontAttributes="Bold" />
                    <Label Text="{TemplateBinding CardDescription}" />
                </StackLayout>
            </Grid>
        </ControlTemplate>
    </ContentPage.Resources>
    <StackLayout Margin="30">
        <controls:CardViewUI BorderColor="DarkGray"
                             CardTitle="John Doe"
                             CardDescription="Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nulla elit dolor, convallis non interdum."
                             IconBackgroundColor="SlateGray"
                             IconImageSource="user.png"
                             ControlTemplate="{StaticResource CardViewCompressed}" />
        <controls:CardViewUI BorderColor="DarkGray"
                             CardTitle="Jane Doe"
                             CardDescription="Phasellus eu convallis mi. In tempus augue eu dignissim fermentum. Morbi ut lacus vitae eros lacinia."
                             IconBackgroundColor="SlateGray"
                             IconImageSource="user.png"
                             ControlTemplate="{StaticResource CardViewCompressed}" />
        <controls:CardViewUI BorderColor="DarkGray"
                             CardTitle="Xamarin Monkey"
                             CardDescription="Aliquam sagittis, odio lacinia fermentum dictum, mi erat scelerisque erat, quis aliquet arcu."
                             IconBackgroundColor="SlateGray"
                             IconImageSource="user.png"
                             ControlTemplate="{StaticResource CardViewCompressed}" />
    </StackLayout>
</ContentPage>
```

Neste exemplo, a estrutura visual do `CardViewUI` objeto é redefinida em um [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) que fornece uma estrutura visual mais compacta adequada para uma lista condensada:

[![Capturas de tela de objetos CardViewUI modelos, no iOS e no Android](control-template-images/redefine-controltemplate.png "Objetos CardViewUI modelados")](control-template-images/redefine-controltemplate-large.png#lightbox "Objetos CardViewUI modelados")

## <a name="substitute-content-into-a-contentpresenter"></a>Substituir o conteúdo em um ContentPresenter

Um [`ContentPresenter`](xref:Xamarin.Forms.ContentPresenter) pode ser colocado em um modelo de controle para marcar onde o conteúdo a ser exibido pelo controle personalizado modelo ou página modelo será exibido. A página ou controle personalizado ou a página que consome o modelo de controle definirá o conteúdo a ser exibido pelo `ContentPresenter`. O diagrama a seguir ilustra um `ControlTemplate` para uma página que contém um número de controles, incluindo um `ContentPresenter` marcado por um retângulo azul:

![Modelo de controle para uma ContentPage](control-template-images/control-template.png "Modelo de controle para uma ContentPage")

O XAML a seguir mostra um modelo de controle chamado `TealTemplate` que contém um [`ContentPresenter`](xref:Xamarin.Forms.ContentPresenter) em sua estrutura visual:

```xaml
<ControlTemplate x:Key="TealTemplate">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="0.1*" />
            <RowDefinition Height="0.8*" />
            <RowDefinition Height="0.1*" />
        </Grid.RowDefinitions>
        <BoxView Color="Teal" />
        <Label Margin="20,0,0,0"
               Text="{TemplateBinding HeaderText}"
               TextColor="White"
               FontSize="Title"
               VerticalOptions="Center" />
        <ContentPresenter Grid.Row="1" />
        <BoxView Grid.Row="2"
                 Color="Teal" />
        <Label x:Name="changeThemeLabel"
               Grid.Row="2"
               Margin="20,0,0,0"
               Text="Change Theme"
               TextColor="White"
               HorizontalOptions="Start"
               VerticalOptions="Center">
            <Label.GestureRecognizers>
                <TapGestureRecognizer Tapped="OnChangeThemeLabelTapped" />
            </Label.GestureRecognizers>
        </Label>
        <controls:HyperlinkLabel Grid.Row="2"
                                 Margin="0,0,20,0"
                                 Text="Help"
                                 TextColor="White"
                                 Url="https://docs.microsoft.com/xamarin/xamarin-forms/"
                                 HorizontalOptions="End"
                                 VerticalOptions="Center" />
    </Grid>
</ControlTemplate>
```

O exemplo a seguir mostra `TealTemplate` atribuído à [`ControlTemplate`](xref:Xamarin.Forms.TemplatedPage.ControlTemplate) propriedade de uma [`ContentPage`](xref:Xamarin.Forms.ContentPage) página derivada:

```xaml
<controls:HeaderFooterPage xmlns="http://xamarin.com/schemas/2014/forms"
                           xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                           xmlns:controls="clr-namespace:ControlTemplateDemos.Controls"                           
                           ControlTemplate="{StaticResource TealTemplate}"
                           HeaderText="MyApp"
                           ...>
    <StackLayout Margin="10">
        <Entry Placeholder="Enter username" />
        <Entry Placeholder="Enter password"
               IsPassword="True" />
        <Button Text="Login" />
    </StackLayout>
</controls:HeaderFooterPage>
```

No tempo de execução, quando `TealTemplate` é aplicado à página, o conteúdo da página é substituído pelo [`ContentPresenter`](xref:Xamarin.Forms.ContentPresenter) definido no modelo de controle:

[![Capturas de tela do objeto de página modelo, no iOS e no Android](control-template-images/tealtemplate-contentpage.png "ContentPage modelada")](control-template-images/tealtemplate-contentpage-large.png#lightbox "ContentPage modelada")

## <a name="get-a-named-element-from-a-template"></a>Obter um elemento nomeado de um modelo

Elementos nomeados dentro de um modelo de controle podem ser recuperados do controle modelo personalizado ou da página modelo. Isso pode ser feito com o `GetTemplateChild` método, que retorna o elemento nomeado na [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) árvore visual instanciada, se encontrado. Caso contrário, ele retornará `null`.

Depois que um modelo de controle foi instanciado, o método `OnApplyTemplate` do modelo é chamado. O método `GetTemplateChild`, portanto, deve ser chamado de uma substituição de `OnApplyTemplate` no controle modelo ou na página modelo.

> [!IMPORTANT]
> O método `GetTemplateChild` só deve ser chamado após o método `OnApplyTemplate` ser chamado.

O XAML a seguir mostra um modelo de controle chamado `TealTemplate` que pode ser aplicado a [`ContentPage`](xref:Xamarin.Forms.ContentPage) páginas derivadas:

```xaml
<ControlTemplate x:Key="TealTemplate">
    <Grid>
        ...
        <Label x:Name="changeThemeLabel"
               Grid.Row="2"
               Margin="20,0,0,0"
               Text="Change Theme"
               TextColor="White"
               HorizontalOptions="Start"
               VerticalOptions="Center">
            <Label.GestureRecognizers>
                <TapGestureRecognizer Tapped="OnChangeThemeLabelTapped" />
            </Label.GestureRecognizers>
        </Label>
        ...
    </Grid>
</ControlTemplate>
```

Neste exemplo, o [`Label`](xref:Xamarin.Forms.Label) elemento é nomeado e pode ser recuperado no código da página modelo. Isso é feito chamando o método `GetTemplateChild` da substituição `OnApplyTemplate` para a página modelo:

```csharp
public partial class AccessTemplateElementPage : HeaderFooterPage
{
    Label themeLabel;

    public AccessTemplateElementPage()
    {
        InitializeComponent();
    }

    protected override void OnApplyTemplate()
    {
        base.OnApplyTemplate();
        themeLabel = (Label)GetTemplateChild("changeThemeLabel");
        themeLabel.Text = OriginalTemplate ? "Aqua Theme" : "Teal Theme";
    }
}
```

Neste exemplo, o [`Label`](xref:Xamarin.Forms.Label) objeto chamado `changeThemeLabel` será recuperado depois que o `ControlTemplate` tiver sido instanciado. Então `changeThemeLabel` pode ser acessado e manipulado pela classe `AccessTemplateElementPage`. As capturas de tela a seguir mostram que o texto exibido pelo `Label` foi alterado:

[![Capturas de tela do objeto de página modelo, no iOS e no Android](control-template-images/get-named-element.png "ContentPage modelada")](control-template-images/get-named-element-large.png#lightbox "ContentPage modelada")

## <a name="bind-to-a-viewmodel"></a>Associar a um ViewModel

Um [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) pode associar dados a um ViewModel, mesmo quando o `ControlTemplate` é associado ao pai do modelo (a instância do objeto de tempo de execução à qual o modelo é aplicado).

O seguinte exemplo de XAML mostra uma página que consome um ViewModel chamado `PeopleViewModel`:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:ControlTemplateDemos"
             xmlns:controls="clr-namespace:ControlTemplateDemos.Controls"
             ...>
    <ContentPage.BindingContext>
        <local:PeopleViewModel />
    </ContentPage.BindingContext>

    <ContentPage.Resources>
        <DataTemplate x:Key="PersonTemplate">
            <controls:CardView BorderColor="DarkGray"
                               CardTitle="{Binding Name}"
                               CardDescription="{Binding Description}"
                               ControlTemplate="{StaticResource CardViewControlTemplate}" />
        </DataTemplate>
    </ContentPage.Resources>

    <StackLayout Margin="10"
                 BindableLayout.ItemsSource="{Binding People}"
                 BindableLayout.ItemTemplate="{StaticResource PersonTemplate}" />
</ContentPage>
```

Neste exemplo, o `BindingContext` da página é definido para uma instância de `PeopleViewModel`. Esse ViewModel expõe uma coleção de `People` e um `ICommand` chamado `DeletePersonCommand`. O [`StackLayout`](xref:Xamarin.Forms.StackLayout) na página usa um layout vinculável para associar dados à `People` coleção e o `ItemTemplate` do layout vinculável é definido como o `PersonTemplate` recurso. Isso [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) especifica que cada item na `People` coleção será exibido usando um `CardView` objeto. A estrutura visual do `CardView` objeto é definida usando um [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) nome `CardViewControlTemplate` :

```xaml
<ControlTemplate x:Key="CardViewControlTemplate">
    <Frame BindingContext="{Binding Source={RelativeSource TemplatedParent}}"
           BackgroundColor="{Binding CardColor}"
           BorderColor="{Binding BorderColor}"
           CornerRadius="5"
           HasShadow="True"
           Padding="8"
           HorizontalOptions="Center"
           VerticalOptions="Center">
        <Grid>
            <Grid.RowDefinitions>
                <RowDefinition Height="75" />
                <RowDefinition Height="4" />
                <RowDefinition Height="Auto" />
            </Grid.RowDefinitions>
            <Label Text="{Binding CardTitle}"
                   FontAttributes="Bold"
                   FontSize="Large"
                   VerticalTextAlignment="Center"
                   HorizontalTextAlignment="Start" />
            <BoxView Grid.Row="1"
                     BackgroundColor="{Binding BorderColor}"
                     HeightRequest="2"
                     HorizontalOptions="Fill" />
            <Label Grid.Row="2"
                   Text="{Binding CardDescription}"
                   VerticalTextAlignment="Start"
                   VerticalOptions="Fill"
                   HorizontalOptions="Fill" />
            <Button Text="Delete"
                    Command="{Binding Source={RelativeSource AncestorType={x:Type local:PeopleViewModel}}, Path=DeletePersonCommand}"
                    CommandParameter="{Binding CardTitle}"
                    HorizontalOptions="End" />
        </Grid>
    </Frame>
</ControlTemplate>
```

Neste exemplo, o elemento raiz do [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) é um [`Frame`](xref:Xamarin.Forms.Frame) objeto. O objeto `Frame` usa a extensão de marcação `RelativeSource` para definir o próprio `BindingContext` como o pai modelo. As expressões de associação do objeto `Frame` e os respectivos filhos são resolvidas em relação às propriedades `CardView`, devido à herança do `BindingContext` do elemento raiz `Frame`. As capturas de tela a seguir mostram a página que exibe a coleção `People`, que consiste em três itens:

[![Capturas de tela de objetos CardView modelos, no iOS e no Android](control-template-images/viewmodel-controltemplate.png "Objetos CardView modelados")](control-template-images/viewmodel-controltemplate-large.png#lightbox "Objetos CardView modelados")

Enquanto os objetos na [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) associação a propriedades em seu pai modelado, o [`Button`](xref:Xamarin.Forms.Button) dentro do modelo de controle é associado ao seu pai de modelo e ao `DeletePersonCommand` no ViewModel. Isso ocorre porque a `Button.Command` propriedade redefine sua origem de associação como o contexto de associação do ancestral cujo tipo de contexto de associação é `PeopleViewModel` , que é o [`StackLayout`](xref:Xamarin.Forms.StackLayout) . A parte `Path` das expressões de associação pode então resolver a propriedade `DeletePersonCommand`. No entanto, a `Button.CommandParameter` propriedade não altera sua fonte de associação, em vez disso, a herda de seu pai no [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) . Portanto, a propriedade `CommandParameter` é associada à propriedade `CardTitle` do `CardView`.

O efeito geral das [`Button`](xref:Xamarin.Forms.Button) associações é que, quando o `Button` é tocado, o `DeletePersonCommand` na `PeopleViewModel` classe é executado, com o valor da `CardName` propriedade que está sendo passada para o `DeletePersonCommand` . Isso resulta na remoção do `CardView` especificado do layout associável:

[![Capturas de tela de objetos CardView modelos, no iOS e no Android](control-template-images/viewmodel-itemdeleted.png "Objetos CardView modelados")](control-template-images/viewmodel-itemdeleted-large.png#lightbox "Objetos CardView modelados")

Para obter mais informações sobre associações relativas, consulte [ Xamarin.Forms associações relativas](~/xamarin-forms/app-fundamentals/data-binding/relative-bindings.md).

## <a name="related-links"></a>Links relacionados

- [ControlTemplateDemos (amostra)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/templates-controltemplatedemos)
- [Xamarin.FormsContentView](~/xamarin-forms/user-interface/layouts/contentview.md)
- [Xamarin.FormsAssociações relativas](~/xamarin-forms/app-fundamentals/data-binding/relative-bindings.md)
- [Xamarin.FormsDicionários de recursos](~/xamarin-forms/xaml/resource-dictionaries.md)
- [Xamarin.FormsAssociação de dados](~/xamarin-forms/app-fundamentals/data-binding/index.md)
- [Xamarin.FormsEstilos](~/xamarin-forms/user-interface/styles/index.md)
