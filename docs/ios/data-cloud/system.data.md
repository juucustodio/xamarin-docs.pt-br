---
title: System. Data no Xamarin. iOS
description: Este documento descreve como usar System. Data e mono. Data. sqlite. dll para acessar dados do SQLite em um aplicativo Xamarin. iOS.
ms.prod: xamarin
ms.assetid: F10C0C57-7BDE-A3F3-B011-9839949D15C8
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 11/25/2015
ms.openlocfilehash: 44d2e468efeacea919af2d243588d0da6d72945d
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70766545"
---
# <a name="systemdata-in-xamarinios"></a>System. Data no Xamarin. iOS

O Xamarin. Ios 8,10 adiciona suporte para [System. Data](xref:System.Data), incluindo `Mono.Data.Sqlite.dll` o provedor ADO.net. O suporte inclui a adição dos seguintes [assemblies](~/cross-platform/internals/available-assemblies.md):

- `System.Data.dll`
- `System.Data.Service.Client.dll`
- `System.Transactions.dll`
- `Mono.Data.Tds.dll`
- `Mono.Data.Sqlite.dll`

<a name="Example" />

## <a name="example"></a>Exemplo

O programa a seguir cria um banco `Documents/mydb.db3`de dados no e, se ele não existir anteriormente, ele será populado com um dado de exemplo. O banco de dados é consultado, com a saída gravada em `stderr`.

### <a name="add-references"></a>Adicionar referências

Primeiro, clique com o botão direito do mouse no nó **referências** e escolha **Editar referências.** . `System.Data` . `Mono.Data.Sqlite`em seguida, selecione e:

[![](system.data-images/edit-references-sml.png "Adicionando novas referências")](system.data-images/edit-references.png#lightbox)

### <a name="sample-code"></a>Código de exemplo

O código a seguir mostra um exemplo simples de criação de uma tabela e inserção de linhas usando comandos SQL inseridos:

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
> Conforme mencionado no exemplo de código acima, é uma prática inadequada inserir cadeias de caracteres em comandos SQL, pois torna seu código vulnerável à [injeção de SQL](https://en.wikipedia.org/wiki/SQL_injection).

### <a name="using-command-parameters"></a>Uso de parâmetros de comando

O código a seguir mostra como usar parâmetros de comando para inserir com segurança o texto inserido pelo usuário no banco de dados (mesmo que o texto contenha caracteres especiais do SQL como um apóstrofo):

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

O **System. Data** e o **mono. Data. sqlite** estão sem alguma funcionalidade.

<a name="System.Data" />

### <a name="systemdata"></a>System.Data

A funcionalidade ausente em **System. Data. dll** consiste em:

- Qualquer coisa que exija [System. CodeDom](xref:System.CodeDom) (por exemplo,  [System. Data. TypedDataSetGenerator](xref:System.Data.TypedDataSetGenerator) )
- Suporte ao arquivo de configuração XML (por exemplo,  [System. Data. Common. DbProviderConfigurationHandler](xref:System.Data.Common.DbProviderConfigurationHandler) )
- [System. Data. Common. DbProviderFactories](xref:System.Data.Common.DbProviderFactories) (depende do suporte a arquivo de configuração XML)
- [System.Data.OleDb](xref:System.Data.OleDb)
- [System.Data.Odbc](xref:System.Data.Odbc)
- A `System.EnterpriseServices.dll` dependência foi *removida* do `System.Data.dll` , resultando na remoção do método [SqlConnection. EnlistDistributedTransaction (ITransaction)](xref:System.Data.SqlClient.SqlConnection.EnlistDistributedTransaction*) .

<a name="Mono.Data.Sqlite" />

### <a name="monodatasqlite"></a>Mono.Data.Sqlite

Enquanto isso, **mono. Data. sqlite. dll** não sofreu alterações no código-fonte, mas pode ser host para vários problemas de tempo `Mono.Data.Sqlite.dll` de *execução* , pois associa o SQLite 3,5. o iOS 8, enquanto isso, é fornecido com o SQLite 3.8.5. Basta dizer que algumas coisas mudaram entre as duas versões.

A versão mais antiga do iOS é fornecida com as seguintes versões do SQLite:

- **Ios 7** -versão 3.7.13.
- **Ios 6** -versão 3.7.13.
- **iOS 5** -versão 3.7.7.
- **IOS 4** -versão 3.6.22.

Os problemas mais comuns parecem estar relacionados à consulta de esquema de banco de dados, por exemplo, determinando em tempo de execução quais colunas existem em `Mono.Data.Sqlite.SqliteConnection.GetSchema` uma determinada tabela, como ( `Mono.Data.Sqlite.SqliteDataReader.GetSchemaTable` substituindo [DbConnection. GetSchema](xref:System.Data.Common.DbConnection.GetSchema) e (substituindo [ DbDataReader. getesquematable](xref:System.Data.Common.DbDataReader.GetSchemaTable). Em suma, parece que algo que usa [DataTable](xref:System.Data.DataTable) provavelmente não funcionará.

<a name="Data_Binding" />

## <a name="data-binding"></a>Associação de dados

A associação de dados não tem suporte no momento.
