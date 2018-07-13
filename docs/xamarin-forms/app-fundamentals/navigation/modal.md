---
title: Páginas do xamarin. Forms Modal
description: O Xamarin.Forms dá suporte a páginas modais. Uma página modal incentiva os usuários a concluir uma tarefa independente da qual não se pode sair via navegação até que essa tarefa seja concluída ou cancelada. Este artigo demonstra como navegar até as páginas modais.
ms.prod: xamarin
ms.assetid: 486CB7FD-2B9A-4DE3-94BD-C8D904E5D3C6
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/01/2017
ms.openlocfilehash: 44aee8500c7de2ae56b59049368d6025ec49cc5e
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/12/2018
ms.locfileid: "38994806"
---
# <a name="xamarinforms-modal-pages"></a>Páginas do xamarin. Forms Modal

_Xamarin. Forms fornece suporte para páginas modais. Uma página modal incentiva os usuários a concluir uma tarefa independente da qual não se pode sair via navegação até que essa tarefa seja concluída ou cancelada. Este artigo demonstra como navegar até as páginas modais._

Este artigo discute os seguintes tópicos:

- [Executar a navegação](#Performing_Navigation) – push páginas para a pilha modal, páginas de retirada da pilha modal, desabilitando o botão Voltar e animar as transições de página.
- [Passagem de dados quando estiver navegando](#Passing_Data_when_Navigating) – passagem de dados por meio de um construtor de página e um `BindingContext`.

## <a name="overview"></a>Visão geral

Uma página restrita pode ser qualquer um dos [página](~/xamarin-forms/user-interface/controls/pages.md) tipos com suporte no xamarin. Forms. Para exibir uma página modal o aplicativo enviará por push para a pilha modal, na qual ele se tornará a página ativa, conforme mostrado no diagrama a seguir:

![](modal-images/pushing.png "Enviar por push uma página para a pilha Modal")

Para retornar à página anterior do aplicativo será exibida a página atual da pilha modal e a nova página de nível mais alta torna-se a página ativa, conforme mostrado no diagrama a seguir:

![](modal-images/popping.png "Exibindo uma página da pilha Modal")

<a name="Performing_Navigation" />

## <a name="performing-navigation"></a>Executar a navegação

Os métodos de navegação modal são expostos pela propriedade [`Navigation`](xref:Xamarin.Forms.VisualElement.Navigation) em quaisquer tipos derivados de [`Page`](xref:Xamarin.Forms.Page). Esses métodos fornecem a capacidade [push páginas modais](#Pushing_Pages_to_the_Modal_Stack) para a pilha modal, e [pop páginas modais](#Popping_Pages_from_the_Modal_Stack) da pilha modal.

O [ `Navigation` ](xref:Xamarin.Forms.VisualElement.Navigation) também expõe uma [ `ModalStack` ](xref:Xamarin.Forms.INavigation.ModalStack) propriedade da qual as páginas modais na pilha modal podem ser obtidas. No entanto, não há nenhum conceito de realização de manipulação de pilha modal ou remoção do item mais recente da pilha até a página raiz na navegação modal. Isso ocorre porque não há suporte universal para essas operações pelas plataformas subjacentes.

> [!NOTE]
> Uma instância de [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) não é necessária para executar a navegação de página modal.

<a name="Pushing_Pages_to_the_Modal_Stack" />

### <a name="pushing-pages-to-the-modal-stack"></a>Enviar por push páginas para a pilha Modal

Para navegar até a `ModalPage` é necessário invocar o [ `PushModalAsync` ](xref:Xamarin.Forms.INavigation.PushModalAsync*) método no [ `Navigation` ](xref:Xamarin.Forms.VisualElement.Navigation) propriedade da página atual, conforme demonstrado no exemplo de código a seguir:

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

Isso faz com que o `ModalPage` instância a ser enviado para a pilha modal, onde se torna a página ativa, fornecida que um item foi selecionado na [ `ListView` ](xref:Xamarin.Forms.ListView) no `MainPage` instância. O `ModalPage` instância é mostrada nas capturas de tela seguir:

![](modal-images/modalpage.png "Exemplo de página modal")

Quando [ `PushModalAsync` ](xref:Xamarin.Forms.INavigation.PushModalAsync*) é invocado, os seguintes eventos ocorrem:

- A chamada de página `PushModalAsync` tem sua [ `OnDisappearing` ](xref:Xamarin.Forms.Page.OnDisappearing) substituição invocada, desde que a plataforma subjacente não é o Android.
- A navegação de página tem seu [ `OnAppearing` ](xref:Xamarin.Forms.Page.OnAppearing) substituição invocada.
- O `PushAsync` tarefa é concluída.

No entanto, a ordem exata que esses eventos ocorrem é dependente da plataforma. Para obter mais informações, consulte [capítulo 24](https://developer.xamarin.com/r/xamarin-forms/book/chapter24.pdf) de xamarin. Forms livro Charles Petzold.

> [!NOTE]
> Chamadas para o [ `OnDisappearing` ](xref:Xamarin.Forms.Page.OnDisappearing) e [ `OnAppearing` ](xref:Xamarin.Forms.Page.OnAppearing) substituições não podem ser tratadas como garantidas indicações de navegação de página. Por exemplo, no iOS, o `OnDisappearing` substituição é chamada na página ativa quando o aplicativo é encerrado.

<a name="Popping_Pages_from_the_Modal_Stack" />

### <a name="popping-pages-from-the-modal-stack"></a>Páginas de retirada da pilha Modal

A página ativa pode ser removida da pilha modal pressionando a *volta* botão no dispositivo, independentemente de se este é um botão físico no dispositivo ou um botão na tela.

Para retornar programaticamente à página original, a instância `ModalPage` deve invocar o método [`PopModalAsync`](xref:Xamarin.Forms.INavigation.PopModalAsync), conforme demonstrado no exemplo de código a seguir:

```csharp
async void OnDismissButtonClicked (object sender, EventArgs args)
{
  await Navigation.PopModalAsync ();
}
```

Isso faz com que o `ModalPage` instância a ser removido da pilha modal, com a nova página de nível mais alta, tornando-se a página ativa. Quando [ `PopModalAsync` ](xref:Xamarin.Forms.INavigation.PopModalAsync) é invocado, os seguintes eventos ocorrem:

- A chamada de página `PopModalAsync` tem sua [ `OnDisappearing` ](xref:Xamarin.Forms.Page.OnDisappearing) substituição invocada.
- A página que está sendo retornada para tem seu [ `OnAppearing` ](xref:Xamarin.Forms.Page.OnAppearing) substituição invocada, desde que a plataforma subjacente não é o Android.
- O `PopModalAsync` retorna da tarefa.

No entanto, a ordem exata que esses eventos ocorrem é dependente da plataforma. Para obter mais informações, consulte [capítulo 24](https://developer.xamarin.com/r/xamarin-forms/book/chapter24.pdf) de xamarin. Forms livro Charles Petzold.

### <a name="disabling-the-back-button"></a>Desabilitar o botão Voltar

No Android, o usuário sempre pode retornar à página anterior pressionando o padrão *volta* botão no dispositivo. Se a página modal exige que o usuário concluir uma tarefa independente antes de sair da página, o aplicativo deve desabilitar a *volta* botão. Isso pode ser feito por meio da substituição de [ `Page.OnBackButtonPressed` ](xref:Xamarin.Forms.Page.OnBackButtonPressed) método na página modal. Para obter mais informações, consulte [capítulo 24](https://developer.xamarin.com/r/xamarin-forms/book/chapter24.pdf) de xamarin. Forms livro Charles Petzold.

### <a name="animating-page-transitions"></a>Animando as transições de página

O [ `Navigation` ](xref:Xamarin.Forms.VisualElement.Navigation) propriedade de cada página também fornece substituída de envio por push e pop-up métodos que incluem um `boolean` parâmetro que controla se deve exibir uma animação de página durante a navegação, conforme mostrado no código a seguir exemplo:

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

Definindo o `boolean` parâmetro para `false` desativa a animação de transição de página, ao mesmo tempo, definindo o parâmetro como `true` permite que a animação de transição de página, desde que ele é suportado pela plataforma subjacente. No entanto, os métodos push e pop que não têm esse parâmetro habilitam a animação por padrão.

<a name="Passing_Data_when_Navigating" />

## <a name="passing-data-when-navigating"></a>Passando dados ao navegar

Às vezes, é necessário para uma página passar dados para outra página durante a navegação. Duas técnicas para realizar isso são, passando dados por meio de um construtor de página e definindo-se a nova página [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext) aos dados. Cada uma será agora discutida detalhadamente.

### <a name="passing-data-through-a-page-constructor"></a>Passando dados através de um construtor de página

A técnica mais simples para passar dados para outra página durante a navegação é por meio de um parâmetro de construtor da página, que é mostrado no exemplo de código a seguir:

```csharp
public App ()
{
  MainPage = new MainPage (DateTime.Now.ToString ("u")));
}
```

Esse código cria um `MainPage` instância, passando a data e hora atuais no formato ISO8601.

O `MainPage` instância recebe os dados por meio de um parâmetro de construtor, conforme mostrado no exemplo de código a seguir:

```csharp
public MainPage (string date)
{
  InitializeComponent ();
  dateLabel.Text = date;
}
```

Os dados são exibidos na página, em seguida, definindo o [ `Label.Text` ](xref:Xamarin.Forms.Label.Text) propriedade.

### <a name="passing-data-through-a-bindingcontext"></a>Passando dados através de um BindingContext

Uma abordagem alternativa para passar dados para outra página durante a navegação é ao definir a nova página [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext) aos dados, conforme mostrado no exemplo de código a seguir:

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

Esse código define a [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext) da `DetailPage` da instância para o `Contact` instância e, em seguida, navega para o `DetailPage`.

O `DetailPage` , em seguida, usa a associação de dados para exibir o `Contact` instância dados, conforme mostrado no exemplo de código XAML a seguir:

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

O exemplo de código a seguir mostra como a associação de dados pode ser feita em c#:

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

Os dados são exibidos na página por uma série de [ `Label` ](xref:Xamarin.Forms.Label) controles.

Para obter mais informações sobre vinculação de dados, veja [Noções básicas de vinculação de dados](~/xamarin-forms/xaml/xaml-basics/index.md).

## <a name="summary"></a>Resumo

Este artigo demonstrou como navegar até as páginas modais. Uma página modal incentiva os usuários a concluir uma tarefa independente da qual não se pode sair via navegação até que essa tarefa seja concluída ou cancelada.


## <a name="related-links"></a>Links relacionados

- [Navegação de página](https://developer.xamarin.com/r/xamarin-forms/book/chapter24.pdf)
- [Modal (amostra)](https://developer.xamarin.com/samples/xamarin-forms/Navigation/Modal/)
- [PassingData (amostra)](https://developer.xamarin.com/samples/xamarin-forms/Navigation/PassingData/)
