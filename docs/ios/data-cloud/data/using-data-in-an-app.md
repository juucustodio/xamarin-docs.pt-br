---
title: Usando dados em um aplicativo iOS
description: Este documento descreve o DataAccess_Adv exemplo, que demonstra como coletar entrada de usuário e executar operações de banco de dados CRUD (criar, ler, atualizar e excluir) em um aplicativo Xamarin. iOS.
ms.prod: xamarin
ms.assetid: 2CB8150E-CD2C-4E97-8605-1EE8CBACFEEC
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 10/11/2016
ms.openlocfilehash: c888c132748c4212b1e52413647614ca83897d75
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/22/2020
ms.locfileid: "86938509"
---
# <a name="using-data-in-an-ios-app"></a>Usando dados em um aplicativo iOS

O exemplo a **DataAccess_Adv** mostra um aplicativo de trabalho que permite a entrada do usuário e a funcionalidade de banco de dados *CRUD* (criar, ler, atualizar e excluir). O aplicativo consiste em duas telas: uma lista e um formulário de entrada de dados. Todo o código de acesso a dados é reutilizável no iOS e no Android sem modificação.

Depois de adicionar alguns dados, as telas de aplicativo têm esta aparência no iOS:

 ![lista de exemplos do iOS](using-data-in-an-app-images/image9.png)

 ![detalhes de exemplo do iOS](using-data-in-an-app-images/image10.png)

O projeto do iOS é mostrado abaixo – o código mostrado nesta seção está contido no diretório **ORM** :

 ![árvore de projeto do iOS](using-data-in-an-app-images/image13.png)

O código da interface do usuário nativa para o ViewControllers no iOS está fora do escopo deste documento.
Consulte o guia de [trabalho do IOS com tabelas e células](~/ios/user-interface/controls/tables/index.md) para obter mais informações sobre os controles da interface do usuário.

## <a name="read"></a>Ler

Há algumas operações de leitura no exemplo:

- Lendo a lista
- Lendo registros individuais

Os dois métodos na `StockDatabase` classe são:

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

o iOS renderiza os dados de forma diferente como um `UITableView` .

## <a name="create-and-update"></a>Criar e atualizar

Para simplificar o código do aplicativo, é fornecido um único método de salvamento que faz uma inserção ou atualização, dependendo se a PrimaryKey foi definida. Como a `Id` propriedade é marcada com um `[PrimaryKey]` atributo, você não deve defini-la em seu código.
Esse método detectará se o valor foi salvo anteriormente (verificando a propriedade de chave primária) e inserirá ou atualizará o objeto de acordo:

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

Aplicativos do mundo real normalmente exigirão alguma validação (como campos obrigatórios, comprimentos mínimos ou outras regras de negócio).
Bons aplicativos de plataforma cruzada implementam a maior parte possível da validação lógica no código compartilhado, passando erros de validação para a interface do usuário para exibição de acordo com os recursos da plataforma.

## <a name="delete"></a>Excluir

Ao contrário `Insert` dos `Update` métodos e, o `Delete<T>` método pode aceitar apenas o valor de chave primária em vez de um `Stock` objeto completo.
Neste exemplo, um `Stock` objeto é passado para o método, mas apenas a propriedade ID é passada para o `Delete<T>` método.

```csharp
public int DeleteStock(Stock stock)
{
    lock (locker) {
        return Delete<Stock> (stock.Id);
    }
}
```

## <a name="using-a-pre-populated-sqlite-database-file"></a>Usando um arquivo de banco de dados SQLite previamente preenchido

Alguns aplicativos são fornecidos com um banco de dados já populado com data.
Você pode fazer isso facilmente em seu aplicativo móvel enviando um arquivo de banco de dados SQLite existente com seu aplicativo e copiando-o em um diretório gravável antes de acessá-lo. Como o SQLite é um formato de arquivo padrão usado em muitas plataformas, há várias ferramentas disponíveis para criar um arquivo de banco de dados do SQLite:

- **Extensão Firefox do Gerenciador do SQLite** – funciona em Mac e Windows e produz arquivos que são compatíveis com Ios e Android.
- **Linha de comando** – consulte [www.sqlite.org/sqlite.html](https://www.sqlite.org/sqlite.html) .

Ao criar um arquivo de banco de dados para distribuição com seu aplicativo, tome cuidado com a nomenclatura de tabelas e colunas para garantir que elas correspondam ao que o código espera, especialmente se você estiver usando SQLite.NET que esperará que os nomes correspondam às suas classes e propriedades do C# (ou aos atributos personalizados associados).

Para o iOS, inclua o arquivo SQLite em seu aplicativo e verifique se ele está marcado com a **ação de Build: content**. Coloque o código no `FinishedLaunching` para copiar o arquivo em um diretório gravável *antes* de chamar qualquer método de dados. O código a seguir copiará um banco de **dados existente chamado Data. sqlite**, somente se ele ainda não existir.

```csharp
// Copy the database across (if it doesn't exist)
var appdir = NSBundle.MainBundle.ResourcePath;
var seedFile = Path.Combine (appdir, "data.sqlite");
if (!File.Exists (Database.DatabaseFilePath))
{
  File.Copy (seedFile, Database.DatabaseFilePath);
}
```

Qualquer código de acesso a dados (se ADO.NET ou usando SQLite.NET) que é executado depois de concluída terá acesso aos dados preenchidos previamente.

## <a name="related-links"></a>Links Relacionados

- [DataAccess básico (exemplo)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic)
- [DataAccess avançado (exemplo)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced)
- [Receitas de dados do iOS](https://github.com/xamarin/recipes/tree/master/Recipes/ios/data/sqlite)
- [Acesso a dados do Xamarin. Forms](~/xamarin-forms/data-cloud/data/databases.md)
