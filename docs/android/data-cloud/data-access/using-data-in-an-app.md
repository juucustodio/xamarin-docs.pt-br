---
title: Usando dados de um aplicativo
ms.prod: xamarin
ms.assetid: D5932AEB-0B6E-4F37-8B32-9BE4775AEE85
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/08/2018
ms.openlocfilehash: dd2a0a58a3a8c10671609aa385629d4754ca5378
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="using-data-in-an-app"></a>Usando dados de um aplicativo

O **DataAccess_Adv** exemplo mostra um aplicativo de trabalho que permite que a entrada do usuário e a funcionalidade de banco de dados CRUD (criar, ler, atualizar e excluir). O aplicativo consiste em duas telas: uma lista e um formulário de entrada de dados. Todo o código de acesso de dados é reutilizável no iOS e Android sem modificação.

Depois de adicionar alguns dados as telas de aplicativo assim no Android:

![Lista de exemplos Android](using-data-in-an-app-images/image11.png "lista de exemplo do Android")

![Detalhes da amostra Android](using-data-in-an-app-images/image12.png "detalhes de exemplo do Android")

O projeto Android é mostrado abaixo &ndash; o código mostrado nesta seção está contido dentro de **Orm** diretório:

![Árvore de projeto Android](using-data-in-an-app-images/image14.png "árvore projeto Android")

O código nativo de interface do usuário para as atividades no Android está fora do escopo deste documento. Consulte o [ListViews Android e adaptadores](~/android/user-interface/layouts/list-view/index.md) guia para obter mais informações sobre os controles de interface do usuário.

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

Android processa os dados como um `ListView`.

## <a name="create-and-update"></a>Criar e atualizar

Para simplificar o código do aplicativo, um único método de salvamento é fornecida, que faz uma inserção ou atualização dependendo se o PrimaryKey foi definida. Porque o `Id` propriedade é marcada com um `[PrimaryKey]` atributo que você não deve configurá-lo em seu código. Esse método irá detectar se o valor tiver sido anterior salvo (Verificando a propriedade de chave primária) e inserir ou atualizar o objeto adequadamente:

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

Aplicativos do mundo real geralmente exige alguma validação (como campos obrigatórios, tamanhos mínimo ou outras regras de negócios). Aplicativos de plataforma cruzada bom implementam tanta da validação lógica como possíveis no código compartilhado, passando os erros de validação de volta até a interface do usuário para exibição de acordo com os recursos da plataforma.

## <a name="delete"></a>Excluir

Ao contrário de `Insert` e `Update` métodos, o `Delete<T>` método pode aceitar apenas o valor de chave primária em vez de um conjunto completo `Stock` objeto. Neste exemplo um `Stock` objeto é passado para o método, mas apenas a propriedade Id é passada para o `Delete<T>` método.

```csharp
public int DeleteStock(Stock stock)
{
    lock (locker) {
        return Delete<Stock> (stock.Id);
    }
}
```

## <a name="using-a-pre-populated-sqlite-database-file"></a>Usando um arquivo de banco de dados SQLite preenchido previamente

Alguns aplicativos são fornecidos com um banco de dados já preenchido com dados. Você pode fazer isso facilmente em seu aplicativo móvel pelo envio de um arquivo de banco de dados SQLite existente com o seu aplicativo e copiá-la para um diretório gravável para acessá-lo. Como SQLite é um formato de arquivo padrão que é usado em muitas plataformas, há várias ferramentas disponíveis para criar um arquivo de banco de dados SQLite:

-   **Extensão do Gerenciador do SQLite Firefox** &ndash; funciona no Mac e Windows e produz arquivos que são compatíveis com o iOS e Android.

-   **Linha de comando** &ndash; consulte [www.sqlite.org/sqlite.html](http://www.sqlite.org/sqlite.html) .

Ao criar um arquivo de banco de dados de distribuição com seu aplicativo, tome cuidado com os nomes de tabelas e colunas para garantir que elas correspondam o que espera que seu código, especialmente se você estiver usando SQLite.NET que esperam que os nomes para coincidir com o c# classes e propriedades (ou o atributos personalizados associados).

Para garantir que algum código seja executado antes de mais nada no seu aplicativo do Android, você pode colocá-lo na primeira atividade para carregar ou criar um `Application` subclasse está carregado antes de todas as atividades. O código a seguir mostra um `Application` subclasse que copia um arquivo de banco de dados existente **data.sqlite** do **/recursos/Raw/** directory.

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
- [DataAccess avançados (amostra)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced)
- [Dados do Android receitas](https://developer.xamarin.com/recipes/android/data/)
- [Acesso a dados xamarin. Forms](~/xamarin-forms/app-fundamentals/databases.md)
