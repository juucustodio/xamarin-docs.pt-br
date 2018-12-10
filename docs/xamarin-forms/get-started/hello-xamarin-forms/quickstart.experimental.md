---
title: Início rápido do Xamarin.Forms
description: Este artigo explica como criar um aplicativo de Notas simples multiplataforma usando o Xamarin.Forms, que permite inserir uma nota e mantê-la no armazenamento do dispositivo.
zone_pivot_groups: platform
ms.topic: quickstart
ms.prod: xamarin
ms.assetid: E8CF05B1-54B9-428B-8518-D068837BD61E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/21/2018
ms.openlocfilehash: 880fa527d91098cf8c5f8c46cd9c5cce9ec9a489
ms.sourcegitcommit: 744c0a50420bb091fca8b92a84c20e61c741cf9e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/01/2018
ms.locfileid: "52742920"
---
# <a name="xamarinforms-quickstart"></a>Início rápido do Xamarin.Forms

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://developer.xamarin.com/samples/xamarin-forms/GetStarted/Notes/SinglePage/)

Este Início Rápido explica como criar um aplicativo de Notas simples multiplataforma usando o Xamarin.Forms, que permite inserir uma nota e mantê-la no armazenamento do dispositivo. O aplicativo final é mostrado abaixo:

[![](quickstart-experimental-images/screenshots-sml.png "Aplicativo de Notas")](quickstart-experimental-images/screenshots.png#lightbox "Aplicativo de Notas")

::: zone pivot="windows"

## <a name="get-started-with-visual-studio"></a>Introdução ao Visual Studio

1. Na tela **Iniciar**, inicialize o Visual Studio. Isso abre a página inicial:

    ![](quickstart-experimental-images/vs/start-page.png "Visual Studio")

2. No Visual Studio, clique em **Criar novo projeto...** para criar um novo projeto:

    ![](quickstart-experimental-images/vs/new-solution.png "Novo Projeto")

3. Na caixa de diálogo **Novo Projeto**, clique em **Multiplataforma**, selecione o modelo **Aplicativo Móvel (Xamarin.Forms)**, defina o Nome como **Notas**, escolha um local adequado para o projeto e clique no botão **OK**:

    ![](quickstart-experimental-images/vs/new-project.png "Modelos de Projeto de Multiplataforma")

    > [!IMPORTANT]
    > Os snippets C# e XAML neste início rápido requerem que a solução seja nomeada como **Notas**. O uso de um nome diferente resultará em erros de build ao copiar o código deste início rápido para a solução.

4. Na caixa de diálogo **Novo Aplicativo de Plataforma Cruzada**, clique em **Aplicativo em Branco**, selecione **.NET Standard** como a estratégia de compartilhamento de código e clique no botão **OK**:

    ![](quickstart-experimental-images/vs/new-app.png "Novo Aplicativo de Multiplataforma")

5. No **Gerenciador de Soluções**, no projeto **Notas**, clique duas vezes em **MainPage.xaml** para abri-lo:

    ![](quickstart-experimental-images/vs/open-mainpage-xaml.png "Abrir MainPage.xaml")

6. Em **MainPage.xaml**, remova todo o código de modelo e substitua-o pelo código a seguir:

    ```xaml
    <?xml version="1.0" encoding="utf-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="Notes.MainPage">
        <StackLayout Margin="10,35,10,10">
            <Label Text="Notes"
                   HorizontalOptions="Center"
                   FontAttributes="Bold" />
            <Editor x:Name="_editor"
                    Placeholder="Enter your note"
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

    Esse código define declarativamente a interface do usuário para a página, que consiste em um [`Label`](xref:Xamarin.Forms.Label) para exibir texto, um [`Editor`](xref:Xamarin.Forms.Editor) para entrada de texto e duas instâncias de [`Button`](xref:Xamarin.Forms.Button) que direcionam o aplicativo para salvar ou excluir um arquivo. As duas instâncias de `Button` são dispostas horizontalmente em uma [`Grid`](xref:Xamarin.Forms.Grid), com o `Label`, o `Editor` e a `Grid` dispostos verticalmente em um [`StackLayout`](xref:Xamarin.Forms.StackLayout).

    Salve as alterações em **MainPage.xaml** ao pressionar **CTRL+S** e feche o arquivo.

7. No **Gerenciador de Soluções**, no projeto **Notas**, expanda **MainPage.xaml** e clique duas vezes em **MainPage.xaml.cs** para abri-lo:

    ![](quickstart-experimental-images/vs/open-mainpage-codebehind.png "Abrir MainPage.xaml.cs")

8. Em **MainPage.xaml.cs**, remova todo o código de modelo e substitua-o pelo código a seguir:

    ```csharp
    using System;
    using System.IO;
    using Xamarin.Forms;

    namespace Notes
    {
        public partial class MainPage : ContentPage
        {
            string _fileName = Path.Combine(Environment.GetFolderPath(Environment.SpecialFolder.LocalApplicationData), "notes.txt");

            public MainPage()
            {
                InitializeComponent();

                if (File.Exists(_fileName))
                {
                    _editor.Text = File.ReadAllText(_fileName);
                }
            }

            void OnSaveButtonClicked(object sender, EventArgs e)
            {
                File.WriteAllText(_fileName, _editor.Text);
            }

            void OnDeleteButtonClicked(object sender, EventArgs e)
            {
                if (File.Exists(_fileName))
                {
                    File.Delete(_fileName);
                }
                _editor.Text = string.Empty;
            }
        }
    }
    ```

    Esse código define um campo `_fileName`, que referencia um arquivo chamado `notes.txt` que armazenará os dados de notas na pasta de dados local do aplicativo. Quando o construtor de página for executado o arquivo será lido, caso exista, e exibido no [`Editor`](xref:Xamarin.Forms.Editor). Quando o [`Button`](xref:Xamarin.Forms.Button) **Salvar** for pressionado, o manipulador de eventos `OnSaveButtonClicked` será executado, salvando no arquivo o conteúdo do `Editor`. Quando o `Button` **Excluir** for pressionado, o manipulador de eventos `OnDeleteButtonClicked` será executado, excluindo o arquivo, caso ele exista, e removendo todo o texto do `Editor`.

    Salve as alterações em **MainPage.xaml.cs** ao pressionar **CTRL+S** e feche o arquivo.

### <a name="building-the-quickstart"></a>Criando o Início Rápido

1. No Visual Studio, selecione o item de menu **Criar > Criar Solução** (ou pressione F6). A solução será criada e uma mensagem de êxito será exibida na barra de status do Visual Studio:

      ![](quickstart-experimental-images/vs/build-succeeded.png "Build concluído com êxito")

    Se houver erros, repita as etapas anteriores e corrija-os até que a solução seja compilada com êxito.

2. Na barra de ferramentas do Visual Studio, pressione o botão **Iniciar** (o botão triangular que se parece com o botão Reproduzir) para iniciar o aplicativo em seu emulador Android escolhido:

    ![](quickstart-experimental-images/vs/android-start.png "Barra de ferramentas do Visual Studio para Android")

    ![](quickstart-experimental-images/vs/notes-android.png "Notas no emulador Android")

    Insira uma nota e pressione o botão **Salvar**.

    > [!NOTE]
    > As etapas a seguir deverão ser realizadas somente se você tiver um [Mac emparelhado](~/ios/get-started/installation/windows/connecting-to-mac/index.md) que atenda aos requisitos do sistema para o desenvolvimento no Xamarin.Forms.

3. Na barra de ferramentas do Visual Studio, clique com o botão direito do mouse no projeto **Notes.iOS** e selecione **Definir como projeto de inicialização**.

      ![](quickstart-experimental-images/vs/set-as-startup-project-ios.png "Definir o iOS como projeto de inicialização")

4. Na barra de ferramentas do Visual Studio, pressione o botão **Iniciar** (o botão triangular que se parece com o botão Reproduzir) para iniciar o aplicativo em seu [Simulador remoto do iOS](~/tools/ios-simulator/index.md) escolhido:

    ![](quickstart-experimental-images/vs/ios-start.png "Barra de ferramentas do iOS do Visual Studio")

    ![](quickstart-experimental-images/vs/notes-ios.png "Notas no simulador do iOS")

    Insira uma nota e pressione o botão **Salvar**.

::: zone-end
::: zone pivot="macos"

## <a name="get-started-with-visual-studio-for-mac"></a>Introdução ao Visual Studio para Mac

1. Inicialize o Visual Studio para Mac e, na página inicial, clique em **Novo Projeto...** para criar um novo projeto:

    ![](quickstart-experimental-images/vsmac/new-project.png "Nova Solução")

2. Na caixa de diálogo **Escolher um modelo para seu novo projeto**, clique em **Multiplataforma > Aplicativo**, selecione o modelo do **Aplicativo de Formulários em Branco** e clique no botão **Avançar**:

    ![](quickstart-experimental-images/vsmac/choose-template.png "Escolher um modelo")

3. Na caixa de diálogo **Configurar seu aplicativo de formulários em branco**, nomeie o novo aplicativo como **Notas**, selecione o botão de opção **Usar .NET Standard** e clique no botão **Avançar**:    

    ![](quickstart-experimental-images/vsmac/configure-app.png "Configurar o Aplicativo Forms")

4. Na caixa de diálogo **Configurar seu novo aplicativo de formulários em branco**, deixe os nomes do Projeto e da Solução definidos como **Notas**, escolha um local adequado para o projeto e clique no botão **Criar** para criá-lo:

    ![](quickstart-experimental-images/vsmac/configure-project.png "Configurar o Projeto Forms")

    > [!IMPORTANT]
    > Os snippets C# e XAML neste início rápido requerem que a solução e o projeto sejam nomeados como **Notas**. O uso de um nome diferente resultará em erros de build ao copiar o código deste início rápido para o projeto.

5. No **Painel de Soluções**, no projeto **Notas**, clique duas vezes em **MainPage.xaml** para abri-lo:

    ![](quickstart-experimental-images/vsmac/mainpage-xaml.png "MainPage.xaml")

6. Em **MainPage.xaml**, remova todo o código de modelo e substitua-o pelo código a seguir:

    ```xaml
    <?xml version="1.0" encoding="utf-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="Notes.MainPage">
        <StackLayout Margin="10,35,10,10">
            <Label Text="Notes"
                   HorizontalOptions="Center"
                   FontAttributes="Bold" />
            <Editor x:Name="_editor"
                    Placeholder="Enter your note"
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

    Esse código define declarativamente a interface do usuário para a página, que consiste em um [`Label`](xref:Xamarin.Forms.Label) para exibir texto, um [`Editor`](xref:Xamarin.Forms.Editor) para entrada de texto e duas instâncias de [`Button`](xref:Xamarin.Forms.Button) que direcionam o aplicativo para salvar ou excluir um arquivo. As duas instâncias de `Button` são dispostas horizontalmente em uma [`Grid`](xref:Xamarin.Forms.Grid), com o `Label`, o `Editor` e a `Grid` dispostos verticalmente em um [`StackLayout`](xref:Xamarin.Forms.StackLayout).

    Salve as alterações em **MainPage.xaml** ao escolher **Arquivo > Salvar** (ou ao pressionar **& #8984; + S**) e feche o arquivo.

7. No **Painel de Soluções**, no projeto **Notas**, expanda **MainPage.xaml** e clique duas vezes em **MainPage.xaml.cs** para abri-lo:

    ![](quickstart-experimental-images/vsmac/mainpage-xaml-cs.png "MainPage.xaml.cs")

8. Em **MainPage.xaml.cs**, remova todo o código de modelo e substitua-o pelo código a seguir:

    ```csharp
    using System;
    using System.IO;
    using Xamarin.Forms;

    namespace Notes
    {
        public partial class MainPage : ContentPage
        {
            string _fileName = Path.Combine(Environment.GetFolderPath(Environment.SpecialFolder.LocalApplicationData), "notes.txt");

            public MainPage()
            {
                InitializeComponent();

                if (File.Exists(_fileName))
                {
                    _editor.Text = File.ReadAllText(_fileName);
                }
            }

            void OnSaveButtonClicked(object sender, EventArgs e)
            {
                File.WriteAllText(_fileName, _editor.Text);
            }

            void OnDeleteButtonClicked(object sender, EventArgs e)
            {
                if (File.Exists(_fileName))
                {
                    File.Delete(_fileName);
                }
                _editor.Text = string.Empty;
            }
        }
    }
    ```

    Esse código define um campo `_fileName`, que referencia um arquivo chamado `notes.txt` que armazenará os dados de notas na pasta de dados local do aplicativo. Quando o construtor de página for executado o arquivo será lido, caso exista, e exibido no [`Editor`](xref:Xamarin.Forms.Editor). Quando o [`Button`](xref:Xamarin.Forms.Button) **Salvar** for pressionado, o manipulador de eventos `OnSaveButtonClicked` será executado, salvando no arquivo o conteúdo do `Editor`. Quando o `Button` **Excluir** for pressionado, o manipulador de eventos `OnDeleteButtonClicked` será executado, excluindo o arquivo, caso ele exista, e removendo todo o texto do `Editor`.

    Salve as alterações em **MainPage.xaml.cs** ao escolher **Arquivo > Salvar** (ou ao pressionar **& #8984; + S**) e feche o arquivo.

### <a name="building-the-quickstart"></a>Criando o Início Rápido

1. No Visual Studio para Mac, selecione o item de menu **Compilar > Compilar Tudo** (ou pressione **&#8984; + B**). Os projetos serão criados e uma mensagem de êxito será exibida na barra de ferramentas do Visual Studio para Mac.

      ![](quickstart-experimental-images/vsmac/build-successful.png "Build bem-sucedido")

    Se houver erros, repita as etapas anteriores e corrija-os até que os projetos sejam compilados com êxito.

2. No **Painel de Soluções**, selecione o projeto **Notes.iOS**, clique com o botão direito do mouse e selecione **Definir como projeto de inicialização**:

      ![](quickstart-experimental-images/vsmac/set-startup-project-ios.png "Definir o iOS como projeto de inicialização")

3. Na barra de ferramentas do Visual Studio para Mac, pressione o botão **Iniciar** (o botão triangular que se parece com o botão Reproduzir) para iniciar o aplicativo no Simulador do iOS escolhido:

      ![](quickstart-experimental-images/vsmac/start.png "Barra de ferramentas do Visual Studio para Mac")

      ![](quickstart-experimental-images/vsmac/notes-ios.png "Notas no simulador do iOS")

    Insira uma nota e pressione o botão **Salvar**.

4. No **Painel de Soluções**, selecione o projeto **Notes.Droid**, clique com o botão direito do mouse e selecione **Definir como projeto de inicialização**:

      ![](quickstart-experimental-images/vsmac/set-startup-project-android.png "Definir Android como projeto de inicialização")

5. Na barra de ferramentas do Visual Studio para Mac, pressione o botão **Iniciar** (o botão triangular que se parece com o botão Reproduzir) para iniciar o aplicativo em seu emulador Android escolhido:

      ![](quickstart-experimental-images/vsmac/notes-android.png "Notas no emulador Android")

    Insira uma nota e pressione o botão **Salvar**.

::: zone-end

## <a name="related-links"></a>Links relacionados

- [Notas (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/GetStarted/Notes/SinglePage/)
