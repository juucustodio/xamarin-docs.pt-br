---
title: Carregando XAML em tempo de execução no Xamarin. Forms
description: O XAML pode ser carregado e analisado em tempo de execução com os métodos de extensão LoadFromXaml.
ms.prod: xamarin
ms.assetid: 25F73FBF-2DD3-468E-A2D8-0897414F0F4A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/12/2018
ms.openlocfilehash: 71f510cd37d4bed2a5a6077ed63f748ce9894725
ms.sourcegitcommit: ae5557c5024d4b7bd52b2f33cb96114ce2b8e086
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/06/2020
ms.locfileid: "78292032"
---
# <a name="loading-xaml-at-runtime-in-xamarinforms"></a>Carregando XAML em tempo de execução no Xamarin. Forms

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-loadruntimexaml)

O namespace [`Xamarin.Forms.Xaml`](xref:Xamarin.Forms.Xaml) inclui dois métodos de extensão [`LoadFromXaml`](xref:Xamarin.Forms.Xaml.Extensions.LoadFromXaml*) que podem ser usados para carregar e analisar XAML em tempo de execução.

## <a name="background"></a>Segundo plano

Quando uma classe XAML Xamarin. Forms é construída, o método [`LoadFromXaml`](xref:Xamarin.Forms.Xaml.Extensions.LoadFromXaml*) é chamado indiretamente. Isso ocorre porque o arquivo code-behind para uma classe XAML chama o método `InitializeComponent` de seu construtor:

```csharp
public partial class MainPage : ContentPage
{
    public MainPage()
    {
        InitializeComponent();
    }
}
```

Quando o Visual Studio cria um projeto que contém um arquivo XAML, ele analisa o arquivo XAML para gerar C# um arquivo de código (por exemplo, **MainPage.XAML.g.cs**) que contém a definição do método `InitializeComponent`:

```csharp
private void InitializeComponent()
{
    global::Xamarin.Forms.Xaml.Extensions.LoadFromXaml(this, typeof(MainPage));
    ...
}
```

O método `InitializeComponent` chama o método [`LoadFromXaml`](xref:Xamarin.Forms.Xaml.Extensions.LoadFromXaml*) para extrair o arquivo XAML (ou seu binário compilado) da biblioteca .net Standard. Após a extração, ele inicializa todos os objetos definidos no arquivo XAML, conecta-os juntos em relações pai-filho, anexa manipuladores de eventos definidos no código aos eventos definidos no arquivo XAML e define a árvore resultante de objetos como o conteúdo do Web.

## <a name="loading-xaml-at-runtime"></a>Carregando XAML em tempo de execução

Os métodos [`LoadFromXaml`](xref:Xamarin.Forms.Xaml.Extensions.LoadFromXaml*) são `public`e, portanto, podem ser chamados de aplicativos Xamarin. Forms para carregar e analisar XAML em tempo de execução. Isso permite cenários como um aplicativo baixando XAML de um serviço Web, criando a exibição necessária a partir do XAML e exibindo-o no aplicativo.

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

Neste exemplo, uma instância de [`Button`](xref:Xamarin.Forms.Button) é criada, com seu valor de propriedade [`Text`](xref:Xamarin.Forms.Button.Text) sendo definido a partir do XAML definido no `string`. Em seguida, o `Button` é adicionado a um [`StackLayout`](xref:Xamarin.Forms.StackLayout) que foi definido no XAML para a página.

> [!NOTE]
> Os métodos de extensão [`LoadFromXaml`](xref:Xamarin.Forms.Xaml.Extensions.LoadFromXaml*) permitem que um argumento de tipo genérico seja especificado. No entanto, raramente é necessário especificar o argumento de tipo, pois ele será inferido do tipo da instância em que está operando.

O método [`LoadFromXaml`](xref:Xamarin.Forms.Xaml.Extensions.LoadFromXaml*) pode ser usado para inflar qualquer XAML, com o exemplo a seguir, replanando um [`ContentPage`](xref:Xamarin.Forms.ContentPage) e, em seguida, navegando até ele:

```csharp
using Xamarin.Forms.Xaml;
...

// See the sample for the full XAML string
string pageXAML = "<?xml version=\"1.0\" encoding=\"utf-8\"?>\r\n<ContentPage xmlns=\"http://xamarin.com/schemas/2014/forms\"\nxmlns:x=\"http://schemas.microsoft.com/winfx/2009/xaml\"\nx:Class=\"LoadRuntimeXAML.CatalogItemsPage\"\nTitle=\"Catalog Items\">\n</ContentPage>";

ContentPage page = new ContentPage().LoadFromXaml(pageXAML);
await Navigation.PushAsync(page);
```

## <a name="accessing-elements"></a>Acessando elementos

O carregamento de XAML em tempo de execução com o método [`LoadFromXaml`](xref:Xamarin.Forms.Xaml.Extensions.LoadFromXaml*) não permite acesso de tipo forte aos elementos XAML que têm nomes de objeto de tempo de execução especificados (usando `x:Name`). No entanto, esses elementos XAML podem ser recuperados usando o método [`FindByName`](xref:Xamarin.Forms.NameScopeExtensions.FindByName*) e, em seguida, acessados conforme necessário:

```csharp
// See the sample for the full XAML string
string pageXAML = "<?xml version=\"1.0\" encoding=\"utf-8\"?>\r\n<ContentPage xmlns=\"http://xamarin.com/schemas/2014/forms\"\nxmlns:x=\"http://schemas.microsoft.com/winfx/2009/xaml\"\nx:Class=\"LoadRuntimeXAML.CatalogItemsPage\"\nTitle=\"Catalog Items\">\n<StackLayout>\n<Label x:Name=\"monkeyName\"\n />\n</StackLayout>\n</ContentPage>";
ContentPage page = new ContentPage().LoadFromXaml(pageXAML);

Label monkeyLabel = page.FindByName<Label>("monkeyName");
monkeyLabel.Text = "Seated Monkey";
...
```

Neste exemplo, o XAML para um [`ContentPage`](xref:Xamarin.Forms.ContentPage) é inconsistente. Esse XAML inclui um [`Label`](xref:Xamarin.Forms.Label) chamado `monkeyName`, que é recuperado usando o método [`FindByName`](xref:Xamarin.Forms.NameScopeExtensions.FindByName*) , antes de sua propriedade [`Text`](xref:Xamarin.Forms.Label.Text) ser definida.

## <a name="related-links"></a>Links relacionados

- [LoadRuntimeXAML (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-loadruntimexaml)
