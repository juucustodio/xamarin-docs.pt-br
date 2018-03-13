---
title: Usando dados de um aplicativo
ms.topic: article
ms.prod: xamarin
ms.assetid: 2CB8150E-CD2C-4E97-8605-1EE8CBACFEEC
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 10/11/2016
ms.openlocfilehash: aefec1992a5f061f9d50d499b3594601a2651b17
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/12/2018
---
# <a name="using-data-in-an-app"></a>Usando dados de um aplicativo

O **DataAccess_Adv** exemplo mostra um aplicativo de trabalho que permite que a entrada do usuário e *CRUD* funcionalidade de banco de dados (criar, ler, atualizar e excluir). O aplicativo consiste em duas telas: uma lista e um formulário de entrada de dados. Todo o código de acesso de dados é reutilizável no iOS e Android sem modificação.

Depois de adicionar alguns dados as telas de aplicativo assim no iOS:

 ![](using-data-in-an-app-images/image9.png "lista de exemplos de iOS")

 ![](using-data-in-an-app-images/image10.png "detalhes da amostra de iOS")

IOS projeto é mostrado abaixo – o código mostrado nesta seção está contido dentro de **Orm** diretório:

 ![](using-data-in-an-app-images/image13.png "árvore de projeto do iOS")

O código nativo de interface do usuário para o ViewControllers no iOS está fora do escopo deste documento.
Consulte o [iOS trabalhando com tabelas e células](~/ios/user-interface/controls/tables/index.md) guia para obter mais informações sobre os controles de interface do usuário.

## <a name="read"></a>Ler

Há duas operações de leitura no exemplo:

-  Ler a lista de
-  Ler registros individuais


Os dois métodos no `StockDatabase` classe são:

```csharp
public IEnumerable<Stock> GetStocks ()
{
    lock (locker) {
        return (from i in Table<Stock> () select i).ToList ();
    }
}
public Stock GetStock (int id)
{
    lock (locker) {
        return Table<Stock>().FirstOrDefault(x => x.Id == id);
    }
}
```

iOS processa os dados de forma diferente, como um `UITableView`.

## <a name="create-and-update"></a>Criar e atualizar

Para simplificar o código do aplicativo, um único método de salvamento é fornecida, que faz uma inserção ou atualização dependendo se o PrimaryKey foi definida. Porque o `Id` propriedade é marcada com um `[PrimaryKey]` atributo que você não deve configurá-lo em seu código.
Esse método irá detectar se o valor tiver sido anterior salvo (Verificando a propriedade de chave primária) e inserir ou atualizar o objeto adequadamente:

```csharp
public int SaveStock (Stock item)
{
    lock (locker) {
        if (item.Id != 0) {
            Update (item);
            return item.Id;
    } else {
            return Insert (item);
        }
    }
}
```



Aplicativos do mundo real geralmente exige alguma validação (como campos obrigatórios, tamanhos mínimo ou outras regras de negócios).
Aplicativos de plataforma cruzada bom implementam tanta da validação lógica como possíveis no código compartilhado, passando os erros de validação de volta até a interface do usuário para exibição de acordo com os recursos da plataforma.

## <a name="delete"></a>Excluir

Ao contrário de `Insert` e `Update` métodos, o `Delete<T>` método pode aceitar apenas o valor de chave primária em vez de um conjunto completo `Stock` objeto.
Neste exemplo um `Stock` objeto é passado para o método, mas apenas a propriedade Id é passada para o `Delete<T>` método.

```csharp
public int DeleteStock(Stock stock)
{
    lock (locker) {
        return Delete<Stock> (stock.Id);
    }
}
```

## <a name="using-a-pre-populated-sqlite-database-file"></a>Usando um arquivo de banco de dados SQLite preenchido previamente

Alguns aplicativos são fornecidos com um banco de dados já preenchido com dados.
Você pode fazer isso facilmente em seu aplicativo móvel pelo envio de um arquivo de banco de dados SQLite existente com o seu aplicativo e copiá-la para um diretório gravável para acessá-lo. Como SQLite é um formato de arquivo padrão que é usado em muitas plataformas, há várias ferramentas disponíveis para criar um arquivo de banco de dados SQLite:

-  **Extensão do Gerenciador do SQLite Firefox** – funciona no Mac e Windows e produz arquivos que são compatíveis com o iOS e Android.
-  **Linha de comando** – consulte [www.sqlite.org/sqlite.html](http://www.sqlite.org/sqlite.html) .


Ao criar um arquivo de banco de dados de distribuição com seu aplicativo, tome cuidado com os nomes de tabelas e colunas para garantir que elas correspondam o que espera que seu código, especialmente se você estiver usando SQLite.NET que esperam que os nomes para coincidir com o c# classes e propriedades (ou o atributos personalizados associados).

Para iOS, inclua o arquivo sqlite em seu aplicativo e certifique-se de que ele está marcado com **ação de compilação: conteúdo**. Coloque o código de `FinishedLaunching` para copiar o arquivo para um diretório gravável *antes de* você chama qualquer método de dados. O código a seguir irá copiar um banco de dados chamado **data.sqlite**, apenas se ele ainda não existir.

```csharp
// Copy the database across (if it doesn't exist)
var appdir = NSBundle.MainBundle.ResourcePath;
var seedFile = Path.Combine (appdir, "data.sqlite");
if (!File.Exists (Database.DatabaseFilePath))
{
  File.Copy (seedFile, Database.DatabaseFilePath);
}
```

Qualquer código de acesso a dados (se ADO.NET ou usando SQLite.NET) que é executado depois que isso tiver concluído será têm acesso aos dados preenchidos previamente.


## <a name="related-links"></a>Links relacionados

- [DataAccess Basic (exemplo)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic)
- [DataAccess avançados (amostra)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced)
- [iOS receitas de dados](https://developer.xamarin.com/recipes/ios/data/sqlite/)
- [Acesso a dados xamarin. Forms](~/xamarin-forms/app-fundamentals/databases.md)
