---
title: Navegação hierárquica
description: Este artigo demonstra como usar a classe NavigationPage para executar a navegação em uma pilha de último a entrar, primeiro a sair (UEPS) páginas.
ms.prod: xamarin
ms.assetid: C8A5EEFF-5A3B-4163-838A-147EE3939FAA
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/10/2017
ms.openlocfilehash: f8f8f9b4e5755e8b1707178fef633321b64e4e94
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/12/2018
ms.locfileid: "38994670"
---
# <a name="hierarchical-navigation"></a>Navegação hierárquica

_A classe NavigationPage fornece uma experiência de Navegação hierárquica em que o usuário é capaz de navegar pelas páginas de frente e para trás, conforme desejado. A classe implementa navegação como uma pilha último a entrar, primeiro a sair (UEPS) dos objetos de página. Este artigo demonstra como usar a classe NavigationPage para executar a navegação em uma pilha de páginas._

Este artigo discute os seguintes tópicos:

- [Executar a navegação](#Performing_Navigation) – criando a página de raiz, enviando páginas para a pilha de navegação, exibindo páginas da pilha de navegação e animação de transições de página.
- [Passagem de dados quando estiver navegando](#Passing_Data_when_Navigating) – passagem de dados por meio de um construtor de página e um `BindingContext`.
- [Manipulando a pilha de navegação](#Manipulating_the_Navigation_Stack) – manipulando a pilha através da inserção ou remoção de páginas.

## <a name="overview"></a>Visão geral

Para mover de uma página para outra, um aplicativo enviará por push uma nova página para a pilha de navegação, em que ela se tornará a página ativa, conforme mostrado no diagrama a seguir:

![](hierarchical-images/pushing.png "Enviar uma página para a pilha de navegação")

Para retornar à página anterior, o aplicativo será exibida a página atual da pilha de navegação e a nova página de nível mais alta torna-se a página ativa, conforme mostrado no diagrama a seguir:

![](hierarchical-images/popping.png "Exibindo uma página da pilha de navegação")

Métodos de navegação são expostos pela [ `Navigation` ](xref:Xamarin.Forms.VisualElement.Navigation) propriedade em qualquer [ `Page` ](xref:Xamarin.Forms.Page) tipos derivados. Esses métodos fornecem a capacidade de enviar por push páginas para a pilha de navegação, a páginas de pop-up da pilha de navegação e para executar a manipulação de pilha.

<a name="Performing_Navigation" />

## <a name="performing-navigation"></a>Executar a navegação

Na barra de navegação hierárquica, a classe [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) é usada para navegar por meio de uma pilha de objetos [`ContentPage`](xref:Xamarin.Forms.ContentPage). As capturas de tela a seguir mostram os principais componentes do `NavigationPage` em cada plataforma:

![](hierarchical-images/navigationpage-components.png "Componentes de NavigationPage")

O layout de um [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage) depende da plataforma:

- No iOS, uma barra de navegação está presente na parte superior da página que exibe um título e que tem um *volta* botão retorna à página anterior.
- No Android, uma barra de navegação está presente na parte superior da página que exibe um título, um ícone e uma *volta* botão retorna à página anterior. O ícone é definido na `[Activity]` atributo decora o `MainActivity` classe no projeto específico da plataforma Android.
- Na plataforma Universal do Windows, uma barra de navegação está presente na parte superior da página que exibe um título.

Em todas as plataformas, o valor de [ `Page.Title` ](xref:Xamarin.Forms.Page.Title) propriedade será exibida como o título da página.

> [!NOTE]
> É recomendável que um `NavigationPage` deve ser preenchido com `ContentPage` somente instâncias.

### <a name="creating-the-root-page"></a>Criando a página de raiz

A primeira página adicionada a uma pilha de navegação é conhecida como a página *raiz* do aplicativo e o exemplo de código a seguir mostra como isso é realizado:

```csharp
public App ()
{
  MainPage = new NavigationPage (new Page1Xaml ());
}
```

Isso faz com que o `Page1Xaml` [ `ContentPage` ](xref:Xamarin.Forms.ContentPage) instância a ser enviado para a pilha de navegação, em que ele se torna a página ativa e a página de raiz do aplicativo. Isso é mostrado nas capturas de tela seguir:

![](hierarchical-images/mainpage.png "Página de raiz da pilha de navegação")

> [!NOTE]
> O [ `RootPage` ](xref:Xamarin.Forms.NavigationPage.RootPage) propriedade de uma [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage) instância fornece acesso para a primeira página na pilha de navegação.

### <a name="pushing-pages-to-the-navigation-stack"></a>Enviando páginas para a pilha de navegação

Para navegar até `Page2Xaml`, é necessário invocar o [ `PushAsync` ](xref:Xamarin.Forms.NavigationPage.PushAsync*) método sobre o [ `Navigation` ](xref:Xamarin.Forms.VisualElement.Navigation) propriedade da página atual, conforme demonstrado no exemplo de código a seguir:

```csharp
async void OnNextPageButtonClicked (object sender, EventArgs e)
{
  await Navigation.PushAsync (new Page2Xaml ());
}
```

Isso faz com que a instância `Page2Xaml` seja enviada por push para a pilha de navegação, em que ele se torna a página ativa. Isso é mostrado nas capturas de tela seguir:

![](hierarchical-images/secondpage.png "Página enviada para a pilha de navegação")

Quando o [ `PushAsync` ](xref:Xamarin.Forms.NavigationPage.PushAsync*) método é invocado, os seguintes eventos ocorrem:

- A chamada de página `PushAsync` tem sua [ `OnDisappearing` ](xref:Xamarin.Forms.Page.OnDisappearing) substituição invocada.
- A navegação de página tem seu [ `OnAppearing` ](xref:Xamarin.Forms.Page.OnAppearing) substituição invocada.
- O `PushAsync` tarefa é concluída.

No entanto, a ordem exata em que esses eventos ocorrem é dependente da plataforma. Para obter mais informações, consulte [capítulo 24](https://developer.xamarin.com/r/xamarin-forms/book/chapter24.pdf) de xamarin. Forms livro Charles Petzold.

> [!NOTE]
> Chamadas para o [ `OnDisappearing` ](xref:Xamarin.Forms.Page.OnDisappearing) e [ `OnAppearing` ](xref:Xamarin.Forms.Page.OnAppearing) substituições não podem ser tratadas como garantidas indicações de navegação de página. Por exemplo, no iOS, o `OnDisappearing` substituição é chamada na página ativa quando o aplicativo é encerrado.

### <a name="popping-pages-from-the-navigation-stack"></a>Páginas de retirada da pilha de navegação

A página ativa pode ser removida como o item mais recente da pilha de navegação pressionando o botão *Voltar* no dispositivo, independentemente de este ser um botão físico no dispositivo ou um botão na tela.

Para retornar programaticamente à página original, a instância `Page2Xaml` deve invocar o método [`PopAsync`](xref:Xamarin.Forms.NavigationPage.PopAsync), conforme demonstrado no exemplo de código a seguir:

```csharp
async void OnPreviousPageButtonClicked (object sender, EventArgs e)
{
  await Navigation.PopAsync ();
}
```

Isso faz com que a instância `Page2Xaml` seja removida da pilha de navegação, com a nova página de nível superior tornando-se a página ativa. Quando o [ `PopAsync` ](xref:Xamarin.Forms.NavigationPage.PopAsync) método é invocado, os seguintes eventos ocorrem:

- A chamada de página `PopAsync` tem sua [ `OnDisappearing` ](xref:Xamarin.Forms.Page.OnDisappearing) substituição invocada.
- A página que está sendo retornada para tem seu [ `OnAppearing` ](xref:Xamarin.Forms.Page.OnAppearing) substituição invocada.
- O `PopAsync` retorna da tarefa.

No entanto, a ordem exata em que esses eventos ocorrem é dependente da plataforma. Para obter mais informações, consulte [capítulo 24](https://developer.xamarin.com/r/xamarin-forms/book/chapter24.pdf) de xamarin. Forms livro Charles Petzold.

Assim como [ `PushAsync` ](xref:Xamarin.Forms.NavigationPage.PushAsync*) e [ `PopAsync` ](xref:Xamarin.Forms.NavigationPage.PopAsync) métodos, o [ `Navigation` ](xref:Xamarin.Forms.VisualElement.Navigation) também fornece uma propriedade de cada página uma [ `PopToRootAsync` ](xref:Xamarin.Forms.NavigationPage.PopToRootAsync) método, que é mostrado no exemplo de código a seguir:

```csharp
async void OnRootPageButtonClicked (object sender, EventArgs e)
{
  await Navigation.PopToRootAsync ();
}
```

Esse método retira todos, exceto a raiz [ `Page` ](xref:Xamarin.Forms.Page) da pilha de navegação, portanto, fazendo a página raiz do aplicativo a página ativa.

### <a name="animating-page-transitions"></a>Animando as transições de página

O [ `Navigation` ](xref:Xamarin.Forms.VisualElement.Navigation) propriedade de cada página também fornece substituída de envio por push e pop-up métodos que incluem um `boolean` parâmetro que controla se deve exibir uma animação de página durante a navegação, conforme mostrado no código a seguir exemplo:

```csharp
async void OnNextPageButtonClicked (object sender, EventArgs e)
{
  // Page appearance not animated
  await Navigation.PushAsync (new Page2Xaml (), false);
}

async void OnPreviousPageButtonClicked (object sender, EventArgs e)
{
  // Page appearance not animated
  await Navigation.PopAsync (false);
}

async void OnRootPageButtonClicked (object sender, EventArgs e)
{
  // Page appearance not animated
  await Navigation.PopToRootAsync (false);
}
```

Definindo o `boolean` parâmetro para `false` desativa a animação de transição de página, ao mesmo tempo, definindo o parâmetro como `true` permite que a animação de transição de página, desde que ele é suportado pela plataforma subjacente. No entanto, os métodos push e pop que não têm esse parâmetro habilitam a animação por padrão.

<a name="Passing_Data_when_Navigating" />

## <a name="passing-data-when-navigating"></a>Passando dados ao navegar

Às vezes, é necessário para uma página passar dados para outra página durante a navegação. Duas técnicas para realizar isso são passando dados através de um construtor de página e definindo a nova página [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext) aos dados. Cada uma será agora discutida detalhadamente.

### <a name="passing-data-through-a-page-constructor"></a>Passando dados através de um construtor de página

A técnica mais simples para passar dados para outra página durante a navegação é por meio de um parâmetro de construtor da página, que é mostrado no exemplo de código a seguir:

```csharp
public App ()
{
  MainPage = new NavigationPage (new MainPage (DateTime.Now.ToString ("u")));
}
```

Esse código cria uma `MainPage` da instância, passando a data e hora atuais no formato ISO8601, que é encapsulado em um [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage) instância.

O `MainPage` instância recebe os dados por meio de um parâmetro de construtor, conforme mostrado no exemplo de código a seguir:

```csharp
public MainPage (string date)
{
  InitializeComponent ();
  dateLabel.Text = date;
}
```

Os dados são exibidos na página, em seguida, definindo o [ `Label.Text` ](xref:Xamarin.Forms.Label.Text) propriedade, conforme mostrado nas capturas de tela seguir:

![](hierarchical-images/passing-data-constructor.png "Dados passados por meio de um construtor de página")

### <a name="passing-data-through-a-bindingcontext"></a>Passando dados através de um BindingContext

Uma abordagem alternativa para passar dados para outra página durante a navegação é ao definir a nova página [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext) aos dados, conforme mostrado no exemplo de código a seguir:

```csharp
async void OnNavigateButtonClicked (object sender, EventArgs e)
{
  var contact = new Contact {
    Name = "Jane Doe",
    Age = 30,
    Occupation = "Developer",
    Country = "USA"
  };

  var secondPage = new SecondPage ();
  secondPage.BindingContext = contact;
  await Navigation.PushAsync (secondPage);
}
```

Esse código define a [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext) da `SecondPage` da instância para o `Contact` instância e, em seguida, navega para o `SecondPage`.

O `SecondPage` , em seguida, usa a associação de dados para exibir o `Contact` instância dados, conforme mostrado no exemplo de código XAML a seguir:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="PassingData.SecondPage"
             Title="Second Page">
    <ContentPage.Content>
        <StackLayout HorizontalOptions="Center" VerticalOptions="Center">
            <StackLayout Orientation="Horizontal">
                <Label Text="Name:" HorizontalOptions="FillAndExpand" />
                <Label Text="{Binding Name}" FontSize="Medium" FontAttributes="Bold" />
            </StackLayout>
            ...
            <Button x:Name="navigateButton" Text="Previous Page" Clicked="OnNavigateButtonClicked" />
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

O exemplo de código a seguir mostra como a associação de dados pode ser feita em c#:

```csharp
public class SecondPageCS : ContentPage
{
  public SecondPageCS ()
  {
    var nameLabel = new Label {
      FontSize = Device.GetNamedSize (NamedSize.Medium, typeof(Label)),
      FontAttributes = FontAttributes.Bold
    };
    nameLabel.SetBinding (Label.TextProperty, "Name");
    ...
    var navigateButton = new Button { Text = "Previous Page" };
    navigateButton.Clicked += OnNavigateButtonClicked;

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
        navigateButton
      }
    };
  }

  async void OnNavigateButtonClicked (object sender, EventArgs e)
  {
    await Navigation.PopAsync ();
  }
}
```

Os dados são exibidos na página por uma série de [ `Label` ](xref:Xamarin.Forms.Label) controles, conforme mostrado nas capturas de tela seguir:

![](hierarchical-images/passing-data-bindingcontext.png "Dados passados por meio de um BindingContext")

Para obter mais informações sobre vinculação de dados, veja [Noções básicas de vinculação de dados](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md).

<a name="Manipulating_the_Navigation_Stack" />

## <a name="manipulating-the-navigation-stack"></a>Manipulando a pilha de navegação

O [ `Navigation` ](xref:Xamarin.Forms.VisualElement.Navigation) propriedade expõe uma [ `NavigationStack` ](xref:Xamarin.Forms.INavigation.NavigationStack) propriedade da qual as páginas na pilha de navegação podem ser obtidas. Enquanto o xamarin. Forms mantém o acesso a pilha de navegação, o `Navigation` propriedade fornece a [ `InsertPageBefore` ](xref:Xamarin.Forms.INavigation.InsertPageBefore*) e [ `RemovePage` ](xref:Xamarin.Forms.INavigation.RemovePage*) métodos para manipular a pilha, inserindo páginas ou removê-los.

O [ `InsertPageBefore` ](xref:Xamarin.Forms.INavigation.InsertPageBefore*) método insere uma página especificada na pilha de navegação antes de uma página existente especificada, conforme mostrado no diagrama a seguir:

![](hierarchical-images/insert-page-before.png "Inserindo uma página na pilha de navegação")

O [ `RemovePage` ](xref:Xamarin.Forms.INavigation.RemovePage*) método remove a página especificada da pilha de navegação, conforme mostrado no diagrama a seguir:

![](hierarchical-images/remove-page.png "Removendo uma página de pilha de navegação")

Esses métodos permitem uma experiência de navegação personalizada, como substituição de uma página de logon com uma nova página, após um logon bem-sucedido. O exemplo de código a seguir demonstra esse cenário:

```csharp
async void OnLoginButtonClicked (object sender, EventArgs e)
{
  ...
  var isValid = AreCredentialsCorrect (user);
  if (isValid) {
    App.IsUserLoggedIn = true;
    Navigation.InsertPageBefore (new MainPage (), this);
    await Navigation.PopAsync ();
  } else {
    // Login failed
  }
}

```

Desde que as credenciais do usuário estão corretas, o `MainPage` instância é inserida na pilha de navegação antes da página atual. O [ `PopAsync` ](xref:Xamarin.Forms.NavigationPage.PopAsync) , em seguida, o método remove a página atual da pilha de navegação, com o `MainPage` instância tornando-se a página ativa.

## <a name="summary"></a>Resumo

Este artigo demonstrou como usar o [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage) classe para executar a navegação em uma pilha de páginas. Essa classe fornece uma experiência de Navegação hierárquica em que o usuário é capaz de navegar pelas páginas de frente e para trás, conforme desejado. A classe implementa navegação como uma pilha UEPS (último a entrar, primeiro a sair) de objetos [`Page`](xref:Xamarin.Forms.Page).


## <a name="related-links"></a>Links relacionados

- [Navegação de página](https://developer.xamarin.com/r/xamarin-forms/book/chapter24.pdf)
- [Hierarchical (amostra)](https://developer.xamarin.com/samples/xamarin-forms/Navigation/Hierarchical/)
- [PassingData (amostra)](https://developer.xamarin.com/samples/xamarin-forms/Navigation/PassingData/)
- [LoginFlow (amostra)](https://developer.xamarin.com/samples/xamarin-forms/Navigation/LoginFlow/)
- [Como criar uma entrada no fluxo de tela do exemplo de xamarin. Forms (vídeo do Xamarin University)](http://xamarinuniversity.blob.core.windows.net/lightninglectures/CreateASignIn.zip)
- [Como criar uma entrada no fluxo de tela do xamarin. Forms (vídeo do Xamarin University)](https://university.xamarin.com/lightninglectures/how-to-create-a-sign-in-screen-flow-in-xamarinforms)
- [NavigationPage](xref:Xamarin.Forms.NavigationPage)
