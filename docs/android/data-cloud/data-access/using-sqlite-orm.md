---
title: Usando SQLite.NET
ms.prod: xamarin
ms.assetid: 3447B7EE-A320-489E-AF02-E5721097760A
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/08/2018
ms.openlocfilehash: 59ba1ef60b0f63ed98302bf65c4d43c8ae207f22
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="using-sqlitenet"></a>Usando SQLite.NET

A biblioteca de SQLite.NET Xamarin recomenda é um ORM muito básica que permite armazenar e recuperar objetos no banco de dados SQLite local em um dispositivo Android. ORM significa de mapeamento relacional de objeto &ndash; uma API que permite que você salvar e recuperar "objetos" de um banco de dados sem escrever instruções SQL.

## <a name="using-sqlitenet"></a>Usando SQLite.NET

Para incluir a biblioteca SQLite.NET em um aplicativo Xamarin, adicione o [pacote NuGet de PCL SQLite.net](https://www.nuget.org/packages/sqlite-net-pcl/) para o projeto usando o **net SQLite PCL** pacote do NuGet:

[![Pacote do SQLite.NET NuGet](using-sqlite-orm-images/image1a-sml.png "pacote SQLite.NET NuGet")](using-sqlite-orm-images/image1a.png#lightbox)

Uma vez que a biblioteca de SQLite.NET disponível, siga estas três etapas para usá-lo para acessar um banco de dados:


1.  **Adicionar um uso instrução** &ndash; adicione a seguinte instrução para os arquivos c# onde o acesso a dados é necessário: 

    ```csharp
    using SQLite;
    ```

2.  **Criar um banco de dados em branco** &ndash; uma referência de banco de dados pode ser criada, passando o caminho do arquivo que o construtor da classe SQLiteConnection. Você não precisa verificar se o arquivo já existe &ndash; ela será criada automaticamente se necessário, caso contrário, o arquivo de banco de dados existente será aberto. O `dbPath` variável deve ser determinada de acordo com as regras abordadas neste documento:

    ```csharp
    var db = new SQLiteConnection (dbPath);
    ```

3.  **Salvar dados** &ndash; depois de criar um objeto SQLiteConnection, comandos de banco de dados são executados ao chamar seus métodos, como CreateTable e Insert como este:

    ```csharp
    db.CreateTable<Stock> ();
    db.Insert (newStock); // after creating the newStock object
    ```

4.  **Recuperar dados** &ndash; para recuperar um objeto (ou uma lista de objetos), use a seguinte sintaxe:

    ```csharp
    var stock = db.Get<Stock>(5); // primary key id of 5
    var stockList = db.Table<Stock>();
    ```

## <a name="basic-data-access-sample"></a>Exemplo de acesso de dados básicos

O *DataAccess_Basic* código de exemplo para este documento esta aparência quando em execução no Android. O código ilustra como executar operações SQLite.NET simples e mostra os resultados como texto na janela principal do aplicativo.


**Android**

![Exemplo do Android SQLite.NET](using-sqlite-orm-images/image3.png "SQLite.NET Android exemplo")

O exemplo de código a seguir mostra uma interação do banco de dados inteiro usando a biblioteca de SQLite.NET para encapsular o acesso de banco de dados subjacente.
Ele mostra:

1.  Criar o arquivo de banco de dados

2.  Inserir alguns dados por meio da criação de objetos e, em seguida, salvá-las

3.  Consultando os dados

Você precisará incluir esses namespaces:

```csharp
using SQLite; // from the github SQLite.cs class
```

O último deles requer que você adicionou SQLite ao seu projeto. Observe que a tabela de banco de dados SQLite é definida com a adição de atributos a uma classe (o `Stock` classe) em vez de um comando CREATE TABLE.

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

Usando o `[Table]` atributo sem especificar um parâmetro de nome de tabela fará com que a tabela de banco de dados subjacente ter o mesmo nome de classe (nesse caso, "Ações"). O nome da tabela real é importante se você escrever consultas SQL diretamente no banco de dados em vez de usa os métodos de acesso de dados ORM. Da mesma forma a `[Column("_id")]` atributo é opcional e se a ausência de uma coluna será adicionada à tabela com o mesmo nome que a propriedade na classe.

## <a name="sqlite-attributes"></a>SQLite atributos

Atributos comuns que você pode aplicar a suas classes para controlar como eles são armazenados no banco de dados subjacente incluem:

-   **[PrimaryKey]**  &ndash; Esse atributo pode ser aplicado a uma propriedade de inteiro para forçá-lo para ser a chave primária da tabela base. Não há suporte para chaves primárias compostas.

-   **[AutoIncrement]**  &ndash; Esse atributo fará com que um valor da propriedade de um inteiro para ser incremento automático para cada novo objeto inserido no banco de dados

-   **[Column(name)]**  &ndash; Fornecendo opcional `name` parâmetro substituirá o valor padrão de nome da coluna do banco de dados subjacente (que é o mesmo que a propriedade).

-   **[Table(name)]**  &ndash; Marca a classe como sendo podem ser armazenados em uma tabela base do SQLite. Especificando o parâmetro de nome opcional substituirá o valor padrão de nome da tabela de banco de dados subjacente (que é o mesmo que o nome da classe).

-   **[MaxLength(value)]**  &ndash; Restringe o comprimento de uma propriedade de texto, quando uma tentativa de inserir um banco de dados. Código de consumo, isso deve validar antes de inserir o objeto como esse atributo é 'verificado apenas' durante uma inserção de banco de dados ou a tentativa de operação de atualização.

-   **[Ignorar]**  &ndash; Faz com que SQLite.NET para ignorar essa propriedade.
    Isso é particularmente útil para propriedades que têm um tipo que não pode ser armazenado no banco de dados, ou propriedades de coleções de modelo que não podem ser resolvidas automaticamente ser SQLite.

-   **[Unique]**  &ndash; Garante que os valores na coluna de banco de dados subjacentes sejam exclusivos.


A maioria desses atributos é opcional, SQLite usará os valores padrão para nomes de tabela e coluna. Você sempre deve especificar uma chave primária de inteiro para que a seleção e a exclusão de consultas podem ser executadas com eficiência em seus dados.

## <a name="more-complex-queries"></a>Consultas mais complexas

Os seguintes métodos em `SQLiteConnection` pode ser usado para executar outras operações de dados:

-   **Inserir** &ndash; adiciona um novo objeto no banco de dados.

-   **Obter&lt;T&gt;**  &ndash; tenta recuperar um objeto usando a chave primária.

-   **Tabela&lt;T&gt;**  &ndash; retorna todos os objetos na tabela.

-   **Excluir** &ndash; exclui um objeto usando sua chave primária.

-   **Consulta&lt;T&gt;**  &ndash; executar uma consulta SQL que retorna um número de linhas (como objetos).

-   **Executar** &ndash; usar esse método (e não `Query`) quando você não espera que linhas de SQL (como instruções INSERT, UPDATE e DELETE).


### <a name="getting-an-object-by-the-primary-key"></a>Obtendo um objeto de chave primária

SQLite.Net fornece o método Get para recuperar um único objeto com base em sua chave primária.

```csharp
var existingItem = db.Get<Stock>(3);
```

### <a name="selecting-an-object-using-linq"></a>Selecionar um objeto usando o Linq

Suportam a métodos que retornam coleções `IEnumerable<T>` , você pode usar o Linq para consultar ou classificar o conteúdo de uma tabela. O código a seguir mostra um exemplo que usa Linq para filtrar todas as entradas que começam com a letra "A":

```csharp
var apple = from s in db.Table<Stock>()
    where s.Symbol.StartsWith ("A")
    select s;
Console.WriteLine ("-> " + apple.FirstOrDefault ().Symbol);
```

### <a name="selecting-an-object-using-sql"></a>Selecionar um objeto usando o SQL

Embora SQLite.Net pode fornecer acesso baseado em objeto aos seus dados, às vezes, talvez seja necessário fazer uma consulta mais complexa que permite o Linq (ou talvez seja necessário um desempenho mais rápido). Você pode usar comandos SQL com o método de consulta, conforme mostrado aqui:

```csharp
var stocksStartingWithA = db.Query<Stock>("SELECT * FROM Items WHERE Symbol = ?", "A");
foreach (var s in stocksStartingWithA) {
    Console.WriteLine ("a " + s.Symbol);
}
```

> [!NOTE]
> Ao escrever instruções SQL diretamente, você criar uma dependência nos nomes das tabelas e colunas em seu banco de dados que foram geradas de suas classes e seus atributos. Se você alterar esses nomes em seu código, você deve lembrar atualizar todas as instruções SQL escritas manualmente.

### <a name="deleting-an-object"></a>Excluir um objeto

A chave primária é usada para excluir a linha, conforme mostrado aqui:

```csharp
var rowcount = db.Delete<Stock>(someStock.Id); // Id is the primary key
```

Você pode verificar o `rowcount` para confirmar a quantas linhas foram afetadas (excluídos nesse caso).

## <a name="using-sqlitenet-with-multiple-threads"></a>Usando SQLite.NET com vários Threads

SQLite oferece suporte a três modos diferentes de threads: *thread único*, *vários segmentos*, e *serializados*. Se você quiser acessar o banco de dados de vários threads sem quaisquer restrições, você pode configurar SQLite para usar o **serializados** modo de threading. É importante definir esse modo no início do seu aplicativo (por exemplo, no início do `OnCreate` método).

Para alterar o modo de threading, chame `SqliteConnection.SetConfig`. Por exemplo, esta linha de código configura SQLite para **serializados** modo: 

```csharp
SqliteConnection.SetConfig(SQLiteConfig.Serialized);
```

A versão Android SQLite tem uma limitação que requer mais algumas etapas. Se a chamada para `SqliteConnection.SetConfig` gera uma exceção SQLite como `library used incorrectly`, em seguida, você deve usar a seguinte solução alternativa: 

1.  Link para o nativo **libsqlite.so** biblioteca para que o `sqlite3_shutdown` e `sqlite3_initialize` APIs são disponibilizados para o aplicativo:

    ```csharp
    [DllImport("libsqlite.so")]
    internal static extern int sqlite3_shutdown();

    [DllImport("libsqlite.so")]
    internal static extern int sqlite3_initialize();
    ```


2.  No início do `OnCreate` método, adicione este código ao desligamento do SQLite, configurá-la para **serializados** modo e reinicializar SQLite:

    ```csharp
    sqlite3_shutdown();
    SqliteConnection.SetConfig(SQLiteConfig.Serialized);
    sqlite3_initialize();
    ```

Essa solução também funciona para o `Mono.Data.Sqlite` biblioteca. Para obter mais informações sobre SQLite e vários segmentos, consulte [SQLite e vários Threads](https://www.sqlite.org/threadsafe.html). 



## <a name="related-links"></a>Links relacionados

- [DataAccess Basic (exemplo)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic)
- [DataAccess avançados (amostra)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced)
- [Dados do Android receitas](https://developer.xamarin.com/recipes/android/data/)
- [Acesso a dados xamarin. Forms](~/xamarin-forms/app-fundamentals/databases.md)
