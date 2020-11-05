---
title: Armazenar dados em um banco de dados SQLite.NET local
description: Este artigo explica como armazenar dados em um banco de dados SQLite.NET local.
zone_pivot_groups: platform
ms.topic: quickstart
ms.prod: xamarin
ms.assetid: 5BF901BD-FDE8-4B74-B4AB-418E81745A3B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/01/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 54a6dca3b16ed4ac9fb7ab2065f1d8462a3f3da4
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93368109"
---
# <a name="store-data-in-a-local-sqlitenet-database"></a>Armazenar dados em um banco de dados SQLite.NET local

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](/samples/xamarin/xamarin-forms-samples/getstarted-notes-database/)

Neste guia de início rápido, você aprenderá a:

- Use o Gerenciador de Pacotes do NuGet para adicionar um pacote do NuGet a um projeto.
- Armazene dados localmente em um banco de dados SQLite.NET.

O guia de início rápido explica como armazenar dados em um banco de dados SQLite.NET local. O aplicativo final é mostrado abaixo:

Página de entrada de observação [ ![ da página de anotações](database-images/screenshots1-sml.png)](database-images/screenshots1.png#lightbox "Página de Observações") 
 [ ![ ](database-images/screenshots2-sml.png)](database-images/screenshots2.png#lightbox "Página de Entrada de Observação")

## <a name="prerequisites"></a>Pré-requisitos

Você deve concluir com êxito o [início rápido anterior](multi-page.md) antes de tentar este guia de início rápido. Como alternativa, baixe o [exemplo de início rápido anterior](/samples/xamarin/xamarin-forms-samples/getstarted-notes-multipage/) e use-o como o ponto de partida para este guia de início rápido.

::: zone pivot="windows"

## <a name="update-the-app-with-visual-studio"></a>Atualizar um aplicativo com o Visual Studio

1. Inicie o Visual Studio e abra a solução Notes.

2. Na **Gerenciador de Soluções** , selecione o projeto **Notes** , clique com o botão direito do mouse e selecione **Gerenciar Pacotes do NuGet…** :

    ![Adicionar pacotes do NuGet](database-images/vs/add-nuget-packages.png)    

3. No **Gerenciador de Pacotes NuGet** , selecione a guia **Procurar** , pesquise pelo pacote NuGet **sqlite-net-pcl** , selecione-o e clique no botão **Instalar** para adicioná-lo ao projeto:

    ![Adicionar pacote](database-images/vs/add-package.png)

    > [!NOTE]
    > Há diversos pacotes NuGet com nomes semelhantes. O pacote correto tem estes atributos:
    > - **Proprietários:** praeclarum
    > - **Autores:** SQLite-net
    > - **Link do NuGet:** [sqlite-net-pcl](https://www.nuget.org/packages/sqlite-net-pcl/)  
    >
    > Apesar do nome do pacote, este pacote NuGet pode ser usado em projetos do .NET Standard.

    Este pacote será usado para incorporar operações de banco de dados ao aplicativo.

4. No **Gerenciador de Soluções** , no projeto **Notes** , abra **Note.cs** na pasta **Modelos** e substitua o código existente pelo código a seguir:

    ```csharp
    using System;
    using SQLite;

    namespace Notes.Models
    {
        public class Note
        {
            [PrimaryKey, AutoIncrement]
            public int ID { get; set; }
            public string Text { get; set; }
            public DateTime Date { get; set; }
        }
    }
    ```

    Essa classe define um modelo de `Note` que armazenará dados sobre cada observação no aplicativo. A propriedade `ID` é marcada com os atributos `PrimaryKey` e `AutoIncrement` para garantir que cada instância de `Note` no banco de dados SQLite.NET terá uma ID exclusiva fornecida pelo SQLite.NET.

    Salve as alterações em **Note.cs** pressionando **CTRL + S** e feche o arquivo.

    > [!WARNING]
    > Tentar compilar o aplicativo neste ponto resultará em erros que serão corrigidos em etapas posteriores.

5. No **Gerenciador de Soluções** , adicione uma nova pasta chamada **Data** ao projeto **Notes**.

6. No **Gerenciador de Soluções** , no projeto **Notes** , adicione uma nova classe chamada **NoteDatabase** à pasta **Data**.

7. Em **NoteDatabase.cs** , substitua o código existente pelo código a seguir:

    ```csharp
    using System.Collections.Generic;
    using System.Threading.Tasks;
    using SQLite;
    using Notes.Models;

    namespace Notes.Data
    {
        public class NoteDatabase
        {
            readonly SQLiteAsyncConnection _database;

            public NoteDatabase(string dbPath)
            {
                _database = new SQLiteAsyncConnection(dbPath);
                _database.CreateTableAsync<Note>().Wait();
            }

            public Task<List<Note>> GetNotesAsync()
            {
                return _database.Table<Note>().ToListAsync();
            }

            public Task<Note> GetNoteAsync(int id)
            {
                return _database.Table<Note>()
                                .Where(i => i.ID == id)
                                .FirstOrDefaultAsync();
            }

            public Task<int> SaveNoteAsync(Note note)
            {
                if (note.ID != 0)
                {
                    return _database.UpdateAsync(note);
                }
                else
                {
                    return _database.InsertAsync(note);
                }
            }

            public Task<int> DeleteNoteAsync(Note note)
            {
                return _database.DeleteAsync(note);
            }
        }
    }
    ```

    Esta classe contém código para criar o banco de dados e ler, gravar e excluir dados do banco de dados. O código usa APIs SQLite.NET assíncronas que movem operações de banco de dados para threads de segundo plano. Além disso, o construtor `NoteDatabase` usa o caminho para o arquivo de banco de dados como um argumento. Esse caminho será fornecido pela classe `App` na próxima etapa.

    Salve as alterações em **NoteDatabase.cs** pressionando **CTRL + S** e feche o arquivo.

    > [!WARNING]
    > Tentar compilar o aplicativo neste ponto resultará em erros que serão corrigidos em etapas posteriores.

8. No **Gerenciador de Soluções** , no projeto **Notes** , clique duas vezes em **App.xaml.cs** para abri-lo. Em seguida, substitua o código existente pelo código a seguir:

    ```csharp
    using System;
    using System.IO;
    using Xamarin.Forms;
    using Notes.Data;

    namespace Notes
    {
        public partial class App : Application
        {
            static NoteDatabase database;

            public static NoteDatabase Database
            {
                get
                {
                    if (database == null)
                    {
                        database = new NoteDatabase(Path.Combine(Environment.GetFolderPath(Environment.SpecialFolder.LocalApplicationData), "Notes.db3"));
                    }
                    return database;
                }
            }

            public App()
            {
                InitializeComponent();
                MainPage = new NavigationPage(new NotesPage());
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

    Esse código define uma propriedade `Database` que cria uma nova instância de `NoteDatabase` como um singleton, passando o nome de arquivo do banco de dados como o argumento para o construtor `NoteDatabase`. A vantagem de expor o banco de dados como um singleton é que uma conexão de banco de dados individual criada é mantida aberta enquanto o aplicativo é executado, evitando, portanto, o trabalho de abrir e fechar o arquivo de banco de dados cada vez que uma operação de banco de dados é realizada.

    Salve as alterações em **App.xaml.cs** ao pressionar **CTRL + S** e feche o arquivo.

    > [!WARNING]
    > Tentar compilar o aplicativo neste ponto resultará em erros que serão corrigidos em etapas posteriores.

9. No **Gerenciador de Soluções** , no projeto **Notes** , clique duas vezes em **NotesPage.xaml.cs** para abri-lo. Em seguida, substitua o método `OnAppearing` pelo código a seguir:

    ```csharp
    protected override async void OnAppearing()
    {
        base.OnAppearing();

        listView.ItemsSource = await App.Database.GetNotesAsync();
    }
    ```    

    Esse código popula o [`ListView`](xref:Xamarin.Forms.ListView) com todas as anotações armazenadas no banco de dados.

    Salve as alterações em **NotesPage.xaml** pressionando **CTRL+S** e feche o arquivo.

    > [!WARNING]
    > Tentar compilar o aplicativo neste ponto resultará em erros que serão corrigidos em etapas posteriores.

10. No **Gerenciador de Soluções** , clique duas vezes em **NoteEntryPage.xaml.cs** para abri-lo. Em seguida, substitua os métodos `OnSaveButtonClicked` e `OnDeleteButtonClicked` pelo código a seguir:

      ```csharp
      async void OnSaveButtonClicked(object sender, EventArgs e)
      {
          var note = (Note)BindingContext;
          note.Date = DateTime.UtcNow;
          await App.Database.SaveNoteAsync(note);
          await Navigation.PopAsync();
      }

      async void OnDeleteButtonClicked(object sender, EventArgs e)
      {
          var note = (Note)BindingContext;
          await App.Database.DeleteNoteAsync(note);
          await Navigation.PopAsync();
      }
      ```    

      O `NoteEntryPage` armazena uma `Note` instância do, que representa uma única nota, no [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) da página. Quando o manipulador de eventos `OnSaveButtonClicked` é executado, a instância de `Note` é salva no banco de dados e o aplicativo navega de volta para a página anterior. Quando o manipulador de eventos `OnDeleteButtonClicked` é executado, a instância de `Note` é excluída do banco de dados e o aplicativo navega de volta para a página anterior.

      Salve as alterações em **NoteEntryPage.xaml.cs** pressionando **CTRL+S** e feche o arquivo.

11. Compile e execute o projeto em cada plataforma. Para obter mais informações, confira [Como criar o guia de início rápido](single-page.md#building-the-quickstart).

    Na **NotesPage** , pressione o botão **+** para navegar até a **NoteEntryPage** e inserir uma observação. Depois de salvar a observação, o aplicativo navegará de volta para a **NotesPage**.

    Insira diversas observações de comprimento variável para observar o comportamento do aplicativo.

::: zone-end
::: zone pivot="macos"

## <a name="update-the-app-with-visual-studio-for-mac"></a>Atualizar um aplicativo com o Visual Studio para Mac

1. Inicie o Visual Studio para Mac e abra o projeto Notes.

2. No **Painel de Soluções** , selecione o projeto **Observações** , clique com o botão direito do mouse e selecione **Adicionar > Adicionar Pacotes do NuGet…** :

    ![Adicionar pacotes do NuGet](database-images/vsmac/add-nuget-packages.png)    

3. Na janela **Adicionar Pacotes** , pesquise pelo pacote NuGet **sqlite-net-pcl** , selecione-o e clique no botão **Adicionar Pacote** para adicioná-lo ao projeto:

    ![Adicionar pacote](database-images/vsmac/add-package.png)

    > [!NOTE]
    > Há diversos pacotes NuGet com nomes semelhantes. O pacote correto tem estes atributos:
    > - **Proprietários:** praeclarum
    > - **Autores:** SQLite-net
    > - **Link do NuGet:** [sqlite-net-pcl](https://www.nuget.org/packages/sqlite-net-pcl/)  
    >
    > Apesar do nome do pacote, este pacote NuGet pode ser usado em projetos do .NET Standard.

    Este pacote será usado para incorporar operações de banco de dados ao aplicativo.

4. No **Painel de Soluções** , no projeto **Notes** , abra **Note.cs** na pasta **Modelos** e substitua o código existente pelo código a seguir:

    ```csharp
    using System;
    using SQLite;

    namespace Notes.Models
    {
        public class Note
        {
            [PrimaryKey, AutoIncrement]
            public int ID { get; set; }
            public string Text { get; set; }
            public DateTime Date { get; set; }
        }
    }
    ```

    Essa classe define um modelo de `Note` que armazenará dados sobre cada observação no aplicativo. A propriedade `ID` é marcada com os atributos `PrimaryKey` e `AutoIncrement` para garantir que cada instância de `Note` no banco de dados SQLite.NET terá uma ID exclusiva fornecida pelo SQLite.NET.

    Salve as alterações em **Note.cs** ao escolher **Arquivo > Salvar** (ou ao pressionar **& #8984; + S** ) e feche o arquivo.

    > [!WARNING]
    > Tentar compilar o aplicativo neste ponto resultará em erros que serão corrigidos em etapas posteriores.

5. No **Painel de Soluções** , adicione uma nova pasta chamada **Data** ao projeto **Notes**.

6. No **Painel de Soluções** , no projeto **Notes** , adicione uma nova classe chamada **NoteDatabase** à pasta **Data**.

7. Em **NoteDatabase.cs** , substitua o código existente pelo código a seguir:

    ```csharp
    using System.Collections.Generic;
    using System.Threading.Tasks;
    using SQLite;
    using Notes.Models;

    namespace Notes.Data
    {
        public class NoteDatabase
        {
            readonly SQLiteAsyncConnection _database;

            public NoteDatabase(string dbPath)
            {
                _database = new SQLiteAsyncConnection(dbPath);
                _database.CreateTableAsync<Note>().Wait();
            }

            public Task<List<Note>> GetNotesAsync()
            {
                return _database.Table<Note>().ToListAsync();
            }

            public Task<Note> GetNoteAsync(int id)
            {
                return _database.Table<Note>()
                                .Where(i => i.ID == id)
                                .FirstOrDefaultAsync();
            }

            public Task<int> SaveNoteAsync(Note note)
            {
                if (note.ID != 0)
                {
                    return _database.UpdateAsync(note);
                }
                else
                {
                    return _database.InsertAsync(note);
                }
            }

            public Task<int> DeleteNoteAsync(Note note)
            {
                return _database.DeleteAsync(note);
            }
        }
    }
    ```

    Esta classe contém código para criar o banco de dados e ler, gravar e excluir dados do banco de dados. O código usa APIs SQLite.NET assíncronas que movem operações de banco de dados para threads de segundo plano. Além disso, o construtor `NoteDatabase` usa o caminho para o arquivo de banco de dados como um argumento. Esse caminho será fornecido pela classe `App` na próxima etapa.

    Salve as alterações em **NoteDatabase.cs** ao escolher **Arquivo > Salvar** (ou ao pressionar **& #8984; + S** ) e feche o arquivo.

    > [!WARNING]
    > Tentar compilar o aplicativo neste ponto resultará em erros que serão corrigidos em etapas posteriores.

8. No **Painel de Soluções** , no projeto **Notes** , clique duas vezes em **App.xaml.cs** para abri-lo. Em seguida, substitua o código existente pelo código a seguir:

    ```csharp
    using System;
    using System.IO;
    using Xamarin.Forms;
    using Notes.Data;

    namespace Notes
    {
        public partial class App : Application
        {
            static NoteDatabase database;

            public static NoteDatabase Database
            {
                get
                {
                    if (database == null)
                    {
                        database = new NoteDatabase(Path.Combine(Environment.GetFolderPath(Environment.SpecialFolder.LocalApplicationData), "Notes.db3"));
                    }
                    return database;
                }
            }

            public App()
            {
                InitializeComponent();
                MainPage = new NavigationPage(new NotesPage());
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

    Esse código define uma propriedade `Database` que cria uma nova instância de `NoteDatabase` como um singleton, passando o nome de arquivo do banco de dados como o argumento para o construtor `NoteDatabase`. A vantagem de expor o banco de dados como um singleton é que uma conexão de banco de dados individual criada é mantida aberta enquanto o aplicativo é executado, evitando, portanto, o trabalho de abrir e fechar o arquivo de banco de dados cada vez que uma operação de banco de dados é realizada.

    Salve as alterações em **App.xaml.cs** escolhendo **Arquivo > Salvar** (ou pressionando **& #8984; + S** ) e feche o arquivo.

    > [!WARNING]
    > Tentar compilar o aplicativo neste ponto resultará em erros que serão corrigidos em etapas posteriores.

9. No **Painel de Soluções** , no projeto **Notes** , clique duas vezes em **NotesPage.xaml.cs** para abri-lo. Em seguida, substitua o método `OnAppearing` pelo código a seguir:

    ```csharp
    protected override async void OnAppearing()
    {
        base.OnAppearing();

        listView.ItemsSource = await App.Database.GetNotesAsync();
    }
    ```    

    Esse código popula o [`ListView`](xref:Xamarin.Forms.ListView) com todas as anotações armazenadas no banco de dados.

    Salve as alterações em **NotesPage.xaml.cs** ao escolher **Arquivo > Salvar** (ou ao pressionar **& #8984; + S** ) e feche o arquivo.

    > [!WARNING]
    > Tentar compilar o aplicativo neste ponto resultará em erros que serão corrigidos em etapas posteriores.

10. No **Painel de Soluções** , clique duas vezes em **NoteEntryPage.xaml.cs** para abri-lo. Em seguida, substitua os métodos `OnSaveButtonClicked` e `OnDeleteButtonClicked` pelo código a seguir:

      ```csharp
      async void OnSaveButtonClicked(object sender, EventArgs e)
      {
          var note = (Note)BindingContext;
          note.Date = DateTime.UtcNow;
          await App.Database.SaveNoteAsync(note);
          await Navigation.PopAsync();
      }

      async void OnDeleteButtonClicked(object sender, EventArgs e)
      {
          var note = (Note)BindingContext;
          await App.Database.DeleteNoteAsync(note);
          await Navigation.PopAsync();
      }
      ```    

      O `NoteEntryPage` armazena uma `Note` instância do, que representa uma única nota, no [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) da página. Quando o manipulador de eventos `OnSaveButtonClicked` é executado, a instância de `Note` é salva no banco de dados e o aplicativo navega de volta para a página anterior. Quando o manipulador de eventos `OnDeleteButtonClicked` é executado, a instância de `Note` é excluída do banco de dados e o aplicativo navega de volta para a página anterior.

      Salve as alterações em **NoteEntryPage.xaml.cs** escolhendo **Arquivo > Salvar** (ou pressionando **&#8984; + S** ) e feche o arquivo.

11. Compile e execute o projeto em cada plataforma. Para obter mais informações, confira [Como criar o guia de início rápido](single-page.md#building-the-quickstart).

    Na **NotesPage** , pressione o botão **+** para navegar até a **NoteEntryPage** e inserir uma observação. Depois de salvar a observação, o aplicativo navegará de volta para a **NotesPage**.

    Insira diversas observações de comprimento variável para observar o comportamento do aplicativo.

::: zone-end

## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você aprendeu a:

- Use o Gerenciador de Pacotes do NuGet para adicionar um pacote do NuGet a um projeto.
- Armazene dados localmente em um banco de dados SQLite.NET.

Para definir o estilo do aplicativo com estilos XAML, vá para o próximo início rápido.

> [!div class="nextstepaction"]
> [Próximo](styling.md)

## <a name="related-links"></a>Links relacionados

- [Notas (exemplo)](/samples/xamarin/xamarin-forms-samples/getstarted-notes-database/)
- [Xamarin.Forms Aprofundamento do guia de início rápido](deepdive.md)