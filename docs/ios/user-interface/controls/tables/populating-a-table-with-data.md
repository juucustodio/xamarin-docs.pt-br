---
title: Populando uma tabela com dados
ms.topic: article
ms.prod: xamarin
ms.assetid: 6FE64DDF-1029-EB9B-6EEC-1C7DFDFDF3AF
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/22/2017
ms.openlocfilehash: fb0e4341d8d8ad0719f35c691add9bad1d3f85a8
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="populating-a-table-with-data"></a>Populando uma tabela com dados

Para adicionar linhas a uma `UITableView` você precisa implementar um `UITableViewSource` subclasse e substituição chama os métodos para exibir a tabela seja preenchida automaticamente.

Este guia abrange:

- Subclasses de um UITableViewSource
- Reutilização de célula
- Adição de um índice
- Adicionar cabeçalhos e rodapés


<a name="Subclassing_UITableViewSource" />

## <a name="subclassing-uitableviewsource"></a>Subclasses UITableViewSource

Um `UITableViewSource` subclasse é atribuída a cada `UITableView`. O modo de exibição de tabela consultas a classe de origem para determinar como processar em si (por exemplo, o número de linhas é necessário e a altura de cada linha, se for diferente do padrão). O mais importante é a fonte fornece cada modo de exibição de célula preenchido com dados.

Há apenas dois métodos obrigatórios necessários para criar uma tabela de exibir dados:

-   **RowsInSection** – retorno um [ `nint` ](http://developer.xamarin.com/guides/cross-platform/macios/nativetypes/) contagem do número total de linhas de dados deve exibir a tabela.
-   **GetCell** – retorno um `UITableCellView` populado com dados para o índice da linha correspondente passado para o método.


O arquivo de exemplo BasicTable **TableSource.cs** tem a mais simples possível implementação `UITableViewSource`. Você pode ver no trecho de código a seguir que aceita uma matriz de cadeias de caracteres a exibir na tabela e retorna um estilo de célula padrão que contém cada cadeia de caracteres:

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

            //---- if there are no cells to reuse, create a new one
            if (cell == null)
            { cell = new UITableViewCell (UITableViewCellStyle.Default, CellIdentifier); }

            cell.TextLabel.Text = item;

            return cell;
        }
}
```

Um `UITableViewSource` pode usar qualquer estrutura de dados de uma matriz de cadeia de caracteres simples (conforme mostrado neste exemplo) para uma lista <> ou outra coleção. A implementação de `UITableViewSource` métodos isola a tabela da estrutura de dados subjacente.

Para usar essa subclasse, crie uma matriz de cadeia de caracteres para construir a fonte, em seguida, atribuí-la a uma instância de `UITableView`:

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

A tabela resultante tem esta aparência:

 [ ![](populating-a-table-with-data-images/image3.png "Tabela de exemplo em execução")](populating-a-table-with-data-images/image3.png)

A maioria das tabelas permitir que o usuário tocar em uma linha para selecioná-lo e executar alguma outra ação (como executar uma música, chamar um contato ou mostrando outra tela). Para fazer isso, há algumas coisas que precisamos fazer. Primeiro, vamos criar um AlertController para exibir uma mensagem quando o usuário clica em uma linha, adicionando o seguinte para o `RowSelected` método:

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
Adicione um construtor para sua classe UITableViewSource que usa um controlador de exibição como um parâmetro e salva-o em um campo de:

```csharp
public TableSource (string[] items, HomeScreen owner)
{
    ...
    this.owner = owner;

}
```
Modifique o método ViewDidLoad onde a classe UITableViewSource é criada para passar o `this` referência:

```csharp
table.Source = new TableSource(tableItems, this);
```
Finalmente, de volta em seu `RowSelected` método, chame `PresentViewController` no campo de dados armazenados em cache:

```csharp
public override void RowSelected (UITableView tableView, NSIndexPath indexPath)
{
    ...
    owner.PresentViewController (okAlertController, true, null);

    ...
}
```


Agora o usuário pode tocar em uma linha e um alerta será exibida:



 [ ![](populating-a-table-with-data-images/image4.png "O alerta selecionado da linha")](populating-a-table-with-data-images/image4.png)


## <a name="cell-reuse"></a>Reutilização de célula

Neste exemplo existem apenas seis itens, portanto, não há nenhuma reutilização de célula necessária. Ao exibir centenas ou milhares de linhas, no entanto, seria uma perda de memória para criar centenas ou milhares de `UITableViewCell` objetos quando apenas alguns cabem na tela de cada vez.

Para evitar essa situação, quando uma célula desaparece da tela de que seu modo de exibição é colocado em uma fila para reutilização. Conforme o usuário rola, a tabela chama `GetCell` para solicitar novos modos de exibição para exibir – para reutilizar uma célula existente (que não está atualmente sendo exibida) simplesmente chamar o `DequeueReusableCell` método. Se uma célula está disponível para reutilização, que ele será retornado, caso contrário, null será retornado e seu código deve criar uma nova instância da célula.

Este trecho de código do exemplo demonstra o padrão:

```csharp
// request a recycled cell to save memory
UITableViewCell cell = tableView.DequeueReusableCell (cellIdentifier);
// if there are no cells to reuse, create a new one
if (cell == null)
    cell = new UITableViewCell (UITableViewCellStyle.Default, cellIdentifier);
```

O `cellIdentifier` cria efetivamente filas separadas para diferentes tipos de célula. Neste exemplo, todas as células pesquisar o mesmo codificado somente um identificador é usado. Se não houvesse diferentes tipos de célula cada deveriam uma cadeia de caracteres do identificador diferente quando eles são instanciados e quando eles são solicitados da fila de reutilização.

### <a name="cell-reuse-in-ios-6"></a>Reutilização de célula no iOS 6 e posteriores

iOS 6 adicionado um padrão de reutilização de célula semelhante a uma introdução com exibições de coleção. Embora o padrão de reutilização existente mostrado acima ainda há suporte para versões anteriores compatibilidade, esse novo padrão é preferível conforme ela remove a necessidade para a verificação de nula na célula.

Com o novo padrão de um aplicativo registra a classe de célula ou xib a ser usado ao chamar o `RegisterClassForCellReuse` ou `RegisterNibForCellReuse` no construtor do controlador. Então, quando dequeueing a célula no `GetCell` simplesmente chamada de método `DequeueReusableCell` passando o identificador registrado para a classe de célula ou xib e o caminho do índice.

Por exemplo, o código a seguir registra uma classe de célula personalizado em um UITableViewController:

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

Com a classe MyCell registrada, a célula pode ser removida da fila no `GetCell` método o `UITableViewSource` sem a necessidade da verificação de nulos extra, conforme mostrado abaixo:

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

Lembre-se, ao usar o novo padrão de reutilização com uma classe de célula personalizado, você precisa implementar o construtor que assume um `IntPtr`, conforme mostrado no trecho abaixo, caso contrário a Objective-C não será possível construir uma instância da classe de célula:

```csharp
public class MyCell : UITableViewCell
{
  public MyCell (IntPtr p):base(p)
  {
  }
  ...
}
```

Você pode ver exemplos dos tópicos explicados acima de **BasicTable** exemplo vinculados a este artigo.

<a name="Adding_an_Index" />

## <a name="adding-an-index"></a>Adição de um índice

Um índice ajuda o usuário rolar listas longas, geralmente classificadas em ordem alfabética, embora você pode indexar por todos os critérios que você deseja. O **BasicTableIndex** exemplo carrega uma lista mais longa de itens de um arquivo para demonstrar o índice. Cada item no índice corresponde a uma 'seção' da tabela.

 [ ![](populating-a-table-with-data-images/image5.png "A exibição do índice")](populating-a-table-with-data-images/image5.png)

Para dar suporte a 'sections' os dados por trás de tabela precisam ser agrupados, portanto, o exemplo BasicTableIndex cria um `Dictionary<>` da matriz de cadeias de caracteres usando a primeira letra de cada item como a chave de dicionário:

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

O `UITableViewSource` subclasse deve, em seguida, os seguintes métodos adicionado ou modificado para usar o `Dictionary<>` :

-   **NumberOfSections** – esse método é opcional, por padrão, a tabela considera uma seção. Ao exibir um índice esse método deve retornar o número de itens no índice (por exemplo, 26 se o índice contém todas as letras do alfabeto em inglês).
-   **RowsInSection** – retorna o número de linhas em uma determinada seção.
-   **SectionIndexTitles** – retorna a matriz de cadeias de caracteres que será usada para exibir o índice. O exemplo de código retorna uma matriz de letras.


Os métodos atualizados no arquivo de exemplo **BasicTableIndex/TableSource.cs** ter esta aparência:

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

Índices geralmente são usados apenas com o estilo de tabela simples.


<a name="Adding_Headers_and_Footers" />

## <a name="adding-headers-and-footers"></a>Adicionar cabeçalhos e rodapés

Cabeçalhos e rodapés de página podem ser usados para agrupar visualmente linhas em uma tabela. A estrutura de dados necessários é muito semelhante à adição de um índice – um `Dictionary<>` funciona muito bem. Em vez de usar o alfabeto para agrupar as células, este exemplo agrupará legumes por tipo científico.
O resultado terá a seguinte aparência:

 [ ![](populating-a-table-with-data-images/image6.png "Exemplos de cabeçalhos e rodapés")](populating-a-table-with-data-images/image6.png)

Para exibir cabeçalhos e rodapés de `UITableViewSource` subclasse requer estes métodos adicionais:

-   **TitleForHeader** – retorna o texto a ser usado como o cabeçalho
-   **TitleForFooter** – retorna o texto a ser usado como o rodapé.


Os métodos atualizados no arquivo de exemplo **BasicTableHeaderFooter/Code/TableSource.cs** ter esta aparência:

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

Você pode personalizar a aparência do cabeçalho e rodapé de página com um modo de exibição do objeto, usando o `GetViewForHeader` e `GetViewForFooter` método substitui em `UITableViewSource`.


## <a name="related-links"></a>Links relacionados

- [WorkingWithTables (exemplo)](https://developer.xamarin.com/samples/monotouch/WorkingWithTables)
