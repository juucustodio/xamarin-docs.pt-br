---
title: Usando o SQLite.NET com o Android
description: A biblioteca NuGet do SQLite.NET PCL fornece um mecanismo de acesso a dados simples para aplicativos Xamarin. Android.
ms.prod: xamarin
ms.assetid: 3447B7EE-A320-489E-AF02-E5721097760A
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 04/18/2018
ms.openlocfilehash: 65c8466e2649c6d48cf5651f25d14c073dbcf5e3
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70754425"
---
# <a name="using-sqlitenet-with-android"></a>Usando o SQLite.NET com o Android

A biblioteca SQLite.NET que o Xamarin recomenda é um ORM muito básico que permite que você armazene e recupere com facilidade objetos no banco de dados SQLite local em um dispositivo Android. ORM significa mapeamento &ndash; relacional de objeto uma API que permite salvar e recuperar "objetos" de um banco de dados sem escrever instruções SQL.

Para incluir a biblioteca SQLite.NET em um aplicativo Xamarin, adicione o seguinte pacote NuGet ao seu projeto:

- **Nome do pacote:** SQLite-net-PCL
- **Autor:** Frank A. Krueger
- **Id:** sqlite-net-pcl
- **Url:** [nuget.org/packages/sqlite-net-pcl](https://www.nuget.org/packages/sqlite-net-pcl/)

[![Pacote NuGet do SQLite.net](using-sqlite-orm-images/image1a-sml.png "Pacote NuGet do SQLite.net")](using-sqlite-orm-images/image1a.png#lightbox)

> [!TIP]
> Há vários pacotes SQLite diferentes disponíveis – certifique-se de escolher o correto (talvez não seja o principal resultado na pesquisa).

Depois de disponibilizar a biblioteca SQLite.NET, siga estas três etapas para usá-la para acessar um banco de dados:

1. **Adicionar uma instrução using** Adicione a seguinte instrução aos arquivos C# em que o acesso a dados é necessário: &ndash;

    ```csharp
    using SQLite;
    ```

2. **Criar um banco de dados em branco** &ndash; Uma referência de banco de dados pode ser criada passando o caminho do arquivo para o construtor da classe SQLiteConnection. Você não precisa verificar se o arquivo já existe &ndash; , ele será criado automaticamente se necessário, caso contrário, o arquivo de banco de dados existente será aberto. A `dbPath` variável deve ser determinada de acordo com as regras discutidas anteriormente neste documento:

    ```csharp
    var db = new SQLiteConnection (dbPath);
    ```

3. **Salvar dados** &ndash; Depois de criar um objeto SQLiteConnection, os comandos de banco de dados são executados chamando seus métodos, como CreateTable e Insert desta forma:

    ```csharp
    db.CreateTable<Stock> ();
    db.Insert (newStock); // after creating the newStock object
    ```

4. **Recuperar dados** &ndash; Para recuperar um objeto (ou uma lista de objetos), use a seguinte sintaxe:

    ```csharp
    var stock = db.Get<Stock>(5); // primary key id of 5
    var stockList = db.Table<Stock>();
    ```

## <a name="basic-data-access-sample"></a>Exemplo de acesso a dados básico

O código de exemplo *DataAccess_Basic* para este documento é semelhante ao executado no Android. O código ilustra como executar operações SQLite.NET simples e mostra os resultados em como texto na janela principal do aplicativo.

**Android**

![Exemplo de SQLite.net do Android](using-sqlite-orm-images/image3.png "Exemplo de SQLite.net do Android")

O exemplo de código a seguir mostra uma interação de banco de dados inteira usando a biblioteca SQLite.NET para encapsular o acesso ao banco de dados subjacente.
Ele mostra:

1. Criando o arquivo de banco de dados

2. Inserindo alguns dados criando objetos e salvando-os

3. Consultando os dados

Você precisará incluir esses namespaces:

```csharp
using SQLite; // from the github SQLite.cs class
```

A última delas exige que você tenha adicionado o SQLite ao seu projeto. Observe que a tabela de banco de dados SQLite é definida pela adição de atributos a `Stock` uma classe (a classe) em vez de um comando CREATE TABLE.

```csharp
[Table("Items")]
public class Stock {
    [PrimaryKey, AutoIncrement, Column("_id")]
    public int Id { get; set; }
    [MaxLength(8)]
    public string Symbol { get; set; }
}
public static void DoSomeDataAccess () {
       Console.WriteLine ("Creating database, if it doesn't already exist");
   string dbPath = Path.Combine (
        Environment.GetFolderPath (Environment.SpecialFolder.Personal),
        "ormdemo.db3");
   var db = new SQLiteConnection (dbPath);
   db.CreateTable<Stock> ();
   if (db.Table<Stock> ().Count() == 0) {
        // only insert the data if it doesn't already exist
        var newStock = new Stock ();
        newStock.Symbol = "AAPL";
        db.Insert (newStock);
        newStock = new Stock ();
        newStock.Symbol = "GOOG";
        db.Insert (newStock);
        newStock = new Stock ();
        newStock.Symbol = "MSFT";
        db.Insert (newStock);
    }
    Console.WriteLine("Reading data");
    var table = db.Table<Stock> ();
    foreach (var s in table) {
        Console.WriteLine (s.Id + " " + s.Symbol);
    }
}
```

Usar o `[Table]` atributo sem especificar um parâmetro de nome de tabela fará com que a tabela de banco de dados subjacente tenha o mesmo nome que a classe (nesse caso, "stock"). O nome real da tabela será importante se você escrever consultas SQL diretamente no banco de dados, em vez de usar os métodos de acesso de dado de ORM. Da mesma `[Column("_id")]` forma, o atributo é opcional e, se estiver ausente, uma coluna será adicionada à tabela com o mesmo nome da propriedade na classe.

## <a name="sqlite-attributes"></a>Atributos do SQLite

Os atributos comuns que você pode aplicar às suas classes para controlar como eles são armazenados no banco de dados subjacente incluem:

- **[PrimaryKey]** &ndash; Esse atributo pode ser aplicado a uma propriedade de número inteiro para forçá-lo a ser a chave primária da tabela subjacente. Não há suporte para chaves primárias compostas.

- **[Incremento automático]** &ndash; Esse atributo fará com que o valor de uma propriedade de inteiro seja incrementado automaticamente para cada novo objeto inserido no banco de dados

- **[Coluna (nome)]** O fornecimento do parâmetro `name` opcional substituirá o valor padrão do nome da coluna de banco de dados subjacente (que é o mesmo que a propriedade). &ndash;

- **[Tabela (nome)]** &ndash; Marca a classe como sendo capaz de ser armazenada em uma tabela SQLite subjacente. A especificação do parâmetro de nome opcional substituirá o valor padrão do nome da tabela de banco de dados subjacente (que é o mesmo que o nome da classe).

- **[MaxLength (valor)]** &ndash; Restringir o comprimento de uma propriedade de texto quando uma tentativa de inserção de banco de dados é tentada. O consumo de código deve validar isso antes de inserir o objeto, pois esse atributo só é "verificado" quando uma operação de inserção ou atualização de banco de dados é tentada.

- **[Ignorar]** &ndash; Faz com que o SQLite.net ignore essa propriedade.
    Isso é particularmente útil para propriedades que têm um tipo que não pode ser armazenado no banco de dados ou propriedades que modelam coleções que não podem ser resolvidas automaticamente pelo SQLite.

- **[Exclusivo]** &ndash; Garante que os valores na coluna banco de dados subjacente sejam exclusivos.

A maioria desses atributos são opcionais, o SQLite usará valores padrão para nomes de tabela e coluna. Você sempre deve especificar uma chave primária de inteiro para que as consultas seleção e exclusão possam ser executadas com eficiência em seus dados.

## <a name="more-complex-queries"></a>Consultas mais complexas

Os seguintes métodos em `SQLiteConnection` podem ser usados para executar outras operações de dados:

- **Inserir** &ndash; Adiciona um novo objeto ao banco de dados.

- **Obter&lt;T&gt;tentarecuperar um objeto** usandoachaveprimária&ndash; .

- **A&lt;tabelaT&gt;**  retorna&ndash; todos os objetos na tabela.

- **Excluir** &ndash; Exclui um objeto usando sua chave primária.

- **A&lt;consultaT&gt;**  executa&ndash; uma consulta SQL que retorna um número de linhas (como objetos).

- **Executar** Use esse método (e não `Query`) quando não esperar linhas do SQL (como instruções INSERT, Update e Delete). &ndash;

### <a name="getting-an-object-by-the-primary-key"></a>Obtendo um objeto pela chave primária

SQLite.Net fornece o método Get para recuperar um único objeto com base em sua chave primária.

```csharp
var existingItem = db.Get<Stock>(3);
```

### <a name="selecting-an-object-using-linq"></a>Selecionando um objeto usando LINQ

Métodos que retornam suporte `IEnumerable<T>` a coleções para que você possa usar o LINQ para consultar ou classificar o conteúdo de uma tabela. O código a seguir mostra um exemplo usando o LINQ para filtrar todas as entradas que começam com a letra "A":

```csharp
var apple = from s in db.Table<Stock>()
    where s.Symbol.StartsWith ("A")
    select s;
Console.WriteLine ("-> " + apple.FirstOrDefault ().Symbol);
```

### <a name="selecting-an-object-using-sql"></a>Selecionando um objeto usando SQL

Embora o SQLite.Net possa fornecer acesso baseado em objeto aos seus dados, às vezes, talvez seja necessário fazer uma consulta mais complexa do que o LINQ permite (ou talvez seja necessário um desempenho mais rápido). Você pode usar comandos SQL com o método Query, como mostrado aqui:

```csharp
var stocksStartingWithA = db.Query<Stock>("SELECT * FROM Items WHERE Symbol = ?", "A");
foreach (var s in stocksStartingWithA) {
    Console.WriteLine ("a " + s.Symbol);
}
```

> [!NOTE]
> Ao escrever instruções SQL diretamente, você cria uma dependência nos nomes de tabelas e colunas no banco de dados, que foram geradas a partir de suas classes e seus atributos. Se você alterar esses nomes em seu código, deverá se lembrar de atualizar quaisquer instruções SQL gravadas manualmente.

### <a name="deleting-an-object"></a>Excluindo um objeto

A chave primária é usada para excluir a linha, conforme mostrado aqui:

```csharp
var rowcount = db.Delete<Stock>(someStock.Id); // Id is the primary key
```

Você pode verificar o `rowcount` para confirmar quantas linhas foram afetadas (excluídas neste caso).

## <a name="using-sqlitenet-with-multiple-threads"></a>Usando SQLite.NET com vários threads

O SQLite dá suporte a três modos de Threading diferentes: *Thread único*, *vários threads*e *serializados*. Se você quiser acessar o banco de dados de vários threads sem restrições, poderá configurar o SQLite para usar o modo de Threading **serializado** . É importante definir esse modo no início do aplicativo (por exemplo, no início do `OnCreate` método).

Para alterar o modo de Threading, chame `SqliteConnection.SetConfig`. Por exemplo, esta linha de código configura o SQLite para o modo **serializado** :

```csharp
using using Mono.Data.Sqlite;
...
SqliteConnection.SetConfig(SQLiteConfig.Serialized);
```

A versão Android do SQLite tem uma limitação que requer mais algumas etapas. Se a chamada para `SqliteConnection.SetConfig` produzir uma exceção `library used incorrectly`do SQLite, como, você deve usar a seguinte solução alternativa:

1. Link para a biblioteca **libsqlite.so** nativa para que as `sqlite3_shutdown` APIs `sqlite3_initialize` e sejam disponibilizadas para o aplicativo:

    ```csharp
    [DllImport("libsqlite.so")]
    internal static extern int sqlite3_shutdown();

    [DllImport("libsqlite.so")]
    internal static extern int sqlite3_initialize();
    ```

2. No início do `OnCreate` método, adicione este código para desligar o SQLite, configurá-lo para o modo **serializado** e reinicializar o SQLite:

    ```csharp
    using using Mono.Data.Sqlite;
    ...
    sqlite3_shutdown();
    SqliteConnection.SetConfig(SQLiteConfig.Serialized);
    sqlite3_initialize();
    ```

Essa solução alternativa também funciona para `Mono.Data.Sqlite` a biblioteca. Para obter mais informações sobre SQLite e multithreading, consulte [SQLite e vários threads](https://www.sqlite.org/threadsafe.html).

## <a name="related-links"></a>Links relacionados

- [DataAccess básico (exemplo)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic)
- [DataAccess avançado (exemplo)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced)
- [Acesso a dados do Xamarin. Forms](~/xamarin-forms/data-cloud/data/databases.md)
