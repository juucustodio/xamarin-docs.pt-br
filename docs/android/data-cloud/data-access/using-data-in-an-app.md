---
title: Usando dados em um aplicativo Android
ms.prod: xamarin
ms.assetid: D5932AEB-0B6E-4F37-8B32-9BE4775AEE85
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/08/2018
ms.openlocfilehash: 922b1fa411a176df580050384e7555120fd68137
ms.sourcegitcommit: 9bfedf07940dad7270db86767eb2cc4007f2a59f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/21/2019
ms.locfileid: "70754453"
---
# <a name="using-data-in-an-app"></a>Usar dados em um aplicativo

O exemplo **DataAccess_Adv** mostra um aplicativo de trabalho que permite a entrada de usuário e a funcionalidade de banco de dados CRUD (criar, ler, atualizar e excluir). O aplicativo consiste em duas telas: uma lista e um formulário de entrada de dados. Todo o código de acesso a dados é reutilizável no iOS e no Android sem modificação.

Depois de adicionar alguns dados, as telas de aplicativo têm esta aparência no Android:

![Lista de exemplos do Android](using-data-in-an-app-images/image11.png "Lista de exemplos do Android")

![Detalhes de exemplo do Android](using-data-in-an-app-images/image12.png "Detalhes de exemplo do Android")

O projeto do Android é mostrado abaixo &ndash; o código mostrado nesta seção está contido no diretório **ORM** :

![Árvore de projeto do Android](using-data-in-an-app-images/image14.png "Árvore de projeto do Android")

O código da interface do usuário nativa para as atividades no Android está fora do escopo deste documento. Consulte o guia de [ListViews e adaptadores do Android](~/android/user-interface/layouts/list-view/index.md) para obter mais informações sobre os controles da interface do usuário.

## <a name="read"></a>Ler

Há algumas operações de leitura no exemplo:

- Lendo a lista
- Lendo registros individuais

Os dois métodos na classe `StockDatabase` são:

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

O Android renderiza os dados como um `ListView`.

## <a name="create-and-update"></a>Criar e atualizar

Para simplificar o código do aplicativo, é fornecido um único método de salvamento que faz uma inserção ou atualização, dependendo se a PrimaryKey foi definida. Como a propriedade `Id` é marcada com um atributo `[PrimaryKey]`, você não deve defini-la em seu código. Esse método detectará se o valor foi salvo anteriormente (verificando a propriedade de chave primária) e inserirá ou atualizará o objeto de acordo:

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

Aplicativos do mundo real normalmente exigirão alguma validação (como campos obrigatórios, comprimentos mínimos ou outras regras de negócio). Bons aplicativos de plataforma cruzada implementam a maior parte possível da validação lógica no código compartilhado, passando erros de validação para a interface do usuário para exibição de acordo com os recursos da plataforma.

## <a name="delete"></a>Excluir

Ao contrário dos métodos `Insert` e `Update`, o método `Delete<T>` pode aceitar apenas o valor da chave primária em vez de um objeto `Stock` completo. Neste exemplo, um objeto `Stock` é passado para o método, mas apenas a propriedade ID é passada para o método `Delete<T>`.

```csharp
public int DeleteStock(Stock stock)
{
    lock (locker) {
        return Delete<Stock> (stock.Id);
    }
}
```

## <a name="using-a-pre-populated-sqlite-database-file"></a>Usando um arquivo de banco de dados SQLite previamente preenchido

Alguns aplicativos são fornecidos com um banco de dados já populado com data. Você pode fazer isso facilmente em seu aplicativo móvel enviando um arquivo de banco de dados SQLite existente com seu aplicativo e copiando-o em um diretório gravável antes de acessá-lo. Como o SQLite é um formato de arquivo padrão usado em muitas plataformas, há várias ferramentas disponíveis para criar um arquivo de banco de dados do SQLite:

- A **extensão Firefox do Gerenciador do SQLite** &ndash; funciona no Mac e no Windows e produz arquivos que são compatíveis com Ios e Android.

- @No__t_1 de **linha de comando** consulte [www.sqlite.org/SQLite.html](http://www.sqlite.org/sqlite.html) .

Ao criar um arquivo de banco de dados para distribuição com seu aplicativo, tome cuidado com a nomenclatura de tabelas e colunas para garantir que elas correspondam ao que o código espera, especialmente se você estiver usando SQLite.NET que C# esperará que os nomes correspondam às suas classes e Propriedades ( ou os atributos personalizados associados).

Para garantir que algum código seja executado antes de qualquer outra coisa em seu aplicativo Android, você pode colocá-lo na primeira atividade para carregar ou você pode criar uma subclasse de `Application` que é carregada antes de qualquer atividade. O código a seguir mostra uma subclasse `Application` que copia um arquivo de banco de **dados existente. sqlite** do diretório **/Resources/RAW/**

```csharp
[Application]
public class YourAndroidApp : Application {
    public override void OnCreate ()
    {
        base.OnCreate ();
        var docFolder = Environment.GetFolderPath(Environment.SpecialFolder.Personal);
        Console.WriteLine ("Data path:" + Database.DatabaseFilePath);
        var dbFile = Path.Combine(docFolder, "data.sqlite"); // FILE NAME TO USE WHEN COPIED
        if (!System.IO.File.Exists(dbFile)) {
            var s = Resources.OpenRawResource(Resource.Raw.data);  // DATA FILE RESOURCE ID
            FileStream writeStream = new FileStream(dbFile, FileMode.OpenOrCreate, FileAccess.Write);
            ReadWriteStream(s, writeStream);
        }
    }
    // readStream is the stream you need to read
    // writeStream is the stream you want to write to
    private void ReadWriteStream(Stream readStream, Stream writeStream)
    {
        int Length = 256;
        Byte[] buffer = new Byte[Length];
        int bytesRead = readStream.Read(buffer, 0, Length);
        // write the required bytes
        while (bytesRead > 0)
        {
            writeStream.Write(buffer, 0, bytesRead);
            bytesRead = readStream.Read(buffer, 0, Length);
        }
        readStream.Close();
        writeStream.Close();
    }
}
```

## <a name="related-links"></a>Links relacionados

- [DataAccess básico (exemplo)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic)
- [DataAccess avançado (exemplo)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced)
- [Receitas de dados do Android](https://github.com/xamarin/recipes/tree/master/Recipes/android/data)
- [Acesso a dados do Xamarin. Forms](~/xamarin-forms/data-cloud/data/databases.md)
