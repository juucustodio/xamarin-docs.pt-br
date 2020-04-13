---
title: Bancos de dados locais do Xamarin.Forms
description: O Xamarin.Forms dá suporte a aplicativos controlados por banco de dados usando o mecanismo de banco de dados SQLite, que torna possível carregar e salvar objetos em código compartilhado. Este artigo descreve como os aplicativos Xamarin.Forms podem ler e gravar dados em um banco de dados SQLite local usando o SQLite.Net.
ms.prod: xamarin
ms.assetid: F687B24B-7DF0-4F8E-A21A-A9BB507480EB
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 12/05/2019
ms.openlocfilehash: 2369afc693940d83971a43877da363e2c66b31b2
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/09/2020
ms.locfileid: "80992398"
---
# <a name="xamarinforms-local-databases"></a>Bancos de dados locais do Xamarin.Forms

[![Baixar](~/media/shared/download.png) amostra Baixar a amostra](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/todo)

O mecanismo de banco de dados SQLite permite que os aplicativos Xamarin.Forms carreguem e salvem objetos de dados em código compartilhado. O aplicativo de exemplo usa uma tabela de banco de dados SQLite para armazenar todos os itens. Este artigo descreve como usar SQLite.Net em código compartilhado para armazenar e recuperar informações em um banco de dados local.

[![Capturas de tela do aplicativo Todolist no iOS e Android](databases-images/todo-list-sml.png)](databases-images/todo-list.png#lightbox "Aplicativo Todolist no iOS e Android")

Integre SQLite.NET em aplicativos móveis seguindo estas etapas:

1. [Instale o pacote NuGet](#install-the-sqlite-nuget-package).
1. [Configurar constantes](#configure-app-constants).
1. [Crie uma classe de acesso ao banco de dados](#create-a-database-access-class).
1. [Acessar dados em Xamarin.Forms](#access-data-in-xamarinforms).
1. [Configuração avançada](#advanced-configuration).

## <a name="install-the-sqlite-nuget-package"></a>Instale o pacote SQLite NuGet

Use o gerenciador de pacotes NuGet para procurar **sqlite-net-pcl** e adicione a versão mais recente ao projeto de código compartilhado.

Há diversos pacotes NuGet com nomes semelhantes. O pacote correto tem estes atributos:

- **Criado por:** Frank A. Krueger
- **ID:** sqlite-net-pcl
- **Link do NuGet:** [sqlite-net-pcl](https://www.nuget.org/packages/sqlite-net-pcl/)

> [!NOTE]
> Apesar do nome do pacote, use o pacote NuGet **sqlite-net-pcl**, mesmo em projetos do .NET Standard.

## <a name="configure-app-constants"></a>Configurar constantes de aplicativos

O projeto de amostra inclui um arquivo **Constants.cs** que fornece dados de configuração comuns:

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

O arquivo constants `SQLiteOpenFlag` especifica valores de enum padrão que são usados para inicializar a conexão do banco de dados. O `SQLiteOpenFlag` enum suporta esses valores:

- `Create`: A conexão criará automaticamente o arquivo do banco de dados se ele não existir.
- `FullMutex`: A conexão é aberta no modo de rosca serializada.
- `NoMutex`: A conexão é aberta no modo multi-threading.
- `PrivateCache`: A conexão não participará do cache compartilhado, mesmo que esteja habilitado.
- `ReadWrite`: A conexão pode ler e gravar dados.
- `SharedCache`: A conexão participará do cache compartilhado, se estiver habilitado.
- `ProtectionComplete`: O arquivo é criptografado e inacessível enquanto o dispositivo está bloqueado.
- `ProtectionCompleteUnlessOpen`: O arquivo é criptografado até ser aberto, mas é então acessível mesmo que o usuário bloqueia o dispositivo.
- `ProtectionCompleteUntilFirstUserAuthentication`: O arquivo é criptografado até depois que o usuário inicializar e desbloquear o dispositivo.
- `ProtectionNone`: O arquivo do banco de dados não está criptografado.

Você pode precisar especificar diferentes bandeiras dependendo de como seu banco de dados será usado. Para obter `SQLiteOpenFlags`mais informações sobre, consulte [Abrir uma nova conexão de banco de dados](https://www.sqlite.org/c3ref/open.html) em sqlite.org.

## <a name="create-a-database-access-class"></a>Crie uma classe de acesso ao banco de dados

Uma classe de invólucro de banco de dados abstrai a camada de acesso aos dados do resto do aplicativo. Essa classe centraliza a lógica de consulta e simplifica o gerenciamento da inicialização do banco de dados, facilitando a refatoração ou expansão das operações de dados à medida que o aplicativo cresce. O aplicativo Todo `TodoItemDatabase` define uma classe para este fim.

### <a name="lazy-initialization"></a>Inicialização lenta

O `TodoItemDatabase` uso da `Lazy` classe .NET para atrasar a inicialização do banco de dados até que ele seja acessado pela primeira vez. O uso da inicialização preguiçosa impede que o processo de carregamento do banco de dados adia o lançamento do aplicativo. Para obter mais informações, consulte [Lazy&lt;T&gt; Class](xref:System.Lazy`1).

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
                initialized = true;
            }
        }
    }

    //...
}
```

A conexão do banco de dados é um campo estático que garante que uma única conexão de banco de dados seja usada para a vida útil do aplicativo. Usar uma conexão estática persistente oferece melhor desempenho do que abrir e fechar conexões várias vezes durante uma única sessão de aplicativo.

O `InitializeAsync` método é responsável por verificar se já `TodoItem` existe uma tabela para armazenar objetos. Este método cria automaticamente a tabela se ela não existir.

### <a name="the-safefireandforget-extension-method"></a>O método de extensão SafeFireAndForget

Quando `TodoItemDatabase` a classe é instanciada, ela deve inicializar a conexão do banco de dados, que é um processo assíncrono. No entanto:

- Os construtores de classe não podem ser assíncronos.
- Um método assíncrono que não é aguardado não lançará exceções.
- O `Wait` uso do método bloqueia o fio _e_ engole exceções.

Para iniciar a inicialização assíncrona, evitar o bloqueio da execução e ter a `SafeFireAndForget`oportunidade de capturar exceções, o aplicativo de amostra usa um método de extensão chamado . O `SafeFireAndForget` método de extensão `Task` fornece funcionalidade adicional à classe:

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

O `SafeFireAndForget` método aguarda a execução assíncrona do objeto fornecido, `Task` e permite que você anexe um `Action` que é chamado se uma exceção for lançada.

Para obter mais informações, consulte [o padrão assíncrono baseado em tarefas (TAP)](https://docs.microsoft.com/dotnet/standard/asynchronous-programming-patterns/task-based-asynchronous-pattern-tap).

### <a name="data-manipulation-methods"></a>Métodos de manipulação de dados

A `TodoItemDatabase` classe inclui métodos para os quatro tipos de manipulação de dados: criar, ler, editar e excluir. A biblioteca SQLite.NET fornece um simples Mapa Relacional de Objeto (ORM) que permite armazenar e recuperar objetos sem escrever instruções SQL.

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

## <a name="access-data-in-xamarinforms"></a>Dados de acesso em Xamarin.Forms

A classe Xamarin.Forms `App` expõe uma `TodoItemDatabase` instância da classe:

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

Essa propriedade permite que os componentes do Xamarin.Forms `Database` chamem métodos de recuperação e manipulação de dados na instância em resposta à interação do usuário. Por exemplo:

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

O SQLite fornece uma API robusta com mais recursos do que são cobertos neste artigo e no aplicativo de amostra. As seções a seguir cobrem características importantes para a escalabilidade.

Para obter mais informações, consulte [a Documentação SQLite](https://www.sqlite.org/docs.html) sobre sqlite.org.

### <a name="write-ahead-logging"></a>Registro de registro antecipada

Por padrão, o SQLite usa um diário de reversão tradicional. Uma cópia do conteúdo do banco de dados inalterado é escrita em um arquivo de reversão separado, em seguida, as alterações são escritas diretamente no arquivo do banco de dados. O COMMIT ocorre quando o diário de reversão é excluído.

Write-Ahead Logging (WAL) grava alterações em um arquivo WAL separado primeiro. No modo WAL, um COMMIT é um registro especial, anexado ao arquivo WAL, que permite que várias transações ocorram em um único arquivo WAL. Um arquivo WAL é mesclado de volta ao arquivo de banco de dados em uma operação especial chamada _checkpoint_.

O WAL pode ser mais rápido para bancos de dados locais porque leitores e escritores não bloqueiam uns aos outros, permitindo que as operações de leitura e gravação sejam simultâneas. No entanto, o modo WAL não permite alterações no tamanho da _página,_ adiciona associações adicionais de arquivos ao banco de dados e adiciona a operação extra _de verificação._

Para habilitar o WAL `EnableWriteAheadLoggingAsync` em `SQLiteAsyncConnection` SQLite.NET, chame o método na instância:

```csharp
await Database.EnableWriteAheadLoggingAsync();
```

Para obter mais informações, consulte [SQLite Write-Ahead Logging](https://www.sqlite.org/wal.html) on sqlite.org.

### <a name="copying-a-database"></a>Copiando um banco de dados

Existem vários casos em que pode ser necessário copiar um banco de dados SQLite:

- Um banco de dados foi enviado com seu aplicativo, mas deve ser copiado ou movido para armazenamento gravável no dispositivo móvel.
- Você precisa fazer um backup ou cópia do banco de dados.
- Você precisa fazer a versão, mover ou renomear o arquivo do banco de dados.

Em geral, mover, renomear ou copiar um arquivo de banco de dados é o mesmo processo que qualquer outro tipo de arquivo com algumas considerações adicionais:

- Todas as conexões do banco de dados devem ser fechadas antes de tentar mover o arquivo do banco de dados.
- Se você usar [o Write-Ahead Logging](#write-ahead-logging), o SQLite criará um arquivo de acesso à memória compartilhada (.shm) e um arquivo (Write Ahead Log) (.wal). Certifique-se de que você aplica quaisquer alterações nesses arquivos também.

Para obter mais informações, consulte [O tratamento de arquivos em Xamarin.Forms](~/xamarin-forms/data-cloud/data/files.md).

## <a name="related-links"></a>Links relacionados

- [Aplicação de amostra inteira](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/todo)
- [pacote SQLite.NET NuGet](https://www.nuget.org/packages/sqlite-net-pcl/)
- [Documentação SQLite](https://www.sqlite.org/docs.html)
- [Usando SQLite com Android](~/android/data-cloud/data-access/using-sqlite-orm.md)
- [Usando SQLite com iOS](~/ios/data-cloud/data/using-sqlite-orm.md)
- [Padrão assíncrono baseado em tarefas (TAP)](https://docs.microsoft.com/dotnet/standard/asynchronous-programming-patterns/task-based-asynchronous-pattern-tap)
- [Classe&lt;&gt; T Preguiçosa](xref:System.Lazy`1)
