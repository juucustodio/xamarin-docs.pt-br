---
title: ''
description: Este artigo explica como adicionar interatividade a um Xamarin.Forms ListView implementando seleções, ações de contexto e pull para atualização.
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 5142965216b328172ae7fa04cdc0c13590f5ff38
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/28/2020
ms.locfileid: "84139880"
---
# <a name="listview-interactivity"></a>Interatividade do ListView

[![Baixar exemplo ](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-interactivity)

A Xamarin.Forms [`ListView`](xref:Xamarin.Forms.ListView) classe dá suporte à interação do usuário com os dados que ele apresenta.

## <a name="selection-and-taps"></a>Seleção e toques

O [`ListView`](xref:Xamarin.Forms.ListView) modo de seleção é controlado pela definição da [`ListView.SelectionMode`](xref:Xamarin.Forms.ListView.SelectionMode) propriedade como um valor da [`ListViewSelectionMode`](xref:Xamarin.Forms.ListViewSelectionMode) enumeração:

- [`Single`](xref:Xamarin.Forms.ListViewSelectionMode.Single)indica que um único item pode ser selecionado, com o item selecionado sendo realçado. Esse é o valor padrão.
- [`None`](xref:Xamarin.Forms.ListViewSelectionMode.None)indica que os itens não podem ser selecionados.

Quando um usuário toca em um item, dois eventos são acionados:

- [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected)acionado quando um novo item é selecionado.
- [`ItemTapped`](xref:Xamarin.Forms.ListView.ItemTapped)acionado quando um item é tocado.

Tocar duas vezes no mesmo item acionará dois [`ItemTapped`](xref:Xamarin.Forms.ListView.ItemTapped) eventos, mas só disparará um único [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) evento.

> [!NOTE]
> A [`ItemTappedEventArgs`](xref:Xamarin.Forms.ItemTappedEventArgs) classe, que contém os argumentos do evento [`ItemTapped`](xref:Xamarin.Forms.ListView.ItemTapped) , tem [`Group`](xref:Xamarin.Forms.ItemTappedEventArgs.Group) e [`Item`](xref:Xamarin.Forms.ItemTappedEventArgs.Item) Propriedades, e uma `ItemIndex` propriedade cujo valor representa o índice no [`ListView`](xref:Xamarin.Forms.ListView) do item tocado. Da mesma forma, a [`SelectedItemChangedEventArgs`](xref:Xamarin.Forms.SelectedItemChangedEventArgs) classe, que contém os argumentos do evento [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) , tem uma [`SelectedItem`](xref:Xamarin.Forms.SelectedItemChangedEventArgs.SelectedItem) propriedade e uma `SelectedItemIndex` propriedade cujo valor representa o índice no `ListView` do item selecionado.

Quando a [`SelectionMode`](xref:Xamarin.Forms.ListView.SelectionMode) propriedade é definida como [`Single`](xref:Xamarin.Forms.ListViewSelectionMode.Single) , os itens no [`ListView`](xref:Xamarin.Forms.ListView) podem ser selecionados, os [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) [`ItemTapped`](xref:Xamarin.Forms.ListView.ItemTapped) eventos e serão acionados e a [`SelectedItem`](xref:Xamarin.Forms.ListView.SelectedItem) propriedade será definida como o valor do item selecionado.

Quando a [`SelectionMode`](xref:Xamarin.Forms.ListView.SelectionMode) propriedade é definida como [`None`](xref:Xamarin.Forms.ListViewSelectionMode.None) , os itens em [`ListView`](xref:Xamarin.Forms.ListView) não podem ser selecionados, o [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) evento não será disparado e a [`SelectedItem`](xref:Xamarin.Forms.ListView.SelectedItem) Propriedade permanecerá `null` . No entanto, [`ItemTapped`](xref:Xamarin.Forms.ListView.ItemTapped) os eventos ainda serão acionados e o item tocado será realçado brevemente durante o toque.

Quando um item tiver sido selecionado e a [`SelectionMode`](xref:Xamarin.Forms.ListView.SelectionMode) propriedade for alterada de [`Single`](xref:Xamarin.Forms.ListViewSelectionMode.Single) para [`None`](xref:Xamarin.Forms.ListViewSelectionMode.None) , a [`SelectedItem`](xref:Xamarin.Forms.ListView.SelectedItem) propriedade será definida como `null` e o [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) evento será acionado com um `null` Item.

As capturas de tela a seguir mostram um [`ListView`](xref:Xamarin.Forms.ListView) com o modo de seleção padrão:

![](interactivity-images/selection-default.png "ListView with Selection Enabled")

### <a name="disable-selection"></a>Desabilitar seleção

Para desabilitar a [`ListView`](xref:Xamarin.Forms.ListView) seleção, defina a [`SelectionMode`](xref:Xamarin.Forms.ListView.SelectionMode) propriedade como [`None`](xref:Xamarin.Forms.ListViewSelectionMode.None) :

```xaml
<ListView ... SelectionMode="None" />
```

```csharp
var listView = new ListView { ... SelectionMode = ListViewSelectionMode.None };
```

## <a name="context-actions"></a>Ações de contexto

Muitas vezes, os usuários desejarão agir em um item em um `ListView` . Por exemplo, considere uma lista de emails no aplicativo de email. No iOS, você pode passar o dedo para excluir uma mensagem:

![](interactivity-images/context-default.png "ListView with Context Actions")

As ações de contexto podem ser implementadas em C# e XAML. Abaixo, você encontrará guias específicos para ambos, mas primeiro vamos dar uma olhada em alguns detalhes da implementação da chave para ambos.

As ações de contexto são criadas usando `MenuItem` elementos. Os eventos TAP para `MenuItems` objetos são gerados por `MenuItem` si só, não pelo `ListView` . Isso é diferente de como os eventos Tap são tratados para células, em que o `ListView` gera o evento em vez da célula. Como o `ListView` está gerando o evento, seu manipulador de eventos recebe informações importantes, como qual item foi selecionado ou tocado.

Por padrão, um `MenuItem` não tem como saber a qual célula ela pertence. A `CommandParameter` propriedade está disponível em `MenuItem` para armazenar objetos, como o objeto por trás do `MenuItem` `ViewCell` . A `CommandParameter` propriedade pode ser definida tanto em XAML quanto em C#.

### <a name="xaml"></a>XAML

`MenuItem`elementos podem ser criados em uma coleção XAML. O XAML a seguir demonstra uma célula personalizada com duas ações de contexto implementadas:

```xaml
<ListView x:Name="ContextDemoList">
  <ListView.ItemTemplate>
    <DataTemplate>
      <ViewCell>
         <ViewCell.ContextActions>
            <MenuItem Clicked="OnMore"
                      CommandParameter="{Binding .}"
                      Text="More" />
            <MenuItem Clicked="OnDelete"
                      CommandParameter="{Binding .}"
                      Text="Delete" IsDestructive="True" />
         </ViewCell.ContextActions>
         <StackLayout Padding="15,0">
              <Label Text="{Binding title}" />
         </StackLayout>
      </ViewCell>
    </DataTemplate>
  </ListView.ItemTemplate>
</ListView>
```

No arquivo code-behind, verifique se os `Clicked` métodos são implementados:

```csharp
public void OnMore (object sender, EventArgs e)
{
    var mi = ((MenuItem)sender);
    DisplayAlert("More Context Action", mi.CommandParameter + " more context action", "OK");
}

public void OnDelete (object sender, EventArgs e)
{
    var mi = ((MenuItem)sender);
    DisplayAlert("Delete Context Action", mi.CommandParameter + " delete context action", "OK");
}
```

> [!NOTE]
> O `NavigationPageRenderer` para Android tem um método substituível `UpdateMenuItemIcon` que pode ser usado para carregar ícones de um personalizado `Drawable` . Essa substituição torna possível usar imagens SVG como ícones em `MenuItem` instâncias no Android.

### <a name="code"></a>Código

As ações de contexto podem ser implementadas em qualquer `Cell` subclasse (desde que ela não esteja sendo usada como um cabeçalho de grupo), criando `MenuItem` instâncias e adicionando-as à `ContextActions` coleção da célula. Você tem as seguintes propriedades que podem ser configuradas para a ação de contexto:

- **Texto** &ndash; de a cadeia de caracteres que aparece no item de menu.
- **Clicado** &ndash; em o evento quando o item é clicado.
- **Isdestrutivo** &ndash; (opcional) quando true, o item é renderizado de forma diferente no iOS.

Várias ações de contexto podem ser adicionadas a uma célula, no entanto, apenas uma deve ter `IsDestructive` definido como `true` . O código a seguir demonstra como as ações de contexto seriam adicionadas a um `ViewCell` :

```csharp
var moreAction = new MenuItem { Text = "More" };
moreAction.SetBinding (MenuItem.CommandParameterProperty, new Binding ("."));
moreAction.Clicked += async (sender, e) =>
{
    var mi = ((MenuItem)sender);
    Debug.WriteLine("More Context Action clicked: " + mi.CommandParameter);
};

var deleteAction = new MenuItem { Text = "Delete", IsDestructive = true }; // red background
deleteAction.SetBinding (MenuItem.CommandParameterProperty, new Binding ("."));
deleteAction.Clicked += async (sender, e) =>
{
    var mi = ((MenuItem)sender);
    Debug.WriteLine("Delete Context Action clicked: " + mi.CommandParameter);
};
// add to the ViewCell's ContextActions property
ContextActions.Add (moreAction);
ContextActions.Add (deleteAction);
```

## <a name="pull-to-refresh"></a>Puxar para atualizar

Os usuários esperam que a recepção de uma lista de dados atualizará essa lista. O [`ListView`](xref:Xamarin.Forms.ListView) controle dá suporte a isso de pronto para uso. Para habilitar a funcionalidade de pull para atualização, defina [`IsPullToRefreshEnabled`](xref:Xamarin.Forms.ListView.IsPullToRefreshEnabled) como `true` :

```xaml
<ListView ...
          IsPullToRefreshEnabled="true" />
```

Este é o código C# equivalente:

```csharp
listView.IsPullToRefreshEnabled = true;
```

Um controle giratório aparece durante a atualização, que é preto por padrão. No entanto, a cor de controle giratório pode ser alterada no iOS e no Android definindo a `RefreshControlColor` propriedade como um [`Color`](xref:Xamarin.Forms.Color) :

```xaml
<ListView ...
          IsPullToRefreshEnabled="true"
          RefreshControlColor="Red" />
```

Este é o código C# equivalente:

```csharp
listView.RefreshControlColor = Color.Red;
```

As capturas de tela a seguir mostram o pull-to-Refresh à medida que o usuário está puxando:

![](interactivity-images/refresh-start.png "ListView Pull to Refresh In-Progress")

As capturas de tela a seguir mostram o pull-to-Refresh depois que o usuário lançou o pull, com o controle giratório exibido enquanto o [`ListView`](xref:Xamarin.Forms.ListView) está atualizando:

![](interactivity-images/refresh-in-progress.png "ListView Pull to Refresh Complete")

[`ListView`](xref:Xamarin.Forms.ListView)aciona o [`Refreshing`](xref:Xamarin.Forms.ListView.Refreshing) evento para iniciar a atualização e a [`IsRefreshing`](xref:Xamarin.Forms.ListView.IsRefreshing) propriedade será definida como `true` . Qualquer código necessário para atualizar o conteúdo de `ListView` deve ser executado pelo manipulador de eventos para o `Refreshing` evento ou pelo método executado pelo [`RefreshCommand`](xref:Xamarin.Forms.ListView.RefreshCommand) . Depois que o `ListView` for atualizado, a `IsRefreshing` Propriedade deverá ser definida como `false` , ou o [`EndRefresh`](xref:Xamarin.Forms.ListView.EndRefresh) método deverá ser chamado para indicar que a atualização foi concluída.

> [!NOTE]
> Ao definir um [`RefreshCommand`](xref:Xamarin.Forms.ListView.RefreshCommand) , o `CanExecute` método do comando pode ser especificado para habilitar ou desabilitar o comando.

## <a name="detect-scrolling"></a>Detectar rolagem

[`ListView`](xref:Xamarin.Forms.ListView)define um `Scrolled` evento que é acionado para indicar que a rolagem ocorreu. O exemplo de XAML a seguir mostra um `ListView` que define um manipulador de eventos para o `Scrolled` evento:

```xaml
<ListView Scrolled="OnListViewScrolled">
    ...
</ListView>
```

Este é o código C# equivalente:

```csharp
ListView listView = new ListView();
listView.Scrolled += OnListViewScrolled;
```

Neste exemplo de código, o `OnListViewScrolled` manipulador de eventos é executado quando o evento é disparado `Scrolled` :

```csharp
void OnListViewScrolled(object sender, ScrolledEventArgs e)
{
    Debug.WriteLine("ScrollX: " + e.ScrollX);
    Debug.WriteLine("ScrollY: " + e.ScrollY);  
}
```

O `OnListViewScrolled` manipulador de eventos gera os valores do `ScrolledEventArgs` objeto que acompanha o evento.

## <a name="related-links"></a>Links relacionados

- [Interatividade de ListView (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-interactivity)
