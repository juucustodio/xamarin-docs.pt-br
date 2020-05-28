---
title: Xamarin.FormsImageButton
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
ms.openlocfilehash: 7d81c0ce4dc2a46a840a34cc9084c8f2388a0169
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/28/2020
ms.locfileid: "84137638"
---
# <a name="xamarinforms-imagebutton"></a>Xamarin.FormsImageButton

[![Baixar exemplo ](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery)

_O ImageButton exibe uma imagem e responde a um toque ou clique que direciona um aplicativo para executar uma tarefa específica._

A `ImageButton` exibição combina a [`Button`](xref:Xamarin.Forms.Button) exibição e a [`Image`](xref:Xamarin.Forms.Image) exibição para criar um botão cujo conteúdo é uma imagem. O usuário pressiona o `ImageButton` com um dedo ou clica nele com um mouse para direcionar o aplicativo para executar uma tarefa específica. No entanto, ao contrário da `Button` exibição, a `ImageButton` exibição não tem nenhum conceito de aparência de texto e texto.

> [!NOTE]
> Enquanto a [`Button`](xref:Xamarin.Forms.Button) exibição define uma [`Image`](xref:Xamarin.Forms.Button.Image) propriedade, que permite que você exiba uma imagem no `Button` , essa propriedade deve ser usada ao exibir um ícone pequeno ao lado do `Button` texto.

Os exemplos de código neste guia são extraídos do [exemplo FormsGallery](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery).

## <a name="setting-the-image-source"></a>Configurando a origem da imagem

`ImageButton`define uma `Source` propriedade que deve ser definida como a imagem a ser exibida no botão, com a origem da imagem sendo um arquivo, um URI, um recurso ou um fluxo. Para obter mais informações sobre como carregar imagens de fontes diferentes, consulte [imagens em Xamarin.Forms ](images.md).

O exemplo a seguir mostra como criar uma instância de `ImageButton` em XAML:

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

A `Source` propriedade especifica a imagem que aparece no `ImageButton` . Neste exemplo, ele é definido como um arquivo local que será carregado de cada projeto de plataforma, resultando nas seguintes capturas de tela:

[![ImageButton básico](imagebutton-images/BasicImageButton.png "ImageButton básico")](imagebutton-images/BasicImageButton-Large.png#lightbox "ImageButton básico")

Por padrão, o `ImageButton` é retangular, mas você pode dar a ele cantos arredondados usando a `CornerRadius` propriedade. Para obter mais informações sobre `ImageButton` aparência, consulte [aparência de ImageButton](#imagebutton-appearance).

> [!NOTE]
> Embora um `ImageButton` possa carregar um GIF animado, ele só exibirá o primeiro quadro do GIF.

O exemplo a seguir mostra como criar uma página que é funcionalmente equivalente ao exemplo de XAML anterior, mas inteiramente em C#:

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

## <a name="handling-imagebutton-clicks"></a>Manipulando cliques de ImageButton

`ImageButton`define um `Clicked` evento que é disparado quando o usuário toca no `ImageButton` com um ponteiro de dedo ou mouse. O evento é acionado quando o botão do dedo ou do mouse é liberado da superfície do `ImageButton` . O `ImageButton` deve ter sua `IsEnabled` propriedade definida como `true` para responder a toques.

O exemplo a seguir mostra como criar uma instância de `ImageButton` em XAML e manipular seu `Clicked` evento:

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

Quando o `ImageButton` é tocado, o método `OnImageButtonClicked` é executado. O `sender` argumento é o `ImageButton` responsável por esse evento. Você pode usar isso para acessar o `ImageButton` objeto ou para distinguir entre vários `ImageButton` objetos que compartilham o mesmo `Clicked` evento.

Esse `Clicked` manipulador específico incrementa um contador e exibe o valor do contador em um [`Label`](xref:Xamarin.Forms.Label) :

[![Clique em ImageButton básico](imagebutton-images/ImageButton.png "Clique em ImageButton básico")](imagebutton-images/ImageButton-Large.png#lightbox "Clique em ImageButton básico")

O exemplo a seguir mostra como criar uma página que é funcionalmente equivalente ao exemplo de XAML anterior, mas inteiramente em C#:

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

Às vezes, um aplicativo está em um estado específico em que um determinado `ImageButton` clique não é uma operação válida. Nesses casos, o `ImageButton` deve ser desabilitado definindo sua `IsEnabled` propriedade como `false` .

## <a name="using-the-command-interface"></a>Usando a interface de comando

É possível que um aplicativo responda a `ImageButton` toques sem manipular o `Clicked` evento. O `ImageButton` implementa um mecanismo de notificação alternativo chamado de _comando_ ou interface de _comando_ . Isso consiste em duas propriedades:

- `Command`do tipo [`ICommand`](xref:System.Windows.Input.ICommand) , uma interface definida no [`System.Windows.Input`](xref:System.Windows.Input) namespace.
- `CommandParameter`Propriedade do tipo [`Object`](xref:System.Object) .

Essa abordagem é adequada em relação à vinculação de dados e, particularmente, ao implementar a arquitetura MVVM (Model-View-ViewModel).

Para obter mais informações sobre como usar a interface de comando, consulte [usando a interface de comando](button.md#using-the-command-interface) no guia de [botão](button.md) .

## <a name="pressing-and-releasing-the-imagebutton"></a>Pressionar e liberar o ImageButton

Além de evento `Clicked`, `ImageButton` também define eventos `Pressed` e `Released`. O `Pressed` evento ocorre quando um dedo pressiona em um `ImageButton` ou um botão do mouse é pressionado com o ponteiro posicionado sobre o `ImageButton` . O `Released` evento ocorre quando o botão do dedo ou do mouse é liberado. Em geral, o `Clicked` evento também é disparado ao mesmo tempo que o `Released` evento, mas se o ponteiro do dedo ou do mouse deslizar para longe da superfície do `ImageButton` antes de ser liberado, o `Clicked` evento poderá não ocorrer.

Para obter mais informações sobre esses eventos, consulte [pressionar e liberar o botão](button.md#pressing-and-releasing-the-button) no guia de [botão](button.md) .

## <a name="imagebutton-appearance"></a>Aparência de ImageButton

Além das propriedades `ImageButton` herdadas da [`View`](xref:Xamarin.Forms.View) classe, o `ImageButton` também define várias propriedades que afetam sua aparência:

- `Aspect`é como a imagem será dimensionada para caber na área de exibição.
- `BorderColor`é a cor de uma área ao redor do `ImageButton` .
- `BorderWidth`é a largura da borda.
- `CornerRadius`é o raio de canto do `ImageButton` .

A `Aspect` propriedade pode ser definida como um dos membros da [`Aspect`](xref:Xamarin.Forms.Aspect) enumeração:

- [`Fill`](xref:Xamarin.Forms.Aspect.Fill)-Alonga a imagem para preencher de forma completa e exata o `ImageButton` . Isso pode resultar na distorção da imagem.
- [`AspectFill`](xref:Xamarin.Forms.Aspect.AspectFill)-Corta a imagem para que ela preencha o `ImageButton` enquanto preserva a taxa de proporção.
- [`AspectFit`](xref:Xamarin.Forms.Aspect.AspectFit)-letterboxes a imagem (se necessário) para que toda a imagem caiba no `ImageButton` , com espaço em branco adicionado à parte superior/inferior ou aos lados, dependendo se a imagem é larga ou comprida. Esse é o valor padrão da [`Aspect`](xref:Xamarin.Forms.Aspect) enumeração.

> [!NOTE]
> A `ImageButton` classe também tem [`Margin`](xref:Xamarin.Forms.View.Margin) e `Padding` Propriedades que controlam o comportamento de layout do `ImageButton` . Para saber mais, confira [Margens e preenchimento](~/xamarin-forms/user-interface/layouts/margin-and-padding.md).

## <a name="imagebutton-visual-states"></a>Estados visuais ImageButton

`ImageButton`tem um `Pressed` [`VisualState`](xref:Xamarin.Forms.VisualState) que pode ser usado para iniciar uma alteração Visual para o `ImageButton` quando pressionado pelo usuário, desde que ele esteja habilitado.

O exemplo de XAML a seguir mostra como definir um estado visual para o `Pressed` estado:

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

O `Pressed` [`VisualState`](xref:Xamarin.Forms.VisualState) especifica que quando o `ImageButton` for pressionado, sua [`Scale`](xref:Xamarin.Forms.VisualElement.Scale) propriedade será alterada de seu valor padrão de 1 para 0,8. O `Normal` `VisualState` especifica que quando o `ImageButton` estiver em um estado normal, sua `Scale` propriedade será definida como 1. Portanto, o efeito geral é que quando o `ImageButton` é pressionado, ele é redimensionado para ser ligeiramente menor e quando o `ImageButton` é liberado, ele é redimensionado para seu tamanho padrão.

Para obter mais informações sobre os Estados visuais, consulte [o Xamarin.Forms Visual State Manager](~/xamarin-forms/user-interface/visual-state-manager.md).

## <a name="related-links"></a>Links relacionados

- [Exemplo de FormsGallery](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery)
