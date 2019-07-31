---
title: Xamarin. Forms ImageButton
description: O ImageButton exibe uma imagem e responde a um toque ou clique que direciona um aplicativo para executar uma tarefa específica.
ms.prod: xamarin
ms.assetid: B5906AB6-3F79-4FCB-8C78-1F0AF18AB39E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/19/2018
ms.openlocfilehash: 90d860cb6429025672a058e22e76f029872d3b34
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68657170"
---
# <a name="xamarinforms-imagebutton"></a>Xamarin. Forms ImageButton

[![Baixar exemplo](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery)

_O ImageButton exibe uma imagem e responde a um toque ou clique que direciona um aplicativo para executar uma tarefa específica._

O `ImageButton` exibir combina o [ `Button` ](xref:Xamarin.Forms.Button) modo de exibição e [ `Image` ](xref:Xamarin.Forms.Image) modo de exibição para criar um botão cujo conteúdo é uma imagem. O usuário pressiona o `ImageButton` com um dedo ou clica nele com o mouse para direcionar o aplicativo para executar uma tarefa específica. No entanto, ao contrário do `Button` modo de exibição, o `ImageButton` exibição não tem nenhum conceito de texto e a aparência do texto.

> [!NOTE]
> Enquanto o [ `Button` ](xref:Xamarin.Forms.Button) view define uma [ `Image` ](xref:Xamarin.Forms.Button.Image) propriedade, que lhe permite exibir uma imagem no `Button`, esta propriedade destina-se a ser usado ao exibir um ícone pequeno ao lado de `Button` texto.

Os exemplos de código neste guia são tirados de [FormsGallery exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery).

## <a name="setting-the-image-source"></a>Definir a origem da imagem

`ImageButton` define um `Source` propriedade que deve ser definida como a imagem para exibir no botão com a origem da imagem que está sendo um arquivo, um URI, um recurso ou um fluxo. Para obter mais informações sobre como carregar imagens de origens diferentes, consulte [imagens no xamarin. Forms](images.md).

O exemplo a seguir mostra como instanciar um `ImageButton` no XAML:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="FormsGallery.XamlExamples.ImageButtonDemoPage"
             Title="ImageButton Demo">
    <StackLayout>
        <Label Text="ImageButton"
               FontSize="50"
               FontAttributes="Bold"
               HorizontalOptions="Center" />

       <ImageButton Source="XamarinLogo.png"
                    HorizontalOptions="Center"
                    VerticalOptions="CenterAndExpand" />
    </StackLayout>
</ContentPage>
```

O `Source` propriedade especifica a imagem que aparece no `ImageButton`. Neste exemplo, ele é definido como um arquivo local que será carregado a partir de cada projeto da plataforma, resultando em capturas de tela as seguir:

[![Básica ImageButton](imagebutton-images/BasicImageButton.png "ImageButton básica")](imagebutton-images/BasicImageButton-Large.png#lightbox "ImageButton básico")

Por padrão, o `ImageButton` é retangular, mas você pode atribuir os cantos arredondado de it, usando o `CornerRadius` propriedade. Para obter mais informações sobre `ImageButton` aparência, consulte [ImageButton aparência](#imagebutton-appearance).

O exemplo a seguir mostra como criar uma página que é funcionalmente equivalente ao exemplo XAML anterior, mas inteiramente no C#:

```csharp
public class ImageButtonDemoPage : ContentPage
{
    public ImageButtonDemoPage()
    {
        Label header = new Label
        {
            Text = "ImageButton",
            FontSize = 50,
            FontAttributes = FontAttributes.Bold,
            HorizontalOptions = LayoutOptions.Center
        };

        ImageButton imageButton = new ImageButton
        {
            Source = "XamarinLogo.png",
            HorizontalOptions = LayoutOptions.Center,
            VerticalOptions = LayoutOptions.CenterAndExpand
        };

        // Build the page.
        Title = "ImageButton Demo";
        Content = new StackLayout
        {
            Children = { header, imageButton }
        };
    }
}
```

## <a name="handling-imagebutton-clicks"></a>Tratamento ImageButton clica

`ImageButton` define uma `Clicked` evento que é acionado quando o usuário toca o `ImageButton` com um ponteiro de mouse ou o dedo. O evento é acionado quando o botão de dedo ou o mouse é liberado da superfície do `ImageButton`. O `ImageButton` deve ter seu `IsEnabled` propriedade definida como `true` para responder aos toques.

O exemplo a seguir mostra como instanciar uma `ImageButton` em XAML e o identificador da sua `Clicked` eventos:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="FormsGallery.XamlExamples.ImageButtonDemoPage"
             Title="ImageButton Demo">
    <StackLayout>
        <Label Text="ImageButton"
               FontSize="50"
               FontAttributes="Bold"
               HorizontalOptions="Center" />

       <ImageButton Source="XamarinLogo.png"
                    HorizontalOptions="Center"
                    VerticalOptions="CenterAndExpand"
                    Clicked="OnImageButtonClicked" />

        <Label x:Name="label"
               Text="0 ImageButton clicks"
               FontSize="Large"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand" />
    </StackLayout>
</ContentPage>
```

O `Clicked` evento é definido como um manipulador de eventos chamado `OnImageButtonClicked` que está localizado no arquivo code-behind:

```csharp
public partial class ImageButtonDemoPage : ContentPage
{
    int clickTotal;

    public ImageButtonDemoPage()
    {
        InitializeComponent();
    }

    void OnImageButtonClicked(object sender, EventArgs e)
    {
        clickTotal += 1;
        label.Text = $"{clickTotal} ImageButton click{(clickTotal == 1 ? "" : "s")}";
    }
}
```

Quando o `ImageButton` é tocado, o `OnImageButtonClicked` método é executado. O `sender` argumento é o `ImageButton` responsável por esse evento. Você pode usar isso para acessar o `ImageButton` objeto, ou para distinguir entre vários `ImageButton` objetos que compartilham o mesmo `Clicked` eventos.

Essa determinada `Clicked` manipulador incrementa um contador e exibe o valor do contador em um [ `Label` ](xref:Xamarin.Forms.Label):

[![Clique básica de ImageButton](imagebutton-images/ImageButton.png "básico clique de ImageButton")](imagebutton-images/ImageButton-Large.png#lightbox "clique de ImageButton básico")

O exemplo a seguir mostra como criar uma página que é funcionalmente equivalente ao exemplo XAML anterior, mas inteiramente no C#:

```csharp
public class ImageButtonDemoPage : ContentPage
{
    Label label;
    int clickTotal = 0;

    public ImageButtonDemoPage()
    {
        Label header = new Label
        {
            Text = "ImageButton",
            FontSize = 50,
            FontAttributes = FontAttributes.Bold,
            HorizontalOptions = LayoutOptions.Center
        };

        ImageButton imageButton = new ImageButton
        {
            Source = "XamarinLogo.png",
            HorizontalOptions = LayoutOptions.Center,
            VerticalOptions = LayoutOptions.CenterAndExpand
        };
        imageButton.Clicked += OnImageButtonClicked;

        label = new Label
        {
            Text = "0 ImageButton clicks",
            FontSize = Device.GetNamedSize(NamedSize.Large, typeof(Label)),
            HorizontalOptions = LayoutOptions.Center,
            VerticalOptions = LayoutOptions.CenterAndExpand
        };

        // Build the page.
        Title = "ImageButton Demo";
        Content = new StackLayout
        {
            Children =
            {
                header,
                imageButton,
                label
            }
        };
    }

    void OnImageButtonClicked(object sender, EventArgs e)
    {
        clickTotal += 1;
        label.Text = $"{clickTotal} ImageButton click{(clickTotal == 1 ? "" : "s")}";
    }
}
```

## <a name="disabling-the-imagebutton"></a>Desabilitando o ImageButton

Às vezes, um aplicativo está em um estado específico em que um determinado `ImageButton` clique não é uma operação válida. Nesses casos, o `ImageButton` deve ser desabilitado definindo seu `IsEnabled` propriedade `false`.

## <a name="using-the-command-interface"></a>Usando a interface de comando

É possível que um aplicativo para responder às `ImageButton` toques sem tratamento a `Clicked` eventos. O `ImageButton` implementa um mecanismo de notificação alternativo chamado a _comando_ ou _comandando_ interface. Isso consiste em duas propriedades:

- `Command` do tipo [ `ICommand` ](xref:System.Windows.Input.ICommand), uma interface definida a [ `System.Windows.Input` ](xref:System.Windows.Input) namespace.
- `CommandParameter` propriedade do tipo [ `Object` ](xref:System.Object).

Essa abordagem é adequada em conexão com a vinculação de dados e especialmente quando a implementação da arquitetura do Model-View-ViewModel (MVVM).

Para obter mais informações sobre como usar a interface de comando, consulte [usando a interface de comando](button.md#using-the-command-interface) na [botão](button.md) guia.

## <a name="pressing-and-releasing-the-imagebutton"></a>Pressionando e soltando o ImageButton

Além de `Clicked` evento `ImageButton` também define `Pressed` e `Released` eventos. O `Pressed` evento ocorre quando um dedo pressiona em um `ImageButton`, ou um botão do mouse é pressionado com o ponteiro posicionado sobre o `ImageButton`. O `Released` evento ocorre quando o botão de dedo ou o mouse é liberado. Em geral, o `Clicked` evento também é acionado ao mesmo tempo que o `Released` evento, mas se o ponteiro do mouse ou o dedo desliza para fora a superfície do `ImageButton` antes de serem liberadas, o `Clicked` não pode ocorrer um evento.

Para obter mais informações sobre esses eventos, consulte [pressionando e soltando o botão](button.md#pressing-and-releasing-the-button) na [botão](button.md) guia.

## <a name="imagebutton-appearance"></a>Aparência ImageButton

Além das propriedades que `ImageButton` herda a [ `View` ](xref:Xamarin.Forms.View) classe, `ImageButton` também define várias propriedades que afetam sua aparência:

- `Aspect` é como a imagem será dimensionada para caber na área de exibição.
- `BorderColor` é a cor de uma área ao redor de `ImageButton`.
- `BorderWidth` é a largura da borda.
- `CornerRadius` é o raio do canto do `ImageButton`.

O `Aspect` propriedade pode ser definida para um dos membros de [ `Aspect` ](xref:Xamarin.Forms.Aspect) enumeração:

- [`Fill`](xref:Xamarin.Forms.Aspect.Fill) -Alonga a imagem para preencher completamente e exatamente o `ImageButton`. Isso pode resultar na imagem que está sendo distorcida.
- [`AspectFill`](xref:Xamarin.Forms.Aspect.AspectFill) -Corta a imagem de modo que ele preencha o `ImageButton` , preservando a taxa de proporção.
- [`AspectFit`](xref:Xamarin.Forms.Aspect.AspectFit) -e Letterbox a imagem (se necessário) para que a imagem inteira se adapta a `ImageButton`, com espaço em branco adicionado para o de cima para baixo ou lados dependendo se a imagem é altas ou largas. Isso é o valor padrão de [ `Aspect` ](xref:Xamarin.Forms.Aspect) enumeração.

> [!NOTE]
> O `ImageButton` classe também tem [ `Margin` ](xref:Xamarin.Forms.View.Margin) e `Padding` as propriedades que controlam o comportamento de layout a `ImageButton`. Para obter mais informações, consulte [margem e preenchimento](~/xamarin-forms/user-interface/layouts/margin-and-padding.md).

## <a name="imagebutton-visual-states"></a>Estados visuais ImageButton

`ImageButton` tem um `Pressed` [ `VisualState` ](xref:Xamarin.Forms.VisualState) que pode ser usado para iniciar uma alteração visual para o `ImageButton` quando pressionado pelo usuário, desde que ele está habilitado.

O exemplo XAML a seguir mostra como definir um estado visual para o `Pressed` estado:

```xaml
<ImageButton Source="XamarinLogo.png"
             ...>
    <VisualStateManager.VisualStateGroups>
        <VisualStateGroup x:Name="CommonStates">
            <VisualState x:Name="Normal">
                <VisualState.Setters>
                    <Setter Property="Scale"
                            Value="1" />
                </VisualState.Setters>
            </VisualState>

            <VisualState x:Name="Pressed">
                <VisualState.Setters>
                    <Setter Property="Scale"
                            Value="0.8" />
                </VisualState.Setters>
            </VisualState>

        </VisualStateGroup>
    </VisualStateManager.VisualStateGroups>
</ImageButton>
```

O `Pressed` [ `VisualState` ](xref:Xamarin.Forms.VisualState) Especifica que, quando o `ImageButton` é pressionado, seu [ `Scale` ](xref:Xamarin.Forms.VisualElement.Scale) propriedade será alterada do valor padrão de 1 para 0,8. O `Normal` `VisualState` Especifica que, quando o `ImageButton` está em um estado normal, seu `Scale` propriedade será definida como 1. Portanto, o efeito geral é que quando o `ImageButton` é pressionado, ele será escalada novamente para ser um pouco menores e quando o `ImageButton` é lançado, ele será escalada novamente ao seu tamanho padrão.

Para obter mais informações sobre estados visuais, consulte [o Gerenciador de estado Visual xamarin. Forms](~/xamarin-forms/user-interface/visual-state-manager.md).

## <a name="related-links"></a>Links relacionados

- [Exemplo de FormsGallery](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery)
