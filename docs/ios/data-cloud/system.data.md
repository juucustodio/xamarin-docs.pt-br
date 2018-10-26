---
title: System. Data no xamarin. IOS
description: Este documento descreve como usar o System. Data e Mono.Data.Sqlite.dll para acessar dados do SQLite em um aplicativo xamarin. IOS.
ms.prod: xamarin
ms.assetid: F10C0C57-7BDE-A3F3-B011-9839949D15C8
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.openlocfilehash: 4e9b782cf266a96f30c79eaf139ef88332e02dca
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50119807"
---
# <a name="systemdata-in-xamarinios"></a>System. Data no xamarin. IOS

Adiciona o suporte para xamarin. IOS 8.10 [System. Data](xref:System.Data), incluindo o `Mono.Data.Sqlite.dll` provedor ADO.NET. O suporte inclui a adição dos seguintes [assemblies](~/cross-platform/internals/available-assemblies.md):

-  `System.Data.dll`
-  `System.Data.Service.Client.dll`
-  `System.Transactions.dll`
-  `Mono.Data.Tds.dll`
-  `Mono.Data.Sqlite.dll`

<a name="Example" />

## <a name="example"></a>Exemplo

O programa a seguir cria um banco de dados `Documents/mydb.db3`, e se o banco de dados anteriormente não existe, ela é preenchida com dados de exemplo. O banco de dados é consultado, com a saída gravada `stderr`.

### <a name="add-references"></a>Adicionar referências

Clique duas vezes em primeiro lugar, o **referências** nó e escolha **Editar referências...**  , em seguida, selecione `System.Data` e `Mono.Data.Sqlite`:

[![](system.data-images/edit-references-sml.png "Adicionando novas referências")](system.data-images/edit-references.png#lightbox)

### <a name="sample-code"></a>Código de exemplo

O código a seguir mostra um exemplo simples de criar uma tabela e inserindo linhas usando comandos do embedded SQL:

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
> Conforme mencionado no exemplo de código acima, é uma prática inadequada para inserir cadeias de caracteres em comandos SQL, pois ele torna o código vulnerável a [injeção de SQL](http://en.wikipedia.org/wiki/SQL_injection).


### <a name="using-command-parameters"></a>Usando parâmetros de comando

O código a seguir mostra como usar parâmetros de comando para inserir o texto inserido pelo usuário com segurança no banco de dados (mesmo se o texto contiver caracteres especiais do SQL como o apóstrofo único):

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

Funcionalidade ausente do **dll** consiste em:

-  Qualquer coisa que exigem [System. CodeDom](xref:System.CodeDom) (por exemplo,  [System.Data.TypedDataSetGenerator](xref:System.Data.TypedDataSetGenerator) )
-  Configuração XML (por exemplo, suporte a arquivos  [System.Data.Common.DbProviderConfigurationHandler](xref:System.Data.Common.DbProviderConfigurationHandler) )
-   [System.Data.Common.DbProviderFactories](xref:System.Data.Common.DbProviderFactories) (depende de suporte de arquivo de configuração XML)
-   [System.Data.OleDb](xref:System.Data.OleDb)
-   [System.Data.Odbc](xref:System.Data.Odbc)
-  O `System.EnterpriseServices.dll` dependência foi *removido* partir `System.Data.dll` , resultando na remoção da [SqlConnection.EnlistDistributedTransaction(ITransaction)](xref:System.Data.SqlClient.SqlConnection.EnlistDistributedTransaction*) método.


<a name="Mono.Data.Sqlite" />

### <a name="monodatasqlite"></a>Mono.Data.Sqlite

Enquanto isso, **Mono.Data.Sqlite.dll** sofreu sem alterações de código fonte, mas em vez disso, pode ser o host de um número de *tempo de execução* problemas desde `Mono.Data.Sqlite.dll` associa SQLite 3.5. iOS 8, enquanto isso, é fornecido com o SQLite 3.8.5. Basta dizer, algumas coisas tenham mudado entre as duas versões.

Enviar uma versão mais antiga do iOS com as seguintes versões do SQLite:

- **iOS 7** -versão 3.7.13.
- **iOS 6** -versão 3.7.13.
- **iOS 5** -versão 3.7.7.
- **iOS 4** -versão 3.6.22.

Por exemplo, os problemas mais comuns que parecem estar relacionados à consulta de esquema de banco de dados, determinar no tempo de execução que existem colunas em uma determinada tabela, como `Mono.Data.Sqlite.SqliteConnection.GetSchema` (substituindo [DbConnection.GetSchema](xref:System.Data.Common.DbConnection.GetSchema) e `Mono.Data.Sqlite.SqliteDataReader.GetSchemaTable` (substituindo [DbDataReader.GetSchemaTable](xref:System.Data.Common.DbDataReader.GetSchemaTable). Em resumo, parece que tudo usando [DataTable](xref:System.Data.DataTable) provavelmente não funcionará.

<a name="Data_Binding" />

## <a name="data-binding"></a>Associação de dados

Não há suporte para a vinculação de dados no momento.

