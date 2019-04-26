---
title: Usar SQLite.NET com xamarin. IOS
description: A biblioteca de NuGet de PCL SQLite.NET fornece um mecanismo de acesso de dados simples para aplicativos xamarin. IOS. Este documento fornece uma visão geral de como usar essa biblioteca.
ms.prod: xamarin
ms.assetid: 79813B09-42D7-47DD-AE71-A605E6B9EF24
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 04/18/2018
ms.openlocfilehash: 370867b52ec09d0c3ad0f801b6a75c356d806734
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61277823"
---
# <a name="using-sqlitenet-with-xamarinios"></a>Usar SQLite.NET com xamarin. IOS

A biblioteca de SQLite.NET Xamarin recomenda é um ORM básico que lhe permite armazenar e recuperar objetos no banco de dados SQLite local em um dispositivo iOS.
ORM significa mapeamento relacional objeto – uma API que permite salvar e recuperar "objetos" de um banco de dados sem escrever instruções SQL.

<a name="Usage"/>

## <a name="usage"></a>Uso

Para incluir a biblioteca SQLite.NET em um aplicativo Xamarin, adicione o seguinte pacote NuGet ao seu projeto:

- **Nome do pacote:** sqlite-net-pcl
- **Autor:** Frank A. Krueger
- **Id:** sqlite-net-pcl
- **Url:** [nuget.org/packages/sqlite-net-pcl](https://www.nuget.org/packages/sqlite-net-pcl/)

[![Pacote do SQLite.NET NuGet](using-sqlite-orm-images/image1a-sml.png "SQLite.NET mobileengagement")](using-sqlite-orm-images/image1a.png#lightbox)

> [!TIP]
> Há um número de pacotes do SQLite diferentes disponível – não se esqueça de escolher a correta (ele pode não ser o resultado principal na pesquisa).

Depois que a biblioteca de SQLite.NET disponível, siga estas três etapas para usá-lo para acessar um banco de dados:

1. **Adicionar uma instrução** -adicione a seguinte instrução para o C# arquivos em que o acesso a dados é necessário:

    ```csharp
    using SQLite;
    ```

1. **Criar um banco de dados em branco** -uma referência de banco de dados pode ser criada, passando o caminho do arquivo o construtor da classe SQLiteConnection. Você não precisa verificar se o arquivo já existir, ele será criado automaticamente se necessário, caso contrário, o arquivo de banco de dados existente será aberto.

    ```csharp
    var db = new SQLiteConnection (dbPath);
    ```

    A variável dbPath deve ser determinada de acordo com as regras discutidas anteriormente neste documento.

1. **Salvar dados** - uma vez que você criou um objeto SQLiteConnection, banco de dados de comandos são executados chamando seus métodos, como CreateTable e inserir como este:

    ```csharp
    db.CreateTable<Stock> ();
    db.Insert (newStock); // after creating the newStock object
    ```

1. **Recuperar dados** – para recuperar um objeto (ou uma lista de objetos) use a seguinte sintaxe:

    ```csharp
    var stock = db.Get<Stock>(5); // primary key id of 5
    var stockList = db.Table<Stock>();
    ```

## <a name="basic-data-access-sample"></a>Exemplo de acesso de dados básicos

O *DataAccess_Basic* código de exemplo para este documento se parece com isso, quando em execução no iOS. O código ilustra como executar operações de SQLite.NET simples e mostra os resultados como texto na janela principal do aplicativo.

**iOS**

 [![exemplo de SQLite.NET do iOS](using-sqlite-orm-images/image2-sml.png)](using-sqlite-orm-images/image2-sml.png#lightbox)

O exemplo de código a seguir mostra uma interação de banco de dados inteiro usando a biblioteca de SQLite.NET para encapsular o acesso de banco de dados subjacente. Ele mostra:

1.  Criando o arquivo de banco de dados
1.  Inserir alguns dados por meio da criação de objetos e, em seguida, salvá-las
1.  Consultando os dados

Você precisará incluir esses namespaces:

```csharp
using SQLite; // from the github SQLite.cs class
```

Isso exige que você adicionou SQLite para seu projeto, como destacado [aqui](#Usage). Observe que a tabela de banco de dados SQLite é definida adicionando atributos a uma classe (a `Stock` classe) em vez de um comando CREATE TABLE.

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

Usando o `[Table]` atributo sem especificar um parâmetro de nome de tabela fará com que a tabela de banco de dados subjacente ter o mesmo nome que a classe (nesse caso, "Stock"). O nome da tabela real é importante se você escreve consultas SQL diretamente no banco de dados em vez de usa os métodos de acesso de dados ORM. Da mesma forma a `[Column("_id")]` atributo é opcional e se a ausência de uma coluna será adicionada à tabela com o mesmo nome que a propriedade na classe.

## <a name="sqlite-attributes"></a>Atributos do SQLite

Atributos comuns que você pode aplicar às suas classes para controlar como eles são armazenados no banco de dados subjacente incluem:

-  **[PrimaryKey]**  – Esse atributo pode ser aplicado a uma propriedade de inteiro para forçá-lo para ser a chave primária da tabela subjacente. Não há suporte para chaves primárias compostas.
-  **[AutoIncrement]**  – Esse atributo fará com que um valor da propriedade de um inteiro para ser incremento automático para cada novo objeto inserido no banco de dados
-  **[Column(name)]**  – Fornecendo opcional `name` parâmetro substituirá o valor padrão de nome da coluna de banco de dados subjacente (que é o mesmo que a propriedade).
-  **[Table(name)]**  – Marca a classe como sendo podem ser armazenados em uma tabela base do SQLite. Especificando o parâmetro de nome opcional substituirá o valor padrão de nome da tabela de banco de dados subjacente (que é o mesmo que o nome de classe).
-  **[MaxLength(value)]**  – Restringe o comprimento de uma propriedade de texto, quando uma inserção de banco de dados é tentada. Consumir o código deve validar isso antes de inserir o objeto como esse atributo é 'verificado apenas' durante uma inserção de banco de dados ou a tentativa de operação de atualização.
-  **[Ignorar]**  – Faz com que SQLite.NET para ignorar essa propriedade. Isso é particularmente útil para as propriedades que têm um tipo que não pode ser armazenado no banco de dados ou que as coleções de modelo que não podem ser resolvidas automaticamente ser SQLite.
-  **[Unique]**  – Garante que os valores na coluna de banco de dados subjacentes sejam exclusivos.


A maioria desses atributos é opcional, SQLite usará os valores padrão para nomes de tabela e coluna. Você sempre deve especificar uma chave primária de inteiro para que a seleção e a exclusão de consultas podem ser executadas com eficiência em seus dados.

## <a name="more-complex-queries"></a>Consultas mais complexas

Os seguintes métodos no `SQLiteConnection` pode ser usado para executar outras operações de dados:

-  **Inserir** – adiciona um novo objeto no banco de dados.
-  **Obtenha<T>**  – tenta recuperar um objeto usando a chave primária.
-  **Tabela<T>**  – retorna todos os objetos na tabela.
-  **Excluir** – exclui um objeto usando sua chave primária.
-  **Consulta<T>**  -executar uma consulta SQL que retorna um número de linhas (como objetos).
-  **Execute** – Use esse método (e não `Query` ) quando você não espera linhas do SQL (como instruções INSERT, UPDATE e DELETE).


### <a name="getting-an-object-by-the-primary-key"></a>Obtendo um objeto pela chave primária

SQLite.Net fornece o método Get para recuperar um único objeto com base em sua chave primária.

```csharp
var existingItem = db.Get<Stock>(3);
```

### <a name="selecting-an-object-using-linq"></a>Selecionar um objeto usando Linq

Suportam a métodos que retornam coleções IEnumerable<T> para que você pode usar o Linq para consultar ou classificar o conteúdo de uma tabela. O código a seguir mostra um exemplo usando o Linq para filtrar todas as entradas que começam com a letra "A":

```csharp
var apple = from s in db.Table<Stock>()
    where s.Symbol.StartsWith ("A")
    select s;
Console.WriteLine ("-> " + apple.FirstOrDefault ().Symbol);
```

### <a name="selecting-an-object-using-sql"></a>Selecionar um objeto usando o SQL

Embora SQLite.Net pode fornecer acesso baseado em objeto aos seus dados, às vezes, você talvez precise fazer uma consulta mais complexa do que o Linq permite que (ou talvez seja necessário um desempenho mais rápido). Você pode usar comandos SQL com o método de consulta, conforme mostrado aqui:

```csharp
var stocksStartingWithA = db.Query<Stock>("SELECT * FROM Items WHERE Symbol = ?", "A");
foreach (var s in stocksStartingWithA) {
    Console.WriteLine ("a " + s.Symbol);
}
```

> [!IMPORTANT]
> Ao escrever instruções SQL diretamente, você criar uma dependência nos nomes das tabelas e colunas em seu banco de dados que foram geradas a partir de suas classes e seus atributos. Se você alterar esses nomes em seu código, você deve lembrar de atualizar quaisquer declarações SQL escritas manualmente.

### <a name="deleting-an-object"></a>Exclusão de um objeto

A chave primária é usada para excluir a linha, conforme mostrado aqui:

```csharp
var rowcount = db.Delete<Stock>(someStock.Id); // Id is the primary key
```

Você pode verificar o `rowcount` para confirmar o número de linhas afetado (nesse caso, excluído).

## <a name="using-sqlitenet-with-multiple-threads"></a>Usar SQLite.NET com vários Threads

SQLite oferece suporte a três modos diferentes de threads: *Single-thread*, *multi-thread*, e *serializado*. Se você quiser acessar o banco de dados de vários threads sem quaisquer restrições, você pode configurar o SQLite para usar o **serializado** modo de threading. É importante definir esse modo no início do seu aplicativo (por exemplo, no início do `OnCreate` método).

Para alterar o modo de threading, chame `SqliteConnection.SetConfig` que está no `Mono.Data.Sqlite` namespace. Por exemplo, esta linha de código configura o SQLite para **serializado** modo:

```csharp
using Mono.Data.Sqlite;
...
SqliteConnection.SetConfig(SQLiteConfig.Serialized);
```

## <a name="related-links"></a>Links relacionados

- [DataAccess Basic (exemplo)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic)
- [DataAccess avançadas (amostra)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced)
- [Acesso a dados do xamarin. Forms](~/xamarin-forms/app-fundamentals/databases.md)
