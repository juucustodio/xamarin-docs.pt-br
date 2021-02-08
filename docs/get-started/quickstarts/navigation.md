---
title: Executar a navegação em um Xamarin.Forms aplicativo
description: Este artigo explica como transformar o Xamarin.Forms aplicativo Shell, capaz de armazenar uma única observação, em um aplicativo capaz de armazenar várias notas.
zone_pivot_groups: platform
ms.topic: quickstart
ms.prod: xamarin
ms.assetid: 030204F7-E9E4-4AE3-B9F7-915B697D0171
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.custom: contperf-fy21q3
ms.date: 01/28/2021
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: b6158df758a5c9563d8d8d0aa0b51e9dccff6a31
ms.sourcegitcommit: 1f391667869a4541dd9b42d78862dc01d69ed160
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/08/2021
ms.locfileid: "99818850"
---
# <a name="perform-navigation-in-a-xamarinforms-application"></a>Executar a navegação em um Xamarin.Forms aplicativo

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](/samples/xamarin/xamarin-forms-samples/getstarted-notes-navigation/)

Neste guia de início rápido, você aprenderá a:

- Adicione páginas adicionais a um Xamarin.Forms aplicativo de Shell.
- Execute a navegação entre as páginas.
- Use associação de dados para sincronizar dados entre elementos da interface do usuário e sua fonte de dados.

O guia de início rápido explica como transformar um aplicativo de Shell de plataforma cruzada Xamarin.Forms , capaz de armazenar uma única nota, em um aplicativo capaz de armazenar várias notas. O aplicativo final é mostrado abaixo:

Página de entrada de observação [ ![ da página de anotações](navigation-images/screenshots1-sml.png)](navigation-images/screenshots1.png#lightbox "Página de Observações") 
 [ ![ ](navigation-images/screenshots2-sml.png)](navigation-images/screenshots2.png#lightbox "Página de Entrada de Observação")

### <a name="prerequisites"></a>Pré-requisitos

Você deve concluir com êxito o [início rápido anterior](app.md) antes de tentar este guia de início rápido. Como alternativa, baixe o [exemplo de início rápido anterior](/samples/xamarin/xamarin-forms-samples/getstarted-notes-app/) e use-o como o ponto de partida para este guia de início rápido.

::: zone pivot="windows"

## <a name="update-the-app-with-visual-studio"></a>Atualizar um aplicativo com o Visual Studio

1. Inicie o Visual Studio. Na janela inicial, clique na solução **Notes** na lista projetos/soluções recentes ou clique em **Abrir um projeto ou solução** e, na caixa de diálogo **Abrir Projeto/Solução**, selecione o arquivo de solução para o projeto Notes:

    ![Abrir Solução](navigation-images/vs/open-solution.png)

2. Em **Gerenciador de soluções**, clique com o botão direito do mouse no projeto do **Notes** e selecione **Adicionar > nova pasta**:

    ![Adicionar Nova Pasta](navigation-images/vs/add-new-folder.png)

3. Em **Gerenciador de Soluções**, dê à pasta o nome **Modelos**:

    ![Pasta de Modelos](navigation-images/vs/name-folder.png)

4. Em **Gerenciador de soluções**, selecione a pasta **modelos** , clique com o botão direito do mouse e selecione **Adicionar > classe...**:

    ![Adicionar Novo Arquivo](navigation-images/vs/add-new-models-file.png)

5. Na caixa de diálogo **Adicionar novo item**, selecione **Visual C# > Itens > Classe**, nomeie o novo arquivo **Note** e clique no botão **Adicionar**:

    ![Adicionar Classe de Observação](navigation-images/vs/add-note-class.png)

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

    Salve as alterações em **note.cs** pressionando **Ctrl + S**.  

7. Em **Gerenciador de soluções**, no projeto do **Notes** , selecione a pasta **exibições** , clique com o botão direito do mouse e selecione **Adicionar > novo item...**. Na caixa de diálogo **Adicionar novo item** , selecione **itens do Visual C# > Xamarin.Forms página > conteúdo**, nomeie o novo arquivo **NoteEntryPage** e clique no botão **Adicionar** :

    ![Add::: no-Loc (Xamarin. Forms)::: ContentPage](navigation-images/vs/add-note-entry-page.png)

    Isso adicionará uma nova página chamada **NoteEntryPage** à pasta raiz do projeto. Esta página será usada para entrada de nota.

8. Em **NoteEntryPage.xaml**, remova todo o código de modelo e substitua-o pelo código a seguir:

      ```xaml
      <?xml version="1.0" encoding="UTF-8"?>
      <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                   xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                   x:Class="Notes.Views.NoteEntryPage"
                   Title="Note Entry">
          <!-- Layout children vertically -->
          <StackLayout Margin="20">
              <Editor Placeholder="Enter your note"
                      Text="{Binding Text}"
                      HeightRequest="100" />
              <!-- Layout children in two columns -->
              <Grid ColumnDefinitions="*,*">
                  <Button Text="Save"
                          Clicked="OnSaveButtonClicked" />
                  <Button Grid.Column="1"
                          Text="Delete"
                          Clicked="OnDeleteButtonClicked"/>
              </Grid>
          </StackLayout>
      </ContentPage>
      ```

      Esse código declarativamente define a interface do usuário para a página, que consiste em uma [`Editor`](xref:Xamarin.Forms.Editor) entrada de texto for e dois [`Button`](xref:Xamarin.Forms.Button) objetos que direcionam o aplicativo para salvar ou excluir um arquivo. As duas `Button` instâncias são dispostas horizontalmente em uma [`Grid`](xref:Xamarin.Forms.Grid) , com a `Editor` e `Grid` que estão sendo colocadas verticalmente em um [`StackLayout`](xref:Xamarin.Forms.StackLayout) . Além disso, o `Editor` usa a associação de dados para associar à propriedade `Text` do modelo de `Note`. Para obter mais informações sobre vinculação de dados, consulte [vinculação de dados](deepdive.md#data-binding) no guia de [ Xamarin.Forms início rápido](deepdive.md)aprofundamento.

      Salve as alterações em **NoteEntryPage. XAML** pressionando **Ctrl + S**.

9. Em **NoteEntryPage.xaml.cs**, remova todo o código de modelo e substitua-o pelo código a seguir:

      ```csharp
      using System;
      using System.IO;
      using Notes.Models;
      using Xamarin.Forms;

      namespace Notes.Views
      {
          [QueryProperty(nameof(ItemId), nameof(ItemId))]
          public partial class NoteEntryPage : ContentPage
          {
              public string ItemId
              {
                  set
                  {
                      LoadNote(value);
                  }
              }

              public NoteEntryPage()
              {
                  InitializeComponent();

                  // Set the BindingContext of the page to a new Note.
                  BindingContext = new Note();
              }

              void LoadNote(string filename)
              {
                  try
                  {
                      // Retrieve the note and set it as the BindingContext of the page.
                      Note note = new Note
                      {
                          Filename = filename,
                          Text = File.ReadAllText(filename),
                          Date = File.GetCreationTime(filename)
                      };
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

                  if (string.IsNullOrWhiteSpace(note.Filename))
                  {
                      // Save the file.
                      var filename = Path.Combine(App.FolderPath, $"{Path.GetRandomFileName()}.notes.txt");
                      File.WriteAllText(filename, note.Text);
                  }
                  else
                  {
                      // Update the file.
                      File.WriteAllText(note.Filename, note.Text);
                  }

                  // Navigate backwards
                  await Shell.Current.GoToAsync("..");
              }

              async void OnDeleteButtonClicked(object sender, EventArgs e)
              {
                  var note = (Note)BindingContext;

                  // Delete the file.
                  if (File.Exists(note.Filename))
                  {
                      File.Delete(note.Filename);
                  }

                  // Navigate backwards
                  await Shell.Current.GoToAsync("..");
              }
          }
      }
      ```

      Esse código armazena uma `Note` instância, que representa uma única nota, no [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) da página. A classe é decorada com um `QueryPropertyAttribute` que permite que os dados sejam passados para a página, durante a navegação, por meio de parâmetros de consulta. O primeiro argumento para o `QueryPropertyAttribute` especifica o nome da propriedade que receberá os dados, com o segundo argumento especificando a ID de parâmetro de consulta. Portanto, o `QueryParameterAttribute` no código acima Especifica que a `ItemId` Propriedade receberá os dados passados no `ItemId` parâmetro de consulta do URI especificado em uma chamada de `GoToAsync` método. `ItemId`Em seguida, a propriedade chama o `LoadNote` método para criar um `Note` objeto do arquivo no dispositivo e define o `BindingContext` da página para o `Note` objeto.

      Quando o **salvamento** [`Button`](xref:Xamarin.Forms.Button) é pressionado `OnSaveButtonClicked` , o manipulador de eventos é executado, o que salva o conteúdo do `Editor` em um novo arquivo com um FileName gerado aleatoriamente ou a um arquivo existente se uma observação estiver sendo atualizada. Em ambos os casos, o arquivo é armazenado na pasta de dados do aplicativo local para o aplicativo. Em seguida, o método navega de volta para a página anterior. Quando **Excluir** `Button` é pressionado, o manipulador de eventos `OnDeleteButtonClicked` é executado, o que exclui o arquivo, caso exista, e navega de volta à página anterior. Para obter mais informações sobre navegação, consulte [navegação](deepdive.md#navigation) no [ Xamarin.Forms início rápido do Shell para aprofundar](deepdive.md)-se.

      Salve as alterações em **NoteEntryPage.XAML.cs** pressionando **Ctrl + S**.

      > [!WARNING]
      > O aplicativo não será compilado no momento devido a erros que serão corrigidos nas etapas subsequentes.

10. No **Gerenciador de soluções**, no projeto do **Notes** , abra **NotesPage. XAML** na pasta **views** .

11. Em **NotesPage.xaml**, remova todo o código de modelo e substitua-o pelo código a seguir:

    ```xaml
    <?xml version="1.0" encoding="UTF-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="Notes.Views.NotesPage"
                 Title="Notes">
        <!-- Add an item to the toolbar -->
        <ContentPage.ToolbarItems>
            <ToolbarItem Text="Add"
                         Clicked="OnAddClicked" />
        </ContentPage.ToolbarItems>

        <!-- Display notes in a list -->
        <CollectionView x:Name="collectionView"
                        Margin="20"
                        SelectionMode="Single"
                        SelectionChanged="OnSelectionChanged">
            <CollectionView.ItemsLayout>
                <LinearItemsLayout Orientation="Vertical"
                                   ItemSpacing="10" />
            </CollectionView.ItemsLayout>
            <!-- Define the appearance of each item in the list -->
            <CollectionView.ItemTemplate>
                <DataTemplate>
                    <StackLayout>
                        <Label Text="{Binding Text}"
                               FontSize="Medium"/>
                        <Label Text="{Binding Date}"
                               TextColor="Silver"
                               FontSize="Small" />
                    </StackLayout>
                </DataTemplate>
            </CollectionView.ItemTemplate>
        </CollectionView>
    </ContentPage>
    ```

    Esse código define declarativamente a interface do usuário para a página, que consiste em um [`CollectionView`](xref:Xamarin.Forms.CollectionView) e um [`ToolbarItem`](xref:Xamarin.Forms.ToolbarItem) . O `CollectionView` usa a ligação de dados para exibir todas as anotações que são recuperadas pelo aplicativo. Selecionar uma observação navegará até o `NoteEntryPage` local em que a nota pode ser modificada. Como alternativa, uma nova observação pode ser criada pressionando o `ToolbarItem`. Para obter mais informações sobre vinculação de dados, consulte [vinculação de dados](deepdive.md#data-binding) no guia de [ Xamarin.Forms início rápido](deepdive.md)aprofundamento.

    Salve as alterações em **NotesPage. XAML** pressionando **Ctrl + S**.

12. No **Gerenciador de soluções**, no projeto **do Notes** , expanda **NotesPage. XAML** na pasta **views** e abra **NotesPage.XAML.cs**.

13. Em **NotesPage.xaml.cs**, remova todo o código de modelo e substitua-o pelo código a seguir:

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.IO;
    using System.Linq;
    using Newtonsoft.Json;
    using Notes.Models;
    using Xamarin.Forms;

    namespace Notes.Views
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

                // Create a Note object from each file.
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

                // Set the data source for the CollectionView to a
                // sorted collection of notes.
                collectionView.ItemsSource = notes
                    .OrderBy(d => d.Date)
                    .ToList();
            }

            async void OnAddClicked(object sender, EventArgs e)
            {
                // Navigate to the NoteEntryPage, without passing any data.
                await Shell.Current.GoToAsync(nameof(NoteEntryPage));
            }

            async void OnSelectionChanged(object sender, SelectionChangedEventArgs e)
            {
                if (e.CurrentSelection != null)
                {
                    // Navigate to the NoteEntryPage, passing the filename as a query parameter.
                    Note note = (Note)e.CurrentSelection.FirstOrDefault();
                    await Shell.Current.GoToAsync($"{nameof(NoteEntryPage)}?{nameof(NoteEntryPage.ItemId)}={note.Filename}");
                }
            }
        }
    }
    ```

    Esse código define a funcionalidade para o `NotesPage`. Quando a página é exibida, o `OnAppearing` método é executado, o que popula o [`CollectionView`](xref:Xamarin.Forms.CollectionView) com todas as anotações que foram recuperadas da pasta de dados do aplicativo local. Quando o [`ToolbarItem`](xref:Xamarin.Forms.ToolbarItem) é pressionado, o `OnAddClicked` manipulador de eventos é executado. Esse método navega para o `NoteEntryPage` . Quando um item em `CollectionView` é selecionado, o manipulador de eventos `OnSelectionChanged` é executado. Esse método navega para o `NoteEntryPage` , desde que um item no [`CollectionView`](xref:Xamarin.Forms.CollectionView) esteja selecionado, passando a `Filename` Propriedade do selecionado `Note` como um parâmetro de consulta para a página. Para obter mais informações sobre navegação, consulte [navegação](deepdive.md#navigation) no guia de [ Xamarin.Forms início rápido](deepdive.md)aprofundado.

    Salve as alterações em **NotesPage.XAML.cs** pressionando **Ctrl + S**.

    > [!WARNING]
    > O aplicativo não será compilado no momento devido a erros que serão corrigidos nas etapas subsequentes.

14. No **Gerenciador de soluções**, no projeto do **Notes** , expanda **AppShell. XAML** e abra **AppShell.XAML.cs**. Em seguida, substitua o código existente pelo código a seguir:

    ```csharp
    using Notes.Views;
    using Xamarin.Forms;

    namespace Notes
    {
        public partial class AppShell : Shell
        {
            public AppShell()
            {
                InitializeComponent();
                Routing.RegisterRoute(nameof(NoteEntryPage), typeof(NoteEntryPage));
            }
        }
    }
    ```

    Esse código registra uma rota para o `NoteEntryPage` , que não é representado na hierarquia visual do Shell (**AppShell. XAML**). Essa página pode então ser navegada para usar a navegação baseada em URI, com o `GoToAsync` método.

    Salve as alterações em **AppShell.XAML.cs** pressionando **Ctrl + S**.

15. No **Gerenciador de soluções**, no projeto do **Notes** , expanda **App. XAML** e abra **app.XAML.cs**. Em seguida, substitua o código existente pelo código a seguir:

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

    Esse código adiciona uma declaração de namespace ao namespace `System.IO` e adiciona uma declaração para uma propriedade estática `FolderPath` do tipo `string`. A propriedade `FolderPath` é usada para armazenar o caminho no dispositivo em que os dados de observação serão armazenados. Além disso, o código inicializa a `FolderPath` propriedade no `App` Construtor e inicializa a [`MainPage`](xref:Xamarin.Forms.Application.MainPage) propriedade para o objeto de subclasse `Shell` .

    Salve as alterações em **app.XAML.cs** pressionando **Ctrl + S**.

16. Compile e execute o projeto em cada plataforma. Para obter mais informações, confira [Como criar o guia de início rápido](app.md#building-the-quickstart).

    No **NotesPage** , pressione o botão **Adicionar** para navegar até o **NoteEntryPage** e insira uma observação. Depois de salvar a observação, o aplicativo navegará de volta para a **NotesPage**.

    Insira várias observações, de comprimento variável, para observar o comportamento do aplicativo. Feche o aplicativo e inicie-o novamente para garantir que as anotações inseridas foram salvas no dispositivo.

::: zone-end
::: zone pivot="macos"

## <a name="update-the-app-with-visual-studio-for-mac"></a>Atualizar um aplicativo com o Visual Studio para Mac

1. Inicialize o Visual Studio para Mac. Na janela inicial, clique em **Abrir** e, na caixa de diálogo, selecione o arquivo de solução para o projeto Notes:

    ![Abrir Solução](navigation-images/vsmac/open-solution.png)

2. Na **painel de soluções**, clique com o botão direito do mouse no projeto do **Notes** e selecione **Adicionar > nova pasta**:

    ![Adicionar Nova Pasta](navigation-images/vsmac/add-new-folder.png)

3. Na caixa de diálogo **nova pasta** , nomeie os novos **modelos** de pasta:

    ![Pasta de Modelos](navigation-images/vsmac/name-folder.png)

4. Na **painel de soluções**, selecione a pasta **modelos** , clique com o botão direito do mouse e selecione **Adicionar > nova classe...**:

    ![Adicionar Novo Arquivo](navigation-images/vsmac/add-new-models-file.png)

5. Na caixa de diálogo **Novo Arquivo**, selecione **Geral > Classe Vazia**, dê ao novo arquivo o nome **Note** e clique no botão **Novo**:

    ![Adicionar Classe de Observação](navigation-images/vsmac/add-note-class.png)

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

    Salve as alterações no **note.cs** escolhendo **arquivo > salvar** (ou pressionando **&#8984; + S**).

7. Na **painel de soluções**, selecione o projeto **notas** , clique com o botão direito do mouse e selecione **Adicionar > novo arquivo...**. Na caixa de diálogo **novo arquivo** , selecione **Forms > Forms Contentpage XAML**, nomeie o novo arquivo **NoteEntryPage** e clique no botão **novo** :

    ![Add::: no-Loc (Xamarin. Forms)::: ContentPage](navigation-images/vsmac/add-note-entry-page.png)

    Isso adicionará uma nova página chamada **NoteEntryPage** à pasta raiz do projeto. Esta página será usada para entrada de nota.

8. Em **NoteEntryPage.xaml**, remova todo o código de modelo e substitua-o pelo código a seguir:

      ```xaml
      <?xml version="1.0" encoding="UTF-8"?>
      <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                   xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                   x:Class="Notes.Views.NoteEntryPage"
                   Title="Note Entry">
          <!-- Layout children vertically -->
          <StackLayout Margin="20">
              <Editor Placeholder="Enter your note"
                      Text="{Binding Text}"
                      HeightRequest="100" />
              <!-- Layout children in two columns -->
              <Grid ColumnDefinitions="*,*">
                  <Button Text="Save"
                          Clicked="OnSaveButtonClicked" />
                  <Button Grid.Column="1"
                          Text="Delete"
                          Clicked="OnDeleteButtonClicked"/>
              </Grid>
          </StackLayout>
      </ContentPage>
      ```

      Esse código declarativamente define a interface do usuário para a página, que consiste em uma [`Editor`](xref:Xamarin.Forms.Editor) entrada de texto for e dois [`Button`](xref:Xamarin.Forms.Button) objetos que direcionam o aplicativo para salvar ou excluir um arquivo. As duas `Button` instâncias são dispostas horizontalmente em uma [`Grid`](xref:Xamarin.Forms.Grid) , com a `Editor` e `Grid` que estão sendo colocadas verticalmente em um [`StackLayout`](xref:Xamarin.Forms.StackLayout) . Além disso, o `Editor` usa a associação de dados para associar à propriedade `Text` do modelo de `Note`. Para obter mais informações sobre vinculação de dados, consulte [vinculação de dados](deepdive.md#data-binding) no guia de [ Xamarin.Forms início rápido](deepdive.md)aprofundamento.

      Salve as alterações em **NoteEntryPage. XAML** escolhendo **arquivo > salvar** (ou pressionando **&#8984; + S**).

9. Em **NoteEntryPage.xaml.cs**, remova todo o código de modelo e substitua-o pelo código a seguir:

      ```csharp
      using System;
      using System.IO;
      using Notes.Models;
      using Xamarin.Forms;

      namespace Notes.Views
      {
          [QueryProperty(nameof(ItemId), nameof(ItemId))]
          public partial class NoteEntryPage : ContentPage
          {
              public string ItemId
              {
                  set
                  {
                      LoadNote(value);
                  }
              }

              public NoteEntryPage()
              {
                  InitializeComponent();

                  // Set the BindingContext of the page to a new Note.
                  BindingContext = new Note();
              }

              void LoadNote(string filename)
              {
                  try
                  {
                      // Retrieve the note and set it as the BindingContext of the page.
                      Note note = new Note
                      {
                          Filename = filename,
                          Text = File.ReadAllText(filename),
                          Date = File.GetCreationTime(filename)
                      };
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

                  if (string.IsNullOrWhiteSpace(note.Filename))
                  {
                      // Save the file.
                      var filename = Path.Combine(App.FolderPath, $"{Path.GetRandomFileName()}.notes.txt");
                      File.WriteAllText(filename, note.Text);
                  }
                  else
                  {
                      // Update the file.
                      File.WriteAllText(note.Filename, note.Text);
                  }

                  // Navigate backwards
                  await Shell.Current.GoToAsync("..");
              }

              async void OnDeleteButtonClicked(object sender, EventArgs e)
              {
                  var note = (Note)BindingContext;

                  // Delete the file.
                  if (File.Exists(note.Filename))
                  {
                      File.Delete(note.Filename);
                  }

                  // Navigate backwards
                  await Shell.Current.GoToAsync("..");
              }
          }
      }
      ```

      Esse código armazena uma `Note` instância, que representa uma única nota, no [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) da página. A classe é decorada com um `QueryPropertyAttribute` que permite que os dados sejam passados para a página, durante a navegação, por meio de parâmetros de consulta. O primeiro argumento para o `QueryPropertyAttribute` especifica o nome da propriedade que receberá os dados, com o segundo argumento especificando a ID de parâmetro de consulta. Portanto, o `QueryParameterAttribute` no código acima Especifica que a `ItemId` Propriedade receberá os dados passados no `ItemId` parâmetro de consulta do URI especificado em uma chamada de `GoToAsync` método. `ItemId`Em seguida, a propriedade chama o `LoadNote` método para criar um `Note` objeto do arquivo no dispositivo e define o `BindingContext` da página para o `Note` objeto.

      Quando o **salvamento** [`Button`](xref:Xamarin.Forms.Button) é pressionado `OnSaveButtonClicked` , o manipulador de eventos é executado, o que salva o conteúdo do `Editor` em um novo arquivo com um FileName gerado aleatoriamente ou a um arquivo existente se uma observação estiver sendo atualizada. Em ambos os casos, o arquivo é armazenado na pasta de dados do aplicativo local para o aplicativo. Em seguida, o método navega de volta para a página anterior. Quando **Excluir** `Button` é pressionado, o manipulador de eventos `OnDeleteButtonClicked` é executado, o que exclui o arquivo, caso exista, e navega de volta à página anterior. Para obter mais informações sobre navegação, consulte [navegação](deepdive.md#navigation) no [ Xamarin.Forms início rápido do Shell para aprofundar](deepdive.md)-se.

      Salve as alterações no **NoteEntryPage.XAML.cs** escolhendo **arquivo > salvar** (ou pressionando **&#8984; + S**).

      > [!WARNING]
      > O aplicativo não será compilado no momento devido a erros que serão corrigidos nas etapas subsequentes.

10. No **painel de soluções**, no projeto do **Notes** , abra **NotesPage. XAML** na pasta **views** .

11. Em **NotesPage.xaml**, remova todo o código de modelo e substitua-o pelo código a seguir:

    ```xaml
    <?xml version="1.0" encoding="UTF-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="Notes.Views.NotesPage"
                 Title="Notes">
        <!-- Add an item to the toolbar -->
        <ContentPage.ToolbarItems>
            <ToolbarItem Text="Add"
                         Clicked="OnAddClicked" />
        </ContentPage.ToolbarItems>

        <!-- Display notes in a list -->
        <CollectionView x:Name="collectionView"
                        Margin="20"
                        SelectionMode="Single"
                        SelectionChanged="OnSelectionChanged">
            <CollectionView.ItemsLayout>
                <LinearItemsLayout Orientation="Vertical"
                                   ItemSpacing="10" />
            </CollectionView.ItemsLayout>
            <!-- Define the appearance of each item in the list -->
            <CollectionView.ItemTemplate>
                <DataTemplate>
                    <StackLayout>
                        <Label Text="{Binding Text}"
                               FontSize="Medium"/>
                        <Label Text="{Binding Date}"
                               TextColor="Silver"
                               FontSize="Small" />
                    </StackLayout>
                </DataTemplate>
            </CollectionView.ItemTemplate>
        </CollectionView>
    </ContentPage>
    ```

    Esse código define declarativamente a interface do usuário para a página, que consiste em um [`CollectionView`](xref:Xamarin.Forms.CollectionView) e um [`ToolbarItem`](xref:Xamarin.Forms.ToolbarItem) . O `CollectionView` usa a ligação de dados para exibir todas as anotações que são recuperadas pelo aplicativo. Selecionar uma observação navegará até o `NoteEntryPage` local em que a nota pode ser modificada. Como alternativa, uma nova observação pode ser criada pressionando o `ToolbarItem`. Para obter mais informações sobre vinculação de dados, consulte [vinculação de dados](deepdive.md#data-binding) no guia de [ Xamarin.Forms início rápido](deepdive.md)aprofundamento.

    Salve as alterações em **NotesPage. XAML** escolhendo **arquivo > salvar** (ou pressionando **&#8984; + S**).

12. No **painel de soluções**, no projeto do **Notes** , expanda **NotesPage. XAML** na pasta **views** e abra **NotesPage.XAML.cs**.

13. Em **NotesPage.xaml.cs**, remova todo o código de modelo e substitua-o pelo código a seguir:

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.IO;
    using System.Linq;
    using Newtonsoft.Json;
    using Notes.Models;
    using Xamarin.Forms;

    namespace Notes.Views
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

                // Create a Note object from each file.
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

                // Set the data source for the CollectionView to a
                // sorted collection of notes.
                collectionView.ItemsSource = notes
                    .OrderBy(d => d.Date)
                    .ToList();
            }

            async void OnAddClicked(object sender, EventArgs e)
            {
                // Navigate to the NoteEntryPage, without passing any data.
                await Shell.Current.GoToAsync(nameof(NoteEntryPage));
            }

            async void OnSelectionChanged(object sender, SelectionChangedEventArgs e)
            {
                if (e.CurrentSelection != null)
                {
                    // Navigate to the NoteEntryPage, passing the filename as a query parameter.
                    Note note = (Note)e.CurrentSelection.FirstOrDefault();
                    await Shell.Current.GoToAsync($"{nameof(NoteEntryPage)}?{nameof(NoteEntryPage.ItemId)}={note.Filename}");
                }
            }
        }
    }
    ```

    Esse código define a funcionalidade para o `NotesPage`. Quando a página é exibida, o `OnAppearing` método é executado, o que popula o [`CollectionView`](xref:Xamarin.Forms.CollectionView) com todas as anotações que foram recuperadas da pasta de dados do aplicativo local. Quando o [`ToolbarItem`](xref:Xamarin.Forms.ToolbarItem) é pressionado, o `OnAddClicked` manipulador de eventos é executado. Esse método navega para o `NoteEntryPage` . Quando um item em `CollectionView` é selecionado, o manipulador de eventos `OnSelectionChanged` é executado. Esse método navega para o `NoteEntryPage` , desde que um item no [`CollectionView`](xref:Xamarin.Forms.CollectionView) esteja selecionado, passando a `Filename` Propriedade do selecionado `Note` como um parâmetro de consulta para a página. Para obter mais informações sobre navegação, consulte [navegação](deepdive.md#navigation) no guia de [ Xamarin.Forms início rápido](deepdive.md)aprofundado.

    Salve as alterações no **NotesPage.XAML.cs** escolhendo **arquivo > salvar** (ou pressionando **&#8984; + S**).

    > [!WARNING]
    > O aplicativo não será compilado no momento devido a erros que serão corrigidos nas etapas subsequentes.

14. No **painel de soluções**, no projeto do **Notes** , expanda **AppShell. XAML** e abra **AppShell.XAML.cs**. Em seguida, substitua o código existente pelo código a seguir:

    ```csharp
    using Notes.Views;
    using Xamarin.Forms;

    namespace Notes
    {
        public partial class AppShell : Shell
        {
            public AppShell()
            {
                InitializeComponent();
                Routing.RegisterRoute(nameof(NoteEntryPage), typeof(NoteEntryPage));
            }
        }
    }
    ```

    Esse código registra uma rota para o `NoteEntryPage` , que não é representado na hierarquia visual do Shell. Essa página pode então ser navegada para usar a navegação baseada em URI, com o `GoToAsync` método.

    Salve as alterações no **AppShell.XAML.cs** escolhendo **arquivo > salvar** (ou pressionando **&#8984; + S**).

15. No **painel de soluções**, no projeto do **Notes** , expanda **App. XAML** e abra **app.XAML.cs**. Em seguida, substitua o código existente pelo código a seguir:

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

    Esse código adiciona uma declaração de namespace ao namespace `System.IO` e adiciona uma declaração para uma propriedade estática `FolderPath` do tipo `string`. A propriedade `FolderPath` é usada para armazenar o caminho no dispositivo em que os dados de observação serão armazenados. Além disso, o código inicializa a `FolderPath` propriedade no `App` Construtor e inicializa a [`MainPage`](xref:Xamarin.Forms.Application.MainPage) propriedade para o objeto de subclasse `Shell` .

    Salve as alterações no **app.XAML.cs** escolhendo **arquivo > salvar** (ou pressionando **&#8984; + S**).

16. Compile e execute o projeto em cada plataforma. Para obter mais informações, confira [Como criar o guia de início rápido](app.md#building-the-quickstart).

    No **NotesPage** , pressione o botão **Adicionar** para navegar até o **NoteEntryPage** e insira uma observação. Depois de salvar a observação, o aplicativo navegará de volta para a **NotesPage**.

    Insira várias observações, de comprimento variável, para observar o comportamento do aplicativo. Feche o aplicativo e inicie-o novamente para garantir que as anotações inseridas foram salvas no dispositivo.

::: zone-end

## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você aprendeu a:

- Adicione páginas adicionais a um Xamarin.Forms aplicativo de Shell.
- Execute a navegação entre as páginas.
- Use associação de dados para sincronizar dados entre elementos da interface do usuário e sua fonte de dados.

Continue para o próximo início rápido para modificar o aplicativo de forma que ele armazene seus dados em um SQLite.NET local.

> [!div class="nextstepaction"]
> [Próximo](database.md)

## <a name="related-links"></a>Links relacionados

- [Notas (exemplo)](/samples/xamarin/xamarin-forms-samples/getstarted-notes-navigation/)
- [Xamarin.Forms Aprofundamento do início rápido do Shell](deepdive.md)
