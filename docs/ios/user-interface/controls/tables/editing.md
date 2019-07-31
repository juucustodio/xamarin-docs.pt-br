---
title: Editando tabelas com Xamarin. iOS
description: Este documento descreve como editar tabelas no Xamarin. iOS. Ele aborda o passar o dedo para excluir, o modo de edição e a inserção de linha.
ms.prod: xamarin
ms.assetid: EC197F25-E865-AFA3-E5CF-B33FAB7744A0
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/22/2017
ms.openlocfilehash: a95e772ab0ba5fa6687ef941034f1de87f5d608a
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68655917"
---
# <a name="editing-tables-with-xamarinios"></a>Editando tabelas com Xamarin. iOS

Os recursos de edição de tabela são habilitados pela `UITableViewSource` substituição de métodos em uma subclasse. O comportamento de edição mais simples é o gesto de passar para a exclusão que pode ser implementado com uma única substituição de método.
A edição mais complexa (incluindo a movimentação de linhas) pode ser feita com a tabela no modo de edição.

## <a name="swipe-to-delete"></a>Passe o dedo para excluir

O recurso de passar o dedo para excluir é um gesto natural no iOS que os usuários esperam. 

 [![](editing-images/image10.png "Exemplo de passar o dedo para excluir")](editing-images/image10.png#lightbox)

Há três substituições de método que afetam o gesto de passar o dedo para mostrar um botão de **exclusão** em uma célula:

-   **CommitEditingStyle** – a origem da tabela detecta se esse método é substituído e habilita automaticamente o gesto de passar para a exclusão. A implementação do método deve chamar `DeleteRows` `UITableView` on para fazer com que as células desapareçam e também remover os dados subjacentes do modelo (por exemplo, uma matriz, um dicionário ou um banco de dados). 
-   **CanEditRow** – se CommitEditingStyle é substituído, todas as linhas são consideradas editáveis. Se esse método for implementado e retornar false (para algumas linhas específicas ou para todas as linhas), o gesto de passar para a exclusão não estará disponível nessa célula. 
-   **TitleForDeleteConfirmation** – opcionalmente, especifica o texto para o botão de **exclusão** . Se esse método não for implementado, o texto do botão será "Delete". 


Esses métodos são implementados na `TableSource` classe a seguir:

```csharp
public override void CommitEditingStyle (UITableView tableView, UITableViewCellEditingStyle editingStyle, Foundation.NSIndexPath indexPath)
{
    switch (editingStyle) {
        case UITableViewCellEditingStyle.Delete:
            // remove the item from the underlying data source
            tableItems.RemoveAt(indexPath.Row);
            // delete the row from the table
            tableView.DeleteRows (new NSIndexPath[] { indexPath }, UITableViewRowAnimation.Fade);
            break;
        case UITableViewCellEditingStyle.None:
            Console.WriteLine ("CommitEditingStyle:None called");
            break;
    }
}
public override bool CanEditRow (UITableView tableView, NSIndexPath indexPath)
{
    return true; // return false if you wish to disable editing for a specific indexPath or for all rows
}
public override string TitleForDeleteConfirmation (UITableView tableView, NSIndexPath indexPath)
{   // Optional - default text is 'Delete'
    return "Trash (" + tableItems[indexPath.Row].SubHeading + ")";
}
```

Para este exemplo, `UITableViewSource` o foi atualizado para usar um `List<TableItem>` (em vez de uma matriz de cadeia de caracteres) como a fonte de dados, pois ele dá suporte à adição e exclusão de itens da coleção.


## <a name="edit-mode"></a>Modo de edição

Quando uma tabela está no modo de edição, o usuário vê um widget "Stop" vermelho em cada linha, que revela um botão de exclusão quando tocado. A tabela também exibe um ícone de ' identificador ' para indicar que a linha pode ser arrastada para alterar a ordem.
O exemplo de **TableEditMode** implementa esses recursos, conforme mostrado.

 [![](editing-images/image11.png "O exemplo TableEditMode implementa esses recursos conforme mostrado")](editing-images/image11.png#lightbox)

Há vários métodos `UITableViewSource` diferentes que afetam o comportamento do modo de edição de uma tabela:

-   **CanEditRow** – se cada linha pode ser editada. Retorne FALSE para impedir que passe o dedo para exclusão e exclusão enquanto estiver no modo de edição. 
-   **CanMoveRow** – retorna true para habilitar a movimentação ' Handle ' ou false para evitar a movimentação. 
-   **EditingStyleForRow** – quando a tabela está no modo de edição, o valor de retorno desse método determina se a célula exibe o ícone de exclusão vermelha ou o ícone de adição verde. Retorna `UITableViewCellEditingStyle.None` se a linha não deve ser editável. 
-   **MoveRow** – chamado quando uma linha é movida para que a estrutura de dados subjacente possa ser modificada para corresponder aos dados conforme eles são exibidos na tabela. 


A implementação dos três primeiros métodos é relativamente direta – a menos que você queira usar o `indexPath` para alterar o comportamento de linhas específicas, basta codificar os valores retornados para a tabela inteira.

```csharp
public override bool CanEditRow (UITableView tableView, NSIndexPath indexPath)
{
    return true; // return false if you wish to disable editing for a specific indexPath or for all rows
}
public override bool CanMoveRow (UITableView tableView, NSIndexPath indexPath)
{
    return true; // return false if you don't allow re-ordering
}
public override UITableViewCellEditingStyle EditingStyleForRow (UITableView tableView, NSIndexPath indexPath)
{
    return UITableViewCellEditingStyle.Delete; // this example doesn't support Insert
}
```

A `MoveRow` implementação é um pouco mais complicada, pois precisa alterar a estrutura de dados subjacente para corresponder à nova ordem. Como os dados são implementados como `List` um código abaixo, o exclui o item de dados em seu local antigo e o insere no novo local. Se os dados foram armazenados em uma tabela de banco de dados SQLite com uma coluna ' Order ' (por exemplo), esse método precisaria executar algumas operações de SQL para reordenar os números nessa coluna.

```csharp
public override void MoveRow (UITableView tableView, NSIndexPath sourceIndexPath, NSIndexPath destinationIndexPath)
{
    var item = tableItems[sourceIndexPath.Row];
    var deleteAt = sourceIndexPath.Row;
    var insertAt = destinationIndexPath.Row;
    
    // are we inserting 
    if (destinationIndexPath.Row < sourceIndexPath.Row) {
        // add one to where we delete, because we're increasing the index by inserting
        deleteAt += 1;
    } else {
        // add one to where we insert, because we haven't deleted the original yet
        insertAt += 1;
    }
    tableItems.Insert (insertAt, item);
    tableItems.RemoveAt (deleteAt);
}
```

Por fim, para colocar a tabela no modo de edição, o botão **Editar** precisa `SetEditing` chamar como esta

```csharp
table.SetEditing (true, true);
```

e quando o usuário terminar de editar, o botão **concluído** deverá desativar o modo de edição:

```csharp
table.SetEditing (false, true);
```


## <a name="row-insertion-editing-style"></a>Estilo de edição de inserção de linha

A inserção de linha de dentro da tabela é uma interface de usuário incomum – o exemplo principal nos aplicativos iOS padrão é a tela **Editar contato** . Esta captura de tela mostra como a funcionalidade de inserção de linha funciona – no modo de edição, há uma linha adicional que (quando clicado) insere linhas adicionais nos dados. Quando a edição for concluída, a linha temporária **(Adicionar nova)** será removida.

 [![](editing-images/image12.png "Quando a edição for concluída, a adição temporária da nova linha será removida")](editing-images/image12.png#lightbox)

Há vários métodos `UITableViewSource` diferentes que afetam o comportamento do modo de edição de uma tabela. Esses métodos foram implementados da seguinte maneira no código de exemplo:

-   **EditingStyleForRow** – retorna `UITableViewCellEditingStyle.Delete` para as linhas que contêm dados e retorna `UITableViewCellEditingStyle.Insert` para a última linha (que será adicionada especificamente para se comportar como um botão de inserção). 
-   **CustomizeMoveTarget** – enquanto o usuário está movendo uma célula, o valor de retorno desse método opcional pode substituir sua escolha de local. Isso significa que você pode impedi-los de ' descartar ' a célula em determinadas posições – como neste exemplo que impede que qualquer linha seja movida após a linha **(Adicionar nova)** . 
-   **CanMoveRow** – retorna true para habilitar a movimentação ' Handle ' ou false para evitar a movimentação. No exemplo, a última linha tem a movimentação ' identificador ' oculta porque ela se destina ao servidor somente como um botão de inserção. 


Também adicionamos dois métodos personalizados para adicionar a linha ' Insert ' e, em seguida, removê-lo novamente quando não for mais necessário. Eles são chamados a partir dos botões **Editar** e **concluídos** :

-   **WillBeginTableEditing** – quando o botão de **edição** é tocado, `SetEditing` ele chama para colocar a tabela no modo de edição. Isso dispara o método WillBeginTableEditing em que exibimos a linha **(Adicionar novo)** no final da tabela para atuar como um ' botão de inserção '. 
-   **DidFinishTableEditing** – quando o botão Done é tocado `SetEditing` , é chamado novamente para desligar o modo de edição. O código de exemplo remove a linha **(Adicionar nova)** da tabela quando a edição não é mais necessária. 


Essas substituições de método são implementadas no arquivo de exemplo **TableEditModeAdd/Code/TableName. cs**:

```csharp
public override UITableViewCellEditingStyle EditingStyleForRow (UITableView tableView, NSIndexPath indexPath)
{
    if (tableView.Editing) {
        if (indexPath.Row == tableView.NumberOfRowsInSection (0) - 1)
            return UITableViewCellEditingStyle.Insert;
        else
            return UITableViewCellEditingStyle.Delete;
    } else // not in editing mode, enable swipe-to-delete for all rows
        return UITableViewCellEditingStyle.Delete;
}
public override NSIndexPath CustomizeMoveTarget (UITableView tableView, NSIndexPath sourceIndexPath, NSIndexPath proposedIndexPath)
{
    var numRows = tableView.NumberOfRowsInSection (0) - 1; // less the (add new) one
    if (proposedIndexPath.Row >= numRows)
        return NSIndexPath.FromRowSection(numRows - 1, 0);
    else
        return proposedIndexPath;
} 
public override bool CanMoveRow (UITableView tableView, NSIndexPath indexPath)
{
    return indexPath.Row < tableView.NumberOfRowsInSection (0) - 1;
}
```

Esses dois métodos personalizados são usados para adicionar e remover a linha **(Adicionar nova)** quando o modo de edição da tabela está habilitado ou desabilitado:

```csharp
public void WillBeginTableEditing (UITableView tableView)
{
    tableView.BeginUpdates ();
    // insert the 'ADD NEW' row at the end of table display
    tableView.InsertRows (new NSIndexPath[] { 
            NSIndexPath.FromRowSection (tableView.NumberOfRowsInSection (0), 0) 
        }, UITableViewRowAnimation.Fade);
    // create a new item and add it to our underlying data (it is not intended to be permanent)
    tableItems.Add (new TableItem ("(add new)"));
    tableView.EndUpdates (); // applies the changes
}
public void DidFinishTableEditing (UITableView tableView)
{
    tableView.BeginUpdates ();
    // remove our 'ADD NEW' row from the underlying data
    tableItems.RemoveAt ((int)tableView.NumberOfRowsInSection (0) - 1); // zero based :)
    // remove the row from the table display
    tableView.DeleteRows (new NSIndexPath[] { NSIndexPath.FromRowSection (tableView.NumberOfRowsInSection (0) - 1, 0) }, UITableViewRowAnimation.Fade);
    tableView.EndUpdates (); // applies the changes
}
```

Por fim, esse código instancia os botões **Editar** e **concluído** , com lambdas que habilitam ou desabilitam o modo de edição quando são tocadas:

```csharp
done = new UIBarButtonItem(UIBarButtonSystemItem.Done, (s,e)=>{
    table.SetEditing (false, true);
    NavigationItem.RightBarButtonItem = edit;
    tableSource.DidFinishTableEditing(table);
});
edit = new UIBarButtonItem(UIBarButtonSystemItem.Edit, (s,e)=>{
    if (table.Editing)
        table.SetEditing (false, true); // if we've half-swiped a row
    tableSource.WillBeginTableEditing(table);
    table.SetEditing (true, true);
    NavigationItem.LeftBarButtonItem = null;
    NavigationItem.RightBarButtonItem = done;
});
```

Esse padrão de interface de usuário de inserção de linha não é usado com muita frequência, `UITableView.BeginUpdates` no `EndUpdates` entanto, você também pode usar os métodos e para animar a inserção ou remoção de células em qualquer tabela. A regra para usar esses métodos é que a diferença no valor retornado por `RowsInSection` entre as `BeginUpdates` chamadas `EndUpdates` e deve corresponder ao número líquido de células adicionadas/excluídas `InsertRows` com `DeleteRows` os métodos e. Se a fonte de base subjacente não for alterada para corresponder às inserções/exclusões no modo de exibição de tabela, ocorrerá um erro.


## <a name="related-links"></a>Links relacionados

- [WorkingWithTables (exemplo)](https://docs.microsoft.com/samples/xamarin/ios-samples/workingwithtables)
