---
title: "Edição"
ms.topic: article
ms.prod: xamarin
ms.assetid: EC197F25-E865-AFA3-E5CF-B33FAB7744A0
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/22/2017
ms.openlocfilehash: 1ea4489cd6f9839d5d32c97aa7ded41e4f15538a
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="editing"></a>Edição

Recursos de edição de tabela são habilitados por substituir métodos em um `UITableViewSource` subclasse. O comportamento de edição mais simples é o gesto de passar para exclusão que pode ser implementado com um único método de substituição.
Edição mais complexos (incluindo mover linhas) pode ser feito com a tabela no modo de edição.

Este guia aborda o seguinte:

- [Passe o dedo para excluir](#Swipe_to_Delete)
- [Modo de edição](#Edit_Mode)
- [Estilo de edição de inserção de linha](#row_insertion_editing_style)

<a name="Swipe_to_delete" />

## <a name="swipe-to-delete"></a>Passe o dedo para exclusão

O dedo para excluir o recurso é um gesto natural no iOS que os usuários esperam. 

 [ ![](editing-images/image10.png "Exemplo de passe o dedo para exclusão")](editing-images/image10.png)

Há três substituições de método que afetam o gesto de passar para mostrar um **excluir** botão em uma célula:

-   **CommitEditingStyle** – a origem da tabela detecta se esse método é substituído e habilita automaticamente o gesto de passar para exclusão. A implementação do método deve chamar `DeleteRows` no `UITableView` para fazer com que as células desaparecem e também remover os dados subjacentes do modelo (por exemplo, uma matriz, dicionário ou os banco de dados). 
-   **CanEditRow** – CommitEditingStyle se será substituído, todas as linhas devem para ser editadas. Se esse método é implementado e retorna false (para algumas linhas específicas ou para todas as linhas), em seguida, o gesto de passar para exclusão não estará disponível na célula. 
-   **TitleForDeleteConfirmation** – opcionalmente, especifica o texto para o **excluir** botão. Se esse método não está implementado o texto do botão será "Excluir". 


Esses métodos são implementados no `TableSource` classe da seguinte maneira:

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

Para este exemplo de `UITableViewSource` foi atualizado para usar um `List<TableItem>` (em vez de uma matriz de cadeia de caracteres) como a fonte de dados porque ele dá suporte à adição e exclusão de itens da coleção.

<a name="Edit_mode" />

## <a name="edit-mode"></a>Modo de edição

Quando uma tabela está no modo de edição o usuário vê um widget vermelho de 'stop' em cada linha, que revela um botão de exclusão quando tocadas. A tabela também exibe um ícone de 'Identificador' para indicar que a linha pode ser arrastada para alterar a ordem.
O **TableEditMode** exemplo implementa esses recursos, como mostrado.

 [ ![](editing-images/image11.png "O exemplo de TableEditMode implementa esses recursos, conforme mostrado")](editing-images/image11.png)

Existem vários métodos diferentes de `UITableViewSource` que afetam o comportamento do modo de edição de uma tabela:

-   **CanEditRow** – se cada linha pode ser editada. Retorne false para evitar a exclusão no modo de edição e passe o dedo para exclusão. 
-   **CanMoveRow** – retorno true para habilitar o 'alça de movimentação' ou false para impedir a movimentação. 
-   **EditingStyleForRow** – quando a tabela está em modo de edição, o valor de retorno deste método determina se a célula exibe o ícone de exclusão vermelho ou verde Adicionar ícone. Retornar `UITableViewCellEditingStyle.None` se a linha não deve ser editada. 
-   **MoveRow** – chamado quando uma linha é movida para que a estrutura de dados subjacente pode ser modificada para corresponder os dados conforme ele é exibido na tabela. 


A implementação para os três primeiros métodos é relativamente simples e prático – a menos que você deseja usar o `indexPath` para alterar o comportamento de linhas específicas, codificar apenas o retorno de valores para a tabela inteira.

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

O `MoveRow` implementação é um pouco mais complicada porque é necessário alterar a estrutura de dados subjacente para corresponder à nova ordem. Como os dados são implementados como um `List` o código a seguir exclui o item de dados no local antigo e o insere no novo local. Se os dados foram armazenados em uma tabela de banco de dados SQLite com uma coluna de 'order' (por exemplo), esse método em vez disso, precisa executar algumas operações de SQL para reordenar os números na coluna.

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

Por fim, para obter a tabela em modo de edição, o **editar** botão precisa chamar `SetEditing` assim

```csharp
table.SetEditing (true, true);
```

e quando é concluído, o usuário de edição, o **feito** botão deve desativar modo de edição:

```csharp
table.SetEditing (false, true);
```

<a name="Edit_mode_–_row_insertion_editing_style" />

## <a name="row-insertion-editing-style"></a>Estilo de edição de inserção de linha

Inserção de linha de dentro da tabela é uma interface de usuário comum – o exemplo principal nos aplicativos do iOS padrão é o **Editar contato** tela. Esta captura de tela mostra como funciona a funcionalidade de inserção de linha – Editar modo não há mais linhas que (quando clicado) insere linhas adicionais nos dados. Quando a edição é concluída, temporárias **(Adicionar nova)** linha será removida.

 [ ![](editing-images/image12.png "Quando a edição estiver concluída, temporárias adicionar nova linha será removida")](editing-images/image12.png)

Existem vários métodos diferentes de `UITableViewSource` que afetam o comportamento do modo de edição de uma tabela. Esses métodos foram implementados como a seguir no código de exemplo:

-   **EditingStyleForRow** – retorna `UITableViewCellEditingStyle.Delete` para as linhas que contêm dados e retorna `UITableViewCellEditingStyle.Insert` para a última linha (que será adicionada especificamente para se comportar como um botão de inserção). 
-   **CustomizeMoveTarget** – enquanto o usuário está movendo uma célula, o valor de retorno deste método opcional pode substituir a escolha do local. Isso significa que você pode impedir que eles 'Descartar' a célula em determinadas posições – como neste exemplo que impede que qualquer linha que está sendo movido após o **(Adicionar nova)** linha. 
-   **CanMoveRow** – retorno true para habilitar o 'alça de movimentação' ou false para impedir a movimentação. No exemplo, a última linha tem a 'alça de movimentação' oculta porque destina-se ao servidor como um botão de inserção. 


Também adicionamos dois métodos personalizados para adicionar a linha de 'insert' e, em seguida, remova-o novamente quando não é mais necessário. Eles são chamados do **editar** e **feito** botões:

-   **WillBeginTableEditing** – quando o **editar** botão é tocadas ele chamadas `SetEditing` para colocar a tabela no modo de edição. Isso dispara o método WillBeginTableEditing onde podemos exibir o **(Adicionar nova)** linha no final da tabela para atuar como um botão de inserção' '. 
-   **DidFinishTableEditing** – quando o botão Done é tocado `SetEditing` é chamado novamente para desativar o modo de edição. O código de exemplo remove o **(Adicionar nova)** linha da tabela durante a edição não é mais necessária. 


Essas substituições de método são implementadas no arquivo de exemplo **TableEditModeAdd/Code/TableSource.cs**:

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

Esses dois métodos personalizados são usados para adicionar e remover o **(Adicionar nova)** quando a tabela do modo de edição de linha está habilitada ou desabilitada:

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

Por fim, esse código cria uma instância de **editar** e **feito** botões com lambdas que habilitar ou desabilitar o modo de edição quando elas são tocadas:

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

Esse padrão de interface de usuário de inserção de linha não é usado com frequência, no entanto, você também pode usar o `UITableView.BeginUpdates` e `EndUpdates` métodos para animar a inserção ou a remoção de células em qualquer tabela. A regra para usar esses métodos é que a diferença no valor retornado por `RowsInSection` entre o `BeginUpdates` e `EndUpdates` chamadas devem corresponder ao número de rede de células adicionados ou excluídos com o `InsertRows` e `DeleteRows` métodos. Se a fonte de dados subjacente não é alterado para corresponder as inserções/exclusões no modo de exibição de tabela, ocorrerá um erro.


## <a name="related-links"></a>Links relacionados

- [WorkingWithTables (exemplo)](https://developer.xamarin.com/samples/monotouch/WorkingWithTables)
