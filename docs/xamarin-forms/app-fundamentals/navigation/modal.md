---
title: Páginas modais do Xamarin.Forms
description: O Xamarin.Forms dá suporte a páginas modais. Uma página modal incentiva os usuários a concluir uma tarefa independente da qual não se pode sair via navegação até que essa tarefa seja concluída ou cancelada. Este artigo demonstra como navegar até as páginas modais.
ms.prod: xamarin
ms.assetid: 486CB7FD-2B9A-4DE3-94BD-C8D904E5D3C6
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/01/2017
ms.openlocfilehash: 7a4c67f067b73873c3d1de4499abda2703217ddf
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "70760820"
---
# <a name="xamarinforms-modal-pages"></a>Páginas modais do Xamarin.Forms

[![Baixar](~/media/shared/download.png) amostra Baixar a amostra](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-modal)

_Xamarin.Forms fornece suporte para páginas modais. Uma página modal incentiva os usuários a completar uma tarefa independente que não pode ser navegada até que a tarefa seja concluída ou cancelada. Este artigo demonstra como navegar para páginas modais._

Este artigo discute os seguintes tópicos:

- [Executar a navegação](#Performing_Navigation) – enviar páginas por push para a pilha modal, remover páginas da pilha modal, desabilitar o botão Voltar e animar transições de página.
- [Passar dados ao navegar](#Passing_Data_when_Navigating) – passar dados por meio de um construtor de página e de um `BindingContext`.

## <a name="overview"></a>Visão geral

Uma página restrita pode ser qualquer um dos tipos de [Página](~/xamarin-forms/user-interface/controls/pages.md) para os quais o Xamarin.Forms dá suporte. Para exibir uma página modal, o aplicativo a enviará por push para a pilha modal, na qual ela se tornará a página ativa, conforme mostrado no diagrama a seguir:

![](modal-images/pushing.png "Pushing a Page to the Modal Stack")

Para retornar à página anterior, o aplicativo removerá a página atual da pilha modal e, em seguida, a nova página de nível superior se tornará a página ativa, conforme mostrado no diagrama a seguir:

![](modal-images/popping.png "Popping a Page from the Modal Stack")

<a name="Performing_Navigation" />

## <a name="performing-navigation"></a>Executando a navegação

Os métodos de navegação modal são expostos pela [`Navigation`](xref:Xamarin.Forms.NavigableElement.Navigation) propriedade em quaisquer [`Page`](xref:Xamarin.Forms.Page) tipos derivados. Esses métodos possibilitam [enviar páginas modais por push](#Pushing_Pages_to_the_Modal_Stack) para a pilha modal e [remover páginas modais](#Popping_Pages_from_the_Modal_Stack) da pilha modal.

O [`Navigation`](xref:Xamarin.Forms.NavigableElement.Navigation) imóvel também [`ModalStack`](xref:Xamarin.Forms.INavigation.ModalStack) expõe um imóvel do qual as páginas modais na pilha modal podem ser obtidas. No entanto, não há nenhum conceito de realização de manipulação de pilha modal ou remoção do item mais recente da pilha até a página raiz na navegação modal. Isso ocorre porque não há suporte universal para essas operações pelas plataformas subjacentes.

> [!NOTE]
>  Uma instância de [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) não é necessária para executar a navegação de página modal.

<a name="Pushing_Pages_to_the_Modal_Stack" />

### <a name="pushing-pages-to-the-modal-stack"></a>Enviando páginas por push para a pilha modal

Para navegar `ModalPage` até o local [`PushModalAsync`](xref:Xamarin.Forms.INavigation.PushModalAsync*) é necessário [`Navigation`](xref:Xamarin.Forms.NavigableElement.Navigation) invocar o método na propriedade da página atual, conforme demonstrado no exemplo de código a seguir:

```csharp
async void OnItemSelected (object sender, SelectedItemChangedEventArgs e)
{
  if (listView.SelectedItem != null) {
    var detailPage = new DetailPage ();
    ...
    await Navigation.PushModalAsync (detailPage);
  }
}
```

Isso faz `ModalPage` com que a instância seja empurrada para a pilha modal, onde se torna a [`ListView`](xref:Xamarin.Forms.ListView) página `MainPage` ativa, desde que um item tenha sido selecionado na instância. A instância de `ModalPage` é mostrada nas capturas de tela a seguir:

![](modal-images/modalpage.png "Modal Page Example")

Quando [`PushModalAsync`](xref:Xamarin.Forms.INavigation.PushModalAsync*) é invocado, ocorrem os seguintes eventos:

- A chamada `PushModalAsync` de [`OnDisappearing`](xref:Xamarin.Forms.Page.OnDisappearing) página tem sua substituição invocada, desde que a plataforma subjacente não seja Android.
- A página que está [`OnAppearing`](xref:Xamarin.Forms.Page.OnAppearing) sendo navegada tem sua substituição invocada.
- A tarefa `PushAsync` é concluída.

No entanto, a ordem exata em que esses eventos ocorrem depende da plataforma. Para obter mais informações, consulte [o capítulo 24](https://developer.xamarin.com/r/xamarin-forms/book/chapter24.pdf) do livro Xamarin.Forms de Charles Petzold.

> [!NOTE]
> Chamadas para [`OnDisappearing`](xref:Xamarin.Forms.Page.OnDisappearing) [`OnAppearing`](xref:Xamarin.Forms.Page.OnAppearing) as substituições e substituições não podem ser tratadas como indicações garantidas de navegação de página. Por exemplo, no iOS, a substituição `OnDisappearing` é chamada na página ativa quando o aplicativo é encerrado.

<a name="Popping_Pages_from_the_Modal_Stack" />

### <a name="popping-pages-from-the-modal-stack"></a>Removendo páginas da pilha modal

A página ativa pode ser removida como o item mais recente da pilha modal pressionando o botão *Voltar* no dispositivo, independentemente de este ser um botão físico no dispositivo ou um botão na tela.

Para retornar programáticamente à página `ModalPage` original, [`PopModalAsync`](xref:Xamarin.Forms.INavigation.PopModalAsync) a instância deve invocar o método, conforme demonstrado no exemplo de código a seguir:

```csharp
async void OnDismissButtonClicked (object sender, EventArgs args)
{
  await Navigation.PopModalAsync ();
}
```

Isso faz com que a instância `ModalPage` seja removida da pilha modal, com a nova página de nível superior tornando-se a página ativa. Quando [`PopModalAsync`](xref:Xamarin.Forms.INavigation.PopModalAsync) é invocado, ocorrem os seguintes eventos:

- A chamada `PopModalAsync` de [`OnDisappearing`](xref:Xamarin.Forms.Page.OnDisappearing) página tem sua substituição invocada.
- A página que está [`OnAppearing`](xref:Xamarin.Forms.Page.OnAppearing) sendo devolvida tem sua substituição invocada, desde que a plataforma subjacente não seja Android.
- A tarefa `PopModalAsync` é retornada.

No entanto, a ordem exata em que esses eventos ocorrem depende da plataforma. Para obter mais informações, consulte [o capítulo 24](https://developer.xamarin.com/r/xamarin-forms/book/chapter24.pdf) do livro Xamarin.Forms de Charles Petzold.

### <a name="disabling-the-back-button"></a>Desabilitando o botão Voltar

No Android, o usuário sempre pode retornar à página anterior pressionando o botão *Voltar* padrão no dispositivo. Se a página modal exigir que o usuário conclua uma tarefa independente antes de sair da página, o aplicativo deverá desabilitar o botão *Voltar*. Isso pode ser feito [`Page.OnBackButtonPressed`](xref:Xamarin.Forms.Page.OnBackButtonPressed) substituindo o método na página modal. Para obter mais informações, confira o [Capítulo 24](https://developer.xamarin.com/r/xamarin-forms/book/chapter24.pdf) do livro de Charles Petzold sobre Xamarin.Forms.

### <a name="animating-page-transitions"></a>Animando transições de página

A [`Navigation`](xref:Xamarin.Forms.NavigableElement.Navigation) propriedade de cada página também fornece métodos de `boolean` pressão e pop substituídos que incluem um parâmetro que controla se deve exibir uma animação de página durante a navegação, como mostrado no seguinte exemplo de código:

```csharp
async void OnNextPageButtonClicked (object sender, EventArgs e)
{
  // Page appearance not animated
  await Navigation.PushModalAsync (new DetailPage (), false);
}

async void OnDismissButtonClicked (object sender, EventArgs args)
{
  // Page appearance not animated
  await Navigation.PopModalAsync (false);
}
```

Definir o parâmetro `boolean` como `false` desabilita a animação de transição de página, enquanto definir o parâmetro como `true` habilita a animação de transição de página, desde que ela tenha suporte da plataforma subjacente. No entanto, os métodos de envio por push e remoção que não têm esse parâmetro habilitam a animação por padrão.

<a name="Passing_Data_when_Navigating" />

## <a name="passing-data-when-navigating"></a>Transmitindo dados ao navegar

Às vezes, é necessário que uma página transmita dados para outra página durante a navegação. Duas técnicas para conseguir isso são passando dados através de um construtor [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) de páginas e definindo as novas páginas para os dados. Descreveremos cada técnica separadamente.

### <a name="passing-data-through-a-page-constructor"></a>Transmitindo dados por meio de um construtor de página

A técnica mais simples para transmitir dados para outra página durante a navegação é por meio de um parâmetro de construtor de página, que é mostrado no exemplo de código a seguir:

```csharp
public App ()
{
  MainPage = new MainPage (DateTime.Now.ToString ("u")));
}
```

Esse código cria uma instância de `MainPage`, transmitindo a data e a hora atuais no formato ISO8601.

A instância de `MainPage` recebe os dados por meio de um parâmetro de construtor, conforme mostrado no exemplo de código a seguir:

```csharp
public MainPage (string date)
{
  InitializeComponent ();
  dateLabel.Text = date;
}
```

Os dados são exibidos na página [`Label.Text`](xref:Xamarin.Forms.Label.Text) definindo a propriedade.

### <a name="passing-data-through-a-bindingcontext"></a>Transmitindo dados por meio de um BindingContext

Uma abordagem alternativa para passar dados para outra página [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) durante a navegação é definindo as novas páginas para os dados, como mostrado no exemplo de código a seguir:

```csharp
async void OnItemSelected (object sender, SelectedItemChangedEventArgs e)
{
  if (listView.SelectedItem != null) {
    var detailPage = new DetailPage ();
    detailPage.BindingContext = e.SelectedItem as Contact;
    listView.SelectedItem = null;
    await Navigation.PushModalAsync (detailPage);
  }
}
```

Este código [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) define `DetailPage` a ocorrência `Contact` para a instância e, em seguida, navega para o `DetailPage`.

O `DetailPage`, em seguida, usa a associação de dados para exibir os dados da instância de `Contact`, conforme mostrado no exemplo de código XAML a seguir:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="ModalNavigation.DetailPage">
    <ContentPage.Padding>
      <OnPlatform x:TypeArguments="Thickness">
        <On Platform="iOS" Value="0,40,0,0" />
      </OnPlatform>
    </ContentPage.Padding>
    <ContentPage.Content>
        <StackLayout HorizontalOptions="Center" VerticalOptions="Center">
            <StackLayout Orientation="Horizontal">
                <Label Text="Name:" FontSize="Medium" HorizontalOptions="FillAndExpand" />
                <Label Text="{Binding Name}" FontSize="Medium" FontAttributes="Bold" />
            </StackLayout>
              ...
            <Button x:Name="dismissButton" Text="Dismiss" Clicked="OnDismissButtonClicked" />
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

O exemplo de código a seguir mostra como a associação de dados pode ser feita em C#:

```csharp
public class DetailPageCS : ContentPage
{
  public DetailPageCS ()
  {
    var nameLabel = new Label {
      FontSize = Device.GetNamedSize (NamedSize.Medium, typeof(Label)),
      FontAttributes = FontAttributes.Bold
    };
    nameLabel.SetBinding (Label.TextProperty, "Name");
    ...
    var dismissButton = new Button { Text = "Dismiss" };
    dismissButton.Clicked += OnDismissButtonClicked;

    Thickness padding;
    switch (Device.RuntimePlatform)
    {
        case Device.iOS:
            padding = new Thickness(0, 40, 0, 0);
            break;
        default:
            padding = new Thickness();
            break;
    }

    Padding = padding;
    Content = new StackLayout {
      HorizontalOptions = LayoutOptions.Center,
      VerticalOptions = LayoutOptions.Center,
      Children = {
        new StackLayout {
          Orientation = StackOrientation.Horizontal,
          Children = {
            new Label{ Text = "Name:", FontSize = Device.GetNamedSize (NamedSize.Medium, typeof(Label)), HorizontalOptions = LayoutOptions.FillAndExpand },
            nameLabel
          }
        },
        ...
        dismissButton
      }
    };
  }

  async void OnDismissButtonClicked (object sender, EventArgs args)
  {
    await Navigation.PopModalAsync ();
  }
}
```

Os dados são exibidos na página [`Label`](xref:Xamarin.Forms.Label) por uma série de controles.

Para obter mais informações sobre vinculação de dados, veja [Noções básicas de vinculação de dados](~/xamarin-forms/xaml/xaml-basics/index.md).

## <a name="summary"></a>Resumo

Este artigo demonstrou como navegar até as páginas modais. Uma página modal incentiva os usuários a concluir uma tarefa independente da qual não se pode sair via navegação até que essa tarefa seja concluída ou cancelada.

## <a name="related-links"></a>Links relacionados

- [Navegação de página](https://developer.xamarin.com/r/xamarin-forms/book/chapter24.pdf)
- [Modal (amostra)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-modal)
- [PassingData (amostra)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-passingdata)
