---
title: Interatividade do ListView
description: Este artigo explica como adicionar interatividade a um ListView do xamarin. Forms com a implementação de seleções, ações de contexto e puxar para atualizar.
ms.prod: xamarin
ms.assetid: CD14EB90-B08C-4E8F-A314-DA0EEC76E647
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/25/2019
ms.openlocfilehash: aa717792bdaefe24d957c9781934933b67aaf92b
ms.sourcegitcommit: eca3b01098dba004d367292c8b0d74b58c4e1206
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/13/2020
ms.locfileid: "79305328"
---
# <a name="listview-interactivity"></a>Interatividade de ListView

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-interactivity)

A classe [`ListView`](xref:Xamarin.Forms.ListView) Xamarin. Forms dá suporte à interação do usuário com os dados que ele apresenta.

## <a name="selection-and-taps"></a>Seleção e toques

O modo de seleção de [`ListView`](xref:Xamarin.Forms.ListView) é controlado pela definição da propriedade [`ListView.SelectionMode`](xref:Xamarin.Forms.ListView.SelectionMode) como um valor da enumeração [`ListViewSelectionMode`](xref:Xamarin.Forms.ListViewSelectionMode) :

- [`Single`](xref:Xamarin.Forms.ListViewSelectionMode.Single) indica que um único item pode ser selecionado, com o item selecionado sendo realçado. Este é o valor padrão.
- [`None`](xref:Xamarin.Forms.ListViewSelectionMode.None) indica que os itens não podem ser selecionados.

Quando um usuário toca um item, dois eventos são disparados:

- [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) é acionado quando um novo item é selecionado.
- [`ItemTapped`](xref:Xamarin.Forms.ListView.ItemTapped) é acionado quando um item é tocado.

Tocar duas vezes no mesmo item acionará dois eventos de [`ItemTapped`](xref:Xamarin.Forms.ListView.ItemTapped) , mas só disparará um único evento de [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) .

> [!NOTE]
> A classe [`ItemTappedEventArgs`](xref:Xamarin.Forms.ItemTappedEventArgs) , que contém os argumentos do evento [`ItemTapped`](xref:Xamarin.Forms.ListView.ItemTapped) , tem as propriedades [`Group`](xref:Xamarin.Forms.ItemTappedEventArgs.Group) e [`Item`](xref:Xamarin.Forms.ItemTappedEventArgs.Item) e uma propriedade `ItemIndex` cujo valor representa o índice na [`ListView`](xref:Xamarin.Forms.ListView) do item tocado. Da mesma forma, a classe [`SelectedItemChangedEventArgs`](xref:Xamarin.Forms.SelectedItemChangedEventArgs) , que contém os argumentos do evento [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) , tem uma propriedade [`SelectedItem`](xref:Xamarin.Forms.SelectedItemChangedEventArgs.SelectedItem) e uma propriedade `SelectedItemIndex` cujo valor representa o índice na `ListView` do item selecionado.

Quando a propriedade [`SelectionMode`](xref:Xamarin.Forms.ListView.SelectionMode) é definida como [`Single`](xref:Xamarin.Forms.ListViewSelectionMode.Single), os itens na [`ListView`](xref:Xamarin.Forms.ListView) podem ser selecionados, os eventos [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) e [`ItemTapped`](xref:Xamarin.Forms.ListView.ItemTapped) serão disparados e a propriedade [`SelectedItem`](xref:Xamarin.Forms.ListView.SelectedItem) será definida como o valor do item selecionado.

Quando a propriedade [`SelectionMode`](xref:Xamarin.Forms.ListView.SelectionMode) for definida como [`None`](xref:Xamarin.Forms.ListViewSelectionMode.None), os itens na [`ListView`](xref:Xamarin.Forms.ListView) não poderão ser selecionados, o evento [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) não será disparado e a propriedade [`SelectedItem`](xref:Xamarin.Forms.ListView.SelectedItem) permanecerá `null`. No entanto, [`ItemTapped`](xref:Xamarin.Forms.ListView.ItemTapped) eventos ainda serão acionados e o item tocado será realçado brevemente durante o toque.

Quando um item tiver sido selecionado e a propriedade [`SelectionMode`](xref:Xamarin.Forms.ListView.SelectionMode) for alterada de [`Single`](xref:Xamarin.Forms.ListViewSelectionMode.Single) para [`None`](xref:Xamarin.Forms.ListViewSelectionMode.None), a propriedade [`SelectedItem`](xref:Xamarin.Forms.ListView.SelectedItem) será definida como `null` e o evento [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) será acionado com um item de `null`.

As capturas de tela a seguir mostram uma [`ListView`](xref:Xamarin.Forms.ListView) com o modo de seleção padrão:

![](interactivity-images/selection-default.png "ListView with Selection Enabled")

### <a name="disable-selection"></a>Desabilitar seleção

Para desabilitar [`ListView`](xref:Xamarin.Forms.ListView) seleção, defina a propriedade [`SelectionMode`](xref:Xamarin.Forms.ListView.SelectionMode) como [`None`](xref:Xamarin.Forms.ListViewSelectionMode.None):

```xaml
<ListView ... SelectionMode="None" />
```

```csharp
var listView = new ListView { ... SelectionMode = ListViewSelectionMode.None };
```

## <a name="context-actions"></a>Ações de contexto

Muitas vezes, os usuários desejarão agir em um item em um `ListView`. Por exemplo, considere uma lista de emails no aplicativo Mail. No iOS, você pode passar o dedo para excluir uma mensagem:

![](interactivity-images/context-default.png "ListView with Context Actions")

Ações de contexto podem ser implementadas em c# e XAML. Abaixo você encontrará guias específicos para ambos, mas primeiro vamos dar uma olhada em alguns detalhes de implementação fundamentais para ambos.

As ações de contexto são criadas usando elementos de `MenuItem`. Os eventos TAP para `MenuItems` objetos são gerados pelo `MenuItem` em si, não pelo `ListView`. Isso é diferente de como os eventos Tap são tratados para células, em que o `ListView` gera o evento em vez da célula. Como o `ListView` está gerando o evento, seu manipulador de eventos recebe informações importantes, como qual item foi selecionado ou tocado.

Por padrão, uma `MenuItem` não tem como saber a qual célula ela pertence. A propriedade `CommandParameter` está disponível em `MenuItem` para armazenar objetos, como o objeto por trás da `ViewCell`do `MenuItem`. A propriedade `CommandParameter` pode ser definida tanto no XAML quanto C#no.

### <a name="xaml"></a>XAML

`MenuItem` elementos podem ser criados em uma coleção XAML. O XAML a seguir demonstra uma célula personalizada com duas ações de contexto implementadas:

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

No arquivo code-behind, verifique se os métodos de `Clicked` são implementados:

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
> O `NavigationPageRenderer` para Android tem um método `UpdateMenuItemIcon` substituível que pode ser usado para carregar ícones de um `Drawable`personalizado. Essa substituição torna possível usar imagens SVG como ícones em `MenuItem` instâncias no Android.

### <a name="code"></a>Código

As ações de contexto podem ser implementadas em qualquer subclasse de `Cell` (desde que ela não esteja sendo usada como um cabeçalho de grupo), criando instâncias de `MenuItem` e adicionando-as à coleção de `ContextActions` para a célula. Você tem as seguintes propriedades podem ser configuradas para a ação de contexto:

- O **texto** &ndash; cadeia de caracteres que aparece no item de menu.
- **Clicado** &ndash; evento quando o item é clicado.
- **Isdestrutivo** &ndash; (opcional) quando verdadeiro, o item é renderizado de forma diferente no Ios.

Várias ações de contexto podem ser adicionadas a uma célula, no entanto, apenas uma deve ter `IsDestructive` definido como `true`. O código a seguir demonstra como ações de contexto seriam adicionadas a um `ViewCell`:

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

## <a name="pull-to-refresh"></a>Efetuar pull para atualizar

Os usuários já conhecem movendo para baixo em uma lista de dados que será atualizada nessa lista. O controle de [`ListView`](xref:Xamarin.Forms.ListView) dá suporte a essa caixa de saída. Para habilitar a funcionalidade de pull para atualização, defina [`IsPullToRefreshEnabled`](xref:Xamarin.Forms.ListView.IsPullToRefreshEnabled) como `true`:

```xaml
<ListView ...
          IsPullToRefreshEnabled="true" />
```

Este é o código C# equivalente:

```csharp
listView.IsPullToRefreshEnabled = true;
```

Um controle giratório aparece durante a atualização, que é preto por padrão. No entanto, a cor de controle giratório pode ser alterada no iOS e no Android definindo a propriedade `RefreshControlColor` como um [`Color`](xref:Xamarin.Forms.Color):

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

As capturas de tela a seguir mostram pull-to-Refresh depois que o usuário libera o pull, com o controle giratório sendo mostrado enquanto o [`ListView`](xref:Xamarin.Forms.ListView) está atualizando:

![](interactivity-images/refresh-in-progress.png "ListView Pull to Refresh Complete")

[`ListView`](xref:Xamarin.Forms.ListView) aciona o evento [`Refreshing`](xref:Xamarin.Forms.ListView.Refreshing) para iniciar a atualização e a propriedade [`IsRefreshing`](xref:Xamarin.Forms.ListView.IsRefreshing) será definida como `true`. Qualquer código necessário para atualizar o conteúdo do `ListView` deve ser executado pelo manipulador de eventos para o evento `Refreshing` ou pelo método executado pelo [`RefreshCommand`](xref:Xamarin.Forms.ListView.RefreshCommand). Depois que o `ListView` for atualizado, a propriedade `IsRefreshing` deverá ser definida como `false`ou o método [`EndRefresh`](xref:Xamarin.Forms.ListView.EndRefresh) deverá ser chamado para indicar que a atualização foi concluída.

> [!NOTE]
> Ao definir um [`RefreshCommand`](xref:Xamarin.Forms.ListView.RefreshCommand), o método `CanExecute` do comando pode ser especificado para habilitar ou desabilitar o comando.

## <a name="detect-scrolling"></a>Detectar rolagem

[`ListView`](xref:Xamarin.Forms.ListView) define um evento de `Scrolled` que é acionado para indicar que a rolagem ocorreu. O exemplo de XAML a seguir mostra um `ListView` que define um manipulador de eventos para o evento `Scrolled`:

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

Neste exemplo de código, o manipulador de eventos `OnListViewScrolled` é executado quando o evento `Scrolled` é acionado:

```csharp
void OnListViewScrolled(object sender, ScrolledEventArgs e)
{
    Debug.WriteLine("ScrollX: " + e.ScrollX);
    Debug.WriteLine("ScrollY: " + e.ScrollY);  
}
```

O manipulador de eventos `OnListViewScrolled` gera os valores do objeto `ScrolledEventArgs` que acompanha o evento.

## <a name="related-links"></a>Links relacionados

- [Interatividade de ListView (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-interactivity)
