---
title: Usando dados em um aplicativo iOS
description: Este documento descreve o DataAccess_Adv exemplo, que demonstra como coletar entrada do usuário e executar a criar, ler, atualizar e excluir (CRUD) operações de banco de dados em um aplicativo xamarin. IOS.
ms.prod: xamarin
ms.assetid: 2CB8150E-CD2C-4E97-8605-1EE8CBACFEEC
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 10/11/2016
ms.openlocfilehash: a47ab26777c594658810b014025477486bbe5cc2
ms.sourcegitcommit: c1d85b2c62ad84c22bdee37874ad30128581bca6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/08/2019
ms.locfileid: "67650144"
---
# <a name="using-data-in-an-ios-app"></a>Usando dados em um aplicativo iOS

O **DataAccess_Adv** exemplo mostra um aplicativo funcional que permite entrada do usuário e *CRUD* funcionalidade de banco de dados (criar, ler, atualizar e excluir). O aplicativo consiste em duas telas: uma lista e um formulário de entrada de dados. Todo o código de acesso de dados é pode ser reutilizado no iOS e Android sem modificação.

Depois de adicionar alguns dados as telas do aplicativo esta aparência no iOS:

 ![](using-data-in-an-app-images/image9.png "lista de exemplos do iOS")

 ![](using-data-in-an-app-images/image10.png "detalhes de exemplo do iOS")

O iOS o projeto é mostrado abaixo – o código mostrado nesta seção está contido dentro de **Orm** diretório:

 ![](using-data-in-an-app-images/image13.png "árvore de projeto do iOS")

O código nativo de interface do usuário para o ViewControllers no iOS está fora do escopo deste documento.
Consulte a [iOS trabalhando com tabelas e células](~/ios/user-interface/controls/tables/index.md) guia para obter mais informações sobre os controles de interface do usuário.

## <a name="read"></a>Ler

Há duas operações de leitura no exemplo:

-  A lista de leitura
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

Para simplificar o código do aplicativo, um único método de salvar é fornecido que faz uma inserção ou atualização, dependendo se o PrimaryKey tiver sido definido. Porque o `Id` propriedade é marcada com um `[PrimaryKey]` atributo que você não deve defini-lo em seu código.
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



Aplicativos do mundo real geralmente exigirá algumas validações (como campos obrigatórios, comprimentos mínimo ou outras regras de negócios).
Implementam bons aplicativos de plataforma cruzada como grande parte da validação lógica como possível em código compartilhado, passando os erros de validação de volta até a interface do usuário para exibição de acordo com os recursos da plataforma.

## <a name="delete"></a>Excluir

Ao contrário do `Insert` e `Update` métodos, o `Delete<T>` método pode aceitar apenas o valor de chave primária em vez de uma completa `Stock` objeto.
Neste exemplo uma `Stock` objeto é passado para o método, mas apenas a propriedade de Id é passada para o `Delete<T>` método.

```csharp
public int DeleteStock(Stock stock)
{
    lock (locker) {
        return Delete<Stock> (stock.Id);
    }
}
```

## <a name="using-a-pre-populated-sqlite-database-file"></a>Usando um arquivo de banco de dados SQLite previamente preenchido

Alguns aplicativos são fornecidos com um banco de dados já preenchido com dados.
Você pode fazer isso facilmente no seu aplicativo móvel pelo envio de um arquivo de banco de dados SQLite existente com o seu aplicativo e copiá-lo para um diretório gravável para acessá-lo. Como o SQLite é um formato de arquivo padrão que é usado em muitas plataformas, existem várias ferramentas disponíveis para criar um arquivo de banco de dados SQLite:

-  **Extensão do Firefox SQLite Manager** – funciona no Mac e Windows e produz arquivos que são compatíveis com iOS e Android.
-  **Linha de comando** – consulte [www.sqlite.org/sqlite.html](http://www.sqlite.org/sqlite.html) .


Ao criar um arquivo de banco de dados de distribuição com seu aplicativo, tenha cuidado com a nomenclatura de tabelas e colunas para garantir que elas correspondam que espera seu código, especialmente se você estiver usando SQLite.NET que espera que os nomes para corresponder a suas propriedades e classes c# (ou o atributos personalizados associados).

Para iOS, incluir o arquivo do sqlite em seu aplicativo e certifique-se de que ele é marcado com **ação de compilação: Content**. Coloque o código a `FinishedLaunching` para copiar o arquivo para um diretório gravável *antes de* você chama qualquer método de dados. O código a seguir irá copiar um banco de dados chamado **data.sqlite**, somente se ele ainda não existir.

```csharp
// Copy the database across (if it doesn't exist)
var appdir = NSBundle.MainBundle.ResourcePath;
var seedFile = Path.Combine (appdir, "data.sqlite");
if (!File.Exists (Database.DatabaseFilePath))
{
  File.Copy (seedFile, Database.DatabaseFilePath);
}
```

Qualquer código de acesso a dados (se ADO.NET ou SQLite.NET de uso) que é executado depois que isso tem serão concluída têm acesso aos dados previamente preenchidos.


## <a name="related-links"></a>Links relacionados

- [DataAccess Basic (exemplo)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic)
- [DataAccess avançadas (amostra)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced)
- [iOS receitas de dados](https://github.com/xamarin/recipes/tree/master/Recipes/ios/data/sqlite)
- [Acesso a dados do xamarin. Forms](~/xamarin-forms/data-cloud/data/databases.md)
