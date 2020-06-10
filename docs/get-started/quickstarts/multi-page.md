---
Título: "executar a navegação em um aplicativo de várias páginas Xamarin.Forms " Descrição: "Este artigo explica como transformar o aplicativo de página única, capaz de armazenar uma única nota, em um aplicativo de várias páginas, capaz de armazenar várias notas."
zone_pivot_groups: plataforma MS. Topic: início rápido MS. Prod: xamarin MS. AssetID: 9DC3B3D6-6CBC-4705-BE80-3D86A9E65F92 MS. Technology: xamarin-Forms autor: davidbritch MS. Author: dabritch MS. Date: 04/01/2019 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="perform-navigation-in-a-multi-page-xamarinforms-application"></a>Executar a navegação em um aplicativo de várias páginas Xamarin.Forms

[![Baixar exemplo ](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/getstarted-notes-multipage/)

Neste guia de início rápido, você aprenderá a:

- Adicione páginas adicionais a uma Xamarin.Forms solução.
- Execute a navegação entre as páginas.
- Use associação de dados para sincronizar dados entre elementos da interface do usuário e sua fonte de dados.

O guia de início rápido explica como transformar um aplicativo de plataforma cruzada em uma única página Xamarin.Forms , capaz de armazenar uma única nota, em um aplicativo de várias páginas, capaz de armazenar várias notas. O aplicativo final é mostrado abaixo:

[![](multi-page-images/screenshots1-sml.png "Notes Page")](multi-page-images/screenshots1.png#lightbox "Notes Page")
[![](multi-page-images/screenshots2-sml.png "Note Entry Page")](multi-page-images/screenshots2.png#lightbox "Note Entry Page")

### <a name="prerequisites"></a>Pré-requisitos

Você deve concluir com êxito o [início rápido anterior](single-page.md) antes de tentar este guia de início rápido. Como alternativa, baixe o [exemplo de início rápido anterior](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/getstarted-notes-singlepage/) e use-o como o ponto de partida para este guia de início rápido.

::: zone pivot="windows"

## <a name="update-the-app-with-visual-studio"></a>Atualizar um aplicativo com o Visual Studio

1. Inicie o Visual Studio. Na janela inicial, clique na solução **Notes** na lista projetos/soluções recentes ou clique em **Abrir um projeto ou solução** e, na caixa de diálogo **Abrir Projeto/Solução**, selecione o arquivo de solução para o projeto Notes:

    ![](multi-page-images/vs/open-solution.png "Open Project")

2. No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto **Notes** e selecione **Adicionar > Nova Pasta**:

    ![](multi-page-images/vs/add-new-item.png "Add New Item")

3. Em **Gerenciador de Soluções**, dê à pasta o nome **Modelos**:

    ![](multi-page-images/vs/name-folder.png "Models Folder")

4. No **Gerenciador de Soluções**, selecione a pasta **Modelos**, clique com o botão direito do mouse e selecione **Adicionar > Novo Item…**:

    ![](multi-page-images/vs/add-new-models-file.png "Add New File")

5. Na caixa de diálogo **Adicionar novo item**, selecione **Visual C# > Itens > Classe**, nomeie o novo arquivo **Note** e clique no botão **Adicionar**:

    ![](multi-page-images/vs/add-note-class.png "Add Note Class")

    Isso adicionará uma classe chamada **Observação** à pasta **Modelos** do projeto **Notes**.

6. Em **Note.cs**, remova todo o código de modelo e substitua-o pelo código a seguir:

    ```csharp
    using System;

    namespace Notes.Models
    {
        public class Note
        {
            public string Filename { get; set; }
            public string Text { get; set; }
            public DateTime Date { get; set; }
        }
    }
    ```

    Essa classe define um modelo de `Note` que armazenará dados sobre cada observação no aplicativo.    

    Salve as alterações em **Note.cs** pressionando **CTRL + S** e feche o arquivo.

7. Em **Gerenciador de soluções**, clique com o botão direito do mouse no projeto do **Notes** e selecione **Adicionar > novo item..**. Na caixa de diálogo **Adicionar novo item** , selecione **itens do Visual C# > Xamarin.Forms página > conteúdo**, nomeie o novo arquivo **NoteEntryPage**e clique no botão **Adicionar** :

    ![](multi-page-images/vs/add-note-entry-page.png "Add Xamarin.Forms ContentPage")

    Isso adicionará uma nova página chamada **NoteEntryPage** à pasta raiz do projeto. Essa página será a segunda página do aplicativo.

8. Em **NoteEntryPage.xaml**, remova todo o código de modelo e substitua-o pelo código a seguir:

      ```xaml
      <?xml version="1.0" encoding="UTF-8"?>
      <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                   xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                   x:Class="Notes.NoteEntryPage"
                   Title="Note Entry">
          <StackLayout Margin="20">
              <Editor Placeholder="Enter your note"
                      Text="{Binding Text}"
                      HeightRequest="100" />
              <Grid>
                  <Grid.ColumnDefinitions>
                      <ColumnDefinition Width="*" />
                      <ColumnDefinition Width="*" />
                  </Grid.ColumnDefinitions>
                  <Button Text="Save"
                          Clicked="OnSaveButtonClicked" />
                  <Button Grid.Column="1"
                          Text="Delete"
                          Clicked="OnDeleteButtonClicked"/>
              </Grid>
          </StackLayout>
      </ContentPage>
      ```

      Esse código declarativamente define a interface do usuário para a página, que consiste em uma [`Editor`](xref:Xamarin.Forms.Editor) entrada de texto for e duas [`Button`](xref:Xamarin.Forms.Button) instâncias que direcionam o aplicativo para salvar ou excluir um arquivo. As duas `Button` instâncias são dispostas horizontalmente em uma [`Grid`](xref:Xamarin.Forms.Grid) , com a `Editor` e `Grid` que estão sendo colocadas verticalmente em um [`StackLayout`](xref:Xamarin.Forms.StackLayout) . Além disso, o `Editor` usa a associação de dados para associar à propriedade `Text` do modelo de `Note`. Para obter mais informações sobre vinculação de dados, consulte [vinculação de dados](deepdive.md#data-binding) no guia de [ Xamarin.Forms início rápido](deepdive.md)aprofundamento.

      Salve as alterações em **NoteEntryPage.xaml** ao pressionar **CTRL+S** e feche o arquivo.

9. Em **NoteEntryPage.xaml.cs**, remova todo o código de modelo e substitua-o pelo código a seguir:

      ```csharp
      using System;
      using System.IO;
      using Xamarin.Forms;
      using Notes.Models;

      namespace Notes
      {
          public partial class NoteEntryPage : ContentPage
          {
              public NoteEntryPage()
              {
                  InitializeComponent();
              }

              async void OnSaveButtonClicked(object sender, EventArgs e)
              {
                  var note = (Note)BindingContext;

                  if (string.IsNullOrWhiteSpace(note.Filename))
                  {
                      // Save
                      var filename = Path.Combine(App.FolderPath, $"{Path.GetRandomFileName()}.notes.txt");
                      File.WriteAllText(filename, note.Text);
                  }
                  else
                  {
                      // Update
                      File.WriteAllText(note.Filename, note.Text);
                  }

                  await Navigation.PopAsync();
              }

              async void OnDeleteButtonClicked(object sender, EventArgs e)
              {
                  var note = (Note)BindingContext;

                  if (File.Exists(note.Filename))
                  {
                      File.Delete(note.Filename);
                  }

                  await Navigation.PopAsync();
              }
          }
      }
      ```

      Esse código armazena uma `Note` instância, que representa uma única nota, no [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) da página. Quando o **salvamento** [`Button`](xref:Xamarin.Forms.Button) é pressionado `OnSaveButtonClicked` , o manipulador de eventos é executado, o que salva o conteúdo do `Editor` em um novo arquivo com um FileName gerado aleatoriamente ou a um arquivo existente se uma observação estiver sendo atualizada. Em ambos os casos, o arquivo é armazenado na pasta de dados do aplicativo local para o aplicativo. Em seguida, o método navega de volta para a página anterior. Quando **Excluir** `Button` é pressionado, o manipulador de eventos `OnDeleteButtonClicked` é executado, o que exclui o arquivo, caso exista, e navega de volta à página anterior. Para obter mais informações sobre navegação, consulte [navegação](deepdive.md#navigation) no guia de [ Xamarin.Forms início rápido](deepdive.md)aprofundado.

      Salve as alterações em **NoteEntryPage.xaml.cs** pressionando **CTRL+S** e feche o arquivo.

      > [!WARNING]
      > Tentar compilar o aplicativo neste ponto resultará em erros que serão corrigidos em etapas posteriores.

10. Em **Gerenciador de soluções**, clique com o botão direito do mouse no projeto do **Notes** e selecione **Adicionar > novo item..**. Na caixa de diálogo **Adicionar novo item** , selecione **itens do Visual C# > Xamarin.Forms página > conteúdo**, nomeie o novo arquivo **NotesPage**e clique no botão **Adicionar** .

      Isso adicionará uma página chamada **NotesPage** à pasta raiz do projeto. Esta página será a página raiz do aplicativo.

11. Em **NotesPage.xaml**, remova todo o código de modelo e substitua-o pelo código a seguir:

    ```xaml
    <?xml version="1.0" encoding="UTF-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="Notes.NotesPage"
                 Title="Notes">
        <ContentPage.ToolbarItems>
            <ToolbarItem Text="+"
                         Clicked="OnNoteAddedClicked" />
        </ContentPage.ToolbarItems>
        <ListView x:Name="listView"
                  Margin="20"
                  ItemSelected="OnListViewItemSelected">
            <ListView.ItemTemplate>
                <DataTemplate>
                    <TextCell Text="{Binding Text}"
                              Detail="{Binding Date}" />
                </DataTemplate>
            </ListView.ItemTemplate>
        </ListView>
    </ContentPage>
    ```

    Esse código define declarativamente a interface do usuário para a página, que consiste em um [`ListView`](xref:Xamarin.Forms.ListView) e um [`ToolbarItem`](xref:Xamarin.Forms.ToolbarItem) . O `ListView` usa associação de dados para exibir quaisquer observações recuperadas pelo aplicativo, e selecionar uma observação navegará de volta para o `NoteEntryPage` em que a observação pode ser modificada. Como alternativa, uma nova observação pode ser criada pressionando o `ToolbarItem`. Para obter mais informações sobre vinculação de dados, consulte [vinculação de dados](deepdive.md#data-binding) no guia de [ Xamarin.Forms início rápido](deepdive.md)aprofundamento.

    Salve as alterações em **NotesPage.xaml** ao pressionar **CTRL+S** e feche o arquivo.

12. Em **NotesPage.xaml.cs**, remova todo o código de modelo e substitua-o pelo código a seguir:

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.IO;
    using System.Linq;
    using Xamarin.Forms;
    using Notes.Models;

    namespace Notes
    {
        public partial class NotesPage : ContentPage
        {
            public NotesPage()
            {
                InitializeComponent();
            }

            protected override void OnAppearing()
            {
                base.OnAppearing();

                var notes = new List<Note>();

                var files = Directory.EnumerateFiles(App.FolderPath, "*.notes.txt");
                foreach (var filename in files)
                {
                    notes.Add(new Note
                    {
                        Filename = filename,
                        Text = File.ReadAllText(filename),
                        Date = File.GetCreationTime(filename)
                    });
                }

                listView.ItemsSource = notes
                    .OrderBy(d => d.Date)
                    .ToList();
            }

            async void OnNoteAddedClicked(object sender, EventArgs e)
            {
                await Navigation.PushAsync(new NoteEntryPage
                {
                    BindingContext = new Note()
                });
            }

            async void OnListViewItemSelected(object sender, SelectedItemChangedEventArgs e)
            {
                if (e.SelectedItem != null)
                {
                    await Navigation.PushAsync(new NoteEntryPage
                    {
                        BindingContext = e.SelectedItem as Note
                    });
                }
            }
        }
    }
    ```    

    Esse código define a funcionalidade para o `NotesPage`. Quando a página é exibida, o `OnAppearing` método é executado, o que popula o [`ListView`](xref:Xamarin.Forms.ListView) com todas as anotações que foram recuperadas da pasta de dados do aplicativo local. Quando o [`ToolbarItem`](xref:Xamarin.Forms.ToolbarItem) é pressionado, o `OnNoteAddedClicked` manipulador de eventos é executado. Esse método navega para o `NoteEntryPage` , definindo o [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) do `NoteEntryPage` para uma nova `Note` instância. Quando um item em `ListView` é selecionado, o manipulador de eventos `OnListViewItemSelected` é executado. Esse método navega para o `NoteEntryPage` , definindo o [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) do `NoteEntryPage` para a `Note` instância selecionada. Para obter mais informações sobre navegação, consulte [navegação](deepdive.md#navigation) no guia de [ Xamarin.Forms início rápido](deepdive.md)aprofundado.

    Salve as alterações em **NotesPage.xaml** pressionando **CTRL+S** e feche o arquivo.

    > [!WARNING]
    > Tentar compilar o aplicativo neste ponto resultará em erros que serão corrigidos em etapas posteriores.

13. No **Gerenciador de Soluções**, clique duas vezes em **App.xaml.cs** para abri-lo. Em seguida, substitua o código existente pelo código a seguir:

    ```csharp
    using System;
    using System.IO;
    using Xamarin.Forms;

    namespace Notes
    {
        public partial class App : Application
        {
            public static string FolderPath { get; private set; }

            public App()
            {
                InitializeComponent();
                FolderPath = Path.Combine(Environment.GetFolderPath(Environment.SpecialFolder.LocalApplicationData));
                MainPage = new NavigationPage(new NotesPage());
            }
            // ...
        }
    }
    ```

    Esse código adiciona uma declaração de namespace ao namespace `System.IO` e adiciona uma declaração para uma propriedade estática `FolderPath` do tipo `string`. A propriedade `FolderPath` é usada para armazenar o caminho no dispositivo em que os dados de observação serão armazenados. Além disso, o código inicializa a `FolderPath` propriedade no `App` Construtor e inicializa a [`MainPage`](xref:Xamarin.Forms.Application.MainPage) propriedade como um [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) que hospeda uma instância do `NotesPage` . Para obter mais informações sobre navegação, consulte [navegação](deepdive.md#navigation) no guia de [ Xamarin.Forms início rápido](deepdive.md)aprofundado.

    Salve as alterações em **App.xaml.cs** ao pressionar **CTRL + S** e feche o arquivo.

14. No **Gerenciador de Soluções**, no projeto **Notes**, clique com o botão direito do mouse em **MainPage.xaml** e selecione **Excluir**. Na caixa de diálogo exibida, pressione o botão **OK** para remover o arquivo do disco rígido.

    Isso remove uma página que não é mais usada.

15. Compile e execute o projeto em cada plataforma. Para obter mais informações, confira [Como criar o guia de início rápido](single-page.md#building-the-quickstart).

    Na **NotesPage**, pressione o botão **+** para navegar até a **NoteEntryPage** e inserir uma observação. Depois de salvar a observação, o aplicativo navegará de volta para a **NotesPage**.

    Insira diversas observações de comprimento variável para observar o comportamento do aplicativo.

::: zone-end
::: zone pivot="macos"

## <a name="update-the-app-with-visual-studio-for-mac"></a>Atualizar um aplicativo com o Visual Studio para Mac

1. Inicialize o Visual Studio para Mac. Na janela inicial, clique em **Abrir** e, na caixa de diálogo, selecione o arquivo de solução para o projeto Notes:

    ![](multi-page-images/vsmac/open-solution.png "Open Solution")

2. No **Painel de Soluções**, selecione o projeto **Observações**, clique com o botão direito do mouse e selecione **Adicionar > Nova Pasta**:

    ![](multi-page-images/vsmac/add-new-folder.png "Add New Folder")

3. No **Painel de Soluções**, dê à nova pasta o nome de **Modelos**:

    ![](multi-page-images/vsmac/name-folder.png "Models Folder")

4. No **Painel de Soluções**, selecione a pasta **Modelos**, clique com o botão direito do mouse e selecione **Adicionar > Novo Arquivo…**:

    ![](multi-page-images/vsmac/add-new-models-file.png "Add New File")

5. Na caixa de diálogo **Novo Arquivo**, selecione **Geral > Classe Vazia**, dê ao novo arquivo o nome **Note** e clique no botão **Novo**:

    ![](multi-page-images/vsmac/add-note-class.png "Add Note Class")

    Isso adicionará uma classe chamada **Observação** à pasta **Modelos** do projeto **Notes**.

6. Em **Note.cs**, remova todo o código de modelo e substitua-o pelo código a seguir:

    ```csharp
    using System;

    namespace Notes.Models
    {
        public class Note
        {
            public string Filename { get; set; }
            public string Text { get; set; }
            public DateTime Date { get; set; }
        }
    }
    ```

    Essa classe define um modelo de `Note` que armazenará dados sobre cada observação no aplicativo.

    Salve as alterações em **Note.cs** ao escolher **Arquivo > Salvar** (ou ao pressionar **& #8984; + S**) e feche o arquivo.

7. Na **painel de soluções**, selecione o projeto **notas** , clique com o botão direito do mouse e selecione **Adicionar > novo arquivo...**. Na caixa de diálogo **novo arquivo** , selecione **Forms > Forms Contentpage XAML**, nomeie o novo arquivo **NoteEntryPage**e clique no botão **novo** :

    ![](multi-page-images/vsmac/add-note-entry-page.png "Add Xamarin.Forms ContentPage")

    Isso adicionará uma nova página chamada **NoteEntryPage** à pasta raiz do projeto. Essa página será a segunda página do aplicativo.

8. Em **NoteEntryPage.xaml**, remova todo o código de modelo e substitua-o pelo código a seguir:

      ```xaml
      <?xml version="1.0" encoding="UTF-8"?>
      <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                   xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                   x:Class="Notes.NoteEntryPage"
                   Title="Note Entry">
          <StackLayout Margin="20">
              <Editor Placeholder="Enter your note"
                      Text="{Binding Text}"
                      HeightRequest="100" />
              <Grid>
                  <Grid.ColumnDefinitions>
                      <ColumnDefinition Width="*" />
                      <ColumnDefinition Width="*" />
                  </Grid.ColumnDefinitions>
                  <Button Text="Save"
                          Clicked="OnSaveButtonClicked" />
                  <Button Grid.Column="1"
                          Text="Delete"
                          Clicked="OnDeleteButtonClicked"/>
              </Grid>
          </StackLayout>
      </ContentPage>
      ```

      Esse código declarativamente define a interface do usuário para a página, que consiste em uma [`Editor`](xref:Xamarin.Forms.Editor) entrada de texto for e duas [`Button`](xref:Xamarin.Forms.Button) instâncias que direcionam o aplicativo para salvar ou excluir um arquivo. As duas `Button` instâncias são dispostas horizontalmente em uma [`Grid`](xref:Xamarin.Forms.Grid) , com a `Editor` e `Grid` que estão sendo colocadas verticalmente em um [`StackLayout`](xref:Xamarin.Forms.StackLayout) . Além disso, o `Editor` usa a associação de dados para associar à propriedade `Text` do modelo de `Note`. Para obter mais informações sobre vinculação de dados, consulte [vinculação de dados](deepdive.md#data-binding) no guia de [ Xamarin.Forms início rápido](deepdive.md)aprofundamento.

      Salve as alterações em **NoteEntryPage.xaml** ao escolher **Arquivo > Salvar** (ou ao pressionar **& #8984; + S**) e feche o arquivo.

9. Em **NoteEntryPage.xaml.cs**, remova todo o código de modelo e substitua-o pelo código a seguir:

      ```csharp
      using System;
      using System.IO;
      using Xamarin.Forms;
      using Notes.Models;

      namespace Notes
      {
          public partial class NoteEntryPage : ContentPage
          {
              public NoteEntryPage()
              {
                  InitializeComponent();
              }

              async void OnSaveButtonClicked(object sender, EventArgs e)
              {
                  var note = (Note)BindingContext;

                  if (string.IsNullOrWhiteSpace(note.Filename))
                  {
                      // Save
                      var filename = Path.Combine(App.FolderPath, $"{Path.GetRandomFileName()}.notes.txt");
                      File.WriteAllText(filename, note.Text);
                  }
                  else
                  {
                      // Update
                      File.WriteAllText(note.Filename, note.Text);
                  }

                  await Navigation.PopAsync();
              }

              async void OnDeleteButtonClicked(object sender, EventArgs e)
              {
                  var note = (Note)BindingContext;

                  if (File.Exists(note.Filename))
                  {
                      File.Delete(note.Filename);
                  }

                  await Navigation.PopAsync();
              }
          }
      }
      ```

      Esse código armazena uma `Note` instância, que representa uma única nota, no [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) da página. Quando o **salvamento** [`Button`](xref:Xamarin.Forms.Button) é pressionado `OnSaveButtonClicked` , o manipulador de eventos é executado, o que salva o conteúdo do `Editor` em um novo arquivo com um FileName gerado aleatoriamente ou a um arquivo existente se uma observação estiver sendo atualizada. Em ambos os casos, o arquivo é armazenado na pasta de dados do aplicativo local para o aplicativo. Em seguida, o método navega de volta para a página anterior. Quando **Excluir** `Button` é pressionado, o manipulador de eventos `OnDeleteButtonClicked` é executado, o que exclui o arquivo, caso exista, e navega de volta à página anterior. Para obter mais informações sobre navegação, consulte [navegação](deepdive.md#navigation) no guia de [ Xamarin.Forms início rápido](deepdive.md)aprofundado.

      Salve as alterações em **NoteEntryPage.xaml.cs** escolhendo **Arquivo > Salvar** (ou pressionando **&#8984; + S**) e feche o arquivo.

      > [!WARNING]
      > Tentar compilar o aplicativo neste ponto resultará em erros que serão corrigidos em etapas posteriores.

10. Na **painel de soluções**, selecione o projeto **notas** , clique com o botão direito do mouse e selecione **Adicionar > novo arquivo...**. Na caixa de diálogo **novo arquivo** , selecione **Forms > Forms Contentpage XAML**, nomeie o novo arquivo **NotesPage**e clique no botão **novo** .

      Isso adicionará uma página chamada **NotesPage** à pasta raiz do projeto. Esta página será a página raiz do aplicativo.

11. Em **NotesPage.xaml**, remova todo o código de modelo e substitua-o pelo código a seguir:

    ```xaml
    <?xml version="1.0" encoding="UTF-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="Notes.NotesPage"
                 Title="Notes">
        <ContentPage.ToolbarItems>
            <ToolbarItem Text="+"
                         Clicked="OnNoteAddedClicked" />
        </ContentPage.ToolbarItems>
        <ListView x:Name="listView"
                  Margin="20"
                  ItemSelected="OnListViewItemSelected">
            <ListView.ItemTemplate>
                <DataTemplate>
                    <TextCell Text="{Binding Text}"
                              Detail="{Binding Date}" />
                </DataTemplate>
            </ListView.ItemTemplate>
        </ListView>
    </ContentPage>
    ```

    Esse código define declarativamente a interface do usuário para a página, que consiste em um [`ListView`](xref:Xamarin.Forms.ListView) e um [`ToolbarItem`](xref:Xamarin.Forms.ToolbarItem) . O `ListView` usa associação de dados para exibir quaisquer observações recuperadas pelo aplicativo, e selecionar uma observação navegará de volta para o `NoteEntryPage` em que a observação pode ser modificada. Como alternativa, uma nova observação pode ser criada pressionando o `ToolbarItem`. Para obter mais informações sobre vinculação de dados, consulte [vinculação de dados](deepdive.md#data-binding) no guia de [ Xamarin.Forms início rápido](deepdive.md)aprofundamento.

    Salve as alterações em **NotesPage.xaml** ao escolher **Arquivo > Salvar** (ou ao pressionar **& #8984; + S**) e feche o arquivo.

12. Em **NotesPage.xaml.cs**, remova todo o código de modelo e substitua-o pelo código a seguir:

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.IO;
    using System.Linq;
    using Xamarin.Forms;
    using Notes.Models;

    namespace Notes
    {
        public partial class NotesPage : ContentPage
        {
            public NotesPage()
            {
                InitializeComponent();
            }

            protected override void OnAppearing()
            {
                base.OnAppearing();

                var notes = new List<Note>();

                var files = Directory.EnumerateFiles(App.FolderPath, "*.notes.txt");
                foreach (var filename in files)
                {
                    notes.Add(new Note
                    {
                        Filename = filename,
                        Text = File.ReadAllText(filename),
                        Date = File.GetCreationTime(filename)
                    });
                }

                listView.ItemsSource = notes
                    .OrderBy(d => d.Date)
                    .ToList();
            }

            async void OnNoteAddedClicked(object sender, EventArgs e)
            {
                await Navigation.PushAsync(new NoteEntryPage
                {
                    BindingContext = new Note()
                });
            }

            async void OnListViewItemSelected(object sender, SelectedItemChangedEventArgs e)
            {
                if (e.SelectedItem != null)
                {
                    await Navigation.PushAsync(new NoteEntryPage
                    {
                        BindingContext = e.SelectedItem as Note
                    });
                }
            }
        }
    }
    ```    

    Esse código define a funcionalidade para o `NotesPage`. Quando a página é exibida, o `OnAppearing` método é executado, o que popula o [`ListView`](xref:Xamarin.Forms.ListView) com todas as anotações que foram recuperadas da pasta de dados do aplicativo local. Quando o [`ToolbarItem`](xref:Xamarin.Forms.ToolbarItem) é pressionado, o `OnNoteAddedClicked` manipulador de eventos é executado. Esse método navega para o `NoteEntryPage` , definindo o [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) do `NoteEntryPage` para uma nova `Note` instância. Quando um item em `ListView` é selecionado, o manipulador de eventos `OnListViewItemSelected` é executado. Esse método navega para o `NoteEntryPage` , definindo o [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) do `NoteEntryPage` para a `Note` instância selecionada. Para obter mais informações sobre navegação, consulte [navegação](deepdive.md#navigation) no guia de [ Xamarin.Forms início rápido](deepdive.md)aprofundado.

    Salve as alterações em **NotesPage.xaml.cs** ao escolher **Arquivo > Salvar** (ou ao pressionar **& #8984; + S**) e feche o arquivo.

    > [!WARNING]
    > Tentar compilar o aplicativo neste ponto resultará em erros que serão corrigidos em etapas posteriores.

13. No **Painel de Soluções**, clique duas vezes em **App.xaml.cs** para abri-lo. Em seguida, substitua o código existente pelo código a seguir:

    ```csharp
    using System;
    using System.IO;
    using Xamarin.Forms;

    namespace Notes
    {
        public partial class App : Application
        {
            public static string FolderPath { get; private set; }

            public App()
            {
                InitializeComponent();
                FolderPath = Path.Combine(Environment.GetFolderPath(Environment.SpecialFolder.LocalApplicationData));
                MainPage = new NavigationPage(new NotesPage());
            }
            // ...
        }
    }
    ```

    Esse código adiciona uma declaração de namespace ao namespace `System.IO` e adiciona uma declaração para uma propriedade estática `FolderPath` do tipo `string`. A propriedade `FolderPath` é usada para armazenar o caminho no dispositivo em que os dados de observação serão armazenados. Além disso, o código inicializa a `FolderPath` propriedade no `App` Construtor e inicializa a [`MainPage`](xref:Xamarin.Forms.Application.MainPage) propriedade como um [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) que hospeda uma instância do `NotesPage` . Para obter mais informações sobre navegação, consulte [navegação](deepdive.md#navigation) no guia de [ Xamarin.Forms início rápido](deepdive.md)aprofundado.

    Salve as alterações em **App.xaml.cs** escolhendo **Arquivo > Salvar** (ou pressionando **& #8984; + S**) e feche o arquivo.

14. No **Painel de Soluções**, no projeto **Notes**, clique com o botão direito do mouse em **MainPage.xaml** e selecione **Remover**. Na caixa de diálogo exibida, pressione o botão **Excluir** para remover o arquivo do disco rígido.

    Isso remove uma página que não é mais usada.

15. Compile e execute o projeto em cada plataforma. Para obter mais informações, confira [Como criar o guia de início rápido](single-page.md#building-the-quickstart).

    Na **NotesPage**, pressione o botão **+** para navegar até a **NoteEntryPage** e inserir uma observação. Depois de salvar a observação, o aplicativo navegará de volta para a **NotesPage**.

    Insira diversas observações de comprimento variável para observar o comportamento do aplicativo.

::: zone-end

## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você aprendeu a:

- Adicione páginas adicionais a uma Xamarin.Forms solução.
- Execute a navegação entre as páginas.
- Use associação de dados para sincronizar dados entre elementos da interface do usuário e sua fonte de dados.

Para modificar o aplicativo de forma que ele armazene seus dados em um banco de dados SQLite.NET local, vá para o próximo início rápido.

> [!div class="nextstepaction"]
> [Avançar](database.md)

## <a name="related-links"></a>Links relacionados

- [Notas (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/getstarted-notes-multipage/)
- [Xamarin.FormsAprofundamento do guia de início rápido](deepdive.md)
