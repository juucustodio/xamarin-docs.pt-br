---
title: Xamarin.FormsPáginas modais
description: Xamarin.Formsfornece suporte para páginas modais. Uma página modal incentiva os usuários a concluir uma tarefa independente da qual não se pode sair via navegação até que essa tarefa seja concluída ou cancelada. Este artigo demonstra como navegar até as páginas modais.
ms.prod: xamarin
ms.assetid: 486CB7FD-2B9A-4DE3-94BD-C8D904E5D3C6
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/01/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: aecab26efaed9815ec6916877b5f42297821582c
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2020
ms.locfileid: "84569602"
---
# <a name="xamarinforms-modal-pages"></a>Xamarin.FormsPáginas modais

[![Baixar exemplo ](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-modal)

_O Xamarin. Forms fornece suporte para páginas modais. Uma página modal incentiva os usuários a concluir uma tarefa independente que não pode ser navegada para longe até que a tarefa seja concluída ou cancelada. Este artigo demonstra como navegar até páginas modais._

Este artigo discute os seguintes tópicos:

- [Executar a navegação](#performing-navigation) – enviar páginas por push para a pilha modal, remover páginas da pilha modal, desabilitar o botão Voltar e animar transições de página.
- [Passar dados ao navegar](#passing-data-when-navigating) – passar dados por meio de um construtor de página e de um `BindingContext`.

## <a name="overview"></a>Visão geral

Uma página modal pode ser qualquer um dos tipos de [página](~/xamarin-forms/user-interface/controls/pages.md) com suporte no Xamarin.Forms . Para exibir uma página modal, o aplicativo a enviará por push para a pilha modal, na qual ela se tornará a página ativa, conforme mostrado no diagrama a seguir:

![](modal-images/pushing.png "Pushing a Page to the Modal Stack")

Para retornar à página anterior, o aplicativo removerá a página atual da pilha modal e, em seguida, a nova página de nível superior se tornará a página ativa, conforme mostrado no diagrama a seguir:

![](modal-images/popping.png "Popping a Page from the Modal Stack")

## <a name="performing-navigation"></a>Executando a navegação

Os métodos de navegação modais são expostos pela [`Navigation`](xref:Xamarin.Forms.NavigableElement.Navigation) propriedade em qualquer [`Page`](xref:Xamarin.Forms.Page) tipo derivado. Esses métodos possibilitam [enviar páginas modais por push](#pushing-pages-to-the-modal-stack) para a pilha modal e [remover páginas modais](#popping-pages-from-the-modal-stack) da pilha modal.

A [`Navigation`](xref:Xamarin.Forms.NavigableElement.Navigation) propriedade também expõe uma [`ModalStack`](xref:Xamarin.Forms.INavigation.ModalStack) propriedade da qual as páginas modais na pilha modal podem ser obtidas. No entanto, não há nenhum conceito de realização de manipulação de pilha modal ou remoção do item mais recente da pilha até a página raiz na navegação modal. Isso ocorre porque não há suporte universal para essas operações pelas plataformas subjacentes.

> [!NOTE]
>  Uma instância de [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) não é necessária para executar a navegação de página modal.

### <a name="pushing-pages-to-the-modal-stack"></a>Enviando páginas por push para a pilha modal

Para navegar até o `ModalPage` , é necessário invocar o [`PushModalAsync`](xref:Xamarin.Forms.INavigation.PushModalAsync*) método na [`Navigation`](xref:Xamarin.Forms.NavigableElement.Navigation) propriedade da página atual, conforme demonstrado no exemplo de código a seguir:

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

Isso faz com `ModalPage` que a instância seja enviada para a pilha modal, na qual ela se torna a página ativa, desde que um item tenha sido selecionado no [`ListView`](xref:Xamarin.Forms.ListView) na `MainPage` instância. A instância de `ModalPage` é mostrada nas capturas de tela a seguir:

![](modal-images/modalpage.png "Modal Page Example")

Quando [`PushModalAsync`](xref:Xamarin.Forms.INavigation.PushModalAsync*) é invocado, ocorrem os seguintes eventos:

- A chamada de página `PushModalAsync` tem sua [`OnDisappearing`](xref:Xamarin.Forms.Page.OnDisappearing) substituição invocada, desde que a plataforma subjacente não seja Android.
- A página que está sendo navegada tem sua [`OnAppearing`](xref:Xamarin.Forms.Page.OnAppearing) substituição invocada.
- A tarefa `PushAsync` é concluída.

No entanto, a ordem exata em que esses eventos ocorrem depende da plataforma. Para obter mais informações, consulte o [capítulo 24](https://developer.xamarin.com/r/xamarin-forms/book/chapter24.pdf) do livro de Charles Petzold Xamarin.Forms .

> [!NOTE]
> Chamadas para as [`OnDisappearing`](xref:Xamarin.Forms.Page.OnDisappearing) [`OnAppearing`](xref:Xamarin.Forms.Page.OnAppearing) substituições e não podem ser tratadas como indicações Garantidas da navegação de página. Por exemplo, no iOS, a substituição `OnDisappearing` é chamada na página ativa quando o aplicativo é encerrado.

### <a name="popping-pages-from-the-modal-stack"></a>Removendo páginas da pilha modal

A página ativa pode ser removida como o item mais recente da pilha modal pressionando o botão *Voltar* no dispositivo, independentemente de este ser um botão físico no dispositivo ou um botão na tela.

Para retornar programaticamente à página original, a `ModalPage` instância deve invocar o [`PopModalAsync`](xref:Xamarin.Forms.INavigation.PopModalAsync) método, conforme demonstrado no exemplo de código a seguir:

```csharp
async void OnDismissButtonClicked (object sender, EventArgs args)
{
  await Navigation.PopModalAsync ();
}
```

Isso faz com que a instância `ModalPage` seja removida da pilha modal, com a nova página de nível superior tornando-se a página ativa. Quando [`PopModalAsync`](xref:Xamarin.Forms.INavigation.PopModalAsync) é invocado, ocorrem os seguintes eventos:

- A chamada de página `PopModalAsync` tem sua [`OnDisappearing`](xref:Xamarin.Forms.Page.OnDisappearing) substituição invocada.
- A página que está sendo retornada tem sua [`OnAppearing`](xref:Xamarin.Forms.Page.OnAppearing) substituição invocada, desde que a plataforma subjacente não seja Android.
- A tarefa `PopModalAsync` é retornada.

No entanto, a ordem exata em que esses eventos ocorrem depende da plataforma. Para obter mais informações, consulte o [capítulo 24](https://developer.xamarin.com/r/xamarin-forms/book/chapter24.pdf) do livro de Charles Petzold Xamarin.Forms .

### <a name="disabling-the-back-button"></a>Desabilitando o botão Voltar

No Android, o usuário sempre pode retornar à página anterior pressionando o botão *Voltar* padrão no dispositivo. Se a página modal exigir que o usuário conclua uma tarefa independente antes de sair da página, o aplicativo deverá desabilitar o botão *Voltar*. Isso pode ser feito substituindo o [`Page.OnBackButtonPressed`](xref:Xamarin.Forms.Page.OnBackButtonPressed) método na página modal. Para obter mais informações, consulte o [capítulo 24](https://developer.xamarin.com/r/xamarin-forms/book/chapter24.pdf) do livro de Charles Petzold Xamarin.Forms .

### <a name="animating-page-transitions"></a>Animando transições de página

A [`Navigation`](xref:Xamarin.Forms.NavigableElement.Navigation) propriedade de cada página também fornece métodos de push e pop substituídos que incluem um `boolean` parâmetro que controla se uma animação de página deve ser exibida durante a navegação, como mostrado no exemplo de código a seguir:

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

## <a name="passing-data-when-navigating"></a>Transmitindo dados ao navegar

Às vezes, é necessário que uma página transmita dados para outra página durante a navegação. Duas técnicas para realizar isso são passando dados por meio de um construtor de página e definindo as novas páginas [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) para os dados. Descreveremos cada técnica separadamente.

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

Os dados são exibidos na página definindo a [`Label.Text`](xref:Xamarin.Forms.Label.Text) propriedade.

### <a name="passing-data-through-a-bindingcontext"></a>Transmitindo dados por meio de um BindingContext

Uma abordagem alternativa para passar dados para outra página durante a navegação é definir as novas páginas [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) para os dados, conforme mostrado no exemplo de código a seguir:

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

Esse código define o [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) da `DetailPage` instância para a `Contact` instância do e, em seguida, navega para o `DetailPage` .

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

Os dados são exibidos na página por uma série de [`Label`](xref:Xamarin.Forms.Label) controles.

Para obter mais informações sobre vinculação de dados, veja [Noções básicas de vinculação de dados](~/xamarin-forms/xaml/xaml-basics/index.md).

## <a name="summary"></a>Resumo

Este artigo demonstrou como navegar até as páginas modais. Uma página modal incentiva os usuários a concluir uma tarefa independente da qual não se pode sair via navegação até que essa tarefa seja concluída ou cancelada.

## <a name="related-links"></a>Links relacionados

- [Navegação de página](https://developer.xamarin.com/r/xamarin-forms/book/chapter24.pdf)
- [Modal (amostra)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-modal)
- [PassingData (amostra)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-passingdata)
