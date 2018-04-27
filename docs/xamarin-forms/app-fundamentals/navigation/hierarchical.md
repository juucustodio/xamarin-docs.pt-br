---
title: Navegação hierárquica
description: A classe NavigationPage fornece uma experiência de Navegação hierárquica em que o usuário é capaz de navegar pelas páginas, frente e para trás, conforme desejado. A classe implementa navegação como uma pilha último a entrar, primeiro a sair (UEPS) dos objetos de página. Este artigo demonstra como usar a classe NavigationPage para executar a navegação em uma pilha de páginas.
ms.prod: xamarin
ms.assetid: C8A5EEFF-5A3B-4163-838A-147EE3939FAA
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/10/2017
ms.openlocfilehash: 3fc5b24474230fd2b2477f020ac24cd72996d7b1
ms.sourcegitcommit: 1561c8022c3585655229a869d9ef3510bf83f00a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/27/2018
---
# <a name="hierarchical-navigation"></a>Navegação hierárquica

_A classe NavigationPage fornece uma experiência de Navegação hierárquica em que o usuário é capaz de navegar pelas páginas, frente e para trás, conforme desejado. A classe implementa navegação como uma pilha último a entrar, primeiro a sair (UEPS) dos objetos de página. Este artigo demonstra como usar a classe NavigationPage para executar a navegação em uma pilha de páginas._

Este artigo aborda os seguintes tópicos:

- [Executar a navegação](#Performing_Navigation) – criar a página de raiz, enviar páginas para a pilha de navegação, tirando páginas da pilha de navegação e animação de transições de página.
- [Transmitindo dados ao navegar](#Passing_Data_when_Navigating) – passar dados por meio de um construtor de página e um `BindingContext`.
- [Manipulando a pilha de navegação](#Manipulating_the_Navigation_Stack) – manipulando a pilha inserindo ou remoção de páginas.

## <a name="overview"></a>Visão geral

Para mover de uma página para outra, um aplicativo enviará por push uma nova página para a pilha de navegação, em que ela se tornará a página ativa, conforme mostrado no diagrama a seguir:

![](hierarchical-images/pushing.png "Enviar uma página para a pilha de navegação")

Para retornar para a página anterior, o aplicativo será exibida a página atual da pilha de navegação, e a nova página de nível superior se torna a página ativa, conforme mostrado no diagrama a seguir:

![](hierarchical-images/popping.png "Exibir uma página de pilha de navegação")

Métodos de navegação são expostos pelo [ `Navigation` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Navigation/) propriedade em qualquer [ `Page` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/) tipos derivados. Esses métodos fornecem a capacidade de enviar por push páginas para a pilha de navegação, em páginas pop da pilha de navegação e para executar a manipulação de pilha.

<a name="Performing_Navigation" />

## <a name="performing-navigation"></a>Executar a navegação

Na barra de navegação hierárquica, a classe [`NavigationPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/) é usada para navegar por meio de uma pilha de objetos [`ContentPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/). As capturas de tela a seguir mostram os principais componentes do `NavigationPage` em cada plataforma:

![](hierarchical-images/navigationpage-components.png "Componentes de NavigationPage")

O layout de um [ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/) depende da plataforma:

- No iOS, uma barra de navegação está presente na parte superior da página que exibe um título, e que possui um *novamente* botão retorna à página anterior.
- No Android, uma barra de navegação está presente na parte superior da página que exibe um título, um ícone e um *novamente* botão retorna à página anterior. O ícone é definido no `[Activity]` atributo decora o `MainActivity` classe no projeto específico da plataforma Android.
- Na plataforma Universal do Windows, uma barra de navegação está presente na parte superior da página que exibe um título. 

Em todas as plataformas, o valor de [ `Page.Title` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Page.Title/) propriedade será exibida como o título da página.

> [!NOTE]
> Recomenda-se que um `NavigationPage` devem ser preenchidos com `ContentPage` somente instâncias.

### <a name="creating-the-root-page"></a>Criando a página de raiz

A primeira página adicionada a uma pilha de navegação é conhecida como a página *raiz* do aplicativo e o exemplo de código a seguir mostra como isso é realizado:

```csharp
public App ()
{
  MainPage = new NavigationPage (new Page1Xaml ());
}
```

Isso faz com que o `Page1Xaml` [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/) instância a ser enviado para a pilha de navegação, em que ele se torna a página ativa e a página de raiz do aplicativo. Isso é mostrado nas capturas de tela seguir:

![](hierarchical-images/mainpage.png "Página de raiz da pilha de navegação")

> [!NOTE]
> O [ `RootPage` ](https://developer.xamarin.com/api/property/Xamarin.Forms.NavigationPage.RootPage/) propriedade de um [ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/) instância fornece acesso para a primeira página na pilha de navegação.

### <a name="pushing-pages-to-the-navigation-stack"></a>Páginas de envio para a pilha de navegação

Para navegar até `Page2Xaml`, é necessário chamar o [ `PushAsync` ](https://developer.xamarin.com/api/member/Xamarin.Forms.NavigationPage.PushAsync(Xamarin.Forms.Page)/) método o [ `Navigation` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Navigation/) propriedade da página atual, como demonstrado no exemplo de código a seguir:

```csharp
async void OnNextPageButtonClicked (object sender, EventArgs e)
{
  await Navigation.PushAsync (new Page2Xaml ());
}
```

Isso faz com que a instância `Page2Xaml` seja enviada por push para a pilha de navegação, em que ele se torna a página ativa. Isso é mostrado nas capturas de tela seguir:

![](hierarchical-images/secondpage.png "Página enviada por push para pilha de navegação")

Quando o [ `PushAsync` ](https://developer.xamarin.com/api/member/Xamarin.Forms.NavigationPage.PushAsync(Xamarin.Forms.Page)/) método é chamado, os seguintes eventos ocorrem:

- A chamada de página `PushAsync` tem seu [ `OnDisappearing` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.OnDisappearing/) substituição invocada.
- A página que está sendo direcionada tem seu [ `OnAppearing` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.OnAppearing/) substituição invocada.
- O `PushAsync` tarefa é concluída.

No entanto, a ordem exata em que esses eventos ocorrem é dependente de plataforma. Para obter mais informações, consulte [capítulo 24](https://developer.xamarin.com/r/xamarin-forms/book/chapter24.pdf) do catálogo do Charles Petzold xamarin. Forms.

> [!NOTE]
> Chamadas para o [ `OnDisappearing` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.OnDisappearing/) e [ `OnAppearing` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.OnAppearing/) substituições não podem ser tratadas como garantidas indicações de navegação de página. Por exemplo, no iOS, o `OnDisappearing` substituição é chamada na página ativa quando o aplicativo termina.

### <a name="popping-pages-from-the-navigation-stack"></a>Páginas produzirão da pilha de navegação

A página ativa pode ser removida como o item mais recente da pilha de navegação pressionando o botão *Voltar* no dispositivo, independentemente de este ser um botão físico no dispositivo ou um botão na tela.

Para retornar programaticamente à página original, a instância `Page2Xaml` deve invocar o método [`PopAsync`](https://developer.xamarin.com/api/member/Xamarin.Forms.NavigationPage.PopAsync()/), conforme demonstrado no exemplo de código a seguir:

```csharp
async void OnPreviousPageButtonClicked (object sender, EventArgs e)
{
  await Navigation.PopAsync ();
}
```

Isso faz com que a instância `Page2Xaml` seja removida da pilha de navegação, com a nova página de nível superior tornando-se a página ativa. Quando o [ `PopAsync` ](https://developer.xamarin.com/api/member/Xamarin.Forms.NavigationPage.PopAsync()/) método é chamado, os seguintes eventos ocorrem:

- A chamada de página `PopAsync` tem seu [ `OnDisappearing` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.OnDisappearing/) substituição invocada.
- A página que está sendo retornada ao tem seu [ `OnAppearing` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.OnAppearing/) substituição invocada.
- O `PopAsync` retorna da tarefa.

No entanto, a ordem exata em que esses eventos ocorrem é dependente de plataforma. Para obter mais informações, consulte [capítulo 24](https://developer.xamarin.com/r/xamarin-forms/book/chapter24.pdf) do catálogo do Charles Petzold xamarin. Forms.

Assim como [ `PushAsync` ](https://developer.xamarin.com/api/member/Xamarin.Forms.NavigationPage.PushAsync(Xamarin.Forms.Page)/) e [ `PopAsync` ](https://developer.xamarin.com/api/member/Xamarin.Forms.NavigationPage.PopAsync()/) métodos, o [ `Navigation` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Navigation/) propriedade de cada página também fornece um [ `PopToRootAsync` ](https://developer.xamarin.com/api/member/Xamarin.Forms.NavigationPage.PopToRootAsync()/) método, que é mostrado no exemplo de código a seguir:

```csharp
async void OnRootPageButtonClicked (object sender, EventArgs e)
{
  await Navigation.PopToRootAsync ();
}
```

Esse método pops todos, exceto a raiz [ `Page` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/) na pilha de navegação, tornando a página raiz do aplicativo a página ativa.

### <a name="animating-page-transitions"></a>Animação de transições de página

O [ `Navigation` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Navigation/) propriedade de cada página também fornece envio substituído e os métodos pop que incluem um `boolean` parâmetro que controla se deseja exibir uma animação de página durante a navegação, conforme mostrado no código a seguir exemplo:

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

Definindo o `boolean` parâmetro `false` desativa a animação de transição de página, ao definir o parâmetro `true` permite que a animação de transição de página, desde que ele é suportado pela plataforma subjacente. No entanto, os métodos de envio por push e pop que não têm esse parâmetro habilitam a animação por padrão.

<a name="Passing_Data_when_Navigating" />

## <a name="passing-data-when-navigating"></a>Passando dados ao navegar

Às vezes, é necessário para uma página passar dados para outra página durante a navegação. Duas técnicas para realizar isso estão transmitindo dados por meio de um construtor de página e definindo a nova página [ `BindingContext` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BindableObject.BindingContext/) aos dados. Cada uma será agora ser discutida.

### <a name="passing-data-through-a-page-constructor"></a>Passando dados através de um construtor de página

A técnica mais simples para transferir dados para outra página durante a navegação é por meio de um parâmetro de construtor da página, que é mostrado no exemplo de código a seguir:

```csharp
public App ()
{
  MainPage = new NavigationPage (new MainPage (DateTime.Now.ToString ("u")));
}
```

Esse código cria um `MainPage` da instância, passando a data e hora atuais no formato ISO8601, que é encapsulado em um [ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/) instância.

O `MainPage` instância recebe os dados por meio de um parâmetro de construtor, conforme mostrado no exemplo de código a seguir:

```csharp
public MainPage (string date)
{
  InitializeComponent ();
  dateLabel.Text = date;
}
```

Os dados são exibidos na página definindo o [ `Label.Text` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Label.Text/) propriedade, conforme mostrado nas capturas de tela seguir:

![](hierarchical-images/passing-data-constructor.png "Dados passados por meio de um construtor de página")

### <a name="passing-data-through-a-bindingcontext"></a>Passando dados através de um BindingContext

Uma abordagem alternativa para passar dados para outra página durante a navegação é definindo a nova página [ `BindingContext` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BindableObject.BindingContext/) aos dados, conforme mostrado no exemplo de código a seguir:

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

Esse código define o [ `BindingContext` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BindableObject.BindingContext/) do `SecondPage` de instância para o `Contact` instância e, em seguida, navega para o `SecondPage`.

O `SecondPage` usa associação de dados para exibir o `Contact` instância dados, conforme mostrado no exemplo de código XAML a seguir:

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

O exemplo de código a seguir mostra como a associação de dados pode ser realizada em c#:

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

Os dados são exibidos na página por uma série de [ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) controla, como mostrado nas capturas de tela seguir:

![](hierarchical-images/passing-data-bindingcontext.png "Dados passados por meio de um BindingContext")

Para obter mais informações sobre vinculação de dados, veja [Noções básicas de vinculação de dados](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md).

<a name="Manipulating_the_Navigation_Stack" />

## <a name="manipulating-the-navigation-stack"></a>Manipulando a pilha de navegação

O [ `Navigation` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Navigation/) propriedade expõe um [ `NavigationStack` ](https://developer.xamarin.com/api/property/Xamarin.Forms.INavigation.NavigationStack/) propriedade na qual as páginas na pilha de navegação podem ser obtidas. Enquanto xamarin. Forms mantém o acesso a pilha de navegação, a `Navigation` propriedade fornece o [ `InsertPageBefore` ](https://developer.xamarin.com/api/member/Xamarin.Forms.INavigation.InsertPageBefore(Xamarin.Forms.Page,Xamarin.Forms.Page)/) e [ `RemovePage` ](https://developer.xamarin.com/api/member/Xamarin.Forms.INavigation.RemovePage(Xamarin.Forms.Page)/) métodos para manipular a pilha inserindo páginas ou removê-los.

O [ `InsertPageBefore` ](https://developer.xamarin.com/api/member/Xamarin.Forms.INavigation.InsertPageBefore(Xamarin.Forms.Page,Xamarin.Forms.Page)/) método insere uma página especificada na pilha de navegação antes de uma página especificada existente, conforme mostrado no diagrama a seguir:

![](hierarchical-images/insert-page-before.png "Inserindo uma página na pilha de navegação")

O [ `RemovePage` ](https://developer.xamarin.com/api/member/Xamarin.Forms.INavigation.RemovePage(Xamarin.Forms.Page)/) método remove a página especificada da pilha de navegação, conforme mostrado no diagrama a seguir:

![](hierarchical-images/remove-page.png "Removendo uma página de pilha de navegação")

Esses métodos permitem uma experiência de navegação personalizada, como a substituição de uma página de logon com uma nova página, após um logon bem-sucedido. O exemplo de código a seguir demonstra esse cenário:

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

Desde que as credenciais do usuário estão corretas, a `MainPage` instância é inserida na pilha de navegação antes da página atual. O [ `PopAsync` ](https://developer.xamarin.com/api/member/Xamarin.Forms.NavigationPage.PopAsync()/) , em seguida, o método remove a página atual da pilha de navegação, com o `MainPage` instância se torna a página ativa.

## <a name="summary"></a>Resumo

Este artigo demonstrou como usar o [ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/) classe para executar a navegação em uma pilha de páginas. Essa classe fornece uma experiência de Navegação hierárquica em que o usuário é capaz de navegar pelas páginas, frente e para trás, conforme desejado. A classe implementa navegação como uma pilha UEPS (último a entrar, primeiro a sair) de objetos [`Page`](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/).


## <a name="related-links"></a>Links relacionados

- [Navegação de página](https://developer.xamarin.com/r/xamarin-forms/book/chapter24.pdf)
- [Hierarchical (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/Navigation/Hierarchical/)
- [PassingData (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/Navigation/PassingData/)
- [LoginFlow (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/Navigation/LoginFlow/)
- [Como criar uma entrada no fluxo de tela no exemplo xamarin. Forms (vídeo do Xamarin University)](http://xamarinuniversity.blob.core.windows.net/lightninglectures/CreateASignIn.zip)
- [Como criar uma entrada no fluxo de tela xamarin. Forms (vídeo do Xamarin University)](https://university.xamarin.com/lightninglectures/how-to-create-a-sign-in-screen-flow-in-xamarinforms)
- [NavigationPage](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/)
