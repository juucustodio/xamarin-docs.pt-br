---
title: Armazenar dados em um banco de dados SQLite.NET local
description: Este artigo explica como armazenar dados em um SQLite.NET local de um Xamarin.Forms aplicativo de Shell.
zone_pivot_groups: platform
ms.topic: quickstart
ms.prod: xamarin
ms.assetid: F669CE4E-1E1B-409F-BC1C-8364633EB7EF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.custom: contperf-fy21q3
ms.date: 01/28/2021
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: cfc479e6211692f3de7d78e2fd52cd80a2630660
ms.sourcegitcommit: 1f391667869a4541dd9b42d78862dc01d69ed160
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/08/2021
ms.locfileid: "99818316"
---
# <a name="store-data-in-a-local-sqlitenet-database"></a>Armazenar dados em um banco de dados SQLite.NET local

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](/samples/xamarin/xamarin-forms-samples/getstarted-notes-database/)

Neste guia de início rápido, você aprenderá a:

- Armazene dados localmente em um banco de dados SQLite.NET.

O guia de início rápido explica como armazenar dados em um SQLite.NET local de um Xamarin.Forms aplicativo de Shell. O aplicativo final é mostrado abaixo:

Página de entrada de observação [ ![ da página de anotações](database-images/screenshots1-sml.png)](database-images/screenshots1.png#lightbox) 
 [ ![ ](database-images/screenshots2-sml.png)](database-images/screenshots2.png#lightbox)

## <a name="prerequisites"></a>Pré-requisitos

Você deve concluir com êxito o [início rápido anterior](navigation.md) antes de tentar este guia de início rápido. Como alternativa, baixe o [exemplo de início rápido anterior](/samples/xamarin/xamarin-forms-samples/getstarted-notes-navigation/) e use-o como o ponto de partida para este guia de início rápido.

::: zone pivot="windows"

## <a name="update-the-app-with-visual-studio"></a>Atualizar um aplicativo com o Visual Studio

1. Inicie o Visual Studio e abra a solução Notes.

2. Em **Gerenciador de soluções**, clique com o botão direito do mouse na solução **observações** e selecione **gerenciar pacotes NuGet para a solução...**:

    ![Gerenciar Pacotes NuGet](database-images/vs/manage-nuget-packages.png)    

3. No **Gerenciador de pacotes NuGet**, selecione a guia **procurar** e procure o pacote NuGet do **SQLite-net-PCL** .

    > [!WARNING]
    > Há muitos pacotes NuGet com nomes semelhantes. O pacote correto tem estes atributos:
    > - **Autores:** SQLite-net
    > - **Link do NuGet:** [sqlite-net-pcl](https://www.nuget.org/packages/sqlite-net-pcl/)  
    >
    > Apesar do nome do pacote, este pacote NuGet pode ser usado em projetos do .NET Standard.

    No **Gerenciador de pacotes NuGet**, selecione o pacote **SQLite-net-PCL** correto, marque a caixa de seleção **projeto** e clique no botão **instalar** para adicioná-lo à solução:

    ![Selecione SQLite-net-PCL](database-images/vs/select-package.png)

    Este pacote será usado para incorporar operações de banco de dados ao aplicativo e será adicionado a todos os projetos na solução.

    Feche o **Gerenciador de pacotes NuGet**.

4. No **Gerenciador de Soluções**, no projeto **Notes**, abra **Note.cs** na pasta **Modelos** e substitua o código existente pelo código a seguir:

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

    Salve as alterações em **note.cs** pressionando **Ctrl + S**.

    > [!WARNING]
    > O aplicativo não será compilado no momento devido a erros que serão corrigidos nas etapas subsequentes.

5. No **Gerenciador de Soluções**, adicione uma nova pasta chamada **Data** ao projeto **Notes**.

6. No **Gerenciador de Soluções**, no projeto **Notes**, adicione uma nova classe chamada **NoteDatabase** à pasta **Data**.

7. Em **NoteDatabase.cs**, substitua o código existente pelo código a seguir:

    ```csharp
    using System.Collections.Generic;
    using System.Threading.Tasks;
    using SQLite;
    using Notes.Models;

    namespace Notes.Data
    {
        public class NoteDatabase
        {
            readonly SQLiteAsyncConnection database;

            public NoteDatabase(string dbPath)
            {
                database = new SQLiteAsyncConnection(dbPath);
                database.CreateTableAsync<Note>().Wait();
            }

            public Task<List<Note>> GetNotesAsync()
            {
                //Get all notes.
                return database.Table<Note>().ToListAsync();
            }

            public Task<Note> GetNoteAsync(int id)
            {
                // Get a specific note.
                return database.Table<Note>()
                                .Where(i => i.ID == id)
                                .FirstOrDefaultAsync();
            }

            public Task<int> SaveNoteAsync(Note note)
            {
                if (note.ID != 0)
                {
                    // Update an existing note.
                    return database.UpdateAsync(note);
                }
                else
                {
                    // Save a new note.
                    return database.InsertAsync(note);
                }
            }

            public Task<int> DeleteNoteAsync(Note note)
            {
                // Delete a note.
                return database.DeleteAsync(note);
            }
        }
    }
    ```

    Esta classe contém código para criar o banco de dados e ler, gravar e excluir dados do banco de dados. O código usa APIs SQLite.NET assíncronas que movem operações de banco de dados para threads de segundo plano. Além disso, o construtor `NoteDatabase` usa o caminho para o arquivo de banco de dados como um argumento. Esse caminho será fornecido pela classe `App` na próxima etapa.

    Salve as alterações em **NoteDatabase.cs** pressionando **Ctrl + S**.  

    > [!WARNING]
    > O aplicativo não será compilado no momento devido a erros que serão corrigidos nas etapas subsequentes.

8. No **Gerenciador de soluções**, no projeto do **Notes** , expanda **app. XAML** e clique duas vezes em **app.XAML.cs** para abri-lo. Em seguida, substitua o código existente pelo código a seguir:

    ```csharp
    using System;
    using System.IO;
    using Notes.Data;
    using Xamarin.Forms;

    namespace Notes
    {
        public partial class App : Application
        {
            static NoteDatabase database;

            // Create the database connection as a singleton.
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
                MainPage = new AppShell();
            }

            protected override void OnStart()
            {
            }

            protected override void OnSleep()
            {
            }

            protected override void OnResume()
            {
            }
        }
    }
    ```

    Esse código define uma propriedade `Database` que cria uma nova instância de `NoteDatabase` como um singleton, passando o nome de arquivo do banco de dados como o argumento para o construtor `NoteDatabase`. A vantagem de expor o banco de dados como um singleton é que uma conexão de banco de dados individual criada é mantida aberta enquanto o aplicativo é executado, evitando, portanto, o trabalho de abrir e fechar o arquivo de banco de dados cada vez que uma operação de banco de dados é realizada.

    Salve as alterações em **app.XAML.cs** pressionando **Ctrl + S**.  

    > [!WARNING]
    > O aplicativo não será compilado no momento devido a erros que serão corrigidos nas etapas subsequentes.

9. No **Gerenciador de soluções**, no projeto **do Notes** , expanda **NotesPage. XAML** na pasta **views** e abra **NotesPage.XAML.cs**. Em seguida, substitua os métodos `OnAppearing` e `OnSelectionChanged` pelo código a seguir:

    ```csharp
    protected override async void OnAppearing()
    {
        base.OnAppearing();

        // Retrieve all the notes from the database, and set them as the
        // data source for the CollectionView.
        collectionView.ItemsSource = await App.Database.GetNotesAsync();
    }

    async void OnSelectionChanged(object sender, SelectionChangedEventArgs e)
    {
        if (e.CurrentSelection != null)
        {
            // Navigate to the NoteEntryPage, passing the ID as a query parameter.
            Note note = (Note)e.CurrentSelection.FirstOrDefault();
            await Shell.Current.GoToAsync($"{nameof(NoteEntryPage)}?{nameof(NoteEntryPage.ItemId)}={note.ID.ToString()}");
        }
    }    
    ```    

    O `OnAppearing` método popula o [`CollectionView`](xref:Xamarin.Forms.CollectionView) com todas as anotações armazenadas no banco de dados. O `OnSelectionChanged` método navega para o `NoteEntryPage` , passando a `ID` Propriedade do `Note` objeto selecionado como um parâmetro de consulta.

    Salve as alterações em **NotesPage.XAML.cs** pressionando **Ctrl + S**.  

    > [!WARNING]
    > O aplicativo não será compilado no momento devido a erros que serão corrigidos nas etapas subsequentes.

10. Em **Gerenciador de soluções**, expanda **NoteEntryPage. XAML** na pasta **views** e abra **NoteEntryPage.XAML.cs**. Em seguida, substitua os `LoadNote` `OnSaveButtonClicked` métodos, e `OnDeleteButtonClicked` pelo seguinte código:

      ```csharp
      async void LoadNote(string itemId)
      {
          try
          {
              int id = Convert.ToInt32(itemId);
              // Retrieve the note and set it as the BindingContext of the page.
              Note note = await App.Database.GetNoteAsync(id);
              BindingContext = note;
          }
          catch (Exception)
          {
              Console.WriteLine("Failed to load note.");
          }
      }

      async void OnSaveButtonClicked(object sender, EventArgs e)
      {
          var note = (Note)BindingContext;
          note.Date = DateTime.UtcNow;
          if (!string.IsNullOrWhiteSpace(note.Text))
          {
              await App.Database.SaveNoteAsync(note);
          }

          // Navigate backwards
          await Shell.Current.GoToAsync("..");
      }

      async void OnDeleteButtonClicked(object sender, EventArgs e)
      {
          var note = (Note)BindingContext;
          await App.Database.DeleteNoteAsync(note);

          // Navigate backwards
          await Shell.Current.GoToAsync("..");
      }
      ```    

      O `NoteEntryPage` usa o `LoadNote` método para recuperar a observação do banco de dados, cuja ID foi passada como um parâmetro de consulta para a página e a armazena como um `Note` objeto no [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) da página. Quando o manipulador de eventos `OnSaveButtonClicked` é executado, a instância de `Note` é salva no banco de dados e o aplicativo navega de volta para a página anterior. Quando o manipulador de eventos `OnDeleteButtonClicked` é executado, a instância de `Note` é excluída do banco de dados e o aplicativo navega de volta para a página anterior.

      Salve as alterações em **NoteEntryPage.XAML.cs** pressionando **Ctrl + S**.  

11. Compile e execute o projeto em cada plataforma. Para obter mais informações, confira [Como criar o guia de início rápido](app.md#building-the-quickstart).

    No **NotesPage** , pressione o botão **Adicionar** para navegar até o **NoteEntryPage** e insira uma observação. Depois de salvar a observação, o aplicativo navegará de volta para a **NotesPage**.

    Insira várias observações, de comprimento variável, para observar o comportamento do aplicativo. Feche o aplicativo e inicie-o novamente para garantir que as anotações inseridas foram salvas no banco de dados.

::: zone-end
::: zone pivot="macos"

## <a name="update-the-app-with-visual-studio-for-mac"></a>Atualizar um aplicativo com o Visual Studio para Mac

1. Inicie o Visual Studio para Mac e abra a solução notas.

2. Na **painel de soluções**, clique com o botão direito do mouse na solução **notas** e selecione **gerenciar pacotes NuGet...**:

    ![Gerenciar Pacotes NuGet](database-images/vsmac/manage-nuget-packages.png)    

3. Na caixa de diálogo **gerenciar pacotes NuGet** , selecione a guia **procurar** e procure o pacote NuGet do **SQLite-net-PCL** .

    > [!WARNING]
    > Há muitos pacotes NuGet com nomes semelhantes. O pacote correto tem estes atributos:
    > - **Autores:** SQLite-net
    > - **Link do NuGet:** [sqlite-net-pcl](https://www.nuget.org/packages/sqlite-net-pcl/)  
    >
    > Apesar do nome do pacote, este pacote NuGet pode ser usado em projetos do .NET Standard.

    Na caixa de diálogo **gerenciar pacotes NuGet** , selecione o pacote **SQLite-net-PCL** e clique no botão **Adicionar pacote** para adicioná-lo à solução:

      ![Selecione SQLite-net-PCL](database-images/vsmac/select-package.png)

    Este pacote será usado para incorporar operações de banco de dados ao aplicativo.

4. Na caixa de diálogo **selecionar projetos** , verifique se todas as caixas de seleção estão marcadas e pressione o botão **OK** :

    ![Adicionar pacote a todos os projetos](database-images/vsmac/add-package.png)

    Isso adicionará o pacote NuGet a todos os projetos na solução.

5. No **Painel de Soluções**, no projeto **Notes**, abra **Note.cs** na pasta **Modelos** e substitua o código existente pelo código a seguir:

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

    Salve as alterações no **note.cs** escolhendo **arquivo > salvar** (ou pressionando **&#8984; + S**).

    > [!WARNING]
    > O aplicativo não será compilado no momento devido a erros que serão corrigidos nas etapas subsequentes.

6. No **Painel de Soluções**, adicione uma nova pasta chamada **Data** ao projeto **Notes**.

7. No **Painel de Soluções**, no projeto **Notes**, adicione uma nova classe chamada **NoteDatabase** à pasta **Data**.

8. Em **NoteDatabase.cs**, substitua o código existente pelo código a seguir:

    ```csharp
    using System.Collections.Generic;
    using System.Threading.Tasks;
    using SQLite;
    using Notes.Models;

    namespace Notes.Data
    {
        public class NoteDatabase
        {
            readonly SQLiteAsyncConnection database;

            public NoteDatabase(string dbPath)
            {
                database = new SQLiteAsyncConnection(dbPath);
                database.CreateTableAsync<Note>().Wait();
            }

            public Task<List<Note>> GetNotesAsync()
            {
                //Get all notes.
                return database.Table<Note>().ToListAsync();
            }

            public Task<Note> GetNoteAsync(int id)
            {
                // Get a specific note.
                return database.Table<Note>()
                                .Where(i => i.ID == id)
                                .FirstOrDefaultAsync();
            }

            public Task<int> SaveNoteAsync(Note note)
            {
                if (note.ID != 0)
                {
                    // Update an existing note.
                    return database.UpdateAsync(note);
                }
                else
                {
                    // Save a new note.
                    return database.InsertAsync(note);
                }
            }

            public Task<int> DeleteNoteAsync(Note note)
            {
                // Delete a note.
                return database.DeleteAsync(note);
            }
        }
    }
    ```

    Esta classe contém código para criar o banco de dados e ler, gravar e excluir dados do banco de dados. O código usa APIs SQLite.NET assíncronas que movem operações de banco de dados para threads de segundo plano. Além disso, o construtor `NoteDatabase` usa o caminho para o arquivo de banco de dados como um argumento. Esse caminho será fornecido pela classe `App` na próxima etapa.

    Salve as alterações no **NoteDatabase.cs** escolhendo **arquivo > salvar** (ou pressionando **&#8984; + S**).

    > [!WARNING]
    > O aplicativo não será compilado no momento devido a erros que serão corrigidos nas etapas subsequentes.

9. No **painel de soluções**, no projeto do **Notes** , expanda **app. XAML** e clique duas vezes em **app.XAML.cs** para abri-lo. Em seguida, substitua o código existente pelo código a seguir:

    ```csharp
    using System;
    using System.IO;
    using Notes.Data;
    using Xamarin.Forms;

    namespace Notes
    {
        public partial class App : Application
        {
            static NoteDatabase database;

            // Create the database connection as a singleton.
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
                MainPage = new AppShell();
            }

            protected override void OnStart()
            {
            }

            protected override void OnSleep()
            {
            }

            protected override void OnResume()
            {
            }
        }
    }
    ```

    Esse código define uma propriedade `Database` que cria uma nova instância de `NoteDatabase` como um singleton, passando o nome de arquivo do banco de dados como o argumento para o construtor `NoteDatabase`. A vantagem de expor o banco de dados como um singleton é que uma conexão de banco de dados individual criada é mantida aberta enquanto o aplicativo é executado, evitando, portanto, o trabalho de abrir e fechar o arquivo de banco de dados cada vez que uma operação de banco de dados é realizada.

    Salve as alterações no **app.XAML.cs** escolhendo **arquivo > salvar** (ou pressionando **&#8984; + S**).

    > [!WARNING]
    > O aplicativo não será compilado no momento devido a erros que serão corrigidos nas etapas subsequentes.

10. No **painel de soluções**, no projeto do **Notes** , expanda **NotesPage. XAML** na pasta **views** e abra **NotesPage.XAML.cs**. Em seguida, substitua os métodos `OnAppearing` e `OnSelectionChanged` pelo código a seguir:

    ```csharp
    protected override async void OnAppearing()
    {
        base.OnAppearing();

        // Retrieve all the notes from the database, and set them as the
        // data source for the CollectionView.
        collectionView.ItemsSource = await App.Database.GetNotesAsync();
    }

    async void OnSelectionChanged(object sender, SelectionChangedEventArgs e)
    {
        if (e.CurrentSelection != null)
        {
            // Navigate to the NoteEntryPage, passing the ID as a query parameter.
            Note note = (Note)e.CurrentSelection.FirstOrDefault();
            await Shell.Current.GoToAsync($"{nameof(NoteEntryPage)}?{nameof(NoteEntryPage.ItemId)}={note.ID.ToString()}");
        }
    }    
    ```    

    O `OnAppearing` método popula o [`CollectionView`](xref:Xamarin.Forms.CollectionView) com todas as anotações armazenadas no banco de dados. O `OnSelectionChanged` método navega para o `NoteEntryPage` , passando a `ID` Propriedade do `Note` objeto selecionado como um parâmetro de consulta.

    Salve as alterações no **NotesPage.XAML.cs** escolhendo **arquivo > salvar** (ou pressionando **&#8984; + S**).

    > [!WARNING]
    > O aplicativo não será compilado no momento devido a erros que serão corrigidos nas etapas subsequentes.

11. No **painel de soluções**, expanda **NoteEntryPage. XAML** na pasta **views** e abra **NoteEntryPage.XAML.cs**. Em seguida, substitua os `LoadNote` `OnSaveButtonClicked` métodos, e `OnDeleteButtonClicked` pelo seguinte código:

      ```csharp
      async void LoadNote(string itemId)
      {
          try
          {
              int id = Convert.ToInt32(itemId);
              // Retrieve the note and set it as the BindingContext of the page.
              Note note = await App.Database.GetNoteAsync(id);
              BindingContext = note;
          }
          catch (Exception)
          {
              Console.WriteLine("Failed to load note.");
          }
      }

      async void OnSaveButtonClicked(object sender, EventArgs e)
      {
          var note = (Note)BindingContext;
          note.Date = DateTime.UtcNow;
          if (!string.IsNullOrWhiteSpace(note.Text))
          {
              await App.Database.SaveNoteAsync(note);
          }

          // Navigate backwards
          await Shell.Current.GoToAsync("..");
      }

      async void OnDeleteButtonClicked(object sender, EventArgs e)
      {
          var note = (Note)BindingContext;
          await App.Database.DeleteNoteAsync(note);

          // Navigate backwards
          await Shell.Current.GoToAsync("..");
      }
      ```    

      O `NoteEntryPage` usa o `LoadNote` método para recuperar a observação do banco de dados, cuja ID foi passada como um parâmetro de consulta para a página e a armazena como um `Note` objeto no [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) da página. Quando o manipulador de eventos `OnSaveButtonClicked` é executado, a instância de `Note` é salva no banco de dados e o aplicativo navega de volta para a página anterior. Quando o manipulador de eventos `OnDeleteButtonClicked` é executado, a instância de `Note` é excluída do banco de dados e o aplicativo navega de volta para a página anterior.

      Salve as alterações no **NoteEntryPage.XAML.cs** escolhendo **arquivo > salvar** (ou pressionando **&#8984; + S**).

12. Compile e execute o projeto em cada plataforma. Para obter mais informações, confira [Como criar o guia de início rápido](app.md#building-the-quickstart).

    No **NotesPage** , pressione o botão **Adicionar** para navegar até o **NoteEntryPage** e insira uma observação. Depois de salvar a observação, o aplicativo navegará de volta para a **NotesPage**.

    Insira várias observações, de comprimento variável, para observar o comportamento do aplicativo. Feche o aplicativo e inicie-o novamente para garantir que as anotações inseridas foram salvas no banco de dados.

::: zone-end

## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você aprendeu a:

- Armazene dados localmente em um banco de dados SQLite.NET.

Continue para o próximo início rápido para estilizar o aplicativo com estilos XAML.

> [!div class="nextstepaction"]
> [Próximo](styling.md)

## <a name="related-links"></a>Links relacionados

- [Notas (exemplo)](/samples/xamarin/xamarin-forms-samples/getstarted-notes-database/)
- [Xamarin.Forms Aprofundamento do início rápido do Shell](deepdive.md)
