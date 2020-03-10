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
ms.sourcegitcommit: eedc6032eb5328115cb0d99ca9c8de48be40b6fa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/07/2020
ms.locfileid: "78915673"
---
# <a name="hierarchical-navigation"></a>Navegação hierárquica

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-hierarchical)

_A classe NavigationPage fornece uma experiência de navegação hierárquica em que o usuário é capaz de navegar pelas páginas, encaminhamentos e para trás, conforme desejado. A classe implementa a navegação como uma pilha UEPS (último a entrar, primeiro a sair) de objetos de página. Este artigo demonstra como usar a classe NavigationPage para realizar a navegação em uma pilha de páginas._

Para ir de uma página para outra, um aplicativo enviará por push uma nova página para a pilha de navegação, na qual ela se tornará a página ativa, conforme mostrado no diagrama a seguir:

![](hierarchical-images/pushing.png "Pushing a Page to the Navigation Stack")

Para retornar à página anterior, o aplicativo removerá a página atual da pilha de navegação e, em seguida, a nova página de nível superior se tornará a página ativa, conforme mostrado no diagrama a seguir:

![](hierarchical-images/popping.png "Popping a Page from the Navigation Stack")

Os métodos de navegação são expostos pela propriedade [`Navigation`](xref:Xamarin.Forms.NavigableElement.Navigation) em qualquer tipo derivado de [`Page`](xref:Xamarin.Forms.Page). Esses métodos possibilitam enviar páginas por push para a pilha de navegação, remover páginas da pilha de navegação e executar a manipulação da pilha.

<a name="Performing_Navigation" />

## <a name="performing-navigation"></a>Executando a navegação

Na barra de navegação hierárquica, a classe [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) é usada para navegar por meio de uma pilha de objetos [`ContentPage`](xref:Xamarin.Forms.ContentPage). As capturas de tela a seguir mostram os principais componentes de `NavigationPage` em cada plataforma:

![](hierarchical-images/navigationpage-components.png "NavigationPage Components")

O layout de um [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) depende da plataforma:

- No iOS, há uma barra de navegação na parte superior da página que exibe um título e que tem um botão *Voltar* que leva à página anterior.
- No Android, há uma barra de navegação na parte superior da página que exibe um título, um ícone e um botão *Voltar* que leva à página anterior. O ícone é definido no atributo `[Activity]` que decora a classe `MainActivity` no projeto específico da plataforma Android.
- Na Plataforma Universal do Windows, há uma barra de navegação na parte superior da página que exibe um título.

Em todas as plataformas, o valor da propriedade [`Page.Title`](xref:Xamarin.Forms.Page.Title) será exibido como o título da página.

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

Isso faz com que a instância de [`ContentPage`](xref:Xamarin.Forms.ContentPage) de `Page1Xaml` seja enviada por push para a pilha de navegação, onde se torna a página ativa e a página raiz do aplicativo. Isso é mostrado nas capturas de tela a seguir:

![](hierarchical-images/mainpage.png "Root Page of Navigation Stack")

> [!NOTE]
> A propriedade [`RootPage`](xref:Xamarin.Forms.NavigationPage.RootPage) de uma instância de [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) fornece acesso à primeira página na pilha de navegação.

### <a name="pushing-pages-to-the-navigation-stack"></a>Enviando páginas por push para a pilha de navegação

Para navegar até `Page2Xaml`, é necessário invocar o método [`PushAsync`](xref:Xamarin.Forms.NavigationPage.PushAsync*) na propriedade [`Navigation`](xref:Xamarin.Forms.NavigableElement.Navigation) da página atual, conforme demonstrado no exemplo de código a seguir:

```csharp
async void OnNextPageButtonClicked (object sender, EventArgs e)
{
  await Navigation.PushAsync (new Page2Xaml ());
}
```

Isso faz com que a instância `Page2Xaml` seja enviada por push para a pilha de navegação, em que ele se torna a página ativa. Isso é mostrado nas capturas de tela a seguir:

![](hierarchical-images/secondpage.png "Page Pushed onto Navigation Stack")

Quando o método [`PushAsync`](xref:Xamarin.Forms.NavigationPage.PushAsync*) é invocado, os seguintes eventos ocorrem:

- A página que chama `PushAsync` tem sua substituição [`OnDisappearing`](xref:Xamarin.Forms.Page.OnDisappearing) invocada.
- A página para a qual o usuário está navegando tem sua substituição [`OnAppearing`](xref:Xamarin.Forms.Page.OnAppearing) invocada.
- A tarefa `PushAsync` é concluída.

No entanto, a ordem exata em que esses eventos ocorrem depende da plataforma. Para obter mais informações, confira o [Capítulo 24](https://developer.xamarin.com/r/xamarin-forms/book/chapter24.pdf) do livro de Charles Petzold sobre Xamarin.Forms.

> [!NOTE]
> Chamadas para as substituições [`OnDisappearing`](xref:Xamarin.Forms.Page.OnDisappearing) e [`OnAppearing`](xref:Xamarin.Forms.Page.OnAppearing) não podem ser tratadas como indicações garantidas de navegação de página. Por exemplo, no iOS, a substituição `OnDisappearing` é chamada na página ativa quando o aplicativo é encerrado.

### <a name="popping-pages-from-the-navigation-stack"></a>Removendo páginas da pilha de navegação

A página ativa pode ser removida como o item mais recente da pilha de navegação pressionando o botão *Voltar* no dispositivo, independentemente de este ser um botão físico no dispositivo ou um botão na tela.

Para retornar programaticamente à página original, a instância `Page2Xaml` deve invocar o método [`PopAsync`](xref:Xamarin.Forms.NavigationPage.PopAsync), conforme demonstrado no exemplo de código a seguir:

```csharp
async void OnPreviousPageButtonClicked (object sender, EventArgs e)
{
  await Navigation.PopAsync ();
}
```

Isso faz com que a instância `Page2Xaml` seja removida da pilha de navegação, com a nova página de nível superior tornando-se a página ativa. Quando o método [`PopAsync`](xref:Xamarin.Forms.NavigationPage.PopAsync) é invocado, os seguintes eventos ocorrem:

- A página que chama `PopAsync` tem sua substituição [`OnDisappearing`](xref:Xamarin.Forms.Page.OnDisappearing) invocada.
- A página para a qual o usuário está voltando tem sua substituição [`OnAppearing`](xref:Xamarin.Forms.Page.OnAppearing) invocada.
- A tarefa `PopAsync` é retornada.

No entanto, a ordem exata em que esses eventos ocorrem depende da plataforma. Para obter mais informações, confira o [Capítulo 24](https://developer.xamarin.com/r/xamarin-forms/book/chapter24.pdf) do livro de Charles Petzold sobre Xamarin.Forms.

Assim como os métodos [`PushAsync`](xref:Xamarin.Forms.NavigationPage.PushAsync*) e [`PopAsync`](xref:Xamarin.Forms.NavigationPage.PopAsync), a propriedade [`Navigation`](xref:Xamarin.Forms.NavigableElement.Navigation) de cada página também fornece um método [`PopToRootAsync`](xref:Xamarin.Forms.NavigationPage.PopToRootAsync), que é mostrado no exemplo de código a seguir:

```csharp
async void OnRootPageButtonClicked (object sender, EventArgs e)
{
  await Navigation.PopToRootAsync ();
}
```

Esse método retira todas a páginas, exceto pela [`Page`](xref:Xamarin.Forms.Page) raiz, da pilha de navegação, tornando assim a página raiz do aplicativo a página ativa.

### <a name="animating-page-transitions"></a>Animando transições de página

A propriedade [`Navigation`](xref:Xamarin.Forms.NavigableElement.Navigation) de cada página também fornece métodos de envio por push e remoção substituídos que incluem um parâmetro `boolean` que controla se deve ser exibida uma animação de página durante a navegação, conforme mostrado no código de exemplo a seguir:

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

Às vezes, é necessário que uma página transmita dados para outra página durante a navegação. Duas técnicas para fazer isso são transmitir dados por meio de um construtor de página e definir o [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) como os dados. Descreveremos cada técnica separadamente.

### <a name="passing-data-through-a-page-constructor"></a>Transmitindo dados por meio de um construtor de página

A técnica mais simples para transmitir dados para outra página durante a navegação é por meio de um parâmetro de construtor de página, que é mostrado no exemplo de código a seguir:

```csharp
public App ()
{
  MainPage = new NavigationPage (new MainPage (DateTime.Now.ToString ("u")));
}
```

Esse código cria uma instância de `MainPage`, transmitindo a data e a hora atuais no formato ISO8601, que é encapsulada em uma instância de [`NavigationPage`](xref:Xamarin.Forms.NavigationPage).

A instância de `MainPage` recebe os dados por meio de um parâmetro de construtor, conforme mostrado no exemplo de código a seguir:

```csharp
public MainPage (string date)
{
  InitializeComponent ();
  dateLabel.Text = date;
}
```

Os dados são exibidos na página definindo a propriedade [`Label.Text`](xref:Xamarin.Forms.Label.Text), conforme mostrado nas capturas de tela seguir:

![](hierarchical-images/passing-data-constructor.png "Data Passed Through a Page Constructor")

### <a name="passing-data-through-a-bindingcontext"></a>Transmitindo dados por meio de um BindingContext

Outra abordagem para transmitir dados para outra página durante a navegação é definir o [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) da nova página como os dados, conforme mostrado no exemplo de código a seguir:

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

Esse código define o [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) da instância de `SecondPage` como a instância de `Contact` e, em seguida, navega para o `SecondPage`.

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

Em seguida, os dados são exibidos na página por uma série de controles de [`Label`](xref:Xamarin.Forms.Label), conforme mostrado nas capturas de tela seguir:

![](hierarchical-images/passing-data-bindingcontext.png "Data Passed Through a BindingContext")

Para obter mais informações sobre vinculação de dados, veja [Noções básicas de vinculação de dados](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md).

<a name="Manipulating_the_Navigation_Stack" />

## <a name="manipulating-the-navigation-stack"></a>Manipulando a pilha de navegação

A propriedade [`Navigation`](xref:Xamarin.Forms.NavigableElement.Navigation) expõe uma propriedade [`NavigationStack`](xref:Xamarin.Forms.INavigation.NavigationStack) da qual as páginas na pilha de navegação podem ser obtidas. Enquanto o Xamarin.Forms mantém o acesso à pilha de navegação, a propriedade `Navigation` fornece os métodos [`InsertPageBefore`](xref:Xamarin.Forms.INavigation.InsertPageBefore*) e [`RemovePage`](xref:Xamarin.Forms.INavigation.RemovePage*) para manipular a pilha inserindo ou removendo páginas.

O método [`InsertPageBefore`](xref:Xamarin.Forms.INavigation.InsertPageBefore*) insere uma página especificada na pilha de navegação antes de uma página existente, conforme mostrado no diagrama a seguir:

![](hierarchical-images/insert-page-before.png "Inserting a Page in the Navigation Stack")

O método [`RemovePage`](xref:Xamarin.Forms.INavigation.RemovePage*) remove a página especificada da pilha de navegação, conforme mostrado no diagrama a seguir:

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

Desde que as credenciais do usuário estejam corretas, a instância `MainPage` será inserida na pilha de navegação antes da página atual. Em seguida, o método [`PopAsync`](xref:Xamarin.Forms.NavigationPage.PopAsync) remove a página atual da pilha de navegação, com a instância de `MainPage` se tornando a página ativa.

## <a name="displaying-views-in-the-navigation-bar"></a>Exibindo modos de exibição na barra de navegação

Qualquer [`View`](xref:Xamarin.Forms.View) da Xamarin.Forms pode ser exibido na barra de navegação de uma [`NavigationPage`](xref:Xamarin.Forms.NavigationPage). Isso é feito definindo a propriedade anexada [`NavigationPage.TitleView`](xref:Xamarin.Forms.NavigationPage.TitleViewProperty) como um `View`. Essa propriedade anexada pode ser definida em qualquer [`Page`](xref:Xamarin.Forms.Page) e, quando a `Page` é enviada por push a uma `NavigationPage`, a `NavigationPage` respeitará o valor da propriedade.

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

Isso faz com que um [`Slider`](xref:Xamarin.Forms.Slider) seja exibido na barra de navegação na [`NavigationPage`](xref:Xamarin.Forms.NavigationPage):

[![TitleView do controle deslizante](hierarchical-images/titleview-small.png "TitleView do controle deslizante")](hierarchical-images/titleview-large.png#lightbox "TitleView do controle deslizante")

> [!IMPORTANT]
> Muitos modos de exibição não aparecerão na barra de navegação a menos que o tamanho do modo de exibição seja especificado com as propriedades [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) e [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest). Como alternativa, o modo de exibição pode ser encapsulado em um [`StackLayout`](xref:Xamarin.Forms.StackLayout) com as propriedades [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) e [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) definidas como os valores apropriados.

Observe que, como a classe [`Layout`](xref:Xamarin.Forms.Layout) é derivada da classe [`View`](xref:Xamarin.Forms.View), a propriedade anexada [`TitleView`](xref:Xamarin.Forms.NavigationPage.TitleViewProperty) pode ser definida para exibir uma classe de layout que contém vários modos de exibição. No iOS e na UWP (Plataforma Universal do Windows), não é possível alterar a altura da barra de navegação e, portanto, será feito um recorte se o modo de exibição na barra de navegação for maior que o tamanho padrão da barra de navegação. No entanto, no Android, a altura da barra de navegação pode ser alterada definindo a propriedade vinculável [`NavigationPage.BarHeight`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat.NavigationPage.BarHeightProperty) como um `double` que representa a nova altura. Para obter mais informações, confira [Definindo a altura da barra de navegação em uma NavigationPage](~/xamarin-forms/platform/android/navigationpage-bar-height.md).

Como alternativa, é possível sugerir uma barra de navegação estendida colocando parte do conteúdo na barra de navegação e parte em um modo de exibição na parte superior do conteúdo da página, cuja cor deve corresponder à da barra de navegação. Além disso, no iOS, a linha separadora e sombra na parte inferior da barra de navegação podem ser removidas definindo a propriedade associável [`NavigationPage.HideNavigationBarSeparator`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage.HideNavigationBarSeparatorProperty) como `true`. Para obter mais informações, confira [Ocultando o separador da barra de navegação em uma NavigationPage](~/xamarin-forms/platform/ios/navigation-bar-separator.md).

> [!NOTE]
> As propriedades [`BackButtonTitle`](xref:Xamarin.Forms.NavigationPage.BackButtonTitleProperty), [`Title`](xref:Xamarin.Forms.Page.Title), [`TitleIcon`](xref:Xamarin.Forms.NavigationPage.TitleIconProperty) e [`TitleView`](xref:Xamarin.Forms.NavigationPage.TitleViewProperty) podem definir valores que ocupam espaço na barra de navegação. Embora o tamanho da barra de navegação varie de acordo com o tamanho da tela e a plataforma, definir todas essas propriedades causará conflitos devido à limitação do espaço disponível. Em vez de tentar usar uma combinação dessas propriedades, você provavelmente concluirá que é mais fácil obter o design desejado da barra de navegação definindo apenas a propriedade `TitleView`.

### <a name="limitations"></a>Limitações

É necessário estar ciente de uma série de limitações ao exibir um [`View`](xref:Xamarin.Forms.View) na barra de navegação de uma [`NavigationPage`](xref:Xamarin.Forms.NavigationPage):

- No iOS, modos de exibição colocados na barra de navegação de um `NavigationPage` poderão aparecer em uma posição diferente se títulos grandes estiverem habilitados. Para obter mais informações sobre a habilitação de títulos grandes, confira [Exibindo títulos grandes](~/xamarin-forms/platform/ios/page-large-title.md).
- No Android, só é possível colocar modos de exibição na barra de navegação de um `NavigationPage` em aplicativos que usam a compatibilidade de aplicativos.
- Não é recomendável colocar modos de exibição grandes e complexos, como [`ListView`](xref:Xamarin.Forms.ListView) e [`TableView`](xref:Xamarin.Forms.TableView), na barra de navegação de uma `NavigationPage`.

## <a name="related-links"></a>Links relacionados

- [Navegação da página](https://developer.xamarin.com/r/xamarin-forms/book/chapter24.pdf)
- [Hierárquica (amostra)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-hierarchical)
- [PassingData (amostra)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-passingdata)
- [LoginFlow (amostra)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-loginflow)
- [TitleView (amostra)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-titleview)
- [Vídeo sobre como criar um fluxo de tela de entrada no Xamarin.Forms](https://www.youtube.com/watch?v=qKQ7pyyG1fo)
- [NavigationPage](xref:Xamarin.Forms.NavigationPage)
