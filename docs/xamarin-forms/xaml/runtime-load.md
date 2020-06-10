---
Título: "carregando XAML em tempo de execução em Xamarin.Forms " Descrição: "XAML pode ser carregado e analisado em tempo de execução com os métodos de extensão LoadFromXaml."
MS. Prod: xamarin MS. AssetID: 25F73FBF-2DD3-468E-A2D8-0897414F0F4A MS. Technology: xamarin-Forms autor: davidbritch MS. Author: dabritch MS. Date: 12/12/2018 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="loading-xaml-at-runtime-in-xamarinforms"></a>Carregando XAML em tempo de execução noXamarin.Forms

[![Baixar exemplo ](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-loadruntimexaml)

O [`Xamarin.Forms.Xaml`](xref:Xamarin.Forms.Xaml) namespace inclui dois [`LoadFromXaml`](xref:Xamarin.Forms.Xaml.Extensions.LoadFromXaml*) métodos de extensão que podem ser usados para carregar e analisar XAML em tempo de execução.

## <a name="background"></a>Segundo plano

Quando uma Xamarin.Forms classe XAML é construída, o [`LoadFromXaml`](xref:Xamarin.Forms.Xaml.Extensions.LoadFromXaml*) método é chamado indiretamente. Isso ocorre porque o arquivo code-behind para uma classe XAML chama o `InitializeComponent` método de seu construtor:

```csharp
public partial class MainPage : ContentPage
{
    public MainPage()
    {
        InitializeComponent();
    }
}
```

Quando o Visual Studio cria um projeto que contém um arquivo XAML, ele analisa o arquivo XAML para gerar um arquivo de código C# (por exemplo, **MainPage.XAML.g.cs**) que contém a definição do `InitializeComponent` método:

```csharp
private void InitializeComponent()
{
    global::Xamarin.Forms.Xaml.Extensions.LoadFromXaml(this, typeof(MainPage));
    ...
}
```

O `InitializeComponent` método chama o [`LoadFromXaml`](xref:Xamarin.Forms.Xaml.Extensions.LoadFromXaml*) método para extrair o arquivo XAML (ou seu binário compilado) da biblioteca de .net Standard. Após a extração, ele inicializa todos os objetos definidos no arquivo XAML, conecta-os juntos em relações pai-filho, anexa manipuladores de eventos definidos no código aos eventos definidos no arquivo XAML e define a árvore resultante de objetos como o conteúdo da página.

## <a name="loading-xaml-at-runtime"></a>Carregando XAML em tempo de execução

Os [`LoadFromXaml`](xref:Xamarin.Forms.Xaml.Extensions.LoadFromXaml*) métodos são `public` e, portanto, podem ser chamados de Xamarin.Forms aplicativos para carregar e analisar XAML em tempo de execução. Isso permite cenários como um aplicativo baixando XAML de um serviço Web, criando a exibição necessária a partir do XAML e exibindo-o no aplicativo.

> [!WARNING]
> O carregamento de XAML em tempo de execução tem um custo de desempenho significativo e, em geral, deve ser evitado.

O exemplo de código a seguir mostra um uso simples:

```csharp
using Xamarin.Forms.Xaml;
...

string navigationButtonXAML = "<Button Text=\"Navigate\" />";
Button navigationButton = new Button().LoadFromXaml(navigationButtonXAML);
...
_stackLayout.Children.Add(navigationButton);
```

Neste exemplo, uma [`Button`](xref:Xamarin.Forms.Button) instância é criada, com seu [`Text`](xref:Xamarin.Forms.Button.Text) valor de propriedade sendo definido a partir do XAML definido no `string` . O `Button` é então adicionado a um [`StackLayout`](xref:Xamarin.Forms.StackLayout) que foi definido no XAML para a página.

> [!NOTE]
> Os [`LoadFromXaml`](xref:Xamarin.Forms.Xaml.Extensions.LoadFromXaml*) métodos de extensão permitem que um argumento de tipo genérico seja especificado. No entanto, raramente é necessário especificar o argumento de tipo, pois ele será inferido do tipo da instância em que está operando.

O [`LoadFromXaml`](xref:Xamarin.Forms.Xaml.Extensions.LoadFromXaml*) método pode ser usado para aumentar qualquer XAML, com o exemplo a seguir, replanando um [`ContentPage`](xref:Xamarin.Forms.ContentPage) e, em seguida, navegando até ele:

```csharp
using Xamarin.Forms.Xaml;
...

// See the sample for the full XAML string
string pageXAML = "<?xml version=\"1.0\" encoding=\"utf-8\"?>\r\n<ContentPage xmlns=\"http://xamarin.com/schemas/2014/forms\"\nxmlns:x=\"http://schemas.microsoft.com/winfx/2009/xaml\"\nx:Class=\"LoadRuntimeXAML.CatalogItemsPage\"\nTitle=\"Catalog Items\">\n</ContentPage>";

ContentPage page = new ContentPage().LoadFromXaml(pageXAML);
await Navigation.PushAsync(page);
```

## <a name="accessing-elements"></a>Acessando elementos

O carregamento de XAML em tempo de execução com o [`LoadFromXaml`](xref:Xamarin.Forms.Xaml.Extensions.LoadFromXaml*) método não permite o acesso de tipo forte aos elementos XAML que têm nomes de objeto de tempo de execução especificados (usando `x:Name` ). No entanto, esses elementos XAML podem ser recuperados usando o [`FindByName`](xref:Xamarin.Forms.NameScopeExtensions.FindByName*) método e, em seguida, acessados conforme necessário:

```csharp
// See the sample for the full XAML string
string pageXAML = "<?xml version=\"1.0\" encoding=\"utf-8\"?>\r\n<ContentPage xmlns=\"http://xamarin.com/schemas/2014/forms\"\nxmlns:x=\"http://schemas.microsoft.com/winfx/2009/xaml\"\nx:Class=\"LoadRuntimeXAML.CatalogItemsPage\"\nTitle=\"Catalog Items\">\n<StackLayout>\n<Label x:Name=\"monkeyName\"\n />\n</StackLayout>\n</ContentPage>";
ContentPage page = new ContentPage().LoadFromXaml(pageXAML);

Label monkeyLabel = page.FindByName<Label>("monkeyName");
monkeyLabel.Text = "Seated Monkey";
...
```

Neste exemplo, o XAML para um é inconsistente [`ContentPage`](xref:Xamarin.Forms.ContentPage) . Esse XAML inclui um [`Label`](xref:Xamarin.Forms.Label) chamado `monkeyName` , que é recuperado usando o [`FindByName`](xref:Xamarin.Forms.NameScopeExtensions.FindByName*) método, antes de sua [`Text`](xref:Xamarin.Forms.Label.Text) propriedade ser definida.

## <a name="related-links"></a>Links relacionados

- [LoadRuntimeXAML (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-loadruntimexaml)
