---
title: Usando o ADO.NET com o xamarin. IOS
description: Este documento descreve como usar o ADO.NET como um método para acessar o SQLite em um aplicativo xamarin. IOS. Ele aborda a amostra BasicDataAccess, Mono.Data.Sqlite e referências de assembly.
ms.prod: xamarin
ms.assetid: 79078A4D-2D24-44F3-9543-B50418A7A000
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/18/2017
ms.openlocfilehash: 9314e1b69df4a5965dfd045d0b4ca3e44f1b9de6
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61153658"
---
# <a name="using-adonet-with-xamarinios"></a>Usando o ADO.NET com o xamarin. IOS

Xamarin tem suporte interno para o banco de dados SQLite está disponível no iOS, exposto usando a sintaxe ADO.NET familiar. Usando essas APIs exige que você escrever instruções SQL que são processadas pelo SQLite, tais como `CREATE TABLE`, `INSERT` e `SELECT` instruções.

## <a name="assembly-references"></a>Referências de Assembly

Para usar o access SQLite por meio do ADO.NET, você deve adicionar `System.Data` e `Mono.Data.Sqlite` faz referência ao seu projeto do iOS, conforme mostrado aqui (para exemplos no Visual Studio para Mac e Visual Studio):

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

 ![](using-adonet-images/image4.png "Referências de assembly no Visual Studio para Mac")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

  ![](using-adonet-images/image6.png "Referências de assembly no Visual Studio")

-----

Clique com botão direito **referências > Editar referências...**  e em seguida, clique para selecionar os assemblies necessários.

## <a name="about-monodatasqlite"></a>Sobre Mono.Data.Sqlite

Nós usaremos o `Mono.Data.Sqlite.SqliteConnection` classe para criar um arquivo de banco de dados em branco e, em seguida, para criar uma instância `SqliteCommand` objetos que podemos usar para executar instruções SQL no banco de dados.


1. **Criando um banco de dados em branco** -chamar o `CreateFile` método com uma validade (ie. gravável) caminho de arquivo. Você deve verificar se o arquivo já existe antes de chamar esse método, caso contrário, um novo banco de dados (em branco) será criado na parte superior do antigo e os dados no arquivo antigo serão perdidos:

    `Mono.Data.Sqlite.SqliteConnection.CreateFile (dbPath);`

    > [!NOTE]
    > O `dbPath` variável deve ser determinada de acordo com as regras discutidas anteriormente neste documento.

2. **Criando uma Conexão de banco de dados** - depois do arquivo de banco de dados SQLite foi criado você pode criar um objeto de conexão para acessar os dados. A conexão é construído com uma cadeia de caracteres de conexão que assume a forma de `Data Source=file_path`, conforme mostrado aqui:

    ```csharp
    var connection = new SqliteConnection ("Data Source=" + dbPath);
    connection.Open();
    // do stuff
    connection.Close();
    ```

    Como mencionado anteriormente, uma conexão nunca deve ser reutilizada entre diferentes threads. Em caso de dúvida, crie a conexão conforme necessário e fechá-lo quando terminar; mas lembre-se de fazer essa mais frequente do que o necessário muito.
    
3. **Criando e executando um comando de banco de dados** – assim que tivermos uma conexão que podemos executar comandos arbitrários de SQL em relação a ela. O código a seguir mostra uma instrução CREATE TABLE que está sendo executada.

    ```csharp
    using (var command = connection.CreateCommand ()) {
        command.CommandText = "CREATE TABLE [Items] ([_id] int, [Symbol] ntext, [Name] ntext);";
        var rowcount = command.ExecuteNonQuery ();
    }
    ```

Ao executar o SQL diretamente no banco de dados que você deve tomar as precauções normais não façam solicitações inválidas, como a tentativa de criar uma tabela que já existe. Manter controle da estrutura do seu banco de dados para que você não fazem com que um SqliteException, como "tabela de erro do SQLite [itens] já existe".

## <a name="basic-data-access"></a>Acesso a dados básicos

O *DataAccess_Basic* código de exemplo para este documento se parece com isso, quando em execução no iOS:

 ![](using-adonet-images/image9.png "exemplo do ADO.NET iOS")

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

Como o SQLite permite comandos arbitrários de SQL ser executado nos dados, você pode executar tudo o que criar clicar, inserir, atualizar, excluir ou selecione as instruções que você deseja. Você pode ler sobre os comandos SQL com suporte pelo SQLite no site do Sqlite. As instruções SQL são executadas usando um dos três métodos em um objeto de SqliteCommand:

-  **ExecuteNonQuery** – normalmente usada para inserção de dados ou criação de tabela. O valor de retorno para algumas operações é o número de linhas afetadas; caso contrário, -1.
-  **ExecuteReader** – usado quando uma coleção de linhas que deve ser retornada como um `SqlDataReader` .
-  **ExecuteScalar** – recupera um valor único (por exemplo, uma agregação).


### <a name="executenonquery"></a>EXECUTENONQUERY

Instruções INSERT, UPDATE e DELETE irá retornar o número de linhas afetadas. Todas as outras instruções SQL retornará -1.

```csharp
using (var c = connection.CreateCommand ()) {
    c.CommandText = "INSERT INTO [Items] ([_id], [Symbol]) VALUES ('1', 'APPL')";
    var rowcount = c.ExecuteNonQuery (); // rowcount will be 1
}
```

### <a name="executereader"></a>EXECUTEREADER

O método a seguir mostra uma cláusula WHERE na instrução SELECT. Porque o código é criar uma instrução SQL completa deve tomar cuidado para escapar caracteres reservados como aspas (') ao redor de cadeias de caracteres.

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

O método ExecuteReader retorna um objeto de SqliteDataReader. O método Read mostrado no exemplo, além de outras propriedades úteis incluem:

-  **RowsAffected** – contagem de linhas afetadas pela consulta.
-  **HasRows** – indica se todas as linhas foram retornadas.


### <a name="executescalar"></a>EXECUTESCALAR

Use isso para instruções SELECT que retornam um único valor (como uma agregação).

```csharp
using (var contents = connection.CreateCommand ()) {
    contents.CommandText = "SELECT COUNT(*) FROM [Items] WHERE Symbol <> 'MSFT'";
    var i = contents.ExecuteScalar ();
}
```

O `ExecuteScalar` é do tipo de retorno do método `object` – você deve converter o resultado, dependendo da consulta de banco de dados. O resultado pode ser um inteiro de uma consulta de contagem ou uma cadeia de caracteres de uma consulta de seleção de coluna única. Observe que isso é diferente para outros métodos de execução que retornam um objeto reader ou uma contagem do número de linhas afetadas.


## <a name="related-links"></a>Links relacionados

- [DataAccess Basic (exemplo)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic)
- [DataAccess avançadas (amostra)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced)
- [iOS receitas de dados](https://github.com/xamarin/recipes/tree/master/Recipes/ios/data/sqlite)
- [Acesso a dados do xamarin. Forms](~/xamarin-forms/app-fundamentals/databases.md)
