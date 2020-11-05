---
title: Xamarin.Forms Bancos de dados locais
description: Xamarin.Forms dá suporte a aplicativos controlados por banco de dados usando o mecanismo de banco de dados SQLite, que possibilita carregar e salvar objetos no código compartilhado. Este artigo descreve como Xamarin.Forms os aplicativos podem ler e gravar dados em um banco de dados SQLite local usando o SQLite.net.
ms.prod: xamarin
ms.assetid: F687B24B-7DF0-4F8E-A21A-A9BB507480EB
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 12/05/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 4331b29c54b5f7c59daf0a9e04cd398693e79201
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93374700"
---
# <a name="no-locxamarinforms-local-databases"></a>Xamarin.Forms Bancos de dados locais

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](/samples/xamarin/xamarin-forms-samples/todo)

O mecanismo de banco de dados SQLite permite que Xamarin.Forms os aplicativos carreguem e salvem objetos de dados em código compartilhado. O aplicativo de exemplo usa uma tabela de banco de dados SQLite para armazenar itens de tarefas. Este artigo descreve como usar o SQLite.Net em código compartilhado para armazenar e recuperar informações em um banco de dados local.

[![Capturas de tela do aplicativo ToDoList no iOS e Android](databases-images/todo-list-sml.png)](databases-images/todo-list.png#lightbox "Aplicativo ToDoList no iOS e Android")

Integre o SQLite.NET a aplicativos móveis seguindo estas etapas:

1. [Instale o pacote NuGet](#install-the-sqlite-nuget-package).
1. [Configurar constantes](#configure-app-constants).
1. [Crie uma classe de acesso ao banco de dados](#create-a-database-access-class).
1. [Acessar dados no Xamarin.Forms ](#access-data-in-xamarinforms).
1. [Configuração avançada](#advanced-configuration).

## <a name="install-the-sqlite-nuget-package"></a>Instalar o pacote NuGet do SQLite

Use o Gerenciador de pacotes NuGet para pesquisar o **SQLite-net-PCL** e adicione a versão mais recente ao projeto de código compartilhado.

Há diversos pacotes NuGet com nomes semelhantes. O pacote correto tem estes atributos:

- **ID:** sqlite-net-pcl
- **Autores:** SQLite-net
- **Proprietários:** praeclarum
- **Link do NuGet:** [sqlite-net-pcl](https://www.nuget.org/packages/sqlite-net-pcl/)

> [!NOTE]
> Apesar do nome do pacote, use o pacote NuGet **sqlite-net-pcl** , mesmo em projetos do .NET Standard.

## <a name="configure-app-constants"></a>Configurar constantes do aplicativo

O projeto de exemplo inclui um arquivo **Constants.cs** que fornece dados de configuração comuns:

```csharp
public static class Constants
{
    public const string DatabaseFilename = "TodoSQLite.db3";

    public const SQLite.SQLiteOpenFlags Flags =
        // open the database in read/write mode
        SQLite.SQLiteOpenFlags.ReadWrite |
        // create the database if it doesn't exist
        SQLite.SQLiteOpenFlags.Create |
        // enable multi-threaded database access
        SQLite.SQLiteOpenFlags.SharedCache;

    public static string DatabasePath
    {
        get
        {
            var basePath = Environment.GetFolderPath(Environment.SpecialFolder.LocalApplicationData);
            return Path.Combine(basePath, DatabaseFilename);
        }
    }
}
```

O arquivo Constants especifica `SQLiteOpenFlag` valores de enumeração padrão que são usados para inicializar a conexão de banco de dados. A `SQLiteOpenFlag` Enumeração dá suporte a esses valores:

- `Create`: A conexão criará automaticamente o arquivo de banco de dados, caso ele não exista.
- `FullMutex`: A conexão é aberta no modo de Threading serializado.
- `NoMutex`: A conexão é aberta no modo multi-threaded.
- `PrivateCache`: A conexão não participará do cache compartilhado, mesmo se estiver habilitada.
- `ReadWrite`: A conexão pode ler e gravar dados.
- `SharedCache`: A conexão participará do cache compartilhado, se estiver habilitada.
- `ProtectionComplete`: O arquivo está criptografado e inacessível enquanto o dispositivo está bloqueado.
- `ProtectionCompleteUnlessOpen`: O arquivo é criptografado até ser aberto, mas é acessível mesmo que o usuário bloqueie o dispositivo.
- `ProtectionCompleteUntilFirstUserAuthentication`: O arquivo é criptografado até que o usuário tenha inicializado e desbloqueado o dispositivo.
- `ProtectionNone`: O arquivo de banco de dados não está criptografado.

Talvez seja necessário especificar sinalizadores diferentes dependendo de como seu banco de dados será usado. Para obter mais informações sobre `SQLiteOpenFlags` o, consulte [abrindo uma nova conexão de banco de dados](https://www.sqlite.org/c3ref/open.html) no SQLite.org.

## <a name="create-a-database-access-class"></a>Criar uma classe de acesso ao banco de dados

Uma classe de wrapper de banco de dados abstrai a camada de acesso de dado do restante do aplicativo. Essa classe centraliza a lógica de consulta e simplifica o gerenciamento da inicialização do banco de dados, facilitando a refatoração ou a expansão das operações do dado à medida que o aplicativo cresce. O aplicativo todo define uma `TodoItemDatabase` classe para essa finalidade.

### <a name="lazy-initialization"></a>Inicialização lenta

O `TodoItemDatabase` usa a `Lazy` classe .net para atrasar a inicialização do banco de dados até que ele seja acessado pela primeira vez. O uso da inicialização lenta impede que o processo de carregamento do banco de dados atrase a inicialização do aplicativo. Para obter mais informações, [consulte &lt; &gt; classe T lenta](xref:System.Lazy`1).

```csharp
public class TodoItemDatabase
{
    static readonly Lazy<SQLiteAsyncConnection> lazyInitializer = new Lazy<SQLiteAsyncConnection>(() =>
    {
        return new SQLiteAsyncConnection(Constants.DatabasePath, Constants.Flags);
    });

    static SQLiteAsyncConnection Database => lazyInitializer.Value;
    static bool initialized = false;

    public TodoItemDatabase()
    {
        InitializeAsync().SafeFireAndForget(false);
    }

    async Task InitializeAsync()
    {
        if (!initialized)
        {
            if (!Database.TableMappings.Any(m => m.MappedType.Name == typeof(TodoItem).Name))
            {
                await Database.CreateTablesAsync(CreateFlags.None, typeof(TodoItem)).ConfigureAwait(false);
            }
            initialized = true;
        }
    }

    //...
}
```

A conexão de banco de dados é um campo estático que garante que uma única conexão de banco de dados seja usada para a vida útil do aplicativo. Usar uma conexão persistente e estática oferece melhor desempenho do que abrir e fechar conexões várias vezes durante uma única sessão de aplicativo.

O `InitializeAsync` método é responsável por verificar se já existe uma tabela para armazenar `TodoItem` objetos. Esse método criará automaticamente a tabela se ela não existir.

### <a name="the-safefireandforget-extension-method"></a>O método de extensão SafeFireAndForget

Quando a `TodoItemDatabase` classe é instanciada, ela deve inicializar a conexão de banco de dados, que é um processo assíncrono. No entanto:

- Construtores de classe não podem ser assíncronos.
- Um método assíncrono que não é esperado não gerará exceções.
- O uso do `Wait` método bloqueia o thread _e_ assimila as exceções.

Para iniciar a inicialização assíncrona, evite bloquear a execução e ter a oportunidade de capturar exceções, o aplicativo de exemplo usa um método de extensão chamado `SafeFireAndForget` . O `SafeFireAndForget` método de extensão fornece funcionalidade adicional para a `Task` classe:

```csharp
public static class TaskExtensions
{
    // NOTE: Async void is intentional here. This provides a way
    // to call an async method from the constructor while
    // communicating intent to fire and forget, and allow
    // handling of exceptions
    public static async void SafeFireAndForget(this Task task,
        bool returnToCallingContext,
        Action<Exception> onException = null)
    {
        try
        {
            await task.ConfigureAwait(returnToCallingContext);
        }

        // if the provided action is not null, catch and
        // pass the thrown exception
        catch (Exception ex) when (onException != null)
        {
            onException(ex);
        }
    }
}
```

O `SafeFireAndForget` método aguarda a execução assíncrona do objeto fornecido `Task` e permite que você anexe um `Action` que será chamado se uma exceção for gerada.

Para obter mais informações, consulte [padrão assíncrono baseado em tarefa (toque)](/dotnet/standard/asynchronous-programming-patterns/task-based-asynchronous-pattern-tap).

### <a name="data-manipulation-methods"></a>Métodos de manipulação de dados

A `TodoItemDatabase` classe inclui métodos para os quatro tipos de manipulação de dados: criar, ler, editar e excluir. A biblioteca SQLite.NET fornece um ORM (mapa relacional de objeto) simples que permite armazenar e recuperar objetos sem escrever instruções SQL.

```csharp
public class TodoItemDatabase {

    // ...

    public Task<List<TodoItem>> GetItemsAsync()
    {
        return Database.Table<TodoItem>().ToListAsync();
    }

    public Task<List<TodoItem>> GetItemsNotDoneAsync()
    {
        // SQL queries are also possible
        return Database.QueryAsync<TodoItem>("SELECT * FROM [TodoItem] WHERE [Done] = 0");
    }

    public Task<TodoItem> GetItemAsync(int id)
    {
        return Database.Table<TodoItem>().Where(i => i.ID == id).FirstOrDefaultAsync();
    }

    public Task<int> SaveItemAsync(TodoItem item)
    {
        if (item.ID != 0)
        {
            return Database.UpdateAsync(item);
        }
        else
        {
            return Database.InsertAsync(item);
        }
    }

    public Task<int> DeleteItemAsync(TodoItem item)
    {
        return Database.DeleteAsync(item);
    }
}
```

## <a name="access-data-in-no-locxamarinforms"></a>Acessar dados em Xamarin.Forms

A Xamarin.Forms `App` classe expõe uma instância da `TodoItemDatabase` classe:

```csharp
static TodoItemDatabase database;
public static TodoItemDatabase Database
{
    get
    {
        if (database == null)
        {
            database = new TodoItemDatabase();
        }
        return database;
    }
}
```

Essa propriedade permite que Xamarin.Forms os componentes chamem métodos de recuperação e de manipulação de dados na `Database` instância do em resposta à interação do usuário. Por exemplo:

```csharp
var saveButton = new Button { Text = "Save" };
saveButton.Clicked += async (sender, e) =>
{
    var todoItem = (TodoItem)BindingContext;
    await App.Database.SaveItemAsync(todoItem);
    await Navigation.PopAsync();
};
```

## <a name="advanced-configuration"></a>Configuração avançada

O SQLite fornece uma API robusta com mais recursos do que o que é abordado neste artigo e no aplicativo de exemplo. As seções a seguir abrangem recursos que são importantes para escalabilidade.

Para obter mais informações, consulte a [documentação do SQLite](https://www.sqlite.org/docs.html) em SQLite.org.

### <a name="write-ahead-logging"></a>Log de Write-Ahead

Por padrão, o SQLite usa um diário de reversão tradicional. Uma cópia do conteúdo do banco de dados inalterado é gravada em um arquivo de reversão separado e, em seguida, as alterações são gravadas diretamente no arquivo de banco de dados. A confirmação ocorre quando o diário de reversão é excluído.

O log de Write-Ahead (WAL) grava as alterações em um arquivo WAL separado primeiro. No modo WAL, uma confirmação é um registro especial, acrescentado ao arquivo WAL, que permite que várias transações ocorram em um único arquivo WAL. Um arquivo WAL é mesclado de volta para o arquivo de banco de dados em uma operação especial chamada _ponto de verificação_.

O WAL pode ser mais rápido para bancos de dados locais, pois leitores e gravadores não bloqueiam um ao outro, permitindo que as operações de leitura e gravação sejam simultâneas. No entanto, o modo WAL não permite alterações no _tamanho da página_ , adiciona associações de arquivo adicionais ao banco de dados e adiciona a operação de _ponto de verificação_ extra.

Para habilitar WAL em SQLite.NET, chame o `EnableWriteAheadLoggingAsync` método na `SQLiteAsyncConnection` instância:

```csharp
await Database.EnableWriteAheadLoggingAsync();
```

Para obter mais informações, consulte [SQLite Write-Ahead Logging](https://www.sqlite.org/wal.html) em SQLite.org.

### <a name="copying-a-database"></a>Copiando um banco de dados

Há vários casos em que pode ser necessário copiar um banco de dados SQLite:

- Um banco de dados foi enviado com seu aplicativo, mas deve ser copiado ou movido para o armazenamento gravável no dispositivo móvel.
- Você precisa fazer um backup ou cópia do banco de dados.
- Você precisará fazer a versão, mover ou renomear o arquivo de banco de dados.

Em geral, mover, renomear ou copiar um arquivo de banco de dados é o mesmo processo que qualquer outro tipo de arquivo com algumas considerações adicionais:

- Todas as conexões de banco de dados devem ser fechadas antes de tentar mover o arquivo de banco de dados.
- Se você usar o [registro em log write-ahead](#write-ahead-logging), o SQLite criará um arquivo de acesso à memória compartilhada (. SHM) e um arquivo (log de gravação antecipada) (. Wal). Certifique-se de aplicar quaisquer alterações a esses arquivos também.

Para obter mais informações, consulte [manipulação de Xamarin.Forms arquivos em ](~/xamarin-forms/data-cloud/data/files.md).

## <a name="related-links"></a>Links Relacionados

- [Aplicativo de exemplo todo](/samples/xamarin/xamarin-forms-samples/todo)
- [Pacote NuGet do SQLite.NET](https://www.nuget.org/packages/sqlite-net-pcl/)
- [Documentação do SQLite](https://www.sqlite.org/docs.html)
- [Usando o SQLite com Android](~/android/data-cloud/data-access/using-sqlite-orm.md)
- [Usando o SQLite com iOS](~/ios/data-cloud/data/using-sqlite-orm.md)
- [Padrão assíncrono baseado em tarefa (toque)](/dotnet/standard/asynchronous-programming-patterns/task-based-asynchronous-pattern-tap)
- [&lt;Classe T &gt; lenta](xref:System.Lazy`1)