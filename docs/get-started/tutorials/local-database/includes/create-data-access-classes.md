---
ms.openlocfilehash: b11a5972c2aabace8a6991a82f5719f34450297d
ms.sourcegitcommit: 3f0e4f10e5def19122588bb05f26ab2baa9df6eb
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/23/2020
ms.locfileid: "67841496"
---
Neste exercício, você adicionará classes de acesso a dados ao projeto **LocalDatabaseTutorial**, que será usado para persistir dados sobre pessoas no banco de dados.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. No **Gerenciador de Soluções**, no projeto **LocalDatabaseTutorial**, adicione uma nova classe chamada `Person` ao projeto. Em seguida, em **Person.cs**, remova todo o código do modelo e substitua-o pelo código a seguir:

    ```csharp
    using SQLite;

    namespace LocalDatabaseTutorial
    {
        public class Person
        {
            [PrimaryKey, AutoIncrement]
            public int ID { get; set; }
            public string Name { get; set; }
            public int Age { get; set; }
        }
    }
    ```

    Esse código define uma classe `Person` que armazenará dados sobre cada pessoa no aplicativo. A propriedade `ID` é marcada com os atributos `PrimaryKey` e `AutoIncrement` para garantir que cada instância de `Person` no banco de dados terá uma ID exclusiva fornecida pelo SQLite.NET.

1. No **Gerenciador de Soluções**, no projeto **LocalDatabaseTutorial**, adicione uma nova classe chamada `Database` ao projeto. Em seguida, em **Database.cs**, remova todo o código do modelo e substitua-o pelo código a seguir:

    ```csharp
    using System.Collections.Generic;
    using System.Threading.Tasks;
    using SQLite;

    namespace LocalDatabaseTutorial
    {
        public class Database
        {
            readonly SQLiteAsyncConnection _database;

            public Database(string dbPath)
            {
                _database = new SQLiteAsyncConnection(dbPath);
                _database.CreateTableAsync<Person>().Wait();
            }

            public Task<List<Person>> GetPeopleAsync()
            {
                return _database.Table<Person>().ToListAsync();
            }

            public Task<int> SavePersonAsync(Person person)
            {
                return _database.InsertAsync(person);
            }
        }
    }
    ```

    Esta classe contém código para criar o banco de dados, ler dados dele e gravar dados nele. O código usa APIs SQLite.NET assíncronas que movem operações de banco de dados para threads de segundo plano. Além disso, o construtor `Database` usa o caminho para o arquivo de banco de dados como um argumento. Esse caminho será fornecido pela classe `App` no próximo exercício.

1. No **Gerenciador de Soluções**, no projeto **LocalDatabaseTutorial**, expanda **App.xaml** e clique duas vezes em **App.xaml.cs** para abri-lo. Em seguida, em **App.xaml.cs**, remova todo o código de modelo e substitua-o pelo código a seguir:

    ```csharp
    using System;
    using System.IO;
    using Xamarin.Forms;

    namespace LocalDatabaseTutorial
    {
        public partial class App : Application
        {
            static Database database;

            public static Database Database
            {
                get
                {
                    if (database == null)
                    {
                        database = new Database(Path.Combine(Environment.GetFolderPath(Environment.SpecialFolder.LocalApplicationData), "people.db3"));
                    }
                    return database;
                }
            }

            public App()
            {
                InitializeComponent();

                MainPage = new MainPage();
            }

            protected override void OnStart()
            {
                // Handle when your app starts
            }

            protected override void OnSleep()
            {
                // Handle when your app sleeps
            }

            protected override void OnResume()
            {
                // Handle when your app resumes
            }
        }
    }
    ```

    Esse código define uma propriedade `Database` que cria uma nova instância de `Database` como um singleton. Um caminho de arquivo local e o nome de arquivo, que representa onde armazenar o banco de dados, são passados como o argumento para o construtor da classe `Database`.

    > [!IMPORTANT]
    > A vantagem de expor o banco de dados como um singleton é que uma conexão de banco de dados individual criada é mantida aberta enquanto o aplicativo é executado, evitando, portanto, o trabalho de abrir e fechar o arquivo de banco de dados cada vez que uma operação de banco de dados é realizada.

1. Compile a solução para garantir que não haja erros.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

1. No **Painel de Soluções**, no projeto **LocalDatabaseTutorial**, adicione uma nova classe chamada `Person` ao projeto. Em seguida, em **Person.cs**, remova todo o código do modelo e substitua-o pelo código a seguir:

    ```csharp
    using SQLite;

    namespace LocalDatabaseTutorial
    {
        public class Person
        {
            [PrimaryKey, AutoIncrement]
            public int ID { get; set; }
            public string Name { get; set; }
            public int Age { get; set; }
        }
    }
    ```

    Esse código define uma classe `Person` que armazenará dados sobre cada pessoa no aplicativo. A propriedade `ID` é marcada com os atributos `PrimaryKey` e `AutoIncrement` para garantir que cada instância de `Person` no banco de dados terá uma ID exclusiva fornecida pelo SQLite.NET.

1. No **Painel de Soluções**, no projeto **LocalDatabaseTutorial**, adicione uma nova classe chamada `Database` ao projeto. Em seguida, em **Database.cs**, remova todo o código do modelo e substitua-o pelo código a seguir:

    ```csharp
    using System.Collections.Generic;
    using System.Threading.Tasks;
    using SQLite;

    namespace LocalDatabaseTutorial
    {
        public class Database
        {
            readonly SQLiteAsyncConnection _database;

            public Database(string dbPath)
            {
                _database = new SQLiteAsyncConnection(dbPath);
                _database.CreateTableAsync<Person>().Wait();
            }

            public Task<List<Person>> GetPeopleAsync()
            {
                return _database.Table<Person>().ToListAsync();
            }

            public Task<int> SavePersonAsync(Person person)
            {
                return _database.InsertAsync(person);
            }
        }
    }
    ```

    Esta classe contém código para criar o banco de dados, ler dados dele e gravar dados nele. O código usa APIs SQLite.NET assíncronas que movem operações de banco de dados para threads de segundo plano. Além disso, o construtor `Database` usa o caminho para o arquivo de banco de dados como um argumento. Esse caminho será fornecido pela classe `App` no próximo exercício.

1. No **Painel de Soluções**, no projeto **LocalDatabaseTutorial**, expanda **App.xaml** e clique duas vezes em **App.xaml.cs** para abri-lo. Em seguida, em **App.xaml.cs**, remova todo o código de modelo e substitua-o pelo código a seguir:

    ```csharp
    using System;
    using System.IO;
    using Xamarin.Forms;

    namespace LocalDatabaseTutorial
    {
        public partial class App : Application
        {
            static Database database;

            public static Database Database
            {
                get
                {
                    if (database == null)
                    {
                        database = new Database(Path.Combine(Environment.GetFolderPath(Environment.SpecialFolder.LocalApplicationData), "people.db3"));
                    }
                    return database;
                }
            }

            public App()
            {
                InitializeComponent();

                MainPage = new MainPage();
            }

            protected override void OnStart()
            {
                // Handle when your app starts
            }

            protected override void OnSleep()
            {
                // Handle when your app sleeps
            }

            protected override void OnResume()
            {
                // Handle when your app resumes
            }
        }
    }
    ```

    Esse código define uma propriedade `Database` que cria uma nova instância de `Database` como um singleton. Um caminho de arquivo local e o nome de arquivo, que representa onde armazenar o banco de dados, são passados como o argumento para o construtor da classe `Database`.

    > [!IMPORTANT]
    > A vantagem de expor o banco de dados como um singleton é que uma conexão de banco de dados individual criada é mantida aberta enquanto o aplicativo é executado, evitando, portanto, o trabalho de abrir e fechar o arquivo de banco de dados cada vez que uma operação de banco de dados é realizada.

1. Compile a solução para garantir que não haja erros.
