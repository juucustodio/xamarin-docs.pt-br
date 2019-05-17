---
title: Pesquisa do Shell do Xamarin.Forms
description: Os aplicativos Shell do Xamarin.Forms podem usar a funcionalidade de pesquisa integrada, fornecida por uma caixa de pesquisa que pode ser adicionada à parte superior de cada página.
ms.prod: xamarin
ms.assetid: F8F9471D-6771-4D23-96C0-2B79473A06D4
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/06/2019
ms.openlocfilehash: acaa847b61443eff480e2b39e388f5df9de06e42
ms.sourcegitcommit: 9d90a26cbe13ebd106f55ba4a5445f28d9c18a1a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65054416"
---
# <a name="xamarinforms-shell"></a>Shell do Xamarin.Forms

![](~/media/shared/preview.png "Esta API está atualmente em pré-lançamento")

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://github.com/xamarin/xamarin-forms-samples/tree/forms40/UserInterface/Xaminals/)

O Shell do Xamarin.Forms inclui a funcionalidade de pesquisa integrada fornecida pela classe `SearchHandler`. A funcionalidade de pesquisa pode ser adicionada a uma página definindo a propriedade anexada `Shell.SearchHandler` como um objeto `SearchHandler` da subclasse. Isso faz com que uma caixa de pesquisa seja adicionada na parte superior da página:

[![Captura de tela de um SearchHandler do Shell no iOS e Android](search-images/searchhandler.png "SearchHandler do Shell")](search-images/searchhandler-large.png#lightbox "SearchHandler do Shell")

Quando uma consulta é inserida na caixa de pesquisa, a área de sugestões de pesquisa pode ser preenchida com dados:

[![Captura de tela dos resultados da pesquisa em um SearchHandler do Shell no iOS e Android](search-images/search-suggestions.png "Resultados da pesquisa no SearchHandler do Shell")](search-images/search-suggestions-large.png#lightbox "Resultados da pesquisa no SearchHandler do Shell")

Em seguida, quando um resultado de pesquisa é selecionado, o aplicativo pode responder apropriadamente, por exemplo, navegando para outra página.

## <a name="searchhandler-class"></a>Classe SearchHandler

A classe `SearchHandler` define as seguintes propriedades que controlam sua aparência e seu comportamento:

- `ClearIcon`, do tipo [`ImageSource`](xref:Xamarin.Forms.ImageSource), o ícone exibido para limpar o conteúdo da caixa de pesquisa.
- `ClearIconHelpText`, do tipo `string`, o texto acessível de ajuda para o ícone de limpeza.
- `ClearIconName`, do tipo `string`, o nome do ícone de limpeza para uso com leitores de tela.
- `ICommand`, do tipo `ClearPlaceholderIcon`, que é executado quando `ClearPlaceholderCommand` é tocado.
- `ClearPlaceholderCommandParameter`, do tipo `object`, que é o parâmetro passado para `ClearPlaceholderCommand`.
- `ClearPlaceholderEnabled`, do tipo `bool`, que determina se o `ClearPlaceholderCommand` pode ser executado. O valor padrão é `true`.
- `ClearPlaceholderHelpText`, do tipo `string`, o texto acessível de ajuda para o ícone de espaço reservado de limpeza.
- `ClearPlaceholderIcon`, do tipo [`ImageSource`](xref:Xamarin.Forms.ImageSource), o ícone de espaço reservado de limpeza exibido quando a caixa de pesquisa está vazia.
- `ClearPlaceholderName`, do tipo `string`, o nome do ícone de espaço reservado de limpeza para uso com leitores de tela.
- `Command`, do tipo `ICommand`, que é executado quando a consulta de pesquisa é confirmada.
- `CommandParameter`, do tipo `object`, que é o parâmetro passado para `Command`.
- `DisplayMemberName`, do tipo `string`, que representa o nome ou caminho da propriedade que é exibida para cada item de dados na coleção `ItemsSource`.
- `IsSearchEnabled`, do tipo `bool`, que representa o estado habilitado da caixa de pesquisa. O valor padrão é `true`.
- `ItemsSource`, do tipo `IEnumerable`, especifica a coleção de itens a serem exibidos na área de sugestão e tem um valor padrão de `null`.
- `ItemTemplate`, do tipo [`DataTemplate`](xref:Xamarin.Forms.DataTemplate), especifica o modelo a ser aplicado em cada item na coleção de itens a ser exibida na área de sugestão.
- `Placeholder`, do tipo `string`, o texto a ser exibido quando a caixa de pesquisa está vazia.
- `Query`, do tipo `string`, o texto inserido pelo usuário na caixa de pesquisa.
- `QueryIcon`, do tipo [`ImageSource`](xref:Xamarin.Forms.ImageSource), o ícone usado para indicar ao usuário que a pesquisa está disponível.
- `QueryIconHelpText`, do tipo `string`, o texto de ajuda acessível para o ícone de consulta.
- `QueryIconName`, do tipo `string`, o nome do ícone de consulta para uso com leitores de tela.
- `SearchBoxVisibility`, do tipo `SearchBoxVisibility`, a visibilidade da caixa de pesquisa. Por padrão, a caixa de pesquisa fica visível e totalmente expandida.
- `SelectedItem`, do tipo `object`, o item selecionado nos resultados da pesquisa. Essa propriedade é somente leitura e tem um valor padrão de `null`.
- `ShowsResults`, do tipo `bool`, indica se os resultados da pesquisa devem ser esperados na área de sugestão na entrada de texto. O valor padrão é `false`.

Todas essas propriedades são apoiadas por objetos [`BindableProperty`](xref:Xamarin.Forms.BindableProperty), o que significa que essas propriedades podem ser o destino de vinculações de dados.

Além disso, a classe `SearchHandler` fornece os seguintes métodos substituíveis:

- `OnClearPlaceholderClicked`, que é chamado sempre que `ClearPlaceholderIcon` é tocado.
- `OnItemSelected`, que é chamado sempre que um resultado de pesquisa é selecionado pelo usuário.
- `Query`, que é chamado quando a propriedade `OnQueryChanged` é alterada.
- `OnQueryConfirmed`, que é chamado sempre que o usuário pressiona ou confirma a consulta na caixa de pesquisa.

Quando o usuário inserir uma consulta na caixa de pesquisa, a propriedade `Query` será atualizada e, em cada atualização, o método `OnQueryChanged` será executado. Esse método pode ser usado para atualizar a área de sugestões que aparece abaixo da caixa de pesquisa. Quando o usuário seleciona um resultado da área de sugestões, o método `OnItemSelected` é executado.

## <a name="create-a-searchhandler"></a>Criar um SearchHandler

A funcionalidade de pesquisa pode ser adicionada a um aplicativo Shell criando subclasses da classe `SearchHandler` e substituindo os métodos `OnQueryChanged` e `OnItemSelected`:

```csharp
public class MonkeySearchHandler : SearchHandler
{
    protected override void OnQueryChanged(string oldValue, string newValue)
    {
        base.OnQueryChanged(oldValue, newValue);

        if (string.IsNullOrWhiteSpace(newValue))
        {
            ItemsSource = null;
        }
        else
        {
            ItemsSource = MonkeyData.Monkeys
                .Where(monkey => monkey.Name.ToLower().Contains(newValue.ToLower()))
                .ToList<Animal>();
        }
    }

    protected override async void OnItemSelected(object item)
    {
        base.OnItemSelected(item);

        // Note: strings will be URL encoded for navigation (e.g. "Blue Monkey" becomes "Blue%20Monkey"). Therefore, decode at the receiver.
        await (App.Current.MainPage as Xamarin.Forms.Shell).GoToAsync($"monkeydetails?name={((Animal)item).Name}");
    }
}
```

A substituição de `OnQueryChanged` tem dois argumentos: `oldValue`, que contém a consulta de pesquisa anterior, e `newValue`, que contém a consulta de pesquisa atual. A área de sugestões de pesquisa pode ser atualizada, definindo-se a propriedade `SearchHandler.ItemsSource` como uma coleção `IEnumerable` que contém itens que correspondem à consulta de pesquisa atual.

Quando um resultado de pesquisa é selecionado pelo usuário, a substituição de `OnItemSelected` é executada, e a propriedade `SelectedItem` é definida. Nesse exemplo, o método navega para outra página que exibe os dados sobre o `Animal` selecionado. Saiba mais sobre a navegação na [navegação do Shell do Xamarin.Forms](navigation.md).

> [!NOTE]
> As propriedades `SearchHandler` adicionais podem ser definidas para controlar a aparência da caixa de pesquisa.

## <a name="consume-a-searchhandler"></a>Criar um SearchHandler

O `SearchHandler` com subclasse pode ser consumido definindo a propriedade anexada `Shell.SearchHandler` como um objeto do tipo de subclasse:

```xaml
<ContentPage ...
             xmlns:controls="clr-namespace:Xaminals.Controls">
    <Shell.SearchHandler>
        <controls:MonkeySearchHandler Placeholder="Enter search term"
                                      ShowsResults="true"
                                      DisplayMemberName="Name" />
    </Shell.SearchHandler>
    ...
</ContentPage>
```

Este é o código C# equivalente:

```csharp
Shell.SetSearchHandler(this, new MonkeySearchHandler
{
    Placeholder = "Enter search term",
    ShowsResults = true,
    DisplayMemberName = "Name"
});
```

O método `MonkeySearchHandler.OnQueryChanged` retorna um `List` de objetos `Animal`. A propriedade `DisplayMemberName` é definida como a propriedade `Name` de cada objeto `Animal` e, portanto, os dados exibidos na área de sugestões serão cada nome de animal.

A propriedade `ShowsResults` é definida como `true`, de modo que as sugestões de pesquisa são exibidas enquanto o usuário insere uma consulta de pesquisa:

[![Captura de tela dos resultados da pesquisa em um SearchHandler do Shell no iOS e Android](search-images/search-results.png "Resultados da pesquisa no SearchHandler do Shell")](search-images/search-results-large.png#lightbox "Resultados da pesquisa no SearchHandler do Shell")

À medida que a consulta de pesquisa muda, a área de sugestões da pesquisa é atualizada:

[![Captura de tela dos resultados da pesquisa em um SearchHandler do Shell no iOS e Android](search-images/search-results-change.png "Resultados da pesquisa no SearchHandler do Shell")](search-images/search-results-change-large.png#lightbox "Resultados da pesquisa no SearchHandler do Shell")

Quando um resultado de pesquisa é selecionado, ocorre a navegação até `MonkeyDetailPage`, e os dados sobre o macaco selecionado são exibidos:

[![Captura de tela de detalhes do macaco no iOS e Android](search-images/detailpage.png "Detalhes do macaco")](search-images/detailpage-large.png#lightbox "Detalhes do macaco")

## <a name="define-search-results-item-appearance"></a>Definir a aparência do item dos resultados da pesquisa

Além de exibir os dados `string` nos resultados da pesquisa, a aparência de cada item do resultado da pesquisa pode ser definida configurando a propriedade `SearchHandler.ItemTemplate` como [`DataTemplate`](xref:Xamarin.Forms.DataTemplate):

```xaml
<ContentPage ...
             xmlns:controls="clr-namespace:Xaminals.Controls">    
    <Shell.SearchHandler>
        <controls:MonkeySearchHandler Placeholder="Enter search term"
                                      ShowsResults="true">
            <controls:MonkeySearchHandler.ItemTemplate>
                <DataTemplate>
                    <Grid Padding="10">
                        <Grid.ColumnDefinitions>
                            <ColumnDefinition Width="0.15*" />
                            <ColumnDefinition Width="0.85*" />
                        </Grid.ColumnDefinitions>
                        <Image Source="{Binding ImageUrl}"
                               Aspect="AspectFill"
                               HeightRequest="40"
                               WidthRequest="40" />
                        <Label Grid.Column="1"
                               Text="{Binding Name}"
                               FontAttributes="Bold" />
                    </Grid>
                </DataTemplate>
            </controls:MonkeySearchHandler.ItemTemplate>
       </controls:MonkeySearchHandler>
    </Shell.SearchHandler>
    ...
</ContentPage>
```

Este é o código C# equivalente:

```csharp
Shell.SetSearchHandler(this, new MonkeySearchHandler
{
    Placeholder = "Enter search term",
    ShowsResults = true,
    DisplayMemberName = "Name",
    ItemTemplate = new DataTemplate(() =>
    {
        Grid grid = new Grid { Padding = 10 };
        grid.ColumnDefinitions.Add(new ColumnDefinition { Width = new GridLength(0.15, GridUnitType.Star) });
        grid.ColumnDefinitions.Add(new ColumnDefinition { Width = new GridLength(0.85, GridUnitType.Star) });

        Image image = new Image { Aspect = Aspect.AspectFill, HeightRequest = 40, WidthRequest = 40 };
        image.SetBinding(Image.SourceProperty, "ImageUrl");
        Label nameLabel = new Label { FontAttributes = FontAttributes.Bold };
        nameLabel.SetBinding(Label.TextProperty, "Name");

        grid.Children.Add(image);
        grid.Children.Add(nameLabel, 1, 0);
        return grid;
    })
});
```

Os elementos especificados em [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) definem a aparência de cada item na área de sugestões. Neste exemplo, o layout dentro do `DataTemplate` é gerenciado por um [`Grid`](xref:Xamarin.Forms.Grid). O `Grid` contém um objeto [`Image`](xref:Xamarin.Forms.Image) e um objeto [`Label`](xref:Xamarin.Forms.Label), que se ligam a propriedades de cada objeto `Monkey`.

As capturas de tela a seguir mostram o resultado dos modelos de cada item na área de sugestões:

[![Captura de tela do modelo dos resultados da pesquisa em um SearchHandler do Shell no iOS e Android](search-images/search-results-template.png "Modelo dos resultados da pesquisa no SearchHandler do Shell")](search-images/search-results-template-large.png#lightbox "Resultados da pesquisa no SearchHandler do Shell")

Para obter mais informações sobre modelos de dados, confira [Modelos de dados do Xamarin.Forms](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md).

## <a name="searchbox-visibility"></a>Visibilidade da SearchBox

Quando uma caixa de pesquisa é adicionada na parte superior de uma página, por padrão, essa caixa fica visível e totalmente expandida. No entanto, esse comportamento pode ser alterado definindo a propriedade `SearchHandler.SearchBoxVisibility` para um dos membros da enumeração `SearchBoxVisibility`:

- `Hidden` – a caixa de pesquisa não está visível ou acessível.
- `Collapsible` – a caixa de pesquisa está oculta até que o usuário execute uma ação para mostrá-la.
- `Expanded` – a caixa de pesquisa está visível e totalmente expandida.

O exemplo a seguir mostra como ocultar a caixa de pesquisa:

```xaml
<ContentPage ...
             xmlns:controls="clr-namespace:Xaminals.Controls">
    <Shell.SearchHandler>
        <controls:MonkeySearchHandler SearchBoxVisibility="Hidden"
                                      ... />
    </Shell.SearchHandler>
    ...
</ContentPage>
```

## <a name="related-links"></a>Links relacionados

- [Xaminals (exemplo)](https://github.com/xamarin/xamarin-forms-samples/tree/forms40/UserInterface/Xaminals/)
- [Navegação do Shell do Xamarin.Forms](navigation.md)
