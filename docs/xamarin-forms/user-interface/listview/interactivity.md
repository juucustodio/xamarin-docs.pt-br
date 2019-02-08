---
title: Interatividade do ListView
description: Este artigo explica como adicionar interatividade a um ListView do xamarin. Forms com a implementação de seleções, ações de contexto e puxar para atualizar.
ms.prod: xamarin
ms.assetid: CD14EB90-B08C-4E8F-A314-DA0EEC76E647
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/14/2018
ms.openlocfilehash: 939df6cfd17de82e28958363cfa51cd199f928cb
ms.sourcegitcommit: 93c9fe61eb2cdfa530960b4253eb85161894c882
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55831685"
---
# <a name="listview-interactivity"></a>Interatividade do ListView

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/ListView/interactivity)

[`ListView`](xref:Xamarin.Forms.ListView) dá suporte a interagir com os dados que ele apresenta.

<a name="selectiontaps" />

## <a name="selection--taps"></a>Toques de & seleção

O [ `ListView` ](xref:Xamarin.Forms.ListView) modo de seleção é controlado pela configuração de [ `ListView.SelectionMode` ](xref:Xamarin.Forms.ListView.SelectionMode) propriedade um valor a [ `ListViewSelectionMode` ](xref:Xamarin.Forms.ListViewSelectionMode) enumeração:

- [`Single`](xref:Xamarin.Forms.ListViewSelectionMode.Single) indica que um único item pode ser selecionado, com o item selecionado que está sendo realçado. Este é o valor padrão.
- [`None`](xref:Xamarin.Forms.ListViewSelectionMode.None) indica que os itens não podem ser selecionados.

Quando um usuário toca um item, dois eventos são disparados:

- [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) Acionado quando um novo item é selecionado.
- [`ItemTapped`](xref:Xamarin.Forms.ListView.ItemTapped) Acionado quando um item é tocado.

> [!NOTE]
> Tocar duas vezes o mesmo item será disparado duas [ `ItemTapped` ](xref:Xamarin.Forms.ListView.ItemTapped) eventos, mas será apenas acionar uma única [ `ItemSelected` ](xref:Xamarin.Forms.ListView.ItemSelected) eventos.

Quando o [ `SelectionMode` ](xref:Xamarin.Forms.ListView.SelectionMode) estiver definida como [ `Single` ](xref:Xamarin.Forms.ListViewSelectionMode.Single), os itens no [ `ListView` ](xref:Xamarin.Forms.ListView) pode ser selecionado, a [ `ItemSelected` ](xref:Xamarin.Forms.ListView.ItemSelected) e [ `ItemTapped` ](xref:Xamarin.Forms.ListView.ItemTapped) eventos serão disparados e o [ `SelectedItem` ](xref:Xamarin.Forms.ListView.SelectedItem) propriedade será definida como o valor do item selecionado.

Quando o [ `SelectionMode` ](xref:Xamarin.Forms.ListView.SelectionMode) estiver definida como [ `None` ](xref:Xamarin.Forms.ListViewSelectionMode.None), os itens no [ `ListView` ](xref:Xamarin.Forms.ListView) não pode ser selecionado, a [ `ItemSelected` ](xref:Xamarin.Forms.ListView.ItemSelected) evento não será disparado e o [ `SelectedItem` ](xref:Xamarin.Forms.ListView.SelectedItem) propriedade permanecerá `null`. No entanto, [ `ItemTapped` ](xref:Xamarin.Forms.ListView.ItemTapped) eventos ainda serão acionados e o item tocado será realçado brevemente durante o toque.

Quando um item foi selecionado e o [ `SelectionMode` ](xref:Xamarin.Forms.ListView.SelectionMode) propriedade é alterada de [ `Single` ](xref:Xamarin.Forms.ListViewSelectionMode.Single) para [ `None` ](xref:Xamarin.Forms.ListViewSelectionMode.None), o [ `SelectedItem` ](xref:Xamarin.Forms.ListView.SelectedItem) propriedade será definida como `null` e o [ `ItemSelected` ](xref:Xamarin.Forms.ListView.ItemSelected) evento será acionado com um `null` item.

As capturas de tela a seguir mostram uma [ `ListView` ](xref:Xamarin.Forms.ListView) com o modo de seleção padrão:

![](interactivity-images/selection-default.png "ListView com seleção habilitada")

### <a name="disabling-selection"></a>Desabilitar seleção

Para desabilitar [ `ListView` ](xref:Xamarin.Forms.ListView) conjunto de seleção a [ `SelectionMode` ](xref:Xamarin.Forms.ListView.SelectionMode) propriedade [ `None` ](xref:Xamarin.Forms.ListViewSelectionMode.None):

```xaml
<ListView ... SelectionMode="None" />
```

```csharp
var listView = new ListView { ... SelectionMode = ListViewSelectionMode.None };
```

<a name="Context_Actions" />

## <a name="context-actions"></a>Ações de contexto

Muitas vezes, os usuários desejarão agir em um item em um `ListView`. Por exemplo, considere uma lista de emails no aplicativo Mail. No iOS, você pode passar para excluir uma mensagem::

![](interactivity-images/context-default.png "ListView com ações de contexto")

Ações de contexto podem ser implementadas em c# e XAML. Abaixo você encontrará guias específicos para ambos, mas primeiro vamos dar uma olhada em alguns detalhes de implementação fundamentais para ambos.

Ações de contexto são criadas usando `MenuItem`s. Eventos de toque para MenuItems são acionados por MenuItem em si, não o ListView. Isso é diferente de como os eventos de toque são tratados para células, onde o ListView gera o evento em vez da célula. Porque o ListView está gerando o evento, seu manipulador de eventos recebe informações de chave, como o qual o item foi selecionado ou tocado.

Por padrão, um MenuItem não tem nenhuma maneira de saber qual célula pertence. `CommandParameter` está disponível em `MenuItem` para armazenar objetos, como o objeto por trás ViewCell de MenuItem. `CommandParameter` pode ser definido em XAML e c#.

### <a name="c"></a>C#  

Ações de contexto podem ser implementadas em qualquer `Cell` subclasse (contanto que ele não está sendo usado como um cabeçalho de grupo), criando `MenuItem`s e adicioná-los para o `ContextActions` coleção para a célula. Você tem as seguintes propriedades podem ser configuradas para a ação de contexto:

* **Texto** &ndash; a cadeia de caracteres que aparece no item de menu.
* **Clicado** &ndash; o evento quando o item é clicado.
* **IsDestructive** &ndash; (opcional) quando for verdadeiro o item será renderizado da maneira diferente, no iOS.

Várias ações de contexto podem ser adicionadas a uma célula, no entanto, somente um deve ter `IsDestructive` definido como `true`. O código a seguir demonstra como as ações de contexto seriam adicionadas a um `ViewCell`:

```csharp
var moreAction = new MenuItem { Text = "More" };
moreAction.SetBinding (MenuItem.CommandParameterProperty, new Binding ("."));
moreAction.Clicked += async (sender, e) => {
    var mi = ((MenuItem)sender);
    Debug.WriteLine("More Context Action clicked: " + mi.CommandParameter);
};

var deleteAction = new MenuItem { Text = "Delete", IsDestructive = true }; // red background
deleteAction.SetBinding (MenuItem.CommandParameterProperty, new Binding ("."));
deleteAction.Clicked += async (sender, e) => {
    var mi = ((MenuItem)sender);
    Debug.WriteLine("Delete Context Action clicked: " + mi.CommandParameter);
};
// add to the ViewCell's ContextActions property
ContextActions.Add (moreAction);
ContextActions.Add (deleteAction);
```

### <a name="xaml"></a>XAML

`MenuItem`s também pode ser criado declarativamente em uma coleção de XAML. O XAML a seguir demonstra uma célula personalizada com duas ações de contexto implementadas:

```xaml
<ListView x:Name="ContextDemoList">
  <ListView.ItemTemplate>
    <DataTemplate>
      <ViewCell>
         <ViewCell.ContextActions>
            <MenuItem Clicked="OnMore" CommandParameter="{Binding .}"
               Text="More" />
            <MenuItem Clicked="OnDelete" CommandParameter="{Binding .}"
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

No arquivo code-behind, verifique se o `Clicked` métodos são implementados:

```csharp
public void OnMore (object sender, EventArgs e) {
    var mi = ((MenuItem)sender);
    DisplayAlert("More Context Action", mi.CommandParameter + " more context action", "OK");
}

public void OnDelete (object sender, EventArgs e) {
    var mi = ((MenuItem)sender);
    DisplayAlert("Delete Context Action", mi.CommandParameter + " delete context action", "OK");
}
```

> [!NOTE]
> O `NavigationPageRenderer` para Android tem uma substituível `UpdateMenuItemIcon` método que pode ser usado para carregar ícones de personalizado `Drawable`. Essa substituição torna possível usar as imagens de SVG como ícones em `MenuItem` instâncias no Android.

<a name="Pull_to_Refresh" />

## <a name="pull-to-refresh"></a>Efetue pull para atualizar

Os usuários já conhecem movendo para baixo em uma lista de dados que será atualizada nessa lista. [`ListView`](xref:Xamarin.Forms.ListView) dá suporte a esse out-of-the-box. Para habilitar a funcionalidade de puxar para atualizar, defina [ `IsPullToRefreshEnabled` ](xref:Xamarin.Forms.ListView.IsPullToRefreshEnabled) para `true`:

```xaml
<ListView ...
          IsPullToRefreshEnabled="true" />
```

O código c# equivalente é:

```csharp
listView.IsPullToRefreshEnabled = true;
```

Um controle giratório aparece durante a atualização, o que é preta por padrão. No entanto, a cor do controle giratório pode ser alterada no iOS e Android, definindo o `RefreshControlColor` propriedade para um [ `Color` ](xref:Xamarin.Forms.Color):

```xaml
<ListView ...
          IsPullToRefreshEnabled="true"
          RefreshControlColor="Red" />
```

O código c# equivalente é:

```csharp
listView.RefreshControlColor = Color.Red;
```

As capturas de tela a seguir mostram puxar para atualizar como o usuário está obtendo:

![](interactivity-images/refresh-start.png "ListView Pull para atualizar em andamento")

As capturas de tela a seguir mostram puxar para atualizar, depois que o usuário lançou o pull com o controle giratório que está sendo mostrado enquanto o [ `ListView` ](xref:Xamarin.Forms.ListView) está atualizando:

![](interactivity-images/refresh-in-progress.png "Pull do ListView para atualização concluída")

[`ListView`](xref:Xamarin.Forms.ListView) é acionado o [ `Refreshing` ](xref:Xamarin.Forms.ListView.Refreshing) evento para iniciar a atualização e o [ `IsRefreshing` ](xref:Xamarin.Forms.ListView.IsRefreshing) propriedade será definida como `true`. O código que é necessário para atualizar o conteúdo do `ListView` , em seguida, deve ser executado após o manipulador de eventos para o `Refreshing` evento, ou pelo método executado pelo [ `RefreshCommand` ](xref:Xamarin.Forms.ListView.RefreshCommand). Uma vez a `ListView` é atualizado, o `IsRefreshing` propriedade deve ser definida como `false`, ou o [ `EndRefresh` ](xref:Xamarin.Forms.ListView.EndRefresh) método deve ser chamado, para indicar que a atualização for concluída.

> [!NOTE]
> Ao definir uma [ `RefreshCommand` ](xref:Xamarin.Forms.ListView.RefreshCommand), o `CanExecute` método do comando pode ser especificado para habilitar ou desabilitar o comando.

## <a name="related-links"></a>Links relacionados

- [Interatividade do ListView (amostra)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/ListView/interactivity)
