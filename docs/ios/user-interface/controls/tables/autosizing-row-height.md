---
title: Dimensionamento automático da altura da linha no Xamarin. iOS
description: Este documento descreve como adicionar à tabela de aplicativos Xamarin. iOS exibindo linhas cujas alturas variam com base no conteúdo. Ele discute o layout de célula no designer do iOS e habilita a altura de redimensionamento automático.
ms.prod: xamarin
ms.assetid: CE45A385-D40A-482A-90A0-E8382C2BFFB9
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/22/2017
ms.openlocfilehash: eedf76a4ecc566a18f4d4b7d5c4f1b63642b8e25
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91437188"
---
# <a name="auto-sizing-row-height-in-xamarinios"></a>Dimensionamento automático da altura da linha no Xamarin. iOS

A partir do iOS 8, a Apple adicionou a capacidade de criar uma exibição de tabela ( `UITableView` ) que pode aumentar e reduzir automaticamente a altura de uma determinada linha com base no tamanho de seu conteúdo usando layout automático, classes de tamanho e restrições.

o iOS 11 adicionou a capacidade de linhas a serem expandidas automaticamente. Cabeçalhos, rodapés e células agora podem ser dimensionados automaticamente com base em seu conteúdo. No entanto, se a tabela for criada no designer do iOS, Interface Builder ou se ela tiver alturas de linha fixas, você deverá habilitar manualmente as células de dimensionamento automático, conforme descrito neste guia.

## <a name="cell-layout-in-the-ios-designer"></a>Layout de célula no designer do iOS

Abra o storyboard do modo de exibição de tabela para o qual você deseja que o redimensionamento automático da linha seja no designer do iOS, selecione o *protótipo* da célula e projete o layout da célula. Por exemplo:

[![O design do protótipo da célula](autosizing-row-height-images/table01.png)](autosizing-row-height-images/table01.png#lightbox)

Para cada elemento no protótipo, adicione restrições para manter os elementos na posição correta, pois o modo de exibição de tabela é redimensionado para rotação ou tamanhos de tela de dispositivo iOS diferentes. Por exemplo, fixar o na `Title` parte superior, esquerda e direita da *exibição de conteúdo*da célula:

[![Fixando o título na parte superior, esquerda e direita da exibição de conteúdo das células](autosizing-row-height-images/table02.png)](autosizing-row-height-images/table02.png#lightbox)

No caso de nossa tabela de exemplo, o pequeno `Label` (abaixo do `Title` ) é o campo que pode reduzir e aumentar para aumentar ou diminuir a altura da linha. Para obter esse efeito, adicione as seguintes restrições para fixar a esquerda, direita, superior e inferior do rótulo:

[![Essas restrições para fixar a esquerda, direita, superior e inferior do rótulo](autosizing-row-height-images/table03.png)](autosizing-row-height-images/table03.png#lightbox)

Agora que restringimos totalmente os elementos na célula, precisamos esclarecer qual elemento deve ser alongado. Para fazer isso, defina o **conteúdo hugging prioridade** e a **prioridade de resistência à compactação de conteúdo** conforme necessário na seção **layout** do painel de propriedades:

[![A seção de layout do Painel de Propriedades](autosizing-row-height-images/table03a.png)](autosizing-row-height-images/table03a.png#lightbox)

Defina o elemento que você deseja expandir para ter um valor de prioridade **mais baixo** hugging e um valor de prioridade de resistência de compactação **menor** .

Em seguida, precisamos selecionar o protótipo de célula e dar a ele um **identificador**exclusivo:

[![Dando ao protótipo de célula um identificador exclusivo](autosizing-row-height-images/table04.png)](autosizing-row-height-images/table04.png#lightbox)

No caso do nosso exemplo, `GrowCell` . Usaremos esse valor posteriormente quando preenchermos a tabela.

> [!IMPORTANT]
> Se a tabela contiver mais de um tipo de célula (**protótipo**), você precisará garantir que cada tipo tenha seu próprio exclusivo `Identifier` para que o redimensionamento automático de linha funcione.

Para cada elemento do nosso protótipo de célula, atribua um **nome** para expô-lo ao código C#. Por exemplo:

[![Atribuir um nome para expô-lo ao código C#](autosizing-row-height-images/table05.png)](autosizing-row-height-images/table05.png#lightbox)

Em seguida, adicione uma classe personalizada para o `UITableViewController` , o `UITableView` e o `UITableCell` (protótipo). Por exemplo:

[![Adicionar uma classe personalizada para UITableViewController, UITableView e UITableCell](autosizing-row-height-images/table06.png)](autosizing-row-height-images/table06.png#lightbox)

Por fim, para certificar-se de que todo o conteúdo esperado seja exibido em nosso rótulo, defina a propriedade **linhas** como `0` :

[![A propriedade Lines definida como 0](autosizing-row-height-images/table06.png)](autosizing-row-height-images/table06a.png#lightbox)

Com a interface do usuário definida, vamos adicionar o código para habilitar o redimensionamento automático da altura da linha.

## <a name="enabling-auto-resizing-height"></a>Habilitando a altura de redimensionamento automático

Em nossa tabela de código fonte ( `UITableViewDatasource` ) ou origem ( `UITableViewSource` ), quando desenfileiramos uma célula, precisamos usar o `Identifier` que definimos no designer. Por exemplo:

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

Por padrão, a exibição de tabela será definida para redimensionamento automático da altura da linha. Para garantir isso, a `RowHeight` propriedade deve ser definida como `UITableView.AutomaticDimension` . Também precisamos definir a `EstimatedRowHeight` propriedade em nosso `UITableViewController` . Por exemplo:

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

Essa estimativa não precisa ser exata, apenas uma estimativa aproximada da altura média de cada linha na exibição de tabela.

Com esse código em vigor, quando o aplicativo for executado, cada linha será reduzida e crescerá com base na altura do último rótulo no protótipo de célula. Por exemplo:

[![Uma execução de tabela de exemplo](autosizing-row-height-images/table07.png)](autosizing-row-height-images/table07.png#lightbox)

## <a name="related-links"></a>Links Relacionados

- [GrowRowTable (exemplo)](/samples/xamarin/ios-samples/growrowtable)