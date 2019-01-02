---
title: Uma Introdução ao Xamarin.Forms
description: Este artigo fornece uma introdução ao Xamarin.Forms e mostra como começar a escrever aplicativos com ele.
ms.prod: xamarin
ms.assetid: f619595f-3ee7-439b-a1bc-d13e5106e6e9
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/25/2018
ms.openlocfilehash: c716f39faad0b58159df5631bf415239a2c658b1
ms.sourcegitcommit: 06f88979db160fb8dd1c9ee0d5000d8749107489
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/28/2018
ms.locfileid: "53806945"
---
# <a name="an-introduction-to-xamarinforms"></a>Uma Introdução ao Xamarin.Forms

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://developer.xamarin.com/samples/xamarin-forms/GettingStarted/)

_Xamarin.Forms é uma estrutura que permite aos desenvolvedores criar aplicativos multiplataforma para Android, iOS e Windows. As definições de interface do usuário e códigos são compartilhadas entre plataformas, mas são renderizadas usando controles nativos. Este artigo faz uma introdução ao Xamarin.Forms e mostra como começar a desenvolver aplicativos com C# e XAML no Visual Studio._

Aplicativos Xamarin.Forms usam projetos do [.NET Standard](~/cross-platform/app-fundamentals/net-standard.md) para armazenar o código compartilhado e separam projetos de aplicativos para consumir o código compartilhado e gerar a saída necessária para cada plataforma. Quando você cria um novo aplicativo Xamarin.Forms, a solução inclui o projeto de código compartilhado (contendo arquivos C# e XAML) e os projetos específicos da plataforma, como mostra a captura de tela a seguir:

![Solução de modelo do Xamarin.Forms no Visual Studio](introduction-to-xamarin-forms-images/solution-both.png)

Quando desenvolve aplicativos Xamarin.Forms, o código e a interface do usuário são adicionados ao projeto do .NET Standard, que é referenciado por projetos Android, iOS e UWP. Você criará e executará os projetos Android, iOS e UWP para testar e implantar o aplicativo.

## <a name="examining-a-xamarinforms-application"></a>Como examinar um aplicativo Xamarin.Forms

O modelo de aplicativo Xamarin.Forms padrão no Visual Studio exibe um único rótulo de texto. Se você executar o aplicativo, ele deverá ser semelhante às capturas de tela a seguir:

[![](introduction-to-xamarin-forms-images/image05-sml.png "Aplicativo Xamarin.Forms padrão")](introduction-to-xamarin-forms-images/image05.png#lightbox)

Cada tela nas capturas de tela corresponde a uma *página* no Xamarin.Forms. Um [`Page`](xref:Xamarin.Forms.Page) representa uma *atividade* no Android, um *controlador de exibição* no iOS ou uma *página* na UWP (Plataforma Universal do Windows). O exemplo nas capturas de tela acima instancia um objeto [`ContentPage`](xref:Xamarin.Forms.ContentPage) e usa isso para exibir um [`Label`](xref:Xamarin.Forms.Label).

Para maximizar a reutilização do código de inicialização, os aplicativos Xamarin.Forms têm uma única classe chamada `App`, que é responsável por instanciar a primeira [`Page`](xref:Xamarin.Forms.Page) que será exibida. Observe um exemplo da classe `App` no código a seguir (em **App.xaml.cs**):

```csharp
public partial class App : Application
{
  public App ()
  {
    InitializeComponent();
    MainPage = new MainPage(); // sets the App.MainPage property to an instance of the MainPage class
  }
}
```

Esse código instancia um novo objeto [`ContentPage`](xref:Xamarin.Forms.ContentPage) chamado `MainPage` que exibirá um único [`Label`](xref:Xamarin.Forms.Label) centralizado, vertical e horizontalmente na página. O XAML no arquivo **MainPage.xaml** tem a seguinte aparência:

```xaml
<?xml version="1.0" encoding="utf-8" ?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" xmlns:local="clr-namespace:AwesomeApp" x:Class="AwesomeApp.MainPage">
    <StackLayout>
        <Label Text="Hello Xamarin.Forms"
           HorizontalOptions="Center"
           VerticalOptions="CenterAndExpand" />
    </StackLayout>
</ContentPage>
```

### <a name="launching-the-initial-xamarinforms-page-on-each-platform"></a>Como iniciar a página inicial do Xamarin.Forms em cada plataforma

> [!TIP]
> As informações específicas da plataforma nesta seção são fornecidas para ajudar a entender como funciona o Xamarin.Forms.
> Os modelos de projeto já incluem essas classes. Não é necessário codificá-los.
>
> Se preferir, avance para a seção [Interface do usuário](#user-interface) e leia esta parte posteriormente.

Para usar uma página (como **MainPage**, no exemplo acima) dentro de um aplicativo, cada aplicativo da plataforma deve inicializar a estrutura do Xamarin.Forms e fornecer uma instância da página quando estiver inicializando. Essa etapa de inicialização varia de uma plataforma para outra e é discutida nas seções a seguir.

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

A substituição `FinishedLaunching` inicializa a estrutura do Xamarin.Forms ao chamar o método `Init`. Isso faz a implementação específica do iOS do Xamarin.Forms ser carregada no aplicativo antes que o controlador de exibição de raiz seja definido pela chamada para o método `LoadApplication`.

#### <a name="android"></a>Android

Para inicializar a página inicial do Xamarin.Forms no Android, o projeto de plataforma inclui código que cria uma `Activity` com o atributo `MainLauncher`, com a atividade herdando da classe `FormsAppCompatActivity`, conforme demonstrado no exemplo de código a seguir:

```csharp
namespace HelloXamarinFormsWorld.Android
{
    [Activity(Label = "HelloXamarinFormsWorld", Theme = "@style/MainTheme", MainLauncher = true,
        ConfigurationChanges = ConfigChanges.ScreenSize | ConfigChanges.Orientation)]
    public class MainActivity : FormsAppCompatActivity
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

#### <a name="universal-windows-platform-uwp"></a>UWP (Plataforma Universal do Windows)

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

O aplicativo Xamarin.Forms é carregado com o método `LoadApplication`. O Visual Studio adiciona todo o código acima quando você cria um novo projeto do Xamarin.Forms.

## <a name="user-interface"></a>Interface do usuário

Existem duas técnicas para criar interfaces do usuário no Xamarin.Forms:

- Criar toda a interface do usuário com o código-fonte do C#.
- *XAML (Extensible Application Markup Language)*, uma linguagem de marcação declarativa usada para descrever interfaces do usuário.

É possível alcançar os mesmos resultados, independentemente do método usado. Ambos os métodos são explicados a seguir. Para saber mais sobre XAML de Xamarin.Forms, confira [Noções básicas de XAML](~/xamarin-forms/xaml/xaml-basics/index.md).

### <a name="views-and-layouts"></a>Layouts e exibições

Há quatro grupos de controle principais usados para criar a interface do usuário de um aplicativo Xamarin.Forms.

- **Páginas** – as páginas do Xamarin.Forms representam telas de aplicativos móveis de plataforma cruzada. Para saber mais sobre páginas, consulte [Xamarin.Forms Pages](~/xamarin-forms/user-interface/controls/pages.md) (Páginas do Xamarin.Forms).
- **Layouts** – os layouts do Xamarin.Forms são contêineres usados para compor exibições em estruturas lógicas. Para saber mais sobre layouts, consulte [Xamarin.Forms Layouts](~/xamarin-forms/user-interface/controls/layouts.md) (Layouts do Xamarin.Forms).
- **Exibições** – as exibições do Xamarin.Forms são os controles exibidos na interface do usuário, como rótulos, botões e caixas de entrada de texto. Para saber mais sobre exibições, consulte [Xamarin.Forms Views](~/xamarin-forms/user-interface/controls/views.md) (Exibições do Xamarin.Forms).
- **Células** – as células do Xamarin.Forms são elementos especializados usados para itens em uma lista e descrevem como cada item em uma lista deve ser desenhado. Para saber mais sobre células, consulte [Xamarin.Forms Cells](~/xamarin-forms/user-interface/controls/cells.md) (Células do Xamarin.Forms).

No tempo de execução, cada controle será mapeado para o equivalente nativo, que é renderizado na tela.

Os controles são hospedados dentro de um layout. A classe [`StackLayout`](xref:Xamarin.Forms.StackLayout)&ndash;, um layout usado normalmente, &ndash; será abordada a seguir.

### <a name="stacklayout"></a>StackLayout

O [`StackLayout`](xref:Xamarin.Forms.StackLayout) simplifica o desenvolvimento de aplicativos de plataforma cruzada organizando automaticamente os controles na tela, independentemente do tamanho dela. Os elementos filho são posicionados um após o outro, horizontalmente ou verticalmente, na ordem em que foram adicionados. A quantidade de espaço que o `StackLayout` usará dependerá de como as propriedades [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) e [`VerticalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) forem definidas, mas por padrão o `StackLayout` tentará usar a tela inteira.

O código XAML a seguir mostra um exemplo de como usar um [`StackLayout`](xref:Xamarin.Forms.StackLayout) para organizar três controles [`Label`](xref:Xamarin.Forms.Label):

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

Por padrão, o [`StackLayout`](xref:Xamarin.Forms.StackLayout) assume uma orientação vertical conforme mostrado nas capturas de tela a seguir:

[![](introduction-to-xamarin-forms-images/image09-sml.png "StackLayout vertical")](introduction-to-xamarin-forms-images/image09.png#lightbox "StackLayout vertical")

A orientação do [`StackLayout`](xref:Xamarin.Forms.StackLayout) pode ser alterado para uma orientação horizontal, conforme demonstrado no exemplo de código XAML a seguir:

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
        // Code that creates red, yellow, green labels removed for clarity (see above)
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

[![](introduction-to-xamarin-forms-images/image11-sml.png "StackLayout horizontal com LayoutOptions")](introduction-to-xamarin-forms-images/image11.png#lightbox)

Para obter mais informações sobre a classe [`StackLayout`](xref:Xamarin.Forms.StackLayout), veja [StackLayout](~/xamarin-forms/user-interface/layouts/stack-layout.md).

## <a name="lists-in-xamarinforms"></a>Listas em Xamarin.Forms

O controle [`ListView`](xref:Xamarin.Forms.ListView) é responsável por exibir uma coleção de itens na tela – cada item no `ListView` estará contido em uma única célula. Por padrão, um `ListView` usará o modelo [`TextCell`](xref:Xamarin.Forms.TextCell) interna e renderizará uma única linha de texto.

O exemplo de código a seguir mostra um exemplo de [`ListView`](xref:Xamarin.Forms.ListView) simples:

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

A captura de tela a seguir mostra o [`ListView`](xref:Xamarin.Forms.ListView) resultante:

 ![](introduction-to-xamarin-forms-images/image13.png "ListView")

Para obter mais informações sobre o controle [`ListView`](xref:Xamarin.Forms.ListView), veja [ListView](~/xamarin-forms/user-interface/listview/index.md).

### <a name="binding-to-a-custom-class"></a>Associação a uma classe personalizada

O controle [`ListView`](xref:Xamarin.Forms.ListView) também pode exibir os objetos personalizados usando o modelo [`TextCell`](xref:Xamarin.Forms.TextCell) padrão.

O exemplo de código a seguir mostra a classe `TodoItem`:

```csharp
public class TodoItem
{
    public string Name { get; set; }
    public bool Done { get; set; }
}
```

O controle [`ListView`](xref:Xamarin.Forms.ListView) pode ser populado conforme demonstrado no exemplo de código a seguir:

```csharp
listView.ItemsSource = new TodoItem [] {
    new TodoItem { Name = "Buy pears" },
    new TodoItem { Name = "Buy oranges", Done=true} ,
    new TodoItem { Name = "Buy mangos" },
    new TodoItem { Name = "Buy apples", Done=true },
    new TodoItem { Name = "Buy bananas", Done=true }
};
```

Uma associação pode ser criada para definir qual propriedade `TodoItem` é exibida pelo [`ListView`](xref:Xamarin.Forms.ListView), conforme demonstrado no exemplo de código a seguir:

```csharp
listView.ItemTemplate = new DataTemplate(typeof(TextCell));
listView.ItemTemplate.SetBinding(TextCell.TextProperty, "Name");
```

Isso cria uma associação que especifica o caminho para a propriedade `TodoItem.Name` e resultará na captura de tela exibida anteriormente.

Para obter mais informações sobre a associação a uma classe personalizada, consulte [Fontes de Dados de ListView](~/xamarin-forms/user-interface/listview/data-and-databinding.md).

### <a name="selecting-an-item-in-a-listview"></a>Selecionando um item em um ListView

Para responder a um usuário tocar em uma célula em um [`ListView`](xref:Xamarin.Forms.ListView), deve-se lidar com o evento [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected), conforme demonstrado no exemplo de código a seguir:

```csharp
listView.ItemSelected += async (sender, e) => {
    await DisplayAlert("Tapped!", e.SelectedItem + " was tapped.", "OK");
};
```

Quando contido em um [`NavigationPage`](xref:Xamarin.Forms.NavigationPage), o método [`PushAsync`](xref:Xamarin.Forms.NavigationPage.PushAsync(Xamarin.Forms.Page)) pode ser usado para abrir uma nova página com navegação de retorno interna. O evento [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) pode acessar o objeto que foi associado à célula por meio da propriedade [`e.SelectedItem`](xref:Xamarin.Forms.SelectedItemChangedEventArgs.SelectedItem), associá-lo a uma nova página e exibir a nova página usando `PushAsync`, conforme demonstrado no exemplo de código a seguir:

```csharp
listView.ItemSelected += async (sender, e) => {
    var todoItem = (TodoItem)e.SelectedItem;
    var todoPage = new TodoItemPage(todoItem); // so the new page shows correct data
    await Navigation.PushAsync(todoPage);
};
```

Cada plataforma implementa navegação de retorno interna de sua própria maneira. Para obter mais informações, veja [Navegação](#Navigation).

Para obter mais informações sobre a seleção [`ListView`](xref:Xamarin.Forms.ListView), veja [Interatividade do ListView](~/xamarin-forms/user-interface/listview/interactivity.md).

### <a name="customizing-the-appearance-of-a-cell"></a>Personalizando a aparência de uma célula

A aparência de célula pode ser personalizada subclassificando-se a classe [`ViewCell`](xref:Xamarin.Forms.ViewCell) e definindo-se o tipo dessa classe para a propriedade [`ItemTemplate`](xref:Xamarin.Forms.ItemsView`1.ItemTemplate) do [`ListView`](xref:Xamarin.Forms.ListView).

A célula mostrada na seguinte captura de tela é composta de um controle [`Image`](xref:Xamarin.Forms.Image) e dois controles [`Label`](xref:Xamarin.Forms.Label):

 ![](introduction-to-xamarin-forms-images/image14.png "Aparência de célula personalizada ListView")

Para criar esse layout personalizado, a classe [`ViewCell`](xref:Xamarin.Forms.ViewCell) deve ser uma subclasse, conforme demonstrado no exemplo de código a seguir:

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
           FontSize = 24
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

-  Ele adiciona um controle [`Image`](xref:Xamarin.Forms.Image) e associa-o à propriedade `ImageUri` do objeto `Employee`. Para obter mais informações sobre vinculação de dados, veja [Vinculação de dados](#Data_Binding).
-  Ele cria um [`StackLayout`](xref:Xamarin.Forms.StackLayout) com uma orientação vertical para manter os dois controles [`Label`](xref:Xamarin.Forms.Label). Os controles `Label` estão associados às propriedades `DisplayName` e `Twitter` do objeto `Employee`.
-  Ele cria um [`StackLayout`](xref:Xamarin.Forms.StackLayout) que hospedará os [`Image`](xref:Xamarin.Forms.Image) e `StackLayout` existentes. Ele organizará seus filhos usando uma orientação horizontal.

Após a célula personalizada ter sido criada, ela poderá ser consumida por um controle [`ListView`](xref:Xamarin.Forms.ListView) encapsulando-o em um [`DataTemplate`](xref:Xamarin.Forms.DataTemplate), conforme demonstrado no exemplo de código a seguir:

```csharp
List<Employee> myListOfEmployeeObjects = GetAListOfAllEmployees();
var listView = new ListView
{
    RowHeight = 40
};
listView.ItemsSource = myListOfEmployeeObjects;
listView.ItemTemplate = new DataTemplate(typeof(EmployeeCell));
```

Esse código fornecerá um `List` de `Employee` para o [`ListView`](xref:Xamarin.Forms.ListView). Cada célula será renderizada usando a classe `EmployeeCell`. O `ListView` passará o objeto `Employee` para o `EmployeeCell` como seu [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext).

Para obter mais informações sobre como personalizar a aparência da célula, consulte [Aparência da célula](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md).

### <a name="using-xaml-to-create-and-customize-a-list"></a>Usando XAML para criar e personalizar uma lista

O equivalente em XAML ao [`ListView`](xref:Xamarin.Forms.ListView) na seção anterior é demonstrado no exemplo de código a seguir:

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

Esse XAML define um [`ContentPage`](xref:Xamarin.Forms.ContentPage) que contém um [`ListView`](xref:Xamarin.Forms.ListView). A fonte de dados do `ListView` é definida por meio do atributo [`ItemsSource`](xref:Xamarin.Forms.ItemsView`1.ItemsSource). O layout de cada linha no `ItemsSource` é definido dentro do elemento [`ListView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1.ItemTemplate).

## <a name="data-binding"></a>Associação de dados

A vinculação de dados conecta dois objetos, chamados de a *origem* e o *destino*. O objeto *origem* fornece os dados. O objeto *destino* consumirá (e geralmente exibirá) dados do objeto de origem. Por exemplo, um [`Label`](xref:Xamarin.Forms.Label) (objeto de *destino*) normalmente associará sua propriedade [`Text`](xref:Xamarin.Forms.Label.Text) a uma propriedade `string` pública em um objeto de *origem*. O diagrama a seguir ilustra essa relação de associação:

![](introduction-to-xamarin-forms-images/data-binding.png "Associação de dados")

O principal benefício da vinculação de dados é que você não precisa se preocupar sobre a sincronização de dados entre suas exibições e a fonte de dados. As alterações no objeto de *origem* são enviadas automaticamente para o objeto de *destino* nos bastidores pela estrutura de associação, enquanto as alterações no objeto de destino podem ser opcionalmente enviadas de volta para o objeto de *origem*.

Estabelecer a vinculação de dados é um processo de duas etapas:

- A propriedade [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) do objeto de *destino* deve ser definida como a *origem*.
- Uma associação deve ser estabelecida entre o *destino* e a *origem*. Em XAML, isso é obtido usando a extensão de marcação [`Binding`](xref:Xamarin.Forms.Xaml.BindingExtension). Em C#, isso é obtido com o método [`SetBinding`](xref:Xamarin.Forms.BindableObject.SetBinding(Xamarin.Forms.BindableProperty,Xamarin.Forms.BindingBase)).

Para obter mais informações sobre vinculação de dados, veja [Noções básicas de vinculação de dados](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md).

### <a name="xaml"></a>XAML

O código a seguir mostra um exemplo de execução de vinculação de dados em XAML:

```xaml
<Entry Text="{Binding FirstName}" ... />
```

Uma associação entre a propriedade [`Entry.Text`](xref:Xamarin.Forms.Entry.Text) e a propriedade `FirstName` do objeto de *origem* é estabelecida. As alterações feitas no controle `Entry` serão propagadas automaticamente para o objeto `employeeToDisplay`. De modo semelhante, quando as alterações são feitas com a propriedade `employeeToDisplay.FirstName`, o mecanismo de associação do Xamarin.Forms também atualiza o conteúdo do controle `Entry`. Isso é conhecido como uma *associação bidirecional*. Para que a associação bidirecional funcione, a classe de modelo deve implementar a interface `INotifyPropertyChanged`.

Embora a propriedade [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) da classe `EmployeeDetailPage` possa ser definida em XAML, aqui ela é definida em code-behind para uma instância de um objeto `Employee`:

```csharp
public EmployeeDetailPage(Employee employee)
{
    InitializeComponent();
    this.BindingContext = employee;
}
```

Embora a propriedade [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) de cada objeto de *destino* possa ser definida individualmente, isso não é necessário. `BindingContext` é uma propriedade especial que é herdada por todos os seus filhos. Portanto, quando o `BindingContext` no [`ContentPage`](xref:Xamarin.Forms.ContentPage) é definido como uma instância de `Employee`, todos os filhos do `ContentPage` têm o mesmo `BindingContext` e podem associar-se a propriedades públicas do objeto `Employee`.

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

O construtor [`ContentPage`](xref:Xamarin.Forms.ContentPage) recebe uma instância de um objeto `Employee` e define o [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) para o objeto ao qual associar-se. Um controle [`Entry`](xref:Xamarin.Forms.Entry) é instanciado e a associação entre a propriedade [`Entry.Text`](xref:Xamarin.Forms.Entry.Text) e a propriedade `FirstName` do objeto de *origem* é estabelecida. As alterações feitas no controle `Entry` serão propagadas automaticamente para o objeto `employeeToDisplay`. De modo semelhante, quando as alterações são feitas com a propriedade `employeeToDisplay.FirstName`, o mecanismo de associação do Xamarin.Forms também atualiza o conteúdo do controle `Entry`. Isso é conhecido como uma *associação bidirecional*. Para que a associação bidirecional funcione, a classe de modelo deve implementar a interface `INotifyPropertyChanged`.

O método `SetBinding` utiliza dois parâmetros. O primeiro parâmetro especifica informações sobre o tipo de associação. O segundo parâmetro é usado para fornecer informações sobre a que devemos associar ou como associar. O segundo parâmetro é, na maioria dos casos, apenas uma cadeia de caracteres contendo o nome da propriedade no [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext). A sintaxe a seguir é usada para associar ao `BindingContext` diretamente:

```csharp
someLabel.SetBinding(Label.TextProperty, new Binding("."));
```

A sintaxe de ponto diz ao Xamarin.Forms para usar o [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) como fonte de dados em vez de uma propriedade no `BindingContext`. Isso é útil quando o `BindingContext` é um tipo simples, como um `string` ou um `int`.

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

## <a name="navigation"></a>Navegação

o Xamarin.Forms oferece uma série de experiências de navegação de página diferentes, dependendo do tipo do [`Page`](xref:Xamarin.Forms.Page) sendo usado. Para instâncias de [`ContentPage`](xref:Xamarin.Forms.ContentPage), há duas experiências de navegação:

- [Navegação hierárquica](#Hierarchical_Navigation)
- [Navegação modal](#Modal_Navigation)

As classes [`CarouselPage`](xref:Xamarin.Forms.CarouselPage), [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) e [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) oferecem experiências de navegação alternativas. Para obter mais informações, veja [Navegação](~/xamarin-forms/app-fundamentals/navigation/index.md).

### <a name="hierarchical-navigation"></a>Navegação hierárquica

A classe [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) oferece uma experiência de Navegação hierárquica em que o usuário é capaz de navegar pelas páginas para frente e para trás, conforme desejado. A classe implementa navegação como uma pilha UEPS (último a entrar, primeiro a sair) de objetos [`Page`](xref:Xamarin.Forms.Page).

Na barra de navegação hierárquica, a classe [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) é usada para navegar por meio de uma pilha de objetos [`ContentPage`](xref:Xamarin.Forms.ContentPage). Para mover-se de uma página para outra, um aplicativo enviará por push uma nova página para a pilha de navegação, na qual ela se tornará a página ativa. Para retornar à página anterior, o aplicativo removerá o item mais recente, que é a página atual da pilha de navegação, então a nova página de nível mais alto se tornará a página ativa.

A primeira página adicionada a uma pilha de navegação é conhecida como a página *raiz* do aplicativo e o exemplo de código a seguir mostra como isso é realizado:

```csharp
public App ()
{
    MainPage = new NavigationPage(new EmployeeListPage());
}
```

Para navegar até o `LoginPage`, é necessário invocar o método [`PushAsync`](xref:Xamarin.Forms.NavigationPage.PushAsync(Xamarin.Forms.Page)) na propriedade [`Navigation`](xref:Xamarin.Forms.VisualElement.Navigation) da página atual, conforme demonstrado no exemplo de código a seguir:

```csharp
await Navigation.PushAsync(new LoginPage());
```

Isso faz com que o novo objeto `LoginPage` seja enviado por push para a pilha de navegação, em que ele se torna a página ativa.

A página ativa pode ser removida como o item mais recente da pilha de navegação pressionando o botão *Voltar* no dispositivo, independentemente de este ser um botão físico no dispositivo ou um botão na tela. Para retornar programaticamente à página anterior, a instância `LoginPage` deve invocar o método [`PopAsync`](xref:Xamarin.Forms.NavigationPage.PopAsync), conforme demonstrado no exemplo de código a seguir:

```csharp
await Navigation.PopAsync();
```

Para obter mais informações sobre navegação hierárquica, veja [Navegação hierárquica](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md).

### <a name="modal-navigation"></a>Navegação modal

O Xamarin.Forms dá suporte a páginas modais. Uma página modal incentiva os usuários a concluir uma tarefa independente da qual não se pode sair via navegação até que essa tarefa seja concluída ou cancelada.

Uma página restrita pode ser qualquer um dos tipos [`Page`](xref:Xamarin.Forms.Page) para os quais o Xamarin.Forms dá suporte. Para exibir uma página modal, o aplicativo a enviará por push para a pilha de navegação, na qual ela se tornará a página ativa. Para retornar à página anterior, o aplicativo removerá o item mais recente, que é a página atual da pilha de navegação, então a nova página de nível mais alto se tornará a página ativa.

Os métodos de navegação modal são expostos pela propriedade [`Navigation`](xref:Xamarin.Forms.VisualElement.Navigation) em quaisquer tipos derivados de [`Page`](xref:Xamarin.Forms.Page). A propriedade [`Navigation`](xref:Xamarin.Forms.VisualElement.Navigation) também expõe uma propriedade [`ModalStack`](xref:Xamarin.Forms.INavigation.ModalStack) da qual as páginas modais na pilha de navegação podem ser obtidas. No entanto, não há nenhum conceito de realização de manipulação de pilha modal ou remoção do item mais recente da pilha até a página raiz na navegação modal. Isso ocorre porque não há suporte universal para essas operações pelas plataformas subjacentes.

> [!NOTE]
> Uma instância de [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) não é necessária para executar a navegação de página modal.

Para realizar navegação modal até o `LoginPage`, é necessário invocar o método [`PushModalAsync`](xref:Xamarin.Forms.INavigation.PushModalAsync*) na propriedade [`Navigation`](xref:Xamarin.Forms.VisualElement.Navigation) da página atual, conforme demonstrado no exemplo de código a seguir:

```csharp
await Navigation.PushModalAsync(new LoginPage());
```

Isso faz com que a instância `LoginPage` seja enviada por push para a pilha de navegação, em que ele se torna a página ativa.

A página ativa pode ser removida como o item mais recente da pilha de navegação pressionando o botão *Voltar* no dispositivo, independentemente de este ser um botão físico no dispositivo ou um botão na tela. Para retornar programaticamente à página original, a instância `LoginPage` deve invocar o método [`PopModalAsync`](xref:Xamarin.Forms.INavigation.PopModalAsync), conforme demonstrado no exemplo de código a seguir:

```csharp
await Navigation.PopModalAsync();
```

Isso faz com que a instância `LoginPage` seja removida da pilha de navegação, com a nova página de nível superior tornando-se a página ativa.

Para obter mais informações sobre navegação modal, consulte [Páginas modais](~/xamarin-forms/app-fundamentals/navigation/modal.md).

## <a name="next-steps"></a>Próximas etapas

Este artigo introdutório deve habilitar você a começar a escrever aplicativos do Xamarin.Forms. As próximas etapas sugeridas incluem ler sobre as seguintes funcionalidades:

- Os modelos de controle fornecem a capacidade de tematizar e tematizar novamente páginas de aplicativo em tempo de execução. Para saber mais, veja [Modelos de controle](~/xamarin-forms/app-fundamentals/templates/control-templates/index.md).
- Os modelos de dados fornecem a capacidade de definir a apresentação de dados em controles com suporte. Para saber mais, veja [Modelos de dados](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md).
- O código compartilhado pode acessar a funcionalidade nativa por meio da classe [`DependencyService`](xref:Xamarin.Forms.DependencyService). Para obter mais informações, consulte [Acessar recursos nativos com DependencyService](~/xamarin-forms/app-fundamentals/dependency-service/index.md).
- O Xamarin.Forms inclui um serviço de mensagens simples para enviar e receber mensagens, reduzindo assim o acoplamento de classes. Para obter mais informações, veja [Publicar e assinar com MessagingCenter](~/xamarin-forms/app-fundamentals/messaging-center.md).
- Cada página, layout e controle é renderizado diferentemente em cada plataforma usando uma classe `Renderer`, que por sua vez cria um controle nativo, organiza sua disposição na tela e adiciona o comportamento especificado no código compartilhado. Os desenvolvedores podem implementar suas próprias classes `Renderer` personalizadas para personalizar a aparência e/ou o comportamento de um controle. Para obter mais informações, veja [Renderizadores personalizados](~/xamarin-forms/app-fundamentals/custom-renderer/index.md).
- Efeitos também permitem que os controles nativos em cada plataforma sejam personalizados. Os efeitos são criados em projetos específicos da plataforma subclassificando-se o controle [`PlatformEffect`](xref:Xamarin.Forms.PlatformEffect`2) e consumidos anexando-os a um controle Xamarin.Forms apropriado. Para obter mais informações, veja [Efeitos](~/xamarin-forms/app-fundamentals/effects/index.md).

Como alternativa, veja [_Criando aplicativos móveis com Xamarin.Forms_](~/xamarin-forms/creating-mobile-apps-xamarin-forms/index.md), um livro de Charles Petzold, é uma ótima opção para saber mais sobre o Xamarin.Forms. O livro está disponível em PDF ou em vários formatos de livro eletrônico.

## <a name="related-links"></a>Links relacionados

- [Noções básicas de XAML](~/xamarin-forms/xaml/xaml-basics/index.md)
- [Referência de Controles](~/xamarin-forms/user-interface/controls/index.md)
- [Interface do Usuário](~/xamarin-forms/user-interface/index.md)
- [Amostras do Xamarin.Forms](https://developer.xamarin.com/samples/xamarin-forms/all/)
- [Exemplos de Introdução](https://developer.xamarin.com/samples/xamarin-forms/GettingStarted/)
- [Referência da API Xamarin.Forms](xref:Xamarin.Forms)
- [Aprendizado autodirigido gratuito (vídeo)](https://university.xamarin.com/self-guided)
