---
title: Trabalhando com ações de linha no xamarin. IOS
description: Este guia demonstra como criar ações de passar o dedo personalizado para linhas da tabela com UISwipeActionsConfiguration ou UITableViewRowAction
ms.prod: xamarin
ms.assetid: 340FB633-0C46-40AA-9963-FF17D7CA6858
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 09/25/2017
ms.openlocfilehash: 6d41f37d4a63db710bb04e35e6e1a4be0dd4f7a4
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61408211"
---
# <a name="working-with-row-actions-in-xamarinios"></a>Trabalhando com ações de linha no xamarin. IOS

_Este guia demonstra como criar ações de passar o dedo personalizado para linhas da tabela com UISwipeActionsConfiguration ou UITableViewRowAction_

![Demonstração de ações de passar o dedo em linhas](row-action-images/action02.png)

iOS fornece duas maneiras de executar ações em uma tabela: `UISwipeActionsConfiguration` e `UITableViewRowAction`.

`UISwipeActionsConfiguration` foi introduzido no iOS 11 e é usado para definir um conjunto de ações que devem levar ocorrem quando o dedo do usuário _em qualquer direção_ em uma linha em uma exibição de tabela. Esse comportamento é semelhante do Mail nativo 

O `UITableViewRowAction` classe é usada para definir uma ação que ocorrerá quando o dedo do usuário restante horizontalmente em uma linha em uma exibição de tabela.
Por exemplo, quando uma tabela, o dedo para a esquerda em uma linha de edição exibe uma **excluir** botão por padrão. Anexando a várias instâncias do `UITableViewRowAction` de classe para um `UITableView`, várias ações personalizadas podem ser definidas, cada um com seu próprio texto, formatação e comportamento.


## <a name="uiswipeactionsconfiguration"></a>UISwipeActionsConfiguration

Há um três etapas necessárias para implementar as ações de passar o dedo com `UISwipeActionsConfiguration`:

1. Substituir `GetLeadingSwipeActionsConfiguration` e/ou `GetTrailingSwipeActionsConfiguration` métodos. Esses métodos retornam um `UISwipeActionsConfiguration`. 
2. Criar uma instância de `UISwipeActionsConfiguration` a ser retornado. Essa classe usa uma matriz de `UIContextualAction`.
3. Criará um `UIContextualAction`.

Eles são explicados mais detalhadamente nas seções a seguir.

### <a name="1-implementing-the-swipeactionsconfigurations-methods"></a>1. Implementando os métodos SwipeActionsConfigurations

`UITableViewController` (e também `UITableViewSource` e `UITableViewDelegate`) contêm dois métodos: `GetLeadingSwipeActionsConfiguration` e `GetTrailingSwipeActionsConfiguration`, que são usados para implementar um conjunto de ações de passar o dedo em uma linha do modo de exibição de tabela. A ação de passar o dedo principal se refere a um passar o dedo do lado esquerdo da tela em um idioma da esquerda para a direita e do lado direito da tela em um idioma da direita para esquerda. 

O exemplo a seguir (da [TableSwipeActions](https://developer.xamarin.com/samples/monotouch/TableSwipeActions) exemplo) demonstra como implementar a configuração de passar o dedo principal. Duas ações são criadas a partir das ações contextuais, que são explicadas [abaixo](#create-uicontextualaction). Em seguida, essas ações são passadas para o recentemente inicializado [ `UISwipeActionsConfiguration` ](#create-uiswipeactionsconfigurations), que é usado como o valor retornado.


```csharp
public override UISwipeActionsConfiguration GetLeadingSwipeActionsConfiguration(UITableView tableView, NSIndexPath indexPath)
{
    //UIContextualActions
    var definitionAction = ContextualDefinitionAction(indexPath.Row);
    var flagAction = ContextualFlagAction(indexPath.Row);

    //UISwipeActionsConfiguration
    var leadingSwipe = UISwipeActionsConfiguration.FromActions(new UIContextualAction[] { flagAction, definitionAction });
    
    leadingSwipe.PerformsFirstActionWithFullSwipe = false;
    
    return leadingSwipe;
}  
```

<a name="create-uiswipeactionsconfigurations" />

### <a name="2-instantiate-a-uiswipeactionsconfiguration"></a>2. Criar uma instância de um `UISwipeActionsConfiguration`

Criar uma instância de um `UISwipeActionsConfiguration` usando o `FromActions` para adicionar uma nova matriz de `UIContextualAction`s, conforme mostrado no trecho de código a seguir:

```csharp
var leadingSwipe = UISwipeActionsConfiguration.FromActions(new UIContextualAction[] { flagAction, definitionAction })

leadingSwipe.PerformsFirstActionWithFullSwipe = false;
```

É importante observar que a ordem na qual exibem suas ações é dependente em como eles são passados para a matriz. Por exemplo, o código acima do dedo principal exibe as ações como assim:

![principais ações passar o dedo exibido em uma linha da tabela](row-action-images/action03.png)

À direita dedo, as ações serão exibidas conforme ilustrado na imagem a seguir:

![à direita passe o dedo para ações exibidas em uma linha da tabela](row-action-images/action04.png)

Este trecho de código também usa o novo `PerformsFirstActionWithFullSwipe` propriedade. Por padrão, essa propriedade é definida como true, o que significa que a primeira ação na matriz acontece quando um usuário passa o dedo totalmente em uma linha. Se você tiver uma ação que não é destrutiva (por exemplo "excluir", isso pode não ser o comportamento ideal e, portanto, você deve defini-lo `false`.

<a name="create-uicontextualaction" />

### <a name="create-a-uicontextualaction"></a>Criar um `UIContextualAction`

A ação contextual é onde você realmente criar a ação que será exibida quando o usuário passa o dedo de uma linha da tabela.

Para inicializar uma ação que você deve fornecer um `UIContextualActionStyle`, um título e um `UIContextualActionHandler`. O `UIContextualActionHandler` usa três parâmetros: uma ação, a ação exibida na exibição e um manipulador de conclusão:

```csharp
public UIContextualAction ContextualFlagAction(int row)
{
    var action = UIContextualAction.FromContextualActionStyle
                    (UIContextualActionStyle.Normal,
                        "Flag",
                        (FlagAction, view, success) => {
                            var alertController = UIAlertController.Create($"Report {words[row]}?", "", UIAlertControllerStyle.Alert);
                            alertController.AddAction(UIAlertAction.Create("Cancel", UIAlertActionStyle.Cancel, null)); 
                            alertController.AddAction(UIAlertAction.Create("Yes", UIAlertActionStyle.Destructive, null));
                            PresentViewController(alertController, true, null);
                            
                            success(true);
                        });

    action.Image = UIImage.FromFile("feedback.png");
    action.BackgroundColor = UIColor.Blue;

    return action;
}
```

Várias propriedades de visual, como a cor do plano de fundo ou imagem da ação podem ser editadas. Trecho de código acima demonstra como adicionar uma imagem para a ação e definir sua cor de plano de fundo como azul.

Depois que as ações contextuais tiverem sido criadas, eles podem usar para inicializar o `UISwipeActionsConfiguration` no `GetLeadingSwipeActionsConfiguration` método.

## <a name="uitableviewrowaction"></a>UITableViewRowAction

Para definir uma ou mais ações de linha personalizado para um `UITableView`, você precisará criar uma instância das `UITableViewDelegate` de classe e substituir o `EditActionsForRow` método. Por exemplo:

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using Foundation;
using UIKit;

namespace BasicTable
{
    public class TableDelegate : UITableViewDelegate
    {
        #region Constructors
        public TableDelegate ()
        {
        }

        public TableDelegate (IntPtr handle) : base (handle)
        {
        }

        public TableDelegate (NSObjectFlag t) : base (t)
        {
        }

        #endregion

        #region Override Methods
        public override UITableViewRowAction[] EditActionsForRow (UITableView tableView, NSIndexPath indexPath)
        {
            UITableViewRowAction hiButton = UITableViewRowAction.Create (
                UITableViewRowActionStyle.Default,
                "Hi",
                delegate {
                    Console.WriteLine ("Hello World!");
                });
            return new UITableViewRowAction[] { hiButton };
        }
        #endregion
    }
}
```

Estático `UITableViewRowAction.Create` método é usado para criar um novo `UITableViewRowAction` que exibirá uma **Hi** botão quando o dedo do usuário é deixada horizontalmente em uma linha na tabela. Posteriormente uma nova instância dos `TableDelegate` é criado e anexado ao `UITableView`. Por exemplo:

```csharp
TableDelegate tableDelegate;
...

// Replace the standard delete button with a "Hi" button
tableDelegate = new TableDelegate ();
table.Delegate = tableDelegate;

```

Quando o código acima é executado e o dedo do usuário à esquerda na linha da tabela, o **Hi** botão será exibido em vez da **excluir** botão que é exibido por padrão:

[![](row-action-images/action01.png "O botão Hi que está sendo exibido em vez de no botão Excluir")](row-action-images/action01.png#lightbox)

Se o usuário toca a **Hi** botão, `Hello World!` será gravada no console no Visual Studio para Mac ou Visual Studio quando o aplicativo é executado no modo de depuração.



## <a name="related-links"></a>Links relacionados

- [TableSwipeActions (amostra)](https://developer.xamarin.com/samples/monotouch/TableSwipeActions)
- [WorkingWithTables (amostra)](https://developer.xamarin.com/samples/monotouch/WorkingWithTables)
