---
title: Modelos de controle do Xamarin.Forms
description: Os modelos de controle do Xamarin.Forms definem a estrutura visual de controles personalizados derivados do ContentView e páginas derivadas de ContentPage.
ms.prod: xamarin
ms.assetid: 8B8E2360-6531-44A3-A7C8-9A8808DE9B86
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/13/2020
ms.openlocfilehash: a73123b89cba932f2e2cb907645f6fe858cf6176
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "79305132"
---
# <a name="xamarinforms-control-templates"></a>Modelos de controle do Xamarin.Forms

[![Baixar](~/media/shared/download.png) amostra Baixar a amostra](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/templates-controltemplatedemos)

Os modelos de controle Xamarin.Forms permitem [`ContentView`](xref:Xamarin.Forms.ContentView) definir a estrutura [`ContentPage`](xref:Xamarin.Forms.ContentPage) visual de controles personalizados derivados e páginas derivadas. Os modelos de controle separam a IU (interface do usuário) para uma página ou controle personalizado da lógica que implementa o controle ou a página. Também é possível inserir conteúdo adicional no controle modelo personalizado ou na página modelo, em um local predefinido.

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

A `CardView` classe, que deriva [`ContentView`](xref:Xamarin.Forms.ContentView) da classe, representa um controle personalizado que exibe dados em um layout semelhante a um cartão. A classe contém propriedades, que são apoiadas por propriedades associáveis, para os dados que ela exibe. No entanto, a classe `CardView` não define nenhuma interface do usuário. Em vez disso, a interface do usuário será definida com um modelo de controle. Para obter mais informações sobre como criar controles personalizados derivados de `ContentView`, veja [Xamarin.Forms ContentView](~/xamarin-forms/user-interface/layouts/contentview.md).

Um modelo de controle [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) é criado com o tipo. Quando você `ControlTemplate`cria um [`View`](xref:Xamarin.Forms.View) , você combina objetos para construir a ui para um controle personalizado ou página. Um `ControlTemplate` deve ter apenas um `View` como seu elemento raiz. No entanto, o elemento raiz geralmente contém outros objetos `View`. A combinação de objetos compõe a estrutura visual do controle.

Embora [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) um possa ser definido inline, a [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) abordagem típica para declarar um é como um recurso em um dicionário de recursos. Como os modelos de controle são recursos, eles obedecem às mesmas regras de escopo que se aplicam a todos os recursos. Por exemplo, se você declarar um modelo de controle no elemento raiz do arquivo do seu arquivo XAML de definição de aplicativo, o modelo poderá ser utilizado em qualquer lugar no aplicativo. Se você definir o modelo em uma página, somente essa página poderá usar o modelo de controle. Para obter mais informações sobre recursos, confira [Dicionários de Recursos do Xamarin.Forms](~/xamarin-forms/xaml/resource-dictionaries.md).

O exemplo XAML [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) a `CardView` seguir mostra um para objetos:

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

Quando [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) um é declarado como um recurso, ele deve `x:Key` ter uma chave especificada com o atributo para que possa ser identificado no dicionário de recursos. Neste exemplo, o elemento raiz do `CardViewControlTemplate` é um objeto [`Frame`](xref:Xamarin.Forms.Frame). O objeto `Frame` usa a extensão de marcação `RelativeSource` para definir o próprio `BindingContext` para a instância de objeto de runtime à qual o modelo será aplicado, que é conhecida como o *pai modelo*. O `Frame` objeto usa [`Grid`](xref:Xamarin.Forms.Grid)uma `Frame` [`Image`](xref:Xamarin.Forms.Image)combinação [`Label`](xref:Xamarin.Forms.Label)de [`BoxView`](xref:Xamarin.Forms.BoxView) , , , e `CardView` objetos para definir a estrutura visual de um objeto. As expressões de associação desses objetos são resolvidas em relação às propriedades `CardView`, devido à herança do `BindingContext` do elemento raiz `Frame`. Para obter mais informações sobre a expressão de marcação `RelativeSource`, veja [Associações Relativas do Xamarin.Forms](~/xamarin-forms/app-fundamentals/data-binding/relative-bindings.md).

## <a name="consume-a-controltemplate"></a>Consumir um ControlTemplate

A [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) pode ser aplicado [`ContentView`](xref:Xamarin.Forms.ContentView) a um controle [`ControlTemplate`](xref:Xamarin.Forms.TemplatedView.ControlTemplate) personalizado derivado definindo sua propriedade para o objeto modelo de controle. Da mesma [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) forma, um pode [`ContentPage`](xref:Xamarin.Forms.ContentPage) ser aplicado a [`ControlTemplate`](xref:Xamarin.Forms.TemplatedPage.ControlTemplate) uma página derivada definindo sua propriedade para o objeto modelo de controle. No tempo de [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) execução, quando um é aplicado, todos `ControlTemplate` os controles definidos no são adicionados à árvore visual do controle personalizado modelado ou página modelada.

O exemplo a `CardViewControlTemplate` seguir mostra [`ControlTemplate`](xref:Xamarin.Forms.TemplatedView.ControlTemplate) o ser `CardView` atribuído à propriedade de cada objeto:

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

Neste exemplo, os controles no `CardViewControlTemplate` tornam-se parte da árvore visual para cada objeto `CardView`. Como o [`Frame`](xref:Xamarin.Forms.Frame) objeto raiz do `BindingContext` modelo de controle define-o para o pai modelado, os `Frame` filhos e seus filhos resolvem suas expressões vinculativas contra as propriedades de cada `CardView` objeto.

As capturas de tela a seguir mostram o `CardViewControlTemplate` aplicado aos três objetos `CardView`:

[![Capturas de tela de objetos CardView modelos, no iOS e no Android](control-template-images/relativesource-controltemplate.png "Objetos CardView modelados")](control-template-images/relativesource-controltemplate-large.png#lightbox "Objetos CardView modelados")

> [!IMPORTANT]
> O ponto no [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) tempo em que a é aplicado a uma `OnApplyTemplate` instância de controle pode ser detectado substituindo o método no controle personalizado modelado ou na página modelada. Para obter mais informações, veja [Obter um elemento nomeado de um modelo](#get-a-named-element-from-a-template).

## <a name="pass-parameters-with-templatebinding"></a>Passar parâmetros com TemplateBinding

A `TemplateBinding` extensão de marcação vincula uma [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) propriedade de um elemento que está em um imóvel público definido pelo controle personalizado ou página modelada. Quando você usa um `TemplateBinding`, você habilita propriedades do controle para atuar como parâmetros para o modelo. Portanto, quando uma propriedade é definida em um controle modelo personalizado ou em uma página modelo, esse valor é passado para o elemento que contém o `TemplateBinding`.

> [!IMPORTANT]
> A `TemplateBinding` extensão de marcação [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) é uma `RelativeSource` alternativa à criação `BindingContext` de uma que usa a extensão de marcação para definir o elemento raiz no modelo para seu pai modelado. A extensão de marcação `TemplateBinding` elimina a associação de `RelativeSource` e substitui as expressões `Binding` por expressões `TemplateBinding`.

A extensão de marcação `TemplateBinding` define as seguintes propriedades:

- `Path`, do tipo `string`, o caminho para a propriedade.
- `Mode`, do tipo `BindingMode`, a direção em que as alterações se propagam entre a *origem* e o *destino*.
- `Converter`, do tipo `IValueConverter`, o conversor de valor de associação.
- `ConverterParameter`, do tipo `object`, o parâmetro do conversor de valor de associação.
- `StringFormat`, do tipo `string`, o formato da cadeia de caracteres para a associação.

O `ContentProperty` para a extensão de marcação `TemplateBinding` é `Path`. Portanto, a parte "Path=" da extensão de marcação poderá ser omitida se o caminho for o primeiro item na expressão `TemplateBinding`. Para obter mais informações sobre como usar essas propriedades em uma expressão de associação, veja [Associação de dados do Xamarin.Forms](~/xamarin-forms/app-fundamentals/data-binding/index.md).

> [!WARNING]
> A `TemplateBinding` extensão de marcação [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate)só deve ser usada dentro de um . No entanto, tentar usar uma expressão `TemplateBinding` fora de um `ControlTemplate` não resultará em um erro de build nem na geração de uma exceção.

O exemplo XAML [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) a `CardView` seguir mostra `TemplateBinding` um para objetos, que usa a extensão de marcação:

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

Neste exemplo, o *implícito* [`Style`](xref:Xamarin.Forms.Style) é automaticamente `CardView` aplicado a [`ControlTemplate`](xref:Xamarin.Forms.TemplatedView.ControlTemplate) cada objeto `CardView` `CardViewControlTemplate`e define a propriedade de cada um para .

Para saber mais sobre páginas, veja [Estilos do Xamarin.Forms](~/xamarin-forms/user-interface/styles/index.md).

## <a name="redefine-a-controls-ui"></a>Redefinir a interface do usuário de um controle

Quando [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) um é instanciado e `ControlTemplate` atribuído [`ContentView`](xref:Xamarin.Forms.ContentView) à propriedade de um [`ContentPage`](xref:Xamarin.Forms.ContentPage) controle personalizado derivado, ou uma página derivada, a estrutura visual `ControlTemplate`definida para o controle personalizado ou página é substituída pela estrutura visual definida no .

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

No entanto, os controles que compõem essa interface do ui podem ser substituídos definindo uma nova estrutura visual em um [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate), e atribuindo-a [`ControlTemplate`](xref:Xamarin.Forms.TemplatedView.ControlTemplate) à propriedade de um `CardViewUI` objeto:

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

Neste exemplo, a estrutura `CardViewUI` visual do objeto [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) é redefinida em um que fornece uma estrutura visual mais compacta que é adequada para uma lista condensada:

[![Capturas de tela de objetos CardViewUI modelos, no iOS e no Android](control-template-images/redefine-controltemplate.png "Objetos CardViewUI modelados")](control-template-images/redefine-controltemplate-large.png#lightbox "Objetos CardViewUI modelados")

## <a name="substitute-content-into-a-contentpresenter"></a>Substituir o conteúdo em um ContentPresenter

Um [`ContentPresenter`](xref:Xamarin.Forms.ContentPresenter) pode ser colocado em um modelo de controle para marcar onde o conteúdo a ser exibido pelo controle personalizado ou página modelada será exibido. A página ou controle personalizado ou a página que consome o modelo de controle definirá o conteúdo a ser exibido pelo `ContentPresenter`. O diagrama a seguir ilustra um `ControlTemplate` para uma página que contém um número de controles, incluindo um `ContentPresenter` marcado por um retângulo azul:

![Modelo de controle para uma ContentPage](control-template-images/control-template.png "Modelo de controle para uma ContentPage")

O XAML a seguir `TealTemplate` mostra um [`ContentPresenter`](xref:Xamarin.Forms.ContentPresenter) modelo de controle chamado que contém um em sua estrutura visual:

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

O exemplo `TealTemplate` a seguir [`ControlTemplate`](xref:Xamarin.Forms.TemplatedPage.ControlTemplate) mostra atribuído [`ContentPage`](xref:Xamarin.Forms.ContentPage) à propriedade de uma página derivada:

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

No tempo de `TealTemplate` execução, quando é aplicado à página, [`ContentPresenter`](xref:Xamarin.Forms.ContentPresenter) o conteúdo da página é substituído no definido no modelo de controle:

[![Capturas de tela do objeto de página modelo, no iOS e no Android](control-template-images/tealtemplate-contentpage.png "ContentPage modelada")](control-template-images/tealtemplate-contentpage-large.png#lightbox "ContentPage modelada")

## <a name="get-a-named-element-from-a-template"></a>Obter um elemento nomeado de um modelo

Elementos nomeados dentro de um modelo de controle podem ser recuperados do controle modelo personalizado ou da página modelo. Isso pode ser `GetTemplateChild` alcançado com o método, que retorna [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) o elemento nomeado na árvore visual instanciada, se encontrado. Caso contrário, ele retornará `null`.

Depois que um modelo de controle foi instanciado, o método `OnApplyTemplate` do modelo é chamado. O método `GetTemplateChild`, portanto, deve ser chamado de uma substituição de `OnApplyTemplate` no controle modelo ou na página modelo.

> [!IMPORTANT]
> O método `GetTemplateChild` só deve ser chamado após o método `OnApplyTemplate` ser chamado.

O XAML a seguir `TealTemplate` mostra um modelo [`ContentPage`](xref:Xamarin.Forms.ContentPage) de controle nomeado que pode ser aplicado a páginas derivadas:

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

Neste exemplo, [`Label`](xref:Xamarin.Forms.Label) o elemento é nomeado e pode ser recuperado no código da página modelada. Isso é feito chamando o método `GetTemplateChild` da substituição `OnApplyTemplate` para a página modelo:

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

Neste exemplo, [`Label`](xref:Xamarin.Forms.Label) o `changeThemeLabel` objeto nomeado é `ControlTemplate` recuperado uma vez que o foi instanciado. Então `changeThemeLabel` pode ser acessado e manipulado pela classe `AccessTemplateElementPage`. As capturas de tela a seguir mostram que o texto exibido pelo `Label` foi alterado:

[![Capturas de tela do objeto de página modelo, no iOS e no Android](control-template-images/get-named-element.png "ContentPage modelada")](control-template-images/get-named-element-large.png#lightbox "ContentPage modelada")

## <a name="bind-to-a-viewmodel"></a>Associar a um ViewModel

A [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) pode vincular os dados a `ControlTemplate` um modelo de exibição, mesmo quando o vínculo com o pai modelado (a instância do objeto em tempo de execução à qual o modelo é aplicado).

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

Neste exemplo, o `BindingContext` da página é definido para uma instância de `PeopleViewModel`. Esse ViewModel expõe uma coleção de `People` e um `ICommand` chamado `DeletePersonCommand`. A [`StackLayout`](xref:Xamarin.Forms.StackLayout) página na página usa um layout `People` vinculável para `ItemTemplate` vincular dados à coleta, `PersonTemplate` e o layout do bindable é definido como recurso. Isso [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) especifica que cada item da `People` coleção será `CardView` exibido usando um objeto. A estrutura visual `CardView` do objeto [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) é `CardViewControlTemplate`definida usando um nome :

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

Neste exemplo, o elemento [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) raiz [`Frame`](xref:Xamarin.Forms.Frame) do é um objeto. O objeto `Frame` usa a extensão de marcação `RelativeSource` para definir o próprio `BindingContext` como o pai modelo. As expressões de associação do objeto `Frame` e os respectivos filhos são resolvidas em relação às propriedades `CardView`, devido à herança do `BindingContext` do elemento raiz `Frame`. As capturas de tela a seguir mostram a página que exibe a coleção `People`, que consiste em três itens:

[![Capturas de tela de objetos CardView modelos, no iOS e no Android](control-template-images/viewmodel-controltemplate.png "Objetos CardView modelados")](control-template-images/viewmodel-controltemplate-large.png#lightbox "Objetos CardView modelados")

Enquanto os [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) objetos no bind e propriedades [`Button`](xref:Xamarin.Forms.Button) em seu pai modelado, o modelo `DeletePersonCommand` de controle dentro se liga tanto ao seu pai modelado quanto ao no modelo de exibição. Isso porque `Button.Command` a propriedade redefine sua fonte vinculante para ser o contexto vinculante `PeopleViewModel`do ancestral [`StackLayout`](xref:Xamarin.Forms.StackLayout)cujo tipo de contexto vinculante é , que é o . A parte `Path` das expressões de associação pode então resolver a propriedade `DeletePersonCommand`. No entanto, a `Button.CommandParameter` propriedade não altera sua fonte vinculante, [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate)em vez de herdá-la de seu pai no . Portanto, a propriedade `CommandParameter` é associada à propriedade `CardTitle` do `CardView`.

O efeito geral [`Button`](xref:Xamarin.Forms.Button) das vinculações `Button` é que quando `DeletePersonCommand` o `PeopleViewModel` é tocado, o na `CardName` classe é executado, com o valor do imóvel sendo repassado para o `DeletePersonCommand`. Isso resulta na remoção do `CardView` especificado do layout associável:

[![Capturas de tela de objetos CardView modelos, no iOS e no Android](control-template-images/viewmodel-itemdeleted.png "Objetos CardView modelados")](control-template-images/viewmodel-itemdeleted-large.png#lightbox "Objetos CardView modelados")

Para obter mais informações sobre associações relativas, veja [Associações Relativas do Xamarin.Forms](~/xamarin-forms/app-fundamentals/data-binding/relative-bindings.md).

## <a name="related-links"></a>Links relacionados

- [ControlTemplateDemos (amostra)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/templates-controltemplatedemos)
- [ContentView do Xamarin.Forms](~/xamarin-forms/user-interface/layouts/contentview.md)
- [Associações relativas do Xamarin.Forms](~/xamarin-forms/app-fundamentals/data-binding/relative-bindings.md)
- [Dicionários de recursos do Xamarin.Forms](~/xamarin-forms/xaml/resource-dictionaries.md)
- [Associação de dados do Xamarin.Forms](~/xamarin-forms/app-fundamentals/data-binding/index.md)
- [Estilos do Xamarin.Forms](~/xamarin-forms/user-interface/styles/index.md)
