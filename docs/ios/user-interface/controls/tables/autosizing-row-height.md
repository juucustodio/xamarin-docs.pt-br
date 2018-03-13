---
title: "Altura da linha de dimensionamento automático"
ms.topic: article
ms.prod: xamarin
ms.assetid: CE45A385-D40A-482A-90A0-E8382C2BFFB9
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/22/2017
ms.openlocfilehash: c8d66ff8199d451ce7469fa893b7673589c9e320
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2018
---
# <a name="auto-sizing-row-height"></a>Altura da linha de dimensionamento automático

A partir do iOS 8, Apple adicionado a capacidade de criar uma exibição de tabela (`UITableView`) que pode automaticamente aumentar ou diminuir a altura de uma determinada linha com base no tamanho do seu conteúdo usando as restrições, Classes de tamanho e Layout automático.

iOS 11 adicionou a capacidade de linhas expandir automaticamente. Cabeçalhos, rodapés e células podem agora ser automaticamente dimensionadas com base em seu conteúdo. No entanto, se a tabela é criada no iOS Designer, construtor de Interface, ou se ele foi corrigido alturas você deve habilitar manualmente self dimensionamento células, conforme descrito neste guia.

## <a name="cell-layout-in-the-ios-designer"></a>Layout de célula no Designer de iOS

Abra o storyboard para o modo de exibição de tabela que você deseja ter o redimensionamento automático da linha no iOS Designer, selecione a célula *protótipo* e criar o layout da célula. Por exemplo:

[![](autosizing-row-height-images/table01.png "Design de protótipo da célula")](autosizing-row-height-images/table01.png#lightbox)

Para cada elemento no protótipo, adicione restrições para manter os elementos na posição correta, como o modo de exibição de tabela é redimensionado para rotação ou iOS diferentes tamanhos de tela do dispositivo. Por exemplo, fixar o `Title` na parte superior, à esquerda e à direita da célula do *exibição de conteúdo*:

[![](autosizing-row-height-images/table02.png "Fixar o título para a parte superior, à esquerda e direita da exibição de conteúdo de células")](autosizing-row-height-images/table02.png#lightbox)

No caso de nossa tabela de exemplo, o pequeno `Label` (sob o `Title`) que pode reduzir e expandir para aumentar ou diminuir a altura da linha. Para obter esse efeito, adicione as seguintes restrições para fixar à esquerda, direita, partes superior e inferior do rótulo:

[![](autosizing-row-height-images/table03.png "Essas restrições para fixar à esquerda, direita, partes superior e inferior do rótulo")](autosizing-row-height-images/table03.png#lightbox)

Agora que estamos totalmente ter restrita os elementos na célula, precisamos esclarecer quais elemento deve ser estendido. Para fazer isso, defina o **conteúdo prioridade abraços** e **prioridade de resistência a compactação de conteúdo** conforme a necessidade do **Layout** seção do painel de propriedades:

[![](autosizing-row-height-images/table03a.png "A seção de Layout do painel de propriedades")](autosizing-row-height-images/table03a.png#lightbox)

Definir o elemento que você deseja expandir para ter um **inferior** valor de prioridade abraços e um **inferior** valor de prioridade de resistência a compactação.

Em seguida, é preciso selecionar o protótipo de célula e dê a ele um exclusivo **identificador**:

[![](autosizing-row-height-images/table04.png "Dando o protótipo de célula de um identificador exclusivo")](autosizing-row-height-images/table04.png#lightbox)

No caso do nosso exemplo, `GrowCell`. Vamos usar esse valor posteriormente quando é popular a tabela.

> [!IMPORTANT]
> **Observação:** se sua tabela contiver mais de um tipo de célula (**protótipo**), você precisa garantir que cada tipo tem seu próprio exclusivo `Identifier` para linha e redimensionamento trabalhar.

Para cada elemento de nosso protótipo de célula, atribuir uma **nome** para expô-lo ao código c#. Por exemplo:

[![](autosizing-row-height-images/table05.png "Atribua um nome para expô-lo ao código c#")](autosizing-row-height-images/table05.png#lightbox)

Em seguida, adicione uma classe personalizada para o `UITableViewController`, o `UITableView` e `UITableCell` (Prototype). Por exemplo: 

[![](autosizing-row-height-images/table06.png "Adicionando uma classe personalizada para o UITableViewController, o UITableView e o UITableCell")](autosizing-row-height-images/table06.png#lightbox)

Finalmente, para certificar-se de que todos os esperados conteúdo é exibido em nosso rótulo, defina o **linhas** propriedade `0`:

[![](autosizing-row-height-images/table06.png "A propriedade de linhas é definido como 0")](autosizing-row-height-images/table06a.png#lightbox)

Com a interface do usuário definida, vamos adicionar o código para habilitar o redimensionamento de altura de linha automática.

## <a name="enabling-auto-resizing-height"></a>Habilitando a altura de redimensionamento automático

No de fonte de dados nossa tabela da exibição (`UITableViewDatasource`) ou origem (`UITableViewSource`), quando é retirada da fila uma célula que precisamos usar o `Identifier` que definimos no designer. Por exemplo:

```csharp
public string CellID {
    get { return "GrowCell"; }
}
...

public override UITableViewCell GetCell (UITableView tableView, Foundation.NSIndexPath indexPath)
{
    var cell = tableView.DequeueReusableCell (CellID, indexPath) as GrowRowTableCell;
    var item = Items [indexPath.Row];

    // Setup
    cell.Image = UIImage.FromFile(item.ImageName);
    cell.Title = item.Title;
    cell.Description = item.Description;

    return cell;
}
```

Por padrão, o modo de exibição de tabela será definido para o redimensionamento automático a altura da linha. Para isso, certifique-se de `RowHeight` propriedade deve ser definida como `UITableView.AutomaticDimension`. Também é necessário definir o `EstimatedRowHeight` propriedade em nosso `UITableViewController`. Por exemplo:

```csharp
public override void ViewWillAppear (bool animated)
{
    base.ViewWillAppear (animated);

    // Initialize table
    TableView.DataSource = new GrowRowTableDataSource(this);
    TableView.Delegate = new GrowRowTableDelegate (this);
    TableView.RowHeight = UITableView.AutomaticDimension;
    TableView.EstimatedRowHeight = 40f;
    TableView.ReloadData ();
}
```

Essa estimativa não precisa ser exato, apenas uma estimativa aproximada da altura de cada linha na exibição de tabela média.

Com esse código, quando o aplicativo é executado, cada linha serão reduzido e crescer com base na altura do último rótulo no protótipo de célula. Por exemplo:

[![](autosizing-row-height-images/table07.png "Uma tabela de exemplo executar")](autosizing-row-height-images/table07.png#lightbox)


## <a name="related-links"></a>Links relacionados

- [GrowRowTable (exemplo)](https://developer.xamarin.com/samples/monotouch/GrowRowTable/)
