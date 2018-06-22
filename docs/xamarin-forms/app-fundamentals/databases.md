---
title: Bancos de dados Local do xamarin. Forms
description: Xamarin. Forms oferece suporte a aplicativos controlados por banco de dados usando o mecanismo de banco de dados SQLite, o que torna possível carregar e salvar objetos no código compartilhado. Este artigo descreve como os aplicativos xamarin. Forms podem ler e gravar dados em um banco de dados local do SQLite usando SQLite.Net.
ms.prod: xamarin
ms.assetid: F687B24B-7DF0-4F8E-A21A-A9BB507480EB
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/21/2018
ms.openlocfilehash: feec4993a0719a083d713e084552b18aead8ee42
ms.sourcegitcommit: eac092f84b603958c761df305f015ff84e0fad44
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/21/2018
ms.locfileid: "36310134"
---
# <a name="xamarinforms-local-databases"></a>Bancos de dados Local do xamarin. Forms

_Xamarin. Forms oferece suporte a aplicativos controlados por banco de dados usando o mecanismo de banco de dados SQLite, o que torna possível carregar e salvar objetos no código compartilhado. Este artigo descreve como os aplicativos xamarin. Forms podem ler e gravar dados em um banco de dados local do SQLite usando SQLite.Net._

## <a name="overview"></a>Visão geral

Xamarin. Forms aplicativos podem usar o [SQLite.NET PCL NuGet](https://www.nuget.org/packages/sqlite-net-pcl/) pacote para incorporar as operações de banco de dados em código compartilhado referenciando o `SQLite` classes que acompanham o NuGet. Operações de banco de dados podem ser definidas no projeto da biblioteca .NET padrão da solução xamarin. Forms.

O que o acompanha [aplicativo de exemplo](https://github.com/xamarin/xamarin-forms-samples/tree/master/Todo) é um aplicativo simples da lista de tarefas. As capturas de tela a seguir mostram como o exemplo é exibido em cada plataforma:

[![Capturas de tela de exemplo de banco de dados xamarin. Forms](databases-images/todo-list-sml.png "TodoList primeiro Page Screenshots")](databases-images/todo-list.png#lightbox "TodoList primeiro Page Screenshots") [ ![ Capturas de tela de exemplo de banco de dados xamarin. Forms](databases-images/todo-list-sml.png "TodoList primeiro Page Screenshots")](databases-images/todo-list.png#lightbox "TodoList primeiro Page Screenshots")

<a name="Using_SQLite_with_PCL" />

## <a name="using-sqlite"></a>Usando SQLite

Para adicionar SQLite suporte a uma biblioteca xamarin. Forms .NET padrão, use a função de pesquisa do NuGet para localizar **sqlite-net-pcl** e instale o pacote mais recente:

![Adicionar o pacote do NuGet SQLite.NET PCL](databases-images/vs2017-sqlite-pcl-nuget.png "adicionar o pacote do NuGet SQLite.NET PCL")

Há um número de pacotes do NuGet com nomes semelhantes, o pacote correto tem estes atributos:

- **Criado por:** Frank A. Krueger
- **ID:** sqlite-net-pcl
- **Link do NuGet:** [sqlite-net-pcl](https://www.nuget.org/packages/sqlite-net-pcl/)

> [!NOTE]
> Apesar do nome do pacote, use o **sqlite-net-pcl** pacote NuGet mesmo em projetos .NET padrão.

Depois que a referência foi adicionada, adicionar uma propriedade para o `App` classe que retorna um caminho de arquivo local para armazenar o banco de dados:

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

O `TodoItemDatabase` construtor, que usa o caminho para o arquivo de banco de dados como um argumento, é mostrado abaixo:

```csharp
public TodoItemDatabase(string dbPath)
{
  database = new SQLiteAsyncConnection(dbPath);
  database.CreateTableAsync<TodoItem>().Wait();
}
```

A vantagem de expor o banco de dados como um singleton é a criação de uma conexão de banco de dados único que é mantida aberta enquanto o aplicativo é executado, portanto, evitando a despesa de abrir e fechar o arquivo de banco de dados cada vez que uma operação de banco de dados é executada.

O restante do `TodoItemDatabase` classe contém consultas SQLite executadas entre plataformas. Código de consulta de exemplo é mostrado abaixo (mais detalhes sobre a sintaxe podem ser encontrados na [SQLite.NET usando](~/cross-platform/app-fundamentals/index.md) artigo):

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
> A vantagem de usar a API SQLite.Net assíncrona é banco de dados operações são movidas para threads em segundo plano. Além disso, não é necessário gravar o código de tratamento porque a API cuida de simultaneidade adicional.

## <a name="summary"></a>Resumo

Xamarin. Forms oferece suporte a aplicativos controlados por banco de dados usando o mecanismo de banco de dados SQLite, o que torna possível carregar e salvar objetos no código compartilhado.

Este artigo se concentra em **acessando** um banco de dados SQLite usando xamarin. Forms. Para obter mais informações sobre como trabalhar com SQLite.Net em si, consulte o [SQLite.NET no Android](~/android/data-cloud/data-access/using-sqlite-orm.md) ou [SQLite.NET no iOS](~/ios/data-cloud/data/using-sqlite-orm.md) documentação.

## <a name="related-links"></a>Links relacionados

- [Exemplo de tarefas](https://developer.xamarin.com/samples/xamarin-forms/Todo/)
- [Amostras do Xamarin.Forms](https://developer.xamarin.com/samples/xamarin-forms/all/)
- [Pasta de trabalho do banco de dados](https://developer.xamarin.com/workbooks/xamarin-forms/application-fundamentals/database/database.workbook)
