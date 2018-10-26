---
title: Usando o ADO.NET com o Android
ms.prod: xamarin
ms.assetid: F6ABCEF1-951E-40D8-9EA9-DD79123C2650
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/08/2018
ms.openlocfilehash: 4b22b15cfe5aaa836b65fc75f847b88c8e00e80b
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50106995"
---
# <a name="using-adonet-with-android"></a>Usando o ADO.NET com o Android

Xamarin tem suporte interno para o banco de dados SQLite que está disponível no Android e pode ser exposto usando a sintaxe ADO.NET familiar. Usando essas APIs exige que você escrever instruções SQL que são processadas pelo SQLite, tais como `CREATE TABLE`, `INSERT` e `SELECT` instruções.

## <a name="assembly-references"></a>Referências de Assembly

Para usar o access SQLite por meio do ADO.NET, você deve adicionar `System.Data` e `Mono.Data.Sqlite` faz referência ao seu projeto Android, conforme mostrado aqui:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows) 

![Referências do Android no Visual Studio](using-adonet-images/image7.png "faz referência a Android no Visual Studio") 

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos) 

![Referências do Android no Visual Studio para Mac](using-adonet-images/image5.png "Android faz referência no Visual Studio para Mac") 

-----


Clique com botão direito **referências > Editar referências...**  e em seguida, clique para selecionar os assemblies necessários.

## <a name="about-monodatasqlite"></a>Sobre Mono.Data.Sqlite

Nós usaremos o `Mono.Data.Sqlite.SqliteConnection` classe para criar um arquivo de banco de dados em branco e, em seguida, para criar uma instância `SqliteCommand` objetos que podemos usar para executar instruções SQL no banco de dados.

**Criando um banco de dados em branco** &ndash; chamar o `CreateFile` método com uma validade (ie. gravável) caminho de arquivo. Você deve verificar se o arquivo já existe antes de chamar esse método, caso contrário, um novo banco de dados (em branco) será criado na parte superior do antigo e os dados no arquivo antigo serão perdidos.
`Mono.Data.Sqlite.SqliteConnection.CreateFile (dbPath);` O `dbPath` variável deve ser determinada de acordo com as regras discutidas anteriormente neste documento.

**Criando uma Conexão de banco de dados** &ndash; depois que o arquivo de banco de dados SQLite foi criado, você pode criar um objeto de conexão para acessar os dados. A conexão é construído com uma cadeia de caracteres de conexão que assume a forma de `Data Source=file_path`, conforme mostrado aqui:

```csharp
var connection = new SqliteConnection ("Data Source=" + dbPath);
connection.Open();
// do stuff
connection.Close();
```

Como mencionado anteriormente, uma conexão nunca deve ser reutilizada entre diferentes threads. Em caso de dúvida, crie a conexão conforme necessário e fechá-lo quando terminar; mas lembre-se de fazer essa mais frequente do que o necessário muito.

**Criando e executando um comando de banco de dados** &ndash; assim que tivermos uma conexão podemos executar comandos arbitrários de SQL em relação a ela. O código a seguir mostra um `CREATE TABLE` instrução sendo executada.

```csharp
using (var command = connection.CreateCommand ()) {
    command.CommandText = "CREATE TABLE [Items] ([_id] int, [Symbol] ntext, [Name] ntext);";
    var rowcount = command.ExecuteNonQuery ();
}
```

Ao executar o SQL diretamente no banco de dados que você deve tomar as precauções normais não façam solicitações inválidas, como a tentativa de criar uma tabela que já existe. Manter o controle da estrutura do seu banco de dados para que não fazem com que um `SqliteException` , como **já existe uma tabela de erro do SQLite [itens]**.

## <a name="basic-data-access"></a>Acesso a dados básicos

O *DataAccess_Basic* código de exemplo para este documento se parece com isso, quando executado no Android:

![Exemplo do ADO.NET Android](using-adonet-images/image8.png "ADO.NET Android de exemplo")

O código a seguir ilustra como executar operações simples de SQLite e mostra os resultados como texto na janela principal do aplicativo.

Você precisará incluir esses namespaces:

```csharp
using System;
using System.IO;
using Mono.Data.Sqlite;
```

O exemplo de código a seguir mostra uma interação de banco de dados inteiro:

1.  Criando o arquivo de banco de dados
2.  Inserir alguns dados
3.  Consultando os dados

Essas operações normalmente seriam exibido em vários locais em todo o código, por exemplo você pode criar o arquivo de banco de dados e as tabelas quando seu aplicativo é iniciado pela primeira vez e executar dados leituras e gravações em telas individuais em seu aplicativo. No exemplo a seguir foram agrupadas em um único método para este exemplo:

```csharp
public static SqliteConnection connection;
public static string DoSomeDataAccess ()
{
    // determine the path for the database file
    string dbPath = Path.Combine (
        Environment.GetFolderPath (Environment.SpecialFolder.Personal),
        "adodemo.db3");

    bool exists = File.Exists (dbPath);

    if (!exists) {
        Console.WriteLine("Creating database");
        // Need to create the database before seeding it with some data
        Mono.Data.Sqlite.SqliteConnection.CreateFile (dbPath);
        connection = new SqliteConnection ("Data Source=" + dbPath);

        var commands = new[] {
            "CREATE TABLE [Items] (_id ntext, Symbol ntext);",
            "INSERT INTO [Items] ([_id], [Symbol]) VALUES ('1', 'AAPL')",
            "INSERT INTO [Items] ([_id], [Symbol]) VALUES ('2', 'GOOG')",
            "INSERT INTO [Items] ([_id], [Symbol]) VALUES ('3', 'MSFT')"
        };
        // Open the database connection and create table with data
        connection.Open ();
        foreach (var command in commands) {
            using (var c = connection.CreateCommand ()) {
                c.CommandText = command;
                var rowcount = c.ExecuteNonQuery ();
                Console.WriteLine("\tExecuted " + command);
            }
        }
    } else {
        Console.WriteLine("Database already exists");
        // Open connection to existing database file
        connection = new SqliteConnection ("Data Source=" + dbPath);
        connection.Open ();
    }

    // query the database to prove data was inserted!
    using (var contents = connection.CreateCommand ()) {
        contents.CommandText = "SELECT [_id], [Symbol] from [Items]";
        var r = contents.ExecuteReader ();
        Console.WriteLine("Reading data");
        while (r.Read ())
            Console.WriteLine("\tKey={0}; Value={1}",
                              r ["_id"].ToString (),
                              r ["Symbol"].ToString ());
    }
    connection.Close ();
}

```

## <a name="more-complex-queries"></a>Consultas mais complexas

Como o SQLite permite comandos arbitrários de SQL ser executado nos dados, você pode executar qualquer `CREATE`, `INSERT`, `UPDATE`, `DELETE`, ou `SELECT` instruções que você deseja. Você pode ler sobre os comandos SQL com suporte pelo SQLite no site do Sqlite. As instruções SQL são executadas usando um dos três métodos em um `SqliteCommand` objeto:

-   **ExecuteNonQuery** &ndash; normalmente usado para inserção de dados ou criação de tabela. O valor de retorno para algumas operações é o número de linhas afetadas; caso contrário, -1.

-   **ExecuteReader** &ndash; usado quando uma coleção de linhas que deve ser retornada como um `SqlDataReader`.

-   **ExecuteScalar** &ndash; recupera um valor único (por exemplo, uma agregação).


### <a name="executenonquery"></a>EXECUTENONQUERY

`INSERT`, `UPDATE`, e `DELETE` instruções retornará o número de linhas afetadas. Todas as outras instruções SQL retornará -1.

```csharp
using (var c = connection.CreateCommand ()) {
    c.CommandText = "INSERT INTO [Items] ([_id], [Symbol]) VALUES ('1', 'APPL')";
    var rowcount = c.ExecuteNonQuery (); // rowcount will be 1
}
```

### <a name="executereader"></a>EXECUTEREADER

O método a seguir mostra uma `WHERE` cláusula no `SELECT` instrução.
Porque o código é criar uma instrução SQL completa deve tomar cuidado para escapar caracteres reservados como aspas (') ao redor de cadeias de caracteres.

```csharp
public static string MoreComplexQuery ()
{
    var output = "";
    output += "\nComplex query example: ";
    string dbPath = Path.Combine (
        Environment.GetFolderPath (Environment.SpecialFolder.Personal), "ormdemo.db3");

    connection = new SqliteConnection ("Data Source=" + dbPath);
    connection.Open ();
    using (var contents = connection.CreateCommand ()) {
        contents.CommandText = "SELECT * FROM [Items] WHERE Symbol = 'MSFT'";
        var r = contents.ExecuteReader ();
        output += "\nReading data";
        while (r.Read ())
            output += String.Format ("\n\tKey={0}; Value={1}",
                    r ["_id"].ToString (),
                    r ["Symbol"].ToString ());
    }
    connection.Close ();

    return output;
}
```

O método `ExecuteReader` retorna um objeto `SqliteDataReader`. Além de `Read` método mostrado no exemplo, outras propriedades úteis incluem:

-   **RowsAffected** &ndash; contagem de linhas afetadas pela consulta.

-   **HasRows** &ndash; se nenhuma linha foi retornada.


### <a name="executescalar"></a>EXECUTESCALAR

Use este formulário `SELECT` instruções que retornam um único valor (como uma agregação).

```csharp
using (var contents = connection.CreateCommand ()) {
    contents.CommandText = "SELECT COUNT(*) FROM [Items] WHERE Symbol <> 'MSFT'";
    var i = contents.ExecuteScalar ();
}
```

O `ExecuteScalar` é do tipo de retorno do método `object` &ndash; você deve converter o resultado, dependendo da consulta de banco de dados. O resultado poderia ser um inteiro de um `COUNT` consulta ou uma cadeia de caracteres de uma única coluna `SELECT` consulta. Observe que isso é diferente de outros `Execute` métodos que retornam um objeto reader ou uma contagem do número de linhas afetadas.



## <a name="related-links"></a>Links relacionados

- [DataAccess Basic (exemplo)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic)
- [DataAccess avançadas (amostra)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced)
- [Receitas de dados do Android](https://github.com/xamarin/recipes/tree/master/Recipes/android/data)
- [Acesso a dados do xamarin. Forms](~/xamarin-forms/app-fundamentals/databases.md)
