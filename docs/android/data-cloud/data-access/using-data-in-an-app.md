---
title: Usando dados em um aplicativo do Android
ms.prod: xamarin
ms.assetid: D5932AEB-0B6E-4F37-8B32-9BE4775AEE85
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/08/2018
ms.openlocfilehash: 7d402e6f665baa8db68d571945490a8d1ae18881
ms.sourcegitcommit: c1d85b2c62ad84c22bdee37874ad30128581bca6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/08/2019
ms.locfileid: "67649562"
---
# <a name="using-data-in-an-app"></a>Usar dados em um aplicativo

O **DataAccess_Adv** exemplo mostra um aplicativo funcional que permite a entrada do usuário e a funcionalidade de banco de dados CRUD (criar, ler, atualizar e excluir). O aplicativo consiste em duas telas: uma lista e um formulário de entrada de dados. Todo o código de acesso de dados é pode ser reutilizado no iOS e Android sem modificação.

Depois de adicionar alguns dados as telas do aplicativo esta aparência no Android:

![Lista de exemplo do Android](using-data-in-an-app-images/image11.png "lista de exemplo do Android")

![Detalhes de exemplo do Android](using-data-in-an-app-images/image12.png "detalhes de exemplo do Android")

O projeto do Android é mostrado abaixo &ndash; o código mostrado nesta seção está contido dentro de **Orm** diretório:

![Árvore de projeto do Android](using-data-in-an-app-images/image14.png "árvore de projeto do Android")

O código nativo de interface do usuário para as atividades no Android está fora do escopo deste documento. Consulte a [Android ListViews e adaptadores](~/android/user-interface/layouts/list-view/index.md) guia para obter mais informações sobre os controles de interface do usuário.

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

Android processa os dados como um `ListView`.

## <a name="create-and-update"></a>Criar e atualizar

Para simplificar o código do aplicativo, um único método de salvar é fornecido que faz uma inserção ou atualização, dependendo se o PrimaryKey tiver sido definido. Porque o `Id` propriedade é marcada com um `[PrimaryKey]` atributo que você não deve defini-lo em seu código. Esse método irá detectar se o valor tiver sido anterior salvo (Verificando a propriedade de chave primária) e inserir ou atualizar o objeto adequadamente:

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

Aplicativos do mundo real geralmente exigirá algumas validações (como campos obrigatórios, comprimentos mínimo ou outras regras de negócios). Implementam bons aplicativos de plataforma cruzada como grande parte da validação lógica como possível em código compartilhado, passando os erros de validação de volta até a interface do usuário para exibição de acordo com os recursos da plataforma.

## <a name="delete"></a>Excluir

Ao contrário do `Insert` e `Update` métodos, o `Delete<T>` método pode aceitar apenas o valor de chave primária em vez de uma completa `Stock` objeto. Neste exemplo uma `Stock` objeto é passado para o método, mas apenas a propriedade de Id é passada para o `Delete<T>` método.

```csharp
public int DeleteStock(Stock stock)
{
    lock (locker) {
        return Delete<Stock> (stock.Id);
    }
}
```

## <a name="using-a-pre-populated-sqlite-database-file"></a>Usando um arquivo de banco de dados SQLite previamente preenchido

Alguns aplicativos são fornecidos com um banco de dados já preenchido com dados. Você pode fazer isso facilmente no seu aplicativo móvel pelo envio de um arquivo de banco de dados SQLite existente com o seu aplicativo e copiá-lo para um diretório gravável para acessá-lo. Como o SQLite é um formato de arquivo padrão que é usado em muitas plataformas, existem várias ferramentas disponíveis para criar um arquivo de banco de dados SQLite:

-   **Extensão do Firefox Manager SQLite** &ndash; funciona no Mac e Windows e produz arquivos que são compatíveis com iOS e Android.

-   **Linha de comando** &ndash; consulte [www.sqlite.org/sqlite.html](http://www.sqlite.org/sqlite.html) .

Ao criar um arquivo de banco de dados de distribuição com seu aplicativo, tenha cuidado com a nomenclatura de tabelas e colunas para garantir que elas correspondam que espera seu código, especialmente se você estiver usando SQLite.NET que espera que os nomes para corresponder a suas propriedades e classes c# (ou o atributos personalizados associados).

Para garantir que algum código é executado antes de mais nada no seu aplicativo Android, você pode colocá-lo na primeira atividade para carregar ou criar um `Application` subclasse está carregado antes de todas as atividades. O código a seguir mostra uma `Application` subclasse que copia um arquivo de banco de dados existente **data.sqlite** fora dos **/recursos/Raw/** directory.

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

- [DataAccess Basic (exemplo)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic)
- [DataAccess avançadas (amostra)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced)
- [Receitas de dados do Android](https://github.com/xamarin/recipes/tree/master/Recipes/android/data)
- [Acesso a dados do xamarin. Forms](~/xamarin-forms/data-cloud/data/databases.md)
