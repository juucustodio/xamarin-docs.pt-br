---
title: Navegação hierárquica
description: Este artigo demonstra como usar a classe NavigationPage para executar a navegação em uma pilha de páginas UEPS (último a entrar, primeiro a sair).
ms.prod: xamarin
ms.assetid: C8A5EEFF-5A3B-4163-838A-147EE3939FAA
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/10/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 8e8c52c03534ca5d8b653eed4d8fc2c8f39edf61
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91562633"
---
# <a name="hierarchical-navigation"></a>Navegação hierárquica

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-hierarchical)

_A classe NavigationPage fornece uma experiência de navegação hierárquica em que o usuário é capaz de navegar pelas páginas, encaminhamentos e para trás, conforme desejado. A classe implementa a navegação como uma pilha UEPS (último a entrar, primeiro a sair) de objetos de página. Este artigo demonstra como usar a classe NavigationPage para realizar a navegação em uma pilha de páginas._

Para ir de uma página para outra, um aplicativo enviará por push uma nova página para a pilha de navegação, na qual ela se tornará a página ativa, conforme mostrado no diagrama a seguir:

![Enviando uma página por push para a pilha de navegação](hierarchical-images/pushing.png)

Para retornar à página anterior, o aplicativo removerá a página atual da pilha de navegação e, em seguida, a nova página de nível superior se tornará a página ativa, conforme mostrado no diagrama a seguir:

![Removendo uma página da pilha de navegação](hierarchical-images/popping.png)

Os métodos de navegação são expostos pela [`Navigation`](xref:Xamarin.Forms.NavigableElement.Navigation) propriedade em qualquer [`Page`](xref:Xamarin.Forms.Page) tipo derivado. Esses métodos possibilitam enviar páginas por push para a pilha de navegação, remover páginas da pilha de navegação e executar a manipulação da pilha.

## <a name="performing-navigation"></a>Executando a navegação

Na navegação hierárquica, a [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) classe é usada para navegar por uma pilha de [`ContentPage`](xref:Xamarin.Forms.ContentPage) objetos. As capturas de tela a seguir mostram os principais componentes de `NavigationPage` em cada plataforma:

![Componentes de NavigationPage](hierarchical-images/navigationpage-components.png)

O layout de um [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) depende da plataforma:

- No iOS, há uma barra de navegação na parte superior da página que exibe um título e que tem um botão *Voltar* que leva à página anterior.
- No Android, há uma barra de navegação na parte superior da página que exibe um título, um ícone e um botão *Voltar* que leva à página anterior. O ícone é definido no atributo `[Activity]` que decora a classe `MainActivity` no projeto específico da plataforma Android.
- Na Plataforma Universal do Windows, há uma barra de navegação na parte superior da página que exibe um título.

Em todas as plataformas, o valor da [`Page.Title`](xref:Xamarin.Forms.Page.Title) propriedade será exibido como o título da página. Além disso, a `IconColor` propriedade pode ser definida como um [`Color`](xref:Xamarin.Forms.Color) que é aplicado ao ícone na barra de navegação.

> [!NOTE]
> É recomendável que um `NavigationPage` seja preenchido somente com instâncias de `ContentPage`.

### <a name="creating-the-root-page"></a>Criando a Página Raiz

A primeira página adicionada a uma pilha de navegação é conhecida como a página *raiz* do aplicativo e o exemplo de código a seguir mostra como isso é realizado:

```csharp
public App ()
{
  MainPage = new NavigationPage (new Page1Xaml ());
}
```

Isso faz com que a `Page1Xaml` [`ContentPage`](xref:Xamarin.Forms.ContentPage) instância seja enviada para a pilha de navegação, onde se torna a página ativa e a página raiz do aplicativo. Isso é mostrado nas seguintes capturas de tela:

![Página Raiz da pilha de navegação](hierarchical-images/mainpage.png)

> [!NOTE]
> A [`RootPage`](xref:Xamarin.Forms.NavigationPage.RootPage) propriedade de uma [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) instância fornece acesso à primeira página na pilha de navegação.

### <a name="pushing-pages-to-the-navigation-stack"></a>Enviando páginas por push para a pilha de navegação

Para navegar até `Page2Xaml` , é necessário invocar o [`PushAsync`](xref:Xamarin.Forms.NavigationPage.PushAsync*) método na [`Navigation`](xref:Xamarin.Forms.NavigableElement.Navigation) propriedade da página atual, conforme demonstrado no exemplo de código a seguir:

```csharp
async void OnNextPageButtonClicked (object sender, EventArgs e)
{
  await Navigation.PushAsync (new Page2Xaml ());
}
```

Isso faz com que a instância `Page2Xaml` seja enviada por push para a pilha de navegação, em que ele se torna a página ativa. Isso é mostrado nas seguintes capturas de tela:

![Página enviada por push para a pilha de navegação](hierarchical-images/secondpage.png)

Quando o [`PushAsync`](xref:Xamarin.Forms.NavigationPage.PushAsync*) método é invocado, ocorrem os seguintes eventos:

- A chamada de página `PushAsync` tem sua [`OnDisappearing`](xref:Xamarin.Forms.Page.OnDisappearing) substituição invocada.
- A página que está sendo navegada tem sua [`OnAppearing`](xref:Xamarin.Forms.Page.OnAppearing) substituição invocada.
- A tarefa `PushAsync` é concluída.

No entanto, a ordem exata em que esses eventos ocorrem depende da plataforma. Para obter mais informações, consulte o [capítulo 24](https://developer.xamarin.com/r/xamarin-forms/book/chapter24.pdf) do livro de Charles Petzold Xamarin.Forms .

> [!NOTE]
> Chamadas para as [`OnDisappearing`](xref:Xamarin.Forms.Page.OnDisappearing) [`OnAppearing`](xref:Xamarin.Forms.Page.OnAppearing) substituições e não podem ser tratadas como indicações Garantidas da navegação de página. Por exemplo, no iOS, a substituição `OnDisappearing` é chamada na página ativa quando o aplicativo é encerrado.

### <a name="popping-pages-from-the-navigation-stack"></a>Removendo páginas da pilha de navegação

A página ativa pode ser removida como o item mais recente da pilha de navegação pressionando o botão *Voltar* no dispositivo, independentemente de este ser um botão físico no dispositivo ou um botão na tela.

Para retornar programaticamente à página original, a `Page2Xaml` instância deve invocar o [`PopAsync`](xref:Xamarin.Forms.NavigationPage.PopAsync) método, conforme demonstrado no exemplo de código a seguir:

```csharp
async void OnPreviousPageButtonClicked (object sender, EventArgs e)
{
  await Navigation.PopAsync ();
}
```

Isso faz com que a instância `Page2Xaml` seja removida da pilha de navegação, com a nova página de nível superior tornando-se a página ativa. Quando o [`PopAsync`](xref:Xamarin.Forms.NavigationPage.PopAsync) método é invocado, ocorrem os seguintes eventos:

- A chamada de página `PopAsync` tem sua [`OnDisappearing`](xref:Xamarin.Forms.Page.OnDisappearing) substituição invocada.
- A página que está sendo retornada tem sua [`OnAppearing`](xref:Xamarin.Forms.Page.OnAppearing) substituição invocada.
- A tarefa `PopAsync` é retornada.

No entanto, a ordem exata em que esses eventos ocorrem depende da plataforma. Para obter mais informações, consulte o [capítulo 24](https://developer.xamarin.com/r/xamarin-forms/book/chapter24.pdf) do livro de Charles Petzold Xamarin.Forms .

Assim como [`PushAsync`](xref:Xamarin.Forms.NavigationPage.PushAsync*) [`PopAsync`](xref:Xamarin.Forms.NavigationPage.PopAsync) os métodos e, a [`Navigation`](xref:Xamarin.Forms.NavigableElement.Navigation) propriedade de cada página também fornece um [`PopToRootAsync`](xref:Xamarin.Forms.NavigationPage.PopToRootAsync) método, que é mostrado no exemplo de código a seguir:

```csharp
async void OnRootPageButtonClicked (object sender, EventArgs e)
{
  await Navigation.PopToRootAsync ();
}
```

Esse método exibe tudo, exceto a raiz da [`Page`](xref:Xamarin.Forms.Page) pilha de navegação, tornando a página raiz do aplicativo a página ativa.

### <a name="animating-page-transitions"></a>Animando transições de página

A [`Navigation`](xref:Xamarin.Forms.NavigableElement.Navigation) propriedade de cada página também fornece métodos de push e pop substituídos que incluem um `boolean` parâmetro que controla se uma animação de página deve ser exibida durante a navegação, como mostrado no exemplo de código a seguir:

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

Definir o parâmetro `boolean` como `false` desabilita a animação de transição de página, enquanto definir o parâmetro como `true` habilita a animação de transição de página, desde que ela tenha suporte da plataforma subjacente. No entanto, os métodos de envio por push e remoção que não têm esse parâmetro habilitam a animação por padrão.

## <a name="passing-data-when-navigating"></a>Transmitindo dados ao navegar

Às vezes, é necessário que uma página transmita dados para outra página durante a navegação. Duas técnicas para realizar isso são passando dados por meio de um construtor de página e definindo as novas páginas [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) para os dados. Descreveremos cada técnica separadamente.

### <a name="passing-data-through-a-page-constructor"></a>Transmitindo dados por meio de um construtor de página

A técnica mais simples para transmitir dados para outra página durante a navegação é por meio de um parâmetro de construtor de página, que é mostrado no exemplo de código a seguir:

```csharp
public App ()
{
  MainPage = new NavigationPage (new MainPage (DateTime.Now.ToString ("u")));
}
```

Esse código cria uma `MainPage` instância, passando a data e a hora atuais no formato ISO8601, que é encapsulado em uma [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) instância.

A instância de `MainPage` recebe os dados por meio de um parâmetro de construtor, conforme mostrado no exemplo de código a seguir:

```csharp
public MainPage (string date)
{
  InitializeComponent ();
  dateLabel.Text = date;
}
```

Os dados são exibidos na página definindo a [`Label.Text`](xref:Xamarin.Forms.Label.Text) propriedade, conforme mostrado nas seguintes capturas de tela:

![Dados transmitidos por meio de um construtor de página](hierarchical-images/passing-data-constructor.png)

### <a name="passing-data-through-a-bindingcontext"></a>Transmitindo dados por meio de um BindingContext

Uma abordagem alternativa para passar dados para outra página durante a navegação é definir as novas páginas [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) para os dados, conforme mostrado no exemplo de código a seguir:

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

Esse código define o [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) da `SecondPage` instância para a `Contact` instância do e, em seguida, navega para o `SecondPage` .

O `SecondPage`, em seguida, usa a associação de dados para exibir os dados da instância de `Contact`, conforme mostrado no exemplo de código XAML a seguir:

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

O exemplo de código a seguir mostra como a associação de dados pode ser feita em C#:

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

Os dados são exibidos na página por uma série de [`Label`](xref:Xamarin.Forms.Label) controles, conforme mostrado nas seguintes capturas de tela:

![Dados transmitidos por meio de um BindingContext](hierarchical-images/passing-data-bindingcontext.png)

Para obter mais informações sobre vinculação de dados, veja [Noções básicas de vinculação de dados](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md).

## <a name="manipulating-the-navigation-stack"></a>Manipulando a pilha de navegação

A [`Navigation`](xref:Xamarin.Forms.NavigableElement.Navigation) Propriedade expõe uma [`NavigationStack`](xref:Xamarin.Forms.INavigation.NavigationStack) propriedade da qual as páginas na pilha de navegação podem ser obtidas. Embora o Xamarin.Forms Mantenha o acesso à pilha de navegação, a `Navigation` propriedade fornece os [`InsertPageBefore`](xref:Xamarin.Forms.INavigation.InsertPageBefore*) [`RemovePage`](xref:Xamarin.Forms.INavigation.RemovePage*) métodos e para manipular a pilha inserindo páginas ou removendo-as.

O [`InsertPageBefore`](xref:Xamarin.Forms.INavigation.InsertPageBefore*) método insere uma página especificada na pilha de navegação antes de uma página especificada existente, conforme mostrado no diagrama a seguir:

![Inserindo uma página na pilha de navegação](hierarchical-images/insert-page-before.png)

O [`RemovePage`](xref:Xamarin.Forms.INavigation.RemovePage*) método remove a página especificada da pilha de navegação, conforme mostrado no diagrama a seguir:

![Removendo uma página da pilha de navegação](hierarchical-images/remove-page.png)

Esses métodos permitem criar uma experiência de navegação personalizada, como substituir uma página de logon por uma nova página após um logon bem-sucedido. O código de exemplo a seguir demonstra esse cenário:

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

Desde que as credenciais do usuário estejam corretas, a instância `MainPage` será inserida na pilha de navegação antes da página atual. O [`PopAsync`](xref:Xamarin.Forms.NavigationPage.PopAsync) método, em seguida, remove a página atual da pilha de navegação, com a `MainPage` instância que se torna a página ativa.

## <a name="displaying-views-in-the-navigation-bar"></a>Exibindo modos de exibição na barra de navegação

Qualquer um Xamarin.Forms [`View`](xref:Xamarin.Forms.View) pode ser exibido na barra de navegação de um [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) . Isso é feito definindo a [`NavigationPage.TitleView`](xref:Xamarin.Forms.NavigationPage.TitleViewProperty) Propriedade anexada como um `View` . Essa propriedade anexada pode ser definida em qualquer [`Page`](xref:Xamarin.Forms.Page) , e quando o `Page` for enviado para um `NavigationPage` , o `NavigationPage` respeitará o valor da propriedade.

O exemplo a seguir, extraído do [Exemplo de Modo de Exibição de Título](/samples/xamarin/xamarin-forms-samples/navigation-titleview), mostra como definir a propriedade anexada [`NavigationPage.TitleView`](xref:Xamarin.Forms.NavigationPage.TitleViewProperty) do XAML:

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

Este é o código C# equivalente:

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

Isso resulta em uma [`Slider`](xref:Xamarin.Forms.Slider) exibição na barra de navegação do [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) :

[![TitleView do controle deslizante](hierarchical-images/titleview-small.png "TitleView do controle deslizante")](hierarchical-images/titleview-large.png#lightbox "TitleView do controle deslizante")

> [!IMPORTANT]
> Muitas exibições não aparecerão na barra de navegação, a menos que o tamanho da exibição seja especificado com as [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) Propriedades e. Como alternativa, o modo de exibição pode ser encapsulado em um [`StackLayout`](xref:Xamarin.Forms.StackLayout) com as [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) Propriedades e definidas com os [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) valores apropriados.

Observe que, como a [`Layout`](xref:Xamarin.Forms.Layout) classe deriva da [`View`](xref:Xamarin.Forms.View) classe, a [`TitleView`](xref:Xamarin.Forms.NavigationPage.TitleViewProperty) Propriedade anexada pode ser definida para exibir uma classe de layout que contém várias exibições. No iOS e na UWP (Plataforma Universal do Windows), não é possível alterar a altura da barra de navegação e, portanto, será feito um recorte se o modo de exibição na barra de navegação for maior que o tamanho padrão da barra de navegação. No entanto, no Android, a altura da barra de navegação pode ser alterada definindo a [`NavigationPage.BarHeight`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat.NavigationPage.BarHeightProperty) propriedade vinculável como um `double` representando a nova altura. Para obter mais informações, confira [Definindo a altura da barra de navegação em uma NavigationPage](~/xamarin-forms/platform/android/navigationpage-bar-height.md).

Como alternativa, é possível sugerir uma barra de navegação estendida colocando parte do conteúdo na barra de navegação e parte em um modo de exibição na parte superior do conteúdo da página, cuja cor deve corresponder à da barra de navegação. Além disso, no iOS, a linha separadora e a sombra na parte inferior da barra de navegação podem ser removidas definindo a [`NavigationPage.HideNavigationBarSeparator`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage.HideNavigationBarSeparatorProperty) propriedade vinculável como `true` . Para obter mais informações, confira [Ocultando o separador da barra de navegação em uma NavigationPage](~/xamarin-forms/platform/ios/navigation-bar-separator.md).

> [!NOTE]
> As [`BackButtonTitle`](xref:Xamarin.Forms.NavigationPage.BackButtonTitleProperty) [`Title`](xref:Xamarin.Forms.Page.Title) Propriedades,, e [`TitleIcon`](xref:Xamarin.Forms.NavigationPage.TitleIconProperty) [`TitleView`](xref:Xamarin.Forms.NavigationPage.TitleViewProperty) podem definir valores que ocupam espaço na barra de navegação. Embora o tamanho da barra de navegação varie de acordo com o tamanho da tela e a plataforma, definir todas essas propriedades causará conflitos devido à limitação do espaço disponível. Em vez de tentar usar uma combinação dessas propriedades, você provavelmente concluirá que é mais fácil obter o design desejado da barra de navegação definindo apenas a propriedade `TitleView`.

### <a name="limitations"></a>Limitações

Há uma série de limitações a serem consideradas ao exibir um [`View`](xref:Xamarin.Forms.View) na barra de navegação de um [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) :

- No iOS, modos de exibição colocados na barra de navegação de um `NavigationPage` poderão aparecer em uma posição diferente se títulos grandes estiverem habilitados. Para obter mais informações sobre a habilitação de títulos grandes, confira [Exibindo títulos grandes](~/xamarin-forms/platform/ios/page-large-title.md).
- No Android, só é possível colocar modos de exibição na barra de navegação de um `NavigationPage` em aplicativos que usam a compatibilidade de aplicativos.
- Não é recomendável posicionar exibições grandes e complexas, como [`ListView`](xref:Xamarin.Forms.ListView) e [`TableView`](xref:Xamarin.Forms.TableView) , na barra de navegação de um `NavigationPage` .

## <a name="related-links"></a>Links Relacionados

- [Navegação de página](https://developer.xamarin.com/r/xamarin-forms/book/chapter24.pdf)
- [Hierárquica (amostra)](/samples/xamarin/xamarin-forms-samples/navigation-hierarchical)
- [PassingData (amostra)](/samples/xamarin/xamarin-forms-samples/navigation-passingdata)
- [LoginFlow (amostra)](/samples/xamarin/xamarin-forms-samples/navigation-loginflow)
- [TitleView (amostra)](/samples/xamarin/xamarin-forms-samples/navigation-titleview)
- [Como criar um fluxo de tela de entrada em Xamarin.Forms vídeo](https://www.youtube.com/watch?v=qKQ7pyyG1fo)
- [NavigationPage](xref:Xamarin.Forms.NavigationPage)