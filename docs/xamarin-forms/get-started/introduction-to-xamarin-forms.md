---
title: Uma Introdução ao Xamarin.Forms
description: O Xamarin.Forms é uma abstração de kit de ferramentas de interface do usuário com suporte nativo multiplataforma que permite aos desenvolvedores criar com facilidade interfaces do usuário que podem ser compartilhadas entre Android, iOS e a Plataforma Universal do Windows. As interfaces do usuário são renderizadas usando controles nativos da plataforma de destino, permitindo que os aplicativos Xamarin.Forms mantenham a aparência apropriada para cada plataforma. Este artigo fornece uma introdução ao Xamarin.Forms e mostra como começar a escrever aplicativos com ele.
ms.prod: xamarin
ms.assetid: f619595f-3ee7-439b-a1bc-d13e5106e6e9
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/02/2016
ms.openlocfilehash: 9f7c9d1b410d9d1d699644148903fdc6cfeec4fd
ms.sourcegitcommit: 1561c8022c3585655229a869d9ef3510bf83f00a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/27/2018
---
# <a name="an-introduction-to-xamarinforms"></a>Uma Introdução ao Xamarin.Forms

_O Xamarin.Forms é uma abstração de kit de ferramentas de interface do usuário com suporte nativo multiplataforma que permite aos desenvolvedores criar com facilidade interfaces do usuário que podem ser compartilhadas entre Android, iOS, Windows e a Plataforma Universal do Windows. As interfaces do usuário são renderizadas usando controles nativos da plataforma de destino, permitindo que os aplicativos Xamarin.Forms mantenham a aparência apropriada para cada plataforma. Este artigo fornece uma introdução ao Xamarin.Forms e mostra como começar a escrever aplicativos com ele._

<a name="Overview" />

## <a name="overview"></a>Visão geral

Xamarin.Forms é uma estrutura que permite aos desenvolvedores criar interfaces do usuário de plataforma cruzada rapidamente. Ele fornece sua própria abstração de interface do usuário que será renderizada usando controles nativos no iOS, Android, ou a UWP (Plataforma Universal do Windows). Isso significa que os aplicativos podem compartilhar uma grande parte do seu código de interface do usuário e ainda manter a aparência nativa da plataforma de destino.

O Xamarin.Forms permite prototipagem rápida de aplicativos que podem evoluir ao longo do tempo para aplicativos complexos. Já que os aplicativos Xamarin.Forms são aplicativos nativos, eles não têm as limitações de outros conjuntos de ferramentas como área restrita de navegador, APIs limitadas ou baixo desempenho. Aplicativos escritos usando Xamarin.Forms são capazes de utilizar qualquer um dos recursos da API ou da plataforma subjacente, assim como (mas não se limitando a) CoreMotion, PassKit e StoreKit no iOS, NFC e Google Play Services no Android e Blocos no Windows. Além disso, é possível criar aplicativos que terão partes da sua interface do usuário criadas com Xamarin.Forms, enquanto outras partes serão criadas usando o kit de ferramentas da interface do usuário nativa.

Aplicativos Xamarin.Forms são arquitetados do mesmo modo que aplicativos tradicionais de plataforma cruzada. A abordagem mais comum é usar [bibliotecas portáteis](~/cross-platform/app-fundamentals/pcl.md) ou [projetos compartilhados](~/cross-platform/app-fundamentals/shared-projects.md) para abrigar o código compartilhado e criar aplicativos específicos da plataforma que consumirão o código compartilhado.

Existem duas técnicas para criar interfaces do usuário no Xamarin.Forms. A primeira técnica é criar interfaces do usuário inteiramente com código-fonte C#. A segunda técnica é usar *linguagem XAML*, uma linguagem de marcação declarativa que é usada para descrever interfaces do usuário. Para obter mais informações sobre como trabalhar com XAML, veja [Noções básicas de XAML](~/xamarin-forms/xaml/xaml-basics/index.md).

Este artigo aborda os conceitos básicos da estrutura do Xamarin.Forms e abrange os seguintes tópicos:

-  [Como examinar um aplicativo Xamarin.Forms](#Examining_A_Xamarin.Forms_Application).
-  [Como páginas e controles do Xamarin.Forms são usados](#Views_and_Layouts).
-  [Como usar e exibir uma lista de dados](#Lists_in_Xamarin.Forms).
-  [Como configurar a vinculação de dados](#Data_Binding).
-  [Como navegar entre páginas](#Navigation).
-  [Próximas etapas](#Next_Steps).

<a name="Examining_A_Xamarin_Forms_Application" />

### <a name="examining-a-xamarinforms-application"></a>Como examinar um aplicativo Xamarin.Forms

No Visual Studio para Mac e Visual Studio, o modelo de aplicativo do Xamarin.Forms padrão cria a solução do Xamarin.Forms mais simples possível, que exibe o texto para o usuário. Se você executar o aplicativo, ele deverá ser semelhante às capturas de tela a seguir:

[![](introduction-to-xamarin-forms-images/image05-sml.png "Aplicativo Xamarin.Forms padrão")](introduction-to-xamarin-forms-images/image05.png#lightbox "Aplicativo Xamarin.Forms padrão")

Cada tela nas capturas de tela corresponde a uma *página* no Xamarin.Forms. Um [`Page`](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/) representa uma *atividade* no Android, um *controlador de exibição* no iOS ou uma *página* na UWP (Plataforma Universal do Windows). O exemplo nas capturas de tela acima instancia um objeto [`ContentPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/) e usa isso para exibir um [`Label`](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/).

Para maximizar a reutilização do código de inicialização, os aplicativos Xamarin.Forms têm uma única classe chamada `App`, que é responsável por instanciar a primeira [`Page`](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/) que será exibida. Um exemplo da classe `App` pode ser visto no código a seguir:

```csharp
public class App : Application
{
  public App ()
  {
    MainPage = new ContentPage {
      Content =  new Label
      {
        Text = "Hello, Forms !",
        VerticalOptions = LayoutOptions.CenterAndExpand,
        HorizontalOptions = LayoutOptions.CenterAndExpand,
      }
    };
  }
}
```

Esse código instancia um novo objeto [`ContentPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/) que exibirá um único [`Label`](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/), centralizado tanto verticalmente quanto horizontalmente na página.

<a name="Launching_the_Initial_Xamarin_Forms_Page_on_Each_Platform" />

### <a name="launching-the-initial-xamarinforms-page-on-each-platform"></a>Como iniciar a página inicial do Xamarin.Forms em cada plataforma

Usar este [`Page`](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/) dentro de um aplicativo, cada aplicativo da plataforma deve inicializar a estrutura do Xamarin.Forms e fornecer uma instância do [`ContentPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/) enquanto ele está sendo inicializado. Essa etapa de inicialização varia de uma plataforma para outra e é discutida nas seções a seguir.

<a name="Launching_in_iOS" />

#### <a name="ios"></a>iOS

Para inicializar a página inicial do Xamarin.Forms no iOS, o projeto de plataforma inclui a classe `AppDelegate` que herda da classe `Xamarin.Forms.Platform.iOS.FormsApplicationDelegate`, conforme demonstrado no exemplo de código a seguir:

```csharp
[Register("AppDelegate")]
public partial class AppDelegate : global::Xamarin.Forms.Platform.iOS.FormsApplicationDelegate
{
    public override bool FinishedLaunching(UIApplication app, NSDictionary options)
    {
      global::Xamarin.Forms.Forms.Init ();
      LoadApplication (new App ());
      return base.FinishedLaunching (app, options);
    }
}
```

A substituição `FinishedLoading` inicializa a estrutura do Xamarin.Forms ao chamar o método `Init`. Isso faz a implementação específica do iOS do Xamarin.Forms ser carregada no aplicativo antes que o controlador de exibição de raiz seja definido pela chamada para o método `LoadApplication`.

<a name="Launching_in_Android" />

#### <a name="android"></a>Android

Para inicializar a página inicial do Xamarin.Forms no Android, o projeto de plataforma inclui código que cria uma `Activity` com o atributo `MainLauncher`, com a atividade herdando da classe `FormsApplicationActivity`, conforme demonstrado no exemplo de código a seguir:

```csharp
namespace HelloXamarinFormsWorld.Android
{
    [Activity(Label = "HelloXamarinFormsWorld", MainLauncher = true,
        ConfigurationChanges = ConfigChanges.ScreenSize | ConfigChanges.Orientation)]
    public class MainActivity : global::Xamarin.Forms.Platform.Android.FormsApplicationActivity
    {
        protected override void OnCreate(Bundle bundle)
        {
            base.OnCreate(bundle);
            Xamarin.Forms.Forms.Init(this, bundle);
            LoadApplication (new App ());
        }
    }
}
```

A substituição `OnCreate` inicializa a estrutura do Xamarin.Forms ao chamar o método `Init`. Isso faz a implementação específica do Android do Xamarin.Forms ser carregada no aplicativo antes de o aplicativo Xamarin.Forms ser carregado.

#### <a name="universal-windows-platform"></a>Plataforma Universal do Windows

Em aplicativos UWP (Plataforma Universal do Windows), o método `Init` que inicializa a estrutura Xamarin.Forms é chamado da classe `App`:

```csharp
Xamarin.Forms.Forms.Init (e);

if (e.PreviousExecutionState == ApplicationExecutionState.Terminated)
{
  ...
}
```

Isso faz a implementação específica de UWP do Xamarin.Forms ser carregada no aplicativo. A página inicial do Xamarin.Forms é iniciada pela classe `MainPage`, conforme demonstrado no exemplo de código a seguir:

```csharp
public partial class MainPage
{
    public MainPage()
    {
      this.InitializeComponent();
      this.LoadApplication(new HelloXamarinFormsWorld.App());
    }
}
```

O aplicativo Xamarin.Forms é carregado com o método `LoadApplication`.

<a name="Views_and_Layouts" />

### <a name="views-and-layouts"></a>Layouts e exibições

Há quatro grupos de controle principais usados para criar a interface do usuário de um aplicativo Xamarin.Forms.

1. **Páginas** – as páginas do Xamarin.Forms representam telas de aplicativos móveis de plataforma cruzada. Para saber mais sobre páginas, consulte [Xamarin.Forms Pages](~/xamarin-forms/user-interface/controls/pages.md) (Páginas do Xamarin.Forms).
1. **Layouts** – os layouts do Xamarin.Forms são contêineres usados para compor exibições em estruturas lógicas. Para saber mais sobre layouts, consulte [Xamarin.Forms Layouts](~/xamarin-forms/user-interface/controls/layouts.md) (Layouts do Xamarin.Forms).
1. **Exibições** – as exibições do Xamarin.Forms são os controles exibidos na interface do usuário, como rótulos, botões e caixas de entrada de texto. Para saber mais sobre exibições, consulte [Xamarin.Forms Views](~/xamarin-forms/user-interface/controls/views.md) (Exibições do Xamarin.Forms).
1. **Células** – as células do Xamarin.Forms são elementos especializados usados para itens em uma lista e descrevem como cada item em uma lista deve ser desenhado. Para saber mais sobre células, consulte [Xamarin.Forms Cells](~/xamarin-forms/user-interface/controls/cells.md) (Células do Xamarin.Forms).

No tempo de execução, cada controle será mapeado para seu equivalente nativo, que é o que será renderizado.

Os controles são hospedados dentro de um layout. A classe [`StackLayout`](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/), que implementa um layout usado com frequência, será agora examinada.

<a name="StackLayout" />

#### <a name="stacklayout"></a>StackLayout

O [`StackLayout`](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/) simplifica o desenvolvimento de aplicativos de plataforma cruzada organizando automaticamente os controles na tela, independentemente do tamanho dela. Os elementos filho são posicionados um após o outro, horizontalmente ou verticalmente, na ordem em que foram adicionados. A quantidade de espaço que o `StackLayout` usará dependerá de como as propriedades [`HorizontalOptions`](https://developer.xamarin.com/api/property/Xamarin.Forms.View.HorizontalOptions/) e [`VerticalOptions`](https://developer.xamarin.com/api/property/Xamarin.Forms.View.HorizontalOptions/) forem definidas, mas por padrão o `StackLayout` tentará usar a tela inteira.

O código XAML a seguir mostra um exemplo de como usar um [`StackLayout`](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/) para organizar três controles [`Label`](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/):

```xaml
<?xml version="1.0" encoding="utf-8" ?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml" x:Class="HelloXamarinFormsWorldXaml.StackLayoutExample1" Padding="20">
  <StackLayout Spacing="10">
    <Label Text="Stop" BackgroundColor="Red" Font="20" />
    <Label Text="Slow down" BackgroundColor="Yellow" Font="20" />
    <Label Text="Go" BackgroundColor="Green" Font="20" />
  </StackLayout>
</ContentPage>
```

O código C# equivalente é mostrado no exemplo de código a seguir:

```csharp
public class StackLayoutExample : ContentPage
{
    public StackLayoutExample()
    {
        Padding = new Thickness(20);
        var red = new Label
        {
            Text = "Stop", BackgroundColor = Color.Red, FontSize = 20
        };
        var yellow = new Label
        {
            Text = "Slow down", BackgroundColor = Color.Yellow, FontSize = 20
        };
        var green = new Label
        {
            Text = "Go", BackgroundColor = Color.Green, FontSize = 20
        };

        Content = new StackLayout
        {
            Spacing = 10,
            Children = { red, yellow, green }
        };
    }
}
```

Por padrão, o [`StackLayout`](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/) assume uma orientação vertical conforme mostrado nas capturas de tela a seguir:

[![](introduction-to-xamarin-forms-images/image09-sml.png "StackLayout vertical")](introduction-to-xamarin-forms-images/image09.png#lightbox "StackLayout vertical")

A orientação do [`StackLayout`](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/) pode ser alterado para uma orientação horizontal, conforme demonstrado no exemplo de código XAML a seguir:

```xaml
<?xml version="1.0" encoding="utf-8" ?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml" x:Class="HelloXamarinFormsWorldXaml.StackLayoutExample2" Padding="20">
  <StackLayout Spacing="10" VerticalOptions="End" Orientation="Horizontal" HorizontalOptions="Start">
    <Label Text="Stop" BackgroundColor="Red" Font="20" />
    <Label Text="Slow down" BackgroundColor="Yellow" Font="20" />
    <Label Text="Go" BackgroundColor="Green" Font="20" />
  </StackLayout>
</ContentPage>
```

O código C# equivalente é mostrado no exemplo de código a seguir:

```csharp
public class StackLayoutExample: ContentPage
{
    public StackLayoutExample()
    {
        // Code that creates labels removed for clarity
        Content = new StackLayout
        {
            Spacing = 10,
            VerticalOptions = LayoutOptions.End,
            Orientation = StackOrientation.Horizontal,
            HorizontalOptions = LayoutOptions.Start,
            Children = { red, yellow, green }
        };
    }
}
```

As capturas de tela a seguir mostram o layout resultante:

[![](introduction-to-xamarin-forms-images/image10-sml.png "StackLayout horizontal")](introduction-to-xamarin-forms-images/image10.png#lightbox "StackLayout horizontal")

O tamanho dos controles pode ser definido por meio das propriedades `HeightRequest` e `WidthRequest`, conforme demonstrado no exemplo de código XAML a seguir:

```xaml
<?xml version="1.0" encoding="utf-8" ?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml" x:Class="HelloXamarinFormsWorldXaml.StackLayoutExample3" Padding="20">
  <StackLayout Spacing="10" VerticalOptions="End" Orientation="Horizontal" HorizontalOptions="Start">
    <Label Text="Stop" BackgroundColor="Red" Font="20" WidthRequest="100" />
    <Label Text="Slow down" BackgroundColor="Yellow" Font="20" WidthRequest="100" />
    <Label Text="Go" BackgroundColor="Green" Font="20" WidthRequest="200" />
  </StackLayout>
</ContentPage>
```

O código C# equivalente é mostrado no exemplo de código a seguir:

```csharp
var red = new Label
{
    Text = "Stop", BackgroundColor = Color.Red, FontSize = 20, WidthRequest = 100
};
var yellow = new Label
{
    Text = "Slow down", BackgroundColor = Color.Yellow, FontSize = 20, WidthRequest = 100
};
var green = new Label
{
    Text = "Go", BackgroundColor = Color.Green, FontSize = 20, WidthRequest = 200
};

Content = new StackLayout
{
    Spacing = 10,
    VerticalOptions = LayoutOptions.End,
    Orientation = StackOrientation.Horizontal,
    HorizontalOptions = LayoutOptions.Start,
    Children = { red, yellow, green }
};
```

As capturas de tela a seguir mostram o layout resultante:

[![](introduction-to-xamarin-forms-images/image11-sml.png "StackLayout horizontal com LayoutOptions")](introduction-to-xamarin-forms-images/image11.png#lightbox "StackLayout horizontal com LayoutOptions")

Para obter mais informações sobre a classe [`StackLayout`](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/), veja [StackLayout](~/xamarin-forms/user-interface/layouts/stack-layout.md).

<a name="Lists_in_Xamarin_Forms" />

## <a name="lists-in-xamarinforms"></a>Listas em Xamarin.Forms

O controle [`ListView`](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) é responsável por exibir uma coleção de itens na tela – cada item no `ListView` estará contido em uma única célula. Por padrão, um `ListView` usará o modelo [`TextCell`](https://developer.xamarin.com/api/type/Xamarin.Forms.TextCell/) interna e renderizará uma única linha de texto.

O exemplo de código a seguir mostra um exemplo de [`ListView`](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) simples:

```csharp
var listView = new ListView
{
    RowHeight = 40
};
listView.ItemsSource = new string []
{
    "Buy pears", "Buy oranges", "Buy mangos", "Buy apples", "Buy bananas"
};
Content = new StackLayout
{
    VerticalOptions = LayoutOptions.FillAndExpand,
    Children = { listView }
};
```

A captura de tela a seguir mostra o [`ListView`](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) resultante:

 ![](introduction-to-xamarin-forms-images/image13.png "ListView")

Para obter mais informações sobre o controle [`ListView`](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/), veja [ListView](~/xamarin-forms/user-interface/listview/index.md).

<a name="Binding_to_a_Custom_Class" />

### <a name="binding-to-a-custom-class"></a>Associação a uma classe personalizada

O controle [`ListView`](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) também pode exibir os objetos personalizados usando o modelo [`TextCell`](https://developer.xamarin.com/api/type/Xamarin.Forms.TextCell/) padrão.

O exemplo de código a seguir mostra a classe `TodoItem`:

```csharp
public class TodoItem
{
    public string Name { get; set; }
    public bool Done { get; set; }
}
```

O controle [`ListView`](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) pode ser populado conforme demonstrado no exemplo de código a seguir:

```csharp
listView.ItemsSource = new TodoItem [] {
    new TodoItem { Name = "Buy pears" },
    new TodoItem { Name = "Buy oranges", Done=true} ,
    new TodoItem { Name = "Buy mangos" },
    new TodoItem { Name = "Buy apples", Done=true },
    new TodoItem { Name = "Buy bananas", Done=true }
};
```

Uma associação pode ser criada para definir qual propriedade `TodoItem` é exibida pelo [`ListView`](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/), conforme demonstrado no exemplo de código a seguir:

```csharp
listView.ItemTemplate = new DataTemplate(typeof(TextCell));
listView.ItemTemplate.SetBinding(TextCell.TextProperty, "Name");
```

Isso cria uma associação que especifica o caminho para a propriedade `TodoItem.Name` e resultará na captura de tela exibida anteriormente.

Para obter mais informações sobre a associação a uma classe personalizada, consulte [Fontes de Dados de ListView](~/xamarin-forms/user-interface/listview/data-and-databinding.md).

<a name="Selecting_an_Item_in_a_ListView" />

### <a name="selecting-an-item-in-a-listview"></a>Selecionar um Item em um ListView

Para responder a um usuário tocar em uma célula em um [`ListView`](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/), deve-se lidar com o evento [`ItemSelected`](https://developer.xamarin.com/api/event/Xamarin.Forms.ListView.ItemSelected/), conforme demonstrado no exemplo de código a seguir:

```csharp
listView.ItemSelected += async (sender, e) => {
    await DisplayAlert("Tapped!", e.SelectedItem + " was tapped.", "OK");
};
```

Quando contido em um [`NavigationPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/), o método [`PushAsync`](https://developer.xamarin.com/api/member/Xamarin.Forms.NavigationPage.PushAsync/p/Xamarin.Forms.Page/) pode ser usado para abrir uma nova página com navegação de retorno interna. O evento [`ItemSelected`](https://developer.xamarin.com/api/event/Xamarin.Forms.ListView.ItemSelected/) pode acessar o objeto que foi associado à célula por meio da propriedade [`e.SelectedItem`](https://developer.xamarin.com/api/property/Xamarin.Forms.SelectedItemChangedEventArgs.SelectedItem/), associá-lo a uma nova página e exibir a nova página usando `PushAsync`, conforme demonstrado no exemplo de código a seguir:

```csharp
listView.ItemSelected += async (sender, e) => {
    var todoItem = (TodoItem)e.SelectedItem;
    var todoPage = new TodoItemPage(todoItem); // so the new page shows correct data
    await Navigation.PushAsync(todoPage);
};
```

Cada plataforma implementa navegação de retorno interna de sua própria maneira. Para obter mais informações, veja [Navegação](#Navigation).

Para obter mais informações sobre a seleção [`ListView`](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/), veja [Interatividade do ListView](~/xamarin-forms/user-interface/listview/interactivity.md).

<a name="Customizing_the_appearance_of_a_cell" />

### <a name="customizing-the-appearance-of-a-cell"></a>Personalizando a aparência de uma célula

A aparência de célula pode ser personalizada subclassificando-se a classe [`ViewCell`](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewCell/) e definindo-se o tipo dessa classe para a propriedade [`ItemTemplate`](https://developer.xamarin.com/api/property/Xamarin.Forms.ItemsView%3CTVisual%3E.ItemTemplate/) do [`ListView`](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/).

A célula mostrada na seguinte captura de tela é composta de um controle [`Image`](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/) e dois controles [`Label`](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/):

 ![](introduction-to-xamarin-forms-images/image14.png "Aparência de célula personalizada ListView")

Para criar esse layout personalizado, a classe [`ViewCell`](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewCell/) deve ser uma subclasse, conforme demonstrado no exemplo de código a seguir:

```csharp
class EmployeeCell : ViewCell
{
    public EmployeeCell()
    {
        var image = new Image
        {
            HorizontalOptions = LayoutOptions.Start
        };
        image.SetBinding(Image.SourceProperty, new Binding("ImageUri"));
        image.WidthRequest = image.HeightRequest = 40;

        var nameLayout = CreateNameLayout();
        var viewLayout = new StackLayout()
        {
           Orientation = StackOrientation.Horizontal,
           Children = { image, nameLayout }
        };
        View = viewLayout;
    }

    static StackLayout CreateNameLayout()
    {
        var nameLabel = new Label
        {
            HorizontalOptions= LayoutOptions.FillAndExpand
        };
        nameLabel.SetBinding(Label.TextProperty, "DisplayName");

        var twitterLabel = new Label
        {
           HorizontalOptions = LayoutOptions.FillAndExpand,
           Font = Fonts.Twitter
        };
        twitterLabel.SetBinding(Label.TextProperty, "Twitter");

        var nameLayout = new StackLayout()
        {
           HorizontalOptions = LayoutOptions.StartAndExpand,
           Orientation = StackOrientation.Vertical,
           Children = { nameLabel, twitterLabel }
        };
        return nameLayout;
    }
}
```

O código realiza as tarefas a seguir:

-  Ele adiciona um controle [`Image`](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/) e associa-o à propriedade `ImageUri` do objeto `Employee`. Para obter mais informações sobre vinculação de dados, veja [Vinculação de dados](#Data_Binding).
-  Ele cria um [`StackLayout`](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/) com uma orientação vertical para manter os dois controles [`Label`](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/). Os controles `Label` estão associados às propriedades `DisplayName` e `Twitter` do objeto `Employee`.
-  Ele cria um [`StackLayout`](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/) que hospedará os [`Image`](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/) e `StackLayout` existentes. Ele organizará seus filhos usando uma orientação horizontal.

Após a célula personalizada ter sido criada, ela poderá ser consumida por um controle [`ListView`](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) encapsulando-o em um [`DataTemplate`](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/), conforme demonstrado no exemplo de código a seguir:

```csharp
List<Employee> myListOfEmployeeObjects = GetAListOfAllEmployees();
var listView = new ListView
{
    RowHeight = 40
};
listView.ItemsSource = myListOfEmployeeObjects;
listView.ItemTemplate = new DataTemplate(typeof(EmployeeCell));
```

Esse código fornecerá um `List` de `Employee` para o [`ListView`](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/). Cada célula será renderizada usando a classe `EmployeeCell`. O `ListView` passará o objeto `Employee` para o `EmployeeCell` como seu [`BindingContext`](https://developer.xamarin.com/api/property/Xamarin.Forms.BindableObject.BindingContext/).

Para obter mais informações sobre como personalizar a aparência da célula, consulte [Aparência da célula](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md).

<a name="Using_XAML_to_Create_and_Customize_A_List" />

### <a name="using-xaml-to-create-and-customize-a-list"></a>Como usar XAML para criar e personalizar uma lista

O equivalente em XAML ao [`ListView`](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) na seção anterior é demonstrado no exemplo de código a seguir:

```xaml
<?xml version="1.0" encoding="utf-8" ?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
             xmlns:local="clr-namespace:XamarinFormsXamlSample;assembly=XamarinFormsXamlSample"
             xmlns:constants="clr-namespace:XamarinFormsSample;assembly=XamarinFormsXamlSample"
             x:Class="XamarinFormsXamlSample.Views.EmployeeListPage"
             Title="Employee List">
  <ListView x:Name="listView" IsVisible="false" ItemsSource="{x:Static local:App.Employees}" ItemSelected="EmployeeListOnItemSelected">
    <ListView.ItemTemplate>
      <DataTemplate>
        <ViewCell>
          <ViewCell.View>
            <StackLayout Orientation="Horizontal">
              <Image Source="{Binding ImageUri}" WidthRequest="40" HeightRequest="40" />
              <StackLayout Orientation="Vertical" HorizontalOptions="StartAndExpand">
                <Label Text="{Binding DisplayName}" HorizontalOptions="FillAndExpand" />
                <Label Text="{Binding Twitter}" Font="{x:Static constants:Fonts.Twitter}"/>
              </StackLayout>
            </StackLayout>
          </ViewCell.View>
        </ViewCell>
      </DataTemplate>
    </ListView.ItemTemplate>
  </ListView>
</ContentPage>
```

Esse XAML define um [`ContentPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/) que contém um [`ListView`](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/). A fonte de dados do `ListView` é definida por meio do atributo [`ItemsSource`](https://developer.xamarin.com/api/property/Xamarin.Forms.ItemsView%3CTVisual%3E.ItemsSource/). O layout de cada linha no `ItemsSource` é definido dentro do elemento [`ListView.ItemTemplate`](https://developer.xamarin.com/api/property/Xamarin.Forms.ItemsView%3CTVisual%3E.ItemTemplate/).

<a name="Data_Binding" />

## <a name="data-binding"></a>Associação de dados

A vinculação de dados conecta dois objetos, chamados de a *origem* e o *destino*. O objeto *origem* fornece os dados. O objeto *destino* consumirá (e geralmente exibirá) dados do objeto de origem. Por exemplo, um [`Label`](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) (objeto de *destino*) normalmente associará sua propriedade [`Text`](https://developer.xamarin.com/api/property/Xamarin.Forms.Label.Text/) a uma propriedade `string` pública em um objeto de *origem*. O diagrama a seguir ilustra essa relação de associação:

![](introduction-to-xamarin-forms-images/data-binding.png "Associação de dados")

O principal benefício da vinculação de dados é que você não precisa se preocupar sobre a sincronização de dados entre suas exibições e a fonte de dados. As alterações no objeto de *origem* são enviadas automaticamente para o objeto de *destino* nos bastidores pela estrutura de associação, enquanto as alterações no objeto de destino podem ser opcionalmente enviadas de volta para o objeto de *origem*.

Estabelecer a vinculação de dados é um processo de duas etapas:

- A propriedade [`BindingContext`](https://developer.xamarin.com/api/property/Xamarin.Forms.BindableObject.BindingContext/) do objeto de *destino* deve ser definida como a *origem*.
- Uma associação deve ser estabelecida entre o *destino* e a *origem*. Em XAML, isso é obtido usando a extensão de marcação [`Binding`](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.BindingExtension/). Em C#, isso é obtido com o método [`SetBinding`](https://developer.xamarin.com/api/member/Xamarin.Forms.BindableObject.SetBinding/p/Xamarin.Forms.BindableProperty/Xamarin.Forms.BindingBase/).

Para obter mais informações sobre vinculação de dados, veja [Noções básicas de vinculação de dados](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md).

### <a name="xaml"></a>XAML

O código a seguir mostra um exemplo de execução de vinculação de dados em XAML:

```xaml
<Entry Text="{Binding FirstName}" ... />
```

Uma associação entre a propriedade [`Entry.Text`](https://developer.xamarin.com/api/property/Xamarin.Forms.Entry.Text/) e a propriedade `FirstName` do objeto de *origem* é estabelecida. As alterações feitas no controle `Entry` serão propagadas automaticamente para o objeto `employeeToDisplay`. De modo similar, se as alterações forem feitas para a propriedade `employeeToDisplay.FirstName`, o mecanismo de associação do Xamarin.Forms também atualizará o conteúdo do controle `Entry`. Isso é conhecido como uma *associação bidirecional*. Para que a associação bidirecional funcione, a classe de modelo deve implementar a interface `INotifyPropertyChanged`.

Embora a propriedade [`BindingContext`](https://developer.xamarin.com/api/property/Xamarin.Forms.BindableObject.BindingContext/) da classe `EmployeeDetailPage` possa ser definida em XAML, aqui ela é definida em code-behind para uma instância de um objeto `Employee`:

```csharp
public EmployeeDetailPage(Employee employee)
{
    InitializeComponent();
    this.BindingContext = employee;
}
```

Embora a propriedade [`BindingContext`](https://developer.xamarin.com/api/property/Xamarin.Forms.BindableObject.BindingContext/) de cada objeto de *destino* possa ser definida individualmente, isso não é necessário. `BindingContext` é uma propriedade especial que é herdada por todos os seus filhos. Portanto, quando o `BindingContext` no [`ContentPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/) é definido como uma instância de `Employee`, todos os filhos do `ContentPage` têm o mesmo `BindingContext` e podem associar-se a propriedades públicas do objeto `Employee`.

### <a name="c35"></a>C&#35;

O código a seguir mostra um exemplo de execução de vinculação de dados em C#:

```csharp
public EmployeeDetailPage(Employee employeeToDisplay)
{
    this.BindingContext = employeeToDisplay;
    var firstName = new Entry()
    {
        HorizontalOptions = LayoutOptions.FillAndExpand
    };
    firstName.SetBinding(Entry.TextProperty, "FirstName");
    ...
}
```

O construtor [`ContentPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/) recebe uma instância de um objeto `Employee` e define o [`BindingContext`](https://developer.xamarin.com/api/property/Xamarin.Forms.BindableObject.BindingContext/) para o objeto ao qual associar-se. Um controle [`Entry`](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/) é instanciado e a associação entre a propriedade [`Entry.Text`](https://developer.xamarin.com/api/property/Xamarin.Forms.Entry.Text/) e a propriedade `FirstName` do objeto de *origem* é estabelecida. As alterações feitas no controle `Entry` serão propagadas automaticamente para o objeto `employeeToDisplay`. De modo similar, se as alterações forem feitas para a propriedade `employeeToDisplay.FirstName`, o mecanismo de associação do Xamarin.Forms também atualizará o conteúdo do controle `Entry`. Isso é conhecido como uma *associação bidirecional*. Para que a associação bidirecional funcione, a classe de modelo deve implementar a interface `INotifyPropertyChanged`.

O método `SetBinding` utiliza dois parâmetros. O primeiro parâmetro especifica informações sobre o tipo de associação. O segundo parâmetro é usado para fornecer informações sobre a que devemos associar ou como associar. O segundo parâmetro é, na maioria dos casos, apenas uma cadeia de caracteres contendo o nome da propriedade no [`BindingContext`](https://developer.xamarin.com/api/property/Xamarin.Forms.BindableObject.BindingContext/). A sintaxe a seguir é usada para associar ao `BindingContext` diretamente:

```csharp
someLabel.SetBinding(Label.TextProperty, new Binding("."));
```

A sintaxe de ponto diz ao Xamarin.Forms para usar o [`BindingContext`](https://developer.xamarin.com/api/property/Xamarin.Forms.BindableObject.BindingContext/) como fonte de dados em vez de uma propriedade no `BindingContext`. Isso é útil quando o `BindingContext` é um tipo simples, como um `string` ou um `int`.

<a name="INotifyPropertyChanged" />

### <a name="property-change-notification"></a>Notificação de alteração de propriedade

Por padrão, o objeto de *destino* só recebe o valor do objeto de *origem* quando a associação é criada. Para manter a interface do usuário sincronizada com a fonte de dados, deverá haver um modo de notificar o objeto de *destino* quando o objeto de *origem* tiver sido alterado. Esse mecanismo é fornecido pela interface `INotifyPropertyChanged`. A implementação dessa interface fornece notificações para todos os controles associados a dados quando o valor da propriedade subjacente é alterado.

Os objetos que implementam `INotifyPropertyChanged` devem gerar o evento `PropertyChanged` quando uma das suas propriedades é atualizada com um novo valor, conforme demonstrado no exemplo de código a seguir:

```csharp
public class MyObject : INotifyPropertyChanged
{
    public event PropertyChangedEventHandler PropertyChanged;

    string _firstName;
    public string FirstName
    {
        get { return _firstName; }
        set
        {
            if (value.Equals(_firstName, StringComparison.Ordinal))
            {
                // Nothing to do - the value hasn't changed;
                return;
            }
            _firstName = value;
            OnPropertyChanged();

        }
    }

    void OnPropertyChanged([CallerMemberName] string propertyName = null)
    {
        var handler = PropertyChanged;
        if (handler != null)
        {
            handler(this, new PropertyChangedEventArgs(propertyName));
        }
    }
}
```

Quando a propriedade `MyObject.FirstName` é alterada, o método `OnPropertyChanged` é chamado e isso dispara o evento `PropertyChanged`. Para evitar o disparo de eventos desnecessários, o evento `PropertyChanged` não será gerado se o valor da propriedade não se altera.

Observe que no método `OnPropertyChanged`, o parâmetro `propertyName` é adornado com o atributo `CallerMemberName`. Isso garante que, se o método `OnPropertyChanged` for invocado com um valor `null`, o atributo `CallerMemberName` fornecerá o nome do método que invocou `OnPropertyChanged`.

<a name="Navigation" />

## <a name="navigation"></a>Navegação

o Xamarin.Forms oferece uma série de experiências de navegação de página diferentes, dependendo do tipo do [`Page`](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/) sendo usado. Para instâncias de [`ContentPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/), há duas experiências de navegação:

- [Navegação hierárquica](#Hierarchical_Navigation)
- [Navegação modal](#Modal_Navigation)

As classes [`CarouselPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.CarouselPage/), [`MasterDetailPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.MasterDetailPage/) e [`TabbedPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/) oferecem experiências de navegação alternativas. Para obter mais informações, veja [Navegação](~/xamarin-forms/app-fundamentals/navigation/index.md).

<a name="Hierarchical_Navigation" />

### <a name="hierarchical-navigation"></a>Navegação hierárquica

A classe [`NavigationPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/) oferece uma experiência de Navegação hierárquica em que o usuário é capaz de navegar pelas páginas para frente e para trás, conforme desejado. A classe implementa navegação como uma pilha UEPS (último a entrar, primeiro a sair) de objetos [`Page`](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/).

Na barra de navegação hierárquica, a classe [`NavigationPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/) é usada para navegar por meio de uma pilha de objetos [`ContentPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/). Para mover-se de uma página para outra, um aplicativo enviará por push uma nova página para a pilha de navegação, na qual ela se tornará a página ativa. Para retornar à página anterior, o aplicativo removerá o item mais recente, que é a página atual da pilha de navegação, então a nova página de nível mais alto se tornará a página ativa.

A primeira página adicionada a uma pilha de navegação é conhecida como a página *raiz* do aplicativo e o exemplo de código a seguir mostra como isso é realizado:

```csharp
public App ()
{
    MainPage = new NavigationPage(new EmployeeListPage());
}
```

Para navegar até o `LoginPage`, é necessário invocar o método [`PushAsync`](https://developer.xamarin.com/api/member/Xamarin.Forms.NavigationPage.PushAsync/p/Xamarin.Forms.Page/) na propriedade [`Navigation`](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Navigation/) da página atual, conforme demonstrado no exemplo de código a seguir:

```csharp
await Navigation.PushAsync(new LoginPage());
```

Isso faz com que o novo objeto `LoginPage` seja enviado por push para a pilha de navegação, em que ele se torna a página ativa.

A página ativa pode ser removida como o item mais recente da pilha de navegação pressionando o botão *Voltar* no dispositivo, independentemente de este ser um botão físico no dispositivo ou um botão na tela. Para retornar programaticamente à página anterior, a instância `LoginPage` deve invocar o método [`PopAsync`](https://developer.xamarin.com/api/member/Xamarin.Forms.NavigationPage.PopAsync()/), conforme demonstrado no exemplo de código a seguir:

```csharp
await Navigation.PopAsync();
```

Para obter mais informações sobre navegação hierárquica, veja [Navegação hierárquica](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md).

<a name="Modal_Navigation" />

### <a name="modal-navigation"></a>Navegação modal

O Xamarin.Forms dá suporte a páginas modais. Uma página modal incentiva os usuários a concluir uma tarefa independente da qual não se pode sair via navegação até que essa tarefa seja concluída ou cancelada.

Uma página restrita pode ser qualquer um dos tipos [`Page`](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/) para os quais o Xamarin.Forms dá suporte. Para exibir uma página modal, o aplicativo a enviará por push para a pilha de navegação, na qual ela se tornará a página ativa. Para retornar à página anterior, o aplicativo removerá o item mais recente, que é a página atual da pilha de navegação, então a nova página de nível mais alto se tornará a página ativa.

Os métodos de navegação modal são expostos pela propriedade [`Navigation`](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Navigation/) em quaisquer tipos derivados de [`Page`](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/). A propriedade [`Navigation`](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Navigation/) também expõe uma propriedade [`ModalStack`](https://developer.xamarin.com/api/property/Xamarin.Forms.INavigation.ModalStack/) da qual as páginas modais na pilha de navegação podem ser obtidas. No entanto, não há nenhum conceito de realização de manipulação de pilha modal ou remoção do item mais recente da pilha até a página raiz na navegação modal. Isso ocorre porque não há suporte universal para essas operações pelas plataformas subjacentes.

> [!NOTE]
> Uma instância de [`NavigationPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/) não é necessária para executar a navegação de página modal.

Para realizar navegação modal até o `LoginPage`, é necessário invocar o método [`PushModalAsync`](https://developer.xamarin.com/api/member/Xamarin.Forms.INavigation.PushModalAsync(Xamarin.Forms.Page)/) na propriedade [`Navigation`](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Navigation/) da página atual, conforme demonstrado no exemplo de código a seguir:

```csharp
await Navigation.PushModalAsync(new LoginPage());
```

Isso faz com que a instância `LoginPage` seja enviada por push para a pilha de navegação, em que ele se torna a página ativa.

A página ativa pode ser removida como o item mais recente da pilha de navegação pressionando o botão *Voltar* no dispositivo, independentemente de este ser um botão físico no dispositivo ou um botão na tela. Para retornar programaticamente à página original, a instância `LoginPage` deve invocar o método [`PopModalAsync`](https://developer.xamarin.com/api/member/Xamarin.Forms.INavigation.PopModalAsync()/), conforme demonstrado no exemplo de código a seguir:

```csharp
await Navigation.PopModalAsync();
```

Isso faz com que a instância `LoginPage` seja removida da pilha de navegação, com a nova página de nível superior tornando-se a página ativa.

Para obter mais informações sobre navegação modal, consulte [Páginas modais](~/xamarin-forms/app-fundamentals/navigation/modal.md).

<a name="Next_Steps" />

## <a name="next-steps"></a>Próximas etapas

Este artigo introdutório deve habilitar você a começar a escrever aplicativos do Xamarin.Forms. As próximas etapas sugeridas incluem ler sobre as seguintes funcionalidades:

- Os modelos de controle fornecem a capacidade de tematizar e tematizar novamente páginas de aplicativo em tempo de execução. Para saber mais, veja [Modelos de controle](~/xamarin-forms/app-fundamentals/templates/control-templates/index.md).
- Os modelos de dados fornecem a capacidade de definir a apresentação de dados em controles com suporte. Para saber mais, veja [Modelos de dados](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md).
- O código compartilhado pode acessar a funcionalidade nativa por meio da classe [`DependencyService`](https://developer.xamarin.com/api/type/Xamarin.Forms.DependencyService/). Para obter mais informações, consulte [Acessar recursos nativos com DependencyService](~/xamarin-forms/app-fundamentals/dependency-service/index.md).
- O Xamarin.Forms inclui um serviço de mensagens simples para enviar e receber mensagens, reduzindo assim o acoplamento de classes. Para obter mais informações, veja [Publicar e assinar com MessagingCenter](~/xamarin-forms/app-fundamentals/messaging-center.md).
- Cada página, layout e controle é renderizado diferentemente em cada plataforma usando uma classe `Renderer`, que por sua vez cria um controle nativo, organiza sua disposição na tela e adiciona o comportamento especificado no código compartilhado. Os desenvolvedores podem implementar suas próprias classes `Renderer` personalizadas para personalizar a aparência e/ou o comportamento de um controle. Para obter mais informações, veja [Renderizadores personalizados](~/xamarin-forms/app-fundamentals/custom-renderer/index.md).
- Efeitos também permitem que os controles nativos em cada plataforma sejam personalizados. Os efeitos são criados em projetos específicos da plataforma subclassificando-se o controle [`PlatformEffect`](https://developer.xamarin.com/api/type/Xamarin.Forms.PlatformEffect%3CTContainer,TControl%3E/) e consumidos anexando-os a um controle Xamarin.Forms apropriado. Para obter mais informações, veja [Efeitos](~/xamarin-forms/app-fundamentals/effects/index.md).

Alternativamente, Criação de aplicativos móveis com Xamarin.Forms, um livro de Charles Petzold, é um bom lugar para saber mais sobre o Xamarin.Forms. Para obter mais informações, consulte [Criação de aplicativos móveis com Xamarin.Forms](~/xamarin-forms/creating-mobile-apps-xamarin-forms/index.md).

## <a name="summary"></a>Resumo

Este artigo forneceu uma introdução ao Xamarin.Forms e mostrou como começar a escrever aplicativos com ele. O Xamarin.Forms é uma abstração de kit de ferramentas de interface do usuário com suporte nativo multiplataforma que permite aos desenvolvedores criar com facilidade interfaces do usuário que podem ser compartilhadas entre Android, iOS e a Plataforma Universal do Windows. As interfaces do usuário são renderizadas usando controles nativos da plataforma de destino, permitindo que os aplicativos Xamarin.Forms mantenham a aparência apropriada para cada plataforma.


## <a name="related-links"></a>Links relacionados

- [Noções básicas de XAML](~/xamarin-forms/xaml/xaml-basics/index.md)
- [Referência de Controles](~/xamarin-forms/user-interface/controls/index.md)
- [Interface do Usuário](~/xamarin-forms/user-interface/index.md)
- [Amostras do Xamarin.Forms](https://developer.xamarin.com/samples/xamarin-forms/all/)
- [Exemplos de Introdução](https://developer.xamarin.com/samples/xamarin-forms/GettingStarted/)
- [Xamarin.Forms](https://developer.xamarin.com/api/namespace/Xamarin.Forms/)
- [Aprendizado autodirigido gratuito (vídeo)](https://university.xamarin.com/self-guided)
- [Pasta de trabalho do Hello, Xamarin.Forms iOS](https://developer.xamarin.com/workbooks/xamarin-forms/getting-started/GettingStartedWithXamarinForms-ios.workbook)
