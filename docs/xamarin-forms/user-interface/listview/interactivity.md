---
title: Interatividade de ListView
description: Adicione interatividade a ListView implementando seleções, passe o dedo para exclusão e puxe para atualizar.
ms.prod: xamarin
ms.assetid: CD14EB90-B08C-4E8F-A314-DA0EEC76E647
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/08/2016
ms.openlocfilehash: d16c45b7f7cb7eef67a905b6fbcb9b8675b0b2ba
ms.sourcegitcommit: 1561c8022c3585655229a869d9ef3510bf83f00a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/27/2018
---
# <a name="listview-interactivity"></a>Interatividade de ListView

ListView oferece suporte a interagir com os dados que apresenta por meio da seguinte maneira:

- [**Seleção de & toques** ](#selectiontaps) &ndash; responder toques e seleções/contíguas de itens. Habilitar ou desabilitar seleção de linha (habilitada por padrão).
- [**Ações de contexto** ](#Context_Actions) &ndash; expõe a funcionalidade por item, por exemplo, passe o dedo para exclusão.
- [**Puxe para atualizar** ](#Pull_to_Refresh) &ndash; implementar o idioma puxe para atualizar os usuários já conhecem com experiências nativo.

<a name="selectiontaps" />

## <a name="selection--taps"></a>Toques de & seleção
`ListView` oferece suporte a seleção de um item por vez. Seleção é ativado por padrão. Quando um usuário toca um item, dois eventos são disparados: `ItemTapped` e `ItemSelected`. Observação tocar duas vezes o mesmo item não será acionado vários `ItemSelected` eventos, mas será acionado vários `ItemTapped` eventos. Observe também que `ItemSelected` será chamado se um item está desmarcado.

Lembre-se que `ItemSelected` é chamado quando itens são desmarcados e quando eles estiverem selecionados. Isso significa que você precisará verificar null `SelectedItem` no seu `ItemSelected` manipulador de eventos antes que você pode usá-lo:

```csharp
void OnSelection (object sender, SelectedItemChangedEventArgs e)
{
  if (e.SelectedItem == null) {
    return; //ItemSelected is called on deselection, which results in SelectedItem being set to null
  }
  DisplayAlert ("Item Selected", e.SelectedItem.ToString (), "Ok");
  //((ListView)sender).SelectedItem = null; //uncomment line if you want to disable the visual selection state.
}
```

### <a name="disabling-selection"></a>Desabilitar seleção

Se você quiser desabilitar seleção, lidar com o `ItemSelected` evento e defina o `SelectedItem` propriedade nulo:

```csharp
SelectionDemoList.ItemSelected += (sender, e) => {
    ((ListView)sender).SelectedItem = null;
};
```

Com seleção habilitado:

![](interactivity-images/selection-default.png "ListView com seleção habilitada")

<a name="Context_Actions" />

## <a name="context-actions"></a>Ações de contexto
Geralmente, os usuários desejam agir em um item em uma `ListView`. Por exemplo, considere uma lista de endereços de email no aplicativo de email. No iOS, você pode passar para excluir uma mensagem:

![](interactivity-images/context-default.png "ListView com ações de contexto")

Ações de contexto podem ser implementadas em c# e XAML. Abaixo você encontrará guias específicas para ambos, mas primeiro vamos dar uma olhada em alguns detalhes de implementação fundamentais para ambos.

Ações de contexto são criadas usando `MenuItem`s. Toque para MenuItems são gerados por MenuItem em si, não a ListView. Isso é diferente de como os eventos de toque são tratados para células, onde a ListView gera o evento em vez de célula. Porque o ListView está gerando o evento, seu manipulador de eventos recebe informações de chave, como qual item foi selecionado ou tocado.

Por padrão, um MenuItem não tem como saber a célula que pertence a. `CommandParameter` está disponível em `MenuItem` para armazenar objetos, como o objeto por trás ViewCell do MenuItem. `CommandParameter` pode ser definido em XAML e c#.

### <a name="c"></a>C#  

Ações de contexto podem ser implementadas em qualquer `Cell` subclasse (desde que ele não está sendo usado como um cabeçalho de grupo), criando `MenuItem`s e adicioná-los para o `ContextActions` coleção para a célula. Você possui as seguintes propriedades podem ser configuradas para a ação de contexto:

* **Texto** &ndash; a cadeia de caracteres que é exibida no item de menu.
* **Clicado** &ndash; o evento quando o item é clicado.
* **IsDestructive** &ndash; (opcional) quando for verdadeiro o item é processado de maneira diferente no iOS.

Várias ações de contexto podem ser adicionadas a uma célula, mas somente um deve ter `IsDestructive` definido como `true`. O código a seguir demonstra como ações de contexto seriam adicionadas a um `ViewCell`:

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

`MenuItem`s também podem ser criados em uma coleção de XAML declarativamente. O XAML a seguir demonstra uma célula personalizada com duas ações de contexto implementadas:

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

No arquivo code-behind, verifique o `Clicked` métodos são implementados:

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

<a name="Pull_to_Refresh" />

## <a name="pull-to-refresh"></a>Extrair a atualização
Os usuários já conhecem recebendo em uma lista de dados que será atualizada dessa lista. `ListView` oferece suporte a esse-de-prontos. Para habilitar a funcionalidade de atualização de pull, defina `IsPullToRefreshEnabled` como true:

```csharp
listView.IsPullToRefreshEnabled = true;
```

Puxe para atualizar como o usuário está recebendo:

![](interactivity-images/refresh-start.png "Recepção de ListView para atualização em andamento")

Puxe para atualizar como o usuário foi lançado de pull. Isso é que o usuário vê enquanto você estiver atualizando a lista: ![ ] (interactivity-images/refresh-in-progress.png "ListView Pull para atualização completa")

ListView expõe alguns eventos que permitem a você responder a eventos de atualização de pull.

-  O `RefreshCommand` será chamado e o `Refreshing` evento chamado. `IsRefreshing` será definido como `true`.
-  Você deve executar qualquer código que é necessário para atualizar o conteúdo da exibição de lista, no comando ou o evento.
-  Quando a atualização é concluída, chame `EndRefresh` ou defina `IsRefreshing` para `false` para informar o modo de exibição de lista que você estiver pronto.

O `CanExecute` propriedade é respeitada, que oferece uma maneira de controlar se o comando de atualização de recepção deve ser habilitado.



## <a name="related-links"></a>Links relacionados

- [ListView interatividade (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/ListView/interactivity)
- [Notas de versão 1.4](http://forums.xamarin.com/discussion/35451/xamarin-forms-1-4-0-released/)
- [Notas de versão 1.3](http://forums.xamarin.com/discussion/29934/xamarin-forms-1-3-0-released/)
