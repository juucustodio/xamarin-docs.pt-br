---
title: Carregando XAML em tempo de execução no xamarin. Forms
description: XAML pode ser carregado e analisado em tempo de execução com os métodos de extensão de LoadFromXaml.
ms.prod: xamarin
ms.assetid: 25F73FBF-2DD3-468E-A2D8-0897414F0F4A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/12/2018
ms.openlocfilehash: ce8ba32a1a6a1f69033615558c7ebf15d41e70fe
ms.sourcegitcommit: f890b5ec9b7c2702875070859e1a8cbf6e870e46
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/28/2018
ms.locfileid: "53814091"
---
# <a name="loading-xaml-at-runtime-in-xamarinforms"></a>Carregando XAML em tempo de execução no xamarin. Forms

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://developer.xamarin.com/samples/xamarin-forms/XAML/LoadRuntimeXAML/)

O [ `Xamarin.Forms.Xaml` ](xref:Xamarin.Forms.Xaml) namespace inclui dois [ `LoadFromXaml` ](xref:Xamarin.Forms.Xaml.Extensions.LoadFromXaml*) métodos de extensão que podem ser usados para carregar e analisar o XAML em tempo de execução.

## <a name="background"></a>Informações preliminares

Quando uma classe de XAML de xamarin. Forms é construída, o [ `LoadFromXaml` ](xref:Xamarin.Forms.Xaml.Extensions.LoadFromXaml*) método é chamado indiretamente. Isso ocorre porque o arquivo code-behind para um XAML classe chama o `InitializeComponent` método de seu construtor:

```csharp
public partial class MainPage : ContentPage
{
    public MainPage()
    {
        InitializeComponent();
    }
}
```

Quando o Visual Studio compila um projeto que contém um arquivo XAML, ele analisa o arquivo XAML para gerar um C# arquivo de código (por exemplo, **MainPage.xaml.g.cs**) que contém a definição do `InitializeComponent` método:

```csharp
private void InitializeComponent()
{
    global::Xamarin.Forms.Xaml.Extensions.LoadFromXaml(this, typeof(MainPage));
    ...
}
```

O `InitializeComponent` chamadas de método de [ `LoadFromXaml` ](xref:Xamarin.Forms.Xaml.Extensions.LoadFromXaml*) método para extrair o arquivo XAML (ou seu binário compilado) da biblioteca do .NET Standard. Após a extração, ele inicializa todos os objetos definidos no arquivo XAML, conecta-se-los todos juntos em relações pai-filho, anexa os manipuladores de eventos definidos no código aos eventos definido no arquivo XAML e define a árvore resultante de objetos que o conteúdo das página.

## <a name="loading-xaml-at-runtime"></a>Carregando XAML em tempo de execução

O [ `LoadFromXaml` ](xref:Xamarin.Forms.Xaml.Extensions.LoadFromXaml*) métodos são `public`e, portanto, podem ser chamados de aplicativos xamarin. Forms para carregar e analisar o XAML em tempo de execução. Isso permite cenários como um aplicativo baixando o XAML de um serviço web, criando a exibição necessária do XAML e exibi-lo no aplicativo.

> [!WARNING]
> Carregar XAML em tempo de execução tem um custo de desempenho significativo e geralmente deve ser evitado.

O exemplo de código a seguir mostra um uso simple:

```csharp
using Xamarin.Forms.Xaml;
...

string navigationButtonXAML = "<Button Text=\"Navigate\" />";
Button navigationButton = new Button().LoadFromXaml(navigationButtonXAML);
...
_stackLayout.Children.Add(navigationButton);
```

Neste exemplo, uma [ `Button` ](xref:Xamarin.Forms.Button) instância é criada, com seus [ `Text` ](xref:Xamarin.Forms.Button.Text) definido pelo valor da propriedade sendo definida do XAML no `string`. O `Button` é adicionado a um [ `StackLayout` ](xref:Xamarin.Forms.StackLayout) que foi definido no XAML para a página.

> [!NOTE]
> O [ `LoadFromXaml` ](xref:Xamarin.Forms.Xaml.Extensions.LoadFromXaml*) métodos de extensão permitem que um argumento de tipo genérico a ser especificado. No entanto, raramente é necessário especificar o argumento de tipo, pois ela será inferido do tipo da instância de seu operando.

O [ `LoadFromXaml` ](xref:Xamarin.Forms.Xaml.Extensions.LoadFromXaml*) método pode ser usado para Inflar qualquer XAML, com o exemplo a seguir inflacionando uma [ `ContentPage` ](xref:Xamarin.Forms.ContentPage) e, em seguida, navegando até ele:

```csharp
using Xamarin.Forms.Xaml;
...

// See the sample for the full XAML string
string pageXAML = "<?xml version=\"1.0\" encoding=\"utf-8\"?>\r\n<ContentPage xmlns=\"http://xamarin.com/schemas/2014/forms\"\nxmlns:x=\"http://schemas.microsoft.com/winfx/2009/xaml\"\nx:Class=\"LoadRuntimeXAML.CatalogItemsPage\"\nTitle=\"Catalog Items\">\n</ContentPage>";

ContentPage page = new ContentPage().LoadFromXaml(pageXAML);
await Navigation.PushAsync(page);
```

## <a name="accessing-elements"></a>Acessando elementos

Carregando XAML em tempo de execução com o [ `LoadFromXaml` ](xref:Xamarin.Forms.Xaml.Extensions.LoadFromXaml*) método não permite acesso fortemente tipado aos elementos XAML que especificou os nomes de objeto de tempo de execução (usando `x:Name`). No entanto, esses elementos XAML podem ser recuperados usando o [ `FindByName` ](xref:Xamarin.Forms.NameScopeExtensions.FindByName*) método e, em seguida, acessados conforme necessário:

```csharp
// See the sample for the full XAML string
string pageXAML = "<?xml version=\"1.0\" encoding=\"utf-8\"?>\r\n<ContentPage xmlns=\"http://xamarin.com/schemas/2014/forms\"\nxmlns:x=\"http://schemas.microsoft.com/winfx/2009/xaml\"\nx:Class=\"LoadRuntimeXAML.CatalogItemsPage\"\nTitle=\"Catalog Items\">\n<StackLayout>\n<Label x:Name=\"monkeyName\"\n />\n</StackLayout>\n</ContentPage>";
ContentPage page = new ContentPage().LoadFromXaml(pageXAML);

Label monkeyLabel = page.FindByName<Label>("monkeyName");
monkeyLabel.Text = "Seated Monkey";
...
```

Neste exemplo, o XAML para um [ `ContentPage` ](xref:Xamarin.Forms.ContentPage) está inflado. Esse XAML inclui um [ `Label` ](xref:Xamarin.Forms.Label) denominada `monkeyName`, que é recuperado usando o [ `FindByName` ](xref:Xamarin.Forms.NameScopeExtensions.FindByName*) método, antes que ele tenha [ `Text` ](xref:Xamarin.Forms.Label.Text) propriedade está definida.

## <a name="related-links"></a>Links relacionados

- [LoadRuntimeXAML (amostra)](https://developer.xamarin.com/samples/xamarin-forms/XAML/LoadRuntimeXAML/)
