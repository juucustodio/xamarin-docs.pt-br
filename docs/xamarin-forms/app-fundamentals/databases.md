---
title: Bancos de dados locais
description: Xamarin. Forms oferece suporte a aplicativos controlados por banco de dados usando o mecanismo de banco de dados SQLite, o que torna possível carregar e salvar objetos no código compartilhado. Este artigo descreve como os aplicativos xamarin. Forms podem ler e gravar dados em um banco de dados local do SQLite usando SQLite.Net.
ms.prod: xamarin
ms.assetid: F687B24B-7DF0-4F8E-A21A-A9BB507480EB
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/31/2018
ms.openlocfilehash: d97fc792e2eb14f7e432d377811d1318c99b9602
ms.sourcegitcommit: a4c2a63ba76b839cda99e4474e7ab46fe307cd39
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34689442"
---
# <a name="local-databases"></a>Bancos de dados locais

_Xamarin. Forms oferece suporte a aplicativos controlados por banco de dados usando o mecanismo de banco de dados SQLite, o que torna possível carregar e salvar objetos no código compartilhado. Este artigo descreve como os aplicativos xamarin. Forms podem ler e gravar dados em um banco de dados local do SQLite usando SQLite.Net._

## <a name="overview"></a>Visão geral

Xamarin. Forms aplicativos podem usar o [SQLite.NET PCL NuGet](https://www.nuget.org/packages/sqlite-net-pcl/) pacote para incorporar as operações de banco de dados em código compartilhado referenciando o `SQLite` classes que acompanham o NuGet. Operações de banco de dados podem ser definidas no projeto biblioteca .NET padrão da solução xamarin. Forms, com projetos de plataforma específica retornando um caminho para onde o banco de dados será armazenado.

O que o acompanha [aplicativo de exemplo](https://github.com/xamarin/xamarin-forms-samples/tree/master/Todo) é um aplicativo simples da lista de tarefas. As capturas de tela a seguir mostram como o exemplo é exibido em cada plataforma:

[![Capturas de tela de exemplo de banco de dados xamarin. Forms](databases-images/todo-list-sml.png "TodoList primeiro Page Screenshots")](databases-images/todo-list.png#lightbox "TodoList primeiro Page Screenshots") [ ![ Capturas de tela de exemplo de banco de dados xamarin. Forms](databases-images/todo-list-sml.png "TodoList primeiro Page Screenshots")](databases-images/todo-list.png#lightbox "TodoList primeiro Page Screenshots")

<a name="Using_SQLite_with_PCL" />

## <a name="using-sqlite"></a>Usando SQLite

Esta seção mostra como adicionar pacotes do SQLite.Net NuGet a uma solução xamarin. Forms, escrever os métodos para executar operações de banco de dados e usar o [ `DependencyService` ](~/xamarin-forms/app-fundamentals/dependency-service/index.md) para determinar um local para armazenar o banco de dados em cada plataforma.

<a name="XamarinForms_PCL_Project" />

### <a name="xamarinsforms-net-standard-or-pcl-project"></a>Projeto PCL ou Xamarins.Forms .NET padrão

Para adicionar SQLite suporte a um projeto xamarin. Forms, use a função de pesquisa do NuGet para localizar **sqlite-net-pcl** e instale o pacote:

![Adicionar o pacote do NuGet SQLite.NET PCL](databases-images/vs2017-sqlite-pcl-nuget.png "adicionar o pacote do NuGet SQLite.NET PCL")

Há um número de pacotes do NuGet com nomes semelhantes, o pacote correto tem estes atributos:

- **Criado por:** Frank A. Krueger
- **ID:** sqlite-net-pcl
- **Link do NuGet:** [sqlite-net-pcl](https://www.nuget.org/packages/sqlite-net-pcl/)

> [!TIP]
> Use o **sqlite-net-pcl** NuGet mesmo em projetos .NET padrão.

Depois que a referência foi adicionada, escreva uma interface para abstrair a funcionalidade específica de plataforma, que é para determinar o local do arquivo de banco de dados. A interface usada no exemplo define um método único:

```csharp
public interface IFileHelper
{
  string GetLocalFilePath(string filename);
}
```

Depois que a interface tiver sido definida, use o [ `DependencyService` ](~/xamarin-forms/app-fundamentals/dependency-service/index.md) para obter uma implementação e obter um caminho de arquivo local (Observe que esta interface não foi implementada ainda). O código a seguir obtém uma implementação `App.Database` propriedade:

```csharp
static TodoItemDatabase database;

public static TodoItemDatabase Database
{
  get
  {
    if (database == null)
    {
      database = new TodoItemDatabase(DependencyService.Get<IFileHelper>().GetLocalFilePath("TodoSQLite.db3"));
    }
    return database;
  }
}
```

O `TodoItemDatabase` construtor é mostrado abaixo:

```csharp
public TodoItemDatabase(string dbPath)
{
  database = new SQLiteAsyncConnection(dbPath);
  database.CreateTableAsync<TodoItem>().Wait();
}
```

Essa abordagem cria uma conexão de banco de dados único que é mantida aberta enquanto o aplicativo é executado, portanto, evitando a despesa de abrir e fechar o arquivo de banco de dados cada vez que uma operação de banco de dados é executada.

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

Todo o código de acesso a dados é gravado no projeto PCL para ser compartilhado entre todas as plataformas. Obter apenas um caminho de arquivo local para o banco de dados requer código específico da plataforma, conforme descrito nas seções a seguir.

<a name="PCL_iOS" />

### <a name="ios-project"></a>Projeto do iOS

Para configurar o aplicativo iOS, adicione o mesmo pacote de NuGet para o projeto iOS usando o *NuGet* janela:

![Adicionar o pacote do NuGet SQLite.NET PCL](databases-images/vsmac-sqlite-nuget.png "adicionar o pacote do NuGet SQLite.NET PCL")

É o único código necessário o `IFileHelper` implementação que determina o caminho do arquivo de dados. O código a seguir coloca o arquivo de banco de dados SQLite no **/bancos de dados biblioteca** pasta dentro de proteção do aplicativo. Consulte o [iOS trabalhando com o sistema de arquivos](~/ios/app-fundamentals/file-system.md) documentação para obter mais informações sobre os diretórios diferentes que estão disponíveis para armazenamento.

```csharp
[assembly: Dependency(typeof(FileHelper))]
namespace Todo.iOS
{
  public class FileHelper : IFileHelper
  {
    public string GetLocalFilePath(string filename)
    {
      string docFolder = Environment.GetFolderPath(Environment.SpecialFolder.Personal);
      string libFolder = Path.Combine(docFolder, "..", "Library", "Databases");

      if (!Directory.Exists(libFolder))
      {
        Directory.CreateDirectory(libFolder);
      }

      return Path.Combine(libFolder, filename);
    }
  }
}
```

Observe que o código inclui o `assembly:Dependency` atributo para que essa implementação é detectável pelo `DependencyService`.

<a name="PCL_Android" />

### <a name="android-project"></a>Projeto Android

Para configurar o aplicativo do Android, adicione o mesmo pacote de NuGet para o projeto Android usando o *NuGet* janela:

![](databases-images/vsmac-sqlite-nuget.png "Adicionar o pacote do NuGet SQLite.NET PCL")

Depois que tiver sido adicionada a esta referência, o único código necessário é o `IFileHelper` implementação que determina o caminho do arquivo de dados.

```csharp
[assembly: Dependency(typeof(FileHelper))]
namespace Todo.Droid
{
  public class FileHelper : IFileHelper
  {
    public string GetLocalFilePath(string filename)
    {
        string path = Environment.GetFolderPath(Environment.SpecialFolder.Personal);
        return Path.Combine(path, filename);
    }
  }
}
```

<a name="PCL_UWP" />

### <a name="windows-10-universal-windows-platform-uwp"></a>UWP (Plataforma Universal do Windows) do Windows 10

Para configurar o aplicativo de UWP, adicione o mesmo pacote de NuGet para o projeto UWP usando o *NuGet* janela:

![Adicionar o pacote do NuGet SQLite.NET PCL](databases-images/vs2017-sqlite-uwp-nuget.png "adicionar o pacote do NuGet SQLite.NET PCL")

Depois que a referência é adicionada, implementar a `IFileHelper` interface usando o específico da plataforma `Windows.Storage` API para determinar o caminho do arquivo de dados.

```csharp
using Windows.Storage;
...

[assembly: Dependency(typeof(FileHelper))]
namespace Todo.UWP
{
  public class FileHelper : IFileHelper
  {
    public string GetLocalFilePath(string filename)
    {
      return Path.Combine(ApplicationData.Current.LocalFolder.Path, filename);
    }
  }
}
```

## <a name="summary"></a>Resumo

Xamarin. Forms oferece suporte a aplicativos controlados por banco de dados usando o mecanismo de banco de dados SQLite, o que torna possível carregar e salvar objetos no código compartilhado.

Este artigo se concentra em **acessando** um banco de dados SQLite usando xamarin. Forms. Para obter mais informações sobre como trabalhar com SQLite.Net em si, consulte o [SQLite.NET no Android](~/android/data-cloud/data-access/using-sqlite-orm.md) ou [SQLite.NET no iOS](~/ios/data-cloud/data/using-sqlite-orm.md) documentação. Grande parte do código SQLite.Net é compartilhável em todas as plataformas; Configurando apenas o local do arquivo de banco de dados SQLite exige a funcionalidade específica de plataforma.

## <a name="related-links"></a>Links relacionados

- [Exemplo de tarefas](https://developer.xamarin.com/samples/xamarin-forms/Todo/)
- [Amostras do Xamarin.Forms](https://developer.xamarin.com/samples/xamarin-forms/all/)
- [Pasta de trabalho do banco de dados](https://developer.xamarin.com/workbooks/xamarin-forms/application-fundamentals/database/database.workbook)
