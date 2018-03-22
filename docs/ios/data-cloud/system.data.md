---
title: System.Data
ms.topic: article
ms.prod: xamarin
ms.assetid: F10C0C57-7BDE-A3F3-B011-9839949D15C8
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: 145a0692ed9761944eec4c7ece1f098a584f2d54
ms.sourcegitcommit: 73bd0c7e5f237f0a1be70a6c1384309bb26609d5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/22/2018
---
# <a name="systemdata"></a>System.Data

Xamarin 8.10 adiciona suporte para [System. Data](https://developer.xamarin.com/api/namespace/System.Data/), incluindo o `Mono.Data.Sqlite.dll` provedor ADO.NET. O suporte inclui a adição dos seguintes [assemblies](~/cross-platform/internals/available-assemblies.md):

-  `System.Data.dll`
-  `System.Data.Service.Client.dll`
-  `System.Transactions.dll`
-  `Mono.Data.Tds.dll`
-  `Mono.Data.Sqlite.dll`


<a name="Example" />

## <a name="example"></a>Exemplo

O programa a seguir cria um banco de dados `Documents/mydb.db3`, e se o banco de dados anteriormente não existe, ela será preenchido com dados de exemplo. O banco de dados é então consultado, com a saída gravada em `stderr`.

### <a name="add-references"></a>Adicionar referências

Primeiro, clique duas vezes no **referências** nó e escolha **Editar referências...**  , em seguida, selecione `System.Data` e `Mono.Data.Sqlite`:

[![](system.data-images/edit-references-sml.png "Adicionando novas referências")](system.data-images/edit-references.png#lightbox)

### <a name="sample-code"></a>Código de exemplo

O código a seguir mostra um exemplo simples de criar uma tabela e inserindo linhas usando comandos SQL inseridos:

```csharp
using System;
using System.Data;
using System.IO;
using Mono.Data.Sqlite;

class Demo {
    static void Main (string [] args)
    {
        var connection = GetConnection ();
        using (var cmd = connection.CreateCommand ()) {
            connection.Open ();
            cmd.CommandText = "SELECT * FROM People";
            using (var reader = cmd.ExecuteReader ()) {
                while (reader.Read ()) {
                    Console.Error.Write ("(Row ");
                    Write (reader, 0);
                    for (nint i = 1; i < reader.FieldCount; ++i) {
                        Console.Error.Write(" ");
                        Write (reader, i);
                    }
                    Console.Error.WriteLine(")");
                }
            }
            connection.Close ();
        }
    }

    static SqliteConnection GetConnection()
    {
        var documents = Environment.GetFolderPath (
                Environment.SpecialFolder.Personal);
        string db = Path.Combine (documents, "mydb.db3");
        bool exists = File.Exists (db);
        if (!exists)
            SqliteConnection.CreateFile (db);
        var conn = new SqliteConnection("Data Source=" + db);
        if (!exists) {
            var commands = new[] {
            "CREATE TABLE People (PersonID INTEGER NOT NULL, FirstName ntext, LastName ntext)",
            // WARNING: never insert user-entered data with embedded parameter values
            "INSERT INTO People (PersonID, FirstName, LastName) VALUES (1, 'First', 'Last')",
            "INSERT INTO People (PersonID, FirstName, LastName) VALUES (2, 'Dewey', 'Cheatem')",
            "INSERT INTO People (PersonID, FirstName, LastName) VALUES (3, 'And', 'How')",
            };
            conn.Open ();
            foreach (var cmd in commands) {
                using (var c = conn.CreateCommand()) {
                    c.CommandText = cmd;
                    c.CommandType = CommandType.Text;
                    c.ExecuteNonQuery ();
                }
            }
            conn.Close ();
        }
        return conn;
    }

    static void Write(SqliteDataReader reader, int index)
    {
        Console.Error.Write("({0} '{1}')",
                reader.GetName(index),
                reader [index]);
    }
}
```

> [!IMPORTANT]
> Conforme mencionado no exemplo de código acima, é uma prática inadequada para inserir cadeias de caracteres em comandos SQL, porque ele faz com que seu código vulnerável a [injeção SQL](http://en.wikipedia.org/wiki/SQL_injection).


### <a name="using-command-parameters"></a>Usando parâmetros de comando

O código a seguir mostra como usar parâmetros de comando para inserir o texto inserido pelo usuário com segurança no banco de dados (mesmo se o texto contém caracteres especiais do SQL como o apóstrofo único):

```csharp
// user input from Textbox control
var fname = fnameTextbox.Text;
var lname = lnameTextbox.Text;
// use command parameters to safely insert into database
using (var addCmd = conn.CreateCommand ()) {
    addCmd.CommandText = "INSERT INTO [People] (PersonID, FirstName, LastName) VALUES (@COL1, @COL2, @COL3)";
    addCmd.CommandType = System.Data.CommandType.Text;
    addCmd.AddParameterWithValue ("@COL1", 1);
    addCmd.AddParameterWithValue ("@COL2", fname);
    addCmd.AddParameterWithValue ("@COL3", lname);
    addCmd.ExecuteNonQuery ();
}
```

<a name="Missing_Functionality" />

## <a name="missing-functionality"></a>Funcionalidade ausente

Ambos **System. Data** e **Mono.Data.Sqlite** estão faltando algumas funcionalidades.

<a name="System.Data" />

### <a name="systemdata"></a>System.Data

Funcionalidade ausente do **System.Data.dll** consiste em:

-  Qualquer coisa que exigem [System. CodeDom](https://developer.xamarin.com/api/namespace/System.CodeDom/) (por exemplo,  [System.Data.TypedDataSetGenerator](https://developer.xamarin.com/api/type/System.Data.TypedDataSetGenerator/) )
-  Arquivo de configuração XML suporte (por exemplo  [System.Data.Common.DbProviderConfigurationHandler](https://developer.xamarin.com/api/type/System.Data.Common.DbProviderConfigurationHandler/) )
-   [System.Data.Common.DbProviderFactories](https://developer.xamarin.com/api/type/System.Data.Common.DbProviderFactories/) (depende do suporte de arquivo de configuração XML)
-   [System.Data.OleDb](https://developer.xamarin.com/api/namespace/System.Data.OleDb/)
-   [System.Data.Odbc](https://developer.xamarin.com/api/namespace/System.Data.Odbc/)
-  O `System.EnterpriseServices.dll` dependência foi *removido* de `System.Data.dll` , resultando na remoção do [SqlConnection.EnlistDistributedTransaction(ITransaction)](https://developer.xamarin.com/api/member/System.Data.SqlClient.SqlConnection.EnlistDistributedTransaction/(System.EnterpriseServices.ITransaction)) método.


<a name="Mono.Data.Sqlite" />

### <a name="monodatasqlite"></a>Mono.Data.Sqlite

Enquanto isso, **Mono.Data.Sqlite.dll** não sofreu nenhuma alteração de código fonte, mas em vez disso, pode ser o host de um número de *tempo de execução* problemas desde `Mono.Data.Sqlite.dll` associa SQLite 3.5. iOS 8, enquanto isso, é fornecido com SQLite 3.8.5. Basta dizer, algumas coisas que foram alterados entre as duas versões.

A versão mais antiga do iOS fornecidas com as seguintes versões do SQLite:

- **iOS 7** -versão 3.7.13.
- **iOS 6** -versão 3.7.13.
- **iOS 5** -versão 3.7.7.
- **iOS 4** -versão 3.6.22.

Por exemplo, os problemas mais comuns parecem estar relacionados a consulta de esquema de banco de dados, determinar em tempo de execução que existem colunas em uma determinada tabela, como `Mono.Data.Sqlite.SqliteConnection.GetSchema` (substituindo [DbConnection.GetSchema](https://developer.xamarin.com/api/member/System.Data.Common.DbConnection.GetSchema/)) e `Mono.Data.Sqlite.SqliteDataReader.GetSchemaTable` ( substituindo [DbDataReader.GetSchemaTable](https://developer.xamarin.com/api/member/System.Data.Common.DbDataReader.GetSchemaTable/)). Em resumo, parece que algo usando [DataTable](https://developer.xamarin.com/api/type/System.Data.DataTable/) é improvável de funcionar.

<a name="Data_Binding" />

## <a name="data-binding"></a>Associação de dados

Não há suporte para associação de dados no momento.

