---
title: Navegação hierárquica
description: Este artigo demonstra como usar a classe NavigationPage para executar a navegação em uma pilha de último a entrar, primeiro a sair (UEPS) páginas.
ms.prod: xamarin
ms.assetid: C8A5EEFF-5A3B-4163-838A-147EE3939FAA
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/14/2018
ms.openlocfilehash: a0a58cf05c97221a73cd0784b7859bb9c84cef86
ms.sourcegitcommit: 7f6127c2f425fadc675b77d14de7a36103cff675
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/24/2018
ms.locfileid: "38994670"
---
# <a name="hierarchical-navigation"></a>Navegação hierárquica

_A classe NavigationPage fornece uma experiência de Navegação hierárquica em que o usuário é capaz de navegar pelas páginas de frente e para trás, conforme desejado. A classe implementa navegação como uma pilha último a entrar, primeiro a sair (UEPS) dos objetos de página. Este artigo demonstra como usar a classe NavigationPage para executar a navegação em uma pilha de páginas._

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

## <a name="displaying-views-in-the-navigation-bar"></a>Exibindo os modos de exibição na barra de navegação

Qualquer xamarin. Forms [ `View` ](xref:Xamarin.Forms.View) pode ser exibido na barra de navegação de uma [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage). Isso é feito definindo a [ `NavigationPage.TitleView` ](xref:Xamarin.Forms.NavigationPage.TitleViewProperty) anexado à propriedade um `View`. Essa propriedade anexada pode ser definida em qualquer [ `Page` ](xref:Xamarin.Forms.Page)e quando o `Page` é enviado por push uma `NavigationPage`, o `NavigationPage` respeitará o valor da propriedade.

O exemplo a seguir, extraído do [exemplo de exibição de título](https://developer.xamarin.com/samples/xamarin-forms/Navigation/TitleView/), mostra como definir o [ `NavigationPage.TitleView` ](xref:Xamarin.Forms.NavigationPage.TitleViewProperty) propriedade anexada do XAML:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="NavigationPageTitleView.TitleViewPage">
    <NavigationPage.TitleView>
        <Slider HeightRequest="44" WidthRequest="300" />
    </NavigationPage.TitleView>
    ...
</ContentPage>
```

Este é o equivalente C# código:

```csharp
public class TitleViewPage : ContentPage
{
    public TitleViewPage()
    {
        var titleView = new Slider { HeightRequest = 44, WidthRequest = 300 };
        NavigationPage.SetTitleView(this, titleView);
        ...
    }
}
```

Isso resulta em uma [ `Slider` ](xref:Xamarin.Forms.Slider) que está sendo exibido na barra de navegação na [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage):

[![Controle deslizante TitleView](hierarchical-images/titleview-small.png "TitleView do controle deslizante")](hierarchical-images/titleview-large.png#lightbox "TitleView de controle deslizante")

> [!IMPORTANT]
> Vários modos de exibição não aparecerão na barra de navegação, a menos que o tamanho da exibição é especificado com o [ `WidthRequest` ](xref:Xamarin.Forms.VisualElement.WidthRequest) e [ `HeightRequest` ](xref:Xamarin.Forms.VisualElement.HeightRequest) propriedades. Como alternativa, o modo de exibição pode ser encapsulado em uma [ `StackLayout` ](xref:Xamarin.Forms.StackLayout) com o [ `HorizontalOptions` ](xref:Xamarin.Forms.View.HorizontalOptions) e [ `VerticalOptions` ](xref:Xamarin.Forms.View.VerticalOptions) propriedades definidas para os valores apropriados.

Observe que, como o [ `Layout` ](xref:Xamarin.Forms.Layout) classe deriva de [ `View` ](xref:Xamarin.Forms.View) classe, o [ `TitleView` ](xref:Xamarin.Forms.NavigationPage.TitleViewProperty) propriedade anexada pode ser definida para exibir um layout classe que contém vários modos de exibição. No iOS e Universal Windows Platform (UWP), não é possível alterar a altura da barra de navegação e, portanto recorte ocorrerá se a exibição na barra de navegação for maior que o tamanho padrão da barra de navegação. No entanto, no Android, a altura da barra de navegação pode ser alterada definindo a [ `NavigationPage.BarHeight` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat.NavigationPage.BarHeightProperty) propriedade associável a uma `double` que representa a nova altura. Para obter mais informações, consulte [definindo a altura da barra de navegação em uma NavigationPage](~/xamarin-forms/platform/platform-specifics/consuming/android.md#navigationpage-barheight).

Como alternativa, uma barra de navegação estendido pode ser sugerida, colocando alguns do conteúdo na barra de navegação e outros em uma exibição na parte superior do conteúdo de página que você cor corresponder à barra de navegação. Além disso, no iOS a linha separadora e sombra que está na parte inferior da barra de navegação pode ser removida, definindo o [ `NavigationPage.HideNavigationBarSeparator` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage.HideNavigationBarSeparatorProperty) para a propriedade associável `true`. Para obter mais informações, consulte [ocultando o separador de barra de navegação em uma NavigationPage](~/xamarin-forms/platform/platform-specifics/consuming/ios.md#navigationpage-hideseparatorbar).

> [!NOTE]
> O [ `BackButtonTitle` ](xref:Xamarin.Forms.NavigationPage.BackButtonTitleProperty), [ `Title` ](xref:Xamarin.Forms.Page.Title), [ `TitleIcon` ](xref:Xamarin.Forms.NavigationPage.TitleIconProperty), e [ `TitleView` ](xref:Xamarin.Forms.NavigationPage.TitleViewProperty) propriedades podem definir valores que ocupam espaço na barra de navegação. Embora o tamanho da barra de navegação varia de acordo com o tamanho de tela e de plataforma, definir todas essas propriedades poderá resultar em conflitos devido a espaço limitado disponível. Em vez de tentar usar uma combinação dessas propriedades, você pode achar que você pode obter melhor seu design de barra de navegação desejado, definindo apenas o `TitleView` propriedade.

### <a name="limitations"></a>Limitações

Há uma série de limitações a serem consideradas ao exibir uma [ `View` ](xref:Xamarin.Forms.View) na barra de navegação de uma [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage):

- No iOS, modos de exibição é colocado na barra de navegação de um `NavigationPage` aparecem em uma posição diferente dependendo se os títulos grandes estão habilitados. Para obter mais informações sobre como habilitar títulos grandes, consulte [exibir títulos grandes](~/xamarin-forms/platform/platform-specifics/consuming/ios.md#large_title).
- No Android, colocando os modos de exibição na barra de navegação de um `NavigationPage` só pode ser realizada em aplicativos que usam a compatibilidade de aplicativos.
- Não é recomendável colocar as exibições grandes e complexas, como [ `ListView` ](xref:Xamarin.Forms.ListView) e [ `TableView` ](xref:Xamarin.Forms.TableView), na barra de navegação de um `NavigationPage`.

## <a name="related-links"></a>Links relacionados

- [Navegação de página](https://developer.xamarin.com/r/xamarin-forms/book/chapter24.pdf)
- [Hierarchical (amostra)](https://developer.xamarin.com/samples/xamarin-forms/Navigation/Hierarchical/)
- [PassingData (amostra)](https://developer.xamarin.com/samples/xamarin-forms/Navigation/PassingData/)
- [LoginFlow (amostra)](https://developer.xamarin.com/samples/xamarin-forms/Navigation/LoginFlow/)
- [TitleView (amostra)](https://developer.xamarin.com/samples/xamarin-forms/Navigation/TitleView/)
- [Como criar uma entrada no fluxo de tela do exemplo de xamarin. Forms (vídeo do Xamarin University)](http://xamarinuniversity.blob.core.windows.net/lightninglectures/CreateASignIn.zip)
- [Como criar uma entrada no fluxo de tela do xamarin. Forms (vídeo do Xamarin University)](https://university.xamarin.com/lightninglectures/how-to-create-a-sign-in-screen-flow-in-xamarinforms)
- [NavigationPage](xref:Xamarin.Forms.NavigationPage)
