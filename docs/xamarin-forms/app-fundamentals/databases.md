---
title: Bancos de dados Local do xamarin. Forms
description: Xamarin. Forms dá suporte a aplicativos controlados por banco de dados usando o mecanismo de banco de dados SQLite, que torna possível carregar e salvar objetos no código compartilhado. Este artigo descreve como os aplicativos xamarin. Forms podem ler e gravar dados em um banco de dados SQLite local usando SQLite.Net.
ms.prod: xamarin
ms.assetid: F687B24B-7DF0-4F8E-A21A-A9BB507480EB
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/21/2018
ms.openlocfilehash: 05c77c4c47841a897d0d1de5c3ba004db524ea2d
ms.sourcegitcommit: 79313604ed68829435cfdbb530db36794d50858f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/18/2018
ms.locfileid: "36310134"
---
# <a name="xamarinforms-local-databases"></a>Bancos de dados Local do xamarin. Forms

_Xamarin. Forms dá suporte a aplicativos controlados por banco de dados usando o mecanismo de banco de dados SQLite, que torna possível carregar e salvar objetos no código compartilhado. Este artigo descreve como os aplicativos xamarin. Forms podem ler e gravar dados em um banco de dados SQLite local usando SQLite.Net._

## <a name="overview"></a>Visão geral

Aplicativos xamarin. Forms podem usar o [SQLite.NET PCL NuGet](https://www.nuget.org/packages/sqlite-net-pcl/) pacote incorporar operações de banco de dados para o código compartilhado referenciando o `SQLite` classes que acompanham o NuGet. Operações de banco de dados podem ser definidas no projeto da biblioteca .NET Standard da solução xamarin. Forms.

O que o acompanha [aplicativo de exemplo](https://github.com/xamarin/xamarin-forms-samples/tree/master/Todo) é um simples aplicativo de lista de tarefas. As capturas de tela a seguir mostram como o exemplo é exibido em cada plataforma:

[![Capturas de tela de exemplo de banco de dados xamarin. Forms](databases-images/todo-list-sml.png "TodoList primeiro Page Screenshots")](databases-images/todo-list.png#lightbox "TodoList primeiro Page Screenshots") [ ![ Capturas de tela de exemplo de banco de dados xamarin. Forms](databases-images/todo-list-sml.png "TodoList primeiro Page Screenshots")](databases-images/todo-list.png#lightbox "TodoList primeiro Page Screenshots")

<a name="Using_SQLite_with_PCL" />

## <a name="using-sqlite"></a>Usando o SQLite

Para adicionar suporte do SQLite em uma biblioteca do xamarin. Forms .NET Standard, use a função de pesquisa do NuGet para localizar **sqlite-net-pcl** e instale o pacote mais recente:

![Adicione o pacote do NuGet SQLite.NET PCL](databases-images/vs2017-sqlite-pcl-nuget.png "Adicionar pacote do NuGet SQLite.NET PCL")

Há um número de pacotes do NuGet com nomes semelhantes, o pacote correto tem os seguintes atributos:

- **Criado por:** Frank A. Krueger
- **ID:** sqlite-net-pcl
- **Link do NuGet:** [sqlite-net-pcl](https://www.nuget.org/packages/sqlite-net-pcl/)

> [!NOTE]
> Apesar do nome do pacote, use o **sqlite-net-pcl** pacote do NuGet, mesmo em projetos do .NET Standard.

Depois que a referência tiver sido adicionada, adicione uma propriedade para o `App` classe que retorna um caminho de arquivo local para armazenar o banco de dados:

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

O `TodoItemDatabase` construtor, que leva o caminho para o arquivo de banco de dados como um argumento, é mostrado abaixo:

```csharp
public TodoItemDatabase(string dbPath)
{
  database = new SQLiteAsyncConnection(dbPath);
  database.CreateTableAsync<TodoItem>().Wait();
}
```

A vantagem de expor o banco de dados como um singleton é que uma conexão de banco de dados individual é criado que é mantida aberta enquanto o aplicativo é executado, portanto, evitando a despesa de abrir e fechar o arquivo de banco de dados cada vez que uma operação de banco de dados é executada.

O restante do `TodoItemDatabase` classe contém SQLite consultas que executam a plataforma cruzada. Código de consulta de exemplo é mostrado abaixo (mais detalhes sobre a sintaxe podem ser encontrados no [SQLite.NET usando com xamarin. IOS](~/ios/data-cloud/data/using-sqlite-orm.md).

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
> A vantagem de usar a API SQLite.Net assíncrona é esse banco de dados operações são movidas para threads de segundo plano. Além disso, não há nenhuma necessidade de escrever código de tratamento de porque a API cuida de simultaneidade adicionais.

## <a name="summary"></a>Resumo

Xamarin. Forms dá suporte a aplicativos controlados por banco de dados usando o mecanismo de banco de dados SQLite, que torna possível carregar e salvar objetos no código compartilhado.

Este artigo enfoca **acessando** um banco de dados SQLite usando xamarin. Forms. Para obter mais informações sobre como trabalhar com SQLite.Net em si, consulte o [SQLite.NET no Android](~/android/data-cloud/data-access/using-sqlite-orm.md) ou [SQLite.NET no iOS](~/ios/data-cloud/data/using-sqlite-orm.md) documentação.

## <a name="related-links"></a>Links relacionados

- [Exemplo de tarefas pendentes](https://developer.xamarin.com/samples/xamarin-forms/Todo/)
- [Amostras do Xamarin.Forms](https://developer.xamarin.com/samples/xamarin-forms/all/)

