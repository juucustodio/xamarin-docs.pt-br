---
title: Usando o ADO.NET com Xamarin. iOS
description: Este documento descreve como usar o ADO.NET como um método para acessar o SQLite em um aplicativo Xamarin. iOS. Ele aborda referências de assembly, mono. Data. SQLite e o exemplo BasicDataAccess.
ms.prod: xamarin
ms.assetid: 79078A4D-2D24-44F3-9543-B50418A7A000
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/18/2017
ms.openlocfilehash: 2ed16c651d0b373e33d58bb73591977d3484d6e0
ms.sourcegitcommit: be8ce3449afab22673e48b546d857431c071d66f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/17/2020
ms.locfileid: "76162932"
---
# <a name="using-adonet-with-xamarinios"></a>Usando o ADO.NET com Xamarin. iOS

O Xamarin tem suporte interno para o banco de dados SQLite que está disponível no iOS, exposto usando uma sintaxe familiar semelhante a ADO.NET. Usar essas APIs exige que você escreva instruções SQL que são processadas pelo SQLite, como `CREATE TABLE`, `INSERT` e instruções de `SELECT`.

## <a name="assembly-references"></a>Referências de Assembly

Para usar o SQLite de acesso via ADO.NET, você deve adicionar `System.Data` e `Mono.Data.Sqlite` referências ao seu projeto do iOS, conforme mostrado aqui (para obter exemplos no Visual Studio para Mac e no Visual Studio):

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

 ![](using-adonet-images/image4.png "Assembly References in Visual Studio for Mac")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

  ![](using-adonet-images/image6.png "Assembly References in Visual Studio")

-----

Clique com o botão direito do mouse em **referências > editar referências...** em seguida, clique para selecionar os assemblies necessários.

## <a name="about-monodatasqlite"></a>Sobre o mono. Data. sqlite

Usaremos a classe `Mono.Data.Sqlite.SqliteConnection` para criar um arquivo de banco de dados em branco e, em seguida, criar uma instância `SqliteCommand` objetos que podemos usar para executar instruções SQL no banco de dados.

1. **Criando um banco de dados em branco** -chame o método `CreateFile` com um caminho de arquivo (IE. Writeable) válido. Você deve verificar se o arquivo já existe antes de chamar esse método, caso contrário, um novo banco de dados (em branco) será criado na parte superior do antigo, e o arquivo antigo será perdido:

    `Mono.Data.Sqlite.SqliteConnection.CreateFile (dbPath);`

    > [!NOTE]
    > A variável `dbPath` deve ser determinada de acordo com as regras discutidas anteriormente neste documento.

2. **Criando uma conexão de banco de dados** -depois que o arquivo de banco de dados do SQLite for criado, você poderá criar um objeto de conexão para acessar os dados. A conexão é construída com uma cadeia de conexão que assume a forma de `Data Source=file_path`, como mostrado aqui:

    ```csharp
    var connection = new SqliteConnection ("Data Source=" + dbPath);
    connection.Open();
    // do stuff
    connection.Close();
    ```

    Como mencionado anteriormente, uma conexão nunca deve ser reutilizada em threads diferentes. Em caso de dúvida, crie a conexão conforme necessário e feche-a quando terminar; Mas lembre-se de fazer isso com mais frequência do que o necessário.

3. **Criando e executando um comando de banco de dados** -uma vez que temos uma conexão, podemos executar comandos SQL arbitrários nele. O código a seguir mostra uma instrução CREATE TABLE que está sendo executada.

    ```csharp
    using (var command = connection.CreateCommand ()) {
        command.CommandText = "CREATE TABLE [Items] ([_id] int, [Symbol] ntext, [Name] ntext);";
        var rowcount = command.ExecuteNonQuery ();
    }
    ```

Ao executar o SQL diretamente no banco de dados, você deve tomar as precauções normais para não fazer solicitações inválidas, como tentar criar uma tabela que já existe. Acompanhe a estrutura do seu banco de dados para que você não cause um Sqliteexception, como "SQLite Error TABLE [Items] já existe".

## <a name="basic-data-access"></a>Acesso a dados básico

O código de exemplo *DataAccess_Basic* para este documento é semelhante ao executado no IOS:

 ![](using-adonet-images/image9.png "iOS ADO.NET sample")

O código a seguir ilustra como executar operações simples do SQLite e mostra os resultados em como texto na janela principal do aplicativo.

Você precisará incluir esses namespaces:

```csharp
using System;
using System.IO;
using Mono.Data.Sqlite;
```

O exemplo de código a seguir mostra uma interação de banco de dados inteira:

1. Criando o arquivo de banco de dados
2. Inserindo alguns dados
3. Consultando os dados

Essas operações normalmente apareceriam em vários lugares em todo o seu código, por exemplo, você pode criar o arquivo de banco de dados e as tabelas quando seu aplicativo for iniciado pela primeira vez e executar leituras e gravações de dados em telas individuais em seu aplicativo. No exemplo abaixo, foram agrupados em um único método para este exemplo:

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

Como o SQLite permite que comandos SQL arbitrários sejam executados em relação aos dados, você pode executar quaisquer instruções de criação, inserção, atualização, exclusão ou seleção que desejar. Você pode ler sobre os comandos SQL com suporte pelo SQLite no site do SQLite. As instruções SQL são executadas usando um dos três métodos em um objeto SqliteCommand:

- **ExecuteNonQuery** – normalmente usado para criação de tabela ou inserção de dados. O valor de retorno para algumas operações é o número de linhas afetadas, caso contrário, é-1.
- **ExecuteReader** – usado quando uma coleção de linhas deve ser retornada como um `SqlDataReader`.
- **ExecuteScalar** – recupera um único valor (por exemplo, uma agregação).

### <a name="executenonquery"></a>EXECUTENONQUERY

As instruções INSERT, UPDATE e DELETE retornarão o número de linhas afetadas. Todas as outras instruções SQL retornarão-1.

```csharp
using (var c = connection.CreateCommand ()) {
    c.CommandText = "INSERT INTO [Items] ([_id], [Symbol]) VALUES ('1', 'APPL')";
    var rowcount = c.ExecuteNonQuery (); // rowcount will be 1
}
```

### <a name="executereader"></a>EXECUTEREADER

O método a seguir mostra uma cláusula WHERE na instrução SELECT. Como o código está criando uma instrução SQL completa, é necessário tomar cuidado para escapar caracteres reservados, como a aspa (') em vez de cadeias.

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

O método ExecuteReader retorna um objeto SqliteDataReader. Além do método Read mostrado no exemplo, outras propriedades úteis incluem:

- **RowsAffected** – contagem das linhas afetadas pela consulta.
- **HasRows** – se alguma linha foi retornada.

### <a name="executescalar"></a>EXECUTESCALAR

Use isso para instruções SELECT que retornam um único valor (como uma agregação).

```csharp
using (var contents = connection.CreateCommand ()) {
    contents.CommandText = "SELECT COUNT(*) FROM [Items] WHERE Symbol <> 'MSFT'";
    var i = contents.ExecuteScalar ();
}
```

O tipo de retorno do método de `ExecuteScalar` é `object` – você deve converter o resultado dependendo da consulta de banco de dados. O resultado pode ser um inteiro de uma consulta de contagem ou uma cadeia de caracteres de uma consulta SELECT de coluna única. Observe que isso é diferente para outros métodos Execute que retornam um objeto leitor ou uma contagem do número de linhas afetadas.

## <a name="microsoftdatasqlite"></a>Microsoft.Data.Sqlite

Há outro `Microsoft.Data.Sqlite`de biblioteca, que pode ser [instalado a partir do NuGet](https://www.nuget.org/packages/Microsoft.Data.Sqlite), que é funcionalmente equivalente a `Mono.Data.Sqlite` e permite os mesmos tipos de consultas.

Há uma [comparação entre as duas bibliotecas](https://docs.microsoft.com/dotnet/standard/data/sqlite/compare) e alguns [detalhes específicos do Xamarin](https://docs.microsoft.com/dotnet/standard/data/sqlite/xamarin). Mais importante para aplicativos Xamarin. iOS, você deve incluir uma chamada de inicialização:

```csharp
// required for Xamarin.iOS
SQLitePCL.Batteries_V2.Init();
```

## <a name="related-links"></a>Links Relacionados

- [DataAccess básico (exemplo)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic)
- [DataAccess avançado (exemplo)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced)
- [Receitas de dados do iOS](https://github.com/xamarin/recipes/tree/master/Recipes/ios/data/sqlite)
- [Acesso a dados do Xamarin. Forms](~/xamarin-forms/data-cloud/data/databases.md)
