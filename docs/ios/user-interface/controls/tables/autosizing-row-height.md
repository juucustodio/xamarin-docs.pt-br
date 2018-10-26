---
title: Altura da linha de dimensionamento automático no xamarin. IOS
description: Este documento descreve como adicionar a aplicativos xamarin. IOS linhas de exibição da tabela cujas alturas variam com base no conteúdo. Ele aborda o layout da célula no iOS Designer e a habilitação de altura redimensionamento automático.
ms.prod: xamarin
ms.assetid: CE45A385-D40A-482A-90A0-E8382C2BFFB9
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/22/2017
ms.openlocfilehash: e4446abc73817eb0672cd10a69ff6f738de0c1e1
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50116466"
---
# <a name="auto-sizing-row-height-in-xamarinios"></a>Altura da linha de dimensionamento automático no xamarin. IOS

Começando com o iOS 8, Apple adicionada a capacidade de criar uma exibição de tabela (`UITableView`) que pode automaticamente aumentar ou diminuir a altura de uma determinada linha com base no tamanho do seu conteúdo usando o Layout automático, as Classes de tamanho e restrições.

iOS 11 adicionou a capacidade de linhas expandir automaticamente. Cabeçalhos, rodapés e células podem agora ser automaticamente dimensionadas com base em seu conteúdo. No entanto, se sua tabela é criada no Designer do iOS, Interface Builder, ou se ele tiver corrigido as alturas das linhas você deve habilitar manualmente auto dimensionamento células, conforme descrito neste guia.

## <a name="cell-layout-in-the-ios-designer"></a>Layout da célula no iOS Designer

Abra o storyboard para o modo de exibição de tabela que você deseja ter o redimensionamento automático da linha no iOS Designer, selecione a célula *protótipo* e criar o layout da célula. Por exemplo:

[![](autosizing-row-height-images/table01.png "Design de protótipo da célula")](autosizing-row-height-images/table01.png#lightbox)

Para cada elemento no protótipo, adicione restrições para manter os elementos na posição correta, como o modo de exibição de tabela é redimensionado para rotação ou iOS diferentes tamanhos de tela do dispositivo. Por exemplo, fixar os `Title` à parte superior, esquerda e à direita da célula *exibição de conteúdo*:

[![](autosizing-row-height-images/table02.png "Fixar o título para a parte superior, esquerda e direita do modo de exibição de conteúdo de células")](autosizing-row-height-images/table02.png#lightbox)

No caso de nossa tabela de exemplo, os pequenos `Label` (sob o `Title`) é o campo que pode reduzir e expandir para aumentar ou diminuir a altura da linha. Para obter esse efeito, adicione as seguintes restrições para fixar à esquerda, direita, superior e inferior do rótulo:

[![](autosizing-row-height-images/table03.png "Essas restrições para fixar à esquerda, direita, superior e inferior do rótulo")](autosizing-row-height-images/table03.png#lightbox)

Agora que podemos ter totalmente restrito os elementos na célula, precisamos esclarecer qual elemento deve ser esticado. Para fazer isso, defina as **conteúdo prioridade abraços** e **prioridade de resistência a compactação de conteúdo** conforme necessário na **Layout** seção do painel de propriedades:

[![](autosizing-row-height-images/table03a.png "A seção de Layout do painel de propriedades")](autosizing-row-height-images/table03a.png#lightbox)

Definir o elemento que você deseja expandir para ter uma **inferior** o valor de prioridade abraços e uma **inferior** valor de prioridade de resistência de compactação.

Em seguida, precisamos selecionar o protótipo de célula e dê a ele um exclusivo **identificador**:

[![](autosizing-row-height-images/table04.png "Dando o protótipo de célula de um identificador exclusivo")](autosizing-row-height-images/table04.png#lightbox)

No caso do nosso exemplo, `GrowCell`. Vamos usar este valor posteriormente quando podemos popular a tabela.

> [!IMPORTANT]
> Se sua tabela contiver mais de um tipo de célula (**protótipo**), você precisa garantir que cada tipo tem sua própria exclusivo `Identifier` para o redimensionamento de linha automático funcione.

Para cada elemento de nossos protótipos de célula, atribua uma **nome** para expô-lo para C# código. Por exemplo:

[![](autosizing-row-height-images/table05.png "Atribua um nome para expô-lo para C# código")](autosizing-row-height-images/table05.png#lightbox)

Em seguida, adicione uma classe personalizada para o `UITableViewController`, o `UITableView` e o `UITableCell` (Prototype). Por exemplo: 

[![](autosizing-row-height-images/table06.png "Adicionando uma classe personalizada para o UITableViewController, o UITableView e o UITableCell")](autosizing-row-height-images/table06.png#lightbox)

Por fim, para garantir que todos os esperados conteúdo é exibido em nosso rótulo, defina as **linhas** propriedade `0`:

[![](autosizing-row-height-images/table06.png "A propriedade de linhas é definido como 0")](autosizing-row-height-images/table06a.png#lightbox)

Com a interface do usuário definida, vamos adicionar o código para permitir o redimensionamento de altura de linha automaticamente.

## <a name="enabling-auto-resizing-height"></a>Habilitando a altura de redimensionamento automático

Em qualquer um de fonte de dados de exibição de nossa tabela (`UITableViewDatasource`) ou fonte de (`UITableViewSource`), quando estamos remoção da fila em uma célula, precisamos usar o `Identifier` que definimos no designer. Por exemplo:

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

Por padrão, o modo de exibição de tabela será definido para redimensionamento automático a altura da linha. Para garantir isso, o `RowHeight` propriedade deve ser definida como `UITableView.AutomaticDimension`. Também precisamos definir a `EstimatedRowHeight` propriedade na nossa `UITableViewController`. Por exemplo:

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

Essa estimativa não precisa ser exato, apenas uma estimativa aproximada da altura da média de cada linha na exibição de tabela.

Com esse código, quando o aplicativo é executado, cada linha será reduzido e crescer com base na altura do último rótulo no protótipo da célula. Por exemplo:

[![](autosizing-row-height-images/table07.png "Uma tabela de exemplo executar")](autosizing-row-height-images/table07.png#lightbox)


## <a name="related-links"></a>Links relacionados

- [GrowRowTable (amostra)](https://developer.xamarin.com/samples/monotouch/GrowRowTable/)
