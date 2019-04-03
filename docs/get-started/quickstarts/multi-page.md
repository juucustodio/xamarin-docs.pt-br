---
title: Navegar em um aplicativo Xamarin.Forms de várias páginas
description: Este artigo explica como ativar o aplicativo de página única, capaz de armazenar uma anotação única, em um aplicativo de várias página, capaz de armazenar várias anotações.
zone_pivot_groups: platform
ms.topic: quickstart
ms.prod: xamarin
ms.assetid: 9DC3B3D6-6CBC-4705-BE80-3D86A9E65F92
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/01/2019
ms.openlocfilehash: 855962560897789dadba535f69c4a7da42bb4742
ms.sourcegitcommit: c4be32ef914465e808d89767c4d5ee72afe93cc6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/02/2019
ms.locfileid: "58854971"
---
# <a name="perform-navigation-in-a-multi-page-xamarinforms-application"></a>Executar a navegação em um aplicativo xamarin. Forms de várias páginas

[![Dbaixar exemplo](~/media/shared/download.png) Baixar a amostra](https://developer.xamarin.com/samples/xamarin-forms/GetStarted/Notes/MultiPage/)

Neste início rápido, você aprenderá como:

- Adicione páginas adicionais a uma solução do xamarin. Forms.
- Execute a navegação entre páginas.
- Use a ligação de dados para sincronizar dados entre elementos de interface do usuário e sua fonte de dados.

O guia de início rápido explica como ativar um plataforma cruzada xamarin. Forms aplicativo de página única, capaz de armazenar uma anotação única, em um aplicativo de várias página, capaz de armazenar várias anotações. O aplicativo final é mostrado abaixo:

[![](multi-page-images/screenshots1-sml.png "Página de anotações")](multi-page-images/screenshots1.png#lightbox "anotações")
[![](multi-page-images/screenshots2-sml.png "Observe a página de entrada")](multi-page-images/screenshots2.png#lightbox "Observação Página de entrada")

### <a name="prerequisites"></a>Pré-requisitos

Você deve concluir com êxito a [guia de início rápido anterior](single-page.md) antes de tentar este início rápido. Como alternativa, baixe o [exemplo de início rápido anterior](https://developer.xamarin.com/samples/xamarin-forms/GetStarted/Notes/SinglePage/) e usá-lo como ponto de partida para este início rápido.

::: zone pivot="windows"

## <a name="update-the-app-with-visual-studio"></a>Atualizar um aplicativo com o Visual Studio

1. Inicie o Visual Studio. Na janela de início, clique o **notas** solução na lista de projetos/soluções recentes, ou clique em **abrir um projeto ou solução**e, na **Abrir projeto/solução** caixa de diálogo Selecione o arquivo de solução para o projeto de notas:

    ![](multi-page-images/vs/open-solution.png "Abrir Projeto")

2. No **Gerenciador de soluções**, clique com botão direito no **notas** do projeto e selecione **Adicionar > nova pasta**:

    ![](multi-page-images/vs/add-new-item.png "Adicionar Novo Item")

3. Na **Gerenciador de soluções**, nomeie a nova pasta **modelos**:

    ![](multi-page-images/vs/name-folder.png "Pasta de modelos")

4. Na **Gerenciador de soluções**, selecione o **modelos** pasta, o botão direito do mouse e selecione **Adicionar > Novo Item...** :

    ![](multi-page-images/vs/add-new-models-file.png "Adicionar Novo Arquivo")

5. No **Adicionar Novo Item** caixa de diálogo, selecione **Visual C# itens > classe**, nomeie o novo arquivo **Observação**e clique no **adicionar** botão:

    ![](multi-page-images/vs/add-note-class.png "Adicionar classe de anotação")

    Isso adicionará uma classe chamada **Observação** para o **modelos** pasta dos **notas** projeto.

6. Na **Note.cs**, remova todo o código de modelo e substitua-o pelo código a seguir:

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

    Essa classe define um `Note` modelo que armazenará os dados sobre cada Observação no aplicativo.    

    Salve as alterações em **Note.cs** pressionando **CTRL + S**e feche o arquivo.

7. No **Gerenciador de soluções**, clique com botão direito no **notas** do projeto e selecione **Adicionar > Novo Item...** . No **Adicionar Novo Item** caixa de diálogo, selecione **Visual C# itens > xamarin. Forms > página de conteúdo**, nomeie o novo arquivo **NoteEntryPage**e clique no  **Adicionar** botão:

    ![](multi-page-images/vs/add-note-entry-page.png "Adicionar ContentPage do xamarin. Forms")

    Isso adicionará uma nova página chamada **NoteEntryPage** para a pasta raiz do projeto. Esta página será a segunda página do aplicativo.

8. Na **NoteEntryPage.xaml**, remova todo o código de modelo e substitua-o pelo código a seguir:

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

      Esse código define declarativamente a interface do usuário para a página, que consiste em uma [ `Editor` ](xref:Xamarin.Forms.Editor) para entrada de texto e dois [ `Button` ](xref:Xamarin.Forms.Button) instâncias que direcionam o aplicativo para salvar ou excluir um arquivo. Os dois `Button` instâncias são dispostas horizontalmente em uma [ `Grid` ](xref:Xamarin.Forms.Grid), com o `Editor` e `Grid` sendo dispostos verticalmente em um [ `StackLayout` ](xref:Xamarin.Forms.StackLayout). Além disso, o `Editor` usa associação de dados para associar ao `Text` propriedade do `Note` modelo. Para obter mais informações sobre associação de dados, consulte [vinculação de dados](deepdive.md#data-binding) na [aprofundamento de início rápido de xamarin. Forms](deepdive.md).

      Salve as alterações em **NoteEntryPage.xaml** pressionando **CTRL + S**e feche o arquivo.

9. Na **NoteEntryPage.xaml.cs**, remova todo o código de modelo e substitua-o pelo código a seguir:

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

      Este código armazena uma `Note` instância, que representa uma anotação única, em de [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext) da página. Quando o **salve** [ `Button` ](xref:Xamarin.Forms.Button) é pressionado a `OnSaveButtonClicked` manipulador de eventos é executado, o que seja salva o conteúdo do `Editor` para um novo arquivo com um nome de arquivo gerado aleatoriamente, ou para um arquivo existente se uma anotação está sendo atualizada. Em ambos os casos, o arquivo é armazenado na pasta de dados do aplicativo local para o aplicativo. Em seguida, o método navega de volta para a página anterior. Quando o **exclua** `Button` é pressionado a `OnDeleteButtonClicked` manipulador de eventos é executado, o que exclui o arquivo, desde que ela existe e navega de volta para a página anterior. Para obter mais informações sobre navegação, consulte [navegação](deepdive.md#navigation) na [aprofundamento de início rápido de xamarin. Forms](deepdive.md).

      Salve as alterações em **NoteEntryPage.xaml.cs** pressionando **CTRL + S**e feche o arquivo.

      > [!WARNING]
      > A tentativa de compilar o aplicativo neste ponto resultará em erros que serão corrigidos em etapas subsequentes.

10. No **Gerenciador de soluções**, clique com botão direito no **notas** do projeto e selecione **Adicionar > Novo Item...** . No **Adicionar Novo Item** caixa de diálogo, selecione **Visual C# itens > xamarin. Forms > página de conteúdo**, nomeie o novo arquivo **NotesPage**e clique no **adicionar**  botão.

      Isso adicionará uma página chamada **NotesPage** para a pasta raiz do projeto. Esta página será a página raiz do aplicativo.

11. Na **NotesPage.xaml**, remova todo o código de modelo e substitua-o pelo código a seguir:

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

    Esse código define declarativamente a interface do usuário para a página, que consiste em uma [ `ListView` ](xref:Xamarin.Forms.ListView) e uma [ `ToolbarItem` ](xref:Xamarin.Forms.ToolbarItem). O `ListView` usa dados de associação para exibir todas as anotações são recuperadas pelo aplicativo e selecionando uma observação navegará até o `NoteEntryPage` em que a anotação pode ser modificada. Como alternativa, uma nova anotação pode ser criada, pressionando o `ToolbarItem`. Para obter mais informações sobre associação de dados, consulte [vinculação de dados](deepdive.md#data-binding) na [aprofundamento de início rápido de xamarin. Forms](deepdive.md).

    Salve as alterações em **NotesPage.xaml** pressionando **CTRL + S**e feche o arquivo.

12. Na **NotesPage.xaml.cs**, remova todo o código de modelo e substitua-o pelo código a seguir:

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

    Esse código define a funcionalidade para o `NotesPage`. Quando a página for exibida, o `OnAppearing` método é executado, que preenche a [ `ListView` ](xref:Xamarin.Forms.ListView) com as observações que foram recuperadas da pasta de dados do aplicativo local. Quando o [ `ToolbarItem` ](xref:Xamarin.Forms.ToolbarItem) é pressionado a `OnNoteAddedClicked` manipulador de eventos é executado. Esse método navega para o `NoteEntryPage`, definindo o [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext) dos `NoteEntryPage` para um novo `Note` instância. Quando um item na `ListView` está selecionado o `OnListViewItemSelected` manipulador de eventos é executado. Esse método navega para o `NoteEntryPage`, definindo o [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext) dos `NoteEntryPage` para selecionado `Note` instância. Para obter mais informações sobre navegação, consulte [navegação](deepdive.md#navigation) na [aprofundamento de início rápido de xamarin. Forms](deepdive.md).

    Salve as alterações em **NotesPage.xaml.cs** pressionando **CTRL + S**e feche o arquivo.

    > [!WARNING]
    > A tentativa de compilar o aplicativo neste ponto resultará em erros que serão corrigidos em etapas subsequentes.

13. Na **Gerenciador de soluções**, clique duas vezes em **App.xaml.cs** para abri-lo. Em seguida, substitua o código existente pelo código a seguir:

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
            ...
        }
    }
    ```

    Esse código adiciona uma declaração de namespace para o `System.IO` namespace e adiciona uma declaração para um estático `FolderPath` propriedade do tipo `string`. O `FolderPath` propriedade é usada para armazenar o caminho no dispositivo onde Observe que os dados serão armazenados. Além disso, o código inicializa o `FolderPath` propriedade no `App` construtor e inicializa o [ `MainPage` ](xref:Xamarin.Forms.Application.MainPage) propriedade seja uma [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage) que hospeda um instância do `NotesPage`. Para obter mais informações sobre navegação, consulte [navegação](deepdive.md#navigation) na [aprofundamento de início rápido de xamarin. Forms](deepdive.md).

    Salve as alterações em **App.xaml.cs** ao pressionar **CTRL + S** e feche o arquivo.

14. Na **Gerenciador de soluções**, no **notas** do projeto, clique com botão direito **MainPage. XAML**e selecione **excluir**. Na caixa de diálogo que aparece pressione a **Okey** botão para remover o arquivo de disco rígido.

    Isso remove uma página que não é mais usada.

15. Compile e execute o projeto em cada plataforma. Para obter mais informações, consulte [criando o guia de início rápido](single-page.md#building-the-quickstart).

    Sobre o **NotesPage** pressione a **+** botão para navegar para o **NoteEntryPage** e insira uma observação. Depois de salvar a nota o aplicativo será navegue de volta para o **NotesPage**.

    Insira um número de observações, de comprimento variado, para observar o comportamento do aplicativo.

::: zone-end
::: zone pivot="macos"

## <a name="update-the-app-with-visual-studio-for-mac"></a>Atualizar um aplicativo com o Visual Studio para Mac

1. Inicialize o Visual Studio para Mac. Na janela de início, clique em **abrir**e na caixa de diálogo, selecione o arquivo de solução para o projeto de notas:

    ![](multi-page-images/vsmac/open-solution.png "Abrir Solução")

2. No **painel de soluções**, selecione o **notas** projeto, o botão direito do mouse e selecione **Adicionar > nova pasta**:

    ![](multi-page-images/vsmac/add-new-folder.png "Adicionar nova pasta")

3. No **painel de soluções**, nomeie a nova pasta **modelos**:

    ![](multi-page-images/vsmac/name-folder.png "Pasta de modelos")

4. No **painel de soluções**, selecione o **modelos** pasta, o botão direito do mouse e selecione **Adicionar > novo arquivo...** :

    ![](multi-page-images/vsmac/add-new-models-file.png "Adicionar Novo Arquivo")

5. No **novo arquivo** caixa de diálogo, selecione **geral > classe vazia**, nomeie o novo arquivo **Observação**e clique no **novo** botão:

    ![](multi-page-images/vsmac/add-note-class.png "Adicionar classe de anotação")

    Isso adicionará uma classe chamada **Observação** para o **modelos** pasta dos **notas** projeto.

6. Na **Note.cs**, remova todo o código de modelo e substitua-o pelo código a seguir:

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

    Essa classe define um `Note` modelo que armazenará os dados sobre cada Observação no aplicativo.

    Salve as alterações em **Note.cs** escolhendo **arquivo > Salvar** (ou pressionando  **&#8984; + S**) e feche o arquivo.

7. No **painel de soluções**, selecione o **notas** projeto, o botão direito do mouse e selecione **Adicionar > novo arquivo...** . No **novo arquivo** caixa de diálogo, selecione **formulários > formulários ContentPage XAML**, nomeie o novo arquivo **NoteEntryPage**e clique no **novo** botão:

    ![](multi-page-images/vsmac/add-note-entry-page.png "Adicionar ContentPage do xamarin. Forms")

    Isso adicionará uma nova página chamada **NoteEntryPage** para a pasta raiz do projeto. Esta página será a segunda página do aplicativo.

8. Na **NoteEntryPage.xaml**, remova todo o código de modelo e substitua-o pelo código a seguir:

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

      Esse código define declarativamente a interface do usuário para a página, que consiste em uma [ `Editor` ](xref:Xamarin.Forms.Editor) para entrada de texto e dois [ `Button` ](xref:Xamarin.Forms.Button) instâncias que direcionam o aplicativo para salvar ou excluir um arquivo. Os dois `Button` instâncias são dispostas horizontalmente em uma [ `Grid` ](xref:Xamarin.Forms.Grid), com o `Editor` e `Grid` sendo dispostos verticalmente em um [ `StackLayout` ](xref:Xamarin.Forms.StackLayout). Além disso, o `Editor` usa associação de dados para associar ao `Text` propriedade do `Note` modelo. Para obter mais informações sobre associação de dados, consulte [vinculação de dados](deepdive.md#data-binding) na [aprofundamento de início rápido de xamarin. Forms](deepdive.md).

      Salve as alterações em **NoteEntryPage.xaml** escolhendo **arquivo > Salvar** (ou pressionando  **&#8984; + S**) e feche o arquivo.

9. Na **NoteEntryPage.xaml.cs**, remova todo o código de modelo e substitua-o pelo código a seguir:

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

      Este código armazena uma `Note` instância, que representa uma anotação única, em de [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext) da página. Quando o **salve** [ `Button` ](xref:Xamarin.Forms.Button) é pressionado a `OnSaveButtonClicked` manipulador de eventos é executado, o que seja salva o conteúdo do `Editor` para um novo arquivo com um nome de arquivo gerado aleatoriamente, ou para um arquivo existente se uma anotação está sendo atualizada. Em ambos os casos, o arquivo é armazenado na pasta de dados do aplicativo local para o aplicativo. Em seguida, o método navega de volta para a página anterior. Quando o **exclua** `Button` é pressionado a `OnDeleteButtonClicked` manipulador de eventos é executado, o que exclui o arquivo, desde que ela existe e navega de volta para a página anterior. Para obter mais informações sobre navegação, consulte [navegação](deepdive.md#navigation) na [aprofundamento de início rápido de xamarin. Forms](deepdive.md).

      Salve as alterações em **NoteEntryPage.xaml.cs** escolhendo **arquivo > Salvar** (ou pressionando  **&#8984; + S**) e feche o arquivo.

      > [!WARNING]
      > A tentativa de compilar o aplicativo neste ponto resultará em erros que serão corrigidos em etapas subsequentes.

10. No **painel de soluções**, selecione o **notas** projeto, o botão direito do mouse e selecione **Adicionar > novo arquivo...** . No **novo arquivo** caixa de diálogo, selecione **formulários > formulários ContentPage XAML**, nomeie o novo arquivo **NotesPage**e clique no **novo** botão.

      Isso adicionará uma página chamada **NotesPage** para a pasta raiz do projeto. Esta página será a página raiz do aplicativo.

11. Na **NotesPage.xaml**, remova todo o código de modelo e substitua-o pelo código a seguir:

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

    Esse código define declarativamente a interface do usuário para a página, que consiste em uma [ `ListView` ](xref:Xamarin.Forms.ListView) e uma [ `ToolbarItem` ](xref:Xamarin.Forms.ToolbarItem). O `ListView` usa dados de associação para exibir todas as anotações são recuperadas pelo aplicativo e selecionando uma observação navegará até o `NoteEntryPage` em que a anotação pode ser modificada. Como alternativa, uma nova anotação pode ser criada, pressionando o `ToolbarItem`. Para obter mais informações sobre associação de dados, consulte [vinculação de dados](deepdive.md#data-binding) na [aprofundamento de início rápido de xamarin. Forms](deepdive.md).

    Salve as alterações em **NotesPage.xaml** escolhendo **arquivo > Salvar** (ou pressionando  **&#8984; + S**) e feche o arquivo.

12. Na **NotesPage.xaml.cs**, remova todo o código de modelo e substitua-o pelo código a seguir:

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

    Esse código define a funcionalidade para o `NotesPage`. Quando a página for exibida, o `OnAppearing` método é executado, que preenche a [ `ListView` ](xref:Xamarin.Forms.ListView) com as observações que foram recuperadas da pasta de dados do aplicativo local. Quando o [ `ToolbarItem` ](xref:Xamarin.Forms.ToolbarItem) é pressionado a `OnNoteAddedClicked` manipulador de eventos é executado. Esse método navega para o `NoteEntryPage`, definindo o [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext) dos `NoteEntryPage` para um novo `Note` instância. Quando um item na `ListView` está selecionado o `OnListViewItemSelected` manipulador de eventos é executado. Esse método navega para o `NoteEntryPage`, definindo o [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext) dos `NoteEntryPage` para selecionado `Note` instância. Para obter mais informações sobre navegação, consulte [navegação](deepdive.md#navigation) na [aprofundamento de início rápido de xamarin. Forms](deepdive.md).

    Salve as alterações em **NotesPage.xaml.cs** escolhendo **arquivo > Salvar** (ou pressionando  **&#8984; + S**) e feche o arquivo.

    > [!WARNING]
    > A tentativa de compilar o aplicativo neste ponto resultará em erros que serão corrigidos em etapas subsequentes.

13. No **painel de soluções**, clique duas vezes em **App.xaml.cs** para abri-lo. Em seguida, substitua o código existente pelo código a seguir:

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
            ...
        }
    }
    ```

    Esse código adiciona uma declaração de namespace para o `System.IO` namespace e adiciona uma declaração para um estático `FolderPath` propriedade do tipo `string`. O `FolderPath` propriedade é usada para armazenar o caminho no dispositivo onde Observe que os dados serão armazenados. Além disso, o código inicializa o `FolderPath` propriedade no `App` construtor e inicializa o [ `MainPage` ](xref:Xamarin.Forms.Application.MainPage) propriedade seja uma [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage) que hospeda um instância do `NotesPage`. Para obter mais informações sobre navegação, consulte [navegação](deepdive.md#navigation) na [aprofundamento de início rápido de xamarin. Forms](deepdive.md).

    Salve as alterações em **App.xaml.cs** escolhendo **Arquivo > Salvar** (ou pressionando **&#8984; + S**) e feche o arquivo.

14. No **painel de soluções**, no **notas** do projeto, clique com botão direito **MainPage. XAML**e selecione **remover**. Na caixa de diálogo que aparece pressione a **excluir** botão para remover o arquivo de disco rígido.

    Isso remove uma página que não é mais usada.

15. Compile e execute o projeto em cada plataforma. Para obter mais informações, consulte [criando o guia de início rápido](single-page.md#building-the-quickstart).

    Sobre o **NotesPage** pressione a **+** botão para navegar para o **NoteEntryPage** e insira uma observação. Depois de salvar a nota o aplicativo será navegue de volta para o **NotesPage**.

    Insira um número de observações, de comprimento variado, para observar o comportamento do aplicativo.

::: zone-end

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você aprendeu como:

- Adicione páginas adicionais a uma solução do xamarin. Forms.
- Execute a navegação entre páginas.
- Use a ligação de dados para sincronizar dados entre elementos de interface do usuário e sua fonte de dados.

Para modificar o aplicativo para que ele armazena seus dados em um banco de dados local SQLite.NET, continue para o próximo início rápido.

> [!div class="nextstepaction"]
> [Avançar](database.md)

## <a name="related-links"></a>Links relacionados

- [Anotações (amostra)](https://developer.xamarin.com/samples/xamarin-forms/GetStarted/Notes/MultiPage/)
- [Aprofundamento de início rápido do xamarin. Forms](deepdive.md)
