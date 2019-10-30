---
title: Navegar em um aplicativo Xamarin.Forms de várias páginas
description: Este artigo explica como transformar o aplicativo de página única, capaz de armazenar uma única nota, em um aplicativo de várias páginas, capaz de armazenar várias notas.
zone_pivot_groups: platform
ms.topic: quickstart
ms.prod: xamarin
ms.assetid: 9DC3B3D6-6CBC-4705-BE80-3D86A9E65F92
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/01/2019
ms.openlocfilehash: 9ce02b4c6412eab1f4b1003b262573c59940286c
ms.sourcegitcommit: 9bfedf07940dad7270db86767eb2cc4007f2a59f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/21/2019
ms.locfileid: "68653790"
---
# <a name="perform-navigation-in-a-multi-page-xamarinforms-application"></a>Executar a navegação em um aplicativo Xamarin.Forms de várias páginas

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/getstarted-notes-multipage/)

Neste guia de início rápido, você aprenderá a:

- Adicionar páginas adicionais a uma solução Xamarin.Forms.
- Execute a navegação entre as páginas.
- Usar a vinculação de dados para sincronizar dados entre elementos da interface do usuário e sua fonte de dados.

O guia de início rápido explica como transformar um aplicativo Xamarin. Forms de plataforma cruzada em uma única página, capaz de armazenar uma única observação, em um aplicativo de várias páginas, capaz de armazenar várias notas. O aplicativo final é mostrado abaixo:

[![](multi-page-images/screenshots1-sml.png "Notes Page")](multi-page-images/screenshots1.png#lightbox "Notes Page")
[![](multi-page-images/screenshots2-sml.png "Note Entry Page")](multi-page-images/screenshots2.png#lightbox "Note Entry Page")

### <a name="prerequisites"></a>Pré-Requisitos

Você deve concluir com êxito o [início rápido anterior](single-page.md) antes de tentar este guia de início rápido. Como alternativa, baixe o [exemplo anterior de início rápido](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/getstarted-notes-singlepage/) e use-o como o ponto de partida para este guia de início rápido.

::: zone pivot="windows"

## <a name="update-the-app-with-visual-studio"></a>Atualizar um aplicativo com o Visual Studio

1. Inicie o Visual Studio. Na janela iniciar, clique na solução **Notes** na lista projetos recentes/soluções, ou clique em **abrir um projeto ou solução**e, na caixa de diálogo **Abrir projeto/solução** , selecione o arquivo de solução para o projeto do Notes:

    ![](multi-page-images/vs/open-solution.png "Open Project")

2. Em **Gerenciador de soluções**, clique com o botão direito do mouse no projeto do **Notes** e selecione **Adicionar > nova pasta**:

    ![](multi-page-images/vs/add-new-item.png "Add New Item")

3. Em **Gerenciador de soluções**, nomeie a nova pasta como **Modelos**

    ![](multi-page-images/vs/name-folder.png "Models Folder")

4. Em **Gerenciador de soluções**, selecione a pasta **modelos** , clique com o botão direito do mouse e selecione **Adicionar > novo item...** :

    ![](multi-page-images/vs/add-new-models-file.png "Add New File")

5. Na caixa de diálogo **Adicionar novo item** , **selecione C# itens visuais > classe**, nomeie o novo arquivo para **Note** e clique no botão **Adicionar** :

    ![](multi-page-images/vs/add-note-class.png "Add Note Class")

    Isso adicionará uma classe denominada **Note** à pasta **modelos** do projeto do **Notes** .

6. No **note.cs**, remova todo o código do modelo e substitua-o pelo código a seguir:

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

    Essa classe define um modelo de `Note` que armazenará dados sobre cada nota no aplicativo.    

    Salve as alterações em **note.cs** pressionando **Ctrl + S** e feche o arquivo.

7. Em **Gerenciador de soluções**, clique com o botão direito do mouse no projeto do **Notes** e selecione **Adicionar > novo item..** . Na caixa de diálogo **Adicionar novo item** , **selecione C# itens visuais > Xamarin.Forms > página de conteúdo**, nomeie o novo arquivo **NoteEntryPage** e clique no botão **Adicionar** :

    ![](multi-page-images/vs/add-note-entry-page.png "Add Xamarin.Forms ContentPage")

    Isso adicionará uma nova página chamada **NoteEntryPage** à pasta raiz do projeto. Esta página será a segunda página do aplicativo.

8. No **NoteEntryPage.XAML**, remova todo o código do modelo e substitua-o pelo seguinte código:

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

      Esse código declarativamente define a interface do usuário para a página, que consiste em um [`Editor`](xref:Xamarin.Forms.Editor) para entrada de texto e duas instâncias de [`Button`](xref:Xamarin.Forms.Button) que direcionam o aplicativo para salvar ou excluir um arquivo. As duas instâncias de `Button` são dispostas horizontalmente em uma [`Grid`](xref:Xamarin.Forms.Grid), com o `Editor` e `Grid` sendo dispostos verticalmente em uma [`StackLayout`](xref:Xamarin.Forms.StackLayout). Além disso, o `Editor` usa a vinculação de dados para associar à propriedade `Text` do modelo de `Note`. Para obter mais informações sobre vinculação de dados, consulte [vinculação de dados](deepdive.md#data-binding) no guia de [início rápido do Xamarin. Forms](deepdive.md).

      Salve as alterações em **NoteEntryPage.XAML** pressionando **Ctrl + S** e feche o arquivo.

9. No **NoteEntryPage.XAML.cs**, remova todo o código do modelo e substitua-o pelo código a seguir:

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

      Esse código armazena uma instância de `Note`, que representa uma única nota, na [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) da página. Quando o [`Button`](xref:Xamarin.Forms.Button) de salvamento é pressionado, o manipulador de eventos de `OnSaveButtonClicked` é executado, o que salva o conteúdo do `Editor` em um novo arquivo com um FileName gerado aleatoriamente ou em um arquivo existente se uma observação estiver sendo atualizada. Em ambos os casos, o arquivo é armazenado na pasta de dados do aplicativo local para o aplicativo. Em seguida, o método navega de volta para a página anterior. Quando o `Button` de **exclusão** é pressionado, o manipulador de eventos `OnDeleteButtonClicked` é executado, o que exclui o arquivo, desde que ele exista e navega de volta para a página anterior. Para obter mais informações sobre navegação, consulte [navegação](deepdive.md#navigation) no guia de [início rápido do Xamarin. Forms](deepdive.md).

      Salve as alterações em **NoteEntryPage.XAML.cs** pressionando **Ctrl + S** e feche o arquivo.

      > [!WARNING]
      > A tentativa de criar o aplicativo neste ponto resultará em erros que serão corrigidos nas etapas subsequentes.

10. Em **Gerenciador de soluções**, clique com o botão direito do mouse no projeto do **Notes** e selecione **Adicionar > novo item..** . Na caixa de diálogo **Adicionar novo item** , **selecione C# itens visuais > Xamarin.Forms > página de conteúdo**, nomeie o novo arquivo **NotesPage**e clique no botão **Adicionar** .

      Isso adicionará uma página chamada **NotesPage** à pasta raiz do projeto. Esta página será a página raiz do aplicativo.

11. No **NotesPage.XAML**, remova todo o código do modelo e substitua-o pelo seguinte código:

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

    Esse código define declarativamente a interface do usuário para a página, que consiste em um [`ListView`](xref:Xamarin.Forms.ListView) e um [`ToolbarItem`](xref:Xamarin.Forms.ToolbarItem). O `ListView` usa a vinculação de dados para exibir todas as anotações que são recuperadas pelo aplicativo e a seleção de uma observação navegará até o `NoteEntryPage` em que a nota pode ser modificada. Como alternativa, uma nova nota pode ser criada pressionando o `ToolbarItem`. Para obter mais informações sobre vinculação de dados, consulte [vinculação de dados](deepdive.md#data-binding) no guia de [início rápido do Xamarin. Forms](deepdive.md).

    Salve as alterações em **NotesPage.XAML** pressionando **Ctrl + S** e feche o arquivo.

12. No **NotesPage.XAML.cs**, remova todo o código do modelo e substitua-o pelo código a seguir:

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

    Esse código define a funcionalidade para o `NotesPage`. Quando a página é exibida, o método `OnAppearing` é executado, o que popula a [`ListView`](xref:Xamarin.Forms.ListView) com todas as anotações que foram recuperadas da pasta de dados do aplicativo local. Quando o [`ToolbarItem`](xref:Xamarin.Forms.ToolbarItem) é pressionado, o manipulador de eventos `OnNoteAddedClicked` é executado. Esse método navega para a `NoteEntryPage`, definindo a [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) do `NoteEntryPage` como uma nova instância `Note`. Quando um item na `ListView` é selecionado, o manipulador de eventos `OnListViewItemSelected` é executado. Esse método navega para a `NoteEntryPage`, definindo a [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) do `NoteEntryPage` para a instância de `Note` selecionada. Para obter mais informações sobre navegação, consulte [navegação](deepdive.md#navigation) no guia de [início rápido do Xamarin. Forms](deepdive.md).

    Salve as alterações em **NotesPage.XAML.cs** pressionando **Ctrl + S** e feche o arquivo.

    > [!WARNING]
    > A tentativa de criar o aplicativo neste ponto resultará em erros que serão corrigidos nas etapas subsequentes.

13. Em **Gerenciador de soluções**, clique duas vezes em **App.XAML.cs** para abri-lo. Em seguida, substitua o código existente pelo código a seguir:

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

    Esse código adiciona uma declaração de namespace para o namespace `System.IO` e adiciona uma declaração para uma propriedade estática `FolderPath` do tipo `string`. A propriedade `FolderPath` é usada para armazenar o caminho no dispositivo em que os dados de observação serão armazenados. Além disso, o código inicializa a propriedade `FolderPath` no Construtor `App` e inicializa a propriedade [`MainPage`](xref:Xamarin.Forms.Application.MainPage) como um [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) que hospeda uma instância do `NotesPage`. Para obter mais informações sobre navegação, consulte [navegação](deepdive.md#navigation) no guia de [início rápido do Xamarin. Forms](deepdive.md).

    Salve as alterações em **App.xaml.cs** ao pressionar **CTRL + S** e feche o arquivo.

14. No **Gerenciador de soluções**, no projeto do **Notes** , clique com o botão direito do mouse em **MainPage.XAML** e selecione **excluir**. Na caixa de diálogo exibida, pressione o botão **OK** para remover o arquivo do disco rígido.

    Isso remove uma página que não é mais usada.

15. Compile e execute o projeto em cada plataforma. Para obter mais informações, consulte [criando o início rápido](single-page.md#building-the-quickstart).

    No **NotesPage** , pressione o botão **+** para navegar até o **NoteEntryPage** e insira uma observação. Depois de salvar a observação, o aplicativo navegará de volta para o **NotesPage**.

    Insira um número de notas, de comprimento variável, para observar o comportamento do aplicativo.

::: zone-end
::: zone pivot="macos"

## <a name="update-the-app-with-visual-studio-for-mac"></a>Atualizar um aplicativo com o Visual Studio para Mac

1. Inicialize o Visual Studio para Mac. Na janela iniciar, clique em **abrir** e, na caixa de diálogo, selecione o arquivo de solução para o projeto do Notes:

    ![](multi-page-images/vsmac/open-solution.png "Open Solution")

2. No **painel de soluções**, selecione o projeto **Notes** , clique com o botão direito do mouse e selecione **Adicionar > nova pasta**:

    ![](multi-page-images/vsmac/add-new-folder.png "Add New Folder")

3. No **painel de soluções**, nomeie a nova pasta como **modelos**:

    ![](multi-page-images/vsmac/name-folder.png "Models Folder")

4. Na **painel de soluções**, selecione a pasta **modelos** , clique com o botão direito do mouse e selecione **Adicionar > novo arquivo...** :

    ![](multi-page-images/vsmac/add-new-models-file.png "Add New File")

5. Na caixa de diálogo **novo arquivo** , selecione **Geral > classe vazia**, nomeie o novo arquivo como **Note** e clique no botão **novo**

    ![](multi-page-images/vsmac/add-note-class.png "Add Note Class")

    Isso adicionará uma classe denominada **Note** à pasta **modelos** do projeto do **Notes** .

6. No **Note.cs**, remova todo o código do modelo e substitua-o pelo código a seguir:

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

    Essa classe define um modelo de `Note` que armazenará dados sobre cada nota no aplicativo.

    Salve as alterações no **Note.cs** escolhendo **arquivo > salvar** (ou pressionando  **&#8984; + S**) e feche o arquivo.

7. Na **painel de soluções**, selecione o projeto **Notes** , clique com o botão direito do mouse e selecione **Adicionar > novo arquivo...** . Na caixa de diálogo **novo arquivo** , selecione **Forms > Forms Contentpage XAML**, nomeie o novo arquivo **NoteEntryPage**e clique no botão **novo** :

    ![](multi-page-images/vsmac/add-note-entry-page.png "Add Xamarin.Forms ContentPage")

    Isso adicionará uma nova página chamada **NoteEntryPage** à pasta raiz do projeto. Esta página será a segunda página do aplicativo.

8. No **NoteEntryPage.XAML**, remova todo o código do modelo e substitua-o pelo seguinte código:

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

      Esse código declarativamente define a interface do usuário para a página, que consiste em um [`Editor`](xref:Xamarin.Forms.Editor) para entrada de texto e duas instâncias de [`Button`](xref:Xamarin.Forms.Button) que direcionam o aplicativo para salvar ou excluir um arquivo. As duas instâncias de `Button` são dispostas horizontalmente em uma [`Grid`](xref:Xamarin.Forms.Grid), com o `Editor` e `Grid` sendo dispostos verticalmente em uma [`StackLayout`](xref:Xamarin.Forms.StackLayout). Além disso, o `Editor` usa a vinculação de dados para associar à propriedade `Text` do modelo de `Note`. Para obter mais informações sobre vinculação de dados, consulte [vinculação de dados](deepdive.md#data-binding) no guia de [início rápido do Xamarin. Forms](deepdive.md).

      Salve as alterações em **NoteEntryPage.XAML** escolhendo **arquivo > salvar** (ou pressionando  **&#8984; + S**) e feche o arquivo.

9. No **NoteEntryPage.XAML.cs**, remova todo o código do modelo e substitua-o pelo código a seguir:

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

      Esse código armazena uma instância de `Note`, que representa uma única nota, na [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) da página. Quando o [`Button`](xref:Xamarin.Forms.Button) de salvamento é pressionado, o manipulador de eventos de `OnSaveButtonClicked` é executado, o que salva o conteúdo do `Editor` em um novo arquivo com um FileName gerado aleatoriamente ou em um arquivo existente se uma observação estiver sendo atualizada. Em ambos os casos, o arquivo é armazenado na pasta de dados do aplicativo local para o aplicativo. Em seguida, o método navega de volta para a página anterior. Quando o `Button` de **exclusão** é pressionado, o manipulador de eventos `OnDeleteButtonClicked` é executado, o que exclui o arquivo, desde que ele exista e navega de volta para a página anterior. Para obter mais informações sobre navegação, consulte [navegação](deepdive.md#navigation) no guia de [início rápido do Xamarin. Forms](deepdive.md).

      Salve as alterações no **NoteEntryPage.XAML.cs** escolhendo **arquivo > salvar** (ou pressionando  **&#8984; + S**) e feche o arquivo.

      > [!WARNING]
      > A tentativa de criar o aplicativo neste ponto resultará em erros que serão corrigidos nas etapas subsequentes.

10. Na **painel de soluções**, selecione o projeto **notas** , clique com o botão direito do mouse e selecione **Adicionar > novo arquivo...** . Na caixa de diálogo **novo arquivo** , selecione **Forms > Forms Contentpage XAML**, nomeie o novo arquivo **NotesPage**e clique no botão **novo** .

      Isso adicionará uma página chamada **NotesPage** à pasta raiz do projeto. Esta página será a página raiz do aplicativo.

11. No **NotesPage.XAML**, remova todo o código do modelo e substitua-o pelo seguinte código:

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

    Esse código define declarativamente a interface do usuário para a página, que consiste em um [`ListView`](xref:Xamarin.Forms.ListView) e um [`ToolbarItem`](xref:Xamarin.Forms.ToolbarItem). O `ListView` usa a vinculação de dados para exibir todas as anotações que são recuperadas pelo aplicativo e a seleção de uma observação navegará até o `NoteEntryPage` em que a nota pode ser modificada. Como alternativa, uma nova nota pode ser criada pressionando o `ToolbarItem`. Para obter mais informações sobre vinculação de dados, consulte [vinculação de dados](deepdive.md#data-binding) no guia de [início rápido do Xamarin. Forms](deepdive.md).

    Salve as alterações em **NotesPage. XAML** escolhendo **arquivo > salvar** (ou pressionando  **&#8984; + S**) e feche o arquivo.

12. No **NotesPage.XAML.cs**, remova todo o código do modelo e substitua-o pelo código a seguir:

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

    Esse código define a funcionalidade para o `NotesPage`. Quando a página é exibida, o método `OnAppearing` é executado, o que popula a [`ListView`](xref:Xamarin.Forms.ListView) com todas as anotações que foram recuperadas da pasta de dados do aplicativo local. Quando o [`ToolbarItem`](xref:Xamarin.Forms.ToolbarItem) é pressionado, o manipulador de eventos `OnNoteAddedClicked` é executado. Esse método navega para a `NoteEntryPage`, definindo a [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) do `NoteEntryPage` como uma nova instância `Note`. Quando um item na `ListView` é selecionado, o manipulador de eventos `OnListViewItemSelected` é executado. Esse método navega para a `NoteEntryPage`, definindo a [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) do `NoteEntryPage` para a instância de `Note` selecionada. Para obter mais informações sobre navegação, consulte [navegação](deepdive.md#navigation) no guia de [início rápido do Xamarin. Forms](deepdive.md).

    Salve as alterações no **NotesPage.XAML.cs** escolhendo **arquivo > salvar** (ou pressionando  **&#8984; + S**) e feche o arquivo.

    > [!WARNING]
    > A tentativa de criar o aplicativo neste ponto resultará em erros que serão corrigidos nas etapas subsequentes.

13. Na **painel de soluções**, clique duas vezes em **app.XAML.cs** para abri-lo. Em seguida, substitua o código existente pelo código a seguir:

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

    Esse código adiciona uma declaração de namespace para o namespace `System.IO` e adiciona uma declaração para uma propriedade estática `FolderPath` do tipo `string`. A propriedade `FolderPath` é usada para armazenar o caminho no dispositivo em que os dados de observação serão armazenados. Além disso, o código inicializa a propriedade `FolderPath` no Construtor `App` e inicializa a propriedade [`MainPage`](xref:Xamarin.Forms.Application.MainPage) como um [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) que hospeda uma instância do `NotesPage`. Para obter mais informações sobre navegação, consulte [navegação](deepdive.md#navigation) no guia de [início rápido do Xamarin. Forms](deepdive.md).

    Salve as alterações em **App.xaml.cs** escolhendo **Arquivo > Salvar** (ou pressionando **& #8984; + S**) e feche o arquivo.

14. No **painel de soluções**, no projeto do **Notes** , clique com o botão direito do mouse em **MainPage. XAML**e selecione **remover**. Na caixa de diálogo exibida, pressione o botão **excluir** para remover o arquivo do disco rígido.

    Isso remove uma página que não é mais usada.

15. Compile e execute o projeto em cada plataforma. Para obter mais informações, consulte [criando o início rápido](single-page.md#building-the-quickstart).

    No **NotesPage** , pressione o botão **+** para navegar até o **NoteEntryPage** e insira uma observação. Depois de salvar a observação, o aplicativo navegará de volta para o **NotesPage**.

    Insira um número de notas, de comprimento variável, para observar o comportamento do aplicativo.

::: zone-end

## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você aprendeu a:

- Adicionar páginas adicionais a uma solução Xamarin. Forms.
- Executar a navegação entre as páginas.
- Usar a vinculação de dados para sincronizar dados entre elementos da interface do usuário e sua fonte de dados.

Para modificar o aplicativo de forma que ele armazene seus dados em um banco de SQLite.NET local, vá para o próximo início rápido.

> [!div class="nextstepaction"]
> [Avançar](database.md)

## <a name="related-links"></a>Links relacionados

- [Notas (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/getstarted-notes-multipage/)
- [Detalhamento do guia de início rápido do Xamarin. Forms](deepdive.md)
