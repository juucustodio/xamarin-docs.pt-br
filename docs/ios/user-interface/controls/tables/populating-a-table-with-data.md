---
title: Populando uma tabela com dados no Xamarin. iOS
description: Este documento descreve como preencher uma tabela com dados em um aplicativo Xamarin. iOS. Ele discute UITableViewSource, reutilização de célula, adição de um índice, cabeçalhos e rodapés.
ms.prod: xamarin
ms.assetid: 6FE64DDF-1029-EB9B-6EEC-1C7DFDFDF3AF
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/22/2017
ms.openlocfilehash: 954273907fea1c57ca9d2aaac5ceb7d81115f553
ms.sourcegitcommit: 57b98c3c818a77cae94749665878d155ad031ff9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/27/2019
ms.locfileid: "75500339"
---
# <a name="populating-a-table-with-data-in-xamarinios"></a>Populando uma tabela com dados no Xamarin. iOS

Para adicionar linhas a um `UITableView` você precisa implementar uma subclasse `UITableViewSource` e substituir os métodos que a exibição de tabela chama para preencher a si mesma.

Este guia aborda:

- Subclasse de um UITableViewSource
- Reutilização de célula
- Adicionando um índice
- Adicionando cabeçalhos e rodapés

<a name="Subclassing_UITableViewSource" />

## <a name="subclassing-uitableviewsource"></a>UITableViewSource de subclasse

Uma subclasse `UITableViewSource` é atribuída a cada `UITableView`. A exibição de tabela consulta a classe de origem para determinar como renderizar a si mesma (por exemplo, quantas linhas são necessárias e a altura de cada linha se for diferente do padrão). O mais importante é que a fonte fornece cada exibição de célula preenchida com dados.

Há apenas dois métodos obrigatórios necessários para fazer uma tabela exibir dados:

- **RowsInSection** – retorna uma contagem de [`nint`](~/cross-platform/macios/nativetypes.md) do número total de linhas de dados que a tabela deve exibir.
- **GetCell** – retorna um `UITableCellView` populado com dados para o índice de linha correspondente passado para o método.

O arquivo de exemplo Basictable **TableSource.cs** tem a implementação mais simples possível de `UITableViewSource`. Você pode ver no trecho de código abaixo que ele aceita uma matriz de cadeias de caracteres para exibir na tabela e retorna um estilo de célula padrão contendo cada cadeia de caracteres:

```csharp
public class TableSource : UITableViewSource {

        string[] TableItems;
        string CellIdentifier = "TableCell";

        public TableSource (string[] items)
        {
            TableItems = items;
        }

        public override nint RowsInSection (UITableView tableview, nint section)
        {
            return TableItems.Length;
        }

        public override UITableViewCell GetCell (UITableView tableView, NSIndexPath indexPath)
        {
            UITableViewCell cell = tableView.DequeueReusableCell (CellIdentifier);
            string item = TableItems[indexPath.Row];

            //if there are no cells to reuse, create a new one
            if (cell == null){ 
                cell = new UITableViewCell (UITableViewCellStyle.Default, CellIdentifier); 
            }

            cell.TextLabel.Text = item;

            return cell;
        }
}
```

Um `UITableViewSource` pode usar qualquer estrutura de dados, de uma matriz de cadeia de caracteres simples (como mostrado neste exemplo) para uma lista < > ou outra coleção. A implementação dos métodos de `UITableViewSource` isola a tabela da estrutura de dados subjacente.

Para usar essa subclasse, crie uma matriz de cadeia de caracteres para construir a origem e, em seguida, atribua-a a uma instância do `UITableView`:

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();
    table = new UITableView(View.Bounds); // defaults to Plain style
    string[] tableItems = new string[] {"Vegetables","Fruits","Flower Buds","Legumes","Bulbs","Tubers"};
    table.Source = new TableSource(tableItems);
    Add (table);
}
```

A tabela resultante é parecida com esta:

 [![](populating-a-table-with-data-images/image3.png "Sample table running")](populating-a-table-with-data-images/image3.png#lightbox)

A maioria das tabelas permite que o usuário toque em uma linha para selecioná-la e executar alguma outra ação (como reproduzir uma música ou chamar um contato ou mostrar outra tela). Para conseguir isso, há algumas coisas que precisamos fazer. Primeiro, vamos criar um AlertController para exibir uma mensagem quando o usuário clicar em uma linha adicionando o seguinte ao método `RowSelected`:

```csharp
public override void RowSelected (UITableView tableView, NSIndexPath indexPath)
{
    UIAlertController okAlertController = UIAlertController.Create ("Row Selected", tableItems[indexPath.Row], UIAlertControllerStyle.Alert);
    okAlertController.AddAction(UIAlertAction.Create("OK", UIAlertActionStyle.Default, null));
    ...

    tableView.DeselectRow (indexPath, true);
}
```

Em seguida, crie uma instância do nosso controlador de exibição:

```csharp
HomeScreen owner;
```

Adicione um Construtor à sua classe UITableViewSource que usa um controlador de exibição como um parâmetro e o salva em um campo:

```csharp
public TableSource (string[] items, HomeScreen owner)
{
    ...
    this.owner = owner;

}
```

Modifique o método ViewDidLoad em que a classe UITableViewSource é criada para passar a referência `this`:

```csharp
table.Source = new TableSource(tableItems, this);
```

Finalmente, de volta ao seu método `RowSelected`, chame `PresentViewController` no campo armazenado em cache:

```csharp
public override void RowSelected (UITableView tableView, NSIndexPath indexPath)
{
    ...
    owner.PresentViewController (okAlertController, true, null);

    ...
}
```

Agora, o usuário pode tocar em uma linha e um alerta será exibido:

 [![](populating-a-table-with-data-images/image4.png "The row selected alert")](populating-a-table-with-data-images/image4.png#lightbox)

## <a name="cell-reuse"></a>Reutilização de célula

Neste exemplo, há apenas seis itens, portanto, não é necessária uma reutilização de célula. No entanto, ao exibir centenas ou milhares de linhas, seria um desperdício de memória para criar centenas ou milhares de objetos de `UITableViewCell` quando apenas alguns couberem na tela por vez.

Para evitar essa situação, quando uma célula desaparece da tela, sua exibição é colocada em uma fila para reutilização. À medida que o usuário rola, a tabela chama `GetCell` para solicitar que novas exibições sejam exibidas – para reutilizar uma célula existente (que não esteja sendo exibida no momento), basta chamar o método `DequeueReusableCell`. Se uma célula estiver disponível para reutilização, ela será retornada, caso contrário, um valor nulo será retornado e o código deverá criar uma nova instância de célula.

Este trecho de código do exemplo demonstra o padrão:

```csharp
// request a recycled cell to save memory
UITableViewCell cell = tableView.DequeueReusableCell (cellIdentifier);
// if there are no cells to reuse, create a new one
if (cell == null)
    cell = new UITableViewCell (UITableViewCellStyle.Default, cellIdentifier);
```

O `cellIdentifier` cria efetivamente filas separadas para diferentes tipos de célula. Neste exemplo, todas as células têm a mesma aparência que apenas um identificador codificado é usado. Se houver tipos diferentes de célula, cada um terá uma cadeia de caracteres de identificador diferente, quando eles forem instanciados e quando forem solicitados na fila de reutilização.

### <a name="cell-reuse-in-ios-6"></a>Reutilização de célula no iOS 6 +

o iOS 6 adicionou um padrão de reutilização de célula semelhante a uma introdução com exibições de coleção. Embora o padrão de reutilização existente mostrado acima ainda tenha suporte para compatibilidade com versões anteriores, esse novo padrão é preferível, pois elimina a necessidade da verificação nula na célula.

Com o novo padrão, um aplicativo registra a classe de célula ou XIB a ser usada chamando `RegisterClassForCellReuse` ou `RegisterNibForCellReuse` no construtor do controlador. Em seguida, ao remover a fila da célula no método `GetCell`, basta chamar `DequeueReusableCell` passando o identificador que você registrou para a classe de célula ou XIB e o caminho do índice.

Por exemplo, o código a seguir registra uma classe de célula personalizada em um UITableViewController:

```csharp
public class MyTableViewController : UITableViewController
{
  static NSString MyCellId = new NSString ("MyCellId");

  public MyTableViewController ()
  {
    TableView.RegisterClassForCellReuse (typeof(MyCell), MyCellId);
  }
  ...
}
```

Com a classe MyCell registrada, a célula pode ser removida da fila no método `GetCell` da `UITableViewSource` sem a necessidade de uma verificação nula extra, como mostrado abaixo:

```csharp
class MyTableSource : UITableViewSource
{
  public override UITableViewCell GetCell (UITableView tableView, NSIndexPath indexPath)
  {
    // if cell is not available in reuse pool, iOS will create one automatically
    // no need to do null check and create cell manually
    var cell = (MyCell) tableView.DequeueReusableCell (MyCellId, indexPath);

    // do whatever you need to with cell, such as assigning properties, etc.

    return cell;
  }
}
```

Lembre-se de que, ao usar o novo padrão de reutilização com uma classe de célula Personalizada, você precisa implementar o construtor que usa um `IntPtr`, conforme mostrado no trecho de código abaixo; caso contrário, o Objective-C não será capaz de construir uma instância da classe Cell:

```csharp
public class MyCell : UITableViewCell
{
  public MyCell (IntPtr p):base(p)
  {
  }
  ...
}
```

Você pode ver exemplos dos tópicos explicados acima no exemplo **basictable** vinculado a este artigo.

<a name="Adding_an_Index" />

## <a name="adding-an-index"></a>Adicionando um índice

Um índice ajuda o usuário a rolar por listas longas, normalmente ordenadas alfabeticamente, embora você possa indexar por qualquer critério que desejar. O exemplo **BasicTableIndex** carrega uma lista muito mais longa de itens de um arquivo para demonstrar o índice. Cada item no índice corresponde a uma "seção" da tabela.

 [![](populating-a-table-with-data-images/image5.png "The Index display")](populating-a-table-with-data-images/image5.png#lightbox)

Para dar suporte a ' seções ', os dados por trás da tabela precisam ser agrupados; portanto, o exemplo BasicTableIndex cria uma `Dictionary<>` da matriz de cadeias de caracteres usando a primeira letra de cada item como a chave de dicionário:

```csharp
indexedTableItems = new Dictionary<string, List<string>>();
foreach (var t in items) {
    if (indexedTableItems.ContainsKey (t[0].ToString ())) {
        indexedTableItems[t[0].ToString ()].Add(t);
    } else {
        indexedTableItems.Add (t[0].ToString (), new List<string>() {t});
    }
}
keys = indexedTableItems.Keys.ToArray ();
```

Em seguida, a subclasse `UITableViewSource` precisa dos seguintes métodos adicionados ou modificados para usar o `Dictionary<>`:

- **NumberOfSections** – esse método é opcional, por padrão, a tabela pressupõe uma seção. Ao exibir um índice, esse método deve retornar o número de itens no índice (por exemplo, 26 se o índice contiver todas as letras do alfabeto em inglês).
- **RowsInSection** – retorna o número de linhas em uma determinada seção.
- **SectionIndexTitles** – retorna a matriz de cadeias de caracteres que será usada para exibir o índice. O código de exemplo retorna uma matriz de letras.

Os métodos atualizados no arquivo de exemplo **BasicTableIndex/TableName. cs** têm a seguinte aparência:

```csharp
public override nint NumberOfSections (UITableView tableView)
{
    return keys.Length;
}
public override nint RowsInSection (UITableView tableview, nint section)
{
    return indexedTableItems[keys[section]].Count;
}
public override string[] SectionIndexTitles (UITableView tableView)
{
    return keys;
}
```

Geralmente, os índices são usados apenas com o estilo de tabela simples.

<a name="Adding_Headers_and_Footers" />

## <a name="adding-headers-and-footers"></a>Adicionando cabeçalhos e rodapés

Cabeçalhos e rodapés podem ser usados para agrupar visualmente linhas em uma tabela. A estrutura de dados necessária é muito semelhante à adição de um índice – um `Dictionary<>` funciona muito bem. Em vez de usar o alfabeto para agrupar as células, este exemplo agrupará o "etc." por tipo botânico.
O resultado terá a seguinte aparência:

 [![](populating-a-table-with-data-images/image6.png "Sample Headers and Footers")](populating-a-table-with-data-images/image6.png#lightbox)

Para exibir cabeçalhos e rodapés, a subclasse `UITableViewSource` requer estes métodos adicionais:

- **TitleForHeader** – retorna o texto a ser usado como o cabeçalho
- **TitleForFooter** – retorna o texto a ser usado como o rodapé.

Os métodos atualizados no arquivo de exemplo **BasicTableHeaderFooter/Code/TableName. cs** têm a seguinte aparência:

```csharp
public override string TitleForHeader (UITableView tableView, nint section)
{
    return keys[section];
}
public override string TitleForFooter (UITableView tableView, nint section)
{
    return indexedTableItems[keys[section]].Count + " items";
}
```

Você pode personalizar ainda mais a aparência do cabeçalho e do rodapé com um objeto View, usando as substituições de método `GetViewForHeader` e `GetViewForFooter` no `UITableViewSource`.

## <a name="related-links"></a>Links Relacionados

- [WorkingWithTables (exemplo)](https://docs.microsoft.com/samples/xamarin/ios-samples/workingwithtables)
