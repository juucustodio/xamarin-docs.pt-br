---
title: Usando o SQLite.NET com Xamarin. iOS
description: A biblioteca NuGet do SQLite.NET PCL fornece um mecanismo de acesso a dados simples para aplicativos Xamarin. iOS. Este documento fornece uma visão geral de como usar essa biblioteca.
ms.prod: xamarin
ms.assetid: 79813B09-42D7-47DD-AE71-A605E6B9EF24
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 04/18/2018
ms.openlocfilehash: 5b169323c606e48bf7eb0d721029c6d9a9db2831
ms.sourcegitcommit: 93e6358aac2ade44e8b800f066405b8bc8df2510
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/09/2020
ms.locfileid: "84571696"
---
# <a name="using-sqlitenet-with-xamarinios"></a>Usando o SQLite.NET com Xamarin. iOS

A biblioteca SQLite.NET que o Xamarin recomenda é um ORM básico que permite armazenar e recuperar objetos no banco de dados SQLite local em um dispositivo iOS.
ORM significa mapeamento relacional de objeto – uma API que permite salvar e recuperar "objetos" de um banco de dados sem escrever instruções SQL.

<a name="Usage"></a>

## <a name="usage"></a>Uso

Para incluir a biblioteca SQLite.NET em um aplicativo Xamarin, adicione o seguinte pacote NuGet ao seu projeto:

- **Nome do pacote:** SQLite-net-PCL
- **Autor:** Frank A. Krueger
- **Id:** sqlite-net-pcl
- **URL:** [NuGet.org/packages/SQLite-net-PCL](https://www.nuget.org/packages/sqlite-net-pcl/)

[![Pacote NuGet do SQLite.NET](using-sqlite-orm-images/image1a-sml.png "Pacote NuGet do SQLite.NET")](using-sqlite-orm-images/image1a.png#lightbox)

> [!TIP]
> Há vários pacotes SQLite diferentes disponíveis – certifique-se de escolher o correto (talvez não seja o principal resultado na pesquisa).

Depois de disponibilizar a biblioteca SQLite.NET, siga estas três etapas para usá-la para acessar um banco de dados:

1. **Adicionar uma instrução using** – adicione a seguinte instrução aos arquivos C# onde o acesso a dados é necessário:

    ```csharp
    using SQLite;
    ```

1. **Criar um banco de dados em branco** -uma referência de banco de dados pode ser criada passando o caminho do arquivo para o construtor da classe SQLiteConnection. Você não precisa verificar se o arquivo já existe – ele será criado automaticamente se necessário, caso contrário, o arquivo de banco de dados existente será aberto.

    ```csharp
    var db = new SQLiteConnection (dbPath);
    ```

    A variável dbPath deve ser determinada de acordo com as regras discutidas anteriormente neste documento.

1. **Salvar dados** -depois de criar um objeto SQLiteConnection, os comandos de banco de dados são executados chamando seus métodos, como CreateTable e Insert desta forma:

    ```csharp
    db.CreateTable<Stock> ();
    db.Insert (newStock); // after creating the newStock object
    ```

1. **Recuperar dados** -para recuperar um objeto (ou uma lista de objetos), use a seguinte sintaxe:

    ```csharp
    var stock = db.Get<Stock>(5); // primary key id of 5
    var stockList = db.Table<Stock>();
    ```

## <a name="basic-data-access-sample"></a>Exemplo de acesso a dados básico

O código de exemplo *DataAccess_Basic* para este documento é semelhante ao executado no Ios. O código ilustra como executar operações SQLite.NET simples e mostra os resultados em como texto na janela principal do aplicativo.

**iOS**

 [![exemplo de SQLite.NET do iOS](using-sqlite-orm-images/image2-sml.png)](using-sqlite-orm-images/image2-sml.png#lightbox)

O exemplo de código a seguir mostra uma interação de banco de dados inteira usando a biblioteca SQLite.NET para encapsular o acesso ao banco de dados subjacente. Ele mostra:

1. Criando o arquivo de banco de dados
1. Inserindo alguns dados criando objetos e salvando-os
1. Consulta dos dados

Você precisará incluir esses namespaces:

```csharp
using SQLite; // from the github SQLite.cs class
```

Isso requer que você tenha adicionado o SQLite ao seu projeto, como realçado [aqui](#Usage). Observe que a tabela de banco de dados SQLite é definida pela adição de atributos a uma classe (a `Stock` classe) em vez de um comando CREATE TABLE.

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

Usar o `[Table]` atributo sem especificar um parâmetro de nome de tabela fará com que a tabela de banco de dados subjacente tenha o mesmo nome que a classe (nesse caso, "stock"). O nome real da tabela será importante se você escrever consultas SQL diretamente no banco de dados, em vez de usar os métodos de acesso de dado de ORM. Da mesma forma `[Column("_id")]` , o atributo é opcional e, se estiver ausente, uma coluna será adicionada à tabela com o mesmo nome da propriedade na classe.

## <a name="sqlite-attributes"></a>Atributos do SQLite

Os atributos comuns que você pode aplicar às suas classes para controlar como eles são armazenados no banco de dados subjacente incluem:

- **[PrimaryKey]** – esse atributo pode ser aplicado a uma propriedade Integer para forçá-lo a ser a chave primária da tabela subjacente. Não há suporte para chaves primárias compostas.
- **[AutoIncrement]** – este atributo fará com que o valor de uma propriedade de inteiro seja incrementado automaticamente para cada novo objeto inserido no banco de dados
- **[Coluna (nome)]** &ndash; O `name` parâmetro define o nome da coluna de banco de dados subjacente.
- **[Table (Name)]** – marca a classe como sendo capaz de ser armazenada em uma tabela SQLite subjacente com o nome especificado.
- **[MaxLength (value)]** – restringir o comprimento de uma propriedade de texto quando uma tentativa de inserção de banco de dados for tentada. O consumo de código deve validar isso antes de inserir o objeto, pois esse atributo só é "verificado" quando uma operação de inserção ou atualização de banco de dados é tentada.
- **[Ignorar]** – faz com que o SQLite.net ignore essa propriedade. Isso é particularmente útil para propriedades que têm um tipo que não pode ser armazenado no banco de dados ou propriedades que modelam coleções que não podem ser resolvidas automaticamente são SQLite.
- **[Unique]** – garante que os valores na coluna banco de dados subjacente sejam exclusivos.

A maioria desses atributos é opcional. Você sempre deve especificar uma chave primária de inteiro para que as consultas seleção e exclusão possam ser executadas com eficiência em seus dados.

## <a name="more-complex-queries"></a>Consultas mais complexas

Os seguintes métodos em `SQLiteConnection` podem ser usados para executar outras operações de dados:

- **Inserir** – adiciona um novo objeto ao banco de dados.
- **Obter \<T> ** – Tenta recuperar um objeto usando a chave primária.
- **Tabela \<T> ** – Retorna todos os objetos na tabela.
- **Excluir** – exclui um objeto usando sua chave primária.
- **Consulta \<T> ** do -Executar uma consulta SQL que retorna um número de linhas (como objetos).
- **Execute** – Use este método (e não `Query` ) quando você não espera linhas do SQL (como instruções INSERT, Update e Delete).

### <a name="getting-an-object-by-the-primary-key"></a>Obtendo um objeto pela chave primária

SQLite.Net fornece o método Get para recuperar um único objeto com base em sua chave primária.

```csharp
var existingItem = db.Get<Stock>(3);
```

### <a name="selecting-an-object-using-linq"></a>Selecionando um objeto usando LINQ

Métodos que retornam coleções dão suporte a IEnumerable para \<T> que você possa usar o LINQ para consultar ou classificar o conteúdo de uma tabela. O código a seguir mostra um exemplo usando o LINQ para filtrar todas as entradas que começam com a letra "A":

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

> [!IMPORTANT]
> Ao escrever instruções SQL diretamente, você cria uma dependência nos nomes de tabelas e colunas no banco de dados, que foram geradas a partir de suas classes e seus atributos. Se você alterar esses nomes em seu código, deverá se lembrar de atualizar quaisquer instruções SQL gravadas manualmente.

### <a name="deleting-an-object"></a>Excluindo um objeto

A chave primária é usada para excluir a linha, conforme mostrado aqui:

```csharp
var rowcount = db.Delete<Stock>(someStock.Id); // Id is the primary key
```

Você pode verificar o `rowcount` para confirmar quantas linhas foram afetadas (excluídas neste caso).

## <a name="using-sqlitenet-with-multiple-threads"></a>Usando SQLite.NET com vários threads

O SQLite dá suporte a três modos de Threading diferentes: *thread único*, *vários threads*e *serializados*. Se você quiser acessar o banco de dados de vários threads sem restrições, poderá configurar o SQLite para usar o modo de Threading **serializado** . É importante definir esse modo no início do aplicativo (por exemplo, no início do `OnCreate` método).

Para alterar o modo de Threading, chame `SqliteConnection.SetConfig` que está no `Mono.Data.Sqlite` namespace. Por exemplo, esta linha de código configura o SQLite para o modo **serializado** :

```csharp
using Mono.Data.Sqlite;
...
SqliteConnection.SetConfig(SQLiteConfig.Serialized);
```

## <a name="related-links"></a>Links relacionados

- [DataAccess básico (exemplo)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic)
- [DataAccess avançado (exemplo)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced)
- [Acesso a dados do Xamarin. Forms](~/xamarin-forms/data-cloud/data/databases.md)
