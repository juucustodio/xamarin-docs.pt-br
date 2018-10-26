---
title: Bancos de dados no xamarin. Mac
description: Este artigo aborda o uso de chave-valor de codificação e chave-valor observando para permitir a associação de dados entre bancos de dados SQLite e elementos de interface do usuário no Interface Builder do Xcode. Ele também aborda o uso SQLite.NET ORM para fornecer acesso aos dados do SQLite.
ms.prod: xamarin
ms.assetid: 44FAFDA8-612A-4E0F-8BB4-5C92A3F4D552
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 03/14/2017
ms.openlocfilehash: 465b4a34d54dbee92461669b16c3b8a13188bbde
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50122199"
---
# <a name="databases-in-xamarinmac"></a>Bancos de dados no xamarin. Mac

_Este artigo aborda o uso de chave-valor de codificação e chave-valor observando para permitir a associação de dados entre bancos de dados SQLite e elementos de interface do usuário no Interface Builder do Xcode. Ele também aborda o uso SQLite.NET ORM para fornecer acesso aos dados do SQLite._

## <a name="overview"></a>Visão geral

Ao trabalhar com c# e .NET em um aplicativo xamarin. Mac, você tem acesso a bancos de dados SQLite mesmo que um aplicativo xamarin. IOS ou xamarin. Android pode acessar.

Neste artigo vamos abordar duas maneiras de acessar dados do SQLite:

1. **Acesso direto** -, acessando diretamente um banco de dados SQLite, podemos usar dados do banco de dados para a codificação de chave-valor e associação de dados com elementos de interface do usuário criada no Interface Builder do Xcode. Usando a chave-valor de codificação e associação de dados técnicas em seu aplicativo xamarin. Mac, você pode diminuir consideravelmente a quantidade de código que você precisa para escrever e manter para popular e trabalhar com elementos de interface do usuário. Você também tem a vantagem de dissociar ainda mais seus dados de backup (_modelo de dados_) da sua frente terminar a Interface do usuário (_Model-View-Controller_), gerando mais fáceis de manter aplicativos mais flexíveis Design.
2. **SQLite.NET ORM** - por meio de software livre [SQLite.NET](http://www.sqlite.org) Manager da relação de objeto (ORM), podemos pode reduzir significativamente a quantidade de código necessária para ler e gravar dados de um banco de dados SQLite. Esses dados, em seguida, podem ser usados para popular um item de interface do usuário como uma exibição de tabela.

[![Um exemplo de aplicativo em execução](databases-images/intro01.png "um exemplo de aplicativo em execução")](databases-images/intro01-large.png#lightbox)

Neste artigo, abordaremos os fundamentos de trabalhar com codificação de chave-valor e a vinculação de dados com bancos de dados SQLite em um aplicativo xamarin. Mac. É altamente recomendável que você trabalhe por meio de [Hello, Mac](~/mac/get-started/hello-mac.md) pela primeira vez, especificamente o artigo a [Introdução ao Interface Builder e Xcode](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) e [saídas e ações](~/mac/get-started/hello-mac.md#outlets-and-actions) seções, como ela aborda os principais conceitos e técnicas que usaremos neste artigo.

Já que vamos usar a codificação de chave-valor e vinculação de dados, trabalhar com o [vinculação de dados e a codificação de chave-valor](~/mac/app-fundamentals/databinding.md) em primeiro lugar, como principais técnicas e conceitos serão abordados que será usado em seu exemplo e esta documentação aplicativo.

Talvez você queira dar uma olhada o [classes expondo c# / métodos para Objective-C](~/mac/internals/how-it-works.md) seção o [recursos internos de xamarin. Mac](~/mac/internals/how-it-works.md) documentar Além disso, ele explica o `Register` e `Export` atributos usado para conectar suas classes de c# a objetos de Objective-C e da interface do usuário elementos.

## <a name="direct-sqlite-access"></a>Acesso direto do SQLite

Para dados do SQLite que vai ser associados aos elementos de interface do usuário no Interface Builder do Xcode, é altamente recomendável que você acessar o banco de dados SQLite diretamente (em vez de usar uma técnica, como um ORM), uma vez que você tem controle total sobre a forma como os dados é gravados e lido  do banco de dados.

Como vimos na [vinculação de dados e a codificação de chave-valor](~/mac/app-fundamentals/databinding.md) documentação, com o uso de chave-valor de codificação e associação de dados técnicas em seu aplicativo xamarin. Mac, você pode diminuir bastante a quantidade de código que você precisa escrever e Manter para popular e trabalhar com elementos de interface do usuário. Quando combinado com acesso direto a um banco de dados SQLite, ele pode reduzir significativamente a quantidade de código necessária para ler e gravar dados no banco de dados.

Neste artigo, nós vamos modificar o aplicativo de exemplo da vinculação de dados e documentos de codificação de chave-valor para usar um banco de dados SQLite como a origem de backup para a associação.

### <a name="including-sqlite-database-support"></a>Incluindo o suporte de banco de dados SQLite

Antes que possamos continuar, precisamos adicionar suporte de banco de dados SQLite para nosso aplicativo, incluindo referências a algumas. Arquivos de DLLs.

Faça o seguinte:

1. No **painel de soluções**, clique com botão direito no **referências** pasta e selecione **Editar referências**.
2. Selecione ambos os **Mono.Data.Sqlite** e **System. Data** assemblies: 

    [![Adição de referências exigidas](databases-images/reference01.png "adicionando as referências necessárias")](databases-images/reference01-large.png#lightbox)
3. Clique o **Okey** botão para salvar suas alterações e adicione as referências.

### <a name="modifying-the-data-model"></a>Modificando o modelo de dados

Agora que adicionamos suporte para acessar diretamente um banco de dados SQLite para nosso aplicativo, precisamos modificar nosso objeto de modelo de dados para ler e gravar dados do banco de dados (bem como fornecer vinculação de dados e a codificação de chave-valor). No caso do nosso aplicativo de exemplo, vamos editar o **PersonModel.cs** de classe e torná-lo semelhante ao seguinte:

```csharp
using System;
using System.Data;
using System.IO;
using Mono.Data.Sqlite;
using Foundation;
using AppKit;

namespace MacDatabase
{
    [Register("PersonModel")]
    public class PersonModel : NSObject
    {
        #region Private Variables
        private string _ID = "";
        private string _managerID = "";
        private string _name = "";
        private string _occupation = "";
        private bool _isManager = false;
        private NSMutableArray _people = new NSMutableArray();
        private SqliteConnection _conn = null;
        #endregion

        #region Computed Properties
        public SqliteConnection Conn {
            get { return _conn; }
            set { _conn = value; }
        }

        [Export("ID")]
        public string ID {
            get { return _ID; }
            set {
                WillChangeValue ("ID");
                _ID = value;
                DidChangeValue ("ID");
            }
        }

        [Export("ManagerID")]
        public string ManagerID {
            get { return _managerID; }
            set {
                WillChangeValue ("ManagerID");
                _managerID = value;
                DidChangeValue ("ManagerID");
            }
        }

        [Export("Name")]
        public string Name {
            get { return _name; }
            set {
                WillChangeValue ("Name");
                _name = value;
                DidChangeValue ("Name");

                // Save changes to database?
                if (_conn != null) Update (_conn);
            }
        }

        [Export("Occupation")]
        public string Occupation {
            get { return _occupation; }
            set {
                WillChangeValue ("Occupation");
                _occupation = value;
                DidChangeValue ("Occupation");

                // Save changes to database?
                if (_conn != null) Update (_conn);
            }
        }

        [Export("isManager")]
        public bool isManager {
            get { return _isManager; }
            set {
                WillChangeValue ("isManager");
                WillChangeValue ("Icon");
                _isManager = value;
                DidChangeValue ("isManager");
                DidChangeValue ("Icon");

                // Save changes to database?
                if (_conn != null) Update (_conn);
            }
        }

        [Export("isEmployee")]
        public bool isEmployee {
            get { return (NumberOfEmployees == 0); }
        }

        [Export("Icon")]
        public NSImage Icon {
            get {
                if (isManager) {
                    return NSImage.ImageNamed ("group.png");
                } else {
                    return NSImage.ImageNamed ("user.png");
                }
            }
        }

        [Export("personModelArray")]
        public NSArray People {
            get { return _people; }
        }

        [Export("NumberOfEmployees")]
        public nint NumberOfEmployees {
            get { return (nint)_people.Count; }
        }
        #endregion

        #region Constructors
        public PersonModel ()
        {
        }

        public PersonModel (string name, string occupation)
        {
            // Initialize
            this.Name = name;
            this.Occupation = occupation;
        }

        public PersonModel (string name, string occupation, bool manager)
        {
            // Initialize
            this.Name = name;
            this.Occupation = occupation;
            this.isManager = manager;
        }

        public PersonModel (string id, string name, string occupation)
        {
            // Initialize
            this.ID = id;
            this.Name = name;
            this.Occupation = occupation;
        }

        public PersonModel (SqliteConnection conn, string id)
        {
            // Load from database
            Load (conn, id);
        }
        #endregion

        #region Array Controller Methods
        [Export("addObject:")]
        public void AddPerson(PersonModel person) {
            WillChangeValue ("personModelArray");
            isManager = true;
            _people.Add (person);
            DidChangeValue ("personModelArray");
        }

        [Export("insertObject:inPersonModelArrayAtIndex:")]
        public void InsertPerson(PersonModel person, nint index) {
            WillChangeValue ("personModelArray");
            _people.Insert (person, index);
            DidChangeValue ("personModelArray");
        }

        [Export("removeObjectFromPersonModelArrayAtIndex:")]
        public void RemovePerson(nint index) {
            WillChangeValue ("personModelArray");
            _people.RemoveObject (index);
            DidChangeValue ("personModelArray");
        }

        [Export("setPersonModelArray:")]
        public void SetPeople(NSMutableArray array) {
            WillChangeValue ("personModelArray");
            _people = array;
            DidChangeValue ("personModelArray");
        }
        #endregion

        #region SQLite Routines
        public void Create(SqliteConnection conn) {

            // Clear last connection to prevent circular call to update
            _conn = null;

            // Create new record ID?
            if (ID == "") {
                ID = Guid.NewGuid ().ToString();
            }

            // Execute query
            conn.Open ();
            using (var command = conn.CreateCommand ()) {
                // Create new command
                command.CommandText = "INSERT INTO [People] (ID, Name, Occupation, isManager, ManagerID) VALUES (@COL1, @COL2, @COL3, @COL4, @COL5)";

                // Populate with data from the record
                command.Parameters.AddWithValue ("@COL1", ID);
                command.Parameters.AddWithValue ("@COL2", Name);
                command.Parameters.AddWithValue ("@COL3", Occupation);
                command.Parameters.AddWithValue ("@COL4", isManager);
                command.Parameters.AddWithValue ("@COL5", ManagerID);

                // Write to database
                command.ExecuteNonQuery ();
            }
            conn.Close ();

            // Save children to database as well
            for (nuint n = 0; n < People.Count; ++n) {
                // Grab person
                var Person = People.GetItem<PersonModel>(n);

                // Save manager ID and create the sub record
                Person.ManagerID = ID;
                Person.Create (conn);
            }

            // Save last connection
            _conn = conn;
        }

        public void Update(SqliteConnection conn) {

            // Clear last connection to prevent circular call to update
            _conn = null;

            // Execute query
            conn.Open ();
            using (var command = conn.CreateCommand ()) {
                // Create new command
                command.CommandText = "UPDATE [People] SET Name = @COL2, Occupation = @COL3, isManager = @COL4, ManagerID = @COL5 WHERE ID = @COL1";

                // Populate with data from the record
                command.Parameters.AddWithValue ("@COL1", ID);
                command.Parameters.AddWithValue ("@COL2", Name);
                command.Parameters.AddWithValue ("@COL3", Occupation);
                command.Parameters.AddWithValue ("@COL4", isManager);
                command.Parameters.AddWithValue ("@COL5", ManagerID);

                // Write to database
                command.ExecuteNonQuery ();
            }
            conn.Close ();

            // Save children to database as well
            for (nuint n = 0; n < People.Count; ++n) {
                // Grab person
                var Person = People.GetItem<PersonModel>(n);

                // Update sub record
                Person.Update (conn);
            }

            // Save last connection
            _conn = conn;
        }

        public void Load(SqliteConnection conn, string id) {
            bool shouldClose = false;

            // Clear last connection to prevent circular call to update
            _conn = null;

            // Is the database already open?
            if (conn.State != ConnectionState.Open) {
                shouldClose = true;
                conn.Open ();
            }

            // Execute query
            using (var command = conn.CreateCommand ()) {
                // Create new command
                command.CommandText = "SELECT * FROM [People] WHERE ID = @COL1";

                // Populate with data from the record
                command.Parameters.AddWithValue ("@COL1", id);

                using (var reader = command.ExecuteReader ()) {
                    while (reader.Read ()) {
                        // Pull values back into class
                        ID = (string)reader [0];
                        Name = (string)reader [1];
                        Occupation = (string)reader [2];
                        isManager = (bool)reader [3];
                        ManagerID = (string)reader [4];
                    }
                }
            }

            // Is this a manager?
            if (isManager) {
                // Yes, load children
                using (var command = conn.CreateCommand ()) {
                    // Create new command
                    command.CommandText = "SELECT ID FROM [People] WHERE ManagerID = @COL1";

                    // Populate with data from the record
                    command.Parameters.AddWithValue ("@COL1", id);

                    using (var reader = command.ExecuteReader ()) {
                        while (reader.Read ()) {
                            // Load child and add to collection
                            var childID = (string)reader [0];
                            var person = new PersonModel (conn, childID);
                            _people.Add (person);
                        }
                    }
                }
            }

            // Should we close the connection to the database
            if (shouldClose) {
                conn.Close ();
            }

            // Save last connection
            _conn = conn;
        }

        public void Delete(SqliteConnection conn) {

            // Clear last connection to prevent circular call to update
            _conn = null;

            // Execute query
            conn.Open ();
            using (var command = conn.CreateCommand ()) {
                // Create new command
                command.CommandText = "DELETE FROM [People] WHERE (ID = @COL1 OR ManagerID = @COL1)";

                // Populate with data from the record
                command.Parameters.AddWithValue ("@COL1", ID);

                // Write to database
                command.ExecuteNonQuery ();
            }
            conn.Close ();

            // Empty class
            ID = "";
            ManagerID = "";
            Name = "";
            Occupation = "";
            isManager = false;
            _people = new NSMutableArray();

            // Save last connection
            _conn = conn;
        }
        #endregion
    }
}
```

Vamos dar uma olhada nas modificações em detalhes abaixo.

Primeiro, adicionamos várias instruções que são necessárias para usar o SQLite e adicionamos uma variável para salvar a nossa última conexão ao banco de dados SQLite:

```csharp
using System.Data;
using System.IO;
using Mono.Data.Sqlite;
...

private SqliteConnection _conn = null;
```

Vamos usar essa conexão salva automaticamente salvar qualquer alteração no registro para o banco de dados quando o usuário modifica o conteúdo na interface do usuário por meio de associação de dados:

```csharp
[Export("Name")]
public string Name {
    get { return _name; }
    set {
        WillChangeValue ("Name");
        _name = value;
        DidChangeValue ("Name");

        // Save changes to database?
        if (_conn != null) Update (_conn);
    }
}

[Export("Occupation")]
public string Occupation {
    get { return _occupation; }
    set {
        WillChangeValue ("Occupation");
        _occupation = value;
        DidChangeValue ("Occupation");

        // Save changes to database?
        if (_conn != null) Update (_conn);
    }
}

[Export("isManager")]
public bool isManager {
    get { return _isManager; }
    set {
        WillChangeValue ("isManager");
        WillChangeValue ("Icon");
        _isManager = value;
        DidChangeValue ("isManager");
        DidChangeValue ("Icon");

        // Save changes to database?
        if (_conn != null) Update (_conn);
    }
}
```

Todas as alterações feitas a **nome**, **ocupação** ou **isManager** propriedades serão enviadas ao banco de dados se os dados foram salvos nela antes (por exemplo, se o `_conn` a variável não é `null`). Em seguida, vamos examinar os métodos que adicionamos ao **Create**, **atualização**, **carga** e **excluir** pessoas do banco de dados.

#### <a name="create-a-new-record"></a>Criar um novo registro

O código a seguir foi adicionado para criar um novo registro no banco de dados SQLite:

```csharp
public void Create(SqliteConnection conn) {

    // Clear last connection to prevent circular call to update
    _conn = null;

    // Create new record ID?
    if (ID == "") {
        ID = Guid.NewGuid ().ToString();
    }

    // Execute query
    conn.Open ();
    using (var command = conn.CreateCommand ()) {
        // Create new command
        command.CommandText = "INSERT INTO [People] (ID, Name, Occupation, isManager, ManagerID) VALUES (@COL1, @COL2, @COL3, @COL4, @COL5)";

        // Populate with data from the record
        command.Parameters.AddWithValue ("@COL1", ID);
        command.Parameters.AddWithValue ("@COL2", Name);
        command.Parameters.AddWithValue ("@COL3", Occupation);
        command.Parameters.AddWithValue ("@COL4", isManager);
        command.Parameters.AddWithValue ("@COL5", ManagerID);

        // Write to database
        command.ExecuteNonQuery ();
    }
    conn.Close ();

    // Save children to database as well
    for (nuint n = 0; n < People.Count; ++n) {
        // Grab person
        var Person = People.GetItem<PersonModel>(n);

        // Save manager ID and create the sub record
        Person.ManagerID = ID;
        Person.Create (conn);
    }

    // Save last connection
    _conn = conn;
}
```

Estamos usando um `SQLiteCommand` para criar o novo registro no banco de dados. Podemos obter um novo comando do `SQLiteConnection` (conn) que é passado para o método chamando `CreateCommand`. Em seguida, definimos a instrução SQL para gravar o novo registro, fornecendo parâmetros para os valores reais:

```csharp
command.CommandText = "INSERT INTO [People] (ID, Name, Occupation, isManager, ManagerID) VALUES (@COL1, @COL2, @COL3, @COL4, @COL5)";
```

Posteriormente, definimos os valores para os parâmetros usando o `Parameters.AddWithValue` método no `SQLiteCommand`. Usando parâmetros, podemos garantir que os valores (como uma aspa simples) obterem codificadas corretamente antes de serem enviados para o SQLite. Exemplo:

```csharp
command.Parameters.AddWithValue ("@COL1", ID);
```

Finalmente, uma vez que uma pessoa pode ser um gerenciador e tem uma coleção de funcionários sob eles, estamos recursivamente chamando o `Create` método em que essas pessoas para salvá-los no banco de dados:

```csharp
// Save children to database as well
for (nuint n = 0; n < People.Count; ++n) {
    // Grab person
    var Person = People.GetItem<PersonModel>(n);

    // Save manager ID and create the sub record
    Person.ManagerID = ID;
    Person.Create (conn);
}
```

#### <a name="updating-a-record"></a>Atualizando um registro

O código a seguir foi adicionado para atualizar um registro existente no banco de dados SQLite:

```csharp
public void Update(SqliteConnection conn) {

    // Clear last connection to prevent circular call to update
    _conn = null;

    // Execute query
    conn.Open ();
    using (var command = conn.CreateCommand ()) {
        // Create new command
        command.CommandText = "UPDATE [People] SET Name = @COL2, Occupation = @COL3, isManager = @COL4, ManagerID = @COL5 WHERE ID = @COL1";

        // Populate with data from the record
        command.Parameters.AddWithValue ("@COL1", ID);
        command.Parameters.AddWithValue ("@COL2", Name);
        command.Parameters.AddWithValue ("@COL3", Occupation);
        command.Parameters.AddWithValue ("@COL4", isManager);
        command.Parameters.AddWithValue ("@COL5", ManagerID);

        // Write to database
        command.ExecuteNonQuery ();
    }
    conn.Close ();

    // Save children to database as well
    for (nuint n = 0; n < People.Count; ++n) {
        // Grab person
        var Person = People.GetItem<PersonModel>(n);

        // Update sub record
        Person.Update (conn);
    }

    // Save last connection
    _conn = conn;
}
```

Como o **Create** acima, obtemos um `SQLiteCommand` do passado no `SQLiteConnection`e defina a nosso SQL para atualizar nosso registro (fornecendo parâmetros):

```csharp
command.CommandText = "UPDATE [People] SET Name = @COL2, Occupation = @COL3, isManager = @COL4, ManagerID = @COL5 WHERE ID = @COL1";
```

Vamos preencher os valores de parâmetro (exemplo: `command.Parameters.AddWithValue ("@COL1", ID);`) e novamente, recursivamente irei chamar update em qualquer filho registros:

```csharp
// Save children to database as well
for (nuint n = 0; n < People.Count; ++n) {
    // Grab person
    var Person = People.GetItem<PersonModel>(n);

    // Update sub record
    Person.Update (conn);
}
```
#### <a name="loading-a-record"></a>Carregamento de um registro

O código a seguir foi adicionado para carregar um registro existente do banco de dados SQLite:

```csharp
public void Load(SqliteConnection conn, string id) {
    bool shouldClose = false;

    // Clear last connection to prevent circular call to update
    _conn = null;

    // Is the database already open?
    if (conn.State != ConnectionState.Open) {
        shouldClose = true;
        conn.Open ();
    }

    // Execute query
    using (var command = conn.CreateCommand ()) {
        // Create new command
        command.CommandText = "SELECT * FROM [People] WHERE ID = @COL1";

        // Populate with data from the record
        command.Parameters.AddWithValue ("@COL1", id);

        using (var reader = command.ExecuteReader ()) {
            while (reader.Read ()) {
                // Pull values back into class
                ID = (string)reader [0];
                Name = (string)reader [1];
                Occupation = (string)reader [2];
                isManager = (bool)reader [3];
                ManagerID = (string)reader [4];
            }
        }
    }

    // Is this a manager?
    if (isManager) {
        // Yes, load children
        using (var command = conn.CreateCommand ()) {
            // Create new command
            command.CommandText = "SELECT ID FROM [People] WHERE ManagerID = @COL1";

            // Populate with data from the record
            command.Parameters.AddWithValue ("@COL1", id);

            using (var reader = command.ExecuteReader ()) {
                while (reader.Read ()) {
                    // Load child and add to collection
                    var childID = (string)reader [0];
                    var person = new PersonModel (conn, childID);
                    _people.Add (person);
                }
            }
        }
    }

    // Should we close the connection to the database
    if (shouldClose) {
        conn.Close ();
    }

    // Save last connection
    _conn = conn;
}
```

Porque a rotina pode ser chamada recursivamente de um objeto pai (por exemplo, um objeto Gerenciador de carregamento de seu objeto de funcionários), código especial foi adicionado para manipular abrindo e fechando a conexão ao banco de dados:

```csharp
bool shouldClose = false;
...

// Is the database already open?
if (conn.State != ConnectionState.Open) {
    shouldClose = true;
    conn.Open ();
}
...

// Should we close the connection to the database
if (shouldClose) {
    conn.Close ();
}

```

Como sempre, definimos nossa SQL para recuperar o registro e usar parâmetros:

```csharp
// Create new command
command.CommandText = "SELECT ID FROM [People] WHERE ManagerID = @COL1";

// Populate with data from the record
command.Parameters.AddWithValue ("@COL1", id);
```

Por fim, podemos usar um leitor de dados para executar a consulta e retorna os campos de registro (que podemos copiar para a instância da `PersonModel` classe):

```csharp
using (var reader = command.ExecuteReader ()) {
    while (reader.Read ()) {
        // Pull values back into class
        ID = (string)reader [0];
        Name = (string)reader [1];
        Occupation = (string)reader [2];
        isManager = (bool)reader [3];
        ManagerID = (string)reader [4];
    }
}
```

Se essa pessoa é um gerente, precisamos também carregar todos os seus funcionários (novamente, chamando recursivamente seus `Load` método):

```csharp
// Is this a manager?
if (isManager) {
    // Yes, load children
    using (var command = conn.CreateCommand ()) {
        // Create new command
        command.CommandText = "SELECT ID FROM [People] WHERE ManagerID = @COL1";

        // Populate with data from the record
        command.Parameters.AddWithValue ("@COL1", id);

        using (var reader = command.ExecuteReader ()) {
            while (reader.Read ()) {
                // Load child and add to collection
                var childID = (string)reader [0];
                var person = new PersonModel (conn, childID);
                _people.Add (person);
            }
        }
    }
}
```

#### <a name="deleting-a-record"></a>Excluindo um registro

O código a seguir foi adicionado para excluir um registro existente do banco de dados SQLite:

```csharp
public void Delete(SqliteConnection conn) {

    // Clear last connection to prevent circular call to update
    _conn = null;

    // Execute query
    conn.Open ();
    using (var command = conn.CreateCommand ()) {
        // Create new command
        command.CommandText = "DELETE FROM [People] WHERE (ID = @COL1 OR ManagerID = @COL1)";

        // Populate with data from the record
        command.Parameters.AddWithValue ("@COL1", ID);

        // Write to database
        command.ExecuteNonQuery ();
    }
    conn.Close ();

    // Empty class
    ID = "";
    ManagerID = "";
    Name = "";
    Occupation = "";
    isManager = false;
    _people = new NSMutableArray();

    // Save last connection
    _conn = conn;
}
```

Aqui, fornecemos o SQL para excluir o registro de gerentes e os registros de todos os funcionários em que o Gerenciador de (usando parâmetros):

```csharp
// Create new command
command.CommandText = "DELETE FROM [People] WHERE (ID = @COL1 OR ManagerID = @COL1)";

// Populate with data from the record
command.Parameters.AddWithValue ("@COL1", ID);
```

Depois que o registro tiver sido removido, desmarcamos a instância atual do `PersonModel` classe:

```csharp
// Empty class
ID = "";
ManagerID = "";
Name = "";
Occupation = "";
isManager = false;
_people = new NSMutableArray();
```

### <a name="initializing-the-database"></a>Inicializando o banco de dados

Com as alterações ao nosso modelo de dados no local para dar suporte à leitura e gravação ao banco de dados, é necessário abrir uma conexão ao banco de dados e inicializá-lo na primeira execução. Vamos adicionar o seguinte código ao nosso **MainWindow.cs** arquivo:

```csharp
using System.Data;
using System.IO;
using Mono.Data.Sqlite;
...

private SqliteConnection DatabaseConnection = null;
...

private SqliteConnection GetDatabaseConnection() {
    var documents = Environment.GetFolderPath (Environment.SpecialFolder.Desktop);
    string db = Path.Combine (documents, "People.db3");

    // Create the database if it doesn't already exist
    bool exists = File.Exists (db);
    if (!exists)
        SqliteConnection.CreateFile (db);

    // Create connection to the database
    var conn = new SqliteConnection("Data Source=" + db);

    // Set the structure of the database
    if (!exists) {
        var commands = new[] {
            "CREATE TABLE People (ID TEXT, Name TEXT, Occupation TEXT, isManager BOOLEAN, ManagerID TEXT)"
        };
        conn.Open ();
        foreach (var cmd in commands) {
            using (var c = conn.CreateCommand()) {
                c.CommandText = cmd;
                c.CommandType = CommandType.Text;
                c.ExecuteNonQuery ();
            }
        }
        conn.Close ();

        // Build list of employees
        var Craig = new PersonModel ("0","Craig Dunn", "Documentation Manager");
        Craig.AddPerson (new PersonModel ("Amy Burns", "Technical Writer"));
        Craig.AddPerson (new PersonModel ("Joel Martinez", "Web & Infrastructure"));
        Craig.AddPerson (new PersonModel ("Kevin Mullins", "Technical Writer"));
        Craig.AddPerson (new PersonModel ("Mark McLemore", "Technical Writer"));
        Craig.AddPerson (new PersonModel ("Tom Opgenorth", "Technical Writer"));
        Craig.Create (conn);

        var Larry = new PersonModel ("1","Larry O'Brien", "API Documentation Manager");
        Larry.AddPerson (new PersonModel ("Mike Norman", "API Documentor"));
        Larry.Create (conn);
    }

    // Return new connection
    return conn;
}
```

Vamos examinar mais detalhadamente o código acima. Primeiro, vamos escolher um local para o novo banco de dados (neste exemplo, a área de trabalho), para ver se o banco de dados existe e se não estiver, criá-lo:

```csharp
var documents = Environment.GetFolderPath (Environment.SpecialFolder.Desktop);
string db = Path.Combine (documents, "People.db3");

// Create the database if it doesn't already exist
bool exists = File.Exists (db);
if (!exists)
    SqliteConnection.CreateFile (db);
```

Em seguida, podemos estabelecer a conectar-se ao banco de dados usando o caminho que criamos acima:

```csharp
var conn = new SqliteConnection("Data Source=" + db);
```

Em seguida, criamos todas as tabelas do SQL no banco de dados que é necessário:

```csharp
var commands = new[] {
    "CREATE TABLE People (ID TEXT, Name TEXT, Occupation TEXT, isManager BOOLEAN, ManagerID TEXT)"
};
conn.Open ();
foreach (var cmd in commands) {
    using (var c = conn.CreateCommand()) {
        c.CommandText = cmd;
        c.CommandType = CommandType.Text;
        c.ExecuteNonQuery ();
    }
}
conn.Close ();
```

Por fim, podemos usar nosso modelo de dados (`PersonModel`) para criar um conjunto padrão de registros para o tempo de banco de dados primeiro o aplicativo é executado ou se o banco de dados está ausente:

```csharp
// Build list of employees
var Craig = new PersonModel ("0","Craig Dunn", "Documentation Manager");
Craig.AddPerson (new PersonModel ("Amy Burns", "Technical Writer"));
Craig.AddPerson (new PersonModel ("Joel Martinez", "Web & Infrastructure"));
Craig.AddPerson (new PersonModel ("Kevin Mullins", "Technical Writer"));
Craig.AddPerson (new PersonModel ("Mark McLemore", "Technical Writer"));
Craig.AddPerson (new PersonModel ("Tom Opgenorth", "Technical Writer"));
Craig.Create (conn);

var Larry = new PersonModel ("1","Larry O'Brien", "API Documentation Manager");
Larry.AddPerson (new PersonModel ("Mike Norman", "API Documentor"));
Larry.Create (conn);
``` 

Quando o aplicativo é iniciado e abre a janela principal, podemos fazer uma conexão ao banco de dados usando o código que adicionamos acima:

```csharp
public override void AwakeFromNib ()
{
    base.AwakeFromNib ();

    // Get access to database
    DatabaseConnection = GetDatabaseConnection ();
}
```

### <a name="loading-bound-data"></a>Dados associados do carregamento

Com todos os componentes para acessar diretamente os dados associados de um banco de dados SQLite em vigor, podemos carregar os dados em diferentes modos de exibição que fornece de nosso aplicativo e ele será exibido automaticamente na nossa interface do usuário.

#### <a name="loading-a-single-record"></a>Carregar um único registro

Para carregar um único registro onde a ID é sabe, podemos usar o código a seguir:

```csharp
Person = new PersonModel (Conn, "0");
```

#### <a name="loading-all-records"></a>Carregamento de todos os registros

Para carregar todas as pessoas, independentemente se eles são um gerente ou não, use o seguinte código:

```csharp
// Load all employees
_conn.Open ();
using (var command = _conn.CreateCommand ()) {
    // Create new command
    command.CommandText = "SELECT ID FROM [People]";

    using (var reader = command.ExecuteReader ()) {
        while (reader.Read ()) {
            // Load child and add to collection
            var childID = (string)reader [0];
            var person = new PersonModel (_conn, childID);
            AddPerson (person);
        }
    }
}
_conn.Close ();

```

Aqui, estamos usando uma sobrecarga do construtor para o `PersonModel` classe para carregar a pessoa na memória:

```csharp
var person = new PersonModel (_conn, childID);
```

Também estamos chamando a classe de dados associados para adicionar a pessoa a nossa coleção de pessoas `AddPerson (person)`, isso garante que a nossa interface do usuário reconhece a alteração e exibe-o:

```csharp
[Export("addObject:")]
public void AddPerson(PersonModel person) {
    WillChangeValue ("personModelArray");
    isManager = true;
    _people.Add (person);
    DidChangeValue ("personModelArray");
}
```

#### <a name="loading-top-level-records-only"></a>Carregar somente os registros de nível superior

Para carregar apenas os gerentes (por exemplo, para exibir dados em uma exibição de estrutura de tópicos), podemos usar o código a seguir:

```csharp
// Load only managers employees
_conn.Open ();
using (var command = _conn.CreateCommand ()) {
    // Create new command
    command.CommandText = "SELECT ID FROM [People] WHERE isManager = 1";

    using (var reader = command.ExecuteReader ()) {
        while (reader.Read ()) {
            // Load child and add to collection
            var childID = (string)reader [0];
            var person = new PersonModel (_conn, childID);
            AddPerson (person);
        }
    }
}
_conn.Close ();
```

A única diferença na instrução SQL em (que carrega somente os gerentes `command.CommandText = "SELECT ID FROM [People] WHERE isManager = 1"`), mas funciona da mesma forma que a seção acima caso contrário.

<a name="Databases-and-ComboBoxes" />

### <a name="databases-and-comboboxes"></a>Bancos de dados e caixas de combinação

Os controles de Menu disponíveis para macOS (por exemplo, a caixa de combinação) pode ser definidos para preencher a lista suspensa de uma lista interna (que pode ser predefinida no construtor de Interface ou preenchida por meio de código) ou fornecendo sua própria fonte de dados personalizados e externos. Ver [fornecendo dados de controle de Menu](~/mac/user-interface/standard-controls.md#Providing-Menu-Control-Data) para obter mais detalhes.

Por exemplo, edite o exemplo de associação simples acima no construtor de Interface, adicione uma caixa de combinação e expô-lo usando uma saída nomeada `EmployeeSelector`:

[![Expor uma tomada de caixa de combinação](databases-images/combo01.png "expondo uma tomada de caixa de combinação")](databases-images/combo01-large.png#lightbox)

No **Inspetor de atributos**, verifique o **preenche automaticamente** e **usa a fonte de dados** propriedades:

![Configurando os atributos de caixa de combinação](databases-images/combo02.png "Configurando os atributos de caixa de combinação")

Salve suas alterações e retorne ao Visual Studio para Mac para sincronizar.

#### <a name="providing-combobox-data"></a>Fornece dados de caixa de combinação

Em seguida, adicione uma nova classe ao projeto chamado `ComboBoxDataSource` e torná-lo semelhante ao seguinte:

```csharp
using System;
using System.Data;
using System.IO;
using Mono.Data.Sqlite;
using Foundation;
using AppKit;

namespace MacDatabase
{
    public class ComboBoxDataSource : NSComboBoxDataSource
    {
        #region Private Variables
        private SqliteConnection _conn = null;
        private string _tableName = "";
        private string _IDField = "ID";
        private string _displayField = "";
        private nint _recordCount = 0;
        #endregion

        #region Computed Properties
        public SqliteConnection Conn {
            get { return _conn; }
            set { _conn = value; }
        }

        public string TableName {
            get { return _tableName; }
            set { 
                _tableName = value;
                _recordCount = GetRecordCount ();
            }
        }

        public string IDField {
            get { return _IDField; }
            set {
                _IDField = value; 
                _recordCount = GetRecordCount ();
            }
        }

        public string DisplayField {
            get { return _displayField; }
            set { 
                _displayField = value; 
                _recordCount = GetRecordCount ();
            }
        }

        public nint RecordCount {
            get { return _recordCount; }
        }
        #endregion

        #region Constructors
        public ComboBoxDataSource (SqliteConnection conn, string tableName, string displayField)
        {
            // Initialize
            this.Conn = conn;
            this.TableName = tableName;
            this.DisplayField = displayField;
        }

        public ComboBoxDataSource (SqliteConnection conn, string tableName, string idField, string displayField)
        {
            // Initialize
            this.Conn = conn;
            this.TableName = tableName;
            this.IDField = idField;
            this.DisplayField = displayField;
        }
        #endregion

        #region Private Methods
        private nint GetRecordCount ()
        {
            bool shouldClose = false;
            nint count = 0;

            // Has a Table, ID and display field been specified?
            if (TableName !="" && IDField != "" && DisplayField != "") {
                // Is the database already open?
                if (Conn.State != ConnectionState.Open) {
                    shouldClose = true;
                    Conn.Open ();
                }

                // Execute query
                using (var command = Conn.CreateCommand ()) {
                    // Create new command
                    command.CommandText = $"SELECT count({IDField}) FROM [{TableName}]";

                    // Get the results from the database
                    using (var reader = command.ExecuteReader ()) {
                        while (reader.Read ()) {
                            // Read count from query
                            var result = (long)reader [0];
                            count = (nint)result;
                        }
                    }
                }

                // Should we close the connection to the database
                if (shouldClose) {
                    Conn.Close ();
                }
            }

            // Return the number of records
            return count;
        }
        #endregion

        #region Public Methods
        public string IDForIndex (nint index)
        {
            NSString value = new NSString ("");
            bool shouldClose = false;

            // Has a Table, ID and display field been specified?
            if (TableName != "" && IDField != "" && DisplayField != "") {
                // Is the database already open?
                if (Conn.State != ConnectionState.Open) {
                    shouldClose = true;
                    Conn.Open ();
                }

                // Execute query
                using (var command = Conn.CreateCommand ()) {
                    // Create new command
                    command.CommandText = $"SELECT {IDField} FROM [{TableName}] ORDER BY {DisplayField} ASC LIMIT 1 OFFSET {index}";

                    // Get the results from the database
                    using (var reader = command.ExecuteReader ()) {
                        while (reader.Read ()) {
                            // Read the display field from the query
                            value = new NSString ((string)reader [0]);
                        }
                    }
                }

                // Should we close the connection to the database
                if (shouldClose) {
                    Conn.Close ();
                }
            }

            // Return results
            return value;
        }

        public string ValueForIndex (nint index)
        {
            NSString value = new NSString ("");
            bool shouldClose = false;

            // Has a Table, ID and display field been specified?
            if (TableName != "" && IDField != "" && DisplayField != "") {
                // Is the database already open?
                if (Conn.State != ConnectionState.Open) {
                    shouldClose = true;
                    Conn.Open ();
                }

                // Execute query
                using (var command = Conn.CreateCommand ()) {
                    // Create new command
                    command.CommandText = $"SELECT {DisplayField} FROM [{TableName}] ORDER BY {DisplayField} ASC LIMIT 1 OFFSET {index}";

                    // Get the results from the database
                    using (var reader = command.ExecuteReader ()) {
                        while (reader.Read ()) {
                            // Read the display field from the query
                            value = new NSString ((string)reader [0]);
                        }
                    }
                }

                // Should we close the connection to the database
                if (shouldClose) {
                    Conn.Close ();
                }
            }

            // Return results
            return value;
        }

        public string IDForValue (string value)
        {
            NSString result = new NSString ("");
            bool shouldClose = false;

            // Has a Table, ID and display field been specified?
            if (TableName != "" && IDField != "" && DisplayField != "") {
                // Is the database already open?
                if (Conn.State != ConnectionState.Open) {
                    shouldClose = true;
                    Conn.Open ();
                }

                // Execute query
                using (var command = Conn.CreateCommand ()) {
                    // Create new command
                    command.CommandText = $"SELECT {IDField} FROM [{TableName}] WHERE {DisplayField} = @VAL";

                    // Populate parameters
                    command.Parameters.AddWithValue ("@VAL", value);

                    // Get the results from the database
                    using (var reader = command.ExecuteReader ()) {
                        while (reader.Read ()) {
                            // Read the display field from the query
                            result = new NSString ((string)reader [0]);
                        }
                    }
                }

                // Should we close the connection to the database
                if (shouldClose) {
                    Conn.Close ();
                }
            }

            // Return results
            return result;
        }
        #endregion 

        #region Override Methods
        public override nint ItemCount (NSComboBox comboBox)
        {
            return RecordCount;
        }

        public override NSObject ObjectValueForItem (NSComboBox comboBox, nint index)
        {
            NSString value = new NSString ("");
            bool shouldClose = false;

            // Has a Table, ID and display field been specified?
            if (TableName != "" && IDField != "" && DisplayField != "") {
                // Is the database already open?
                if (Conn.State != ConnectionState.Open) {
                    shouldClose = true;
                    Conn.Open ();
                }

                // Execute query
                using (var command = Conn.CreateCommand ()) {
                    // Create new command
                    command.CommandText = $"SELECT {DisplayField} FROM [{TableName}] ORDER BY {DisplayField} ASC LIMIT 1 OFFSET {index}";

                    // Get the results from the database
                    using (var reader = command.ExecuteReader ()) {
                        while (reader.Read ()) {
                            // Read the display field from the query
                            value = new NSString((string)reader [0]);
                        }
                    }
                }

                // Should we close the connection to the database
                if (shouldClose) {
                    Conn.Close ();
                }
            }

            // Return results
            return value;
        }

        public override nint IndexOfItem (NSComboBox comboBox, string value)
        {
            bool shouldClose = false;
            bool found = false;
            string field = "";
            nint index = NSRange.NotFound;

            // Has a Table, ID and display field been specified?
            if (TableName != "" && IDField != "" && DisplayField != "") {
                // Is the database already open?
                if (Conn.State != ConnectionState.Open) {
                    shouldClose = true;
                    Conn.Open ();
                }

                // Execute query
                using (var command = Conn.CreateCommand ()) {
                    // Create new command
                    command.CommandText = $"SELECT {DisplayField} FROM [{TableName}] ORDER BY {DisplayField} ASC";

                    // Get the results from the database
                    using (var reader = command.ExecuteReader ()) {
                        while (reader.Read () && !found) {
                            // Read the display field from the query
                            field = (string)reader [0];
                            ++index;

                            // Is this the value we are searching for?
                            if (value == field) {
                                // Yes, exit loop
                                found = true;
                            }
                        }
                    }
                }

                // Should we close the connection to the database
                if (shouldClose) {
                    Conn.Close ();
                }
            }

            // Return results
            return index;
        }

        public override string CompletedString (NSComboBox comboBox, string uncompletedString)
        {
            bool shouldClose = false;
            bool found = false;
            string field = "";

            // Has a Table, ID and display field been specified?
            if (TableName != "" && IDField != "" && DisplayField != "") {
                // Is the database already open?
                if (Conn.State != ConnectionState.Open) {
                    shouldClose = true;
                    Conn.Open ();
                }

                // Escape search string
                uncompletedString = uncompletedString.Replace ("'", "");

                // Execute query
                using (var command = Conn.CreateCommand ()) {
                    // Create new command
                    command.CommandText = $"SELECT {DisplayField} FROM [{TableName}] WHERE {DisplayField} LIKE @VAL";

                    // Populate parameters
                    command.Parameters.AddWithValue ("@VAL", uncompletedString + "%");

                    // Get the results from the database
                    using (var reader = command.ExecuteReader ()) {
                        while (reader.Read ()) {
                            // Read the display field from the query
                            field = (string)reader [0];
                        }
                    }
                }

                // Should we close the connection to the database
                if (shouldClose) {
                    Conn.Close ();
                }
            }

            // Return results
            return field;
        }
        #endregion
    }
}
```

Neste exemplo, estamos criando um novo `NSComboBoxDataSource` que pode apresentar os itens de caixa de combinação de qualquer fonte de dados SQLite. Primeiro, definimos as propriedades a seguir:

- `Conn` -Obtém ou define uma conexão ao banco de dados SQLite.
- `TableName` -Obtém ou define o nome da tabela.
- `IDField` -Obtém ou define o campo que fornece a ID exclusiva para a tabela especificada. O valor padrão é `ID`.
- `DisplayField` -Obtém ou define o campo que é exibido na lista suspensa.
- `RecordCount` -Obtém o número de registros da tabela fornecida.

Quando criamos uma nova instância do objeto, podemos passar a conexão, nome da tabela, opcionalmente, o campo de ID e o campo de exibição:

```csharp
public ComboBoxDataSource (SqliteConnection conn, string tableName, string displayField)
{
    // Initialize
    this.Conn = conn;
    this.TableName = tableName;
    this.DisplayField = displayField;
}
```

O `GetRecordCount` método retorna o número de registros na tabela fornecida:

```csharp
private nint GetRecordCount ()
{
    bool shouldClose = false;
    nint count = 0;

    // Has a Table, ID and display field been specified?
    if (TableName !="" && IDField != "" && DisplayField != "") {
        // Is the database already open?
        if (Conn.State != ConnectionState.Open) {
            shouldClose = true;
            Conn.Open ();
        }

        // Execute query
        using (var command = Conn.CreateCommand ()) {
            // Create new command
            command.CommandText = $"SELECT count({IDField}) FROM [{TableName}]";

            // Get the results from the database
            using (var reader = command.ExecuteReader ()) {
                while (reader.Read ()) {
                    // Read count from query
                    var result = (long)reader [0];
                    count = (nint)result;
                }
            }
        }

        // Should we close the connection to the database
        if (shouldClose) {
            Conn.Close ();
        }
    }

    // Return the number of records
    return count;
}
```

Ele é chamado sempre que o `TableName`, `IDField` ou `DisplayField` valor das propriedades é alterada.

O `IDForIndex` método retorna a ID exclusiva (`IDField`) para o registro no índice de item da lista suspensa determinado: 

```csharp
public string IDForIndex (nint index)
{
    NSString value = new NSString ("");
    bool shouldClose = false;

    // Has a Table, ID and display field been specified?
    if (TableName != "" && IDField != "" && DisplayField != "") {
        // Is the database already open?
        if (Conn.State != ConnectionState.Open) {
            shouldClose = true;
            Conn.Open ();
        }

        // Execute query
        using (var command = Conn.CreateCommand ()) {
            // Create new command
            command.CommandText = $"SELECT {IDField} FROM [{TableName}] ORDER BY {DisplayField} ASC LIMIT 1 OFFSET {index}";

            // Get the results from the database
            using (var reader = command.ExecuteReader ()) {
                while (reader.Read ()) {
                    // Read the display field from the query
                    value = new NSString ((string)reader [0]);
                }
            }
        }

        // Should we close the connection to the database
        if (shouldClose) {
            Conn.Close ();
        }
    }

    // Return results
    return value;
}
```

O `ValueForIndex` método retorna o valor (`DisplayField`) para o item no índice da lista suspensa de determinado:

```csharp
public string ValueForIndex (nint index)
{
    NSString value = new NSString ("");
    bool shouldClose = false;

    // Has a Table, ID and display field been specified?
    if (TableName != "" && IDField != "" && DisplayField != "") {
        // Is the database already open?
        if (Conn.State != ConnectionState.Open) {
            shouldClose = true;
            Conn.Open ();
        }

        // Execute query
        using (var command = Conn.CreateCommand ()) {
            // Create new command
            command.CommandText = $"SELECT {DisplayField} FROM [{TableName}] ORDER BY {DisplayField} ASC LIMIT 1 OFFSET {index}";

            // Get the results from the database
            using (var reader = command.ExecuteReader ()) {
                while (reader.Read ()) {
                    // Read the display field from the query
                    value = new NSString ((string)reader [0]);
                }
            }
        }

        // Should we close the connection to the database
        if (shouldClose) {
            Conn.Close ();
        }
    }

    // Return results
    return value;
}
```

O `IDForValue` método retorna a ID exclusiva (`IDField`) para determinado valor (`DisplayField`):

```csharp
public string IDForValue (string value)
{
    NSString result = new NSString ("");
    bool shouldClose = false;

    // Has a Table, ID and display field been specified?
    if (TableName != "" && IDField != "" && DisplayField != "") {
        // Is the database already open?
        if (Conn.State != ConnectionState.Open) {
            shouldClose = true;
            Conn.Open ();
        }

        // Execute query
        using (var command = Conn.CreateCommand ()) {
            // Create new command
            command.CommandText = $"SELECT {IDField} FROM [{TableName}] WHERE {DisplayField} = @VAL";

            // Populate parameters
            command.Parameters.AddWithValue ("@VAL", value);

            // Get the results from the database
            using (var reader = command.ExecuteReader ()) {
                while (reader.Read ()) {
                    // Read the display field from the query
                    result = new NSString ((string)reader [0]);
                }
            }
        }

        // Should we close the connection to the database
        if (shouldClose) {
            Conn.Close ();
        }
    }

    // Return results
    return result;
}
```

O `ItemCount` retorna o número pré-calculada de itens na lista, conforme calculado quando o `TableName`, `IDField` ou `DisplayField` propriedades forem alteradas:

```csharp
public override nint ItemCount (NSComboBox comboBox)
{
    return RecordCount;
}
```

O `ObjectValueForItem` método fornece o valor (`DisplayField`) para o índice do item de lista suspensa de determinado:

```csharp
public override NSObject ObjectValueForItem (NSComboBox comboBox, nint index)
{
    NSString value = new NSString ("");
    bool shouldClose = false;

    // Has a Table, ID and display field been specified?
    if (TableName != "" && IDField != "" && DisplayField != "") {
        // Is the database already open?
        if (Conn.State != ConnectionState.Open) {
            shouldClose = true;
            Conn.Open ();
        }

        // Execute query
        using (var command = Conn.CreateCommand ()) {
            // Create new command
            command.CommandText = $"SELECT {DisplayField} FROM [{TableName}] ORDER BY {DisplayField} ASC LIMIT 1 OFFSET {index}";

            // Get the results from the database
            using (var reader = command.ExecuteReader ()) {
                while (reader.Read ()) {
                    // Read the display field from the query
                    value = new NSString((string)reader [0]);
                }
            }
        }

        // Should we close the connection to the database
        if (shouldClose) {
            Conn.Close ();
        }
    }

    // Return results
    return value;
}
```

Observe que estamos usando o `LIMIT` e `OFFSET` instruções em nosso comando SQLite para limitar a um registro que é necessária.

O `IndexOfItem` método retorna o índice do item de menu suspenso do valor (`DisplayField`) determinado:

```csharp
public override nint IndexOfItem (NSComboBox comboBox, string value)
{
    bool shouldClose = false;
    bool found = false;
    string field = "";
    nint index = NSRange.NotFound;

    // Has a Table, ID and display field been specified?
    if (TableName != "" && IDField != "" && DisplayField != "") {
        // Is the database already open?
        if (Conn.State != ConnectionState.Open) {
            shouldClose = true;
            Conn.Open ();
        }

        // Execute query
        using (var command = Conn.CreateCommand ()) {
            // Create new command
            command.CommandText = $"SELECT {DisplayField} FROM [{TableName}] ORDER BY {DisplayField} ASC";

            // Get the results from the database
            using (var reader = command.ExecuteReader ()) {
                while (reader.Read () && !found) {
                    // Read the display field from the query
                    field = (string)reader [0];
                    ++index;

                    // Is this the value we are searching for?
                    if (value == field) {
                        // Yes, exit loop
                        found = true;
                    }
                }
            }
        }

        // Should we close the connection to the database
        if (shouldClose) {
            Conn.Close ();
        }
    }

    // Return results
    return index;
}
```

Se o valor não for encontrado, o `NSRange.NotFound` valor é retornado e todos os itens estão desselecionados na lista suspensa.

O `CompletedString` método retorna o primeiro valor correspondente (`DisplayField`) para uma entrada tipada parcialmente:

```csharp
public override string CompletedString (NSComboBox comboBox, string uncompletedString)
{
    bool shouldClose = false;
    bool found = false;
    string field = "";

    // Has a Table, ID and display field been specified?
    if (TableName != "" && IDField != "" && DisplayField != "") {
        // Is the database already open?
        if (Conn.State != ConnectionState.Open) {
            shouldClose = true;
            Conn.Open ();
        }

        // Escape search string
        uncompletedString = uncompletedString.Replace ("'", "");

        // Execute query
        using (var command = Conn.CreateCommand ()) {
            // Create new command
            command.CommandText = $"SELECT {DisplayField} FROM [{TableName}] WHERE {DisplayField} LIKE @VAL";

            // Populate parameters
            command.Parameters.AddWithValue ("@VAL", uncompletedString + "%");

            // Get the results from the database
            using (var reader = command.ExecuteReader ()) {
                while (reader.Read ()) {
                    // Read the display field from the query
                    field = (string)reader [0];
                }
            }
        }

        // Should we close the connection to the database
        if (shouldClose) {
            Conn.Close ();
        }
    }

    // Return results
    return field;
}
```

#### <a name="displaying-data-and-responding-to-events"></a>Exibindo dados e respondendo a eventos

Para reunir todas as partes, edite o `SubviewSimpleBindingController` e torná-lo semelhante ao seguinte:

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Data;
using System.IO;
using Mono.Data.Sqlite;
using Foundation;
using AppKit;

namespace MacDatabase
{
    public partial class SubviewSimpleBindingController : AppKit.NSViewController
    {
        #region Private Variables
        private PersonModel _person = new PersonModel();
        private SqliteConnection Conn;
        #endregion

        #region Computed Properties
        //strongly typed view accessor
        public new SubviewSimpleBinding View {
            get {
                return (SubviewSimpleBinding)base.View;
            }
        }

        [Export("Person")]
        public PersonModel Person {
            get {return _person; }
            set {
                WillChangeValue ("Person");
                _person = value;
                DidChangeValue ("Person");
            }
        }

        public ComboBoxDataSource DataSource {
            get { return EmployeeSelector.DataSource as ComboBoxDataSource; }
        }
        #endregion

        #region Constructors
        // Called when created from unmanaged code
        public SubviewSimpleBindingController (IntPtr handle) : base (handle)
        {
            Initialize ();
        }

        // Called when created directly from a XIB file
        [Export ("initWithCoder:")]
        public SubviewSimpleBindingController (NSCoder coder) : base (coder)
        {
            Initialize ();
        }

        // Call to load from the XIB/NIB file
        public SubviewSimpleBindingController (SqliteConnection conn) : base ("SubviewSimpleBinding", NSBundle.MainBundle)
        {
            // Initialize
            this.Conn = conn;
            Initialize ();
        }

        // Shared initialization code
        void Initialize ()
        {
        }
        #endregion

        #region Private Methods
        private void LoadSelectedPerson (string id)
        {

            // Found?
            if (id != "") {
                // Yes, load requested record
                Person = new PersonModel (Conn, id);
            }
        }
        #endregion

        #region Override Methods
        public override void AwakeFromNib ()
        {
            base.AwakeFromNib ();

            // Configure Employee selector dropdown
            EmployeeSelector.DataSource = new ComboBoxDataSource (Conn, "People", "Name");

            // Wireup events
            EmployeeSelector.Changed += (sender, e) => {
                // Get ID
                var id = DataSource.IDForValue (EmployeeSelector.StringValue);
                LoadSelectedPerson (id);
            };

            EmployeeSelector.SelectionChanged += (sender, e) => {
                // Get ID
                var id = DataSource.IDForIndex (EmployeeSelector.SelectedIndex);
                LoadSelectedPerson (id);
            };

            // Auto select the first person
            EmployeeSelector.StringValue = DataSource.ValueForIndex (0);
            Person = new PersonModel (Conn, DataSource.IDForIndex(0));
    
        }
        #endregion
    }
}
```

O `DataSource` propriedade fornece um atalho para o `ComboBoxDataSource` (criado acima) anexado à caixa de combinação.

O `LoadSelectedPerson` método carrega a pessoa do banco de dados para a ID exclusiva fornecida:

```csharp
private void LoadSelectedPerson (string id)
{

    // Found?
    if (id != "") {
        // Yes, load requested record
        Person = new PersonModel (Conn, id);
    }
}
```

No `AwakeFromNib` substituição do método, primeiro é anexar uma instância de nossa fonte de dados de caixa de combinação personalizada:

```csharp
EmployeeSelector.DataSource = new ComboBoxDataSource (Conn, "People", "Name");
```

Em seguida, podemos responder ao usuário editar o valor de texto da caixa de combinação, localizando a ID exclusiva associada (`IDField`) dos dados apresentar e carregando a determinada pessoa se encontradas:

```csharp
EmployeeSelector.Changed += (sender, e) => {
    // Get ID
    var id = DataSource.IDForValue (EmployeeSelector.StringValue);
    LoadSelectedPerson (id);
};
```

Também podemos carregar uma nova pessoa se o usuário seleciona um novo item na lista suspensa:

```csharp
EmployeeSelector.SelectionChanged += (sender, e) => {
    // Get ID
    var id = DataSource.IDForIndex (EmployeeSelector.SelectedIndex);
    LoadSelectedPerson (id);
};
```

Por fim, vamos preencher automaticamente a caixa de combinação e a pessoa exibida com o primeiro item na lista:

```csharp
// Auto select the first person
EmployeeSelector.StringValue = DataSource.ValueForIndex (0);
Person = new PersonModel (Conn, DataSource.IDForIndex(0));
```

## <a name="sqlitenet-orm"></a>SQLite.NET ORM

Conforme mencionado acima, por meio de software livre [SQLite.NET](http://www.sqlite.org) Manager da relação de objeto (ORM), podemos pode reduzir significativamente a quantidade de código necessária para ler e gravar dados de um banco de dados SQLite. Isso pode não ser a melhor rota a ser tomada quando a associação de dados devido a vários dos requisitos que colocam a vinculação de dados e a codificação de chave-valor em um objeto.

Acordo com o site SQLite.Net, _"SQLite é uma biblioteca de software que implementa um mecanismo de banco de dados SQL independente, sem servidor, sem nenhuma configuração e transacional. O SQLite é o mecanismo de banco de dados mais amplamente implantado no mundo. O código-fonte para SQLite é de domínio público."_

As seções a seguir, mostraremos como usar SQLite.Net para fornecer dados para uma exibição de tabela.

### <a name="including-the-sqlitenet-nuget"></a>Incluindo o SQLite.net NuGet

SQLite.NET é apresentado como um pacote do NuGet que você incluir em seu aplicativo. Antes de podermos adicionar suporte de banco de dados usando SQLite.NET, precisamos incluir esse pacote.

Faça o seguinte para adicionar o pacote:

1. No **painel de soluções**, com o botão direito do **pacotes** pasta e selecione **adicionar pacotes...**
2. Insira `SQLite.net` no **caixa de pesquisa** e selecione o **sqlite-net** entrada:

    [![Adicionar o pacote NuGet do SQLite](databases-images/nuget01.png "adicionar o pacote NuGet do SQLite")](databases-images/nuget01-large.png#lightbox)
3. Clique o **Adicionar pacote** botão para concluir.

### <a name="creating-the-data-model"></a>Criando o modelo de dados

Vamos adicionar uma nova classe ao projeto e chame em `OccupationModel`. Em seguida, vamos editar o **OccupationModel.cs** de arquivo e torná-lo semelhante ao seguinte:

```csharp
using System;
using SQLite;

namespace MacDatabase
{
    public class OccupationModel
    {
        #region Computed Properties
        [PrimaryKey, AutoIncrement]
        public int ID { get; set; }

        public string Name { get; set;}
        public string Description { get; set;}
        #endregion

        #region Constructors
        public OccupationModel ()
        {
        }

        public OccupationModel (string name, string description)
        {

            // Initialize
            this.Name = name;
            this.Description = description;

        }
        #endregion
    }
}
```

Primeiro, incluímos SQLite.NET (`using Sqlite`), em seguida, podemos expõem várias propriedades, cada um deles será gravada no banco de dados quando esse registro é salvo. A primeira propriedade Verifique como a chave primária e defina-o como incremento automático, da seguinte maneira:

```csharp
[PrimaryKey, AutoIncrement]
public int ID { get; set; }
```
### <a name="initializing-the-database"></a>Inicializando o banco de dados

Com as alterações ao nosso modelo de dados no local para dar suporte à leitura e gravação ao banco de dados, é necessário abrir uma conexão ao banco de dados e inicializá-lo na primeira execução. Vamos adicionar o código a seguir:

```csharp
using SQLite;
...

public SQLiteConnection Conn { get; set; }
...

private SQLiteConnection GetDatabaseConnection() {
    var documents = Environment.GetFolderPath (Environment.SpecialFolder.Desktop);
    string db = Path.Combine (documents, "Occupation.db3");
    OccupationModel Occupation;

    // Create the database if it doesn't already exist
    bool exists = File.Exists (db);

    // Create connection to database
    var conn = new SQLiteConnection (db);

    // Initially populate table?
    if (!exists) {
        // Yes, build table
        conn.CreateTable<OccupationModel> ();

        // Add occupations
        Occupation = new OccupationModel ("Documentation Manager", "Manages the Documentation Group");
        conn.Insert (Occupation);

        Occupation = new OccupationModel ("Technical Writer", "Writes technical documentation and sample applications");
        conn.Insert (Occupation);

        Occupation = new OccupationModel ("Web & Infrastructure", "Creates and maintains the websites that drive documentation");
        conn.Insert (Occupation);

        Occupation = new OccupationModel ("API Documentation Manager", "Manages the API Documentation Group");
        conn.Insert (Occupation);

        Occupation = new OccupationModel ("API Documenter", "Creates and maintains API documentation");
        conn.Insert (Occupation);
    }

    return conn;
}
```

Primeiro, vamos obter um caminho para o banco de dados (a área de trabalho neste caso) e se o banco de dados já existe:

```csharp
var documents = Environment.GetFolderPath (Environment.SpecialFolder.Desktop);
string db = Path.Combine (documents, "Occupation.db3");
OccupationModel Occupation;

// Create the database if it doesn't already exist
bool exists = File.Exists (db);
```

Em seguida, podemos estabelecer uma conexão ao banco de dados no caminho que criamos acima:

```csharp
var conn = new SQLiteConnection (db);
```

Por fim, criamos a tabela e adicionar alguns registros de padrão:

```csharp
// Yes, build table
conn.CreateTable<OccupationModel> ();

// Add occupations
Occupation = new OccupationModel ("Documentation Manager", "Manages the Documentation Group");
conn.Insert (Occupation);

Occupation = new OccupationModel ("Technical Writer", "Writes technical documentation and sample applications");
conn.Insert (Occupation);

Occupation = new OccupationModel ("Web & Infrastructure", "Creates and maintains the websites that drive documentation");
conn.Insert (Occupation);

Occupation = new OccupationModel ("API Documentation Manager", "Manages the API Documentation Group");
conn.Insert (Occupation);

Occupation = new OccupationModel ("API Documenter", "Creates and maintains API documentation");
conn.Insert (Occupation);
```

### <a name="adding-a-table-view"></a>Adicionando uma exibição de tabela

Como um exemplo de uso, vamos adicionar uma exibição de tabela para nossa interface do usuário no Interface builder do Xcode. Podemos irá expor essa exibição de tabela por meio de uma tomada (`OccupationTable`) para que podemos pode acessá-lo por meio de código c#:

[![Expor uma tomada de modo de exibição de tabela](databases-images/table01.png "expondo uma tomada de modo de exibição de tabela")](databases-images/table01-large.png#lightbox)

Em seguida, vamos adicionar as classes personalizadas para preencher esta tabela com dados do banco de dados SQLite.NET.

### <a name="creating-the-table-data-source"></a>Criando a fonte de dados de tabela

Vamos criar uma fonte de dados personalizado para fornecer dados para nossa tabela. Primeiro, adicione uma nova classe chamada `TableORMDatasource` e torná-lo semelhante ao seguinte:

```csharp
using System;
using AppKit;
using CoreGraphics;
using Foundation;
using System.Collections;
using System.Collections.Generic;
using SQLite;

namespace MacDatabase
{
    public class TableORMDatasource : NSTableViewDataSource
    {
        #region Computed Properties
        public List<OccupationModel> Occupations { get; set;} = new List<OccupationModel>();
        public SQLiteConnection Conn { get; set; }
        #endregion

        #region Constructors
        public TableORMDatasource (SQLiteConnection conn)
        {
            // Initialize
            this.Conn = conn;
            LoadOccupations ();
        }
        #endregion

        #region Public Methods
        public void LoadOccupations() {

            // Get occupations from database
            var query = Conn.Table<OccupationModel> ();

            // Copy into table collection
            Occupations.Clear ();
            foreach (OccupationModel occupation in query) {
                Occupations.Add (occupation);
            }

        }
        #endregion

        #region Override Methods
        public override nint GetRowCount (NSTableView tableView)
        {
            return Occupations.Count;
        }
        #endregion
    }
}
```

Quando criamos uma instância dessa classe posteriormente, vamos passar nossa conexão de banco de dados SQLite.NET aberto. O `LoadOccupations` método consulta o banco de dados e copia os registros encontrados na memória (usando nosso `OccupationModel` modelo de dados).

### <a name="creating-the-table-delegate"></a>Criar o delegado de tabela

A classe final que precisamos é um delegado de tabela personalizados para exibir as informações que carregados do banco de dados SQLite.NET. Vamos adicionar um novo `TableORMDelegate` ao nosso projeto e torná-lo semelhante ao seguinte:

```csharp
using System;
using AppKit;
using CoreGraphics;
using Foundation;
using System.Collections;
using System.Collections.Generic;
using SQLite;

namespace MacDatabase
{
    public class TableORMDelegate : NSTableViewDelegate
    {
        #region Constants 
        private const string CellIdentifier = "OccCell";
        #endregion

        #region Private Variables
        private TableORMDatasource DataSource;
        #endregion

        #region Constructors
        public TableORMDelegate (TableORMDatasource dataSource)
        {
            // Initialize
            this.DataSource = dataSource;
        }
        #endregion

        #region Override Methods
        public override NSView GetViewForItem (NSTableView tableView, NSTableColumn tableColumn, nint row)
        {
            // This pattern allows you reuse existing views when they are no-longer in use.
            // If the returned view is null, you instance up a new view
            // If a non-null view is returned, you modify it enough to reflect the new data
            NSTextField view = (NSTextField)tableView.MakeView (CellIdentifier, this);
            if (view == null) {
                view = new NSTextField ();
                view.Identifier = CellIdentifier;
                view.BackgroundColor = NSColor.Clear;
                view.Bordered = false;
                view.Selectable = false;
                view.Editable = false;
            }

            // Setup view based on the column selected
            switch (tableColumn.Title) {
            case "Occupation":
                view.StringValue = DataSource.Occupations [(int)row].Name;
                break;
            case "Description":
                view.StringValue = DataSource.Occupations [(int)row].Description;
                break;
            }

            return view;
        }
        #endregion
    }
}
```

Aqui, usamos a fonte de dados `Occupations` coleção (que são carregados do banco de dados SQLite.NET) para preencher as colunas da nossa tabela por meio de `GetViewForItem` substituição do método.

### <a name="populating-the-table"></a>Populando a tabela

Com todas as peças no lugar, vamos preencher nossa tabela quando ela é inflada do arquivo. XIB, substituindo o `AwakeFromNib` método e tornando a aparência semelhante à seguinte:

```csharp
public override void AwakeFromNib ()
{
    base.AwakeFromNib ();

    // Get database connection
    Conn = GetDatabaseConnection ();

    // Create the Occupation Table Data Source and populate it
    var DataSource = new TableORMDatasource (Conn);

    // Populate the Product Table
    OccupationTable.DataSource = DataSource;
    OccupationTable.Delegate = new TableORMDelegate (DataSource);
}
```

Primeiro, podemos obter acesso ao nosso banco de dados SQLite.NET, criando e preenchendo-o se ele ainda não existir. Em seguida, criamos uma nova instância da nossa fonte de dados de tabela personalizado, passe nossa conexão de banco de dados e podemos anexá-lo à tabela. Por fim, criamos uma nova instância do nosso representante de tabela personalizado, passe em nossa fonte de dados e anexá-lo à tabela.

## <a name="summary"></a>Resumo

Este artigo apresentou uma visão detalhada de como trabalhar com associação de dados e a codificação de chave-valor com bancos de dados SQLite em um aplicativo xamarin. Mac. Primeiro, ele examinou expondo uma classe c# para Objective-C usando a codificação de chave-valor (KVC) e chave-valor observando (KVO). Em seguida, ele mostrou como usar uma classe KVO compatível e dados associação-lo aos elementos de interface do usuário no Interface Builder do Xcode. O artigo também abordou trabalhando com SQLite dados por meio de SQLite.NET ORM e exibir esses dados em uma exibição de tabela.



## <a name="related-links"></a>Links relacionados

- [MacDatabase (amostra)](https://developer.xamarin.com/samples/mac/MacDatabase/)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Vinculação de dados e a codificação de chave-valor](~/mac/app-fundamentals/databinding.md)
- [Controles padrão](~/mac/user-interface/standard-controls.md)
- [Modos de exibição de tabela](~/mac/user-interface/table-view.md)
- [Modos de exibição de estrutura de tópicos](~/mac/user-interface/outline-view.md)
- [Exibições de coleção](~/mac/user-interface/collection-view.md)
- [Guia de programação de codificação de chave-valor](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/KeyValueCoding/index.html)
- [Introdução aos tópicos de programação de associações de Cocoa](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/CocoaBindings/CocoaBindings.html)
- [Introdução à referência de associações do Cocoa](https://developer.apple.com/library/content/documentation/Cocoa/Reference/CocoaBindingsRef/CocoaBindingsRef.html)
- [NSCollectionView](https://developer.apple.com/documentation/appkit/nscollectionview)
- [Diretrizes de Interface humana do macOS](https://developer.apple.com/macos/human-interface-guidelines/overview/themes/)
