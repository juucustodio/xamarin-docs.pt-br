---
title: Xamarin. Forms ImageButton
description: O ImageButton exibe uma imagem e responde a um toque ou clique que direciona um aplicativo para executar uma tarefa específica.
ms.prod: xamarin
ms.assetid: B5906AB6-3F79-4FCB-8C78-1F0AF18AB39E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/04/2019
ms.openlocfilehash: 7c6647a0299b5ece3caaaa1d322ec1a0efac3557
ms.sourcegitcommit: eca3b01098dba004d367292c8b0d74b58c4e1206
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/13/2020
ms.locfileid: "79304131"
---
# <a name="xamarinforms-imagebutton"></a>Xamarin. Forms ImageButton

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery)

_O ImageButton exibe uma imagem e responde a um toque ou clique que direciona um aplicativo para executar uma tarefa específica._

A exibição de `ImageButton` combina a exibição de [`Button`](xref:Xamarin.Forms.Button) e [`Image`](xref:Xamarin.Forms.Image) exibição para criar um botão cujo conteúdo é uma imagem. O usuário pressiona o `ImageButton` com um dedo ou clica com um mouse para direcionar o aplicativo para executar uma tarefa específica. No entanto, ao contrário do modo de exibição de `Button`, a exibição `ImageButton` não tem conceito de aparência de texto e texto.

> [!NOTE]
> Enquanto a exibição de [`Button`](xref:Xamarin.Forms.Button) define uma propriedade [`Image`](xref:Xamarin.Forms.Button.Image) , que permite que você exiba uma imagem no `Button`, essa propriedade deve ser usada ao exibir um ícone pequeno ao lado do texto `Button`.

Os exemplos de código neste guia são extraídos do [exemplo FormsGallery](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery).

## <a name="setting-the-image-source"></a>Definir a origem da imagem

`ImageButton` define uma propriedade `Source` que deve ser definida como a imagem a ser exibida no botão, com a origem da imagem sendo um arquivo, um URI, um recurso ou um fluxo. Para obter mais informações sobre como carregar imagens de fontes diferentes, consulte [imagens no Xamarin. Forms](images.md).

O exemplo a seguir mostra como criar uma instância de um `ImageButton` em XAML:

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

A propriedade `Source` especifica a imagem que aparece na `ImageButton`. Neste exemplo, ele é definido como um arquivo local que será carregado a partir de cada projeto da plataforma, resultando em capturas de tela as seguir:

[![ImageButton básico](imagebutton-images/BasicImageButton.png "ImageButton básico")](imagebutton-images/BasicImageButton-Large.png#lightbox "ImageButton básico")

Por padrão, a `ImageButton` é retangular, mas você pode dar a ela cantos arredondados usando a propriedade `CornerRadius`. Para obter mais informações sobre a aparência de `ImageButton`, consulte [aparência de ImageButton](#imagebutton-appearance).

> [!NOTE]
> Embora um `ImageButton` possa carregar um GIF animado, ele só exibirá o primeiro quadro do GIF.

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

`ImageButton` define um evento de `Clicked` que é disparado quando o usuário toca o `ImageButton` com um dedo ou ponteiro do mouse. O evento é acionado quando o botão do dedo ou do mouse é liberado da superfície do `ImageButton`. O `ImageButton` deve ter sua propriedade `IsEnabled` definida como `true` para responder a toques.

O exemplo a seguir mostra como criar uma instância de um `ImageButton` em XAML e manipular seu evento `Clicked`:

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

O evento `Clicked` é definido como um manipulador de eventos chamado `OnImageButtonClicked` que está localizado no arquivo code-behind:

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

Quando o `ImageButton` é tocado, o método `OnImageButtonClicked` é executado. O argumento `sender` é o `ImageButton` responsável por esse evento. Você pode usar isso para acessar o objeto `ImageButton` ou para distinguir entre vários objetos `ImageButton` que compartilham o mesmo evento `Clicked`.

Esse manipulador de `Clicked` específico incrementa um contador e exibe o valor do contador em uma [`Label`](xref:Xamarin.Forms.Label):

[![Clique em ImageButton básico](imagebutton-images/ImageButton.png "Clique em ImageButton básico")](imagebutton-images/ImageButton-Large.png#lightbox "Clique em ImageButton básico")

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

Às vezes, um aplicativo está em um estado específico em que um determinado `ImageButton` clique não é uma operação válida. Nesses casos, o `ImageButton` deve ser desabilitado definindo sua propriedade `IsEnabled` como `false`.

## <a name="using-the-command-interface"></a>Usando a interface de comando

É possível que um aplicativo responda a `ImageButton` toques sem manipular o evento `Clicked`. O `ImageButton` implementa um mecanismo de notificação alternativo chamado de _comando_ ou interface de _comando_ . Isso consiste em duas propriedades:

- `Command` do tipo [`ICommand`](xref:System.Windows.Input.ICommand), uma interface definida no namespace [`System.Windows.Input`](xref:System.Windows.Input) .
- `CommandParameter` Propriedade do tipo [`Object`](xref:System.Object).

Essa abordagem é adequada em conexão com a vinculação de dados e especialmente quando a implementação da arquitetura do Model-View-ViewModel (MVVM).

Para obter mais informações sobre como usar a interface de comando, consulte [usando a interface de comando](button.md#using-the-command-interface) no guia de [botão](button.md) .

## <a name="pressing-and-releasing-the-imagebutton"></a>Pressionando e soltando o ImageButton

Além do evento `Clicked`, `ImageButton` também define os eventos `Pressed` e `Released`. O evento `Pressed` ocorre quando um dedo se pressiona em uma `ImageButton`ou um botão do mouse é pressionado com o ponteiro posicionado sobre a `ImageButton`. O evento `Released` ocorre quando o botão do dedo ou do mouse é liberado. Em geral, o evento `Clicked` também é disparado ao mesmo tempo que o evento `Released`, mas se o ponteiro do dedo ou do mouse deslizar para longe da superfície do `ImageButton` antes de ser liberado, o evento `Clicked` pode não ocorrer.

Para obter mais informações sobre esses eventos, consulte [pressionar e liberar o botão](button.md#pressing-and-releasing-the-button) no guia de [botão](button.md) .

## <a name="imagebutton-appearance"></a>Aparência ImageButton

Além das propriedades que `ImageButton` herda da classe [`View`](xref:Xamarin.Forms.View) , `ImageButton` também define várias propriedades que afetam sua aparência:

- `Aspect` é como a imagem será dimensionada para se ajustar à área de exibição.
- `BorderColor` é a cor de uma área ao redor da `ImageButton`.
- `BorderWidth` é a largura da borda.
- `CornerRadius` é o raio de canto da `ImageButton`.

A propriedade `Aspect` pode ser definida como um dos membros da enumeração [`Aspect`](xref:Xamarin.Forms.Aspect) :

- [`Fill`](xref:Xamarin.Forms.Aspect.Fill) -alonga a imagem para preencher de forma completa e exata o `ImageButton`. Isso pode resultar na imagem que está sendo distorcida.
- [`AspectFill`](xref:Xamarin.Forms.Aspect.AspectFill) -corta a imagem para que ela preencha a `ImageButton` enquanto preserva a taxa de proporção.
- [`AspectFit`](xref:Xamarin.Forms.Aspect.AspectFit) -letterboxes a imagem (se necessário) para que toda a imagem caiba na `ImageButton`, com espaço em branco adicionado à parte superior/inferior ou aos lados, dependendo se a imagem é larga ou comprida. Esse é o valor padrão da enumeração [`Aspect`](xref:Xamarin.Forms.Aspect) .

> [!NOTE]
> A classe `ImageButton` também tem [`Margin`](xref:Xamarin.Forms.View.Margin) e `Padding` propriedades que controlam o comportamento de layout do `ImageButton`. Para saber mais, confira [Margens e preenchimento](~/xamarin-forms/user-interface/layouts/margin-and-padding.md).

## <a name="imagebutton-visual-states"></a>Estados visuais ImageButton

`ImageButton` tem uma [`VisualState`](xref:Xamarin.Forms.VisualState) `Pressed` que pode ser usada para iniciar uma alteração visual no `ImageButton` quando pressionada pelo usuário, desde que esteja habilitada.

O exemplo de XAML a seguir mostra como definir um estado visual para o estado de `Pressed`:

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

O `Pressed` [`VisualState`](xref:Xamarin.Forms.VisualState) especifica que quando o `ImageButton` for pressionado, sua propriedade [`Scale`](xref:Xamarin.Forms.VisualElement.Scale) será alterada de seu valor padrão de 1 para 0,8. O `Normal` `VisualState` especifica que quando o `ImageButton` estiver em um estado normal, sua propriedade `Scale` será definida como 1. Portanto, o efeito geral é que, quando o `ImageButton` é pressionado, ele é redimensionado para ser ligeiramente menor e quando o `ImageButton` é liberado, ele é redimensionado para seu tamanho padrão.

Para obter mais informações sobre os Estados visuais, consulte [o Gerenciador de estado visual do Xamarin. Forms](~/xamarin-forms/user-interface/visual-state-manager.md).

## <a name="related-links"></a>Links relacionados

- [Exemplo de FormsGallery](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery)
