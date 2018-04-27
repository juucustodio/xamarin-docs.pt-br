---
title: Páginas modais
description: O Xamarin.Forms dá suporte a páginas modais. Uma página modal incentiva os usuários a concluir uma tarefa independente da qual não se pode sair via navegação até que essa tarefa seja concluída ou cancelada. Este artigo demonstra como navegar em páginas modais.
ms.prod: xamarin
ms.assetid: 486CB7FD-2B9A-4DE3-94BD-C8D904E5D3C6
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/01/2017
ms.openlocfilehash: 00d52aa69372ab5ec3073e2355a43521b515ca8b
ms.sourcegitcommit: 1561c8022c3585655229a869d9ef3510bf83f00a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/27/2018
---
# <a name="modal-pages"></a>Páginas modais

_Xamarin. Forms fornece suporte para páginas modais. Uma página modal incentiva os usuários a concluir uma tarefa independente da qual não se pode sair via navegação até que essa tarefa seja concluída ou cancelada. Este artigo demonstra como navegar em páginas modais._

Este artigo aborda os seguintes tópicos:

- [Executar a navegação](#Performing_Navigation) – enviar páginas para a pilha modal, produzirão páginas da pilha modal, desativando o botão Voltar e animação de transições de página.
- [Transmitindo dados ao navegar](#Passing_Data_when_Navigating) – passar dados por meio de um construtor de página e um `BindingContext`.

## <a name="overview"></a>Visão geral

Uma página restrita pode ser qualquer o [página](~/xamarin-forms/user-interface/controls/pages.md) tipos compatíveis com o xamarin. Forms. Para exibir uma página restrita o aplicativo enviará por push-lo para a pilha modal, onde ele se tornará a página ativa, conforme mostrado no diagrama a seguir:

![](modal-images/pushing.png "Enviar uma página para a pilha de janela restrita")

Para retornar à página anterior do aplicativo será exibida a página atual da pilha modal e a nova página de nível superior se torna a página ativa, conforme mostrado no diagrama a seguir:

![](modal-images/popping.png "Exibir uma página da pilha de janela restrita")

<a name="Performing_Navigation" />

## <a name="performing-navigation"></a>Executar a navegação

Os métodos de navegação modal são expostos pela propriedade [`Navigation`](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Navigation/) em quaisquer tipos derivados de [`Page`](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/). Esses métodos fornecem a capacidade de [push páginas modais](#Pushing_Pages_to_the_Modal_Stack) na pilha modal, e [pop páginas modais](#Popping_Pages_from_the_Modal_Stack) da pilha modal.

O [ `Navigation` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Navigation/) propriedade também expõe um [ `ModalStack` ](https://developer.xamarin.com/api/property/Xamarin.Forms.INavigation.ModalStack/) propriedade na qual as páginas modais na pilha de modal podem ser obtidas. No entanto, não há nenhum conceito de realização de manipulação de pilha modal ou remoção do item mais recente da pilha até a página raiz na navegação modal. Isso ocorre porque não há suporte universal para essas operações pelas plataformas subjacentes.

> [!NOTE]
> Uma instância de [`NavigationPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/) não é necessária para executar a navegação de página modal.

<a name="Pushing_Pages_to_the_Modal_Stack" />

### <a name="pushing-pages-to-the-modal-stack"></a>Páginas de envio para a pilha de janela restrita

Para navegar até o `ModalPage` é necessário chamar o [ `PushModalAsync` ](https://developer.xamarin.com/api/member/Xamarin.Forms.INavigation.PushModalAsync(Xamarin.Forms.Page)/) método o [ `Navigation` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Navigation/) propriedade da página atual, como demonstrado no exemplo de código a seguir:

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

Isso faz com que o `ModalPage` instância a ser inserido na pilha modal, onde se torna a página ativa, fornecida que um item foi selecionado no [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) no `MainPage` instância. O `ModalPage` instância é mostrada nas capturas de tela seguir:

![](modal-images/modalpage.png "Exemplo de página modal")

Quando [ `PushModalAsync` ](https://developer.xamarin.com/api/member/Xamarin.Forms.INavigation.PushModalAsync(Xamarin.Forms.Page)/) é invocado, os seguintes eventos ocorrem:

- A chamada de página `PushModalAsync` tem seu [ `OnDisappearing` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.OnDisappearing/) substituição invocada, desde que a plataforma subjacente não Android.
- A página que está sendo direcionada tem seu [ `OnAppearing` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.OnAppearing/) substituição invocada.
- O `PushAsync` tarefa é concluída.

No entanto, a ordem exata que esses eventos ocorrem é dependente de plataforma. Para obter mais informações, consulte [capítulo 24](https://developer.xamarin.com/r/xamarin-forms/book/chapter24.pdf) do catálogo do Charles Petzold xamarin. Forms.

> [!NOTE]
> Chamadas para o [ `OnDisappearing` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.OnDisappearing/) e [ `OnAppearing` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.OnAppearing/) substituições não podem ser tratadas como garantidas indicações de navegação de página. Por exemplo, no iOS, o `OnDisappearing` substituição é chamada na página ativa quando o aplicativo termina.

<a name="Popping_Pages_from_the_Modal_Stack" />

### <a name="popping-pages-from-the-modal-stack"></a>Páginas produzirão da pilha de janela restrita

A página ativa pode ser exibida da pilha modal pressionando o *novamente* botão no dispositivo, independentemente de se este é um botão físico no dispositivo ou um botão na tela.

Para retornar programaticamente à página original, a instância `ModalPage` deve invocar o método [`PopModalAsync`](https://developer.xamarin.com/api/member/Xamarin.Forms.INavigation.PopModalAsync()/), conforme demonstrado no exemplo de código a seguir:

```csharp
async void OnDismissButtonClicked (object sender, EventArgs args)
{
  await Navigation.PopModalAsync ();
}
```

Isso faz com que o `ModalPage` instância a ser removido da pilha modal, com a nova página de nível superior se torna a página ativa. Quando [ `PopModalAsync` ](https://developer.xamarin.com/api/member/Xamarin.Forms.INavigation.PopModalAsync()/) é invocado, os seguintes eventos ocorrem:

- A chamada de página `PopModalAsync` tem seu [ `OnDisappearing` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.OnDisappearing/) substituição invocada.
- A página que está sendo retornada ao tem seu [ `OnAppearing` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.OnAppearing/) substituição invocada, desde que a plataforma subjacente não Android.
- O `PopModalAsync` retorna da tarefa.

No entanto, a ordem exata que esses eventos ocorrem é dependente de plataforma. Para obter mais informações, consulte [capítulo 24](https://developer.xamarin.com/r/xamarin-forms/book/chapter24.pdf) do catálogo do Charles Petzold xamarin. Forms.

### <a name="disabling-the-back-button"></a>Desabilitar o botão Voltar

No Android, o usuário pode sempre retornar à página anterior pressionando padrão *novamente* botão no dispositivo. Se a página modal exige que o usuário concluir uma tarefa autossuficiente antes de sair da página, o aplicativo deve desabilitar o *novamente* botão. Isso pode ser feito por meio da substituição de [ `Page.OnBackButtonPressed` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.OnBackButtonPressed/) método na página modal. Para obter mais informações, consulte [capítulo 24](https://developer.xamarin.com/r/xamarin-forms/book/chapter24.pdf) do catálogo do Charles Petzold xamarin. Forms.

### <a name="animating-page-transitions"></a>Animação de transições de página

O [ `Navigation` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Navigation/) propriedade de cada página também fornece envio substituído e os métodos pop que incluem um `boolean` parâmetro que controla se deseja exibir uma animação de página durante a navegação, conforme mostrado no código a seguir exemplo:

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

Definindo o `boolean` parâmetro `false` desativa a animação de transição de página, ao definir o parâmetro `true` permite que a animação de transição de página, desde que ele é suportado pela plataforma subjacente. No entanto, os métodos de envio por push e pop que não têm esse parâmetro habilitam a animação por padrão.

<a name="Passing_Data_when_Navigating" />

## <a name="passing-data-when-navigating"></a>Passando dados ao navegar

Às vezes, é necessário para uma página passar dados para outra página durante a navegação. São de duas técnicas para realizar isso, passando dados por meio de um construtor de página e definindo a nova página [ `BindingContext` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BindableObject.BindingContext/) aos dados. Cada uma será agora ser discutida.

### <a name="passing-data-through-a-page-constructor"></a>Passando dados através de um construtor de página

A técnica mais simples para transferir dados para outra página durante a navegação é por meio de um parâmetro de construtor da página, que é mostrado no exemplo de código a seguir:

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

Os dados são exibidos na página definindo o [ `Label.Text` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Label.Text/) propriedade.

### <a name="passing-data-through-a-bindingcontext"></a>Passando dados através de um BindingContext

Uma abordagem alternativa para passar dados para outra página durante a navegação é definindo a nova página [ `BindingContext` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BindableObject.BindingContext/) aos dados, conforme mostrado no exemplo de código a seguir:

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

Esse código define o [ `BindingContext` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BindableObject.BindingContext/) do `DetailPage` de instância para o `Contact` instância e, em seguida, navega para o `DetailPage`.

O `DetailPage` usa associação de dados para exibir o `Contact` instância dados, conforme mostrado no exemplo de código XAML a seguir:

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

O exemplo de código a seguir mostra como a associação de dados pode ser realizada em c#:

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

Os dados são exibidos na página por uma série de [ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) controles.

Para obter mais informações sobre vinculação de dados, veja [Noções básicas de vinculação de dados](~/xamarin-forms/xaml/xaml-basics/index.md).

## <a name="summary"></a>Resumo

Este artigo demonstrou como navegar em páginas modais. Uma página modal incentiva os usuários a concluir uma tarefa independente da qual não se pode sair via navegação até que essa tarefa seja concluída ou cancelada.


## <a name="related-links"></a>Links relacionados

- [Navegação de página](https://developer.xamarin.com/r/xamarin-forms/book/chapter24.pdf)
- [Modal (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/Navigation/Modal/)
- [PassingData (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/Navigation/PassingData/)
