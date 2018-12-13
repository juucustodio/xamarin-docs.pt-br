---
title: Bancos de dados locais do Xamarin.Forms
description: O Xamarin.Forms dá suporte a aplicativos controlados por banco de dados usando o mecanismo de banco de dados SQLite, que torna possível carregar e salvar objetos em código compartilhado. Este artigo descreve como os aplicativos Xamarin.Forms podem ler e gravar dados em um banco de dados SQLite local usando o SQLite.Net.
ms.prod: xamarin
ms.assetid: F687B24B-7DF0-4F8E-A21A-A9BB507480EB
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/21/2018
ms.openlocfilehash: 05c77c4c47841a897d0d1de5c3ba004db524ea2d
ms.sourcegitcommit: 79313604ed68829435cfdbb530db36794d50858f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/18/2018
ms.locfileid: "36310134"
---
# <a name="xamarinforms-local-databases"></a>Bancos de dados locais do Xamarin.Forms

_O Xamarin.Forms dá suporte a aplicativos controlados por banco de dados usando o mecanismo de banco de dados SQLite, que torna possível carregar e salvar objetos em código compartilhado. Este artigo descreve como os aplicativos Xamarin.Forms podem ler e gravar dados em um banco de dados SQLite local usando o SQLite.Net._

## <a name="overview"></a>Visão geral

Os aplicativos Xamarin.Forms podem usar o pacote [NuGet SQLite.NET PCL](https://www.nuget.org/packages/sqlite-net-pcl/) para incorporar operações de banco de dados no código compartilhado referenciando as classes `SQLite` que acompanham o NuGet. As operações de banco de dados podem ser definidas no projeto de biblioteca do .NET Standard da solução Xamarin.Forms.

O [aplicativo de exemplo](https://github.com/xamarin/xamarin-forms-samples/tree/master/Todo) que acompanha é um aplicativo simples de lista de tarefas. As capturas de tela a seguir mostram como o exemplo é exibido em cada plataforma:

[![Capturas de tela de exemplo do banco de dados Xamarin.Forms](databases-images/todo-list-sml.png "Capturas de tela da primeira página TodoList")](databases-images/todo-list.png#lightbox "Capturas de tela da primeira página TodoList")[![Capturas de tela de exemplo do banco de dados Xamarin.Forms](databases-images/todo-list-sml.png "Capturas de tela de exemplo do banco de dados Xamarin.Forms")](databases-images/todo-list.png#lightbox "Capturas de tela de exemplo do banco de dados Xamarin.Forms")

<a name="Using_SQLite_with_PCL" />

## <a name="using-sqlite"></a>Usando o SQLite

Para adicionar o suporte do SQLite em uma biblioteca do .NET Standard Xamarin.Forms, use a função de pesquisa do NuGet para localizar **sqlite-net-pcl** e instalar o pacote mais recente:

![Adicione o pacote NuGet SQLite.NET PCL](databases-images/vs2017-sqlite-pcl-nuget.png "Adicione o pacote NuGet SQLite.NET PCL")

Há inúmeros pacotes NuGet com nomes semelhantes. O pacote correto tem estes atributos:

- **Criado por:** Frank A. Krueger
- **Id:** sqlite-net-pcl
- **Link do NuGet:** [sqlite-net-pcl](https://www.nuget.org/packages/sqlite-net-pcl/)

> [!NOTE]
> Apesar do nome do pacote, use o pacote NuGet **sqlite-net-pcl**, mesmo em projetos do .NET Standard.

Depois que a referência tiver sido adicionada, adicione uma propriedade à classe `App` que retorna um caminho de arquivo local para armazenar o banco de dados:

```csharp
static TodoItemDatabase database;

public static TodoItemDatabase Database
{
  get
  {
    if (database == null)
    {
      database = new TodoItemDatabase(
        Path.Combine(Environment.GetFolderPath(Environment.SpecialFolder.LocalApplicationData), "TodoSQLite.db3"));
    }
    return database;
  }
}
```

O construtor `TodoItemDatabase`, que usa o caminho para o arquivo de banco de dados como um argumento, é mostrado abaixo:

```csharp
public TodoItemDatabase(string dbPath)
{
  database = new SQLiteAsyncConnection(dbPath);
  database.CreateTableAsync<TodoItem>().Wait();
}
```

A vantagem de expor o banco de dados como um singleton é que uma conexão de banco de dados individual criada é mantida aberta enquanto o aplicativo é executado, evitando, portanto, o trabalho de abrir e fechar o arquivo de banco de dados cada vez que uma operação de banco de dados é realizada.

O restante da classe `TodoItemDatabase` contém consultas SQLite em execução multiplataforma. O código de consulta de exemplo é mostrado abaixo (mais detalhes sobre a sintaxe podem ser encontrados em [Usando SQLite.NET com o Xamarin.iOS](~/ios/data-cloud/data/using-sqlite-orm.md).

```csharp
public Task<List<TodoItem>> GetItemsAsync()
{
  return database.Table<TodoItem>().ToListAsync();
}

public Task<List<TodoItem>> GetItemsNotDoneAsync()
{
  return database.QueryAsync<TodoItem>("SELECT * FROM [TodoItem] WHERE [Done] = 0");
}

public Task<TodoItem> GetItemAsync(int id)
{
  return database.Table<TodoItem>().Where(i => i.ID == id).FirstOrDefaultAsync();
}

public Task<int> SaveItemAsync(TodoItem item)
{
  if (item.ID != 0)
  {
    return database.UpdateAsync(item);
  }
  else {
    return database.InsertAsync(item);
  }
}

public Task<int> DeleteItemAsync(TodoItem item)
{
  return database.DeleteAsync(item);
}
```

> [!NOTE]
> A vantagem de usar a API do SQLite.Net assíncrona é que as operações de banco de dados são movidas para ameaças em segundo plano. Além disso, não há nenhuma necessidade de escrever código de tratamento de simultaneidade adicional, porque a API faz isso.

## <a name="summary"></a>Resumo

O Xamarin.Forms dá suporte a aplicativos controlados por banco de dados usando o mecanismo de banco de dados SQLite, que torna possível carregar e salvar objetos em código compartilhado.

O foco deste artigo foi o **acesso** a um banco de dados SQLite que usa o Xamarin.Forms. Para saber mais sobre trabalhar com o próprio SQLite, confira a documentação [SQLite.NET no Android](~/android/data-cloud/data-access/using-sqlite-orm.md) ou [SQLite.NET no iOS](~/ios/data-cloud/data/using-sqlite-orm.md).

## <a name="related-links"></a>Links relacionados

- [Exemplo de tarefas pendentes](https://developer.xamarin.com/samples/xamarin-forms/Todo/)
- [Amostras do Xamarin.Forms](https://developer.xamarin.com/samples/xamarin-forms/all/)

