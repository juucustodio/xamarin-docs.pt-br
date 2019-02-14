---
title: Data Store em um banco de dados local SQLite.NET
description: Este artigo explica como armazenar dados em um banco de dados local do SQLite.NET.
zone_pivot_groups: platform
ms.topic: quickstart
ms.prod: xamarin
ms.assetid: 5BF901BD-FDE8-4B74-B4AB-418E81745A3B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/02/2019
ms.openlocfilehash: 3cea41aa3c021dbb03f851a4deb443ee86fcad25
ms.sourcegitcommit: 817d26585093cd180a36b28179eb354b0eb900b3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55292714"
---
# <a name="store-data-in-a-local-sqlitenet-database"></a>Store dados em um banco de dados Local SQLite.NET

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://developer.xamarin.com/samples/xamarin-forms/GetStarted/Notes/Database/)

Neste início rápido, você aprenderá como:

- Use o Gerenciador de pacotes do NuGet para adicionar um pacote do NuGet a um projeto.
- Store dados localmente em um banco de dados SQLite.NET.

O guia de início rápido orienta como armazenar dados em um banco de dados local do SQLite.NET. O aplicativo final é mostrado abaixo:

[![](database-images/screenshots1-sml.png "Página de anotações")](database-images/screenshots1.png#lightbox "anotações")
[![](database-images/screenshots2-sml.png "Observe a página de entrada")](database-images/screenshots2.png#lightbox "Observação Página de entrada")

### <a name="prerequisites"></a>Pré-requisitos

Você deve concluir com êxito a [guia de início rápido anterior](multi-page.md) antes de tentar este início rápido. Como alternativa, baixe o [exemplo de início rápido anterior](https://developer.xamarin.com/samples/xamarin-forms/GetStarted/Notes/MultiPage/) e usá-lo como ponto de partida para este início rápido.

::: zone pivot="windows"

## <a name="update-the-app-with-visual-studio"></a>Atualizar um aplicativo com o Visual Studio

1. Inicie o Visual Studio e abra a solução de notas.

2. Na **Gerenciador de soluções**, selecione o **notas** do projeto, clique com botão direito e selecione **gerenciar pacotes NuGet...** :

    ![](database-images/vs/add-nuget-packages.png "Adicionar Pacotes NuGet")    

3. No **Gerenciador de pacotes NuGet**, selecione o **procurar** guia, procure o **sqlite-net-pcl** pacote do NuGet, selecione-o e clique no **instalar**botão para adicioná-lo ao projeto:

    ![](database-images/vs/add-package.png "Adicionar pacote")

    > [!NOTE]
    > Há um número de pacotes do NuGet com nomes semelhantes. O pacote correto possui estes atributos:
    > - **Autores:** Frank A. Krueger
    > - **Id:** sqlite-net-pcl
    > - **Link do NuGet:** [sqlite-net-pcl](https://www.nuget.org/packages/sqlite-net-pcl/)  
    >
    > Apesar do nome do pacote, esse pacote NuGet pode ser usado em projetos do .NET Standard.

    Este pacote será usado para incorporar operações de banco de dados no aplicativo.

4. Na **Gerenciador de soluções**, no **notas** projeto, abra **Note.cs** no **modelos** pasta e substitua o existente de código com o o código a seguir:

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

    Essa classe define um `Note` modelo que armazenará os dados sobre cada Observação no aplicativo. O `ID` propriedade é marcada com `PrimaryKey` e `AutoIncrement` atributos para garantir que cada `Note` instância no banco de dados SQLite.NET terá uma id exclusiva fornecida pelo SQLite.NET.

    Salve as alterações em **Note.cs** pressionando **CTRL + S**e feche o arquivo.

    > [!WARNING]
    > A tentativa de compilar o aplicativo neste ponto resultará em erros que serão corrigidos em etapas subsequentes.

5. Na **Gerenciador de soluções**, adicione uma nova pasta chamada **dados** para o **notas** projeto.

6. Na **Gerenciador de soluções**, no **notas** do projeto, adicione uma nova classe denominada **NoteDatabase** para o **dados** pasta.

7. Na **NoteDatabase.cs**, substitua o código existente pelo código a seguir:

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

    Essa classe contém código para criar o banco de dados, ler dados a partir dele, gravação de dados e exclua dados dele. O código usa SQLite.NET APIs assíncronas que mover as operações de banco de dados para threads de segundo plano. Além disso, o `NoteDatabase` construtor usa o caminho do arquivo de banco de dados como um argumento. Esse caminho será fornecido pelo `App` classe na próxima etapa.

    Salve as alterações em **NoteDatabase.cs** pressionando **CTRL + S**e feche o arquivo.

    > [!WARNING]
    > A tentativa de compilar o aplicativo neste ponto resultará em erros que serão corrigidos em etapas subsequentes.

8. Na **Gerenciador de soluções**, no **notas** do projeto, clique duas vezes em **App.xaml.cs** para abri-lo. Em seguida, substitua o código existente pelo código a seguir:

    ```csharp
    using System;
    using System.IO;
    using Xamarin.Forms;
    using Xamarin.Forms.Xaml;
    using Notes.Data;

    [assembly: XamlCompilation(XamlCompilationOptions.Compile)]
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
            ...
        }
    }
    ```

    Esse código define uma `Database` que cria uma nova propriedade `NoteDatabase` instância como um singleton, passando o nome do arquivo do banco de dados como o argumento para o `NoteDatabase` construtor. A vantagem de expor o banco de dados como um singleton é que uma conexão de banco de dados individual criada é mantida aberta enquanto o aplicativo é executado, evitando, portanto, o trabalho de abrir e fechar o arquivo de banco de dados cada vez que uma operação de banco de dados é realizada.

    Salve as alterações em **App.xaml.cs** ao pressionar **CTRL + S** e feche o arquivo.

    > [!WARNING]
    > A tentativa de compilar o aplicativo neste ponto resultará em erros que serão corrigidos em etapas subsequentes.

9. Na **Gerenciador de soluções**, no **notas** do projeto, clique duas vezes em **NotesPage.xaml.cs** para abri-lo. Em seguida, substitua o `OnAppearing` método com o código a seguir:

    ```csharp
    protected override async void OnAppearing()
    {
        base.OnAppearing();

        listView.ItemsSource = await App.Database.GetNotesAsync();
    }
    ```    

    Esse código preenche a [ `ListView` ](xref:Xamarin.Forms.ListView) com as observações armazenadas no banco de dados.

    Salve as alterações em **NotesPage.xaml.cs** pressionando **CTRL + S**e feche o arquivo.

    > [!WARNING]
    > A tentativa de compilar o aplicativo neste ponto resultará em erros que serão corrigidos em etapas subsequentes.

10. Na **Gerenciador de soluções**, clique duas vezes em **NoteEntryPage.xaml.cs** para abri-lo. Em seguida, substitua os `OnSaveButtonClicked` e `OnDeleteButtonClicked` métodos com o código a seguir:

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

      O `NoteEntryPage` armazena um `Note` instância, que representa uma anotação única, nas [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext) da página. Quando o `OnSaveButtonClicked` manipulador de eventos é executado, o `Note` instância é salva no banco de dados e o aplicativo navega de volta à página anterior. Quando o `OnDeleteButtonClicked` manipulador de eventos é executado, o `Note` instância é excluída do banco de dados e o aplicativo navega de volta à página anterior.

      Salve as alterações em **NoteEntryPage.xaml.cs** pressionando **CTRL + S**e feche o arquivo.

11. Compile e execute o projeto em cada plataforma. Para obter mais informações, consulte [criando o guia de início rápido](single-page.md#building-the-quickstart).

    Sobre o **NotesPage** pressione a **+** botão para navegar para o **NoteEntryPage** e insira uma observação. Depois de salvar a nota o aplicativo será navegue de volta para o **NotesPage**.

    Insira um número de observações, de comprimento variado, para observar o comportamento do aplicativo.

::: zone-end
::: zone pivot="macos"

## <a name="update-the-app-with-visual-studio-for-mac"></a>Atualizar um aplicativo com o Visual Studio para Mac

1. Inicie o Visual Studio para Mac e abra o projeto de notas.

2. No **painel de soluções**, selecione o **notas** do projeto, clique com botão direito e selecione **Adicionar > Adicionar pacotes NuGet...** :

    ![](database-images/vsmac/add-nuget-packages.png "Adicionar Pacotes NuGet")    

3. No **adicionar pacotes** janela, procure o **sqlite-net-pcl** pacote do NuGet, selecione-o e clique no **Adicionar pacote** botão para adicioná-lo ao projeto:

    ![](database-images/vsmac/add-package.png "Adicionar pacote")

    > [!NOTE]
    > Há um número de pacotes do NuGet com nomes semelhantes. O pacote correto possui estes atributos:
    > - **Autor:** Frank A. Krueger
    > - **Id:** sqlite-net-pcl
    > - **Link do NuGet:** [sqlite-net-pcl](https://www.nuget.org/packages/sqlite-net-pcl/)  
    >
    > Apesar do nome do pacote, esse pacote NuGet pode ser usado em projetos do .NET Standard.

    Este pacote será usado para incorporar operações de banco de dados no aplicativo.

4. No **painel de soluções**, no **notas** projeto, abra **Note.cs** no **modelos** pasta e substitua o código existente pelo seguinte código:

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

    Essa classe define um `Note` modelo que armazenará os dados sobre cada Observação no aplicativo. O `ID` propriedade é marcada com `PrimaryKey` e `AutoIncrement` atributos para garantir que cada `Note` instância no banco de dados SQLite.NET terá uma id exclusiva fornecida pelo SQLite.NET.

    Salve as alterações em **Note.cs** escolhendo **arquivo > Salvar** (ou pressionando  **&#8984; + S**) e feche o arquivo.

    > [!WARNING]
    > A tentativa de compilar o aplicativo neste ponto resultará em erros que serão corrigidos em etapas subsequentes.

5. No **painel de soluções**, adicione uma nova pasta chamada **dados** para o **notas** projeto.

6. No **painel de soluções**, no **notas** do projeto, adicione uma nova classe denominada **NoteDatabase** para o **dados** pasta.

7. Na **NoteDatabase.cs**, substitua o código existente pelo código a seguir:

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

    Essa classe contém código para criar o banco de dados, ler dados a partir dele, gravação de dados e exclua dados dele. O código usa SQLite.NET APIs assíncronas que mover as operações de banco de dados para threads de segundo plano. Além disso, o `NoteDatabase` construtor usa o caminho do arquivo de banco de dados como um argumento. Esse caminho será fornecido pelo `App` classe na próxima etapa.

    Salve as alterações em **NoteDatabase.cs** escolhendo **arquivo > Salvar** (ou pressionando  **&#8984; + S**) e feche o arquivo.

    > [!WARNING]
    > A tentativa de compilar o aplicativo neste ponto resultará em erros que serão corrigidos em etapas subsequentes.

8. No **painel de soluções**, no **notas** do projeto, clique duas vezes em **App.xaml.cs** para abri-lo. Em seguida, substitua o código existente pelo código a seguir:

    ```csharp
    using System;
    using System.IO;
    using Xamarin.Forms;
    using Xamarin.Forms.Xaml;
    using Notes.Data;

    [assembly: XamlCompilation(XamlCompilationOptions.Compile)]
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
            ...
        }
    }
    ```

    Esse código define uma `Database` que cria uma nova propriedade `NoteDatabase` instância como um singleton, passando o nome do arquivo do banco de dados como o argumento para o `NoteDatabase` construtor. A vantagem de expor o banco de dados como um singleton é que uma conexão de banco de dados individual criada é mantida aberta enquanto o aplicativo é executado, evitando, portanto, o trabalho de abrir e fechar o arquivo de banco de dados cada vez que uma operação de banco de dados é realizada.

    Salve as alterações em **App.xaml.cs** escolhendo **Arquivo > Salvar** (ou pressionando **&#8984; + S**) e feche o arquivo.

    > [!WARNING]
    > A tentativa de compilar o aplicativo neste ponto resultará em erros que serão corrigidos em etapas subsequentes.

9. No **painel de soluções**, no **notas** do projeto, clique duas vezes em **NotesPage.xaml.cs** para abri-lo. Em seguida, substitua o `OnAppearing` método com o código a seguir:

    ```csharp
    protected override async void OnAppearing()
    {
        base.OnAppearing();

        listView.ItemsSource = await App.Database.GetNotesAsync();
    }
    ```    

    Esse código preenche a [ `ListView` ](xref:Xamarin.Forms.ListView) com as observações armazenadas no banco de dados.

    Salve as alterações em **NotesPage.xaml.cs** escolhendo **arquivo > Salvar** (ou pressionando  **&#8984; + S**) e feche o arquivo.

    > [!WARNING]
    > A tentativa de compilar o aplicativo neste ponto resultará em erros que serão corrigidos em etapas subsequentes.

10. No **painel de soluções**, clique duas vezes em **NoteEntryPage.xaml.cs** para abri-lo. Em seguida, substitua os `OnSaveButtonClicked` e `OnDeleteButtonClicked` métodos com o código a seguir:

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

      O `NoteEntryPage` armazena um `Note` instância, que representa uma anotação única, nas [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext) da página. Quando o `OnSaveButtonClicked` manipulador de eventos é executado, o `Note` instância é salva no banco de dados e o aplicativo navega de volta à página anterior. Quando o `OnDeleteButtonClicked` manipulador de eventos é executado, o `Note` instância é excluída do banco de dados e o aplicativo navega de volta à página anterior.

      Salve as alterações em **NoteEntryPage.xaml.cs** escolhendo **arquivo > Salvar** (ou pressionando  **&#8984; + S**) e feche o arquivo.

11. Compile e execute o projeto em cada plataforma. Para obter mais informações, consulte [criando o guia de início rápido](single-page.md#building-the-quickstart).

    Sobre o **NotesPage** pressione a **+** botão para navegar para o **NoteEntryPage** e insira uma observação. Depois de salvar a nota o aplicativo será navegue de volta para o **NotesPage**.

    Insira um número de observações, de comprimento variado, para observar o comportamento do aplicativo.

::: zone-end

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você aprendeu como:

- Use o Gerenciador de pacotes do NuGet para adicionar um pacote do NuGet a um projeto.
- Store dados localmente em um banco de dados SQLite.NET.

Para definir o estilo de aplicativo com os estilos XAML, continue para o próximo início rápido.

> [!div class="nextstepaction"]
> [Avançar](styling.md)

## <a name="related-links"></a>Links relacionados

- [Notas (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/GetStarted/Notes/Database/)
- [Aprofundamento de início rápido do xamarin. Forms](deepdive.md)
