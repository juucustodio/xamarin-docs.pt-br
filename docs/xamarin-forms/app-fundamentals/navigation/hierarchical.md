---
title: Navegação hierárquica
description: Este artigo demonstra como usar a classe NavigationPage para executar a navegação em uma pilha de páginas UEPS (último a entrar, primeiro a sair).
ms.prod: xamarin
ms.assetid: C8A5EEFF-5A3B-4163-838A-147EE3939FAA
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/14/2018
ms.openlocfilehash: 11ad1fb18d1263eb77ef037350a3633510934c42
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "79305111"
---
# <a name="hierarchical-navigation"></a>Navegação hierárquica

[![Baixar](~/media/shared/download.png) amostra Baixar a amostra](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-hierarchical)

_A classe NavigationPage oferece uma experiência de navegação hierárquica onde o usuário é capaz de navegar através de páginas, para frente e para trás, conforme desejado. A classe implementa a navegação como uma pilha de objetos LIFO (First-Out) de última entrada e primeira saída ..de primeira. Este artigo demonstra como usar a classe NavigationPage para executar a navegação em uma pilha de páginas._

Para ir de uma página para outra, um aplicativo enviará por push uma nova página para a pilha de navegação, na qual ela se tornará a página ativa, conforme mostrado no diagrama a seguir:

![](hierarchical-images/pushing.png "Pushing a Page to the Navigation Stack")

Para retornar à página anterior, o aplicativo removerá a página atual da pilha de navegação e, em seguida, a nova página de nível superior se tornará a página ativa, conforme mostrado no diagrama a seguir:

![](hierarchical-images/popping.png "Popping a Page from the Navigation Stack")

Os métodos de navegação [`Navigation`](xref:Xamarin.Forms.NavigableElement.Navigation) são [`Page`](xref:Xamarin.Forms.Page) expostos pela propriedade em quaisquer tipos derivados. Esses métodos possibilitam enviar páginas por push para a pilha de navegação, remover páginas da pilha de navegação e executar a manipulação da pilha.

<a name="Performing_Navigation" />

## <a name="performing-navigation"></a>Executando a navegação

Na navegação hierárquica, a [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) classe [`ContentPage`](xref:Xamarin.Forms.ContentPage) é usada para navegar através de uma pilha de objetos. As capturas de tela a seguir mostram os principais componentes de `NavigationPage` em cada plataforma:

![](hierarchical-images/navigationpage-components.png "NavigationPage Components")

O layout [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) de a depende da plataforma:

- No iOS, há uma barra de navegação na parte superior da página que exibe um título e que tem um botão *Voltar* que leva à página anterior.
- No Android, há uma barra de navegação na parte superior da página que exibe um título, um ícone e um botão *Voltar* que leva à página anterior. O ícone é definido no atributo `[Activity]` que decora a classe `MainActivity` no projeto específico da plataforma Android.
- Na Plataforma Universal do Windows, há uma barra de navegação na parte superior da página que exibe um título.

Em todas as plataformas, [`Page.Title`](xref:Xamarin.Forms.Page.Title) o valor da propriedade será exibido como o título da página.

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

Isso faz `Page1Xaml` [`ContentPage`](xref:Xamarin.Forms.ContentPage) com que a instância seja empurrada para a pilha de navegação, onde se torna a página ativa e a página raiz do aplicativo. Isso é mostrado nas seguintes capturas de tela:

![](hierarchical-images/mainpage.png "Root Page of Navigation Stack")

> [!NOTE]
> A [`RootPage`](xref:Xamarin.Forms.NavigationPage.RootPage) propriedade [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) de uma instância fornece acesso à primeira página na pilha de navegação.

### <a name="pushing-pages-to-the-navigation-stack"></a>Enviando páginas por push para a pilha de navegação

Para navegar, `Page2Xaml`é necessário invocar [`PushAsync`](xref:Xamarin.Forms.NavigationPage.PushAsync*) o [`Navigation`](xref:Xamarin.Forms.NavigableElement.Navigation) método na propriedade da página atual, conforme demonstrado no exemplo de código a seguir:

```csharp
async void OnNextPageButtonClicked (object sender, EventArgs e)
{
  await Navigation.PushAsync (new Page2Xaml ());
}
```

Isso faz com que a instância `Page2Xaml` seja enviada por push para a pilha de navegação, em que ele se torna a página ativa. Isso é mostrado nas seguintes capturas de tela:

![](hierarchical-images/secondpage.png "Page Pushed onto Navigation Stack")

Quando [`PushAsync`](xref:Xamarin.Forms.NavigationPage.PushAsync*) o método é invocado, ocorrem os seguintes eventos:

- A chamada `PushAsync` de [`OnDisappearing`](xref:Xamarin.Forms.Page.OnDisappearing) página tem sua substituição invocada.
- A página que está [`OnAppearing`](xref:Xamarin.Forms.Page.OnAppearing) sendo navegada tem sua substituição invocada.
- A tarefa `PushAsync` é concluída.

No entanto, a ordem exata em que esses eventos ocorrem depende da plataforma. Para obter mais informações, consulte [o capítulo 24](https://developer.xamarin.com/r/xamarin-forms/book/chapter24.pdf) do livro Xamarin.Forms de Charles Petzold.

> [!NOTE]
> Chamadas para [`OnDisappearing`](xref:Xamarin.Forms.Page.OnDisappearing) [`OnAppearing`](xref:Xamarin.Forms.Page.OnAppearing) as substituições e substituições não podem ser tratadas como indicações garantidas de navegação de página. Por exemplo, no iOS, a substituição `OnDisappearing` é chamada na página ativa quando o aplicativo é encerrado.

### <a name="popping-pages-from-the-navigation-stack"></a>Removendo páginas da pilha de navegação

A página ativa pode ser removida como o item mais recente da pilha de navegação pressionando o botão *Voltar* no dispositivo, independentemente de este ser um botão físico no dispositivo ou um botão na tela.

Para retornar programáticamente à página `Page2Xaml` original, [`PopAsync`](xref:Xamarin.Forms.NavigationPage.PopAsync) a instância deve invocar o método, conforme demonstrado no exemplo de código a seguir:

```csharp
async void OnPreviousPageButtonClicked (object sender, EventArgs e)
{
  await Navigation.PopAsync ();
}
```

Isso faz com que a instância `Page2Xaml` seja removida da pilha de navegação, com a nova página de nível superior tornando-se a página ativa. Quando [`PopAsync`](xref:Xamarin.Forms.NavigationPage.PopAsync) o método é invocado, ocorrem os seguintes eventos:

- A chamada `PopAsync` de [`OnDisappearing`](xref:Xamarin.Forms.Page.OnDisappearing) página tem sua substituição invocada.
- A página que está [`OnAppearing`](xref:Xamarin.Forms.Page.OnAppearing) sendo devolvida tem sua substituição invocada.
- A tarefa `PopAsync` é retornada.

No entanto, a ordem exata em que esses eventos ocorrem depende da plataforma. Para obter mais informações, confira o [Capítulo 24](https://developer.xamarin.com/r/xamarin-forms/book/chapter24.pdf) do livro de Charles Petzold sobre Xamarin.Forms.

Assim como [`PushAsync`](xref:Xamarin.Forms.NavigationPage.PushAsync*) [`PopAsync`](xref:Xamarin.Forms.NavigationPage.PopAsync) e métodos, a [`Navigation`](xref:Xamarin.Forms.NavigableElement.Navigation) propriedade de [`PopToRootAsync`](xref:Xamarin.Forms.NavigationPage.PopToRootAsync) cada página também fornece um método, que é mostrado no seguinte exemplo de código:

```csharp
async void OnRootPageButtonClicked (object sender, EventArgs e)
{
  await Navigation.PopToRootAsync ();
}
```

Este método estoura tudo, [`Page`](xref:Xamarin.Forms.Page) menos a raiz da pilha de navegação, tornando assim a página raiz do aplicativo a página ativa.

### <a name="animating-page-transitions"></a>Animando transições de página

A [`Navigation`](xref:Xamarin.Forms.NavigableElement.Navigation) propriedade de cada página também fornece métodos de `boolean` pressão e pop substituídos que incluem um parâmetro que controla se deve exibir uma animação de página durante a navegação, como mostrado no seguinte exemplo de código:

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

<a name="Passing_Data_when_Navigating" />

## <a name="passing-data-when-navigating"></a>Transmitindo dados ao navegar

Às vezes, é necessário que uma página transmita dados para outra página durante a navegação. Duas técnicas para conseguir isso são passar dados através de um [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) construtor de páginas e definir as novas páginas para os dados. Descreveremos cada técnica separadamente.

### <a name="passing-data-through-a-page-constructor"></a>Transmitindo dados por meio de um construtor de página

A técnica mais simples para transmitir dados para outra página durante a navegação é por meio de um parâmetro de construtor de página, que é mostrado no exemplo de código a seguir:

```csharp
public App ()
{
  MainPage = new NavigationPage (new MainPage (DateTime.Now.ToString ("u")));
}
```

Esse código `MainPage` cria uma instância, passando na data e hora atuais no formato [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) ISO8601, que é embrulhado em uma instância.

A instância de `MainPage` recebe os dados por meio de um parâmetro de construtor, conforme mostrado no exemplo de código a seguir:

```csharp
public MainPage (string date)
{
  InitializeComponent ();
  dateLabel.Text = date;
}
```

Os dados são exibidos na página [`Label.Text`](xref:Xamarin.Forms.Label.Text) definindo a propriedade, conforme mostrado nas capturas de tela a seguir:

![](hierarchical-images/passing-data-constructor.png "Data Passed Through a Page Constructor")

### <a name="passing-data-through-a-bindingcontext"></a>Transmitindo dados por meio de um BindingContext

Uma abordagem alternativa para passar dados para outra página [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) durante a navegação é definindo as novas páginas para os dados, como mostrado no exemplo de código a seguir:

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

Este código [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) define `SecondPage` a ocorrência `Contact` para a instância e, em seguida, navega para o `SecondPage`.

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

Os dados são então exibidos na [`Label`](xref:Xamarin.Forms.Label) página por uma série de controles, como mostrado nas capturas de tela a seguir:

![](hierarchical-images/passing-data-bindingcontext.png "Data Passed Through a BindingContext")

Para obter mais informações sobre vinculação de dados, veja [Noções básicas de vinculação de dados](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md).

<a name="Manipulating_the_Navigation_Stack" />

## <a name="manipulating-the-navigation-stack"></a>Manipulando a pilha de navegação

A [`Navigation`](xref:Xamarin.Forms.NavigableElement.Navigation) propriedade expõe [`NavigationStack`](xref:Xamarin.Forms.INavigation.NavigationStack) uma propriedade a partir da qual as páginas na pilha de navegação podem ser obtidas. Enquanto o Xamarin.Forms mantém `Navigation` o acesso [`InsertPageBefore`](xref:Xamarin.Forms.INavigation.InsertPageBefore*) [`RemovePage`](xref:Xamarin.Forms.INavigation.RemovePage*) à pilha de navegação, a propriedade fornece e métodos para manipular a pilha inserindo páginas ou removendo-as.

O [`InsertPageBefore`](xref:Xamarin.Forms.INavigation.InsertPageBefore*) método insere uma página especificada na pilha de navegação antes de uma página especificada existente, conforme mostrado no diagrama a seguir:

![](hierarchical-images/insert-page-before.png "Inserting a Page in the Navigation Stack")

O [`RemovePage`](xref:Xamarin.Forms.INavigation.RemovePage*) método remove a página especificada da pilha de navegação, conforme mostrado no diagrama a seguir:

![](hierarchical-images/remove-page.png "Removing a Page from the Navigation Stack")

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

Desde que as credenciais do usuário estejam corretas, a instância `MainPage` será inserida na pilha de navegação antes da página atual. Em [`PopAsync`](xref:Xamarin.Forms.NavigationPage.PopAsync) seguida, o método remove a página `MainPage` atual da pilha de navegação, com a instância se tornando a página ativa.

## <a name="displaying-views-in-the-navigation-bar"></a>Exibindo modos de exibição na barra de navegação

Qualquer Xamarin.Forms [`View`](xref:Xamarin.Forms.View) pode ser exibido na [`NavigationPage`](xref:Xamarin.Forms.NavigationPage)barra de navegação de a . Isso é feito [`NavigationPage.TitleView`](xref:Xamarin.Forms.NavigationPage.TitleViewProperty) definindo a `View`propriedade anexada a um . Esta propriedade anexada pode [`Page`](xref:Xamarin.Forms.Page)ser definida `Page` em qualquer , `NavigationPage`e `NavigationPage` quando a é empurrada para um , o vai respeitar o valor do imóvel.

O exemplo a seguir, extraído do [Exemplo de Modo de Exibição de Título](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-titleview), mostra como definir a propriedade anexada [`NavigationPage.TitleView`](xref:Xamarin.Forms.NavigationPage.TitleViewProperty) do XAML:

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

Isso resulta [`Slider`](xref:Xamarin.Forms.Slider) em um ser exibido [`NavigationPage`](xref:Xamarin.Forms.NavigationPage)na barra de navegação no :

[![Título do controle deslizanteVer](hierarchical-images/titleview-small.png "Título do controle deslizanteVer")](hierarchical-images/titleview-large.png#lightbox "Título do controle deslizanteVer")

> [!IMPORTANT]
> Muitas visualizações não aparecerão na barra de navegação a menos [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) que o tamanho da exibição seja especificado com as propriedades e. Alternativamente, a vista pode [`StackLayout`](xref:Xamarin.Forms.StackLayout) ser [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) envolta em um com as propriedades definidas [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) para valores apropriados.

Observe que, [`Layout`](xref:Xamarin.Forms.Layout) como a classe [`View`](xref:Xamarin.Forms.View) deriva [`TitleView`](xref:Xamarin.Forms.NavigationPage.TitleViewProperty) da classe, a propriedade anexada pode ser definida para exibir uma classe de layout que contenha várias visualizações. No iOS e na UWP (Plataforma Universal do Windows), não é possível alterar a altura da barra de navegação e, portanto, será feito um recorte se o modo de exibição na barra de navegação for maior que o tamanho padrão da barra de navegação. No entanto, no Android, a altura da [`NavigationPage.BarHeight`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat.NavigationPage.BarHeightProperty) barra de navegação `double` pode ser alterada definindo a propriedade vinculável para uma que represente a nova altura. Para obter mais informações, confira [Definindo a altura da barra de navegação em uma NavigationPage](~/xamarin-forms/platform/android/navigationpage-bar-height.md).

Como alternativa, é possível sugerir uma barra de navegação estendida colocando parte do conteúdo na barra de navegação e parte em um modo de exibição na parte superior do conteúdo da página, cuja cor deve corresponder à da barra de navegação. Além disso, no iOS, a linha e a sombra do separador que [`NavigationPage.HideNavigationBarSeparator`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage.HideNavigationBarSeparatorProperty) está na `true`parte inferior da barra de navegação podem ser removidas definindo a propriedade vinculável para . Para obter mais informações, confira [Ocultando o separador da barra de navegação em uma NavigationPage](~/xamarin-forms/platform/ios/navigation-bar-separator.md).

> [!NOTE]
> As [`BackButtonTitle`](xref:Xamarin.Forms.NavigationPage.BackButtonTitleProperty) [`Title`](xref:Xamarin.Forms.Page.Title)propriedades [`TitleIcon`](xref:Xamarin.Forms.NavigationPage.TitleIconProperty)e [`TitleView`](xref:Xamarin.Forms.NavigationPage.TitleViewProperty) propriedades podem definir valores que ocupam espaço na barra de navegação. Embora o tamanho da barra de navegação varie de acordo com o tamanho da tela e a plataforma, definir todas essas propriedades causará conflitos devido à limitação do espaço disponível. Em vez de tentar usar uma combinação dessas propriedades, você provavelmente concluirá que é mais fácil obter o design desejado da barra de navegação definindo apenas a propriedade `TitleView`.

### <a name="limitations"></a>Limitações

Há uma série de limitações a serem [`View`](xref:Xamarin.Forms.View) atentas ao [`NavigationPage`](xref:Xamarin.Forms.NavigationPage)exibir um na barra de navegação de a:

- No iOS, modos de exibição colocados na barra de navegação de um `NavigationPage` poderão aparecer em uma posição diferente se títulos grandes estiverem habilitados. Para obter mais informações sobre a habilitação de títulos grandes, confira [Exibindo títulos grandes](~/xamarin-forms/platform/ios/page-large-title.md).
- No Android, só é possível colocar modos de exibição na barra de navegação de um `NavigationPage` em aplicativos que usam a compatibilidade de aplicativos.
- Não é recomendável colocar vistas grandes e [`ListView`](xref:Xamarin.Forms.ListView) complexas, como e [`TableView`](xref:Xamarin.Forms.TableView), `NavigationPage`na barra de navegação de a .

## <a name="related-links"></a>Links relacionados

- [Navegação de página](https://developer.xamarin.com/r/xamarin-forms/book/chapter24.pdf)
- [Hierárquica (amostra)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-hierarchical)
- [PassingData (amostra)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-passingdata)
- [LoginFlow (amostra)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-loginflow)
- [TitleView (amostra)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-titleview)
- [Vídeo sobre como criar um fluxo de tela de entrada no Xamarin.Forms](https://www.youtube.com/watch?v=qKQ7pyyG1fo)
- [NavigationPage](xref:Xamarin.Forms.NavigationPage)
